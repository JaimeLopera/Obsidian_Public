# 03 - Dockerfile

> [!info] ¿Qué es?
> Un **Dockerfile** es un archivo de texto con instrucciones paso a paso para **construir una imagen** propia. Cada instrucción añade una capa a la imagen final.

---

## 1. Antes de empezar

Conviene haber leído [[02 - Imágenes y contenedores]]. Para los ejemplos con Node, ver [[Node.js y npm/02 - npm y package.json]].

---

## 2. Concepto fundamental

El flujo es:

```
Dockerfile ──docker build──► Imagen ──docker run──► Contenedor
```

Un Dockerfile responde a estas preguntas, en orden:

1. ¿De qué imagen parto? (`FROM`)
2. ¿Dónde trabajo? (`WORKDIR`)
3. ¿Qué archivos copio? (`COPY`)
4. ¿Qué instalo o preparo? (`RUN`)
5. ¿Qué puerto usa la app? (`EXPOSE`)
6. ¿Cómo se arranca? (`CMD`)

---

## 3. Sintaxis / estructura

```dockerfile
FROM node:20-alpine

WORKDIR /app

COPY package*.json ./
RUN npm ci

COPY . .

EXPOSE 3000

CMD ["node", "index.js"]
```

Construir y ejecutar:

```bash
docker build -t mi-app:1.0 .
docker run -d -p 3000:3000 --name mi-app mi-app:1.0
```

- `-t` pone nombre y etiqueta a la imagen.
- El punto final `.` es el **contexto**: la carpeta cuyos archivos puede usar el build.

---

## 4. Elementos / propiedades / características

### 4.1 Instrucciones principales

| Instrucción | Qué hace |
|---|---|
| `FROM` | Imagen base de la que partes (siempre la primera) |
| `WORKDIR` | Carpeta de trabajo dentro de la imagen (la crea si no existe) |
| `COPY origen destino` | Copia archivos de tu equipo a la imagen |
| `RUN` | Ejecuta un comando **al construir** (instalar, compilar) |
| `ENV` | Define una variable de entorno |
| `ARG` | Variable que solo existe **durante la construcción** |
| `EXPOSE` | Documenta qué puerto usa la app |
| `USER` | Usuario con el que se ejecuta lo siguiente |
| `CMD` | Comando por defecto **al arrancar** el contenedor |
| `ENTRYPOINT` | Programa fijo que siempre se ejecuta al arrancar |
| `VOLUME` | Declara un punto donde se guardarán datos |
| `HEALTHCHECK` | Comprueba si la app está sana |

### 4.2 `.dockerignore`

Archivo que indica qué **no** enviar al build. Funciona como `.gitignore`:

```
node_modules
.git
.env
Dockerfile
*.log
```

Acelera la construcción y evita meter secretos o carpetas pesadas.

### 4.3 Formas de escribir `CMD` y `RUN`

| Forma | Ejemplo | Comentario |
|---|---|---|
| **Exec** (lista) | `CMD ["node", "index.js"]` | Recomendada para `CMD` y `ENTRYPOINT` |
| **Shell** (texto) | `CMD node index.js` | Se ejecuta dentro de una shell |

### 4.4 Caché de capas

Docker reutiliza una capa si ni la instrucción ni lo anterior han cambiado. Por eso se **copia primero lo que cambia poco** (`package.json`) y **después lo que cambia mucho** (el código). Así `npm ci` no se repite en cada cambio de código.

### 4.5 Construcción en varias etapas (*multi-stage*)

Sirve para que la imagen final sea pequeña: se compila en una etapa y solo se copia el resultado a otra.

```dockerfile
# Etapa 1: construir
FROM node:20-alpine AS build
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# Etapa 2: imagen final
FROM nginx:alpine
COPY --from=build /app/dist /usr/share/nginx/html
EXPOSE 80
```

---

## 5. Ejemplos prácticos

### 5.1 Ejemplo básico

```dockerfile
FROM python:3.12-slim
WORKDIR /app
COPY . .
CMD ["python", "main.py"]
```

### 5.2 Ejemplo habitual: API de Node

