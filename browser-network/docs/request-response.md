# HTTP Request и Response

Структура HTTP-запроса и ответа, их компоненты и использование.  
Материал представлен в формате **вопрос–ответ**.

---

## Содержание (Table of Contents)

1. [Request vs Response](#1-request-vs-response)
2. [Headers](#2-headers)
3. [Body](#3-body)
4. [Query params](#4-query-params)
5. [Path params](#5-path-params)

---

## 1. Request vs Response

HTTP работает по модели **запрос-ответ** (request-response): клиент отправляет **запрос** (request), а сервер возвращает **ответ** (response).

### Простое определение

- **Request (запрос)** — это сообщение, которое клиент отправляет серверу
- **Response (ответ)** — это сообщение, которое сервер отправляет клиенту в ответ на запрос

**Аналогия:** Запрос — это вопрос, ответ — это ответ на вопрос.

### Структура HTTP Request

HTTP-запрос состоит из нескольких частей:

```
GET /api/users?page=1 HTTP/1.1
Host: api.example.com
User-Agent: Mozilla/5.0
Accept: application/json
Authorization: Bearer token123

(тело запроса, если есть)
```

**Составляющие запроса:**
1. **Строка запроса** (Request Line)
   - Метод (`GET`, `POST`, `PUT`, и т.д.)
   - Путь (`/api/users`)
   - Версия протокола (`HTTP/1.1`)
2. **Заголовки** (Headers)
   - `Host`, `User-Agent`, `Accept`, `Authorization`, и т.д.
3. **Пустая строка** (разделитель)
4. **Тело запроса** (Body) — опционально

### Структура HTTP Response

HTTP-ответ также состоит из нескольких частей:

```
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 123
Date: Mon, 01 Jan 2024 12:00:00 GMT

{
  "users": [
    { "id": 1, "name": "Алекс" }
  ]
}
```

**Составляющие ответа:**
1. **Строка статуса** (Status Line)
   - Версия протокола (`HTTP/1.1`)
   - Статус-код (`200`)
   - Статус-текст (`OK`)
2. **Заголовки** (Headers)
   - `Content-Type`, `Content-Length`, `Date`, и т.д.
3. **Пустая строка** (разделитель)
4. **Тело ответа** (Body)

### Сравнительная таблица

| Характеристика | Request | Response |
|---------------|---------|----------|
| **Кто отправляет** | Клиент | Сервер |
| **Начало** | Метод + путь (`GET /api/users`) | Статус-код (`200 OK`) |
| **Заголовки** | `Host`, `User-Agent`, `Authorization` | `Content-Type`, `Content-Length` |
| **Тело** | Данные для отправки (POST, PUT, PATCH) | Данные от сервера |
| **Обязательное тело** | ❌ Нет (опционально) | ❌ Нет (опционально) |

### Визуальная аналогия

**Request** — как **письмо с просьбой**:
- На конверте указано: "Кому" (Host), "Что нужно" (метод и путь)
- Внутри письма (body) — подробности запроса

**Response** — как **ответ на письмо**:
- На конверте указано: статус (200 OK — всё хорошо, 404 — не найдено)
- Внутри письма (body) — запрошенные данные

### Примеры в JavaScript

#### Простой GET запрос (без тела)

```javascript
// Клиент отправляет запрос
fetch('https://api.example.com/users')
  .then(response => {
    // response — это Response объект
    return response.json();
  })
  .then(data => {
    // data — это данные из тела ответа
    console.log(data);
  });
```

**Что происходит:**
1. Браузер формирует **Request**
2. Отправляет на сервер
3. Сервер обрабатывает
4. Сервер возвращает **Response**
5. Браузер парсит ответ

#### POST запрос (с телом)

```javascript
// Клиент отправляет запрос с данными
fetch('https://api.example.com/users', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    name: 'Алекс',
    email: 'alex@example.com'
  })
})
  .then(response => response.json())
  .then(data => console.log(data));
```

**Request:**
```
POST /users HTTP/1.1
Host: api.example.com
Content-Type: application/json

{
  "name": "Алекс",
  "email": "alex@example.com"
}
```

**Response:**
```
HTTP/1.1 201 Created
Content-Type: application/json

{
  "id": 123,
  "name": "Алекс",
  "email": "alex@example.com"
}
```

### Направление данных

#### Request (от клиента к серверу)

```javascript
// Данные идут ОТ клиента К серверу
const requestData = {
  username: 'alex',
  password: 'secret'
};

fetch('/api/login', {
  method: 'POST',
  body: JSON.stringify(requestData)
});
```

#### Response (от сервера к клиенту)

```javascript
// Данные идут ОТ сервера К клиенту
fetch('/api/users')
  .then(response => response.json())
  .then(data => {
    // data — данные, которые сервер отправил клиенту
    console.log(data);
  });
```

### Разница в использовании

#### Request

```javascript
// Request содержит:
// 1. Метод
method: 'POST'

// 2. URL
url: 'https://api.example.com/users'

// 3. Заголовки
headers: {
  'Content-Type': 'application/json',
  'Authorization': 'Bearer token123'
}

// 4. Тело
body: JSON.stringify({ name: 'Алекс' })
```

#### Response

```javascript
// Response содержит:
fetch('/api/users')
  .then(response => {
    // 1. Статус-код
    console.log(response.status);  // 200
    
    // 2. Статус-текст
    console.log(response.statusText);  // OK
    
    // 3. Заголовки
    console.log(response.headers.get('Content-Type'));  // application/json
    
    // 4. Тело (нужно прочитать)
    return response.json();
  })
  .then(data => {
    // 5. Данные из тела
    console.log(data);
  });
```

### ⚠️ Частая ошибка

Путают направление данных:

```javascript
// ❌ Неправильное понимание
// Request — это данные, которые мы получаем
// Response — это данные, которые мы отправляем

// ✅ Правильное понимание
// Request — данные, которые клиент отправляет серверу
// Response — данные, которые сервер отправляет клиенту
```

### Итоги

- **Request** — запрос от клиента к серверу
- **Response** — ответ от сервера к клиенту
- Request содержит: метод, путь, заголовки, тело (опционально)
- Response содержит: статус-код, заголовки, тело (опционально)
- HTTP работает по модели запрос-ответ
- Каждый запрос получает ответ

---

## 2. Headers

HTTP заголовки (headers) — это **метаданные** запроса или ответа, которые передают дополнительную информацию о запросе/ответе, но не являются самими данными.

### Простое определение

Заголовки — это "настройки" запроса или ответа: они говорят, **как** обрабатывать данные, но не содержат сами данные.

**Аналогия:** Заголовки — как "инструкция" на посылке: куда доставить, как обработать, но не сама посылка.

### Структура заголовков

```
Header-Name: header-value
```

**Формат:**
- Имя заголовка (регистр не важен, но обычно используется заглавная первая буква каждого слова)
- Двоеточие с пробелом
- Значение заголовка

**Примеры:**
```
Content-Type: application/json
Authorization: Bearer token123
User-Agent: Mozilla/5.0
```

### Типы заголовков

#### 1. Request Headers (заголовки запроса)

Заголовки, которые клиент отправляет серверу.

##### Host — домен сервера

```
Host: api.example.com
```

**Зачем:** Указывает домен, к которому обращается клиент (важно для виртуальных хостов).

```javascript
fetch('https://api.example.com/users', {
  headers: {
    // Host устанавливается автоматически браузером
  }
});
```

##### User-Agent — информация о клиенте

```
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36
```

**Зачем:** Сообщает серверу, какой браузер/клиент делает запрос.

```javascript
// Устанавливается автоматически браузером
fetch('/api/data');
// Браузер добавляет User-Agent автоматически
```

##### Authorization — токен авторизации

```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

**Зачем:** Передаёт учётные данные для авторизации.

```javascript
fetch('/api/profile', {
  headers: {
    'Authorization': `Bearer ${token}`
  }
});
```

##### Content-Type — тип данных в теле запроса

```
Content-Type: application/json
```

**Зачем:** Говорит серверу, в каком формате отправляются данные.

```javascript
fetch('/api/users', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({ name: 'Алекс' })
});
```

##### Accept — какие типы данных клиент принимает

```
Accept: application/json, text/html
```

**Зачем:** Говорит серверу, какие форматы данных клиент может обработать.

```javascript
fetch('/api/data', {
  headers: {
    'Accept': 'application/json'
  }
});
```

#### 2. Response Headers (заголовки ответа)

Заголовки, которые сервер отправляет клиенту.

##### Content-Type — тип данных в теле ответа

```
Content-Type: application/json
```

**Зачем:** Говорит клиенту, в каком формате данные в теле ответа.

```javascript
fetch('/api/users')
  .then(response => {
    const contentType = response.headers.get('Content-Type');
    console.log(contentType);  // application/json
    
    if (contentType.includes('json')) {
      return response.json();
    }
  });
```

##### Content-Length — размер тела ответа

```
Content-Length: 1234
```

**Зачем:** Указывает размер тела ответа в байтах.

##### Status — статус-код (в строке статуса)

```
HTTP/1.1 200 OK
```

**Зачем:** Информирует клиента о результате обработки запроса.

##### Set-Cookie — установка cookie

```
Set-Cookie: sessionId=abc123; Path=/; HttpOnly
```

**Зачем:** Устанавливает cookie в браузере клиента.

```javascript
fetch('/api/login', {
  method: 'POST',
  body: JSON.stringify({ username: 'alex', password: 'secret' })
})
  .then(response => {
    // Браузер автоматически сохраняет cookie из Set-Cookie заголовка
    // Cookie автоматически отправляются в следующих запросах
  });
```

##### Location — перенаправление

```
Location: https://example.com/new-page
```

**Зачем:** Указывает новый URL для перенаправления (3xx статус-коды).

```javascript
fetch('/old-page')
  .then(response => {
    if (response.status === 301) {
      const newUrl = response.headers.get('Location');
      console.log(newUrl);  // https://example.com/new-page
    }
  });
```

### Кастомные заголовки

Можно создавать свои заголовки (обычно с префиксом `X-`):

```javascript
fetch('/api/data', {
  headers: {
    'X-Custom-Header': 'my-value',
    'X-Request-ID': 'req-123-abc'
  }
});
```

**На сервере:**
```javascript
// Express.js пример
app.get('/api/data', (req, res) => {
  const customHeader = req.headers['x-custom-header'];
  console.log(customHeader);  // my-value
});
```

### Чтение заголовков в JavaScript

#### Чтение заголовков ответа

```javascript
fetch('/api/users')
  .then(response => {
    // Получить один заголовок
    const contentType = response.headers.get('Content-Type');
    
    // Получить все заголовки
    response.headers.forEach((value, key) => {
      console.log(`${key}: ${value}`);
    });
    
    // Проверить наличие заголовка
    if (response.headers.has('X-Custom-Header')) {
      console.log('Есть кастомный заголовок');
    }
  });
```

#### Установка заголовков запроса

```javascript
fetch('/api/users', {
  headers: {
    'Content-Type': 'application/json',
    'Authorization': 'Bearer token123',
    'X-Custom-Header': 'value'
  }
});
```

### Важные заголовки для CORS

При работе с CORS важны следующие заголовки:

#### Request Headers
```
Origin: https://example.com
```

#### Response Headers
```
Access-Control-Allow-Origin: https://example.com
Access-Control-Allow-Methods: GET, POST, PUT
Access-Control-Allow-Headers: Content-Type, Authorization
```

```javascript
// Браузер автоматически добавляет Origin
fetch('https://api.example.com/data');

// Сервер должен вернуть соответствующие CORS заголовки
```

### Заголовки для кеширования

#### Cache-Control — управление кешем

```
Cache-Control: max-age=3600, public
```

```javascript
fetch('/api/data')
  .then(response => {
    const cacheControl = response.headers.get('Cache-Control');
    console.log(cacheControl);  // max-age=3600, public
  });
```

#### ETag — тег версии ресурса

```
ETag: "abc123"
```

**Зачем:** Для проверки, изменился ли ресурс.

```javascript
// Первый запрос
fetch('/api/users')
  .then(response => {
    const etag = response.headers.get('ETag');
    localStorage.setItem('users-etag', etag);
  });

// Второй запрос с условием
fetch('/api/users', {
  headers: {
    'If-None-Match': localStorage.getItem('users-etag')
  }
})
  .then(response => {
    if (response.status === 304) {
      // Использовать кешированные данные
    }
  });
```

### Визуальная аналогия

Заголовки — как **метки на посылке**:

- **Content-Type** — "Осторожно: хрупкое" (как обрабатывать)
- **Authorization** — "Для получателя" (кто может открыть)
- **Cache-Control** — "Хранить в прохладном месте" (как хранить)
- **User-Agent** — "Отправитель: почта России" (кто отправляет)

Сама посылка (body) — это данные, а метки (headers) — это инструкции.

### ⚠️ Частая ошибка

Забывают указать `Content-Type` при отправке JSON:

```javascript
// ❌ Плохо: сервер может не понять формат
fetch('/api/users', {
  method: 'POST',
  body: JSON.stringify({ name: 'Алекс' })
  // Нет Content-Type!
});

// ✅ Хорошо: явно указываем тип данных
fetch('/api/users', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({ name: 'Алекс' })
});
```

**Почему важно:** Сервер может не понять формат данных без заголовка `Content-Type`.

### Ограничения заголовков

- **Имя заголовка:** до 8192 символов
- **Значение заголовка:** до 8192 символов
- **Количество заголовков:** практически без ограничений (но обычно не больше 100)
- **Регистр:** имена заголовков нечувствительны к регистру (`Content-Type` = `content-type`)

### Итоги

- Заголовки — метаданные запроса/ответа
- Request headers отправляются клиентом серверу
- Response headers отправляются сервером клиенту
- Важные заголовки: `Content-Type`, `Authorization`, `Accept`, `Cache-Control`
- Заголовки не содержат сами данные, а описывают их
- Можно создавать кастомные заголовки
- Всегда указывай `Content-Type` при отправке JSON

---

## 3. Body

Тело запроса (body) — это **данные**, которые клиент отправляет серверу в запросе (для POST, PUT, PATCH) или которые сервер отправляет клиенту в ответе.

### Простое определение

Body — это **содержимое** запроса или ответа: сами данные, которые нужно передать, в отличие от заголовков (метаданных).

**Аналогия:** Если заголовки — это "адрес и инструкции на конверте", то body — это "письмо внутри конверта".

### Body в Request

Тело запроса используется для отправки данных на сервер (обычно в POST, PUT, PATCH запросах).

#### Форматы данных в body

##### JSON

```javascript
fetch('/api/users', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    name: 'Алекс',
    email: 'alex@example.com',
    age: 25
  })
});
```

**Структура:**
```
POST /api/users HTTP/1.1
Content-Type: application/json

