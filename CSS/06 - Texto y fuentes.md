# Texto y fuentes

> [!info] ¿Qué es?
> CSS controla **cómo se ve el texto**: la fuente, el tamaño, el grosor, el espacio entre líneas y letras, la alineación y mucho más. Una buena tipografía hace que una web sea cómoda de leer y que tenga personalidad.

---

## 1. Antes de empezar

Para entender esta nota conviene saber:

- Las unidades `rem`, `em` y `ch` (ver [[04 - Unidades y valores]]).
- Cómo dar color al texto con `color` (ver [[05 - Colores y fondos]]).
- Que muchas propiedades de texto **se heredan**: si las pones en `body`, afectan a casi todo.

HTML que usaremos en los ejemplos:

```html
<article class="articulo">
  <h1>Título del artículo</h1>
  <p class="intro">Un párrafo de introducción con un <a href="#">enlace</a>.</p>
  <p>Otro párrafo con <strong>texto importante</strong> y <em>énfasis</em>.</p>
</article>
```

---

## 2. Concepto fundamental

Las propiedades de texto se agrupan en dos familias:

- **Propiedades de fuente (`font-*`)**: definen **la letra en sí**: familia, tamaño, grosor, estilo.
- **Propiedades de texto (`text-*` y otras)**: definen **cómo se organiza el texto**: alineación, decoración, espaciado, saltos de línea.

> [!tip] Idea clave
> Casi todas las propiedades de texto **se heredan**. Lo normal es definir la tipografía base en `body` y cambiar solo lo necesario en cada elemento.

---

## 3. Sintaxis / estructura

```css
body {
  font-family: "Inter", Arial, sans-serif;
  font-size: 1rem;
  line-height: 1.6;
  color: #222;
}
```

---

## 4. Elementos / propiedades / características

### 4.1 `font-family` (la familia de fuente)

Es una **lista de fuentes por orden de preferencia**. El navegador usa la primera que tenga disponible.

```css
body {
  font-family: "Inter", "Helvetica Neue", Arial, sans-serif;
}
```

- Si el nombre tiene **espacios**, va entre comillas: `"Helvetica Neue"`.
- La **última** debe ser siempre una **familia genérica** como respaldo.

| Familia genérica | Aspecto | Ejemplo típico |
|---|---|---|
| `serif` | Con remates (serifas) | Times, Georgia |
| `sans-serif` | Sin remates | Arial, Helvetica |
| `monospace` | Todas las letras con el mismo ancho | Courier, Consolas |
| `cursive` | Tipo manuscrito | Comic Sans |
| `fantasy` | Decorativa | Impact |
| `system-ui` | La fuente del sistema operativo | San Francisco, Segoe UI |

Una pila de fuentes del sistema (rápida, sin descargar nada):

```css
body {
  font-family: system-ui, -apple-system, "Segoe UI", Roboto, sans-serif;
}
```

### 4.2 Cargar fuentes propias

Hay dos formas habituales.

#### Con `@font-face` (archivos propios)

```css
@font-face {
  font-family: "MiFuente";
  src: url("fuentes/mifuente.woff2") format("woff2");
  font-weight: 400;
  font-style: normal;
  font-display: swap;
}

body {
  font-family: "MiFuente", sans-serif;
}
```

- Usa el formato **`woff2`**: es el más ligero y está soportado en todos los navegadores actuales.
- **`font-display: swap`** muestra primero una fuente de respaldo y cambia a la tuya cuando carga. Así el texto no queda invisible mientras descarga.

#### Con Google Fonts (u otro servicio)

Se añade un `<link>` en el `<head>` del HTML y luego se usa el nombre en CSS:

```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;700&display=swap" rel="stylesheet">
```

```css
body {
  font-family: "Inter", sans-serif;
}
```

> [!note]
> Carga solo los **pesos y estilos que realmente uses**: cada uno añade peso a la página.

### 4.3 `font-size` (tamaño)

```css
body {
  font-size: 1rem;      /* normalmente 16px */
}

h1 {
  font-size: 2.5rem;
}

small {
  font-size: 0.875rem;
}
```

También existen palabras clave (`small`, `medium`, `large`, `x-large`…), pero se usan poco.

Para un tamaño que crece con la pantalla:

```css
h1 {
  font-size: clamp(2rem, 5vw, 3.5rem);
}
```

### 4.4 `font-weight` (grosor)

| Valor | Significado |
|---|---|
| `normal` o `400` | Normal |
| `bold` o `700` | Negrita |
| `100` a `900` | Escala completa (de muy fina a muy gruesa) |
| `lighter` / `bolder` | Más fino / más grueso que el padre |

