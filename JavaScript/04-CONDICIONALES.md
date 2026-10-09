# 04 - Condicionales

> [!info] ¿Qué es un condicional?
> Un condicional es una instrucción que le dice al programa: **"si pasa esto, haz aquello; si no, haz otra cosa"**. Sirve para que el código tome decisiones según los datos que tenga en cada momento.

---

## 1. Antes de empezar

Conviene haber visto:

- [[02 - Variables y tipos]]: sobre todo los valores *truthy* y *falsy*.
- [[03 - Operadores]]: los de comparación (`===`, `>`...) y los lógicos (`&&`, `||`, `!`), porque son los que forman las condiciones.

---

## 2. Concepto fundamental

Una **condición** es una expresión que da como resultado `true` (verdadero) o `false` (falso).

```js
edad >= 18      // true o false según el valor de edad
```

El condicional mira esa condición y decide qué trozo de código ejecutar.

Es como una bifurcación en un camino:

- Si la condición es **verdadera** → se toma un camino.
- Si es **falsa** → se toma otro (o ninguno).

---

## 3. Sintaxis / estructura

### `if`

Ejecuta el código solo si la condición es verdadera.

```js
if (condicion) {
  // código si es verdadera
}
```

### `if...else`

Añade un camino alternativo para cuando es falsa.

```js
if (condicion) {
  // si es verdadera
} else {
  // si es falsa
}
```

### `if...else if...else`

Para comprobar varias condiciones, una detrás de otra.

```js
if (condicion1) {
  // si se cumple la 1
} else if (condicion2) {
  // si no la 1, pero sí la 2
} else {
  // si no se cumple ninguna
}
```

Se evalúan **en orden** y, en cuanto una es verdadera, se ejecuta su bloque y se ignora el resto.

### `switch`

Compara **un valor** con varios casos posibles.

```js
switch (valor) {
  case "a":
    // si valor es "a"
    break;
  case "b":
    // si valor es "b"
    break;
  default:
    // si no coincide con ninguno
}
```

### Operador ternario

Un `if...else` en una sola línea (visto en [[03 - Operadores]]).

```js
const resultado = condicion ? valorSiVerdadera : valorSiFalsa;
```

---

## 4. Elementos / propiedades / características

### La condición

Va siempre entre paréntesis `( )`. Puede ser:

- Una comparación: `edad >= 18`
- Varias unidas con lógicos: `edad >= 18 && tieneCarnet`
- Un valor suelto, que JavaScript trata como *truthy* o *falsy*: `if (nombre)`

### Las llaves `{ }`

Delimitan el bloque de código que se ejecuta. Si el bloque tiene una sola instrucción se pueden omitir, pero **es mejor ponerlas siempre**.

### `else if` y `else`

- `else if`: se puede poner todas las veces que quieras.
- `else`: es opcional y siempre va al final. Recoge todo lo que no cumplió ninguna condición.

### `break` en el `switch`

Sin `break`, JavaScript sigue ejecutando los casos siguientes aunque no coincidan (*fall-through*).

```js
switch (dia) {
  case "sábado":
  case "domingo":
    console.log("Fin de semana");   // vale para los dos casos
    break;
  default:
    console.log("Entre semana");
}
```

Aquí el *fall-through* es útil a propósito: agrupa varios casos con la misma respuesta.

### `default`

Es el caso por defecto del `switch`, equivalente al `else`. Es opcional.

### Comparación en el `switch`

El `switch` compara con `===` (valor y tipo), así que `"5"` y `5` no coinciden.

### Condiciones *truthy* y *falsy*

No hace falta comparar con `true` o `false`; basta con poner el valor:

```js
if (nombre) {
  // se ejecuta si nombre NO es "", null, undefined, 0 o NaN
}
```

---

## 5. Ejemplos prácticos

### Ejemplo básico: `if...else`

```js
const edad = 20;

if (edad >= 18) {
  console.log("Eres mayor de edad");
} else {
  console.log("Eres menor de edad");
}
```

### Ejemplo habitual: varias opciones con `else if`

```js
const nota = 7;

if (nota >= 9) {
  console.log("Sobresaliente");
} else if (nota >= 7) {
  console.log("Notable");
} else if (nota >= 5) {
  console.log("Aprobado");
} else {
  console.log("Suspenso");
}
```

### Ejemplo: `switch`

