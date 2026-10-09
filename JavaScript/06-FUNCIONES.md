# 06 - Funciones

> [!info] ¿Qué es una función?
> Una función es un **bloque de código con nombre** que puedes ejecutar cuando quieras, las veces que quieras. Puede recibir datos (**parámetros**) y devolver un resultado. Sirve para no repetir código y para organizar el programa en piezas pequeñas.

---

## 1. Antes de empezar

Conviene haber visto [[02 - Variables y tipos]], [[03 - Operadores]], [[04 - Condicionales]] y [[05 - Bucles]], porque dentro de una función se usa todo eso.

---

## 2. Concepto fundamental

Piensa en una función como una **receta**:

- Le das unos **ingredientes** (parámetros).
- Sigue unos **pasos** (el código de dentro).
- Te entrega un **plato** (el valor que devuelve).

Hay dos momentos distintos:

1. **Declarar** la función: escribir la receta.
2. **Llamarla**: ejecutarla de verdad. Si no la llamas, no hace nada.

En JavaScript las funciones son **valores**: se pueden guardar en variables, pasar como argumento a otras funciones y devolver desde otras funciones.

---

## 3. Sintaxis / estructura

### Declaración de función

```js
function saludar(nombre) {
  return `Hola, ${nombre}`;
}

saludar("Ana");   // "Hola, Ana"
```

### Expresión de función

La función se guarda en una variable.

```js
const saludar = function (nombre) {
  return `Hola, ${nombre}`;
};
```

### Función flecha (arrow function)

Forma corta y moderna.

```js
const saludar = (nombre) => {
  return `Hola, ${nombre}`;
};
```

Si solo hay una expresión, se puede quitar `{ }` y `return`:

```js
const saludar = (nombre) => `Hola, ${nombre}`;
```

Si hay un único parámetro, se pueden quitar los paréntesis:

```js
const doble = n => n * 2;
```

---

## 4. Elementos / propiedades / características

### Parámetros y argumentos

- **Parámetros**: los nombres que pones al declarar la función.
- **Argumentos**: los valores reales que pasas al llamarla.

```js
function sumar(a, b) {     // a y b son parámetros
  return a + b;
}

sumar(2, 3);               // 2 y 3 son argumentos
```

### `return`

Devuelve un valor y **termina la función** de inmediato. Lo que haya después no se ejecuta.

Si no pones `return`, la función devuelve `undefined`.

### Valores por defecto

Se usan cuando no se pasa el argumento.

```js
function saludar(nombre = "Invitado") {
  return `Hola, ${nombre}`;
}

saludar();   // "Hola, Invitado"
```

### Parámetros rest (`...`)

Recogen un número variable de argumentos en un array.

```js
function sumarTodo(...numeros) {
  let total = 0;
  for (const n of numeros) total += n;
  return total;
}

sumarTodo(1, 2, 3, 4);   // 10
```

### Alcance (scope) de las funciones

Las variables creadas **dentro** de una función solo existen dentro de ella.

```js
function prueba() {
  const secreto = 42;
}
console.log(secreto);   // error
```

Pero una función **sí puede leer** variables de fuera.

### Funciones como valores

```js
const operacion = (a, b) => a + b;
```

Se pueden pasar a otras funciones:

```js
function ejecutar(fn) {
  return fn(2, 3);
}
ejecutar(operacion);   // 5
```

### Callbacks

Un **callback** es una función que se pasa a otra para que esta la llame en el momento adecuado. Es la base de eventos, `forEach`, `map`, temporizadores, etc.

```js
[1, 2, 3].forEach(n => console.log(n));
```

### Funciones de orden superior

Son las que **reciben o devuelven otras funciones** (como `map`, `filter` o `forEach`; ver [[07 - Arrays]]).

### Closures

Una función **recuerda** las variables del lugar donde fue creada, aunque se ejecute después.

```js
function contador() {
  let cuenta = 0;
  return () => ++cuenta;
}

const siguiente = contador();
siguiente();   // 1
siguiente();   // 2
```

La variable `cuenta` sigue viva y protegida: solo se accede a ella a través de la función devuelta.

### Funciones autoejecutables (IIFE)

Se declaran y ejecutan a la vez. Antes servían para crear un ámbito privado; hoy se usan poco porque los módulos y `let`/`const` lo resuelven.

```js
(function () {
  console.log("Me ejecuto sola");
})();
```

### Recursividad

Una función que **se llama a sí misma**. Necesita siempre un caso final para parar.

```js
function cuentaAtras(n) {
  if (n === 0) return;
  console.log(n);
  cuentaAtras(n - 1);
}
```

### Métodos

