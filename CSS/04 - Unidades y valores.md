# Unidades y valores

> [!info] ¿Qué es?
> Cada propiedad de CSS necesita un **valor**: un color, un tamaño, una palabra clave… Las **unidades** son las medidas que acompañan a los números (`px`, `rem`, `%`, `vh`…). Saber qué valor y qué unidad usar en cada caso es lo que hace que una web se adapte bien a cualquier pantalla.

---

## 1. Antes de empezar

Para entender esta nota conviene saber:

- Cómo se escribe una regla CSS: `selector { propiedad: valor; }` (ver [[01 - Fundamentos]]).
- Qué es el Box Model, porque casi todas las medidas se aplican a sus capas (ver [[03 - Box Model]]).

HTML que usaremos en los ejemplos:

```html
<body>
  <main class="contenedor">
    <h1>Título</h1>
    <p>Un párrafo de ejemplo.</p>
  </main>
</body>
```

---

## 2. Concepto fundamental

Un valor CSS puede ser de varios **tipos**. El tipo depende de la propiedad: `color` espera un color, `width` espera una medida, `display` espera una palabra clave.

Las medidas (longitudes) se dividen en dos grandes familias:

- **Absolutas**: siempre miden lo mismo (`px`).
- **Relativas**: dependen de otra cosa: el tamaño de la letra, el tamaño de la pantalla o el tamaño del elemento padre (`em`, `rem`, `%`, `vw`…).

> [!tip] Idea clave
> Las unidades relativas hacen que tu diseño **se adapte**. Si el usuario cambia el tamaño de letra de su navegador o abre la web en un móvil, las medidas relativas se ajustan solas.

---

## 3. Sintaxis / estructura

```css
propiedad: valor;
```

Reglas para escribir medidas:

- El número y la unidad van **pegados**, sin espacio: `16px` ✅, `16 px` ❌.
- Se pueden usar decimales: `1.5rem`, `0.5em`. También se puede omitir el 0: `.5rem`.
- Con el valor **`0`** no hace falta unidad: `margin: 0;`.
- Se admiten valores **negativos** en algunas propiedades: `margin-left: -10px;`.
- Algunas propiedades usan números **sin unidad**, como `line-height: 1.5` o `opacity: 0.8`.

---

## 4. Elementos / propiedades / características

### 4.1 Tipos de valores

| Tipo | Qué es | Ejemplos |
|---|---|---|
| Palabra clave | Una palabra predefinida | `block`, `none`, `bold`, `center` |
| Número | Un número sin unidad | `1.5`, `0.8`, `400` |
| Longitud | Un número con unidad | `16px`, `2rem`, `50vh` |
| Porcentaje | Un número con `%` | `50%`, `100%` |
| Color | Un color en distintos formatos | `red`, `#ff0000`, `rgb(255 0 0)` |
| Cadena de texto | Texto entre comillas | `"Hola"` |
| URL | Dirección de un recurso | `url("imagen.jpg")` |
| Función | Un valor calculado | `calc(100% - 20px)` |
| Tiempo | Segundos o milisegundos | `0.3s`, `300ms` |
| Ángulo | Grados | `45deg` |

### 4.2 Unidades absolutas

#### `px` (píxel)

La unidad más usada. Mide siempre lo mismo, sin depender de nada.

```css
.caja {
  width: 300px;
  border: 1px solid black;
}
```

Otras unidades absolutas existen (`cm`, `mm`, `in`, `pt`, `pc`), pero se usan casi solo para **impresión**:

| Unidad | Significado | Equivalencia aproximada |
|---|---|---|
| `cm` | Centímetros | 1cm ≈ 37.8px |
| `mm` | Milímetros | 10mm = 1cm |
| `in` | Pulgadas | 1in = 96px |
| `pt` | Puntos | 1pt ≈ 1.33px |
| `pc` | Picas | 1pc = 16px |

### 4.3 Unidades relativas al texto

#### `em`

Es relativa al **tamaño de letra del propio elemento** (o, si hablamos de `font-size`, al del padre).

```css
.boton {
  font-size: 16px;
  padding: 0.5em 1em;   /* 8px arriba/abajo, 16px a los lados */
}
```

Si cambias el `font-size` del botón, su padding **cambia con él**.

#### `rem` (*root em*)

Es relativa al tamaño de letra del **elemento raíz** (`<html>`). Normalmente el navegador pone 16px, así que `1rem = 16px`.

```css
html {
  font-size: 16px;      /* es el valor por defecto */
}

h1 {
  font-size: 2rem;      /* 32px */
}

.contenedor {
  padding: 1.5rem;      /* 24px */
}
```

#### Otras unidades de texto

| Unidad | Qué mide |
|---|---|
| `ch` | El ancho del carácter "0" de la fuente. Muy útil para limitar el ancho de los textos |
| `ex` | La altura de la letra "x" minúscula |
| `lh` | La altura de línea (`line-height`) del elemento |

