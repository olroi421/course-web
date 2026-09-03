# Лекція 1. Вступ до сучасної веброзробки та JavaScript ES6+

## Частина I. Вступ до сучасної веброзробки

Ця лекція складається з двох взаємопов'язаних частин. У **частині I** ми розберемося, з яких шарів складається сучасний вебдодаток, як клієнт і сервер спілкуються між собою та які технологічні стеки використовують для побудови таких систем. У **частині II** ми перейдемо на рівень мови програмування, якою ці системи здебільшого написані на боці клієнта (а часто — і на боці сервера): сучасного JavaScript (ES6+) та підходів до асинхронного програмування на ньому. Обидві частини описують одну й ту саму реальність із різних масштабів: частина I — це "з висоти пташиного польоту", частина II — це "на рівні коду".

## Архітектура сучасних вебдодатків

### Визначення вебдодатку

**Вебдодаток** — це програмне забезпечення, яке працює на вебсервері та доступне користувачам через веббраузер без необхідності встановлення на локальному комп'ютері. На відміну від звичайної програми, яку потрібно завантажити й встановити, вебдодаток запускається за посиланням: клієнтський пристрій виконує лише частину роботи (відображення інтерфейсу, обробку дій користувача), а основна логіка та дані зазвичай лишаються на боці сервера.

Розуміння того, як саме розподілена ця робота між клієнтом і сервером, — ключ до всієї подальшої дисципліни. Кожна тема курсу, від проєктування бази даних до вибору фреймворку, так чи інакше відповідає на одне й те саме питання: **що виконувати на клієнті, а що — на сервері, і як організувати обмін даними між ними**.

### Еволюція вебдодатків

Щоб зрозуміти, чому сучасні вебдодатки влаштовані саме так, а не інакше, корисно простежити, як змінювалися вимоги до вебу за останні три десятиліття. Кожен новий етап виникав не випадково, а як відповідь на обмеження попереднього.

#### 1. Статичні вебсайти (1990-ті)

Характеристики:

- HTML-файли зберігаються на сервері в готовому вигляді;
- відсутність інтерактивності — сторінка не реагує на дії користувача, окрім переходів за посиланнями;
- контент однаковий для всіх користувачів незалежно від того, хто саме його переглядає.

Приклад структури:
```
website/
├── index.html
├── about.html
├── contact.html
├── css/
│   └── style.css
└── images/
    └── logo.png
```

Сервер у цій моделі виконував мінімальну роль — просто віддавав файли "як є". Обмеження такого підходу очевидні: щоб змінити хоча б одне слово на сайті, потрібно було вручну редагувати HTML-файл і завантажувати його на сервер.

#### 2. Динамічні вебсайти (2000-ні)

Характеристики:

- серверні мови програмування (PHP, ASP, JSP) генерують HTML "на льоту";
- бази даних використовуються для зберігання контенту, який більше не "зашитий" у файли;
- контент став персоналізованим — різні користувачі бачать різні дані (наприклад, свій профіль чи історію замовлень).

Приклад технологічного стеку:
```
LAMP Stack:
├── Linux (операційна система)
├── Apache (вебсервер)
├── MySQL (база даних)
└── PHP (серверна мова програмування)
```

Цей етап розв'язав проблему статичності, але сторінка як і раніше повністю перезавантажувалася під час кожної взаємодії з сервером — навіть якщо змінювався лише один невеликий фрагмент.

#### 3. Вебдодатки (2010-ті)

Характеристики:

- багата інтерактивність — інтерфейс реагує на дії користувача без затримок;
- AJAX дає змогу надсилати запити до сервера асинхронно, без перезавантаження всієї сторінки;
- RESTful API стає стандартним способом обміну даними між клієнтом і сервером;
- клієнтська частина (фронтенд) і серверна частина (бекенд) остаточно розділяються на окремі проєкти з власними технологічними стеками.

Саме на цьому етапі з'явилося чітке розмежування ролей: серверна частина відповідає за бізнес-логіку, зберігання й обробку даних та безпеку, а клієнтська — за відображення інтерфейсу та взаємодію з користувачем. Це розмежування є основою архітектури, яку ми розглядатимемо далі.

#### 4. Сучасні хмарні та AI-орієнтовані вебдодатки (2020-ні)

Останній етап еволюції — той, у якому ми працюємо зараз, — характеризується кількома паралельними тенденціями:

- перехід до мікросервісної архітектури та хмарних платформ (AWS, Google Cloud, Azure) як стандарту для великих проєктів;
- поширення Progressive Web Apps (PWA) — вебдодатків із можливостями, близькими до нативних мобільних застосунків;
- взаємодія в реальному часі (чати, спільне редагування документів, біржові дані) як звична функціональність, а не виняток;
- поява AI-асистованих інструментів розробки (агентних асистентів на кшталт Claude Code, GitHub Copilot) та впровадження штучного інтелекту безпосередньо у функціональність вебдодатків (розумний пошук, рекомендації, чат-боти);
- поступова відмова від Node.js як єдиного варіанта серверного JavaScript-середовища на користь альтернатив — Deno та Bun (детальніше — у розділі про технологічні стеки).

Ці тенденції не скасовують попередні підходи, а доповнюють їх: статичні сторінки, серверний рендеринг і SPA продовжують співіснувати, кожен — для свого класу задач.

### Сучасна архітектура вебдодатків

```mermaid
graph TB
    subgraph "Клієнтський рівень"
        A[🌐 Веббраузер]
        B[📱 Мобільний застосунок]
        C[💻 Десктопний застосунок]
    end

    subgraph "Рівень представлення"
        D[⚡ CDN]
        E[🎨 Клієнтський застосунок]
    end

    subgraph "Рівень застосунку"
        F[🔄 Балансувальник навантаження]
        G[⚙️ API-шлюз]
        H[🛡️ Сервіс автентифікації]
        I[📊 Сервіси бізнес-логіки]
    end

    subgraph "Рівень даних"
        J[💾 Основна база даних]
        K[⚡ Шар кешування]
        L[📈 Аналітична БД]
        M[📁 Файлове сховище]
    end

    A --> D
    B --> D
    C --> D
    D --> E
    E --> F
    F --> G
    G --> H
    G --> I
    I --> J
    I --> K
    I --> L
    I --> M
```

Ця схема — не абстракція заради абстракції: кожен блок відповідає на конкретне архітектурне рішення, яке доводиться приймати під час проєктування реального проєкту. Розглянемо кожен рівень детальніше.

### Детальний аналіз архітектурних компонентів

#### 1. **Клієнтський рівень**

Включає всі пристрої та застосунки, через які користувачі взаємодіють із вебдодатком:

- **веббраузери**: Chrome, Firefox, Safari, Edge;
- **мобільні застосунки**: нативні iOS/Android застосунки, які звертаються до того самого API, що й браузер;
- **десктопні застосунки**: побудовані на Electron або подібних технологіях (Slack, Discord, Visual Studio Code).

Важливо розуміти: незалежно від типу клієнта (браузер, мобільний застосунок, десктопна програма), спосіб взаємодії із сервером принципово однаковий — через HTTP-запити до того самого API. Це і є практична цінність розділення клієнтської та серверної частин: один бекенд може обслуговувати водночас вебсайт, мобільний застосунок і десктопну програму.

#### 2. **Рівень представлення**

Відповідає за доставку та відображення інтерфейсу користувача:

- **CDN (Content Delivery Network, мережа доставки контенту)**: глобальна мережа серверів для швидкої доставки статичного контенту (зображень, стилів, скриптів) географічно близько до користувача;
- **клієнтський застосунок (frontend application)**: React, Vue.js, Angular або інші застосунки, що виконуються в браузері.

Приклад архітектури CDN:
```mermaid
graph LR
    A[👤 Користувач в Україні] --> B[🌍 Київський CDN-сервер]
    C[👤 Користувач в США] --> D[🌍 Нью-Йоркський CDN-сервер]
    E[👤 Користувач в Японії] --> F[🌍 Токійський CDN-сервер]

    B --> G[☁️ Основний сервер]
    D --> G
    F --> G
```

Ідея CDN проста: чим фізично ближче сервер до користувача, тим менша затримка (latency) при завантаженні. Замість того щоб кожен запит ішов до одного центрального сервера в іншій частині світу, CDN зберігає копії статичних файлів на десятках серверів по всьому світу.

#### 3. **Рівень застосунку**

Містить бізнес-логіку та обробляє запити, що надходять від клієнтів.

**Балансувальник навантаження (load balancer)**:

- розподіляє вхідні запити між кількома серверами;
- забезпечує високу доступність — якщо один сервер виходить з ладу, запити автоматично перенаправляються на інші;
- приклади інструментів: NGINX, HAProxy, AWS Elastic Load Balancing.

```mermaid
graph LR
    A[🌐 Інтернет] --> B[⚖️ Балансувальник навантаження]
    B --> C[🖥️ Сервер 1]
    B --> D[🖥️ Сервер 2]
    B --> E[🖥️ Сервер 3]

    F[📊 Перевірка стану] --> C
    F --> D
    F --> E
```

**API-шлюз (API gateway)**:

- єдина точка входу для всіх запитів до API;
- автентифікація та авторизація запитів ще до того, як вони потраплять у бізнес-логіку;
- моніторинг та аналітика трафіку;
- обмеження кількості запитів (rate limiting) для захисту від перевантаження та зловживань.

Приклад конфігурації API-шлюзу:

```yaml
# Приклад конфігурації Kong API Gateway
services:
  - name: user-service
    url: http://user-service:3000
    routes:
      - name: users
        paths: ["/api/users"]
        methods: ["GET", "POST", "PUT", "DELETE"]
    plugins:
      - name: rate-limiting
        config:
          minute: 100
          hour: 1000
```

#### 4. **Рівень даних**

Зберігає та управляє всіма даними застосунку:

- **основна база даних**: головне сховище даних (PostgreSQL, MySQL, MongoDB);
- **шар кешування**: забезпечує швидкий доступ до часто запитуваних даних (Redis, Memcached);
- **аналітична база даних**: спеціалізовані сховища для аналітики великих обсягів даних (ClickHouse, BigQuery);
- **файлове сховище**: зберігання файлів — зображень, документів, відео (AWS S3, Google Cloud Storage).

### Мікросервісна архітектура

Сучасні великі вебдодатки часто відходять від монолітної архітектури (коли весь застосунок — один великий проєкт) на користь мікросервісної: система розбивається на набір невеликих, незалежних сервісів, кожен з яких відповідає за окрему бізнес-функцію.

```mermaid
graph TB
    subgraph "Клієнтська частина"
        A[🎨 React SPA]
    end

    subgraph "API-шлюз"
        B[🚪 Kong / Zuul]
    end

    subgraph "Мікросервіси"
        C[👤 Сервіс користувачів]
        D[📦 Сервіс товарів]
        E[🛒 Сервіс кошика]
        F[💳 Сервіс платежів]
        G[📧 Сервіс сповіщень]
    end

    subgraph "Рівень даних"
        H[(👤 БД користувачів)]
        I[(📦 БД товарів)]
        J[(🛒 Кеш кошика)]
        K[💳 API платіжної системи]
        L[📧 Черга електронної пошти]
    end

    A --> B
    B --> C
    B --> D
    B --> E
    B --> F
    B --> G

    C --> H
    D --> I
    E --> J
    F --> K
    G --> L
```