```dockerfile
FROM node:20-alpine
WORKDIR /app

COPY package*.json ./
RUN npm ci --omit=dev

COPY . .

ENV NODE_ENV=production
USER node
EXPOSE 3000

CMD ["node", "index.js"]
```

---

## 6. Buenas prácticas

- **Usa una imagen base pequeña y con versión** (`node:20-alpine`).
- **Ordena las instrucciones** de lo que menos cambia a lo que más cambia.
- **Crea un `.dockerignore`** siempre.
- **No metas secretos** (contraseñas, claves) en el Dockerfile ni con `ENV`: se quedan en la imagen.
- **No ejecutes como administrador:** usa `USER`.
- **Encadena comandos relacionados** en un solo `RUN` para no crear capas de más.
- **Un proceso por contenedor.**
- **Usa multi-stage** para proyectos que se compilan.
- **`npm ci`** en vez de `npm install` para instalaciones reproducibles.

---

## 7. Diferencias importantes

### 7.1 `RUN` vs. `CMD`

| | `RUN` | `CMD` |
|---|---|---|
| Cuándo se ejecuta | Al **construir** la imagen | Al **arrancar** el contenedor |
| Cuántas veces | Muchas (cada una crea capa) | Solo la última cuenta |
| Para qué | Instalar, compilar | Iniciar la app |

### 7.2 `CMD` vs. `ENTRYPOINT`

| | `CMD` | `ENTRYPOINT` |
|---|---|---|
| Se puede sobrescribir fácilmente con `docker run ... otro-comando` | Sí | No (hace falta `--entrypoint`) |
| Uso típico | Comando por defecto | Programa fijo de la imagen |

Se pueden combinar: `ENTRYPOINT` es el programa y `CMD` son sus argumentos por defecto.

### 7.3 `COPY` vs. `ADD`

`COPY` solo copia. `ADD` además descomprime tar y descarga URLs, lo que a veces sorprende. **Prefiere `COPY`.**

### 7.4 `ENV` vs. `ARG`

| | `ENV` | `ARG` |
|---|---|---|
| Disponible al ejecutar | Sí | No |
| Disponible al construir | Sí | Sí |
| Se define con | `ENV X=1` | `--build-arg X=1` |

### 7.5 `EXPOSE` vs. `-p`

`EXPOSE` solo **documenta**. Lo que realmente publica el puerto es `-p` al ejecutar (o `ports` en Compose).

---

## 8. Casos especiales

### 8.1 Variables en `RUN` y `CMD`

La forma *exec* (`["node", "x"]`) **no** expande variables como `$PORT`. Si las necesitas, usa la forma *shell* o `["sh", "-c", "..."]`.

### 8.2 Argumentos de `FROM`

```dockerfile
ARG NODE_VERSION=20
FROM node:${NODE_VERSION}-alpine
```

### 8.3 Reconstruir sin caché

```bash
docker build --no-cache -t mi-app .
```

### 8.4 Imágenes mínimas

`FROM scratch` parte de una imagen vacía; se usa con programas compilados estáticamente (Go, por ejemplo).

### 8.5 Archivos de entorno

No copies `.env` a la imagen. Pásalo al ejecutar con `--env-file .env` o en Compose ([[05 - Docker Compose]]).

### 8.6 Dos Dockerfile en un proyecto

```bash
docker build -f Dockerfile.dev -t mi-app:dev .
```

---

## 9. Resumen

- Un **Dockerfile** son las instrucciones para construir una imagen; cada una crea una **capa**.
- Estructura típica: `FROM` → `WORKDIR` → `COPY` → `RUN` → `EXPOSE` → `CMD`.
- `docker build -t nombre:etiqueta .` construye la imagen.
- `RUN` actúa al construir; `CMD` al arrancar.
- Copia primero `package.json` e instala, luego el código, para aprovechar la **caché**.
- `.dockerignore` evita enviar `node_modules`, `.git` y `.env`.
- **Multi-stage** reduce el tamaño final.
- No metas secretos en la imagen; ejecuta con un usuario no administrador.
- Prefiere `COPY` a `ADD` y la forma *exec* en `CMD`.