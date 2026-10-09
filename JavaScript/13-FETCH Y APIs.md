# 13 - Fetch y APIs

> [!info] ¿Qué es `fetch`?
> `fetch()` es la función de JavaScript para **pedir o enviar datos a un servidor** a través de internet, sin recargar la página. Se usa sobre todo para hablar con **APIs**: servicios que ofrecen datos (usuarios, productos, el tiempo, etc.) en un formato que los programas pueden leer, normalmente JSON.

---

## 1. Antes de empezar

Conviene haber visto:

- [[12 - Asincronía]]: `fetch` devuelve una **promesa**, así que necesitas entender `async/await` y `try...catch`.
- [[08 - Objetos]] y [[07 - Arrays]]: los datos de una API suelen llegar como objetos y arrays.
- [[17 - JSON]]: el formato en que casi siempre viajan los datos.

> [!tip] Recordatorio rápido de promesas
> Una **promesa** es un valor que llegará en el futuro. Con `await` esperas ese valor dentro de una función `async` sin bloquear la página. Todo el detalle está en [[12 - Asincronía]].

---

## 2. Concepto fundamental

### Qué es una API

Una **API** (*Application Programming Interface*) es una forma que ofrece un programa para que otros programas hablen con él. En web, normalmente es una **dirección (URL)** a la que pides datos.

Imagina un restaurante: tú (el cliente) haces un pedido al camarero (la API) y este te trae lo que hay en cocina (el servidor) sin que tengas que entrar tú. Tú solo conoces **qué puedes pedir** y **cómo pedirlo**.

### Cómo funciona una petición

1. Tu código envía una **petición** (*request*) a una URL.
2. El servidor la procesa.
3. Devuelve una **respuesta** (*response*) con un **código de estado** y, normalmente, datos.

### Métodos HTTP

| Método | Para qué sirve | Ejemplo |
|--------|----------------|---------|
| `GET` | **Leer** datos | Obtener la lista de usuarios |
| `POST` | **Crear** algo nuevo | Registrar un usuario |
| `PUT` | **Reemplazar** algo entero | Actualizar todos los datos de un usuario |
| `PATCH` | Modificar **una parte** | Cambiar solo el email |
| `DELETE` | **Borrar** | Eliminar un usuario |

### Códigos de estado

| Rango | Significado | Ejemplos |
|-------|-------------|----------|
| `2xx` | Todo bien | `200` OK, `201` creado |
| `3xx` | Redirección | `301` movido |
| `4xx` | Error del **cliente** | `400` petición mala, `401` sin permiso, `404` no encontrado |
| `5xx` | Error del **servidor** | `500` error interno |

Más detalle de HTTP y REST en [[01 - HTTP]] y [[02 - APIs REST]] de Conceptos generales.

---

## 3. Sintaxis / estructura

### Petición `GET` básica

```js
const respuesta = await fetch("https://api.ejemplo.com/usuarios");
const datos = await respuesta.json();
console.log(datos);
```

Son **dos pasos** con `await`:

1. `fetch()` espera a que llegue la **respuesta** (cabeceras y estado).
2. `.json()` espera a leer el **cuerpo** y convertirlo en objeto de JavaScript.

### Con `async/await` y control de errores

```js
async function cargarUsuarios() {
  try {
    const respuesta = await fetch("https://api.ejemplo.com/usuarios");

    if (!respuesta.ok) {
      throw new Error(`Error ${respuesta.status}`);
    }

    const datos = await respuesta.json();
    return datos;
  } catch (error) {
    console.error("No se pudo cargar:", error.message);
  }
}
```

### Con `.then()` (alternativa)

```js
fetch("https://api.ejemplo.com/usuarios")
  .then(r => r.json())
  .then(datos => console.log(datos))
  .catch(error => console.error(error));
```

### Petición `POST` enviando JSON

```js
const respuesta = await fetch("https://api.ejemplo.com/usuarios", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({ nombre: "Ana", edad: 20 })
});
```

