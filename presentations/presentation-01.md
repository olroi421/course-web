# Вступ до сучасної веброзробки та JavaScript ES6+

## План лекції

**Частина I. Веброзробка**

1. Архітектура сучасних вебдодатків
2. Client-Server взаємодія та HTTP
3. REST API vs GraphQL vs gRPC
4. SPA vs MPA vs SSR
5. Огляд технологічних стеків

**Частина II. JavaScript ES6+**

6. Еволюція JavaScript та ES6+
7. Деструктуризація, spread/rest, arrow functions
8. Модульна система import/export
9. Promises, async/await, обробка помилок
10. Event Loop та асинхронність
11. Практичні паттерни та оптимізація

## **Частина I. Вступ до сучасної веброзробки**

## **1. Архітектура сучасних вебдодатків**

**Вебдодаток** — це програмне забезпечення, яке працює на вебсервері та доступне користувачам через веббраузер без необхідності встановлення на локальному комп'ютері.


## Еволюція вебтехнологій

```mermaid
timeline
    title Розвиток вебдодатків

    1990-ті : 📄 Статичні HTML сторінки
             : Мінімальна інтерактивність
             : Простий контент

    2000-ні : 🔄 Динамічні вебсайти
             : PHP, ASP.NET, JSP
             : Бази даних

    2010-ні : ⚡ Вебдодатки
             : AJAX, SPA
             : REST API

    2020-ні : 🚀 Хмарні та AI-орієнтовані додатки
             : Мікросервіси, PWA
             : Real-time, AI-асистенти
```

## Сучасна багатошарова архітектура

```mermaid
graph TB
    subgraph "🌐 Клієнтський рівень"
        A[Веббраузер]
        B[Мобільний застосунок]
        C[Десктопний застосунок]
    end

    subgraph "🎨 Рівень представлення"
        D[CDN]
        E[Клієнтський застосунок]
    end

    subgraph "⚙️ Рівень застосунку"
        F[Балансувальник навантаження]
        G[API-шлюз]
        H[Мікросервіси]
    end

    subgraph "💾 Рівень даних"
        I[База даних]
        J[Кеш]
        K[Файлове сховище]
    end

    A --> D
    B --> D
    C --> D
    D --> E
    E --> F
    F --> G
    G --> H
    H --> I
    H --> J
    H --> K
```

## Мікросервісна архітектура

### 📊 Порівняння підходів:

| Монолітна архітектура | Мікросервісна архітектура |
|:---:|:---:|
| 📦 Один великий застосунок | 🧩 Множина малих сервісів |
| 🛠️ Одна технологія | 🎨 Різні технології |
| 📈 Важко масштабувати | ⚡ Легко масштабувати |
| 🐛 Збій = весь застосунок | 🛡️ Ізольовані збої |

### Приклад: e-commerce платформа

```mermaid
graph LR
    A[🎨 Клієнтська частина] --> B[🚪 API-шлюз]
    B --> C[👤 Сервіс користувачів]
    B --> D[📦 Сервіс товарів]
    B --> E[🛒 Сервіс кошика]
    B --> F[💳 Сервіс платежів]
    B --> G[📧 Сервіс сповіщень]
```

## **2. Client-Server взаємодія та HTTP**

## HTTP протокол: основи

### Модель Request-Response:

```mermaid
sequenceDiagram
    participant C as 🌐 Клієнт
    participant S as 🖥️ Сервер

    C->>S: HTTP Request
    Note over C,S: GET /api/users

    S-->>S: 🔧 Обробка запиту

    S->>C: HTTP Response
    Note over C,S: 200 OK + JSON

    C-->>C: 🎨 Оновлення інтерфейсу
```

### Структура HTTP запиту:

```http
GET /api/users/123 HTTP/1.1
Host: api.example.com
Authorization: Bearer eyJhbGciOiJIUzI1NiIs...
Accept: application/json
Content-Type: application/json

{
  "include": ["profile", "preferences"]
}
```

## HTTP методи

| Метод | Призначення | Ідемпотентний | Приклад |
|-------|-------------|---------------|---------|
| **GET** | 📖 Отримати дані | ✅ | `GET /api/users` |
| **POST** | ➕ Створити ресурс | ❌ | `POST /api/users` |
| **PUT** | 🔄 Повне оновлення | ✅ | `PUT /api/users/123` |
| **PATCH** | ✏️ Часткове оновлення | ❌ | `PATCH /api/users/123` |
| **DELETE** | 🗑️ Видалити ресурс | ✅ | `DELETE /api/users/123` |

## HTTP статус коди

```mermaid
graph LR
    A[📊 HTTP Status] --> B[2xx ✅ Success]
    A --> C[3xx ↩️ Redirect]
    A --> D[4xx ❌ Client Error]
    A --> E[5xx 💥 Server Error]

    B --> B1[200 OK]
    B --> B2[201 Created]

    C --> C1[301 Moved]
    C --> C2[304 Not Modified]

    D --> D1[400 Bad Request]
    D --> D2[401 Unauthorized]
    D --> D3[404 Not Found]

    E --> E1[500 Internal Error]
    E --> E2[503 Unavailable]
```

