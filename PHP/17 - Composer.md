# 17 - Composer

> [!info] ¿Qué es?
> **Composer** es el gestor de dependencias de PHP. Descarga y actualiza las **librerías** que usa tu proyecto y genera un **autoload** que carga tus clases automáticamente.

---

## 1. Antes de empezar

Necesitas PHP y la terminal (nota [[Terminal y Linux/00 - Índice|Terminal y Linux]]). Para entender el autoload, ayuda conocer namespaces (nota [[14 - POO|12]]).

Comprueba que está instalado:

```bash
composer --version
```

---

## 2. Concepto fundamental

Un proyecto moderno rara vez lo escribe todo: usa **paquetes** de otros (para enviar emails, leer `.env`, hacer pruebas...). Composer:

1. Lee qué paquetes necesitas (`composer.json`).
2. Los descarga en la carpeta `vendor/`.
3. Anota las versiones exactas instaladas (`composer.lock`).
4. Genera `vendor/autoload.php`, que carga todo.

Los paquetes se buscan en **Packagist** (packagist.org).

---

## 3. Sintaxis / estructura

### Crear el proyecto

```bash
composer init
```

Genera un `composer.json`.

### Instalar un paquete

```bash
composer require vlucas/phpdotenv
composer require --dev phpunit/phpunit
```

`--dev` es para paquetes que solo usas al desarrollar (tests, análisis).

### Usar el autoload

```php
require __DIR__ . "/vendor/autoload.php";

$dotenv = Dotenv\Dotenv::createImmutable(__DIR__);
$dotenv->load();

echo $_ENV["DB_HOST"];
```

### Fichero `composer.json`

```json
{
    "name": "jaime/mi-proyecto",
    "require": {
        "php": ">=8.2",
        "vlucas/phpdotenv": "^5.6"
    },
    "require-dev": {
        "phpunit/phpunit": "^11.0"
    },
    "autoload": {
        "psr-4": {
            "App\\": "src/"
        }
    }
}
```

---

## 4. Elementos / características

### Comandos principales

| Comando | Qué hace |
|---|---|
| `composer install` | Instala **exactamente** lo que dice `composer.lock` |
| `composer update` | Actualiza paquetes dentro de lo permitido y reescribe el `.lock` |
| `composer require paquete` | Añade un paquete |
| `composer remove paquete` | Quita un paquete |
| `composer dump-autoload` | Regenera el autoload |
| `composer show` | Lista paquetes instalados |
| `composer outdated` | Muestra paquetes con versión nueva |
| `composer audit` | Comprueba vulnerabilidades conocidas |

### Versiones

| Notación | Significado |
|---|---|
| `^5.6` | Cualquier `5.x` desde `5.6` (sin saltar a `6`) |
| `~5.6.2` | `5.6.x` desde `5.6.2` |
| `5.6.*` | Cualquier `5.6.x` |
| `>=8.2` | `8.2` o superior |

`^` es lo más habitual.

### `composer.lock`

Guarda las **versiones exactas** instaladas. Se sube a Git para que todo el equipo (y el servidor) use lo mismo.

### Autoload PSR-4

Con esta configuración:

```json
"autoload": { "psr-4": { "App\\": "src/" } }
```

La clase `App\Modelos\Usuario` debe estar en `src/Modelos/Usuario.php`:

```php
<?php
namespace App\Modelos;

class Usuario {}
```

Y en tu código:

```php
require __DIR__ . "/vendor/autoload.php";

use App\Modelos\Usuario;

$u = new Usuario();
```

Si añades o mueves clases, ejecuta `composer dump-autoload`.

### Scripts

```json
"scripts": {
    "test": "phpunit",
    "servir": "php -S localhost:8000 -t public"
}
```

```bash
composer test
composer servir
```

---

## 5. Ejemplos prácticos

### Ejemplo básico: instalar una librería

```bash
composer require nesbot/carbon
```

```php
require __DIR__ . "/vendor/autoload.php";

use Carbon\Carbon;

echo Carbon::now()->addDays(3)->format("d/m/Y");
```

### Ejemplo habitual: estructura de proyecto

```
mi-proyecto/
├── composer.json
├── composer.lock
├── .env
├── .gitignore
├── public/
│   └── index.php
├── src/
│   ├── Modelos/
│   └── Controladores/
└── vendor/
```

La carpeta pública (`public/`) es la única accesible desde el navegador.

---

## 6. Buenas prácticas

- Sube `composer.json` y `composer.lock` a Git.
- **No** subas `vendor/` ni `.env`: añádelos a `.gitignore`.
- En el servidor usa `composer install --no-dev --optimize-autoloader`.
- Usa `composer install` (no `update`) para reproducir el mismo entorno.
- Ejecuta `composer audit` de vez en cuando.
- Revisa que los paquetes estén mantenidos y sean fiables.
- Usa PSR-4 con namespaces para tus propias clases.

---

## 7. Diferencias importantes

| Comparación | Diferencia |
|---|---|
| `install` vs `update` | `install` respeta el `.lock`; `update` busca versiones nuevas |
| `require` vs `require-dev` | `require-dev` solo para desarrollo |
| Composer vs npm | Composer es para PHP (`vendor/`); npm es para JavaScript (`node_modules/`) |

---

## 8. Casos especiales

### Instalar Composer sin instalarlo en el sistema

Puedes usar el fichero `composer.phar`:

```bash
php composer.phar install
```

### Composer con Docker

```dockerfile
COPY --from=composer:2 /usr/bin/composer /usr/bin/composer
RUN composer install --no-dev --optimize-autoloader
```

### Paquetes privados

Se pueden definir repositorios propios en `composer.json` (`repositories`).

### Conflictos de versiones

Si dos paquetes necesitan versiones incompatibles, Composer lo indica y no instala. Puedes ver el motivo con:

```bash
composer why-not paquete versión
```

---

## 9. Resumen

- Composer instala librerías en `vendor/` y genera `vendor/autoload.php`.
- `composer.json` dice qué necesitas; `composer.lock` fija las versiones exactas.
- `require` añade, `install` instala lo del `.lock`, `update` actualiza.
- PSR-4 conecta namespaces con carpetas.
- No subas `vendor/` a Git.
- Ejecuta `composer audit` para revisar vulnerabilidades.