```js
const dia = 3;
let nombreDia;

switch (dia) {
  case 1:
    nombreDia = "Lunes";
    break;
  case 2:
    nombreDia = "Martes";
    break;
  case 3:
    nombreDia = "Miércoles";
    break;
  default:
    nombreDia = "Otro día";
}
```

### Ejemplo: ternario

```js
const mensaje = edad >= 18 ? "Puedes entrar" : "No puedes entrar";
```

---

## 6. Buenas prácticas

- **Pon siempre las llaves `{ }`**, aunque solo haya una instrucción.
- **Usa `===`** en las comparaciones.
- **Ordena los `else if` de lo más específico a lo más general** (importa el orden).
- **Evita anidar muchos `if` unos dentro de otros**: se vuelve difícil de leer.
- **Usa "retorno temprano"** dentro de funciones para evitar anidación (ver abajo).
- **Usa `switch`** cuando compares un mismo valor con muchos casos; usa `if` para el resto.
- **No olvides el `break`** en el `switch`, salvo que quieras agrupar casos a propósito.
- **Usa el ternario solo para casos simples**; para algo complejo, usa `if`.

### Retorno temprano

En vez de anidar:

```js
function entrar(edad, tieneEntrada) {
  if (edad >= 18) {
    if (tieneEntrada) {
      return "Puedes pasar";
    }
  }
  return "No puedes pasar";
}
```

Es más claro cortar pronto los casos que no valen:

```js
function entrar(edad, tieneEntrada) {
  if (edad < 18) return "No puedes pasar";
  if (!tieneEntrada) return "No puedes pasar";
  return "Puedes pasar";
}
```

(Las funciones se ven en [[06 - Funciones]].)

---

## 7. Diferencias importantes

### `if` vs `switch`

| | `if` | `switch` |
|---|------|----------|
| Qué compara | Cualquier condición | Un valor con varios casos exactos |
| Rangos (`>`, `<`) | Sí | No (solo igualdad) |
| Cuándo usarlo | Condiciones variadas o complejas | Muchas opciones para un mismo valor |

### `if` vs ternario

| | `if` | Ternario |
|---|------|----------|
| Ejecuta bloques de código | Sí | No, solo devuelve un valor |
| Legibilidad con lógica larga | Mejor | Peor |
| Asignar un valor según condición | Largo | Cómodo |

### `else if` vs varios `if` seguidos

```js
// Con else if: solo se ejecuta UNO
if (x > 10) { ... } else if (x > 5) { ... }

// Con if separados: pueden ejecutarse VARIOS
if (x > 10) { ... }
if (x > 5) { ... }
```

---

## 8. Casos especiales

### Usar `=` en vez de `===`

Es un fallo muy típico:

```js
if (x = 5) { ... }    // ASIGNA 5 a x, y siempre es verdadero
if (x === 5) { ... }  // COMPARA
```

### Comparar con `true` o `false`

No hace falta:

```js
if (activo === true) { ... }   // innecesario
if (activo) { ... }            // mejor
```

### Condiciones con `0` y texto vacío

`0` y `""` son *falsy*. Si son valores válidos en tu caso, compara explícitamente:

```js
const cantidad = 0;

if (cantidad) { ... }              // NO entra, aunque 0 sea válido
if (cantidad !== undefined) { ... } // sí entra
```

### `switch(true)`

Permite usar rangos dentro de un `switch`, aunque suele ser más claro un `else if`:

```js
switch (true) {
  case nota >= 9: ...
  case nota >= 5: ...
}
```

### Valores por defecto y cortocircuito

Muchas veces no hace falta un `if`:

```js
const nombre = usuario || "Invitado";
```

---

## 9. Resumen

- Un **condicional** ejecuta código solo si se cumple una condición.
- **`if`**: si se cumple. **`else`**: si no. **`else if`**: otra condición alternativa.
- Se evalúan **en orden** y solo se ejecuta el primer bloque cuya condición sea verdadera.
- **`switch`** compara un valor con varios casos exactos; necesita **`break`** y suele llevar **`default`**.
- El **ternario** `cond ? a : b` sirve para asignar un valor según una condición simple.
- Usa **`===`**, no `=`, para comparar.
- Los valores **falsy** (`false`, `0`, `""`, `null`, `undefined`, `NaN`) hacen que la condición sea falsa.
- Pon **siempre llaves** y evita anidar demasiados `if`.
- El **retorno temprano** mantiene el código más limpio.

---

⬅️ [[03 - Operadores]] | ➡️ [[05 - Bucles]]