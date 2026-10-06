# 02 - APIs REST

> [!info] ¿Qué es?
> Una **API** es una "ventanilla" que ofrece un programa para que otros programas le pidan cosas (datos o acciones). **REST** es un conjunto de reglas para organizar esa ventanilla usando **HTTP**, de forma que sea ordenada, predecible y fácil de usar.

---

## 1. Antes de empezar

- Para entender esta nota ayuda saber cómo funciona HTTP: métodos (`GET`, `POST`…), códigos de estado (`200`, `404`…) y cabeceras. Si no lo tienes fresco, repasa [[01 - HTTP]].
- Los datos de las APIs REST casi siempre viajan en formato JSON. Si no lo recuerdas, mira [[03 - JSON]].
- Aquí aprenderás **cómo se diseña y se usa** una API REST. Cómo se consume desde JavaScript está en [[13 - Fetch y APIs]], y cómo se construye un servidor en [[04 - Servidor con Express]].

---

## 2. Concepto fundamental

### ¿Qué es una API?

**API** significa *Application Programming Interface* (interfaz de programación de aplicaciones). Es la forma en que un programa se comunica con otro.

> [!example] Analogía del restaurante
> - Tú (el **cliente**) miras la carta y haces un pedido.
> - El camarero (la **API**) lleva el pedido a la cocina y te trae el plato.
> - La cocina (el **servidor y la base de datos**) prepara las cosas, pero tú no entras en ella.
>
> La carta es la lista de cosas que la API permite pedir. No necesitas saber cómo se cocina, solo cómo pedir.

### ¿Qué es REST?

**REST** (*Representational State Transfer*) es un **estilo de diseño**, no un programa ni un protocolo. Propone organizar la API alrededor de **recursos** y usar HTTP de forma coherente.

Una API que sigue estas reglas se llama **API REST** o **RESTful**.

### La idea clave: recursos

Un **recurso** es "una cosa" que la API gestiona: un usuario, un producto, un pedido, un artículo.

- Cada recurso tiene una **dirección (URL)** que lo identifica.
- Se actúa sobre él con **métodos HTTP** (`GET` leer, `POST` crear, etc.).
- Se intercambia en un **formato** (normalmente JSON).

```text
Recurso:    un usuario
URL:        /usuarios/42
Acción:     GET  → leerlo
            PUT  → reemplazarlo
            DELETE → borrarlo
Formato:    JSON
```

### Los principios de REST

| Principio | Qué significa en sencillo |
|---|---|
| **Cliente-servidor** | Cliente y servidor están separados y evolucionan por su cuenta |
| **Sin estado** (*stateless*) | Cada petición lleva toda la información necesaria; el servidor no "recuerda" la anterior |
| **Interfaz uniforme** | Siempre se usan las mismas reglas: URLs de recursos + métodos HTTP estándar |
| **Representaciones** | El recurso se envía en un formato acordado (JSON, XML…), no el objeto interno del servidor |
| **Cacheable** | Las respuestas pueden indicar si se pueden guardar para reutilizarlas |
| **Capas** | Entre cliente y servidor puede haber intermediarios (proxies, balanceadores) sin que cambie nada |

---

## 3. Sintaxis / estructura

### 3.1 Estructura de una URL REST

```text
https://api.ejemplo.com/v1/usuarios/42/pedidos?estado=pendiente&pagina=2
└────────┬────────────┘└┬┘└───┬───┘└┬┘└──┬───┘└────────────┬───────────┘
      dominio        versión recurso id subrecurso      parámetros (query)
```

| Parte | Función |
|---|---|
| Dominio | Dónde está la API |
| `/v1` | Versión de la API (opcional pero recomendable) |
| `/usuarios` | Colección de recursos |
| `/42` | Un recurso concreto dentro de la colección |
| `/pedidos` | Recursos que pertenecen a ese usuario |
| `?estado=…&pagina=…` | Filtros, orden y paginación |

