## TypeScript

Подборка вопросов и ответов по TypeScript в формате **вопрос–ответ**.

Материал сгруппирован по темам и предназначен для:

- подготовки к техническим собеседованиям
- систематизации знаний по типизации
- понимания различий между runtime и compile-time

---

## Содержание

### Core

▸ [docs/core.md](./docs/core.md)

- Явная vs неявная типизация
- Типизация аргументов функций
- Типизация возвращаемого значения
- Что происходит, если функция ничего не возвращает
- Опциональные параметры
- Параметры по умолчанию

---

### Union и Intersection типы

▸ [docs/union-intersection.md](./docs/union-intersection.md)

- Что такое union типы (`|`)
- Что такое intersection типы (`&`)
- Разница между `A | B` и `A & B`
- Как TypeScript работает с union типами
- Когда `never` появляется в union

---

### Type vs Interface

▸ [docs/type-vs-interface.md](./docs/type-vs-interface.md)

- Разница между `type` и `interface`
- Когда лучше использовать `interface`
- Можно ли расширять `type`
- Декларативное объединение интерфейсов
- Что нельзя сделать с `type`, но можно с `interface`

---

### Типизация объектов

▸ [docs/object-types.md](./docs/object-types.md)

- Типизация объектов
- Опциональные свойства (`?`)
- `readonly` свойства
- Index signatures (`[key: string]: T`)
- Избыточные свойства (excess property checks)
- Почему объект проходит проверку, а литерал — нет

---

### Массивы и кортежи

▸ [docs/arrays-tuples.md](./docs/arrays-tuples.md)

- Типизация массивов (`T[]` vs `Array<T>`)
- Массивы с union типами
- Что такое tuple
- Отличие tuple от массива
- Почему tuple — не просто массив

---

### Enum

▸ [docs/enum.md](./docs/enum.md)

- Что такое enum
- Числовые enum
- Строковые enum
- Проблемы enum в runtime
- Почему enum часто избегают
- Альтернатива enum (union литералов)

---

### Literal Types

▸ [docs/literal-types.md](./docs/literal-types.md)

- Строковые литеральные типы
- Числовые литеральные типы
- Зачем нужны literal types
- Union + literal types

---

### Type Narrowing

▸ [docs/type-narrowing.md](./docs/type-narrowing.md)

- Что такое narrowing
- `typeof` narrowing
- `in` narrowing
- `instanceof` narrowing
- Пользовательские type guards
- Discriminated unions

---

### Type Assertions

▸ [docs/type-assertions.md](./docs/type-assertions.md)

- Что такое type assertion
- `as` vs `<Type>`
- Почему assertion — не приведение типов
- Опасности type assertion
- Когда assertion допустим

---

### Generics

▸ [docs/generics.md](./docs/generics.md)

- Что такое generics
- Зачем нужны generics
- Generic функции
- Generic интерфейсы
- Ограничения generics (`extends`)
- Default generics
- Почему generics лучше `any`

---

### Utility Types

▸ [docs/utility-types.md](./docs/utility-types.md)

- `Partial`
- `Required`
- `Readonly`
- `Pick`
- `Omit`
- `Record`
- Когда использовать utility types

---

### keyof / typeof / Indexed Access

▸ [docs/keyof-typeof.md](./docs/keyof-typeof.md)

- Оператор `keyof`
- Использование `typeof` в типах
- Indexed access types (`T[K]`)
- Связка `keyof` + generics

---

### Классы в TypeScript

▸ [docs/classes.md](./docs/classes.md)

- Типизация классов
- `public`, `private`, `protected`
- `readonly` в классах
- Абстрактные классы
- Реализация интерфейсов
- Интерфейс vs абстрактный класс

---

### Перегрузка функций

▸ [docs/function-overloads.md](./docs/function-overloads.md)

- Что такое function overloads
- Overload signatures
- Зачем нужны перегрузки
- Частые ошибки с overloads

---

### Работа с this

▸ [docs/this.md](./docs/this.md)

- Типизация `this`
- Потеря `this` в callback
- `this: void`
- `noImplicitThis`

---

### never и исчерпывающие проверки

▸ [docs/never.md](./docs/never.md)

- Когда TypeScript выводит `never`
- Exhaustive check через `never`
- Зачем это нужно в `switch` и union типах

---

### Async и Promise

▸ [docs/async.md](./docs/async.md)

- Типизация `Promise`
- Что возвращает `async` функция
- Типизация `await`
- Ошибки и `Promise<never>`

---

### DOM и события

▸ [docs/dom-events.md](./docs/dom-events.md)

- Типизация событий (`MouseEvent`, `KeyboardEvent`)
- Типизация `event.target`
- Почему `event.target` — `EventTarget`
- Приведение типов в DOM

---

### Типизация сторонних библиотек

▸ [docs/third-party-types.md](./docs/third-party-types.md)

- Что такое `@types`
- Когда нужны `.d.ts` файлы
- Declaration files
- Почему TypeScript ругается на JS библиотеку

---

### Runtime vs Compile-time

▸ [docs/runtime-vs-compiletime.md](./docs/runtime-vs-compiletime.md)

- Какие типы существуют только во время компиляции
- Почему типов нет в runtime
- Частая ошибка: проверка типов в runtime

---

### Частые фейлы на собеседованиях

▸ [docs/interview-fails.md](./docs/interview-fails.md)

- Почему `any` — красный флаг
- Почему `as any` — плохо
- Почему enum может быть плохой идеей
- Почему `unknown` безопаснее `any`
- Почему TypeScript не спасает от всех ошибок

---

## Формат

- Каждый пункт — отдельный вопрос
- Краткий и точный ответ по существу
- Примеры кода — там, где это необходимо для понимания

---

## Примечание

Материал ориентирован на **понимание**, а не заучивание.  
Рекомендуется проверять примеры и экспериментировать с кодом самостоятельно.
