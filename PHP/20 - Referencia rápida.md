# 20 - Referencia rápida

> [!info] ¿Para qué sirve?
> Chuleta con lo que más se usa en PHP. Si necesitas la explicación completa, ve a la nota de cada tema.

---

## Estructura básica

```php
<?php
declare(strict_types=1);

require_once __DIR__ . "/vendor/autoload.php";

echo "Hola";
```

| Cosa | Sintaxis |
|---|---|
| Variable | `$nombre = "Ana";` |
| Constante | `const IVA = 0.21;` |
| Comentarios | `//`  `#`  `/* */` |
| Mostrar | `echo`, `print_r()`, `var_dump()` |
| Incluir | `require_once`, `include` |
| Constantes mágicas | `__FILE__`, `__DIR__`, `__LINE__`, `__CLASS__`, `__FUNCTION__` |

---

## Tipos y comprobaciones

| Función | Uso |
|---|---|
| `gettype()` / `var_dump()` | Ver el tipo |
| `is_int`, `is_string`, `is_array`, `is_numeric` | Comprobar tipo |
| `isset()` | Existe y no es `null` |
| `empty()` | No existe o es "falsy" |
| `(int)`, `(string)`, `(float)`, `(bool)`, `(array)` | Conversión |

**Falsy**: `false`, `0`, `0.0`, `""`, `"0"`, `[]`, `null`.

---

## Operadores clave

| Operador | Uso |
|---|---|
| `===` / `!==` | Comparación estricta |
| `<=>` | Compara (-1, 0, 1) |
| `??` / `??=` | Valor por defecto si es `null` / asignar si es `null` |
| `?:` | Valor por defecto si es falsy |
| `?->` | Nullsafe |
| `.` / `.=` | Concatenar texto |
| `**` / `%` | Potencia / resto |
| `&` `\|` `^` `~` `<<` `>>` | Bit a bit (banderas) |

---

## Condicionales y bucles

```php
if ($a) { } elseif ($b) { } else { }

$r = match ($x) { 1 => "uno", 2 => "dos", default => "otro" };

for ($i = 0; $i < 5; $i++) { }
while ($cond) { }
do { } while ($cond);
foreach ($array as $clave => $valor) { }
```

| Palabra | Qué hace |
|---|---|
| `break` | Sale del bucle |
| `continue` | Pasa a la siguiente vuelta |

---

## Funciones

```php
function suma(int $a, int $b = 0): int { return $a + $b; }

$f = function (int $x) use ($y) { return $x + $y; };
$g = fn(int $x): int => $x * 2;

function total(int ...$n): int { return array_sum($n); }

suma(b: 5, a: 1);        // argumentos con nombre
$h = strlen(...);        // función como valor
```

---

## Arrays

```php
$a = [1, 2, 3];
$b = ["clave" => "valor"];
$a[] = 4;
unset($a[0]);
[$x, $y] = [1, 2];
```

| Función | Qué hace |
|---|---|
| `count`, `empty`, `isset`, `array_key_exists` | Información y comprobaciones |
| `in_array($v, $a, true)`, `array_search` | Buscar |
| `array_key_first`, `array_key_last` | Primera y última clave |
| `array_push`, `array_pop`, `array_shift`, `array_unshift`, `array_splice` | Añadir y quitar |
| `array_slice`, `array_merge`, `[...$a, ...$b]`, `array_chunk` | Extraer y combinar |
| `array_keys`, `array_values`, `array_flip`, `array_column` | Claves y valores |
| `array_unique`, `array_reverse`, `array_count_values` | Utilidades |
| `array_diff`, `array_intersect` (y `_key`) | Comparar arrays |
| `array_sum`, `array_product`, `min`, `max` | Cálculos |
| `array_map`, `array_filter`, `array_reduce`, `array_walk` | Transformar |
| `array_find`, `array_any`, `array_all` (8.4), `array_first`, `array_last` (8.5) | Buscar con función |
| `sort`, `rsort`, `asort`, `arsort`, `ksort`, `usort`, `uasort` | Ordenar |
| `explode`, `implode` | Texto ↔ array |