### 3.2 Patrón básico: colección y elemento

Para cada recurso hay **dos tipos de URL**:

| Tipo | URL | Qué representa |
|---|---|---|
| **Colección** | `/usuarios` | Todos los usuarios |
| **Elemento** | `/usuarios/42` | El usuario con id 42 |

### 3.3 Las operaciones CRUD y sus métodos

**CRUD** son las 4 operaciones básicas sobre datos: *Create, Read, Update, Delete*.

| Operación | Método | URL | Resultado habitual |
|---|---|---|---|
| Listar todos | `GET` | `/usuarios` | `200` + lista |
| Ver uno | `GET` | `/usuarios/42` | `200` + usuario (o `404`) |
| Crear | `POST` | `/usuarios` | `201` + usuario creado |
| Reemplazar entero | `PUT` | `/usuarios/42` | `200` o `204` |
| Modificar una parte | `PATCH` | `/usuarios/42` | `200` o `204` |
| Borrar | `DELETE` | `/usuarios/42` | `204` (sin cuerpo) |

> [!tip] Regla de oro
> **La URL dice QUÉ** (el recurso) y **el método dice QUÉ HACER** con él.

---

## 4. Elementos / propiedades / características

### 4.1 Cómo nombrar las URLs

| Regla | ✅ Bien | ❌ Mal |
|---|---|---|
| Usar **sustantivos**, no verbos | `/usuarios` | `/obtenerUsuarios` |
| Usar **plural** para colecciones | `/productos` | `/producto` |
| **Minúsculas** y guiones para separar palabras | `/tipos-de-pago` | `/TiposDePago` · `/tipos_de_pago` |
| Mostrar la **jerarquía** con `/` | `/usuarios/42/pedidos` | `/pedidosDelUsuario42` |
| **No** poner la extensión | `/usuarios/42` | `/usuarios/42.json` |
| **No** poner el método en la URL | `DELETE /usuarios/42` | `/usuarios/42/borrar` |

### 4.2 Los datos en una API

**¿Dónde viaja cada cosa?**

| Lugar | Para qué | Ejemplo |
|---|---|---|
| **Ruta** | Decir qué recurso es | `/usuarios/42` |
| **Query string** | Filtrar, ordenar, paginar, buscar | `/productos?categoria=libros&orden=precio` |
| **Cuerpo (body)** | Los datos a crear o modificar | `{"nombre": "Ana"}` |
| **Cabeceras** | Información extra: formato, credenciales | `Authorization: Bearer …` |

### 4.3 Formato de los datos (JSON)

```json
{
  "id": 42,
  "nombre": "Ana",
  "email": "ana@ejemplo.com",
  "activo": true
}
```

El cliente avisa con cabeceras de qué formato envía y cuál espera:

| Cabecera | Función |
|---|---|
| `Content-Type: application/json` | "Lo que te envío está en JSON" |
| `Accept: application/json` | "Quiero la respuesta en JSON" |

### 4.4 Códigos de estado más usados en APIs

| Código | Cuándo usarlo |
|---|---|
| `200 OK` | Todo bien, devuelvo datos |
| `201 Created` | He creado el recurso |
| `204 No Content` | Todo bien, no hay nada que devolver (típico en `DELETE`) |
| `400 Bad Request` | La petición está mal formada |
| `401 Unauthorized` | No estás identificado |
| `403 Forbidden` | Estás identificado pero no tienes permiso |
| `404 Not Found` | El recurso no existe |
| `409 Conflict` | Choca con algo existente (ej. email repetido) |
| `422 Unprocessable Content` | Los datos tienen formato correcto pero no son válidos |
| `429 Too Many Requests` | Has superado el límite de peticiones |
| `500 Internal Server Error` | Fallo del servidor |

La lista completa está en [[01 - HTTP]].

### 4.5 Filtrado, orden, búsqueda y paginación

Se hacen con **parámetros en la query string**:

| Función | Ejemplo |
|---|---|
| **Filtrar** | `/productos?categoria=libros` |
| **Ordenar** | `/productos?orden=precio` · `/productos?orden=-precio` (descendente) |
| **Buscar** | `/productos?q=python` |
| **Paginar** | `/productos?pagina=2&limite=20` |
| **Elegir campos** | `/usuarios?campos=id,nombre` |

La **paginación** evita enviar miles de resultados de golpe. Una respuesta paginada suele incluir información extra:

```json
{
  "datos": [ { "id": 21 }, { "id": 22 } ],
  "pagina": 2,
  "limite": 20,
  "total": 134
}
```

### 4.6 Versionado

Sirve para poder cambiar la API sin romper a quienes ya la usan.

| Estrategia | Ejemplo | Comentario |
|---|---|---|
| **En la URL** | `/v1/usuarios` | La más común y fácil de ver |
| **En una cabecera** | `Accept: application/vnd.api+json; version=1` | Más "limpia" pero menos visible |

### 4.7 Autenticación y autorización

| Concepto | Pregunta que responde |
|---|---|
| **Autenticación** | ¿Quién eres? |
| **Autorización** | ¿Qué tienes permiso para hacer? |

Formas habituales de identificarse:

| Método | Cómo funciona | Uso típico |
|---|---|---|
| **API Key** | Una clave fija enviada en una cabecera | APIs públicas sencillas |
| **Bearer Token (JWT)** | Un token enviado en `Authorization: Bearer <token>` | Aplicaciones con login |
| **OAuth 2.0** | El usuario autoriza a una app sin dar su contraseña | "Iniciar sesión con Google" |
| **Basic Auth** | Usuario y contraseña codificados en cada petición | Casos muy simples; solo con HTTPS |

### 4.8 Formato de errores

Un error útil explica **qué pasó**. Conviene usar siempre la misma estructura:

```json
{
  "error": "validacion",
  "mensaje": "El email no es válido",
  "detalles": [
    { "campo": "email", "problema": "formato incorrecto" }
  ]
}
```

---

## 5. Ejemplos prácticos

### Ejemplo básico: leer datos (`GET`)

**Petición**
```http
GET /api/v1/usuarios/42 HTTP/1.1
Host: api.ejemplo.com
Accept: application/json
```

**Respuesta**
```http
HTTP/1.1 200 OK
Content-Type: application/json

{ "id": 42, "nombre": "Ana", "email": "ana@ejemplo.com" }
```

**Si el usuario no existe**
```http
HTTP/1.1 404 Not Found
Content-Type: application/json

{ "error": "no_encontrado", "mensaje": "El usuario 42 no existe" }
```

---

### Ejemplo habitual: ciclo completo CRUD

Imagina una API de **tareas** (`/tareas`):

```http
# 1. Crear una tarea
POST /tareas
{ "titulo": "Estudiar REST", "hecha": false }
→ 201 Created   { "id": 7, "titulo": "Estudiar REST", "hecha": false }

# 2. Leer la lista
GET /tareas
→ 200 OK        [ { "id": 7, ... }, { "id": 8, ... } ]

# 3. Leer una tarea
GET /tareas/7
→ 200 OK        { "id": 7, "titulo": "Estudiar REST", "hecha": false }

# 4. Marcar como hecha (cambio parcial)
PATCH /tareas/7
{ "hecha": true }
→ 200 OK        { "id": 7, "titulo": "Estudiar REST", "hecha": true }

# 5. Borrar
DELETE /tareas/7
→ 204 No Content
```

---

### Ejemplo completo: consumir una API desde JavaScript

