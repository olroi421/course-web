# RESTful API дизайн


## Що таке REST?

**REST** (Representational State Transfer) — архітектурний стиль для розподілених систем


### **REST — це НЕ протокол, а набір принципів!**


## 6 принципів REST

```mermaid
graph TD
    A[REST Принципи] --> B[Client-Server]
    A --> C[Stateless]
    A --> D[Cacheable]
    A --> E[Uniform Interface]
    A --> F[Layered System]
    A --> G[Code on Demand]

    B --> B1[Розділення відповідальності]
    C --> C1[Кожен запит самодостатній]
    D --> D1[Відповіді можна кешувати]
    E --> E1[Уніфікований інтерфейс]
    F --> F1[Багаторівнева архітектура]
    G --> G1[Опціонально]
```


## Принцип 1: Client-Server

```mermaid
graph LR
    Client[🖥️ Клієнт<br/>UI/UX<br/>Взаємодія] --> |HTTP запити| Server[🖥️ Сервер<br/>Бізнес-логіка<br/>Дані]
    Server --> |HTTP відповіді| Client
    Server --> Database[(💾 База даних)]
```

### **Переваги:**
- Незалежний розвиток компонентів
- Легше масштабування
- Різні клієнти для одного API


## Принцип 2: Stateless

### **Кожен запит містить ВСЮ необхідну інформацію**

```javascript
// ✅ ПРАВИЛЬНО - Stateless
GET /api/users/123
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...

// ❌ НЕПРАВИЛЬНО - Stateful
GET /api/users/current  // Залежить від сесії
```

### **Переваги:**
- Легше масштабування
- Простіше відновлення після збоїв
- Можливість кешування

## Концепція ресурсів

### **Все є ресурсом**

```javascript
// Приклади ресурсів:
https://api.example.com/users/123        // Конкретний користувач
https://api.example.com/users            // Колекція користувачів
https://api.example.com/users/123/posts  // Пости користувача
https://api.example.com/orders           // Замовлення
```

### **Ресурс ≠ Файл або Таблиця**
**Ресурс** = концептуальна цілісність з різними представленнями

## HTTP методи: CRUD операції

```mermaid
graph LR
    subgraph "CRUD Операції"
        C[Create] --> POST[POST]
        R[Read] --> GET[GET]
        U[Update] --> PUT[PUT/PATCH]
        D[Delete] --> DELETE[DELETE]
    end

    subgraph "Властивості"
        POST --> P1[Не ідемпотентний]
        GET --> G1[Безпечний + Ідемпотентний]
        PUT --> U1[Ідемпотентний]
        DELETE --> D1[Ідемпотентний]
    end
```

## GET: Отримання даних

```javascript
// Отримання колекції
GET /api/users
Response: 200 OK
[
    {"id": 1, "name": "Іван", "email": "ivan@example.com"},
    {"id": 2, "name": "Марія", "email": "maria@example.com"}
]

// Отримання конкретного ресурсу
GET /api/users/123
Response: 200 OK
{"id": 123, "name": "Іван", "email": "ivan@example.com"}

// З параметрами фільтрації
GET /api/users?status=active&limit=10
```

### **Властивості:** Безпечний + Ідемпотентний + Кешований

## POST: Створення ресурсів

```javascript
// Створення користувача
POST /api/users
Content-Type: application/json

{
    "name": "Олександр Коваленко",
    "email": "alex@example.com",
    "password": "securePass123"
}

Response: 201 Created
Location: /api/users/456
{
    "id": 456,
    "name": "Олександр Коваленко",
    "email": "alex@example.com",
    "created_at": "2024-01-15T14:30:00Z"
}
```

### **Властивості:** НЕ ідемпотентний, може створити кілька ресурсів

## PUT vs PATCH

### **PUT - Повне заміщення**
```javascript
PUT /api/users/123
{
    "name": "Нове ім'я",
    "email": "new@example.com",
    "status": "active"
}
// Заміщує ВСІ поля ресурсу
```

### **PATCH - Часткове оновлення**
```javascript
PATCH /api/users/123
{
    "email": "updated@example.com"
}
// Оновлює ТІЛЬКИ вказані поля
```

## DELETE: Видалення ресурсів

```javascript
DELETE /api/users/123

// Варіанти відповідей:
Response: 204 No Content  // Без тіла відповіді

// АБО
Response: 200 OK
{
    "message": "Користувача видалено",
    "deleted_at": "2024-01-15T16:15:00Z"
}
```

### **Типи видалення:**
- **Жорстке:** повне видалення з БД
- **М'яке:** позначення як видалений
- **Архівування:** переміщення в архів

## HTTP статус коди

