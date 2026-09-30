# Тестування та розгортання серверної частини

### Забезпечення якості, випуск і супровід застосунків



## 📋 План лекції

- **🔬 Вступ до тестування**: типи, піраміда, TDD
- **⚡ Модульне тестування з Vitest**
- **🔗 Інтеграційне тестування API**
- **📊 Покриття коду та безперервна інтеграція**
- **⚙️ Конфігурація середовищ**
- **☁️ Розгортання на хмарних платформах**
- **📈 Моніторинг та логування**
- **✅ Висновки та найкращі практики**



## 🤔 Навіщо тестувати серверну частину?

**🎯 Основні причини:**
- 🔍 Виявлення помилок до випуску
- 💪 Впевненість при змінах коду (рефакторинг без страху)
- 📚 Тести як жива документація поведінки
- 💰 Зниження вартості виправлення помилок

**📈 Економіка помилок:** чим пізніше знайдено помилку, тим дорожче її виправлення (за оцінками галузі, у робочому середовищі — на порядки дорожче, ніж під час розробки)

**🔗 Зв'язок:** тести ловлять помилки *до* випуску, моніторинг — *після*



## 🏗️ Типи тестування

```mermaid
flowchart TB
    E2E["End-to-End (наскрізні)<br/>мало · повільно · дорого"]
    INT["Integration (інтеграційні)<br/>помірно · помірна швидкість"]
    UNIT["Unit (модульні)<br/>багато · швидко · дешево"]
    E2E --- INT
    INT --- UNIT
    style E2E fill:#fde2e2,stroke:#c0392b
    style INT fill:#fff3cd,stroke:#d4a017
    style UNIT fill:#d4edda,stroke:#2e8b57
```

- **🧩 Модульні** — окрема функція чи клас
- **🔗 Інтеграційні** — взаємодія модулів, API з базою даних
- **🌍 Наскрізні** — повний сценарій користувача



## 🔁 Test-Driven Development (TDD)

```mermaid
flowchart LR
    R["Red: написати тест, який не проходить"] --> G["Green: мінімальний код, щоб тест пройшов"]
    G --> F["Refactor: покращити код, зберігши поведінку"]
    F --> R
```

**✨ Що дає TDD:**
- 🎯 Код пишеться під конкретну вимогу (YAGNI, KISS)
- 🧱 Код стає тестованим за побудовою
- 🛡️ Безпечний рефакторинг

**⚠️ Реалістично:** TDD корисний для логіки з чіткими правилами; для прототипів і UI — за потреби. Поряд існує **BDD** — тести у формі опису поведінки



## 🚀 Vitest — основний інструмент

| Критерій | Vitest | Jest | `node:test` |
| --- | --- | --- | --- |
| ESM | вбудовано | експериментально | вбудовано |
| Швидкість | висока | середня | висока |
| Налаштування | мінімальні | помірні | нульові |
| Моки й покриття | є | є | базові |

**✅ Чому Vitest у курсі:**
- 📦 працює з ES-модулями без обхідних шляхів
- 🔁 той самий інструмент для React-компонентів (лекція 7)
- 🔄 API, сумісний із Jest (`describe`, `it`, `expect`)

```bash
npm install --save-dev vitest @vitest/coverage-v8 supertest
```



## ⚙️ Налаштування Vitest

```javascript
// vitest.config.js
import { defineConfig } from 'vitest/config';

export default defineConfig({
    test: {
        projects: [
            { test: { name: 'unit', include: ['tests/unit/**/*.test.js'] } },
            {
                test: {
                    name: 'integration',
                    include: ['tests/integration/**/*.test.js'],
                    globalSetup: ['tests/setup/global-setup.js'],
                    setupFiles: ['tests/setup/env.js']
                }
            }
        ],
        coverage: {
            provider: 'v8',
            include: ['src/**/*.js'],
            thresholds: { lines: 70, functions: 70, branches: 70 }
        }
    }
});
```

