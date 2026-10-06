# HTML - Accesibilidad

> [!info] ¿Qué es?
> La **accesibilidad web** consiste en crear páginas que **puedan usar todas las personas**, incluidas las que tienen discapacidades visuales, auditivas, motoras o cognitivas, y las que usan tecnologías de apoyo (lectores de pantalla, teclado en lugar de ratón, ampliadores, control por voz). En HTML se consigue sobre todo con **buena semántica**, textos alternativos, etiquetas claras, navegación por teclado y, cuando hace falta, atributos **ARIA**.

---

## 1. Antes de empezar

Conviene dominar antes:

- La semántica de la página (`header`, `nav`, `main`, encabezados): [[HTML/02 - Estructura y semántica]].
- Los textos de enlace y el atributo `alt`: [[HTML/03 - Texto y enlaces]] y [[HTML/04 - Imágenes y multimedia]].
- Las etiquetas de formulario y los atributos globales (`tabindex`, `lang`): [[HTML/06 - Formularios]] y [[HTML/07 - Atributos]].

> [!tip] Prueba rápida
> Abre tu página y navega **solo con el teclado** (`Tab`, `Shift + Tab`, `Intro`, `Espacio`, flechas). Si no puedes llegar a todo, ver dónde estás o activar cada control, hay un problema de accesibilidad.

---

## 2. Concepto fundamental

Las pautas WCAG (*Web Content Accessibility Guidelines*) organizan la accesibilidad en cuatro principios, conocidos como **POUR**:

| Principio | Idea | Ejemplo en HTML |
|-----------|------|-----------------|
| **Perceptible** | La información se puede percibir con distintos sentidos | `alt` en imágenes, subtítulos en vídeo, buen contraste |
| **Operable** | Se puede manejar con distintos dispositivos | Navegación con teclado, foco visible, enlaces claros |
| **Comprensible** | Contenido y funcionamiento fáciles de entender | `lang`, etiquetas claras, mensajes de error útiles |
| **Robusto** | Funciona con navegadores y tecnologías de apoyo actuales y futuros | HTML válido y semántico, ARIA bien usado |

Los niveles de conformidad son **A** (mínimo), **AA** (el objetivo habitual y el exigido en muchas normativas) y **AAA** (el más estricto).

La regla de oro es:

> [!note] La primera regla de ARIA
> **Si existe una etiqueta HTML nativa que hace lo que necesitas, úsala en lugar de ARIA.** Un `<button>` ya es operable con teclado, tiene rol de botón y recibe el foco. Un `<div role="button">` obliga a programar todo eso a mano. ARIA **no añade comportamiento**, solo describe: nada de lo que añadas con ARIA hace que un elemento funcione con el teclado.

---

## 3. Sintaxis / estructura

### 3.1 Idioma y título de la página

```html
<html lang="es">
  <head>
    <title>Contacto - Academia Web</title>
  </head>
```

### 3.2 Imágenes con texto alternativo

```html
<img src="img/grafico.png" alt="Gráfico de barras: las ventas suben un 20 % en el tercer trimestre">
<img src="img/adorno.svg" alt="">
```

### 3.3 Enlace de salto al contenido

```html
<body>
  <a href="#contenido" class="saltar">Saltar al contenido principal</a>
  <header>...</header>
  <main id="contenido">...</main>
</body>
```

### 3.4 Campo de formulario accesible

```html
<label for="correo">Correo electrónico</label>
<input type="email" id="correo" name="correo" required aria-describedby="ayuda-correo">
<p id="ayuda-correo">Te enviaremos la confirmación a esta dirección.</p>
```

### 3.5 Atributos ARIA

```html
<button aria-label="Cerrar ventana">×</button>
<button aria-expanded="false" aria-controls="menu">Menú</button>
<div role="alert">No se ha podido guardar el formulario.</div>
<section aria-labelledby="titulo-ofertas">
  <h2 id="titulo-ofertas">Ofertas</h2>
</section>
```

### 3.6 Texto solo para lectores de pantalla

```html
<button>
  <svg aria-hidden="true" focusable="false">...</svg>
  <span class="sr-only">Añadir al carrito</span>
</button>
```

```css
.sr-only {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border: 0;
}
```

---

## 4. Elementos / propiedades / características

### 4.1 Elementos HTML con accesibilidad integrada

