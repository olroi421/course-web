# Безпека вебзастосунків

## Чому безпека критично важлива?

**Статистика кіберзлочинності**

- 70%+ вебзастосунків мають критичні вразливості
- 200+ днів середній час виявлення компрометації
- Мільйони доларів середня вартість інциденту

**Наслідки порушень безпеки**

- Витік персональних даних користувачів
- Фінансові втрати та юридичні наслідки
- Репутаційні збитки для компанії
- Втрата довіри клієнтів

---

## Принципи безпечної розробки

**Defense in Depth**
Багаторівнева система захисту

**Principle of Least Privilege**
Мінімальні необхідні привілеї

**Security by Design**
Безпека з самого початку розробки

**Fail Securely**
Безпечна поведінка при помилках

**Never Trust User Input**
Ніколи не довіряти даним користувача

```mermaid
graph TB
    A[Вхідні дані] --> B[Валідація]
    B --> C[Санітизація]
    C --> D[Авторизація]
    D --> E[Безпечна обробка]
    E --> F[Логування]
```

---

## OWASP Top 10

**Найкритичніші ризики безпеки вебзастосунків**

1. **A01: Broken Access Control** - порушення контролю доступу
2. **A02: Cryptographic Failures** - криптографічні помилки
3. **A03: Injection** - ін'єкційні атаки
4. **A04: Insecure Design** - небезпечний дизайн
5. **A05: Security Misconfiguration** - неправильна конфігурація
6. **A06: Vulnerable Components** - вразливі компоненти
7. **A07: Authentication Failures** - помилки автентифікації
8. **A08: Data Integrity Failures** - порушення цілісності
9. **A09: Logging Failures** - недостатнє логування
10. **A10: Server-Side Request Forgery** - SSRF атаки

---

## Broken Access Control

**Проблема:** користувачі отримують доступ до ресурсів без належних дозволів

**Приклади атак**

- Зміна параметрів URL для доступу до чужих даних
- Підвищення привілеїв через маніпуляцію токенами
- Обхід перевірок авторизації

**Захист**

```javascript
// Перевірка авторизації для кожного запиту
app.get('/api/users/:userId/profile',
  authenticateToken,
  async (req, res) => {
    if (req.user.id !== userId && !req.user.isAdmin) {
      return res.status(403).json({
        error: 'Доступ заборонено'
      });
    }
    // Повернення даних
});
```

---

## SQL Injection

**Механізм атаки:** впровадження шкідливого SQL коду через вхідні дані

```javascript
// ВРАЗЛИВИЙ КОД
const query = `SELECT * FROM users WHERE id = ${userId}`;
// userId = "1 OR 1=1" → повертає всіх користувачів

// БЕЗПЕЧНИЙ КОД: параметризовані запити
const query = 'SELECT * FROM users WHERE id = ?';
await db.execute(query, [userId]);

// НАЙКРАЩИЙ ПІДХІД: ORM
const user = await prisma.user.findUnique({
  where: { id: userId }
});
```

**Наслідки:** викрадення даних, модифікація або видалення таблиць

---

## Cross-Site Scripting (XSS)

**Типи XSS атак**

- **Reflected XSS:** шкідливий скрипт у запиті
- **Stored XSS:** шкідливий код у базі даних
- **DOM-based XSS:** маніпуляція DOM на клієнті

```javascript
// ВРАЗЛИВИЙ КОД
res.send(`<h1>Привіт, ${req.query.name}</h1>`);
// name=<script>alert('XSS')</script>

// БЕЗПЕЧНИЙ КОД: екранування HTML
const escapeHTML = (str) =>
  str.replace(/[&<>"'/]/g, char => map[char]);

res.send(`<h1>Привіт, ${escapeHTML(req.query.name)}</h1>`);
```

---

## Content Security Policy

**CSP:** механізм захисту від XSS через визначення білого списку джерел ресурсів

```javascript
app.use(helmet.contentSecurityPolicy({
  directives: {
    defaultSrc: ["'self'"],
    scriptSrc: ["'self'", "https://trusted-cdn.com"],
    styleSrc: ["'self'", "'unsafe-inline'"],
    imgSrc: ["'self'", "data:", "https:"],
    objectSrc: ["'none'"],
    upgradeInsecureRequests: []
  }
}));
```

**Переваги:** блокування непередбачених скриптів та стилів

---

## Cross-Site Request Forgery (CSRF)

**Проблема:** примушення автентифікованого користувача виконувати небажані дії

