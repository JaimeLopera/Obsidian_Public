# HTML - Listas y tablas

> [!info] ¿Qué es?
> Las **listas** agrupan elementos relacionados (pasos, opciones, términos con su definición) y las **tablas** presentan **datos organizados en filas y columnas**. Ambas aportan estructura y significado al contenido: un lector de pantalla anuncia "lista de 5 elementos" o "tabla de 3 columnas", y el navegador y los buscadores entienden cómo se relacionan los datos.

---

## 1. Antes de empezar

Conviene dominar antes:

- La sintaxis de etiquetas, atributos y anidación: [[HTML/01 - Fundamentos]].
- Las zonas semánticas de la página, ya que las listas forman la base de los menús dentro de `<nav>`: [[HTML/02 - Estructura y semántica]].

> [!tip] Cómo elegir
> ¿El orden importa? → lista ordenada. ¿Solo agrupas elementos sin orden? → lista no ordenada. ¿Son términos con su descripción? → lista de descripción. ¿Son **datos con dos dimensiones** (por ejemplo, producto y precio)? → tabla.

---

## 2. Concepto fundamental

**Listas.** Una lista es un contenedor (`<ul>`, `<ol>` o `<dl>`) que agrupa elementos hijos. En `<ul>` y `<ol>`, cada elemento es un `<li>`. Los puntos, números o sangrías que se ven son solo el estilo por defecto: el significado está en la **estructura**, y el aspecto se cambia con CSS.

**Tablas.** Una tabla es una cuadrícula de **filas** (`<tr>`) formadas por **celdas**. Hay dos tipos de celda:

- `<th>`: celda de **encabezado** (describe una fila o una columna).
- `<td>`: celda de **datos**.

Distinguirlas es lo que permite a las tecnologías de apoyo asociar cada dato con su encabezado.

> [!note] Tablas para datos, no para maquetar
> Una tabla sirve para **mostrar datos tabulares**, no para colocar elementos en la página. La disposición visual de una página se hace con CSS ([[CSS/07 - Flexbox]], [[CSS/08 - Grid]]).

---

## 3. Sintaxis / estructura

### 3.1 Lista no ordenada

```html
<ul>
  <li>Manzanas</li>
  <li>Peras</li>
  <li>Naranjas</li>
</ul>
```

### 3.2 Lista ordenada

```html
<ol>
  <li>Abrir el editor</li>
  <li>Crear el archivo <code>index.html</code></li>
  <li>Abrirlo en el navegador</li>
</ol>
```

### 3.3 Lista de descripción

```html
<dl>
  <dt>HTML</dt>
  <dd>Lenguaje de marcado para estructurar contenido.</dd>

  <dt>CSS</dt>
  <dd>Lenguaje de estilos para dar aspecto al contenido.</dd>
</dl>
```

### 3.4 Listas anidadas

La lista hija va **dentro de un `<li>`**, no directamente dentro de `<ul>` u `<ol>`:

```html
<ul>
  <li>Frontend
    <ul>
      <li>HTML</li>
      <li>CSS</li>
    </ul>
  </li>
  <li>Backend</li>
</ul>
```

### 3.5 Tabla básica

```html
<table>
  <tr>
    <th>Producto</th>
    <th>Precio</th>
  </tr>
  <tr>
    <td>Cuaderno</td>
    <td>3,50 €</td>
  </tr>
</table>
```

### 3.6 Tabla con estructura completa

```html
<table>
  <caption>Ventas por trimestre (2026)</caption>
  <thead>
    <tr>
      <th scope="col">Trimestre</th>
      <th scope="col">Ventas</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th scope="row">T1</th>
      <td>12.000 €</td>
    </tr>
    <tr>
      <th scope="row">T2</th>
      <td>15.500 €</td>
    </tr>
  </tbody>
  <tfoot>
    <tr>
      <th scope="row">Total</th>
      <td>27.500 €</td>
    </tr>
  </tfoot>
</table>
```

---

## 4. Elementos / propiedades / características

### 4.1 Elementos de lista

