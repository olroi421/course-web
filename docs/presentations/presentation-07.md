# Аутентифікація та безпека

## 🔐 Аутентифікація vs Авторизація

```mermaid
graph LR
    A[Користувач] --> B{Аутентифікація}
    B --> |"Хто ви?"| C[Перевірка особи]
    C --> D{Авторизація}
    D --> |"Що можете робити?"| E[Перевірка дозволів]
    E --> F[Доступ до ресурсів]

    style B fill:#ff9999
    style D fill:#99ccff
```

### **Аутентифікація** = "Хто ви?"

### **Авторизація** = "Що ви можете робити?"

## ⚖️ Сесії vs Токени

### 🏪 Сесійна аутентифікація (Stateful)

```mermaid
sequenceDiagram
    participant C as Client
    participant S as Server
    participant DB as Session Store

    C->>S: POST /login
    S->>DB: Створити сесію
    DB-->>S: Session ID
    S-->>C: Set-Cookie: sessionId

    C->>S: GET /protected
    S->>DB: Перевірити сесію
    DB-->>S: Дані сесії
    S-->>C: Захищений ресурс
```

## 🎫 JWT токени (Stateless)

```mermaid
sequenceDiagram
    participant C as Client
    participant AS as Auth Server
    participant RS as Resource Server

    C->>AS: POST /login
    AS-->>C: JWT Token

    C->>RS: GET /protected + JWT
    RS->>RS: Перевірити підпис
    RS-->>C: Захищений ресурс
```

### JWT структура: **Header.Payload.Signature**

## 🔍 Як сервер перевіряє справжність JWT?

### **Ключовий принцип: сервер повторює підпис!**

```mermaid
graph TD
    A[Отримує токен] --> B[Розкладає на частини]
    B --> C[header.payload.signature]

    C --> D[Бере header + payload]
    D --> E[Підписує СВОЇМ ключем]
    E --> F[Новий підпис]

    F --> G{Підписи збігаються?}
    G -->|✅ Так| H[Токен справжній]
    G -->|❌ Ні| I[Токен підроблений]

    style H fill:#90ee90
    style I fill:#ffcccb
```

## 🔐 Процес перевірки токена

```javascript
function verifyToken(token, secret) {
    // 1. Розкладає токен
    const [header, payload, receivedSignature] = token.split('.');

    // 2. Бере дані для підпису
    const dataToSign = header + '.' + payload;

    // 3. ПІДПИСУЄ тими самими даними
    const mySignature = HMAC_SHA256(dataToSign, secret);

    // 4. Порівнює підписи
    if (mySignature === receivedSignature) {
        return "✅ Токен справжній!";
    } else {
        throw "❌ Токен підроблений!";
    }
}
```

## 🏛️ Аналогія з печаткою

```
┌─────────────────────────────┐
│ ПОСВІДЧЕННЯ ОСОБИ          │
│ ─────────────────────────── │ ← header
│ Ім'я: Іван Петренко        │ ← payload
│ ID: 123, Роль: admin       │
│ ─────────────────────────── │
│     [ПЕЧАТКА СЕРВЕРА] 🏛️   │ ← signature
└─────────────────────────────┘
```

**Як перевіряє сервер:**

1. 📖 Читає документ
2. 🏛️ Ставить свою печатку на ці дані
3. 🔍 Порівнює з печаткою в документі
4. ✅ Якщо збігаються → документ справжній!

## ❌ Чому атака не працює?

### Спроба підробки:
```javascript
// Зловмисник змінює payload:
originalPayload = { userId: 123, role: 'user' }
hackedPayload   = { userId: 123, role: 'admin' } // ⚠️

// Але підпис залишається старий!
oldSignature = "abc123xyz" // для старого payload

// Сервер перевіряє:
newSignature = HMAC_SHA256(header + hackedPayload, SECRET)
// Результат: "def456uvw" ≠ "abc123xyz" ❌
```

### **🔑 Без SECRET ключа неможливо створити правильний підпис!**

## 📊 Порівняння підходів

| Критерій | Сесії | JWT |
|----------|-------|-----|
| **Стан сервера** | ✅ Stateful | ❌ Stateless |
| **Масштабування** | ⚠️ Складне | ✅ Легке |
| **Відкликання** | ✅ Миттєве | ⚠️ Складне |
| **Розмір** | ✅ Малий | ⚠️ Великий |
| **Безпека** | ✅ Висока | ⚠️ Потребує уваги |

## 🔒 Хешування паролів: bcrypt

### ❌ Небезпечно
```javascript
// НІКОЛИ не робіть так!
const password = "mypassword123";
const hash = crypto.createHash('sha256')
                  .update(password)
                  .digest('hex');
```

