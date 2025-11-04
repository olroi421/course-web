# Лекція 8. Тестування та деплой backend

## Вступ до тестування серверних застосунків

Тестування серверних застосунків є критично важливим етапом розробки, який забезпечує стабільність, надійність та якість програмного продукту. У сучасному світі розробки програмного забезпечення тестування не є опціональним етапом, а обов'язковою частиною процесу розробки.

Тестування backend застосунків має свої особливості, оскільки серверна логіка часто включає роботу з базами даних, зовнішніми API, файловими системами та іншими ресурсами, які потребують спеціальних підходів до тестування.

### Типи тестування в backend розробці

Тестування backend застосунків традиційно поділяється на кілька рівнів, які утворюють так звану "піраміду тестування":

```mermaid
graph TD
    A[Unit Tests] --> B[Integration Tests]
    B --> C[End-to-End Tests]

    subgraph "Швидкість виконання"
        D[Швидко] --> E[Помірно] --> F[Повільно]
    end

    subgraph "Кількість тестів"
        G[Багато] --> H[Помірно] --> I[Мало]
    end
```

**Unit тести** перевіряють окремі модулі або функції у ізоляції від інших компонентів системи. Вони є найшвидшими та найдешевшими у виконанні, тому їх має бути найбільше.

**Integration тести** перевіряють взаємодію між різними модулями системи, включаючи роботу з базами даних, зовнішніми сервісами та API.

**End-to-End тести** перевіряють повний робочий процес застосунку від початку до кінця, імітуючи реальні сценарії використання.

### Філософія Test-Driven Development (TDD)

Test-Driven Development є методологією розробки, яка передбачає написання тестів перед написанням самого коду. Цикл TDD складається з трьох етапів:

1. **Red**: написати тест, який не проходить
2. **Green**: написати мінімальний код, щоб тест пройшов
3. **Refactor**: покращити код, зберігаючи функціональність

Переваги TDD включають кращу архітектуру коду, вищу впевненість у змінах та автоматичну документацію поведінки системи через тести.

## Unit тестування з Jest

Jest є одним з найпопулярніших фреймворків для тестування JavaScript застосунків. Він розроблений Facebook (Meta) та забезпечує потужний набір інструментів для unit тестування з мінімальною конфігурацією.

### Основи Jest та його архітектура

Jest поставляється з великою кількістю вбудованих можливостей:

- **Test runner**: виконує тести та збирає результати
- **Assertion library**: надає методи для перевірки очікувань
- **Mocking framework**: дозволяє імітувати залежності
- **Code coverage**: аналізує покриття коду тестами
- **Snapshot testing**: перевіряє незмінність виводу

### Налаштування Jest для Node.js проєкту

Початкове налаштування Jest потребує встановлення пакету та конфігурації:

```bash
npm install --save-dev jest supertest
```

Конфігурація в `package.json`:

```json
{
  "scripts": {
    "test": "jest",
    "test:watch": "jest --watch",
    "test:coverage": "jest --coverage"
  },
  "jest": {
    "testEnvironment": "node",
    "collectCoverageFrom": [
      "src/**/*.js",
      "!src/server.js",
      "!**/node_modules/**"
    ],
    "coverageReporters": ["text", "lcov", "html"],
    "testMatch": ["**/__tests__/**/*.js", "**/?(*.)+(spec|test).js"]
  }
}
```

### Написання unit тестів для функцій

Unit тести перевіряють окремі функції або методи у повній ізоляції від інших компонентів системи. Вони мають бути швидкими, детермінованими та незалежними. Кожен unit тест зосереджується на одному аспекті поведінки функції.

При написанні unit тестів важливо дотримуватися принципу AAA (Arrange, Act, Assert): спочатку підготувати тестові дані, потім виконати дію, і нарешті перевірити результат. Також слід тестувати як позитивні сценарії (коли функція працює правильно), так і негативні (коли вона має обробляти помилкові дані).

Розглянемо приклад тестування утилітарних функцій валідації. Ці функції є ідеальними кандидатами для unit тестування, оскільки вони мають чіткі вхідні параметри та передбачувані результати:

```javascript
// src/utils/validation.js
function validateEmail(email) {
    const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    return emailRegex.test(email);
}

function validatePassword(password) {
    if (!password || password.length < 8) {
        return { isValid: false, message: "Пароль повинен містити принаймні 8 символів" };
    }

    if (!/[A-Z]/.test(password)) {
        return { isValid: false, message: "Пароль повинен містити принаймні одну велику літеру" };
    }

    if (!/[0-9]/.test(password)) {
        return { isValid: false, message: "Пароль повинен містити принаймні одну цифру" };
    }

    return { isValid: true, message: "Пароль валідний" };
}

function formatUserName(firstName, lastName) {
    if (!firstName || !lastName) {
        throw new Error("Ім'я та прізвище є обов'язковими");
    }

    return `${firstName.trim()} ${lastName.trim()}`;
}

module.exports = { validateEmail, validatePassword, formatUserName };
```

Тести для цих функцій:

```javascript
// __tests__/utils/validation.test.js
const { validateEmail, validatePassword, formatUserName } = require('../../src/utils/validation');

describe('Validation Utils', () => {
    describe('validateEmail', () => {
        test('повертає true для валідних email адрес', () => {
            const validEmails = [
                'test@example.com',
                'user.name@domain.co.uk',
                'test+tag@example.org'
            ];

            validEmails.forEach(email => {
                expect(validateEmail(email)).toBe(true);
            });
        });

        test('повертає false для невалідних email адрес', () => {
            const invalidEmails = [
                'invalid-email',
                '@example.com',
                'test@',
                'test..test@example.com'
            ];

            invalidEmails.forEach(email => {
                expect(validateEmail(email)).toBe(false);
            });
        });
    });

    describe('validatePassword', () => {
        test('повертає isValid: true для сильних паролів', () => {
            const strongPasswords = ['Password123', 'MySecure1', 'Test123456'];

            strongPasswords.forEach(password => {
                const result = validatePassword(password);
                expect(result.isValid).toBe(true);
                expect(result.message).toBe("Пароль валідний");
            });
        });

        test('повертає відповідні помилки для слабких паролів', () => {
            expect(validatePassword('short')).toEqual({
                isValid: false,
                message: "Пароль повинен містити принаймні 8 символів"
            });

            expect(validatePassword('lowercase123')).toEqual({
                isValid: false,
                message: "Пароль повинен містити принаймні одну велику літеру"
            });

            expect(validatePassword('NoNumbers')).toEqual({
                isValid: false,
                message: "Пароль повинен містити принаймні одну цифру"
            });
        });
    });

    describe('formatUserName', () => {
        test('правильно форматує ім\'я та прізвище', () => {
            expect(formatUserName('Іван', 'Петренко')).toBe('Іван Петренко');
            expect(formatUserName(' Марія ', ' Коваленко ')).toBe('Марія Коваленко');
        });

        test('викидає помилку для відсутніх параметрів', () => {
            expect(() => formatUserName('', 'Петренко')).toThrow("Ім'я та прізвище є обов'язковими");
            expect(() => formatUserName('Іван', '')).toThrow("Ім'я та прізвище є обов'язковими");
            expect(() => formatUserName()).toThrow("Ім'я та прізвище є обов'язковими");
        });
    });
});
```

