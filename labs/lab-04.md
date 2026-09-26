# Лабораторна робота 04 Реалізація React-проєкту

## 🎯 Мета роботи

Здобути практичні навички створення сучасного React проєкту з використанням TypeScript, налаштування інструментів розробки та побудови базової архітектури клієнтської частини застосунку з використанням Tailwind CSS для стилізації.

## ✅ Завдання

### Загальний контекст

Ця лабораторна робота є першою в блоці розробки клієнтської частини та безпосередньо продовжує роботу над проєктом, розпочату в лабораторних роботах 1–3. Здобувачі освіти створюють клієнтську частину для серверної системи, яку вже розробили раніше.

### Технічні завдання

**Рівень 1. Базова конфігурація проєкту**

1. Створити новий React проєкт з використанням Vite та TypeScript.
2. Налаштувати Tailwind CSS для стилізації компонентів.
3. Встановити та налаштувати ESLint і Prettier для забезпечення якості коду.
4. Налаштувати React Router для маршрутизації застосунку.
5. Інтегрувати Axios для HTTP-запитів до API серверної частини.
6. Створити базову структуру папок проєкту.
7. Реалізувати адаптивний макет (responsive layout) з використанням утилітарних класів Tailwind.

**Рівень 2. Базові компоненти та навігація**

8. Створити набір повторно використовуваних UI-компонентів: Button, Input, Card, Badge.
9. Розробити компонент Header з навігаційним меню.
10. Реалізувати компонент Footer з контактною інформацією.
11. Створити Layout компонент для єдиної структури сторінок.
12. Налаштувати маршрутизацію для основних сторінок: Home, About, NotFound.
13. Впровадити систему кольорів та типографіки через тему Tailwind (директива `@theme`).
14. Створити компонент Loading для індикації завантаження.

**Рівень 3. Розширена архітектура**

15. Налаштувати змінні середовища для різних режимів роботи.
16. Створити власні типи TypeScript для відповідей API.
17. Реалізувати interceptors (перехоплювачі) Axios для обробки запитів та відповідей.
18. Впровадити Error Boundary для обробки помилок відтворення React.
19. Налаштувати абсолютні імпорти (псевдонім `@/`) через `paths` у tsconfig та Vite.
20. Створити допоміжні функції для форматування даних.
21. Додати темну тему за допомогою варіанта `dark` у Tailwind.

### Результат виконання

Після завершення лабораторної роботи здобувач освіти матиме повністю налаштований React-проєкт з TypeScript, готовий до розробки функціональних компонентів, з правильною структурою папок, налаштованими інструментами розробки та базовим набором повторно використовуваних UI-компонентів.

## 👥 Форма виконання роботи

Форма виконання роботи **індивідуальна**.

## 📝 Критерії оцінювання

**Середній рівень (оцінка "задовільно")**

- Створено базовий React проєкт з Vite та TypeScript.
- Встановлено Tailwind CSS з мінімальною конфігурацією.
- Налаштовано базову маршрутизацію з 2–3 сторінками.
- Створено 2-3 простих компоненти.
- Код має недоліки у структурі та організації.
- Відсутня або неповна документація.
- Не використовуються TypeScript типи.
- Мінімальна стилізація без адаптивності.

**Достатній рівень (оцінка "добре")**

- Правильно налаштовано проєкт з усіма необхідними інструментами.
- Створено базові повторно використовувані компоненти з Tailwind.
- Налаштовано ESLint та Prettier з базовими правилами.
- Реалізовано адаптивний дизайн для основних компонентів.
- Використовуються TypeScript типи для пропсів компонентів.
- Налаштовано Axios з базовою конфігурацією.
- Створено документацію для основних компонентів.
- Код організовано у логічні папки.

**Високий рівень (оцінка "відмінно")**

- Повністю виконано всі завдання трьох рівнів.
- Створено розширений набір повторно використовуваних компонентів.
- Налаштовано детальну TypeScript конфігурацію.
- Впроваджено interceptors та централізовану обробку помилок.
- Реалізовано темну тему та налаштовано власну тему Tailwind.
- Створено допоміжні функції та модулі.
- Код відповідає принципам чистого коду та SOLID.
- Повна документація проєкту з прикладами використання.
- Продемонстровано глибоке розуміння React, TypeScript та сучасних практик розробки.