**📜 Скрипти:** `test`, `test:unit`, `test:integration`, `test:coverage`



## 📐 Структура тесту: патерн AAA

```javascript
import { describe, it, expect } from 'vitest';
import { calculateTotal } from '../../src/utils/price.js';

describe('calculateTotal', () => {
    it('додає податок до ціни', () => {
        // Arrange — підготовка
        const price = 100;
        const tax = 0.2;

        // Act — дія
        const result = calculateTotal(price, tax);

        // Assert — перевірка
        expect(result).toBe(120);
    });
});
```

**🎯 Правила:** один тест — одна поведінка; незалежність; читабельні назви



## 🧪 Модульні тести функцій

```javascript
import { describe, it, expect } from 'vitest';
import { isValidEmail } from '../../src/utils/validation.js';

describe('isValidEmail', () => {
    it.each([
        ['user@example.com', true],
        ['user.name+tag@example.co.uk', true],
        ['invalid-email', false],
        ['user@', false],
        ['test..test@example.com', false]
    ])('isValidEmail(%s) → %s', (email, expected) => {
        expect(isValidEmail(email)).toBe(expected);
    });
});
```

**💡 `test.each`** — таблиця випадків замість копіпасту



## ⏰ Тестування асинхронного коду

```javascript
import { describe, it, expect, vi, beforeEach } from 'vitest';
import argon2 from 'argon2';
import { createUser } from '../../src/services/userService.js';

vi.mock('argon2', () => ({
    default: { hash: vi.fn(), verify: vi.fn() }
}));

describe('createUser', () => {
    beforeEach(() => vi.clearAllMocks());

    it('хешує пароль і не повертає його', async () => {
        argon2.hash.mockResolvedValue('hashed');
        const user = await createUser({ email: 'a@b.com', password: 'Secret123' });

        expect(argon2.hash).toHaveBeenCalledWith('Secret123');
        expect(user).not.toHaveProperty('passwordHash');
    });

    it('відхиляє порожній email', async () => {
        await expect(createUser({ email: '', password: 'x' })).rejects.toThrow();
    });
});
```

**🔐 Argon2id** замінює bcrypt як рекомендований алгоритм хешування паролів



## 🎭 Моки, стаби та інші тестові двійники

| Двійник | Призначення |
| --- | --- |
| **Dummy** | заповнює параметр, не використовується |
| **Stub** | повертає заздалегідь задані значення |
| **Spy** | стежить за викликами, зберігаючи реальну логіку |
| **Mock** | перевіряє очікувані взаємодії |
| **Fake** | спрощена робоча реалізація (in-memory) |

```javascript
vi.spyOn(console, 'error').mockImplementation(() => {});
vi.useFakeTimers();
vi.advanceTimersByTime(1000);
```

**⚠️ Правило:** мокайте зовнішні межі (мережа, час, сервіси), а не власну логіку



## 🔗 Інтеграційні тести: середовище

**🐳 Testcontainers** — справжня PostgreSQL в одноразовому контейнері

```javascript
// tests/setup/global-setup.js
import { PostgreSqlContainer } from '@testcontainers/postgresql';

export default async function setup({ provide }) {
    const container = await new PostgreSqlContainer('postgres:18-alpine').start();
    // застосувати db/schema.sql ...
    provide('databaseUrl', container.getConnectionUri());
    return () => container.stop(); // teardown
}
```

**✅ Переваги:** реальна база замість моків; чисте середовище; однакова поведінка локально й у CI

**♻️ Хуки:** `beforeAll` / `afterEach` (очищення) / `afterAll` (закриття пулу)



## 🌐 Тестування API з supertest

