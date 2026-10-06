# Pseudoclases y pseudoelementos

> [!info] ¿Qué es?
> Son dos tipos de selectores especiales que añaden más precisión a [[02 - Selectores]]:
> - Una **pseudoclase** (`:`) selecciona un elemento **según su estado o su posición** (con el ratón encima, el primero de la lista, un campo inválido…).
> - Un **pseudoelemento** (`::`) selecciona **una parte concreta** de un elemento (la primera letra, el contenido que se añade antes o después…).

---

## 1. Antes de empezar

Para entender esta nota conviene saber:

- Cómo funcionan los selectores básicos (clase, etiqueta, ID, combinadores) (ver [[02 - Selectores]]).
- Qué es el árbol del HTML: padres, hijos y hermanos.
- Cómo funcionan las transiciones, porque suelen combinarse con `:hover` (ver [[11 - Transiciones y animaciones]]).

HTML que usaremos en los ejemplos:

```html
<nav class="menu">
  <a href="#" class="enlace">Inicio</a>
  <a href="#" class="enlace">Blog</a>
  <a href="#" class="enlace">Contacto</a>
</nav>

<ul class="lista">
  <li>Uno</li>
  <li>Dos</li>
  <li>Tres</li>
  <li>Cuatro</li>
</ul>

<form class="formulario">
  <input type="text" required placeholder="Nombre">
  <input type="email" placeholder="Correo">
  <input type="checkbox" id="acepto">
  <button type="submit" disabled>Enviar</button>
</form>

<p class="texto">Un párrafo de ejemplo con varias líneas de texto.</p>
```

---

## 2. Concepto fundamental

**Diferencia clave:**

| | Pseudoclase | Pseudoelemento |
|---|---|---|
| Símbolo | **`:`** (dos puntos) | **`::`** (cuatro puntos) |
| Selecciona | Un **elemento** en cierto **estado o posición** | Una **parte** de un elemento o contenido **generado** |
| Ejemplo | `a:hover`, `li:first-child` | `p::first-line`, `div::before` |

Se escriben **pegados** al selector al que acompañan:

```css
a:hover { color: orange; }
p::first-letter { font-size: 2em; }
```

> [!tip] Idea clave
> Las pseudoclases responden a la pregunta *"¿en qué situación está este elemento?"*. Los pseudoelementos responden a *"¿qué parte de este elemento quiero estilar?"*.

---

## 3. Sintaxis / estructura

```css
selector:pseudoclase {
  propiedad: valor;
}

selector::pseudoelemento {
  propiedad: valor;
}
```

Se pueden **combinar** varias pseudoclases y añadir un pseudoelemento al final:

```css
a:not(.activo):hover::after {
  content: " →";
}
```

---

## 4. Elementos / propiedades / características

## 4.A Pseudoclases

### 4.1 Pseudoclases de enlaces y de interacción

| Pseudoclase | Se aplica cuando... |
|---|---|
| `:link` | Un enlace **aún no visitado** |
| `:visited` | Un enlace **ya visitado** |
| `:hover` | El **ratón está encima** |
| `:active` | Se está **pulsando** (mientras se mantiene el clic) |
| `:focus` | El elemento tiene el **foco** (por ratón o teclado) |
| `:focus-visible` | Tiene el foco **y el navegador decide mostrarlo** (normalmente con teclado) |
| `:focus-within` | **Un hijo** del elemento tiene el foco |

```css
a {
  color: #1e3a8a;
}

a:visited {
  color: #6b21a8;
}

a:hover {
  color: crimson;
}

a:active {
  color: darkred;
}

button:focus-visible {
  outline: 3px solid #4a90e2;
  outline-offset: 2px;
}

.formulario:focus-within {
  border-color: #4a90e2;       /* el formulario se resalta si un campo tiene el foco */
}
```

> [!note] El orden "LVHA"
> Si usas varias pseudoclases de enlace, escríbelas en este orden: **`:link`, `:visited`, `:hover`, `:active`**. Como tienen la misma especificidad, la última gana, y el orden evita que una anule a otra.

