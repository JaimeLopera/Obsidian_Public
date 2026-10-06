# Flexbox

> [!info] ¿Qué es?
> **Flexbox** (*Flexible Box Layout*) es un sistema de CSS para **organizar elementos en una sola dirección** (en fila o en columna). Permite alinearlos, repartir el espacio entre ellos y hacer que se adapten al tamaño disponible, sin trucos con `float` ni márgenes.

---

## 1. Antes de empezar

Para entender esta nota conviene saber:

- El Box Model: tamaño, padding, borde y margen (ver [[03 - Box Model]]).
- Las unidades `%`, `rem` y `fr` (ver [[04 - Unidades y valores]]).
- Que existen elementos de bloque y en línea (ver [[03 - Box Model]]).

HTML que usaremos en los ejemplos:

```html
<nav class="menu">
  <a href="#">Inicio</a>
  <a href="#">Blog</a>
  <a href="#">Contacto</a>
</nav>

<div class="fila">
  <div class="caja">1</div>
  <div class="caja">2</div>
  <div class="caja">3</div>
</div>
```

---

## 2. Concepto fundamental

Flexbox tiene **dos tipos de piezas**:

- **El contenedor flex** (el padre): el elemento con `display: flex`.
- **Los ítems flex** (los hijos directos): se organizan dentro del contenedor.

Solo los **hijos directos** son ítems. Los nietos no se ven afectados.

### Los dos ejes

Todo en Flexbox gira en torno a dos ejes:

- **Eje principal (main axis)**: la dirección en la que se colocan los ítems. Por defecto es **horizontal** (de izquierda a derecha).
- **Eje transversal (cross axis)**: el eje **perpendicular** al principal. Por defecto es vertical.

```text
flex-direction: row (por defecto)

   Eje principal  →→→→→→→→→→→→→→→→→→→→→
   ┌───────┐ ┌───────┐ ┌───────┐
   │   1   │ │   2   │ │   3   │      ↓
   └───────┘ └───────┘ └───────┘      Eje
                                     transversal
                                       ↓
```

Si cambias a `flex-direction: column`, **los ejes se intercambian**: el principal pasa a ser vertical y el transversal horizontal.

> [!tip] Idea clave
> - **`justify-content`** trabaja en el **eje principal**.
> - **`align-items`** trabaja en el **eje transversal**.
> Si cambias la dirección, lo que hacen cada uno cambia de sentido.

---

## 3. Sintaxis / estructura

Para activar Flexbox, basta con una línea en el padre:

```css
.fila {
  display: flex;
}
```

Con solo eso, los hijos pasan a colocarse **en una fila**, uno al lado del otro, aunque fueran elementos de bloque.

Las propiedades se reparten en dos grupos:

| Se escriben en... | Propiedades |
|---|---|
| **El contenedor** (padre) | `display`, `flex-direction`, `flex-wrap`, `flex-flow`, `justify-content`, `align-items`, `align-content`, `gap` |
| **Los ítems** (hijos) | `flex-grow`, `flex-shrink`, `flex-basis`, `flex`, `align-self`, `order` |

---

## 4. Elementos / propiedades / características

### 4.1 `display: flex` y `display: inline-flex`

| Valor | Efecto |
|---|---|
| `flex` | El contenedor es un **bloque** (ocupa todo el ancho) con ítems flex dentro |
| `inline-flex` | El contenedor es **en línea** (solo ocupa lo necesario), con ítems flex dentro |

### 4.2 `flex-direction` (dirección)

Define el eje principal.

| Valor | Efecto |
|---|---|
| `row` | (por defecto) En fila, de izquierda a derecha |
| `row-reverse` | En fila, de derecha a izquierda |
| `column` | En columna, de arriba abajo |
| `column-reverse` | En columna, de abajo arriba |

```css
.lista {
  display: flex;
  flex-direction: column;
}
```

### 4.3 `flex-wrap` (saltar de línea)

Por defecto, los ítems **no saltan de línea**: se encogen para caber. Con `flex-wrap` pueden pasar a la fila siguiente.

| Valor | Efecto |
|---|---|
| `nowrap` | (por defecto) Todo en una línea |
| `wrap` | Los ítems pasan a la siguiente línea si no caben |
| `wrap-reverse` | Igual, pero las líneas nuevas van **arriba** |

```css
.galeria {
  display: flex;
  flex-wrap: wrap;
}
```

Existe la abreviatura **`flex-flow`**: `flex-flow: row wrap;` equivale a `flex-direction: row` + `flex-wrap: wrap`.

### 4.4 `justify-content` (alinear en el eje principal)