### Тестування асинхронного коду

Асинхронний код в Node.js є повсюдним, тому Jest надає спеціальні можливості для його тестування. Основні підходи включають використання async/await, Promise chains або callback functions. Важливо правильно обробляти як успішні результати, так і помилки.

При тестуванні асинхронних функцій критично важливо використовувати моки (mocks) для імітації зовнішніх залежностей як бази даних, HTTP запитів або файлової системи. Це забезпечує швидкість виконання тестів та їх незалежність від зовнішніх ресурсів.

Розглянемо приклад тестування сервісу, який працює з користувачами та виконує асинхронні операції:

```javascript
// src/services/userService.js
const bcrypt = require('bcrypt');

class UserService {
    constructor(userRepository) {
        this.userRepository = userRepository;
    }

    async createUser(userData) {
        const { email, password, firstName, lastName } = userData;

        // Перевірка існування користувача
        const existingUser = await this.userRepository.findByEmail(email);
        if (existingUser) {
            throw new Error('Користувач з таким email вже існує');
        }

        // Хешування пароля
        const hashedPassword = await bcrypt.hash(password, 12);

        // Створення користувача
        const user = await this.userRepository.create({
            email,
            password: hashedPassword,
            firstName,
            lastName,
            createdAt: new Date()
        });

        // Повернення без пароля
        const { password: _, ...userWithoutPassword } = user;
        return userWithoutPassword;
    }

    async authenticateUser(email, password) {
        const user = await this.userRepository.findByEmail(email);
        if (!user) {
            return null;
        }

        const isValidPassword = await bcrypt.compare(password, user.password);
        if (!isValidPassword) {
            return null;
        }

        const { password: _, ...userWithoutPassword } = user;
        return userWithoutPassword;
    }
}

module.exports = UserService;
```

Тести для асинхронного сервісу:

```javascript
// __tests__/services/userService.test.js
const UserService = require('../../src/services/userService');
const bcrypt = require('bcrypt');

// Мок для bcrypt
jest.mock('bcrypt');

describe('UserService', () => {
    let userService;
    let mockUserRepository;

    beforeEach(() => {
        // Створення mock об'єкту репозиторію
        mockUserRepository = {
            findByEmail: jest.fn(),
            create: jest.fn()
        };

        userService = new UserService(mockUserRepository);

        // Очищення всіх моків перед кожним тестом
        jest.clearAllMocks();
    });

    describe('createUser', () => {
        const userData = {
            email: 'test@example.com',
            password: 'Password123',
            firstName: 'Іван',
            lastName: 'Петренко'
        };

        test('успішно створює користувача', async () => {
            // Arrange
            mockUserRepository.findByEmail.mockResolvedValue(null);
            bcrypt.hash.mockResolvedValue('hashed-password');
            const createdUser = {
                id: 1,
                email: 'test@example.com',
                password: 'hashed-password',
                firstName: 'Іван',
                lastName: 'Петренко',
                createdAt: new Date()
            };
            mockUserRepository.create.mockResolvedValue(createdUser);

            // Act
            const result = await userService.createUser(userData);

            // Assert
            expect(mockUserRepository.findByEmail).toHaveBeenCalledWith('test@example.com');
            expect(bcrypt.hash).toHaveBeenCalledWith('Password123', 12);
            expect(mockUserRepository.create).toHaveBeenCalledWith({
                email: 'test@example.com',
                password: 'hashed-password',
                firstName: 'Іван',
                lastName: 'Петренко',
                createdAt: expect.any(Date)
            });
            expect(result).not.toHaveProperty('password');
            expect(result.email).toBe('test@example.com');
        });

        test('викидає помилку якщо користувач вже існує', async () => {
            // Arrange
            mockUserRepository.findByEmail.mockResolvedValue({ id: 1, email: 'test@example.com' });

            // Act & Assert
            await expect(userService.createUser(userData))
                .rejects.toThrow('Користувач з таким email вже існує');

            expect(bcrypt.hash).not.toHaveBeenCalled();
            expect(mockUserRepository.create).not.toHaveBeenCalled();
        });
    });

    describe('authenticateUser', () => {
        test('повертає користувача для валідних credentials', async () => {
            // Arrange
            const user = {
                id: 1,
                email: 'test@example.com',
                password: 'hashed-password',
                firstName: 'Іван'
            };
            mockUserRepository.findByEmail.mockResolvedValue(user);
            bcrypt.compare.mockResolvedValue(true);

            // Act
            const result = await userService.authenticateUser('test@example.com', 'Password123');

            // Assert
            expect(result).not.toHaveProperty('password');
            expect(result.email).toBe('test@example.com');
        });

        test('повертає null для невалідного email', async () => {
            // Arrange
            mockUserRepository.findByEmail.mockResolvedValue(null);

            // Act
            const result = await userService.authenticateUser('invalid@example.com', 'password');

            // Assert
            expect(result).toBeNull();
            expect(bcrypt.compare).not.toHaveBeenCalled();
        });

        test('повертає null для невалідного пароля', async () => {
            // Arrange
            const user = { id: 1, email: 'test@example.com', password: 'hashed-password' };
            mockUserRepository.findByEmail.mockResolvedValue(user);
            bcrypt.compare.mockResolvedValue(false);

            // Act
            const result = await userService.authenticateUser('test@example.com', 'wrong-password');

            // Assert
            expect(result).toBeNull();
        });
    });
});
```

### Моки та стаби в Jest

Мокування є фундаментальною концепцією unit тестування, яка дозволяє замінити реальні залежності контрольованими імітаціями. Jest надає потужну систему мокування, яка може імітувати модулі, функції та об'єкти.

Різниця між моками та стабами полягає в їх призначенні: моки перевіряють взаємодію (чи був викликаний метод, з якими параметрами), тоді як стаби просто повертають заздалегідь визначені значення. Jest поєднує обидві концепції в єдиному API.

Ось основні способи створення моків в Jest:

```javascript
// Автоматичне мокування модуля
jest.mock('nodemailer');

// Ручне мокування з реалізацією
jest.mock('../../src/utils/logger', () => ({
    info: jest.fn(),
    error: jest.fn(),
    warn: jest.fn()
}));

// Мокування конкретних методів
const fs = require('fs').promises;
jest.spyOn(fs, 'readFile').mockResolvedValue('file content');

// Використання mockImplementation для складної логіки
const mockCalculateDiscount = jest.fn().mockImplementation((price, percentage) => {
    return price * (percentage / 100);
});
```

## Integration тестування API

Integration тести перевіряють взаємодію між різними компонентами системи та є критично важливими для забезпечення правильної роботи API endpoints.

### Налаштування середовища для integration тестів

Integration тести вимагають більш складного налаштування, оскільки вони перевіряють взаємодію між реальними компонентами системи. На відміну від unit тестів, integration тести часто потребують доступу до бази даних, файлової системи або зовнішніх сервісів.

