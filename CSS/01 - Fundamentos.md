# 01 - Fundamentos

> [!info] ¿Qué es?
> **CSS** (*Cascading Style Sheets*, hojas de estilo en cascada) es el lenguaje que define **cómo se ve** una página HTML: colores, tamaños, letras, espacios y posiciones. HTML dice **qué hay** en la página; CSS dice **cómo se muestra**.

---

## 1. Antes de empezar

- CSS **no sirve por sí solo**: siempre se aplica sobre un documento HTML. Conviene saber lo básico de HTML: [[01 - Fundamentos]] y [[02 - Estructura y semántica]] de la carpeta HTML.
- CSS **no es un lenguaje de programación**: no tiene `if` ni bucles. Es un lenguaje de **reglas de estilo** (aunque hoy incluye variables y funciones, ver [[13 - Variables y funciones]]).
- Los ficheros de CSS terminan en **`.css`**.
- Para probar lo que vas aprendiendo, tienes dos opciones muy cómodas:
  - Un fichero `index.html` con un `estilos.css` en tu editor (por ejemplo VS Code).
  - Las **herramientas del navegador** (tecla **F12**), donde puedes cambiar estilos en directo.

**Analogía:** si una casa fuera una página web, HTML sería la estructura (paredes, puertas, ventanas) y CSS sería la decoración (pintura, muebles, iluminación).

---

## 2. Concepto fundamental

### La idea básica

Una hoja de estilos es una **lista de reglas**. Cada regla responde a tres preguntas:

1. **¿A qué elementos?** → el *selector*.
2. **¿Qué quiero cambiar?** → la *propiedad*.
3. **¿Cómo lo quiero?** → el *valor*.

```css
p {
  color: blue;
}
```

Esto significa: "a todos los párrafos (`p`), ponles el color (`color`) azul (`blue`)".

### Qué ocurre cuando el navegador carga la página

1. Lee el **HTML** y construye el árbol de elementos (el DOM).
2. Lee el **CSS** y construye otro árbol con las reglas.
3. **Combina** los dos: para cada elemento calcula qué estilos le tocan.
4. **Pinta** la página.

Por eso, si cambias un estilo, la página se actualiza al momento.

### Los tres conceptos clave de CSS

Casi todo lo que pasa en CSS se explica con estas tres ideas. Se desarrollan en la sección 4:

| Concepto | Pregunta que responde |
|---|---|
| **Cascada** | Si dos reglas chocan, ¿cuál gana? |
| **Herencia** | ¿Qué estilos pasan de padres a hijos? |
| **Especificidad** | ¿Qué selector pesa más? |

---

## 3. Sintaxis / estructura

### 3.1 Anatomía de una regla

```css
selector {
  propiedad: valor;
  otra-propiedad: otro-valor;
}
```

```css
h1 {
  color: tomato;
  font-size: 2rem;
}
```

| Parte | Qué es | En el ejemplo |
|---|---|---|
| **Selector** | A quién se aplica | `h1` |
| **Llaves `{ }`** | Delimitan el bloque de declaraciones | `{ ... }` |
| **Declaración** | Una pareja propiedad + valor | `color: tomato;` |
| **Propiedad** | Qué aspecto se cambia | `color` |
| **Valor** | Cómo se cambia | `tomato` |
| **Punto y coma `;`** | Termina cada declaración | `;` |

> [!tip] Importante
> Cada declaración termina en `;`. Si olvidas uno, la declaración siguiente puede **dejar de funcionar** sin avisar.

### 3.2 Varios selectores a la vez

Si varios elementos comparten estilo, sepáralos con comas:

```css
h1, h2, h3 {
  font-family: Arial, sans-serif;
}
```

### 3.3 Comentarios

```css
/* Esto es un comentario: el navegador lo ignora */
p {
  color: gray; /* también se puede comentar al final de una línea */
}
```

> [!warning] Solo existe un tipo de comentario
> En CSS **no** funcionan `//` ni `<!-- -->`. Solo `/* ... */`.

### 3.4 Formas de añadir CSS a una página

**1. Fichero externo (la forma recomendada)**

```html
<head>
  <link rel="stylesheet" href="estilos.css">
</head>
```

**2. Etiqueta `<style>` dentro del HTML**

```html
<head>
  <style>
    h1 { color: tomato; }
  </style>
</head>
```

**3. Estilo en línea (*inline*)**

