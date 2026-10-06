# Transiciones y animaciones

> [!info] ¿Qué es?
> Las **transiciones** hacen que un cambio de estilo (por ejemplo, al pasar el ratón sobre un botón) ocurra **poco a poco** en lugar de al instante. Las **animaciones** permiten crear movimientos más complejos, con varios pasos, que pueden repetirse solos. Junto a **`transform`** (mover, girar y escalar), son la base de los efectos visuales en CSS.

---

## 1. Antes de empezar

Para entender esta nota conviene saber:

- Las unidades de tiempo: `s` (segundos) y `ms` (milisegundos) (ver [[04 - Unidades y valores]]).
- Las pseudoclases como `:hover` y `:focus` (ver [[12 - Pseudoclases y pseudoelementos]]).
- El Box Model, porque algunos efectos afectan al tamaño de las cajas (ver [[03 - Box Model]]).

HTML que usaremos en los ejemplos:

```html
<a href="#" class="boton">Pulsa aquí</a>

<div class="caja">Caja</div>

<div class="cargando"></div>
```

---

## 2. Concepto fundamental

Cuando una propiedad cambia de valor, el navegador normalmente lo muestra **de golpe**. Con CSS puedes decirle que lo haga **gradualmente**:

| Herramienta | Para qué sirve | Cuándo se usa |
|---|---|---|
| **`transition`** | Pasar de un estado A a un estado B suavemente | Hover, focus, clases que cambian |
| **`@keyframes` + `animation`** | Crear una secuencia de varios pasos | Cargadores, entradas de elementos, efectos que se repiten |
| **`transform`** | Mover, girar, escalar o inclinar un elemento | Combinado con las dos anteriores |

> [!tip] Idea clave
> Una **transición** necesita que algo **provoque** el cambio (hover, un clic, una clase nueva). Una **animación** puede empezar sola, sin que nada la provoque.

---

## 3. Sintaxis / estructura

### Transición

```css
.boton {
  background: crimson;
  transition: background 0.3s ease;      /* propiedad | duración | curva */
}

.boton:hover {
  background: darkred;                   /* el cambio ocurrirá en 0.3 segundos */
}
```

### Animación

```css
@keyframes latido {
  from { transform: scale(1); }
  to   { transform: scale(1.2); }
}

.caja {
  animation: latido 1s ease-in-out infinite alternate;
}
```

---

## 4. Elementos / propiedades / características

### 4.1 `transition` (transiciones)

Se **escribe en el estado inicial** (el elemento normal), no en `:hover`. Así funciona tanto al entrar como al salir del estado.

#### Propiedades por separado

| Propiedad | Qué define | Ejemplo |
|---|---|---|
| `transition-property` | Qué propiedad se anima | `background`, `transform`, `all` |
| `transition-duration` | Cuánto dura | `0.3s`, `300ms` |
| `transition-timing-function` | La "curva" de velocidad | `ease`, `linear`, `ease-in-out` |
| `transition-delay` | Cuánto espera antes de empezar | `0.1s` |

#### Abreviatura

```css
.boton {
  transition: background-color 0.3s ease 0s;   /* propiedad | duración | curva | retraso */
}
```

#### Varias propiedades

Se separan con comas:

```css
.tarjeta {
  transition:
    transform 0.3s ease,
    box-shadow 0.3s ease;
}
```

#### Curvas de velocidad (*timing functions*)

| Valor | Cómo se mueve |
|---|---|
| `ease` | (por defecto) Empieza lento, acelera y termina lento |
| `linear` | Velocidad constante |
| `ease-in` | Empieza lento y acelera |
| `ease-out` | Empieza rápido y termina lento |
| `ease-in-out` | Lento al principio y al final |
| `cubic-bezier(a, b, c, d)` | Curva personalizada |
| `steps(n)` | Avanza a **saltos** (n pasos) |

```css
.menu {
  transition: opacity 0.4s cubic-bezier(0.4, 0, 0.2, 1);
}
```

> [!note]
> **`transition: all`** anima cualquier propiedad que cambie. Es cómodo, pero puede animar cosas que no quieres y gastar rendimiento. Es mejor **nombrar las propiedades**.

### 4.2 Qué propiedades se pueden animar

Se pueden animar las que tienen valores **intermedios** posibles: números, medidas, colores y transformaciones.

