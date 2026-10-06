# HTML - Imágenes y multimedia

> [!info] ¿Qué es?
> HTML permite incrustar **recursos multimedia** dentro de una página: imágenes (`<img>`, `<picture>`), audio (`<audio>`), vídeo (`<video>`) y contenido externo (`<iframe>`). Estos recursos no se "escriben" en el HTML, sino que se **enlazan** desde un archivo externo o desde otro sitio, y el navegador los descarga y los muestra en su lugar.

---

## 1. Antes de empezar

Conviene dominar antes:

- La sintaxis de etiquetas y atributos, y los elementos vacíos: [[HTML/01 - Fundamentos]].
- Las rutas relativas y absolutas, que se usan igual en `src` que en `href`: [[HTML/03 - Texto y enlaces]].

> [!tip] Organiza los recursos
> Guarda los archivos multimedia en carpetas propias (`img/`, `audio/`, `video/`) y usa nombres en minúsculas, sin espacios ni tildes (`logo-principal.png`). Así las rutas son más fáciles de escribir y de mantener.

---

## 2. Concepto fundamental

Un elemento multimedia tiene dos partes:

- **La etiqueta**, que indica el tipo de recurso y cómo mostrarlo.
- **El recurso**, un archivo aparte al que apunta el atributo `src` (o `srcset`).

El navegador lee el HTML, descubre cada recurso y lo pide al servidor. Por eso las imágenes y los vídeos son lo que más **pesa** en una página, y de cómo se incluyan dependen el rendimiento, la accesibilidad y el diseño.

Tres ideas guían todo el tema:

- **Alternativa en texto**: toda imagen con significado necesita una descripción para quien no puede verla (`alt`).
- **Reservar espacio**: indicar el tamaño de la imagen evita que la página "salte" al cargarse.
- **Elegir el recurso adecuado**: formato, tamaño y resolución según el dispositivo.

> [!note] `<img>` frente a imagen de fondo en CSS
> Si la imagen **aporta contenido** (una foto de producto, un gráfico), va en HTML con `<img>`. Si es **decorativa** (un patrón, un degradado), va en CSS con `background-image` ([[CSS/05 - Colores y fondos]]).

---

## 3. Sintaxis / estructura

### 3.1 Imagen básica

```html
<img src="img/paisaje.jpg" alt="Montañas nevadas al amanecer" width="800" height="600">
```

`<img>` es un elemento **vacío**: no lleva etiqueta de cierre.

### 3.2 Imagen con leyenda

```html
<figure>
  <img src="img/grafico.png" alt="Gráfico de barras con las ventas por trimestre">
  <figcaption>Ventas por trimestre, 2026</figcaption>
</figure>
```

### 3.3 Imagen adaptable con `srcset` y `sizes`

```html
<img
  src="img/foto-800.jpg"
  srcset="img/foto-400.jpg 400w, img/foto-800.jpg 800w, img/foto-1600.jpg 1600w"
  sizes="(max-width: 600px) 100vw, 800px"
  alt="Plaza mayor llena de gente"
  width="800" height="533">
```

### 3.4 Formatos alternativos con `<picture>`

```html
<picture>
  <source srcset="img/foto.avif" type="image/avif">
  <source srcset="img/foto.webp" type="image/webp">
  <img src="img/foto.jpg" alt="Atardecer en la playa" width="800" height="533">
</picture>
```

### 3.5 Audio

```html
<audio controls>
  <source src="audio/podcast.mp3" type="audio/mpeg">
  <source src="audio/podcast.ogg" type="audio/ogg">
  Tu navegador no admite el elemento de audio.
</audio>
```

### 3.6 Vídeo

```html
<video controls width="640" height="360" poster="img/portada.jpg">
  <source src="video/clase.mp4" type="video/mp4">
  <source src="video/clase.webm" type="video/webm">
  <track kind="subtitles" src="video/clase-es.vtt" srclang="es" label="Español" default>
  Tu navegador no admite el elemento de vídeo.
</video>
```

### 3.7 Contenido incrustado con `<iframe>`

```html
<iframe
  src="https://www.youtube.com/embed/ID_DEL_VIDEO"
  title="Vídeo: introducción a HTML"
  width="560" height="315"
  loading="lazy"
  allowfullscreen></iframe>
```

---

## 4. Elementos / propiedades / características

### 4.1 Elementos multimedia

