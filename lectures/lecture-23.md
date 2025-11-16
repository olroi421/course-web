# Лекція 23. Безпека вебзастосунків

## Вступ до безпеки вебзастосунків

### Критична важливість безпеки у сучасній веброзробці

Безпека вебзастосунків є одним з найважливіших аспектів сучасної розробки програмного забезпечення. У світі, де персональні дані користувачів, фінансова інформація та критична бізнес-логіка переміщуються через вебінтерфейси, захист від зловмисних атак стає не просто рекомендацією, а абсолютною необхідністю. Кожна вразливість у вебзастосунку потенційно може призвести до витоку даних, фінансових втрат, репутаційних збитків та юридичних наслідків для організації.

Статистика кіберзлочинності демонструє тривожні тенденції. За даними дослідників безпеки, понад 70% вебзастосунків мають принаймні одну критичну вразливість. Середній час від моменту компрометації системи до її виявлення становить більше 200 днів, що дає зловмисникам значний час для завдання шкоди. Вартість середнього інциденту безпеки для компаній середнього розміру може перевищувати мільйони доларів, враховуючи прямі збитки, витрати на розслідування, відновлення систем та компенсації постраждалим користувачам.

Розробники несуть професійну відповідальність за створення безпечних систем. Це вимагає не просто знання основних принципів безпеки, але й постійного відстеження нових загроз, вразливостей та методів захисту. Безпека має бути інтегрована в кожен етап життєвого циклу розробки, від проектування архітектури до тестування та моніторингу продакшн-систем.

### Принципи безпечної розробки

**Defense in Depth** або принцип глибинного захисту передбачає створення багаторівневої системи безпеки. Якщо один рівень захисту буде порушений, наступні рівні повинні запобігти успішній атаці. Це означає комбінування різних технік безпеки: валідації на клієнті та сервері, авторизації на рівні мережі та застосунку, шифрування даних при передачі та зберіганні, регулярні бекапи та системи виявлення вторгнень.

**Principle of Least Privilege** вимагає надання мінімально необхідних привілеїв для виконання конкретної задачі. Користувачі, процеси та сервіси повинні мати доступ тільки до тих ресурсів, які абсолютно необхідні для їхньої роботи. Це значно обмежує потенційний збиток від компрометації облікового запису або системного компонента.

**Security by Design** означає, що безпека має враховуватися з самого початку розробки, а не додаватися як додаткова функція пізніше. Архітектурні рішення, вибір технологій, дизайн API та бази даних повинні приймати до уваги потенційні загрози безпеці. Ретроактивне додавання безпеки до існуючої системи завжди складніше, дорожче та менш ефективне.

**Fail Securely** принцип диктує, що система повинна залишатися в безпечному стані навіть при виникненні помилок. Якщо відбувається збій аутентифікації, система має заборонити доступ, а не надати його за замовчуванням. Повідомлення про помилки не повинні розкривати чутливу інформацію про внутрішню структуру системи, яка може бути використана зловмисниками.

**Never Trust User Input** є фундаментальним правилом безпеки. Всі дані, що надходять від користувачів чи зовнішніх систем, потенційно небезпечні та повинні проходити ретельну валідацію, санітизацію та верифікацію перед використанням. Це стосується не тільки форм вводу, але й заголовків HTTP, cookies, параметрів URL та будь-яких інших джерел зовнішніх даних.

## OWASP Top 10 вразливостей

### Що таке OWASP та його значення

**Open Web Application Security Project** є глобальною некомерційною організацією, присвяченою покращенню безпеки програмного забезпечення. OWASP публікує широкий спектр ресурсів безпеки, включаючи документацію, інструменти, навчальні матеріали та найбільш відомий список Top 10 найкритичніших ризиків безпеки вебзастосунків, який оновлюється кожні кілька років на основі аналізу тисяч вразливостей з реальних застосунків.

OWASP Top 10 став індустрійним стандартом для оцінки та покращення безпеки вебзастосунків. Організації по всьому світу використовують цей список як базу для тренінгів розробників, аудитів безпеки, вимог до постачальників програмного забезпечення та створення корпоративних стандартів безпеки. Розуміння OWASP Top 10 є обов'язковим для кожного професійного веброзробника.

### Детальний огляд OWASP Top 10 2021

#### A01: Broken Access Control

**Порушення контролю доступу** посідає перше місце в списку OWASP Top 10 2021, що відображає критичну важливість правильної реалізації авторизації. Ця категорія включає вразливості, які дозволяють користувачам отримувати доступ до ресурсів або виконувати дії, на які вони не повинні мати дозволу.

Типові приклади включають можливість доступу до чужих даних шляхом зміни параметрів у URL, підвищення привілеїв через маніпуляцію токенами або cookies, обхід перевірок авторизації через пряме звернення до захищених ресурсів, доступ до API без належної автентифікації.

```javascript
// ВРАЗЛИВИЙ КОД: відсутність перевірки авторизації
app.get('/api/users/:userId/profile', async (req, res) => {
    const { userId } = req.params;

    // Отримуємо профіль без перевірки, чи має поточний користувач право його бачити
    const profile = await User.findById(userId);
    res.json(profile);
});

// БЕЗПЕЧНИЙ КОД: перевірка авторизації
app.get('/api/users/:userId/profile', authenticateToken, async (req, res) => {
    const { userId } = req.params;
    const currentUserId = req.user.id;

    // Перевіряємо, чи користувач намагається отримати свій власний профіль
    // або має адміністративні права
    if (currentUserId !== userId && !req.user.isAdmin) {
        return res.status(403).json({
            error: 'Доступ заборонено'
        });
    }

    const profile = await User.findById(userId);
    res.json(profile);
});
```

Захист від порушень контролю доступу вимагає реалізації надійної моделі авторизації на серверній стороні, перевірки дозволів для кожного запиту, використання принципу найменших привілеїв, логування та моніторингу спроб несанкціонованого доступу, регулярного аудиту правил доступу.

#### A02: Cryptographic Failures

**Криптографічні помилки** охоплюють вразливості, пов'язані з неналежним захистом чутливих даних. Це включає використання слабких алгоритмів шифрування, зберігання паролів у відкритому вигляді або з використанням застарілих методів хешування, передачу чутливих даних без шифрування, неправильне управління криптографічними ключами.

```javascript
// ВРАЗЛИВИЙ КОД: зберігання паролів у відкритому вигляді
const createUser = async (username, password) => {
    await User.create({
        username,
        password // Пароль зберігається як є, у відкритому вигляді
    });
};

// БЕЗПЕЧНИЙ КОД: хешування паролів з bcrypt
const bcrypt = require('bcrypt');
const SALT_ROUNDS = 12;

const createUser = async (username, password) => {
    // Генеруємо сіль та хешуємо пароль
    const hashedPassword = await bcrypt.hash(password, SALT_ROUNDS);

    await User.create({
        username,
        password: hashedPassword
    });
};

// Перевірка пароля при логіні
const authenticateUser = async (username, password) => {
    const user = await User.findOne({ username });

    if (!user) {
        return null;
    }

    // Порівнюємо введений пароль з хешем
    const isValid = await bcrypt.compare(password, user.password);

    return isValid ? user : null;
};
```

Правильне використання криптографії включає застосування сучасних алгоритмів шифрування, використання HTTPS для всіх з'єднань, правильне зберігання ключів у безпечних сховищах, регулярне оновлення криптографічних бібліотек, дотримання стандартів шифрування даних у спокої та при передачі.

#### A03: Injection

