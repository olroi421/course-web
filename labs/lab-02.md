# Лабораторна робота 02 Аутентифікація, авторизація та розширений функціонал

## 🎯 Мета роботи

Здобути практичні навички реалізації системи аутентифікації та авторизації користувачів, впровадити механізми безпечного зберігання паролів та управління сесіями, розширити функціональність серверного застосунку файловим сервісом, пошуком, фільтрацією та пагінацією, а також створити документацію API.

## ✅ Завдання

### Рівень 1 (обов'язковий мінімум)

1. Реалізувати систему реєстрації користувачів з валідацією даних.
2. Впровадити безпечне зберігання паролів за допомогою Argon2id.
3. Створити систему аутентифікації на основі JWT токенів.
4. Реалізувати middleware для перевірки автентифікації користувачів.
5. Додати базову систему ролей користувачів (наприклад: користувач, адміністратор).
6. Впровадити middleware авторизації для захисту окремих маршрутів.
7. Реалізувати пагінацію для списків даних.
8. Створити базову документацію API за допомогою Swagger.

### Рівень 2

1. Реалізувати файловий сервіс для завантаження та зберігання файлів.
2. Додати валідацію типів та розмірів файлів.
3. Впровадити систему пошуку по основних сутностях предметної області.
4. Реалізувати фільтрацію даних за різними критеріями.
5. Додати можливість оновлення та видалення завантажених файлів.
6. Створити endpoint для зміни паролю користувача.
7. Реалізувати refresh токени для оновлення JWT з можливістю їх відкликання.
8. Написати базові тести для endpoints аутентифікації.

### Рівень 3

1. Впровадити систему відновлення паролю через email.
2. Реалізувати двофакторну аутентифікацію.
3. Додати можливість входу через соціальні мережі (OAuth).
4. Створити систему логування дій користувачів.
5. Реалізувати обмеження частоти запитів (rate limiting) для захисту від зловживань.
6. Впровадити складнішу систему дозволів з детальними правами доступу.
7. Додати можливість завантаження декількох файлів одночасно.
8. Створити розширену документацію API з прикладами використання та опис всіх можливих помилок.


## 📚 Теоретичні відомості

### Аутентифікація та авторизація

**Аутентифікація** (Authentication) — процес перевірки ідентичності користувача, підтвердження того, що користувач є тим, за кого себе видає. Це перший крок у забезпеченні безпеки додатку.

**Авторизація** (Authorization) — процес визначення прав доступу автентифікованого користувача до ресурсів системи. Авторизація завжди відбувається після аутентифікації.

Ключова різниця: аутентифікація відповідає на питання "Хто ви?", а авторизація на питання "Що вам дозволено робити?".

### JWT (JSON Web Token)

**JWT** — це відкритий стандарт для створення токенів доступу, які дозволяють передавати інформацію між сторонами у вигляді JSON об'єкта. JWT складається з трьох частин, розділених крапками:

1. **Header** (заголовок) — містить тип токена та алгоритм підпису.
2. **Payload** (корисне навантаження) — містить claims (твердження) про користувача та додаткові дані.
3. **Signature** (підпис) — використовується для перевірки цілісності токена. Важливо: вміст JWT лише підписаний, але не зашифрований, тому в payload не можна зберігати паролі чи інші конфіденційні дані.

```mermaid
graph LR
    A[Клієнт] -->|1. Login credentials| B[Сервер]
    B -->|2. Validate credentials| C[База даних]
    C -->|3. User data| B
    B -->|4. Generate JWT| D[JWT Token]
    D -->|5. Return token| A
    A -->|6. Request with token| B
    B -->|7. Verify token| B
    B -->|8. Return protected data| A
```

Переваги JWT:

- Компактність та ефективність передачі.
- Самодостатність токена, який містить всю необхідну інформацію.
- Можливість використання на різних платформах.
- Відсутність необхідності зберігати сесії на сервері.


**Access та refresh токени.** Access токен короткоживучий (наприклад, 15 хвилин) і передається з кожним запитом у заголовку `Authorization: Bearer ...`. Оскільки сервер не зберігає його стан, відкликати такий токен до завершення терміну дії неможливо, тому його час життя має бути малим. Refresh токен живе довше (наприклад, 7 днів), використовується лише для отримання нової пари токенів і тому зберігається на сервері у вигляді хешу, що дає змогу його відкликати. Під час кожного оновлення застосовують **ротацію**: старий refresh токен відкликається, а користувач отримує новий. Якщо відкликаний токен пред'являється повторно, це ознака викрадення, і всі токени користувача скасовуються.

### Argon2id

**Argon2id** — сучасна функція хешування паролів, переможець конкурсу Password Hashing Competition, яку OWASP рекомендує як основний вибір для нових систем. На відміну від швидких хеш-функцій загального призначення (SHA-256, MD5), вона навмисно повільна й вимоглива до пам'яті, тому підбір паролів на відеокартах стає надто дорогим. Основні характеристики:

- Використання унікальної солі для кожного пароля, яка зберігається в самому хеші, тож окремого поля в базі даних не потрібно.
- Налаштування вартості обчислень через параметри пам'яті, кількості ітерацій та паралелізму.
- Стійкість до атак підбором завдяки високим вимогам до пам'яті.
- Хеш є рядком формату `$argon2id$v=19$m=...,t=...,p=...$сіль$хеш`, що містить усі параметри, потрібні для перевірки.

Раніше стандартом вважався **bcrypt**. Він і надалі є прийнятною альтернативою (наприклад, у застарілих системах), але для нових проєктів обирають Argon2id. У курсі використовується пакет `argon2`.

Приклад роботи з Argon2id:

```javascript
import argon2 from 'argon2';

// Хешування пароля
export function hashPassword(plainPassword) {
  return argon2.hash(plainPassword, { type: argon2.argon2id });
}

// Перевірка пароля (порядок аргументів: спочатку хеш, потім пароль)
export function verifyPassword(plainPassword, hashedPassword) {
  return argon2.verify(hashedPassword, plainPassword);
}
```

