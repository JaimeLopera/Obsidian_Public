# 18 - Seguridad

> [!info] ¿Qué es?
> La seguridad en PHP consiste en **no fiarse de los datos que llegan** y proteger lo que se guarda y se muestra. La mayoría de los ataques aprovechan datos sin validar, sin escapar o permisos sin comprobar.

---

## 1. Antes de empezar

Debes conocer formularios (nota [[PHP/10 - Formularios|10]]), sesiones (nota [[PHP/11 - Sesiones y cookies|11]]), ficheros (nota [[PHP/12 - Ficheros|12]]) y PDO (nota [[PHP/16 - PDO y bases de datos|16]]). La teoría general está en [[Conceptos generales/05 - Seguridad web|Seguridad web]].

---

## 2. Concepto fundamental

Dos ideas mandan sobre todo lo demás:

1. **Todo dato externo es sospechoso**: `$_GET`, `$_POST`, `$_COOKIE`, `$_FILES`, cabeceras, `$_SERVER`, ficheros subidos, respuestas de otras APIs...
2. **Cada dato se protege según dónde se usa**: en SQL, en HTML, en rutas de ficheros, en comandos del sistema...

Y una regla de organización: **valida al entrar, escapa al salir, y comprueba permisos siempre**.

---

## 3. Sintaxis / estructura

### 1. Inyección SQL

**Problema**: el atacante mete SQL dentro de un campo.

```php
// ❌ Vulnerable
$pdo->query("SELECT * FROM usuarios WHERE email = '$email'");
```

Si `$email` vale `' OR '1'='1`, devuelve todos los usuarios.

**Solución**: consultas preparadas.

```php
// ✅ Seguro
$stmt = $pdo->prepare("SELECT * FROM usuarios WHERE email = :email");
$stmt->execute(["email" => $email]);
```

- Los **nombres de tablas y columnas** no se pueden poner como marcador: valídalos con una lista permitida.
- El usuario de la base de datos de tu aplicación debe tener **los permisos mínimos** (no uses `root`).

### 2. XSS (Cross-Site Scripting)

**Problema**: el atacante guarda código JavaScript (por ejemplo en un comentario) y se ejecuta en el navegador de otros usuarios.

```php
// ❌ Vulnerable
echo $_GET["nombre"];
```

**Solución**: escapar al mostrar.

```php
// ✅ Seguro
echo htmlspecialchars($_GET["nombre"], ENT_QUOTES, "UTF-8");
```

- `strip_tags` **no** es una protección completa.
- `FILTER_SANITIZE_STRING` está **obsoleto** desde PHP 8.1: no lo uses.

**Escapar según el contexto:**

| Dónde lo pones | Cómo lo proteges |
|---|---|
| Texto o atributo HTML | `htmlspecialchars($x, ENT_QUOTES, "UTF-8")` |
| Parte de una URL | `rawurlencode($x)` o `http_build_query([...])` |
| Valor en JavaScript | `json_encode($x, JSON_HEX_TAG \| JSON_HEX_AMP \| JSON_HEX_APOS \| JSON_HEX_QUOT)` |
| Consulta SQL | Consultas preparadas |
| Comando del sistema | `escapeshellarg($x)` |

Una función de ayuda para no olvidarlo:

```php
function e(string $texto): string
{
    return htmlspecialchars($texto, ENT_QUOTES, "UTF-8");
}
```

### 3. CSRF (Cross-Site Request Forgery)

**Problema**: otra web consigue que el navegador del usuario envíe una petición (por ejemplo "borrar cuenta") a tu sitio, aprovechando su sesión abierta.

**Solución**: un **token** secreto en cada formulario que cambia datos.

```php
// Al generar el formulario
session_start();

if (empty($_SESSION["csrf"])) {
    $_SESSION["csrf"] = bin2hex(random_bytes(32));
}
```

```html
<input type="hidden" name="csrf" value="<?= htmlspecialchars($_SESSION["csrf"]) ?>">
```