```css
.articulo {
  max-width: 65ch;   /* unas 65 letras por línea: lectura cómoda */
}
```

### 4.4 Unidades de viewport

El **viewport** es la parte visible de la página en el navegador. Estas unidades se calculan con su tamaño.

| Unidad | Significado |
|---|---|
| `vw` | 1% del **ancho** del viewport |
| `vh` | 1% del **alto** del viewport |
| `vmin` | 1% del lado **menor** (ancho o alto) |
| `vmax` | 1% del lado **mayor** |

```css
.portada {
  width: 100vw;
  height: 100vh;       /* ocupa toda la pantalla */
}
```

Versiones modernas para móviles (por las barras del navegador que aparecen y desaparecen):

| Unidad | Qué mide |
|---|---|
| `svh` | Alto con las barras **visibles** (el más pequeño) |
| `lvh` | Alto con las barras **ocultas** (el más grande) |
| `dvh` | Alto que **se adapta** en cada momento |

```css
.portada {
  min-height: 100dvh;   /* mejor opción en móviles */
}
```

### 4.5 Porcentajes `%`

Un porcentaje es siempre **relativo a otra cosa**. Qué cosa depende de la propiedad:

| Propiedad | Porcentaje de... |
|---|---|
| `width`, `max-width` | El **ancho** del padre |
| `height` | El **alto** del padre (el padre debe tener alto definido) |
| `padding`, `margin` (cualquier lado) | El **ancho** del padre |
| `font-size` | El `font-size` del padre |
| `line-height` | El `font-size` del propio elemento |
| `top`, `left`... (con `position`) | Las dimensiones del bloque contenedor |

```css
.columna {
  width: 50%;      /* la mitad del ancho del padre */
}
```

### 4.6 Otras unidades

| Unidad | Para qué sirve |
|---|---|
| `fr` | Fracción del espacio libre en un Grid (ver [[08 - Grid]]) |
| `deg`, `rad`, `turn` | Ángulos: `45deg`, `0.25turn` |
| `s`, `ms` | Tiempo: `0.3s`, `300ms` (ver [[11 - Transiciones y animaciones]]) |
| `dpi`, `dppx` | Resolución de pantalla (en media queries) |

### 4.7 Valores especiales (palabras clave globales)

Todas las propiedades aceptan estas palabras:

| Valor | Qué hace |
|---|---|
| `inherit` | Toma el valor del **padre** |
| `initial` | Vuelve al valor **por defecto de CSS** |
| `unset` | Si la propiedad se hereda, actúa como `inherit`; si no, como `initial` |
| `revert` | Vuelve al estilo por defecto **del navegador** |
| `auto` | El navegador decide el valor (no es global, pero se usa en muchas propiedades) |

```css
a {
  color: inherit;         /* los enlaces toman el color del texto que los rodea */
  text-decoration: none;
}
```

### 4.8 Funciones de valor

#### `calc()`

Hace operaciones matemáticas mezclando unidades distintas.

```css
.barra {
  width: calc(100% - 250px);   /* todo el ancho menos 250px */
}
```

> [!warning]
> Dentro de `calc()`, los operadores `+` y `-` **necesitan espacios** a ambos lados. `calc(100%-20px)` no funciona.

#### `min()`, `max()` y `clamp()`

| Función | Qué hace |
|---|---|
| `min(a, b)` | Usa el valor **más pequeño** |
| `max(a, b)` | Usa el valor **más grande** |
| `clamp(mín, ideal, máx)` | Usa el ideal, pero **sin bajar del mínimo ni pasar del máximo** |

```css
.contenedor {
  width: min(90%, 1000px);          /* 90% del padre, pero nunca más de 1000px */
}

h1 {
  font-size: clamp(1.5rem, 4vw, 3rem);   /* tamaño que crece con la pantalla, con límites */
}
```

### 4.9 Cuándo usar cada unidad

| Quiero... | Uso |
|---|---|
| Tamaño de letra | `rem` |
| Padding y márgenes | `rem` o `em` |
| Bordes finos y sombras | `px` |
| Ancho flexible de una caja | `%`, `max-width` con `px` o `rem` |
| Ancho de un párrafo | `ch` |
| Ocupar la pantalla entera | `dvh`, `vw` |
| Que algo crezca con la pantalla, con límites | `clamp()` |
| Espacio en un Grid | `fr` |

---

## 5. Ejemplos prácticos

### Ejemplo básico

```css
h1 {
  font-size: 2rem;
  margin-bottom: 1rem;
}

p {
  font-size: 1rem;
  line-height: 1.6;
}
```

### Ejemplo habitual

Un contenedor que se adapta a cualquier pantalla y un texto cómodo de leer:

```css
.contenedor {
  width: min(90%, 1100px);
  margin-inline: auto;
  padding: 2rem 1rem;
}

.contenedor p {
  max-width: 65ch;
}
```

### Ejemplo completo

Una portada de pantalla completa con tipografía fluida:

```css
.portada {
  min-height: 100dvh;                       /* toda la pantalla, también en móvil */
  display: grid;
  place-content: center;
  padding: clamp(1rem, 5vw, 4rem);          /* el espacio crece con la pantalla */
  text-align: center;
}

.portada h1 {
  font-size: clamp(2rem, 6vw, 4.5rem);      /* mínimo 2rem, máximo 4.5rem */
  line-height: 1.1;
}

.portada p {
  max-width: 50ch;
  margin-inline: auto;
  font-size: clamp(1rem, 2vw, 1.25rem);
}

.barra-lateral {
  width: calc(100% - 280px);                /* ancho restante tras una columna fija */
}
```

---

## 6. Buenas prácticas

- **Usa `rem` para los tamaños de letra.** Respeta la configuración de accesibilidad del usuario (si agranda la letra en su navegador, tu web se agranda).
- **No fijes `font-size` en `px` en el `html`.** Déjalo en su valor por defecto o usa `100%`.
- **Usa `px` para detalles muy finos**: bordes de 1px, sombras, líneas.
- **Usa `max-width` en lugar de `width` fijo** para que las cajas se adapten.
- **Usa `line-height` sin unidad** (`1.5`): se calcula bien en los hijos.
- **Limita el ancho de los textos con `ch`** (unos 60-75 caracteres por línea).
- **Usa `dvh`** en vez de `vh` para alturas de pantalla completa en móviles.
- **Aprovecha `clamp()`** para tamaños fluidos sin escribir muchas media queries.
- **No mezcles demasiadas unidades distintas** sin motivo: mantiene el CSS más coherente.

---

## 7. Diferencias importantes

| Comparación | Diferencia |
|---|---|
| `em` vs `rem` | `em` depende del elemento (o del padre en `font-size`) y se puede acumular. `rem` depende siempre del `html`, así que es más predecible. |
| `px` vs `rem` | `px` es fijo. `rem` escala con la configuración de letra del usuario. |
| `%` vs `vw` | `%` es relativo al **padre**. `vw` es relativo a la **pantalla**. |
| `vh` vs `dvh` | `vh` ignora las barras del navegador móvil (puede cortar contenido). `dvh` se ajusta a ellas. |
| `line-height: 1.5` vs `line-height: 150%` | Sin unidad se recalcula en cada hijo; con `%` se calcula **una vez** en el padre y los hijos heredan ese valor ya fijado. |
| `inherit` vs `initial` | `inherit` copia el valor del padre. `initial` vuelve al valor original de CSS. |
| `min()` vs `max()` | `min()` impone un **techo**. `max()` impone un **suelo**. |

---

## 8. Casos especiales

- **`em` se acumula**: si anidas elementos con `font-size: 1.2em`, cada nivel multiplica el anterior (1.2 × 1.2 × 1.2...). Con `rem` no pasa.
- **`padding` y `margin` en `%`** se calculan con el **ancho** del padre, incluso `padding-top` y `margin-bottom`.
- **`height: 100%`** solo funciona si el **padre tiene una altura definida**. Si el padre tiene `height: auto`, el porcentaje no se resuelve.
- **`100vw` puede causar scroll horizontal**, porque incluye la barra de desplazamiento vertical. A veces es mejor `100%`.
- **El valor `0` no necesita unidad**, pero con `calc(0)` o en funciones de tiempo (`0s`) sí puede hacerla falta.
- **Dentro de `calc()`**, la multiplicación y la división no necesitan espacios, pero la suma y la resta sí. Además, `*` exige que al menos un lado sea un número sin unidad.
- **Unidades negativas**: `margin` admite negativos; `padding`, `width` y `height` **no**.
- **`font-size: 62.5%`** en el `html` es un truco antiguo para que `1rem = 10px`. Funciona, pero complica los cálculos y no es necesario hoy.
- **Números como cadenas**: `font-weight: 700` es un número, no una medida; no lleva unidad.

---

## 9. Resumen

- Cada propiedad espera un **tipo de valor**: palabra clave, número, longitud, porcentaje, color, función…
- Unidad **absoluta** principal: `px`. Unidades **relativas**: `em`, `rem`, `%`, `vw`, `vh`, `ch`…
- **`rem`** es la mejor opción para letras y espacios; **`px`** para detalles finos; **`%`** para anchos relativos al padre.
- **`vw`/`vh`** miden la pantalla; en móvil es mejor **`dvh`**.
- **`ch`** sirve para limitar el ancho de los textos.
- Valores globales: **`inherit`**, **`initial`**, **`unset`**, **`revert`**.
- **`calc()`** mezcla unidades; **`min()`**, **`max()`** y **`clamp()`** ponen límites y crean tamaños fluidos.
- Número y unidad van **pegados**; el `0` no necesita unidad; en `calc()`, `+` y `-` llevan espacios.
- Buena práctica: `rem` para texto, `max-width` en lugar de `width` fijo y `clamp()` para tipografía fluida.