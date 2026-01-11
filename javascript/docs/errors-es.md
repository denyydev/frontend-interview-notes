# Errors & ES

Обработка ошибок в JavaScript и связанные особенности языка.  
Материал представлен в формате **вопрос–ответ**.

1. [Как работает `try / catch / finally`](#1-как-работает-try--catch--finally)
2. [Как ловить ошибки в `async / await`](#2-как-ловить-ошибки-в-async--await)
3. [Почему `try/catch` не ловит ошибку в `setTimeout`](#3-почему-trycatch-не-ловит-ошибку-в-settimeout)

## 1. Как работает `try / catch / finally`

`try / catch / finally` — конструкция для обработки ошибок в JavaScript. Понимание её работы важно.

### Базовый синтаксис

```js
try {
  // код, который может выбросить ошибку
  riskyOperation();
} catch (error) {
  // обработка ошибки
  console.error(error);
} finally {
  // выполняется всегда
  console.log("завершено");
}
```

### `try` — блок кода

В `try` пишется код, который может выбросить ошибку:

```js
try {
  const data = JSON.parse("невалидный JSON");
  console.log(data);
} catch (error) {
  console.error("Ошибка парсинга:", error);
}
```

### `catch` — обработка ошибки

`catch` ловит ошибку, выброшенную в `try`:

```js
try {
  throw new Error("Что-то пошло не так");
} catch (error) {
  console.error(error.message); // "Что-то пошло не так"
}
```

**Важно:** `catch` ловит только ошибки из **синхронного кода** в `try`.

### `finally` — выполняется всегда

`finally` выполняется **в любом случае** — и при успехе, и при ошибке:

```js
try {
  console.log("попытка");
  throw new Error("ошибка");
} catch (error) {
  console.log("ошибка поймана");
} finally {
  console.log("всегда выполнится");
}

// Вывод: попытка, ошибка поймана, всегда выполнится
```

### Порядок выполнения

```js
function test() {
  try {
    console.log("1");
    return "return из try";
  } catch (error) {
    console.log("2");
    return "return из catch";
  } finally {
    console.log("3");
  }
}

console.log(test());
// Вывод: 1, 3, return из try
```

**Важно:** `finally` выполняется **перед** `return`!

### `return` в `finally`

Если в `finally` есть `return`, он **переопределяет** возврат из `try`/`catch`:

```js
function test() {
  try {
    return "из try";
  } finally {
    return "из finally"; // этот return имеет приоритет!
  }
}

console.log(test()); // "из finally"
```

### Без `catch`

Можно использовать `try / finally` без `catch`:

```js
try {
  riskyOperation();
} finally {
  cleanup(); // выполнится всегда, даже если была ошибка
}
```

Но ошибка **не будет обработана** и "пробросится" дальше.

### Вложенные `try / catch`

```js
try {
  try {
    throw new Error("внутренняя ошибка");
  } catch (error) {
    console.log("внутренний catch:", error.message);
    throw error; // пробрасываем дальше
  }
} catch (error) {
  console.log("внешний catch:", error.message);
}
```

### Ограничения

`try / catch` **не ловит** ошибки из:

- Асинхронного кода (setTimeout, Promise без await)
- Колбэков
- Событий

```js
try {
  setTimeout(() => {
    throw new Error("ошибка");
  }, 0);
} catch (error) {
  // НЕ поймает! ❌
  console.error(error);
}
```

### Итоги

- `try` — блок кода, который может выбросить ошибку
- `catch` — обработка ошибки
- `finally` — выполняется всегда, даже при return
- `finally` выполняется перед return
- Не ловит ошибки из асинхронного кода

---

## 2. Как ловить ошибки в `async / await`

Ошибки в `async / await` можно ловить несколькими способами. Важно знать все варианты.

### Способ 1: `try / catch` (рекомендуется)

Самый простой и понятный способ:

```js
async function loadData() {
  try {
    const data = await fetchData();
    const processed = await processData(data);
    return processed;
  } catch (error) {
    console.error("Ошибка:", error);
    // можно вернуть значение по умолчанию
    return getDefaultData();
  }
}
```

### Способ 2: `.catch()` на Promise

`async` функция возвращает Promise, поэтому можно использовать `.catch()`:

```js
async function loadData() {
  const data = await fetchData();
  return processData(data);
}

loadData()
  .then((result) => console.log(result))
  .catch((error) => console.error(error));
```

### Комбинированный подход

Можно комбинировать:

```js
async function loadData() {
  try {
    const data = await fetchData();
    return processData(data);
  } catch (error) {
    console.error("Локальная обработка:", error);
    throw error; // пробрасываем дальше
  }
}

loadData()
  .then((result) => console.log(result))
  .catch((error) => console.error("Глобальная обработка:", error));
```

### Ошибки в цепочке `await`

Один `catch` ловит все ошибки в цепочке:

```js
async function example() {
  try {
    const user = await fetchUser(1); // может упасть здесь
    const posts = await fetchPosts(user.id); // или здесь
    const comments = await fetchComments(posts[0].id); // или здесь
  } catch (error) {
    // ловит ошибку из любой части цепочки ✅
    console.error(error);
  }
}
```

### Обработка конкретных ошибок

Можно обрабатывать разные типы ошибок:

```js
async function loadData() {
  try {
    const data = await fetchData();
    return data;
  } catch (error) {
    if (error instanceof TypeError) {
      console.error("Ошибка типа:", error);
    } else if (error instanceof Error) {
      console.error("Общая ошибка:", error);
    } else {
      console.error("Неизвестная ошибка:", error);
    }
    throw error;
  }
}
```

### Продолжение после ошибки

После `catch` можно продолжить выполнение:

```js
async function loadData() {
  try {
    const data = await fetchData();
    return data;
  } catch (error) {
    console.error("Ошибка загрузки:", error);
    // возвращаем значение по умолчанию
    return getDefaultData();
  }
}

const data = await loadData(); // получим данные или default
```

### `finally` в async функциях

`finally` работает и в async функциях:

```js
async function loadData() {
  let loader = true;
  try {
    const data = await fetchData();
    return data;
  } catch (error) {
    console.error(error);
    throw error;
  } finally {
    loader = false; // выполнится всегда
  }
}
```

### ⚠️ Частая ошибка

Забывают обработать ошибку:

```js
async function loadData() {
  const data = await fetchData(); // если ошибка — не обработана! ❌
  return processData(data);
}

// Правильно
async function loadData() {
  try {
    const data = await fetchData();
    return processData(data);
  } catch (error) {
    console.error(error); // ✅ обработано
    throw error;
  }
}
```

### Итоги

- `try / catch` — основной способ в async функциях
- `.catch()` — альтернативный способ на Promise
- Можно комбинировать оба подхода
- Один `catch` ловит все ошибки в цепочке
- Всегда обрабатывайте ошибки!

---

## 3. Почему `try/catch` не ловит ошибку в `setTimeout`

Это частая ошибка новичков. `try / catch` **не работает** с асинхронным кодом в колбэках.

### Проблема

```js
try {
  setTimeout(() => {
    throw new Error("Ошибка!");
  }, 1000);
} catch (error) {
  console.error(error); // НЕ выполнится! ❌
}
```

**Ошибка не поймана!** Почему?

### Причина

`try / catch` работает только с **синхронным кодом**. Когда выполняется `setTimeout`, функция `setTimeout` **завершается сразу** (она не ждёт колбэк). Колбэк выполняется **позже**, когда `try / catch` уже завершился.

### Визуализация

```
1. try блок начинается
2. setTimeout вызывается → возвращает timer ID (синхронно)
3. try блок завершается (ошибки нет!)
4. catch не выполняется
5. Позже: колбэк setTimeout выполняется
6. Ошибка выбрасывается, но try/catch уже не работает ❌
```

### Решение 1: `try / catch` внутри колбэка

```js
setTimeout(() => {
  try {
    throw new Error("Ошибка!");
  } catch (error) {
    console.error(error); // ✅ поймает ошибку
  }
}, 1000);
```

### Решение 2: Promise с async/await

Обернуть `setTimeout` в Promise:

```js
function delay(ms) {
  return new Promise((resolve) => setTimeout(resolve, ms));
}

async function example() {
  try {
    await delay(1000);
    throw new Error("Ошибка!");
  } catch (error) {
    console.error(error); // ✅ поймает ошибку
  }
}
```

### Решение 3: Обработчик ошибок в колбэке

```js
setTimeout(() => {
  try {
    riskyOperation();
  } catch (error) {
    handleError(error); // обрабатываем внутри
  }
}, 1000);
```

### Почему так сделано?

JavaScript **однопоточный**. `try / catch` работает только в текущем контексте выполнения. Асинхронные колбэки выполняются **в другом контексте**, когда исходный контекст уже завершён.

### Аналогия

Представьте:

- **Синхронный код** — как разговор лицом к лицу (можно сразу поймать ошибку)
- **Асинхронный код** — как письмо (отправили, ответ придёт позже, ошибку не поймать сразу)

### С Promise и async/await работает

```js
async function example() {
  try {
    await new Promise((resolve, reject) => {
      setTimeout(() => {
        reject(new Error("Ошибка!"));
      }, 1000);
    });
  } catch (error) {
    console.error(error); // ✅ работает!
  }
}
```

**Почему работает?** Потому что `await` **приостанавливает** выполнение функции, и ошибка выбрасывается в том же контексте.

### Итоги

- `try / catch` не работает с колбэками `setTimeout`
- Колбэк выполняется позже, когда `try / catch` уже завершился
- Решение: `try / catch` внутри колбэка или Promise/async-await
- С `async / await` ошибки ловятся правильно

---
