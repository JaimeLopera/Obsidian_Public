# 17 - JSON

> [!info] ¿Qué es?
> **JSON** (*JavaScript Object Notation*) es un formato de **texto** para guardar e intercambiar datos. Se parece a un objeto de JavaScript, pero es solo texto, por eso sirve para enviar información entre programas, servidores y archivos.

---

## 1. Antes de empezar

Conviene conocer:

- Objetos y arrays ([[07 - Arrays]], [[08 - Objetos]]).
- Para ver cómo se pide JSON a un servidor: [[13 - Fetch y APIs]].
- Visión general fuera de JavaScript: [[03 - JSON]] en Conceptos generales.

**Idea clave:** JSON no es JavaScript, es un **formato de texto** que casi todos los lenguajes saben leer y escribir.

---

## 2. Concepto fundamental

Dos operaciones:

| Operación | Qué hace | Método |
|---|---|---|
| **Serializar** | Convierte un objeto de JS en texto JSON | `JSON.stringify()` |
| **Parsear** | Convierte texto JSON en un objeto de JS | `JSON.parse()` |

```
Objeto JS  ──stringify──►  Texto JSON  ──parse──►  Objeto JS
```

---

## 3. Sintaxis / estructura

### 3.1 Cómo es un JSON

```json
{
  "nombre": "Ana",
  "edad": 20,
  "activa": true,
  "direccion": null,
  "hobbies": ["leer", "correr"],
  "contacto": {
    "email": "ana@correo.com"
  }
}
```

### 3.2 Reglas estrictas

- Las **claves** van **siempre entre comillas dobles**.
- Los **textos** van entre comillas dobles (nunca simples).
- **No** se permiten comas sobrantes al final.
- **No** se permiten comentarios.
- **No** puede haber `undefined`, funciones ni `NaN`.

### 3.3 Tipos de valor permitidos

| Tipo | Ejemplo |
|---|---|
| Texto | `"hola"` |
| Número | `42`, `3.14` |
| Booleano | `true`, `false` |
| Nulo | `null` |
| Array | `[1, 2, 3]` |
| Objeto | `{ "a": 1 }` |

---

## 4. Elementos / propiedades / características

### 4.1 `JSON.stringify()`

```javascript
const persona = { nombre: "Ana", edad: 20 };

JSON.stringify(persona);
// '{"nombre":"Ana","edad":20}'
```

Con formato legible (sangría de 2 espacios):

```javascript
JSON.stringify(persona, null, 2);
```

**Segundo parámetro (filtro):** una lista de claves a incluir.

```javascript
JSON.stringify(persona, ["nombre"]);
// '{"nombre":"Ana"}'
```

### 4.2 `JSON.parse()`

```javascript
const texto = '{"nombre":"Ana","edad":20}';
const objeto = JSON.parse(texto);

console.log(objeto.nombre); // "Ana"
```

Si el texto no es un JSON válido, lanza un `SyntaxError`.

### 4.3 Qué pasa con cada valor al serializar

| Valor de JS | Resultado en JSON |
|---|---|
| `undefined`, funciones, `Symbol` | Se **omiten** (en un array, pasan a `null`) |
| `NaN`, `Infinity` | Se convierten en `null` |
| `Date` | Texto en formato ISO (usa su `toJSON`) |
| `Map`, `Set` | Quedan como `{}` (se pierden los datos) |

### 4.4 Parámetro `reviver` de `parse`

Una función que transforma cada valor al leer:

```javascript
const datos = JSON.parse('{"fecha":"2026-10-09T10:00:00.000Z"}', (clave, valor) => {
  return clave === "fecha" ? new Date(valor) : valor;
});
```

### 4.5 Método `toJSON`

Si un objeto tiene este método, `stringify` usa lo que devuelva:

```javascript
const usuario = {
  nombre: "Ana",
  password: "1234",
  toJSON() {
    return { nombre: this.nombre };
  }
};

JSON.stringify(usuario); // '{"nombre":"Ana"}'
```

