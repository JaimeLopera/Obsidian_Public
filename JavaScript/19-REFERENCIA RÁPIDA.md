# 19 - Referencia rápida

> [!info] ¿Qué es?
> Chuleta de **JavaScript** para consultar en segundos: sintaxis, métodos y trucos de todas las notas de la carpeta. No explica, solo recuerda. Si algo no te suena, sigue el enlace a su nota.

---

## 1. Variables y tipos ([[02-VARIABLES Y TIPOS]])

```javascript
const fijo = 10;       // no se reasigna
let cambia = 5;        // se puede reasignar

typeof "hola";         // "string"
typeof 42;             // "number"
typeof true;           // "boolean"
typeof undefined;      // "undefined"
typeof null;           // "object" (error histórico)
typeof {};             // "object"
typeof [];             // "object" (usa Array.isArray)
typeof (() => {});     // "function"
```

| Conversión | Código |
|---|---|
| A número | `Number("5")`, `parseInt("5px")`, `parseFloat("3.5")` |
| A texto | `String(5)`, `(5).toString()` |
| A booleano | `Boolean(x)`, `!!x` |

**Valores "falsos":** `false`, `0`, `""`, `null`, `undefined`, `NaN`. Todo lo demás es verdadero.

---

## 2. Operadores ([[03-OPERADORES]])

| Tipo | Operadores |
|---|---|
| Aritméticos | `+  -  *  /  %  **  ++  --` |
| Asignación | `=  +=  -=  *=  /=  ||=  &&=  ??=` |
| Comparación | `===  !==  >  <  >=  <=` (evita `==`) |
| Lógicos | `&&  ||  !  ??` |
| Otros | `? :` (ternario), `?.`, `typeof`, `...` |

---

## 3. Condicionales ([[04-CONDICIONALES]])

```javascript
if (a > 5) {
} else if (a === 5) {
} else {
}

const texto = edad >= 18 ? "adulto" : "menor";

switch (valor) {
  case 1:
    break;
  default:
}
```

---

## 4. Bucles ([[05-BUCLES]])

```javascript
for (let i = 0; i < 5; i++) {}
for (const x of array) {}          // valores
for (const clave in objeto) {}     // claves
while (condicion) {}
do {} while (condicion);

break;      // sale del bucle
continue;   // salta a la siguiente vuelta
```

---

## 5. Funciones ([[06-FUNCIONES]])

```javascript
function sumar(a, b = 0) { return a + b; }
const restar = function (a, b) { return a - b; };
const multiplicar = (a, b) => a * b;
const doble = (n) => n * 2;
const objeto = () => ({ clave: 1 });     // devolver un objeto

function todos(...args) {}               // parámetros rest
(function () { /* IIFE */ })();
```

---

## 6. Arrays ([[07-ARRAYS]])

| Acción | Método |
|---|---|
| Añadir al final / quitar del final | `push()` / `pop()` |
| Añadir al inicio / quitar del inicio | `unshift()` / `shift()` |
| Cortar sin modificar | `slice(inicio, fin)` |
| Insertar/eliminar en medio | `splice(pos, cuantos, ...nuevos)` |
| Unir | `concat()`, `[...a, ...b]` |
| Buscar | `indexOf()`, `includes()`, `find()`, `findIndex()` |
| Transformar | `map()` |
| Filtrar | `filter()` |
| Reducir a un valor | `reduce((acc, x) => ..., inicial)` |
| Recorrer | `forEach()` |
| Comprobar | `some()`, `every()` |
| Ordenar | `sort()`, `toSorted()` |
| Invertir | `reverse()`, `toReversed()` |
| A texto | `join(",")` |
| Aplanar | `flat()` |
| Último | `at(-1)` |

> `sort()` sin función ordena **como texto**. Para números: `sort((a, b) => a - b)`.

---

## 7. Objetos ([[08-OBJETOS]])

```javascript
const persona = { nombre: "Ana", edad: 20 };

persona.nombre;               // acceso con punto
persona["edad"];              // acceso con corchetes
persona.ciudad = "Madrid";    // añadir
delete persona.edad;          // borrar
"nombre" in persona;          // ¿existe la clave?

Object.keys(persona);         // claves
Object.values(persona);       // valores
Object.entries(persona);      // pares [clave, valor]
Object.assign({}, persona);   // copia superficial
structuredClone(persona);     // copia profunda
Object.freeze(persona);       // impedir cambios
```

---

## 8. Textos

| Método | Qué hace |
|---|---|
| `length` | Longitud |
| `toUpperCase()` / `toLowerCase()` | Mayúsculas / minúsculas |
| `trim()` | Quita espacios de los extremos |
| `includes()`, `startsWith()`, `endsWith()` | Comprobaciones |
| `indexOf()` | Posición de un texto |
| `slice(i, f)` | Extraer parte |
| `split(",")` | Texto → array |
| `replace()` / `replaceAll()` | Reemplazar |
| `padStart(n, "0")` | Rellenar por delante |
| `repeat(n)` | Repetir |

---

## 9. DOM ([[09-DOM]])

```javascript
document.querySelector(".clase");          // primero que coincide
document.querySelectorAll("li");           // todos
document.getElementById("id");

el.textContent = "texto";                  // solo texto
el.innerHTML = "<b>html</b>";              // HTML (¡cuidado con datos externos!)
el.setAttribute("href", "#");
el.getAttribute("href");
el.classList.add("a");                     // remove, toggle, contains
el.style.color = "red";
el.dataset.id;                             // atributos data-id

const nuevo = document.createElement("li");
padre.append(nuevo);                       // prepend, before, after
el.remove();
```

---

## 10. Eventos ([[10-EVENTOS]])

