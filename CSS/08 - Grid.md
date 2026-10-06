# Grid

> [!info] ¿Qué es?
> **CSS Grid** es un sistema para organizar elementos en **dos dimensiones a la vez**: filas y columnas. Es como dibujar una cuadrícula y decidir en qué celdas va cada elemento. Es ideal para el diseño general de una página y para galerías de tarjetas.

---

## 1. Antes de empezar

Para entender esta nota conviene saber:

- El Box Model (ver [[03 - Box Model]]).
- Las unidades de CSS, sobre todo `%` y `rem` (ver [[04 - Unidades y valores]]).
- Cómo funciona Flexbox, para poder compararlos (ver [[07 - Flexbox]]).

HTML que usaremos en los ejemplos:

```html
<div class="cuadricula">
  <div class="item">1</div>
  <div class="item">2</div>
  <div class="item">3</div>
  <div class="item">4</div>
  <div class="item">5</div>
  <div class="item">6</div>
</div>
```

---

## 2. Concepto fundamental

Igual que en Flexbox, hay dos piezas:

- **El contenedor grid** (el padre): el elemento con `display: grid`.
- **Los ítems grid** (los hijos directos): se colocan en las celdas.

Vocabulario básico:

| Término | Qué es |
|---|---|
| **Línea (line)** | Cada línea que separa filas o columnas. Se numeran desde 1 |
| **Pista (track)** | Una fila o una columna completa |
| **Celda (cell)** | Un hueco entre dos filas y dos columnas |
| **Área (area)** | Un conjunto de celdas rectangulares |
| **Gap** | El espacio entre pistas |

```text
Línea 1   Línea 2   Línea 3   Línea 4
   │  col 1  │  col 2  │  col 3  │
   ├─────────┼─────────┼─────────┤ ← Línea 1 (filas)
   │    1    │    2    │    3    │   fila 1
   ├─────────┼─────────┼─────────┤ ← Línea 2
   │    4    │    5    │    6    │   fila 2
   ├─────────┼─────────┼─────────┤ ← Línea 3
```

> [!tip] Idea clave
> Con Grid **defines la cuadrícula en el padre** y los hijos se colocan solos (o tú les dices dónde ir). Con Flexbox, en cambio, los hijos mandan más sobre el reparto.

---

## 3. Sintaxis / estructura

```css
.cuadricula {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;   /* 3 columnas iguales */
  gap: 1rem;
}
```

Con esto, los 6 hijos se reparten en 2 filas de 3 columnas.

| Se escriben en... | Propiedades |
|---|---|
| **El contenedor** | `display`, `grid-template-columns`, `grid-template-rows`, `grid-template-areas`, `gap`, `grid-auto-*`, `justify-items`, `align-items`, `justify-content`, `align-content` |
| **Los ítems** | `grid-column`, `grid-row`, `grid-area`, `justify-self`, `align-self` |

---

## 4. Elementos / propiedades / características

### 4.1 `display: grid` y `display: inline-grid`

| Valor | Efecto |
|---|---|
| `grid` | Contenedor de **bloque** (ocupa todo el ancho) |
| `inline-grid` | Contenedor **en línea** (ocupa solo lo necesario) |

### 4.2 Definir columnas y filas

```css
.cuadricula {
  grid-template-columns: 200px 1fr 2fr;   /* 3 columnas */
  grid-template-rows: 100px auto;         /* 2 filas */
}
```

Valores que puedes usar para el tamaño de cada pista:

| Valor | Significado |
|---|---|
| `200px`, `10rem`, `30%` | Medida fija o porcentaje |
| `auto` | Se ajusta al contenido (y ocupa el espacio sobrante) |
| `1fr` | **Fracción** del espacio libre |
| `min-content` | Lo mínimo que necesita el contenido |
| `max-content` | Lo máximo que ocupa el contenido sin saltar de línea |
| `minmax(150px, 1fr)` | Mínimo 150px, máximo 1fr |
| `fit-content(300px)` | Se ajusta al contenido, sin pasar de 300px |

#### La unidad `fr`

