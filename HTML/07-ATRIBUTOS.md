# HTML - Atributos

> [!info] ¿Qué es?
> Un **atributo** es una pieza de información adicional que se escribe dentro de la etiqueta de apertura de un elemento y que **configura o describe** su comportamiento. Tiene casi siempre la forma `nombre="valor"`. Gracias a ellos un `<a>` sabe a dónde enlazar (`href`), una imagen qué mostrar (`src`) y cualquier elemento puede identificarse (`id`), clasificarse (`class`) o hacerse accesible (`aria-label`).

---

## 1. Antes de empezar

Conviene dominar antes:

- La anatomía de un elemento (etiqueta de apertura, contenido y cierre): [[HTML/01 - Fundamentos]].
- Haber visto atributos concretos en uso: `href` y `target` en [[HTML/03 - Texto y enlaces]], `src` y `alt` en [[HTML/04 - Imágenes y multimedia]], `colspan` en [[HTML/05 - Listas y tablas]] y `name`, `required` en [[HTML/06 - Formularios]].

> [!tip] Cómo usar esta nota
> Aquí se explica **cómo funcionan los atributos en general** y se estudian los atributos **globales**, que valen para cualquier elemento. Los atributos propios de cada etiqueta están en la nota de cada tema y en [[HTML/12 - Referencia rápida]].

---

## 2. Concepto fundamental

Los atributos se dividen en dos grandes familias:

- **Atributos globales**: se pueden poner en **cualquier elemento** (`id`, `class`, `lang`, `title`, `hidden`, `tabindex`, `data-*`...).
- **Atributos específicos**: solo tienen sentido en ciertos elementos (`href` en `<a>`, `src` en `<img>`, `action` en `<form>`...).

Según el tipo de valor que admiten, se distinguen tres formas:

| Tipo | Cómo se escribe | Ejemplo |
|------|-----------------|---------|
| **Con valor libre** | `nombre="valor"` | `alt="Logotipo"`, `href="inicio.html"` |
| **Booleano** | Basta con su **presencia** | `disabled`, `required`, `hidden` |
| **Enumerado** | `nombre="uno de los valores permitidos"` | `contenteditable="true"`, `dir="rtl"` |

Cuando el navegador lee el HTML, cada atributo pasa a formar parte del elemento en el árbol DOM y desde JavaScript se puede leer y modificar ([[JavaScript/09 - DOM]]). CSS también puede seleccionar elementos por sus atributos ([[CSS/02 - Selectores]]).

> [!note] Atributo y propiedad no son lo mismo
> El **atributo** es lo que escribes en el HTML y recoge el valor **inicial**. La **propiedad** es el estado actual del elemento en el DOM. Por ejemplo, en `<input value="Ana">` el atributo `value` guarda `"Ana"`, pero si la persona escribe otra cosa, la propiedad `value` cambia y el atributo no.

---

## 3. Sintaxis / estructura

### 3.1 Forma general

```html
<etiqueta nombre1="valor1" nombre2="valor2">Contenido</etiqueta>
```

- Los atributos van **solo en la etiqueta de apertura**, nunca en la de cierre.
- Se separan entre sí por **espacios** (puede haber saltos de línea).
- El orden no importa.
- El nombre del atributo no distingue mayúsculas de minúsculas, pero se escribe en **minúsculas** por convención.

### 3.2 Comillas

```html
<p class="aviso">Con comillas dobles (recomendado)</p>
<p class='aviso'>Con comillas simples</p>
<p class=aviso>Sin comillas (solo válido si el valor no tiene espacios)</p>
```

Cuando el valor contiene espacios o caracteres especiales, las comillas son obligatorias:

```html
<p class="aviso importante" title="Más información">...</p>
```

### 3.3 Atributos booleanos

Un atributo booleano está activo si **aparece**, sea cual sea su valor:

```html
<input type="text" disabled>
<input type="text" disabled="">
<input type="text" disabled="disabled">
```

Las tres líneas son equivalentes. Para desactivarlo hay que **quitar el atributo**:

```html
<input type="text">
```

### 3.4 Atributos de datos personalizados

```html
<li data-id="42" data-categoria="oferta">Cuaderno</li>
```

### 3.5 Atributos de accesibilidad

```html
<button aria-label="Cerrar ventana">X</button>
<div role="alert">Se ha producido un error.</div>
```

---

## 4. Elementos / propiedades / características

### 4.1 Atributos globales más usados

| Atributo | Función | Ejemplo |
|----------|---------|---------|
| `id` | Identificador **único** en la página | `id="menu"` |
| `class` | Una o varias clases separadas por espacios | `class="tarjeta destacada"` |
| `style` | Estilos CSS en línea | `style="color: red;"` |
| `title` | Información adicional (aparece como tooltip) | `title="Ayuda"` |
| `lang` | Idioma del contenido del elemento | `lang="en"` |
| `dir` | Dirección del texto: `ltr`, `rtl`, `auto` | `dir="rtl"` |
| `hidden` | Oculta el elemento | `hidden` |
| `tabindex` | Orden y posibilidad de recibir el foco con el teclado | `tabindex="0"` |
| `accesskey` | Atajo de teclado para activar el elemento | `accesskey="s"` |
| `contenteditable` | Hace editable el contenido | `contenteditable="true"` |
| `draggable` | Permite arrastrar el elemento | `draggable="true"` |
| `spellcheck` | Activa o desactiva el corrector ortográfico | `spellcheck="false"` |
| `translate` | Indica si el contenido se debe traducir | `translate="no"` |
| `inert` | Hace que el elemento y su contenido no sean interactivos | `inert` |
| `popover` | Convierte el elemento en una ventana emergente nativa | `popover` |
| `autofocus` | El elemento recibe el foco al cargar | `autofocus` |
| `data-*` | Datos personalizados para scripts y estilos | `data-id="42"` |
| `role` y `aria-*` | Información de accesibilidad | `role="alert"`, `aria-label="..."` |

### 4.2 Detalle de `id` y `class`

| Aspecto | `id` | `class` |
|---------|------|---------|
| Cuántos por elemento | Uno | Varios (separados por espacios) |
| Cuántos elementos pueden compartirlo | **Uno solo** por página | Todos los que quieras |
| Se escribe en CSS como | `#menu` | `.tarjeta` |
| Distingue mayúsculas | Sí | Sí |
| Uso típico | Enlaces internos, `label for`, scripts, un elemento concreto | Dar estilo a grupos de elementos |

Un `id` no puede contener espacios y debe tener al menos un carácter.

### 4.3 Valores de `tabindex`

| Valor | Efecto |
|-------|--------|
| `0` | El elemento puede recibir el foco y sigue el orden natural del documento |
| `-1` | Solo puede recibir el foco **por script**, no con la tecla Tab |
| Positivo (`1`, `2`...) | Fuerza un orden propio; **no se recomienda** |

### 4.4 Valores de `contenteditable`

| Valor | Efecto |
|-------|--------|
| `true` (o vacío) | El contenido es editable |
| `false` | No es editable |
| `plaintext-only` | Solo admite texto plano, sin formato |

### 4.5 Atributos de datos `data-*`

- El nombre debe empezar por `data-` seguido de al menos un carácter, en minúsculas.
- El valor es siempre **texto**.
- Desde JavaScript se leen con `dataset`, y los guiones pasan a *camelCase*:

```html
<li id="producto" data-id="42" data-categoria-principal="oferta">Cuaderno</li>
```

```javascript
const producto = document.querySelector("#producto");
console.log(producto.dataset.id);                  // "42"
console.log(producto.dataset.categoriaPrincipal);  // "oferta"
```

### 4.6 Atributos específicos más comunes por elemento

