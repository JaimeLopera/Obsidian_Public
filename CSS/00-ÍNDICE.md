# 00 - Índice de CSS

> [!info] ¿Qué es CSS?
> **CSS** (*Cascading Style Sheets*, hojas de estilo en cascada) es el lenguaje que controla **cómo se ve** una página web: colores, tamaños, tipos de letra, posiciones, animaciones y adaptación a cada pantalla. Si HTML es el **esqueleto** de la página, CSS es la **ropa y la decoración**.

---

## 1. Cómo usar esta carpeta

- Las notas están **numeradas en el orden en que conviene estudiarlas**. Cada una se apoya en las anteriores.
- Si estás repasando algo concreto, ve directo a su nota. Si lo has olvidado del todo, empieza por la nota y lee también la anterior.
- La última nota ([[16 - Referencia rápida]]) es una chuleta para consultar sin leer todo.
- Antes de empezar con CSS conviene tener claras las bases de HTML: [[00 - Índice]] de HTML, sobre todo [[01 - Fundamentos]] y [[02 - Estructura y semántica]].

---

## 2. Qué se aprende en cada nota

### Bloque 1: Las bases

| Nota                        | De qué trata                                                                                                |
| --------------------------- | ----------------------------------------------------------------------------------------------------------- |
| [[01 - Fundamentos]]        | Qué es CSS, cómo se conecta con HTML, cómo se escribe una regla, la cascada, la herencia y la especificidad |
| [[02 - Selectores]]         | Cómo elegir a qué elementos se aplica un estilo: por etiqueta, clase, id, atributo y combinadores           |
| [[03 - Box Model]]          | El modelo de caja: contenido, `padding`, `border` y `margin`; `box-sizing` y colapso de márgenes            |
| [[04 - Unidades y valores]] | `px`, `%`, `em`, `rem`, `vw`, `vh`, `fr` y cuándo usar cada una                                             |

### Bloque 2: Aspecto visual

| Nota | De qué trata |
|---|---|
| [[05 - Colores y fondos]] | Formatos de color (`hex`, `rgb`, `hsl`), transparencia, degradados, imágenes de fondo, sombras |
| [[06 - Texto y fuentes]] | Tipografía: `font-family`, tamaños, pesos, interlineado, alineación, Google Fonts y fuentes propias |

### Bloque 3: Maquetación (layout)

| Nota | De qué trata |
|---|---|
| [[07 - Flexbox]] | Organizar elementos en **una dimensión** (fila o columna), alinear y repartir espacio |
| [[08 - Grid]] | Organizar elementos en **dos dimensiones** (filas y columnas), áreas y diseños complejos |
| [[09 - Position]] | `static`, `relative`, `absolute`, `fixed`, `sticky`, `z-index` y capas |
| [[10 - Responsive Design]] | Adaptar la web a móvil, tablet y escritorio: `media queries`, `mobile first`, imágenes adaptables |

### Bloque 4: Interactividad y detalle

| Nota | De qué trata |
|---|---|
| [[11 - Transiciones y animaciones]] | `transition`, `transform`, `@keyframes` y animaciones suaves |
| [[12 - Pseudoclases y pseudoelementos]] | `:hover`, `:focus`, `:nth-child()`, `::before`, `::after` y similares |

### Bloque 5: CSS avanzado y profesional

| Nota | De qué trata |
|---|---|
| [[13 - Variables y funciones]] | Variables CSS (`--color`), `var()`, `calc()`, `clamp()`, `min()`, `max()` |
| [[14 - CSS moderno]] | Novedades actuales: `:has()`, `@container`, `@layer`, anidamiento nativo, `aspect-ratio`, `gap`… |
| [[15 - Buenas prácticas]] | Organización, nombres de clases (BEM), reset, accesibilidad, rendimiento y mantenimiento |
| [[16 - Referencia rápida]] | Chuleta con las propiedades y valores más usados |

---

## 3. Ruta de estudio recomendada

```
Fundamentos → Selectores → Box Model → Unidades
        ↓
Colores y fondos → Texto y fuentes
        ↓
Flexbox → Grid → Position
        ↓
Responsive Design
        ↓
Transiciones y animaciones → Pseudoclases y pseudoelementos
        ↓
Variables y funciones → CSS moderno → Buenas prácticas
```

