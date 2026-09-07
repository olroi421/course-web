# Лабораторна робота 4 Реалізація React проєкту

## 🎯 Мета роботи

Здобути практичні навички створення сучасного React проєкту з використанням TypeScript, налаштування інструментів розробки та побудови базової архітектури frontend застосунку з використанням Tailwind CSS для стилізації.

## ✅ Завдання

### Загальний контекст

Ця лабораторна робота є першою у блоці frontend розробки та безпосередньо продовжує роботу над проєктом, розпочату в лабораторних роботах 1-3. Здобувачі освіти створюють клієнтську частину для backend системи, яку вже розробили раніше.

### Технічні завдання

**Рівень 1. Базова конфігурація проєкту**

1. Створити новий React проєкт з використанням Vite та TypeScript.
2. Налаштувати Tailwind CSS для стилізації компонентів.
3. Встановити та налаштувати ESLint і Prettier для забезпечення якості коду.
4. Налаштувати React Router для маршрутизації застосунку.
5. Інтегрувати Axios для HTTP запитів до backend API.
6. Створити базову структуру папок проєкту.
7. Реалізувати responsive layout з використанням Tailwind utilities.

**Рівень 2. Базові компоненти та навігація**

8. Створити набір переусних UI компонентів: Button, Input, Card, Badge.
9. Розробити компонент Header з навігаційним меню.
10. Реалізувати компонент Footer з контактною інформацією.
11. Створити Layout компонент для єдиної структури сторінок.
12. Налаштувати routing для основних сторінок: Home, About, NotFound.
13. Впровадити систему кольорів та типографіки через Tailwind config.
14. Створити Loading компонент для індикації завантаження.

**Рівень 3. Розширена архітектура**

15. Налаштувати environment variables для різних режимів роботи.
16. Створити кастомні TypeScript типи для API відповідей.
17. Реалізувати Axios interceptors для обробки запитів та відповідей.
18. Впровадити Error Boundary для обробки помилок React.
19. Налаштувати абсолютні імпорти через tsconfig.
20. Створити utility функції для форматування даних.
21. Додати темну тему через Tailwind dark mode.

### Результат виконання

Після завершення лабораторної роботи здобувач освіти матиме повністю налаштований React проєкт з TypeScript, готовий до розробки функціональних компонентів, з правильною структурою папок, налаштованими інструментами розробки та базовим набором переусних UI компонентів.

## 👥 Форма виконання роботи

Форма виконання роботи **індивідуальна**.

## 📝 Критерії оцінювання

**Середній рівень (оцінка "задовільно")**

- Створено базовий React проєкт з Vite та TypeScript.
- Встановлено Tailwind CSS з мінімальною конфігурацією.
- Налаштовано базовий routing з 2-3 сторінками.
- Створено 2-3 простих компоненти.
- Код має недоліки у структурі та організації.
- Відсутня або неповна документація.
- Не використовуються TypeScript типи.
- Мінімальна стилізація без адаптивності.

**Достатній рівень (оцінка "добре")**

- Правильно налаштовано проєкт з усіма необхідними інструментами.
- Створено базові переусні компоненти з Tailwind.
- Налаштовано ESLint та Prettier з базовими правилами.
- Реалізовано responsive дизайн для основних компонентів.
- Використовуються TypeScript типи для пропсів компонентів.
- Налаштовано Axios з базовою конфігурацією.
- Створено документацію для основних компонентів.
- Код організовано у логічні папки.

**Високий рівень (оцінка "відмінно")**

- Повністю виконано всі завдання трьох рівнів.
- Створено розширений набір переусних компонентів.
- Налаштовано детальну TypeScript конфігурацію.
- Впроваджено interceptors та централізовану обробку помилок.
- Реалізовано темну тему та налаштовано кастомну Tailwind конфігурацію.
- Створено utility функції та helper модулі.
- Код відповідає принципам чистого коду та SOLID.
- Повна документація проєкту з прикладами використання.
- Продемонстровано глибоке розуміння React, TypeScript та сучасних практик розробки.

## ⏰ Політика щодо дедлайнів

При порушенні встановленого терміну здачі лабораторної роботи максимальна можлива оцінка становить "добре", незалежно від якості виконаної роботи. Винятки можливі лише за поважних причин, підтверджених документально.

## 📚 Теоретичні відомості

### Vite

**Vite** — це сучасний інструмент для побудови frontend проєктів, який використовує нативні ES модулі браузера під час розробки. Основні переваги Vite включають миттєвий запуск сервера розробки, надшвидку гарячу заміну модулів HMR та оптимізовану збірку для продакшену. На відміну від традиційних bundler-ів як Webpack, Vite не збирає весь код під час розробки, що значно прискорює процес.

