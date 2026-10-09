# 02 - Variables y tipos

> [!info] ¿Qué es una variable?
> Una variable es una **caja con nombre** donde guardas un dato para usarlo después. El dato puede ser un número, un texto, una lista, etc. Los **tipos** son las distintas clases de datos que JavaScript sabe manejar.

---

## 1. Antes de empezar

Necesitas haber visto [[01 - Fundamentos]]: saber dónde se escribe JavaScript y cómo mostrar cosas con `console.log()`.

Para probar todo lo de esta nota, usa la consola del navegador (`F12`).

---

## 2. Concepto fundamental

Cuando programas, constantemente necesitas **recordar datos**: el nombre del usuario, el precio de un producto, si ha iniciado sesión... Para eso se crean variables.

Una variable tiene tres cosas:

1. **Un nombre** para encontrarla.
2. **Un valor** que guarda.
3. **Un tipo** de dato (lo decide JavaScript según el valor).

Como JavaScript es de **tipado dinámico**, la misma variable puede guardar un número ahora y un texto después. Es posible, pero no es buena idea hacerlo.

---

## 3. Sintaxis / estructura

### Crear variables

Hay tres palabras para crearlas:

```js
const pi = 3.14;     // valor que NO cambia
let edad = 20;       // valor que SÍ puede cambiar
var antiguo = "a";   // forma antigua (no usar)
```

### Cambiar el valor

```js
let puntos = 0;
puntos = 10;         // ahora vale 10
puntos = puntos + 5; // ahora vale 15
```

### Declarar sin valor

```js
let nombre;          // existe, pero vale undefined
nombre = "Ana";      // ahora ya tiene valor
```

### Declarar varias a la vez

```js
let a = 1, b = 2, c = 3;
```

---

## 4. Elementos / propiedades / características

### `const`, `let` y `var`

| | `const` | `let` | `var` |
|---|---------|-------|-------|
| ¿Se puede cambiar el valor? | No | Sí | Sí |
| ¿Se puede declarar sin valor? | No | Sí | Sí |
| Alcance | Bloque `{ }` | Bloque `{ }` | Función |
| ¿Se recomienda? | **Sí, por defecto** | Sí, si cambia | No |

> [!warning] Obsoleto / legado
> `var` es la forma antigua de crear variables. Tiene comportamientos confusos (se puede redeclarar y su alcance es raro). Hoy se usa **`const`** y, cuando el valor tiene que cambiar, **`let`**.

> [!tip] Regla práctica
> Empieza siempre con `const`. Si luego ves que necesitas cambiar el valor, cámbialo a `let`.

### Alcance (scope)

El **alcance** es la zona del código donde una variable existe.

```js
if (true) {
  let dentro = "hola";
  console.log(dentro);  // funciona
}
console.log(dentro);    // da error: fuera del bloque no existe
```

Con `let` y `const`, la variable solo vive dentro de las llaves `{ }` donde se creó.

### Reglas para los nombres

- Pueden tener letras, números, `_` y `$`.
- No pueden empezar por número.
- No pueden ser palabras reservadas (`if`, `class`, `return`...).
- Se distingue mayúsculas de minúsculas.
- Se escriben en **camelCase**: `nombreCompleto`.
- Las constantes fijas suelen ir en **MAYÚSCULAS**: `MAX_INTENTOS`.

### Los tipos de datos

JavaScript tiene dos grandes grupos.

**Tipos primitivos** (valores simples):

| Tipo | Qué guarda | Ejemplo |
|------|-----------|---------|
| `string` | Texto | `"Hola"` |
| `number` | Números (enteros y decimales) | `42`, `3.14` |
| `boolean` | Verdadero o falso | `true`, `false` |
| `undefined` | Variable sin valor asignado | `undefined` |
| `null` | Ausencia de valor, puesta a propósito | `null` |
| `bigint` | Números enormes | `123n` |
| `symbol` | Identificador único (uso avanzado) | `Symbol("id")` |

**Tipos de referencia** (valores compuestos):

| Tipo | Qué guarda | Se ve en |
|------|-----------|----------|
| `object` | Datos con nombre | [[08 - Objetos]] |
| `array` | Lista ordenada | [[07 - Arrays]] |
| `function` | Código reutilizable | [[06 - Funciones]] |

### Strings (texto)