> [!warning]
> Por privacidad, `:visited` **solo permite cambiar colores**. El navegador no deja usar otras propiedades para que una web no pueda saber qué páginas has visitado.

### 4.2 Pseudoclases de formularios

| Pseudoclase | Se aplica cuando... |
|---|---|
| `:checked` | Casilla, radio u opción **marcada** |
| `:disabled` | Campo **desactivado** |
| `:enabled` | Campo **activo** |
| `:required` | Campo **obligatorio** |
| `:optional` | Campo **no obligatorio** |
| `:valid` | El contenido **es válido** |
| `:invalid` | El contenido **no es válido** |
| `:placeholder-shown` | Se **ve el texto de ayuda** (el campo está vacío) |
| `:read-only` / `:read-write` | Solo lectura / editable |
| `:indeterminate` | Casilla en estado intermedio |
| `:user-invalid` | Inválido **después de que el usuario haya interactuado** |

```css
input:required {
  border-left: 3px solid crimson;
}

input:invalid {
  border-color: crimson;
}

input:valid {
  border-color: seagreen;
}

button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

input[type="checkbox"]:checked + label {
  font-weight: bold;
}
```

> [!tip]
> `:invalid` se activa **desde el principio** (un campo obligatorio vacío ya es inválido y se vería en rojo antes de escribir nada). Para evitarlo, usa **`:user-invalid`** o combina con `:not(:placeholder-shown)`.

### 4.3 Pseudoclases estructurales (según la posición)

Seleccionan elementos según **dónde están** entre sus hermanos.

| Pseudoclase | Selecciona |
|---|---|
| `:first-child` | El **primer** hijo de su padre |
| `:last-child` | El **último** hijo |
| `:only-child` | El **único** hijo |
| `:nth-child(n)` | El hijo en la posición indicada |
| `:nth-last-child(n)` | Igual, **contando desde el final** |
| `:first-of-type` | El primero **de su etiqueta** |
| `:last-of-type` | El último de su etiqueta |
| `:only-of-type` | El único de su etiqueta |
| `:nth-of-type(n)` | El enésimo de su etiqueta |
| `:empty` | Elementos **sin contenido** (ni texto ni hijos) |
| `:root` | El elemento raíz (`<html>`) |

```css
li:first-child {
  font-weight: bold;
}

li:last-child {
  border-bottom: none;
}

p:empty {
  display: none;
}
```

#### La fórmula de `:nth-child()`

Acepta un **número**, una **palabra** o una **fórmula `an+b`**:

| Escribes | Selecciona |
|---|---|
| `:nth-child(3)` | Solo el tercero |
| `:nth-child(odd)` | Los impares (1, 3, 5…) |
| `:nth-child(even)` | Los pares (2, 4, 6…) |
| `:nth-child(3n)` | Cada tercero (3, 6, 9…) |
| `:nth-child(3n+1)` | 1, 4, 7, 10… |
| `:nth-child(n+3)` | Del tercero en adelante |
| `:nth-child(-n+3)` | Solo los **tres primeros** |
| `:nth-child(2n+1 of .item)` | Con el filtro de selector `of` |

```css
/* Filas de tabla con rayas */
tr:nth-child(even) {
  background: #f4f4f4;
}

/* Primeras 3 tarjetas destacadas */
.tarjeta:nth-child(-n+3) {
  border-color: gold;
}
```

> [!note] `:nth-child` vs `:nth-of-type`
> `:nth-child(2)` cuenta **todos los hermanos** sin importar la etiqueta. `:nth-of-type(2)` cuenta solo los de **la misma etiqueta**. Si el padre mezcla `<h2>`, `<p>` y `<img>`, el resultado es distinto.

### 4.4 Pseudoclases de lógica y de relación

Ya vistas en [[02 - Selectores]]; aquí un resumen:

| Pseudoclase | Selecciona |
|---|---|
| `:not(x)` | Lo que **no** cumple `x` |
| `:is(x, y)` | Lo que cumple **cualquiera** de `x` o `y` |
| `:where(x, y)` | Igual que `:is()`, pero con **especificidad 0** |
| `:has(x)` | Los elementos que **contienen** a `x` |

