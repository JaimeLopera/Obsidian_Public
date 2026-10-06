# Colores y fondos

> [!info] ¿Qué es?
> CSS permite dar **color al texto**, a los **bordes** y al **fondo** de cualquier elemento. Los fondos pueden ser un color liso, una imagen, un degradado o varias de estas cosas a la vez. Esta nota explica los formatos de color, las propiedades `color` y `background`, los degradados y las sombras.

---

## 1. Antes de empezar

Para entender esta nota conviene saber:

- Cómo funciona el Box Model, porque el fondo se pinta en ciertas capas de la caja (ver [[03 - Box Model]]).
- Las unidades de CSS, sobre todo `%` y `px` (ver [[04 - Unidades y valores]]).

HTML que usaremos en los ejemplos:

```html
<section class="portada">
  <h1>Mi web</h1>
  <p>Un texto de ejemplo.</p>
  <a href="#" class="boton">Empezar</a>
</section>
```

---

## 2. Concepto fundamental

Hay dos ideas básicas:

- **`color`** pinta el **texto** de un elemento (y los bordes si no tienen color propio).
- **`background`** pinta lo que hay **detrás** del contenido: color, imagen o degradado.

El color se puede escribir en **varios formatos** (nombre, hexadecimal, `rgb()`, `hsl()`…). Todos sirven para lo mismo: describir un color. Elegir uno u otro es cuestión de comodidad.

> [!tip] Idea clave
> El fondo se pinta debajo del **contenido, el padding y el borde**. El **margen** nunca tiene fondo.

---

## 3. Sintaxis / estructura

```css
.caja {
  color: white;                       /* color del texto */
  background-color: #1e3a8a;          /* color de fondo */
  border: 2px solid rgb(255 255 255 / 0.5);
}
```

---

## 4. Elementos / propiedades / características

### 4.1 Formas de escribir un color

#### Nombres de color

CSS tiene unos 140 nombres: `red`, `blue`, `tomato`, `rebeccapurple`, `lightgray`…

```css
p {
  color: tomato;
}
```

Son cómodos para pruebas, pero poco precisos para un diseño real.

#### Hexadecimal

Empieza por `#` y tiene 3, 4, 6 u 8 dígitos (de `0` a `9` y de `a` a `f`).

| Formato | Ejemplo | Significado |
|---|---|---|
| `#rgb` | `#f00` | Abreviado: cada dígito se duplica (`#ff0000`) |
| `#rrggbb` | `#1e3a8a` | Rojo, verde y azul |
| `#rrggbbaa` | `#1e3a8a80` | Los 2 últimos dígitos son la **transparencia** |

#### `rgb()`

Rojo, verde y azul, de 0 a 255.

```css
.caja {
  background: rgb(30 58 138);           /* sintaxis moderna: con espacios */
  background: rgb(30, 58, 138);         /* sintaxis clásica: con comas (también válida) */
}
```

#### `hsl()`

**Tono** (0 a 360, la "posición" en el círculo de colores), **saturación** (%) y **luminosidad** (%).

```css
.caja {
  background: hsl(220 70% 30%);
}
```

- **Tono (hue)**: 0 = rojo, 120 = verde, 240 = azul.
- **Saturación**: 0% = gris, 100% = color intenso.
- **Luminosidad**: 0% = negro, 50% = color normal, 100% = blanco.

`hsl()` es muy cómodo para crear variantes: basta con subir o bajar la luminosidad.

#### Transparencia (canal alfa)

Se añade con `/` después de los valores:

```css
.capa {
  background: rgb(0 0 0 / 0.5);          /* negro al 50% */
  background: hsl(220 70% 30% / 40%);    /* también acepta porcentajes */
}
```

`0` es totalmente transparente y `1` (o `100%`) es totalmente opaco.

#### Palabras clave especiales

| Valor | Qué es |
|---|---|
| `transparent` | Totalmente transparente |
| `currentColor` | El valor de `color` del propio elemento |

```css
.icono {
  border: 2px solid currentColor;   /* el borde usa el mismo color que el texto */
}
```

#### Espacios de color modernos

Existen también `hwb()`, `lab()`, `lch()`, `oklch()` y la función `color-mix()`. Son más modernos y permiten colores más vivos y mezclas precisas:

```css
.caja {
  background: oklch(60% 0.2 250);
  border-color: color-mix(in srgb, blue 60%, white);   /* mezcla azul y blanco */
}
```