{
  "name": "Алекс",
  "email": "alex@example.com",
  "age": 25
}
```

##### Form Data

```javascript
const formData = new FormData();
formData.append('name', 'Алекс');
formData.append('email', 'alex@example.com');
formData.append('avatar', fileInput.files[0]);  // Файл

fetch('/api/users', {
  method: 'POST',
  body: formData
  // Content-Type устанавливается автоматически с boundary
});
```

**Структура:**
```
POST /api/users HTTP/1.1
Content-Type: multipart/form-data; boundary=----WebKitFormBoundary

------WebKitFormBoundary
Content-Disposition: form-data; name="name"

Алекс
------WebKitFormBoundary
Content-Disposition: form-data; name="email"

alex@example.com
------WebKitFormBoundary--
```

##### URL-encoded

```javascript
const params = new URLSearchParams();
params.append('name', 'Алекс');
params.append('email', 'alex@example.com');

fetch('/api/users', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/x-www-form-urlencoded'
  },
  body: params.toString()
});
```

**Структура:**
```
POST /api/users HTTP/1.1
Content-Type: application/x-www-form-urlencoded

name=Алекс&email=alex%40example.com
```

##### Plain Text

```javascript
fetch('/api/logs', {
  method: 'POST',
  headers: {
    'Content-Type': 'text/plain'
  },
  body: 'Лог сообщение: ошибка на строке 42'
});
```

##### XML

```javascript
const xml = `
<user>
  <name>Алекс</name>
  <email>alex@example.com</email>
</user>
`;

