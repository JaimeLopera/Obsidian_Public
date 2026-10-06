# HTML - Estructura y semántica

> [!info] ¿Qué es?
> La **semántica** en HTML consiste en elegir cada etiqueta por **lo que significa** el contenido y no por cómo se ve. Las etiquetas semánticas (`<header>`, `<nav>`, `<main>`, `<article>`, `<footer>`...) describen el **papel** de cada zona de la página. Así la entienden mejor los navegadores, los lectores de pantalla, los buscadores y las personas que leen el código.

---

## 1. Antes de empezar

Conviene dominar antes:

- El esqueleto de un documento y la anidación de elementos: [[HTML/01 - Fundamentos]].
- La diferencia entre `<head>` (metadatos) y `<body>` (contenido visible).

> [!tip] Cómo pensar la estructura
> Antes de escribir código, dibuja la página en bloques: cabecera, menú, contenido principal, barra lateral, pie. Cada bloque corresponde a una etiqueta semántica.

---

## 2. Concepto fundamental

Existen dos tipos de elementos para agrupar contenido:

- **Semánticos**: su nombre indica su función (`<nav>` es navegación, `<footer>` es un pie).
- **No semánticos**: no aportan significado (`<div>` para bloques y `<span>` para fragmentos de texto).

Visualmente, un `<div>` y un `<section>` se comportan igual por defecto. La diferencia está en el **significado**, y de él dependen:

- **Accesibilidad**: los lectores de pantalla ofrecen atajos para saltar entre zonas (*landmarks*), como ir directamente al contenido principal.
- **SEO**: los buscadores comprenden mejor qué es contenido principal y qué es secundario.
- **Mantenimiento**: un código con `<nav>` y `<main>` se lee mejor que uno con diez `<div>` anidados.

> [!note] Semántica no es estilo
> Elegir `<h1>` o `<h3>` por su tamaño en pantalla es un error. El tamaño lo decide CSS ([[CSS/06 - Texto y fuentes]]). La etiqueta se elige por la **jerarquía** del contenido.

---

## 3. Sintaxis / estructura

### 3.1 Esqueleto semántico típico de una página

```html
<body>
  <header>
    <h1>Nombre del sitio</h1>
    <nav>...</nav>
  </header>

  <main>
    <article>...</article>
    <aside>...</aside>
  </main>

  <footer>...</footer>
</body>
```

### 3.2 Esquema visual

```
┌──────────────────────────────────┐
│ <header>   (<nav> dentro)        │
├───────────────────────┬──────────┤
│ <main>                │ <aside>  │
│   <article>           │          │
│   <section>           │          │
├───────────────────────┴──────────┤
│ <footer>                         │
└──────────────────────────────────┘
```

### 3.3 Jerarquía de encabezados

Los encabezados `<h1>` a `<h6>` forman un esquema de niveles, como el índice de un libro:

```html
<h1>Título de la página</h1>
  <h2>Sección</h2>
    <h3>Subsección</h3>
  <h2>Otra sección</h2>
```

---

## 4. Elementos / propiedades / características

### 4.1 Elementos de estructura de página

| Elemento | Función | Notas |
|----------|---------|-------|
| `<header>` | Cabecera de la página o de una sección | Suele contener logo, título y navegación |
| `<nav>` | Bloque de enlaces de navegación principales | Solo para navegación importante, no para cualquier grupo de enlaces |
| `<main>` | Contenido principal y único de la página | Solo uno visible por página |
| `<footer>` | Pie de la página o de una sección | Autoría, copyright, enlaces legales, contacto |
| `<aside>` | Contenido relacionado pero secundario | Barras laterales, notas, publicidad, enlaces relacionados |

### 4.2 Elementos de agrupación de contenido

| Elemento | Función | Cuándo usarlo |
|----------|---------|---------------|
| `<article>` | Contenido **independiente** y reutilizable | Entrada de blog, noticia, comentario, tarjeta de producto |
| `<section>` | Agrupación **temática** de contenido | Cuando el bloque tiene un título propio y sentido como sección |
| `<div>` | Contenedor **sin significado** | Solo para agrupar con fines de estilo o scripts |
| `<span>` | Fragmento de texto **sin significado** | Aplicar estilo a una parte del texto |

### 4.3 Elementos de apoyo

