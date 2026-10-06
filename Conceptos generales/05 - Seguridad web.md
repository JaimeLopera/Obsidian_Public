# 05 - Seguridad web

> [!info] ¿Qué es?
> La **seguridad web** es el conjunto de ideas y técnicas para que una aplicación web **no pueda ser atacada ni engañada**, y para proteger los **datos de los usuarios** (contraseñas, correos, pagos…). Un solo fallo puede permitir que alguien robe datos, entre en cuentas ajenas o rompa la aplicación.

---

## 1. Antes de empezar

- La seguridad **no es una función que se añade al final**: se piensa desde el principio, al escribir cada formulario, cada consulta y cada ruta.
- Los ataques casi siempre aprovechan un error muy simple: **confiar en lo que envía el usuario**.
- Conviene conocer antes:
  - Cómo funciona HTTP: [[01 - HTTP]]
  - Qué es una API: [[02 - APIs REST]]
  - Las expresiones regulares (útiles para validar, pero con cuidado): [[04 - Regex]]
- Esta nota es una **introducción práctica**. Cada tema tiene mucho más detalle, pero aquí tienes lo esencial que debe saber cualquier desarrollador web.

---

## 2. Concepto fundamental

### La regla de oro

> [!warning] Nunca confíes en los datos que vienen del usuario
> Todo lo que llega al servidor (formularios, URL, cookies, cabeceras, ficheros subidos…) **puede haber sido manipulado**. Hay que **comprobarlo siempre**, aunque el navegador ya lo haya comprobado.

### Cliente vs servidor

| | Cliente (navegador) | Servidor |
|---|---|---|
| ¿Quién lo controla? | **El usuario** (y un atacante también) | **Tú** |
| ¿Se puede manipular? | Sí, siempre (con las herramientas del navegador) | Solo si hay fallos |
| Validar aquí sirve para | Mejorar la experiencia del usuario | **Seguridad real** |

Por eso la validación en JavaScript del navegador es solo una **comodidad**. La validación que de verdad protege es la del **servidor**.

### Los tres objetivos de la seguridad

| Objetivo | Significa | Ejemplo de fallo |
|---|---|---|
| **Confidencialidad** | Solo ve los datos quien debe verlos | Un usuario lee los pedidos de otro |
| **Integridad** | Los datos no se modifican sin permiso | Alguien cambia el precio de un producto |
| **Disponibilidad** | El servicio funciona cuando se necesita | Un ataque deja la web caída |

### Autenticación vs autorización

Se confunden mucho, pero son cosas distintas:

| | Autenticación | Autorización |
|---|---|---|
| Pregunta | **¿Quién eres?** | **¿Qué puedes hacer?** |
| Ejemplo | Iniciar sesión con usuario y contraseña | Solo el administrador puede borrar usuarios |
| Orden | Primero | Después |

---

## 3. Sintaxis / estructura

La seguridad no tiene una sintaxis única, pero hay una **estructura de defensa** que conviene seguir. Se llama **defensa en profundidad**: usar varias capas, de forma que si una falla, otra te protege.

```
Usuario
   ↓
1. HTTPS ......................... protege el camino
   ↓
2. Validación de entrada ......... filtra lo que llega
   ↓
3. Autenticación y autorización .. controla quién entra y qué hace
   ↓
4. Consultas y salidas seguras ... evita inyecciones
   ↓
5. Cabeceras de seguridad ........ refuerzan el navegador
   ↓
Datos
```

### Flujo seguro de una petición

1. El usuario envía datos por HTTPS.
2. El servidor **valida** el formato y los límites.
3. El servidor comprueba **quién es** y **si tiene permiso**.
4. El servidor usa los datos de forma segura (consultas preparadas).
5. El servidor **escapa** lo que muestra en pantalla.
6. La respuesta lleva cabeceras de seguridad.

---

## 4. Elementos / propiedades / características

### 4.1 HTTPS

**HTTP** envía la información como texto normal: cualquiera en medio (por ejemplo, en una wifi pública) podría leerla. **HTTPS** la **cifra**.

