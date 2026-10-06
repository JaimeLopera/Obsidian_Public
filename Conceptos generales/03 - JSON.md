# 03 - JSON

> [!info] ¿Qué es?
> **JSON** (*JavaScript Object Notation*) es un **formato de texto** para guardar y enviar datos de forma ordenada. Es fácil de leer para las personas y fácil de procesar para los programas, por eso es el formato más usado para intercambiar datos entre aplicaciones y APIs.

---

## 1. Antes de empezar

- JSON es **solo texto**. No es un lenguaje de programación ni ejecuta nada: únicamente describe datos.
- Nació a partir de la sintaxis de los objetos de JavaScript, pero hoy lo usan **todos los lenguajes** (Python, PHP, Java, Node.js…).
- En esta nota se explica **el formato en sí**. Cómo usarlo desde JavaScript (`JSON.parse()` y `JSON.stringify()`) está en [[17 - JSON]].
- Para ver cómo viaja por la red, repasa [[01 - HTTP]] y [[02 - APIs REST]].

---

## 2. Concepto fundamental

### La idea

Dos programas distintos necesitan intercambiar información. Para entenderse, acuerdan un **formato común**, y ese formato es JSON.

```text
Programa A (JavaScript)  ──►  texto JSON  ──►  Programa B (Python)
```

> [!example] Analogía
> JSON es como un **formulario con casillas**: cada casilla tiene un nombre (la clave) y un contenido (el valor). Da igual quién lo rellene o quién lo lea, todos entienden dónde está cada dato.

### Las dos estructuras básicas

Todo JSON se construye con solo dos piezas:

| Estructura | Símbolo | Qué es | Ejemplo |
|---|---|---|---|
| **Objeto** | `{ }` | Conjunto de pares `clave: valor` | `{"nombre": "Ana"}` |
| **Array** | `[ ]` | Lista ordenada de valores | `[1, 2, 3]` |

### Serializar y deserializar

| Término | Qué significa | Dirección |
|---|---|---|
| **Serializar** | Convertir datos del programa a texto JSON | Programa → JSON |
| **Deserializar** (*parsear*) | Convertir texto JSON a datos del programa | JSON → Programa |

---

## 3. Sintaxis / estructura

### 3.1 Un objeto JSON

```json
{
  "nombre": "Ana",
  "edad": 25,
  "activa": true
}
```

- Va entre **llaves** `{ }`.
- Cada dato es un par **`"clave": valor`**.
- Los pares se separan con **comas**.
- Después de la última pareja **no** va coma.

### 3.2 Un array JSON

```json
["rojo", "verde", "azul"]
```

- Va entre **corchetes** `[ ]`.
- Los valores se separan con **comas**.
- El **orden importa**: cada valor tiene una posición (0, 1, 2…).

### 3.3 Las reglas obligatorias

| Regla | ✅ Correcto | ❌ Incorrecto |
|---|---|---|
| Las claves van **entre comillas dobles** | `{"nombre": "Ana"}` | `{nombre: "Ana"}` |
| Los textos usan **comillas dobles** | `"hola"` | `'hola'` |
| **Sin coma final** | `[1, 2, 3]` | `[1, 2, 3,]` |
| **Sin comentarios** | — | `// comentario` · `/* … */` |
| Los valores permitidos son solo **seis tipos** | `null` | `undefined` |

> [!tip] Regla de oro
> JSON es **mucho más estricto** que un objeto de JavaScript. Si algo falla al leerlo, casi siempre es una comilla o una coma.

---

## 4. Elementos / propiedades / características

### 4.1 Los seis tipos de valor

| Tipo | Ejemplo | Notas |
|---|---|---|
| **String** (texto) | `"Hola"` | Siempre con comillas dobles |
| **Number** (número) | `42` · `3.14` · `-7` · `1.5e3` | Sin comillas; sin ceros a la izquierda |
| **Boolean** (booleano) | `true` · `false` | En minúsculas y sin comillas |
| **null** (vacío) | `null` | Significa "no hay valor" |
| **Object** (objeto) | `{"a": 1}` | Puede contener cualquier valor |
| **Array** (lista) | `[1, "a", true]` | Puede mezclar tipos |

**Lo que NO existe en JSON:**

