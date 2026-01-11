# React Hooks

Хуки в React, их назначение и особенности использования.  
Материал представлен в формате **вопрос–ответ**.

1. [Что такое хуки](#1-что-такое-хуки)
2. [Зачем хуки были добавлены в React](#2-зачем-хуки-были-добавлены-в-react)
3. [Основные правила хуков](#3-основные-правила-хуков)
4. [Что такое useState](#4-что-такое-usestate)
5. [Как работает useState под капотом (в общих чертах)](#5-как-работает-usestate-под-капотом-в-общих-чертах)
6. [Что такое useEffect](#6-что-такое-useeffect)
7. [Когда вызывается useEffect](#7-когда-вызывается-useeffect)
8. [Разница между useEffect([], [deps], без deps)](#8-разница-между-useeffect-deps-без-deps)
9. [Cleanup-функция в useEffect](#9-cleanup-функция-в-useeffect)
10. [Почему нельзя делать useEffect async](#10-почему-нельзя-делать-useeffect-async)
11. [Что такое dependency array и зачем он нужен](#11-что-такое-dependency-array-и-зачем-он-нужен)

## 1. Что такое хуки

Хуки (hooks) — это **специальные функции React**, которые позволяют использовать состояние и другие возможности React в функциональных компонентах.

### Простое определение

Хуки — это функции, которые начинаются с префикса `use` (например, `useState`, `useEffect`). Они "цепляются" к React и дают доступ к его возможностям.

### Базовые хуки

#### useState

```jsx
import { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0); // Хук useState

  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

#### useEffect

```jsx
import { useEffect } from "react";

function Component() {
  useEffect(() => {
    console.log("Компонент отрендерился");
  });

  return <div>Компонент</div>;
}
```

### Зачем нужны хуки?

До хуков функциональные компоненты были **только для отображения** (presentational components). Чтобы использовать состояние или побочные эффекты, нужно было использовать классовые компоненты.

**Без хуков (классовый компонент):**

```jsx
class Counter extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
  }

  componentDidMount() {
    console.log("Монтирован");
  }

  render() {
    return (
      <button onClick={() => this.setState({ count: this.state.count + 1 })}>
        {this.state.count}
      </button>
    );
  }
}
```

**С хуками (функциональный компонент):**

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    console.log("Монтирован");
  }, []);

  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

### Правила хуков

Хуки имеют **строгие правила** использования:

1. **Вызывай хуки только на верхнем уровне** (не в циклах, условиях, вложенных функциях)
2. **Вызывай хуки только в функциональных компонентах** или в других хуках

### Встроенные хуки React

#### Базовые хуки

- `useState` — для состояния
- `useEffect` — для побочных эффектов
- `useContext` — для контекста

#### Дополнительные хуки

- `useReducer` — альтернатива useState
- `useCallback` — мемоизация функций
- `useMemo` — мемоизация значений
- `useRef` — ссылки на элементы
- `useImperativeHandle` — кастомный ref
- `useLayoutEffect` — эффект перед paint
- `useDebugValue` — для кастомных хуков

### Визуальная аналогия

Хуки — как **инструменты в ящике**:

- `useState` — инструмент для хранения данных
- `useEffect` — инструмент для выполнения действий
- `useContext` — инструмент для получения данных из контекста

Каждый хук решает свою задачу, но все они "подключаются" к React одинаково.

### Итоги

- Хуки — функции с префиксом `use`, дающие доступ к возможностям React
- Позволяют использовать состояние и эффекты в функциональных компонентах
- Есть строгие правила использования
- React предоставляет встроенные хуки
- Можно создавать свои кастомные хуки

---

## 2. Зачем хуки были добавлены в React

Хуки были добавлены в React 16.8 (2019) для решения проблем, которые возникали при работе с классовыми компонентами и переиспользованием логики.

### Проблема 1: Переиспользование логики

Без хуков для переиспользования логики использовались **HOC (Higher-Order Components)** или **Render Props**, что создавало "обёртки ада" (wrapper hell):

```jsx
// HOC пример
function withAuth(Component) {
  return class extends React.Component {
    componentDidMount() {
      // Логика авторизации
    }
    render() {
      return <Component {...this.props} />;
    }
  };
}

// Render Props пример
function AuthProvider({ children }) {
  return children({ isAuthenticated: true });
}

// Использование — много обёрток
<ThemeProvider>
  <AuthProvider>
    <LocaleProvider>
      <Component />
    </LocaleProvider>
  </AuthProvider>
</ThemeProvider>;
```

С хуками логику можно переиспользовать через **custom hooks**:

```jsx
// Custom hook
function useAuth() {
  const [isAuthenticated, setIsAuthenticated] = useState(false);
  // Логика авторизации
  return { isAuthenticated };
}

// Использование — просто
function Component() {
  const { isAuthenticated } = useAuth();
  return <div>...</div>;
}
```

### Проблема 2: Сложность классовых компонентов

Классовые компоненты были сложными:

```jsx
class UserProfile extends React.Component {
  constructor(props) {
    super(props);
    this.state = { user: null, loading: true };
    this.handleClick = this.handleClick.bind(this); // Нужен bind
  }

  componentDidMount() {
    this.fetchUser();
  }

  componentDidUpdate(prevProps) {
    if (prevProps.userId !== this.props.userId) {
      this.fetchUser();
    }
  }

  componentWillUnmount() {
    // Очистка
  }

  fetchUser = async () => {
    // Логика разбросана по разным методам
  };

  handleClick() {
    // Проблемы с this
  }

  render() {
    // ...
  }
}
```

С хуками логика **в одном месте**:

```jsx
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetchUser(userId).then((user) => {
      setUser(user);
      setLoading(false);
    });
  }, [userId]);

  const handleClick = () => {
    // Нет проблем с this
  };

  return <div>...</div>;
}
```

### Проблема 3: Проблемы с this

В классовых компонентах нужно было помнить про `this`:

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

Нужен либо `bind`, либо стрелочные функции:

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
    console.log("Клик!"); // Всё просто
  };

  return <button onClick={handleClick}>Кнопка</button>;
}
```

### Проблема 4: Разделение связанной логики

В классовых компонентах связанная логика **разбросана** по разным методам:

```jsx
class Component extends React.Component {
  componentDidMount() {
    // Логика подписки 1
    document.addEventListener("click", this.handleClick);
    // Логика подписки 2
    window.addEventListener("resize", this.handleResize);
  }

  componentDidUpdate() {
    // Логика обновления 1
    // Логика обновления 2
  }

  componentWillUnmount() {
    // Очистка подписки 1
    document.removeEventListener("click", this.handleClick);
    // Очистка подписки 2
    window.removeEventListener("resize", this.handleResize);
  }
}
```

С хуками связанная логика **вместе**:

```jsx
function Component() {
  // Вся логика подписки на клики — вместе
  useEffect(() => {
    document.addEventListener("click", handleClick);
    return () => {
      document.removeEventListener("click", handleClick);
    };
  }, []);

  // Вся логика подписки на resize — вместе
  useEffect(() => {
    window.addEventListener("resize", handleResize);
    return () => {
      window.removeEventListener("resize", handleResize);
    };
  }, []);
}
```

### Проблема 5: Понимание React

Классовые компоненты требуют знания:

- JavaScript классов
- `this` контекста
- Методов жизненного цикла
- Когда что вызывается

Хуки проще — это просто функции.

### Что дали хуки?

1. **Переиспользование логики** — через custom hooks
2. **Простота** — нет `this`, нет bind
3. **Группировка логики** — связанная логика вместе
4. **Меньше кода** — функциональные компоненты короче
5. **Лучшая читаемость** — код проще понять

### Обратная совместимость

**Важно:** хуки не сломали старый код. Классовые компоненты **всё ещё работают**. React команда не планирует их удалять.

### Итоги

- Хуки решают проблемы переиспользования логики
- Упрощают код (нет `this`, нет bind)
- Позволяют группировать связанную логику
- Делают функциональные компоненты полнофункциональными
- Классовые компоненты остались для обратной совместимости

---

## 3. Основные правила хуков

Хуки имеют **строгие правила**, которые нужно соблюдать. Нарушение этих правил приведёт к ошибкам и багам.

### Правило 1: Вызывай хуки только на верхнем уровне

**НЕ вызывай хуки:**

- В циклах
- В условиях (`if`, `switch`)
- Во вложенных функциях
- В обработчиках событий (напрямую)

#### ❌ Неправильно

```jsx
function Component({ condition }) {
  if (condition) {
    const [count, setCount] = useState(0); // ❌ Ошибка!
  }

  return <div>...</div>;
}
```

```jsx
function Component({ items }) {
  items.map((item) => {
    const [state, setState] = useState(0); // ❌ Ошибка!
    return <div>...</div>;
  });
}
```

```jsx
function Component() {
  const handleClick = () => {
    const [count, setCount] = useState(0); // ❌ Ошибка!
  };

  return <button onClick={handleClick}>Кнопка</button>;
}
```

#### ✅ Правильно

```jsx
function Component({ condition }) {
  const [count, setCount] = useState(0); // ✅ На верхнем уровне

  if (condition) {
    // Используем count здесь
  }

  return <div>...</div>;
}
```

### Правило 2: Вызывай хуки только в функциональных компонентах или других хуках

**НЕ вызывай хуки:**

- В обычных JavaScript-функциях
- В классовых компонентах
- В обработчиках событий

#### ❌ Неправильно

```jsx
// Обычная функция
function regularFunction() {
  const [count, setCount] = useState(0); // ❌ Ошибка!
}

// Классовый компонент
class Component extends React.Component {
  render() {
    const [count, setCount] = useState(0); // ❌ Ошибка!
    return <div>...</div>;
  }
}
```

#### ✅ Правильно

```jsx
// Функциональный компонент
function Component() {
  const [count, setCount] = useState(0); // ✅ Правильно
  return <div>...</div>;
}

// Custom hook
function useCustomHook() {
  const [count, setCount] = useState(0); // ✅ Правильно (внутри хука)
  return count;
}
```

### Почему эти правила важны?

React **полагается на порядок вызовов хуков** для правильной работы. Если хуки вызываются условно, порядок может измениться, и React не сможет правильно сопоставить состояние.

#### Пример проблемы

```jsx
// ❌ Неправильно
function Component({ condition }) {
  if (condition) {
    const [name, setName] = useState("");
  }
  const [count, setCount] = useState(0);

  // При первом рендере: name, count
  // При втором рендере (condition = false): только count
  // React запутается в порядке!
}
```

```jsx
// ✅ Правильно
function Component({ condition }) {
  const [name, setName] = useState("");
  const [count, setCount] = useState(0);

  // Порядок всегда одинаковый: name, count
}
```

### ESLint плагин

React предоставляет ESLint плагин `eslint-plugin-react-hooks`, который **автоматически проверяет** правила хуков:

```json
{
  "plugins": ["react-hooks"],
  "rules": {
    "react-hooks/rules-of-hooks": "error",
    "react-hooks/exhaustive-deps": "warn"
  }
}
```

### Условная логика с хуками

Если нужна условная логика, используй **условия внутри хуков**, а не условия вокруг хуков:

```jsx
// ❌ Неправильно
function Component({ userId }) {
  if (userId) {
    useEffect(() => {
      fetchUser(userId);
    }, [userId]);
  }
}

// ✅ Правильно
function Component({ userId }) {
  useEffect(() => {
    if (userId) {
      fetchUser(userId);
    }
  }, [userId]);
}
```

### Хуки в циклах

Если нужно использовать хуки в цикле, нужно вынести логику в **отдельный компонент**:

```jsx
// ❌ Неправильно
function List({ items }) {
  return items.map((item) => {
    const [count, setCount] = useState(0); // ❌ Ошибка!
    return <div key={item.id}>{count}</div>;
  });
}

// ✅ Правильно
function ListItem({ item }) {
  const [count, setCount] = useState(0); // ✅ Правильно
  return <div>{count}</div>;
}

function List({ items }) {
  return items.map((item) => <ListItem key={item.id} item={item} />);
}
```

### ⚠️ Частая ошибка

Условное использование хуков:

```jsx
// ❌ Неправильно
function Component({ isLoggedIn }) {
  if (isLoggedIn) {
    const [user, setUser] = useState(null);
  }

  return <div>...</div>;
}

// ✅ Правильно
function Component({ isLoggedIn }) {
  const [user, setUser] = useState(null); // Всегда вызываем

  useEffect(() => {
    if (isLoggedIn) {
      fetchUser().then(setUser);
    }
  }, [isLoggedIn]);

  return <div>...</div>;
}
```

### Итоги

- Хуки вызывай только на верхнем уровне (не в условиях, циклах, вложенных функциях)
- Хуки вызывай только в функциональных компонентах или других хуках
- React полагается на порядок вызовов хуков
- Используй ESLint плагин для проверки
- Условную логику делай внутри хуков, а не вокруг них

---

## 4. Что такое useState

`useState` — это хук React, который позволяет добавлять **состояние** (state) в функциональные компоненты.

### Базовый синтаксис

```jsx
import { useState } from "react";

const [state, setState] = useState(initialValue);
```

- `state` — текущее значение состояния
- `setState` — функция для обновления состояния
- `initialValue` — начальное значение (используется только при первом рендере)

### Простейший пример

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

При каждом клике `count` увеличивается на 1, и компонент **перерисовывается**.

### Как это работает?

1. **Первый рендер:** `useState(0)` создаёт состояние со значением `0`
2. **Клик:** `setCount(count + 1)` обновляет состояние
3. **Ре-рендер:** компонент перерисовывается с новым значением

### Разные типы начальных значений

#### Примитивы

```jsx
const [name, setName] = useState(""); // строка
const [count, setCount] = useState(0); // число
const [isActive, setIsActive] = useState(false); // булево
```

#### Объекты

```jsx
const [user, setUser] = useState({ name: "", email: "" });

// Обновление
setUser({ ...user, name: "Алекс" });
```

#### Массивы

```jsx
const [items, setItems] = useState([]);

// Добавление
setItems([...items, newItem]);
```

#### Функция (ленивая инициализация)

Если начальное значение вычисляется **дорого**, можно передать функцию:

```jsx
// ❌ Неоптимально (вычисляется при каждом рендере)
const [data, setData] = useState(expensiveCalculation());

// ✅ Оптимально (вычисляется только один раз)
const [data, setData] = useState(() => expensiveCalculation());
```

Функция вызывается **только при первом рендере**.

### Обновление состояния

#### Простое обновление

```jsx
const [count, setCount] = useState(0);

setCount(5); // Устанавливает значение
setCount(count + 1); // Использует текущее значение
```

#### Функциональное обновление

```jsx
const [count, setCount] = useState(0);

setCount((prevCount) => prevCount + 1); // Использует предыдущее значение
```

**Когда использовать функциональную форму:**

- Когда новое значение зависит от старого
- При множественных обновлениях
- В асинхронном коде

### Множественный state

В компоненте может быть **несколько `useState`**:

```jsx
function Form() {
  const [name, setName] = useState("");
  const [email, setEmail] = useState("");
  const [age, setAge] = useState(0);

  return (
    <form>
      <input value={name} onChange={(e) => setName(e.target.value)} />
      <input value={email} onChange={(e) => setEmail(e.target.value)} />
      <input value={age} onChange={(e) => setAge(Number(e.target.value))} />
    </form>
  );
}
```

### Объект state

Можно хранить несколько значений в **одном объекте**:

```jsx
function Form() {
  const [form, setForm] = useState({
    name: "",
    email: "",
    age: 0,
  });

  const updateField = (field, value) => {
    setForm({ ...form, [field]: value });
  };

  return (
    <form>
      <input
        value={form.name}
        onChange={(e) => updateField("name", e.target.value)}
      />
    </form>
  );
}
```

### useState vs переменные

**Обычная переменная** не вызывает ре-рендер:

```jsx
function Component() {
  let count = 0; // ❌ Не вызовет ре-рендер

  return <button onClick={() => count++}>{count} // Не обновится!</button>;
}
```

**useState** вызывает ре-рендер:

```jsx
function Component() {
  const [count, setCount] = useState(0); // ✅ Вызовет ре-рендер

  return (
    <button onClick={() => setCount(count + 1)}>{count} // Обновится!</button>
  );
}
```

### Визуальная аналогия

`useState` — как **ящик с данными**:

- `useState(0)` — создаёт ящик с начальным значением `0`
- `count` — текущее содержимое ящика
- `setCount(5)` — кладёт новое значение `5` в ящик
- Когда содержимое меняется, React обновляет интерфейс

### Итоги

- `useState` — хук для добавления состояния в функциональные компоненты
- Возвращает текущее значение и функцию для обновления
- Может принимать начальное значение или функцию для ленивой инициализации
- Обновление состояния вызывает ре-рендер компонента
- Можно использовать несколько `useState` в одном компоненте

---

## 5. Как работает useState под капотом (в общих чертах)

Понимание того, как работает `useState` под капотом, помогает лучше понимать React и избегать ошибок.

### Упрощённое объяснение

React хранит состояние **вне компонента** и использует **порядок вызовов хуков** для сопоставления состояния с компонентами.

### Как React хранит состояние?

React поддерживает **внутренний список** (linked list) всех хуков для каждого компонента:

```
Компонент → [hook1, hook2, hook3, ...]
```

При каждом рендере React проходит по списку хуков в том же порядке.

### Пример работы

```jsx
function Component() {
  const [count, setCount] = useState(0); // hook1
  const [name, setName] = useState(""); // hook2

  return <div>...</div>;
}
```

**Что происходит:**

1. **Первый рендер:**

   - React создаёт список: `[hook1, hook2]`
   - `useState(0)` возвращает `[0, setCount]`
   - `useState('')` возвращает `['', setName]`

2. **Обновление состояния (setCount(5)):**

   - React находит соответствующий hook в списке
   - Обновляет значение
   - Запланирует ре-рендер

3. **Второй рендер:**
   - React проходит по списку в том же порядке
   - `useState(0)` возвращает `[5, setCount]` (новое значение)
   - `useState('')` возвращает `['', setName]` (то же значение)

### Почему порядок важен?

React **полагается на порядок** вызовов хуков:

```jsx
// ❌ Неправильно
function Component({ condition }) {
  if (condition) {
    const [count, setCount] = useState(0); // hook1
  }
  const [name, setName] = useState(""); // hook2

  // При condition = true:  [hook1, hook2]
  // При condition = false: [hook2]  ← порядок изменился!
  // React запутается!
}
```

```jsx
// ✅ Правильно
function Component({ condition }) {
  const [count, setCount] = useState(0); // hook1 (всегда)
  const [name, setName] = useState(""); // hook2 (всегда)

  // Порядок всегда одинаковый: [hook1, hook2]
}
```

### Упрощённая реализация (псевдокод)

```js
let hooks = [];
let currentHook = 0;

function useState(initialValue) {
  const hookIndex = currentHook;
  currentHook++;

  // Первый рендер
  if (!hooks[hookIndex]) {
    hooks[hookIndex] = initialValue;
  }

  const setState = (newValue) => {
    hooks[hookIndex] = newValue;
    rerender(); // Запланировать ре-рендер
  };

  return [hooks[hookIndex], setState];
}

function rerender() {
  currentHook = 0; // Сбросить индекс перед рендером
  // Вызвать компонент снова
}
```

**Важно:** это **упрощённая** версия. Реальная реализация намного сложнее.

### Зачем это знать?

Понимание работы `useState` помогает:

1. **Понять правила хуков** — почему нельзя вызывать в условиях
2. **Избежать ошибок** — почему порядок важен
3. **Оптимизировать** — понимать, когда происходит ре-рендер

### Батчинг обновлений

React **группирует обновления** для производительности:

```jsx
function Component() {
  const [count, setCount] = useState(0);
  const [name, setName] = useState("");

  const update = () => {
    setCount(5);
    setName("Алекс");
    // Оба обновления группируются — только один ре-рендер
  };
}
```

### Итоги

- React хранит состояние вне компонента в списке хуков
- Порядок вызовов хуков критичен — React полагается на него
- При каждом рендере React проходит по списку в том же порядке
- React группирует обновления для производительности
- Понимание работы помогает избежать ошибок

---

## 6. Что такое useEffect

`useEffect` — это хук React, который позволяет выполнять **побочные эффекты** (side effects) в функциональных компонентах.

### Что такое побочные эффекты?

Побочные эффекты — это действия, которые **выходят за рамки рендеринга**:

- Запросы к API
- Подписки на события
- Изменение DOM вручную
- Таймеры
- Логирование

### Базовый синтаксис

```jsx
import { useEffect } from "react";

useEffect(() => {
  // Побочный эффект
}, [dependencies]);
```

### Простейший пример

```jsx
function Component() {
  useEffect(() => {
    console.log("Компонент отрендерился");
  });

  return <div>Компонент</div>;
}
```

Эффект выполнится **после каждого рендера**.

### Загрузка данных

```jsx
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    fetch(`/api/users/${userId}`)
      .then((res) => res.json())
      .then((data) => setUser(data));
  }, [userId]);

  if (!user) return <div>Загрузка...</div>;
  return <div>{user.name}</div>;
}
```

### Когда выполняется useEffect?

`useEffect` выполняется **после рендера** компонента (после того, как изменения применены к DOM).

```
Рендер компонента
    ↓
Обновление DOM
    ↓
useEffect выполняется
```

### Аналогия с методами жизненного цикла

`useEffect` объединяет функциональность нескольких методов жизненного цикла классовых компонентов:

- `componentDidMount` — первый рендер
- `componentDidUpdate` — обновление
- `componentWillUnmount` — размонтирование

### Разные варианты использования

#### 1. Без зависимостей — выполняется при каждом рендере

```jsx
useEffect(() => {
  console.log("Рендер произошёл");
});
```

#### 2. С пустым массивом — только при монтировании

```jsx
useEffect(() => {
  console.log("Компонент смонтирован");
}, []);
```

#### 3. С зависимостями — при изменении зависимостей

```jsx
useEffect(() => {
  console.log("userId изменился");
}, [userId]);
```

### Примеры использования

#### Подписка на события

```jsx
function Component() {
  useEffect(() => {
    const handleResize = () => {
      console.log("Окно изменило размер");
    };

    window.addEventListener("resize", handleResize);

    // Cleanup функция
    return () => {
      window.removeEventListener("resize", handleResize);
    };
  }, []);

  return <div>Компонент</div>;
}
```

#### Таймер

```jsx
function Timer() {
  const [seconds, setSeconds] = useState(0);

  useEffect(() => {
    const interval = setInterval(() => {
      setSeconds((prev) => prev + 1);
    }, 1000);

    return () => clearInterval(interval);
  }, []);

  return <div>{seconds} секунд</div>;
}
```

#### Изменение заголовка страницы

```jsx
function Page({ title }) {
  useEffect(() => {
    document.title = title;
  }, [title]);

  return <div>Страница</div>;
}
```

### Визуальная аналогия

`useEffect` — как **инструкция "сделай что-то после рендера"**:

- Рендер — как сборка мебели
- `useEffect` — как инструкция "после сборки включи лампу, поставь книгу на полку"

### Итоги

- `useEffect` — хук для побочных эффектов в функциональных компонентах
- Выполняется после рендера компонента
- Может выполнять запросы, подписки, таймеры и т.д.
- Заменяет методы жизненного цикла классовых компонентов
- Может иметь зависимости для контроля выполнения

---

## 7. Когда вызывается useEffect

Понимание того, когда вызывается `useEffect`, критично для правильной работы с побочными эффектами.

### Базовое правило

`useEffect` вызывается **после рендера** компонента, после того, как изменения применены к DOM.

### Схема выполнения

```
1. Компонент рендерится
   ↓
2. React обновляет DOM
   ↓
3. useEffect выполняется
```

### Варианты выполнения

#### 1. При каждом рендере (без зависимостей)

```jsx
function Component() {
  useEffect(() => {
    console.log("Эффект выполнен");
  }); // Нет массива зависимостей

  return <div>Компонент</div>;
}
```

**Когда выполняется:**

- При первом рендере (монтирование)
- При каждом обновлении компонента

#### 2. Только при монтировании (пустой массив зависимостей)

```jsx
function Component() {
  useEffect(() => {
    console.log("Эффект выполнен один раз");
  }, []); // Пустой массив

  return <div>Компонент</div>;
}
```

**Когда выполняется:**

- Только при первом рендере (монтирование)
- Не выполняется при обновлениях

#### 3. При изменении зависимостей

```jsx
function Component({ userId }) {
  useEffect(() => {
    console.log("Эффект выполнен для userId:", userId);
  }, [userId]); // Зависимость

  return <div>Компонент</div>;
}
```

**Когда выполняется:**

- При первом рендере
- При изменении `userId`

### Примеры

#### Загрузка данных при монтировании

```jsx
function UserProfile() {
  const [user, setUser] = useState(null);

  useEffect(() => {
    fetchUser().then(setUser);
  }, []); // Только при монтировании

  return <div>{user?.name}</div>;
}
```

#### Загрузка данных при изменении ID

```jsx
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    fetchUser(userId).then(setUser);
  }, [userId]); // При изменении userId

  return <div>{user?.name}</div>;
}
```

#### Логирование при каждом рендере

```jsx
function Component({ data }) {
  useEffect(() => {
    console.log("Данные:", data);
  }); // При каждом рендере

  return <div>{data}</div>;
}
```

### Порядок выполнения

Если в компоненте **несколько `useEffect`**, они выполняются **в порядке объявления**:

```jsx
function Component() {
  useEffect(() => {
    console.log("Эффект 1");
  });

  useEffect(() => {
    console.log("Эффект 2");
  });

  useEffect(() => {
    console.log("Эффект 3");
  });

  // Вывод: Эффект 1, Эффект 2, Эффект 3
}
```

### Cleanup функции

Cleanup функции выполняются **перед следующим выполнением эффекта** или **при размонтировании**:

```jsx
function Component() {
  useEffect(() => {
    const timer = setInterval(() => {
      console.log("Тик");
    }, 1000);

    return () => {
      clearInterval(timer); // Выполнится перед следующим эффектом или при размонтировании
    };
  }, []);
}
```

### Сравнение с методами жизненного цикла

| Метод класса           | useEffect эквивалент                                          |
| ---------------------- | ------------------------------------------------------------- |
| `componentDidMount`    | `useEffect(() => {...}, [])`                                  |
| `componentDidUpdate`   | `useEffect(() => {...})` или `useEffect(() => {...}, [deps])` |
| `componentWillUnmount` | `useEffect(() => { return () => {...} }, [])`                 |

### ⚠️ Частая ошибка

Забывают зависимости:

```jsx
// ❌ Неправильно
function Component({ userId }) {
  useEffect(() => {
    fetchUser(userId); // Использует userId, но не в зависимостях
  }, []); // Эффект выполнится только один раз с начальным userId

  return <div>...</div>;
}

// ✅ Правильно
function Component({ userId }) {
  useEffect(() => {
    fetchUser(userId);
  }, [userId]); // Эффект выполнится при изменении userId
}
```

### Итоги

- `useEffect` вызывается после рендера компонента
- Без зависимостей — при каждом рендере
- С пустым массивом — только при монтировании
- С зависимостями — при изменении зависимостей
- Cleanup функции выполняются перед следующим эффектом или при размонтировании

---

## 8. Разница между useEffect([], [deps], без deps)

В `useEffect` есть три основных варианта использования, которые отличаются по частоте выполнения.

### Три варианта

1. **Без массива зависимостей** — `useEffect(() => {...})`
2. **С пустым массивом** — `useEffect(() => {...}, [])`
3. **С зависимостями** — `useEffect(() => {...}, [dep1, dep2])`

### 1. Без массива зависимостей

```jsx
useEffect(() => {
  console.log("Эффект выполнен");
});
```

**Когда выполняется:**

- ✅ При первом рендере (монтирование)
- ✅ При каждом обновлении компонента
- ✅ При изменении любого состояния или props

**Использование:**

- Редко (может привести к проблемам с производительностью)
- Когда эффект должен выполняться при каждом изменении

```jsx
function Component({ data }) {
  useEffect(() => {
    // Выполнится при каждом рендере
    console.log("Данные обновились:", data);
  });

  return <div>{data}</div>;
}
```

### 2. С пустым массивом зависимостей

```jsx
useEffect(() => {
  console.log("Эффект выполнен");
}, []);
```

**Когда выполняется:**

- ✅ Только при первом рендере (монтирование)
- ❌ НЕ выполняется при обновлениях

**Использование:**

- Загрузка данных при монтировании
- Подписки, которые нужны только один раз
- Инициализация

```jsx
function Component() {
  useEffect(() => {
    // Выполнится только один раз при монтировании
    fetchInitialData();
  }, []);

  return <div>Компонент</div>;
}
```

### 3. С зависимостями

```jsx
useEffect(() => {
  console.log("Эффект выполнен");
}, [userId, filter]);
```

**Когда выполняется:**

- ✅ При первом рендере
- ✅ При изменении любой из зависимостей (`userId` или `filter`)
- ❌ НЕ выполняется при изменении других значений

**Использование:**

- Загрузка данных при изменении ID
- Реакция на изменения конкретных значений
- Самый частый вариант использования

```jsx
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    // Выполнится при первом рендере и при изменении userId
    fetchUser(userId).then(setUser);
  }, [userId]);

  return <div>{user?.name}</div>;
}
```

### Сравнительная таблица

| Вариант     | Когда выполняется          | Пример использования               |
| ----------- | -------------------------- | ---------------------------------- |
| Без массива | При каждом рендере         | Редко (логирование, синхронизация) |
| `[]`        | Только при монтировании    | Загрузка данных, подписки          |
| `[deps]`    | При изменении зависимостей | Реакция на изменения               |

### Примеры различий

#### Пример 1: Счётчик

```jsx
function Component() {
  const [count, setCount] = useState(0);

  // Вариант 1: Без массива
  useEffect(() => {
    console.log("Рендер (без массива):", count);
  });

  // Вариант 2: Пустой массив
  useEffect(() => {
    console.log("Монтирование (пустой массив):", count);
  }, []);

  // Вариант 3: С зависимостью
  useEffect(() => {
    console.log("Изменение count (с зависимостью):", count);
  }, [count]);

  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}

// При клике:
// "Рендер (без массива): 1"        ← выполнится
// "Изменение count (с зависимостью): 1"  ← выполнится
// "Монтирование (пустой массив): 0"  ← НЕ выполнится
```

#### Пример 2: Загрузка данных

```jsx
function UserProfile({ userId }) {
  // ❌ Неправильно — выполнится при каждом рендере
  useEffect(() => {
    fetchUser(userId);
  });

  // ✅ Правильно — только при монтировании (если userId не меняется)
  useEffect(() => {
    fetchUser(userId);
  }, []);

  // ✅ Правильно — при изменении userId
  useEffect(() => {
    fetchUser(userId);
  }, [userId]);
}
```

### Важно: зависимости

Если эффект использует значения из компонента, они должны быть в массиве зависимостей:

```jsx
function Component({ userId }) {
  const [data, setData] = useState(null);

  // ❌ Неправильно — может использовать устаревшее значение userId
  useEffect(() => {
    fetchData(userId);
  }, []); // userId не в зависимостях!

  // ✅ Правильно
  useEffect(() => {
    fetchData(userId);
  }, [userId]);
}
```

### ⚠️ Частая ошибка

Забывают зависимости:

```jsx
function Component({ userId }) {
  useEffect(() => {
    console.log(userId); // Использует userId
  }, []); // ❌ userId не в зависимостях!
}
```

ESLint правило `exhaustive-deps` предупредит об этом.

### Итоги

- Без массива — при каждом рендере (редко используется)
- Пустой массив `[]` — только при монтировании
- С зависимостями `[deps]` — при изменении зависимостей (самый частый вариант)
- Все используемые значения должны быть в зависимостях
- Используй ESLint для проверки зависимостей

---

## 922222. Cleanup-функция в useEffect

Cleanup-функция (функция очистки) в `useEffect` позволяет **очистить ресурсы** перед следующим выполнением эффекта или при размонтировании компонента.

### Что такое cleanup-функция?

Cleanup-функция — это функция, которую возвращает эффект. Она выполняется:

1. **Перед следующим выполнением эффекта** (если есть зависимости)
2. **При размонтировании компонента** (когда компонент удаляется из DOM)

### Синтаксис

```jsx
useEffect(() => {
  // Эффект

  return () => {
    // Cleanup-функция
  };
}, [dependencies]);
```

### Когда нужна cleanup-функция?

Cleanup нужна для:

- Отписки от событий
- Очистки таймеров
- Отмены запросов
- Закрытия соединений

### Примеры

#### Подписка на события

```jsx
function Component() {
  useEffect(() => {
    const handleResize = () => {
      console.log("Окно изменило размер");
    };

    window.addEventListener("resize", handleResize);

    // Cleanup: отписка от события
    return () => {
      window.removeEventListener("resize", handleResize);
    };
  }, []);

  return <div>Компонент</div>;
}
```

Без cleanup-функции подписка останется активной даже после размонтирования компонента, что приведёт к утечке памяти.

#### Таймеры

```jsx
function Timer() {
  const [seconds, setSeconds] = useState(0);

  useEffect(() => {
    const interval = setInterval(() => {
      setSeconds((prev) => prev + 1);
    }, 1000);

    // Cleanup: очистка интервала
    return () => {
      clearInterval(interval);
    };
  }, []);

  return <div>{seconds} секунд</div>;
}
```

Без cleanup-функции таймер продолжит работать после размонтирования.

#### Запросы к API

```jsx
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    let cancelled = false;

    fetchUser(userId).then((data) => {
      if (!cancelled) {
        setUser(data);
      }
    });

    // Cleanup: отмена запроса
    return () => {
      cancelled = true;
    };
  }, [userId]);

  return <div>{user?.name}</div>;
}
```

Если компонент размонтируется или `userId` изменится до завершения запроса, состояние не обновится.

### Когда выполняется cleanup?

#### С пустым массивом зависимостей

```jsx
useEffect(() => {
  console.log("Эффект выполнен");

  return () => {
    console.log("Cleanup выполнен");
  };
}, []); // Пустой массив
```

**Выполнение:**

- Эффект: при монтировании
- Cleanup: при размонтировании

#### С зависимостями

```jsx
useEffect(() => {
  console.log("Эффект выполнен для userId:", userId);

  return () => {
    console.log("Cleanup для userId:", userId);
  };
}, [userId]);
```

**Выполнение:**

1. Монтирование: эффект для `userId = 1`
2. Изменение `userId` на `2`:
   - Cleanup для `userId = 1`
   - Эффект для `userId = 2`
3. Размонтирование: Cleanup для `userId = 2`

### Порядок выполнения

```jsx
function Component() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    console.log("Эффект:", count);

    return () => {
      console.log("Cleanup:", count);
    };
  }, [count]);

  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}

// Клик 1:
// Эффект: 0 (первый рендер)
// [клик]
// Cleanup: 0
// Эффект: 1

// Клик 2:
// Cleanup: 1
// Эффект: 2
```

**Важно:** cleanup получает значение из предыдущего рендера.

### Несколько cleanup функций

Если в компоненте несколько эффектов, каждый может иметь свою cleanup-функцию:

```jsx
function Component() {
  useEffect(() => {
    const timer1 = setInterval(() => {}, 1000);
    return () => clearInterval(timer1);
  }, []);

  useEffect(() => {
    const timer2 = setInterval(() => {}, 2000);
    return () => clearInterval(timer2);
  }, []);
}
```

### Когда cleanup НЕ нужна?

Cleanup не нужна для:

- Простых операций (логирование, изменение DOM через React)
- Операций, которые не создают ресурсы

```jsx
// Cleanup не нужна
useEffect(() => {
  console.log("Компонент отрендерился");
}, []);

// Cleanup не нужна (React сам управляет DOM)
useEffect(() => {
  document.title = "Новый заголовок";
}, []);
```

### ⚠️ Частая ошибка

Забывают cleanup для подписок и таймеров:

```jsx
// ❌ Неправильно — утечка памяти
function Component() {
  useEffect(() => {
    window.addEventListener("resize", handleResize);
    // Нет cleanup!
  }, []);
}

// ✅ Правильно
function Component() {
  useEffect(() => {
    window.addEventListener("resize", handleResize);
    return () => {
      window.removeEventListener("resize", handleResize);
    };
  }, []);
}
```

### Итоги

- Cleanup-функция возвращается из эффекта
- Выполняется перед следующим эффектом или при размонтировании
- Нужна для очистки ресурсов (подписки, таймеры, запросы)
- Предотвращает утечки памяти
- Cleanup получает значения из предыдущего рендера

---

## 10. Почему нельзя делать useEffect async

Нельзя делать саму функцию `useEffect` асинхронной (async). Это приведёт к ошибке, потому что `useEffect` должен возвращать либо `undefined`, либо cleanup-функцию, а async-функция всегда возвращает Promise.

### Проблема

```jsx
// ❌ Неправильно — ошибка!
useEffect(async () => {
  const data = await fetchData();
  setData(data);
}, []);
```

**Ошибка:** `useEffect` не может возвращать Promise. Async-функция всегда возвращает Promise.

### Почему это ошибка?

`useEffect` ожидает:

- `undefined` — если cleanup не нужна
- Функцию — если нужна cleanup

Async-функция всегда возвращает Promise, что нарушает контракт `useEffect`.

### Правильное решение

Создай **внутреннюю async-функцию** и вызови её:

```jsx
// ✅ Правильно
useEffect(() => {
  async function fetchData() {
    const data = await fetchData();
    setData(data);
  }

  fetchData();
}, []);
```

Или используй **стрелочную функцию**:

```jsx
// ✅ Правильно
useEffect(() => {
  const fetchData = async () => {
    const data = await fetchData();
    setData(data);
  };

  fetchData();
}, []);
```

### Обработка ошибок

Не забудь обработать ошибки:

```jsx
useEffect(() => {
  async function fetchData() {
    try {
      const data = await fetchData();
      setData(data);
    } catch (error) {
      setError(error);
    }
  }

  fetchData();
}, []);
```

### Cleanup с async

Если нужна cleanup-функция, она **не должна быть async**, но может отменять async-операции:

```jsx
useEffect(() => {
  let cancelled = false;

  async function fetchData() {
    const data = await fetchData();
    if (!cancelled) {
      setData(data);
    }
  }

  fetchData();

  return () => {
    cancelled = true; // Cleanup не async
  };
}, []);
```

### Альтернатива: .then()

Можно использовать `.then()` вместо async/await:

```jsx
useEffect(() => {
  fetchData()
    .then((data) => setData(data))
    .catch((error) => setError(error));
}, []);
```

### Пример полного кода

```jsx
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    let cancelled = false;

    async function fetchUser() {
      try {
        setLoading(true);
        const data = await fetchUser(userId);
        if (!cancelled) {
          setUser(data);
          setError(null);
        }
      } catch (err) {
        if (!cancelled) {
          setError(err);
        }
      } finally {
        if (!cancelled) {
          setLoading(false);
        }
      }
    }

    fetchUser();

    return () => {
      cancelled = true;
    };
  }, [userId]);

  if (loading) return <div>Загрузка...</div>;
  if (error) return <div>Ошибка: {error.message}</div>;
  return <div>{user.name}</div>;
}
```

### Почему это важно?

- `useEffect` должен возвращать `undefined` или функцию
- Async-функция всегда возвращает Promise
- Это нарушает контракт React
- Внутренняя async-функция решает проблему

### ⚠️ Частая ошибка

Делают useEffect async:

```jsx
// ❌ Неправильно
useEffect(async () => {
  const data = await fetchData();
  setData(data);
}, []);

// ✅ Правильно
useEffect(() => {
  async function fetchData() {
    const data = await fetchData();
    setData(data);
  }
  fetchData();
}, []);
```

### Итоги

- Нельзя делать `useEffect` async
- Async-функция возвращает Promise, а `useEffect` ожидает функцию или `undefined`
- Создай внутреннюю async-функцию и вызови её
- Cleanup-функция не должна быть async
- Используй флаги отмены для async-операций в cleanup

---

## 11. Что такое dependency array и зачем он нужен

Dependency array (массив зависимостей) — это второй параметр `useEffect`, который определяет, **когда эффект должен выполняться**.

### Синтаксис

```jsx
useEffect(() => {
  // Эффект
}, [dependency1, dependency2, ...]);  // ← Dependency array
```

### Зачем нужен dependency array?

Dependency array контролирует **когда выполняется эффект**:

- Без массива — при каждом рендере
- Пустой массив `[]` — только при монтировании
- С зависимостями `[dep1, dep2]` — при изменении зависимостей

### Что должно быть в dependency array?

В массив зависимостей нужно включать **все значения из области видимости компонента**, которые используются в эффекте:

- Props
- State
- Переменные из компонента

### Примеры

#### Без dependency array

```jsx
useEffect(() => {
  console.log("Эффект выполнен");
}); // Нет массива — выполнится при каждом рендере
```

#### С пустым массивом

```jsx
useEffect(() => {
  console.log("Эффект выполнен");
}, []); // Пустой массив — только при монтировании
```

#### С зависимостями

```jsx
function Component({ userId }) {
  const [filter, setFilter] = useState("all");

  useEffect(() => {
    fetchData(userId, filter);
  }, [userId, filter]); // Эффект выполнится при изменении userId или filter
}
```

### Что включать в зависимости?

#### ✅ Включай

- Props, которые используются в эффекте
- State, которые используются в эффекте
- Переменные из компонента

```jsx
function Component({ userId }) {
  const [page, setPage] = useState(1);

  useEffect(() => {
    fetchData(userId, page); // Использует userId и page
  }, [userId, page]); // ✅ Оба в зависимостях
}
```

#### ❌ Не включай

- Функции setState (стабильные)
- Значения, которые не используются в эффекте
- Константы

```jsx
function Component({ userId }) {
  const [count, setCount] = useState(0);
  const MAX_ITEMS = 10; // Константа

  useEffect(() => {
    fetchData(userId); // Использует только userId
    setCount(5); // setCount стабильна
  }, [userId]); // ✅ Только userId

  // setCount и MAX_ITEMS не нужны в зависимостях
}
```

### Примеры ошибок

#### Забыли зависимость

```jsx
function Component({ userId }) {
  useEffect(() => {
    fetchData(userId); // Использует userId
  }, []); // ❌ userId не в зависимостях!
}
```

**Проблема:** эффект выполнится только один раз с начальным значением `userId`.

#### Лишние зависимости

```jsx
function Component({ userId }) {
  const [unrelated, setUnrelated] = useState(0);

  useEffect(() => {
    fetchData(userId); // Использует только userId
  }, [userId, unrelated]); // ⚠️ unrelated не нужен
}
```

**Проблема:** эффект будет выполняться лишний раз при изменении `unrelated`.

### ESLint правило

React предоставляет ESLint правило `exhaustive-deps`, которое **автоматически проверяет** зависимости:

```json
{
  "rules": {
    "react-hooks/exhaustive-deps": "warn"
  }
}
```

Это правило предупредит, если зависимость пропущена.

### Функции в зависимостях

Если функция используется в эффекте, она должна быть в зависимостях:

```jsx
function Component({ userId }) {
  const fetchData = async (id) => {
    // ...
  };

  useEffect(() => {
    fetchData(userId);
  }, [userId, fetchData]); // fetchData в зависимостях
}
```

**Проблема:** `fetchData` создаётся заново при каждом рендере, что вызовет лишние выполнения эффекта.

**Решение:** используй `useCallback`:

```jsx
function Component({ userId }) {
  const fetchData = useCallback(async (id) => {
    // ...
  }, []); // Зависимости fetchData

  useEffect(() => {
    fetchData(userId);
  }, [userId, fetchData]);
}
```

### Объекты и массивы в зависимостях

Объекты и массивы сравниваются по **ссылке**, а не по содержимому:

```jsx
function Component({ user }) {
  useEffect(() => {
    fetchData(user.id);
  }, [user]); // Эффект выполнится при каждом рендере, если user — новый объект
}
```

**Решение:** используй примитивные значения:

```jsx
function Component({ user }) {
  useEffect(() => {
    fetchData(user.id);
  }, [user.id]); // ✅ Только id в зависимостях
}
```

### Итоги

- Dependency array контролирует, когда выполняется эффект
- Включай все используемые значения (props, state, переменные)
- Не включай setState функции, константы, неиспользуемые значения
- Используй ESLint правило `exhaustive-deps` для проверки
- Для функций используй `useCallback`
- Для объектов/массивов используй примитивные значения

---
