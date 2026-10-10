# 16 - PDO y bases de datos

> [!info] ¿Qué es?
> **PDO** (PHP Data Objects) es la forma estándar de conectar PHP con bases de datos (MySQL, PostgreSQL, SQLite...). Su gran ventaja son las **consultas preparadas**, que protegen contra la inyección SQL.

---

## 1. Antes de empezar

Debes conocer SQL básico ([[SQL/00 - Índice|SQL]]), arrays (nota 07), POO básica (nota 12) y excepciones (nota 13).

---

## 2. Concepto fundamental

1. Creas una **conexión** (objeto `PDO`).
2. Escribes una consulta SQL con **marcadores** en vez de valores.
3. La **preparas** y la **ejecutas** pasando los valores por separado.
4. Recoges los resultados.

> [!warning] Regla de oro
> **Nunca** metas variables del usuario directamente en el SQL. Usa siempre consultas preparadas.

---

## 3. Sintaxis / estructura

### Conectar

```php
$dsn = "mysql:host=localhost;dbname=tienda;charset=utf8mb4";

$pdo = new PDO($dsn, "usuario", "contraseña", [
    PDO::ATTR_ERRMODE            => PDO::ERRMODE_EXCEPTION,
    PDO::ATTR_DEFAULT_FETCH_MODE => PDO::FETCH_ASSOC,
    PDO::ATTR_EMULATE_PREPARES   => false,
]);
```

| Opción | Para qué |
|---|---|
| `ERRMODE_EXCEPTION` | Lanza excepciones si hay errores |
| `FETCH_ASSOC` | Devuelve los resultados como arrays asociativos |
| `EMULATE_PREPARES => false` | Usa preparación real del servidor |

### Consulta preparada

```php
$stmt = $pdo->prepare("SELECT * FROM usuarios WHERE email = :email");
$stmt->execute(["email" => $email]);
$usuario = $stmt->fetch();   // una fila, o false si no hay
```

### Marcadores

```php
// Con nombre (recomendado)
$pdo->prepare("SELECT * FROM usuarios WHERE id = :id AND activo = :activo");

// Posicionales
$stmt = $pdo->prepare("SELECT * FROM usuarios WHERE id = ? AND activo = ?");
$stmt->execute([5, 1]);
```

---

## 4. Elementos / características

### Obtener resultados

| Método | Devuelve |
|---|---|
| `fetch()` | Una fila (o `false`) |
| `fetchAll()` | Todas las filas |
| `fetchColumn()` | El primer valor de la primera fila |
| `rowCount()` | Filas afectadas (INSERT/UPDATE/DELETE) |

```php
$todos = $pdo->query("SELECT * FROM productos")->fetchAll();
$total = $pdo->query("SELECT COUNT(*) FROM productos")->fetchColumn();
```

`query()` solo se usa cuando **no hay datos del usuario** en la consulta.

### Modos de recuperar (`fetch mode`)

| Constante | Resultado |
|---|---|
| `PDO::FETCH_ASSOC` | `["nombre" => "Ana"]` |
| `PDO::FETCH_NUM` | `[0 => "Ana"]` |
| `PDO::FETCH_OBJ` | Objeto anónimo |
| `PDO::FETCH_CLASS` | Objeto de tu clase |
| `PDO::FETCH_KEY_PAIR` | `[clave => valor]` con dos columnas |

### CRUD

```php
// INSERT
$stmt = $pdo->prepare("INSERT INTO usuarios (nombre, email) VALUES (:nombre, :email)");
$stmt->execute(["nombre" => $nombre, "email" => $email]);
$nuevoId = $pdo->lastInsertId();

// UPDATE
$stmt = $pdo->prepare("UPDATE usuarios SET nombre = :nombre WHERE id = :id");
$stmt->execute(["nombre" => $nombre, "id" => $id]);

// DELETE
$stmt = $pdo->prepare("DELETE FROM usuarios WHERE id = :id");
$stmt->execute(["id" => $id]);
```

### Transacciones

Agrupan varias operaciones: o se hacen **todas**, o **ninguna**.

```php
try {
    $pdo->beginTransaction();

    $pdo->prepare("UPDATE cuentas SET saldo = saldo - :c WHERE id = :o")
        ->execute(["c" => $cantidad, "o" => $origen]);
    $pdo->prepare("UPDATE cuentas SET saldo = saldo + :c WHERE id = :d")
        ->execute(["c" => $cantidad, "d" => $destino]);

    $pdo->commit();
} catch (Throwable $e) {
    $pdo->rollBack();
    throw $e;
}
```

