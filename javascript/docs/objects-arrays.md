# Objects & Arrays

Объекты, массивы и связанные с ними механизмы JavaScript.  
Материал представлен в формате **вопрос–ответ**.

1. [Изменяемы ли строки в JavaScript](#1-изменяемы-ли-строки-в-javascript)
2. [Что такое объект в JavaScript](#2-что-такое-объект-в-javascript)
3. [Как проверить, что значение — массив](#3-как-проверить-что-значение--массив)
4. [Как проверить наличие свойства в объекте](#4-как-проверить-наличие-свойства-в-объекте)
5. [Разница между `in` и `hasOwnProperty`](#5-разница-между-in-и-hasownproperty)
6. [Итерация по объекту](#6-итерация-по-объекту)
7. [Почему `{}` !== `{}` и как сравниваются объекты](#7-почему----и-как-сравниваются-объекты)
8. [Что такое прототип](#8-что-такое-прототип)
9. [`__proto__`, `[[Prototype]]`, `Object.getPrototypeOf`](#9-__proto__-prototype-objectgetprototypeof)
10. [Цепочка прототипов](#10-цепочка-прототипов)
11. [Что находится в конце цепочки прототипов](#11-что-находится-в-конце-цепочки-прототипов)
12. [Поверхностное и глубокое копирование](#12-поверхностное-и-глубокое-копирование)
13. [Способы копирования объектов](#13-способы-копирования-объектов)
14. [Как сделать глубокую копию объекта](#14-как-сделать-глубокую-копию-объекта)
15. [Ограничения `JSON.parse(JSON.stringify())`](#15-ограничения-jsonparsejsonstringify)
16. [Какие типы данных теряются при `JSON.stringify`](#16-какие-типы-данных-теряются-при-jsonstringify)
17. [Мутация и иммутабельность](#17-мутация-и-иммутабельность)
18. [Методы массивов `map`, `filter`, `reduce`](#18-методы-массивов-map-filter-reduce)
19. [Разница между `map` и `forEach`](#19-разница-между-map-и-foreach)
20. [Методы массива `find`, `some`, `every`](#20-методы-массива-find-some-every)
21. [Мутирующие и немутирующие методы массивов](#21-мутирующие-и-немутирующие-методы-массивов)
22. [`Set`, `Map`, `WeakSet`, `WeakMap`](#22-set-map-weakset-weakmap)
23. [Отличие `Map` от обычного объекта](#23-отличие-map-от-обычного-объекта)
24. [Зачем нужны `WeakMap` и `WeakSet`](#24-зачем-нужны-weakmap-и-weakset)

## 1. Изменяемы ли строки в JavaScript

**Нет**, строки в JavaScript **неизменяемы** (immutable). Любая операция, которая "изменяет" строку, на самом деле создаёт **новую строку**.

### Что означает неизменяемость?

Нельзя изменить символ в строке напрямую:

```js
let str = "hello";
str[0] = "H"; // не работает
console.log(str); // "hello" (не изменилось!)
```

### Операции создают новые строки

Все методы строк возвращают **новую строку**, не изменяя исходную:

```js
let str = "hello";

let upper = str.toUpperCase(); // создаёт новую строку
console.log(str); // "hello" (не изменилась)
console.log(upper); // "HELLO" (новая строка)

let trimmed = str.trim(); // новая строка
let replaced = str.replace("l", "L"); // новая строка
```

### Конкатенация создаёт новую строку

```js
let str = "hello";
str = str + "!"; // создаётся новая строка "hello!"
// исходная строка "hello" удаляется сборщиком мусора
```

### Почему строки неизменяемы?

Неизменяемость даёт преимущества:

1. **Безопасность** — нельзя случайно изменить строку
2. **Производительность** — строки можно переиспользовать
3. **Простые сравнения** — сравнение по значению быстрое

### Сравнение с массивами

Массивы **изменяемы**:

```js
let arr = [1, 2, 3];
arr.push(4); // изменяет исходный массив
console.log(arr); // [1, 2, 3, 4]

let str = "hello";
str.toUpperCase(); // не изменяет исходную строку
console.log(str); // "hello"
```

### Создание новой строки из существующей

Если нужно "изменить" строку, создайте новую:

```js
let str = "hello";
str = "H" + str.slice(1); // создаёт новую строку "Hello"
console.log(str); // "Hello"
```

### Итоги

- Строки неизменяемы — нельзя изменить символ напрямую
- Все операции со строками создают новые строки
- Исходная строка не изменяется
- Это обеспечивает безопасность и производительность

---

## 2. Что такое объект в JavaScript

Объект в JavaScript — это **коллекция пар "ключ-значение"**, где ключ — это строка (или Symbol), а значение — любой тип данных.

### Создание объекта

```js
// Литерал объекта
const person = {
  name: "Alex",
  age: 25,
  city: "Moscow",
};

// Через конструктор
const person2 = new Object();
person2.name = "Bob";
person2.age = 30;
```

### Структура объекта

```js
const obj = {
  key1: "value1", // строка
  key2: 42, // число
  key3: true, // boolean
  key4: [1, 2, 3], // массив
  key5: {
    // вложенный объект
    nested: "value",
  },
  key6: function () {
    // метод
    return "method";
  },
};
```

### Доступ к свойствам

```js
const person = { name: "Alex", age: 25 };

// Точечная нотация
person.name; // "Alex"

// Скобочная нотация
person["name"]; // "Alex"
person["age"]; // 25

// Динамический ключ
const key = "name";
person[key]; // "Alex"
```

### Особенности объектов в JavaScript

1. **Динамические** — можно добавлять и удалять свойства:

```js
const obj = {};
obj.newProp = "новое свойство";
delete obj.newProp;
```

2. **Ссылочные** — хранятся по ссылке:

```js
const obj1 = { x: 1 };
const obj2 = obj1; // копируется ссылка
obj2.x = 2;
console.log(obj1.x); // 2 (изменился исходный объект!)
```

3. **Все — объекты** (почти):
   - Массивы — это объекты
   - Функции — это объекты
   - Даже примитивы имеют объекты-обёртки

### Типы значений в объектах

Ключи могут быть:

- **Строки** (автоматически, даже без кавычек)
- **Symbol** (уникальные идентификаторы)

```js
const obj = {
  "string key": "value",
  numberKey: 42,
  [Symbol("symbol")]: "symbol value",
};
```

### Методы объекта

Метод — это функция, хранящаяся как свойство объекта:

```js
const calculator = {
  x: 10,
  y: 5,
  add() {
    return this.x + this.y;
  },
  multiply() {
    return this.x * this.y;
  },
};

calculator.add(); // 15
calculator.multiply(); // 50
```

### Итоги

- Объект — коллекция пар "ключ-значение"
- Свойства могут быть любого типа
- Объекты динамические и ссылочные
- Массивы и функции — тоже объекты

---

## 3. Как проверить, что значение — массив

В JavaScript массивы технически являются объектами, поэтому обычная проверка `typeof` не работает. Нужны специальные методы.

### ❌ Неправильные способы

```js
const arr = [1, 2, 3];

typeof arr; // "object" ❌ (не подходит)
arr instanceof Array; // true ✅ (работает, но есть нюансы)
```

**Проблема с `instanceof`:**

- Не работает через iframe (разные контексты)
- Может быть переопределено

### ✅ Правильный способ: `Array.isArray()`

**Рекомендуемый способ** — использовать `Array.isArray()`:

```js
Array.isArray([1, 2, 3]); // true ✅
Array.isArray({}); // false ✅
Array.isArray("string"); // false ✅
Array.isArray(null); // false ✅
Array.isArray(undefined); // false ✅
Array.isArray([]); // true ✅
```

### Почему `Array.isArray()` лучше?

1. **Надёжно** — работает везде, даже через iframe
2. **Быстро** — оптимизированная встроенная функция
3. **Понятно** — явно показывает намерение

### Альтернативные способы (не рекомендуются)

```js
// Через Object.prototype.toString
(Object.prototype.toString.call([1, 2, 3]) ===
  "[object Array]"[ // true
    // Через конструктор (ненадёжно)
    (1, 2, 3)
  ].constructor) ===
  Array; // true (но можно обмануть)
```

### Пример использования

```js
function processData(data) {
  if (Array.isArray(data)) {
    // обрабатываем как массив
    return data.map((item) => item * 2);
  } else {
    // обрабатываем как объект
    return { processed: data };
  }
}

processData([1, 2, 3]); // [2, 4, 6]
processData({ x: 1 }); // { processed: { x: 1 } }
```

### Итоги

- Используйте `Array.isArray()` для проверки массивов
- `typeof` не работает (возвращает "object")
- `instanceof` работает, но менее надёжен

---

## 4. Как проверить наличие свойства в объекте

В JavaScript есть несколько способов проверить, есть ли свойство в объекте. Каждый имеет свои особенности.

### Способ 1: Оператор `in`

Оператор `in` проверяет свойство **в объекте и в его прототипе**:

```js
const obj = { name: "Alex" };

"name" in obj; // true ✅
"age" in obj; // false ✅
"toString" in obj; // true ⚠️ (из прототипа!)
```

### Способ 2: `hasOwnProperty()`

Метод `hasOwnProperty()` проверяет только **собственные свойства** объекта (не из прототипа):

```js
const obj = { name: "Alex" };

obj.hasOwnProperty("name"); // true ✅
obj.hasOwnProperty("age"); // false ✅
obj.hasOwnProperty("toString"); // false ✅ (не своё свойство)
```

### Способ 3: `Object.hasOwn()` (ES2022)

Современный способ, аналогичный `hasOwnProperty()`, но более безопасный:

```js
const obj = { name: "Alex" };

Object.hasOwn(obj, "name"); // true ✅
Object.hasOwn(obj, "toString"); // false ✅
```

**Преимущество:** работает даже если объект создан с `Object.create(null)`.

### Способ 4: Проверка через `undefined`

Простейший способ, но **не всегда надёжен**:

```js
const obj = { name: "Alex", age: undefined };

obj.name !== undefined; // true ✅
obj.age !== undefined; // false ⚠️ (но свойство есть!)
obj.city !== undefined; // false ✅
```

**Проблема:** если свойство существует, но его значение `undefined`, проверка даст `false`.

### Сравнительная таблица

| Способ             | Собственные свойства | Свойства прототипа | Значение `undefined` |
| ------------------ | -------------------- | ------------------ | -------------------- |
| `in`               | ✅                   | ✅                 | ✅                   |
| `hasOwnProperty()` | ✅                   | ❌                 | ✅                   |
| `Object.hasOwn()`  | ✅                   | ❌                 | ✅                   |
| `!== undefined`    | ❌                   | ❌                 | ❌                   |

### ⚠️ Частая ошибка

```js
const obj = { name: "Alex", age: undefined };

// Неправильно
if (obj.age) {
  console.log("Есть возраст");
} // не выполнится, т.к. undefined — falsy

// Правильно
if ("age" in obj) {
  console.log("Свойство age существует");
} // выполнится
```

### Когда что использовать?

- **`in`** — когда нужно проверить свойство включая прототип
- **`hasOwnProperty()` / `Object.hasOwn()`** — когда нужны только собственные свойства (обычно так)
- **`!== undefined`** — когда уверены, что значение не `undefined`

### Итоги

- `in` — проверяет свойство и в объекте, и в прототипе
- `hasOwnProperty()` / `Object.hasOwn()` — только собственные свойства
- Проверка через `undefined` ненадёжна

---

## 5. Разница между `in` и `hasOwnProperty`

Это важное различие, которое часто вызывает путаницу. Давайте разберёмся подробно.

### `in` — проверяет всю цепочку прототипов

Оператор `in` проверяет свойство **во всём объекте**, включая прототип:

```js
const obj = { name: "Alex" };

"name" in obj; // true ✅ (собственное свойство)
"toString" in obj; // true ✅ (из прототипа Object)
"valueOf" in obj; // true ✅ (из прототипа Object)
```

### `hasOwnProperty()` — только собственные свойства

Метод `hasOwnProperty()` проверяет только **собственные свойства** объекта, игнорируя прототип:

```js
const obj = { name: "Alex" };

obj.hasOwnProperty("name"); // true ✅ (собственное свойство)
obj.hasOwnProperty("toString"); // false ✅ (из прототипа, не собственное)
obj.hasOwnProperty("valueOf"); // false ✅ (из прототипа)
```

### Визуальная аналогия

Представьте объект как дом:

- **`in`** — ищет ключ во всём доме, включая подвал (прототип)
- **`hasOwnProperty()`** — ищет ключ только в основных комнатах (собственные свойства)

### Пример с наследованием

```js
function Animal(name) {
  this.name = name;
}

Animal.prototype.speak = function () {
  console.log(this.name + " говорит");
};

const dog = new Animal("Рекс");

// in — находит и собственные, и унаследованные свойства
"name" in dog; // true ✅ (собственное)
"speak" in dog; // true ✅ (из прототипа)

// hasOwnProperty — только собственные
dog.hasOwnProperty("name"); // true ✅
dog.hasOwnProperty("speak"); // false ✅ (из прототипа)
```

### Когда что использовать?

**Используйте `in`, когда:**

- Нужно проверить, доступно ли свойство (включая методы прототипа)
- Проверяете существование метода

**Используйте `hasOwnProperty()` / `Object.hasOwn()`, когда:**

- Нужны только собственные свойства объекта (обычно так!)
- Итерируете по свойствам объекта
- Проверяете данные объекта, а не методы

### ⚠️ Проблема с `hasOwnProperty`

Если объект создан с `Object.create(null)`, у него нет прототипа, и `hasOwnProperty` недоступен:

```js
const obj = Object.create(null);
obj.name = "Alex";

obj.hasOwnProperty("name"); // TypeError ❌ (метода нет!)

// Решение 1: Object.hasOwn() (ES2022)
Object.hasOwn(obj, "name"); // true ✅

// Решение 2: call
Object.prototype.hasOwnProperty.call(obj, "name"); // true ✅
```

### Современная альтернатива: `Object.hasOwn()`

В ES2022 появился `Object.hasOwn()`, который решает проблему:

```js
const obj = { name: "Alex" };

Object.hasOwn(obj, "name"); // true ✅
Object.hasOwn(obj, "toString"); // false ✅

// Работает даже с Object.create(null)
const obj2 = Object.create(null);
obj2.x = 1;
Object.hasOwn(obj2, "x"); // true ✅
```

### Итоги

- `in` — проверяет свойство во всей цепочке прототипов
- `hasOwnProperty()` — только собственные свойства
- `Object.hasOwn()` — современная альтернатива `hasOwnProperty()`
- Обычно используют `hasOwnProperty()` / `Object.hasOwn()`

---

## 6. Итерация по объекту

В JavaScript есть несколько способов перебрать свойства объекта. Каждый имеет свои особенности.

### Способ 1: `for...in`

Цикл `for...in` перебирает **все перечисляемые свойства**, включая унаследованные:

```js
const obj = { name: "Alex", age: 25 };

for (let key in obj) {
  console.log(key, obj[key]);
}
// name Alex
// age 25
```

**⚠️ Важно:** перебирает и свойства прототипа! Используйте `hasOwnProperty()`:

```js
function Parent() {
  this.parentProp = "parent";
}

function Child() {
  this.childProp = "child";
}
Child.prototype = new Parent();

const obj = new Child();

for (let key in obj) {
  console.log(key); // childProp, parentProp (оба!)
}

// Только собственные свойства
for (let key in obj) {
  if (obj.hasOwnProperty(key)) {
    console.log(key); // только childProp
  }
}
```

### Способ 2: `Object.keys()`

`Object.keys()` возвращает **массив ключей** только собственных перечисляемых свойств:

```js
const obj = { name: "Alex", age: 25 };

Object.keys(obj); // ["name", "age"]

Object.keys(obj).forEach((key) => {
  console.log(key, obj[key]);
});
```

**Преимущества:**

- Только собственные свойства (не из прототипа)
- Можно использовать методы массивов (`map`, `filter`)

### Способ 3: `Object.values()`

`Object.values()` возвращает **массив значений** собственных перечисляемых свойств:

```js
const obj = { name: "Alex", age: 25 };

Object.values(obj); // ["Alex", 25]

Object.values(obj).forEach((value) => {
  console.log(value);
});
```

### Способ 4: `Object.entries()`

`Object.entries()` возвращает **массив пар [ключ, значение]**:

```js
const obj = { name: "Alex", age: 25 };

Object.entries(obj); // [["name", "Alex"], ["age", 25]]

Object.entries(obj).forEach(([key, value]) => {
  console.log(key, value);
});

// Преобразование в Map
const map = new Map(Object.entries(obj));
```

### Сравнительная таблица

| Метод              | Что возвращает      | Собственные свойства | Унаследованные |
| ------------------ | ------------------- | -------------------- | -------------- |
| `for...in`         | ключи (итерация)    | ✅                   | ✅             |
| `Object.keys()`    | массив ключей       | ✅                   | ❌             |
| `Object.values()`  | массив значений     | ✅                   | ❌             |
| `Object.entries()` | массив [key, value] | ✅                   | ❌             |

### Примеры использования

```js
const user = { name: "Alex", age: 25, city: "Moscow" };

// for...in
for (let key in user) {
  if (user.hasOwnProperty(key)) {
    console.log(`${key}: ${user[key]}`);
  }
}

// Object.keys() с map
const keys = Object.keys(user);
keys.map((key) => `${key}: ${user[key]}`);

// Object.entries() с деструктуризацией
Object.entries(user).forEach(([key, value]) => {
  console.log(`${key}: ${value}`);
});

// Фильтрация
Object.entries(user)
  .filter(([key, value]) => typeof value === "string")
  .forEach(([key, value]) => console.log(key, value));
```

### Перечисляемые свойства

Все эти методы работают только с **перечисляемыми свойствами**. Неперечисляемые свойства (например, `length` у массивов) не попадут в перебор:

```js
const arr = [1, 2, 3];

Object.keys(arr); // ["0", "1", "2"] (length не попадает)
Object.getOwnPropertyNames(arr); // ["0", "1", "2", "length"] (все свойства)
```

### Итоги

- `for...in` — перебирает все свойства (включая прототип)
- `Object.keys()` — массив ключей собственных свойств
- `Object.values()` — массив значений
- `Object.entries()` — массив пар [ключ, значение]
- Обычно используют `Object.keys()` или `Object.entries()`

---

## 7. Почему `{}` !== `{}` и как сравниваются объекты

Это один из самых частых вопросов на собеседованиях. Давайте разберёмся, почему объекты сравниваются именно так.

### Объекты сравниваются по ссылке

В JavaScript объекты сравниваются **по ссылке**, а не по содержимому:

```js
{} !== {}        // true (разные объекты в памяти)
[] !== []        // true (разные массивы)
```

**Почему?** Каждый объект создаётся в **новом месте памяти**, даже если содержимое одинаковое.

### Визуальная аналогия

Представьте два одинаковых дома:

- **Примитивы** (`5 === 5`) — сравниваем сами дома (значения)
- **Объекты** (`{} !== {}`) — сравниваем адреса домов (ссылки), а не сами дома

### Примеры

```js
const obj1 = { name: "Alex" };
const obj2 = { name: "Alex" };

obj1 === obj2; // false ❌ (разные объекты)

const obj3 = obj1;
obj1 === obj3; // true ✅ (один и тот же объект)

obj1.name = "Bob";
console.log(obj3.name); // "Bob" (изменился, т.к. это тот же объект)
```

### Массивы тоже объекты

```js
[1, 2, 3] === [1, 2, 3]; // false ❌

const arr1 = [1, 2, 3];
const arr2 = arr1;
arr1 === arr2; // true ✅
```

### Функции тоже объекты

```js
function fn() {}
function fn2() {}

fn === fn2; // false ❌

const fn3 = fn;
fn === fn3; // true ✅
```

### Как сравнить объекты по содержимому?

JavaScript **не имеет встроенного способа** глубокого сравнения объектов. Нужно сравнивать вручную:

#### Простое сравнение (поверхностное)

```js
function shallowEqual(obj1, obj2) {
  const keys1 = Object.keys(obj1);
  const keys2 = Object.keys(obj2);

  if (keys1.length !== keys2.length) {
    return false;
  }

  for (let key of keys1) {
    if (obj1[key] !== obj2[key]) {
      return false;
    }
  }

  return true;
}

shallowEqual({ a: 1 }, { a: 1 }); // true ✅
shallowEqual({ a: 1, b: { c: 2 } }, { a: 1, b: { c: 2 } }); // false ⚠️ (вложенные объекты!)
```

#### Глубокое сравнение (сложное)

Для глубокого сравнения обычно используют библиотеки (например, Lodash):

```js
// С Lodash
import _ from "lodash";
_.isEqual({ a: { b: 1 } }, { a: { b: 1 } }); // true
```

Или реализуют рекурсивную функцию (довольно сложно).

### Сравнение примитивов

Для сравнения всё работает как ожидается:

```js
5 === 5; // true ✅
"hello" === "hello"; // true ✅
true === true; // true ✅
null === null; // true ✅
undefined === undefined; // true ✅
```

### Почему так сделано?

Сравнение по ссылке **быстрее**, чем сравнение по содержимому. Если бы объекты сравнивались по содержимому, пришлось бы проверять все свойства, включая вложенные.

### ⚠️ Частая ошибка

```js
function createUser(name) {
  return { name };
}

const user1 = createUser("Alex");
const user2 = createUser("Alex");

if (user1 === user2) {
  // false ❌
  console.log("Один пользователь");
} else {
  console.log("Разные пользователи"); // выполнится
}

// Правильно — сравнивать по значению
if (user1.name === user2.name) {
  // true ✅
  console.log("Одинаковые имена");
}
```

### Итоги

- Объекты сравниваются по ссылке, а не по содержимому
- `{} !== {}` потому что это разные объекты в памяти
- Для сравнения по содержимому нужна специальная функция
- Примитивы сравниваются по значению

---

## 8. Что такое прототип

Прототип (prototype) — это **механизм наследования** в JavaScript. Каждый объект имеет ссылку на другой объект — свой прототип.

### Простыми словами

Прототип — это "запасной объект", где JavaScript ищет свойства, если их нет в самом объекте.

### Как это работает?

```js
const obj = {};
obj.toString(); // "[object Object]" — откуда взялся метод toString?

// toString находится в прототипе Object.prototype
```

Когда вы обращаетесь к свойству объекта:

1. JavaScript сначала ищет свойство **в самом объекте**
2. Если не находит — идёт в **прототип объекта**
3. Если не находит — идёт в прототип прототипа (цепочка прототипов)
4. И так до `null`

### Визуальная аналогия

Представьте библиотеку:

- **Объект** — ваша полка с книгами
- **Прототип** — общая библиотека
- Если книги нет на вашей полке, идёте в общую библиотеку

### Пример с конструктором

```js
function User(name) {
  this.name = name;
}

// Добавляем метод в прототип
User.prototype.sayHi = function () {
  console.log(`Привет, я ${this.name}`);
};

const user1 = new User("Alex");
const user2 = new User("Bob");

user1.sayHi(); // "Привет, я Alex"
user2.sayHi(); // "Привет, я Bob"

// Метод sayHi один для всех экземпляров!
user1.sayHi === user2.sayHi; // true ✅
```

**Почему методы в прототипе?** Чтобы все экземпляры использовали **один и тот же метод**, а не копию.

### Доступ к прототипу

```js
const obj = {};

// Старый способ (не рекомендуется)
obj.__proto__; // Object.prototype

// Современный способ
Object.getPrototypeOf(obj); // Object.prototype
```

### Установка прототипа

```js
const animal = { type: "animal" };
const dog = { name: "Rex" };

// Старый способ (не рекомендуется)
dog.__proto__ = animal;

// Современный способ
Object.setPrototypeOf(dog, animal);

// Или при создании
const dog2 = Object.create(animal);
dog2.name = "Rex";
```

### Зачем нужны прототипы?

1. **Наследование** — объекты могут наследовать свойства и методы
2. **Экономия памяти** — методы хранятся один раз, а не в каждом объекте
3. **Динамичность** — можно добавлять методы в прототип, и все объекты их получат

### Итоги

- Прототип — механизм наследования в JavaScript
- Если свойства нет в объекте, поиск идёт в прототипе
- Методы обычно хранят в прототипе
- Используйте `Object.getPrototypeOf()` вместо `__proto__`

---

## 9. `__proto__`, `[[Prototype]]`, `Object.getPrototypeOf`

Это три способа работы с прототипом объекта. Давайте разберёмся в их различиях.

### `[[Prototype]]` — внутреннее свойство

`[[Prototype]]` — это **внутреннее (скрытое) свойство** объекта, которое хранит ссылку на прототип. К нему **нельзя обратиться напрямую** в коде.

Это часть спецификации ECMAScript.

### `__proto__` — устаревший способ доступа

`__proto__` — это **геттер/сеттер** для доступа к `[[Prototype]]`. Это **устаревший способ**, который:

- Работает, но не рекомендуется
- Может быть медленнее
- Не всегда доступен (например, `Object.create(null)`)

```js
const obj = {};

obj.__proto__; // Object.prototype (устаревший способ)
obj.__proto__ === Object.prototype; // true
```

### `Object.getPrototypeOf()` — современный способ

`Object.getPrototypeOf()` — **рекомендуемый способ** получить прототип:

```js
const obj = {};

Object.getPrototypeOf(obj); // Object.prototype ✅
Object.getPrototypeOf(obj) === Object.prototype; // true ✅
```

**Преимущества:**

- Работает везде
- Более явный и понятный
- Рекомендуется стандартом

### Сравнение способов

```js
const obj = {};

// Все три способа дают один результат
obj.__proto__ === Object.getPrototypeOf(obj); // true
Object.getPrototypeOf(obj) === Object.prototype; // true
```

### Установка прототипа

#### `__proto__` (не рекомендуется)

```js
const parent = { x: 1 };
const child = {};

child.__proto__ = parent; // ⚠️ устаревший способ
console.log(child.x); // 1
```

#### `Object.setPrototypeOf()` (рекомендуется)

```js
const parent = { x: 1 };
const child = {};

Object.setPrototypeOf(child, parent); // ✅ современный способ
console.log(child.x); // 1
```

#### `Object.create()` (лучший способ при создании)

```js
const parent = { x: 1 };
const child = Object.create(parent); // ✅ создаёт объект с прототипом

console.log(child.x); // 1
```

### Когда что использовать?

- **`[[Prototype]]`** — это внутреннее свойство, к нему нельзя обратиться
- **`__proto__`** — избегайте, устаревший способ
- **`Object.getPrototypeOf()`** — используйте для получения прототипа
- **`Object.setPrototypeOf()`** — используйте для установки прототипа
- **`Object.create()`** — используйте при создании объекта с прототипом

### ⚠️ Важно

Изменение прототипа **медленная операция** и может влиять на производительность:

```js
// Медленно ❌
Object.setPrototypeOf(obj, newProto);

// Быстро ✅
const obj = Object.create(newProto);
```

### Итоги

- `[[Prototype]]` — внутреннее свойство, недоступно напрямую
- `__proto__` — устаревший способ доступа
- `Object.getPrototypeOf()` — рекомендуемый способ получения прототипа
- `Object.setPrototypeOf()` — для установки прототипа
- `Object.create()` — лучший способ при создании объекта

---

## 10. Цепочка прототипов

Цепочка прототипов (prototype chain) — это путь, по которому JavaScript ищет свойства объекта, переходя от объекта к прототипу, затем к прототипу прототипа и так далее.

### Как это работает?

Когда вы обращаетесь к свойству объекта, JavaScript:

1. Ищет свойство **в самом объекте**
2. Если не находит — идёт в **прототип объекта**
3. Если не находит — идёт в **прототип прототипа**
4. Продолжает до конца цепочки
5. Если не находит — возвращает `undefined`

### Пример цепочки

```js
function Animal(name) {
  this.name = name;
}

Animal.prototype.eat = function () {
  console.log(this.name + " ест");
};

function Dog(name, breed) {
  Animal.call(this, name);
  this.breed = breed;
}

Dog.prototype = Object.create(Animal.prototype);
Dog.prototype.constructor = Dog;

Dog.prototype.bark = function () {
  console.log(this.name + " лает");
};

const rex = new Dog("Rex", "Labrador");

// Цепочка прототипов:
// rex → Dog.prototype → Animal.prototype → Object.prototype → null

rex.bark(); // "Rex лает" (из Dog.prototype)
rex.eat(); // "Rex ест" (из Animal.prototype)
rex.toString(); // "[object Object]" (из Object.prototype)
```

### Визуализация цепочки

```
rex (объект)
  ↓ __proto__
Dog.prototype
  ↓ __proto__
Animal.prototype
  ↓ __proto__
Object.prototype
  ↓ __proto__
null
```

### Поиск свойства

```js
const obj = { x: 1 };

// Поиск свойства y:
// 1. obj — нет y
// 2. Object.prototype — нет y
// 3. null — конец цепочки
// Результат: undefined

console.log(obj.y); // undefined
```

### Собственные vs унаследованные свойства

```js
function Parent() {
  this.own = "собственное";
}

Parent.prototype.inherited = "унаследованное";

const child = new Parent();

console.log(child.own); // "собственное" (своё свойство)
console.log(child.inherited); // "унаследованное" (из прототипа)

child.hasOwnProperty("own"); // true ✅
child.hasOwnProperty("inherited"); // false ✅ (из прототипа)
```

### Переопределение свойства

Если свойство есть и в объекте, и в прототипе, используется **собственное**:

```js
function Parent() {}
Parent.prototype.value = "прототип";

const child = new Parent();
child.value = "собственное";

console.log(child.value); // "собственное" (переопределило)
```

### Зачем нужна цепочка прототипов?

1. **Наследование** — объекты наследуют свойства и методы
2. **Экономия памяти** — методы хранятся один раз
3. **Гибкость** — можно добавлять методы в прототип

### Итоги

- Цепочка прототипов — путь поиска свойств от объекта до null
- JavaScript ищет свойства по цепочке снизу вверх
- Собственные свойства имеют приоритет
- Цепочка заканчивается на `null`

---

## 11. Что находится в конце цепочки прототипов

В конце цепочки прототипов всегда находится **`null`**.

### Структура цепочки

Цепочка прототипов всегда заканчивается на `null`:

```js
const obj = {};

// Цепочка:
// obj → Object.prototype → null

Object.getPrototypeOf(obj) === Object.prototype; // true
Object.getPrototypeOf(Object.prototype) === null; // true ✅
```

### Проверка конца цепочки

```js
function getPrototypeChain(obj) {
  const chain = [];
  let current = obj;

  while (current !== null) {
    chain.push(current);
    current = Object.getPrototypeOf(current);
  }

  return chain;
}

const obj = {};
const chain = getPrototypeChain(obj);
// [obj, Object.prototype]
// null — конец цепочки
```

### Почему `null`?

`null` используется как маркер **конца цепочки**. Когда JavaScript доходит до `null`, он понимает, что свойство не найдено, и возвращает `undefined`.

### Пример для разных объектов

```js
// Обычный объект
const obj = {};
// obj → Object.prototype → null

// Массив
const arr = [];
// arr → Array.prototype → Object.prototype → null

// Функция
function fn() {}
// fn → Function.prototype → Object.prototype → null

// Строка (при использовании как объект)
const str = "hello";
// str (примитив) → String.prototype → Object.prototype → null
```

### `Object.prototype` — последний реальный объект

`Object.prototype` — это **последний объект** в цепочке, его прототип — `null`:

```js
Object.getPrototypeOf(Object.prototype); // null ✅
Object.prototype.__proto__; // null ✅
```

### Что в `Object.prototype`?

`Object.prototype` содержит базовые методы для всех объектов:

```js
Object.prototype.toString; // метод toString
Object.prototype.valueOf; // метод valueOf
Object.prototype.hasOwnProperty; // метод hasOwnProperty
// и другие...
```

### Итоги

- В конце цепочки прототипов всегда `null`
- `Object.prototype` — последний объект (его прототип — `null`)
- `null` — маркер конца цепочки
- Когда JavaScript доходит до `null`, возвращает `undefined`

---

## 12. Поверхностное и глубокое копирование

При копировании объектов важно понимать разницу между поверхностным и глубоким копированием.

### Поверхностное копирование (Shallow Copy)

Поверхностное копирование создаёт новый объект и копирует только **первый уровень** свойств. Вложенные объекты **не копируются**, а передаются по ссылке.

```js
const original = {
  name: "Alex",
  age: 25,
  address: {
    city: "Moscow",
    street: "Lenin St",
  },
};

// Поверхностная копия
const shallow = Object.assign({}, original);
// или
const shallow2 = { ...original };

shallow.name = "Bob";
shallow.address.city = "SPB";

console.log(original.name); // "Alex" ✅ (не изменилось)
console.log(original.address.city); // "SPB" ⚠️ (изменилось!)
```

**Проблема:** изменения во вложенных объектах влияют на оригинал.

### Глубокое копирование (Deep Copy)

Глубокое копирование создаёт **полностью независимую копию** со всеми вложенными объектами.

```js
const original = {
  name: "Alex",
  address: {
    city: "Moscow",
  },
};

// Глубокая копия
const deep = structuredClone(original); // современный способ

deep.address.city = "SPB";

console.log(original.address.city); // "Moscow" ✅ (не изменилось!)
console.log(deep.address.city); // "SPB" ✅
```

### Визуальная аналогия

- **Поверхностное** — как ксерокопия первой страницы документа, остальные страницы — ссылки на оригинал
- **Глубокое** — как полная ксерокопия всего документа со всеми страницами

### Способы поверхностного копирования

```js
const obj = { a: 1, b: { c: 2 } };

// 1. Object.assign
const copy1 = Object.assign({}, obj);

// 2. Spread operator
const copy2 = { ...obj };

// 3. Array.from (для массивов)
const arr = [1, 2, { x: 3 }];
const copy3 = Array.from(arr);
// или
const copy4 = [...arr];
```

### Способы глубокого копирования

#### 1. `structuredClone()` (ES2022, рекомендуемый)

```js
const obj = { a: 1, b: { c: 2 } };
const deep = structuredClone(obj);
```

**Преимущества:**

- Простой синтаксис
- Копирует большинство типов данных
- Работает с циклическими ссылками

**Ограничения:**

- Не копирует функции
- Не копирует Symbol

#### 2. `JSON.parse(JSON.stringify())`

```js
const obj = { a: 1, b: { c: 2 } };
const deep = JSON.parse(JSON.stringify(obj));
```

**Ограничения:**

- Не копирует функции
- Не копирует `undefined`
- Не копирует Symbol
- Не копирует Date (превращает в строку)
- Не работает с циклическими ссылками

#### 3. Рекурсивная функция (для сложных случаев)

```js
function deepClone(obj) {
  if (obj === null || typeof obj !== "object") {
    return obj;
  }

  if (obj instanceof Date) {
    return new Date(obj);
  }

  if (obj instanceof Array) {
    return obj.map((item) => deepClone(item));
  }

  const cloned = {};
  for (let key in obj) {
    if (obj.hasOwnProperty(key)) {
      cloned[key] = deepClone(obj[key]);
    }
  }

  return cloned;
}
```

### Когда что использовать?

- **Поверхностное копирование** — когда нет вложенных объектов или они не должны изменяться
- **Глубокое копирование** — когда нужна полностью независимая копия со всеми вложенными объектами

### ⚠️ Частая ошибка

```js
const original = { a: 1, b: { c: 2 } };

// Кажется, что это глубокая копия, но это поверхностная!
const copy = { ...original };
copy.b.c = 3;

console.log(original.b.c); // 3 ⚠️ (изменился!)
```

### Итоги

- Поверхностное копирование — только первый уровень, вложенные объекты по ссылке
- Глубокое копирование — полностью независимая копия
- Используйте `structuredClone()` для глубокого копирования
- Spread и Object.assign — поверхностное копирование

---

## 13. Способы копирования объектов

В JavaScript есть несколько способов копирования объектов. Выбор зависит от того, нужна ли вам поверхностная или глубокая копия.

### Поверхностное копирование

#### 1. Spread operator (`...`)

Самый популярный и читаемый способ:

```js
const obj = { name: "Alex", age: 25 };
const copy = { ...obj };

copy.name = "Bob";
console.log(obj.name); // "Alex" ✅ (не изменилось)
```

#### 2. `Object.assign()`

Создаёт поверхностную копию:

```js
const obj = { name: "Alex", age: 25 };
const copy = Object.assign({}, obj);

// Можно объединить несколько объектов
const merged = Object.assign({}, obj1, obj2, obj3);
```

#### 3. `Array.from()` / Spread для массивов

```js
const arr = [1, 2, 3];
const copy1 = Array.from(arr);
const copy2 = [...arr];
```

### Глубокое копирование

#### 1. `structuredClone()` (ES2022, рекомендуемый)

```js
const obj = { a: 1, b: { c: 2 } };
const deep = structuredClone(obj);
```

#### 2. `JSON.parse(JSON.stringify())`

```js
const obj = { a: 1, b: { c: 2 } };
const deep = JSON.parse(JSON.stringify(obj));
```

**⚠️ Ограничения:** не копирует функции, `undefined`, Symbol, Date (см. тему 43).

#### 3. Рекурсивная функция

Для сложных случаев с функциями и другими типами данных.

### Сравнительная таблица

| Способ                         | Тип копирования | Скорость | Ограничения                     |
| ------------------------------ | --------------- | -------- | ------------------------------- |
| `{ ...obj }`                   | Поверхностная   | Быстро   | Вложенные объекты по ссылке     |
| `Object.assign()`              | Поверхностная   | Быстро   | Вложенные объекты по ссылке     |
| `structuredClone()`            | Глубокая        | Средне   | Не копирует функции, Symbol     |
| `JSON.parse(JSON.stringify())` | Глубокая        | Медленно | Много ограничений (см. тему 43) |

### Когда что использовать?

- **Spread / Object.assign** — когда нет вложенных объектов или они не должны изменяться
- **structuredClone()** — когда нужна глубокая копия (большинство случаев)
- **JSON способ** — только для простых данных без функций

### Итоги

- Поверхностное: spread, Object.assign
- Глубокое: structuredClone(), JSON способ
- Выбор зависит от структуры данных

---

## 14. Как сделать глубокую копию объекта

Глубокая копия нужна, когда нужно создать полностью независимую копию объекта со всеми вложенными объектами.

### Способ 1: `structuredClone()` (рекомендуемый, ES2022)

Самый простой и современный способ:

```js
const original = {
  name: "Alex",
  address: {
    city: "Moscow",
    street: "Lenin St",
  },
  hobbies: ["reading", "coding"],
};

const deep = structuredClone(original);

deep.address.city = "SPB";
deep.hobbies.push("gaming");

console.log(original.address.city); // "Moscow" ✅
console.log(original.hobbies); // ["reading", "coding"] ✅
```

**Что копирует:**

- Объекты и массивы
- Примитивы
- Date (как Date объект)
- RegExp
- Map, Set
- Циклические ссылки

**Что НЕ копирует:**

- Функции
- Symbol

### Способ 2: `JSON.parse(JSON.stringify())`

Работает, но с ограничениями:

```js
const original = { a: 1, b: { c: 2 } };
const deep = JSON.parse(JSON.stringify(original));
```

**Проблемы:**

- Функции теряются
- `undefined` теряется
- Symbol теряется
- Date превращается в строку
- Не работает с циклическими ссылками

### Способ 3: Рекурсивная функция

Для случаев, когда нужны функции или другие особые типы:

```js
function deepClone(obj) {
  // Примитивы и null
  if (obj === null || typeof obj !== "object") {
    return obj;
  }

  // Date
  if (obj instanceof Date) {
    return new Date(obj.getTime());
  }

  // Массивы
  if (Array.isArray(obj)) {
    return obj.map((item) => deepClone(item));
  }

  // Объекты
  const cloned = {};
  for (let key in obj) {
    if (obj.hasOwnProperty(key)) {
      cloned[key] = deepClone(obj[key]);
    }
  }

  return cloned;
}

const original = {
  name: "Alex",
  date: new Date(),
  arr: [1, { x: 2 }],
};

const deep = deepClone(original);
```

### Способ 4: Библиотеки (Lodash)

Для продакшена часто используют библиотеки:

```js
import _ from "lodash";

const deep = _.cloneDeep(original);
```

### Сравнение способов

```js
const obj = {
  a: 1,
  b: { c: 2 },
  fn: function () {},
  date: new Date(),
  sym: Symbol("test"),
  undef: undefined,
};

// structuredClone
const clone1 = structuredClone(obj);
// Результат: a, b, date ✅ (но нет fn, sym, undef)

// JSON способ
const clone2 = JSON.parse(JSON.stringify(obj));
// Результат: только a, b ✅ (date → строка, остальное потеряно)

// Рекурсивная функция
const clone3 = deepClone(obj);
// Результат: все кроме Symbol (нужно добавить обработку)
```

### ⚠️ Частая ошибка

Попытка сделать глубокую копию через spread:

```js
const original = { a: { b: 1 } };
const copy = { ...original }; // поверхностная копия!

copy.a.b = 2;
console.log(original.a.b); // 2 ⚠️ (изменилось!)
```

### Итоги

- `structuredClone()` — лучший современный способ
- JSON способ — только для простых данных
- Рекурсивная функция — для особых случаев
- Библиотеки — для сложных проектов

---

## 15. Ограничения `JSON.parse(JSON.stringify())`

Метод `JSON.parse(JSON.stringify())` — простой способ глубокого копирования, но у него много ограничений. Важно их знать!

### Что работает

```js
const obj = {
  name: "Alex",
  age: 25,
  nested: { x: 1 },
  arr: [1, 2, 3],
};

const copy = JSON.parse(JSON.stringify(obj));
// ✅ Копирует: объекты, массивы, примитивы (string, number, boolean, null)
```

### Ограничение 1: Функции теряются

```js
const obj = {
  name: "Alex",
  greet: function () {
    return "Hello";
  },
};

const copy = JSON.parse(JSON.stringify(obj));
console.log(copy.greet); // undefined ❌ (функция потеряна!)
```

### Ограничение 2: `undefined` теряется

```js
const obj = {
  name: "Alex",
  age: undefined,
};

const copy = JSON.parse(JSON.stringify(obj));
console.log(copy.age); // undefined (но свойство отсутствует!)
console.log("age" in copy); // false ❌ (свойство удалено!)
```

### Ограничение 3: Symbol теряется

```js
const sym = Symbol("test");
const obj = {
  name: "Alex",
  [sym]: "value",
};

const copy = JSON.parse(JSON.stringify(obj));
console.log(copy[sym]); // undefined ❌ (Symbol потерян!)
```

### Ограничение 4: Date превращается в строку

```js
const obj = {
  name: "Alex",
  date: new Date(),
};

const copy = JSON.parse(JSON.stringify(obj));
console.log(copy.date); // "2024-01-01T00:00:00.000Z" ❌ (строка, не Date!)
console.log(copy.date instanceof Date); // false
```

### Ограничение 5: Циклические ссылки вызывают ошибку

```js
const obj = { name: "Alex" };
obj.self = obj; // циклическая ссылка

JSON.parse(JSON.stringify(obj)); // TypeError ❌ (не может сериализовать!)
```

### Ограничение 6: `NaN`, `Infinity`, `-Infinity` превращаются в `null`

```js
const obj = {
  a: NaN,
  b: Infinity,
  c: -Infinity,
};

const copy = JSON.parse(JSON.stringify(obj));
console.log(copy.a); // null ❌
console.log(copy.b); // null ❌
console.log(copy.c); // null ❌
```

### Ограничение 7: Map и Set теряются

```js
const obj = {
  map: new Map([["key", "value"]]),
  set: new Set([1, 2, 3]),
};

const copy = JSON.parse(JSON.stringify(obj));
console.log(copy.map); // {} ❌ (пустой объект)
console.log(copy.set); // {} ❌ (пустой объект)
```

### Когда использовать?

Используйте `JSON.parse(JSON.stringify())` только если:

- ✅ Нет функций
- ✅ Нет `undefined` (или не важно)
- ✅ Нет Symbol
- ✅ Нет Date (или можно преобразовать)
- ✅ Нет циклических ссылок
- ✅ Только простые данные (объекты, массивы, примитивы)

### Альтернатива: `structuredClone()`

Для большинства случаев лучше использовать `structuredClone()`:

```js
const obj = {
  date: new Date(),
  map: new Map([["key", "value"]]),
  nested: { x: 1 },
};

const copy = structuredClone(obj); // ✅ Работает лучше!
```

### Итоги

- `JSON.parse(JSON.stringify())` имеет много ограничений
- Не работает с функциями, `undefined`, Symbol, Date, циклическими ссылками
- Используйте только для простых данных
- Для сложных случаев — `structuredClone()` или библиотеки

---

## 16. Какие типы данных теряются при `JSON.stringify`

`JSON.stringify()` может сериализовать только ограниченный набор типов данных. Всё остальное теряется или преобразуется.

### Что сохраняется

```js
const obj = {
  string: "text",
  number: 42,
  boolean: true,
  null: null,
  array: [1, 2, 3],
  object: { nested: "value" },
};

JSON.stringify(obj);
// ✅ Все эти типы сохраняются
```

### Что теряется или преобразуется

#### 1. `undefined` — полностью удаляется

```js
const obj = { a: 1, b: undefined };
JSON.stringify(obj); // '{"a":1}' ❌ (b удалено!)
```

#### 2. Функции — удаляются

```js
const obj = { a: 1, fn: function () {} };
JSON.stringify(obj); // '{"a":1}' ❌ (fn удалено!)
```

#### 3. Symbol — удаляется

```js
const sym = Symbol("test");
const obj = { a: 1, [sym]: "value" };
JSON.stringify(obj); // '{"a":1}' ❌ (Symbol удалён!)
```

#### 4. Date — превращается в строку

```js
const obj = { date: new Date() };
JSON.stringify(obj);
// '{"date":"2024-01-01T00:00:00.000Z"}' ❌ (строка, не Date!)
```

#### 5. `NaN`, `Infinity`, `-Infinity` — превращаются в `null`

```js
const obj = { a: NaN, b: Infinity, c: -Infinity };
JSON.stringify(obj);
// '{"a":null,"b":null,"c":null}' ❌
```

#### 6. Map, Set — становятся пустыми объектами

```js
const obj = {
  map: new Map([["key", "value"]]),
  set: new Set([1, 2, 3]),
};

JSON.stringify(obj);
// '{"map":{},"set":{}}' ❌ (пустые объекты!)
```

#### 7. RegExp — становится пустым объектом

```js
const obj = { regex: /test/gi };
JSON.stringify(obj);
// '{"regex":{}}' ❌
```

#### 8. Циклические ссылки — ошибка

```js
const obj = { name: "Alex" };
obj.self = obj;

JSON.stringify(obj); // TypeError ❌ (не может сериализовать!)
```

### Таблица потери данных

| Тип данных                    | Что происходит           |
| ----------------------------- | ------------------------ |
| `string`, `number`, `boolean` | ✅ Сохраняется           |
| `null`                        | ✅ Сохраняется           |
| `Array`, `Object`             | ✅ Сохраняется           |
| `undefined`                   | ❌ Удаляется             |
| `Function`                    | ❌ Удаляется             |
| `Symbol`                      | ❌ Удаляется             |
| `Date`                        | ❌ Превращается в строку |
| `NaN`, `Infinity`             | ❌ Превращается в `null` |
| `Map`, `Set`                  | ❌ Пустой объект `{}`    |
| `RegExp`                      | ❌ Пустой объект `{}`    |
| Циклические ссылки            | ❌ Ошибка                |

### Как сохранить Date?

Можно использовать `replacer` функцию:

```js
const obj = { date: new Date() };

JSON.stringify(obj, (key, value) => {
  if (value instanceof Date) {
    return value.toISOString(); // или value.getTime()
  }
  return value;
});
```

Но при `JSON.parse()` это всё равно будет строка, не Date.

### Итоги

- `JSON.stringify()` сохраняет только: объекты, массивы, примитивы
- Теряет: функции, `undefined`, Symbol
- Преобразует: Date → строка, NaN/Infinity → null, Map/Set → {}
- Для сохранения всех типов используйте `structuredClone()` или библиотеки

---

## 17. Мутация и иммутабельность

Мутация и иммутабельность — важные концепции в программировании, особенно в функциональном программировании и современных фреймворках.

### Что такое мутация?

**Мутация** (mutation) — это изменение существующего объекта или массива.

```js
const obj = { name: "Alex" };
obj.name = "Bob"; // мутация — изменён существующий объект

const arr = [1, 2, 3];
arr.push(4); // мутация — изменён существующий массив
```

### Что такое иммутабельность?

**Иммутабельность** (immutability) — создание нового объекта/массива вместо изменения существующего.

```js
const obj = { name: "Alex" };
const newObj = { ...obj, name: "Bob" }; // новый объект

const arr = [1, 2, 3];
const newArr = [...arr, 4]; // новый массив
```

### Примитивы — неизменяемы

Примитивы в JavaScript **всегда неизменяемы**:

```js
let str = "hello";
str.toUpperCase(); // создаёт новую строку
console.log(str); // "hello" ✅ (не изменилась)

let num = 5;
num = 6; // это не мутация! Переменная получила новое значение
```

### Объекты и массивы — изменяемы

Объекты и массивы **изменяемы по умолчанию**:

```js
const obj = { count: 0 };
obj.count = 1; // мутация ✅

const arr = [1, 2];
arr.push(3); // мутация ✅
```

### Почему важна иммутабельность?

1. **Предсказуемость** — данные не изменяются неожиданно
2. **Отладка** — проще отслеживать изменения
3. **Реактивность** — фреймворки (React, Vue) отслеживают изменения через сравнение ссылок
4. **Параллелизм** — безопасность при многопоточности

### Пример проблемы с мутацией

```js
const original = { name: "Alex", age: 25 };
const copy = original; // копия ссылки

copy.age = 30; // мутация
console.log(original.age); // 30 ⚠️ (изменился оригинал!)
```

### Иммутабельное обновление

```js
const original = { name: "Alex", age: 25 };
const updated = { ...original, age: 30 }; // новый объект

console.log(original.age); // 25 ✅ (не изменился)
console.log(updated.age); // 30 ✅
```

### Иммутабельная работа с массивами

#### Вместо мутации:

```js
// ❌ Мутация
const arr = [1, 2, 3];
arr.push(4);
arr.pop();
arr.sort();

// ✅ Иммутабельно
const arr = [1, 2, 3];
const withFour = [...arr, 4]; // новый массив
const withoutLast = arr.slice(0, -1); // новый массив
const sorted = [...arr].sort(); // новый массив
```

### Иммутабельные методы массивов

Методы, которые **не изменяют** исходный массив (возвращают новый):

```js
const arr = [1, 2, 3];

arr.map((x) => x * 2); // [2, 4, 6] ✅ (новый массив)
arr.filter((x) => x > 1); // [2, 3] ✅ (новый массив)
arr.slice(0, 2); // [1, 2] ✅ (новый массив)
arr.concat([4]); // [1, 2, 3, 4] ✅ (новый массив)
```

### Мутирующие методы массивов

Методы, которые **изменяют** исходный массив:

```js
const arr = [1, 2, 3];

arr.push(4); // изменяет arr ❌
arr.pop(); // изменяет arr ❌
arr.sort(); // изменяет arr ❌
arr.reverse(); // изменяет arr ❌
```

### Итоги

- Мутация — изменение существующего объекта/массива
- Иммутабельность — создание нового объекта/массива
- Примитивы неизменяемы, объекты изменяемы
- Иммутабельность делает код предсказуемее
- Используйте spread и немутирующие методы для иммутабельности

---

## 18. Методы массивов `map`, `filter`, `reduce`

Три самых важных метода массивов в JavaScript. Они не изменяют исходный массив и очень часто используются.

### `map` — преобразование элементов

`map` создаёт **новый массив**, применяя функцию к каждому элементу:

```js
const numbers = [1, 2, 3, 4];
const doubled = numbers.map((n) => n * 2);
// [2, 4, 6, 8] ✅ (новый массив)

console.log(numbers); // [1, 2, 3, 4] (исходный не изменился)
```

**Синтаксис:**

```js
array.map((element, index, array) => {
  // возвращает новое значение для элемента
});
```

**Примеры:**

```js
// Преобразование объектов
const users = [
  { name: "Alex", age: 25 },
  { name: "Bob", age: 30 },
];

const names = users.map((user) => user.name);
// ["Alex", "Bob"]

// Преобразование типов
const strings = ["1", "2", "3"];
const numbers = strings.map((str) => Number(str));
// [1, 2, 3]
```

### `filter` — фильтрация элементов

`filter` создаёт **новый массив** с элементами, прошедшими проверку:

```js
const numbers = [1, 2, 3, 4, 5];
const even = numbers.filter((n) => n % 2 === 0);
// [2, 4] ✅ (только чётные)

console.log(numbers); // [1, 2, 3, 4, 5] (исходный не изменился)
```

**Синтаксис:**

```js
array.filter((element, index, array) => {
  // возвращает true/false
  // true — элемент остаётся, false — убирается
});
```

**Примеры:**

```js
// Фильтрация по условию
const users = [
  { name: "Alex", age: 25 },
  { name: "Bob", age: 30 },
  { name: "Charlie", age: 20 },
];

const adults = users.filter((user) => user.age >= 25);
// [{ name: "Alex", age: 25 }, { name: "Bob", age: 30 }]

// Удаление undefined/null
const values = [1, null, 2, undefined, 3];
const clean = values.filter((v) => v != null);
// [1, 2, 3]
```

### `reduce` — свёртка массива в одно значение

`reduce` **сводит массив к одному значению**, применяя функцию к каждому элементу:

```js
const numbers = [1, 2, 3, 4];
const sum = numbers.reduce((acc, n) => acc + n, 0);
// 10 ✅

// Пошагово:
// acc = 0, n = 1 → acc = 1
// acc = 1, n = 2 → acc = 3
// acc = 3, n = 3 → acc = 6
// acc = 6, n = 4 → acc = 10
```

**Синтаксис:**

```js
array.reduce((accumulator, element, index, array) => {
  // возвращает новое значение аккумулятора
}, initialValue);
```

**Важно:** если не указать начальное значение, берётся первый элемент (может привести к ошибкам!).

**Примеры:**

```js
// Сумма
const sum = [1, 2, 3].reduce((acc, n) => acc + n, 0); // 6

// Произведение
const product = [2, 3, 4].reduce((acc, n) => acc * n, 1); // 24

// Максимум
const max = [5, 2, 8, 1].reduce((acc, n) => (n > acc ? n : acc), -Infinity); // 8

// Группировка
const items = [
  { type: "fruit", name: "apple" },
  { type: "fruit", name: "banana" },
  { type: "vegetable", name: "carrot" },
];

const grouped = items.reduce((acc, item) => {
  if (!acc[item.type]) {
    acc[item.type] = [];
  }
  acc[item.type].push(item.name);
  return acc;
}, {});
// { fruit: ["apple", "banana"], vegetable: ["carrot"] }
```

### Цепочки методов

Методы можно объединять в цепочки:

```js
const numbers = [1, 2, 3, 4, 5, 6];

const result = numbers
  .filter((n) => n % 2 === 0) // [2, 4, 6]
  .map((n) => n * 2) // [4, 8, 12]
  .reduce((acc, n) => acc + n, 0); // 24
```

### Сравнение методов

| Метод    | Что возвращает                  | Изменяет исходный? |
| -------- | ------------------------------- | ------------------ |
| `map`    | Новый массив (та же длина)      | ❌ Нет             |
| `filter` | Новый массив (меньше или равно) | ❌ Нет             |
| `reduce` | Одно значение                   | ❌ Нет             |

### ⚠️ Частая ошибка с `reduce`

Забывают начальное значение:

```js
const numbers = [1, 2, 3];

// Без начального значения (работает, но рискованно)
const sum1 = numbers.reduce((acc, n) => acc + n); // 6

// С начальным значением (правильно)
const sum2 = numbers.reduce((acc, n) => acc + n, 0); // 6 ✅

// Проблема с пустым массивом
[].reduce((acc, n) => acc + n); // TypeError ❌
[].reduce((acc, n) => acc + n, 0); // 0 ✅
```

### Итоги

- `map` — преобразует каждый элемент (новая длина = старая)
- `filter` — отбирает элементы по условию (новая длина ≤ старая)
- `reduce` — сводит массив к одному значению
- Все три метода не изменяют исходный массив
- Можно объединять в цепочки

---

## 19. Разница между `map` и `forEach`

`map` и `forEach` похожи, но имеют важные отличия. Часто их путают новички.

### `map` — возвращает новый массив

`map` **возвращает новый массив** с преобразованными элементами:

```js
const numbers = [1, 2, 3];
const doubled = numbers.map((n) => n * 2);

console.log(doubled); // [2, 4, 6] ✅ (новый массив)
console.log(numbers); // [1, 2, 3] (исходный не изменился)
```

### `forEach` — ничего не возвращает

`forEach` **не возвращает значение** (возвращает `undefined`):

```js
const numbers = [1, 2, 3];
const result = numbers.forEach((n) => n * 2);

console.log(result); // undefined ❌
```

### Когда использовать `map`?

Используйте `map`, когда нужно:

- ✅ Преобразовать массив
- ✅ Получить новый массив с результатами
- ✅ Объединить в цепочку методов

```js
const users = [{ name: "Alex" }, { name: "Bob" }];

const names = users.map((user) => user.name);
// ["Alex", "Bob"]

// В цепочке
users
  .map((user) => user.name)
  .filter((name) => name.length > 3)
  .map((name) => name.toUpperCase());
```

### Когда использовать `forEach`?

Используйте `forEach`, когда нужно:

- ✅ Выполнить действие для каждого элемента
- ✅ Вывести в консоль
- ✅ Изменить внешние переменные
- ✅ Вызвать функцию с побочными эффектами

```js
const numbers = [1, 2, 3];

// Вывод в консоль
numbers.forEach((n) => console.log(n));

// Изменение внешней переменной
let sum = 0;
numbers.forEach((n) => {
  sum += n;
});
console.log(sum); // 6

// Побочные эффекты
numbers.forEach((n) => {
  updateDatabase(n); // действие с побочным эффектом
});
```

### Сравнительная таблица

| Особенность                | `map`           | `forEach`                 |
| -------------------------- | --------------- | ------------------------- |
| Возвращает значение        | ✅ Новый массив | ❌ `undefined`            |
| Изменяет исходный массив   | ❌ Нет          | ❌ Нет                    |
| Использование              | Преобразование  | Действия/побочные эффекты |
| Можно объединять в цепочки | ✅ Да           | ❌ Нет                    |

### ⚠️ Частая ошибка

Попытка использовать `forEach` для преобразования:

```js
const numbers = [1, 2, 3];

// Неправильно
const doubled = numbers.forEach((n) => n * 2);
console.log(doubled); // undefined ❌

// Правильно
const doubled = numbers.map((n) => n * 2);
console.log(doubled); // [2, 4, 6] ✅
```

### Можно ли использовать `map` для побочных эффектов?

Технически да, но **не рекомендуется**:

```js
// Плохо ❌ (map для побочных эффектов)
numbers.map((n) => console.log(n));

// Хорошо ✅ (forEach для побочных эффектов)
numbers.forEach((n) => console.log(n));
```

**Почему?** `map` предназначен для преобразования, использование для побочных эффектов запутывает код.

### Производительность

`map` и `forEach` имеют **одинаковую производительность**. Разница незначительна.

### Итоги

- `map` — возвращает новый массив (для преобразования)
- `forEach` — ничего не возвращает (для действий)
- `map` — для получения нового массива
- `forEach` — для побочных эффектов
- Не путайте их назначение!

---

## 20. Методы массива `find`, `some`, `every`

Эти три метода помогают проверять элементы массива и находить нужные значения.

### `find` — находит первый элемент

`find` возвращает **первый элемент**, который соответствует условию:

```js
const users = [
  { id: 1, name: "Alex", age: 25 },
  { id: 2, name: "Bob", age: 30 },
  { id: 3, name: "Charlie", age: 25 },
];

const user = users.find((u) => u.age === 25);
// { id: 1, name: "Alex", age: 25 } ✅ (первый найденный)

const notFound = users.find((u) => u.age > 100);
// undefined ✅ (не найдено)
```

**Синтаксис:**

```js
array.find((element, index, array) => {
  // возвращает true/false
  // true — элемент найден, возвращается element
});
```

### `some` — проверяет, есть ли хотя бы один

`some` возвращает `true`, если **хотя бы один элемент** соответствует условию:

```js
const numbers = [1, 2, 3, 4, 5];

numbers.some((n) => n > 3); // true ✅ (есть элементы > 3)
numbers.some((n) => n > 10); // false ✅ (нет элементов > 10)
```

**Синтаксис:**

```js
array.some((element, index, array) => {
  // возвращает true/false
});
```

**Примеры:**

```js
const users = [
  { name: "Alex", active: true },
  { name: "Bob", active: false },
  { name: "Charlie", active: false },
];

const hasActive = users.some((u) => u.active);
// true ✅ (есть хотя бы один активный)

// Проверка на наличие
const hasAdmin = users.some((u) => u.role === "admin");
```

### `every` — проверяет, все ли соответствуют

`every` возвращает `true`, если **все элементы** соответствуют условию:

```js
const numbers = [2, 4, 6, 8];

numbers.every((n) => n % 2 === 0); // true ✅ (все чётные)
numbers.every((n) => n > 5); // false ✅ (не все > 5)
```

**Синтаксис:**

```js
array.every((element, index, array) => {
  // возвращает true/false
});
```

**Примеры:**

```js
const users = [{ age: 25 }, { age: 30 }, { age: 28 }];

const allAdults = users.every((u) => u.age >= 18);
// true ✅ (всем есть 18+)

// Валидация формы
const fields = ["name", "email", "password"];
const allFilled = fields.every((field) => form[field].length > 0);
```

### Сравнительная таблица

| Метод   | Что возвращает          | Когда возвращает `true`         |
| ------- | ----------------------- | ------------------------------- |
| `find`  | Элемент или `undefined` | Когда находит первый подходящий |
| `some`  | `true` или `false`      | Когда хотя бы один подходит     |
| `every` | `true` или `false`      | Когда все подходят              |

### Особенности

#### Пустой массив

```js
[].some(() => true); // false ✅ (нет элементов для проверки)
[].every(() => true); // true ✅ (все 0 элементов соответствуют!)
```

**Почему `every` возвращает `true` для пустого массива?** Это математическое правило: для пустого множества все элементы соответствуют любому условию (vacuous truth).

#### Прерывание выполнения

Все три метода **прерывают выполнение**, когда находят результат:

```js
const numbers = [1, 2, 3, 4, 5];

// some прервётся на первом true
numbers.some((n) => {
  console.log(n); // 1, 2 (прервалось на 2, т.к. 2 > 1)
  return n > 1;
});

// every прервётся на первом false
numbers.every((n) => {
  console.log(n); // 1 (прервалось на 1, т.к. 1 не > 1)
  return n > 1;
});
```

### Примеры использования

```js
const products = [
  { name: "Laptop", price: 1000, inStock: true },
  { name: "Phone", price: 500, inStock: false },
  { name: "Tablet", price: 300, inStock: true },
];

// Найти первый доступный товар
const available = products.find((p) => p.inStock);
// { name: "Laptop", price: 1000, inStock: true }

// Проверить, есть ли дорогие товары (> 800)
const hasExpensive = products.some((p) => p.price > 800);
// true

// Проверить, все ли товары доступны
const allAvailable = products.every((p) => p.inStock);
// false
```

### Разница с `filter`

```js
const numbers = [1, 2, 3, 4, 5];

// find — первый элемент
numbers.find((n) => n > 2); // 3 ✅ (один элемент)

// filter — все подходящие элементы
numbers.filter((n) => n > 2); // [3, 4, 5] ✅ (массив)
```

### Итоги

- `find` — находит первый подходящий элемент (или `undefined`)
- `some` — проверяет, есть ли хотя бы один (`true`/`false`)
- `every` — проверяет, все ли подходят (`true`/`false`)
- Все три прерывают выполнение при нахождении результата
- `every` возвращает `true` для пустого массива

---

## 21. Мутирующие и немутирующие методы массивов

Важно знать, какие методы массивов изменяют исходный массив, а какие создают новый.

### Немутирующие методы (не изменяют исходный массив)

Эти методы **возвращают новый массив** или значение:

```js
const arr = [1, 2, 3];

arr.map((x) => x * 2); // [2, 4, 6] ✅ (новый массив)
arr.filter((x) => x > 1); // [2, 3] ✅ (новый массив)
arr.slice(0, 2); // [1, 2] ✅ (новый массив)
arr.concat([4]); // [1, 2, 3, 4] ✅ (новый массив)
arr.reduce((a, b) => a + b); // 6 ✅ (значение)

console.log(arr); // [1, 2, 3] ✅ (не изменился)
```

**Список немутирующих методов:**

- `map` — преобразование
- `filter` — фильтрация
- `slice` — копирование части
- `concat` — объединение
- `reduce` / `reduceRight` — свёртка
- `find` / `findIndex` — поиск
- `some` / `every` — проверка
- `indexOf` / `lastIndexOf` — поиск индекса
- `includes` — проверка наличия
- `join` — объединение в строку

### Мутирующие методы (изменяют исходный массив)

Эти методы **изменяют исходный массив**:

```js
const arr = [1, 2, 3];

arr.push(4); // [1, 2, 3, 4] ❌ (изменён)
arr.pop(); // [1, 2, 3] ❌ (изменён)
arr.unshift(0); // [0, 1, 2, 3] ❌ (изменён)
arr.shift(); // [1, 2, 3] ❌ (изменён)
arr.sort(); // ❌ (изменён)
arr.reverse(); // ❌ (изменён)
arr.splice(1, 1); // ❌ (изменён)

console.log(arr); // изменён! ❌
```

**Список мутирующих методов:**

- `push` — добавить в конец
- `pop` — удалить с конца
- `unshift` — добавить в начало
- `shift` — удалить с начала
- `sort` — сортировка
- `reverse` — обратный порядок
- `splice` — удаление/вставка элементов
- `fill` — заполнение
- `copyWithin` — копирование внутри массива

### Как сделать мутирующие методы немутирующими?

#### 1. Создать копию перед изменением

```js
const arr = [3, 1, 2];

// sort — мутирует
arr.sort(); // ❌ изменяет arr

// Правильно — создать копию
const sorted = [...arr].sort(); // ✅ новый массив
console.log(arr); // [3, 1, 2] (не изменился)
```

#### 2. Использовать альтернативы

```js
// push — мутирует
arr.push(4); // ❌

// Альтернатива — concat или spread
const newArr = arr.concat(4); // ✅
// или
const newArr = [...arr, 4]; // ✅

// reverse — мутирует
arr.reverse(); // ❌

// Альтернатива — создать копию
const reversed = [...arr].reverse(); // ✅
```

### ⚠️ Частая ошибка

Использование мутирующих методов, думая, что они немутирующие:

```js
const numbers = [3, 1, 2];

function getSorted(arr) {
  return arr.sort(); // ❌ мутирует исходный массив!
}

const sorted = getSorted(numbers);
console.log(numbers); // [1, 2, 3] ⚠️ (изменился!)
```

**Правильно:**

```js
function getSorted(arr) {
  return [...arr].sort(); // ✅ создаёт копию
}
```

### Особый случай: `sort`

`sort` **всегда мутирует** массив:

```js
const arr = [3, 1, 2];
const sorted = arr.sort();

console.log(arr); // [1, 2, 3] ❌ (изменился!)
console.log(sorted); // [1, 2, 3] (та же ссылка!)

// arr === sorted → true (один и тот же массив!)
```

**Правильно:**

```js
const sorted = [...arr].sort(); // ✅ копия
```

### Когда использовать мутирующие методы?

Мутирующие методы можно использовать, когда:

- Нужно изменить массив на месте (для производительности)
- Массив больше не используется после изменения
- Работа с большими массивами (экономия памяти)

Но в современном JavaScript обычно предпочитают иммутабельный подход.

### Сравнительная таблица

| Метод                    | Тип          | Возвращает                       |
| ------------------------ | ------------ | -------------------------------- |
| `map`, `filter`, `slice` | Немутирующий | Новый массив                     |
| `push`, `pop`, `sort`    | Мутирующий   | Изменённый массив (та же ссылка) |
| `find`, `some`, `every`  | Немутирующий | Значение/boolean                 |

### Итоги

- Немутирующие методы создают новый массив
- Мутирующие методы изменяют исходный массив
- Используйте spread `[...arr]` для копирования перед мутацией
- В современном коде предпочитают немутирующие методы

---

## 22. `Set`, `Map`, `WeakSet`, `WeakMap`

Это специальные коллекции в JavaScript для работы с данными. Каждая имеет свои особенности.

### `Set` — коллекция уникальных значений

`Set` хранит **только уникальные значения** (примитивы или ссылки на объекты):

```js
const set = new Set([1, 2, 2, 3, 3, 3]);
console.log(set); // Set(3) {1, 2, 3} ✅ (дубликаты удалены)

set.add(4);
set.add(2); // не добавится (уже есть)
console.log(set.size); // 4
```

**Методы:**

```js
const set = new Set();

set.add(value); // добавить
set.has(value); // проверить наличие
set.delete(value); // удалить
set.clear(); // очистить
set.size; // размер (не length!)
```

**Примеры использования:**

```js
// Удаление дубликатов из массива
const arr = [1, 2, 2, 3, 3, 3];
const unique = [...new Set(arr)]; // [1, 2, 3] ✅

// Проверка уникальности
const emails = ["a@test.com", "b@test.com", "a@test.com"];
const uniqueEmails = new Set(emails);
if (emails.length !== uniqueEmails.size) {
  console.log("Есть дубликаты!");
}
```

### `Map` — коллекция пар ключ-значение

`Map` хранит пары **ключ-значение**, где ключом может быть **любой тип** (не только строка):

```js
const map = new Map();

map.set("name", "Alex"); // строка как ключ
map.set(1, "one"); // число как ключ
map.set(true, "yes"); // boolean как ключ
map.set({ id: 1 }, "object key"); // объект как ключ! ✅

console.log(map.get("name")); // "Alex"
console.log(map.get(1)); // "one"
```

**Методы:**

```js
const map = new Map();

map.set(key, value); // установить
map.get(key); // получить
map.has(key); // проверить
map.delete(key); // удалить
map.clear(); // очистить
map.size; // размер
```

**Итерация:**

```js
const map = new Map([
  ["a", 1],
  ["b", 2],
]);

map.keys(); // итератор ключей
map.values(); // итератор значений
map.entries(); // итератор пар [key, value]

for (let [key, value] of map) {
  console.log(key, value);
}
```

### `WeakSet` — Set для объектов

`WeakSet` похож на `Set`, но:

- Хранит **только объекты** (не примитивы)
- **Не итерируемый** (нет методов для перебора)
- Не препятствует сборке мусора (weak references)

```js
const weakSet = new WeakSet();

const obj = { id: 1 };
weakSet.add(obj);
weakSet.has(obj); // true
weakSet.delete(obj);

// Нет size, нет итерации!
```

**Использование:** для хранения "меток" объектов (например, обработанные объекты).

### `WeakMap` — Map для объектов как ключей

`WeakMap` похож на `Map`, но:

- Ключами могут быть **только объекты**
- **Не итерируемый**
- Не препятствует сборке мусора

```js
const weakMap = new WeakMap();

const key = { id: 1 };
weakMap.set(key, "value");
weakMap.get(key); // "value"
weakMap.has(key); // true

// Нет size, нет итерации!
```

**Использование:** приватные данные объектов, кэши, метаданные.

### Сравнительная таблица

| Коллекция | Ключи          | Итерация | Weak references |
| --------- | -------------- | -------- | --------------- |
| `Set`     | Любые          | ✅ Да    | ❌ Нет          |
| `Map`     | Любые          | ✅ Да    | ❌ Нет          |
| `WeakSet` | Только объекты | ❌ Нет   | ✅ Да           |
| `WeakMap` | Только объекты | ❌ Нет   | ✅ Да           |

### Когда использовать что?

- **`Set`** — когда нужны уникальные значения
- **`Map`** — когда нужны ключи любого типа (не только строки)
- **`WeakSet`** — для меток объектов (редко)
- **`WeakMap`** — для приватных данных объектов, кэшей

### Пример: `Map` vs обычный объект

```js
// Обычный объект — ключи только строки
const obj = {};
obj[1] = "one";
obj["1"]; // "one" (1 преобразовано в "1")

// Map — ключи сохраняют тип
const map = new Map();
map.set(1, "one");
map.set("1", "one string");
map.get(1); // "one"
map.get("1"); // "one string" ✅ (разные ключи!)
```

### Итоги

- `Set` — уникальные значения
- `Map` — пары ключ-значение, ключи любого типа
- `WeakSet` / `WeakMap` — только объекты, weak references
- `Set`/`Map` — итерируемые, `Weak*` — нет
- Используйте `Set` для уникальности, `Map` для ключей любого типа

---

## 23. Отличие `Map` от обычного объекта

`Map` и обычный объект похожи, но имеют важные отличия. Когда использовать что?

### Основные отличия

#### 1. Типы ключей

**Обычный объект:**

```js
const obj = {};
obj["name"] = "Alex"; // строка
obj[1] = "one"; // число (преобразуется в строку!)
obj["1"] = "one string"; // строка
console.log(obj[1]); // "one string" ⚠️ (1 и "1" — один ключ!)
```

**Map:**

```js
const map = new Map();
map.set("name", "Alex");
map.set(1, "one");
map.set("1", "one string");
console.log(map.get(1)); // "one" ✅
console.log(map.get("1")); // "one string" ✅ (разные ключи!)
```

#### 2. Размер

**Обычный объект:**

```js
const obj = { a: 1, b: 2 };
Object.keys(obj).length; // нужно считать вручную
```

**Map:**

```js
const map = new Map([
  ["a", 1],
  ["b", 2],
]);
map.size; // 2 ✅ (встроенное свойство)
```

#### 3. Итерация

**Обычный объект:**

```js
const obj = { a: 1, b: 2 };
for (let key in obj) {
  console.log(key, obj[key]);
}
// или
Object.entries(obj).forEach(([key, value]) => {});
```

**Map:**

```js
const map = new Map([
  ["a", 1],
  ["b", 2],
]);
for (let [key, value] of map) {
  console.log(key, value);
}
// или
map.forEach((value, key) => {});
```

#### 4. Производительность

**Map** быстрее для частого добавления/удаления элементов.

**Обычный объект** быстрее для редких операций.

#### 5. JSON сериализация

**Обычный объект:**

```js
const obj = { a: 1, b: 2 };
JSON.stringify(obj); // '{"a":1,"b":2}' ✅
```

**Map:**

```js
const map = new Map([
  ["a", 1],
  ["b", 2],
]);
JSON.stringify(map); // '{}' ❌ (пустой объект)
```

### Сравнительная таблица

| Особенность        | Обычный объект                 | Map                      |
| ------------------ | ------------------------------ | ------------------------ |
| Ключи              | Только строки (и Symbol)       | Любой тип                |
| Размер             | `Object.keys().length`         | `size`                   |
| Итерация           | `for...in`, `Object.entries()` | `for...of`, `forEach()`  |
| Производительность | Быстро (редкие операции)       | Быстро (частые операции) |
| JSON               | ✅ Сериализуется               | ❌ Нет                   |
| Прототип           | ✅ Есть                        | ❌ Нет                   |
| Случайные ключи    | ⚠️ Могут конфликтовать         | ✅ Нет проблем           |

### Когда использовать обычный объект?

- ✅ Ключи — строки
- ✅ Нужна JSON сериализация
- ✅ Редкие операции добавления/удаления
- ✅ Простая структура данных

```js
const user = {
  name: "Alex",
  age: 25,
  city: "Moscow",
};
```

### Когда использовать Map?

- ✅ Ключи — не только строки (числа, объекты)
- ✅ Частые операции добавления/удаления
- ✅ Нужно знать размер
- ✅ Нужна итерация в порядке вставки
- ✅ Не нужна JSON сериализация

```js
const cache = new Map();
cache.set(userObject, userData); // объект как ключ
cache.set(123, "value"); // число как ключ
```

### Особенность: порядок в объектах

В современном JavaScript объекты **сохраняют порядок** ключей (ES2015+), но Map более предсказуем в этом плане.

### Итоги

- Обычный объект — ключи только строки, сериализуется в JSON
- Map — ключи любого типа, быстрее для частых операций
- Выбор зависит от задачи

---

## 24. Зачем нужны `WeakMap` и `WeakSet`

`WeakMap` и `WeakSet` — специальные коллекции с **weak references** (слабыми ссылками). Они решают проблемы с памятью.

### Проблема с обычными `Map` и `Set`

Обычные `Map` и `Set` хранят **сильные ссылки** на объекты, что может привести к утечкам памяти:

```js
const map = new Map();
let obj = { data: "важные данные" };

map.set(obj, "метаданные");
obj = null; // объект удалён из переменной

// Но объект НЕ удалён из памяти! Map всё ещё ссылается на него
// Утечка памяти! ❌
```

### Что такое weak references?

**Weak references** (слабые ссылки) — ссылки, которые **не препятствуют сборке мусора**. Если на объект ссылается только WeakMap/WeakSet, объект может быть удалён.

### `WeakMap` — Map со слабыми ссылками

```js
const weakMap = new WeakMap();
let obj = { id: 1 };

weakMap.set(obj, "метаданные");
obj = null; // объект может быть удалён из памяти ✅
// weakMap больше не ссылается на объект
```

**Особенности `WeakMap`:**

- Ключами могут быть **только объекты**
- Не итерируемый (нет `keys()`, `values()`, `entries()`)
- Нет свойства `size`
- Не препятствует сборке мусора

### `WeakSet` — Set со слабыми ссылками

```js
const weakSet = new WeakSet();
let obj = { id: 1 };

weakSet.add(obj);
obj = null; // объект может быть удалён ✅
```

**Особенности `WeakSet`:**

- Хранит **только объекты**
- Не итерируемый
- Нет `size`
- Не препятствует сборке мусора

### Когда использовать `WeakMap`?

#### 1. Приватные данные объектов

```js
const privateData = new WeakMap();

class User {
  constructor(name) {
    privateData.set(this, { name }); // приватные данные
  }

  getName() {
    return privateData.get(this).name;
  }
}

const user = new User("Alex");
user.getName(); // "Alex"
// privateData недоступна извне ✅
```

#### 2. Кэширование

```js
const cache = new WeakMap();

function processObject(obj) {
  if (cache.has(obj)) {
    return cache.get(obj); // вернуть из кэша
  }

  const result = expensiveOperation(obj);
  cache.set(obj, result);
  return result;
}

// Когда объект удаляется, кэш очищается автоматически ✅
```

#### 3. Метаданные объектов

```js
const metadata = new WeakMap();

function markAsProcessed(obj) {
  metadata.set(obj, { processed: true, date: new Date() });
}

function isProcessed(obj) {
  return metadata.has(obj) && metadata.get(obj).processed;
}
```

### Когда использовать `WeakSet`?

#### 1. Отслеживание объектов

```js
const processed = new WeakSet();

function processItem(item) {
  if (processed.has(item)) {
    return; // уже обработан
  }

  // обработка...
  processed.add(item);
}
```

#### 2. Предотвращение циклических ссылок

```js
const visited = new WeakSet();

function traverse(obj) {
  if (visited.has(obj)) {
    return; // уже посещён
  }

  visited.add(obj);
  // обход...
}
```

### Сравнительная таблица

| Особенность   | Map/Set     | WeakMap/WeakSet |
| ------------- | ----------- | --------------- |
| Ключи         | Любые       | Только объекты  |
| Итерация      | ✅ Да       | ❌ Нет          |
| Size          | ✅ Да       | ❌ Нет          |
| Сборка мусора | ❌ Нет      | ✅ Да           |
| Утечки памяти | ⚠️ Возможны | ✅ Нет          |

### ⚠️ Важно

`WeakMap` и `WeakSet` **не итерируемы** по дизайну! Это сделано специально, чтобы сборщик мусора мог удалять объекты в любое время.

```js
const weakMap = new WeakMap();
weakMap.set({}, "value");

weakMap.keys(); // TypeError ❌
weakMap.values(); // TypeError ❌
weakMap.forEach(); // TypeError ❌
```

### Пример проблемы с обычным Map

```js
// Проблема: утечка памяти
const cache = new Map();
function createData() {
  const data = {
    /* большой объект */
  };
  cache.set(data, "метаданные");
  return data;
}

const obj = createData();
// obj удалён, но данные в cache остались в памяти! ❌
```

### Решение с WeakMap

```js
// Решение: автоматическая очистка
const cache = new WeakMap();
function createData() {
  const data = {
    /* большой объект */
  };
  cache.set(data, "метаданные");
  return data;
}

const obj = createData();
// obj удалён → cache автоматически очищается ✅
```

### Итоги

- `WeakMap`/`WeakSet` используют weak references
- Не препятствуют сборке мусора
- Ключами только объекты
- Не итерируемы
- Используются для приватных данных, кэшей, метаданных

---