```php
// Al procesarlo
if (!hash_equals($_SESSION["csrf"] ?? "", $_POST["csrf"] ?? "")) {
    http_response_code(403);
    exit("Petición no válida");
}
```

Además:

- Las acciones que **cambian** datos nunca deben hacerse con `GET`.
- Cookies de sesión con `samesite` en `Lax` o `Strict`.
- Una API que usa tokens Bearer (no cookies) no sufre CSRF.

### 4. Contraseñas

```php
// Guardar
$hash = password_hash($password, PASSWORD_DEFAULT);

// Comprobar
if (password_verify($password, $hash)) { }

// Actualizar el hash si el algoritmo mejora (hazlo tras un login correcto)
if (password_needs_rehash($hash, PASSWORD_DEFAULT)) {
    $nuevoHash = password_hash($password, PASSWORD_DEFAULT);
}
```

- **Nunca** guardes contraseñas en texto ni con `md5` o `sha1`.
- La columna del hash debe ser `VARCHAR(255)` (el tamaño puede crecer).
- `password_hash` añade la sal automáticamente.
- No limites la longitud máxima por debajo de unos 64 caracteres.
- `PASSWORD_ARGON2ID` es otra opción si tu PHP lo incluye.

### 5. Autorización (permisos)

Estar logueado **no** significa poder hacerlo todo. Comprueba permisos en el servidor, **en cada acción**.

```php
// ❌ Cualquier usuario logueado puede ver cualquier pedido cambiando el id
$stmt = $pdo->prepare("SELECT * FROM pedidos WHERE id = :id");

// ✅ Solo ve los suyos
$stmt = $pdo->prepare("SELECT * FROM pedidos WHERE id = :id AND usuario_id = :uid");
$stmt->execute(["id" => $id, "uid" => $_SESSION["usuario_id"]]);
```

Este fallo se llama **IDOR** (referencia directa a objetos sin comprobar permisos) y es de los más comunes.

### 6. Asignación masiva (mass assignment)

```php
// ❌ El usuario puede añadir campos que no esperabas (por ejemplo "rol" = "admin")
foreach ($_POST as $campo => $valor) { /* UPDATE ... SET $campo = ... */ }

// ✅ Solo los campos permitidos
$permitidos = ["nombre", "email"];
$datos = array_intersect_key($_POST, array_flip($permitidos));
```

---

## 4. Elementos / características

### Validar entradas

- Comprueba **tipo, longitud, rango y formato**.
- Usa **listas permitidas** (whitelist) mejor que listas prohibidas.

```php
$rol = $_POST["rol"] ?? "";

if (!in_array($rol, ["usuario", "editor"], true)) {
    exit("Rol no válido");
}
```

### Sesiones seguras

- `session_regenerate_id(true)` tras el login.
- Cookies con `secure`, `httponly` y `samesite`.
- `session.use_strict_mode = 1` (no acepta IDs de sesión inventados).
- Cierra bien la sesión al hacer logout (nota [[PHP/11 - Sesiones y cookies|11]]).
- No guardes datos sensibles de más en la sesión.

### Subidas de ficheros

- Valida el tipo real con `finfo`, no con `name` ni `type`.
- Limita el tamaño y genera un nombre nuevo.
- No permitas guardar ficheros ejecutables en carpetas públicas.
- Detalle completo en la nota [[PHP/12 - Ficheros|12]].

### Path traversal (rutas manipuladas)

```php
// ❌ Vulnerable: ?f=../../etc/passwd
readfile("documentos/" . $_GET["f"]);

// ✅ Seguro
$base = realpath(__DIR__ . "/documentos");
$ruta = realpath($base . "/" . ($_GET["f"] ?? ""));

if ($ruta === false || !str_starts_with($ruta, $base . DIRECTORY_SEPARATOR)) {
    exit("No permitido");
}
```

### Inclusión de ficheros (LFI / RFI)

```php
// ❌ Vulnerable
include $_GET["pagina"] . ".php";

// ✅ Lista permitida
$paginas = ["inicio", "contacto", "acerca"];
$pagina  = in_array($_GET["pagina"] ?? "", $paginas, true) ? $_GET["pagina"] : "inicio";
include __DIR__ . "/paginas/$pagina.php";
```

