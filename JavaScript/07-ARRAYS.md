# 07 - Arrays

> [!info] ¿Qué es un array?
> Un array es una **lista ordenada de valores** guardada en una sola variable. Cada valor tiene una posición (un **índice**) que empieza en **0**. Sirve para manejar conjuntos de datos: una lista de nombres, de precios, de tareas, etc.

---

## 1. Antes de empezar

Conviene haber visto [[02 - Variables y tipos]], [[05 - Bucles]] y [[06 - Funciones]], porque los arrays se recorren con bucles y casi todos sus métodos reciben funciones (callbacks).

---

## 2. Concepto fundamental

Imagina una fila de casillas numeradas, empezando por la 0. En cada casilla guardas un valor.

```
índice:   0        1        2
valor:  "rojo"  "verde"  "azul"
```

Características principales:

- Es **ordenado**: cada elemento tiene su sitio.
- Es **dinámico**: puede crecer o encogerse.
- Puede mezclar **cualquier tipo** de datos (aunque es mejor que todos sean del mismo tipo).
- Es un **tipo de referencia** (un objeto especial): al copiarlo, ambas variables apuntan a la misma lista (ver [[02 - Variables y tipos]]).

---

## 3. Sintaxis / estructura

### Crear un array

```js
const colores = ["rojo", "verde", "azul"];
const vacio = [];
const mezcla = [1, "hola", true, null];
```

### Acceder a un elemento

```js
colores[0];   // "rojo"
colores[2];   // "azul"
colores[5];   // undefined (no existe)
```

### Modificar un elemento

```js
colores[1] = "amarillo";
```

### Longitud

```js
colores.length;   // 3
```

### Último elemento

```js
colores[colores.length - 1];   // "azul"
colores.at(-1);                // "azul" (forma moderna)
```

---

## 4. Elementos / propiedades / características

### Añadir y quitar elementos

| Método | Qué hace | Modifica el original |
|--------|----------|---------------------|
| `push(x)` | Añade al **final** | Sí |
| `pop()` | Quita el **último** y lo devuelve | Sí |
| `unshift(x)` | Añade al **principio** | Sí |
| `shift()` | Quita el **primero** y lo devuelve | Sí |
| `splice(i, n, ...x)` | Quita o inserta en cualquier posición | Sí |

```js
const lista = [1, 2, 3];
lista.push(4);      // [1, 2, 3, 4]
lista.pop();        // [1, 2, 3]
lista.unshift(0);   // [0, 1, 2, 3]
lista.shift();      // [1, 2, 3]
```

`splice(posición, cuántos borrar, ...nuevos)`:

```js
const letras = ["a", "b", "c", "d"];
letras.splice(1, 2);          // quita "b" y "c" → ["a", "d"]
letras.splice(1, 0, "x");     // inserta "x" en la posición 1
```

### Copiar y cortar

| Método | Qué hace |
|--------|----------|
| `slice(inicio, fin)` | Devuelve una **copia** de un trozo (el `fin` no se incluye) |
| `concat(otro)` | Une arrays y devuelve uno nuevo |
| `[...a]` | Copia superficial con el operador spread |

```js
const nums = [1, 2, 3, 4];
nums.slice(1, 3);     // [2, 3]
[...nums, 5, 6];      // [1, 2, 3, 4, 5, 6]
```

### Buscar

| Método | Qué devuelve |
|--------|--------------|
| `indexOf(x)` | Posición del elemento, o `-1` si no está |
| `includes(x)` | `true` o `false` |
| `find(fn)` | **Primer elemento** que cumple la condición |
| `findIndex(fn)` | Posición del primero que cumple |
| `some(fn)` | `true` si **alguno** cumple |
| `every(fn)` | `true` si **todos** cumplen |

```js
const edades = [12, 18, 25];
edades.includes(18);            // true
edades.find(e => e > 15);       // 18
edades.some(e => e < 15);       // true
edades.every(e => e >= 18);     // false
```

### Recorrer

```js
const frutas = ["pera", "uva"];

for (const fruta of frutas) {        // bucle for...of
  console.log(fruta);
}

frutas.forEach(f => console.log(f)); // método forEach
```

### Transformar: `map`, `filter` y `reduce`

Son los tres métodos más importantes. **No modifican el original**; devuelven un array o valor nuevo.

**`map`**: transforma cada elemento y devuelve un array del mismo tamaño.

```js
[1, 2, 3].map(n => n * 2);   // [2, 4, 6]
```

**`filter`**: se queda solo con los que cumplen la condición.

```js
[1, 2, 3, 4].filter(n => n % 2 === 0);   // [2, 4]
```

**`reduce`**: combina todos los elementos en **un solo valor**.

```js
[1, 2, 3].reduce((total, n) => total + n, 0);   // 6
```

El `0` es el valor inicial del acumulador.

### Ordenar

```js
const nombres = ["Luis", "Ana", "Carlos"];
nombres.sort();      // ["Ana", "Carlos", "Luis"]
nombres.reverse();   // da la vuelta al orden
```

> [!warning] Cuidado con `sort()` y los números
> Por defecto ordena **como texto**, así que `[10, 9, 1].sort()` da `[1, 10, 9]`. Para números, pasa una función de comparación:
>
> ```js
> [10, 9, 1].sort((a, b) => a - b);   // [1, 9, 10]
> ```

### Convertir entre texto y array

```js
["a", "b", "c"].join("-");    // "a-b-c"
"a-b-c".split("-");           // ["a", "b", "c"]
```

