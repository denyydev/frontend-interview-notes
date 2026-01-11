### 📌 Union и Intersection типы

1. [Что такое union типы (`|`)](#1-что-такое-union-типы-)
2. [Что такое intersection типы (`&`)](#2-что-такое-intersection-типы-)
3. [Разница между `A | B` и `A & B`](#3-разница-между-a--b-и-a--b)
4. [Как TypeScript работает с union типами](#4-как-typescript-работает-с-union-типами)
5. [Когда `never` появляется в union](#5-когда-never-появляется-в-union)

---

## 1. Что такое union типы (`|`)

Union тип (объединённый тип) — это тип, который представляет значение, которое может быть **одним из нескольких типов**. Создаётся с помощью оператора `|` (вертикальная черта).

### Синтаксис

```ts
type UnionType = Type1 | Type2 | Type3;
```

### Простыми словами

Union тип — это **"или-или"**. Значение может быть одним типом **или** другим типом.

### Базовые примеры

#### Пример 1: Простые типы

```ts
let value: string | number;
value = 'привет';   // ✅ string
value = 42;         // ✅ number
value = true;       // ❌ Type 'boolean' is not assignable to type 'string | number'
```

#### Пример 2: С примитивными типами

```ts
type StringOrNumber = string | number;

function process(value: StringOrNumber): void {
  console.log(value);
}

process('текст');   // ✅
process(42);        // ✅
process(true);      // ❌ Ошибка
```

#### Пример 3: С `null` и `undefined`

```ts
type MaybeString = string | null | undefined;

let name: MaybeString;
name = 'Алекс';     // ✅
name = null;        // ✅
name = undefined;   // ✅
```

### С объектами

```ts
interface Dog {
  type: 'dog';
  bark: () => void;
}

interface Cat {
  type: 'cat';
  meow: () => void;
}

type Pet = Dog | Cat;

function makeSound(pet: Pet): void {
  // pet может быть Dog или Cat
  if (pet.type === 'dog') {
    pet.bark();  // TypeScript знает, что это Dog
  } else {
    pet.meow();  // TypeScript знает, что это Cat
  }
}
```

### С литеральными типами

```ts
type Status = 'pending' | 'completed' | 'failed';

let status: Status;
status = 'pending';    // ✅
status = 'completed';  // ✅
status = 'failed';     // ✅
status = 'error';      // ❌ Type '"error"' is not assignable to type 'Status'

// Использование
function handleStatus(status: Status): void {
  switch (status) {
    case 'pending':
      console.log('В процессе');
      break;
    case 'completed':
      console.log('Завершено');
      break;
    case 'failed':
      console.log('Ошибка');
      break;
  }
}
```

### С функциями

```ts
type Handler = () => void | (() => string);

function process(handler: Handler): void {
  const result = handler();
  if (typeof result === 'string') {
    console.log(result);
  }
}
```

### Discriminated Union (размеченные union типы)

```ts
interface Success {
  type: 'success';
  data: string;
}

interface Error {
  type: 'error';
  message: string;
}

type Result = Success | Error;

function handleResult(result: Result): void {
  // TypeScript может определить тип на основе поля 'type'
  if (result.type === 'success') {
    console.log(result.data);      // ✅ TypeScript знает, что это Success
  } else {
    console.log(result.message);   // ✅ TypeScript знает, что это Error
  }
}
```

### Примеры использования

#### Пример 1: API ответы

```ts
interface UserResponse {
  id: number;
  name: string;
  email: string;
}

interface ErrorResponse {
  error: string;
  code: number;
}

type ApiResponse = UserResponse | ErrorResponse;

async function fetchUser(id: number): Promise<ApiResponse> {
  try {
    const response = await fetch(`/api/users/${id}`);
    const data = await response.json();
    return data as UserResponse;
  } catch (error) {
    return { error: 'Не удалось загрузить пользователя', code: 500 };
  }
}
```

#### Пример 2: Формы

```ts
type InputType = 'text' | 'email' | 'password' | 'number';

interface InputProps {
  type: InputType;
  value: string | number;
  onChange: (value: string | number) => void;
}

function Input({ type, value, onChange }: InputProps): void {
  // ...
}
```

#### Пример 3: События

```ts
type EventType = 'click' | 'focus' | 'blur' | 'submit';

function handleEvent(type: EventType, callback: () => void): void {
  // ...
}

handleEvent('click', () => console.log('Клик'));
handleEvent('focus', () => console.log('Фокус'));
```

### Визуальная аналогия

Union тип — как **меню в ресторане**:
- Ты можешь выбрать **или** суп, **или** салат, **или** основное блюдо
- Но не можешь выбрать всё сразу (это было бы intersection)
- Один из вариантов, но не все одновременно

**Другой пример:**
- `string | number` — значение может быть **или** строкой, **или** числом
- Как выбор между чаем и кофе — выбираешь один напиток

### ⚠️ Частая ошибка

Думают, что union тип позволяет использовать все типы одновременно:

```ts
// ❌ Неправильное понимание
type StringOrNumber = string | number;
let value: StringOrNumber = 'привет' + 42;  // Ошибка: нельзя смешивать

// ✅ Правильно
type StringOrNumber = string | number;
let value1: StringOrNumber = 'привет';  // string
let value2: StringOrNumber = 42;        // number
// value может быть либо string, либо number в конкретный момент времени
```

### Итоги

- Union тип (`|`) — значение может быть **одним из нескольких типов**
- Синтаксис: `Type1 | Type2 | Type3`
- Используется для значений, которые могут иметь разные типы
- Discriminated Union помогает TypeScript определить конкретный тип
- Значение может быть **одним** из типов, но не всеми одновременно

---

## 2. Что такое intersection типы (`&`)

Intersection тип (пересечённый тип) — это тип, который представляет значение, которое **одновременно является всеми указанными типами**. Создаётся с помощью оператора `&` (амперсанд).

### Синтаксис

```ts
type IntersectionType = Type1 & Type2 & Type3;
```

### Простыми словами

Intersection тип — это **"и-и"**. Значение должно быть **и** одним типом, **и** другим типом одновременно.

### Базовые примеры

#### Пример 1: С примитивными типами

```ts
type NeverType = string & number;
// Результат: never (невозможно быть одновременно string и number)

let value: NeverType;  // ❌ Нельзя создать значение этого типа
```

**Почему `never`?** Невозможно быть одновременно строкой и числом.

#### Пример 2: С объектами (основное применение)

```ts
interface HasName {
  name: string;
}

interface HasAge {
  age: number;
}

type Person = HasName & HasAge;
// Person имеет и name, и age

const person: Person = {
  name: 'Алекс',
  age: 25
  // ✅ Должен иметь оба свойства
};

const invalid: Person = {
  name: 'Алекс'
  // ❌ Property 'age' is missing
};
```

#### Пример 3: Комбинирование интерфейсов

```ts
interface Flyable {
  fly: () => void;
}

interface Swimmable {
  swim: () => void;
}

type Duck = Flyable & Swimmable;
// Duck может и летать, и плавать

const duck: Duck = {
  fly: () => console.log('Летит'),
  swim: () => console.log('Плывёт')
  // ✅ Должен иметь оба метода
};
```

### Слияние свойств объектов

```ts
interface A {
  x: number;
  y: string;
}

interface B {
  y: number;  // Конфликтующее свойство
  z: boolean;
}

type C = A & B;
// C имеет: x: number, y: number, z: boolean
// y: string & number → never (конфликт типов)

const c: C = {
  x: 1,
  y: 42,      // Должно быть одновременно string и number (невозможно!)
  z: true
};
// ❌ Type 'number' is not assignable to type 'never'
```

**Правильный пример без конфликтов:**

```ts
interface A {
  x: number;
  y: string;
}

interface B {
  y: string;  // Совместимые типы
  z: boolean;
}

type C = A & B;
// C имеет: x: number, y: string, z: boolean

const c: C = {
  x: 1,
  y: 'привет',  // ✅ string
  z: true
};
```

### Комбинирование с union типами

```ts
type A = { x: number } | { y: string };
type B = { z: boolean };

type C = A & B;
// Результат: ({ x: number } & { z: boolean }) | ({ y: string } & { z: boolean })

const c1: C = { x: 1, z: true };        // ✅
const c2: C = { y: 'привет', z: true }; // ✅
const c3: C = { x: 1 };                 // ❌ Property 'z' is missing
```

### Примеры использования

#### Пример 1: Миксины (mixins)

```ts
class Animal {
  name: string;
  constructor(name: string) {
    this.name = name;
  }
}

interface Flyable {
  fly(): void;
}

interface Swimmable {
  swim(): void;
}

// Комбинируем класс и интерфейсы
type Duck = Animal & Flyable & Swimmable;

function createDuck(name: string): Duck {
  return {
    name,
    fly: () => console.log(`${name} летит`),
    swim: () => console.log(`${name} плывёт`)
  } as Duck;
}
```

#### Пример 2: Расширение типов

```ts
interface BaseUser {
  id: number;
  email: string;
}

interface Admin {
  role: 'admin';
  permissions: string[];
}

interface Moderator {
  role: 'moderator';
  canEdit: boolean;
}

type AdminUser = BaseUser & Admin;
type ModeratorUser = BaseUser & Moderator;

function processUser(user: AdminUser | ModeratorUser): void {
  console.log(user.id, user.email);  // Общие свойства
  
  if (user.role === 'admin') {
    console.log(user.permissions);   // TypeScript знает, что это AdminUser
  } else {
    console.log(user.canEdit);       // TypeScript знает, что это ModeratorUser
  }
}
```

#### Пример 3: Утилитарные типы

```ts
interface User {
  id: number;
  name: string;
  email: string;
}

type UserWithTimestamp = User & {
  createdAt: Date;
  updatedAt: Date;
};

function createUser(data: User): UserWithTimestamp {
  return {
    ...data,
    createdAt: new Date(),
    updatedAt: new Date()
  };
}
```

#### Пример 4: Функции

```ts
type Callable = {
  (): void;
};

type HasName = {
  name: string;
};

type CallableWithName = Callable & HasName;

const fn: CallableWithName = (() => {
  const func = () => console.log('Вызвано');
  func.name = 'myFunction';
  return func;
})() as CallableWithName;
```

### Разница с расширением интерфейсов

```ts
// С intersection
type Extended = Base & { extra: string };

// С extends (для интерфейсов)
interface Extended extends Base {
  extra: string;
}

// Обычно предпочтительнее extends для интерфейсов
// Но intersection полезен для комбинирования типов, которые не являются интерфейсами
```

### Визуальная аналогия

Intersection тип — как **комбинированный обед**:
- Ты получаешь **и** суп, **и** салат, **и** основное блюдо одновременно
- Всё вместе, а не что-то одно

**Другой пример:**
- `HasName & HasAge` — объект должен иметь **и** `name`, **и** `age`
- Как быть одновременно студентом **и** работником — нужно соответствовать обоим требованиям

### ⚠️ Частая ошибка

Путают intersection с union:

```ts
// ❌ Неправильное понимание
type A = { x: number };
type B = { y: string };
type C = A & B;  // Думают: "или A, или B"

// ✅ Правильно
type C = A & B;  // Это "и A, и B одновременно"
const c: C = {
  x: 1,      // ✅ Нужно
  y: 'привет' // ✅ Тоже нужно
};
```

### Итоги

- Intersection тип (`&`) — значение должно быть **всеми указанными типами одновременно**
- Синтаксис: `Type1 & Type2 & Type3`
- Основное применение — комбинирование объектов
- Конфликтующие свойства становятся `never`
- Используется для миксинов, расширения типов, комбинирования интерфейсов
- С примитивными типами обычно даёт `never` (нельзя быть одновременно разными примитивами)

---

## 3. Разница между `A | B` и `A & B`

`A | B` (union) и `A & B` (intersection) — это **разные операции** над типами, которые дают разные результаты.

### Основная разница

| Операция | Значение | Оператор | Аналогия |
|----------|----------|----------|----------|
| **Union** (`A \| B`) | Значение может быть **одним из** типов | `\|` | **"или-или"** |
| **Intersection** (`A & B`) | Значение должно быть **всеми** типами | `&` | **"и-и"** |

### Визуальное сравнение

```
Union (A | B):
┌─────────┐     ┌─────────┐
│   Тип A │  OR │   Тип B │
└─────────┘     └─────────┘
     ↓              ↓
  Значение может быть A или B

Intersection (A & B):
┌─────────┐
│   Тип A │
│  + Тип B│
└─────────┘
     ↓
  Значение должно быть и A, и B одновременно
```

### Примеры с объектами

```ts
interface HasName {
  name: string;
}

interface HasAge {
  age: number;
}
```

#### Union: `HasName | HasAge`

```ts
type Person = HasName | HasAge;

const person1: Person = {
  name: 'Алекс'
  // ✅ Может быть только HasName
};

const person2: Person = {
  age: 25
  // ✅ Может быть только HasAge
};

const person3: Person = {
  name: 'Алекс',
  age: 25
  // ✅ Тоже может быть (удовлетворяет HasName)
};

// Использование
function process(person: Person): void {
  // ⚠️ Нужна проверка, какие свойства доступны
  if ('name' in person) {
    console.log(person.name);  // ✅ Доступно
  }
  if ('age' in person) {
    console.log(person.age);   // ✅ Доступно
  }
  // person.name или person.age могут не существовать!
}
```

#### Intersection: `HasName & HasAge`

```ts
type Person = HasName & HasAge;

const person: Person = {
  name: 'Алекс',
  age: 25
  // ✅ Должен иметь ОБА свойства
};

const invalid1: Person = {
  name: 'Алекс'
  // ❌ Property 'age' is missing
};

const invalid2: Person = {
  age: 25
  // ❌ Property 'name' is missing
};

// Использование
function process(person: Person): void {
  console.log(person.name);  // ✅ Всегда доступно
  console.log(person.age);   // ✅ Всегда доступно
  // Оба свойства гарантированно есть!
}
```

### С примитивными типами

```ts
type StringOrNumber = string | number;
type StringAndNumber = string & number;
```

#### Union: `string | number`

```ts
let value: string | number;
value = 'привет';   // ✅ string
value = 42;         // ✅ number
value = true;       // ❌ Ошибка
```

#### Intersection: `string & number`

```ts
let value: string & number;
// ❌ Невозможно создать значение, которое одновременно string и number
// Результат: never
```

### С функциями

```ts
type Fn1 = (x: number) => string;
type Fn2 = (x: string) => number;
```

#### Union: `Fn1 | Fn2`

```ts
type Fn = Fn1 | Fn2;
// Функция может быть либо Fn1, либо Fn2
// Но нельзя вызвать без проверки типа

const fn: Fn = (x: number) => String(x);  // ✅ Fn1
```

#### Intersection: `Fn1 & Fn2`

```ts
type Fn = Fn1 & Fn2;
// Функция должна удовлетворять ОБОИМ типам одновременно
// (x: number) => string И (x: string) => number
// Это невозможно для одной функции, результат: never
```

### Слияние свойств объектов

```ts
interface A {
  x: number;
  y: string;
}

interface B {
  y: number;  // Конфликт с A.y
  z: boolean;
}
```

#### Union: `A | B`

```ts
type C = A | B;
// C может быть либо A (x, y: string), либо B (y: number, z)

const c1: C = { x: 1, y: 'привет' };        // ✅ A
const c2: C = { y: 42, z: true };           // ✅ B
const c3: C = { x: 1, y: 'привет', z: true }; // ✅ A (удовлетворяет A)

// Доступ к свойствам
function process(c: C): void {
  if ('x' in c) {
    console.log(c.x);  // ✅ Доступно
  }
  if ('z' in c) {
    console.log(c.z);  // ✅ Доступно
  }
  // c.y может быть string или number, нужна проверка
}
```

#### Intersection: `A & B`

```ts
type C = A & B;
// C должен иметь: x, y (string & number → never), z

const c: C = {
  x: 1,
  y: 42,      // ❌ Конфликт: y должен быть одновременно string и number
  z: true
};
// Type 'number' is not assignable to type 'never'
```

### Сравнительная таблица

| Аспект | `A | B` (Union) | `A & B` (Intersection) |
|--------|-------------|----------------------|
| **Значение** | Один из типов | Все типы одновременно |
| **Оператор** | `\|` | `&` |
| **Доступ к свойствам** | Нужна проверка | Все свойства доступны |
| **С примитивами** | Работает | Обычно `never` |
| **С объектами** | Любой из объектов | Все свойства вместе |
| **Конфликты свойств** | Разрешены | Становятся `never` |
| **Использование** | Варианты, опции | Комбинирование, расширение |

### Практические примеры

#### Пример 1: API ответы

```ts
interface Success {
  data: string;
}

interface Error {
  error: string;
}

// Union — ответ может быть либо успешным, либо с ошибкой
type ApiResponse = Success | Error;

// Intersection — ответ должен быть одновременно успешным и с ошибкой (бессмысленно)
type InvalidResponse = Success & Error;  // ❌ Противоречие
```

#### Пример 2: Пользователи

```ts
interface BaseUser {
  id: number;
  email: string;
}

interface Admin {
  role: 'admin';
  permissions: string[];
}

// Union — пользователь может быть обычным или админом
type User = BaseUser | (BaseUser & Admin);

// Intersection — пользователь должен быть и базовым, и админом одновременно
type AdminUser = BaseUser & Admin;
```

#### Пример 3: Стили

```ts
interface Size {
  width: number;
  height: number;
}

interface Position {
  x: number;
  y: number;
}

// Intersection — элемент имеет и размер, и позицию
type Element = Size & Position;

const element: Element = {
  width: 100,
  height: 50,
  x: 10,
  y: 20
  // ✅ Должен иметь все свойства
};
```

### Визуальная аналогия

**Union (`|`)** — как **выбор в меню**:
- Ты можешь выбрать суп **или** салат **или** основное блюдо
- Один вариант из списка

**Intersection (`&`)** — как **комплексный обед**:
- Ты получаешь **и** суп, **и** салат, **и** основное блюдо
- Всё вместе, одновременно

### ⚠️ Частая ошибка

Путают операторы:

```ts
// ❌ Ошибка
type Person = HasName | HasAge;
const person: Person = {
  name: 'Алекс'
  // Думают: "нужно только name" (правильно для union)
};
// Но потом пытаются использовать person.age без проверки
console.log(person.age);  // ❌ Может не существовать!

// ✅ Правильно для union
if ('age' in person) {
  console.log(person.age);
}

// ✅ Или используй intersection, если нужны оба свойства
type Person = HasName & HasAge;
const person: Person = {
  name: 'Алекс',
  age: 25  // Оба обязательны
};
console.log(person.age);  // ✅ Всегда доступно
```

### Итоги

- `A | B` (union) — значение может быть **одним из** типов ("или-или")
- `A & B` (intersection) — значение должно быть **всеми** типами ("и-и")
- Union требует проверки доступности свойств
- Intersection гарантирует наличие всех свойств
- С примитивными типами intersection обычно даёт `never`
- Union используется для вариантов, intersection — для комбинирования

---

## 4. Как TypeScript работает с union типами

TypeScript использует **type narrowing** (сужение типа) для работы с union типами. Это позволяет TypeScript определять конкретный тип значения на основе проверок в коде.

### Type Narrowing (сужение типа)

TypeScript анализирует код и **сужает** union тип до конкретного типа на основе проверок.

#### Пример 1: `typeof` проверка

```ts
function process(value: string | number): void {
  if (typeof value === 'string') {
    // TypeScript знает, что value: string
    console.log(value.toUpperCase());  // ✅ Доступны методы string
  } else {
    // TypeScript знает, что value: number
    console.log(value.toFixed(2));     // ✅ Доступны методы number
  }
}
```

#### Пример 2: Проверка на `null`/`undefined`

```ts
function process(value: string | null | undefined): void {
  if (value === null) {
    return;  // Выходим, если null
  }
  
  if (value === undefined) {
    return;  // Выходим, если undefined
  }
  
  // TypeScript знает, что value: string
  console.log(value.toUpperCase());  // ✅ Безопасно
}
```

#### Пример 3: Проверка свойства (`in` оператор)

```ts
interface Dog {
  type: 'dog';
  bark: () => void;
}

interface Cat {
  type: 'cat';
  meow: () => void;
}

type Pet = Dog | Cat;

function makeSound(pet: Pet): void {
  if ('bark' in pet) {
    // TypeScript знает, что pet: Dog
    pet.bark();  // ✅
  } else {
    // TypeScript знает, что pet: Cat
    pet.meow();  // ✅
  }
}
```

### Discriminated Union (размеченные union)

Если union типы имеют общее свойство-дискриминант, TypeScript может автоматически определить тип:

```ts
interface Success {
  type: 'success';
  data: string;
}

interface Error {
  type: 'error';
  message: string;
}

type Result = Success | Error;

function handleResult(result: Result): void {
  switch (result.type) {
    case 'success':
      // TypeScript знает, что result: Success
      console.log(result.data);      // ✅ Доступно data
      break;
    case 'error':
      // TypeScript знает, что result: Error
      console.log(result.message);   // ✅ Доступно message
      break;
  }
}
```

### Type Guards (функции-проверки)

Можно создать функции, которые TypeScript распознаёт как проверки типов:

```ts
interface Fish {
  swim: () => void;
}

interface Bird {
  fly: () => void;
}

function isFish(pet: Fish | Bird): pet is Fish {
  return (pet as Fish).swim !== undefined;
}

function move(pet: Fish | Bird): void {
  if (isFish(pet)) {
    // TypeScript знает, что pet: Fish
    pet.swim();  // ✅
  } else {
    // TypeScript знает, что pet: Bird
    pet.fly();   // ✅
  }
}
```

### Общие свойства union типов

Если все типы в union имеют общее свойство, оно доступно без проверки:

```ts
interface A {
  common: string;
  a: number;
}

interface B {
  common: string;
  b: number;
}

type C = A | B;

function process(c: C): void {
  console.log(c.common);  // ✅ Доступно (общее свойство)
  // console.log(c.a);    // ❌ Может не существовать
  // console.log(c.b);    // ❌ Может не существовать
  
  if ('a' in c) {
    console.log(c.a);  // ✅ Доступно
  }
}
```

### Обработка всех вариантов

TypeScript может проверить, что все варианты union обработаны:

```ts
type Status = 'pending' | 'completed' | 'failed';

function handleStatus(status: Status): string {
  switch (status) {
    case 'pending':
      return 'В процессе';
    case 'completed':
      return 'Завершено';
    case 'failed':
      return 'Ошибка';
    default:
      // TypeScript проверяет, что все случаи обработаны
      const _exhaustive: never = status;
      return _exhaustive;
  }
}

// Если добавить новый вариант:
// type Status = 'pending' | 'completed' | 'failed' | 'cancelled';
// TypeScript покажет ошибку в default ветке
```

### Примеры

#### Пример 1: Обработка API ответов

```ts
interface UserResponse {
  success: true;
  data: {
    id: number;
    name: string;
  };
}

interface ErrorResponse {
  success: false;
  error: string;
}

type ApiResponse = UserResponse | ErrorResponse;

async function fetchUser(id: number): Promise<ApiResponse> {
  try {
    const response = await fetch(`/api/users/${id}`);
    const data = await response.json();
    return { success: true, data };
  } catch (error) {
    return { success: false, error: 'Ошибка загрузки' };
  }
}

async function processUser(id: number): Promise<void> {
  const response = await fetchUser(id);
  
  if (response.success) {
    // TypeScript знает, что response: UserResponse
    console.log(response.data.name);  // ✅ Доступно
  } else {
    // TypeScript знает, что response: ErrorResponse
    console.error(response.error);    // ✅ Доступно
  }
}
```

#### Пример 2: Формы

```ts
type InputValue = string | number | null;

function processInput(value: InputValue): string {
  if (value === null) {
    return '';
  }
  
  if (typeof value === 'number') {
    return String(value);
  }
  
  // TypeScript знает, что value: string
  return value.toUpperCase();  // ✅ Безопасно
}
```

#### Пример 3: События

```ts
interface ClickEvent {
  type: 'click';
  x: number;
  y: number;
}

interface KeyboardEvent {
  type: 'keyboard';
  key: string;
}

type Event = ClickEvent | KeyboardEvent;

function handleEvent(event: Event): void {
  switch (event.type) {
    case 'click':
      // TypeScript знает, что event: ClickEvent
      console.log(`Клик в (${event.x}, ${event.y})`);
      break;
    case 'keyboard':
      // TypeScript знает, что event: KeyboardEvent
      console.log(`Нажата клавиша: ${event.key}`);
      break;
  }
}
```

### Операторы для сужения типа

#### `typeof`

```ts
function process(value: string | number): void {
  if (typeof value === 'string') {
    // value: string
  } else {
    // value: number
  }
}
```

#### `instanceof`

```ts
function process(value: Date | string): void {
  if (value instanceof Date) {
    // value: Date
    console.log(value.getFullYear());
  } else {
    // value: string
    console.log(value.toUpperCase());
  }
}
```

#### `in`

```ts
function process(obj: { a: number } | { b: string }): void {
  if ('a' in obj) {
    // obj: { a: number }
    console.log(obj.a);
  } else {
    // obj: { b: string }
    console.log(obj.b);
  }
}
```

#### Сравнение с литералами

```ts
function process(value: 'a' | 'b' | 'c'): void {
  if (value === 'a') {
    // value: 'a'
  } else if (value === 'b') {
    // value: 'b'
  } else {
    // value: 'c'
  }
}
```

### Визуальная аналогия

TypeScript с union типами — как **детектив, который сужает круг подозреваемых**:
- В начале у тебя список подозреваемых (union тип)
- Каждая проверка исключает некоторых подозреваемых
- В конце остаётся один конкретный подозреваемый (конкретный тип)

### ⚠️ Частая ошибка

Не используют проверки типов:

```ts
// ❌ Плохо
function process(value: string | number): void {
  console.log(value.toUpperCase());  
  // ❌ Property 'toUpperCase' does not exist on type 'number'
}

// ✅ Хорошо
function process(value: string | number): void {
  if (typeof value === 'string') {
    console.log(value.toUpperCase());  // ✅ Безопасно
  } else {
    console.log(value.toFixed(2));     // ✅ Безопасно
  }
}
```

### Итоги

- TypeScript использует **type narrowing** для работы с union типами
- Проверки (`typeof`, `instanceof`, `in`, сравнения) сужают тип
- Discriminated Union помогает автоматически определить тип
- Type Guards — функции, которые помогают TypeScript определить тип
- Общие свойства доступны без проверки
- TypeScript может проверить обработку всех вариантов union
- Всегда проверяй тип перед использованием специфичных свойств

---

## 5. Когда `never` появляется в union

`never` в union типах имеет **специальное поведение**: он **исчезает** (удаляется) из union типа, так как `never` представляет тип, значения которого никогда не существуют.

### Основное правило

`never` в union типах **удаляется**:

```ts
type Example = string | never;
// Результат: string

type Example2 = string | number | never;
// Результат: string | number

type Example3 = never | never;
// Результат: never (всё ещё never, так как нет других типов)
```

### Почему `never` удаляется?

`never` означает "значение, которого никогда не будет". Если в union есть `never`, это означает "или этот тип, или ничего (never)". Но "или ничего" не добавляет вариантов, поэтому `never` игнорируется.

### Примеры

#### Пример 1: Простое удаление

```ts
type A = string | never;
// A = string

let value: A = 'привет';  // ✅
let value2: A = null;     // ❌ (если не добавлен null в union)
```

#### Пример 2: С несколькими типами

```ts
type B = string | number | never;
// B = string | number

let value: B = 'текст';  // ✅
let value2: B = 42;      // ✅
```

#### Пример 3: Только `never`

```ts
type C = never | never;
// C = never

let value: C;  // ❌ Нельзя создать значение типа never
```

### Когда `never` появляется?

#### 1. Intersection конфликты

```ts
type Conflict = (string & number) | string;
// string & number = never
// Результат: never | string = string

let value: Conflict = 'привет';  // ✅
```

#### 2. Условные типы (Conditional Types)

```ts
type Exclude<T, U> = T extends U ? never : T;

type Example = Exclude<'a' | 'b' | 'c', 'b'>;
// Результат: 'a' | 'c' (never удаляется)

// Как это работает:
// 'a' extends 'b' ? never : 'a' → 'a'
// 'b' extends 'b' ? never : 'b' → never
// 'c' extends 'b' ? never : 'c' → 'c'
// Результат: 'a' | never | 'c' → 'a' | 'c'
```

#### 3. Функции, которые никогда не возвращаются

```ts
function throwError(): never {
  throw new Error('Ошибка');
}

type Result = string | ReturnType<typeof throwError>;
// ReturnType<typeof throwError> = never
// Результат: string | never = string

let value: Result = 'текст';  // ✅
```

#### 4. Исчерпывающие проверки в switch

```ts
type Status = 'pending' | 'completed';

function process(status: Status): string {
  switch (status) {
    case 'pending':
      return 'В процессе';
    case 'completed':
      return 'Завершено';
    default:
      // TypeScript знает, что status никогда не достигнет здесь
      const exhaustive: never = status;
      return exhaustive;
  }
}

// Если добавить новый вариант:
// type Status = 'pending' | 'completed' | 'failed';
// TypeScript покажет ошибку: Type 'string' is not assignable to type 'never'
```

### Практические примеры

#### Пример 1: Utility type `Exclude`

```ts
type Exclude<T, U> = T extends U ? never : T;

type Result = Exclude<'a' | 'b' | 'c', 'b'>;
// Результат: 'a' | 'c'

// Шаг за шагом:
// 'a' extends 'b' ? never : 'a' → 'a'
// 'b' extends 'b' ? never : 'b' → never (удаляется)
// 'c' extends 'b' ? never : 'c' → 'c'
// Итог: 'a' | 'c'
```

#### Пример 2: Удаление `null` и `undefined`

```ts
type NonNullable<T> = T extends null | undefined ? never : T;

type Example = NonNullable<string | null | undefined>;
// Результат: string

// Шаг за шагом:
// string extends null | undefined ? never : string → string
// null extends null | undefined ? never : null → never (удаляется)
// undefined extends null | undefined ? never : undefined → never (удаляется)
// Итог: string
```

#### Пример 3: Фильтрация типов

```ts
type FilterStrings<T> = T extends string ? T : never;

type Example = FilterStrings<string | number | boolean>;
// Результат: string

// Шаг за шагом:
// string extends string ? string : never → string
// number extends string ? number : never → never (удаляется)
// boolean extends string ? boolean : never → never (удаляется)
// Итог: string
```

#### Пример 4: Комбинирование с intersection

```ts
type A = string & number;  // never
type B = A | string;       // never | string = string

let value: B = 'текст';    // ✅
```

### Использование для проверки полноты

```ts
type Status = 'pending' | 'completed' | 'failed';

function handleStatus(status: Status): void {
  switch (status) {
    case 'pending':
      console.log('В процессе');
      break;
    case 'completed':
      console.log('Завершено');
      break;
    case 'failed':
      console.log('Ошибка');
      break;
    default:
      // Если все случаи обработаны, status имеет тип never
      const exhaustiveCheck: never = status;
      // Это означает, что default ветка никогда не выполнится
      return exhaustiveCheck;
  }
}

// Если добавить новый вариант, TypeScript покажет ошибку:
// type Status = 'pending' | 'completed' | 'failed' | 'cancelled';
// Ошибка: Type 'string' is not assignable to type 'never'
// Нужно добавить обработку 'cancelled'
```

### Визуальная аналогия

`never` в union — как **пустой элемент в списке**:
- Если в списке есть пустой элемент, его можно игнорировать
- Список `['яблоко', пусто, 'банан']` = `['яблоко', 'банан']`
- Пустой элемент не добавляет вариантов выбора

**Другой пример:**
- Union с `never` — как меню, где один пункт пустой
- Этот пункт можно не показывать, так как его нельзя выбрать

### ⚠️ Частая ошибка

Думают, что `never` остаётся в union:

```ts
// ❌ Неправильное понимание
type A = string | never;
// Думают: "A может быть string или never"

// ✅ Правильно
type A = string | never;
// Результат: string (never удаляется)

let value: A = 'текст';  // ✅
// value не может быть never, так как never удалился из union
```

### Итоги

- `never` в union типах **автоматически удаляется**
- `string | never` = `string`
- `never` появляется в intersection конфликтах, условных типах, функциях, которые никогда не возвращаются
- Используется в utility types (`Exclude`, `NonNullable`) для фильтрации типов
- Используется для исчерпывающих проверок в switch/case
- `never` не добавляет вариантов в union, поэтому игнорируется

---
