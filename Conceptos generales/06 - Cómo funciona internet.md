# 06 - Cómo funciona internet

> [!info] ¿Qué es?
> **Internet** es una red enorme de ordenadores conectados entre sí que se comunican siguiendo unas reglas comunes. Cuando abres una web, tu ordenador **pide** una página a otro ordenador (el servidor) y este **responde** enviándola. Esta nota explica, paso a paso, todo lo que ocurre entre que escribes una dirección y que ves la página.

---

## 1. Antes de empezar

- No hace falta saber nada de redes. Esta nota explica los conceptos desde cero.
- Internet **no es lo mismo que la web**:

| | Internet | La web (WWW) |
|---|---|---|
| Qué es | La **red física y lógica** que conecta ordenadores | Un **servicio** que funciona sobre internet (páginas, enlaces) |
| Ejemplo | Cables, routers, satélites | Páginas web con HTML, CSS y JavaScript |
| Otros servicios en internet | Correo, videollamadas, juegos online, FTP | — |

- Esta nota es la base para entender [[01 - HTTP]], [[02 - APIs REST]] y [[05 - Seguridad web]].

---

## 2. Concepto fundamental

### El modelo cliente-servidor

Casi todo en la web funciona con dos papeles:

| Papel | Quién es | Qué hace |
|---|---|---|
| **Cliente** | Tu navegador (o una app) | **Pide** cosas |
| **Servidor** | Un ordenador siempre encendido | **Responde** con lo que se le pide |

**Analogía:** es como un restaurante. Tú (cliente) le pides un plato al camarero, la cocina (servidor) lo prepara y te lo devuelve. Tú no necesitas saber cómo funciona la cocina.

### Petición y respuesta

1. El cliente envía una **petición** (*request*): "dame la página `/inicio`".
2. El servidor devuelve una **respuesta** (*response*): "aquí tienes el HTML" (o un error si algo falla).

### Los paquetes

La información **no viaja de una vez**. Se corta en trozos pequeños llamados **paquetes**. Cada paquete:
- Lleva la **dirección de origen** y la de **destino**.
- Puede viajar por **caminos distintos**.
- Se **reensambla** al llegar.

**Analogía:** es como enviar un libro por correo arrancando las páginas y mandándolas en varios sobres. Cada sobre lleva la dirección y el número de página, y el destinatario las junta en orden.

---

## 3. Sintaxis / estructura

### Qué pasa cuando escribes una URL

Imagina que escribes `https://www.ejemplo.com/contacto` y pulsas Intro. Esto es lo que ocurre:

```
1. El navegador mira la URL
        ↓
2. Consulta el DNS → obtiene la dirección IP del servidor
        ↓
3. Abre una conexión con el servidor (TCP)
        ↓
4. Negocia el cifrado (TLS) porque es HTTPS
        ↓
5. Envía la petición HTTP
        ↓
6. El servidor procesa la petición y envía la respuesta
        ↓
7. El navegador recibe el HTML y lo interpreta
        ↓
8. Pide los demás recursos (CSS, JS, imágenes)
        ↓
9. Pinta la página en pantalla
```

### Partes de una URL

```
https://www.ejemplo.com:443/contacto?tema=ayuda#formulario
└─┬─┘   └──────┬──────┘└┬┘└───┬───┘└─────┬─────┘└────┬────┘
protocolo    dominio  puerto ruta     parámetros   fragmento
```

| Parte | Qué es | Ejemplo |
|---|---|---|
| **Protocolo** | Las reglas de comunicación | `https` |
| **Dominio** | El nombre del servidor | `www.ejemplo.com` |
| **Puerto** | La "puerta" del servidor (opcional) | `443` |
| **Ruta** | Qué recurso se pide | `/contacto` |
| **Parámetros** (*query string*) | Datos extra en la petición | `?tema=ayuda` |
| **Fragmento** | Un punto dentro de la página (no se envía al servidor) | `#formulario` |

### Partes de un dominio