### Найважливіші коди:

- 200 OK - успішний запит ✅
- 400 Bad Request - некоректний запит ❌
- 401 Unauthorized - потрібна авторизація 🔒
- 404 Not Found - ресурс не знайдено 🔍
- 500 Internal Server Error - помилка сервера 💥

## Асинхронна взаємодія

### AJAX vs традиційні форми:

```mermaid
graph LR
    subgraph "🔄 Традиційні форми"
        A1[Дія користувача] --> B1[Перезавантаження сторінки]
        B1 --> C1[Відповідь сервера]
        C1 --> D1[Повне відтворення сторінки]
    end

    subgraph "⚡ AJAX"
        A2[Дія користувача] --> B2[Фоновий запит]
        B2 --> C2[Відповідь сервера]
        C2 --> D2[Оновлення частини сторінки]
    end
```

### Fetch API приклад:

```javascript
async function fetchUsers() {
  try {
    const response = await fetch('/api/users');
    const users = await response.json();
    displayUsers(users);
  } catch (error) {
    showError('Помилка завантаження');
  }
}
```

## **3. REST vs GraphQL vs gRPC**

## REST API

### Принципи REST:

1. 🔄 **Stateless** - без збереження стану
2. 📋 **Resource-based** - ресурсно-орієнтований
3. 🛠️ **HTTP methods** - стандартні методи
4. 📊 **Multiple representations** - JSON, XML

### RESTful URL структура:

```
GET    /api/users          # Список користувачів
GET    /api/users/123      # Конкретний користувач
POST   /api/users          # Створити користувача
PUT    /api/users/123      # Оновити користувача
DELETE /api/users/123      # Видалити користувача
```

### ✅ Переваги REST:

- Простота та зрозумілість
- Стандартні HTTP методи
- Відмінне кешування
- Широка підтримка

### ❌ Недоліки REST:

- Over-fetching (зайві дані)
- Under-fetching (недостатньо даних)
- Множинні запити

## GraphQL

### GraphQL - одна точка входу:

```mermaid
graph LR
    A[📱 Клієнт] --> B[🎯 Єдиний endpoint]
    B --> C[🔍 GraphQL Query]
    C --> D[⚙️ Резолвери]
    D --> E[💾 Різні джерела даних]
```

### Приклад GraphQL запиту:

```graphql
query GetUserWithPosts($userId: ID!) {
  user(id: $userId) {
    name
    email
    posts {
      title
      createdAt
      comments {
        text
        author
      }
    }
  }
}
```

### ✅ Переваги GraphQL:

- Точні дані - тільки потрібні поля
- Один endpoint для всього
- Сильна типізація
- Real-time підтримка

### ❌ Недоліки GraphQL:

- Складність кешування
- Вища крива навчання
- Проблеми з продуктивністю

## gRPC

### gRPC особливості:

- 🔥 **Високопродуктивний** - бінарний протокол
- 🌐 **HTTP/2** - мультиплексування
- 🎯 **Багатомовність** - 10+ мов
- 📡 **Streaming** - 4 типи комунікації

### Типи gRPC викликів:

```mermaid
graph TB
    A[🔄 Unary<br/>Request → Response]
    B[📤 Server Streaming<br/>Request → Stream]
    C[📥 Client Streaming<br/>Stream → Response]
    D[🔄 Bidirectional<br/>Stream ↔ Stream]
```

## tRPC — сучасна альтернатива

- 🎯 Тільки для full-stack **TypeScript**-проєктів (наприклад, з Next.js)
- 🔗 Виклик серверних функцій як звичайних локальних функцій
- ✅ Типізація без окремої схеми (на відміну від GraphQL)
- ⚠️ Не замінює REST/GraphQL для публічних API

## Порівняння підходів

| Критерій | REST | GraphQL | gRPC |
|----------|------|---------|------|
| **🏃 Продуктивність** | Середня | Середня | **Висока** |
| **💾 Кешування** | **Відмінне** | Складне | Обмежене |
| **📚 Вивчення** | **Легко** | Середня | Складно |
| **🎯 Точність даних** | Низька | **Висока** | Висока |
| **🌐 Підтримка браузерів** | **Повна** | Повна | Обмежена |

### Коли використовувати:

```mermaid
flowchart TD
    A[Вибір API типу] --> B{Тип проєкту?}

    B -->|Публічний API| C[🌐 REST]
    B -->|Мобільний застосунок| D[🎯 GraphQL]
    B -->|Мікросервіси| E[🚀 gRPC]
    B -->|Прототип| C
```

## **4. SPA vs MPA vs SSR**

## Single Page Application (SPA)

### SPA - один HTML файл:

```mermaid
sequenceDiagram
    participant U as 👤 Користувач
    participant B as 🌐 Браузер
    participant S as 🖥️ Сервер

    U->>B: Перший візит
    B->>S: GET /
    S->>B: HTML + JS Bundle
    B-->>B: ⚡ Ініціалізація застосунку

    U->>B: Навігація
    B-->>B: 🔄 Маршрутизація на клієнті
    B-->>B: 🎨 Оновлення інтерфейсу
```

