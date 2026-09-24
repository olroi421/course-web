# RESTful API дизайн

## План лекції

1. **Принципи REST** і модель зрілості
2. **Методи HTTP**: семантика, безпечність, ідемпотентність
3. **Коди стану** та їх вибір
4. **Структура адрес**: ресурси, ієрархія, фільтри, пагінація
5. **Кешування** та умовні запити
6. **Валідація й помилки**: Zod, Problem Details (RFC 9457)
7. **Документація**: OpenAPI 3.1/3.2
8. **Версіонування**: стратегії, Deprecation і Sunset

## Що таке REST?

**REST** (Representational State Transfer) — архітектурний стиль для розподілених систем (Рой Філдінг, 2000)

### **REST — це НЕ протокол, а набір обмежень!**

- Кожне обмеження дає системі певну властивість
- На практиці «REST API» = JSON поверх HTTP з ресурсними адресами

## 6 принципів REST

```mermaid
graph LR
    A[Принципи REST] --> B[Клієнт-сервер]
    A --> C[Без збереження стану]
    A --> D[Кешованість]
    A --> E[Уніфікований інтерфейс]
    A --> F[Багаторівнева система]
    A --> G[Код за запитом]

    B --> B1[Розділення відповідальності]
    C --> C1[Кожен запит самодостатній]
    D --> D1[Відповіді можна кешувати]
    E --> E1[Єдині правила взаємодії]
    F --> F1[Проміжні вузли прозорі]
    G --> G1[Опціонально]
```

## Принцип 1: Client-Server

```mermaid
graph LR
    Client[🖥️ Клієнт<br/>Інтерфейс<br/>Взаємодія] -->|HTTP-запити| Server[🖥️ Сервер<br/>Бізнес-логіка<br/>Дані]
    Server -->|HTTP-відповіді| Client
    Server --> Database[(💾 База даних)]
```

### **Переваги:**
- Незалежний розвиток клієнтської та серверної частин
- Один API — багато клієнтів (веб, мобільний, партнери)

## Принцип 2: Stateless

### **Кожен запит містить ВСЮ необхідну інформацію**

```http
GET /api/v1/users/me
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

- ✅ `/users/me` — **це stateless**: «хто я» визначається з токена в запиті
- ❌ Порушення — сервер пам'ятає попередні запити клієнта (серверна сесія)

### **Переваги:**
- Будь-який екземпляр сервера обробить будь-який запит
- Просте горизонтальне масштабування й відновлення після збоїв

## Концепція ресурсів

### **Все є ресурсом**

```http
https://api.example.com/users/123        # Конкретний користувач
https://api.example.com/users            # Колекція користувачів
https://api.example.com/users/123/posts  # Пости користувача
https://api.example.com/orders           # Замовлення
```

### **Ресурс ≠ файл або таблиця**
**Ресурс** = абстракція з одним або кількома представленнями (`Accept` / `Content-Type`)

## Модель зрілості Richardson

| Рівень | Назва | Ознака |
|--------|-------|--------|
| 0 | «Болото POX» | Одна адреса, один метод |
| 1 | Ресурси | Окремі адреси для ресурсів |
| 2 | Методи HTTP | Правильні методи й коди стану |
| 3 | Гіпермедіа | Посилання на дії у відповідях (HATEOAS) |

**Мета більшості API — рівень 2**

## REST серед інших підходів

| Підхід | Модель | Коли доречний |
|--------|--------|---------------|
| REST | Ресурси + методи HTTP | Публічні API, HTTP-кешування |
| GraphQL | Клієнт описує потрібні дані | Складні клієнти, агрегація джерел |
| gRPC | Виклик процедур, Protocol Buffers | Внутрішні сервіси, потоки |
| tRPC | Типізовані виклики функцій | Клієнт і сервер на TypeScript в одному репозиторії |

## HTTP методи: CRUD операції

```mermaid
graph LR
    subgraph "CRUD-операції"
        C[Create] --> POST[POST]
        R[Read] --> GET[GET]
        U[Update] --> PUT[PUT/PATCH]
        D[Delete] --> DELETE[DELETE]
    end

    subgraph "Властивості"
        POST --> P1[Не ідемпотентний]
        GET --> G1[Безпечний + ідемпотентний]
        PUT --> U1[Ідемпотентний]
        DELETE --> D1[Ідемпотентний]
    end
```

- **Безпечний** — не змінює стан сервера
- **Ідемпотентний** — повтор не змінює результат → запит можна повторити

## GET: Отримання даних

```http
GET /api/v1/users
200 OK
[
    {"id": 1, "name": "Іван", "email": "ivan@example.com"},
    {"id": 2, "name": "Марія", "email": "maria@example.com"}
]

