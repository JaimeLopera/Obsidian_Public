# 07 - Arrays

> [!info] ¿Qué es?
> Un **array** es una variable que guarda **varios valores**, cada uno con su **clave**. Es la estructura de datos más usada en PHP: sirve como lista, diccionario, tabla, pila o cola.

---

## 1. Antes de empezar

Debes conocer variables, bucles y funciones (notas [[PHP/02 - Variables y tipos|02]], [[PHP/05 - Bucles|05]] y [[PHP/06 - Funciones|06]]).

---

## 2. Concepto fundamental

En PHP un array es un **mapa ordenado de clave → valor**:

- Las claves son **enteros** o **textos**.
- Los elementos **mantienen el orden** en que los añadiste.
- Los valores pueden ser de **cualquier tipo**, incluso otros arrays.

| Tipo | Claves | Se parece a |
|---|---|---|
| Indexado | `0, 1, 2...` | Una lista |
| Asociativo | Textos | Un diccionario |
| Multidimensional | Cualquiera, con arrays dentro | Una tabla |

### Reglas de las claves

| Clave que escribes | Clave real |
|---|---|
| `"5"` (texto que es un entero) | `5` (entero) |
| `"05"` | `"05"` (se queda como texto) |
| `true` / `false` | `1` / `0` |
| `null` | `""` (texto vacío) |
| `1.7` (decimal) | `1` (se corta; obsoleto desde PHP 8.1) |

Si repites una clave, **gana el último valor**.

---

## 3. Sintaxis / estructura

### Crear

```php
$colores = ["rojo", "verde", "azul"];                // indexado
$persona = ["nombre" => "Ana", "edad" => 20];        // asociativo
$mezcla  = ["a", "x" => 1, "b"];                     // claves: 0, "x", 1

$alumnos = [                                         // multidimensional
    ["nombre" => "Ana",  "nota" => 8],
    ["nombre" => "Luis", "nota" => 6],
];
```

### Leer

```php
echo $colores[0];               // rojo
echo $persona["nombre"];        // Ana
echo $alumnos[1]["nombre"];     // Luis
echo $persona["ciudad"] ?? "sin datos";   // clave que puede no existir
```

Leer una clave que no existe da un aviso (*Undefined array key*). Usa `??` o `isset`.

### Añadir, modificar y borrar

```php
$colores[] = "amarillo";          // añade al final
$colores[1] = "lima";             // modifica
$persona["ciudad"] = "Sevilla";   // nueva clave
unset($colores[0]);               // borra, SIN reindexar
$colores = array_values($colores);   // reindexa 0, 1, 2...
```

### Crear arrays con funciones

```php
range(1, 5);                       // [1, 2, 3, 4, 5]
range(0, 10, 2);                   // [0, 2, 4, 6, 8, 10]
range("a", "e");                   // ["a", "b", "c", "d", "e"]
array_fill(0, 3, "x");             // ["x", "x", "x"]
array_fill_keys(["a", "b"], 0);    // ["a" => 0, "b" => 0]
array_pad([1, 2], 4, 0);           // [1, 2, 0, 0]
array_combine(["a", "b"], [1, 2]); // ["a" => 1, "b" => 2]
compact("nombre", "edad");         // ["nombre" => $nombre, "edad" => $edad]
```

---

## 4. Elementos / características

### Información y búsqueda

| Función | Qué hace |
|---|---|
| `count($a)` | Número de elementos (`COUNT_RECURSIVE` cuenta también los internos) |
| `empty($a)` | `true` si está vacío |
| `isset($a["x"])` | `true` si la clave existe y su valor no es `null` |
| `array_key_exists("x", $a)` | `true` si la clave existe, aunque su valor sea `null` |
| `in_array($v, $a, true)` | ¿Está el valor? (`true` = comparación estricta) |
| `array_search($v, $a, true)` | Clave del valor, o `false` si no está |
| `array_key_first($a)` / `array_key_last($a)` | Primera / última clave (`null` si está vacío) |
| `array_is_list($a)` | `true` si las claves son `0, 1, 2...` en orden (PHP 8.1) |

### Añadir y quitar