### ✅ Переваги SPA:

- ⚡ Миттєва навігація
- 🎨 Багата інтерактивність
- 😊 Відмінний UX
- 📱 Досвід, близький до нативного

### ❌ Недоліки SPA:

- 🐌 Повільне початкове завантаження
- 🔍 Проблеми з SEO
- 📱 JavaScript обов'язковий

## Multi-Page Application (MPA)

### MPA - традиційний підхід:

```mermaid
sequenceDiagram
    participant U as 👤 Користувач
    participant B as 🌐 Браузер
    participant S as 🖥️ Сервер

    U->>B: Перехід на сторінку
    B->>S: GET /products
    S->>B: 📄 HTML сторінка

    U->>B: Інша сторінка
    B->>S: GET /about
    S->>B: 📄 Нова HTML сторінка
```

### ✅ Переваги MPA:

- 🔍 Відмінне SEO
- ⚡ Швидке перше завантаження
- 🎯 Працює без JavaScript
- 📊 Простий аналіз

### ❌ Недоліки MPA:

- 🐌 Повільна навігація
- 🔄 Перезавантаження сторінок
- 📱 Менш інтерактивний

## Server-Side Rendering (SSR)

### SSR - найкраще з двох світів:

```mermaid
sequenceDiagram
    participant U as 👤 Користувач
    participant B as 🌐 Браузер
    participant S as 🖥️ SSR сервер

    U->>B: Перший візит
    B->>S: GET /products
    S-->>S: 🔧 Відтворення React
    S->>B: Готовий HTML + JS
    B-->>B: 💧 Гідратація

    U->>B: Навігація
    B-->>B: ⚡ Маршрутизація на клієнті
```

## React Server Components

- 🖥️ Компоненти, що виконуються **лише на сервері**
- 🚫 Ніколи не потрапляють у клієнтський JS-бандл
- 💾 Прямий доступ до БД без окремого API-шару
- ⚛️ Основа App Router у Next.js

## Порівняння підходів

| Критерій | SPA | MPA | SSR |
|----------|-----|-----|-----|
| **⚡ Початкове завантаження** | 🐌 Повільне | 🚀 Швидке | 🚀 Швидке |
| **🔄 Навігація** | ⚡ Миттєва | 🐌 Повільна | ⚡ Миттєва |
| **🔍 SEO** | ❌ Погане | ✅ Відмінне | ✅ Відмінне |
| **🎨 Інтерактивність** | 🌟 Висока | 📊 Низька | 🌟 Висока |
| **🏗️ Складність** | 📊 Середня | 🟢 Низька | 🔴 Висока |


## **5. Технологічні стеки**

## MEAN Stack

```mermaid
graph TB
    subgraph "🅰️ MEAN Stack"
        A[🅰️ Angular<br/>Клієнтська частина]
        B[🚀 Express.js<br/>Вебфреймворк]
        C[🟢 Node.js<br/>Runtime]
        D[🍃 MongoDB<br/>База даних]
    end

    A --> B
    B --> C
    C --> D
```

### Особливості MEAN:

- ✅ Повний JavaScript стек
- ✅ TypeScript підтримка з коробки
- ✅ Структурований підхід Angular
- ❌ Менша гнучкість, спадна популярність серед нових проєктів

## MERN Stack

```mermaid
graph TB
    subgraph "⚛️ MERN Stack"
        A[⚛️ React<br/>Клієнтська частина]
        B[🚀 Express.js<br/>Вебфреймворк]
        C[🟢 Node.js<br/>Runtime]
        D[🍃 MongoDB<br/>База даних]
    end

    A --> B
    B --> C
    C --> D
```

### Особливості MERN:

- ✅ Велика екосистема React
- ✅ Висока гнучкість
- ✅ Швидка розробка (Vite замість Webpack)
- ❌ Потребує більше налаштувань

## Next.js Full-Stack

```mermaid
graph TB
    subgraph "⚡ Next.js Ecosystem"
        A[⚡ Next.js 16<br/>React 19.2 + SSR/SSG]
        B[🔧 Route Handlers<br/>Серверна логіка]
        C[💾 База даних<br/>PostgreSQL/MongoDB]
        D[☁️ Розгортання<br/>Vercel/Netlify]
    end

    A --> B
    B --> C
    A --> D
```

### Переваги Next.js:
- ⚡ SSR/SSG з коробки, App Router — стандарт
- 🎯 Маршрутизація на основі файлової структури
- 🔧 Turbopack — стандартний бандлер
- 📊 React Compiler: автооптимізація без useMemo/useCallback

## Bun та Deno

- ⚡ **Bun** — швидкий runtime із вбудованим пакетним менеджером і бандлером
- 🦕 **Deno** — TypeScript "з коробки", сувора модель безпеки
- 🟢 **Node.js LTS** лишається базовим вибором курсу (2026: лінія 24, восени — перехід на 26)

## Порівняння стеків

