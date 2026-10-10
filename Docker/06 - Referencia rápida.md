# 06 - Referencia rápida

> [!info] ¿Qué es?
> Chuleta de **Docker** para consultar rápido. No explica, solo recuerda. Si algo no te suena, sigue el enlace a su nota.

---

## 1. Fundamentos ([[DESARROLLO WEB/PHP/01 - Fundamentos]])

| Concepto | Resumen |
|---|---|
| Imagen | Plantilla de solo lectura |
| Contenedor | Imagen en ejecución |
| Registro | Donde se guardan las imágenes (Docker Hub) |
| Capa | Cada instrucción del Dockerfile; se cachea |

```bash
docker --version
docker info
docker run hello-world
```

---

## 2. Imágenes ([[02 - Imágenes y contenedores]])

```bash
docker pull nginx:alpine          # descargar
docker image ls                   # listar
docker image rm nginx:alpine      # borrar
docker image prune                # borrar las no usadas
docker tag mi-app usuario/mi-app:1.0
docker push usuario/mi-app:1.0    # subir a un registro
docker login
```

---

## 3. Contenedores ([[02 - Imágenes y contenedores]])

```bash
docker run -d -p 8080:80 --name web nginx
docker ps                         # en ejecución
docker ps -a                      # todos
docker stop web
docker start web
docker restart web
docker rm web                     # borrar (parado)
docker rm -f web                  # forzar
docker container prune            # borrar todos los parados
```

| Opción de `run` | Significado |
|---|---|
| `-d` | Segundo plano |
| `-it` | Interactivo con terminal |
| `--name` | Nombre del contenedor |
| `-p 8080:80` | Puerto tuyo : puerto del contenedor |
| `-e CLAVE=valor` | Variable de entorno |
| `--env-file .env` | Variables desde archivo |
| `-v datos:/ruta` | Volumen |
| `--network red` | Conectar a una red |
| `--rm` | Borrar al terminar |
| `--restart unless-stopped` | Reinicio automático |

---

## 4. Depurar ([[02 - Imágenes y contenedores]])

```bash
docker logs web                   # ver logs
docker logs -f web                # seguirlos
docker exec -it web sh            # terminal dentro
docker inspect web                # detalles en JSON
docker stats                      # CPU y memoria
docker top web                    # procesos
docker cp web:/ruta ./local       # copiar archivos
```

---

## 5. Dockerfile ([[03 - Dockerfile]])

```dockerfile
FROM node:20-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
ENV NODE_ENV=production
USER node
EXPOSE 3000
CMD ["node", "index.js"]
```

```bash
docker build -t mi-app:1.0 .
docker build --no-cache -t mi-app .
docker build -f Dockerfile.dev -t mi-app:dev .
```

| Instrucción | Para qué |
|---|---|
| `FROM` | Imagen base |
| `WORKDIR` | Carpeta de trabajo |
| `COPY` | Copiar archivos |
| `RUN` | Comando al **construir** |
| `CMD` | Comando al **arrancar** |
| `ENTRYPOINT` | Programa fijo |
| `ENV` | Variable de entorno |
| `ARG` | Variable solo al construir |
| `EXPOSE` | Documentar puerto |
| `USER` | Usuario de ejecución |
| `HEALTHCHECK` | Comprobación de salud |

**`.dockerignore`:** `node_modules`, `.git`, `.env`, `*.log`.

---

## 6. Volúmenes ([[04 - Volúmenes y redes]])

```bash
docker volume create datos
docker volume ls
docker volume inspect datos
docker volume rm datos
docker volume prune

docker run -v datos:/var/lib/mysql mysql:8      # volumen
docker run -v "$(pwd)":/app mi-app              # bind mount
docker run -v ./config:/config:ro mi-app        # solo lectura
```

| Tipo | Uso |
|---|---|
| Volumen | Datos persistentes |
| Bind mount | Desarrollo |
| tmpfs | Datos temporales en RAM |

---

## 7. Redes ([[04 - Volúmenes y redes]])

```bash
docker network create mi-red
docker network ls
docker network inspect mi-red
docker network connect mi-red web
docker network rm mi-red

docker run -d --name db --network mi-red postgres:16
```

- En una red creada por ti, los contenedores se encuentran **por nombre**.
- Entre contenedores se usa el **puerto interno**.
- Tipos: `bridge` (por defecto), `host`, `none`.

---

## 8. Docker Compose ([[05 - Docker Compose]])

```yaml
services:
  api:
    build: .
    ports:
      - "3000:3000"
    env_file:
      - .env
    depends_on:
      db:
        condition: service_healthy

  db:
    image: postgres:16
    volumes:
      - pgdata:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 5s
      retries: 5

volumes:
  pgdata:
```

```bash
docker compose up -d
docker compose up -d --build
docker compose ps
docker compose logs -f api
docker compose exec api sh
docker compose stop
docker compose down
docker compose down -v          # ¡borra los volúmenes!
```

---

## 9. Limpieza

```bash
docker container prune          # contenedores parados
docker image prune              # imágenes sin usar
docker image prune -a           # todas las no usadas
docker volume prune             # volúmenes sin usar
docker network prune            # redes sin usar
docker system prune             # todo lo anterior (menos volúmenes)
docker system df                # espacio que ocupa Docker
```

---

## 10. Trampas que conviene recordar

- Lo que se escribe dentro de un contenedor **se pierde** al borrarlo: usa volúmenes.
- `localhost` dentro de un contenedor es **el propio contenedor**.
- `EXPOSE` no publica puertos; lo hace `-p`.
- `latest` cambia con el tiempo: fija versiones.
- `docker compose down -v` y `volume rm` borran datos para siempre.
- `depends_on` no espera a que el servicio esté **listo**, solo a que arranque.
- No metas contraseñas ni `.env` dentro de la imagen.
- Un cambio en el código no se ve si no reconstruyes (`--build`) o no usas bind mount.
- El YAML no admite tabuladores: usa espacios.
- Se usa `docker compose` (sin guion); `version:` es legado.