```html
<h1 style="color: tomato;">Hola</h1>
```

| Forma | Ventajas | Inconvenientes | ¿Cuándo? |
|---|---|---|---|
| **Externo** | Un solo fichero para muchas páginas, se guarda en caché, código ordenado | Una petición extra | **Casi siempre** |
| **`<style>`** | Rápido para probar | Solo vale para esa página | Pruebas, páginas únicas, CSS crítico |
| **En línea** | Muy fácil de aplicar | Difícil de mantener, pesa mucho en la cascada | Casos puntuales (valores dinámicos desde JavaScript) |

### 3.5 `@import` (forma antigua)

```css
@import url("otro.css");
```

> [!warning] Obsoleto / legado
> `@import` dentro del CSS **retrasa la carga**, porque el navegador tiene que descargar un fichero para descubrir el siguiente. **Alternativa actual:** varias etiquetas `<link>` en el HTML, o un empaquetador (Vite, etc.).

### 3.6 Valores comunes de una declaración

| Tipo de valor | Ejemplos |
|---|---|
| **Palabra clave** | `red`, `bold`, `center`, `none`, `block` |
| **Número con unidad** | `16px`, `2rem`, `50%`, `100vh` |
| **Color** | `#ff6347`, `rgb(255, 99, 71)`, `hsl(9, 100%, 64%)` |
| **Texto entre comillas** | `"Roboto"`, `"\201C"` |
| **URL** | `url("imagen.png")` |
| **Función** | `calc(100% - 20px)`, `var(--color)` |

Más detalle en [[04 - Unidades y valores]] y [[05 - Colores y fondos]].

### 3.7 Propiedades abreviadas (*shorthand*)

Algunas propiedades permiten escribir varios valores en una sola línea:

```css
/* Versión larga */
margin-top: 10px;
margin-right: 20px;
margin-bottom: 10px;
margin-left: 20px;

/* Versión abreviada: arriba/abajo 10px, derecha/izquierda 20px */
margin: 10px 20px;
```

Se explican con detalle en [[03 - Box Model]].

---

## 4. Elementos / propiedades / características

### 4.1 Los selectores más básicos

Hay mucho más en [[02 - Selectores]], pero para empezar basta con estos:

| Selector | Qué elige | Ejemplo |
|---|---|---|
| **Etiqueta** | Todos los elementos de ese tipo | `p { ... }` |
| **Clase** | Elementos con ese `class` | `.destacado { ... }` |
| **Id** | El elemento con ese `id` (único) | `#cabecera { ... }` |
| **Universal** | Todos los elementos | `* { ... }` |

```html
<p class="destacado">Texto importante</p>
<header id="cabecera">...</header>
```

```css
p { color: black; }
.destacado { color: red; }
#cabecera { background: navy; }
```

> [!tip] ¿Clase o id?
> Usa **clases** casi siempre. Los ids son únicos en la página y tienen demasiado peso en la cascada, así que complican los estilos.

### 4.2 La cascada

Se llama "cascada" porque el navegador va aplicando reglas **en orden**, y cuando dos reglas chocan, decide cuál gana siguiendo **tres criterios, en este orden**:

1. **Importancia** (`!important` y origen del estilo).
2. **Especificidad** (qué selector es más concreto).
3. **Orden** (si todo lo demás empata, gana la regla que va **después**).

#### Orden: gana la última

```css
p { color: red; }
p { color: blue; }   /* gana: está después */
```

El párrafo se verá **azul**.

#### Origen de los estilos (de menos a más prioridad, simplificado)

1. Estilos por defecto del **navegador** (por eso un `<h1>` ya es grande y en negrita).
2. Estilos del **usuario** (accesibilidad, modo oscuro forzado…).
3. Estilos del **autor** (los tuyos).

### 4.3 La especificidad

Cuando dos reglas apuntan al mismo elemento, gana la que tiene el **selector más específico**, **da igual el orden**.

**Cómo se calcula (de más a menos peso):**

| Nivel | Qué cuenta | Ejemplo |
|---|---|---|
| 1. En línea | Atributo `style="..."` | `<p style="...">` |
| 2. Ids | Cada `#id` | `#menu` |
| 3. Clases, atributos y pseudoclases | `.clase`, `[type="text"]`, `:hover` | `.boton` |
| 4. Etiquetas y pseudoelementos | `p`, `h1`, `::before` | `div` |