---

## Strings

| Función | Qué hace |
|---|---|
| `strlen`, `mb_strlen` | Longitud |
| `strtolower`, `strtoupper`, `ucfirst`, `ucwords` | Mayúsculas / minúsculas |
| `trim`, `ltrim`, `rtrim` | Quitar espacios |
| `str_replace`, `substr`, `str_repeat`, `str_pad` | Transformar |
| `str_contains`, `str_starts_with`, `str_ends_with`, `strpos` | Buscar |
| `sprintf`, `number_format` | Formatear |
| `preg_match`, `preg_replace`, `preg_split` | Expresiones regulares |
| `htmlspecialchars` | Escapar HTML |

Comillas dobles interpretan `$variable`; las simples no.

---

## Matemáticas y fechas

```php
round(3.14159, 2);   floor(5.7);   ceil(5.1);   intdiv(7, 2);   abs(-3);
random_int(1, 6);                  // seguro
number_format(1234.5, 2, ",", ".");

date("d/m/Y H:i");
$f = new DateTimeImmutable("2026-10-10");
$f->modify("+1 day");  $f->add(new DateInterval("P1M"));
$f->diff($otra)->days;
DateTimeImmutable::createFromFormat("!d/m/Y", "25/12/2026");
date_default_timezone_set("Europe/Madrid");
```

| `DateInterval` | Significado |
|---|---|
| `P1D` / `P2W` / `P3M` / `P1Y` | Días / semanas / meses / años |
| `PT4H` / `PT30M` | Horas / minutos |

---

## Formularios

```php
$nombre = trim($_POST["nombre"] ?? "");
$email  = filter_var($_POST["email"] ?? "", FILTER_VALIDATE_EMAIL);

if ($_SERVER["REQUEST_METHOD"] === "POST") { }

header("Location: gracias.php");
exit;
```

| Superglobal | Contenido |
|---|---|
| `$_GET` | Datos de la URL |
| `$_POST` | Datos del formulario |
| `$_FILES` | Ficheros subidos |
| `$_SESSION` | Datos de sesión |
| `$_COOKIE` | Cookies |
| `$_SERVER` | Información del servidor y la petición |

---

## Sesiones y cookies

```php
session_start();
$_SESSION["usuario_id"] = 5;
session_regenerate_id(true);
session_destroy();

setcookie("tema", "oscuro", [
    "expires" => time() + 3600, "path" => "/",
    "secure" => true, "httponly" => true, "samesite" => "Lax",
]);
```

---

## Ficheros

```php
file_get_contents($ruta);
file_put_contents($ruta, $texto, FILE_APPEND | LOCK_EX);

$f = fopen($ruta, "r");     // r, w, a, x, c
while (($linea = fgets($f)) !== false) { }
fclose($f);

move_uploaded_file($_FILES["f"]["tmp_name"], $destino);
```

| Función | Qué hace |
|---|---|
| `file_exists`, `is_file`, `is_dir` | Comprobar |
| `unlink`, `rename`, `copy`, `mkdir`, `scandir` | Gestionar |
| `basename`, `dirname`, `pathinfo`, `realpath` | Rutas |
| `fgetcsv`, `fputcsv` | CSV |

---

## JSON y APIs

```php
$json  = json_encode($datos, JSON_UNESCAPED_UNICODE | JSON_THROW_ON_ERROR);
$datos = json_decode($json, true, 512, JSON_THROW_ON_ERROR);

http_response_code(201);
header("Content-Type: application/json; charset=utf-8");
echo json_encode($respuesta);

$cuerpo = json_decode(file_get_contents("php://input"), true);
```

| Código | Significado |
|---|---|
| `200` / `201` / `204` | OK / creado / sin contenido |
| `400` / `422` | Petición mal formada / datos no válidos |
| `401` / `403` / `404` | No autenticado / sin permiso / no existe |
| `405` / `429` / `500` | Método no permitido / demasiadas peticiones / error del servidor |