---

## 4. Elementos / propiedades / características

### El segundo argumento: opciones

```js
fetch(url, {
  method: "POST",
  headers: { ... },
  body: "...",
});
```

| Opción | Qué hace |
|--------|----------|
| `method` | El método HTTP (`GET` por defecto) |
| `headers` | Cabeceras de la petición |
| `body` | Los datos que envías (en `POST`, `PUT`, `PATCH`) |
| `signal` | Para poder cancelar la petición |
| `credentials` | Si se envían cookies (`"include"`) |
| `mode` | Modo CORS (`"cors"` por defecto) |
| `cache` | Cómo usar la caché |

### La respuesta (`Response`)

| Propiedad / método | Qué da |
|--------------------|--------|
| `ok` | `true` si el estado es 200-299 |
| `status` | Código numérico (`200`, `404`...) |
| `statusText` | Texto del estado (`"OK"`) |
| `headers` | Cabeceras de la respuesta |
| `url` | URL final |
| `json()` | Cuerpo convertido desde JSON |
| `text()` | Cuerpo como texto |
| `blob()` | Cuerpo como archivo binario (imágenes, PDFs) |
| `formData()` | Cuerpo como `FormData` |

> [!warning] El cuerpo solo se puede leer una vez
> Si llamas a `respuesta.json()` y luego a `respuesta.text()`, la segunda falla. Si necesitas ambos, usa `respuesta.clone()` antes.

### `fetch` NO falla con errores HTTP

Esto sorprende a casi todos. `fetch` **solo da error** (rechaza la promesa) si hay un **fallo de red** (sin internet, servidor caído, CORS bloqueado). Con un `404` o un `500` **no lanza error**: la promesa se cumple con normalidad.

Por eso hay que comprobar **siempre** `respuesta.ok`:

```js
if (!respuesta.ok) {
  throw new Error(`Error HTTP: ${respuesta.status}`);
}
```

### Cabeceras (headers)

Información extra sobre la petición.

```js
headers: {
  "Content-Type": "application/json",
  "Authorization": "Bearer MI_TOKEN"
}
```

| Cabecera | Para qué |
|----------|----------|
| `Content-Type` | Qué formato tienen los datos que envías |
| `Accept` | Qué formato quieres recibir |
| `Authorization` | Credenciales o token de acceso |

### Parámetros en la URL

Hay dos formas habituales:

**Parámetros de ruta**: identifican un recurso.

```js
fetch(`https://api.ejemplo.com/usuarios/${id}`);
```

**Parámetros de consulta (*query string*)**: filtros y opciones, tras `?`.

```js
fetch("https://api.ejemplo.com/usuarios?pais=ES&limite=10");
```

Para construirlos de forma segura:

```js
const parametros = new URLSearchParams({ pais: "ES", limite: 10 });
fetch(`https://api.ejemplo.com/usuarios?${parametros}`);
```

### Enviar formularios y archivos

```js
const datos = new FormData(formulario);   // ver 11 - Formularios

fetch("/api/subir", {
  method: "POST",
  body: datos    // no pongas Content-Type: lo establece el navegador
});
```

### Autenticación

Muchas APIs piden una **clave (API key)** o un **token**:

```js
headers: { "Authorization": "Bearer TU_TOKEN" }
```

> [!warning] Nunca pongas claves secretas en el JavaScript del navegador
> Cualquiera puede verlas en las herramientas del desarrollador. Las claves privadas deben usarse solo desde el servidor.

### CORS

El navegador, por seguridad, **bloquea** las peticiones a un dominio distinto al de tu página, **a menos que el servidor lo permita** con unas cabeceras especiales (*CORS*). Si ves un error de CORS en la consola, el problema está en el **servidor**, no en tu `fetch`.

### Cancelar una petición

```js
const controlador = new AbortController();

fetch(url, { signal: controlador.signal });