Se suele escribir como tres números **(ids, clases, etiquetas)**:

| Selector | Especificidad |
|---|---|
| `p` | (0, 0, 1) |
| `.destacado` | (0, 1, 0) |
| `p.destacado` | (0, 1, 1) |
| `#cabecera` | (1, 0, 0) |
| `#cabecera .logo` | (1, 1, 0) |
| `nav ul li a` | (0, 0, 4) |

Se comparan **de izquierda a derecha**: un solo id (1,0,0) vence a mil clases (0,1000,0).

```css
p { color: red; }              /* (0,0,1) */
.destacado { color: blue; }    /* (0,1,0) → gana */
```

```html
<p class="destacado">¿De qué color soy?</p>  <!-- azul -->
```

> [!tip] Cosas que no suman especificidad
> El selector universal `*` y los combinadores (`>`, `+`, `~`, espacio) **no suman**. Tampoco `:where()`, que vale 0 a propósito. `:is()` y `:not()` toman la especificidad de lo que llevan dentro.

### 4.4 `!important`

Añade `!important` a una declaración para que **pase por encima** de las demás:

```css
p {
  color: red !important;
}
```

> [!warning] Úsalo lo mínimo posible
> `!important` rompe el orden natural de la cascada y la única forma de vencerlo es **otro** `!important` con más especificidad. Se acaba llenando el código de ellos. Primero intenta resolver el problema con mejores selectores o ajustando el orden. Reserva `!important` para casos como clases de utilidad (`.oculto { display: none !important; }`) o para sobrescribir CSS de terceros.

### 4.5 La herencia

Algunas propiedades **pasan automáticamente** del elemento padre a sus hijos.

```html
<div class="tarjeta">
  <p>Este párrafo hereda el color</p>
</div>
```

```css
.tarjeta {
  color: green;
  border: 1px solid black;
}
```

- El `<p>` se verá en **verde** (el `color` se hereda).
- El `<p>` **no** tendrá borde (el `border` no se hereda).

**Propiedades que se heredan normalmente** (casi todo lo relacionado con el texto):

`color`, `font-family`, `font-size`, `font-weight`, `font-style`, `line-height`, `text-align`, `letter-spacing`, `visibility`, `cursor`, `list-style`.

**Propiedades que NO se heredan** (casi todo lo relacionado con la caja y el diseño):

`margin`, `padding`, `border`, `background`, `width`, `height`, `display`, `position`, `overflow`.

> [!tip] Truco
> Gracias a la herencia, puedes poner la fuente y el color de texto **una sola vez** en `body` y toda la página los usará.
> ```css
> body {
>   font-family: Arial, sans-serif;
>   color: #333;
> }
> ```

#### Palabras clave para controlar la herencia

| Valor | Qué hace |
|---|---|
| `inherit` | **Fuerza** a heredar el valor del padre |
| `initial` | Pone el valor **por defecto de CSS** de la propiedad |
| `unset` | Si la propiedad se hereda → `inherit`; si no → `initial` |
| `revert` | Vuelve al valor que tendría **el navegador** |

```css
a {
  color: inherit;  /* el enlace usa el color del texto que lo rodea */
}

button {
  all: unset;      /* quita casi todos los estilos */
}
```

### 4.6 Valores por defecto del navegador

Aunque no escribas CSS, el navegador ya aplica uno básico (su **hoja de estilos por defecto**): por eso un `<h1>` sale grande, un `<a>` azul y subrayado, y un `<ul>` con viñetas y sangría.

Los navegadores no coinciden al 100 % en esos valores, así que muchos proyectos empiezan con un **reset** o **normalize** para partir de una base común.

```css
/* Reset mínimo muy usado */
*,
*::before,
*::after {
  box-sizing: border-box;
}

body {
  margin: 0;
}
```

### 4.7 `display`: bloque vs en línea (vistazo rápido)

Cada elemento HTML se comporta como **bloque** o **en línea**. Esto afecta a cómo se colocan:

| Tipo | Se comporta así | Ejemplos |
|---|---|---|
| **Bloque** (`block`) | Ocupa **todo el ancho** y empieza en una línea nueva | `div`, `p`, `h1`, `ul`, `section` |
| **En línea** (`inline`) | Ocupa **solo lo que mide** su contenido y sigue en la misma línea | `span`, `a`, `strong`, `em` |
| **En línea-bloque** (`inline-block`) | En la misma línea, pero acepta ancho y alto | `img`, `button` |

