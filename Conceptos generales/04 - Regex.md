# 04 - Regex

> [!info] ¿Qué es?
> Una **expresión regular** (o *regex*) es un patrón que describe un tipo de texto. Sirve para **buscar**, **comprobar** o **reemplazar** partes de un texto sin tener que escribir cada caso a mano. Por ejemplo, un solo patrón puede decir "cualquier número de teléfono de 9 cifras" o "cualquier correo electrónico".

---

## 1. Antes de empezar

- Una regex **no es un lenguaje de programación**: es una forma de escribir patrones. Casi todos los lenguajes la incluyen (JavaScript, Python, PHP, SQL, `grep` en la terminal…).
- La idea básica es la misma en todos, pero hay **pequeñas diferencias** entre lenguajes (los "sabores" o *flavors*). En esta nota se usa la sintaxis de **JavaScript**, que es muy parecida a la de los demás.
- Para practicar y ver qué hace cada parte del patrón, es muy útil una herramienta visual como **regex101.com** (elige el sabor "ECMAScript / JavaScript").
- Conviene saber qué es un **string** (cadena de texto). Ver [[02 - Variables y tipos]] en JavaScript.

---

## 2. Concepto fundamental

Una regex es una **plantilla**. Le pasas un texto y la regex responde si ese texto **encaja** con la plantilla y, si quieres, **qué parte** encaja.

Los patrones se construyen con dos tipos de piezas:

| Tipo | Qué es | Ejemplo |
|---|---|---|
| **Caracteres literales** | Letras o números que se buscan tal cual | `gato` encaja con "gato" |
| **Metacaracteres** | Símbolos con significado especial | `.`, `*`, `+`, `?`, `^`, `$`… |

**Ejemplo para entenderlo:** el patrón `c.sa` encaja con "casa", "cosa", "cesa"… porque `.` significa "cualquier carácter". No encaja con "csa" (falta una letra).

---

## 3. Sintaxis / estructura

### En JavaScript

Hay dos formas de crear una regex:

```js
// 1. Literal: entre barras. La más habitual.
const patron = /hola/;

// 2. Con el constructor: el patrón va en un string.
const patron2 = new RegExp("hola");
```

Con el constructor, las barras invertidas hay que **duplicarlas** porque el string ya las usa:

```js
const a = /\d+/;              // literal
const b = new RegExp("\\d+"); // equivale a lo anterior
```

> [!tip] ¿Cuál usar?
> Usa el **literal** `/.../` casi siempre. Usa `new RegExp()` solo cuando el patrón se construye con variables o llega de fuera.

### Partes de una regex literal

```
/   patrón   /  flags
```

- **Patrón**: lo que se busca.
- **Flags** (banderas): letras opcionales después de la segunda barra que cambian cómo se busca. Ejemplo: `/hola/gi`.

---

## 4. Elementos / propiedades / características

### 4.1 Flags (banderas)

| Flag | Nombre | Qué hace |
|---|---|---|
| `g` | global | Busca **todas** las coincidencias, no solo la primera |
| `i` | ignore case | No distingue mayúsculas de minúsculas |
| `m` | multiline | `^` y `$` funcionan en **cada línea**, no solo en todo el texto |
| `s` | dotAll | El `.` también encaja con saltos de línea |
| `u` | unicode | Trata bien emojis y caracteres especiales |
| `y` | sticky | Busca solo desde una posición exacta (poco usado) |

### 4.2 Metacaracteres básicos