## ⏰ Політика щодо дедлайнів

При порушенні встановленого терміну здачі лабораторної роботи максимальна можлива оцінка становить "добре", незалежно від якості виконаної роботи. Винятки можливі лише за поважних причин, підтверджених документально.

## 📚 Теоретичні відомості

### Vite

**Vite** — це сучасний інструмент для побудови клієнтської частини вебзастосунків, який використовує нативні ES-модулі браузера під час розробки. Основні переваги Vite: миттєвий запуск сервера розробки, надшвидка гаряча заміна модулів (HMR) та оптимізована збірка для продакшену. На відміну від традиційних збирачів на кшталт Webpack, Vite не збирає весь код під час розробки, що значно прискорює процес. Create React App офіційно визнано застарілим, і для нових React-проєктів рекомендують саме Vite або повноцінні фреймворки (наприклад, Next.js).

Vite підтримує TypeScript, JSX, CSS-модулі та інші сучасні технології без додаткового налаштування. Під час збірки Vite 8 використовує збирач Rolldown, написаний мовою Rust, для створення оптимізованих пакетів із розбиттям коду (code splitting), вилученням невикористаного коду (tree shaking) та відкладеним завантаженням (lazy loading). Vite 8 потребує Node.js 20.19+ або 22.12+; у курсі використовується Node.js 24 LTS.

### TypeScript у React

**TypeScript** надає статичну типізацію для JavaScript, що дозволяє виявляти помилки на етапі розробки та покращує підтримку коду. У React-проєктах TypeScript особливо корисний для типізації пропсів компонентів, стану та обробників подій.

Основні можливості TypeScript у React: сувора перевірка типів пропсів, автодоповнення в IDE, безпечний рефакторинг та самодокументування коду через типи. Інтерфейси й типи TypeScript слугують контрактами між компонентами, що робить код передбачуванішим і легшим у підтримці. Шаблон Vite використовує TypeScript 6 і розділяє налаштування на `tsconfig.app.json` (код застосунку) та `tsconfig.node.json` (конфігурація збірки).

### Tailwind CSS

**Tailwind CSS** — це утилітарний (utility-first) CSS-фреймворк, який надає низькорівневі класи для побудови власних дизайнів безпосередньо в розмітці. На відміну від компонентних фреймворків на кшталт Bootstrap, Tailwind не нав'язує певний дизайн, а надає інструменти для створення власного.

Основні переваги Tailwind: швидка розробка завдяки готовим утилітарним класам, відсутність конфліктів імен класів, зручне налаштування теми та вбудована підтримка адаптивного дизайну, темної теми й анімацій. Починаючи з версії 4, налаштування виконується безпосередньо в CSS: підключення `@import "tailwindcss";`, теми задаються директивою `@theme`, а окремий файл `tailwind.config.js`, PostCSS та автопрефіксер не потрібні. Tailwind сам сканує файли проєкту й генерує лише використані стилі. Версія 4 орієнтована на сучасні браузери (Safari 16.4+, Chrome 111+, Firefox 128+).

### React Router

**React Router** — це стандартна бібліотека маршрутизації для React-застосунків. Вона дозволяє створювати односторінкові застосунки (SPA) з навігацією між сторінками без перезавантаження браузера.

React Router надає компоненти для декларативного визначення маршрутів, програмної навігації, параметрів URL та вкладених маршрутів (компонент `Outlet` відображає дочірній маршрут усередині батьківського). У курсі використовується React Router 8, який вимагає React 19.2.7+ та Node.js 22.22+. Усі компоненти й хуки імпортуються з пакета `react-router`; окремий пакет `react-router-dom` у версії 8 вилучено.

### Axios

**Axios** — це популярна HTTP-бібліотека для браузера та Node.js, яка надає зручний API для виконання HTTP-запитів. Основні переваги Axios над нативним `fetch`: автоматичне перетворення JSON, interceptors для запитів та відповідей, скасування запитів і зручніша обробка помилок.

Interceptors Axios дозволяють глобально обробляти всі запити та відповіді, що корисно для додавання токенів автентифікації, обробки помилок та журналювання. Axios також підтримує одночасні запити, тайм-аути та перетворення даних.