GET /api/v1/users/123
200 OK
{"id": 123, "name": "Іван", "email": "ivan@example.com"}

GET /api/v1/users?status=active&limit=10
```

### **Властивості:** безпечний + ідемпотентний + кешований
- ❌ Без тіла запиту
- Складний пошук у тілі → метод **QUERY** (у розробці, є в OpenAPI 3.2) або `POST /search`

## POST: Створення ресурсів

```http
POST /api/v1/users
Content-Type: application/json

{ "name": "Олександр Коваленко", "email": "alex@example.com", "password": "securePass123" }

201 Created
Location: /api/v1/users/456
{ "id": 456, "name": "Олександр Коваленко", "email": "alex@example.com",
  "createdAt": "2026-01-15T14:30:00Z" }
```

### **Не ідемпотентний** → для безпечного повтору: заголовок `Idempotency-Key`
- Пароль **ніколи** не повертається у відповіді

## PUT vs PATCH

### **PUT — повне заміщення**
```http
PUT /api/v1/users/123
{ "name": "Нове ім'я", "email": "new@example.com", "status": "active" }
# Відсутні поля → значення за замовчуванням
```

### **PATCH — часткове оновлення**
```http
PATCH /api/v1/users/123
Content-Type: application/merge-patch+json
{ "email": "updated@example.com" }
# Змінюються ТІЛЬКИ вказані поля
```

- **JSON Merge Patch** (RFC 7396) — просто змінені поля
- **JSON Patch** (RFC 6902) — список операцій

## DELETE: Видалення ресурсів

```http
DELETE /api/v1/users/123

204 No Content
```

### **Типи видалення:**
- **Жорстке:** повне видалення з БД
- **М'яке:** позначка `deletedAt`, можна відновити
- **Архівування:** переміщення в архів

**Для клієнта результат однаковий — ресурс недоступний**

## HTTP статус коди

```mermaid
graph TD
    Status[Коди стану HTTP] --> S1[1xx Інформаційні]
    Status --> S2[2xx Успішні]
    Status --> S3[3xx Перенаправлення]
    Status --> S4[4xx Помилки клієнта]
    Status --> S5[5xx Помилки сервера]

    S2 --> S2A[200 OK<br/>201 Created<br/>202 Accepted<br/>204 No Content]
    S3 --> S3A[304 Not Modified]
    S4 --> S4A[400 Bad Request<br/>401 Unauthorized<br/>403 Forbidden<br/>404 Not Found<br/>409 Conflict<br/>412 Precondition Failed<br/>422 Unprocessable Content<br/>429 Too Many Requests]
    S5 --> S5A[500 Internal Server Error<br/>502 Bad Gateway<br/>503 Service Unavailable]
```

## Ключові статус коди

### **Помилки клієнта (4xx)**
- **400** — не відповідає схемі (типи, обов'язкові поля)
- **401** — немає або недійсні облікові дані
- **403** — користувача знаємо, але дію заборонено
- **404** — не знайдено (або приховуємо існування)
- **409** — конфлікт зі станом (email вже зайнятий)
- **422** — схема правильна, але порушено правила предметної області

### **Правило курсу**
400 — схема · 422 — бізнес-правила · 409 — конфлікт даних

❌ `200 OK` з полем `"error"` у тілі — ніколи!

## Структурування endpoints

### **Ресурсно-орієнтований підхід**

```http
# ✅ ПРАВИЛЬНО — іменники + методи HTTP
GET    /api/v1/users
POST   /api/v1/users
GET    /api/v1/users/123
PUT    /api/v1/users/123
DELETE /api/v1/users/123

# ❌ НЕПРАВИЛЬНО — дієслова в адресах
GET    /api/getUsers
POST   /api/createUser
```

- Множина для колекцій, kebab-case, малі літери
- Дія поза CRUD → ресурс: `POST /orders/456/cancellation`

## Ієрархічна структура

```http
GET    /api/v1/users/123/posts        # Пости користувача
POST   /api/v1/users/123/posts        # Створити пост
GET    /api/v1/posts/456/comments     # Коментарі до поста
```

### **⚠️ Не більше 2 рівнів вкладеності**
- Ресурс із власним ID → звертаємося напряму: `/posts/456`
- В Express 5: `Router({ mergeParams: true })`

## Фільтрація та пагінація

```http
# Фільтрація
GET /api/v1/products?category=electronics&priceMin=100&priceMax=500

# Сортування (лише за дозволеними полями!)
GET /api/v1/products?sort=-price,name

