# HTML - Formularios

> [!info] ¿Qué es?
> Un **formulario** (`<form>`) es la zona de una página donde la persona usuaria **introduce datos y los envía**: un registro, un inicio de sesión, un buscador, un pedido. Se compone de un contenedor `<form>` y de **controles** (campos de texto, casillas, listas desplegables, botones) que recogen la información. Al enviarlo, el navegador empaqueta los datos como pares `nombre=valor` y los manda a una dirección.

---

## 1. Antes de empezar

Conviene dominar antes:

- La sintaxis de etiquetas y atributos, y los elementos vacíos como `<input>`: [[HTML/01 - Fundamentos]].
- Los atributos `id` y `class`, que se usan para conectar elementos: [[HTML/07 - Atributos]].

> [!tip] Lo que HTML hace y no hace
> HTML **recoge y envía** los datos. Lo que ocurre después (guardarlos, procesarlos, responder) lo hace un programa en el **servidor** ([[PHP/09 - Formularios]], [[Node.js y npm/04 - Servidor con Express]]) o un script en el navegador ([[JavaScript/11 - Formularios]]).

---

## 2. Concepto fundamental

Un formulario funciona con cuatro ideas:

- **El contenedor `<form>`** define a dónde y cómo se envían los datos (`action` y `method`).
- **Los controles** recogen los datos. Cada uno lleva un atributo **`name`**: sin `name`, el dato **no se envía**.
- **Las etiquetas `<label>`** describen cada control y lo hacen accesible.
- **El botón de envío** dispara el envío; antes, el navegador ejecuta la **validación nativa**.

Los datos viajan como pares nombre-valor. Por ejemplo, un campo `<input name="email">` con el valor `ana@ejemplo.com` se envía como `email=ana@ejemplo.com`.

> [!note] `id` frente a `name`
> `id` identifica el elemento **en la página** (para `label`, CSS y JavaScript). `name` identifica el dato **en el envío**. Suelen escribirse iguales, pero cumplen funciones distintas, y en grupos de botones de radio el `name` se comparte mientras que los `id` deben ser únicos.

---

## 3. Sintaxis / estructura

### 3.1 Estructura mínima

```html
<form action="/registro" method="post">
  <label for="nombre">Nombre</label>
  <input type="text" id="nombre" name="nombre">

  <button type="submit">Enviar</button>
</form>
```

### 3.2 Etiquetar un control

Hay dos formas válidas de asociar `<label>` con su control:

```html
<!-- Con for e id -->
<label for="correo">Correo</label>
<input type="email" id="correo" name="correo">

<!-- Envolviendo el control -->
<label>
  Correo
  <input type="email" name="correo">
</label>
```

### 3.3 Casillas y botones de radio

```html
<!-- Casilla: se puede marcar o no -->
<label>
  <input type="checkbox" name="acepto" value="si" required>
  Acepto las condiciones
</label>

<!-- Radio: solo una opción del grupo (mismo name) -->
<fieldset>
  <legend>Nivel</legend>
  <label><input type="radio" name="nivel" value="basico" checked> Básico</label>
  <label><input type="radio" name="nivel" value="avanzado"> Avanzado</label>
</fieldset>
```

### 3.4 Lista desplegable y área de texto

```html
<label for="pais">País</label>
<select id="pais" name="pais">
  <option value="">Selecciona un país</option>
  <option value="es">España</option>
  <option value="mx">México</option>
</select>

<label for="mensaje">Mensaje</label>
<textarea id="mensaje" name="mensaje" rows="5" cols="40"></textarea>
```

### 3.5 Botones

```html
<button type="submit">Enviar</button>
<button type="reset">Borrar todo</button>
<button type="button">Acción sin enviar</button>
```

---

## 4. Elementos / propiedades / características

### 4.1 Elementos del formulario

| Elemento | Función |
|----------|---------|
| `<form>` | Contenedor del formulario |
| `<label>` | Etiqueta de un control |
| `<input>` | Control de entrada; su comportamiento depende de `type` (elemento vacío) |
| `<textarea>` | Texto de varias líneas |
| `<select>` | Lista desplegable |
| `<option>` | Opción de un `<select>` o de un `<datalist>` |
| `<optgroup>` | Agrupa opciones dentro de un `<select>` |
| `<datalist>` | Lista de sugerencias para un `<input>` |
| `<button>` | Botón |
| `<fieldset>` y `<legend>` | Agrupan controles relacionados y les dan un título |
| `<output>` | Muestra el resultado de un cálculo |
| `<progress>` | Barra de progreso de una tarea |
| `<meter>` | Valor dentro de un rango conocido (por ejemplo, un nivel de disco) |

### 4.2 Atributos de `<form>`

