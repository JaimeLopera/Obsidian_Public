# 11 - Formularios

> [!info] ¿Qué es trabajar con formularios en JavaScript?
> Es usar JavaScript para **leer lo que escribe el usuario**, **comprobar que es correcto** (validar) y **decidir qué hacer** con esos datos: mostrar errores, enviarlos a un servidor, guardarlos, etc. El formulario en sí se escribe en HTML; JavaScript le da la inteligencia.

---

## 1. Antes de empezar

Conviene haber visto:

- [[09 - DOM]]: para seleccionar los campos y mostrar mensajes.
- [[10 - Eventos]]: sobre todo `submit`, `input`, `change` y `preventDefault()`.
- El HTML de formularios (`<form>`, `<input>`, `<label>`, `<select>`...).

---

## 2. Concepto fundamental

Un formulario HTML, por defecto, **envía los datos al servidor y recarga la página**. Con JavaScript normalmente quieres **interceptar** ese envío para controlarlo tú.

El flujo habitual es:

1. El usuario rellena los campos.
2. Pulsa enviar → se dispara el evento **`submit`**.
3. Tu código llama a **`preventDefault()`** para evitar la recarga.
4. **Lees** los valores.
5. Los **validas**.
6. Si todo está bien, los **procesas** (por ejemplo, con `fetch`; ver [[13 - Fetch y APIs]]). Si no, muestras errores.

> [!warning] Nunca confíes solo en la validación del navegador
> La validación con JavaScript mejora la experiencia, pero **no es segura**: cualquiera puede saltársela. Los datos siempre deben validarse también en el servidor.

---

## 3. Sintaxis / estructura

### HTML de ejemplo

```html
<form id="formulario">
  <label for="nombre">Nombre</label>
  <input type="text" id="nombre" name="nombre" required>

  <label for="email">Email</label>
  <input type="email" id="email" name="email" required>

  <button type="submit">Enviar</button>
</form>
```

### Seleccionar el formulario y sus campos

```js
const formulario = document.querySelector("#formulario");
const nombre = document.querySelector("#nombre");
```

También se puede acceder por el atributo `name`:

```js
formulario.elements.nombre;
formulario.elements["email"];
```

### Escuchar el envío

```js
formulario.addEventListener("submit", (e) => {
  e.preventDefault();
  console.log(nombre.value);
});
```

---

## 4. Elementos / propiedades / características

### Leer valores según el tipo de campo

| Campo | Propiedad | Devuelve |
|-------|-----------|----------|
| `input` de texto, email, número... | `value` | Un **texto** |
| `textarea` | `value` | Un texto |
| `select` | `value` | El valor de la opción elegida |
| `checkbox` | `checked` | `true` o `false` |
| `radio` | `checked` (de cada uno) | `true` o `false` |
| `input type="file"` | `files` | Lista de archivos |

> [!warning] `value` siempre es texto
> Aunque el input sea de tipo `number`, `value` devuelve un **string**. Conviértelo con `Number()` si lo necesitas.

```js
const edad = Number(inputEdad.value);
```

### Checkbox y radio

```js
aceptar.checked;   // true o false

const elegido = document.querySelector('input[name="color"]:checked');
elegido?.value;    // valor del radio seleccionado (o undefined si no hay)
```

### `select`

```js
selector.value;              // valor elegido
selector.selectedIndex;      // posición elegida
selector.options;            // todas las opciones
```

### Cambiar valores desde JavaScript

```js
nombre.value = "Ana";
aceptar.checked = true;
formulario.reset();       // vacía el formulario
```

### Eventos de los formularios

| Evento | Cuándo ocurre |
|--------|---------------|
| `submit` | Se envía el formulario |
| `input` | El valor cambia **mientras escribes** |
| `change` | El valor cambia y **se confirma** |
| `focus` / `blur` | El campo gana / pierde el foco |
| `reset` | Se vacía el formulario |
| `invalid` | Un campo no pasa la validación del navegador |