```javascript
const URL_BASE = "https://api.ejemplo.com/v1";

// Leer lista con filtros
async function obtenerTareas() {
  const respuesta = await fetch(`${URL_BASE}/tareas?hecha=false&limite=10`);

  if (!respuesta.ok) {
    throw new Error(`Error ${respuesta.status}`);
  }
  return await respuesta.json();
}

// Crear una tarea
async function crearTarea(titulo) {
  const respuesta = await fetch(`${URL_BASE}/tareas`, {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
      "Authorization": "Bearer MI_TOKEN"
    },
    body: JSON.stringify({ titulo, hecha: false })
  });

  if (!respuesta.ok) {
    throw new Error(`Error ${respuesta.status}`);
  }
  return await respuesta.json();
}

// Borrar una tarea
async function borrarTarea(id) {
  const respuesta = await fetch(`${URL_BASE}/tareas/${id}`, {
    method: "DELETE"
  });
  return respuesta.status === 204;
}
```

Detalle de `fetch` y `async/await` en [[13 - Fetch y APIs]] y [[12 - Asincronía]].

---

## 6. Buenas prácticas

- **Piensa en recursos**, no en acciones: `/usuarios`, no `/crearUsuario`.
- **Sustantivos en plural** y URLs en minúsculas con guiones.
- **Usa el método correcto** para cada operación y respeta su significado (`GET` nunca modifica datos).
- **Devuelve el código de estado adecuado**, no `200` para todo.
- **Responde siempre en un formato coherente** (normalmente JSON) con `Content-Type` correcto.
- **Errores con estructura fija** y mensajes claros que ayuden a corregir el problema.
- **Pagina** las listas grandes y permite **filtrar y ordenar**.
- **Versiona** la API desde el principio (`/v1`).
- **Usa HTTPS** siempre.
- **Valida los datos** que llegan; nunca confíes en lo que envía el cliente.
- **Protege** con autenticación y autorización lo que no sea público, y no expongas datos sensibles.
- **Limita el uso** (*rate limiting*) para evitar abusos.
- **Documenta** la API: qué rutas hay, qué reciben y qué devuelven (ej. con OpenAPI/Swagger).
- **No cambies una respuesta existente** de forma que rompa a quien ya la usa; para eso está el versionado.

Más sobre seguridad en [[05 - Seguridad web]].

---

## 7. Diferencias importantes

### API vs API REST

| | API | API REST |
|---|---|---|
| Qué es | Cualquier forma de que un programa hable con otro | Un tipo de API que sigue las reglas de REST sobre HTTP |
| Ejemplo | Una librería, el sistema operativo, una API web | `GET /usuarios/42` |

### REST vs otros estilos

| | REST | GraphQL | SOAP |
|---|---|---|---|
| Idea | Muchas URLs, una por recurso | Una sola URL; el cliente pide exactamente los campos que quiere | Mensajes en XML con reglas estrictas |
| Formato | Normalmente JSON | JSON | XML |
| Ventaja | Simple, estándar y muy extendido | Evita recibir datos de más o de menos | Muy formal y con muchas garantías |
| Uso actual | Muy habitual | En crecimiento | Sistemas antiguos o corporativos |

### `PUT` vs `PATCH`

| | `PUT` | `PATCH` |
|---|---|---|
| Acción | Reemplaza **todo** el recurso | Cambia **solo** lo que envías |
| Si faltan campos | Se pierden o quedan vacíos | Se quedan como estaban |

### `POST` vs `PUT`

| | `POST` | `PUT` |
|---|---|---|
| Uso | Crear (el servidor elige el id) | Reemplazar un recurso con id conocido |
| Repetirlo | Crea otro recurso cada vez | Deja el mismo resultado |

### Parámetro de ruta vs parámetro de query

| | Ruta | Query |
|---|---|---|
| Identifica | **Qué** recurso es | **Cómo** quieres verlo |
| Ejemplo | `/productos/15` | `/productos?orden=precio` |
| Obligatorio | Sí | No |

### Autenticación vs autorización

| | Autenticación | Autorización |
|---|---|---|
| Responde | ¿Quién eres? | ¿Qué puedes hacer? |
| Fallo típico | `401` | `403` |