```javascript
import request from 'supertest';
import { app } from '../../src/app.js';

describe('POST /api/auth/register', () => {
    it('створює користувача', async () => {
        const res = await request(app)
            .post('/api/auth/register')
            .send({ email: 'new@example.com', password: 'Secret123' })
            .expect(201);

        expect(res.body).not.toHaveProperty('passwordHash');
    });

    it('409 для дубліката email', async () => {
        await request(app).post('/api/auth/register')
            .send({ email: 'dup@example.com', password: 'Secret123' });
        await request(app).post('/api/auth/register')
            .send({ email: 'dup@example.com', password: 'Secret123' })
            .expect(409);
    });
});
```

**🧩 Розділення `app.js` і `server.js`:** застосунок тестуємо без відкриття порту



## 🔐 Тестування захищених маршрутів

```javascript
const token = createTestToken({ id: 1, role: 'user' });

it('401 без токена', async () => {
    await request(app).get('/api/users/me').expect(401);
});

it('401 з простроченим токеном', async () => {
    await request(app).get('/api/users/me')
        .set('Authorization', `Bearer ${expiredToken}`).expect(401);
});

it('200 з дійсним токеном', async () => {
    await request(app).get('/api/users/me')
        .set('Authorization', `Bearer ${token}`).expect(200);
});
```

**⚠️ Не забути:** перевіряти авторизацію на рівні даних (чужий ресурс → 403/404)



## 🛡️ Тестування middleware та помилок

- **🚦 Rate limiting** → після ліміту 429
- **🌍 CORS** → preflight `OPTIONS` повертає **204** із потрібними заголовками
- **❓ Невідомий маршрут** → 404 у єдиному форматі
- **💥 Внутрішня помилка** → 500 **без** стека викликів

```javascript
it('preflight CORS', async () => {
    await request(app).options('/api/users')
        .set('Origin', 'http://localhost:5173')
        .set('Access-Control-Request-Method', 'GET')
        .expect(204);
});
```



## 📊 Покриття коду

**Метрики:** рядки · функції · гілки · оператори

```bash
npm run test:coverage
```

| Показник | Значення |
| --- | --- |
| Мета для навчального проєкту | 70–80 % |
| Критичний код (автентифікація, платежі) | вище |
| 100 % покриття | не гарантує відсутності помилок |

**⚠️ Антипатерни:** тести без перевірок · тестування реалізації · гонитва за відсотком · нестабільні тести

**🎯 Покриття — індикатор прогалин, а не мета**



## 🤖 Безперервна інтеграція: конвеєр

```mermaid
flowchart LR
    A[Коміт або pull request] --> B[Якість коду: lint]
    B --> C[Тести: модульні та інтеграційні]
    C --> D[Безпека: audit, CodeQL]
    D --> E[Збірка Docker-образу]
    E --> F{Гілка main?}
    F -- так --> G[Розгортання]
    G --> H[Димові тести й моніторинг]
    F -- ні --> I[Кінець]
```

- **CI** — кожна зміна автоматично перевіряється
- **CD** — доставка (вручну) або розгортання (автоматично)



## 🧾 GitHub Actions: workflow

```yaml
# .github/workflows/ci.yml
name: CI
on:
  push: { branches: [main] }
  pull_request:

permissions:
  contents: read   # найменші права

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node-version: [22.x, 24.x]
    steps:
      - uses: actions/checkout@v6
      - uses: actions/setup-node@v6
        with:
          node-version: ${{ matrix.node-version }}
          cache: npm
      - run: npm ci
      - run: npm run test:coverage
      - uses: actions/upload-artifact@v4
        with: { name: coverage-${{ matrix.node-version }}, path: coverage }
```

**📦 Testcontainers працює на `ubuntu-latest` без окремого сервісу PostgreSQL**



## 🔁 Повний конвеєр і залежності

**Одна workflow — кілька завдань:**
- 🧪 `test` — lint + тести на Node.js 22 і 24
- 🔒 `security` — `npm audit`, CodeQL
- 🏗️ `build` — Docker-образ (`needs: [test, security]`)
- 🚀 `deploy` — лише `main`, `environment: production`