| Elemento | Función |
|----------|---------|
| `<ul>` | Lista **no ordenada** (el orden no importa) |
| `<ol>` | Lista **ordenada** (el orden importa) |
| `<li>` | Elemento de una lista `<ul>` u `<ol>` |
| `<dl>` | Lista de **descripción** (términos y definiciones) |
| `<dt>` | Término dentro de `<dl>` |
| `<dd>` | Descripción o definición del término |
| `<menu>` | Equivalente semántico de `<ul>` para barras de herramientas o acciones |

### 4.2 Atributos de listas

| Atributo | Elemento | Función | Ejemplo |
|----------|----------|---------|---------|
| `start` | `<ol>` | Número inicial de la lista | `<ol start="5">` |
| `reversed` | `<ol>` | Numera en orden descendente | `<ol reversed>` |
| `type` | `<ol>` | Tipo de numeración: `1`, `a`, `A`, `i`, `I` | `<ol type="a">` |
| `value` | `<li>` | Número de ese elemento en una `<ol>` | `<li value="10">` |

### 4.3 Elementos de tabla

| Elemento | Función |
|----------|---------|
| `<table>` | Contenedor de la tabla |
| `<caption>` | Título de la tabla (primer hijo de `<table>`) |
| `<thead>` | Grupo de filas de **encabezado** |
| `<tbody>` | Grupo de filas del **cuerpo** (puede haber varios) |
| `<tfoot>` | Grupo de filas del **pie** (totales, resúmenes) |
| `<tr>` | Fila |
| `<th>` | Celda de encabezado |
| `<td>` | Celda de datos |
| `<colgroup>` y `<col>` | Agrupan columnas para aplicarles estilo o ancho |

### 4.4 Atributos de celdas

| Atributo | Elemento | Función | Ejemplo |
|----------|----------|---------|---------|
| `colspan` | `<td>`, `<th>` | La celda ocupa varias **columnas** | `<td colspan="2">` |
| `rowspan` | `<td>`, `<th>` | La celda ocupa varias **filas** | `<td rowspan="3">` |
| `scope` | `<th>` | Indica a qué afecta el encabezado: `col`, `row`, `colgroup`, `rowgroup` | `<th scope="col">` |
| `headers` | `<td>`, `<th>` | Lista de `id` de los encabezados asociados (tablas complejas) | `<td headers="t1 ventas">` |
| `abbr` | `<th>` | Versión abreviada del encabezado para lectores de pantalla | `<th abbr="Trim.">` |

### 4.5 Contenido permitido

- Dentro de `<ul>` y `<ol>` solo se permiten **hijos `<li>`** (y elementos de script o plantilla).
- Dentro de `<li>` puede haber casi cualquier contenido: texto, enlaces, imágenes, otras listas.
- Dentro de `<dl>` van `<dt>` y `<dd>`, y se pueden agrupar con un `<div>` por cada par término-descripción.
- Dentro de `<table>` el orden es: `<caption>`, `<colgroup>`, `<thead>`, `<tbody>`, `<tfoot>`. Las filas no deben ir sueltas si se usan secciones.

### 4.6 Aspecto por defecto

| Elemento | Aspecto del navegador |
|----------|----------------------|
| `<ul>` | Viñetas (puntos) con sangría |
| `<ol>` | Números con sangría |
| `<dd>` | Sangría a la izquierda |
| `<th>` | Negrita y texto centrado |
| `<table>` | Sin bordes: se añaden con CSS |

---

## 5. Ejemplos prácticos

### Ejemplo básico

Una lista de la compra y una tabla mínima:

```html
<h2>Lista de la compra</h2>
<ul>
  <li>Pan</li>
  <li>Leche</li>
  <li>Huevos</li>
</ul>

<table>
  <tr>
    <th>Lenguaje</th>
    <th>Uso</th>
  </tr>
  <tr>
    <td>HTML</td>
    <td>Estructura</td>
  </tr>
  <tr>
    <td>CSS</td>
    <td>Estilo</td>
  </tr>
</table>
```

### Ejemplo habitual

Menú de navegación con lista, pasos ordenados y glosario:

```html
<header>
  <nav aria-label="Principal">
    <ul>
      <li><a href="index.html">Inicio</a></li>
      <li><a href="cursos.html">Cursos</a></li>
      <li><a href="contacto.html">Contacto</a></li>
    </ul>
  </nav>
</header>

<main>
  <h1>Cómo crear una página web</h1>

  <ol>
    <li>Crear la carpeta del proyecto.</li>
    <li>Escribir el archivo <code>index.html</code>.</li>
    <li>Añadir una hoja de estilos.</li>
    <li>Probar la página en el navegador.</li>
  </ol>

  <h2>Glosario</h2>
  <dl>
    <dt>Etiqueta</dt>
    <dd>Marca escrita entre <code>&lt;</code> y <code>&gt;</code>.</dd>
    <dt>Atributo</dt>
    <dd>Información adicional dentro de la etiqueta de apertura.</dd>
  </dl>
</main>
```

Los enlaces del menú se explican en [[HTML/03 - Texto y enlaces]].

### Ejemplo completo

Tabla accesible con título, secciones, celdas combinadas y una lista anidada:

```html
<main>
  <h1>Horario del curso</h1>

  <table>
    <caption>Horario semanal de DAW</caption>
    <colgroup>
      <col>
      <col span="2">
    </colgroup>
    <thead>
      <tr>
        <th scope="col">Hora</th>
        <th scope="col">Lunes</th>
        <th scope="col">Martes</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <th scope="row">8:00 - 10:00</th>
        <td>HTML</td>
        <td>Python</td>
      </tr>
      <tr>
        <th scope="row">10:00 - 12:00</th>
        <td colspan="2">Proyecto en grupo</td>
      </tr>
    </tbody>
    <tfoot>
      <tr>
        <td colspan="3">Horario sujeto a cambios.</td>
      </tr>
    </tfoot>
  </table>

  <h2>Materiales por asignatura</h2>
  <ul>
    <li>HTML
      <ol>
        <li>Apuntes de la unidad 1</li>
        <li>Ejercicios prácticos</li>
      </ol>
    </li>
    <li>Python
      <ul>
        <li>Libro de referencia</li>
        <li>Repositorio de ejemplos</li>
      </ul>
    </li>
  </ul>
</main>
```

---

## 6. Buenas prácticas

- Elegir el tipo de lista por su **significado**: ordenada si el orden importa, no ordenada si no, y de descripción para términos y definiciones.
- Usar listas para **menús y agrupaciones de enlaces**: es el patrón estándar dentro de `<nav>`.
- Anidar las listas **dentro de un `<li>`**, nunca como hijas directas de `<ul>` u `<ol>`.
- Cambiar viñetas, números y sangrías con **CSS** (`list-style`, [[CSS/03 - Box Model]]), no con HTML.
- Usar tablas solo para **datos tabulares**; nunca para maquetar.
- Incluir siempre un `<caption>` o un encabezado cercano que explique de qué trata la tabla.
- Usar `<th>` con `scope="col"` o `scope="row"` para los encabezados, y `<td>` para los datos.
- Estructurar con `<thead>`, `<tbody>` y `<tfoot>` en tablas medianas o grandes.
- Mantener las tablas **simples**: evitar `colspan` y `rowspan` salvo que sean necesarios, porque dificultan la lectura con lector de pantalla.
- Dar estilo con CSS: bordes (`border`, `border-collapse`), espaciado (`padding`) y filas alternas.
- Envolver las tablas anchas en un contenedor con `overflow-x: auto` para que se desplacen en móvil sin romper la página ([[CSS/10 - Responsive Design]]).
- No dejar celdas vacías sin motivo: si no hay dato, escribir un valor como "-" o "No disponible".

---

## 7. Diferencias importantes

