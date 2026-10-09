# 05 - Bucles

> [!info] ¿Qué es un bucle?
> Un bucle es una instrucción que **repite un trozo de código** varias veces, sin que tengas que escribirlo una y otra vez. Se repite mientras se cumpla una condición o hasta recorrer todos los elementos de una lista.

---

## 1. Antes de empezar

Conviene haber visto:

- [[02 - Variables y tipos]]: los bucles usan variables que cambian en cada vuelta.
- [[03 - Operadores]]: sobre todo `++`, `+=` y los de comparación.
- [[04 - Condicionales]]: la condición de un bucle funciona igual que la de un `if`.

---

## 2. Concepto fundamental

Imagina que quieres mostrar los números del 1 al 100. Escribir 100 `console.log()` sería absurdo. Con un bucle lo haces en tres líneas.

Todo bucle necesita tres cosas:

1. **Un punto de partida**: dónde empieza.
2. **Una condición**: hasta cuándo se repite.
3. **Un cambio en cada vuelta**: algo que acerque el bucle a su final.

Cada repetición se llama **iteración**.

> [!warning] Bucle infinito
> Si la condición **nunca** deja de cumplirse, el bucle no termina y el navegador se queda bloqueado. Siempre asegúrate de que algo cambia en cada vuelta para que acabe.

---

## 3. Sintaxis / estructura

### `for`

El más usado cuando sabes **cuántas veces** repetir.

```js
for (inicio; condicion; cambio) {
  // código que se repite
}
```

```js
for (let i = 0; i < 5; i++) {
  console.log(i);   // 0, 1, 2, 3, 4
}
```

Se lee así:

1. `let i = 0` → empieza con `i` en 0 (solo ocurre una vez).
2. `i < 5` → antes de cada vuelta, comprueba si sigue siendo verdadero.
3. `i++` → al final de cada vuelta, suma 1.

### `while`

Repite **mientras** la condición sea verdadera. Útil cuando no sabes cuántas vueltas harán falta.

```js
while (condicion) {
  // código que se repite
}
```

```js
let n = 0;
while (n < 3) {
  console.log(n);
  n++;
}
```

### `do...while`

Igual que `while`, pero **siempre se ejecuta al menos una vez**, porque la condición se comprueba al final.

```js
do {
  // código
} while (condicion);
```

### `for...of`

Recorre **los valores** de una lista (array, texto...).

```js
for (const elemento of coleccion) {
  // código
}
```

### `for...in`

Recorre **las claves** (nombres de propiedades) de un objeto.

```js
for (const clave in objeto) {
  // código
}
```

---

## 4. Elementos / propiedades / características

### Las tres partes del `for`

| Parte | Qué hace | Ejemplo |
|-------|---------|---------|
| Inicio | Crea la variable contador | `let i = 0` |
| Condición | Decide si hay otra vuelta | `i < 10` |
| Cambio | Actualiza el contador | `i++` |

Las tres son opcionales, pero los `;` hay que ponerlos.

### `break`

Sale del bucle **de inmediato**.

```js
for (let i = 0; i < 10; i++) {
  if (i === 3) break;
  console.log(i);   // 0, 1, 2
}
```

### `continue`

Salta **a la siguiente vuelta**, ignorando el resto del código de esa iteración.

```js
for (let i = 0; i < 5; i++) {
  if (i === 2) continue;
  console.log(i);   // 0, 1, 3, 4
}
```

### Bucles anidados

Un bucle dentro de otro. El interno da todas sus vueltas por cada vuelta del externo.

```js
for (let fila = 1; fila <= 2; fila++) {
  for (let col = 1; col <= 3; col++) {
    console.log(fila, col);
  }
}
```

### Bucles para arrays: métodos modernos

Para recorrer arrays, JavaScript tiene métodos como `forEach`, `map` o `filter` que suelen ser más cómodos que un `for`. Se ven a fondo en [[07 - Arrays]]:

```js
const numeros = [1, 2, 3];
numeros.forEach(n => console.log(n));
```

### Etiquetas (casos avanzados)

Permiten salir de un bucle externo desde uno interno. Se usan muy poco.

---

## 5. Ejemplos prácticos

### Ejemplo básico: contar

```js
for (let i = 1; i <= 5; i++) {
  console.log(i);   // 1, 2, 3, 4, 5
}
```

### Ejemplo habitual: recorrer un array

```js
const frutas = ["manzana", "pera", "uva"];

for (const fruta of frutas) {
  console.log(fruta);
}
```