fetch('/api/users', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/xml'
  },
  body: xml
});
```

### Body в Response

Тело ответа содержит данные, которые сервер отправляет клиенту.

#### Чтение body ответа

##### JSON

```javascript
fetch('/api/users')
  .then(response => response.json())
  .then(data => {
    console.log(data);  // { users: [...] }
  });
```

##### Text

```javascript
fetch('/api/page.html')
  .then(response => response.text())
  .then(html => {
    console.log(html);  // '<html>...</html>'
  });
```

##### Blob (бинарные данные)

```javascript
fetch('/api/image.jpg')
  .then(response => response.blob())
  .then(blob => {
    const url = URL.createObjectURL(blob);
    img.src = url;
  });
```

##### ArrayBuffer

```javascript
fetch('/api/file.pdf')
  .then(response => response.arrayBuffer())
  .then(buffer => {
    // Работа с бинарными данными
    console.log(buffer.byteLength);
  });
```

##### FormData

```javascript
fetch('/api/form')
  .then(response => response.formData())
  .then(formData => {
    console.log(formData.get('name'));
  });
```

### Важно: body можно прочитать только один раз

```javascript
fetch('/api/users')
  .then(response => {
    // ✅ Первое чтение
    response.json().then(data => console.log(data));
    
    // ❌ Второе чтение — ошибка!
    response.json().then(data => console.log(data));  // TypeError: Already read
  });