| Elemento | Función |
|----------|---------|
| `<img>` | Imagen (elemento vacío) |
| `<picture>` | Contenedor para ofrecer varias versiones de una imagen |
| `<source>` | Recurso alternativo dentro de `<picture>`, `<audio>` o `<video>` |
| `<figure>` y `<figcaption>` | Contenido autónomo con su leyenda |
| `<audio>` | Reproductor de sonido |
| `<video>` | Reproductor de vídeo |
| `<track>` | Subtítulos, descripciones o capítulos para audio y vídeo |
| `<iframe>` | Incrusta otra página o servicio dentro de la tuya |
| `<canvas>` | Lienzo para dibujar con JavaScript |
| `<svg>` | Gráficos vectoriales escritos directamente en el HTML |

### 4.2 Atributos de `<img>`

| Atributo | Función | Notas |
|----------|---------|-------|
| `src` | Ruta del archivo de imagen | Obligatorio |
| `alt` | Texto alternativo | Obligatorio; vacío (`alt=""`) si es decorativa |
| `width` y `height` | Dimensiones en píxeles | Permiten reservar espacio antes de cargar |
| `loading` | `lazy` o `eager` | `lazy` retrasa la carga hasta que la imagen está cerca de verse |
| `decoding` | `async`, `sync` o `auto` | `async` evita bloquear el pintado de la página |
| `srcset` | Lista de versiones con su ancho o densidad | Para imágenes adaptables |
| `sizes` | Ancho que ocupará la imagen en cada situación | Se usa junto a `srcset` con descriptores `w` |
| `fetchpriority` | `high`, `low` o `auto` | `high` para la imagen principal visible al cargar |

### 4.3 Atributos de `<audio>` y `<video>`

| Atributo | Función |
|----------|---------|
| `controls` | Muestra los controles del reproductor |
| `autoplay` | Empieza a reproducir al cargar (casi siempre exige `muted`) |
| `muted` | Silencia el sonido por defecto |
| `loop` | Reproduce en bucle |
| `preload` | `none`, `metadata` o `auto`: cuánto descargar antes de pulsar reproducir |
| `poster` | Imagen de portada del vídeo (solo `<video>`) |
| `playsinline` | Reproduce dentro de la página en iOS en lugar de pantalla completa (solo `<video>`) |
| `width` y `height` | Dimensiones del reproductor (solo `<video>`) |

### 4.4 Atributos de `<track>`

| Atributo | Función |
|----------|---------|
| `kind` | `subtitles`, `captions`, `descriptions`, `chapters` o `metadata` |
| `src` | Archivo de texto `.vtt` |
| `srclang` | Idioma de la pista (`es`, `en`...) |
| `label` | Nombre que se muestra en el menú del reproductor |
| `default` | Activa esta pista por defecto |

### 4.5 Atributos de `<iframe>`

| Atributo | Función |
|----------|---------|
| `src` | Dirección del contenido que se incrusta |
| `title` | Descripción del contenido (necesaria para accesibilidad) |
| `width` y `height` | Dimensiones |
| `loading` | `lazy` para cargarlo solo cuando está cerca de verse |
| `allowfullscreen` | Permite pantalla completa |
| `allow` | Permisos del contenido (cámara, geolocalización, reproducción automática...) |
| `sandbox` | Restringe lo que puede hacer el contenido incrustado |
| `referrerpolicy` | Controla qué información de origen se envía |

### 4.6 Formatos de imagen

| Formato | Tipo | Cuándo usarlo |
|---------|------|---------------|
| **JPEG** (`.jpg`) | Con pérdida | Fotografías |
| **PNG** | Sin pérdida | Capturas, imágenes con transparencia y pocos colores |
| **WebP** | Con o sin pérdida | Alternativa moderna a JPEG y PNG, más ligera |
| **AVIF** | Con o sin pérdida | Muy buena compresión; el más ligero de los habituales |
| **SVG** | Vectorial | Logos, iconos e ilustraciones que deben verse nítidos a cualquier tamaño |
| **GIF** | Con paleta limitada | Animaciones sencillas (suele ser mejor un vídeo corto) |

### 4.7 Formatos de audio y vídeo

| Tipo | Formatos habituales | Valor de `type` |
|------|---------------------|-----------------|
| Audio | MP3, OGG, WAV | `audio/mpeg`, `audio/ogg`, `audio/wav` |
| Vídeo | MP4 (H.264), WebM | `video/mp4`, `video/webm` |

MP4 es el formato más compatible, y por eso suele ir como opción principal.

### 4.8 Cómo escribir un buen `alt`