### Ejemplo: sumar valores

```js
const precios = [10, 20, 30];
let total = 0;

for (const precio of precios) {
  total += precio;
}

console.log(total);   // 60
```

### Ejemplo: recorrer un objeto

```js
const persona = { nombre: "Ana", edad: 20 };

for (const clave in persona) {
  console.log(clave, persona[clave]);
}
```

### Ejemplo: `while` con condición variable

```js
let intentos = 0;

while (intentos < 3) {
  console.log("Intento", intentos + 1);
  intentos++;
}
```

---

## 6. Buenas prácticas

- **Asegúrate de que el bucle termina**: algo debe cambiar en cada vuelta.
- **Usa `for...of`** para recorrer arrays y textos; es más claro que un `for` con índice.
- **Usa `for`** cuando necesites el número de vuelta (`i`) o una cantidad exacta de repeticiones.
- **Usa `while`** cuando no sepas cuántas vueltas habrá.
- **Declara el contador con `let`**, nunca con `var`.
- **Usa nombres claros**: `i` está bien para contadores simples, pero `fruta` es mejor que `x` para elementos.
- **No modifiques un array mientras lo recorres**: puede dar resultados raros.
- **Evita anidar muchos bucles**: se vuelven lentos y difíciles de leer.
- **Prefiere los métodos de arrays** (`map`, `filter`, `forEach`...) cuando encajen con lo que quieres hacer.

---

## 7. Diferencias importantes

### Qué bucle elegir

| Bucle | Úsalo cuando... |
|-------|-----------------|
| `for` | Sabes cuántas veces repetir, o necesitas el índice |
| `while` | No sabes cuántas veces; depende de una condición |
| `do...while` | Debe ejecutarse al menos una vez |
| `for...of` | Recorres valores de un array o texto |
| `for...in` | Recorres las claves de un objeto |

### `for...of` vs `for...in`

```js
const letras = ["a", "b", "c"];

for (const x of letras) console.log(x);   // a, b, c (valores)
for (const x in letras) console.log(x);   // 0, 1, 2 (índices, como texto)
```

> [!warning] No uses `for...in` con arrays
> Recorre índices como texto y puede incluir propiedades que no son elementos. Para arrays, usa `for...of`.

### `while` vs `do...while`

- `while`: comprueba **antes**; puede no ejecutarse nunca.
- `do...while`: comprueba **después**; se ejecuta como mínimo una vez.

### `break` vs `continue`

- `break`: **termina** todo el bucle.
- `continue`: **salta solo esta vuelta** y sigue con la siguiente.

---

## 8. Casos especiales

### Bucle infinito intencionado

A veces se usa a propósito, con una salida con `break`:

```js
while (true) {
  // ...
  if (terminado) break;
}
```

### Contar hacia atrás

```js
for (let i = 5; i > 0; i--) {
  console.log(i);   // 5, 4, 3, 2, 1
}
```

### Saltar de dos en dos

```js
for (let i = 0; i < 10; i += 2) {
  console.log(i);   // 0, 2, 4, 6, 8
}
```

### Recorrer un texto

Un string se puede recorrer con `for...of`, letra a letra:

```js
for (const letra of "hola") {
  console.log(letra);
}
```

### Recorrer con índice y valor a la vez

```js
const colores = ["rojo", "verde"];

for (const [indice, color] of colores.entries()) {
  console.log(indice, color);
}
```

### `break` dentro de `forEach`

No funciona: `forEach` no se puede detener con `break`. Si necesitas parar antes, usa `for...of`.

### `const` en `for...of`

Es válido usar `const` porque en cada vuelta se crea una variable nueva:

```js
for (const x of lista) { ... }   // correcto
```

---

## 9. Resumen

- Un **bucle** repite código; cada repetición es una **iteración**.
- **`for`**: cantidad conocida de vueltas. **`while`**: depende de una condición. **`do...while`**: como `while`, pero al menos una vez.
- **`for...of`** recorre los **valores** de arrays y textos; **`for...in`** recorre las **claves** de un objeto.
- **`break`** sale del bucle; **`continue`** salta a la siguiente vuelta.
- Evita los **bucles infinitos**: algo debe cambiar en cada vuelta.
- Declara los contadores con **`let`**.
- Para recorrer arrays, suele ser mejor `for...of` o los métodos (`forEach`, `map`, `filter`).
- No uses `for...in` con arrays.

---

⬅️ [[04 - Condicionales]] | ➡️ [[06 - Funciones]]