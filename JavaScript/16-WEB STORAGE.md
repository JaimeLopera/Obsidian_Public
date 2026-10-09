# 16 - Web Storage

> [!info] ¿Qué es?
> **Web Storage** es un pequeño almacén dentro del navegador donde puedes guardar datos en forma de texto (clave → valor) para recuperarlos después, **sin necesidad de servidor**. Existen dos: `localStorage` y `sessionStorage`.

---

## 1. Antes de empezar

Conviene conocer:

- Objetos y cadenas de texto ([[08 - Objetos]]).
- JSON, porque Web Storage solo guarda texto y hay que convertir objetos ([[17 - JSON]]).

**Para qué se usa:** recordar el tema oscuro/claro, el contenido de un carrito, un borrador de formulario, el idioma elegido...

---

## 2. Concepto fundamental

Web Storage guarda pares **clave : valor**, y **ambos son siempre texto**.

| | `localStorage` | `sessionStorage` |
|---|---|---|
| Duración | **Permanente** hasta que se borre | Solo mientras dure la **pestaña** |
| Al cerrar el navegador | Se mantiene | Se borra |
| Compartido entre pestañas | Sí (mismo sitio) | No, cada pestaña tiene el suyo |
| Capacidad aproximada | 5 MB | 5 MB |

Los datos están ligados al **origen** (protocolo + dominio + puerto). Una web no puede leer los datos de otra.

---

## 3. Sintaxis / estructura

Los dos tienen los mismos métodos. Aquí con `localStorage`:

```javascript
localStorage.setItem("tema", "oscuro");      // guardar
const tema = localStorage.getItem("tema");   // leer → "oscuro"
localStorage.removeItem("tema");             // borrar una clave
localStorage.clear();                        // borrar todo
```

---

## 4. Elementos / propiedades / características

### 4.1 Métodos y propiedades

| Miembro | Qué hace |
|---|---|
| `setItem(clave, valor)` | Guarda o sobrescribe un valor |
| `getItem(clave)` | Devuelve el valor, o `null` si no existe |
| `removeItem(clave)` | Elimina esa clave |
| `clear()` | Vacía todo el almacén de ese sitio |
| `key(indice)` | Devuelve el nombre de la clave en esa posición |
| `length` | Número de elementos guardados |

### 4.2 Solo guarda texto

Si guardas un número o un objeto, se convierte en texto:

```javascript
localStorage.setItem("edad", 20);
typeof localStorage.getItem("edad"); // "string"

localStorage.setItem("usuario", { nombre: "Ana" });
localStorage.getItem("usuario"); // "[object Object]"  ← inútil
```

### 4.3 Guardar objetos y arrays con JSON

```javascript
const usuario = { nombre: "Ana", edad: 20 };

localStorage.setItem("usuario", JSON.stringify(usuario));

const recuperado = JSON.parse(localStorage.getItem("usuario"));
console.log(recuperado.nombre); // "Ana"
```

### 4.4 Recorrer todo lo guardado

```javascript
for (let i = 0; i < localStorage.length; i++) {
  const clave = localStorage.key(i);
  console.log(clave, localStorage.getItem(clave));
}
```

### 4.5 Ver los datos en el navegador

DevTools (**F12**) → pestaña **Application** → **Local Storage** / **Session Storage**. Desde ahí puedes ver, editar y borrar.

---

## 5. Ejemplos prácticos

### 5.1 Ejemplo básico: recordar el tema

```javascript
const botonTema = document.querySelector("#tema");

if (localStorage.getItem("tema") === "oscuro") {
  document.body.classList.add("oscuro");
}

botonTema.addEventListener("click", () => {
  const esOscuro = document.body.classList.toggle("oscuro");
  localStorage.setItem("tema", esOscuro ? "oscuro" : "claro");
});
```

### 5.2 Ejemplo habitual: lista de tareas persistente

```javascript
function cargarTareas() {
  const guardado = localStorage.getItem("tareas");
  return guardado ? JSON.parse(guardado) : [];
}

function guardarTareas(tareas) {
  localStorage.setItem("tareas", JSON.stringify(tareas));
}

const tareas = cargarTareas();
tareas.push({ texto: "Estudiar JavaScript", hecha: false });
guardarTareas(tareas);
```

---

## 6. Buenas prácticas

- **Usa `JSON.stringify` / `JSON.parse`** siempre que guardes algo que no sea texto simple.
- **Comprueba si existe** antes de usarlo: `getItem` devuelve `null` si no hay nada.
- **Pon un prefijo a las claves** (`"miApp_tema"`) para no chocar con otros datos.
- **Envuelve `JSON.parse` en `try...catch`**: si el texto guardado está corrupto, fallará ([[15 - Errores y debugging]]).
- **Guarda poco y solo lo necesario**: no es una base de datos.
- **Crea funciones auxiliares** (`cargar`, `guardar`) para no repetir código.
- **Usa `sessionStorage`** para datos temporales de una sola visita.

---

## 7. Diferencias importantes

### 7.1 Web Storage vs. cookies

| | Web Storage | Cookies |
|---|---|---|
| Capacidad | ~5 MB | ~4 KB |
| Se envían al servidor | **No** | **Sí**, en cada petición |
| Caducidad | Manual (`local`) o al cerrar pestaña (`session`) | Se define con `expires` / `max-age` |
| Acceso | Solo desde JavaScript | JavaScript y servidor |
| Uso típico | Preferencias y datos de la interfaz | Sesiones y autenticación |

### 7.2 Web Storage vs. IndexedDB

**IndexedDB** es una base de datos real del navegador: guarda mucho más, admite objetos y es asíncrona, pero es más compleja. Web Storage es simple y **síncrona** (bloquea mientras lee/escribe).

---

## 8. Casos especiales

### 8.1 No guardar datos sensibles

Cualquier script de la página puede leer `localStorage`. **Nunca** guardes contraseñas, tokens muy sensibles ni datos personales delicados.

### 8.2 Evento `storage`

Se dispara en **otras pestañas** del mismo sitio cuando cambia `localStorage` (no en la que hace el cambio):

```javascript
window.addEventListener("storage", (evento) => {
  console.log(evento.key, evento.oldValue, evento.newValue);
});
```

### 8.3 Cuota llena

Si superas el límite, `setItem` lanza un error (`QuotaExceededError`). Envuélvelo en `try...catch`.

### 8.4 Modo privado y bloqueos

En modo incógnito o con ciertos ajustes, el almacenamiento puede estar desactivado o borrarse al cerrar. No des por hecho que siempre funciona.

### 8.5 Acceso con notación de objeto

También funciona `localStorage.tema = "oscuro"`, pero **se recomienda usar los métodos**: evita choques con nombres propios del objeto.

---

## 9. Resumen

- **Web Storage** guarda pares clave-valor en el navegador, sin servidor.
- **`localStorage`**: permanente. **`sessionStorage`**: dura lo que la pestaña.
- Métodos: `setItem`, `getItem`, `removeItem`, `clear`, `key`, `length`.
- **Todo se guarda como texto**: usa `JSON.stringify` y `JSON.parse` para objetos y arrays.
- `getItem` devuelve `null` si no existe.
- Capacidad ~5 MB, ligado al origen, **no se envía al servidor**.
- No guardes datos sensibles; cualquier script de la página puede leerlos.
- Para cosas más grandes o complejas, IndexedDB; para sesiones, cookies.