| Función | Qué hace |
|---|---|
| `$a[] = $v` | Añade al final (lo más habitual) |
| `array_push($a, $v1, $v2)` | Añade uno o varios al final |
| `array_pop($a)` | Quita y devuelve el **último** |
| `array_shift($a)` | Quita y devuelve el **primero** (reindexa) |
| `array_unshift($a, $v)` | Añade al **principio** (reindexa) |
| `array_splice($a, $pos, $n, $nuevos)` | Quita `$n` elementos desde `$pos` y, si quieres, inserta otros. Modifica el array |
| `unset($a[$k])` | Quita sin reindexar |

### Extraer y combinar

| Función | Qué hace |
|---|---|
| `array_slice($a, $ini, $n)` | Copia una parte. Reindexa las claves numéricas (pasa `true` como 4.º argumento para conservarlas) |
| `array_merge($a, $b)` | Une. Reindexa las numéricas; en las de texto repetidas gana la última |
| `[...$a, ...$b]` | Igual que `array_merge`, con spread |
| `$a + $b` | Unión: si una clave se repite, **gana la del primero** |
| `array_replace($a, $b)` | Reemplaza valores por clave |
| `array_chunk($a, $n)` | Divide en trozos de `$n` elementos |

### Claves y valores

| Función | Qué hace |
|---|---|
| `array_keys($a)` | Todas las claves (`array_keys($a, $valor)` solo las de ese valor) |
| `array_values($a)` | Todos los valores, reindexados |
| `array_flip($a)` | Intercambia claves y valores (los valores deben ser enteros o textos) |
| `array_column($a, "col", "clave")` | Una columna de un array de arrays |
| `array_unique($a)` | Quita repetidos (conserva la primera clave de cada uno) |
| `array_reverse($a)` | Invierte el orden |
| `array_count_values($a)` | Cuenta cuántas veces aparece cada valor |

### Conjuntos (comparar arrays)

| Función | Qué hace |
|---|---|
| `array_diff($a, $b)` | Valores de `$a` que **no** están en `$b` |
| `array_diff_key($a, $b)` | Lo mismo, comparando claves |
| `array_intersect($a, $b)` | Valores que están en **ambos** |
| `array_intersect_key($a, $b)` | Lo mismo, comparando claves |

`array_diff` y `array_intersect` comparan los valores **como texto**.

### Cálculos y azar

| Función | Qué hace |
|---|---|
| `array_sum($a)` / `array_product($a)` | Suma / producto |
| `min($a)` / `max($a)` | Menor / mayor |
| `shuffle($a)` | Mezcla (modifica el array) |
| `array_rand($a, $n)` | Una o varias claves al azar |

> [!warning] Azar y seguridad
> `shuffle` y `array_rand` **no son seguros** para tokens o sorteos importantes. Para eso usa `random_int` (nota [[PHP/09 - Matemáticas y fechas|09]]).

### Transformar con funciones

```php
$numeros = [1, 2, 3, 4];

$dobles = array_map(fn($n) => $n * 2, $numeros);              // [2, 4, 6, 8]
$pares  = array_filter($numeros, fn($n) => $n % 2 === 0);     // [1 => 2, 3 => 4]
$suma   = array_reduce($numeros, fn($acc, $n) => $acc + $n, 0);   // 10
```

| Función | Detalles |
|---|---|
| `array_map($fn, $a)` | Aplica la función a cada elemento y devuelve otro array. Con un solo array conserva las claves |
| `array_filter($a, $fn)` | Se queda con los que cumplen la condición. **Conserva las claves**. Sin función, quita los valores "falsy" |
| `array_reduce($a, $fn, $inicial)` | Reduce el array a un solo valor |
| `array_walk($a, $fn)` | Recorre el array; si la función recibe `&$valor`, lo modifica |

`array_filter` puede recibir también la clave con `ARRAY_FILTER_USE_KEY` o ambos con `ARRAY_FILTER_USE_BOTH`:

```php
$sinEdad = array_filter($persona, fn($valor, $clave) => $clave !== "edad", ARRAY_FILTER_USE_BOTH);
```

Para modificar con `array_walk`:

```php
array_walk($precios, function (&$precio, $clave) {
    $precio = round($precio * 1.21, 2);
});
```

**Funciones de búsqueda nuevas:**