| Стек | Складність | SEO | Екосистема | Продуктивність |
|------|------------|-----|------------|----------------|
| **MEAN** | 🔴 Висока | ✅ Відмінно | 📊 Середня | 📈 Добра |
| **MERN** | 🟡 Середня | 🛠️ Потребує SSR | 🌟 Велика | 🚀 Висока |
| **Next.js** | 🟡 Середня | ✅ Нативно | 📈 Провідна | 🚀 Відмінна |


## Підсумки частини I

### 🎯 Ключові принципи сучасної веброзробки:

1. **📐 Архітектура**: розуміння багатошарової архітектури
2. **🌐 API**: вміння проєктувати REST/GraphQL/tRPC API
3. **⚡ Продуктивність**: оптимізація швидкості завантаження
4. **🔍 SEO**: забезпечення пошукової оптимізації
5. **🧪 Тестування**: покриття коду тестами
6. **🚀 DevOps**: CI/CD та автоматизація
7. **🤖 AI-інструменти**: агентні асистенти розробки як частина щоденного процесу
## **Частина II. JavaScript ES6+ та асинхронне програмування**

## Еволюція JavaScript

### Ключові етапи розвитку

- 1995 - Народження JavaScript (Netscape, Brendan Eich за 10 днів)
- 1997 - Стандартизація як ECMAScript (міжнародний стандарт)
- 2009 - ES5 з JSON та strict mode (перший великий крок)
- 2015 - ES6/ES2015 - революційні зміни (класи, модулі, Promise)
- 2016+ - Щорічні оновлення ES (передбачуваний розвиток)

### Що змінив ES6+?

JavaScript перетворився з мови сценаріїв на повноцінну платформу для великих додатків:

- ✅ Модульність та інкапсуляція
- ✅ Читабельний асинхронний код
- ✅ Сучасний синтаксис
- ✅ Кращі інструменти розробки

## ES6+ у продовженні: ES2020–ES2024

### Стандарт оновлюється щороку

- `?.` **Optional chaining** — безпечне звернення до вкладених властивостей
- `??` **Nullish coalescing** — значення за замовчуванням тільки для `null`/`undefined`
- **Top-level `await`** — `await` прямо в тілі модуля
- `array.at(-1)` — звернення з кінця масиву без обчислення індексу
- `structuredClone()` — глибоке клонування об'єктів "з коробки"
- `Object.groupBy()` / `Map.groupBy()` — угруповання масиву за ключем (ES2024)

## Деструктуризація

**Деструктуризація** дозволяє "розпаковувати" значення з масивів або властивості з об'єктів в окремі змінні за один крок. Це не просто синтаксичний цукор — це новий спосіб мислення про роботу з даними.

### До ES6: громіздкий код
```javascript
const person = { name: 'Іван', age: 25, city: 'Київ' };
const personName = person.name;
const personAge = person.age;
const personCity = person.city;
```

### ES6+: елегантне рішення
```javascript
const { name, age, city } = person;
console.log(name, age, city); // 'Іван' 25 'Київ'
```

*Код стає більш декларативним — ми говоримо "що" хочемо, а не "як" це отримати.*

## Деструктуризація масивів

### Основний синтаксис
```javascript
const [x, y, z] = [10, 20, 30];
console.log(x, y, z); // 10 20 30

// Пропуск елементів
const [first, , third] = [1, 2, 3];
console.log(first, third); // 1 3

// Значення за замовчуванням
const [width = 100, height = 200] = [300];
console.log(width, height); // 300 200
```

### Rest в деструктуризації
```javascript
const [head, ...tail] = [1, 2, 3, 4, 5];
console.log(head); // 1
console.log(tail); // [2, 3, 4, 5]
```

## Деструктуризація об'єктів

### Перейменування змінних
```javascript
const apiResponse = {
    user_name: 'Марія',
    user_age: 28
};

const {
    user_name: name,
    user_age: age
} = apiResponse;
```

### Глибока деструктуризація
```javascript
const user = {
    profile: {
        contacts: { email: 'maria@example.com' }
    }
};

const { profile: { contacts: { email } } } = user;
console.log(email); // 'maria@example.com'
```

## Деструктуризація в функціях

### Традиційний підхід
```javascript
function createUser(name, age, email, city) {
    return { name, age, email, city };
}

// Проблема: порядок має значення
const user = createUser('Олексій', 30, 'alex@example.com', 'Львів');
```

### ES6+ підхід
```javascript
function createUser({ name, age, email, city = 'Київ' }) {
    return { name, age, email, city };
}

// Переваги: порядок не важливий, значення за замовчуванням
const user = createUser({
    email: 'dev@example.com',
    name: 'Анна',
    age: 27
});
```

## Spread оператор: розгортання структур

**Spread оператор (...)** дозволяє "розпакувати" ітеровані об'єкти. Це універсальний інструмент для копіювання, об'єднання та передачі даних без мутації оригінальних структур.

### Об'єднання масивів
```javascript
const primaryColors = ['червоний', 'синій', 'жовтий'];
const secondaryColors = ['зелений', 'помаранчевий'];

const allColors = [...primaryColors, ...secondaryColors];
// ['червоний', 'синій', 'жовтий', 'зелений', 'помаранчевий']

// Копіювання масиву (shallow copy)
const colorsCopy = [...primaryColors];
```