---

## POO

```php
namespace App\Modelos;

interface Pago { public function pagar(float $importe): bool; }

abstract class Base { abstract public function ejecutar(): void; }

trait Registra { public function log(string $m): void { echo $m; } }

final class Usuario extends Base implements Pago
{
    use Registra;

    public const ROL = "usuario";

    public function __construct(
        private readonly string $nombre,
        protected int $edad = 18,
    ) {}

    public function ejecutar(): void {}

    public function pagar(float $importe): bool { return true; }

    public static function crear(string $nombre): self { return new self($nombre); }
}

$u = new Usuario("Ana");
$u->pagar(10.0);
Usuario::crear("Ana");
Usuario::ROL;
```

| Visibilidad | Acceso |
|---|---|
| `public` | Todos |
| `protected` | Clase e hijas |
| `private` | Solo la clase |

```php
enum Estado: string { case Activo = "activo"; }
Estado::from("activo");   Estado::tryFrom("x");   Estado::Activo->value;
```

---

## Excepciones

```php
try {
    throw new InvalidArgumentException("Mensaje");
} catch (InvalidArgumentException | TypeError $e) {
    error_log($e->getMessage());
} finally {
    // siempre se ejecuta
}
```

---

## PDO

```php
$pdo = new PDO("mysql:host=localhost;dbname=bd;charset=utf8mb4", $usuario, $pass, [
    PDO::ATTR_ERRMODE => PDO::ERRMODE_EXCEPTION,
    PDO::ATTR_DEFAULT_FETCH_MODE => PDO::FETCH_ASSOC,
    PDO::ATTR_EMULATE_PREPARES => false,
]);

$stmt = $pdo->prepare("SELECT * FROM usuarios WHERE id = :id");
$stmt->execute(["id" => $id]);
$fila  = $stmt->fetch();
$filas = $stmt->fetchAll();
$id    = $pdo->lastInsertId();

$pdo->beginTransaction();
$pdo->commit();
$pdo->rollBack();
```

---

## Composer

| Comando | Qué hace |
|---|---|
| `composer init` | Crea `composer.json` |
| `composer require paquete` | Instala un paquete |
| `composer require --dev paquete` | Solo desarrollo |
| `composer install` | Instala según el `.lock` |
| `composer update` | Actualiza versiones |
| `composer dump-autoload` | Regenera el autoload |
| `composer audit` | Revisa vulnerabilidades |

---

## Seguridad: lo imprescindible

| Riesgo | Defensa |
|---|---|
| Inyección SQL | Consultas preparadas |
| XSS | `htmlspecialchars` al mostrar |
| CSRF | Token con `random_bytes` y `hash_equals` |
| Contraseñas | `password_hash` / `password_verify` |
| Permisos (IDOR) | Comprobar propietario y rol en cada acción |
| Rutas y `include` | `realpath`, `basename` y listas permitidas |
| Subidas de ficheros | Validar tipo real, tamaño y renombrar |
| `unserialize` | No usarlo con datos externos; usar JSON |
| Sesiones | `session_regenerate_id(true)`, cookies seguras |
| Errores | Ocultos al usuario, guardados en log |

---

## PHP moderno

| Característica | Ejemplo |
|---|---|
| Tipos estrictos | `declare(strict_types=1);` |
| Enum | `enum Rol: string { case Admin = "admin"; }` |
| Readonly | `public readonly string $nombre` |
| Match | `match ($x) { ... }` |
| Nullsafe | `$a?->b?->c` |
| Property hooks (8.4) | `public string $n { set => trim($value); }` |
| Pipe (8.5) | `$x \|> trim(...) \|> strtolower(...)` |

---

## Comandos de terminal útiles

```bash
php -v                    # versión
php -S localhost:8000     # servidor integrado
php script.php            # ejecutar un script
php -l fichero.php        # comprobar sintaxis
php -m                    # extensiones cargadas
```