Ключовим принципом integration тестування є створення ізольованого тестового середовища, яке максимально наближене до production, але не впливає на реальні дані. Це включає використання окремої тестової бази даних, мокування зовнішніх API та забезпечення чистого стану перед кожним тестом.

Розглянемо як налаштувати надійне тестове середовище:

```javascript
// src/config/test.js
module.exports = {
    database: {
        host: process.env.TEST_DB_HOST || 'localhost',
        port: process.env.TEST_DB_PORT || 5432,
        username: process.env.TEST_DB_USERNAME || 'test',
        password: process.env.TEST_DB_PASSWORD || 'test',
        database: process.env.TEST_DB_NAME || 'testdb'
    },
    jwt: {
        secret: 'test-secret-key',
        expiresIn: '1h'
    },
    port: process.env.TEST_PORT || 3001
};
```

Налаштування тестової бази даних:

```javascript
// __tests__/setup/database.js
const { Pool } = require('pg');
const testConfig = require('../../src/config/test');

class TestDatabase {
    constructor() {
        this.pool = new Pool(testConfig.database);
    }

    async connect() {
        await this.pool.connect();
    }

    async cleanup() {
        // Очищення всіх таблиць перед тестами
        await this.pool.query('TRUNCATE TABLE users, posts, comments CASCADE');
    }

    async disconnect() {
        await this.pool.end();
    }

    async seedTestData() {
        // Додавання тестових даних
        const users = [
            { email: 'test1@example.com', firstName: 'Тест1', lastName: 'Користувач1' },
            { email: 'test2@example.com', firstName: 'Тест2', lastName: 'Користувач2' }
        ];

        for (const user of users) {
            await this.pool.query(
                'INSERT INTO users (email, first_name, last_name, password) VALUES ($1, $2, $3, $4)',
                [user.email, user.firstName, user.lastName, 'hashed-password']
            );
        }
    }
}

module.exports = TestDatabase;
```

### Тестування API endpoints з supertest

Supertest є бібліотекою, спеціально розробленою для тестування HTTP серверів. Вона дозволяє легко відправляти HTTP запити до вашого Express застосунку та перевіряти відповіді. Основна перевага supertest полягає в тому, що вона не потребує запуску сервера на реальному порту.

При тестуванні API endpoints важливо перевіряти не тільки статус коди відповідей, але й структуру та зміст повернутих даних, правильність обробки помилок та дотримання API контракту. Кожен endpoint має бути протестований з різними сценаріями: валідними даними, невалідними даними, відсутніми полями та граничними випадками.

Розглянемо комплексне тестування системи аутентифікації:

```javascript
// __tests__/integration/auth.test.js
const request = require('supertest');
const app = require('../../src/app');
const TestDatabase = require('../setup/database');

describe('Authentication API', () => {
    let testDb;

    beforeAll(async () => {
        testDb = new TestDatabase();
        await testDb.connect();
    });

    beforeEach(async () => {
        await testDb.cleanup();
        await testDb.seedTestData();
    });

    afterAll(async () => {
        await testDb.disconnect();
    });

    describe('POST /api/auth/register', () => {
        test('успішно реєструє нового користувача', async () => {
            const userData = {
                email: 'newuser@example.com',
                password: 'Password123',
                firstName: 'Новий',
                lastName: 'Користувач'
            };

            const response = await request(app)
                .post('/api/auth/register')
                .send(userData)
                .expect(201);

            expect(response.body).toHaveProperty('user');
            expect(response.body).toHaveProperty('token');
            expect(response.body.user.email).toBe(userData.email);
            expect(response.body.user).not.toHaveProperty('password');
        });

        test('повертає помилку для дублікату email', async () => {
            const userData = {
                email: 'test1@example.com', // Користувач вже існує
                password: 'Password123',
                firstName: 'Дублікат',
                lastName: 'Користувач'
            };

            const response = await request(app)
                .post('/api/auth/register')
                .send(userData)
                .expect(400);

            expect(response.body).toHaveProperty('error');
            expect(response.body.error).toContain('вже існує');
        });

        test('валідує обов\'язкові поля', async () => {
            const invalidData = {
                email: 'invalid-email',
                password: '123', // Занадто короткий
                firstName: '',
                lastName: ''
            };

            const response = await request(app)
                .post('/api/auth/register')
                .send(invalidData)
                .expect(400);

            expect(response.body).toHaveProperty('errors');
            expect(Array.isArray(response.body.errors)).toBe(true);
        });
    });

    describe('POST /api/auth/login', () => {
        test('успішно авторизує користувача', async () => {
            const loginData = {
                email: 'test1@example.com',
                password: 'correct-password'
            };

            const response = await request(app)
                .post('/api/auth/login')
                .send(loginData)
                .expect(200);

            expect(response.body).toHaveProperty('user');
            expect(response.body).toHaveProperty('token');
            expect(typeof response.body.token).toBe('string');
        });

        test('повертає помилку для невалідних credentials', async () => {
            const loginData = {
                email: 'test1@example.com',
                password: 'wrong-password'
            };

            await request(app)
                .post('/api/auth/login')
                .send(loginData)
                .expect(401);
        });
    });
});
```

### Тестування захищених endpoints

Тестування endpoints, які потребують аутентифікації, вимагає особливого підходу. Необхідно імітувати процес логіну користувача та отримання JWT токену, а потім використовувати цей токен для доступу до захищених ресурсів.

Ефективна стратегія включає створення helper функцій для генерації валідних токенів, налаштування тестових користувачів з різними ролями та систематичне тестування різних сценаріїв авторизації. Важливо перевіряти як успішні запити авторизованих користувачів, так і відхилення неавторизованих або недостатньо привілейованих запитів.

Приклад тестування користувацьких API з JWT аутентифікацією:

```javascript
// __tests__/integration/users.test.js
const request = require('supertest');
const jwt = require('jsonwebtoken');
const app = require('../../src/app');
const TestDatabase = require('../setup/database');

describe('Users API', () => {
    let testDb;
    let authToken;
    let testUserId;

    beforeAll(async () => {
        testDb = new TestDatabase();
        await testDb.connect();
    });

    beforeEach(async () => {
        await testDb.cleanup();
        await testDb.seedTestData();

        // Створення тестового токену
        testUserId = 1; // ID користувача з seedTestData
        authToken = jwt.sign(
            { userId: testUserId, email: 'test1@example.com' },
            process.env.JWT_SECRET || 'test-secret',
            { expiresIn: '1h' }
        );
    });

    afterAll(async () => {
        await testDb.disconnect();
    });

    describe('GET /api/users/profile', () => {
        test('повертає профіль авторизованого користувача', async () => {
            const response = await request(app)
                .get('/api/users/profile')
                .set('Authorization', `Bearer ${authToken}`)
                .expect(200);

            expect(response.body).toHaveProperty('id', testUserId);
            expect(response.body).toHaveProperty('email', 'test1@example.com');
            expect(response.body).not.toHaveProperty('password');
        });

        test('повертає 401 без токену', async () => {
            await request(app)
                .get('/api/users/profile')
                .expect(401);
        });

        test('повертає 401 для невалідного токену', async () => {
            await request(app)
                .get('/api/users/profile')
                .set('Authorization', 'Bearer invalid-token')
                .expect(401);
        });
    });

    describe('PUT /api/users/profile', () => {
        test('оновлює профіль користувача', async () => {
            const updateData = {
                firstName: 'Оновлене',
                lastName: 'Ім\'я'
            };

            const response = await request(app)
                .put('/api/users/profile')
                .set('Authorization', `Bearer ${authToken}`)
                .send(updateData)
                .expect(200);

            expect(response.body.firstName).toBe('Оновлене');
            expect(response.body.lastName).toBe('Ім\'я');
        });

        test('валідує дані оновлення', async () => {
            const invalidData = {
                firstName: '', // Пусте ім'я
                email: 'invalid-email'
            };

            await request(app)
                .put('/api/users/profile')
                .set('Authorization', `Bearer ${authToken}`)
                .send(invalidData)
                .expect(400);
        });
    });
});
```