```php
$primerPar = array_find($numeros, fn($n) => $n % 2 === 0);    // 2        (PHP 8.4)
$hayPar    = array_any($numeros, fn($n) => $n % 2 === 0);     // true     (PHP 8.4)
$todosPos  = array_all($numeros, fn($n) => $n > 0);           // true     (PHP 8.4)
$primero   = array_first($numeros);                            // 1        (PHP 8.5)
$ultimo    = array_last($numeros);                             // 4        (PHP 8.5)
```

También existe `array_find_key` (PHP 8.4). Si tu servidor tiene una versión anterior, usa `foreach`.

### Ordenar

| Función | Ordena por | Conserva claves |
|---|---|---|
| `sort` / `rsort` | Valor (ascendente / descendente) | No |
| `asort` / `arsort` | Valor | Sí |
| `ksort` / `krsort` | Clave | Sí |
| `usort` | Valor, con tu función | No |
| `uasort` | Valor, con tu función | Sí |
| `uksort` | Clave, con tu función | Sí |
| `natsort` / `natcasesort` | Orden natural (`img2` antes que `img10`) | Sí |
| `array_multisort` | Varios arrays o columnas a la vez | Según el caso |

- Todas **modifican el array original** y devuelven `true`.
- Desde PHP 8.0 el orden es **estable** (si dos elementos son iguales, mantienen su orden).
- Se pueden usar banderas: `SORT_STRING`, `SORT_NUMERIC`, `SORT_NATURAL | SORT_FLAG_CASE`.

La función de `usort` devuelve un número negativo, cero o positivo. Lo más cómodo es el operador `<=>` (nota [[PHP/03 - Operadores|03]]):

```php
usort($alumnos, fn($a, $b) => $b["nota"] <=> $a["nota"]);   // por nota, de mayor a menor

// Por nota (mayor a menor) y, si empatan, por nombre (A-Z)
usort($alumnos, fn($a, $b) =>
    [$b["nota"], $a["nombre"]] <=> [$a["nota"], $b["nombre"]]
);
```

### Texto ↔ array

```php
explode(",", "a,b,c");         // ["a", "b", "c"]
explode(",", "a,b,c", 2);      // ["a", "b,c"]   (máximo 2 trozos)
implode(", ", ["a", "b"]);     // "a, b"
str_split("abc");              // ["a", "b", "c"]
mb_str_split("ñandú");         // letra a letra, respetando UTF-8
```

### Desestructurar

```php
[$a, $b] = [10, 20];
["nombre" => $n, "edad" => $e] = $persona;
[, $segundo] = [1, 2];          // se salta el primero
[$a, $b] = [$b, $a];            // intercambia dos variables

foreach ($puntos as [$x, $y]) { }   // dentro de un foreach
```

### Recorrer

Se recorre con `foreach` (nota [[PHP/05 - Bucles|05]]):

```php
foreach ($persona as $clave => $valor) {
    echo "$clave: $valor";
}
```

Existen funciones antiguas con puntero interno (`current`, `next`, `reset`, `end`, `key`), pero hoy casi nunca hacen falta.

### `compact` y `extract`

`compact("a", "b")` crea un array con esas variables. `extract($array)` hace lo contrario: crea variables a partir de las claves.

> [!warning] No uses `extract` con datos del usuario
> `extract($_POST)` permite al usuario crear o pisar variables de tu programa.

---

## 5. Ejemplos prácticos

### Ejemplo básico

```php
$notas = [7, 9, 5];
$media = $notas ? array_sum($notas) / count($notas) : 0;   // evita dividir entre 0
```

### Ejemplo habitual: agrupar y extraer columnas

```php
$productos = [
    ["nombre" => "Ratón",   "categoria" => "perifericos", "precio" => 20],
    ["nombre" => "Teclado", "categoria" => "perifericos", "precio" => 35],
    ["nombre" => "Monitor", "categoria" => "pantallas",   "precio" => 150],
];

$nombres = array_column($productos, "nombre");               // ["Ratón", "Teclado", "Monitor"]
$precios = array_column($productos, "precio", "nombre");     // ["Ratón" => 20, "Teclado" => 35, ...]

$porCategoria = [];
foreach ($productos as $p) {
    $porCategoria[$p["categoria"]][] = $p["nombre"];         // crea los arrays internos solo
}
// ["perifericos" => ["Ratón", "Teclado"], "pantallas" => ["Monitor"]]
```

### Ejemplo completo: contar palabras y ordenar por frecuencia

