# 04 - Volúmenes y redes

> [!info] ¿Qué es?
> Los **volúmenes** sirven para que los datos **sobrevivan** cuando un contenedor se borra. Las **redes** sirven para que los contenedores **se comuniquen** entre sí y con el exterior.

---

## 1. Antes de empezar

Conviene saber lo de [[02 - Imágenes y contenedores]] (especialmente `-p` y `-v`). Para entender las direcciones y puertos, ver [[06 - Cómo funciona internet]].

---

## 2. Concepto fundamental

### Datos

Todo lo que un contenedor escribe en su interior vive en una capa temporal. **Al borrar el contenedor, se pierde.** Para conservar datos (una base de datos, archivos subidos...) hay que guardarlos **fuera**.

### Comunicación

Cada contenedor está aislado. Para que, por ejemplo, tu API hable con tu base de datos, ambos deben estar en la **misma red** de Docker.

---

## 3. Sintaxis / estructura

### 3.1 Volúmenes

```bash
docker volume create datos        # crear
docker volume ls                  # listar
docker volume inspect datos       # ver detalles
docker volume rm datos            # borrar
docker volume prune               # borrar los que no se usan
```

Usar un volumen al ejecutar:

```bash
docker run -d -v datos:/var/lib/mysql mysql:8
```

Formato: `-v origen:destino[:opciones]`.

### 3.2 Redes

```bash
docker network create mi-red
docker network ls
docker network inspect mi-red
docker network rm mi-red
```

Conectar contenedores a la red:

```bash
docker run -d --name base --network mi-red mysql:8
docker run -d --name api  --network mi-red -p 3000:3000 mi-app
```

Desde `api`, la base se encuentra en el host **`base`** (el nombre del contenedor).

---

## 4. Elementos / propiedades / características

### 4.1 Tipos de almacenamiento

| Tipo | Cómo se escribe | Dónde vive | Cuándo usarlo |
|---|---|---|---|
| **Volumen** | `-v datos:/ruta` | Gestionado por Docker | Datos persistentes (bases de datos). **Lo recomendado** |
| **Bind mount** | `-v ./carpeta:/ruta` | Una carpeta de tu equipo | Desarrollo: ver tus cambios al instante |
| **tmpfs** | `--tmpfs /ruta` | Memoria RAM | Datos temporales que no deben guardarse |

### 4.2 Bind mount en desarrollo

```bash
docker run -d -p 3000:3000 -v "$(pwd)":/app mi-app
```

Lo que editas en tu carpeta se refleja dentro del contenedor al momento.

### 4.3 Solo lectura

```bash
docker run -v ./config:/app/config:ro mi-app
```

`:ro` impide que el contenedor modifique esos archivos.

### 4.4 Tipos de red

| Red | Descripción |
|---|---|
| **bridge** | Red virtual privada. Es la **por defecto** |
| **host** | El contenedor usa directamente la red de tu máquina (solo Linux) |
| **none** | Sin red |

### 4.5 Redes definidas por el usuario

Crear tu propia red (`docker network create`) tiene una ventaja clave: los contenedores se encuentran **por nombre** (DNS automático). En la red `bridge` por defecto no.

### 4.6 Puertos publicados vs. red interna

| Quién se conecta | Qué usa |
|---|---|
| Tú desde el navegador | Puerto publicado: `localhost:3000` |
| Otro contenedor de la misma red | Nombre del contenedor y puerto **interno**: `base:3306` |

La base de datos **no necesita** publicar puerto si solo la usan otros contenedores.

---

## 5. Ejemplos prácticos

### 5.1 Ejemplo básico: que los datos no se pierdan

```bash
docker run -d --name base -e MYSQL_ROOT_PASSWORD=secreto -v datos:/var/lib/mysql mysql:8

docker rm -f base

docker run -d --name base2 -e MYSQL_ROOT_PASSWORD=secreto -v datos:/var/lib/mysql mysql:8
```