### Тестування middleware та error handling

Middleware компоненти є критично важливими частинами Express застосунків, які обробляють запити до того, як вони досягнуть основних route handlers. Тестування middleware включає перевірку їх правильної роботи в ізоляції та в контексті повного request/response циклу.

Особливу увагу слід приділити тестуванню middleware для обробки помилок, rate limiting, CORS та валідації. Ці компоненти часто є першою лінією захисту застосунку, тому їх надійність критично важлива для безпеки та стабільності системи.

Розглянемо тестування ключових middleware компонентів:

```javascript
// __tests__/integration/middleware.test.js
const request = require('supertest');
const app = require('../../src/app');

describe('Middleware Integration', () => {
    describe('Rate Limiting', () => {
        test('блокує запити після перевищення ліміту', async () => {
            const endpoint = '/api/auth/login';
            const requestData = { email: 'test@example.com', password: 'wrong' };

            // Виконати максимальну кількість запитів
            for (let i = 0; i < 5; i++) {
                await request(app)
                    .post(endpoint)
                    .send(requestData);
            }

            // Наступний запит має бути заблокований
            const response = await request(app)
                .post(endpoint)
                .send(requestData)
                .expect(429);

            expect(response.body).toHaveProperty('error');
            expect(response.body.error).toContain('Too many requests');
        });
    });

    describe('CORS', () => {
        test('встановлює правильні CORS headers', async () => {
            const response = await request(app)
                .options('/api/users')
                .expect(200);

            expect(response.headers['access-control-allow-origin']).toBeDefined();
            expect(response.headers['access-control-allow-methods']).toBeDefined();
        });
    });

    describe('Error Handling', () => {
        test('повертає 404 для неіснуючих endpoints', async () => {
            const response = await request(app)
                .get('/api/nonexistent')
                .expect(404);

            expect(response.body).toHaveProperty('error');
        });

        test('обробляє внутрішні помилки сервера', async () => {
            // Цей тест вимагає спеціального endpoint для тестування
            const response = await request(app)
                .get('/api/test/error')
                .expect(500);

            expect(response.body).toHaveProperty('error');
            expect(response.body.error).toBe('Internal server error');
        });
    });
});
```

## Test coverage та CI/CD

Test coverage (покриття коду тестами) є метрикою, яка показує, яка частина коду виконується під час тестування. Вона допомагає виявити неперевірені частини коду та покращити якість тестування.

### Налаштування test coverage

Jest має вбудовану підтримку збору метрик покриття:

```json
{
  "jest": {
    "collectCoverage": true,
    "coverageDirectory": "coverage",
    "coverageReporters": ["text", "lcov", "html", "json"],
    "collectCoverageFrom": [
      "src/**/*.js",
      "!src/server.js",
      "!src/config/**",
      "!**/node_modules/**",
      "!**/vendor/**"
    ],
    "coverageThreshold": {
      "global": {
        "branches": 80,
        "functions": 80,
        "lines": 80,
        "statements": 80
      },
      "src/services/": {
        "branches": 90,
        "functions": 90,
        "lines": 90,
        "statements": 90
      }
    }
  }
}
```

### Інтерпретація метрик покриття

Jest збирає детальну статистику про те, які частини коду виконуються під час тестування. Розуміння цих метрик допомагає виявити неперевірені ділянки коду та покращити якість тестування.

Важливо розуміти, що високе покриття не гарантує якості тестів. Можна мати 100% покриття, але тести можуть перевіряти тільки "щасливі шляхи" і пропускати критичні помилкові сценарії. Навпаки, 80% покриття з якісними тестами може бути кращим за 100% з поверхневими тестами.

- **Statements**: відсоток виконаних інструкцій
- **Branches**: відсоток перевірених умовних гілок (if/else, switch)
- **Functions**: відсоток викликаних функцій
- **Lines**: відсоток виконаних рядків коду

Приклад звіту покриття:

```
----------------------|---------|----------|---------|---------|-------------------
File                  | % Stmts | % Branch | % Funcs | % Lines | Uncovered Line #s
----------------------|---------|----------|---------|---------|-------------------
All files            |   87.23 |    78.94 |   91.30 |   86.95 |
 src/controllers      |   95.12 |    89.47 |   96.77 |   94.87 |
  authController.js   |   97.56 |    92.31 |     100 |   97.37 | 45,67
  userController.js   |   92.68 |    86.67 |   93.33 |   92.31 | 123,145,167
 src/services         |   89.74 |    84.21 |   94.12 |   89.23 |
  userService.js      |   91.30 |    87.50 |   95.45 |   90.91 | 78,89,134
 src/utils            |   75.61 |    60.00 |   81.82 |   74.39 |
  validation.js       |   68.75 |    55.56 |   77.78 |   67.86 | 34,45,67,89,123
----------------------|---------|----------|---------|---------|-------------------
```

### Continuous Integration з GitHub Actions

GitHub Actions дозволяє створювати потужні CI/CD пайплайни, які автоматично виконуються при кожному commit або pull request. Правильно налаштований пайплайн включає не тільки запуск тестів, але й перевірку якості коду, безпекові скани та автоматичний деплой.

Ефективний CI пайплайн має бути швидким, надійним та інформативним. Він повинен надавати розробникам швидкий зворотний зв'язок про стан коду та автоматично запобігати злиттю коду, який не відповідає стандартам якості.

Розглянемо комплексну конфігурацію GitHub Actions для Node.js проєкту:

```yaml
# .github/workflows/ci.yml
name: CI Pipeline

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest

    services:
      postgres:
        image: postgres:14
        env:
          POSTGRES_USER: testuser
          POSTGRES_PASSWORD: testpass
          POSTGRES_DB: testdb
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
        ports:
          - 5432:5432

    strategy:
      matrix:
        node-version: [16.x, 18.x, 20.x]

    steps:
    - name: Checkout code
      uses: actions/checkout@v3

    - name: Setup Node.js ${{ matrix.node-version }}
      uses: actions/setup-node@v3
      with:
        node-version: ${{ matrix.node-version }}
        cache: 'npm'

    - name: Install dependencies
      run: npm ci

    - name: Run linting
      run: npm run lint

    - name: Run unit tests
      run: npm run test:unit
      env:
        NODE_ENV: test

    - name: Run integration tests
      run: npm run test:integration
      env:
        NODE_ENV: test
        TEST_DB_HOST: localhost
        TEST_DB_PORT: 5432
        TEST_DB_USERNAME: testuser
        TEST_DB_PASSWORD: testpass
        TEST_DB_NAME: testdb
        JWT_SECRET: test-jwt-secret

    - name: Generate test coverage
      run: npm run test:coverage

    - name: Upload coverage to Codecov
      uses: codecov/codecov-action@v3
      with:
        file: ./coverage/lcov.info
        flags: unittests
        name: codecov-umbrella

    - name: Check coverage threshold
      run: |
        npm run test:coverage -- --passWithNoTests
        if [ $? -ne 0 ]; then
          echo "Coverage threshold not met"
          exit 1
        fi

  security:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout code
      uses: actions/checkout@v3

    - name: Run security audit
      run: npm audit --audit-level high

    - name: Run Snyk security scan
      uses: snyk/actions/node@master
      env:
        SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}

  build:
    needs: [test, security]
    runs-on: ubuntu-latest

    steps:
    - name: Checkout code
      uses: actions/checkout@v3

    - name: Setup Node.js
      uses: actions/setup-node@v3
      with:
        node-version: '18.x'
        cache: 'npm'

    - name: Install dependencies
      run: npm ci

    - name: Build application
      run: npm run build

    - name: Upload build artifacts
      uses: actions/upload-artifact@v3
      with:
        name: build-files
        path: dist/
```

### Налаштування автоматичного деплою

Автоматичний деплой є логічним продовженням CI пайплайну і дозволяє безпечно доставляти код до production середовища. Ключові принципи включають поетапний деплой (staging -> production), автоматичні rollback механізми та post-deployment перевірки.

Важливо розділяти деплой на різні середовища: development гілка може автоматично деплоїтися на staging, тоді як production деплой може вимагати додаткового підтвердження або відбуватися тільки з main гілки після успішного проходження всіх тестів.

Приклад конфігурації автоматичного деплою:

```yaml
# .github/workflows/deploy.yml
name: Deploy to Production

on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    needs: [test, security, build]

    steps:
    - name: Checkout code
      uses: actions/checkout@v3

    - name: Deploy to Railway
      uses: bervProject/railway-deploy@v1.0.0
      with:
        railway_token: ${{ secrets.RAILWAY_TOKEN }}
        service: ${{ secrets.RAILWAY_SERVICE }}

    - name: Run smoke tests
      run: |
        sleep 30 # Дати час на запуск сервісу
        curl -f ${{ secrets.PRODUCTION_URL }}/health || exit 1

    - name: Notify deployment
      uses: 8398a7/action-slack@v3
      with:
        status: ${{ job.status }}
        channel: '#deployments'
        webhook_url: ${{ secrets.SLACK_WEBHOOK }}
```

## Environment конфігурації

Правильне управління конфігураціями різних середовищ є критично важливим для безпеки та гнучкості застосунку.

### Структура конфігураційних файлів

Правильна організація конфігурацій є основою для надійного деплою на різні середовища. Кожне середовище (development, staging, production) має свої специфічні налаштування, але спільна структура повинна забезпечувати консистентність та зручність управління.

Основний принцип полягає в тому, що конфігурації мають бути зовнішніми щодо коду застосунку. Це означає використання змінних середовища для всіх параметрів, які можуть відрізнятися між середовищами, таких як адреси баз даних, API ключі та налаштування безпеки.

Розглянемо структуру конфігураційної системи:

```javascript
// src/config/index.js
const development = require('./development');
const production = require('./production');
const test = require('./test');

const configs = {
    development,
    production,
    test
};

const env = process.env.NODE_ENV || 'development';

module.exports = configs[env];
```

```javascript
// src/config/development.js
module.exports = {
    app: {
        port: process.env.PORT || 3000,
        env: 'development'
    },
    database: {
        host: process.env.DB_HOST || 'localhost',
        port: process.env.DB_PORT || 5432,
        username: process.env.DB_USERNAME || 'devuser',
        password: process.env.DB_PASSWORD || 'devpass',
        database: process.env.DB_NAME || 'devdb',
        ssl: false,
        logging: true
    },
    jwt: {
        secret: process.env.JWT_SECRET || 'dev-secret-key',
        expiresIn: '24h'
    },
    cors: {
        origin: ['http://localhost:3000', 'http://localhost:3001'],
        credentials: true
    },
    rateLimit: {
        windowMs: 15 * 60 * 1000, // 15 хвилин
        max: 1000 // Ліберальний ліміт для розробки
    },
    logging: {
        level: 'debug',
        format: 'combined'
    }
};
```

```javascript
// src/config/production.js
module.exports = {
    app: {
        port: process.env.PORT || 8080,
        env: 'production'
    },
    database: {
        host: process.env.DB_HOST,
        port: process.env.DB_PORT || 5432,
        username: process.env.DB_USERNAME,
        password: process.env.DB_PASSWORD,
        database: process.env.DB_NAME,
        ssl: {
            require: true,
            rejectUnauthorized: false
        },
        logging: false,
        pool: {
            min: 2,
            max: 10,
            acquire: 30000,
            idle: 10000
        }
    },
    jwt: {
        secret: process.env.JWT_SECRET, // Обов'язково з environment
        expiresIn: '1h'
    },
    cors: {
        origin: process.env.ALLOWED_ORIGINS?.split(',') || [],
        credentials: true
    },
    rateLimit: {
        windowMs: 15 * 60 * 1000,
        max: 100 // Строгий ліміт для production
    },
    logging: {
        level: 'error',
        format: 'json'
    },
    monitoring: {
        enabled: true,
        service: process.env.MONITORING_SERVICE
    }
};
```

### Валідація конфігурацій

Валідація конфігурацій на етапі запуску застосунку допомагає виявити проблеми до того, як вони вплинуть на роботу системи. Це особливо важливо для production середовища, де неправильні налаштування можуть призвести до серйозних проблем.

Використання схем валідації забезпечує автоматичну перевірку типів даних, обов'язкових полів та допустимих значень. Це створює додатковий рівень безпеки та допомагає розробникам швидко виявляти помилки конфігурації.

Приклад валідації конфігурацій з використанням Joi:

```javascript
// src/config/validator.js
const Joi = require('joi');

const configSchema = Joi.object({
    app: Joi.object({
        port: Joi.number().port().required(),
        env: Joi.string().valid('development', 'production', 'test').required()
    }).required(),

    database: Joi.object({
        host: Joi.string().required(),
        port: Joi.number().port().required(),
        username: Joi.string().required(),
        password: Joi.string().required(),
        database: Joi.string().required(),
        ssl: Joi.alternatives().try(
            Joi.boolean(),
            Joi.object()
        ).required(),
        logging: Joi.boolean().required()
    }).required(),

    jwt: Joi.object({
        secret: Joi.string().min(32).required(),
        expiresIn: Joi.string().required()
    }).required()
});

function validateConfig(config) {
    const { error, value } = configSchema.validate(config, {
        allowUnknown: true,
        abortEarly: false
    });

    if (error) {
        const errorMessages = error.details.map(detail => detail.message);
        throw new Error(`Configuration validation failed: ${errorMessages.join(', ')}`);
    }

    return value;
}

module.exports = { validateConfig };
```