Una **fracción del espacio libre**. Se reparte lo que queda después de las medidas fijas.

```css
.pagina {
  grid-template-columns: 250px 1fr;     /* lateral fijo + contenido que ocupa el resto */
}

.tres {
  grid-template-columns: 1fr 2fr 1fr;   /* la del medio es el doble de ancha */
}
```

#### `repeat()`

Evita escribir lo mismo muchas veces.

```css
.cuadricula {
  grid-template-columns: repeat(3, 1fr);       /* igual que 1fr 1fr 1fr */
  grid-template-columns: repeat(2, 100px 1fr); /* patrón repetido */
}
```

### 4.3 `gap` (espacio entre pistas)

```css
.cuadricula {
  gap: 1rem;            /* igual en filas y columnas */
  gap: 2rem 1rem;       /* fila | columna */
}
```

También existen `row-gap` y `column-gap`.

### 4.4 Cuadrícula que se adapta sola: `auto-fit`, `auto-fill` y `minmax()`

Es la técnica más útil de Grid: columnas que **aparecen o desaparecen** según el ancho disponible, **sin media queries**.

```css
.galeria {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 1rem;
}
```

Se lee así: "pon todas las columnas de **al menos 250px** que quepan, y repártete el espacio sobrante a partes iguales".

| Valor | Qué hace con los huecos sobrantes |
|---|---|
| `auto-fit` | Las columnas **vacías se contraen** y las ocupadas crecen para llenar el ancho |
| `auto-fill` | Mantiene las **columnas vacías** (reserva el hueco aunque no haya ítems) |

### 4.5 Cuadrícula implícita: `grid-auto-rows`, `grid-auto-columns` y `grid-auto-flow`

La cuadrícula **explícita** es la que defines con `grid-template-*`. Si hay más ítems de los que caben, Grid crea filas o columnas **implícitas** automáticamente.

```css
.cuadricula {
  grid-template-columns: repeat(3, 1fr);
  grid-auto-rows: minmax(100px, auto);   /* alto de las filas creadas automáticamente */
}
```

| Propiedad | Qué controla |
|---|---|
| `grid-auto-rows` | Tamaño de las filas implícitas |
| `grid-auto-columns` | Tamaño de las columnas implícitas |
| `grid-auto-flow` | Cómo se colocan los ítems: `row` (por defecto), `column` o `dense` (rellena huecos) |

### 4.6 Colocar ítems con líneas

Se puede indicar entre qué líneas va un ítem.

```css
.cabecera {
  grid-column: 1 / 4;       /* desde la línea 1 hasta la 4 (ocupa 3 columnas) */
  grid-row: 1;              /* en la fila 1 */
}

.destacado {
  grid-column: span 2;      /* ocupa 2 columnas, desde donde le toque */
}

.todo {
  grid-column: 1 / -1;      /* de la primera a la última línea: ancho completo */
}
```

| Escribes | Significado |
|---|---|
| `grid-column: 2 / 4` | Empieza en la línea 2, termina en la 4 |
| `grid-column: span 3` | Ocupa 3 columnas |
| `grid-column: 1 / -1` | De la primera a la última línea |
| `grid-column-start`, `grid-column-end` | Las dos partes por separado |

### 4.7 Áreas con nombre: `grid-template-areas`

Permite **dibujar el diseño con texto**, muy fácil de leer.

```css
.pagina {
  display: grid;
  grid-template-columns: 250px 1fr;
  grid-template-rows: auto 1fr auto;
  grid-template-areas:
    "cabecera cabecera"
    "lateral  contenido"
    "pie      pie";
  min-height: 100dvh;
}

.cabecera  { grid-area: cabecera; }
.lateral   { grid-area: lateral; }
.contenido { grid-area: contenido; }
.pie       { grid-area: pie; }
```

- Cada palabra es una celda; si se repite, esa área ocupa varias celdas.
- Un punto `.` deja una celda vacía.
- Cada área debe formar un **rectángulo**.

### 4.8 Alinear los ítems dentro de sus celdas