```

**Решение:** Клонировать response или сохранить данные:

```javascript
// Вариант 1: Клонировать
fetch('/api/users')
  .then(response => {
    const clone1 = response.clone();
    const clone2 = response.clone();
    
    clone1.json().then(data => console.log('Clone 1:', data));
    clone2.text().then(text => console.log('Clone 2:', text));
  });

// Вариант 2: Сохранить данные
fetch('/api/users')
  .then(response => response.json())
  .then(data => {
    console.log(data);
    // Использовать data дальше
    processData(data);
  });
```

### Когда body нужен, а когда нет?

#### Body нужен

- ✅ **POST** — создание ресурса (обычно с данными)
- ✅ **PUT** — замена ресурса (с новыми данными)
- ✅ **PATCH** — частичное обновление (с данными для обновления)

#### Body не нужен (или опционален)

- ❌ **GET** — получение данных (body не используется, данные в URL)
- ❌ **DELETE** — удаление (обычно без body, ID в URL)
- ⚠️ **HEAD** — только заголовки (body отсутствует)

### Размер body

#### Ограничения

- **Теоретически:** без ограничений
- **Практически:** ограничения браузера и сервера
  - Браузер: обычно несколько МБ
  - Сервер: настраивается (обычно несколько МБ или десятки МБ)

#### Для больших файлов

Используй `FormData` с файлами:

```javascript
const formData = new FormData();
formData.append('file', largeFile);