Переваги мікросервісної архітектури:

- **масштабованість**: кожен сервіс можна масштабувати незалежно від інших (наприклад, під час розпродажу масштабується лише сервіс товарів і кошика, а не весь застосунок);
- **технологічна гнучкість**: різні сервіси можуть використовувати різні мови програмування та бази даних — там, де це найдоречніше;
- **ізольованість**: збій одного сервісу (наприклад, сервісу сповіщень) не обов'язково призводить до відмови всієї системи;
- **швидкість розробки**: різні команди можуть працювати над різними сервісами незалежно одна від одної.

Недоліки:

- **складність**: потребує досвіду в DevOps та розподілених системах, яких не вимагає монолітна архітектура;
- **мережеві затримки**: комунікація між сервісами відбувається через мережу, а не через виклик функції в одному процесі;
- **складність тестування**: інтеграційне тестування взаємодії десятків сервісів помітно складніше за тестування одного монолітного застосунку.

Важливо: мікросервісна архітектура — не універсально "правильніший" вибір. Для навчального проєкту чи стартапу на ранній стадії монолітна архітектура зазвичай доцільніша: вона простіша в розробці, розгортанні та підтримці, а до мікросервісів варто переходити тоді, коли моноліт справді починає заважати масштабуванню команди чи навантаженню.

## Client-Server взаємодія та HTTP протокол

### Основи Client-Server моделі

**Client-Server модель** — це архітектурна модель, де клієнт ініціює запити до сервера, який обробляє ці запити та повертає відповіді. Клієнт ніколи не звертається до бази даних чи файлової системи сервера напряму — весь обмін відбувається через чітко визначений протокол, яким у вебі є HTTP.

```mermaid
sequenceDiagram
    participant C as 🌐 Клієнт (браузер)
    participant S as 🖥️ Сервер

    C->>S: HTTP-запит
    Note over C,S: GET /api/users

    S-->>S: Обробка запиту
    Note over S: Запит до БД,<br/>бізнес-логіка

    S->>C: HTTP-відповідь
    Note over C,S: 200 OK + JSON-дані

    C-->>C: Відтворення інтерфейсу
    Note over C: Оновлення DOM,<br/>показ даних
```

### HTTP протокол детально

#### Структура HTTP запиту

```
GET /api/users/123 HTTP/1.1
Host: api.example.com
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
Accept: application/json
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64)
Content-Type: application/json

{
  "include": ["profile", "preferences"]
}
```

Компоненти HTTP запиту:

1. **Рядок запиту (Request Line)**: метод + URL + версія протоколу;
2. **Заголовки (Headers)**: додаткова інформація про запит (тип авторизації, очікуваний формат відповіді тощо);
3. **Тіло запиту (Body)**: дані запиту (для методів POST, PUT, PATCH).

#### Структура HTTP відповіді

```
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 156
Cache-Control: max-age=3600
Set-Cookie: session_id=abc123; Path=/; HttpOnly

{
  "id": 123,
  "name": "Іван Петренко",
  "email": "ivan@example.com",
  "profile": {
    "avatar": "https://cdn.example.com/avatars/123.jpg"
  }
}
```

#### HTTP методи та їх застосування

| Метод | Призначення | Ідемпотентний | Безпечний | Приклад |
|-------|-------------|---------------|-----------|---------|
| **GET** | Отримання даних | ✅ | ✅ | `GET /api/users` |
| **POST** | Створення ресурсу | ❌ | ❌ | `POST /api/users` |
| **PUT** | Повне оновлення | ✅ | ❌ | `PUT /api/users/123` |
| **PATCH** | Часткове оновлення | ❌ | ❌ | `PATCH /api/users/123` |
| **DELETE** | Видалення ресурсу | ✅ | ❌ | `DELETE /api/users/123` |
| **OPTIONS** | Інформація про допустимі методи | ✅ | ✅ | `OPTIONS /api/users` |

Пояснення термінів:

- **Ідемпотентний**: повторний виклик з тими самими параметрами дає той самий результат, що й одноразовий (наприклад, `DELETE /api/users/123` двічі поспіль так само видаляє ресурс — після першого разу він просто вже відсутній);
- **Безпечний**: не змінює стан сервера (лише читає дані).

#### HTTP статус коди

```mermaid
graph LR
    A[📊 Статус-коди HTTP] --> B[1xx Інформаційні]
    A --> C[2xx Успіх]
    A --> D[3xx Переспрямування]
    A --> E[4xx Помилка клієнта]
    A --> F[5xx Помилка сервера]

    C --> C1[200 OK]
    C --> C2[201 Created]
    C --> C3[204 No Content]

    D --> D1[301 Moved Permanently]
    D --> D2[302 Found]
    D --> D3[304 Not Modified]

    E --> E1[400 Bad Request]
    E --> E2[401 Unauthorized]
    E --> E3[403 Forbidden]
    E --> E4[404 Not Found]

    F --> F1[500 Internal Server Error]
    F --> F2[502 Bad Gateway]
    F --> F3[503 Service Unavailable]
```

Детальний розгляд важливих кодів:

**2xx Успіх:**

- **200 OK**: успішний запит;
- **201 Created**: ресурс створено;
- **204 No Content**: успішно, але немає контенту для повернення.

**4xx Помилка клієнта:**

- **400 Bad Request**: некоректний запит;
- **401 Unauthorized**: потрібна автентифікація;
- **403 Forbidden**: доступ заборонено;
- **404 Not Found**: ресурс не знайдено;
- **422 Unprocessable Entity**: помилки валідації даних.

**5xx Помилка сервера:**

- **500 Internal Server Error**: помилка на сервері;
- **502 Bad Gateway**: помилка проксі-сервера;
- **503 Service Unavailable**: сервер тимчасово недоступний.

### Асинхронна взаємодія з сервером

#### AJAX (Asynchronous JavaScript and XML)

**AJAX** дозволяє надсилати запити до сервера без перезавантаження сторінки. Сучасний спосіб реалізації AJAX-запитів у браузері — Fetch API:

```javascript
// Приклад використання Fetch API
async function fetchUsers() {
    try {
        const response = await fetch('/api/users', {
            method: 'GET',
            headers: {
                'Authorization': 'Bearer ' + token,
                'Content-Type': 'application/json'
            }
        });

        if (!response.ok) {
            throw new Error(`HTTP error! status: ${response.status}`);
        }

        const users = await response.json();
        displayUsers(users);
    } catch (error) {
        console.error('Помилка завантаження користувачів:', error);
        showErrorMessage('Не вдалося завантажити користувачів');
    }
}
```

#### WebSockets для реального часу

Для застосунків, що потребують взаємодії в реальному часі (чати, ігри, біржові дані, спільне редагування документів), звичайного HTTP-запиту недостатньо: клієнту потрібно отримувати дані від сервера без окремого запиту на кожне оновлення. Для цього використовується протокол WebSocket, який встановлює постійне двостороннє з'єднання:

```javascript
// Клієнтська частина
const socket = new WebSocket('ws://localhost:8080');

socket.onopen = function(event) {
    console.log('З\'єднання встановлено');
    socket.send(JSON.stringify({
        type: 'join_room',
        room: 'general'
    }));
};

socket.onmessage = function(event) {
    const message = JSON.parse(event.data);
    displayMessage(message);
};

socket.onclose = function(event) {
    console.log('З\'єднання закрито');
};
```

### Кешування в HTTP

Кешування покращує продуктивність вебдодатків, дозволяючи браузеру або проміжному серверу повертати вже отриману відповідь замість повторного звернення до сервера:

```mermaid
sequenceDiagram
    participant B as 🌐 Браузер
    participant C as 🗄️ Кеш
    participant S as 🖥️ Сервер

    B->>C: Запит /api/data
    alt Кеш містить відповідь
        C->>B: Відповідь із кешу
    else Кеш не містить відповіді
        C->>S: Переслати запит
        S->>C: Відповідь + заголовки кешування
        C->>B: Відповідь
        C-->>C: Зберегти в кеші
    end
```

**HTTP заголовки для кешування:**

```http
# Кешування на 1 годину
Cache-Control: max-age=3600

# Кешування з валідацією
Cache-Control: no-cache
ETag: "abc123"
Last-Modified: Wed, 21 Oct 2023 07:28:00 GMT

# Заборона кешування
Cache-Control: no-store, no-cache, must-revalidate
```

## REST API vs GraphQL vs gRPC

Коли клієнт і сервер спілкуються через HTTP, лишається питання: у якому саме форматі й за якими правилами вони обмінюються даними? Історично першим і досі найпоширенішим підходом став REST, але для окремих класів задач з'явилися й альтернативи — GraphQL та gRPC. Розгляньмо всі три та зрозуміймо, коли який підхід доречний.

### REST API (Representational State Transfer)

**REST** — це архітектурний стиль для розробки вебсервісів, заснований на принципах HTTP.

#### Принципи REST:

1. **Stateless (без стану)**: кожен запит містить всю необхідну інформацію, сервер не зберігає стан клієнта між запитами;
2. **Resource-based (ресурсно-орієнтований)**: URL-адреси представляють ресурси (наприклад, `/users`), а не дії над ними;
3. **HTTP методи**: використання стандартних методів HTTP (GET, POST, PUT, DELETE) для позначення дій над ресурсом;
4. **Representation (представлення)**: один і той самий ресурс може передаватись у різних форматах (найчастіше — JSON, іноді XML).

#### Приклад REST API:

```javascript
// Отримання списку користувачів
GET /api/users
Response: [
    { "id": 1, "name": "Іван", "email": "ivan@example.com" },
    { "id": 2, "name": "Марія", "email": "maria@example.com" }
]

// Отримання конкретного користувача
GET /api/users/1
Response: { "id": 1, "name": "Іван", "email": "ivan@example.com" }

// Створення нового користувача
POST /api/users
Body: { "name": "Петро", "email": "petro@example.com" }
Response: { "id": 3, "name": "Петро", "email": "petro@example.com" }

// Оновлення користувача
PUT /api/users/1
Body: { "name": "Іван Оновлений", "email": "ivan.new@example.com" }

// Видалення користувача
DELETE /api/users/1
Response: 204 No Content
```

#### Структура RESTful URL:

```
https://api.example.com/v1/users          # Колекція користувачів
https://api.example.com/v1/users/123      # Конкретний користувач
https://api.example.com/v1/users/123/posts # Пости користувача
https://api.example.com/v1/posts/456/comments # Коментарі до посту
```

Переваги REST:

- ✅ простота та зрозумілість;
- ✅ використання стандартних HTTP методів;
- ✅ кешування на рівні HTTP "з коробки";
- ✅ широка підтримка інструментів та бібліотек у будь-якій мові програмування.

Недоліки REST:

- ❌ over-fetching (надлишкове отримання даних): клієнт отримує більше полів, ніж йому потрібно;
- ❌ under-fetching (недостатнє отримання даних): для складного екрана доводиться робити кілька запитів;
- ❌ множинні запити для отримання пов'язаних даних;
- ❌ версіонування API (v1, v2 тощо) може ускладнювати підтримку.

### GraphQL

**GraphQL** — це мова запитів та середовище виконання для API, розроблена компанією Meta (Facebook) як відповідь на проблеми over- та under-fetching у REST.

#### Основні концепції GraphQL:

```graphql
# Схема GraphQL
type User {
  id: ID!
  name: String!
  email: String!
  posts: [Post!]!
  profile: Profile
}

type Post {
  id: ID!
  title: String!
  content: String!
  author: User!
  comments: [Comment!]!
  createdAt: DateTime!
}

type Query {
  users: [User!]!
  user(id: ID!): User
  posts(limit: Int, offset: Int): [Post!]!
}

type Mutation {
  createUser(input: CreateUserInput!): User!
  updateUser(id: ID!, input: UpdateUserInput!): User!
  deleteUser(id: ID!): Boolean!
}
```

#### Приклади запитів GraphQL:

```graphql
# Отримання користувачів з постами
query GetUsersWithPosts {
  users {
    id
    name
    email
    posts {
      id
      title
      createdAt
    }
  }
}

# Отримання конкретного користувача з профілем
query GetUserProfile($userId: ID!) {
  user(id: $userId) {
    name
    email
    profile {
      avatar
      bio
      location
    }
  }
}

# Створення нового користувача
mutation CreateNewUser($input: CreateUserInput!) {
  createUser(input: $input) {
    id
    name
    email
    createdAt
  }
}
```

#### Архітектура GraphQL:

```mermaid
graph LR
    A[📱 Клієнт] --> B[🔍 GraphQL-запит]
    B --> C[⚙️ GraphQL-сервер]
    C --> D[🔧 Резолвери]
    D --> E[(💾 База даних)]
    D --> F[🌐 Зовнішнє API]
    D --> G[📁 Файлова система]

    C --> H[📊 Схема]
    H --> I[📝 Типи]
    H --> J[❓ Запити]
    H --> K[✏️ Мутації]
    H --> L[📡 Підписки]
```

Переваги GraphQL:

- ✅ точні дані: клієнт отримує тільки ті поля, які запитав;
- ✅ один endpoint для всіх запитів замість десятків REST-маршрутів;
- ✅ сильна типізація та інтроспекція схеми;
- ✅ підтримка взаємодії в реальному часі через subscriptions;
- ✅ зручні інструменти розробника (GraphiQL, Apollo DevTools).

Недоліки GraphQL:

- ❌ складність кешування порівняно з REST, де кешування прив'язане до URL;
- ❌ крива навчання вища, ніж у REST;
- ❌ потенційні проблеми з продуктивністю (проблема N+1 запитів);
- ❌ складність авторизації на рівні окремих полів схеми.

### gRPC (Google Remote Procedure Call)

**gRPC** — це високопродуктивний RPC-фреймворк з відкритим кодом, розроблений Google.

#### Особливості gRPC:

- **Protocol Buffers (protobuf)**: бінарний формат серіалізації даних, значно компактніший за JSON;
- **HTTP/2**: мультиплексування запитів в одному з'єднанні, стискання заголовків;
- **Багатомовність**: офіційна підтримка понад 10 мов програмування;
- **Чотири типи комунікації**: Unary, Server Streaming, Client Streaming, Bidirectional Streaming.

#### Визначення сервісу в gRPC:

```protobuf
// user_service.proto
syntax = "proto3";

package user;

service UserService {
  rpc GetUser(GetUserRequest) returns (User);
  rpc GetUsers(GetUsersRequest) returns (stream User);
  rpc CreateUser(CreateUserRequest) returns (User);
  rpc UpdateUser(UpdateUserRequest) returns (User);
  rpc DeleteUser(DeleteUserRequest) returns (Empty);
}

message User {
  int32 id = 1;
  string name = 2;
  string email = 3;
  int64 created_at = 4;
}

message GetUserRequest {
  int32 id = 1;
}

message CreateUserRequest {
  string name = 1;
  string email = 2;
}
```

#### Типи викликів gRPC:

```mermaid
graph TB
    subgraph "Типи комунікації gRPC"
        A[🔄 Unary RPC<br/>Запит → Відповідь]
        B[📤 Server Streaming<br/>Запит → Потік відповідей]
        C[📥 Client Streaming<br/>Потік запитів → Відповідь]
        D[🔄 Bidirectional Streaming<br/>Потік запитів ↔ Потік відповідей]
    end
```

**Приклад клієнтського коду:**

```javascript
// Node.js gRPC client
const grpc = require('@grpc/grpc-js');
const protoLoader = require('@grpc/proto-loader');

const packageDefinition = protoLoader.loadSync('user_service.proto');
const userService = grpc.loadPackageDefinition(packageDefinition).user;

const client = new userService.UserService(
  'localhost:50051',
  grpc.credentials.createInsecure()
);

// Unary call
client.getUser({ id: 1 }, (error, response) => {
  if (!error) {
    console.log('User:', response);
  } else {
    console.error('Error:', error);
  }
});

// Server streaming
const call = client.getUsers({});
call.on('data', (user) => {
  console.log('Received user:', user);
});
call.on('end', () => {
  console.log('Stream ended');
});
```

### Порівняння REST, GraphQL та gRPC

| Критерій | REST | GraphQL | gRPC |
|----------|------|---------|------|
| **Протокол** | HTTP/1.1, HTTP/2 | HTTP/1.1, HTTP/2 | HTTP/2 |
| **Формат даних** | JSON, XML | JSON | Protocol Buffers (бінарний) |
| **Продуктивність** | Середня | Середня-Висока | Висока |
| **Кешування** | Відмінне | Складне | Обмежене |
| **Інструменти** | Широка підтримка | Зростаюча екосистема | Специфічні інструменти |
| **Крива навчання** | Низька | Середня | Висока |
| **Типізація** | Слабка | Сильна | Сильна |
| **Streaming** | Обмежено | Subscriptions | Нативно |

### Сучасна альтернатива: tRPC

Окремо варто згадати **tRPC** — підхід, що набув популярності останніми роками в проєктах, де і клієнт, і сервер написані на TypeScript (наприклад, у зв'язці з Next.js). tRPC дозволяє викликати серверні функції з клієнта так, ніби це звичайні функції в тому самому проєкті — TypeScript автоматично перевіряє типи запиту й відповіді на етапі компіляції, без окремої схеми (на відміну від GraphQL) і без ручного опису ендпоінтів (на відміну від REST). Це не заміна REST чи GraphQL для публічних API, а зручний інструмент саме для full-stack TypeScript-проєктів у межах одного репозиторію.

### Вибір підходу для різних сценаріїв

```mermaid
flowchart TD
    A[Вибір API стилю] --> B{Тип застосунку?}

    B -->|Публічний API| C[REST]
    B -->|Мобільний застосунок| D[GraphQL]
    B -->|Мікросервіси| E[gRPC]
    B -->|Веб-застосунок| F{Складність даних?}

    F -->|Проста| C
    F -->|Складна| D

    C --> G[✅ Простота<br/>✅ Кешування<br/>✅ Стандарти]
    D --> H[✅ Гнучкість<br/>✅ Ефективність<br/>✅ Типізація]
    E --> I[✅ Продуктивність<br/>✅ Streaming<br/>✅ Типізація]
```

## SPA vs MPA vs SSR

Наступне важливе архітектурне рішення стосується того, **де саме формується HTML-сторінка**, яку зрештою бачить користувач: повністю на сервері, повністю в браузері чи комбіновано. Від цього вибору залежать швидкість завантаження, пошукова оптимізація (SEO) та складність розробки.

### Single Page Application (SPA)

**SPA** — це вебдодаток, який завантажується один раз і динамічно оновлює контент без перезавантаження сторінки.

#### Архітектура SPA:

```mermaid
sequenceDiagram
    participant U as 👤 Користувач
    participant B as 🌐 Браузер
    participant S as 🖥️ Сервер
    participant A as ⚛️ API

    U->>B: Перший візит
    B->>S: GET /
    S->>B: HTML + JS-бандл (React/Vue/Angular)
    B-->>B: Ініціалізація застосунку

    U->>B: Навігація (/profile)
    B-->>B: Маршрутизація на клієнті
    B->>A: GET /api/user
    A->>B: JSON-дані
    B-->>B: Відтворення компонента
```

#### Приклад структури SPA (React):

```
spa-app/
├── public/
│   └── index.html                 # Єдина HTML-сторінка
├── src/
│   ├── components/
│   │   ├── Header.jsx
│   │   ├── UserProfile.jsx
│   │   └── ProductList.jsx
│   ├── pages/
│   │   ├── HomePage.jsx
│   │   ├── ProfilePage.jsx
│   │   └── ProductsPage.jsx
│   ├── services/
│   │   └── api.js                 # Виклики до API
│   ├── App.jsx                    # Головний компонент
│   └── index.js                   # Точка входу
└── package.json
```

Переваги SPA:

- ✅ швидка навігація після першого завантаження;
- ✅ багата інтерактивність;
- ✅ гарний користувацький досвід;
- ✅ менше навантаження на сервер після початкового завантаження, оскільки далі передаються лише дані, а не готовий HTML.

Недоліки SPA:

- ❌ повільне початкове завантаження (браузер має завантажити й виконати весь JS-бандл, перш ніж щось показати);
- ❌ проблеми з пошуковою оптимізацією без додаткових заходів;
- ❌ складність кешування;
- ❌ JavaScript обов'язково потрібен для роботи сторінки.

### Multi-Page Application (MPA)

**MPA** — це традиційний вебдодаток, де кожна сторінка завантажується окремо з сервера.

#### Архітектура MPA:

```mermaid
sequenceDiagram
    participant U as 👤 Користувач
    participant B as 🌐 Браузер
    participant S as 🖥️ Сервер

    U->>B: Візит домашньої сторінки
    B->>S: GET /
    S->>B: HTML-сторінка (index.html)

    U->>B: Клік на "Про нас"
    B->>S: GET /about
    S->>B: HTML-сторінка (about.html)

    U->>B: Клік на "Продукти"
    B->>S: GET /products
    S->>B: HTML-сторінка з даними продуктів
```

#### Приклад структури MPA:

```
mpa-app/
├── pages/
│   ├── index.html                 # Домашня сторінка
│   ├── about.html                 # Про нас
│   ├── products.html              # Продукти
│   └── contact.html               # Контакти
├── assets/
│   ├── css/
│   │   └── styles.css
│   ├── js/
│   │   └── main.js
│   └── images/
├── server/
│   ├── routes/
│   │   ├── home.js
│   │   ├── products.js
│   │   └── contact.js
│   └── app.js
└── package.json
```

Переваги MPA:

- ✅ відмінна пошукова оптимізація;
- ✅ швидше початкове завантаження сторінки;
- ✅ проста архітектура;
- ✅ працює без JavaScript.

Недоліки MPA:

- ❌ повільна навігація (повне перезавантаження сторінки при кожному переході);
- ❌ дублювання коду між сторінками;
- ❌ більше навантаження на сервер, оскільки кожен перехід — це новий повний запит;
- ❌ менш інтерактивний користувацький досвід.

### Server-Side Rendering (SSR)

**SSR (відтворення на сервері)** — це підхід, при якому HTML генерується на сервері для кожного запиту, що поєднує переваги SPA та MPA: швидке перше відображення сторінки (як у MPA) і швидку подальшу навігацію без перезавантажень (як у SPA).

#### Архітектура SSR:

```mermaid
sequenceDiagram
    participant U as 👤 Користувач
    participant B as 🌐 Браузер
    participant S as 🖥️ SSR-сервер
    participant A as ⚛️ API

    U->>B: Перший візит
    B->>S: GET /products
    S->>A: Запит даних
    A->>S: Дані про товари
    S-->>S: Відтворення React/Vue-компонента
    S->>B: Готовий HTML + JS
    B-->>B: Гідратація (активація JS поверх готового HTML)

    U->>B: Навігація (/profile)
    Note over B: Маршрутизація на клієнті
    B->>A: GET /api/user
    A->>B: JSON-дані
    B-->>B: Відтворення на клієнті
```

#### Приклад SSR з Next.js:

```javascript
// pages/products/[id].js - Next.js SSR (Pages Router, legacy-підхід)
import { GetServerSideProps } from 'next';

export default function ProductPage({ product }) {
  return (
    <div>
      <h1>{product.name}</h1>
      <p>{product.description}</p>
      <p>Ціна: {product.price} грн</p>
    </div>
  );
}

// Ця функція виконується на сервері для кожного запиту
export const getServerSideProps = async (context) => {
  const { id } = context.params;

  try {
    const res = await fetch(`http://api.example.com/products/${id}`);
    const product = await res.json();

    return {
      props: {
        product
      }
    };
  } catch (error) {
    return {
      notFound: true
    };
  }
};
```

> Наведений приклад використовує Pages Router — підхід, який Next.js зберігає для сумісності зі старими проєктами. У новостворюваних проєктах SSR реалізується через App Router (детальніше — у розділі про технологічні стеки).

#### Static Site Generation (SSG)

**SSG (статична генерація сторінок)** — це підвид SSR, де сторінки генеруються заздалегідь, під час збірки проєкту, а не під час кожного запиту:

```javascript
// Next.js SSG приклад
export const getStaticProps = async () => {
  const res = await fetch('http://api.example.com/products');
  const products = await res.json();

  return {
    props: {
      products
    },
    revalidate: 3600 // Регенерація кожну годину
  };
};

