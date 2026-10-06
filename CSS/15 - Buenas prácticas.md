# Buenas prácticas

> [!info] ¿Qué es?
> Escribir CSS que "funciona" es fácil; escribir CSS que sea **fácil de leer, cambiar y mantener durante años** es lo difícil. Esta nota reúne las **buenas prácticas** más importantes: cómo organizar, nombrar, escribir y optimizar tus estilos, y cómo cuidar la accesibilidad.

---

## 1. Antes de empezar

Para entender esta nota conviene haber visto el resto de la carpeta de CSS, sobre todo:

- Selectores y especificidad (ver [[02 - Selectores]]).
- Box Model (ver [[03 - Box Model]]).
- Variables y funciones (ver [[13 - Variables y funciones]]).
- Diseño responsive (ver [[10 - Responsive Design]]).
- CSS moderno (ver [[14 - CSS moderno]]).

HTML que usaremos en los ejemplos:

```html
<article class="tarjeta tarjeta--destacada">
  <h2 class="tarjeta__titulo">Título</h2>
  <p class="tarjeta__texto">Descripción de la tarjeta.</p>
  <a href="#" class="boton boton--principal">Leer más</a>
</article>
```

---

## 2. Concepto fundamental

Un buen CSS busca cinco objetivos:

| Objetivo | Significa |
|---|---|
| **Legible** | Se entiende de un vistazo, incluso meses después |
| **Predecible** | Cambiar algo aquí no rompe algo allí |
| **Reutilizable** | No se repite el mismo código |
| **Escalable** | Funciona igual de bien con 10 que con 1000 componentes |
| **Accesible y rápido** | Lo puede usar cualquiera y carga rápido |

> [!tip] Idea clave
> El CSS se lee muchas más veces de las que se escribe. Escribe pensando en **la persona que lo leerá después** (que puedes ser tú dentro de seis meses).

---

## 3. Sintaxis / estructura

### Formato del código

```css
/* ✅ Bien formateado */
.boton {
  display: inline-block;
  padding: 0.6rem 1.2rem;
  border-radius: 8px;
  background: var(--color-principal);
  color: white;
}

.boton:hover {
  background: var(--color-principal-oscuro);
}
```

Reglas de formato habituales:

- **Una declaración por línea.**
- **Sangría** de 2 espacios (o 4, pero siempre igual).
- **Espacio** después de los dos puntos: `color: red;`.
- **Punto y coma** al final de cada declaración, incluida la última.
- **Una línea en blanco** entre reglas.
- **Un selector por línea** cuando hay una lista con comas.

```css
h1,
h2,
h3 {
  line-height: 1.2;
}
```

---

## 4. Elementos / propiedades / características

### 4.1 Organización de los archivos

Para proyectos pequeños, un solo archivo basta. Para proyectos grandes, divide por responsabilidad:

```text
css/
├── 00-reset.css          # Reinicio de estilos del navegador
├── 01-variables.css      # Variables (colores, espacios, fuentes)
├── 02-base.css           # Estilos de etiquetas (body, h1, a, p...)
├── 03-layout.css         # Estructura general (cabecera, rejilla, pie)
├── 04-componentes/       # Un archivo por componente
│   ├── boton.css
│   ├── tarjeta.css
│   └── formulario.css
├── 05-utilidades.css     # Clases auxiliares (.oculto, .centrado)
└── main.css              # Importa a todos los demás
```

#### Orden dentro de un archivo

1. Reset y estilos base.
2. Variables.
3. Layout (estructura de la página).
4. Componentes.
5. Utilidades.
6. Media queries y preferencias (junto a lo que modifican).

Con `@layer` (ver [[14 - CSS moderno]]) este orden queda **garantizado** sin pelear con la especificidad.

### 4.2 Orden de las propiedades

Elige un orden y mantenlo. Un orden lógico muy usado:

1. **Posicionamiento**: `position`, `inset`, `z-index`
2. **Modelo de caja y layout**: `display`, `flex`, `grid`, `gap`, `width`, `height`, `margin`, `padding`
3. **Tipografía**: `font`, `line-height`, `text-align`, `color`
4. **Visual**: `background`, `border`, `border-radius`, `box-shadow`, `opacity`
5. **Otros**: `transition`, `animation`, `cursor`

```css
.tarjeta {
  /* Posición */
  position: relative;

  /* Layout y caja */
  display: grid;
  gap: 1rem;
  padding: 1.5rem;

  /* Tipografía */
  font-size: 1rem;
  color: var(--color-texto);

  /* Visual */
  background: var(--color-fondo);
  border: 1px solid var(--color-borde);
  border-radius: var(--radio);

  /* Otros */
  transition: transform 0.2s;
}
```

### 4.3 Nombrar clases

Un buen nombre dice **qué es** o **qué hace**, no **cómo se ve**.

| ❌ Mal | ✅ Bien | Por qué |
|---|---|---|
| `.rojo` | `.alerta` | Si cambia el color, el nombre miente |
| `.margen-izquierda-20` | `.tarjeta__contenido` | Se describe la función, no el valor |
| `.caja1`, `.caja2` | `.tarjeta`, `.resumen` | Nombres con significado |
| `.Cabecera`, `.mi_clase` | `.cabecera`, `.mi-clase` | Estilo uniforme |

**Normas básicas:**

- Todo en **minúsculas**.
- Palabras separadas por **guiones** (`kebab-case`): `.menu-principal`.
- **Sin acentos ni `ñ`** en los nombres.
- **Nombres cortos, pero claros.**
- **Idioma coherente**: todo en español o todo en inglés, no mezclar.

#### Metodología BEM

**BEM** (*Block, Element, Modifier*) es una forma muy popular de nombrar:

| Parte | Qué es | Formato | Ejemplo |
|---|---|---|---|
| **Bloque** | Un componente independiente | `.bloque` | `.tarjeta` |
| **Elemento** | Una parte del bloque | `.bloque__elemento` | `.tarjeta__titulo` |
| **Modificador** | Una variante o estado | `.bloque--modificador` | `.tarjeta--destacada` |

```css
.tarjeta { }
.tarjeta__titulo { }
.tarjeta__texto { }
.tarjeta--destacada { }

.boton { }
.boton--principal { }
.boton--grande { }
```

Ventajas: nombres predecibles, **especificidad baja y uniforme** (solo clases), y componentes que no se pisan entre sí.

### 4.4 Controlar la especificidad

- **Usa clases** como herramienta principal. Evita IDs para estilos.
- **Evita selectores largos**: `main .contenido article ul li a` es frágil.
- **Evita encadenar con etiquetas**: `div.tarjeta` es más específico que `.tarjeta` sin necesidad.
- **Evita `!important`**. Solo para utilidades muy concretas (`.oculto { display: none !important; }`) o para sobrescribir CSS de terceros.
- **Mantén las especificidades parecidas** entre componentes, para que el orden decida.
- **Usa `:where()`** para estilos base que quieras fácilmente sobrescribibles.

```css
/* ❌ Frágil */
body main .contenido ul.lista li.item a.enlace { color: red; }

/* ✅ Robusto */
.enlace-lista { color: red; }
```

### 4.5 Reset y estilos base

Cada navegador aplica sus propios estilos por defecto. Un **reset** mínimo da un punto de partida uniforme:

```css
*,
*::before,
*::after {
  box-sizing: border-box;
}

body {
  margin: 0;
  font-family: system-ui, sans-serif;
  line-height: 1.6;
}

img,
picture,
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

p,
h1,
h2,
h3 {
  overflow-wrap: break-word;
}
```

También existen resets listos: **Modern CSS Reset**, **normalize.css** o el reset de Josh Comeau.

### 4.6 Usar variables