### ESLint та Prettier

**ESLint** — це інструмент статичного аналізу коду JavaScript та TypeScript, який виявляє проблеми в коді та забезпечує дотримання стандартів кодування. Починаючи з версії 10, ESLint підтримує лише «плоский» формат конфігурації (`eslint.config.js`), а старі файли `.eslintrc` більше не читаються. Для TypeScript використовується пакет `typescript-eslint`.

**Prettier** — це форматувальник коду з наперед заданим стилем, який автоматично форматує код згідно з визначеними правилами. Prettier підтримує JavaScript, TypeScript, CSS, JSON та інші формати. Щоб уникнути конфліктів між правилами лінтингу та форматування, використовують пакет `eslint-config-prettier`, який вимикає правила ESLint, що суперечать Prettier; сам Prettier запускають окремою командою.

Шаблон Vite за замовчуванням постачається з лінтером **oxlint** (дуже швидким аналогом ESLint, написаним на Rust). У цій лабораторній роботі ми замінюємо його на ESLint, який має ширшу екосистему плагінів і є галузевим стандартом.

### Компонентна архітектура

**Компонентна архітектура** у React передбачає розбиття користувацького інтерфейсу на незалежні повторно використовувані компоненти. Кожен компонент інкапсулює свою логіку, стан та відображення, що полегшує розробку, тестування та підтримку.

Принципи компонентної архітектури: єдина відповідальність кожного компонента, композиція замість наслідування, використання пропсів для передачі даних та подій для комунікації між компонентами. Правильна організація компонентів включає розділення на презентаційні та контейнерні компоненти, створення повторно використовуваних UI-компонентів та дотримання принципу DRY.

## 🔗 Додаткові ресурси

