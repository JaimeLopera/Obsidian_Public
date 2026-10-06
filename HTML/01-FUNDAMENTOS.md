# HTML - Fundamentos

> [!info] ¿Qué es?
> **HTML** (*HyperText Markup Language*) es el lenguaje de marcado con el que se escribe el contenido de una página web. Mediante **etiquetas** indica al navegador qué es cada parte del contenido (un título, un párrafo, un enlace, una imagen) y cómo se organiza. Un documento HTML es un archivo de texto con extensión `.html` que el navegador lee y convierte en la página que ves.

---

## 1. Antes de empezar

Para seguir esta nota necesitas:

- Un editor de código (por ejemplo, VS Code).
- Un navegador moderno.
- Saber crear un archivo de texto con extensión `.html` y abrirlo en el navegador (doble clic o arrastrándolo a una ventana).

> [!tip] Primer contacto
> Crea un archivo `index.html`, escribe cualquier ejemplo de esta nota y ábrelo en el navegador. Con `F12` accedes a las herramientas de desarrollo, donde puedes inspeccionar cómo el navegador interpreta tu código.

Conviene conocer la idea general de cómo llega una página al navegador: [[Conceptos generales/06 - Cómo funciona internet]] y [[Conceptos generales/01 - HTTP]].

---

## 2. Concepto fundamental

HTML funciona con tres ideas que conviene distinguir desde el principio:

- **Etiqueta**: la marca escrita entre `<` y `>` (por ejemplo, `<p>` o `</p>`).
- **Elemento**: el conjunto formado por la etiqueta de apertura, el contenido y la etiqueta de cierre.
- **Atributo**: información adicional que se añade dentro de la etiqueta de apertura (por ejemplo, `class="intro"`).

Cuando el navegador recibe un archivo HTML, lo **analiza** (*parsea*) y construye con él un **árbol de elementos** en memoria llamado DOM. Ese árbol es lo que se dibuja en pantalla, lo que CSS estiliza y lo que JavaScript manipula (ver [[JavaScript/09 - DOM]]).

> [!note] HTML no es un lenguaje de programación
> HTML **describe** contenido, no ejecuta lógica: no tiene variables, condiciones ni bucles. Por eso se le llama lenguaje de **marcado**.

El estándar lo mantiene la organización WHATWG como un estándar vivo (*HTML Living Standard*). El término "HTML5" se sigue usando de forma coloquial para referirse al HTML moderno.

---

## 3. Sintaxis / estructura

### 3.1 Anatomía de un elemento

```html
<p class="intro">Hola, mundo</p>
```

| Parte | En el ejemplo | Función |
|-------|---------------|---------|
| Etiqueta de apertura | `<p class="intro">` | Marca el inicio del elemento |
| Atributo | `class="intro"` | Añade información al elemento (nombre + valor) |
| Contenido | `Hola, mundo` | Lo que contiene el elemento |
| Etiqueta de cierre | `</p>` | Marca el final (lleva una `/` antes del nombre) |
| Elemento | todo el conjunto | Apertura + contenido + cierre |

### 3.2 Esqueleto de un documento

Todo documento HTML parte de esta estructura:

```html
<!DOCTYPE html>
<html lang="es">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Título de la página</title>
  </head>
  <body>
    <h1>Contenido visible</h1>
  </body>
</html>
```

### 3.3 Anidación

Los elementos pueden contener a otros elementos. La regla es que **el último elemento que se abre es el primero que se cierra**:

```html
<p>Texto con <strong>una parte importante</strong>.</p>
```

### 3.4 Elementos vacíos

Algunos elementos no tienen contenido ni etiqueta de cierre:

```html
<br>
<hr>
<img src="foto.jpg" alt="Descripción">
<input type="text">
<meta charset="UTF-8">
```

### 3.5 Comentarios

Los comentarios no se muestran en la página:

```html
<!-- Esto es un comentario -->
```

### 3.6 Entidades de caracteres

Para escribir caracteres reservados o especiales se usan entidades, que empiezan por `&` y terminan en `;`:

