# HTML - Texto y enlaces

> [!info] ¿Qué es?
> El **texto** es la base de casi cualquier página web, y HTML ofrece etiquetas para darle **estructura y significado**: títulos, párrafos, énfasis, citas o fragmentos de código. Los **enlaces** (`<a>`) conectan unas páginas con otras y son la esencia de la web: lo que convierte documentos aislados en una red de hipertexto.

---

## 1. Antes de empezar

Conviene dominar antes:

- La sintaxis de etiquetas, atributos y anidación: [[HTML/01 - Fundamentos]].
- La jerarquía de encabezados y las zonas de la página: [[HTML/02 - Estructura y semántica]].

> [!tip] Cómo elegir una etiqueta de texto
> Pregúntate **qué significa** ese texto (¿es importante?, ¿es una cita?, ¿es código?) y no cómo quieres que se vea. El aspecto lo decide CSS ([[CSS/06 - Texto y fuentes]]).

---

## 2. Concepto fundamental

Las etiquetas de texto se dividen en dos grupos según cómo se comportan en la página:

- **Elementos de bloque**: ocupan todo el ancho disponible y empiezan en una línea nueva (`<h1>`-`<h6>`, `<p>`, `<blockquote>`, `<pre>`, `<hr>`).
- **Elementos en línea** (*inline*): fluyen dentro del texto sin romper la línea (`<strong>`, `<em>`, `<a>`, `<code>`, `<span>`).

Un **enlace** es un elemento `<a>` (*anchor*, ancla) con un atributo `href` que indica el **destino**. Ese destino puede ser otra página, un archivo, un punto concreto de la misma página, una dirección de correo o un número de teléfono.

> [!note] El texto del enlace importa
> Lo que hay dentro de `<a>...</a>` es lo que ven y escuchan las personas usuarias, los lectores de pantalla y los buscadores. Debe describir **a dónde lleva** el enlace.

---

## 3. Sintaxis / estructura

### 3.1 Encabezados y párrafos

```html
<h1>Título principal</h1>
<h2>Sección</h2>
<p>Esto es un párrafo de texto.</p>
```

### 3.2 Saltos de línea y separadores

```html
<p>Calle Mayor, 1<br>18000 Granada</p>
<hr>
```

- `<br>` fuerza un salto de línea **dentro** de un texto (direcciones, poemas).
- `<hr>` marca un cambio temático entre bloques de contenido.

### 3.3 Énfasis e importancia

```html
<p>Esto es <strong>muy importante</strong> y esto lleva <em>énfasis</em>.</p>
```

### 3.4 Citas

```html
<blockquote cite="https://ejemplo.com/fuente">
  <p>Cita larga, en bloque aparte.</p>
</blockquote>
<p>Como dijo alguien: <q>cita corta dentro del texto</q>.</p>
```

### 3.5 Código y texto preformateado

```html
<p>Usa la etiqueta <code>&lt;p&gt;</code> para párrafos.</p>

<pre><code>function saludar() {
  console.log("Hola");
}</code></pre>
```

### 3.6 Anatomía de un enlace

```html
<a href="https://www.ejemplo.com" target="_blank" rel="noopener">Visitar Ejemplo</a>
```

| Parte | En el ejemplo | Función |
|-------|---------------|---------|
| Etiqueta `<a>` | `<a>...</a>` | Crea el enlace |
| `href` | `https://www.ejemplo.com` | Destino del enlace |
| `target` | `_blank` | Dónde se abre (aquí, en una pestaña nueva) |
| `rel` | `noopener` | Relación con el destino y medidas de seguridad |
| Texto del enlace | `Visitar Ejemplo` | Lo que se ve y se pulsa |

---

## 4. Elementos / propiedades / características

### 4.1 Encabezados y bloques de texto

| Elemento | Función |
|----------|---------|
| `<h1>` a `<h6>` | Encabezados de nivel 1 (más importante) a 6 (menos importante) |
| `<p>` | Párrafo |
| `<br>` | Salto de línea (elemento vacío) |
| `<hr>` | Separador temático (elemento vacío) |
| `<blockquote>` | Cita extensa en bloque |
| `<pre>` | Texto preformateado: respeta espacios y saltos de línea |