**Ін'єкції** залишаються однією з найнебезпечніших категорій вразливостей, хоча їх частота знижується завдяки використанню ORM та підготовлених запитів. Ін'єкції виникають, коли ненадійні дані надсилаються інтерпретатору як частина команди або запиту. Зловмисник може використати ін'єкцію для виконання непередбачених команд або доступу до даних без належної авторизації.

```javascript
// ВРАЗЛИВИЙ КОД: SQL ін'єкція через конкатенацію рядків
const getUserByEmail = async (email) => {
    const query = `SELECT * FROM users WHERE email = '${email}'`;
    // Якщо email = "' OR '1'='1", запит поверне всіх користувачів
    return await db.query(query);
};

// БЕЗПЕЧНИЙ КОД: використання параметризованих запитів
const getUserByEmail = async (email) => {
    const query = 'SELECT * FROM users WHERE email = ?';
    return await db.query(query, [email]);
};

// БЕЗПЕЧНИЙ КОД: використання ORM Prisma
const getUserByEmail = async (email) => {
    return await prisma.user.findUnique({
        where: { email }
    });
};

// ВРАЗЛИВИЙ КОД: NoSQL ін'єкція в MongoDB
const findUser = async (username) => {
    // Якщо username = {"$gt": ""}, це поверне всіх користувачів
    return await User.findOne({ username });
};

// БЕЗПЕЧНИЙ КОД: валідація та санітизація вводу
const findUser = async (username) => {
    // Перевіряємо, що username є рядком
    if (typeof username !== 'string') {
        throw new Error('Некоректний тип даних для username');
    }

    // Видаляємо спеціальні символи MongoDB
    const sanitizedUsername = username.replace(/[.$]/g, '');

    return await User.findOne({ username: sanitizedUsername });
};
```

Захист від ін'єкцій передбачає використання ORM та підготовлених запитів, валідацію всіх вхідних даних, використання білих списків дозволених значень, уникнення динамічного виконання коду, обмеження привілеїв облікових записів баз даних.

#### A04: Insecure Design

**Небезпечний дизайн** відображує недоліки в архітектурі та проектуванні застосунку, які не можуть бути виправлені простою реалізацією. Це стосується відсутності моделювання загроз, невірного визначення бізнес-ризиків, неадекватної архітектури безпеки на етапі проектування.

```javascript
// ВРАЗЛИВИЙ ДИЗАЙН: відсутність обмеження спроб логіну
let loginAttempts = 0;

const login = async (username, password) => {
    const user = await authenticateUser(username, password);

    if (!user) {
        loginAttempts++;
        return { success: false };
    }

    return { success: true, token: generateToken(user) };
};

// БЕЗПЕЧНИЙ ДИЗАЙН: обмеження спроб з тимчасовим блокуванням
class LoginRateLimiter {
    constructor() {
        this.attempts = new Map();
        this.lockouts = new Map();
        this.MAX_ATTEMPTS = 5;
        this.LOCKOUT_DURATION = 15 * 60 * 1000; // 15 хвилин
    }

    isLocked(username) {
        const lockoutTime = this.lockouts.get(username);

        if (!lockoutTime) {
            return false;
        }

        if (Date.now() - lockoutTime > this.LOCKOUT_DURATION) {
            this.lockouts.delete(username);
            this.attempts.delete(username);
            return false;
        }

        return true;
    }

    recordAttempt(username, success) {
        if (success) {
            this.attempts.delete(username);
            this.lockouts.delete(username);
            return;
        }

        const currentAttempts = (this.attempts.get(username) || 0) + 1;
        this.attempts.set(username, currentAttempts);

        if (currentAttempts >= this.MAX_ATTEMPTS) {
            this.lockouts.set(username, Date.now());
        }
    }

    getRemainingAttempts(username) {
        const attempts = this.attempts.get(username) || 0;
        return Math.max(0, this.MAX_ATTEMPTS - attempts);
    }
}

const rateLimiter = new LoginRateLimiter();

const login = async (username, password) => {
    if (rateLimiter.isLocked(username)) {
        return {
            success: false,
            error: 'Акаунт тимчасово заблокований через надто багато невдалих спроб входу'
        };
    }

    const user = await authenticateUser(username, password);
    rateLimiter.recordAttempt(username, !!user);

    if (!user) {
        const remaining = rateLimiter.getRemainingAttempts(username);
        return {
            success: false,
            error: `Невірні облікові дані. Залишилось спроб: ${remaining}`
        };
    }

    return { success: true, token: generateToken(user) };
};
```

Безпечний дизайн вимагає моделювання загроз на етапі проектування, використання перевірених шаблонів безпеки, врахування принципу найменших привілеїв у архітектурі, планування відновлення після інцидентів, регулярного перегляду та оновлення моделей безпеки.

#### A05: Security Misconfiguration

**Неправильна конфігурація безпеки** охоплює широкий спектр проблем від залишених налаштувань за замовчуванням до відкритих консолей адміністрування. Це включає невимкнені непотрібні функції, налаштування за замовчуванням, детальні повідомлення про помилки, відсутність безпекових заголовків HTTP, застарілі версії програмного забезпечення.

```javascript
// ВРАЗЛИВА КОНФІГУРАЦІЯ: детальні повідомлення про помилки в продакшні
app.use((err, req, res, next) => {
    res.status(500).json({
        error: err.message,
        stack: err.stack, // Розкриває внутрішню структуру
        sql: err.sql // Розкриває структуру бази даних
    });
});

// БЕЗПЕЧНА КОНФІГУРАЦІЯ: різні обробники для розробки та продакшн
const errorHandler = (err, req, res, next) => {
    console.error('Error:', err);

    if (process.env.NODE_ENV === 'production') {
        // В продакшні повертаємо загальне повідомлення
        res.status(err.statusCode || 500).json({
            error: 'Виникла внутрішня помилка сервера'
        });
    } else {
        // В розробці надаємо детальну інформацію
        res.status(err.statusCode || 500).json({
            error: err.message,
            stack: err.stack
        });
    }
};

app.use(errorHandler);

// БЕЗПЕЧНА КОНФІГУРАЦІЯ: налаштування security headers
const helmet = require('helmet');

app.use(helmet({
    contentSecurityPolicy: {
        directives: {
            defaultSrc: ["'self'"],
            styleSrc: ["'self'", "'unsafe-inline'"],
            scriptSrc: ["'self'"],
            imgSrc: ["'self'", "data:", "https:"]
        }
    },
    hsts: {
        maxAge: 31536000,
        includeSubDomains: true,
        preload: true
    },
    noSniff: true,
    xssFilter: true,
    referrerPolicy: { policy: 'same-origin' }
}));
```

Правильна конфігурація включає видалення непотрібних функцій та сервісів, зміну паролів за замовчуванням, регулярне оновлення програмного забезпечення, налаштування безпекових заголовків, обмеження детальності повідомлень про помилки, автоматизацію процесу налаштування.

#### A06: Vulnerable and Outdated Components

**Вразливі та застарілі компоненти** стають дедалі більшою проблемою у світі, де типовий вебзастосунок може мати сотні залежностей. Використання компонентів з відомими вразливостями є простим шляхом для зловмисників отримати контроль над системою.

```javascript
// Приклад перевірки вразливостей у package.json
{
  "name": "secure-app",
  "version": "1.0.0",
  "scripts": {
    "audit": "npm audit",
    "audit:fix": "npm audit fix",
    "update:check": "npm outdated",
    "update:interactive": "npm-check -u"
  },
  "dependencies": {
    "express": "^4.18.2",
    "helmet": "^7.1.0",
    // Регулярно оновлюємо залежності
  },
  "devDependencies": {
    "npm-check": "^6.0.1"
  }
}

// Автоматизація перевірки вразливостей у CI/CD
// .github/workflows/security.yml
// name: Security Audit
// on: [push, pull_request]
// jobs:
//   audit:
//     runs-on: ubuntu-latest
//     steps:
//       - uses: actions/checkout@v3
//       - name: Run npm audit
//         run: npm audit --audit-level=moderate
```