### `FormData`

Recoge **todos los campos de una vez** usando el atributo `name`:

```js
const datos = new FormData(formulario);

datos.get("nombre");                          // un valor concreto
const objeto = Object.fromEntries(datos);     // { nombre: "...", email: "..." }
```

Es la forma más cómoda de leer formularios grandes.

### Validación con HTML

El navegador valida solo con estos atributos:

| Atributo | Qué comprueba |
|----------|---------------|
| `required` | Que no esté vacío |
| `minlength` / `maxlength` | Longitud del texto |
| `min` / `max` | Rango de números o fechas |
| `pattern` | Que cumpla una expresión regular |
| `type="email"` / `"url"` | Formato de email o URL |

### API de validación

JavaScript puede consultar y controlar esa validación:

```js
nombre.validity.valid;           // true si es válido
nombre.validity.valueMissing;    // true si falta (required)
nombre.validationMessage;        // mensaje del navegador
nombre.checkValidity();          // comprueba y devuelve true/false
formulario.reportValidity();     // comprueba y muestra los mensajes
nombre.setCustomValidity("Mensaje propio");
```

Para vaciar un mensaje personalizado: `setCustomValidity("")`.

### Validación personalizada

Cuando las reglas del HTML no bastan, se programa a mano:

```js
function validar() {
  const errores = [];

  if (nombre.value.trim().length < 3) {
    errores.push("El nombre debe tener al menos 3 caracteres");
  }

  if (!email.value.includes("@")) {
    errores.push("Email no válido");
  }

  return errores;
}
```

### Mostrar errores

```js
const mensaje = document.querySelector("#error");
mensaje.textContent = errores.join(". ");
```

Se puede marcar el campo con una clase (`campo.classList.add("invalido")`) y estilizarlo con CSS.

### Pseudoclases CSS de validación

El CSS puede reaccionar al estado del campo:

```css
input:invalid { border-color: red; }
input:valid { border-color: green; }
```

### Desactivar el formulario o campos

```js
boton.disabled = true;     // evita envíos repetidos
campo.readOnly = true;     // se ve pero no se edita
```

### Enviar los datos con `fetch`

```js
const respuesta = await fetch("/api/contacto", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify(Object.fromEntries(new FormData(formulario)))
});
```

Se explica en [[13 - Fetch y APIs]] y [[17 - JSON]].

---

## 5. Ejemplos prácticos

### Ejemplo básico: leer un valor

```js
formulario.addEventListener("submit", (e) => {
  e.preventDefault();
  console.log("Nombre:", nombre.value);
});
```

### Ejemplo habitual: validar y mostrar errores

```js
formulario.addEventListener("submit", (e) => {
  e.preventDefault();

  const errores = [];
  if (nombre.value.trim() === "") errores.push("Escribe tu nombre");
  if (!email.value.includes("@")) errores.push("Email no válido");

  if (errores.length > 0) {
    mensaje.textContent = errores.join(". ");
    return;
  }

  mensaje.textContent = "";
  const datos = Object.fromEntries(new FormData(formulario));
  console.log("Enviando", datos);
});
```

### Ejemplo: validar mientras se escribe

```js
email.addEventListener("input", () => {
  if (email.validity.typeMismatch) {
    email.setCustomValidity("Escribe un email válido");
  } else {
    email.setCustomValidity("");
  }
});
```

---

## 6. Buenas prácticas

- **Usa `preventDefault()`** en `submit` cuando quieras controlar el envío.
- **Escucha `submit` en el formulario**, no `click` en el botón: así funciona también al pulsar Enter.
- **Usa `FormData`** para leer varios campos de golpe.
- **Define `name` en todos los campos** para que `FormData` funcione.
- **Asocia cada campo con su `<label>`** (`for` + `id`) por accesibilidad.
- **Usa `trim()`** para quitar espacios sobrantes antes de validar.
- **Convierte los tipos**: `value` es siempre texto.
- **Aprovecha la validación nativa** (`required`, `type="email"`, `pattern`...) antes de escribir la tuya.
- **Muestra los errores cerca del campo** y con mensajes claros.
- **Valida siempre también en el servidor.**
- **Desactiva el botón** mientras se envía para evitar envíos duplicados.
- **No guardes contraseñas** en variables más tiempo del necesario ni las muestres en la consola.