```javascript
// Генерація та перевірка CSRF токену
const csrfProtection = csrf({
  cookie: { httpOnly: true, sameSite: 'strict' }
});

app.get('/transfer', csrfProtection, (req, res) => {
  res.render('transfer', {
    csrfToken: req.csrfToken()
  });
});

app.post('/transfer', csrfProtection, async (req, res) => {
  // Токен автоматично перевіряється
  await processTransfer(req.body);
});
```

**Додатковий захист:** SameSite cookies, перевірка Origin headers

---

## Валідація та санітизація даних

**Стратегії валідації**

- **Whitelist:** дозволяємо тільки безпечні значення
- **Blacklist:** блокуємо небезпечні значення (менш надійно)
- **Format validation:** перевірка структури даних
- **Business logic:** відповідність бізнес-правилам

```javascript
const userSchema = Joi.object({
  username: Joi.string()
    .alphanum()
    .min(3)
    .max(30)
    .required(),
  email: Joi.string()
    .email()
    .required(),
  age: Joi.number()
    .integer()
    .min(18)
    .required()
});
```

---

## Security Headers

**Критичні HTTP заголовки безпеки**

```javascript
app.use(helmet({
  // HSTS: примушує використання HTTPS
  hsts: { maxAge: 31536000, includeSubDomains: true },

  // Захист від clickjacking
  frameguard: { action: 'deny' },

  // Запобігання MIME-sniffing
  noSniff: true,

  // Referrer Policy
  referrerPolicy: { policy: 'strict-origin' }
}));
```

**Результат:** захист від різних векторів атак без змін у коді

---

## Автентифікація та авторизація

**Надійна автентифікація**

- Сильні вимоги до паролів (мінімум 12 символів, різні типи)
- Хешування паролів з bcrypt (SALT_ROUNDS ≥ 12)
- Багатофакторна автентифікація (2FA/MFA)
- Rate limiting для захисту від brute force
- Безпечне управління сесіями

```javascript
const validatePassword = (password) => {
  const hasUpperCase = /[A-Z]/.test(password);
  const hasLowerCase = /[a-z]/.test(password);
  const hasNumbers = /\d/.test(password);
  const hasSpecialChar = /[!@#$%^&*]/.test(password);

  return password.length >= 12 &&
         hasUpperCase && hasLowerCase &&
         hasNumbers && hasSpecialChar;
};
```

---

## Логування та моніторинг

**Що логувати**

- Спроби автентифікації (успішні та невдалі)
- Зміни конфігурації та привілеїв
- Доступ до чутливих даних
- Підозріла активність та аномалії

```javascript
securityLogger.warn('Failed login attempt', {
  username,
  ipAddress,
  userAgent,
  timestamp: new Date().toISOString(),
  reason: 'Invalid credentials'
});

// Моніторинг підозрілої активності
if (failedAttempts >= THRESHOLD) {
  securityLogger.error('Suspicious activity', {
    identifier: ipAddress,
    activityType: 'login_attempt',
    count: failedAttempts
  });
}
```

---

## Penetration Testing

**Фази тестування безпеки**

1. **Reconnaissance:** збір інформації про ціль
2. **Scanning:** виявлення відкритих портів та вразливостей
3. **Gaining Access:** експлуатація вразливостей
4. **Maintaining Access:** тестування persistency
5. **Analysis:** документування та рекомендації

**Автоматизація в CI/CD**

```javascript
// Security testing pipeline
describe('Security Tests', () => {
  test('Має містити CSP заголовок', async () => {
    const response = await request(app).get('/');
    expect(response.headers['content-security-policy'])
      .toBeDefined();
  });

  test('Має захищати від XSS', async () => {
    const xssPayload = '<script>alert("XSS")</script>';
    const response = await request(app)
      .get(`/search?q=${encodeURIComponent(xssPayload)}`);
    expect(response.text).not.toContain('<script>');
  });
});
```

---

## NoSQL Injection

**Специфіка MongoDB атак**

```javascript
// ВРАЗЛИВИЙ КОД
const user = await User.findOne({
  username,
  password
});
// username: {"$gt": ""} → обхід автентифікації

// БЕЗПЕЧНИЙ КОД: валідація типів
if (typeof username !== 'string') {
  throw new Error('Некоректний тип даних');
}

const sanitizedUsername = username.replace(/[.$]/g, '');
const user = await User.findOne({
  username: { $eq: sanitizedUsername },
  password: { $eq: password }
});
```

**Захист:** mongo-sanitize middleware, строга валідація типів

---

## Управління залежностями

**Проблеми застарілих компонентів**

- Відомі вразливості у публічних базах (CVE)
- Ланцюг постачання атаки
- Відсутність патчів безпеки

