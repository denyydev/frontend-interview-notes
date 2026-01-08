53. [Что такое Promise](#53-что-такое-promise)
54. [Состояния Promise](#54-состояния-promise)
55. [Цепочки `.then`](#55-цепочки-then)
56. [Способы обработки ошибок в Promise](#56-способы-обработки-ошибок-в-promise)
57. [`Promise.all`, `Promise.race`, `Promise.allSettled`, `Promise.any`](#57-promiseall-promiserace-promiseallsettled-promiseany)
58. [Что происходит, если Promise в `Promise.all` отклоняется](#58-что-происходит-если-promise-в-promiseall-отклоняется)
59. [Что такое `async / await`](#59-что-такое-async--await)
60. [Что возвращает `async` функция](#60-что-возвращает-async-функция)
61. [Можно ли использовать `await` вне `async`](#61-можно-ли-использовать-await-вне-async)
62. [Что такое Event Loop и зачем он нужен](#62-что-такое-event-loop-и-зачем-он-нужен)
63. [Call Stack](#63-call-stack)
64. [Microtasks и Macrotasks](#64-microtasks-и-macrotasks)
65. [Порядок выполнения `async / await`](#65-порядок-выполнения-async--await)
66. [Когда реально выполняется `setTimeout`](#66-когда-реально-выполняется-settimeout)
67. [Порядок выполнения `Promise`, `setTimeout`, `async / await`](#67-порядок-выполнения-promise-settimeout-async--await)

## 53. Что такое Promise

Promise (Обещание) — это объект, представляющий результат **асинхронной операции**, которая может завершиться успешно или с ошибкой.

### Зачем нужны Promise?

До Promise асинхронный код писали через **колбэки** (callback hell):

```js
// Callback hell ❌
getData(function (data) {
  processData(data, function (result) {
    saveData(result, function (saved) {
      // и так далее...
    });
  });
});
```

Promise делает код **более читаемым**:

```js
// Promise ✅
getData()
  .then(processData)
  .then(saveData)
  .then((saved) => {
    // более читаемо!
  });
```

### Создание Promise

```js
const promise = new Promise((resolve, reject) => {
  // асинхронная операция

  if (успешно) {
    resolve("результат"); // успешное завершение
  } else {
    reject("ошибка"); // ошибка
  }
});
```

### Пример

```js
const fetchData = new Promise((resolve, reject) => {
  setTimeout(() => {
    const success = true;

    if (success) {
      resolve({ data: "полученные данные" });
    } else {
      reject(new Error("Ошибка загрузки"));
    }
  }, 1000);
});
```

### Использование Promise

```js
fetchData
  .then((result) => {
    console.log(result); // { data: "полученные данные" }
    return result.data;
  })
  .catch((error) => {
    console.error(error); // обработка ошибки
  });
```

### Состояния Promise

Promise имеет три состояния:

1. **`pending`** — ожидание (начальное состояние)
2. **`fulfilled`** — выполнено успешно (resolve)
3. **`rejected`** — отклонено с ошибкой (reject)

### Визуальная аналогия

Представьте заказ в ресторане:

- **Promise** — это квитанция об заказе
- **pending** — заказ готовится
- **fulfilled** — заказ готов (resolve)
- **rejected** — заказ отменён (reject)

### Преимущества Promise

1. **Читаемость** — код читается сверху вниз
2. **Цепочки** — можно объединять операции
3. **Обработка ошибок** — централизованная через `.catch()`
4. **Параллельное выполнение** — `Promise.all()`, `Promise.race()`

### Итоги

- Promise — объект для асинхронных операций
- Три состояния: pending, fulfilled, rejected
- Делает асинхронный код более читаемым
- Основа для `async/await`

---

## 54. Состояния Promise

Promise может находиться в одном из трёх состояний. Понимание этих состояний важно для работы с асинхронным кодом.

### Три состояния Promise

1. **`pending`** (ожидание) — начальное состояние
2. **`fulfilled`** (выполнено) — операция успешно завершена
3. **`rejected`** (отклонено) — операция завершилась ошибкой

### Состояние: `pending`

**Pending** — начальное состояние, когда Promise создан, но операция ещё не завершена:

```js
const promise = new Promise((resolve, reject) => {
  // пока здесь — состояние pending
  setTimeout(() => {
    resolve("готово");
  }, 1000);
});

// promise сейчас в состоянии pending
```

### Состояние: `fulfilled`

**Fulfilled** — Promise выполнен успешно (вызван `resolve`):

```js
const promise = Promise.resolve("успех");
// promise в состоянии fulfilled

promise.then((result) => {
  console.log(result); // "успех"
});
```

### Состояние: `rejected`

**Rejected** — Promise отклонён с ошибкой (вызван `reject`):

```js
const promise = Promise.reject(new Error("ошибка"));
// promise в состоянии rejected

promise.catch((error) => {
  console.error(error); // Error: ошибка
});
```

### Переходы состояний

Promise может перейти **только один раз** из `pending`:

```
pending → fulfilled ✅
pending → rejected ✅

fulfilled → rejected ❌ (нельзя!)
rejected → fulfilled ❌ (нельзя!)
```

### Проверка состояния

В JavaScript **нет прямого способа** проверить состояние Promise. Но можно понять через поведение:

```js
const promise = new Promise((resolve) => {
  setTimeout(resolve, 1000);
});

// Если then выполняется сразу — fulfilled
// Если then выполняется позже — был pending
promise.then(() => {
  console.log("выполнен");
});
```

### Пример с таймаутом

```js
const fetchData = () => {
  return new Promise((resolve, reject) => {
    // Начальное состояние: pending

    setTimeout(() => {
      const success = Math.random() > 0.5;

      if (success) {
        resolve("данные"); // состояние: fulfilled
      } else {
        reject(new Error("ошибка")); // состояние: rejected
      }
    }, 1000);
  });
};

fetchData()
  .then((data) => console.log(data)) // если fulfilled
  .catch((error) => console.error(error)); // если rejected
```

### Уже выполненные Promise

Promise может быть создан уже в состоянии `fulfilled` или `rejected`:

```js
// Уже fulfilled
const resolved = Promise.resolve("готово");
resolved.then((data) => console.log(data)); // выполнится сразу

// Уже rejected
const rejected = Promise.reject(new Error("ошибка"));
rejected.catch((error) => console.error(error)); // выполнится сразу
```

### Неизменяемость состояния

После того, как Promise перешёл в `fulfilled` или `rejected`, его состояние **нельзя изменить**:

```js
const promise = new Promise((resolve, reject) => {
  resolve("первый результат");
  reject(new Error("ошибка")); // игнорируется! ✅
  resolve("второй результат"); // игнорируется! ✅
});

promise.then((data) => console.log(data)); // "первый результат"
```

### Итоги

- Три состояния: pending, fulfilled, rejected
- Переход только один раз из pending
- После fulfilled/rejected состояние неизменяемо
- Проверить состояние напрямую нельзя

---

## 55. Цепочки `.then`

Одно из главных преимуществ Promise — возможность объединять их в **цепочки** (chaining). Это делает асинхронный код более читаемым.

### Базовый пример

```js
fetchUser(1)
  .then((user) => fetchPosts(user.id))
  .then((posts) => processPosts(posts))
  .then((result) => console.log(result));
```

Каждый `.then()` возвращает **новый Promise**, который можно продолжить цепочкой.

### Как работает цепочка?

```js
Promise.resolve(1)
  .then((value) => {
    console.log(value); // 1
    return value * 2; // возвращает 2
  })
  .then((value) => {
    console.log(value); // 2
    return value * 3; // возвращает 6
  })
  .then((value) => {
    console.log(value); // 6
  });
```

**Важно:** значение, возвращаемое из `.then()`, становится значением следующего Promise в цепочке.

### Возврат значения

```js
fetchData()
  .then((data) => {
    return data.toUpperCase(); // возвращает строку
  })
  .then((upperData) => {
    console.log(upperData); // данные в верхнем регистре
  });
```

### Возврат Promise

Если возвращается Promise, следующий `.then()` ждёт его выполнения:

```js
fetchUser(1)
  .then((user) => {
    return fetchPosts(user.id); // возвращает Promise
  })
  .then((posts) => {
    // выполнится после того, как fetchPosts завершится
    console.log(posts);
  });
```

Это можно упростить:

```js
fetchUser(1)
  .then((user) => fetchPosts(user.id)) // автоматически распаковывается
  .then((posts) => console.log(posts));
```

### Обработка ошибок в цепочке

Ошибка в любой части цепочки переходит в `.catch()`:

```js
fetchData()
  .then((data) => processData(data)) // может выбросить ошибку
  .then((result) => saveData(result)) // может выбросить ошибку
  .catch((error) => {
    // ловит ошибку из любой части цепочки
    console.error(error);
  });
```

### Один catch для всей цепочки

Один `.catch()` обрабатывает ошибки **во всей цепочке**:

```js
fetchUser(1)
  .then((user) => fetchPosts(user.id))
  .then((posts) => processPosts(posts))
  .catch((error) => {
    // ловит ошибку из fetchUser, fetchPosts или processPosts
    console.error(error);
  });
```

### Возврат к успешному пути

После `.catch()` можно вернуться к цепочке `.then()`:

```js
fetchData()
  .then((data) => processData(data))
  .catch((error) => {
    console.error(error);
    return getDefaultData(); // возвращает значение по умолчанию
  })
  .then((data) => {
    // выполнится с данными или default данными
    console.log(data);
  });
```

### Порядок выполнения

```js
console.log("1");

Promise.resolve()
  .then(() => console.log("2"))
  .then(() => console.log("3"));

console.log("4");

// Вывод: 1, 4, 2, 3
// Синхронный код выполняется первым, затем Promise
```

### Вложенные Promise

Избегайте вложенности (callback hell):

```js
// Плохо ❌
fetchUser(1).then((user) => {
  fetchPosts(user.id).then((posts) => {
    processPosts(posts).then((result) => {
      console.log(result);
    });
  });
});

// Хорошо ✅
fetchUser(1)
  .then((user) => fetchPosts(user.id))
  .then((posts) => processPosts(posts))
  .then((result) => console.log(result));
```

### Итоги

- `.then()` возвращает новый Promise
- Можно объединять в цепочки
- Один `.catch()` обрабатывает все ошибки
- Избегайте вложенности, используйте цепочки

---

## 56. Способы обработки ошибок в Promise

Ошибки в Promise можно обрабатывать несколькими способами. Важно знать все варианты.

### Способ 1: `.catch()`

Самый распространённый способ — метод `.catch()`:

```js
fetchData()
  .then((data) => processData(data))
  .catch((error) => {
    console.error("Ошибка:", error);
  });
```

`.catch()` ловит ошибки **во всей цепочке** выше.

### Способ 2: Второй параметр `.then()`

`.then()` принимает два параметра: для успеха и для ошибки:

```js
fetchData().then(
  (data) => console.log(data), // успех
  (error) => console.error(error) // ошибка
);
```

**⚠️ Ограничение:** этот способ ловит ошибки только из предыдущего `.then()`, а не из всей цепочки.

### Способ 3: `try/catch` с `async/await`

В `async` функциях можно использовать `try/catch`:

```js
async function loadData() {
  try {
    const data = await fetchData();
    const processed = await processData(data);
    return processed;
  } catch (error) {
    console.error("Ошибка:", error);
  }
}
```

### Сравнение способов

```js
// Способ 1: .catch() ✅
fetchData()
  .then((data) => processData(data))
  .catch((error) => console.error(error));

// Способ 2: второй параметр .then() ⚠️
fetchData().then(
  (data) => processData(data),
  (error) => console.error(error) // ловит только ошибку из fetchData
);

// Способ 3: async/await ✅
async function load() {
  try {
    const data = await fetchData();
    return await processData(data);
  } catch (error) {
    console.error(error);
  }
}
```

### Обработка ошибок в цепочке

```js
fetchUser(1)
  .then((user) => fetchPosts(user.id)) // может упасть здесь
  .then((posts) => processPosts(posts)) // или здесь
  .catch((error) => {
    // ловит ошибку из любой части цепочки ✅
    console.error(error);
  });
```

### Продолжение после ошибки

После `.catch()` можно продолжить цепочку:

```js
fetchData()
  .then((data) => processData(data))
  .catch((error) => {
    console.error(error);
    return getDefaultData(); // возвращает значение по умолчанию
  })
  .then((data) => {
    // выполнится с данными или default
    console.log(data);
  });
```

### Необработанные ошибки

Если ошибка не обработана, она попадает в глобальный обработчик:

```js
fetchData().then((data) => {
  throw new Error("Ошибка!");
});
// Ошибка не обработана! Может упасть в консоль

// Добавьте .catch()
fetchData()
  .then((data) => {
    throw new Error("Ошибка!");
  })
  .catch((error) => console.error(error)); // ✅ обработано
```

### `finally()` — выполняется всегда

`.finally()` выполняется **в любом случае** — и при успехе, и при ошибке:

```js
fetchData()
  .then((data) => processData(data))
  .catch((error) => console.error(error))
  .finally(() => {
    console.log("Выполнено"); // всегда выполнится
  });
```

**Использование:** очистка ресурсов, скрытие лоадера и т.д.

### Проброс ошибки дальше

Можно пробросить ошибку дальше по цепочке:

```js
fetchData()
  .then((data) => processData(data))
  .catch((error) => {
    console.log("Локальная обработка");
    throw error; // пробрасывает ошибку дальше
  })
  .catch((error) => {
    console.log("Глобальная обработка");
  });
```

### Итоги

- `.catch()` — основной способ обработки ошибок
- `.then(成功, 错误)` — второй параметр для ошибок
- `try/catch` — в async/await функциях
- `.finally()` — выполняется всегда
- Обрабатывайте все ошибки!

---

## 57. `Promise.all`, `Promise.race`, `Promise.allSettled`, `Promise.any`

Эти методы позволяют работать с несколькими Promise одновременно. Каждый решает свою задачу.

### `Promise.all` — все должны выполниться

`Promise.all()` ждёт, пока **все Promise выполнятся**, и возвращает массив результатов:

```js
const promise1 = fetchUser(1);
const promise2 = fetchUser(2);
const promise3 = fetchUser(3);

Promise.all([promise1, promise2, promise3])
  .then((users) => {
    console.log(users); // [user1, user2, user3]
  })
  .catch((error) => {
    // если любой Promise отклонится, весь Promise.all отклоняется
    console.error(error);
  });
```

**Особенность:** если **любой** Promise отклоняется, весь `Promise.all` отклоняется.

### `Promise.race` — кто первый

`Promise.race()` возвращает результат **первого выполнившегося** Promise (успешного или с ошибкой):

```js
const fast = fetchFromCache();
const slow = fetchFromServer();

Promise.race([fast, slow]).then((data) => {
  console.log(data); // данные от того, кто быстрее
});
```

**Использование:** таймауты, выбор самого быстрого источника данных.

### `Promise.allSettled` — все, независимо от результата

`Promise.allSettled()` ждёт, пока **все Promise завершатся** (успешно или с ошибкой), и возвращает массив результатов:

```js
const promise1 = Promise.resolve("успех");
const promise2 = Promise.reject("ошибка");
const promise3 = Promise.resolve("успех2");

Promise.allSettled([promise1, promise2, promise3]).then((results) => {
  console.log(results);
  // [
  //   { status: 'fulfilled', value: 'успех' },
  //   { status: 'rejected', reason: 'ошибка' },
  //   { status: 'fulfilled', value: 'успех2' }
  // ]
});
```

**Преимущество:** не отклоняется, даже если некоторые Promise отклонены.

### `Promise.any` — первый успешный

`Promise.any()` возвращает результат **первого успешно выполнившегося** Promise:

```js
const source1 = fetchFromSource1(); // медленный
const source2 = fetchFromSource2(); // быстрый, но может упасть
const source3 = fetchFromSource3(); // быстрый ✅

Promise.any([source1, source2, source3])
  .then((data) => {
    console.log(data); // данные от первого успешного
  })
  .catch((error) => {
    // только если ВСЕ Promise отклонены
    console.error("Все источники недоступны");
  });
```

**Особенность:** отклоняется только если **все** Promise отклонены.

### Сравнительная таблица

| Метод                | Когда выполняется | Что возвращает              | Когда отклоняется    |
| -------------------- | ----------------- | --------------------------- | -------------------- |
| `Promise.all`        | Все выполнены     | Массив результатов          | Если любой отклонён  |
| `Promise.race`       | Первый выполнился | Результат первого           | Если первый отклонён |
| `Promise.allSettled` | Все завершились   | Массив статусов             | Никогда              |
| `Promise.any`        | Первый успешный   | Результат первого успешного | Если все отклонены   |

### Примеры использования

#### `Promise.all` — загрузка нескольких ресурсов

```js
const [user, posts, comments] = await Promise.all([
  fetchUser(id),
  fetchPosts(id),
  fetchComments(id),
]);
```

#### `Promise.race` — таймаут

```js
const timeout = new Promise((_, reject) =>
  setTimeout(() => reject(new Error("Таймаут")), 5000)
);

Promise.race([fetchData(), timeout])
  .then((data) => console.log(data))
  .catch((error) => console.error(error)); // таймаут или ошибка загрузки
```

#### `Promise.allSettled` — независимые операции

```js
const results = await Promise.allSettled([
  saveToDB(data),
  sendEmail(data),
  updateCache(data),
]);

results.forEach((result, index) => {
  if (result.status === "fulfilled") {
    console.log(`Операция ${index} успешна`);
  } else {
    console.log(`Операция ${index} провалилась:`, result.reason);
  }
});
```

#### `Promise.any` — резервные источники

```js
const data = await Promise.any([
  fetchFromPrimaryServer(),
  fetchFromSecondaryServer(),
  fetchFromCache(),
]);
// Получим данные от первого доступного источника
```

### Итоги

- `Promise.all` — все должны выполниться
- `Promise.race` — кто первый
- `Promise.allSettled` — все, независимо от результата
- `Promise.any` — первый успешный
- Выбор зависит от задачи

---

## 58. Что происходит, если Promise в `Promise.all` отклоняется

Важное поведение `Promise.all` — он отклоняется, если **любой** из Promise отклоняется.

### Поведение `Promise.all`

Если **любой** Promise в массиве отклоняется, весь `Promise.all` **сразу отклоняется**:

```js
const p1 = Promise.resolve("успех 1");
const p2 = Promise.reject("ошибка");
const p3 = Promise.resolve("успех 3");

Promise.all([p1, p2, p3])
  .then((results) => {
    // НЕ выполнится! ❌
    console.log(results);
  })
  .catch((error) => {
    console.error(error); // "ошибка" ✅ (ошибка из p2)
  });
```

### Порядок выполнения

`Promise.all` **не ждёт** остальные Promise после первой ошибки:

```js
const slow = new Promise((resolve) =>
  setTimeout(() => resolve("медленный"), 2000)
);
const fast = Promise.reject("быстрая ошибка");

Promise.all([slow, fast]).catch((error) => {
  console.log(error); // "быстрая ошибка" (сразу, не ждёт slow)
});
```

### Все Promise всё равно выполняются

**Важно:** даже если `Promise.all` отклоняется, **все Promise продолжают выполняться**:

```js
const p1 = new Promise((resolve) => {
  setTimeout(() => {
    console.log("p1 выполнен");
    resolve("p1");
  }, 1000);
});

const p2 = Promise.reject("ошибка");

Promise.all([p1, p2]).catch((error) => {
  console.log(error); // "ошибка" (сразу)
});

// Через 1 секунду:
// "p1 выполнен" ✅ (p1 всё равно выполнился!)
```

### Что делать, если нужно все результаты?

Если нужны **все результаты**, включая ошибки, используйте `Promise.allSettled`:

```js
const p1 = Promise.resolve("успех 1");
const p2 = Promise.reject("ошибка");
const p3 = Promise.resolve("успех 3");

Promise.allSettled([p1, p2, p3]).then((results) => {
  console.log(results);
  // [
  //   { status: 'fulfilled', value: 'успех 1' },
  //   { status: 'rejected', reason: 'ошибка' },
  //   { status: 'fulfilled', value: 'успех 3' }
  // ]
});
```

### Обработка ошибок

Можно обработать ошибки индивидуально перед `Promise.all`:

```js
const promises = [
  fetchData1().catch((error) => ({ error: "данные 1 недоступны" })),
  fetchData2().catch((error) => ({ error: "данные 2 недоступны" })),
  fetchData3().catch((error) => ({ error: "данные 3 недоступны" })),
];

Promise.all(promises).then((results) => {
  // Всегда выполнится, даже если были ошибки
  console.log(results);
});
```

### Пример: загрузка нескольких изображений

```js
const imageUrls = ["img1.jpg", "img2.jpg", "img3.jpg"];

Promise.all(imageUrls.map((url) => loadImage(url)))
  .then((images) => {
    // Все изображения загружены ✅
    images.forEach((img) => document.body.appendChild(img));
  })
  .catch((error) => {
    // Если любое изображение не загрузилось ❌
    console.error("Ошибка загрузки:", error);
  });
```

### Итоги

- `Promise.all` отклоняется, если любой Promise отклонён
- Остальные Promise продолжают выполняться
- Для всех результатов используйте `Promise.allSettled`
- Обрабатывайте ошибки перед `Promise.all` или используйте `.catch()`

---

## 59. Что такое `async / await`

`async / await` — это синтаксический сахар над Promise, который позволяет писать асинхронный код **как синхронный**.

### Зачем нужны `async / await`?

С Promise код выглядит так:

```js
// Promise (цепочка .then)
fetchUser(1)
  .then((user) => fetchPosts(user.id))
  .then((posts) => processPosts(posts))
  .then((result) => console.log(result))
  .catch((error) => console.error(error));
```

С `async / await` код читается проще:

```js
// async/await (читается как синхронный код)
async function loadData() {
  try {
    const user = await fetchUser(1);
    const posts = await fetchPosts(user.id);
    const result = await processPosts(posts);
    console.log(result);
  } catch (error) {
    console.error(error);
  }
}
```

### `async` функция

Функция с ключевым словом `async` **всегда возвращает Promise**:

```js
async function getData() {
  return "данные";
}

getData().then((data) => console.log(data)); // "данные"
// или
const data = await getData(); // "данные"
```

### `await` — ожидание Promise

`await` приостанавливает выполнение функции до завершения Promise:

```js
async function example() {
  const data = await fetchData(); // ждёт выполнения fetchData()
  console.log(data); // выполнится после получения данных
}
```

**Важно:** `await` можно использовать **только внутри `async` функции**.

### Пример сравнения

```js
// С Promise
function loadUser(id) {
  return fetchUser(id)
    .then((user) => {
      return fetchPosts(user.id);
    })
    .then((posts) => {
      return { user, posts };
    });
}

// С async/await
async function loadUser(id) {
  const user = await fetchUser(id);
  const posts = await fetchPosts(user.id);
  return { user, posts };
}
```

### Обработка ошибок

С `async / await` ошибки обрабатываются через `try / catch`:

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

### Параллельное выполнение

Для параллельного выполнения используйте `Promise.all`:

```js
// Последовательно (медленно) ❌
async function slow() {
  const user = await fetchUser(1); // ждём
  const posts = await fetchPosts(1); // ждём
  const comments = await fetchComments(1); // ждём
}

// Параллельно (быстро) ✅
async function fast() {
  const [user, posts, comments] = await Promise.all([
    fetchUser(1),
    fetchPosts(1),
    fetchComments(1),
  ]);
}
```

### Возврат значений

`async` функция всегда возвращает Promise:

```js
async function getValue() {
  return 42; // автоматически обёрнуто в Promise.resolve(42)
}

getValue().then((value) => console.log(value)); // 42
```

### Итоги

- `async / await` — синтаксический сахар над Promise
- Код читается как синхронный
- `async` функция всегда возвращает Promise
- `await` ждёт выполнения Promise
- Ошибки обрабатываются через `try / catch`

---

## 60. Что возвращает `async` функция

Важно понимать: `async` функция **всегда возвращает Promise**, даже если вы возвращаете обычное значение.

### Базовое поведение

`async` функция **всегда возвращает Promise**:

```js
async function getValue() {
  return 42;
}

const result = getValue();
console.log(result); // Promise { <fulfilled>: 42 } ✅

result.then((value) => console.log(value)); // 42
// или
const value = await getValue(); // 42
```

### Возврат обычного значения

Если возвращается обычное значение, оно **автоматически оборачивается** в `Promise.resolve()`:

```js
async function getString() {
  return "hello";
}

// Эквивалентно:
function getString() {
  return Promise.resolve("hello");
}
```

### Возврат Promise

Если возвращается Promise, он возвращается как есть:

```js
async function getData() {
  return fetch("/api/data"); // возвращает Promise
}

// Результат тот же
```

### Возврат ошибки

Если выбрасывается ошибка, возвращается **отклонённый Promise**:

```js
async function throwError() {
  throw new Error("Ошибка!");
}

throwError().catch((error) => console.error(error)); // Error: Ошибка!

// Эквивалентно:
function throwError() {
  return Promise.reject(new Error("Ошибка!"));
}
```

### Примеры

```js
// Возврат примитива
async function getNumber() {
  return 42;
}
getNumber().then((n) => console.log(n)); // 42

// Возврат объекта
async function getObject() {
  return { name: "Alex" };
}
getObject().then((obj) => console.log(obj)); // { name: "Alex" }

// Возврат массива
async function getArray() {
  return [1, 2, 3];
}
getArray().then((arr) => console.log(arr)); // [1, 2, 3]

// Без return (undefined)
async function noReturn() {
  // нет return
}
noReturn().then((value) => console.log(value)); // undefined
```

### Возврат из `await`

Значение из `await` — это результат Promise:

```js
async function loadData() {
  const data = await fetchData(); // data — результат Promise
  return data; // возвращает Promise с data
}
```

### Цепочка async функций

`async` функции можно вызывать из других `async` функций:

```js
async function getUserId() {
  return 1;
}

async function getUser(id) {
  return { id, name: "Alex" };
}

async function loadUser() {
  const id = await getUserId(); // ждём Promise
  const user = await getUser(id); // ждём Promise
  return user; // возвращает Promise с user
}
```

### Важно понимать

```js
async function example() {
  return "hello";
}

// Это НЕ то же самое, что:
function example() {
  return "hello";
}

// Это то же самое, что:
function example() {
  return Promise.resolve("hello");
}
```

### Итоги

- `async` функция всегда возвращает Promise
- Обычные значения оборачиваются в `Promise.resolve()`
- Ошибки оборачиваются в `Promise.reject()`
- Можно использовать `.then()` или `await`

---

## 61. Можно ли использовать `await` вне `async`

Короткий ответ: **нет, нельзя** (в обычном коде). Но есть исключения.

### Обычное правило

`await` можно использовать **только внутри `async` функции**:

```js
// Ошибка ❌
const data = await fetchData(); // SyntaxError

// Правильно ✅
async function loadData() {
  const data = await fetchData(); // работает
}
```

### Исключение: Top-level await (ES2022)

В **ES2022 модулях** можно использовать `await` на верхнем уровне (top-level await):

```js
// В модуле (ES2022)
const data = await fetchData(); // ✅ работает!

export { data };
```

**Условия:**

- Файл должен быть ES модулем (`type="module"` в HTML или `.mjs` расширение)
- Поддержка ES2022

**Пример:**

```js
// main.mjs
const response = await fetch("/api/data");
const data = await response.json();
console.log(data);
```

### Почему нужно `async`?

`await` **приостанавливает выполнение** функции. JavaScript должен знать, что функция может быть приостановлена, поэтому нужна пометка `async`.

```js
async function example() {
  const data = await fetchData(); // функция приостановлена здесь
  console.log(data); // выполнится после получения данных
}
```

### Что происходит при `await`?

Когда встречается `await`:

1. Выполнение функции **приостанавливается**
2. Контроль возвращается в Event Loop
3. Когда Promise завершается, выполнение продолжается
4. `await` возвращает результат Promise

Без `async` JavaScript не знает, как обработать приостановку.

### Обходные пути (не рекомендуются)

Можно использовать IIFE:

```js
(async function () {
  const data = await fetchData();
  console.log(data);
})();
```

Но лучше просто сделать функцию `async`.

### Итоги

- `await` можно использовать только в `async` функции
- Исключение: top-level await в ES2022 модулях
- Без `async` будет SyntaxError
- `async` нужна, чтобы JavaScript знал о приостановке выполнения

---

## 62. Что такое Event Loop и зачем он нужен

Event Loop (Цикл событий) — это механизм JavaScript, который позволяет выполнять **асинхронный код**, не блокируя основной поток.

### Проблема: JavaScript однопоточный

JavaScript **однопоточный** — может выполнять только одну задачу за раз. Но нам нужно:

- Загружать данные с сервера
- Обрабатывать события
- Анимации
- И всё это одновременно?

Event Loop решает эту проблему!

### Как работает Event Loop?

Event Loop работает по принципу **очереди задач**:

1. Выполняется весь **синхронный код**
2. Когда стек пуст, Event Loop берёт задачу из **очереди**
3. Выполняет её
4. Повторяет процесс

### Визуальная аналогия

Представьте ресторан:

- **Официант (Event Loop)** — один человек
- **Столы (Call Stack)** — где он работает
- **Заказы (очередь задач)** — что нужно сделать

Официант обслуживает один стол, затем переходит к следующему заказу. Так работает Event Loop.

### Простой пример

```js
console.log("1");

setTimeout(() => {
  console.log("2");
}, 0);

console.log("3");

// Вывод: 1, 3, 2
```

**Почему так?**

1. Выполняется `console.log("1")` — синхронно
2. `setTimeout` ставит задачу в очередь
3. Выполняется `console.log("3")` — синхронно
4. Когда стек пуст, Event Loop берёт задачу из `setTimeout`
5. Выполняется `console.log("2")`

### Зачем нужен Event Loop?

Без Event Loop:

- Асинхронные операции блокировали бы основной поток
- Интерфейс "зависал" бы
- Нельзя было бы делать несколько вещей одновременно

С Event Loop:

- Асинхронные операции не блокируют
- Интерфейс остаётся отзывчивым
- Можно "одновременно" делать несколько вещей

### Очереди задач

Event Loop управляет двумя типами очередей:

1. **Microtasks** (микротаски) — высокий приоритет

   - `Promise.then/catch/finally`
   - `queueMicrotask()`

2. **Macrotasks** (макротаски) — низкий приоритет
   - `setTimeout`
   - `setInterval`
   - События DOM

### Порядок выполнения

1. Весь синхронный код
2. **Все microtasks** (до полной очистки)
3. Одна **macrotask**
4. Повтор

### Итоги

- Event Loop — механизм выполнения асинхронного кода
- JavaScript однопоточный, Event Loop делает его неблокирующим
- Управляет очередями задач (microtasks и macrotasks)
- Без Event Loop интерфейс бы "зависал"

---

## 63. Call Stack

Call Stack (Стек вызовов) — это структура данных, которая отслеживает, **какие функции выполняются в данный момент**.

### Что такое стек?

Стек — это структура данных типа **LIFO** (Last In, First Out — последний пришёл, первый ушёл). Как стопка тарелок: кладём сверху, берём сверху.

### Как работает Call Stack?

Когда вызывается функция, она **добавляется наверх** стека. Когда функция завершается, она **убирается из стека**.

```js
function first() {
  console.log("first");
  second();
}

function second() {
  console.log("second");
  third();
}

function third() {
  console.log("third");
}

first();
```

**Состояние стека:**

1. `first()` вызывается → стек: `[first]`
2. `first()` вызывает `second()` → стек: `[first, second]`
3. `second()` вызывает `third()` → стек: `[first, second, third]`
4. `third()` завершается → стек: `[first, second]`
5. `second()` завершается → стек: `[first]`
6. `first()` завершается → стек: `[]`

### Визуализация

```
┌─────────┐
│ third() │  ← выполняется
├─────────┤
│ second()│
├─────────┤
│ first() │
└─────────┘
Call Stack
```

### Переполнение стека (Stack Overflow)

Если функция вызывает сама себя бесконечно, стек переполняется:

```js
function infinite() {
  infinite(); // вызывает сама себя
}

infinite(); // RangeError: Maximum call stack size exceeded ❌
```

### Синхронный код выполняется сразу

Синхронный код попадает в Call Stack и выполняется **немедленно**:

```js
console.log("1");
console.log("2");
console.log("3");

// Выполняется: 1, 2, 3 (сразу, по порядку)
```

### Асинхронный код — не в стеке

Асинхронные операции **не попадают в Call Stack сразу**:

```js
console.log("1");

setTimeout(() => {
  console.log("2"); // не в стеке сразу!
}, 0);

console.log("3");

// Вывод: 1, 3, 2
// "2" выполнится позже, когда стек пуст
```

### Связь с Event Loop

Event Loop **ждёт, пока Call Stack пуст**, затем берёт задачу из очереди и выполняет её:

1. Call Stack пуст?
2. Да → Event Loop берёт задачу из очереди
3. Кладёт задачу в Call Stack
4. Выполняется
5. Повтор

### Итоги

- Call Stack — структура данных для отслеживания выполняющихся функций
- Работает по принципу LIFO
- Синхронный код выполняется сразу
- Асинхронный код ждёт, пока стек пуст
- Event Loop управляет задачами, когда стек пуст

---

## 64. Microtasks и Macrotasks

В Event Loop есть два типа очередей с разным приоритетом: **microtasks** и **macrotasks**.

### Что такое microtasks и macrotasks?

- **Microtasks** (микротаски) — задачи с **высоким приоритетом**
- **Macrotasks** (макротаски) — задачи с **низким приоритетом**

### Microtasks

Microtasks выполняются **сразу после синхронного кода**, до macrotasks:

```js
console.log("1");

Promise.resolve().then(() => console.log("2"));

setTimeout(() => console.log("3"), 0);

console.log("4");

// Вывод: 1, 4, 2, 3
// Порядок: синхронный код → microtasks → macrotasks
```

**Что относится к microtasks:**

- `Promise.then()`
- `Promise.catch()`
- `Promise.finally()`
- `queueMicrotask()`
- `MutationObserver`

### Macrotasks

Macrotasks выполняются **после всех microtasks**:

```js
console.log("1");

setTimeout(() => console.log("2"), 0);

Promise.resolve().then(() => console.log("3"));

console.log("4");

// Вывод: 1, 4, 3, 2
// microtask (3) выполнился раньше macrotask (2)
```

**Что относится к macrotasks:**

- `setTimeout`
- `setInterval`
- События DOM (клики, скролл и т.д.)
- `setImmediate` (Node.js)
- `requestAnimationFrame` (браузер)

### Порядок выполнения

1. **Весь синхронный код** (Call Stack)
2. **Все microtasks** (до полной очистки!)
3. **Одна macrotask**
4. Снова все microtasks
5. Следующая macrotask
6. И так далее

### Пример: все microtasks выполняются до macrotasks

```js
console.log("1");

setTimeout(() => console.log("macrotask"), 0);

Promise.resolve().then(() => console.log("microtask 1"));
Promise.resolve().then(() => console.log("microtask 2"));
Promise.resolve().then(() => {
  console.log("microtask 3");
  Promise.resolve().then(() => console.log("microtask 4"));
});

console.log("2");

// Вывод: 1, 2, microtask 1, microtask 2, microtask 3, microtask 4, macrotask
// Все microtasks выполняются до macrotask!
```

### Почему так сделано?

Microtasks — это обычно **обработчики Promise**, которые должны выполниться быстро, чтобы код был предсказуемым.

Macrotasks — это **более тяжёлые операции**, которые могут подождать.

### Цепочки microtasks

Если в microtask создаётся новый microtask, он **тоже выполнится до macrotasks**:

```js
Promise.resolve().then(() => {
  console.log("1");
  Promise.resolve().then(() => {
    console.log("2");
    Promise.resolve().then(() => console.log("3"));
  });
});

setTimeout(() => console.log("macrotask"), 0);

// Вывод: 1, 2, 3, macrotask
// Все microtasks выполняются до macrotask
```

### Сравнительная таблица

| Тип        | Приоритет | Примеры                      | Когда выполняется            |
| ---------- | --------- | ---------------------------- | ---------------------------- |
| Microtasks | Высокий   | Promise.then, queueMicrotask | Сразу после синхронного кода |
| Macrotasks | Низкий    | setTimeout, события          | После всех microtasks        |

### Итоги

- Microtasks — высокий приоритет, выполняются первыми
- Macrotasks — низкий приоритет, выполняются после microtasks
- Все microtasks выполняются до первой macrotask
- Это обеспечивает предсказуемость асинхронного кода

---

## 65. Порядок выполнения `async / await`

`async / await` работает поверх Promise и подчиняется правилам Event Loop. Важно понимать порядок выполнения.

### Код до `await` — синхронный

Код **до первого `await`** выполняется **синхронно**:

```js
console.log("1");

async function test() {
  console.log("2"); // синхронно!
  await Promise.resolve();
  console.log("3");
}

test();
console.log("4");

// Вывод: 1, 2, 4, 3
```

**Почему так?**

1. `console.log("1")` — синхронно
2. `test()` вызывается
3. `console.log("2")` — синхронно (до await)
4. `await` приостанавливает выполнение
5. `console.log("4")` — синхронно
6. `console.log("3")` — после await (microtask)

### `await` создаёт microtask

Когда встречается `await`, код после него становится **microtask**:

```js
async function example() {
  console.log("до await");
  await Promise.resolve(); // здесь выполнение приостанавливается
  console.log("после await"); // это microtask
}
```

### Порядок выполнения

```js
console.log("1");

async function async1() {
  console.log("2");
  await Promise.resolve();
  console.log("3");
}

async function async2() {
  console.log("4");
  await Promise.resolve();
  console.log("5");
}

async1();
async2();
console.log("6");

// Вывод: 1, 2, 4, 6, 3, 5
```

**Почему?**

1. `1` — синхронно
2. `async1()` вызывается, `2` — синхронно
3. `await` в `async1()` → код после него в microtasks
4. `async2()` вызывается, `4` — синхронно
5. `await` в `async2()` → код после него в microtasks
6. `6` — синхронно
7. `3`, `5` — microtasks (в порядке создания)

### `await` всегда "уступает" стеку

`await` **всегда приостанавливает выполнение**, даже если Promise уже resolved:

```js
console.log("1");

async function test() {
  console.log("2");
  await Promise.resolve(); // уже resolved, но всё равно уступает
  console.log("3");
}

test();
console.log("4");

// Вывод: 1, 2, 4, 3
// "3" выполнится после "4", даже если Promise уже готов
```

### Цепочки `await`

```js
async function example() {
  console.log("1");
  await Promise.resolve();
  console.log("2");
  await Promise.resolve();
  console.log("3");
}

example();
console.log("4");

// Вывод: 1, 4, 2, 3
```

### Вложенные async функции

```js
async function outer() {
  console.log("outer 1");
  await inner();
  console.log("outer 2");
}

async function inner() {
  console.log("inner 1");
  await Promise.resolve();
  console.log("inner 2");
}

outer();
console.log("sync");

// Вывод: outer 1, inner 1, sync, inner 2, outer 2
```

### Итоги

- Код до `await` выполняется синхронно
- `await` приостанавливает выполнение
- Код после `await` становится microtask
- `await` всегда уступает стеку, даже если Promise готов
- Порядок выполнения предсказуемый

---

## 66. Когда реально выполняется `setTimeout`

`setTimeout` выполняется **не точно** через указанное время. Важно понимать, когда он реально выполнится.

### Задержка — минимальное время

Задержка в `setTimeout` — это **минимальное время ожидания**, а не точное:

```js
setTimeout(() => {
  console.log("выполнилось");
}, 1000);

// Выполнится НЕ РАНЬШЕ чем через 1000мс
// Но может позже!
```

### Когда выполняется `setTimeout`?

`setTimeout` выполнится, когда:

1. Прошло **минимум** указанное время
2. Call Stack **пуст**
3. Все **microtasks** выполнены
4. Дошла очередь до этой **macrotask**

### Пример: блокирующий код

```js
setTimeout(() => {
  console.log("timeout");
}, 0);

// Блокирующий код
for (let i = 0; i < 1000000000; i++) {
  // долгая операция
}

console.log("done");

// Вывод: done, timeout
// timeout выполнится ПОСЛЕ блокирующего кода, даже если задержка 0!
```

### `setTimeout(fn, 0)` не означает "сразу"

```js
console.log("1");

setTimeout(() => {
  console.log("2");
}, 0);

Promise.resolve().then(() => console.log("3"));

console.log("4");

// Вывод: 1, 4, 3, 2
// setTimeout(0) выполнится ПОСЛЕ microtasks!
```

### Порядок выполнения

```js
console.log("sync 1");

setTimeout(() => console.log("timeout 1"), 0);
setTimeout(() => console.log("timeout 2"), 0);

Promise.resolve().then(() => console.log("microtask 1"));
Promise.resolve().then(() => console.log("microtask 2"));

console.log("sync 2");

// Вывод: sync 1, sync 2, microtask 1, microtask 2, timeout 1, timeout 2
```

### Почему так?

1. `setTimeout` ставит задачу в очередь **macrotasks**
2. Macrotasks выполняются **после всех microtasks**
3. Даже с задержкой 0, `setTimeout` ждёт своей очереди

### Реальное время выполнения

```js
const start = Date.now();

setTimeout(() => {
  const elapsed = Date.now() - start;
  console.log(`Прошло ${elapsed}мс`); // может быть больше 1000!
}, 1000);

// Если стек занят, реальное время будет больше 1000мс
```

### Точность таймеров

Таймеры в JavaScript **не точные**:

- Минимальная задержка в браузере — около 4мс (для `setTimeout(0)`)
- В фоновых вкладках задержки могут быть больше
- Точность зависит от нагрузки системы

### Итоги

- `setTimeout` выполняется не раньше указанного времени
- Может выполниться позже, если стек занят
- Выполняется после всех microtasks
- `setTimeout(0)` не означает "сразу"
- Таймеры не точные

---

## 67. Порядок выполнения `Promise`, `setTimeout`, `async / await`

Это классический вопрос на собеседованиях. Важно понимать порядок выполнения всех этих конструкций.

### Общее правило

Порядок выполнения:

1. **Синхронный код**
2. **Microtasks** (Promise, async/await)
3. **Macrotasks** (setTimeout)

### Пример 1: Promise и setTimeout

```js
console.log("1");

setTimeout(() => console.log("2"), 0);

Promise.resolve().then(() => console.log("3"));

console.log("4");

// Вывод: 1, 4, 3, 2
// Порядок: sync → microtasks → macrotasks
```

### Пример 2: async/await и setTimeout

```js
console.log("1");

async function async1() {
  console.log("2");
  await Promise.resolve();
  console.log("3");
}

setTimeout(() => console.log("4"), 0);

async1();
console.log("5");

// Вывод: 1, 2, 5, 3, 4
```

**Разбор:**

1. `1` — синхронно
2. `async1()` вызывается, `2` — синхронно (до await)
3. `await` приостанавливает, код после него → microtask
4. `setTimeout` → macrotask
5. `5` — синхронно
6. `3` — microtask (выполняется первым)
7. `4` — macrotask

### Пример 3: Сложный случай

```js
console.log("1");

setTimeout(() => console.log("2"), 0);

Promise.resolve().then(() => {
  console.log("3");
  Promise.resolve().then(() => console.log("4"));
});

setTimeout(() => console.log("5"), 0);

async function test() {
  console.log("6");
  await Promise.resolve();
  console.log("7");
}

test();
console.log("8");

// Вывод: 1, 6, 8, 3, 4, 7, 2, 5
```

**Разбор:**

1. `1` — sync
2. `setTimeout(2)` → macrotask
3. `Promise.then(3)` → microtask
4. `setTimeout(5)` → macrotask
5. `test()` вызывается, `6` — sync
6. `await` → код после него → microtask
7. `8` — sync
8. Microtasks: `3`, создаётся новый microtask `4`, `4`, `7`
9. Macrotasks: `2`, `5`

### Важные правила

1. **Все microtasks выполняются до macrotasks**
2. **Код до `await` — синхронный**
3. **Код после `await` — microtask**
4. **`setTimeout` — всегда macrotask**

### Визуализация

```
Синхронный код
    ↓
Все microtasks (до очистки)
    ↓
Одна macrotask
    ↓
Все microtasks (если создались)
    ↓
Следующая macrotask
```

### Итоги

- Порядок: sync → microtasks → macrotasks
- Promise и async/await — microtasks
- setTimeout — macrotask
- Все microtasks выполняются до macrotasks
- Код до await — синхронный, после await — microtask

---
