# 11 - Sesiones y cookies

> [!info] ¿Qué es?
> HTTP **no recuerda** al usuario entre una petición y otra. Las **cookies** y las **sesiones** son las herramientas para "recordarlo": mantener un login, un carrito, preferencias, etc.

---

## 1. Antes de empezar

Conviene entender cómo funciona HTTP ([[Conceptos generales/01 - HTTP|HTTP]]) y cómo se reciben datos en PHP (nota [[10 - Formularios|09]]).

---

## 2. Concepto fundamental

| | Cookie | Sesión |
|---|---|---|
| Dónde se guardan los datos | En el **navegador** del usuario | En el **servidor** |
| Qué recibe el navegador | Los datos | Solo un **identificador** (una cookie con el ID de sesión) |
| ¿Puede el usuario modificarlo? | Sí | No (solo ve el ID) |
| Úsalo para | Preferencias no sensibles | Login, carrito, datos privados |

> [!warning] Importante
> Nunca guardes datos sensibles (contraseñas, roles) en una cookie normal. El usuario puede cambiarla.

---

## 3. Sintaxis / estructura

### Cookies

```php
// Crear (antes de enviar cualquier salida)
setcookie("tema", "oscuro", [
    "expires"  => time() + 60 * 60 * 24 * 30,  // 30 días
    "path"     => "/",
    "secure"   => true,       // solo por HTTPS
    "httponly" => true,       // JavaScript no puede leerla
    "samesite" => "Lax",
]);

// Leer (disponible en la siguiente petición)
$tema = $_COOKIE["tema"] ?? "claro";

// Borrar: con fecha en el pasado
setcookie("tema", "", time() - 3600, "/");
```

### Sesiones

```php
session_start();   // al principio, antes de cualquier salida

$_SESSION["usuario_id"] = 5;          // guardar
$id = $_SESSION["usuario_id"] ?? null; // leer

unset($_SESSION["usuario_id"]);        // borrar un dato
```

`session_start()` debe llamarse **en cada página** que use la sesión.

---

## 4. Elementos / características

### Parámetros de una cookie

| Opción | Para qué sirve |
|---|---|
| `expires` | Cuándo caduca. Sin ella, se borra al cerrar el navegador |
| `path` | Rutas donde se envía. `"/"` = todo el sitio |
| `domain` | Dominio donde vale |
| `secure` | Solo se envía por HTTPS |
| `httponly` | Impide leerla con JavaScript (protege de XSS) |
| `samesite` | Controla el envío entre sitios (`Lax`, `Strict`, `None`) |

### Ciclo de vida de una sesión

1. `session_start()` crea una sesión y manda al navegador la cookie `PHPSESSID`.
2. En cada petición, el navegador devuelve ese ID.
3. PHP carga `$_SESSION` con los datos de ese ID.
4. La sesión caduca por inactividad o al destruirla.

### Cerrar sesión (logout)

```php
session_start();
$_SESSION = [];                 // vacía los datos

if (ini_get("session.use_cookies")) {
    $p = session_get_cookie_params();
    setcookie(session_name(), "", time() - 42000,
        $p["path"], $p["domain"], $p["secure"], $p["httponly"]);
}

session_destroy();
header("Location: login.php");
exit;
```

### Regenerar el ID de sesión

```php
session_regenerate_id(true);
```

Hazlo **justo tras el login** para evitar el ataque de *session fixation*.

### Mensajes "flash" (se muestran una vez)

```php
// Al guardar:
$_SESSION["flash"] = "Datos guardados";

// Al mostrar:
if (isset($_SESSION["flash"])) {
    echo htmlspecialchars($_SESSION["flash"]);
    unset($_SESSION["flash"]);
}
```

---

## 5. Ejemplos prácticos

### Ejemplo básico: contador de visitas

```php
session_start();
$_SESSION["visitas"] = ($_SESSION["visitas"] ?? 0) + 1;
echo "Has visitado esta página {$_SESSION["visitas"]} veces";
```

### Ejemplo habitual: login

```php
// login.php
session_start();

$usuario = buscarUsuarioPorEmail($pdo, $_POST["email"] ?? "");

if ($usuario && password_verify($_POST["password"] ?? "", $usuario["password_hash"])) {
    session_regenerate_id(true);
    $_SESSION["usuario_id"] = $usuario["id"];
    header("Location: panel.php");
    exit;
}

$error = "Usuario o contraseña incorrectos";
```

```php
// panel.php — página protegida
session_start();

if (!isset($_SESSION["usuario_id"])) {
    header("Location: login.php");
    exit;
}
```

---

## 6. Buenas prácticas

- Llama a `session_start()` **antes de cualquier salida** (antes del HTML).
- Regenera el ID de sesión al iniciar sesión.
- Guarda en la sesión lo mínimo (normalmente el ID del usuario) y consulta el resto en la base de datos.
- Usa `secure`, `httponly` y `samesite` en las cookies importantes.
- Destruye la sesión por completo al cerrar sesión.
- Comprueba permisos **en cada página protegida**, no solo al entrar.
- Guarda contraseñas con `password_hash` (nota 16).

---

## 7. Diferencias importantes

| Comparación | Diferencia |
|---|---|
| Cookie vs sesión | La cookie guarda el dato en el navegador; la sesión, en el servidor |
| `session_destroy()` vs `unset($_SESSION[...])` | `destroy` elimina toda la sesión; `unset` solo un dato |
| Cookie de sesión vs persistente | Sin `expires` se borra al cerrar el navegador |

---

## 8. Casos especiales

### Error "headers already sent"

```
Warning: Cannot modify header information - headers already sent
```

Pasa si hay **cualquier salida** (incluso un espacio o línea en blanco antes de `<?php`) antes de `session_start()`, `setcookie()` o `header()`.

### "Recordarme" (login persistente)

No guardes el ID de usuario ni la contraseña en una cookie. Crea un **token aleatorio**, guarda su hash en la base de datos y pon el token en la cookie.

### Configuración de la sesión

```php
ini_set("session.cookie_httponly", "1");
ini_set("session.cookie_secure", "1");
ini_set("session.use_strict_mode", "1");
```

Mejor definirlo en `php.ini` o antes de `session_start()`.

### Varios servidores

Si tu aplicación usa varios servidores, las sesiones deben compartirse (Redis, base de datos).

---

## 9. Resumen

- Las cookies guardan datos en el navegador; las sesiones, en el servidor.
- Sesión: `session_start()` y `$_SESSION`.
- Cookie: `setcookie()` y `$_COOKIE`.
- Regenera el ID tras el login y destruye todo en el logout.
- Protege cookies con `secure`, `httponly` y `samesite`.
- No pongas salida antes de `session_start()` o `setcookie()`.