| Atributo | Función | Valores |
|----------|---------|---------|
| `action` | Dirección a la que se envían los datos | URL; si se omite, la propia página |
| `method` | Método HTTP del envío | `get`, `post`, `dialog` |
| `enctype` | Codificación de los datos (solo con `post`) | `application/x-www-form-urlencoded` (por defecto), `multipart/form-data` (archivos), `text/plain` |
| `target` | Dónde se muestra la respuesta | `_self`, `_blank`... |
| `autocomplete` | Autocompletado del formulario | `on`, `off` |
| `novalidate` | Desactiva la validación nativa al enviar | Atributo booleano |

### 4.3 Valores de `type` en `<input>`

| Grupo | Tipos |
|-------|-------|
| **Texto** | `text`, `password`, `email`, `url`, `tel`, `search` |
| **Números** | `number`, `range` |
| **Fecha y hora** | `date`, `time`, `datetime-local`, `month`, `week` |
| **Elección** | `checkbox`, `radio`, `color` |
| **Archivos** | `file` |
| **Botones** | `submit`, `reset`, `button`, `image` |
| **Ocultos** | `hidden` |

Cada tipo cambia el teclado en móvil, la validación y la interfaz que muestra el navegador. Si un navegador no conoce un tipo, lo trata como `text`.

### 4.4 Atributos comunes de los controles

| Atributo | Función |
|----------|---------|
| `name` | Nombre con el que se envía el dato |
| `value` | Valor inicial (en `checkbox` y `radio`, el valor que se envía si están marcados) |
| `id` | Identificador único, para enlazar con `<label>` |
| `placeholder` | Texto de ayuda dentro del campo vacío |
| `required` | El campo es obligatorio |
| `disabled` | Desactiva el control: no se puede usar **ni se envía** |
| `readonly` | No se puede editar, pero **sí se envía** |
| `autofocus` | Recibe el foco al cargar la página |
| `autocomplete` | Pista de autocompletado (`email`, `name`, `tel`, `current-password`, `new-password`...) |
| `form` | `id` del formulario al que pertenece, aunque esté fuera de él |

### 4.5 Atributos de validación

| Atributo | Se aplica a | Función |
|----------|-------------|---------|
| `required` | Casi todos | No permite enviar si está vacío |
| `minlength` y `maxlength` | Texto, `textarea` | Longitud mínima y máxima |
| `min` y `max` | Números y fechas | Valor mínimo y máximo |
| `step` | Números y fechas | Salto permitido entre valores |
| `pattern` | Texto, `email`, `tel`, `url`, `password`, `search` | Expresión regular que debe cumplir el valor ([[Conceptos generales/04 - Regex]]) |
| `type` | `<input>` | `email`, `url` y `number` validan el formato por sí solos |
| `multiple` | `email`, `file`, `select` | Permite varios valores |
| `accept` | `file` | Tipos de archivo admitidos (`image/*`, `.pdf`) |

### 4.6 Atributos de los botones

| Atributo | Función |
|----------|---------|
| `type` | `submit` (envía), `reset` (restablece), `button` (sin acción por defecto) |
| `formaction`, `formmethod`, `formnovalidate` | Sustituyen los atributos del `<form>` solo para ese botón |

### 4.7 Estados de validación en CSS

Los navegadores marcan los controles con pseudoclases que se pueden estilar:

| Pseudoclase | Significado |
|-------------|-------------|
| `:required` / `:optional` | Campo obligatorio / opcional |
| `:valid` / `:invalid` | Cumple / no cumple las reglas |
| `:user-invalid` | No cumple las reglas **tras la interacción** de la persona usuaria |
| `:disabled` / `:enabled` | Desactivado / activado |
| `:checked` | Casilla o radio marcados |
| `:focus` | Control con el foco |

Más en [[CSS/12 - Pseudoclases y pseudoelementos]].

---

## 5. Ejemplos prácticos

### Ejemplo básico

Formulario de búsqueda:

```html
<form action="/buscar" method="get">
  <label for="q">Buscar</label>
  <input type="search" id="q" name="q" required>
  <button type="submit">Buscar</button>
</form>
```

Con `method="get"`, los datos aparecen en la URL: `/buscar?q=html`.

### Ejemplo habitual

Formulario de registro con validación nativa:

```html
<form action="/registro" method="post">
  <div>
    <label for="nombre">Nombre completo</label>
    <input type="text" id="nombre" name="nombre" autocomplete="name" required minlength="3">
  </div>

  <div>
    <label for="email">Correo electrónico</label>
    <input type="email" id="email" name="email" autocomplete="email" required>
  </div>

  <div>
    <label for="clave">Contraseña</label>
    <input type="password" id="clave" name="clave" autocomplete="new-password" required minlength="8">
  </div>

  <div>
    <label for="edad">Edad</label>
    <input type="number" id="edad" name="edad" min="18" max="99">
  </div>

  <div>
    <label>
      <input type="checkbox" name="condiciones" required>
      Acepto las condiciones de uso
    </label>
  </div>

  <button type="submit">Crear cuenta</button>
</form>
```