**🤖 Dependabot:** автоматичні оновлення залежностей і дій

**📈 Метрики DORA:** частота розгортань · час від коміту до випуску · частка невдалих випусків · час відновлення

**ℹ️ Node.js 20 завершив підтримку у квітні 2026 → матриця 22.x/24.x**



## 🚦 Стратегії розгортання

| Стратегія | Суть | Ризик |
| --- | --- | --- |
| **Recreate** | зупинити стару, запустити нову | простій |
| **Rolling** | поступова заміна екземплярів | середній |
| **Blue-green** | два середовища, перемикання трафіку | низький, дорожче |
| **Canary** | нова версія для малої частки трафіку | найнижчий, складніше |

**↩️ Завжди майте план відкоту**



## 🔒 Безпека в тестуванні та конвеєрі

**🔍 Аналіз безпеки:**
- **SAST** — аналіз коду (CodeQL, `eslint-plugin-security`)
- **DAST** — перевірка працюючого застосунку
- **Залежності** — `npm audit`, Dependabot

```javascript
it('додає захисні заголовки (helmet)', async () => {
    const res = await request(app).get('/health/live');
    expect(res.headers['x-content-type-options']).toBe('nosniff');
    expect(res.headers).not.toHaveProperty('x-powered-by');
});
```

**📋 OWASP Top 10** — перелік ризиків для перевірки



## 💥 Навантажувальне тестування

