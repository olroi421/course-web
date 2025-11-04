# Лекція 15. Tailwind CSS та сучасна стилізація

## Вступ до utility-first підходу

Tailwind CSS представляє радикально інший підхід до стилізації вебзастосунків порівняно з традиційними методами. Замість написання власних CSS класів та стилів, розробники використовують набір готових utility класів безпосередньо в HTML або JSX розмітці.

Ця філософія може здатися незвичною на перший погляд, особливо для розробників, які звикли до традиційних CSS методологій як BEM або SMACSS. Проте utility-first підхід вирішує багато фундаментальних проблем традиційного CSS, роблячи процес розробки швидшим, більш передбачуваним та легшим у підтримці.

Традиційний CSS часто призводить до накопичення незатребуваних стилів, складності з найменуванням класів та проблем з масштабуванням. Tailwind CSS усуває ці проблеми через обмежений набір заздалегідь визначених класів, які покривають більшість потреб сучасної веброзробки.

## Utility-first підхід: філософія та переваги

### Порівняння з традиційним CSS

```css
/* Традиційний підхід: написання власних класів */
.card {
    max-width: 400px;
    margin: 0 auto;
    padding: 1.5rem;
    background-color: white;
    border-radius: 0.5rem;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.card-title {
    font-size: 1.5rem;
    font-weight: 700;
    margin-bottom: 1rem;
    color: #1a202c;
}

.card-content {
    font-size: 1rem;
    line-height: 1.5;
    color: #4a5568;
}

.button-primary {
    padding: 0.75rem 1.5rem;
    background-color: #3b82f6;
    color: white;
    border-radius: 0.375rem;
    font-weight: 600;
    transition: background-color 0.2s;
}

.button-primary:hover {
    background-color: #2563eb;
}
```

```jsx
// Традиційне використання
<div className="card">
    <h2 className="card-title">Заголовок картки</h2>
    <p className="card-content">Це текст всередині картки.</p>
    <button className="button-primary">Натисни мене</button>
</div>
```

```jsx
// Tailwind підхід: utility класи
<div className="max-w-md mx-auto p-6 bg-white rounded-lg shadow-md">
    <h2 className="text-2xl font-bold mb-4 text-gray-900">
        Заголовок картки
    </h2>
    <p className="text-base leading-relaxed text-gray-600">
        Це текст всередині картки.
    </p>
    <button className="px-6 py-3 bg-blue-500 text-white rounded-md font-semibold hover:bg-blue-600 transition-colors">
        Натисни мене
    </button>
</div>
```

### Переваги utility-first підходу

**Швидкість розробки:** розробники не витрачають час на придумування назв класів та переключення між HTML і CSS файлами. Всі стилі застосовуються безпосередньо в розмітці.

**Передбачуваність:** кожен utility клас робить рівно одну річ, що робить поведінку стилів абсолютно передбачуваною. Немає каскадних ефектів або несподіваних перевизначень стилів.

**Відсутність мертвого коду:** при видаленні компонента автоматично видаляються всі його стилі. Немає накопичення незатребуваного CSS коду в проєкті.

**Дизайн-система з коробки:** Tailwind надає узгоджену систему значень для кольорів, відступів, шрифтів та інших властивостей, що забезпечує візуальну консистентність всього додатку.

**Мобільна розробка:** responsive модифікатори дозволяють легко створювати адаптивні дизайни без написання медіа-запитів.

```jsx
// Responsive дизайн з Tailwind
<div className="
    w-full           /* На мобільних - 100% ширина */
    md:w-1/2         /* На планшетах - 50% ширина */
    lg:w-1/3         /* На десктопах - 33% ширина */
    p-4              /* Padding 16px на всіх пристроях */
    md:p-6           /* Padding 24px на планшетах і більше */
    lg:p-8           /* Padding 32px на десктопах */
">
    Адаптивний контент
</div>
```

## Базові концепції та система дизайну

### Система spacing

