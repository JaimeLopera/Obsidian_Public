# 10 - Formularios

> [!info] ¿Qué es?
> Los formularios HTML envían datos al servidor. PHP **recibe, valida y procesa** esos datos usando `$_GET` y `$_POST`.

---

## 1. Antes de empezar

Debes conocer formularios HTML ([[HTML/06 - Formularios|HTML - Formularios]]), arrays (nota 07) y condicionales (nota 04).

---

## 2. Concepto fundamental

1. El usuario rellena el formulario y lo envía.
2. El navegador manda los datos al fichero PHP indicado en `action`.
3. PHP los recibe en un array superglobal (`$_GET` o `$_POST`).
4. PHP **valida** los datos y actúa (guardar, mostrar error, redirigir).

> [!warning] Regla de oro
> **Nunca te fíes de los datos del usuario.** Siempre se validan en el servidor, aunque ya existan validaciones en HTML o JavaScript.

---

## 3. Sintaxis / estructura

### Formulario HTML

```html
<form action="procesar.php" method="post">
  <label for="nombre">Nombre</label>
  <input type="text" id="nombre" name="nombre">

  <label for="email">Email</label>
  <input type="email" id="email" name="email">

  <button type="submit">Enviar</button>
</form>
```

El atributo **`name`** de cada campo es la clave con la que lo recibirás en PHP.

### Recibir datos

```php
$nombre = $_POST["nombre"] ?? "";
$email  = $_POST["email"] ?? "";
```

Usa `??` porque la clave puede no existir.

### Comprobar que se envió

```php
if ($_SERVER["REQUEST_METHOD"] === "POST") {
    // procesar
}
```

---

## 4. Elementos / características

### GET frente a POST

| | GET | POST |
|---|---|---|
| Datos | En la URL | En el cuerpo de la petición |
| Array PHP | `$_GET` | `$_POST` |
| Para | Búsquedas, filtros, paginación | Login, registro, crear o modificar datos |
| Se puede guardar en favoritos | Sí | No |
| Límite de tamaño | Sí (la URL) | Mucho mayor |

> [!tip] Regla simple
> Si la acción **cambia algo** (crear, editar, borrar), usa **POST**. Si solo **consulta**, puede ser GET.

### Tipos de campos especiales

```php
// Checkbox: solo llega si está marcado
$acepta = isset($_POST["terminos"]);

// Varios checkbox con name="aficiones[]"
$aficiones = $_POST["aficiones"] ?? [];   // array

// Select múltiple con name="paises[]"
$paises = $_POST["paises"] ?? [];

// Radio
$genero = $_POST["genero"] ?? null;
```

### Validar y sanear

Son dos ideas diferentes:

- **Validar**: comprobar que el dato es correcto (¿es un email?).
- **Sanear / limpiar**: preparar el dato (quitar espacios).

```php
$nombre = trim($_POST["nombre"] ?? "");
$edad   = filter_var($_POST["edad"] ?? "", FILTER_VALIDATE_INT, [
    "options" => ["min_range" => 0, "max_range" => 120],
]);
$email  = filter_var($_POST["email"] ?? "", FILTER_VALIDATE_EMAIL);
```

`filter_var` devuelve el valor si es válido, o `false` si no lo es.

### Acumular errores

```php
$errores = [];

if ($nombre === "") {
    $errores["nombre"] = "El nombre es obligatorio";
}
if ($email === false) {
    $errores["email"] = "El email no es válido";
}
if ($edad === false) {
    $errores["edad"] = "La edad no es válida";
}
```

### Mostrar errores y mantener valores

```php
<input type="text" name="nombre"
       value="<?= htmlspecialchars($nombre ?? "") ?>">
<?php if (isset($errores["nombre"])): ?>
    <span class="error"><?= $errores["nombre"] ?></span>
<?php endif; ?>
```

Siempre escapa con `htmlspecialchars` lo que vuelves a mostrar.