| Se anima bien | No se anima (o no de forma natural) |
|---|---|
| `opacity`, `transform` | `display` |
| `color`, `background-color`, `border-color` | `height: auto` (de o hacia `auto`) |
| `width`, `height`, `margin`, `padding` (con valores concretos) | `font-family`, `position` |
| `box-shadow`, `border-radius`, `filter` | Propiedades con palabras clave sin valor intermedio |

> [!warning]
> No se puede animar de `display: none` a `display: block` de forma directa. Para ocultar y mostrar con suavidad, anima **`opacity`** (y `visibility`, o `transform`). Existen soluciones modernas con `@starting-style` y `transition-behavior: allow-discrete`, pero revisa la compatibilidad (ver [[14 - CSS moderno]]).

### 4.3 `transform` (transformaciones)

Mueve, gira, escala o inclina un elemento **sin afectar a los demás** (no cambia el diseño de la página).

| Función | Efecto | Ejemplo |
|---|---|---|
| `translate(x, y)` | **Mover** | `translate(20px, 10px)` |
| `translateX()` / `translateY()` | Mover en un solo eje | `translateY(-4px)` |
| `scale(n)` | **Escalar** (1 = tamaño original) | `scale(1.1)` |
| `rotate(ángulo)` | **Girar** | `rotate(45deg)` |
| `skew(x, y)` | **Inclinar** | `skew(10deg)` |

```css
.caja {
  transform: translateY(-4px) scale(1.05) rotate(2deg);   /* varias a la vez */
}
```

> [!note]
> El **orden importa**: `translate(100px) rotate(45deg)` no da el mismo resultado que `rotate(45deg) translate(100px)`. Se aplican de **derecha a izquierda** respecto al sistema de coordenadas.

También existen propiedades individuales modernas:

```css
.caja {
  translate: 20px 10px;
  rotate: 15deg;
  scale: 1.1;
}
```

#### `transform-origin` (punto de origen)

Es el punto alrededor del cual se gira o escala. Por defecto es el centro.

```css
.puerta {
  transform-origin: left center;     /* gira desde el borde izquierdo */
  transform: rotate(-30deg);
}
```

#### Efectos en 3D

```css
.escena {
  perspective: 800px;                /* profundidad (en el padre) */
}

.carta {
  transform: rotateY(40deg);         /* gira en el eje vertical */
}
```

### 4.4 `@keyframes` (definir una animación)

Describe **los pasos** de la animación. Se pueden usar `from` y `to`, o porcentajes.

```css
@keyframes aparecer {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes pulso {
  0%   { transform: scale(1); }
  50%  { transform: scale(1.15); }
  100% { transform: scale(1); }
}
```

### 4.5 `animation` (aplicar una animación)

| Propiedad | Qué define | Valores habituales |
|---|---|---|
| `animation-name` | Qué `@keyframes` usar | `aparecer` |
| `animation-duration` | Cuánto dura una vuelta | `1s` |
| `animation-timing-function` | Curva de velocidad | `ease`, `linear` |
| `animation-delay` | Espera antes de empezar | `0.5s` |
| `animation-iteration-count` | Cuántas veces se repite | `1`, `3`, `infinite` |
| `animation-direction` | Sentido de las repeticiones | `normal`, `reverse`, `alternate` |
| `animation-fill-mode` | Qué estilo mantiene antes o después | `none`, `forwards`, `backwards`, `both` |
| `animation-play-state` | Reproducir o pausar | `running`, `paused` |

**Abreviatura:** `animation: nombre duración curva retraso repeticiones dirección fill-mode;`

```css
.tarjeta {
  animation: aparecer 0.6s ease-out both;
}

.cargando {
  width: 40px;
  height: 40px;
  border: 4px solid #ddd;
  border-top-color: crimson;
  border-radius: 50%;
  animation: girar 1s linear infinite;
}

@keyframes girar {
  to { transform: rotate(360deg); }
}
```

#### `animation-fill-mode`

| Valor | Efecto |
|---|---|
| `none` | Al terminar, vuelve al estilo original |
| `forwards` | **Se queda** con el último paso al terminar |
| `backwards` | Aplica el **primer paso** durante el retraso |
| `both` | Las dos cosas |

#### `animation-direction`

| Valor | Efecto |
|---|---|
| `normal` | Siempre de principio a fin |
| `reverse` | Siempre de fin a principio |
| `alternate` | Va y vuelve (útil con `infinite`) |
| `alternate-reverse` | Va y vuelve, empezando al revés |

### 4.6 Rendimiento: qué conviene animar

No todas las propiedades cuestan lo mismo al animarse:

| Coste | Propiedades | Por qué |
|---|---|---|
| **Bajo** (muy recomendado) | `transform`, `opacity` | El navegador las gestiona con la tarjeta gráfica, sin recalcular el diseño |
| **Medio** | `color`, `background-color`, `box-shadow` | Hay que repintar |
| **Alto** (evitar) | `width`, `height`, `margin`, `padding`, `top`, `left` | Obligan a recalcular el diseño de la página |

```css
/* Mejor: mover con transform */
.caja { transition: transform 0.3s; }
.caja:hover { transform: translateX(20px); }

/* Peor: mover con left */
.caja { position: relative; transition: left 0.3s; }
.caja:hover { left: 20px; }
```

#### `will-change`

Avisa al navegador de qué va a cambiar, para que se prepare:

```css
.menu-lateral {
  will-change: transform;
}
```

> [!warning]
> Úsalo **solo** en elementos que realmente se animan y por poco tiempo. Abusar de `will-change` consume memoria.

### 4.7 Accesibilidad: respetar el "menos movimiento"

Algunas personas se marean o se distraen con las animaciones. Pueden pedir al sistema que las reduzca:

```css
@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
  }
}
```

Otras recomendaciones:

- No hagas animaciones que **parpadeen** más de 3 veces por segundo.
- Las animaciones largas o infinitas deberían poder **pausarse**.
- No uses el movimiento como **única** forma de transmitir información.

---

## 5. Ejemplos prácticos

### Ejemplo básico

Botón que cambia de color suavemente:

```css
.boton {
  background: crimson;
  color: white;
  padding: 0.7rem 1.4rem;
  border-radius: 8px;
  text-decoration: none;
  transition: background-color 0.3s ease;
}

.boton:hover {
  background: darkred;
}
```

### Ejemplo habitual

Tarjeta que se eleva al pasar el ratón, con sombra:

```css
.tarjeta {
  padding: 1.5rem;
  border-radius: 12px;
  background: white;
  box-shadow: 0 2px 6px rgb(0 0 0 / 0.1);
  transition:
    transform 0.25s ease,
    box-shadow 0.25s ease;
}

.tarjeta:hover,
.tarjeta:focus-within {
  transform: translateY(-6px);
  box-shadow: 0 12px 24px rgb(0 0 0 / 0.15);
}
```

### Ejemplo completo

Entrada escalonada de tarjetas, cargador y respeto de "menos movimiento":

```css
/* Animación de entrada */
@keyframes aparecer {
  from {
    opacity: 0;
    transform: translateY(24px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.tarjeta {
  animation: aparecer 0.6s ease-out both;
}

/* Cada tarjeta empieza un poco más tarde */
.tarjeta:nth-child(1) { animation-delay: 0.1s; }
.tarjeta:nth-child(2) { animation-delay: 0.2s; }
.tarjeta:nth-child(3) { animation-delay: 0.3s; }

/* Cargador giratorio */
@keyframes girar {
  to { transform: rotate(360deg); }
}

.cargando {
  width: 40px;
  height: 40px;
  border: 4px solid #e5e5e5;
  border-top-color: crimson;
  border-radius: 50%;
  animation: girar 0.9s linear infinite;
}

/* Botón con efecto de pulsación */
.boton {
  display: inline-block;
  padding: 0.7rem 1.4rem;
  background: crimson;
  color: white;
  border-radius: 8px;
  transition: transform 0.15s ease, background-color 0.3s ease;
}

.boton:hover  { background: darkred; }
.boton:active { transform: scale(0.96); }

/* Accesibilidad */
@media (prefers-reduced-motion: reduce) {
  .tarjeta,
  .cargando {
    animation: none;
  }

  .boton {
    transition: none;
  }
}
```

---

## 6. Buenas prácticas

- **Anima `transform` y `opacity`** siempre que puedas: son las más ligeras.
- **Nombra las propiedades** en `transition` en lugar de usar `all`.
- **Escribe la `transition` en el estado normal**, no en `:hover`, para que también funcione al salir.
- **Usa duraciones cortas**: entre **150ms y 400ms** para efectos de interfaz; más de 1 segundo cansa.
- **Usa `ease-out`** para elementos que aparecen y **`ease-in`** para los que desaparecen.
- **Respeta `prefers-reduced-motion`.**
- **No animes lo que no aporta**: el movimiento debe ayudar (guiar la atención, dar respuesta), no decorar por decorar.
- **No animes propiedades de diseño** (`width`, `height`, `top`, `left`) en elementos con mucho contenido.
- **Usa `forwards` o `both`** en `animation-fill-mode` si quieres que el elemento se quede en su estado final.
- **Aplica los efectos `:hover` solo si hay ratón** con `@media (hover: hover)` (ver [[10 - Responsive Design]]).
- **Añade el mismo efecto a `:focus-visible`** que a `:hover`, para quienes navegan con teclado.

