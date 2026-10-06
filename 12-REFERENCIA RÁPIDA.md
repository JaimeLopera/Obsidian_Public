# 12 - Referencia rápida

> [!info] ¿Qué es?
> Chuleta de consulta rápida de HTML: las etiquetas, atributos y patrones más habituales, agrupados por tema y con enlace a la nota donde se explican en detalle. Está pensada para **encontrar algo en segundos**, no para aprenderlo desde cero.

---

## 1. Antes de empezar

- Esta nota **resume**; no explica. Si algo no te suena, ve a la nota enlazada de cada bloque.
- Convenciones usadas:
  - `<etiqueta>` → elemento con cierre
  - `<etiqueta>` *(vacía)* → elemento sin contenido ni cierre (`<img>`, `<br>`, `<input>`…)
  - **Bloque** = ocupa todo el ancho · **En línea** = fluye dentro del texto

---

## 2. Concepto fundamental

HTML describe **la estructura y el significado** del contenido mediante **elementos**: etiqueta de apertura + contenido + etiqueta de cierre, con **atributos** que los configuran.

```html
<a href="https://ejemplo.com" target="_blank">Enlace</a>
<!-- etiqueta · atributo="valor" · contenido · cierre -->
```

Ampliado en [[01 - Fundamentos]].

---

## 3. Sintaxis / estructura

### Esqueleto base

```html
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Título de la página</title>
  <link rel="stylesheet" href="estilos.css">
  <script src="app.js" defer></script>
</head>
<body>
  <header></header>
  <main></main>
  <footer></footer>
</body>
</html>
```

### Etiquetas del `<head>`

| Etiqueta | Función |
|---|---|
| `<title>` | Título de la pestaña y de los resultados de búsqueda |
| `<meta charset="UTF-8">` | Codificación de caracteres |
| `<meta name="viewport">` | Adaptación a móviles |
| `<meta name="description">` | Descripción para buscadores |
| `<link rel="stylesheet">` | Enlaza una hoja de estilos |
| `<link rel="icon">` | Favicon |
| `<script defer>` | Carga JS sin bloquear el HTML |
| `<style>` | CSS interno |

---

## 4. Elementos / propiedades / características

### 4.1 Estructura y semántica → [[02 - Estructura y semántica]]

| Etiqueta | Uso |
|---|---|
| `<header>` | Cabecera de página o sección |
| `<nav>` | Bloque de navegación principal |
| `<main>` | Contenido principal (uno por página) |
| `<section>` | Bloque temático con encabezado |
| `<article>` | Contenido autónomo y reutilizable |
| `<aside>` | Contenido complementario (barra lateral) |
| `<footer>` | Pie de página o sección |
| `<figure>` / `<figcaption>` | Contenido ilustrativo con su leyenda |
| `<div>` / `<span>` | Contenedores sin significado (bloque / en línea) |

### 4.2 Texto y enlaces → [[03 - Texto y enlaces]]

| Etiqueta | Uso |
|---|---|
| `<h1>` … `<h6>` | Encabezados (jerarquía) |
| `<p>` | Párrafo |
| `<br>` *(vacía)* | Salto de línea |
| `<hr>` *(vacía)* | Separación temática |
| `<strong>` / `<em>` | Importancia / énfasis |
| `<mark>` | Texto resaltado |
| `<small>` | Letra pequeña, aclaraciones |
| `<code>` / `<pre>` | Código en línea / bloque preformateado |
| `<blockquote>` / `<q>` | Cita larga / cita corta |
| `<abbr title="">` | Abreviatura |
| `<time datetime="">` | Fecha u hora legible por máquinas |
| `<a href="">` | Enlace |

**Tipos de `href`:**

| Valor | Resultado |
|---|---|
| `"https://sitio.com"` | Enlace externo |
| `"/pagina.html"` | Ruta relativa a la raíz |
| `"#seccion"` | Ancla dentro de la misma página |
| `"mailto:correo@dominio.com"` | Abre el cliente de correo |
| `"tel:+34600000000"` | Inicia una llamada |

### 4.3 Imágenes y multimedia → [[04 - Imágenes y multimedia]]

```html
<img src="foto.jpg" alt="Descripción" width="600" height="400" loading="lazy">

<picture>
  <source srcset="foto.avif" type="image/avif">
  <source srcset="foto.webp" type="image/webp">
  <img src="foto.jpg" alt="Descripción">
</picture>

<video src="video.mp4" controls poster="portada.jpg"></video>
<audio src="audio.mp3" controls></audio>
<iframe src="https://ejemplo.com" title="Descripción"></iframe>
```

### 4.4 Listas y tablas → [[05 - Listas y tablas]]