```mermaid
graph TD
    Status[HTTP Status Codes] --> S1[1xx Інформаційні]
    Status --> S2[2xx Успішні]
    Status --> S3[3xx Перенаправлення]
    Status --> S4[4xx Помилки клієнта]
    Status --> S5[5xx Помилки сервера]

    S2 --> S2A[200 OK<br/>201 Created<br/>204 No Content]
    S4 --> S4A[400 Bad Request<br/>401 Unauthorized<br/>404 Not Found<br/>409 Conflict]
    S5 --> S5A[500 Internal Error<br/>502 Bad Gateway<br/>503 Service Unavailable]
```

## Ключові статус коди

### **Успішні (2xx)**
- **200 OK** - стандартна успішна відповідь
- **201 Created** - ресурс створено (з Location header)
- **204 No Content** - успіх без тіла відповіді

### **Помилки клієнта (4xx)**
- **400 Bad Request** - невірні дані
- **401 Unauthorized** - потрібна аутентифікація
- **403 Forbidden** - доступ заборонено
- **404 Not Found** - ресурс не знайдено
- **409 Conflict** - конфлікт ресурсів

## Структурування endpoints

### **Ресурсо-орієнтований підхід**

```javascript
// ✅ ПРАВИЛЬНО - ресурси + HTTP методи
GET    /api/users              // Отримати користувачів
POST   /api/users              // Створити користувача
GET    /api/users/123          // Отримати користувача 123
PUT    /api/users/123          // Оновити користувача 123
DELETE /api/users/123          // Видалити користувача 123

// ❌ НЕПРАВИЛЬНО - дія-орієнтований підхід
GET    /api/getUsers
POST   /api/createUser
GET    /api/getUserById?id=123
```

## Ієрархічна структура

### **Природні зв'язки між ресурсами**

```javascript
// Користувачі та їх пости
GET    /api/users/123/posts           // Всі пости користувача
POST   /api/users/123/posts           // Створити пост
GET    /api/users/123/posts/456       // Конкретний пост
PUT    /api/users/123/posts/456       // Оновити пост
DELETE /api/users/123/posts/456       // Видалити пост

// Пости та коментарі
GET    /api/posts/456/comments        // Коментарі до поста
POST   /api/posts/456/comments        // Додати коментар
```

### **⚠️ Обмежуйте глибину до 2-3 рівнів**

## Фільтрація та пагінація

### **Query параметри для додаткової логіки**

```javascript
// Фільтрація
GET /api/products?category=electronics&price_min=100&price_max=500
GET /api/users?status=active&created_after=2024-01-01

// Сортування
GET /api/products?sort=price          // Зростання
GET /api/products?sort=-price         // Спадання
GET /api/products?sort=name,price     // Множинне

// Пагінація
GET /api/users?limit=20&offset=40     // Offset-based
GET /api/users?page=3&per_page=20     // Page-based
GET /api/users?cursor=xyz123          // Cursor-based
```

## Валідація даних

### **Багаторівнева система валідації**

```mermaid
graph TD
    Input[Вхідні дані] --> Schema[Валідація схеми]
    Schema --> |Типи, формат, обов'язковість| Business[Бізнес-валідація]
    Business --> |Унікальність, правила домену| Success[✅ Успіх]

    Schema --> |Помилка| Error400[400 Bad Request]
    Business --> |Помилка| Error409[409 Conflict]
```

### **Рівні валідації:**
1. **Схема:** типи даних, формат, довжина
2. **Бізнес-логіка:** унікальність, правила домену
3. **Безпека:** SQL injection, XSS захист

## Структура помилок

### **Стандартизований формат**

```javascript
{
    "error": {
        "type": "ValidationError",
        "message": "Дані не пройшли валідацію",
        "code": "VALIDATION_FAILED",
        "timestamp": "2024-01-15T16:30:00Z",
        "request_id": "req_123456789",
        "details": [
            {
                "field": "email",
                "message": "Невірний формат email",
                "code": "INVALID_EMAIL_FORMAT",
                "value": "invalid-email"
            }
        ]
    }
}
```

## Глобальна обробка помилок

```javascript
function globalErrorHandler(error, req, res, next) {
    // Логування
    console.error(`Error: ${error.message}`, {
        url: req.url,
        method: req.method,
        timestamp: new Date().toISOString()
    });

    // Визначення типу помилки
    let statusCode = 500;
    let errorResponse = {
        error: {
            type: 'InternalServerError',
            message: 'Внутрішня помилка сервера',
            code: 'INTERNAL_SERVER_ERROR'
        }
    };

    // Спеціалізована обробка
    if (error.name === 'ValidationError') {
        statusCode = 400;
        errorResponse.error.type = 'ValidationError';
    }

    res.status(statusCode).json(errorResponse);
}
```