| Símbolo | Significado | Ejemplo | Encaja con |
|---|---|---|---|
| `.` | Cualquier carácter (menos salto de línea) | `a.c` | "abc", "a1c" |
| `^` | Inicio del texto | `^Hola` | "Hola mundo" |
| `$` | Final del texto | `fin$` | "al fin" |
| `\` | Escapa un símbolo especial | `\.` | un punto real "." |
| `\|` | "O" (alternativa) | `gato\|perro` | "gato" o "perro" |

### 4.3 Clases de caracteres

Una **clase** encaja con **un solo carácter** de un grupo.

| Patrón | Significado |
|---|---|
| `[abc]` | "a", "b" o "c" |
| `[a-z]` | Cualquier letra minúscula |
| `[A-Z]` | Cualquier letra mayúscula |
| `[0-9]` | Cualquier dígito |
| `[a-zA-Z0-9]` | Letra o número |
| `[^abc]` | Cualquier carácter **excepto** "a", "b" o "c" (el `^` dentro de `[]` niega) |

### 4.4 Atajos de clases

| Atajo | Equivale a | Significado |
|---|---|---|
| `\d` | `[0-9]` | Un dígito |
| `\D` | `[^0-9]` | Algo que **no** es dígito |
| `\w` | `[A-Za-z0-9_]` | Letra, número o guion bajo |
| `\W` | `[^A-Za-z0-9_]` | Algo que **no** es `\w` |
| `\s` | espacio, tabulador, salto de línea | Un espacio en blanco |
| `\S` | — | Algo que **no** es espacio en blanco |
| `\b` | — | Límite de palabra (inicio o fin de una palabra) |

> [!warning] Ojo con las tildes y la ñ
> `\w` **no** reconoce letras como `á`, `é` o `ñ`. Si trabajas con español, usa `[a-zA-ZáéíóúüñÁÉÍÓÚÜÑ]` o el flag `u` junto con `\p{L}` (ver sección 8).

### 4.5 Cuantificadores

Indican **cuántas veces** se repite lo que tienen justo delante.

| Cuantificador | Significado | Ejemplo | Encaja con |
|---|---|---|---|
| `*` | 0 o más veces | `ab*` | "a", "ab", "abbb" |
| `+` | 1 o más veces | `ab+` | "ab", "abbb" (no "a") |
| `?` | 0 o 1 vez (opcional) | `colou?r` | "color", "colour" |
| `{3}` | Exactamente 3 veces | `\d{3}` | "123" |
| `{2,4}` | Entre 2 y 4 veces | `\d{2,4}` | "12", "123", "1234" |
| `{2,}` | 2 veces o más | `\d{2,}` | "12", "12345" |

### 4.6 Grupos

Los paréntesis `()` sirven para **agrupar** y para **capturar** (guardar) lo que encaja.

| Sintaxis | Nombre | Para qué sirve |
|---|---|---|
| `(abc)` | Grupo de captura | Agrupa y guarda lo encontrado |
| `(?:abc)` | Grupo sin captura | Agrupa, pero **no** guarda |
| `(?<nombre>abc)` | Grupo con nombre | Guarda y permite llamarlo por nombre |
| `\1` | Referencia | Repite lo que capturó el grupo 1 |

```js
// Agrupar para repetir un bloque entero
/(ab)+/   // encaja con "ab", "abab", "ababab"
```

### 4.7 Anticipación y retroceso (lookaround)

Comprueban lo que hay **antes o después** de un punto sin incluirlo en el resultado.

| Sintaxis | Nombre | Significado |
|---|---|---|
| `a(?=b)` | Lookahead positivo | "a" **seguida de** "b" |
| `a(?!b)` | Lookahead negativo | "a" **no seguida de** "b" |
| `(?<=b)a` | Lookbehind positivo | "a" **precedida de** "b" |
| `(?<!b)a` | Lookbehind negativo | "a" **no precedida de** "b" |

```js
// Precio sin el símbolo €
"25€".match(/\d+(?=€)/); // ["25"]
```

### 4.8 Codicioso vs perezoso

Por defecto, los cuantificadores son **codiciosos** (*greedy*): cogen **todo lo que pueden**. Si añades `?` detrás, se vuelven **perezosos** (*lazy*): cogen **lo mínimo posible**.

```js
const texto = "<b>uno</b> y <b>dos</b>";

texto.match(/<b>.*<\/b>/);   // ["<b>uno</b> y <b>dos</b>"]  → codicioso: coge todo
texto.match(/<b>.*?<\/b>/);  // ["<b>uno</b>"]               → perezoso: coge lo mínimo
```

---

## 5. Ejemplos prácticos

### 5.1 Métodos de JavaScript para usar regex

| Método | Qué devuelve | Uso típico |
|---|---|---|
| `regex.test(texto)` | `true` / `false` | Comprobar si encaja |
| `texto.match(regex)` | Array con coincidencias o `null` | Sacar lo encontrado |
| `texto.matchAll(regex)` | Iterador con todas las coincidencias (requiere `g`) | Recorrer coincidencias con sus grupos |
| `texto.replace(regex, nuevo)` | Texto nuevo | Reemplazar |
| `texto.replaceAll(regex, nuevo)` | Texto nuevo (requiere `g`) | Reemplazar todas |
| `texto.search(regex)` | Posición o `-1` | Saber dónde empieza |
| `texto.split(regex)` | Array | Cortar el texto por un patrón |

### 5.2 Ejemplo básico: comprobar si encaja

```js
const regex = /hola/i;

console.log(regex.test("Hola mundo")); // true
console.log(regex.test("Adiós"));      // false
```

**Explicación:** `/hola/i` busca "hola" sin importar mayúsculas. `test()` solo responde sí o no.

### 5.3 Ejemplo básico: sacar todos los números

```js
const texto = "Tengo 3 gatos y 12 peces";
const numeros = texto.match(/\d+/g);