El segundo contenedor encuentra los mismos datos del primero.

### 5.2 Ejemplo habitual: API + base de datos en la misma red

```bash
docker network create app-red

docker run -d --name db --network app-red \
  -e POSTGRES_PASSWORD=secreto \
  -v pgdata:/var/lib/postgresql/data \
  postgres:16

docker run -d --name api --network app-red \
  -p 3000:3000 \
  -e DB_HOST=db \
  mi-api
```

La API se conecta con `DB_HOST=db`. Esto mismo se simplifica mucho con [[05 - Docker Compose]].

---

## 6. Buenas prácticas

- **Volúmenes con nombre** para datos que importan (bases de datos).
- **Bind mounts solo en desarrollo.**
- **Crea una red por proyecto** en lugar de usar la por defecto.
- **No publiques puertos que no hagan falta**, sobre todo los de bases de datos.
- **Usa `:ro`** cuando el contenedor solo deba leer.
- **Haz copias de seguridad** de los volúmenes importantes.
- **Conecta por nombre de contenedor**, nunca por IP (cambian).

---

## 7. Diferencias importantes

### 7.1 Volumen vs. bind mount

| | Volumen | Bind mount |
|---|---|---|
| Quién lo gestiona | Docker | Tú (una carpeta tuya) |
| Portabilidad | Alta | Depende de tu equipo |
| Rendimiento | Bueno | Puede ser más lento (Windows/macOS) |
| Uso ideal | Producción y bases de datos | Desarrollo |

### 7.2 `-v` vs. `--mount`

| | `-v` | `--mount` |
|---|---|---|
| Escritura | Corta | Más larga y explícita |
| Si la carpeta no existe | La crea sin avisar | Da error |

```bash
docker run --mount type=volume,source=datos,target=/data mi-app
```

### 7.3 `EXPOSE` vs. `-p`

`EXPOSE` solo documenta. `-p` abre el puerto hacia tu máquina.

---

## 8. Casos especiales

### 8.1 Borrar un volumen borra los datos

`docker volume rm` o `docker compose down -v` eliminan los datos para siempre. Piénsalo antes.

### 8.2 Un volumen vacío se rellena con la imagen

Si montas un volumen **nuevo** en una carpeta que ya tiene contenido en la imagen, Docker lo copia al volumen la primera vez. Con un bind mount, en cambio, **tapa** lo que había.

### 8.3 `node_modules` y bind mounts

Si montas todo el proyecto (`-v ./:/app`), tu carpeta puede tapar el `node_modules` de la imagen. Solución habitual: un volumen anónimo extra solo para esa carpeta (`-v /app/node_modules`).

### 8.4 Permisos de archivos

En Linux, los archivos creados dentro del contenedor pueden pertenecer a `root` en tu carpeta. Se soluciona con `--user` o ajustando permisos.

### 8.5 Llegar a tu máquina desde un contenedor

Con Docker Desktop, `host.docker.internal` apunta a tu equipo.

### 8.6 Red `host`

Quita el aislamiento de red y los puertos con `-p` dejan de tener efecto. Solo funciona de forma completa en Linux.

---

## 9. Resumen

- Lo que se escribe dentro de un contenedor **se pierde** al borrarlo; para conservarlo, usa **volúmenes**.
- **Volumen** (gestionado por Docker) para datos; **bind mount** (carpeta tuya) para desarrollo.
- `-v nombre:/ruta` monta un volumen; `:ro` lo deja en solo lectura.
- Las **redes** permiten que los contenedores se comuniquen.
- En una red creada por ti, los contenedores se encuentran **por nombre**.
- Entre contenedores se usa el **puerto interno**; el `-p` es solo para acceder desde tu máquina.
- No publiques puertos innecesarios, sobre todo de bases de datos.
- Borrar un volumen elimina sus datos de forma definitiva.