### Система ролей та дозволів

**RBAC (Role-Based Access Control)** — модель контролю доступу, заснована на ролях користувачів. Основні компоненти:

- **Ролі** — набір дозволів, призначених групі користувачів.
- **Дозволи** — конкретні права на виконання операцій.
- **Користувачі** — суб'єкти, яким призначаються ролі.

```mermaid
graph TD
    A[Користувач] -->|має| B[Роль]
    B -->|містить| C[Дозволи]
    C -->|дозволяє| D[Операції]

    E[admin] -->|може| F[Всі операції]
    G[user] -->|може| H[Читання та оновлення власних даних]
    I[guest] -->|може| J[Тільки читання публічних даних]
```


### Файловий сервіс

**Multer** — middleware для Node.js, призначений для обробки multipart/form-data, який використовується для завантаження файлів. У курсі використовується Multer 2.x: гілка 1.x має відомі вразливості (відмова в обслуговуванні через некоректні запити) і більше не підтримується. Основні можливості:

- Завантаження одного або декількох файлів.
- Налаштування місця збереження файлів.
- Фільтрація файлів за типом та розміром.
- Генерація унікальних імен файлів.

Пам'ятайте, що MIME-тип (`file.mimetype`) і ім'я файлу надходять від клієнта і їх легко підробити. Тому перевіряйте одночасно MIME-тип і розширення, ніколи не використовуйте оригінальне ім'я файлу як шлях на диску, обмежуйте розмір і кількість файлів. Для критичних застосунків додатково перевіряють «магічні байти» вмісту файлу.

Приклад базової конфігурації Multer:

```javascript
import path from 'node:path';
import multer from 'multer';

const storage = multer.diskStorage({
  destination: (req, file, cb) => cb(null, 'uploads/'),
  filename: (req, file, cb) => {
    const uniqueSuffix = `${Date.now()}-${Math.round(Math.random() * 1e9)}`;
    cb(null, `${file.fieldname}-${uniqueSuffix}${path.extname(file.originalname)}`);
  },
});

const fileFilter = (req, file, cb) => {
  const allowedTypes = ['image/jpeg', 'image/png', 'image/gif'];
  if (allowedTypes.includes(file.mimetype)) {
    cb(null, true);
  } else {
    cb(new Error('Непідтримуваний тип файлу'));
  }
};

const upload = multer({
  storage,
  fileFilter,
  limits: { fileSize: 5 * 1024 * 1024 },
});
```

### Пошук, фільтрація та пагінація

**Пагінація** — розділення великого набору даних на окремі сторінки для покращення продуктивності та зручності використання. Основні параметри:

- `page` — номер поточної сторінки.
- `limit` — кількість елементів на сторінці.
- `offset` — зміщення від початку набору даних.

**Фільтрація** — обмеження результатів запиту за певними критеріями. Може включати:

- Пошук за текстовими полями.
- Фільтрацію за діапазоном значень.
- Фільтрацію за категоріями або статусами.
- Сортування результатів.

Приклад реалізації пагінації з Prisma:

```javascript
async function getProducts(page = 1, limit = 10, filters = {}) {
    const skip = (page - 1) * limit;

    const where = {};
    if (filters.category) {
        where.category = filters.category;
    }
    if (filters.minPrice) {
        where.price = { gte: parseFloat(filters.minPrice) };
    }
    if (filters.search) {
        where.name = { contains: filters.search, mode: 'insensitive' };
    }

    const [products, total] = await Promise.all([
        prisma.product.findMany({
            where,
            skip,
            take: limit,
            orderBy: { createdAt: 'desc' }
        }),
        prisma.product.count({ where })
    ]);

    return {
        data: products,
        pagination: {
            page,
            limit,
            total,
            totalPages: Math.ceil(total / limit)
        }
    };
}
```

### Swagger та документація API

**Swagger** — набір інструментів для проектування, побудови та документування RESTful API. Основні переваги:

- Автоматична генерація документації з коду.
- Інтерактивний інтерфейс для тестування endpoints.
- Підтримка специфікації OpenAPI.
- Можливість генерації клієнтського коду.

Для Node.js найпопулярнішою бібліотекою є swagger-jsdoc та swagger-ui-express. Приклад базового налаштування:

```javascript
import swaggerJsdoc from 'swagger-jsdoc';
import swaggerUi from 'swagger-ui-express';

const options = {
    definition: {
        openapi: '3.0.0',
        info: {
            title: 'API Documentation',
            version: '1.0.0',
            description: 'Документація API для вебдодатку'
        },
        servers: [
            {
                url: 'http://localhost:3000',
                description: 'Development server'
            }
        ]
    },
    apis: ['./src/routes/*.js']
};

const specs = swaggerJsdoc(options);
app.use('/api-docs', swaggerUi.serve, swaggerUi.setup(specs));
```

Приклад документування endpoint:

```javascript
/**
 * @swagger
 * /api/users/register:
 *   post:
 *     summary: Реєстрація нового користувача
 *     tags: [Authentication]
 *     requestBody:
 *       required: true
 *       content:
 *         application/json:
 *           schema:
 *             type: object
 *             required:
 *               - email
 *               - password
 *               - name
 *             properties:
 *               email:
 *                 type: string
 *                 format: email
 *               password:
 *                 type: string
 *                 minLength: 8
 *               name:
 *                 type: string
 *     responses:
 *       201:
 *         description: Користувач успішно зареєстрований
 *       400:
 *         description: Помилка валідації даних
 *       409:
 *         description: Користувач вже існує
 */
```

### Безпека вебдодатків

Основні принципи безпеки при розробці серверної частини:

1. **Валідація вхідних даних** — завжди перевіряйте та очищайте дані від користувачів.
2. **Захист від SQL-ін'єкцій** — використовуйте ORM або параметризовані запити.
3. **Захист від XSS-атак** — екрануйте вихідні дані.
4. **HTTPS** — завжди використовуйте шифроване з'єднання в production.
5. **Обмеження частоти запитів (rate limiting)** — обмежуйте кількість запитів від одного клієнта.
6. **CORS** — правильно налаштовуйте політику cross-origin запитів.
7. **Helmet** — використовуйте middleware для встановлення безпечних HTTP-заголовків.
8. **Секрети** — ніколи не зберігайте ключі та паролі в репозиторії, тримайте їх у змінних середовища.

