# 14 - POO

> [!info] ¿Qué es?
> La **Programación Orientada a Objetos (POO)** organiza el código en **clases** (plantillas) y **objetos** (cosas creadas a partir de esas plantillas). Cada objeto junta sus **datos** y las **acciones** que puede hacer.

---

## 1. Antes de empezar

Debes dominar variables, funciones y arrays (notas [[PHP/02 - Variables y tipos|02]], [[PHP/06 - Funciones|06]] y [[PHP/07 - Arrays|07]]).

---

## 2. Concepto fundamental

- **Clase**: el molde. Define qué datos (**propiedades**) y qué acciones (**métodos**) tendrán los objetos.
- **Objeto** (o instancia): una copia creada del molde, con sus propios datos.

Ejemplo mental: la clase `Coche` es el plano; cada coche real es un objeto.

### Los cuatro pilares

| Pilar | Idea |
|---|---|
| **Encapsulamiento** | Ocultar los datos internos y ofrecer solo lo necesario (propiedades `private` y métodos públicos) |
| **Abstracción** | Mostrar *qué* hace algo sin enseñar *cómo* (interfaces y clases abstractas) |
| **Herencia** | Una clase reutiliza y amplía a otra |
| **Polimorfismo** | Objetos distintos responden al mismo método a su manera |

---

## 3. Sintaxis / estructura

```php
class Usuario
{
    private array $roles = [];           // propiedad con valor por defecto

    public function __construct(
        private string $nombre,
        private string $email,
    ) {}

    public function getNombre(): string
    {
        return $this->nombre;
    }

    public function saludar(): string
    {
        return "Hola, soy {$this->nombre}";
    }
}

$u = new Usuario("Ana", "ana@mail.com");
echo $u->saludar();
```

- `class` define la clase; `new` crea un objeto.
- `$this` es "este objeto"; `->` accede a sus propiedades y métodos.
- `__construct` se ejecuta al crear el objeto.
- Poner la **visibilidad** en los parámetros del constructor (`private string $nombre`) se llama *promoción de propiedades*: crea la propiedad y le asigna el valor automáticamente.
- Una propiedad con tipo y **sin valor** está "no inicializada": leerla antes de asignarla lanza un `Error`.
- Los valores por defecto de las propiedades deben ser constantes (nada de llamar a funciones).

---

## 4. Elementos / características

### Visibilidad

| Modificador | Se puede usar desde... |
|---|---|
| `public` | Cualquier sitio |
| `protected` | La clase y sus **hijas** |
| `private` | Solo dentro de **esa** clase |

> [!tip] Encapsulamiento
> Haz las propiedades `private` o `protected` y accede a ellas con métodos. Así controlas qué valores se aceptan.

### Propiedades de solo lectura (`readonly`)

```php
class Punto
{
    public function __construct(
        public readonly int $x,
        public readonly int $y,
    ) {}
}

$p = new Punto(1, 2);
$p->x = 5;   // Error: no se puede modificar una propiedad readonly
```

- Se asigna **una sola vez** (al construir) y debe tener tipo.
- Desde PHP 8.2 se puede declarar toda la clase como `readonly`.
- Para "cambiar" un objeto inmutable, se crea uno nuevo:

```php
public function conY(int $y): static
{
    return new static($this->x, $y);
}
```

### Constantes de clase

```php
class Config
{
    public const VERSION = "1.0";
    private const CLAVE  = "abc";
    final public const MODO = "prod";     // las hijas no pueden cambiarla (PHP 8.1)

    public function version(): string
    {
        return self::VERSION;
    }
}

echo Config::VERSION;
```

### Miembros estáticos

Pertenecen a la **clase**, no a cada objeto. Se usan con `::`.

```php
class Contador
{
    private static int $total = 0;

    public static function incrementar(): int
    {
        return ++self::$total;
    }
}

Contador::incrementar();
```

