# 09 - DOM

> [!info] ¿Qué es el DOM?
> El **DOM** (*Document Object Model*) es la **representación de la página web como un árbol de objetos** que JavaScript puede leer y modificar. Gracias a él, puedes cambiar textos, estilos, añadir o quitar elementos y reaccionar a lo que hace el usuario, todo sin recargar la página.

---

## 1. Antes de empezar

Conviene haber visto:

- [[02 - Variables y tipos]], [[06 - Funciones]], [[07 - Arrays]] y [[08 - Objetos]]: el DOM está hecho de objetos y los selectores devuelven listas parecidas a arrays.
- HTML y CSS básicos, porque el DOM trabaja sobre ellos.

> [!tip] Cargar bien el script
> Para que el script encuentre los elementos, carga el archivo con `defer` (ver [[01 - Fundamentos]]).

---

## 2. Concepto fundamental

Cuando el navegador lee tu HTML, lo convierte en un **árbol**. Cada etiqueta es un **nodo**, y los nodos se relacionan entre sí como padres, hijos y hermanos.

```html
<body>
  <h1>Título</h1>
  <p>Texto</p>
</body>
```

```
document
└── html
    └── body
        ├── h1  →  "Título"
        └── p   →  "Texto"
```

El punto de entrada es el objeto **`document`**, que representa la página entera. Desde él puedes llegar a cualquier elemento.

El proceso habitual con el DOM siempre es el mismo:

1. **Seleccionar** un elemento.
2. **Leer o modificar** algo de él (texto, estilo, atributos...).
3. Opcionalmente, **reaccionar** a eventos (ver [[10 - Eventos]]).

> [!warning] El DOM no es JavaScript
> El DOM es una interfaz que ofrece el **navegador**. Por eso no existe en Node.js (ver [[01 - Fundamentos]]).

---

## 3. Sintaxis / estructura

### Seleccionar elementos

**Métodos modernos (los recomendados)**

```js
const titulo = document.querySelector("h1");          // el primero que coincida
const items = document.querySelectorAll(".item");     // todos los que coincidan
```

Aceptan **cualquier selector CSS**: `"p"`, `".clase"`, `"#id"`, `"ul > li"`, `"input[type='text']"`...

**Métodos clásicos**

```js
document.getElementById("menu");
document.getElementsByClassName("item");
document.getElementsByTagName("p");
```

### Modificar contenido

```js
titulo.textContent = "Nuevo título";
titulo.innerHTML = "Nuevo <em>título</em>";
```

### Modificar estilos

```js
titulo.style.color = "red";
titulo.classList.add("destacado");
```

### Crear y añadir elementos

```js
const nuevo = document.createElement("li");
nuevo.textContent = "Elemento nuevo";
lista.append(nuevo);
```

---

## 4. Elementos / propiedades / características

### Selección

| Método | Devuelve |
|--------|----------|
| `querySelector(sel)` | El **primer** elemento, o `null` si no hay |
| `querySelectorAll(sel)` | **Todos** (una `NodeList`, estática) |
| `getElementById(id)` | Un elemento por su `id` |
| `getElementsByClassName(c)` | Colección **viva** de elementos |
| `getElementsByTagName(t)` | Colección **viva** de elementos |

> [!tip] Cuál usar
> Usa `querySelector` y `querySelectorAll` casi siempre: son más flexibles y se parecen a CSS.

### Recorrer una `NodeList`

`querySelectorAll` devuelve una `NodeList`. Se puede recorrer con `forEach` o `for...of`; para usar `map` o `filter`, conviértela en array:

```js
document.querySelectorAll(".item").forEach(el => console.log(el));

const arr = [...document.querySelectorAll(".item")];
```

### Contenido

| Propiedad | Qué hace |
|-----------|----------|
| `textContent` | Lee o cambia el **texto**, sin interpretar HTML |
| `innerText` | Parecido, pero respeta lo que se ve en pantalla (CSS) |
| `innerHTML` | Lee o cambia el **HTML interno** |
| `outerHTML` | Incluye también la propia etiqueta |
| `value` | Valor de un `input`, `select`, `textarea` |