### ✅ Безпечно

```javascript
// Правильний підхід з bcrypt
const bcrypt = require('bcryptjs');

const hashPassword = async (password) => {
    return await bcrypt.hash(password, 12);
};

const verifyPassword = async (password, hash) => {
    return await bcrypt.compare(password, hash);
};
```
## 🔧 Middleware аутентифікації

```javascript
function authenticateToken(req, res, next) {
    const authHeader = req.headers['authorization'];
    const token = authHeader && authHeader.split(' ')[1];

    if (!token) {
        return res.status(401).json({
            error: 'Токен відсутній'
        });
    }

    jwt.verify(token, process.env.JWT_SECRET, (err, user) => {
        if (err) return res.status(403).json({
            error: 'Недійсний токен'
        });

        req.user = user;
        next();
    });
}
```
## 👥 Система ролей (RBAC)

```mermaid
graph TD
    A[Користувач] --> B[Ролі]
    B --> C[Дозволи]
    C --> D[Ресурси]

    A1[Іван] --> B1[Admin]
    A2[Марія] --> B2[Moderator]
    A3[Петро] --> B3[User]

    B1 --> C1["users:*, posts:*"]
    B2 --> C2["posts:update, posts:delete"]
    B3 --> C3["posts:read, posts:create"]

    style B1 fill:#ff6b6b
    style B2 fill:#ffd93d
    style B3 fill:#6bcf7f
```

## 🛡️ Middleware авторизації

```javascript
function requireRole(roles) {
    return (req, res, next) => {
        if (!req.user) {
            return res.status(401).json({
                error: 'Не аутентифіковано'
            });
        }

        const userRoles = req.user.roles || [];
        const hasRole = roles.some(role =>
            userRoles.includes(role)
        );

        if (!hasRole) {
            return res.status(403).json({
                error: 'Недостатньо прав'
            });
        }

        next();
    };
}
```
## 🌐 CORS: Cross-Origin Resource Sharing

### Проблема

Браузери блокують запити між різними доменами з міркувань безпеки

### Рішення
```javascript
app.use(cors({
    origin: [
        'https://yourdomain.com',
        'https://app.yourdomain.com'
    ],
    credentials: true,
    methods: ['GET', 'POST', 'PUT', 'DELETE'],
    allowedHeaders: ['Content-Type', 'Authorization']
}));
```

**⚠️ Ніколи не використовуйте `origin: '*'` у продакшені!**

## 🎭 CSRF: Cross-Site Request Forgery

```mermaid
graph LR
    A[Зловмисний сайт] --> B[Скритий запит]
    B --> C[Банківський сайт]
    C --> D[Виконання операції]
    D --> E[💸 Перевід коштів]

    style A fill:#ff6b6b
    style E fill:#ff6b6b
```

### Захист: CSRF токени
```javascript
// Генерація токена
app.get('/csrf-token', (req, res) => {
    res.json({ csrfToken: req.csrfToken() });
});

// Перевірка токена
app.use('/api', csrfProtection);
```
## 💉 XSS: Cross-Site Scripting

### Типи XSS атак

- **Reflected XSS**: шкідливий код у URL
- **Stored XSS**: збережений у базі даних
- **DOM-based XSS**: маніпуляції з DOM

### Захист

```javascript
// Санітизація вхідних даних
function sanitizeInput(input) {
    return input
        .replace(/</g, '&lt;')
        .replace(/>/g, '&gt;')
        .replace(/"/g, '&quot;')
        .replace(/'/g, '&#x27;');
}

// Content Security Policy
app.use(helmet({
    contentSecurityPolicy: {
        directives: {
            defaultSrc: ["'self'"],
            scriptSrc: ["'self'"]
        }
    }
}));
```
## 🚀 Rate Limiting

### Захист від Brute Force атак

```javascript
const rateLimit = require('express-rate-limit');

// Загальний rate limiting
const generalLimiter = rateLimit({
    windowMs: 15 * 60 * 1000, // 15 хвилин
    max: 100 // 100 запитів на вікно
});

// Спеціальний для login
const authLimiter = rateLimit({
    windowMs: 15 * 60 * 1000,
    max: 5, // Тільки 5 спроб входу
    message: 'Забагато спроб входу'
});

app.use('/auth/login', authLimiter);
```

## 📊 Система моніторингу

```mermaid
graph TD
    A[Запит користувача] --> B{Middleware безпеки}
    B --> C[Логування дій]
    B --> D[Перевірка підозрілої активності]
    C --> E[Аудит лог]
    D --> F{Підозріла активність?}
    F -->|Так| G[🚨 Алерт адміну]
    F -->|Ні| H[Продовжити запит]

    style G fill:#ff6b6b
    style H fill:#6bcf7f
```