---

## 8. Casos especiales

### Relaciones entre recursos

Cuando un recurso pertenece a otro, se anida en la URL:

```text
GET /usuarios/42/pedidos         → pedidos del usuario 42
GET /usuarios/42/pedidos/9       → el pedido 9 de ese usuario
```

> [!tip] No anides demasiado
> Más de dos niveles (`/a/1/b/2/c/3`) se vuelve difícil de usar. Si hace falta, ofrece también acceso directo: `/pedidos/9`.

### Acciones que no encajan en CRUD

A veces hay operaciones que no son "crear, leer, modificar o borrar" (enviar un correo, cancelar un pedido). Opciones habituales:

```text
POST /pedidos/9/cancelacion      → tratar la acción como un recurso
POST /correos                    → "crear" un envío de correo
```

### Operaciones repetidas e idempotencia

Un método es **idempotente** si repetirlo da el mismo resultado final. Es importante porque en redes inestables las peticiones se reintentan.

| Método | Idempotente |
|---|---|
| `GET`, `PUT`, `DELETE` | Sí |
| `POST` | No (puede crear duplicados) |

### CORS

Si una web en `https://miweb.com` llama a una API en `https://api.ejemplo.com`, el navegador la bloquea por defecto. El servidor de la API debe permitirlo con la cabecera `Access-Control-Allow-Origin`. Explicado en [[01 - HTTP]].

### HATEOAS

Idea de REST "puro": la respuesta incluye **enlaces** a las acciones posibles. Es poco habitual en la práctica.

```json
{
  "id": 42,
  "nombre": "Ana",
  "enlaces": {
    "pedidos": "/usuarios/42/pedidos",
    "borrar": "/usuarios/42"
  }
}
```

### Subida de archivos

Se usa `multipart/form-data` en lugar de JSON.

```http
POST /usuarios/42/avatar
Content-Type: multipart/form-data
```

### Probar una API

| Herramienta | Uso |
|---|---|
| `curl` | Desde la terminal |
| Postman / Insomnia / Bruno | Interfaces gráficas para probar peticiones |
| Pestaña **Network** del navegador | Ver las llamadas reales de una web |

```bash
curl https://api.ejemplo.com/v1/usuarios/42
curl -X POST https://api.ejemplo.com/v1/usuarios \
  -H "Content-Type: application/json" \
  -d '{"nombre":"Luis"}'
```

> [!warning] Obsoleto / legado
> - **SOAP/XML** para APIs nuevas: hoy se prefiere REST con JSON (o GraphQL).
> - **Verbos en la URL** (`/getUser`, `/deleteUser`): usa sustantivos + método HTTP.
> - **Enviar credenciales en la query string** (`?password=…`): usa cabeceras `Authorization`.
> - **Basic Auth sin HTTPS**: nunca es seguro.

---

## 9. Resumen

- Una **API** permite que un programa pida datos o acciones a otro; **REST** es un estilo para organizarla con **HTTP**.
- Todo gira en torno a **recursos**: la **URL** dice qué recurso es y el **método** dice qué hacer con él.
- Patrón base: **colección** (`/usuarios`) y **elemento** (`/usuarios/42`).
- **CRUD** = `POST` (crear), `GET` (leer), `PUT`/`PATCH` (modificar), `DELETE` (borrar).
- URLs con **sustantivos en plural**, minúsculas y guiones; nada de verbos.
- Los datos viajan en **ruta, query, cuerpo y cabeceras**; el formato habitual es **JSON**.
- Devuelve **códigos de estado correctos** y **errores con estructura clara**.
- **Pagina, filtra y ordena** con parámetros en la query string.
- **Versiona** (`/v1`), **autentica y autoriza**, usa **HTTPS** y limita el uso.
- **No tiene estado**: cada petición lleva todo lo necesario.
- Pruébala con `curl`, Postman o la pestaña **Network**.