Tailwind використовує передбачувану шкалу відступів, де кожна одиниця відповідає 0.25rem або 4px за замовчуванням.

```jsx
// Margin та Padding
<div className="m-4">Margin з усіх сторін (16px)</div>
<div className="mx-4">Margin horizontal (16px)</div>
<div className="my-4">Margin vertical (16px)</div>
<div className="mt-4">Margin top (16px)</div>
<div className="mr-4">Margin right (16px)</div>
<div className="mb-4">Margin bottom (16px)</div>
<div className="ml-4">Margin left (16px)</div>

// Padding працює аналогічно
<div className="p-6">Padding з усіх сторін (24px)</div>
<div className="px-6 py-4">Padding horizontal 24px, vertical 16px</div>

// Негативні margins
<div className="-mt-4">Негативний margin top</div>

// Space between елементами
<div className="space-y-4">
    <div>Елемент 1</div>
    <div>Елемент 2</div>
    <div>Елемент 3</div>
</div>
```

### Система кольорів

Tailwind надає багату палітру кольорів з градаціями від 50 до 900.

```jsx
// Кольори тексту
<p className="text-gray-900">Темно-сірий текст</p>
<p className="text-blue-500">Синій текст</p>
<p className="text-red-600">Червоний текст</p>

// Кольори фону
<div className="bg-white">Білий фон</div>
<div className="bg-gray-100">Світло-сірий фон</div>
<div className="bg-blue-500">Синій фон</div>

// Кольори рамки
<div className="border border-gray-300">Сіра рамка</div>
<div className="border-2 border-blue-500">Товста синя рамка</div>

// Opacity
<div className="bg-blue-500 bg-opacity-50">Напівпрозорий синій фон</div>
<p className="text-gray-900 text-opacity-75">Напівпрозорий текст</p>
```

### Типографіка

```jsx
// Розміри шрифту
<h1 className="text-4xl">Великий заголовок (36px)</h1>
<h2 className="text-3xl">Середній заголовок (30px)</h2>
<p className="text-base">Звичайний текст (16px)</p>
<small className="text-sm">Малий текст (14px)</small>

// Вага шрифту
<p className="font-light">Легкий шрифт (300)</p>
<p className="font-normal">Звичайний шрифт (400)</p>
<p className="font-semibold">Напівжирний шрифт (600)</p>
<p className="font-bold">Жирний шрифт (700)</p>

// Міжрядковий інтервал
<p className="leading-tight">Щільний інтервал</p>
<p className="leading-normal">Звичайний інтервал</p>
<p className="leading-relaxed">Розслаблений інтервал</p>

// Вирівнювання тексту
<p className="text-left">Вирівнювання ліворуч</p>
<p className="text-center">По центру</p>
<p className="text-right">Праворуч</p>
<p className="text-justify">По ширині</p>

// Декорація тексту
<p className="underline">Підкреслений текст</p>
<p className="line-through">Закреслений текст</p>
<p className="uppercase">Великі літери</p>
<p className="lowercase">малі літери</p>
<p className="capitalize">Капіталізація</p>
```

### Flexbox та Grid

