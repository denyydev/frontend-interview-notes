# Хранение данных в браузере

Механизмы хранения данных в браузере: Cookies, LocalStorage, SessionStorage.  
Материал представлен в формате **вопрос–ответ**.

---

## Содержание (Table of Contents)

1. [Cookies](#1-cookies)
2. [LocalStorage](#2-localstorage)
3. [SessionStorage](#3-sessionstorage)
4. [Cookies vs LocalStorage](#4-cookies-vs-localstorage)

---

## 1. Cookies

Cookies (куки) — это **небольшие текстовые файлы**, которые веб-сервер отправляет браузеру и которые браузер хранит на компьютере пользователя. Cookies используются для сохранения состояния между запросами и для идентификации пользователя.

### Простое определение

Cookies — это **"память браузера"**, которая позволяет сайту запомнить информацию о вас (например, что вы залогинены, какие товары в корзине) даже после закрытия страницы.

**Аналогия:** Cookies — как **бейджик в библиотеке**:
- Вам дают бейджик (cookie) с вашим именем и номером
- Каждый раз, когда вы приходите, вы показываете бейджик
- Библиотекарь (сервер) узнаёт вас и может предложить книги по вашим интересам
- Бейджик может быть временным (до конца дня) или постоянным (на месяц)

### Что такое Cookies

**Cookies** — это строки в формате `ключ=значение`, которые хранятся в браузере и автоматически отправляются на сервер с каждым HTTP-запросом.

**Структура cookie:**

```
name=value; expires=Wed, 21 Oct 2025 07:28:00 GMT; path=/; domain=.example.com; secure; httponly; samesite=strict
```

**Компоненты:**
- `name=value` — имя и значение
- `expires` — срок действия
- `path` — путь, для которого действует cookie
- `domain` — домен, для которого действует cookie
- `secure` — отправлять только по HTTPS
- `httponly` — недоступен через JavaScript
- `samesite` — защита от CSRF-атак

### Как работают Cookies

**Процесс:**

1. **Сервер отправляет cookie** в HTTP-ответе
2. **Браузер сохраняет cookie** на компьютере пользователя
3. **Браузер автоматически отправляет cookie** с каждым запросом к этому домену
4. **Сервер читает cookie** и использует информацию

**Пример:**

```http
# Сервер отправляет cookie
HTTP/1.1 200 OK
Set-Cookie: sessionId=abc123; Path=/; HttpOnly
Set-Cookie: userId=42; Path=/; Max-Age=3600

# Браузер автоматически отправляет cookie в следующем запросе
GET /api/profile HTTP/1.1
Host: example.com
Cookie: sessionId=abc123; userId=42
```

### Установка Cookies

#### На сервере (HTTP-заголовок)

```http
# Простой cookie
Set-Cookie: username=alex

# Cookie с параметрами
Set-Cookie: sessionId=abc123; Path=/; HttpOnly; Secure; SameSite=Strict; Max-Age=3600
```

**Параметры:**
- `Path=/` — действует для всех путей сайта
- `Domain=.example.com` — действует для поддоменов
- `Max-Age=3600` — срок действия в секундах (1 час)
- `Expires=Wed, 21 Oct 2025 07:28:00 GMT` — конкретная дата истечения
- `HttpOnly` — недоступен через JavaScript (защита от XSS)
- `Secure` — отправляется только по HTTPS
- `SameSite=Strict` — защита от CSRF-атак

#### В JavaScript (document.cookie)

```javascript
// Установка cookie
document.cookie = "username=alex; path=/; max-age=3600";

// Установка нескольких cookies
document.cookie = "username=alex; path=/; max-age=3600";
document.cookie = "theme=dark; path=/; max-age=86400";

// Установка cookie с expires
const expires = new Date();
expires.setTime(expires.getTime() + 3600 * 1000); // +1 час
document.cookie = `username=alex; path=/; expires=${expires.toUTCString()}`;
```

**Функция для установки cookie:**

```javascript
function setCookie(name, value, days) {
  const expires = new Date();
  expires.setTime(expires.getTime() + days * 24 * 60 * 60 * 1000);
  document.cookie = `${name}=${value}; path=/; expires=${expires.toUTCString()}`;
}

setCookie('username', 'alex', 7); // Cookie на 7 дней
```

### Чтение Cookies

#### В JavaScript (document.cookie)

```javascript
// Получение всех cookies
const allCookies = document.cookie;
console.log(allCookies);
// "username=alex; theme=dark; sessionId=abc123"

// Функция для получения cookie по имени
function getCookie(name) {
  const nameEQ = name + "=";
  const cookies = document.cookie.split(';');
  
  for (let i = 0; i < cookies.length; i++) {
    let cookie = cookies[i];
    while (cookie.charAt(0) === ' ') {
      cookie = cookie.substring(1, cookie.length);
    }
    if (cookie.indexOf(nameEQ) === 0) {
      return cookie.substring(nameEQ.length, cookie.length);
    }
  }
  return null;
}

const username = getCookie('username');
console.log(username); // "alex"
```

**Или с регулярным выражением:**

```javascript
function getCookie(name) {
  const match = document.cookie.match(new RegExp('(^| )' + name + '=([^;]+)'));
  return match ? match[2] : null;
}
```

#### На сервере

```javascript
// Node.js (Express)
app.get('/api/profile', (req, res) => {
  const sessionId = req.cookies.sessionId;
  console.log(sessionId); // "abc123"
});

// Python (Flask)
from flask import request

@app.route('/api/profile')
def profile():
    session_id = request.cookies.get('sessionId')
    print(session_id)  # "abc123"
```

### Удаление Cookies

```javascript
// Удаление cookie (установка с прошедшей датой)
function deleteCookie(name) {
  document.cookie = `${name}=; path=/; expires=Thu, 01 Jan 1970 00:00:00 GMT`;
}

deleteCookie('username');

// Или установка с пустым значением и Max-Age=0
document.cookie = "username=; path=/; max-age=0";
```

### Ограничения Cookies

**Размер:**
- Максимум **4 КБ** на один cookie
- Максимум **50 cookies** на домен (зависит от браузера)

**Пример:**

```javascript
// ❌ Слишком большой cookie
const largeData = 'x'.repeat(5000); // 5000 символов
document.cookie = `data=${largeData}`; // Ошибка или обрезка
```

**Домен и путь:**
- Cookies привязаны к домену и пути
- Нельзя установить cookie для другого домена (без CORS)

```javascript
// На странице example.com
document.cookie = "test=value; domain=other.com"; // ❌ Не сработает
document.cookie = "test=value; domain=.example.com"; // ✅ Работает
```

### Использование Cookies

**Типичные случаи:**

1. **Авторизация и сессии**
   ```javascript
   // Сервер устанавливает cookie с session ID
   Set-Cookie: sessionId=abc123; HttpOnly; Secure
   
   // Браузер отправляет session ID с каждым запросом
   // Сервер идентифицирует пользователя
   ```

2. **Корзина покупок**
   ```javascript
   // Сохранение товаров в корзине
   document.cookie = "cart=item1,item2,item3; path=/; max-age=86400";
   ```

3. **Настройки пользователя**
   ```javascript
   // Тема оформления, язык, регион
   document.cookie = "theme=dark; path=/; max-age=2592000"; // 30 дней
   document.cookie = "lang=ru; path=/; max-age=2592000";
   ```

4. **Отслеживание и аналитика**
   ```javascript
   // Google Analytics, рекламные сети
   // Уникальный идентификатор пользователя
   ```

### Безопасность Cookies

#### HttpOnly

**Защита от XSS:**

```javascript
// ❌ Без HttpOnly — доступен через JavaScript
document.cookie = "sessionId=abc123"; // Уязвим к XSS

// ✅ С HttpOnly — недоступен через JavaScript
Set-Cookie: sessionId=abc123; HttpOnly
// JavaScript не может прочитать или изменить эту cookie
```

**Важно:** HttpOnly cookies устанавливаются только на сервере, не через `document.cookie`.

#### Secure

**Отправка только по HTTPS:**

```http
Set-Cookie: sessionId=abc123; Secure
```

- Cookie отправляется только при HTTPS-соединении
- Защита от перехвата в незащищённых сетях

#### SameSite

**Защита от CSRF:**

```http
Set-Cookie: sessionId=abc123; SameSite=Strict
Set-Cookie: sessionId=abc123; SameSite=Lax
Set-Cookie: sessionId=abc123; SameSite=None; Secure
```

**Значения:**
- `Strict` — cookie отправляется только с запросов с того же сайта
- `Lax` — cookie отправляется с GET-запросов с других сайтов, но не с POST
- `None` — cookie отправляется всегда (требует `Secure`)

### Визуальная аналогия

**Cookies — как пропуск в офис:**

1. **Получение пропуска** — сервер выдаёт cookie
2. **Хранение пропуска** — браузер сохраняет на компьютере
3. **Показ пропуска** — автоматически отправляется с каждым запросом
4. **Проверка пропуска** — сервер проверяет и идентифицирует пользователя
5. **Срок действия** — пропуск может быть временным или постоянным

### ⚠️ Частая ошибка

Думают, что cookie — это большая база данных:

```javascript
// ❌ Неправильное понимание
// "Cookies могут хранить много данных"
const largeObject = { users: [...1000 записей...] };
document.cookie = `data=${JSON.stringify(largeObject)}`; // Слишком большой!
```

**Реальность:** 
- Cookie ограничены **4 КБ**
- Используйте для небольших данных (идентификаторы, токены, настройки)
- Для больших данных используйте **LocalStorage** или **SessionStorage**

### Итоги

- Cookies — это небольшие текстовые файлы, хранящиеся в браузере
- Автоматически отправляются с каждым HTTP-запросом
- Максимум 4 КБ на один cookie
- Используются для авторизации, сессий, настроек, корзины
- Параметры: `Path`, `Domain`, `Expires`, `Max-Age`, `HttpOnly`, `Secure`, `SameSite`
- HttpOnly защищает от XSS, Secure — от перехвата, SameSite — от CSRF
- Устанавливаются на сервере (Set-Cookie) или в JavaScript (document.cookie)
- Удаляются установкой с прошедшей датой
- Типичная ошибка: попытка хранить большие данные в cookies

---

## 2. LocalStorage

LocalStorage — это **веб-хранилище**, которое позволяет сохранять данные в браузере пользователя **без срока действия**. Данные остаются даже после закрытия браузера и перезагрузки компьютера.

### Простое определение

LocalStorage — это **"шкаф для хранения"** в браузере, куда можно положить данные и они останутся там навсегда (пока их не удалят). В отличие от cookies, данные не отправляются на сервер автоматически.

**Аналогия:** LocalStorage — как **ящик в шкафу дома**:
- Вы кладёте вещи (данные) в ящик
- Вещи остаются там, даже если вы закрыли дверь (закрыли браузер)
- Вещи остаются, даже если вы ушли и вернулись (перезагрузка компьютера)
- Вещи доступны только вам (только для вашего домена)

### Что такое LocalStorage

**LocalStorage** — это часть Web Storage API, которое предоставляет доступ к хранилищу браузера для конкретного домена.

**Особенности:**
- Данные хранятся **постоянно** (без срока действия)
- Данные привязаны к **домену** (каждый домен имеет своё хранилище)
- Доступен только через **JavaScript** (не отправляется на сервер автоматически)
- Больше места, чем у cookies (**5-10 МБ**, зависит от браузера)

### Как работает LocalStorage

**Архитектура:**

```
Браузер
├── example.com
│   └── LocalStorage: { key1: "value1", key2: "value2" }
├── other.com
│   └── LocalStorage: { data: "different data" }
└── localhost:3000
    └── LocalStorage: { theme: "dark" }
```

Каждый домен имеет **своё изолированное хранилище**.

### Работа с LocalStorage

#### Сохранение данных (setItem)

```javascript
// Сохранение строки
localStorage.setItem('username', 'alex');

// Сохранение числа
localStorage.setItem('age', 25);

// Сохранение объекта (нужно сериализовать в JSON)
const user = { name: 'Алекс', age: 25 };
localStorage.setItem('user', JSON.stringify(user));
```

**Короткая запись:**

```javascript
// Можно использовать как объект
localStorage.username = 'alex';
localStorage['theme'] = 'dark';
```

**⚠️ Важно:** `localStorage` хранит только **строки**. Для объектов нужно использовать `JSON.stringify` и `JSON.parse`.

#### Получение данных (getItem)

```javascript
// Получение строки
const username = localStorage.getItem('username');
console.log(username); // "alex"

// Получение объекта
const userStr = localStorage.getItem('user');
const user = JSON.parse(userStr);
console.log(user); // { name: 'Алекс', age: 25 }

// Если ключа нет — возвращается null
const missing = localStorage.getItem('missing');
console.log(missing); // null
```

**Короткая запись:**

```javascript
const username = localStorage.username;
const theme = localStorage['theme'];
```

#### Удаление данных (removeItem)

```javascript
// Удаление одного ключа
localStorage.removeItem('username');

// Удаление через delete
delete localStorage.username;
```

#### Очистка всего хранилища (clear)

```javascript
// Удаление всех данных из LocalStorage текущего домена
localStorage.clear();
```

**⚠️ Важно:** `clear()` удаляет **только данные текущего домена**, не всех сайтов.

#### Получение всех ключей

```javascript
// Получение количества элементов
const length = localStorage.length;
console.log(length); // 3

// Получение ключа по индексу
const key = localStorage.key(0);
console.log(key); // "username"

// Получение всех ключей
for (let i = 0; i < localStorage.length; i++) {
  const key = localStorage.key(i);
  const value = localStorage.getItem(key);
  console.log(`${key}: ${value}`);
}
```

**Или через Object.keys:**

```javascript
const keys = Object.keys(localStorage);
keys.forEach(key => {
  console.log(`${key}: ${localStorage.getItem(key)}`);
});
```

### Примеры использования

#### 1. Сохранение настроек пользователя

```javascript
// Сохранение темы оформления
function saveTheme(theme) {
  localStorage.setItem('theme', theme);
}

function getTheme() {
  return localStorage.getItem('theme') || 'light'; // По умолчанию 'light'
}

// Использование
saveTheme('dark');
const currentTheme = getTheme();
document.body.className = `theme-${currentTheme}`;
```

#### 2. Сохранение данных формы

```javascript
// Автосохранение формы
const form = document.getElementById('myForm');

// Сохранение при изменении
form.addEventListener('input', (e) => {
  const formData = new FormData(form);
  const data = Object.fromEntries(formData);
  localStorage.setItem('formData', JSON.stringify(data));
});

// Восстановление при загрузке
window.addEventListener('load', () => {
  const savedData = localStorage.getItem('formData');
  if (savedData) {
    const data = JSON.parse(savedData);
    Object.keys(data).forEach(key => {
      const input = form.querySelector(`[name="${key}"]`);
      if (input) input.value = data[key];
    });
  }
});
```

#### 3. Кеширование данных API

```javascript
// Кеширование данных с проверкой актуальности
function getCachedData(key, ttl = 3600000) { // ttl в миллисекундах
  const cached = localStorage.getItem(key);
  if (!cached) return null;
  
  const { data, timestamp } = JSON.parse(cached);
  const now = Date.now();
  
  if (now - timestamp > ttl) {
    localStorage.removeItem(key);
    return null; // Кеш устарел
  }
  
  return data;
}

function setCachedData(key, data) {
  const cached = {
    data,
    timestamp: Date.now()
  };
  localStorage.setItem(key, JSON.stringify(cached));
}

// Использование
async function fetchUsers() {
  // Проверяем кеш
  const cached = getCachedData('users', 3600000); // 1 час
  if (cached) return cached;
  
  // Загружаем данные
  const response = await fetch('/api/users');
  const users = await response.json();
  
  // Сохраняем в кеш
  setCachedData('users', users);
  
  return users;
}
```

#### 4. Корзина покупок

```javascript
// Сохранение корзины
function addToCart(product) {
  const cart = getCart();
  cart.push(product);
  localStorage.setItem('cart', JSON.stringify(cart));
}

function getCart() {
  const cartStr = localStorage.getItem('cart');
  return cartStr ? JSON.parse(cartStr) : [];
}

function removeFromCart(productId) {
  const cart = getCart();
  const filtered = cart.filter(p => p.id !== productId);
  localStorage.setItem('cart', JSON.stringify(filtered));
}

function clearCart() {
  localStorage.removeItem('cart');
}
```

### Ограничения LocalStorage

**Размер:**
- **5-10 МБ** (зависит от браузера)
- Больше, чем cookies (4 КБ), но всё равно ограничено

**Проверка размера:**

```javascript
function getLocalStorageSize() {
  let total = 0;
  for (let key in localStorage) {
    if (localStorage.hasOwnProperty(key)) {
      total += localStorage[key].length + key.length;
    }
  }
  return total; // Размер в байтах
}

const size = getLocalStorageSize();
console.log(`LocalStorage size: ${(size / 1024).toFixed(2)} KB`);
```

**Обработка переполнения:**

```javascript
function setItemSafe(key, value) {
  try {
    localStorage.setItem(key, value);
    return true;
  } catch (e) {
    if (e.name === 'QuotaExceededError') {
      console.error('LocalStorage переполнен!');
      // Очистка старых данных или предупреждение пользователю
      return false;
    }
    throw e;
  }
}
```

**Домен:**
- Данные изолированы по домену
- `example.com` и `www.example.com` — **разные** хранилища

**Синхронность:**
- LocalStorage **блокирует** выполнение кода при записи/чтении
- Для больших данных может замедлять работу приложения

### Безопасность LocalStorage

**Важные моменты:**

1. **Доступен через JavaScript**
   - Любой скрипт на странице может читать/изменять данные
   - Уязвим к XSS-атакам

2. **Не отправляется на сервер автоматически**
   - Данные остаются в браузере
   - Нельзя использовать для авторизации (как session cookies)

3. **Не используйте для чувствительных данных**
   ```javascript
   // ❌ Плохо — пароль в LocalStorage
   localStorage.setItem('password', 'secret123');
   
   // ❌ Плохо — токены без HttpOnly (лучше cookies с HttpOnly)
   localStorage.setItem('authToken', 'jwt-token');
   ```

4. **Используйте для нечувствительных данных**
   ```javascript
   // ✅ Хорошо — настройки UI
   localStorage.setItem('theme', 'dark');
   localStorage.setItem('language', 'ru');
   
   // ✅ Хорошо — кеш данных
   localStorage.setItem('cachedUsers', JSON.stringify(users));
   ```

### События LocalStorage

**Storage Event** срабатывает при изменении LocalStorage на **других вкладках** того же домена:

```javascript
// Слушаем изменения на других вкладках
window.addEventListener('storage', (e) => {
  console.log('Изменён ключ:', e.key);
  console.log('Старое значение:', e.oldValue);
  console.log('Новое значение:', e.newValue);
  console.log('URL страницы:', e.url);
  
  // Синхронизация состояния
  if (e.key === 'theme') {
    document.body.className = `theme-${e.newValue}`;
  }
});
```

**⚠️ Важно:** Событие `storage` **не срабатывает** на той же вкладке, где произошло изменение.

**Пример синхронизации:**

```javascript
// Вкладка 1
localStorage.setItem('theme', 'dark');

// Вкладка 2 получит событие storage
// Вкладка 1 НЕ получит событие
```

### Визуальная аналогия

**LocalStorage — как ящик в шкафу:**

1. **Каждый домен** — отдельный шкаф
2. **Данные хранятся постоянно** — даже после закрытия браузера
3. **Доступен только через JavaScript** — как ключ от ящика
4. **Ограниченный размер** — как размер ящика (5-10 МБ)
5. **Изоляция** — каждый домен не видит данные других доменов

### ⚠️ Частая ошибка

Попытка сохранить объект напрямую:

```javascript
// ❌ Неправильно
const user = { name: 'Алекс', age: 25 };
localStorage.setItem('user', user);
console.log(localStorage.getItem('user')); // "[object Object]"

// ✅ Правильно
localStorage.setItem('user', JSON.stringify(user));
const saved = JSON.parse(localStorage.getItem('user'));
```

**Реальность:** LocalStorage хранит только строки. Для объектов используйте `JSON.stringify` и `JSON.parse`.

### Итоги

- LocalStorage — веб-хранилище для постоянного хранения данных
- Данные хранятся без срока действия (до явного удаления)
- Размер: 5-10 МБ (больше, чем cookies)
- Данные привязаны к домену (изолированы)
- Доступен только через JavaScript (не отправляется на сервер автоматически)
- Хранит только строки (для объектов нужен JSON.stringify/parse)
- Методы: `setItem()`, `getItem()`, `removeItem()`, `clear()`
- Используется для настроек, кеширования, форм, корзины
- Не используйте для чувствительных данных (пароли, токены)
- Событие `storage` уведомляет другие вкладки об изменениях
- Типичная ошибка: попытка сохранить объект без JSON.stringify

---

## 3. SessionStorage

SessionStorage — это **веб-хранилище**, которое позволяет сохранять данные в браузере **на время сессии**. Данные удаляются автоматически при закрытии вкладки или браузера.

### Простое определение

SessionStorage — это **"временная память"** браузера, которая работает только пока открыта вкладка. Как только вы закрываете вкладку, данные исчезают.

**Аналогия:** SessionStorage — как **доска для заметок на рабочем месте**:
- Вы делаете заметки во время работы (открыта вкладка)
- Заметки доступны, пока вы на рабочем месте
- Когда вы уходите и закрываете рабочее место (закрываете вкладку), заметки выбрасываются
- Каждое рабочее место (вкладка) имеет свою доску

### Что такое SessionStorage

**SessionStorage** — это часть Web Storage API, похожая на LocalStorage, но с **ограниченным сроком жизни**.

**Особенности:**
- Данные хранятся **только на время сессии** (пока открыта вкладка)
- Данные удаляются при **закрытии вкладки**
- Данные изолированы **по вкладкам** (каждая вкладка имеет своё хранилище)
- Та же API, что у LocalStorage (`setItem`, `getItem`, `removeItem`, `clear`)
- Размер: **5-10 МБ** (как у LocalStorage)

### Разница SessionStorage и LocalStorage

| Характеристика | LocalStorage | SessionStorage |
|---------------|--------------|----------------|
| **Срок действия** | Постоянно (до явного удаления) | Только на время сессии |
| **Закрытие вкладки** | Данные остаются | Данные удаляются |
| **Закрытие браузера** | Данные остаются | Данные удаляются |
| **Изоляция** | По домену | По вкладке (tab) |
| **Размер** | 5-10 МБ | 5-10 МБ |
| **API** | `setItem`, `getItem`, и т.д. | `setItem`, `getItem`, и т.д. |

### Работа с SessionStorage

#### Сохранение данных (setItem)

```javascript
// Сохранение строки
sessionStorage.setItem('username', 'alex');

// Сохранение объекта
const user = { name: 'Алекс', age: 25 };
sessionStorage.setItem('user', JSON.stringify(user));

// Короткая запись
sessionStorage.theme = 'dark';
sessionStorage['language'] = 'ru';
```

#### Получение данных (getItem)

```javascript
// Получение строки
const username = sessionStorage.getItem('username');
console.log(username); // "alex"

// Получение объекта
const userStr = sessionStorage.getItem('user');
const user = JSON.parse(userStr);
console.log(user); // { name: 'Алекс', age: 25 }

// Если ключа нет — возвращается null
const missing = sessionStorage.getItem('missing');
console.log(missing); // null
```

#### Удаление данных (removeItem)

```javascript
// Удаление одного ключа
sessionStorage.removeItem('username');

// Удаление через delete
delete sessionStorage.theme;
```

#### Очистка всего хранилища (clear)

```javascript
// Удаление всех данных из SessionStorage текущей вкладки
sessionStorage.clear();
```

#### Получение всех ключей

```javascript
// Получение количества элементов
const length = sessionStorage.length;

// Получение всех ключей
for (let i = 0; i < sessionStorage.length; i++) {
  const key = sessionStorage.key(i);
  const value = sessionStorage.getItem(key);
  console.log(`${key}: ${value}`);
}
```

### Примеры использования

#### 1. Временное сохранение данных формы

```javascript
// Автосохранение формы только на время сессии
const form = document.getElementById('myForm');

// Сохранение при изменении
form.addEventListener('input', (e) => {
  const formData = new FormData(form);
  const data = Object.fromEntries(formData);
  sessionStorage.setItem('formData', JSON.stringify(data));
});

// Восстановление при загрузке (только если вкладка всё ещё открыта)
window.addEventListener('load', () => {
  const savedData = sessionStorage.getItem('formData');
  if (savedData) {
    const data = JSON.parse(savedData);
    Object.keys(data).forEach(key => {
      const input = form.querySelector(`[name="${key}"]`);
      if (input) input.value = data[key];
    });
  }
});
```

#### 2. Сохранение состояния многошаговой формы

```javascript
// Сохранение текущего шага и данных формы
class MultiStepForm {
  constructor() {
    this.currentStep = parseInt(sessionStorage.getItem('currentStep') || '1');
    this.formData = JSON.parse(sessionStorage.getItem('formData') || '{}');
  }
  
  saveStep(step, data) {
    this.currentStep = step;
    this.formData = { ...this.formData, ...data };
    
    sessionStorage.setItem('currentStep', step.toString());
    sessionStorage.setItem('formData', JSON.stringify(this.formData));
  }
  
  getStep() {
    return this.currentStep;
  }
  
  getData() {
    return this.formData;
  }
  
  reset() {
    sessionStorage.removeItem('currentStep');
    sessionStorage.removeItem('formData');
    this.currentStep = 1;
    this.formData = {};
  }
}

// Использование
const form = new MultiStepForm();
form.saveStep(2, { name: 'Алекс', email: 'alex@example.com' });
```

#### 3. Временное кеширование данных

```javascript
// Кеширование данных только на время сессии
const cache = new Map();

async function fetchUsers() {
  // Проверяем кеш в SessionStorage
  const cached = sessionStorage.getItem('users');
  if (cached) {
    return JSON.parse(cached);
  }
  
  // Загружаем данные
  const response = await fetch('/api/users');
  const users = await response.json();
  
  // Сохраняем в кеш (только на время сессии)
  sessionStorage.setItem('users', JSON.stringify(users));
  
  return users;
}
```

#### 4. Отслеживание действий пользователя в сессии

```javascript
// Логирование действий пользователя в сессии
function trackAction(action, data) {
  const logs = JSON.parse(sessionStorage.getItem('userLogs') || '[]');
  logs.push({
    action,
    data,
    timestamp: Date.now()
  });
  sessionStorage.setItem('userLogs', JSON.stringify(logs));
}

// Использование
trackAction('page_view', { page: '/home' });
trackAction('button_click', { button: 'submit' });

// Отправка логов при закрытии вкладки
window.addEventListener('beforeunload', () => {
  const logs = sessionStorage.getItem('userLogs');
  if (logs) {
    // Отправка на сервер
    navigator.sendBeacon('/api/logs', logs);
  }
});
```

#### 5. Временное хранение токенов (не рекомендуется для продакшена)

```javascript
// ⚠️ ВНИМАНИЕ: Это пример, в продакшене используйте HttpOnly cookies
function saveAuthToken(token) {
  sessionStorage.setItem('authToken', token);
}

function getAuthToken() {
  return sessionStorage.getItem('authToken');
}

function removeAuthToken() {
  sessionStorage.removeItem('authToken');
}

// Использование
saveAuthToken('jwt-token-123');
const token = getAuthToken();
```

**⚠️ Важно:** В продакшене для токенов лучше использовать **HttpOnly cookies** для защиты от XSS.

### Изоляция по вкладкам

**Важная особенность:** SessionStorage изолирован **по вкладкам**:

```javascript
// Вкладка 1
sessionStorage.setItem('data', 'value1');
console.log(sessionStorage.getItem('data')); // "value1"

// Вкладка 2 (тот же домен)
console.log(sessionStorage.getItem('data')); // null (другая вкладка)
sessionStorage.setItem('data', 'value2'); // Своё значение

// Вкладка 1
console.log(sessionStorage.getItem('data')); // "value1" (не изменилось)
```

**Визуализация:**

```
Браузер
├── Вкладка 1 (example.com)
│   └── SessionStorage: { data: "value1" }
├── Вкладка 2 (example.com)
│   └── SessionStorage: { data: "value2" }
└── Вкладка 3 (other.com)
    └── SessionStorage: { data: "value3" }
```

Каждая вкладка имеет **своё изолированное хранилище**.

### События SessionStorage

**Storage Event** срабатывает при изменении SessionStorage на **других вкладках** того же домена:

```javascript
// Слушаем изменения на других вкладках
window.addEventListener('storage', (e) => {
  if (e.storageArea === sessionStorage) {
    console.log('Изменён ключ:', e.key);
    console.log('Старое значение:', e.oldValue);
    console.log('Новое значение:', e.newValue);
  }
});
```

**⚠️ Важно:** Событие `storage` **не срабатывает** на той же вкладке, где произошло изменение.

### Когда использовать SessionStorage

**Используйте SessionStorage для:**

1. **Временных данных формы**
   - Автосохранение формы на время сессии
   - Многошаговые формы

2. **Временного кеширования**
   - Кеш данных API на время сессии
   - Улучшение производительности

3. **Состояния UI**
   - Сохранение состояния интерфейса
   - Восстановление после обновления страницы (но не после закрытия)

4. **Аналитики сессии**
   - Отслеживание действий пользователя в сессии
   - Логи для отладки

**Не используйте SessionStorage для:**

1. **Чувствительных данных**
   - Пароли, токены (используйте HttpOnly cookies)
   - Личные данные

2. **Постоянных данных**
   - Настройки пользователя (используйте LocalStorage)
   - Данные, которые должны сохраняться

### Визуальная аналогия

**SessionStorage — как доска для заметок:**

1. **Каждая вкладка** — отдельная доска
2. **Заметки доступны**, пока открыта вкладка
3. **При закрытии вкладки** — доска выбрасывается
4. **Заметки не синхронизируются** между досками (вкладками)
5. **При обновлении страницы** — доска остаётся (данные сохраняются)

### ⚠️ Частая ошибка

Думают, что SessionStorage общий для всех вкладок:

```javascript
// ❌ Неправильное понимание
// "SessionStorage общий для всех вкладок одного домена"

// Вкладка 1
sessionStorage.setItem('theme', 'dark');

// Вкладка 2 ожидает увидеть 'dark'
const theme = sessionStorage.getItem('theme'); // null (другая вкладка!)
```

**Реальность:** 
- SessionStorage **изолирован по вкладкам**
- Каждая вкладка имеет своё хранилище
- Для общих данных используйте **LocalStorage** или события `storage`

### Итоги

- SessionStorage — веб-хранилище для временного хранения данных
- Данные хранятся только на время сессии (пока открыта вкладка)
- Данные удаляются при закрытии вкладки
- Изолирован по вкладкам (каждая вкладка имеет своё хранилище)
- Размер: 5-10 МБ (как у LocalStorage)
- Та же API, что у LocalStorage (`setItem`, `getItem`, `removeItem`, `clear`)
- Используется для временных данных, форм, кеширования, аналитики сессии
- Событие `storage` уведомляет другие вкладки об изменениях
- Не используйте для чувствительных данных (пароли, токены)
- Типичная ошибка: думать, что SessionStorage общий для всех вкладок

---

## 4. Cookies vs LocalStorage

Cookies и LocalStorage — это разные механизмы хранения данных в браузере, каждый со своими особенностями и областями применения.

### Сравнительная таблица

| Характеристика | Cookies | LocalStorage |
|---------------|---------|--------------|
| **Размер** | 4 КБ (на cookie) | 5-10 МБ (общий) |
| **Срок действия** | Задаётся вручную (`expires`, `max-age`) | Постоянно (до явного удаления) |
| **Отправка на сервер** | ✅ Автоматически с каждым HTTP-запросом | ❌ Не отправляется автоматически |
| **Доступ на сервере** | ✅ Доступен (HTTP-заголовок `Cookie`) | ❌ Доступен только в JavaScript |
| **HttpOnly** | ✅ Поддерживается (защита от XSS) | ❌ Не поддерживается |
| **Домен** | Может быть настроен (`domain`) | Привязан к домену |
| **Путь** | Может быть настроен (`path`) | Весь домен |
| **Безопасность** | Выше (HttpOnly, Secure, SameSite) | Ниже (доступен через JavaScript) |
| **API** | `document.cookie` (строка) | `localStorage.setItem/getItem` (объект) |
| **Синхронность** | Синхронный | Синхронный (блокирует) |
| **Использование** | Авторизация, сессии, небольшие данные | Настройки, кеш, большие данные |

### Когда использовать Cookies

**Используйте Cookies для:**

1. **Авторизации и сессий**
   ```javascript
   // ✅ Идеально для сессий
   Set-Cookie: sessionId=abc123; HttpOnly; Secure; SameSite=Strict
   ```

2. **Данных, нужных на сервере**
   ```javascript
   // ✅ Cookie отправляется автоматически
   // Сервер может прочитать и использовать
   Cookie: userId=42; theme=dark
   ```

3. **Небольших данных**
   ```javascript
   // ✅ Небольшие идентификаторы, флаги
   document.cookie = "lang=ru; max-age=2592000";
   ```

**Пример:**

```javascript
// Авторизация через cookie
// Сервер устанавливает HttpOnly cookie
Set-Cookie: authToken=jwt123; HttpOnly; Secure

// Браузер автоматически отправляет cookie с каждым запросом
GET /api/profile HTTP/1.1
Cookie: authToken=jwt123

// Сервер проверяет токен и идентифицирует пользователя
```

### Когда использовать LocalStorage

**Используйте LocalStorage для:**

1. **Настроек пользователя**
   ```javascript
   // ✅ Тема, язык, размер шрифта
   localStorage.setItem('theme', 'dark');
   localStorage.setItem('language', 'ru');
   ```

2. **Кеширования данных**
   ```javascript
   // ✅ Кеш данных API
   localStorage.setItem('users', JSON.stringify(users));
   ```

3. **Больших данных**
   ```javascript
   // ✅ Корзина покупок, черновики
   localStorage.setItem('cart', JSON.stringify(cart));
   ```

4. **Данных, не нужных на сервере**
   ```javascript
   // ✅ Данные только для frontend
   localStorage.setItem('uiState', JSON.stringify(state));
   ```

**Пример:**

```javascript
// Сохранение настроек UI
localStorage.setItem('theme', 'dark');
localStorage.setItem('sidebarCollapsed', 'true');

// Восстановление при загрузке
const theme = localStorage.getItem('theme');
document.body.className = `theme-${theme}`;
```

### Ключевые различия

#### 1. Размер

**Cookies:**
```javascript
// ❌ Ограничение 4 КБ
const largeData = 'x'.repeat(5000);
document.cookie = `data=${largeData}`; // Ошибка или обрезка
```

**LocalStorage:**
```javascript
// ✅ До 5-10 МБ
const largeData = JSON.stringify({ users: [...1000 записей...] });
localStorage.setItem('data', largeData); // Работает
```

#### 2. Отправка на сервер

**Cookies:**
```http
# Автоматически с каждым запросом
GET /api/users HTTP/1.1
Cookie: sessionId=abc123; userId=42
```

**LocalStorage:**
```javascript
// ❌ Не отправляется автоматически
// Нужно вручную добавлять в запрос
const token = localStorage.getItem('token');
fetch('/api/users', {
  headers: {
    'Authorization': `Bearer ${token}`
  }
});
```

#### 3. Безопасность

**Cookies:**
```javascript
// ✅ HttpOnly защищает от XSS
Set-Cookie: sessionId=abc123; HttpOnly

// ✅ Secure защищает от перехвата
Set-Cookie: sessionId=abc123; Secure

// ✅ SameSite защищает от CSRF
Set-Cookie: sessionId=abc123; SameSite=Strict
```

**LocalStorage:**
```javascript
// ❌ Доступен через JavaScript (уязвим к XSS)
localStorage.setItem('token', 'jwt123');
// Любой скрипт может прочитать:
const token = localStorage.getItem('token'); // Уязвимость!
```

#### 4. API

**Cookies:**
```javascript
// Работа со строками (неудобно)
document.cookie = "username=alex; max-age=3600";
const cookies = document.cookie; // "username=alex; theme=dark"
// Нужна функция для парсинга
```

**LocalStorage:**
```javascript
// Удобный API (объект)
localStorage.setItem('username', 'alex');
const username = localStorage.getItem('username');
localStorage.removeItem('username');
localStorage.clear();
```

### Гибридный подход

**Комбинирование Cookies и LocalStorage:**

```javascript
// Cookies для авторизации (безопасность)
Set-Cookie: sessionId=abc123; HttpOnly; Secure

// LocalStorage для настроек (удобство)
localStorage.setItem('theme', 'dark');
localStorage.setItem('language', 'ru');

// LocalStorage для кеша
localStorage.setItem('cachedUsers', JSON.stringify(users));
```

### Визуальная аналогия

**Cookies vs LocalStorage:**

**Cookies** — как **удостоверение личности**:
- Носит с собой (отправляется автоматически)
- Небольшое (4 КБ)
- Защищённое (HttpOnly, Secure)
- Показываешь при необходимости (на сервере)

**LocalStorage** — как **личный дневник**:
- Остаётся дома (не отправляется автоматически)
- Больше места (5-10 МБ)
- Доступен только вам (JavaScript)
- Для личных записей (настройки, кеш)

### ⚠️ Частая ошибка

Хранение токенов авторизации в LocalStorage:

```javascript
// ❌ Плохо — токен в LocalStorage
localStorage.setItem('authToken', 'jwt-token-123');

// Проблемы:
// 1. Доступен через JavaScript (уязвим к XSS)
// 2. Не отправляется автоматически на сервер
// 3. Нет защиты HttpOnly

// ✅ Хорошо — токен в HttpOnly cookie
Set-Cookie: authToken=jwt-token-123; HttpOnly; Secure; SameSite=Strict

// Преимущества:
// 1. Недоступен через JavaScript (защита от XSS)
// 2. Отправляется автоматически с каждым запросом
// 3. Защита от CSRF (SameSite)
```

**Реальность:** 
- Для **авторизации** используйте **HttpOnly cookies**
- Для **настроек и кеша** используйте **LocalStorage**

### Примеры правильного использования

#### 1. Авторизация (Cookies)

```javascript
// ✅ Сервер устанавливает HttpOnly cookie
// Frontend не имеет доступа к токену через JavaScript
Set-Cookie: sessionId=abc123; HttpOnly; Secure; SameSite=Strict

// Браузер автоматически отправляет cookie
fetch('/api/profile')
  .then(res => res.json())
  .then(data => console.log(data));
```

#### 2. Настройки UI (LocalStorage)

```javascript
// ✅ Сохранение настроек в LocalStorage
const settings = {
  theme: 'dark',
  language: 'ru',
  fontSize: 'medium'
};
localStorage.setItem('settings', JSON.stringify(settings));

// Восстановление при загрузке
const saved = JSON.parse(localStorage.getItem('settings') || '{}');
applySettings(saved);
```

#### 3. Кеширование данных (LocalStorage)

```javascript
// ✅ Кеш данных API
async function getUsers() {
  const cached = localStorage.getItem('users');
  if (cached) {
    const { data, timestamp } = JSON.parse(cached);
    if (Date.now() - timestamp < 3600000) { // 1 час
      return data;
    }
  }
  
  const users = await fetch('/api/users').then(r => r.json());
  localStorage.setItem('users', JSON.stringify({
    data: users,
    timestamp: Date.now()
  }));
  return users;
}
```

### Итоги

- **Cookies** — для авторизации, сессий, небольших данных (4 КБ)
- **LocalStorage** — для настроек, кеша, больших данных (5-10 МБ)
- Cookies отправляются автоматически на сервер, LocalStorage — нет
- Cookies поддерживают HttpOnly/Secure/SameSite, LocalStorage — нет
- Cookies удобны для серверной работы, LocalStorage — для клиентской
- Для токенов авторизации используйте **HttpOnly cookies** (не LocalStorage)
- Для настроек и кеша используйте **LocalStorage** (не cookies)
- Комбинируйте оба подхода для оптимального решения
