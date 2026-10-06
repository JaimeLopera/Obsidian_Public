# Selectores CSS

> [!info] ¿Qué es?
> Un **selector** es la parte de una regla CSS que dice **a qué elementos del HTML** se les aplican los estilos. Antes de poder cambiar un color, un tamaño o una posición, hay que decirle al navegador "esto es lo que quiero cambiar". Eso es lo que hace el selector.

---

## 1. Antes de empezar

Para entender esta nota conviene saber:

- Qué es una etiqueta HTML (`<p>`, `<div>`, `<a>`…) y que tiene atributos como `class` e `id`.
- Que el HTML forma un **árbol**: unos elementos están dentro de otros (padres, hijos, hermanos). Los selectores usan mucho esas relaciones.
- Cómo se conecta un CSS a una página (visto en [[01 - Fundamentos]]).

Para los ejemplos de toda la nota usaremos este HTML:

```html
<header id="cabecera">
  <h1 class="titulo">Mi blog</h1>
  <nav>
    <a href="/" class="enlace activo">Inicio</a>
    <a href="https://ejemplo.com" class="enlace externo" target="_blank">Ejemplo</a>
    <a href="/contacto.pdf" class="enlace">Contacto</a>
  </nav>
</header>

<main>
  <article class="tarjeta destacada">
    <h2>Primer artículo</h2>
    <p>Texto con un <em>énfasis</em> y un <a href="/mas">enlace</a>.</p>
    <p>Otro párrafo.</p>
    <ul>
      <li>Uno</li>
      <li>Dos</li>
      <li>Tres</li>
    </ul>
  </article>
</main>
```

---

## 2. Concepto fundamental

Una regla CSS tiene dos partes:

- **Selector**: *a quién* se aplica.
- **Declaraciones**: *qué* se le aplica (propiedad y valor).

El navegador hace esto con cada regla:

1. Lee el selector.
2. Busca en la página **todos** los elementos que lo cumplen.
3. Les aplica las declaraciones.

Un selector no apunta a un solo elemento: apunta a **todos los que coincidan**. Si hay 20 párrafos y escribes `p`, los 20 se ven afectados.

> [!tip] Idea clave
> Cuando escribas un selector, pregúntate: *"¿qué elementos de mi página va a atrapar exactamente?"*. Si atrapa más (o menos) de lo que quieres, ajusta el selector.

---

## 3. Sintaxis / estructura

### Anatomía de una regla

```css
selector {
  propiedad: valor;
  otra-propiedad: otro-valor;
}
```

```css
p {
  color: navy;
  font-size: 18px;
}
```

### Lista de selectores (coma)

Si varios elementos comparten los mismos estilos, se separan con **comas** y comparten el bloque:

```css
h1,
h2,
h3 {
  font-family: Georgia, serif;
}
```

Es lo mismo que escribir tres reglas por separado, pero más corto.

### Comentarios

```css
/* Esto es un comentario en CSS */
```

---

## 4. Tipos de selectores

### 4.1 Selector universal `*`

Atrapa **todos** los elementos de la página.

```css
* {
  box-sizing: border-box;
  margin: 0;
}
```

> [!note]
> Se usa sobre todo en "resets" al inicio de la hoja de estilos. No tiene valor de especificidad (es 0).

### 4.2 Selector de tipo (etiqueta)

Atrapa todos los elementos de una etiqueta concreta.

```css
p {
  line-height: 1.6;
}

a {
  color: crimson;
}
```

### 4.3 Selector de clase `.`

Atrapa los elementos que tienen esa clase en su atributo `class`. Se escribe con un **punto** delante.

```css
.tarjeta {
  border: 1px solid #ccc;
  padding: 1rem;
}
```

- Una clase se puede usar en **muchos** elementos.
- Un elemento puede tener **varias** clases separadas por espacios: `class="enlace activo"`.

Para exigir que tenga **varias clases a la vez**, se escriben pegadas, sin espacios:

```css
.tarjeta.destacada {
  border-color: gold;
}
```

### 4.4 Selector de ID `#`

Atrapa el elemento con ese `id`. Se escribe con una **almohadilla** delante.

```css
#cabecera {
  background: #222;
  color: white;
}
```

- Un `id` debe ser **único** en la página.
- Tiene mucha más fuerza que una clase (ver especificidad en el apartado 4.7), por eso se recomienda usarlo poco en CSS.

### 4.5 Selectores de atributo `[ ]`

Atrapan elementos según **sus atributos**, no solo por clase o id.

