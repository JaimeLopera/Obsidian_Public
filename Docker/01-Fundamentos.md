# 01 - Fundamentos

> [!info] ¿Qué es?
> **Docker** es una herramienta que ejecuta aplicaciones dentro de **contenedores**: entornos aislados y ligeros que llevan dentro todo lo necesario para funcionar. Resuelve el clásico "en mi ordenador funciona".

---

## 1. Antes de empezar

Conviene saber:

- Usar la terminal básica ([[Terminal y Linux/01 - Fundamentos de la terminal]]).
- Qué es un puerto y un servidor ([[01 - HTTP]]).

**Problema que resuelve:** cada proyecto necesita versiones concretas de programas (Node, PHP, MySQL...). Instalar todo eso a mano en cada ordenador es lento y suele dar conflictos. Con Docker lo defines una vez y se reproduce igual en cualquier sitio.

---

## 2. Concepto fundamental

Docker se basa en tres ideas:

| Concepto | Qué es | Comparación |
|---|---|---|
| **Imagen** | Plantilla de solo lectura con la app y todo lo que necesita | La receta o el molde |
| **Contenedor** | Una imagen **en ejecución** | El plato hecho o la pieza fabricada |
| **Registro** | Sitio donde se guardan y descargan imágenes (Docker Hub) | La tienda de recetas |

De una misma imagen puedes arrancar muchos contenedores independientes.

### Contenedor vs. máquina virtual

| | Máquina virtual | Contenedor |
|---|---|---|
| Qué incluye | Un sistema operativo completo | Solo la app y sus dependencias |
| Peso | Gigas | Megas |
| Arranque | Minutos | Segundos |
| Aislamiento | Total (hardware virtual) | Fuerte, pero comparte el núcleo del sistema |

Los contenedores comparten el **núcleo** del sistema anfitrión, por eso son mucho más ligeros.

---

## 3. Sintaxis / estructura

### 3.1 Instalación

- **Windows y macOS:** Docker Desktop.
- **Linux:** Docker Engine desde los repositorios oficiales.

Comprobar que funciona:

```bash
docker --version
docker run hello-world
```

### 3.2 Forma general de un comando

```bash
docker <área> <acción> [opciones] <objetivo>
```

Ejemplos: `docker image ls`, `docker container rm mi-app`.

---

## 4. Elementos / propiedades / características

### 4.1 Piezas de Docker

| Pieza | Función |
|---|---|
| **Docker Engine** | El programa que crea y ejecuta contenedores |
| **Docker Daemon** (`dockerd`) | Servicio en segundo plano que hace el trabajo |
| **Docker CLI** | Los comandos `docker ...` que escribes |
| **Docker Hub** | Registro público de imágenes |
| **Docker Desktop** | Aplicación con interfaz para Windows y macOS |

Tú escribes en la CLI, y esta le pide las cosas al daemon.

### 4.2 Qué consigue Docker

- **Portabilidad:** funciona igual en cualquier máquina.
- **Aislamiento:** cada contenedor va a lo suyo sin pisar a los demás.
- **Reproducibilidad:** el entorno queda descrito en archivos.
- **Rapidez:** arranca en segundos y se borra sin dejar rastro.

### 4.3 Las capas

Una imagen se construye en **capas** apiladas. Cada instrucción del Dockerfile añade una capa. Docker las guarda en caché y las reutiliza, lo que acelera mucho las construcciones.

---

## 5. Ejemplos prácticos

### 5.1 Ejemplo básico

```bash
docker run hello-world
```

Docker descarga la imagen `hello-world` (si no la tiene), crea un contenedor, imprime un mensaje y termina.

### 5.2 Ejemplo habitual: servidor web en un comando

```bash
docker run -d -p 8080:80 --name mi-web nginx
```

Abre `http://localhost:8080` y verás la página de Nginx. Para pararlo y borrarlo:

```bash
docker stop mi-web
docker rm mi-web
```

---

## 6. Buenas prácticas

- **Un contenedor, un proceso principal:** no metas web, base de datos y todo junto.
- **Los contenedores son desechables:** deben poder borrarse y recrearse sin perder nada importante.
- **Lo importante se guarda fuera** del contenedor ([[04 - Volúmenes y redes]]).
- **Usa imágenes oficiales** y con versión concreta, no solo `latest`.
- **Describe todo en archivos** (Dockerfile, Compose) en lugar de repetir comandos a mano.

---

## 7. Diferencias importantes

### 7.1 Imagen vs. contenedor

| | Imagen | Contenedor |
|---|---|---|
| Estado | Estática, no cambia | Viva, puede cambiar |
| Se puede ejecutar | No directamente | Sí, es la ejecución |
| Cantidad | Una | Tantos como quieras de la misma imagen |

### 7.2 Docker vs. instalar en local

| | Instalar en local | Docker |
|---|---|---|
| Versiones | Conflictos entre proyectos | Cada proyecto con las suyas |
| Limpieza | Quedan restos | Se borra todo limpio |
| Equipo | "Cada uno tiene algo distinto" | Todos el mismo entorno |

---

## 8. Casos especiales

### 8.1 Docker en Windows

Docker Desktop usa **WSL 2** (subsistema de Linux) por debajo. Los contenedores siguen siendo de Linux.

### 8.2 Arquitectura del procesador

Una imagen se construye para una arquitectura (`amd64`, `arm64`). En un Mac con chip Apple puede hacer falta indicarla con `--platform` si la imagen solo existe para otra.

### 8.3 Permisos en Linux

Si tu usuario no está en el grupo `docker`, tendrás que usar `sudo` en cada comando.

### 8.4 Los contenedores no son máquinas seguras por sí solos

Aíslan, pero no hacen la app segura. Evita ejecutar como administrador dentro del contenedor y no metas secretos en las imágenes.

---

## 9. Resumen

- **Docker** ejecuta aplicaciones en **contenedores** aislados y portables.
- **Imagen** = plantilla de solo lectura; **contenedor** = imagen en ejecución.
- **Docker Hub** es el registro público de imágenes.
- Los contenedores son más ligeros que las máquinas virtuales porque comparten el núcleo del sistema.
- Las imágenes se construyen en **capas** que se cachean.
- Los contenedores deben ser **desechables**; los datos importantes van fuera.
- Comprueba la instalación con `docker --version` y `docker run hello-world`.