- Un método estático **no puede usar `$this`**.
- `self::` se refiere a la clase donde está escrito el código.
- `static::` se refiere a la clase que se está usando realmente (*late static binding*):

```php
class Modelo
{
    public static function crear(): static
    {
        return new static();     // crea la clase hija correcta
    }
}

class Producto extends Modelo {}

$p = Producto::crear();          // es un objeto Producto
```

Los métodos estáticos que crean objetos se llaman *métodos de fábrica* (`Usuario::desdeArray(...)`).

### Herencia

```php
class Animal
{
    public function __construct(protected string $nombre) {}

    public function hablar(): string
    {
        return "...";
    }
}

class Perro extends Animal
{
    public function __construct(string $nombre, private string $raza)
    {
        parent::__construct($nombre);
    }

    #[\Override]                       // PHP 8.3: error si no existe en el padre
    public function hablar(): string
    {
        return "{$this->nombre} dice guau";
    }
}
```

- Una clase solo puede tener **un** padre.
- `parent::metodo()` llama a la versión del padre.
- La hija **no puede reducir** la visibilidad ni cambiar los tipos de forma incompatible.
- `final` en una clase impide heredar de ella; en un método, impide sobrescribirlo.

### Clases abstractas

```php
abstract class Figura
{
    public function __construct(protected string $nombre) {}

    abstract public function area(): float;      // sin cuerpo: las hijas deben implementarlo

    public function describir(): string
    {
        return sprintf("%s: %.2f", $this->nombre, $this->area());
    }
}

class Circulo extends Figura
{
    public function __construct(private float $radio)
    {
        parent::__construct("Círculo");
    }

    public function area(): float
    {
        return M_PI * $this->radio ** 2;
    }
}
```

No se pueden instanciar (`new Figura()` da error). Pueden mezclar métodos con código y métodos abstractos.

### Interfaces

Una interfaz es un **contrato**: lista de métodos públicos que una clase debe tener.

```php
interface Notificable
{
    public const CANAL = "general";

    public function enviar(string $mensaje): void;
}

class Email implements Notificable
{
    public function enviar(string $mensaje): void
    {
        // ...
    }
}
```

- Una clase puede implementar **varias** interfaces (`implements A, B`).
- Una interfaz puede extender varias (`interface C extends A, B`).
- No contienen código ni propiedades normales.

**Interfaces integradas importantes:**

| Interfaz | Para qué |
|---|---|
| `Countable` | Que `count($objeto)` funcione (`count(): int`) |
| `IteratorAggregate` | Que se pueda recorrer con `foreach` (`getIterator(): Traversable`) |
| `Iterator` | Recorrido con control total (`current`, `key`, `next`, `rewind`, `valid`) |
| `ArrayAccess` | Usar el objeto como array (`$o["x"]`): `offsetExists`, `offsetGet`, `offsetSet`, `offsetUnset` |
| `JsonSerializable` | Controlar cómo se convierte en JSON (nota [[PHP/13 - JSON y APIs|13]]) |
| `Stringable` | Objetos convertibles en texto (se añade sola si hay `__toString`) |

```php
class Carrito implements Countable, IteratorAggregate
{
    private array $items = [];

    public function agregar(string $producto): void
    {
        $this->items[] = $producto;
    }

    public function count(): int
    {
        return count($this->items);
    }

    public function getIterator(): Traversable
    {
        return new ArrayIterator($this->items);
    }
}

$c = new Carrito();
$c->agregar("Teclado");
echo count($c);            // 1
foreach ($c as $item) { }  // recorre los productos
```

### Traits

Fragmentos de código que se **reutilizan** en varias clases (que no están relacionadas por herencia).

```php
trait Registra
{
    private array $log = [];

    public function registrar(string $mensaje): void
    {
        $this->log[] = $mensaje;
    }
}

class Servicio
{
    use Registra;
}
```

- Se pueden usar varios (`use A, B;`).
- Si dos traits tienen un método con el mismo nombre, se resuelve así:

```php
class Mixto
{
    use A, B {
        A::hola insteadof B;     // usa el de A
        B::hola as holaDeB;      // y conserva el de B con otro nombre
    }
}
```

- Un trait no se puede instanciar ni tiene tipo (no sirve para `instanceof`).

### Métodos mágicos

| Método | Cuándo se ejecuta |
|---|---|
| `__construct()` | Al crear el objeto |
| `__destruct()` | Cuando el objeto deja de usarse o termina el script |
| `__toString()` | Al convertir el objeto en texto |
| `__get($n)` / `__set($n, $v)` | Al leer / escribir una propiedad que no existe o no es accesible |
| `__isset($n)` / `__unset($n)` | Al usar `isset` / `unset` sobre esa propiedad |
| `__call($n, $args)` / `__callStatic(...)` | Al llamar a un método que no existe |
| `__invoke()` | Al usar el objeto como si fuera una función |
| `__clone()` | Después de clonar el objeto |

```php
class Direccion
{
    public function __construct(private string $calle, private string $ciudad) {}

    public function __toString(): string
    {
        return "{$this->calle}, {$this->ciudad}";
    }
}

echo new Direccion("Gran Vía 1", "Madrid");
```

```php
class Ajustes
{
    private array $datos = [];

    public function __set(string $clave, mixed $valor): void
    {
        $this->datos[$clave] = $valor;
    }

    public function __get(string $clave): mixed
    {
        return $this->datos[$clave] ?? null;
    }

    public function __isset(string $clave): bool
    {
        return isset($this->datos[$clave]);
    }
}

$a = new Ajustes();
$a->tema = "oscuro";      // llama a __set
echo $a->tema;            // llama a __get
```

```php
class Doblar
{
    public function __invoke(int $n): int
    {
        return $n * 2;
    }
}

$f = new Doblar();
echo $f(4);                                   // 8
$resultado = array_map(new Doblar(), [1, 2]); // [2, 4]
```

> [!warning] Úsalos con moderación
> `__get`, `__set` y `__call` ocultan errores (un nombre mal escrito deja de dar fallo) y hacen el código más difícil de entender y de analizar. Úsalos solo cuando haya un motivo claro.

### Clonar objetos

```php
$copia = clone $original;
```

`clone` hace una copia **superficial**: si el objeto contiene otros objetos, la copia comparte los mismos. Para una copia profunda:

```php
public function __clone()
{
    $this->direccion = clone $this->direccion;
}
```

### Namespaces y `use`

Los namespaces organizan las clases y evitan choques de nombres.

```php
namespace App\Modelos;

class Usuario {}
```

```php
use App\Modelos\Usuario;
use App\Servicios\Correo as ServicioCorreo;     // con alias
use App\{Controladores\Inicio, Controladores\Panel};   // agrupar

$u = new Usuario();
echo Usuario::class;                  // "App\Modelos\Usuario"
echo \strlen("hola");                 // la barra inicial indica el espacio global
```

### Autoload

Con Composer (nota [[PHP/17 - Composer|17]]) el autoload ya funciona. Sin Composer, puedes registrar uno propio:

```php
spl_autoload_register(function (string $clase): void {
    $prefijo = "App\\";

    if (!str_starts_with($clase, $prefijo)) {
        return;
    }

    $ruta = __DIR__ . "/src/" . str_replace("\\", "/", substr($clase, strlen($prefijo))) . ".php";

    if (is_file($ruta)) {
        require $ruta;
    }
});
```

La clase `App\Modelos\Usuario` se busca en `src/Modelos/Usuario.php`.

### Enums (PHP 8.1)

Un enum es un conjunto cerrado de valores posibles.

