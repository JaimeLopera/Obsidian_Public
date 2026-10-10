# 12 - Ficheros

> [!info] ¿Qué es?
> PHP puede **leer, crear, modificar y borrar** ficheros y carpetas del servidor, **recibir** ficheros que sube el usuario y **ofrecer descargas**. Es una de las zonas donde más cuidado hay que tener con la seguridad.

---

## 1. Antes de empezar

Debes conocer strings (nota [[PHP/08 - Strings|08]]), arrays (nota [[PHP/07 - Arrays|07]]) y formularios (nota [[PHP/10 - Formularios|10]]).

---

## 2. Concepto fundamental

Hay dos formas de trabajar con ficheros:

- **Funciones rápidas**: leen o escriben el fichero entero de una vez (`file_get_contents`, `file_put_contents`). Son cómodas, pero cargan todo en memoria.
- **Manejadores (handles)**: abres el fichero, lees o escribes por partes y lo cierras (`fopen`, `fgets`, `fwrite`, `fclose`). Sirven para ficheros grandes.

Casi todas las funciones de ficheros devuelven **`false`** si fallan (y lanzan un aviso). Comprueba siempre el resultado con `=== false`.

---

## 3. Sintaxis / estructura

### Leer y escribir de una vez

```php
$ruta = __DIR__ . "/datos.txt";

$contenido = file_get_contents($ruta);                       // false si falla

file_put_contents($ruta, "Hola\n");                          // sobrescribe
file_put_contents($ruta, "Otra línea\n", FILE_APPEND | LOCK_EX);   // añade al final con bloqueo
```

`file_put_contents` devuelve los bytes escritos o `false`. `LOCK_EX` evita que dos peticiones escriban a la vez.

### Leer línea a línea (ficheros grandes)

```php
$f = fopen($ruta, "r");

if ($f === false) {
    throw new RuntimeException("No se pudo abrir el fichero");
}

while (($linea = fgets($f)) !== false) {
    echo $linea;
}

fclose($f);
```

> [!warning] Cuidado con `feof`
> `while (!feof($f)) { $l = fgets($f); }` puede dar una vuelta de más con `false`. Es mejor el patrón de arriba: `while (($linea = fgets($f)) !== false)`.

### Escribir con manejador

```php
$f = fopen($ruta, "a");
fwrite($f, "Nueva línea\n");
fclose($f);
```

### Modos de `fopen`

| Modo | Significado |
|---|---|
| `r` | Solo lectura (el fichero debe existir) |
| `r+` | Lectura y escritura (debe existir) |
| `w` | Escritura. **Vacía** el fichero o lo crea |
| `w+` | Lectura y escritura. Vacía o crea |
| `a` | Añadir al final. Lo crea si no existe |
| `a+` | Leer y añadir al final |
| `x` | Crea el fichero. **Falla si ya existe** |
| `c` / `c+` | Abre o crea **sin vaciar** (ideal con `flock`) |

Añadir `b` (por ejemplo `rb`) abre en modo binario; es recomendable para ficheros no de texto.

---

## 4. Elementos / características

### Leer de otras formas

| Función | Qué hace |
|---|---|
| `fgets($f)` | Una línea |
| `fgetc($f)` | Un carácter |
| `fread($f, $bytes)` | Un número de bytes |
| `stream_get_contents($f)` | Todo lo que queda |
| `file($ruta, FILE_IGNORE_NEW_LINES \| FILE_SKIP_EMPTY_LINES)` | Todo el fichero como array de líneas |
| `readfile($ruta)` | Lo envía directamente a la salida |
| `rewind($f)` / `fseek($f, $pos)` / `ftell($f)` | Moverse dentro del fichero |
| `ftruncate($f, 0)` | Vaciar o recortar |

### Bloquear un fichero (`flock`)

Cuando varias peticiones pueden escribir a la vez, bloquea el fichero:

```php
$f = fopen($ruta, "c+");              // no lo vacía al abrir

if (flock($f, LOCK_EX)) {             // bloqueo exclusivo (espera su turno)
    $contador = (int) stream_get_contents($f);
    $contador++;

    ftruncate($f, 0);
    rewind($f);
    fwrite($f, (string) $contador);
    fflush($f);

    flock($f, LOCK_UN);               // liberar
}

fclose($f);
```

`LOCK_SH` es un bloqueo compartido (varios pueden leer).

### Comprobaciones

| Función | Qué hace |
|---|---|
| `file_exists($ruta)` | ¿Existe fichero o carpeta? |
| `is_file`, `is_dir` | ¿Es un fichero? ¿Es una carpeta? |
| `is_readable`, `is_writable` | ¿Se puede leer / escribir? |
| `filesize($ruta)` | Tamaño en bytes |
| `filemtime($ruta)` | Fecha de modificación (timestamp) |

### Gestionar ficheros y carpetas

| Función | Qué hace |
|---|---|
| `unlink($ruta)` | Borra un fichero |
| `rename($a, $b)` | Renombra o mueve |
| `copy($a, $b)` | Copia |
| `touch($ruta)` | Crea vacío o actualiza la fecha |
| `mkdir($ruta, 0755, true)` | Crea carpeta (`true` = crea también las intermedias) |
| `rmdir($ruta)` | Borra una carpeta **vacía** |
| `scandir($ruta)` | Lista el contenido (incluye `.` y `..`) |
| `glob(__DIR__ . "/*.txt")` | Busca por patrón |
| `chmod($ruta, 0644)` | Cambia permisos |
| `tempnam(sys_get_temp_dir(), "pre_")` | Crea un fichero temporal con nombre único |

```php
$archivos = array_diff(scandir(__DIR__), [".", ".."]);   // quita . y ..
```

### Recorrer carpetas con subcarpetas

```php
$it = new RecursiveIteratorIterator(
    new RecursiveDirectoryIterator(__DIR__ . "/docs", FilesystemIterator::SKIP_DOTS)
);

foreach ($it as $fichero) {
    if ($fichero->isFile()) {
        echo $fichero->getPathname(), "\n";
    }
}
```

### Información de rutas

```php
$ruta = "/var/www/fotos/gato.jpg";

basename($ruta);                       // gato.jpg
dirname($ruta);                        // /var/www/fotos
pathinfo($ruta, PATHINFO_EXTENSION);   // jpg
pathinfo($ruta, PATHINFO_FILENAME);    // gato
realpath($ruta);                       // ruta absoluta real (false si no existe)
```

### CSV

```php
// Leer
$f = fopen("usuarios.csv", "r");
$cabecera = fgetcsv($f);
while (($fila = fgetcsv($f)) !== false) {
    $registro = array_combine($cabecera, $fila);     // ["nombre" => "Ana", "edad" => "20"]
}
fclose($f);

// Escribir
$f = fopen("salida.csv", "w");
fputcsv($f, ["nombre", "edad"]);
fputcsv($f, ["Ana", 20]);
fclose($f);
```

Para el **Excel en español**, que usa `;` como separador y espera UTF-8 con BOM:

```php
fwrite($f, "\xEF\xBB\xBF");                       // BOM al principio
fputcsv($f, ["nombre", "edad"], ";");
$fila = fgetcsv($f, null, ";");                   // al leer: longitud null y separador ;
```

### JSON en ficheros

```php
file_put_contents("datos.json", json_encode($datos, JSON_PRETTY_PRINT | JSON_UNESCAPED_UNICODE));
$leidos = json_decode(file_get_contents("datos.json"), true, 512, JSON_THROW_ON_ERROR);
```

Todo sobre JSON está en la nota [[PHP/13 - JSON y APIs|13]].

### Leer ficheros con `SplFileObject`

