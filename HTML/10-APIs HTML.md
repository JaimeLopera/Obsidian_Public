# 10 - APIs HTML

> [!info] ¿Qué es?
> Las **APIs HTML** (o *Web APIs*) son funcionalidades que el navegador expone a JavaScript para que una página pueda hacer cosas que el HTML por sí solo no puede: reproducir audio por código, dibujar gráficos, conocer la ubicación del usuario, arrastrar elementos, leer archivos, copiar al portapapeles, etc. HTML aporta los elementos (`<video>`, `<canvas>`, `<dialog>`…) y el navegador aporta la API para controlarlos.

---

## 1. Antes de empezar

Para entender esta nota conviene tener claro:

- **API** = conjunto de objetos, métodos, propiedades y eventos que el navegador ofrece para usar una funcionalidad.
- **DOM**: el árbol de elementos de la página. Ver [[09 - DOM]] en la carpeta de JavaScript.
- **Objeto `window`**: contenedor global donde viven muchas APIs (`window.history`, `window.localStorage`…). Se puede escribir sin `window.`.
- **Eventos y Promesas**: casi todas las APIs modernas se usan con ellos. Ver [[10 - Eventos]] y [[12 - Asincronía]].

> [!note] Alcance de esta nota
> Aquí se ve **qué APIs existen, para qué sirven y cómo se conectan con el HTML**. El detalle profundo de `fetch()`, Web Storage o JSON está en sus notas propias:
> - [[13 - Fetch y APIs]]
> - [[16 - Web Storage]]
> - [[17 - JSON]]

---

## 2. Concepto fundamental

El HTML describe **estructura y contenido**. Las APIs añaden **capacidades y comportamiento**:

| Capa | Responsabilidad | Ejemplo |
|---|---|---|
| HTML | Declarar el elemento | `<video src="clip.mp4"></video>` |
| API (JS) | Controlarlo por código | `video.play()`, `video.currentTime = 30` |

Hay tres formas en que una API se relaciona con el HTML:

1. **API de un elemento concreto**: el propio elemento tiene métodos y propiedades (`<video>`, `<canvas>`, `<dialog>`, `<form>`, `<input>`).
2. **API del navegador o del documento**: no pertenece a un elemento (`navigator.geolocation`, `history`, `localStorage`).
3. **API que observa o reacciona al DOM**: vigilan cambios o visibilidad (`IntersectionObserver`, `MutationObserver`, `ResizeObserver`).

> [!tip] Idea clave
> Una API **no es HTML**: es JavaScript que opera sobre el navegador. Por eso casi siempre se necesita un `<script>` y un elemento o evento que lo dispare.

---

## 3. Sintaxis / estructura

### Patrón general de uso

```js
// 1. Comprobar que el navegador la soporta
if ("geolocation" in navigator) {
  // 2. Usarla
  navigator.geolocation.getCurrentPosition(onExito, onError);
} else {
  // 3. Alternativa si no existe
  console.log("API no disponible");
}
```

### Tres estilos de API

```js
// a) Métodos directos sobre un elemento
document.querySelector("video").play();

// b) Callbacks (APIs antiguas)
navigator.geolocation.getCurrentPosition(
  (pos) => console.log(pos.coords.latitude),
  (err) => console.log(err.message)
);

// c) Promesas (APIs modernas)
const texto = await navigator.clipboard.readText();
```

### Detección de funcionalidades (*feature detection*)

```js
"IntersectionObserver" in window;   // objeto global
"showModal" in HTMLDialogElement.prototype; // método de un elemento
"clipboard" in navigator;           // propiedad de navigator
```

---

## 4. Elementos / propiedades / características

### 4.1 Mapa de APIs más usadas

| API | Para qué sirve | Se relaciona con |
|---|---|---|
| **HTMLMediaElement** | Controlar audio y vídeo | `<audio>`, `<video>` |
| **Canvas 2D** | Dibujar gráficos con código | `<canvas>` |
| **Drag and Drop** | Arrastrar y soltar elementos | atributo `draggable` |
| **Dialog** | Ventanas modales nativas | `<dialog>` |
| **Popover** | Capas emergentes sin JS | atributo `popover` |
| **Geolocation** | Ubicación del usuario | `navigator.geolocation` |
| **Clipboard** | Copiar y pegar | `navigator.clipboard` |
| **History** | Cambiar la URL sin recargar | `history` |
| **File** | Leer archivos del usuario | `<input type="file">` |
| **Intersection Observer** | Saber si un elemento es visible | cualquier elemento |
| **Web Storage** | Guardar datos clave-valor | `localStorage`, `sessionStorage` |
| **Notifications** | Avisos del sistema | `Notification` |
| **Web Workers** | JavaScript en segundo plano | `Worker` |
| **Page Visibility** | Saber si la pestaña está visible | `document.visibilityState` |
| **Fullscreen** | Pantalla completa | `element.requestFullscreen()` |

