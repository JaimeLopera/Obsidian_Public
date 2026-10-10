# 19 - PHP moderno

> [!info] ¿Qué es?
> Desde PHP 7 y sobre todo PHP 8, el lenguaje ha cambiado mucho: tipos estrictos, menos código repetido, más seguridad. Esta nota resume **lo moderno** que conviene usar y **lo antiguo** que hay que evitar.

---

## 1. Antes de empezar

Debes dominar lo básico de PHP (notas 01 a 14). Comprueba tu versión:

```bash
php -v
```

> [!tip] Recomendación
> Usa una versión de PHP que **siga recibiendo soporte**. Consulta las versiones activas en php.net/supported-versions.php.

---

## 2. Concepto fundamental

El estilo moderno de PHP se basa en:

- **Tipos** en todo (parámetros, retornos, propiedades).
- **Menos código repetido** (promoción de propiedades, `match`, argumentos con nombre).
- **Clases inmutables y enums** para representar datos con claridad.
- **Composer y PSR-4** para organizar el proyecto.

---

## 3. Sintaxis / estructura

### Tipos estrictos

```php
<?php
declare(strict_types=1);
```

Va **al principio** del fichero. Evita conversiones automáticas peligrosas en llamadas a funciones.

### Tipos modernos

```php
function buscar(int|string $id, ?array $opciones = null): ?Usuario { ... }

function fallar(): never { throw new Exception("Error"); }
function guardar(): void { ... }
```

| Tipo | Significado |
|---|---|
| `?T` | `T` o `null` |
| `A\|B` | Unión de tipos |
| `A&B` | Intersección (objeto que cumple ambas interfaces) |
| `mixed` | Cualquier valor |
| `never` | La función nunca vuelve |
| `static` | Devuelve la misma clase que se está usando |
| `true`, `false`, `null` | Tipos independientes (PHP 8.2) |

### Promoción de propiedades en el constructor

```php
class Producto
{
    public function __construct(
        private string $nombre,
        private float $precio = 0.0,
    ) {}
}
```

### Propiedades `readonly` (8.1) y clases `readonly` (8.2)

```php
final readonly class Dinero
{
    public function __construct(
        public int $cantidad,
        public string $moneda,
    ) {}
}
```

Los objetos no se pueden modificar tras crearse (inmutables).

### Enums (8.1)

```php
enum Rol: string
{
    case Admin = "admin";
    case Usuario = "usuario";

    public function etiqueta(): string
    {
        return match ($this) {
            self::Admin => "Administrador",
            self::Usuario => "Usuario",
        };
    }
}

$r = Rol::from("admin");        // lanza error si no existe
$r = Rol::tryFrom("otro");      // null si no existe
echo $r?->etiqueta();
```

### `match`

```php
$texto = match ($estado) {
    "ok" => "Correcto",
    "error" => "Fallo",
    default => "Desconocido",
};
```

### Argumentos con nombre

```php
crearUsuario(nombre: "Ana", activo: true);
```

### Operador nullsafe

```php
$ciudad = $usuario?->direccion?->ciudad;
```

### Funciones flecha

```php
$dobles = array_map(fn(int $n): int => $n * 2, $numeros);
```

### Función como valor (first-class callable, 8.1)

```php
$f = strlen(...);
$g = $objeto->metodo(...);
```

### Atributos (8.0)

Metadatos en el código; los usan frameworks y herramientas.

```php
#[Route("/inicio")]
class InicioControlador {}
```

### `new` en inicializadores (8.1)

```php
class Servicio
{
    public function __construct(private Logger $logger = new Logger()) {}
}
```

---

## 4. Elementos / características

### Novedades útiles por versión

| Versión | Novedades destacadas |
|---|---|
| 7.4 | Propiedades tipadas, funciones flecha, `??=` |
| 8.0 | `match`, argumentos con nombre, promoción de propiedades, nullsafe, tipos unión, atributos, `str_contains` |
| 8.1 | Enums, `readonly`, `never`, first-class callable, `new` en inicializadores, intersección de tipos |
| 8.2 | Clases `readonly`, tipos `true`, `false`, `null` independientes |
| 8.3 | Constantes de clase tipadas, `#[\Override]`, `json_validate()` |
| 8.4 | Property hooks, visibilidad asimétrica, `new` sin paréntesis extra, `array_find`, `array_any`, `array_all` |
| 8.5 | Operador pipe `\|>`, `array_first()` y `array_last()`, `#[\NoDiscard]`, extensión de URI |

### Property hooks (8.4)

```php
class Usuario
{
    public string $nombre {
        set => trim($value);
    }

    public string $nombreCompleto {
        get => "{$this->nombre} {$this->apellido}";
    }
}
```

