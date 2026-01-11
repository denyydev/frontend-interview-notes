# React Basics

Базовые концепции React и фундаментальные принципы библиотеки.  
Материал представлен в формате **вопрос–ответ**

1. [Что такое React и какую проблему он решает](#1-что-такое-react-и-какую-проблему-он-решает)
2. [Чем React отличается от обычного JavaScript / jQuery](#2-чем-react-отличается-от-обычного-javascript--jquery)
3. [Что такое SPA](#3-что-такое-spa)
4. [Что такое Virtual DOM](#4-что-такое-virtual-dom)
5. [Как React сравнивает Virtual DOM (reconciliation)](#5-как-react-сравнивает-virtual-dom-reconciliation)
6. [Что такое JSX](#6-что-такое-jsx)
7. [Почему JSX — это не HTML](#7-почему-jsx--это-не-html)
8. [Можно ли писать React без JSX](#8-можно-ли-писать-react-без-jsx)
9. [Что такое элемент React](#9-что-такое-элемент-react)
10. [Чем React element отличается от компонента](#10-чем-react-element-отличается-от-компонента)

## 1. Что такое React и какую проблему он решает

React — это JavaScript-библиотека для создания пользовательских интерфейсов, разработанная компанией Facebook (Meta).

### Какую проблему решает React?

До React разработчики обновляли DOM напрямую, что было сложно и приводило к ошибкам.

#### Проблема 1: Сложность управления DOM

**Без React (обычный JavaScript/jQuery):**

```js
// Нужно вручную находить элементы, создавать, удалять, обновлять
const button = document.getElementById("counter-btn");
const count = document.getElementById("counter");

let counter = 0;

button.addEventListener("click", () => {
  counter++;
  count.textContent = counter; // вручную обновляем DOM
});
```

Когда интерфейс усложняется, код становится неуправляемым.

#### Проблема 2: Синхронизация данных и UI

Когда данные меняются, нужно обновлять много элементов:

```js
// Пользователь изменил имя
user.name = "Новое имя";

// Нужно обновить все места, где отображается имя
document.getElementById("header-name").textContent = user.name;
document.getElementById("profile-name").textContent = user.name;
document.getElementById("sidebar-name").textContent = user.name;
// ... и ещё 10 мест

// Легко забыть обновить какое-то место! ❌
```

#### Проблема 3: Переиспользование кода

Сложно переиспользовать код для похожих элементов:

```js
// Создаём карточку пользователя
function createUserCard(user) {
  const card = document.createElement("div");
  card.innerHTML = `
    <h3>${user.name}</h3>
    <p>${user.email}</p>
  `;
  return card;
}

// Для каждого элемента — своя функция
function createProductCard(product) {
  // почти тот же код, но нужно копировать...
}
```

### Что предлагает React?

React решает эти проблемы через:

1. **Декларативный подход** — описываешь, как должен выглядеть UI, а не как его изменять
2. **Компонентную архитектуру** — разбиваешь интерфейс на переиспользуемые части
3. **Virtual DOM** — React сам эффективно обновляет реальный DOM
4. **Реактивность** — UI автоматически обновляется при изменении данных

### Пример с React

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <button onClick={() => setCount(count + 1)}>Увеличить</button>
      <p>Счёт: {count}</p>
    </div>
  );
}
```

**Преимущества:**

- Описываем **что** хотим (UI с кнопкой и счётчиком)
- React сам решает **как** это отобразить и обновить
- Код проще и понятнее
- Легко переиспользовать компонент

### Аналогия

Представь строительство дома:

- **Без React** — ты сам заказываешь каждый кирпич, следишь за его укладкой, краской, окнами... Это долго и сложно
- **С React** — ты говоришь "мне нужен дом с 3 комнатами", и React сам решает, как его построить и обновить

### Итоги

- React — библиотека для создания UI
- Решает проблему сложного управления DOM
- Делает код проще, переиспользуемым и предсказуемым
- Декларативный подход вместо императивного

---

## 2. Чем React отличается от обычного JavaScript / jQuery

React и обычный JavaScript/jQuery решают одни и те же задачи, но **подходы кардинально разные**.

### Императивный vs Декларативный подход

#### Обычный JavaScript / jQuery — императивный

Ты говоришь **как** делать каждый шаг:

```js
// jQuery пример
const $button = $("#my-button");
const $counter = $("#counter");
let count = 0;

$button.on("click", function () {
  count++;
  $counter.text(count); // явно обновляем текст
  if (count > 10) {
    $button.prop("disabled", true); // явно отключаем кнопку
  }
});
```

Ты **управляешь каждым действием** вручную.

#### React — декларативный

Ты описываешь **что** хочешь получить, React решает как это сделать:

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <button onClick={() => setCount(count + 1)} disabled={count > 10}>
        Увеличить
      </button>
      <p>Счёт: {count}</p>
    </div>
  );
}
```

Ты описываешь **желаемое состояние** (кнопка отключена, если count > 10), React сам обновляет DOM.

### Работа с DOM

#### JavaScript/jQuery — прямая работа с DOM

```js
// Создаём элемент
const div = document.createElement("div");
div.className = "card";
div.innerHTML = "<h2>Заголовок</h2>";

// Добавляем в DOM
document.body.appendChild(div);

// Обновляем
div.innerHTML = "<h2>Новый заголовок</h2>"; // пересоздаём весь HTML

// Удаляем
div.remove();
```

Ты **напрямую меняешь реальный DOM**.

#### React — Virtual DOM

```jsx
// Описываем компонент
function Card({ title }) {
  return (
    <div className="card">
      <h2>{title}</h2>
    </div>
  );
}

// Используем
<Card title="Заголовок" />  // React сам создаст/обновит DOM
<Card title="Новый заголовок" />  // React эффективно обновит только изменившееся
```

React работает с **Virtual DOM** и эффективно обновляет реальный DOM.

### Организация кода

#### JavaScript/jQuery — функции и объекты

```js
// Функции для работы с элементами
function createUserCard(user) {
  const card = document.createElement("div");
  card.innerHTML = `
    <h3>${user.name}</h3>
    <p>${user.email}</p>
  `;
  return card;
}

function updateUserCard(card, user) {
  card.innerHTML = `
    <h3>${user.name}</h3>
    <p>${user.email}</p>
  `;
}

// Вручную управляем жизненным циклом
const card = createUserCard(user);
document.body.appendChild(card);
// позже
updateUserCard(card, newUser);
// ещё позже
card.remove();
```

#### React — компоненты

```jsx
// Компонент — самодостаточная единица
function UserCard({ user }) {
  return (
    <div>
      <h3>{user.name}</h3>
      <p>{user.email}</p>
    </div>
  );
}

// Используем
<UserCard user={user} />; // React управляет жизненным циклом
```

### Управление состоянием

#### JavaScript/jQuery — переменные и события

```js
let user = { name: "Alex", age: 25 };
const $name = $("#user-name");
const $age = $("#user-age");

function updateUI() {
  $name.text(user.name);
  $age.text(user.age);
}

updateUI();

// Меняем данные
user.age = 26;
updateUI(); // нужно вручную обновить UI
```

Нужно **вручную синхронизировать** данные и UI.

#### React — состояние компонентов

```jsx
function UserProfile() {
  const [user, setUser] = useState({ name: "Alex", age: 25 });

  return (
    <div>
      <p>{user.name}</p>
      <p>{user.age}</p>
      <button onClick={() => setUser({ ...user, age: 26 })}>
        Увеличить возраст
      </button>
    </div>
  );
}
```

Изменение состояния **автоматически обновляет** UI.

### Переиспользование кода

#### JavaScript/jQuery

```js
// Нужно копировать и изменять код
function createCard1(data) {
  /* ... */
}
function createCard2(data) {
  /* ... */
} // почти то же самое
function createCard3(data) {
  /* ... */
} // опять копия
```

#### React

```jsx
// Один компонент, разные данные
function Card({ title, content }) {
  return (
    <div>
      <h3>{title}</h3>
      <p>{content}</p>
    </div>
  );
}

<Card title="Заголовок 1" content="Текст 1" />
<Card title="Заголовок 2" content="Текст 2" />
```

### Сравнительная таблица

| Аспект            | JavaScript/jQuery              | React                             |
| ----------------- | ------------------------------ | --------------------------------- |
| Подход            | Императивный (как)             | Декларативный (что)               |
| DOM               | Прямая работа                  | Virtual DOM                       |
| Состояние         | Переменные + ручное обновление | State + автоматическое обновление |
| Код               | Функции и объекты              | Компоненты                        |
| Переиспользование | Копирование кода               | Переиспользование компонентов     |

### Когда использовать что?

**JavaScript/jQuery:**

- Простые страницы с минимальной интерактивностью
- Нужен полный контроль над DOM
- Легковесные проекты

**React:**

- Сложные интерактивные приложения
- Нужна структура и переиспользуемость
- Командная разработка

### Итоги

- JavaScript/jQuery — императивный подход, прямая работа с DOM
- React — декларативный подход, работа через Virtual DOM
- React автоматизирует обновления UI
- React лучше для сложных приложений

---

## 3. Что такое SPA

SPA (Single Page Application) — это веб-приложение, которое загружается один раз, а затем обновляет контент **без перезагрузки страницы**.

### Как работают обычные сайты (MPA)

**MPA (Multi Page Application)** — классический подход:

```
Пользователь кликает ссылку
    ↓
Браузер отправляет запрос на сервер
    ↓
Сервер отправляет новую HTML-страницу
    ↓
Браузер перезагружает всю страницу
    ↓
Новое содержимое отображается
```

Каждая страница — это **отдельный HTML-файл** на сервере.

**Пример:**

```
/ → index.html
/about → about.html
/contact → contact.html
```

### Как работает SPA

**SPA (Single Page Application)** — современный подход:

```
Приложение загружается один раз (index.html)
    ↓
JavaScript управляет отображением контента
    ↓
При клике на ссылку JavaScript меняет контент
    ↓
Без перезагрузки страницы!
```

Есть **один HTML-файл**, а JavaScript меняет его содержимое.

### Визуальная аналогия

**MPA** — как книжный магазин: чтобы посмотреть другую книгу, нужно идти в другой отдел (загружать новую страницу).

**SPA** — как электронная книга: всё на одном экране, переключение между разделами мгновенное, без "перезагрузки".

### Пример SPA на React

```jsx
import { useState } from "react";

function App() {
  const [page, setPage] = useState("home");

  return (
    <div>
      <nav>
        <button onClick={() => setPage("home")}>Главная</button>
        <button onClick={() => setPage("about")}>О нас</button>
        <button onClick={() => setPage("contact")}>Контакты</button>
      </nav>

      {page === "home" && <HomePage />}
      {page === "about" && <AboutPage />}
      {page === "contact" && <ContactPage />}
    </div>
  );
}
```

При клике на кнопку **меняется только контент**, страница не перезагружается.

### Преимущества SPA

1. **Быстрая навигация** — нет перезагрузки страницы
2. **Плавный UX** — переходы мгновенные
3. **Экономия трафика** — загружаются только данные, а не весь HTML
4. **Близко к нативным приложениям** — работает как мобильное приложение

### Недостатки SPA

1. **Первая загрузка долгая** — нужно загрузить весь JavaScript
2. **SEO сложнее** — поисковым роботам сложнее индексировать
3. **Требуется JavaScript** — без JS приложение не работает

### React Router — маршрутизация в SPA

React сам по себе не делает SPA, но с React Router это просто:

```jsx
import { BrowserRouter, Routes, Route, Link } from "react-router-dom";

function App() {
  return (
    <BrowserRouter>
      <nav>
        <Link to="/">Главная</Link>
        <Link to="/about">О нас</Link>
      </nav>

      <Routes>
        <Route path="/" element={<HomePage />} />
        <Route path="/about" element={<AboutPage />} />
      </Routes>
    </BrowserRouter>
  );
}
```

### Как работает навигация в SPA

**Без перезагрузки страницы:**

```jsx
// Пользователь кликает на ссылку
<Link to="/about">О нас</Link>;

// React Router меняет URL (через History API)
window.history.pushState({}, "", "/about");

// React Router рендерит нужный компонент
<AboutPage />;
```

URL меняется, но страница **не перезагружается**.

### Итоги

- SPA — приложение с одной HTML-страницей
- Контент обновляется через JavaScript без перезагрузки
- Быстрая навигация и плавный UX
- React отлично подходит для создания SPA

---

## 4. Что такое Virtual DOM

Virtual DOM (виртуальный DOM) — это **JavaScript-представление реального DOM**, которое React использует для оптимизации обновлений.

### Что такое реальный DOM?

DOM (Document Object Model) — это структура HTML-документа, которую браузер создаёт при загрузке страницы.

```html
<div id="app">
  <h1>Привет</h1>
  <p>Мир</p>
</div>
```

Браузер создаёт **дерево объектов**, с которым можно работать через JavaScript:

```js
const element = document.getElementById("app");
element.innerHTML = "<h1>Новый заголовок</h1>";
```

### Проблема с реальным DOM

**Изменение DOM — медленная операция:**

```js
// Каждое изменение DOM вызывает перерисовку браузера
element.innerHTML = "<h1>Заголовок 1</h1>"; // перерисовка
element.className = "new-class"; // ещё перерисовка
element.style.color = "red"; // ещё перерисовка
```

Если изменений много, страница может "тормозить".

### Что такое Virtual DOM?

Virtual DOM — это **объект JavaScript**, который описывает структуру DOM.

**Реальный DOM:**

```html
<div class="card">
  <h2>Заголовок</h2>
  <p>Текст</p>
</div>
```

**Virtual DOM (упрощённо):**

```js
{
  type: 'div',
  props: {
    className: 'card',
    children: [
      {
        type: 'h2',
        props: {
          children: 'Заголовок'
        }
      },
      {
        type: 'p',
        props: {
          children: 'Текст'
        }
      }
    ]
  }
}
```

### Как работает Virtual DOM в React

1. **Создание Virtual DOM** — React создаёт JavaScript-объект, описывающий UI
2. **Изменение Virtual DOM** — при изменении состояния создаётся новый Virtual DOM
3. **Сравнение (Diffing)** — React сравнивает старый и новый Virtual DOM
4. **Обновление реального DOM (Reconciliation)** — React обновляет только изменённые элементы

### Визуализация процесса

```
Состояние изменилось
    ↓
Создаётся новый Virtual DOM
    ↓
React сравнивает старый и новый Virtual DOM (Diffing)
    ↓
React находит только изменённые элементы
    ↓
React обновляет только эти элементы в реальном DOM
```

### Пример

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <button onClick={() => setCount(count + 1)}>+</button>
      <p>Счёт: {count}</p>
    </div>
  );
}
```

**Что происходит:**

1. Первый рендер:

   - React создаёт Virtual DOM: `{ type: 'div', children: [button, p: 'Счёт: 0'] }`
   - React создаёт реальный DOM

2. Клик на кнопку:
   - `count` меняется с 0 на 1
   - React создаёт новый Virtual DOM: `{ type: 'div', children: [button, p: 'Счёт: 1'] }`
   - React сравнивает: изменился только текст в `<p>`
   - React обновляет **только текст** в реальном DOM

### Почему Virtual DOM быстрый?

**Без Virtual DOM (прямое изменение DOM):**

```js
// Нужно вручную найти и изменить каждый элемент
const p = document.querySelector("p");
p.textContent = "Счёт: 1"; // перерисовка

const button = document.querySelector("button");
button.disabled = true; // ещё перерисовка
```

**С Virtual DOM:**

React **батчит** (группирует) все изменения и применяет их **одной операцией**.

### Аналогия

**Реальный DOM** — как дом: чтобы поменять картину на стене, нужно прийти, снять старую, повесить новую.

**Virtual DOM** — как план дома: ты сначала меняешь план (быстро), потом разом применяешь все изменения к дому.

### Важно понимать

Virtual DOM — это **не быстрее**, чем прямое изменение DOM. Virtual DOM даёт:

1. **Предсказуемость** — React сам решает, что обновлять
2. **Батчинг** — изменения группируются
3. **Удобство разработки** — пишешь декларативный код

### Итоги

- Virtual DOM — JavaScript-представление реального DOM
- React использует Virtual DOM для оптимизации обновлений
- React сравнивает старый и новый Virtual DOM и обновляет только изменённое
- Виртуальный DOM делает код проще, а обновления предсказуемыми

---

## 5. Как React сравнивает Virtual DOM (reconciliation)

Reconciliation (согласование) — это процесс, когда React **сравнивает новый Virtual DOM со старым** и решает, какие изменения применить к реальному DOM.

### Что такое Reconciliation?

Когда состояние компонента меняется, React создаёт **новый Virtual DOM**. Затем он сравнивает его со **старым Virtual DOM** и находит различия. Этот процесс и называется reconciliation.

### Алгоритм сравнения (Diffing Algorithm)

React использует эвристический алгоритм (основанный на предположениях) для быстрого сравнения.

#### Правило 1: Сравнение по типу элемента

Если тип элемента **изменился**, React удаляет старое дерево и создаёт новое:

```jsx
// Было
<div>
  <Counter />
</div>

// Стало
<span>
  <Counter />
</span>
```

React **удалит** `<div>` и всё внутри, затем **создаст** `<span>` с `<Counter />`.

**Почему?** Потому что разные типы элементов (div и span) обычно имеют разную структуру.

#### Правило 2: Сравнение по ключу (key)

Если элементы одного типа, React смотрит на **ключи (keys)**:

```jsx
// Список без key (или с index)
<ul>
  <li>Элемент 1</li>
  <li>Элемент 2</li>
</ul>

// Добавили элемент в начало
<ul>
  <li>Элемент 0</li>  {/* React думает: это старый "Элемент 1"? */}
  <li>Элемент 1</li>  {/* React думает: это старый "Элемент 2"? */}
  <li>Элемент 2</li>  {/* Новый элемент? */}
</ul>
```

Без key React может **неправильно сопоставить** элементы.

**С key:**

```jsx
<ul>
  <li key="1">Элемент 1</li>
  <li key="2">Элемент 2</li>
</ul>

// Добавили элемент в начало
<ul>
  <li key="0">Элемент 0</li>  {/* Новый элемент, создаём */}
  <li key="1">Элемент 1</li>  {/* Старый key=1, обновляем */}
  <li key="2">Элемент 2</li>  {/* Старый key=2, обновляем */}
</ul>
```

С key React **правильно сопоставляет** элементы.

#### Правило 3: Рекурсивное сравнение дочерних элементов

React **рекурсивно** сравнивает дочерние элементы:

```jsx
// Было
<div>
  <h1>Заголовок</h1>
  <p>Текст</p>
</div>

// Стало (изменился только текст в p)
<div>
  <h1>Заголовок</h1>
  <p>Новый текст</p>
</div>
```

React:

1. Сравнивает `<div>` — тип одинаковый, продолжаем
2. Сравнивает `<h1>` — тип и содержимое одинаковые, пропускаем
3. Сравнивает `<p>` — содержимое изменилось, обновляем только текст

### Пример процесса

```jsx
function TodoList({ todos }) {
  return (
    <ul>
      {todos.map((todo) => (
        <TodoItem key={todo.id} todo={todo} />
      ))}
    </ul>
  );
}
```

**Изменение списка:**

1. **Старый Virtual DOM:**

   ```js
   [
     {
       type: TodoItem,
       key: 1,
       props: { todo: { id: 1, text: "Купить хлеб" } },
     },
     { type: TodoItem, key: 2, props: { todo: { id: 2, text: "Позвонить" } } },
   ];
   ```

2. **Новый Virtual DOM (добавили элемент в начало):**

   ```js
   [
     {
       type: TodoItem,
       key: 3,
       props: { todo: { id: 3, text: "Новая задача" } },
     },
     {
       type: TodoItem,
       key: 1,
       props: { todo: { id: 1, text: "Купить хлеб" } },
     },
     { type: TodoItem, key: 2, props: { todo: { id: 2, text: "Позвонить" } } },
   ];
   ```

3. **React сравнивает:**

   - key=3 — новый элемент, создаём
   - key=1 — такой же key, проверяем props (не изменились), пропускаем
   - key=2 — такой же key, проверяем props (не изменились), пропускаем

4. **Результат:** React добавит только один новый элемент в реальный DOM.

### Почему это эффективно?

React использует **эвристику** (упрощённые правила), а не точное сравнение, потому что:

1. **Точное сравнение** всех элементов было бы слишком медленным
2. **Эвристика** работает в большинстве случаев
3. **Key** помогает React правильно сопоставлять элементы

### ⚠️ Частая ошибка

Использование **index** как key:

```jsx
// ❌ Плохо
{
  todos.map((todo, index) => <TodoItem key={index} todo={todo} />);
}

// ✅ Хорошо
{
  todos.map((todo) => <TodoItem key={todo.id} todo={todo} />);
}
```

**Почему плохо?** Если порядок элементов меняется, index не соответствует элементу, и React неправильно сопоставит элементы.

### Итоги

- Reconciliation — процесс сравнения старого и нового Virtual DOM
- React сравнивает по типу элемента, ключам и рекурсивно по дочерним элементам
- Key помогает React правильно сопоставлять элементы
- Используй уникальные key (не index!)

---

## 6. Что такое JSX

JSX (JavaScript XML) — это **синтаксическое расширение JavaScript**, которое позволяет писать HTML-подобный код в JavaScript.

### Как выглядит JSX?

```jsx
const element = <h1>Привет, мир!</h1>;
```

Это **не HTML** и **не строка** — это специальный синтаксис JavaScript.

### Зачем нужен JSX?

**Без JSX** (с React.createElement):

```js
const element = React.createElement("h1", null, "Привет, мир!");
```

**С JSX:**

```jsx
const element = <h1>Привет, мир!</h1>;
```

JSX делает код **читабельнее и понятнее**.

### JSX — это выражение

JSX можно использовать как обычное JavaScript-выражение:

```jsx
function greet(name) {
  if (name) {
    return <h1>Привет, {name}!</h1>;
  }
  return <h1>Привет, незнакомец!</h1>;
}

const element = greet("Алекс");
```

JSX можно присваивать переменным, возвращать из функций, использовать в условиях.

### Встраивание выражений

В JSX можно встраивать **любые JavaScript-выражения** через фигурные скобки `{}`:

```jsx
const name = "Алекс";
const element = <h1>Привет, {name}!</h1>; // Привет, Алекс!

const count = 5;
const message = <p>У тебя {count} сообщений</p>;

const sum = <p>2 + 2 = {2 + 2}</p>; // 2 + 2 = 4

function formatName(user) {
  return user.firstName + " " + user.lastName;
}

const user = { firstName: "Алекс", lastName: "Иванов" };
const element = <h1>Привет, {formatName(user)}!</h1>;
```

### JSX после компиляции

JSX **компилируется** в вызовы `React.createElement()`:

**До компиляции (JSX):**

```jsx
const element = (
  <div className="card">
    <h1>Заголовок</h1>
    <p>Текст</p>
  </div>
);
```

**После компиляции (JavaScript):**

```js
const element = React.createElement(
  "div",
  { className: "card" },
  React.createElement("h1", null, "Заголовок"),
  React.createElement("p", null, "Текст")
);
```

Браузер видит **только JavaScript**, JSX — это просто удобный синтаксис для разработчика.

### Атрибуты в JSX

#### className вместо class

В JSX используется `className` вместо `class` (потому что `class` — зарезервированное слово в JavaScript):

```jsx
// ✅ JSX
<div className="container">

// ❌ Неправильно
<div class="container">
```

#### camelCase для событий

```jsx
// ✅ JSX
<button onClick={handleClick}>Кнопка</button>

// ❌ HTML
<button onclick="handleClick()">Кнопка</button>
```

#### Самозакрывающиеся теги

```jsx
// ✅ JSX
<img src="image.jpg" alt="Описание" />
<br />
<input type="text" />

// ❌ HTML (не работает в JSX)
<img src="image.jpg" alt="Описание">
<br>
```

### Вложенность JSX

JSX элементы можно вкладывать друг в друга:

```jsx
const element = (
  <div>
    <h1>Заголовок</h1>
    <ul>
      <li>Пункт 1</li>
      <li>Пункт 2</li>
    </ul>
  </div>
);
```

### JSX предотвращает инъекции

JSX **автоматически экранирует** значения, что предотвращает XSS-атаки:

```jsx
const userInput = '<script>alert("XSS")</script>';

// JSX безопасно отобразит это как текст
const element = <div>{userInput}</div>; // Покажет текст, не выполнит скрипт
```

### Итоги

- JSX — синтаксическое расширение JavaScript для написания HTML-подобного кода
- JSX компилируется в React.createElement()
- Можно встраивать JavaScript-выражения через {}
- Используется className вместо class, camelCase для событий
- JSX предотвращает XSS-атаки

---

## 7. Почему JSX — это не HTML

Хотя JSX выглядит как HTML, это **совершенно другой синтаксис** с важными отличиями.

### JSX — это JavaScript

JSX — это **синтаксический сахар** над JavaScript. Он компилируется в вызовы `React.createElement()`:

```jsx
// JSX
const element = <div className="card">Привет</div>;
```

Компилируется в:

```js
const element = React.createElement("div", { className: "card" }, "Привет");
```

**HTML** — это язык разметки, который браузер понимает напрямую. **JSX** — это JavaScript-код, который компилируется.

### Ключевые отличия

#### 1. className вместо class

```jsx
// ✅ JSX
<div className="container">

// ❌ HTML (не работает в JSX)
<div class="container">
```

**Почему?** `class` — зарезервированное слово в JavaScript, поэтому используется `className`.

#### 2. camelCase для атрибутов

```jsx
// ✅ JSX
<div tabIndex="0">
<input maxLength="10" />
<label htmlFor="email">Email</label>

// ❌ HTML
<div tabindex="0">
<input maxlength="10" />
<label for="email">Email</label>
```

В JSX атрибуты пишутся в **camelCase**, в HTML — в **kebab-case**.

#### 3. Самозакрывающиеся теги обязательны

```jsx
// ✅ JSX
<img src="image.jpg" alt="Описание" />
<br />
<input type="text" />

// ❌ HTML (не работает в JSX)
<img src="image.jpg" alt="Описание">
<br>
<input type="text">
```

В JSX **все теги должны быть закрыты**.

#### 4. JavaScript-выражения в фигурных скобках

```jsx
// ✅ JSX
<div>{count + 1}</div>
<button onClick={handleClick}>Кнопка</button>

// ❌ HTML (не работает)
<div>{count + 1}</div>  <!-- Браузер покажет это как текст -->
<button onclick="handleClick()">Кнопка</button>  <!-- строка, не функция -->
```

В JSX можно встраивать **JavaScript-выражения**, в HTML — только строки.

#### 5. Компоненты с большой буквы

```jsx
// ✅ JSX (компонент)
<MyComponent />

// ❌ JSX (будет искать HTML-тег <mycomponent>)
<myComponent />

// HTML (всё маленькими буквами)
<my-component></my-component>
```

В JSX компоненты пишутся с **большой буквы**, HTML-теги — с маленькой.

#### 6. style принимает объект

```jsx
// ✅ JSX
<div style={{ color: 'red', fontSize: '20px' }}>

// ❌ HTML
<div style="color: red; font-size: 20px;">
```

В JSX `style` — это **объект JavaScript**, в HTML — строка CSS.

#### 7. Комментарии

```jsx
// ✅ JSX
<div>
  {/* Это комментарий */}
  <p>Текст</p>
</div>

// ❌ HTML
<div>
  <!-- Это комментарий -->
  <p>Текст</p>
</div>
```

В JSX комментарии в **фигурных скобках**, в HTML — `<!-- -->`.

### Что можно, а что нельзя

#### В JSX можно:

```jsx
// Условный рендеринг
{isLoggedIn ? <UserMenu /> : <LoginButton />}

// Циклы
{todos.map(todo => <TodoItem key={todo.id} todo={todo} />)}

// Функции
<button onClick={() => console.log('clicked')}>

// Переменные
const name = 'Алекс';
<h1>Привет, {name}!</h1>
```

#### В HTML нельзя:

HTML не поддерживает логику JavaScript напрямую. Нужен JavaScript отдельно:

```html
<!-- HTML -->
<div id="app"></div>

<script>
  // JavaScript отдельно
  if (isLoggedIn) {
    document.getElementById("app").innerHTML = "<UserMenu />";
  }
</script>
```

### Визуальная аналогия

**HTML** — как инструкция по сборке мебели: статичная, браузер читает и показывает.

**JSX** — как чертёж мебели, который сначала обрабатывает инженер (компилятор), а потом строитель (React) создаёт реальную мебель (DOM).

### Итоги

- JSX — это JavaScript, а не HTML
- JSX компилируется в React.createElement()
- Отличия: className, camelCase, самозакрывающиеся теги, JavaScript-выражения
- JSX позволяет писать логику вместе с разметкой

---

## 8. Можно ли писать React без JSX

**Да, можно!** JSX — это **необязательный** синтаксический сахар. React можно писать, используя только `React.createElement()`.

### JSX vs React.createElement

**С JSX:**

```jsx
function Greeting({ name }) {
  return (
    <div className="card">
      <h1>Привет, {name}!</h1>
      <button onClick={() => alert("Клик!")}>Кнопка</button>
    </div>
  );
}
```

**Без JSX (только React.createElement):**

```js
function Greeting({ name }) {
  return React.createElement(
    "div",
    { className: "card" },
    React.createElement("h1", null, "Привет, ", name, "!"),
    React.createElement("button", { onClick: () => alert("Клик!") }, "Кнопка")
  );
}
```

### Как работает JSX под капотом

JSX **компилируется** в `React.createElement()`:

**До компиляции:**

```jsx
<div className="container">
  <h1>Заголовок</h1>
</div>
```

**После компиляции (Babel/TypeScript):**

```js
React.createElement(
  "div",
  { className: "container" },
  React.createElement("h1", null, "Заголовок")
);
```

### Синтаксис React.createElement

```js
React.createElement(
  type, // тип элемента (строка или компонент)
  props, // свойства (объект или null)
  ...children // дочерние элементы
);
```

**Примеры:**

```js
// Простой элемент
React.createElement("div", null, "Текст");

// С атрибутами
React.createElement("div", { className: "card" }, "Текст");

// С дочерними элементами
React.createElement(
  "div",
  null,
  React.createElement("h1", null, "Заголовок"),
  React.createElement("p", null, "Текст")
);

// Компонент
React.createElement(Greeting, { name: "Алекс" });
```

### Сложный пример без JSX

**С JSX:**

```jsx
function TodoList({ todos }) {
  return (
    <ul className="todo-list">
      {todos.map((todo) => (
        <li key={todo.id} className="todo-item">
          <input type="checkbox" checked={todo.completed} />
          <span>{todo.text}</span>
        </li>
      ))}
    </ul>
  );
}
```

**Без JSX:**

```js
function TodoList({ todos }) {
  return React.createElement(
    "ul",
    { className: "todo-list" },
    todos.map((todo) =>
      React.createElement(
        "li",
        { key: todo.id, className: "todo-item" },
        React.createElement("input", {
          type: "checkbox",
          checked: todo.completed,
        }),
        React.createElement("span", null, todo.text)
      )
    )
  );
}
```

Как видно, **без JSX код намного сложнее** и менее читаемый.

### Когда писать без JSX?

Обычно **никогда**. JSX делает код проще и понятнее.

Но иногда полезно понимать, как это работает:

1. **Отладка** — понимаешь, что происходит под капотом
2. **Динамическое создание** — когда нужно создавать элементы программно
3. **Библиотеки** — некоторые библиотеки работают без JSX

### Почему используется JSX?

1. **Читабельность** — код похож на HTML, понятнее
2. **Продуктивность** — быстрее писать
3. **Инструменты** — редакторы лучше работают с JSX (подсветка, автодополнение)
4. **Стандарт** — все используют JSX, проще работать в команде

### Итоги

- Можно писать React без JSX, используя React.createElement()
- JSX компилируется в React.createElement()
- JSX делает код проще и понятнее
- На практике всегда используют JSX

---

## 9. Что такое элемент React

React element (элемент React) — это **объект JavaScript**, который описывает, что должно быть отображено на экране.

### React Element — это объект

React element — это **не DOM-элемент**, а **простое описание** того, что нужно создать:

```jsx
const element = <h1>Привет, мир!</h1>;
```

После компиляции это становится объектом:

```js
const element = {
  type: "h1",
  props: {
    children: "Привет, мир!",
  },
};
```

### Свойства React Element

React element — это **неизменяемый объект** со свойствами:

- `type` — тип элемента (строка для HTML-тегов, функция/класс для компонентов)
- `props` — свойства элемента (атрибуты, children)

**Пример:**

```jsx
const element = (
  <div className="card" id="my-card">
    <h1>Заголовок</h1>
    <p>Текст</p>
  </div>
);
```

**Объект (упрощённо):**

```js
{
  type: 'div',
  props: {
    className: 'card',
    id: 'my-card',
    children: [
      {
        type: 'h1',
        props: {
          children: 'Заголовок'
        }
      },
      {
        type: 'p',
        props: {
          children: 'Текст'
        }
      }
    ]
  }
}
```

### Элемент vs DOM-элемент

**React element:**

- Это **объект JavaScript**
- Описывает, что должно быть отображено
- **Неизменяемый** (immutable)
- Легковесный

**DOM-element:**

- Это **реальный элемент** в браузере
- Можно изменять напрямую
- Тяжёлый (содержит методы, события и т.д.)

```jsx
const reactElement = <div>Привет</div>; // объект JavaScript

// React создаёт DOM-элемент только при рендере
ReactDOM.render(reactElement, document.getElementById("root"));
// Теперь в DOM есть реальный <div>
```

### Создание элементов

**С помощью JSX:**

```jsx
const element = <h1>Привет</h1>;
```

**С помощью React.createElement:**

```js
const element = React.createElement("h1", null, "Привет");
```

**Результат одинаковый** — объект React element.

### Элементы неизменяемы

React elements **нельзя изменять** после создания:

```jsx
const element = <h1>Привет</h1>;

// ❌ Неправильно
element.props.children = "Пока"; // Не работает!

// ✅ Правильно — создаём новый элемент
const newElement = <h1>Пока</h1>;
```

**Почему?** React использует неизменяемость для оптимизации. Если нужно изменить элемент, создаётся **новый**.

### Элементы компонентов

Element может описывать не только HTML-теги, но и компоненты:

```jsx
function Greeting({ name }) {
  return <h1>Привет, {name}!</h1>;
}

const element = <Greeting name="Алекс" />;
```

**Объект (упрощённо):**

```js
{
  type: Greeting,  // функция компонента
  props: {
    name: 'Алекс'
  }
}
```

React видит, что `type` — это функция, вызывает её и рендерит результат.

### Обновление элементов

React elements **не обновляются**, создаются **новые**:

```jsx
function Clock() {
  const [time, setTime] = useState(new Date());

  // Каждую секунду создаётся НОВЫЙ element
  return <div>Время: {time.toLocaleTimeString()}</div>;
}
```

При каждом рендере создаётся **новый объект**, старый не изменяется.

### Итоги

- React element — это объект JavaScript, описывающий UI
- Имеет свойства type и props
- Неизменяемый (immutable)
- Не является DOM-элементом
- React использует elements для создания DOM

---

## 10. Чем React element отличается от компонента

Это важное различие! **React element** — это объект, а **компонент** — это функция (или класс), которая возвращает element.

### React Element

React element — это **объект JavaScript**, описывающий, что нужно отобразить:

```jsx
const element = <h1>Привет</h1>;

// Это объект:
// {
//   type: 'h1',
//   props: { children: 'Привет' }
// }
```

Element — это **результат**, описание UI.

### Компонент

Компонент — это **функция** (или класс), которая создаёт и возвращает elements:

```jsx
// Это КОМПОНЕНТ (функция)
function Greeting() {
  return <h1>Привет</h1>; // Возвращает ELEMENT
}

// Это ELEMENT (объект, созданный компонентом)
const element = <Greeting />;
```

Компонент — это **фабрика**, которая создаёт elements.

### Визуальная аналогия

**Компонент** — как рецепт торта (инструкция, как сделать).
**Element** — как сам торт (результат работы по рецепту).

### Детальное сравнение

#### Компонент — это функция

```jsx
// КОМПОНЕНТ — функция
function Button({ text, onClick }) {
  return <button onClick={onClick}>{text}</button>;
}
```

Компонент можно вызвать и получить element:

```jsx
// Вызов компонента → получаем element
const element = Button({ text: "Клик", onClick: () => {} });
```

#### Element — это объект

```jsx
// ELEMENT — объект, созданный компонентом
const element = <Button text="Клик" onClick={() => {}} />;

// Или без JSX:
const element = React.createElement(Button, {
  text: "Клик",
  onClick: () => {},
});
```

### Пример для понимания

```jsx
// КОМПОНЕНТ (функция)
function UserCard({ user }) {
  return (
    <div>
      <h2>{user.name}</h2>
      <p>{user.email}</p>
    </div>
  );
}

// ELEMENT (объект, созданный компонентом)
const element = (
  <UserCard user={{ name: "Алекс", email: "alex@example.com" }} />
);
```

**Что происходит:**

1. `<UserCard />` — это **element** с `type: UserCard` (компонентом)
2. React видит, что `type` — функция, и вызывает её: `UserCard({ user: {...} })`
3. Компонент возвращает **новый element** (с `type: 'div'`)
4. React рендерит этот element в DOM

### Множественные элементы от одного компонента

Один компонент может создавать **много элементов**:

```jsx
// КОМПОНЕНТ (один)
function TodoItem({ todo }) {
  return <li>{todo.text}</li>;
}

// ELEMENTS (много, созданы одним компонентом)
const todos = [
  { id: 1, text: "Купить хлеб" },
  { id: 2, text: "Позвонить" },
];

const elements = todos.map((todo) => (
  <TodoItem key={todo.id} todo={todo} /> // Каждый — отдельный element
));
```

### Сравнительная таблица

| Аспект           | Компонент        | Element           |
| ---------------- | ---------------- | ----------------- |
| Что это          | Функция/класс    | Объект JavaScript |
| Назначение       | Создаёт elements | Описывает UI      |
| Можно вызвать?   | Да (функция)     | Нет (объект)      |
| Можно изменить?  | Можно переписать | Неизменяемый      |
| Где используется | В JSX как тег    | Результат рендера |

### Важные моменты

1. **Компонент вызывается, element создаётся:**

   ```jsx
   <Button /> // React создаёт element и вызывает Button()
   ```

2. **Компонент возвращает element:**

   ```jsx
   function Button() {
     return <button>Клик</button>; // возвращает element
   }
   ```

3. **Element может содержать компонент:**
   ```jsx
   <div>
     <Button /> // element с type: Button (компонентом)
   </div>
   ```

### Итоги

- Компонент — это функция/класс, создающая elements
- Element — это объект, описывающий UI
- Компонент вызывается, element создаётся
- Компонент возвращает element
- Один компонент может создавать много elements

---