Se escriben entre comillas simples, dobles o con acento grave (`` ` ``):

```js
const a = "Hola";
const b = 'Hola';
const c = `Hola`;
```

Las comillas con acento grave permiten **plantillas**: meter variables dentro del texto.

```js
const nombre = "Ana";
console.log(`Hola, ${nombre}`);   // Hola, Ana
```

También permiten texto en varias líneas.

### Numbers (números)

Todos los números, enteros o decimales, son del mismo tipo `number`.

```js
const entero = 10;
const decimal = 3.5;
```

Hay dos valores especiales:

- `NaN`: "no es un número" (resultado de una operación imposible, como `"hola" * 2`).
- `Infinity`: infinito (por ejemplo `1 / 0`).

> [!warning] Cuidado con los decimales
> `0.1 + 0.2` da `0.30000000000000004`, no `0.3`. Es normal en casi todos los lenguajes. Para dinero, trabaja con céntimos (enteros) o redondea con `toFixed(2)`.

### Booleans

Solo dos valores: `true` y `false`. Se usan para tomar decisiones (ver [[04 - Condicionales]]).

### `undefined` y `null`

| | Significado | Quién lo pone |
|---|------------|---------------|
| `undefined` | "Todavía no tiene valor" | JavaScript automáticamente |
| `null` | "Está vacío a propósito" | Tú, de forma intencionada |

### Saber el tipo: `typeof`

```js
typeof "hola";     // "string"
typeof 42;         // "number"
typeof true;       // "boolean"
typeof undefined;  // "undefined"
typeof null;       // "object"  ← error histórico del lenguaje
typeof [];         // "object"  (los arrays son objetos)
```

### Conversión de tipos

A veces hay que convertir un dato a otro tipo.

```js
Number("42");      // 42
String(42);        // "42"
Boolean(0);        // false
parseInt("42px");  // 42
parseFloat("3.5"); // 3.5
```

JavaScript también convierte **solo** en algunas operaciones (conversión implícita), y a veces da sorpresas:

```js
"5" + 3;   // "53"  (une como texto)
"5" - 3;   // 2     (resta como número)
```

### Valores "falsy" y "truthy"

En una condición, JavaScript trata algunos valores como **falsos**:

`false`, `0`, `""` (texto vacío), `null`, `undefined`, `NaN`.

Todo lo demás se considera **verdadero**.

---

## 5. Ejemplos prácticos

### Ejemplo básico

```js
const nombre = "Ana";
let edad = 20;

edad = 21;
console.log(nombre, edad);   // Ana 21
```

### Ejemplo habitual: datos de un usuario

```js
const nombre = "Luis";
const edad = 25;
const esAdmin = false;

console.log(`${nombre} tiene ${edad} años`);
console.log(typeof esAdmin);   // boolean
```

### Ejemplo: convertir lo que escribe el usuario

Lo que se lee de un formulario siempre llega como **texto**, aunque sea un número.

```js
const texto = "15";
const numero = Number(texto);
console.log(numero + 5);   // 20
```

---

## 6. Buenas prácticas

- **Usa `const` por defecto** y `let` solo cuando el valor cambie.
- **No uses `var`.**
- **Pon nombres que se entiendan** (`precioTotal`, no `x`).
- **Una variable, un propósito**: no cambies el tipo de dato que guarda.
- **Declara la variable cerca de donde la usas.**
- **Usa plantillas** (`` `${}` ``) en vez de unir textos con `+`.
- **Convierte los datos explícitamente** (`Number()`) en vez de confiar en la conversión automática.

---

## 7. Diferencias importantes

### `==` vs `===`

Aunque se ve a fondo en [[03 - Operadores]], conviene saberlo ya: `===` compara valor **y tipo** (recomendado); `==` convierte antes de comparar y puede dar sorpresas.

```js
5 === "5";   // false
5 == "5";    // true
```

### `null` vs `undefined`

`undefined` lo pone JavaScript; `null` lo pones tú. Ambos significan "sin valor", pero con intención distinta.

### Primitivos vs referencia

- Los **primitivos** se copian por valor: si copias una variable, la copia es independiente.
- Los **objetos y arrays** se copian por referencia: ambas variables apuntan al mismo dato.

```js
let a = 5;
let b = a;
b = 10;
console.log(a);   // 5 (no cambia)

const lista1 = [1, 2];
const lista2 = lista1;
lista2.push(3);
console.log(lista1);   // [1, 2, 3] (cambia también)
```

---

## 8. Casos especiales

### `const` con objetos y arrays

`const` impide **reasignar** la variable, pero no impide **modificar el contenido** de un objeto o array.

```js
const lista = [1, 2];
lista.push(3);      // permitido
lista = [5];        // error: no se puede reasignar
```

### Variables sin declarar

Si usas un nombre sin `let` o `const`, JavaScript puede crear una variable global sin avisar. En modo estricto da error, que es lo deseable.

### Hoisting

JavaScript "sube" las declaraciones al principio de su zona antes de ejecutar. Con `var` esto da `undefined`; con `let` y `const` da error si las usas antes de declararlas. Otra razón para evitar `var`.

### Variables globales

Una variable creada fuera de cualquier función o bloque es **global** y accesible desde todo el código. Evita crearlas sin necesidad: pueden pisarse entre sí.

---

## 9. Resumen

- Una **variable** guarda un dato con un nombre.
- Usa **`const`** por defecto, **`let`** si el valor cambia, y **no uses `var`**.
- `let` y `const` solo existen dentro del **bloque `{ }`** donde se crean.
- Tipos primitivos: `string`, `number`, `boolean`, `undefined`, `null`, `bigint`, `symbol`.
- Tipos de referencia: objetos, arrays y funciones.
- `typeof` dice el tipo de un valor (ojo: `typeof null` da `"object"`).
- Usa plantillas con `` `${}` `` para meter variables en un texto.
- Convierte tipos con `Number()`, `String()` y `Boolean()`.
- Son falsy: `false`, `0`, `""`, `null`, `undefined`, `NaN`.
- `const` no deja reasignar, pero sí modificar el contenido de objetos y arrays.

---

⬅️ [[01 - Fundamentos]] | ➡️ [[03 - Operadores]]