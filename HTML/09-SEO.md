# HTML - SEO

> [!info] ¿Qué es?
> **SEO** (*Search Engine Optimization*, optimización para motores de búsqueda) es el conjunto de prácticas que ayudan a que los buscadores **encuentren, entiendan e indexen** una página, y a que la muestren de forma atractiva en los resultados. Desde HTML se trabaja el **SEO técnico y de contenido**: título, metaetiquetas, encabezados, enlaces, imágenes y datos estructurados. El posicionamiento final depende de muchos más factores (calidad del contenido, autoridad del sitio, rendimiento), y ninguna etiqueta garantiza una posición concreta.

---

## 1. Antes de empezar

Conviene dominar antes:

- El `<head>` y sus metadatos básicos: [[HTML/01 - Fundamentos]].
- Los encabezados y la estructura semántica: [[HTML/02 - Estructura y semántica]].
- Los enlaces y las imágenes con `alt`: [[HTML/03 - Texto y enlaces]] y [[HTML/04 - Imágenes y multimedia]].

> [!tip] Piensa primero en las personas
> Los buscadores intentan premiar lo que mejor responde a lo que la gente busca. Un contenido claro, útil y bien estructurado es la base del SEO; las etiquetas solo ayudan a que el buscador lo **entienda** y lo **presente** bien.

---

## 2. Concepto fundamental

Un buscador funciona en tres fases:

| Fase | Qué hace | Qué puedes controlar desde HTML |
|------|----------|----------------------------------|
| **Rastreo** (*crawling*) | Un robot descubre páginas siguiendo enlaces | Enlaces `<a href>` reales, `robots.txt`, `sitemap.xml` |
| **Indexación** | Analiza el contenido y lo guarda en su índice | `<title>`, encabezados, texto, `canonical`, `meta robots`, datos estructurados |
| **Posicionamiento** (*ranking*) | Ordena los resultados para cada búsqueda | Relevancia del contenido, experiencia de uso, rendimiento |

El HTML sirve a esas fases de tres maneras:

- **Legibilidad**: el buscador lee el HTML, por lo que el contenido importante debe estar en el código como texto, no solo dentro de imágenes.
- **Significado**: la semántica y los datos estructurados explican **qué es** cada cosa.
- **Presentación**: `<title>`, `meta description` y las etiquetas de redes sociales determinan cómo se **muestra** la página en los resultados y al compartirla.

> [!note] Lo que ya no importa
> Las metaetiquetas por sí solas no posicionan. `<meta name="keywords">` lo ignoran los principales buscadores desde hace años, y repetir palabras clave en exceso (*keyword stuffing*) puede perjudicar.

---

## 3. Sintaxis / estructura

### 3.1 Cabecera mínima orientada a SEO

```html
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Curso de HTML desde cero | Academia Web</title>
  <meta name="description" content="Aprende HTML paso a paso con ejemplos prácticos: etiquetas, formularios, accesibilidad y SEO. Curso gratuito para principiantes.">
  <link rel="canonical" href="https://www.ejemplo.com/cursos/html">
</head>
```

### 3.2 Control de la indexación

```html
<!-- No indexar esta página y no seguir sus enlaces -->
<meta name="robots" content="noindex, nofollow">

<!-- Indexar pero no mostrar fragmento de texto -->
<meta name="robots" content="nosnippet">
```

### 3.3 Etiquetas para redes sociales (Open Graph y Twitter)

```html
<meta property="og:type" content="article">
<meta property="og:title" content="Curso de HTML desde cero">
<meta property="og:description" content="Aprende HTML paso a paso con ejemplos prácticos.">
<meta property="og:image" content="https://www.ejemplo.com/img/portada-html.jpg">
<meta property="og:url" content="https://www.ejemplo.com/cursos/html">
<meta name="twitter:card" content="summary_large_image">
```

### 3.4 Versiones de la página en otros idiomas

```html
<link rel="alternate" hreflang="es" href="https://www.ejemplo.com/es/">
<link rel="alternate" hreflang="en" href="https://www.ejemplo.com/en/">
<link rel="alternate" hreflang="x-default" href="https://www.ejemplo.com/">
```

