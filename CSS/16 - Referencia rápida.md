# Referencia rápida de CSS

> [!info] ¿Qué es?
> Una **chuleta** con lo más importante de CSS en tablas y fragmentos listos para consultar: selectores, unidades, propiedades, valores habituales y soluciones frecuentes. Para entender algo en profundidad, consulta la nota completa de cada tema (enlazadas en cada apartado).

---

## 1. Cómo conectar CSS con HTML

```html
<!-- Archivo externo (recomendado) -->
<link rel="stylesheet" href="estilos.css">

<!-- Dentro del HTML -->
<style>
  p { color: navy; }
</style>

<!-- En línea (evitar) -->
<p style="color: navy;">Texto</p>
```

```css
selector {
  propiedad: valor;
}
```

---

## 2. Selectores (ver [[02 - Selectores]])

| Selector | Qué selecciona | Ejemplo |
|---|---|---|
| `*` | Todos los elementos | `* { margin: 0; }` |
| `p` | Por etiqueta | `p { color: gray; }` |
| `.clase` | Por clase | `.tarjeta { }` |
| `#id` | Por ID | `#cabecera { }` |
| `a.b` | Elemento con una clase | `p.intro { }` |
| `.a.b` | Con **ambas** clases | `.tarjeta.destacada { }` |
| `a, b` | Lista (cualquiera) | `h1, h2 { }` |
| `a b` | Descendiente | `nav a { }` |
| `a > b` | Hijo directo | `ul > li { }` |
| `a + b` | Hermano inmediato | `h2 + p { }` |
| `a ~ b` | Todos los hermanos siguientes | `h2 ~ p { }` |
| `[attr]` | Tiene el atributo | `[disabled]` |
| `[attr="v"]` | Valor exacto | `[type="email"]` |
| `[attr^="v"]` | Empieza por | `[href^="https"]` |
| `[attr$="v"]` | Termina en | `[href$=".pdf"]` |
| `[attr*="v"]` | Contiene | `[href*="ejemplo"]` |

### Especificidad (de mayor a menor)

| Nivel | Qué cuenta |
|---|---|
| 1 | Estilo en línea (`style="..."`) |
| 2 | IDs (`#x`) |
| 3 | Clases, atributos y pseudoclases (`.x`, `[x]`, `:hover`) |
| 4 | Etiquetas y pseudoelementos (`p`, `::before`) |

A igual especificidad, gana la regla **más abajo**. `!important` salta todo (evitar).

---

## 3. Pseudoclases y pseudoelementos (ver [[12 - Pseudoclases y pseudoelementos]])

| Pseudoclase | Se aplica cuando... |
|---|---|
| `:hover` | Ratón encima |
| `:active` | Se está pulsando |
| `:focus` / `:focus-visible` | Tiene el foco / foco visible (teclado) |
| `:focus-within` | Un hijo tiene el foco |
| `:visited` / `:link` | Enlace visitado / no visitado |
| `:checked` | Casilla o radio marcado |
| `:disabled` / `:enabled` | Campo desactivado / activo |
| `:required` / `:optional` | Obligatorio / opcional |
| `:valid` / `:invalid` | Válido / inválido |
| `:placeholder-shown` | Se muestra el texto de ayuda |
| `:first-child` / `:last-child` | Primer / último hijo |
| `:nth-child(2n+1)` | Posición según fórmula |
| `:nth-of-type(n)` | Enésimo de su etiqueta |
| `:empty` | Sin contenido |
| `:not(x)` | Todo menos `x` |
| `:is(x, y)` / `:where(x, y)` | Cualquiera de... (`where` = especificidad 0) |
| `:has(x)` | Contiene a `x` |
| `:root` | Elemento raíz (`html`) |
| `:target` | Destino del `#ancla` de la URL |

| Pseudoelemento | Selecciona |
|---|---|
| `::before` / `::after` | Contenido generado antes / después (necesita `content`) |
| `::first-letter` | Primera letra |
| `::first-line` | Primera línea |
| `::selection` | Texto seleccionado |
| `::placeholder` | Texto de ayuda del campo |
| `::marker` | Viñeta o número de lista |
| `::backdrop` | Fondo de un `<dialog>` modal |

---

## 4. Box Model (ver [[03 - Box Model]])

```text
margin → border → padding → content
```