### Ejemplo completo

Formulario de inscripción con grupos, archivo, sugerencias y varios tipos de control:

```html
<form action="/inscripcion" method="post" enctype="multipart/form-data">
  <fieldset>
    <legend>Datos personales</legend>

    <label for="nombre">Nombre</label>
    <input type="text" id="nombre" name="nombre" required>

    <label for="telefono">Teléfono</label>
    <input type="tel" id="telefono" name="telefono" autocomplete="tel"
           pattern="[0-9]{9}" title="Introduce 9 dígitos sin espacios">

    <label for="nacimiento">Fecha de nacimiento</label>
    <input type="date" id="nacimiento" name="nacimiento" max="2008-12-31">
  </fieldset>

  <fieldset>
    <legend>Curso</legend>

    <label for="curso">Curso</label>
    <select id="curso" name="curso" required>
      <option value="">Selecciona un curso</option>
      <optgroup label="Desarrollo">
        <option value="html">HTML</option>
        <option value="js">JavaScript</option>
      </optgroup>
      <optgroup label="Datos">
        <option value="sql">SQL</option>
      </optgroup>
    </select>

    <p>Modalidad</p>
    <label><input type="radio" name="modalidad" value="presencial" required> Presencial</label>
    <label><input type="radio" name="modalidad" value="online"> Online</label>

    <label for="ciudad">Ciudad</label>
    <input type="text" id="ciudad" name="ciudad" list="ciudades">
    <datalist id="ciudades">
      <option value="Granada">
      <option value="Sevilla">
      <option value="Málaga">
    </datalist>

    <label for="nivel">Nivel de experiencia (1-10)</label>
    <input type="range" id="nivel" name="nivel" min="1" max="10" value="5">
  </fieldset>

  <fieldset>
    <legend>Documentación</legend>

    <label for="cv">Currículum (PDF)</label>
    <input type="file" id="cv" name="cv" accept=".pdf">

    <label for="comentarios">Comentarios</label>
    <textarea id="comentarios" name="comentarios" rows="4" maxlength="500"></textarea>
  </fieldset>

  <input type="hidden" name="origen" value="web">

  <button type="submit">Enviar inscripción</button>
  <button type="reset">Borrar</button>
</form>
```

La accesibilidad de los formularios (etiquetas, errores, foco) se amplía en [[HTML/08 - Accesibilidad]].

---

## 6. Buenas prácticas

- Asociar **cada control con un `<label>`** visible. El `placeholder` no sustituye a la etiqueta: desaparece al escribir y suele tener poco contraste.
- Dar a cada control un **`name`**, o el dato no se enviará.
- Elegir el **`type` más específico** (`email`, `tel`, `number`, `date`...): mejora el teclado en móvil y la validación.
- Usar `autocomplete` con los valores estándar (`name`, `email`, `current-password`, `new-password`) para facilitar el relleno automático.
- Agrupar controles relacionados con `<fieldset>` y `<legend>`, sobre todo los grupos de radio y casillas.
- Usar `required`, `minlength`, `min`, `max` y `pattern` para la **validación nativa**, y añadir un `title` explicativo a los `pattern`.
- **Validar siempre también en el servidor**: la validación del navegador mejora la experiencia, pero se puede saltar ([[Conceptos generales/05 - Seguridad web]]).
- Usar `method="post"` para datos que modifican algo o son sensibles, y `get` para búsquedas y filtros.
- Añadir `enctype="multipart/form-data"` cuando haya campos `type="file"`.
- Usar `<button type="submit">` para el envío y `type="button"` para botones que no deben enviar.
- Mantener los formularios **cortos**: pedir solo los datos necesarios.
- Indicar claramente cuáles son obligatorios y dar mensajes de error claros y cercanos al campo.
- No usar `autofocus` salvo cuando sea realmente útil: puede desorientar a quien usa lector de pantalla.
- Dar estilo con CSS ([[CSS/03 - Box Model]]) y mantener un tamaño de toque cómodo en móvil.

Más criterios de calidad en [[HTML/11 - Buenas prácticas]].

---

## 7. Diferencias importantes