Reparte el espacio sobrante **a lo largo del eje principal**.

| Valor | Efecto |
|---|---|
| `flex-start` | (por defecto) Ítems al principio |
| `flex-end` | Ítems al final |
| `center` | Ítems en el centro |
| `space-between` | Espacio **entre** ítems; el primero y el último pegados a los bordes |
| `space-around` | Espacio **alrededor** de cada ítem (los bordes tienen la mitad) |
| `space-evenly` | Espacio **idéntico** entre todos y en los bordes |

```text
flex-start:      [1][2][3]                       
center:                [1][2][3]                 
flex-end:                              [1][2][3]
space-between:   [1]          [2]          [3]   
space-evenly:       [1]     [2]     [3]          
```

```css
.menu {
  display: flex;
  justify-content: space-between;
}
```

### 4.5 `align-items` (alinear en el eje transversal)

Alinea los ítems **en la dirección perpendicular**, dentro de su línea.

| Valor | Efecto |
|---|---|
| `stretch` | (por defecto) Los ítems se **estiran** hasta ocupar toda la altura |
| `flex-start` | Alineados al principio del eje transversal |
| `flex-end` | Alineados al final |
| `center` | Centrados |
| `baseline` | Alineados por la **línea base del texto** |

```css
.cabecera {
  display: flex;
  align-items: center;      /* centrados verticalmente */
}
```

### 4.6 Centrar algo en el medio (el truco clásico)

Con Flexbox, centrar es muy fácil:

```css
.centrado {
  display: flex;
  justify-content: center;   /* centrado horizontal */
  align-items: center;       /* centrado vertical */
  min-height: 100vh;
}
```

### 4.7 `align-content` (varias líneas)

Alinea **las líneas completas** cuando hay varias (solo si hay `flex-wrap: wrap` y más de una línea). Acepta los mismos valores que `justify-content` más `stretch`.

```css
.galeria {
  display: flex;
  flex-wrap: wrap;
  height: 400px;
  align-content: flex-start;
}
```

> [!note]
> `align-items` alinea los ítems **dentro de cada línea**. `align-content` alinea **las líneas entre sí**. Con una sola línea, `align-content` no tiene efecto.

### 4.8 `gap` (espacio entre ítems)

La forma moderna de separar ítems **sin usar márgenes**.

```css
.fila {
  display: flex;
  gap: 1rem;                  /* 1rem entre todos los ítems */
  gap: 1rem 2rem;             /* fila | columna */
}
```

También existen `row-gap` y `column-gap`.

### 4.9 Propiedades de los ítems: `flex-grow`, `flex-shrink` y `flex-basis`

#### `flex-basis` (tamaño base)

El tamaño **inicial** del ítem en el eje principal, antes de repartir espacio. Funciona como un `width` (en fila) o `height` (en columna).

```css
.caja {
  flex-basis: 200px;
}
```

El valor por defecto es `auto` (usa el `width` o el tamaño del contenido).

#### `flex-grow` (crecer)

Cuánto **crece** el ítem para ocupar el espacio libre sobrante. Es un **número** de proporción.

```css
.a { flex-grow: 1; }   /* se reparten el espacio sobrante 1 : 2 */
.b { flex-grow: 2; }   /* b recibe el doble que a */
```

El valor por defecto es `0` (no crece).

#### `flex-shrink` (encogerse)

Cuánto se **encoge** el ítem cuando no hay espacio suficiente. El valor por defecto es `1` (se encoge). Con `0`, el ítem **no se encoge**.

```css
.icono {
  flex-shrink: 0;       /* nunca se aplasta */
}
```

### 4.10 La abreviatura `flex`

Une `flex-grow`, `flex-shrink` y `flex-basis`:

```css
.caja {
  flex: 1 1 200px;    /* grow | shrink | basis */
}
```

Valores abreviados más usados:

| Escribes | Equivale a | Efecto |
|---|---|---|
| `flex: 1` | `1 1 0%` | Todos los ítems **comparten el espacio a partes iguales** |
| `flex: auto` | `1 1 auto` | Crece y se encoge, partiendo de su tamaño natural |
| `flex: none` | `0 0 auto` | **Tamaño fijo**: ni crece ni se encoge |
| `flex: 0 0 200px` | — | Ancho fijo de 200px |
| `flex: 2` | `2 1 0%` | Ocupa el doble que un ítem con `flex: 1` |

```css
.contenido {
  flex: 1;             /* ocupa todo el espacio restante */
}

.lateral {
  flex: 0 0 250px;     /* ancho fijo de 250px */
}
```