```jsx
// Flexbox layout
<div className="flex items-center justify-between">
    <div>Лівий елемент</div>
    <div>Правий елемент</div>
</div>

// Flex направлення
<div className="flex flex-col">       {/* Вертикальний */}
<div className="flex flex-row">       {/* Горизонтальний */}
<div className="flex flex-col-reverse"> {/* Зворотний вертикальний */}

// Justify content
<div className="flex justify-start">    {/* На початку */}
<div className="flex justify-center">   {/* По центру */}
<div className="flex justify-end">      {/* В кінці */}
<div className="flex justify-between">  {/* З проміжками */}
<div className="flex justify-around">   {/* Рівномірно */}

// Align items
<div className="flex items-start">     {/* Вгорі */}
<div className="flex items-center">    {/* По центру */}
<div className="flex items-end">       {/* Внизу */}
<div className="flex items-stretch">   {/* Розтягнуто */}

// Flex grow/shrink
<div className="flex-1">Розтягується</div>
<div className="flex-none">Фіксований розмір</div>

// Gap between items
<div className="flex gap-4">           {/* Проміжок 16px */}
<div className="flex gap-x-4 gap-y-2"> {/* Різні проміжки */}

// Grid layout
<div className="grid grid-cols-3 gap-4">
    <div>Колонка 1</div>
    <div>Колонка 2</div>
    <div>Колонка 3</div>
</div>

// Responsive grid
<div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
    {/* На мобільних - 1 колонка, планшетах - 2, десктопах - 3 */}
</div>

// Grid span
<div className="grid grid-cols-4">
    <div className="col-span-2">Займає 2 колонки</div>
    <div>Колонка 3</div>
    <div>Колонка 4</div>
</div>
```

## Responsive дизайн з Tailwind

### Breakpoints система

Tailwind використовує mobile-first підхід до responsive дизайну. Стилі без префіксу застосовуються на всіх пристроях, а з префіксом – починаючи з вказаного breakpoint.

```jsx
// Breakpoints:
// sm: 640px
// md: 768px
// lg: 1024px
// xl: 1280px
// 2xl: 1536px

// Приклад responsive компонента
<div className="
    w-full          /* Мобільні: 100% ширина */
    sm:w-auto       /* ≥640px: автоматична ширина */
    md:w-1/2        /* ≥768px: 50% ширина */
    lg:w-1/3        /* ≥1024px: 33% ширина */
    xl:w-1/4        /* ≥1280px: 25% ширина */
    
    p-4             /* Всюди: padding 16px */
    md:p-6          /* ≥768px: padding 24px */
    lg:p-8          /* ≥1024px: padding 32px */
    
    text-sm         /* Мобільні: малий текст */
    md:text-base    /* ≥768px: звичайний текст */
    lg:text-lg      /* ≥1024px: великий текст */
">
    Responsive контент
</div>
```

### Практичний приклад: адаптивна навігація

```jsx
function ResponsiveNavigation() {
    const [isMenuOpen, setIsMenuOpen] = useState(false);

    return (
        <nav className="bg-white shadow-lg">
            <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
                <div className="flex justify-between h-16">
                    {/* Logo */}
                    <div className="flex items-center">
                        <img
                            src="/logo.svg"
                            alt="Logo"
                            className="h-8 w-auto"
                        />
                    </div>

                    {/* Desktop menu */}
                    <div className="hidden md:flex md:items-center md:space-x-4">
                        <a href="/" className="px-3 py-2 text-gray-700 hover:text-blue-600">
                            Головна
                        </a>
                        <a href="/about" className="px-3 py-2 text-gray-700 hover:text-blue-600">
                            Про нас
                        </a>
                        <a href="/contact" className="px-3 py-2 text-gray-700 hover:text-blue-600">
                            Контакти
                        </a>
                        <button className="px-4 py-2 bg-blue-500 text-white rounded-md hover:bg-blue-600">
                            Увійти
                        </button>
                    </div>

                    {/* Mobile menu button */}
                    <div className="flex items-center md:hidden">
                        <button
                            onClick={() => setIsMenuOpen(!isMenuOpen)}
                            className="p-2 rounded-md text-gray-700 hover:bg-gray-100"
                        >
                            <svg
                                className="h-6 w-6"
                                fill="none"
                                viewBox="0 0 24 24"
                                stroke="currentColor"
                            >
                                {isMenuOpen ? (
                                    <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M6 18L18 6M6 6l12 12" />
                                ) : (
                                    <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M4 6h16M4 12h16M4 18h16" />
                                )}
                            </svg>
                        </button>
                    </div>
                </div>
            </div>

            {/* Mobile menu */}
            {isMenuOpen && (
                <div className="md:hidden">
                    <div className="px-2 pt-2 pb-3 space-y-1">
                        <a href="/" className="block px-3 py-2 text-gray-700 hover:bg-gray-100 rounded-md">
                            Головна
                        </a>
                        <a href="/about" className="block px-3 py-2 text-gray-700 hover:bg-gray-100 rounded-md">
                            Про нас
                        </a>
                        <a href="/contact" className="block px-3 py-2 text-gray-700 hover:bg-gray-100 rounded-md">
                            Контакти
                        </a>
                        <button className="w-full text-left px-3 py-2 bg-blue-500 text-white rounded-md hover:bg-blue-600">
                            Увійти
                        </button>
                    </div>
                </div>
            )}
        </nav>
    );
}
```