---

### 4.2 Audio y vídeo (HTMLMediaElement)

Los elementos `<audio>` y `<video>` comparten la misma API.

| Elemento | Tipo | Descripción |
|---|---|---|
| `play()` | método | Inicia la reproducción (devuelve una Promesa) |
| `pause()` | método | Pausa |
| `currentTime` | propiedad | Posición actual en segundos |
| `duration` | propiedad | Duración total |
| `volume` | propiedad | De `0` a `1` |
| `muted` | propiedad | Silenciar |
| `playbackRate` | propiedad | Velocidad (`1` = normal) |
| `paused` / `ended` | propiedad | Estado |
| `ended`, `timeupdate`, `loadedmetadata` | eventos | Reaccionar al progreso |

```html
<video id="clip" src="clip.mp4" controls></video>
<button id="btn">Play / Pause</button>

<script>
  const video = document.querySelector("#clip");
  document.querySelector("#btn").addEventListener("click", () => {
    video.paused ? video.play() : video.pause();
  });
</script>
```

---

### 4.3 Canvas

`<canvas>` es un lienzo vacío: todo lo que se ve se dibuja con JavaScript mediante un **contexto**.

```html
<canvas id="lienzo" width="300" height="150"></canvas>

<script>
  const ctx = document.querySelector("#lienzo").getContext("2d");
  ctx.fillStyle = "royalblue";
  ctx.fillRect(20, 20, 100, 60); // x, y, ancho, alto
</script>
```

| Método | Función |
|---|---|
| `fillRect(x, y, w, h)` | Rectángulo relleno |
| `strokeRect(x, y, w, h)` | Rectángulo con solo borde |
| `clearRect(x, y, w, h)` | Borra una zona |
| `beginPath()`, `moveTo()`, `lineTo()`, `stroke()` | Dibujar líneas |
| `arc(x, y, r, inicio, fin)` | Círculos y arcos |
| `fillText(texto, x, y)` | Texto |
| `drawImage(img, x, y)` | Dibujar una imagen |

> [!note]
> `width` y `height` del `<canvas>` se definen **como atributos HTML**, no solo con CSS. Si solo se usa CSS, el dibujo se estira y se ve borroso.

---

### 4.4 Drag and Drop

Se activa con el atributo `draggable="true"` y se controla con eventos.

| Evento | Se dispara en | Cuándo |
|---|---|---|
| `dragstart` | el elemento arrastrado | Empieza el arrastre |
| `dragover` | la zona de destino | Mientras se pasa por encima |
| `drop` | la zona de destino | Al soltar |
| `dragend` | el elemento arrastrado | Termina el arrastre |

```html
<div id="origen" draggable="true">Arrástrame</div>
<div id="destino">Suelta aquí</div>

<script>
  const origen = document.querySelector("#origen");
  const destino = document.querySelector("#destino");

  origen.addEventListener("dragstart", (e) => {
    e.dataTransfer.setData("text/plain", e.target.id);
  });

  destino.addEventListener("dragover", (e) => e.preventDefault()); // permite soltar

  destino.addEventListener("drop", (e) => {
    e.preventDefault();
    const id = e.dataTransfer.getData("text/plain");
    destino.append(document.getElementById(id));
  });
</script>
```

> [!warning] Detalle imprescindible
> Sin `e.preventDefault()` en `dragover`, el navegador **no permite soltar** y el evento `drop` nunca se dispara.

---

### 4.5 Dialog

`<dialog>` es una ventana emergente nativa, con accesibilidad y gestión del foco incluidas.

