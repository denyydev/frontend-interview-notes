# Events and Forms

Обработка событий и работа с формами в React.  
Материал представлен в формате **вопрос–ответ**

1. [Как обрабатываются события в React](#1-как-обрабатываются-события-в-react)
2. [SyntheticEvent — что это](#2-syntheticevent--что-это)
3. [Как работает onChange в input](#3-как-работает-onchange-в-input)
4. [Как сделать controlled input](#4-как-сделать-controlled-input)

## 1. Как обрабатываются события в React

Обработка событий в React похожа на обработку событий в обычном HTML, но есть важные отличия в синтаксисе и поведении.

### Базовый синтаксис

В React события называются в **camelCase**, а не в lowercase:

```jsx
// HTML
<button onclick="handleClick()">Клик</button>

// React
<button onClick={handleClick}>Клик</button>
```

**Отличия:**

- `onclick` → `onClick` (camelCase)
- Значение — функция, а не строка
- Функция передаётся без скобок (если не нужно передать аргументы)

### Передача обработчика

```jsx
function Button() {
  const handleClick = () => {
    console.log("Кнопка нажата!");
  };

  return <button onClick={handleClick}>Нажми меня</button>;
}
```

**Важно:** передаём **ссылку на функцию**, а не вызываем её:

```jsx
// ❌ Неправильно — функция вызовется сразу
<button onClick={handleClick()}>Нажми</button>

// ✅ Правильно — функция вызовется при клике
<button onClick={handleClick}>Нажми</button>
```

### Передача аргументов

Если нужно передать аргументы, используй стрелочную функцию:

```jsx
function Button() {
  const handleClick = (id, name) => {
    console.log("ID:", id, "Name:", name);
  };

  return <button onClick={() => handleClick(1, "Алекс")}>Нажми меня</button>;
}
```

Или используй `bind`:

```jsx
function Button() {
  const handleClick = function (id, name) {
    console.log("ID:", id, "Name:", name);
  };

  return (
    <button onClick={handleClick.bind(null, 1, "Алекс")}>Нажми меня</button>
  );
}
```

### Встроенный обработчик

Можно определить обработчик прямо в JSX:

```jsx
function Button() {
  return <button onClick={() => console.log("Клик!")}>Нажми меня</button>;
}
```

### Разные типы событий

React поддерживает все стандартные события DOM:

```jsx
function Form() {
  return (
    <form onSubmit={(e) => e.preventDefault()}>
      <input
        onChange={(e) => console.log(e.target.value)}
        onFocus={() => console.log("Фокус")}
        onBlur={() => console.log("Потеря фокуса")}
        onKeyDown={(e) => console.log("Клавиша:", e.key)}
      />
      <button type="submit">Отправить</button>
    </form>
  );
}
```

### Event объект

React передаёт специальный объект события — `SyntheticEvent`:

```jsx
function Button() {
  const handleClick = (e) => {
    console.log(e); // SyntheticEvent
    console.log(e.target); // Элемент, на который кликнули
    console.log(e.type); // 'click'
  };

  return <button onClick={handleClick}>Нажми</button>;
}
```

### Отмена стандартного поведения

Используй `preventDefault()`:

```jsx
function Link() {
  const handleClick = (e) => {
    e.preventDefault(); // Отменяем переход по ссылке
    console.log("Клик по ссылке, но без перехода");
  };

  return (
    <a href="https://example.com" onClick={handleClick}>
      Ссылка
    </a>
  );
}
```

### Остановка всплытия

Используй `stopPropagation()`:

```jsx
function Parent() {
  const handleParentClick = () => {
    console.log("Клик по родителю");
  };

  return (
    <div onClick={handleParentClick}>
      <button
        onClick={(e) => {
          e.stopPropagation(); // Останавливаем всплытие
          console.log("Клик по кнопке");
        }}
      >
        Кнопка
      </button>
    </div>
  );
}
```

Без `stopPropagation()` при клике на кнопку вызовутся оба обработчика.

### Обработка событий в классовых компонентах

В классовых компонентах нужно привязать `this`:

```jsx
class Button extends React.Component {
  handleClick() {
    console.log("Клик!", this); // this должен быть привязан
  }

  render() {
    // Вариант 1: стрелочная функция
    return <button onClick={() => this.handleClick()}>Нажми</button>;

    // Вариант 2: bind в конструкторе
    // constructor() {
    //   this.handleClick = this.handleClick.bind(this);
    // }
    // return <button onClick={this.handleClick}>Нажми</button>;
  }
}
```

### Визуальная аналогия

Обработка событий в React — как **кнопка звонка**:

- В HTML ты пишешь инструкцию на бумажке и приклеиваешь к кнопке
- В React ты даёшь кнопке **функцию**, которая выполнится при нажатии
- React сам "нажимает" кнопку и вызывает твою функцию

### ⚠️ Частая ошибка

Вызывают функцию сразу вместо передачи ссылки:

```jsx
// ❌ Неправильно
<button onClick={handleClick()}>Нажми</button>
// handleClick вызовется сразу при рендере, а не при клике

// ✅ Правильно
<button onClick={handleClick}>Нажми</button>
// handleClick вызовется только при клике
```

### Итоги

- События в React называются в camelCase (`onClick`, `onChange`)
- Передавай функцию, а не вызывай её (если не нужны аргументы)
- React передаёт `SyntheticEvent` вместо нативного события
- Используй `preventDefault()` и `stopPropagation()` как обычно
- В функциональных компонентах проще работать с событиями

---

## 2. SyntheticEvent — что это

`SyntheticEvent` — это **обёртка React** над нативными событиями браузера. React создаёт единый интерфейс для событий, который работает одинаково во всех браузерах.

### Что такое SyntheticEvent?

`SyntheticEvent` — это объект, который React передаёт в обработчики событий вместо нативного события браузера:

```jsx
function Button() {
  const handleClick = (e) => {
    console.log(e); // SyntheticEvent, а не нативное событие
    console.log(e.nativeEvent); // Нативное событие браузера
  };

  return <button onClick={handleClick}>Нажми</button>;
}
```

### Зачем нужен SyntheticEvent?

React создал `SyntheticEvent` для:

1. **Единообразия** — одинаковый API во всех браузерах
2. **Производительности** — пул событий (event pooling)
3. **Дополнительных возможностей** — дополнительные методы и свойства

### Основные свойства

`SyntheticEvent` имеет те же свойства, что и нативное событие:

```jsx
function Input() {
  const handleChange = (e) => {
    console.log(e.target); // Элемент, который вызвал событие
    console.log(e.target.value); // Значение input
    console.log(e.type); // Тип события: 'change'
    console.log(e.currentTarget); // Элемент, на котором обработчик
  };

  return <input onChange={handleChange} />;
}
```

### Доступ к нативному событию

Если нужен доступ к нативному событию браузера:

```jsx
function Button() {
  const handleClick = (e) => {
    const nativeEvent = e.nativeEvent; // Нативное событие
    console.log(nativeEvent);
  };

  return <button onClick={handleClick}>Нажми</button>;
}
```

### Event Pooling (до React 17)

**Важно:** В React 17+ event pooling **отключён**. Но важно понимать концепцию.

**До React 17:**

- React переиспользовал объекты событий для производительности
- После завершения обработчика свойства события могли быть очищены
- Нужно было сохранять значения:

```jsx
// React 16 и ниже
function Button() {
  const handleClick = (e) => {
    // ❌ Неправильно — значение может быть очищено
    setTimeout(() => {
      console.log(e.target); // Может быть null!
    }, 1000);

    // ✅ Правильно — сохраняем значение
    const target = e.target;
    setTimeout(() => {
      console.log(target); // Работает
    }, 1000);
  };

  return <button onClick={handleClick}>Нажми</button>;
}
```

**React 17+:**

- Event pooling отключён
- Можно использовать событие асинхронно без сохранения значений

### Стандартные методы

`SyntheticEvent` поддерживает стандартные методы событий:

```jsx
function Form() {
  const handleSubmit = (e) => {
    e.preventDefault(); // Отменяем стандартное поведение
    e.stopPropagation(); // Останавливаем всплытие
    console.log("Форма отправлена");
  };

  return (
    <form onSubmit={handleSubmit}>
      <button type="submit">Отправить</button>
    </form>
  );
}
```

### Сравнение с нативным событием

```jsx
function Button() {
  const handleClick = (e) => {
    // SyntheticEvent
    console.log(e.constructor.name); // 'SyntheticMouseEvent'

    // Нативное событие
    console.log(e.nativeEvent.constructor.name); // 'MouseEvent'

    // Оба имеют похожие свойства
    console.log(e.target === e.nativeEvent.target); // true
  };

  return <button onClick={handleClick}>Нажми</button>;
}
```

### Типы SyntheticEvent

React создаёт разные типы для разных событий:

```jsx
function Component() {
  return (
    <div>
      <input
        onChange={(e) => console.log(e.constructor.name)} // SyntheticEvent
        onFocus={(e) => console.log(e.constructor.name)} // SyntheticFocusEvent
        onKeyDown={(e) => console.log(e.constructor.name)} // SyntheticKeyboardEvent
      />
      <button
        onClick={(e) => console.log(e.constructor.name)} // SyntheticMouseEvent
      >
        Клик
      </button>
    </div>
  );
}
```

### Визуальная аналогия

`SyntheticEvent` — как **переводчик**:

- Браузер говорит на своём языке (нативное событие)
- React переводит на универсальный язык (SyntheticEvent)
- Ты получаешь понятный интерфейс, который работает везде одинаково

### ⚠️ Частая ошибка

Пытаются использовать событие асинхронно без сохранения (в старых версиях React):

```jsx
// React 16 и ниже — неправильно
function Button() {
  const handleClick = (e) => {
    setTimeout(() => {
      console.log(e.target.value); // Может быть null!
    }, 1000);
  };

  return <button onClick={handleClick}>Нажми</button>;
}

// ✅ Правильно (React 16)
function Button() {
  const handleClick = (e) => {
    const value = e.target.value; // Сохраняем значение
    setTimeout(() => {
      console.log(value); // Работает
    }, 1000);
  };

  return <button onClick={handleClick}>Нажми</button>;
}
```

**В React 17+ это не нужно** — event pooling отключён.

### Итоги

- `SyntheticEvent` — обёртка React над нативными событиями
- Обеспечивает единообразие во всех браузерах
- Имеет те же свойства и методы, что и нативное событие
- Доступ к нативному событию через `e.nativeEvent`
- В React 17+ можно использовать событие асинхронно без проблем

---

## 3. Как работает onChange в input

`onChange` в React срабатывает **при каждом изменении** значения input, а не только при потере фокуса (как в HTML).

### Базовое использование

```jsx
function Input() {
  const handleChange = (e) => {
    console.log("Новое значение:", e.target.value);
  };

  return <input onChange={handleChange} />;
}
```

### Когда срабатывает onChange?

В React `onChange` срабатывает **при каждом изменении** значения:

```jsx
function Input() {
  const handleChange = (e) => {
    console.log(e.target.value); // Вызовется при каждом вводе символа
  };

  return <input onChange={handleChange} />;
}
```

**Отличие от HTML:**

- **HTML:** `onchange` срабатывает при потере фокуса
- **React:** `onChange` срабатывает при каждом изменении

### Получение значения

Значение получаешь через `e.target.value`:

```jsx
function Input() {
  const handleChange = (e) => {
    const newValue = e.target.value; // Новое значение
    console.log(newValue);
  };

  return <input onChange={handleChange} />;
}
```

### Сохранение значения в state

Обычно значение сохраняют в state:

```jsx
function Input() {
  const [value, setValue] = useState("");

  const handleChange = (e) => {
    setValue(e.target.value); // Обновляем state
  };

  return (
    <input
      value={value} // Контролируемый input
      onChange={handleChange}
    />
  );
}
```

### Разные типы input

`onChange` работает для всех типов input:

```jsx
function Form() {
  const [text, setText] = useState("");
  const [number, setNumber] = useState(0);
  const [checked, setChecked] = useState(false);

  return (
    <form>
      <input
        type="text"
        value={text}
        onChange={(e) => setText(e.target.value)}
      />
      <input
        type="number"
        value={number}
        onChange={(e) => setNumber(Number(e.target.value))}
      />
      <input
        type="checkbox"
        checked={checked}
        onChange={(e) => setChecked(e.target.checked)} // checked, а не value!
      />
    </form>
  );
}
```

**Важно:** для checkbox используй `e.target.checked`, а не `e.target.value`.

### Textarea

`onChange` работает и для textarea:

```jsx
function Textarea() {
  const [text, setText] = useState("");

  return <textarea value={text} onChange={(e) => setText(e.target.value)} />;
}
```

### Select

Для select тоже используй `onChange`:

```jsx
function Select() {
  const [selected, setSelected] = useState("");

  return (
    <select value={selected} onChange={(e) => setSelected(e.target.value)}>
      <option value="">Выберите...</option>
      <option value="option1">Опция 1</option>
      <option value="option2">Опция 2</option>
    </select>
  );
}
```

### Обработка без state (uncontrolled)

Можно использовать `onChange` без сохранения в state:

```jsx
function Input() {
  const handleChange = (e) => {
    console.log("Значение:", e.target.value);
    // Делаем что-то с значением, но не сохраняем в state
  };

  return <input onChange={handleChange} />;
}
```

Но обычно лучше использовать controlled input (со state).

### Фильтрация ввода

Можно фильтровать ввод прямо в `onChange`:

```jsx
function NumberInput() {
  const [value, setValue] = useState("");

  const handleChange = (e) => {
    const input = e.target.value;
    // Разрешаем только цифры
    if (/^\d*$/.test(input)) {
      setValue(input);
    }
  };

  return (
    <input
      type="text"
      value={value}
      onChange={handleChange}
      placeholder="Только цифры"
    />
  );
}
```

### Визуальная аналогия

`onChange` в React — как **микрофон**, который слышит каждое слово:

- В HTML `onchange` — как запись, которая включается только в конце
- В React `onChange` — как живая трансляция, которая передаёт каждое изменение сразу

### ⚠️ Частая ошибка

Путают `onChange` и `onInput`:

```jsx
// ❌ Неправильно — onInput не стандартный prop в React
<input onInput={handleChange} />

// ✅ Правильно — используй onChange
<input onChange={handleChange} />
```

Также забывают про `checked` для checkbox:

```jsx
// ❌ Неправильно
<input
  type="checkbox"
  checked={checked}
  onChange={(e) => setChecked(e.target.value)}  // value не существует для checkbox
/>

// ✅ Правильно
<input
  type="checkbox"
  checked={checked}
  onChange={(e) => setChecked(e.target.checked)}  // Используем checked
/>
```

### Итоги

- `onChange` срабатывает при каждом изменении значения
- Получай значение через `e.target.value` (или `e.target.checked` для checkbox)
- Обычно значение сохраняют в state для controlled input
- Работает для всех типов input, textarea и select
- В React `onChange` работает как `oninput` в HTML, а не как `onchange`

---

## 4. Как сделать controlled input

Controlled input — это input, значение которого **контролируется React** через state. React становится "единственным источником правды" для значения input.

### Базовый пример

```jsx
function ControlledInput() {
  const [value, setValue] = useState("");

  return (
    <input
      value={value} // Значение из state
      onChange={(e) => setValue(e.target.value)} // Обновляем state
    />
  );
}
```

**Ключевые моменты:**

- `value={value}` — значение приходит из state
- `onChange` обновляет state
- React полностью контролирует значение

### Почему controlled?

Controlled input называется так, потому что React **контролирует** значение:

- React решает, какое значение показывать
- React решает, когда значение может измениться
- Можно валидировать, фильтровать, трансформировать значение

### Полный пример с валидацией

```jsx
function EmailInput() {
  const [email, setEmail] = useState("");
  const [error, setError] = useState("");

  const handleChange = (e) => {
    const newValue = e.target.value;
    setEmail(newValue);

    // Валидация
    if (newValue && !newValue.includes("@")) {
      setError("Email должен содержать @");
    } else {
      setError("");
    }
  };

  return (
    <div>
      <input
        type="email"
        value={email}
        onChange={handleChange}
        placeholder="Введите email"
      />
      {error && <div style={{ color: "red" }}>{error}</div>}
    </div>
  );
}
```

### Controlled textarea

```jsx
function ControlledTextarea() {
  const [text, setText] = useState("");

  return (
    <textarea
      value={text}
      onChange={(e) => setText(e.target.value)}
      placeholder="Введите текст"
    />
  );
}
```

### Controlled select

```jsx
function ControlledSelect() {
  const [selected, setSelected] = useState("");

  return (
    <select value={selected} onChange={(e) => setSelected(e.target.value)}>
      <option value="">Выберите...</option>
      <option value="option1">Опция 1</option>
      <option value="option2">Опция 2</option>
      <option value="option3">Опция 3</option>
    </select>
  );
}
```

### Controlled checkbox

```jsx
function ControlledCheckbox() {
  const [checked, setChecked] = useState(false);

  return (
    <label>
      <input
        type="checkbox"
        checked={checked} // checked, а не value!
        onChange={(e) => setChecked(e.target.checked)} // checked, а не value!
      />
      Согласен с условиями
    </label>
  );
}
```

**Важно:** для checkbox используй `checked` и `e.target.checked`, а не `value`.

### Controlled radio buttons

```jsx
function ControlledRadio() {
  const [selected, setSelected] = useState("option1");

  return (
    <div>
      <label>
        <input
          type="radio"
          value="option1"
          checked={selected === "option1"}
          onChange={(e) => setSelected(e.target.value)}
        />
        Опция 1
      </label>
      <label>
        <input
          type="radio"
          value="option2"
          checked={selected === "option2"}
          onChange={(e) => setSelected(e.target.value)}
        />
        Опция 2
      </label>
    </div>
  );
}
```

### Фильтрация ввода

Controlled input позволяет легко фильтровать ввод:

```jsx
function NumberInput() {
  const [value, setValue] = useState("");

  const handleChange = (e) => {
    const input = e.target.value;
    // Разрешаем только цифры
    if (input === "" || /^\d+$/.test(input)) {
      setValue(input);
    }
  };

  return (
    <input
      type="text"
      value={value}
      onChange={handleChange}
      placeholder="Только цифры"
    />
  );
}
```

### Трансформация значения

Можно трансформировать значение перед сохранением:

```jsx
function UppercaseInput() {
  const [value, setValue] = useState("");

  const handleChange = (e) => {
    // Сохраняем в верхнем регистре
    setValue(e.target.value.toUpperCase());
  };

  return (
    <input
      value={value}
      onChange={handleChange}
      placeholder="Всё в верхнем регистре"
    />
  );
}
```

### Сброс значения

Легко сбросить значение controlled input:

```jsx
function Form() {
  const [name, setName] = useState("");
  const [email, setEmail] = useState("");

  const handleSubmit = (e) => {
    e.preventDefault();
    console.log({ name, email });
    // Сброс формы
    setName("");
    setEmail("");
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        value={name}
        onChange={(e) => setName(e.target.value)}
        placeholder="Имя"
      />
      <input
        value={email}
        onChange={(e) => setEmail(e.target.value)}
        placeholder="Email"
      />
      <button type="submit">Отправить</button>
    </form>
  );
}
```

### Сравнение с uncontrolled

**Controlled (рекомендуется):**

```jsx
function Controlled() {
  const [value, setValue] = useState("");
  return <input value={value} onChange={(e) => setValue(e.target.value)} />;
}
```

**Uncontrolled:**

```jsx
function Uncontrolled() {
  const inputRef = useRef();
  return <input ref={inputRef} defaultValue="начальное значение" />;
}
```

**Преимущества controlled:**

- Полный контроль над значением
- Легко валидировать и фильтровать
- Легко сбрасывать форму
- Значение всегда синхронизировано с state

### Визуальная аналогия

Controlled input — как **пульт управления**:

- Ты (React) полностью контролируешь, что показывается
- Каждое изменение проходит через тебя
- Ты можешь проверять, фильтровать, изменять значение перед отображением

Uncontrolled input — как **автоматическая система**:

- Значение хранится в DOM
- Ты не контролируешь его напрямую
- Можешь только читать значение через ref

### ⚠️ Частая ошибка

Забывают про `value` и получают uncontrolled input:

```jsx
// ❌ Неправильно — uncontrolled input
function Input() {
  const [value, setValue] = useState("");
  return <input onChange={(e) => setValue(e.target.value)} />;
  // Нет value={value}, поэтому input не контролируется
}

// ✅ Правильно — controlled input
function Input() {
  const [value, setValue] = useState("");
  return (
    <input
      value={value} // Обязательно!
      onChange={(e) => setValue(e.target.value)}
    />
  );
}
```

Также путают `checked` и `value` для checkbox:

```jsx
// ❌ Неправильно
<input
  type="checkbox"
  value={checked}  // Неправильно
  onChange={(e) => setChecked(e.target.value)}
/>

// ✅ Правильно
<input
  type="checkbox"
  checked={checked}  // Используем checked
  onChange={(e) => setChecked(e.target.checked)}  // И checked в onChange
/>
```

### Итоги

- Controlled input — значение контролируется через state
- Используй `value={state}` и `onChange` для обновления state
- Для checkbox используй `checked` вместо `value`
- Controlled input даёт полный контроль над значением
- Можно легко валидировать, фильтровать и трансформировать значение
- Всегда указывай `value` (или `checked`), иначе input будет uncontrolled

---