**Рішення**

```json
{
  "scripts": {
    "audit": "npm audit",
    "audit:fix": "npm audit fix",
    "update:check": "npm outdated"
  }
}
```

**Автоматизація:** GitHub Dependabot, Snyk, npm audit у CI/CD

---

## Криптографія

**Правильне використання**

```javascript
// Хешування паролів
const bcrypt = require('bcrypt');
const SALT_ROUNDS = 12;

const hashedPassword = await bcrypt.hash(
  password,
  SALT_ROUNDS
);

// Перевірка
const isValid = await bcrypt.compare(
  inputPassword,
  hashedPassword
);

// JWT токени з сильними секретами
const token = jwt.sign(
  { userId: user.id },
  process.env.JWT_SECRET,
  { expiresIn: '1h', algorithm: 'HS256' }
);
```

**Принципи:** використовувати перевірені бібліотеки, сильні секрети (≥32 символи)

---

## Rate Limiting

**Захист від brute force та DDoS атак**

```javascript
const rateLimit = require('express-rate-limit');

const loginLimiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 хвилин
  max: 5, // Максимум 5 спроб
  message: 'Занадто багато спроб входу',
  standardHeaders: true,
  skipSuccessfulRequests: true
});

app.post('/api/login', loginLimiter, async (req, res) => {
  // Логіка автентифікації
});

// Глобальний rate limit
const globalLimiter = rateLimit({
  windowMs: 60 * 1000, // 1 хвилина
  max: 100 // 100 запитів на хвилину
});

app.use('/api/', globalLimiter);
```

---

## HTTPS та безпека транспорту

**Чому HTTPS обов'язковий**

- Шифрування даних при передачі
- Захист від man-in-the-middle атак
- Підтвердження ідентичності сервера
- SEO переваги та довіра користувачів

**HSTS заголовок**

```javascript
app.use(helmet.hsts({
  maxAge: 31536000, // 1 рік
  includeSubDomains: true,
  preload: true
}));
```

**Результат:** браузер примушує використовувати HTTPS навіть при спробі HTTP з'єднання

---

## Безпека файлових завантажень

**Загрози**

- Завантаження шкідливих файлів
- Виконання коду на сервері
- Path traversal атаки

**Захист**

```javascript
const multer = require('multer');

const storage = multer.diskStorage({
  destination: './uploads/',
  filename: (req, file, cb) => {
    // Санітизація імені файлу
    const safeName = file.originalname
      .replace(/[^\w\s.-]/gi, '')
      .replace(/\s+/g, '_');
    cb(null, Date.now() + '-' + safeName);
  }
});

const upload = multer({
  storage,
  limits: { fileSize: 5 * 1024 * 1024 }, // 5MB
  fileFilter: (req, file, cb) => {
    const allowedTypes = ['image/jpeg', 'image/png'];
    if (allowedTypes.includes(file.mimetype)) {
      cb(null, true);
    } else {
      cb(new Error('Недозволений тип файлу'));
    }
  }
});
```

---

## Безпека API

**RESTful API Security**

- Автентифікація через JWT або OAuth 2.0
- Версіонування API для безпечних оновлень
- Обмеження розміру payload
- API ключі та rate limiting

```javascript
// API ключі middleware
const validateApiKey = (req, res, next) => {
  const apiKey = req.header('X-API-Key');

  if (!apiKey || !isValidApiKey(apiKey)) {
    return res.status(401).json({
      error: 'Невірний API ключ'
    });
  }

  next();
};

app.use('/api/v1/', validateApiKey);

// Обмеження розміру
app.use(express.json({
  limit: '100kb'
}));
```

---

## CORS конфігурація

**Правильне налаштування Cross-Origin Resource Sharing**

```javascript
const cors = require('cors');

// Продакшн конфігурація
const corsOptions = {
  origin: function (origin, callback) {
    const allowedOrigins = [
      'https://example.com',
      'https://app.example.com'
    ];

    if (!origin || allowedOrigins.includes(origin)) {
      callback(null, true);
    } else {
      callback(new Error('Заборонено CORS'));
    }
  },
  credentials: true,
  optionsSuccessStatus: 200,
  methods: ['GET', 'POST', 'PUT', 'DELETE'],
  allowedHeaders: ['Content-Type', 'Authorization']
};

app.use(cors(corsOptions));
```

**Важливо:** ніколи не використовувати `origin: '*'` у продакшні

---

## Безпека змінних середовища

**Управління секретами**