```css
span { display: block; }   /* ahora el span se comporta como un bloque */
```

Se desarrolla en [[03 - Box Model]] y en [[07 - Flexbox]].

### 4.8 Herramientas del navegador

Con **F12** (o clic derecho → *Inspeccionar*) puedes:

- Ver qué reglas afectan a cada elemento.
- Ver las reglas **tachadas** (perdieron en la cascada) y **por qué**.
- **Editar** estilos en directo para probar.
- Ver la caja del elemento (margen, relleno, borde).
- Ver el valor **final calculado** de cada propiedad.

> [!tip] El mejor aliado
> Cuando un estilo "no funciona", inspecciona el elemento: casi siempre verás la regla tachada y sabrás qué otra regla ganó.

---

## 5. Ejemplos prácticos

### 5.1 Ejemplo básico: tu primera hoja de estilos

**index.html**

```html
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Mi primera página</title>
  <link rel="stylesheet" href="estilos.css">
</head>
<body>
  <h1>Hola, CSS</h1>
  <p>Este es mi primer párrafo con estilo.</p>
</body>
</html>
```

**estilos.css**

```css
body {
  font-family: Arial, sans-serif;
  background-color: #f4f4f4;
  color: #333;
}

h1 {
  color: tomato;
}
```

**Explicación:**
- `<link>` conecta el CSS con el HTML. La ruta `href` debe ser correcta.
- `body` define fuente, fondo y color de texto para toda la página.
- `h1` sobreescribe solo el color del título (gana a `body` porque es más específico y el color heredado pierde ante una regla directa).

### 5.2 Ejemplo básico: clases reutilizables

```html
<p>Párrafo normal</p>
<p class="aviso">Párrafo de aviso</p>
<p class="aviso peligro">Párrafo de peligro</p>
```

```css
.aviso {
  padding: 10px;
  background: #fff3cd;
}

.peligro {
  background: #f8d7da;
  color: #721c24;
}
```

**Explicación:** un elemento puede tener **varias clases** separadas por espacios. El tercero recibe los estilos de `.aviso` y `.peligro`; como `.peligro` va después, su `background` gana.

### 5.3 Ejemplo habitual: la cascada en acción

```html
<p id="mensaje" class="texto">¿De qué color soy?</p>
```

```css
p { color: red; }              /* (0,0,1) */
.texto { color: blue; }        /* (0,1,0) */
#mensaje { color: green; }     /* (1,0,0) → gana */
```

**Resultado:** verde. El id tiene más especificidad que la clase y la etiqueta, sin importar el orden en que estén escritas.

### 5.4 Ejemplo habitual: orden cuando hay empate

```css
.boton { background: gray; }
.boton { background: black; }   /* gana: misma especificidad, va después */
```

### 5.5 Ejemplo habitual: herencia práctica

```html
<article>
  <h2>Título</h2>
  <p>Texto del artículo.</p>
  <a href="#">Leer más</a>
</article>
```

```css
article {
  font-family: Georgia, serif;
  color: #444;
}

a {
  color: inherit;          /* usa el color del artículo en vez del azul por defecto */
  text-decoration: underline;
}
```

### 5.6 Ejemplo habitual: depurar una regla que no funciona

```css
.menu a { color: white; }
a { color: blue; }
```

**Problema:** "mi enlace del menú no se ve blanco".

**Diagnóstico:** `.menu a` es (0,1,1) y `a` es (0,0,1), así que `.menu a` debería ganar. Si no lo hace, hay que comprobar con F12 si:
- ¿El enlace está **realmente** dentro de un elemento con clase `menu`?
- ¿La clase está bien escrita (`menu` y no `Menu`)?
- ¿Hay otra regla con más peso o un `!important`?
- ¿Se está cargando el fichero CSS?

### 5.7 Ejemplo completo: una tarjeta sencilla

```html
<div class="tarjeta">
  <h2 class="tarjeta__titulo">Aprende CSS</h2>
  <p class="tarjeta__texto">CSS da estilo a tus páginas web.</p>
  <a class="tarjeta__enlace" href="#">Empezar</a>
</div>
```

