# 13 - JSON y APIs

> [!info] ¿Qué es?
> **JSON** es el formato de texto más usado para intercambiar datos entre programas. Una **API** es un servicio al que otros programas (una web con JavaScript, una app móvil, otro servidor) hacen peticiones y reciben datos, normalmente en JSON. Con PHP puedes **crear** APIs y **consumir** las de otros.

---

## 1. Antes de empezar

Debes conocer arrays (nota [[PHP/07 - Arrays|07]]), formularios (nota [[PHP/10 - Formularios|10]]), excepciones (nota [[PHP/15 - Excepciones|15]]) y PDO (nota [[PHP/16 - PDO y bases de datos|16]]). La teoría general está en [[Conceptos generales/01 - HTTP|HTTP]], [[Conceptos generales/02 - APIs REST|APIs REST]] y [[Conceptos generales/03 - JSON|JSON]].

---

## 2. Concepto fundamental

Una API en PHP es un script que:

1. **Lee la petición**: método (GET, POST...), ruta, cabeceras y cuerpo.
2. **Hace su trabajo** (consultar la base de datos, validar, guardar).
3. **Responde** con un **código de estado**, **cabeceras** y un **cuerpo JSON**.

Para pasar datos entre PHP y JSON:

| PHP | JSON |
|---|---|
| Array asociativo | Objeto `{ }` |
| Array lista (`0, 1, 2...`) | Array `[ ]` |
| `string`, `int`, `float`, `bool` | Texto, número, `true` / `false` |
| `null` | `null` |

---

## 3. Sintaxis / estructura

### Convertir PHP → JSON

```php
$datos = ["nombre" => "Ana", "edad" => 20, "roles" => ["admin", "editor"]];

$json = json_encode($datos, JSON_UNESCAPED_UNICODE | JSON_UNESCAPED_SLASHES | JSON_THROW_ON_ERROR);
// {"nombre":"Ana","edad":20,"roles":["admin","editor"]}
```

Banderas más útiles de `json_encode`:

| Bandera | Qué hace |
|---|---|
| `JSON_PRETTY_PRINT` | Con sangrías (para leer o guardar en ficheros) |
| `JSON_UNESCAPED_UNICODE` | Deja `ñ`, tildes y emojis tal cual (si no, salen como `\u00f1`) |
| `JSON_UNESCAPED_SLASHES` | No escapa las barras `/` |
| `JSON_THROW_ON_ERROR` | Lanza una excepción `JsonException` si algo falla |
| `JSON_PRESERVE_ZERO_FRACTION` | Mantiene `10.0` en lugar de `10` |
| `JSON_FORCE_OBJECT` | Convierte los arrays lista en objetos |
| `JSON_HEX_TAG`, `JSON_HEX_AMP`, `JSON_HEX_QUOT`, `JSON_HEX_APOS` | Escapan caracteres para incrustar JSON en HTML |
| `JSON_INVALID_UTF8_SUBSTITUTE` | Sustituye el UTF-8 inválido en vez de fallar |

Se combinan con `|`.

### Convertir JSON → PHP

```php
$array  = json_decode($json, true, 512, JSON_THROW_ON_ERROR);   // true = arrays asociativos
$objeto = json_decode($json, false, 512, JSON_THROW_ON_ERROR);  // objetos stdClass
```

- Sin `true`, los objetos JSON se convierten en objetos `stdClass` (`$objeto->nombre`).
- Sin `JSON_THROW_ON_ERROR`, un JSON mal formado devuelve `null` (y es imposible distinguirlo de un JSON que valga `null`). Usa siempre la bandera.
- Sin esa bandera, puedes consultar `json_last_error()` y `json_last_error_msg()`.
- `json_validate($texto)` (PHP 8.3) comprueba si un texto es JSON válido sin convertirlo.

### Casos que sorprenden al convertir