## API документація з OpenAPI

### **OpenAPI = Стандарт для опису REST API**

```yaml
openapi: 3.0.3
info:
  title: Task Management API
  version: 1.0.0
  description: API для управління завданнями

servers:
  - url: https://api.example.com/v1
    description: Production server

paths:
  /users:
    get:
      summary: Отримати користувачів
      responses:
        '200':
          description: Список користувачів
```

### **Переваги:** Автогенерація документації, клієнтів, тестів

## Swagger UI інтеграція

```javascript
const swaggerUi = require('swagger-ui-express');
const swaggerJsdoc = require('swagger-jsdoc');

const swaggerOptions = {
    definition: {
        openapi: '3.0.3',
        info: {
            title: 'API Documentation',
            version: '1.0.0'
        }
    },
    apis: ['./routes/*.js'] // Файли з коментарями
};

const swaggerSpec = swaggerJsdoc(swaggerOptions);

// Підключення до Express
app.use('/api-docs', swaggerUi.serve, swaggerUi.setup(swaggerSpec));
```

### **Результат:** Інтерактивна документація на `/api-docs`

## Документування endpoints

```javascript
/**
 * @swagger
 * /api/users:
 *   get:
 *     summary: Отримати користувачів
 *     tags: [Users]
 *     parameters:
 *       - in: query
 *         name: limit
 *         schema:
 *           type: integer
 *         description: Кількість користувачів
 *     responses:
 *       200:
 *         description: Список користувачів
 *         content:
 *           application/json:
 *             schema:
 *               type: array
 *               items:
 *                 $ref: '#/components/schemas/User'
 */
app.get('/api/users', getUsersController);
```

## Версіонування API

### **Чому потрібне версіонування?**

- ✅ Зворотна сумісність
- ✅ Поступова міграція клієнтів
- ✅ Можливість експериментів
- ✅ Підтримка legacy систем

```mermaid
graph LR
    A[v1 API] --> B[v2 розробка]
    B --> C[v2 реліз]
    C --> D[Підтримка v1+v2]
    D --> E[Deprecation v1]
    E --> F[Видалення v1]
```

## Стратегії версіонування

### **1. URL Versioning** ⭐
```javascript
GET /api/v1/users
GET /api/v2/users
```

### **2. Header Versioning**
```javascript
GET /api/users
Headers: { "API-Version": "v2" }
```

### **3. Query Parameter**
```javascript
GET /api/users?version=v2
```

### **Рекомендація:** URL versioning для простоти та очевидності

## Реалізація версіонування

```javascript
// Middleware для версіонування
function versionMiddleware(req, res, next) {
    let version = req.params.version || 'v1';

    const supportedVersions = ['v1', 'v2', 'v3'];
    if (!supportedVersions.includes(version)) {
        return res.status(400).json({
            error: {
                message: `Версія ${version} не підтримується`,
                supported_versions: supportedVersions
            }
        });
    }

    req.apiVersion = version;
    next();
}

// Версіоновані маршрути
app.get('/api/:version/users', versionMiddleware, (req, res) => {
    switch(req.apiVersion) {
        case 'v1': return getUsersV1(req, res);
        case 'v2': return getUsersV2(req, res);
        case 'v3': return getUsersV3(req, res);
    }
});
```

## Життєвий цикл версій

```mermaid
graph TD
    A[Розробка v2] --> B[Beta тестування]
    B --> C[🚀 Реліз v2]
    C --> D[Підтримка v1 + v2]
    D --> E[⚠️ Deprecation warning v1]
    E --> F[❌ Видалення v1]
    F --> G[Тільки v2]

    style C fill:#90EE90
    style E fill:#FFE4B5
    style F fill:#FFB6C1
```

### **Deprecation Headers:**
```javascript
'X-API-Deprecation-Warning': 'v1 буде видалено 2024-06-01'
'X-API-Sunset-Date': '2024-06-01'
'X-API-Migration-Guide': 'https://docs.example.com/migration'
```

## Найкращі практики

### **🎯 Принципи успішного REST API**

1. **Консистентність** - однакові конвенції у всьому API
2. **Передбачуваність** - поведінка відповідає очікуванням
3. **Простота** - легко використовувати для базових операцій
4. **Документованість** - якісна та актуальна документація

### **📋 Чек-лист якості**
- ✅ Правильні HTTP методи та статус коди
- ✅ Ресурсо-орієнтована структура URL
- ✅ Стандартизована обробка помилок
- ✅ Версіонування з самого початку
- ✅ Автоматична документація