Управління компонентами вимагає інвентаризації всіх використовуваних бібліотек, регулярного моніторингу вразливостей, швидкого оновлення компонентів при виявленні проблем, видалення невикористовуваних залежностей, використання перевірених джерел компонентів.

#### A07: Identification and Authentication Failures

**Помилки ідентифікації та автентифікації** включають вразливості, які дозволяють зловмисникам компрометувати паролі, ключі, сесійні токени або використовувати інші недоліки реалізації для прийняття ідентичності інших користувачів тимчасово або постійно.

```javascript
// ВРАЗЛИВА РЕАЛІЗАЦІЯ: слабкі вимоги до паролів
const validatePassword = (password) => {
    return password.length >= 6; // Занадто слабка вимога
};

// БЕЗПЕЧНА РЕАЛІЗАЦІЯ: сильні вимоги до паролів
const validatePassword = (password) => {
    const minLength = 12;
    const hasUpperCase = /[A-Z]/.test(password);
    const hasLowerCase = /[a-z]/.test(password);
    const hasNumbers = /\d/.test(password);
    const hasSpecialChar = /[!@#$%^&*(),.?":{}|<>]/.test(password);

    const errors = [];

    if (password.length < minLength) {
        errors.push(`Пароль повинен містити мінімум ${minLength} символів`);
    }
    if (!hasUpperCase) {
        errors.push('Пароль повинен містити великі літери');
    }
    if (!hasLowerCase) {
        errors.push('Пароль повинен містити малі літери');
    }
    if (!hasNumbers) {
        errors.push('Пароль повинен містити цифри');
    }
    if (!hasSpecialChar) {
        errors.push('Пароль повинен містити спеціальні символи');
    }

    return {
        isValid: errors.length === 0,
        errors
    };
};

// БЕЗПЕЧНА РЕАЛІЗАЦІЯ: захист від автоматизованих атак
const rateLimit = require('express-rate-limit');

const loginLimiter = rateLimit({
    windowMs: 15 * 60 * 1000, // 15 хвилин
    max: 5, // Максимум 5 спроб
    message: 'Занадто багато спроб входу з цієї IP адреси',
    standardHeaders: true,
    legacyHeaders: false,
    skipSuccessfulRequests: true
});

app.post('/api/login', loginLimiter, async (req, res) => {
    // Логіка автентифікації
});

// Реалізація багатофакторної автентифікації
const speakeasy = require('speakeasy');

const generateTOTP = (user) => {
    const secret = speakeasy.generateSecret({
        name: `MyApp (${user.email})`
    });

    // Зберігаємо secret для користувача
    user.totpSecret = secret.base32;
    await user.save();

    return secret.otpauth_url;
};

const verifyTOTP = (user, token) => {
    return speakeasy.totp.verify({
        secret: user.totpSecret,
        encoding: 'base32',
        token: token,
        window: 2 // Дозволяємо невелике відхилення часу
    });
};
```

Надійна автентифікація включає використання багатофакторної автентифікації, захист від brute force атак через rate limiting, безпечне зберігання облікових даних, правильне управління сесіями, використання безпечних методів відновлення паролів.

#### A08: Software and Data Integrity Failures

**Порушення цілісності програмного забезпечення та даних** стосуються коду та інфраструктури, які не захищені від порушень цілісності. Приклади включають використання бібліотек з ненадійних джерел, автоматичне оновлення без перевірки цілісності, небезпечна десеріалізація даних.

```javascript
// ВРАЗЛИВИЙ КОД: небезпечна десеріалізація
app.post('/api/save-state', (req, res) => {
    const state = JSON.parse(req.body.state);
    // Якщо state містить __proto__, можлива prototype pollution
    Object.assign({}, state);
});

// БЕЗПЕЧНИЙ КОД: валідація перед десеріалізацією
const Joi = require('joi');

const stateSchema = Joi.object({
    userId: Joi.string().required(),
    preferences: Joi.object({
        theme: Joi.string().valid('light', 'dark'),
        language: Joi.string().valid('uk', 'en')
    }),
    lastActivity: Joi.date()
}).unknown(false); // Не дозволяємо невідомі поля

app.post('/api/save-state', async (req, res) => {
    try {
        const { error, value } = stateSchema.validate(req.body.state);

        if (error) {
            return res.status(400).json({ error: error.details[0].message });
        }

        // value тепер безпечний для використання
        await saveUserState(value);
        res.json({ success: true });
    } catch (err) {
        res.status(500).json({ error: 'Помилка збереження стану' });
    }
});

// Перевірка цілісності залежностей
// package-lock.json містить хеші для перевірки цілісності
// Використання npm ci замість npm install в CI/CD гарантує точні версії
```

Забезпечення цілісності вимагає використання цифрових підписів для оновлень, перевірки цілісності завантажених компонентів, уникнення небезпечної десеріалізації, використання захищених каналів для оновлень, впровадження CI/CD з перевірками безпеки.

#### A09: Security Logging and Monitoring Failures

**Недостатнє логування та моніторинг** роблять неможливим виявлення, ескалацію та реагування на активні атаки. Відсутність належного логування означає, що порушення може залишатися непоміченим тижнями або місяцями.

```javascript
// ВРАЗЛИВА РЕАЛІЗАЦІЯ: мінімальне логування
app.post('/api/login', async (req, res) => {
    const user = await authenticateUser(req.body.username, req.body.password);
    if (user) {
        res.json({ token: generateToken(user) });
    } else {
        res.status(401).json({ error: 'Невірні дані' });
    }
});

// БЕЗПЕЧНА РЕАЛІЗАЦІЯ: детальне логування безпеки
const winston = require('winston');

const securityLogger = winston.createLogger({
    level: 'info',
    format: winston.format.combine(
        winston.format.timestamp(),
        winston.format.json()
    ),
    transports: [
        new winston.transports.File({
            filename: 'security.log',
            level: 'warn'
        }),
        new winston.transports.File({
            filename: 'audit.log'
        })
    ]
});

app.post('/api/login', async (req, res) => {
    const { username, password } = req.body;
    const ipAddress = req.ip;
    const userAgent = req.get('user-agent');

    securityLogger.info('Login attempt', {
        username,
        ipAddress,
        userAgent,
        timestamp: new Date().toISOString()
    });

    const user = await authenticateUser(username, password);

    if (user) {
        securityLogger.info('Successful login', {
            userId: user.id,
            username,
            ipAddress,
            userAgent
        });

        res.json({ token: generateToken(user) });
    } else {
        securityLogger.warn('Failed login attempt', {
            username,
            ipAddress,
            userAgent,
            reason: 'Invalid credentials'
        });

        res.status(401).json({ error: 'Невірні облікові дані' });
    }
});

// Моніторинг підозрілої активності
class SecurityMonitor {
    constructor() {
        this.suspiciousActivity = new Map();
        this.THRESHOLD = 10;
        this.TIME_WINDOW = 60000; // 1 хвилина
    }

    recordActivity(identifier, activityType) {
        const key = `${identifier}:${activityType}`;
        const now = Date.now();

        if (!this.suspiciousActivity.has(key)) {
            this.suspiciousActivity.set(key, []);
        }

        const activities = this.suspiciousActivity.get(key);
        activities.push(now);

        // Видаляємо старі записи
        const recent = activities.filter(time => now - time < this.TIME_WINDOW);
        this.suspiciousActivity.set(key, recent);

        // Перевіряємо, чи перевищено поріг
        if (recent.length >= this.THRESHOLD) {
            this.alertSuspiciousActivity(identifier, activityType, recent.length);
        }
    }

    alertSuspiciousActivity(identifier, activityType, count) {
        securityLogger.error('Suspicious activity detected', {
            identifier,
            activityType,
            count,
            timestamp: new Date().toISOString(),
            severity: 'high'
        });

        // Можна додати відправку сповіщень адміністраторам
    }
}

const monitor = new SecurityMonitor();

app.post('/api/login', async (req, res) => {
    monitor.recordActivity(req.ip, 'login_attempt');
    // Решта логіки
});
```