```php
$fichero = new SplFileObject($ruta);
$fichero->setFlags(SplFileObject::DROP_NEW_LINE | SplFileObject::SKIP_EMPTY | SplFileObject::READ_AHEAD);

foreach ($fichero as $numero => $linea) {
    echo $numero + 1, ": ", $linea, "\n";
}
```

### Subida de ficheros

**Formulario** (hace falta `method="post"` y `enctype`):

```html
<form method="post" enctype="multipart/form-data">
  <input type="file" name="foto">
  <button>Subir</button>
</form>
```

**PHP:**

```php
$foto = $_FILES["foto"] ?? null;

if ($foto === null || $foto["error"] !== UPLOAD_ERR_OK) {
    exit("No se pudo subir el fichero");
}

if ($foto["size"] > 2 * 1024 * 1024) {
    exit("Máximo 2 MB");
}

// Tipo real del contenido (no el que dice el navegador)
$finfo = new finfo(FILEINFO_MIME_TYPE);
$tipo  = $finfo->file($foto["tmp_name"]);

$permitidos = ["image/jpeg" => "jpg", "image/png" => "png", "image/webp" => "webp"];

if (!isset($permitidos[$tipo])) {
    exit("Tipo de fichero no permitido");
}

// Nombre nuevo y extensión según el tipo real
$nombre = bin2hex(random_bytes(8)) . "." . $permitidos[$tipo];

if (!move_uploaded_file($foto["tmp_name"], __DIR__ . "/uploads/" . $nombre)) {
    exit("No se pudo guardar el fichero");
}
```

Qué trae `$_FILES["foto"]`:

| Clave | Qué es |
|---|---|
| `name` | Nombre original (**no te fíes**) |
| `type` | Tipo que dice el navegador (**no te fíes**) |
| `tmp_name` | Ruta temporal en el servidor |
| `size` | Tamaño en bytes |
| `error` | Código de error |

Códigos de error más habituales:

| Constante | Significado |
|---|---|
| `UPLOAD_ERR_OK` | Todo correcto |
| `UPLOAD_ERR_INI_SIZE` | Supera `upload_max_filesize` de `php.ini` |
| `UPLOAD_ERR_FORM_SIZE` | Supera `MAX_FILE_SIZE` del formulario |
| `UPLOAD_ERR_PARTIAL` | Se subió solo una parte |
| `UPLOAD_ERR_NO_FILE` | No se envió ningún fichero |
| `UPLOAD_ERR_NO_TMP_DIR` | Falta la carpeta temporal |
| `UPLOAD_ERR_CANT_WRITE` | No se pudo escribir en disco |

**Varios ficheros** (`<input type="file" name="fotos[]" multiple>`): llegan como `$_FILES["fotos"]["name"][0]`, `$_FILES["fotos"]["tmp_name"][0]`, etc.

### Ofrecer una descarga

```php
header("Content-Type: application/pdf");
header('Content-Disposition: attachment; filename="informe.pdf"');
header("Content-Length: " . filesize($ruta));
readfile($ruta);
exit;
```

---

## 5. Ejemplos prácticos

### Ejemplo básico: guardar un log

```php
$linea = date("Y-m-d H:i:s") . " - Usuario entró\n";
file_put_contents(__DIR__ . "/log.txt", $linea, FILE_APPEND | LOCK_EX);
```

### Ejemplo habitual: descargar un fichero que elige el usuario (sin riesgo)

```php
$base = realpath(__DIR__ . "/documentos");
$ruta = realpath($base . "/" . ($_GET["f"] ?? ""));

// realpath devuelve false si no existe; además comprobamos que sigue dentro de $base
if ($ruta === false || !str_starts_with($ruta, $base . DIRECTORY_SEPARATOR) || !is_file($ruta)) {
    http_response_code(404);
    exit("No encontrado");
}

header("Content-Type: application/octet-stream");
header('Content-Disposition: attachment; filename="' . basename($ruta) . '"');
header("Content-Length: " . filesize($ruta));
readfile($ruta);
exit;
```

---

## 6. Buenas prácticas