| Comparación | Diferencia |
|-------------|------------|
| **`id` vs `name`** | `id` identifica el elemento en la página; `name` identifica el dato al enviarlo |
| **`get` vs `post`** | `get` añade los datos a la URL (visibles, limitados, se pueden guardar en favoritos); `post` los envía en el cuerpo de la petición |
| **`checkbox` vs `radio`** | `checkbox` permite marcar varias opciones; `radio` solo una por grupo (mismo `name`) |
| **`disabled` vs `readonly`** | `disabled` no se puede usar y **no se envía**; `readonly` no se edita pero **sí se envía** |
| **`<button>` vs `<input type="submit">`** | `<button>` admite contenido interno (texto con formato, iconos); `<input>` solo un `value` |
| **`placeholder` vs `<label>`** | `<label>` describe el campo de forma permanente y es accesible; `placeholder` es solo una pista que se borra |
| **`<select>` vs `<datalist>`** | `<select>` obliga a elegir una de sus opciones; `<datalist>` solo sugiere y deja escribir otro valor |
| **`type="text"` vs `type="number"`** | `number` valida que sea numérico y muestra controles de incremento, pero no es adecuado para códigos o teléfonos |
| **`type="submit"` vs `type="button"`** | `submit` envía el formulario; `button` no hace nada por sí solo |
| **Validación nativa vs servidor** | La del navegador es una ayuda para la persona usuaria; la del servidor es la que protege los datos |

---

## 8. Casos especiales

### Un `<button>` dentro de un formulario envía por defecto

El valor por defecto de `type` en `<button>` es `submit`. Un botón que solo debe ejecutar una acción necesita `type="button"`, o enviará el formulario sin querer.

### Las casillas sin marcar no se envían

Una casilla desmarcada **no aparece** en los datos enviados. Si el servidor necesita saber que fue "no", debe interpretar su ausencia. Los controles `disabled` tampoco se envían.

### Campos para números que no son números

Códigos postales, teléfonos o DNI no son cantidades: se comportan mejor con `type="text"` o `type="tel"` más `inputmode="numeric"` y `pattern`. `type="number"` elimina ceros iniciales y admite notación científica.

### Controles fuera del formulario

El atributo `form="id-del-formulario"` permite asociar un control o botón a un formulario aunque esté en otra parte del documento.

### Enviar con Intro

Un formulario con un solo campo de texto se envía al pulsar Intro. En formularios con varios campos, Intro envía si hay un botón de envío.

### Subir archivos

Para enviar archivos hay que usar `method="post"` y `enctype="multipart/form-data"`. Con `multiple` se admiten varios. El atributo `accept` solo orienta al navegador: el servidor debe volver a comprobar el tipo y el tamaño.

### Campos de contraseña

`type="password"` solo oculta los caracteres en pantalla. No cifra nada: la protección real exige HTTPS. Los gestores de contraseñas funcionan mejor con `autocomplete="current-password"` y `"new-password"`.

### Validación personalizada

Cuando la validación nativa no basta, JavaScript puede usar `setCustomValidity()` y la API de validación ([[JavaScript/11 - Formularios]]). El atributo `novalidate` desactiva la validación nativa si se prefiere gestionarla por completo con scripts.

### `method="dialog"`

Dentro de un elemento `<dialog>`, un formulario con `method="dialog"` cierra el cuadro de diálogo al enviarse, sin recargar la página ([[HTML/10 - APIs HTML]]).

> [!warning] Obsoleto / legado
> - `<isindex>` y `<keygen>` están eliminados.
> - Los tipos `datetime` y `datetime-local` antiguos con zona horaria (`type="datetime"`) están obsoletos: se usa `datetime-local`.
> - `<input type="image">` sigue siendo válido, pero hoy se prefiere `<button>` con una imagen dentro.
> - Los atributos de presentación como `align` en `<input>` están obsoletos: se usa **CSS**.
> - Maquetar formularios con tablas (una celda para la etiqueta y otra para el campo) está obsoleto: se usa **CSS** ([[CSS/07 - Flexbox]], [[CSS/08 - Grid]]).

---

## 9. Resumen

- Un `<form>` agrupa controles y los envía a la dirección `action` mediante el método `method` (`get` o `post`).
- Cada control necesita un **`name`** para enviarse, y un **`<label>`** para ser comprensible y accesible.
- `<input>` cambia según su `type` (`text`, `email`, `password`, `number`, `date`, `checkbox`, `radio`, `file`...); también existen `<textarea>`, `<select>`, `<datalist>` y `<button>`.
- `<fieldset>` y `<legend>` agrupan controles relacionados.
- La **validación nativa** usa `required`, `minlength`, `min`, `max`, `pattern` y el propio `type`, y se puede estilar con `:valid` e `:invalid`.
- `disabled` no envía el dato; `readonly` sí. Las casillas desmarcadas no se envían.
- Los archivos requieren `post` y `enctype="multipart/form-data"`.
- La validación del navegador **no sustituye** a la del servidor.