# 04 - Condicionales

> [!info] ¿Qué es?
> Un **condicional** permite que el programa tome decisiones: ejecuta un bloque de código solo si se cumple una condición.

---

## 1. Antes de empezar

Debes conocer operadores de comparación y lógicos (nota [[PHP/03 - Operadores|03 - Operadores]]).

---

## 2. Concepto fundamental

Se evalúa una condición. Si es `true` (o "truthy"), se ejecuta un bloque; si no, se ejecuta otro o ninguno.

---

## 3. Sintaxis / estructura

### `if`, `elseif` y `else`

```php
if ($nota >= 9) {
    echo "Sobresaliente";
} elseif ($nota >= 5) {
    echo "Aprobado";
} else {
    echo "Suspenso";
}
```

- `elseif` y `else if` funcionan igual en llaves.
- Si el bloque tiene una sola línea, las llaves son opcionales, pero **úsalas siempre**.

### `switch`

```php
switch ($dia) {
    case "sabado":
    case "domingo":
        echo "Fin de semana";
        break;
    case "lunes":
        echo "Vuelta al trabajo";
        break;
    default:
        echo "Día normal";
}
```

- `switch` compara con `==` (no estricto).
- Sin `break`, el código **sigue** ejecutando los siguientes `case`.

### `match` (PHP 8)

```php
$texto = match ($codigo) {
    200, 201 => "Correcto",
    404      => "No encontrado",
    500      => "Error del servidor",
    default  => "Desconocido",
};
```

- Compara con `===` (estricto).
- **Devuelve un valor**.
- No necesita `break`.
- Si no hay coincidencia ni `default`, lanza un error (`UnhandledMatchError`).

### Ternario

```php
$estado = $activo ? "Activo" : "Inactivo";
```

---

## 4. Elementos / características

### Sintaxis alternativa (para plantillas HTML)

```php
<?php if ($usuario): ?>
    <p>Hola, <?= htmlspecialchars($usuario) ?></p>
<?php else: ?>
    <p>Inicia sesión</p>
<?php endif; ?>
```

Existen también `endwhile`, `endfor`, `endforeach` y `endswitch`.

### Combinar condiciones

```php
if ($edad >= 18 && $tieneCarnet) { ... }
if ($rol === "admin" || $rol === "editor") { ... }
if (!$activo) { ... }
```

### `match (true)`

Truco para usar rangos:

```php
$nivel = match (true) {
    $puntos >= 90 => "A",
    $puntos >= 70 => "B",
    default       => "C",
};
```

---

## 5. Ejemplos prácticos

### Ejemplo básico

```php
$hora = (int) date("H");

if ($hora < 12) {
    echo "Buenos días";
} elseif ($hora < 20) {
    echo "Buenas tardes";
} else {
    echo "Buenas noches";
}
```

### Ejemplo habitual: salida temprana (early return)

```php
function obtenerPrecio(?array $producto): float {
    if ($producto === null) {
        return 0.0;
    }
    if (!isset($producto["precio"])) {
        return 0.0;
    }
    return (float) $producto["precio"];
}
```

---

## 6. Buenas prácticas

- Usa siempre llaves `{}`.
- Prefiere `match` a `switch` cuando devuelves un valor.
- Prefiere **salidas tempranas** a muchos `if` anidados.
- No encadenes ternarios.
- Escribe condiciones positivas y claras.
- Usa `===` en las comparaciones.

---

## 7. Diferencias importantes

| | `switch` | `match` |
|---|---|---|
| Comparación | `==` (flexible) | `===` (estricta) |
| Devuelve valor | No | Sí |
| Necesita `break` | Sí | No |
| Sin coincidencia | Sigue en silencio | Lanza error |

---

## 8. Casos especiales

### Asignar dentro de una condición

```php
if ($usuario = buscarUsuario($id)) { ... }
```

Funciona, pero se confunde con `==`. Es más claro asignar antes.

### Condiciones con valores "falsy"

```php
if ($cantidad) { ... }   // falla con 0
if ($cantidad !== null) { ... }   // más seguro
```

### Comprobar existencia

```php
if (isset($_POST["nombre"]) && $_POST["nombre"] !== "") { ... }
```

---

## 9. Resumen

- `if / elseif / else` es el condicional básico.
- `switch` compara con `==` y necesita `break`.
- `match` es estricto, devuelve un valor y no necesita `break`.
- El ternario es un `if` en una línea; no lo encadenes.
- La sintaxis alternativa (`endif`) es cómoda en plantillas.
- Usa llaves y salidas tempranas para código claro.