# TypeScript Core — Must-Have Questions (Junior+ / Middle-)

---

## Основы TypeScript
- Что такое TypeScript и зачем он нужен
- Отличия TypeScript от JavaScript
- Что компилирует TypeScript
- Что такое `tsconfig.json`
- Основные флаги `tsconfig` (`target`, `module`, `strict`, `lib`)
- Что включает режим `strict`

---

## Базовые типы
- Базовые типы в TypeScript
- Разница между `any`, `unknown`, `never`
- Почему `any` — плохо
- Когда использовать `unknown`
- Тип `void`
- Типы `null` и `undefined`
- Что меняет `strictNullChecks`

---

## Типизация переменных и функций
- Явная vs неявная типизация
- Типизация аргументов функций
- Типизация возвращаемого значения
- Что происходит, если функция ничего не возвращает
- Опциональные параметры
- Параметры по умолчанию

---

## Union и Intersection типы
- Что такое `union` типы (`|`)
- Что такое `intersection` типы (`&`)
- Разница между `A | B` и `A & B`
- Как TypeScript работает с union типами
- Когда `never` появляется в union

---

## Type vs Interface (классика собесов)
- Разница между `type` и `interface`
- Когда лучше использовать `interface`
- Можно ли расширять `type`
- Декларативное объединение интерфейсов
- Что нельзя сделать с `type`, но можно с `interface`

---

## Типизация объектов
- Типизация объектов
- Опциональные свойства (`?`)
- `readonly` свойства
- Index signatures (`[key: string]: T`)
- Избыточные свойства (excess property checks)
- Почему объект проходит проверку, а литерал — нет

---

## Массивы и кортежи
- Типизация массивов (`T[]` vs `Array<T>`)
- Массивы с union типами
- Что такое `tuple`
- Отличие tuple от массива
- Почему tuple — не просто массив

---

## Enum (осторожно)
- Что такое `enum`
- Числовые enum
- Строковые enum
- Проблемы enum в runtime
- Почему enum часто избегают
- Альтернатива enum (union литералов)

---

## Literal Types
- Строковые литеральные типы
- Числовые литеральные типы
- Зачем нужны literal types
- Union + literal types

---

## Type Narrowing (очень важно)
- Что такое narrowing
- `typeof` narrowing
- `in` narrowing
- `instanceof` narrowing
- Пользовательские type guards
- Discriminated unions (tagged unions)

---

## Type Assertions
- Что такое type assertion
- `as` vs `<Type>`
- Почему assertion — не приведение типов
- Опасности type assertion
- Когда assertion допустим

---

## Generics
- Что такое generics
- Зачем нужны generics
- Generic функции
- Generic интерфейсы
- Ограничения generics (`extends`)
- Default generics
- Почему generics лучше `any`

---

## Utility Types
- `Partial`
- `Required`
- `Readonly`
- `Pick`
- `Omit`
- `Record`
- Когда использовать utility types

---

## keyof / typeof / Indexed Access
- Оператор `keyof`
- Использование `typeof` в типах
- Indexed access types (`T[K]`)
- Связка `keyof` + generics

---

## Классы в TypeScript
- Типизация классов
- `public`, `private`, `protected`
- `readonly` в классах
- Абстрактные классы
- Реализация интерфейсов
- Интерфейс vs абстрактный класс

---

## Перегрузка функций
- Что такое function overloads
- Overload signatures
- Зачем нужны перегрузки
- Частые ошибки с overloads

---

## Работа с `this`
- Типизация `this`
- Потеря `this` в callback
- `this: void`
- `noImplicitThis`

---

## never и исчерпывающие проверки
- Когда TypeScript выводит `never`
- Exhaustive check через `never`
- Зачем это нужно в `switch` и union типах

---

## Async / Promise
- Типизация `Promise`
- Что возвращает `async` функция
- Типизация `await`
- Ошибки и `Promise<never>`

---

## DOM и события
- Типизация событий (`MouseEvent`, `KeyboardEvent`)
- Типизация `event.target`
- Почему `event.target` — `EventTarget`
- Приведение типов в DOM

---

## Типизация сторонних библиотек
- Что такое `@types`
- Когда нужны `.d.ts` файлы
- Declaration files
- Почему TS ругается на JS библиотеку

---

## Runtime vs Compile-time
- Какие типы существуют только во время компиляции
- Почему типов нет в runtime
- Частая ошибка: проверка типов в runtime

---

## Частые фейлы на собеседованиях
- Почему `any` — красный флаг
- Почему `as any` — плохо
- Почему enum может быть плохой идеей
- Почему `unknown` безопаснее `any`
- Почему TypeScript не спасает от всех ошибок

---