| Elemento | Qué aporta |
|----------|------------|
| `<button>`, `<a href>` | Foco con teclado, activación con `Intro` (y `Espacio` en botones), rol correcto |
| `<label>` | Nombre accesible para el control y mayor área de clic |
| `<fieldset>` y `<legend>` | Nombre de grupo para controles relacionados |
| `<nav>`, `<main>`, `<header>`, `<footer>`, `<aside>` | Zonas de la página (*landmarks*) para saltar entre ellas |
| `<h1>`-`<h6>` | Estructura navegable por encabezados |
| `<ul>`, `<ol>`, `<dl>` | Anuncio de lista y número de elementos |
| `<table>` con `<th>`, `<caption>` y `scope` | Asociación entre datos y encabezados |
| `<details>` y `<summary>` | Bloque desplegable operable con teclado |
| `<dialog>` | Ventana modal con gestión nativa del foco |
| `<track>` | Subtítulos y descripciones en vídeo y audio |

### 4.2 Zonas (*landmarks*) y su equivalencia

| Elemento | Rol ARIA implícito |
|----------|--------------------|
| `<header>` (hijo directo de `<body>`) | `banner` |
| `<nav>` | `navigation` |
| `<main>` | `main` |
| `<aside>` | `complementary` |
| `<footer>` (hijo directo de `<body>`) | `contentinfo` |
| `<section>` con nombre accesible | `region` |
| `<form>` con nombre accesible | `form` |
| `<search>` | `search` |

### 4.3 Atributos ARIA para dar nombre y descripción

| Atributo | Función |
|----------|---------|
| `aria-label` | Da un nombre accesible en forma de texto |
| `aria-labelledby` | Toma el nombre de **otro elemento** (por su `id`) |
| `aria-describedby` | Asocia una descripción adicional (ayuda, instrucciones, error) |

### 4.4 Atributos ARIA de estado

| Atributo | Función | Valores |
|----------|---------|---------|
| `aria-expanded` | Indica si algo está desplegado | `true`, `false` |
| `aria-pressed` | Estado de un botón de alternancia | `true`, `false`, `mixed` |
| `aria-checked` | Estado de una casilla o interruptor personalizado | `true`, `false`, `mixed` |
| `aria-selected` | Elemento seleccionado (pestañas, opciones) | `true`, `false` |
| `aria-current` | Elemento actual dentro de un conjunto | `page`, `step`, `location`, `true` |
| `aria-disabled` | Control desactivado pero visible | `true`, `false` |
| `aria-invalid` | El valor no es válido | `true`, `false` |
| `aria-required` | Campo obligatorio (en controles no nativos) | `true`, `false` |
| `aria-busy` | La zona se está actualizando | `true`, `false` |
| `aria-hidden` | Oculta el elemento a las tecnologías de apoyo | `true` |

### 4.5 Atributos ARIA de relación y de anuncio

| Atributo | Función |
|----------|---------|
| `aria-controls` | Indica qué elemento controla este |
| `aria-live` | Anuncia los cambios de una zona: `polite` (espera su turno) o `assertive` (interrumpe) |
| `aria-atomic` | Si se anuncia toda la zona o solo la parte cambiada |
| `aria-haspopup` | El elemento abre un menú o ventana emergente |
| `aria-modal` | La ventana es modal |

### 4.6 Roles ARIA habituales

| Rol | Uso |
|-----|-----|
| `alert` | Mensaje importante y urgente (se anuncia de inmediato) |
| `status` | Mensaje informativo no urgente |
| `dialog` | Ventana de diálogo |
| `tablist`, `tab`, `tabpanel` | Sistema de pestañas |
| `progressbar` | Indicador de progreso |
| `img` | Agrupa contenido para tratarlo como una imagen (por ejemplo, un SVG) |
| `presentation` o `none` | Elimina la semántica de un elemento |

### 4.7 Contraste y tamaño (WCAG 2.2, nivel AA)

| Elemento | Requisito |
|----------|-----------|
| Texto normal | Contraste mínimo **4,5 : 1** respecto al fondo |
| Texto grande (≥ 24 px o ≥ 18,66 px en negrita) | Contraste mínimo **3 : 1** |
| Componentes de interfaz y gráficos | Contraste mínimo **3 : 1** |
| Zonas pulsables | Tamaño mínimo de **24 × 24 px** CSS |
| Zoom | La página debe seguir usable al **ampliar el texto hasta el 200 %** |

Estos aspectos se resuelven en CSS ([[CSS/05 - Colores y fondos]], [[CSS/06 - Texto y fuentes]]).

---

## 5. Ejemplos prácticos

### Ejemplo básico

Los tres mínimos: idioma, texto alternativo y etiqueta de campo:

```html
<html lang="es">
  <body>
    <img src="img/logo.png" alt="Logotipo de Academia Web">

    <label for="buscar">Buscar en el sitio</label>
    <input type="search" id="buscar" name="buscar">
  </body>
</html>
```

### Ejemplo habitual

Página con enlace de salto, zonas, navegación con página actual y botón de icono:

```html
<body>
  <a href="#contenido" class="saltar">Saltar al contenido principal</a>

  <header>
    <p>Academia Web</p>
    <nav aria-label="Principal">
      <ul>
        <li><a href="index.html" aria-current="page">Inicio</a></li>
        <li><a href="cursos.html">Cursos</a></li>
        <li><a href="contacto.html">Contacto</a></li>
      </ul>
    </nav>
  </header>

  <main id="contenido">
    <h1>Bienvenido a la Academia</h1>

    <button type="button">
      <svg aria-hidden="true" focusable="false" width="16" height="16">...</svg>
      <span class="sr-only">Añadir a favoritos</span>
    </button>

    <p>
      <a href="https://developer.mozilla.org" target="_blank" rel="noopener">
        Documentación de MDN <span class="sr-only">(se abre en una pestaña nueva)</span>
      </a>
    </p>
  </main>

  <footer>
    <p>&copy; 2026 Academia Web</p>
  </footer>
</body>
```

El estilo `.sr-only` está en el apartado de sintaxis, y los enlaces con `target="_blank"` se explican en [[HTML/03 - Texto y enlaces]].

### Ejemplo completo

Formulario con errores accesibles y botón que despliega contenido:

```html
<main id="contenido">
  <h1>Contacto</h1>

  <p id="resumen-error" role="alert" hidden>
    Hay errores en el formulario. Revisa los campos marcados.
  </p>

  <form id="contacto" novalidate>
    <fieldset>
      <legend>Tus datos</legend>

      <div>
        <label for="nombre">Nombre (obligatorio)</label>
        <input type="text" id="nombre" name="nombre" required autocomplete="name"
               aria-describedby="error-nombre">
        <p id="error-nombre" class="error" hidden>Escribe tu nombre.</p>
      </div>

      <div>
        <label for="correo">Correo electrónico (obligatorio)</label>
        <input type="email" id="correo" name="correo" required autocomplete="email"
               aria-describedby="ayuda-correo error-correo">
        <p id="ayuda-correo">Te responderemos a esta dirección.</p>
        <p id="error-correo" class="error" hidden>Escribe un correo válido, por ejemplo ana@ejemplo.com.</p>
      </div>
    </fieldset>

    <button type="submit">Enviar mensaje</button>
  </form>

  <h2>Preguntas frecuentes</h2>
  <button type="button" id="boton-faq" aria-expanded="false" aria-controls="faq">
    ¿Cuánto tardáis en responder?
  </button>
  <div id="faq" hidden>
    <p>Respondemos en un máximo de 48 horas laborables.</p>
  </div>

  <p id="estado" role="status"></p>
</main>

<script>
  const boton = document.querySelector("#boton-faq");
  const faq = document.querySelector("#faq");

  boton.addEventListener("click", () => {
    const abierto = boton.getAttribute("aria-expanded") === "true";
    boton.setAttribute("aria-expanded", String(!abierto));
    faq.hidden = abierto;
  });

  document.querySelector("#contacto").addEventListener("submit", (evento) => {
    const campos = document.querySelectorAll("#contacto input");
    let hayErrores = false;

    campos.forEach((campo) => {
      const error = document.querySelector("#error-" + campo.name);
      const valido = campo.checkValidity();
      campo.setAttribute("aria-invalid", String(!valido));
      error.hidden = valido;
      if (!valido) hayErrores = true;
    });

    document.querySelector("#resumen-error").hidden = !hayErrores;

    if (hayErrores) {
      evento.preventDefault();
      document.querySelector("#contacto input[aria-invalid='true']").focus();
    } else {
      evento.preventDefault();
      document.querySelector("#estado").textContent = "Mensaje enviado correctamente.";
    }
  });
</script>
```

Este ejemplo reúne las ideas clave: el error se **asocia** al campo con `aria-describedby`, se marca con `aria-invalid`, se **anuncia** con `role="alert"` y el **foco** se mueve al primer campo con problemas. Los eventos y la validación desde JavaScript se profundizan en [[JavaScript/10 - Eventos]] y [[JavaScript/11 - Formularios]].

---

## 6. Buenas prácticas