### 3.5 Datos estructurados en JSON-LD

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Article",
  "headline": "Curso de HTML desde cero",
  "datePublished": "2026-10-02",
  "author": { "@type": "Person", "name": "Ana López" }
}
</script>
```

---

## 4. Elementos / propiedades / características

### 4.1 Elementos y metaetiquetas clave

| Elemento | Función SEO | Recomendación |
|----------|-------------|---------------|
| `<title>` | Título que se muestra en los resultados y en la pestaña | Único por página y descriptivo; unos **50-60 caracteres** para que no se corte |
| `<meta name="description">` | Resumen que suele aparecer bajo el título | Unos **150-160 caracteres**; no posiciona, pero influye en que se haga clic |
| `<h1>`-`<h6>` | Estructura y temas del contenido | Un `<h1>` claro y subtítulos jerárquicos ([[HTML/02 - Estructura y semántica]]) |
| `<link rel="canonical">` | Indica la URL **preferida** cuando hay contenido duplicado o similar | Una por página, con URL absoluta |
| `<meta name="robots">` | Controla indexación y fragmentos | Solo cuando haya que cambiar el comportamiento por defecto |
| `<link rel="alternate" hreflang>` | Relaciona versiones por idioma o región | Cada versión se enlaza a todas las demás y a sí misma |
| `<html lang>` | Idioma del contenido | Siempre declarado ([[HTML/01 - Fundamentos]]) |
| `<meta name="viewport">` | Adaptación a móvil | Imprescindible: Google rastrea con enfoque *mobile-first* |
| `<script type="application/ld+json">` | Datos estructurados | Para obtener resultados enriquecidos |
| `<img alt>` | Describe la imagen al buscador | Descriptivo y breve ([[HTML/04 - Imágenes y multimedia]]) |
| `<a href>` | Enlaza páginas y transmite contexto | Texto descriptivo y destino rastreable |
| `<link rel="icon">` | Icono del sitio en pestañas y resultados | Un favicon accesible y de buen tamaño |

### 4.2 Valores de `meta name="robots"`

| Valor | Efecto |
|-------|--------|
| `index` / `noindex` | Permite / impide que la página aparezca en el índice (`index` es el valor por defecto) |
| `follow` / `nofollow` | Permite / impide seguir los enlaces de la página (`follow` es el valor por defecto) |
| `nosnippet` | No muestra fragmento de texto en los resultados |
| `max-snippet:n` | Limita el fragmento a `n` caracteres |
| `max-image-preview:large` | Permite imágenes grandes en los resultados |
| `noarchive` | No ofrece una copia en caché |

Los valores se combinan separados por comas: `noindex, nofollow`.

### 4.3 Valores de `rel` en enlaces

| Valor | Cuándo usarlo |
|-------|---------------|
| `nofollow` | Enlaces a los que no quieres dar tu respaldo |
| `sponsored` | Enlaces publicitarios o patrocinados |
| `ugc` | Enlaces en contenido generado por usuarios (comentarios, foros) |
| `noopener` | Seguridad en `target="_blank"`; no es un valor SEO ([[HTML/03 - Texto y enlaces]]) |

### 4.4 Etiquetas Open Graph y Twitter más usadas

| Etiqueta | Función |
|----------|---------|
| `og:title` | Título al compartir |
| `og:description` | Descripción al compartir |
| `og:image` | Imagen de la vista previa (URL absoluta; tamaño recomendado 1200 × 630 px) |
| `og:url` | URL canónica de la página |
| `og:type` | Tipo de contenido (`website`, `article`...) |
| `og:locale` | Idioma y región (`es_ES`) |
| `twitter:card` | Formato de la tarjeta: `summary` o `summary_large_image` |

### 4.5 Tipos de datos estructurados frecuentes (schema.org)

| Tipo | Describe |
|------|----------|
| `Article` / `BlogPosting` | Artículos y entradas de blog |
| `Product` y `Offer` | Productos, precios y disponibilidad |
| `Organization` / `LocalBusiness` | Empresa, logo y datos de contacto |
| `BreadcrumbList` | Migas de pan (ruta de navegación) |
| `Recipe` | Recetas |
| `Event` | Eventos |
| `VideoObject` | Vídeos |

Los buscadores deciden qué resultados enriquecidos muestran y esa lista cambia con el tiempo. Conviene comprobar cada caso con la herramienta **Prueba de resultados enriquecidos** de Google.

### 4.6 Rendimiento y experiencia (Core Web Vitals)

| Métrica | Mide | Objetivo orientativo |
|---------|------|----------------------|
| **LCP** (*Largest Contentful Paint*) | Cuánto tarda en verse el contenido principal | ≤ 2,5 s |
| **INP** (*Interaction to Next Paint*) | Cuánto tarda la página en responder a las interacciones | ≤ 200 ms |
| **CLS** (*Cumulative Layout Shift*) | Cuánto se desplaza el diseño mientras carga | ≤ 0,1 |

Desde HTML se mejoran con `width` y `height` en las imágenes, `loading="lazy"` en las que no se ven al cargar, `fetchpriority="high"` en la principal y `defer` en los scripts ([[HTML/04 - Imágenes y multimedia]], [[HTML/07 - Atributos]]).

### 4.7 Archivos relacionados que no son HTML

| Archivo | Función |
|---------|---------|
| `robots.txt` | Indica a los robots qué zonas **no deben rastrear** |
| `sitemap.xml` | Lista las URL que quieres que el buscador conozca |

Ambos se sitúan en la raíz del sitio.

---

## 5. Ejemplos prácticos

### Ejemplo básico

Título, descripción e idioma:

```html
<!DOCTYPE html>
<html lang="es">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Receta de tortilla de patatas | Cocina fácil</title>
    <meta name="description" content="Receta paso a paso de la tortilla de patatas jugosa, con ingredientes, tiempos y trucos para que te salga perfecta.">
  </head>
  <body>
    <h1>Receta de tortilla de patatas</h1>
  </body>
