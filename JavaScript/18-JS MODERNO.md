# 18 - JavaScript moderno

> [!info] ¿Qué es?
> Se llama **JavaScript moderno** a las características añadidas al lenguaje desde **ES6 (2015)** en adelante. Hacen el código más corto, claro y seguro. Esta nota reúne las más importantes que no tienen su propia nota.

---

## 1. Antes de empezar

Conviene conocer lo básico de [[02 - Variables y tipos]], [[06 - Funciones]], [[07 - Arrays]] y [[08 - Objetos]].

**Cómo funciona el lenguaje:** JavaScript se estandariza cada año con el nombre **ECMAScript (ES)**. Los navegadores actuales ya entienden casi todo lo moderno; para navegadores muy antiguos se usan herramientas como Babel.

---

## 2. Concepto fundamental

Lo moderno no cambia la idea del lenguaje: ofrece **atajos más claros** para cosas que antes eran largas o propensas a error.

> [!warning] Obsoleto / legado
> `var` y las funciones con `function` en todo lugar siguen funcionando, pero hoy se prefieren `let`/`const` y las funciones flecha. Las concatenaciones con `+` se sustituyen por plantillas de texto.

---

## 3. Sintaxis / estructura

### 3.1 Resumen de lo esencial

| Característica | Forma moderna |
|---|---|
| Variables | `let` y `const` |
| Funciones cortas | `(a, b) => a + b` |
| Texto con variables | `` `Hola ${nombre}` `` |
| Sacar valores | `const { a, b } = objeto` |
| Copiar/unir | `{ ...objeto }`, `[...array]` |
| Valores por defecto | `function f(x = 10) {}` |
| Acceso seguro | `objeto?.propiedad` |
| Valor por defecto seguro | `valor ?? "defecto"` |
| Módulos | `import` / `export` |

---

## 4. Elementos / propiedades / características

### 4.1 Plantillas de texto

```javascript
const nombre = "Ana";
const mensaje = `Hola, ${nombre}. Tienes ${2 + 3} avisos.`;
```

También permiten varias líneas sin trucos.

### 4.2 Desestructuración

Sacar partes de objetos y arrays en variables:

```javascript
const usuario = { nombre: "Ana", edad: 20, ciudad: "Madrid" };
const { nombre, edad } = usuario;

const [primero, segundo] = [10, 20, 30];
```

Con valor por defecto y renombrado:

```javascript
const { ciudad: lugar = "Desconocida" } = usuario;
```

### 4.3 Operador *spread* (`...`) y *rest*

**Spread:** "desparrama" los elementos.

```javascript
const a = [1, 2];
const b = [...a, 3, 4];            // [1, 2, 3, 4]

const base = { color: "rojo" };
const completo = { ...base, tamano: "L" };
```

**Rest:** "recoge" el resto.

```javascript
function sumar(...numeros) {
  return numeros.reduce((total, n) => total + n, 0);
}
```

### 4.4 Encadenamiento opcional `?.`

Si algo en la cadena es `null` o `undefined`, devuelve `undefined` en vez de dar error:

```javascript
const calle = usuario?.direccion?.calle;
const primero = lista?.[0];
const resultado = objeto.metodo?.();
```

### 4.5 Fusión nula `??` y asignaciones lógicas

`??` usa el valor de la derecha **solo** si la izquierda es `null` o `undefined`:

```javascript
const cantidad = 0;
cantidad || 10;   // 10  (0 cuenta como "falso")
cantidad ?? 10;   // 0   (0 sí es un valor válido)
```

Asignaciones abreviadas:

```javascript
a ||= 5;   // asigna si a es "falso"
a &&= 5;   // asigna si a es "verdadero"
a ??= 5;   // asigna si a es null o undefined
```

### 4.6 Métodos modernos de arrays y objetos

| Método | Qué hace |
|---|---|
| `array.at(-1)` | Último elemento |
| `array.flat()` / `flatMap()` | Aplana arrays anidados |
| `array.includes(x)` | ¿Contiene `x`? |
| `array.findLast()` | Último que cumple una condición |
| `array.toSorted()`, `toReversed()` | Ordenar o invertir **sin modificar** el original |
| `Object.entries(obj)` | Pares `[clave, valor]` |
| `Object.fromEntries(pares)` | Pares → objeto |
| `Object.groupBy(lista, fn)` | Agrupa elementos por criterio |
| `structuredClone(obj)` | Copia profunda real |

### 4.7 Abreviaturas en objetos

```javascript
const nombre = "Ana";
const edad = 20;

const persona = {
  nombre,                 // igual que nombre: nombre
  edad,
  saludar() {             // método sin la palabra function
    return `Soy ${this.nombre}`;
  },
  ["id_" + edad]: true    // clave calculada
};
```

### 4.8 Clases y campos privados