| Propiedad | Qué hace |
|---|---|
| `width` / `height` | Tamaño del contenido |
| `min-width`, `max-width` | Límites de ancho |
| `min-height`, `max-height` | Límites de alto |
| `padding` | Espacio **interior** |
| `border` | Borde: `1px solid #ccc` |
| `border-radius` | Esquinas redondeadas |
| `margin` | Espacio **exterior** (transparente) |
| `box-sizing: border-box` | `width` incluye padding y borde |
| `overflow` | `visible`, `hidden`, `scroll`, `auto` |
| `outline` | Contorno (no ocupa espacio) |

**Abreviaturas (sentido del reloj: arriba, derecha, abajo, izquierda):**

| Escribes | Significado |
|---|---|
| `margin: 10px` | Los 4 lados |
| `margin: 10px 20px` | Vertical, horizontal |
| `margin: 10px 20px 30px` | Arriba, horizontal, abajo |
| `margin: 10px 20px 30px 40px` | Arriba, derecha, abajo, izquierda |

---

## 5. Unidades y valores (ver [[04 - Unidades y valores]])

| Unidad | Relativa a... | Uso típico |
|---|---|---|
| `px` | Fija | Bordes, sombras |
| `rem` | Tamaño de letra del `html` | Texto, espacios |
| `em` | Tamaño de letra del elemento | Espacios ligados al texto |
| `%` | El padre (depende de la propiedad) | Anchos relativos |
| `vw` / `vh` | Ancho / alto de la ventana | Secciones a pantalla completa |
| `dvh` / `svh` / `lvh` | Alto dinámico / pequeño / grande | Pantalla completa en móvil |
| `ch` | Ancho del carácter "0" | Ancho de párrafos |
| `fr` | Fracción del espacio libre en Grid | Columnas de Grid |
| `s`, `ms` | Tiempo | Transiciones y animaciones |
| `deg`, `turn` | Ángulos | `rotate()` |

| Función | Qué hace |
|---|---|
| `calc(100% - 20px)` | Operaciones con unidades mixtas |
| `min(a, b)` / `max(a, b)` | El menor / el mayor |
| `clamp(mín, ideal, máx)` | Valor con límites |
| `var(--x, respaldo)` | Usa una variable |

Valores globales: `inherit`, `initial`, `unset`, `revert`.

---

## 6. Colores y fondos (ver [[05 - Colores y fondos]])

| Formato | Ejemplo |
|---|---|
| Nombre | `crimson` |
| Hexadecimal | `#e11d48`, `#f00`, `#e11d4880` |
| RGB | `rgb(225 29 72)`, `rgb(0 0 0 / 50%)` |
| HSL | `hsl(350 80% 50%)` |
| OKLCH | `oklch(60% 0.2 20)` |
| Especiales | `transparent`, `currentColor` |
| Mezcla | `color-mix(in srgb, red 50%, white)` |

| Propiedad | Valores / uso |
|---|---|
| `color` | Color del texto |
| `background-color` | Color de fondo |
| `background-image` | `url("img.jpg")`, `linear-gradient(...)` |
| `background-repeat` | `no-repeat`, `repeat-x`, `repeat-y` |
| `background-position` | `center`, `top right`, `50% 20%` |
| `background-size` | `cover`, `contain`, `200px` |
| `background-attachment` | `scroll`, `fixed` |
| `opacity` | `0` a `1` (afecta a todo el elemento) |
| `box-shadow` | `0 4px 12px rgb(0 0 0 / 0.2)`; `inset` para interior |
| `accent-color` | Color de casillas y radios |

```css
/* Degradados */
background: linear-gradient(135deg, #f06, #4a90e2);
background: radial-gradient(circle, white, #333);
background: conic-gradient(red, yellow, green, red);

/* Fondo con imagen y capa oscura */
background:
  linear-gradient(rgb(0 0 0 / 0.5), rgb(0 0 0 / 0.5)),
  url("img/fondo.jpg") center / cover no-repeat;
```

---

## 7. Texto y fuentes (ver [[06 - Texto y fuentes]])

