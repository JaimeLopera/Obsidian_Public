# Variables y funciones

> [!info] ¿Qué es?
> Las **variables CSS** (llamadas *propiedades personalizadas*) permiten **guardar un valor una vez** (un color, un tamaño…) y **reutilizarlo** en muchos sitios. Las **funciones** de CSS (`calc()`, `var()`, `clamp()`, `min()`, `max()`…) **calculan o construyen valores** en el momento. Juntas hacen el CSS más ordenado, fácil de cambiar y adaptable.

---

## 1. Antes de empezar

Para entender esta nota conviene saber:

- Cómo se escribe una regla CSS y qué es un selector (ver [[01 - Fundamentos]] y [[02 - Selectores]]).
- Las unidades de CSS (ver [[04 - Unidades y valores]]).
- Que muchas propiedades **se heredan** de padres a hijos.

HTML que usaremos en los ejemplos:

```html
<body>
  <header class="cabecera">
    <h1 class="titulo">Mi web</h1>
  </header>

  <main>
    <article class="tarjeta">
      <h2>Título</h2>
      <p>Texto de la tarjeta.</p>
      <a href="#" class="boton">Leer más</a>
    </article>
  </main>
</body>
```

---

## 2. Concepto fundamental

### Variables

Una variable CSS tiene **dos pasos**:

1. **Declarar**: darle un nombre y un valor, con dos guiones delante: `--color-principal: crimson;`
2. **Usar**: llamarla con la función `var()`: `color: var(--color-principal);`

```css
:root {
  --color-principal: crimson;
}

.boton {
  background: var(--color-principal);
}
```

Si mañana quieres otro color, **cambias una sola línea** y se actualiza en toda la web.

### Funciones

Una función CSS es un valor que se escribe con **nombre y paréntesis**, y devuelve **otro valor** calculado: `calc(100% - 20px)`, `var(--x)`, `rgb(0 0 0 / 50%)`…

> [!tip] Idea clave
> A diferencia de las variables de Sass o similares, las variables CSS **viven en el navegador**: pueden cambiar mientras la página está abierta (por ejemplo, con JavaScript o al cambiar a modo oscuro) y respetan la herencia.

---

## 3. Sintaxis / estructura

### Declarar una variable

```css
selector {
  --nombre: valor;
}
```

- El nombre **empieza siempre por dos guiones** `--`.
- Distingue **mayúsculas y minúsculas**: `--Color` y `--color` son distintas.
- El valor puede ser casi cualquier cosa: color, medida, texto, número, o varias cosas juntas.

### Usar una variable

```css
selector {
  propiedad: var(--nombre);
  propiedad: var(--nombre, valor-de-respaldo);   /* con valor alternativo */
}
```

---

## 4. Elementos / propiedades / características

### 4.1 Dónde declarar variables: el ámbito (*scope*)

Una variable solo está disponible en el elemento donde se declara **y en todos sus descendientes** (por herencia).

#### Variables globales con `:root`

`:root` es el elemento raíz (`<html>`). Lo que declares ahí está disponible **en toda la página**.

```css
:root {
  --color-principal: #e11d48;
  --color-texto: #222;
  --espaciado: 1rem;
  --radio: 8px;
}
```

#### Variables locales

Se declaran en un componente concreto y **solo valen dentro de él**:

```css
.tarjeta {
  --relleno: 1.5rem;
  padding: var(--relleno);
}

.tarjeta--grande {
  --relleno: 3rem;          /* solo cambiamos la variable */
}
```

#### Reasignar una variable más abajo

Un elemento puede **sobrescribir** una variable para sus hijos:

```css
:root {
  --color-fondo: white;
}

.oscuro {
  --color-fondo: #111;      /* dentro de .oscuro, vale #111 */
}

.caja {
  background: var(--color-fondo);
}
```

La misma `.caja` se verá blanca o negra según dónde esté.

### 4.2 `var()` y valores de respaldo

```css
.boton {
  background: var(--color-boton, crimson);   /* si no existe --color-boton, usa crimson */
  padding: var(--relleno, 1rem 2rem);
}
```

El respaldo solo se usa si la variable **no está definida**. Se pueden anidar:

```css
color: var(--color-enlace, var(--color-principal, blue));
```

### 4.3 Qué se puede guardar en una variable

```css
:root {
  --color: #e11d48;                       /* un color */
  --ancho-max: 1100px;                    /* una medida */
  --sombra: 0 4px 12px rgb(0 0 0 / 0.15); /* un valor compuesto */
  --fuente: "Inter", system-ui, sans-serif;
  --cantidad: 3;                          /* un número */
  --texto: "Hola";                        /* una cadena (para content) */
}
```

Usándolas:

```css
.tarjeta {
  box-shadow: var(--sombra);
  max-width: var(--ancho-max);
  font-family: var(--fuente);
}

.etiqueta::after {
  content: var(--texto);
}

.cuadricula {
  grid-template-columns: repeat(var(--cantidad), 1fr);
}
```

> [!warning]
> Una variable **no puede usarse como parte del nombre de una propiedad** ni dentro de una media query de forma directa (`@media (min-width: var(--ancho))` no funciona).

### 4.4 Variables con `calc()`

Las variables guardan valores, y `calc()` los combina:

```css
:root {
  --espacio: 0.5rem;
}

.caja {
  padding: calc(var(--espacio) * 2);       /* 1rem */
  margin-bottom: calc(var(--espacio) * 4); /* 2rem */
}
```

Esto permite crear una **escala de espaciado** coherente.

### 4.5 Temas (modo claro y oscuro)

Las variables son perfectas para temas: se **redefinen** y todo cambia.

```css
:root {
  --fondo: #ffffff;
  --texto: #222222;
  --tarjeta: #f4f4f4;
}

@media (prefers-color-scheme: dark) {
  :root {
    --fondo: #121212;
    --texto: #eeeeee;
    --tarjeta: #1e1e1e;
  }
}

/* O con un atributo en el HTML: <html data-tema="oscuro"> */
[data-tema="oscuro"] {
  --fondo: #121212;
  --texto: #eeeeee;
  --tarjeta: #1e1e1e;
}

body {
  background: var(--fondo);
  color: var(--texto);
}

.tarjeta {
  background: var(--tarjeta);
}
```

### 4.6 Cambiar variables con JavaScript

```js
// Leer una variable
const valor = getComputedStyle(document.documentElement)
  .getPropertyValue("--color-principal");

// Cambiar una variable (afecta a todo lo que la use)
document.documentElement.style.setProperty("--color-principal", "royalblue");

// Cambiarla solo en un elemento
document.querySelector(".tarjeta").style.setProperty("--relleno", "3rem");
```

### 4.7 `@property`: variables con tipo

Con `@property` se declara una variable con un **tipo**, un **valor inicial** y si se hereda. Su gran ventaja es que así **se pueden animar**.

```css
@property --angulo {
  syntax: "<angle>";
  inherits: false;
  initial-value: 0deg;
}

.rueda {
  background: conic-gradient(from var(--angulo), red, yellow, red);
  animation: girar 3s linear infinite;
}

@keyframes girar {
  to { --angulo: 360deg; }
}
```

### 4.8 Funciones matemáticas

#### `calc()`

Mezcla unidades distintas con las cuatro operaciones.

```css
.barra {
  width: calc(100% - 250px);
  height: calc(100dvh - var(--alto-cabecera));
  padding: calc(1rem + 2vw);
}
```

> [!note]
> Dentro de `calc()`, `+` y `-` **necesitan espacios** a ambos lados. En `*` y `/`, al menos uno de los lados debe ser un número sin unidad.

#### `min()`, `max()` y `clamp()`

| Función | Qué hace |
|---|---|
| `min(a, b, ...)` | Devuelve el valor **más pequeño** (pone un techo) |
| `max(a, b, ...)` | Devuelve el valor **más grande** (pone un suelo) |
| `clamp(mínimo, ideal, máximo)` | Usa el ideal, pero **sin salirse** de los límites |

```css
.contenedor {
  width: min(90%, 1100px);
}

h1 {
  font-size: clamp(2rem, 5vw, 4rem);
}

.imagen {
  height: max(200px, 30vh);
}
```

#### Funciones matemáticas adicionales

| Función | Para qué sirve |
|---|---|
| `round()` | Redondea un valor a un múltiplo |
| `mod()` / `rem()` | Resto de una división |
| `abs()` / `sign()` | Valor absoluto / signo |
| `sin()`, `cos()`, `tan()` | Trigonometría |
| `pow()`, `sqrt()`, `hypot()` | Potencias y raíces |

(Son recientes: revisa la compatibilidad antes de usarlas.)

### 4.9 Funciones de color

```css
.caja {
  color: rgb(30 58 138);
  background: hsl(220 70% 30% / 0.8);
  border-color: oklch(60% 0.2 250);
  outline-color: color-mix(in srgb, crimson 70%, white);   /* mezcla de colores */
}
```

`color-mix()` es muy útil junto con variables para crear variantes:

```css
:root {
  --principal: #e11d48;
  --principal-claro: color-mix(in srgb, var(--principal) 20%, white);
  --principal-oscuro: color-mix(in srgb, var(--principal) 80%, black);
}
```

### 4.10 Otras funciones útiles