| Elemento | Atributos habituales |
|----------|----------------------|
| `<html>` | `lang` |
| `<meta>` | `charset`, `name`, `content` |
| `<link>` | `rel`, `href`, `type`, `media` |
| `<script>` | `src`, `type`, `defer`, `async`, `integrity`, `crossorigin` |
| `<a>` | `href`, `target`, `rel`, `download`, `hreflang` |
| `<img>` | `src`, `alt`, `width`, `height`, `loading`, `srcset`, `sizes` |
| `<video>` / `<audio>` | `src`, `controls`, `autoplay`, `muted`, `loop`, `preload`, `poster` |
| `<iframe>` | `src`, `title`, `loading`, `sandbox`, `allow` |
| `<form>` | `action`, `method`, `enctype`, `novalidate` |
| `<input>` | `type`, `name`, `value`, `placeholder`, `required`, `disabled`, `readonly`, `min`, `max`, `pattern` |
| `<label>` | `for` |
| `<button>` | `type`, `disabled`, `popovertarget` |
| `<td>` / `<th>` | `colspan`, `rowspan`, `scope`, `headers` |
| `<ol>` / `<li>` | `start`, `reversed`, `type`, `value` |
| `<details>` | `open` |
| `<time>` | `datetime` |

### 4.5 Atributos de recursos y carga

| Atributo | Elemento | Función |
|----------|----------|---------|
| `defer` | `<script>` | Descarga en paralelo y ejecuta al terminar de leer el HTML |
| `async` | `<script>` | Descarga en paralelo y ejecuta en cuanto está listo |
| `integrity` | `<script>`, `<link>` | Hash para comprobar que el recurso externo no ha sido alterado |
| `crossorigin` | `<script>`, `<link>`, `<img>` | Cómo se piden recursos de otro origen |
| `referrerpolicy` | `<a>`, `<img>`, `<iframe>`... | Qué información de origen se envía |
| `loading` | `<img>`, `<iframe>` | Carga diferida (`lazy`) |

### 4.6 Atributos para teclados virtuales

| Atributo | Función | Ejemplo |
|----------|---------|---------|
| `inputmode` | Tipo de teclado en móvil | `inputmode="numeric"` |
| `enterkeyhint` | Etiqueta de la tecla Intro | `enterkeyhint="send"` |
| `autocapitalize` | Mayúsculas automáticas | `autocapitalize="words"` |

---

## 5. Ejemplos prácticos

### Ejemplo básico

Atributos específicos y globales juntos:

```html
<a href="https://developer.mozilla.org" class="enlace" title="Documentación de MDN">MDN Web Docs</a>

<img src="img/logo.png" alt="Logotipo de la academia" width="200" height="60">

<input type="email" name="correo" required>
```

### Ejemplo habitual

Atributos globales para identificar, clasificar y hacer accesible:

```html
<body>
  <a href="#contenido" class="saltar">Saltar al contenido</a>

  <nav id="menu" class="menu principal" aria-label="Principal">
    <ul>
      <li><a href="index.html" aria-current="page">Inicio</a></li>
      <li><a href="cursos.html">Cursos</a></li>
    </ul>
  </nav>

  <main id="contenido">
    <h1>Cursos</h1>

    <ul>
      <li class="curso" data-id="101" data-nivel="basico">HTML desde cero</li>
      <li class="curso" data-id="102" data-nivel="avanzado">JavaScript avanzado</li>
    </ul>

    <p lang="en">This paragraph is in English.</p>

    <p hidden>Este texto no se muestra.</p>
  </main>
</body>
```

`aria-current="page"` indica la página actual en un menú ([[HTML/08 - Accesibilidad]]).

### Ejemplo completo

Atributos globales poco habituales, de datos y de carga:

```html
<!DOCTYPE html>
<html lang="es" dir="ltr">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Atributos en acción</title>
    <link rel="stylesheet" href="css/estilos.css">
    <script src="js/app.js" defer></script>
  </head>
  <body>
    <h1>Panel de tareas</h1>

    <ul id="tareas">
      <li class="tarea" data-id="1" data-prioridad="alta">Revisar el temario</li>
      <li class="tarea" data-id="2" data-prioridad="baja">Ordenar apuntes</li>
    </ul>

    <p>
      Término técnico que no debe traducirse:
      <span translate="no">flexbox</span>.
    </p>

    <div contenteditable="true" spellcheck="false" tabindex="0">
      Notas rápidas: haz clic y escribe.
    </div>

    <label for="codigo">Código postal</label>
    <input type="text" id="codigo" name="codigo" inputmode="numeric" maxlength="5" autocomplete="postal-code">

    <button type="button" popovertarget="ayuda">Mostrar ayuda</button>
    <div id="ayuda" popover>
      <p>Aquí aparece la ayuda como ventana emergente.</p>
    </div>

    <details open>
      <summary>Más información</summary>
      <p>Este bloque se muestra abierto desde el principio.</p>
    </details>
  </body>
</html>
```

Desde JavaScript, los `data-*` se leen con `dataset` y se pueden usar para reaccionar a los eventos ([[JavaScript/10 - Eventos]]).

---

## 6. Buenas prácticas

- Escribir nombres de atributo en **minúsculas** y valores entre **comillas dobles**.
- Usar **un `id` único** por página y reservarlo para lo que realmente necesita identificarse (enlaces internos, `label for`, scripts). Para dar estilo, usar `class`.
- Nombrar clases e identificadores por su **función o significado** (`tarjeta-producto`) y no por su aspecto (`rojo-grande`).
- Evitar el atributo `style` en línea: el estilo pertenece a CSS ([[CSS/01 - Fundamentos]]).
- Usar `data-*` para guardar datos propios en lugar de inventar atributos no estándar.
- Declarar el idioma con `lang` en `<html>` y marcar con `lang` los fragmentos en otro idioma.
- No usar `title` como única vía para transmitir información importante: no es accesible en táctil ni con teclado.
- Mantener `tabindex` en `0` o `-1`; **evitar valores positivos**, que rompen el orden natural de navegación.
- Escribir los booleanos solo con su nombre (`disabled`), y quitar el atributo para desactivarlos.
- No usar `accesskey` salvo casos muy concretos: puede chocar con los atajos del navegador y de los lectores de pantalla.
- Preferir los atributos nativos (`hidden`, `inert`, `popover`, `loading`) antes que soluciones con scripts.
- No duplicar un atributo en la misma etiqueta: el navegador solo hace caso al primero.
- Evitar atributos `on...` (`onclick`) en el HTML: es mejor asignar eventos desde JavaScript ([[JavaScript/10 - Eventos]]).

Más criterios de calidad en [[HTML/11 - Buenas prácticas]].

---

## 7. Diferencias importantes

| Comparación | Diferencia |
|-------------|------------|
| **Atributo global vs específico** | El global funciona en cualquier elemento; el específico solo en ciertos elementos |
| **`id` vs `class`** | `id` es único en la página; `class` se puede repetir en muchos elementos y combinar varias en uno |
| **Atributo booleano vs enumerado** | El booleano está activo por su presencia; el enumerado necesita un valor entre los permitidos |
| **`disabled="false"` vs quitar `disabled`** | `disabled="false"` **sigue** desactivando el elemento; para activarlo hay que eliminar el atributo |
| **Atributo vs propiedad** | El atributo es el valor inicial del HTML; la propiedad es el estado actual en el DOM |
| **`data-*` vs `aria-*`** | `data-*` guarda datos propios sin significado para el navegador; `aria-*` aporta información a las tecnologías de apoyo |
| **`hidden` vs `display: none`** | `hidden` es un atributo HTML; `display: none` es CSS y puede sobrescribirlo. Ambos ocultan también para lectores de pantalla |
| **`title` vs `aria-label`** | `title` es un tooltip adicional; `aria-label` da nombre accesible a un elemento sin texto visible |
| **`tabindex="0"` vs `"-1"`** | `0` entra en el orden con la tecla Tab; `-1` solo recibe el foco por script |
| **`defer` vs `async`** | `defer` ejecuta al terminar el HTML y respeta el orden; `async` ejecuta cuando esté listo, sin orden garantizado |
| **`readonly` vs `disabled`** | `readonly` no se edita pero se envía; `disabled` no se usa ni se envía ([[HTML/06 - Formularios]]) |

