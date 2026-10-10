# 03 - Operadores

> [!info] ¿Qué es?
> Los **operadores** son símbolos que hacen una operación con uno o más valores: sumar, comparar, unir textos, etc.

---

## 1. Antes de empezar

Conviene conocer variables y tipos (nota [[PHP/02 - Variables y tipos|02 - Variables y tipos]]).

---

## 2. Concepto fundamental

Un operador toma **operandos** y devuelve un resultado. Según cuántos operandos use se llama **unario** (1), **binario** (2) o **ternario** (3).

---

## 3. Sintaxis / estructura

```php
$resultado = $a + $b;   // + es el operador, $a y $b los operandos
```

---

## 4. Elementos / características

### Aritméticos

| Operador | Operación | Ejemplo |
|---|---|---|
| `+` | Suma | `5 + 2` → `7` |
| `-` | Resta | `5 - 2` → `3` |
| `*` | Multiplicación | `5 * 2` → `10` |
| `/` | División | `5 / 2` → `2.5` |
| `%` | Resto (módulo) | `5 % 2` → `1` |
| `**` | Potencia | `2 ** 3` → `8` |
| `intdiv()` | División entera | `intdiv(5, 2)` → `2` |

### Asignación

| Operador | Equivale a |
|---|---|
| `=` | Asigna |
| `+=` | `$a = $a + ...` |
| `-=` `*=` `/=` `%=` `**=` | Igual con cada operación |
| `.=` | Añade texto al final |
| `??=` | Asigna solo si es `null` o no existe |

### Incremento y decremento

```php
$i++;   // devuelve $i y luego suma 1
++$i;   // suma 1 y luego devuelve $i
$i--;
--$i;
```

### Comparación

| Operador | Significado |
|---|---|
| `==` | Igual (valor) |
| `===` | Idéntico (valor y tipo) |
| `!=` o `<>` | Distinto (valor) |
| `!==` | No idéntico |
| `<` `>` `<=` `>=` | Menor, mayor, etc. |
| `<=>` | Nave espacial: devuelve `-1`, `0` o `1` |

```php
echo 1 <=> 2;   // -1
echo 2 <=> 2;   // 0
echo 3 <=> 2;   // 1
```

El operador `<=>` se usa mucho para ordenar (ver `usort` en la nota 07).

### Lógicos

| Operador | Significado |
|---|---|
| `&&` | Y (ambos verdaderos) |
| `\|\|` | O (al menos uno verdadero) |
| `!` | No (invierte) |
| `xor` | O exclusivo (solo uno verdadero) |
| `and` / `or` | Como `&&` y `\|\|`, pero con **menos prioridad** (evítalos) |

PHP usa **evaluación en cortocircuito**: si con el primer valor ya sabe el resultado, no evalúa el segundo.

### Cadenas

| Operador | Uso |
|---|---|
| `.` | Unir textos: `"Hola " . "mundo"` |
| `.=` | Añadir al final: `$t .= "más"` |

### Null y ternarios

| Operador | Nombre | Uso |
|---|---|---|
| `??` | Fusión de null | `$a ?? "defecto"` |
| `?:` | Elvis | `$a ?: "defecto"` (si `$a` es falsy) |
| `? :` | Ternario | `$x > 5 ? "mayor" : "menor"` |
| `?->` | Nullsafe | `$usuario?->perfil?->nombre` |

### Otros

| Operador | Uso |
|---|---|
| `instanceof` | Comprueba si un objeto es de una clase |
| `...` | Spread: expande un array (nota 07 y 06) |
| `@` | Silencia errores (**evítalo**) |
| `&` | Referencia |

---

## 5. Ejemplos prácticos

### Ejemplo básico

```php
$edad = 20;
$esMayor = $edad >= 18;      // true
$mensaje = $esMayor ? "Adulto" : "Menor";
```

### Ejemplo habitual: cadena de valores por defecto

```php
$ciudad = $_POST["ciudad"] ?? $_GET["ciudad"] ?? "Sin definir";
```
### Bit a bit (bitwise)

Trabajan con los **bits** de un número entero. Se usan sobre todo para **banderas (flags)**: varias opciones de sí/no guardadas en un solo número.

| Operador | Nombre | Ejemplo |
|---|---|---|
| `&` | Y | `6 & 3` → `2` |
| `\|` | O | `6 \| 3` → `7` |
| `^` | O exclusivo | `6 ^ 3` → `5` |
| `~` | Negación | `~6` → `-7` |
| `<<` | Desplazar a la izquierda | `1 << 3` → `8` |
| `>>` | Desplazar a la derecha | `8 >> 2` → `2` |

### Uso típico: banderas

```php
const LEER = 1;       // 001
const ESCRIBIR = 2;   // 010
const BORRAR = 4;     // 100

$permisos = LEER | ESCRIBIR;             // combinar: 3

if (($permisos & ESCRIBIR) !== 0) {      // comprobar una bandera
    echo "Puede escribir";
}

$permisos &= ~ESCRIBIR;                  // quitar una bandera
```

PHP usa este sistema en muchas constantes: `FILE_APPEND | LOCK_EX`, `JSON_PRETTY_PRINT | JSON_UNESCAPED_UNICODE`, `E_ALL & ~E_NOTICE`.

---

## 6. Buenas prácticas

- Usa `===` y `!==` siempre que puedas.
- Usa paréntesis cuando dudes de la prioridad.
- Usa `??` para valores por defecto.
- No encadenes ternarios: usa `if` o `match`.
- No uses `@`: oculta errores en vez de resolverlos.

---

## 7. Diferencias importantes

| Comparación | Resultado |
|---|---|
| `??` | Solo mira si es `null` o no existe |
| `?:` | Mira si es falsy (`0`, `""`, `[]`...) |

```php
$a = 0;
echo $a ?? 5;   // 0
echo $a ?: 5;   // 5
```

---

## 8. Casos especiales

### Prioridad de operadores

De más a menos prioridad (simplificado): `!` → `* / %` → `+ -` → `.` → comparaciones → `&&` → `||` → `??` → ternario → asignación.

> [!warning] Cuidado con `.` y `+`
> Desde PHP 8, `+` y `-` tienen **más** prioridad que `.`. Si mezclas, usa paréntesis.

### División por cero

`/` y `%` con cero lanzan `DivisionByZeroError`. Compruébalo antes.

### Comparar textos con números

En PHP 8, `0 == "texto"` es `false`. Aun así, usa `===`.

---

## 9. Resumen

- Hay operadores aritméticos, de asignación, comparación, lógicos y de cadena.
- `===` compara valor y tipo; es el recomendado.
- `??` da un valor por defecto si algo es `null`.
- `<=>` compara y devuelve `-1`, `0` o `1`.
- El punto `.` une textos.
- Usa paréntesis para dejar clara la prioridad.