# Пагінація
GET /api/v1/users?limit=20&offset=40     # Зміщення
GET /api/v1/users?page=3&perPage=20      # Номер сторінки
GET /api/v1/users?cursor=eyJpZCI6MTIzfQ  # Курсор
```

| Зміщення / сторінка | Курсор |
|---------------------|--------|
| Перехід на довільну сторінку | Лише «далі» |
| Повільно на великій глибині | Однаково швидко |
| Дублі при вставках | Стійкий до змін |

**Завжди обмежуйте максимальний `limit`!**

## Кешування: Cache-Control

| Директива | Значення |
|-----------|----------|
| `max-age=60` | Свіжа 60 с |
| `public` / `private` | Спільні кеші / лише браузер |
| `no-cache` | Перевіряти перед використанням |
| `no-store` | Не зберігати взагалі |

```javascript
res.set('Cache-Control', 'public, max-age=600');   // довідник
res.set('Cache-Control', 'private, no-cache');     // профіль користувача
```

## Умовні запити: ETag

```mermaid
sequenceDiagram
    participant C as Клієнт
    participant S as Сервер

    C->>S: GET /api/v1/products/42
    S-->>C: 200 OK, ETag: "v7" + тіло
    Note over C: Зберігає відповідь і ETag

    C->>S: GET /api/v1/products/42<br/>If-None-Match: "v7"
    S-->>C: 304 Not Modified (без тіла)

    C->>S: GET /api/v1/products/42<br/>If-None-Match: "v7"
    Note over S: Товар змінено, тепер "v8"
    S-->>C: 200 OK, ETag: "v8" + нове тіло
```

- **`If-Match`** при PUT/PATCH → захист від одночасного редагування
- Версія змінилася → **412 Precondition Failed**

## Валідація даних

### **Багаторівнева система валідації**

```mermaid
graph TD
    Input[Вхідні дані] --> Schema[Валідація схеми]
    Schema -->|Типи, формат, обов'язковість| Business[Бізнес-валідація]
    Business -->|Правила предметної області| Unique[Перевірка стану даних]
    Unique -->|Унікальність, існування зв'язків| Success[✅ Обробка запиту]

    Schema -->|Помилка| Error400[400 Bad Request]
    Business -->|Помилка| Error422[422 Unprocessable Content]
    Unique -->|Помилка| Error409[409 Conflict]
```

**Унікальність гарантує лише індекс у БД, а не попередня перевірка!**

## Схеми валідації: Zod

```javascript
import { z } from 'zod';

