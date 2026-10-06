# 01 - HTTP

> [!info] ¿Qué es?
> **HTTP** (*HyperText Transfer Protocol*) es el protocolo con el que se comunican navegadores (clientes) y servidores en la web. Funciona con un esquema de **petición → respuesta**: el cliente pide algo y el servidor contesta.

---

## 1. Antes de empezar

- HTTP es el "idioma" común de la web: da igual que el servidor esté hecho con Node.js, PHP o Python, todos hablan HTTP.
- No necesitas saber programar para entenderlo, pero verás ejemplos de código para fijar ideas.
- Para el panorama completo (DNS, IP, recorrido de una petición) ve a [[06 - Cómo funciona internet]].

---

## 2. Concepto fundamental

### Modelo cliente-servidor

```text
Cliente (navegador)  ──── petición ────►  Servidor
                     ◄─── respuesta ────
```

- El **cliente siempre inicia** la comunicación; el servidor solo responde.
- Cada par petición/respuesta es **independiente**.

### HTTP es *stateless* (sin estado)

El servidor **no recuerda** peticiones anteriores. Cada petición debe llevar toda la información necesaria. Para "recordar" al usuario (sesión iniciada, carrito) se usan **cookies**, **sesiones** o **tokens**, que viajan en las cabeceras.

### HTTP vs HTTPS

| | HTTP | HTTPS |
|---|---|---|
| Cifrado | No | Sí (TLS) |
| Puerto por defecto | 80 | 443 |
| Seguridad | Los datos viajan en claro | Los datos viajan cifrados |
| Uso recomendado | Nunca en producción | Siempre |

HTTPS **no es otro protocolo**: es HTTP dentro de una conexión cifrada con TLS.

---

## 3. Sintaxis / estructura

### 3.1 Anatomía de una URL

```text
https://www.ejemplo.com:443/blog/articulo?id=5&lang=es#comentarios
└─┬─┘   └──────┬──────┘└┬┘└──────┬──────┘└─────┬──────┘└────┬────┘
esquema      host    puerto    ruta        query string  fragmento
```

| Parte | Función |
|---|---|
| Esquema | Protocolo (`http`, `https`) |
| Host | Dominio o IP del servidor |
| Puerto | Opcional; se omite si es el de por defecto |
| Ruta | Recurso dentro del servidor |
| Query string | Parámetros `clave=valor` separados por `&` |
| Fragmento | Ancla interna; **no se envía al servidor** |

### 3.2 Estructura de una petición

```http
GET /blog/articulo?id=5 HTTP/1.1
Host: www.ejemplo.com
Accept: text/html
User-Agent: Mozilla/5.0

(cuerpo opcional)
```

1. **Línea de petición:** método + ruta + versión
2. **Cabeceras:** pares `Nombre: valor`
3. **Línea en blanco**
4. **Cuerpo:** opcional (datos enviados)

### 3.3 Estructura de una respuesta

```http
HTTP/1.1 200 OK
Content-Type: text/html; charset=UTF-8
Content-Length: 1256

<!DOCTYPE html>
<html>…</html>
```

1. **Línea de estado:** versión + código + texto
2. **Cabeceras**
3. **Línea en blanco**
4. **Cuerpo:** el recurso solicitado

---

## 4. Elementos / propiedades / características

### 4.1 Métodos HTTP

| Método | Uso habitual | Lleva cuerpo |
|---|---|---|
| `GET` | Obtener un recurso | No |
| `POST` | Crear un recurso / enviar datos | Sí |
| `PUT` | Reemplazar un recurso completo | Sí |
| `PATCH` | Modificar parte de un recurso | Sí |
| `DELETE` | Eliminar un recurso | Normalmente no |
| `HEAD` | Como `GET`, pero solo cabeceras | No |
| `OPTIONS` | Consultar qué métodos admite el servidor | No |

**Propiedades importantes:**

| Método | ¿Seguro? | ¿Idempotente? |
|---|---|---|
| `GET` | ✅ | ✅ |
| `HEAD` | ✅ | ✅ |
| `OPTIONS` | ✅ | ✅ |
| `PUT` | ❌ | ✅ |
| `DELETE` | ❌ | ✅ |
| `POST` | ❌ | ❌ |
| `PATCH` | ❌ | ❌ (no garantizado) |

- **Seguro:** no modifica datos en el servidor.
- **Idempotente:** repetir la misma petición varias veces deja el mismo resultado que hacerla una vez.

### 4.2 Códigos de estado

| Rango | Significado |
|---|---|
| `1xx` | Informativo |
| `2xx` | Éxito |
| `3xx` | Redirección |
| `4xx` | Error del **cliente** |
| `5xx` | Error del **servidor** |

**Los más habituales:**

