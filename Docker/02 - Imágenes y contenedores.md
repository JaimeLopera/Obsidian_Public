# 02 - Imágenes y contenedores

> [!info] ¿Qué es?
> Las **imágenes** son las plantillas y los **contenedores** son esas plantillas en funcionamiento. Esta nota recoge los comandos para descargar, arrancar, parar, inspeccionar y borrar ambos.

---

## 1. Antes de empezar

Conviene haber leído [[DESARROLLO WEB/PHP/01 - Fundamentos]]. También ayuda saber qué es un puerto ([[01 - HTTP]]).

---

## 2. Concepto fundamental

**Ciclo de vida de un contenedor:**

```
imagen ──create──► creado ──start──► en ejecución ──stop──► parado ──rm──► borrado
```

- Una imagen se **descarga** (`pull`) o se **construye** (`build`, ver [[03 - Dockerfile]]).
- `docker run` hace dos cosas: **crea** y **arranca** un contenedor.
- Un contenedor parado sigue existiendo hasta que lo borras.
- Cuando el proceso principal del contenedor termina, el contenedor se para.

---

## 3. Sintaxis / estructura

### 3.1 Nombre de una imagen

```
[registro/][usuario/]nombre[:etiqueta]
```

Ejemplos: `nginx`, `node:20-alpine`, `mysql:8`. Si no pones etiqueta, usa `latest`.

### 3.2 Imágenes

```bash
docker pull node:20-alpine     # descargar
docker image ls                # listar las locales
docker image rm node:20-alpine # borrar
docker image prune             # borrar las que no se usan
```

### 3.3 Contenedores

```bash
docker run [opciones] imagen [comando]
docker ps                      # contenedores en ejecución
docker ps -a                   # todos, también los parados
docker stop mi-app             # parar
docker start mi-app            # arrancar uno ya creado
docker restart mi-app
docker rm mi-app               # borrar (debe estar parado)
docker rm -f mi-app            # forzar borrado
docker container prune         # borrar todos los parados
```

---

## 4. Elementos / propiedades / características

### 4.1 Opciones más usadas de `docker run`

| Opción | Qué hace |
|---|---|
| `-d` | Segundo plano (*detached*) |
| `-it` | Modo interactivo con terminal |
| `--name nombre` | Pone nombre al contenedor |
| `-p 8080:80` | Publica puerto: `puerto-tuyo:puerto-contenedor` |
| `-e CLAVE=valor` | Define una variable de entorno |
| `-v origen:destino` | Monta un volumen o carpeta ([[04 - Volúmenes y redes]]) |
| `--rm` | Borra el contenedor al terminar |
| `--network red` | Conecta a una red |
| `--restart unless-stopped` | Reinicia automáticamente |

### 4.2 Inspeccionar y depurar

| Comando | Para qué |
|---|---|
| `docker logs mi-app` | Ver lo que ha escrito la app |
| `docker logs -f mi-app` | Seguir los logs en directo |
| `docker exec -it mi-app sh` | Abrir una terminal **dentro** de un contenedor en marcha |
| `docker inspect mi-app` | Información completa en JSON |
| `docker stats` | Uso de CPU y memoria |
| `docker top mi-app` | Procesos del contenedor |
| `docker cp mi-app:/ruta ./local` | Copiar archivos entre contenedor y tu equipo |

### 4.3 Etiquetas (tags)

La etiqueta indica la versión: `node:20`, `node:20-alpine`. Las variantes `alpine` son muy pequeñas; las `slim` son un punto intermedio.

### 4.4 Publicar puertos

`-p 8080:80` significa: lo que llegue al puerto **8080 de tu máquina** va al puerto **80 del contenedor**. Sin `-p`, el contenedor no es accesible desde fuera.

---

## 5. Ejemplos prácticos

### 5.1 Ejemplo básico

```bash
docker run -d -p 8080:80 --name web nginx
docker ps
docker logs web
docker stop web
docker rm web
```

### 5.2 Ejemplo habitual: base de datos temporal y entrar a ella

```bash
docker run -d --name base -e MYSQL_ROOT_PASSWORD=secreto -p 3306:3306 mysql:8
docker exec -it base mysql -u root -p
```

Para una terminal suelta usando una imagen y borrarla al salir:

```bash
docker run -it --rm node:20-alpine sh
```

---

## 6. Buenas prácticas

- **Pon nombre** a tus contenedores con `--name`.
- **Usa etiquetas concretas** (`mysql:8`), no `latest`.
- **Usa `--rm`** en contenedores de prueba para no acumular basura.
- **Limpia de vez en cuando** con `docker system prune`.
- **Mira los logs primero** si algo falla: `docker logs`.
- **No guardes datos importantes dentro** del contenedor; se pierden al borrarlo.
- **No guardes contraseñas** en la línea de comandos de uso real; usa variables de entorno desde un archivo ([[05 - Docker Compose]]).

---

## 7. Diferencias importantes

### 7.1 `docker run` vs. `docker start`

| | `docker run` | `docker start` |
|---|---|---|
| Crea un contenedor nuevo | Sí | No |
| Necesita imagen | Sí | No, el contenedor ya existe |
| Resultado repetido | Un contenedor nuevo cada vez | Reutiliza el mismo |

### 7.2 `stop` vs. `kill` vs. `rm`

| Comando | Efecto |
|---|---|
| `stop` | Pide al proceso que termine y espera un poco |
| `kill` | Lo corta de inmediato |
| `rm` | Borra el contenedor (parado) |

### 7.3 `docker exec` vs. `docker attach`

`exec` abre un proceso **nuevo** dentro del contenedor (lo normal). `attach` se engancha al proceso **principal**; salir mal puede pararlo.

---

## 8. Casos especiales

### 8.1 El contenedor se para nada más arrancar

Pasa cuando el proceso principal termina enseguida (por ejemplo, un `sh` sin terminal). Usa `-it`, o mira `docker logs` para ver el motivo.

### 8.2 Puerto ocupado

Si otro programa ya usa el puerto, Docker da error. Cambia el puerto de la izquierda: `-p 8081:80`.

### 8.3 Nombre ya en uso

No puede haber dos contenedores con el mismo nombre, ni siquiera si uno está parado. Bórralo con `docker rm` o usa otro nombre.

### 8.4 `localhost` dentro de un contenedor

Dentro de un contenedor, `localhost` es **el propio contenedor**, no tu máquina. Para hablar con otros contenedores, usa su nombre en una red ([[04 - Volúmenes y redes]]).

### 8.5 Imágenes "colgadas"

Las imágenes sin nombre (`<none>`) son restos de construcciones antiguas. Se eliminan con `docker image prune`.

---

## 9. Resumen

- **Imagen** = plantilla; **contenedor** = ejecución de esa imagen.
- `docker run` = crear + arrancar; `docker start` solo arranca uno existente.
- Nombre de imagen: `registro/usuario/nombre:etiqueta`.
- Opciones clave: `-d`, `-it`, `--name`, `-p`, `-e`, `-v`, `--rm`.
- `-p 8080:80` = puerto de tu máquina : puerto del contenedor.
- Depurar: `logs`, `exec -it ... sh`, `inspect`, `stats`.
- Limpiar: `rm`, `image rm`, `container prune`, `image prune`, `system prune`.
- Los datos dentro de un contenedor se pierden al borrarlo.