fetch('/api/upload', {
  method: 'POST',
  body: formData
  // Нет заголовка Content-Length — браузер установит автоматически
});
```

### Визуальная аналогия

Body — как **содержимое посылки**:

- **Заголовки** — адрес, инструкции, метки на коробке
- **Body** — то, что лежит внутри коробки (данные)

**Примеры:**
- **JSON body** — документ в коробке (структурированные данные)
- **FormData body** — несколько предметов в коробке (файлы + текст)
- **Text body** — письмо в конверте (простой текст)

### ⚠️ Частая ошибка

Забывают преобразовать объект в JSON:

```javascript
// ❌ Плохо: отправляется [object Object]
fetch('/api/users', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json'
  },
  body: { name: 'Алекс' }  // Ошибка! Нужна строка
});

// ✅ Хорошо: преобразовали в JSON-строку
fetch('/api/users', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({ name: 'Алекс' })
});
```

**Почему:** Body должен быть строкой (или FormData, Blob, и т.д.), а не объектом JavaScript.

### Разница между body и query params

#### Query params (в URL)

```javascript
// Данные в URL
fetch('/api/users?page=1&limit=10');
```

**Используется для:**
- Параметров запроса (фильтры, пагинация)
- GET запросов

#### Body (в теле запроса)

```javascript
// Данные в body
fetch('/api/users', {
  method: 'POST',
  body: JSON.stringify({ name: 'Алекс' })
});
```

**Используется для:**
- Данных для создания/обновления ресурса
- POST, PUT, PATCH запросов

### Итоги

- Body — данные в запросе или ответе
- Body в request используется для POST, PUT, PATCH
- Body в response содержит данные от сервера
- Форматы: JSON, FormData, URL-encoded, text, XML
- Body можно прочитать только один раз (нужно клонировать для повторного чтения)
- GET и DELETE обычно не используют body
- Важно: JSON.stringify() для объектов в body
- Body отличается от query params (body — в теле, query — в URL)

---

## 4. Query params

Query параметры (query parameters или query string) — это **данные, передаваемые в URL** после знака вопроса (`?`), обычно используются для фильтрации, пагинации и других параметров запроса.

### Простое определение

Query params — это "настройки запроса" в URL: параметры, которые идут после `?` и разделяются `&`.

**Аналогия:** Query params — как "дополнительные вопросы" в запросе: "Дай мне список пользователей, но только первую страницу и по 10 штук".

### Структура query params

```
https://api.example.com/users?page=1&limit=10&sort=name
                              ^    ^  ^     ^  ^    ^
                              |    |  |     |  |    |
                            начало |  |     |  |    значение
                                ключ  |     |  ключ
                                   значение  |
                                           разделитель
```

**Формат:**
```
?key1=value1&key2=value2&key3=value3
```

**Компоненты:**
- `?` — начало query string
- `key=value` — пара ключ-значение
- `&` — разделитель между параметрами

### Примеры

#### Простые параметры

```javascript
// Один параметр
fetch('https://api.example.com/users?page=1');

// Несколько параметров
fetch('https://api.example.com/users?page=1&limit=10');

// Параметры с пробелами (автоматически кодируются)
fetch('https://api.example.com/users?search=john doe');
// Браузер преобразует в: search=john%20doe
```

#### Кодирование значений

Специальные символы кодируются (URL encoding):

```javascript
// Пробел → %20
'john doe' → 'john%20doe'

// & → %26
'bread & butter' → 'bread%20%26%20butter'

// = → %3D
'key=value' → 'key%3Dvalue'

// Русские символы → UTF-8 кодировка
'Алекс' → '%D0%90%D0%BB%D0%B5%D0%BA%D1%81'
```

**JavaScript автоматически кодирует:**

```javascript
// Браузер автоматически кодирует
const url = new URL('https://api.example.com/search');
url.searchParams.set('q', 'john doe');
fetch(url);  // q=john%20doe
```

### Работа с query params в JavaScript

#### Создание query params

##### Вариант 1: URLSearchParams (рекомендуется)

```javascript
const params = new URLSearchParams();
params.append('page', '1');
params.append('limit', '10');
params.append('sort', 'name');