| Propiedad | Valores habituales |
|---|---|
| `font-family` | `"Inter", system-ui, sans-serif` |
| `font-size` | `1rem`, `clamp(2rem, 5vw, 3rem)` |
| `font-weight` | `400`, `700`, `100`-`900`, `bold` |
| `font-style` | `normal`, `italic` |
| `line-height` | `1.5` (sin unidad) |
| `text-align` | `left`, `center`, `right`, `justify` |
| `text-decoration` | `none`, `underline`, `line-through` |
| `text-transform` | `uppercase`, `lowercase`, `capitalize` |
| `letter-spacing` | `0.05em` |
| `word-spacing` | `0.2em` |
| `text-indent` | `2rem` |
| `white-space` | `normal`, `nowrap`, `pre`, `pre-wrap` |
| `overflow-wrap` | `break-word` |
| `text-overflow` | `ellipsis` (con `overflow: hidden` y `nowrap`) |
| `text-shadow` | `2px 2px 4px rgb(0 0 0 / 0.4)` |
| `text-wrap` | `balance`, `pretty` |

```css
/* Cargar una fuente propia */
@font-face {
  font-family: "MiFuente";
  src: url("mifuente.woff2") format("woff2");
  font-display: swap;
}

/* Texto en una línea con "..." */
.una-linea {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}
```

---

## 8. Display y Flexbox (ver [[07 - Flexbox]])

| `display` | Efecto |
|---|---|
| `block` | Ocupa todo el ancho, línea nueva |
| `inline` | Solo lo que mide; ignora `width`/`height` |
| `inline-block` | En línea, pero acepta `width`/`height` |
| `none` | No se muestra ni ocupa espacio |
| `flex` / `inline-flex` | Contenedor Flexbox |
| `grid` / `inline-grid` | Contenedor Grid |

**Contenedor flex:**

| Propiedad | Valores |
|---|---|
| `flex-direction` | `row`, `row-reverse`, `column`, `column-reverse` |
| `flex-wrap` | `nowrap`, `wrap`, `wrap-reverse` |
| `justify-content` | `flex-start`, `center`, `flex-end`, `space-between`, `space-around`, `space-evenly` (eje principal) |
| `align-items` | `stretch`, `flex-start`, `center`, `flex-end`, `baseline` (eje transversal) |
| `align-content` | Reparto de varias líneas |
| `gap` | `1rem`, `1rem 2rem` |

**Ítems flex:**

| Propiedad | Qué hace |
|---|---|
| `flex: 1` | Reparte el espacio por igual |
| `flex: 0 0 250px` | Ancho fijo |
| `flex-grow` / `flex-shrink` / `flex-basis` | Crecer / encoger / tamaño base |
| `align-self` | Alineación individual |
| `order` | Orden visual |
| `margin-left: auto` | Empuja el ítem al extremo |

---

## 9. Grid (ver [[08 - Grid]])

| Propiedad | Ejemplo |
|---|---|
| `grid-template-columns` | `repeat(3, 1fr)`, `200px 1fr` |
| `grid-template-rows` | `auto 1fr auto` |
| `gap` | `1rem` |
| `grid-template-areas` | `"cabecera cabecera" "lateral contenido"` |
| `grid-area` | `cabecera` (en el hijo) |
| `grid-column` | `1 / 3`, `span 2`, `1 / -1` |
| `grid-row` | `1 / 3`, `span 2` |
| `place-items` | `center` |
| `place-content` | `center`, `space-between` |
| `grid-auto-flow` | `row`, `column`, `dense` |
| `grid-auto-rows` | `minmax(100px, auto)` |

```css
/* Galería adaptable sin media queries */
.galeria {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 1rem;
}
```

---

## 10. Position (ver [[09 - Position]])

| Valor | Comportamiento |
|---|---|
| `static` | Flujo normal (por defecto) |
| `relative` | Desplazable desde su sitio; referencia para hijos `absolute` |
| `absolute` | Fuera del flujo; respecto al ancestro posicionado más cercano |
| `fixed` | Fijo respecto a la ventana |
| `sticky` | `relative` hasta llegar al límite; después se pega |

| Propiedad | Uso |
|---|---|
| `top`, `right`, `bottom`, `left` | Distancias |
| `inset: 0` | Los cuatro a 0 |
| `z-index` | Orden de apilamiento (solo en elementos posicionados o ítems flex/grid) |

---

## 11. Responsive (ver [[10 - Responsive Design]])

```html
<meta name="viewport" content="width=device-width, initial-scale=1">
```