### Адаптивна сітка

```jsx
function ResponsiveGrid() {
    const items = Array.from({ length: 12 }, (_, i) => i + 1);

    return (
        <div className="container mx-auto px-4 py-8">
            <div className="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-4">
                {items.map(item => (
                    <div
                        key={item}
                        className="
                            bg-white rounded-lg shadow-md p-6
                            hover:shadow-xl transition-shadow
                            transform hover:-translate-y-1
                        "
                    >
                        <h3 className="text-xl font-bold mb-2">
                            Картка {item}
                        </h3>
                        <p className="text-gray-600">
                            Опис картки з адаптивним дизайном
                        </p>
                    </div>
                ))}
            </div>
        </div>
    );
}
```

## Кастомізація та extending

### Налаштування конфігурації

```javascript
// tailwind.config.js
module.exports = {
    content: [
        './src/**/*.{js,jsx,ts,tsx}',
    ],
    theme: {
        extend: {
            // Додавання кастомних кольорів
            colors: {
                primary: {
                    50: '#f0f9ff',
                    100: '#e0f2fe',
                    500: '#0ea5e9',
                    900: '#0c4a6e',
                },
                secondary: {
                    500: '#8b5cf6',
                },
            },
            // Кастомні spacing значення
            spacing: {
                '128': '32rem',
                '144': '36rem',
            },
            // Кастомні шрифти
            fontFamily: {
                sans: ['Inter', 'sans-serif'],
                heading: ['Poppins', 'sans-serif'],
            },
            // Кастомні breakpoints
            screens: {
                'xs': '475px',
                '3xl': '1920px',
            },
            // Кастомні box shadows
            boxShadow: {
                'card': '0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -1px rgba(0, 0, 0, 0.06)',
                'card-hover': '0 20px 25px -5px rgba(0, 0, 0, 0.1), 0 10px 10px -5px rgba(0, 0, 0, 0.04)',
            },
            // Кастомні border radius
            borderRadius: {
                '4xl': '2rem',
            },
            // Animations
            animation: {
                'fade-in': 'fadeIn 0.5s ease-in-out',
                'slide-up': 'slideUp 0.3s ease-out',
            },
            keyframes: {
                fadeIn: {
                    '0%': { opacity: '0' },
                    '100%': { opacity: '1' },
                },
                slideUp: {
                    '0%': { transform: 'translateY(20px)', opacity: '0' },
                    '100%': { transform: 'translateY(0)', opacity: '1' },
                },
            },
        },
    },
    plugins: [],
};
```

### Використання кастомних значень

```jsx
// Використання кастомізованих значень
function CustomStyledComponent() {
    return (
        <div className="max-w-7xl mx-auto px-4 py-128">
            <div className="
                bg-primary-50 
                text-primary-900
                font-heading
                shadow-card hover:shadow-card-hover
                rounded-4xl
                p-6
                animate-fade-in
            ">
                <h1 className="text-3xl xs:text-4xl lg:text-5xl 3xl:text-6xl">
                    Кастомізований компонент
                </h1>
                <p className="text-secondary-500 mt-4">
                    Використання власних значень з конфігурації
                </p>
            </div>
        </div>
    );
}
```