### Spread з об'єктами
```javascript
const baseConfig = {
    theme: 'light',
    language: 'uk'
};

const userConfig = {
    ...baseConfig,
    theme: 'dark', // Перезаписує існуючу властивість
    fontSize: 'large' // Додає нову властивість
};
```

*Spread дотримується принципу immutability — створює нові об'єкти замість зміни існуючих.*

## Rest параметри: збирання аргументів

**Rest параметри** дозволяють функціям приймати змінну кількість аргументів як масив. Це сучасна заміна проблематичного об'єкта `arguments`.

### Проблема з arguments
```javascript
// Застарілий підхід
function oldSum() {
    var total = 0;
    for (var i = 0; i < arguments.length; i++) { // arguments не справжній масив
        total += arguments[i];
    }
    return total;
}
```

### ES6+ рішення
```javascript
function sum(...numbers) {
    return numbers.reduce((total, num) => total + num, 0);
}

console.log(sum(1, 2, 3, 4, 5)); // 15
console.log(sum(10, 20)); // 30
```

*Rest параметри завжди дають справжній масив зі всіма його методами (map, filter, reduce тощо).*

## Arrow Functions: функціональна революція

### Еволюція синтаксису
```javascript
// 1. Function declaration
function multiply(a, b) {
    return a * b;
}

// 2. Function expression
const multiply = function(a, b) {
    return a * b;
};

// 3. Arrow function
const multiply = (a, b) => a * b;
```

### Різні форми
```javascript
const square = x => x * x; // Один параметр
const greet = () => console.log('Привіт!'); // Без параметрів
const process = data => {
    // Багаторядкове тіло
    return data.map(item => item * 2);
};
```

## Контекст this у стрілочних функціях

**Найважливіша відмінність** стрілочних функцій — це поведінка `this`. Традиційні функції створюють власний контекст, а стрілочні наслідують його з оточуючого scope.

### Проблема традиційних функцій
```javascript
const timer = {
    seconds: 0,
    start: function() {
        setInterval(function() {
            this.seconds++; // this = global/window ❌
            console.log(this.seconds); // undefined
        }, 1000);
    }
};
```

### Рішення з arrow functions
```javascript
const timer = {
    seconds: 0,
    start: function() {
        setInterval(() => {
            this.seconds++; // this = timer ✅
            console.log(this.seconds);
        }, 1000);
    }
};
```

*Стрілочні функції "запам'ятовують" контекст з місця створення (лексичний scope).*

## Модульна система: організація коду

ES6 модулі вирішили одну з найбільших проблем JavaScript — відсутність стандартного способу організації коду. До цього використовували різні підходи з їхніми недоліками.

### До ES6: глобальне забруднення
```javascript
// file1.js
var userName = 'Іван';
function processUser() { /* ... */ }

// file2.js
var userName = 'Марія'; // ❌ Конфлікт! Перезаписує глобальну змінну
function processUser() { /* ... */ } // ❌ Перезапис функції!
```

### ES6+ модулі: інкапсуляція
```javascript
// mathematics.js
export const PI = 3.14159;
export function add(a, b) { return a + b; }
export default class Calculator { /* ... */ }

// app.js
import Calculator, { add, PI } from './mathematics.js';
```

*Модулі забезпечують власну область видимості, явні залежності та контрольований публічний API.*

## Named Export vs Default Export

### Named Export: множинні експорти
```javascript
// utils.js
export const formatDate = (date) => date.toLocaleDateString();
export const formatTime = (date) => date.toLocaleTimeString();
export const CURRENT_YEAR = new Date().getFullYear();

// app.js
import { formatDate, formatTime, CURRENT_YEAR } from './utils.js';
```

### Default Export: головний експорт
```javascript
// logger.js
export default class Logger {
    log(message) { console.log(message); }
}

// app.js
import Logger from './logger.js'; // Довільне ім'я
```

## Типи імпорту

### Іменований імпорт
```javascript
import { add, multiply } from './math.js';
```

### Namespace імпорт
```javascript
import * as MathUtils from './math.js';
console.log(MathUtils.add(1, 2));
```

### Динамічний імпорт
```javascript
async function loadMath() {
    const math = await import('./math.js');
    return math.add(1, 2);
}
```

### Умовний імпорт
```javascript
if (condition) {
    const module = await import('./conditional-module.js');
    module.initialize();
}
```

## Promises: концептуальна революція

**Promise** — це "контракт" про майбутнє значення. Він представляє результат асинхронної операції, яка може завершитися успішно або з помилкою. Promises вирішили проблему "callback hell" та надали структурований підхід до асинхронності.

### Стани Promise
```mermaid
stateDiagram-v2
    [*] --> Pending
    Pending --> Fulfilled: resolve(value)
    Pending --> Rejected: reject(error)
    Fulfilled --> [*]
    Rejected --> [*]
```

### Створення Promise
```javascript
const fetchData = (id) => {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            if (id > 0) {
                resolve({ id, name: `User ${id}` });
            } else {
                reject(new Error('Invalid ID'));
            }
        }, 1000);
    });
};
```

*Promise може бути тільки в одному стані одночасно і не може змінити стан повторно.*