```php
enum Estado: string
{
    case Activo   = "activo";
    case Inactivo = "inactivo";

    public function etiqueta(): string
    {
        return match ($this) {
            self::Activo   => "Activo",
            self::Inactivo => "Inactivo",
        };
    }
}

$e = Estado::from("activo");          // lanza error si el valor no existe
$e = Estado::tryFrom("otro");         // null si no existe
echo Estado::Activo->value;           // "activo"
echo Estado::Activo->name;            // "Activo"
Estado::cases();                      // array con todos los casos
```

- Un enum "puro" no tiene valores (`enum Palo { case Corazones; case Picas; }`).
- Puede tener métodos, constantes y implementar interfaces, pero **no propiedades** de instancia.
- No se puede instanciar con `new`.

### Clases anónimas

```php
$obj = new class {
    public function hola(): string { return "hola"; }
};
```

### Funciones útiles sobre clases y objetos

| Función | Qué hace |
|---|---|
| `$obj instanceof Clase` | ¿Es de esa clase (o hija, o implementa la interfaz)? |
| `$obj::class` / `get_class($obj)` | Nombre de la clase |
| `get_object_vars($obj)` | Propiedades accesibles desde donde llamas |
| `method_exists($obj, "m")` | ¿Tiene ese método? |
| `property_exists($obj, "p")` | ¿Tiene esa propiedad? |
| `class_exists("Nombre")` | ¿Existe la clase? |
| `spl_object_id($obj)` | Identificador único del objeto |

### Tipos con clases

`self`, `static`, `parent`, `?Clase`, `Clase|OtraClase`, `object`, `iterable`, `mixed`.

---

## 5. Ejemplos prácticos

### Ejemplo básico

```php
class Producto
{
    public function __construct(
        private string $nombre,
        private float $precio,
    ) {}

    public function precioConIva(float $iva = 0.21): float
    {
        return round($this->precio * (1 + $iva), 2);
    }
}

$p = new Producto("Teclado", 30);
echo $p->precioConIva();   // 36.3
```

### Ejemplo habitual: interfaz + inyección de dependencias

```php
interface Pago
{
    public function pagar(float $importe): bool;
}

class PagoTarjeta implements Pago
{
    public function pagar(float $importe): bool { return true; }
}

class PagoPaypal implements Pago
{
    public function pagar(float $importe): bool { return true; }
}

class Tienda
{
    public function __construct(private Pago $metodoDePago) {}   // inyección de dependencias

    public function cobrar(float $importe): bool
    {
        return $this->metodoDePago->pagar($importe);
    }
}

$tienda = new Tienda(new PagoTarjeta());
```

`Tienda` funciona con cualquier método de pago: no depende de uno concreto.

### Ejemplo completo: objeto de valor inmutable con enum

```php
enum Moneda: string
{
    case EUR = "EUR";
    case USD = "USD";
}

final readonly class Dinero
{
    public function __construct(
        public int $centimos,
        public Moneda $moneda,
    ) {}

    public static function euros(float $cantidad): self
    {
        return new self((int) round($cantidad * 100), Moneda::EUR);
    }

    public function sumar(Dinero $otro): self
    {
        if ($this->moneda !== $otro->moneda) {
            throw new InvalidArgumentException("Monedas distintas");
        }

        return new self($this->centimos + $otro->centimos, $this->moneda);
    }

    public function __toString(): string
    {
        return number_format($this->centimos / 100, 2, ",", ".") . " " . $this->moneda->value;
    }
}

echo Dinero::euros(19.99)->sumar(Dinero::euros(0.01));   // 20,00 EUR
```

---

## 6. Buenas prácticas

- Una clase = una responsabilidad. Un fichero por clase, con el mismo nombre.
- Nombres: clases en `PascalCase`; métodos y propiedades en `camelCase`.
- Propiedades `private` o `protected`; métodos públicos solo para lo necesario.
- Declara tipos en propiedades, parámetros y retornos.
- Usa `readonly` para datos que no deben cambiar.
- Recibe las dependencias por el **constructor** (no las crees dentro con `new`).
- Programa contra **interfaces** cuando varios objetos puedan intercambiarse.
- Prefiere **composición** (usar otros objetos) a herencia profunda.
- Evita los métodos mágicos salvo que tengan un motivo claro.
- Valida en el constructor para que no existan objetos en estado inválido.