---

## 7. Diferencias importantes

### `input` vs `change`

- `input`: en cada pulsación (ideal para validar en vivo).
- `change`: al confirmar el cambio (al salir del campo).

### `submit` en el formulario vs `click` en el botón

`submit` se dispara también al pulsar Enter en un campo y respeta la validación nativa. `click` no.

### `value` vs `checked`

- `value`: texto de campos de escritura y `select`.
- `checked`: estado de checkbox y radio.

### `FormData` vs leer campo por campo

| | `FormData` | Campo por campo |
|---|-----------|-----------------|
| Cantidad de código | Poco | Mucho |
| Necesita `name` | Sí | No |
| Ideal para | Formularios grandes | Uno o dos campos |

### `checkValidity()` vs `reportValidity()`

- `checkValidity()`: solo comprueba y devuelve `true`/`false`.
- `reportValidity()`: además **muestra** los mensajes del navegador.

### Validación del cliente vs del servidor

- **Cliente (JavaScript)**: comodidad y rapidez para el usuario.
- **Servidor**: seguridad real. Imprescindible.

---

## 8. Casos especiales

### Varios checkbox con el mismo `name`

Para obtener todos los marcados:

```js
const marcados = [...document.querySelectorAll('input[name="hobby"]:checked')]
  .map(c => c.value);
```

### `select` múltiple

```js
const elegidos = [...selector.selectedOptions].map(o => o.value);
```

### Archivos

```js
const archivo = inputArchivo.files[0];
console.log(archivo.name, archivo.size, archivo.type);
```

### Contraseña: mostrar y ocultar

```js
inputPassword.type = verPassword.checked ? "text" : "password";
```

### Desactivar la validación nativa

Con `novalidate` en el `<form>` para encargarte tú de todo:

```html
<form novalidate>
```

### Formulario sin recargar la página

Es lo habitual con `preventDefault()` + `fetch`.

### Autocompletado

El atributo `autocomplete` ayuda a los gestores de contraseñas y navegadores (`autocomplete="email"`, `"current-password"`...).

### Evitar envíos repetidos

```js
boton.disabled = true;
// ...tras terminar el envío
boton.disabled = false;
```

### Campos numéricos con decimales

Hay que usar `step="any"` en el HTML o el navegador rechazará valores decimales.

### Formularios dinámicos

Si añades campos con JavaScript, usa **delegación de eventos** (ver [[10 - Eventos]]).

---

## 9. Resumen

- Un formulario por defecto **recarga la página**; con JavaScript se intercepta con **`submit` + `preventDefault()`**.
- Escucha **`submit` en el `<form>`**, no `click` en el botón.
- Lee los valores con **`value`** (texto) y **`checked`** (checkbox y radio). `value` **siempre es un string**.
- **`FormData`** + `Object.fromEntries()` recoge todos los campos con `name`.
- La **validación nativa** (`required`, `type="email"`, `pattern`...) se complementa con la **API de validación** (`validity`, `setCustomValidity`, `reportValidity`).
- Para reglas propias, valida a mano y **muestra los errores** cerca del campo.
- Usa **`input`** para validar en vivo y **`change`** para cuando se confirma.
- Los datos se envían con **`fetch`**.
- La validación en el navegador **no es segura**: **siempre** hay que validar también en el servidor.
- Usa `<label>`, `name` y `autocomplete` correctamente por accesibilidad y comodidad.

---

⬅️ [[10 - Eventos]] | ➡️ [[12 - Asincronía]]