```php
json_encode([]);                      // []     (un array vacío es siempre una lista)
json_encode(new stdClass());          // {}     (así se obtiene un objeto vacío)
json_encode([1 => "a", 2 => "b"]);    // {"1":"a","2":"b"}   ¡objeto! (las claves no empiezan en 0)
json_encode(array_values([1 => "a", 2 => "b"]));   // ["a","b"]
```

> [!warning] Arrays con huecos
> Después de `unset` o `array_filter`, un array puede dejar de ser una lista y `json_encode` lo convierte en **objeto**. Usa `array_values()` antes de convertir.

### Objetos y JSON

- `json_encode` solo incluye las propiedades **públicas** de un objeto.
- Para controlarlo, implementa `JsonSerializable`:

```php
class Usuario implements JsonSerializable
{
    public function __construct(
        private int $id,
        private string $nombre,
        private string $passwordHash,
    ) {}

    public function jsonSerialize(): array
    {
        return ["id" => $this->id, "nombre" => $this->nombre];   // sin el hash
    }
}

echo json_encode(new Usuario(1, "Ana", "xxxx"));   // {"id":1,"nombre":"Ana"}
```

- Un `enum` con valor (backed enum) se convierte en su valor.
- Un `DateTime` se convierte en un objeto con `date`, `timezone_type` y `timezone`: formatéalo tú con `->format(DATE_ATOM)` antes.

---

## 4. Elementos / características

### Responder JSON

```php
function responder(mixed $datos, int $codigo = 200): never
{
    http_response_code($codigo);

    if ($codigo !== 204) {   // 204 no lleva cuerpo
        header("Content-Type: application/json; charset=utf-8");
        echo json_encode($datos, JSON_UNESCAPED_UNICODE | JSON_UNESCAPED_SLASHES | JSON_THROW_ON_ERROR);
    }

    exit;
}
```

Las cabeceras (`header`, `http_response_code`) deben enviarse **antes de cualquier salida**.

### Códigos de estado más usados

| Código | Significado | Cuándo |
|---|---|---|
| `200` | OK | Consulta o modificación correcta |
| `201` | Created | Se creó un recurso |
| `204` | No Content | Correcto, sin cuerpo (típico en `DELETE`) |
| `400` | Bad Request | Petición mal formada (por ejemplo JSON roto) |
| `401` | Unauthorized | No autenticado |
| `403` | Forbidden | Autenticado, pero sin permiso |
| `404` | Not Found | No existe el recurso |
| `405` | Method Not Allowed | Método no permitido (añade la cabecera `Allow`) |
| `409` | Conflict | Choque con el estado actual (por ejemplo, email repetido) |
| `415` | Unsupported Media Type | Formato no admitido |
| `422` | Unprocessable Content | Datos con formato correcto pero no válidos |
| `429` | Too Many Requests | Demasiadas peticiones |
| `500` | Internal Server Error | Fallo del servidor |

### Leer la petición

| Qué | Cómo |
|---|---|
| Método | `$_SERVER["REQUEST_METHOD"]` |
| Ruta | `parse_url($_SERVER["REQUEST_URI"], PHP_URL_PATH)` |
| Parámetros de la URL | `$_GET` |
| Tipo de contenido | `$_SERVER["CONTENT_TYPE"] ?? ""` |
| Cabeceras | `getallheaders()` o `$_SERVER["HTTP_NOMBRE"]` (`HTTP_AUTHORIZATION`, `HTTP_X_API_KEY`...) |
| Cuerpo en bruto | `file_get_contents("php://input")` |

Cuando el cliente envía JSON, `$_POST` queda **vacío**: hay que leer `php://input`.

> [!note] Cabecera `Authorization`
> En algunos servidores (Apache con PHP-FPM o CGI) la cabecera `Authorization` no llega a PHP si no se configura una regla de reescritura en el servidor.

