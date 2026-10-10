# 09 - Matemáticas y fechas

> [!info] ¿Qué es?
> PHP incluye funciones para hacer **cálculos** (redondear, potencias, números aleatorios) y para trabajar con **fechas y horas** (mostrarlas, compararlas, sumar días, calcular edades). Se usan en casi cualquier proyecto real.

---

## 1. Antes de empezar

Debes conocer variables, tipos y operadores (notas [[PHP/02 - Variables y tipos|02]] y [[PHP/03 - Operadores|03]]). Para la parte de fechas se usan objetos; aquí se explica lo necesario y en [[PHP/14 - POO|14 - POO]] tienes la base.

---

## 2. Concepto fundamental

### Números

- **`int`**: entero. En sistemas de 64 bits el máximo es `PHP_INT_MAX` (9223372036854775807). Si te pasas, el número se convierte en `float`.
- **`float`**: decimal. Guarda **aproximaciones**, por eso `0.1 + 0.2` no es exactamente `0.3`.
- La división `/` devuelve un `int` si es exacta (`10 / 5` → `2`) y un `float` si no (`10 / 4` → `2.5`).

### Fechas

Hay tres formas de representar una fecha:

| Forma | Qué es |
|---|---|
| **Timestamp** | Número de segundos desde el 1 de enero de 1970 (UTC) |
| **Texto** | `"2026-10-10 14:30"` o `"10/10/2026"` |
| **Objeto** | `DateTimeImmutable` (lo recomendado) |

Además siempre hay una **zona horaria**: la misma hora puede ser distinta según el lugar.

---

## 3. Sintaxis / estructura

## Parte A: Matemáticas

### Funciones básicas

| Función | Qué hace |
|---|---|
| `abs($n)` | Valor absoluto |
| `round($n, $decimales)` | Redondea. En la mitad, se aleja del cero |
| `floor($n)` / `ceil($n)` | Redondea hacia abajo / hacia arriba |
| `intdiv($a, $b)` | División entera (devuelve `int`) |
| `$a % $b` / `fmod($a, $b)` | Resto con enteros / con decimales |
| `$a ** $b` / `pow($a, $b)` | Potencia |
| `sqrt($n)` | Raíz cuadrada |
| `min(...)` / `max(...)` | Menor / mayor (varios valores o un array) |
| `pi()` o `M_PI` | El número π |
| `log($n)`, `log10($n)`, `exp($n)` | Logaritmos y exponencial |
| `sin`, `cos`, `tan` | Trigonometría (en radianes; `deg2rad` y `rad2deg` convierten) |
| `is_nan`, `is_infinite`, `is_finite` | Comprobar valores especiales |
| `fdiv($a, $b)` | División que devuelve `INF` o `NAN` en vez de lanzar error (PHP 8) |

```php
round(3.14159, 2);   // 3.14
round(2.5);          // 3
round(-2.5);         // -3
round(1234, -2);     // 1200    (precisión negativa: redondea a centenas)
floor(5.7);          // 5
ceil(5.1);           // 6
intdiv(7, 2);        // 3
7 % 3;               // 1
-7 % 3;              // -1      (el signo es el del primer número)
fmod(7.5, 2);        // 1.5
2 ** 10;             // 1024
```

> [!note] Devuelven `float`
> `round`, `floor` y `ceil` devuelven `float` aunque el resultado sea un número entero. Si necesitas un `int`, conviértelo con `(int)`.

### División entre cero

```php
1 / 0;          // DivisionByZeroError
5 % 0;          // DivisionByZeroError
intdiv(1, 0);   // DivisionByZeroError
fdiv(1, 0);     // INF (no lanza error)
```

### Mostrar números con formato

```php
echo number_format(1234567.891);                  // 1,234,568
echo number_format(1234567.891, 2);               // 1,234,567.89
echo number_format(1234567.891, 2, ",", ".");     // 1.234.567,89
```

Los argumentos son: número, decimales, separador decimal, separador de miles.

### Números aleatorios

| Función | Uso |
|---|---|
| `random_int($min, $max)` | **Seguro** (criptográfico). Úsalo para códigos, tokens y sorteos importantes |
| `random_bytes($n)` | Bytes aleatorios seguros. Combínalo con `bin2hex` |
| `mt_rand($min, $max)` / `rand()` | Rápidos, pero **no seguros** |
| `mt_srand($semilla)` | Fija la semilla para obtener siempre la misma secuencia (tests, juegos) |

