# 12 - Asincronía

> [!info] ¿Qué es la asincronía?
> Es la forma en que JavaScript maneja las tareas que **tardan en terminar** (pedir datos a un servidor, esperar un temporizador, leer un archivo) **sin bloquear el resto del programa**. Mientras la tarea espera, el código sigue ejecutándose, y cuando termina, se avisa para continuar.

---

## 1. Antes de empezar

Conviene haber visto:

- [[06 - Funciones]]: sobre todo **callbacks** y funciones flecha.
- [[08 - Objetos]]: las promesas son objetos.
- [[10 - Eventos]]: los eventos ya son un tipo de código asíncrono.

---

## 2. Concepto fundamental

### Código síncrono vs asíncrono

- **Síncrono**: cada instrucción espera a que termine la anterior. Todo ocurre en orden, una cosa a la vez.
- **Asíncrono**: una tarea lenta se "deja en marcha" y el programa sigue con lo siguiente. Cuando la tarea acaba, se ejecuta su parte pendiente.

Piensa en un restaurante: el camarero toma tu pedido, lo pasa a cocina y **no se queda esperando** a que esté listo; atiende otras mesas y vuelve cuando el plato está preparado.

### Por qué es necesario

JavaScript tiene **un solo hilo**: solo puede hacer una cosa a la vez. Si esperara de forma síncrona a que un servidor responda, **la página entera se quedaría congelada**. La asincronía evita eso.

```js
console.log("1");
setTimeout(() => console.log("2"), 1000);
console.log("3");

// Salida: 1, 3, 2
```

El `2` sale el último porque espera un segundo, pero **no bloquea** al `3`.

### El bucle de eventos (event loop)

Es el mecanismo que lo hace posible:

1. Las tareas lentas las gestiona el **navegador** (fuera del hilo principal).
2. Cuando terminan, su función pendiente se pone en una **cola**.
3. El **event loop** va sacando de la cola y ejecutando, **solo cuando el hilo principal está libre**.

Hay dos colas: la de **microtareas** (promesas, con prioridad) y la de **macrotareas** (temporizadores, eventos). Las microtareas se ejecutan antes.

---

## 3. Sintaxis / estructura

La asincronía en JavaScript ha evolucionado en tres etapas:

### 1. Callbacks (la forma antigua)

Se pasa una función que se ejecutará cuando la tarea termine.

```js
setTimeout(() => {
  console.log("Han pasado 2 segundos");
}, 2000);
```

### 2. Promesas

Un objeto que representa un resultado **futuro**.

```js
const promesa = new Promise((resolve, reject) => {
  // ...tarea...
  resolve("Todo bien");     // éxito
  // reject("Algo falló");  // error
});

promesa
  .then(valor => console.log(valor))
  .catch(error => console.error(error))
  .finally(() => console.log("Terminado"));
```

### 3. `async` / `await` (la forma moderna)

Permite escribir código asíncrono que **se lee como si fuera síncrono**.

```js
async function cargar() {
  try {
    const resultado = await promesa;
    console.log(resultado);
  } catch (error) {
    console.error(error);
  }
}
```

---

## 4. Elementos / propiedades / características

### Temporizadores

| Función | Qué hace |
|---------|----------|
| `setTimeout(fn, ms)` | Ejecuta `fn` **una vez** tras `ms` milisegundos |
| `setInterval(fn, ms)` | Ejecuta `fn` **repetidamente** cada `ms` |
| `clearTimeout(id)` | Cancela un `setTimeout` |
| `clearInterval(id)` | Cancela un `setInterval` |

```js
const id = setInterval(() => console.log("tic"), 1000);
clearInterval(id);   // para detenerlo
```

### Callbacks y el "callback hell"

Anidar callbacks uno dentro de otro se vuelve ilegible:

```js
paso1(() => {
  paso2(() => {
    paso3(() => {
      // pirámide infernal
    });
  });
});
```

Las promesas y `async/await` nacieron para evitar esto.

### Estados de una promesa

| Estado | Significado |
|--------|-------------|
| `pending` | Pendiente: aún no ha terminado |
| `fulfilled` | Cumplida: terminó bien |
| `rejected` | Rechazada: terminó con error |

Una vez cumplida o rechazada, **ya no cambia**.

### Métodos de las promesas

| Método | Para qué sirve |
|--------|----------------|
| `.then(fn)` | Qué hacer si sale bien |
| `.catch(fn)` | Qué hacer si falla |
| `.finally(fn)` | Se ejecuta siempre al terminar |