| Método / propiedad | Descripción |
|---|---|
| `showModal()` | Abre como modal (bloquea el resto, añade `::backdrop`, `Esc` la cierra) |
| `show()` | Abre sin bloquear la página |
| `close(valor)` | Cierra y guarda un valor en `returnValue` |
| `open` | Atributo/propiedad: indica si está abierto |
| evento `close` | Se dispara al cerrarse |

```html
<dialog id="aviso">
  <p>¿Seguro que quieres continuar?</p>
  <form method="dialog">
    <button value="no">Cancelar</button>
    <button value="si">Aceptar</button>
  </form>
</dialog>
<button id="abrir">Abrir</button>

<script>
  const aviso = document.querySelector("#aviso");
  document.querySelector("#abrir").addEventListener("click", () => aviso.showModal());
  aviso.addEventListener("close", () => console.log(aviso.returnValue));
</script>
```

> [!tip]
> Un `<form method="dialog">` dentro de un `<dialog>` lo cierra automáticamente al enviar y guarda el `value` del botón pulsado en `returnValue`, sin escribir JavaScript extra.

---

### 4.6 Popover

Capas emergentes **sin JavaScript**, declaradas con atributos.

```html
<button popovertarget="menu">Abrir menú</button>

<div id="menu" popover>
  <p>Contenido del popover</p>
</div>
```

| Atributo | Función |
|---|---|
| `popover` | Convierte el elemento en un popover |
| `popovertarget="id"` | El botón controla ese popover |
| `popovertargetaction="show \| hide \| toggle"` | Acción del botón (por defecto `toggle`) |

Con JavaScript: `elemento.showPopover()`, `hidePopover()`, `togglePopover()`.

---

### 4.7 Geolocation

```js
navigator.geolocation.getCurrentPosition(
  (pos) => {
    const { latitude, longitude, accuracy } = pos.coords;
    console.log(latitude, longitude, accuracy);
  },
  (err) => console.log(err.code, err.message),
  { enableHighAccuracy: true, timeout: 10000 }
);
```

| Método | Descripción |
|---|---|
| `getCurrentPosition(ok, error, opciones)` | Obtiene la posición **una vez** |
| `watchPosition(ok, error, opciones)` | La sigue **continuamente** y devuelve un `id` |
| `clearWatch(id)` | Detiene el seguimiento |

Códigos de error: `1` permiso denegado, `2` posición no disponible, `3` tiempo agotado.

---

### 4.8 Clipboard

```js
// Copiar
await navigator.clipboard.writeText("Texto copiado");

// Pegar (pide permiso al usuario)
const texto = await navigator.clipboard.readText();
```

> [!warning] Obsoleto / legado
> `document.execCommand("copy")` está **obsoleto**. Sustituir por `navigator.clipboard.writeText()`.

---

### 4.9 History

Permite cambiar la URL y el historial **sin recargar la página** (base de las *Single Page Applications*).

| Método / evento | Descripción |
|---|---|
| `history.pushState(estado, "", url)` | Añade una entrada nueva |
| `history.replaceState(estado, "", url)` | Sustituye la entrada actual |
| `history.back()` / `forward()` / `go(n)` | Navegar por el historial |
| evento `popstate` | Se dispara al pulsar atrás/adelante |

```js
history.pushState({ pagina: 2 }, "", "/productos?pagina=2");

window.addEventListener("popstate", (e) => {
  console.log("Estado:", e.state);
});
```

---

### 4.10 File API

Lee archivos elegidos por el usuario con `<input type="file">` (o soltados en una zona de Drag and Drop).

```html
<input type="file" id="archivo" accept="image/*">
<img id="vista" alt="Vista previa">

<script>
  document.querySelector("#archivo").addEventListener("change", (e) => {
    const file = e.target.files[0];
    if (!file) return;
    console.log(file.name, file.size, file.type);
    document.querySelector("#vista").src = URL.createObjectURL(file);
  });
</script>
```

| Objeto | Para qué |
|---|---|
| `File` | Representa un archivo (`name`, `size`, `type`, `lastModified`) |
| `FileReader` | Lee el contenido (`readAsText`, `readAsDataURL`…) |
| `URL.createObjectURL(file)` | Crea una URL temporal para mostrarlo |

---

### 4.11 Intersection Observer

Detecta cuándo un elemento **entra o sale de la zona visible**. Es la base de la carga diferida (*lazy loading*) y del scroll infinito.

