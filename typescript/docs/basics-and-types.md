# Основы TypeScript и типы

Базовые концепции TypeScript: что это такое, отличия от JavaScript, основные типы и их применение во фронтенд-разработке.  
Материал представлен в формате **вопрос–ответ**.

---

## Содержание (Table of Contents)

1. [Что такое TypeScript и зачем он используется во фронтенд-разработке?](#1-что-такое-typescript-и-зачем-он-используется-во-фронтенд-разработке)
2. [Какие основные отличия TypeScript от JavaScript?](#2-какие-основные-отличия-typescript-от-javascript)
3. [В чём разница между `type` и `interface`?](#3-в-чём-разница-между-type-и-interface)
4. [Разница между `any`, `unknown` и `never`](#4-разница-между-any-unknown-и-never)
5. [В каких случаях применяется `never`?](#5-в-каких-случаях-применяется-never)

---

## 1. Что такое TypeScript и зачем он используется во фронтенд-разработке?

TypeScript — это **типизированный надстройка над JavaScript**, разработанная Microsoft. TypeScript компилируется в обычный JavaScript, но добавляет статическую типизацию и другие возможности для улучшения разработки.

### Простое определение

TypeScript — это **JavaScript с типами**. Он позволяет указывать, какой тип данных ожидается в переменной, функции или объекте, и проверяет это до выполнения кода.

**Аналогия:** TypeScript — как **контроль качества на заводе**:
- JavaScript проверяет товар после производства (ошибки во время выполнения)
- TypeScript проверяет товар на этапе производства (ошибки на этапе разработки)
- Это экономит время и предотвращает дефекты до того, как они попадут к пользователю

### Что такое TypeScript

**TypeScript** — это язык программирования, который является **строгим синтаксическим надмножеством JavaScript**. Это означает:

1. **Любой валидный JavaScript-код является валидным TypeScript-кодом**
2. **TypeScript добавляет типизацию** и дополнительные возможности
3. **TypeScript компилируется в JavaScript** для выполнения в браузере или Node.js

**История:**
- Создан Microsoft в 2012 году
- Открытый исходный код (Apache 2.0)
- Активно развивается и поддерживается сообществом

### Зачем TypeScript во фронтенд-разработке

#### 1. Раннее обнаружение ошибок

**JavaScript:**

```javascript
// ❌ Ошибка обнаружится только при выполнении
function calculateTotal(price, quantity) {
  return price * quantity;
}

calculateTotal("10", 5); // "105" (строковая конкатенация вместо умножения)
```

**TypeScript:**

```typescript
// ✅ Ошибка обнаружится на этапе разработки
function calculateTotal(price: number, quantity: number): number {
  return price * quantity;
}

calculateTotal("10", 5); // ❌ TypeScript Error: Argument of type 'string' is not assignable to parameter of type 'number'
```

#### 2. Улучшенная поддержка IDE

**Автодополнение и подсказки:**

```typescript
interface User {
  id: number;
  name: string;
  email: string;
}

function getUser(id: number): User {
  return { id: 1, name: "Alex", email: "alex@example.com" };
}

const user = getUser(1);
// IDE подскажет доступные свойства: user.id, user.name, user.email
console.log(user.naem); // ❌ TypeScript Error: Property 'naem' does not exist
```

#### 3. Улучшенная рефакторинг

**Безопасное переименование и изменение:**

```typescript
interface User {
  firstName: string; // Переименовали name → firstName
  lastName: string;
}

// TypeScript найдёт все использования и предложит обновить
// В JavaScript пришлось бы искать вручную, что чревато ошибками
```

#### 4. Документация кода

**Типы как документация:**

```typescript
// Явно видно, что функция ожидает и возвращает
function fetchUser(id: number): Promise<User | null> {
  // ...
}

// Без TypeScript пришлось бы читать реализацию или документацию
function fetchUser(id) {
  // Что принимает? Что возвращает? Нужно читать код...
}
```

#### 5. Работа с большими проектами

**Масштабируемость:**

- Легче понимать код, написанный другими разработчиками
- Меньше ошибок при интеграции компонентов
- Проще онбординг новых членов команды

### Основные возможности TypeScript

#### 1. Статическая типизация

```typescript
let age: number = 25;
let name: string = "Alex";
let isActive: boolean = true;
let data: any = { anything: "goes" };
```

#### 2. Интерфейсы и типы

```typescript
interface User {
  id: number;
  name: string;
  email?: string; // Опциональное свойство
}

type Status = "pending" | "approved" | "rejected";
```

#### 3. Generics (обобщённые типы)

```typescript
function identity<T>(arg: T): T {
  return arg;
}

const result = identity<string>("hello");
```

#### 4. Классы и наследование

```typescript
class Animal {
  constructor(public name: string) {}
  
  makeSound(): void {
    console.log("Some sound");
  }
}

class Dog extends Animal {
  makeSound(): void {
    console.log("Woof!");
  }
}
```

### TypeScript в экосистеме React

**Типизация компонентов и пропсов:**

```typescript
interface ButtonProps {
  label: string;
  onClick: () => void;
  disabled?: boolean;
}

const Button: React.FC<ButtonProps> = ({ label, onClick, disabled }) => {
  return (
    <button onClick={onClick} disabled={disabled}>
      {label}
    </button>
  );
};

// ❌ TypeScript предотвратит ошибки
<Button label={123} onClick="not a function" /> // Ошибки типов
```

**Типизация хуков:**

```typescript
const [count, setCount] = useState<number>(0);
const [user, setUser] = useState<User | null>(null);

// TypeScript знает тип count и user
setCount("invalid"); // ❌ Error
```

### Процесс работы с TypeScript

**Разработка → Компиляция → Выполнение:**

```
1. Написание TypeScript кода (.ts/.tsx)
   ↓
2. Компиляция в JavaScript (tsc)
   ↓
3. Получение JavaScript кода (.js)
   ↓
4. Выполнение в браузере/Node.js
```

**Конфигурация (`tsconfig.json`):**

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "ESNext",
    "jsx": "react",
    "strict": true,
    "outDir": "./dist"
  }
}
```

### Преимущества TypeScript

| Преимущество | Описание |
|-------------|----------|
| **Раннее обнаружение ошибок** | Ошибки на этапе разработки, а не в runtime |
| **Улучшенная поддержка IDE** | Автодополнение, подсказки, навигация |
| **Лучшая документация** | Типы служат документацией |
| **Упрощённый рефакторинг** | Безопасное переименование и изменение |
| **Масштабируемость** | Легче работать с большими проектами |
| **Современные возможности** | Поддержка современных фич JavaScript |

### Недостатки TypeScript

| Недостаток | Описание |
|-----------|----------|
| **Кривая обучения** | Нужно время на изучение типов |
| **Дополнительный шаг** | Требуется компиляция |
| **Больше кода** | Нужно писать аннотации типов |
| **Может замедлить разработку** | На начальных этапах |

**⚠️ Важно:** Недостатки обычно перевешиваются преимуществами в реальных проектах.

### Визуальная аналогия

**TypeScript vs JavaScript:**

**JavaScript** — как **езда без правил**:
- Можно делать всё, что угодно
- Быстро, но много аварий (ошибок)
- Ошибки обнаруживаются только в дороге (runtime)

**TypeScript** — как **езда с правилами дорожного движения**:
- Есть ограничения и правила (типы)
- Немного медленнее на старте (компиляция)
- Меньше аварий, безопаснее

### ⚠️ Частая ошибка

Думают, что TypeScript делает код медленнее:

```typescript
// ❌ Неправильное понимание
// "TypeScript замедляет выполнение кода"

// TypeScript компилируется в JavaScript
// В браузере выполняется JavaScript, не TypeScript
// Производительность такая же!
```

**Реальность:** 
- TypeScript компилируется в JavaScript
- В браузере выполняется JavaScript
- TypeScript влияет только на **скорость разработки** (позитивно), не на **выполнение**

### Итоги

- TypeScript — типизированная надстройка над JavaScript
- Добавляет статическую типизацию и дополнительные возможности
- Компилируется в обычный JavaScript
- Основные преимущества: раннее обнаружение ошибок, поддержка IDE, документация, масштабируемость
- Особенно полезен для больших проектов и работы в команде
- Широко используется во фронтенд-разработке (React, Vue, Angular)
- Не замедляет выполнение кода (выполняется JavaScript)
- Типичная ошибка: думать, что TypeScript замедляет работу приложения

---

## 2. Какие основные отличия TypeScript от JavaScript?

TypeScript и JavaScript — это разные языки, но TypeScript является надмножеством JavaScript. Основные отличия связаны с типизацией, компиляцией и дополнительными возможностями.

### Простое определение

**JavaScript** — язык программирования, который выполняется напрямую в браузере или Node.js.

**TypeScript** — это JavaScript + типы + компиляция + дополнительные возможности.

**Аналогия:** 
- JavaScript — как **рукописный текст** (выполняется напрямую)
- TypeScript — как **текст с проверкой орфографии** (сначала проверяется, потом выполняется)

### Основные отличия

#### 1. Статическая типизация

**JavaScript (динамическая типизация):**

```javascript
// Типы определяются во время выполнения
let value = 42;
value = "hello"; // ✅ Работает
value = true;    // ✅ Работает

function add(a, b) {
  return a + b;
}

add(1, 2);        // ✅ 3
add("1", "2");    // ✅ "12" (конкатенация)
add(1, "2");      // ✅ "12" (неожиданное поведение)
```

**TypeScript (статическая типизация):**

```typescript
// Типы определяются на этапе разработки
let value: number = 42;
value = "hello"; // ❌ TypeScript Error: Type 'string' is not assignable to type 'number'
value = true;    // ❌ TypeScript Error: Type 'boolean' is not assignable to type 'number'

function add(a: number, b: number): number {
  return a + b;
}

add(1, 2);        // ✅ 3
add("1", "2");    // ❌ TypeScript Error: Argument of type 'string' is not assignable
add(1, "2");      // ❌ TypeScript Error: Argument of type 'string' is not assignable
```

#### 2. Компиляция

**JavaScript:**
- Выполняется напрямую (интерпретируется)
- Нет этапа компиляции (или используется транспиляция Babel для совместимости)
- Ошибки обнаруживаются во время выполнения

**TypeScript:**
- Требует компиляции в JavaScript
- Компилятор проверяет типы и находит ошибки
- Ошибки обнаруживаются на этапе компиляции

```bash
# JavaScript
node script.js  # Выполняется напрямую

# TypeScript
tsc script.ts   # Компиляция в script.js
node script.js  # Выполнение скомпилированного кода
```

#### 3. Аннотации типов

**JavaScript:**

```javascript
// Нет аннотаций типов
function greet(name) {
  return `Hello, ${name}!`;
}
```

**TypeScript:**

```typescript
// Есть аннотации типов
function greet(name: string): string {
  return `Hello, ${name}!`;
}
```

#### 4. Интерфейсы и типы

**JavaScript:**

```javascript
// Нет интерфейсов, только объекты
const user = {
  id: 1,
  name: "Alex"
};
```

**TypeScript:**

```typescript
// Есть интерфейсы и типы
interface User {
  id: number;
  name: string;
  email?: string;
}

const user: User = {
  id: 1,
  name: "Alex"
};
```

#### 5. Generics

**JavaScript:**

```javascript
// Нет generics
function identity(arg) {
  return arg;
}
```

**TypeScript:**

```typescript
// Есть generics
function identity<T>(arg: T): T {
  return arg;
}

const result = identity<string>("hello");
```

#### 6. Union и Intersection типы

**JavaScript:**

```javascript
// Нет union/intersection типов
function processValue(value) {
  // Нужно проверять тип вручную
  if (typeof value === "string") {
    // ...
  } else if (typeof value === "number") {
    // ...
  }
}
```

**TypeScript:**

```typescript
// Есть union типы
type StringOrNumber = string | number;

function processValue(value: StringOrNumber): void {
  if (typeof value === "string") {
    // TypeScript знает, что здесь value - string
  } else {
    // TypeScript знает, что здесь value - number
  }
}

// Intersection типы
type A = { a: number };
type B = { b: string };
type C = A & B; // { a: number; b: string }
```

#### 7. Модификаторы доступа

**JavaScript:**

```javascript
// Нет модификаторов доступа
class User {
  constructor(name) {
    this.name = name; // Публичное свойство
  }
}
```

**TypeScript:**

```typescript
// Есть модификаторы доступа
class User {
  public name: string;      // Публичное
  private age: number;      // Приватное
  protected email: string;  // Защищённое
  readonly id: number;      // Только для чтения
}
```

#### 8. Enum

**JavaScript:**

```javascript
// Нет enum, используются объекты
const Status = {
  PENDING: "pending",
  APPROVED: "approved",
  REJECTED: "rejected"
};
```

**TypeScript:**

```typescript
// Есть enum
enum Status {
  PENDING = "pending",
  APPROVED = "approved",
  REJECTED = "rejected"
}

const status: Status = Status.PENDING;
```

### Сравнительная таблица

| Характеристика | JavaScript | TypeScript |
|---------------|------------|------------|
| **Типизация** | Динамическая (runtime) | Статическая (compile-time) |
| **Компиляция** | Не требуется (или Babel для совместимости) | Требуется (tsc) |
| **Аннотации типов** | ❌ Нет | ✅ Есть |
| **Интерфейсы** | ❌ Нет | ✅ Есть |
| **Generics** | ❌ Нет | ✅ Есть |
| **Union/Intersection** | ❌ Нет | ✅ Есть |
| **Модификаторы доступа** | ❌ Нет | ✅ Есть |
| **Enum** | ❌ Нет (используются объекты) | ✅ Есть |
| **Ошибки** | Runtime | Compile-time |
| **Поддержка IDE** | Базовая | Продвинутая (автодополнение, рефакторинг) |
| **Размер файлов** | Меньше | Больше (но компилируется в JS) |

### Пример: обработка данных

**JavaScript:**

```javascript
// Ошибка обнаружится только при выполнении
function getUserData(id) {
  // Что возвращает? Нужно читать реализацию
  return fetch(`/api/users/${id}`).then(res => res.json());
}

const user = await getUserData(123);
console.log(user.naem); // ❌ Ошибка: "naem" вместо "name"
// Ошибка обнаружится только когда код выполнится
```

**TypeScript:**

```typescript
// Ошибка обнаружится на этапе разработки
interface UserData {
  id: number;
  name: string;
  email: string;
}

async function getUserData(id: number): Promise<UserData> {
  const response = await fetch(`/api/users/${id}`);
  return response.json();
}

const user = await getUserData(123);
console.log(user.naem); // ❌ TypeScript Error: Property 'naem' does not exist
// Ошибка обнаружится сразу при компиляции
```

### Обратная совместимость

**Важно:** TypeScript — это надмножество JavaScript:

```typescript
// Любой валидный JavaScript код — валидный TypeScript код
let x = 10;
function greet(name) {
  return `Hello, ${name}!`;
}

// Это работает в TypeScript без изменений
```

**Постепенная миграция:**

```typescript
// Можно постепенно добавлять типы
let x = 10;                    // JavaScript стиль
let y: number = 10;            // TypeScript стиль

function add(a, b) {           // JavaScript стиль
  return a + b;
}

function multiply(a: number, b: number): number {  // TypeScript стиль
  return a * b;
}
```

### Производительность

**Важно понимать:**

```typescript
// TypeScript код
function add(a: number, b: number): number {
  return a + b;
}

// Компилируется в обычный JavaScript
function add(a, b) {
  return a + b;
}

// В браузере выполняется JavaScript код
// Производительность одинаковая!
```

**TypeScript не замедляет выполнение**, потому что:
- TypeScript компилируется в JavaScript
- Типы удаляются при компиляции
- В браузере выполняется обычный JavaScript

### Визуальная аналогия

**JavaScript vs TypeScript:**

**JavaScript** — как **езда на велосипеде без правил**:
- Свободно и быстро
- Можно ехать как угодно
- Ошибки обнаруживаются только при падении

**TypeScript** — как **езда на велосипеде с защитой**:
- Есть правила и ограничения
- Немного больше подготовки (компиляция)
- Ошибки обнаруживаются до выезда
- Безопаснее в долгосрочной перспективе

### ⚠️ Частая ошибка

Думают, что TypeScript — это совершенно другой язык:

```typescript
// ❌ Неправильное понимание
// "TypeScript — это новый язык, нужно учить всё заново"

// ✅ Реальность
// TypeScript — это JavaScript + типы
// Любой JavaScript код работает в TypeScript
```

**Реальность:** 
- TypeScript — надмножество JavaScript
- Всё, что вы знаете о JavaScript, применимо в TypeScript
- Нужно только добавить знание типов

### Итоги

- **Типизация:** JavaScript — динамическая, TypeScript — статическая
- **Компиляция:** JavaScript выполняется напрямую, TypeScript требует компиляции
- **Возможности:** TypeScript добавляет интерфейсы, generics, union/intersection, enum, модификаторы доступа
- **Ошибки:** JavaScript — runtime, TypeScript — compile-time
- **Поддержка IDE:** TypeScript имеет лучшую поддержку (автодополнение, рефакторинг)
- **Обратная совместимость:** TypeScript — надмножество JavaScript, любой JS код работает в TS
- **Производительность:** Одинаковая (TypeScript компилируется в JavaScript)
- **Постепенная миграция:** Можно добавлять типы постепенно
- Типичная ошибка: думать, что TypeScript — совершенно другой язык

---

## 3. В чём разница между `type` и `interface`?

`type` и `interface` — это два способа определения типов в TypeScript. Они похожи, но имеют важные различия в использовании и возможностях.

### Простое определение

**`interface`** — это способ описать структуру объекта, который можно расширять и дополнять.

**`type`** — это способ создать псевдоним для любого типа (объект, union, intersection, примитив и т.д.).

**Аналогия:**
- `interface` — как **чертёж здания**, который можно достраивать (расширять)
- `type` — как **ярлык на папке**, который указывает на конкретное содержимое

### Основные отличия

#### 1. Расширение (Extension)

**Interface (extends):**

```typescript
// Интерфейсы можно расширять через extends
interface Animal {
  name: string;
}

interface Dog extends Animal {
  breed: string;
}

const dog: Dog = {
  name: "Rex",
  breed: "Bulldog"
};
```

**Type (intersection):**

```typescript
// Типы расширяются через intersection (&)
type Animal = {
  name: string;
};

type Dog = Animal & {
  breed: string;
};

const dog: Dog = {
  name: "Rex",
  breed: "Bulldog"
};
```

#### 2. Объявление (Declaration Merging)

**Interface (можно объединять):**

```typescript
// ✅ Интерфейсы можно объявлять несколько раз
interface User {
  name: string;
}

interface User {
  age: number;
}

// TypeScript автоматически объединяет их
const user: User = {
  name: "Alex",
  age: 25
  // Оба свойства доступны
};
```

**Type (нельзя объединять):**

```typescript
// ❌ Типы нельзя объявлять несколько раз
type User = {
  name: string;
};

type User = {  // ❌ Error: Duplicate identifier 'User'
  age: number;
};
```

**Практическое применение declaration merging:**

```typescript
// Полезно для расширения глобальных типов
interface Window {
  customProperty: string;
}

// Где-то в другом файле
interface Window {
  anotherProperty: number;
}

// Теперь window имеет оба свойства
window.customProperty = "hello";
window.anotherProperty = 42;
```

#### 3. Union и Intersection типы

**Type (поддерживает union):**

```typescript
// ✅ Типы могут быть union
type Status = "pending" | "approved" | "rejected";
type ID = string | number;
type UserOrAdmin = User | Admin;
```

**Interface (не поддерживает union напрямую):**

```typescript
// ❌ Интерфейсы не могут быть union напрямую
interface Status {  // Это не union
  value: "pending" | "approved" | "rejected";
}

// Но можно использовать type
type Status = "pending" | "approved" | "rejected";
```

#### 4. Примитивы и сложные типы

**Type (может описывать всё):**

```typescript
// ✅ Типы могут описывать примитивы, union, tuples и т.д.
type ID = string;
type Coordinates = [number, number];
type Callback = () => void;
type Nullable<T> = T | null;
```

**Interface (только объекты и функции):**

```typescript
// ✅ Интерфейсы описывают объекты
interface User {
  id: string;
  name: string;
}

// ✅ Интерфейсы могут описывать функции
interface SearchFunc {
  (source: string, subString: string): boolean;
}

// ❌ Интерфейсы не могут описывать примитивы напрямую
interface ID extends string {}  // ❌ Error
```

#### 5. Вычисляемые свойства (Computed Properties)

**Type (поддерживает):**

```typescript
// ✅ Типы поддерживают mapped types и вычисляемые свойства
type Keys = "name" | "age";
type User = {
  [K in Keys]: string;
};
// { name: string; age: string; }
```

**Interface (ограниченная поддержка):**

```typescript
// ❌ Интерфейсы не поддерживают mapped types напрямую
interface User {
  [K in Keys]: string;  // ❌ Error
}

// Но можно использовать type внутри interface
interface User {
  [key: string]: any;
}
```

### Сравнительная таблица

| Характеристика | `interface` | `type` |
|---------------|-------------|--------|
| **Расширение** | `extends` | `&` (intersection) |
| **Declaration Merging** | ✅ Поддерживается | ❌ Не поддерживается |
| **Union типы** | ❌ Не поддерживается | ✅ Поддерживается |
| **Intersection типы** | ✅ Через `extends` | ✅ Через `&` |
| **Примитивы** | ❌ Только объекты/функции | ✅ Всё |
| **Tuples** | ❌ Не напрямую | ✅ Поддерживается |
| **Mapped Types** | ❌ Ограниченно | ✅ Полная поддержка |
| **Computed Properties** | ❌ Ограниченно | ✅ Поддерживается |

### Примеры использования

#### Когда использовать Interface

**1. Описание объектов (особенно для публичного API):**

```typescript
// ✅ Interface лучше для объектов
interface User {
  id: number;
  name: string;
  email: string;
}

interface Admin extends User {
  role: "admin";
  permissions: string[];
}
```

**2. Когда нужен declaration merging:**

```typescript
// ✅ Полезно для расширения библиотек
interface ExpressRequest {
  user?: User;
}

// Где-то в другом месте
interface ExpressRequest {
  session?: Session;
}

// Оба свойства доступны
```

**3. Для классов:**

```typescript
// ✅ Interface хорошо подходит для классов
interface ClockInterface {
  currentTime: Date;
  setTime(d: Date): void;
}

class Clock implements ClockInterface {
  currentTime: Date = new Date();
  setTime(d: Date) {
    this.currentTime = d;
  }
}
```

#### Когда использовать Type

**1. Union типы:**

```typescript
// ✅ Type лучше для union
type Status = "pending" | "approved" | "rejected";
type ID = string | number;
type Result<T> = T | Error;
```

**2. Tuples:**

```typescript
// ✅ Type для tuples
type Coordinates = [number, number];
type Point3D = [number, number, number];
type Entry = [string, number];
```

**3. Mapped Types и утилиты:**

```typescript
// ✅ Type для сложных преобразований
type Partial<T> = {
  [P in keyof T]?: T[P];
};

type Readonly<T> = {
  readonly [P in keyof T]: T[P];
};

type User = {
  id: number;
  name: string;
  email: string;
};

type PartialUser = Partial<User>;
// { id?: number; name?: string; email?: string; }
```

**4. Примитивы и функции:**

```typescript
// ✅ Type для примитивов
type ID = string;
type Callback = () => void;
type AsyncFunction<T> = () => Promise<T>;
```

### Когда можно использовать оба

**Для простых объектов оба подхода эквивалентны:**

```typescript
// ✅ Оба работают одинаково
interface User {
  name: string;
  age: number;
}

type User = {
  name: string;
  age: number;
};

// Использование одинаковое
const user: User = { name: "Alex", age: 25 };
```

**Но есть нюансы:**

```typescript
// Interface можно расширить позже
interface User {
  name: string;
}

// Где-то в другом файле
interface User {
  age: number;  // ✅ Добавляется к существующему интерфейсу
}

// Type нельзя переопределить
type User = {
  name: string;
};

type User = {  // ❌ Error: Duplicate identifier
  age: number;
};
```

### Рекомендации

**Используйте `interface` когда:**
- Описываете объекты (особенно для публичного API)
- Нужна возможность расширения (extends)
- Работаете с классами
- Возможно понадобится declaration merging

**Используйте `type` когда:**
- Нужны union или intersection типы
- Работаете с примитивами, tuples
- Нужны mapped types или сложные преобразования
- Создаёте утилитные типы

**В React (типизация пропсов):**

```typescript
// ✅ Оба подхода работают
interface ButtonProps {
  label: string;
  onClick: () => void;
}

type ButtonProps = {
  label: string;
  onClick: () => void;
};

// Обычно используют interface для компонентов
const Button: React.FC<ButtonProps> = ({ label, onClick }) => {
  return <button onClick={onClick}>{label}</button>;
};
```

### Визуальная аналогия

**Interface vs Type:**

**Interface** — как **договор, который можно дополнить**:
- Основной договор (базовый interface)
- Можно добавить приложения (extends или declaration merging)
- Гибкий и расширяемый

**Type** — как **конкретное определение**:
- Чёткое и неизменное определение
- Можно комбинировать с другими типами (union, intersection)
- Универсальный инструмент

### ⚠️ Частая ошибка

Думают, что `interface` и `type` полностью идентичны:

```typescript
// ❌ Неправильное понимание
// "interface и type — это одно и то же, можно использовать любой"

// ✅ Реальность
// Они похожи, но имеют важные различия
// Interface: declaration merging, лучше для объектов
// Type: union, tuples, mapped types, лучше для сложных типов
```

**Реальность:** 
- `interface` и `type` похожи, но имеют различия
- Выбирайте в зависимости от задачи
- Для объектов часто используют `interface`
- Для union и сложных типов — `type`

### Итоги

- `interface` — для описания объектов, поддерживает `extends` и declaration merging
- `type` — универсальный способ создать псевдоним типа, поддерживает union, intersection, tuples
- `interface` можно объявлять несколько раз (объединение), `type` — нет
- `type` может описывать примитивы, union, tuples; `interface` — только объекты и функции
- `type` лучше для mapped types и сложных преобразований
- Для простых объектов оба подхода эквивалентны
- Рекомендация: `interface` для объектов и классов, `type` для union и сложных типов
- В React обычно используют `interface` для пропсов компонентов
- Типичная ошибка: думать, что `interface` и `type` полностью идентичны

---

## 4. Разница между `any`, `unknown` и `never`

`any`, `unknown` и `never` — это специальные типы в TypeScript, которые имеют разные назначения и уровни безопасности типов.

### Простое определение

**`any`** — отключает проверку типов, позволяет делать что угодно (небезопасно).

**`unknown`** — безопасная версия `any`, требует проверки типа перед использованием.

**`never`** — тип для значений, которые никогда не могут существовать.

**Аналогия:**
- `any` — как **слепой перекрёсток** (можете ехать куда угодно, но опасно)
- `unknown` — как **перекрёсток с остановкой** (нужно посмотреть перед движением)
- `never` — как **тупик** (туда нельзя попасть)

### `any` — отключение проверки типов

**Что такое `any`:**

`any` — это тип, который отключает проверку типов TypeScript. Переменная типа `any` может иметь любое значение и с ней можно делать что угодно.

```typescript
let value: any = 42;
value = "hello";        // ✅ Работает
value = true;           // ✅ Работает
value.foo.bar.baz;      // ✅ TypeScript не проверяет (опасно!)
value();                // ✅ Вызов функции (может быть ошибка runtime)
```

**Проблемы с `any`:**

```typescript
// ❌ any отключает всю проверку типов
function processData(data: any) {
  return data.name.toUpperCase(); // Ошибка, если data.name undefined
}

processData({}); // ❌ Runtime error: Cannot read property 'toUpperCase' of undefined
```

**Когда используется `any`:**

```typescript
// Миграция JavaScript кода в TypeScript
function legacyFunction(param: any) {
  // Старый код, который сложно типизировать
}

// Работа с внешними библиотеками без типов
declare const externalLib: any;

// Быстрое прототипирование (не рекомендуется для продакшена)
let quickPrototype: any = { anything: "goes" };
```

### `unknown` — безопасная версия `any`

**Что такое `unknown`:**

`unknown` — это тип для значений, тип которых мы не знаем заранее. В отличие от `any`, `unknown` **требует проверки типа** перед использованием.

```typescript
let value: unknown = 42;

// ❌ Нельзя использовать без проверки типа
value.toUpperCase();     // Error: Object is of type 'unknown'
value.foo;               // Error: Object is of type 'unknown'
value();                 // Error: Object is of type 'unknown'

// ✅ Нужно сначала проверить тип
if (typeof value === "string") {
  value.toUpperCase();   // ✅ Теперь TypeScript знает, что value - string
}

if (typeof value === "number") {
  value.toFixed(2);      // ✅ Теперь TypeScript знает, что value - number
}
```

**Безопасная обработка `unknown`:**

```typescript
function processData(data: unknown): string {
  // ✅ Проверяем тип перед использованием
  if (typeof data === "string") {
    return data.toUpperCase();
  }
  
  if (typeof data === "number") {
    return data.toString();
  }
  
  if (data && typeof data === "object" && "name" in data) {
    return String(data.name);
  }
  
  throw new Error("Invalid data");
}

processData("hello");     // ✅ "HELLO"
processData(42);          // ✅ "42"
processData({ name: "Alex" }); // ✅ "Alex"
processData({});          // ✅ Error (ожидаемо)
```

**Type narrowing с `unknown`:**

```typescript
function isString(value: unknown): value is string {
  return typeof value === "string";
}

function isUser(value: unknown): value is { name: string; age: number } {
  return (
    typeof value === "object" &&
    value !== null &&
    "name" in value &&
    "age" in value &&
    typeof (value as any).name === "string" &&
    typeof (value as any).age === "number"
  );
}

let data: unknown = getUserData();

if (isString(data)) {
  console.log(data.toUpperCase()); // ✅ TypeScript знает тип
}

if (isUser(data)) {
  console.log(data.name, data.age); // ✅ TypeScript знает структуру
}
```

### `never` — тип для невозможных значений

**Что такое `never`:**

`never` — это тип, который представляет значения, которые **никогда не могут существовать**. Это bottom type в системе типов TypeScript.

```typescript
// Функция, которая никогда не возвращает значение
function throwError(message: string): never {
  throw new Error(message);
}

// Функция с бесконечным циклом
function infiniteLoop(): never {
  while (true) {
    // ...
  }
}

// Union тип, который исключает все возможности
type Impossible = string & number; // never (строка не может быть числом)
```

**Использование `never`:**

```typescript
// 1. Функции, которые всегда выбрасывают исключение
function fail(message: string): never {
  throw new Error(message);
}

// 2. Функции с бесконечным циклом
function infinite(): never {
  while (true) {}
}

// 3. В union типах для исключения
type Status = "pending" | "approved" | "rejected";

function handleStatus(status: Status) {
  switch (status) {
    case "pending":
      return "Wait";
    case "approved":
      return "OK";
    case "rejected":
      return "No";
    default:
      // Если добавим новый статус, TypeScript выдаст ошибку
      const exhaustiveCheck: never = status; // ✅ Ошибка, если не все случаи обработаны
      return exhaustiveCheck;
  }
}

// 4. В условных типах
type NonNullable<T> = T extends null | undefined ? never : T;
```

### Сравнительная таблица

| Характеристика | `any` | `unknown` | `never` |
|---------------|-------|-----------|---------|
| **Проверка типов** | ❌ Отключена | ✅ Требуется проверка | ✅ Всегда ошибка |
| **Безопасность** | ❌ Небезопасно | ✅ Безопасно | ✅ Безопасно |
| **Использование без проверки** | ✅ Возможно | ❌ Невозможно | ❌ Невозможно |
| **Назначение** | Отключение проверки | Неизвестный тип | Невозможные значения |
| **Когда использовать** | Миграция, прототипы | API, парсинг JSON | Исключения, exhaustive checks |

### Примеры использования

#### `any` — когда нужен (и когда избегать)

```typescript
// ❌ Избегайте any в продакшене
function badExample(data: any) {
  return data.someProperty.anotherProperty; // Опасно!
}

// ✅ Лучше использовать unknown
function goodExample(data: unknown) {
  if (data && typeof data === "object" && "someProperty" in data) {
    // Проверяем структуру
  }
}

// ⚠️ any допустим только в особых случаях
// 1. Миграция старого кода
function legacyCode(param: any) { /* ... */ }

// 2. Временное решение (с TODO)
function temporary(data: any) { // TODO: добавить типы
  /* ... */
}
```

#### `unknown` — для безопасной работы с данными

```typescript
// ✅ Парсинг JSON
function parseJSON(json: string): unknown {
  return JSON.parse(json);
}

const data = parseJSON('{"name": "Alex", "age": 25}');

// Нужно проверить структуру
if (data && typeof data === "object" && "name" in data && "age" in data) {
  const user = data as { name: string; age: number };
  console.log(user.name, user.age);
}

// ✅ API ответы
async function fetchUser(id: number): Promise<unknown> {
  const response = await fetch(`/api/users/${id}`);
  return response.json();
}

const userData = await fetchUser(1);
// Проверяем перед использованием
```

#### `never` — для exhaustive checks

```typescript
// ✅ Exhaustive check в switch
type Status = "pending" | "approved" | "rejected";

function getStatusMessage(status: Status): string {
  switch (status) {
    case "pending":
      return "Please wait";
    case "approved":
      return "Approved!";
    case "rejected":
      return "Rejected";
    default:
      // Если добавим новый статус, TypeScript предупредит
      const exhaustiveCheck: never = status;
      throw new Error(`Unhandled status: ${exhaustiveCheck}`);
  }
}
```

### Иерархия типов

```
any (самый широкий, небезопасный)
  ↓
unknown (широкий, но безопасный)
  ↓
конкретные типы (string, number, object, и т.д.)
  ↓
never (самый узкий, невозможные значения)
```

**Назначения:**

- `any` — отключение проверки (опасно)
- `unknown` — неизвестный тип (безопасно, требует проверки)
- Конкретные типы — известный тип (безопасно)
- `never` — невозможный тип (безопасно, для исключений)

### Визуальная аналогия

**any, unknown, never:**

**`any`** — как **слепая зона**:
- Можно идти куда угодно
- Не видно препятствий (ошибок)
- Очень опасно

**`unknown`** — как **туннель с освещением**:
- Нужно включить свет (проверить тип)
- После проверки безопасно двигаться
- Контролируемая опасность

**`never`** — как **тупик**:
- Туда невозможно попасть
- Используется для обозначения невозможных ситуаций
- Полезен для exhaustive checks

### ⚠️ Частая ошибка

Использование `any` вместо `unknown`:

```typescript
// ❌ Плохо — any отключает проверку
function processData(data: any) {
  return data.name.toUpperCase(); // Может упасть в runtime
}

// ✅ Хорошо — unknown требует проверки
function processData(data: unknown) {
  if (data && typeof data === "object" && "name" in data) {
    const name = (data as { name: unknown }).name;
    if (typeof name === "string") {
      return name.toUpperCase();
    }
  }
  throw new Error("Invalid data");
}
```

**Реальность:** 
- `any` отключает всю проверку типов (опасно)
- `unknown` требует проверки перед использованием (безопасно)
- Всегда предпочитайте `unknown` вместо `any`

### Итоги

- `any` — отключает проверку типов, небезопасно, используйте только для миграции
- `unknown` — безопасная версия `any`, требует проверки типа перед использованием
- `never` — тип для невозможных значений, используется в exhaustive checks и исключениях
- Иерархия: `any` (широкий, небезопасный) → `unknown` (широкий, безопасный) → конкретные типы → `never` (узкий)
- `any` допустим только для миграции старого кода или прототипирования
- Всегда предпочитайте `unknown` вместо `any` для безопасности
- `never` полезен для exhaustive checks в switch/case
- Типичная ошибка: использование `any` вместо `unknown`

---

## 5. В каких случаях применяется `never`?

`never` — это специальный тип в TypeScript, который представляет значения, которые **никогда не могут существовать**. Он используется в нескольких специфических случаях для повышения типобезопасности.

### Простое определение

`never` — это **bottom type** в системе типов TypeScript, означающий, что значение этого типа никогда не появится в программе.

**Аналогия:** `never` — как **тупик в лабиринте**:
- Туда невозможно попасть
- Если вы там оказались, значит что-то пошло не так
- Используется для обозначения недостижимого кода

### Основные случаи использования

#### 1. Функции, которые всегда выбрасывают исключение

**Функции, которые никогда не возвращают значение:**

```typescript
// ✅ Функция выбрасывает исключение, никогда не возвращает
function throwError(message: string): never {
  throw new Error(message);
}

// ✅ Функция всегда выбрасывает, return недостижим
function fail(message: string): never {
  throw new Error(message);
  // Код после throw недостижим, но TypeScript требует never
}

// Использование
function divide(a: number, b: number): number {
  if (b === 0) {
    throwError("Division by zero"); // ✅ never, функция не возвращает значение
  }
  return a / b;
}
```

**Пример с обработкой ошибок:**

```typescript
// ✅ never для обработчиков ошибок
function handleError(error: Error): never {
  console.error(error);
  throw error;
  // После throw выполнение прекращается
}

// ✅ never для ассертов
function assert(condition: boolean, message: string): asserts condition {
  if (!condition) {
    throw new Error(message); // never
  }
  // После этого TypeScript знает, что condition === true
}

function processNumber(value: number | null): number {
  assert(value !== null, "Value cannot be null");
  return value * 2; // ✅ TypeScript знает, что value не null
}
```

#### 2. Функции с бесконечным циклом

**Функции, которые никогда не завершаются:**

```typescript
// ✅ Бесконечный цикл, функция никогда не вернёт значение
function infiniteLoop(): never {
  while (true) {
    console.log("Looping forever...");
  }
  // return недостижим
}

// ✅ Обработчик событий, который работает постоянно
function eventListener(): never {
  while (true) {
    // Слушаем события
    const event = waitForEvent();
    processEvent(event);
  }
}
```

#### 3. Exhaustive Checks (Исчерпывающие проверки)

**Проверка, что все случаи обработаны в switch/case:**

```typescript
type Status = "pending" | "approved" | "rejected";

function getStatusMessage(status: Status): string {
  switch (status) {
    case "pending":
      return "Please wait";
    case "approved":
      return "Approved!";
    case "rejected":
      return "Rejected";
    default:
      // ✅ Если добавим новый статус, TypeScript выдаст ошибку
      const exhaustiveCheck: never = status;
      throw new Error(`Unhandled status: ${exhaustiveCheck}`);
  }
}

// Если добавим новый статус:
type Status = "pending" | "approved" | "rejected" | "cancelled";

// ❌ TypeScript Error: Type '"cancelled"' is not assignable to type 'never'
// Это заставляет нас обработать новый случай!
```

**Практический пример с состояниями:**

```typescript
type LoadingState = { type: "loading" };
type SuccessState = { type: "success"; data: string };
type ErrorState = { type: "error"; error: Error };

type State = LoadingState | SuccessState | ErrorState;

function handleState(state: State): string {
  switch (state.type) {
    case "loading":
      return "Loading...";
    case "success":
      return state.data;
    case "error":
      return state.error.message;
    default:
      // ✅ Exhaustive check
      const exhaustiveCheck: never = state;
      throw new Error(`Unhandled state: ${exhaustiveCheck}`);
  }
}
```

#### 4. Условные типы (Conditional Types)

**Исключение типов в условных типах:**

```typescript
// ✅ Удаление null и undefined
type NonNullable<T> = T extends null | undefined ? never : T;

type A = NonNullable<string | null>;        // string
type B = NonNullable<number | undefined>;   // number
type C = NonNullable<string | null | undefined>; // string

// ✅ Извлечение только строк из union
type StringOnly<T> = T extends string ? T : never;

type D = StringOnly<string | number | boolean>; // string

// ✅ Исключение определённых типов
type Exclude<T, U> = T extends U ? never : T;

type E = Exclude<"a" | "b" | "c", "a">; // "b" | "c"
```

**Фильтрация типов:**

```typescript
// ✅ Только функции
type FunctionPropertyNames<T> = {
  [K in keyof T]: T[K] extends Function ? K : never;
}[keyof T];

interface User {
  name: string;
  age: number;
  greet: () => void;
  getId: () => number;
}

type FuncNames = FunctionPropertyNames<User>; // "greet" | "getId"
```

#### 5. Union типы с невозможными комбинациями

**Типы, которые логически несовместимы:**

```typescript
// ✅ Строка не может быть числом одновременно
type Impossible = string & number; // never

// ✅ Невозможный union
type NeverType = never | string; // string (never исчезает из union)

// ✅ В intersection
type AlsoImpossible = string & null; // never (строка не может быть null)
```

**Практическое использование:**

```typescript
// ✅ Исключение из union через never
type AllowedKeys = "name" | "email" | "age";
type ForbiddenKeys = "password" | "token";

type SafeKeys<T> = T extends ForbiddenKeys ? never : T;

type UserKeys = SafeKeys<AllowedKeys | ForbiddenKeys>;
// "name" | "email" | "age" (password и token исключены)
```

#### 6. В generic constraints

**Ограничение generic типов:**

```typescript
// ✅ Функция, которая никогда не должна быть вызвана
function impossible<T extends never>(): T {
  throw new Error("This should never be called");
}

// ❌ Нельзя вызвать с каким-либо типом
// impossible<string>(); // Error: Type 'string' does not satisfy the constraint 'never'
```

### Практические примеры

#### Пример 1: Обработка ошибок с never

```typescript
// ✅ Централизованная обработка ошибок
class AppError extends Error {
  constructor(message: string) {
    super(message);
    this.name = "AppError";
  }
}

function handleError(error: unknown): never {
  if (error instanceof AppError) {
    console.error(`App Error: ${error.message}`);
  } else if (error instanceof Error) {
    console.error(`Error: ${error.message}`);
  } else {
    console.error("Unknown error:", error);
  }
  throw error; // ✅ never, функция выбрасывает исключение
}

// Использование
try {
  riskyOperation();
} catch (error) {
  handleError(error); // TypeScript знает, что код после этого недостижим
}
```

#### Пример 2: State machine с exhaustive check

```typescript
type State = 
  | { type: "idle" }
  | { type: "loading" }
  | { type: "success"; data: string }
  | { type: "error"; error: Error };

function handleState(state: State): void {
  switch (state.type) {
    case "idle":
      console.log("Ready");
      break;
    case "loading":
      console.log("Loading...");
      break;
    case "success":
      console.log("Data:", state.data);
      break;
    case "error":
      console.error("Error:", state.error);
      break;
    default:
      // ✅ Exhaustive check
      const exhaustiveCheck: never = state;
      throw new Error(`Unhandled state: ${exhaustiveCheck}`);
  }
}
```

#### Пример 3: Utility type с never

```typescript
// ✅ Удаление определённых ключей из объекта
type Omit<T, K extends keyof T> = Pick<T, Exclude<keyof T, K>>;

// Или с never:
type OmitNever<T, K extends keyof T> = {
  [P in keyof T as P extends K ? never : P]: T[P];
};

interface User {
  id: number;
  name: string;
  password: string;
  email: string;
}

type SafeUser = OmitNever<User, "password">;
// { id: number; name: string; email: string; }
```

### Когда НЕ использовать never

```typescript
// ❌ Неправильно — функция может вернуть значение
function mightReturn(): never {
  if (Math.random() > 0.5) {
    return "something"; // ❌ Error: Type 'string' is not assignable to type 'never'
  }
  throw new Error();
}

// ✅ Правильно — функция всегда выбрасывает
function alwaysThrows(): never {
  throw new Error();
}

// ❌ Неправильно — можно использовать для любого типа
function badUse(): never {
  return 42; // ❌ Error
}

// ✅ never только для невозможных значений
```

### Визуальная аналогия

**never в системе типов:**

```
Система типов:
├── any (все значения)
├── unknown (неизвестные значения)
├── конкретные типы (string, number, object)
└── never (нет значений) ← тупик
```

**never как страховка:**
- Если вы попали в `never`, значит что-то не так
- Используется для exhaustive checks
- Гарантирует, что все случаи обработаны

### ⚠️ Частая ошибка

Думают, что `never` — это то же самое, что `void`:

```typescript
// ❌ Неправильное понимание
// "never и void — это одно и то же"

// ✅ Реальность
function returnsVoid(): void {
  return; // ✅ void означает "нет возвращаемого значения"
}

function returnsNever(): never {
  throw new Error(); // ✅ never означает "никогда не вернётся"
}

// void — функция возвращает undefined
// never — функция никогда не возвращается (всегда исключение или бесконечный цикл)
```

**Реальность:** 
- `void` — функция не возвращает значение (возвращает `undefined`)
- `never` — функция никогда не возвращается (всегда исключение или бесконечный цикл)

### Итоги

- `never` используется для функций, которые всегда выбрасывают исключение
- `never` используется для функций с бесконечным циклом
- `never` используется для exhaustive checks в switch/case (гарантия обработки всех случаев)
- `never` используется в условных типах для исключения типов (`NonNullable`, `Exclude`)
- `never` используется для фильтрации типов в mapped types
- `never` представляет невозможные значения (bottom type)
- `never` отличается от `void`: `void` — нет возвращаемого значения, `never` — никогда не возвращается
- Типичная ошибка: думать, что `never` — это то же самое, что `void`