---

## 8. Casos especiales

### Atributos que aceptan varios valores

`class`, `rel` y `headers` admiten varios valores **separados por espacios**:

```html
<a href="https://ejemplo.com" rel="noopener noreferrer nofollow">Enlace</a>
```

### Atributos sin valor frente a valor vacío

`<input value="">` define un valor inicial vacío. `<input disabled>` activa el atributo booleano. No son lo mismo: el primero es un atributo con valor vacío y el segundo un booleano.

### Valores que contienen comillas

Si el valor lleva comillas dobles, se usan comillas simples para encerrarlo, o se escribe la entidad `&quot;`:

```html
<p title='Dijo "hola" al entrar'>Texto</p>
<p title="Dijo &quot;hola&quot; al entrar">Texto</p>
```

### `&` dentro de una URL en un atributo

Un `&` en un valor de atributo debe escribirse como `&amp;`, sobre todo cuando forma parte de una cadena de consulta:

```html
<a href="buscar.html?q=html&amp;pagina=2">Buscar</a>
```

### `hidden="until-found"`

Oculta el contenido pero permite que la búsqueda del navegador (`Ctrl + F`) lo encuentre y lo muestre.

### `inert` frente a `aria-hidden`

`inert` impide la interacción y oculta el contenido para las tecnologías de apoyo. `aria-hidden="true"` solo lo oculta para ellas, pero **no** impide que reciba el foco.

### Atributos desconocidos o mal escritos

El navegador **no muestra errores**: simplemente ignora los atributos que no conoce. Por eso un fallo tipográfico (`clss="..."`) pasa desapercibido y conviene validar el código.

### Atributos de eventos en el HTML

`onclick`, `onchange`, `onsubmit` y similares existen y funcionan, pero mezclan comportamiento y estructura, y complican las políticas de seguridad del sitio. Es preferible usar `addEventListener` desde JavaScript.

### Sensibilidad a mayúsculas

Los **nombres** de atributo no distinguen mayúsculas, pero algunos **valores** sí (`id`, `class`, rutas de archivo en `src` y `href`).

> [!warning] Obsoleto / legado
> - Los atributos de presentación están obsoletos y se sustituyen por **CSS**: `align`, `bgcolor`, `background`, `border`, `color`, `face`, `height` y `width` en elementos de texto o tablas, `cellpadding`, `cellspacing`, `valign` ([[CSS/05 - Colores y fondos]], [[CSS/03 - Box Model]]).
> - `name` en `<a>` para crear anclas está obsoleto: se usa `id`.
> - `charset` en `<a>` y `<script>`, y `language` en `<script>`, están obsoletos.
> - `contextmenu`, `dropzone` y `manifest` han sido eliminados.
> - Los atributos `on...` en línea siguen funcionando, pero se consideran una práctica heredada.
> - Los **atributos propios inventados** (`<div color="rojo">`) no son válidos: se sustituyen por `data-*`.

---

## 9. Resumen

- Un **atributo** configura o describe un elemento; se escribe en la etiqueta de apertura como `nombre="valor"`.
- Los **globales** valen para cualquier elemento (`id`, `class`, `lang`, `title`, `hidden`, `tabindex`, `data-*`...); los **específicos** solo para ciertas etiquetas.
- Los **booleanos** se activan por su presencia (`disabled`) y se desactivan quitándolos; `disabled="false"` sigue activo.
- Los **enumerados** admiten solo ciertos valores (`contenteditable`, `dir`, `draggable`).
- `id` es único en la página; `class` se repite y admite varios valores separados por espacios.
- `data-*` guarda datos propios y se lee desde JavaScript con `dataset`; `role` y `aria-*` aportan accesibilidad.
- El atributo es el valor **inicial** del HTML; la propiedad es el estado **actual** en el DOM.
- Conviene usar minúsculas, comillas dobles, nombres significativos y dejar el aspecto a CSS y los eventos a JavaScript.