### Захист чутливих даних

Безпека конфігураційних даних є критично важливою, особливо для production середовищ. Чутливі дані як паролі, API ключі та секрети шифрування ніколи не повинні зберігатися в коді або звичайних файлах конфігурації.

Сучасні підходи включають використання керованих сервісів секретів (AWS Secrets Manager, Azure Key Vault), шифрування на рівні застосунку або спеціалізованих інструментів як HashiCorp Vault. Для простіших випадків можна використовувати базове шифрування з ключами, що зберігаються окремо.

Розглянемо простий приклад шифрування секретів на рівні застосунку:

```javascript
// src/utils/secrets.js
const crypto = require('crypto');

class SecretsManager {
    constructor() {
        this.algorithm = 'aes-256-gcm';
        this.keyLength = 32;
    }

    generateKey() {
        return crypto.randomBytes(this.keyLength);
    }

    encrypt(text, key) {
        const iv = crypto.randomBytes(16);
        const cipher = crypto.createCipher(this.algorithm, key, iv);

        let encrypted = cipher.update(text, 'utf8', 'hex');
        encrypted += cipher.final('hex');

        const authTag = cipher.getAuthTag();

        return {
            encrypted,
            iv: iv.toString('hex'),
            authTag: authTag.toString('hex')
        };
    }

    decrypt(encryptedData, key) {
        const decipher = crypto.createDecipher(
            this.algorithm,
            key,
            Buffer.from(encryptedData.iv, 'hex')
        );

        decipher.setAuthTag(Buffer.from(encryptedData.authTag, 'hex'));

        let decrypted = decipher.update(encryptedData.encrypted, 'hex', 'utf8');
        decrypted += decipher.final('utf8');

        return decrypted;
    }
}

module.exports = SecretsManager;
```

## Деплой на хмарні платформи

Розглянемо процес деплою на різні популярні хмарні платформи.

### Деплой на Railway

Railway є однією з найсучасніших платформ для деплою, яка автоматично виявляє тип проєкту та налаштовує відповідне середовище. Вона підтримує автоматичне масштабування, інтегрується з Git репозиторіями та надає зручний dashboard для моніторингу.

Основні переваги Railway включають нульову конфігурацію для стандартних Node.js проєктів, автоматичне встановлення SSL сертифікатів та простоту налаштування баз даних. Платформа також надає built-in метрики та логування.

Конфігурація для Railway може бути мінімальною або детальною:

```json
// railway.json
{
  "build": {
    "builder": "NIXPACKS",
    "buildCommand": "npm run build"
  },
  "deploy": {
    "startCommand": "npm start",
    "healthcheckPath": "/health",
    "restartPolicyType": "ON_FAILURE",
    "restartPolicyMaxRetries": 3
  }
}
```

```dockerfile
# Dockerfile для Railway (опціонально)
FROM node:18-alpine

WORKDIR /app

# Встановлення залежностей
COPY package*.json ./
RUN npm ci --only=production

# Копіювання коду
COPY . .

# Створення non-root користувача
RUN addgroup -g 1001 -S nodejs
RUN adduser -S nodejs -u 1001

USER nodejs

EXPOSE 3000

CMD ["npm", "start"]
```

#### Змінні середовища для Railway

```bash
# Railway environment variables
NODE_ENV=production
PORT=3000
DATABASE_URL=postgresql://username:password@host:port/database
JWT_SECRET=your-super-secret-jwt-key
CORS_ORIGIN=https://your-frontend-domain.com
RATE_LIMIT_MAX=100
LOG_LEVEL=info
```

### Деплой на Heroku

Heroku залишається однією з найпопулярніших платформ завдяки зрілості екосистеми та великій кількості додаткових сервісів (add-ons). Платформа використовує концепцію dyno (контейнерів) та підтримує горизонтальне масштабування через збільшення кількості dyno.

Heroku особливо зручний для команд, які потребують швидкого прототипування та MVP розробки. Платформа автоматично обробляє SSL, надає built-in моніторинг та підтримує одним кліком встановлення популярних add-ons як PostgreSQL, Redis, Elasticsearch.

Основні файли конфігурації для Heroku:

```json
// package.json - додати scripts для Heroku
{
  "scripts": {
    "start": "node src/server.js",
    "dev": "nodemon src/server.js",
    "build": "echo 'No build step required'",
    "heroku-postbuild": "npm run db:migrate"
  },
  "engines": {
    "node": ">=16.0.0",
    "npm": ">=8.0.0"
  }
}
```

```yaml
# Procfile
web: npm start
worker: node src/workers/backgroundJobs.js
```

### Налаштування бази даних для Heroku

Heroku автоматично надає змінну середовища DATABASE_URL при додаванні PostgreSQL add-on. Важливо правильно налаштувати connection pooling та SSL для production використання.

Heroku PostgreSQL використовує connection limits, тому важливо оптимізувати використання з'єднань через proper pooling. Також слід враховувати, що Heroku dyno можуть "засинати" при відсутності трафіку, що вимагає warm-up стратегій для production застосунків.

Приклад конфігурації бази даних:

```javascript
// src/config/database.js
const { Pool } = require('pg');

const pool = new Pool({
    connectionString: process.env.DATABASE_URL,
    ssl: process.env.NODE_ENV === 'production' ? {
        require: true,
        rejectUnauthorized: false
    } : false,
    max: 20,
    idleTimeoutMillis: 30000,
    connectionTimeoutMillis: 2000,
});

module.exports = pool;
```

### Деплой на Digital Ocean

Digital Ocean надає більший контроль над інфраструктурою через різні сервіси: App Platform (PaaS рішення), Droplets (віртуальні машини) та Kubernetes. Це дозволяє команді обирати рівень абстракції відповідно до потреб проєкту.

App Platform є найпростішим варіантом, схожим на Heroku, тоді як Droplets надають повний контроль над операційною системою та налаштуваннями сервера. Kubernetes підходить для великих застосунків з складними вимогами до оркестрації.

Розглянемо налаштування для різних варіантів деплою:

```yaml
# .do/app.yaml
name: my-web-app
services:
- name: api
  source_dir: /
  github:
    repo: your-username/your-repo
    branch: main
    deploy_on_push: true
  run_command: npm start
  environment_slug: node-js
  instance_count: 1
  instance_size_slug: basic-xxs
  envs:
  - key: NODE_ENV
    value: production
  - key: JWT_SECRET
    value: ${JWT_SECRET}
  - key: DATABASE_URL
    value: ${db.DATABASE_URL}
  health_check:
    http_path: /health
    initial_delay_seconds: 10
    period_seconds: 10
    timeout_seconds: 5
    success_threshold: 1
    failure_threshold: 3

databases:
- name: db
  engine: PG
  version: "14"
  size: basic-xs
```

#### Налаштування для Droplet з PM2

