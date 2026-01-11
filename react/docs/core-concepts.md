# React Architecture

Базовые архитектурные принципы React и подходы к организации логики.  
Материал представлен в формате **вопрос–ответ**.

1. [Поднятие состояния (lifting state up)](#1-поднятие-состояния-lifting-state-up)
2. [Однонаправленный поток данных](#2-однонаправленный-поток-данных)
3. [Что такое prop drilling](#3-что-такое-prop-drilling)
4. [Когда стоит выносить логику в хук](#4-когда-стоит-выносить-логику-в-хук)
5. [Что такое custom hook](#5-что-такое-custom-hook)

## 1. Поднятие состояния (lifting state up)

Поднятие состояния (lifting state up) — это техника перемещения state из дочерних компонентов в их **общего родителя**, чтобы несколько компонентов могли использовать одни и те же данные.

### Проблема без поднятия состояния

Когда два компонента должны использовать одни и те же данные, но state находится в одном из них:

```jsx
function TemperatureInput() {
  const [temperature, setTemperature] = useState("");

  return (
    <input
      value={temperature}
      onChange={(e) => setTemperature(e.target.value)}
    />
  );
}

function App() {
  return (
    <div>
      <TemperatureInput /> {/* Celsius */}
      <TemperatureInput /> {/* Fahrenheit */}
      {/* Как синхронизировать эти два input? */}
    </div>
  );
}
```

**Проблема:** два input независимы, нельзя синхронизировать их значения.

### Решение: поднять состояние

Переносим state в общего родителя:

```jsx
function App() {
  // State в родителе
  const [temperature, setTemperature] = useState("");

  return (
    <div>
      <TemperatureInput
        temperature={temperature}
        onTemperatureChange={setTemperature}
      />
      <TemperatureInput
        temperature={temperature}
        onTemperatureChange={setTemperature}
      />
    </div>
  );
}

function TemperatureInput({ temperature, onTemperatureChange }) {
  return (
    <input
      value={temperature}
      onChange={(e) => onTemperatureChange(e.target.value)}
    />
  );
}
```

Теперь оба input используют одно и то же состояние.

### Когда использовать?

Поднимай состояние, когда:

- **Два или более компонента** используют одни и те же данные
- **Дочерний компонент** должен изменить данные родителя
- **Несколько компонентов** должны быть синхронизированы

### Пример: форма с валидацией

```jsx
function App() {
  const [formData, setFormData] = useState({
    name: "",
    email: "",
  });
  const [errors, setErrors] = useState({});

  const handleChange = (field, value) => {
    setFormData({ ...formData, [field]: value });
    // Валидация
    if (field === "email" && value && !value.includes("@")) {
      setErrors({ ...errors, email: "Неверный email" });
    } else {
      setErrors({ ...errors, [field]: "" });
    }
  };

  return (
    <form>
      <InputField
        label="Имя"
        value={formData.name}
        onChange={(value) => handleChange("name", value)}
        error={errors.name}
      />
      <InputField
        label="Email"
        value={formData.email}
        onChange={(value) => handleChange("email", value)}
        error={errors.email}
      />
    </form>
  );
}

function InputField({ label, value, onChange, error }) {
  return (
    <div>
      <label>{label}</label>
      <input value={value} onChange={(e) => onChange(e.target.value)} />
      {error && <div style={{ color: "red" }}>{error}</div>}
    </div>
  );
}
```

### Пример: счётчик с синхронизацией

```jsx
function App() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <CounterDisplay count={count} />
      <CounterButton count={count} onIncrement={() => setCount(count + 1)} />
      <CounterButton count={count} onIncrement={() => setCount(count + 1)} />
    </div>
  );
}

function CounterDisplay({ count }) {
  return <div>Счёт: {count}</div>;
}

function CounterButton({ count, onIncrement }) {
  return <button onClick={onIncrement}>Увеличить ({count})</button>;
}
```

### Передача функций для обновления

Обычно передают функцию для обновления state:

```jsx
function App() {
  const [value, setValue] = useState("");

  return (
    <ChildComponent
      value={value}
      onChange={setValue} // Передаём функцию обновления
    />
  );
}

function ChildComponent({ value, onChange }) {
  return <input value={value} onChange={(e) => onChange(e.target.value)} />;
}
```

### Визуальная аналогия

Поднятие состояния — как **центральный склад**:

- Вместо того чтобы хранить товары в разных магазинах (локальный state)
- Хранишь их в одном складе (родительский компонент)
- Все магазины (дочерние компоненты) могут получить товары из одного склада

### Когда НЕ нужно поднимать состояние

Не поднимай состояние, если:

- **Только один компонент** использует данные
- **Данные локальны** и не нужны другим компонентам
- **Поднятие усложняет** код без необходимости

```jsx
// ❌ Не нужно поднимать — используется только здесь
function Button() {
  const [isHovered, setIsHovered] = useState(false);
  return (
    <button
      onMouseEnter={() => setIsHovered(true)}
      onMouseLeave={() => setIsHovered(false)}
    >
      {isHovered ? "Наведено" : "Наведи"}
    </button>
  );
}
```

### ⚠️ Частая ошибка

Пытаются синхронизировать компоненты через общий state, не поднимая его:

```jsx
// ❌ Неправильно — каждый компонент имеет свой state
function Component1() {
  const [value, setValue] = useState("");
  return <input value={value} onChange={(e) => setValue(e.target.value)} />;
}

function Component2() {
  const [value, setValue] = useState("");
  return <input value={value} onChange={(e) => setValue(e.target.value)} />;
}
// Эти компоненты не синхронизированы

// ✅ Правильно — поднимаем state
function App() {
  const [value, setValue] = useState("");
  return (
    <div>
      <Component1 value={value} onChange={setValue} />
      <Component2 value={value} onChange={setValue} />
    </div>
  );
}
```

### Итоги

- Поднятие состояния — перенос state из дочерних компонентов в родителя
- Используй, когда несколько компонентов должны использовать одни данные
- Передавай state и функцию обновления через props
- Не поднимай состояние, если оно нужно только одному компоненту
- Это основа однонаправленного потока данных в React

---

## 2. Однонаправленный поток данных

Однонаправленный поток данных (unidirectional data flow) — это принцип React, согласно которому данные **текут в одном направлении**: от родительских компонентов к дочерним через props.

### Как работает однонаправленный поток?

```jsx
function App() {
  const [count, setCount] = useState(0); // Источник данных

  return <Parent count={count} onIncrement={() => setCount(count + 1)} />;
}

function Parent({ count, onIncrement }) {
  return (
    <div>
      <Child count={count} onIncrement={onIncrement} />
    </div>
  );
}

function Child({ count, onIncrement }) {
  return (
    <button onClick={onIncrement}>
      Count: {count} {/* Данные пришли сверху */}
    </button>
  );
}
```

**Направление данных:**

1. `App` → `Parent` (через props)
2. `Parent` → `Child` (через props)
3. Изменения идут вверх через функции (`onIncrement`)

### Визуальная схема

```
┌─────────┐
│   App   │  ← Источник данных (state)
└────┬────┘
     │ props: count, onIncrement
     ▼
┌─────────┐
│ Parent  │
└────┬────┘
     │ props: count, onIncrement
     ▼
┌─────────┐
│  Child  │
└─────────┘
```

### Данные текут вниз

Данные (props) всегда передаются **вниз** от родителя к потомкам:

```jsx
function App() {
  const user = { name: 'Алекс', age: 25 };

  return (
    <UserProfile user={user} />  {/* Передаём данные вниз */}
  );
}

function UserProfile({ user }) {
  return (
    <div>
      <UserName name={user.name} />  {/* Передаём дальше вниз */}
      <UserAge age={user.age} />     {/* Передаём дальше вниз */}
    </div>
  );
}

function UserName({ name }) {
  return <h1>{name}</h1>;  {/* Используем данные */}
}

function UserAge({ age }) {
  return <p>{age} лет</p>;  {/* Используем данные */}
}
```

### События текут вверх

Изменения данных идут **вверх** через функции-колбэки:

```jsx
function App() {
  const [name, setName] = useState('Алекс');

  // Функция для обновления данных
  const handleNameChange = (newName) => {
    setName(newName);  // Обновляем state в родителе
  };

  return (
    <UserProfile
      name={name}
      onNameChange={handleNameChange}  {/* Передаём функцию вниз */}
    />
  );
}

function UserProfile({ name, onNameChange }) {
  return (
    <div>
      <NameInput
        name={name}
        onChange={onNameChange}  {/* Передаём функцию дальше вниз */}
      />
    </div>
  );
}

function NameInput({ name, onChange }) {
  return (
    <input
      value={name}
      onChange={(e) => onChange(e.target.value)}  {/* Вызываем функцию, данные идут вверх */}
    />
  );
}
```

**Поток:**

1. `NameInput` вызывает `onChange` → данные идут в `UserProfile`
2. `UserProfile` вызывает `onNameChange` → данные идут в `App`
3. `App` обновляет state → новые данные текут вниз через props

### Преимущества однонаправленного потока

1. **Предсказуемость** — всегда знаешь, откуда пришли данные
2. **Простота отладки** — легко отследить поток данных
3. **Меньше багов** — нет циклических зависимостей
4. **Легче тестировать** — компоненты получают данные через props

### Пример проблемы с двунаправленным потоком

В других фреймворках (например, Angular 1.x) был двунаправленный биндинг:

```js
// Angular 1.x (пример)
<input ng-model="user.name" />
<div>{{user.name}}</div>
// Изменения в input автоматически обновляют div
// И наоборот — изменения в user.name автоматически обновляют input
```

**Проблемы:**

- Сложно отследить, откуда пришло изменение
- Могут возникать циклические обновления
- Сложнее отлаживать

### React: явный однонаправленный поток

```jsx
function App() {
  const [name, setName] = useState("Алекс");

  return (
    <div>
      <input
        value={name} // Данные вниз
        onChange={(e) => setName(e.target.value)} // Изменения вверх
      />
      <div>{name}</div> {/* Данные вниз */}
    </div>
  );
}
```

**Преимущества:**

- Явно видно, откуда приходят данные (`useState`)
- Явно видно, куда идут изменения (`setName`)
- Легко понять поток данных

### Визуальная аналогия

Однонаправленный поток — как **река**:

- Вода (данные) течёт в одном направлении — вниз
- Если нужно что-то изменить, поднимаешься вверх по течению (функция-колбэк)
- Всегда знаешь, где источник (state в родителе)

Двунаправленный поток — как **лабиринт**:

- Изменения могут прийти откуда угодно
- Сложно понять, откуда пришло изменение
- Могут возникнуть циклы

### ⚠️ Частая ошибка

Пытаются изменять props напрямую:

```jsx
// ❌ Неправильно — мутация props
function Child({ user }) {
  const handleClick = () => {
    user.name = "Мария"; // ❌ Нельзя изменять props!
  };

  return <button onClick={handleClick}>{user.name}</button>;
}

// ✅ Правильно — вызываем функцию для обновления
function Child({ user, onUserChange }) {
  const handleClick = () => {
    onUserChange({ ...user, name: "Мария" }); // Обновляем через функцию
  };

  return <button onClick={handleClick}>{user.name}</button>;
}
```

### Итоги

- Однонаправленный поток данных — данные текут только вниз (через props)
- Изменения данных идут вверх через функции-колбэки
- Это делает код предсказуемым и легче отлаживаемым
- Props неизменяемы — нельзя изменять их напрямую
- Всегда знаешь источник данных (state в родителе)

---

## 3. Что такое prop drilling

Prop drilling — это передача props через **несколько уровней компонентов**, которые не используют эти props, только чтобы передать их дальше вниз к компоненту, которому они действительно нужны.

### Пример prop drilling

```jsx
function App() {
  const [user, setUser] = useState({ name: 'Алекс', theme: 'dark' });

  return (
    <Header user={user} />  {/* Передаём user */}
  );
}

function Header({ user }) {
  // Header не использует user, только передаёт дальше
  return (
    <div>
      <Navigation user={user} />  {/* Передаём user */}
    </div>
  );
}

function Navigation({ user }) {
  // Navigation не использует user, только передаёт дальше
  return (
    <nav>
      <UserMenu user={user} />  {/* Передаём user */}
    </nav>
  );
}

function UserMenu({ user }) {
  // Вот здесь user действительно нужен
  return <div>Привет, {user.name}!</div>;
}
```

**Проблема:** `user` проходит через `Header` и `Navigation`, хотя они его не используют.

### Визуальная схема

```
App (имеет user)
  └── Header (передаёт user, но не использует)
       └── Navigation (передаёт user, но не использует)
            └── UserMenu (использует user) ✅
```

### Почему это проблема?

1. **Лишний код** — компоненты получают props, которые им не нужны
2. **Сложность рефакторинга** — нужно менять много компонентов при изменении структуры
3. **Снижение читаемости** — непонятно, какие props реально используются
4. **Потенциальные ошибки** — легко забыть передать prop на каком-то уровне

### Пример с большим количеством props

```jsx
function App() {
  const [user, setUser] = useState({ name: "Алекс" });
  const [theme, setTheme] = useState("dark");
  const [language, setLanguage] = useState("ru");

  return (
    <Layout
      user={user}
      theme={theme}
      language={language} // Передаём много props
    />
  );
}

function Layout({ user, theme, language }) {
  // Layout не использует эти props
  return (
    <div>
      <Sidebar user={user} theme={theme} language={language} />
      <Content user={user} theme={theme} language={language} />
    </div>
  );
}

function Sidebar({ user, theme, language }) {
  // Sidebar использует только theme
  return <aside style={{ background: theme === "dark" ? "#000" : "#fff" }} />;
}

function Content({ user, theme, language }) {
  // Content использует только user
  return <main>Привет, {user.name}!</main>;
}
```

**Проблема:** `Layout` получает все props, хотя не использует их, только передаёт дальше.

### Решения prop drilling

#### 1. Context API

Используй Context для передачи данных без prop drilling:

```jsx
const UserContext = createContext();

function App() {
  const [user, setUser] = useState({ name: "Алекс" });

  return (
    <UserContext.Provider value={user}>
      <Header /> {/* Не передаём user */}
    </UserContext.Provider>
  );
}

function Header() {
  return <Navigation />;
  {
    /* Не передаём user */
  }
}

function Navigation() {
  return <UserMenu />;
  {
    /* Не передаём user */
  }
}

function UserMenu() {
  const user = useContext(UserContext); // Получаем user из Context
  return <div>Привет, {user.name}!</div>;
}
```

#### 2. Composition (композиция)

Используй композицию компонентов:

```jsx
function App() {
  const [user, setUser] = useState({ name: "Алекс" });

  return (
    <Header>
      <Navigation>
        <UserMenu user={user} /> {/* Передаём user только туда, где нужно */}
      </Navigation>
    </Header>
  );
}

function Header({ children }) {
  return <header>{children}</header>;
}

function Navigation({ children }) {
  return <nav>{children}</nav>;
}

function UserMenu({ user }) {
  return <div>Привет, {user.name}!</div>;
}
```

#### 3. Передача только нужных данных

Разбивай данные и передавай только то, что нужно:

```jsx
function App() {
  const [user, setUser] = useState({ name: "Алекс", theme: "dark" });

  return (
    <Header theme={user.theme}>
      {" "}
      {/* Передаём только theme */}
      <Navigation>
        <UserMenu name={user.name} /> {/* Передаём только name */}
      </Navigation>
    </Header>
  );
}
```

### Когда prop drilling допустим?

Prop drilling допустим, когда:

- **Небольшое количество уровней** (2-3 уровня)
- **Данные используются несколькими компонентами** на разных уровнях
- **Данные не глобальные** (не нужны везде)

```jsx
// ✅ Допустимо — всего 2 уровня, данные нужны только здесь
function Form() {
  const [email, setEmail] = useState("");
  return <FormField email={email} onChange={setEmail} />;
}

function FormField({ email, onChange }) {
  return <Input value={email} onChange={onChange} />;
}
```

### Визуальная аналогия

Prop drilling — как **эстафета**:

- Ты передаёшь палочку (prop) через много бегунов (компонентов)
- Некоторые бегуны просто передают палочку дальше, не используя её
- Наконец, палочка попадает к тому, кому она нужна

Context — как **радиостанция**:

- Ты вещаешь данные на определённой частоте (Context)
- Кто хочет — может настроиться на эту частоту (useContext)
- Не нужно передавать данные через всех

### ⚠️ Частая ошибка

Создают Context для всего, даже когда prop drilling не проблема:

```jsx
// ❌ Избыточно — prop drilling всего 2 уровня
const ThemeContext = createContext();

function App() {
  return (
    <ThemeContext.Provider value="dark">
      <Button />
    </ThemeContext.Provider>
  );
}

function Button() {
  const theme = useContext(ThemeContext);
  return <button className={theme}>Кнопка</button>;
}

// ✅ Проще — обычные props
function App() {
  return <Button theme="dark" />;
}

function Button({ theme }) {
  return <button className={theme}>Кнопка</button>;
}
```

### Итоги

- Prop drilling — передача props через компоненты, которые их не используют
- Это проблема при большом количестве уровней вложенности
- Решения: Context API, композиция компонентов, передача только нужных данных
- Prop drilling допустим для небольшого количества уровней
- Не создавай Context для всего — иногда обычные props проще

---

## 4. Когда стоит выносить логику в хук

Вынесение логики в хук полезно, когда логика **переиспользуется** или когда нужно **разделить ответственность** компонента.

### Когда выносить логику?

#### 1. Переиспользование логики

Если одна и та же логика используется в нескольких компонентах:

```jsx
// ❌ Логика дублируется
function Component1() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `Count: ${count}`;
  }, [count]);

  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}

function Component2() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `Count: ${count}`;
  }, [count]);

  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}

// ✅ Логика в хуке
function useDocumentTitle(count) {
  useEffect(() => {
    document.title = `Count: ${count}`;
  }, [count]);
}

function Component1() {
  const [count, setCount] = useState(0);
  useDocumentTitle(count);
  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}

function Component2() {
  const [count, setCount] = useState(0);
  useDocumentTitle(count);
  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

#### 2. Сложная логика

Если логика сложная и делает компонент слишком большим:

```jsx
// ❌ Компонент слишком большой
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    let cancelled = false;

    async function fetchUser() {
      try {
        setLoading(true);
        const response = await fetch(`/api/users/${userId}`);
        if (!response.ok) throw new Error("Ошибка загрузки");
        const data = await response.json();
        if (!cancelled) {
          setUser(data);
          setError(null);
        }
      } catch (err) {
        if (!cancelled) {
          setError(err.message);
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
  if (error) return <div>Ошибка: {error}</div>;
  if (!user) return null;

  return <div>{user.name}</div>;
}

// ✅ Логика в хуке
function useUser(userId) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    let cancelled = false;

    async function fetchUser() {
      try {
        setLoading(true);
        const response = await fetch(`/api/users/${userId}`);
        if (!response.ok) throw new Error("Ошибка загрузки");
        const data = await response.json();
        if (!cancelled) {
          setUser(data);
          setError(null);
        }
      } catch (err) {
        if (!cancelled) {
          setError(err.message);
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

  return { user, loading, error };
}

function UserProfile({ userId }) {
  const { user, loading, error } = useUser(userId);

  if (loading) return <div>Загрузка...</div>;
  if (error) return <div>Ошибка: {error}</div>;
  if (!user) return null;

  return <div>{user.name}</div>;
}
```

#### 3. Разделение ответственности

Если компонент делает слишком много:

```jsx
// ❌ Компонент делает всё
function TodoApp() {
  const [todos, setTodos] = useState([]);
  const [filter, setFilter] = useState("all");

  // Логика управления todos
  const addTodo = (text) => {
    setTodos([...todos, { id: Date.now(), text, completed: false }]);
  };

  const toggleTodo = (id) => {
    setTodos(
      todos.map((todo) =>
        todo.id === id ? { ...todo, completed: !todo.completed } : todo
      )
    );
  };

  // Логика фильтрации
  const filteredTodos = todos.filter((todo) => {
    if (filter === "active") return !todo.completed;
    if (filter === "completed") return todo.completed;
    return true;
  });

  return <div>{/* ... */}</div>;
}

// ✅ Логика разделена по хукам
function useTodos() {
  const [todos, setTodos] = useState([]);

  const addTodo = (text) => {
    setTodos([...todos, { id: Date.now(), text, completed: false }]);
  };

  const toggleTodo = (id) => {
    setTodos(
      todos.map((todo) =>
        todo.id === id ? { ...todo, completed: !todo.completed } : todo
      )
    );
  };

  return { todos, addTodo, toggleTodo };
}

function useFilteredTodos(todos, filter) {
  return todos.filter((todo) => {
    if (filter === "active") return !todo.completed;
    if (filter === "completed") return todo.completed;
    return true;
  });
}

function TodoApp() {
  const [filter, setFilter] = useState("all");
  const { todos, addTodo, toggleTodo } = useTodos();
  const filteredTodos = useFilteredTodos(todos, filter);

  return <div>{/* ... */}</div>;
}
```

#### 4. Тестирование логики

Хуки легче тестировать отдельно:

```jsx
// ✅ Логику можно протестировать отдельно
function useCounter(initialValue = 0) {
  const [count, setCount] = useState(initialValue);

  const increment = () => setCount(count + 1);
  const decrement = () => setCount(count - 1);
  const reset = () => setCount(initialValue);

  return { count, increment, decrement, reset };
}

// Тест хука отдельно
// test('useCounter increments count', () => { ... })
```

### Когда НЕ нужно выносить логику?

#### 1. Простая, специфичная логика

Если логика простая и используется только в одном месте:

```jsx
// ✅ Оставляем в компоненте — логика простая и специфична
function Button() {
  const [isHovered, setIsHovered] = useState(false);

  return (
    <button
      onMouseEnter={() => setIsHovered(true)}
      onMouseLeave={() => setIsHovered(false)}
    >
      {isHovered ? "Наведено" : "Наведи"}
    </button>
  );
}

// ❌ Избыточно — создавать хук для такой простой логики
function useHover() {
  const [isHovered, setIsHovered] = useState(false);
  return {
    isHovered,
    onMouseEnter: () => setIsHovered(true),
    onMouseLeave: () => setIsHovered(false),
  };
}
```

#### 2. Логика, связанная только с UI

Если логика тесно связана с конкретным UI:

```jsx
// ✅ Оставляем в компоненте
function Modal({ isOpen, onClose }) {
  useEffect(() => {
    if (isOpen) {
      document.body.style.overflow = "hidden";
    } else {
      document.body.style.overflow = "";
    }
  }, [isOpen]);

  if (!isOpen) return null;

  return (
    <div className="modal">
      <button onClick={onClose}>Закрыть</button>
    </div>
  );
}
```

### Принципы вынесения логики

1. **DRY (Don't Repeat Yourself)** — если логика повторяется, вынеси её
2. **Single Responsibility** — один хук — одна ответственность
3. **Читаемость** — компонент должен быть понятным
4. **Тестируемость** — логику легче тестировать в хуке

### Визуальная аналогия

Вынесение логики в хук — как **вынос рецепта в отдельную книгу**:

- Если рецепт нужен для разных блюд (переиспользование) — выносишь в книгу
- Если рецепт сложный и занимает много места — выносишь в книгу
- Если рецепт простой и используется только для одного блюда — оставляешь в заметках

### ⚠️ Частая ошибка

Выносят всю логику в хуки, даже простую:

```jsx
// ❌ Избыточно
function useButton() {
  const [clicked, setClicked] = useState(false);
  const handleClick = () => setClicked(true);
  return { clicked, handleClick };
}

function Button() {
  const { clicked, handleClick } = useButton();
  return <button onClick={handleClick}>{clicked ? "Нажато" : "Нажми"}</button>;
}

// ✅ Проще
function Button() {
  const [clicked, setClicked] = useState(false);
  return (
    <button onClick={() => setClicked(true)}>
      {clicked ? "Нажато" : "Нажми"}
    </button>
  );
}
```

### Итоги

- Выноси логику в хук, если она переиспользуется или сложная
- Выноси логику для разделения ответственности и улучшения читаемости
- Выноси логику для упрощения тестирования
- Не выноси простую, специфичную логику
- Не выноси логику, тесно связанную с конкретным UI
- Следуй принципам DRY и Single Responsibility

---

## 5. Что такое custom hook

Custom hook (пользовательский хук) — это **функция, которая начинается с `use`** и может использовать другие хуки. Это способ переиспользования логики с состоянием между компонентами.

### Базовое определение

```jsx
function useCounter(initialValue = 0) {
  const [count, setCount] = useState(initialValue);

  const increment = () => setCount(count + 1);
  const decrement = () => setCount(count - 1);
  const reset = () => setCount(initialValue);

  return { count, increment, decrement, reset };
}
```

**Важные правила:**

1. Имя начинается с `use`
2. Может использовать другие хуки
3. Может возвращать любые значения

### Использование custom hook

```jsx
function Counter() {
  const { count, increment, decrement, reset } = useCounter(10);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={increment}>+</button>
      <button onClick={decrement}>-</button>
      <button onClick={reset}>Сброс</button>
    </div>
  );
}
```

### Пример: хук для загрузки данных

```jsx
function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    let cancelled = false;

    async function fetchData() {
      try {
        setLoading(true);
        setError(null);
        const response = await fetch(url);
        if (!response.ok) throw new Error("Ошибка загрузки");
        const result = await response.json();

        if (!cancelled) {
          setData(result);
        }
      } catch (err) {
        if (!cancelled) {
          setError(err.message);
        }
      } finally {
        if (!cancelled) {
          setLoading(false);
        }
      }
    }

    fetchData();

    return () => {
      cancelled = true;
    };
  }, [url]);

  return { data, loading, error };
}

// Использование
function UserProfile({ userId }) {
  const { data: user, loading, error } = useFetch(`/api/users/${userId}`);

  if (loading) return <div>Загрузка...</div>;
  if (error) return <div>Ошибка: {error}</div>;
  if (!user) return null;

  return <div>{user.name}</div>;
}
```

### Пример: хук для локального хранилища

```jsx
function useLocalStorage(key, initialValue) {
  // Инициализация из localStorage
  const [storedValue, setStoredValue] = useState(() => {
    try {
      const item = window.localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch (error) {
      return initialValue;
    }
  });

  // Функция для обновления
  const setValue = (value) => {
    try {
      setStoredValue(value);
      window.localStorage.setItem(key, JSON.stringify(value));
    } catch (error) {
      console.error("Ошибка сохранения в localStorage:", error);
    }
  };

  return [storedValue, setValue];
}

// Использование
function App() {
  const [name, setName] = useLocalStorage("name", "Алекс");

  return <input value={name} onChange={(e) => setName(e.target.value)} />;
}
```

### Пример: хук для debounce

```jsx
function useDebounce(value, delay) {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    const handler = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);

    return () => {
      clearTimeout(handler);
    };
  }, [value, delay]);

  return debouncedValue;
}

// Использование
function SearchInput() {
  const [searchTerm, setSearchTerm] = useState("");
  const debouncedSearchTerm = useDebounce(searchTerm, 500);

  useEffect(() => {
    if (debouncedSearchTerm) {
      // Выполняем поиск
      console.log("Поиск:", debouncedSearchTerm);
    }
  }, [debouncedSearchTerm]);

  return (
    <input value={searchTerm} onChange={(e) => setSearchTerm(e.target.value)} />
  );
}
```

### Пример: хук для формы

```jsx
function useForm(initialValues) {
  const [values, setValues] = useState(initialValues);
  const [errors, setErrors] = useState({});

  const handleChange = (field, value) => {
    setValues({ ...values, [field]: value });
    // Очищаем ошибку при изменении
    if (errors[field]) {
      setErrors({ ...errors, [field]: "" });
    }
  };

  const setError = (field, message) => {
    setErrors({ ...errors, [field]: message });
  };

  const reset = () => {
    setValues(initialValues);
    setErrors({});
  };

  return { values, errors, handleChange, setError, reset };
}

// Использование
function LoginForm() {
  const { values, errors, handleChange, setError, reset } = useForm({
    email: "",
    password: "",
  });

  const handleSubmit = (e) => {
    e.preventDefault();
    // Валидация
    if (!values.email) {
      setError("email", "Email обязателен");
      return;
    }
    // Отправка формы
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        value={values.email}
        onChange={(e) => handleChange("email", e.target.value)}
      />
      {errors.email && <div>{errors.email}</div>}
      <button type="submit">Войти</button>
    </form>
  );
}
```

### Правила custom hooks

1. **Имя начинается с `use`** — это правило React
2. **Можно использовать другие хуки** — внутри custom hook
3. **Не вызывай внутри условий или циклов** — как и обычные хуки
4. **Может возвращать что угодно** — значения, функции, объекты, массивы

### Преимущества custom hooks

1. **Переиспользование логики** — одна логика в нескольких компонентах
2. **Разделение ответственности** — компонент фокусируется на UI
3. **Тестируемость** — логику можно тестировать отдельно
4. **Читаемость** — компонент становится проще

### Визуальная аналогия

Custom hook — как **универсальный инструмент**:

- Обычный хук (`useState`) — как молоток (базовый инструмент)
- Custom hook — как дрель (специализированный инструмент, собранный из базовых)
- Можно использовать один и тот же инструмент в разных местах

### ⚠️ Частая ошибка

Забывают про правило имён или используют хуки неправильно:

```jsx
// ❌ Неправильно — имя не начинается с use
function counter() {
  const [count, setCount] = useState(0);
  return { count, setCount };
}

// ✅ Правильно
function useCounter() {
  const [count, setCount] = useState(0);
  return { count, setCount };
}
```

Также неправильно вызывают custom hook:

```jsx
// ❌ Неправильно — хук в условии
function Component() {
  if (someCondition) {
    const { count } = useCounter(); // ❌ Хук в условии
  }
  return <div>...</div>;
}

// ✅ Правильно
function Component() {
  const { count } = useCounter(); // Хук на верхнем уровне
  return <div>...</div>;
}
```

### Итоги

- Custom hook — функция, начинающаяся с `use`, использующая другие хуки
- Позволяет переиспользовать логику с состоянием между компонентами
- Может возвращать любые значения (объекты, массивы, примитивы)
- Следуй правилам хуков (не в условиях, не в циклах)
- Используй для переиспользования логики, разделения ответственности и улучшения читаемости

---