export const getStaticPaths = async () => {
  const res = await fetch('http://api.example.com/products');
  const products = await res.json();

  const paths = products.map((product) => ({
    params: { id: product.id.toString() }
  }));

  return {
    paths,
    fallback: 'blocking' // Генерувати нові сторінки на вимогу
  };
};
```

#### React Server Components — розвиток ідеї SSR

Останніми роками екосистема React пішла далі за традиційний SSR у бік **React Server Components (RSC)** — компонентів, які виконуються виключно на сервері, ніколи не потрапляють у клієнтський JS-бандл і можуть напряму звертатися до бази даних чи файлової системи, без окремого API-шару. Це не замінює клієнтські компоненти повністю: типова сторінка поєднує серверні компоненти (для статичного чи рідко змінюваного вмісту) і клієнтські (для інтерактивних елементів — форм, кнопок, анімацій). App Router у Next.js побудований саме навколо цієї моделі.

### Порівняльна таблиця підходів

| Критерій | SPA | MPA | SSR | SSG |
|----------|-----|-----|-----|-----|
| **Початкове завантаження** | Повільне | Швидке | Швидке | Найшвидше |
| **Навігація** | Миттєва | Повільна | Миттєва | Миттєва |
| **SEO** | Погане | Відмінне | Відмінне | Відмінне |
| **Інтерактивність** | Висока | Низька | Висока | Висока |
| **Складність розробки** | Середня | Низька | Висока | Висока |
| **Навантаження на сервер** | Низьке | Високе | Високе | Низьке |
| **Кешування** | Складне | Просте | Середнє | Відмінне |

### Вибір підходу для різних проєктів

```mermaid
flowchart TD
    A[Вибір архітектури] --> B{SEO критично важливе?}

    B -->|Ні| C[SPA]
    B -->|Так| D{Контент динамічний?}

    C --> G[✅ Дашборд<br/>✅ Адмін-панель<br/>✅ CRM-система]

    D -->|Так| E[SSR]
    D -->|Ні| F[SSG]

    E --> H[✅ E-commerce<br/>✅ Новинні сайти<br/>✅ Блоги]
    F --> I[✅ Документація<br/>✅ Лендінг-сторінки<br/>✅ Портфоліо]
```

## Огляд технологічних стеків

Розглянуті вище архітектурні рішення (мікросервіси чи моноліт, REST чи GraphQL, SPA чи SSR) на практиці втілюються через конкретні набори технологій — так звані технологічні стеки. Розгляньмо три найпоширеніші для JavaScript-екосистеми.

### MEAN Stack

**MEAN** — це повний JavaScript-стек для веброзробки:

- **M**ongoDB — NoSQL база даних;
- **E**xpress.js — вебфреймворк для Node.js;
- **A**ngular — фреймворк для клієнтської частини;
- **N**ode.js — серверне середовище виконання JavaScript.

```mermaid
graph TB
    subgraph "Архітектура MEAN Stack"
        A[🅰️ Angular — клієнтська частина] --> B[🚀 Express.js API]
        B --> C[🟢 Node.js Runtime]
        C --> D[🍃 MongoDB]
    end

    subgraph "Потік даних"
        E[👤 Дія користувача] --> A
        A --> F[📡 HTTP-запити]
        F --> B
        B --> G[📊 Запити до БД]
        G --> D
    end
```

#### Приклад MEAN застосунку:

Серверна частина (Express.js + Node.js):

```javascript
// server.js
const express = require('express');
const mongoose = require('mongoose');
const cors = require('cors');

const app = express();

// Підключення до MongoDB
mongoose.connect('mongodb://localhost:27017/meanapp');

// Middleware
app.use(cors());
app.use(express.json());

// Модель користувача
const userSchema = new mongoose.Schema({
  name: String,
  email: String,
  createdAt: { type: Date, default: Date.now }
});

const User = mongoose.model('User', userSchema);

// API маршрути
app.get('/api/users', async (req, res) => {
  try {
    const users = await User.find();
    res.json(users);
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});

app.post('/api/users', async (req, res) => {
  try {
    const user = new User(req.body);
    await user.save();
    res.status(201).json(user);
  } catch (error) {
    res.status(400).json({ error: error.message });
  }
});

app.listen(3000, () => {
  console.log('Сервер запущено на порті 3000');
});
```

Клієнтська частина (Angular):

```typescript
// user.service.ts
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';

export interface User {
  _id?: string;
  name: string;
  email: string;
  createdAt?: Date;
}

@Injectable({
  providedIn: 'root'
})
export class UserService {
  private apiUrl = 'http://localhost:3000/api';

  constructor(private http: HttpClient) {}

  getUsers(): Observable<User[]> {
    return this.http.get<User[]>(`${this.apiUrl}/users`);
  }

  createUser(user: User): Observable<User> {
    return this.http.post<User>(`${this.apiUrl}/users`, user);
  }
}

// user-list.component.ts
import { Component, OnInit } from '@angular/core';
import { UserService, User } from './user.service';

@Component({
  selector: 'app-user-list',
  template: `
    <div>
      <h2>Користувачі</h2>
      <ul>
        <li *ngFor="let user of users">
          {{ user.name }} - {{ user.email }}
        </li>
      </ul>
    </div>
  `
})
export class UserListComponent implements OnInit {
  users: User[] = [];

  constructor(private userService: UserService) {}

  ngOnInit() {
    this.userService.getUsers().subscribe(
      users => this.users = users,
      error => console.error('Помилка завантаження користувачів:', error)
    );
  }
}
```

Angular лишається затребуваним у великих корпоративних проєктах завдяки вбудованій структурованості та нативній підтримці TypeScript, однак його частка серед нових проєктів поступово зменшується на користь React та легших альтернатив (Vue.js, Svelte) — це варто враховувати під час вибору стеку для власного проєкту.

### MERN Stack

**MERN** замінює Angular на React:

- **M**ongoDB — NoSQL база даних;
- **E**xpress.js — вебфреймворк для Node.js;
- **R**eact — бібліотека для клієнтської частини;
- **N**ode.js — серверне середовище виконання JavaScript.

```mermaid
graph TB
    subgraph "Архітектура MERN Stack"
        A[⚛️ React — клієнтська частина] --> B[🚀 Express.js API]
        B --> C[🟢 Node.js Runtime]
        C --> D[🍃 MongoDB]
    end

    subgraph "Інструменти розробки"
        E[📦 Vite] --> A
        F[🔧 Nodemon] --> C
        G[🧪 Vitest / Testing Library] --> A
        H[📊 MongoDB Compass] --> D
    end
```

#### Приклад компонента MERN:

```jsx
// UserList.jsx
import React, { useState, useEffect } from 'react';
import axios from 'axios';

const UserList = () => {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchUsers = async () => {
      try {
        const response = await axios.get('/api/users');
        setUsers(response.data);
      } catch (err) {
        setError('Не вдалося завантажити користувачів');
      } finally {
        setLoading(false);
      }
    };

    fetchUsers();
  }, []);

  if (loading) return <div>Завантаження...</div>;
  if (error) return <div>Помилка: {error}</div>;

  return (
    <div className="user-list">
      <h2>Список користувачів</h2>
      {users.map(user => (
        <div key={user._id} className="user-card">
          <h3>{user.name}</h3>
          <p>{user.email}</p>
          <small>{new Date(user.createdAt).toLocaleDateString()}</small>
        </div>
      ))}
    </div>
  );
};

export default UserList;
```

> Зверніть увагу: у сучасних React-проєктах для збирання застосунку майже повсюдно використовують Vite замість Webpack — він помітно швидший на етапі розробки завдяки нативним ES-модулям у браузері.

### Next.js Full-Stack

**Next.js** — це React-фреймворк із вбудованими можливостями full-stack розробки: маршрутизація, серверне відтворення сторінок, API-маршрути та оптимізація — все "з коробки", без потреби окремо збирати ці частини з різних бібліотек.

Станом на 2026 рік чинна мажорна версія — **Next.js 16**, яка принесла кілька суттєвих змін порівняно з попередніми версіями:

- **App Router став основним і рекомендованим підходом** (директорія `app/`), а Pages Router (`pages/`) підтримується для сумісності зі старими проєктами, але вважається застарілим шляхом для нових розробок;
- **Turbopack — стандартний бандлер** для `next dev` і `next build` (раніше потрібно було вмикати його окремим прапорцем);
- App Router працює на React 19.2 з підтримкою View Transitions (анімація переходів між станами інтерфейсу) та стабілізованим React Compiler, який автоматично оптимізує повторні відтворення компонентів без ручного застосування `useMemo`/`useCallback`.

```mermaid
graph TB
    subgraph "Архітектура Next.js"
        A[🌐 Браузер] --> B[⚡ Next.js застосунок]
        B --> C[📄 App Router]
        B --> D[🔧 Route Handlers / API]
        B --> E[🎨 Компоненти]
        D --> F[💾 База даних]
        B --> G[📦 Статичні файли]
    end

    subgraph "Стратегії відтворення"
        H[SSG — статична генерація]
        I[SSR — відтворення на сервері]
        J[ISR — покрокова регенерація]
        K[CSR — відтворення на клієнті]
    end