```
blog.ejemplo.com
 │      │     └── TLD (dominio de nivel superior): .com, .es, .org
 │      └──────── Dominio principal
 └─────────────── Subdominio
```

---

## 4. Elementos / propiedades / características

### 4.1 Dirección IP

Es el **número de identificación** de un dispositivo en una red, como la dirección de una casa.

| Versión | Aspecto | Ejemplo |
|---|---|---|
| **IPv4** | 4 números entre 0 y 255 | `192.168.1.10` |
| **IPv6** | Más larga, con letras y números | `2001:0db8:85a3::8a2e:0370:7334` |

- IPv4 se está **agotando** (hay unos 4.300 millones de direcciones), por eso existe IPv6, que tiene un número casi ilimitado.
- **IP pública**: la que ve internet (la de tu router).
- **IP privada**: la que usan los dispositivos dentro de tu casa (`192.168.x.x`, `10.x.x.x`). No se ven desde fuera.
- `127.0.0.1` o `localhost` es **tu propio ordenador**. Es lo que usas al probar una web en local.

### 4.2 DNS (Sistema de Nombres de Dominio)

Las personas recordamos **nombres** (`ejemplo.com`), pero los ordenadores usan **números** (IP). El **DNS** es como una **agenda de contactos** que traduce un nombre en su IP.

```
ejemplo.com  →  DNS  →  93.184.216.34
```

**Cómo se resuelve un nombre (simplificado):**
1. El navegador mira su **caché** (¿ya lo conozco?).
2. Si no, pregunta al **servidor DNS** de tu proveedor (o al que hayas configurado: Google `8.8.8.8`, Cloudflare `1.1.1.1`).
3. Este consulta a los servidores DNS de la jerarquía (raíz → TLD → servidor del dominio) hasta encontrar la IP.
4. La respuesta se guarda en caché durante un tiempo (**TTL**).

**Tipos de registros DNS más comunes:**

| Registro | Para qué sirve | Ejemplo |
|---|---|---|
| **A** | Nombre → IPv4 | `ejemplo.com → 93.184.216.34` |
| **AAAA** | Nombre → IPv6 | `ejemplo.com → 2606:2800:...` |
| **CNAME** | Un nombre es alias de otro | `www → ejemplo.com` |
| **MX** | Servidor de correo del dominio | `mail.ejemplo.com` |
| **TXT** | Texto libre (verificaciones, seguridad del correo) | `v=spf1 ...` |
| **NS** | Qué servidores gestionan el dominio | `ns1.proveedor.com` |

### 4.3 Puertos

Una IP identifica a **un ordenador**; un **puerto** identifica a **un servicio dentro de ese ordenador**. 

**Analogía:** la IP es la dirección del edificio y el puerto es el número de piso.

| Puerto | Servicio |
|---|---|
| `80` | HTTP |
| `443` | HTTPS |
| `22` | SSH (acceso remoto seguro) |
| `21` | FTP |
| `25` / `587` | Correo (SMTP) |
| `3306` | MySQL |
| `5432` | PostgreSQL |
| `3000`, `5173`, `8080` | Habituales al desarrollar en local |

Si no escribes el puerto en la URL, el navegador usa el `80` para HTTP y el `443` para HTTPS.

### 4.4 Protocolos

Un **protocolo** es un conjunto de **reglas** para que dos máquinas se entiendan.

| Protocolo | Para qué sirve |
|---|---|
| **IP** | Dirigir los paquetes hasta su destino |
| **TCP** | Asegurar que los datos llegan **completos y en orden** |
| **UDP** | Enviar datos **rápido**, sin garantías (vídeo en directo, juegos) |
| **HTTP / HTTPS** | Pedir y enviar páginas y datos de la web |
| **TLS** | Cifrar la comunicación (es lo que hace "la S" de HTTPS) |
| **DNS** | Traducir nombres en IP |
| **SMTP / IMAP / POP3** | Enviar y recibir correo |
| **SSH** | Controlar un servidor de forma remota y segura |
| **FTP / SFTP** | Transferir ficheros |
| **WebSocket** | Comunicación continua en ambos sentidos (chats, juegos online) |

