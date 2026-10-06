# CSS moderno

> [!info] ¿Qué es?
> En los últimos años CSS ha incorporado **muchas funciones nuevas** que antes solo se conseguían con JavaScript o con herramientas como Sass: anidamiento, selector padre (`:has()`), consultas de contenedor, capas de cascada, colores avanzados y más. Esta nota reúne las novedades más útiles y bien soportadas, y explica cómo usarlas con seguridad.

---

## 1. Antes de empezar

Para entender esta nota conviene saber:

- Selectores y especificidad (ver [[02 - Selectores]]).
- Variables y funciones de CSS (ver [[13 - Variables y funciones]]).
- Flexbox, Grid y diseño responsive (ver [[07 - Flexbox]], [[08 - Grid]] y [[10 - Responsive Design]]).

> [!warning] Compatibilidad
> CSS evoluciona rápido. Antes de usar una función reciente, **comprueba su soporte** en [caniuse.com](https://caniuse.com) o en MDN. Una función se considera segura de usar cuando está disponible en los **tres navegadores principales** (Chrome, Firefox y Safari), una situación que se conoce como *Baseline*.

HTML que usaremos en los ejemplos:

```html
<div class="zona">
  <article class="tarjeta">
    <img src="foto.jpg" alt="Foto" class="imagen">
    <h2>Título</h2>
    <p>Texto de la tarjeta.</p>
    <a href="#" class="boton">Leer más</a>
  </article>
</div>
```

---

## 2. Concepto fundamental

Las novedades de CSS se agrupan en cuatro grandes ideas:

| Idea | Qué resuelve | Ejemplos |
|---|---|---|
| **Escribir menos y más claro** | Menos repetición en el código | Anidamiento, `:is()`, `:where()` |
| **Seleccionar mejor** | Condiciones que antes eran imposibles | `:has()`, `:not()`, `:focus-visible` |
| **Adaptarse mejor** | Diseños flexibles por contexto | Container queries, `clamp()`, `subgrid` |
| **Controlar mejor la cascada** | Evitar guerras de especificidad | `@layer`, `@scope`, `@supports` |

> [!tip] Idea clave
> Casi todo lo moderno se puede usar con **mejora progresiva**: escribes primero una versión que funcione en cualquier navegador y añades la novedad encima. Si el navegador no la entiende, la ignora y la página sigue funcionando.

---

## 3. Sintaxis / estructura

Muchas novedades son simplemente **nuevas propiedades** o **nuevas pseudoclases**. Otras añaden **reglas `@`** (at-rules):

| Regla | Para qué sirve |
|---|---|
| `@layer` | Ordenar capas de la cascada |
| `@container` | Consultas de contenedor |
| `@supports` | Comprobar si el navegador soporta algo |
| `@property` | Declarar variables con tipo |
| `@scope` | Limitar los estilos a una zona |
| `@starting-style` | Estilo inicial para transiciones de entrada |

---

## 4. Elementos / propiedades / características

### 4.1 Anidamiento nativo (*nesting*)

Permite escribir reglas **dentro de otras** sin Sass. El símbolo `&` representa "el selector padre".

```css
.tarjeta {
  padding: 1.5rem;
  border: 1px solid #ddd;

  h2 {
    margin-top: 0;
  }

  &:hover {
    border-color: crimson;
  }

  &.destacada {
    border-color: gold;
  }

  .boton {
    margin-top: 1rem;
  }

  @media (min-width: 768px) {
    padding: 2rem;               /* la media query también se puede anidar */
  }
}
```

Equivale a escribir `.tarjeta h2`, `.tarjeta:hover`, `.tarjeta.destacada`, etc.

> [!note]
> Anida **como máximo 2 o 3 niveles**. Más hace el CSS difícil de leer y sube la especificidad sin darte cuenta.

### 4.2 `:has()` (el "selector padre")

Selecciona un elemento **según lo que contiene**.

```css
/* Tarjetas que tienen imagen */
.tarjeta:has(img) {
  padding: 0;
}

/* Etiqueta cuyo campo está marcado */
label:has(input:checked) {
  background: #e6f4ea;
}

/* Formulario con algún campo inválido */
form:has(:invalid) .boton-enviar {
  opacity: 0.5;
  pointer-events: none;
}

/* Cambiar la página si hay un modal abierto */
body:has(dialog[open]) {
  overflow: hidden;
}
```

### 4.3 Consultas de contenedor (*container queries*)

Un componente se adapta al tamaño de **su contenedor**, no al de la pantalla. Así es reutilizable en cualquier sitio.

```css
.zona {
  container-type: inline-size;
  container-name: zona;
}

.tarjeta {
  display: block;
}

@container zona (min-width: 500px) {
  .tarjeta {
    display: grid;
    grid-template-columns: 200px 1fr;
    gap: 1rem;
  }
}
```

También existen **unidades de contenedor**: `cqw` (1% del ancho del contenedor), `cqh`, `cqi`, `cqb`, `cqmin`, `cqmax`.

```css
.tarjeta h2 {
  font-size: clamp(1.1rem, 5cqw, 2rem);   /* el tamaño depende del contenedor */
}
```

### 4.4 Capas de cascada: `@layer`

Permite **ordenar grupos de estilos por prioridad**, sin pelear con la especificidad. Las capas declaradas **más tarde** ganan a las anteriores, sin importar la especificidad de los selectores.

```css
/* 1. Declarar el orden (de menor a mayor prioridad) */
@layer reset, base, componentes, utilidades;

/* 2. Rellenar cada capa */
@layer reset {
  * { margin: 0; box-sizing: border-box; }
}

@layer base {
  body { font-family: system-ui, sans-serif; }
}

@layer componentes {
  .boton { padding: 0.6rem 1.2rem; background: crimson; }
}

@layer utilidades {
  .oculto { display: none; }
}
```

- Los estilos **fuera de cualquier capa** ganan a los que están dentro de capas.
- Es muy útil para integrar librerías de terceros (ponerlas en una capa baja) sin tener que usar `!important`.

### 4.5 `@scope`

Limita unos estilos a **una zona concreta** del documento, con un inicio y, opcionalmente, un límite donde dejan de aplicarse.

```css
@scope (.tarjeta) to (.contenido-anidado) {
  img {
    border-radius: 8px;
  }

  a {
    color: crimson;
  }
}
```

Estos estilos solo afectan a `img` y `a` dentro de `.tarjeta`, sin llegar a `.contenido-anidado`. (Es relativamente reciente: revisa la compatibilidad.)

### 4.6 `@supports` (comprobar compatibilidad)

Aplica estilos **solo si el navegador soporta** una función.

```css
.galeria {
  display: flex;                        /* respaldo para navegadores antiguos */
  flex-wrap: wrap;
}

@supports (display: grid) {
  .galeria {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  }
}

@supports not (backdrop-filter: blur(5px)) {
  .cristal {
    background: rgb(255 255 255 / 0.95);   /* alternativa sin desenfoque */
  }
}
```

### 4.7 Propiedades lógicas

Se adaptan a la **dirección de escritura** del idioma (útil en webs multilingües) en lugar de usar `left`, `right`, `top`, `bottom`.

| Física | Lógica |
|---|---|
| `width` / `height` | `inline-size` / `block-size` |
| `margin-left` / `margin-right` | `margin-inline-start` / `margin-inline-end` |
| `margin-top` / `margin-bottom` | `margin-block-start` / `margin-block-end` |
| `padding-left` + `padding-right` | `padding-inline` |
| `padding-top` + `padding-bottom` | `padding-block` |
| `top`, `right`, `bottom`, `left` | `inset-block-*`, `inset-inline-*` y `inset` |
| `border-left` | `border-inline-start` |
| `text-align: left` | `text-align: start` |

```css
.caja {
  padding-inline: 1rem;
  margin-block: 2rem;
  border-inline-start: 4px solid crimson;
}
```

### 4.8 `aspect-ratio`

Define la **proporción** de una caja sin trucos con padding.

```css
.video {
  width: 100%;
  aspect-ratio: 16 / 9;
}

.avatar {
  width: 80px;
  aspect-ratio: 1;               /* cuadrado */
  object-fit: cover;
  border-radius: 50%;
}
```

### 4.9 `gap`, `place-items` y otras abreviaturas modernas

```css
.centrado {
  display: grid;
  place-items: center;            /* align-items + justify-items */
}

.fila {
  display: flex;
  gap: 1rem;                      /* espacio entre elementos */
}

.capa {
  position: absolute;
  inset: 0;                       /* top, right, bottom y left a 0 */
}
```

### 4.10 `subgrid`

Un ítem grid hereda las filas o columnas del Grid padre para **alinear su contenido interno** con el resto (ver [[08 - Grid]]).

```css
.tarjetas {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
}

.tarjeta {
  display: grid;
  grid-row: span 3;
  grid-template-rows: subgrid;
}
```

### 4.11 Colores modernos

```css
.caja {
  background: oklch(65% 0.2 25);                              /* color perceptualmente uniforme */
  border-color: color-mix(in oklch, crimson 60%, white);      /* mezcla */
  color: light-dark(#222, #eee);                              /* claro y oscuro en una línea */
  accent-color: crimson;                                      /* color de casillas y radios */
}

:root {
  color-scheme: light dark;
}
```

Además, los **colores relativos** permiten modificar un color existente:

```css
.boton {
  background: oklch(from var(--principal) calc(l - 0.1) c h);   /* un poco más oscuro */
}
```

### 4.12 Tipografía moderna

```css
h1, h2 {
  text-wrap: balance;               /* líneas equilibradas en títulos */
}

p {
  text-wrap: pretty;                /* evita palabras sueltas al final */
  max-width: 65ch;
}

.letra-capital::first-letter {
  initial-letter: 3;                /* letra capital sencilla (soporte parcial) */
}
```

Las **fuentes variables** permiten valores intermedios: `font-weight: 650;` (ver [[06 - Texto y fuentes]]).

### 4.13 Scroll moderno

```css
html {
  scroll-behavior: smooth;                  /* desplazamiento suave al usar anclas */
  scroll-padding-top: 5rem;                 /* deja sitio para una cabecera fija */
}

/* Carrusel con "imanes" */
.carrusel {
  display: flex;
  overflow-x: auto;
  scroll-snap-type: x mandatory;
}

.carrusel > * {
  flex: 0 0 80%;
  scroll-snap-align: center;
}

/* Evita que el scroll interior arrastre la página */
.panel {
  overscroll-behavior: contain;
}

/* Evita que el contenido salte al aparecer la barra de scroll */
html {
  scrollbar-gutter: stable;
}
```

### 4.14 Efectos visuales modernos

```css
.cristal {
  backdrop-filter: blur(10px);               /* desenfoca lo que hay detrás */
  background: rgb(255 255 255 / 0.5);
}

.imagen {
  filter: grayscale(100%) contrast(1.1);     /* filtros */
  mix-blend-mode: multiply;                  /* mezcla con lo que hay debajo */
}

.recorte {
  clip-path: circle(50%);                    /* recorta con una forma */
}

.mascara {
  mask-image: linear-gradient(black, transparent);   /* desvanece */
}
```

### 4.15 Transiciones y animaciones nuevas

**`@starting-style`** define el estilo desde el que **entra** un elemento recién creado o mostrado:

```css
.aviso {
  opacity: 1;
  transition: opacity 0.4s, display 0.4s allow-discrete;

  @starting-style {
    opacity: 0;                         /* empieza invisible y aparece suave */
  }
}
```

**Transiciones entre páginas o vistas** (*View Transitions*):

```css
@view-transition {
  navigation: auto;                     /* transición suave al cambiar de página (misma web) */
}
```

**Animaciones ligadas al scroll** (soporte creciente):

```css
.barra-progreso {
  animation: crecer linear;
  animation-timeline: scroll();         /* avanza según el scroll de la página */
}

@keyframes crecer {
  from { transform: scaleX(0); }
  to   { transform: scaleX(1); }
}
```

### 4.16 Elementos HTML con ayuda de CSS

Algunas funciones modernas se apoyan en HTML nativo, con CSS para el estilo:

| Elemento | Qué hace | Se estila con |
|---|---|---|
| `<dialog>` | Ventana modal accesible | `dialog::backdrop`, `:modal` |
| `popover` (atributo) | Menús y avisos emergentes | `:popover-open` |
| `<details>` / `<summary>` | Acordeones | `details[open]` |

```css
dialog::backdrop {
  background: rgb(0 0 0 / 0.6);
}

details[open] summary {
  font-weight: bold;
}
```

### 4.17 Posicionamiento por anclaje (*anchor positioning*)

Permite colocar un elemento (por ejemplo, un menú o *tooltip*) **junto a otro**, sin JavaScript.

```css
.boton-menu {
  anchor-name: --menu;
}

.menu-flotante {
  position: absolute;
  position-anchor: --menu;
  top: anchor(bottom);
  left: anchor(left);
}
```

(Es una función muy reciente: comprueba la compatibilidad.)

---

## 5. Ejemplos prácticos

### Ejemplo básico

Anidamiento y `:has()`:

```css
.tarjeta {
  padding: 1rem;

  h2 {
    margin: 0;
  }

  &:hover {
    box-shadow: 0 4px 12px rgb(0 0 0 / 0.15);
  }

  &:has(img) {
    padding: 0;
  }
}
```

### Ejemplo habitual

Un componente tarjeta que se adapta a su contenedor, con proporción de imagen y tipografía equilibrada:

```css
.zona {
  container-type: inline-size;
}

.tarjeta {
  display: grid;
  gap: 1rem;

  .imagen {
    width: 100%;
    aspect-ratio: 16 / 9;
    object-fit: cover;
    border-radius: 8px;
  }

  h2 {
    text-wrap: balance;
  }
}

@container (min-width: 520px) {
  .tarjeta {
    grid-template-columns: 220px 1fr;
    align-items: start;
  }
}
```

### Ejemplo completo

Capas, tema claro/oscuro, mejora progresiva y componentes modernos juntos:

```css
@layer reset, base, componentes;

@layer reset {
  *,
  *::before,
  *::after {
    box-sizing: border-box;
    margin: 0;
  }
}

@layer base {
  :root {
    color-scheme: light dark;
    --principal: oklch(60% 0.22 20);
  }

  body {
    background: light-dark(#ffffff, #111111);
    color: light-dark(#18181b, #fafafa);
    font-family: system-ui, sans-serif;
    line-height: 1.6;
  }

  h1, h2 {
    text-wrap: balance;
  }

  html {
    scroll-behavior: smooth;
    scrollbar-gutter: stable;
  }
}

@layer componentes {
  .zona {
    container-type: inline-size;
    padding-inline: 1rem;
  }

  .tarjeta {
    display: grid;
    gap: 1rem;
    padding: 1rem;
    border: 1px solid color-mix(in oklch, currentColor 15%, transparent);
    border-radius: 12px;

    .imagen {
      aspect-ratio: 16 / 9;
      width: 100%;
      object-fit: cover;
      border-radius: 8px;
    }

    .boton {
      justify-self: start;
      padding-block: 0.6rem;
      padding-inline: 1.2rem;
      background: var(--principal);
      color: white;
      border-radius: 8px;
      text-decoration: none;

      &:hover {
        background: color-mix(in oklch, var(--principal) 85%, black);
      }
    }

    &:has(img) {
      padding: 0;

      > :not(.imagen) {
        padding-inline: 1rem;
      }

      > :last-child {
        margin-bottom: 1rem;
      }
    }
  }

  @container (min-width: 560px) {
    .tarjeta:has(img) {
      grid-template-columns: 240px 1fr;
      align-items: start;
    }
  }

  /* Mejora progresiva: solo si hay desenfoque disponible */
  @supports (backdrop-filter: blur(4px)) {
    .cabecera {
      backdrop-filter: blur(10px);
      background: color-mix(in oklch, canvas 70%, transparent);
    }
  }
}

@media (prefers-reduced-motion: reduce) {
  html {
    scroll-behavior: auto;
  }
}
```

---

## 6. Buenas prácticas

- **Comprueba la compatibilidad** antes de usar una función nueva (caniuse, MDN y *Baseline*).
- **Aplica mejora progresiva**: una versión base que funcione en todo, y la novedad encima con `@supports` cuando haga falta.
- **Usa `@layer`** para ordenar reset, base, componentes y utilidades, y evita `!important`.
- **Anida con moderación** (2 o 3 niveles) para no subir la especificidad.
- **Prefiere container queries** para componentes reutilizables, y media queries para el diseño general de la página.
- **Usa propiedades lógicas** (`margin-inline`, `padding-block`) en proyectos que puedan ser multilingües.
- **Usa `aspect-ratio`** en lugar del antiguo truco del `padding-bottom` en porcentaje.
- **Respeta las preferencias del usuario**: `prefers-color-scheme`, `prefers-reduced-motion`.
- **Usa HTML nativo** (`<dialog>`, `<details>`, `popover`) antes de construir componentes con JavaScript.
- **No uses una novedad solo por moda**: úsala cuando simplifique de verdad tu código.
- **Mantente al día** con MDN y con las notas de versión de los navegadores.

---

## 7. Diferencias importantes

| Comparación | Diferencia |
|---|---|
| Media query vs container query | La primera mira la **pantalla**; la segunda, el **contenedor**. |
| `@layer` vs especificidad | Las capas deciden primero; la especificidad solo cuenta **dentro** de una misma capa. |
| `@layer` vs `@scope` | `@layer` ordena la **prioridad**; `@scope` limita **dónde** se aplican los estilos. |
| `:has()` vs `:is()` | `:has()` mira **lo que contiene** el elemento; `:is()` agrupa varios selectores. |
| Anidamiento CSS vs Sass | El anidamiento CSS funciona en el navegador, sin compilar; Sass se compila y ofrece más funciones (mixins, bucles). |
| `margin-left` vs `margin-inline-start` | La física siempre es la izquierda; la lógica sigue la dirección del idioma. |
| `aspect-ratio` vs `height` fijo | `aspect-ratio` mantiene la proporción al cambiar el ancho; un `height` fijo no. |
| `oklch()` vs `hsl()` | `oklch` es más uniforme a la vista (misma luminosidad = parece igual de claro); `hsl` es más sencillo pero desigual. |
| `@supports` vs detectar con JavaScript | `@supports` se resuelve en CSS, sin código extra. |

---

## 8. Casos especiales

- **Reglas desconocidas**: si un navegador no entiende una propiedad o un valor, **lo ignora** y sigue con lo demás. Eso hace posible la mejora progresiva: pon primero el valor seguro y después el moderno.

```css
  .titulo {
    font-size: 2rem;                      /* respaldo */
    font-size: clamp(2rem, 5vw, 3rem);    /* moderno */
  }
```
- **Selectores inválidos en una lista**: si uno no se entiende, el navegador descarta **todo el bloque**. Por eso `:is()` y `:where()` (que ignoran los inválidos) son más seguros.
- **Container queries**: el elemento con `container-type` **no puede estilarse a sí mismo** dentro de su propia `@container`; solo a sus descendientes.
- **`container-type: size`** exige que el contenedor tenga tamaño fijo en ambos ejes; `inline-size` es lo normal.
- **`@layer` y `!important`**: con `!important`, el orden de las capas se **invierte** (la capa más baja gana).
- **El anidamiento y `&`**: sin `&` delante, un selector anidado como `h2` es válido; pero para pseudoclases y modificadores (`:hover`, `.activa`) sí necesitas `&`.
- **`:has()` es potente pero costoso**: evita selectores muy generales como `:has(*)` en páginas grandes.
- **`backdrop-filter` y `filter`** crean contextos de apilamiento (ver [[09 - Position]]) y pueden afectar al rendimiento.
- **`scroll-behavior: smooth`** puede marear a algunos usuarios: desactívalo con `prefers-reduced-motion`.
- **`text-wrap: balance`** solo funciona con pocas líneas (títulos); no se aplica a párrafos largos.
- **Funciones experimentales** (`anchor()`, `animation-timeline`, `@scope`, `attr()` avanzado) pueden variar entre navegadores. Úsalas solo como mejora, no como base.

> [!warning] Obsoleto / legado
> Técnicas antiguas que las novedades reemplazan: el **truco del `padding-bottom`** para proporciones (ahora `aspect-ratio`), los **márgenes entre ítems** en Flexbox (ahora `gap`), **`float`** para maquetar (ahora Flexbox y Grid), los **prefijos de navegador** en casi todo (usa Autoprefixer solo si lo necesitas) y las **librerías de JavaScript** para selector padre o para centrado vertical.

---

## 9. Resumen

- El CSS moderno permite **escribir menos**, **seleccionar mejor**, **adaptarse mejor** y **controlar mejor la cascada**.
- **Anidamiento nativo** con `&`: reglas dentro de reglas, sin Sass (máximo 2 o 3 niveles).
- **`:has()`** es el "selector padre": elige un elemento por lo que contiene.
- **Container queries** (`@container`) adaptan un componente a su contenedor, no a la pantalla.
- **`@layer`** ordena la cascada por capas; **`@scope`** limita estilos a una zona; **`@supports`** permite mejora progresiva.
- **Propiedades lógicas** (`margin-inline`, `padding-block`, `inset`) se adaptan a la dirección del idioma.
- **`aspect-ratio`**, **`gap`**, **`place-items`**, **`subgrid`** y **`text-wrap`** simplifican diseños y tipografía.
- Colores modernos: **`oklch()`**, **`color-mix()`**, **`light-dark()`**, colores relativos y `accent-color`.
- Novedades en movimiento: **`@starting-style`**, **View Transitions**, **animaciones ligadas al scroll** y **anchor positioning** (comprueba soporte).
- Regla de oro: **mejora progresiva**, comprobar compatibilidad y usar HTML nativo cuando exista.