```js
const observador = new IntersectionObserver((entradas) => {
  entradas.forEach((entrada) => {
    if (entrada.isIntersecting) {
      entrada.target.classList.add("visible");
      observador.unobserve(entrada.target); // dejar de observar
    }
  });
}, { threshold: 0.3 }); // 30 % visible

document.querySelectorAll(".animar").forEach((el) => observador.observe(el));
```

---

### 4.12 Otras APIs a conocer

| API | Idea en una línea |
|---|---|
| **Web Storage** | Guardar datos pequeños en el navegador → [[16 - Web Storage]] |
| **IndexedDB** | Base de datos del navegador para volúmenes grandes de datos |
| **Web Workers** | Ejecutar JS pesado en un hilo aparte sin bloquear la interfaz |
| **Notifications** | Mostrar avisos del sistema (requiere permiso) |
| **Page Visibility** | `document.visibilityState` y evento `visibilitychange` |
| **Fullscreen** | `elemento.requestFullscreen()` y `document.exitFullscreen()` |
| **Service Workers** | Caché y funcionamiento sin conexión (PWA) |
| **MutationObserver** | Detectar cambios en el DOM |
| **ResizeObserver** | Detectar cambios de tamaño de un elemento |
| **Web Share** | Abrir el menú de compartir del sistema |

---

## 5. Ejemplos prácticos

### Ejemplo básico: botón de pantalla completa

```html
<video id="peli" src="peli.mp4" controls></video>
<button id="fs">Pantalla completa</button>

<script>
  document.querySelector("#fs").addEventListener("click", () => {
    document.querySelector("#peli").requestFullscreen();
  });
</script>
```

### Ejemplo habitual: copiar texto con aviso

```html
<code id="comando">npm install express</code>
<button id="copiar">Copiar</button>
<p id="mensaje" aria-live="polite"></p>

<script>
  const mensaje = document.querySelector("#mensaje");

  document.querySelector("#copiar").addEventListener("click", async () => {
    try {
      const texto = document.querySelector("#comando").textContent;
      await navigator.clipboard.writeText(texto);
      mensaje.textContent = "Copiado ✔";
    } catch (error) {
      mensaje.textContent = "No se pudo copiar";
    }
  });
</script>
```

### Ejemplo completo: carga diferida de imágenes con Intersection Observer

```html
<img data-src="foto1.jpg" alt="Paisaje de montaña" width="600" height="400">
<img data-src="foto2.jpg" alt="Playa al atardecer" width="600" height="400">

<script>
  const imagenes = document.querySelectorAll("img[data-src]");

  if (!("IntersectionObserver" in window)) {
    // Alternativa: cargar todas directamente
    imagenes.forEach((img) => (img.src = img.dataset.src));
  } else {
    const observador = new IntersectionObserver((entradas, obs) => {
      entradas.forEach((entrada) => {
        if (!entrada.isIntersecting) return;
        const img = entrada.target;
        img.src = img.dataset.src;
        obs.unobserve(img);
      });
    }, { rootMargin: "200px" }); // empieza a cargar 200px antes

    imagenes.forEach((img) => observador.observe(img));
  }
</script>
```

> [!tip]
> Para el caso simple de imágenes, el atributo HTML `loading="lazy"` ya hace lo mismo **sin JavaScript**. El Intersection Observer compensa cuando se necesita más control (animaciones, scroll infinito, métricas).

---

## 6. Buenas prácticas

- **Comprobar el soporte** antes de usar una API (*feature detection*), nunca el nombre del navegador.
- **Preferir la solución declarativa en HTML** cuando existe (`<dialog>`, `popover`, `loading="lazy"`, `<details>`) antes de escribir JavaScript.
- **Pedir permisos solo cuando hacen falta** y como respuesta a una acción del usuario (un clic), no al cargar la página.
- **Gestionar siempre el rechazo del permiso** con `try/catch` o la función de error.
- **Dar alternativa** si la API no está disponible o el usuario la deniega.
- **Respetar la accesibilidad**: avisar con `aria-live`, mantener el foco, no depender solo del ratón en Drag and Drop.
- **Liberar recursos**: `clearWatch()`, `unobserve()`, `disconnect()`, `URL.revokeObjectURL()`.
- **Usar HTTPS**: muchas APIs solo funcionan en contextos seguros.
- **No guardar datos sensibles** en Web Storage.

---

## 7. Diferencias importantes

### `show()` vs `showModal()`