```php
$dado   = random_int(1, 6);
$token  = bin2hex(random_bytes(16));   // 32 caracteres hexadecimales
```

### Precisión decimal y dinero

```php
var_dump(0.1 + 0.2 === 0.3);                              // false
var_dump(abs((0.1 + 0.2) - 0.3) < PHP_FLOAT_EPSILON);     // true
```

- **Nunca compares decimales con `===`**: compara con una pequeña diferencia o redondea antes.
- **Para dinero, trabaja en céntimos** (enteros):

```php
$precioCentimos = 1999;
$total = $precioCentimos * 3;                                // 5997
echo number_format($total / 100, 2, ",", ".") . " €";        // 59,97 €
```

Si necesitas decimales exactos con muchos dígitos, existe la extensión `bcmath` (`bcadd("0.1", "0.2", 2)` da `"0.30"`).

### Cambiar de base

```php
decbin(10);        // "1010"
bindec("1010");    // 10
dechex(255);       // "ff"
hexdec("ff");      // 255
base_convert("ff", 16, 2);   // "11111111"
```

### Validar números del usuario

```php
is_numeric("12.5");                                  // true
filter_var("42", FILTER_VALIDATE_INT);               // 42
filter_var("abc", FILTER_VALIDATE_INT);              // false
filter_var("3.5", FILTER_VALIDATE_FLOAT);            // 3.5
```

> [!tip] Decimales con coma
> En España los usuarios escriben `12,5`. Reemplaza la coma por un punto antes de convertir: `str_replace(",", ".", $texto)`.

Constantes útiles: `PHP_INT_MAX`, `PHP_INT_MIN`, `PHP_FLOAT_EPSILON`, `PHP_FLOAT_MAX`.

---

## Parte B: Fechas y horas

### Zona horaria

```php
date_default_timezone_set("Europe/Madrid");
echo date_default_timezone_get();
```

Lo mejor es definirla una vez en `php.ini` (`date.timezone = "Europe/Madrid"`) o al inicio de tu aplicación. Si no la configuras, PHP usa UTC.

### `date()` y timestamps

```php
echo date("d/m/Y H:i:s");                 // 10/10/2026 14:30:05
echo date("d/m/Y", $timestamp);           // fecha de un timestamp concreto

time();                                   // timestamp actual
mktime(14, 30, 0, 10, 10, 2026);          // hora, minuto, segundo, mes, día, año
strtotime("2026-12-25 18:00");            // texto → timestamp (false si falla)
checkdate(2, 30, 2026);                   // false: mes, día, año (¿existe esa fecha?)
```

### Formatos de `date()` y `format()`

| Letra | Significado |
|---|---|
| `d` / `j` | Día con ceros (01–31) / sin ceros |
| `D` / `l` | Día de la semana abreviado / completo (**en inglés**) |
| `N` | Día de la semana: 1 (lunes) a 7 (domingo) |
| `m` / `n` | Mes con ceros / sin ceros |
| `M` / `F` | Mes abreviado / completo (**en inglés**) |
| `Y` / `y` | Año con 4 / 2 dígitos |
| `H` / `G` | Hora 24 h con ceros / sin ceros |
| `h` / `g` | Hora 12 h con ceros / sin ceros |
| `i` / `s` | Minutos / segundos |
| `A` / `a` | AM, PM / am, pm |
| `t` | Días que tiene el mes |
| `L` | `1` si el año es bisiesto |
| `W` | Número de semana ISO |
| `U` | Timestamp |
| `e` / `T` | Zona horaria (identificador / abreviatura) |
| `P` | Desfase (`+02:00`) |
| `c` | Fecha ISO 8601 completa |