Se explican con más detalle en [[14 - CSS moderno]].

### 4.2 `color` (texto)

```css
body {
  color: #222;
}

.error {
  color: crimson;
}
```

- **Se hereda**: si lo pones en `body`, afecta a casi todo el texto de la página.

### 4.3 Opacidad

#### `opacity`

Hace transparente **todo el elemento**: fondo, texto, bordes y también sus hijos.

```css
.imagen {
  opacity: 0.7;      /* de 0 (invisible) a 1 (opaco) */
}
```

#### Alfa en el color

Solo hace transparente **ese color**, no el contenido:

```css
.tarjeta {
  background: rgb(0 0 0 / 0.5);   /* el fondo es semitransparente, el texto no */
}
```

> [!warning]
> Si quieres un fondo semitransparente pero el texto totalmente opaco, usa **alfa en el color**, no `opacity`.

### 4.4 `background-color`

```css
.caja {
  background-color: #f4f4f4;
}
```

El valor por defecto es `transparent`.

### 4.5 `background-image`

Pone una imagen (o un degradado) de fondo.

```css
.portada {
  background-image: url("img/fondo.jpg");
}
```

- La imagen se coloca **encima** del `background-color`. Por eso conviene poner también un color de respaldo.
- Para imágenes que son contenido (con significado), usa `<img>` en HTML. El fondo CSS es para **decoración**.

### 4.6 Control de la imagen de fondo

#### `background-repeat`

| Valor | Efecto |
|---|---|
| `repeat` | (por defecto) Se repite en horizontal y vertical |
| `no-repeat` | Se muestra una sola vez |
| `repeat-x` | Solo se repite en horizontal |
| `repeat-y` | Solo se repite en vertical |

#### `background-position`

Dónde se coloca la imagen dentro de la caja.

```css
.portada {
  background-position: center;          /* centrada */
  background-position: top right;       /* arriba a la derecha */
  background-position: 50% 20%;         /* horizontal | vertical */
}
```

#### `background-size`

| Valor | Efecto |
|---|---|
| `auto` | Tamaño original |
| `cover` | La imagen **cubre** toda la caja (puede recortarse) |
| `contain` | La imagen se ve **entera** dentro de la caja (puede dejar huecos) |
| `200px 100px` | Ancho y alto concretos |
| `50%` | Un porcentaje del ancho de la caja |

#### `background-attachment`

| Valor | Efecto |
|---|---|
| `scroll` | (por defecto) El fondo se mueve con el elemento |
| `fixed` | El fondo se queda fijo en la pantalla al hacer scroll (efecto parallax simple) |
| `local` | El fondo se mueve con el contenido del elemento |

#### `background-origin` y `background-clip`

| Propiedad | Qué controla |
|---|---|
| `background-origin` | Desde dónde se **empieza a colocar** la imagen (`padding-box`, `border-box`, `content-box`) |
| `background-clip` | Hasta dónde se **pinta** el fondo (`border-box`, `padding-box`, `content-box`, `text`) |

```css
.titulo {
  background: linear-gradient(90deg, #f06, #4a90e2);
  background-clip: text;              /* el fondo solo se ve dentro de las letras */
  color: transparent;
}
```

> [!note]
> En algunos navegadores, `background-clip: text` aún necesita el prefijo `-webkit-background-clip: text`. Revisa la compatibilidad antes de usarlo.

### 4.7 La abreviatura `background`

Se pueden juntar varias propiedades en una sola:

```css
.portada {
  background: #222 url("img/fondo.jpg") no-repeat center / cover;
}
```

Orden habitual: **color, imagen, repetición, posición / tamaño, attachment**. Fíjate en que la posición y el tamaño van separados por **`/`**.

> [!warning]
> La abreviatura `background` **reinicia** las propiedades que no escribes. Si pones `background: red;`, borra cualquier `background-image` o `background-size` anterior.

### 4.8 Varios fondos a la vez

Se separan con comas. **El primero queda arriba**, el último abajo.

```css
.portada {
  background:
    linear-gradient(rgb(0 0 0 / 0.5), rgb(0 0 0 / 0.5)),   /* capa oscura encima */
    url("img/fondo.jpg") center / cover;                     /* imagen debajo */
}
```

Esto es muy útil para oscurecer una imagen y que el texto se lea mejor.

### 4.9 Degradados

