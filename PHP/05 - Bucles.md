# 05 - Bucles

> [!info] ¿Qué es?
> Un **bucle** repite un bloque de código varias veces: un número fijo de veces, mientras se cumpla una condición o por cada elemento de un array.

---

## 1. Antes de empezar

Debes conocer condiciones (nota [[PHP/04 - Condicionales|04 - Condicionales]]) y arrays básicos (nota [[PHP/07 - Arrays|07 - Arrays]]).

---

## 2. Concepto fundamental

Un bucle tiene siempre tres ideas: un **punto de partida**, una **condición** para seguir y un **cambio** en cada vuelta que evite que sea infinito.

---

## 3. Sintaxis / estructura

### `for` (sabes cuántas veces)

```php
for ($i = 1; $i <= 5; $i++) {
    echo $i;
}
```

Partes: `inicio; condición; paso`.

### `while` (mientras se cumpla)

```php
$i = 1;
while ($i <= 5) {
    echo $i;
    $i++;
}
```

La condición se comprueba **antes** de cada vuelta.

### `do ... while`

```php
do {
    echo $i;
    $i++;
} while ($i <= 5);
```

Se ejecuta **al menos una vez**, porque la condición se comprueba **después**.

### `foreach` (para arrays)

```php
$frutas = ["manzana", "pera", "uva"];

foreach ($frutas as $fruta) {
    echo $fruta;
}
```

Con clave y valor:

```php
$edades = ["Ana" => 20, "Luis" => 25];

foreach ($edades as $nombre => $edad) {
    echo "$nombre tiene $edad";
}
```

---

## 4. Elementos / características

### `break` y `continue`

| Palabra | Qué hace |
|---|---|
| `break` | Sale del bucle |
| `continue` | Salta a la siguiente vuelta |
| `break 2` | Sale de **dos** bucles anidados |

```php
foreach ($numeros as $n) {
    if ($n === 0) continue;     // se salta el cero
    if ($n < 0) break;          // termina al ver un negativo
    echo $n;
}
```

### Modificar elementos con referencia

```php
foreach ($precios as &$precio) {
    $precio = $precio * 1.21;
}
unset($precio);   // importante
```

El `&` hace que `$precio` sea el elemento real, no una copia.

### Descomponer arrays dentro del bucle

```php
foreach ($puntos as [$x, $y]) {
    echo "$x,$y";
}
```

### Generar rangos

```php
foreach (range(1, 10, 2) as $n) {   // 1, 3, 5, 7, 9
    echo $n;
}
```

### Sintaxis alternativa (plantillas)

```php
<?php foreach ($productos as $p): ?>
    <li><?= htmlspecialchars($p["nombre"]) ?></li>
<?php endforeach; ?>
```

---

## 5. Ejemplos prácticos

### Ejemplo básico

```php
$suma = 0;
for ($i = 1; $i <= 10; $i++) {
    $suma += $i;
}
echo $suma;   // 55
```

### Ejemplo habitual: tabla HTML desde un array

```php
echo "<table>";
foreach ($usuarios as $u) {
    echo "<tr><td>" . htmlspecialchars($u["nombre"]) . "</td></tr>";
}
echo "</table>";
```

---

## 6. Buenas prácticas

- Usa `foreach` para recorrer arrays (más claro y seguro que `for`).
- Evita modificar el array mientras lo recorres.
- Si usas referencia (`&`), haz `unset()` de la variable al terminar.
- Evita bucles muy anidados: sepáralos en funciones.
- No hagas consultas a la base de datos dentro de un bucle si puedes hacer una sola antes.
- Asegúrate de que la condición llegará a ser falsa.

---

## 7. Diferencias importantes

| Bucle | Úsalo cuando |
|---|---|
| `for` | Sabes cuántas vueltas hacer |
| `while` | No sabes cuántas, depende de una condición |
| `do while` | Debe ejecutarse al menos una vez |
| `foreach` | Recorres arrays u objetos |

---

## 8. Casos especiales

### Bucle infinito

```php
while (true) {
    // ...
    if ($terminar) break;
}
```

Es válido si tienes una salida con `break`.

### Recorrer objetos

`foreach` también recorre las propiedades públicas de un objeto.

### Generators (para datos muy grandes)

```php
function numeros() {
    for ($i = 1; $i <= 1000000; $i++) {
        yield $i;
    }
}

foreach (numeros() as $n) { ... }
```

`yield` va entregando valores uno a uno, sin guardar todo en memoria.

---

## 9. Resumen

- `for` para un número conocido de vueltas, `while` y `do while` para condiciones, `foreach` para arrays.
- `break` sale del bucle; `continue` salta a la siguiente vuelta.
- `foreach` con `&` modifica el array original; haz `unset()` después.
- Evita bucles infinitos y anidados en exceso.
- Los generators ahorran memoria con grandes cantidades de datos.