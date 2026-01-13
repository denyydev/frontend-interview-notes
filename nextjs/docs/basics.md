# Next.js — Основы

Основные концепции Next.js, его преимущества и отличия от чистого React.  
Материал представлен в формате **вопрос–ответ**.

---

## Содержание (Table of Contents)

1. [Что такое Next.js и зачем он нужен](#1-что-такое-nextjs-и-зачем-он-нужен)
2. [SEO и почему Next лучше чистого React](#2-seo-и-почему-next-лучше-чистого-react)
3. [Разница pages router vs app router (база)](#3-разница-pages-router-vs-app-router-база)

---

## 1. Что такое Next.js и зачем он нужен

Next.js — это **фреймворк для React**, который добавляет серверный рендеринг, статическую генерацию, маршрутизацию на основе файлов и другие возможности для создания полноценных веб-приложений.

### Простое определение

Next.js — это "React с батарейками": он берёт React и добавляет всё необходимое для создания production-ready приложений из коробки.

**Аналогия:** Если React — это "движок автомобиля", то Next.js — это "полный автомобиль" с колёсами, рулём, коробкой передач и всем остальным.

### Основные возможности Next.js

#### 1. Server-Side Rendering (SSR)

Рендеринг компонентов на сервере перед отправкой клиенту.

```jsx
// pages/about.js (Pages Router)
export async function getServerSideProps() {
  const data = await fetch('https://api.example.com/data');
  return {
    props: { data: await data.json() }
  };
}

export default function About({ data }) {
  return <div>{data.title}</div>;
}
```

**Преимущества:**
- ✅ Лучше для SEO (поисковые системы видят готовый HTML)
- ✅ Быстрая первая загрузка (HTML уже готов)
- ✅ Работает без JavaScript

#### 2. Static Site Generation (SSG)

Генерация статических HTML-страниц на этапе сборки.

```jsx
// pages/products.js
export async function getStaticProps() {
  const products = await fetch('https://api.example.com/products');
  return {
    props: { products: await products.json() }
  };
}

export default function Products({ products }) {
  return (
    <div>
      {products.map(product => (
        <div key={product.id}>{product.name}</div>
      ))}
    </div>
  );
}
```

**Преимущества:**
- ✅ Очень быстрая загрузка (статический HTML)
- ✅ Можно хостить на CDN
- ✅ Низкая нагрузка на сервер

#### 3. File-based Routing

Маршрутизация на основе структуры файлов.

```
pages/
  index.js          → /
  about.js          → /about
  blog/
    index.js        → /blog
    [slug].js       → /blog/:slug
    [id]/
      [comment].js  → /blog/:id/:comment
```

**Преимущества:**
- ✅ Не нужно настраивать роутинг вручную
- ✅ Интуитивная структура
- ✅ Автоматическая генерация маршрутов

#### 4. API Routes

Создание API endpoints прямо в Next.js приложении.

```jsx
// pages/api/users.js
export default function handler(req, res) {
  if (req.method === 'GET') {
    res.status(200).json({ users: [...] });
  } else if (req.method === 'POST') {
    // Создать пользователя
    res.status(201).json({ success: true });
  }
}
```

**Использование:**
```javascript
// В компоненте
fetch('/api/users')
  .then(res => res.json())
  .then(data => console.log(data));
```

#### 5. Оптимизация изображений

Автоматическая оптимизация изображений.

```jsx
import Image from 'next/image';

export default function MyComponent() {
  return (
    <Image
      src="/photo.jpg"
      width={500}
      height={300}
      alt="Описание"
    />
  );
}
```

**Преимущества:**
- ✅ Автоматическое изменение размера
- ✅ Ленивая загрузка (lazy loading)
- ✅ Современные форматы (WebP, AVIF)

#### 6. Code Splitting

Автоматическое разделение кода на чанки.

```jsx
import dynamic from 'next/dynamic';

// Компонент загружается только когда нужен
const DynamicComponent = dynamic(() => import('../components/Heavy'), {
  loading: () => <p>Загрузка...</p>
});

export default function Page() {
  return <DynamicComponent />;
}
```

### Зачем нужен Next.js?

#### Проблемы чистого React

**1. Client-Side Rendering (CSR)**

```jsx
// Чистый React
function App() {
  return <div>Привет!</div>;
}

ReactDOM.render(<App />, document.getElementById('root'));
```

**Проблемы:**
- ❌ SEO: поисковики видят пустой HTML
- ❌ Медленная первая загрузка (нужно загрузить JS)
- ❌ Не работает без JavaScript

**2. Роутинг нужно настраивать вручную**

```jsx
// React Router (нужно устанавливать отдельно)
import { BrowserRouter, Routes, Route } from 'react-router-dom';

function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
      </Routes>
    </BrowserRouter>
  );
}
```

**3. Нет встроенной оптимизации**

- Нужно вручную настраивать code splitting
- Нет оптимизации изображений
- Нет автоматической оптимизации производительности

#### Решения Next.js

**1. SSR/SSG из коробки**

```jsx
// Next.js автоматически рендерит на сервере
export default function Page() {
  return <div>Привет!</div>;
}
```

**2. File-based routing**

```
pages/
  index.js    → /
  about.js    → /about
```

**3. Встроенная оптимизация**

- Автоматический code splitting
- Оптимизация изображений
- Оптимизация шрифтов
- Production оптимизации

### Когда использовать Next.js?

#### ✅ Используй Next.js для:

- Веб-сайтов с важным SEO (блоги, новостные сайты)
- E-commerce приложений
- Маркетплейсов
- Корпоративных сайтов
- Приложений, где важна производительность

#### ⚠️ Может быть избыточно для:

- Простых SPA без требований к SEO
- Админ-панелей (только для внутреннего использования)
- Прототипов и MVP

### Визуальная аналогия

**Чистый React:**
- Движок автомобиля
- Нужно самому добавлять колёса, руль, коробку передач

**Next.js:**
- Полный автомобиль
- Всё уже настроено и готово к использованию
- Можно сразу ехать

### Пример: простое приложение

#### Чистый React

```jsx
// Нужно настроить роутинг
import { BrowserRouter, Routes, Route } from 'react-router-dom';

// Нужно настроить SSR (если нужен)
// Нужно настроить оптимизацию
// Нужно настроить сборку

function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Home />} />
      </Routes>
    </BrowserRouter>
  );
}
```

#### Next.js

```jsx
// pages/index.js
export default function Home() {
  return <div>Главная</div>;
}

// Всё готово! Роутинг, SSR, оптимизация — всё работает
```

### ⚠️ Частая ошибка

Думают, что Next.js заменяет React:

```jsx
// ❌ Неправильно: Next.js не заменяет React
// Next.js использует React внутри

// ✅ Правильно: Next.js — это фреймворк для React
// Ты всё ещё пишешь React компоненты
```

### Итоги

- Next.js — фреймворк для React
- Добавляет SSR, SSG, file-based routing, API routes
- Решает проблемы SEO и производительности
- Оптимизация из коробки
- Упрощает разработку production-ready приложений
- Используется для сайтов с важным SEO и производительностью
- Next.js не заменяет React, а расширяет его возможности

---

## 2. SEO и почему Next лучше чистого React

SEO (Search Engine Optimization) — это оптимизация сайта для поисковых систем. Next.js лучше подходит для SEO, чем чистый React, потому что он рендерит HTML на сервере, который поисковики могут проиндексировать.

### Простое определение

SEO — это способность поисковых систем (Google, Yandex) находить и индексировать твой сайт. Next.js лучше для SEO, потому что поисковики видят готовый HTML, а не пустую страницу.

**Аналогия:** Если сайт — это книга, то:
- **Чистый React** — книга с пустыми страницами, текст появляется только когда читатель открывает книгу
- **Next.js** — книга с напечатанным текстом, которую можно прочитать сразу

### Проблема чистого React с SEO

#### Client-Side Rendering (CSR)

```jsx
// Чистый React
function App() {
  const [data, setData] = useState(null);

  useEffect(() => {
    fetch('/api/data')
      .then(res => res.json())
      .then(data => setData(data));
  }, []);

  if (!data) return <div>Загрузка...</div>;

  return <div>{data.title}</div>;
}

ReactDOM.render(<App />, document.getElementById('root'));
```

**Что видит поисковик:**

```html
<!-- HTML, который получает поисковик -->
<!DOCTYPE html>
<html>
  <head>
    <title>My App</title>
  </head>
  <body>
    <div id="root"></div>
    <script src="/bundle.js"></script>
  </body>
</html>
```

**Проблемы:**
- ❌ Поисковик видит пустой `<div id="root"></div>`
- ❌ Контент загружается только после выполнения JavaScript
- ❌ Поисковики могут не дождаться загрузки контента
- ❌ Плохая индексация

#### Что видит пользователь

1. Браузер загружает HTML (пустой)
2. Браузер загружает JavaScript
3. JavaScript выполняется
4. React рендерит компоненты
5. Контент появляется на экране

**Время до появления контента:** ~2-5 секунд

### Решение Next.js: Server-Side Rendering

#### SSR в Next.js

```jsx
// pages/about.js (Pages Router)
export async function getServerSideProps() {
  const res = await fetch('https://api.example.com/data');
  const data = await res.json();

  return {
    props: { data }
  };
}

export default function About({ data }) {
  return (
    <div>
      <h1>{data.title}</h1>
      <p>{data.description}</p>
    </div>
  );
}
```

**Что видит поисковик:**

```html
<!-- HTML, который получает поисковик -->
<!DOCTYPE html>
<html>
  <head>
    <title>About Page</title>
  </head>
  <body>
    <div>
      <h1>Заголовок страницы</h1>
      <p>Описание страницы</p>
    </div>
  </body>
</html>
```

**Преимущества:**
- ✅ Поисковик видит готовый HTML с контентом
- ✅ Контент доступен сразу, без JavaScript
- ✅ Хорошая индексация
- ✅ Быстрая первая загрузка

#### SSG в Next.js (ещё лучше для SEO)

```jsx
// pages/products.js
export async function getStaticProps() {
  const res = await fetch('https://api.example.com/products');
  const products = await res.json();

  return {
    props: { products },
    revalidate: 60 // ISR: обновлять каждые 60 секунд
  };
}

export default function Products({ products }) {
  return (
    <div>
      <h1>Наши продукты</h1>
      {products.map(product => (
        <div key={product.id}>
          <h2>{product.name}</h2>
          <p>{product.description}</p>
        </div>
      ))}
    </div>
  );
}
```

**Преимущества SSG:**
- ✅ HTML генерируется на этапе сборки
- ✅ Очень быстрая загрузка (статический HTML)
- ✅ Можно хостить на CDN
- ✅ Отлично для SEO

### Сравнительная таблица

| Характеристика | Чистый React (CSR) | Next.js (SSR/SSG) |
|---------------|-------------------|-------------------|
| **HTML для поисковика** | Пустой `<div id="root"></div>` | Готовый HTML с контентом |
| **Индексация** | ❌ Плохая | ✅ Хорошая |
| **Первая загрузка** | Медленная (нужен JS) | Быстрая (HTML готов) |
| **Работа без JS** | ❌ Нет | ✅ Да (частично) |
| **Мета-теги** | ❌ Динамически через JS | ✅ В HTML |
| **Open Graph** | ❌ Проблемы | ✅ Работает |

### Мета-теги и SEO

#### Чистый React (проблемы)

```jsx
// React Helmet (нужно устанавливать отдельно)
import { Helmet } from 'react-helmet';

function Page() {
  return (
    <>
      <Helmet>
        <title>Моя страница</title>
        <meta name="description" content="Описание" />
      </Helmet>
      <div>Контент</div>
    </>
  );
}
```

**Проблема:** Поисковики могут не увидеть эти теги, если они добавляются через JavaScript.

#### Next.js (работает из коробки)

```jsx
// pages/about.js
import Head from 'next/head';

export default function About() {
  return (
    <>
      <Head>
        <title>О нас</title>
        <meta name="description" content="Описание страницы" />
        <meta property="og:title" content="О нас" />
        <meta property="og:description" content="Описание" />
      </Head>
      <div>Контент</div>
    </>
  );
}
```

**В App Router:**
```jsx
// app/about/page.jsx
export const metadata = {
  title: 'О нас',
  description: 'Описание страницы',
  openGraph: {
    title: 'О нас',
    description: 'Описание'
  }
};

export default function About() {
  return <div>Контент</div>;
}
```

**Преимущества:**
- ✅ Мета-теги в HTML (видны поисковикам)
- ✅ Open Graph работает корректно
- ✅ Правильные title и description

### Структурированные данные (Schema.org)

#### Next.js с JSON-LD

```jsx
// pages/product.js
export default function Product({ product }) {
  const structuredData = {
    "@context": "https://schema.org",
    "@type": "Product",
    "name": product.name,
    "description": product.description,
    "price": product.price
  };

  return (
    <>
      <Head>
        <script
          type="application/ld+json"
          dangerouslySetInnerHTML={{ __html: JSON.stringify(structuredData) }}
        />
      </Head>
      <div>
        <h1>{product.name}</h1>
        <p>{product.description}</p>
      </div>
    </>
  );
}
```

**Преимущества:**
- ✅ Поисковики понимают структуру данных
- ✅ Могут показывать rich snippets в результатах поиска

### Производительность и SEO

Google учитывает скорость загрузки в ранжировании:

#### Чистый React

```
1. Загрузка HTML (пустой)          → 100ms
2. Загрузка JavaScript bundle      → 500ms
3. Выполнение JavaScript           → 200ms
4. Запрос к API                    → 300ms
5. Рендеринг контента              → 100ms
─────────────────────────────────────────
Итого: ~1200ms до появления контента
```

#### Next.js (SSR)

```
1. Загрузка HTML (с контентом)     → 200ms
─────────────────────────────────────────
Итого: ~200ms до появления контента
```

**Преимущества Next.js:**
- ✅ В 6 раз быстрее первая загрузка
- ✅ Лучше для SEO (скорость учитывается)

### Визуальная аналогия

**Чистый React:**
- Поисковик приходит в пустую комнату
- Нужно подождать, пока кто-то включит свет и принесёт мебель
- Может уйти, не дождавшись

**Next.js:**
- Поисковик приходит в обставленную комнату
- Всё уже на месте, можно сразу изучать
- Полная картина доступна сразу

### ⚠️ Частая ошибка

Думают, что SSR автоматически решает все проблемы SEO:

```jsx
// ❌ Неправильно: только SSR недостаточно
export default function Page() {
  return <div>Контент</div>;
  // Нет мета-тегов, нет структурированных данных
}

// ✅ Правильно: SSR + мета-теги + структурированные данные
export default function Page() {
  return (
    <>
      <Head>
        <title>Правильный заголовок</title>
        <meta name="description" content="Правильное описание" />
      </Head>
      <div>Контент</div>
    </>
  );
}
```

### Итоги

- SEO — оптимизация для поисковых систем
- Чистый React плох для SEO (пустой HTML, нужен JS)
- Next.js лучше для SEO (готовый HTML, контент сразу)
- SSR/SSG рендерит HTML на сервере
- Мета-теги работают корректно в Next.js
- Быстрая загрузка улучшает SEO
- SSR + мета-теги + структурированные данные = лучшее SEO
- Next.js даёт преимущества в индексации и ранжировании

---

## 3. Разница pages router vs app router (база)

Next.js предлагает два способа организации маршрутизации: **Pages Router** (старый, стабильный) и **App Router** (новый, на основе React Server Components).

### Простое определение

- **Pages Router** — классический подход Next.js, где каждая страница — это файл в папке `pages/`
- **App Router** — новый подход Next.js 13+, где страницы в папке `app/` с использованием React Server Components

**Аналогия:** 
- **Pages Router** — как "старая библиотека" с книгами на полках (каждая книга = страница)
- **App Router** — как "новая библиотека" с умными полками, которые сами знают, что где лежит

### Pages Router (классический)

#### Структура

```
pages/
  index.js          → /
  about.js          → /about
  blog/
    index.js        → /blog
    [slug].js       → /blog/:slug
  api/
    users.js        → /api/users
```

#### Пример

```jsx
// pages/about.js
export default function About() {
  return <div>О нас</div>;
}

// pages/blog/[slug].js
export async function getStaticPaths() {
  return {
    paths: [{ params: { slug: 'post-1' } }],
    fallback: false
  };
}

export async function getStaticProps({ params }) {
  const post = await fetchPost(params.slug);
  return {
    props: { post }
  };
}

export default function BlogPost({ post }) {
  return <div>{post.title}</div>;
}
```

#### Особенности Pages Router

- ✅ Стабильный и проверенный
- ✅ Простая структура
- ✅ Хорошая документация
- ✅ Все страницы — Client Components по умолчанию
- ⚠️ Нет встроенной поддержки Server Components
- ⚠️ Layouts через `_app.js` и `_document.js`

### App Router (новый)

#### Структура

```
app/
  layout.js         → Общий layout для всех страниц
  page.js           → /
  about/
    page.js         → /about
  blog/
    layout.js       → Layout для /blog/*
    page.js         → /blog
    [slug]/
      page.js       → /blog/:slug
  api/
    users/
      route.js      → /api/users
```

#### Пример

```jsx
// app/layout.js (Server Component по умолчанию)
export default function RootLayout({ children }) {
  return (
    <html lang="ru">
      <body>{children}</body>
    </html>
  );
}

// app/about/page.jsx (Server Component)
export default async function About() {
  const data = await fetch('https://api.example.com/data');
  const json = await data.json();

  return <div>{json.title}</div>;
}

// app/blog/[slug]/page.jsx
export default async function BlogPost({ params }) {
  const post = await fetchPost(params.slug);
  return <div>{post.title}</div>;
}
```

#### Особенности App Router

- ✅ React Server Components из коробки
- ✅ Вложенные layouts
- ✅ Streaming и Suspense
- ✅ Улучшенная производительность
- ✅ Более гибкая структура
- ⚠️ Новее, может быть меньше примеров
- ⚠️ Нужно понимать Server vs Client Components

### Ключевые различия

#### 1. Структура файлов

**Pages Router:**
```
pages/
  index.js
  about.js
```

**App Router:**
```
app/
  page.js
  about/
    page.js
```

#### 2. Data Fetching

**Pages Router:**
```jsx
// pages/about.js
export async function getServerSideProps() {
  const data = await fetchData();
  return { props: { data } };
}

export default function About({ data }) {
  return <div>{data.title}</div>;
}
```

**App Router:**
```jsx
// app/about/page.jsx
export default async function About() {
  const data = await fetchData(); // Прямо в компоненте!
  return <div>{data.title}</div>;
}
```

#### 3. Layouts

**Pages Router:**
```jsx
// pages/_app.js
export default function MyApp({ Component, pageProps }) {
  return (
    <Layout>
      <Component {...pageProps} />
    </Layout>
  );
}
```

**App Router:**
```jsx
// app/layout.js
export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        <Header />
        {children}
        <Footer />
      </body>
    </html>
  );
}

// app/blog/layout.js (вложенный layout)
export default function BlogLayout({ children }) {
  return (
    <div>
      <BlogNav />
      {children}
    </div>
  );
}
```

#### 4. Server vs Client Components

**Pages Router:**
- Все компоненты — Client Components
- Нужно использовать `getServerSideProps` для серверной логики

**App Router:**
- По умолчанию — Server Components
- Нужно явно указывать `'use client'` для Client Components

```jsx
// app/components/ClientButton.jsx
'use client'; // Явно указываем Client Component

import { useState } from 'react';

export default function ClientButton() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

#### 5. API Routes

**Pages Router:**
```jsx
// pages/api/users.js
export default function handler(req, res) {
  res.status(200).json({ users: [...] });
}
```

**App Router:**
```jsx
// app/api/users/route.js
export async function GET() {
  return Response.json({ users: [...] });
}

export async function POST(request) {
  const body = await request.json();
  // ...
  return Response.json({ success: true });
}
```

### Сравнительная таблица

| Характеристика | Pages Router | App Router |
|---------------|--------------|------------|
| **Структура** | `pages/` | `app/` |
| **Файлы страниц** | `index.js`, `about.js` | `page.js` в папках |
| **Data Fetching** | `getServerSideProps`, `getStaticProps` | Прямо в компоненте |
| **Layouts** | `_app.js`, `_document.js` | `layout.js` (вложенные) |
| **Server Components** | ❌ Нет | ✅ Да (по умолчанию) |
| **Client Components** | ✅ Все | ⚠️ Нужно `'use client'` |
| **Streaming** | ❌ Нет | ✅ Да |
| **Стабильность** | ✅ Очень стабильный | ⚠️ Новее |
| **Документация** | ✅ Много примеров | ⚠️ Меньше примеров |

### Когда использовать что?

#### Используй Pages Router если:

- ✅ Нужна максимальная стабильность
- ✅ Команда знакома со старым подходом
- ✅ Много существующего кода на Pages Router
- ✅ Не нужны Server Components

#### Используй App Router если:

- ✅ Начинаешь новый проект
- ✅ Нужны Server Components
- ✅ Хочешь использовать последние возможности Next.js
- ✅ Нужны вложенные layouts
- ✅ Важна производительность (streaming)

### Визуальная аналогия

**Pages Router:**
- Классическая библиотека
- Каждая книга на своей полке
- Просто и понятно
- Проверено временем

**App Router:**
- Современная библиотека
- Умные полки с автоматической сортировкой
- Более гибко
- Новые возможности

### ⚠️ Частая ошибка

Путают структуру файлов:

```jsx
// ❌ Неправильно в App Router
app/
  about.js  // Должно быть about/page.js

// ✅ Правильно в App Router
app/
  about/
    page.js
```

**Или наоборот:**

```jsx
// ❌ Неправильно в Pages Router
pages/
  about/
    page.js  // Должно быть about.js

// ✅ Правильно в Pages Router
pages/
  about.js
```

### Можно ли использовать оба?

Технически можно, но **не рекомендуется**:

```
pages/
  old-page.js
app/
  new-page/
    page.js
```

**Проблемы:**
- Путаница в структуре
- Разные подходы к data fetching
- Сложнее поддерживать

**Рекомендация:** Выбери один подход и придерживайся его.

### Миграция с Pages на App Router

Постепенная миграция возможна:

1. Начни с новых страниц в `app/`
2. Постепенно переноси старые страницы
3. Используй оба подхода временно
4. Полностью перейди на App Router

### Итоги

- Pages Router — классический подход (`pages/`)
- App Router — новый подход (`app/`)
- Pages Router: стабильный, простой, все компоненты клиентские
- App Router: Server Components, вложенные layouts, streaming
- Data fetching: `getServerSideProps` vs прямо в компоненте
- Выбор зависит от проекта и требований
- Можно использовать оба, но не рекомендуется
- App Router — будущее Next.js, но Pages Router всё ещё актуален
