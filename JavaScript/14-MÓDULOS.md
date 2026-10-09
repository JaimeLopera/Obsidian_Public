# 14 - Módulos

> [!info] ¿Qué es?
> Un **módulo** es un archivo de JavaScript que guarda su propio código en privado y solo comparte lo que tú decides con `export`. Otros archivos lo usan con `import`. Sirven para dividir un programa grande en piezas pequeñas, ordenadas y reutilizables.

---

## 1. Antes de empezar

Para entender esta nota conviene saber:

- Qué es una **función**, un **objeto** y una **constante** (ver [[06 - Funciones]] y [[08 - Objetos]]).
- Qué es una **Promesa**, porque `import()` dinámico devuelve una (ver [[12 - Asincronía]]).

**Problema que resuelven los módulos:** antes, todos los archivos `.js` compartían las mismas variables globales. Si dos archivos usaban el mismo nombre, se pisaban entre sí. Con módulos, cada archivo tiene su propio espacio y no se mezclan.

---

## 2. Concepto fundamental

Un módulo funciona con tres ideas:

1. **Ámbito propio:** lo que declaras dentro de un módulo (variables, funciones, clases) es **privado** por defecto.
2. **`export`:** marca lo que quieres que otros archivos puedan usar.
3. **`import`:** trae a tu archivo lo que otro módulo exporta.

Además, estas cosas son siempre ciertas en un módulo:

| Característica | Qué significa |
|---|---|
| Ámbito propio | Nada se vuelve global sin que tú lo pidas |
| Modo estricto | Siempre activo, no hace falta escribir `"use strict"` |
| Se ejecuta una sola vez | Aunque lo importen 10 archivos, su código corre una vez y se reutiliza |
| `import` arriba del todo | Los imports se resuelven antes de ejecutar el código |
| `this` es `undefined` | En el nivel superior del módulo, no es `window` |

---

## 3. Sintaxis / estructura

### 3.1 Exportar

**Export con nombre** (puedes tener varios por archivo):

```javascript
// matematicas.js
export const PI = 3.14159;

export function sumar(a, b) {
  return a + b;
}

export class Calculadora {}
```

También puedes exportar todo junto al final:

```javascript
const PI = 3.14159;
function sumar(a, b) {
  return a + b;
}

export { PI, sumar };
```

**Export por defecto** (solo **uno** por archivo):

```javascript
// saludo.js
export default function saludar(nombre) {
  return "Hola, " + nombre;
}
```

### 3.2 Importar

**Import con nombre** (las llaves y el nombre deben coincidir):

```javascript
import { PI, sumar } from "./matematicas.js";
```

**Import por defecto** (sin llaves, y el nombre lo eliges tú):

```javascript
import saludar from "./saludo.js";
```

**Los dos a la vez:**

```javascript
import saludar, { PI } from "./modulo.js";
```

**Renombrar con `as`** (útil si hay choque de nombres):

```javascript
import { sumar as suma } from "./matematicas.js";
export { sumar as add };
```

**Importar todo como un objeto** (espacio de nombres):

```javascript
import * as mates from "./matematicas.js";

mates.sumar(2, 3);
mates.PI;
```

**Import solo por efecto** (ejecuta el archivo, no trae nada):

```javascript
import "./configuracion.js";
```

### 3.3 Usar módulos en el navegador

Hay que decirle al navegador que el script es un módulo:

```html
<script type="module" src="./main.js"></script>
```

---

## 4. Elementos / propiedades / características

### 4.1 Export con nombre vs. export por defecto

| | Con nombre | Por defecto |
|---|---|---|
| Cuántos por archivo | Los que quieras | Solo uno |
| Al importar | `{ nombre }` con llaves | Sin llaves |
| Nombre al importar | Debe coincidir (o usar `as`) | Lo eliges tú |
| Cuándo usarlo | Varias utilidades en un archivo | Un archivo = una cosa principal |

### 4.2 Re-exportar

Sirve para juntar varios módulos en un solo punto de entrada (muy típico en un archivo `index.js`):