### 4.5 TCP: el "apretón de manos"

Antes de enviar datos, cliente y servidor se saludan en tres pasos (*three-way handshake*):

```
Cliente  ──── SYN ────────►  Servidor     "¿Hay alguien?"
Cliente  ◄─── SYN-ACK ────   Servidor     "Sí, estoy aquí"
Cliente  ──── ACK ────────►  Servidor     "Perfecto, empezamos"
```

Después, TCP comprueba que cada paquete llega y **reenvía los que se pierden**.

### 4.6 TLS / HTTPS

Con HTTPS, tras conectar, ambos acuerdan una **clave de cifrado** y el servidor demuestra su identidad con un **certificado**. A partir de ahí, todo lo que viaja va cifrado.

- El certificado lo emite una **autoridad certificadora** (CA) de confianza.
- El navegador lo comprueba; si hay problema, muestra una advertencia de seguridad.

### 4.7 Cómo viajan los datos por la red

Entre tu ordenador y el servidor hay muchos dispositivos intermedios:

| Dispositivo | Qué hace |
|---|---|
| **Router** | Conecta tu red doméstica con internet y dirige los paquetes |
| **ISP** (proveedor de internet) | Te da acceso a internet (Movistar, Orange…) |
| **Routers intermedios** | Pasan los paquetes de red en red hasta el destino |
| **Cables submarinos y fibra** | Llevan la mayor parte del tráfico mundial |
| **Switch** | Conecta dispositivos dentro de una misma red local |

Puedes ver el recorrido real de los paquetes con:

```bash
traceroute ejemplo.com      # Linux y macOS
tracert ejemplo.com         # Windows
```

### 4.8 Servidores, hosting y centros de datos

- Un **servidor** es simplemente un ordenador (físico o virtual) que está siempre encendido y conectado, esperando peticiones.
- Un **centro de datos** es un edificio lleno de servidores.
- El **hosting** es alquilar espacio en un servidor para tu web.

| Tipo de alojamiento | Qué es |
|---|---|
| **Hosting compartido** | Varias webs en el mismo servidor. Barato, con menos control |
| **VPS** | Parte de un servidor reservada para ti, con más control |
| **Servidor dedicado** | Un servidor entero para ti |
| **Cloud** | Recursos que crecen o disminuyen según la demanda (AWS, Azure, Google Cloud) |
| **Plataformas modernas** | Despliegue sencillo de apps (Vercel, Netlify, Render…) |

### 4.9 CDN (Red de Distribución de Contenidos)

Una **CDN** guarda copias de tus ficheros (imágenes, CSS, JS) en servidores repartidos por todo el mundo. Cada visitante los recibe **desde el más cercano**, así carga más rápido y se reduce la carga de tu servidor.

```
Usuario en Madrid ──► servidor CDN en Madrid   (rápido)
Usuario en Tokio  ──► servidor CDN en Tokio    (rápido)
        en vez de ir los dos al servidor original en EE. UU.
```

### 4.10 Caché

La **caché** guarda copias para no repetir trabajo. Hay caché en varios sitios:

| Dónde | Qué guarda |
|---|---|
| **Navegador** | Ficheros ya descargados (imágenes, CSS, JS) |
| **DNS** | Traducciones nombre → IP |
| **CDN** | Copias de tu web cerca del usuario |
| **Servidor** | Resultados de consultas costosas |

La caché hace la web más rápida, pero a veces causa que **veas una versión antigua** de una página.

### 4.11 Proxy, firewall y balanceador