## Promise методи

### Promise.all() - чекає на всі
```javascript
const [user, posts, stats] = await Promise.all([
    fetchUser(id),
    fetchPosts(id),
    fetchStats(id)
]);
// Якщо один fail - весь Promise.all fails
```

### Promise.allSettled() - чекає на всі, не зупиняється
```javascript
const results = await Promise.allSettled([
    fetchUser(id),
    fetchPosts(id),
    fetchStats(id)
]);

results.forEach(result => {
    if (result.status === 'fulfilled') {
        console.log('Success:', result.value);
    } else {
        console.error('Error:', result.reason);
    }
});
```

## Promise.race() та Promise.any()

### Promise.race() - перший завершений
```javascript
const winner = await Promise.race([
    fetch('/api/data'),
    new Promise((_, reject) =>
        setTimeout(() => reject(new Error('Timeout')), 5000)
    )
]);
```

### Promise.any() - перший успішний
```javascript
const data = await Promise.any([
    fetchFromPrimaryServer(),
    fetchFromSecondaryServer(),
    fetchFromCache()
]);
// Повертає дані з першого успішного сервера
```

## async/await

**async/await** — це новий спосіб мислення про асинхронний код. Він дозволяє писати асинхронний код у стилі синхронного, зберігаючи всі переваги неблокуючого виконання.

### Еволюція від callbacks до async/await

#### Callback Hell
```javascript
// Піраміда смерті — код росте вправо, а не вниз
fetchUser(id, (userErr, user) => {
    if (userErr) return callback(userErr);
    fetchPosts(user.id, (postsErr, posts) => {
        if (postsErr) return callback(postsErr);
        fetchStats(user.id, (statsErr, stats) => {
            if (statsErr) return callback(statsErr);
            callback(null, { user, posts, stats });
        });
    });
});
```

*Callback hell робить код важкочитабельним, схильним до помилок і складним для тестування.*

## Promise Chains vs Async/Await

### Promise chains
```javascript
function loadUserData(id) {
    return fetchUser(id)
        .then(user => {
            return fetchPosts(user.id)
                .then(posts => ({ user, posts }));
        })
        .then(data => {
            return fetchStats(data.user.id)
                .then(stats => ({ ...data, stats }));
        });
}
```

### Async/await - найкраще рішення
```javascript
async function loadUserData(id) {
    const user = await fetchUser(id);
    const posts = await fetchPosts(user.id);
    const stats = await fetchStats(user.id);
    return { user, posts, stats };
}
```

## Послідовність vs Паралельність

### ❌ НЕЕФЕКТИВНО: послідовне виконання
```javascript
async function loadDataSequential(id) {
    const user = await fetchUser(id);      // ~1 сек
    const posts = await fetchPosts(id);    // ~1 сек
    const stats = await fetchStats(id);    // ~1 сек
    // Загалом: ~3 секунди
    return { user, posts, stats };
}
```

### ✅ ЕФЕКТИВНО: паралельне виконання
```javascript
async function loadDataParallel(id) {
    const [user, posts, stats] = await Promise.all([
        fetchUser(id),
        fetchPosts(id),
        fetchStats(id)
    ]);
    // Загалом: ~1 секунда
    return { user, posts, stats };
}
```

## Обробка помилок з async/await

### Базова обробка
```javascript
async function fetchUserSafely(id) {
    try {
        const user = await fetchUser(id);
        return user;
    } catch (error) {
        console.error('User fetch failed:', error.message);
        return { id, name: 'Unknown User' }; // Fallback
    }
}
```

### Власні класи помилок
```javascript
class ValidationError extends Error {
    constructor(message, field) {
        super(message);
        this.name = 'ValidationError';
        this.field = field;
    }
}

class NetworkError extends Error {
    constructor(message) {
        super(message);
        this.name = 'NetworkError';
        this.retryable = true;
    }
}
```

## Event Loop: серце асинхронності

**Event Loop** — це механізм, який дозволяє однопоточному JavaScript виконувати неблокуючі операції. Він координує виконання коду, збір та обробку подій, виконання під-завдань в черзі.

### Архітектура JavaScript Runtime

```mermaid
graph TB
    subgraph "JavaScript Engine"
        CS[Call Stack<br/>Стек викликів функцій]
        H[Heap<br/>Об'єкти в пам'яті]
    end
    subgraph "Web APIs"
        T[Timers<br/>setTimeout/setInterval]
        F[Fetch<br/>HTTP запити]
        D[DOM Events<br/>click, scroll]
    end
    subgraph "Event Loop"
        MT[Macrotask Queue<br/>Великі завдання]
        MC[Microtask Queue<br/>Дрібні завдання]
        EL[Event Loop<br/>Координатор]
    end

    CS --> T
    CS --> F
    CS --> D
    T --> MT
    F --> MT
    D --> MT
    MC --> EL
    MT --> EL
    EL --> CS
```

*Event Loop постійно перевіряє черги та передає завдання до Call Stack коли він порожній.*

## Пріоритети виконання в Event Loop

### Порядок виконання