```javascript
// utils/index.js
export { sumar } from "./matematicas.js";
export { default as saludar } from "./saludo.js";
export * from "./fechas.js";
```

### 4.3 Import dinámico: `import()`

Carga un módulo **cuando lo necesitas**, no al principio. Devuelve una **Promesa**.

```javascript
const boton = document.querySelector("#grafica");

boton.addEventListener("click", async () => {
  const modulo = await import("./grafica.js");
  modulo.dibujar();
});
```

Se usa para **carga perezosa** (*lazy loading*): la página arranca más rápido porque no descarga código que quizá nunca se use.

### 4.4 `await` en el nivel superior

Dentro de un módulo puedes usar `await` sin necesidad de una función `async`:

```javascript
const respuesta = await fetch("https://api.ejemplo.com/datos");
const datos = await respuesta.json();
```

### 4.5 `import.meta`

Objeto con información del módulo actual. Lo más usado es `import.meta.url`, la dirección del propio archivo.

### 4.6 Enlaces vivos (*live bindings*)

Lo que importas **no es una copia**: es una conexión al valor original. Si el módulo que exporta cambia su variable, tú ves el cambio.

```javascript
// contador.js
export let cuenta = 0;
export function incrementar() {
  cuenta++;
}
```

```javascript
import { cuenta, incrementar } from "./contador.js";

incrementar();
console.log(cuenta); // 1
// cuenta = 5;  → ERROR: no puedes reasignar un import desde fuera
```

---

## 5. Ejemplos prácticos

### 5.1 Ejemplo básico

Dos archivos: uno exporta, otro importa.

```javascript
// utiles.js
export function esPar(numero) {
  return numero % 2 === 0;
}
```

```javascript
// main.js
import { esPar } from "./utiles.js";

console.log(esPar(4)); // true
```

```html
<script type="module" src="./main.js"></script>
```

### 5.2 Ejemplo habitual: organizar un proyecto

```
proyecto/
├── index.html
├── main.js
└── js/
    ├── api.js
    ├── dom.js
    └── index.js   ← junta todo
```

```javascript
// js/api.js
export async function obtenerUsuarios() {
  const respuesta = await fetch("https://jsonplaceholder.typicode.com/users");
  return respuesta.json();
}
```

```javascript
// js/dom.js
export function pintarLista(usuarios, contenedor) {
  contenedor.innerHTML = usuarios
    .map((usuario) => `<li>${usuario.name}</li>`)
    .join("");
}
```

```javascript
// main.js
import { obtenerUsuarios } from "./js/api.js";
import { pintarLista } from "./js/dom.js";

const lista = document.querySelector("#lista");
const usuarios = await obtenerUsuarios();
pintarLista(usuarios, lista);
```

Cada archivo hace **una sola cosa**, y `main.js` solo las coordina.

---

## 6. Buenas prácticas

- **Una responsabilidad por módulo:** si un archivo hace de todo, divídelo.
- **Escribe siempre la extensión** en el navegador: `"./utiles.js"`, no `"./utiles"`.
- **Rutas relativas con `./` o `../`:** sin ellas el navegador cree que es un paquete.
- **Pon los `import` al principio** del archivo, agrupados y ordenados.
- **Prefiere export con nombre:** el editor autocompleta mejor y evita nombres distintos para lo mismo en cada archivo.
- **Exporta solo lo necesario:** lo que no exportas es privado y puedes cambiarlo sin romper nada.
- **No modifiques lo que importas** desde fuera; si hace falta cambiarlo, exporta una función que lo haga.
- **Usa `import()` dinámico** para código pesado o que se usa pocas veces.
- **Archivos `index.js`** para ofrecer un único punto de entrada por carpeta.

---

## 7. Diferencias importantes

### 7.1 Módulo vs. script normal