### Desestructuración

Sacar valores del array a variables sueltas.

```js
const [primero, segundo] = ["rojo", "verde"];
```

### Spread (`...`)

Expande un array en sus elementos individuales.

```js
const a = [1, 2];
const b = [...a, 3, 4];   // [1, 2, 3, 4]
```

### Comprobar si es un array

```js
Array.isArray([1, 2]);   // true
typeof [1, 2];           // "object" (no sirve)
```

### Arrays multidimensionales

Arrays dentro de arrays, como una tabla.

```js
const tabla = [
  [1, 2],
  [3, 4]
];
tabla[1][0];   // 3
```

### Aplanar

```js
[1, [2, 3]].flat();   // [1, 2, 3]
```

---

## 5. Ejemplos prácticos

### Ejemplo básico

```js
const frutas = ["manzana", "pera"];
frutas.push("uva");
console.log(frutas.length);   // 3
```

### Ejemplo habitual: filtrar y transformar

```js
const productos = [
  { nombre: "Camiseta", precio: 20 },
  { nombre: "Pantalón", precio: 40 },
  { nombre: "Calcetines", precio: 5 }
];

const baratos = productos.filter(p => p.precio < 30);
const nombres = baratos.map(p => p.nombre);

console.log(nombres);   // ["Camiseta", "Calcetines"]
```

### Ejemplo: sumar con `reduce`

```js
const total = productos.reduce((suma, p) => suma + p.precio, 0);   // 65
```

---

## 6. Buenas prácticas

- **Usa `const`** para los arrays: puedes modificar su contenido igualmente.
- **Prefiere `map`, `filter` y `reduce`** a bucles manuales cuando transformes datos.
- **Evita modificar el array original** si no hace falta; crea uno nuevo con `map`, `filter` o spread.
- **Mete datos del mismo tipo** en un mismo array.
- **Usa `includes`** en vez de `indexOf(...) !== -1`.
- **Usa `at(-1)`** para el último elemento.
- **Pasa siempre una función de comparación a `sort`** cuando ordenes números.
- **Usa nombres en plural** (`usuarios`, `precios`) para que se entienda que es una lista.
- **No uses `for...in`** para recorrer arrays; usa `for...of`.

---

## 7. Diferencias importantes

### Métodos que modifican vs métodos que devuelven uno nuevo

| Modifican el original | Devuelven uno nuevo |
|-----------------------|---------------------|
| `push`, `pop`, `shift`, `unshift` | `map`, `filter`, `slice` |
| `splice`, `sort`, `reverse` | `concat`, `flat`, spread `[...a]` |

### `slice` vs `splice`

- `slice`: **copia** un trozo, no toca el original.
- `splice`: **quita o inserta** en el propio array.

### `map` vs `forEach`

- `map` devuelve un **array nuevo** con los resultados.
- `forEach` solo recorre y **no devuelve nada**.

### `find` vs `filter`

- `find`: **un solo elemento** (el primero que cumple).
- `filter`: **un array** con todos los que cumplen.

### Copiar vs asignar

```js
const a = [1, 2];
const b = a;        // MISMA lista (referencia)
const c = [...a];   // COPIA independiente
```

---

## 8. Casos especiales

### Copia superficial

El spread y `slice` copian solo el primer nivel. Si el array contiene objetos o arrays, esos siguen compartidos. Para una copia profunda:

```js
const copia = structuredClone(original);
```

### Posiciones vacías

Asignar a una posición muy lejana crea huecos:

```js
const a = [1];
a[3] = 4;   // [1, <2 empty items>, 4]
```

### `length` se puede modificar

```js
const a = [1, 2, 3, 4];
a.length = 2;   // [1, 2]
```

### Comparar arrays

Se comparan por referencia, no por contenido:

```js
[1, 2] === [1, 2];   // false
```

### Recorrer con índice y valor

```js
for (const [i, valor] of lista.entries()) { }
```

### `sort` y `reverse` modifican el original

Si no quieres alterarlo, copia primero o usa las versiones modernas `toSorted()` y `toReversed()`.

### Parar un `forEach`

No se puede con `break`. Usa `for...of`, `some` o `every`.

### `arguments` y otros "casi arrays"

Algunos objetos (como las colecciones del DOM) parecen arrays pero no lo son. Se convierten con `Array.from()` o `[...coleccion]`.

---

## 9. Resumen

- Un **array** es una lista ordenada; el primer elemento está en el índice **0**.
- Se crea con `[ ]`; se accede con `lista[i]`; su tamaño es `lista.length`.
- **Añadir/quitar**: `push`, `pop`, `unshift`, `shift`, `splice`.
- **Buscar**: `includes`, `indexOf`, `find`, `findIndex`, `some`, `every`.
- **Transformar**: `map` (cambia cada elemento), `filter` (selecciona), `reduce` (combina en un valor).
- **Copiar/cortar**: `slice`, `concat`, spread `[...a]`.
- `sort()` ordena como texto por defecto: con números, usa `(a, b) => a - b`.
- `join` y `split` convierten entre array y texto.
- Algunos métodos **modifican** el original (`push`, `splice`, `sort`...) y otros **devuelven uno nuevo** (`map`, `filter`, `slice`...).
- Los arrays se copian por **referencia**: usa `[...a]` para una copia real.

---

⬅️ [[06 - Funciones]] | ➡️ [[08 - Objetos]]