| Elemento | Función |
|----------|---------|
| `<figure>` y `<figcaption>` | Contenido autónomo (imagen, diagrama, código) con su pie o leyenda |
| `<address>` | Información de contacto del autor o propietario del documento |
| `<hgroup>` | Agrupa un título con su subtítulo |
| `<search>` | Zona de búsqueda o filtrado de la página |
| `<details>` y `<summary>` | Bloque desplegable de información |
| `<time>` | Fecha u hora legible por máquinas (`datetime="2026-10-02"`) |

### 4.4 Cómo decidir entre `<article>`, `<section>` y `<div>`

1. ¿El contenido tendría sentido **por sí solo**, fuera de la página (como una noticia en un lector RSS)? → `<article>`
2. ¿Es un bloque **temático** con su propio encabezado dentro de un contenido mayor? → `<section>`
3. ¿Solo necesitas un contenedor para **estilo o scripts**? → `<div>`

### 4.5 Reglas de los encabezados

- Cada página debe tener un `<h1>` que describa su contenido principal.
- No se saltan niveles (de `<h2>` a `<h4>` sin `<h3>`).
- Un encabezado introduce el contenido que le sigue; no se usa para dar tamaño a un texto.

---

## 5. Ejemplos prácticos

### Ejemplo básico

Estructura mínima con las zonas principales:

```html
<body>
  <header>
    <h1>Mi blog</h1>
  </header>

  <main>
    <p>Contenido principal de la página.</p>
  </main>

  <footer>
    <p>&copy; 2026 Mi blog</p>
  </footer>
</body>
```

### Ejemplo habitual

Página de blog con navegación, artículo y barra lateral:

```html
<body>
  <header>
    <h1>Mi blog de desarrollo web</h1>
    <nav aria-label="Principal">
      <ul>
        <li><a href="index.html">Inicio</a></li>
        <li><a href="articulos.html">Artículos</a></li>
        <li><a href="contacto.html">Contacto</a></li>
      </ul>
    </nav>
  </header>

  <main>
    <article>
      <header>
        <h2>Qué es la semántica en HTML</h2>
        <p>Publicado el <time datetime="2026-10-02">2 de octubre de 2026</time></p>
      </header>
      <p>Texto del artículo...</p>
      <section>
        <h3>Ventajas</h3>
        <p>Mejor accesibilidad y SEO.</p>
      </section>
    </article>

    <aside>
      <h2>Artículos relacionados</h2>
      <ul>
        <li><a href="#">Introducción a CSS</a></li>
      </ul>
    </aside>
  </main>

  <footer>
    <p>&copy; 2026 Mi blog</p>
  </footer>
</body>
```

Las listas de enlaces se explican en [[HTML/05 - Listas y tablas]] y los enlaces en [[HTML/03 - Texto y enlaces]].

### Ejemplo completo

Página con dos navegaciones diferenciadas, `figure`, `address` y un bloque desplegable:

```html
<body>
  <header>
    <h1>Academia Web</h1>
    <nav aria-label="Principal">
      <ul>
        <li><a href="/">Inicio</a></li>
        <li><a href="/cursos">Cursos</a></li>
      </ul>
    </nav>
  </header>

  <main>
    <h2>Curso de HTML</h2>

    <section>
      <h3>Temario</h3>
      <figure>
        <img src="img/temario.png" alt="Esquema del temario del curso">
        <figcaption>Esquema general del curso</figcaption>
      </figure>
      <details>
        <summary>¿Necesito conocimientos previos?</summary>
        <p>No, el curso parte desde cero.</p>
      </details>
    </section>

    <section>
      <h3>Contacto</h3>
      <address>
        Escríbenos a <a href="mailto:info@academiaweb.example">info@academiaweb.example</a>
      </address>
    </section>
  </main>

  <footer>
    <nav aria-label="Legal">
      <ul>
        <li><a href="/privacidad">Privacidad</a></li>
        <li><a href="/cookies">Cookies</a></li>
      </ul>
    </nav>
    <p>&copy; 2026 Academia Web</p>
  </footer>
</body>
```

Cuando hay más de un `<nav>`, se diferencian con `aria-label` ([[HTML/08 - Accesibilidad]]). Las imágenes y `figure` se tratan en [[HTML/04 - Imágenes y multimedia]].