### Encadenar promesas

Cada `.then()` devuelve una nueva promesa, así que se pueden encadenar de forma plana:

```js
obtenerUsuario()
  .then(usuario => obtenerPedidos(usuario.id))
  .then(pedidos => console.log(pedidos))
  .catch(error => console.error(error));
```

### `async` y `await`

- **`async`** delante de una función: hace que **siempre devuelva una promesa**.
- **`await`** delante de una promesa: **pausa esa función** hasta que se resuelva y devuelve su valor. No bloquea el resto del programa.

```js
async function obtenerDatos() {
  const respuesta = await fetch("https://api.ejemplo.com/datos");
  const datos = await respuesta.json();
  return datos;
}
```

> [!warning] `await` solo funciona dentro de una función `async`
> (o en el nivel superior de un módulo). Si lo usas en una función normal, da error.

### Manejo de errores

Con `async/await`, se usa `try...catch`:

```js
async function cargar() {
  try {
    const respuesta = await fetch("/api/datos");
    const datos = await respuesta.json();
    return datos;
  } catch (error) {
    console.error("Algo falló:", error);
  } finally {
    console.log("Fin");
  }
}
```

### Ejecutar varias promesas a la vez

| Método | Qué hace |
|--------|----------|
| `Promise.all([...])` | Espera a que **todas** terminen; si **una falla**, falla todo |
| `Promise.allSettled([...])` | Espera a todas y da el resultado de **cada una**, bien o mal |
| `Promise.race([...])` | Devuelve la **primera** que termine (bien o mal) |
| `Promise.any([...])` | Devuelve la **primera que salga bien** |

```js
const [usuarios, productos] = await Promise.all([
  fetch("/api/usuarios").then(r => r.json()),
  fetch("/api/productos").then(r => r.json())
]);
```

### Crear promesas útiles

```js
function esperar(ms) {
  return new Promise(resolve => setTimeout(resolve, ms));
}

await esperar(1000);   // pausa 1 segundo
```

### Promesas ya resueltas

```js
Promise.resolve("valor");
Promise.reject(new Error("fallo"));
```

### `await` en bucles

```js
for (const id of ids) {
  const dato = await obtener(id);   // uno detrás de otro (secuencial)
}
```

Para hacerlas en paralelo:

```js
await Promise.all(ids.map(id => obtener(id)));
```

### `fetch` y la asincronía

`fetch` devuelve una promesa. Se ve a fondo en [[13 - Fetch y APIs]].

---

## 5. Ejemplos prácticos

### Ejemplo básico: `setTimeout`

```js
console.log("Inicio");
setTimeout(() => console.log("Pasó 1 segundo"), 1000);
console.log("Fin");

// Inicio, Fin, Pasó 1 segundo
```

### Ejemplo habitual: `async/await` con `try/catch`

```js
async function cargarUsuario(id) {
  try {
    const respuesta = await fetch(`/api/usuarios/${id}`);
    if (!respuesta.ok) throw new Error("No encontrado");
    return await respuesta.json();
  } catch (error) {
    console.error(error.message);
  }
}

const usuario = await cargarUsuario(1);
```

### Ejemplo: varias peticiones a la vez

```js
async function cargarTodo() {
  const [a, b] = await Promise.all([
    fetch("/api/a").then(r => r.json()),
    fetch("/api/b").then(r => r.json())
  ]);
  console.log(a, b);
}
```

---

## 6. Buenas prácticas

- **Usa `async/await`** en lugar de callbacks anidados o largas cadenas de `.then()`.
- **Maneja siempre los errores** con `try...catch` (o `.catch()`).
- **Usa `Promise.all`** cuando las tareas no dependen unas de otras: es mucho más rápido que esperarlas una a una.
- **No uses `await` dentro de un bucle** si las tareas se pueden hacer en paralelo.
- **Cancela los temporizadores** (`clearTimeout`, `clearInterval`) cuando ya no hagan falta.
- **Comprueba `respuesta.ok`** al usar `fetch`: no lanza error con códigos como 404 o 500.
- **Devuelve siempre las promesas** dentro de `.then()` para poder encadenar.
- **No mezcles** `async/await` y `.then()` sin necesidad.
- **Da nombres que indiquen que es asíncrona** (`cargarDatos`, `obtenerUsuario`).
- **Muestra feedback al usuario** (un "Cargando...") mientras se espera.