Vite підтримує TypeScript, JSX, CSS modules та інші сучасні технології без додаткового налаштування. Під час збірки Vite використовує Rollup для створення оптимізованих бандлів з code splitting, tree shaking та lazy loading.

### TypeScript у React

**TypeScript** надає статичну типізацію для JavaScript, що дозволяє виявляти помилки на етапі розробки та покращує підтримку коду. У React проєктах TypeScript особливо корисний для типізації пропсів компонентів, стану та event handler-ів.

Основні можливості TypeScript у React включають строгу перевірку типів пропсів, автодоповнення в IDE, рефакторинг з впевненістю та самодокументацію коду через типи. TypeScript інтерфейси та типи служать як контракти між компонентами, що робить код більш передбачуваним та легким у підтримці.

### Tailwind CSS

**Tailwind CSS** — це utility-first CSS фреймворк, який надає низькорівневі utility класи для побудови кастомних дизайнів безпосередньо в HTML. На відміну від компонентних фреймворків як Bootstrap, Tailwind не нав'язує певний дизайн, а надає інструменти для створення власного.

Основні переваги Tailwind включають швидку розробку через готові utility класи, відсутність конфліктів імен класів, легку кастомізацію через конфігураційний файл та автоматичне видалення невикористаних стилів у продакшені через PurgeCSS. Tailwind також має вбудовану підтримку responsive дизайну, темної теми та анімацій.

### React Router

**React Router** — це стандартна бібліотека для маршрутизації у React застосунках. Вона дозволяє створювати Single Page Applications з навігацією між сторінками без перезавантаження браузера.

React Router надає компоненти для декларативного визначення маршрутів, програмної навігації, параметрів URL та вкладених маршрутів. Версія 6 спростила API та додала нові можливості як outlet для вкладених маршрутів та покращену типізацію для TypeScript.

### Axios

**Axios** — це популярна HTTP бібліотека для браузера та Node.js, яка надає зручний API для виконання HTTP запитів. Основні переваги Axios над нативним fetch включають автоматичне перетворення JSON, interceptors для запитів та відповідей, скасування запитів та кращу обробку помилок.

Axios interceptors дозволяють глобально обробляти всі запити та відповіді, що корисно для додавання токенів автентифікації, обробки помилок та логування. Axios також підтримує одночасні запити, timeout та трансформацію даних.

### ESLint та Prettier

**ESLint** — це інструмент для статичного аналізу коду JavaScript та TypeScript, який виявляє проблеми в коді та забезпечує дотримання стандартів кодування. ESLint можна налаштувати через конфігураційний файл для визначення правил, які відповідають стилю команди.

**Prettier** — це opinionated форматувач коду, який автоматично форматує код згідно з визначеними правилами. Prettier підтримує JavaScript, TypeScript, CSS, JSON та інші формати. Інтеграція ESLint з Prettier через плагіни дозволяє уникнути конфліктів між правилами лінтингу та форматування.

### Компонентна архітектура

**Компонентна архітектура** у React передбачає розбиття користувацького інтерфейсу на незалежні переусні компоненти. Кожен компонент інкапсулює свою логіку, стан та відображення, що полегшує розробку, тестування та підтримку.

Принципи компонентної архітектури включають єдину відповідальність кожного компонента, композицію замість наслідування, використання пропсів для передачі даних та events для комунікації між компонентами. Правильна організація компонентів включає розділення на презентаційні та контейнерні компоненти, створення переусних UI компонентів та дотримання принципу DRY.

## 🔗 Додаткові ресурси