```css
.destacado {
  font-weight: 600;
}
```

> [!note]
> Solo se verán los grosores que la fuente **realmente tenga**. Si pides `600` y la fuente solo trae `400` y `700`, se usará el más cercano.

### 4.5 `font-style` y `font-variant`

```css
em {
  font-style: italic;       /* cursiva (de la fuente) */
}

.cita {
  font-style: oblique;      /* cursiva "inclinada" artificialmente */
}

.titulo-versalitas {
  font-variant: small-caps; /* versalitas: minúsculas con forma de mayúscula pequeña */
}
```

### 4.6 La abreviatura `font`

Junta varias propiedades en una:

```css
p {
  font: italic 700 1rem/1.5 "Inter", sans-serif;
}
```

Orden: **style, weight, size/line-height, family**. Los dos últimos (tamaño y familia) son **obligatorios**. Como reinicia lo que no escribes, muchos programadores prefieren escribir las propiedades por separado.

### 4.7 `line-height` (altura de línea)

Es el **espacio vertical de cada línea** de texto.

```css
body {
  line-height: 1.6;       /* sin unidad: 1.6 veces el tamaño de la letra */
}

h1 {
  line-height: 1.2;       /* los títulos suelen ir más apretados */
}
```

- **Sin unidad** es lo recomendado: se recalcula para cada elemento.
- Para el texto normal, un valor de **1.5 a 1.7** es cómodo de leer.

### 4.8 `text-align` (alineación)

| Valor | Efecto |
|---|---|
| `left` | A la izquierda |
| `right` | A la derecha |
| `center` | Centrado |
| `justify` | Justificado (las líneas ocupan todo el ancho) |
| `start` / `end` | Según la dirección del idioma (útil en webs multilingües) |

```css
.titulo {
  text-align: center;
}
```

> [!warning]
> `text-align` alinea el **contenido en línea** dentro de una caja. **No centra la caja** en sí. Para eso: `margin-inline: auto` (ver [[03 - Box Model]]).

### 4.9 `text-decoration` (subrayado y similares)

```css
a {
  text-decoration: underline;
}

a:hover {
  text-decoration: underline wavy crimson;   /* línea ondulada roja */
}

.sin-linea {
  text-decoration: none;
}
```

Se puede dividir en partes:

| Propiedad | Valores habituales |
|---|---|
| `text-decoration-line` | `underline`, `overline`, `line-through`, `none` |
| `text-decoration-style` | `solid`, `dotted`, `dashed`, `wavy`, `double` |
| `text-decoration-color` | Cualquier color |
| `text-decoration-thickness` | `2px`, `0.1em`… |
| `text-underline-offset` | Distancia del subrayado al texto: `4px` |

### 4.10 `text-transform` (mayúsculas y minúsculas)

| Valor | Efecto |
|---|---|
| `uppercase` | TODO EN MAYÚSCULAS |
| `lowercase` | todo en minúsculas |
| `capitalize` | Primera Letra De Cada Palabra En Mayúscula |
| `none` | Sin cambios |

```css
.etiqueta {
  text-transform: uppercase;
  letter-spacing: 0.05em;
}
```

### 4.11 Espaciado: `letter-spacing`, `word-spacing` y `text-indent`

| Propiedad | Qué controla | Ejemplo |
|---|---|---|
| `letter-spacing` | Espacio entre **letras** | `0.05em` |
| `word-spacing` | Espacio entre **palabras** | `0.2em` |
| `text-indent` | **Sangría** de la primera línea | `2rem` |

```css
.titulo {
  letter-spacing: -0.02em;     /* un poco más juntas */
}

.texto-libro p {
  text-indent: 2rem;
}
```

Usa `em` para que el espaciado se adapte al tamaño de letra.

### 4.12 Saltos de línea y desbordamiento del texto

#### `white-space`

| Valor | Efecto |
|---|---|
| `normal` | (por defecto) Junta los espacios y salta de línea al llegar al borde |
| `nowrap` | **No** salta de línea |
| `pre` | Respeta espacios y saltos del HTML (como `<pre>`) |
| `pre-wrap` | Respeta espacios y saltos, pero también salta al llegar al borde |

#### Palabras muy largas

```css
.comentario {
  overflow-wrap: break-word;    /* rompe palabras largas si no caben */
  word-break: normal;
}
```

| Propiedad | Para qué sirve |
|---|---|
| `overflow-wrap: break-word` | Rompe una palabra solo si **no cabe** en la línea |
| `word-break: break-all` | Rompe las palabras en **cualquier** punto (más agresivo) |
| `hyphens: auto` | Pone guiones al cortar palabras (necesita `lang` en el HTML) |