Para escribir letras literales, escápalas con `\`: `date("\H\o\y \e\s d/m")`.

### Nombres en español

`date()` devuelve los nombres de días y meses en **inglés**. Para español usa la extensión `intl`:

```php
$f = new IntlDateFormatter("es_ES", IntlDateFormatter::FULL, IntlDateFormatter::NONE, "Europe/Madrid");
echo $f->format(new DateTimeImmutable());   // sábado, 10 de octubre de 2026
```

`strftime()` y `setlocale()` para fechas están **obsoletos** desde PHP 8.1.

### `DateTimeImmutable` (recomendado)

```php
$ahora  = new DateTimeImmutable();                                  // ahora
$fecha  = new DateTimeImmutable("2026-10-10 14:30");
$madrid = new DateTimeImmutable("now", new DateTimeZone("Europe/Madrid"));

$navidad = DateTimeImmutable::createFromFormat("!d/m/Y", "25/12/2026");   // false si no coincide
echo $navidad->format("Y-m-d");        // 2026-12-25
```

El `!` al inicio del formato pone la hora a `00:00:00`. Sin él, la hora sería la actual.

**Validar una fecha escrita por el usuario** (rechaza `31/02/2026`):

```php
function fechaValida(string $texto, string $formato = "d/m/Y"): bool
{
    $f = DateTimeImmutable::createFromFormat("!" . $formato, $texto);
    return $f !== false && $f->format($formato) === $texto;
}
```

### Operar con fechas

```php
$manana      = $fecha->modify("+1 day");
$proxMes     = $fecha->add(new DateInterval("P1M"));
$haceUnAnio  = $fecha->sub(new DateInterval("P1Y"));
$inicioMes   = $fecha->modify("first day of this month")->setTime(0, 0);
$finMes      = $fecha->modify("last day of this month");
$enUtc       = $fecha->setTimezone(new DateTimeZone("UTC"));
```

`DateTimeImmutable` **no modifica** el objeto original: cada operación devuelve uno nuevo.

### `DateInterval`: duraciones

Empieza por `P`; la parte de horas va tras una `T`.

| Texto | Significado |
|---|---|
| `P1D` | 1 día |
| `P2W` | 2 semanas |
| `P3M` | 3 meses |
| `P1Y` | 1 año |
| `PT4H` | 4 horas |
| `PT30M` | 30 minutos |
| `P1Y2M3DT4H5M6S` | Todo combinado |

### Diferencia entre fechas

```php
$nacimiento = new DateTimeImmutable("2005-03-15");
$hoy        = new DateTimeImmutable("today");

$dif = $nacimiento->diff($hoy);
echo $dif->y;      // años cumplidos (la edad)
echo $dif->days;   // días totales
echo $dif->invert; // 1 si $hoy es anterior a $nacimiento
echo $dif->format("%y años, %m meses y %d días");
```

Marcadores de `DateInterval::format`: `%y` años, `%m` meses, `%d` días, `%h` horas, `%i` minutos, `%s` segundos, `%a` días totales, `%R` signo.

### Comparar fechas

```php
if ($a < $b) { }
if ($a == $b) { }    // mismo instante
```

Con objetos de fecha puedes usar `<`, `>` y `==`. No uses `===`: compara si son **el mismo objeto**.

### Recorrer un rango de fechas

```php
$periodo = new DatePeriod(
    new DateTimeImmutable("2026-10-01"),
    new DateInterval("P1D"),
    new DateTimeImmutable("2026-10-08")      // el final NO se incluye
);

foreach ($periodo as $dia) {
    echo $dia->format("d/m"), "\n";
}
```

Desde PHP 8.2 puedes incluir el final pasando `DatePeriod::INCLUDE_END_DATE` como cuarto argumento.

### `strtotime` con texto

```php
strtotime("now");
strtotime("tomorrow");
strtotime("+2 weeks");
strtotime("next monday");
strtotime("last day of next month");
```

### Medir tiempo de ejecución

```php
$inicio = hrtime(true);          // nanosegundos
// ... código ...
$ms = (hrtime(true) - $inicio) / 1_000_000;
```

`microtime(true)` también sirve (segundos con decimales).

---

## 4. Elementos / características

### Qué guardar en la base de datos

- Usa el formato ISO: `Y-m-d` para fechas y `Y-m-d H:i:s` para fecha y hora.
- Lo más seguro es guardar en **UTC** y convertir a la zona del usuario al mostrar.
- Los campos HTML envían: `<input type="date">` → `2026-10-10`; `<input type="datetime-local">` → `2026-10-10T14:30`.

### `DateTime` frente a `DateTimeImmutable`

| | `DateTime` | `DateTimeImmutable` |
|---|---|---|
| `modify`, `add`, `sub`, `setTime`... | **Modifican** el objeto | Devuelven **uno nuevo** |
| Riesgo | Efectos inesperados si lo pasas a funciones | Ninguno |

---

## 5. Ejemplos prácticos

### Ejemplo básico: calcular la edad

```php
function calcularEdad(string $fechaNacimiento): int
{
    return (new DateTimeImmutable($fechaNacimiento))
        ->diff(new DateTimeImmutable("today"))
        ->y;
}