| Entidad | Resultado | Uso |
|---------|-----------|-----|
| `&lt;` | `<` | Evitar que el navegador lo tome como etiqueta |
| `&gt;` | `>` | Símbolo "mayor que" |
| `&amp;` | `&` | El propio símbolo `&` |
| `&quot;` | `"` | Comillas dobles dentro de un atributo |
| `&nbsp;` | (espacio) | Espacio que no se colapsa ni permite salto de línea |
| `&copy;` | © | Símbolo de copyright |

---

## 4. Elementos / propiedades / características

### 4.1 Elementos del esqueleto

| Elemento | Obligatorio | Función |
|----------|-------------|---------|
| `<!DOCTYPE html>` | Sí | Indica al navegador que el documento es HTML moderno y activa el modo estándar |
| `<html>` | Sí | Elemento raíz; envuelve todo el documento |
| `<head>` | Sí | Información sobre el documento que **no se muestra** en la página |
| `<meta charset="UTF-8">` | Recomendado | Codificación de caracteres (tildes, ñ, emojis) |
| `<meta name="viewport" ...>` | Recomendado | Adapta la página a pantallas de móvil |
| `<title>` | Sí | Título que aparece en la pestaña del navegador |
| `<body>` | Sí | Todo el contenido **visible** de la página |

### 4.2 Atributo `lang`

`<html lang="es">` declara el idioma principal del documento. Lo usan los lectores de pantalla para pronunciar bien el texto, los navegadores para ofrecer traducción y los buscadores para clasificar la página.

### 4.3 Características del lenguaje

- **No distingue mayúsculas de minúsculas** en etiquetas y atributos (`<P>` funciona igual que `<p>`), pero la convención es escribir todo en minúsculas.
- **Los espacios en blanco se colapsan**: varios espacios, tabuladores o saltos de línea seguidos se muestran como un único espacio.
- **Es tolerante a errores**: el navegador intenta corregir el código mal escrito en lugar de detenerse, lo que a veces oculta fallos.
- **Los valores de atributo** se escriben entre comillas (`class="intro"`).
- **Los archivos** suelen llamarse en minúsculas, sin espacios ni tildes. La página principal de un sitio se llama `index.html` por convención.

---

## 5. Ejemplos prácticos

### Ejemplo básico

El documento válido más pequeño con contenido:

```html
<!DOCTYPE html>
<html lang="es">
  <head>
    <meta charset="UTF-8">
    <title>Mi primera página</title>
  </head>
  <body>
    <h1>Hola, mundo</h1>
    <p>Esta es mi primera página en HTML.</p>
  </body>
</html>
```

### Ejemplo habitual

Esqueleto típico de un proyecto real, con hoja de estilos y script enlazados:

```html
<!DOCTYPE html>
<html lang="es">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Mi proyecto</title>
    <link rel="stylesheet" href="css/estilos.css">
    <script src="js/app.js" defer></script>
  </head>
  <body>
    <h1>Mi proyecto</h1>
    <p>Texto de ejemplo con <strong>énfasis</strong>.</p>
  </body>
</html>
```

`<link>` conecta la hoja de estilos ([[CSS/01 - Fundamentos]]) y `<script defer>` carga el JavaScript sin bloquear la lectura del HTML ([[JavaScript/01 - Fundamentos]]).

### Ejemplo completo

Reúne comentarios, anidación, elementos vacíos y entidades en un mismo documento:

```html
<!DOCTYPE html>
<html lang="es">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Guía rápida de etiquetas</title>
  </head>
  <body>
    <!-- Cabecera de la guía -->
    <h1>Guía rápida de etiquetas</h1>

    <p>
      Una etiqueta se escribe entre &lt; y &gt;, por ejemplo
      <strong>&lt;p&gt;</strong>.
    </p>

    <hr>

    <p>
      Los elementos pueden anidarse: este párrafo contiene
      <em>énfasis con <strong>importancia</strong> dentro</em>.
    </p>

    <p>&copy; 2026 - Todos los derechos reservados</p>
  </body>
</html>
```

---


## 6. Buenas prácticas

