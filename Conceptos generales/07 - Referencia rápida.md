# 07 - Referencia rápida

> [!info] ¿Qué es?
> Una **chuleta** que resume lo más importante de la carpeta *Conceptos generales*: HTTP, APIs REST, JSON, Regex, Seguridad web y Cómo funciona internet. Sirve para **repasar rápido** o encontrar un dato concreto sin releer las notas completas. Si algo no queda claro, cada apartado indica la nota donde está explicado con detalle.

---

## 1. Antes de empezar

- Esta nota **no enseña desde cero**: recuerda lo esencial.
- Si un tema se te ha olvidado del todo, ve primero a su nota completa:
  - [[01 - HTTP]]
  - [[02 - APIs REST]]
  - [[03 - JSON]]
  - [[04 - Regex]]
  - [[05 - Seguridad web]]
  - [[06 - Cómo funciona internet]]
- Usa `Ctrl + F` (o el buscador de Obsidian) para saltar directamente al término que buscas.

---

## 2. Mapa general

```
Escribes una URL
      ↓
DNS → IP del servidor                      (06 - Cómo funciona internet)
      ↓
Conexión TCP + cifrado TLS (HTTPS)         (06 - Cómo funciona internet)
      ↓
Petición HTTP (método + ruta + cabeceras)  (01 - HTTP)
      ↓
API REST procesa y responde                (02 - APIs REST)
      ↓
Datos en formato JSON                      (03 - JSON)
      ↓
Validar con regex, proteger la respuesta   (04 - Regex, 05 - Seguridad web)
```

---

## 3. HTTP

### 3.1 Estructura de una petición

```http
GET /usuarios/5 HTTP/1.1
Host: api.ejemplo.com
Accept: application/json
Authorization: Bearer eyJhbGci...
```

| Parte | Qué es |
|---|---|
| **Método** | Qué quieres hacer (`GET`, `POST`…) |
| **Ruta** | Qué recurso quieres |
| **Cabeceras** | Información extra sobre la petición |
| **Cuerpo** (*body*) | Los datos que envías (en `POST`, `PUT`, `PATCH`) |

### 3.2 Estructura de una respuesta

```http
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: max-age=3600

{ "id": 5, "nombre": "Ana" }
```

### 3.3 Métodos

| Método | Para qué | ¿Tiene cuerpo? | ¿Seguro? | ¿Idempotente? |
|---|---|---|---|---|
| `GET` | Leer | No | Sí | Sí |
| `POST` | Crear | Sí | No | No |
| `PUT` | Reemplazar entero | Sí | No | Sí |
| `PATCH` | Modificar una parte | Sí | No | No necesariamente |
| `DELETE` | Borrar | Normalmente no | No | Sí |
| `HEAD` | Como GET pero solo cabeceras | No | Sí | Sí |
| `OPTIONS` | Consultar qué se permite (CORS) | No | Sí | Sí |

> [!tip] Recordatorio
> **Seguro** = no modifica nada. **Idempotente** = repetirlo varias veces deja el mismo resultado que hacerlo una vez.

### 3.4 Códigos de estado

| Rango | Significado |
|---|---|
| `1xx` | Informativo |
| `2xx` | Éxito |
| `3xx` | Redirección |
| `4xx` | Error del **cliente** |
| `5xx` | Error del **servidor** |

| Código | Nombre | Cuándo |
|---|---|---|
| `200` | OK | Todo correcto |
| `201` | Created | Se ha creado un recurso |
| `204` | No Content | Correcto, sin nada que devolver (típico en `DELETE`) |
| `301` | Moved Permanently | Redirección permanente |
| `302` | Found | Redirección temporal |
| `304` | Not Modified | Usa tu copia en caché |
| `400` | Bad Request | Petición mal formada o datos no válidos |
| `401` | Unauthorized | No autenticado |
| `403` | Forbidden | Autenticado pero sin permiso |
| `404` | Not Found | No existe |
| `405` | Method Not Allowed | Método no permitido en esa ruta |
| `409` | Conflict | Choca con el estado actual (duplicado) |
| `422` | Unprocessable Entity | Datos con formato correcto pero no válidos |
| `429` | Too Many Requests | Límite de peticiones superado |
| `500` | Internal Server Error | Fallo interno |
| `502` | Bad Gateway | El servidor intermedio recibió una respuesta mala |
| `503` | Service Unavailable | Servidor caído o saturado |