- Guarda en variables **todo lo que se repite**: colores, espacios, fuentes, radios, sombras.
- Nómbralas por **función**, no por valor.
- Define **temas** cambiando variables (ver [[13 - Variables y funciones]]).

```css
:root {
  --color-principal: #e11d48;
  --espacio-m: 1rem;
  --radio: 8px;
}
```

### 4.7 Estrategia *mobile-first*

- Escribe primero el diseño para **móvil**.
- Añade mejoras con **`min-width`** para pantallas mayores.
- **Agrupa las media queries junto al componente** que modifican.
- Usa primero **Flexbox, Grid y `clamp()`**; las media queries son el refuerzo (ver [[10 - Responsive Design]]).

### 4.8 Comentarios

Comenta **el porqué**, no el qué.

```css
/* ❌ Inútil */
/* Hace el texto rojo */
.error { color: red; }

/* ✅ Útil */
/* Safari ignora min-height en este contenedor; usamos height para igualarlo */
.panel { height: 100%; }
```

Y usa comentarios para **dividir secciones**:

```css
/* ==========================================================================
   COMPONENTE: Tarjeta
   ========================================================================== */
```

### 4.9 Reutilización: no te repitas

Si repites el mismo bloque de declaraciones varias veces:

- Agrúpalas en una **clase común**.
- O usa una **variable**.
- O usa una **lista de selectores** con coma.

```css
/* ❌ Repetido */
.boton-a { padding: 0.6rem 1.2rem; border-radius: 8px; }
.boton-b { padding: 0.6rem 1.2rem; border-radius: 8px; }

/* ✅ Compartido */
.boton { padding: 0.6rem 1.2rem; border-radius: 8px; }
.boton--a { background: crimson; }
.boton--b { background: navy; }
```

> [!note]
> No lleves la reutilización al extremo: crear una clase por cada propiedad (`.mt-1`, `.p-2`…) puede ser útil (enfoque *utility-first*, como Tailwind), pero es una forma de trabajar distinta. Elige un enfoque y sé coherente.

### 4.10 Accesibilidad

| Aspecto | Qué hacer |
|---|---|
| **Foco visible** | Nunca quites `outline` sin dar otra señal clara; usa `:focus-visible` |
| **Contraste** | Texto/fondo con al menos **4.5:1** (3:1 en texto grande) |
| **Tamaños** | Usa `rem` para texto para respetar el zoom y los ajustes del usuario |
| **Zoom** | No bloquees el zoom en el `viewport` |
| **Movimiento** | Respeta `prefers-reduced-motion` |
| **Color** | No uses solo el color para transmitir información |
| **Objetivos táctiles** | Botones y enlaces de al menos unos **44×44px** |
| **Ocultar contenido** | `display: none` lo oculta a todos; para ocultar solo a la vista usa una clase `.solo-lectores` |
| **Orden visual** | No cambies el orden con `order` o Grid si altera el sentido de lectura |

```css
/* Texto oculto visualmente, pero legible por lectores de pantalla */
.solo-lectores {
  position: absolute;
  width: 1px;
  height: 1px;
  margin: -1px;
  padding: 0;
  overflow: hidden;
  clip-path: inset(50%);
  white-space: nowrap;
  border: 0;
}

/* Foco visible y coherente */
:focus-visible {
  outline: 3px solid #4a90e2;
  outline-offset: 2px;
}

@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
  }
}
```

### 4.11 Rendimiento