---


## 6. Buenas prácticas

- Elegir la etiqueta por su **significado**, no por su aspecto.
- Usar un único `<main>` visible y un único `<h1>` por página.
- Mantener la **jerarquía de encabezados** sin saltos de nivel.
- Reservar `<div>` y `<span>` para cuando no exista una etiqueta semántica adecuada.
- Dar un **encabezado** a cada `<section>` y a cada `<article>`.
- Diferenciar varias navegaciones con `aria-label`.
- Usar `<figure>` y `<figcaption>` para imágenes o ejemplos con leyenda.
- Usar `<time datetime="...">` para fechas y horas.
- Pensar la estructura **antes** de escribir estilos: una buena base semántica simplifica el CSS ([[CSS/07 - Flexbox]], [[CSS/08 - Grid]]).

Más criterios de calidad en [[HTML/11 - Buenas prácticas]].

---

## 7. Diferencias importantes

| Comparación | Diferencia |
|-------------|------------|
| **`<div>` vs `<section>`** | `<div>` no tiene significado; `<section>` agrupa contenido temático y debe llevar encabezado |
| **`<section>` vs `<article>`** | `<article>` es autónomo y reutilizable; `<section>` es una parte temática de un conjunto mayor |
| **`<header>` vs `<head>`** | `<header>` es una cabecera **visible**; `<head>` contiene metadatos **no visibles** |
| **`<aside>` vs `<main>`** | `<main>` es el contenido central; `<aside>` es contenido relacionado y secundario |
| **`<div>` vs `<span>`** | `<div>` es un contenedor de **bloque**; `<span>` es de **línea**, dentro del texto |
| **`<footer>` de página vs de sección** | Puede haber un `<footer>` general y otros dentro de `<article>` o `<section>` |

---

## 8. Casos especiales

### `<header>` y `<footer>` dentro de otros elementos

No son exclusivos de la página: también pueden usarse dentro de un `<article>` o `<section>` (por ejemplo, para el título y la fecha de un artículo, o su autoría). Los lectores de pantalla solo los tratan como zonas globales (*banner* y *contentinfo*) cuando son hijos directos de `<body>`.

### Varios `<nav>` y `<aside>`

Se pueden usar varios si cada uno cumple su función, diferenciándolos con `aria-label` o con un encabezado.

### `<section>` sin encabezado

Un `<section>` sin encabezado no se muestra como zona identificable en la navegación asistida. Si no hay encabezado posible, probablemente deba ser un `<div>`.

### Roles ARIA redundantes

Un `<nav>` ya equivale a `role="navigation"`. Añadir el rol a una etiqueta semántica es innecesario. Los roles ARIA se reservan para cuando no hay etiqueta nativa ([[HTML/08 - Accesibilidad]]).

> [!warning] Obsoleto / legado
> - **Maquetar con tablas** (`<table>` para colocar la cabecera, el menú y el contenido) está obsoleto. Se sustituye por etiquetas semánticas más **CSS** ([[CSS/07 - Flexbox]], [[CSS/08 - Grid]]).
> - El patrón `<div id="header">`, `<div id="nav">`, `<div id="footer">` se sustituye por `<header>`, `<nav>` y `<footer>`.
> - El **algoritmo de esquema del documento** (*outline algorithm*), que permitía varios `<h1>` anidados en `<section>` con niveles automáticos, nunca fue implementado por los navegadores y se eliminó del estándar. Hay que usar los niveles `h1` a `h6` explícitamente.

---


## 9. Resumen

- La **semántica** elige cada etiqueta por su **significado**, no por su aspecto.
- Estructura de página: `<header>`, `<nav>`, `<main>`, `<aside>` y `<footer>`.
- Agrupación de contenido: `<article>` (autónomo), `<section>` (temático, con encabezado) y `<div>` (sin significado, solo para estilo o scripts).
- Un solo `<main>` visible y un solo `<h1>` por página; la jerarquía `h1` a `h6` no se salta niveles.
- `<figure>`, `<address>`, `<time>` y `<details>` completan el contenido con significado propio.
- La semántica mejora la **accesibilidad**, el **SEO** y el **mantenimiento** del código.
- Evitar la "sopa de divs", maquetar con tablas y elegir encabezados por su tamaño visual.