- Protege contraseñas, cookies y datos personales mientras viajan.
- Se activa con un **certificado TLS** (hoy es gratis con Let's Encrypt).
- Hoy **todas** las webs deben usar HTTPS, aunque no manejen datos "importantes".

### 4.2 Inyección SQL (SQL Injection)

Ocurre cuando el usuario consigue que **su texto se ejecute como código SQL**.

```js
// ❌ Vulnerable: se pega el texto del usuario dentro de la consulta
const consulta = "SELECT * FROM usuarios WHERE nombre = '" + nombre + "'";
```

Si el usuario escribe `' OR '1'='1`, la consulta se convierte en:

```sql
SELECT * FROM usuarios WHERE nombre = '' OR '1'='1'
```

Eso devuelve **todos** los usuarios. Con otros textos se podrían incluso borrar tablas.

**Solución:** usar **consultas preparadas** (parametrizadas). Los datos se envían **aparte** del código SQL y nunca se interpretan como código.

```js
// ✅ Seguro: el valor va como parámetro
const [filas] = await db.execute(
  "SELECT * FROM usuarios WHERE nombre = ?",
  [nombre]
);
```

```php
// ✅ Seguro en PHP con PDO
$stmt = $pdo->prepare("SELECT * FROM usuarios WHERE nombre = :nombre");
$stmt->execute(["nombre" => $nombre]);
```

### 4.3 XSS (Cross-Site Scripting)

Ocurre cuando una web **muestra texto del usuario sin protegerlo** y ese texto contiene **código JavaScript** que se ejecuta en el navegador de otras personas.

```js
// ❌ Vulnerable: innerHTML interpreta el texto como HTML
comentario.innerHTML = textoDelUsuario;
// Si el texto es: <img src=x onerror="robarCookies()">  → se ejecuta
```

Con XSS un atacante puede robar sesiones, cambiar lo que ve la víctima o redirigirla a otra web.

**Soluciones:**

```js
// ✅ textContent trata el texto como texto, no como HTML
comentario.textContent = textoDelUsuario;
```

- **Escapar** la salida: convertir `<` en `&lt;`, `>` en `&gt;`, etc.
- Usar **frameworks** (React, Vue, Angular) que escapan por defecto.
- Si de verdad necesitas HTML del usuario, límpialo con una librería como **DOMPurify**.
- Añadir una **Content Security Policy** (ver 4.8).

```php
// ✅ En PHP
echo htmlspecialchars($texto, ENT_QUOTES, 'UTF-8');
```

### 4.4 CSRF (Cross-Site Request Forgery)

Un atacante **engaña al navegador de un usuario con sesión iniciada** para que haga una acción sin darse cuenta (por ejemplo, transferir dinero) desde otra web maliciosa.

Funciona porque el navegador **envía las cookies automáticamente** en cada petición a tu sitio.

**Soluciones:**
- **Token CSRF**: un valor secreto único que va en cada formulario y que el servidor comprueba.
- Cookies con **`SameSite=Lax`** o **`Strict`**.
- No cambiar datos con peticiones `GET` (usar `POST`, `PUT`, `DELETE`).

```html
<form method="POST" action="/transferir">
  <input type="hidden" name="csrf_token" value="a8f3k29x...">
  <!-- resto del formulario -->
</form>
```

### 4.5 Contraseñas

Una contraseña **nunca se guarda tal cual** en la base de datos. Se guarda su **hash**: el resultado de una operación matemática que **no se puede deshacer**.

| Opción | ¿Es válida? |
|---|---|
| Guardar la contraseña en texto plano | ❌ Nunca |
| Cifrarla (se puede descifrar) | ❌ No es lo adecuado |
| MD5 o SHA-1 | ❌ Demasiado rápidos y rotos |
| **bcrypt**, **argon2** o **scrypt** | ✅ Diseñados para contraseñas |

```js
// Node.js con bcrypt
import bcrypt from "bcrypt";

// Al registrarse: guardar el hash
const hash = await bcrypt.hash(contrasena, 10);

// Al iniciar sesión: comparar
const esCorrecta = await bcrypt.compare(contrasenaEscrita, hash);
```

```php
// PHP
$hash = password_hash($contrasena, PASSWORD_DEFAULT);
$esCorrecta = password_verify($contrasenaEscrita, $hash);
```

> [!tip] ¿Por qué un hash y no cifrado?
> Si alguien roba tu base de datos, con un hash bien hecho **no puede recuperar** las contraseñas originales. Con cifrado reversible, sí podría si consigue la clave.

### 4.6 Sesiones y cookies

Cuando alguien inicia sesión, el servidor le da una **cookie de sesión** que funciona como un carné temporal. Si alguien roba esa cookie, **puede hacerse pasar por el usuario**.

Atributos que protegen las cookies:

| Atributo | Qué hace |
|---|---|
| `HttpOnly` | JavaScript **no puede leer** la cookie (frena el robo por XSS) |
| `Secure` | Solo se envía por **HTTPS** |
| `SameSite` | Limita el envío desde otras webs (frena CSRF) |
| `Max-Age` / `Expires` | Hace que la sesión **caduque** |

```
Set-Cookie: sesion=abc123; HttpOnly; Secure; SameSite=Lax; Max-Age=3600
```

### 4.7 Validación y sanitización de entradas

- **Validar**: comprobar que el dato tiene el formato esperado y **rechazarlo** si no (por ejemplo, que un email parezca un email).
- **Sanitizar**: limpiar o transformar el dato para quitar partes peligrosas.
- **Escapar** (en la salida): convertir los caracteres especiales para que se muestren como texto.

Qué comprobar siempre:
- **Tipo** (número, texto, fecha…).
- **Longitud** (mínima y máxima).
- **Formato** (con una regex, por ejemplo: [[04 - Regex]]).
- **Valores permitidos** (lista blanca).

> [!tip] Lista blanca mejor que lista negra
> Es más seguro **permitir solo lo que sabes que es válido** que intentar bloquear todo lo malo, porque siempre habrá algo que se te olvide.

### 4.8 Cabeceras de seguridad

Son cabeceras HTTP que el servidor envía para que el navegador **se proteja más**.

| Cabecera | Para qué sirve |
|---|---|
| `Content-Security-Policy` (CSP) | Define de dónde se pueden cargar scripts, estilos e imágenes. Frena el XSS |
| `Strict-Transport-Security` (HSTS) | Obliga al navegador a usar siempre HTTPS |
| `X-Content-Type-Options: nosniff` | Evita que el navegador "adivine" el tipo de un fichero |
| `X-Frame-Options` / `frame-ancestors` | Evita que tu web se muestre dentro de un `<iframe>` ajeno (clickjacking) |
| `Referrer-Policy` | Controla cuánta información de origen se envía |

```
Content-Security-Policy: default-src 'self'; script-src 'self'
Strict-Transport-Security: max-age=31536000; includeSubDomains
X-Content-Type-Options: nosniff
```

### 4.9 CORS

El navegador **bloquea por defecto** que una web lea respuestas de **otro dominio**. **CORS** es el mecanismo con el que el servidor dice qué otros dominios tienen permiso.

```
Access-Control-Allow-Origin: https://mi-frontend.com
```

> [!warning] CORS no es una protección del servidor
> CORS solo controla lo que hace el **navegador**. Alguien puede hacer peticiones directas con `curl` o Postman. La seguridad real sigue estando en la autenticación y la autorización del servidor.

### 4.10 Subida de ficheros

Un fichero subido puede ser peligroso (por ejemplo, un script con extensión engañosa). Hay que:
- Comprobar el **tipo real** del fichero, no solo la extensión.
- Limitar el **tamaño**.
- **Renombrar** el fichero con un nombre generado por ti.
- Guardarlo **fuera de la carpeta pública** o en un servicio de almacenamiento aparte.
- **Nunca** ejecutar ficheros subidos por usuarios.

### 4.11 Dependencias y secretos

- **Dependencias**: las librerías que instalas pueden tener fallos conocidos. Revisa con `npm audit` y actualiza con frecuencia.
- **Secretos** (claves de API, contraseñas de la base de datos): **nunca** en el código ni en Git. Se guardan en variables de entorno (ver [[05 - Variables de entorno]]).

```bash
# Revisar vulnerabilidades en un proyecto Node
npm audit
```

---

## 5. Ejemplos prácticos

### 5.1 Ejemplo básico: mostrar texto del usuario sin riesgo

```js
const nombre = prompt("¿Cómo te llamas?");

// ❌ Peligroso
document.querySelector("#saludo").innerHTML = "Hola, " + nombre;

// ✅ Seguro
document.querySelector("#saludo").textContent = "Hola, " + nombre;
```

**Explicación:** `textContent` trata todo como texto. Si el usuario escribe `<script>...</script>`, se muestra tal cual en pantalla y **no se ejecuta**.

### 5.2 Ejemplo básico: escapar HTML a mano

```js
function escaparHTML(texto) {
  return texto
    .replaceAll("&", "&amp;")
    .replaceAll("<", "&lt;")
    .replaceAll(">", "&gt;")
    .replaceAll('"', "&quot;")
    .replaceAll("'", "&#39;");
}

escaparHTML("<b>hola</b>"); // "&lt;b&gt;hola&lt;/b&gt;"
```

### 5.3 Ejemplo habitual: validar datos en el servidor (Express)

```js
import express from "express";
const app = express();
app.use(express.json());

app.post("/registro", (req, res) => {
  const { email, edad } = req.body;

  // 1. Tipo y formato
  const emailValido = typeof email === "string" && /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
  const edadValida = Number.isInteger(edad) && edad >= 0 && edad <= 120;

  // 2. Rechazar si algo no cuadra
  if (!emailValido || !edadValida) {
    return res.status(400).json({ error: "Datos no válidos" });
  }

  // 3. Aquí ya es seguro continuar
  res.status(201).json({ mensaje: "Usuario creado" });
});
```

**Explicación:** el servidor no se fía de lo que llega. Si algo no cumple, responde con **400** (petición incorrecta) y no sigue.

### 5.4 Ejemplo habitual: evitar inyección SQL en Node.js

```js
// ❌ Vulnerable
const sql = `SELECT * FROM productos WHERE id = ${req.params.id}`;

// ✅ Seguro: consulta preparada
const [filas] = await db.execute(
  "SELECT * FROM productos WHERE id = ?",
  [req.params.id]
);
```

### 5.5 Ejemplo habitual: cookie de sesión segura en Express

```js
res.cookie("sesion", idDeSesion, {
  httpOnly: true,       // JavaScript no puede leerla
  secure: true,         // solo por HTTPS
  sameSite: "lax",      // frena CSRF
  maxAge: 60 * 60 * 1000 // 1 hora
});
```

### 5.6 Ejemplo completo: registro e inicio de sesión con contraseña protegida

```js
import express from "express";
import bcrypt from "bcrypt";

const app = express();
app.use(express.json());

// Base de datos simulada
const usuarios = [];

app.post("/registro", async (req, res) => {
  const { email, contrasena } = req.body;

  if (typeof contrasena !== "string" || contrasena.length < 8) {
    return res.status(400).json({ error: "La contraseña debe tener al menos 8 caracteres" });
  }

  const hash = await bcrypt.hash(contrasena, 10);
  usuarios.push({ email, hash });

  res.status(201).json({ mensaje: "Registrado" });
});

app.post("/login", async (req, res) => {
  const { email, contrasena } = req.body;
  const usuario = usuarios.find(u => u.email === email);

  // Mismo mensaje si falla el email o la contraseña
  if (!usuario || !(await bcrypt.compare(contrasena, usuario.hash))) {
    return res.status(401).json({ error: "Credenciales incorrectas" });
  }

  res.json({ mensaje: "Sesión iniciada" });
});
```

**Puntos clave:**
- La contraseña **nunca se guarda** tal cual, solo su hash.
- El mensaje de error es **el mismo** si falla el email o la contraseña, para no dar pistas a un atacante.
- Se exige una **longitud mínima**.

### 5.7 Ejemplo: añadir cabeceras de seguridad con Helmet

```js
import express from "express";
import helmet from "helmet";

const app = express();
app.use(helmet()); // añade varias cabeceras de seguridad de golpe
```

```bash
npm install helmet
```

---

## 6. Buenas prácticas

- **Valida siempre en el servidor**, aunque ya valides en el navegador.
- **Usa HTTPS** en todo el sitio y activa HSTS.
- **Usa consultas preparadas** en todas las consultas a la base de datos, sin excepciones.
- **Escapa lo que muestras** y prefiere `textContent` a `innerHTML`.
- **Guarda las contraseñas con bcrypt o argon2**, nunca en texto plano.
- **Da el mínimo de permisos necesario** (*principio de mínimo privilegio*): cada usuario y cada servicio solo con lo que necesita.
- **Comprueba la autorización en cada petición**, no solo ocultando botones en la pantalla.
- **No muestres errores técnicos al usuario**: da un mensaje genérico y guarda los detalles en un registro interno.
- **No guardes secretos en el código** ni los subas a Git. Usa variables de entorno y un `.gitignore`.
- **Mantén las dependencias actualizadas** y revisa con `npm audit`.
- **Limita los intentos de inicio de sesión** (*rate limiting*) para frenar la fuerza bruta.
- **Haz copias de seguridad** y prueba que se pueden restaurar.
- **Registra eventos importantes** (inicios de sesión fallidos, cambios de permisos) para detectar problemas.
- **Usa la autenticación en dos pasos (2FA)** siempre que puedas.

---

## 7. Diferencias importantes

### Cifrar vs hashear

| | Cifrado | Hash |
|---|---|---|
| ¿Se puede revertir? | **Sí**, con la clave | **No** |
| Se usa para | Datos que luego hay que leer (un mensaje privado) | Contraseñas |
| Ejemplos | AES | bcrypt, argon2 |

### Validar vs sanitizar vs escapar

| | Cuándo | Qué hace |
|---|---|---|
| **Validar** | Al recibir el dato | Acepta o rechaza |
| **Sanitizar** | Al recibir el dato | Limpia partes peligrosas |
| **Escapar** | Al mostrar el dato | Convierte símbolos especiales en texto inofensivo |

### XSS vs CSRF

| | XSS | CSRF |
|---|---|---|
| El atacante consigue | Ejecutar **su código** en tu web | Que el usuario envíe **una petición** sin querer |
| Se aprovecha de | Que la web confía en el texto del usuario | Que el navegador envía cookies automáticamente |
| Defensa principal | Escapar la salida y CSP | Token CSRF y `SameSite` |

### `localStorage` vs cookie `HttpOnly`

| | `localStorage` | Cookie `HttpOnly` |
|---|---|---|
| ¿JavaScript puede leerlo? | **Sí** | **No** |
| Riesgo ante XSS | Un script puede robar el contenido | Más protegida |
| Se envía automáticamente al servidor | No | Sí (por eso hay que vigilar CSRF) |

> [!tip] Para guardar tokens de sesión
> Normalmente es más seguro usar una **cookie `HttpOnly`** que `localStorage`.

### HTTP vs HTTPS

| | HTTP | HTTPS |
|---|---|---|
| Cifrado | No | Sí |
| Candado en el navegador | No | Sí |
| Recomendado hoy | ❌ | ✅ |

---

## 8. Casos especiales

### 8.1 Otros ataques que conviene conocer

| Ataque | Qué es | Defensa básica |
|---|---|---|
| **Fuerza bruta** | Probar muchas contraseñas hasta acertar | Límite de intentos, 2FA, bloqueos temporales |
| **Clickjacking** | Tu web se muestra invisible dentro de otra para engañar a hacer clic | `frame-ancestors` / `X-Frame-Options` |
| **Man-in-the-middle** | Alguien intercepta la comunicación | HTTPS y HSTS |
| **Path traversal** | Usar `../../` para acceder a ficheros del servidor | Validar rutas, no usar la entrada directamente |
| **Inyección de comandos** | El texto del usuario se ejecuta en la terminal | No pasar datos a comandos del sistema |
| **DoS / DDoS** | Saturar el servidor con peticiones | Límites de peticiones, CDN, proxies |
| **IDOR** | Cambiar un `id` en la URL para ver datos de otro | Comprobar siempre que el recurso pertenece al usuario |

### 8.2 IDOR: el fallo de autorización más típico

```
GET /pedidos/1042     ← pedido propio
GET /pedidos/1043     ← ¿el usuario puede ver el de otra persona?
```

```js
// ✅ Comprobar que el pedido es del usuario que lo pide
const pedido = await db.buscarPedido(req.params.id);
if (pedido.usuarioId !== req.usuario.id) {
  return res.status(403).json({ error: "No tienes permiso" });
}
```

### 8.3 Códigos de estado para errores de seguridad

| Código | Significado | Cuándo |
|---|---|---|
| `400` | Petición incorrecta | Datos no válidos |
| `401` | No autenticado | No ha iniciado sesión o credenciales erróneas |
| `403` | Prohibido | Ha iniciado sesión, pero no tiene permiso |
| `404` | No encontrado | A veces se usa para no revelar que algo existe |
| `429` | Demasiadas peticiones | Límite de intentos superado |

### 8.4 JWT (JSON Web Token)

Un **JWT** es un token firmado que lleva información del usuario. Se usa mucho en APIs.

- Está **firmado**, pero **no cifrado**: cualquiera puede leer su contenido. No metas datos secretos dentro.
- Ponle una **caducidad corta**.
- Guarda la clave de firma como secreto.

### 8.5 Expresiones regulares y rendimiento

Una regex mal escrita puede volverse muy lenta con ciertos textos (ataque **ReDoS**). Si validas texto de usuarios con regex, evita repeticiones anidadas y limita la longitud antes. Más detalle en [[04 - Regex]].

### 8.6 Seguridad en entorno de desarrollo y producción

- En **producción** desactiva los mensajes de error detallados y el modo *debug*.
- No dejes **usuarios y contraseñas por defecto** (`admin/admin`).
- No expongas puertos innecesarios (en Docker, publica solo los que hagan falta).
- Separa las claves de **desarrollo** y de **producción**.

### 8.7 Lista de comprobación rápida antes de publicar

- [ ] La web funciona solo por HTTPS.
- [ ] Las consultas SQL son preparadas.
- [ ] Se escapa todo lo que viene del usuario al mostrarlo.
- [ ] Las contraseñas se guardan con bcrypt o argon2.
- [ ] Las cookies tienen `HttpOnly`, `Secure` y `SameSite`.
- [ ] Hay token CSRF en los formularios que cambian datos.
- [ ] Hay cabeceras de seguridad (por ejemplo, con Helmet).
- [ ] Los secretos no están en el código ni en Git.
- [ ] `npm audit` no muestra fallos graves.
- [ ] Hay límite de intentos de inicio de sesión.

---

## 9. Resumen

- La regla de oro: **nunca confíes en los datos del usuario**; valida siempre en el **servidor**.
- **Autenticación** = quién eres. **Autorización** = qué puedes hacer.
- **HTTPS** cifra la comunicación y debe usarse siempre.
- **Inyección SQL** → se evita con **consultas preparadas**.
- **XSS** → se evita **escapando la salida** (`textContent`), usando frameworks y una CSP.
- **CSRF** → se evita con **token CSRF** y cookies `SameSite`.
- **Contraseñas** → se guardan con **hash** (bcrypt o argon2), nunca en texto plano.
- **Cookies de sesión** → `HttpOnly`, `Secure`, `SameSite` y con caducidad.
- **Cabeceras de seguridad** (CSP, HSTS, `nosniff`…) refuerzan al navegador; Helmet las pone fácilmente en Express.
- **CORS** controla al navegador, **no** sustituye a la autenticación del servidor.
- Aplica el **mínimo privilegio**, mantén las **dependencias actualizadas** y guarda los **secretos fuera del código**.
- La seguridad se construye en **capas** (defensa en profundidad): si una falla, otra te protege.