| Вид | Мета |
| --- | --- |
| Load | поведінка при очікуваному навантаженні |
| Stress | де межа міцності |
| Spike | різкий сплеск трафіку |
| Soak | тривала робота (витоки пам'яті) |

```javascript
// k6
import http from 'k6/http';
import { check } from 'k6';

export const options = {
    stages: [{ duration: '1m', target: 50 }],
    thresholds: { http_req_duration: ['p(95)<500'] }
};

export default function () {
    check(http.get(`${__ENV.BASE_URL}/health/live`), {
        'статус 200': (r) => r.status === 200
    });
}
```



## 🐛 Налагодження та масштабування тестів

**🔧 Налагодження:** конфігурація Debug Vitest у VS Code · `it.only` · `vitest --reporter=verbose`

**📈 Масштабування набору:**
- 🗂️ структура `tests/unit`, `tests/integration`, `tests/setup`
- 🏷️ зрозумілі назви: «що робить за якої умови»
- 🏭 фабрики тестових даних замість копіпасту
- ⚡ паралельний запуск і **shard** у CI



## ⚙️ Конфігурація середовищ

**🏛️ 12-Factor App:** конфігурація — у змінних середовища, не в коді

| Параметр | Development | Test | Production |
| --- | --- | --- | --- |
| База даних | локальна / Docker | контейнер | керована |
| SSL до БД | ні | ні | так |
| Рівень журналу | `debug` | `error` | `info` / `warn` |
| Формат журналу | для людини | мінімальний | JSON |
| CORS | `localhost` | — | лише домен клієнта |

**🧪 Критерій:** якби репозиторій став публічним — чи розкрилося б щось секретне?



## 📁 Змінні середовища

```bash
# Node.js читає файл самостійно, без dotenv
node --env-file=.env src/server.js
node --env-file-if-exists=.env src/server.js
```

```bash
# .env.example — шаблон у репозиторії
NODE_ENV=development
PORT=3000
DATABASE_URL=postgresql://user:password@localhost:5432/appdb
JWT_SECRET=замініть-на-випадковий-рядок-не-менше-32-символів
ALLOWED_ORIGINS=http://localhost:5173
LOG_LEVEL=debug
```

**⚠️** `.env` — у `.gitignore`; значення за замовчуванням — лише для нечутливих параметрів



## ✅ Валідація конфігурації

```javascript
// src/config/index.js
import Joi from 'joi';

const envSchema = Joi.object({
    NODE_ENV: Joi.string().valid('development', 'production', 'test').default('development'),
    PORT: Joi.number().port().default(3000),
    DATABASE_URL: Joi.string().uri({ scheme: ['postgres', 'postgresql'] }).required(),
    JWT_SECRET: Joi.string().min(32).required(),
    LOG_LEVEL: Joi.string().valid('error', 'warn', 'info', 'http', 'debug').default('info')
}).unknown(true);

const { error, value: env } = envSchema.validate(process.env, { abortEarly: false });
if (error) throw new Error(`Некоректна конфігурація: ${error.message}`);

export const config = { env: env.NODE_ENV, port: env.PORT /* ... */ };
```

**🎯 Швидка відмова на старті** — краще не запуститись, ніж працювати з неправильними налаштуваннями



## 🔐 Захист чутливих даних

**🪜 Рівні захисту:**
1. `.env` поза репозиторієм + сканування секретів (`gitleaks`)
2. Сховище секретів платформи, GitHub Secrets
3. Керовані сервіси: AWS Secrets Manager, Azure Key Vault, Vault
4. Короткочасні облікові дані (OIDC)

**🚨 Секрет потрапив у репозиторій?** Вважайте його скомпрометованим: **відкликати й замінити**

```javascript
import { createCipheriv, randomBytes } from 'node:crypto';
const iv = randomBytes(12);                       // унікальний для кожної операції
const cipher = createCipheriv('aes-256-gcm', key, iv);
```

**🚫** `createCipher` видалено з Node.js



## ☁️ Моделі хмарних послуг

| Модель | Ви керуєте | Приклади |
| --- | --- | --- |
| **IaaS** | ОС, середовищем, застосунком | DigitalOcean Droplet, AWS EC2 |
| **PaaS** | лише застосунком і даними | Railway, Render, Heroku, App Platform |
| **SaaS** | нічим, лише користуєтеся | Gmail, Sentry |

**🎯 Для навчальних проєктів** зазвичай обирають PaaS



## 🐳 Контейнеризація з Docker

```dockerfile
# Dockerfile
FROM node:24-alpine AS deps
WORKDIR /app
COPY package*.json ./
RUN npm ci --omit=dev

FROM node:24-alpine
WORKDIR /app
ENV NODE_ENV=production
COPY --from=deps /app/node_modules ./node_modules
COPY . .
USER node
EXPOSE 3000
CMD ["node", "src/server.js"]
```

**✅ Практики:** багатоетапне збирання · `.dockerignore` · запуск від `node` · `node` напряму (не `npm start`), щоб SIGTERM дійшов до застосунку



## 🚄 Railway

```json
{
  "$schema": "https://railway.com/railway.schema.json",
  "build": { "builder": "RAILPACK" },
  "deploy": {
    "startCommand": "node src/server.js",
    "healthcheckPath": "/health/ready",
    "restartPolicyType": "ON_FAILURE"
  }
}
```

**🚀 Кроки:** GitHub-репозиторій → сервіс → база PostgreSQL → змінні → домен

**ℹ️ Актуально:** Railpack замінив Nixpacks; безкоштовного плану немає; `PORT` задає платформа



## 🟣 Heroku

```text
# Procfile
release: npm run migrate
web: node src/server.js
```

- 🧱 **Dyno** — контейнер із процесом застосунку
- 🔄 Фаза `release` — міграції перед перемиканням трафіку
- 💳 **Безкоштовний план скасовано у листопаді 2022**
- 🗄️ Postgres — платний аддон; SSL до БД обов'язковий



## 🎨 Render та 🌊 DigitalOcean

```yaml
# render.yaml
services:
  - type: web
    name: user-api
    runtime: node
    buildCommand: npm ci
    startCommand: node src/server.js
    healthCheckPath: /health/ready
```

| Варіант | Керування | Коли обирати |
| --- | --- | --- |
| **App Platform** | PaaS, `.do/app.yaml` | простота |
| **Droplet + PM2** | IaaS, повний контроль | навчання адмініструванню |

**⚠️** PM2 з ESM: файл `ecosystem.config.cjs`



## ⚖️ Порівняння платформ

| Платформа | Складність | Безкоштовний план | Особливість |
| --- | --- | --- | --- |
| Railway | низька | ні (пробний період) | швидкий старт, Railpack |
| Render | низька | є (з обмеженнями) | `render.yaml` |
| Heroku | низька | ні | зріла екосистема |
| DigitalOcean | від низької до високої | ні | гнучкість, IaaS |

**🧭 Обирайте за:** керованістю · вартістю · вимогами до даних · можливістю переїзду



## 🛑 Коректне завершення роботи

```javascript
// src/server.js
const server = app.listen(config.port);

async function shutdown(signal) {
    health.shuttingDown = true;               // readiness → 503
    server.close(async () => {                // дочекатися поточних запитів
        await pool.end();                     // закрити пул з'єднань
        process.exit(0);
    });
    setTimeout(() => process.exit(1), 10_000).unref();  // страховка
}

process.on('SIGTERM', () => shutdown('SIGTERM'));
process.on('SIGINT', () => shutdown('SIGINT'));
```

**🎯 Без цього** розгортання нової версії обриває запити користувачів



## 📝 Структуроване журналювання (Winston)

```javascript
import winston from 'winston';

export const logger = winston.createLogger({
    level: config.logging.level,
    format: config.env === 'production' ? prodFormat : devFormat,
    defaultMeta: { service: 'user-api' },
    transports: [new winston.transports.Console()],
    silent: config.env === 'test'
});

logger.info('Користувача створено', { userId: user.id });
```

- 📊 Рівні npm: `error` → `warn` → `info` → `http` → `verbose` → `debug` → `silly`
- 🖥️ Пишемо в `stdout` — платформа збирає журнали
- 🚫 Не журналюємо паролі, токени, `req.body`
- ⚡ Альтернатива — **Pino**



## 🌐 Журналювання HTTP-запитів

```javascript
export function requestLogger(req, res, next) {
    const startedAt = process.hrtime.bigint();
    req.id = req.get('x-request-id') ?? randomUUID();
    res.set('X-Request-Id', req.id);

    res.on('finish', () => {
        logger.log(res.statusCode >= 500 ? 'error' : 'http', 'Оброблено HTTP-запит', {
            requestId: req.id,
            method: req.method,
            path: req.originalUrl.split('?')[0],
            status: res.statusCode,
            durationMs: Number(process.hrtime.bigint() - startedAt) / 1e6
        });
    });
    next();
}
```

**🔗 `requestId`** — наскрізна мітка для пошуку всіх записів однієї операції



## 💚 Перевірки стану (health checks)

- **Liveness** `/health/live` — процес живий? Немає — перезапуск
- **Readiness** `/health/ready` — готовий до запитів? Немає — трафік не спрямовується

```javascript
healthRouter.get('/ready', async (req, res) => {
    if (health.shuttingDown) return res.status(503).json({ status: 'shutting_down' });
    try {
        await pool.query('SELECT 1');              // спільний пул
        res.json({ status: 'ok' });
    } catch {
        res.status(503).json({ status: 'unavailable' });
    }
});
```

**⚠️** Код **503** при збої · без зайвих подробиць · `/metrics` (prom-client) не робити публічним



## 🔍 Сервіси моніторингу

| Категорія | Приклади |
| --- | --- |
| Відстеження помилок | Sentry, Bugsnag |
| Метрики та панелі | Prometheus + Grafana, Datadog |
| Журнали | Loki, Better Stack, ELK |
| Доступність ззовні | UptimeRobot, Pingdom |
| Трасування | Jaeger, Tempo, Honeycomb |

**🌐 OpenTelemetry** — відкритий стандарт збирання телеметрії, без прив'язки до постачальника

```bash
node --import ./src/instrumentation.js src/server.js
```



## 🚨 Відстеження помилок (Sentry)

```javascript
// src/instrument.js — завантажується першим
import * as Sentry from '@sentry/node';

Sentry.init({
    dsn: config.monitoring.sentryDsn,
    environment: config.env,
    release: process.env.RELEASE_SHA,
    sendDefaultPii: false
});
```

```javascript
// src/app.js — після маршрутів, до власного обробника
Sentry.setupExpressErrorHandler(app);
app.use(errorHandler);
```

**ℹ️ Актуально:** SDK 8+ — без `Sentry.Handlers.*`; ініціалізація до імпорту Express



## 🧯 Обробка помилок

```javascript
export class AppError extends Error {
    constructor(message, statusCode = 500, code = 'INTERNAL_ERROR') {
        super(message);
        this.statusCode = statusCode;
        this.code = code;
    }
}
export class NotFoundError extends AppError {
    constructor(resource = 'Ресурс') { super(`${resource} не знайдено`, 404, 'NOT_FOUND'); }
}
```

- 🎯 Очікувані помилки (4xx) ≠ збої (5xx)
- 🚫 Відповідь клієнту без стека й подробиць БД
- 🧾 У журнал — контекст і `requestId`, **без** `req.body`



## 🔔 Сповіщення (alerting)

- 🎯 Сповіщайте про **симптоми**, а не причини
- ✋ Кожне сповіщення вимагає дії
- 🚦 Пріоритети: критичне — негайно; попередження — у канал
- 🔗 Контекст: панель, останній випуск, runbook
- 📉 Сплеск помилок після розгортання → **відкіт**

**⚠️ Втома від сповіщень** — головний ворог моніторингу



## 👁️ Спостережуваність

| Сигнал | Питання | Інструменти |
| --- | --- | --- |
| **Журнали** | Що сталося? | Winston, Pino, Loki |
| **Метрики** | Скільки й як швидко? | Prometheus, prom-client |
| **Трейси** | Де витрачено час? | OpenTelemetry, Jaeger |

**🌟 Чотири золоті сигнали:**
1. ⏱️ Затримка (перцентилі p95/p99, а не середнє)
2. 📶 Трафік
3. ❌ Помилки
4. 🔋 Насичення (CPU, пам'ять, пул з'єднань)



## 🎯 SLI, SLO, SLA

| Поняття | Що це | Приклад |
| --- | --- | --- |
| **SLI** | вимірюваний показник | частка успішних запитів |
| **SLO** | внутрішня ціль | 99,5 % за 30 днів |
| **SLA** | угода з наслідками | повернення коштів при порушенні |

**💰 Бюджет помилок:** поки не вичерпано — випускаємо нове; вичерпано — пріоритет надійності



## ✅ Висновки та найкращі практики

**🧪 Тестування:** піраміда · AAA · Testcontainers · покриття як індикатор

**🤖 Автоматизація:** конвеєр на кожну зміну · мінімальні права · Dependabot · `npm audit`, CodeQL

**⚙️ Конфігурація:** один код — різні змінні · валідація на старті · секрети поза репозиторієм

**☁️ Розгортання:** Docker · `SIGTERM` · вибір платформи за критеріями

**📈 Моніторинг:** структуровані журнали · live/ready · золоті сигнали · Sentry · SLO

**➡️ Далі:** клієнтська частина — React (лекція 7); ті самі Vitest і конвеєр



## ❓ Запитання та обговорення

1. Чому піраміда тестів саме така?
2. Чим стаб відрізняється від моку?
3. Чому інтеграційні тести краще з реальною базою?
4. Що означає 100 % покриття й чого воно не гарантує?
5. Чим liveness відрізняється від readiness?
6. Що таке SLO і бюджет помилок?
