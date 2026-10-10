# 05 - Docker Compose

> [!info] ¿Qué es?
> **Docker Compose** permite definir y arrancar **varios contenedores** (web, base de datos, etc.) desde un único archivo `compose.yaml`, con un solo comando, en lugar de escribir muchos `docker run` largos.

---

## 1. Antes de empezar

Conviene dominar [[03 - Dockerfile]] y [[04 - Volúmenes y redes]]: Compose es esencialmente una forma ordenada de describirlos.

Necesitas saber leer **YAML**: se escribe con **sangría de espacios** (nunca tabuladores) y pares `clave: valor`.

---

## 2. Concepto fundamental

Un proyecto real suele tener varias piezas:

```
navegador ─► web/api ─► base de datos
```

Con Compose describes cada pieza como un **servicio** y Docker se encarga de crearlas, conectarlas en una red común y arrancarlas juntas.

| Sin Compose | Con Compose |
|---|---|
| Varios `docker run` largos | Un archivo |
| Crear red y volúmenes a mano | Se crean solos |
| Difícil de compartir | El archivo va en el repositorio |

---

## 3. Sintaxis / estructura

### 3.1 El archivo

Se llama `compose.yaml` (también vale `docker-compose.yml`).

```yaml
services:
  api:
    build: .
    ports:
      - "3000:3000"
    environment:
      DB_HOST: db
      DB_PASSWORD: secreto
    depends_on:
      - db

  db:
    image: postgres:16
    environment:
      POSTGRES_PASSWORD: secreto
    volumes:
      - pgdata:/var/lib/postgresql/data

volumes:
  pgdata:
```

### 3.2 Comandos básicos

```bash
docker compose up              # crea y arranca (en primer plano)
docker compose up -d           # en segundo plano
docker compose up --build      # reconstruye las imágenes antes
docker compose down            # para y borra contenedores y red
docker compose down -v         # además borra los volúmenes (¡datos!)
docker compose ps              # estado de los servicios
docker compose logs -f api     # logs de un servicio
docker compose exec api sh     # terminal dentro de un servicio
docker compose stop            # parar sin borrar
docker compose restart api
```

> [!warning] Obsoleto / legado
> El comando antiguo era `docker-compose` (con guion) y el archivo empezaba con `version: "3"`. Hoy se usa **`docker compose`** (sin guion) y la clave `version` ya no hace falta.

---

## 4. Elementos / propiedades / características

### 4.1 Claves principales de un servicio

| Clave | Qué hace |
|---|---|
| `image` | Imagen a usar (de Docker Hub u otra) |
| `build` | Carpeta con un Dockerfile para construir la imagen |
| `container_name` | Nombre fijo del contenedor (opcional) |
| `ports` | Publica puertos: `"host:contenedor"` |
| `environment` | Variables de entorno |
| `env_file` | Carga variables desde un archivo |
| `volumes` | Volúmenes y bind mounts |
| `depends_on` | Orden de arranque entre servicios |
| `networks` | Redes a las que se conecta |
| `restart` | Política de reinicio (`no`, `always`, `unless-stopped`) |
| `command` | Sustituye al `CMD` de la imagen |
| `healthcheck` | Cómo comprobar que el servicio está sano |

### 4.2 `build` con opciones

```yaml
api:
  build:
    context: ./backend
    dockerfile: Dockerfile.dev
```

### 4.3 Variables de entorno desde un archivo

```yaml
api:
  env_file:
    - .env
```

Dentro del propio `compose.yaml` también puedes usar variables de tu `.env`:

```yaml
db:
  image: postgres:${PG_VERSION}
```

### 4.4 Red automática

Compose crea una red para el proyecto y conecta todos los servicios. Se encuentran **por el nombre del servicio** (`db`, `api`...), sin configurar nada más.

### 4.5 `depends_on` con condición

Por defecto solo espera a que el contenedor **arranque**, no a que esté **listo**. Para esperar a que esté sano:

```yaml
api:
  depends_on:
    db:
      condition: service_healthy

db:
  image: postgres:16
  healthcheck:
    test: ["CMD-SHELL", "pg_isready -U postgres"]
    interval: 5s
    timeout: 3s
    retries: 5
```

### 4.6 Perfiles y archivos múltiples

- **`profiles`:** servicios opcionales que solo arrancan si los pides (`--profile herramientas`).
- **Varios archivos:** `docker compose -f compose.yaml -f compose.dev.yaml up` combina una configuración base con otra de desarrollo.

---

## 5. Ejemplos prácticos