| Selector | Qué atrapa | Ejemplo |
|---|---|---|
| `[attr]` | Tiene el atributo (con cualquier valor) | `[target]` |
| `[attr="valor"]` | El valor es **exactamente** ese | `[type="email"]` |
| `[attr~="valor"]` | El valor es una lista de palabras y **una** es esa | `[class~="activo"]` |
| `[attr\|="valor"]` | El valor es `valor` o empieza por `valor-` | `[lang\|="es"]` |
| `[attr^="valor"]` | El valor **empieza** por... | `[href^="https"]` |
| `[attr$="valor"]` | El valor **termina** en... | `[href$=".pdf"]` |
| `[attr*="valor"]` | El valor **contiene**... | `[href*="ejemplo"]` |

```css
/* Enlaces que abren en pestaña nueva */
a[target="_blank"] {
  text-decoration: underline dotted;
}

/* Enlaces externos seguros */
a[href^="https"] {
  color: green;
}

/* Enlaces a PDF */
a[href$=".pdf"]::after {
  content: " (PDF)";
}
```

Se puede añadir una **`i`** antes del corchete de cierre para ignorar mayúsculas y minúsculas:

```css
a[href$=".pdf" i] {
  color: red;
}
```

### 4.6 Combinadores

Los combinadores permiten seleccionar un elemento **según su relación** con otro.

#### Descendiente (espacio)

Atrapa elementos que están **dentro** de otro, a cualquier profundidad.

```css
.tarjeta p {
  margin-bottom: 0.5rem;
}
```

Atrapa cualquier `<p>` dentro de `.tarjeta`, esté justo dentro o más profundo.

#### Hijo directo `>`

Atrapa solo los **hijos directos** (un nivel), no los nietos.

```css
.tarjeta > p {
  font-weight: bold;
}
```

#### Hermano adyacente `+`

Atrapa el elemento que viene **justo después** de otro, con el mismo padre.

```css
h2 + p {
  font-size: 1.2rem; /* solo el primer párrafo tras un h2 */
}
```

#### Hermano general `~`

Atrapa **todos** los hermanos que vienen **después** de un elemento (no tienen que ser inmediatos).

```css
h2 ~ p {
  color: gray;
}
```

#### Resumen visual de combinadores

| Combinador | Símbolo | Relación | Ejemplo |
|---|---|---|---|
| Descendiente | (espacio) | Dentro, a cualquier nivel | `article p` |
| Hijo | `>` | Dentro, solo un nivel | `ul > li` |
| Hermano adyacente | `+` | El siguiente justo después | `h2 + p` |
| Hermano general | `~` | Todos los siguientes | `h2 ~ p` |

### 4.7 Especificidad: quién gana cuando hay conflicto

Si dos reglas distintas afectan al mismo elemento y a la misma propiedad, el navegador debe elegir una. Lo hace con la **especificidad**: cuanto más "concreto" es un selector, más fuerza tiene.

Se calcula con **cuatro niveles**, de más fuerte a más débil:

| Nivel | Qué cuenta | Ejemplo |
|---|---|---|
| 1 | Estilos en línea (`style="..."`) | `<p style="color: red">` |
| 2 | IDs | `#cabecera` |
| 3 | Clases, atributos y pseudoclases | `.tarjeta`, `[type="text"]`, `:hover` |
| 4 | Etiquetas y pseudoelementos | `p`, `::before` |

Se suele escribir como una cifra **(estilo en línea, IDs, clases, etiquetas)**:

| Selector | Especificidad |
|---|---|
| `p` | (0, 0, 0, 1) |
| `.tarjeta` | (0, 0, 1, 0) |
| `p.tarjeta` | (0, 0, 1, 1) |
| `nav a.enlace` | (0, 0, 1, 2) |
| `#cabecera` | (0, 1, 0, 0) |
| `#cabecera .titulo` | (0, 1, 1, 0) |

**Reglas para comparar:**

1. Se compara **nivel por nivel**, empezando por el más fuerte. Un solo ID le gana a cualquier cantidad de clases.
2. Si hay **empate**, gana la regla que aparece **más abajo** en el CSS.
3. El selector universal `*` y los combinadores (`>`, `+`, `~`, espacio) **no suman** nada.

```css
p {
  color: black;         /* (0,0,0,1) */
}

.tarjeta p {
  color: blue;          /* (0,0,1,1) → gana a la anterior */
}

#principal p {
  color: green;         /* (0,1,0,1) → gana a las dos */
}
```

> [!warning] `!important`
> Poner `!important` a un valor lo salta todo el sistema de especificidad:
> ```css
> p { color: red !important; }
> ```
> Úsalo solo en casos muy justificados (por ejemplo, para sobrescribir CSS de terceros que no puedes tocar). Si abusas de él, el CSS se vuelve casi imposible de mantener.