```php
function leerJson(): array
{
    $tipo = $_SERVER["CONTENT_TYPE"] ?? "";

    if (!str_starts_with($tipo, "application/json")) {
        responderError("Content-Type debe ser application/json", 415);
    }

    try {
        $datos = json_decode(file_get_contents("php://input"), true, 512, JSON_THROW_ON_ERROR);
    } catch (JsonException) {
        responderError("JSON no válido", 400);
    }

    if (!is_array($datos)) {
        responderError("Se esperaba un objeto JSON", 400);
    }

    return $datos;
}

function responderError(string $mensaje, int $codigo, array $extra = []): never
{
    responder(["error" => ["mensaje" => $mensaje] + $extra], $codigo);
}
```

### Diseño de una API REST

| Acción | Método y ruta | Respuesta correcta |
|---|---|---|
| Listar | `GET /tareas` | `200` con la lista |
| Ver una | `GET /tareas/5` | `200` o `404` |
| Crear | `POST /tareas` | `201` con el recurso (y cabecera `Location`) |
| Reemplazar | `PUT /tareas/5` | `200` |
| Modificar parte | `PATCH /tareas/5` | `200` |
| Borrar | `DELETE /tareas/5` | `204` |

---

## 5. Ejemplos prácticos

### Ejemplo básico

```php
header("Content-Type: application/json; charset=utf-8");
echo json_encode(["hora" => date("c"), "version" => PHP_VERSION]);
```

### Ejemplo completo: API de tareas

Tabla de la base de datos:

```sql
CREATE TABLE tareas (
    id     INT AUTO_INCREMENT PRIMARY KEY,
    titulo VARCHAR(100) NOT NULL,
    hecha  TINYINT(1) NOT NULL DEFAULT 0
);
```

Fichero `api.php` (junto con `responder`, `responderError` y `leerJson` de arriba):

```php
<?php
declare(strict_types=1);

require __DIR__ . "/conexion.php";   // define $pdo (nota 16)

set_exception_handler(function (Throwable $e): void {
    error_log($e->getMessage());
    responderError("Error interno del servidor", 500);
});

function listar(PDO $pdo): never
{
    $pagina = max(1, (int) ($_GET["pagina"] ?? 1));
    $por    = min(50, max(1, (int) ($_GET["por_pagina"] ?? 10)));

    $stmt = $pdo->prepare("SELECT id, titulo, hecha FROM tareas ORDER BY id LIMIT :lim OFFSET :off");
    $stmt->bindValue(":lim", $por, PDO::PARAM_INT);
    $stmt->bindValue(":off", ($pagina - 1) * $por, PDO::PARAM_INT);
    $stmt->execute();

    responder(["datos" => $stmt->fetchAll(), "pagina" => $pagina, "por_pagina" => $por]);
}

function obtener(PDO $pdo, int $id): never
{
    $stmt = $pdo->prepare("SELECT id, titulo, hecha FROM tareas WHERE id = :id");
    $stmt->execute(["id" => $id]);
    $tarea = $stmt->fetch();

    if (!$tarea) {
        responderError("Tarea no encontrada", 404);
    }

    responder($tarea);
}

function crear(PDO $pdo): never
{
    $datos  = leerJson();
    $titulo = $datos["titulo"] ?? null;

    if (!is_string($titulo) || trim($titulo) === "" || mb_strlen($titulo) > 100) {
        responderError("Datos no válidos", 422, [
            "campos" => ["titulo" => "Obligatorio, texto de hasta 100 caracteres"],
        ]);
    }

    $stmt = $pdo->prepare("INSERT INTO tareas (titulo) VALUES (:titulo)");
    $stmt->execute(["titulo" => trim($titulo)]);
    $id = (int) $pdo->lastInsertId();

    header("Location: /tareas/$id");
    responder(["id" => $id, "titulo" => trim($titulo), "hecha" => 0], 201);
}

function actualizar(PDO $pdo, int $id): never
{
    $datos   = leerJson();
    $campos  = [];
    $valores = ["id" => $id];

    if (array_key_exists("titulo", $datos)) {
        if (!is_string($datos["titulo"]) || trim($datos["titulo"]) === "" || mb_strlen($datos["titulo"]) > 100) {
            responderError("Título no válido", 422);
        }
        $campos[] = "titulo = :titulo";               // nombres de columna fijos en el código
        $valores["titulo"] = trim($datos["titulo"]);
    }

    if (array_key_exists("hecha", $datos)) {
        if (!is_bool($datos["hecha"])) {
            responderError("«hecha» debe ser true o false", 422);
        }
        $campos[] = "hecha = :hecha";
        $valores["hecha"] = (int) $datos["hecha"];
    }

    if (!$campos) {
        responderError("No hay nada que actualizar", 422);
    }

    $stmt = $pdo->prepare("UPDATE tareas SET " . implode(", ", $campos) . " WHERE id = :id");
    $stmt->execute($valores);

    obtener($pdo, $id);   // devuelve la tarea actualizada (o 404)
}

function borrar(PDO $pdo, int $id): never
{
    $stmt = $pdo->prepare("DELETE FROM tareas WHERE id = :id");
    $stmt->execute(["id" => $id]);

    if ($stmt->rowCount() === 0) {
        responderError("Tarea no encontrada", 404);
    }

    responder(null, 204);
}

// --- Enrutado ---
$metodo = $_SERVER["REQUEST_METHOD"];
$ruta   = parse_url($_SERVER["REQUEST_URI"], PHP_URL_PATH);

if (!preg_match("#^/tareas(?:/(\d+))?$#", $ruta, $m)) {
    responderError("Ruta no encontrada", 404);
}

$id = isset($m[1]) ? (int) $m[1] : null;

match (true) {
    $metodo === "GET"    && $id === null => listar($pdo),
    $metodo === "GET"    && $id !== null => obtener($pdo, $id),
    $metodo === "POST"   && $id === null => crear($pdo),
    in_array($metodo, ["PUT", "PATCH"], true) && $id !== null => actualizar($pdo, $id),
    $metodo === "DELETE" && $id !== null => borrar($pdo, $id),
    default => responderError("Método no permitido", 405),
};
```