Un degradado es una **imagen creada por CSS** (no necesita archivo). Se usa en `background` o `background-image`.

#### Lineal

```css
.caja {
  background: linear-gradient(to right, #f06, #4a90e2);
  background: linear-gradient(45deg, red, yellow, green);   /* con ángulo y 3 colores */
  background: linear-gradient(#000 0%, #000 40%, #fff 100%); /* con posiciones */
}
```

#### Radial

Parte de un punto central y se expande en círculo o elipse.

```css
.caja {
  background: radial-gradient(circle, white, #333);
  background: radial-gradient(circle at top left, #f06, transparent);
}
```

#### Cónico

Los colores giran alrededor de un centro, como un reloj.

```css
.rueda {
  background: conic-gradient(red, yellow, green, blue, red);
  border-radius: 50%;
}
```

#### Repetidos

`repeating-linear-gradient()`, `repeating-radial-gradient()` y `repeating-conic-gradient()` repiten el patrón (útil para rayas o cuadrículas).

```css
.rayas {
  background: repeating-linear-gradient(45deg, #eee 0 10px, #fff 10px 20px);
}
```

### 4.10 Sombras

#### `box-shadow` (sombra de la caja)

```css
.tarjeta {
  box-shadow: 0 4px 12px rgb(0 0 0 / 0.15);
}
```

Orden de valores: **desplazamiento X, desplazamiento Y, desenfoque, extensión (opcional), color**.

| Valor | Significado |
|---|---|
| `0` | Desplazamiento horizontal (positivo = derecha) |
| `4px` | Desplazamiento vertical (positivo = abajo) |
| `12px` | Desenfoque: cuanto mayor, más suave |
| `rgb(0 0 0 / 0.15)` | Color de la sombra |

- Con la palabra **`inset`** al principio, la sombra va **dentro** de la caja.
- Se pueden poner varias separadas por comas.

```css
.boton {
  box-shadow:
    0 1px 2px rgb(0 0 0 / 0.2),
    0 8px 16px rgb(0 0 0 / 0.1);
}

.campo:focus {
  box-shadow: inset 0 0 0 2px #4a90e2;
}
```

La sombra **no ocupa espacio** ni modifica el tamaño de la caja. Para sombras de texto, mira [[06 - Texto y fuentes]].

### 4.11 Contraste y accesibilidad

Un buen diseño necesita que el texto se **lea bien** sobre su fondo.

- Las pautas de accesibilidad (WCAG) piden una relación de contraste de al menos **4.5:1** para texto normal y **3:1** para texto grande.
- No uses **solo el color** para transmitir información (por ejemplo, "los campos en rojo son errores"). Añade también un texto o un icono.
- Si pones texto sobre una imagen, añade una capa oscura o clara para asegurar la lectura.

Las **DevTools** del navegador muestran el contraste al elegir un color.

---

## 5. Ejemplos prácticos

### Ejemplo básico

```css
body {
  background-color: #f9f9f9;
  color: #222;
}

a {
  color: #1e3a8a;
}
```

### Ejemplo habitual

Portada con imagen de fondo y capa oscura para que el texto se lea:

```css
.portada {
  min-height: 60vh;
  display: grid;
  place-content: center;
  text-align: center;
  color: white;
  background:
    linear-gradient(rgb(0 0 0 / 0.55), rgb(0 0 0 / 0.55)),
    url("img/portada.jpg") center / cover no-repeat;
}
```

### Ejemplo completo

Botón con degradado, sombra y estados, más una tarjeta:

```css
.boton {
  display: inline-block;
  padding: 0.7rem 1.4rem;
  border-radius: 8px;
  color: white;
  text-decoration: none;
  background: linear-gradient(135deg, #f06, #4a90e2);
  box-shadow: 0 4px 12px rgb(240 0 100 / 0.35);
}

.boton:hover {
  background: linear-gradient(135deg, #d4005a, #2f78c4);
  box-shadow: 0 6px 16px rgb(240 0 100 / 0.5);
}

.tarjeta {
  padding: 1.5rem;
  border-radius: 12px;
  background-color: white;
  border: 1px solid hsl(220 15% 90%);
  box-shadow: 0 2px 8px rgb(0 0 0 / 0.08);
}

.tarjeta--oscura {
  background-color: hsl(220 30% 15%);
  color: hsl(220 20% 92%);
}
```

---

## 6. Buenas prácticas