| No existe | Qué hacer |
|---|---|
| `undefined` | Omitir la clave o usar `null` |
| Funciones | No se pueden guardar |
| Fechas | Guardarlas como texto (ver sección 8) |
| `NaN`, `Infinity` | No son válidos; usar `null` o texto |
| Comentarios | No se permiten |
| Números en hexadecimal (`0xFF`) | Escribirlos en decimal |

### 4.2 Strings y caracteres especiales

Algunos caracteres se escriben con una **barra invertida** (`\`):

| Escritura | Significa |
|---|---|
| `\"` | Comillas dobles |
| `\\` | Barra invertida |
| `\n` | Salto de línea |
| `\t` | Tabulador |
| `\r` | Retorno de carro |
| `\/` | Barra normal (opcional) |
| `\uXXXX` | Carácter Unicode (ej. `\u00f1` = ñ) |

```json
{
  "cita": "Ella dijo: \"hola\"",
  "ruta": "C:\\Usuarios\\Ana",
  "poema": "Línea 1\nLínea 2"
}
```

> [!note] Un string no puede tener saltos de línea reales
> Si quieres un salto de línea dentro del texto, usa `\n`.

### 4.3 Anidamiento

Los objetos y arrays pueden **contenerse unos a otros** tantas veces como haga falta.

```json
{
  "id": 1,
  "autor": {
    "nombre": "Ana",
    "contacto": { "email": "ana@ejemplo.com" }
  },
  "etiquetas": ["html", "css"],
  "comentarios": [
    { "usuario": "Luis", "texto": "Genial" },
    { "usuario": "Eva", "texto": "Gracias" }
  ]
}
```

### 4.4 Cómo "leer" un camino dentro de un JSON

Para llegar a un dato se va entrando nivel a nivel:

| Dato que quiero | Camino (estilo JavaScript) | Resultado |
|---|---|---|
| Nombre del autor | `datos.autor.nombre` | `"Ana"` |
| Email del autor | `datos.autor.contacto.email` | `"ana@ejemplo.com"` |
| Primera etiqueta | `datos.etiquetas[0]` | `"html"` |
| Texto del 2.º comentario | `datos.comentarios[1].texto` | `"Gracias"` |

### 4.5 Tipo MIME y extensión

| Elemento | Valor |
|---|---|
| Extensión de archivo | `.json` |
| Cabecera HTTP `Content-Type` | `application/json` |
| Codificación | **UTF-8** |
| Primer carácter de un JSON válido | `{` o `[` (o un valor simple) |

### 4.6 Dónde se usa JSON

| Uso | Ejemplo |
|---|---|
| **APIs web** | Respuestas de una API REST |
| **Archivos de configuración** | `package.json`, `tsconfig.json`, `composer.json` |
| **Guardar datos en el navegador** | `localStorage` (guarda texto, por eso se convierte a JSON) |
| **Intercambio entre servicios** | Un servidor Node.js con otro en Python |
| **Bases de datos** | Columnas JSON en SQL, bases de datos documentales |
| **Archivos de datos** | Listas de productos, traducciones, ajustes de una app |

---

## 5. Ejemplos prácticos

### Ejemplo básico: un objeto sencillo

```json
{
  "producto": "Cuaderno",
  "precio": 3.5,
  "enStock": true,
  "descuento": null
}
```

**Qué contiene:** un texto, un número, un booleano y un `null`.

---

### Ejemplo habitual: una lista de objetos

Es la forma más común de recibir datos de una API.

```json
[
  { "id": 1, "nombre": "Ana",  "rol": "admin" },
  { "id": 2, "nombre": "Luis", "rol": "editor" },
  { "id": 3, "nombre": "Eva",  "rol": "lector" }
]
```

**Cómo se lee:** un array con tres objetos, todos con las mismas claves.

---

### Ejemplo completo: una respuesta de API real

```json
{
  "pagina": 1,
  "limite": 2,
  "total": 134,
  "datos": [
    {
      "id": 21,
      "titulo": "Aprender JSON",
      "publicado": true,
      "fecha": "2026-10-02T09:30:00Z",
      "autor": { "id": 7, "nombre": "Ana" },
      "etiquetas": ["datos", "web"],
      "valoracion": null
    },
    {
      "id": 22,
      "titulo": "Aprender REST",
      "publicado": false,
      "fecha": "2026-10-03T12:00:00Z",
      "autor": { "id": 8, "nombre": "Luis" },
      "etiquetas": [],
      "valoracion": 4.5
    }
  ]
}
```

**Qué hace bien:** usa claves coherentes, anida objetos y arrays, guarda la fecha como texto y usa `null` para "sin valor" y `[]` para "lista vacía".

---

### Ejemplo de corrección: errores típicos

❌ **Qué está mal**
```text
{
  nombre: 'Ana',
  edad: 25,
  // usuario activo
  activa: true,
}
```

🤔 **Por qué ocurre**
Es la sintaxis de un objeto de JavaScript, no de JSON. JSON exige comillas dobles en claves y textos, y no admite comentarios ni coma final.

✅ **Cómo se corrige**
```json
{
  "nombre": "Ana",
  "edad": 25,
  "activa": true
}
```

---

### Ejemplo de uso rápido en JavaScript

```javascript
const texto = '{"nombre":"Ana","edad":25}';

const objeto = JSON.parse(texto);        // texto → objeto
console.log(objeto.nombre);              // "Ana"

const devuelta = JSON.stringify(objeto); // objeto → texto
console.log(devuelta);                   // '{"nombre":"Ana","edad":25}'
```

Detalle completo en [[17 - JSON]].

---

## 6. Buenas prácticas

- **Valida siempre** el JSON antes de usarlo (un validador online, tu editor o el propio `JSON.parse` dentro de un `try/catch`).
- **Usa nombres de clave claros y coherentes** en todo el documento.
- **Elige un estilo de nombres y mantenlo:** `camelCase` (`nombreUsuario`) o `snake_case` (`nombre_usuario`), sin mezclar.
- **Usa el tipo correcto:** `25` (número) y no `"25"` (texto); `true` y no `"true"`.
- **Usa `null`** cuando una clave exista pero no tenga valor; **`[]`** para listas vacías.
- **Fechas en formato ISO 8601** como texto: `"2026-10-02T09:30:00Z"`.
- **Mantén las listas homogéneas:** que todos los elementos de un array tengan la misma estructura.
- **No anides sin necesidad:** más de 3 o 4 niveles se vuelve difícil de leer.
- **Indenta** los archivos JSON (2 espacios es lo habitual) para que se lean bien.
- **No guardes datos sensibles** (contraseñas, claves secretas) en archivos JSON que se compartan o suban a un repositorio.
- **No construyas JSON uniendo textos a mano**; usa las funciones del lenguaje (`JSON.stringify`, `json_encode`, `json.dumps`).
- **Indica `Content-Type: application/json`** al enviar JSON por HTTP.

---

## 7. Diferencias importantes

### JSON vs objeto de JavaScript

| | JSON | Objeto de JavaScript |
|---|---|---|
| Qué es | **Texto** | Estructura **en memoria** |
| Claves | Siempre con `"comillas dobles"` | Con o sin comillas |
| Textos | Solo `"comillas dobles"` | `'simples'`, `"dobles"` o `` `backticks` `` |
| Comentarios | No | Sí |
| Coma final | No | Sí |
| Funciones | No | Sí |
| `undefined` | No | Sí |

```javascript
// Objeto de JavaScript (válido en JS, NO es JSON)
const usuario = { nombre: 'Ana', saludar() { return "hola"; } };

// JSON (texto)
const json = '{"nombre": "Ana"}';
```

### JSON vs XML

| | JSON | XML |
|---|---|---|
| Aspecto | `{"nombre": "Ana"}` | `<nombre>Ana</nombre>` |
| Tamaño | Más compacto | Más largo |
| Legibilidad | Muy fácil | Más cargado |
| Tipos de datos | Número, booleano, null… | Todo es texto |
| Uso actual | Muy extendido en la web | Sistemas antiguos, documentos |

### JSON vs YAML

| | JSON | YAML |
|---|---|---|
| Sintaxis | Llaves, corchetes y comillas | Sangría, sin llaves ni comillas |
| Comentarios | No | Sí |
| Uso típico | Intercambio de datos | Archivos de configuración (Docker Compose) |

```yaml
nombre: Ana
edad: 25
```

### JSON vs JSON5 vs JSONC

| | JSON | JSON5 / JSONC |
|---|---|---|
| Estándar | Sí, estricto | Extensiones no estándar |
| Comentarios | No | Sí |
| Coma final | No | Sí |
| Dónde se ve | En todas partes | Configuración (ej. `tsconfig.json` admite comentarios) |

### Valor `null` vs clave ausente

| | `"valoracion": null` | (sin la clave) |
|---|---|---|
| Significado | "Existe, pero no tiene valor" | "No se ha informado" |

### Número vs texto

| | `25` | `"25"` |
|---|---|---|
| Tipo | Número | Texto |
| Se puede sumar | Sí | No (se concatena) |

---

## 8. Casos especiales

### Fechas

JSON **no tiene un tipo fecha**. Se guardan como texto, preferiblemente en formato ISO 8601:

```json
{ "creado": "2026-10-02T09:30:00Z" }
```

Al leerlas hay que convertirlas manualmente (por ejemplo, con `new Date()` en JavaScript).

### Números muy grandes o con muchos decimales

JSON no limita el tamaño, pero los lenguajes que lo leen sí. En JavaScript, los enteros por encima de `9007199254740991` pierden precisión. Para identificadores enormes o dinero exacto, se suele usar **texto**:

```json
{ "idLargo": "12345678901234567890", "precio": "19.99" }
```

### Valores que no existen en JSON

`NaN`, `Infinity` y `undefined` no son válidos. Si una función los genera, `JSON.stringify` los convierte en `null` o los descarta.

### JSON vacío o con un solo valor

Todos estos son JSON válidos:

```json
{}
[]
"hola"
42
true
null
```

### Claves repetidas

El estándar lo desaconseja, y cada lenguaje reacciona distinto (normalmente se queda con la última). **Evítalo.**

```json
{ "a": 1, "a": 2 }
```

### Archivos grandes: JSON Lines

Para muchos registros (logs, exportaciones), se usa **un objeto JSON por línea**, sin corchetes externos. Así se puede leer línea a línea sin cargar todo el archivo.

```text
{"id": 1, "nombre": "Ana"}
{"id": 2, "nombre": "Luis"}
{"id": 3, "nombre": "Eva"}
```

Extensión habitual: `.jsonl` o `.ndjson`.

### Validar la estructura: JSON Schema

Un **JSON Schema** describe cómo debe ser un JSON (qué claves, qué tipos, cuáles son obligatorias) y permite comprobar automáticamente si un documento es válido.

```json
{
  "type": "object",
  "properties": {
    "nombre": { "type": "string" },
    "edad":   { "type": "number", "minimum": 0 }
  },
  "required": ["nombre"]
}
```

### Herramientas útiles

| Herramienta | Para qué |
|---|---|
| Extensión o formateador del editor | Ordenar y comprobar JSON |
| `jq` (terminal) | Leer, filtrar y transformar JSON |
| Validadores online | Detectar errores de sintaxis |
| Pestaña **Network** del navegador | Ver el JSON real que devuelve una API |

```bash
# Mostrar un JSON formateado y filtrar un campo
curl -s https://api.ejemplo.com/usuarios/42 | jq '.nombre'
```

> [!warning] Obsoleto / legado
> - **`eval()` para leer JSON:** es inseguro porque ejecuta código. Usa `JSON.parse()`.
> - **JSONP:** técnica antigua para saltarse restricciones entre dominios. Hoy se usa **CORS** (ver [[01 - HTTP]]).
> - **Armar JSON con concatenación de textos:** propenso a errores. Usa las funciones del lenguaje.

---

## 9. Resumen

- **JSON es un formato de texto** para guardar e intercambiar datos; no ejecuta nada.
- Solo tiene **dos estructuras**: **objetos** `{ }` (clave: valor) y **arrays** `[ ]` (listas).
- Solo admite **seis tipos de valor**: string, number, boolean, null, object y array.
- **Reglas estrictas:** comillas dobles en claves y textos, sin coma final, sin comentarios.
- **No existen** `undefined`, funciones, fechas ni `NaN`; las fechas van como texto ISO 8601.
- Se puede **anidar** todo lo necesario; se accede a los datos por niveles.
- Se usa en **APIs, archivos de configuración, `localStorage`** y casi cualquier intercambio de datos.
- **Tipo MIME:** `application/json`, codificación **UTF-8**, extensión `.json`.
- **Serializar** = programa → JSON; **deserializar** = JSON → programa.
- Valida siempre, mantén nombres coherentes y no guardes datos sensibles.
- Para usarlo con código, ver [[17 - JSON]]; para su papel en las APIs, [[02 - APIs REST]].