Para probarla:

```bash
php -S localhost:8000 api.php

curl -X POST http://localhost:8000/tareas \
  -H "Content-Type: application/json" \
  -d '{"titulo":"Estudiar PHP"}'

curl http://localhost:8000/tareas
curl -X PATCH http://localhost:8000/tareas/1 -H "Content-Type: application/json" -d '{"hecha":true}'
curl -X DELETE http://localhost:8000/tareas/1
```

---

## 6. Buenas prácticas

- Usa siempre `JSON_THROW_ON_ERROR` al convertir.
- Responde con el **código de estado correcto** y siempre con el mismo formato de error.
- **Valida todo** lo que llega (tipos, longitudes, rangos), también en JSON.
- No devuelvas datos internos (hashes de contraseña, trazas de error, consultas SQL).
- Registra los errores en un log y devuelve al cliente un mensaje genérico.
- Usa consultas preparadas y paginación con un límite máximo.
- Versiona la API (`/api/v1/...`) cuando vaya a usarla otra gente.
- Usa HTTPS.

---

## 7. Diferencias importantes

| Comparación | Diferencia |
|---|---|
| `json_decode($j)` vs `json_decode($j, true)` | El primero da objetos `stdClass`; el segundo arrays |
| `$_POST` vs `php://input` | `$_POST` solo funciona con formularios; con JSON hay que leer `php://input` |
| `PUT` vs `PATCH` | `PUT` reemplaza todo el recurso; `PATCH` cambia solo algunos campos |
| `401` vs `403` | `401`: no sabemos quién eres; `403`: sabemos quién eres, pero no puedes |
| `400` vs `422` | `400`: la petición está mal formada; `422`: está bien formada pero los datos no son válidos |

---

## 8. Casos especiales

### CORS (si tu API la usa JavaScript desde otro dominio)

El navegador bloquea las peticiones entre dominios si el servidor no lo permite.

