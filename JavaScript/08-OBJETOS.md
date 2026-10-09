# 08 - Objetos

> [!info] ¿Qué es un objeto?
> Un objeto es una **colección de datos con nombre**. Cada dato se guarda como un par **clave: valor**. Sirve para agrupar información relacionada en una sola variable, como los datos de una persona, un producto o una configuración.

---

## 1. Antes de empezar

Conviene haber visto [[02 - Variables y tipos]], [[06 - Funciones]] y [[07 - Arrays]].

> [!tip] Si vienes de Python
> Un objeto se parece mucho a un **diccionario de Python**: guarda pares clave-valor. La diferencia principal es que un objeto también puede tener **funciones** dentro (métodos). Hay una comparación más abajo.

---

## 2. Concepto fundamental

Un array guarda valores por **posición** (0, 1, 2...). Un objeto guarda valores por **nombre**.

```js
const persona = {
  nombre: "Ana",
  edad: 20,
  esEstudiante: true
};
```

- Cada par se llama **propiedad**.
- La parte izquierda es la **clave** (el nombre).
- La parte derecha es el **valor** (puede ser de cualquier tipo, incluso otro objeto, un array o una función).
- Si el valor es una función, la propiedad se llama **método**.

Los objetos son un **tipo de referencia**: al copiarlos, ambas variables apuntan al mismo objeto (ver [[02 - Variables y tipos]]).

---

## 3. Sintaxis / estructura

### Crear un objeto

```js
const coche = {
  marca: "Seat",
  modelo: "Ibiza",
  año: 2020
};

const vacio = {};
```

### Leer una propiedad

```js
coche.marca;        // "Seat"  (notación de punto)
coche["modelo"];    // "Ibiza" (notación de corchetes)
```

### Cuándo usar corchetes

- Cuando la clave está en una **variable**.
- Cuando la clave tiene **espacios o caracteres raros**.

```js
const campo = "marca";
coche[campo];                // "Seat"

const otro = { "primer nombre": "Ana" };
otro["primer nombre"];       // "Ana"
```

### Modificar, añadir y borrar

```js
coche.año = 2022;          // modificar
coche.color = "rojo";      // añadir
delete coche.modelo;       // borrar
```

> [!tip] `const` y objetos
> `const` impide reasignar la variable, pero **sí puedes modificar las propiedades** del objeto.

---

## 4. Elementos / propiedades / características

### Métodos

Una función guardada dentro de un objeto.

```js
const persona = {
  nombre: "Ana",
  saludar() {
    return `Hola, soy ${this.nombre}`;
  }
};

persona.saludar();   // "Hola, soy Ana"
```

### `this`

Dentro de un método, `this` se refiere **al objeto que lo llama**. Gracias a eso, el método puede acceder a las demás propiedades.

> [!warning] `this` y funciones flecha
> Las funciones flecha **no tienen su propio `this`**, así que no son buena idea como métodos de un objeto (ver [[06 - Funciones]]).

### Objetos anidados

```js
const usuario = {
  nombre: "Luis",
  direccion: {
    ciudad: "Granada",
    cp: "18001"
  }
};

usuario.direccion.ciudad;    // "Granada"
```

### Encadenamiento opcional `?.`

Evita errores si algo no existe (ver [[03 - Operadores]]).

```js
usuario.trabajo?.empresa;    // undefined, sin error
```

### Propiedad abreviada

Si la variable tiene el mismo nombre que la clave:

```js
const nombre = "Ana";
const edad = 20;
const persona = { nombre, edad };   // equivale a { nombre: nombre, edad: edad }
```

### Claves calculadas

```js
const campo = "color";
const objeto = { [campo]: "azul" };   // { color: "azul" }
```

### Comprobar si existe una propiedad

```js
"marca" in coche;               // true
coche.hasOwnProperty("marca");  // true
coche.marca !== undefined;      // true
```

### Recorrer un objeto

```js
for (const clave in coche) {
  console.log(clave, coche[clave]);
}
```

Más moderno, con los métodos de `Object`:

| Método | Devuelve |
|--------|----------|
| `Object.keys(obj)` | Array con las **claves** |
| `Object.values(obj)` | Array con los **valores** |
| `Object.entries(obj)` | Array de pares `[clave, valor]` |

```js
for (const [clave, valor] of Object.entries(coche)) {
  console.log(clave, valor);
}
```

### Desestructuración

Sacar propiedades a variables sueltas.

```js
const { nombre, edad } = persona;

const { nombre: n, edad: e = 18 } = persona;   // renombrar y valor por defecto
```

### Spread `...`

Copia y combina objetos.

```js
const copia = { ...persona };
const nuevo = { ...persona, edad: 21 };   // copia cambiando una propiedad
```

### Copiar y fusionar

```js
Object.assign({}, a, b);   // forma antigua de fusionar
{ ...a, ...b };            // forma moderna
```

### Congelar un objeto

```js
const config = Object.freeze({ modo: "oscuro" });
config.modo = "claro";   // se ignora (o da error en modo estricto)
```

### Getters y setters

Propiedades que se calculan al leerlas o al escribirlas.

```js
const persona = {
  nombre: "Ana",
  apellido: "Pérez",
  get nombreCompleto() {
    return `${this.nombre} ${this.apellido}`;
  }
};

persona.nombreCompleto;   // "Ana Pérez"
```