> [!warning] Cuidado con `innerHTML`
> Si metes texto que viene del usuario con `innerHTML`, alguien podría inyectar código malicioso (**ataque XSS**). Para texto normal, usa siempre `textContent`.

### Atributos

```js
img.getAttribute("src");
img.setAttribute("alt", "Descripción");
img.removeAttribute("title");
img.hasAttribute("alt");
```

Muchos atributos también existen como propiedad directa:

```js
img.src;
enlace.href;
input.disabled = true;
```

### Atributos `data-*`

Sirven para guardar datos propios en el HTML y se leen con `dataset`:

```html
<button data-id="42" data-accion="borrar">Borrar</button>
```

```js
boton.dataset.id;        // "42"
boton.dataset.accion;    // "borrar"
```

### Clases

Se manejan con `classList`, la forma recomendada:

| Método | Qué hace |
|--------|----------|
| `add("a")` | Añade una clase |
| `remove("a")` | Quita una clase |
| `toggle("a")` | La pone si no está, la quita si está |
| `contains("a")` | Comprueba si la tiene |
| `replace("a", "b")` | Cambia una por otra |

### Estilos

```js
el.style.backgroundColor = "blue";   // propiedades en camelCase
el.style.fontSize = "20px";
```

> [!tip] Mejor con clases
> Cambiar estilos con `classList` es más limpio que escribirlos uno a uno con `style`. Deja los estilos en el CSS y desde JavaScript solo cambia las clases.

Para leer un estilo calculado final:

```js
getComputedStyle(el).color;
```

### Crear, insertar y eliminar elementos

| Método | Qué hace |
|--------|----------|
| `createElement("div")` | Crea un elemento |
| `append(x)` | Añade al **final** del padre |
| `prepend(x)` | Añade al **principio** del padre |
| `before(x)` / `after(x)` | Inserta antes / después del elemento |
| `remove()` | Elimina el elemento |
| `replaceWith(x)` | Lo sustituye por otro |
| `cloneNode(true)` | Clona el elemento (con sus hijos) |

### Navegar por el árbol

| Propiedad | Devuelve |
|-----------|----------|
| `parentElement` | El padre |
| `children` | Los hijos (solo elementos) |
| `firstElementChild` / `lastElementChild` | Primer / último hijo |
| `nextElementSibling` | Hermano siguiente |
| `previousElementSibling` | Hermano anterior |
| `closest("selector")` | El ancestro más cercano que coincida (incluido él mismo) |

### Medidas y posición

```js
el.offsetWidth;                 // ancho visible
el.getBoundingClientRect();     // posición y tamaño en pantalla
```

### Otros objetos globales útiles

- `window`: la ventana del navegador.
- `document.body`: el elemento `<body>`.
- `document.title`: el título de la pestaña.

### Evitar problemas de carga

El código debe ejecutarse **cuando el HTML ya está cargado**. Con `defer` basta. También puedes usar el evento:

```js
document.addEventListener("DOMContentLoaded", () => {
  // aquí el DOM ya está listo
});
```

---

## 5. Ejemplos prácticos

### Ejemplo básico: cambiar un texto

```html
<h1 id="titulo">Hola</h1>
```

```js
const titulo = document.querySelector("#titulo");
titulo.textContent = "Hola, mundo";
titulo.classList.add("destacado");
```

### Ejemplo habitual: crear una lista desde un array

```html
<ul id="lista"></ul>
```

```js
const frutas = ["manzana", "pera", "uva"];
const lista = document.querySelector("#lista");

for (const fruta of frutas) {
  const li = document.createElement("li");
  li.textContent = fruta;
  lista.append(li);
}
```

### Ejemplo: mostrar u ocultar con una clase

```css
.oculto { display: none; }
```

```js
const caja = document.querySelector(".caja");
caja.classList.toggle("oculto");
```

---

## 6. Buenas prácticas

