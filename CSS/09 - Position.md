# Position

> [!info] ¿Qué es?
> La propiedad **`position`** decide **cómo se coloca un elemento** en la página: en su sitio normal, desplazado un poco, fuera del flujo, fijo en la pantalla o pegajoso al hacer scroll. Junto con `top`, `right`, `bottom`, `left` y `z-index`, permite crear menús fijos, insignias, ventanas modales y superposiciones.

---

## 1. Antes de empezar

Para entender esta nota conviene saber:

- El Box Model y los tipos de elementos (bloque y en línea) (ver [[03 - Box Model]]).
- Las unidades de CSS, sobre todo `px`, `%` y `rem` (ver [[04 - Unidades y valores]]).
- Que normalmente los elementos se colocan **uno detrás de otro** según el orden del HTML. Eso se llama **flujo normal**.

HTML que usaremos en los ejemplos:

```html
<header class="cabecera">Cabecera</header>

<div class="tarjeta">
  <span class="insignia">Nuevo</span>
  <h2>Producto</h2>
  <p>Descripción del producto.</p>
</div>

<div class="fondo-modal">
  <div class="modal">Ventana</div>
</div>
```

---

## 2. Concepto fundamental

Cada elemento tiene un valor de `position`. Según cuál sea:

- Sigue el **flujo normal** (ocupa su sitio y empuja a los demás), o
- Se **saca del flujo** (los demás lo ignoran, como si no estuviera).

Las propiedades **`top`, `right`, `bottom` y `left`** (desplazamientos) solo tienen efecto si `position` **no** es `static`.

> [!tip] Idea clave
> Para posicionar bien un elemento hay que responder a dos preguntas:
> 1. ¿Qué valor de `position` tiene?
> 2. ¿**Respecto a qué** se calculan sus `top`, `left`…? (su sitio original, su padre, la pantalla…)

---

## 3. Sintaxis / estructura

```css
.elemento {
  position: relative;
  top: 10px;
  left: 20px;
}
```

| Propiedad | Qué hace |
|---|---|
| `position` | Define el modo de posicionamiento |
| `top`, `right`, `bottom`, `left` | Distancia desde cada borde de referencia |
| `inset` | Abreviatura de los cuatro (`inset: 0` = `top`, `right`, `bottom` y `left` a 0) |
| `z-index` | Orden de apilamiento (qué queda encima) |

---

## 4. Elementos / propiedades / características

### 4.1 `position: static` (por defecto)

El elemento sigue el flujo normal. **`top`, `left`… y `z-index` no tienen efecto.**

```css
.normal {
  position: static;
}
```

### 4.2 `position: relative`

El elemento **sigue ocupando su sitio original**, pero se puede **desplazar** respecto a él.

```css
.caja {
  position: relative;
  top: 10px;      /* se mueve 10px hacia abajo desde su sitio */
  left: 20px;     /* y 20px hacia la derecha */
}
```

- Los demás elementos **no se enteran** del movimiento: el hueco original se queda reservado.
- Su uso más importante es ser **referencia** para elementos `absolute` hijos (ver 4.3).

### 4.3 `position: absolute`

El elemento **sale del flujo normal** (no ocupa espacio) y se coloca respecto a su **ancestro posicionado más cercano**: el primer padre (o abuelo…) que tenga `position` distinto de `static`. Si no hay ninguno, se coloca respecto a la página (`<html>`).

```css
.tarjeta {
  position: relative;          /* ← será la referencia */
  padding: 1.5rem;
}

.insignia {
  position: absolute;
  top: 0.5rem;
  right: 0.5rem;               /* esquina superior derecha de la tarjeta */
  padding: 0.25rem 0.6rem;
  background: crimson;
  color: white;
  border-radius: 4px;
}
```

> [!warning]
> Si olvidas poner `position: relative` al padre, el elemento absoluto se colocará respecto a otro ancestro (o a la página entera) y aparecerá en un sitio inesperado.

**Truco para cubrir todo el padre:**

```css
.capa {
  position: absolute;
  inset: 0;                    /* top, right, bottom y left a 0 */
}
```

**Centrar un elemento absoluto en el medio:**

```css
.centrado {
  position: absolute;
  inset: 0;
  margin: auto;
  width: 300px;
  height: 200px;               /* necesita tamaño definido */
}
```

### 4.4 `position: fixed`

Sale del flujo y se coloca respecto a la **ventana del navegador** (el viewport). **No se mueve al hacer scroll.**

```css
.barra-superior {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  background: #222;
  color: white;
}

.boton-subir {
  position: fixed;
  bottom: 1rem;
  right: 1rem;
}
```

> [!note]
> Como un elemento `fixed` sale del flujo, tapa el contenido que hay debajo. Normalmente se añade `padding-top` o `margin-top` al contenido para dejarle sitio.

### 4.5 `position: sticky`

