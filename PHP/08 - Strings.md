# 08 - Strings

> [!info] ¿Qué es?
> Un **string** es un texto: una secuencia de caracteres. PHP incluye muchísimas funciones para trabajar con ellos.

---

## 1. Antes de empezar

Conviene conocer variables y operadores, sobre todo el punto `.` (notas [[PHP/02 - Variables y tipos|02]] y [[PHP/03 - Operadores|03]]).

---

## 2. Concepto fundamental

Un string se crea con comillas. Dependiendo de las comillas, PHP **interpreta** el contenido (comillas dobles) o lo toma **literalmente** (comillas simples).

---

## 3. Sintaxis / estructura

### Comillas simples y dobles

```php
$nombre = "Ana";

echo 'Hola $nombre\n';   // Hola $nombre\n   (literal)
echo "Hola $nombre\n";   // Hola Ana + salto de línea
```

| | Simples `'...'` | Dobles `"..."` |
|---|---|---|
| Sustituye variables | No | Sí |
| Secuencias como `\n`, `\t` | No | Sí |
| Velocidad | Igual en la práctica | Igual en la práctica |

Para expresiones complejas dentro de dobles:

```php
echo "Total: {$pedido["total"]} €";
echo "Nombre: {$usuario->nombre}";
```

### Heredoc y Nowdoc

```php
$html = <<<HTML
<h1>Hola, $nombre</h1>
<p>Texto largo con "comillas" sin problema.</p>
HTML;

$literal = <<<'TEXTO'
Aquí $nombre NO se sustituye.
TEXTO;
```

- **Heredoc** (`<<<ETIQUETA`): se comporta como comillas dobles.
- **Nowdoc** (`<<<'ETIQUETA'`): se comporta como comillas simples.

### Concatenar

```php
$completo = $nombre . " " . $apellido;
$texto .= " más texto";
```

### Acceder a un carácter

```php
$palabra = "casa";
echo $palabra[0];    // c
echo $palabra[-1];   // a
```

---

## 4. Elementos / características

### Longitud y búsqueda

| Función | Qué hace |
|---|---|
| `strlen($s)` | Longitud en **bytes** |
| `mb_strlen($s)` | Longitud en **caracteres** (UTF-8) |
| `str_contains($s, "x")` | ¿Contiene el texto? |
| `str_starts_with($s, "x")` | ¿Empieza por...? |
| `str_ends_with($s, "x")` | ¿Termina en...? |
| `strpos($s, "x")` | Posición de la primera aparición, o `false` |

### Transformar

| Función | Resultado |
|---|---|
| `strtolower` / `strtoupper` | Minúsculas / mayúsculas |
| `ucfirst` / `ucwords` | Primera letra / cada palabra en mayúscula |
| `trim` / `ltrim` / `rtrim` | Quita espacios (u otros caracteres) |
| `str_replace("a", "b", $s)` | Reemplaza texto |
| `substr($s, $ini, $len)` | Extrae una parte |
| `str_repeat("ab", 3)` | Repite |
| `str_pad($s, 10, "0", STR_PAD_LEFT)` | Rellena hasta una longitud |
| `strrev($s)` | Invierte |
| `wordwrap`, `nl2br` | Ajusta líneas / salto de línea a `<br>` |

### Dividir y unir

```php
$palabras = explode(" ", "uno dos tres");   // array
$frase = implode("-", $palabras);           // "uno-dos-tres"
```

### Formatear

```php
$texto = sprintf("%s tiene %d años", "Ana", 20);
echo number_format(1234.5, 2, ",", ".");   // 1.234,50
```

| Marcador | Tipo |
|---|---|
| `%s` | Texto |
| `%d` | Entero |
| `%f` / `%.2f` | Decimal / con 2 decimales |
| `%05d` | Entero con ceros a la izquierda |

### Comparar

```php
strcmp("a", "b");         // -1, 0 o 1
strcasecmp("A", "a");     // 0 (sin distinguir mayúsculas)
$a === $b;                // comparación normal
```

### Funciones multibyte (`mb_*`)

Para textos con tildes, ñ, emojis y cualquier carácter no ASCII:

`mb_strlen`, `mb_substr`, `mb_strtoupper`, `mb_strtolower`, `mb_str_split`.

### Expresiones regulares

```php
preg_match("/^\d{8}[A-Z]$/", $dni);              // 1 si coincide
preg_replace("/\s+/", " ", $texto);              // reemplaza
preg_split("/[,;]/", "a,b;c");                    // divide
preg_match_all("/\d+/", "a1b22", $coincidencias); // todas las coincidencias
```

Teoría de regex en [[Conceptos generales/04 - Regex|Regex]].

### Seguridad al mostrar texto

```php
echo htmlspecialchars($textoUsuario, ENT_QUOTES, "UTF-8");
```

Convierte `<`, `>`, `&` y comillas en entidades HTML. Es la defensa básica contra XSS (nota 16).

---

## 5. Ejemplos prácticos

### Ejemplo básico

```php
$nombre = "  maría gómez ";
echo ucwords(trim($nombre));   // María Gómez
```

### Ejemplo habitual: crear un "slug" para una URL

```php
function crearSlug(string $titulo): string {
    $slug = mb_strtolower(trim($titulo));
    $slug = preg_replace("/[^a-z0-9]+/", "-", $slug);
    return trim($slug, "-");
}

echo crearSlug("Hola Mundo PHP");   // hola-mundo-php
```

---

## 6. Buenas prácticas

- Usa `mb_*` cuando haya acentos o emojis.
- Usa `str_contains` en vez de `strpos(...) !== false`.
- Usa `sprintf` o interpolación para frases con datos, en vez de muchas concatenaciones.
- Escapa con `htmlspecialchars` todo lo que muestres y venga del usuario.
- Guarda y trabaja siempre en UTF-8.

---

## 7. Diferencias importantes

| Comparación | Diferencia |
|---|---|
| `strlen` vs `mb_strlen` | `strlen("ñ")` da `2`; `mb_strlen("ñ")` da `1` |
| `str_contains` vs `strpos` | `strpos` devuelve posición (puede ser `0`, que es falsy) |
| Heredoc vs Nowdoc | Heredoc interpreta variables; Nowdoc no |

---

## 8. Casos especiales

### `strpos` y la posición 0

```php
if (strpos($s, "a")) { ... }        // Falla si "a" está en la posición 0
if (strpos($s, "a") !== false) { ... } // Correcto
```

### Números dentro de texto

`"5" + 3` da `8` porque PHP convierte el texto a número. Con `strict_types` esto es más estricto en funciones.

### Texto muy grande

Para ficheros enormes, lee por partes (nota 11) en lugar de cargar todo en un string.

---

## 9. Resumen

- Comillas dobles interpretan variables; simples son literales.
- El punto `.` concatena; `sprintf` y `number_format` formatean.
- Usa `mb_*` para UTF-8 y `str_contains` / `str_starts_with` para búsquedas.
- `explode` e `implode` convierten entre texto y array.
- `preg_*` son las funciones de expresiones regulares.
- `htmlspecialchars` protege al mostrar texto del usuario.