Mantén `allow_url_include` desactivado (lo está por defecto).

### Deserialización insegura

`unserialize()` con datos del usuario puede **crear objetos y ejecutar código** de tus clases.

```php
// ❌ Nunca con datos externos
$datos = unserialize($_COOKIE["datos"]);

// ✅ Usa JSON
$datos = json_decode($_COOKIE["datos"] ?? "{}", true);
```

Si no hay más remedio: `unserialize($texto, ["allowed_classes" => false])`.

### Ejecución de comandos

Evita `exec`, `shell_exec`, `system` y `passthru` con datos del usuario. Si no hay otra opción:

```php
$arg = escapeshellarg($entrada);
```

Casi siempre hay una función de PHP que hace lo mismo sin lanzar un comando.

### Redirecciones y cabeceras

- No redirijas a una URL que venga del usuario sin comprobar que es de tu sitio (*open redirect*): usa una lista permitida de destinos.
- Con `header("Location: ...")` y datos del usuario, comprueba que no contengan saltos de línea.

### SSRF (servidor que hace peticiones por el usuario)

Si tu servidor descarga una URL que escribe el usuario (cURL, `file_get_contents`), podría apuntar a tu red interna (`http://localhost`, `http://192.168...`). Permite solo `https` y dominios de una lista, o bloquea direcciones internas.

### Aleatoriedad y comparaciones seguras

```php
$token = bin2hex(random_bytes(16));          // ✅
// ❌ No uses rand(), mt_rand() ni uniqid() para tokens o contraseñas

hash_equals($tokenGuardado, $tokenRecibido);   // compara en tiempo constante
```

### Recuperar contraseñas

- Genera un token con `random_bytes`.
- Guarda **su hash** en la base de datos, con fecha de caducidad (por ejemplo 1 hora).
- Que solo se pueda usar **una vez**.
- Responde igual si el email existe o no ("si existe, te hemos enviado un correo").

### Cifrar datos

No inventes criptografía. PHP incluye **libsodium** (`sodium_crypto_secretbox` y funciones relacionadas) para cifrar datos de forma segura. Las claves van **fuera** del código.

### Errores, logs y configuración

- En producción: `display_errors = 0` y errores guardados en un log.
- No registres contraseñas, tokens ni datos personales completos.
- `expose_php = Off` para no anunciar la versión.
- Credenciales fuera del código (`.env` o variables de entorno), y `.env` y `vendor/` **fuera** de la carpeta pública.
- La carpeta pública del servidor debe ser `public/`, no la raíz del proyecto.
- No dejes `phpinfo()` accesible.

### Cabeceras de seguridad

```php
header("X-Content-Type-Options: nosniff");
header("Referrer-Policy: strict-origin-when-cross-origin");
header("Content-Security-Policy: default-src 'self'; frame-ancestors 'none'");
header("Strict-Transport-Security: max-age=31536000; includeSubDomains");   // solo si usas HTTPS
```

- `frame-ancestors 'none'` (o `X-Frame-Options: DENY`) evita el *clickjacking*.
- Una política CSP estricta limita el daño de un XSS.

### Fuerza bruta

Limita los intentos de login por usuario e IP: bloquea o retrasa tras varios fallos, y registra los intentos.

### Dependencias

- `composer audit` revisa vulnerabilidades conocidas (nota [[PHP/17 - Composer|17]]).
- Mantén PHP y las librerías actualizados y elimina las que no uses.

---

## 5. Ejemplos prácticos

### Ejemplo básico: mostrar texto del usuario

```php
echo "<p>" . e($comentario) . "</p>";   // función e() definida arriba
```

### Ejemplo habitual: login seguro (resumen)

