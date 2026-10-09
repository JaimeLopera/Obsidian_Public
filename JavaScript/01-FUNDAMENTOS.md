# 01 - Fundamentos

> [!info] ¿Qué es JavaScript?
> JavaScript es un lenguaje de programación. Es el que hace que una página web **haga cosas**: reaccionar a un clic, comprobar un formulario, cargar datos nuevos sin recargar la página, etc. Se ejecuta directamente en el navegador, sin instalar nada.

---

## 1. Antes de empezar

Lo único que necesitas es:

- Un **navegador** (Chrome, Firefox, Edge...).
- Un **editor de código** (por ejemplo VS Code).
- Saber lo básico de [[01 - Fundamentos|HTML]] y CSS, porque JavaScript trabaja sobre ellos.

> [!tip] Para probar código rápido
> Abre el navegador, pulsa `F12` y ve a la pestaña **Consola**. Ahí puedes escribir JavaScript y ejecutarlo al momento.

---

## 2. Concepto fundamental

Una página web se construye con tres piezas:

| Pieza | Para qué sirve |
|-------|----------------|
| HTML | El contenido y la estructura |
| CSS | El aspecto visual |
| JavaScript | El comportamiento |

JavaScript es un lenguaje **interpretado**: el navegador lee el código y lo ejecuta línea a línea, de arriba abajo, sin tener que compilarlo antes.

También es un lenguaje **de tipado dinámico**: una variable no necesita que le digas de qué tipo es, el propio lenguaje lo deduce según el valor que guarde.

### Dónde se ejecuta JavaScript

- **En el navegador**: para webs (es lo que veremos sobre todo en esta carpeta).
- **En el servidor con Node.js**: para crear servidores, herramientas, scripts, etc.

### JavaScript y ECMAScript

**ECMAScript** es el estándar, es decir, las reglas oficiales del lenguaje. **JavaScript** es la implementación que usan los navegadores. Cuando oigas "ES6" o "ES2015", se refiere a una versión del estándar que añadió muchas novedades importantes.

> [!warning] No confundir
> **JavaScript no es Java.** Son lenguajes distintos que solo comparten parte del nombre por motivos de marketing de los años 90.

---

## 3. Sintaxis / estructura

### Cómo añadir JavaScript a una página

Hay tres formas, pero solo una es la recomendada.

**a) Archivo externo (la recomendada)**

```html
<script src="script.js" defer></script>
```

**b) Dentro del propio HTML**

```html
<script>
  console.log("Hola desde el HTML");
</script>
```

**c) En un atributo de un elemento (evitar)**

```html
<button onclick="alert('Hola')">Pulsar</button>
```

> [!warning] Obsoleto / legado
> Poner JavaScript dentro de atributos como `onclick` mezcla el código con el HTML y es difícil de mantener. Hoy se usa `addEventListener` desde el archivo `.js` (se ve en [[10 - Eventos]]).

### El atributo `defer`

Hace que el navegador descargue el script mientras carga la página, pero lo ejecute **cuando el HTML ya está listo**. Así el script puede encontrar los elementos de la página sin problemas.

| Atributo | Cuándo se ejecuta |
|----------|-------------------|
| (ninguno) | Al encontrarlo, parando la carga de la página |
| `defer` | Cuando el HTML ya está cargado, respetando el orden |
| `async` | En cuanto se descarga, sin orden garantizado |
| `type="module"` | Como módulo, y se comporta como `defer` (ver [[14 - Módulos]]) |

### Reglas básicas del lenguaje

```js
// Esto es un comentario de una línea

/* Esto es un
   comentario de varias líneas */

console.log("Hola");   // Cada instrucción termina con ;
```

---

## 4. Elementos / propiedades / características

### Instrucciones (sentencias)

Cada orden que das al programa es una **instrucción**. Se suelen terminar con punto y coma `;`. Se ejecutan **en orden**, de arriba hacia abajo.

### Mayúsculas y minúsculas

JavaScript distingue entre mayúsculas y minúsculas (*case sensitive*). `nombre`, `Nombre` y `NOMBRE` son tres cosas distintas.

### Espacios y saltos de línea

Se ignoran casi siempre. Sirven solo para que el código sea más fácil de leer.

### Formas de mostrar información