```css
/* Base de la página */
body {
  font-family: Arial, sans-serif;
  background: #eef1f5;
  color: #333;
  margin: 0;
  padding: 20px;
}

/* La tarjeta */
.tarjeta {
  background: white;
  border: 1px solid #ddd;
  border-radius: 8px;
  padding: 20px;
  max-width: 320px;
}

.tarjeta__titulo {
  margin-top: 0;
  color: #1a73e8;
}

.tarjeta__enlace {
  display: inline-block;
  padding: 8px 16px;
  background: #1a73e8;
  color: white;
  text-decoration: none;
  border-radius: 4px;
}
```

**Explicación:**
- El `body` fija la tipografía y el fondo para toda la página (herencia).
- Cada parte de la tarjeta tiene su **clase propia**, así los estilos son fáciles de localizar y de reutilizar.
- El enlace se convierte en `inline-block` para poder darle `padding` y que parezca un botón.

---

## 6. Buenas prácticas

- **Usa un fichero CSS externo** y conéctalo con `<link>` en el `<head>`.
- **Usa clases** como selector principal; evita los ids y los estilos en línea.
- **Pon los estilos generales en `body`** (fuente, color) para aprovechar la herencia.
- **No uses `!important`** salvo en casos justificados.
- **Mantén selectores cortos y de poca especificidad** (como `.tarjeta__titulo` en vez de `body div.contenedor article h2`). Así son más fáciles de sobreescribir.
- **Escribe de lo general a lo específico**: primero reset y estilos base, luego componentes, al final casos concretos.
- **Un nombre claro y consistente** para las clases (por ejemplo, con la metodología BEM: ver [[15 - Buenas prácticas]]).
- **Comenta las secciones** del fichero para encontrarlas rápido.
- **Usa la misma sangría y formato** en todo el fichero (2 espacios por nivel, una declaración por línea).
- **Usa el inspector del navegador (F12)** siempre que algo no se vea como esperas.
- **Escribe el CSS pensando en reutilizar**: si copias y pegas el mismo bloque varias veces, crea una clase común.
- **Añade siempre el meta `viewport`** en el HTML para que la web se vea bien en el móvil: [[10 - Responsive Design]].

---

## 7. Diferencias importantes

### HTML vs CSS

| | HTML | CSS |
|---|---|---|
| Función | **Estructura** y contenido | **Aspecto** |
| Ejemplo | "Esto es un título" | "El título es rojo y grande" |
| Extensión | `.html` | `.css` |

### Propiedad vs valor vs declaración

| Término | Ejemplo |
|---|---|
| Propiedad | `color` |
| Valor | `red` |
| Declaración | `color: red;` |
| Regla | `p { color: red; }` |

### Clase vs id

| | Clase (`.nombre`) | Id (`#nombre`) |
|---|---|---|
| ¿Cuántas veces en la página? | Las que quieras | **Solo una** |
| Especificidad | Media | **Alta** |
| Uso en CSS | **Recomendado** | Evitar |
| Uso en JavaScript / enlaces internos | Posible | Muy habitual |

### Herencia vs cascada

| | Herencia | Cascada |
|---|---|---|
| Qué resuelve | Qué estilos pasan del padre al hijo | Qué regla gana cuando dos chocan |
| Ejemplo | El `color` del `body` llega a los `<p>` | `#id` gana a `.clase` |

### Estilo externo vs `<style>` vs en línea

| | Externo | `<style>` | En línea |
|---|---|---|---|
| Reutilizable en varias páginas | ✅ | ❌ | ❌ |
| Especificidad | Normal | Normal | **Muy alta** |
| Mantenimiento | Fácil | Medio | Difícil |

### `inherit` vs `initial` vs `unset` vs `revert`

| Valor | Resultado |
|---|---|
| `inherit` | Toma el valor del padre |
| `initial` | Valor por defecto **de CSS** |
| `unset` | `inherit` si se hereda, `initial` si no |
| `revert` | Valor por defecto **del navegador** |

---

## 8. Casos especiales

### 8.1 Una propiedad no existe o el valor es incorrecto

Si escribes algo que el navegador no entiende, **ignora esa declaración** y sigue. No muestra error.

```css
p {
  colour: red;       /* propiedad mal escrita → ignorada */
  color: rojoo;      /* valor no válido → ignorada */
  font-size: 18px;   /* esta sí funciona */
}
```

> [!tip] Comprobación rápida
> Si un estilo no se aplica, mira el inspector: las declaraciones inválidas aparecen **tachadas con un aviso**.