> [!tip] Si tienes poco tiempo
> Para poder maquetar una web real, las notas **imprescindibles** son: [[02 - Selectores]], [[03 - Box Model]], [[07 - Flexbox]], [[08 - Grid]] y [[10 - Responsive Design]].

---

## 4. Preguntas rápidas: ¿dónde miro?

| Quiero saber... | Ve a |
|---|---|
| Por qué mi estilo no se aplica | [[01 - Fundamentos]] (cascada y especificidad) y [[02 - Selectores]] |
| Cómo separar o ajustar el espacio entre elementos | [[03 - Box Model]] |
| Cuándo usar `rem`, `%` o `vw` | [[04 - Unidades y valores]] |
| Cómo poner un degradado o una sombra | [[05 - Colores y fondos]] |
| Cómo cargar una fuente de Google Fonts | [[06 - Texto y fuentes]] |
| Cómo centrar algo | [[07 - Flexbox]] y [[08 - Grid]] |
| Cómo hacer un menú fijo arriba | [[09 - Position]] |
| Cómo hacer que la web se vea bien en el móvil | [[10 - Responsive Design]] |
| Cómo animar un botón al pasar el ratón | [[11 - Transiciones y animaciones]] y [[12 - Pseudoclases y pseudoelementos]] |
| Cómo reutilizar colores y tamaños | [[13 - Variables y funciones]] |
| Qué opciones nuevas tiene CSS hoy | [[14 - CSS moderno]] |
| Cómo organizar mi CSS | [[15 - Buenas prácticas]] |
| Una propiedad concreta, rápido | [[16 - Referencia rápida]] |

---

## 5. Conceptos clave de toda la carpeta

Estas son las ideas que se repiten una y otra vez en CSS. Si las tienes claras, el resto es más fácil:

- **Cascada**: cuando varias reglas afectan al mismo elemento, gana una según su **origen**, su **especificidad** y su **orden**.
- **Herencia**: algunas propiedades (como `color` o `font-family`) pasan de padre a hijo automáticamente.
- **Todo es una caja**: cada elemento HTML es un rectángulo con contenido, relleno, borde y margen.
- **Flujo normal**: los elementos se colocan solos, uno tras otro, hasta que tú cambias su posición o su tipo de `display`.
- **Layout moderno**: se hace con **Flexbox** y **Grid**, ya no con `float` ni tablas.
- **Mobile first**: se diseña primero para pantallas pequeñas y se amplía para las grandes.
- **Variables y unidades relativas**: hacen que el diseño sea flexible y fácil de cambiar.

---

## 6. Cómo se conecta CSS con HTML (vistazo rápido)

Hay tres formas de añadir CSS a una página. Se explican a fondo en [[01 - Fundamentos]].

```html
<!-- 1. Fichero externo (la forma recomendada) -->
<link rel="stylesheet" href="estilos.css">

<!-- 2. Dentro del propio HTML -->
<style>
  h1 { color: tomato; }
</style>

<!-- 3. En línea, sobre un elemento (evitar salvo casos concretos) -->
<h1 style="color: tomato;">Hola</h1>
```

Y así se ve una regla CSS:

```css
selector {
  propiedad: valor;
}

/* Ejemplo */
h1 {
  color: tomato;
  font-size: 2rem;
}
```

---

## 7. Resumen

- **CSS** da estilo a las páginas HTML: colores, tipografía, tamaños, posición y animaciones.
- Esta carpeta tiene **16 notas** más este índice, ordenadas para estudiarse de principio a fin.
- Orden general: **bases** (fundamentos, selectores, box model, unidades) → **aspecto** (colores y texto) → **maquetación** (Flexbox, Grid, Position, Responsive) → **detalle** (animaciones y pseudoclases) → **avanzado** (variables, CSS moderno, buenas prácticas).
- Para maquetar una web real lo imprescindible es: **selectores, box model, Flexbox, Grid y responsive**.
- Cuando necesites un dato concreto y rápido, usa [[16 - Referencia rápida]].