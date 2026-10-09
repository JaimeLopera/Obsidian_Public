# 15 - Errores y debugging

> [!info] ¿Qué es?
> **Debugging** es buscar y arreglar fallos en tu código. Todo programa tiene errores; lo importante es saber **leerlos**, **controlarlos** con `try...catch` y **encontrar su causa** con las herramientas del navegador.

---

## 1. Antes de empezar

Conviene conocer:

- Funciones y objetos ([[06 - Funciones]], [[08 - Objetos]]).
- Qué es una Promesa y `async/await` ([[12 - Asincronía]]), porque los errores asíncronos se tratan distinto.

**Idea clave:** un error no es el fin del mundo. El navegador te dice **qué** ha pasado, **dónde** y **en qué línea**. Aprender a leer ese mensaje resuelve la mitad de los problemas.

---

## 2. Concepto fundamental

Hay tres grandes tipos de fallos:

| Tipo | Cuándo aparece | Ejemplo |
|---|---|---|
| **De sintaxis** | Antes de ejecutar: el código está mal escrito | Falta un paréntesis o una llave |
| **De ejecución** (*runtime*) | Mientras corre: algo no se puede hacer | Llamar a una función que no existe |
| **Lógicos** | No da error, pero el resultado es incorrecto | Usar `<` en vez de `<=` |

Los lógicos son los más difíciles: JavaScript no avisa, tienes que descubrirlos tú.

### Errores de ejecución más comunes

| Error | Qué significa |
|---|---|
| `SyntaxError` | El código está mal escrito y no se puede interpretar |
| `ReferenceError` | Usas una variable que no existe |
| `TypeError` | Haces algo que ese tipo de valor no permite (por ejemplo, llamar a algo que no es función) |
| `RangeError` | Un número fuera del rango permitido (por ejemplo, recursión infinita) |
| `URIError` | Mal uso de funciones de URL como `decodeURIComponent` |

---

## 3. Sintaxis / estructura

### 3.1 `try...catch...finally`

```javascript
try {
  // código que puede fallar
} catch (error) {
  // qué hacer si falla
} finally {
  // se ejecuta siempre (opcional)
}
```

- `try`: lo intentas.
- `catch`: solo entra si hubo error; `error` es el objeto del fallo.
- `finally`: se ejecuta **siempre**, haya error o no (sirve para limpiar, cerrar, ocultar un "cargando...").

### 3.2 Lanzar tus propios errores: `throw`

```javascript
function dividir(a, b) {
  if (b === 0) {
    throw new Error("No se puede dividir entre cero");
  }
  return a / b;
}
```

### 3.3 Errores con `async/await`

```javascript
async function cargar() {
  try {
    const respuesta = await fetch("https://api.ejemplo.com/datos");
    if (!respuesta.ok) {
      throw new Error("Error HTTP: " + respuesta.status);
    }
    return await respuesta.json();
  } catch (error) {
    console.error("Fallo al cargar:", error.message);
  }
}
```

---

## 4. Elementos / propiedades / características

### 4.1 El objeto `Error`

| Propiedad | Qué contiene |
|---|---|
| `name` | Tipo de error (`"TypeError"`, etc.) |
| `message` | Descripción en texto |
| `stack` | Recorrido de llamadas hasta el fallo (la "pista") |
| `cause` | Error original que provocó este (opcional) |

### 4.2 Errores personalizados

```javascript
class ErrorValidacion extends Error {
  constructor(mensaje) {
    super(mensaje);
    this.name = "ErrorValidacion";
  }
}

try {
  throw new ErrorValidacion("El correo no es válido");
} catch (error) {
  if (error instanceof ErrorValidacion) {
    console.log("Problema de validación:", error.message);
  } else {
    throw error; // no sabemos tratarlo, lo relanzamos
  }
}
```

### 4.3 Métodos de `console`

| Método | Para qué sirve |
|---|---|
| `console.log()` | Mostrar información general |
| `console.error()` | Mostrar un error (en rojo) |
| `console.warn()` | Mostrar un aviso (en amarillo) |
| `console.table()` | Mostrar arrays/objetos como tabla |
| `console.dir()` | Ver un objeto con todas sus propiedades |
| `console.group()` / `groupEnd()` | Agrupar mensajes |
| `console.time()` / `timeEnd()` | Medir cuánto tarda algo |
| `console.assert()` | Avisar solo si una condición es falsa |

### 4.4 La palabra `debugger`

Si escribes `debugger;` en el código y tienes las herramientas de desarrollo abiertas, la ejecución **se detiene** en esa línea para que inspecciones todo.

### 4.5 Herramientas del navegador (DevTools)

Se abren con **F12** o clic derecho → *Inspeccionar*.

| Pestaña | Para qué |
|---|---|
| **Console** | Ver mensajes y errores, probar código |
| **Sources** | Poner puntos de ruptura y avanzar paso a paso |
| **Network** | Ver las peticiones (`fetch`) y sus respuestas |
| **Elements** | Ver y editar el HTML/CSS en vivo |

**Controles al depurar paso a paso:**

- **Continuar:** sigue hasta el siguiente punto de ruptura.
- **Step over:** ejecuta la línea actual sin entrar en funciones.
- **Step into:** entra dentro de la función de esa línea.
- **Step out:** sale de la función actual.

### 4.6 Errores sin capturar