```javascript
class Cuenta {
  #saldo = 0;                    // privado: solo accesible dentro de la clase

  ingresar(cantidad) {
    this.#saldo += cantidad;
  }

  get saldo() {
    return this.#saldo;
  }
}
```

### 4.9 Otras novedades útiles

- **Separadores numéricos:** `1_000_000`.
- **`BigInt`:** enteros enormes, `123n`.
- **`Symbol`:** identificadores únicos.
- **`Map` y `Set`:** colecciones con claves de cualquier tipo y sin duplicados.
- **`for...of`:** recorrer valores de arrays, textos, `Map`, `Set`.
- **`String.prototype.replaceAll()`, `padStart()`, `trim()`** y similares.
- **`Promise.all`, `allSettled`, `any`:** combinar promesas ([[12 - Asincronía]]).

---

## 5. Ejemplos prácticos

### 5.1 Ejemplo básico

```javascript
const producto = { nombre: "Libro", precio: 12 };
const { nombre, precio } = producto;

console.log(`${nombre} cuesta ${precio} €`);
```

### 5.2 Ejemplo habitual: actualizar sin modificar el original

```javascript
const estado = { usuario: "Ana", tareas: ["estudiar"] };

const nuevoEstado = {
  ...estado,
  tareas: [...estado.tareas, "repasar"]
};
```

`estado` queda intacto y `nuevoEstado` es una versión nueva. Es un patrón muy usado en aplicaciones web.

---

## 6. Buenas prácticas

- **`const` por defecto**, `let` solo si vas a reasignar, **nunca `var`**.
- **Funciones flecha** para callbacks cortos; `function` normal cuando necesites tu propio `this`.
- **Plantillas de texto** en lugar de concatenar con `+`.
- **`??` en vez de `||`** cuando valores como `0` o `""` son válidos.
- **`?.` con cabeza**: úsalo donde el dato pueda faltar de verdad, no en todas partes (esconde errores reales).
- **Prefiere métodos que no modifican** (`toSorted`, spread) para evitar efectos inesperados.
- **No abuses de la desestructuración** anidada: si cuesta leerla, divídela.
- **Usa módulos** para organizar el código ([[14 - Módulos]]).

---

## 7. Diferencias importantes

### 7.1 `||` vs. `??`

| Valor izquierdo | `valor \|\| "x"` | `valor ?? "x"` |
|---|---|---|
| `0` | `"x"` | `0` |
| `""` | `"x"` | `""` |
| `false` | `"x"` | `false` |
| `null` / `undefined` | `"x"` | `"x"` |

### 7.2 Spread vs. rest

Se escriben igual (`...`) pero hacen lo contrario: **spread** expande, **rest** agrupa. Depende de dónde lo uses (al llamar/construir vs. al declarar parámetros o desestructurar).

### 7.3 Copia superficial vs. profunda

| | Spread / `Object.assign` | `structuredClone` |
|---|---|---|
| Nivel de copia | Solo el primer nivel | Todos los niveles |
| Objetos internos | Se **comparten** | Se copian |

---

## 8. Casos especiales

### 8.1 Funciones flecha y `this`

Las flechas **no tienen su propio `this`**: usan el del lugar donde se crearon. Por eso no son buena opción como métodos de un objeto, ni como constructores.

### 8.2 Desestructuración de parámetros

```javascript
function mostrar({ nombre, edad = 18 }) {
  return `${nombre} (${edad})`;
}
```

### 8.3 Compatibilidad

Algunas novedades muy recientes (`Object.groupBy`, `toSorted`) pueden faltar en navegadores antiguos. Compruébalo en **MDN** (sección "Compatibilidad") o en *Can I use*.

### 8.4 Orden de las propiedades al unir con spread

Si dos objetos tienen la misma clave, **gana el último**:

```javascript
const resultado = { ...{ a: 1 }, ...{ a: 2 } }; // { a: 2 }
```

### 8.5 Indicadores de versión

Los nombres `ES6`, `ES2015`, `ES2020`... se refieren al mismo sistema por años. Lo importante es saber qué función existe, no memorizar en qué año salió.

---

## 9. Resumen

- JavaScript moderno = mejoras de **ES6 (2015)** en adelante, para escribir menos y más claro.
- **`let`/`const`**, flechas y **plantillas de texto** sustituyen a `var`, `function` y `+`.
- **Desestructuración** saca valores; **spread** copia/une; **rest** recoge el resto.
- **`?.`** evita errores al acceder a datos que pueden faltar; **`??`** da un valor por defecto solo si es `null` o `undefined`.
- Métodos útiles: `at`, `flat`, `includes`, `toSorted`, `Object.entries`, `structuredClone`.
- Clases con **campos privados** (`#`), y colecciones `Map` y `Set`.
- Las flechas no tienen su propio `this`.
- Spread hace copia **superficial**; `structuredClone` hace copia **profunda**.
- Comprueba la compatibilidad de lo muy reciente.