### Що логувати:

- Спроби входу (успішні/неуспішні)
- Зміни дозволів
- Доступ до чутливих даних
- Підозрілі запити

## 🔐 Шифрування даних

### Шифрування в спокої

```javascript
const crypto = require('crypto');

class DataEncryption {
    encrypt(text) {
        const cipher = crypto.createCipher('aes-256-gcm', secretKey);
        let encrypted = cipher.update(text, 'utf8', 'hex');
        encrypted += cipher.final('hex');
        return encrypted;
    }

    decrypt(encryptedText) {
        const decipher = crypto.createDecipher('aes-256-gcm', secretKey);
        let decrypted = decipher.update(encryptedText, 'hex', 'utf8');
        decrypted += decipher.final('utf8');
        return decrypted;
    }
}
```

### **HTTPS у продакшені - обов'язково!**

## ✅ Чекліст безпеки

### Аутентифікація
- ✅ bcrypt для паролів (salt rounds ≥ 10)
- ✅ JWT з коротким терміном дії
- ✅ Refresh токени
- ✅ Rate limiting для login

### Авторизація
- ✅ Принцип найменших привілеїв
- ✅ Ролі та дозволи
- ✅ Перевірка на кожному endpoint

### Захист від атак
- ✅ CORS налаштування
- ✅ CSRF токени
- ✅ XSS санітизація
- ✅ SQL injection захист

## 🛠️ Інструменти безпеки

### Автоматизація

- **Helmet.js** - безпечні HTTP заголовки
- **express-rate-limit** - обмеження запитів
- **express-validator** - валідація вхідних даних
- **bcryptjs** - хешування паролів

### Тестування

- **OWASP ZAP** - сканування вразливостей
- **npm audit** - перевірка залежностей
- **Snyk** - моніторинг безпеки

### Моніторинг

- **Winston** - логування
- **Elasticsearch** - аналіз логів
- **Grafana** - візуалізація метрик

## 🚨 Типові помилки

### ❌ Що НЕ робити

- Зберігати паролі в plaintext
- Використовувати слабкі алгоритми (MD5, SHA1)
- Ігнорувати валідацію на сервері
- Довіряти тільки клієнтській валідації
- Використовувати HTTP у продакшені
- Зберігати секрети в коді
- Ігнорувати логування безпеки

### ✅ Що робити ЗАВЖДИ

- Валідувати ВСІ вхідні дані
- Використовувати HTTPS
- Регулярно оновлювати залежності
- Логувати безпекові події
- Тестувати на вразливості

## 📈 Життєвий цикл безпеки

```mermaid
graph LR
    A[Планування] --> B[Розробка]
    B --> C[Тестування]
    C --> D[Деплой]
    D --> E[Моніторинг]
    E --> F[Оновлення]
    F --> A

    A1[Threat modeling] --> A
    B1[Secure coding] --> B
    C1[Security testing] --> C
    D1[Secure deployment] --> D
    E1[Incident response] --> E
    F1[Patch management] --> F

    style A fill:#e1f5fe
    style B fill:#f3e5f5
    style C fill:#fff3e0
    style D fill:#e8f5e8
    style E fill:#fff8e1
    style F fill:#fce4ec
```

## 💡 Ключові принципи

### Defense in Depth
**Кілька рівнів захисту краще за один досконалий**

### Principle of Least Privilege
**Мінімальні необхідні дозволи**

### Fail Secure
**Безпечна поведінка при помилках**

### Security by Design
**Безпека з самого початку, не як доповнення**

## 🌟 Майбутнє безпеки

### Сучасні тренди
- **Zero Trust** архітектура
- **Multi-Factor Authentication** (MFA)
- **Behavioral Analytics** для виявлення аномалій
- **AI/ML** в кібербезпеці

### Що вивчати далі
- OAuth 2.0 / OpenID Connect
- WebAuthn / FIDO2
- Security Headers
- Container Security

## 📚 Ресурси для поглиблення

### Документація
- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [MDN Web Security](https://developer.mozilla.org/en-US/docs/Web/Security)
- [JWT Best Practices](https://auth0.com/blog/a-look-at-the-latest-draft-for-jwt-bcp/)

### Практика
- [TryHackMe](https://tryhackme.com/) - практичні завдання
- [DVWA](http://www.dvwa.co.uk/) - тестове середовище
- [WebGoat](https://owasp.org/www-project-webgoat/) - навчальні вразливості

## 🎉 Висновки

### Безпека - це не опція, а необхідність

- **Комплексний підхід** до захисту додатку
- **Постійне навчання** та оновлення знань
- **Превентивні заходи** краще за реактивні
- **Тестування безпеки** на всіх етапах розробки
