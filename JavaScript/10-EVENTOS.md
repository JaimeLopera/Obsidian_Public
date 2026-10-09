# 10 - Eventos

> [!info] ¿Qué es un evento?
> Un evento es **algo que ocurre en la página**: un clic, una tecla pulsada, un formulario enviado, la página terminando de cargar... Con JavaScript puedes **escuchar** esos eventos y ejecutar código cuando suceden. Es lo que hace que una web sea interactiva.

---

## 1. Antes de empezar

Conviene haber visto:

- [[06 - Funciones]]: los eventos se manejan con funciones (callbacks).
- [[08 - Objetos]]: el evento llega como un objeto con información.
- [[09 - DOM]]: para escuchar un evento primero hay que seleccionar el elemento.

---

## 2. Concepto fundamental

El funcionamiento siempre es el mismo:

1. Eliges un **elemento** (un botón, un input...).
2. Dices **qué evento** quieres escuchar (`"click"`, `"input"`...).
3. Le das una **función** que se ejecutará cuando el evento ocurra.

Esa función se llama **manejador** (*handler*) o *listener*.

```js
boton.addEventListener("click", () => {
  console.log("¡Me han pulsado!");
});
```

Cuando el evento sucede, el navegador llama a tu función y le pasa un **objeto evento** con los detalles (qué elemento fue, qué tecla, dónde se hizo clic...).

---

## 3. Sintaxis / estructura

### `addEventListener`

La forma recomendada.

```js
elemento.addEventListener("evento", funcion);
```

```js
const boton = document.querySelector("#boton");

function saludar(evento) {
  console.log("Hola");
}

boton.addEventListener("click", saludar);
```

> [!warning] No pongas paréntesis
> Pasa la función **sin ejecutarla**: `saludar`, no `saludar()`. Con paréntesis se ejecutaría al momento.

### Con función flecha

```js
boton.addEventListener("click", (e) => {
  console.log(e.target);
});
```

### Quitar un evento

Hace falta una referencia a la **misma función**:

```js
boton.removeEventListener("click", saludar);
```

Con una función flecha anónima no se puede quitar. Para eso, guárdala en una variable.

### Opciones

```js
boton.addEventListener("click", saludar, { once: true });
```

| Opción | Efecto |
|--------|--------|
| `once: true` | Se ejecuta **una sola vez** y se elimina solo |
| `capture: true` | Escucha en la fase de captura |
| `passive: true` | Promete no usar `preventDefault()` (mejora el rendimiento al hacer scroll) |

---

## 4. Elementos / propiedades / características

### Eventos más comunes

**Ratón**

| Evento | Cuándo ocurre |
|--------|---------------|
| `click` | Al hacer clic |
| `dblclick` | Doble clic |
| `mouseenter` / `mouseleave` | El ratón entra / sale del elemento |
| `mouseover` / `mouseout` | Igual, pero también para los hijos |
| `mousemove` | El ratón se mueve |
| `contextmenu` | Clic derecho |

**Teclado**

| Evento | Cuándo ocurre |
|--------|---------------|
| `keydown` | Se pulsa una tecla |
| `keyup` | Se suelta una tecla |

**Formularios** (ver [[11 - Formularios]])

| Evento | Cuándo ocurre |
|--------|---------------|
| `submit` | Se envía el formulario |
| `input` | El valor cambia mientras se escribe |
| `change` | El valor cambia y se confirma (al salir del campo, en un `select`...) |
| `focus` / `blur` | El campo gana / pierde el foco |

**Página y ventana**

| Evento | Cuándo ocurre |
|--------|---------------|
| `DOMContentLoaded` | El HTML está cargado y el DOM listo |
| `load` | Todo (imágenes incluidas) está cargado |
| `resize` | Cambia el tamaño de la ventana |
| `scroll` | Se hace scroll |

**Táctil y arrastrar**

| Evento | Cuándo ocurre |
|--------|---------------|
| `touchstart` / `touchend` | Se toca / se deja de tocar la pantalla |
| `dragstart` / `drop` | Arrastrar y soltar |

### El objeto evento

El manejador recibe un objeto con información. Por convención se llama `e` o `evento`.