```css
.enlace:not(.activo) {
  opacity: 0.7;
}

:is(h1, h2, h3):hover {
  color: crimson;
}

.tarjeta:has(img) {
  padding: 0;
}

label:has(input:checked) {
  background: #e6f4ea;
}
```

### 4.5 Otras pseudoclases útiles

| Pseudoclase | Se aplica cuando... |
|---|---|
| `:target` | El elemento es el **destino del `#ancla`** de la URL |
| `:lang(es)` | El elemento está en el idioma indicado |
| `:dir(rtl)` | El texto va de derecha a izquierda |
| `:fullscreen` | El elemento está en pantalla completa |
| `:modal` | Una ventana `<dialog>` abierta como modal |
| `:popover-open` | Un popover abierto |
| `:defined` | Un elemento personalizado ya registrado |

```css
section:target {
  background: #fffbe6;          /* resalta la sección a la que apunta el enlace #seccion */
}
```

## 4.B Pseudoelementos

### 4.6 `::before` y `::after`

Crean un **elemento virtual** como primer o último hijo del elemento. **Solo existen si tienen la propiedad `content`**, aunque sea vacía.

```css
.enlace-externo::after {
  content: " ↗";
  font-size: 0.8em;
}

.cita::before {
  content: "“";
  font-size: 2rem;
  color: #999;
}
```

Usos habituales:

- Añadir **iconos o símbolos** decorativos.
- Crear **formas** (líneas, triángulos, círculos) sin añadir HTML.
- Dibujar **capas** sobre un elemento (con `position: absolute`).

```css
.titulo::after {
  content: "";                  /* vacío, pero necesario */
  display: block;
  width: 60px;
  height: 4px;
  margin-top: 0.5rem;
  background: crimson;          /* una línea decorativa bajo el título */
}
```

También se puede insertar el valor de un atributo con `attr()`:

```css
a[href^="http"]::after {
  content: " (" attr(href) ")";   /* muestra la URL después del enlace */
}
```

> [!warning]
> El contenido de `::before` y `::after` **no forma parte del HTML**: no se puede seleccionar con el ratón y algunos lectores de pantalla lo leen y otros no. **No pongas información importante ahí**; úsalo para decoración.

### 4.7 Pseudoelementos de texto

| Pseudoelemento | Selecciona |
|---|---|
| `::first-letter` | La **primera letra** del texto |
| `::first-line` | La **primera línea** del texto |
| `::selection` | El texto que el usuario **selecciona** con el ratón |

```css
.texto::first-letter {
  font-size: 3em;
  float: left;
  line-height: 1;
  margin-right: 0.1em;            /* letra capital */
}

.texto::first-line {
  font-weight: bold;
}

::selection {
  background: crimson;
  color: white;
}
```

> [!note]
> `::first-letter` y `::first-line` solo funcionan en elementos de **bloque**, y admiten un conjunto limitado de propiedades (de texto, fuente, color, fondo, márgenes...).

### 4.8 Pseudoelementos de formularios y listas

| Pseudoelemento | Selecciona |
|---|---|
| `::placeholder` | El **texto de ayuda** de un campo |
| `::marker` | La **viñeta o número** de una lista |
| `::file-selector-button` | El botón de un campo de **archivo** |
| `::backdrop` | El **fondo** de un `<dialog>` modal o un elemento a pantalla completa |

```css
input::placeholder {
  color: #999;
  font-style: italic;
}

li::marker {
  color: crimson;
  font-weight: bold;
}

dialog::backdrop {
  background: rgb(0 0 0 / 0.6);
}
```

### 4.9 Otros pseudoelementos recientes

| Pseudoelemento | Para qué sirve |
|---|---|
| `::part()` | Estilar partes expuestas de un componente web |
| `::slotted()` | Estilar contenido insertado en un `<slot>` |
| `::cue` | Subtítulos de vídeo |
| `::highlight()` | Resaltados personalizados creados con JavaScript |
| `::view-transition-*` | Transiciones entre páginas o vistas (ver [[14 - CSS moderno]]) |

---

## 5. Ejemplos prácticos

### Ejemplo básico

