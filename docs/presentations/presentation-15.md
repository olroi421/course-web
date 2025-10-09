# Tailwind CSS та сучасна стилізація

---

## План лекції

- Utility-first підхід
- Responsive дизайн з Tailwind
- Кастомізація та extending
- Компонентний підхід з Tailwind
- Dark/Light теми
- Headless UI компоненти

---

## Еволюція CSS підходів

**Традиційний CSS:**
```css
.card {
    max-width: 400px;
    padding: 1.5rem;
    background: white;
    border-radius: 0.5rem;
}
```

**BEM методологія:**
```css
.card__title { }
.card__content { }
.card__button--primary { }
```

**Utility-first (Tailwind):**
```html
<div class="max-w-md p-6 bg-white rounded-lg">
```

---

## Що таке Utility-First?

**Філософія:** використовувати готові utility класи замість написання власних стилів

**Переваги:**
- ⚡ Швидкість розробки
- 🎯 Передбачуваність
- 🗑️ Відсутність мертвого коду
- 🎨 Дизайн-система з коробки
- 📱 Легкий responsive дизайн

**Tailwind надає:**
- Spacing (margin, padding)
- Кольори
- Типографіку
- Flexbox/Grid
- Responsive модифікатори

---

## Встановлення Tailwind CSS

```bash
# Через Vite
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

```javascript
// tailwind.config.js
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

```css
/* src/index.css */
@tailwind base;
@tailwind components;
@tailwind utilities;
```

---

## Порівняння підходів

**Традиційний CSS:**
```css
.button {
    padding: 0.75rem 1.5rem;
    background-color: #3b82f6;
    color: white;
    border-radius: 0.375rem;
}
```

**Tailwind:**
```html
<button class="px-6 py-3 bg-blue-500 text-white rounded-md">
    Натисни мене
</button>
```

**Результат:** однаковий, але Tailwind:
- Не потребує перемикання між файлами
- Не потребує придумування назв класів
- Автоматично видаляє невикористані стилі

---

## Система Spacing

**Шкала:** 1 одиниця = 0.25rem = 4px

```html
<!-- Margin -->
<div class="m-4">Margin з усіх сторін (16px)</div>
<div class="mx-4">Margin horizontal (16px)</div>
<div class="my-4">Margin vertical (16px)</div>
<div class="mt-4">Margin top (16px)</div>

<!-- Padding -->
<div class="p-6">Padding з усіх сторін (24px)</div>
<div class="px-6 py-4">Padding horizontal 24px, vertical 16px</div>

<!-- Негативні margins -->
<div class="-mt-4">Негативний margin top</div>

<!-- Space between -->
<div class="space-y-4">
    <div>Елемент 1</div>
    <div>Елемент 2</div>
</div>
```

---

## Система кольорів

**Градації від 50 до 900:**

```html
<!-- Текст -->
<p class="text-gray-900">Темно-сірий</p>
<p class="text-blue-500">Синій</p>
<p class="text-red-600">Червоний</p>

<!-- Фон -->
<div class="bg-white">Білий фон</div>
<div class="bg-gray-100">Світло-сірий фон</div>
<div class="bg-blue-500">Синій фон</div>

<!-- Рамки -->
<div class="border border-gray-300">Сіра рамка</div>
<div class="border-2 border-blue-500">Товста синя рамка</div>

<!-- Прозорість -->
<div class="bg-blue-500 bg-opacity-50">Напівпрозорий</div>
```

---

## Типографіка

```html
<!-- Розміри -->
<h1 class="text-4xl">Великий заголовок (36px)</h1>
<h2 class="text-3xl">Середній заголовок (30px)</h2>
<p class="text-base">Звичайний текст (16px)</p>
<small class="text-sm">Малий текст (14px)</small>

<!-- Вага шрифту -->
<p class="font-light">Легкий (300)</p>
<p class="font-normal">Звичайний (400)</p>
<p class="font-bold">Жирний (700)</p>

<!-- Міжрядковий інтервал -->
<p class="leading-tight">Щільний</p>
<p class="leading-relaxed">Розслаблений</p>

<!-- Вирівнювання -->
<p class="text-center">По центру</p>
<p class="text-right">Праворуч</p>
```