### 4.11 `align-self` (alineación individual)

Permite que **un ítem** se alinee distinto al resto en el eje transversal.

```css
.contenedor {
  display: flex;
  align-items: flex-start;
}

.especial {
  align-self: flex-end;   /* solo este va abajo */
}
```

Acepta los mismos valores que `align-items`, más `auto` (usa lo que diga el contenedor).

### 4.12 `order` (cambiar el orden)

Cambia el orden **visual** de los ítems sin tocar el HTML. Se ordenan de menor a mayor; el valor por defecto es `0`.

```css
.caja-3 {
  order: -1;     /* pasa a ser el primero */
}
```

> [!warning]
> `order` solo cambia el orden **visual**. Los lectores de pantalla y la tecla Tab siguen el orden del **HTML**. No lo uses para reorganizar contenido importante.

### 4.13 Márgenes `auto` dentro de Flexbox

Un `margin: auto` en un ítem absorbe todo el espacio sobrante en esa dirección. Es muy útil para "empujar" un ítem hacia un lado:

```css
.menu {
  display: flex;
  gap: 1rem;
}

.menu .ultimo {
  margin-left: auto;    /* este y los siguientes se van al extremo derecho */
}
```

---

## 5. Ejemplos prácticos

### Ejemplo básico

Una fila de cajas con separación:

```css
.fila {
  display: flex;
  gap: 1rem;
}

.caja {
  padding: 1rem;
  background: #eee;
}
```

### Ejemplo habitual

Barra de navegación con el logo a la izquierda y los enlaces a la derecha:

```html
<header class="barra">
  <a href="#" class="logo">MiWeb</a>
  <nav class="enlaces">
    <a href="#">Inicio</a>
    <a href="#">Blog</a>
    <a href="#">Contacto</a>
  </nav>
</header>
```

```css
.barra {
  display: flex;
  justify-content: space-between;   /* logo a un extremo, enlaces al otro */
  align-items: center;              /* centrados verticalmente */
  padding: 1rem 2rem;
  background: #222;
}

.enlaces {
  display: flex;
  gap: 1.5rem;
}

.barra a {
  color: white;
  text-decoration: none;
}
```

### Ejemplo completo

Tres ejemplos juntos: **pie de página siempre abajo**, **galería de tarjetas que saltan de línea** y **tarjeta con botón siempre al final**.

```html
<body class="pagina">
  <header>Cabecera</header>
  <main class="galeria">
    <article class="tarjeta">
      <h2>Título</h2>
      <p>Descripción corta.</p>
      <a href="#" class="boton">Ver más</a>
    </article>
    <!-- más tarjetas... -->
  </main>
  <footer>Pie de página</footer>
</body>
```

```css
/* 1. Pie de página siempre abajo (sticky footer) */
.pagina {
  display: flex;
  flex-direction: column;
  min-height: 100dvh;
}

.pagina main {
  flex: 1;                            /* ocupa todo el espacio sobrante */
}

/* 2. Galería que salta de línea y se adapta */
.galeria {
  display: flex;
  flex-wrap: wrap;
  gap: 1.5rem;
  padding: 1.5rem;
}

.tarjeta {
  flex: 1 1 250px;                    /* crece, se encoge, base de 250px */
  max-width: 400px;
  display: flex;
  flex-direction: column;             /* una tarjeta también puede ser flex */
  padding: 1.5rem;
  border: 1px solid #ddd;
  border-radius: 12px;
}

/* 3. El botón siempre queda al fondo de la tarjeta */
.tarjeta .boton {
  margin-top: auto;                   /* empuja el botón hacia abajo */
  align-self: flex-start;
  padding: 0.6rem 1.2rem;
  background: crimson;
  color: white;
  border-radius: 6px;
  text-decoration: none;
}
```

---

## 6. Buenas prácticas

- **Usa Flexbox para organizar en una dimensión** (una fila o una columna). Para una cuadrícula en dos dimensiones, usa [[08 - Grid]].
- **Usa `gap` en lugar de márgenes** para separar ítems.
- **Usa `flex: 1`** cuando quieras que varios ítems compartan el espacio por igual.
- **Usa `flex-wrap: wrap`** junto con un `flex-basis` para crear diseños que se adaptan solos.
- **Pon `flex-shrink: 0`** en iconos, imágenes o botones que no deban aplastarse.
- **Mantén el HTML en el orden lógico** y evita abusar de `order`.
- **Anida contenedores flex** si lo necesitas: un ítem puede ser a su vez un contenedor flex.
- **Prueba con las DevTools**: los navegadores muestran un icono "flex" para inspeccionar los ejes y el espacio.
- **No uses Flexbox para toda la página sin pensar**: para el diseño general (cabecera, lateral, contenido) a veces Grid es más claro.