| Comparación | Diferencia |
|-------------|------------|
| **`<ul>` vs `<ol>`** | `<ul>` no tiene orden (viñetas); `<ol>` sí lo tiene (números o letras) |
| **`<ul>` vs `<dl>`** | `<ul>` agrupa elementos sueltos; `<dl>` asocia cada término con su descripción |
| **`<th>` vs `<td>`** | `<th>` es un encabezado de fila o columna; `<td>` es un dato |
| **`<thead>` vs `<th>`** | `<thead>` es un **grupo de filas** de encabezado; `<th>` es una **celda** de encabezado |
| **`<caption>` vs `<h2>`** | `<caption>` es el título interno de la tabla y queda asociado a ella; `<h2>` es un encabezado de sección independiente |
| **`colspan` vs `rowspan`** | `colspan` combina celdas en horizontal (columnas); `rowspan`, en vertical (filas) |
| **`scope` vs `headers`** | `scope` basta en tablas simples; `headers` se reserva para tablas complejas con encabezados irregulares |
| **Tabla de datos vs tabla de maquetación** | La primera muestra datos con significado; la segunda era un uso antiguo para colocar elementos y hoy está obsoleta |
| **`<ol>` vs viñetas de CSS** | `<ol>` expresa un **orden real**; cambiar el `list-style` solo cambia cómo se ve |

---

## 8. Casos especiales

### Listas sin viñetas

Los menús suelen quitar las viñetas con CSS (`list-style: none`). La lista sigue siendo una lista, pero algunos navegadores con lectores de pantalla dejan de anunciarla como tal. Si es importante conservar esa semántica, se puede añadir `role="list"`.

### Cambiar la numeración

`start`, `reversed` y `value` permiten empezar en otro número, contar hacia atrás o saltar a un valor concreto. Es útil para continuar una lista interrumpida o hacer una cuenta atrás.

### Listas de descripción con varios términos o descripciones

Un término puede tener **varias descripciones**, y varias descripciones pueden compartir **un término** (por ejemplo, un mismo significado con varias palabras). Agrupar cada par en un `<div>` facilita aplicar estilos.

### Tablas con encabezados en dos niveles

Cuando hay encabezados agrupados, se combinan `colspan` y `scope="colgroup"`:

```html
<thead>
  <tr>
    <td rowspan="2"></td>
    <th colspan="2" scope="colgroup">2026</th>
  </tr>
  <tr>
    <th scope="col">T1</th>
    <th scope="col">T2</th>
  </tr>
</thead>
```

### Tablas largas

`<thead>` se puede mantener visible al desplazarse con CSS (`position: sticky`, [[CSS/09 - Position]]). Para tablas con muchas filas, conviene separar el contenido en varios `<tbody>`.

### Tabla frente a lista o cuadrícula

Si los datos no tienen dos dimensiones reales, una tabla no es la herramienta adecuada. Una lista de tarjetas con **CSS Grid** ([[CSS/08 - Grid]]) funciona mejor en pantallas pequeñas.

> [!warning] Obsoleto / legado
> - Maquetar páginas con tablas (`<table>` para cabecera, menú y contenido) está obsoleto: se usa **CSS** ([[CSS/07 - Flexbox]], [[CSS/08 - Grid]]).
> - Los atributos `border`, `cellpadding`, `cellspacing`, `align`, `bgcolor`, `width` y `valign` en tablas están obsoletos: se sustituyen por **CSS**.
> - `<dir>` está obsoleto: se usa `<ul>`.
> - El atributo `compact` en listas está obsoleto.
> - El atributo `summary` de `<table>` está obsoleto: se usa `<caption>` o un texto cercano.

---

## 9. Resumen

- Hay tres tipos de lista: `<ul>` (sin orden), `<ol>` (con orden) y `<dl>` (términos y descripciones). Los elementos de `<ul>` y `<ol>` son `<li>`; los de `<dl>` son `<dt>` y `<dd>`.
- Las listas anidadas van **dentro de un `<li>`**, y los menús de navegación se construyen con listas.
- `<ol>` admite `start`, `reversed` y `type`; `<li>` admite `value`.
- Una tabla se compone de filas `<tr>` con celdas `<th>` (encabezado) y `<td>` (datos).
- La estructura completa de una tabla es `<caption>`, `<thead>`, `<tbody>` y `<tfoot>`.
- `colspan` y `rowspan` combinan celdas; `scope` y `headers` asocian los datos con sus encabezados.
- Las tablas son solo para **datos tabulares**; el aspecto de listas y tablas se controla con CSS.