echo calcularEdad("2005-03-15");
```

### Ejemplo habitual: días que faltan para una fecha

```php
$hoy     = new DateTimeImmutable("today");
$evento  = new DateTimeImmutable("2026-12-25");

$dif = $hoy->diff($evento);
$dias = $dif->invert ? -$dif->days : $dif->days;

echo $dias >= 0 ? "Faltan $dias días" : "Pasó hace " . abs($dias) . " días";
```

---

## 6. Buenas prácticas

- Usa `DateTimeImmutable` en lugar de `date()` y `strtotime()` cuando operes con fechas.
- Configura la zona horaria una sola vez.
- Guarda fechas en ISO y, si puedes, en UTC.
- Valida las fechas del usuario con `createFromFormat` y comprobando el resultado.
- No uses decimales (`float`) para dinero: usa céntimos.
- No compares decimales con `===`.
- Usa `random_int` y `random_bytes` para cualquier cosa de seguridad.

---

## 7. Diferencias importantes

| Comparación | Diferencia |
|---|---|
| `round` vs `floor` vs `ceil` | Redondea al más cercano / hacia abajo / hacia arriba |
| `/` vs `intdiv` | `/` puede dar decimales; `intdiv` siempre un entero |
| `%` vs `fmod` | `%` trabaja con enteros; `fmod` con decimales |
| `rand` / `mt_rand` vs `random_int` | Los dos primeros no son seguros |
| `date()` vs `DateTimeImmutable::format()` | Usan las mismas letras; el objeto permite operar con fechas |

---

## 8. Casos especiales

### Sumar meses al final de mes

```php
$f = new DateTimeImmutable("2026-01-31");
echo $f->add(new DateInterval("P1M"))->format("Y-m-d");   // 2026-03-03 (febrero no tiene 31)
echo $f->modify("last day of next month")->format("Y-m-d");   // 2026-02-28
```

Si quieres "el mismo día del mes siguiente, o el último si no existe", usa `last day of next month` cuando el día sea mayor que el último del mes.

### Cambio de hora (horario de verano)

`+1 day` mantiene la misma hora en el reloj; `+24 hours` suma exactamente 24 horas. Los días en los que cambia la hora, no son lo mismo.

### Formatos ambiguos en `strtotime`

```php
strtotime("10/11/2026");   // se interpreta como mes/día → 11 de octubre
strtotime("10-11-2026");   // se interpreta como día-mes → 10 de noviembre
```

Con texto del usuario, usa mejor `createFromFormat` con un formato explícito.

### Timestamps de JavaScript

JavaScript usa **milisegundos**; PHP usa **segundos**. Multiplica o divide por 1000.

### Zonas horarias disponibles

```php
DateTimeZone::listIdentifiers();   // ["Africa/Abidjan", ..., "Europe/Madrid", ...]
```

### Enteros grandes

Si `PHP_INT_MAX + 1`, el resultado es `float` y pierde precisión. Para identificadores muy grandes (por ejemplo de APIs) usa texto.

---

## 9. Resumen

- `round`, `floor`, `ceil`, `intdiv` y `%` cubren el cálculo básico; `round` y compañía devuelven `float`.
- Los decimales son aproximados: no los compares con `===` y usa céntimos para dinero.
- `random_int` y `random_bytes` son los aleatorios seguros.
- `date()` formatea timestamps; los nombres salen en inglés (usa `IntlDateFormatter` para español).
- `DateTimeImmutable`, `DateInterval`, `diff` y `DatePeriod` cubren sumar, restar, comparar y recorrer fechas.
- Valida fechas con `createFromFormat` y guarda en formato ISO, mejor en UTC.