---

## 7. Diferencias importantes

| Comparación | Diferencia |
|---|---|
| `justify-content` vs `align-items` | `justify-content` trabaja en el **eje principal**; `align-items` en el **transversal**. |
| `align-items` vs `align-content` | `align-items` alinea ítems dentro de cada línea; `align-content` alinea las **líneas** entre sí (solo con varias líneas). |
| `align-items` vs `align-self` | `align-items` va en el **padre** y afecta a todos; `align-self` va en **un hijo** y solo le afecta a él. |
| `flex-basis` vs `width` | Ambos dan un tamaño inicial, pero `flex-basis` manda sobre `width` en el eje principal de un contenedor flex. |
| `flex: 1` vs `flex: auto` | `flex: 1` parte de base `0` (reparto igual); `flex: auto` parte del tamaño natural del contenido (reparto desigual). |
| `flex-grow` vs `flex-shrink` | `grow` decide cuánto **crece** con espacio libre; `shrink` cuánto **se encoge** cuando falta espacio. |
| `display: flex` vs `inline-flex` | `flex` ocupa todo el ancho (bloque); `inline-flex` se ajusta a su contenido (en línea). |
| Flexbox vs Grid | Flexbox organiza en **una dimensión**; Grid en **dos** (filas y columnas a la vez). |
| `gap` vs `margin` | `gap` pone espacio **solo entre** ítems; `margin` también lo pone en los extremos. |

---

## 8. Casos especiales

- **Solo los hijos directos son ítems flex.** Si quieres que un nieto se comporte como ítem, su padre también debe ser un contenedor flex.
- **Estirado por defecto**: como `align-items: stretch` es el valor por defecto, todos los ítems de una fila tendrán la misma altura. Si no lo quieres, cambia a `flex-start` o `center`.
- **El problema de `min-width: auto`**: un ítem flex no se encoge por debajo del tamaño de su contenido (por ejemplo, una palabra muy larga o una imagen grande). Para permitirlo, usa `min-width: 0` en el ítem. En columnas, el equivalente es `min-height: 0`.
- **Ignoran `float`, `clear` y `vertical-align`**: dentro de un contenedor flex no tienen efecto.
- **Los márgenes no colapsan** entre ítems flex, a diferencia de los bloques normales.
- **Un ítem con texto suelto**: el texto directo dentro de un contenedor flex se convierte en un ítem anónimo.
- **`flex-direction: column` y la altura**: en columna, los ítems solo se reparten el espacio si el contenedor tiene una altura definida (`height` o `min-height`).
- **`flex: 1` en un ítem con contenido largo**: puede desbordar el contenedor si no pones `min-width: 0`.
- **Elementos con `position: absolute`** dentro de un contenedor flex salen del flujo y ya no son ítems flex.
- **`order` con valores negativos** permite pasar un ítem al principio sin tocar los demás.
- **`gap` en Flexbox** funciona en todos los navegadores actuales.

> [!warning] Obsoleto / legado
> La sintaxis antigua `display: box`, `display: -webkit-box` y `display: -ms-flexbox`, así como los prefijos `-webkit-flex` y `-moz-box`, son versiones **antiguas** de Flexbox. Hoy basta con `display: flex`. Evita las propiedades `box-flex`, `box-orient` y similares.

---

## 9. Resumen

- **Flexbox** organiza ítems en **una dimensión**: fila o columna.
- Se activa con **`display: flex`** en el padre; solo los **hijos directos** son ítems.
- Hay dos ejes: el **principal** (`flex-direction`) y el **transversal** (perpendicular).
- **`justify-content`** alinea en el eje principal; **`align-items`** en el transversal; **`align-content`** reparte las líneas cuando hay varias.
- **`flex-wrap: wrap`** permite que los ítems salten de línea; **`gap`** los separa sin márgenes.
- En los ítems: **`flex-grow`** (crecer), **`flex-shrink`** (encoger), **`flex-basis`** (tamaño base) y la abreviatura **`flex`**.
- **`flex: 1`** reparte el espacio por igual; **`flex: 0 0 250px`** da un ancho fijo.
- **`align-self`** cambia la alineación de un solo ítem; **`order`** cambia el orden visual (con cuidado por accesibilidad).
- **`margin: auto`** en un ítem empuja el resto hacia el otro lado.
- Para diseños en dos dimensiones, usa Grid ([[08 - Grid]]).