```javascript
boton.addEventListener("click", (evento) => {
  evento.preventDefault();      // cancelar acción por defecto
  evento.stopPropagation();     // no propagar a los padres
  evento.target;                // quién lo originó
  evento.currentTarget;         // dónde está el listener
});

boton.removeEventListener("click", funcion);
```

| Eventos habituales | |
|---|---|
| Ratón | `click`, `dblclick`, `mouseover`, `mouseout` |
| Teclado | `keydown`, `keyup` |
| Formularios | `submit`, `input`, `change`, `focus`, `blur` |
| Página | `DOMContentLoaded`, `load`, `scroll`, `resize` |

**Delegación:** un solo listener en el padre y usar `evento.target.closest("selector")`.

---

## 11. Formularios ([[11-FORMULARIOS]])

```javascript
formulario.addEventListener("submit", (e) => {
  e.preventDefault();
  const datos = new FormData(formulario);
  const objeto = Object.fromEntries(datos);
});

input.value;                    // valor actual (siempre texto)
checkbox.checked;               // true / false
input.validity.valid;           // ¿pasa la validación HTML?
formulario.reportValidity();    // muestra los errores
```

---

## 12. Asincronía ([[12-ASINCRONÍA]])

```javascript
setTimeout(() => {}, 1000);          // una vez
const id = setInterval(() => {}, 1000);
clearInterval(id);

const promesa = new Promise((resolver, rechazar) => {});
promesa.then(valor => {}).catch(error => {}).finally(() => {});

async function f() {
  const x = await promesa;
}

await Promise.all([p1, p2]);         // todas, falla si una falla
await Promise.allSettled([p1, p2]);  // espera todas, sin fallar
await Promise.race([p1, p2]);        // la primera en terminar
await Promise.any([p1, p2]);         // la primera que tenga éxito
```

---

## 13. Fetch y APIs ([[13-FETCH Y APIs]])

```javascript
const respuesta = await fetch(url);
if (!respuesta.ok) throw new Error(respuesta.status);
const datos = await respuesta.json();

await fetch(url, {
  method: "POST",                              // PUT, PATCH, DELETE
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify(objeto)
});
```

| Código HTTP | Significado |
|---|---|
| 200 | OK |
| 201 | Creado |
| 400 | Petición incorrecta |
| 401 / 403 | No autenticado / sin permiso |
| 404 | No encontrado |
| 500 | Error del servidor |

---

## 14. Módulos ([[14-MÓDULOS]])

```javascript
export const a = 1;
export default function () {}

import porDefecto, { a } from "./archivo.js";
import * as todo from "./archivo.js";
const modulo = await import("./archivo.js");
```

```html
<script type="module" src="main.js"></script>
```

---

## 15. Errores y debugging ([[15-ERRORES Y DEBUGGING]])

```javascript
try {
  // código
} catch (error) {
  console.error(error.name, error.message);
} finally {
  // siempre
}

throw new Error("Mensaje");
debugger;
```

| Error | Causa típica |
|---|---|
| `SyntaxError` | Código mal escrito |
| `ReferenceError` | Variable inexistente |
| `TypeError` | Operación no válida sobre ese valor |
| `RangeError` | Fuera de rango / recursión infinita |

`console.log`, `error`, `warn`, `table`, `time`/`timeEnd`. **F12** abre las DevTools.

---

## 16. Web Storage ([[16-WEB STORAGE]])

```javascript
localStorage.setItem("clave", "valor");
localStorage.getItem("clave");          // null si no existe
localStorage.removeItem("clave");
localStorage.clear();

localStorage.setItem("obj", JSON.stringify(objeto));
const obj = JSON.parse(localStorage.getItem("obj"));
```

`sessionStorage` tiene los mismos métodos pero se borra al cerrar la pestaña.

---

## 17. JSON ([[17-JSON]])

```javascript
JSON.stringify(objeto);           // objeto → texto
JSON.stringify(objeto, null, 2);  // con formato
JSON.parse(texto);                // texto → objeto
```

Claves y textos con **comillas dobles**; sin comas finales ni comentarios.

---

## 18. JavaScript moderno ([[18-JS MODERNO]])

```javascript
`Hola ${nombre}`                  // plantilla
const { a, b } = objeto;          // desestructurar
const [x, y] = array;
const copia = { ...objeto };      // spread
objeto?.propiedad                 // acceso seguro
valor ?? "defecto"                // solo si null/undefined
class A { #privado = 1; }         // campo privado
```

---

## 19. Matemáticas y fechas

```javascript
Math.round(2.5);      // 3
Math.floor(2.9);      // 2
Math.ceil(2.1);       // 3
Math.max(1, 5, 3);    // 5
Math.min(1, 5, 3);    // 1
Math.random();        // entre 0 y 1
Math.abs(-4);         // 4
Math.PI;

(3.14159).toFixed(2); // "3.14" (devuelve texto)

const ahora = new Date();
ahora.getFullYear();
ahora.getMonth();     // 0 = enero
ahora.getDate();      // día del mes
ahora.toISOString();
Date.now();           // milisegundos desde 1970
```

---

## 20. Trampas que conviene recordar

- `===` en lugar de `==`.
- `typeof null` es `"object"`.
- `0.1 + 0.2` no es exactamente `0.3`.
- `sort()` ordena como texto por defecto.
- `fetch` **no** falla con 404 o 500: mira `respuesta.ok`.
- `input.value` siempre es **texto**, aunque sea `type="number"`.
- `innerHTML` con datos externos es un riesgo de seguridad.
- Las flechas no tienen su propio `this`.
- `Object.assign` y spread copian solo el primer nivel.
- Los meses de `Date` empiezan en **0**.