### Asociar tipos con `bindValue`

```php
$stmt = $pdo->prepare("SELECT * FROM productos LIMIT :limite");
$stmt->bindValue(":limite", 10, PDO::PARAM_INT);
$stmt->execute();
```

---

## 5. Ejemplos prácticos

### Ejemplo básico

```php
$stmt = $pdo->prepare("SELECT nombre FROM usuarios WHERE id = :id");
$stmt->execute(["id" => 1]);
echo $stmt->fetchColumn();
```

### Ejemplo habitual: repositorio sencillo

```php
class UsuarioRepositorio
{
    public function __construct(private PDO $pdo) {}

    public function buscarPorEmail(string $email): ?array
    {
        $stmt = $this->pdo->prepare("SELECT * FROM usuarios WHERE email = :email");
        $stmt->execute(["email" => $email]);
        return $stmt->fetch() ?: null;
    }

    public function crear(string $nombre, string $email, string $password): int
    {
        $stmt = $this->pdo->prepare(
            "INSERT INTO usuarios (nombre, email, password_hash)
             VALUES (:nombre, :email, :hash)"
        );
        $stmt->execute([
            "nombre" => $nombre,
            "email"  => $email,
            "hash"   => password_hash($password, PASSWORD_DEFAULT),
        ]);
        return (int) $this->pdo->lastInsertId();
    }
}
```

---

## 6. Buenas prácticas

- Usa **siempre** consultas preparadas con datos externos.
- Activa `ERRMODE_EXCEPTION` y `utf8mb4`.
- Guarda las credenciales **fuera** del código (variables de entorno o `.env`).
- Crea la conexión **una vez** y pásala a las clases que la necesiten.
- Pide solo las columnas necesarias (`SELECT nombre, email`, no `SELECT *`).
- Usa transacciones cuando haya varias escrituras relacionadas.
- Los nombres de tablas y columnas **no se pueden** poner como marcador: valídalos con una lista permitida.
- No muestres nunca mensajes de error de la base de datos al usuario.

---

## 7. Diferencias importantes

| Comparación | Diferencia |
|---|---|
| `query()` vs `prepare()` | `query` ejecuta SQL directo (sin datos del usuario); `prepare` es seguro con datos externos |
| `fetch()` vs `fetchAll()` | `fetch` una fila; `fetchAll` todas (cuidado con memoria) |
| PDO vs `mysqli` | `mysqli` solo MySQL; PDO funciona con muchas bases de datos |
| `mysql_*` | **Obsoleto y eliminado**. Nunca lo uses |

---

## 8. Casos especiales

### `IN (...)` con una lista

```php
$ids = [1, 2, 3];
$marcadores = implode(",", array_fill(0, count($ids), "?"));

$stmt = $pdo->prepare("SELECT * FROM productos WHERE id IN ($marcadores)");
$stmt->execute($ids);
```

### `LIKE`

```php
$stmt = $pdo->prepare("SELECT * FROM productos WHERE nombre LIKE :busqueda");
$stmt->execute(["busqueda" => "%" . $texto . "%"]);
```

### Ordenar por columna elegida por el usuario

```php
$permitidas = ["nombre", "precio", "fecha"];
$columna = in_array($_GET["orden"] ?? "", $permitidas, true) ? $_GET["orden"] : "nombre";

$pdo->query("SELECT * FROM productos ORDER BY $columna");
```

### Conexión SQLite (útil para pruebas)

```php
$pdo = new PDO("sqlite:" . __DIR__ . "/datos.db");
```

### Paginación

```php
$porPagina = 10;
$pagina = max(1, (int) ($_GET["pagina"] ?? 1));
$offset = ($pagina - 1) * $porPagina;

$stmt = $pdo->prepare("SELECT * FROM productos LIMIT :lim OFFSET :off");
$stmt->bindValue(":lim", $porPagina, PDO::PARAM_INT);
$stmt->bindValue(":off", $offset, PDO::PARAM_INT);
$stmt->execute();
```

---

## 9. Resumen

- PDO conecta PHP con bases de datos con una interfaz común.
- Configura `ERRMODE_EXCEPTION`, `FETCH_ASSOC` y `utf8mb4`.
- `prepare` + `execute` con marcadores evita la inyección SQL.
- `fetch`, `fetchAll` y `fetchColumn` recogen resultados; `lastInsertId` da el nuevo ID.
- Las transacciones garantizan que un grupo de cambios se hace completo o no se hace.
- Nombres de tablas y columnas se validan con una lista permitida.