---

## Flexbox Layout

```html
<!-- Базовий flex -->
<div class="flex items-center justify-between">
    <div>Лівий</div>
    <div>Правий</div>
</div>

<!-- Направлення -->
<div class="flex flex-col">Вертикальний</div>
<div class="flex flex-row">Горизонтальний</div>

<!-- Justify content -->
<div class="flex justify-start">На початку</div>
<div class="flex justify-center">По центру</div>
<div class="flex justify-between">З проміжками</div>

<!-- Align items -->
<div class="flex items-start">Вгорі</div>
<div class="flex items-center">По центру</div>
<div class="flex items-end">Внизу</div>

<!-- Gap -->
<div class="flex gap-4">Проміжок 16px</div>
```

---

## Grid Layout

```html
<!-- Базова сітка -->
<div class="grid grid-cols-3 gap-4">
    <div>Колонка 1</div>
    <div>Колонка 2</div>
    <div>Колонка 3</div>
</div>

<!-- Span -->
<div class="grid grid-cols-4 gap-4">
    <div class="col-span-2">Займає 2 колонки</div>
    <div>Колонка 3</div>
    <div>Колонка 4</div>
</div>

<!-- Auto columns -->
<div class="grid grid-cols-[200px_auto_200px] gap-4">
    <div>Фіксована 200px</div>
    <div>Автоматична</div>
    <div>Фіксована 200px</div>
</div>
```

---

## Responsive Design

**Breakpoints:**
- `sm`: 640px
- `md`: 768px
- `lg`: 1024px
- `xl`: 1280px
- `2xl`: 1536px

**Mobile-first підхід:**
```html
<div class="
    w-full          /* Мобільні: 100% */
    sm:w-auto       /* ≥640px: auto */
    md:w-1/2        /* ≥768px: 50% */
    lg:w-1/3        /* ≥1024px: 33% */
    
    p-4             /* Всюди: 16px */
    md:p-6          /* ≥768px: 24px */
    lg:p-8          /* ≥1024px: 32px */
">
    Адаптивний контент
</div>
```

---

## Responsive Navigation

```jsx
function Navigation() {
    const [isOpen, setIsOpen] = useState(false);

    return (
        <nav class="bg-white shadow-lg">
            <div class="max-w-7xl mx-auto px-4">
                {/* Desktop menu - прихований на мобільних */}
                <div class="hidden md:flex md:space-x-4">
                    <a href="/">Головна</a>
                    <a href="/about">Про нас</a>
                </div>

                {/* Mobile button - прихований на десктопі */}
                <button 
                    class="md:hidden"
                    onClick={() => setIsOpen(!isOpen)}
                >
                    ☰
                </button>
            </div>

            {/* Mobile menu */}
            {isOpen && (
                <div class="md:hidden">
                    <a href="/">Головна</a>
                    <a href="/about">Про нас</a>
                </div>
            )}
        </nav>
    );
}
```

---

## Responsive Grid

```html
<div class="
    grid 
    grid-cols-1      /* Мобільні: 1 колонка */
    sm:grid-cols-2   /* Планшети: 2 колонки */
    md:grid-cols-3   /* Середні: 3 колонки */
    lg:grid-cols-4   /* Десктопи: 4 колонки */
    gap-4
">
    <div class="bg-white p-6 rounded-lg shadow">Картка 1</div>
    <div class="bg-white p-6 rounded-lg shadow">Картка 2</div>
    <div class="bg-white p-6 rounded-lg shadow">Картка 3</div>
    <div class="bg-white p-6 rounded-lg shadow">Картка 4</div>
</div>
```

---

## Кастомізація: tailwind.config.js

```javascript
export default {
    theme: {
        extend: {
            colors: {
                primary: {
                    50: '#f0f9ff',
                    500: '#0ea5e9',
                    900: '#0c4a6e',
                },
            },
            spacing: {
                '128': '32rem',
            },
            fontFamily: {
                sans: ['Inter', 'sans-serif'],
            },
            boxShadow: {
                'card': '0 4px 6px rgba(0, 0, 0, 0.1)',
            },
            animation: {
                'fade-in': 'fadeIn 0.5s ease-in',
            },
            keyframes: {
                fadeIn: {
                    '0%': { opacity: '0' },
                    '100%': { opacity: '1' },
                }
            }
        }
    }
}
```