- [Vite офіційна документація](https://vite.dev/)
- [React TypeScript Cheatsheet](https://react-typescript-cheatsheet.netlify.app/)
- [Tailwind CSS документація](https://tailwindcss.com/docs)
- [React Router документація](https://reactrouter.com/)
- [Axios документація](https://axios-http.com/)
- [ESLint: конфігурація](https://eslint.org/docs/latest/use/configure/)
- [typescript-eslint](https://typescript-eslint.io/)
- [Prettier документація](https://prettier.io/)

## ▶️ Хід роботи

### Крок 1. Створення проєкту з Vite

Переконайтеся, що встановлено Node.js 24 LTS (`node -v`). Відкрийте термінал та виконайте команду для створення нового проєкту з шаблоном Vite для React та TypeScript.

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
```

Вивчіть створену структуру проєкту та ознайомтеся з файлами конфігурації `vite.config.ts`, `tsconfig.app.json`, `tsconfig.node.json` та `package.json`. Зверніть увагу, що шаблон уже містить лінтер oxlint (скрипт `npm run lint`): на кроці 3 ми замінимо його на ESLint. Запустіть `npm run dev` та переконайтеся, що застосунок відкривається за адресою, яку виводить Vite.

### Крок 2. Встановлення Tailwind CSS

Встановіть Tailwind CSS та офіційний плагін для Vite. Окремі пакети PostCSS та автопрефіксера, а також команда `npx tailwindcss init` у версії 4 не потрібні.

```bash
npm install tailwindcss @tailwindcss/vite
```

Підключіть плагін у файлі `vite.config.ts`. Тут же вмикається підтримка абсолютних імпортів через `paths` з `tsconfig` (див. крок 5).

```typescript
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import tailwindcss from '@tailwindcss/vite';

export default defineConfig({
  plugins: [react(), tailwindcss()],
  resolve: { tsconfigPaths: true },
});
```

Замініть увесь вміст файлу `src/index.css` єдиним рядком імпорту Tailwind та видаліть файл `src/App.css` разом з демонстраційним вмістом `App.tsx`, який ви заміните на кроці 9.

```css
@import "tailwindcss";
```

Перевірте, що утилітарні класи працюють: додайте до будь-якого елемента `className="text-3xl font-bold text-blue-600"` і подивіться результат у браузері.

### Крок 3. Налаштування ESLint та Prettier

Видаліть oxlint з шаблону та встановіть ESLint з необхідними плагінами й Prettier.

```bash
npm uninstall oxlint
npm install -D eslint @eslint/js typescript-eslint eslint-plugin-react-hooks eslint-plugin-react-refresh globals
npm install -D prettier eslint-config-prettier
```

Створіть файл `eslint.config.js` (формат «плоскої» конфігурації; файл `.eslintrc.cjs` ESLint 10 не читає).

```javascript
import js from '@eslint/js';
import globals from 'globals';
import reactHooks from 'eslint-plugin-react-hooks';
import reactRefresh from 'eslint-plugin-react-refresh';
import tseslint from 'typescript-eslint';
import prettier from 'eslint-config-prettier';
import { defineConfig, globalIgnores } from 'eslint/config';

export default defineConfig([
  globalIgnores(['dist']),
  {
    files: ['**/*.{ts,tsx}'],
    extends: [
      js.configs.recommended,
      tseslint.configs.recommended,
      reactHooks.configs.flat.recommended,
      reactRefresh.configs.vite,
      prettier,
    ],
    languageOptions: { ecmaVersion: 2023, globals: globals.browser },
  },
]);
```

Створіть файл `.prettierrc` з правилами форматування.

```json
{
  "semi": true,
  "singleQuote": true,
  "trailingComma": "es5",
  "printWidth": 100,
  "tabWidth": 2
}
```

У файлі `package.json` змініть скрипт `lint` та додайте скрипт форматування:

```json
{
  "scripts": {
    "lint": "eslint .",
    "format": "prettier --write src"
  }
}
```

Запустіть `npm run lint` та `npm run format` і переконайтеся, що помилок немає.

### Крок 4. Встановлення React Router та Axios

Встановіть необхідні пакети для маршрутизації та HTTP-запитів. Типи для `react-router` та `axios` входять до складу самих пакетів.

```bash
npm install react-router axios
```

### Крок 5. Створення структури папок

Створіть таку структуру папок у директорії `src`:

```
src/
├── components/      # Повторно використовувані UI-компоненти
│   ├── common/     # Базові компоненти (Button, Input)
│   └── layout/     # Компоненти розмітки (Header, Footer)
├── pages/          # Компоненти сторінок
├── services/       # API-сервіси та налаштування Axios
├── types/          # Типи та інтерфейси TypeScript
├── utils/          # Допоміжні функції
├── hooks/          # Власні хуки React
└── constants/      # Константи застосунку
```

Налаштуйте абсолютні імпорти з псевдонімом `@/`, який вказує на папку `src`. Для цього додайте `paths` до `tsconfig.app.json` (плагін Vite вже підключено на кроці 2). Параметр `baseUrl` у TypeScript 6 застарів, тому використовувати його не потрібно.

```json
{
  "compilerOptions": {
    "paths": { "@/*": ["./src/*"] }
  }
}
```

Тепер замість `import Button from '../../components/common/Button'` можна писати `import Button from '@/components/common/Button'`.

### Крок 6. Налаштування Axios

Створіть файл `src/services/api.ts` з базовою конфігурацією Axios. Токен доступу, який повертає серверна частина з лабораторної роботи 2, зберігається під ключем `accessToken`. У лабораторній роботі 5 це сховище буде замінено на стан застосунку.

```typescript
import axios from 'axios';

const api = axios.create({
  baseURL: import.meta.env.VITE_API_URL ?? 'http://localhost:3000/api',
  headers: { 'Content-Type': 'application/json' },
});

// Interceptor запиту: додає токен доступу до кожного запиту
api.interceptors.request.use((config) => {
  const token = localStorage.getItem('accessToken');
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});

// Interceptor відповіді: при 401 очищає токен і повертає на сторінку входу
api.interceptors.response.use(
  (response) => response,
  (error) => {
    if (error.response?.status === 401) {
      localStorage.removeItem('accessToken');
      window.location.href = '/login';
    }
    return Promise.reject(error);
  }
);

export default api;
```

### Крок 7. Створення базових UI-компонентів

Створіть повторно використовуваний компонент `Button` у файлі `src/components/common/Button.tsx`. Тип `ComponentProps<'button'>` містить усі стандартні атрибути кнопки.

```typescript
import type { ComponentProps } from 'react';

interface ButtonProps extends ComponentProps<'button'> {
  variant?: 'primary' | 'secondary' | 'danger';
  size?: 'sm' | 'md' | 'lg';
  isLoading?: boolean;
}

const variantClasses = {
  primary: 'bg-primary-600 hover:bg-primary-700 text-white',
  secondary:
    'bg-gray-200 hover:bg-gray-300 text-gray-800 dark:bg-gray-700 dark:hover:bg-gray-600 dark:text-gray-100',
  danger: 'bg-red-600 hover:bg-red-700 text-white',
};

const sizeClasses = {
  sm: 'px-3 py-1.5 text-sm',
  md: 'px-4 py-2',
  lg: 'px-6 py-3 text-lg',
};

export default function Button({
  variant = 'primary',
  size = 'md',
  isLoading = false,
  className = '',
  disabled,
  children,
  ...props
}: ButtonProps) {
  return (
    <button
      className={`rounded font-medium transition-colors duration-200 disabled:opacity-50 ${variantClasses[variant]} ${sizeClasses[size]} ${className}`}
      disabled={isLoading || disabled}
      {...props}
    >
      {isLoading ? 'Завантаження...' : children}
    </button>
  );
}
```

Створіть компонент `Input`. У React 19 атрибут `ref` передається як звичайний проп, тому обгортка `forwardRef` не потрібна. Це знадобиться в лабораторній роботі 5 для бібліотеки React Hook Form.

```typescript
import type { ComponentProps } from 'react';

// У React 19 ref передається як звичайний проп, тому forwardRef не потрібен.
// Це важливо для React Hook Form, який зберігає ref поля через register().
export default function Input({ className = '', ...props }: ComponentProps<'input'>) {
  return (
    <input
      className={`w-full rounded border border-gray-300 px-3 py-2 focus:outline-none focus:ring-2 focus:ring-primary-500 dark:border-gray-600 dark:bg-gray-800 ${className}`}
      {...props}
    />
  );
}
```

Аналогічно створіть компоненти `Card`, `Badge`, `Loading` та інші базові UI-елементи.

### Крок 8. Створення компонентів розмітки

Створіть компонент `Header` у файлі `src/components/layout/Header.tsx`. Кнопку перемикання теми буде підключено на кроці 12.

```typescript
import { Link } from 'react-router';
import { useTheme } from '@/hooks/useTheme';

export default function Header() {
  const { theme, toggleTheme } = useTheme();

  return (
    <header className="bg-white shadow-md dark:bg-gray-900">
      <nav className="container mx-auto flex items-center justify-between px-4 py-4">
        <Link to="/" className="text-2xl font-bold text-primary-600">
          MyApp
        </Link>
        <div className="flex items-center gap-6">
          <Link to="/" className="hover:text-primary-600">
            Головна
          </Link>
          <Link to="/about" className="hover:text-primary-600">
            Про нас
          </Link>
          <button type="button" onClick={toggleTheme} aria-label="Перемкнути тему">
            {theme === 'dark' ? '☀️' : '🌙'}
          </button>
        </div>
      </nav>
    </header>
  );
}
```

Створіть компонент `Footer` з контактною інформацією.

```typescript
export default function Footer() {
  return (
    <footer className="bg-gray-100 py-6 text-center text-sm text-gray-600 dark:bg-gray-900 dark:text-gray-400">
      <p>© {new Date().getFullYear()} MyApp. Контакти: info@example.com</p>
    </footer>
  );
}
```

Створіть компонент `Layout` у файлі `src/components/layout/Layout.tsx`.

```typescript
import { Outlet } from 'react-router';
import Header from './Header';
import Footer from './Footer';

export default function Layout() {
  return (
    <div className="flex min-h-screen flex-col bg-white text-gray-900 dark:bg-gray-950 dark:text-gray-100">
      <Header />
      <main className="container mx-auto flex-grow px-4 py-8">
        <Outlet />
      </main>
      <Footer />
    </div>
  );
}
```

### Крок 9. Налаштування React Router

Створіть сторінки `HomePage`, `AboutPage` та `NotFoundPage` у папці `src/pages` (для початку достатньо заголовка `<h1>` на кожній) і налаштуйте маршрутизацію у файлі `src/App.tsx`. Усі імпорти беруться з пакета `react-router`.

```typescript
import { BrowserRouter, Route, Routes } from 'react-router';
import Layout from '@/components/layout/Layout';
import HomePage from '@/pages/HomePage';
import AboutPage from '@/pages/AboutPage';
import NotFoundPage from '@/pages/NotFoundPage';

export default function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Layout />}>
          <Route index element={<HomePage />} />
          <Route path="about" element={<AboutPage />} />
          <Route path="*" element={<NotFoundPage />} />
        </Route>
      </Routes>
    </BrowserRouter>
  );
}
```

Файл `src/main.tsx` лише відтворює `App` у DOM:

```typescript
import { StrictMode } from 'react';
import { createRoot } from 'react-dom/client';
import App from './App';
import './index.css';

createRoot(document.getElementById('root')!).render(
  <StrictMode>
    <App />
  </StrictMode>
);
```

### Крок 10. Створення типів TypeScript

Створіть файл `src/types/api.types.ts` з типами для відповідей API. Формат пагінованої відповіді збігається з тим, який повертає серверна частина в лабораторній роботі 2.

```typescript
export interface User {
  id: number;
  name: string;
  email: string;
  role: 'USER' | 'ADMIN' | 'MODERATOR';
  createdAt: string;
}

export interface Pagination {
  page: number;
  limit: number;
  total: number;
  totalPages: number;
  hasMore: boolean;
}

// Формат відповіді серверної частини з лабораторної роботи 2
export interface PaginatedResponse<T> {
  data: T[];
  pagination: Pagination;
}

export interface ApiError {
  error: string;
}
```

Створіть також допоміжні функції форматування в `src/utils/format.ts`:

```typescript
export function formatDate(iso: string, locale = 'uk-UA'): string {
  return new Intl.DateTimeFormat(locale, { dateStyle: 'long' }).format(new Date(iso));
}

export function formatPrice(value: number, currency = 'UAH', locale = 'uk-UA'): string {
  return new Intl.NumberFormat(locale, { style: 'currency', currency }).format(value);
}
```

Додайте Error Boundary, який перехоплює помилки відтворення дочірніх компонентів і показує запасний інтерфейс замість «білого екрана». Error Boundary досі реалізується лише класовим компонентом. Обгорніть ним `<Outlet />` у `Layout`.

```typescript
import { Component, type ErrorInfo, type ReactNode } from 'react';

interface Props {
  children: ReactNode;
  fallback?: ReactNode;
}

interface State {
  hasError: boolean;
}

// Error Boundary досі реалізується лише класовим компонентом
export default class ErrorBoundary extends Component<Props, State> {
  state: State = { hasError: false };

  static getDerivedStateFromError(): State {
    return { hasError: true };
  }

  componentDidCatch(error: Error, info: ErrorInfo) {
    console.error('Помилка відтворення:', error, info.componentStack);
  }

  render() {
    return this.state.hasError
      ? (this.props.fallback ?? <p role="alert">Щось пішло не так. Оновіть сторінку.</p>)
      : this.props.children;
  }
}
```

### Крок 11. Налаштування змінних середовища

Створіть файл `.env` у корені проєкту.

```
VITE_API_URL=http://localhost:3000/api
VITE_APP_NAME=MyApp
```

Створіть файл `.env.example` з прикладами змінних (без реальних значень, якщо вони конфіденційні). Vite передає в код лише змінні з префіксом `VITE_`, і вони потрапляють у кінцевий пакет, який може прочитати будь-хто. Тому ніколи не зберігайте в них секрети (ключі, паролі). Для різних режимів використовуйте файли `.env.development` та `.env.production`.

### Крок 12. Додавання темної теми

У Tailwind 4 темна тема налаштовується в CSS. Оголосіть варіант `dark`, що спрацьовує за наявності класу `dark` на елементі `<html>`, і задайте кольори власної теми директивою `@theme`. Доповніть `src/index.css`:

```css
@import "tailwindcss";

/* Перемикання темної теми класом .dark на елементі <html> */
@custom-variant dark (&:where(.dark, .dark *));

@theme {
  --color-primary-50: #eff6ff;
  --color-primary-100: #dbeafe;
  --color-primary-500: #3b82f6;
  --color-primary-600: #2563eb;
  --color-primary-700: #1d4ed8;
  --color-primary-900: #1e3a8a;
  --font-sans: "Inter", system-ui, sans-serif;
}
```

Без директиви `@custom-variant` класи `dark:...` реагуватимуть лише на системну тему, а не на клас на `<html>`.

Створіть хук `src/hooks/useTheme.ts`, який визначає початкову тему (збережений вибір або системне налаштування), перемикає клас `dark` на `<html>` та запам'ятовує вибір:

```typescript
import { useEffect, useState } from 'react';

type Theme = 'light' | 'dark';

function getInitialTheme(): Theme {
  const saved = localStorage.getItem('theme');
  if (saved === 'light' || saved === 'dark') return saved;
  return window.matchMedia('(prefers-color-scheme: dark)').matches ? 'dark' : 'light';
}

export function useTheme() {
  const [theme, setTheme] = useState<Theme>(getInitialTheme);

  useEffect(() => {
    document.documentElement.classList.toggle('dark', theme === 'dark');
    localStorage.setItem('theme', theme);
  }, [theme]);

  const toggleTheme = () => setTheme((t) => (t === 'dark' ? 'light' : 'dark'));

  return { theme, toggleTheme };
}
```

Тепер у компонентах використовуйте класи на кшталт `bg-white dark:bg-gray-950` (див. `Layout` і `Header`), а кнопка в `Header` перемикає тему.

### Крок 13. Оформлення README.md

Створити або оновити файл `README.md` у корені проєкту з наступною структурою:

````markdown
# Назва проєкту

## Опис проєкту
Короткий опис обраної предметної області та функціональності застосунку.

## Технології
- React 19
- TypeScript
- Vite
- Tailwind CSS 4
- React Router 8
- Axios
- ESLint + Prettier

## Встановлення та запуск

### Вимоги
- Node.js 24 LTS
- npm

### Інструкції
```bash
# Клонування репозиторію
git clone [URL репозиторію]

# Встановлення залежностей
npm install

# Копіювання .env файлу
cp .env.example .env

# Запуск у режимі розробки
npm run dev

# Збірка для продакшену
npm run build
```

## Структура проєкту
```
src/
├── components/      # Повторно використовувані UI-компоненти
├── pages/          # Компоненти сторінок
├── services/       # API-сервіси
├── types/          # Типи TypeScript
├── utils/          # Допоміжні функції
└── hooks/          # Власні хуки React
```

## Скріншоти
[Додати скріншоти головної сторінки]

## Автор
[Ваше ім'я, група]
````


### Крок 14. Здача роботи

Зробити коміт усіх змін до GitHub репозиторію з описовим повідомленням. Переконатися, що:
- файл `.env` додано до `.gitignore`, а в репозиторії є `.env.example`;
- README.md містить всю необхідну інформацію;
- у README.md додані скріншоти.

Здати роботу через Moodle, вставивши посилання на GitHub репозиторій.

[⬆️ Здати лабораторну роботу](https://moodle.vcolnuft.volyn.ua/moodle/course/view.php?id=17#section-2)


## ❓ Контрольні запитання

1. Які переваги має Vite порівняно зі збирачами на основі повного бандлінгу (Webpack, Create React App)? Поясніть концепцію ES-модулів у браузері.
2. Що таке інтерфейси TypeScript та як вони використовуються для типізації пропсів компонентів?
3. Поясніть концепцію utility-first CSS та основні переваги Tailwind CSS. Що змінилося в налаштуванні Tailwind 4?
4. Як працюють interceptors в Axios? Наведіть приклади їх використання.
5. Що таке `Outlet` у React Router та як він використовується для вкладених маршрутів?
6. Поясніть різницю між ESLint та Prettier. Як уникнути конфліктів між ними?
7. Що таке змінні середовища та чому важливо не додавати файл `.env` до репозиторію? Чому в змінних із префіксом `VITE_` не можна зберігати секрети?
8. Яких принципів слід дотримуватися при створенні повторно використовуваних компонентів?
9. Поясніть поняття адаптивного дизайну (responsive design) та як Tailwind спрощує його реалізацію.
10. Як організувати структуру папок у великому React-проєкті? Які підходи ви знаєте?