---

## 7. Diferencias importantes

| Comparación | Diferencia |
|---|---|
| `transition` vs `animation` | La transición va de un estado a otro y necesita un disparador; la animación tiene pasos propios y puede empezar sola. |
| `transform` vs `top`/`left` | `transform` no cambia el diseño y es más ligera; `top`/`left` obligan a recalcular el diseño. |
| `translate()` vs `margin` | `translate` mueve visualmente sin empujar a los vecinos; `margin` cambia el espacio que ocupa. |
| `animation-fill-mode: forwards` vs `both` | `forwards` conserva el último paso; `both` también aplica el primer paso durante el retraso. |
| `ease` vs `linear` | `ease` acelera y frena (más natural); `linear` va a velocidad constante (bueno para giros infinitos). |
| `alternate` vs `reverse` | `alternate` va y vuelve; `reverse` siempre va al revés. |
| `opacity: 0` vs `display: none` | `opacity: 0` se puede animar y el elemento sigue ocupando sitio y recibiendo clics; `display: none` lo elimina del todo. |
| `visibility: hidden` vs `opacity: 0` | `hidden` oculta (y quita el foco y los clics) pero ocupa sitio; `opacity: 0` lo hace transparente pero sigue interactivo. |

---

## 8. Casos especiales

- **Si no hay valor inicial claro**, no hay transición: debe haber un valor "de partida" explícito. Por ejemplo, de `height: auto` a `height: 200px` no se anima.
- **Una transición no se dispara con el primer pintado**: si añades un elemento y le cambias el estilo de inmediato, puede no animarse (se resuelve con `@starting-style` o con una animación de entrada).
- **`transition` no se repite**: solo se ejecuta cuando cambia el valor. Para repetir, usa `animation`.
- **Si cambias el estado a mitad de la transición** (el usuario entra y sale del `:hover` rápido), la transición se invierte desde donde estaba, sin saltos.
- **`transform` crea un contexto de apilamiento** y un nuevo bloque contenedor para hijos `fixed` y `absolute` (ver [[09 - Position]]).
- **`transform` en elementos en línea (`<span>`, `<a>`)** no funciona: necesitan `display: inline-block` o `block`.
- **El orden de las transformaciones importa**: aplicar `rotate` y luego `translate` mueve el elemento en la dirección girada.
- **Animar `height: auto`** se puede hacer con `grid-template-rows: 0fr` → `1fr`, o con `interpolate-size: allow-keywords` (todavía reciente).
- **`animation-delay` negativo** hace que la animación empiece "ya avanzada", útil para desfasar varias animaciones infinitas.
- **Las animaciones se pueden pausar** con `animation-play-state: paused` (por ejemplo, al pasar el ratón por encima).
- **`@keyframes` con el mismo nombre**: si repites el nombre, gana el último que aparezca en el CSS.

> [!warning] Obsoleto / legado
> Las versiones con prefijo `-webkit-transition`, `-webkit-transform`, `-webkit-animation` y `@-webkit-keyframes` eran necesarias en navegadores antiguos. Hoy **no hacen falta**. Tampoco uses `<marquee>` ni `<blink>` de HTML: son obsoletas.

---

## 9. Resumen

- **`transition`** suaviza un cambio de estado; se escribe en el estado inicial e indica propiedad, duración, curva y retraso.
- **`@keyframes` + `animation`** crean secuencias de varios pasos que pueden empezar solas y repetirse.
- **`transform`** mueve (`translate`), escala (`scale`), gira (`rotate`) e inclina (`skew`) sin cambiar el diseño; el orden importa.
- **`transform-origin`** cambia el punto alrededor del que se gira o escala.
- Propiedades de animación clave: **duration, timing-function, delay, iteration-count, direction y fill-mode**.
- **Anima solo `transform` y `opacity`** siempre que puedas, para un buen rendimiento.
- No se puede animar `display` ni `height: auto` de forma directa.
- Mejor **evitar `transition: all`** y nombrar las propiedades.
- Duraciones recomendadas: **150-400ms** para interfaz.
- Respeta **`prefers-reduced-motion`** y no uses el movimiento como única señal.