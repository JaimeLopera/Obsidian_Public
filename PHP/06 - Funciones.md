# 06 - Funciones

> [!info] ¿Qué es?
> Una **función** es un bloque de código con nombre que puedes reutilizar. Recibe datos (parámetros), hace algo y puede devolver un resultado.

---

## 1. Antes de empezar

Debes conocer variables, tipos y condicionales (notas [[PHP/02 - Variables y tipos|02]] y [[PHP/04 - Condicionales|04]]).

---

## 2. Concepto fundamental

Las funciones evitan repetir código. Se **definen** una vez y se **llaman** las veces que haga falta. Las variables de dentro de una función **son locales**: no existen fuera.

---

## 3. Sintaxis / estructura

```php
function saludar(string $nombre): string {
    return "Hola, $nombre";
}

echo saludar("Ana");   // Hola, Ana
```

Partes:

- `function` + nombre.
- Parámetros entre paréntesis, con su tipo.
- Tipo de retorno tras `:`.
- `return` devuelve el resultado y termina la función.

---

## 4. Elementos / características

### Parámetros con valor por defecto

```php
function saludar(string $nombre = "invitado"): string {
    return "Hola, $nombre";
}
```

Los parámetros con valor por defecto van **al final**.

### Tipos de parámetros y retorno

| Tipo | Ejemplo |
|---|---|
| Simples | `int`, `float`, `string`, `bool`, `array` |
| Nullable | `?string` (puede ser `null`) |
| Unión | `int\|float` |
| Sin retorno | `void` |
| Nunca vuelve | `never` (lanza error o termina el script) |
| Clase | `Usuario` |

```php
function dividir(int|float $a, int|float $b): float {
    return $a / $b;
}
```

### Argumentos con nombre (PHP 8)

```php
function crearUsuario(string $nombre, int $edad = 18, bool $activo = true) { ... }

crearUsuario(nombre: "Ana", activo: false);
```

Permiten saltar parámetros opcionales y hacen el código más legible.

### Paso por valor y por referencia

```php
function sumarUno(int &$n): void {
    $n++;
}

$x = 5;
sumarUno($x);   // $x vale 6
```

Con `&` modificas la variable original. Úsalo con moderación.

### Número variable de argumentos

```php
function sumar(int ...$numeros): int {
    return array_sum($numeros);
}

echo sumar(1, 2, 3, 4);   // 10
```

### Devolver varios valores

```php
function minMax(array $n): array {
    return [min($n), max($n)];
}

[$min, $max] = minMax([3, 8, 1]);
```

### Funciones anónimas (closures)

```php
$doble = function (int $n): int {
    return $n * 2;
};

echo $doble(4);   // 8
```

Para usar variables externas hay que traerlas con `use`:

```php
$iva = 0.21;
$conIva = function (float $p) use ($iva): float {
    return $p * (1 + $iva);
};
```

### Funciones flecha

```php
$doble = fn(int $n): int => $n * 2;
```

- Una sola expresión.
- **Captura automáticamente** las variables externas (solo lectura).

### Funciones como argumento (callables)

```php
$resultado = array_map(fn($n) => $n * 2, [1, 2, 3]);
```

---

## 5. Ejemplos prácticos

### Ejemplo básico

```php
function esPar(int $n): bool {
    return $n % 2 === 0;
}
```

### Ejemplo habitual: función con validación

```php
function calcularPrecioFinal(float $precio, float $descuento = 0.0): float {
    if ($precio < 0 || $descuento < 0 || $descuento > 100) {
        throw new InvalidArgumentException("Valores no válidos");
    }
    return round($precio * (1 - $descuento / 100), 2);
}
```

---

## 6. Buenas prácticas

- Una función debe hacer **una sola cosa**.
- Nombres con verbo: `calcularTotal`, `obtenerUsuario`.
- Declara siempre tipos de parámetros y de retorno.
- Evita variables globales: pasa los datos como parámetros.
- Evita `&` salvo que sea necesario.
- Evita funciones muy largas (más de unas 30 líneas suele indicar que se puede dividir).
- Documenta con DocBlock si algo no es obvio.

---

## 7. Diferencias importantes

| | Función normal | Closure | Función flecha |
|---|---|---|---|
| Nombre | Sí | No (se guarda en variable) | No |
| Variables externas | No | Con `use` | Automático (lectura) |
| Varias líneas | Sí | Sí | No (solo una expresión) |

---

## 8. Casos especiales

### Recursión

Una función que se llama a sí misma.

```php
function factorial(int $n): int {
    return $n <= 1 ? 1 : $n * factorial($n - 1);
}
```

Necesita siempre un **caso base** que la detenga.

### `declare(strict_types=1)`

Al inicio del fichero, obliga a que los tipos de los argumentos coincidan exactamente:

```php
declare(strict_types=1);

function doble(int $n): int { return $n * 2; }
doble("5");   // TypeError
```

Sin esa línea, PHP convertiría `"5"` a `5`.

### Funciones integradas útiles

`strlen`, `count`, `round`, `max`, `min`, `date`, `is_numeric`, `array_map`, `in_array`... Antes de escribir una función, comprueba si PHP ya la tiene.

### Función como valor de primera clase (PHP 8.1)

```php
$f = strlen(...);
echo $f("hola");   // 4
```

---

## 9. Resumen

- Se define con `function`, parámetros y `return`.
- Usa tipos en parámetros y retorno.
- Puede tener valores por defecto, argumentos con nombre y número variable de argumentos (`...`).
- Closures (`function`) y funciones flecha (`fn`) son funciones sin nombre.
- Las variables de dentro son locales.
- Una función, una responsabilidad.