- **Anima `transform` y `opacity`**, no `width`, `height`, `top` o `left` (ver [[11 - Transiciones y animaciones]]).
- **Evita selectores extremadamente complejos** o `:has()` demasiado general.
- **Elimina CSS sin usar** (herramientas como PurgeCSS o las DevTools → *Coverage*).
- **Minifica el CSS** en producción.
- **Carga el CSS crítico primero** y el resto de forma diferida si el proyecto es grande.
- **Usa `font-display: swap`** y el formato `woff2` en las fuentes (ver [[06 - Texto y fuentes]]).
- **Limita el uso de sombras, filtros y `backdrop-filter`** grandes en muchos elementos.
- **Usa `content-visibility: auto`** en secciones largas fuera de pantalla para acelerar el pintado.
- **Evita `@import`** en CSS para cargar archivos: ralentiza la carga. Usa varios `<link>` o un empaquetador.
- **Usa `will-change` solo cuando sea necesario.**

### 4.12 Herramientas útiles

| Herramienta | Para qué sirve |
|---|---|
| **DevTools** del navegador | Inspeccionar, depurar y probar estilos en vivo |
| **Stylelint** | Linter: detecta errores y fuerza un estilo coherente |
| **Prettier** | Formatea el código automáticamente |
| **Autoprefixer** (PostCSS) | Añade prefijos de navegador solo cuando hacen falta |
| **Lighthouse** | Mide rendimiento, accesibilidad y buenas prácticas |
| **caniuse.com** / **MDN** | Comprueban la compatibilidad y documentan todo |
| **Contrast checker** | Verifica el contraste de colores |

### 4.13 Depurar CSS

Cuando algo no sale como esperas:

1. **Inspecciona el elemento** con las DevTools.
2. Mira la pestaña de **estilos**: ¿se aplica tu regla? ¿aparece tachada (la ha anulado otra)?
3. Revisa el **diagrama del Box Model**: ¿tamaños y márgenes correctos?
4. Comprueba la **especificidad** y el **orden** de las reglas.
5. Busca **errores de escritura** (una llave o `;` mal puesto, una propiedad mal escrita).
6. **Aísla el problema**: comenta reglas hasta encontrar la culpable.
7. Añade temporalmente `outline: 1px solid red;` a todos los elementos para ver sus cajas:

```css
* {
  outline: 1px solid red;   /* solo para depurar; bórralo después */
}
```

---

## 5. Ejemplos prácticos

### Ejemplo básico

Reglas ordenadas y con variables:

```css
:root {
  --color-principal: #e11d48;
  --radio: 8px;
}

.boton {
  display: inline-block;
  padding: 0.6rem 1.2rem;
  border-radius: var(--radio);
  background: var(--color-principal);
  color: white;
}
```

### Ejemplo habitual

Un componente con BEM, variantes y especificidad baja:

```css
/* ==========================================================================
   COMPONENTE: Tarjeta
   ========================================================================== */
.tarjeta {
  display: grid;
  gap: 1rem;
  padding: 1.5rem;
  border: 1px solid var(--color-borde);
  border-radius: var(--radio);
  background: var(--color-fondo);
}

.tarjeta__titulo {
  margin: 0;
  font-size: 1.25rem;
  line-height: 1.2;
}

.tarjeta__texto {
  margin: 0;
  color: var(--color-texto-suave);
}

/* Variante */
.tarjeta--destacada {
  border-color: gold;
  background: color-mix(in srgb, gold 10%, var(--color-fondo));
}
```

### Ejemplo completo

Base de proyecto con capas, reset, variables, accesibilidad y respeto de preferencias:

```css
/* ==========================================================================
   ORDEN DE CAPAS
   ========================================================================== */
@layer reset, base, layout, componentes, utilidades;

/* ==========================================================================
   RESET
   ========================================================================== */
@layer reset {
  *,
  *::before,
  *::after {
    box-sizing: border-box;
  }

  body,
  h1,
  h2,
  h3,
  p {
    margin: 0;
  }

  img,
  svg,
  video {
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
}

/* ==========================================================================
   BASE: variables y estilos de etiquetas
   ========================================================================== */
@layer base {
  :root {
    color-scheme: light dark;

    --color-principal: #e11d48;
    --color-fondo: light-dark(#ffffff, #121212);
    --color-texto: light-dark(#18181b, #fafafa);
    --color-borde: light-dark(#e4e4e7, #3f3f46);
    --espacio-m: 1rem;
    --radio: 8px;
  }

  body {
    background: var(--color-fondo);
    color: var(--color-texto);
    font-family: system-ui, sans-serif;
    line-height: 1.6;
  }

  h1,
  h2,
  h3 {
    line-height: 1.2;
    text-wrap: balance;
  }

  :focus-visible {
    outline: 3px solid #4a90e2;
    outline-offset: 2px;
  }
}

/* ==========================================================================
   LAYOUT
   ========================================================================== */
@layer layout {
  .contenedor {
    width: min(100% - 2rem, 1100px);
    margin-inline: auto;
  }

  .galeria {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
    gap: var(--espacio-m);
  }
}

/* ==========================================================================
   COMPONENTES
   ========================================================================== */
@layer componentes {
  .boton {
    display: inline-block;
    padding: 0.6rem 1.2rem;
    border-radius: var(--radio);
    background: var(--color-principal);
    color: white;
    text-decoration: none;
    transition: transform 0.15s ease;
  }

  .boton:hover {
    transform: translateY(-2px);
  }
}

/* ==========================================================================
   UTILIDADES
   ========================================================================== */
@layer utilidades {
  .solo-lectores {
    position: absolute;
    width: 1px;
    height: 1px;
    margin: -1px;
    overflow: hidden;
    clip-path: inset(50%);
    white-space: nowrap;
  }

  .centrado {
    text-align: center;
  }
}

/* ==========================================================================
   PREFERENCIAS DEL USUARIO
   ========================================================================== */
@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}
```

---

## 6. Buenas prácticas (lista de comprobación)

**Organización**
- [ ] El código sigue un orden claro (reset → base → layout → componentes → utilidades).
- [ ] Hay un único formato (sangría, espacios, saltos de línea).
- [ ] Un componente = un bloque de CSS (o un archivo).

**Nombres y selectores**
- [ ] Clases con nombres descriptivos, en minúsculas y con guiones.
- [ ] Nombres por función, no por apariencia.
- [ ] Especificidad baja: sobre todo clases, sin IDs ni cadenas largas.
- [ ] Sin `!important` (salvo casos justificados).

**Diseño**
- [ ] `box-sizing: border-box` en todo.
- [ ] Unidades relativas (`rem`, `%`, `ch`, `clamp()`).
- [ ] Mobile-first con `min-width`.
- [ ] Flexbox y Grid antes que `float` o `position`.
- [ ] `gap` en lugar de márgenes para separar ítems.

**Mantenimiento**
- [ ] Variables para colores, espacios, fuentes y radios.
- [ ] Sin código repetido.
- [ ] Comentarios que explican el porqué.
- [ ] Sin CSS que ya no se usa.

**Accesibilidad**
- [ ] Foco visible en todo lo interactivo.
- [ ] Contraste suficiente.
- [ ] `prefers-reduced-motion` respetado.
- [ ] Zoom permitido y texto en `rem`.

**Rendimiento**
- [ ] Animaciones con `transform` y `opacity`.
- [ ] CSS minificado en producción.
- [ ] Fuentes en `woff2` con `font-display: swap`.

---

## 7. Diferencias importantes

| Comparación | Diferencia |
|---|---|
| Clase vs ID para estilos | La clase es reutilizable y de especificidad media; el ID es único y muy fuerte. |
| Nombre por función vs por apariencia | `.alerta` sigue valiendo si cambia el color; `.rojo` no. |
| BEM vs nombres libres | BEM da estructura predecible y baja especificidad; los nombres libres son más cortos, pero menos coherentes. |
| *Mobile-first* vs *desktop-first* | El primero parte de lo simple y añade; el segundo parte de lo complejo y quita. |
| Reset vs normalize | El reset **borra** estilos del navegador; normalize los **uniformiza** conservando los útiles. |
| `!important` vs `@layer` | `!important` rompe la cascada; `@layer` la ordena de forma limpia. |
| CSS escrito a mano vs *utility-first* | A mano: clases semánticas; *utility-first*: muchas clases pequeñas de una propiedad. Ambos válidos si eres coherente. |
| `display: none` vs `.solo-lectores` | `none` oculta para todos; `.solo-lectores` oculta a la vista pero lo leen los lectores de pantalla. |
| Medidas en `px` vs `rem` | `px` es fijo; `rem` respeta la configuración del usuario. |