| Elemento | Qué hace |
|---|---|
| **Proxy** | Intermediario entre tú y el servidor (oculta tu IP, filtra, cachea) |
| **Proxy inverso** | Intermediario **delante del servidor** (Nginx), reparte peticiones y protege |
| **Firewall** | Filtra el tráfico permitido y bloquea el sospechoso |
| **Balanceador de carga** | Reparte las peticiones entre varios servidores para que no se sature ninguno |
| **VPN** | Crea un túnel cifrado y cambia tu IP aparente |

### 4.12 Cómo se interpreta una página en el navegador

Al recibir el HTML, el navegador:

1. **Lee el HTML** y construye el **DOM** (el árbol de elementos).
2. Descubre los ficheros CSS y JS y los **descarga**.
3. **Aplica los estilos** CSS a cada elemento.
4. Calcula el **diseño** (tamaños y posiciones).
5. **Pinta** la página en pantalla.
6. **Ejecuta el JavaScript**, que puede modificar el DOM y repetir parte del proceso.

Ver [[09 - DOM]] en JavaScript.

---

## 5. Ejemplos prácticos

### 5.1 Ejemplo básico: ver la IP de un dominio

```bash
nslookup ejemplo.com
# o también
dig ejemplo.com
```

**Explicación:** muestra la IP asociada al nombre, es decir, el resultado de la consulta DNS.

### 5.2 Ejemplo básico: comprobar si un servidor responde

```bash
ping ejemplo.com
```

**Explicación:** envía pequeños paquetes al servidor y mide cuánto tarda en contestar. Si hay respuesta, hay conexión.

### 5.3 Ejemplo habitual: hacer una petición HTTP desde la terminal

```bash
curl -i https://ejemplo.com
```

Respuesta (resumida):

```
HTTP/2 200
content-type: text/html; charset=UTF-8
cache-control: max-age=3600

<!doctype html>
<html>...
```

**Explicación:** `-i` muestra las **cabeceras** y el **código de estado** además del contenido. `200` significa "todo correcto".

### 5.4 Ejemplo habitual: hacer una petición desde JavaScript

```js
const respuesta = await fetch("https://api.ejemplo.com/usuarios");
console.log(respuesta.status);        // 200
const datos = await respuesta.json(); // el cuerpo convertido a objeto
console.log(datos);
```

**Explicación:** el navegador hace por debajo todo lo de la sección 3 (DNS, conexión, TLS, petición). Ver [[13 - Fetch y APIs]].

### 5.5 Ejemplo habitual: levantar un servidor en local

```bash
# Con Node.js (servidor estático rápido)
npx serve .

# Con Python
python3 -m http.server 8000
```

Después abres `http://localhost:8000` en el navegador.

**Explicación:** tu propio ordenador actúa a la vez de cliente y de servidor. `localhost` apunta a `127.0.0.1`.

### 5.6 Ejemplo completo: un servidor mínimo con Node.js

```js
import http from "node:http";

const servidor = http.createServer((req, res) => {
  console.log(`Petición: ${req.method} ${req.url}`);

  res.writeHead(200, { "Content-Type": "text/plain; charset=utf-8" });
  res.end("¡Hola desde el servidor!");
});

servidor.listen(3000, () => {
  console.log("Servidor escuchando en http://localhost:3000");
});
```

**Explicación:**
- `createServer` define qué hacer **cada vez que llega una petición**.
- `req` contiene lo que pide el cliente; `res` es la respuesta.
- `listen(3000)` abre el **puerto 3000** y se queda esperando.

Ver [[04 - Servidor con Express]] para una versión más cómoda.

### 5.7 Ejemplo: ver el recorrido de una petición en el navegador

1. Abre una página y pulsa **F12** (herramientas de desarrollo).
2. Ve a la pestaña **Red** (*Network*).
3. Recarga la página.
4. Verás cada recurso que se pidió, su **código de estado**, su **tamaño** y cuánto **tardó**.

> [!tip] Muy útil para depurar
> Aquí ves rápidamente si un fichero da `404`, si una API devuelve un error `500` o qué recurso hace lenta la página.

---

## 6. Buenas prácticas