const url = `https://api.example.com/users?${params.toString()}`;
// https://api.example.com/users?page=1&limit=10&sort=name

fetch(url);
```

##### Вариант 2: Объект в URL

```javascript
const url = new URL('https://api.example.com/users');
url.searchParams.set('page', '1');
url.searchParams.set('limit', '10');
url.searchParams.set('sort', 'name');

fetch(url);
```

##### Вариант 3: Ручное формирование (не рекомендуется)

```javascript
const page = 1;
const limit = 10;
const url = `https://api.example.com/users?page=${page}&limit=${limit}`;

fetch(url);
```

#### Чтение query params

##### Из URL страницы

```javascript
// URL: https://example.com/users?page=1&limit=10

const urlParams = new URLSearchParams(window.location.search);
const page = urlParams.get('page');      // '1'
const limit = urlParams.get('limit');    // '10'
const sort = urlParams.get('sort');      // null (если нет)

// Проверка наличия
if (urlParams.has('page')) {
  console.log('Есть параметр page');
}

// Все параметры
urlParams.forEach((value, key) => {
  console.log(`${key}: ${value}`);
});
```

##### Из URL объекта

```javascript
const url = new URL('https://api.example.com/users?page=1&limit=10');
const page = url.searchParams.get('page');    // '1'
const limit = url.searchParams.get('limit');  // '10'
```

### Типичные случаи использования

#### 1. Пагинация

```javascript
function fetchUsers(page = 1, limit = 10) {
  const params = new URLSearchParams({
    page: page.toString(),
    limit: limit.toString()
  });
  
  return fetch(`https://api.example.com/users?${params}`);
}

fetchUsers(2, 20);  // ?page=2&limit=20
```

#### 2. Поиск и фильтрация

```javascript
function searchUsers(query, filters) {
  const params = new URLSearchParams({
    q: query,
    status: filters.status,
    role: filters.role
  });
  
  return fetch(`https://api.example.com/users?${params}`);
}

searchUsers('john', { status: 'active', role: 'admin' });
// ?q=john&status=active&role=admin
```

#### 3. Сортировка

```javascript
function fetchUsersSorted(sortBy, order = 'asc') {
  const params = new URLSearchParams({
    sort: sortBy,
    order: order
  });
  
  return fetch(`https://api.example.com/users?${params}`);
}

fetchUsersSorted('name', 'desc');  // ?sort=name&order=desc
```

#### 4. Комбинация параметров

```javascript
function fetchFilteredUsers(options) {
  const params = new URLSearchParams();
  
  if (options.page) params.set('page', options.page);
  if (options.limit) params.set('limit', options.limit);
  if (options.search) params.set('q', options.search);
  if (options.status) params.set('status', options.status);
  if (options.sort) params.set('sort', options.sort);
  
  return fetch(`https://api.example.com/users?${params}`);
}

fetchFilteredUsers({
  page: 1,
  limit: 20,
  search: 'john',
  status: 'active',
  sort: 'name'
});
// ?page=1&limit=20&q=john&status=active&sort=name
```

### Массивы в query params

#### Вариант 1: Повторяющиеся ключи

```javascript
// ?tags=js&tags=react&tags=vue
const params = new URLSearchParams();
params.append('tags', 'js');
params.append('tags', 'react');
params.append('tags', 'vue');

fetch(`https://api.example.com/posts?${params}`);
```

**На сервере:**
```javascript
// Express.js
app.get('/posts', (req, res) => {
  const tags = req.query.tags;  // ['js', 'react', 'vue'] или 'js,react,vue'
});
```

#### Вариант 2: Через запятую

```javascript
// ?tags=js,react,vue
const params = new URLSearchParams({
  tags: 'js,react,vue'
});

fetch(`https://api.example.com/posts?${params}`);
```

**На сервере:**
```javascript
const tags = req.query.tags.split(',');  // ['js', 'react', 'vue']
```

### Ограничения query params

- **Длина URL:** ограничена браузером (обычно 2048 символов)
- **Видимость:** видны в URL (не использовать для секретных данных)
- **Кеширование:** URL с query params кешируется
- **История:** сохраняется в истории браузера

### Query params vs Body

| Характеристика | Query Params | Body |
|---------------|--------------|------|
| **Где** | В URL после `?` | В теле запроса |
| **Видимость** | ✅ Видны в URL | ❌ Не видны в URL |
| **Использование** | GET запросы, параметры | POST, PUT, PATCH, данные |
| **Размер** | Ограничен (2048 символов) | Больше (несколько МБ) |
| **Кеширование** | ✅ Кешируется | ❌ Не кешируется |
| **Примеры** | Фильтры, пагинация | Данные для создания ресурса |

### Визуальная аналогия

Query params — как **дополнительные вопросы в запросе**:

```
"Дай мне список пользователей"
+ "но только первую страницу" → ?page=1
+ "по 10 штук" → &limit=10
+ "отсортированных по имени" → &sort=name
```

**Итоговый запрос:**
```
Дай мне список пользователей?page=1&limit=10&sort=name
```

### ⚠️ Частая ошибка

Используют query params для секретных данных:

```javascript
// ❌ Плохо: пароль в URL (виден везде!)
fetch(`/api/login?username=alex&password=secret123`);
// URL виден в истории браузера, логах сервера, рефералах