Ефективне логування включає запис всіх подій безпеки, збереження логів у захищеному місці, налаштування алертів для підозрілої активності, регулярний аналіз логів, централізоване управління логами для розподілених систем.

#### A10: Server-Side Request Forgery

**Підробка запитів на стороні сервера** виникає, коли вебзастосунок отримує віддалений ресурс без валідації URL, наданого користувачем. Це дозволяє зловмисникові змусити застосунок надсилати запити до неочікуваного призначення, навіть якщо воно захищене брандмауером або VPN.

```javascript
// ВРАЗЛИВИЙ КОД: некерований SSRF
app.post('/api/fetch-url', async (req, res) => {
    const { url } = req.body;

    // Небезпечно: дозволяє запити до будь-якого URL
    const response = await fetch(url);
    const data = await response.text();

    res.send(data);
});

// БЕЗПЕЧНИЙ КОД: валідація та обмеження URL
const url = require('url');

const ALLOWED_DOMAINS = ['api.example.com', 'cdn.example.com'];
const ALLOWED_PROTOCOLS = ['https:'];
const BLOCKED_IPS = ['127.0.0.1', 'localhost', '0.0.0.0'];

const validateURL = (inputUrl) => {
    try {
        const parsed = new URL(inputUrl);

        // Перевірка протоколу
        if (!ALLOWED_PROTOCOLS.includes(parsed.protocol)) {
            return { valid: false, error: 'Дозволено тільки HTTPS' };
        }

        // Перевірка домену
        if (!ALLOWED_DOMAINS.includes(parsed.hostname)) {
            return { valid: false, error: 'Недозволений домен' };
        }

        // Перевірка на спробу доступу до локальних ресурсів
        if (BLOCKED_IPS.includes(parsed.hostname)) {
            return { valid: false, error: 'Доступ заборонено' };
        }

        return { valid: true, url: parsed.href };
    } catch (err) {
        return { valid: false, error: 'Некоректний URL' };
    }
};

app.post('/api/fetch-url', async (req, res) => {
    const { url: inputUrl } = req.body;

    const validation = validateURL(inputUrl);

    if (!validation.valid) {
        return res.status(400).json({ error: validation.error });
    }

    try {
        const response = await fetch(validation.url, {
            timeout: 5000, // Обмеження часу запиту
            follow: 0, // Заборона редіректів
            size: 1024 * 1024 // Обмеження розміру відповіді до 1MB
        });

        const data = await response.text();
        res.send(data);
    } catch (err) {
        res.status(500).json({ error: 'Помилка отримання даних' });
    }
});
```

## Input Sanitization та Validation

### Філософія валідації даних

Валідація та санітизація вхідних даних є критичною лінією захисту в будь-якому вебзастосунку. Основний принцип полягає в тому, що всі дані від зовнішніх джерел потенційно небезпечні та повинні проходити ретельну перевірку перед використанням. Існує фундамен тальна різниця між валідацією та санітизацією. Валідація перевіряє, чи відповідають дані очікуваному формату та обмеженням, тоді як санітизація видаляє або екранує потенційно небезпечні символи та конструкції.

### Типи валідації

**Whitelist validation** дозволяє тільки відомі безпечні значення та є найбільш надійним підходом. Замість перевірки на присутність небезпечних символів, ми дозволяємо тільки ті символи та формати, які є абсолютно необхідними.

**Blacklist validation** блокує відомі небезпечні значення, але цей підхід менш надійний, оскільки неможливо передбачити всі можливі варіанти шкідливого вводу.

**Format validation** перевіряє структуру даних за допомогою регулярних виразів або спеціалізованих валідаторів.

**Business logic validation** перевіряє дані відповідно до бізнес-правил застосунку.

```javascript
// Комплексна система валідації з використанням Joi
const Joi = require('joi');

// Схема для реєстрації користувача
const userRegistrationSchema = Joi.object({
    username: Joi.string()
        .alphanum()
        .min(3)
        .max(30)
        .required()
        .messages({
            'string.alphanum': "Ім'я користувача може містити тільки букви та цифри",
            'string.min': "Ім'я користувача повинно містити мінімум 3 символи",
            'string.max': "Ім'я користувача не може перевищувати 30 символів"
        }),

    email: Joi.string()
        .email({ minDomainSegments: 2 })
        .required()
        .messages({
            'string.email': 'Некоректний формат електронної пошти'
        }),

    password: Joi.string()
        .min(12)
        .pattern(new RegExp('^(?=.*[a-z])(?=.*[A-Z])(?=.*\\d)(?=.*[@$!%*?&])[A-Za-z\\d@$!%*?&]'))
        .required()
        .messages({
            'string.min': 'Пароль повинен містити мінімум 12 символів',
            'string.pattern.base': 'Пароль повинен містити великі та малі літери, цифри та спеціальні символи'
        }),

    age: Joi.number()
        .integer()
        .min(18)
        .max(120)
        .required()
        .messages({
            'number.min': 'Вік повинен бути не менше 18 років'
        }),

    website: Joi.string()
        .uri({ scheme: ['http', 'https'] })
        .optional(),

    phoneNumber: Joi.string()
        .pattern(new RegExp('^\\+?[1-9]\\d{1,14}$'))
        .optional()
        .messages({
            'string.pattern.base': 'Некоректний формат телефону'
        })
});

// Middleware для валідації
const validate = (schema) => {
    return (req, res, next) => {
        const { error, value } = schema.validate(req.body, {
            abortEarly: false, // Повертати всі помилки, не тільки першу
            stripUnknown: true // Видаляти невідомі поля
        });

        if (error) {
            const errors = error.details.map(detail => ({
                field: detail.path.join('.'),
                message: detail.message
            }));

            return res.status(400).json({
                error: 'Помилка валідації',
                details: errors
            });
        }

        req.validatedData = value;
        next();
    };
};

// Використання валідації в маршруті
app.post('/api/register',
    validate(userRegistrationSchema),
    async (req, res) => {
        const userData = req.validatedData;
        // Дані вже провалідовані та безпечні для використання
        const user = await createUser(userData);
        res.json({ success: true, userId: user.id });
    }
);
```

### Санітизація даних