Використання Droplets надає повний контроль над сервером, але вимагає більше налаштувань. PM2 є популярним process manager для Node.js застосунків, який забезпечує автоматичний restart, clustering та моніторинг.

PM2 дозволяє запускати застосунок в cluster mode, використовуючи всі доступні CPU cores, та автоматично перезапускати процеси у випадку збоїв. Також PM2 надає built-in логування та можливості для zero-downtime deployments.

Конфігурація PM2 та скрипт деплою:

```javascript
// ecosystem.config.js - конфігурація PM2
module.exports = {
  apps: [{
    name: 'web-app-api',
    script: './src/server.js',
    instances: 'max',
    exec_mode: 'cluster',
    env: {
      NODE_ENV: 'development',
      PORT: 3000
    },
    env_production: {
      NODE_ENV: 'production',
      PORT: 8080
    },
    error_file: './logs/err.log',
    out_file: './logs/out.log',
    log_file: './logs/combined.log',
    time: true,
    max_memory_restart: '1G',
    node_args: '--max_old_space_size=1024'
  }]
};
```

```bash
# deploy.sh - скрипт деплою
#!/bin/bash

echo "Starting deployment..."

# Оновлення коду
git pull origin main

# Встановлення залежностей
npm ci --production

# Запуск міграцій
npm run db:migrate

# Перезапуск PM2
pm2 reload ecosystem.config.js --env production

echo "Deployment completed!"
```

## Моніторинг та логування

Ефективний моніторинг та логування є критично важливими для підтримки стабільності production застосунків.

### Структуроване логування з Winston

Winston є найпопулярнішою бібліотекою для логування в Node.js екосистемі. Він підтримує різні рівні логування, множинні transports (destinations) та гнучке форматування повідомлень.

Структуроване логування означає збереження інформації в структурованому форматі (зазвичай JSON), що полегшує пошук, фільтрацію та аналіз логів у production середовищі. Це особливо важливо при використанні систем як ELK Stack (Elasticsearch, Logstash, Kibana) або подібних рішень.

Основні принципи ефективного логування включають використання відповідних рівнів (error, warn, info, debug), додавання контекстної інформації та уникнення логування чутливих даних.

Приклад налаштування Winston:

```javascript
// src/utils/logger.js
const winston = require('winston');
const config = require('../config');

const logFormat = winston.format.combine(
    winston.format.timestamp(),
    winston.format.errors({ stack: true }),
    winston.format.json()
);

const logger = winston.createLogger({
    level: config.logging.level,
    format: logFormat,
    defaultMeta: {
        service: 'web-api',
        version: process.env.npm_package_version
    },
    transports: [
        new winston.transports.File({
            filename: 'logs/error.log',
            level: 'error'
        }),
        new winston.transports.File({
            filename: 'logs/combined.log'
        })
    ]
});

if (process.env.NODE_ENV !== 'production') {
    logger.add(new winston.transports.Console({
        format: winston.format.combine(
            winston.format.colorize(),
            winston.format.simple()
        )
    }));
}

// Middleware для логування HTTP запитів
const httpLogger = winston.createLogger({
    format: winston.format.combine(
        winston.format.timestamp(),
        winston.format.json()
    ),
    transports: [
        new winston.transports.File({ filename: 'logs/access.log' })
    ]
});

module.exports = { logger, httpLogger };
```

### Middleware для логування запитів

HTTP request логування є критично важливим для debugging, моніторингу продуктивності та безпекового аудиту. Middleware для логування має збирати ключову інформацію про кожен запит: метод, URL, час відповіді, статус код та користувача (якщо доступно).

Важливо балансувати між детальністю логування та продуктивністю. Надмірне логування може створити величезні файли логів та вплинути на продуктивність, тоді як недостатнє логування ускладнює діагностику проблем.

Ефективний request logger також має підтримувати correlation IDs для трекінгу запитів через мікросервісну архітектуру та фільтрацію чутливої інформації.

Приклад middleware для логування:

```javascript
// src/middleware/requestLogger.js
const { httpLogger } = require('../utils/logger');

function requestLogger(req, res, next) {
    const start = Date.now();

    // Логування початку запиту
    httpLogger.info('Request started', {
        method: req.method,
        url: req.url,
        userAgent: req.get('user-agent'),
        ip: req.ip,
        userId: req.user?.id
    });

    // Перехоплення завершення відповіді
    res.on('finish', () => {
        const duration = Date.now() - start;

        httpLogger.info('Request completed', {
            method: req.method,
            url: req.url,
            statusCode: res.statusCode,
            duration,
            ip: req.ip,
            userId: req.user?.id
        });
    });

    next();
}

module.exports = requestLogger;
```

### Моніторинг здоров'я системи

Health checks є фундаментальним компонентом production застосунків, який дозволяє автоматизованим системам (load balancers, orchestrators) визначати стан сервісу. Ефективний health check перевіряє не тільки доступність сервісу, але й стан критичних залежностей.

Типовий health check має включати перевірку з'єднання з базою даних, доступності зовнішніх API та стану файлової системи. Також корисно додавати інформацію про версію застосунку, час роботи та використання ресурсів.

Розрізняють різні типи health checks: liveness (чи працює сервіс), readiness (чи готовий приймати запити) та startup (чи завершився процес ініціалізації).

Приклад comprehensive health check endpoint:

```javascript
// src/routes/health.js
const express = require('express');
const { Pool } = require('pg');
const { logger } = require('../utils/logger');
const config = require('../config');

const router = express.Router();
const pool = new Pool(config.database);

router.get('/health', async (req, res) => {
    const health = {
        status: 'ok',
        timestamp: new Date().toISOString(),
        uptime: process.uptime(),
        version: process.env.npm_package_version,
        environment: config.app.env,
        memory: process.memoryUsage(),
        checks: {}
    };

    try {
        // Перевірка бази даних
        const dbStart = Date.now();
        await pool.query('SELECT 1');
        health.checks.database = {
            status: 'ok',
            responseTime: Date.now() - dbStart
        };
    } catch (error) {
        health.status = 'error';
        health.checks.database = {
            status: 'error',
            error: error.message
        };
    }

    try {
        // Перевірка зовнішніх сервісів
        const externalStart = Date.now();
        // await fetch('https://external-api.com/health');
        health.checks.externalServices = {
            status: 'ok',
            responseTime: Date.now() - externalStart
        };
    } catch (error) {
        health.checks.externalServices = {
            status: 'error',
            error: error.message
        };
    }

    const statusCode = health.status === 'ok' ? 200 : 503;

    logger.info('Health check performed', health);
    res.status(statusCode).json(health);
});

router.get('/metrics', (req, res) => {
    const metrics = {
        timestamp: new Date().toISOString(),
        process: {
            pid: process.pid,
            uptime: process.uptime(),
            memory: process.memoryUsage(),
            cpu: process.cpuUsage()
        },
        system: {
            loadavg: require('os').loadavg(),
            freemem: require('os').freemem(),
            totalmem: require('os').totalmem()
        }
    };

    res.json(metrics);
});

module.exports = router;
```

### Інтеграція з моніторинговими сервісами