| Función | Para qué sirve | Ejemplo |
|---|---|---|
| `url()` | Apuntar a un archivo | `background: url("img/fondo.jpg")` |
| `attr()` | Leer un atributo HTML (sobre todo en `content`) | `content: attr(data-etiqueta)` |
| `repeat()` | Repetir pistas en Grid | `repeat(3, 1fr)` |
| `minmax()` | Rango de tamaño en Grid | `minmax(200px, 1fr)` |
| `linear-gradient()` y similares | Degradados | `linear-gradient(90deg, red, blue)` |
| `translate()`, `rotate()`, `scale()` | Transformaciones | `transform: scale(1.1)` |
| `cubic-bezier()`, `steps()` | Curvas de animación | `transition-timing-function: steps(4)` |
| `env()` | Valores del dispositivo (zonas seguras) | `padding-bottom: env(safe-area-inset-bottom)` |
| `counter()` | Contadores automáticos | `content: counter(paso)` |
| `light-dark()` | Un color para modo claro y otro para oscuro | `color: light-dark(#222, #eee)` |

```css
:root {
  color-scheme: light dark;
}

body {
  background: light-dark(#ffffff, #121212);
  color: light-dark(#222222, #eeeeee);
}
```

---

## 5. Ejemplos prácticos

### Ejemplo básico

Una paleta de colores reutilizable:

```css
:root {
  --color-principal: #e11d48;
  --color-texto: #222;
}

a {
  color: var(--color-principal);
}

body {
  color: var(--color-texto);
}
```

### Ejemplo habitual

Un sistema de diseño sencillo: colores, espacios, radios y sombra.

```css
:root {
  /* Colores */
  --color-principal: #e11d48;
  --color-fondo: #ffffff;
  --color-texto: #1f2937;
  --color-borde: #e5e7eb;

  /* Espaciado */
  --espacio-1: 0.5rem;
  --espacio-2: 1rem;
  --espacio-3: 1.5rem;
  --espacio-4: 2rem;

  /* Otros */
  --radio: 8px;
  --sombra: 0 4px 12px rgb(0 0 0 / 0.1);
}

.tarjeta {
  padding: var(--espacio-3);
  border: 1px solid var(--color-borde);
  border-radius: var(--radio);
  background: var(--color-fondo);
  box-shadow: var(--sombra);
}

.boton {
  padding: var(--espacio-1) var(--espacio-2);
  border-radius: var(--radio);
  background: var(--color-principal);
  color: white;
}
```

### Ejemplo completo

Tema claro/oscuro, escala de espaciado con `calc()`, variantes de color con `color-mix()`, tipografía fluida y un componente con variables locales:

```css
:root {
  color-scheme: light dark;

  /* Base */
  --principal: #e11d48;
  --unidad: 0.5rem;

  /* Colores derivados */
  --principal-suave: color-mix(in srgb, var(--principal) 15%, transparent);
  --principal-hover: color-mix(in srgb, var(--principal) 85%, black);

  /* Tema claro por defecto */
  --fondo: #ffffff;
  --superficie: #f4f4f5;
  --texto: #18181b;

  /* Escala de espacios */
  --espacio-s: var(--unidad);
  --espacio-m: calc(var(--unidad) * 2);
  --espacio-l: calc(var(--unidad) * 4);

  /* Tipografía fluida */
  --texto-titulo: clamp(1.75rem, 4vw + 1rem, 3rem);
}

@media (prefers-color-scheme: dark) {
  :root {
    --fondo: #09090b;
    --superficie: #18181b;
    --texto: #fafafa;
  }
}

body {
  margin: 0;
  background: var(--fondo);
  color: var(--texto);
  font-family: system-ui, sans-serif;
}

h1 {
  font-size: var(--texto-titulo);
}

/* Componente con variables locales */
.boton {
  --fondo-boton: var(--principal);
  --relleno-boton: var(--espacio-s) var(--espacio-m);

  padding: var(--relleno-boton);
  background: var(--fondo-boton);
  color: white;
  border-radius: 8px;
  transition: background 0.2s;
}

.boton:hover {
  --fondo-boton: var(--principal-hover);   /* solo cambiamos la variable */
}

.boton--suave {
  --fondo-boton: var(--principal-suave);
  color: var(--principal);
}

.tarjeta {
  padding: var(--espacio-l);
  background: var(--superficie);
  border-radius: 12px;
  width: min(100% - 2rem, 40rem);          /* margen lateral y tope de ancho */
  margin-inline: auto;
}
```

---

## 6. Buenas prácticas