---

## Використання кастомних значень

```html
<!-- Кастомні кольори -->
<div class="bg-primary-50 text-primary-900">
    Кастомні кольори
</div>

<!-- Кастомний spacing -->
<div class="mt-128">
    Великий відступ
</div>

<!-- Кастомний font -->
<h1 class="font-sans">
    Inter шрифт
</h1>

<!-- Кастомна анімація -->
<div class="animate-fade-in">
    Анімація появи
</div>

<!-- Кастомна тінь -->
<div class="shadow-card">
    Кастомна тінь
</div>
```

---

## Власні Utility класи

```css
/* src/index.css */
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer utilities {
    .text-shadow {
        text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.1);
    }
    
    .gradient-text {
        background: linear-gradient(to right, #3b82f6, #8b5cf6);
        -webkit-background-clip: text;
        -webkit-text-fill-color: transparent;
    }
    
    .scrollbar-hide {
        -ms-overflow-style: none;
        scrollbar-width: none;
    }
}
```

```html
<h1 class="text-4xl gradient-text">
    Градієнтний текст
</h1>
```

---

## @apply directive

```css
@layer components {
    .btn-primary {
        @apply px-6 py-3 bg-blue-500 text-white rounded-md;
        @apply hover:bg-blue-600 active:bg-blue-700;
        @apply transition-colors duration-200;
        @apply focus:outline-none focus:ring-2;
    }
    
    .card {
        @apply bg-white rounded-lg shadow-md p-6;
        @apply hover:shadow-xl transition-shadow;
    }
    
    .input-field {
        @apply w-full px-4 py-2 border rounded-md;
        @apply focus:ring-2 focus:ring-blue-500;
    }
}
```

```html
<button class="btn-primary">Первинна кнопка</button>
<div class="card">Контент картки</div>
```

---

## Компонентний підхід

```jsx
function Button({ 
    children, 
    variant = 'primary',
    size = 'md',
    ...props 
}) {
    const baseStyles = 'font-semibold rounded-md transition-colors';
    
    const variants = {
        primary: 'bg-blue-500 text-white hover:bg-blue-600',
        secondary: 'bg-gray-200 text-gray-900 hover:bg-gray-300',
        danger: 'bg-red-500 text-white hover:bg-red-600'
    };
    
    const sizes = {
        sm: 'px-3 py-1.5 text-sm',
        md: 'px-6 py-3 text-base',
        lg: 'px-8 py-4 text-lg'
    };
    
    return (
        <button 
            className={`${baseStyles} ${variants[variant]} ${sizes[size]}`}
            {...props}
        >
            {children}
        </button>
    );
}
```

---

## Card компонент

```jsx
function Card({ 
    title, 
    description, 
    image,
    hoverable = true 
}) {
    return (
        <div className={`
            bg-white rounded-lg shadow-md overflow-hidden
            ${hoverable ? 'hover:shadow-xl transition-shadow' : ''}
        `}>
            {image && (
                <img 
                    src={image} 
                    alt={title}
                    className="w-full h-48 object-cover"
                />
            )}
            
            <div className="p-6">
                <h3 className="text-xl font-bold mb-2">
                    {title}
                </h3>
                <p className="text-gray-600">
                    {description}
                </p>
            </div>
        </div>
    );
}
```

---

## Input компонент

```jsx
function Input({
    label,
    error,
    icon,
    ...props
}) {
    return (
        <div className="w-full">
            {label && (
                <label className="block text-sm font-medium mb-2">
                    {label}
                </label>
            )}
            
            <div className="relative">
                {icon && (
                    <div className="absolute left-3 top-1/2 -translate-y-1/2">
                        {icon}
                    </div>
                )}
                
                <input
                    className={`
                        w-full px-4 py-2 rounded-md
                        ${icon ? 'pl-10' : ''}
                        ${error ? 'border-red-500' : 'border-gray-300'}
                        focus:ring-2 focus:border-transparent
                    `}
                    {...props}
                />
            </div>
            
            {error && <p className="mt-1 text-sm text-red-600">{error}</p>}
        </div>
    );
}
```