```

#### Приклад застосунку Next.js:

Файлова структура:

```
nextjs-app/
├── app/                          # App Router (основний підхід, Next.js 13+)
│   ├── layout.js                 # Кореневий layout
│   ├── page.js                   # Домашня сторінка
│   ├── users/
│   │   ├── page.js              # Сторінка /users
│   │   └── [id]/
│   │       └── page.js          # Сторінка /users/[id]
│   └── api/
│       └── users/
│           └── route.js         # API-маршрут
├── pages/                         # Pages Router (legacy, для сумісності)
│   └── api/
│       └── legacy-endpoint.js
├── components/
│   ├── UserCard.jsx
│   └── Navigation.jsx
├── lib/
│   └── database.js              # Утиліти для роботи з БД
└── public/
    └── images/
```

API-маршрут:

```javascript
// app/api/users/route.js (App Router)
import { NextResponse } from 'next/server';
import { connectToDatabase } from '@/lib/database';

export async function GET() {
  try {
    const db = await connectToDatabase();
    const users = await db.collection('users').find({}).toArray();

    return NextResponse.json(users);
  } catch (error) {
    return NextResponse.json(
      { error: 'Не вдалося завантажити користувачів' },
      { status: 500 }
    );
  }
}

export async function POST(request) {
  try {
    const userData = await request.json();
    const db = await connectToDatabase();

    const result = await db.collection('users').insertOne({
      ...userData,
      createdAt: new Date()
    });

    return NextResponse.json(
      { id: result.insertedId, ...userData },
      { status: 201 }
    );
  } catch (error) {
    return NextResponse.json(
      { error: 'Не вдалося створити користувача' },
      { status: 400 }
    );
  }
}
```

Серверний компонент з відтворенням на сервері:

```jsx
// app/users/[id]/page.js
async function getUser(id) {
  const res = await fetch(`${process.env.API_URL}/api/users/${id}`, {
    next: { revalidate: 3600 } // Кешування на годину
  });

  if (!res.ok) {
    throw new Error('Не вдалося завантажити користувача');
  }

  return res.json();
}

export default async function UserPage({ params }) {
  const user = await getUser(params.id);

  return (
    <div className="user-profile">
      <h1>{user.name}</h1>
      <p>{user.email}</p>
      <p>Реєстрація: {new Date(user.createdAt).toLocaleDateString()}</p>
    </div>
  );
}

// Генерація статичних параметрів
export async function generateStaticParams() {
  const res = await fetch(`${process.env.API_URL}/api/users`);
  const users = await res.json();

  return users.map((user) => ({
    id: user.id.toString()
  }));
}
```

Наведений вище компонент `UserPage` — приклад серверного компонента (Server Component): він виконується виключно на сервері, звертається до API напряму під час відтворення сторінки, і його код ніколи не потрапляє в JS-бандл, який завантажує браузер.

### Порівняння технологічних стеків

| Критерій | MEAN | MERN | Next.js |
|----------|------|------|---------|
| **Клієнтська частина** | Angular | React | React |
| **Крива навчання** | Висока | Середня | Середня-Висока |
| **TypeScript** | Нативно | Додатково | Нативно |
| **SEO** | Потребує Angular Universal | Потребує окремого SSR | Вбудовано |
| **Розмір застосунку** | Великий | Середній | Оптимізований |
| **Екосистема** | Менша | Велика | Зростаюча, нині — провідна для React |
| **Продуктивність** | Середня | Висока | Висока |
| **Гнучкість** | Середня | Висока | Середня |

### Альтернативні середовища виконання: Bun та Deno

Node.js понад десятиліття залишається стандартом серверного JavaScript, і саме тому в цій лекції він розглядається як базове середовище. Однак варто знати про дві активні альтернативи, що з'явилися останніми роками:

- **Deno** — середовище виконання від творця Node.js, з вбудованою підтримкою TypeScript без окремого налаштування, суворішою моделлю безпеки (доступ до файлової системи чи мережі потрібно дозволяти явно) та сумісністю з частиною npm-пакетів;
- **Bun** — середовище виконання, орієнтоване передусім на швидкість: власний JavaScript-двигун, вбудований пакетний менеджер і бандлер, що заміняють npm/yarn і Webpack/Vite в одному інструменті.

Для навчальних і більшості комерційних проєктів Node.js LTS (станом на 2026 рік — лінія Node.js 24, з переходом на Node.js 26 восени 2026 року) лишається найбезпечнішим і найбільш документованим вибором: усі приклади в цьому курсі орієнтовані саме на нього. Bun і Deno варто розглядати як предмет подальшого самостійного вивчення, коли базові концепції серверного JavaScript вже засвоєні.

## Підсумки частини I

Сучасна веброзробка — це складна екосистема технологій, інструментів та підходів, і жодна її частина не існує ізольовано: вибір архітектури впливає на вибір API, вибір API — на вибір способу відтворення сторінок, а всі разом — на вибір технологічного стеку.

### Ключові аспекти:

1. **Архітектура**: розуміння client-server взаємодії та різних архітектурних підходів (монолітна, мікросервісна) — основа якісної розробки.
2. **Проєктування API**: вибір між REST, GraphQL, gRPC та tRPC залежить від специфіки проєкту й вимог до продуктивності, а не від "модності" технології.
3. **Спосіб відтворення сторінок**: SPA, MPA, SSR та SSG мають власні переваги й недоліки для різних типів застосунків; React Server Components — сучасний розвиток ідеї SSR.
4. **Технологічні стеки**: MEAN, MERN та Next.js надають готові рішення для швидкого старту проєктів, а Bun і Deno розширюють вибір середовищ виконання.
5. **Інструменти**: сучасні IDE, системи збірки (Vite, Turbopack) та інструменти на основі штучного інтелекту суттєво підвищують продуктивність розробки.

### Тенденції розвитку:

- **Мікрофронтенди**: декомпозиція клієнтської частини великих застосунків на незалежні частини, які можна розробляти й розгортати окремо;
- **Serverless**: функції як сервіс (Function as a Service) для серверної логіки без потреби керувати сервером самостійно;
- **Edge Computing**: виконання коду на серверах, максимально наближених географічно до користувача, для мінімізації затримок;
- **WebAssembly**: виконання високопродуктивного коду (написаного, наприклад, на Rust чи C++) безпосередньо в браузері;
- **Progressive Web Apps**: вебдодатки з можливостями, близькими до нативних мобільних застосунків;
- **AI-асистована розробка**: агентні інструменти (Claude Code, GitHub Copilot та подібні) дедалі глибше інтегруються в процес написання, рефакторингу й тестування коду, а штучний інтелект усе частіше стає й частиною самого продукту — від розумного пошуку до чат-ботів у вебдодатку.

Усе розглянуте вище описує архітектуру "згори" — те, з яких частин складається вебдодаток і як вони обмінюються даними. Але кожна з цих частин — клієнтський застосунок, серверна логіка, обробники API-запитів — врешті-решт написана конкретною мовою програмування. Для клієнтської частини (а дедалі частіше й для серверної) такою мовою є JavaScript. У частині II ми розглянемо, які можливості дає сучасний JavaScript (стандарт ES6+) для того, щоб писати код, який реалізує архітектуру, описану вище: як структурувати дані, як організувати код у модулі та як писати асинхронні операції — а саме вони й лежать в основі всієї Client-Server взаємодії, розглянутої в цій частині.

## Частина II. JavaScript ES6+ та асинхронне програмування
## Вступ до сучасного JavaScript

У частині I ми розглядали вебдодаток як систему взаємодіючих компонентів: клієнт, сервер, API, спосіб відтворення сторінок. Тепер спустимося на рівень нижче — до мови, якою ця взаємодія реалізується в коді. Практично всі приклади в частині I (звернення через `fetch`, обробка HTTP-відповідей, побудова інтерфейсу) написані на JavaScript, і саме тому розуміння сучасних можливостей цієї мови — необхідна умова для того, щоб не просто розуміти архітектуру, а вміти її реалізувати.

### Історична еволюція JavaScript

JavaScript народився в 1995 році як проста мова сценаріїв для браузера Netscape Navigator. Спочатку мова мала обмежені можливості: додавання інтерактивності до статичних HTML сторінок, валідація форм, маніпуляції з DOM елементами. Проте за майже три десятиліття JavaScript пройшов неймовірний шлях еволюції, перетворившись з допоміжної мови в одну з найпопулярніших та наймогутніших мов програмування сучасності.

Ключовими етапами цього розвитку стали:

- **1995 рік**: створення JavaScript Бренданом Айхом за 10 днів у Netscape;
- **1997 рік**: стандартизація JavaScript як ECMAScript (ES1);
- **1999 рік**: ECMAScript 3 — додання регулярних виразів, обробки винятків;
- **2009 рік**: ECMAScript 5 — strict mode, нові методи масивів, JSON підтримка;
- **2015 рік**: ECMAScript 2015 (ES6) — революційне оновлення з класами, модулями, стрілочними функціями;
- **2016-теперішній час**: щорічні оновлення ECMAScript з постійним додаванням нових функцій.

### Що таке ECMAScript та його значення

**ECMAScript** — це стандарт, який визначає синтаксис, типи даних, об'єкти та методи, на яких базується JavaScript. Важливо розуміти, що JavaScript — це реалізація стандарту ECMAScript, хоча на практиці ці терміни часто використовуються як синоніми.

**ES6+ (ECMAScript 2015+)** позначає всі версії, починаючи з ES2015 і до сучасних версій. Це позначення підкреслює кардинальні зміни, які відбулися в мові, роблячи її більш потужною, виразною та зручною для розробки складних застосунків.

Основні причини, чому ES6+ став настільки важливим:

1. **Синтаксичні покращення**: введення більш зрозумілого та лаконічного синтаксису;
2. **Модульність**: нативна підтримка модульної системи;
3. **Асинхронність**: кращі інструменти для роботи з асинхронним кодом;
4. **Об'єктно-орієнтованість**: класи та наслідування в більш знайомому вигляді;
5. **Функціональне програмування**: покращена підтримка функціональних підходів.

### Проблеми, які вирішив ES6+

До появи ES6 розробники JavaScript стикалися з рядом фундаментальних проблем:

**Проблема області видимості**: використання `var` призводило до неочікуваної поведінки через hoisting (підняття оголошень змінних і функцій на початок області видимості до моменту виконання) та функціональну область видимості замість блочної.

**Відсутність модульності**: не було стандартного способу організації коду в модулі, що призводило до конфліктів глобальних змінних.

**Callback Hell** (буквально «пекло зворотних викликів»): глибока вкладеність зворотних викликів (callback-функцій) робила асинхронний код важкочитабельним та схильним до помилок.

**Багатослівність**: багато повсякденних операцій вимагали значної кількості коду.

ES6+ систематично вирішив ці проблеми, надавши розробникам сучасні інструменти для створення чистого, ефективного та підтримуваного коду.

### ES6+ у продовженні: що з'явилося після 2015 року

Назва «ES6+» невипадково має знак «+»: після революційного ES2015 стандарт ECMAScript оновлюється щороку, і кожне оновлення додає невеликі, але практично корисні можливості. До моменту, коли ці лекції готувалися вперше, частина з них уже встигла стати буденною частиною щоденного коду, тому варто розглянути їх окремо — не як щось "нове й екзотичне", а як природне продовження ідей ES6.

Найпомітніші доповнення останніх років:

- **Optional chaining `?.`** (ES2020) — безпечне звернення до вкладених властивостей об'єкта без ланцюжка перевірок на `null`/`undefined`: `user?.address?.city` замість `user && user.address && user.address.city`;
- **Nullish coalescing `??`** (ES2020) — повертає праву частину лише тоді, коли ліва дорівнює `null` або `undefined` (на відміну від `||`, який спрацює і на `0` чи порожньому рядку): `const port = config.port ?? 3000;`;
- **Top-level `await`** (ES2022) — дозволяє використовувати `await` безпосередньо в тілі модуля, без обгортання в `async`-функцію, що спрощує ініціалізацію модулів, які залежать від асинхронних ресурсів (наприклад, підключення до бази даних);
- **`Array.prototype.at()`** (ES2022) — звернення до елементів масиву з кінця без ручного обчислення індексу: `array.at(-1)` замість `array[array.length - 1]`;
- **`structuredClone()`** (ES2022, як глобальна функція середовища виконання) — вбудований спосіб глибокого клонування об'єктів без сторонніх бібліотек;
- **`Object.groupBy()` та `Map.groupBy()`** (ES2024) — угруповання елементів масиву за обчислюваним ключем однією функцією, без ручного написання `reduce`;
- **`Promise.withResolvers()`** (ES2024) — компактний спосіб отримати `promise`, `resolve` та `reject` одним викликом, корисний при побудові власних асинхронних утиліт.

Ці можливості не змінюють фундаментальні концепції, розглянуті нижче (деструктуризація, spread/rest, модулі, Promise), а роблять код, побудований на цих концепціях, ще компактнішим. У прикладах цієї лекції вони використовуються там, де це природно.

## ES6+ можливості: деструктуризація, spread/rest, arrow functions

### Деструктуризація: революція в роботі з даними

**Деструктуризація** — це синтаксична конструкція, яка дозволяє розпакувати значення з масивів або властивості з об'єктів і присвоїти їх окремим змінним за один синтаксичний крок. Ця функціональність радикально змінила підхід до роботи з складними структурами даних у JavaScript.

#### Філософія деструктуризації

Традиційно програмісти змушені були писати повторюваний код для витягування даних зі структур. Деструктуризація відображає фундаментальний принцип сучасного програмування: код повинен бути виразним та мінімізувати boilerplate (шаблонний код).

Концептуально деструктуризація працює як "дзеркальне відображення" конструкції даних. Якщо ми можемо створити масив як `[1, 2, 3]`, то ми можемо його деструктурувати як `[a, b, c]`.

#### Деструктуризація масивів

Деструктуризація масивів базується на позиційному принципі — елементи витягуються відповідно до їх позиції в масиві.

```javascript
// Традиційний підхід: громіздкий код
const coordinates = [10, 20, 30];
const x = coordinates[0];
const y = coordinates[1];
const z = coordinates[2];