Es una mezcla: se comporta como `relative` hasta que, al hacer scroll, llega a una **posición límite** y entonces se **queda pegado**.

```css
.cabecera {
  position: sticky;
  top: 0;                      /* se pega al borde superior al llegar a él */
  background: white;
  z-index: 10;
}
```

- **Necesita al menos un desplazamiento** (`top`, `bottom`, `left` o `right`); sin él, no hace nada.
- Se queda pegado **dentro de su contenedor padre**: cuando el padre termina, el elemento se va con él.
- Sigue ocupando su sitio en el flujo.

### 4.6 Resumen visual de los valores

| Valor | ¿Ocupa sitio? | Se coloca respecto a... | ¿Se mueve con el scroll? |
|---|---|---|---|
| `static` | Sí | Flujo normal | Sí |
| `relative` | Sí (su sitio original) | Su propia posición original | Sí |
| `absolute` | No | Ancestro posicionado más cercano | Sí (con su ancestro) |
| `fixed` | No | La ventana (viewport) | **No** |
| `sticky` | Sí | Su sitio, hasta que llega al límite | Sí, hasta pegarse |

### 4.7 `top`, `right`, `bottom`, `left` e `inset`

Indican la **distancia** desde cada borde de referencia hacia dentro.

```css
.caja {
  position: absolute;
  top: 10px;          /* 10px desde el borde superior */
  right: 20px;        /* 20px desde el borde derecho */
}
```

- Valores negativos sacan el elemento por fuera: `top: -10px`.
- Si pones `top` y `bottom` a la vez (y no hay `height`), el elemento se **estira** entre ambos.

La abreviatura `inset` sigue el orden de `margin`:

```css
.capa {
  inset: 0;                 /* los 4 a 0 */
  inset: 10px 20px;         /* arriba/abajo 10px, izquierda/derecha 20px */
}
```

### 4.8 `z-index` (orden de apilamiento)

Cuando dos elementos se **solapan**, `z-index` decide cuál queda **encima**. Gana el número mayor.

```css
.fondo {
  position: relative;
  z-index: 1;
}

.modal {
  position: relative;
  z-index: 100;               /* queda por encima */
}
```

- Solo funciona en elementos **posicionados** (`relative`, `absolute`, `fixed`, `sticky`) y en **ítems de Flexbox o Grid**.
- Admite valores negativos (el elemento queda por detrás).
- Si dos elementos tienen el mismo `z-index`, queda encima **el que aparece más tarde** en el HTML.

#### Contextos de apilamiento (*stacking context*)

Cada vez que se crea un **contexto de apilamiento**, los elementos de dentro compiten **solo entre ellos**. Un `z-index: 9999` dentro de un contexto bajo **no puede superar** a un elemento de otro contexto con un valor mayor.

Crean un contexto de apilamiento:

- Un elemento con `position` distinto de `static` **y** `z-index` distinto de `auto`.
- `opacity` menor que 1.
- `transform`, `filter`, `perspective`.
- Un ítem flex o grid con `z-index`.
- `isolation: isolate` (la forma limpia de crear uno a propósito).

### 4.9 `float` y `clear` (versión antigua)

`float` saca un elemento del flujo y lo coloca a la izquierda o derecha, y el texto lo rodea.

```css
img.foto {
  float: left;
  margin: 0 1rem 1rem 0;
}

.limpiar {
  clear: both;            /* no se coloca junto a elementos flotantes */
}
```

Hoy solo se usa para **texto rodeando una imagen**. Para diseños, usa [[07 - Flexbox]] o [[08 - Grid]].

---

## 5. Ejemplos prácticos

### Ejemplo básico

Una insignia en la esquina de una tarjeta:

```css
.tarjeta {
  position: relative;
}

.insignia {
  position: absolute;
  top: 0.5rem;
  right: 0.5rem;
}
```

### Ejemplo habitual

Cabecera pegada arriba al hacer scroll:

```css
.cabecera {
  position: sticky;
  top: 0;
  z-index: 100;
  padding: 1rem;
  background: white;
  box-shadow: 0 2px 6px rgb(0 0 0 / 0.1);
}
```

### Ejemplo completo

Ventana modal con fondo oscuro, centrada y por encima de todo:

```html
<div class="fondo-modal">
  <div class="modal">
    <button class="cerrar" aria-label="Cerrar">×</button>
    <h2>Título de la ventana</h2>
    <p>Contenido de la ventana modal.</p>
  </div>
</div>
```

```css
.fondo-modal {
  position: fixed;
  inset: 0;                         /* cubre toda la pantalla */
  display: grid;
  place-items: center;              /* centra la ventana */
  background: rgb(0 0 0 / 0.6);
  z-index: 1000;
}

.modal {
  position: relative;               /* referencia para el botón */
  width: min(90%, 500px);
  padding: 2rem;
  background: white;
  border-radius: 12px;
}

.cerrar {
  position: absolute;
  top: 0.5rem;
  right: 0.5rem;
  border: 0;
  background: none;
  font-size: 1.5rem;
  cursor: pointer;
}
```