### 3.5 Cabeceras habituales

| Cabecera | Para qué |
|---|---|
| `Content-Type` | Tipo de lo que **envías** (`application/json`) |
| `Accept` | Tipo de lo que **quieres recibir** |
| `Authorization` | Credenciales (`Bearer <token>`) |
| `Cookie` / `Set-Cookie` | Enviar / establecer cookies |
| `Cache-Control` | Cómo y cuánto se guarda en caché |
| `User-Agent` | Quién hace la petición (navegador, app) |
| `Location` | A dónde redirigir |
| `Access-Control-Allow-Origin` | Qué origen permite CORS |

### 3.6 Tipos de contenido (MIME)

| Tipo | Contenido |
|---|---|
| `text/html` | Página HTML |
| `text/css` | Hoja de estilos |
| `application/javascript` | JavaScript |
| `application/json` | JSON |
| `application/x-www-form-urlencoded` | Formulario clásico |
| `multipart/form-data` | Formulario con ficheros |
| `image/png`, `image/jpeg`, `image/svg+xml` | Imágenes |

---

## 4. APIs REST

### 4.1 Idea clave

Una API REST expone **recursos** (usuarios, pedidos, productos) mediante **URL** y los manipula con los **métodos HTTP**.

### 4.2 Diseño de rutas

| Acción | Método y ruta | Respuesta típica |
|---|---|---|
| Listar | `GET /usuarios` | `200` + lista |
| Ver uno | `GET /usuarios/5` | `200` + objeto, o `404` |
| Crear | `POST /usuarios` | `201` + objeto creado |
| Reemplazar | `PUT /usuarios/5` | `200` o `204` |
| Modificar parte | `PATCH /usuarios/5` | `200` |
| Borrar | `DELETE /usuarios/5` | `204` |
| Recursos anidados | `GET /usuarios/5/pedidos` | `200` + lista |

### 4.3 Reglas de nombres

- Usa **sustantivos en plural**: `/usuarios`, no `/obtenerUsuarios`.
- Usa **minúsculas** y guiones: `/tipos-de-producto`.
- **No pongas verbos** en la ruta; el verbo lo da el método HTTP.
- Usa **parámetros de consulta** para filtrar, ordenar y paginar.

```
GET /productos?categoria=libros&orden=precio&pagina=2&limite=20
```

### 4.4 Principios REST

| Principio | Significado |
|---|---|
| **Cliente-servidor** | Quien pide y quien responde están separados |
| **Sin estado** (*stateless*) | Cada petición lleva toda la información necesaria |
| **Interfaz uniforme** | Siempre las mismas reglas (URL + métodos) |
| **Cacheable** | Las respuestas pueden indicar si se guardan en caché |
| **Capas** | Puede haber intermediarios (proxies, CDN) |

### 4.5 Autenticación en APIs

| Método | Cómo funciona |
|---|---|
| **API Key** | Una clave fija en una cabecera o parámetro |
| **Bearer token / JWT** | Un token firmado en `Authorization: Bearer ...` |
| **Sesión + cookie** | El servidor guarda la sesión y entrega una cookie |
| **OAuth 2.0** | Un tercero (Google, GitHub) autoriza el acceso sin compartir la contraseña |

### 4.6 Ejemplo mínimo con `fetch`

```js
const res = await fetch("https://api.ejemplo.com/usuarios", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({ nombre: "Ana" })
});

if (!res.ok) throw new Error(`Error ${res.status}`);
const usuario = await res.json();
```

> [!warning] Recuerda
> `fetch` **no lanza error** con un `404` o `500`. Hay que comprobar `res.ok` tú mismo.

### 4.7 Formato habitual de error

```json
{
  "error": "Datos no válidos",
  "detalles": [
    { "campo": "email", "mensaje": "Formato incorrecto" }
  ]
}
```

---

## 5. JSON

### 5.1 Tipos de datos permitidos

| Tipo | Ejemplo |
|---|---|
| **String** | `"hola"` (siempre comillas **dobles**) |
| **Number** | `42`, `3.14`, `-5` |
| **Boolean** | `true`, `false` |
| **null** | `null` |
| **Array** | `[1, 2, 3]` |
| **Object** | `{ "clave": "valor" }` |

