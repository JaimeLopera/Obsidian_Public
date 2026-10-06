# Box Model

> [!info] ¿Qué es?
> El **Box Model** (modelo de caja) es la regla con la que el navegador calcula el **tamaño y el espacio** de cada elemento. Según este modelo, **todo elemento HTML es una caja rectangular** formada por cuatro capas: contenido, relleno (*padding*), borde (*border*) y margen (*margin*). Entenderlo es la base para controlar tamaños, separaciones y alineaciones.

---

## 1. Antes de empezar

Para entender esta nota conviene saber:

- Cómo seleccionar elementos con CSS (ver [[02 - Selectores]]).
- Que existen elementos **de bloque** (`<div>`, `<p>`, `<h1>`, `<ul>`…, ocupan todo el ancho y empiezan en una línea nueva) y elementos **en línea** (`<span>`, `<a>`, `<em>`…, ocupan solo lo que miden y siguen en la misma línea).
- Las unidades básicas de CSS: `px`, `%`, `rem`… (se ven a fondo en [[04 - Unidades y valores]]).

HTML que usaremos en los ejemplos:

```html
<div class="caja">
  Hola, soy una caja.
</div>

<article class="tarjeta">
  <h2>Título</h2>
  <p>Texto de la tarjeta.</p>
</article>
```

---

## 2. Concepto fundamental

Para el navegador, cada elemento es una **caja**. Esa caja tiene cuatro capas, de dentro hacia fuera:

| Capa | Qué es | Propiedad |
|---|---|---|
| **Content** (contenido) | Donde van el texto, las imágenes, etc. | `width`, `height` |
| **Padding** (relleno) | Espacio **dentro** de la caja, entre el contenido y el borde | `padding` |
| **Border** (borde) | La línea que rodea el relleno y el contenido | `border` |
| **Margin** (margen) | Espacio **fuera** de la caja, que la separa de las demás | `margin` |

Dibujo para tenerlo claro:

```text
┌───────────────────────────────────────┐
│               MARGIN                  │   ← espacio exterior (transparente)
│   ┌───────────────────────────────┐   │
│   │            BORDER             │   │   ← la línea del borde
│   │   ┌───────────────────────┐   │   │
│   │   │       PADDING         │   │   │   ← espacio interior
│   │   │   ┌───────────────┐   │   │   │
│   │   │   │    CONTENT    │   │   │   │   ← texto, imágenes...
│   │   │   └───────────────┘   │   │   │
│   │   └───────────────────────┘   │   │
│   └───────────────────────────────┘   │
└───────────────────────────────────────┘
```

> [!tip] Cómo recordarlo
> Piensa en un **marco con una foto**: la foto es el *contenido*, el cartón blanco que rodea la foto es el *padding*, el marco es el *border* y el espacio hasta la pared o hasta el cuadro de al lado es el *margin*.

**Qué se ve y qué no:**

- El **fondo** (`background`) se pinta debajo del contenido y del padding (y llega hasta el borde).
- El **margen** es siempre **transparente**: no se le puede poner color.

---

## 3. Sintaxis / estructura

```css
.caja {
  width: 300px;                 /* ancho del contenido */
  height: 150px;                /* alto del contenido */
  padding: 20px;                /* espacio interior */
  border: 2px solid black;      /* borde: grosor, estilo, color */
  margin: 30px;                 /* espacio exterior */
}
```

Con esa regla, la caja ocupa en pantalla (usando el modelo por defecto, que veremos en el apartado 4.5):

- **Ancho total** = 300 (contenido) + 20 + 20 (padding) + 2 + 2 (borde) = **344 px**.
- **Alto total** = 150 + 20 + 20 + 2 + 2 = **194 px**.
- A eso hay que sumarle el margen (30 por cada lado) para saber el espacio que ocupa **contando la separación**: 404 px de ancho.

---

## 4. Elementos / propiedades / características

### 4.1 Contenido: `width` y `height`

Definen el tamaño de la zona de contenido.

```css
.caja {
  width: 300px;
  height: 150px;
}
```

Propiedades relacionadas, para poner límites:

| Propiedad | Qué hace |
|---|---|
| `min-width` | Ancho **mínimo**: no se hará más estrecha que esto |
| `max-width` | Ancho **máximo**: no se hará más ancha que esto |
| `min-height` | Alto mínimo |
| `max-height` | Alto máximo |

