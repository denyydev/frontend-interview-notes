# Rendering

Процесс рендеринга компонентов и обновления интерфейса в React.  
Материал представлен в формате **вопрос–ответ**.

1. [Когда компонент перерисовывается](#1-когда-компонент-перерисовывается)
2. [Что вызывает re-render](#2-что-вызывает-re-render)
3. [Почему изменение обычной переменной не вызывает ререндер](#3-почему-изменение-обычной-переменной-не-вызывает-ререндер)
4. [Что такое key и зачем он нужен](#4-что-такое-key-и-зачем-он-нужен)
5. [Почему нельзя использовать index как key (база)](#5-почему-нельзя-использовать-index-как-key-база)

## 1. Когда компонент перерисовывается

Компонент перерисовывается (re-render) когда React решает, что нужно обновить отображение компонента на основе изменений в данных.

### Основные причины перерисовки

1. **Изменение state компонента**
2. **Изменение props компонента**
3. **Ре-рендер родительского компонента**
4. **Вызов `forceUpdate()` (классовые компоненты)**

### Изменение state

Когда state компонента изменяется, компонент **обязательно** перерисовывается:

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  console.log("Рендер:", count); // Вызовется при каждом изменении count

  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

При каждом клике `count` изменяется → компонент перерисовывается.

### Изменение props

Когда props компонента изменяются, компонент перерисовывается:

```jsx
function App() {
  const [name, setName] = useState("Алекс");

  return (
    <div>
      <button onClick={() => setName("Мария")}>Изменить имя</button>
      <Greeting name={name} /> {/* Перерисуется при изменении name */}
    </div>
  );
}

function Greeting({ name }) {
  console.log("Greeting рендерится:", name); // Вызовется при изменении name
  return <h1>Привет, {name}!</h1>;
}
```

Когда `name` в `App` изменяется, `Greeting` получает новые props и перерисовывается.

### Ре-рендер родительского компонента

Когда родительский компонент перерисовывается, **дочерние компоненты тоже перерисовываются** (даже если их props не изменились):

```jsx
function App() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <button onClick={() => setCount(count + 1)}>{count}</button>
      <StaticComponent /> {/* Перерисуется, хотя props не изменились */}
    </div>
  );
}

function StaticComponent() {
  console.log("StaticComponent рендерится"); // Вызовется при каждом клике
  return <div>Статичный компонент</div>;
}
```

**Важно:** даже если props дочернего компонента не изменились, он всё равно перерисовывается при ре-рендере родителя.

### Когда компонент НЕ перерисовывается?

Компонент **не перерисовывается**, если:

- State и props не изменились
- Родительский компонент не перерисовывается

### Оптимизация: React.memo

Чтобы предотвратить лишние ре-рендеры, можно использовать `React.memo`:

```jsx
const StaticComponent = React.memo(function StaticComponent() {
  console.log("StaticComponent рендерится");
  return <div>Статичный компонент</div>;
});

function App() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <button onClick={() => setCount(count + 1)}>{count}</button>
      <StaticComponent /> {/* Не перерисуется, если props не изменились */}
    </div>
  );
}
```

`React.memo` делает **поверхностное сравнение** props и пропускает ре-рендер, если они не изменились.

### Визуальная аналогия

Ре-рендер — как **обновление картины**:

- State изменяется → картина перерисовывается
- Props изменяются → картина перерисовывается
- Родитель перерисовывается → все дочерние картины тоже обновляются

### Итоги

- Компонент перерисовывается при изменении state или props
- Дочерние компоненты перерисовываются при ре-рендере родителя
- Можно использовать `React.memo` для предотвращения лишних ре-рендеров
- React эффективно обновляет только изменённые части DOM

---

## 2. Что вызывает re-render

Re-render (перерисовка) компонента вызывается в нескольких случаях. Понимание этих случаев помогает оптимизировать производительность приложения.

### Основные триггеры re-render

1. **Изменение state компонента**
2. **Изменение props компонента**
3. **Ре-рендер родительского компонента**
4. **Вызов `forceUpdate()` (классовые компоненты, не рекомендуется)**

### 1. Изменение state

Самая очевидная причина — изменение state через `setState`:

```jsx
function Counter() {
  const [count, setCount] = useState(0);
  const [name, setName] = useState("");

  console.log("Рендер:", count, name);

  return (
    <div>
      <button onClick={() => setCount(count + 1)}>Count: {count}</button>
      <input value={name} onChange={(e) => setName(e.target.value)} />
    </div>
  );
}
```

Каждое изменение `count` или `name` вызывает re-render.

### 2. Изменение props

Когда родитель передаёт новые props, компонент перерисовывается:

```jsx
function App() {
  const [user, setUser] = useState({ name: "Алекс", age: 25 });

  return (
    <UserCard
      name={user.name}
      age={user.age}
      onUpdate={() => setUser({ ...user, age: 26 })}
    />
  );
}

function UserCard({ name, age, onUpdate }) {
  console.log("UserCard рендерится"); // При изменении props

  return (
    <div>
      <p>
        {name}, {age} лет
      </p>
      <button onClick={onUpdate}>Увеличить возраст</button>
    </div>
  );
}
```

### 3. Ре-рендер родителя

Когда родительский компонент перерисовывается, **все дочерние компоненты тоже перерисовываются** (даже если их props не изменились):

```jsx
function App() {
  const [count, setCount] = useState(0);
  const [unrelated, setUnrelated] = useState(0);

  return (
    <div>
      <button onClick={() => setCount(count + 1)}>Count: {count}</button>
      <button onClick={() => setUnrelated(unrelated + 1)}>
        Unrelated: {unrelated}
      </button>
      <ChildComponent /> {/* Перерисуется при изменении count И unrelated */}
    </div>
  );
}

function ChildComponent() {
  console.log("ChildComponent рендерится");
  return <div>Дочерний компонент</div>;
}
```

**Важно:** `ChildComponent` перерисовывается даже если не использует `count` или `unrelated`.

### 4. Context обновление

Если компонент использует Context, он перерисовывается при изменении значения Context:

```jsx
const ThemeContext = createContext();

function App() {
  const [theme, setTheme] = useState("light");

  return (
    <ThemeContext.Provider value={theme}>
      <button onClick={() => setTheme(theme === "light" ? "dark" : "light")}>
        Переключить тему
      </button>
      <ThemedComponent /> {/* Перерисуется при изменении theme */}
    </ThemeContext.Provider>
  );
}

function ThemedComponent() {
  const theme = useContext(ThemeContext);
  console.log("ThemedComponent рендерится:", theme);
  return <div>Тема: {theme}</div>;
}
```

### Что НЕ вызывает re-render?

#### Изменение обычной переменной

```jsx
function Component() {
  let count = 0; // Обычная переменная

  const increment = () => {
    count++; // ❌ Не вызовет re-render
    console.log(count); // Значение изменится, но UI не обновится
  };

  return (
    <button onClick={increment}>
      {count} {/* Не обновится! */}
    </button>
  );
}
```

#### Изменение объекта/массива напрямую (мутация)

```jsx
function Component() {
  const [user, setUser] = useState({ name: "Алекс" });

  const badUpdate = () => {
    user.name = "Мария"; // ❌ Мутация — не вызовет re-render
    setUser(user); // React не заметит изменения (та же ссылка)
  };

  return (
    <button onClick={badUpdate}>
      {user.name} {/* Не обновится! */}
    </button>
  );
}
```

### Когда React может пропустить re-render?

React может **пропустить re-render**, если:

- Props не изменились (при использовании `React.memo`)
- State остался прежним (при сравнении по ссылке)
- Компонент возвращает тот же результат

```jsx
function Component({ value }) {
  const [count, setCount] = useState(0);

  const noChange = () => {
    setCount(0); // То же значение — React может пропустить re-render
  };

  return <div>{count}</div>;
}
```

### Сравнительная таблица

| Действие                     | Вызывает re-render?    |
| ---------------------------- | ---------------------- |
| `setState(newValue)`         | ✅ Да                  |
| Изменение props              | ✅ Да                  |
| Ре-рендер родителя           | ✅ Да (для дочерних)   |
| Изменение обычной переменной | ❌ Нет                 |
| Мутация объекта/массива      | ❌ Нет                 |
| `setState(текущееЗначение)`  | ⚠️ Может быть пропущен |

### ⚠️ Частая ошибка

Думают, что изменение переменной вызовет re-render:

```jsx
// ❌ Неправильно
function Component() {
  let count = 0;

  return (
    <button onClick={() => count++}>
      {count} {/* Не обновится */}
    </button>
  );
}

// ✅ Правильно
function Component() {
  const [count, setCount] = useState(0);

  return (
    <button onClick={() => setCount(count + 1)}>
      {count} {/* Обновится */}
    </button>
  );
}
```

### Итоги

- Re-render вызывается при изменении state, props, ре-рендере родителя
- Изменение обычных переменных не вызывает re-render
- Мутация объектов/массивов не вызывает re-render
- React может пропустить re-render, если ничего не изменилось
- Используй state для данных, которые должны вызывать re-render

---

## 3. Почему изменение обычной переменной не вызывает ререндер

Изменение обычной переменной не вызывает re-render, потому что React **не отслеживает** обычные переменные. React отслеживает только **state** и **props**.

### Проблема с обычными переменными

```jsx
function Component() {
  let count = 0; // Обычная переменная

  const increment = () => {
    count++; // Изменяем переменную
    console.log(count); // Значение изменилось в консоли
  };

  return (
    <button onClick={increment}>
      {count} {/* Но на экране не обновится! */}
    </button>
  );
}
```

**Что происходит:**

1. При клике `count` увеличивается
2. Значение переменной меняется в памяти
3. Но React **не знает** об этом изменении
4. UI не обновляется

### Почему React не отслеживает обычные переменные?

React отслеживает изменения только для:

- **State** (через хуки `useState`, `useReducer`)
- **Props** (передаются от родителя)
- **Context** (через `useContext`)

React **не может** отслеживать все переменные в коде, потому что:

1. **Производительность** — отслеживание всех переменных было бы очень медленным
2. **Простота** — явное управление через state проще
3. **Предсказуемость** — точно знаешь, что вызывает обновление

### Как работает state?

State хранится **вне компонента** в специальной структуре React:

```jsx
function Component() {
  const [count, setCount] = useState(0);

  // React отслеживает count в своей внутренней структуре
  // При вызове setCount React:
  // 1. Обновляет значение
  // 2. Помечает компонент для re-render
  // 3. Перерисовывает компонент
}
```

### Сравнение

#### Обычная переменная

```jsx
function Component() {
  let count = 0; // Хранится в локальной области видимости функции

  const increment = () => {
    count++; // Изменение локальной переменной
    // React не знает об этом изменении
  };

  return <button onClick={increment}>{count}</button>;
}
```

**Проблемы:**

- Значение теряется при каждом рендере
- React не отслеживает изменения
- UI не обновляется

#### State

```jsx
function Component() {
  const [count, setCount] = useState(0); // Хранится в React

  const increment = () => {
    setCount(count + 1); // React узнаёт об изменении
    // React обновляет UI
  };

  return <button onClick={increment}>{count}</button>;
}
```

**Преимущества:**

- Значение сохраняется между рендерами
- React отслеживает изменения
- UI автоматически обновляется

### Что происходит при рендере?

При каждом рендере функция компонента **выполняется заново**:

```jsx
function Component() {
  let count = 0; // При каждом рендере count снова становится 0!

  console.log("Рендер, count:", count); // Всегда 0

  return <div>{count}</div>;
}
```

**С state:**

```jsx
function Component() {
  const [count, setCount] = useState(0);

  console.log("Рендер, count:", count); // Сохраняется между рендерами

  return <div>{count}</div>;
}
```

### Визуальная аналогия

**Обычная переменная** — как запись в блокноте:

- Можешь изменить её
- Но никто об этом не узнает
- При следующем рендере блокнот "обновляется" (переменная снова инициализируется)

**State** — как табличка на доске объявлений:

- Когда меняешь её, все видят изменения
- React видит изменение и обновляет UI
- Значение сохраняется между рендерами

### Когда использовать обычные переменные?

Обычные переменные можно использовать для:

- **Вычисляемых значений** (не влияют на UI)
- **Временных значений** (внутри функции)
- **Констант**

```jsx
function Component({ items }) {
  const [filter, setFilter] = useState("all");

  // Обычная переменная — вычисляемое значение
  const filteredItems = items.filter((item) => {
    if (filter === "active") return !item.completed;
    return true;
  });

  // Обычная переменная — константа
  const MAX_ITEMS = 10;

  return (
    <div>
      {filteredItems.slice(0, MAX_ITEMS).map((item) => (
        <div key={item.id}>{item.text}</div>
      ))}
    </div>
  );
}
```

### ⚠️ Частая ошибка

Пытаются использовать обычные переменные вместо state:

```jsx
// ❌ Неправильно
function Component() {
  let count = 0;

  return (
    <button onClick={() => count++}>
      {count} {/* Не обновится */}
    </button>
  );
}

// ✅ Правильно
function Component() {
  const [count, setCount] = useState(0);

  return (
    <button onClick={() => setCount(count + 1)}>
      {count} {/* Обновится */}
    </button>
  );
}
```

### Итоги

- Обычные переменные не вызывают re-render, потому что React их не отслеживает
- React отслеживает только state, props и context
- State хранится вне компонента и сохраняется между рендерами
- Используй state для данных, которые должны вызывать обновление UI
- Используй обычные переменные для вычисляемых значений и констант

---

## 4. Что такое key и зачем он нужен

`key` — это специальный prop в React, который помогает React **идентифицировать элементы** в списке и эффективно обновлять их при изменениях.

### Базовый синтаксис

```jsx
{
  items.map((item) => <Item key={item.id} item={item} />);
}
```

### Зачем нужен key?

React использует `key` для:

1. **Идентификации элементов** в списке
2. **Эффективного обновления** при изменениях
3. **Правильного сопоставления** элементов между рендерами

### Проблема без key

Без `key` React может **неправильно сопоставить** элементы при изменениях:

```jsx
// Список без key
function TodoList({ todos }) {
  return (
    <ul>
      {todos.map(todo => (
        <li>{todo.text}</li>  {/* ❌ Нет key */}
      ))}
    </ul>
  );
}
```

**Проблемы:**

- React выдаст предупреждение
- Может неправильно обновить элементы
- Может потерять состояние элементов

### Пример работы key

```jsx
function TodoList({ todos }) {
  return (
    <ul>
      {todos.map(todo => (
        <li key={todo.id}>{todo.text}</li>  {/* ✅ С key */}
      ))}
    </ul>
  );
}
```

**Что происходит:**

1. **Первый рендер:**

   ```js
   todos = [
     { id: 1, text: "Купить хлеб" },
     { id: 2, text: "Позвонить" },
   ];
   ```

   React создаёт элементы с `key="1"` и `key="2"`.

2. **Добавили элемент в начало:**

   ```js
   todos = [
     { id: 3, text: "Новая задача" }, // Новый элемент
     { id: 1, text: "Купить хлеб" },
     { id: 2, text: "Позвонить" },
   ];
   ```

   React видит:

   - `key="3"` — новый элемент, создаёт
   - `key="1"` — существующий элемент, обновляет (если нужно)
   - `key="2"` — существующий элемент, обновляет (если нужно)

3. **Результат:** React эффективно обновляет только нужные элементы.

### Требования к key

`key` должен быть:

- **Уникальным** среди siblings (элементов одного уровня)
- **Стабильным** (не должен меняться между рендерами)
- **Не меняться** при ре-рендере

### Правильные значения для key

#### ✅ Уникальный ID

```jsx
{
  todos.map((todo) => <TodoItem key={todo.id} todo={todo} />);
}
```

#### ✅ Уникальная строка

```jsx
{
  users.map((user) => <UserCard key={`user-${user.id}`} user={user} />);
}
```

#### ❌ Index (не рекомендуется)

```jsx
{
  todos.map((todo, index) => (
    <TodoItem key={index} todo={todo} /> // ⚠️ Не рекомендуется
  ));
}
```

**Почему не рекомендуется:** если порядок элементов может измениться, index не является стабильным идентификатором.

#### ❌ Случайные значения

```jsx
{
  todos.map((todo) => (
    <TodoItem key={Math.random()} todo={todo} /> // ❌ Неправильно
  ));
}
```

**Проблема:** при каждом рендере создаётся новый key, React думает, что это новый элемент.

#### ❌ Нестабильные значения

```jsx
{
  todos.map((todo) => (
    <TodoItem key={todo.text} todo={todo} /> // ⚠️ Может быть неуникальным
  ));
}
```

**Проблема:** если текст может повториться, key не будет уникальным.

### Key должен быть на верхнем уровне

```jsx
// ✅ Правильно
function List({ items }) {
  return (
    <ul>
      {items.map((item) => (
        <li key={item.id}>{item.text}</li>
      ))}
    </ul>
  );
}

// ❌ Неправильно
function List({ items }) {
  return (
    <ul>
      {items.map((item) => (
        <li>
          <span key={item.id}>{item.text}</span>{" "}
          {/* key не на верхнем уровне */}
        </li>
      ))}
    </ul>
  );
}
```

`key` должен быть на элементе, который возвращается из `map`.

### Key с Fragment

Если используешь Fragment, `key` должен быть на Fragment:

```jsx
{
  items.map((item) => (
    <React.Fragment key={item.id}>
      <dt>{item.term}</dt>
      <dd>{item.definition}</dd>
    </React.Fragment>
  ));
}
```

**Важно:** короткий синтаксис `<>` не поддерживает key.

### Визуальная аналогия

`key` — как **номер квартиры** в доме:

- Каждая квартира имеет уникальный номер
- Когда приходит почта, находят квартиру по номеру
- Даже если квартиры переставили, номер помогает найти правильную

Без `key` React как почтальон без номеров — может перепутать, кому что доставлять.

### Итоги

- `key` — специальный prop для идентификации элементов в списке
- Должен быть уникальным, стабильным и не меняться
- Используй уникальный ID из данных
- Избегай index, если порядок может измениться
- `key` должен быть на элементе верхнего уровня в map

---

## 5. Почему нельзя использовать index как key (база)

Использование `index` как `key` может привести к **багам** и **неправильному поведению** компонентов, особенно когда порядок элементов изменяется.

### Проблема с index

```jsx
// ❌ Неправильно
{
  todos.map((todo, index) => <TodoItem key={index} todo={todo} />);
}
```

### Проблема 1: Неправильное сопоставление при изменении порядка

Когда элементы добавляются или удаляются, index не соответствует элементу:

```jsx
// Начальное состояние
todos = [
  { id: 1, text: "Купить хлеб" }, // index: 0
  { id: 2, text: "Позвонить" }, // index: 1
];

// После удаления первого элемента
todos = [
  { id: 2, text: "Позвонить" }, // index: 0 (был 1!)
];
```

**Проблема:**

- React видит элемент с `key="0"`
- Думает, что это тот же элемент (с `key="0"`)
- Но на самом деле это другой элемент (был `key="1"`)
- Может показать неправильные данные или состояние

### Проблема 2: Сохранение состояния в неправильном элементе

Если компонент имеет внутреннее состояние (например, input), оно может "прилипнуть" к неправильному элементу:

```jsx
function TodoList({ todos, onDelete }) {
  return (
    <ul>
      {todos.map((todo, index) => (
        <TodoItem
          key={index} // ❌ Используем index
          todo={todo}
          onDelete={() => onDelete(index)}
        />
      ))}
    </ul>
  );
}

function TodoItem({ todo, onDelete }) {
  const [isEditing, setIsEditing] = useState(false);
  const [text, setText] = useState(todo.text);

  return (
    <li>
      {isEditing ? (
        <input value={text} onChange={(e) => setText(e.target.value)} />
      ) : (
        <span>{text}</span>
      )}
      <button onClick={() => setIsEditing(!isEditing)}>Редактировать</button>
      <button onClick={onDelete}>Удалить</button>
    </li>
  );
}
```

**Сценарий:**

1. У тебя 3 задачи: [0, 1, 2]
2. Ты начинаешь редактировать задачу с index 1
3. Удаляешь задачу с index 0
4. Задача, которую ты редактировал, теперь имеет index 0
5. React думает, что элемент с key="0" — это тот же элемент
6. Состояние редактирования может "перепрыгнуть" на другую задачу!

### Проблема 3: Неправильное обновление при фильтрации

```jsx
function TodoList({ todos, filter }) {
  const filteredTodos = todos.filter((todo) => {
    if (filter === "active") return !todo.completed;
    if (filter === "completed") return todo.completed;
    return true;
  });

  return (
    <ul>
      {filteredTodos.map((todo, index) => (
        <TodoItem key={index} todo={todo} /> // ❌ Index меняется при фильтрации
      ))}
    </ul>
  );
}
```

**Проблема:**

- При изменении фильтра список меняется
- Index элементов меняется
- React может неправильно сопоставить элементы

### Правильное решение: использовать уникальный ID

```jsx
// ✅ Правильно
{
  todos.map((todo) => <TodoItem key={todo.id} todo={todo} />);
}
```

**Преимущества:**

- ID стабилен и не меняется
- Правильное сопоставление элементов
- Состояние сохраняется в правильном элементе

### Когда index допустим?

Index можно использовать **только если**:

- Список **никогда не меняется** (не добавляются/удаляются элементы)
- Элементы **не имеют внутреннего состояния**
- Порядок **никогда не меняется**

```jsx
// ✅ Может быть допустимо (если список статичен)
const COLORS = ["red", "green", "blue"];

function ColorList() {
  return (
    <ul>
      {COLORS.map((color, index) => (
        <li key={index}>{color}</li> // Статичный список
      ))}
    </ul>
  );
}
```

Но даже в этом случае лучше использовать уникальный ключ:

```jsx
// ✅ Лучше
const COLORS = [
  { id: "red", value: "red" },
  { id: "green", value: "green" },
  { id: "blue", value: "blue" },
];

function ColorList() {
  return (
    <ul>
      {COLORS.map((color) => (
        <li key={color.id}>{color.value}</li>
      ))}
    </ul>
  );
}
```

### Пример бага с index

```jsx
function BuggyList() {
  const [items, setItems] = useState([
    { id: 1, text: "Элемент 1" },
    { id: 2, text: "Элемент 2" },
    { id: 3, text: "Элемент 3" },
  ]);

  const deleteFirst = () => {
    setItems(items.slice(1)); // Удаляем первый элемент
  };

  return (
    <div>
      <button onClick={deleteFirst}>Удалить первый</button>
      {items.map((item, index) => (
        <div key={index}>
          <input defaultValue={item.text} />{" "}
          {/* Состояние может "прилипнуть" */}
          <span>{item.text}</span>
        </div>
      ))}
    </div>
  );
}
```

**Что происходит:**

1. У тебя 3 элемента
2. Начинаешь вводить текст в input первого элемента
3. Удаляешь первый элемент
4. Текст, который ты вводил, может "перепрыгнуть" на другой элемент!

### ⚠️ Частая ошибка

Используют index, когда есть уникальный ID:

```jsx
// ❌ Неправильно
{
  todos.map((todo, index) => (
    <TodoItem key={index} todo={todo} /> // Используем index
  ));
}

// ✅ Правильно
{
  todos.map((todo) => (
    <TodoItem key={todo.id} todo={todo} /> // Используем уникальный ID
  ));
}
```

### Итоги

- Index как key может привести к багам при изменении порядка элементов
- Состояние компонентов может "прилипнуть" к неправильным элементам
- Используй уникальный ID из данных
- Index допустим только для статичных списков без состояния
- Всегда лучше использовать уникальный идентификатор

---
