# 15 - Excepciones

> [!info] ¿Qué es?
> Una **excepción** es un aviso de que algo ha ido mal. En vez de que el programa se rompa, puedes **lanzar** la excepción y **capturarla** para decidir qué hacer.

---

## 1. Antes de empezar

Debes conocer funciones y POO básica (notas [[PHP/06 - Funciones|06]] y [[14 - POO|12]]).

---

## 2. Concepto fundamental

1. En un punto del código ocurre un problema → se **lanza** (`throw`) una excepción.
2. PHP deja de ejecutar el código normal y busca un `catch` que la **capture**.
3. Si nadie la captura, el script termina con un error.

Ventaja: separas **el camino normal** del **tratamiento de errores**.

---

## 3. Sintaxis / estructura

```php
try {
    $resultado = dividir(10, 0);
    echo $resultado;
} catch (DivisionByZeroError $e) {
    echo "No se puede dividir entre cero";
} catch (InvalidArgumentException $e) {
    echo "Argumento no válido: " . $e->getMessage();
} finally {
    echo "Esto se ejecuta siempre";
}
```

- `try`: código que puede fallar.
- `catch`: qué hacer si falla. Puede haber varios.
- `finally`: se ejecuta **siempre** (con o sin error). Sirve para cerrar recursos.

### Lanzar una excepción

```php
function dividir(float $a, float $b): float
{
    if ($b === 0.0) {
        throw new InvalidArgumentException("El divisor no puede ser cero");
    }
    return $a / $b;
}
```

---

## 4. Elementos / características

### Jerarquía

Todo lo que se puede lanzar implementa `Throwable`:

```
Throwable
├── Error                  (errores graves del lenguaje: TypeError, DivisionByZeroError...)
└── Exception              (excepciones "normales")
    ├── LogicException     (errores del programador)
    │   └── InvalidArgumentException, DomainException...
    └── RuntimeException   (errores en ejecución)
        └── UnexpectedValueException, OutOfBoundsException...
```

### Qué métodos tiene una excepción

| Método | Devuelve |
|---|---|
| `getMessage()` | El mensaje |
| `getCode()` | El código |
| `getFile()` / `getLine()` | Dónde ocurrió |
| `getTrace()` / `getTraceAsString()` | La pila de llamadas |
| `getPrevious()` | La excepción anterior (si hay encadenamiento) |

### Excepciones propias

```php
class SaldoInsuficienteException extends Exception
{
    public function __construct(private float $falta)
    {
        parent::__construct("Faltan {$falta} € para completar la operación");
    }

    public function getFalta(): float
    {
        return $this->falta;
    }
}
```

### Capturar varios tipos a la vez

```php
try {
    // ...
} catch (InvalidArgumentException | TypeError $e) {
    // mismo tratamiento
}
```

### Relanzar y encadenar

```php
try {
    $pdo->query($sql);
} catch (PDOException $e) {
    throw new RuntimeException("No se pudo guardar el pedido", 0, $e);
}
```

El tercer parámetro conserva la excepción original (útil para los logs).

### Manejador global

```php
set_exception_handler(function (Throwable $e) {
    error_log($e->getMessage());
    http_response_code(500);
    echo "Ha ocurrido un error. Inténtalo más tarde.";
});
```

---

## 5. Ejemplos prácticos

### Ejemplo básico

```php
try {
    $datos = json_decode("{no es json}", true, 512, JSON_THROW_ON_ERROR);
} catch (JsonException $e) {
    echo "JSON no válido";
}
```

### Ejemplo habitual: transacción con excepciones

```php
try {
    $pdo->beginTransaction();
    // varias operaciones...
    $pdo->commit();
} catch (Throwable $e) {
    $pdo->rollBack();
    error_log($e->getMessage());
    echo "No se pudo completar la operación";
}
```

---

## 6. Buenas prácticas

- Lanza excepciones para situaciones **excepcionales**, no para el flujo normal.
- Captura el tipo **más concreto** posible; evita `catch (Throwable)` salvo en el nivel más alto.
- No dejes un `catch` vacío.
- **Registra** el detalle en un log; al usuario muéstrale un mensaje genérico.
- Crea excepciones propias para errores de tu aplicación.
- Usa `finally` para liberar recursos.
- Conserva la excepción original al relanzar.

---

## 7. Diferencias importantes

| Comparación | Diferencia |
|---|---|
| `Error` vs `Exception` | `Error` son fallos internos del lenguaje; `Exception` son los que lanzas tú |
| Excepción vs `trigger_error` | Las excepciones se capturan con `try/catch`; los errores clásicos no |
| `return` en `finally` | Pisa el `return` del `try`; evítalo |

---

## 8. Casos especiales

### Errores clásicos de PHP (notices, warnings)

Algunas funciones antiguas no lanzan excepciones, sino **avisos**. Puedes convertirlos:

```php
set_error_handler(function (int $nivel, string $mensaje, string $fichero, int $linea) {
    throw new ErrorException($mensaje, 0, $nivel, $fichero, $linea);
});
```

### Configurar la visibilidad de errores

| Entorno | `display_errors` | `log_errors` |
|---|---|---|
| Desarrollo | `1` | `1` |
| Producción | `0` | `1` |

```php
error_reporting(E_ALL);
ini_set("display_errors", "0");
ini_set("log_errors", "1");
ini_set("error_log", __DIR__ . "/errores.log");
```

### Registrar mensajes

```php
error_log("Pago fallido para el pedido 25");
```

### PDO

Para que PDO lance excepciones hay que activarlo (nota 14):

```php
$pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
```

---

## 9. Resumen

- `throw` lanza; `try/catch` captura; `finally` se ejecuta siempre.
- Todo lo lanzable implementa `Throwable`: `Error` y `Exception`.
- Crea tus propias excepciones extendiendo `Exception`.
- Captura tipos concretos, registra el detalle y muestra mensajes genéricos al usuario.
- En producción: errores **ocultos en pantalla** y **guardados en log**.