### Створення власних utility класів

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
    
    .scrollbar-hide::-webkit-scrollbar {
        display: none;
    }
}

@layer components {
    .btn-primary {
        @apply px-6 py-3 bg-blue-500 text-white rounded-md font-semibold;
        @apply hover:bg-blue-600 active:bg-blue-700;
        @apply transition-colors duration-200;
        @apply focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2;
    }
    
    .card {
        @apply bg-white rounded-lg shadow-md p-6;
        @apply hover:shadow-xl transition-shadow duration-300;
    }
    
    .input-field {
        @apply w-full px-4 py-2 border border-gray-300 rounded-md;
        @apply focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent;
        @apply placeholder-gray-400;
    }
}
```

## Компонентний підхід з Tailwind

### Створення багаторазових компонентів

```jsx
// Button компонент з варіантами
function Button({ 
    children, 
    variant = 'primary', 
    size = 'md',
    fullWidth = false,
    disabled = false,
    onClick 
}) {
    const baseStyles = 'font-semibold rounded-md transition-colors duration-200 focus:outline-none focus:ring-2 focus:ring-offset-2';
    
    const variants = {
        primary: 'bg-blue-500 text-white hover:bg-blue-600 focus:ring-blue-500',
        secondary: 'bg-gray-200 text-gray-900 hover:bg-gray-300 focus:ring-gray-500',
        danger: 'bg-red-500 text-white hover:bg-red-600 focus:ring-red-500',
        success: 'bg-green-500 text-white hover:bg-green-600 focus:ring-green-500',
        outline: 'border-2 border-blue-500 text-blue-500 hover:bg-blue-50 focus:ring-blue-500',
    };
    
    const sizes = {
        sm: 'px-3 py-1.5 text-sm',
        md: 'px-6 py-3 text-base',
        lg: 'px-8 py-4 text-lg',
    };
    
    const className = `
        ${baseStyles}
        ${variants[variant]}
        ${sizes[size]}
        ${fullWidth ? 'w-full' : ''}
        ${disabled ? 'opacity-50 cursor-not-allowed' : 'cursor-pointer'}
    `.trim();
    
    return (
        <button
            className={className}
            disabled={disabled}
            onClick={onClick}
        >
            {children}
        </button>
    );
}

// Використання
function Example() {
    return (
        <div className="space-y-4">
            <Button variant="primary" size="md">
                Первинна кнопка
            </Button>
            <Button variant="secondary" size="lg">
                Вторинна кнопка
            </Button>
            <Button variant="danger" fullWidth>
                Кнопка на всю ширину
            </Button>
            <Button variant="outline" disabled>
                Вимкнена кнопка
            </Button>
        </div>
    );
}
```

### Card компонент

```jsx
function Card({ 
    title, 
    description, 
    image,
    footer,
    hoverable = true,
    className = '' 
}) {
    return (
        <div className={`
            bg-white rounded-lg shadow-md overflow-hidden
            ${hoverable ? 'hover:shadow-xl transition-shadow duration-300' : ''}
            ${className}
        `}>
            {image && (
                <div className="h-48 overflow-hidden">
                    <img
                        src={image}
                        alt={title}
                        className="w-full h-full object-cover transform hover:scale-110 transition-transform duration-300"
                    />
                </div>
            )}
            
            <div className="p-6">
                {title && (
                    <h3 className="text-xl font-bold mb-2 text-gray-900">
                        {title}
                    </h3>
                )}
                
                {description && (
                    <p className="text-gray-600 leading-relaxed">
                        {description}
                    </p>
                )}
            </div>
            
            {footer && (
                <div className="px-6 py-4 bg-gray-50 border-t border-gray-200">
                    {footer}
                </div>
            )}
        </div>
    );
}