### 4.8 Pseudoclases y pseudoelementos (vistazo rápido)

Se explican con detalle en [[12 - Pseudoclases y pseudoelementos]]. Aquí solo lo necesario para reconocerlos:

- **Pseudoclase** (`:`): un **estado** o una **posición** del elemento. Ejemplos: `a:hover` (con el ratón encima), `li:first-child` (el primer hijo), `input:focus`.
- **Pseudoelemento** (`::`): una **parte** concreta de un elemento. Ejemplos: `p::first-line`, `div::before`.

```css
a:hover {
  color: orange;
}

li:nth-child(2) {
  font-weight: bold;
}
```

### 4.9 Selectores modernos: `:is()`, `:where()`, `:not()`, `:has()`

Son pseudoclases especiales que ayudan a escribir selectores más cortos y potentes.

#### `:is()` — "cualquiera de estos"

Agrupa varios selectores y evita repetir.

```css
/* Antes */
header a:hover,
main a:hover,
footer a:hover {
  color: orange;
}

/* Con :is() */
:is(header, main, footer) a:hover {
  color: orange;
}
```

Su especificidad es la del **selector más fuerte** de su lista.

#### `:where()` — igual que `:is()`, pero sin fuerza

Funciona igual, pero su especificidad es **siempre 0**. Es ideal para estilos base que otras reglas puedan sobrescribir fácilmente.

```css
:where(h1, h2, h3) {
  margin-block: 1em;
}
```

#### `:not()` — "todo menos esto"

```css
.enlace:not(.activo) {
  opacity: 0.7;
}

li:not(:last-child) {
  border-bottom: 1px solid #ddd;
}
```

#### `:has()` — "el que contiene a..."

Permite seleccionar un elemento **según lo que tiene dentro** (a veces llamado "selector padre").

```css
/* Tarjetas que contienen una imagen */
.tarjeta:has(img) {
  padding: 0;
}

/* Formularios con algún campo inválido */
form:has(input:invalid) {
  border: 1px solid red;
}
```

### 4.10 Anidamiento de CSS (`&`)

El CSS moderno permite escribir reglas **dentro de otras**, como en Sass, sin herramientas extra. El símbolo `&` representa "el selector padre".

```css
.tarjeta {
  border: 1px solid #ccc;

  & h2 {
    font-size: 1.5rem;
  }

  &:hover {
    border-color: black;
  }

  &.destacada {
    border-color: gold;
  }
}
```

Equivale a escribir `.tarjeta h2`, `.tarjeta:hover` y `.tarjeta.destacada` por separado.

> [!tip]
> Cuidado con anidar demasiado: más de 2 o 3 niveles hacen el CSS difícil de leer y suben la especificidad sin darte cuenta.

---

## 5. Ejemplos prácticos

### Ejemplo básico

Cambiar el color de todos los párrafos y dar estilo a los elementos con una clase:

```css
p {
  color: #333;
}

.titulo {
  font-size: 2.5rem;
}
```

### Ejemplo habitual

Menú de navegación: enlaces con estilo base, enlace activo y enlaces externos distintos.

```css
nav a {
  color: #444;
  text-decoration: none;
  padding: 0.5rem 1rem;
}

nav a:hover {
  background: #eee;
}

nav a.activo {
  font-weight: bold;
  border-bottom: 2px solid crimson;
}

nav a[href^="https"]::after {
  content: " ↗";
}
```

### Ejemplo completo

Una tarjeta con listas, separadores y estados, usando varios tipos de selector a la vez:

```css
/* Base */
.tarjeta {
  border: 1px solid #ccc;
  border-radius: 8px;
  padding: 1rem;
  max-width: 40ch;
}

/* Variante destacada: dos clases pegadas */
.tarjeta.destacada {
  border-color: gold;
  background: #fffbea;
}

/* Solo el título directo de la tarjeta */
.tarjeta > h2 {
  margin-top: 0;
}

/* Primer párrafo justo después del título */
.tarjeta > h2 + p {
  font-size: 1.1rem;
  color: #555;
}

/* Lista dentro de la tarjeta */
.tarjeta ul {
  padding-left: 1.2rem;
}

/* Separador entre elementos, menos el último */
.tarjeta li:not(:last-child) {
  border-bottom: 1px dashed #ddd;
}

/* Enlaces dentro de párrafos de la tarjeta */
.tarjeta p a {
  color: crimson;
}

/* Si la tarjeta contiene una lista, más espacio abajo */
.tarjeta:has(ul) {
  padding-bottom: 1.5rem;
}
```

---

## 6. Buenas prácticas