controlador.abort();   // cancela
```

### Peticiones en paralelo

```js
const [usuarios, productos] = await Promise.all([
  fetch("/api/usuarios").then(r => r.json()),
  fetch("/api/productos").then(r => r.json())
]);
```

### Mostrar los datos en la página

Una vez tienes los datos, se pintan usando el DOM (ver [[09 - DOM]]):

```js
const lista = document.querySelector("#lista");

for (const usuario of usuarios) {
  const li = document.createElement("li");
  li.textContent = usuario.nombre;
  lista.append(li);
}
```

### Estados de la interfaz

Una buena pantalla que carga datos maneja **tres estados**:

1. **Cargando**: mostrar un indicador.
2. **Éxito**: mostrar los datos.
3. **Error**: mostrar un mensaje claro.

### Alternativa: `XMLHttpRequest` y librerías

> [!warning] Obsoleto / legado
> `XMLHttpRequest` (XHR) es la forma antigua de hacer peticiones. Hoy se usa **`fetch`**. Otra opción popular es la librería **Axios**, pero `fetch` suele bastar.

---

## 5. Ejemplos prácticos

### Ejemplo básico: obtener datos

```js
async function obtenerUsuarios() {
  const respuesta = await fetch("https://jsonplaceholder.typicode.com/users");
  const usuarios = await respuesta.json();
  console.log(usuarios);
}

obtenerUsuarios();
```

### Ejemplo habitual: cargar y pintar con estados

```js
const lista = document.querySelector("#lista");
const mensaje = document.querySelector("#mensaje");

async function cargar() {
  mensaje.textContent = "Cargando...";

  try {
    const respuesta = await fetch("https://jsonplaceholder.typicode.com/users");
    if (!respuesta.ok) throw new Error(`Error ${respuesta.status}`);

    const usuarios = await respuesta.json();

    for (const u of usuarios) {
      const li = document.createElement("li");
      li.textContent = u.name;
      lista.append(li);
    }

    mensaje.textContent = "";
  } catch (error) {
    mensaje.textContent = "No se pudieron cargar los datos";
    console.error(error);
  }
}

cargar();
```

### Ejemplo: enviar datos con `POST`

```js
async function crearUsuario(datos) {
  const respuesta = await fetch("https://api.ejemplo.com/usuarios", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(datos)
  });

  if (!respuesta.ok) throw new Error("No se pudo crear");
  return await respuesta.json();
}
```

---

## 6. Buenas prácticas

- **Comprueba siempre `respuesta.ok`**: `fetch` no falla con un 404 o 500.
- **Usa `async/await` con `try...catch`.**
- **Muestra los tres estados**: cargando, éxito y error.
- **Usa `JSON.stringify()`** para el `body` y pon `Content-Type: application/json`.
- **Construye la URL con `URLSearchParams`** en lugar de pegar textos a mano.
- **No expongas claves secretas** en el código del navegador.
- **Pon la URL base en una constante** para no repetirla.
- **Crea funciones pequeñas** y reutilizables para cada petición.
- **Usa `Promise.all`** para peticiones independientes.
- **Cancela las peticiones** que ya no hagan falta (por ejemplo, en un buscador).
- **No confíes en los datos recibidos**: valida su formato antes de usarlos.
- **Usa `textContent`** al pintar datos externos para evitar inyección de código (ver [[09 - DOM]]).

---

## 7. Diferencias importantes

### `fetch` vs `XMLHttpRequest` vs Axios

| | `fetch` | `XMLHttpRequest` | Axios |
|---|---------|------------------|-------|
| Basado en promesas | Sí | No (callbacks) | Sí |
| Viene en el navegador | Sí | Sí | No, se instala |
| Lanza error en 404/500 | **No** | No | **Sí** |
| Convierte JSON solo | No (`.json()`) | No | Sí |
| Estado actual | **Estándar** | Obsoleto | Opcional |

### `GET` vs `POST`

- `GET`: **pide** datos; los parámetros van en la URL; no lleva `body`.
- `POST`: **envía** datos; van en el `body`.

### `PUT` vs `PATCH`

- `PUT`: sustituye el recurso **completo**.
- `PATCH`: cambia **solo los campos** que envías.

### `json()` vs `text()`

- `json()`: convierte el cuerpo a objeto de JavaScript. Falla si no es JSON válido.
- `text()`: lo devuelve tal cual, como texto.

### Error de red vs error HTTP

| | Error de red | Error HTTP (404, 500) |
|---|-------------|-----------------------|
| ¿`fetch` rechaza la promesa? | Sí (va al `catch`) | **No** |
| Cómo detectarlo | `catch` | `respuesta.ok` / `status` |

### Parámetros de ruta vs de consulta

- **Ruta** (`/usuarios/5`): identifican **un recurso concreto**.
- **Consulta** (`?pais=ES`): **filtran u ordenan**.

---

## 8. Casos especiales

### La respuesta no es JSON

Si la API devuelve texto, HTML o vacío, `.json()` lanza error. Revisa antes el tipo de contenido:

```js
const tipo = respuesta.headers.get("content-type");
if (tipo?.includes("application/json")) { ... }
```

### Respuestas vacías (`204 No Content`)

Con `DELETE` es habitual que no haya cuerpo. No llames a `.json()` en ese caso.

### Peticiones lentas o que no responden

`fetch` no tiene *timeout* propio. Se puede añadir con `AbortController`:

```js
const controlador = new AbortController();
const temporizador = setTimeout(() => controlador.abort(), 5000);

