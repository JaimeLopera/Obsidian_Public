# 11 - Buenas prácticas

> [!info] ¿Qué es?
> Conjunto de criterios que separan un HTML que **simplemente funciona** de un HTML **limpio, mantenible, accesible y preparado para durar**. No añaden etiquetas nuevas: definen cómo usar bien las que ya conoces.

---

## 1. Antes de empezar

Esta nota **no enseña etiquetas nuevas**, sino que reúne los criterios de calidad que se apoyan en las notas anteriores. Si algo no te suena, repasa primero:

- [[02 - Estructura y semántica]] → etiquetas semánticas y esqueleto del documento
- [[07 - Atributos]] → atributos globales y específicos
- [[08 - Accesibilidad]] → ARIA, `alt`, navegación con teclado
- [[09 - SEO]] → `title`, `meta`, encabezados

**Por qué importa:** el navegador es tolerante y "arregla" casi cualquier HTML roto. Eso hace que los fallos pasen desapercibidos hasta que algo falla en otro navegador, con un lector de pantalla o en un buscador.

---

## 2. Concepto fundamental

Un buen HTML cumple cinco principios:

| Principio | Idea clave |
|---|---|
| **Semántica** | Cada etiqueta se elige por su **significado**, no por su aspecto |
| **Separación de responsabilidades** | HTML = contenido y estructura · CSS = presentación · JS = comportamiento |
| **Accesibilidad** | Debe poder usarse sin ratón, sin vista y en cualquier dispositivo |
| **Validez** | Sigue el estándar: sin etiquetas sin cerrar ni anidaciones incorrectas |
| **Legibilidad** | Otra persona (o tú dentro de un año) lo entiende de un vistazo |

---

## 3. Sintaxis / estructura

### Esqueleto mínimo correcto

```html
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Título descriptivo de la página</title>
</head>
<body>
  <header>…</header>
  <main>…</main>
  <footer>…</footer>
</body>
</html>
```

### Convenciones de escritura

- Etiquetas y atributos en **minúsculas**
- Valores de atributos **entre comillas dobles**
- **Indentación** consistente (2 o 4 espacios, siempre la misma)
- Una sola etiqueta `<h1>` por página
- Un solo `<main>` por página

---

## 4. Elementos / propiedades / características

### 4.1 Estructura del documento

- Declara siempre `<!DOCTYPE html>` (activa el modo estándar).
- Declara el idioma con `lang` en `<html>`: ayuda a lectores de pantalla, traductores y buscadores.
- Declara `charset="UTF-8"` **al principio** del `<head>`.
- Incluye la etiqueta `viewport` para que la página sea responsive.
- Cada página necesita un `<title>` único y descriptivo.

### 4.2 Semántica

- Usa `<header>`, `<nav>`, `<main>`, `<section>`, `<article>`, `<aside>` y `<footer>` según su función.
- Usa `<button>` para acciones y `<a>` para navegación.
- Usa `<ul>`/`<ol>` para listas reales y `<table>` solo para datos tabulares.
- Usa `<strong>` y `<em>` por **énfasis con significado**; no para dar estilo.

### 4.3 Encabezados

- Respeta la jerarquía: `h1` → `h2` → `h3`, sin saltarte niveles.
- Elige el nivel por la **estructura**, no por el tamaño visual (el tamaño se controla con CSS).

### 4.4 Imágenes y multimedia

- `alt` siempre presente. Descriptivo si la imagen aporta información; `alt=""` si es decorativa.
- Indica `width` y `height` para evitar saltos de diseño mientras carga.
- Usa `loading="lazy"` en imágenes que no estén en la primera pantalla.
- Elige formatos modernos (WebP, AVIF) con `<picture>` cuando sea posible.

### 4.5 Enlaces

- El texto del enlace debe tener sentido por sí solo.
- Si un enlace abre en otra pestaña (`target="_blank"`), añade `rel="noopener noreferrer"`.

### 4.6 Formularios