| Código | Nombre | Cuándo se usa |
|---|---|---|
| `200` | OK | Petición correcta |
| `201` | Created | Recurso creado (tras un `POST`) |
| `204` | No Content | Correcto, sin cuerpo en la respuesta |
| `301` | Moved Permanently | Redirección permanente |
| `302` | Found | Redirección temporal |
| `304` | Not Modified | El recurso en caché sigue siendo válido |
| `400` | Bad Request | Petición mal formada |
| `401` | Unauthorized | Falta autenticación o es inválida |
| `403` | Forbidden | Autenticado, pero sin permiso |
| `404` | Not Found | El recurso no existe |
| `405` | Method Not Allowed | Método no permitido en esa ruta |
| `409` | Conflict | Conflicto con el estado actual |
| `422` | Unprocessable Content | Datos bien formados pero inválidos |
| `429` | Too Many Requests | Demasiadas peticiones (límite de uso) |
| `500` | Internal Server Error | Fallo genérico del servidor |
| `502` | Bad Gateway | Respuesta inválida de un servidor intermedio |
| `503` | Service Unavailable | Servidor saturado o en mantenimiento |
| `504` | Gateway Timeout | Un servidor intermedio no respondió a tiempo |

### 4.3 Cabeceras (headers)

**Más comunes en peticiones:**

| Cabecera | Función |
|---|---|
| `Host` | Dominio al que va dirigida la petición |
| `Accept` | Formatos que el cliente acepta |
| `Content-Type` | Formato del cuerpo enviado |
| `Authorization` | Credenciales (token, usuario) |
| `Cookie` | Cookies guardadas para ese dominio |
| `User-Agent` | Identifica al cliente |
| `Origin` | Origen de la petición (relevante en CORS) |

**Más comunes en respuestas:**

| Cabecera | Función |
|---|---|
| `Content-Type` | Formato del cuerpo devuelto |
| `Content-Length` | Tamaño del cuerpo en bytes |
| `Set-Cookie` | Pide al cliente guardar una cookie |
| `Location` | Destino de una redirección |
| `Cache-Control` | Normas de caché |
| `Access-Control-Allow-Origin` | Orígenes permitidos (CORS) |

**Valores frecuentes de `Content-Type`:**

| Valor | Contenido |
|---|---|
| `text/html` | Página HTML |
| `application/json` | Datos en JSON (ver [[03 - JSON]]) |
| `application/x-www-form-urlencoded` | Formulario clásico |
| `multipart/form-data` | Formulario con archivos |
| `text/css` · `text/javascript` | Hoja de estilos · script |
| `image/png` · `image/jpeg` | Imágenes |

### 4.4 Parámetros: dónde viajan los datos

| Lugar | Ejemplo | Uso típico |
|---|---|---|
| **Ruta** | `/usuarios/42` | Identificar un recurso |
| **Query string** | `/buscar?q=html&pag=2` | Filtros, búsquedas, paginación |
| **Cuerpo** | `{"nombre":"Ana"}` | Datos a crear o modificar |
| **Cabeceras** | `Authorization: Bearer …` | Metadatos y credenciales |

### 4.5 Versiones

| Versión | Característica principal |
|---|---|
| HTTP/1.1 | Texto plano, una petición a la vez por conexión (muy extendido) |
| HTTP/2 | Binario, **multiplexación** de varias peticiones en una conexión |
| HTTP/3 | Sobre QUIC (UDP), menor latencia y mejor recuperación ante pérdidas |

Para el día a día **el código que escribes es el mismo**: el navegador y el servidor negocian la versión.

---

## 5. Ejemplos prácticos

### Ejemplo básico: petición `GET` y su respuesta

**Petición**
```http
GET /api/usuarios/42 HTTP/1.1
Host: api.ejemplo.com
Accept: application/json
```

**Respuesta**
```http
HTTP/1.1 200 OK
Content-Type: application/json

{"id": 42, "nombre": "Ana"}
```

---

### Ejemplo habitual: crear un recurso con `POST`

**Petición**
```http
POST /api/usuarios HTTP/1.1
Host: api.ejemplo.com
Content-Type: application/json

{"nombre": "Luis", "email": "luis@ejemplo.com"}
```

**Respuesta**
```http
HTTP/1.1 201 Created
Content-Type: application/json
Location: /api/usuarios/43

{"id": 43, "nombre": "Luis", "email": "luis@ejemplo.com"}
```

---

### Ejemplo completo: lo mismo desde herramientas reales

**Con `curl` (terminal)**
```bash
# GET
curl https://api.ejemplo.com/usuarios/42

# POST con JSON
curl -X POST https://api.ejemplo.com/usuarios \
  -H "Content-Type: application/json" \
  -d '{"nombre":"Luis","email":"luis@ejemplo.com"}'

# Ver también las cabeceras de la respuesta
curl -i https://api.ejemplo.com/usuarios/42
```

**Con JavaScript (`fetch`)**
```javascript
const respuesta = await fetch("https://api.ejemplo.com/usuarios", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({ nombre: "Luis", email: "luis@ejemplo.com" })
});

console.log(respuesta.status);     // 201
const datos = await respuesta.json();
```

> [!tip] Inspeccionar HTTP en el navegador
> En las herramientas de desarrollo (`F12`) → pestaña **Red / Network** puedes ver cada petición real: método, código, cabeceras y cuerpo. Es la mejor forma de aprender HTTP.

Detalle de `fetch` en [[13 - Fetch y APIs]].

---

## 6. Buenas prácticas

