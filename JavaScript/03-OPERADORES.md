# 03 - Operadores

> [!info] ¿Qué es un operador?
> Un operador es un **símbolo que hace una operación** con uno o más valores: sumar, comparar, combinar condiciones, asignar un valor, etc. Los valores sobre los que actúa se llaman **operandos**. En `5 + 3`, el operador es `+` y los operandos son `5` y `3`.

---

## 1. Antes de empezar

Conviene haber visto [[02 - Variables y tipos]], sobre todo los tipos de datos y los valores *truthy* y *falsy*, porque los operadores trabajan con ellos.

---

## 2. Concepto fundamental

Los operadores se agrupan según lo que hacen:

| Grupo | Para qué sirve |
|-------|----------------|
| Aritméticos | Cálculos matemáticos |
| De asignación | Guardar un valor en una variable |
| De comparación | Comparar dos valores (da `true` o `false`) |
| Lógicos | Combinar condiciones |
| De texto | Unir strings |
| Otros | Ternario, `typeof`, encadenamiento opcional, etc. |

También se pueden clasificar por cuántos operandos usan:

- **Unarios**: uno (`!activo`, `typeof x`).
- **Binarios**: dos (`a + b`).
- **Ternario**: tres (`condicion ? a : b`).

---

## 3. Sintaxis / estructura

```js
resultado = operando1 operador operando2;
```

```js
const suma = 5 + 3;    // 8
```

---

## 4. Elementos / propiedades / características

### Operadores aritméticos

| Operador | Qué hace | Ejemplo | Resultado |
|----------|----------|---------|-----------|
| `+` | Suma | `5 + 3` | `8` |
| `-` | Resta | `5 - 3` | `2` |
| `*` | Multiplica | `5 * 3` | `15` |
| `/` | Divide | `6 / 3` | `2` |
| `%` | Resto de la división | `7 % 3` | `1` |
| `**` | Potencia | `2 ** 3` | `8` |
| `++` | Suma 1 | `x++` | |
| `--` | Resta 1 | `x--` | |

> [!tip] Para qué sirve `%`
> Se usa mucho para saber si un número es par (`n % 2 === 0`) o para repetir ciclos.

### Operadores de asignación

| Operador | Equivale a |
|----------|-----------|
| `=` | Asigna un valor |
| `+=` | `x = x + valor` |
| `-=` | `x = x - valor` |
| `*=` | `x = x * valor` |
| `/=` | `x = x / valor` |
| `%=` | `x = x % valor` |

```js
let puntos = 10;
puntos += 5;   // 15
```

### Operadores de comparación

Siempre devuelven `true` o `false`.

| Operador | Significado |
|----------|-------------|
| `===` | Igual (mismo valor y mismo tipo) |
| `!==` | Distinto (valor o tipo) |
| `==` | Igual, convirtiendo tipos |
| `!=` | Distinto, convirtiendo tipos |
| `>` | Mayor que |
| `<` | Menor que |
| `>=` | Mayor o igual |
| `<=` | Menor o igual |

> [!tip] Usa siempre `===` y `!==`
> Son más seguros porque no convierten los tipos por detrás.

### Operadores lógicos

Sirven para combinar condiciones.

| Operador | Nombre | Devuelve `true` cuando... |
|----------|--------|--------------------------|
| `&&` | Y (AND) | Las dos condiciones son verdaderas |
| `\|\|` | O (OR) | Al menos una es verdadera |
| `!` | NO (NOT) | Invierte el valor |

Tabla de verdad:

| A | B | `A && B` | `A \|\| B` |
|---|---|----------|-----------|
| `true` | `true` | `true` | `true` |
| `true` | `false` | `false` | `true` |
| `false` | `true` | `false` | `true` |
| `false` | `false` | `false` | `false` |

### Cortocircuito

`&&` y `||` no siempre devuelven `true` o `false`: devuelven **uno de los dos valores**, y dejan de evaluar en cuanto saben el resultado.

```js
const nombre = "" || "Invitado";   // "Invitado"
const activo = true && "Sí";       // "Sí"
```

- `||` devuelve el **primer valor truthy** que encuentre.
- `&&` devuelve el **primer valor falsy**, o el último si todos son truthy.

### Operador de coalescencia nula `??`

Devuelve el valor de la derecha **solo si el de la izquierda es `null` o `undefined`**.

```js
const cantidad = 0;
console.log(cantidad || 10);   // 10  (0 es falsy)
console.log(cantidad ?? 10);   // 0   (0 es un valor válido)
```

> [!tip] `??` vs `||`
> Usa `??` cuando valores como `0` o `""` sean válidos. Usa `||` cuando quieras sustituir cualquier valor falsy.

### Operador ternario

Es un `if` en una sola línea.

```js
const mensaje = edad >= 18 ? "Mayor de edad" : "Menor de edad";
```

Se lee: *"si la condición es cierta, usa lo primero; si no, lo segundo"*.

### Encadenamiento opcional `?.`

Evita errores al acceder a algo que podría no existir.

```js
const usuario = {};
console.log(usuario.direccion?.calle);   // undefined (sin error)
```