1. **Синхронний код** (Call Stack)
2. **Microtasks** (Promises, queueMicrotask)
3. **Macrotasks** (setTimeout, DOM events)
4. **Render** (тільки в браузері)

```javascript
console.log('1: Синхронний'); // 1

setTimeout(() => console.log('2: Macrotask'), 0); // 4

Promise.resolve().then(() => console.log('3: Microtask')); // 3

console.log('4: Синхронний'); // 2

// Вивід: 1 → 4 → 3 → 2
```

## Macrotasks vs Microtasks

Розуміння різниці між типами завдань критично важливе для передбачення порядку виконання коду.

### Macrotasks (Task Queue)

Великі завдання, кожен Event Loop цикл обробляє тільки одну macrotask:

- `setTimeout` / `setInterval`
- I/O операції (файли, мережа)
- DOM події (click, scroll)
- HTTP запити

### Microtasks (Microtask Queue)

Дрібні завдання з високим пріоритетом, всі microtasks виконуються перед наступною macrotask:

- Promise callbacks (`.then()`, `.catch()`)
- `queueMicrotask()`
- `async/await`
- MutationObserver (браузер)

**Правило:** Всі microtasks виконуються перед будь-якими macrotasks!

*Це означає, що Promise завжди "обганяють" setTimeout, навіть з затримкою 0.*

## Демонстрація Event Loop

```javascript
console.log('🟢 Start');

setTimeout(() => {
    console.log('🔴 setTimeout (macrotask)');
    Promise.resolve().then(() => {
        console.log('🟡 Promise in setTimeout (microtask)');
    });
}, 0);

Promise.resolve().then(() => {
    console.log('🟡 Promise (microtask)');
});

queueMicrotask(() => {
    console.log('🟡 queueMicrotask (microtask)');
});

console.log('🟢 End');

/* Результат:
🟢 Start
🟢 End
🟡 Promise (microtask)
🟡 queueMicrotask (microtask)
🔴 setTimeout (macrotask)
🟡 Promise in setTimeout (microtask)
*/
```

## Уникнення блокування Event Loop

**Блокування Event Loop** — одна з найсерйозніших проблем JavaScript додатків. Коли синхронний код виконується занадто довго, він "заморожує" всю програму.

### ❌ ПОГАНО: блокуючий код
```javascript
function heavyCalculation() {
    let result = 0;
    for (let i = 0; i < 10000000000; i++) { // 10 мільярдів ітерацій
        result += Math.random();
    }
    return result; // 5+ секунд блокування UI, користувач не може взаємодіяти
}
```

### ✅ ДОБРЕ: неблокуючий підхід
```javascript
function heavyCalculationAsync(callback) {
    let result = 0, processed = 0;
    const total = 10000000000, chunkSize = 1000000; // Обробляємо по частинах

    function processChunk() {
        const end = Math.min(processed + chunkSize, total);
        for (let i = processed; i < end; i++) {
            result += Math.random();
        }
        processed = end;

        if (processed < total) {
            setTimeout(processChunk, 0); // Передаємо контроль Event Loop
        } else {
            callback(result);
        }
    }
    processChunk();
}
```

*Розбиття важких операцій на частини дозволяє браузеру обробляти інші події між обчисленнями.*

## Web Workers для важких обчислень

**Web Workers** дозволяють виконувати JavaScript код в окремих потоках, повністю уникаючи блокування головного потоку UI.

```javascript
// Створення Web Worker для неблокуючих обчислень
function heavyComputationWithWorker(data) {
    return new Promise((resolve, reject) => {
        // Код worker-а як рядок (в реальному проєкті це окремий файл)
        const workerCode = `
            self.onmessage = function(e) {
                const data = e.data;
                let result = 0;

                // Важкі обчислення виконуються в окремому потоці
                for (let i = 0; i < data.length; i++) {
                    result += Math.sqrt(data[i]);
                }

                // Відправляємо результат назад
                self.postMessage(result);
            };
        `;

        const blob = new Blob([workerCode]);
        const worker = new Worker(URL.createObjectURL(blob));

        worker.onmessage = (e) => {
            resolve(e.data);
            worker.terminate(); // Очищуємо ресурси
        };

        worker.postMessage(data);
    });
}
```

*Worker працює паралельно з основним потоком, інтерфейс лишається чутливим до дій користувача.*

## Retry механізм з Exponential Backoff

```javascript
class RetryManager {
    constructor(options = {}) {
        this.maxRetries = options.maxRetries || 3;
        this.baseDelay = options.baseDelay || 1000;
        this.backoffFactor = options.backoffFactor || 2;
    }

    async executeWithRetry(operation) {
        let lastError;

        for (let attempt = 0; attempt <= this.maxRetries; attempt++) {
            try {
                return await operation();
            } catch (error) {
                lastError = error;

                if (attempt === this.maxRetries || !this.isRetryable(error)) {
                    break;
                }

                const delay = this.baseDelay * Math.pow(this.backoffFactor, attempt);
                console.warn(`Retry attempt ${attempt + 1} after ${delay}ms`);
                await this.delay(delay);
            }
        }

        throw lastError;
    }
}
```

## Connection Pool для оптимізації