try {
  const r = await fetch(url, { signal: controlador.signal });
} finally {
  clearTimeout(temporizador);
}
```

### Paginación

Muchas APIs devuelven los datos por páginas:

```js
fetch("/api/productos?pagina=2&limite=20");
```

### Límites de uso (rate limits)

Las APIs limitan cuántas peticiones puedes hacer. Si superas el límite, devuelven `429 Too Many Requests`. Evita pedir lo mismo repetidamente: guarda los resultados.

### Guardar resultados para no repetir peticiones

Puedes guardarlos en una variable o en `localStorage` (ver [[16 - Web Storage]]).

### Subir archivos

Usa `FormData` y **no** pongas `Content-Type` a mano: el navegador lo configura con el *boundary* correcto.

### Cookies y sesión

Para enviar cookies a otro dominio se usa `credentials: "include"`, y el servidor debe permitirlo.

### Probar con APIs públicas

Para practicar sin servidor propio existen APIs gratuitas como JSONPlaceholder, PokéAPI o Open-Meteo.

### Entorno de pruebas

Herramientas como la pestaña **Red (Network)** de las herramientas del desarrollador permiten ver cada petición, su estado y su respuesta.

---

## 9. Resumen

- **`fetch(url, opciones)`** pide o envía datos a un servidor y devuelve una **promesa**.
- Una **API** es una URL con la que programas hablan entre sí; los datos suelen viajar en **JSON** (ver [[17 - JSON]]).
- Se hace en **dos pasos**: `await fetch(...)` y luego `await respuesta.json()`.
- **`fetch` no falla con errores 404 o 500**: comprueba siempre **`respuesta.ok`**.
- Solo va al `catch` ante fallos de red.
- Métodos principales: **`GET`** (leer), **`POST`** (crear), **`PUT`/`PATCH`** (actualizar), **`DELETE`** (borrar).
- Para enviar JSON: **`method`**, **`headers`** con `Content-Type` y **`body: JSON.stringify(...)`**.
- Usa **`URLSearchParams`** para los parámetros de consulta.
- Maneja los **tres estados**: cargando, éxito y error.
- **CORS** es una restricción del navegador que se arregla en el servidor.
- **Nunca pongas claves secretas** en el JavaScript del navegador.
- `AbortController` permite **cancelar** peticiones y crear *timeouts*.

---

⬅️ [[12 - Asincronía]] | ➡️ [[14 - Módulos]]