- Asocia cada campo con su `<label>` (por `for`/`id` o envolviéndolo).
- Usa el `type` correcto (`email`, `tel`, `number`, `date`…) para obtener teclado y validación adecuados.
- Agrupa campos relacionados con `<fieldset>` y `<legend>`.
- Usa `name`, `required`, `autocomplete` y `placeholder` con criterio (el placeholder **no sustituye** al label).

### 4.7 Separación de responsabilidades

- CSS en archivos externos (`<link rel="stylesheet">`).
- JavaScript en archivos externos, con `defer` o al final del `<body>`.
- Evita `style="…"` y atributos `onclick="…"` en línea.

---

## 5. Ejemplos prácticos

### Ejemplo básico: etiqueta correcta para cada función

❌ **Qué está mal**
```html
<div class="boton" onclick="enviar()">Enviar</div>
```

🤔 **Por qué ocurre**
Un `<div>` no es focusable con el teclado, no se anuncia como botón y no responde a `Enter` ni `Espacio`.

✅ **Cómo se corrige**
```html
<button type="button" id="enviar">Enviar</button>
```

---

### Ejemplo habitual: estructura semántica de una página

❌ **Qué está mal**
```html
<div id="cabecera">
  <div id="menu">
    <div><a href="/">Inicio</a></div>
    <div><a href="/blog">Blog</a></div>
  </div>
</div>
<div id="contenido">
  <div class="titulo">Mi artículo</div>
</div>
```

🤔 **Por qué ocurre**
Es el hábito de "maquetar con `div`". Funciona visualmente, pero no transmite significado a navegadores, buscadores ni tecnologías de apoyo.

✅ **Cómo se corrige**
```html
<header>
  <nav>
    <ul>
      <li><a href="/">Inicio</a></li>
      <li><a href="/blog">Blog</a></li>
    </ul>
  </nav>
</header>
<main>
  <article>
    <h1>Mi artículo</h1>
  </article>
</main>
```

---

### Ejemplo completo: formulario bien construido

```html
<form action="/contacto" method="post">
  <fieldset>
    <legend>Datos de contacto</legend>

    <label for="nombre">Nombre</label>
    <input type="text" id="nombre" name="nombre" autocomplete="name" required>

    <label for="email">Correo electrónico</label>
    <input type="email" id="email" name="email" autocomplete="email" required>

    <label for="mensaje">Mensaje</label>
    <textarea id="mensaje" name="mensaje" rows="5" required></textarea>
  </fieldset>

  <button type="submit">Enviar</button>
</form>
```

**Qué hace bien:** cada campo tiene su `label`, los `type` son los adecuados, `autocomplete` facilita el rellenado, el botón declara su `type` y los campos relacionados están agrupados.

---

## 6. Buenas prácticas

Lista de comprobación rápida antes de dar una página por terminada:

### Documento
- [ ] `<!DOCTYPE html>`, `lang`, `charset` y `viewport` presentes
- [ ] `<title>` único y descriptivo
- [ ] Una sola etiqueta `<h1>` y jerarquía de encabezados ordenada

### Contenido
- [ ] Etiquetas semánticas en lugar de `div` genéricos
- [ ] Todas las imágenes con `alt` (vacío si son decorativas)
- [ ] Textos de enlace comprensibles fuera de contexto

### Formularios
- [ ] Todo campo con su `label`
- [ ] `type` y `autocomplete` correctos
- [ ] Botones con `type` explícito

### Código
- [ ] Sin estilos ni eventos en línea
- [ ] Indentación y comillas consistentes
- [ ] IDs únicos en toda la página
- [ ] Sin etiquetas sin cerrar ni mal anidadas