```css
.contenedor {
  width: 100%;
  max-width: 1000px;   /* ocupa todo el ancho, pero nunca pasa de 1000px */
}
```

**Valores habituales:**

- `auto`: el navegador decide (en un bloque, el ancho será todo el disponible y el alto lo que mida el contenido). Es el valor por defecto.
- Una medida fija: `300px`, `20rem`.
- Un porcentaje: `50%` (relativo al tamaño del padre).

> [!note]
> En la mayoría de los casos **es mejor no fijar el alto** (`height`) y dejar que crezca según el contenido. Si fijas un alto y el contenido es más largo, se saldrá de la caja (ver 4.8).

### 4.2 Relleno: `padding`

Es el espacio **dentro** de la caja, entre el contenido y el borde.

```css
.caja {
  padding: 20px;                 /* los 4 lados */
}
```

**Un lado concreto:**

```css
.caja {
  padding-top: 10px;
  padding-right: 20px;
  padding-bottom: 10px;
  padding-left: 20px;
}
```

**Forma abreviada** (el orden va en el sentido de las agujas del reloj: arriba, derecha, abajo, izquierda):

| Valores | Significado |
|---|---|
| `padding: 10px;` | Los 4 lados = 10px |
| `padding: 10px 20px;` | Arriba y abajo = 10px, derecha e izquierda = 20px |
| `padding: 10px 20px 30px;` | Arriba = 10px, derecha e izquierda = 20px, abajo = 30px |
| `padding: 10px 20px 30px 40px;` | Arriba, derecha, abajo, izquierda |

**Características del padding:**

- **No admite valores negativos.**
- Hereda el **color de fondo** de la caja (el fondo también se ve en el relleno).
- Aumenta el tamaño total de la caja (salvo con `border-box`, ver 4.5).

### 4.3 Borde: `border`

Es la línea que rodea el padding y el contenido. Para que se vea necesita al menos **estilo** (`solid`, `dashed`…). Sin estilo, el borde no aparece aunque tenga grosor y color.

```css
.caja {
  border: 2px solid black;   /* grosor | estilo | color */
}
```

Se puede escribir por partes:

```css
.caja {
  border-width: 2px;
  border-style: solid;
  border-color: black;
}
```

**Estilos de borde más usados:**

| Valor | Aspecto |
|---|---|
| `solid` | Línea continua |
| `dashed` | Línea de guiones |
| `dotted` | Línea de puntos |
| `double` | Doble línea |
| `none` | Sin borde |

**Bordes por lado:**

```css
.caja {
  border-bottom: 3px solid crimson;   /* solo el borde de abajo */
  border-left: 5px solid navy;        /* solo el de la izquierda */
}
```

**Esquinas redondeadas** con `border-radius`:

```css
.tarjeta {
  border-radius: 8px;      /* esquinas suavemente redondeadas */
}

.avatar {
  width: 80px;
  height: 80px;
  border-radius: 50%;      /* un cuadrado se convierte en círculo */
}
```

> [!note] `border` vs `outline`
> El `outline` (contorno) se parece a un borde, pero **no ocupa espacio** en la caja y no afecta al diseño. Se usa mucho para mostrar el foco (`:focus`) de un elemento. No lo quites sin poner otra señal visible: es importante para la accesibilidad.

### 4.4 Margen: `margin`

Es el espacio **fuera** de la caja, que la separa de las cajas vecinas. Se escribe igual que el padding:

```css
.caja {
  margin: 30px;                  /* los 4 lados */
  margin: 10px 20px;             /* vertical | horizontal */
  margin: 10px 20px 30px 40px;   /* arriba | derecha | abajo | izquierda */
  margin-top: 10px;              /* solo un lado */
}
```

**Características del margin:**

- Es **transparente** (no tiene color).
- **Admite valores negativos**: con `margin-top: -10px` la caja se acerca o se solapa con la de arriba.
- Puede valer **`auto`**.

**Centrar una caja horizontalmente con `margin: auto`:**

```css
.contenedor {
  width: 600px;           /* hace falta un ancho definido */
  margin: 0 auto;         /* 0 arriba y abajo, auto a izquierda y derecha */
}
```

El navegador reparte el espacio sobrante a partes iguales a izquierda y derecha, y la caja queda centrada.