- Usa **siempre HTTPS** en producción.
- Elige el **método según la intención**: `GET` para leer, `POST` para crear, etc.
- **Nunca modifiques datos con `GET`**: puede ser lanzado por cachés, enlaces o rastreadores.
- Devuelve el **código de estado correcto** (`201` al crear, `404` si no existe, `401`/`403` según el caso), no un `200` para todo.
- Indica siempre **`Content-Type`** al enviar un cuerpo.
- **No envíes datos sensibles** (contraseñas, tokens) en la query string: queda en historiales, logs y cachés.
- Aprovecha la **caché** (`Cache-Control`) en recursos estáticos.
- Controla la **paginación y los límites** de uso (`429`) en las APIs.
- Comprueba `respuesta.ok` o el `status` antes de procesar los datos del `fetch`.

Seguridad en profundidad en [[05 - Seguridad web]].

---

## 7. Diferencias importantes

### `GET` vs `POST`

| | `GET` | `POST` |
|---|---|---|
| Propósito | Obtener datos | Enviar / crear datos |
| Datos | En la URL (query string) | En el cuerpo |
| Visible en el historial | Sí | No |
| Cacheable | Sí | Normalmente no |
| Límite de tamaño | Sí (longitud de URL) | Prácticamente no |

### `PUT` vs `PATCH`

| | `PUT` | `PATCH` |
|---|---|---|
| Acción | Reemplaza **todo** el recurso | Modifica **solo** los campos enviados |
| Idempotente | Sí | No garantizado |

### `401` vs `403`

| | `401` | `403` |
|---|---|---|
| Significa | No estás identificado | Estás identificado, pero no tienes permiso |
| Solución | Iniciar sesión / enviar credenciales | Pedir permisos; reautenticar no basta |

### `301` vs `302`

| | `301` | `302` |
|---|---|---|
| Tipo | Permanente | Temporal |
| Efecto | Navegadores y buscadores actualizan la URL | Se mantiene la URL original |

### Query string vs cuerpo

| | Query string | Cuerpo |
|---|---|---|
| Ideal para | Filtros, búsquedas, orden | Datos de creación o edición |
| Seguridad | Visible en URL y logs | No aparece en la URL |

### `4xx` vs `5xx`

- `4xx`: **el problema está en la petición** (el cliente debe corregirla).
- `5xx`: **el problema está en el servidor** (el cliente no puede arreglarlo).

---

## 8. Casos especiales

### Redirecciones

El servidor responde con `3xx` y una cabecera `Location`; el navegador sigue automáticamente al nuevo destino.

```http
HTTP/1.1 301 Moved Permanently
Location: https://www.ejemplo.com/nueva-ruta
```

### Caché condicional (`304`)

El navegador pregunta si su copia sigue siendo válida; si lo es, el servidor responde `304` **sin cuerpo** y se ahorra la descarga.

### Cookies y sesiones

Al ser HTTP *stateless*, el servidor envía `Set-Cookie` y el navegador la devuelve en cada petición con `Cookie`.

```http
Set-Cookie: sesion=abc123; HttpOnly; Secure; SameSite=Lax
```

| Atributo | Función |
|---|---|
| `HttpOnly` | JavaScript no puede leerla |
| `Secure` | Solo se envía por HTTPS |
| `SameSite` | Limita el envío en peticiones entre sitios |

### CORS (*Cross-Origin Resource Sharing*)

Por seguridad, el navegador bloquea peticiones desde un **origen** (esquema + dominio + puerto) a otro, salvo que el servidor lo permita con `Access-Control-Allow-Origin`. En peticiones "no simples" el navegador envía antes una petición **preflight** con `OPTIONS`.

### Formularios HTML

Un `<form>` solo puede usar `GET` y `POST` en su atributo `method`. Para `PUT`, `PATCH` o `DELETE` hay que usar JavaScript (`fetch`). Ver [[06 - Formularios]].

> [!warning] Obsoleto / legado
> - **HTTP sin cifrar** en sitios reales: usa HTTPS.
> - **Prefijo `X-`** en cabeceras personalizadas (`X-Mi-Cabecera`): está desaconsejado; usa nombres sin prefijo.
> - **HTTP/1.0 y anteriores:** sustituidos por HTTP/1.1 y superiores.

---

## 9. Resumen

- HTTP es un protocolo **cliente → servidor de petición/respuesta** y **sin estado**.
- Una petición lleva **método + ruta + cabeceras + cuerpo opcional**; una respuesta, **código + cabeceras + cuerpo**.
- Métodos clave: `GET` (leer), `POST` (crear), `PUT`/`PATCH` (modificar), `DELETE` (borrar).
- Los códigos se agrupan por rangos: `2xx` éxito, `3xx` redirección, `4xx` error del cliente, `5xx` error del servidor.
- Las **cabeceras** transportan metadatos: formato, credenciales, cookies, caché, CORS.
- Los datos viajan en **ruta, query string, cuerpo o cabeceras**, según su propósito.
- **HTTPS siempre**; nunca datos sensibles en la URL.
- Usa la pestaña **Network** del navegador para ver HTTP en acción.