Sin `?.`, esa línea daría error. Se usa mucho con objetos y datos de APIs.

### Operadores de texto

`+` une textos (concatenación). Las plantillas con acento grave suelen ser más cómodas.

```js
const nombre = "Ana";
console.log("Hola, " + nombre);
console.log(`Hola, ${nombre}`);
```

### Incremento y decremento

`++` y `--` suman o restan 1. Cambia el resultado si van antes o después:

```js
let a = 5;
console.log(a++);   // 5 (devuelve y luego suma)
console.log(a);     // 6

let b = 5;
console.log(++b);   // 6 (suma y luego devuelve)
```

### Operador `typeof`

Devuelve el tipo de un valor como texto (ver [[02 - Variables y tipos]]).

### Precedencia de operadores

Cuando hay varios operadores juntos, JavaScript sigue un orden, como en matemáticas:

1. Paréntesis `( )`
2. `!` y `++` / `--`
3. `**`
4. `*`, `/`, `%`
5. `+`, `-`
6. Comparaciones (`<`, `>`, `===`...)
7. `&&`
8. `||` y `??`
9. Ternario `? :`
10. Asignación (`=`, `+=`...)

> [!tip] Ante la duda, usa paréntesis
> `(a + b) * c` es más claro que depender de la precedencia.

---

## 5. Ejemplos prácticos

### Ejemplo básico

```js
const precio = 20;
const unidades = 3;
const total = precio * unidades;   // 60
```

### Ejemplo habitual: comprobar si puede entrar

```js
const edad = 20;
const tieneEntrada = true;

const puedeEntrar = edad >= 18 && tieneEntrada;
console.log(puedeEntrar);   // true
```

### Ejemplo: valor por defecto

```js
const nombreUsuario = "";
const mostrar = nombreUsuario || "Invitado";   // "Invitado"
```

### Ejemplo: número par o impar

```js
const n = 7;
const tipo = n % 2 === 0 ? "par" : "impar";   // "impar"
```

---

## 6. Buenas prácticas

- **Usa `===` y `!==`**, nunca `==` ni `!=`.
- **Usa paréntesis** para que el orden de las operaciones sea evidente.
- **Usa `??`** cuando `0` o `""` sean valores válidos.
- **Usa `?.`** al acceder a datos que pueden no existir.
- **Evita ternarios anidados**: se vuelven ilegibles; usa un `if`.
- **Usa `+=` y `++`** para escribir menos, pero sin abusar dentro de expresiones complejas.
- **Prefiere plantillas** (`` `${}` ``) para unir textos.

---

## 7. Diferencias importantes

### `==` vs `===`

```js
0 == "";        // true
0 === "";       // false
null == undefined;    // true
null === undefined;   // false
```

`==` convierte los tipos antes de comparar y da resultados poco intuitivos. `===` no convierte nada.

### `||` vs `??`

| Valor de la izquierda | `valor \|\| "x"` | `valor ?? "x"` |
|-----------------------|-----------------|----------------|
| `0` | `"x"` | `0` |
| `""` | `"x"` | `""` |
| `null` | `"x"` | `"x"` |
| `undefined` | `"x"` | `"x"` |

### `+` con números vs con textos

Con números suma; si uno de los operandos es texto, **une**:

```js
2 + 3;      // 5
"2" + 3;    // "23"
```

---

## 8. Casos especiales

### Comparar `NaN`

`NaN` no es igual a nada, ni a sí mismo. Para comprobarlo, usa `Number.isNaN()`:

```js
NaN === NaN;           // false
Number.isNaN(NaN);     // true
```

### Comparar objetos y arrays

Se comparan por **referencia**, no por contenido:

```js
[1, 2] === [1, 2];   // false (son dos arrays distintos)
```

### Comparar textos

Se comparan letra a letra según el orden del alfabeto. Las mayúsculas van antes que las minúsculas.

```js
"a" < "b";   // true
"B" < "a";   // true
```

### Decimales

Los cálculos con decimales pueden dar pequeños errores (ver [[02 - Variables y tipos]]):

```js
0.1 + 0.2 === 0.3;   // false
```

### División entre cero

No da error: `5 / 0` devuelve `Infinity`.

---

## 9. Resumen

- Un **operador** realiza una operación con uno o más **operandos**.
- **Aritméticos**: `+ - * / % **`, y `++` / `--` para sumar o restar 1.
- **Asignación**: `=` y las versiones cortas `+=`, `-=`, `*=`...
- **Comparación**: usa siempre **`===`** y **`!==`**.
- **Lógicos**: `&&` (y), `||` (o), `!` (no); devuelven uno de los valores, no solo `true`/`false`.
- **`??`** da un valor por defecto solo si hay `null` o `undefined`.
- **`?.`** evita errores al acceder a propiedades que pueden no existir.
- El **ternario** `cond ? a : b` es un `if` en una línea.
- La **precedencia** marca el orden de las operaciones; los **paréntesis** lo aclaran.
- `NaN` nunca es igual a nada: usa `Number.isNaN()`.

---

⬅️ [[02 - Variables y tipos]] | ➡️ [[04 - Condicionales]]