### 5.2 Reglas de sintaxis

- Las **claves** van siempre entre **comillas dobles**.
- **No** se permiten comillas simples.
- **No** se permiten comas finales (`[1, 2,]` es inválido).
- **No** se permiten comentarios.
- **No** existen `undefined`, funciones ni fechas (las fechas se guardan como texto).

```json
{
  "nombre": "Ana",
  "edad": 25,
  "activa": true,
  "telefono": null,
  "aficiones": ["leer", "correr"],
  "direccion": { "ciudad": "Granada", "cp": "18110" }
}
```

### 5.3 Métodos en JavaScript

| Método | Qué hace |
|---|---|
| `JSON.stringify(obj)` | Objeto → **texto JSON** |
| `JSON.stringify(obj, null, 2)` | Igual, pero con **sangría** legible |
| `JSON.parse(texto)` | Texto JSON → **objeto** |

```js
const texto = JSON.stringify({ a: 1 });   // '{"a":1}'
const obj = JSON.parse('{"a":1}');         // { a: 1 }
```

### 5.4 Trampas habituales

| Situación | Resultado |
|---|---|
| `JSON.parse` con texto inválido | Lanza error (usa `try/catch`) |
| `undefined` o funciones en un objeto | Se **omiten** al hacer `stringify` |
| `Date` | Se convierte en texto ISO |
| `NaN`, `Infinity` | Se convierten en `null` |
| Referencias circulares | `stringify` lanza error |

```js
try {
  const datos = JSON.parse(textoPosiblementeRoto);
} catch (error) {
  console.error("JSON no válido");
}
```

### 5.5 Copia profunda rápida

```js
const copia = structuredClone(original);        // recomendado
const copia2 = JSON.parse(JSON.stringify(original)); // antiguo, con limitaciones
```

---

## 6. Regex

### 6.1 Estructura

```js
/patrón/flags
new RegExp("patrón", "flags")
```

### 6.2 Flags

| Flag | Significado |
|---|---|
| `g` | Todas las coincidencias |
| `i` | Ignora mayúsculas |
| `m` | `^` y `$` por línea |
| `s` | `.` incluye saltos de línea |
| `u` | Unicode |

### 6.3 Chuleta de símbolos

| Símbolo | Significado |
|---|---|
| `.` | Cualquier carácter |
| `^` / `$` | Inicio / fin |
| `\|` | O |
| `\d` / `\D` | Dígito / no dígito |
| `\w` / `\W` | Letra, número o `_` / lo contrario |
| `\s` / `\S` | Espacio en blanco / lo contrario |
| `\b` | Límite de palabra |
| `[abc]` | Uno de esos |
| `[^abc]` | Cualquiera menos esos |
| `[a-z]` | Rango |
| `*` | 0 o más |
| `+` | 1 o más |
| `?` | 0 o 1 (opcional) |
| `{n}` / `{n,}` / `{n,m}` | Exactamente n / n o más / entre n y m |
| `*?` `+?` | Versión perezosa |
| `(…)` | Grupo con captura |
| `(?:…)` | Grupo sin captura |
| `(?<n>…)` | Grupo con nombre |
| `(?=…)` `(?!…)` | Lo que sigue (sí / no) |
| `(?<=…)` `(?<!…)` | Lo que precede (sí / no) |

### 6.4 Métodos

| Método | Devuelve |
|---|---|
| `regex.test(texto)` | `true` / `false` |
| `texto.match(regex)` | Coincidencias o `null` |
| `texto.matchAll(regex)` | Todas, con grupos (requiere `g`) |
| `texto.replace(regex, nuevo)` | Texto con el reemplazo |
| `texto.search(regex)` | Posición o `-1` |
| `texto.split(regex)` | Array |

### 6.5 Patrones útiles

| Para | Patrón |
|---|---|
| Código postal español | `/^\d{5}$/` |
| Email (sencillo) | `/^[^\s@]+@[^\s@]+\.[^\s@]+$/` |
| Fecha `AAAA-MM-DD` | `/^\d{4}-\d{2}-\d{2}$/` |
| Solo números | `/^\d+$/` |
| Solo letras (con tildes) | `/^\p{L}+$/u` |
| Espacios sobrantes | `/\s+/g` |
| Color hexadecimal | `/^#([0-9a-fA-F]{3}\|[0-9a-fA-F]{6})$/` |