Si un error no está dentro de un `try...catch`, el programa de esa parte se detiene. Puedes enganchar un último aviso global:

```javascript
window.addEventListener("error", (evento) => {
  console.error("Error sin capturar:", evento.message);
});

window.addEventListener("unhandledrejection", (evento) => {
  console.error("Promesa rechazada sin capturar:", evento.reason);
});
```

---

## 5. Ejemplos prácticos

### 5.1 Ejemplo básico

```javascript
try {
  const usuario = JSON.parse("esto no es JSON");
} catch (error) {
  console.error("JSON no válido:", error.message);
}
```

### 5.2 Ejemplo habitual: leer el mensaje de error

```
Uncaught TypeError: Cannot read properties of undefined (reading 'nombre')
    at mostrar (main.js:12)
```

Cómo se lee:

1. **`TypeError`**: tipo de fallo (usaste un valor de forma incorrecta).
2. **`Cannot read properties of undefined (reading 'nombre')`**: intentaste hacer `algo.nombre` pero `algo` es `undefined`.
3. **`main.js:12`**: archivo y línea donde ocurrió.

Solución típica: comprobar que el valor existe antes de usarlo.

```javascript
const nombre = usuario?.nombre ?? "Anónimo";
```

### 5.3 Método para encontrar un fallo

1. **Reproduce** el error siempre igual.
2. **Lee** el mensaje y la línea.
3. **Aísla**: comenta partes hasta saber cuál falla.
4. **Observa** los valores con `console.log` o un punto de ruptura.
5. **Arregla** una sola cosa a la vez.
6. **Comprueba** que funciona y que no has roto otra cosa.

---

## 6. Buenas prácticas

- **Lee el mensaje completo** antes de cambiar nada.
- **Lanza siempre `new Error(...)`**, no textos sueltos (`throw "mal"` pierde el `stack`).
- **Captura solo lo que sabes tratar**; si no, relanza con `throw error`.
- **No dejes un `catch` vacío**: esconde problemas y luego es imposible encontrarlos.
- **Usa `console.error`** para errores, no `console.log`.
- **Comprueba `respuesta.ok`** en `fetch`: una respuesta 404 o 500 **no** lanza error por sí sola.
- **Usa `finally`** para cosas que deben pasar siempre (quitar un spinner, cerrar algo).
- **Quita los `console.log` y `debugger`** antes de entregar el código.
- **Puntos de ruptura antes que `console.log`** cuando el fallo es complicado.

---

## 7. Diferencias importantes

### 7.1 `console.log` vs. puntos de ruptura

| | `console.log` | Punto de ruptura |
|---|---|---|
| Rapidez | Muy rápido de poner | Hay que abrir DevTools |
| Qué ves | Solo lo que imprimes | Todas las variables en ese momento |
| Cambia el código | Sí | No |

### 7.2 `throw` vs. `return`

| | `throw` | `return` |
|---|---|---|
| Qué hace | Corta el flujo y salta al `catch` más cercano | Devuelve un valor y sigue normal |
| Cuándo | Situación realmente inesperada o inválida | Resultado esperado |

### 7.3 `Error` vs. `TypeError` y compañía

`Error` es el tipo general. Los demás (`TypeError`, `RangeError`...) son versiones más concretas que **heredan** de él. Usa el específico si encaja; si no, `Error`.

---

## 8. Casos especiales

### 8.1 `try...catch` no atrapa errores de callbacks diferidos

```javascript
try {
  setTimeout(() => {
    throw new Error("Esto NO lo atrapa el catch de fuera");
  }, 1000);
} catch (error) {
  // nunca se ejecuta
}
```

El `try` termina antes de que el temporizador se dispare. Pon el `try...catch` **dentro** del callback.

### 8.2 `Promise` con `.catch()`

```javascript
fetch("https://api.ejemplo.com/datos")
  .then((respuesta) => respuesta.json())
  .catch((error) => console.error(error));
```

### 8.3 `finally` y `return`

Si hay un `return` dentro de `try`, el `finally` se ejecuta **igualmente** antes de salir de la función.

### 8.4 Errores con `cause`

```javascript
try {
  JSON.parse("{mal");
} catch (error) {
  throw new Error("No se pudo leer la configuración", { cause: error });
}
```

### 8.5 Errores del navegador que no son tuyos

Un error que viene de una extensión o de un script externo puede aparecer en la consola aunque tu código esté bien. Fíjate en el **archivo** que indica.

### 8.6 Recursión infinita

Una función que se llama a sí misma sin parar produce `RangeError: Maximum call stack size exceeded`. Falta una **condición de parada**.

---

## 9. Resumen

- Tres tipos de fallo: **sintaxis**, **ejecución** y **lógicos** (los más traicioneros).
- Errores comunes: `SyntaxError`, `ReferenceError`, `TypeError`, `RangeError`.
- `try...catch...finally` controla los errores; `throw new Error()` crea los tuyos.
- Con `async/await` se usa `try...catch`; con promesas, `.catch()`.
- `fetch` no falla con 404/500: comprueba `respuesta.ok`.
- El objeto `Error` tiene `name`, `message`, `stack` y `cause`.
- DevTools (**F12**): Console, Sources, Network, Elements.
- `debugger` y los puntos de ruptura detienen el código para inspeccionarlo.
- Método: reproducir → leer → aislar → observar → arreglar → comprobar.