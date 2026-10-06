# Responsive Design

> [!info] ¿Qué es?
> El **diseño responsive** (o adaptable) consiste en crear páginas web que **se ven y funcionan bien en cualquier dispositivo**: móvil, tableta, portátil o pantalla grande. En vez de hacer una web distinta para cada pantalla, se escribe **un solo diseño que se adapta** al espacio disponible.

---

## 1. Antes de empezar

Para entender esta nota conviene saber:

- Las unidades relativas: `%`, `rem`, `vw`, `clamp()` (ver [[04 - Unidades y valores]]).
- Cómo funcionan Flexbox y Grid (ver [[07 - Flexbox]] y [[08 - Grid]]).
- Que el **viewport** es la parte visible de la página en el navegador.

HTML que usaremos en los ejemplos:

```html
<header class="cabecera">
  <a href="#" class="logo">MiWeb</a>
  <nav class="menu">
    <a href="#">Inicio</a>
    <a href="#">Blog</a>
    <a href="#">Contacto</a>
  </nav>
</header>

<main class="contenedor">
  <article class="tarjeta">...</article>
  <article class="tarjeta">...</article>
  <article class="tarjeta">...</article>
</main>
```

---

## 2. Concepto fundamental

El diseño responsive se apoya en **tres ideas**:

1. **Una etiqueta `viewport`** en el HTML, para que los móviles muestren la página a su tamaño real.
2. **Diseños flexibles**: anchos relativos, Flexbox, Grid, `max-width`.
3. **Media queries**: reglas CSS que **solo se aplican** cuando se cumple una condición (por ejemplo, "pantalla de al menos 768px de ancho").

### Enfoque *mobile-first*

Lo recomendado es escribir **primero el diseño para móvil** (el más sencillo) y luego **añadir mejoras** para pantallas más grandes con `min-width`.

```text
Móvil (base)  →  Tableta (@media min-width: 768px)  →  Escritorio (@media min-width: 1024px)
 sencillo           añade columnas                        añade más espacio
```

> [!tip] Idea clave
> Con *mobile-first* escribes menos CSS y los móviles (que son más lentos) cargan lo mínimo. Cada media query solo **añade o cambia** lo necesario.

---

## 3. Sintaxis / estructura

### La etiqueta viewport (en el HTML)

Sin ella, los móviles muestran la web como si fuera de escritorio y la reducen. Va dentro del `<head>`:

```html
<meta name="viewport" content="width=device-width, initial-scale=1">
```

### Estructura de una media query

```css
@media (condición) {
  /* reglas que solo se aplican si se cumple la condición */
}
```

```css
@media (min-width: 768px) {
  .contenedor {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
  }
}
```

---

## 4. Elementos / propiedades / características

### 4.1 Condiciones más usadas

| Condición | Se aplica cuando... |
|---|---|
| `(min-width: 768px)` | El ancho es **768px o más** |
| `(max-width: 767px)` | El ancho es **767px o menos** |
| `(min-width: 600px) and (max-width: 900px)` | El ancho está **entre** 600 y 900px |
| `(orientation: landscape)` | La pantalla está en **horizontal** |
| `(orientation: portrait)` | La pantalla está en **vertical** |
| `(prefers-color-scheme: dark)` | El usuario prefiere el **modo oscuro** |
| `(prefers-reduced-motion: reduce)` | El usuario prefiere **menos animaciones** |
| `(hover: hover)` | El dispositivo permite **hover** (ratón) |
| `(pointer: coarse)` | El dispositivo se maneja con un **dedo** (táctil) |
| `print` | Al **imprimir** la página |

Existe también una **sintaxis moderna con rangos**, más fácil de leer:

```css
@media (width >= 768px) {
  /* equivale a min-width: 768px */
}

@media (600px <= width <= 900px) {
  /* entre 600 y 900px */
}
```

### 4.2 Puntos de ruptura (*breakpoints*)

Son los anchos en los que el diseño cambia. Valores orientativos:

| Dispositivo | Ancho aproximado |
|---|---|
| Móvil | hasta 600px |
| Tableta | 600px - 1024px |
| Portátil | 1024px - 1280px |
| Escritorio grande | 1280px o más |

```css
/* Base: móvil */
.contenedor {
  padding: 1rem;
}

@media (min-width: 768px) {
  /* Tableta y superiores */
  .contenedor {
    padding: 2rem;
  }
}

@media (min-width: 1024px) {
  /* Portátil y superiores */
  .contenedor {
    max-width: 1100px;
    margin-inline: auto;
  }
}
```

> [!note]
> No existen puntos de ruptura "oficiales". Lo mejor es **cambiar el diseño cuando el contenido empiece a verse mal**, no cuando se alcance el ancho de un dispositivo concreto.

### 4.3 Diseños flexibles (la base)

Antes de usar media queries, haz que el diseño se adapte solo:

```css
.contenedor {
  width: min(90%, 1100px);       /* flexible, con límite */
  margin-inline: auto;
}

.galeria {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));   /* columnas automáticas */
  gap: 1rem;
}

.fila {
  display: flex;
  flex-wrap: wrap;
  gap: 1rem;
}
```

Muchas veces, con Flexbox, Grid y `clamp()` **no hacen falta media queries**.

### 4.4 Tipografía y espacios fluidos

```css
h1 {
  font-size: clamp(2rem, 5vw, 3.5rem);
}

.seccion {
  padding: clamp(1rem, 4vw, 4rem);
}
```

### 4.5 Imágenes responsive

#### Con CSS

```css
img {
  max-width: 100%;      /* nunca más ancha que su contenedor */
  height: auto;         /* mantiene la proporción */
  display: block;
}
```

#### `object-fit` (cuando la imagen tiene tamaño fijo)

```css
.avatar {
  width: 100%;
  height: 200px;
  object-fit: cover;        /* recorta para llenar sin deformar */
}
```

| Valor | Efecto |
|---|---|
| `cover` | Llena la caja, recortando si hace falta |
| `contain` | Se ve entera, puede dejar huecos |
| `fill` | Se estira y se deforma |
| `none` | Tamaño original |

#### Con HTML: `srcset`, `sizes` y `<picture>`

Permiten cargar **distintas imágenes** según la pantalla:

```html
<img
  src="foto-800.jpg"
  srcset="foto-400.jpg 400w, foto-800.jpg 800w, foto-1600.jpg 1600w"
  sizes="(min-width: 1024px) 50vw, 100vw"
  alt="Descripción de la foto">

<picture>
  <source media="(min-width: 768px)" srcset="banner-ancho.jpg">
  <img src="banner-movil.jpg" alt="Descripción del banner">
</picture>
```

### 4.6 Menú que se adapta

```css
/* Móvil: enlaces en columna */
.menu {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

/* Tableta y más: enlaces en fila */
@media (min-width: 768px) {
  .menu {
    flex-direction: row;
    gap: 1.5rem;
  }
}
```

### 4.7 Consultas de contenedor (*container queries*)

En lugar de mirar el tamaño de la **pantalla**, miran el tamaño del **contenedor** del componente. Así un mismo componente se adapta donde lo pongas.

```css
.zona {
  container-type: inline-size;     /* marca el contenedor */
  container-name: tarjetas;        /* nombre opcional */
}

.tarjeta {
  display: block;
}

@container tarjetas (min-width: 500px) {
  .tarjeta {
    display: flex;                 /* si el contenedor mide 500px o más */
    gap: 1rem;
  }
}
```

### 4.8 Preferencias del usuario

```css
/* Modo oscuro */
@media (prefers-color-scheme: dark) {
  body {
    background: #121212;
    color: #eee;
  }
}

/* Menos movimiento */
@media (prefers-reduced-motion: reduce) {
  * {
    animation: none !important;
    transition: none !important;
  }
}

/* Solo efectos hover si hay ratón */
@media (hover: hover) {
  .boton:hover {
    background: crimson;
  }
}
```