---

## Dark Mode: налаштування

```javascript
// tailwind.config.js
export default {
    darkMode: 'class', // або 'media'
    // ...
}
```

```jsx
// ThemeProvider
function ThemeProvider({ children }) {
    const [theme, setTheme] = useState('light');

    useEffect(() => {
        document.documentElement.classList.toggle('dark', theme === 'dark');
    }, [theme]);

    return (
        <ThemeContext.Provider value={{ theme, setTheme }}>
            {children}
        </ThemeContext.Provider>
    );
}
```

---

## Dark Mode: стилізація

```html
<div class="
    bg-white dark:bg-gray-800
    text-gray-900 dark:text-gray-100
    border-gray-200 dark:border-gray-700
">
    <h2 class="text-gray-900 dark:text-white">
        Заголовок
    </h2>
    
    <p class="text-gray-600 dark:text-gray-300">
        Опис з підтримкою теми
    </p>
    
    <button class="
        bg-blue-500 dark:bg-blue-600
        hover:bg-blue-600 dark:hover:bg-blue-700
    ">
        Кнопка
    </button>
</div>
```

---

## Theme Toggle компонент

```jsx
function ThemeToggle() {
    const { theme, setTheme } = useTheme();

    return (
        <button
            onClick={() => setTheme(theme === 'light' ? 'dark' : 'light')}
            className="
                p-2 rounded-lg
                bg-gray-200 dark:bg-gray-700
                text-gray-800 dark:text-gray-200
                hover:bg-gray-300 dark:hover:bg-gray-600
                transition-colors
            "
            aria-label={`Switch to ${theme === 'light' ? 'dark' : 'light'} mode`}
        >
            {theme === 'light' ? '🌙' : '☀️'}
        </button>
    );
}
```

---

## Headless UI

**Що це?**
- Несстилізовані, доступні компоненти
- Повна інтеграція з Tailwind
- Підтримка клавіатури та screen readers
- Анімації через Transition

```bash
npm install @headlessui/react
```

**Компоненти:**
- Dialog (Modal)
- Menu (Dropdown)
- Listbox (Select)
- Combobox (Autocomplete)
- Tabs
- Disclosure

---

## Dialog (Modal) компонент

```jsx
import { Dialog, Transition } from '@headlessui/react';

function Modal({ isOpen, onClose }) {
    return (
        <Transition show={isOpen}>
            <Dialog onClose={onClose} className="relative z-50">
                <Transition.Child
                    enter="ease-out duration-300"
                    enterFrom="opacity-0"
                    enterTo="opacity-100"
                >
                    <div className="fixed inset-0 bg-black/25" />
                </Transition.Child>

                <div className="fixed inset-0 flex items-center justify-center">
                    <Dialog.Panel className="bg-white rounded-lg p-6">
                        <Dialog.Title className="text-lg font-bold">
                            Заголовок
                        </Dialog.Title>
                        <p>Контент модального вікна</p>
                        <button onClick={onClose}>Закрити</button>
                    </Dialog.Panel>
                </div>
            </Dialog>
        </Transition>
    );
}
```

---

## Menu (Dropdown)

```jsx
import { Menu } from '@headlessui/react';

function Dropdown() {
    return (
        <Menu as="div" className="relative">
            <Menu.Button className="px-4 py-2 bg-blue-500 text-white rounded">
                Опції
            </Menu.Button>

            <Menu.Items className="absolute mt-2 bg-white shadow-lg rounded">
                <Menu.Item>
                    {({ active }) => (
                        <button className={`
                            w-full px-4 py-2 text-left
                            ${active ? 'bg-blue-500 text-white' : 'text-gray-900'}
                        `}>
                            Редагувати
                        </button>
                    )}
                </Menu.Item>
                <Menu.Item>
                    {({ active }) => (
                        <button className={`
                            w-full px-4 py-2 text-left
                            ${active ? 'bg-red-500 text-white' : 'text-gray-900'}
                        `}>
                            Видалити
                        </button>
                    )}
                </Menu.Item>
            </Menu.Items>
        </Menu>
    );
}
```