| Propiedad | Eje | Dónde se escribe |
|---|---|---|
| `justify-items` | Horizontal (dentro de la celda) | Contenedor |
| `align-items` | Vertical (dentro de la celda) | Contenedor |
| `place-items` | Las dos a la vez (`align justify`) | Contenedor |
| `justify-self` | Horizontal, para **un** ítem | Ítem |
| `align-self` | Vertical, para **un** ítem | Ítem |
| `place-self` | Las dos para **un** ítem | Ítem |

Valores habituales: `start`, `end`, `center`, `stretch` (por defecto).

```css
.centrado {
  display: grid;
  place-items: center;      /* el truco más corto para centrar algo */
  min-height: 100dvh;
}
```

### 4.9 Alinear toda la cuadrícula dentro del contenedor

Si la cuadrícula es más pequeña que el contenedor, estas propiedades la colocan:

| Propiedad | Qué hace |
|---|---|
| `justify-content` | Coloca las columnas en el eje horizontal |
| `align-content` | Coloca las filas en el eje vertical |
| `place-content` | Las dos a la vez |

Valores: `start`, `end`, `center`, `stretch`, `space-between`, `space-around`, `space-evenly`.

### 4.10 Grids anidados y `subgrid`

Un ítem grid puede ser, a su vez, un contenedor grid. Con **`subgrid`** el hijo **hereda las pistas** del padre para alinear su contenido con la cuadrícula principal.

```css
.tarjetas {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
}

.tarjeta {
  display: grid;
  grid-row: span 3;
  grid-template-rows: subgrid;      /* las filas internas se alinean con las de las otras tarjetas */
}
```

---

## 5. Ejemplos prácticos

### Ejemplo básico

Tres columnas iguales con separación:

```css
.cuadricula {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 1rem;
}

.item {
  padding: 1rem;
  background: #eee;
}
```

### Ejemplo habitual

Galería de tarjetas que se adapta sola a cualquier pantalla:

```css
.galeria {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 1.5rem;
  padding: 1.5rem;
}

.tarjeta {
  padding: 1.5rem;
  border: 1px solid #ddd;
  border-radius: 12px;
}
```

### Ejemplo completo

Diseño de página con cabecera, lateral, contenido y pie, que en móvil pasa a una sola columna:

```html
<body class="pagina">
  <header class="cabecera">Cabecera</header>
  <aside class="lateral">Lateral</aside>
  <main class="contenido">Contenido</main>
  <footer class="pie">Pie</footer>
</body>
```

```css
.pagina {
  display: grid;
  min-height: 100dvh;
  gap: 1rem;
  grid-template-columns: 1fr;               /* móvil: una columna */
  grid-template-areas:
    "cabecera"
    "contenido"
    "lateral"
    "pie";
  grid-template-rows: auto 1fr auto auto;
}

@media (min-width: 768px) {
  .pagina {
    grid-template-columns: 250px 1fr;       /* escritorio: lateral + contenido */
    grid-template-areas:
      "cabecera cabecera"
      "lateral  contenido"
      "pie      pie";
    grid-template-rows: auto 1fr auto;
  }
}

.cabecera  { grid-area: cabecera; background: #222; color: white; padding: 1rem; }
.lateral   { grid-area: lateral;  background: #f4f4f4; padding: 1rem; }
.contenido { grid-area: contenido; padding: 1rem; }
.pie       { grid-area: pie;      background: #222; color: white; padding: 1rem; }
```

---

## 6. Buenas prácticas

- **Usa Grid para el diseño en dos dimensiones** (la estructura de la página, galerías, paneles). Para una sola fila o columna, usa [[07 - Flexbox]].
- **Usa `repeat(auto-fit, minmax(...))`** para galerías adaptables sin media queries.
- **Usa `fr`** en lugar de porcentajes: no tienes que restar el `gap`.
- **Usa `gap`** en lugar de márgenes.
- **Usa `grid-template-areas`** para diseños de página: se lee casi como un dibujo.
- **Combina Grid y Flexbox**: Grid para la estructura general, Flexbox para el contenido dentro de cada zona.
- **Mantén el orden del HTML lógico**; no reordenes con Grid cosas que cambien el sentido de lectura (los lectores de pantalla siguen el HTML).
- **Usa las DevTools**: el navegador dibuja las líneas y pistas de la cuadrícula al activarla.
- **No fijes alturas de fila** si el contenido puede crecer: usa `auto` o `minmax(..., auto)`.