### 4.2 Elementos en línea con significado

| Elemento | Significado | Aspecto por defecto |
|----------|-------------|---------------------|
| `<strong>` | Importancia, seriedad o urgencia | Negrita |
| `<em>` | Énfasis (cambia el sentido al leerlo) | Cursiva |
| `<mark>` | Texto resaltado por relevancia en el contexto | Fondo amarillo |
| `<small>` | Letra pequeña: aviso legal, nota, derechos | Texto más pequeño |
| `<del>` / `<ins>` | Texto eliminado / añadido en una revisión | Tachado / subrayado |
| `<sub>` / `<sup>` | Subíndice / superíndice | H<sub>2</sub>O, x<sup>2</sup> |
| `<q>` | Cita corta en línea | Con comillas |
| `` | Título de una obra citada | Cursiva |
| `<abbr title="...">` | Abreviatura o sigla con su significado | Subrayado punteado |
| `<time datetime="...">` | Fecha u hora legible por máquinas | Sin cambio |
| `<span>` | Contenedor en línea sin significado | Sin cambio |

### 4.3 Elementos de código y teclado

| Elemento | Uso |
|----------|-----|
| `<code>` | Fragmento de código |
| `<kbd>` | Tecla o combinación que debe pulsar la persona usuaria (`<kbd>Ctrl</kbd> + <kbd>C</kbd>`) |
| `<samp>` | Salida de un programa |
| `<var>` | Variable de una expresión matemática o de programación |

### 4.4 Atributos del enlace `<a>`

| Atributo | Función | Ejemplo |
|----------|---------|---------|
| `href` | Destino del enlace | `href="contacto.html"` |
| `target` | Dónde se abre el destino | `_self` (por defecto, misma pestaña), `_blank` (pestaña nueva) |
| `rel` | Relación con el destino | `noopener`, `noreferrer`, `nofollow`, `sponsored`, `ugc` |
| `download` | Descarga el archivo en lugar de abrirlo | `download="informe.pdf"` |
| `title` | Información adicional (tooltip) | `title="Ir al contacto"` |
| `hreflang` | Idioma del destino | `hreflang="en"` |

### 4.5 Tipos de destino de `href`

| Tipo | Ejemplo | Qué hace |
|------|---------|----------|
| URL absoluta | `https://www.ejemplo.com/pagina` | Enlaza a otro sitio (incluye protocolo y dominio) |
| Ruta relativa | `contacto.html`, `../img/logo.png` | Enlaza dentro del mismo sitio, desde la ubicación actual |
| Ruta desde la raíz | `/blog/articulo.html` | Parte de la raíz del sitio |
| Ancla interna | `#seccion` | Salta a un elemento de la misma página con `id="seccion"` |
| Ancla en otra página | `pagina.html#seccion` | Abre otra página y salta a ese punto |
| Correo | `mailto:info@ejemplo.com` | Abre el cliente de correo |
| Teléfono | `tel:+34600000000` | Inicia una llamada en dispositivos compatibles |

### 4.6 Rutas relativas más usadas

| Ruta | Significado |
|------|-------------|
| `archivo.html` | Mismo directorio |
| `carpeta/archivo.html` | Dentro de una subcarpeta |
| `../archivo.html` | Una carpeta por encima |
| `/archivo.html` | Desde la raíz del sitio |

### 4.7 Comportamiento del espacio en blanco

HTML colapsa los espacios y saltos de línea consecutivos en uno solo (ver [[HTML/01 - Fundamentos]]). La excepción es `<pre>`, que conserva el formato tal como está escrito.

---

## 5. Ejemplos prácticos

### Ejemplo básico

Título, párrafo y un enlace:

```html
<h1>Bienvenido</h1>
<p>Esta es mi página. Visita <a href="https://developer.mozilla.org">MDN Web Docs</a> para aprender más.</p>
```

### Ejemplo habitual

Artículo con énfasis, cita, código y enlaces de distintos tipos:

```html
<article>
  <h1>Aprender HTML</h1>
  <p>
    HTML es la <strong>base de la web</strong>. Empieza por los
    <a href="fundamentos.html">fundamentos</a> y practica cada día.
  </p>

  <blockquote cite="https://ejemplo.com/fuente">
    <p>La práctica constante es la mejor manera de aprender.</p>
  </blockquote>

  <p>Para crear un párrafo usa la etiqueta <code>&lt;p&gt;</code>.</p>

  <p>
    ¿Dudas? Escríbenos a
    <a href="mailto:info@ejemplo.com">info@ejemplo.com</a>
    o llama al <a href="tel:+34600000000">600 000 000</a>.
  </p>
</article>
```

### Ejemplo completo

Página con índice de anclas internas, enlace externo seguro, descarga y texto técnico:

```html
<body>
  <a href="#contenido">Saltar al contenido</a>

  <header>
    <h1>Guía de enlaces</h1>
    <nav aria-label="Índice">
      <ul>
        <li><a href="#tipos">Tipos de enlaces</a></li>
        <li><a href="#recursos">Recursos</a></li>
      </ul>
    </nav>
  </header>

  <main id="contenido">
    <section id="tipos">
      <h2>Tipos de enlaces</h2>
      <p>
        Un enlace <em>interno</em> usa una ruta relativa, como
        <a href="../inicio.html">volver al inicio</a>. Uno <em>externo</em>
        usa la URL completa.
      </p>
      <p>
        Al copiar, pulsa <kbd>Ctrl</kbd> + <kbd>C</kbd>. El comando
        <code>git status</code> devuelve:
      </p>
      <pre><samp>On branch main
nothing to commit, working tree clean</samp></pre>
    </section>

    <section id="recursos">
      <h2>Recursos</h2>
      <ul>
        <li>
          <a href="https://developer.mozilla.org" target="_blank" rel="noopener">
            Documentación de MDN (se abre en una pestaña nueva)
          </a>
        </li>
        <li>
          <a href="docs/guia.pdf" download>Descargar la guía en PDF</a>
        </li>
      </ul>
      <p>Actualizado el <time datetime="2026-10-02">2 de octubre de 2026</time>.</p>
      <p><small>Contenido de uso educativo.</small></p>
    </section>

    <p><a href="#">Volver arriba</a></p>
  </main>
</body>
```

Las listas se estudian en [[HTML/05 - Listas y tablas]] y la navegación por teclado en [[HTML/08 - Accesibilidad]].

---

## 6. Buenas prácticas

- Usar **un `<p>` por párrafo** y los encabezados en orden, sin saltar niveles.
- Elegir `<strong>`, `<em>`, `<mark>`, etc. por su **significado**, no por su aspecto.
- Escribir **textos de enlace descriptivos**, comprensibles aunque se lean fuera de contexto.
- Usar rutas **relativas** para enlaces internos y URL absolutas con `https://` para los externos.
- Añadir `rel="noopener"` a los enlaces con `target="_blank"` y avisar de que abren una pestaña nueva.
- Marcar con `rel="nofollow"`, `rel="sponsored"` o `rel="ugc"` los enlaces patrocinados o de usuarios cuando corresponda ([[HTML/09 - SEO]]).
- Poner un `<a href="#contenido">Saltar al contenido</a>` al principio de páginas con mucha navegación.
- Usar `<code>` para el código en línea y `<pre><code>` para bloques.
- Usar `<abbr title="...">` la primera vez que aparece una sigla poco conocida.
- No depender solo del **color** para distinguir los enlaces: conservar el subrayado u otra pista visual ([[CSS/12 - Pseudoclases y pseudoelementos]] para los estados `:hover`, `:focus` y `:visited`).

Más criterios de calidad en [[HTML/11 - Buenas prácticas]].

---

## 7. Diferencias importantes

