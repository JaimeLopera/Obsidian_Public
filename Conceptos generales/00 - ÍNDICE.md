# 00 - Índice

> [!info] ¿Qué es?
> Carpeta de **conceptos transversales** del desarrollo web: temas que no pertenecen a un único lenguaje o herramienta, sino que se usan en casi todos. Se explican aquí una sola vez y el resto de carpetas los enlazan.

---

## 1. Antes de empezar

- No hace falta dominar ningún lenguaje para leer esta carpeta, pero sí ayuda haber visto [[01 - Fundamentos]] de HTML y los fundamentos de JavaScript.
- Si buscas algo concreto de un lenguaje (por ejemplo, `fetch()` o `JSON.parse()`), ve a su carpeta; aquí solo está la **base conceptual** común.

---

## 2. Contenido de la carpeta

| Nota | De qué trata |
|---|---|
| [[01 - HTTP]] | Cómo se comunican el navegador y el servidor: peticiones, respuestas, métodos, cabeceras y códigos de estado |
| [[02 - APIs REST]] | Cómo se diseñan y consumen las APIs: recursos, endpoints, verbos y formatos |
| [[03 - JSON]] | El formato de intercambio de datos: sintaxis, tipos y usos |
| [[04 - Regex]] | Expresiones regulares: patrones para buscar y validar texto |
| [[05 - Seguridad web]] | Principios generales para proteger aplicaciones y usuarios |
| [[06 - Cómo funciona internet]] | Visión global: cliente-servidor, DNS, direcciones IP y recorrido de una petición |
| [[07 - Referencia rápida]] | Chuleta de consulta de toda la carpeta |

---

## 3. Orden de lectura recomendado

Si es la primera vez que la recorres, este orden va de lo general a lo concreto:

1. [[06 - Cómo funciona internet]] → el panorama completo
2. [[01 - HTTP]] → el idioma en el que hablan cliente y servidor
3. [[03 - JSON]] → el formato en el que viajan los datos
4. [[02 - APIs REST]] → cómo se organizan las peticiones sobre esos datos
5. [[05 - Seguridad web]] → cómo proteger todo lo anterior
6. [[04 - Regex]] → herramienta independiente, se puede leer en cualquier momento

> [!tip] Consulta rápida
> Si solo necesitas recordar algo, empieza por [[07 - Referencia rápida]].

---

## 4. Dónde se aplica cada tema

| Tema | Se usa sobre todo en |
|---|---|
| HTTP | JavaScript (Fetch), Node.js, PHP, formularios HTML |
| APIs REST | JavaScript (Fetch y APIs), Node.js (Express), Python, PHP |
| JSON | JavaScript, TypeScript, Node.js, Python, PHP |
| Regex | JavaScript, Python, PHP, SQL, Terminal y Linux (`grep`, `sed`) |
| Seguridad web | HTML (formularios), PHP, SQL, Node.js, Docker |
| Cómo funciona internet | Cualquier tema que implique un servidor |

---

## 5. Notas relacionadas en otras carpetas

- [[00 - Ruta de aprendizaje]] → dónde encaja esta carpeta en el plan general
- [[17 - JSON]] (JavaScript) → uso práctico con `JSON.parse()` y `JSON.stringify()`
- [[13 - Fetch y APIs]] (JavaScript) → consumir APIs desde el navegador
- [[16 - Seguridad]] (PHP) → seguridad aplicada a PHP

---

## 6. Resumen

- Esta carpeta reúne lo que **sirve para todos los lenguajes**: HTTP, APIs REST, JSON, Regex, seguridad y funcionamiento de internet.
- Cada tema se explica **una sola vez** aquí; las demás carpetas lo enlazan en lugar de repetirlo.
- Orden sugerido: internet → HTTP → JSON → APIs REST → seguridad; Regex a parte.
- Para consulta rápida, usa [[07 - Referencia rápida]].