- [Vite офіційна документація](https://vitejs.dev/)
- [React TypeScript Cheatsheet](https://react-typescript-cheatsheet.netlify.app/)
- [Tailwind CSS документація](https://tailwindcss.com/docs)
- [React Router документація](https://reactrouter.com/)
- [Axios документація](https://axios-http.com/)
- [ESLint документація](https://eslint.org/)
- [Prettier документація](https://prettier.io/)

## ▶️ Хід роботи

### Крок 1. Створення проєкту з Vite

Відкрити термінал та виконати команду для створення нового проєкту з Vite template для React та TypeScript.

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
```

Вивчити створену структуру проєкту та ознайомитися з файлами конфігурації `vite.config.ts`, `tsconfig.json` та `package.json`.

### Крок 2. Встановлення Tailwind CSS

Встановити Tailwind CSS та його залежності.

```bash
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

Налаштувати `tailwind.config.js` для сканування файлів компонентів.

```javascript
export default {
  content: [
    "./index.html",
    "./src/**/*.{js,ts,jsx,tsx}",
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

Додати Tailwind директиви до файлу `src/index.css`.

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

### Крок 3. Налаштування ESLint та Prettier

Встановити ESLint та Prettier з необхідними плагінами.

```bash
npm install -D eslint prettier eslint-config-prettier eslint-plugin-prettier
npm install -D @typescript-eslint/eslint-plugin @typescript-eslint/parser
```

Створити файл `.eslintrc.cjs` з конфігурацією правил.

```javascript
module.exports = {
  root: true,
  env: { browser: true, es2020: true },
  extends: [
    'eslint:recommended',
    'plugin:@typescript-eslint/recommended',
    'plugin:react-hooks/recommended',
    'prettier'
  ],
  ignorePatterns: ['dist', '.eslintrc.cjs'],
  parser: '@typescript-eslint/parser',
  plugins: ['react-refresh', 'prettier'],
  rules: {
    'react-refresh/only-export-components': [
      'warn',
      { allowConstantExport: true },
    ],
    'prettier/prettier': 'error',
  },
}
```

Створити файл `.prettierrc` з правилами форматування.

```json
{
  "semi": true,
  "trailingComma": "es5",
  "singleQuote": true,
  "printWidth": 80,
  "tabWidth": 2
}
```

### Крок 4. Встановлення React Router та Axios

Встановити необхідні пакети для роутингу та HTTP запитів.

```bash
npm install react-router-dom axios
npm install -D @types/node
```

### Крок 5. Створення структури папок

Створити наступну структуру папок у директорії `src`:

```
src/
├── components/      # Переусні UI компоненти
│   ├── common/     # Базові компоненти (Button, Input)
│   └── layout/     # Компоненти розміщення (Header, Footer)
├── pages/          # Компоненти сторінок
├── services/       # API сервіси та Axios конфігурація
├── types/          # TypeScript типи та інтерфейси
├── utils/          # Utility функції
├── hooks/          # Кастомні React hooks
└── constants/      # Константи застосунку
```

### Крок 6. Налаштування Axios

Створити файл `src/services/api.ts` з базовою конфігурацією Axios.

```typescript
import axios from 'axios';

const api = axios.create({
  baseURL: import.meta.env.VITE_API_URL || 'http://localhost:3000/api',
  headers: {
    'Content-Type': 'application/json',
  },
});

// Request interceptor
api.interceptors.request.use(
  (config) => {
    const token = localStorage.getItem('token');
    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }
    return config;
  },
  (error) => {
    return Promise.reject(error);
  }
);

// Response interceptor
api.interceptors.response.use(
  (response) => response,
  (error) => {
    if (error.response?.status === 401) {
      localStorage.removeItem('token');
      window.location.href = '/login';
    }
    return Promise.reject(error);
  }
);

export default api;
```

### Крок 7. Створення базових UI компонентів

Створити переусний компонент `Button` у файлі `src/components/common/Button.tsx`.

```typescript
import React from 'react';

interface ButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement> {
  variant?: 'primary' | 'secondary' | 'danger';
  size?: 'sm' | 'md' | 'lg';
  isLoading?: boolean;
}

const Button: React.FC<ButtonProps> = ({
  children,
  variant = 'primary',
  size = 'md',
  isLoading = false,
  className = '',
  ...props
}) => {
  const baseClasses = 'font-medium rounded transition-colors duration-200';

  const variantClasses = {
    primary: 'bg-blue-600 hover:bg-blue-700 text-white',
    secondary: 'bg-gray-200 hover:bg-gray-300 text-gray-800',
    danger: 'bg-red-600 hover:bg-red-700 text-white',
  };

  const sizeClasses = {
    sm: 'px-3 py-1.5 text-sm',
    md: 'px-4 py-2',
    lg: 'px-6 py-3 text-lg',
  };

  return (
    <button
      className={`${baseClasses} ${variantClasses[variant]} ${sizeClasses[size]} ${className}`}
      disabled={isLoading}
      {...props}
    >
      {isLoading ? 'Завантаження...' : children}
    </button>
  );
};

export default Button;
```

Аналогічно створити компоненти `Input`, `Card` та інші базові UI елементи.

### Крок 8. Створення Layout компонентів

Створити компонент `Header` у файлі `src/components/layout/Header.tsx`.

```typescript
import React from 'react';
import { Link } from 'react-router-dom';

const Header: React.FC = () => {
  return (
    <header className="bg-white shadow-md">
      <nav className="container mx-auto px-4 py-4">
        <div className="flex justify-between items-center">
          <Link to="/" className="text-2xl font-bold text-blue-600">
            MyApp
          </Link>
          <div className="space-x-6">
            <Link to="/" className="hover:text-blue-600">
              Головна
            </Link>
            <Link to="/about" className="hover:text-blue-600">
              Про нас
            </Link>
          </div>
        </div>
      </nav>
    </header>
  );
};

export default Header;
```

Створити компонент `Layout` у файлі `src/components/layout/Layout.tsx`.

```typescript
import React from 'react';
import { Outlet } from 'react-router-dom';
import Header from './Header';
import Footer from './Footer';

const Layout: React.FC = () => {
  return (
    <div className="min-h-screen flex flex-col">
      <Header />
      <main className="flex-grow container mx-auto px-4 py-8">
        <Outlet />
      </main>
      <Footer />
    </div>
  );
};

export default Layout;
```

### Крок 9. Налаштування React Router

Налаштувати роутинг у файлі `src/App.tsx`.

```typescript
import React from 'react';
import { BrowserRouter, Routes, Route } from 'react-router-dom';
import Layout from './components/layout/Layout';
import HomePage from './pages/HomePage';
import AboutPage from './pages/AboutPage';
import NotFoundPage from './pages/NotFoundPage';

const App: React.FC = () => {
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
};

export default App;
```

### Крок 10. Створення TypeScript типів

Створити файл `src/types/api.types.ts` з типами для API відповідей.

```typescript
export interface User {
  id: number;
  name: string;
  email: string;
  createdAt: string;
}

export interface ApiResponse<T> {
  data: T;
  message: string;
  success: boolean;
}

export interface PaginatedResponse<T> {
  data: T[];
  total: number;
  page: number;
  limit: number;
}
```

### Крок 11. Налаштування environment variables

Створити файл `.env` у корені проєкту.

```
VITE_API_URL=http://localhost:3000/api
VITE_APP_NAME=MyApp
```

Створити файл `.env.example` з прикладами змінних.

### Крок 12. Додавання темної теми

Розширити конфігурацію Tailwind для підтримки темної теми.

```javascript
export default {
  darkMode: 'class',
  content: ["./index.html", "./src/**/*.{js,ts,jsx,tsx}"],
  theme: {
    extend: {
      colors: {
        primary: {
          50: '#eff6ff',
          100: '#dbeafe',
          // ... інші відтінки
          900: '#1e3a8a',
        },
      },
    },
  },
  plugins: [],
}
```

### Крок 13. Оформлення README.md

Створити або оновити файл `README.md` у корені проєкту з наступною структурою:

```markdown
# Назва проєкту

## Опис проєкту
Короткий опис обраної предметної області та функціональності застосунку.

## Технології
- React 18
- TypeScript
- Vite
- Tailwind CSS
- React Router
- Axios
- ESLint + Prettier

## Встановлення та запуск

### Вимоги
- Node.js 18+
- npm або yarn

### Інструкції
\`\`\`bash
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
\`\`\`

## Структура проєкту
\`\`\`
src/
├── components/      # Переусні UI компоненти
├── pages/          # Компоненти сторінок
├── services/       # API сервіси
├── types/          # TypeScript типи
├── utils/          # Utility функції
└── hooks/          # Кастомні React hooks
\`\`\`

## Скріншоти
[Додати скріншоти головної сторінки]

## Автор
[Ваше ім'я, група]
```

### Крок 14. Здача роботи

Зробити коміт усіх змін до GitHub репозиторію з описовим повідомленням. Переконатися, що:
- файл `.env` додано до `.gitignore`;
- README.md містить всю необхідну інформацію;
- у README.md додані скріншоти.

Здати роботу через Moodle, вставивши посилання на GitHub репозиторій.

[⬆️ Здати лабораторну роботу](https://moodle.vcolnuft.volyn.ua/moodle/course/view.php?id=17#section-2)

## ❓ Контрольні запитання

1. Які переваги має Vite порівняно з Create React App? Поясніть концепцію ES modules у браузері.
2. Що таке TypeScript інтерфейси та як вони використовуються для типізації пропсів компонентів?
3. Поясніть концепцію utility-first CSS та основні переваги Tailwind CSS.
4. Як працюють interceptors у Axios? Наведіть приклади їх використання.
5. Що таке React Router outlet та як він використовується для вкладених маршрутів?
6. Поясніть різницю між ESLint та Prettier. Як уникнути конфліктів між ними?
7. Що таке environment variables та чому важливо не комітити файл `.env` до репозиторію?
8. Які принципи слід дотримуватися при створенні переусних компонентів?
9. Поясніть поняття responsive дизайну та як Tailwind спрощує його реалізацію.
10. Як організувати структуру папок у великому React проєкті? Які підходи ви знаєте?