- Empezar siempre con `<!DOCTYPE html>` y declarar el idioma con `<html lang="es">`.
- Colocar `<meta charset="UTF-8">` lo primero dentro del `<head>`.
- Incluir la metaetiqueta `viewport` en todo proyecto que deba verse bien en móvil.
- Escribir un `<title>` descriptivo y único para cada página.
- Usar etiquetas y atributos en **minúsculas** y valores de atributo entre **comillas dobles**.
- **Indentar** el código (2 espacios es lo más habitual) para reflejar la anidación.
- Nombrar los archivos en minúsculas, sin espacios ni tildes (`index.html`, `sobre-nosotros.html`).
- Separar responsabilidades: HTML para el contenido, CSS para el aspecto y JavaScript para el comportamiento. No usar HTML para maquetar visualmente.
- Validar el código con el validador de W3C para detectar errores que el navegador corrige en silencio.

Más criterios de calidad en [[HTML/11 - Buenas prácticas]].

---

## 7. Diferencias importantes

| Comparación | Diferencia |
|-------------|------------|
| **Etiqueta vs elemento** | La etiqueta es solo la marca (`<p>`); el elemento incluye apertura, contenido y cierre |
| **Atributo vs contenido** | El atributo va dentro de la etiqueta de apertura y describe el elemento; el contenido va entre las etiquetas |
| **HTML vs XHTML** | XHTML era una versión estricta basada en XML (todo en minúsculas, todo cerrado). HTML moderno es más flexible y es el estándar actual |
| **`<head>` vs `<header>`** | `<head>` contiene metadatos no visibles; `<header>` es una cabecera visible dentro del `<body>` |
| **HTML vs lenguaje de programación** | HTML describe contenido; un lenguaje de programación ejecuta lógica |
| **`<title>` vs `<h1>`** | `<title>` aparece en la pestaña del navegador; `<h1>` es el título visible dentro de la página |

---

## 8. Casos especiales

### Modo quirks vs modo estándar

Sin `<!DOCTYPE html>`, el navegador activa el modo *quirks*, que imita comportamientos de navegadores antiguos. Con él, usa el modo estándar. Hoy no hay motivo para usar quirks.

### Etiquetas de cierre opcionales

El estándar permite omitir ciertas etiquetas de cierre (por ejemplo, `</p>` o `</li>`) y hasta las etiquetas `<html>`, `<head>` y `<body>`, porque el navegador las deduce. Es válido, pero **no se recomienda**: el código resulta menos claro y más propenso a errores.

### Los comentarios no se anidan

```html
<!-- Comentario <!-- otro comentario --> -->
```

El primer `-->` cierra el comentario y el resto aparece como texto en la página.

### `<meta charset>` debe estar al principio

La declaración de codificación debe aparecer en los primeros 1024 bytes del documento, por eso se coloca lo primero en el `<head>`.

> [!warning] Obsoleto / legado
> - `<font>`, `<center>`, `<big>` y `<marquee>` están obsoletos. Su sustituto es **CSS** ([[CSS/06 - Texto y fuentes]]).
> - Los `DOCTYPE` largos de HTML 4 y XHTML han sido sustituidos por `<!DOCTYPE html>`.
> - `<frameset>` y `<frame>` están obsoletos. Para incrustar contenido externo se usa `<iframe>` ([[HTML/04 - Imágenes y multimedia]]).

---


## 9. Resumen

- HTML es un lenguaje de **marcado** que describe la estructura y el significado del contenido; no es un lenguaje de programación.
- Un **elemento** se compone de etiqueta de apertura, contenido y etiqueta de cierre; los **atributos** van en la apertura.
- Todo documento parte de `<!DOCTYPE html>`, `<html lang="...">`, `<head>` (metadatos) y `<body>` (contenido visible).
- Los elementos pueden **anidarse**, y se cierra primero el último abierto.
- Los **elementos vacíos** (`<br>`, `<img>`, `<input>`, `<meta>`...) no llevan etiqueta de cierre.
- El **espacio en blanco se colapsa** y los caracteres reservados se escriben con **entidades** (`&lt;`, `&amp;`...).
- El navegador es tolerante con los errores, así que conviene **validar** el código y mantener el estilo limpio: minúsculas, comillas dobles e indentación.