```php
$texto = "hola mundo hola php";
$conteo = array_count_values(explode(" ", $texto));   // ["hola" => 2, "mundo" => 1, "php" => 1]
arsort($conteo);                                       // de más a menos, conservando las claves
```

---

## 6. Buenas prácticas

- Usa `[]` en vez de `array()`.
- Usa `in_array(..., true)` y `array_search(..., true)` (comparación estricta).
- Lee claves que pueden no existir con `??`.
- Elige bien: ¿es una **lista** (claves 0, 1, 2) o un **mapa** (claves con significado)?
- Si el array tiene datos con estructura fija (id, nombre, precio), considera usar una **clase** (nota [[PHP/14 - POO|14]]).
- Usa `array_map` / `array_filter` cuando dejen el código más claro; si se complica, usa `foreach`.
- Comprueba que el array no esté vacío antes de usar `max`, `min` o dividir.

---

## 7. Diferencias importantes

| Comparación | Diferencia |
|---|---|
| `isset` vs `array_key_exists` | `isset` da `false` si el valor es `null` |
| `array_merge` vs `+` | `array_merge` reindexa las numéricas y gana el último; `+` conserva el primero |
| `sort` vs `asort` | `sort` pierde las claves; `asort` las conserva |
| `unset` vs `array_splice` | `unset` deja un hueco en las claves; `array_splice` reindexa |
| `array_map` vs `foreach` | `array_map` devuelve un array nuevo; `foreach` es más flexible |
| `array_search` vs `in_array` | `array_search` devuelve la clave; `in_array` solo `true` o `false` |

---

## 8. Casos especiales

### Los arrays se copian al asignarlos

```php
$a = [1, 2];
$b = $a;
$b[] = 3;      // $a sigue siendo [1, 2]
```

PHP copia de forma perezosa (solo cuando modificas), así que es barato. Los **objetos** dentro del array sí se comparten.

### `array_search` y la clave 0

```php
if (array_search("a", ["a", "b"])) { }              // ❌ falla: la clave es 0 (falsy)
if (array_search("a", ["a", "b"]) !== false) { }    // ✅
```

### `foreach` por valor recorre una copia

Si modificas el array dentro del `foreach`, el bucle no ve esos cambios. Con referencia (`&$valor`) sí, pero acuérdate de `unset($valor)` al terminar.

### `array_filter` conserva claves

Si quieres una lista limpia (por ejemplo para convertir a JSON), usa `array_values`:

```php
$pares = array_values(array_filter($numeros, fn($n) => $n % 2 === 0));
```

Un array con huecos en las claves se convierte en **objeto** al hacer `json_encode` (nota [[PHP/13 - JSON y APIs|13]]).

### Errores con arrays vacíos

- `max([])` y `min([])` lanzan `ValueError` en PHP 8.
- `count(null)` lanza `TypeError`: `count` solo acepta arrays u objetos contables.

### Comparación flexible de `in_array`

Sin `true`, `in_array("1e1", ["10"])` da `true` porque ambos son textos numéricos iguales. Usa siempre el modo estricto.

### Funciones como texto

Puedes pasar el nombre de una función en texto o como *first-class callable*:

```php
array_map("trim", $lineas);
array_map(strtoupper(...), $palabras);
```

### Spread con claves de texto (PHP 8.1)

```php
$a = ["x" => 1];
$b = ["y" => 2];
$c = [...$a, ...$b];   // ["x" => 1, "y" => 2]
```

### Arrays muy grandes

Ocupan mucha memoria. Si solo necesitas recorrerlos, usa un *generator* con `yield` (nota [[PHP/05 - Bucles|05]]) o procesa por trozos con `array_chunk`.

---

## 9. Resumen

- Un array es un mapa ordenado de claves (enteros o textos) a valores.
- `[]` añade; `unset` borra sin reindexar; `array_values` reindexa.
- Buscar: `in_array`, `array_search`, `array_key_exists`, `isset`; siempre en modo estricto.
- Transformar: `array_map`, `array_filter` (conserva claves), `array_reduce`, `array_column`.
- Ordenar: `sort` / `asort` / `ksort`, y `usort` con `<=>` para criterios propios.
- Combinar: `array_merge` o spread, `array_diff`, `array_intersect`.
- Los arrays se copian al asignarse; `max` y `min` fallan con arrays vacíos.