```php
session_start();

if ($_SERVER["REQUEST_METHOD"] === "POST") {
    if (!hash_equals($_SESSION["csrf"] ?? "", $_POST["csrf"] ?? "")) {
        http_response_code(403);
        exit;
    }

    $stmt = $pdo->prepare("SELECT id, password_hash FROM usuarios WHERE email = :email");
    $stmt->execute(["email" => $_POST["email"] ?? ""]);
    $u = $stmt->fetch();

    if ($u && password_verify($_POST["password"] ?? "", $u["password_hash"])) {
        session_regenerate_id(true);
        $_SESSION["usuario_id"] = $u["id"];
        header("Location: panel.php");
        exit;
    }

    $error = "Usuario o contraseña incorrectos";   // mismo mensaje en ambos casos
}
```

---

## 6. Buenas prácticas

- **Valida al entrar, escapa al salir.**
- Consultas preparadas **siempre**.
- `password_hash` y `password_verify` para contraseñas.
- Token CSRF en los formularios que modifican datos.
- Comprueba permisos en el servidor en **cada** acción.
- Listas permitidas para rutas, rutas incluidas, campos aceptados y tipos de fichero.
- Principio de **mínimo privilegio**: usuario de base de datos, carpetas y ficheros con los permisos justos.
- Errores ocultos al usuario, guardados en un log.
- HTTPS, cookies seguras y dependencias actualizadas.
- Haz copias de seguridad.

### Checklist antes de publicar

- [ ] Todas las consultas con datos externos son preparadas
- [ ] Todo lo que se muestra va escapado
- [ ] Formularios que cambian datos con token CSRF
- [ ] Contraseñas con `password_hash`
- [ ] `session_regenerate_id(true)` tras el login y cookies seguras
- [ ] Permisos comprobados en cada acción
- [ ] Subidas de ficheros validadas y renombradas
- [ ] Rutas y `include` dinámicos con lista permitida
- [ ] `display_errors` desactivado y log activado
- [ ] `.env`, `vendor/` y código fuera de la carpeta pública
- [ ] HTTPS activo y cabeceras de seguridad
- [ ] `composer audit` sin avisos graves

---

## 7. Diferencias importantes

| Comparación | Diferencia |
|---|---|
| Validar vs escapar | Validar al **recibir** el dato; escapar al **mostrarlo** o usarlo |
| Autenticación vs autorización | Autenticar: ¿quién eres?; autorizar: ¿qué puedes hacer? |
| Hash vs cifrado | El hash no se revierte; el cifrado sí (con clave) |
| `md5` / `sha1` vs `password_hash` | Los primeros son rápidos y fáciles de atacar; `password_hash` está diseñado para contraseñas |
| `==` vs `hash_equals` | `hash_equals` compara en tiempo constante (evita ataques por tiempo) |
| `rand` / `uniqid` vs `random_bytes` | Los dos primeros son predecibles |

---

## 8. Casos especiales

### Datos en JSON para JavaScript dentro de HTML

```php
<script>
  const usuario = <?= json_encode($usuario, JSON_HEX_TAG | JSON_HEX_AMP | JSON_HEX_APOS | JSON_HEX_QUOT) ?>;
</script>
```

### Cookies y `SameSite`

`Lax` es un buen valor por defecto; `Strict` es más estricto; `None` solo con `secure` y cuando de verdad lo necesites.

### Mensajes de error en login

Mismo mensaje y mismo tiempo de respuesta tanto si el usuario no existe como si la contraseña es incorrecta.

### Cuando todo falla

Ten un plan: si sospechas que se han filtrado datos, cambia credenciales y claves, invalida sesiones y avisa a los afectados.

---

## 9. Resumen

- No te fíes de ningún dato externo.
- SQL → consultas preparadas. HTML → `htmlspecialchars`. Formularios → token CSRF.
- Contraseñas → `password_hash` y `password_verify`.
- Permisos → comprobar en cada acción (cuidado con IDOR y asignación masiva).
- Ficheros y rutas → `realpath`, `basename` y listas permitidas.
- `unserialize` con datos externos, nunca; usa JSON.
- Sesiones → regenerar ID, cookies seguras. Aleatorios → `random_bytes`.
- Errores ocultos, credenciales fuera del código, HTTPS y dependencias al día.