> [!warning] Trampas rápidas
> - Para **validar**, usa `^` y `$` y **no** pongas `g` (por el problema de `lastIndex`).
> - `\w` no reconoce `á`, `é`, `ñ`.
> - Evita repeticiones anidadas como `(a+)+` (riesgo de ReDoS).

---

## 7. Seguridad web

### 7.1 Reglas de oro

1. **Nunca confíes en los datos del usuario.**
2. **Valida siempre en el servidor.**
3. Usa **HTTPS** en todo.
4. Da el **mínimo de permisos** necesarios.
5. Protege en **capas** (defensa en profundidad).

### 7.2 Ataques y defensas

| Ataque | Qué es | Defensa principal |
|---|---|---|
| **SQL Injection** | El texto del usuario se ejecuta como SQL | Consultas preparadas |
| **XSS** | Se ejecuta JavaScript ajeno en tu web | `textContent`, escapar salida, CSP |
| **CSRF** | Se engaña al navegador para enviar una petición | Token CSRF, cookies `SameSite` |
| **Fuerza bruta** | Probar contraseñas sin parar | Límite de intentos, 2FA |
| **Clickjacking** | Tu web dentro de un `<iframe>` ajeno | `frame-ancestors` / `X-Frame-Options` |
| **Man-in-the-middle** | Interceptar la comunicación | HTTPS + HSTS |
| **IDOR** | Cambiar un `id` para ver datos de otros | Comprobar que el recurso es del usuario |
| **Path traversal** | Usar `../` para leer ficheros | Validar rutas |
| **ReDoS** | Regex que bloquea el servidor | Evitar repeticiones anidadas, limitar longitud |

### 7.3 Contraseñas

| Opción | ¿Válida? |
|---|---|
| Texto plano | ❌ |
| MD5 / SHA-1 | ❌ |
| **bcrypt**, **argon2**, **scrypt** | ✅ |

### 7.4 Cookie segura

```
Set-Cookie: sesion=abc; HttpOnly; Secure; SameSite=Lax; Max-Age=3600
```

| Atributo | Función |
|---|---|
| `HttpOnly` | JavaScript no puede leerla |
| `Secure` | Solo por HTTPS |
| `SameSite` | Frena CSRF |
| `Max-Age` | Caduca |

### 7.5 Cabeceras de seguridad

| Cabecera | Función |
|---|---|
| `Content-Security-Policy` | Controla de dónde se cargan los recursos |
| `Strict-Transport-Security` | Obliga a usar HTTPS |
| `X-Content-Type-Options: nosniff` | Evita que el navegador adivine el tipo |
| `Referrer-Policy` | Limita la información de origen enviada |

En Express se ponen de golpe con `helmet()`.

### 7.6 Diferencias que se confunden

| | |
|---|---|
| **Autenticación** | ¿Quién eres? |
| **Autorización** | ¿Qué puedes hacer? |
| **Validar** | Aceptar o rechazar al recibir |
| **Escapar** | Hacer inofensivo al mostrar |
| **Cifrar** | Reversible con clave |
| **Hashear** | No reversible |
| `401` | No has iniciado sesión |
| `403` | Has iniciado sesión pero no tienes permiso |

### 7.7 Lista antes de publicar

- [ ] Solo HTTPS
- [ ] Consultas preparadas
- [ ] Salida escapada
- [ ] Contraseñas con bcrypt o argon2
- [ ] Cookies `HttpOnly`, `Secure`, `SameSite`
- [ ] Token CSRF en formularios que cambian datos
- [ ] Cabeceras de seguridad (Helmet)
- [ ] Secretos fuera del código y de Git
- [ ] `npm audit` limpio
- [ ] Límite de intentos de login

---

## 8. Cómo funciona internet

### 8.1 Qué pasa al abrir una URL

```
1. DNS         → obtiene la IP
2. TCP         → conexión con el servidor (SYN, SYN-ACK, ACK)
3. TLS         → cifrado (si es HTTPS)
4. HTTP        → petición
5. Respuesta   → HTML
6. Navegador   → DOM + CSS + JS → pinta la página
```

### 8.2 Partes de una URL