```css
/* Mobile-first */
@media (min-width: 768px) { /* tableta y más */ }
@media (min-width: 1024px) { /* portátil y más */ }

/* Preferencias del usuario */
@media (prefers-color-scheme: dark) { }
@media (prefers-reduced-motion: reduce) { }
@media (hover: hover) { }
@media print { }

/* Container query */
.zona { container-type: inline-size; }
@container (min-width: 500px) { }
```

```css
/* Imágenes fluidas */
img {
  max-width: 100%;
  height: auto;
}
```

---

## 12. Transiciones, animaciones y transform (ver [[11 - Transiciones y animaciones]])

```css
/* Transición */
.boton {
  transition: background-color 0.3s ease;
}

/* Animación */
@keyframes aparecer {
  from { opacity: 0; transform: translateY(20px); }
  to   { opacity: 1; transform: translateY(0); }
}

.tarjeta {
  animation: aparecer 0.6s ease-out both;
}
```

| Propiedad | Valores |
|---|---|
| `transition` | `propiedad duración curva retraso` |
| `animation` | `nombre duración curva retraso repeticiones dirección fill-mode` |
| `animation-iteration-count` | `1`, `3`, `infinite` |
| `animation-direction` | `normal`, `reverse`, `alternate` |
| `animation-fill-mode` | `none`, `forwards`, `backwards`, `both` |
| Curvas | `ease`, `linear`, `ease-in`, `ease-out`, `ease-in-out`, `cubic-bezier()`, `steps()` |

| `transform` | Efecto |
|---|---|
| `translate(x, y)` | Mover |
| `scale(n)` | Escalar |
| `rotate(45deg)` | Girar |
| `skew(10deg)` | Inclinar |
| `transform-origin` | Punto de origen |

Anima preferiblemente **`transform`** y **`opacity`**.

---

## 13. Variables y funciones (ver [[13 - Variables y funciones]])

```css
:root {
  --color-principal: #e11d48;
  --espacio: 1rem;
}

.boton {
  background: var(--color-principal);
  padding: calc(var(--espacio) * 2);
  color: var(--color-texto, white);   /* con respaldo */
}
```

```js
// Cambiar una variable con JavaScript
document.documentElement.style.setProperty("--color-principal", "royalblue");
```

---

## 14. CSS moderno (ver [[14 - CSS moderno]])

| Novedad | Ejemplo |
|---|---|
| Anidamiento | `.tarjeta { &:hover { } h2 { } }` |
| `:has()` | `.tarjeta:has(img) { }` |
| Container queries | `@container (min-width: 500px) { }` |
| Capas | `@layer reset, base, componentes;` |
| `@supports` | `@supports (display: grid) { }` |
| Propiedades lógicas | `margin-inline`, `padding-block` |
| `aspect-ratio` | `aspect-ratio: 16 / 9;` |
| `light-dark()` | `color: light-dark(#222, #eee);` |
| `color-mix()` | `color-mix(in srgb, red 40%, white)` |
| `text-wrap` | `text-wrap: balance;` |
| `scroll-behavior` | `scroll-behavior: smooth;` |
| `scroll-snap` | `scroll-snap-type: x mandatory;` |
| `backdrop-filter` | `backdrop-filter: blur(10px);` |

---

## 15. Soluciones rápidas (*snippets*)

### Reset mínimo

```css
*,
*::before,
*::after {
  box-sizing: border-box;
}

body {
  margin: 0;
  line-height: 1.6;
}

img,
video,
svg {
  display: block;
  max-width: 100%;
  height: auto;
}

input,
button,
textarea,
select {
  font: inherit;
}
```

### Centrar

```css
/* Horizontalmente (bloque con ancho) */
.caja { width: min(90%, 600px); margin-inline: auto; }

/* Todo en el centro con Flexbox */
.centrado { display: flex; justify-content: center; align-items: center; }

/* Todo en el centro con Grid (la más corta) */
.centrado { display: grid; place-items: center; }

/* Texto */
.texto { text-align: center; }

/* Absoluto en el centro */
.centrado { position: absolute; inset: 0; margin: auto; width: 300px; height: 200px; }
```

### Contenedor centrado y con límite

```css
.contenedor {
  width: min(100% - 2rem, 1100px);
  margin-inline: auto;
}
```