```php
$permitidos = ["https://miweb.com", "http://localhost:3000"];
$origen = $_SERVER["HTTP_ORIGIN"] ?? "";

if (in_array($origen, $permitidos, true)) {
    header("Access-Control-Allow-Origin: $origen");
    header("Vary: Origin");
    header("Access-Control-Allow-Methods: GET, POST, PUT, PATCH, DELETE, OPTIONS");
    header("Access-Control-Allow-Headers: Content-Type, Authorization");
}

if ($_SERVER["REQUEST_METHOD"] === "OPTIONS") {   // petición previa (preflight)
    http_response_code(204);
    exit;
}
```

No uses `*` si la API trabaja con cookies o credenciales.

### Autenticación

- **Misma web y mismo dominio**: sesiones (nota [[PHP/11 - Sesiones y cookies|11]]).
- **APIs para otros programas**: clave de API o *token Bearer* en la cabecera `Authorization: Bearer ...`. Guarda el **hash** del token y compáralo con `hash_equals`.
- **JWT**: usa una librería probada (por ejemplo `firebase/php-jwt`); no implementes criptografía a mano.

### Consumir una API externa con cURL

Necesita la extensión `curl`.

```php
$ch = curl_init("https://api.ejemplo.com/datos");

curl_setopt_array($ch, [
    CURLOPT_RETURNTRANSFER => true,          // devolver la respuesta como texto
    CURLOPT_TIMEOUT        => 10,            // no esperar más de 10 s
    CURLOPT_HTTPHEADER     => ["Accept: application/json", "Authorization: Bearer $token"],
]);

$respuesta = curl_exec($ch);

if ($respuesta === false) {
    throw new RuntimeException("Error de red: " . curl_error($ch));
}

$codigo = curl_getinfo($ch, CURLINFO_RESPONSE_CODE);

if ($codigo >= 400) {
    throw new RuntimeException("La API respondió con $codigo");
}

$datos = json_decode($respuesta, true, 512, JSON_THROW_ON_ERROR);
```

Para enviar JSON por POST:

```php
curl_setopt_array($ch, [
    CURLOPT_POST       => true,
    CURLOPT_POSTFIELDS => json_encode($payload, JSON_THROW_ON_ERROR),
    CURLOPT_HTTPHEADER => ["Content-Type: application/json", "Accept: application/json"],
    CURLOPT_RETURNTRANSFER => true,
]);
```

- No desactives la verificación de certificados SSL (`CURLOPT_SSL_VERIFYPEER`).
- Desde PHP 8 no hace falta `curl_close()`: el recurso se libera solo.
- Para algo más cómodo, existen librerías como **Guzzle** o **Symfony HttpClient** (se instalan con Composer, nota [[PHP/17 - Composer|17]]).
- Si la dirección la escribe el usuario, ten cuidado con el SSRF (nota [[PHP/18 - Seguridad|18]]).

### Servir la API con Apache

Para que todas las rutas lleguen a `index.php`, usa un `.htaccess`:

```apache
RewriteEngine On
RewriteCond %{REQUEST_FILENAME} !-f
RewriteRule ^ index.php [QSA,L]
```

---

## 9. Resumen

- `json_encode` convierte PHP en JSON; `json_decode` hace lo contrario. Usa siempre `JSON_THROW_ON_ERROR`.
- Un array asociativo es un objeto JSON; una lista es un array JSON; arrays con huecos salen como objeto (usa `array_values`).
- Para responder: código con `http_response_code`, cabecera `Content-Type: application/json` y `echo json_encode(...)`.
- Para leer un cuerpo JSON: `file_get_contents("php://input")`; `$_POST` no sirve.
- Usa los códigos de estado con sentido (`201`, `204`, `404`, `422`...).
- Una API REST combina método + ruta + validación + PDO.
- Para consumir APIs externas se usa cURL (o Guzzle), con tiempo máximo y comprobando el código de respuesta.