export const createUserSchema = z.object({
    name: z.string().trim().min(2).max(50)
        .regex(/^[\p{L}\s'’-]+$/u),        // будь-які літери, зокрема кирилиця
    email: z.email(),
    password: z.string().min(8).max(64)
});

export function validate(schema, source = 'body') {
    return (req, res, next) => {
        const result = schema.safeParse(req[source]);
        if (!result.success) throw new ValidationError(result.error.issues);
        req.valid = { ...req.valid, [source]: result.data };
        next();
    };
}
```

⚠️ `[a-zA-Z]` відкидає «Олександр»

## Структура помилок: Problem Details

### **Стандарт RFC 9457** · `Content-Type: application/problem+json`

```javascript
{
    "type": "https://api.example.com/problems/validation-error",
    "title": "Дані не пройшли валідацію",
    "status": 400,
    "detail": "Два поля містять некоректні значення",
    "instance": "/api/v1/users",
    "requestId": "req_7f3a9c21",
    "errors": [
        { "field": "email", "code": "INVALID_EMAIL_FORMAT",
          "message": "Невірний формат email-адреси" }
    ]
}
```

- `title`/`message` — для людини, `code` — для програми
- Мова повідомлень — за `Accept-Language`

## Глобальна обробка помилок

```javascript
// Express 5: винятки з async-маршрутів потрапляють сюди автоматично
app.use((err, req, res, next) => {
    const isKnown = err instanceof HttpError;
    const status = isKnown ? err.status : 500;

    console.error({ requestId: req.id, url: req.originalUrl, status,
                    stack: status >= 500 ? err.stack : undefined });

    const problem = isKnown
        ? { type: err.type, title: err.title, status, detail: err.detail, ...err.extensions }
        : { type: 'about:blank', title: 'Внутрішня помилка сервера', status: 500 };

    res.status(status).type('application/problem+json')
       .json({ ...problem, instance: req.originalUrl, requestId: req.id });
});
```

**Деталі — у журнал, клієнту — лише безпечна інформація**

## API документація з OpenAPI

### **OpenAPI = стандарт машиночитного опису REST API**

- **3.1** — схеми = JSON Schema 2020-12 (`type: [string, "null"]` замість `nullable`)
- **3.2** (вересень 2025) — ієрархічні теги, потокові відповіді, метод QUERY; повністю сумісна з 3.1

```yaml
openapi: 3.1.1
info:
  title: Task Management API
  version: 1.0.0
servers:
  - url: https://api.example.com/v1
paths:
  /users:
    get:
      operationId: listUsers
      responses:
        '200':
          description: Список користувачів
        '400':
          $ref: '#/components/responses/BadRequest'
```

### **Переваги:** документація, клієнти, тести, імітаційні сервери — з одного файлу

## Підходи до документування

| «Спочатку контракт» | «Спочатку код» |
|---------------------|----------------|
| Пишемо OpenAPI → реалізуємо | Генеруємо OpenAPI з коду |
| Паралельна робота команд | Документація не розходиться з кодом |
| Тести перевіряють відповідність | Схеми Zod → OpenAPI |

**Переглядачі:** Swagger UI · Scalar · Redoc

## Swagger UI інтеграція

```javascript
import swaggerUi from 'swagger-ui-express';
import swaggerJsdoc from 'swagger-jsdoc';

const swaggerSpec = swaggerJsdoc({
    definition: {
        openapi: '3.1.1',
        info: { title: 'Task Management API', version: '1.0.0' }
    },
    apis: ['./src/routes/*.js'] // Файли з коментарями @openapi
});

app.use('/api-docs', swaggerUi.serve, swaggerUi.setup(swaggerSpec));
app.get('/openapi.json', (req, res) => res.json(swaggerSpec));
```

### **Результат:** інтерактивна документація на `/api-docs`

## Версіонування API

### **Навіщо?**
- ✅ Сумісні зміни (нове поле, нова адреса) — **без нової версії**
- ⚠️ Несумісні (видалення/перейменування поля, зміна типу) — **нова версія**

```mermaid
graph LR
    A[v1 API] --> B[Розробка v2]
    B --> C[Випуск v2]
    C --> D[Підтримка v1+v2]
    D --> E[Deprecation v1]
    E --> F[Sunset v1]
```

## Стратегії версіонування

### **1. Шлях URL** ⭐
```http
GET /api/v1/users
```

### **2. Заголовок**
```http
GET /api/users
API-Version: 2        # + Vary: API-Version для кешів!
```

### **3. Параметр запиту**
```http
GET /api/users?version=2
```

### **Рекомендація:** URL із мажорним номером

## Реалізація версіонування

```javascript
// Окремий маршрутизатор на кожну версію
import usersV1 from './routes/v1/users.js';
import usersV2 from './routes/v2/users.js';

const v1 = express.Router();
v1.use('/users', usersV1);

const v2 = express.Router();
v2.use('/users', usersV2);

app.use('/api/v1', v1);
app.use('/api/v2', v2);
```

- Спільна логіка — у сервісному шарі
- ⚠️ Express 5: `'/api/:version?/*'` більше не працює

## Життєвий цикл версій

```mermaid
graph TD
    A[Розробка v2] --> B[Бета-тестування]
    B --> C[🚀 Випуск v2]
    C --> D[Підтримка v1 + v2]
    D --> E[⚠️ Deprecation v1]
    E --> F[❌ Sunset v1]
    F --> G[Тільки v2]

    style C fill:#90EE90
    style E fill:#FFE4B5
    style F fill:#FFB6C1
```

### **Стандартні заголовки:**
```http
Deprecation: @1788220800                        # RFC 9745
Sunset: Mon, 01 Mar 2027 00:00:00 GMT           # RFC 8594
Link: <https://docs.example.com/migration/v1-to-v2>; rel="deprecation"
```

## Найкращі практики

### **🎯 Принципи успішного REST API**

1. **Консистентність** — однакові конвенції в усьому API
2. **Передбачуваність** — поведінка відповідає очікуванням
3. **Простота** — базові операції без зусиль
4. **Стандарти** замість власних винаходів

### **📋 Чекліст якості**
- ✅ Правильні методи й коди стану HTTP
- ✅ Ресурсно-орієнтовані адреси
- ✅ Валідація на сервері + унікальні індекси
- ✅ Помилки у форматі RFC 9457
- ✅ Cache-Control і ETag
- ✅ Версія в URL з першого дня
- ✅ Опис OpenAPI, що не розходиться з кодом

## Далі

- **Лекція 4:** підключаємо бази даних — PostgreSQL, MongoDB, ORM
- **Лекція 5:** захищаємо API — аутентифікація, авторизація, безпека