| | Script clásico | Módulo (`type="module"`) |
|---|---|---|
| Ámbito | Global | Propio del archivo |
| Modo estricto | Opcional | Siempre |
| `import` / `export` | No | Sí |
| Carga | Bloquea el HTML (salvo `defer`/`async`) | Diferida por defecto |
| `this` arriba del todo | `window` | `undefined` |
| Requiere servidor | No | Sí (ver caso especial) |

### 7.2 ES Modules vs. CommonJS

> [!warning] Obsoleto / legado
> **CommonJS** (`require` y `module.exports`) es el sistema antiguo de Node.js. Sigue funcionando y lo verás en muchos proyectos, pero para código nuevo se recomienda **ES Modules** (`import` / `export`). Los detalles de Node están en [[03 - Módulos CommonJS y ESM]].

```javascript
// CommonJS (antiguo)
const { sumar } = require("./matematicas");
module.exports = { sumar };

// ES Modules (actual)
import { sumar } from "./matematicas.js";
export { sumar };
```

| | CommonJS | ES Modules |
|---|---|---|
| Sintaxis | `require` / `module.exports` | `import` / `export` |
| Carga | Síncrona | Asíncrona |
| Dónde nació | Node.js | Estándar del lenguaje |
| Funciona en el navegador | No | Sí |

### 7.3 `import` estático vs. `import()` dinámico

| | `import ... from` | `import()` |
|---|---|---|
| Dónde se escribe | Solo arriba del archivo | En cualquier parte |
| Ruta | Texto fijo | Puede ser una variable |
| Devuelve | Lo importado | Una Promesa |
| Cuándo carga | Al inicio | Cuando se ejecuta |

---

## 8. Casos especiales

### 8.1 No funciona abriendo el HTML con doble clic

Si abres el archivo con `file:///...`, el navegador **bloquea** los módulos por seguridad (error de CORS). Necesitas un servidor local, por ejemplo:

- La extensión **Live Server** de VS Code.
- `npx serve` o `python -m http.server` en la terminal.

### 8.2 Módulos en Node.js

Para usar `import` / `export` en Node tienes dos opciones:

- Poner `"type": "module"` en el `package.json`.
- Usar la extensión `.mjs` en el archivo.

En Node las rutas relativas también necesitan la extensión (`./utiles.js`).

### 8.3 Dependencias circulares

Si `a.js` importa de `b.js` y `b.js` importa de `a.js`, puede que uno de ellos intente usar algo que **todavía no se ha ejecutado** y recibas un error. La solución normal es mover lo común a un tercer módulo.

### 8.4 Rutas sin `./` (*bare specifiers*)

`import "lodash"` funciona en Node y con herramientas como Vite o Webpack, pero **no** directamente en el navegador. Ahí se necesita un *import map* o un empaquetador.

### 8.5 Empaquetadores

Herramientas como **Vite**, **Webpack** o **esbuild** unen muchos módulos en pocos archivos optimizados. En proyectos reales casi siempre se usan; tú sigues escribiendo `import` / `export` igual.

### 8.6 Importar JSON

Se puede, pero hay que indicarlo explícitamente:

```javascript
import datos from "./datos.json" with { type: "json" };
```

### 8.7 Un módulo se ejecuta solo una vez

Aunque lo importen varios archivos, comparten **la misma instancia**. Esto sirve para guardar estado compartido, pero también significa que un cambio en un sitio se ve en todos.

---

## 9. Resumen

- Un **módulo** es un archivo con ámbito propio: lo suyo es privado salvo que lo exportes.
- **`export`** comparte; **`import`** trae.
- **Con nombre** (`{ x }`, varios por archivo) o **por defecto** (sin llaves, solo uno).
- `as` renombra; `* as nombre` importa todo en un objeto.
- En el navegador: `<script type="module">` y un **servidor local**.
- `import()` carga módulos **bajo demanda** y devuelve una Promesa.
- Se permite `await` en el nivel superior del módulo.
- Los imports son **enlaces vivos**, no copias, y no se pueden reasignar desde fuera.
- Un módulo se ejecuta **una sola vez** aunque lo importen muchos.
- **CommonJS** (`require`) es legado; usa **ES Modules** en código nuevo.