> [!warning]
> `margin: auto` solo centra **horizontalmente** en un bloque normal, y solo si la caja tiene un ancho menor que el de su padre. Para centrar en vertical hay mejores herramientas (ver [[07 - Flexbox]] y [[08 - Grid]]).

### 4.5 `box-sizing`: cómo se calcula el tamaño

Esta propiedad es **muy importante**. Define **qué cuentan** `width` y `height`.

#### `content-box` (valor por defecto)

`width` y `height` miden **solo el contenido**. El padding y el borde se **suman** por fuera.

```css
.caja {
  box-sizing: content-box;
  width: 300px;
  padding: 20px;
  border: 5px solid black;
}
/* Ancho real = 300 + 20 + 20 + 5 + 5 = 350px */
```

#### `border-box`

`width` y `height` incluyen **el contenido, el padding y el borde**. El tamaño que escribes es el tamaño total que ves.

```css
.caja {
  box-sizing: border-box;
  width: 300px;
  padding: 20px;
  border: 5px solid black;
}
/* Ancho real = 300px (el contenido se reduce a 250px) */
```

| | `content-box` | `border-box` |
|---|---|---|
| `width` mide... | Solo el contenido | Contenido + padding + borde |
| Ancho real con `width: 300px`, `padding: 20px`, `border: 5px` | 350px | 300px |
| ¿Es fácil de calcular? | No: hay que sumar | Sí: lo que escribes es lo que ves |

**Es casi universal poner `border-box` en todos los elementos** con este "reset":

```css
*,
*::before,
*::after {
  box-sizing: border-box;
}
```

> [!tip]
> Pon esto al principio de todos tus CSS. Con `content-box`, una caja de `width: 100%` con padding se sale de su contenedor; con `border-box`, no.

### 4.6 `display` y el Box Model

El valor de `display` cambia **cómo se comporta la caja**:

| `display` | Ocupa | `width` / `height` | `margin` y `padding` vertical |
|---|---|---|---|
| `block` | Todo el ancho, línea nueva | Sí se aplican | Sí se aplican |
| `inline` | Solo lo que mide su contenido, sin saltar de línea | **Se ignoran** | Se aplican, pero **no empujan** a las líneas vecinas |
| `inline-block` | Solo lo que mide, sin saltar de línea | Sí se aplican | Sí se aplican |
| `none` | **No se muestra** y no ocupa espacio | — | — |

```css
span {
  display: inline-block;   /* ahora acepta width, height y margen vertical */
  width: 100px;
  padding: 10px;
}
```

### 4.7 Colapso de márgenes (*margin collapsing*)

Cuando el margen **vertical** (arriba/abajo) de dos cajas se toca, **no se suman**: se fusiona en uno solo, del tamaño del **mayor**.

```css
.a { margin-bottom: 30px; }
.b { margin-top: 20px; }
/* Espacio entre .a y .b = 30px (no 50px) */
```

Esto ocurre en tres situaciones:

1. **Hermanos seguidos**: el margen inferior de uno y el superior del siguiente.
2. **Padre e hijo**: el margen superior del primer hijo "sale" por arriba del padre si el padre no tiene `padding`, `border` ni nada entre medias.
3. **Cajas vacías**: sus márgenes superior e inferior se fusionan entre sí.

Reglas que conviene recordar:

- **Solo colapsan los márgenes verticales.** Los horizontales siempre se suman.
- Si los dos son negativos, gana el más negativo; si uno es positivo y otro negativo, se suman.
- **No colapsan** dentro de contenedores `flex` o `grid`, ni con `display: inline-block`, ni cuando hay `padding`, `border` o `overflow` distinto de `visible` entre ellos.

### 4.8 Desbordamiento: `overflow`

Si el contenido no cabe en la caja (por ejemplo, tiene un `height` fijo y el texto es largo), se **desborda**. Con `overflow` decides qué pasa:

| Valor | Qué hace |
|---|---|
| `visible` | (por defecto) El contenido se sale de la caja y se ve |
| `hidden` | Lo que sobra se **recorta** y no se ve |
| `scroll` | Siempre muestra barras de desplazamiento |
| `auto` | Muestra barras **solo si hacen falta** |

```css
.caja {
  height: 100px;
  overflow: auto;     /* aparece scroll si el contenido no cabe */
}
```

Existen también `overflow-x` y `overflow-y` para controlar cada eje por separado.