Permiten lógica en leer o escribir una propiedad **sin** métodos `get` y `set` aparte.

### Visibilidad asimétrica (8.4)

```php
class Cuenta
{
    public private(set) float $saldo = 0.0;   // se lee públicamente, solo se modifica dentro
}
```

### Operador pipe (8.5)

```php
$resultado = "  Hola Mundo  "
    |> trim(...)
    |> strtolower(...)
    |> ucfirst(...);
```

Encadena funciones de izquierda a derecha, en vez de anidarlas.

### Herramientas del ecosistema

| Herramienta | Para qué |
|---|---|
| PHPStan / Psalm | Análisis estático: encuentra errores sin ejecutar |
| PHP-CS-Fixer / PHP_CodeSniffer | Formato de código |
| PHPUnit / Pest | Pruebas |
| Rector | Moderniza código antiguo automáticamente |

### Estándares PSR

- **PSR-1 / PSR-12**: estilo de código.
- **PSR-4**: autoload.
- **PSR-3**: interfaz de logs.
- **PSR-7 / 15**: peticiones HTTP y middleware.

---

## 5. Ejemplos prácticos

### Ejemplo básico: clase moderna

```php
<?php
declare(strict_types=1);

final class Articulo
{
    public function __construct(
        public readonly string $titulo,
        public readonly Estado $estado = Estado::Borrador,
    ) {}
}

enum Estado: string
{
    case Borrador = "borrador";
    case Publicado = "publicado";
}
```

### Ejemplo habitual: antes y después

```php
// ❌ Estilo antiguo
function obtenerNombre($usuario) {
    if (isset($usuario) && isset($usuario->perfil) && isset($usuario->perfil->nombre)) {
        return $usuario->perfil->nombre;
    }
    return "Anónimo";
}

// ✅ Estilo moderno
function obtenerNombre(?Usuario $usuario): string
{
    return $usuario?->perfil?->nombre ?? "Anónimo";
}
```

---

## 6. Buenas prácticas

- Empieza cada fichero con `declare(strict_types=1);`.
- Tipa **todo**: parámetros, retornos y propiedades.
- Usa `readonly` y enums para datos que no cambian o que tienen valores fijos.
- Usa `match` en lugar de `switch` cuando devuelvas un valor.
- Pasa un analizador estático (PHPStan o Psalm) y un formateador.
- Sigue PSR-12 para el estilo y PSR-4 para el autoload.
- Actualiza PHP con regularidad.

---

## 7. Diferencias importantes

| Antiguo | Moderno |
|---|---|
| `array()` | `[]` |
| `strpos(...) !== false` | `str_contains(...)` |
| `switch` para devolver un valor | `match` |
| Propiedades sin tipo | Propiedades tipadas |
| Constantes con `define` en clases | `const` con tipo |
| Getters y setters para todo | Promoción de propiedades, `readonly`, hooks |

---

## 8. Casos especiales

### Funciones y características obsoletas o eliminadas

> [!warning] Obsoleto / legado
> No uses estas cosas. Se han eliminado o están desaconsejadas.

| Obsoleto | Sustituto actual |
|---|---|
| `mysql_*` | PDO o `mysqli` |
| `create_function()` | Funciones anónimas o flecha |
| `each()` | `foreach` |
| `split()` / `ereg()` | `explode()` / `preg_*` |
| `magic_quotes`, `register_globals` | Validación y escapado manual |
| `md5()` / `sha1()` para contraseñas | `password_hash()` |
| Etiquetas cortas `<?` | `<?php` |
| `utf8_encode()` / `utf8_decode()` (obsoletas en 8.2) | `mb_convert_encoding()` |
| Propiedades dinámicas (obsoletas en 8.2) | Declarar las propiedades en la clase |

### Propiedades dinámicas

```php
class A {}
$a = new A();
$a->x = 5;   // Obsoleto desde 8.2: declara la propiedad en la clase
```

### Compatibilidad entre versiones

En `composer.json` indica la versión mínima:

```json
"require": { "php": ">=8.2" }
```

### Rendimiento

PHP 8 incorpora JIT y OPcache. Activa **OPcache** en producción.

---

## 9. Resumen

- PHP moderno usa tipos estrictos, `match`, enums, `readonly`, promoción de propiedades y argumentos con nombre.
- PHP 8.4 y 8.5 añaden property hooks, visibilidad asimétrica, funciones de búsqueda en arrays y el operador pipe.
- Usa Composer, PSR-4 y herramientas como PHPStan y un formateador.
- Evita lo obsoleto: `mysql_*`, `md5` para contraseñas, propiedades dinámicas, etiquetas cortas.
- Mantén PHP actualizado.