```javascript
// .env файл (НЕ комітити в Git!)
DATABASE_URL=postgresql://user:pass@localhost/db
JWT_SECRET=secure_random_string_min_32_chars
SESSION_SECRET=another_secure_random_string
API_KEY=your_api_key_here

// Використання
require('dotenv').config();

const dbUrl = process.env.DATABASE_URL;

// Перевірка наявності критичних змінних
const requiredEnvVars = [
  'DATABASE_URL',
  'JWT_SECRET',
  'SESSION_SECRET'
];

requiredEnvVars.forEach(varName => {
  if (!process.env[varName]) {
    throw new Error(`Відсутня змінна: ${varName}`);
  }
});
```

**.gitignore:** завжди включати `.env`, `.env.local`, `.env.*`

---

## Security Checklist

**Перевірка перед деплоєм**

✓ HTTPS налаштовано для всіх з'єднань
✓ Security headers (CSP, HSTS, X-Frame-Options)
✓ Всі залежності оновлені (npm audit)
✓ Rate limiting для критичних endpoints
✓ Input validation та sanitization
✓ CSRF захист для форм
✓ Паролі хешуються з bcrypt
✓ JWT токени з сильними секретами
✓ Логування безпекових подій
✓ Змінні середовища захищені
✓ CORS правильно налаштовано
✓ Обмеження розміру файлів

---

## Моніторинг безпеки

**Continuous Security Monitoring**

```javascript
// Централізоване логування
const winston = require('winston');

const securityLogger = winston.createLogger({
  level: 'info',
  format: winston.format.json(),
  transports: [
    new winston.transports.File({
      filename: 'security.log',
      level: 'warn'
    })
  ]
});

// Алерти для критичних подій
class SecurityMonitor {
  detectAnomalies(event) {
    if (this.isSuspicious(event)) {
      this.sendAlert({
        severity: 'high',
        event,
        timestamp: new Date()
      });
    }
  }
}
```

**Інструменти:** ELK Stack, Prometheus, Grafana, Sentry

---

## Інциденти безпеки

**План реагування на інциденти**

1. **Виявлення:** системи моніторингу та алерти
2. **Ізоляція:** обмеження доступу до скомпрометованих систем
3. **Аналіз:** визначення масштабу та причин
4. **Відновлення:** повернення до нормального стану
5. **Документування:** звіт про інцидент та висновки

**Превентивні заходи**

- Регулярні бекапи критичних даних
- План disaster recovery
- Тестування процедур відновлення
- Навчання команди реагуванню на інциденти

---

## Compliance та стандарти

**Відповідність регуляторним вимогам**

- **GDPR:** захист персональних даних (ЄС)
- **PCI DSS:** безпека платіжних карток
- **HIPAA:** захист медичних даних (США)
- **SOC 2:** безпека хмарних сервісів

**Основні вимоги**

- Шифрування чутливих даних
- Контроль доступу та аудит
- Регулярні security assessments
- Право користувачів на видалення даних
- Сповіщення про витоки даних (протягом 72 годин)

---

## DevSecOps

**Інтеграція безпеки в CI/CD**

```yaml
# .github/workflows/security.yml
name: Security Checks
on: [push, pull_request]

jobs:
  security:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: npm audit
        run: npm audit --audit-level=moderate

      - name: SAST scan
        run: npx eslint . --ext .js

      - name: Dependency check
        uses: dependency-check/Dependency-Check_Action@main

      - name: Container scan
        run: docker scan app:latest
```

**Переваги:** раннє виявлення вразливостей, автоматизація перевірок

---

## Освітні ресурси

**Навчання та сертифікації**

- **OWASP:** безкоштовні матеріали та проєкти
- **PortSwigger Web Security Academy:** інтерактивні labs
- **SANS Security Training:** професійні курси
- **HackerOne/Bugcrowd:** bug bounty платформи

**Практика**

- Участь у CTF (Capture The Flag) змаганнях
- Вирішення завдань на HackTheBox, TryHackMe
- Внесок у open source security проєкти
- Проходження security code review

---

##  Майбутнє безпеки

**Тренди та виклики**

- **AI/ML у кібербезпеці:** автоматизоване виявлення загроз
- **Zero Trust Architecture:** ніколи не довіряти, завжди перевіряти
- **Post-Quantum криптографія:** підготовка до квантових комп'ютерів
- **Supply Chain Security:** захист ланцюга постачання

**Постійна еволюція**

Безпека є неперервним процесом, що вимагає постійного навчання, адаптації та вдосконалення практик. Успішна безпека вимагає балансу між зручністю користувачів та захистом систем.