### 4.6 Con `fetch`

```javascript
const respuesta = await fetch("https://api.ejemplo.com/usuarios");
const usuarios = await respuesta.json(); // equivale a parsear el texto

await fetch("https://api.ejemplo.com/usuarios", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({ nombre: "Ana" })
});
```

---

## 5. Ejemplos prácticos

### 5.1 Ejemplo básico: ida y vuelta

```javascript
const original = { producto: "Libro", precio: 12.5 };

const texto = JSON.stringify(original);
const copia = JSON.parse(texto);

console.log(copia.precio); // 12.5
```

### 5.2 Ejemplo habitual: leer JSON con seguridad

```javascript
function leerJSON(texto) {
  try {
    return JSON.parse(texto);
  } catch (error) {
    console.error("JSON no válido:", error.message);
    return null;
  }
}

leerJSON('{"ok": true}'); // { ok: true }
leerJSON("{ok: true}");   // null (faltan las comillas en la clave)
```

---

## 6. Buenas prácticas

- **Siempre `try...catch` con `JSON.parse`** si el texto viene de fuera ([[15 - Errores y debugging]]).
- **Comillas dobles** en claves y textos.
- **Usa `JSON.stringify(x, null, 2)`** para ver datos de forma legible al depurar.
- **No metas datos sensibles** en JSON que vaya a guardarse o enviarse sin protección.
- **Nombres de clave coherentes** (por ejemplo, siempre en `camelCase`).
- **Para copiar objetos**, prefiere `structuredClone()` en vez de `parse(stringify())`, que pierde fechas, `undefined`, etc.

---

## 7. Diferencias importantes

### 7.1 JSON vs. objeto de JavaScript

| | Objeto JS | JSON |
|---|---|---|
| Qué es | Valor en memoria | Texto |
| Claves | Con o sin comillas | Siempre con comillas dobles |
| Comillas de textos | Simples o dobles | Solo dobles |
| Funciones | Permitidas | No |
| Comentarios | Sí | No |
| Coma final | Permitida | No |

### 7.2 `JSON.stringify` vs. `toString`

`toString()` en un objeto devuelve `"[object Object]"`; `stringify` devuelve el contenido en JSON.

---

## 8. Casos especiales

### 8.1 Referencias circulares

Si un objeto se contiene a sí mismo, `stringify` lanza `TypeError`.

```javascript
const a = {};
a.yo = a;
JSON.stringify(a); // TypeError
```

### 8.2 Números muy grandes (`BigInt`)

`BigInt` no se puede serializar directamente y da error. Hay que convertirlo antes a texto.

### 8.3 `null` vs. clave ausente

`{ "direccion": null }` y `{}` no significan lo mismo: la primera dice "no tiene valor", la segunda "no existe la clave".

### 8.4 Fechas

JSON no tiene tipo fecha. Se guardan como texto y hay que reconvertirlas a mano (o con `reviver`).

### 8.5 Archivos `.json`

Es un archivo de texto con extensión `.json` (por ejemplo `package.json`). Ver [[03 - Módulos CommonJS y ESM]] para importarlo en Node.

---

## 9. Resumen

- **JSON** es un formato de **texto** para intercambiar datos, no es lo mismo que un objeto de JS.
- **`JSON.stringify()`**: objeto → texto. **`JSON.parse()`**: texto → objeto.
- Claves y textos con **comillas dobles**; sin comas finales ni comentarios.
- Tipos válidos: texto, número, booleano, `null`, array y objeto.
- `undefined`, funciones y símbolos se pierden al serializar; `Date` pasa a texto.
- `JSON.stringify(x, null, 2)` da formato legible.
- Usa `try...catch` al parsear datos externos.
- Con `fetch`: `respuesta.json()` para leer, `JSON.stringify` para enviar.
- Para clonar objetos, mejor `structuredClone()`.