// Використання
function CardExample() {
    return (
        <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
            <Card
                title="Заголовок картки"
                description="Опис картки з важливою інформацією для користувача"
                image="/card-image.jpg"
                footer={
                    <Button variant="primary" fullWidth>
                        Дізнатися більше
                    </Button>
                }
            />
        </div>
    );
}
```

### Input компонент з валідацією

```jsx
function Input({
    label,
    type = 'text',
    placeholder,
    value,
    onChange,
    error,
    helperText,
    required = false,
    disabled = false,
    icon,
    ...props
}) {
    return (
        <div className="w-full">
            {label && (
                <label className="block text-sm font-medium text-gray-700 mb-2">
                    {label}
                    {required && <span className="text-red-500 ml-1">*</span>}
                </label>
            )}
            
            <div className="relative">
                {icon && (
                    <div className="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none">
                        {icon}
                    </div>
                )}
                
                <input
                    type={type}
                    value={value}
                    onChange={onChange}
                    placeholder={placeholder}
                    disabled={disabled}
                    className={`
                        w-full px-4 py-2 rounded-md
                        ${icon ? 'pl-10' : ''}
                        ${error
                            ? 'border-2 border-red-500 focus:ring-red-500'
                            : 'border border-gray-300 focus:ring-blue-500'
                        }
                        focus:outline-none focus:ring-2 focus:border-transparent
                        disabled:bg-gray-100 disabled:cursor-not-allowed
                        placeholder-gray-400
                        transition-colors duration-200
                    `}
                    {...props}
                />
            </div>
            
            {error && (
                <p className="mt-1 text-sm text-red-600 flex items-center">
                    <svg className="w-4 h-4 mr-1" fill="currentColor" viewBox="0 0 20 20">
                        <path fillRule="evenodd" d="M18 10a8 8 0 11-16 0 8 8 0 0116 0zm-7 4a1 1 0 11-2 0 1 1 0 012 0zm-1-9a1 1 0 00-1 1v4a1 1 0 102 0V6a1 1 0 00-1-1z" clipRule="evenodd" />
                    </svg>
                    {error}
                </p>
            )}
            
            {helperText && !error && (
                <p className="mt-1 text-sm text-gray-500">
                    {helperText}
                </p>
            )}
        </div>
    );
}
```

## Dark/Light теми

### Налаштування темної теми

```javascript
// tailwind.config.js
module.exports = {
    darkMode: 'class', // або 'media' для автоматичної теми
    // ... інша конфігурація
};
```

### Реалізація перемикання теми

```jsx
import { createContext, useContext, useState, useEffect } from 'react';

const ThemeContext = createContext();

export function ThemeProvider({ children }) {
    const [theme, setTheme] = useState(() => {
        return localStorage.getItem('theme') || 'light';
    });

    useEffect(() => {
        const root = window.document.documentElement;
        root.classList.remove('light', 'dark');
        root.classList.add(theme);
        localStorage.setItem('theme', theme);
    }, [theme]);

    const toggleTheme = () => {
        setTheme(prev => prev === 'light' ? 'dark' : 'light');
    };

    return (
        <ThemeContext.Provider value={{ theme, toggleTheme }}>
            {children}
        </ThemeContext.Provider>
    );
}

export function useTheme() {
    return useContext(ThemeContext);
}