Estados de un enlace:

```css
a {
  color: #1e3a8a;
}

a:hover {
  color: crimson;
}

a:focus-visible {
  outline: 3px solid #4a90e2;
}
```

### Ejemplo habitual

Lista con filas alternas, sin borde en el último elemento, y viñetas de color:

```css
.lista li {
  padding: 0.6rem 0;
  border-bottom: 1px solid #ddd;
}

.lista li:nth-child(even) {
  background: #fafafa;
}

.lista li:last-child {
  border-bottom: none;
}

.lista li::marker {
  color: crimson;
}
```

### Ejemplo completo

Formulario con estados de validación y menú con indicador animado:

```css
/* Campos del formulario */
.formulario input {
  padding: 0.6rem 0.8rem;
  border: 2px solid #ccc;
  border-radius: 6px;
  transition: border-color 0.2s;
}

.formulario input::placeholder {
  color: #aaa;
}

.formulario input:focus-visible {
  outline: none;
  border-color: #4a90e2;
}

/* Solo marcamos error cuando el usuario ya escribió algo */
.formulario input:not(:placeholder-shown):invalid {
  border-color: crimson;
}

.formulario input:not(:placeholder-shown):valid {
  border-color: seagreen;
}

.formulario input:required + label::after {
  content: " *";
  color: crimson;
}

.formulario button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

/* Menú con línea inferior animada */
.menu .enlace {
  position: relative;
  text-decoration: none;
  color: #222;
}

.menu .enlace::after {
  content: "";
  position: absolute;
  left: 0;
  bottom: -4px;
  width: 100%;
  height: 2px;
  background: crimson;
  transform: scaleX(0);
  transform-origin: left;
  transition: transform 0.3s ease;
}

.menu .enlace:hover::after,
.menu .enlace:focus-visible::after {
  transform: scaleX(1);
}

/* Separador entre enlaces, menos tras el último */
.menu .enlace:not(:last-child)::before {
  content: "·";
  position: absolute;
  right: -1rem;
  color: #aaa;
}
```

---

## 6. Buenas prácticas

- **Usa `:focus-visible`** para mostrar el foco al navegar con teclado; **no quites el `outline`** sin dar otra señal visible.
- **Aplica a `:focus-visible` lo mismo que a `:hover`**, para que la experiencia sea igual con teclado y ratón.
- **Respeta el orden `:link`, `:visited`, `:hover`, `:active`.**
- **Usa `:hover` solo para mejoras visuales**, nunca para funciones imprescindibles (en móvil no hay hover).
- **No pongas contenido importante en `::before` o `::after`**: es decorativo.
- **Recuerda `content: ""`** al crear formas con pseudoelementos.
- **Evita estilar `:invalid` desde el inicio**; combina con `:not(:placeholder-shown)` o usa `:user-invalid`.
- **Prefiere `:is()`, `:where()` y `:not()`** para agrupar y evitar repeticiones.
- **No abuses de `:nth-child` complejos**: si necesitas muchos, mejor añade una clase.
- **Usa los dos puntos dobles `::`** para pseudoelementos, para diferenciarlos de las pseudoclases.

---

## 7. Diferencias importantes

| Comparación | Diferencia |
|---|---|
| `:` vs `::` | Un `:` es **pseudoclase** (estado o posición). Dos `::` son **pseudoelemento** (una parte o contenido generado). |
| `:focus` vs `:focus-visible` | `:focus` se activa con cualquier foco (también con clic); `:focus-visible` solo cuando conviene mostrarlo (teclado). |
| `:focus` vs `:focus-within` | `:focus` es el propio elemento; `:focus-within` es un padre cuando uno de sus hijos tiene el foco. |
| `:hover` vs `:active` | `:hover` es con el ratón encima; `:active` es mientras se pulsa. |
| `:nth-child` vs `:nth-of-type` | `child` cuenta todos los hermanos; `of-type` solo los de la misma etiqueta. |
| `:first-child` vs `:first-of-type` | `:first-child` exige ser el primero de **todos**; `:first-of-type` el primero **de su etiqueta**. |
| `:is()` vs `:where()` | Lo mismo, pero `:is()` aporta especificidad y `:where()` aporta 0. |
| `::before` vs `::after` | `::before` va **antes** del contenido; `::after` **después**. |
| `::first-letter` vs `::first-line` | La primera es la letra inicial; la segunda toda la primera línea (que cambia con el ancho de pantalla). |
| `:invalid` vs `:user-invalid` | `:invalid` actúa siempre; `:user-invalid` solo tras la interacción del usuario. |