- **Usa clases como herramienta principal.** Son reutilizables y tienen una especificidad media, fácil de controlar.
- **Evita los IDs para dar estilos.** Su fuerza es muy alta y luego cuesta sobrescribirlos. Déjalos para enlaces internos (`#seccion`) o JavaScript.
- **No encadenes selectores largos.** `body main article .contenido ul li a` es frágil: si cambias el HTML, se rompe. Mejor `.enlace-lista`.
- **Mantén la especificidad baja y pareja.** Así las reglas se pueden sobrescribir sin trucos.
- **No uses `!important`** salvo caso justificado.
- **Pon nombres que describan el propósito**, no la apariencia: `.alerta` es mejor que `.rojo`.
- **Usa `:where()` en estilos base** para que sea fácil sobrescribirlos.
- **No uses selectores de etiqueta sueltos para componentes** (por ejemplo `div { ... }`): afectan a toda la página.
- **No anides más de 2 o 3 niveles**, ni con `&` ni con espacios.

---

## 7. Diferencias importantes

| Comparación | Diferencia |
|---|---|
| `.a .b` vs `.a.b` | Con espacio: `.b` **dentro de** `.a`. Sin espacio: un **mismo** elemento con **ambas** clases. |
| `.a, .b` vs `.a .b` | Con coma: elementos que son `.a` **o** `.b`. Con espacio: `.b` dentro de `.a`. |
| `.a .b` vs `.a > .b` | Espacio: cualquier nivel de profundidad. `>`: solo hijo directo. |
| `h2 + p` vs `h2 ~ p` | `+`: solo el siguiente inmediato. `~`: todos los siguientes hermanos. |
| Clase vs ID | La clase se reutiliza y tiene menos fuerza. El ID es único y tiene mucha más fuerza. |
| `:is()` vs `:where()` | Hacen lo mismo, pero `:is()` aporta la especificidad de su selector más fuerte y `:where()` aporta 0. |
| `:` vs `::` | Un solo `:` es **pseudoclase** (estado). Dos `::` es **pseudoelemento** (una parte del elemento). |
| `[class="a"]` vs `.a` | `[class="a"]` exige que el atributo sea **exactamente** `a`. `.a` funciona aunque haya más clases. |

---

## 8. Casos especiales

- **Mayúsculas y minúsculas**: los nombres de clases e IDs **sí distinguen** mayúsculas (`.Tarjeta` ≠ `.tarjeta`). Los nombres de etiquetas HTML no. Los valores de atributo sí, salvo que uses la bandera `i`.
- **Caracteres especiales en nombres**: si una clase tiene caracteres raros (como `/` o `:`), hay que **escaparlos** con una barra invertida.
```css
  .ancho-1\/2 {
    width: 50%;
  }
```
- **IDs o clases que empiezan por número**: no son válidos sin escapar en CSS (`#1caja` falla). Mejor nombrarlos empezando por letra.
- **Un selector inválido en una lista invalida toda la regla.** Si en `h1, h2, :selector-que-no-existe { ... }` uno falla, el navegador ignora **todo el bloque**. Dentro de `:is()` y `:where()` esto no pasa: ignoran solo el selector inválido.
- **Selectores de atributo sin comillas**: solo funcionan si el valor es un identificador simple. Con espacios o símbolos, usa comillas siempre.
- **Elementos que se añaden con JavaScript** se estilan igual que los demás: el CSS se aplica en cuanto existen.

> [!warning] Obsoleto / legado
> Los combinadores `>>>` y `/deep/` (para "atravesar" el Shadow DOM) están **obsoletos** y no deben usarse. Lo actual es exponer partes con `::part()` o compartir estilos mediante variables CSS (ver [[13 - Variables y funciones]]).

---

## 9. Resumen

- Un **selector** dice a qué elementos se aplican los estilos; atrapa **todos** los que coincidan.
- Los tipos principales: **universal** `*`, **etiqueta** `p`, **clase** `.x`, **ID** `#x`, **atributo** `[x]`.
- Los **combinadores** relacionan elementos: espacio (descendiente), `>` (hijo), `+` (hermano inmediato), `~` (hermanos siguientes).
- La **coma** agrupa selectores; **pegar** selectores (`.a.b`) exige que se cumplan todos a la vez.
- La **especificidad** decide quién gana: estilo en línea > ID > clase/atributo/pseudoclase > etiqueta. Si hay empate, gana la regla más abajo.
- Selectores modernos útiles: `:is()`, `:where()` (fuerza 0), `:not()`, `:has()` y el anidamiento con `&`.
- Buena práctica: **clases** con especificidad baja, sin `!important` y sin cadenas largas de selectores.
- Detalles de pseudoclases y pseudoelementos en [[12 - Pseudoclases y pseudoelementos]].