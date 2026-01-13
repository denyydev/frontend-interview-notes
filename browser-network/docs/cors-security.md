# CORS и безопасность

Политика безопасности браузера CORS (Cross-Origin Resource Sharing) и связанные вопросы.  
Материал представлен в формате **вопрос–ответ**.

---

## Содержание (Table of Contents)

1. [CORS](#1-cors)
2. [Preflight request](#2-preflight-request)
3. [Почему Postman работает, а браузер — нет](#3-почему-postman-работает-а-браузер--нет)

---

## 1. CORS

CORS (Cross-Origin Resource Sharing) — это **механизм безопасности браузера**, который контролирует, может ли веб-страница делать запросы к другому домену, отличному от того, с которого она была загружена.

### Простое определение

CORS — это **"пропускная система"** браузера, которая проверяет, разрешено ли вашему сайту обращаться к другому серверу. Если сервер не дал разрешения, браузер блокирует запрос.

**Аналогия:** CORS — как охранник на входе в здание:
- Вы (ваш сайт) хотите зайти в другое здание (другой домен)
- Охранник (браузер) спрашивает у администратора здания (сервера): "Можно ли этому человеку войти?"
- Если администратор говорит "да" (правильные CORS-заголовки), охранник пропускает
- Если "нет" или нет ответа, охранник блокирует вход

### Что такое Same-Origin Policy (SOP)

**Same-Origin Policy** — это базовая политика безопасности браузера, которая **блокирует** запросы между разными источниками (origins).

**Origin (источник) состоит из:**
- **Протокол** (http/https)
- **Домен** (example.com)
- **Порт** (80, 443, 3000, и т.д.)

**Примеры:**

```javascript
// Текущая страница: https://example.com:443/page.html

// ✅ Same-Origin (одинаковый источник)
https://example.com:443/api/users        // Протокол, домен, порт совпадают

// ❌ Cross-Origin (разный источник)
http://example.com:80/api/users         // Другой протокол (http vs https)
https://api.example.com:443/users       // Другой домен
https://example.com:3000/api/users      // Другой порт
https://other.com:443/api/users         // Другой домен
```

**Правило:** Если хотя бы один компонент (протокол, домен, порт) отличается — это **Cross-Origin**.

### Зачем нужен CORS

**Проблема без CORS:**

Представьте, что вы зашли на сайт `evil.com`, и он пытается сделать запрос к вашему банку `bank.com`:

```javascript
// На сайте evil.com
fetch('https://bank.com/api/transfer', {
  method: 'POST',
  body: JSON.stringify({
    to: 'hacker-account',
    amount: 10000
  })
});
```

**Без CORS:** Такой запрос был бы возможен, и злоумышленник мог бы украсть ваши деньги.

**С CORS:** Браузер блокирует запрос, потому что `bank.com` не разрешил `evil.com` делать к себе запросы.

### Как работает CORS

CORS работает через **HTTP-заголовки**, которые сервер отправляет в ответе:

#### Основные CORS-заголовки

**1. `Access-Control-Allow-Origin`** — разрешает определённые домены

```http
Access-Control-Allow-Origin: https://example.com
```

**Разрешает только `https://example.com`:**

```javascript
// На странице https://example.com
fetch('https://api.example.com/users')
  .then(res => res.json())
  .then(data => console.log(data)); // ✅ Работает
```

```javascript
// На странице https://other.com
fetch('https://api.example.com/users')
  .then(res => res.json())
  .then(data => console.log(data)); // ❌ CORS error
```

**Разрешить все домены (небезопасно!):**

```http
Access-Control-Allow-Origin: *
```

**⚠️ Важно:** `*` не работает с `credentials: true` (cookies, authorization headers).

**2. `Access-Control-Allow-Methods`** — разрешает HTTP-методы

```http
Access-Control-Allow-Methods: GET, POST, PUT, DELETE
```

**3. `Access-Control-Allow-Headers`** — разрешает заголовки

```http
Access-Control-Allow-Headers: Content-Type, Authorization
```

**4. `Access-Control-Allow-Credentials`** — разрешает отправку cookies и авторизационных заголовков

```http
Access-Control-Allow-Credentials: true
```

**5. `Access-Control-Max-Age`** — время кеширования preflight-запроса

```http
Access-Control-Max-Age: 3600
```

### Пример CORS-запроса

**Frontend (JavaScript):**

```javascript
// Запрос с другого домена
fetch('https://api.example.com/users', {
  method: 'GET',
  headers: {
    'Content-Type': 'application/json',
    'Authorization': 'Bearer token123'
  },
  credentials: 'include' // Отправлять cookies
})
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error('CORS error:', error));
```

**Backend (ответ сервера):**

```http
HTTP/1.1 200 OK
Access-Control-Allow-Origin: https://myapp.com
Access-Control-Allow-Methods: GET, POST, PUT, DELETE
Access-Control-Allow-Headers: Content-Type, Authorization
Access-Control-Allow-Credentials: true

[
  { "id": 1, "name": "Алекс" },
  { "id": 2, "name": "Мария" }
]
```

### Типичная ошибка CORS

**Ошибка в консоли браузера:**

```
Access to fetch at 'https://api.example.com/users' from origin 'https://myapp.com' 
has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is 
present on the requested resource.
```

**Причины:**

1. **Сервер не отправляет CORS-заголовки**
   ```http
   // ❌ Нет заголовка
   HTTP/1.1 200 OK
   Content-Type: application/json
   
   {"data": "..."}
   ```

2. **Неправильный домен в заголовке**
   ```http
   // ❌ Разрешён другой домен
   Access-Control-Allow-Origin: https://other.com
   
   // ✅ Нужно разрешить ваш домен
   Access-Control-Allow-Origin: https://myapp.com
   ```

3. **Запрос с credentials, но сервер не разрешил**
   ```javascript
   // Frontend
   fetch('https://api.example.com/users', {
     credentials: 'include' // Отправляем cookies
   });
   ```
   
   ```http
   // ❌ Backend не разрешил credentials
   Access-Control-Allow-Origin: https://myapp.com
   // Нет Access-Control-Allow-Credentials: true
   ```

### Решение CORS на бэкенде

#### Node.js (Express)

```javascript
const express = require('express');
const app = express();

// Middleware для CORS
app.use((req, res, next) => {
  res.header('Access-Control-Allow-Origin', 'https://myapp.com');
  res.header('Access-Control-Allow-Methods', 'GET, POST, PUT, DELETE');
  res.header('Access-Control-Allow-Headers', 'Content-Type, Authorization');
  res.header('Access-Control-Allow-Credentials', 'true');
  next();
});

// Или использовать библиотеку cors
const cors = require('cors');
app.use(cors({
  origin: 'https://myapp.com',
  credentials: true
}));
```

#### Python (Flask)

```python
from flask import Flask
from flask_cors import CORS

app = Flask(__name__)
CORS(app, origins=['https://myapp.com'], supports_credentials=True)
```

#### PHP

```php
<?php
header('Access-Control-Allow-Origin: https://myapp.com');
header('Access-Control-Allow-Methods: GET, POST, PUT, DELETE');
header('Access-Control-Allow-Headers: Content-Type, Authorization');
header('Access-Control-Allow-Credentials: true');
?>
```

### Простые запросы (Simple Requests)

Некоторые запросы **не требуют preflight** (предварительного запроса):

**Условия простого запроса:**

1. **Метод:** `GET`, `POST`, или `HEAD`
2. **Заголовки:** Только стандартные (Accept, Accept-Language, Content-Language, Content-Type)
3. **Content-Type:** `text/plain`, `multipart/form-data`, или `application/x-www-form-urlencoded`

**Пример простого запроса:**

```javascript
// ✅ Простой запрос (без preflight)
fetch('https://api.example.com/users', {
  method: 'GET'
});
```

**Браузер сразу отправляет запрос**, без предварительной проверки.

### Сложные запросы (Complex Requests)

Запросы, которые **требуют preflight**:

**Условия сложного запроса:**

1. **Метод:** `PUT`, `DELETE`, `PATCH`, или кастомный метод
2. **Заголовки:** Кастомные заголовки (Authorization, X-Custom-Header)
3. **Content-Type:** `application/json`

**Пример сложного запроса:**

```javascript
// ❌ Сложный запрос (требует preflight)
fetch('https://api.example.com/users', {
  method: 'PUT',
  headers: {
    'Content-Type': 'application/json',
    'Authorization': 'Bearer token123'
  },
  body: JSON.stringify({ name: 'Алекс' })
});
```

**Браузер сначала отправляет OPTIONS-запрос** (preflight), затем основной запрос.

### Визуальная аналогия

**CORS — как система пропусков:**

1. **Вы (frontend)** хотите попасть в здание (API)
2. **Охранник (браузер)** проверяет ваш пропуск (Origin)
3. **Администратор (сервер)** проверяет список разрешённых (Access-Control-Allow-Origin)
4. Если вы в списке → пропуск
5. Если нет → блокировка

**Простой запрос** — как обычный вход (проверка на месте)  
**Сложный запрос** — как вход с грузом (нужна предварительная проверка — preflight)

### ⚠️ Частая ошибка

Думают, что CORS можно обойти на frontend:

```javascript
// ❌ НЕ РАБОТАЕТ
fetch('https://api.example.com/users', {
  headers: {
    'Access-Control-Allow-Origin': '*' // Это заголовок для сервера, не клиента!
  }
});
```

**Реальность:** CORS контролируется **только сервером**. Frontend не может обойти CORS, это механизм безопасности браузера.

**Решение:** Настроить CORS на **бэкенде**.

### Итоги

- CORS — механизм безопасности браузера для контроля Cross-Origin запросов
- Same-Origin Policy блокирует запросы между разными источниками
- Origin состоит из протокола, домена и порта
- CORS работает через HTTP-заголовки (`Access-Control-Allow-Origin`, и т.д.)
- Простые запросы (GET, POST) не требуют preflight
- Сложные запросы (PUT, DELETE, кастомные заголовки) требуют preflight
- CORS настраивается на сервере, frontend не может обойти
- Типичная ошибка: сервер не отправляет нужные CORS-заголовки
- `Access-Control-Allow-Origin: *` не работает с `credentials: true`

---

## 2. Preflight request

Preflight request (предварительный запрос) — это **OPTIONS-запрос**, который браузер автоматически отправляет перед сложным Cross-Origin запросом, чтобы проверить, разрешён ли этот запрос сервером.

### Простое определение

Preflight — это **"разрешение на вход"**, которое браузер запрашивает у сервера перед отправкой сложного запроса. Браузер спрашивает: "Можно ли отправить такой запрос?", и только после разрешения отправляет основной запрос.

**Аналогия:** Preflight — как звонок перед визитом:
- Вы хотите прийти с грузом (сложный запрос)
- Сначала звоните и спрашиваете: "Можно ли прийти с грузом?" (OPTIONS-запрос)
- Если разрешают → приходите с грузом (основной запрос)
- Если нет → остаётесь дома (браузер блокирует запрос)

### Когда браузер отправляет preflight

Браузер отправляет preflight для **сложных запросов** (complex requests):

#### 1. Нестандартные HTTP-методы

```javascript
// ❌ Требует preflight
fetch('https://api.example.com/users', {
  method: 'PUT' // или DELETE, PATCH
});
```

#### 2. Кастомные заголовки

```javascript
// ❌ Требует preflight
fetch('https://api.example.com/users', {
  headers: {
    'Authorization': 'Bearer token123', // Кастомный заголовок
    'X-Custom-Header': 'value'
  }
});
```

#### 3. Content-Type: application/json

```javascript
// ❌ Требует preflight
fetch('https://api.example.com/users', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json' // Не простой Content-Type
  },
  body: JSON.stringify({ name: 'Алекс' })
});
```

#### 4. Другие нестандартные заголовки

```javascript
// ❌ Требует preflight
fetch('https://api.example.com/users', {
  headers: {
    'X-Requested-With': 'XMLHttpRequest'
  }
});
```

### Когда preflight НЕ нужен

**Простые запросы** (simple requests) не требуют preflight:

```javascript
// ✅ Простой запрос (без preflight)
fetch('https://api.example.com/users', {
  method: 'GET'
});

// ✅ Простой POST с form-data
fetch('https://api.example.com/users', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/x-www-form-urlencoded'
  },
  body: 'name=Алекс&age=25'
});
```

**Условия простого запроса:**
- Метод: `GET`, `POST`, или `HEAD`
- Только стандартные заголовки
- `Content-Type`: `text/plain`, `multipart/form-data`, или `application/x-www-form-urlencoded`

### Как работает preflight

**Процесс:**

1. **Браузер отправляет OPTIONS-запрос** (preflight)
2. **Сервер отвечает** с разрешениями (CORS-заголовки)
3. **Браузер проверяет** разрешения
4. **Если разрешено** → отправляет основной запрос
5. **Если нет** → блокирует запрос

**Визуализация:**

```
Frontend                Backend
   |                       |
   |--- OPTIONS ---------->|  (1. Preflight запрос)
   |                       |
   |<-- 200 OK ------------|  (2. Ответ с разрешениями)
   |   Access-Control-Allow-Origin: https://myapp.com
   |   Access-Control-Allow-Methods: PUT
   |   Access-Control-Allow-Headers: Content-Type, Authorization
   |                       |
   |--- PUT -------------->|  (3. Основной запрос)
   |   Content-Type: application/json
   |   Authorization: Bearer token
   |   Body: {...}
   |                       |
   |<-- 200 OK ------------|  (4. Ответ на основной запрос)
   |   Data: {...}
```

### Пример preflight-запроса

**Frontend (JavaScript):**

```javascript
// Сложный запрос
fetch('https://api.example.com/users/1', {
  method: 'PUT',
  headers: {
    'Content-Type': 'application/json',
    'Authorization': 'Bearer token123'
  },
  body: JSON.stringify({ name: 'Алекс' })
});
```

**1. Preflight-запрос (OPTIONS):**

```http
OPTIONS /users/1 HTTP/1.1
Host: api.example.com
Origin: https://myapp.com
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: content-type, authorization
```

**Заголовки preflight-запроса:**
- `Origin` — откуда идёт запрос
- `Access-Control-Request-Method` — какой метод будет использован
- `Access-Control-Request-Headers` — какие заголовки будут отправлены

**2. Ответ сервера на preflight:**

```http
HTTP/1.1 200 OK
Access-Control-Allow-Origin: https://myapp.com
Access-Control-Allow-Methods: GET, POST, PUT, DELETE
Access-Control-Allow-Headers: Content-Type, Authorization
Access-Control-Allow-Credentials: true
Access-Control-Max-Age: 3600
```

**Заголовки ответа:**
- `Access-Control-Allow-Origin` — разрешённый домен
- `Access-Control-Allow-Methods` — разрешённые методы
- `Access-Control-Allow-Headers` — разрешённые заголовки
- `Access-Control-Max-Age` — время кеширования preflight (в секундах)

**3. Основной запрос (PUT):**

```http
PUT /users/1 HTTP/1.1
Host: api.example.com
Origin: https://myapp.com
Content-Type: application/json
Authorization: Bearer token123

{"name": "Алекс"}
```

**4. Ответ на основной запрос:**

```http
HTTP/1.1 200 OK
Access-Control-Allow-Origin: https://myapp.com
Content-Type: application/json

{"id": 1, "name": "Алекс"}
```

### Кеширование preflight

**`Access-Control-Max-Age`** — время, на которое браузер кеширует результат preflight:

```http
Access-Control-Max-Age: 3600
```

**Что это значит:**
- Браузер запомнит разрешения на **1 час** (3600 секунд)
- В течение этого времени браузер **не будет отправлять** новые OPTIONS-запросы
- После истечения времени — новый preflight

**Пример:**

```javascript
// Первый запрос
fetch('https://api.example.com/users', {
  method: 'PUT',
  headers: { 'Content-Type': 'application/json' }
});
// → OPTIONS запрос отправлен

// Второй запрос (в течение 1 часа)
fetch('https://api.example.com/users', {
  method: 'PUT',
  headers: { 'Content-Type': 'application/json' }
});
// → OPTIONS запрос НЕ отправлен (используется кеш)
```

### Обработка preflight на бэкенде

#### Node.js (Express)

```javascript
const express = require('express');
const app = express();

// Обработка preflight (OPTIONS)
app.options('*', (req, res) => {
  res.header('Access-Control-Allow-Origin', 'https://myapp.com');
  res.header('Access-Control-Allow-Methods', 'GET, POST, PUT, DELETE');
  res.header('Access-Control-Allow-Headers', 'Content-Type, Authorization');
  res.header('Access-Control-Allow-Credentials', 'true');
  res.header('Access-Control-Max-Age', '3600');
  res.sendStatus(200);
});

// Основные роуты
app.put('/users/:id', (req, res) => {
  res.header('Access-Control-Allow-Origin', 'https://myapp.com');
  res.json({ id: req.params.id, name: req.body.name });
});
```

**Или с библиотекой cors:**

```javascript
const cors = require('cors');

app.use(cors({
  origin: 'https://myapp.com',
  methods: ['GET', 'POST', 'PUT', 'DELETE'],
  allowedHeaders: ['Content-Type', 'Authorization'],
  credentials: true,
  maxAge: 3600
}));
```

#### Python (Flask)

```python
from flask import Flask
from flask_cors import CORS

app = Flask(__name__)
CORS(app, 
     origins=['https://myapp.com'],
     methods=['GET', 'POST', 'PUT', 'DELETE'],
     allow_headers=['Content-Type', 'Authorization'],
     supports_credentials=True,
     max_age=3600)
```

### Типичные ошибки с preflight

#### 1. Сервер не обрабатывает OPTIONS

```javascript
// ❌ Ошибка: сервер не отвечает на OPTIONS
// Браузер отправляет OPTIONS, но сервер возвращает 404 или 405
```

**Решение:** Добавить обработку OPTIONS-запросов на сервере.

#### 2. Неправильные заголовки в ответе

```http
// ❌ Не разрешён метод PUT
Access-Control-Allow-Methods: GET, POST

// ✅ Нужно разрешить PUT
Access-Control-Allow-Methods: GET, POST, PUT, DELETE
```

#### 3. Не разрешён кастомный заголовок

```http
// ❌ Не разрешён заголовок Authorization
Access-Control-Allow-Headers: Content-Type

// ✅ Нужно разрешить Authorization
Access-Control-Allow-Headers: Content-Type, Authorization
```

#### 4. Preflight проходит, но основной запрос блокируется

```http
// Preflight ответ
Access-Control-Allow-Origin: https://myapp.com

// Основной запрос ответ
// ❌ Нет Access-Control-Allow-Origin
HTTP/1.1 200 OK
Content-Type: application/json
```

**Решение:** Добавить CORS-заголовки в **оба** ответа (preflight и основной).

### Визуальная аналогия

**Preflight — как проверка багажа в аэропорту:**

1. **Вы приезжаете** с багажом (сложный запрос)
2. **Охранник проверяет** багаж (OPTIONS-запрос)
3. **Если всё в порядке** → пропускают, и вы идёте дальше (основной запрос)
4. **Если что-то запрещено** → не пропускают (браузер блокирует)

**Кеширование** — как пропуск на несколько дней:
- После первой проверки вам дают пропуск на час
- В течение часа вы можете проходить без повторной проверки

### ⚠️ Частая ошибка

Думают, что preflight можно отключить на frontend:

```javascript
// ❌ НЕ РАБОТАЕТ
fetch('https://api.example.com/users', {
  method: 'PUT',
  headers: {
    'Skip-Preflight': 'true' // Такого заголовка не существует!
  }
});
```

**Реальность:** Preflight — это **автоматическое поведение браузера**. Его нельзя отключить на frontend. Можно только сделать запрос "простым" (убрать кастомные заголовки, использовать GET/POST).

### Итоги

- Preflight — это OPTIONS-запрос перед сложным Cross-Origin запросом
- Браузер автоматически отправляет preflight для сложных запросов
- Сложные запросы: нестандартные методы, кастомные заголовки, `application/json`
- Простые запросы (GET, POST с простыми заголовками) не требуют preflight
- Процесс: OPTIONS → проверка разрешений → основной запрос (если разрешено)
- `Access-Control-Max-Age` кеширует результат preflight
- Сервер должен обрабатывать OPTIONS-запросы
- CORS-заголовки нужны в обоих ответах (preflight и основной)
- Preflight нельзя отключить на frontend, это поведение браузера

---

## 3. Почему Postman работает, а браузер — нет

Postman и другие HTTP-клиенты работают **без ограничений CORS**, потому что они не являются браузерами и не применяют Same-Origin Policy. Браузер же **блокирует** Cross-Origin запросы по соображениям безопасности.

### Простое определение

Postman — это **инструмент для разработчиков**, который отправляет HTTP-запросы напрямую, минуя браузер. Браузер же **защищает пользователей** от вредоносных сайтов, блокируя небезопасные запросы.

**Аналогия:** 
- **Postman** — как прямой звонок в компанию (без посредников)
- **Браузер** — как звонок через секретаря, который проверяет, можно ли вам звонить (CORS-проверка)

### Почему Postman работает

**Postman — это не браузер:**

1. **Нет Same-Origin Policy**
   - Postman не применяет политику безопасности браузера
   - Он отправляет HTTP-запросы напрямую к серверу
   - Нет проверки Origin

2. **Нет CORS-проверки**
   - Postman не проверяет CORS-заголовки
   - Он просто отправляет запрос и получает ответ
   - Сервер может отвечать без CORS-заголовков

3. **Прямое HTTP-соединение**
   - Postman устанавливает TCP-соединение напрямую
   - Нет промежуточного слоя (браузера)
   - Работает как обычный HTTP-клиент

**Пример:**

```http
# Postman отправляет запрос напрямую
PUT https://api.example.com/users/1 HTTP/1.1
Content-Type: application/json
Authorization: Bearer token123

{"name": "Алекс"}
```

**Сервер отвечает:**

```http
HTTP/1.1 200 OK
Content-Type: application/json

{"id": 1, "name": "Алекс"}
```

**✅ Работает!** Postman не проверяет CORS-заголовки.

### Почему браузер блокирует

**Браузер применяет Same-Origin Policy:**

1. **Проверка Origin**
   - Браузер проверяет, откуда идёт запрос
   - Сравнивает Origin запроса с текущим доменом
   - Если разные — блокирует

2. **Проверка CORS-заголовков**
   - Браузер проверяет ответ сервера
   - Ищет `Access-Control-Allow-Origin`
   - Если заголовка нет или домен не разрешён — блокирует

3. **Безопасность пользователей**
   - Защита от вредоносных сайтов
   - Предотвращение CSRF-атак
   - Защита личных данных

**Пример:**

```javascript
// На странице https://myapp.com
fetch('https://api.example.com/users', {
  method: 'PUT',
  headers: {
    'Content-Type': 'application/json',
    'Authorization': 'Bearer token123'
  },
  body: JSON.stringify({ name: 'Алекс' })
});
```

**Браузер отправляет запрос:**

```http
PUT https://api.example.com/users HTTP/1.1
Origin: https://myapp.com
Content-Type: application/json
Authorization: Bearer token123

{"name": "Алекс"}
```

**Сервер отвечает (без CORS-заголовков):**

```http
HTTP/1.1 200 OK
Content-Type: application/json

{"id": 1, "name": "Алекс"}
```

**❌ Браузер блокирует!** Нет заголовка `Access-Control-Allow-Origin`.

**Ошибка в консоли:**

```
Access to fetch at 'https://api.example.com/users' from origin 'https://myapp.com' 
has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is 
present on the requested resource.
```

### Сравнительная таблица

| Характеристика | Postman | Браузер |
|---------------|---------|---------|
| **Same-Origin Policy** | ❌ Нет | ✅ Есть |
| **CORS-проверка** | ❌ Нет | ✅ Есть |
| **Проверка Origin** | ❌ Нет | ✅ Есть |
| **Блокировка запросов** | ❌ Нет | ✅ Есть (если нет CORS) |
| **Безопасность** | ⚠️ Низкая (инструмент разработчика) | ✅ Высокая (защита пользователей) |
| **Preflight-запросы** | ❌ Нет | ✅ Есть (для сложных запросов) |

### Почему это важно для безопасности

**Сценарий атаки без CORS:**

Представьте, что вы зашли на вредоносный сайт `evil.com`:

```javascript
// На сайте evil.com
// Злоумышленник пытается украсть ваши деньги
fetch('https://bank.com/api/transfer', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_TOKEN', // Украденный токен
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    to: 'hacker-account',
    amount: 10000
  })
});
```

**Без CORS:** Такой запрос был бы возможен, и злоумышленник мог бы украсть деньги.

**С CORS:** Браузер блокирует запрос, потому что `bank.com` не разрешил `evil.com` делать к себе запросы.

### Когда Postman полезен

**Postman полезен для:**

1. **Тестирования API**
   - Проверка работы API без CORS
   - Отладка запросов
   - Проверка ответов сервера

2. **Разработки бэкенда**
   - Тестирование до настройки CORS
   - Проверка авторизации
   - Тестирование различных сценариев

3. **Документации API**
   - Создание коллекций запросов
   - Примеры использования
   - Тестирование различных endpoints

### Когда браузер нужен

**Браузер нужен для:**

1. **Реального использования**
   - Пользователи работают через браузер
   - Нужна правильная настройка CORS
   - Проверка реального поведения

2. **Тестирования frontend**
   - Проверка работы в реальных условиях
   - Отладка CORS-ошибок
   - Тестирование с credentials

3. **Безопасности**
   - Защита пользователей
   - Предотвращение атак
   - Контроль доступа

### Решение проблемы

**Если Postman работает, а браузер нет:**

1. **Проверить CORS-заголовки на сервере**
   ```http
   Access-Control-Allow-Origin: https://myapp.com
   Access-Control-Allow-Methods: GET, POST, PUT, DELETE
   Access-Control-Allow-Headers: Content-Type, Authorization
   Access-Control-Allow-Credentials: true
   ```

2. **Проверить обработку OPTIONS (preflight)**
   ```javascript
   // Сервер должен обрабатывать OPTIONS-запросы
   app.options('*', (req, res) => {
     res.header('Access-Control-Allow-Origin', 'https://myapp.com');
     res.header('Access-Control-Allow-Methods', 'GET, POST, PUT, DELETE');
     res.header('Access-Control-Allow-Headers', 'Content-Type, Authorization');
     res.sendStatus(200);
   });
   ```

3. **Проверить Origin в запросе**
   - Убедиться, что Origin в запросе совпадает с разрешённым
   - Проверить протокол (http vs https)
   - Проверить порт (если не стандартный)

### Визуальная аналогия

**Postman vs Браузер:**

**Postman** — как **прямой звонок**:
- Вы звоните напрямую в компанию
- Никто не проверяет, кто вы
- Вы получаете ответ сразу

**Браузер** — как **звонок через секретаря**:
- Вы звоните, но сначала вас проверяет секретарь
- Секретарь спрашивает у компании: "Можно ли этому человеку звонить?"
- Если разрешают → соединяют
- Если нет → блокируют звонок

**CORS** — это "секретарь", который защищает компанию (сервер) от нежелательных звонков.

### ⚠️ Частая ошибка

Думают, что если Postman работает, то проблема в браузере:

```javascript
// ❌ Неправильное понимание
// "Postman работает, значит API правильный, проблема в браузере"
// "Нужно как-то обойти CORS на frontend"
```

**Реальность:** 
- Postman работает, потому что он **не применяет CORS**
- Браузер блокирует, потому что **нет CORS-заголовков на сервере**
- **Решение:** Настроить CORS на **бэкенде**, а не обходить на frontend

### Итоги

- Postman работает без CORS, потому что это не браузер
- Браузер блокирует запросы без CORS-заголовков по соображениям безопасности
- Postman полезен для тестирования API и разработки
- Браузер нужен для реального использования и проверки безопасности
- Если Postman работает, а браузер нет — нужно настроить CORS на сервере
- CORS нельзя обойти на frontend, это механизм безопасности браузера
- Same-Origin Policy защищает пользователей от вредоносных сайтов
- Разница: Postman = прямой HTTP-клиент, Браузер = HTTP-клиент с проверками безопасности