### Patrón Post / Redirect / Get

Tras procesar un POST con éxito, **redirige** para que al recargar la página no se reenvíe el formulario:

```php
header("Location: gracias.php");
exit;
```

`exit` es importante: detiene el script tras la redirección.

---

## 5. Ejemplos prácticos

### Ejemplo básico

```php
<?php
$nombre = htmlspecialchars($_POST["nombre"] ?? "invitado");
echo "Hola, $nombre";
```

### Ejemplo completo: formulario de contacto

```php
<?php
declare(strict_types=1);

$errores = [];
$nombre = $email = $mensaje = "";

if ($_SERVER["REQUEST_METHOD"] === "POST") {
    $nombre  = trim($_POST["nombre"] ?? "");
    $email   = trim($_POST["email"] ?? "");
    $mensaje = trim($_POST["mensaje"] ?? "");

    if ($nombre === "") {
        $errores["nombre"] = "Obligatorio";
    }
    if (!filter_var($email, FILTER_VALIDATE_EMAIL)) {
        $errores["email"] = "Email no válido";
    }
    if (mb_strlen($mensaje) < 10) {
        $errores["mensaje"] = "Mínimo 10 caracteres";
    }

    if (!$errores) {
        // guardar o enviar el mensaje
        header("Location: gracias.php");
        exit;
    }
}
?>
<form method="post">
  <input name="nombre" value="<?= htmlspecialchars($nombre) ?>">
  <?= $errores["nombre"] ?? "" ?>

  <input name="email" value="<?= htmlspecialchars($email) ?>">
  <?= $errores["email"] ?? "" ?>

  <textarea name="mensaje"><?= htmlspecialchars($mensaje) ?></textarea>
  <?= $errores["mensaje"] ?? "" ?>

  <button>Enviar</button>
</form>
```

---

## 6. Buenas prácticas

- Valida **siempre** en el servidor.
- Usa POST para acciones que modifican datos.
- Usa `??` para leer claves que pueden no existir.
- Usa `trim` en textos y `filter_var` en emails, enteros y URLs.
- Escapa con `htmlspecialchars` al volver a mostrar datos.
- Redirige tras un POST correcto.
- Protege formularios importantes con un token CSRF (nota 16).
- No digas más de lo necesario en errores de login ("usuario o contraseña incorrectos").

---

## 7. Diferencias importantes

| Concepto | Diferencia |
|---|---|
| Validar vs sanear | Validar comprueba; sanear modifica |
| Validación HTML/JS vs PHP | La de HTML/JS es comodidad; la de PHP es **seguridad** |
| `$_POST` vs `$_REQUEST` | `$_REQUEST` mezcla GET, POST y cookies; evítalo |

---

## 8. Casos especiales

### Subida de ficheros

Requiere `method="post"` y `enctype="multipart/form-data"`. Los archivos llegan en `$_FILES` (nota [[12 - Ficheros|12 - Ficheros]]).

### Leer datos con `filter_input`

```php
$email = filter_input(INPUT_POST, "email", FILTER_VALIDATE_EMAIL);
```

Equivale a leer de `$_POST` y filtrar a la vez.

### Peticiones JSON

Si el cliente envía JSON (por ejemplo con `fetch`), `$_POST` estará vacío:

```php
$datos = json_decode(file_get_contents("php://input"), true);
```

### Campos desactivados o ausentes

Los campos `disabled` no se envían. Un checkbox sin marcar tampoco.

---

## 9. Resumen

- Los datos llegan en `$_GET` o `$_POST` según el método.
- El atributo `name` define la clave.
- Usa `??` para leer, `trim` y `filter_var` para limpiar y validar.
- Acumula los errores en un array y vuelve a mostrar los valores escapados.
- Tras un POST correcto, redirige con `header("Location: ...")` y `exit`.
- La validación en el servidor es obligatoria.