// ES6+ рішення: елегантна деструктуризація
const [x, y, z] = [10, 20, 30];

// Пропуск елементів дозволяє селективно витягувати тільки потрібні значення
const colors = ['червоний', 'зелений', 'синій', 'жовтий'];
const [primary, , tertiary] = colors;
console.log(primary, tertiary); // 'червоний' 'синій'

// Значення за замовчуванням захищають від undefined
const [width = 100, height = 200, depth = 50] = [300, 150];
console.log(width, height, depth); // 300 150 50

// Rest синтаксис дозволяє зібрати решту елементів
const [head, ...tail] = [1, 2, 3, 4, 5];
console.log(head); // 1
console.log(tail); // [2, 3, 4, 5]
```

#### Деструктуризація об'єктів

На відміну від масивів, деструктуризація об'єктів базується на іменах властивостей, що робить її більш семантичною та стійкою до змін структури.

```javascript
const person = {
    name: 'Іван Петренко',
    age: 25,
    city: 'Київ',
    profession: 'Розробник'
};

// Базова деструктуризація
const { name, age, city } = person;

// Перейменування змінних вирішує конфлікти імен та покращує семантику
const { name: fullName, age: years } = person;

// Значення за замовчуванням
const { name, age, salary = 50000 } = person;

// Глибока (вкладена) деструктуризація дозволяє працювати з складними структурами
const company = {
    name: 'TechCorp',
    location: {
        country: 'Україна',
        city: 'Київ',
        coordinates: { lat: 50.4501, lng: 30.5234 }
    }
};

const {
    name: companyName,
    location: {
        city,
        coordinates: { lat, lng }
    }
} = company;
```

#### Деструктуризація в параметрах функцій

Деструктуризація в параметрах функцій є одним з найпотужніших застосувань цієї можливості, дозволяючи створювати більш виразні та гнучкі API.

```javascript
// Традиційний підхід: позиційні параметри
function createUser(name, age, email, city, profession) {
    return { id: Math.random(), name, age, email, city, profession };
}
// Проблема: порядок параметрів має значення, легко помилитися

// ES6+ підхід: деструктуризація параметрів
function createUserModern({ name, age, email, city = 'Київ', profession = 'Спеціаліст' }) {
    return { id: Math.random(), name, age, email, city, profession };
}

// Переваги: порядок не має значення, значення за замовчуванням, самодокументованість
const user = createUserModern({
    profession: 'Розробник',
    email: 'dev@example.com',
    name: 'Анна',
    age: 27
});
```

### Оператори Spread і Rest: універсальність через три крапки

Оператори spread (`...`) та rest (`...`) є одними з найбільш універсальних та потужних додатків ES6+. Незважаючи на ідентичний синтаксис, вони виконують протилежні функції: spread розгортає структури, а rest збирає елементи в структури.

#### Spread оператор: розгортання як філософія

**Концептуальна основа spread оператора** полягає в тому, що він дозволяє "розпакувати" ітерований об'єкт і передати його елементи як окремі аргументи або значення.

```javascript
// Spread з масивами: об'єднання та копіювання
const primaryColors = ['червоний', 'синій', 'жовтий'];
const secondaryColors = ['зелений', 'помаранчевий', 'фіолетовий'];

// Об'єднання масивів: до ES6 використовувався concat()
const allColors = [...primaryColors, ...secondaryColors];

// Shallow копіювання масиву
const colorsCopy = [...primaryColors];
// Це створює новий масив, а не посилання на існуючий

// Додавання елементів в довільних позиціях
const extendedColors = ['білий', ...primaryColors, 'чорний', ...secondaryColors];

// Spread з рядками демонструює універсальність оператора
const greeting = "Привіт";
const characters = [...greeting];
console.log(characters); // ['П', 'р', 'и', 'в', 'і', 'т']

// Spread з об'єктами дозволяє елегантно працювати з immutable patterns
const baseConfig = {
    theme: 'light',
    language: 'uk',
    notifications: true
};

const userConfig = {
    ...baseConfig,
    theme: 'dark', // Перезаписує існуючу властивість
    fontSize: 'large' // Додає нову властивість
};
```

#### Rest параметри: збирання як принцип

**Rest параметри** дозволяють функціям приймати змінну кількість аргументів, збираючи їх у масив. Це рішення замінило застарілий об'єкт `arguments` більш сучасним та зручним підходом.

```javascript
// Проблема з arguments (застарілий підхід)
function oldSum() {
    var total = 0;
    for (var i = 0; i < arguments.length; i++) {
        total += arguments[i];
    }
    return total;
}
// arguments - це не справжній масив, тому методи масивів недоступні

// ES6+ рішення з rest параметрами
function sum(...numbers) {
    return numbers.reduce((total, num) => total + num, 0);
}

console.log(sum(1, 2, 3, 4, 5)); // 15
console.log(sum(10, 20)); // 30
console.log(sum()); // 0

// Комбінування звичайних параметрів з rest
function createMessage(level, category, ...details) {
    const timestamp = new Date().toISOString();
    return {
        timestamp,
        level,
        category,
        message: details.join(' '),
        details
    };
}

const errorMessage = createMessage('ERROR', 'AUTH', 'User', 'authentication', 'failed');
```

### Arrow Functions: функціональна революція

**Стрілочні функції (Arrow Functions)** є однією з найвидимих та найбільш використовуваних можливостей ES6+. Вони не просто надають скорочений синтаксис — вони змінюють семантику функцій, особливо в контексті обробки `this`.

#### Синтаксична еволюція функцій

```javascript
// Еволюція синтаксису функцій у JavaScript

// 1. Function declaration (традиційний спосіб)
function multiply(a, b) {
    return a * b;
}

// 2. Function expression
const multiply = function(a, b) {
    return a * b;
};

// 3. Arrow function (ES6+)
const multiply = (a, b) => a * b;

// Різні форми arrow functions
const square = x => x * x; // Один параметр - дужки не обов'язкові
const greet = () => console.log('Привіт!'); // Без параметрів
const processData = data => {
    // Багаторядкове тіло функції
    const processed = data.map(item => item * 2);
    return processed.filter(item => item > 10);
};

// Повернення об'єкта (потрібні дужки)
const createUser = (name, age) => ({ name, age, id: Math.random() });
```

#### Семантичні відмінності: контекст this

Найважливішою концептуальною відмінністю стрілочних функцій є їхня поведінка щодо контексту `this`. Традиційні функції створюють власний контекст `this`, тоді як стрілочні функції наслідують `this` з лексичного оточення.

```javascript
// Проблема традиційних функцій з this
const timer = {
    seconds: 0,
    start: function() {
        // this тут посилається на об'єкт timer
        setInterval(function() {
            this.seconds++; // this тут посилається на global/window
            console.log(this.seconds); // undefined або помилка
        }, 1000);
    }
};

// Класичне рішення: збереження контексту
const timer = {
    seconds: 0,
    start: function() {
        const self = this; // Зберігаємо посилання на правильний this
        setInterval(function() {
            self.seconds++;
            console.log(self.seconds);
        }, 1000);
    }
};