---

## 6. Buenas prácticas

- **No uses `position` si no hace falta.** Flexbox y Grid resuelven casi todos los diseños.
- **Pon `position: relative` al padre** siempre que uses `absolute` en un hijo.
- **Usa `inset`** en lugar de escribir las cuatro propiedades.
- **Usa `sticky`** en lugar de JavaScript para cabeceras pegajosas.
- **Usa valores de `z-index` pequeños y ordenados**, y mejor con variables (por ejemplo `--z-modal: 100`). Evita valores como `9999`.
- **Usa `isolation: isolate`** para crear un contexto de apilamiento a propósito y evitar problemas de `z-index`.
- **Deja espacio para los elementos `fixed`** (con padding o margen) para que no tapen contenido.
- **No uses `absolute` para colocar el contenido principal de una página**: no se adapta bien a distintos tamaños.
- **Cuida la accesibilidad**: una ventana modal debe poder cerrarse con teclado y gestionar el foco (mejor con la etiqueta `<dialog>`).

---

## 7. Diferencias importantes

| Comparación | Diferencia |
|---|---|
| `relative` vs `absolute` | `relative` sigue ocupando su sitio; `absolute` sale del flujo y se coloca respecto a un ancestro. |
| `absolute` vs `fixed` | `absolute` depende de un ancestro posicionado; `fixed` depende de la ventana y no se mueve con el scroll. |
| `fixed` vs `sticky` | `fixed` está siempre en la misma posición de la pantalla; `sticky` solo se pega al llegar a su límite y dentro de su padre. |
| `top: 0` en `relative` vs `absolute` | En `relative`, 0 es su sitio original; en `absolute`, 0 es el borde del ancestro posicionado. |
| `z-index` vs orden del HTML | A igual `z-index`, queda encima el que va después en el HTML. |
| `float` vs Flexbox/Grid | `float` es para que el texto rodee una imagen; Flexbox y Grid son para diseñar. |
| `display: none` vs `position: absolute; left: -9999px` | `none` oculta para todos (incluidos lectores de pantalla); la otra técnica oculta solo a la vista (antiguo truco). |

---

## 8. Casos especiales

- **`absolute` sin ancestro posicionado** se coloca respecto a la página (`<html>`), no respecto a su padre directo.
- **`transform`, `filter` y `perspective` en un padre** convierten a ese padre en la referencia de sus hijos `fixed`: un `fixed` dentro de un padre con `transform` deja de ser fijo respecto a la ventana.
- **`sticky` no funciona** si algún ancestro tiene `overflow: hidden`, `auto` o `scroll`, porque el elemento se pega respecto a ese contenedor, no a la ventana.
- **`sticky` necesita un límite** (`top`, etc.) y que su padre sea más alto que él.
- **Los elementos `absolute` y `fixed` se vuelven de bloque**: su `display` se transforma (un `span` se comporta como bloque).
- **Un elemento `absolute` sin ancho** se encoge a su contenido, no ocupa todo el ancho como un bloque normal.
- **Margin collapse**: los elementos absolutos no colapsan márgenes.
- **`z-index` y Flexbox/Grid**: los ítems flex y grid aceptan `z-index` aunque no estén posicionados.
- **`position: fixed` en móviles**: las barras del navegador al hacer scroll pueden hacer que el elemento "salte". Prueba en dispositivos reales.
- **`inset` con un solo valor** aplica el mismo desplazamiento a los cuatro lados.

> [!warning] Obsoleto / legado
> Maquetar páginas enteras con `float` (columnas flotantes con "clearfix") es una técnica **antigua**. Usa Flexbox o Grid. El valor `position: -webkit-sticky` era necesario en Safari antiguo; hoy basta con `position: sticky`.

---

## 9. Resumen

- **`position`** define cómo se coloca un elemento; `top`, `right`, `bottom`, `left` y `inset` lo desplazan, salvo en `static`.
- **`static`**: flujo normal (valor por defecto).
- **`relative`**: sigue ocupando su sitio, se puede desplazar y sirve de referencia para hijos absolutos.
- **`absolute`**: sale del flujo y se coloca respecto al ancestro posicionado más cercano.
- **`fixed`**: se coloca respecto a la ventana y no se mueve con el scroll.
- **`sticky`**: se comporta como `relative` hasta llegar a un límite, y entonces se pega.
- **`z-index`** ordena elementos solapados, pero solo en elementos posicionados o ítems flex/grid, y dentro de su contexto de apilamiento.
- **`inset: 0`** cubre todo el padre; con `margin: auto` y un tamaño, centra el elemento.
- Antes de usar `position`, comprueba si Flexbox o Grid lo resuelven mejor.
- **`float`** queda para que el texto rodee imágenes.