// ✅ Хорошо: секретные данные в body
fetch('/api/login', {
  method: 'POST',
  body: JSON.stringify({
    username: 'alex',
    password: 'secret123'  // Не виден в URL
  })
});
```

**Почему плохо:**
- URL логируется на сервере
- URL виден в истории браузера
- URL передаётся в рефералах
- URL виден в инструментах разработчика

### Итоги

- Query params — параметры в URL после `?`
- Формат: `?key1=value1&key2=value2`
- Используются для фильтров, пагинации, сортировки
- Кодируются автоматически (URL encoding)
- Работа через `URLSearchParams` (рекомендуется)
- Видны в URL (не использовать для секретов)
- Ограничены по длине (2048 символов)
- Используются в GET запросах
- Отличаются от body (query — в URL, body — в теле)

---

## 5. Path params

Path параметры (path parameters или route parameters) — это **части URL-пути**, которые используются для идентификации конкретного ресурса, в отличие от query params, которые идут после знака вопроса.

### Простое определение

Path params — это "переменные" в пути URL, которые используются для указания конкретного ресурса.

**Аналогия:** Если query params — это "вопросы" (фильтры), то path params — это "имя ресурса" в адресе.

### Структура path params

```
https://api.example.com/users/123
                              ^^^
                              |
                          Path param (ID пользователя)
```

**Формат:**
```
/resource/:id
/resource/:id/subresource/:subId
```

**Примеры:**
- `/users/123` — пользователь с ID 123
- `/posts/456/comments/789` — комментарий 789 к посту 456
- `/products/laptop-2024` — продукт "laptop-2024"

### Path params vs Query params

#### Path params (в пути)

```
https://api.example.com/users/123
                              ^^^
                         Path param
```

**Используются для:**
- Идентификации конкретного ресурса
- Обязательных параметров
- Части структуры URL

#### Query params (после `?`)

```
https://api.example.com/users?page=1&limit=10
                              ^    ^  ^     ^
                         Query params
```

**Используются для:**
- Фильтрации, пагинации
- Опциональных параметров
- Настроек запроса

### Примеры использования

#### Базовые примеры

##### Один path param

```javascript
// GET /users/123
fetch('https://api.example.com/users/123')
  .then(response => response.json())
  .then(user => {
    console.log(user);  // { id: 123, name: 'Алекс' }
  });
```

**На сервере (Express.js):**
```javascript
app.get('/users/:id', (req, res) => {
  const userId = req.params.id;  // '123'
  // Найти пользователя по ID
});
```

##### Несколько path params

```javascript
// GET /users/123/posts/456
fetch('https://api.example.com/users/123/posts/456')
  .then(response => response.json())
  .then(post => {
    console.log(post);  // Пост 456 пользователя 123
  });
```

**На сервере:**
```javascript
app.get('/users/:userId/posts/:postId', (req, res) => {
  const userId = req.params.userId;    // '123'
  const postId = req.params.postId;    // '456'
});
```

#### Типичные REST паттерны

##### Получить ресурс

```javascript
// GET /users/123
function getUser(userId) {
  return fetch(`https://api.example.com/users/${userId}`);
}

getUser(123);
```

##### Обновить ресурс

```javascript
// PUT /users/123
function updateUser(userId, data) {
  return fetch(`https://api.example.com/users/${userId}`, {
    method: 'PUT',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(data)
  });
}

updateUser(123, { name: 'Алексей' });
```

##### Удалить ресурс

```javascript
// DELETE /users/123
function deleteUser(userId) {
  return fetch(`https://api.example.com/users/${userId}`, {
    method: 'DELETE'
  });
}

deleteUser(123);
```

##### Вложенные ресурсы

```javascript
// GET /users/123/posts
function getUserPosts(userId) {
  return fetch(`https://api.example.com/users/${userId}/posts`);
}

// GET /users/123/posts/456
function getUserPost(userId, postId) {
  return fetch(`https://api.example.com/users/${userId}/posts/${postId}`);
}