| Tipo de imagen | `alt` recomendado |
|----------------|-------------------|
| Informativa (foto con contenido) | Describe lo esencial: `alt="Perro labrador corriendo en la playa"` |
| Funcional (imagen dentro de un enlace o botón) | Describe la **acción o el destino**: `alt="Ir al inicio"` |
| Decorativa | Vacío: `alt=""` (el lector de pantalla la omite) |
| Gráfico o diagrama | Resume la conclusión y detalla los datos en el texto cercano |

---

## 5. Ejemplos prácticos

### Ejemplo básico

Una imagen con su descripción y dimensiones:

```html
<img src="img/logo.png" alt="Logotipo de Academia Web" width="200" height="60">
```

### Ejemplo habitual

Imagen principal optimizada, con leyenda y una imagen que funciona como enlace:

```html
<main>
  <figure>
    <picture>
      <source srcset="img/portada.avif" type="image/avif">
      <source srcset="img/portada.webp" type="image/webp">
      <img
        src="img/portada.jpg"
        alt="Estudiante programando en un portátil"
        width="1200" height="800"
        fetchpriority="high">
    </picture>
    <figcaption>Aprender HTML paso a paso</figcaption>
  </figure>

  <a href="index.html">
    <img src="img/logo.svg" alt="Ir al inicio" width="120" height="40">
  </a>

  <img src="img/galeria-1.jpg" alt="Aula con ordenadores" width="600" height="400" loading="lazy">
</main>
```

La imagen principal **no** lleva `loading="lazy"` porque se ve nada más cargar la página. Las que quedan más abajo sí.

### Ejemplo completo

Página con vídeo con subtítulos, audio y contenido incrustado:

```html
<main>
  <h1>Clase de introducción</h1>

  <section>
    <h2>Vídeo de la clase</h2>
    <video controls width="640" height="360" poster="img/portada-clase.jpg" preload="metadata">
      <source src="video/clase.webm" type="video/webm">
      <source src="video/clase.mp4" type="video/mp4">
      <track kind="subtitles" src="video/clase-es.vtt" srclang="es" label="Español" default>
      <track kind="subtitles" src="video/clase-en.vtt" srclang="en" label="English">
      <p>Tu navegador no admite vídeo. <a href="video/clase.mp4">Descárgalo aquí</a>.</p>
    </video>
  </section>

  <section>
    <h2>Resumen en audio</h2>
    <audio controls preload="none">
      <source src="audio/resumen.mp3" type="audio/mpeg">
      <p>Tu navegador no admite audio. <a href="audio/resumen.mp3">Descarga el resumen</a>.</p>
    </audio>
  </section>

  <section>
    <h2>Dónde estamos</h2>
    <iframe
      src="https://www.openstreetmap.org/export/embed.html"
      title="Mapa con la ubicación de la academia"
      width="600" height="400"
      loading="lazy"></iframe>
  </section>
</main>
```

Los subtítulos y los textos alternativos conectan con [[HTML/08 - Accesibilidad]].

---


## 6. Buenas prácticas

- Escribir un `alt` **descriptivo** en toda imagen informativa y `alt=""` en las decorativas.
- Indicar `width` y `height` en todas las imágenes y adaptarlas con CSS ([[CSS/10 - Responsive Design]]).
- Usar `loading="lazy"` en las imágenes que no se ven al abrir la página.
- Optimizar el peso: redimensionar, comprimir y preferir WebP o AVIF cuando sea posible.
- Ofrecer varias versiones con `srcset`, `sizes` o `<picture>` según el dispositivo.
- Usar **SVG** para logos e iconos.
- Envolver con `<figure>` y `<figcaption>` las imágenes que llevan leyenda.
- Incluir `controls` en `<audio>` y `<video>`, y subtítulos con `<track>` en vídeo con voz.
- Poner un texto o enlace de respaldo dentro de `<audio>` y `<video>`.
- Añadir `title` a todos los `<iframe>` y `loading="lazy"` a los que no están a la vista.
- Incrustar contenido de terceros solo si es necesario y limitar sus permisos con `sandbox` o `allow`.
- No indicar información esencial solo mediante una imagen: debe estar también en texto.

Más criterios de calidad en [[HTML/11 - Buenas prácticas]].

---

## 7. Diferencias importantes