// ES6+ рішення: стрілочні функції наслідують this
const timer = {
    seconds: 0,
    start: function() {
        setInterval(() => {
            this.seconds++; // this тут правильно посилається на timer
            console.log(this.seconds);
        }, 1000);
    }
};
```

#### Практичне застосування в методах масивів

```javascript
const products = [
    { name: 'Ноутбук', price: 25000, category: 'electronics' },
    { name: 'Книга', price: 300, category: 'books' },
    { name: 'Навушники', price: 2000, category: 'electronics' }
];

// ES6+ підхід: лаконічний та виразний
const expensiveElectronics = products
    .filter(product => product.category === 'electronics')
    .filter(product => product.price > 5000)
    .map(product => ({
        name: product.name,
        formattedPrice: `${product.price} грн`
    }));
```

## Модульна система (import/export)

### Концептуальні основи модульності

**Модуль** у JavaScript — це окремий файл, який інкапсулює функціональність та експортує тільки те, що повинно бути доступне зовні. Це реалізує принципи інкапсуляції та розділення відповідальності.

### Export: оголошення публічного API

#### Named Export

```javascript
// mathematics.js
export const PI = 3.14159;
export function add(a, b) { return a + b; }
export function multiply(a, b) { return a * b; }

export class Calculator {
    constructor() { this.memory = 0; }
    add(value) { this.memory += value; return this; }
    getResult() { return this.memory; }
}

// Альтернативний синтаксис
const subtract = (a, b) => a - b;
export { subtract };
```

#### Default Export

```javascript
// logger.js
export default class Logger {
    constructor(level = 'INFO') {
        this.level = level;
    }

    log(message) {
        console.log(`[${this.level}] ${message}`);
    }
}

// Можна комбінувати default та named
export const LOG_LEVELS = ['INFO', 'WARN', 'ERROR'];
```

### Import: споживання функціональності

```javascript
// Іменований імпорт
import { add, multiply, Calculator } from './mathematics.js';

// Default імпорт
import Logger from './logger.js';

// Комбінований імпорт
import Logger, { LOG_LEVELS } from './logger.js';

// Namespace імпорт
import * as MathUtils from './mathematics.js';

// Динамічний імпорт
async function loadModule() {
    const math = await import('./mathematics.js');
    return math.add(1, 2);
}
```

## Promises, async/await, обробка помилок

### Promises: концептуальна революція

**Promise** представляє eventual completion або failure асинхронної операції та її результуючу вартість. Promise має три стани: pending (очікування), fulfilled (виконано), rejected (відхилено).

```mermaid
stateDiagram-v2
    [*] --> Pending
    Pending --> Fulfilled: resolve(value)
    Pending --> Rejected: reject(error)
    Fulfilled --> [*]
    Rejected --> [*]
```

#### Створення та використання Promise

```javascript
// Створення Promise
const fetchUserData = (userId) => {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            if (userId > 0) {
                resolve({
                    id: userId,
                    name: `Користувач ${userId}`,
                    email: `user${userId}@example.com`
                });
            } else {
                reject(new Error('Некоректний ID користувача'));
            }
        }, 1000);
    });
};

// Використання Promise
fetchUserData(123)
    .then(user => {
        console.log('Користувач:', user);
        return fetchUserPosts(user.id);
    })
    .then(posts => {
        console.log('Пости:', posts);
    })
    .catch(error => {
        console.error('Помилка:', error.message);
    })
    .finally(() => {
        console.log('Операція завершена');
    });
```

#### Promise методи

```javascript
// Promise.all() - чекає на всі Promise
const [userData, userPosts, userStats] = await Promise.all([
    fetchUserData(1),
    fetchUserPosts(1),
    fetchUserStats(1)
]);

// Promise.allSettled() - не зупиняється на помилці
const results = await Promise.allSettled([
    fetchUserData(1),
    fetchUserData(999) // може дати помилку
]);

// Promise.race() - повертає перший завершений (успішний чи з помилкою)
const winner = await Promise.race([
    fetchData(),
    timeout(5000) // timeout після 5 секунд
]);

// Promise.any() - повертає перший успішний, ігноруючи помилки інших
const data = await Promise.any([
    fetchFromPrimaryServer(),
    fetchFromSecondaryServer(),
    fetchFromCache()
]);
// Відхиляється лише тоді, коли ВСІ передані Promise відхилені
```

Різниця між `Promise.race()` і `Promise.any()` часто плутає на практиці: `race()` завершується першим результатом незалежно від того, успішний він чи ні, тоді як `any()` чекає саме на перший *успішний* результат і ігнорує помилки, доки не відмовлять усі варіанти.

### Async/Await: синтаксичний цукор з глибокою семантикою

**Async/await** дозволяє писати асинхронний код у стилі, близькому до синхронного.

```javascript
// Еволюція підходів
// 1. Callback Hell
fetchUser(id, (userErr, user) => {
    if (userErr) return callback(userErr);
    fetchPosts(user.id, (postsErr, posts) => {
        if (postsErr) return callback(postsErr);
        callback(null, { user, posts });
    });
});

// 2. Promise chains
function loadUserData(id) {
    return fetchUser(id)
        .then(user => fetchPosts(user.id)
            .then(posts => ({ user, posts })));
}

// 3. Async/await (найкраще рішення)
async function loadUserData(id) {
    try {
        const user = await fetchUser(id);
        const posts = await fetchPosts(user.id);
        return { user, posts };
    } catch (error) {
        console.error('Помилка:', error);
        throw error;
    }
}
```

#### Послідовне vs паралельне виконання

```javascript
// НЕЕФЕКТИВНО: послідовне виконання
async function loadDataSequential(userId) {
    const userData = await fetchUserData(userId);    // ~1 секунда
    const userPosts = await fetchUserPosts(userId);  // ~1 секунда
    const userStats = await fetchUserStats(userId);  // ~1 секунда
    // Загалом: ~3 секунди
    return { userData, userPosts, userStats };
}

// ЕФЕКТИВНО: паралельне виконання
async function loadDataParallel(userId) {
    const [userData, userPosts, userStats] = await Promise.all([
        fetchUserData(userId),
        fetchUserPosts(userId),
        fetchUserStats(userId)
    ]);
    // Загалом: ~1 секунда
    return { userData, userPosts, userStats };
}
```

### Обробка помилок

#### Створення власних типів помилок

```javascript
class APIError extends Error {
    constructor(message, statusCode) {
        super(message);
        this.name = 'APIError';
        this.statusCode = statusCode;
    }
}

class ValidationError extends Error {
    constructor(message, field) {
        super(message);
        this.name = 'ValidationError';
        this.field = field;
    }
}
```

#### Комплексна обробка помилок

```javascript
async function fetchUserSafely(userId) {
    try {
        // Валідація
        if (!userId || userId <= 0) {
            throw new ValidationError('ID має бути позитивним числом');
        }

        const user = await fetchUser(userId);
        return user;

    } catch (error) {
        if (error instanceof ValidationError) {
            console.error('Валідаційна помилка:', error.message);
        } else if (error instanceof APIError && error.statusCode >= 500) {
            console.error('Серверна помилка:', error.message);
        }

        // Повернути fallback значення
        return { id: userId, name: 'Невідомий користувач' };
    }
}
```

## Event Loop та асинхронність в JavaScript

### Фундаментальні основи Event Loop

**Event Loop** є серцем асинхронності JavaScript. Це механізм, який дозволяє однопоточній мові виконувати неблокуючі операції шляхом делегування операцій системним API та управління чергами завдань.

```mermaid
graph TB
    subgraph "JavaScript Engine"
        CS[Call Stack]
        H[Heap]
    end
    subgraph "Web APIs"
        WA[Timers, Fetch, DOM]
    end
    subgraph "Event Loop"
        MT[Macrotask Queue]
        MC[Microtask Queue]
        EL[Event Loop]
    end

    CS --> WA
    WA --> MT
    WA --> MC
    MC --> EL
    MT --> EL
    EL --> CS
```

#### Пріоритети виконання в Event Loop

1. **Синхронний код** виконується негайно в Call Stack;
2. **Microtasks** мають найвищий пріоритет серед асинхронних операцій;
3. **Macrotasks** виконуються після завершення всіх microtasks.

```javascript
console.log('1: Синхронний код');

setTimeout(() => {
    console.log('2: Macrotask (setTimeout)');
}, 0);

Promise.resolve().then(() => {
    console.log('3: Microtask (Promise)');
});

console.log('4: Синхронний код');

// Вивід: 1 → 4 → 3 → 2
```

#### Типи завдань

**Macrotasks**: setTimeout, setInterval, I/O операції, DOM події
**Microtasks**: Promise callbacks, queueMicrotask, async/await

### Продуктивність та оптимізація

#### Уникнення блокування Event Loop

```javascript
// ПОГАНО: блокування Event Loop
function heavyCalculation() {
    let result = 0;
    for (let i = 0; i < 10000000000; i++) {
        result += Math.random();
    }
    return result; // Блокує UI на кілька секунд
}