### 5.1 Ejemplo básico: un servidor web

```yaml
services:
  web:
    image: nginx:alpine
    ports:
      - "8080:80"
    volumes:
      - ./sitio:/usr/share/nginx/html:ro
```

```bash
docker compose up -d
```

### 5.2 Ejemplo habitual: PHP + MySQL + phpMyAdmin

```yaml
services:
  web:
    build: .
    ports:
      - "8000:80"
    volumes:
      - ./src:/var/www/html
    depends_on:
      db:
        condition: service_healthy

  db:
    image: mysql:8
    environment:
      MYSQL_ROOT_PASSWORD: secreto
      MYSQL_DATABASE: tienda
    volumes:
      - datos:/var/lib/mysql
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost"]
      interval: 5s
      timeout: 3s
      retries: 10

  phpmyadmin:
    image: phpmyadmin
    ports:
      - "8081:80"
    environment:
      PMA_HOST: db
    depends_on:
      - db

volumes:
  datos:
```

---

## 6. Buenas prácticas

- **Guarda `compose.yaml` en el repositorio** junto al código.
- **Secretos en `.env`**, y `.env` en `.gitignore`. Sube un `.env.example` sin valores reales.
- **Volúmenes con nombre** para bases de datos.
- **Versiones concretas** en `image` (`postgres:16`), no `latest`.
- **No publiques puertos de bases de datos** si solo los usa otro servicio.
- **Usa `healthcheck` + `condition: service_healthy`** en vez de confiar solo en `depends_on`.
- **Usa `restart: unless-stopped`** para servicios que deben mantenerse vivos.
- **Un archivo de desarrollo y otro de producción**, con las diferencias aparte.
- **Cuidado con la sangría** del YAML: dos espacios, sin tabuladores.

---

## 7. Diferencias importantes

### 7.1 `docker compose up` vs. `start`

| | `up` | `start` |
|---|---|---|
| Crea contenedores si no existen | Sí | No |
| Crea red y volúmenes | Sí | No |
| Uso | Primer arranque o tras cambios | Reanudar algo parado |

### 7.2 `down` vs. `stop`

| | `stop` | `down` |
|---|---|---|
| Contenedores | Los para | Los para y **borra** |
| Red | Se mantiene | Se borra |
| Volúmenes | Se mantienen | Se mantienen (salvo con `-v`) |

### 7.3 `image` vs. `build`

`image` descarga algo ya hecho. `build` construye desde tu Dockerfile. Se pueden usar juntos para dar nombre a lo que construyes.

### 7.4 `ports` vs. `expose`

`ports` publica hacia tu máquina. `expose` solo deja el puerto disponible **entre contenedores** (y ya lo está en la red del proyecto; sirve más como documentación).

---

## 8. Casos especiales

### 8.1 Cambios que no se aplican

Si cambias el código o el Dockerfile y no ves cambios, reconstruye: `docker compose up -d --build`.

### 8.2 Los datos "reaparecen" o "desaparecen"

Reaparecen si el volumen sigue existiendo tras un `down`. Desaparecen si usaste `down -v`.

### 8.3 Variables sin definir

Si usas `${VARIABLE}` y no existe, Compose avisa con un aviso y la deja vacía. Puedes poner un valor por defecto: `${PUERTO:-3000}`.

### 8.4 Varios proyectos a la vez

El nombre del proyecto sale de la carpeta. Para cambiarlo: `docker compose -p otro-nombre up`.

### 8.5 Escalar un servicio

```bash
docker compose up -d --scale api=3
```

Funciona si no fijaste `container_name` ni un puerto del host único.

### 8.6 Modo desarrollo con recarga

Un bind mount del código más un comando de desarrollo (`npm run dev`) permite ver los cambios sin reconstruir.

---

## 9. Resumen

- **Compose** describe varios servicios en un `compose.yaml` y los gestiona con un solo comando.
- Estructura: `services`, `volumes` y, si hace falta, `networks`.
- Comandos: `up -d`, `down`, `ps`, `logs -f`, `exec`, `up --build`.
- Cada servicio usa `image` o `build`, y puede definir `ports`, `environment`, `volumes`, `depends_on`...
- Los servicios se encuentran **por nombre** en la red que crea Compose.
- `depends_on` solo ordena el arranque; para esperar a que esté listo hay que usar `healthcheck`.
- `down` borra contenedores y red; `down -v` también los **volúmenes** (datos).
- Secretos en `.env` fuera de Git.
- Se usa `docker compose` (sin guion) y la clave `version` es legado.