- **Usar primero HTML semántico** y recurrir a ARIA solo cuando no exista una etiqueta nativa adecuada.
- Declarar el idioma con `lang` en `<html>` y marcar los fragmentos en otro idioma.
- Dar un `<title>` único y descriptivo a cada página.
- Mantener un **único `<h1>`** y una jerarquía de encabezados sin saltos de nivel.
- Escribir `alt` descriptivo en imágenes informativas y `alt=""` en las decorativas.
- Asociar un `<label>` visible a **cada control** de formulario y no depender solo del `placeholder`.
- Escribir **textos de enlace y botón comprensibles** fuera de contexto ("Descargar la guía", no "Haz clic aquí").
- Garantizar que **todo se pueda usar con el teclado**, con un orden de foco lógico y un **foco visible**. No eliminar `outline` sin ofrecer una alternativa clara.
- Usar `<button>` para acciones y `<a>` para navegación; no usar `<div>` con `onclick`.
- Incluir un **enlace de salto** al contenido principal en páginas con mucha navegación.
- Cuidar el **contraste** (4,5 : 1 en texto normal) y no transmitir información **solo con el color**.
- Ofrecer **subtítulos** en el vídeo y una **transcripción** para el audio ([[HTML/04 - Imágenes y multimedia]]).
- No reproducir audio ni vídeo automáticamente con sonido, y permitir pausar cualquier animación.
- Respetar la preferencia del sistema de reducir movimiento (`prefers-reduced-motion`, [[CSS/11 - Transiciones y animaciones]]).
- Indicar con `aria-expanded`, `aria-current` y `aria-invalid` el **estado** de los controles que cambian.
- Anunciar los **cambios dinámicos** (mensajes, resultados) con `role="status"` o `role="alert"`.
- Probar con **herramientas y con personas**: Lighthouse, axe DevTools, el validador de HTML, un lector de pantalla (NVDA, VoiceOver) y solo con el teclado.

Más criterios de calidad en [[HTML/11 - Buenas prácticas]].

---

## 7. Diferencias importantes

| Comparación | Diferencia |
|-------------|------------|
| **`alt` vs `aria-label`** | `alt` es el texto alternativo de una imagen; `aria-label` da nombre a cualquier elemento sin texto visible |
| **`aria-label` vs `aria-labelledby`** | `aria-label` escribe el nombre directamente; `aria-labelledby` lo toma de otro elemento visible por su `id` |
| **`aria-labelledby` vs `aria-describedby`** | El primero da el **nombre** del elemento; el segundo, una **descripción adicional** |
| **`<label>` vs `aria-label`** | `<label>` es visible y amplía el área de clic; `aria-label` es invisible y se reserva para cuando no cabe una etiqueta visible |
| **`<button>` vs `<div role="button">`** | `<button>` ya incluye foco y teclado; el `<div>` requiere programarlo todo a mano |
| **`role="alert"` vs `role="status"`** | `alert` interrumpe y se usa para errores urgentes; `status` espera su turno y sirve para información |
| **`aria-live="polite"` vs `"assertive"`** | `polite` anuncia al terminar la lectura actual; `assertive` interrumpe de inmediato |
| **`hidden` vs `aria-hidden="true"`** | `hidden` oculta a **todos**; `aria-hidden` oculta solo a las tecnologías de apoyo pero el elemento sigue visible |
| **`hidden` vs `.sr-only`** | `hidden` lo oculta a todos; `.sr-only` lo oculta visualmente pero lo deja disponible para lectores de pantalla |
| **`inert` vs `aria-hidden`** | `inert` bloquea además el foco y la interacción; `aria-hidden` no bloquea el foco |
| **`tabindex="0"` vs `"-1"`** | `0` entra en el orden de tabulación; `-1` solo se enfoca por script |
| **Accesibilidad vs usabilidad** | La accesibilidad garantiza que **todas las personas** puedan usar la página; la usabilidad mide lo fácil y agradable que resulta |

---

## 8. Casos especiales

### Iconos y SVG

- Un icono **decorativo** junto a un texto: `aria-hidden="true"`.
- Un icono que **es el único contenido** de un botón: añadir un nombre accesible (`aria-label` o texto `.sr-only`).
- Un SVG informativo: `role="img"` y un `<title>` dentro del SVG, o `aria-label`.

### Nombre accesible de un elemento

El navegador calcula el nombre en este orden de preferencia: `aria-labelledby`, `aria-label`, el contenido o la etiqueta nativa (`<label>`, `alt`, `<caption>`...) y, por último, `title`. Se puede revisar en la pestaña **Accesibilidad** de las herramientas de desarrollo.