| Propiedad / método | Qué da |
|--------------------|--------|
| `e.target` | El elemento donde **ocurrió** el evento |
| `e.currentTarget` | El elemento que **tiene el listener** |
| `e.type` | El tipo de evento (`"click"`) |
| `e.preventDefault()` | Cancela el comportamiento por defecto |
| `e.stopPropagation()` | Detiene la propagación del evento |
| `e.key` | La tecla pulsada (`"Enter"`, `"a"`) |
| `e.clientX` / `e.clientY` | Posición del ratón en la ventana |

### `preventDefault()`

Cancela lo que el navegador haría normalmente:

- Evitar que un formulario recargue la página al enviarse.
- Evitar que un enlace navegue.

```js
formulario.addEventListener("submit", (e) => {
  e.preventDefault();
  // aquí compruebas y procesas los datos
});
```

### `this` dentro del manejador

En una función normal, `this` es el elemento que escucha. En una flecha **no** (usa `e.currentTarget` en su lugar).

### Propagación de eventos (bubbling)

Cuando ocurre un evento en un elemento, **sube** por sus padres hasta el documento. Por eso un clic en un botón también "se entera" el `div` que lo contiene.

```
botón → div → body → document
```

Las fases son tres:

1. **Captura**: baja desde `document` hasta el elemento.
2. **Objetivo**: llega al elemento.
3. **Burbujeo**: sube de vuelta (es la fase en la que escuchan por defecto los listeners).

`stopPropagation()` detiene esa subida.

### Delegación de eventos

En lugar de poner un listener en **cada** elemento de una lista, pones **uno solo en el padre** y averiguas quién fue con `e.target`. Ventajas: menos código, mejor rendimiento y funciona con elementos creados después.

```js
lista.addEventListener("click", (e) => {
  const item = e.target.closest("li");
  if (item) {
    console.log("Clic en", item.textContent);
  }
});
```

### Eventos personalizados

Puedes crear y lanzar tus propios eventos:

```js
const aviso = new CustomEvent("avisar", { detail: { mensaje: "Hola" } });
document.dispatchEvent(aviso);

document.addEventListener("avisar", (e) => console.log(e.detail.mensaje));
```

### Otras formas de asignar eventos

> [!warning] Obsoleto / legado
> Dos formas antiguas que conviene no usar:
>
> - En el HTML: `<button onclick="saludar()">`
> - Con propiedad: `boton.onclick = saludar;` (solo permite **un** manejador por evento; el siguiente pisa al anterior)
>
> Usa siempre **`addEventListener`**.

---

## 5. Ejemplos prácticos

### Ejemplo básico: contador con clic

```html
<button id="boton">Pulsar</button>
<p id="resultado">0</p>
```

```js
const boton = document.querySelector("#boton");
const resultado = document.querySelector("#resultado");
let cuenta = 0;

boton.addEventListener("click", () => {
  cuenta++;
  resultado.textContent = cuenta;
});
```

### Ejemplo habitual: formulario sin recargar la página

```js
const formulario = document.querySelector("#formulario");

formulario.addEventListener("submit", (e) => {
  e.preventDefault();
  const nombre = formulario.querySelector("#nombre").value;
  console.log("Enviado:", nombre);
});
```

### Ejemplo: tecla pulsada

```js
document.addEventListener("keydown", (e) => {
  if (e.key === "Escape") {
    console.log("Has pulsado Escape");
  }
});
```

### Ejemplo: delegación en una lista

```js
const lista = document.querySelector("#lista");

lista.addEventListener("click", (e) => {
  if (e.target.matches("button.borrar")) {
    e.target.closest("li").remove();
  }
});
```

---

## 6. Buenas prácticas