```javascript
// Комплексна система санітизації
const validator = require('validator');
const DOMPurify = require('isomorphic-dompurify');

class DataSanitizer {
    // Санітизація HTML контенту
    static sanitizeHTML(html) {
        return DOMPurify.sanitize(html, {
            ALLOWED_TAGS: ['p', 'br', 'strong', 'em', 'u', 'a', 'ul', 'ol', 'li'],
            ALLOWED_ATTR: ['href', 'target'],
            ALLOW_DATA_ATTR: false
        });
    }

    // Санітизація для SQL (хоча краще використовувати ORM)
    static sanitizeForSQL(input) {
        if (typeof input !== 'string') {
            return input;
        }

        // Екранування спеціальних символів
        return input
            .replace(/'/g, "''")
            .replace(/\\/g, '\\\\')
            .replace(/\0/g, '\\0');
    }

    // Санітизація URL
    static sanitizeURL(url) {
        if (!validator.isURL(url, { protocols: ['http', 'https'] })) {
            throw new Error('Некоректний URL');
        }

        // Видалення JavaScript протоколу та інших небезпечних елементів
        const cleaned = url.replace(/javascript:/gi, '').replace(/data:/gi, '');

        return validator.escape(cleaned);
    }

    // Санітизація імені файлу
    static sanitizeFileName(filename) {
        // Видалення шляхів та небезпечних символів
        return filename
            .replace(/^.*[\\\/]/, '') // Видалити шлях
            .replace(/[^\w\s.-]/gi, '') // Дозволити тільки букви, цифри, пробіли, крапки та дефіси
            .replace(/\s+/g, '_') // Замінити пробіли на підкреслення
            .toLowerCase();
    }

    // Санітизація JSON для запобігання prototype pollution
    static sanitizeJSON(obj) {
        const cleaned = {};

        for (const key in obj) {
            if (key === '__proto__' || key === 'constructor' || key === 'prototype') {
                continue; // Пропустити небезпечні ключі
            }

            if (typeof obj[key] === 'object' && obj[key] !== null) {
                cleaned[key] = this.sanitizeJSON(obj[key]);
            } else {
                cleaned[key] = obj[key];
            }
        }

        return cleaned;
    }

    // Універсальна санітизація тексту
    static sanitizeText(text, options = {}) {
        if (typeof text !== 'string') {
            return text;
        }

        let sanitized = text;

        // Видалення контрольних символів
        sanitized = sanitized.replace(/[\x00-\x1F\x7F]/g, '');

        // Обмеження довжини
        if (options.maxLength) {
            sanitized = sanitized.substring(0, options.maxLength);
        }

        // Видалення зайвих пробілів
        if (options.trim !== false) {
            sanitized = sanitized.trim().replace(/\s+/g, ' ');
        }

        // HTML escape якщо потрібно
        if (options.escapeHTML) {
            sanitized = validator.escape(sanitized);
        }

        return sanitized;
    }
}

// Приклад використання
app.post('/api/posts', async (req, res) => {
    const { title, content, url, attachmentName } = req.body;

    const sanitizedData = {
        title: DataSanitizer.sanitizeText(title, {
            maxLength: 200,
            escapeHTML: true
        }),
        content: DataSanitizer.sanitizeHTML(content),
        url: DataSanitizer.sanitizeURL(url),
        attachmentName: DataSanitizer.sanitizeFileName(attachmentName)
    };

    await createPost(sanitizedData);
    res.json({ success: true });
});
```

## SQL Injection та NoSQL Injection

### SQL Injection: механізм атаки

SQL Injection залишається однією з найнебезпечніших вразливостей, незважаючи на те, що методи захисту відомі вже десятиліттями. Атака відбувається, коли зловмисник вставляє шкідливий SQL код у вхідні дані, які потім використовуються для побудови SQL запиту без належної валідації або екранування.

```javascript
// КРИТИЧНО ВРАЗЛИВИЙ КОД: пряма конкатенація в SQL
const getUserData = async (userId) => {
    const query = `SELECT * FROM users WHERE id = ${userId}`;
    // Якщо userId = "1 OR 1=1", повернуться всі користувачі
    // Якщо userId = "1; DROP TABLE users; --", буде видалено таблицю
    return await db.query(query);
};

// ВРАЗЛИВИЙ КОД: конкатенація рядків навіть з перевіркою типу
const searchUsers = async (searchTerm) => {
    // Навіть якщо перевіряємо тип, все одно небезпечно
    if (typeof searchTerm !== 'string') {
        throw new Error('Invalid input');
    }

    const query = `SELECT * FROM users WHERE name LIKE '%${searchTerm}%'`;
    // searchTerm = "'; DELETE FROM users WHERE '1'='1"
    return await db.query(query);
};

// БЕЗПЕЧНИЙ КОД: параметризовані запити
const mysql = require('mysql2/promise');

const pool = mysql.createPool({
    host: process.env.DB_HOST,
    user: process.env.DB_USER,
    password: process.env.DB_PASSWORD,
    database: process.env.DB_NAME
});

const getUserData = async (userId) => {
    const query = 'SELECT * FROM users WHERE id = ?';
    const [rows] = await pool.execute(query, [userId]);
    return rows;
};

const searchUsers = async (searchTerm) => {
    const query = 'SELECT * FROM users WHERE name LIKE ?';
    const [rows] = await pool.execute(query, [`%${searchTerm}%`]);
    return rows;
};

// НАЙБЕЗПЕЧНІШИЙ ПІДХІД: використання ORM Prisma
const getUserDataWithPrisma = async (userId) => {
    return await prisma.user.findUnique({
        where: { id: parseInt(userId) }
    });
};

const searchUsersWithPrisma = async (searchTerm) => {
    return await prisma.user.findMany({
        where: {
            name: {
                contains: searchTerm
            }
        }
    });
};
```

### NoSQL Injection: специфіка MongoDB

NoSQL бази даних, хоча й не використовують традиційний SQL, також схильні до ін'єкційних атак через те, як вони обробляють об'єктні запити.

```javascript
// ВРАЗЛИВИЙ КОД: небезпечні запити MongoDB
const findUser = async (username, password) => {
    // Якщо username = {"$gt": ""} та password = {"$gt": ""},
    // це поверне будь-якого користувача
    return await User.findOne({ username, password });
};

// Атака через HTTP запит:
// POST /api/login
// { "username": {"$gt": ""}, "password": {"$gt": ""} }

// БЕЗПЕЧНИЙ КОД: валідація типів та санітизація
const findUser = async (username, password) => {
    // Перевірка типів
    if (typeof username !== 'string' || typeof password !== 'string') {
        throw new Error('Некоректний тип даних');
    }

    // Видалення MongoDB операторів
    const sanitizedUsername = username.replace(/[.$]/g, '');

    // Використання точних збігів
    return await User.findOne({
        username: { $eq: sanitizedUsername },
        password: { $eq: password }
    });
};

// КРАЩИЙ ПІДХІД: використання спеціалізованих бібліотек
const mongoSanitize = require('express-mongo-sanitize');

// Middleware для санітизації всіх запитів
app.use(mongoSanitize({
    replaceWith: '_', // Замінити заборонені символи
    onSanitize: ({ req, key }) => {
        console.warn(`Спроба NoSQL ін'єкції виявлена в ${key}`);
    }
}));

// Детальна валідація з Joi для MongoDB запитів
const Joi = require('joi');

const loginSchema = Joi.object({
    username: Joi.string()
        .alphanum()
        .min(3)
        .max(30)
        .required(),
    password: Joi.string()
        .min(8)
        .required()
});

app.post('/api/login', async (req, res) => {
    const { error, value } = loginSchema.validate(req.body);

    if (error) {
        return res.status(400).json({ error: 'Некоректні дані' });
    }

    const user = await findUser(value.username, value.password);

    if (!user) {
        return res.status(401).json({ error: 'Невірні облікові дані' });
    }

    res.json({ token: generateToken(user) });
});
```

### Додаткові методи захисту від ін'єкцій

```javascript
// Stored Procedures для додаткового рівня захисту в SQL
const callStoredProcedure = async (userId) => {
    const query = 'CALL GetUserData(?)';
    const [rows] = await pool.execute(query, [userId]);
    return rows;
};

// Обмеження привілеїв бази даних
// CREATE USER 'app_user'@'localhost' IDENTIFIED BY 'password';
// GRANT SELECT, INSERT, UPDATE ON app_database.* TO 'app_user'@'localhost';
// Не надавати DROP, ALTER та інші небезпечні привілеї