| | `show()` | `showModal()` |
|---|---|---|
| Bloquea el resto de la página | No | Sí |
| `::backdrop` | No | Sí |
| Cierra con `Esc` | No | Sí |
| Gestión del foco | Manual | Automática |

### `<dialog>` vs `popover`

| | `<dialog>` modal | `popover` |
|---|---|---|
| Interrumpe al usuario | Sí | No (es ligero) |
| Cierra al pulsar fuera | No | Sí (modo `auto`) |
| Uso típico | Confirmaciones, formularios | Menús, tooltips, desplegables |

### `getCurrentPosition()` vs `watchPosition()`

| | `getCurrentPosition()` | `watchPosition()` |
|---|---|---|
| Frecuencia | Una vez | Cada cambio de posición |
| Detener | No hace falta | `clearWatch(id)` |
| Consumo de batería | Bajo | Alto |

### `pushState()` vs `replaceState()`

| | `pushState()` | `replaceState()` |
|---|---|---|
| Añade entrada al historial | Sí | No (reemplaza la actual) |
| Botón «atrás» | Vuelve a la anterior | Salta la reemplazada |

### Web Storage vs IndexedDB

| | Web Storage | IndexedDB |
|---|---|---|
| Tipo de datos | Solo texto | Objetos, archivos, blobs |
| Capacidad | ~5 MB | Mucho mayor |
| Complejidad | Muy simple | Más compleja |
| Síncrona | Sí | No |

---

## 8. Casos especiales

### Contexto seguro (HTTPS)

APIs como Geolocation, Clipboard, Notifications o Service Workers solo funcionan en **HTTPS** (o en `localhost` durante el desarrollo). Se puede comprobar con:

```js
console.log(window.isSecureContext); // true / false
```

### Permisos denegados

Si el usuario rechaza un permiso, el navegador normalmente **no vuelve a preguntar**. La aplicación debe mostrar un mensaje explicando cómo reactivarlo o ofrecer otra vía.

```js
const estado = await navigator.permissions.query({ name: "geolocation" });
console.log(estado.state); // "granted" | "denied" | "prompt"
```

### APIs dentro de `<iframe>`

Algunas APIs están bloqueadas en iframes de otro origen salvo que se permitan con el atributo `allow`:

```html
<iframe src="https://ejemplo.com/mapa" allow="geolocation; fullscreen"></iframe>
```

### Privacidad y fingerprinting

Los navegadores pueden limitar la precisión o la disponibilidad de ciertas APIs por motivos de privacidad. No se debe asumir que los datos serán exactos.

### Autoplay bloqueado

`video.play()` o `audio.play()` pueden ser **rechazados** si el usuario no ha interactuado antes con la página. Como `play()` devuelve una Promesa, hay que capturar el error:

```js
video.play().catch(() => console.log("Reproducción bloqueada por el navegador"));
```

> [!warning] Obsoleto / legado
> Algunas APIs antiguas ya no deben usarse:
> - **Application Cache (`manifest`)** → sustituida por **Service Workers**.
> - **WebSQL** → sustituida por **IndexedDB**.
> - **`document.execCommand()`** → sustituido por la **Clipboard API** y por la edición nativa.
> - **Evento `unload`** → usar `pagehide` o `visibilitychange`.
> - **`document.write()`** → manipular el DOM con `append()`, `createElement()`, etc.

---

## 9. Resumen

- Las **APIs HTML** son capacidades del navegador accesibles desde JavaScript; el HTML aporta la estructura y la API aporta el comportamiento.
- Tres tipos: **de un elemento** (`<video>`, `<canvas>`, `<dialog>`), **del navegador** (`navigator`, `history`) y **observadoras** (`IntersectionObserver`…).
- Patrón de trabajo: **detectar soporte → usar → gestionar errores y permisos**.
- Antes de recurrir a JS, comprobar si existe una **solución declarativa** (`popover`, `<dialog>`, `loading="lazy"`).
- Muchas APIs exigen **HTTPS** y **permiso del usuario**; hay que contemplar que se deniegue.
- Las APIs modernas devuelven **Promesas**; las antiguas usan **callbacks**.
- Evitar APIs **obsoletas** (`execCommand`, WebSQL, AppCache, `unload`, `document.write`).
- Siguiente nota: [[11 - Buenas prácticas]]