#### Puntos suspensivos (`…`)

Para cortar un texto de **una sola línea** con "…":

```css
.una-linea {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}
```

Para limitar a **varias líneas**:

```css
.resumen {
  display: -webkit-box;
  -webkit-line-clamp: 3;          /* máximo 3 líneas */
  -webkit-box-orient: vertical;
  overflow: hidden;
}
```

### 4.13 `text-shadow` (sombra del texto)

```css
.titulo {
  text-shadow: 2px 2px 4px rgb(0 0 0 / 0.4);   /* x | y | desenfoque | color */
}
```

Funciona como `box-shadow`, pero sin `inset` ni extensión (ver [[05 - Colores y fondos]]).

### 4.14 `text-wrap` (mejoras modernas)

```css
h1,
h2 {
  text-wrap: balance;     /* reparte las palabras para que las líneas queden equilibradas */
}

p {
  text-wrap: pretty;      /* evita palabras sueltas al final del párrafo */
}
```

### 4.15 Fuentes variables

Una **fuente variable** es un solo archivo que contiene muchos grosores, anchos y estilos. Permite usar cualquier valor intermedio:

```css
.titulo {
  font-weight: 650;    /* un valor entre 600 y 700 */
}
```

Ahorra peso de descarga cuando necesitas varios pesos.

### 4.16 Pautas de legibilidad

- **Tamaño base**: al menos **16px** (`1rem`) para el texto del cuerpo.
- **Altura de línea**: entre **1.5 y 1.7** en párrafos.
- **Longitud de línea**: entre **45 y 75 caracteres** (`max-width: 65ch`).
- **Contraste**: suficiente entre texto y fondo (ver [[05 - Colores y fondos]]).
- **Máximo 2 fuentes** distintas por proyecto: una para títulos y otra para texto, o una sola.

---

## 5. Ejemplos prácticos

### Ejemplo básico

```css
body {
  font-family: Arial, sans-serif;
  font-size: 1rem;
  line-height: 1.6;
  color: #222;
}

h1 {
  font-size: 2rem;
  text-align: center;
}
```

### Ejemplo habitual

Tipografía base de una web con fuente del sistema, enlaces y limitación del ancho del texto:

```css
body {
  font-family: system-ui, "Segoe UI", Roboto, sans-serif;
  font-size: 1rem;
  line-height: 1.6;
  color: #222;
}

h1,
h2,
h3 {
  line-height: 1.2;
  text-wrap: balance;
}

.articulo p {
  max-width: 65ch;
}

a {
  color: #1e3a8a;
  text-decoration: underline;
  text-underline-offset: 3px;
}

a:hover {
  text-decoration-thickness: 2px;
}
```

### Ejemplo completo

Fuente propia, jerarquía de títulos, etiqueta en mayúsculas y resumen limitado a 3 líneas:

```css
@font-face {
  font-family: "Inter";
  src: url("fuentes/inter-variable.woff2") format("woff2");
  font-weight: 100 900;           /* rango de pesos de una fuente variable */
  font-display: swap;
}

:root {
  font-size: 100%;
}

body {
  font-family: "Inter", system-ui, sans-serif;
  font-size: 1rem;
  line-height: 1.6;
  color: #222;
}

h1 {
  font-size: clamp(2rem, 5vw, 3.5rem);
  font-weight: 800;
  line-height: 1.1;
  letter-spacing: -0.02em;
}

h2 {
  font-size: 1.75rem;
  font-weight: 700;
  line-height: 1.25;
}

.etiqueta {
  font-size: 0.75rem;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.08em;
  color: #666;
}

.resumen {
  display: -webkit-box;
  -webkit-line-clamp: 3;
  -webkit-box-orient: vertical;
  overflow: hidden;
  text-wrap: pretty;
}

code {
  font-family: ui-monospace, "Cascadia Code", Consolas, monospace;
  font-size: 0.9em;
  padding: 0.1em 0.3em;
  background: #f1f1f1;
  border-radius: 4px;
}
```

---

## 6. Buenas prácticas

