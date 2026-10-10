# 🐘 PHP — Índice

> [!info] ¿Qué es PHP?
> PHP es un lenguaje de programación que se ejecuta **en el servidor**. Cuando alguien abre una página, el servidor ejecuta el código PHP, genera el resultado (normalmente HTML o JSON) y se lo envía al navegador. El usuario nunca ve el código PHP, solo el resultado.
> Se usa para formularios, inicios de sesión, bases de datos, carritos de compra, APIs... WordPress, por ejemplo, está hecho con PHP.

---

## 1. Antes de empezar

Para practicar PHP necesitas:

- **Un intérprete de PHP**: el programa que ejecuta tu código.
- **Un servidor**: el integrado de PHP (`php -S localhost:8000`), XAMPP o Docker.
- **Un editor de código**: por ejemplo VS Code.

Los ficheros PHP terminan en `.php` y el código va dentro de `<?php ... ?>`.

---

## 2. Cómo usar esta carpeta

Cada nota explica **un tema completo**, desde cero hasta los casos avanzados, para servir tanto de repaso rápido como de explicación desde el principio.

- Si has olvidado un tema **a medias**: ve a su nota y lee la sintaxis y el resumen.
- Si lo has olvidado **del todo**: léela entera.
- Si solo necesitas recordar una función: ve a [[PHP/20 - Referencia rápida|20 - Referencia rápida]].

---

## 3. Contenido

### 🧱 Bases del lenguaje

| Nota | Qué vas a encontrar |
|---|---|
| [[PHP/01 - Fundamentos\|01 - Fundamentos]] | Qué es PHP, cómo se ejecuta, etiquetas, `echo`, comentarios, `include` y constantes mágicas |
| [[PHP/02 - Variables y tipos\|02 - Variables y tipos]] | Variables, constantes, tipos de datos, conversiones y comprobaciones |
| [[PHP/03 - Operadores\|03 - Operadores]] | Aritméticos, comparación, lógicos, asignación, especiales y bit a bit |
| [[PHP/04 - Condicionales\|04 - Condicionales]] | `if`, `switch` y `match` |
| [[PHP/05 - Bucles\|05 - Bucles]] | `for`, `while`, `do...while`, `foreach`, `break` y `continue` |
| [[PHP/06 - Funciones\|06 - Funciones]] | Parámetros, retornos, tipos, closures y funciones flecha |

### 📦 Datos, texto, números y fechas

| Nota | Qué vas a encontrar |
|---|---|
| [[PHP/07 - Arrays\|07 - Arrays]] | Arrays indexados, asociativos y multidimensionales, y todas sus funciones |
| [[PHP/08 - Strings\|08 - Strings]] | Cadenas, comillas, funciones de texto y expresiones regulares |
| [[PHP/09 - Matemáticas y fechas\|09 - Matemáticas y fechas]] | Redondeo, aleatorios, precisión, `date()`, `DateTime`, intervalos y zonas horarias |

### 🌐 PHP en la web

| Nota | Qué vas a encontrar |
|---|---|
| [[PHP/10 - Formularios\|10 - Formularios]] | `$_GET`, `$_POST`, validación y recogida de datos |
| [[PHP/11 - Sesiones y cookies\|11 - Sesiones y cookies]] | Recordar al usuario entre páginas |
| [[PHP/12 - Ficheros\|12 - Ficheros]] | Leer, escribir, subir y descargar ficheros de forma segura |
| [[13 - JSON y APIs\|13 - JSON y APIs]] | `json_encode`, `json_decode`, crear una API y consumir APIs externas |

### 🏗️ Programación avanzada

| Nota | Qué vas a encontrar |
|---|---|
| [[PHP/14 - POO\|14 - POO]] | Clases, herencia, interfaces, traits, enums, magia y autoload |
| [[PHP/15 - Excepciones\|15 - Excepciones]] | `try`, `catch`, `finally` y excepciones propias |

### 🗄️ Bases de datos y herramientas

| Nota | Qué vas a encontrar |
|---|---|
| [[PHP/16 - PDO y bases de datos\|16 - PDO y bases de datos]] | Conectar con MySQL, consultas preparadas y transacciones |
| [[PHP/17 - Composer\|17 - Composer]] | Dependencias, autoload y paquetes |

### 🔒 Calidad y cierre

| Nota | Qué vas a encontrar |
|---|---|
| [[PHP/18 - Seguridad\|18 - Seguridad]] | Inyección SQL, XSS, CSRF, contraseñas, subidas, rutas y más |
| [[PHP/19 - PHP moderno\|19 - PHP moderno]] | Novedades del lenguaje y qué evitar |
| [[PHP/20 - Referencia rápida\|20 - Referencia rápida]] | Chuleta con lo más usado |

---

## 4. Orden recomendado

Si aprendes PHP desde cero, sigue el orden numérico:

1. **Bases**: 01 a 06.
2. **Datos, texto, números y fechas**: 07 a 09.
3. **Web**: 10 a 13.
4. **Avanzado**: 14 y 15.
5. **Bases de datos y herramientas**: 16 y 17.
6. **Seguridad y modernización**: 18 y 19.

> [!tip] Consejo
> Las notas 10 (Formularios) y 16 (PDO) son las que más usarás en proyectos reales. La 18 (Seguridad) no es opcional: léela antes de publicar cualquier proyecto.

---

## 5. Notas relacionadas

- [[HTML/00 - Índice|HTML]]: PHP genera HTML y recibe datos de formularios HTML.
- [[SQL/00 - Índice|SQL]]: el lenguaje que usarás con PDO.
- [[Conceptos generales/01 - HTTP|HTTP]]: cómo viaja la información entre navegador y servidor.
- [[Conceptos generales/05 - Seguridad web|Seguridad web]]: base teórica de la nota 18.

---

## 6. Resumen

- PHP se ejecuta en el servidor y devuelve HTML o JSON al navegador.
- Esta carpeta va de lo básico (variables, bucles) a lo real (formularios, APIs, bases de datos, seguridad).
- Para consultar rápido, usa la **Referencia rápida**.