- **Usa siempre HTTPS**, también en desarrollo cuando sea posible.
- **Reduce el número y el peso de las peticiones**: menos ficheros, imágenes optimizadas, código minificado.
- **Aprovecha la caché** con cabeceras como `Cache-Control` y nombres de fichero con versión.
- **Usa una CDN** para recursos estáticos si tu web tiene visitantes de varios países.
- **Carga lo importante primero**: CSS en el `<head>`, scripts con `defer` o al final.
- **Usa las herramientas del navegador (F12 → Red)** para entender qué ocurre de verdad.
- **Gestiona los errores de red** en tu código: la conexión puede fallar, ser lenta o cortarse.
- **No expongas puertos o servicios que no necesitas** (bases de datos abiertas a todo internet).
- **Usa nombres de dominio** y no IP directas en tu código.
- **Piensa en el idioma y la distancia**: un servidor lejano añade retraso (*latencia*).

---

## 7. Diferencias importantes

### Internet vs web

| | Internet | Web |
|---|---|---|
| Es | La infraestructura de red | Un servicio sobre esa red |
| Incluye | Web, correo, juegos, videollamadas | Páginas, enlaces, HTML |

### IP vs dominio vs puerto

| | IP | Dominio | Puerto |
|---|---|---|---|
| Qué identifica | Un dispositivo | Un nombre fácil de recordar | Un servicio dentro del dispositivo |
| Ejemplo | `93.184.216.34` | `ejemplo.com` | `443` |

### TCP vs UDP

| | TCP | UDP |
|---|---|---|
| Fiabilidad | Garantiza que llega todo y en orden | No garantiza nada |
| Velocidad | Más lento | Más rápido |
| Se usa en | Web, correo, descargas | Streaming, videollamadas, juegos |

### HTTP vs HTTPS

| | HTTP | HTTPS |
|---|---|---|
| Cifrado | No | Sí (TLS) |
| Puerto | 80 | 443 |
| Recomendado | ❌ | ✅ |

### IP pública vs IP privada

| | Pública | Privada |
|---|---|---|
| Visible desde internet | Sí | No |
| Ejemplo | `85.60.12.9` | `192.168.1.10` |
| Quién la asigna | Tu proveedor | Tu router |

### Proxy vs proxy inverso vs VPN

| | Proxy | Proxy inverso | VPN |
|---|---|---|---|
| Se coloca | Del lado del **cliente** | Del lado del **servidor** | Entre tu dispositivo y la red |
| Sirve para | Filtrar o ocultar al cliente | Repartir y proteger servidores | Cifrar todo tu tráfico |

### Frontend vs backend

| | Frontend | Backend |
|---|---|---|
| Dónde se ejecuta | En el **navegador** | En el **servidor** |
| Tecnologías | HTML, CSS, JavaScript | Node.js, PHP, Python, bases de datos |
| Qué hace | Mostrar e interactuar | Procesar, guardar y proteger datos |

### Web estática vs web dinámica

| | Estática | Dinámica |
|---|---|---|
| Contenido | Ficheros fijos, iguales para todos | Se genera según la petición o el usuario |
| Servidor | Solo entrega ficheros | Ejecuta código y consulta bases de datos |
| Ejemplo | Una landing page en HTML | Una tienda online |

---

## 8. Casos especiales

### 8.1 Cuando algo falla: ¿dónde está el problema?

| Síntoma | Posible causa |
|---|---|
| "No se puede encontrar la dirección del servidor" | Problema de **DNS** o el dominio no existe |
| "Tiempo de espera agotado" | El servidor no responde o hay un firewall bloqueando |
| "Conexión rechazada" | No hay nada escuchando en ese puerto |
| Advertencia del certificado | Certificado caducado, mal configurado o falso |
| Error `404` | La ruta pedida no existe en el servidor |
| Error `500` | Fallo interno del servidor |
| Error `502` / `503` | El servidor intermedio no obtiene respuesta o está sobrecargado |
| Veo una versión antigua de la web | Caché del navegador o de la CDN |