</html>
```

### Ejemplo habitual

Página con canonical, Open Graph y estructura de encabezados clara:

```html
<!DOCTYPE html>
<html lang="es">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Guía de Flexbox: ejemplos y propiedades | Academia Web</title>
    <meta name="description" content="Aprende CSS Flexbox con ejemplos visuales: ejes, alineación, espaciado y casos reales de maquetación.">
    <link rel="canonical" href="https://www.ejemplo.com/guias/flexbox">

    <meta property="og:type" content="article">
    <meta property="og:title" content="Guía de Flexbox: ejemplos y propiedades">
    <meta property="og:description" content="Aprende CSS Flexbox con ejemplos visuales.">
    <meta property="og:image" content="https://www.ejemplo.com/img/flexbox.jpg">
    <meta property="og:url" content="https://www.ejemplo.com/guias/flexbox">
    <meta name="twitter:card" content="summary_large_image">
  </head>
  <body>
    <header>
      <nav aria-label="Principal">
        <ul>
          <li><a href="/">Inicio</a></li>
          <li><a href="/guias">Guías</a></li>
        </ul>
      </nav>
    </header>

    <main>
      <article>
        <h1>Guía de Flexbox</h1>
        <p>Flexbox es un sistema de maquetación en una dimensión...</p>

        <h2>El contenedor flex</h2>
        <p>...</p>

        <h2>Alineación de elementos</h2>
        <h3>Eje principal</h3>
        <p>...</p>
        <h3>Eje secundario</h3>
        <p>...</p>

        <img src="img/ejes-flexbox.png" alt="Diagrama de los ejes principal y secundario en Flexbox" width="800" height="450">
      </article>
    </main>
  </body>
</html>
```

La maquetación de Flexbox se estudia en [[CSS/07 - Flexbox]].

### Ejemplo completo

Artículo multilingüe con datos estructurados, migas de pan y enlaces diferenciados:

```html
<!DOCTYPE html>
<html lang="es">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Qué es la semántica en HTML | Academia Web</title>
    <meta name="description" content="Descubre qué es la semántica en HTML, por qué mejora el SEO y la accesibilidad, y cómo elegir la etiqueta correcta en cada caso.">
    <meta name="robots" content="max-image-preview:large">
    <link rel="canonical" href="https://www.ejemplo.com/es/semantica-html">
    <link rel="alternate" hreflang="es" href="https://www.ejemplo.com/es/semantica-html">
    <link rel="alternate" hreflang="en" href="https://www.ejemplo.com/en/html-semantics">
    <link rel="alternate" hreflang="x-default" href="https://www.ejemplo.com/en/html-semantics">
    <link rel="icon" href="/favicon.svg" type="image/svg+xml">

    <meta property="og:type" content="article">
    <meta property="og:title" content="Qué es la semántica en HTML">
    <meta property="og:description" content="Por qué mejora el SEO y cómo elegir la etiqueta correcta.">
    <meta property="og:image" content="https://www.ejemplo.com/img/semantica.jpg">
    <meta property="og:url" content="https://www.ejemplo.com/es/semantica-html">
    <meta property="og:locale" content="es_ES">
    <meta name="twitter:card" content="summary_large_image">

    <script type="application/ld+json">
    {
      "@context": "https://schema.org",
      "@graph": [
        {
          "@type": "Article",
          "headline": "Qué es la semántica en HTML",
          "image": "https://www.ejemplo.com/img/semantica.jpg",
          "datePublished": "2026-10-02",
          "dateModified": "2026-10-02",
          "author": { "@type": "Person", "name": "Ana López" },
          "inLanguage": "es"
        },
        {
          "@type": "BreadcrumbList",
          "itemListElement": [
            { "@type": "ListItem", "position": 1, "name": "Inicio", "item": "https://www.ejemplo.com/" },
            { "@type": "ListItem", "position": 2, "name": "Guías", "item": "https://www.ejemplo.com/es/guias" },
            { "@type": "ListItem", "position": 3, "name": "Semántica en HTML" }
          ]
        }
      ]
    }
    </script>
  </head>
  <body>
    <header>
      <nav aria-label="Migas de pan">
        <ol>
          <li><a href="/">Inicio</a></li>
          <li><a href="/es/guias">Guías</a></li>
          <li aria-current="page">Semántica en HTML</li>
        </ol>
      </nav>
    </header>

    <main>
      <article>
        <h1>Qué es la semántica en HTML</h1>
        <p>Publicado el <time datetime="2026-10-02">2 de octubre de 2026</time></p>

        <p>
          Una buena estructura ayuda al buscador a entender el contenido.
          Puedes profundizar en la <a href="/es/accesibilidad-html">accesibilidad en HTML</a>
          o consultar la <a href="https://developer.mozilla.org/es/docs/Web/HTML">documentación de MDN</a>.
        </p>

        <h2>Por qué importa la semántica</h2>
        <p>...</p>

        <h2>Cómo elegir la etiqueta correcta</h2>
        <p>...</p>

        <p>
          Herramienta recomendada:
          <a href="https://www.ejemplo-afiliado.com/producto" rel="sponsored noopener" target="_blank">ver oferta</a>.
        </p>
      </article>
    </main>
  </body>