- **Define tu paleta de colores en un solo sitio**, con variables CSS (ver [[13 - Variables y funciones]]).
- **Usa `hsl()` o `oklch()`** cuando necesites crear variantes (más claro, más oscuro) de un mismo color.
- **Pon siempre un `background-color`** como respaldo cuando uses `background-image`, por si la imagen no carga.
- **Usa `background-size: cover`** para fondos que deben llenar la caja.
- **Usa alfa en el color** (`rgb(0 0 0 / 0.5)`) en vez de `opacity` si solo quieres transparentar el fondo.
- **Comprueba el contraste** de texto y fondo.
- **No uses el color como único indicador** de estado o error.
- **Usa `currentColor`** en iconos y bordes para que sigan el color del texto.
- **Las imágenes con significado van en HTML (`<img>`)**, con su `alt`; el fondo CSS es para decoración.
- **Cuida el rendimiento**: evita fondos fijos muy pesados o muchas sombras grandes en cientos de elementos.

---

## 7. Diferencias importantes

| Comparación | Diferencia |
|---|---|
| `color` vs `background-color` | `color` pinta el **texto**; `background-color` pinta el **fondo** de la caja. |
| `opacity` vs alfa en color | `opacity` afecta a **todo el elemento y sus hijos**; el alfa solo al **color** concreto. |
| `background` vs `background-color` | `background` es la abreviatura (reinicia todo); `background-color` cambia solo el color. |
| `cover` vs `contain` | `cover` llena toda la caja (puede recortar); `contain` muestra toda la imagen (puede dejar huecos). |
| `hex` vs `rgb()` vs `hsl()` | Todos describen colores. `hsl()` es el más fácil de ajustar a mano; el hex es el más corto. |
| `box-shadow` vs `border` | La sombra **no ocupa espacio** ni cambia el tamaño; el borde sí. |
| `transparent` vs `currentColor` | `transparent` es sin color; `currentColor` es el color del texto del elemento. |
| `<img>` vs `background-image` | `<img>` es contenido con significado (con `alt`); el fondo CSS es decoración. |

---

## 8. Casos especiales

- **`background` reinicia lo demás**: `background: red;` borra imagen, tamaño y posición definidos antes.
- **Fondo en `body` o `html`**: si solo uno de los dos tiene fondo, se aplica a **todo el lienzo** de la página, no solo a esa caja.
- **`background-attachment: fixed`** puede funcionar mal o desactivarse en algunos móviles (sobre todo iOS).
- **Degradados con `transparent`**: algunos navegadores antiguos mezclaban `transparent` como negro transparente y se veía un tono gris. Hoy se interpola correctamente.
- **`opacity` crea un contexto de apilamiento** (stacking context), lo que puede afectar a `z-index`.
- **`opacity` menor que 1 en un padre** afecta a todos sus hijos y no se puede "deshacer" en uno de ellos.
- **Color de selección**: se puede cambiar con `::selection { background: ...; color: ...; }`.
- **`accent-color`** cambia el color de casillas, radios y barras de progreso del navegador: `input { accent-color: crimson; }`.
- **Imágenes de fondo no se pueden "ver" con lectores de pantalla**: no pongas información importante solo ahí.
- **Modo oscuro**: se puede adaptar con `@media (prefers-color-scheme: dark)` (ver [[10 - Responsive Design]] y [[14 - CSS moderno]]).

---

## 9. Resumen

- **`color`** pinta el texto; **`background`** pinta el fondo; el margen nunca tiene fondo.
- Formatos de color: **nombres**, **hexadecimal**, **`rgb()`**, **`hsl()`** y los modernos (`oklch()`, `color-mix()`).
- La **transparencia** se añade con `/`: `rgb(0 0 0 / 0.5)`.
- **`opacity`** afecta a todo el elemento; el alfa en el color solo al color.
- Las propiedades de la imagen de fondo: **repeat, position, size, attachment, origin, clip**. `cover` llena, `contain` muestra entera.
- La abreviatura **`background`** reinicia lo que no escribes; posición y tamaño se separan con `/`.
- Se pueden poner **varios fondos** separados por comas (el primero queda arriba).
- Los **degradados** (`linear`, `radial`, `conic`) son imágenes creadas con CSS.
- **`box-shadow`** crea sombras sin ocupar espacio; `inset` las pone dentro.
- Buenas prácticas: paleta con variables, color de respaldo, buen contraste y fondos CSS solo para decoración.