- **Define la tipografía base en `body`** y deja que se herede.
- **Usa `rem` para `font-size`** para respetar la configuración del usuario (ver [[04 - Unidades y valores]]).
- **Pon siempre una familia genérica al final** de `font-family`.
- **Usa `line-height` sin unidad** (por ejemplo `1.6`).
- **Limita el ancho de los párrafos con `max-width: 65ch`.**
- **Carga solo los pesos que uses** y usa el formato `woff2`.
- **Usa `font-display: swap`** para que el texto se vea mientras se descarga la fuente.
- **Usa una pila de fuentes del sistema** si buscas máxima velocidad.
- **No dependas solo de `text-transform` para el significado**: el texto en HTML sigue siendo el original.
- **No uses `text-align: justify`** en columnas estrechas: crea huecos feos entre palabras.
- **No quites el subrayado de los enlaces** sin dar otra señal visual clara.
- **No uses imágenes para mostrar texto**: usa texto real.

---

## 7. Diferencias importantes

| Comparación | Diferencia |
|---|---|
| `font-family` vs familia genérica | La primera es una lista de preferencias; la genérica es el respaldo final. |
| `font-style: italic` vs `oblique` | `italic` usa la cursiva diseñada de la fuente; `oblique` inclina la letra normal. |
| `line-height: 1.5` vs `line-height: 150%` | Sin unidad se recalcula en cada hijo; con `%` se calcula una vez y se hereda el valor final. |
| `text-align` vs `margin: auto` | `text-align` alinea el texto dentro de la caja; `margin: auto` centra la caja. |
| `overflow-wrap` vs `word-break` | `overflow-wrap` rompe solo si no cabe; `word-break: break-all` rompe en cualquier sitio. |
| `text-overflow` vs `overflow` | `text-overflow` decide cómo se ve el recorte (los puntos suspensivos); necesita `overflow: hidden` y `white-space: nowrap`. |
| `text-shadow` vs `box-shadow` | `text-shadow` sigue la forma de las letras; `box-shadow` sigue la forma de la caja. |
| `@font-face` vs Google Fonts | `@font-face` carga archivos propios; Google Fonts los sirve desde sus servidores (con su propia carga externa). |

---

## 8. Casos especiales

- **`text-overflow: ellipsis` necesita tres propiedades a la vez**: `white-space: nowrap`, `overflow: hidden` y `text-overflow: ellipsis`. Si falta una, no funciona.
- **`-webkit-line-clamp`** funciona en todos los navegadores modernos, aunque lleve el prefijo `-webkit-`. Existe también la propiedad estándar `line-clamp`, pero su soporte todavía es limitado.
- **`hyphens: auto`** solo funciona si el HTML tiene el atributo `lang` bien puesto (`<html lang="es">`).
- **Un peso que no existe** en la fuente se aproxima al más cercano; si no está cargado, el navegador puede "falsear" la negrita (queda peor).
- **`font-size` en un elemento afecta a los `em`** de sus propiedades de espacio (padding, margin) y a las de sus hijos.
- **Mayúsculas con `text-transform`** no cambian cómo lo lee un lector de pantalla, pero algunos leen cada letra por separado si el texto está escrito directamente en mayúsculas en el HTML.
- **Texto sobre imágenes**: asegura el contraste con una capa oscura o clara por debajo.
- **Fuente de respaldo con distinto tamaño**: al cambiar de la fuente de respaldo a la tuya, el texto puede "saltar". Se puede reducir con `size-adjust` en `@font-face`.
- **Los elementos `<input>` y `<button>` no heredan la fuente** por defecto. Para que usen la del resto de la página: `input, button { font: inherit; }`.

> [!warning] Obsoleto / legado
> La etiqueta HTML `<font>` y los atributos `align` o `color` en HTML están **obsoletos**: usa CSS. `text-decoration: blink` también está obsoleto, y `word-wrap` es el nombre antiguo de `overflow-wrap` (sigue funcionando, pero conviene usar el nuevo).

---

## 9. Resumen

- Dos familias: **`font-*`** (la letra) y **`text-*`** (cómo se organiza el texto). Casi todo **se hereda**.
- **`font-family`** es una lista de preferencias que termina con una familia **genérica**.
- Fuentes propias con **`@font-face`** (formato `woff2` y `font-display: swap`) o con servicios como Google Fonts.
- **`font-size`** en `rem`; **`font-weight`** de 100 a 900; **`line-height`** sin unidad (1.5 a 1.7).
- **`text-align`** alinea el texto, no la caja; **`text-decoration`**, **`text-transform`**, **`letter-spacing`** y **`text-indent`** ajustan el aspecto.
- **`white-space`**, **`overflow-wrap`** y **`text-overflow`** controlan saltos de línea y recortes; los puntos suspensivos necesitan tres propiedades juntas.
- **`text-wrap: balance`** y **`pretty`** mejoran el reparto de líneas.
- Para leer bien: unos **16px** de base, **45-75 caracteres** por línea y buen contraste.
- Los formularios no heredan la fuente: usa `font: inherit`.