// ДОБРЕ: неблокуюча операція
function heavyCalculationAsync(callback) {
    let result = 0, processed = 0;
    const total = 10000000000, chunkSize = 1000000;

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

#### Web Workers для дійсно паралельних обчислень

Розбиття важких обчислень на частини, показане вище, — компроміс: обчислення все одно виконуються в тому самому потоці, лише невеликими порціями. Якщо потрібно виконати справді важкі обчислення, не "заморожуючи" інтерфейс жодним чином, використовують **Web Workers** — механізм, що запускає JavaScript-код в окремому потоці браузера, повністю ізольованому від головного потоку з інтерфейсом:

```javascript
// Головний потік
const worker = new Worker('heavy-calculation.js');

worker.postMessage({ numbers: largeDataset });

worker.onmessage = (event) => {
    console.log('Результат обчислення:', event.data);
};

// heavy-calculation.js — виконується в окремому потоці
self.onmessage = (event) => {
    const result = event.data.numbers.reduce((sum, n) => sum + Math.sqrt(n), 0);
    self.postMessage(result);
};
```

Головний потік лишається чутливим до дій користувача (кліків, прокручування) увесь час, доки worker обчислює результат у фоні; обмін даними між потоками відбувається через повідомлення (`postMessage`/`onmessage`), а не через спільну пам'ять.

### Практичні приклади асинхронного коду

#### Connection Pool для оптимізації

```javascript
class ConnectionPool {
    constructor(maxConnections = 5) {
        this.maxConnections = maxConnections;
        this.activeConnections = 0;
        this.queue = [];
    }

    async execute(operation) {
        return new Promise((resolve, reject) => {
            this.queue.push({ operation, resolve, reject });
            this.processQueue();
        });
    }

            if (request.priority > this.queue[i].priority) {
                this.queue.splice(i, 0, request);
                inserted = true;
                break;
            }
        }
        if (!inserted) {
            this.queue.push(request);
        }
    }

    async processQueue() {
        if (this.activeConnections >= this.maxConnections || this.queue.length === 0) {
            return;
        }

        const request = this.queue.shift();
        this.activeConnections++;

        try {
            const result = await request.operation();
            this.stats.completedRequests++;
            request.resolve(result);
        } catch (error) {
            this.stats.failedRequests++;
            request.reject(error);
        } finally {
            this.activeConnections--;
            this.processQueue(); // Обробити наступний запит
        }
    }

    getStats() {
        return {
            ...this.stats,
            queueLength: this.queue.length,
            activeConnections: this.activeConnections,
            successRate: (this.stats.completedRequests / this.stats.totalRequests * 100).toFixed(2) + '%'
        };
    }
}

// Використання пулу з'єднань
const apiPool = new ConnectionPool(3);

async function fetchUserWithPool(id, priority = 0) {
    return apiPool.execute(async () => {
        const response = await fetch(`/api/users/${id}`);
        if (!response.ok) {
            throw new Error(`HTTP ${response.status}: ${response.statusText}`);
        }
        return response.json();
    }, priority);
}

// Приклад використання з пріоритетами
async function loadDashboard() {
    const requests = [
        fetchUserWithPool(1, 10),  // Високий пріоритет - критичні дані
        fetchUserWithPool(2, 5),   // Середній пріоритет
        fetchUserWithPool(3, 1),   // Низький пріоритет - додаткові дані
        fetchUserWithPool(4, 10),  // Високий пріоритет
        fetchUserWithPool(5, 5)    // Середній пріоритет
    ];

    try {
        const users = await Promise.allSettled(requests);
        console.log('Статистика пулу:', apiPool.getStats());
        return users.filter(result => result.status === 'fulfilled').map(result => result.value);
    } catch (error) {
        console.error('Помилка завантаження dashboard:', error);
    }
}
```

##### Розумне кешування асинхронних операцій

```javascript
// Просунута система кешування з TTL та стратегіями оновлення
class SmartAsyncCache {
    constructor(options = {}) {
        this.cache = new Map();
        this.defaultTTL = options.ttl || 300000; // 5 хвилин за замовчуванням
        this.maxSize = options.maxSize || 100;
        this.stats = {
            hits: 0,
            misses: 0,
            evictions: 0
        };

        // Автоматичне очищення застарілих записів
        this.cleanupInterval = setInterval(() => {
            this.cleanup();
        }, 60000); // Кожну хвилину
    }

    async get(key, fetchFunction, options = {}) {
        const ttl = options.ttl || this.defaultTTL;
        const forceRefresh = options.forceRefresh || false;
        const cached = this.cache.get(key);

        // Перевірка актуальності кешованих даних
        if (cached && !forceRefresh && !this.isExpired(cached, ttl)) {
            this.stats.hits++;
            cached.lastAccessed = Date.now();
            return cached.data;
        }

        this.stats.misses++;

        // Background refresh для кращого UX
        if (cached && !forceRefresh && options.backgroundRefresh) {
            // Повернути застарілі дані, але оновити в фоні
            this.refreshInBackground(key, fetchFunction, ttl);
            return cached.data;
        }

        try {
            // Завантаження нових даних
            const data = await fetchFunction();
            this.set(key, data, ttl);
            return data;
        } catch (error) {
            // Graceful degradation - якщо є застарілі дані, повернути їх при помилці
            if (cached && options.fallbackToStale) {
                console.warn('Використання застарілих даних через помилку:', error.message);
                return cached.data;
            }
            throw error;
        }
    }

    set(key, data, ttl = this.defaultTTL) {
        // Перевірка розміру кешу та видалення найменш використовуваних записів
        if (this.cache.size >= this.maxSize) {
            this.evictLRU();
        }

        this.cache.set(key, {
            data,
            createdAt: Date.now(),
            lastAccessed: Date.now(),
            ttl
        });
    }

    isExpired(cached, ttl) {
        return Date.now() - cached.createdAt > ttl;
    }

    async refreshInBackground(key, fetchFunction, ttl) {
        try {
            const data = await fetchFunction();
            this.set(key, data, ttl);
            console.log(`Background refresh completed for key: ${key}`);
        } catch (error) {
            console.warn('Background refresh failed for key:', key, error);
        }
    }

    evictLRU() {
        // Видалити найменш нещодавно використаний елемент
        let oldestKey = null;
        let oldestAccess = Date.now();

        for (const [key, value] of this.cache) {
            if (value.lastAccessed < oldestAccess) {
                oldestAccess = value.lastAccessed;
                oldestKey = key;
            }
        }

        if (oldestKey) {
            this.cache.delete(oldestKey);
            this.stats.evictions++;
            console.log(`Evicted LRU cache entry: ${oldestKey}`);
        }
    }

    cleanup() {
        const now = Date.now();
        const toDelete = [];

        for (const [key, value] of this.cache) {
            if (this.isExpired(value, value.ttl)) {
                toDelete.push(key);
            }
        }

        toDelete.forEach(key => {
            this.cache.delete(key);
            this.stats.evictions++;
        });

        if (toDelete.length > 0) {
            console.log(`Очищено ${toDelete.length} застарілих записів з кешу`);
        }
    }

    getStats() {
        const totalRequests = this.stats.hits + this.stats.misses;
        const hitRate = totalRequests > 0 ? (this.stats.hits / totalRequests * 100).toFixed(2) : 0;

        return {
            ...this.stats,
            hitRate: hitRate + '%',
            cacheSize: this.cache.size,
            memoryUsage: this.estimateMemoryUsage()
        };
    }

    estimateMemoryUsage() {
        // Приблизна оцінка використання пам'яті
        let size = 0;
        for (const [key, value] of this.cache) {
            size += key.length * 2; // UTF-16 characters
            size += JSON.stringify(value.data).length * 2;
        }
        return `${(size / 1024).toFixed(2)} KB`;
    }

    clear() {
        this.cache.clear();
        clearInterval(this.cleanupInterval);
    }
}

// Глобальний кеш для API запитів
const apiCache = new SmartAsyncCache({
    ttl: 300000,      // 5 хвилин
    maxSize: 50       // Максимум 50 записів
});

// Кеширована функція для отримання користувачів
async function getCachedUser(id, options = {}) {
    return apiCache.get(
        `user-${id}`,
        () => fetch(`/api/users/${id}`).then(r => r.json()),
        {
            backgroundRefresh: true,
            fallbackToStale: true,
            ...options
        }
    );
}

// Приклад використання з різними стратегіями
async function loadUserProfile(userId) {
    try {
        // Завантажити основні дані користувача з кешу
        const user = await getCachedUser(userId, {
            backgroundRefresh: true
        });

        // Завантажити додаткові дані паралельно
        const [posts, stats] = await Promise.all([
            apiCache.get(`user-posts-${userId}`,
                () => fetch(`/api/users/${userId}/posts`).then(r => r.json()),
                { ttl: 600000 } // 10 хвилин для постів
            ),
            apiCache.get(`user-stats-${userId}`,
                () => fetch(`/api/users/${userId}/stats`).then(r => r.json()),
                { ttl: 30000 } // 30 секунд для статистики
            )
        ]);

        return { user, posts, stats };
    } catch (error) {
        console.error('Помилка завантаження профілю:', error);
        throw error;
    }
}
```

## Висновки

Ця лекція пройшла шлях від архітектури вебдодатку в цілому (частина I) до інструментів мови, якими ця архітектура реалізується в коді (частина II). Це не два окремих набори знань, а два масштаби розгляду одного й того самого предмета: коли частина I говорить про "асинхронну взаємодію клієнта й сервера через AJAX", частина II показує, як саме ця взаємодія виглядає в реальному коді — через `fetch`, `Promise` та `async/await`. Коли частина I описує REST API чи GraphQL-запит, частина II дає мову (буквально), якою цей запит обробляється: деструктуризацію відповіді, модулі для організації клієнтського коду, обробку помилок мережі.

### Ключові висновки про ES6+ можливості

**Деструктуризація** не просто спрощує синтаксис — вона змінює спосіб мислення про структури даних, роблячи код більш декларативним та менш схильним до помилок. Особливо це помітно при роботі з API відповідями та конфігураційними об'єктами.

**Spread та Rest оператори** демонструють елегантність сучасного JavaScript, де три крапки можуть виконувати протилежні функції залежно від контексту. Ці оператори є основою для багатьох функціональних підходів та immutable паттернів, які є критично важливими в сучасних фреймворках як React.

**Стрілочні функції** революціонізували не тільки синтаксис, але й семантику функцій у JavaScript. Лексичне зв'язування `this` вирішило одну з найбільш заплутаних проблем мови та зробило код більш передбачуваним, особливо в контексті асинхронного програмування та обробки подій.

**Модульна система** перетворила JavaScript з мови сценаріїв на повноцінну платформу для створення великих додатків. Стандартизація import/export забезпечила основу для сучасних інструментів збірки та оптимізації коду, дозволяючи створювати масштабовані архітектури з чіткими залежностями.

### Асинхронне програмування: від хаосу до елегантності

Еволюція від callback-ів до Promise та async/await відображає зрілість JavaScript як платформи. **Promises** надали структурований спосіб роботи з асинхронністю, вирішивши проблему "callback hell" та забезпечивши композиційність асинхронних операцій. **Async/await** зробив асинхронний код читабельним як синхронний, не жертвуючи при цьому потужністю та гнучкістю.

**Event Loop** залишається серцем JavaScript, і розуміння його роботи критично важливе для написання ефективного коду. Знання різниці між microtasks та macrotasks дозволяє передбачати поведінку програми та оптимізувати її продуктивність. Особливо важливо розуміти, як уникнути блокування Event Loop при роботі з важкими обчисленнями.

### Найкращі практики для сучасної розробки

1. **Використовуйте const та let замість var** для блочної області видимості та запобігання hoisting проблем;
2. **Віддавайте перевагу async/await** над Promise chains для кращої читабельності, але розумійте коли використовувати паралельне виконання з Promise.all;
3. **Структуруйте код в модулі** з чіткими залежностями та єдиною відповідальністю - це основа масштабованої архітектури;
4. **Обробляйте помилки на кожному рівні** з використанням спеціалізованих класів помилок та централізованої системи логування;
5. **Уникайте блокування Event Loop** через розбиття важких операцій на частини або використання Web Workers для CPU-інтенсивних завдань;
6. **Використовуйте кешування розумно** з урахуванням TTL, стратегій оновлення та memory management;

Розуміння цих концепцій та їх практичне застосування є основою для створення сучасних, ефективних та підтримуваних JavaScript-застосунків, які можуть конкурувати з рішеннями на інших платформах за продуктивністю та надійністю. Ключ до успіху — це не просто знання синтаксису, а глибоке розуміння принципів асинхронності, архітектурних патернів та оптимізації продуктивності.

Разом частини I та II цієї лекції формують фундамент, на якому будуватиметься решта курсу: наступні теми — від проєктування бази даних до розгортання застосунку — послідовно спиратимуться і на архітектурні поняття (client-server, REST, SPA/SSR), і на мовні інструменти сучасного JavaScript (модулі, асинхронність, обробку помилок), розглянуті тут.