console.log(numeros); // ["3", "12"]
```

**Explicación:** `\d+` es "uno o más dígitos" y `g` hace que busque todos, no solo el primero.

### 5.4 Ejemplo habitual: validar un código postal español

```js
const cp = /^\d{5}$/;

console.log(cp.test("18110"));  // true
console.log(cp.test("1811"));   // false (faltan cifras)
console.log(cp.test("18110a")); // false (sobra una letra)
```

**Explicación:** `^` y `$` obligan a que **todo** el texto sea el patrón. Sin ellos, "18110a" también pasaría porque *contiene* cinco dígitos.

### 5.5 Ejemplo habitual: validar un correo (versión sencilla)

```js
const email = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;

console.log(email.test("ana@correo.com")); // true
console.log(email.test("ana@correo"));     // false
```

**Explicación por partes:**
- `^` → inicio.
- `[^\s@]+` → uno o más caracteres que no sean espacio ni `@`.
- `@` → la arroba.
- `[^\s@]+` → el dominio.
- `\.` → un punto real.
- `[^\s@]+` → la terminación (com, es…).
- `$` → fin.

> [!warning] Los correos reales son más complicados
> Esta regex es **suficiente para un formulario**, pero no valida el 100 % de los correos posibles. La única forma de saber si un correo existe es enviarle un mensaje.

### 5.6 Ejemplo habitual: reemplazar con grupos

```js
const fecha = "2026-10-04";
const nueva = fecha.replace(/(\d{4})-(\d{2})-(\d{2})/, "$3/$2/$1");

console.log(nueva); // "04/10/2026"
```

**Explicación:** cada `( )` captura una parte. En el reemplazo, `$1` es el primer grupo (año), `$2` el segundo (mes) y `$3` el tercero (día). Se reordenan.

### 5.7 Ejemplo completo: grupos con nombre y varias coincidencias

```js
const log = "10:30 Error en login | 11:45 Error en pago | 12:00 Todo bien";
const regex = /(?<hora>\d{2}:\d{2}) Error en (?<lugar>\w+)/g;

