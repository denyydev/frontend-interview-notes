# React Components

Компоненты в React, их типы и принципы использования.  
Материал представлен в формате **вопрос–ответ**.

1. [Что такое компонент в React](#1-что-такое-компонент-в-react)
2. [Функциональные и классовые компоненты (в чём разница)](#2-функциональные-и-классовые-компоненты-в-чём-разница)
3. [Почему сейчас используют функциональные компоненты](#3-почему-сейчас-используют-функциональные-компоненты)
4. [Что компонент обязан возвращать](#4-что-компонент-обязан-возвращать)
5. [Можно ли вернуть несколько элементов из компонента](#5-можно-ли-вернуть-несколько-элементов-из-компонента)
6. [Что такое Fragment и зачем он нужен](#6-что-такое-fragment-и-зачем-он-нужен)
7. [Controlled vs Uncontrolled компоненты (базово)](#7-controlled-vs-uncontrolled-компоненты-базово)

## 1. Что такое компонент в React

Компонент в React — это **независимая, переиспользуемая часть UI**, которая возвращает JSX (React elements).

### Простое определение

Компонент — это **функция** (или класс), которая:

1. Принимает данные через `props`
2. Возвращает JSX, описывающий UI
3. Может иметь своё состояние (`state`)

### Простейший компонент

```jsx
function Greeting() {
  return <h1>Привет, мир!</h1>;
}
```

Это компонент, который всегда показывает одно и то же.

### Компонент с props

```jsx
function Greeting({ name }) {
  return <h1>Привет, {name}!</h1>;
}

// Использование
<Greeting name="Алекс" />  // Привет, Алекс!
<Greeting name="Мария" />  // Привет, Мария!
```

Один компонент, **разные данные**.

### Компонент с состоянием

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>Счёт: {count}</p>
      <button onClick={() => setCount(count + 1)}>Увеличить</button>
    </div>
  );
}
```

Компонент **управляет своим состоянием**.

### Зачем нужны компоненты?

#### 1. Переиспользование

Один компонент можно использовать много раз:

```jsx
function Button({ text, onClick }) {
  return <button onClick={onClick}>{text}</button>;
}

// Используем везде
<Button text="Сохранить" onClick={handleSave} />
<Button text="Отмена" onClick={handleCancel} />
<Button text="Удалить" onClick={handleDelete} />
```

#### 2. Разделение ответственности

Разбиваем сложный UI на простые части:

```jsx
function App() {
  return (
    <div>
      <Header />
      <Sidebar />
      <MainContent />
      <Footer />
    </div>
  );
}
```

Каждый компонент **отвечает за свою часть**.

#### 3. Читаемость

Код проще понимать:

```jsx
// Без компонентов — всё в одном месте (сложно)
function App() {
  return <div>{/* 200 строк JSX... */}</div>;
}

// С компонентами — разбито на части (понятно)
function App() {
  return (
    <div>
      <Header />
      <UserProfile />
      <PostsList />
    </div>
  );
}
```

### Компоненты как строительные блоки

Компоненты можно **вкладывать друг в друга**:

```jsx
function Card({ title, content }) {
  return (
    <div className="card">
      <CardHeader title={title} />
      <CardBody content={content} />
    </div>
  );
}

function CardHeader({ title }) {
  return <h2>{title}</h2>;
}

function CardBody({ content }) {
  return <p>{content}</p>;
}
```

### Аналогия

Компоненты — как **строительные блоки LEGO**:

- Каждый блок (компонент) делает что-то своё
- Блоки можно комбинировать
- Одинаковые блоки переиспользуются
- Из блоков строятся сложные конструкции

### Имена компонентов

Компоненты **всегда** пишутся с **большой буквы**:

```jsx
// ✅ Правильно
function Button() {}
function UserCard() {}

// ❌ Неправильно
function button() {} // React подумает, что это HTML-тег
function userCard() {} // React подумает, что это HTML-тег
```

### Итоги

- Компонент — это независимая, переиспользуемая часть UI
- Компонент — это функция, возвращающая JSX
- Компоненты можно переиспользовать и вкладывать друг в друга
- Компоненты делают код проще и понятнее
- Имя компонента всегда с большой буквы

---

## 2. Функциональные и классовые компоненты (в чём разница)

В React есть два типа компонентов: **функциональные** и **классовые**. Сейчас используются преимущественно функциональные.

### Функциональный компонент

Функциональный компонент — это **обычная JavaScript-функция**, которая возвращает JSX:

```jsx
function Greeting({ name }) {
  return <h1>Привет, {name}!</h1>;
}

// Или стрелочная функция
const Greeting = ({ name }) => {
  return <h1>Привет, {name}!</h1>;
};
```

### Классовый компонент

Классовый компонент — это **класс ES6**, который наследуется от `React.Component`:

```jsx
class Greeting extends React.Component {
  render() {
    return <h1>Привет, {this.props.name}!</h1>;
  }
}
```

### Ключевые отличия

#### 1. Синтаксис

**Функциональный:**

```jsx
function Counter() {
  const [count, setCount] = useState(0);
  return <div>{count}</div>;
}
```

**Классовый:**

```jsx
class Counter extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
  }

  render() {
    return <div>{this.state.count}</div>;
  }
}
```

#### 2. Работа с состоянием

**Функциональный (хуки):**

```jsx
function Counter() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

**Классовый:**

```jsx
class Counter extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
  }

  increment = () => {
    this.setState({ count: this.state.count + 1 });
  };

  render() {
    return <button onClick={this.increment}>{this.state.count}</button>;
  }
}
```

#### 3. Работа с props

**Функциональный:**

```jsx
function UserCard({ name, email }) {
  return (
    <div>
      <h2>{name}</h2>
      <p>{email}</p>
    </div>
  );
}
```

**Классовый:**

```jsx
class UserCard extends React.Component {
  render() {
    return (
      <div>
        <h2>{this.props.name}</h2>
        <p>{this.props.email}</p>
      </div>
    );
  }
}
```

#### 4. Побочные эффекты (useEffect)

**Функциональный:**

```jsx
function UserProfile({ userId }) {
  useEffect(() => {
    // Загрузка данных
    fetchUser(userId);
  }, [userId]);

  return <div>Профиль</div>;
}
```

**Классовый:**

```jsx
class UserProfile extends React.Component {
  componentDidMount() {
    fetchUser(this.props.userId);
  }

  componentDidUpdate(prevProps) {
    if (prevProps.userId !== this.props.userId) {
      fetchUser(this.props.userId);
    }
  }

  render() {
    return <div>Профиль</div>;
  }
}
```

#### 5. this контекст

**Функциональный:**

```jsx
function Button() {
  const handleClick = () => {
    console.log("Клик!"); // this не нужен
  };
  return <button onClick={handleClick}>Кнопка</button>;
}
```

**Классовый:**

```jsx
class Button extends React.Component {
  handleClick = () => {
    console.log("Клик!"); // стрелочная функция для сохранения this
  };

  // или bind в constructor
  constructor(props) {
    super(props);
    this.handleClick = this.handleClick.bind(this);
  }

  render() {
    return <button onClick={this.handleClick}>Кнопка</button>;
  }
}
```

### Сравнительная таблица

| Аспект             | Функциональный    | Классовый                                 |
| ------------------ | ----------------- | ----------------------------------------- |
| Синтаксис          | Функция           | Класс ES6                                 |
| Состояние          | `useState` хук    | `this.state`                              |
| Побочные эффекты   | `useEffect` хук   | `componentDidMount`, `componentDidUpdate` |
| Props              | Параметры функции | `this.props`                              |
| this               | Не нужен          | Нужен (сложности с контекстом)            |
| Производительность | Чуть лучше        | Хорошая                                   |
| Размер кода        | Меньше            | Больше                                    |

### Когда использовать классовые компоненты?

**Практически никогда!** Классовые компоненты используются только:

- В старых проектах
- Для компонентов с Error Boundaries (но можно использовать библиотеки)

### Итоги

- Функциональные компоненты — функции, возвращающие JSX
- Классовые компоненты — классы, наследуемые от React.Component
- Функциональные проще и современнее
- Сейчас используются почти исключительно функциональные компоненты
- Классовые компоненты остались для обратной совместимости

---

## 4. Почему сейчас используют функциональные компоненты

Функциональные компоненты стали стандартом в React. Вот основные причины.

### 1. Проще синтаксис

**Классовый компонент:**

```jsx
class Counter extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
  }

  increment = () => {
    this.setState({ count: this.state.count + 1 });
  };

  render() {
    return <button onClick={this.increment}>{this.state.count}</button>;
  }
}
```

**Функциональный компонент:**

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

Функциональный компонент **короче и понятнее**.

### 2. Нет проблем с this

В классовых компонентах нужно помнить про `this`:

```jsx
class Button extends React.Component {
  handleClick() {
    console.log(this); // undefined без bind!
  }

  render() {
    return <button onClick={this.handleClick}>Кнопка</button>;
  }
}
```

Нужно либо `bind`, либо стрелочные функции:

```jsx
// Решение 1: bind
constructor(props) {
  super(props);
  this.handleClick = this.handleClick.bind(this);
}

// Решение 2: стрелочная функция
handleClick = () => {
  console.log(this);
}
```

В функциональных компонентах `this` **не нужен**:

```jsx
function Button() {
  const handleClick = () => {
    console.log("Клик!"); // всё просто
  };

  return <button onClick={handleClick}>Кнопка</button>;
}
```

### 3. Хуки — мощный инструмент

Хуки (hooks) работают **только с функциональными компонентами**:

```jsx
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetchUser(userId).then((data) => {
      setUser(data);
      setLoading(false);
    });
  }, [userId]);

  if (loading) return <div>Загрузка...</div>;
  return <div>{user.name}</div>;
}
```

В классовых компонентах нужно использовать несколько методов жизненного цикла:

```jsx
class UserProfile extends React.Component {
  state = { user: null, loading: true };

  componentDidMount() {
    this.fetchUser();
  }

  componentDidUpdate(prevProps) {
    if (prevProps.userId !== this.props.userId) {
      this.fetchUser();
    }
  }

  fetchUser = async () => {
    const user = await fetchUser(this.props.userId);
    this.setState({ user, loading: false });
  };

  render() {
    if (this.state.loading) return <div>Загрузка...</div>;
    return <div>{this.state.user.name}</div>;
  }
}
```

Хуки **объединяют логику** в одном месте.

### 4. Легче тестировать

Функциональные компоненты — это **чистые функции**, их проще тестировать:

```jsx
function Greeting({ name }) {
  return <h1>Привет, {name}!</h1>;
}

// Тест простой
test("renders greeting", () => {
  const { getByText } = render(<Greeting name="Алекс" />);
  expect(getByText("Привет, Алекс!")).toBeInTheDocument();
});
```

Классовые компоненты требуют создания экземпляра класса.

### 5. Меньше кода

Функциональные компоненты **короче**:

```jsx
// Функциональный — 5 строк
function Button({ text, onClick }) {
  return <button onClick={onClick}>{text}</button>;
}

// Классовый — 10+ строк
class Button extends React.Component {
  render() {
    return <button onClick={this.props.onClick}>{this.props.text}</button>;
  }
}
```

### 6. Переиспользование логики

С хуками можно создавать **custom hooks** для переиспользования логики:

```jsx
// Custom hook
function useCounter(initialValue = 0) {
  const [count, setCount] = useState(initialValue);
  const increment = () => setCount(count + 1);
  const decrement = () => setCount(count - 1);
  return { count, increment, decrement };
}

// Использование в разных компонентах
function Counter1() {
  const { count, increment } = useCounter();
  return <button onClick={increment}>{count}</button>;
}

function Counter2() {
  const { count, increment } = useCounter(10);
  return <button onClick={increment}>{count}</button>;
}
```

В классовых компонентах для этого использовали паттерны HOC (Higher-Order Components) или Render Props, которые сложнее.

### 7. Производительность

Функциональные компоненты **чуть быстрее**, потому что:

- Нет создания экземпляра класса
- Меньше накладных расходов
- React может лучше оптимизировать

Разница небольшая, но она есть.

### 8. Будущее React

React команда **фокусируется на функциональных компонентах**:

- Все новые фичи для функциональных компонентов
- Хуки — основной способ работы с состоянием и эффектами
- Классовые компоненты остались для обратной совместимости

### Когда ещё используют классовые компоненты?

Практически **никогда в новых проектах**. Только:

- В старых проектах (legacy code)
- Для Error Boundaries (но можно использовать библиотеки)

### Итоги

- Функциональные компоненты проще и короче
- Нет проблем с `this`
- Хуки — мощный инструмент для работы с состоянием и эффектами
- Легче тестировать и переиспользовать логику
- React команда фокусируется на функциональных компонентах
- Классовые компоненты остались для обратной совместимости

---

## 4. Что компонент обязан возвращать

Компонент React **обязан возвращать что-то**, что может быть отрендерено. Это может быть JSX, `null`, или `false`.

### Обязательный return

Компонент должен **возвращать значение**:

```jsx
// ✅ Правильно
function Greeting() {
  return <h1>Привет!</h1>;
}

// ❌ Неправильно (нет return)
function Greeting() {
  <h1>Привет!</h1>; // Не вернёт ничего (undefined)
}
```

### Что можно возвращать?

#### 1. JSX элемент

```jsx
function Button() {
  return <button>Кнопка</button>;
}
```

#### 2. Несколько элементов (Fragment или обёртка)

```jsx
function List() {
  return (
    <>
      <li>Пункт 1</li>
      <li>Пункт 2</li>
    </>
  );
}
```

#### 3. null (условный рендеринг)

```jsx
function Conditional({ show }) {
  if (!show) {
    return null; // Ничего не рендерится
  }
  return <div>Видимо</div>;
}
```

#### 4. false (условный рендеринг)

```jsx
function Conditional({ show }) {
  return show && <div>Видимо</div>; // false не рендерится
}
```

#### 5. Строка или число

```jsx
function Text() {
  return "Простой текст";
}

function Number() {
  return 42;
}
```

#### 6. Массив элементов

```jsx
function List() {
  return [<li key="1">Пункт 1</li>, <li key="2">Пункт 2</li>];
}
```

### Что нельзя возвращать?

#### undefined

```jsx
// ❌ Неправильно
function Greeting() {
  // Нет return — вернёт undefined
}

// ✅ Правильно
function Greeting() {
  return <h1>Привет!</h1>;
}
```

#### Обычный объект (не JSX)

```jsx
// ❌ Неправильно
function Data() {
  return { name: 'Алекс' };  // Ошибка!
}

// ✅ Правильно
function Data() {
  return <div>{name: 'Алекс'}</div>;
}
```

### Явный и неявный return

**Явный return (фигурные скобки):**

```jsx
function Greeting() {
  return <h1>Привет!</h1>;
}
```

**Неявный return (без фигурных скобок):**

```jsx
const Greeting = () => <h1>Привет!</h1>;
```

Оба варианта правильные.

### Условный возврат

Компонент может возвращать разные значения в зависимости от условий:

```jsx
function UserProfile({ user }) {
  if (!user) {
    return <div>Загрузка...</div>;
  }

  if (user.error) {
    return <div>Ошибка загрузки</div>;
  }

  return (
    <div>
      <h1>{user.name}</h1>
      <p>{user.email}</p>
    </div>
  );
}
```

**Важно:** если используется `if`, нужен `return` для каждого случая.

### Ранний возврат (early return)

```jsx
function Component({ data }) {
  // Ранний возврат для простых случаев
  if (!data) return null;
  if (data.loading) return <div>Загрузка...</div>;

  // Основная логика
  return <div>{data.content}</div>;
}
```

Это делает код **понятнее**.

### ⚠️ Частая ошибка

Забывают `return`:

```jsx
// ❌ Неправильно
function Button() {
  <button>Кнопка</button>; // Нет return!
}

// ✅ Правильно
function Button() {
  return <button>Кнопка</button>;
}
```

### Итоги

- Компонент обязан возвращать значение
- Можно возвращать: JSX, null, false, строку, число, массив элементов
- Нельзя возвращать: undefined, обычный объект
- Можно использовать условный возврат и ранний возврат
- Всегда проверяй, что есть `return`

---

## 5. Можно ли вернуть несколько элементов из компонента

**Да, можно!** Но есть нюансы. Компонент не может вернуть **несколько элементов на верхнем уровне** без обёртки.

### Проблема: несколько элементов без обёртки

```jsx
// ❌ Ошибка!
function List() {
  return (
    <li>Пункт 1</li>
    <li>Пункт 2</li>
  );
}
```

Это **синтаксическая ошибка**. JSX требует **один корневой элемент**.

### Решение 1: Обёртка div

```jsx
// ✅ Правильно
function List() {
  return (
    <div>
      <li>Пункт 1</li>
      <li>Pункт 2</li>
    </div>
  );
}
```

**Проблема:** лишний `<div>` в DOM.

### Решение 2: Fragment (рекомендуется)

Fragment — это **невидимая обёртка**, которая не создаёт элемент в DOM:

```jsx
// ✅ Правильно (синтаксис Fragment)
function List() {
  return (
    <>
      <li>Пункт 1</li>
      <li>Пункт 2</li>
    </>
  );
}
```

Или явный синтаксис:

```jsx
// ✅ Правильно (явный Fragment)
function List() {
  return (
    <React.Fragment>
      <li>Пункт 1</li>
      <li>Пункт 2</li>
    </React.Fragment>
  );
}
```

**Преимущество:** нет лишнего элемента в DOM.

### Решение 3: Массив элементов

Можно вернуть **массив элементов** (нужны key):

```jsx
// ✅ Правильно
function List() {
  return [<li key="1">Пункт 1</li>, <li key="2">Пункт 2</li>];
}
```

**Важно:** элементы должны иметь `key`.

### Когда что использовать?

#### Fragment — для нескольких элементов

```jsx
function Card() {
  return (
    <>
      <h2>Заголовок</h2>
      <p>Текст</p>
      <button>Кнопка</button>
    </>
  );
}
```

#### div — когда нужна обёртка для стилей

```jsx
function Card() {
  return (
    <div className="card">
      <h2>Заголовок</h2>
      <p>Текст</p>
    </div>
  );
}
```

#### Массив — для динамических списков

```jsx
function TodoList({ todos }) {
  return todos.map((todo) => <TodoItem key={todo.id} todo={todo} />);
}
```

### Fragment с ключами

Если нужны ключи (например, в циклах), используй явный Fragment:

```jsx
function List({ items }) {
  return items.map((item) => (
    <React.Fragment key={item.id}>
      <dt>{item.term}</dt>
      <dd>{item.definition}</dd>
    </React.Fragment>
  ));
}
```

**Важно:** короткий синтаксис `<>` не поддерживает ключи.

### Сравнение решений

| Решение      | DOM элемент | Ключи                            | Когда использовать             |
| ------------ | ----------- | -------------------------------- | ------------------------------ |
| `<div>`      | Да (лишний) | Не нужны                         | Когда нужна обёртка для стилей |
| `<Fragment>` | Нет         | Поддерживаются (явный синтаксис) | Когда не нужна обёртка         |
| Массив       | Нет         | Обязательны                      | Для динамических списков       |

### ⚠️ Частая ошибка

Забывают обёртку:

```jsx
// ❌ Ошибка
function Component() {
  return (
    <h1>Заголовок</h1>
    <p>Текст</p>
  );
}

// ✅ Правильно
function Component() {
  return (
    <>
      <h1>Заголовок</h1>
      <p>Текст</p>
    </>
  );
}
```

### Итоги

- Можно вернуть несколько элементов, но нужна обёртка
- Fragment — лучший вариант (нет лишнего DOM-элемента)
- div — когда нужна обёртка для стилей
- Массив — для динамических списков (нужны key)
- Всегда используй обёртку для нескольких элементов

---

## 6. Что такое Fragment и зачем он нужен

Fragment (фрагмент) — это специальный компонент React, который позволяет группировать несколько элементов **без создания дополнительного DOM-элемента**.

### Проблема: лишний div

Без Fragment нужна обёртка:

```jsx
function List() {
  return (
    <div>
      {" "}
      {/* Лишний элемент в DOM! */}
      <li>Пункт 1</li>
      <li>Пункт 2</li>
    </div>
  );
}
```

В DOM появится лишний `<div>`, который может мешать стилям.

### Решение: Fragment

Fragment позволяет группировать элементы **без создания элемента в DOM**:

```jsx
function List() {
  return (
    <>
      <li>Пункт 1</li>
      <li>Пункт 2</li>
    </>
  );
}
```

В DOM будут только `<li>`, без обёртки!

### Два синтаксиса Fragment

#### 1. Короткий синтаксис (рекомендуется)

```jsx
function List() {
  return (
    <>
      <li>Пункт 1</li>
      <li>Пункт 2</li>
    </>
  );
}
```

**Преимущества:**

- Короче
- Читабельнее
- Не нужно импортировать

**Ограничение:** нельзя использовать ключи (key).

#### 2. Явный синтаксис

```jsx
import React from "react";

function List() {
  return (
    <React.Fragment>
      <li>Пункт 1</li>
      <li>Пункт 2</li>
    </React.Fragment>
  );
}
```

Или:

```jsx
import { Fragment } from "react";

function List() {
  return (
    <Fragment>
      <li>Пункт 1</li>
      <li>Пункт 2</li>
    </Fragment>
  );
}
```

**Преимущества:**

- Можно использовать ключи
- Явнее видно, что это Fragment

### Когда использовать Fragment?

#### 1. Несколько элементов без обёртки

```jsx
function Card() {
  return (
    <>
      <h2>Заголовок</h2>
      <p>Текст</p>
      <button>Кнопка</button>
    </>
  );
}
```

#### 2. Списки определений (dl/dt/dd)

```jsx
function Glossary({ items }) {
  return (
    <dl>
      {items.map((item) => (
        <React.Fragment key={item.id}>
          <dt>{item.term}</dt>
          <dd>{item.definition}</dd>
        </React.Fragment>
      ))}
    </dl>
  );
}
```

**Важно:** для ключей нужен явный `<React.Fragment>`.

#### 3. Таблицы (table/tr/td)

```jsx
function Table({ rows }) {
  return (
    <table>
      <tbody>
        {rows.map((row) => (
          <React.Fragment key={row.id}>
            <tr>
              <td>{row.col1}</td>
              <td>{row.col2}</td>
            </tr>
          </React.Fragment>
        ))}
      </tbody>
    </table>
  );
}
```

### Fragment vs div

**С div:**

```jsx
function List() {
  return (
    <div>
      <li>Пункт 1</li>
      <li>Пункт 2</li>
    </div>
  );
}

// DOM:
// <div>
//   <li>Пункт 1</li>
//   <li>Пункт 2</li>
// </div>
```

**С Fragment:**

```jsx
function List() {
  return (
    <>
      <li>Пункт 1</li>
      <li>Пункт 2</li>
    </>
  );
}

// DOM:
// <li>Пункт 1</li>
// <li>Пункт 2</li>
```

Fragment **не создаёт элемента** в DOM.

### Когда НЕ нужен Fragment?

Если нужна обёртка для стилей:

```jsx
function Card() {
  return (
    <div className="card">
      {" "}
      {/* Обёртка нужна для стилей */}
      <h2>Заголовок</h2>
      <p>Текст</p>
    </div>
  );
}
```

### Итоги

- Fragment — компонент для группировки элементов без создания DOM-элемента
- Два синтаксиса: `<>` (короткий) и `<React.Fragment>` (явный)
- Используй Fragment, когда не нужна обёртка
- Для ключей используй явный `<React.Fragment>`
- Fragment делает DOM чище и проще

---

## 7. Controlled vs Uncontrolled компоненты (базово)

В React есть два подхода к работе с формами и input-элементами: **Controlled** (управляемые) и **Uncontrolled** (неуправляемые) компоненты.

### Controlled компоненты (управляемые)

Controlled компонент — это компонент, значение которого **контролируется React state**.

#### Как это работает?

Значение input **хранится в state**, и изменения обрабатываются через обработчики событий:

```jsx
function Input() {
  const [value, setValue] = useState("");

  return (
    <input
      value={value} // Значение из state
      onChange={(e) => setValue(e.target.value)} // Обновляем state
    />
  );
}
```

**Поток данных:**

```
State → Input value → Пользователь вводит → onChange → Обновляем state → Input value
```

React **полностью контролирует** значение input.

### Uncontrolled компоненты (неуправляемые)

Uncontrolled компонент — это компонент, значение которого **хранится в DOM**, а не в React state.

#### Как это работает?

Используем `ref` для доступа к значению:

```jsx
function Input() {
  const inputRef = useRef(null);

  const handleSubmit = () => {
    console.log(inputRef.current.value); // Получаем значение из DOM
  };

  return (
    <input
      ref={inputRef} // Ссылка на DOM-элемент
      defaultValue="Начальное значение" // Только начальное значение
    />
  );
}
```

**Поток данных:**

```
DOM хранит значение → Пользователь вводит → Значение в DOM → Читаем через ref
```

React **не контролирует** значение, оно хранится в DOM.

### Сравнение

#### Controlled

```jsx
function ControlledInput() {
  const [value, setValue] = useState("");

  return (
    <div>
      <input value={value} onChange={(e) => setValue(e.target.value)} />
      <p>Текущее значение: {value}</p>
    </div>
  );
}
```

**Преимущества:**

- React полностью контролирует значение
- Можно легко валидировать
- Можно изменять значение программно
- Можно сбросить значение (setValue(''))

**Недостатки:**

- Больше кода (state + handler)
- Ре-рендер при каждом изменении

#### Uncontrolled

```jsx
function UncontrolledInput() {
  const inputRef = useRef(null);

  const handleClick = () => {
    alert(inputRef.current.value);
  };

  return (
    <div>
      <input ref={inputRef} defaultValue="Начальное" />
      <button onClick={handleClick}>Показать значение</button>
    </div>
  );
}
```

**Преимущества:**

- Меньше кода
- Нет ре-рендеров при каждом изменении
- Проще для простых форм

**Недостатки:**

- Сложнее валидировать в реальном времени
- Нельзя легко изменить значение программно
- Меньше контроля

### Когда что использовать?

#### Controlled — рекомендуется для большинства случаев

```jsx
// Валидация в реальном времени
function EmailInput() {
  const [email, setEmail] = useState("");
  const [error, setError] = useState("");

  const handleChange = (e) => {
    const value = e.target.value;
    setEmail(value);

    // Валидация в реальном времени
    if (!value.includes("@")) {
      setError("Некорректный email");
    } else {
      setError("");
    }
  };

  return (
    <div>
      <input value={email} onChange={handleChange} />
      {error && <p>{error}</p>}
    </div>
  );
}
```

#### Uncontrolled — для простых случаев

```jsx
// Простая форма, значение нужно только при submit
function SimpleForm() {
  const nameRef = useRef(null);
  const emailRef = useRef(null);

  const handleSubmit = (e) => {
    e.preventDefault();
    const data = {
      name: nameRef.current.value,
      email: emailRef.current.value,
    };
    console.log(data);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input ref={nameRef} defaultValue="" />
      <input ref={emailRef} defaultValue="" />
      <button type="submit">Отправить</button>
    </form>
  );
}
```

### Сравнительная таблица

| Аспект                | Controlled               | Uncontrolled             |
| --------------------- | ------------------------ | ------------------------ |
| Значение хранится     | В React state            | В DOM                    |
| Доступ к значению     | Из state                 | Через ref                |
| Валидация             | Легко в реальном времени | Сложнее                  |
| Программное изменение | Легко (setState)         | Сложнее                  |
| Ре-рендеры            | При каждом изменении     | Только при необходимости |
| Код                   | Больше                   | Меньше                   |
| Когда использовать    | В большинстве случаев    | Простые формы            |

### Переключение между Controlled и Uncontrolled

⚠️ **Нельзя переключаться** между controlled и uncontrolled для одного компонента:

```jsx
// ❌ Неправильно
function BadInput({ isControlled }) {
  const [value, setValue] = useState("");
  const ref = useRef(null);

  return isControlled ? (
    <input value={value} onChange={(e) => setValue(e.target.value)} />
  ) : (
    <input ref={ref} defaultValue="" />
  );
}
```

React выдаст предупреждение. Выбери один подход и придерживайся его.

### Итоги

- Controlled — значение контролируется React state
- Uncontrolled — значение хранится в DOM, доступ через ref
- Controlled рекомендуется для большинства случаев (больше контроля)
- Uncontrolled — для простых форм (меньше кода)
- Нельзя переключаться между подходами для одного компонента

---