### 4.9 Estilos para impresión

```css
@media print {
  .menu,
  .publicidad {
    display: none;             /* oculta lo que no se debe imprimir */
  }

  body {
    color: black;
    background: white;
  }
}
```

### 4.10 Cómo probar un diseño responsive

- **DevTools** del navegador (F12) → botón de **modo dispositivo** (icono de móvil y tableta): permite cambiar el ancho y simular móviles.
- Redimensiona la ventana del navegador y observa **dónde se rompe** el diseño.
- Prueba en **dispositivos reales**, sobre todo en móviles.
- Prueba también con **zoom** y con letra más grande.

---

## 5. Ejemplos prácticos

### Ejemplo básico

Un contenedor que cambia de una a dos columnas:

```css
.contenedor {
  display: grid;
  gap: 1rem;
}

@media (min-width: 768px) {
  .contenedor {
    grid-template-columns: 1fr 1fr;
  }
}
```

### Ejemplo habitual

Cabecera con menú que pasa de columna a fila, e imágenes adaptables:

```css
.cabecera {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1rem;
  padding: 1rem;
}

.menu {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 1rem;
}

img {
  max-width: 100%;
  height: auto;
}

@media (min-width: 768px) {
  .cabecera {
    flex-direction: row;
    justify-content: space-between;
  }
}
```

### Ejemplo completo

Diseño *mobile-first* con tarjetas automáticas, tipografía fluida, modo oscuro y reducción de animaciones:

```css
:root {
  --fondo: #ffffff;
  --texto: #222222;
  --tarjeta: #f4f4f4;
}

*,
*::before,
*::after {
  box-sizing: border-box;
}

body {
  margin: 0;
  background: var(--fondo);
  color: var(--texto);
  font-family: system-ui, sans-serif;
  line-height: 1.6;
}

.contenedor {
  width: min(92%, 1100px);
  margin-inline: auto;
  padding-block: clamp(1rem, 4vw, 3rem);
}

h1 {
  font-size: clamp(1.75rem, 5vw, 3rem);
  line-height: 1.15;
}

/* Tarjetas: columnas automáticas sin media queries */
.galeria {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(240px, 1fr));
  gap: 1.5rem;
}

.tarjeta {
  padding: 1.5rem;
  border-radius: 12px;
  background: var(--tarjeta);
  transition: transform 0.2s ease;
}

img {
  max-width: 100%;
  height: auto;
  display: block;
}

/* Solo hover si hay ratón */
@media (hover: hover) {
  .tarjeta:hover {
    transform: translateY(-4px);
  }
}

/* Pantallas grandes: más espacio */
@media (min-width: 1024px) {
  .galeria {
    gap: 2rem;
  }
}

/* Modo oscuro */
@media (prefers-color-scheme: dark) {
  :root {
    --fondo: #121212;
    --texto: #eeeeee;
    --tarjeta: #1e1e1e;
  }
}

/* Menos movimiento */
@media (prefers-reduced-motion: reduce) {
  .tarjeta {
    transition: none;
  }
}
```

---

## 6. Buenas prácticas

- **Incluye siempre la etiqueta `viewport`** en el `<head>`.
- **Diseña primero para móvil** (*mobile-first*) y añade mejoras con `min-width`.
- **Aprovecha Flexbox, Grid y `clamp()`** antes de recurrir a muchas media queries.
- **Usa unidades relativas** (`rem`, `%`, `ch`, `vw`) en vez de píxeles fijos.
- **Pon `max-width: 100%` y `height: auto` a las imágenes.**
- **Elige los puntos de ruptura según el contenido**, no según modelos de dispositivos.
- **No uses ocultar contenido** como solución para móvil: intenta reorganizarlo.
- **Los elementos táctiles deben ser grandes**: botones y enlaces de al menos unos 44×44px.
- **Respeta las preferencias del usuario**: modo oscuro, menos movimiento.
- **No bloquees el zoom** (`user-scalable=no`): perjudica la accesibilidad.
- **Prueba en dispositivos reales**, no solo en el navegador.
- **Agrupa las media queries cerca del componente** al que afectan, para que sea fácil mantenerlas.