- Construye las rutas con `__DIR__`.
- Comprueba que el fichero existe antes de leerlo y comprueba el resultado (`=== false`).
- Cierra siempre con `fclose`.
- Usa `LOCK_EX` o `flock` si puede haber varias escrituras a la vez.
- Para ficheros grandes lee **por líneas**, no con `file_get_contents`.
- No uses `@` para ocultar errores de ficheros.
- Guarda las subidas **fuera de la carpeta pública** si puedes.
- Nunca construyas una ruta directamente con un dato del usuario (ver más abajo).

---

## 7. Diferencias importantes

| Comparación | Diferencia |
|---|---|
| `w` vs `a` vs `c` | `w` vacía; `a` añade al final; `c` abre sin vaciar |
| `file_get_contents` vs `fopen + fgets` | El primero carga todo; el segundo lee por partes |
| `include` vs `file_get_contents` | `include` **ejecuta** el PHP; `file_get_contents` solo lee el texto |
| `basename` vs `realpath` | `basename` solo quita las carpetas; `realpath` resuelve la ruta real y comprueba que existe |
| `is_file` vs `file_exists` | `file_exists` también da `true` para carpetas |

---

## 8. Casos especiales

### Path traversal (rutas manipuladas)

Si el usuario controla parte de la ruta, puede escribir `../../etc/passwd` y salir de tu carpeta.

```php
// ❌ Vulnerable
readfile("documentos/" . $_GET["f"]);
```

Protégete con:

1. `basename($nombre)` si solo quieres un nombre de fichero (sin carpetas).
2. `realpath` y comprobar que el resultado empieza por tu carpeta base (como en el ejemplo habitual).
3. Mejor aún: usar una **lista permitida** o identificadores (`?id=5`) que busques en la base de datos.

### Subidas: reglas de seguridad

- No confíes en `name` ni `type`: usa `finfo` y una lista de tipos permitidos.
- Genera un **nombre nuevo** y decide la extensión según el tipo real.
- Limita el tamaño (y configura `upload_max_filesize` y `post_max_size`).
- Evita que se ejecuten ficheros en la carpeta de subidas (nada de `.php`).
- Para imágenes, puedes comprobar también con `getimagesize()`: devuelve `false` si no es una imagen.

### Límites de `php.ini`

`upload_max_filesize`, `post_max_size`, `max_file_uploads` y `memory_limit` limitan lo que se puede subir y procesar.

### Rutas relativas

`"datos.txt"` depende del directorio desde el que se ejecuta el script, y puede fallar. Usa `__DIR__`.

### Permisos

En Linux, el usuario del servidor web (por ejemplo `www-data`) debe poder escribir en la carpeta donde guardas. Usa permisos mínimos (`0755` en carpetas y `0644` en ficheros).

### Codificación

Guarda los ficheros de texto en **UTF-8**. Si te llegan en otra codificación, conviértelos con `mb_convert_encoding($texto, "UTF-8", "ISO-8859-1")`.

### Ficheros temporales

`tmpfile()` crea un fichero temporal que se borra al cerrarlo; `tempnam()` crea uno con nombre único que debes borrar tú.

---

## 9. Resumen

- `file_get_contents` y `file_put_contents` para ficheros pequeños; `fopen`, `fgets`, `fwrite` y `fclose` para ficheros grandes.
- Modos: `r` lee, `w` vacía, `a` añade, `x` crea nuevo, `c` abre sin vaciar.
- Casi todas las funciones devuelven `false` si fallan: compruébalo con `===`.
- `flock` y `LOCK_EX` evitan escrituras simultáneas.
- CSV con `fgetcsv` y `fputcsv`; JSON con `json_encode` y `json_decode`.
- Subidas: `$_FILES`, comprobar `error`, tamaño y tipo real con `finfo`, renombrar y `move_uploaded_file`.
- Rutas con datos del usuario: `basename`, `realpath` y listas permitidas para evitar el path traversal.