---

## Переваги Tailwind CSS

**Продуктивність:**
- ⚡ Швидша розробка
- 🔄 Легкий рефакторинг
- 🎯 Немає конфліктів імен

**Якість коду:**
- 📦 Малий розмір production bundle
- 🗑️ Автоматичне видалення невикористаного CSS
- 🎨 Консистентний дизайн

**Команда:**
- 👥 Легше онбординг нових розробників
- 📖 Самодокументований код
- 🔧 Менше code review для стилів

---

## Порівняння розміру bundle

**Традиційний CSS проєкт:**
```
app.css: 245 KB
```

**Tailwind після PurgeCSS:**
```
app.css: 8 KB
```

**Чому так мало?**
- Видаляються всі невикористані класи
- Залишається тільки те, що реально використовується
- Оптимізація для production автоматична

---

## Найкращі практики

**Використання:**
- Прийміть utility-first філософію повністю
- Не бійтеся довгих списків класів
- Створюйте React компоненти для повторюваних патернів
- Використовуйте @apply тільки для справді багаторазового коду

**Організація:**
- Кастомізуйте через tailwind.config.js
- Групуйте схожі utility класи разом
- Використовуйте prettier-plugin-tailwindcss для сортування

**Продуктивність:**
- Використовуйте PurgeCSS в production
- Перевіряйте content paths в конфігурації
- Оптимізуйте кастомні класи

---

## Інтеграція з іншими інструментами

**IDE підтримка:**
```bash
# VS Code extension
Tailwind CSS IntelliSense
```

**Prettier:**
```bash
npm install -D prettier-plugin-tailwindcss
```

**ESLint:**
```bash
npm install -D eslint-plugin-tailwindcss
```

**TypeScript:**
```typescript
type ButtonProps = {
    variant?: 'primary' | 'secondary';
    size?: 'sm' | 'md' | 'lg';
}
```

---

## Альтернативи Tailwind

**Styled Components:**
- CSS-in-JS підхід
- Динамічні стилі через props
- Більший bundle size

**CSS Modules:**
- Локальні стилі
- Традиційний CSS синтаксис
- Потребує більше boilerplate

**Emotion:**
- CSS-in-JS
- Гарна TypeScript підтримка
- Складніша конфігурація

**Коли НЕ використовувати Tailwind:**
- Дуже специфічні дизайни
- Команда не підтримує utility-first

---

## Корисні ресурси

**Документація:**
- [Tailwind CSS](https://tailwindcss.com/docs)
- [Headless UI](https://headlessui.com/)
- [Tailwind UI](https://tailwindui.com/) - готові компоненти (платно)

**Інструменти:**
- [Tailwind Play](https://play.tailwindcss.com/) - онлайн playground
- [Tailwind Toolbox](https://www.tailwindtoolbox.com/) - безкоштовні шаблони
- [Hypercolor](https://hypercolor.dev/) - градієнти

**Спільнота:**
- [Awesome Tailwind CSS](https://github.com/aniftyco/awesome-tailwindcss)
- [Tailwind Components](https://tailwindcomponents.com/)

---

## Питання для самоперевірки

1. У чому суть utility-first підходу?
2. Як працює responsive дизайн в Tailwind?
3. Коли використовувати @apply directive?
4. Як налаштувати dark mode?
5. Що таке Headless UI і навіщо він потрібен?
6. Як Tailwind оптимізує розмір bundle?

---

## Практичне завдання

**Створіть dashboard з Tailwind:**
- Responsive navigation з mobile menu
- Сітка карток з hover ефектами
- Форма з кастомними input компонентами
- Dark/Light theme toggle
- Modal dialog з Headless UI
- Dropdown menu

**Вимоги:**
- Повна адаптивність (mobile → desktop)
- Підтримка темної теми
- Анімації переходів
- Доступність (a11y)

---

## Дякую за увагу! 🎉

**Контакти для запитань:**
📧 Email: [ваш email]
💼 LinkedIn: [ваш профіль]
🐙 GitHub: [ваш репозиторій]

**Наступна тема:**
Тестування React компонентів