// Компонент перемикача теми
function ThemeToggle() {
    const { theme, toggleTheme } = useTheme();

    return (
        <button
            onClick={toggleTheme}
            className="
                p-2 rounded-lg
                bg-gray-200 dark:bg-gray-700
                text-gray-800 dark:text-gray-200
                hover:bg-gray-300 dark:hover:bg-gray-600
                transition-colors duration-200
            "
        >
            {theme === 'light' ? '🌙' : '☀️'}
        </button>
    );
}
```

### Стилізація з підтримкою теми

```jsx
function ThemedCard() {
    return (
        <div className="
            bg-white dark:bg-gray-800
            text-gray-900 dark:text-gray-100
            border border-gray-200 dark:border-gray-700
            rounded-lg shadow-md
            p-6
            transition-colors duration-200
        ">
            <h2 className="
                text-2xl font-bold mb-4
                text-gray-900 dark:text-white
            ">
                Картка з підтримкою теми
            </h2>
            
            <p className="
                text-gray-600 dark:text-gray-300
                leading-relaxed
            ">
                Цей текст автоматично адаптується до обраної теми
            </p>
            
            <button className="
                mt-4 px-6 py-2
                bg-blue-500 dark:bg-blue-600
                hover:bg-blue-600 dark:hover:bg-blue-700
                text-white
                rounded-md
                transition-colors duration-200
            ">
                Кнопка
            </button>
        </div>
    );
}
```

## Headless UI компоненти

### Інтеграція Headless UI

Headless UI надає повністю доступні, несстилізовані компоненти UI, які ідеально поєднуються з Tailwind CSS.

```bash
npm install @headlessui/react
```

### Dialog компонент

```jsx
import { Dialog, Transition } from '@headlessui/react';
import { Fragment, useState } from 'react';

function Modal() {
    const [isOpen, setIsOpen] = useState(false);

    return (
        <>
            <button
                onClick={() => setIsOpen(true)}
                className="px-6 py-3 bg-blue-500 text-white rounded-md hover:bg-blue-600"
            >
                Відкрити модальне вікно
            </button>

            <Transition appear show={isOpen} as={Fragment}>
                <Dialog
                    as="div"
                    className="relative z-10"
                    onClose={() => setIsOpen(false)}
                >
                    <Transition.Child
                        as={Fragment}
                        enter="ease-out duration-300"
                        enterFrom="opacity-0"
                        enterTo="opacity-100"
                        leave="ease-in duration-200"
                        leaveFrom="opacity-100"
                        leaveTo="opacity-0"
                    >
                        <div className="fixed inset-0 bg-black bg-opacity-25" />
                    </Transition.Child>

                    <div className="fixed inset-0 overflow-y-auto">
                        <div className="flex min-h-full items-center justify-center p-4">
                            <Transition.Child
                                as={Fragment}
                                enter="ease-out duration-300"
                                enterFrom="opacity-0 scale-95"
                                enterTo="opacity-100 scale-100"
                                leave="ease-in duration-200"
                                leaveFrom="opacity-100 scale-100"
                                leaveTo="opacity-0 scale-95"
                            >
                                <Dialog.Panel className="
                                    w-full max-w-md transform overflow-hidden
                                    rounded-2xl bg-white p-6 shadow-xl
                                    transition-all
                                ">
                                    <Dialog.Title className="text-lg font-medium leading-6 text-gray-900">
                                        Заголовок модального вікна
                                    </Dialog.Title>
                                    
                                    <div className="mt-2">
                                        <p className="text-sm text-gray-500">
                                            Контент модального вікна з важливою інформацією
                                        </p>
                                    </div>

                                    <div className="mt-4 flex gap-2">
                                        <button
                                            type="button"
                                            className="px-4 py-2 bg-blue-500 text-white rounded-md hover:bg-blue-600"
                                            onClick={() => setIsOpen(false)}
                                        >
                                            Підтвердити
                                        </button>
                                        <button
                                            type="button"
                                            className="px-4 py-2 bg-gray-200 text-gray-900 rounded-md hover:bg-gray-300"
                                            onClick={() => setIsOpen(false)}
                                        >
                                            Скасувати
                                        </button>
                                    </div>
                                </Dialog.Panel>
                            </Transition.Child>
                        </div>
                    </div>
                </Dialog>
            </Transition>
        </>
    );
}
```

### Dropdown Menu

```jsx
import { Menu, Transition } from '@headlessui/react';
import { Fragment } from 'react';