Сучасні застосунки потребують comprehensive моніторингу, який включає збір метрик, трекінг помилок та аналіз продуктивності. Інтеграція з спеціалізованими сервісами як Sentry, DataDog або New Relic забезпечує професійний рівень observability.

Ефективний моніторинг включає automatic error detection, performance tracking, custom business metrics та alerting при критичних подіях. Важливо налаштувати правильні пороги для alerts, щоб уникнути як false positives, так і missed incidents.

Custom metrics можуть включати business-specific показники як кількість реєстрацій, conversion rates або API usage patterns. Ці дані допомагають не тільки в технічному моніторингу, але й в business intelligence.

Приклад сервісу для моніторингу:

```javascript
// src/utils/monitoring.js
const config = require('../config');
const { logger } = require('./logger');

class MonitoringService {
    constructor() {
        this.isEnabled = config.monitoring?.enabled || false;
        this.service = config.monitoring?.service;
    }

    trackError(error, context = {}) {
        logger.error('Application error', {
            error: error.message,
            stack: error.stack,
            context
        });

        if (this.isEnabled && this.service === 'sentry') {
            // Інтеграція з Sentry
            require('@sentry/node').captureException(error, {
                tags: context,
                extra: {
                    timestamp: new Date().toISOString()
                }
            });
        }
    }

    trackPerformance(operation, duration, metadata = {}) {
        logger.info('Performance metric', {
            operation,
            duration,
            metadata
        });

        if (this.isEnabled) {
            // Відправка метрик до системи моніторингу
            this.sendMetric('performance', {
                operation,
                duration,
                timestamp: Date.now(),
                ...metadata
            });
        }
    }

    trackCustomEvent(event, data = {}) {
        logger.info('Custom event', {
            event,
            data
        });

        if (this.isEnabled) {
            this.sendMetric('custom_event', {
                event,
                data,
                timestamp: Date.now()
            });
        }
    }

    sendMetric(type, data) {
        // Реалізація відправки метрик
        // Може використовувати StatsD, InfluxDB, або інші системи
        if (process.env.STATSD_HOST) {
            // Приклад відправки через StatsD
            const StatsD = require('node-statsd');
            const client = new StatsD({
                host: process.env.STATSD_HOST,
                port: process.env.STATSD_PORT || 8125
            });

            client.increment(`app.${type}`, 1, data);
        }
    }
}

module.exports = new MonitoringService();
```

### Error tracking та alerting

Централізована обробка помилок є критично важливою для підтримки стабільності production застосунків. Effective error handling включає не тільки логування помилок, але й збір контекстної інформації, categorization за типами та automatic alerting для критичних issues.

Error tracking система має розрізняти різні типи помилок: validation errors (400), authorization failures (401/403), not found errors (404) та internal server errors (500). Кожен тип вимагає різного рівня уваги та різних стратегій реагування.

Важливо також налаштувати graceful degradation для non-critical errors та circuit breaker patterns для захисту від cascading failures. Proper error handling може перетворити потенційний system outage на minor user experience issue.

Приклад comprehensive error handling middleware:

```javascript
// src/middleware/errorHandler.js
const { logger } = require('../utils/logger');
const monitoring = require('../utils/monitoring');

function errorHandler(error, req, res, next) {
    // Логування помилки
    const errorContext = {
        url: req.url,
        method: req.method,
        userAgent: req.get('user-agent'),
        ip: req.ip,
        userId: req.user?.id,
        body: req.body,
        params: req.params,
        query: req.query
    };

    logger.error('Unhandled error', {
        error: error.message,
        stack: error.stack,
        context: errorContext
    });

    // Відправка до системи моніторингу
    monitoring.trackError(error, errorContext);

    // Визначення коду статусу
    let statusCode = 500;
    let message = 'Internal server error';

    if (error.name === 'ValidationError') {
        statusCode = 400;
        message = error.message;
    } else if (error.name === 'UnauthorizedError') {
        statusCode = 401;
        message = 'Unauthorized';
    } else if (error.name === 'ForbiddenError') {
        statusCode = 403;
        message = 'Forbidden';
    } else if (error.name === 'NotFoundError') {
        statusCode = 404;
        message = 'Not found';
    }

    res.status(statusCode).json({
        error: message,
        ...(process.env.NODE_ENV === 'development' && {
            stack: error.stack,
            details: error.details
        })
    });
}

// Обробка unhandled rejections
process.on('unhandledRejection', (reason, promise) => {
    logger.error('Unhandled Rejection', {
        reason: reason.toString(),
        stack: reason.stack,
        promise
    });

    monitoring.trackError(new Error(`Unhandled Rejection: ${reason}`), {
        type: 'unhandledRejection'
    });
});

// Обробка uncaught exceptions
process.on('uncaughtException', (error) => {
    logger.error('Uncaught Exception', {
        error: error.message,
        stack: error.stack
    });

    monitoring.trackError(error, {
        type: 'uncaughtException'
    });

    // Graceful shutdown
    process.exit(1);
});

module.exports = errorHandler;
```

## Висновки та найкращі практики

Тестування та деплой є невід'ємними частинами сучасного циклу розробки backend застосунків. Ефективна стратегія тестування має включати всі рівні піраміди тестування з особливою увагою до unit тестів як основи стабільності коду.

**Jest** надає потужний та гнучкий інструментарій для створення якісних тестів, а **supertest** дозволяє ефективно тестувати API endpoints в інтеграційних тестах. Важливо пам'ятати, що тести мають бути швидкими, надійними та незалежними один від одного.

**Test coverage** є корисною метрикою, але не слід сліпо прагнути 100% покриття. Важливіше забезпечити покриття критичних шляхів коду та бізнес-логіки. Налаштування порогів покриття допомагає підтримувати якість коду на належному рівні.

**CI/CD пайплайни** автоматизують процес тестування та деплою, знижуючи ризик помилок та прискорюючи доставку нових функцій. GitHub Actions, Jenkins або інші системи CI/CD дозволяють створити надійний автоматизований процес.

**Environment конфігурації** мають бути чітко розділені між різними середовищами з особливою увагою до безпеки production конфігурацій. Використання валідації конфігурацій допомагає виявити проблеми на ранній стадії.

**Хмарні платформи** як Railway, Heroku та Digital Ocean надають різні рівні абстракції та контролю. Вибір платформи залежить від специфічних потреб проєкту, бюджету та рівня контролю, який потрібен команді.

**Моніторинг та логування** є критично важливими для підтримки production застосунків. Структуроване логування з Winston, health checks та інтеграція з системами моніторингу забезпечують visibility у роботу застосунку та швидке виявлення проблем.

Найкращі практики включають:

- Написання тестів як частини процесу розробки, а не як окремого етапу;
- Використання моків для ізоляції unit тестів від зовнішніх залежностей;
- Створення окремих тестових баз даних для integration тестів;
- Автоматизацію всіх етапів від тестування до деплою;
- Регулярний моніторинг метрик покриття та якості коду;
- Правильне управління секретами та конфігураціями;
- Впровадження comprehensive логування та моніторингу з першого дня.

Ці практики забезпечують створення надійних, масштабованих та підтримуваних backend застосунків, готових до production використання.