---

## 7. Diferencias importantes

### Callbacks vs promesas vs `async/await`

| | Callbacks | Promesas | `async/await` |
|---|-----------|----------|---------------|
| Legibilidad | Mala si se anidan | Buena | **La mejor** |
| Manejo de errores | Manual en cada nivel | `.catch()` | `try...catch` |
| Estado actual | Antiguo | Base de todo lo moderno | **Recomendado** |

### `setTimeout` vs `setInterval`

- `setTimeout`: ejecuta **una vez**.
- `setInterval`: ejecuta **una y otra vez** hasta que lo cancelas.

### `Promise.all` vs `allSettled` vs `race` vs `any`

| Método | Termina cuando... | Si una falla |
|--------|-------------------|--------------|
| `all` | Todas terminan bien | Falla todo |
| `allSettled` | Todas terminan | No pasa nada, informa de cada una |
| `race` | La primera termina | Falla si esa falla |
| `any` | La primera sale **bien** | La ignora |

### Secuencial vs paralelo

```js
// Secuencial: tarda a + b
const x = await tareaA();
const y = await tareaB();

// Paralelo: tarda max(a, b)
const [x2, y2] = await Promise.all([tareaA(), tareaB()]);
```

### Síncrono vs asíncrono

- **Síncrono**: bloquea hasta terminar.
- **Asíncrono**: sigue y ya avisará.

---

## 8. Casos especiales

### El `0` de `setTimeout` no es inmediato

```js
setTimeout(() => console.log("B"), 0);
console.log("A");
// A, B
```

Aunque el tiempo sea 0, la función va a la cola y espera a que el código actual termine.

### Orden: microtareas antes que macrotareas

```js
setTimeout(() => console.log("timeout"), 0);
Promise.resolve().then(() => console.log("promesa"));
console.log("normal");

// normal, promesa, timeout
```

### Una promesa sin `.catch()`

Si falla y nadie la captura, aparece un error "unhandled rejection". Captura siempre los errores.

### `await` no bloquea el programa

Pausa solo **esa función `async`**; el resto del programa sigue funcionando.

### Funciones flecha `async`

```js
const cargar = async () => {
  const datos = await obtener();
  return datos;
};
```

### `forEach` no espera a `await`

```js
ids.forEach(async (id) => {
  await procesar(id);   // forEach NO espera a estas promesas
});
```

Usa `for...of` o `Promise.all(ids.map(...))`.

### `await` de nivel superior

En módulos (`type="module"`, ver [[14 - Módulos]]) se puede usar `await` fuera de una función.

### Cancelar peticiones

Con `AbortController` se pueden cancelar `fetch` en curso:

```js
const controlador = new AbortController();
fetch(url, { signal: controlador.signal });
controlador.abort();
```

### Evitar condiciones de carrera

Si lanzas varias peticiones seguidas (por ejemplo, un buscador), la respuesta de una antigua puede llegar **después** que la nueva. Se soluciona cancelando la anterior o comprobando que la respuesta sigue siendo relevante.

### Timeout en una promesa

```js
Promise.race([
  fetch(url),
  new Promise((_, reject) => setTimeout(() => reject(new Error("Tiempo agotado")), 5000))
]);
```

---

## 9. Resumen

- **Asíncrono** = una tarea lenta se deja en marcha y el programa **sigue sin esperar**.
- JavaScript tiene **un solo hilo**; el **event loop** ejecuta lo pendiente cuando el hilo está libre.
- Tres formas de manejarlo: **callbacks** (antiguo), **promesas** (`.then`/`.catch`) y **`async/await`** (moderno y recomendado).
- Una **promesa** puede estar `pending`, `fulfilled` o `rejected`.
- **`async`** hace que una función devuelva una promesa; **`await`** espera a una promesa dentro de ella.
- Usa **`try...catch`** para manejar errores con `async/await`.
- **`Promise.all`** lanza tareas en paralelo; `allSettled`, `race` y `any` cubren otros casos.
- **`setTimeout`** ejecuta una vez; **`setInterval`** repite; ambos se cancelan con `clear...`.
- **`forEach`** no espera a `await`: usa `for...of` o `Promise.all`.
- Las **microtareas** (promesas) se ejecutan antes que las **macrotareas** (temporizadores).

---

⬅️ [[11 - Formularios]] | ➡️ [[13 - Fetch y APIs]]