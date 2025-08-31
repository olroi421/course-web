# Вступ до сучасної веброзробки

## План лекції

1. Архітектура сучасних вебдодатків
2. Client-Server взаємодія та HTTP
3. REST API vs GraphQL vs gRPC
4. SPA vs MPA vs SSR
5. Огляд технологічних стеків

## **1. Архітектура сучасних вебдодатків**

> **Вебдодаток** — це програмне забезпечення, яке працює на вебсервері та доступне користувачам через веббраузер без необхідності встановлення на локальному комп'ютері.


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

    2010-ні : ⚡ Веб-додатки
             : AJAX, SPA
             : REST API

    2020-ні : 🚀 Сучасні додатки
             : Мікросервіси, PWA
             : Real-time, AI
```

## Сучасна багатошарова архітектура

```mermaid
graph TB
    subgraph "🌐 Client Layer"
        A[Web Browser]
        B[Mobile App]
        C[Desktop App]
    end

    subgraph "🎨 Presentation Layer"
        D[CDN]
        E[Frontend App]
    end

    subgraph "⚙️ Application Layer"
        F[Load Balancer]
        G[API Gateway]
        H[Microservices]
    end

    subgraph "💾 Data Layer"
        I[Database]
        J[Cache]
        K[File Storage]
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
| 📦 Один великий додаток | 🧩 Множина малих сервісів |
| 🛠️ Одна технологія | 🎨 Різні технології |
| 📈 Важко масштабувати | ⚡ Легко масштабувати |
| 🐛 Збій = весь додаток | 🛡️ Ізольовані збої |

### Приклад: E-commerce платформа

```mermaid
graph LR
    A[🎨 Frontend] --> B[🚪 API Gateway]
    B --> C[👤 User Service]
    B --> D[📦 Product Service]
    B --> E[🛒 Cart Service]
    B --> F[💳 Payment Service]
    B --> G[📧 Notification Service]
```

## **2. Client-Server взаємодія та HTTP**

## HTTP протокол: основи

### Модель Request-Response:

```mermaid
sequenceDiagram
    participant C as 🌐 Client
    participant S as 🖥️ Server

    C->>S: HTTP Request
    Note over C,S: GET /api/users

    S-->>S: 🔧 Process Request

    S->>C: HTTP Response
    Note over C,S: 200 OK + JSON

    C-->>C: 🎨 Update UI
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
graph TB
    subgraph "🔄 Traditional Forms"
        A1[User Action] --> B1[Page Reload]
        B1 --> C1[Server Response]
        C1 --> D1[Full Page Render]
    end

    subgraph "⚡ AJAX"
        A2[User Action] --> B2[Background Request]
        B2 --> C2[Server Response]
        C2 --> D2[Update Part of Page]
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
    A[📱 Client] --> B[🎯 Single Endpoint]
    B --> C[🔍 GraphQL Query]
    C --> D[⚙️ Resolvers]
    D --> E[💾 Multiple Sources]
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

- 🔥 **Високопродуктивний** - binary протокол
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
    A[Вибір API типу] --> B{Тип проекту?}

    B -->|Публічний API| C[🌐 REST]
    B -->|Мобільний додаток| D[🎯 GraphQL]
    B -->|Мікросервіси| E[🚀 gRPC]
    B -->|Прототип| C
```

## **4. SPA vs MPA vs SSR**

## Single Page Application (SPA)

### SPA - один HTML файл:

```mermaid
sequenceDiagram
    participant U as 👤 User
    participant B as 🌐 Browser
    participant S as 🖥️ Server

    U->>B: Перший візит
    B->>S: GET /
    S->>B: HTML + JS Bundle
    B-->>B: ⚡ App ініціалізація

    U->>B: Навігація
    B-->>B: 🔄 Client routing
    B-->>B: 🎨 Update UI
```

### ✅ Переваги SPA:

- ⚡ Миттєва навігація
- 🎨 Багата інтерактивність
- 😊 Відмінний UX
- 📱 Native-like досвід

### ❌ Недоліки SPA:

- 🐌 Повільне початкове завантаження
- 🔍 Проблеми з SEO
- 📱 JavaScript обов'язковий

## Multi-Page Application (MPA)

### MPA - традиційний підхід:

```mermaid
sequenceDiagram
    participant U as 👤 User
    participant B as 🌐 Browser
    participant S as 🖥️ Server

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
    participant U as 👤 User
    participant B as 🌐 Browser
    participant S as 🖥️ SSR Server

    U->>B: Перший візит
    B->>S: GET /products
    S-->>S: 🔧 Render React
    S->>B: Pre-rendered HTML + JS
    B-->>B: 💧 Hydration

    U->>B: Навігація
    B-->>B: ⚡ Client navigation
```

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
        A[🅰️ Angular<br/>Frontend]
        B[🚀 Express.js<br/>Web Framework]
        C[🟢 Node.js<br/>Runtime]
        D[🍃 MongoDB<br/>Database]
    end

    A --> B
    B --> C
    C --> D
```

### Особливості MEAN:

- ✅ Повний JavaScript стек
- ✅ TypeScript підтримка з коробки
- ✅ Структурований підхід Angular
- ❌ Менша гнучкість

## MERN Stack

```mermaid
graph TB
    subgraph "⚛️ MERN Stack"
        A[⚛️ React<br/>Frontend]
        B[🚀 Express.js<br/>Web Framework]
        C[🟢 Node.js<br/>Runtime]
        D[🍃 MongoDB<br/>Database]
    end

    A --> B
    B --> C
    C --> D
```

### Особливості MERN:

- ✅ Велика екосистема React
- ✅ Висока гнучкість
- ✅ Швидка розробка
- ❌ Потребує більше налаштувань

## Next.js Full-Stack

```mermaid
graph TB
    subgraph "⚡ Next.js Ecosystem"
        A[⚡ Next.js<br/>React + SSR/SSG]
        B[🔧 API Routes<br/>Backend]
        C[💾 Database<br/>PostgreSQL/MongoDB]
        D[☁️ Deployment<br/>Vercel/Netlify]
    end

    A --> B
    B --> C
    A --> D
```

### Переваги Next.js:
- ⚡ SSR/SSG з коробки
- 🎯 File-based routing
- 🔧 API routes вбудовані
- 📊 Відмінна оптимізація

## Порівняння стеків

| Стек | Складність | SEO | Екосистема | Продуктивність |
|------|------------|-----|------------|----------------|
| **MEAN** | 🔴 Висока | ✅ Відмінно | 📊 Середня | 📈 Добра |
| **MERN** | 🟡 Середня | 🛠️ Потребує SSR | 🌟 Велика | 🚀 Висока |
| **Next.js** | 🟡 Середня | ✅ Нативно | 📈 Зростає | 🚀 Відмінна |


## Підсумки

### 🎯 Ключові принципи сучасної веброзробки:

1. **📐 Архітектура**: Розуміння багатошарової архітектури
2. **🌐 API**: Вміння проектувати REST/GraphQL API
3. **⚡ Performance**: Оптимізація швидкості завантаження
4. **🔍 SEO**: Забезпечення пошукової оптимізації
5. **🧪 Testing**: Покриття коду тестами
6. **🚀 DevOps**: CI/CD та автоматизація
