# 02 - Variables y tipos

> [!info] ¿Qué es?
> Una **variable** es un nombre que guarda un valor. Cada valor tiene un **tipo** (número, texto, verdadero/falso...). PHP decide el tipo solo, pero puedes controlarlo.

---

## 1. Antes de empezar

Debes conocer la estructura básica de un script PHP (nota [[PHP/01 - Fundamentos|01 - Fundamentos]]).

---

## 2. Concepto fundamental

PHP es un lenguaje de **tipado dinámico**: una variable no tiene un tipo fijo, lo tiene su **valor**. Puedes guardar un número y luego un texto en la misma variable.

```php
$dato = 5;        // int
$dato = "cinco";  // ahora es string
```

Esa libertad es cómoda, pero puede causar errores. Por eso existen los tipos declarados (nota 06 y 17).

---

## 3. Sintaxis / estructura

### Declarar variables

```php
$nombre = "Ana";
$edad = 20;
```

No hace falta una palabra clave como `let` o `var`.

### Reglas de nombres

- Empiezan por `$` y luego una letra o `_`.
- Pueden tener letras, números y `_`.
- Distinguen mayúsculas: `$edad` ≠ `$Edad`.
- No pueden llamarse `$this`.
- Convención habitual: `camelCase` (`$nombreUsuario`) o `snake_case` (`$nombre_usuario`). Elige una y úsala siempre.

### Constantes

Valores que **no cambian**. No llevan `$`.

```php
const IVA = 0.21;
define("APP_NOMBRE", "Mi web");
```

- `const` se usa en el código normal y dentro de clases.
- `define()` permite nombres calculados en tiempo de ejecución.
- Por convención se escriben en `MAYUSCULAS`.

---

## 4. Elementos / características

### Tipos de datos

**Escalares (valores simples)**

| Tipo | Qué guarda | Ejemplo |
|---|---|---|
| `int` | Enteros | `42`, `-7` |
| `float` | Decimales | `3.14` |
| `string` | Texto | `"Hola"` |
| `bool` | Verdadero o falso | `true`, `false` |

**Compuestos**

| Tipo | Qué guarda |
|---|---|
| `array` | Lista de valores (nota 07) |
| `object` | Instancia de una clase (nota 12) |
| `callable` | Algo que se puede ejecutar (función) |
| `iterable` | Array o algo recorrible con `foreach` |

**Especiales**

| Tipo | Qué es |
|---|---|
| `null` | "Sin valor" |
| `resource` | Referencia a un recurso externo (fichero abierto, conexión) |

### Ver y comprobar el tipo

```php
var_dump($x);        // valor + tipo
gettype($x);         // "integer", "string"...
is_int($x);          // true/false
is_string($x);
is_array($x);
is_numeric("12.5");  // true: es un número o texto numérico
```

### Comprobar si existe o está vacía

| Función | Devuelve `true` cuando |
|---|---|
| `isset($x)` | La variable existe **y no es null** |
| `empty($x)` | La variable no existe o es "falsa" (`0`, `""`, `"0"`, `[]`, `null`, `false`) |
| `is_null($x)` | El valor es exactamente `null` |

### Conversión de tipos (casting)

```php
$a = (int) "42abc";   // 42
$b = (string) 15;     // "15"
$c = (float) "3.5";   // 3.5
$d = (bool) "hola";   // true
$e = (array) "x";     // ["x"]
```

También existen `intval()`, `floatval()`, `strval()`, `boolval()`.

### Valores "falsos" (falsy)

Se consideran `false` al convertirlos a bool: `false`, `0`, `0.0`, `""`, `"0"`, `[]`, `null`. **Todo lo demás es `true`**, incluso `"0.0"` y `"false"`.

---

## 5. Ejemplos prácticos

### Ejemplo básico

```php
$precio = 19.99;
$cantidad = 3;
$total = $precio * $cantidad;
echo "Total: $total €";
```

### Ejemplo habitual: valor por defecto si no existe

```php
$nombre = $_GET["nombre"] ?? "invitado";
```

`??` usa el valor de la derecha si el de la izquierda no existe o es `null` (ver nota 03).

---

## 6. Buenas prácticas

- Usa nombres descriptivos (`$precioFinal`, no `$p`).
- Inicializa las variables antes de usarlas.
- Usa constantes para valores fijos (IVA, rutas, nombres).
- Compara con `===` en vez de `==` (ver más abajo).
- Declara los tipos en funciones y propiedades (notas 06, 12 y 17).

---

## 7. Diferencias importantes

### `==` frente a `===`

| Operador | Compara | Ejemplo |
|---|---|---|
| `==` | Valor, **convirtiendo tipos** | `"5" == 5` → `true` |
| `===` | Valor **y tipo** | `"5" === 5` → `false` |

> [!tip] Regla simple
> Usa siempre `===` y `!==`. Evita sorpresas.

### Comillas simples frente a dobles

Se explican en la nota [[PHP/08 - Strings|08 - Strings]].

### `isset` frente a `empty`

- `isset` pregunta: *¿existe y tiene valor?*
- `empty` pregunta: *¿no existe o es "vacío"?*
- `0` y `"0"` son "vacíos" para `empty` pero **sí** pasan `isset`. Ten cuidado con formularios que acepten `0`.

---

## 8. Casos especiales

### Ámbito (scope) de las variables

Una variable creada fuera de una función **no se ve dentro** de ella.

```php
$x = 10;

function mostrar() {
    echo $x;   // Aviso: variable no definida
}
```

Soluciones: pasarla como parámetro (lo mejor), o usar `global $x;` (evítalo).

### Variables estáticas

Conservan su valor entre llamadas a la misma función.

```php
function contar() {
    static $veces = 0;
    $veces++;
    return $veces;
}
```

### Superglobales

Variables que se ven en **cualquier** sitio: `$_GET`, `$_POST`, `$_SESSION`, `$_COOKIE`, `$_FILES`, `$_SERVER`, `$_ENV`, `$GLOBALS`.

### Conversión automática de tipos (type juggling)

PHP convierte tipos solo en ciertas operaciones: `"5" + 3` da `8`. Con `declare(strict_types=1)` esto se vuelve más estricto en las llamadas a funciones.

### Números decimales y precisión

```php
var_dump(0.1 + 0.2 == 0.3);   // false
```

Los decimales no son exactos. Para dinero, trabaja en **céntimos** (enteros) o usa `round()`.

---

## 9. Resumen

- Las variables empiezan por `$` y su tipo lo decide el valor.
- Tipos principales: `int`, `float`, `string`, `bool`, `array`, `object`, `null`.
- `const` y `define` crean constantes.
- `var_dump` muestra valor y tipo.
- `isset`, `empty` e `is_null` comprueban existencia y vacío.
- Usa `===` en vez de `==`.
- Las variables no entran en las funciones automáticamente.