---

## 7. Diferencias importantes

| Comparación | Diferencia |
|---|---|
| Grid vs Flexbox | Grid organiza en **dos dimensiones**; Flexbox en **una**. |
| `fr` vs `%` | `fr` reparte el **espacio libre** (descuenta el gap); `%` se calcula sobre el ancho total. |
| `auto-fit` vs `auto-fill` | `auto-fit` contrae las columnas vacías; `auto-fill` las mantiene. |
| `justify-items` vs `justify-content` | `justify-items` alinea el ítem **dentro de su celda**; `justify-content` alinea **toda la cuadrícula** en el contenedor. |
| `align-items` vs `align-self` | La primera va en el **padre** y afecta a todos; la segunda en **un hijo**. |
| Cuadrícula explícita vs implícita | La explícita la defines tú; la implícita la crea Grid cuando faltan celdas. |
| `grid-column: span 2` vs `1 / 3` | `span 2` ocupa 2 columnas desde donde esté; `1 / 3` fija las líneas exactas. |
| `minmax(0, 1fr)` vs `1fr` | `1fr` equivale a `minmax(auto, 1fr)`: no baja del tamaño del contenido. `minmax(0, 1fr)` sí puede encogerse más. |

---

## 8. Casos especiales

- **Solo los hijos directos son ítems grid.** Los nietos no se colocan en la cuadrícula (salvo con `subgrid`).
- **`1fr` no baja del contenido**: si un ítem tiene una palabra larga o una imagen grande, la columna puede desbordar. Se arregla con `minmax(0, 1fr)`.
- **Los ítems pueden solaparse** si los colocas en las mismas celdas. Se controla el orden de apilamiento con `z-index`.
- **`grid-auto-flow: dense`** rellena huecos, pero puede **cambiar el orden visual** respecto al HTML.
- **Márgenes y Grid**: los márgenes de los ítems se suman al `gap` y no colapsan.
- **Un ítem con `position: absolute`** sale de la cuadrícula; si el contenedor tiene `position: relative`, se posiciona respecto a él.
- **Áreas con nombre**: si no forman un rectángulo perfecto o hay un nombre mal escrito, la propiedad se ignora entera.
- **Líneas con nombre**: puedes nombrar las líneas entre corchetes: `grid-template-columns: [inicio] 1fr [medio] 1fr [fin];` y usarlas en `grid-column: inicio / fin`.
- **`min-height` en vez de `height`** en el contenedor para que pueda crecer con su contenido.
- **Imágenes dentro de una celda** se estiran por defecto: usa `object-fit` o `align-self`.

> [!warning] Obsoleto / legado
> La primera versión de Grid, con prefijo `-ms-grid` y propiedades como `-ms-grid-columns`, es de Internet Explorer y **ya no se usa**. Hoy basta con la sintaxis estándar.

---

## 9. Resumen

- **Grid** organiza elementos en **filas y columnas a la vez**.
- Se activa con **`display: grid`** y se define con **`grid-template-columns`** y **`grid-template-rows`**.
- **`fr`** reparte el espacio libre; **`repeat()`** evita repetir; **`minmax()`** pone mínimo y máximo.
- **`repeat(auto-fit, minmax(250px, 1fr))`** crea galerías adaptables sin media queries.
- **`gap`** separa las pistas.
- Los ítems se colocan solos, o con **`grid-column`**, **`grid-row`**, **`span`** o **`grid-area`**.
- **`grid-template-areas`** permite dibujar el diseño con nombres.
- **`place-items: center`** centra el contenido de las celdas; **`place-content`** coloca toda la cuadrícula.
- Grid para la estructura, Flexbox para el contenido: se combinan muy bien.
- **`subgrid`** permite alinear el contenido interno con la cuadrícula principal.