# 01 - Fundamentos

> [!info] ¿Qué es?
> PHP es un lenguaje que se ejecuta **en el servidor**. El navegador pide una página, el servidor ejecuta el código PHP y devuelve el resultado (normalmente HTML). El usuario nunca ve el código PHP.

---

## 1. Antes de empezar

Necesitas:

- **PHP instalado** (comprueba con `php -v` en la terminal).
- **Un servidor**: el integrado de PHP, XAMPP o Docker.
- **Un editor**: VS Code, por ejemplo.

Para probar sin instalar nada más, usa el servidor integrado dentro de la carpeta de tu proyecto:

```bash
php -S localhost:8000
```

Después abre `http://localhost:8000/index.php` en el navegador.

---

## 2. Concepto fundamental

Cuando el servidor recibe una petición de un fichero `.php`:

1. Lee el fichero.
2. Ejecuta lo que está dentro de `<?php ... ?>`.
3. Sustituye ese código por lo que haya **mostrado** (con `echo`).
4. Envía el resultado final al navegador.

Todo lo que está **fuera** de las etiquetas PHP se envía tal cual (HTML, texto, etc.).

---

## 3. Sintaxis / estructura

### Etiquetas

```php
<?php
echo "Hola mundo";
```

- En ficheros que **solo** tienen PHP, **no se cierra** la etiqueta `?>` (evita espacios o saltos de línea accidentales).
- Si mezclas con HTML, sí se abre y se cierra.

### Mezclar PHP con HTML

```php
<h1><?php echo "Mi página"; ?></h1>
<p>Hoy es <?= date("d/m/Y") ?></p>
```

`<?= ... ?>` es un atajo de `<?php echo ... ?>`.

### Reglas básicas

- Cada instrucción termina con `;`.
- Las variables empiezan por `$`.
- Los nombres de **variables** distinguen mayúsculas y minúsculas (`$a` ≠ `$A`).
- Los nombres de **funciones y palabras clave** (`echo`, `if`...) **no** las distinguen.

### Comentarios

```php
// Comentario de una línea
# Otro comentario de una línea (menos usado)
/* Comentario
   de varias líneas */
/** Comentario de documentación (DocBlock) */
```

---

## 4. Elementos / características

### Mostrar información

| Instrucción | Qué hace |
|---|---|
| `echo` | Muestra uno o varios valores. Es la más usada |
| `print` | Muestra un valor y devuelve `1` |
| `print_r($x)` | Muestra arrays y objetos de forma legible |
| `var_dump($x)` | Muestra el valor **con su tipo**. Ideal para depurar |

```php
echo "Hola", " ", "mundo";
var_dump(42);          // int(42)
print_r([1, 2, 3]);
```

### Incluir otros ficheros

| Instrucción | Si el fichero no existe |
|---|---|
| `include` | Aviso, el script **continúa** |
| `require` | Error fatal, el script **se detiene** |
| `include_once` / `require_once` | Igual, pero **solo lo carga una vez** |

```php
require_once __DIR__ . "/config.php";
```

`__DIR__` es la carpeta del fichero actual. Úsala siempre para evitar rutas rotas.

### Ejecutar PHP desde la terminal

```bash
php script.php
```

Útil para scripts, tareas automáticas y pruebas rápidas.
### Constantes mágicas

Cambian según el sitio del código donde las escribas.

| Constante | Valor |
|---|---|
| `__FILE__` | Ruta completa del fichero actual |
| `__DIR__` | Carpeta del fichero actual |
| `__LINE__` | Número de línea actual |
| `__FUNCTION__` | Nombre de la función actual |
| `__CLASS__` | Nombre de la clase actual |
| `__METHOD__` | Clase y método actuales |
| `__NAMESPACE__` | Namespace actual |
| `Clase::class` | Nombre completo de una clase (con su namespace) |

```php
echo "Error en " . __FILE__ . " línea " . __LINE__;
```

### Constantes predefinidas útiles

| Constante | Valor |
|---|---|
| `PHP_VERSION` | Versión de PHP en uso |
| `PHP_EOL` | Salto de línea según el sistema |
| `PHP_INT_MAX` | Mayor entero posible |
| `DIRECTORY_SEPARATOR` | `/` en Linux y macOS, `\` en Windows |
| `PHP_OS` | Sistema operativo |

---

## 5. Ejemplos prácticos

### Ejemplo básico

```php
<?php
$nombre = "Ana";
echo "Hola, $nombre";
```

### Ejemplo habitual: cabecera y pie reutilizables

```php
<?php require __DIR__ . "/partes/cabecera.php"; ?>

<main>
  <h1>Inicio</h1>
</main>

<?php require __DIR__ . "/partes/pie.php"; ?>
```

---

## 6. Buenas prácticas

- Usa `require_once` para ficheros imprescindibles (configuración, funciones).
- No cierres `?>` en ficheros solo PHP.
- Usa `__DIR__` en las rutas de `include` y `require`.
- Activa los errores **solo en desarrollo**.
- Añade `declare(strict_types=1);` al inicio de tus ficheros (ver nota 17).

---

## 7. Diferencias importantes

| Concepto | PHP | JavaScript en navegador |
|---|---|---|
| Dónde se ejecuta | Servidor | Navegador |
| ¿El usuario ve el código? | No | Sí |
| Acceso a base de datos | Directo | A través de una API |
| Variables | Empiezan por `$` | Sin `$` |
| Concatenar texto | `.` | `+` |

---

## 8. Casos especiales

### Mostrar errores en desarrollo

```php
ini_set("display_errors", "1");
error_reporting(E_ALL);
```

En **producción** se desactivan y los errores se guardan en un log (ver nota 13).

### Ver la configuración de PHP

```php
phpinfo();
```

Muestra versión, extensiones y ajustes. No la dejes accesible en un servidor real.

### Etiquetas cortas `<?`

Están desaconsejadas. Usa siempre `<?php`.

---

## 9. Resumen

- PHP se ejecuta en el servidor y devuelve HTML al navegador.
- El código va dentro de `<?php ... ?>`; en ficheros solo PHP no se cierra.
- `echo` muestra datos; `var_dump` ayuda a depurar.
- `require_once` carga ficheros necesarios una sola vez.
- Usa `__DIR__` para las rutas.
- Muestra errores solo en desarrollo.