function Dropdown() {
    return (
        <Menu as="div" className="relative inline-block text-left">
            <Menu.Button className="
                inline-flex justify-center items-center
                px-4 py-2 bg-white border border-gray-300
                rounded-md shadow-sm
                hover:bg-gray-50
                focus:outline-none focus:ring-2 focus:ring-blue-500
            ">
                Опції
                <svg className="w-5 h-5 ml-2" fill="currentColor" viewBox="0 0 20 20">
                    <path fillRule="evenodd" d="M5.293 7.293a1 1 0 011.414 0L10 10.586l3.293-3.293a1 1 0 111.414 1.414l-4 4a1 1 0 01-1.414 0l-4-4a1 1 0 010-1.414z" clipRule="evenodd" />
                </svg>
            </Menu.Button>

            <Transition
                as={Fragment}
                enter="transition ease-out duration-100"
                enterFrom="transform opacity-0 scale-95"
                enterTo="transform opacity-100 scale-100"
                leave="transition ease-in duration-75"
                leaveFrom="transform opacity-100 scale-100"
                leaveTo="transform opacity-0 scale-95"
            >
                <Menu.Items className="
                    absolute right-0 mt-2 w-56
                    origin-top-right bg-white
                    divide-y divide-gray-100
                    rounded-md shadow-lg ring-1 ring-black ring-opacity-5
                    focus:outline-none
                ">
                    <div className="px-1 py-1">
                        <Menu.Item>
                            {({ active }) => (
                                <button className={`
                                    ${active ? 'bg-blue-500 text-white' : 'text-gray-900'}
                                    group flex rounded-md items-center w-full px-2 py-2 text-sm
                                `}>
                                    Редагувати
                                </button>
                            )}
                        </Menu.Item>
                        <Menu.Item>
                            {({ active }) => (
                                <button className={`
                                    ${active ? 'bg-blue-500 text-white' : 'text-gray-900'}
                                    group flex rounded-md items-center w-full px-2 py-2 text-sm
                                `}>
                                    Дублювати
                                </button>
                            )}
                        </Menu.Item>
                    </div>
                    <div className="px-1 py-1">
                        <Menu.Item>
                            {({ active }) => (
                                <button className={`
                                    ${active ? 'bg-red-500 text-white' : 'text-gray-900'}
                                    group flex rounded-md items-center w-full px-2 py-2 text-sm
                                `}>
                                    Видалити
                                </button>
                            )}
                        </Menu.Item>
                    </div>
                </Menu.Items>
            </Transition>
        </Menu>
    );
}
```

## Висновки та найкращі практики

Tailwind CSS представляє сучасний підхід до стилізації, який значно прискорює розробку та покращує підтримуваність коду. Основні принципи ефективного використання Tailwind:

**Прийміть utility-first філософію:** спочатку це може здатися незвичним, але utility-first підхід забезпечує консистентність, передбачуваність та швидкість розробки. Не бійтеся довгих списків класів у JSX – це нормальна практика.

**Використовуйте кастомізацію розумно:** Tailwind надає чудову систему дизайну з коробки, але не соромтеся кастомізувати її під потреби вашого проєкту. Додавайте власні кольори, spacing та інші значення через конфігураційний файл.

**Створюйте багаторазові компоненти:** замість дублювання довгих списків класів, інкапсулюйте повторювану логіку стилізації у React компоненти. Це забезпечує консистентність та спрощує підтримку.

**Використовуйте responsive модифікатори:** Tailwind робить responsive дизайн інтуїтивним через префікси breakpoints. Завжди починайте з mobile-first підходу.

**Інтегруйте Headless UI:** для складних інтерактивних компонентів як модальні вікна, випадаючі меню та табси, використовуйте Headless UI для забезпечення доступності без зайвого коду.

**Оптимізуйте для продакшену:** Tailwind автоматично видаляє невикористані класи в продакшн білді через PurgeCSS, але переконайтеся, що ваша конфігурація правильно налаштована для сканування всіх файлів.

Розуміння та застосування цих принципів дозволить створювати красиві, консистентні та доступні інтерфейси швидше, ніж будь-коли раніше, забезпечуючи високу якість та підтримуваність коду в довгостроковій перспективі.