- **Usa `querySelector` y `querySelectorAll`** para seleccionar.
- **Guarda los elementos en variables** (`const`) en vez de buscarlos una y otra vez.
- **Usa `textContent`** para texto y **evita `innerHTML`** con datos del usuario.
- **Cambia estilos con `classList`**, no escribiendo `style` a mano.
- **Comprueba que el elemento existe** antes de usarlo (puede ser `null`).
- **Usa `defer`** en el script para que el DOM esté listo.
- **Crea los elementos con `createElement`** en lugar de montar texto HTML largo.
- **Evita tocar el DOM dentro de bucles grandes** (es lento); construye todo y añade al final.
- **Usa `data-*`** para guardar información en elementos.
- **Usa IDs y clases con sentido** en el HTML para seleccionar con facilidad.

---

## 7. Diferencias importantes

### `textContent` vs `innerText` vs `innerHTML`

| | Interpreta HTML | Respeta CSS (oculto) | Uso típico |
|---|----------------|---------------------|-----------|
| `textContent` | No | No | Poner texto (recomendado) |
| `innerText` | No | Sí | Leer lo que se ve |
| `innerHTML` | **Sí** | No | Insertar HTML (con cuidado) |

### `querySelectorAll` vs `getElementsByClassName`

- `querySelectorAll`: lista **estática** (no se actualiza si cambia la página). Admite `forEach`.
- `getElementsByClassName`: colección **viva** (se actualiza sola). No admite `forEach` directamente.

### Propiedad vs atributo

```js
input.value;                    // valor actual, lo que escribe el usuario
input.getAttribute("value");    // valor inicial escrito en el HTML
```

### `append` vs `appendChild`

`append` es más moderno: acepta varios elementos y también texto. `appendChild` es el clásico: solo acepta un nodo.

### `remove()` vs ocultar

- `remove()` lo **elimina** del DOM.
- Una clase con `display: none` lo **oculta** pero sigue existiendo.

---

## 8. Casos especiales

### El elemento no existe

`querySelector` devuelve `null`. Usar propiedades sobre `null` da error:

```js
const el = document.querySelector(".no-existe");
el.textContent = "Hola";   // error

el?.classList.add("x");    // seguro con ?.
```

### Elementos creados dinámicamente

Si creas un elemento después de haber añadido eventos, ese elemento nuevo **no tiene el evento**. Se soluciona con delegación de eventos (ver [[10 - Eventos]]).

### Modificar muchos elementos

```js
document.querySelectorAll(".item").forEach(el => {
  el.classList.add("activo");
});
```

### Insertar HTML en una posición concreta

```js
el.insertAdjacentHTML("beforeend", "<p>Nuevo</p>");
```

Posiciones: `beforebegin`, `afterbegin`, `beforeend`, `afterend`.

### `DocumentFragment`

Permite construir muchos elementos fuera de la página y añadirlos todos de una vez, para mejor rendimiento:

```js
const fragmento = document.createDocumentFragment();
// ...añadir elementos al fragmento...
lista.append(fragmento);
```

### Observar cambios

`MutationObserver` avisa cuando el DOM cambia. Es un tema avanzado.

### Nodos de texto y comentarios

El DOM también incluye nodos que no son elementos (texto, comentarios). Por eso `children` (solo elementos) es más cómodo que `childNodes` (todos).

### Elementos de formulario

Tienen propiedades especiales como `value`, `checked` y `selectedIndex`. Se ven en [[11 - Formularios]].

---

## 9. Resumen

- El **DOM** es la página convertida en un **árbol de objetos** que JavaScript puede manipular.
- El punto de entrada es **`document`**.
- Flujo habitual: **seleccionar → modificar → (reaccionar a eventos)**.
- Selecciona con **`querySelector`** (uno) y **`querySelectorAll`** (varios).
- Cambia el texto con **`textContent`**; **evita `innerHTML`** con datos del usuario (riesgo XSS).
- Maneja las clases con **`classList`** (`add`, `remove`, `toggle`, `contains`).
- Atributos: `getAttribute`, `setAttribute`, `dataset` para `data-*`.
- Crea elementos con **`createElement`** y añádelos con **`append`**; elimina con **`remove()`**.
- Navega por el árbol con `parentElement`, `children`, `nextElementSibling` y `closest`.
- Comprueba siempre que el elemento **no sea `null`** y carga el script con **`defer`**.

---

⬅️ [[08 - Objetos]] | ➡️ [[10 - Eventos]]