### 4.9 Propiedades lógicas (versión moderna)

Además de `top/right/bottom/left`, CSS tiene propiedades **lógicas**, que se adaptan al idioma y a la dirección de escritura (útiles en webs multilingües):

| Propiedad lógica | Equivale (en español/inglés) a |
|---|---|
| `margin-inline` | `margin-left` + `margin-right` |
| `margin-block` | `margin-top` + `margin-bottom` |
| `padding-inline` | `padding-left` + `padding-right` |
| `padding-block` | `padding-top` + `padding-bottom` |
| `inline-size` | `width` |
| `block-size` | `height` |

```css
.caja {
  padding-block: 10px;     /* arriba y abajo */
  padding-inline: 20px;    /* izquierda y derecha */
  margin-inline: auto;     /* centrar horizontalmente */
}
```

### 4.10 Cómo ver el Box Model: las DevTools

En el navegador, haz clic derecho sobre un elemento → **Inspeccionar**. En la pestaña de estilos (*Computed* / *Calculado*) aparece un **diagrama de colores** con las cuatro capas y sus medidas exactas.

- Azul: contenido.
- Verde: padding.
- Amarillo/naranja: borde.
- Naranja/marrón: margen.

Es la mejor herramienta para averiguar por qué una caja ocupa más (o menos) de lo esperado.

---

## 5. Ejemplos prácticos

### Ejemplo básico

Una caja con espacio interior, borde y separación:

```css
.caja {
  width: 300px;
  padding: 20px;
  border: 2px solid #333;
  margin: 20px;
  background: #f4f4f4;
}
```

### Ejemplo habitual

Un contenedor centrado y con `border-box`, como el que se usa en casi cualquier página:

```css
*,
*::before,
*::after {
  box-sizing: border-box;
}

.contenedor {
  width: 100%;
  max-width: 1000px;
  margin: 0 auto;
  padding: 0 1rem;
}
```

- `max-width` evita que sea demasiado ancho en pantallas grandes.
- `margin: 0 auto` lo centra.
- `padding` lateral evita que el contenido toque los bordes en móvil.

### Ejemplo completo

Una tarjeta con todas las capas bien usadas, y botones separados con márgenes:

```html
<article class="tarjeta">
  <h2>Título de la tarjeta</h2>
  <p>Un texto breve que describe la tarjeta.</p>
  <a href="#" class="boton">Leer más</a>
</article>
```

```css
*,
*::before,
*::after {
  box-sizing: border-box;
}

.tarjeta {
  max-width: 360px;
  margin: 2rem auto;              /* centrada, con espacio arriba y abajo */
  padding: 1.5rem;                /* espacio interior */
  border: 1px solid #ddd;
  border-radius: 12px;            /* esquinas redondeadas */
  background: white;
}

.tarjeta h2 {
  margin: 0 0 0.5rem;             /* quitamos el margen de arriba, dejamos 0.5rem abajo */
}

.tarjeta p {
  margin: 0 0 1rem;
  color: #555;
}

.boton {
  display: inline-block;          /* para que acepte padding y márgenes verticales */
  padding: 0.6rem 1.2rem;
  border-radius: 6px;
  background: crimson;
  color: white;
  text-decoration: none;
}
```

---

## 6. Buenas prácticas

- **Usa `box-sizing: border-box` en todo** con el reset universal. Hace los cálculos mucho más fáciles.
- **Usa `max-width` en lugar de `width` fijo** para que las cajas se adapten a pantallas pequeñas.
- **No fijes `height` si no es necesario.** Deja que la caja crezca con su contenido.
- **Usa `padding` para el espacio interior y `margin` para separar cajas entre sí.** Cada uno tiene su papel.
- **Haz la separación en una sola dirección**: por ejemplo, usa solo `margin-bottom` entre elementos, así evitas confusión con el colapso de márgenes.
- **Usa unidades relativas** (`rem`, `em`, `%`) para el padding y los márgenes, de modo que el diseño escale mejor.
- **Para separar elementos dentro de Flexbox o Grid**, usa `gap` en lugar de márgenes (ver [[07 - Flexbox]] y [[08 - Grid]]).
- **Inspecciona con las DevTools** cuando algo no mida lo que esperas.
- **Si quitas el `outline` de un elemento**, ofrece otra señal visible para el foco.

---