### 8.2 Latencia vs ancho de banda

| | Latencia | Ancho de banda |
|---|---|---|
| Qué mide | **Cuánto tarda** en llegar un dato | **Cuántos datos** caben por segundo |
| Unidad | Milisegundos (ms) | Mbps |
| Analogía | Lo que tarda un coche en llegar | Cuántos carriles tiene la carretera |

Tener mucho ancho de banda no arregla una latencia alta: una web puede cargar lentamente aunque tengas fibra, si el servidor está muy lejos.

### 8.3 CORS y el origen

Un **origen** es la combinación de protocolo + dominio + puerto. Si dos URL difieren en cualquiera de las tres partes, son orígenes distintos y el navegador aplica restricciones (CORS). Ver [[05 - Seguridad web]].

```
https://ejemplo.com       ┐
https://ejemplo.com:443   ┘ mismo origen
http://ejemplo.com        → distinto (protocolo)
https://api.ejemplo.com   → distinto (dominio)
https://ejemplo.com:8080  → distinto (puerto)
```

### 8.4 Actualización del DNS (propagación)

Si cambias la IP de un dominio, los cambios **no se ven al instante** en todo el mundo, porque las cachés DNS guardan la respuesta antigua hasta que caduca su **TTL**. Puede tardar desde minutos hasta horas.

### 8.5 NAT: muchos dispositivos, una sola IP pública

Dentro de casa, todos los dispositivos comparten **una IP pública**. El router, con **NAT**, recuerda qué dispositivo hizo cada petición y entrega la respuesta al correcto.

### 8.6 HTTP/1.1, HTTP/2 y HTTP/3

| Versión | Característica principal |
|---|---|
| **HTTP/1.1** | Muy extendido; una petición por conexión a la vez |
| **HTTP/2** | Varias peticiones **a la vez** por la misma conexión |
| **HTTP/3** | Funciona sobre **QUIC (UDP)**; más rápido y estable en redes inestables |

Normalmente no tienes que hacer nada: el navegador y el servidor negocian la mejor versión disponible.

### 8.7 Comunicación en tiempo real

El modelo normal es "el cliente pide, el servidor responde". Para chats o juegos online, donde el servidor debe **enviar datos sin que se los pidan**, se usan:

- **WebSocket**: conexión abierta continua en ambos sentidos.
- **Server-Sent Events (SSE)**: el servidor envía datos continuamente al cliente.
- **Polling**: el cliente pregunta cada pocos segundos (sencillo, pero poco eficiente).

---

## 9. Resumen

- **Internet** es la red mundial de ordenadores; la **web** es un servicio que funciona sobre ella.
- Casi todo sigue el modelo **cliente-servidor**: el cliente envía una **petición** y el servidor devuelve una **respuesta**.
- Los datos viajan en **paquetes** que se reensamblan al llegar.
- Al abrir una URL ocurre: **DNS → conexión TCP → TLS → petición HTTP → respuesta → el navegador pinta la página**.
- Partes de una URL: protocolo, dominio, puerto, ruta, parámetros y fragmento.
- **IP** identifica un dispositivo, el **dominio** es un nombre fácil de recordar y el **puerto** identifica un servicio. El **DNS** traduce dominios en IP.
- **TCP** es fiable, **UDP** es rápido; **HTTPS** = HTTP + cifrado TLS.
- Puertos habituales: `80` HTTP, `443` HTTPS, `22` SSH, `3306` MySQL.
- Una **CDN** acerca los ficheros al usuario y la **caché** evita repetir trabajo.
- Un **proxy inverso** y un **balanceador** protegen y reparten el trabajo entre servidores.
- Las herramientas del navegador (**F12 → Red**) y comandos como `ping`, `curl`, `nslookup` y `traceroute` sirven para ver qué ocurre de verdad.
- Entender cómo viaja la información ayuda a **depurar errores**, **mejorar el rendimiento** y **escribir código más seguro**.