| Comparación                        | Diferencia                                                                                                |
| ---------------------------------- | --------------------------------------------------------------------------------------------------------- |
| **`<strong>` vs `<b>`**            | `<strong>` indica importancia; `<b>` solo llama la atención sin darle más importancia                     |
| **`<em>` vs `<i>`**                | `<em>` indica énfasis; `<i>` marca otra voz o tono (términos técnicos, nombres científicos, pensamientos) |
| **`<blockquote>` vs `<q>`**        | `<blockquote>` es una cita larga en bloque; `<q>` es una cita corta en línea                              |
| **`<code>` vs `<pre>`**            | `<code>` marca código; `<pre>` conserva el formato. Para bloques de código se combinan                    |
| **`<p>` vs `<br>`**                | `<p>` separa párrafos; `<br>` rompe una línea dentro del mismo párrafo                                    |
| **Enlace relativo vs absoluto**    | El relativo depende de dónde está la página actual; el absoluto incluye la dirección completa             |
| **`<a>` vs `<button>`**            | `<a>` lleva a otro destino; `<button>` ejecuta una acción                                                 |
| **`target="_self"` vs `"_blank"`** | `_self` abre en la misma pestaña; `_blank`, en una nueva                                                  |
| **`<del>` vs `<s>`**               | `<del>` es un texto eliminado de un documento; `<s>` es un texto que ya no es correcto o relevante        |

---

## 8. Casos especiales

### Enlaces que envuelven más que texto

Un `<a>` puede contener imágenes y bloques completos, como una tarjeta pulsable. Si envuelve una imagen sin texto, el atributo `alt` de la imagen actúa como texto del enlace ([[HTML/04 - Imágenes y multimedia]]).

### `mailto:` con asunto y cuerpo

```html
<a href="mailto:info@ejemplo.com?subject=Consulta&amp;body=Hola">Escribir un correo</a>
```

Los parámetros se separan con `&`, que dentro de un atributo debe escribirse como `&amp;`.

### `download` solo funciona en el mismo origen

El atributo `download` se aplica a archivos del **mismo sitio**. En enlaces a otro dominio, el navegador suele ignorarlo y abrir el archivo.

### Anclas y `scroll-padding`

Si la página tiene una cabecera fija, el destino de una ancla puede quedar oculto debajo. Se corrige con CSS (`scroll-margin-top` o `scroll-padding-top`).

### Texto largo sin espacios

Una URL o palabra muy larga puede desbordar su contenedor. Se puede ayudar al navegador con `<wbr>` (punto de ruptura opcional) o resolverlo con CSS (`overflow-wrap`).

### Un enlace puede apuntar al mismo documento y a otro a la vez

Con `pagina.html#seccion` se abre otra página y se salta directamente a un punto concreto de ella.

> [!warning] Obsoleto / legado
> - `<font>`, `<center>`, `<big>`, `<strike>` y `<tt>` están obsoletos: se sustituyen por **CSS** ([[CSS/06 - Texto y fuentes]]).
> - `<acronym>` está obsoleto: se usa `<abbr>`.
> - `<a name="seccion">` para crear anclas está obsoleto: se usa `id` en el elemento de destino.
> - Atributos de color de enlace en `<body>` (`link`, `vlink`, `alink`) están obsoletos: se usan los estados `:link`, `:visited` y `:active` en CSS.

---


## 9. Resumen

- El texto se estructura con **encabezados** (`<h1>`-`<h6>`), **párrafos** (`<p>`) y elementos **en línea** con significado (`<strong>`, `<em>`, `<mark>`, `<code>`, `<q>`, `<abbr>`...).
- `<br>` y `<hr>` son elementos vacíos para saltos de línea y separadores temáticos; `<pre>` conserva el formato del texto.
- Un **enlace** es un `<a href="...">` y su destino puede ser una URL, una ruta relativa, un ancla (`#id`), un correo (`mailto:`) o un teléfono (`tel:`).
- `target="_blank"` abre una pestaña nueva y debe acompañarse de `rel="noopener"`.
- El texto del enlace debe ser **descriptivo**: evitar "haz clic aquí".
- Un enlace **navega**; un botón **ejecuta una acción**.
- Se elige cada etiqueta por su **significado**, y el aspecto se deja a CSS.