Una función guardada dentro de un objeto (ver [[08 - Objetos]]).

```js
const persona = {
  nombre: "Ana",
  saludar() {
    return `Hola, soy ${this.nombre}`;
  }
};
```

---

## 5. Ejemplos prácticos

### Ejemplo básico

```js
function sumar(a, b) {
  return a + b;
}

console.log(sumar(2, 3));   // 5
```

### Ejemplo habitual: función con validación

```js
function dividir(a, b) {
  if (b === 0) {
    return "No se puede dividir entre cero";
  }
  return a / b;
}
```

### Ejemplo: función flecha con arrays

```js
const precios = [10, 20, 30];
const conIva = precios.map(p => p * 1.21);
```

---

## 6. Buenas prácticas

- **Una función, una tarea.** Si hace demasiadas cosas, divídela.
- **Ponle un nombre que diga lo que hace**, empezando por un verbo: `calcularTotal`, `obtenerUsuario`.
- **Usa funciones flecha** para callbacks y funciones cortas.
- **Evita demasiados parámetros** (más de 3 o 4): mejor pasa un objeto.
- **Usa valores por defecto** en vez de comprobar a mano si falta un argumento.
- **Devuelve valores** en lugar de modificar variables externas.
- **Mantén las funciones cortas** y fáciles de leer.
- **Usa retorno temprano** para evitar anidar muchos `if` (ver [[04 - Condicionales]]).
- **Comenta solo lo que no sea evidente.**

---

## 7. Diferencias importantes

### Declaración vs expresión vs flecha

| | Declaración | Expresión | Flecha |
|---|-------------|-----------|--------|
| Sintaxis | `function f() {}` | `const f = function() {}` | `const f = () => {}` |
| Se puede usar antes de declararla | Sí (hoisting) | No | No |
| Tiene su propio `this` | Sí | Sí | No, usa el de fuera |
| Ideal para | Funciones principales | Casos concretos | Callbacks y funciones cortas |

### Parámetro vs argumento

Parámetro = nombre en la **declaración**. Argumento = valor en la **llamada**.

### `return` vs `console.log`

`console.log` solo **muestra** un valor en consola. `return` **devuelve** el valor para poder usarlo en otro sitio.

```js
function a() { console.log(5); }   // muestra, pero no devuelve nada
function b() { return 5; }         // devuelve 5
```

### Función normal vs flecha con `this`

Las flechas **no tienen su propio `this`**: heredan el del lugar donde se crean. Por eso no son buena idea como métodos de objetos.

---

## 8. Casos especiales

### Llamar con más o menos argumentos de los esperados

- Si faltan, el parámetro vale `undefined`.
- Si sobran, se ignoran (salvo que uses `...rest`).

### Devolver varios valores

Una función solo devuelve un valor, pero puede ser un array u objeto:

```js
function minMax(lista) {
  return { min: Math.min(...lista), max: Math.max(...lista) };
}

const { min, max } = minMax([3, 1, 7]);
```

### Funciones flecha que devuelven un objeto

Hay que envolver el objeto entre paréntesis, o JavaScript lo confunde con un bloque:

```js
const crear = () => ({ nombre: "Ana" });
```

### Hoisting

Las **declaraciones** de función se pueden llamar antes de escribirlas en el código. Las **expresiones y flechas** no.

```js
hola();                 // funciona
function hola() { }

adios();                // error
const adios = () => { };
```

### Funciones asíncronas

Con `async`/`await`; se ven en [[12 - Asincronía]].

### Olvidar los paréntesis

`saludar` es la función en sí; `saludar()` la **ejecuta**. Confundirlo es muy típico, sobre todo al pasar callbacks:

```js
boton.addEventListener("click", saludar);     // correcto
boton.addEventListener("click", saludar());   // incorrecto: la ejecuta al momento
```

---

## 9. Resumen

- Una **función** es un bloque de código reutilizable que se **declara** y luego se **llama**.
- Tiene **parámetros** (al declarar) y recibe **argumentos** (al llamar).
- **`return`** devuelve un valor y termina la función; sin él devuelve `undefined`.
- Formas de crearlas: **declaración**, **expresión** y **flecha** `() => {}`.
- Admiten **valores por defecto** (`n = 0`) y **rest** (`...args`).
- Las variables de dentro de una función **no se ven desde fuera**.
- Una función puede pasarse como valor: **callbacks** y **funciones de orden superior**.
- Un **closure** es una función que recuerda las variables de donde nació.
- Las flechas **no tienen su propio `this`**.
- `saludar` es la función; `saludar()` la ejecuta.

---

⬅️ [[05 - Bucles]] | ➡️ [[07 - Arrays]]