// Додаткова валідація на рівні бази даних
const createUserWithConstraints = async (userData) => {
    // Використання database constraints для додаткової валідації
    const query = `
        INSERT INTO users (username, email, age)
        VALUES (?, ?, ?)
    `;
    // База даних має CHECK constraints:
    // CHECK (age >= 18 AND age <= 120)
    // CHECK (email LIKE '%@%.%')

    try {
        await pool.execute(query, [
            userData.username,
            userData.email,
            userData.age
        ]);
    } catch (err) {
        if (err.code === 'ER_CHECK_CONSTRAINT_VIOLATED') {
            throw new Error('Дані не відповідають вимогам');
        }
        throw err;
    }
};
```

## XSS та CSRF атаки

### Cross-Site Scripting: типи та механізми

**Cross-Site Scripting** дозволяє зловмисникам впроваджувати шкідливі скрипти в вебсторінки, які переглядаються іншими користувачами. Існує три основні типи XSS атак, кожен з яких вимагає специфічного підходу до захисту.

#### Reflected XSS

Reflected XSS виникає, коли шкідливий скрипт відображається на сторінці як частина запиту користувача без належної санітизації.

```javascript
// ВРАЗЛИВИЙ КОД: відображення користувацького вводу без санітизації
app.get('/search', (req, res) => {
    const query = req.query.q;
    res.send(`<h1>Результати пошуку для: ${query}</h1>`);
    // URL: /search?q=<script>alert('XSS')</script>
    // Скрипт виконається в браузері користувача
});