---

## 8. Casos especiales

- **Proyectos con frameworks** (React, Vue, Angular, Svelte): el CSS suele ir **ligado al componente** (CSS Modules, estilos con ámbito, `<style scoped>`). Las buenas prácticas siguen valiendo, pero la organización por archivos cambia.
- **CSS de terceros** (Bootstrap, librerías): colócalo en una **capa baja** con `@layer` y sobrescribe encima, sin `!important`.
- **Proyectos antiguos** con mucho `!important` y especificidad desordenada: no los reescribas de golpe. **Mejora por partes**: arregla lo que toques y añade `@layer` poco a poco.
- **Propiedades abreviadas** (`margin`, `padding`, `background`, `font`) **reinician** los valores que no escribes. Úsalas con cuidado cuando quieras cambiar solo una parte.
- **Orden de las reglas**: con la misma especificidad, gana la última. Por eso las media queries van **después** de la regla base.
- **Prefijos de navegador**: casi nunca hacen falta a mano; deja que **Autoprefixer** los añada según tu lista de navegadores soportados.
- **Código "muerto"**: antes de borrar un selector, comprueba con una búsqueda global que no se use en HTML, JavaScript ni plantillas.
- **Selectores generados con JavaScript**: las clases añadidas por código no aparecen en el HTML original; no las borres como "sin usar".
- **`z-index`**: define una escala con variables (`--z-menu: 10`, `--z-modal: 100`) en lugar de valores sueltos tipo `9999`.
- **Impresión**: considera un bloque `@media print` en webs de contenido (artículos, facturas).
- **Navegadores muy antiguos**: decide qué navegadores soportas y ajusta tus herramientas a esa decisión, no al revés.

> [!warning] Obsoleto / legado
> Prácticas **que ya no se recomiendan**: maquetar con `<table>` o con `float`, usar `clearfix`, poner estilos en línea (`style="..."`) para todo, usar atributos de presentación de HTML (`align`, `bgcolor`, `<font>`), hacks para Internet Explorer (`*zoom`, `_height`, `filter: alpha()`), `-webkit-box` para layout, y abusar de `!important`.

---

## 9. Resumen

- Un buen CSS es **legible, predecible, reutilizable, escalable, accesible y rápido**.
- **Organiza** el código: reset → base → layout → componentes → utilidades, idealmente con **`@layer`**.
- **Nombra por función**, en minúsculas y con guiones; **BEM** da estructura (`bloque__elemento--modificador`).
- Mantén la **especificidad baja**: clases, sin IDs, sin cadenas largas, sin `!important`.
- Usa **variables** para todo lo que se repite y para los temas.
- Escribe **mobile-first**, con unidades relativas, Flexbox, Grid y `clamp()`.
- Aplica un **reset mínimo** con `box-sizing: border-box`, imágenes fluidas y `font: inherit` en formularios.
- **Accesibilidad**: foco visible, buen contraste, `rem`, `prefers-reduced-motion`, zonas táctiles grandes.
- **Rendimiento**: anima `transform` y `opacity`, elimina CSS sin usar, minifica y carga bien las fuentes.
- **Comenta el porqué**, no el qué, y apóyate en herramientas: DevTools, Stylelint, Prettier, Autoprefixer y Lighthouse.
- Ante un error: **inspecciona, revisa especificidad y orden, y aísla el problema**.