### Principios SOLID

| Letra | Principio | En una frase |
|---|---|---|
| **S** | Responsabilidad única | Una clase, un motivo para cambiar |
| **O** | Abierto/cerrado | Se amplía añadiendo código, no modificando el existente |
| **L** | Sustitución de Liskov | Una hija debe poder usarse donde se espera la clase padre |
| **I** | Segregación de interfaces | Mejor varias interfaces pequeñas que una enorme |
| **D** | Inversión de dependencias | Depende de interfaces, no de clases concretas |

---

## 7. Diferencias importantes

| Comparación | Diferencia |
|---|---|
| Clase abstracta vs interfaz | La abstracta puede tener código y propiedades; la interfaz solo define el contrato |
| Herencia vs trait | La herencia expresa "es un"; el trait solo reutiliza código |
| Herencia vs composición | Herencia: "es un"; composición: "tiene un" (suele ser más flexible) |
| `self::` vs `static::` | `self` es la clase donde se escribió; `static` es la clase que realmente se usa |
| `==` vs `===` en objetos | `==` compara propiedades; `===` comprueba que sea **el mismo** objeto |
| Método normal vs estático | El normal necesita un objeto (`$this`); el estático no |

---

## 8. Casos especiales

### Los objetos se pasan por "identificador"

```php
$a = new Usuario("Ana", "ana@mail.com");
$b = $a;          // $b apunta al MISMO objeto
$c = clone $a;    // copia independiente
```

Si pasas un objeto a una función y lo modifica, el cambio se ve fuera.

### Constructor privado

```php
class Conexion
{
    private function __construct() {}

    public static function crear(): self
    {
        return new self();
    }
}
```

Sirve para obligar a usar un método de fábrica (o, menos recomendable, el patrón *singleton*).

### Propiedades estáticas y herencia

Las propiedades estáticas se **comparten** con las clases hijas, salvo que la hija las vuelva a declarar.

### Propiedades dinámicas

Crear una propiedad que no está declarada (`$obj->nueva = 1`) está **obsoleto desde PHP 8.2**. Declara siempre las propiedades.

### Destructor

`__destruct` se ejecuta cuando ya no hay referencias o termina el script. No dependas de él para cerrar recursos importantes: ciérralos explícitamente.

### Serialización

`serialize` y `unserialize` convierten objetos en texto y viceversa. **Nunca** uses `unserialize` con datos del usuario (nota [[PHP/18 - Seguridad|18]]); usa JSON.

### Novedades de PHP 8.4

Los *property hooks* y la visibilidad asimétrica (`public private(set)`) simplifican los getters y setters. Están explicados en la nota [[PHP/19 - PHP moderno|19]].

### Excepciones en clases

Las clases suelen lanzar excepciones propias para sus errores (nota [[PHP/15 - Excepciones|15]]).

---

## 9. Resumen

- Una **clase** es un molde; un **objeto** se crea con `new`. `$this` es el objeto actual.
- Visibilidad: `public`, `protected`, `private`. Usa la promoción de propiedades y `readonly`.
- `static` y `const` pertenecen a la clase y se usan con `::`.
- Herencia con `extends`; contratos con `interface`; reutilización de código con `trait`; piezas fijas con `enum`.
- Las interfaces integradas (`Countable`, `IteratorAggregate`, `JsonSerializable`...) integran tus objetos con el lenguaje.
- Los métodos mágicos se usan con moderación.
- Namespaces y autoload ordenan el proyecto; Composer lo hace por ti.
- Los objetos se pasan por identificador; usa `clone` para copiar.
- Dependencias por constructor, interfaces y composición dan código flexible.