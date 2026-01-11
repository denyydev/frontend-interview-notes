# React State

Состояние компонентов и управление данными в React.  
Материал представлен в формате **вопрос–ответ**.

1. [Что такое state](#1-что-такое-state)
2. [Чем state отличается от props](#2-чем-state-отличается-от-props)
3. [Когда использовать state](#3-когда-использовать-state)
4. [Почему нельзя мутировать state напрямую](#4-почему-нельзя-мутировать-state-напрямую)
5. [Как обновляется state](#5-как-обновляется-state)
6. [Асинхронный ли setState / setState-хуки](#6-асинхронный-ли-setstate--setstate-хуки)

## 1. Что такое state

State (состояние) — это **данные компонента, которые могут изменяться со временем**. Когда state изменяется, React автоматически перерисовывает компонент.

### Простое определение

State — это **внутренние данные компонента**, которые управляют его поведением и отображением.

```jsx
function Counter() {
  const [count, setCount] = useState(0); // state

  return (
    <div>
      <p>Счёт: {count}</p>
      <button onClick={() => setCount(count + 1)}>Увеличить</button>
    </div>
  );
}
```

Здесь `count` — это state, который хранит текущее значение счётчика.

### Зачем нужен state?

State нужен для хранения **динамических данных**, которые:

- Могут изменяться
- Влияют на отображение компонента
- Управляются самим компонентом

### Примеры использования state

#### Счётчик

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>{count}</p>
      <button onClick={() => setCount(count + 1)}>+</button>
      <button onClick={() => setCount(count - 1)}>-</button>
    </div>
  );
}
```

#### Форма с input

```jsx
function InputForm() {
  const [name, setName] = useState("");

  return (
    <div>
      <input value={name} onChange={(e) => setName(e.target.value)} />
      <p>Вы ввели: {name}</p>
    </div>
  );
}
```

#### Список элементов

```jsx
function TodoList() {
  const [todos, setTodos] = useState([]);

  const addTodo = (text) => {
    setTodos([...todos, { id: Date.now(), text }]);
  };

  return (
    <div>
      {todos.map((todo) => (
        <div key={todo.id}>{todo.text}</div>
      ))}
    </div>
  );
}
```

### useState хук

В функциональных компонентах state создаётся с помощью хука `useState`:

```jsx
const [state, setState] = useState(initialValue);
```

- `state` — текущее значение состояния
- `setState` — функция для обновления состояния
- `initialValue` — начальное значение

### Множественный state

В компоненте может быть **несколько state**:

```jsx
function UserForm() {
  const [name, setName] = useState("");
  const [email, setEmail] = useState("");
  const [age, setAge] = useState(0);

  return (
    <form>
      <input value={name} onChange={(e) => setName(e.target.value)} />
      <input value={email} onChange={(e) => setEmail(e.target.value)} />
      <input
        type="number"
        value={age}
        onChange={(e) => setAge(Number(e.target.value))}
      />
    </form>
  );
}
```

### Объект state

Можно хранить несколько значений в **одном объекте state**:

```jsx
function UserForm() {
  const [user, setUser] = useState({
    name: "",
    email: "",
    age: 0,
  });

  const updateField = (field, value) => {
    setUser({ ...user, [field]: value });
  };

  return (
    <form>
      <input
        value={user.name}
        onChange={(e) => updateField("name", e.target.value)}
      />
      <input
        value={user.email}
        onChange={(e) => updateField("email", e.target.value)}
      />
    </form>
  );
}
```

### Когда state обновляется, компонент ре-рендерится

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  console.log("Рендер!"); // Вызовется при каждом изменении count

  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

При каждом клике `count` обновляется, и компонент **перерисовывается**.

### State vs переменные

**Обычная переменная** не вызывает ре-рендер:

```jsx
function Component() {
  let count = 0; // ❌ Не вызовет ре-рендер

  return <button onClick={() => count++}>{count} // Не обновится!</button>;
}
```

**State** вызывает ре-рендер:

```jsx
function Component() {
  const [count, setCount] = useState(0); // ✅ Вызовет ре-рендер

  return (
    <button onClick={() => setCount(count + 1)}>{count} // Обновится!</button>
  );
}
```

### Визуальная аналогия

State — как **переменная с "магией"**:

- Обычная переменная — просто хранит значение
- State — хранит значение **и сообщает React**, что нужно обновить UI

Представь, что state — это **колокольчик**: когда он меняется, React "слышит" это и обновляет интерфейс.

### Итоги

- State — данные компонента, которые могут изменяться
- State создаётся с помощью хука `useState`
- Когда state изменяется, компонент автоматически ре-рендерится
- В компоненте может быть несколько state
- State нужен для динамических данных, которые влияют на UI

---

## 2. Чем state отличается от props

State и props — это два разных способа работы с данными в React. Понимание их различий критично для правильной работы с компонентами.

### Основное различие

- **Props** — данные, которые **передаются** в компонент (извне)
- **State** — данные, которые **управляются** компонентом (внутри)

### Сравнительная таблица

| Аспект              | Props                       | State                      |
| ------------------- | --------------------------- | -------------------------- |
| Откуда берутся      | От родительского компонента | Внутри компонента          |
| Можно изменять?     | Нет (только для чтения)     | Да (через setState)        |
| Кто управляет?      | Родительский компонент      | Сам компонент              |
| Вызывает ре-рендер? | Да (при изменении)          | Да (при изменении)         |
| Назначение          | Передача данных вниз        | Хранение внутренних данных |

### Props — данные извне

```jsx
function Greeting({ name }) {
  // name — это prop
  return <h1>Привет, {name}!</h1>;
}

// Родитель передаёт данные
<Greeting name="Алекс" />;
```

`name` приходит **извне**, от родительского компонента.

### State — данные внутри

```jsx
function Counter() {
  const [count, setCount] = useState(0); // count — это state

  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

`count` **управляется внутри** компонента.

### Props неизменяемы, state изменяем

#### Props

```jsx
function Component({ name }) {
  name = "Другое имя"; // ❌ Нельзя изменить prop
  return <h1>{name}</h1>;
}
```

#### State

```jsx
function Component() {
  const [name, setName] = useState("Имя");

  const changeName = () => {
    setName("Другое имя"); // ✅ Можно изменить state
  };

  return (
    <div>
      <h1>{name}</h1>
      <button onClick={changeName}>Изменить</button>
    </div>
  );
}
```

### Кто управляет данными?

#### Props — родитель управляет

```jsx
function App() {
  const [userName, setUserName] = useState("Алекс");

  return (
    <div>
      <Greeting name={userName} /> {/* Родитель управляет */}
      <button onClick={() => setUserName("Мария")}>Изменить имя</button>
    </div>
  );
}

function Greeting({ name }) {
  // name управляется родителем (App)
  return <h1>Привет, {name}!</h1>;
}
```

#### State — компонент управляет сам

```jsx
function Counter() {
  const [count, setCount] = useState(0); // Компонент управляет сам

  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

### Комбинация props и state

Часто компонент использует **и props, и state**:

```jsx
function UserCard({ initialName }) {
  // prop
  const [name, setName] = useState(initialName); // state

  return (
    <div>
      <p>{name}</p>
      <button onClick={() => setName("Новое имя")}>Изменить</button>
    </div>
  );
}

// Использование
<UserCard initialName="Алекс" />;
```

Здесь `initialName` — prop (передаётся извне), `name` — state (управляется внутри).

### Когда что использовать?

#### Используй props, когда:

- Данные приходят от родителя
- Компонент должен быть переиспользуемым с разными данными
- Данные не должны изменяться внутри компонента

```jsx
function Button({ text, onClick }) {
  // props
  return <button onClick={onClick}>{text}</button>;
}
```

#### Используй state, когда:

- Данные управляются самим компонентом
- Данные изменяются в ответ на действия пользователя
- Данные нужны только внутри компонента

```jsx
function Counter() {
  const [count, setCount] = useState(0); // state

  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

### Поток данных

**Props:** данные текут **сверху вниз** (от родителя к ребёнку)

```
App (родитель)
  ↓ props
UserCard (ребёнок)
```

**State:** данные управляются **внутри компонента**

```
Counter (компонент)
  ↓ state
Внутренние данные
```

### ⚠️ Частая ошибка

Путают props и state:

```jsx
// ❌ Неправильно
function Component({ count }) {
  count = count + 1; // Пытаются изменить prop
  return <div>{count}</div>;
}

// ✅ Правильно
function Component() {
  const [count, setCount] = useState(0); // Используют state
  return (
    <div>
      <div>{count}</div>
      <button onClick={() => setCount(count + 1)}>+</button>
    </div>
  );
}
```

### Итоги

- Props — данные извне (от родителя), неизменяемы
- State — данные внутри компонента, изменяемы
- Props управляются родителем, state — самим компонентом
- Используй props для переиспользуемых компонентов
- Используй state для внутренних данных компонента

---

## 3. Когда использовать state

State нужно использовать **только когда данные должны изменяться** и влиять на отображение компонента. Не всё нужно хранить в state.

### Когда использовать state?

#### 1. Данные, которые изменяются

```jsx
function Counter() {
  const [count, setCount] = useState(0); // ✅ Изменяется

  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

#### 2. Данные, влияющие на UI

```jsx
function Toggle() {
  const [isOpen, setIsOpen] = useState(false); // ✅ Влияет на UI

  return (
    <div>
      <button onClick={() => setIsOpen(!isOpen)}>
        {isOpen ? "Скрыть" : "Показать"}
      </button>
      {isOpen && <div>Содержимое</div>}
    </div>
  );
}
```

#### 3. Ввод пользователя

```jsx
function InputForm() {
  const [value, setValue] = useState(""); // ✅ Ввод пользователя

  return <input value={value} onChange={(e) => setValue(e.target.value)} />;
}
```

#### 4. Состояние загрузки

```jsx
function DataLoader() {
  const [loading, setLoading] = useState(true); // ✅ Состояние загрузки
  const [data, setData] = useState(null);

  useEffect(() => {
    fetchData().then((result) => {
      setData(result);
      setLoading(false);
    });
  }, []);

  if (loading) return <div>Загрузка...</div>;
  return <div>{data}</div>;
}
```

### Когда НЕ использовать state?

#### 1. Вычисляемые значения

```jsx
// ❌ Неправильно
function Component({ a, b }) {
  const [sum, setSum] = useState(0);

  useEffect(() => {
    setSum(a + b);
  }, [a, b]);

  return <div>{sum}</div>;
}

// ✅ Правильно
function Component({ a, b }) {
  const sum = a + b; // Вычисляем напрямую

  return <div>{sum}</div>;
}
```

#### 2. Данные из props

```jsx
// ❌ Неправильно
function Component({ name }) {
  const [userName, setUserName] = useState(name);

  return <div>{userName}</div>;
}

// ✅ Правильно
function Component({ name }) {
  return <div>{name}</div>; // Используем prop напрямую
}
```

#### 3. Константы

```jsx
// ❌ Неправильно
function Component() {
  const [PI, setPI] = useState(3.14);
  return <div>{PI}</div>;
}

// ✅ Правильно
function Component() {
  const PI = 3.14; // Константа
  return <div>{PI}</div>;
}
```

#### 4. Данные, которые не влияют на рендер

```jsx
// ❌ Неправильно
function Component() {
  const [timer, setTimer] = useState(null);

  useEffect(() => {
    const id = setInterval(() => {
      console.log("Тик");
    }, 1000);
    setTimer(id);
  }, []);

  return <div>Компонент</div>;
}

// ✅ Правильно
function Component() {
  useEffect(() => {
    const id = setInterval(() => {
      console.log("Тик");
    }, 1000);

    return () => clearInterval(id);
  }, []);

  return <div>Компонент</div>;
}
```

### Правило: минимальный state

Храни в state **только то, что действительно нужно**:

```jsx
// ❌ Плохо: лишний state
function UserForm() {
  const [firstName, setFirstName] = useState("");
  const [lastName, setLastName] = useState("");
  const [fullName, setFullName] = useState(""); // ❌ Можно вычислить

  useEffect(() => {
    setFullName(`${firstName} ${lastName}`);
  }, [firstName, lastName]);

  return <div>{fullName}</div>;
}

// ✅ Хорошо: только необходимый state
function UserForm() {
  const [firstName, setFirstName] = useState("");
  const [lastName, setLastName] = useState("");
  const fullName = `${firstName} ${lastName}`; // ✅ Вычисляем

  return <div>{fullName}</div>;
}
```

### Когда поднимать state вверх?

Если данные нужны **нескольким компонентам**, подними state в **общего родителя**:

```jsx
// ❌ Плохо: state в каждом компоненте
function App() {
  return (
    <div>
      <Counter /> {/* Свой state */}
      <Display /> {/* Свой state */}
    </div>
  );
}

// ✅ Хорошо: state в родителе
function App() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <Counter count={count} onIncrement={() => setCount(count + 1)} />
      <Display count={count} />
    </div>
  );
}
```

### Примеры правильного использования

#### Форма с валидацией

```jsx
function LoginForm() {
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");
  const [errors, setErrors] = useState({});

  const validate = () => {
    const newErrors = {};
    if (!email) newErrors.email = "Email обязателен";
    if (!password) newErrors.password = "Пароль обязателен";
    setErrors(newErrors);
    return Object.keys(newErrors).length === 0;
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    if (validate()) {
      // Отправка формы
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <input value={email} onChange={(e) => setEmail(e.target.value)} />
      {errors.email && <span>{errors.email}</span>}
      {/* ... */}
    </form>
  );
}
```

#### Список с фильтрацией

```jsx
function TodoList() {
  const [todos, setTodos] = useState([]);
  const [filter, setFilter] = useState("all");

  const filteredTodos = todos.filter((todo) => {
    if (filter === "active") return !todo.completed;
    if (filter === "completed") return todo.completed;
    return true;
  });

  return (
    <div>
      <select value={filter} onChange={(e) => setFilter(e.target.value)}>
        <option value="all">Все</option>
        <option value="active">Активные</option>
        <option value="completed">Завершённые</option>
      </select>
      {filteredTodos.map((todo) => (
        <TodoItem key={todo.id} todo={todo} />
      ))}
    </div>
  );
}
```

### Итоги

- Используй state для данных, которые изменяются и влияют на UI
- Не используй state для вычисляемых значений, констант, данных из props
- Храни минимальный state (только необходимое)
- Поднимай state вверх, если данные нужны нескольким компонентам
- State должен влиять на отображение компонента

---

## 4. Почему нельзя мутировать state напрямую

Мутация (изменение) state напрямую — это **плохая практика**, которая может привести к багам, непредсказуемому поведению и проблемам с производительностью.

### Что такое мутация?

Мутация — это **изменение объекта или массива напрямую**, без создания нового:

```jsx
// ❌ Мутация
const user = { name: "Алекс" };
user.name = "Мария"; // Изменили объект напрямую

const items = [1, 2, 3];
items.push(4); // Изменили массив напрямую
```

### Почему нельзя мутировать state?

#### 1. React не заметит изменения

React сравнивает state по **ссылке**, а не по содержимому:

```jsx
function Component() {
  const [user, setUser] = useState({ name: "Алекс" });

  const badUpdate = () => {
    user.name = "Мария"; // ❌ Мутация
    setUser(user); // React не заметит изменения!
  };

  return (
    <div>
      <p>{user.name}</p>
      <button onClick={badUpdate}>Изменить</button>
    </div>
  );
}
```

React **не обновит компонент**, потому что ссылка на объект не изменилась.

#### 2. Проблемы с ре-рендерингом

```jsx
function TodoList() {
  const [todos, setTodos] = useState([
    { id: 1, text: "Задача 1" },
    { id: 2, text: "Задача 2" },
  ]);

  const badAdd = () => {
    todos.push({ id: 3, text: "Задача 3" }); // ❌ Мутация
    setTodos(todos); // React не обновит!
  };

  return (
    <div>
      {todos.map((todo) => (
        <div key={todo.id}>{todo.text}</div>
      ))}
      <button onClick={badAdd}>Добавить</button>
    </div>
  );
}
```

Компонент **не обновится**, хотя массив изменился.

#### 3. Проблемы с оптимизациями React

React использует **shallow comparison** (поверхностное сравнение) для оптимизации. Мутация нарушает это:

```jsx
function Component() {
  const [data, setData] = useState({ count: 0 });

  useEffect(() => {
    // Этот эффект не сработает, если мутировать data
    console.log("Data изменилась");
  }, [data]);
}
```

#### 4. Непредсказуемое поведение

Мутация может привести к **неожиданным результатам**:

```jsx
function Component() {
  const [items, setItems] = useState([1, 2, 3]);

  const badUpdate = () => {
    items[0] = 999; // ❌ Мутация
    setItems(items);

    // Позже где-то в коде
    console.log(items); // [999, 2, 3] — неожиданно!
  };
}
```

### Правильный подход: создание новых объектов/массивов

#### Объекты

```jsx
// ❌ Неправильно
function Component() {
  const [user, setUser] = useState({ name: "Алекс", age: 25 });

  const badUpdate = () => {
    user.name = "Мария"; // Мутация
    setUser(user);
  };
}

// ✅ Правильно
function Component() {
  const [user, setUser] = useState({ name: "Алекс", age: 25 });

  const goodUpdate = () => {
    setUser({ ...user, name: "Мария" }); // Новый объект
  };
}
```

#### Массивы

```jsx
// ❌ Неправильно
function Component() {
  const [items, setItems] = useState([1, 2, 3]);

  const badAdd = () => {
    items.push(4); // Мутация
    setItems(items);
  };
}

// ✅ Правильно
function Component() {
  const [items, setItems] = useState([1, 2, 3]);

  const goodAdd = () => {
    setItems([...items, 4]); // Новый массив
  };
}
```

### Примеры правильных обновлений

#### Добавление элемента в массив

```jsx
const [todos, setTodos] = useState([]);

// ✅ Правильно
const addTodo = (text) => {
  setTodos([...todos, { id: Date.now(), text }]);
};
```

#### Удаление элемента из массива

```jsx
const [todos, setTodos] = useState([...]);

// ✅ Правильно
const removeTodo = (id) => {
  setTodos(todos.filter(todo => todo.id !== id));
};
```

#### Обновление элемента в массиве

```jsx
const [todos, setTodos] = useState([...]);

// ✅ Правильно
const updateTodo = (id, newText) => {
  setTodos(todos.map(todo =>
    todo.id === id ? { ...todo, text: newText } : todo
  ));
};
```

#### Обновление свойства объекта

```jsx
const [user, setUser] = useState({ name: "Алекс", age: 25 });

// ✅ Правильно
const updateName = (newName) => {
  setUser({ ...user, name: newName });
};

// ✅ Правильно (обновление нескольких свойств)
const updateUser = (updates) => {
  setUser({ ...user, ...updates });
};
```

#### Вложенные объекты

```jsx
const [user, setUser] = useState({
  name: "Алекс",
  address: { city: "Москва", street: "Ленина" },
});

// ✅ Правильно
const updateCity = (newCity) => {
  setUser({
    ...user,
    address: { ...user.address, city: newCity },
  });
};
```

### Почему React требует неизменяемость?

#### 1. Предсказуемость

Когда state неизменяем, поведение **предсказуемо**:

- Легче отследить изменения
- Легче отлаживать
- Легче тестировать

#### 2. Производительность

React может **оптимизировать** обновления:

- Сравнение по ссылке быстрее, чем глубокое сравнение
- Может пропустить ненужные ре-рендеры

#### 3. Функциональное программирование

Неизменяемость — принцип **функционального программирования**:

- Функции не имеют побочных эффектов
- Код проще понимать и поддерживать

### ⚠️ Частая ошибка

Мутация вложенных объектов:

```jsx
// ❌ Неправильно
const [user, setUser] = useState({
  name: "Алекс",
  address: { city: "Москва" },
});

const badUpdate = () => {
  user.address.city = "СПб"; // Мутация вложенного объекта
  setUser(user);
};

// ✅ Правильно
const goodUpdate = () => {
  setUser({
    ...user,
    address: { ...user.address, city: "СПб" },
  });
};
```

### Итоги

- Нельзя мутировать state напрямую
- React не заметит изменения при мутации
- Создавай новые объекты/массивы при обновлении
- Используй spread оператор (`...`) для копирования
- Неизменяемость делает код предсказуемым и производительным

---

## 5. Как обновляется state

Понимание того, как обновляется state, критично для правильной работы с React. State обновляется **асинхронно**, и React может **батчить** (группировать) обновления.

### Базовое обновление state

State обновляется через **функцию setState** (или `setState` из хука):

```jsx
function Component() {
  const [count, setCount] = useState(0);

  const increment = () => {
    setCount(count + 1); // Обновление state
  };

  return <button onClick={increment}>{count}</button>;
}
```

### State обновляется асинхронно

**Важно:** обновление state **не происходит мгновенно**:

```jsx
function Component() {
  const [count, setCount] = useState(0);

  const increment = () => {
    setCount(count + 1);
    console.log(count); // ⚠️ Старое значение! (0, а не 1)
  };

  return <button onClick={increment}>{count}</button>;
}
```

После `setCount(count + 1)` значение `count` **ещё не обновилось**.

### Функциональное обновление

Чтобы использовать **текущее значение** state, используй **функциональную форму**:

```jsx
function Component() {
  const [count, setCount] = useState(0);

  const increment = () => {
    setCount((prevCount) => prevCount + 1); // ✅ Используем предыдущее значение
    console.log(count); // Всё ещё старое значение
  };

  return <button onClick={increment}>{count}</button>;
}
```

Функция `prevCount => prevCount + 1` получает **актуальное значение** state.

### Множественные обновления

React может **батчить** (группировать) обновления:

```jsx
function Component() {
  const [count, setCount] = useState(0);

  const increment = () => {
    setCount(count + 1);
    setCount(count + 1);
    setCount(count + 1);
    // count будет 1, а не 3! ⚠️
  };

  return <button onClick={increment}>{count}</button>;
}
```

Все три вызова используют **одно и то же значение** `count` (0).

### Правильное множественное обновление

Используй **функциональную форму** для множественных обновлений:

```jsx
function Component() {
  const [count, setCount] = useState(0);

  const increment = () => {
    setCount((prev) => prev + 1);
    setCount((prev) => prev + 1);
    setCount((prev) => prev + 1);
    // count будет 3! ✅
  };

  return <button onClick={increment}>{count}</button>;
}
```

Каждый вызов получает **результат предыдущего**.

### Обновление объекта

При обновлении объекта **всегда создавай новый объект**:

```jsx
function Component() {
  const [user, setUser] = useState({ name: "Алекс", age: 25 });

  const updateName = (newName) => {
    setUser({ ...user, name: newName }); // ✅ Новый объект
  };

  return <div>{user.name}</div>;
}
```

### Множественное обновление объекта

```jsx
function Component() {
  const [user, setUser] = useState({ name: "Алекс", age: 25 });

  const updateUser = () => {
    // ❌ Неправильно (может потерять изменения)
    setUser({ ...user, name: "Мария" });
    setUser({ ...user, age: 30 });

    // ✅ Правильно (функциональная форма)
    setUser((prev) => ({ ...prev, name: "Мария" }));
    setUser((prev) => ({ ...prev, age: 30 }));
  };
}
```

### Когда использовать функциональную форму?

#### 1. Зависимость от предыдущего значения

```jsx
const [count, setCount] = useState(0);

// ✅ Когда нужно предыдущее значение
const increment = () => {
  setCount((prev) => prev + 1);
};
```

#### 2. Множественные обновления

```jsx
const [count, setCount] = useState(0);

// ✅ Множественные обновления
const increment = () => {
  setCount((prev) => prev + 1);
  setCount((prev) => prev + 1);
};
```

#### 3. Асинхронные обновления

```jsx
const [count, setCount] = useState(0);

useEffect(() => {
  setTimeout(() => {
    // ✅ Используем функциональную форму в асинхронном коде
    setCount((prev) => prev + 1);
  }, 1000);
}, []);
```

### Ре-рендер после обновления

После обновления state компонент **автоматически ре-рендерится**:

```jsx
function Component() {
  const [count, setCount] = useState(0);

  console.log("Рендер!"); // Вызовется при каждом обновлении count

  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

### Оптимизация: пропуск обновления

Если новое значение **равно старому**, React может **пропустить ре-рендер**:

```jsx
function Component() {
  const [count, setCount] = useState(0);

  const noChange = () => {
    setCount(0); // То же значение — ре-рендер может быть пропущен
  };

  return <button onClick={noChange}>{count}</button>;
}
```

**Важно:** для объектов и массивов сравнение идёт по **ссылке**, а не по содержимому:

```jsx
const [user, setUser] = useState({ name: "Алекс" });

const update = () => {
  setUser({ name: "Алекс" }); // Новый объект — ре-рендер произойдёт!
};
```

### Итоги

- State обновляется асинхронно
- Используй функциональную форму (`prev => ...`) для зависимости от предыдущего значения
- React может батчить обновления
- После обновления state компонент ре-рендерится
- Всегда создавай новые объекты/массивы при обновлении

---

## 6. Асинхронный ли setState / setState-хуки

**Да, обновление state асинхронное!** Это важное свойство React, которое влияет на то, как работает код.

### setState асинхронный

Когда вызываешь `setState` (или `setCount`, `setName` и т.д.), обновление **не происходит мгновенно**:

```jsx
function Component() {
  const [count, setCount] = useState(0);

  const increment = () => {
    setCount(count + 1);
    console.log(count); // ⚠️ Старое значение! (0, а не 1)
  };

  return <button onClick={increment}>{count}</button>;
}
```

После `setCount(count + 1)` значение `count` **ещё не обновилось**.

### Почему асинхронный?

React **батчит** (группирует) обновления для:

1. **Производительности** — меньше ре-рендеров
2. **Консистентности** — все обновления применяются вместе
3. **Оптимизации** — React может отменить ненужные обновления

### Проблема: использование старого значения

```jsx
function Component() {
  const [count, setCount] = useState(0);

  const increment = () => {
    setCount(count + 1);
    setCount(count + 1);
    setCount(count + 1);
    // count будет 1, а не 3! ⚠️
  };

  return <button onClick={increment}>{count}</button>;
}
```

Все три вызова используют **одно и то же значение** `count` (0).

### Решение: функциональная форма

Используй **функциональную форму** для получения актуального значения:

```jsx
function Component() {
  const [count, setCount] = useState(0);

  const increment = () => {
    setCount((prev) => prev + 1);
    setCount((prev) => prev + 1);
    setCount((prev) => prev + 1);
    // count будет 3! ✅
  };

  return <button onClick={increment}>{count}</button>;
}
```

Каждый вызов получает **результат предыдущего**.

### Когда значение обновится?

Значение обновится **после завершения функции** и **перед следующим рендером**:

```jsx
function Component() {
  const [count, setCount] = useState(0);

  const increment = () => {
    setCount(count + 1);
    console.log(count); // 0 (старое значение)
  };

  console.log(count); // Обновлённое значение в следующем рендере

  return <button onClick={increment}>{count}</button>;
}
```

### useEffect для отслеживания изменений

Чтобы увидеть **обновлённое значение**, используй `useEffect`:

```jsx
function Component() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    console.log("Count обновился:", count); // Актуальное значение
  }, [count]);

  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

### Классовые компоненты: setState

В классовых компонентах `setState` тоже **асинхронный**:

```jsx
class Component extends React.Component {
  state = { count: 0 };

  increment = () => {
    this.setState({ count: this.state.count + 1 });
    console.log(this.state.count); // ⚠️ Старое значение!
  };

  render() {
    return <button onClick={this.increment}>{this.state.count}</button>;
  }
}
```

### Функциональная форма в классовых компонентах

```jsx
class Component extends React.Component {
  state = { count: 0 };

  increment = () => {
    this.setState((prevState) => ({
      count: prevState.count + 1,
    }));
    this.setState((prevState) => ({
      count: prevState.count + 1,
    }));
    // count увеличится на 2 ✅
  };

  render() {
    return <button onClick={this.increment}>{this.state.count}</button>;
  }
}
```

### Callback после обновления (только классовые)

В классовых компонентах можно передать **callback**:

```jsx
class Component extends React.Component {
  state = { count: 0 };

  increment = () => {
    this.setState({ count: this.state.count + 1 }, () => {
      console.log("Обновлено:", this.state.count); // Актуальное значение
    });
  };
}
```

В функциональных компонентах используй `useEffect`:

```jsx
function Component() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    console.log("Обновлено:", count); // Актуальное значение
  }, [count]);

  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

### Батчинг обновлений

React **группирует обновления** в обработчиках событий:

```jsx
function Component() {
  const [count, setCount] = useState(0);
  const [name, setName] = useState("Алекс");

  const update = () => {
    setCount(count + 1);
    setName("Мария");
    // Оба обновления применятся вместе — только один ре-рендер
  };

  return <button onClick={update}>Обновить</button>;
}
```

### ⚠️ Частая ошибка

Использование старого значения сразу после setState:

```jsx
// ❌ Неправильно
function Component() {
  const [user, setUser] = useState({ name: "Алекс" });

  const update = () => {
    setUser({ name: "Мария" });
    console.log(user.name); // "Алекс" (старое значение!)
    sendToServer(user); // Отправит старое значение!
  };
}

// ✅ Правильно
function Component() {
  const [user, setUser] = useState({ name: "Алекс" });

  useEffect(() => {
    if (user.name === "Мария") {
      sendToServer(user); // Актуальное значение
    }
  }, [user]);

  const update = () => {
    setUser({ name: "Мария" });
  };
}
```

### Итоги

- `setState` и хуки обновления state **асинхронные**
- Значение не обновляется сразу после вызова
- Используй функциональную форму (`prev => ...`) для актуального значения
- React батчит обновления для производительности
- Используй `useEffect` для отслеживания изменений state

---