</html>
```

---

## 6. Buenas prácticas

- Escribir un `<title>` **único, descriptivo y atractivo** para cada página, con las palabras importantes al principio.
- Redactar una `meta description` única por página, que resuma de verdad el contenido e invite a hacer clic. El buscador puede mostrar otro fragmento si lo considera más adecuado.
- Usar **un solo `<h1>`** que describa el tema de la página, y una jerarquía lógica de subtítulos.
- Escribir contenido **original, útil y para personas**; integrar las palabras clave de forma natural, sin repetirlas artificialmente.
- Declarar `lang` en `<html>` e incluir `viewport` para el rastreo móvil.
- Usar enlaces `<a href>` **reales y rastreables** (no solo botones con JavaScript) con textos descriptivos.
- Enlazar entre páginas relacionadas del propio sitio (enlazado interno).
- Añadir un `alt` descriptivo a las imágenes, nombres de archivo claros (`semantica-html.jpg`) y las dimensiones `width` y `height`.
- Definir una URL **canónica** en cada página, sobre todo si el mismo contenido es accesible desde varias URL (parámetros, versiones con y sin barra final).
- Usar `noindex` en páginas que no deben aparecer en buscadores (resultados internos de búsqueda, agradecimientos, áreas privadas).
- Marcar los enlaces de pago con `rel="sponsored"` y los de usuarios con `rel="ugc"`.
- Añadir **datos estructurados** (JSON-LD) cuando el tipo de contenido lo permita, y comprobarlos con las herramientas de prueba.
- Cuidar la velocidad y la estabilidad visual: imágenes optimizadas, `defer` en scripts, carga diferida.
- Mantener URL cortas, legibles y en minúsculas (`/cursos/html`, no `/p?id=327`).
- Servir el sitio por **HTTPS** y mantener un `sitemap.xml` actualizado.
- Medir resultados con **Google Search Console**, que informa de rastreo, indexación y errores.

Más criterios de calidad en [[HTML/11 - Buenas prácticas]].

---

## 7. Diferencias importantes

| Comparación | Diferencia |
|-------------|------------|
| **`<title>` vs `<h1>`** | `<title>` aparece en los resultados y en la pestaña; `<h1>` es el título visible dentro de la página. Pueden ser parecidos, no idénticos |
| **`meta description` vs contenido** | La descripción es un resumen para los resultados; el buscador analiza el contenido real de la página |
| **`noindex` vs `robots.txt`** | `noindex` impide la **indexación**; `robots.txt` impide el **rastreo**. Si bloqueas una URL en `robots.txt`, el robot no verá su `noindex` y la URL aún puede aparecer en resultados |
| **`noindex` vs `canonical`** | `noindex` excluye la página del índice; `canonical` indica cuál de varias páginas similares es la preferida |
| **`nofollow` (meta) vs `rel="nofollow"`** | El de `meta robots` afecta a **todos** los enlaces de la página; el de `rel`, a un **enlace concreto** |
| **`nofollow` vs `sponsored` vs `ugc`** | Los tres indican que no respaldas el enlace; los dos últimos explican además el motivo |
| **Open Graph vs `meta description`** | Open Graph controla la vista previa en redes sociales; la `meta description`, los resultados del buscador |
| **`hreflang` vs `lang`** | `lang` declara el idioma de **esta** página; `hreflang` relaciona esta página con sus **versiones** en otros idiomas |
| **SEO técnico vs de contenido** | El técnico facilita rastrear y entender el sitio; el de contenido responde a lo que la gente busca |
| **JSON-LD vs microdatos** | JSON-LD va en un bloque aparte, separado del HTML visible, y es el formato recomendado; los microdatos van mezclados en las etiquetas |

---

## 8. Casos especiales

### Contenido que se genera con JavaScript

Los buscadores ejecutan JavaScript, pero con retraso y algunas limitaciones. El contenido importante y los enlaces deberían estar en el **HTML que llega al navegador** (renderizado en el servidor o generado de antemano) y no depender de que un script lo cree después.

### Páginas duplicadas o muy similares

Si el mismo contenido es accesible por varias URL (con y sin `www`, con parámetros de seguimiento, versiones para imprimir), se usa `canonical` para señalar la preferida. Las redirecciones permanentes (301) en el servidor son todavía más claras.

### Paginación y filtros

Cada página de una paginación debería tener su propio `<title>` y su `canonical` a sí misma. Las páginas de filtros que generan miles de combinaciones se suelen excluir del índice con `noindex` o evitar con enlaces rastreables.

### Cambio de estado "oculto"

El contenido oculto por defecto (pestañas, acordeones) se indexa, pero los buscadores pueden darle menos peso que el visible. Si es esencial, conviene mostrarlo.

### Imágenes en buscadores

Las imágenes aparecen también en la búsqueda de imágenes. Ayudan el `alt`, un nombre de archivo descriptivo, el texto cercano, la leyenda en `<figcaption>` y formatos ligeros (WebP, AVIF).

### Vídeo

Ayudan una buena página de destino con título y descripción, `VideoObject` en datos estructurados y una transcripción en el texto.

### Resultados enriquecidos y FAQ

Los tipos de resultados enriquecidos que Google muestra cambian con los años, y algunos se han limitado mucho (por ejemplo, los de preguntas frecuentes). Marcar datos estructurados no garantiza que se muestren.

### Páginas con error

Una página que no existe debe devolver el código **404** (o **410** si se eliminó definitivamente), no un 200 con el texto "página no encontrada". Es una configuración del servidor ([[Conceptos generales/01 - HTTP]]).

### Datos estructurados inventados

Los datos estructurados deben reflejar **contenido visible** en la página. Marcar información falsa o invisible puede dar lugar a sanciones.

### SEO internacional

Para sitios en varios idiomas, además de `hreflang`, conviene separar cada idioma en su propia URL (`/es/`, `/en/`) en vez de cambiar el contenido según el navegador del visitante.

> [!warning] Obsoleto / legado
> - `<meta name="keywords">` lo ignoran los principales buscadores.
> - `<meta name="revisit-after">`, `<meta name="distribution">` y `<meta name="rating">` no tienen efecto.
> - `<meta name="robots" content="index, follow">` es redundante: es el comportamiento por defecto.
> - `rel="prev"` y `rel="next"` para paginación ya no los utiliza Google para indexar.
> - Técnicas antiguas como el *keyword stuffing*, el texto oculto o los enlaces comprados sin marcar están penalizadas.
> - La autoría de Google (`rel="author"` con Google+) desapareció.
> - `<meta name="description">` repetida en todas las páginas es una mala práctica heredada: debe ser única.

---

## 9. Resumen

- El SEO ayuda a que los buscadores **rastreen, indexen y muestren** bien una página; el HTML aporta estructura, significado y presentación.
- Cada página necesita un **`<title>`** único, una **`meta description`** propia, un solo **`<h1>`**, el idioma en **`lang`** y la etiqueta **`viewport`**.
- `<link rel="canonical">` indica la URL preferida; `meta robots` y `noindex` controlan la indexación; `robots.txt` controla el rastreo.
- `hreflang` relaciona versiones en otros idiomas; Open Graph y Twitter controlan cómo se ve la página al compartirla.
- Los **datos estructurados** (JSON-LD con schema.org) describen el contenido y pueden dar resultados enriquecidos, sin garantía.
- Los enlaces deben ser rastreables y descriptivos; `rel="nofollow"`, `"sponsored"` y `"ugc"` matizan los que no se respaldan.
- La velocidad y la estabilidad visual (LCP, INP, CLS) forman parte de la experiencia que se valora.
- Las metaetiquetas no sustituyen a un contenido **útil y original**, y ninguna técnica garantiza una posición concreta.