### Convertir a JSON

Los objetos se convierten a texto con `JSON.stringify` y de vuelta con `JSON.parse`. Se ve en [[17 - JSON]].

---

## 5. Ejemplos prácticos

### Ejemplo básico

```js
const libro = {
  titulo: "Don Quijote",
  autor: "Cervantes",
  paginas: 900
};

console.log(libro.titulo);
libro.paginas = 950;
```

### Ejemplo habitual: array de objetos

Es la estructura más común al trabajar con datos de APIs.

```js
const usuarios = [
  { nombre: "Ana", edad: 20 },
  { nombre: "Luis", edad: 17 }
];

const mayores = usuarios.filter(u => u.edad >= 18);
const nombres = usuarios.map(u => u.nombre);
```

### Ejemplo: función que recibe un objeto

```js
function presentar({ nombre, edad = 18 }) {
  return `${nombre} tiene ${edad} años`;
}

presentar({ nombre: "Ana" });   // "Ana tiene 18 años"
```

---

## 6. Buenas prácticas

- **Usa `const`** para los objetos.
- **Usa nombres de clave claros** y en camelCase (`nombreCompleto`).
- **Prefiere la notación de punto**; usa corchetes solo si hace falta.
- **Usa `?.`** al acceder a datos que pueden no existir.
- **Usa desestructuración** para sacar varias propiedades de golpe.
- **Usa spread** para copiar o actualizar sin modificar el original.
- **No modifiques objetos que recibes como parámetro**; copia primero.
- **Evita anidar demasiados niveles**: se vuelve difícil de leer.
- **Usa `Object.entries()`** en lugar de `for...in` cuando quieras clave y valor.
- **Usa `Map`** cuando necesites claves que no sean texto o muchas altas y bajas.

---

## 7. Diferencias importantes

### Objeto de JavaScript vs diccionario de Python

| | Objeto (JavaScript) | Diccionario (Python) |
|---|--------------------|----------------------|
| Para qué se usa | Datos **y** comportamiento | Solo datos |
| Claves permitidas | Texto o `Symbol` | Cualquier valor inmutable |
| Acceso | `obj.clave` o `obj["clave"]` | `dic["clave"]` |
| Clave inexistente | Devuelve `undefined` | Da `KeyError` (salvo con `.get()`) |
| Puede tener funciones | Sí (métodos y `this`) | Se pueden guardar, pero no hay `this` |
| Equivalente exacto | `Map` | `dict` |

### Objeto vs array

- **Array**: valores ordenados por posición.
- **Objeto**: valores con nombre; el orden no es lo importante.

### Objeto vs `Map`

| | Objeto | `Map` |
|---|--------|-------|
| Claves | Texto o `Symbol` | Cualquier tipo |
| Tamaño | Hay que contarlo a mano | `map.size` |
| Orden | Casi siempre el de inserción | Siempre el de inserción |
| Uso típico | Datos estructurados | Diccionario dinámico |

### Copiar vs asignar

```js
const a = { x: 1 };
const b = a;           // MISMO objeto
const c = { ...a };    // COPIA independiente
```

---

## 8. Casos especiales

### Copia superficial

El spread y `Object.assign` solo copian el primer nivel. Los objetos anidados se siguen compartiendo. Para copia profunda:

```js
const copia = structuredClone(original);
```

### Comparar objetos

Se comparan por referencia, no por contenido:

```js
{ a: 1 } === { a: 1 };   // false
```

### Orden de las claves

Las claves numéricas se ordenan primero de menor a mayor; las de texto, en el orden en que se crearon.

### Claves repetidas

Si repites una clave, **gana la última**.

### Propiedades que no existen

Leer una propiedad inexistente devuelve `undefined`, no da error. Pero acceder a una propiedad **de** `undefined` sí da error (por eso se usa `?.`).

### Objeto vacío

```js
Object.keys(obj).length === 0;   // true si está vacío
```

### Perder el `this`

Si sacas un método del objeto y lo ejecutas suelto, `this` deja de apuntar al objeto:

```js
const f = persona.saludar;
f();   // this ya no es persona
```

### Prototipos y clases

Los objetos heredan de otros mediante **prototipos**. Las `class` de JavaScript son una forma más cómoda de usarlos. Es un tema más avanzado.

---

## 9. Resumen

- Un **objeto** agrupa datos como pares **clave: valor**; se crea con `{ }`.
- Se accede con **punto** (`obj.clave`) o **corchetes** (`obj["clave"]`).
- Se puede **añadir**, **modificar** y **borrar** (`delete`) propiedades, aunque sea `const`.
- Un **método** es una función dentro de un objeto; dentro se usa **`this`**.
- `Object.keys`, `values` y `entries` convierten un objeto en arrays para recorrerlo.
- La **desestructuración** `{ a, b } = obj` y el **spread** `{ ...obj }` son muy usados.
- `?.` evita errores con propiedades que pueden no existir.
- Se copian por **referencia**; usa `{ ...obj }` para una copia real.
- Se parece a un **diccionario de Python**, pero puede tener métodos y las claves son texto.
- Un **array de objetos** es la estructura típica de los datos reales.

---

⬅️ [[07 - Arrays]] | ➡️ [[09 - DOM]]