---

## 8. Casos especiales

- **`::before` y `::after` no funcionan en elementos "reemplazados"** como `<img>`, `<input>` o `<br>`, porque no tienen contenido interior.
- **Por defecto son `inline`**: para darles ancho, alto o márgenes verticales hay que usar `display: block` o `inline-block`, o posicionarlos con `position: absolute`.
- **`content` acepta**: texto entre comillas, `attr(atributo)`, `url(...)`, contadores (`counter()`), comillas (`open-quote`) o una cadena vacía `""`.
- **Contadores**: con `counter-reset` y `counter-increment` se puede numerar elementos automáticamente.

```css
  .lista { counter-reset: paso; }
  .lista li::before {
    counter-increment: paso;
    content: counter(paso) ". ";
  }
```
- **Especificidad**: las pseudoclases cuentan como una **clase** (nivel 3); los pseudoelementos cuentan como una **etiqueta** (nivel 4) (ver [[02 - Selectores]]).
- **No se pueden agrupar en una sola lista** pseudoelementos que el navegador no entienda: si uno no es válido (por ejemplo, de otro navegador), se ignora toda la regla. Escríbelos en reglas separadas.
- **`::selection` admite pocas propiedades**: sobre todo `color`, `background-color`, `text-decoration` y `text-shadow`.
- **Los pseudoelementos no se pueden combinar** (`p::before::after` no es válido), salvo algunos casos concretos como `::before::marker`.
- **`:hover` en pantallas táctiles**: puede "quedarse pegado" tras tocar. Usa `@media (hover: hover)` para limitarlo a dispositivos con ratón.
- **`:has()` es costoso** si el selector es muy general; úsalo con selectores concretos.
- **`:empty` no cuenta** un elemento con espacios o saltos de línea como vacío en navegadores antiguos; hoy los espacios en blanco se ignoran, pero un comentario HTML sí es válido.

> [!warning] Obsoleto / legado
> La sintaxis antigua de **un solo `:`** en pseudoelementos (`:before`, `:after`, `:first-line`, `:first-letter`) funciona por compatibilidad, pero se recomienda usar **`::`**. Los prefijos `::-webkit-input-placeholder`, `::-moz-placeholder` o `:-ms-input-placeholder` ya no son necesarios: basta con `::placeholder`.

---

## 9. Resumen

- **Pseudoclase (`:`)**: selecciona un elemento por su **estado o posición**. **Pseudoelemento (`::`)**: selecciona una **parte** o contenido **generado**.
- Estados de interacción: **`:hover`**, **`:active`**, **`:focus`**, **`:focus-visible`**, **`:focus-within`**. Orden de enlaces: **`:link`, `:visited`, `:hover`, `:active`**.
- Formularios: **`:checked`**, **`:disabled`**, **`:required`**, **`:valid`**, **`:invalid`**, **`:placeholder-shown`**, **`:user-invalid`**.
- Posición: **`:first-child`**, **`:last-child`**, **`:nth-child(an+b)`**, **`:nth-of-type()`**, **`:empty`**, **`:root`**.
- Lógica: **`:not()`**, **`:is()`**, **`:where()`**, **`:has()`**.
- **`::before`** y **`::after`** crean contenido decorativo y necesitan **`content`**.
- Pseudoelementos de texto y formularios: **`::first-letter`**, **`::first-line`**, **`::selection`**, **`::placeholder`**, **`::marker`**, **`::backdrop`**.
- No pongas **información importante** en pseudoelementos.
- Usa **`:focus-visible`** y no elimines el contorno del foco sin una alternativa.
- Las pseudoclases pesan como una **clase**; los pseudoelementos, como una **etiqueta**.