```javascript
class ConnectionPool {
    constructor(maxConnections = 5) {
        this.maxConnections = maxConnections;
        this.activeConnections = 0;
        this.queue = [];
    }

    async execute(operation, priority = 0) {
        return new Promise((resolve, reject) => {
            this.queue.push({ operation, resolve, reject, priority });
            this.queue.sort((a, b) => b.priority - a.priority); // Сортування за пріоритетом
            this.processQueue();
        });
    }

    async processQueue() {
        if (this.activeConnections >= this.maxConnections || !this.queue.length) {
            return;
        }

        const { operation, resolve, reject } = this.queue.shift();
        this.activeConnections++;

        try {
            const result = await operation();
            resolve(result);
        } catch (error) {
            reject(error);
        } finally {
            this.activeConnections--;
            this.processQueue();
    }
}
```

*Кеш з TTL та fallback стратегіями забезпечує баланс між свіжістю даних та продуктивністю.*

## Розумне кешування асинхронних операцій

```javascript
class SmartAsyncCache {
    constructor(options = {}) {
        this.cache = new Map();
        this.defaultTTL = options.ttl || 300000; // 5 хвилин
        this.stats = { hits: 0, misses: 0 };
    }

    async get(key, fetchFunction, options = {}) {
        const cached = this.cache.get(key);
        const ttl = options.ttl || this.defaultTTL;

        // Перевірка кешу
        if (cached && !this.isExpired(cached, ttl)) {
            this.stats.hits++;
            return cached.data;
        }

        this.stats.misses++;

        try {
            const data = await fetchFunction();
            this.set(key, data);
            return data;
        } catch (error) {
            // Fallback до застарілих даних при помилці
            if (cached && options.fallbackToStale) {
                return cached.data;
            }
            throw error;
        }
    }
}
```

## Практичний приклад: Real-time система

Real-time системи вимагають особливого підходу до Event Loop оптимізації, оскільки потрібно обробляти велику кількість повідомлень без блокування UI.

```javascript
class RealtimeMessaging {
    constructor(options) {
        this.wsUrl = options.wsUrl;
        this.messageBuffer = []; // Буфер для batch обробки
        this.batchSize = options.batchSize || 10;
        this.batchInterval = options.batchInterval || 100; // 100ms
    }

    handleMessage(message) {
        // Batch обробка для оптимізації Event Loop
        this.messageBuffer.push(message);

        // Відкладена обробка для групування повідомлень
        if (!this.batchTimer) {
            this.batchTimer = setTimeout(() => {
                this.processBatchedMessages();
            }, this.batchInterval);
        }

        // Негайна обробка при переповненні буферу
        if (this.messageBuffer.length >= this.batchSize) {
            clearTimeout(this.batchTimer);
            this.processBatchedMessages();
        }
    }

    processBatchedMessages() {
        const messages = this.messageBuffer.splice(0); // Очищуємо буфер
        this.batchTimer = null;

        // queueMicrotask для неблокуючої обробки
        queueMicrotask(() => {
            messages.forEach(msg => this.processMessage(msg));
        });
    }
}
```

*Batch обробка зменшує кількість операцій DOM та покращує загальну продуктивність.*

## Найкращі практики

Ефективне використання сучасного JavaScript вимагає розуміння не тільки синтаксису, але й принципів продуктивності, читабельності та підтримуваності коду.

### ✅ DO (Роби)

- Використовуй `const`/`let` замість `var` (блочна область видимості)
- Віддавай перевагу `async/await` над Promise chains (читабельність)
- Структуруй код в модулі з чіткими залежностями (архітектура)
- Обробляй помилки на кожному рівні (надійність)
- Використовуй паралельне виконання де можливо (продуктивність)
- Кешуй результати довгих операцій (оптимізація)
- Реалізовуй retry логіку для нестабільних операцій (стійкість)

### ❌ DON'T (Не роби)

- Не блокуй Event Loop важкими обчисленнями
- Не ігноруй помилки в асинхронному коді
- Не використовуй nested callbacks (callback hell)
- Не забувай про cleanup (clear timers, close connections)
- Не використовуй `for...in` з масивами
- Не модифікуй прототипи вбудованих об'єктів

## Підсумки

### Архітектура + JavaScript ES6+ = фундамент курсу

- 🌐 **Частина I**: архітектура вебдодатку, HTTP, REST/GraphQL/gRPC, SPA/MPA/SSR, технологічні стеки
- 🚀 **Частина II**: сучасний синтаксис JavaScript, модулі, асинхронність, Event Loop
- 🔗 Частина II — це **мова реалізації** того, що описано в частині I: `fetch`-запит з частини I стає `async`-функцією з частини II

### Ключове про ES6+ та асинхронність

- 📚 **Читабельність**: деструктуризація, arrow functions, async/await роблять код декларативнішим
- 🛡️ **Надійність**: спеціалізовані класи помилок, обробка на кожному рівні
- 🔧 **Підтримуваність**: модульна архітектура з чіткими залежностями
- ⚡ **Продуктивність**: паралельне виконання, кешування, Web Workers, уникнення блокування Event Loop