### Ventanas modales

Es preferible usar el elemento nativo `<dialog>` con `showModal()`: gestiona el foco, bloquea el resto de la página y se cierra con `Esc`. Si se construye a mano hay que atrapar el foco dentro, devolverlo al elemento que abrió la ventana al cerrar, y marcar el resto como `inert`.

### Contenido que cambia sin recargar

Al actualizar una zona con JavaScript (resultados de búsqueda, mensajes de éxito), una persona con lector de pantalla no se entera si no se anuncia. Se usa una región `aria-live` o `role="status"` que **ya exista en el HTML** antes de cambiar su contenido.

### Gestión del foco

Tras una acción que cambia mucho la página (abrir un diálogo, enviar un formulario con errores, navegar en una aplicación de una sola página), hay que **mover el foco** a un lugar lógico. Para dar foco a un elemento que no es interactivo se le añade `tabindex="-1"`.

### Tablas de datos

Una tabla accesible lleva `<caption>`, celdas `<th>` con `scope` y estructura `<thead>`/`<tbody>`. Si es muy compleja, `headers` relaciona cada dato con sus encabezados ([[HTML/05 - Listas y tablas]]).

### Enlaces que abren pestañas nuevas o descargan

Conviene avisarlo en el texto del enlace o en un texto `.sr-only` ("se abre en una pestaña nueva", "PDF, 2 MB").

### Texto en imágenes

El texto dentro de una imagen no se puede ampliar, traducir ni leer en voz alta. Debe escribirse como texto real, o repetirse íntegro en el `alt`.

### Zoom y reflujo

La página debe poder verse a 400 % de zoom en una ventana de 1280 px sin desplazamiento horizontal. Se logra con diseños adaptables y unidades relativas ([[CSS/10 - Responsive Design]], [[CSS/04 - Unidades y valores]]). No se debe bloquear el zoom con `user-scalable=no` en el `viewport`.

### Los atributos ARIA incorrectos empeoran la experiencia

Un ARIA mal usado (un rol que no corresponde, un `aria-hidden` en un elemento enfocable) es **peor que no usarlo**. Lo más fiable es aplicar lo mínimo necesario y comprobarlo con un lector de pantalla.

> [!warning] Obsoleto / legado
> - `aria-grabbed` y `aria-dropeffect` están obsoletos en las versiones recientes de ARIA.
> - Los **roles redundantes** (`<nav role="navigation">`, `<button role="button">`) no son un error grave, pero sobran: la etiqueta nativa ya los incluye.
> - `longdesc` en `<img>` casi no tiene soporte: la descripción larga se pone en el texto o en una leyenda.
> - `accesskey` suele chocar con atajos del navegador y de los lectores de pantalla; hoy no se recomienda.
> - Los valores positivos de `tabindex` (`1`, `2`...) rompen el orden natural: se evita.
> - `<blink>` y `<marquee>` están obsoletos y son inaccesibles: se sustituyen por CSS y se debe permitir pausar cualquier movimiento.
> - Las técnicas de "enlace con `title` como única descripción" y de "formularios maquetados con tablas" han sido sustituidas por `<label>` y CSS ([[CSS/07 - Flexbox]], [[CSS/08 - Grid]]).

---

## 9. Resumen

- La accesibilidad permite que **todas las personas** usen la web; se rige por los principios **POUR** (perceptible, operable, comprensible y robusto) y los niveles WCAG **A, AA y AAA**.
- La base es el **HTML semántico**: zonas (`header`, `nav`, `main`), encabezados ordenados, listas, tablas con `<th>` y botones y enlaces reales.
- Cada imagen necesita su **`alt`** (vacío si es decorativa), cada control su **`<label>`**, y cada página su **idioma** y su **título**.
- Todo debe funcionar con el **teclado**, con orden de foco lógico, foco visible y un enlace de salto al contenido.
- **ARIA** complementa, no sustituye: primero HTML nativo, después `aria-label`, `aria-labelledby`, `aria-describedby`, `aria-expanded`, `aria-current`, `aria-invalid`, `role="alert"` y `role="status"` donde haga falta.
- `aria-hidden="true"` no debe ponerse en elementos que puedan recibir el foco; `hidden` e `inert` ocultan de forma más completa.
- El contraste mínimo es 4,5 : 1 en texto normal, y la información no debe depender solo del color.
- Se comprueba con **herramientas** (Lighthouse, axe, validador), con el **teclado** y con un **lector de pantalla**.