# Scope & Functions

Области видимости, функции и контекст выполнения в JavaScript.

1. [Что такое hoisting и что именно поднимается](#1-что-такое-hoisting-и-что-именно-поднимается)
2. [Что такое область видимости (scope)](#2-что-такое-область-видимости-scope)
3. [Что такое лексическое окружение](#3-что-такое-лексическое-окружение)
4. [Разница между `function declaration` и `function expression`](#4-разница-между-function-declaration-и-function-expression)
5. [Стрелочные функции и их особенности](#5-стрелочные-функции-и-их-особенности)
6. [Есть ли у стрелочных функций `this`, `arguments`, `prototype`](#6-есть-ли-у-стрелочных-функций-this-arguments-prototype)
7. [Что такое `this` и как он определяется](#7-что-такое-this-и-как-он-определяется)
8. [Потеря контекста `this`](#8-потеря-контекста-this)
9. [`call`, `apply`, `bind` и разница между ними](#9-call-apply-bind-и-разница-между-ними)
10. [Что такое IIFE](#10-что-такое-iife)
11. [Как передаются аргументы в функции (по значению и по ссылке)](#11-как-передаются-аргументы-в-функции-по-значению-и-по-ссылке)
12. [Что такое замыкание](#12-что-такое-замыкание)
13. [Замыкания и приватные переменные](#13-замыкания-и-приватные-переменные)
14. [Замыкания в циклах (`var` vs `let`)](#14-замыкания-в-циклах-var-vs-let)

## 1. Что такое hoisting и что именно поднимается

Hoisting (поднятие) — это механизм JavaScript, при котором объявления переменных и функций **"поднимаются"** в начало области видимости.

### Важно понимать

Hoisting — это **концептуальная модель**. На самом деле JavaScript не перемещает код. Но ведёт себя так, будто объявления выполнены первыми.

### Что поднимается?

Разные конструкции поднимаются по-разному:

#### 1. `var` — поднимается со значением `undefined`

```js
console.log(x); // undefined (не ошибка!)
var x = 10;
console.log(x); // 10
```

Это работает так, как будто написано:

```js
var x; // объявление поднято
console.log(x); // undefined
x = 10; // присваивание осталось на месте
console.log(x); // 10
```

#### 2. `let` и `const` — поднимаются, но в TDZ

```js
console.log(y); // ReferenceError (в TDZ)
let y = 20;
```

Технически `let` тоже поднимается, но находится в Temporal Dead Zone до объявления.

#### 3. `function declaration` — поднимается целиком

```js
test(); // "Работает!" ✅ (можно вызвать до объявления)

function test() {
  console.log("Работает!");
}
```

Функция поднимается **со всем телом**, поэтому её можно вызвать до объявления.

#### 4. `function expression` — НЕ поднимается как функция

```js
test(); // TypeError (test === undefined)

var test = function () {
  console.log("Не работает");
};
```

Здесь поднимается только `var test`, а не функция. Поэтому `test` равен `undefined` до присваивания.

### Сравнительная таблица

| Конструкция            | Поднимается? | Инициализация           |
| ---------------------- | ------------ | ----------------------- |
| `var`                  | ✅ Да        | `undefined`             |
| `let`                  | ✅ Да (TDZ)  | Не инициализирована     |
| `const`                | ✅ Да (TDZ)  | Не инициализирована     |
| `function declaration` | ✅ Да        | Вся функция             |
| `function expression`  | ❌ Нет       | `undefined` (для `var`) |

### Примеры

```js
// var
console.log(a); // undefined
var a = 1;

// let
console.log(b); // ReferenceError
let b = 2;

// function declaration
foo(); // "Hello"
function foo() {
  console.log("Hello");
}

// function expression
bar(); // TypeError
var bar = function () {
  console.log("World");
};
```

### Порядок выполнения

При hoisting **функции имеют приоритет** над переменными:

```js
console.log(test); // function test() { ... } (функция!)

var test = "переменная";

function test() {
  console.log("функция");
}
```

### ⚠️ Частая ошибка

```js
if (true) {
  function test() {
    console.log("A");
  }
} else {
  function test() {
    console.log("B");
  }
}

test(); // Результат зависит от реализации (непредсказуемо!)
```

Function declaration в блоках ведёт себя непредсказуемо. Используйте function expression.

### Рекомендации

1. **Всегда объявляйте переменные до использования**
2. Не полагайтесь на hoisting — это может запутать
3. Используйте `let`/`const` вместо `var` — они безопаснее

### Итоги

- Hoisting — концептуальная модель поведения JavaScript
- `var` поднимается как `undefined`
- `let`/`const` поднимаются, но в TDZ
- Function declaration поднимается целиком
- Function expression не поднимается как функция
- Лучше явно объявлять переменные до использования

---

## 2. Что такое область видимости (scope)

Область видимости (scope) — это **место в коде, где переменная доступна и может быть использована**.

### Типы областей видимости

В JavaScript есть три основных типа областей видимости:

1. **Глобальная область видимости** — переменные, объявленные вне функций и блоков
2. **Функциональная область видимости** — переменные доступны внутри функции (для `var`)
3. **Блочная область видимости** — переменные доступны только внутри блока `{}` (для `let`/`const`)

### Глобальная область видимости

Переменные, объявленные на верхнем уровне, доступны везде:

```js
let global = "доступна везде";

function test1() {
  console.log(global); // "доступна везде"
}

function test2() {
  console.log(global); // "доступна везде"
}
```

### Функциональная область видимости

`var` имеет функциональную область видимости — переменная доступна **во всей функции**:

```js
function test() {
  if (true) {
    var x = 10; // объявлена в блоке
  }
  console.log(x); // 10 ✅ (доступна во всей функции)
}
```

### Блочная область видимости

`let` и `const` имеют блочную область видимости — переменная доступна **только в блоке**:

```js
function test() {
  if (true) {
    let x = 10; // объявлена в блоке
  }
  console.log(x); // ReferenceError ❌ (не доступна вне блока)
}
```

### Вложенность областей видимости

Внутренние области видят внешние переменные, но не наоборот:

```js
let outer = "внешняя";

function test() {
  let middle = "средняя";

  if (true) {
    let inner = "внутренняя";
    console.log(outer); // "внешняя" ✅
    console.log(middle); // "средняя" ✅
    console.log(inner); // "внутренняя" ✅
  }

  console.log(inner); // ReferenceError ❌
}
```

### Поиск переменных (scope chain)

Когда JavaScript ищет переменную, он:

1. Сначала смотрит в текущей области видимости
2. Если не находит — поднимается на уровень выше
3. Продолжает до глобальной области
4. Если не находит — `ReferenceError`

```js
let global = "глобальная";

function outer() {
  let outerVar = "внешняя";

  function inner() {
    console.log(global); // "глобальная" (найдена в глобальной области)
    console.log(outerVar); // "внешняя" (найдена во внешней функции)
  }

  inner();
}
```

### Затенение (shadowing)

Если во внутренней области объявить переменную с тем же именем, она "затенит" внешнюю:

```js
let x = "внешняя";

function test() {
  let x = "внутренняя"; // затеняет внешнюю x
  console.log(x); // "внутренняя"
}

console.log(x); // "внешняя" (не изменилась)
```

### Итоги

- Область видимости определяет доступность переменных
- JavaScript использует лексическую область видимости (определяется местом объявления)
- Внутренние области видят внешние переменные
- `var` имеет функциональную область, `let`/`const` — блочную

---

## 3. Что такое лексическое окружение

Лексическое окружение (Lexical Environment) — это внутренняя структура JavaScript, которая хранит **связь между идентификаторами (именами переменных) и их значениями**.

### Что это такое?

Простыми словами: лексическое окружение — это **"словарь"**, который хранит все переменные и функции в определённой области видимости.

### Структура лексического окружения

Каждое лексическое окружение содержит:

1. **Environment Record** (запись окружения) — объект, хранящий переменные и функции
2. **Ссылка на внешнее окружение** (outer reference) — ссылка на родительское окружение

```js
// Глобальное окружение
let global = "глобальная"; // хранится в глобальном Environment Record

function outer() {
  let outerVar = "внешняя"; // хранится в Environment Record функции outer

  function inner() {
    let innerVar = "внутренняя"; // хранится в Environment Record функции inner
    // inner имеет ссылку на окружение outer
    // outer имеет ссылку на глобальное окружение
  }
}
```

### Почему "лексическое"?

**Лексическое** означает, что структура окружения определяется **местом написания кода** (лексически), а не местом вызова.

```js
let x = "глобальная";

function outer() {
  let x = "внешняя";

  function inner() {
    console.log(x); // "внешняя" (берётся из окружения, где функция объявлена)
  }

  return inner;
}

let innerFunc = outer();
innerFunc(); // "внешняя" (не "глобальная"!)
```

Функция `inner` "видит" `x` из `outer`, потому что была **объявлена** внутри `outer`, а не потому что была **вызвана** оттуда.

### Как работает поиск переменных?

При обращении к переменной JavaScript:

1. Ищет в текущем Environment Record
2. Если не находит — идёт по ссылке во внешнее окружение
3. Продолжает до глобального окружения
4. Если не находит — `ReferenceError`

Это называется **scope chain** (цепочка областей видимости).

### Пример с замыканием

Лексическое окружение — основа замыканий:

```js
function createCounter() {
  let count = 0; // хранится в Environment Record createCounter

  return function () {
    count++; // находит count во внешнем окружении (createCounter)
    return count;
  };
}

let counter = createCounter();
counter(); // 1
counter(); // 2
```

Функция "запоминает" окружение, в котором была создана, даже после завершения `createCounter`.

### Визуальная аналогия

Представьте матрёшку:

- Самая внешняя — глобальное окружение
- Внутри неё — окружения функций
- Каждая "знает" о внешней
- Внутренняя может видеть внешние переменные

### Итоги

- Лексическое окружение — внутренняя структура, хранящая переменные
- Определяется местом написания кода (лексически)
- Создаёт цепочку областей видимости (scope chain)
- Основа для работы замыканий

---

## 4. Разница между `function declaration` и `function expression`

В JavaScript функции можно создавать двумя способами, и между ними есть важные отличия.

### Function Declaration (объявление функции)

Function declaration — функция, объявленная как **отдельная инструкция**:

```js
function sayHello() {
  console.log("Hello");
}
```

**Особенности:**

- Поднимается (hoisting) **целиком** — можно вызвать до объявления
- Имеет имя (обязательно)
- Создаётся до выполнения кода

### Function Expression (функциональное выражение)

Function expression — функция, присвоенная **переменной**:

```js
const sayHello = function () {
  console.log("Hello");
};
```

**Особенности:**

- **Не поднимается** как функция (поднимается только переменная)
- Может быть анонимной или именованной
- Создаётся при выполнении кода

### Разница в hoisting

```js
// Function declaration
sayHi(); // "Hi!" ✅ (работает!)

function sayHi() {
  console.log("Hi!");
}

// Function expression
sayBye(); // TypeError ❌ (sayBye === undefined)

const sayBye = function () {
  console.log("Bye!");
};
```

### Когда что использовать?

**Function declaration** — когда функция — основная часть кода:

```js
function calculateTotal(price, tax) {
  return price * (1 + tax);
}

// используется везде в файле
```

**Function expression** — когда функция используется как значение:

```js
// Колбэк
setTimeout(function () {
  console.log("Done");
}, 1000);

// Присваивание
const handler = function (event) {
  // обработка события
};
```

### Именованные function expressions

Function expression может иметь имя (полезно для отладки):

```js
const factorial = function fact(n) {
  if (n <= 1) return 1;
  return n * fact(n - 1); // имя доступно только внутри функции
};

factorial(5); // 120
fact(5); // ReferenceError (имя недоступно снаружи)
```

### Arrow functions — разновидность function expression

Стрелочные функции — это тоже function expression:

```js
const add = (a, b) => a + b; // function expression
```

### Сравнительная таблица

| Особенность                 | Function Declaration | Function Expression           |
| --------------------------- | -------------------- | ----------------------------- |
| Поднимается                 | ✅ Да (целиком)      | ❌ Нет                        |
| Можно вызвать до объявления | ✅ Да                | ❌ Нет                        |
| Имя обязательно             | ✅ Да                | ❌ Нет (может быть анонимной) |
| Использование               | Основные функции     | Колбэки, присваивания         |

### ⚠️ Частая ошибка

```js
if (true) {
  function test() {
    console.log("A");
  }
} else {
  function test() {
    console.log("B");
  }
}

test(); // Результат непредсказуем!
```

Function declaration в блоках ведёт себя непредсказуемо. Используйте function expression:

```js
let test;

if (true) {
  test = function () {
    console.log("A");
  };
} else {
  test = function () {
    console.log("B");
  };
}
```

### Итоги

- Function declaration поднимается целиком, function expression — нет
- Function declaration имеет имя, function expression может быть анонимной
- Выбор зависит от контекста использования

---

## 5. Стрелочные функции и их особенности

Стрелочные функции (arrow functions) — это **короткий синтаксис** для создания функций, введённый в ES6.

### Базовый синтаксис

```js
// Обычная функция
const add1 = function (a, b) {
  return a + b;
};

// Стрелочная функция
const add2 = (a, b) => {
  return a + b;
};

// Короткий вариант (одна строка)
const add3 = (a, b) => a + b;
```

### Особенности синтаксиса

1. **Один параметр** — скобки можно опустить:

```js
const square = (x) => x * x;
```

2. **Без параметров** — скобки обязательны:

```js
const greet = () => "Hello";
```

3. **Несколько строк** — нужны фигурные скобки и `return`:

```js
const multiply = (a, b) => {
  const result = a * b;
  return result;
};
```

4. **Возврат объекта** — нужны скобки:

```js
const createUser = (name) => ({ name: name });
// или
const createUser = (name) => ({ name }); // короткая запись
```

### Главное отличие: нет своего `this`

Самое важное отличие — стрелочные функции **не имеют своего `this`**. Они берут `this` из внешней области видимости:

```js
const obj = {
  name: "Alex",

  regular: function () {
    setTimeout(function () {
      console.log(this.name); // undefined (this === window/undefined)
    }, 100);
  },

  arrow: function () {
    setTimeout(() => {
      console.log(this.name); // "Alex" ✅ (this === obj)
    }, 100);
  },
};
```

### Другие отличия

1. **Нет `arguments`**:

```js
function regular() {
  console.log(arguments); // объект arguments
}

const arrow = () => {
  console.log(arguments); // ReferenceError
};
```

2. **Нельзя использовать как конструктор**:

```js
const Regular = function (name) {
  this.name = name;
};
new Regular("Alex"); // ✅ работает

const Arrow = (name) => {
  this.name = name;
};
new Arrow("Alex"); // TypeError
```

3. **Нет `prototype`**:

```js
const regular = function () {};
console.log(regular.prototype); // {} (есть)

const arrow = () => {};
console.log(arrow.prototype); // undefined (нет)
```

### Когда использовать стрелочные функции?

✅ **Хорошо для:**

- Колбэков и коротких функций
- Методов массивов (`map`, `filter`, `reduce`)
- Когда нужно сохранить `this` из внешнего контекста

```js
const numbers = [1, 2, 3];
const doubled = numbers.map((n) => n * 2); // ✅ удобно
```

❌ **Не подходят для:**

- Методов объектов (нужен свой `this`)
- Конструкторов
- Функций с динамическим `this`
- Когда нужен `arguments`

```js
const obj = {
  name: "Alex",
  greet: () => {
    console.log(this.name); // undefined ❌
  },
  greet2: function () {
    console.log(this.name); // "Alex" ✅
  },
};
```

### Итоги

- Стрелочные функции — короткий синтаксис для функций
- Не имеют своего `this`, `arguments`, `prototype`
- Нельзя использовать как конструктор
- Удобны для колбэков и коротких функций

---

## 21. Есть ли у стрелочных функций `this`, `arguments`, `prototype`

Это важный вопрос на собеседованиях. Давайте разберёмся по пунктам.

### `this` — нет своего, берётся извне

Стрелочные функции **не имеют своего `this`**. Они берут `this` из внешней (лексической) области видимости:

```js
const obj = {
  name: "Alex",

  regular() {
    console.log(this.name); // "Alex" (this === obj)

    function inner() {
      console.log(this.name); // undefined (this === window/undefined)
    }
    inner();
  },

  arrow() {
    console.log(this.name); // "Alex" (this === obj)

    const inner = () => {
      console.log(this.name); // "Alex" ✅ (this === obj, из внешней области)
    };
    inner();
  },
};
```

### `arguments` — нет

У стрелочных функций **нет объекта `arguments`**:

```js
function regular() {
  console.log(arguments); // [1, 2, 3]
}

regular(1, 2, 3);

const arrow = () => {
  console.log(arguments); // ReferenceError ❌
};

arrow(1, 2, 3);
```

**Решение:** используйте rest-параметры:

```js
const arrow = (...args) => {
  console.log(args); // [1, 2, 3] ✅
};
```

### `prototype` — нет

У стрелочных функций **нет свойства `prototype`**:

```js
function regular() {}
console.log(regular.prototype); // {} ✅

const arrow = () => {};
console.log(arrow.prototype); // undefined ❌
```

Это логично, потому что стрелочные функции нельзя использовать как конструктор.

### Почему так сделано?

Стрелочные функции создавались для **коротких колбэков**, где нужно было:

1. Сохранить `this` из внешнего контекста
2. Упростить синтаксис
3. Избежать проблем с `this` в колбэках

### Сводная таблица

| Свойство                           | Обычная функция          | Стрелочная функция          |
| ---------------------------------- | ------------------------ | --------------------------- |
| `this`                             | Свой (зависит от вызова) | Нет (берётся извне)         |
| `arguments`                        | Есть                     | Нет (используйте `...args`) |
| `prototype`                        | Есть                     | Нет                         |
| Можно использовать как конструктор | Да                       | Нет                         |

### Примеры использования

```js
// ✅ Хорошо — колбэк сохраняет this
class Component {
  constructor() {
    this.value = 10;
  }

  setup() {
    setTimeout(() => {
      console.log(this.value); // 10 ✅
    }, 100);
  }
}

// ❌ Плохо — метод объекта нужен свой this
const obj = {
  name: "Alex",
  greet: () => {
    console.log(this.name); // undefined ❌
  },
};

// ✅ Правильно
const obj2 = {
  name: "Alex",
  greet() {
    console.log(this.name); // "Alex" ✅
  },
};
```

### Итоги

- У стрелочных функций **нет** своего `this` — берётся извне
- У стрелочных функций **нет** `arguments` — используйте `...args`
- У стрелочных функций **нет** `prototype`
- Используйте стрелочные функции для колбэков, обычные — для методов

---

## 7. Что такое `this` и как он определяется

`this` — это специальное ключевое слово, которое указывает на **контекст выполнения функции**. Его значение определяется **в момент вызова**, а не объявления.

### Важно понимать

`this` — это **не переменная**, а специальное значение, которое автоматически устанавливается при вызове функции. Оно зависит от **способа вызова**, а не от места объявления.

### Как определяется `this`?

#### 1. В методе объекта — `this` указывает на объект

```js
const obj = {
  name: "Alex",
  greet() {
    console.log(this.name); // this === obj
  },
};

obj.greet(); // "Alex"
```

#### 2. В обычной функции — `this` зависит от режима

```js
function test() {
  console.log(this);
}

// В обычном режиме (браузер)
test(); // window (глобальный объект)

// В strict mode
("use strict");
function test() {
  console.log(this); // undefined
}
```

#### 3. В стрелочной функции — `this` из внешней области

```js
const obj = {
  name: "Alex",
  regular() {
    const arrow = () => {
      console.log(this.name); // this из regular (т.е. obj)
    };
    arrow();
  },
};
```

#### 4. С `new` — `this` указывает на новый объект

```js
function User(name) {
  this.name = name; // this === новый объект
}

const user = new User("Alex");
console.log(user.name); // "Alex"
```

#### 5. С `call`, `apply`, `bind` — `this` задаётся явно

```js
function greet() {
  console.log(this.name);
}

const obj = { name: "Alex" };
greet.call(obj); // "Alex" (this === obj)
greet.apply(obj); // "Alex" (this === obj)

const bound = greet.bind(obj);
bound(); // "Alex" (this === obj)
```

### Потеря контекста `this`

Частая проблема — потеря `this` при передаче метода:

```js
const obj = {
  name: "Alex",
  greet() {
    console.log(this.name);
  },
};

const fn = obj.greet; // теряется контекст!
fn(); // undefined (this === window/undefined)
```

**Решения:**

1. Использовать стрелочную функцию:

```js
const fn = () => obj.greet();
fn(); // "Alex"
```

2. Использовать `bind`:

```js
const fn = obj.greet.bind(obj);
fn(); // "Alex"
```

### Визуальная аналогия

Представьте, что функция — это человек, а `this` — это "я":

- Когда человек говорит о себе в семье — "я" = этот человек
- Когда говорит на работе — "я" = сотрудник
- Когда говорит в другой стране — "я" = может быть по-другому

`this` меняется в зависимости от **контекста вызова**.

### Правила определения `this` (приоритет)

1. **`new`** — создаёт новый объект, `this` = новый объект
2. **`call`/`apply`/`bind`** — `this` задаётся явно
3. **Метод объекта** (`obj.method()`) — `this` = объект
4. **Обычная функция** — `this` = `window` (обычный режим) или `undefined` (strict mode)
5. **Стрелочная функция** — `this` из внешней области

### Итоги

- `this` определяется в момент вызова, а не объявления
- В методе объекта `this` = объект
- В обычной функции `this` = `window`/`undefined`
- В стрелочной функции `this` из внешней области
- Можно явно задать через `call`/`apply`/`bind`

---

## 8. Потеря контекста `this`

Потеря контекста `this` — одна из самых частых проблем в JavaScript. Давайте разберёмся, почему это происходит и как исправить.

### Что такое потеря контекста?

Потеря контекста происходит, когда метод объекта **отсоединяется от объекта** при передаче в другую функцию или переменную.

### Пример проблемы

```js
const user = {
  name: "Alex",
  greet() {
    console.log(`Привет, я ${this.name}`);
  },
};

user.greet(); // "Привет, я Alex" ✅

const fn = user.greet; // метод отсоединился от объекта!
fn(); // "Привет, я undefined" ❌ (this === window/undefined)
```

### Почему это происходит?

Когда вы вызываете `user.greet()`, JavaScript знает, что `this` = `user`. Но когда вы сохраняете метод в переменную, **связь с объектом теряется**.

### Где часто возникает?

#### 1. Колбэки

```js
const button = {
  text: "Нажми меня",
  click() {
    console.log(this.text);
  },
};

setTimeout(button.click, 100); // undefined ❌
```

#### 2. Обработчики событий

```js
const handler = {
  name: "Handler",
  handleClick() {
    console.log(this.name);
  },
};

document.addEventListener("click", handler.handleClick); // undefined ❌
```

#### 3. Массивы методов

```js
const calculator = {
  numbers: [1, 2, 3],
  multiplier: 2,
  multiply() {
    return this.numbers.map(function (n) {
      return n * this.multiplier; // undefined ❌
    });
  },
};
```

### Решения

#### 1. Стрелочная функция (рекомендуется)

```js
const user = {
  name: "Alex",
  greet() {
    console.log(`Привет, я ${this.name}`);
  },
};

const fn = () => user.greet(); // ✅
fn(); // "Привет, я Alex"

// В методах массивов
calculator.multiply = function () {
  return this.numbers.map((n) => n * this.multiplier); // ✅
};
```

#### 2. `bind`

```js
const fn = user.greet.bind(user); // ✅
fn(); // "Привет, я Alex"

setTimeout(button.click.bind(button), 100); // ✅
```

#### 3. `call`/`apply` (если вызываете сразу)

```js
setTimeout(() => button.click.call(button), 100); // ✅
```

#### 4. Сохранение `this` в переменную

```js
const calculator = {
  numbers: [1, 2, 3],
  multiplier: 2,
  multiply() {
    const self = this; // сохраняем this
    return this.numbers.map(function (n) {
      return n * self.multiplier; // ✅
    });
  },
};
```

### Современный подход

В современном JavaScript используйте **стрелочные функции**:

```js
class Component {
  constructor() {
    this.value = 10;
  }

  setup() {
    // ✅ Стрелочная функция сохраняет this
    setTimeout(() => {
      console.log(this.value);
    }, 100);

    // ✅ В методах массивов
    [1, 2, 3].map((n) => n * this.value);
  }
}
```

### ⚠️ Частая ошибка

```js
const obj = {
  name: "Alex",
  methods: {
    greet: () => {
      console.log(this.name); // undefined ❌
      // Стрелочная функция в объекте НЕ имеет this объекта!
    },
  },
};
```

В этом случае стрелочная функция берёт `this` из глобальной области. Используйте обычную функцию:

```js
methods: {
  greet() {
    console.log(this.name);  // ✅
  }
}
```

### Итоги

- Потеря контекста происходит при отсоединении метода от объекта
- Решения: стрелочные функции, `bind`, сохранение `this`
- Используйте стрелочные функции для колбэков
- Обычные функции для методов объектов

---

## 9. `call`, `apply`, `bind` и разница между ними

Эти три метода позволяют **явно задать значение `this`** для функции. Но между ними есть важные отличия.

### `call` — вызывает функцию сразу

`call` вызывает функцию **немедленно** с указанным `this` и аргументами:

```js
function greet(greeting, punctuation) {
  console.log(`${greeting}, я ${this.name}${punctuation}`);
}

const person = { name: "Alex" };

greet.call(person, "Привет", "!"); // "Привет, я Alex!"
//        ↑this  ↑arg1   ↑arg2
```

**Синтаксис:** `func.call(thisArg, arg1, arg2, ...)`

### `apply` — вызывает функцию сразу, аргументы массивом

`apply` работает как `call`, но аргументы передаются **массивом**:

```js
function greet(greeting, punctuation) {
  console.log(`${greeting}, я ${this.name}${punctuation}`);
}

const person = { name: "Alex" };

greet.apply(person, ["Привет", "!"]); // "Привет, я Alex!"
//        ↑this   ↑массив аргументов
```

**Синтаксис:** `func.apply(thisArg, [arg1, arg2, ...])`

### `bind` — возвращает новую функцию, не вызывает

`bind` **не вызывает** функцию, а возвращает **новую функцию** с привязанным `this`:

```js
function greet(greeting) {
  console.log(`${greeting}, я ${this.name}`);
}

const person = { name: "Alex" };

const boundGreet = greet.bind(person); // не вызывает, а создаёт новую функцию
boundGreet("Привет"); // "Привет, я Alex"
```

**Синтаксис:** `func.bind(thisArg, arg1, arg2, ...)`

### Сравнительная таблица

| Метод   | Вызывает функцию? | Возвращает        | Аргументы                             |
| ------- | ----------------- | ----------------- | ------------------------------------- |
| `call`  | ✅ Да (сразу)     | Результат функции | По отдельности                        |
| `apply` | ✅ Да (сразу)     | Результат функции | Массивом                              |
| `bind`  | ❌ Нет            | Новую функцию     | По отдельности (частичное применение) |

### Примеры использования

#### `call` — когда аргументов немного

```js
function introduce(age, city) {
  console.log(`Я ${this.name}, мне ${age}, из ${city}`);
}

const person = { name: "Alex" };
introduce.call(person, 25, "Москва"); // удобно с несколькими аргументами
```

#### `apply` — когда аргументы в массиве

```js
function sum() {
  return Array.from(arguments).reduce((a, b) => a + b, 0);
}

const numbers = [1, 2, 3, 4, 5];
sum.apply(null, numbers); // 15 (this не важен, передаём null)
```

**Современная альтернатива с spread:**

```js
sum(...numbers); // 15 (проще!)
```

#### `bind` — для колбэков

```js
const button = {
  text: "Нажми",
  click() {
    console.log(this.text);
  },
};

// Сохраняем метод с привязанным this
const boundClick = button.click.bind(button);
setTimeout(boundClick, 100); // "Нажми" ✅
```

### Частичное применение с `bind`

`bind` может "зафиксировать" не только `this`, но и аргументы:

```js
function multiply(a, b, c) {
  return a * b * c;
}

const double = multiply.bind(null, 2); // фиксируем первый аргумент
double(3, 4); // 24 (2 * 3 * 4)

const triple = multiply.bind(null, 2, 3); // фиксируем два аргумента
triple(4); // 24 (2 * 3 * 4)
```

### Когда что использовать?

- **`call`** — когда нужно вызвать функцию сразу с другим `this`
- **`apply`** — когда аргументы в массиве (редко, обычно используют spread)
- **`bind`** — когда нужно сохранить функцию с привязанным `this` (колбэки)

### Современные альтернативы

В современном JavaScript часто используют стрелочные функции вместо `bind`:

```js
// Вместо bind
const fn = obj.method.bind(obj);

// Стрелочная функция
const fn = () => obj.method();
```

### Итоги

- `call` — вызывает сразу, аргументы по отдельности
- `apply` — вызывает сразу, аргументы массивом
- `bind` — не вызывает, возвращает новую функцию
- Используйте `bind` для колбэков, `call`/`apply` для немедленного вызова

---

## 10. Что такое IIFE

IIFE (Immediately Invoked Function Expression) — это функция, которая **выполняется сразу после объявления**.

### Синтаксис

```js
(function () {
  console.log("Выполнилась сразу!");
})();

// или

(function () {
  console.log("Выполнилась сразу!");
})();
```

Оба варианта эквивалентны. Скобки вокруг функции и `()` в конце обязательны.

### Зачем нужны IIFE?

#### 1. Изоляция переменных (до ES6)

До появления `let`/`const` IIFE использовались для создания изолированной области видимости:

```js
(function () {
  var x = 10; // локальная переменная
  console.log(x); // 10
})();

console.log(x); // ReferenceError (x не доступна снаружи)
```

#### 2. Избежание конфликтов имён

```js
// Библиотека 1
(function () {
  var library = "Library 1";
  // код библиотеки
})();

// Библиотека 2
(function () {
  var library = "Library 2"; // не конфликтует с первой!
  // код библиотеки
})();
```

#### 3. Модульный паттерн (Module Pattern)

```js
const counter = (function () {
  let count = 0; // приватная переменная

  return {
    increment() {
      count++;
    },
    getCount() {
      return count;
    },
  };
})();

counter.increment();
console.log(counter.getCount()); // 1
console.log(counter.count); // undefined (приватная)
```

### Современная альтернатива

В современном JavaScript (ES6+) IIFE редко нужны, так как есть:

- `let`/`const` — блочная область видимости
- Модули (`import`/`export`) — изоляция кода

```js
// Вместо IIFE
{
  let x = 10;
  console.log(x);
}

// Или модули
// module.js
let x = 10;
export { x };
```

### Итоги

- IIFE — функция, вызываемая сразу после объявления
- Использовалась для изоляции переменных (до ES6)
- Современная альтернатива: `let`/`const` и модули

---

## 11. Как передаются аргументы в функции (по значению и по ссылке)

В JavaScript аргументы передаются **по значению**, но для объектов передаётся **копия ссылки**. Это важное различие!

### Примитивы передаются по значению

При передаче примитивного значения создаётся **копия**:

```js
function changeValue(x) {
  x = 20; // изменяем копию
  console.log(x); // 20
}

let a = 10;
changeValue(a);
console.log(a); // 10 (не изменилось!)
```

### Объекты передаются по ссылке (копия ссылки)

При передаче объекта передаётся **копия ссылки** на объект, а не сам объект:

```js
function changeObject(obj) {
  obj.value = 20; // изменяем содержимое объекта ✅
  obj = { value: 30 }; // переопределяем ссылку (не влияет на исходный объект)
}

let o = { value: 10 };
changeObject(o);
console.log(o.value); // 20 (изменилось содержимое!)
```

**Важно:** нельзя заменить сам объект, но можно изменять его свойства.

### Визуальная аналогия

- **Примитив** — как ксерокопия документа: изменили копию, оригинал не пострадал
- **Объект** — как адрес дома: передали копию адреса, оба указывают на один дом, изменили дом — оба видят изменения

### Примеры

```js
// Примитивы
function addOne(num) {
  num = num + 1;
}

let x = 5;
addOne(x);
console.log(x); // 5 (не изменилось)

// Объекты
function addProperty(obj) {
  obj.newProp = "новое";
}

let obj = {};
addProperty(obj);
console.log(obj.newProp); // "новое" (изменилось!)
```

### ⚠️ Частая ошибка

Попытка заменить объект внутри функции:

```js
function replaceObject(obj) {
  obj = { new: "объект" }; // не работает!
}

let myObj = { old: "объект" };
replaceObject(myObj);
console.log(myObj); // { old: "объект" } (не изменился!)
```

### Итоги

- Примитивы передаются по значению (копируется значение)
- Объекты передаются по ссылке (копируется ссылка)
- Можно изменять свойства объекта, но нельзя заменить сам объект

---

## 12. Что такое замыкание

Замыкание (closure) — это функция, которая имеет доступ к переменным из **внешней области видимости** даже после того, как внешняя функция завершилась.

### Простой пример

```js
function outer() {
  let x = 10; // переменная внешней функции

  function inner() {
    console.log(x); // имеет доступ к x из outer
  }

  return inner;
}

const fn = outer(); // outer завершилась
fn(); // 10 ✅ (inner всё ещё видит x!)
```

### Почему это работает?

Внешние переменные **не удаляются** из памяти, пока есть функция, которая на них ссылается. JavaScript "запоминает" окружение, в котором функция была создана.

### Практический пример — счётчик

```js
function createCounter() {
  let count = 0; // приватная переменная

  return function () {
    count++;
    return count;
  };
}

const counter1 = createCounter();
const counter2 = createCounter();

counter1(); // 1
counter1(); // 2
counter2(); // 1 (независимый счётчик!)
counter1(); // 3
```

Каждый вызов `createCounter()` создаёт **новое** замыкание с собственной переменной `count`.

### Замыкания в циклах (проблема)

Классическая проблема с `var`:

```js
for (var i = 0; i < 3; i++) {
  setTimeout(function () {
    console.log(i); // 3, 3, 3 ❌ (все ссылаются на одну i)
  }, 100);
}
```

**Решения:**

1. Использовать `let`:

```js
for (let i = 0; i < 3; i++) {
  setTimeout(function () {
    console.log(i); // 0, 1, 2 ✅
  }, 100);
}
```

2. IIFE:

```js
for (var i = 0; i < 3; i++) {
  (function (j) {
    setTimeout(function () {
      console.log(j); // 0, 1, 2 ✅
    }, 100);
  })(i);
}
```

### Приватные переменные

Замыкания позволяют создавать приватные переменные:

```js
function createBankAccount(initialBalance) {
  let balance = initialBalance; // приватная переменная

  return {
    deposit(amount) {
      balance += amount;
    },
    withdraw(amount) {
      if (amount <= balance) {
        balance -= amount;
      }
    },
    getBalance() {
      return balance;
    },
  };
}

const account = createBankAccount(100);
account.deposit(50);
console.log(account.getBalance()); // 150
console.log(account.balance); // undefined (приватная!)
```

### Итоги

- Замыкание — функция с доступом к внешним переменным
- Внешние переменные сохраняются в памяти
- Используется для приватных переменных и модулей
- Осторожно с замыканиями в циклах (используйте `let`)

---

## 13. Замыкания и приватные переменные

Замыкания — отличный способ создания **приватных переменных** в JavaScript, так как в языке нет встроенной поддержки приватности на уровне класса (до ES2022).

### Что такое приватные переменные?

Приватные переменные — переменные, к которым **нельзя обратиться напрямую** снаружи. Доступ к ним возможен только через методы.

### Создание приватных переменных через замыкания

```js
function createPerson(name) {
  let age = 0; // приватная переменная

  return {
    getName() {
      return name;
    },
    getAge() {
      return age;
    },
    setAge(newAge) {
      if (newAge > 0) {
        age = newAge;
      }
    },
    haveBirthday() {
      age++;
    },
  };
}

const person = createPerson("Alex");
person.haveBirthday();
console.log(person.getAge()); // 1
console.log(person.age); // undefined (приватная!)
person.age = 100; // не работает (не меняет внутреннюю age)
console.log(person.getAge()); // 1 (не изменилось!)
```

### Модульный паттерн

Классический способ создания модулей с приватными переменными:

```js
const module = (function () {
  let privateVar = "секрет";

  function privateFunction() {
    return privateVar;
  }

  return {
    publicMethod() {
      return privateFunction();
    },
    anotherPublicMethod() {
      return "публичный метод";
    },
  };
})();

module.publicMethod(); // "секрет"
module.privateVar; // undefined
module.privateFunction(); // TypeError
```

### Современная альтернатива (ES2022)

В современных версиях JavaScript можно использовать приватные поля класса:

```js
class Person {
  #age = 0; // приватное поле

  constructor(name) {
    this.name = name;
  }

  getAge() {
    return this.#age;
  }

  setAge(age) {
    if (age > 0) {
      this.#age = age;
    }
  }
}

const person = new Person("Alex");
person.setAge(25);
console.log(person.getAge()); // 25
console.log(person.#age); // SyntaxError (приватное поле!)
```

### Сравнение подходов

| Подход               | Доступность | Совместимость                  |
| -------------------- | ----------- | ------------------------------ |
| Замыкания            | ✅ Везде    | ✅ Все браузеры                |
| Приватные поля (`#`) | ✅ ES2022+  | ❌ Только современные браузеры |

### Когда использовать замыкания?

Замыкания для приватности полезны, когда:

- Нужна поддержка старых браузеров
- Создаёте библиотеку или модуль
- Нужна полная изоляция данных

### Итоги

- Замыкания позволяют создавать приватные переменные
- Приватные переменные недоступны снаружи
- Доступ только через методы
- Современная альтернатива — приватные поля классов (`#`)

---

## 14. Замыкания в циклах (`var` vs `let`)

Классическая проблема с замыканиями в циклах — одна из самых частых ошибок в JavaScript.

### Проблема с `var`

```js
for (var i = 0; i < 3; i++) {
  setTimeout(function () {
    console.log(i); // 3, 3, 3 ❌
  }, 100);
}
```

**Почему так?** Все функции в `setTimeout` ссылаются на **одну и ту же переменную** `i`, которая к моменту выполнения уже равна 3.

### Решение 1: `let` (рекомендуется)

`let` создаёт **новую переменную** для каждой итерации:

```js
for (let i = 0; i < 3; i++) {
  setTimeout(function () {
    console.log(i); // 0, 1, 2 ✅
  }, 100);
}
```

### Решение 2: IIFE

Можно использовать IIFE для создания новой области видимости:

```js
for (var i = 0; i < 3; i++) {
  (function (j) {
    setTimeout(function () {
      console.log(j); // 0, 1, 2 ✅
    }, 100);
  })(i);
}
```

### Решение 3: `bind`

```js
function logIndex(i) {
  console.log(i);
}

for (var i = 0; i < 3; i++) {
  setTimeout(logIndex.bind(null, i), 100); // 0, 1, 2 ✅
}
```

### Почему `let` работает?

При использовании `let` в цикле `for` JavaScript создаёт **новое лексическое окружение** для каждой итерации. Каждая функция замыкается на свою копию `i`.

### Визуальная аналогия

- **С `var`**: все функции смотрят на **одну и ту же** переменную в общей комнате
- **С `let`**: каждая функция находится в **своей комнате** со своей копией переменной

### Пример с массивом функций

```js
// С var — все функции возвращают 3
var functions = [];
for (var i = 0; i < 3; i++) {
  functions.push(function () {
    return i; // все ссылаются на одну i
  });
}
functions[0](); // 3 ❌
functions[1](); // 3 ❌
functions[2](); // 3 ❌

// С let — каждая функция возвращает свой индекс
let functions2 = [];
for (let i = 0; i < 3; i++) {
  functions2.push(function () {
    return i; // каждая ссылается на свою i
  });
}
functions2[0](); // 0 ✅
functions2[1](); // 1 ✅
functions2[2](); // 2 ✅
```

### Итоги

- С `var` все замыкания ссылаются на одну переменную
- С `let` каждая итерация создаёт новую переменную
- Используйте `let` в циклах для правильной работы замыканий

---