```
https://www.ejemplo.com:443/contacto?tema=ayuda#formulario
protocolo   dominio       puerto ruta   parámetros  fragmento
```

### 8.3 Puertos habituales

| Puerto | Servicio |
|---|---|
| `80` | HTTP |
| `443` | HTTPS |
| `22` | SSH |
| `21` | FTP |
| `25` / `587` | Correo (SMTP) |
| `3306` | MySQL |
| `5432` | PostgreSQL |
| `27017` | MongoDB |
| `3000`, `5173`, `8080` | Desarrollo local |

### 8.4 Registros DNS

| Registro | Función |
|---|---|
| `A` | Nombre → IPv4 |
| `AAAA` | Nombre → IPv6 |
| `CNAME` | Alias de otro nombre |
| `MX` | Servidor de correo |
| `TXT` | Texto libre (verificaciones) |
| `NS` | Servidores del dominio |

### 8.5 Comparativas rápidas

| | |
|---|---|
| **TCP** | Fiable, ordenado, más lento |
| **UDP** | Rápido, sin garantías |
| **IP pública** | Visible desde internet |
| **IP privada** | Solo dentro de tu red (`192.168.x.x`) |
| `localhost` / `127.0.0.1` | Tu propio equipo |
| **Latencia** | Cuánto tarda (ms) |
| **Ancho de banda** | Cuántos datos por segundo (Mbps) |
| **Proxy** | Intermediario del lado del cliente |
| **Proxy inverso** | Intermediario del lado del servidor |
| **CDN** | Copias de tus ficheros cerca del usuario |
| **Estática** | Ficheros fijos |
| **Dinámica** | Contenido generado por código y base de datos |

### 8.6 Origen (CORS)

Origen = **protocolo + dominio + puerto**. Si cambia cualquiera, es otro origen.

### 8.7 Comandos de diagnóstico

```bash
ping ejemplo.com            # ¿responde el servidor?
nslookup ejemplo.com        # ¿qué IP tiene?
dig ejemplo.com             # información DNS detallada
traceroute ejemplo.com      # recorrido de los paquetes (tracert en Windows)
curl -i https://ejemplo.com # petición HTTP con cabeceras
```

### 8.8 Herramientas del navegador

- **F12 → Red** (*Network*): ver todas las peticiones, códigos de estado, tamaños y tiempos.
- **F12 → Aplicación**: ver cookies, `localStorage` y caché.
- **F12 → Consola**: errores y pruebas de JavaScript.

### 8.9 Diagnóstico rápido de errores

| Síntoma | Causa probable |
|---|---|
| No encuentra la dirección del servidor | DNS o dominio inexistente |
| Tiempo de espera agotado | Servidor caído o firewall |
| Conexión rechazada | Nada escuchando en ese puerto |
| Advertencia de certificado | Certificado caducado o mal configurado |
| `404` | La ruta no existe |
| `500` | Error interno del servidor |
| `502` / `503` | Servidor intermedio sin respuesta o saturado |
| Veo la versión antigua | Caché del navegador o de la CDN |

---

## 9. Resumen

- **HTTP**: petición (método + ruta + cabeceras + cuerpo) → respuesta (código + cabeceras + cuerpo). Los códigos `2xx` son éxito, `4xx` error del cliente y `5xx` error del servidor.
- **REST**: rutas con **sustantivos en plural**, el verbo lo pone el **método**, y cada petición es **independiente** (sin estado).
- **JSON**: claves y strings con **comillas dobles**, sin comentarios ni comas finales. `JSON.stringify()` convierte a texto y `JSON.parse()` a objeto.
- **Regex**: `/patrón/flags`. Para validar usa `^` y `$`, sin `g`. Cuidado con `\w` y las tildes, y con los patrones que se vuelven lentos.
- **Seguridad**: no confíes en el usuario, valida en el servidor, usa HTTPS, consultas preparadas, escapa la salida, guarda contraseñas con hash (bcrypt o argon2) y protege las cookies.
- **Internet**: DNS traduce nombres a IP → TCP conecta → TLS cifra → HTTP pide → el navegador pinta. Los **puertos** identifican servicios (`80` HTTP, `443` HTTPS).
- Herramientas clave de diagnóstico: **F12 → Red**, `ping`, `nslookup`, `curl` y `traceroute`.
- Ante la duda, vuelve a la nota completa del tema.