| Comparación | Diferencia |
|-------------|------------|
| **`<img>` vs `background-image`** | `<img>` es contenido con significado y `alt`; el fondo en CSS es decoración |
| **`srcset` vs `<picture>`** | `srcset` ofrece versiones de la **misma** imagen en distintos tamaños; `<picture>` permite cambiar de formato o recortar según condiciones |
| **`alt` vs `title`** | `alt` es el texto alternativo y es imprescindible; `title` es información adicional (tooltip) y no sustituye al `alt` |
| **`<figcaption>` vs `alt`** | `figcaption` es una leyenda visible para todos; `alt` describe la imagen para quien no la ve |
| **`<audio>`/`<video>` vs `<iframe>`** | `<audio>` y `<video>` reproducen archivos propios; `<iframe>` incrusta un servicio externo (YouTube, mapas) |
| **SVG vs PNG/JPEG** | SVG es vectorial y se escala sin perder calidad; PNG y JPEG son de píxeles |
| **`preload="none"` vs `"metadata"`** | `none` no descarga nada hasta reproducir; `metadata` descarga solo la duración y las dimensiones |
| **`<track kind="subtitles">` vs `"captions"`** | `subtitles` traducen el diálogo; `captions` incluyen también sonidos y efectos para personas sordas |

---

## 8. Casos especiales

### Imágenes dentro de enlaces

Si una imagen es el único contenido de un `<a>`, su `alt` actúa como texto del enlace y debe describir el destino, no la imagen ([[HTML/03 - Texto y enlaces]]).

### Imágenes que cambian de recorte con `media`

`<picture>` permite elegir otro recorte según el ancho de pantalla:

```html
<picture>
  <source media="(max-width: 600px)" srcset="img/portada-movil.jpg">
  <img src="img/portada-escritorio.jpg" alt="Equipo de trabajo en la oficina" width="1200" height="600">
</picture>
```

### Mapas de imagen

`<map>` y `<area>` definen zonas pulsables dentro de una imagen (`usemap="#nombre"`). Funcionan, pero son poco flexibles y responsive. Suele ser preferible usar enlaces posicionados con CSS o SVG con enlaces.

### SVG en línea frente a SVG como imagen

Un SVG cargado con `<img src="icono.svg">` no se puede modificar con CSS desde la página. Escrito directamente como `<svg>` dentro del HTML, sí se puede estilar y animar, pero hay que añadirle accesibilidad (`role="img"` y `<title>`).

### Favicon

El icono de la pestaña no se pone en el `<body>`, sino en el `<head>`:

```html
<link rel="icon" href="img/favicon.svg" type="image/svg+xml">
```

### Reproducción bloqueada

Si un vídeo con `autoplay` no arranca, normalmente es por la política del navegador sobre sonido automático. Suele resolverse con `muted`, o dejando que la persona pulse reproducir.

### `<iframe>` y seguridad

Un `<iframe>` ejecuta contenido que no controlas. Algunos sitios impiden ser incrustados. Para contenido no fiable se usa `sandbox`, y se cargan solo servicios de confianza ([[Conceptos generales/05 - Seguridad web]]).

> [!warning] Obsoleto / legado
> - `<embed>`, `<object>` y `<applet>` se usaban para Flash y Java, hoy no soportados. Para vídeo y audio se usan `<video>` y `<audio>`.
> - `<bgsound>` (música de fondo) y `<marquee>` están obsoletos.
> - Los atributos `align`, `border`, `hspace` y `vspace` en `<img>` están obsoletos: se usa **CSS** ([[CSS/03 - Box Model]]).
> - `longdesc` en `<img>` no tiene soporte real: la descripción larga se pone en el texto cercano o en una leyenda.
> - Los GIF animados pesados se sustituyen por `<video autoplay muted loop playsinline>`, que pesa mucho menos.

---

## 9. Resumen

- `<img>` incrusta imágenes con `src` (archivo) y `alt` (texto alternativo, obligatorio); es un elemento **vacío**.
- Indicar `width` y `height` evita saltos de diseño, y `loading="lazy"` retrasa las imágenes que no se ven al cargar.
- `srcset`, `sizes` y `<picture>` ofrecen versiones según dispositivo, resolución o formato (AVIF, WebP, JPEG).
- Formatos: JPEG para fotos, PNG para transparencia, SVG para vectores, WebP y AVIF como opciones modernas ligeras.
- `<figure>` y `<figcaption>` agrupan una imagen con su leyenda.
- `<audio>` y `<video>` llevan `controls`, varias fuentes con `<source>`, subtítulos con `<track>` y contenido de respaldo.
- `<iframe>` incrusta contenido externo y necesita `title`, y `loading="lazy"` si no está a la vista.
- Optimizar el peso, describir el contenido y no depender solo de imágenes para la información esencial.