---

## 7. Diferencias importantes

| Comparación | Diferencia |
|---|---|
| `min-width` vs `max-width` | `min-width` aplica **desde** ese ancho hacia arriba (mobile-first); `max-width` aplica **hasta** ese ancho hacia abajo (desktop-first). |
| *Mobile-first* vs *desktop-first* | El primero parte del diseño móvil y añade; el segundo parte del escritorio y quita. |
| Media query vs container query | La media query mira el tamaño de la **pantalla**; la container query, el del **contenedor**. |
| `%` vs `vw` | `%` es relativo al **padre**; `vw` a la **ventana**. |
| `width` vs `max-width` | `width` fija el ancho; `max-width` pone un tope y deja que se adapte. |
| `<img srcset>` vs `<picture>` | `srcset` ofrece la misma imagen en varios tamaños; `<picture>` permite imágenes **distintas** (distintos recortes) según la pantalla. |
| *Responsive* vs *adaptive* | Responsive cambia de forma **continua**; adaptive salta entre diseños fijos para ciertos anchos. |
| `hover: hover` vs `hover` normal | La primera aplica el efecto solo si el dispositivo realmente tiene ratón. |

---

## 8. Casos especiales

- **Sin la etiqueta viewport**, las media queries por ancho no se comportan como esperas en móvil.
- **`vh` en móvil** cambia cuando aparece o desaparece la barra del navegador: usa `dvh` o `svh` (ver [[04 - Unidades y valores]]).
- **El orden de las media queries importa**: con la misma especificidad, gana la última. En mobile-first van de menor a mayor `min-width`.
- **No mezcles `min-width` y `max-width`** en el mismo proyecto sin necesidad; complica los solapes.
- **`max-width: 767px` y `min-width: 768px`**: usa valores que no se solapen. Si usas `max-width: 768px` y `min-width: 768px`, a 768px se aplican las dos.
- **Zoom del usuario**: al hacer zoom, el ancho "visible" en CSS baja y se activan las media queries de pantallas pequeñas. Es lo esperado.
- **Texto largo y palabras largas**: usa `overflow-wrap: break-word` para evitar desbordamientos horizontales (ver [[06 - Texto y fuentes]]).
- **Tablas anchas**: ponlas en un contenedor con `overflow-x: auto` para que tengan scroll horizontal en móvil.
- **Scroll horizontal accidental**: suele venir de un elemento más ancho que la pantalla (`100vw`, imagen sin `max-width`, margen negativo). Se localiza con las DevTools.
- **Container queries** necesitan que el contenedor tenga `container-type`, y el elemento que se estila debe estar **dentro** de él (no puede ser el propio contenedor).
- **Diseño para pantallas plegables y con muescas**: se puede usar `env(safe-area-inset-*)` para respetar las zonas seguras.

---

## 9. Resumen

- **Responsive** significa que **un solo diseño se adapta** a cualquier pantalla.
- Necesitas la etiqueta **`<meta name="viewport" content="width=device-width, initial-scale=1">`**.
- Trabaja con *mobile-first*: base para móvil y **`@media (min-width: …)`** para ampliar.
- Primero usa diseños flexibles (**Flexbox**, **Grid**, **`auto-fit` + `minmax()`**, **`clamp()`**); las media queries son el refuerzo.
- Imágenes: **`max-width: 100%`**, **`height: auto`**, `object-fit`, y `srcset` o `<picture>` en HTML.
- Las **container queries** adaptan un componente según el tamaño de su contenedor.
- Las media queries también detectan **preferencias del usuario**: `prefers-color-scheme`, `prefers-reduced-motion`, `hover`, `pointer`.
- Los puntos de ruptura se eligen **según el contenido**, no según dispositivos.
- Prueba con las **DevTools** y en dispositivos reales.
- Cuida la accesibilidad: zoom permitido, botones táctiles grandes y contraste suficiente.