### Pie de página siempre abajo

```css
body {
  min-height: 100dvh;
  display: flex;
  flex-direction: column;
}

main {
  flex: 1;
}
```

### Cabecera pegada arriba

```css
.cabecera {
  position: sticky;
  top: 0;
  z-index: 100;
  background: white;
}
```

### Cubrir toda la pantalla

```css
.fondo {
  position: fixed;
  inset: 0;
}
```

### Proporción fija (vídeo, imagen)

```css
.video {
  width: 100%;
  aspect-ratio: 16 / 9;
}
```

### Imagen que llena su caja sin deformarse

```css
.imagen {
  width: 100%;
  height: 200px;
  object-fit: cover;
}
```

### Texto cortado con "..." (1 línea y varias líneas)

```css
.una-linea {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.tres-lineas {
  display: -webkit-box;
  -webkit-line-clamp: 3;
  -webkit-box-orient: vertical;
  overflow: hidden;
}
```

### Texto oculto solo visualmente (para lectores de pantalla)

```css
.solo-lectores {
  position: absolute;
  width: 1px;
  height: 1px;
  margin: -1px;
  overflow: hidden;
  clip-path: inset(50%);
  white-space: nowrap;
}
```

### Tarjeta con elevación al pasar el ratón

```css
.tarjeta {
  transition: transform 0.25s ease, box-shadow 0.25s ease;
}

@media (hover: hover) {
  .tarjeta:hover {
    transform: translateY(-6px);
    box-shadow: 0 12px 24px rgb(0 0 0 / 0.15);
  }
}
```

### Círculo (avatar)

```css
.avatar {
  width: 80px;
  aspect-ratio: 1;
  border-radius: 50%;
  object-fit: cover;
}
```

### Cargador giratorio

```css
.cargando {
  width: 40px;
  height: 40px;
  border: 4px solid #ddd;
  border-top-color: crimson;
  border-radius: 50%;
  animation: girar 1s linear infinite;
}

@keyframes girar {
  to { transform: rotate(360deg); }
}
```

### Tema claro y oscuro

```css
:root {
  color-scheme: light dark;
  --fondo: light-dark(#ffffff, #121212);
  --texto: light-dark(#18181b, #fafafa);
}

body {
  background: var(--fondo);
  color: var(--texto);
}
```

### Foco visible y menos movimiento

```css
:focus-visible {
  outline: 3px solid #4a90e2;
  outline-offset: 2px;
}

@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}
```

### Depurar cajas

```css
* {
  outline: 1px solid red;   /* solo para depurar */
}
```

---

## 16. Mini-guía: ¿qué herramienta uso?

| Quiero... | Uso |
|---|---|
| Una fila o columna de elementos | **Flexbox** |
| Filas **y** columnas a la vez | **Grid** |
| Centrar algo | `display: grid; place-items: center;` |
| Separar elementos | `gap` |
| Galería adaptable | `grid` + `repeat(auto-fit, minmax(250px, 1fr))` |
| Un elemento fijo en pantalla | `position: fixed` |
| Cabecera que se pega al hacer scroll | `position: sticky` |
| Una insignia en la esquina de una tarjeta | `position: absolute` con padre `relative` |
| Tamaño de texto que crece con la pantalla | `clamp()` |
| Cambiar el diseño según pantalla | `@media (min-width: ...)` |
| Cambiar un componente según su contenedor | `@container` |
| Reutilizar un valor | Variable `--nombre` |
| Efecto suave al pasar el ratón | `transition` |
| Movimiento con varios pasos | `@keyframes` + `animation` |
| Seleccionar por lo que contiene | `:has()` |
| Evitar guerras de especificidad | `@layer` y clases con nombres BEM |

---

## 17. Resumen de buenas prácticas

- `box-sizing: border-box` en todo.
- Texto y espacios en **`rem`**; `px` solo para detalles finos.
- **Mobile-first** con `min-width`, y primero Flexbox, Grid y `clamp()`.
- **Clases** con nombres por función; especificidad baja; sin `!important`.
- **Variables** para colores, espacios y fuentes.
- Animar **`transform`** y **`opacity`**.
- Accesibilidad: **foco visible**, contraste, zoom permitido y `prefers-reduced-motion`.
- Comprobar la **compatibilidad** de las novedades antes de usarlas.