| Instrucción | Qué hace | Cuándo usarla |
|-------------|----------|---------------|
| `console.log()` | Escribe en la consola del navegador | Para probar y depurar (la más usada) |
| `alert()` | Muestra una ventana emergente | Solo para pruebas rápidas |
| `prompt()` | Pide un dato al usuario con una ventana | Solo para pruebas rápidas |
| `document.write()` | Escribe directamente en la página | **No usar** (obsoleto) |

### Nombres válidos (identificadores)

Los nombres de variables y funciones:

- Pueden contener letras, números, `_` y `$`.
- **No pueden empezar por un número.**
- No pueden ser palabras reservadas (`if`, `for`, `function`, `class`...).
- Por convención se escriben en **camelCase**: `miPrimerNombre`.

### Modo estricto

Escribiendo `"use strict";` al principio de un archivo, JavaScript es más exigente y avisa de errores que normalmente deja pasar (como usar una variable que no has creado). Los **módulos** ya lo activan automáticamente.

---

## 5. Ejemplos prácticos

### Ejemplo básico: tu primer script

```js
console.log("Hola, mundo");
```

Abre la consola del navegador y verás el mensaje.

### Ejemplo habitual: estructura de un proyecto sencillo

Archivos:

```
mi-proyecto/
├── index.html
└── script.js
```

`index.html`:

```html
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <title>Mi primera página</title>
  <script src="script.js" defer></script>
</head>
<body>
  <h1 id="titulo">Hola</h1>
</body>
</html>
```

`script.js`:

```js
const titulo = document.getElementById("titulo");
titulo.textContent = "Hola desde JavaScript";
```

Al abrir la página, el título cambia solo. (Esto se explica a fondo en [[09 - DOM]]).

---

## 6. Buenas prácticas

- **Escribe el JavaScript en archivos `.js` separados**, no mezclado con el HTML.
- **Usa `defer`** al cargar el script en el `<head>`.
- **Termina las instrucciones con `;`** para evitar sorpresas.
- **Usa nombres claros** que expliquen lo que guardan (`precioTotal` mejor que `pt`).
- **Escribe en camelCase** los nombres de variables y funciones.
- **Comenta lo que no sea obvio**, pero sin llenar el código de comentarios inútiles.
- **Abre la consola con frecuencia** para ver errores y probar cosas.

---

## 7. Diferencias importantes

### JavaScript vs Java

| | JavaScript | Java |
|---|-----------|------|
| Uso principal | Webs (y servidores con Node) | Aplicaciones, Android, empresas |
| Tipado | Dinámico | Estático |
| Ejecución | Interpretado | Compilado |
| Relación | Ninguna, solo comparten parte del nombre | |

### JavaScript vs TypeScript

TypeScript es JavaScript con **tipos añadidos**. Se convierte a JavaScript antes de ejecutarse. Tiene su propia carpeta de notas.

### JavaScript en navegador vs Node.js

| | Navegador | Node.js |
|---|-----------|---------|
| Acceso a la página (DOM) | Sí | No |
| Acceso a archivos del ordenador | No | Sí |
| Objeto global | `window` | `global` |

---

## 8. Casos especiales

### Si el script no encuentra los elementos

Pasa cuando el script se ejecuta **antes** de que el HTML esté cargado. Soluciones:

- Poner `defer` en la etiqueta `<script>`.
- O colocar el `<script>` justo antes de cerrar `</body>`.

### Si tienes varios scripts

Se ejecutan en el orden en que aparecen en el HTML. Si uno depende de otro, el orden importa.

### Si el navegador tiene JavaScript desactivado

La página no podrá ejecutar tu código. Para contenido importante, puedes añadir un aviso con `<noscript>`:

```html
<noscript>Esta página necesita JavaScript para funcionar.</noscript>
```

---

## 9. Resumen

- JavaScript da **comportamiento** a las páginas web; HTML da estructura y CSS da estilo.
- Se ejecuta en el **navegador** y también en el **servidor** con Node.js.
- Es **interpretado** y de **tipado dinámico**.
- **ECMAScript** es el estándar; JavaScript es lo que usan los navegadores.
- Lo recomendado es usar un archivo externo con `<script src="..." defer>`.
- Distingue **mayúsculas y minúsculas**.
- Se usa **camelCase** para los nombres.
- `console.log()` es la herramienta básica para mostrar y probar cosas.
- **Evita** `onclick` en el HTML y `document.write()`.

---

⬅️ [[00 - Índice]] | ➡️ [[02 - Variables y tipos]]