- **Usa siempre `addEventListener`**, no `onclick` ni atributos en el HTML.
- **No pongas paréntesis** al pasar la función: `saludar`, no `saludar()`.
- **Usa delegación de eventos** cuando tengas muchos elementos similares o elementos creados dinámicamente.
- **Llama a `preventDefault()`** en formularios y enlaces cuando quieras controlar el comportamiento.
- **Usa nombres claros** para las funciones manejadoras.
- **Usa `once: true`** si el evento solo debe ocurrir una vez.
- **Quita los listeners** que ya no necesites si el elemento va a seguir existiendo.
- **Evita `stopPropagation()`** salvo que sea necesario: rompe la delegación.
- **Usa `input` en lugar de `keyup`** para reaccionar a lo que se escribe.
- **Limita eventos frecuentes** (`scroll`, `resize`, `mousemove`) para no sobrecargar la página.

---

## 7. Diferencias importantes

### `target` vs `currentTarget`

- `target`: el elemento **exacto** donde se hizo clic (puede ser un hijo).
- `currentTarget`: el elemento **al que pertenece** el listener.

### `addEventListener` vs `onclick`

| | `addEventListener` | `onclick` |
|---|-------------------|-----------|
| Varios manejadores del mismo evento | Sí | No (el último pisa) |
| Opciones (`once`, `capture`...) | Sí | No |
| Se recomienda | **Sí** | No |

### `input` vs `change`

- `input`: se dispara **en cada cambio**, mientras escribes.
- `change`: se dispara **al terminar** (cuando el campo pierde el foco).

### `mouseenter` vs `mouseover`

`mouseover` también se dispara al pasar por los **hijos**, y puede ejecutarse varias veces. `mouseenter` solo cuando entras en el propio elemento.

### `keydown` vs `keyup`

`keydown` se repite mientras mantienes la tecla; `keyup` solo una vez al soltar.

### `DOMContentLoaded` vs `load`

- `DOMContentLoaded`: cuando el HTML está listo (más rápido).
- `load`: cuando todo está cargado, imágenes y estilos incluidos.

---

## 8. Casos especiales

### Elementos creados después

Un listener solo afecta a los elementos que existían al añadirlo. Los nuevos no lo tienen. Solución: **delegación de eventos** en un padre que ya exista.

### Varios elementos con el mismo evento

Hay que recorrerlos:

```js
document.querySelectorAll(".boton").forEach(b => {
  b.addEventListener("click", manejar);
});
```

### Pasar argumentos al manejador

Envuelve la llamada en una flecha:

```js
boton.addEventListener("click", () => saludar("Ana"));
```

### Clics rápidos repetidos

Se puede desactivar el botón mientras se procesa, o usar `once: true`.

### Enter en un formulario

Pulsar Enter dentro de un campo ya dispara el evento `submit`; no hace falta escuchar la tecla.

### Evitar disparos excesivos (debounce)

En eventos como `input` o `scroll`, se espera un tiempo antes de ejecutar la acción pesada:

```js
let temporizador;
input.addEventListener("input", () => {
  clearTimeout(temporizador);
  temporizador = setTimeout(() => buscar(input.value), 300);
});
```

### Listeners en `window` y `document`

Eventos como `resize`, `scroll`, `keydown` globales o `DOMContentLoaded` se escuchan en `window` o `document`, no en un elemento concreto.

### Eventos de teclado y accesibilidad

Para acciones principales, usa elementos como `<button>`, que ya funcionan con teclado, en lugar de un `<div>` con `click`.

---

## 9. Resumen

- Un **evento** es algo que ocurre en la página; se **escucha** con `addEventListener("evento", funcion)`.
- La función manejadora recibe un **objeto evento** (`e`) con información.
- **No pongas paréntesis** al pasar la función: `saludar`, no `saludar()`.
- `e.target` es donde ocurrió el evento; `e.currentTarget` es quien tiene el listener.
- **`preventDefault()`** cancela el comportamiento por defecto (formularios, enlaces).
- Los eventos **suben** por el árbol (**bubbling**); `stopPropagation()` lo detiene.
- La **delegación de eventos** pone un solo listener en el padre y funciona con elementos nuevos.
- Eventos comunes: `click`, `input`, `change`, `submit`, `keydown`, `DOMContentLoaded`.
- **Evita** `onclick` en el HTML y `elemento.onclick = ...`.
- Usa `once: true` para ejecutar un evento una sola vez.

---

⬅️ [[09 - DOM]] | ➡️ [[11 - Formularios]]