### 8.2 Declaraciones repetidas: respaldo (*fallback*)

Como el navegador ignora lo que no entiende, puedes escribir un valor seguro primero y uno moderno después:

```css
.caja {
  width: 90%;                 /* lo entienden todos */
  width: min(90%, 1000px);    /* si el navegador lo entiende, sobreescribe */
}
```

### 8.3 Unidades sin espacio

El número y la unidad van **pegados**, y el `0` no necesita unidad:

```css
margin: 10px;    /* ✅ */
margin: 10 px;   /* ❌ no funciona */
margin: 0;       /* ✅ el 0 no necesita unidad */
```

### 8.4 Sensibilidad a mayúsculas

- Los **nombres de propiedades y valores** no distinguen mayúsculas, pero por convención se escriben en minúsculas.
- Los **nombres de clases e ids sí distinguen** mayúsculas: `.Menu` y `.menu` son distintos.

### 8.5 Espacios en blanco y saltos de línea

El navegador **ignora** los espacios y saltos de línea extra. Estas dos reglas son idénticas:

```css
p{color:red;font-size:16px}

p {
  color: red;
  font-size: 16px;
}
```

La segunda es mucho más legible, y la primera es lo que hace un *minificador* para reducir el peso.

### 8.6 Estilos de varias fuentes en conflicto

Cuando usas librerías externas (Bootstrap, etc.), sus estilos se mezclan con los tuyos. Para que los tuyos ganen:
- Carga tu CSS **después** del de la librería.
- Usa selectores con un poco más de especificidad.
- Como última opción, `!important`.

### 8.7 Capas de cascada (`@layer`)

CSS moderno permite **ordenar la prioridad por capas**, de forma que una capa posterior gana a otra anterior **independientemente de la especificidad**. Es la solución actual a los conflictos entre librerías. Se explica en [[14 - CSS moderno]].

```css
@layer base, componentes, utilidades;
```

### 8.8 Prefijos de navegador (legado)

Antes, algunas propiedades nuevas necesitaban prefijos (`-webkit-`, `-moz-`) para funcionar:

```css
-webkit-transition: all 0.3s;
transition: all 0.3s;
```

> [!warning] Obsoleto / legado
> Hoy casi ninguna propiedad necesita prefijo. Si lo requieres, no lo escribas a mano: usa una herramienta como **Autoprefixer**, que los añade solo cuando hacen falta.

### 8.9 Elementos y atributos de estilo antiguos en HTML

Hay etiquetas y atributos HTML antiguos que daban estilo y ya no se deben usar.

> [!warning] Obsoleto / legado
> `<font>`, `<center>`, `<b>` como estilo, `bgcolor`, `align`, `border` en tablas… **Alternativa actual:** CSS (`color`, `text-align`, `background`, `border`…).

---

## 9. Resumen

- **CSS** da estilo a HTML. HTML es la estructura; CSS, el aspecto.
- Una **regla** tiene un **selector** y un bloque `{ }` con **declaraciones** (`propiedad: valor;`).
- Se añade a la página preferiblemente con un **fichero externo** y `<link rel="stylesheet">` en el `<head>`. También existen `<style>` y el estilo en línea.
- Solo hay un tipo de comentario: `/* ... */`.
- Selectores básicos: **etiqueta**, **clase** (`.`), **id** (`#`) y **universal** (`*`). Usa sobre todo clases.
- La **cascada** decide qué regla gana: primero **importancia**, luego **especificidad**, y por último el **orden** (gana la última).
- **Especificidad**, de más a menos: estilo en línea > id > clase/atributo/pseudoclase > etiqueta.
- `!important` fuerza una regla, pero debe usarse **lo mínimo posible**.
- La **herencia** hace que propiedades de texto (`color`, `font-family`, `line-height`…) pasen del padre al hijo; las de caja (`margin`, `padding`, `border`, `background`…) **no**.
- `inherit`, `initial`, `unset` y `revert` controlan explícitamente la herencia.
- Los elementos son de tipo **bloque** (ocupan toda la línea) o **en línea** (ocupan lo justo).
- Si algo falla, abre el inspector con **F12**: te muestra qué reglas se aplican, cuáles están tachadas y por qué.
- Un navegador ignora sin avisar lo que no entiende, así que revisa **ortografía de propiedades y valores**.