## 7. Diferencias importantes

| Comparación | Diferencia |
|---|---|
| `padding` vs `margin` | `padding` es espacio **dentro** de la caja (tiene el fondo). `margin` es espacio **fuera** (transparente). |
| `border` vs `outline` | `border` ocupa espacio en la caja. `outline` se dibuja por fuera y **no** ocupa espacio. |
| `content-box` vs `border-box` | En `content-box`, `width` mide solo el contenido. En `border-box`, incluye padding y borde. |
| `width: 100%` vs `width: auto` | `100%` fuerza el ancho del padre (y con `content-box` + padding se sale). `auto` ocupa el espacio disponible **respetando** padding, borde y márgenes. |
| `display: inline` vs `inline-block` | `inline` ignora `width`, `height` y márgenes verticales. `inline-block` los acepta, sin saltar de línea. |
| `display: none` vs `visibility: hidden` | `none` quita la caja y **no ocupa espacio**. `hidden` la oculta pero **sigue ocupando** su sitio. |
| Margen vertical vs horizontal | Los verticales pueden **colapsar**; los horizontales siempre se **suman**. |
| `overflow: hidden` vs `auto` | `hidden` recorta lo que sobra. `auto` añade scroll si hace falta. |

---

## 8. Casos especiales

- **Porcentajes en `padding` y `margin`**: siempre se calculan según el **ancho** del contenedor, incluso en `padding-top` o `margin-bottom`. Por eso un `padding-top: 50%` depende del ancho, no del alto.
- **`box-sizing` no se hereda**: por eso se aplica con `*` (todos los elementos) y no solo al `body`.
- **`margin: auto` vertical** no centra en un bloque normal: el navegador lo trata como `0`. Dentro de Flexbox o Grid sí funciona distinto.
- **Elementos `inline` y márgenes verticales**: `margin-top` y `margin-bottom` no tienen efecto en un `<span>` o `<a>` en línea. Si lo necesitas, cámbialo a `inline-block` o `block`.
- **Elementos reemplazados** como `<img>`, `<video>` o `<canvas>` tienen su propio tamaño natural. Para adaptarlos suele bastar con `max-width: 100%; height: auto;`.
- **Márgenes negativos**: permiten solapar cajas o "tirar" de ellas hacia arriba o la izquierda. Úsalos con moderación, porque pueden hacer el diseño difícil de entender.
- **Altura de un padre con hijos flotantes (`float`)**: el padre puede "colapsar" a altura 0. Se resuelve con `display: flow-root` en el padre.
- **Tablas**: `<table>` tiene reglas propias para bordes y espacios (`border-collapse`, `border-spacing`), y no se comporta del todo como una caja normal.
- **`min-width` / `max-width` ganan a `width`**: si un límite choca con el ancho, manda el límite.

> [!warning] Obsoleto / legado
> Las versiones con prefijo `-webkit-box-sizing` y `-moz-box-sizing` eran necesarias en navegadores muy antiguos. Hoy **ya no hacen falta**: basta con `box-sizing`. Igualmente, el viejo modo "quirks" de Internet Explorer, que calculaba el tamaño como `border-box` por defecto, ya no es relevante.

---

## 9. Resumen

- Todo elemento es una **caja** con cuatro capas: **content → padding → border → margin**.
- `width` y `height` definen el **contenido**; `padding` es espacio **interior**; `border` es la **línea**; `margin` es espacio **exterior** y transparente.
- La **forma abreviada** de `padding` y `margin` sigue el orden: arriba, derecha, abajo, izquierda.
- `margin: 0 auto` + un ancho definido **centra** una caja horizontalmente.
- `box-sizing: border-box` hace que `width` y `height` incluyan padding y borde: es la opción recomendada, con el reset `*, *::before, *::after`.
- `display` cambia el comportamiento: `block` ocupa toda la línea, `inline` ignora `width`/`height`, `inline-block` mezcla ambos, `none` elimina la caja.
- Los **márgenes verticales colapsan** (se fusionan en el mayor); los horizontales no.
- `overflow` controla qué pasa cuando el contenido no cabe: `visible`, `hidden`, `scroll` o `auto`.
- Las **DevTools** del navegador muestran el diagrama del Box Model de cualquier elemento.
- Buena práctica: `border-box`, `max-width`, no fijar el `height` y usar `gap` en Flexbox y Grid.