- **Declara las variables globales en `:root`**, y las específicas de un componente en el propio componente.
- **Nombra las variables por su función, no por su valor**: `--color-principal` mejor que `--rojo`. Así puedes cambiar el color sin que el nombre quede mentiroso.
- **Usa un prefijo o agrupación coherente**: `--color-*`, `--espacio-*`, `--radio-*`, `--fuente-*`.
- **Usa variables para todo lo que se repite**: colores, espaciados, fuentes, sombras, radios.
- **Define los temas cambiando variables**, no repitiendo reglas completas.
- **Pon siempre un valor de respaldo** en `var()` cuando una variable pueda no existir.
- **Usa `calc()` con variables** para crear escalas (espacios, tamaños).
- **No guardes en variables lo que solo se usa una vez**: añade complejidad sin beneficio.
- **No anides demasiados niveles de `var()`**: se hace difícil de depurar.
- **Revisa la compatibilidad** de las funciones nuevas (`light-dark()`, `color-mix()`, `round()`).
- **Usa las DevTools**: en la pestaña de estilos, las variables se muestran con su valor calculado.

---

## 7. Diferencias importantes

| Comparación | Diferencia |
|---|---|
| Variables CSS vs variables de Sass | Las de Sass se resuelven al **compilar** y desaparecen; las de CSS **viven en el navegador**, heredan y cambian en vivo. |
| Variable global vs local | La global (en `:root`) vale en toda la página; la local solo en el elemento y sus hijos. |
| `var(--x)` vs `var(--x, valor)` | El segundo tiene un **respaldo** si `--x` no está definida. |
| `calc()` vs `clamp()` | `calc()` hace operaciones; `clamp()` limita un valor entre un mínimo y un máximo. |
| `min()` vs `max()` | `min()` da el menor (techo); `max()` da el mayor (suelo). |
| `@property` vs variable normal | `@property` define tipo y valor inicial, y permite animar la variable. |
| `:root` vs `html` | Equivalentes en la práctica, pero `:root` tiene más **especificidad** (es una pseudoclase). |
| `light-dark()` vs `@media (prefers-color-scheme)` | `light-dark()` pone ambos valores en una sola línea; la media query reescribe bloques completos. |

---

## 8. Casos especiales

- **Una variable "inválida en el momento de uso"**: si `--ancho: azul` y haces `width: var(--ancho)`, la propiedad no es válida y se comporta como `unset` (hereda o vuelve a su valor inicial). El navegador no avisa del error.
- **Las variables se heredan**: lo declarado en un padre llega a los hijos, salvo que se redefina (o se use `inherits: false` en `@property`).
- **Una variable no se puede definir a partir de sí misma** (referencia circular): queda inválida.
- **Las variables no se pueden usar en media queries ni en nombres de propiedades**, pero sí dentro de `calc()`, `clamp()`, `repeat()`…
- **Números sin unidad en variables**: guarda `--cantidad: 3` y úsalo con `calc(var(--cantidad) * 1rem)` para obtener una medida.
- **Espacios en el valor**: `--x:  10px ` conserva los espacios; normalmente no afectan, pero en `content: var(--texto)` las comillas deben incluirse.
- **Valor vacío**: `--x: ;` es válido y puede usarse como "interruptor" (técnicas avanzadas).
- **`var()` con `!important`**: el `!important` va en la propiedad que usa la variable, no dentro de `var()`.
- **Rendimiento**: cambiar una variable en `:root` obliga al navegador a recalcular todo lo que la use. Para animaciones frecuentes, cambia variables locales en un elemento concreto.
- **`attr()` en propiedades distintas de `content`** todavía no está disponible en todos los navegadores.
- **Compatibilidad**: las variables CSS funcionan en todos los navegadores modernos; las funciones más recientes (`round()`, `color-mix()`, `light-dark()`) conviene comprobarlas en caniuse.com.

> [!warning] Obsoleto / legado
> Las funciones **`-webkit-calc()`** y **`-moz-calc()`** eran necesarias en navegadores antiguos y ya no se usan. Tampoco hace falta el truco de `postcss-custom-properties` para dar soporte a Internet Explorer, que ya no se mantiene.

---

## 9. Resumen

- Una **variable CSS** se declara con **`--nombre: valor;`** y se usa con **`var(--nombre)`**.
- Las variables se **heredan** y tienen **ámbito**: globales en **`:root`**, locales en un componente.
- **`var(--x, respaldo)`** da un valor alternativo si la variable no existe.
- Las variables permiten **temas** (claro/oscuro) cambiando solo sus valores, y se pueden modificar con **JavaScript** (`setProperty`).
- **`@property`** crea variables con tipo y permite animarlas.
- **`calc()`** hace cálculos; **`min()`**, **`max()`** y **`clamp()`** ponen límites y crean tamaños fluidos.
- **`color-mix()`**, **`rgb()`**, **`hsl()`**, **`oklch()`** y **`light-dark()`** trabajan con colores.
- Otras funciones comunes: **`url()`**, **`attr()`**, **`repeat()`**, **`minmax()`**, **`env()`**, **`counter()`**.
- Nombra las variables por su **función** (`--color-principal`), no por su valor.
- Las variables hacen el CSS **más mantenible**, pero no abuses guardando todo.