getUserPosts(123);
getUserPost(123, 456);
```

### Комбинация path params и query params

Path params и query params можно использовать вместе:

```javascript
// GET /users/123/posts?page=1&limit=10
function getUserPostsPaginated(userId, page = 1, limit = 10) {
  const params = new URLSearchParams({
    page: page.toString(),
    limit: limit.toString()
  });
  
  return fetch(`https://api.example.com/users/${userId}/posts?${params}`);
}

getUserPostsPaginated(123, 2, 20);
// /users/123/posts?page=2&limit=20
```

**Структура:**
- **Path params** (`123`) — идентификация ресурса (пользователь)
- **Query params** (`page=2&limit=20`) — настройки запроса (пагинация)

### Строковые значения в path params

Path params могут быть не только числами:

```javascript
// GET /products/laptop-2024
fetch('https://api.example.com/products/laptop-2024')
  .then(response => response.json())
  .then(product => {
    console.log(product);
  });
```

**На сервере:**
```javascript
app.get('/products/:slug', (req, res) => {
  const slug = req.params.slug;  // 'laptop-2024'
  // Найти продукт по slug
});
```

### Кодирование path params

Специальные символы в path params должны быть закодированы:

```javascript
// ✅ Хорошо: кодирование выполняется автоматически
const productName = 'laptop & mouse';
const encoded = encodeURIComponent(productName);  // 'laptop%20%26%20mouse'
fetch(`https://api.example.com/products/${encoded}`);
```

**Важно:** Используй `encodeURIComponent()` для каждого сегмента пути:

```javascript
// ❌ Плохо: небезопасно
const userId = '123/../admin';
fetch(`https://api.example.com/users/${userId}`);  // Проблема безопасности!

// ✅ Хорошо: кодирование
const userId = '123';
fetch(`https://api.example.com/users/${encodeURIComponent(userId)}`);
```

### Визуальная аналогия

Path params — как **адрес здания**:

```
/users/123/posts/456
```

**Аналогия:**
- `/users` — улица
- `123` — номер дома (path param: ID пользователя)
- `/posts` — подъезд
- `456` — номер квартиры (path param: ID поста)

**Query params** — как "дополнительные инструкции":
```
/users/123/posts?sort=date&limit=10
                ^    ^  ^     ^
           path param  query params
```

### Сравнительная таблица

| Характеристика | Path Params | Query Params |
|---------------|-------------|--------------|
| **Расположение** | В пути URL | После `?` в URL |
| **Пример** | `/users/123` | `/users?id=123` |
| **Использование** | Идентификация ресурса | Фильтры, настройки |
| **Обязательность** | ✅ Обычно обязательные | ❌ Опциональные |
| **Структура URL** | Часть пути | Параметры запроса |
| **RESTful** | ✅ Стандарт REST | ⚠️ Дополнительные параметры |

### Когда использовать что?

#### Используй Path Params для:

- ✅ Идентификации конкретного ресурса (`/users/123`)
- ✅ Вложенных ресурсов (`/users/123/posts/456`)
- ✅ Обязательных параметров
- ✅ Части структуры URL

#### Используй Query Params для:

- ✅ Фильтрации (`/users?status=active`)
- ✅ Пагинации (`/users?page=1&limit=10`)
- ✅ Сортировки (`/users?sort=name&order=asc`)
- ✅ Опциональных параметров

### ⚠️ Частая ошибка

Путают path params и query params:

```javascript
// ❌ Неправильно: ID в query params (не RESTful)
fetch('/users?id=123');  // Можно, но не стандарт

// ✅ Правильно: ID в path params (RESTful)
fetch('/users/123');

// ❌ Неправильно: фильтры в path params
fetch('/users/active');  // Непонятно, это ID или статус?

// ✅ Правильно: фильтры в query params
fetch('/users?status=active');
```

### Безопасность path params

⚠️ **Важно:** Всегда валидируй path params на сервере:

```javascript
// ❌ Опасность: SQL injection через path param
app.get('/users/:id', (req, res) => {
  const query = `SELECT * FROM users WHERE id = ${req.params.id}`;
  // Опасность, если id = "1; DROP TABLE users;"
});

// ✅ Безопасно: валидация и параметризованные запросы
app.get('/users/:id', (req, res) => {
  const userId = parseInt(req.params.id);
  if (isNaN(userId)) {
    return res.status(400).json({ error: 'Invalid ID' });
  }
  // Использовать параметризованные запросы
});
```

### Итоги

- Path params — части пути URL для идентификации ресурса
- Формат: `/resource/:id` или `/users/:userId/posts/:postId`
- Используются для обязательных параметров, идентификации ресурсов
- Отличаются от query params (path — в пути, query — после `?`)
- Можно комбинировать с query params
- Нужно кодировать специальные символы (`encodeURIComponent`)
- Важно валидировать на сервере для безопасности
- Path params — стандарт RESTful API
- Query params — для фильтров, пагинации, сортировки