### Calidad
- [ ] Validado con el [validador de W3C](https://validator.w3.org/)
- [ ] Navegable solo con teclado (`Tab`, `Enter`, `Espacio`)
- [ ] Revisado con Lighthouse (accesibilidad, SEO, buenas prácticas)

> [!tip] Hábito útil
> Valida el HTML **a menudo** mientras trabajas, no solo al final. Los errores pequeños acumulados son los más difíciles de localizar.

---

## 7. Diferencias importantes

### `id` vs `class`

| | `id` | `class` |
|---|---|---|
| Unicidad | **Único** en la página | Reutilizable |
| Uso típico | Anclas, `label for`, JS puntual | Estilos y agrupación |
| Convención | `kebab-case` | `kebab-case` |

### `<button>` vs `<a>`

| | `<button>` | `<a>` |
|---|---|---|
| Propósito | **Ejecutar una acción** | **Navegar** a otra URL o ancla |
| Ejemplo | Enviar, abrir menú, borrar | Ir a Contacto, descargar fichero |

### `<strong>`/`<em>` vs `<b>`/`<i>`

| | Semántico | Presentacional |
|---|---|---|
| Etiquetas | `<strong>`, `<em>` | `<b>`, `<i>` |
| Significado | Importancia / énfasis | Solo estilo (sin énfasis) |
| Recomendado | Por defecto | Casos concretos (nombres técnicos, términos extranjeros) |

### `<section>` vs `<article>` vs `<div>`

| Etiqueta | Cuándo usarla |
|---|---|
| `<article>` | Contenido **autónomo** que tendría sentido por sí solo (noticia, entrada de blog) |
| `<section>` | Bloque temático **con su propio encabezado** |
| `<div>` | Solo cuando **no hay** etiqueta semántica adecuada (contenedor para estilos) |

---

## 8. Casos especiales

### Cuándo SÍ es correcto usar `<div>` y `<span>`

No son "malos": son contenedores **sin significado**. Úsalos cuando necesites agrupar con fines de estilo o script y ninguna etiqueta semántica encaje.

```html
<div class="tarjeta-grid">
  <article class="tarjeta">…</article>
  <article class="tarjeta">…</article>
</div>
```

### ARIA: primera regla

Si existe una etiqueta HTML nativa con el comportamiento que necesitas, **úsala en lugar de ARIA**. Añadir `role="button"` a un `<div>` es peor que usar `<button>`. Detalle en [[08 - Accesibilidad]].

### Imágenes decorativas

Si la imagen no aporta información, deja el `alt` **vacío** (no lo omitas): así los lectores de pantalla la ignoran.

```html
<img src="adorno.svg" alt="">
```

### Contenido generado dinámicamente

Si insertas HTML con JavaScript, las mismas reglas aplican: semántica, `alt`, `label`, etc. La calidad no depende de cómo se genera el HTML, sino de cómo queda.

### Atributos booleanos

Basta con escribir el nombre; no necesitan valor.

```html
<input type="checkbox" checked disabled>
```

> [!warning] Obsoleto / legado
> Estas etiquetas y atributos están **obsoletos** en HTML5. Usa en su lugar CSS:
>
> | Obsoleto | Alternativa actual |
> |---|---|
> | `<font>`, `<center>`, `<big>`, `<strike>` | Propiedades CSS (`font-*`, `text-align`, `text-decoration`) |
> | `align`, `bgcolor`, `border` (en tablas e imágenes) | CSS (`text-align`, `background-color`, `border`) |
> | `<frame>`, `<frameset>` | `<iframe>` o layouts con CSS |
> | `<marquee>`, `<blink>` | Animaciones CSS |

---

## 9. Resumen

- **Semántica primero:** elige la etiqueta por su significado, no por su aspecto.
- **Separa responsabilidades:** HTML estructura, CSS presenta, JS se comporta.
- **Accesibilidad por defecto:** `alt`, `label`, `lang`, teclado y jerarquía de encabezados.
- **Documento completo:** `DOCTYPE`, `lang`, `charset`, `viewport` y `title` siempre.
- **Código limpio:** minúsculas, comillas dobles, indentación coherente, IDs únicos.
- **Prefiere lo nativo:** `<button>`, `<label>`, `<nav>`… antes que ARIA o `div` con eventos.
- **Valida y prueba:** validador W3C, navegación con teclado y Lighthouse.
- **Evita lo obsoleto:** nada de `<font>`, `<center>` ni atributos de presentación.