| Etiqueta | Uso |
|---|---|
| `<ul>` + `<li>` | Lista sin orden |
| `<ol>` + `<li>` | Lista ordenada (`start`, `reversed`, `type`) |
| `<dl>` + `<dt>` + `<dd>` | Lista de descripciones (término / definición) |
| `<table>` | Tabla |
| `<caption>` | Título de la tabla |
| `<thead>` / `<tbody>` / `<tfoot>` | Cabecera, cuerpo y pie |
| `<tr>` | Fila |
| `<th>` / `<td>` | Celda de encabezado / de datos |

**Atributos de tabla:** `colspan` (fusiona columnas) · `rowspan` (fusiona filas) · `scope="col|row"` (accesibilidad).

### 4.5 Formularios → [[06 - Formularios]]

| Etiqueta | Uso |
|---|---|
| `<form action="" method="">` | Contenedor del formulario |
| `<label for="">` | Texto asociado a un campo |
| `<input>` *(vacía)* | Campo de entrada (según `type`) |
| `<textarea>` | Texto multilínea |
| `<select>` + `<option>` | Lista desplegable |
| `<optgroup>` | Agrupa opciones |
| `<button>` | Botón |
| `<fieldset>` + `<legend>` | Agrupa campos con título |
| `<datalist>` | Sugerencias para un `input` |

**Valores de `type` en `<input>`:**

| Categoría | Valores |
|---|---|
| Texto | `text` · `password` · `email` · `url` · `tel` · `search` |
| Número | `number` · `range` |
| Fecha y hora | `date` · `time` · `datetime-local` · `month` · `week` |
| Selección | `checkbox` · `radio` · `color` · `file` |
| Acción | `submit` · `reset` · `button` |
| Oculto | `hidden` |

**Atributos de formulario habituales:** `name` · `value` · `placeholder` · `required` · `disabled` · `readonly` · `min` · `max` · `step` · `minlength` · `maxlength` · `pattern` · `autocomplete` · `autofocus` · `multiple`.

### 4.6 Atributos → [[07 - Atributos]]

**Globales** (valen en cualquier elemento):

| Atributo | Función |
|---|---|
| `id` | Identificador único |
| `class` | Una o varias clases |
| `style` | CSS en línea (evitar) |
| `title` | Texto de ayuda al pasar el ratón |
| `lang` | Idioma del elemento |
| `hidden` | Oculta el elemento |
| `tabindex` | Orden/posibilidad de foco con teclado |
| `data-*` | Datos personalizados para JS |
| `contenteditable` | Hace editable el contenido |

**Específicos frecuentes:**

| Atributo | Elementos | Función |
|---|---|---|
| `href` | `a`, `link` | Destino |
| `src` | `img`, `script`, `video`, `audio`, `iframe` | Origen del recurso |
| `alt` | `img` | Texto alternativo |
| `target` | `a`, `form` | Dónde se abre (`_blank`, `_self`) |
| `rel` | `a`, `link` | Relación con el recurso |
| `type` | `input`, `button`, `script`, `source` | Tipo |
| `for` | `label` | Asocia con el `id` del campo |
| `action` / `method` | `form` | Destino y método (`get` / `post`) |
| `download` | `a` | Descarga en vez de navegar |

### 4.7 Accesibilidad → [[08 - Accesibilidad]]

| Recurso | Cuándo usarlo |
|---|---|
| `alt` | Siempre en `<img>` (vacío si es decorativa) |
| `<label>` | Siempre en campos de formulario |
| `lang` | Siempre en `<html>` |
| `aria-label` | Nombre accesible cuando no hay texto visible |
| `aria-labelledby` | Nombre tomado de otro elemento |
| `aria-describedby` | Descripción adicional (ayudas, errores) |
| `aria-hidden="true"` | Oculta a lectores de pantalla (decorativo) |
| `aria-live` | Anuncia cambios dinámicos |
| `role` | Solo si no existe etiqueta nativa equivalente |
| `tabindex="0"` / `"-1"` | Incluir / sacar del orden de tabulación |

### 4.8 SEO → [[09 - SEO]]

| Elemento | Recomendación |
|---|---|
| `<title>` | Único y descriptivo por página |
| `<meta name="description">` | Resumen claro de la página |
| `<h1>` | Uno solo, con el tema principal |
| `<link rel="canonical">` | URL preferida del contenido |
| `<meta name="robots">` | Controla indexación (`index`, `noindex`, `nofollow`) |
| Open Graph (`og:title`, `og:image`…) | Vista previa al compartir en redes |
| `alt` en imágenes | Contexto para buscadores |

### 4.9 APIs HTML → [[10 - APIs HTML]]

| API / elemento | Para qué sirve |
|---|---|
| `<canvas>` | Dibujo 2D mediante JS |
| `localStorage` / `sessionStorage` | Guardar datos en el navegador |
| Geolocation | Obtener la ubicación (con permiso) |
| Drag and Drop (`draggable`) | Arrastrar y soltar |
| History | Manipular el historial de navegación |
| `<dialog>` | Ventana modal/no modal nativa |
| `<details>` / `<summary>` | Contenido desplegable |