Приклад налаштування базової безпеки:

```javascript
import helmet from 'helmet';
import cors from 'cors';
import { rateLimit } from 'express-rate-limit';

app.use(helmet());

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  limit: 100, // у версіях до 7 цей параметр називався max
  standardHeaders: 'draft-8',
  legacyHeaders: false,
  message: { error: 'Забагато запитів з цієї IP-адреси' },
});

app.use('/api/', limiter);

app.use(cors({
  origin: process.env.FRONTEND_URL,
  credentials: true,
}));
```

## 🔗 Додаткові ресурси

- [JWT офіційна документація](https://jwt.io/introduction)
- [OWASP: Password Storage Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html)
- [Argon2 (пакет argon2)](https://www.npmjs.com/package/argon2)
- [Multer документація](https://www.npmjs.com/package/multer)
- [Prisma 7: документація](https://www.prisma.io/docs)
- [Express 5: міграція з версії 4](https://expressjs.com/en/guide/migrating-5.html)
- [Swagger документація](https://swagger.io/docs/)
- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [Express security best practices](https://expressjs.com/en/advanced/best-practice-security.html)

## ▶️ Хід роботи

[:fontawesome-brands-github: Приклад реалізації лабораторної роботи](https://github.com/olroi421/course-web-project){ .md-button }

### Крок 1. Підготовка середовища

1. Переконайтеся, що проєкт з лабораторної роботи 1 працює коректно, а встановлена версія Node.js — 24 LTS (`node -v`).
2. Перейдіть на ES-модулі. Prisma 7 постачається лише як ES-модуль, і це ж основний формат модулів у курсі (див. лекцію 02). У файлі `package.json` додайте `"type": "module"`, замініть у коді `require(...)` на `import ...`, а `module.exports` на `export`. У локальних імпортах вказуйте розширення файлу (`'./app.js'`). Замість `nodemon` використовуйте вбудований режим спостереження Node.js, а змінні середовища підвантажуйте прапорцем `--env-file`:
    ```json
    {
      "type": "module",
      "scripts": {
        "dev": "node --env-file=.env --watch src/server.js",
        "start": "node src/server.js",
        "test": "node --env-file=.env --test \"tests/**/*.test.js\""
      }
    }
    ```
3. Встановіть Express 5 (якщо в проєкті лабораторної роботи 1 ще версія 4) та додаткові залежності:
    ```bash
    npm install express@5 jsonwebtoken argon2 multer@2 swagger-jsdoc swagger-ui-express helmet express-rate-limit cors
    npm install @prisma/client@7 @prisma/adapter-pg pg dotenv
    npm install --save-dev prisma@7 supertest
    ```
    Зверніть увагу: `multer@2` — обов'язково. Версія 1.x має відомі вразливості й позначена як застаріла.
4. Створіть змінні середовища у файлі `.env` (файл не можна додавати до репозиторію). Секрети згенеруйте командою `node -p "require('node:crypto').randomBytes(32).toString('hex')"`:
    ```
    DATABASE_URL="postgresql://user:password@localhost:5432/mydb"
    JWT_SECRET=згенерований-секрет-1
    JWT_EXPIRES_IN=15m
    REFRESH_TOKEN_SECRET=згенерований-секрет-2
    REFRESH_TOKEN_EXPIRES_IN=7d
    UPLOAD_DIR=uploads
    MAX_FILE_SIZE=5242880
    FRONTEND_URL=http://localhost:5173
    ```
5. Додайте до `.gitignore` рядки `.env`, `uploads/` та `src/generated/`.

### Крок 2. Оновлення моделей даних

1. Prisma 7 змінила спосіб налаштування. Якщо в лабораторній роботі 1 використовувалася Prisma 6, оновіть файл `prisma/schema.prisma`: генератор `prisma-client-js` замініть на `prisma-client` із явним шляхом `output`, а рядок `url = env("DATABASE_URL")` з блоку `datasource` приберіть (адресу бази тепер задає окремий файл конфігурації). Додайте поля для аутентифікації та файлів і модель для refresh токенів:
    ```prisma
    generator client {
      provider = "prisma-client"
      output   = "../src/generated/prisma"
    }

    datasource db {
      provider = "postgresql"
    }

    enum Role {
      USER
      ADMIN
      MODERATOR
    }

    model User {
      id            Int            @id @default(autoincrement())
      email         String         @unique
      password      String
      name          String
      role          Role           @default(USER)
      avatar        String?
      createdAt     DateTime       @default(now())
      updatedAt     DateTime       @updatedAt
      files         File[]
      refreshTokens RefreshToken[]
    }

    model File {
      id           Int      @id @default(autoincrement())
      filename     String
      originalName String
      mimetype     String
      size         Int
      path         String
      uploadedBy   Int
      createdAt    DateTime @default(now())
      user         User     @relation(fields: [uploadedBy], references: [id], onDelete: Cascade)

      @@index([uploadedBy])
    }

    model RefreshToken {
      id        Int      @id @default(autoincrement())
      tokenHash String   @unique
      userId    Int
      expiresAt DateTime
      revokedAt DateTime?
      user      User     @relation(fields: [userId], references: [id], onDelete: Cascade)

      @@index([userId])
    }
    ```
2. Створіть у корені проєкту файл `prisma.config.ts` з адресою бази даних та розташуванням міграцій:
    ```typescript
    import 'dotenv/config';
    import { defineConfig, env } from 'prisma/config';

    export default defineConfig({
      schema: 'prisma/schema.prisma',
      migrations: { path: 'prisma/migrations' },
      datasource: { url: env('DATABASE_URL') },
    });
    ```
3. Виконайте міграцію бази даних і згенеруйте клієнт. У Prisma 7 генерація клієнта не запускається автоматично після міграції, тому її потрібно виконувати окремою командою:
    ```bash
    npx prisma migrate dev --name add-auth-and-files
    npx prisma generate
    ```
4. Створіть файл `src/db.js` з єдиним екземпляром клієнта. Починаючи з Prisma 7, клієнт створюється разом з адаптером драйвера бази даних, а згенерований клієнт лежить у вашому проєкті (це TypeScript-файли, які Node.js 24 виконує без окремої компіляції):
    ```javascript
    import { PrismaPg } from '@prisma/adapter-pg';
    import { PrismaClient } from './generated/prisma/client.ts';

    const adapter = new PrismaPg({ connectionString: process.env.DATABASE_URL });

    export const prisma = new PrismaClient({ adapter });
    ```
    Далі скрізь використовуйте `import { prisma } from '../db.js'` замість створення `new PrismaClient()` у кожному контролері.

### Крок 3. Створення utility функцій
1. Створіть файл `src/utils/jwt.js` для роботи з JWT. Для refresh токена додано поле `jti`, яке робить кожен токен унікальним:
    ```javascript
    import { randomUUID } from 'node:crypto';
    import jwt from 'jsonwebtoken';

    export function generateAccessToken(userId, role) {
      return jwt.sign({ userId, role }, process.env.JWT_SECRET, {
        expiresIn: process.env.JWT_EXPIRES_IN,
      });
    }

    export function generateRefreshToken(userId) {
      // jti робить кожен refresh токен унікальним, навіть якщо їх видано в одну секунду
      return jwt.sign({ userId, jti: randomUUID() }, process.env.REFRESH_TOKEN_SECRET, {
        expiresIn: process.env.REFRESH_TOKEN_EXPIRES_IN,
      });
    }

    export const verifyAccessToken = (token) => jwt.verify(token, process.env.JWT_SECRET);

    export const verifyRefreshToken = (token) => jwt.verify(token, process.env.REFRESH_TOKEN_SECRET);
    ```
2. Створіть файл `src/utils/password.js` для роботи з паролями:
    ```javascript
    import argon2 from 'argon2';

    export function hashPassword(password) {
      // Argon2id із параметрами за замовчуванням (рекомендація OWASP)
      return argon2.hash(password, { type: argon2.argon2id });
    }

    export function comparePassword(password, hash) {
      return argon2.verify(hash, password);
    }
    ```

### Крок 4. Створення middleware

1. Створіть файл `src/middleware/auth.js` для аутентифікації:
    ```javascript
    import { verifyAccessToken } from '../utils/jwt.js';

    export function authenticate(req, res, next) {
      const authHeader = req.headers.authorization;

      if (!authHeader?.startsWith('Bearer ')) {
        return res.status(401).json({ error: 'Токен аутентифікації відсутній' });
      }

      try {
        req.user = verifyAccessToken(authHeader.slice(7));
        next();
      } catch {
        res.status(401).json({ error: 'Недійсний або прострочений токен' });
      }
    }
    ```
2. Створіть файл `src/middleware/authorize.js` для авторизації:
    ```javascript
    export function authorize(...allowedRoles) {
      return (req, res, next) => {
        if (!req.user) {
          return res.status(401).json({ error: 'Необхідна аутентифікація' });
        }

        if (!allowedRoles.includes(req.user.role)) {
          return res.status(403).json({ error: 'Недостатньо прав для виконання цієї операції' });
        }

        next();
      };
    }
    ```
3. Створіть файл `src/middleware/upload.js` для завантаження файлів. Тип файлу перевіряється за MIME-типом і розширенням одночасно:
    ```javascript
    import fs from 'node:fs';
    import path from 'node:path';
    import multer from 'multer';

    const uploadDir = process.env.UPLOAD_DIR || 'uploads';
    fs.mkdirSync(uploadDir, { recursive: true });

    // Дозволені типи: MIME-тип і розширення мають збігатися
    const allowedTypes = new Map([
      ['image/jpeg', ['.jpg', '.jpeg']],
      ['image/png', ['.png']],
      ['image/gif', ['.gif']],
      ['application/pdf', ['.pdf']],
    ]);

    const storage = multer.diskStorage({
      destination: (req, file, cb) => cb(null, uploadDir),
      filename: (req, file, cb) => {
        const uniqueSuffix = `${Date.now()}-${Math.round(Math.random() * 1e9)}`;
        const ext = path.extname(file.originalname).toLowerCase();
        cb(null, `${file.fieldname}-${uniqueSuffix}${ext}`);
      },
    });

    const fileFilter = (req, file, cb) => {
      const ext = path.extname(file.originalname).toLowerCase();
      if (allowedTypes.get(file.mimetype)?.includes(ext)) {
        cb(null, true);
      } else {
        const error = new Error('Непідтримуваний тип файлу');
        error.status = 400;
        cb(error);
      }
    };

    export const upload = multer({
      storage,
      fileFilter,
      limits: { fileSize: Number(process.env.MAX_FILE_SIZE) || 5 * 1024 * 1024, files: 1 },
    });
    ```

### Крок 5. Реалізація контролерів аутентифікації
1. Створіть файл `src/controllers/authController.js`. У Express 5 помилки з асинхронних обробників потрапляють до централізованого обробника помилок автоматично, тому блоки `try/catch` у кожному контролері не потрібні. Refresh токени зберігаються в базі даних у вигляді SHA-256 хешу: це дозволяє відкликати їх та виконувати ротацію:
    ```javascript
    import { createHash } from 'node:crypto';
    import { prisma } from '../db.js';
    import { hashPassword, comparePassword } from '../utils/password.js';
    import {
      generateAccessToken,
      generateRefreshToken,
      verifyRefreshToken,
    } from '../utils/jwt.js';

    const sha256 = (value) => createHash('sha256').update(value).digest('hex');

    // Створює пару токенів і зберігає хеш refresh токена в БД, щоб його можна було відкликати
    async function issueTokens(user) {
      const accessToken = generateAccessToken(user.id, user.role);
      const refreshToken = generateRefreshToken(user.id);
      const { exp } = verifyRefreshToken(refreshToken);

      await prisma.refreshToken.create({
        data: {
          tokenHash: sha256(refreshToken),
          userId: user.id,
          expiresAt: new Date(exp * 1000),
        },
      });

      return { accessToken, refreshToken };
    }

    export async function register(req, res) {
      const { email, password, name } = req.body ?? {};

      if (!email || !password || !name) {
        return res.status(400).json({ error: "Email, пароль та ім'я є обов'язковими" });
      }

      if (password.length < 8) {
        return res.status(400).json({ error: 'Пароль має містити мінімум 8 символів' });
      }

      const existingUser = await prisma.user.findUnique({ where: { email } });

      if (existingUser) {
        return res.status(409).json({ error: 'Користувач з таким email вже існує' });
      }

      const user = await prisma.user.create({
        data: { email, password: await hashPassword(password), name },
        select: { id: true, email: true, name: true, role: true, createdAt: true },
      });

      res.status(201).json({
        message: 'Користувача успішно зареєстровано',
        user,
        tokens: await issueTokens(user),
      });
    }

    export async function login(req, res) {
      const { email, password } = req.body ?? {};

      if (!email || !password) {
        return res.status(400).json({ error: "Email та пароль є обов'язковими" });
      }

      const user = await prisma.user.findUnique({ where: { email } });

      // Однакова відповідь для неіснуючого email і хибного пароля
      if (!user || !(await comparePassword(password, user.password))) {
        return res.status(401).json({ error: 'Невірний email або пароль' });
      }

      const { password: _password, ...userWithoutPassword } = user;

      res.json({
        message: 'Успішний вхід',
        user: userWithoutPassword,
        tokens: await issueTokens(user),
      });
    }

    export async function refresh(req, res) {
      const { refreshToken } = req.body ?? {};

      if (!refreshToken) {
        return res.status(400).json({ error: 'Refresh токен відсутній' });
      }

      try {
        verifyRefreshToken(refreshToken);
      } catch {
        return res.status(401).json({ error: 'Недійсний refresh токен' });
      }

      const stored = await prisma.refreshToken.findUnique({
        where: { tokenHash: sha256(refreshToken) },
        include: { user: true },
      });

      if (!stored || stored.revokedAt) {
        // Повторне використання відкликаного токена: відкликаємо всі токени користувача
        if (stored) {
          await prisma.refreshToken.updateMany({
            where: { userId: stored.userId, revokedAt: null },
            data: { revokedAt: new Date() },
          });
        }
        return res.status(401).json({ error: 'Недійсний refresh токен' });
      }

      // Ротація: старий токен відкликаємо, видаємо нову пару
      await prisma.refreshToken.update({
        where: { id: stored.id },
        data: { revokedAt: new Date() },
      });

      res.json({ tokens: await issueTokens(stored.user) });
    }

    export async function logout(req, res) {
      const { refreshToken } = req.body ?? {};

      if (refreshToken) {
        await prisma.refreshToken.updateMany({
          where: { tokenHash: sha256(refreshToken), revokedAt: null },
          data: { revokedAt: new Date() },
        });
      }

      res.status(204).end();
    }
    ```

### Крок 6. Додавання пошуку та фільтрації
1. Розширте контролери основних сутностей методами пошуку та фільтрації. Наприклад, для продуктів (замініть `product` на сутність вашої предметної області). Зверніть увагу на два захисти: обмеження `limit` та білий список полів сортування, щоб клієнт не міг передати довільне значення:
    ```javascript
    import { prisma } from '../db.js';

    const SORTABLE_FIELDS = ['createdAt', 'name', 'price'];

    export async function getProducts(req, res) {
      const {
        page = 1,
        limit = 10,
        search,
        category,
        minPrice,
        maxPrice,
        sortBy = 'createdAt',
        order = 'desc',
      } = req.query;

      const pageNumber = Math.max(Number(page) || 1, 1);
      const take = Math.min(Math.max(Number(limit) || 10, 1), 100);
      const skip = (pageNumber - 1) * take;

      const where = {};

      if (search) {
        where.OR = [
          { name: { contains: search, mode: 'insensitive' } },
          { description: { contains: search, mode: 'insensitive' } },
        ];
      }

      if (category) {
        where.category = category;
      }

      if (minPrice || maxPrice) {
        where.price = {};
        if (minPrice) where.price.gte = parseFloat(minPrice);
        if (maxPrice) where.price.lte = parseFloat(maxPrice);
      }

      const orderField = SORTABLE_FIELDS.includes(sortBy) ? sortBy : 'createdAt';
      const orderDirection = order === 'asc' ? 'asc' : 'desc';

      const [products, total] = await Promise.all([
        prisma.product.findMany({
          where,
          skip,
          take,
          orderBy: { [orderField]: orderDirection },
        }),
        prisma.product.count({ where }),
      ]);

      res.json({
        data: products,
        pagination: {
          page: pageNumber,
          limit: take,
          total,
          totalPages: Math.ceil(total / take),
          hasMore: skip + products.length < total,
        },
      });
    }
    ```

### Крок 7. Реалізація файлового сервісу

1. Створіть файл `src/controllers/fileController.js`:
    ```javascript
    import fs from 'node:fs/promises';
    import path from 'node:path';
    import { prisma } from '../db.js';

    export async function uploadFile(req, res) {
      if (!req.file) {
        return res.status(400).json({ error: 'Файл не надано' });
      }

      try {
        const file = await prisma.file.create({
          data: {
            filename: req.file.filename,
            originalName: req.file.originalname,
            mimetype: req.file.mimetype,
            size: req.file.size,
            path: req.file.path,
            uploadedBy: req.user.userId,
          },
        });

        res.status(201).json({ message: 'Файл успішно завантажено', file });
      } catch (error) {
        // Якщо запис у БД не вдався, не залишаємо «осиротілий» файл на диску
        await fs.unlink(req.file.path).catch(() => {});
        throw error;
      }
    }

    export async function getFile(req, res) {
      const file = await prisma.file.findUnique({ where: { id: Number(req.params.id) } });

      if (!file) {
        return res.status(404).json({ error: 'Файл не знайдено' });
      }

      res.sendFile(path.resolve(file.path));
    }

    export async function deleteFile(req, res) {
      const file = await prisma.file.findUnique({ where: { id: Number(req.params.id) } });

      if (!file) {
        return res.status(404).json({ error: 'Файл не знайдено' });
      }

      if (file.uploadedBy !== req.user.userId && req.user.role !== 'ADMIN') {
        return res.status(403).json({ error: 'Ви не маєте прав для видалення цього файлу' });
      }

      await prisma.file.delete({ where: { id: file.id } });
      await fs.unlink(file.path).catch(() => {});

      res.json({ message: 'Файл успішно видалено' });
    }
    ```

### Крок 8. Налаштування маршрутів

1. Створіть файл `src/routes/authRoutes.js`:
    ```javascript
    import { Router } from 'express';
    import { register, login, refresh, logout } from '../controllers/authController.js';

    const router = Router();

    /**
     * @swagger
     * /api/auth/register:
     *   post:
     *     summary: Реєстрація нового користувача
     *     tags: [Authentication]
     *     requestBody:
     *       required: true
     *       content:
     *         application/json:
     *           schema:
     *             type: object
     *             required: [email, password, name]
     *             properties:
     *               email: { type: string, format: email }
     *               password: { type: string, minLength: 8 }
     *               name: { type: string }
     *     responses:
     *       201: { description: Користувач успішно зареєстрований }
     *       400: { description: Помилка валідації даних }
     *       409: { description: Користувач вже існує }
     */
    router.post('/register', register);

    /**
     * @swagger
     * /api/auth/login:
     *   post:
     *     summary: Вхід користувача
     *     tags: [Authentication]
     *     requestBody:
     *       required: true
     *       content:
     *         application/json:
     *           schema:
     *             type: object
     *             required: [email, password]
     *             properties:
     *               email: { type: string, format: email }
     *               password: { type: string }
     *     responses:
     *       200: { description: Успішний вхід }
     *       401: { description: Невірні дані для входу }
     */
    router.post('/login', login);

    /**
     * @swagger
     * /api/auth/refresh:
     *   post:
     *     summary: Оновлення пари токенів (ротація refresh токена)
     *     tags: [Authentication]
     *     requestBody:
     *       required: true
     *       content:
     *         application/json:
     *           schema:
     *             type: object
     *             required: [refreshToken]
     *             properties:
     *               refreshToken: { type: string }
     *     responses:
     *       200: { description: Токени успішно оновлено }
     *       401: { description: Недійсний або відкликаний refresh токен }
     */
    router.post('/refresh', refresh);

    /**
     * @swagger
     * /api/auth/logout:
     *   post:
     *     summary: Вихід (відкликання refresh токена)
     *     tags: [Authentication]
     *     requestBody:
     *       content:
     *         application/json:
     *           schema:
     *             type: object
     *             properties:
     *               refreshToken: { type: string }
     *     responses:
     *       204: { description: Токен відкликано }
     */
    router.post('/logout', logout);

    export default router;
    ```
2. Створіть файл `src/routes/fileRoutes.js`:
    ```javascript
    import { Router } from 'express';
    import { uploadFile, getFile, deleteFile } from '../controllers/fileController.js';
    import { authenticate } from '../middleware/auth.js';
    import { upload } from '../middleware/upload.js';

    const router = Router();

    /**
     * @swagger
     * /api/files/upload:
     *   post:
     *     summary: Завантаження файлу
     *     tags: [Files]
     *     security:
     *       - bearerAuth: []
     *     requestBody:
     *       required: true
     *       content:
     *         multipart/form-data:
     *           schema:
     *             type: object
     *             properties:
     *               file: { type: string, format: binary }
     *     responses:
     *       201: { description: Файл успішно завантажено }
     *       400: { description: Файл не надано або невалідний }
     *       401: { description: Необхідна аутентифікація }
     */
    router.post('/upload', authenticate, upload.single('file'), uploadFile);

    /**
     * @swagger
     * /api/files/{id}:
     *   get:
     *     summary: Отримання файлу за ID
     *     tags: [Files]
     *     parameters:
     *       - { in: path, name: id, required: true, schema: { type: integer } }
     *     responses:
     *       200: { description: Файл успішно отримано }
     *       404: { description: Файл не знайдено }
     */
    router.get('/:id', getFile);

    /**
     * @swagger
     * /api/files/{id}:
     *   delete:
     *     summary: Видалення файлу
     *     tags: [Files]
     *     security:
     *       - bearerAuth: []
     *     parameters:
     *       - { in: path, name: id, required: true, schema: { type: integer } }
     *     responses:
     *       200: { description: Файл успішно видалено }
     *       403: { description: Недостатньо прав }
     *       404: { description: Файл не знайдено }
     */
    router.delete('/:id', authenticate, deleteFile);

    export default router;
    ```
3. Оновіть існуючі маршрути, додавши захист через middleware. Наприклад, для продуктів:
    ```javascript
    import { Router } from 'express';
    import {
      getProducts,
      getProductById,
      createProduct,
      updateProduct,
      deleteProduct,
    } from '../controllers/productController.js';
    import { authenticate } from '../middleware/auth.js';
    import { authorize } from '../middleware/authorize.js';

    const router = Router();

    router.get('/', getProducts);
    router.get('/:id', getProductById);
    router.post('/', authenticate, authorize('ADMIN', 'MODERATOR'), createProduct);
    router.put('/:id', authenticate, authorize('ADMIN', 'MODERATOR'), updateProduct);
    router.delete('/:id', authenticate, authorize('ADMIN'), deleteProduct);

    export default router;
    ```

### Крок 9. Налаштування Swagger

1. Створіть файл `src/config/swagger.js`:
    ```javascript
    import swaggerJsdoc from 'swagger-jsdoc';

    const options = {
      definition: {
        openapi: '3.0.0',
        info: {
          title: 'API Documentation',
          version: '1.0.0',
          description: 'Документація API для вебзастосунку',
        },
        servers: [{ url: 'http://localhost:3000', description: 'Development server' }],
        components: {
          securitySchemes: {
            bearerAuth: { type: 'http', scheme: 'bearer', bearerFormat: 'JWT' },
          },
        },
      },
      apis: ['./src/routes/*.js'],
    };

    export const swaggerSpecs = swaggerJsdoc(options);
    ```
2. Створіть файл `src/app.js`. Він лише збирає застосунок і **не запускає сервер**: це дозволяє імпортувати `app` у тестах без відкриття порту. Підключіть також маршрути ваших сутностей (наприклад, `app.use('/api/products', productRoutes)`):
    ```javascript
    import express from 'express';
    import helmet from 'helmet';
    import cors from 'cors';
    import { rateLimit } from 'express-rate-limit';
    import swaggerUi from 'swagger-ui-express';
    import { swaggerSpecs } from './config/swagger.js';
    import authRoutes from './routes/authRoutes.js';
    import fileRoutes from './routes/fileRoutes.js';

    const app = express();

    app.use(helmet());

    app.use(
      cors({
        origin: process.env.FRONTEND_URL || 'http://localhost:5173',
        credentials: true,
      })
    );

    app.use(
      '/api/',
      rateLimit({
        windowMs: 15 * 60 * 1000,
        limit: Number(process.env.RATE_LIMIT) || 100,
        standardHeaders: 'draft-8',
        legacyHeaders: false,
        message: { error: 'Забагато запитів з цієї IP-адреси, спробуйте пізніше' },
      })
    );

    app.use(express.json());

    app.use('/api-docs', swaggerUi.serve, swaggerUi.setup(swaggerSpecs, { explorer: true }));

    app.use('/api/auth', authRoutes);
    app.use('/api/files', fileRoutes);

    // У Express 5 помилки з async-обробників потрапляють сюди автоматично
    app.use((err, req, res, next) => {
      if (err.name === 'MulterError') {
        const error = err.code === 'LIMIT_FILE_SIZE' ? 'Файл занадто великий' : 'Помилка завантаження файлу';
        return res.status(400).json({ error });
      }

      const status = err.status || 500;
      if (status >= 500) console.error(err);

      res.status(status).json({ error: status >= 500 ? 'Внутрішня помилка сервера' : err.message });
    });

    export default app;
    ```
3. Створіть файл `src/server.js`, який запускає сервер:
    ```javascript
    import app from './app.js';

    const PORT = process.env.PORT || 3000;

    app.listen(PORT, () => {
      console.log(`Сервер запущено на порту ${PORT}`);
      console.log(`Документація API: http://localhost:${PORT}/api-docs`);
    });
    ```

### Крок 10. Тестування API

1. Створіть файл `tests/auth.test.js`. Для тестів використовується вбудований у Node.js тестовий раннер `node:test` та бібліотека `supertest`, тому окремий фреймворк (Jest) не потрібен. Тести працюють із реальною базою даних, тож для них бажано створити окрему базу й окремий файл змінних середовища:
    ```javascript
    import { test, describe, before, after } from 'node:test';
    import assert from 'node:assert/strict';
    import request from 'supertest';
    import app from '../src/app.js';
    import { prisma } from '../src/db.js';

    const user = { email: 'test@example.com', password: 'testpassword123', name: 'Test User' };

    describe('Authentication API', () => {
      before(() => prisma.user.deleteMany({ where: { email: user.email } }));

      after(async () => {
        await prisma.user.deleteMany({ where: { email: user.email } });
        await prisma.$disconnect();
      });

      test('реєструє нового користувача', async () => {
        const res = await request(app).post('/api/auth/register').send(user);

        assert.equal(res.status, 201);
        assert.equal(res.body.user.email, user.email);
        assert.ok(res.body.tokens.accessToken);
        assert.equal(res.body.user.password, undefined);
      });

      test('не реєструє користувача з існуючим email', async () => {
        const res = await request(app).post('/api/auth/register').send(user);
        assert.equal(res.status, 409);
      });

      test('не реєструє користувача з коротким паролем', async () => {
        const res = await request(app)
          .post('/api/auth/register')
          .send({ ...user, email: 'test2@example.com', password: 'short' });
        assert.equal(res.status, 400);
      });

      test('входить із правильними даними', async () => {
        const res = await request(app)
          .post('/api/auth/login')
          .send({ email: user.email, password: user.password });

        assert.equal(res.status, 200);
        assert.ok(res.body.tokens.accessToken);
        assert.ok(res.body.tokens.refreshToken);
      });

      test('не входить із хибним паролем', async () => {
        const res = await request(app)
          .post('/api/auth/login')
          .send({ email: user.email, password: 'wrongpassword' });
        assert.equal(res.status, 401);
      });

      test('refresh: ротація токена та відкликання старого', async () => {
        const login = await request(app)
          .post('/api/auth/login')
          .send({ email: user.email, password: user.password });
        const { refreshToken } = login.body.tokens;

        const first = await request(app).post('/api/auth/refresh').send({ refreshToken });
        assert.equal(first.status, 200);
        assert.notEqual(first.body.tokens.refreshToken, refreshToken);

        // Старий токен вже відкликано
        const reuse = await request(app).post('/api/auth/refresh').send({ refreshToken });
        assert.equal(reuse.status, 401);

        // Повторне використання відкликаного токена скасовує й нові токени користувача
        const afterReuse = await request(app)
          .post('/api/auth/refresh')
          .send({ refreshToken: first.body.tokens.refreshToken });
        assert.equal(afterReuse.status, 401);
      });

      test('logout відкликає refresh токен', async () => {
        const login = await request(app)
          .post('/api/auth/login')
          .send({ email: user.email, password: user.password });
        const { refreshToken } = login.body.tokens;

        assert.equal((await request(app).post('/api/auth/logout').send({ refreshToken })).status, 204);
        assert.equal((await request(app).post('/api/auth/refresh').send({ refreshToken })).status, 401);
      });
    });

    describe('Files API', () => {
      let token;
      const fileUser = { email: 'files@example.com', password: 'testpassword123', name: 'Files' };
      const png = Buffer.from('89504e470d0a1a0a', 'hex');

      before(async () => {
        await prisma.user.deleteMany({ where: { email: fileUser.email } });
        const res = await request(app).post('/api/auth/register').send(fileUser);
        token = res.body.tokens.accessToken;
      });

      after(async () => {
        await prisma.user.deleteMany({ where: { email: fileUser.email } });
      });

      test('без токена 401', async () => {
        assert.equal((await request(app).post('/api/files/upload')).status, 401);
      });

      test('завантажує PNG, відхиляє .exe та завеликий файл', async () => {
        const auth = { Authorization: `Bearer ${token}` };

        const ok = await request(app)
          .post('/api/files/upload')
          .set(auth)
          .attach('file', png, { filename: 'a.png', contentType: 'image/png' });
        assert.equal(ok.status, 201);

        const bad = await request(app)
          .post('/api/files/upload')
          .set(auth)
          .attach('file', png, { filename: 'a.exe', contentType: 'application/x-msdownload' });
        assert.equal(bad.status, 400);

        const big = await request(app)
          .post('/api/files/upload')
          .set(auth)
          .attach('file', Buffer.alloc(5 * 1024 * 1024 + 1), { filename: 'b.png', contentType: 'image/png' });
        assert.equal(big.status, 400);
        assert.equal(big.body.error, 'Файл занадто великий');

        const get = await request(app).get(`/api/files/${ok.body.file.id}`);
        assert.equal(get.status, 200);

        const del = await request(app).delete(`/api/files/${ok.body.file.id}`).set(auth);
        assert.equal(del.status, 200);
      });
    });
    ```
2. Запустіть тести командою `npm test`. Скрипт `test` було додано до `package.json` у кроці 1.

### Крок 11. Створення звіту

1. Скопіюйте шаблон звіту до папки `/reports/` ([завантажити шаблон](assets/lab2-report.md)).
2. Заповніть всі розділи звіту та зробіть скріншоти.

### Крок 12. Фіналізація та здача роботи

1. Переконайтеся, що всі endpoints працюють коректно.
2. Перевірте, що документація Swagger доступна за адресою `http://localhost:3000/api-docs`.
3. Створіть коміт з усіма змінами:
    ```bash
    git add .
    git commit -m "feat: додано аутентифікацію, авторизацію, файловий сервіс та документацію API"
    git push origin main
    ```
4. Здайте роботу на Moodle, вставивши посилання на GitHub репозиторій.

[⬆️ Здати лабораторну роботу](https://moodle.vcolnuft.volyn.ua/moodle/course/view.php?id=17#section-2)



## 📝 Критерії оцінювання

### Середній рівень (оцінка "задовільно")

- Реалізовано базову реєстрацію та вхід користувачів.
- Створено JWT токени для аутентифікації.
- Додано middleware для перевірки автентифікації.
- Реалізовано базову пагінацію.
- Створено мінімальну документацію Swagger.
- Код працює, але має значні недоліки в безпеці або структурі.
- Відсутня або неповна валідація даних.
- Базове розуміння принципів аутентифікації та авторизації.

### Достатній рівень (оцінка "добре")

- Виконано всі завдання рівня 1.
- Реалізовано систему ролей користувачів.
- Додано файловий сервіс з валідацією.
- Впроваджено пошук та фільтрацію даних.
- Створено refresh токени.
- Код добре структурований з належною обробкою помилок.
- Документація API містить більшість endpoints з прикладами.
- Написано базові тести для ключових функцій.
- Показано розуміння принципів безпеки вебдодатків.

### Високий рівень (оцінка "відмінно")

- Виконано всі завдання рівнів 1 та 2.
- Реалізовано додаткові функції з рівня 3 (наприклад, відновлення пароля, двофакторна аутентифікація).
- Впроваджено комплексну систему безпеки з helmet, rate limiting, CORS.
- Створено детальну та професійну документацію API з прикладами для всіх endpoints.
- Написано повний набір тестів з високим покриттям коду.
- Код написаний якісно з дотриманням принципів SOLID та чистого коду.
- Реалізовано систему логування дій користувачів.
- Продемонстровано глибоке розуміння аутентифікації, авторизації та безпеки під час захисту.
- Додано власні інноваційні рішення або покращення.

## ⏰ Політика щодо дедлайнів

При порушенні встановленого терміну здачі лабораторної роботи максимальна можлива оцінка становить "добре", незалежно від якості виконаної роботи. Винятки можливі лише за поважних причин, підтверджених документально.

## ❓ Контрольні запитання

1. Поясніть різницю між аутентифікацією та авторизацією. Наведіть приклади з вашого проєкту.
2. Що таке JWT токен і з яких частин він складається? Чому JWT є популярним для аутентифікації в API?
3. Чим Argon2id відрізняється від швидких хеш-функцій (наприклад, SHA-256) і чому він безпечніший для зберігання паролів? Що таке сіль (salt) і навіщо вона потрібна?
4. Поясніть принцип роботи middleware в Express.js. Як ви використали middleware для аутентифікації та авторизації?
5. Що таке RBAC (Role-Based Access Control) і як ви реалізували систему ролей у своєму проєкті?
6. Як працює Multer для завантаження файлів? Які обмеження та валідації ви застосували?
7. Поясніть, як реалізовано пагінацію в вашому проєкті. Які параметри використовуються?
8. Що таке Swagger і для чого він використовується? Які переваги автоматичної документації API?
9. Які основні загрози безпеки існують для вебдодатків? Як ви їх мінімізували в своєму проєкті?
10. Поясніть різницю між access токеном та refresh токеном. Навіщо потрібні два типи токенів і що таке ротація refresh токенів?