// БЕЗПЕЧНИЙ КОД: екранування HTML
const escapeHTML = (str) => {
    const map = {
        '&': '&amp;',
        '<': '&lt;',
        '>': '&gt;',
        '"': '&quot;',
        "'": '&#x27;',
        '/': '&#x2F;'
    };

    return str.replace(/[&<>"'/]/g, char => map[char]);
};

app.get('/search', (req, res) => {
    const query = escapeHTML(req.query.q || '');
    res.send(`<h1>Результати пошуку для: ${query}</h1>`);
});

// КРАЩИЙ ПІДХІД: використання шаблонізаторів з автоматичним екрануванням
const ejs = require('ejs');

app.get('/search', (req, res) => {
    res.render('search', { query: req.query.q });
    // В шаблоні: <h1>Результати пошуку для: <%= query %></h1>
    // <%= автоматично екранує HTML
});
```

#### Stored XSS

Stored XSS зберігає шкідливий код в базі даних, який потім виконується кожного разу, коли дані відображаються користувачам.

```javascript
// ВРАЗЛИВИЙ КОД: збереження та відображення без санітизації
app.post('/api/comments', async (req, res) => {
    const { text } = req.body;
    await Comment.create({ text }); // Зберігаємо без перевірки
});

app.get('/api/comments', async (req, res) => {
    const comments = await Comment.findAll();
    res.json(comments); // Frontend може відобразити це через innerHTML
});

// БЕЗПЕЧНИЙ КОД: санітизація при збереженні та відображенні
const DOMPurify = require('isomorphic-dompurify');

app.post('/api/comments', async (req, res) => {
    const { text } = req.body;

    // Санітизація HTML, дозволяючи тільки безпечні теги
    const sanitizedText = DOMPurify.sanitize(text, {
        ALLOWED_TAGS: ['p', 'br', 'strong', 'em'],
        ALLOWED_ATTR: []
    });

    await Comment.create({ text: sanitizedText });
    res.json({ success: true });
});

// На frontend використовуємо textContent замість innerHTML
// document.getElementById('comment').textContent = comment.text;
```

#### DOM-based XSS

DOM-based XSS виникає повністю на клієнтській стороні через небезпечне використання JavaScript для маніпуляції DOM.

```javascript
// ВРАЗЛИВИЙ КОД на frontend
const displayMessage = () => {
    const message = new URLSearchParams(window.location.search).get('msg');
    document.getElementById('display').innerHTML = message;
    // URL: /?msg=<img src=x onerror=alert('XSS')>
};

// БЕЗПЕЧНИЙ КОД на frontend
const displayMessage = () => {
    const message = new URLSearchParams(window.location.search).get('msg');
    document.getElementById('display').textContent = message;
    // textContent автоматично екранує HTML
};

// АБО використання санітизації
import DOMPurify from 'dompurify';

const displayMessage = () => {
    const message = new URLSearchParams(window.location.search).get('msg');
    const sanitized = DOMPurify.sanitize(message);
    document.getElementById('display').innerHTML = sanitized;
};
```

### Content Security Policy

**Content Security Policy** є потужним механізмом захисту від XSS атак через визначення білого списку джерел, з яких браузер може завантажувати ресурси.

```javascript
// Налаштування CSP за допомогою helmet
const helmet = require('helmet');

app.use(helmet.contentSecurityPolicy({
    directives: {
        // Дозволити завантаження скриптів тільки з власного домену
        defaultSrc: ["'self'"],

        // Скрипти
        scriptSrc: [
            "'self'",
            "'nonce-random123'", // Використання nonce для inline скриптів
            "https://trusted-cdn.com"
        ],

        // Стилі
        styleSrc: [
            "'self'",
            "'unsafe-inline'", // Дозволити inline стилі (використовувати обережно)
            "https://fonts.googleapis.com"
        ],

        // Зображення
        imgSrc: [
            "'self'",
            "data:",
            "https:"
        ],

        // Шрифти
        fontSrc: [
            "'self'",
            "https://fonts.gstatic.com"
        ],

        // Відключення object та embed
        objectSrc: ["'none'"],

        // Frames
        frameSrc: ["'none'"],

        // Base URI обмеження
        baseUri: ["'self'"],

        // Form actions
        formAction: ["'self'"],

        // Upgrade insecure requests
        upgradeInsecureRequests: []
    }
}));

// Використання nonce в HTML
app.get('/', (req, res) => {
    const nonce = crypto.randomBytes(16).toString('base64');

    res.set('Content-Security-Policy',
        `script-src 'self' 'nonce-${nonce}'`);

    res.send(`
        <!DOCTYPE html>
        <html>
        <head>
            <script nonce="${nonce}">
                // Цей скрипт дозволений
                console.log('Безпечний скрипт');
            </script>
        </head>
        <body>
            <h1>Захищена сторінка</h1>
        </body>
        </html>
    `);
});
```

### Cross-Site Request Forgery: механізм та захист

**CSRF** атаки змушують автентифікованого користувача виконувати небажані дії в вебзастосунку. Атака використовує факт, що браузер автоматично включає cookies в запити.

```javascript
// Приклад CSRF атаки:
// Зловмисник створює сторінку з формою:
// <form action="https://bank.com/transfer" method="POST">
//   <input name="to" value="attacker_account">
//   <input name="amount" value="10000">
// </form>
// <script>document.forms[0].submit();</script>
// Якщо жертва увійшла в bank.com, запит виконається автоматично

// ЗАХИСТ 1: CSRF токени
const csrf = require('csurf');
const cookieParser = require('cookie-parser');

app.use(cookieParser());

const csrfProtection = csrf({
    cookie: {
        httpOnly: true,
        secure: process.env.NODE_ENV === 'production',
        sameSite: 'strict'
    }
});

// Генерація форми з CSRF токеном
app.get('/transfer', csrfProtection, (req, res) => {
    res.render('transfer', { csrfToken: req.csrfToken() });
});

// В HTML формі:
// <form method="POST" action="/transfer">
//   <input type="hidden" name="_csrf" value="<%= csrfToken %>">
//   ...
// </form>

// Перевірка токену при обробці форми
app.post('/transfer', csrfProtection, async (req, res) => {
    // Токен автоматично перевіряється middleware
    await processTransfer(req.body);
    res.json({ success: true });
});

// ЗАХИСТ 2: SameSite cookies
app.use(session({
    secret: process.env.SESSION_SECRET,
    cookie: {
        httpOnly: true,
        secure: process.env.NODE_ENV === 'production',
        sameSite: 'strict', // або 'lax'
        maxAge: 3600000
    }
}));

// ЗАХИСТ 3: Верифікація Origin та Referer headers
const verifyOrigin = (req, res, next) => {
    const origin = req.get('origin');
    const referer = req.get('referer');
    const allowedOrigins = [process.env.SITE_URL];

    if (!origin || !allowedOrigins.includes(origin)) {
        return res.status(403).json({ error: 'Заборонений origin' });
    }

    next();
};

app.post('/api/sensitive-action', verifyOrigin, async (req, res) => {
    // Виконуємо дію
});

// ЗАХИСТ 4: Custom headers для AJAX запитів
app.use((req, res, next) => {
    if (req.method !== 'GET' && req.method !== 'HEAD') {
        const customHeader = req.get('X-Requested-With');

        if (customHeader !== 'XMLHttpRequest') {
            return res.status(403).json({
                error: 'Відсутній обов\'язковий заголовок'
            });
        }
    }

    next();
});

// На frontend додаємо заголовок
fetch('/api/transfer', {
    method: 'POST',
    headers: {
        'Content-Type': 'application/json',
        'X-Requested-With': 'XMLHttpRequest'
    },
    body: JSON.stringify(transferData)
});
```

## Security Headers та Content Security Policy

### Критичні HTTP заголовки безпеки

HTTP заголовки безпеки є простим але ефективним способом захисту вебзастосунків від різних векторів атак. Правильна конфігурація цих заголовків може значно підвищити рівень безпеки без змін у логіці застосунку.

```javascript
const helmet = require('helmet');

// Комплексна конфігурація безпекових заголовків
app.use(helmet({
    // Content Security Policy
    contentSecurityPolicy: {
        directives: {
            defaultSrc: ["'self'"],
            scriptSrc: ["'self'", "'unsafe-inline'"],
            styleSrc: ["'self'", "'unsafe-inline'"],
            imgSrc: ["'self'", "data:", "https:"],
            connectSrc: ["'self'"],
            fontSrc: ["'self'"],
            objectSrc: ["'none'"],
            mediaSrc: ["'self'"],
            frameSrc: ["'none'"]
        }
    },

    // HTTP Strict Transport Security
    hsts: {
        maxAge: 31536000, // 1 рік
        includeSubDomains: true,
        preload: true
    },

    // X-Frame-Options
    frameguard: {
        action: 'deny' // Або 'sameorigin'
    },

    // X-Content-Type-Options
    noSniff: true,

    // X-XSS-Protection (застарілий, але корисний для старих браузерів)
    xssFilter: true,

    // Referrer-Policy
    referrerPolicy: {
        policy: 'strict-origin-when-cross-origin'
    },

    // Permissions-Policy (раніше Feature-Policy)
    permissionsPolicy: {
        features: {
            geolocation: ["'self'"],
            microphone: ["'none'"],
            camera: ["'none'"],
            payment: ["'self'"]
        }
    }
}));

// Додаткові кастомні заголовки
app.use((req, res, next) => {
    // Видалення заголовків, що розкривають інформацію про сервер
    res.removeHeader('X-Powered-By');

    // Додавання кастомних заголовків безпеки
    res.setHeader('X-DNS-Prefetch-Control', 'off');
    res.setHeader('Expect-CT', 'max-age=86400, enforce');

    next();
});
```

### Детальна конфігурація CSP

```javascript
// Прогресивне впровадження CSP
const generateCSP = (mode = 'report') => {
    const basePolicy = {
        defaultSrc: ["'self'"],
        scriptSrc: [
            "'self'",
            // Nonce для inline скриптів
            (req, res) => `'nonce-${res.locals.nonce}'`
        ],
        styleSrc: ["'self'", "'unsafe-inline'"],
        imgSrc: ["'self'", "data:", "https:"],
        fontSrc: ["'self'", "https://fonts.gstatic.com"],
        connectSrc: ["'self'", "https://api.example.com"],
        frameSrc: ["'none'"],
        objectSrc: ["'none'"],
        baseUri: ["'self'"],
        formAction: ["'self'"],
        upgradeInsecureRequests: []
    };

    if (mode === 'report') {
        basePolicy.reportUri = '/api/csp-report';
    }

    return basePolicy;
};

// Middleware для генерації nonce
app.use((req, res, next) => {
    res.locals.nonce = crypto.randomBytes(16).toString('base64');
    next();
});

// Застосування CSP
app.use(helmet.contentSecurityPolicy({
    directives: generateCSP('enforce')
}));

// Endpoint для отримання звітів про порушення CSP
app.post('/api/csp-report',
    express.json({ type: 'application/csp-report' }),
    (req, res) => {
        const report = req.body['csp-report'];

        console.log('CSP Violation:', {
            documentUri: report['document-uri'],
            violatedDirective: report['violated-directive'],
            blockedUri: report['blocked-uri'],
            sourceFile: report['source-file'],
            lineNumber: report['line-number']
        });

        // Можна зберігати в базу даних для аналізу
        res.status(204).send();
    }
);
```

## Penetration Testing основи

### Методологія тестування безпеки

**Penetration Testing** є симульованою кібератакою на систему для виявлення вразливостей, які можуть бути використані зловмисниками. Тестування проводиться з дозволу власника системи та має на меті покращення безпеки.

### Фази пентестингу

**Reconnaissance** включає збір інформації про ціль, включаючи технології, що використовуються, структуру додатку, публічну інформацію про домени та IP адреси.

**Scanning** передбачає активне сканування цілі для виявлення відкритих портів, версій програмного забезпечення, потенційних точок входу.

**Gaining Access** включає експлуатацію виявлених вразливостей для отримання доступу до системи.

**Maintaining Access** тестує, чи можливо зберегти доступ до скомпрометованої системи.

**Analysis** передбачає документування всіх виявлених вразливостей, їх серйозності та рекомендацій щодо виправлення.

### Інструменти для тестування безпеки

```javascript
// Автоматизоване тестування безпеки в CI/CD
// .github/workflows/security-testing.yml
// name: Security Testing
// on: [push, pull_request]
// jobs:
//   security-scan:
//     runs-on: ubuntu-latest
//     steps:
//       - uses: actions/checkout@v3
//
//       - name: OWASP Dependency Check
//         run: |
//           npm audit
//           npm audit fix --force
//
//       - name: SAST with ESLint Security Plugin
//         run: |
//           npm install --save-dev eslint-plugin-security
//           npx eslint . --ext .js
//
//       - name: ZAP Baseline Scan
//         uses: zaproxy/action-baseline@v0.7.0
//         with:
//           target: 'http://localhost:3000'

// Інтеграція безпекових тестів в код
const request = require('supertest');
const app = require('../app');

describe('Security Tests', () => {
    test('Має містити CSP заголовок', async () => {
        const response = await request(app).get('/');
        expect(response.headers['content-security-policy']).toBeDefined();
    });

    test('Має захищати від XSS', async () => {
        const xssPayload = '<script>alert("XSS")</script>';
        const response = await request(app)
            .get(`/search?q=${encodeURIComponent(xssPayload)}`);

        expect(response.text).not.toContain('<script>');
        expect(response.text).toContain('&lt;script&gt;');
    });

    test('Має захищати від SQL ін\'єкції', async () => {
        const sqlPayload = "' OR '1'='1";
        const response = await request(app)
            .post('/api/login')
            .send({ username: sqlPayload, password: 'test' });

        expect(response.status).toBe(401);
        expect(response.body.error).toBeDefined();
    });

    test('Має вимагати CSRF токен', async () => {
        const response = await request(app)
            .post('/api/transfer')
            .send({ to: 'attacker', amount: 1000 });

        expect(response.status).toBe(403);
    });

    test('Має обмежувати rate of requests', async () => {
        const requests = Array(10).fill(null).map(() =>
            request(app).post('/api/login').send({
                username: 'test',
                password: 'wrong'
            })
        );

        const responses = await Promise.all(requests);
        const rateLimited = responses.some(r => r.status === 429);

        expect(rateLimited).toBe(true);
    });
});
```

### Практичні рекомендації для безпечної розробки

```javascript
// Чек-лист безпеки для вебзастосунку
class SecurityChecklist {
    static async performSecurityAudit(app) {
        const results = {
            passed: [],
            failed: [],
            warnings: []
        };

        // Перевірка HTTPS
        if (process.env.NODE_ENV === 'production' && !app.get('trust proxy')) {
            results.failed.push('HTTPS не налаштовано для продакшн');
        } else {
            results.passed.push('HTTPS налаштовано');
        }

        // Перевірка змінних середовища
        const requiredEnvVars = [
            'DATABASE_URL',
            'JWT_SECRET',
            'SESSION_SECRET'
        ];

        requiredEnvVars.forEach(varName => {
            if (!process.env[varName]) {
                results.failed.push(`Відсутня змінна середовища: ${varName}`);
            } else if (process.env[varName].length < 32) {
                results.warnings.push(`Слабкий секрет: ${varName}`);
            } else {
                results.passed.push(`Секрет налаштовано: ${varName}`);
            }
        });

        // Перевірка rate limiting
        const hasRateLimiting = app._router.stack.some(
            layer => layer.name === 'rateLimit'
        );

        if (!hasRateLimiting) {
            results.failed.push('Rate limiting не налаштовано');
        } else {
            results.passed.push('Rate limiting активовано');
        }

        // Перевірка CORS
        const hasCORS = app._router.stack.some(
            layer => layer.name === 'corsMiddleware'
        );

        if (!hasCORS && process.env.NODE_ENV === 'production') {
            results.warnings.push('CORS не налаштовано');
        }

        // Перевірка helmet
        const hasHelmet = app._router.stack.some(
            layer => layer.name === 'helmet'
        );

        if (!hasHelmet) {
            results.failed.push('Helmet middleware не підключено');
        } else {
            results.passed.push('Helmet активовано');
        }

        return results;
    }

    static generateReport(results) {
        console.log('\n========== SECURITY AUDIT REPORT ==========\n');

        console.log('✓ PASSED:');
        results.passed.forEach(item => console.log(`  - ${item}`));

        if (results.warnings.length > 0) {
            console.log('\n⚠ WARNINGS:');
            results.warnings.forEach(item => console.log(`  - ${item}`));
        }

        if (results.failed.length > 0) {
            console.log('\n✗ FAILED:');
            results.failed.forEach(item => console.log(`  - ${item}`));
            console.log('\n⚠ КРИТИЧНІ ПРОБЛЕМИ ВИЯВЛЕНО!\n');
            process.exit(1);
        } else {
            console.log('\n✓ Всі перевірки пройдено успішно!\n');
        }
    }
}

// Запуск аудиту при старті застосунку
if (process.env.NODE_ENV !== 'test') {
    SecurityChecklist.performSecurityAudit(app)
        .then(SecurityChecklist.generateReport)
        .catch(console.error);
}
```

## Висновки та найкращі практики

### Ключові принципи безпечної розробки

Безпека вебзастосунків є не одноразовою задачею, а постійним процесом, що вимагає уваги на всіх етапах розробки. **Security by Design** означає, що безпека має бути інтегрована в архітектуру з самого початку, а не додаватися пізніше як латка. Кожне рішення про архітектуру, кожна вибрана технологія, кожна реалізована функція повинна розглядатися через призму потенційних загроз безпеці.

**Defense in Depth** принцип вимагає створення багаторівневого захисту. Якщо один рівень скомпрометовано, інші рівні повинні запобігти успішній атаці. Це означає комбінування різних технік безпеки: валідації на клієнті та сервері, авторизації на рівні мережі та застосунку, шифрування при передачі та зберіганні, моніторингу та детекції аномалій.

**Principle of Least Privilege** диктує, що кожен компонент системи, користувач чи процес повинен мати мінімально необхідні привілеї для виконання своїх функцій. Це обмежує потенційний збиток від компрометації будь-якого компонента. Застосунок не повинен працювати з root привілеями, користувачі не повинні мати адміністративні права без необхідності, база даних повинна мати обмежені дозволи.

### Практичні рекомендації

Валідація та санітизація вхідних даних має бути всеохоплюючою та багаторівневою. Ніколи не довіряйте даним від користувача, незалежно від їх джерела. Використовуйте whitelist validation де можливо, санітизуйте всі дані перед використанням, застосовуйте контекстно-відповідне кодування при виводі даних.

Криптографія має використовуватися правильно та послідовно. Використовуйте перевірені алгоритми та бібліотеки, ніколи не створюйте власні криптографічні рішення, зберігайте ключі безпечно в спеціалізованих сховищах, регулярно оновлюйте криптографічні компоненти.

Автентифікація та авторизація потребують особливої уваги. Використовуйте багатофакторну автентифікацію для критичних операцій, впроваджуйте надійну систему управління сесіями, застосовуйте принцип найменших привілеїв для авторизації, регулярно переглядайте та оновлюйте правила доступу.

Логування та моніторинг є критично важливими для швидкого виявлення та реагування на інциденти безпеки. Логуйте всі події безпеки включаючи спроби доступу, зміни конфігурації, підозрілу активність, налаштуйте автоматичні алерти для критичних подій, регулярно аналізуйте логи для виявлення аномалій.

Регулярні оновлення та патчі є необхідністю в сучасному світі постійно еволюціонуючих загроз. Підтримуйте всі компоненти системи в актуальному стані, автоматизуйте процес моніторингу вразливостей, майте план швидкого реагування на критичні вразливості, регулярно проводьте аудити безпеки.

### Культура безпеки в команді

Безпека є відповідальністю кожного члена команди, а не тільки спеціалістів з безпеки. Розробники повинні розуміти основні принципи безпеки та постійно підвищувати свою кваліфікацію. Регулярні тренінги з безпеки, code review з фокусом на безпеку, автоматизовані перевірки безпеки в CI/CD пайплайні створюють культуру, де безпека є пріоритетом.

Проактивний підхід до безпеки передбачає регулярне тестування, моделювання загроз, проведення пентестів, участь у bug bounty програмах. Це дозволяє виявляти та виправляти вразливості до того, як вони будуть експлуатовані зловмисниками.

Розуміння OWASP Top 10 та регулярне його перегляд допомагає залишатися в курсі найактуальніших загроз. Використання інструментів безпеки, дотримання best practices, навчання на помилках інших організацій створює міцну основу для безпечної розробки.

Безпека вебзастосунків є комплексною задачею, що вимагає технічних знань, постійного навчання та дисципліни в дотриманні практик безпечної розробки. Інвестиції в безпеку на ранніх етапах розробки значно дешевші та ефективніші, ніж виправлення наслідків успішної атаки.