---

## 5. Ejemplos prácticos

### Ejemplo básico: página mínima

```html
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Mi página</title>
</head>
<body>
  <h1>Hola, mundo</h1>
  <p>Mi primera página.</p>
</body>
</html>
```

### Ejemplo habitual: tarjeta con imagen, texto y enlace

```html
<article class="tarjeta">
  <img src="producto.jpg" alt="Auriculares inalámbricos negros" width="300" height="200" loading="lazy">
  <h2>Auriculares</h2>
  <p>Cancelación de ruido y 30 horas de batería.</p>
  <a href="/producto/auriculares">Ver más</a>
</article>
```

### Ejemplo completo: formulario de contacto

```html
<form action="/contacto" method="post">
  <fieldset>
    <legend>Contacto</legend>

    <label for="nombre">Nombre</label>
    <input type="text" id="nombre" name="nombre" autocomplete="name" required>

    <label for="email">Correo</label>
    <input type="email" id="email" name="email" autocomplete="email" required>

    <label for="asunto">Asunto</label>
    <select id="asunto" name="asunto">
      <option value="consulta">Consulta</option>
      <option value="soporte">Soporte</option>
    </select>

    <label for="mensaje">Mensaje</label>
    <textarea id="mensaje" name="mensaje" rows="5" required></textarea>

    <label>
      <input type="checkbox" name="acepto" required>
      Acepto la política de privacidad
    </label>
  </fieldset>

  <button type="submit">Enviar</button>
</form>
```

---

## 6. Buenas prácticas

Versión resumida de [[11 - Buenas prácticas]]:

- `DOCTYPE`, `lang`, `charset`, `viewport` y `title` en todas las páginas
- Etiqueta semántica antes que `div`
- Un solo `<h1>` y jerarquía de encabezados sin saltos
- `alt` en imágenes y `label` en campos de formulario
- `<button>` para acciones, `<a>` para navegación
- CSS y JS en archivos externos; sin `style` ni `onclick` en línea
- IDs únicos, minúsculas, comillas dobles e indentación coherente
- Validar en W3C y probar con teclado

---

## 7. Diferencias importantes

| Comparación | Diferencia clave |
|---|---|
| `<div>` vs `<span>` | Bloque vs en línea |
| `<section>` vs `<article>` | Bloque temático vs contenido autónomo |
| `<ul>` vs `<ol>` | Sin orden vs con orden |
| `<th>` vs `<td>` | Celda de encabezado vs de datos |
| `<strong>` vs `<b>` | Importancia semántica vs solo estilo |
| `<em>` vs `<i>` | Énfasis semántico vs solo estilo |
| `<button>` vs `<a>` | Acción vs navegación |
| `get` vs `post` | Datos en la URL vs en el cuerpo de la petición |
| `id` vs `class` | Único vs reutilizable |
| `localStorage` vs `sessionStorage` | Persiste vs se borra al cerrar la pestaña |
| `defer` vs `async` | Ejecuta tras parsear, en orden vs ejecuta en cuanto descarga, sin orden |

---

## 8. Casos especiales

### Elementos vacíos (sin cierre)

`<img>` · `<br>` · `<hr>` · `<input>` · `<meta>` · `<link>` · `<source>`

### Atributos booleanos (basta el nombre)

`required` · `disabled` · `checked` · `readonly` · `hidden` · `autofocus` · `multiple` · `controls` · `defer`

### Entidades HTML más usadas

| Carácter | Entidad |
|---|---|
| `<` | `&lt;` |
| `>` | `&gt;` |
| `&` | `&amp;` |
| `"` | `&quot;` |
| Espacio que no se rompe | `&nbsp;` |
| `©` | `&copy;` |

### Comentarios

```html
<!-- Esto no se muestra en la página -->
```

> [!warning] Obsoleto / legado
> Evita `<font>`, `<center>`, `<big>`, `<strike>`, `<frame>`, `<marquee>` y atributos de presentación como `align` o `bgcolor`. Usa CSS en su lugar (ver [[11 - Buenas prácticas]]).

---

## 9. Resumen

- **HTML = estructura y significado**; el aspecto lo da CSS y el comportamiento, JS.
- Todo documento necesita `DOCTYPE`, `lang`, `charset`, `viewport` y `title`.
- Elige la etiqueta **por lo que significa**: semántica antes que `div`.
- Imágenes con `alt`, campos con `label`, botones con `type`.
- Los atributos globales (`id`, `class`, `lang`, `data-*`) valen en cualquier elemento.
- Accesibilidad y SEO empiezan con un buen HTML, no con parches posteriores.
- Ante la duda, consulta la nota enlazada de cada bloque.