for (const m of log.matchAll(regex)) {
  console.log(`A las ${m.groups.hora} falló: ${m.groups.lugar}`);
}
// A las 10:30 falló: login
// A las 11:45 falló: pago
```

**Explicación:** los grupos con nombre hacen el código más legible: en vez de `m[1]` y `m[2]` usas `m.groups.hora` y `m.groups.lugar`.

### 5.8 Ejemplo: regex fuera de JavaScript

```bash
# Terminal: buscar líneas que empiecen por "ERROR"
grep -E "^ERROR" servidor.log
```

```sql
-- MySQL: nombres que empiezan por vocal
SELECT nombre FROM usuarios WHERE nombre REGEXP '^[aeiou]';
```

```python
# Python
import re
re.findall(r"\d+", "Tengo 3 gatos y 12 peces")  # ['3', '12']
```

---

## 6. Buenas prácticas

- **Empieza simple y ve añadiendo.** Escribe una parte del patrón, pruébala y sigue. No escribas una regex gigante de golpe.
- **Prueba siempre en regex101.com** con ejemplos que deben encajar **y** ejemplos que **no** deben encajar.
- **Usa `^` y `$`** cuando quieras validar que *todo* el texto cumple el patrón.
- **Escapa los símbolos especiales** cuando los quieras como texto normal: `\.`, `\?`, `\(`, `\$`…
- **Usa grupos con nombre** `(?<nombre>...)` cuando haya varios grupos: el código se entiende mejor.
- **Usa `(?:...)`** si solo necesitas agrupar y no guardar.
- **Comenta las regex complejas.** Una regex que hoy entiendes puede ser ilegible dentro de un mes:
```js
  // Fecha en formato AAAA-MM-DD
  const fecha = /^\d{4}-\d{2}-\d{2}$/;
```
- **No intentes hacerlo todo con regex.** Para JSON usa `JSON.parse()`; para HTML usa el DOM. Las regex no son buenas para estructuras anidadas.
- **Guarda las regex útiles en constantes con nombre** (`const REGEX_CP = ...`) para reutilizarlas.

---

## 7. Diferencias importantes

### `test()` vs `match()`

| | `regex.test(texto)` | `texto.match(regex)` |
|---|---|---|
| Devuelve | `true` / `false` | Array o `null` |
| Úsalo para | Validar | Extraer datos |

### `match()` con `g` vs sin `g`

| | Sin `g` | Con `g` |
|---|---|---|
| Resultado | Primera coincidencia **con detalles** (grupos, posición) | **Todas** las coincidencias, pero **sin** los grupos |

> [!tip] Si necesitas todas las coincidencias **y** sus grupos, usa `matchAll()`.

### `[abc]` vs `(abc)`

| | `[abc]` | `(abc)` |
|---|---|---|
| Significa | **Un** carácter: a, b o c | La secuencia **"abc"** completa |

### `^` fuera y dentro de corchetes

| | Significado |
|---|---|
| `^abc` | Inicio del texto |
| `[^abc]` | Cualquier carácter **menos** a, b, c |

### Literal `/.../` vs `new RegExp()`

| | Literal | Constructor |
|---|---|---|
| Se escribe | `/\d+/` | `new RegExp("\\d+")` |
| Barras invertidas | Una | Doble |
| Cuándo | Patrón fijo | Patrón dinámico (con variables) |

---

## 8. Casos especiales

### 8.1 El flag `g` y `lastIndex` (trampa con `test()`)

Si una regex tiene el flag `g` y la reutilizas con `test()`, **recuerda dónde se quedó** y puede dar resultados raros:

```js
const regex = /a/g;

console.log(regex.test("a")); // true
console.log(regex.test("a")); // false  ← ¡empieza a buscar tras la primera "a"!
```

> [!tip] Solución
> Para **validar** no uses `g`. Reserva `g` para buscar o reemplazar varias coincidencias.

### 8.2 Letras con tildes y ñ (Unicode)

```js
// \p{L} = cualquier letra de cualquier idioma (necesita el flag u)
/^\p{L}+$/u.test("Muñoz");  // true
/^\p{L}+$/u.test("José");   // true
```

### 8.3 Construir una regex con una variable

Si el texto viene del usuario, hay que **escapar** los símbolos especiales o el patrón se rompe (o se vuelve peligroso):

```js
function escapar(texto) {
  return texto.replace(/[.*+?^${}()|[\]\\]/g, "\\$&");
}

const busqueda = "precio (€)";
const regex = new RegExp(escapar(busqueda), "i");
```

### 8.4 Regex que se vuelven muy lentas (ReDoS)

Algunos patrones mal escritos, con repeticiones dentro de repeticiones como `(a+)+`, pueden **bloquear el programa** con ciertos textos. Esto se conoce como **ReDoS**.

```js
// ⚠️ Peligroso con textos largos que casi encajan
/^(a+)+$/.test("aaaaaaaaaaaaaaaaaaaaaaaaaaaa!");
```

> [!warning] Cuidado en el servidor
> Si una regex se aplica a texto que escribe el usuario, evita repeticiones anidadas y limita la longitud del texto antes de comprobarlo. Ver [[05 - Seguridad web]].

### 8.5 Saltos de línea

- `.` **no** encaja con saltos de línea (salvo con el flag `s`).
- `^` y `$` solo marcan inicio y fin de **todo** el texto (salvo con el flag `m`).

```js
const texto = "uno\ndos";
texto.match(/^dos$/);   // null
texto.match(/^dos$/m);  // ["dos"]
```

### 8.6 Diferencias entre lenguajes

- La base (`\d`, `+`, `[ ]`, `( )`…) funciona **igual** en casi todos.
- Las funciones avanzadas (lookbehind, grupos con nombre, `\p{L}`) pueden tener **sintaxis distinta** o no existir en algún lenguaje o versión.
- **Consulta siempre la documentación** del lenguaje que uses.

---

## 9. Resumen

- Una **regex** es un patrón para buscar, validar o reemplazar texto.
- En JavaScript se escribe como `/patrón/flags` o con `new RegExp()`.
- Piezas clave:
  - **Literales**: letras tal cual.
  - **Clases**: `[a-z]`, `\d`, `\w`, `\s`.
  - **Cuantificadores**: `*`, `+`, `?`, `{n,m}`.
  - **Anclas**: `^` inicio, `$` fin, `\b` límite de palabra.
  - **Grupos**: `( )`, `(?: )`, `(?<nombre> )`.
  - **Lookaround**: `(?= )`, `(?! )`, `(?<= )`, `(?<! )`.
- **Flags** más usados: `g` (todas), `i` (sin mayúsculas), `m` (multilínea), `u` (Unicode).
- Los cuantificadores son **codiciosos** por defecto; con `?` detrás son **perezosos**.
- Métodos útiles: `test()`, `match()`, `matchAll()`, `replace()`, `replaceAll()`, `split()`.
- Para **validar** usa `^` y `$` y evita el flag `g`.
- **Prueba** tus regex en regex101.com y **coméntalas** si son complejas.
- Cuidado con los patrones que se pueden volver muy lentos (ReDoS) si procesan texto del usuario.