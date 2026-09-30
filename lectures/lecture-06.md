# Лекція 6. Тестування та розгортання серверної частини

У попередніх лекціях ми створили серверний застосунок на Node.js та Express.js (лекція 2), спроєктували REST API (лекція 3), підключили базу даних (лекція 4) і захистили маршрути автентифікацією (лекція 5). Застосунок працює на комп'ютері розробника — але це лише половина шляху. Щоб він став продуктом, треба довести, що він працює правильно й продовжуватиме працювати після кожної зміни коду (тестування), а потім доставити його на сервер, де до нього матимуть доступ користувачі, і стежити за його станом (розгортання та експлуатація).

Ця лекція об'єднує обидві половини в один ланцюжок: **написали код → перевірили автоматично → зібрали й розгорнули → спостерігаємо за роботою**. Наступна лекція переносить ту саму логіку на клієнтську частину — вивчення React.

## Вступ до тестування серверних застосунків

Тестування серверних застосунків є критично важливим етапом розробки, який забезпечує стабільність, надійність та якість програмного продукту. У сучасній розробці тестування — не опційний етап «наприкінці проєкту», а постійна частина процесу: тести пишуться разом із кодом і запускаються автоматично при кожній зміні.

Тестування серверної частини має свої особливості. Серверна логіка майже завжди взаємодіє з базами даних, зовнішніми API, файловою системою, чергами повідомлень, тобто з ресурсами, які повільні, ненадійні або мають побічні ефекти (наприклад, надсилають листи чи списують гроші). Тому значна частина мистецтва тестування полягає в тому, щоб вирішити, які з цих залежностей замінити імітаціями, а які використати справжніми.

### Навіщо тестувати: економіка помилок

Головний аргумент на користь тестів економічний. Що пізніше виявлено помилку, то дорожче її виправити: помилку, знайдену під час написання функції, виправляють за хвилини; помилку, що дійшла до користувачів, — за години чи дні, з відкатом релізу, аналізом журналів і, можливо, втратою даних. Відомі оцінки на кшталт «у 100 разів дорожче» є лише орієнтиром (вони ґрунтуються на старих дослідженнях і в конкретних командах відрізняються), але напрям залежності підтверджується практикою.

Тести дають ще три речі, які важко отримати іншим способом:

- **впевненість у змінах** — можна рефакторити код, знаючи, що сигналізація спрацює при порушенні поведінки;
- **виконувану документацію** — тест показує, як саме має поводитися функція чи маршрут, і ця документація не застаріває, доки тест проходить;
- **швидкий зворотний зв'язок** — автоматичний набір тестів за секунди перевіряє те, що вручну довелося б перевіряти хвилинами.

### Типи тестування в розробці серверної частини

Тести традиційно поділяють на кілька рівнів, які утворюють «піраміду тестування». Що вищий рівень, то ближче тест до реального використання, але то повільніший, дорожчий і крихкіший він є:

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

**Модульні тести (unit tests)** перевіряють окремі функції або класи в ізоляції від решти системи. Вони найшвидші й найдешевші, тому їх має бути найбільше.

**Інтеграційні тести (integration tests)** перевіряють взаємодію кількох модулів: маршрут Express разом із проміжними обробниками, сервісом і справжньою базою даних.

**Наскрізні тести (end-to-end, E2E)** перевіряють увесь робочий процес через зовнішній інтерфейс: наприклад, браузер відкриває сторінку, користувач реєструється, і система видає результат. Їх пишуть найменше й лише для найважливіших сценаріїв.

Класична піраміда — не догма. Для застосунків, де вся цінність міститься у взаємодії з базою даних і API (а це типова серверна частина), часто радять «трофей тестування»: небагато модульних тестів для чистої логіки, **основна маса інтеграційних тестів** і кілька наскрізних. Ця лекція йде саме цим шляхом: модульними тестами перевіряємо чисту логіку, а маршрути й роботу з базою — інтеграційними, причому з реальним PostgreSQL.

### Філософія Test-Driven Development (TDD)

Test-Driven Development (розробка через тестування) — методологія, за якої тест пишеться *до* коду, який він перевіряє. Цикл складається з трьох кроків, які повторюються дрібними порціями:

```mermaid
flowchart LR
    R["Red: написати тест, який не проходить"] --> G["Green: мінімальний код, щоб тест пройшов"]
    G --> F["Refactor: покращити код, зберігши поведінку"]
    F --> R
```

1. **Red** — пишемо тест для функції, якої ще немає, і переконуємося, що він «червоний». Так ми перевіряємо і сам тест: тест, який не може впасти, нічого не варт.
2. **Green** — пишемо найпростіший код, що змушує тест пройти. Не «гарний» і не «повний» — лише достатній.
3. **Refactor** — покращуємо структуру, назви, усуваємо дублювання. Тести гарантують, що поведінка не змінилася.

Переваги TDD — краща архітектура (код, який складно тестувати, зазвичай погано спроєктований), вища впевненість у змінах і документація поведінки у вигляді тестів. Обмеження — TDD вимагає дисципліни і найкраще працює для логіки з чітко відомими вхідними та вихідними даними; для дослідницького програмування чи інтерфейсів його застосовують вибірково.

Поруч із TDD часто згадують **BDD (Behavior-Driven Development)** — розробку, керовану поведінкою. Якщо TDD має технічний фокус («функція повертає true для коректної адреси»), то BDD описує поведінку мовою предметної області («користувач із неправильною поштою бачить повідомлення про помилку»), зрозумілою й нетехнічним учасникам команди.

Ще три принципи, які добре узгоджуються з TDD: **YAGNI** (You Aren't Gonna Need It — не пишіть того, що зараз не потрібно), **KISS** (Keep It Simple — робіть просто) та **DRY** (Don't Repeat Yourself — не дублюйте знання).

## Модульне тестування з Vitest

### Вибір інструмента: Vitest, Jest чи node:test

Протягом багатьох років стандартом тестування JavaScript був Jest, створений у Facebook (Meta). Він і досі широко використовується, підтримується (Jest 30 вийшов у червні 2025 року) і зустрічається в більшості наявних проєктів. Проте його архітектура орієнтована на CommonJS, а підтримка ES Modules потребує додаткових налаштувань — тоді як наш курс, починаючи з лекції 2, використовує ES Modules як основний формат. Тому основним інструментом у цій лекції є **Vitest** — засіб запуску тестів, створений командою Vite. Він працює з ES Modules і TypeScript «з коробки», запускається помітно швидше за Jest, а його API майже збігається з Jest: `describe`, `test`, `expect` працюють однаково, а `jest.fn()`/`jest.mock()` мають прямі відповідники `vi.fn()`/`vi.mock()`. Тому знання, отримані тут, легко переносяться на наявні проєкти з Jest.

Порівняємо три доступні варіанти:

| Критерій | Vitest | Jest | `node:test` (вбудований) |
| --- | --- | --- | --- |
| Установлення | `npm i -D vitest` | `npm i -D jest` | не потрібне, входить до Node.js |
| ES Modules | нативно | потребує додаткового налаштування | нативно |
| TypeScript | без налаштувань | через Babel/SWC/ts-jest | Node.js 24 запускає `.ts` вилученням типів |
| Моки | `vi.fn`, `vi.mock`, `vi.spyOn` | `jest.fn`, `jest.mock`, `jest.spyOn` | `mock.fn`, `mock.method` (простіші) |
| Покриття коду | `@vitest/coverage-v8` | вбудоване | `--experimental-test-coverage` |
| Спостереження за змінами | миттєве, лише змінені файли | так, повільніше | `--watch` |
| Екосистема | швидко зростає | найбільша, зрілі плагіни | мінімальна |
| Коли обирати | нові проєкти, ES Modules, Vite | наявні кодові бази, React Native | невеликі проєкти без залежностей |

Вбудований `node:test` (згаданий у лекції 2) — чудовий вибір для бібліотек і невеликих сервісів, де не хочеться додавати залежності. Для навчального проєкту із серверною й клієнтською частинами Vitest зручніший ще й тим, що той самий інструмент застосовуватиметься для тестів React-компонентів у наступній лекції.

Що входить до Vitest (і до будь-якого сучасного засобу тестування):

- **Засіб запуску тестів (test runner)** — знаходить файли тестів, виконує їх паралельно, збирає результати;
- **Бібліотека тверджень (assertion library)** — методи `expect(...)` для перевірки очікувань;
- **Система моків** — імітація функцій і модулів;
- **Покриття коду (code coverage)** — аналіз, які рядки виконано під час тестів;
- **Знімки (snapshot testing)** — перевірка, що результат не змінився порівняно зі збереженим зразком.

### Налаштування Vitest для проєкту на Node.js

Встановимо Vitest, бібліотеку `supertest` для тестування HTTP та плагін покриття:

```bash
npm install --save-dev vitest @vitest/coverage-v8 supertest
```

Переконайтеся, що в `package.json` вказано `"type": "module"` (ES Modules), і додайте скрипти:

```json
{
  "type": "module",
  "scripts": {
    "test": "vitest run",
    "test:watch": "vitest",
    "test:unit": "vitest run --project unit",
    "test:integration": "vitest run --project integration",
    "test:coverage": "vitest run --coverage"
  }
}
```

Команда `vitest` без `run` запускається в режимі спостереження й перезапускає лише ті тести, яких стосуються змінені файли, а `vitest run` виконує тести один раз — саме так їх слід запускати в CI.

Конфігурація зберігається у файлі `vitest.config.js`. Розділимо тести на два «проєкти» — модульні й інтеграційні, бо їм потрібні різні умови (інтеграційним потрібна база даних):

```javascript
// vitest.config.js
import { defineConfig } from 'vitest/config';

export default defineConfig({
    test: {
        environment: 'node',
        projects: [
            {
                test: {
                    name: 'unit',
                    include: ['tests/unit/**/*.test.js']
                }
            },
            {
                test: {
                    name: 'integration',
                    include: ['tests/integration/**/*.test.js'],
                    globalSetup: ['tests/setup/global-setup.js'],
                    setupFiles: ['tests/setup/env.js'],
                    fileParallelism: false // тести ділять одну БД
                }
            }
        ],
        coverage: {
            provider: 'v8',
            include: ['src/**/*.js'],
            exclude: ['src/server.js'],
            reporter: ['text', 'lcov', 'html'],
            thresholds: {
                lines: 80,
                functions: 80,
                branches: 80,
                statements: 80
            }
        }
    }
});
```

Зверніть увагу: у Vitest потрібно явно вказувати `coverage.include` — інакше у звіт потраплять лише файли, які тести справді імпортували, і покриття виглядатиме кращим, ніж є насправді.

### Структура тесту: патерн AAA

Кожен добрий тест має однакову структуру — **Arrange, Act, Assert**:

- **Arrange** (підготувати) — створити вхідні дані й умови;
- **Act** (діяти) — викликати тестований код, причому одну дію;
- **Assert** (перевірити) — порівняти результат із очікуванням.

Терміни, які будуть траплятися далі: **тестовий випадок (test case)** — один сценарій, `test(...)`; **набір тестів (test suite)** — група пов'язаних випадків, блок `describe(...)`; **твердження (assertion)** — перевірка очікуваного результату, `expect(...)`.

Також слід тестувати не лише «щасливий шлях» (коли все працює правильно), а й негативні сценарії та граничні значення: порожні рядки, `null`, надто довгі дані, від'ємні числа.

### Написання модульних тестів для функцій

Модульні тести перевіряють функції в повній ізоляції. Вони мають бути швидкими, детермінованими (однаковий результат при кожному запуску) та незалежними одне від одного. Кожен тест зосереджується на одному аспекті поведінки.

Ідеальні кандидати — утилітарні функції валідації: чіткі вхідні параметри й передбачувані результати.

```javascript
// src/utils/validation.js
export function validateEmail(email) {
    const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    return emailRegex.test(email) && !email.includes('..');
}

export function validatePassword(password) {
    if (!password || password.length < 8) {
        return { isValid: false, message: 'Пароль повинен містити принаймні 8 символів' };
    }

    if (!/[A-Z]/.test(password)) {
        return { isValid: false, message: 'Пароль повинен містити принаймні одну велику літеру' };
    }

    if (!/[0-9]/.test(password)) {
        return { isValid: false, message: 'Пароль повинен містити принаймні одну цифру' };
    }

    return { isValid: true, message: 'Пароль валідний' };
}

export function formatUserName(firstName, lastName) {
    if (!firstName || !lastName) {
        throw new Error("Ім'я та прізвище є обов'язковими");
    }

    return `${firstName.trim()} ${lastName.trim()}`;
}
```

Зверніть увагу на перевірку `!email.includes('..')`: у попередній версії цієї лекції тест очікував, що `test..test@example.com` буде відхилено, хоча регулярний вираз його пропускав. Це типовий приклад того, як тест виявляє помилку в коді — і як важливо запускати тести, а не лише писати їх.

Тепер тести. Для перевірки багатьох значень однією логікою у Vitest є `test.each`: кожне значення стає окремим тестом зі своєю назвою в звіті, і при падінні одразу видно, яке саме значення не пройшло (у циклі `forEach` було б лише «один тест упав»).

```javascript
// tests/unit/validation.test.js
import { describe, test, expect } from 'vitest';
import { validateEmail, validatePassword, formatUserName } from '../../src/utils/validation.js';

describe('validateEmail', () => {
    test.each([
        'test@example.com',
        'user.name@domain.co.uk',
        'test+tag@example.org'
    ])('приймає коректну адресу %s', (email) => {
        expect(validateEmail(email)).toBe(true);
    });

    test.each([
        'invalid-email',
        '@example.com',
        'test@',
        'test..test@example.com'
    ])('відхиляє некоректну адресу %s', (email) => {
        expect(validateEmail(email)).toBe(false);
    });
});

describe('validatePassword', () => {
    test.each(['Password123', 'MySecure1', 'Test123456'])(
        'визнає надійним пароль %s',
        (password) => {
            expect(validatePassword(password)).toEqual({
                isValid: true,
                message: 'Пароль валідний'
            });
        }
    );

    test('повідомляє про надто короткий пароль', () => {
        expect(validatePassword('short')).toEqual({
            isValid: false,
            message: 'Пароль повинен містити принаймні 8 символів'
        });
    });

    test('вимагає велику літеру', () => {
        expect(validatePassword('lowercase123')).toEqual({
            isValid: false,
            message: 'Пароль повинен містити принаймні одну велику літеру'
        });
    });

    test('вимагає цифру', () => {
        expect(validatePassword('NoNumbers')).toEqual({
            isValid: false,
            message: 'Пароль повинен містити принаймні одну цифру'
        });
    });
});

describe('formatUserName', () => {
    test("форматує ім'я та прізвище, прибираючи зайві пробіли", () => {
        expect(formatUserName('Іван', 'Петренко')).toBe('Іван Петренко');
        expect(formatUserName(' Марія ', ' Коваленко ')).toBe('Марія Коваленко');
    });

    test('викидає помилку, якщо дані відсутні', () => {
        const message = "Ім'я та прізвище є обов'язковими";

        expect(() => formatUserName('', 'Петренко')).toThrow(message);
        expect(() => formatUserName('Іван', '')).toThrow(message);
        expect(() => formatUserName()).toThrow(message);
    });
});
```

Під час пошуку помилок корисний режим спостереження (`npm run test:watch`): він показує результат за частки секунди після збереження файлу.

### Тестування асинхронного коду

Асинхронний код у Node.js повсюдний, тому засіб тестування має вміти чекати на завершення промісів. Найпростіший спосіб — оголосити тест як `async` і використовувати `await`; для очікуваної помилки застосовують `await expect(promise).rejects.toThrow(...)`. Старіші підходи (повернення проміса чи колбеки) працюють, але читаються гірше.

Критично важливо ізолювати асинхронний код від зовнішніх залежностей — бази даних, HTTP-запитів, файлової системи. Інакше тест стає повільним, залежить від мережі й може випадково падати. Для цього використовують **моки** (імітації залежностей).

Розглянемо сервіс користувачів. Він приймає репозиторій (об'єкт для роботи з базою) через конструктор — це простий прийом **впровадження залежностей (dependency injection)**, який робить сервіс легко тестованим: у тесті замість справжнього репозиторію передаємо імітацію. Для хешування паролів використовуємо Argon2id (див. лекцію 5) — його підтримує пакет `argon2`, який за замовчуванням застосовує саме цей алгоритм.

```javascript
// src/services/userService.js
import * as argon2 from 'argon2';

export class UserService {
    constructor(userRepository) {
        this.userRepository = userRepository;
    }

    async createUser({ email, password, firstName, lastName }) {
        const existingUser = await this.userRepository.findByEmail(email);
        if (existingUser) {
            throw new Error('Користувач з таким email вже існує');
        }

        const passwordHash = await argon2.hash(password);

        const user = await this.userRepository.create({
            email,
            passwordHash,
            firstName,
            lastName,
            createdAt: new Date()
        });

        return withoutPassword(user);
    }

    async authenticateUser(email, password) {
        const user = await this.userRepository.findByEmail(email);
        if (!user) {
            return null;
        }

        const isValidPassword = await argon2.verify(user.passwordHash, password);
        if (!isValidPassword) {
            return null;
        }

        return withoutPassword(user);
    }
}

function withoutPassword({ passwordHash: _passwordHash, ...safeUser }) {
    return safeUser;
}
```

Тести для цього сервісу. Пакет `argon2` ми замінюємо імітацією: справжнє хешування навмисно повільне (це його призначення), а нам у модульному тесті потрібно перевірити лише, що сервіс викликає його правильно.

```javascript
// tests/unit/userService.test.js
import { describe, test, expect, vi, beforeEach } from 'vitest';
import * as argon2 from 'argon2';
import { UserService } from '../../src/services/userService.js';

// vi.mock піднімається нагору файла й замінює модуль до імпортів
vi.mock('argon2', () => ({
    hash: vi.fn(),
    verify: vi.fn()
}));

describe('UserService', () => {
    let userService;
    let userRepository;

    beforeEach(() => {
        vi.clearAllMocks();

        userRepository = {
            findByEmail: vi.fn(),
            create: vi.fn()
        };
        userService = new UserService(userRepository);
    });

    describe('createUser', () => {
        const userData = {
            email: 'test@example.com',
            password: 'Password123',
            firstName: 'Іван',
            lastName: 'Петренко'
        };

        test('створює користувача й не повертає хеш пароля', async () => {
            // Arrange
            userRepository.findByEmail.mockResolvedValue(null);
            argon2.hash.mockResolvedValue('hashed-password');
            userRepository.create.mockResolvedValue({
                id: 1,
                email: 'test@example.com',
                passwordHash: 'hashed-password',
                firstName: 'Іван',
                lastName: 'Петренко'
            });

            // Act
            const result = await userService.createUser(userData);

            // Assert
            expect(userRepository.findByEmail).toHaveBeenCalledWith('test@example.com');
            expect(argon2.hash).toHaveBeenCalledWith('Password123');
            expect(userRepository.create).toHaveBeenCalledWith({
                email: 'test@example.com',
                passwordHash: 'hashed-password',
                firstName: 'Іван',
                lastName: 'Петренко',
                createdAt: expect.any(Date)
            });
            expect(result).not.toHaveProperty('passwordHash');
            expect(result.email).toBe('test@example.com');
        });

        test('викидає помилку, якщо користувач уже існує', async () => {
            userRepository.findByEmail.mockResolvedValue({ id: 1, email: 'test@example.com' });

            await expect(userService.createUser(userData))
                .rejects.toThrow('Користувач з таким email вже існує');

            expect(argon2.hash).not.toHaveBeenCalled();
            expect(userRepository.create).not.toHaveBeenCalled();
        });
    });

    describe('authenticateUser', () => {
        test('повертає користувача для правильного пароля', async () => {
            userRepository.findByEmail.mockResolvedValue({
                id: 1,
                email: 'test@example.com',
                passwordHash: 'hashed-password',
                firstName: 'Іван'
            });
            argon2.verify.mockResolvedValue(true);

            const result = await userService.authenticateUser('test@example.com', 'Password123');

            expect(argon2.verify).toHaveBeenCalledWith('hashed-password', 'Password123');
            expect(result).not.toHaveProperty('passwordHash');
            expect(result.email).toBe('test@example.com');
        });

        test('повертає null для невідомого email', async () => {
            userRepository.findByEmail.mockResolvedValue(null);

            const result = await userService.authenticateUser('unknown@example.com', 'password');

            expect(result).toBeNull();
            expect(argon2.verify).not.toHaveBeenCalled();
        });

        test('повертає null для неправильного пароля', async () => {
            userRepository.findByEmail.mockResolvedValue({
                id: 1,
                email: 'test@example.com',
                passwordHash: 'hashed-password'
            });
            argon2.verify.mockResolvedValue(false);

            const result = await userService.authenticateUser('test@example.com', 'wrong-password');

            expect(result).toBeNull();
        });
    });
});
```

### Моки, стаби та інші тестові двійники

Мокування — фундаментальна техніка модульного тестування: реальну залежність замінюють контрольованою імітацією. Загальна назва для всіх таких імітацій — **тестові двійники (test doubles)**. Розрізняють кілька видів:

| Вид | Призначення | Приклад |
| --- | --- | --- |
| **Dummy** (заглушка) | заповнює параметр, який не використовується | порожній об'єкт як аргумент |
| **Stub** (стаб) | повертає заздалегідь визначені значення | `findByEmail` завжди повертає `null` |
| **Spy** (шпигун) | записує інформацію про виклики реального об'єкта | `vi.spyOn(console, 'error')` |
| **Mock** (мок) | перевіряє взаємодію: чи викликано метод і з якими аргументами | `toHaveBeenCalledWith(...)` |
| **Fake** (підробка) | спрощена робоча реалізація | база даних у пам'яті |

Різниця між моком і стабом — у призначенні: стаб *підставляє дані*, мок *перевіряє поведінку*. Vitest, як і Jest, поєднує обидва підходи в одному API — `vi.fn()` можна використовувати і як стаб, і як мок.

Основні способи створення імітацій у Vitest:

```javascript
import { vi } from 'vitest';
import fs from 'node:fs/promises';

// Автоматичне мокування модуля: усі експорти стають vi.fn()
vi.mock('nodemailer');

// Мокування з явною реалізацією
vi.mock('../../src/utils/logger.js', () => ({
    logger: { info: vi.fn(), error: vi.fn(), warn: vi.fn() }
}));

// Шпигун: підмінює метод існуючого об'єкта, повернути оригінал можна mockRestore()
const readFileSpy = vi.spyOn(fs, 'readFile').mockResolvedValue('file content');

// Складна логіка імітації
const calculateDiscount = vi.fn((price, percentage) => price * (percentage / 100));

// Керування часом: перевірка коду, що залежить від setTimeout чи Date
vi.useFakeTimers();
vi.setSystemTime(new Date('2026-01-01T00:00:00Z'));
// ... тест ...
vi.useRealTimers();
```

Мокуйте лише **межі** системи — те, що виходить за межі вашого коду (мережа, база, час, файли). Якщо в тесті замінено все підряд, він перевіряє самі імітації, а не застосунок.

Наостанок про **тести-знімки (snapshot testing)**: `expect(value).toMatchSnapshot()` зберігає результат у файл і надалі порівнює з ним. Для серверної частини вони корисні обмежено (наприклад, для шаблонів листів), бо легко перетворюються на «тест, що завжди оновлюється, доки не набридне перевіряти».

## Інтеграційне тестування API

Інтеграційні тести перевіряють, що компоненти працюють *разом*: що маршрут Express приймає запит, проміжні обробники його пропускають, сервіс викликає репозиторій, запит потрапляє в базу даних, а відповідь має очікувану форму. Саме тут виявляються помилки, яких не побачити на модульному рівні: неправильна назва стовпця, забутий проміжний обробник, порядок підключення маршрутів.

### Налаштування середовища для інтеграційних тестів

Інтеграційні тести складніші в налаштуванні: їм потрібна справжня база даних, а іноді й інші сервіси. Ключовий принцип — **ізольоване тестове середовище**, максимально схоже на робоче, але таке, що не зачіпає реальних даних: окрема база, чистий стан перед кожним тестом, імітація лише тих зовнішніх сервісів, які неможливо чи не варто викликати по-справжньому (платіжний шлюз, поштовий сервіс).

Раніше для цього зазвичай піднімали окремий екземпляр PostgreSQL і передавали параметри підключення змінними середовища. Це працює, але створює типові проблеми: «на моєму комп'ютері база є, а в колеги — ні», забуті міграції, залишки даних від попередніх запусків. Сучасне рішення — **Testcontainers**: бібліотека, яка перед тестами сама запускає одноразовий Docker-контейнер із потрібною базою, а після тестів видаляє його. Кожен запуск починається з чистого сервера тієї самої версії PostgreSQL, що й у робочому середовищі; потрібен лише запущений Docker (який ми вже використовували в курсі).

```bash
npm install --save-dev @testcontainers/postgresql pg
```

Глобальне налаштування, яке Vitest виконує один раз перед усіма інтеграційними тестами, запускає контейнер, застосовує схему бази й передає адресу підключення тестам:

```javascript
// tests/setup/global-setup.js
import { readFileSync } from 'node:fs';
import { PostgreSqlContainer } from '@testcontainers/postgresql';
import pg from 'pg';

export default async function setup({ provide }) {
    const container = await new PostgreSqlContainer('postgres:18-alpine').start();
    const databaseUrl = container.getConnectionUri();

    // Застосовуємо схему бази даних (у реальному проєкті — запуск міграцій)
    const client = new pg.Client({ connectionString: databaseUrl });
    await client.connect();
    await client.query(readFileSync('db/schema.sql', 'utf8'));
    await client.end();

    // Передаємо адресу тестам
    provide('databaseUrl', databaseUrl);

    // Функція, яку Vitest викличе після всіх тестів
    return async () => {
        await container.stop();
    };
}
```

Змінні середовища для кожного файла тестів задаються окремим файлом, що виконується до імпорту застосунку:

```javascript
// tests/setup/env.js
import { inject } from 'vitest';

process.env.NODE_ENV = 'test';
process.env.DATABASE_URL = inject('databaseUrl');
process.env.JWT_SECRET = 'test-secret-key-that-is-long-enough-32chars';
```

Допоміжні функції для роботи з тестовою базою збираємо в одному місці. Пароль тестового користувача хешуємо по-справжньому, щоб потім можна було виконати вхід:

```javascript
// tests/setup/database.js
import * as argon2 from 'argon2';
import { pool } from '../../src/db.js';

export async function cleanup() {
    // Очищаємо таблиці й скидаємо лічильники id
    await pool.query('TRUNCATE TABLE users RESTART IDENTITY CASCADE');
}

export async function seedTestData() {
    const passwordHash = await argon2.hash('Password123');
    const users = [
        { email: 'test1@example.com', firstName: 'Тест1', lastName: 'Користувач1' },
        { email: 'test2@example.com', firstName: 'Тест2', lastName: 'Користувач2' }
    ];

    for (const user of users) {
        await pool.query(
            `INSERT INTO users (email, first_name, last_name, password_hash)
             VALUES ($1, $2, $3, $4)`,
            [user.email, user.firstName, user.lastName, passwordHash]
        );
    }
}

export async function disconnect() {
    await pool.end();
}
```

Якщо ваш проєкт використовує ORM (наприклад, Sequelize з лекції 4), замість «сирої» схеми виконуйте міграції та очищайте таблиці методами ORM — принцип той самий.

### Життєвий цикл тесту: хуки

Vitest, як і Jest, надає хуки, що виконуються в певні моменти життя набору тестів:

- `beforeAll()` — один раз перед усіма тестами набору (наприклад, підключення до бази);
- `beforeEach()` — перед кожним тестом (наприклад, очищення й наповнення даними);
- `afterEach()` — після кожного тесту;
- `afterAll()` — один раз після всіх тестів (наприклад, закриття з'єднань).

Головне правило: **кожен тест має починатися з відомого стану**, незалежно від того, які тести виконувалися до нього й у якому порядку. Тому дані очищаються в `beforeEach`, а не після останнього тесту.

### Тестування API з supertest

**Supertest** — бібліотека для тестування HTTP-серверів. Вона приймає ваш Express-застосунок і надсилає до нього запити, причому **не потребує запуску сервера на реальному порту**: застосунок при цьому імпортується з окремого файла `app.js`, а `server.js` (де викликається `listen`) в тестах не використовується. Це ще одна причина розділяти створення застосунку й запуск сервера.

Перевіряти слід не лише код статусу, а й структуру та вміст відповіді, обробку помилок і дотримання контракту API — для кожного маршруту з різними сценаріями: коректні дані, некоректні дані, відсутні поля, граничні випадки.

Розглянемо тестування реєстрації та входу:

```javascript
// tests/integration/auth.test.js
import { describe, test, expect, beforeEach, afterAll } from 'vitest';
import request from 'supertest';
import app from '../../src/app.js';
import { cleanup, seedTestData, disconnect } from '../setup/database.js';

describe('Authentication API', () => {
    beforeEach(async () => {
        await cleanup();
        await seedTestData();
    });

    afterAll(async () => {
        await disconnect();
    });

    describe('POST /api/auth/register', () => {
        test('реєструє нового користувача', async () => {
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
            expect(response.body.user).not.toHaveProperty('passwordHash');
            expect(response.body.user).not.toHaveProperty('password');
        });

        test('повертає помилку для дубліката email', async () => {
            const response = await request(app)
                .post('/api/auth/register')
                .send({
                    email: 'test1@example.com', // такий користувач уже є
                    password: 'Password123',
                    firstName: 'Дублікат',
                    lastName: 'Користувач'
                })
                .expect(409);

            expect(response.body.error).toContain('вже існує');
        });

        test('валідує обов’язкові поля', async () => {
            const response = await request(app)
                .post('/api/auth/register')
                .send({ email: 'invalid-email', password: '123', firstName: '', lastName: '' })
                .expect(400);

            expect(Array.isArray(response.body.errors)).toBe(true);
            expect(response.body.errors.length).toBeGreaterThan(0);
        });
    });

    describe('POST /api/auth/login', () => {
        test('авторизує користувача з правильним паролем', async () => {
            const response = await request(app)
                .post('/api/auth/login')
                .send({ email: 'test1@example.com', password: 'Password123' })
                .expect(200);

            expect(response.body).toHaveProperty('user');
            expect(typeof response.body.token).toBe('string');
        });

        test('відхиляє неправильний пароль', async () => {
            await request(app)
                .post('/api/auth/login')
                .send({ email: 'test1@example.com', password: 'wrong-password' })
                .expect(401);
        });
    });
});
```

Зауважте, що для дубліката ми очікуємо код **409 Conflict** (а не 400): це точніше відображає ситуацію й відповідає рекомендаціям REST із лекції 3. Якщо ваш застосунок повертає інший код, змініть або код, або тест — але свідомо.

### Тестування захищених маршрутів

Маршрути, що вимагають автентифікації, потребують окремого підходу: у тесті потрібен дійсний JWT-токен. Є два шляхи — виконати справжній вхід через `/api/auth/login` (повільніше, але перевіряє весь ланцюжок) або підписати токен у тесті тим самим секретом, що використовує застосунок (швидше, зручно для маси перевірок). Ефективна стратегія — допоміжна функція для створення токенів, тестові користувачі з різними ролями та систематична перевірка сценаріїв авторизації: успішний доступ, запит без токена, з недійсним токеном, з токеном недостатньо привілейованого користувача.

```javascript
// tests/integration/users.test.js
import { describe, test, expect, beforeEach, afterAll } from 'vitest';
import request from 'supertest';
import jwt from 'jsonwebtoken';
import app from '../../src/app.js';
import { cleanup, seedTestData, disconnect } from '../setup/database.js';

function createTestToken(userId, email) {
    return jwt.sign({ userId, email }, process.env.JWT_SECRET, { expiresIn: '1h' });
}

describe('Users API', () => {
    const testUserId = 1; // перший користувач із seedTestData
    let authToken;

    beforeEach(async () => {
        await cleanup();
        await seedTestData();
        authToken = createTestToken(testUserId, 'test1@example.com');
    });

    afterAll(async () => {
        await disconnect();
    });

    describe('GET /api/users/profile', () => {
        test('повертає профіль авторизованого користувача', async () => {
            const response = await request(app)
                .get('/api/users/profile')
                .set('Authorization', `Bearer ${authToken}`)
                .expect(200);

            expect(response.body).toHaveProperty('id', testUserId);
            expect(response.body).toHaveProperty('email', 'test1@example.com');
            expect(response.body).not.toHaveProperty('passwordHash');
        });

        test('повертає 401 без токена', async () => {
            await request(app).get('/api/users/profile').expect(401);
        });

        test('повертає 401 для недійсного токена', async () => {
            await request(app)
                .get('/api/users/profile')
                .set('Authorization', 'Bearer invalid-token')
                .expect(401);
        });
    });

    describe('PUT /api/users/profile', () => {
        test('оновлює профіль користувача', async () => {
            const response = await request(app)
                .put('/api/users/profile')
                .set('Authorization', `Bearer ${authToken}`)
                .send({ firstName: 'Оновлене', lastName: "Ім'я" })
                .expect(200);

            expect(response.body.firstName).toBe('Оновлене');
            expect(response.body.lastName).toBe("Ім'я");
        });

        test('відхиляє некоректні дані', async () => {
            await request(app)
                .put('/api/users/profile')
                .set('Authorization', `Bearer ${authToken}`)
                .send({ firstName: '', email: 'invalid-email' })
                .expect(400);
        });
    });
});
```

Окремо варто перевіряти **авторизацію на рівні даних**: користувач A не повинен мати змоги прочитати чи змінити дані користувача B (запит до `/api/users/2` з токеном користувача 1 має повертати 403 або 404). Саме такі помилки контролю доступу — одні з найпоширеніших і найнебезпечніших.

### Тестування middleware та обробки помилок

Middleware (проміжні обробники) — це код, що виконується до основних обробників маршрутів: перевірка токена, обмеження частоти запитів, CORS, валідація, обробка помилок. Вони є першою лінією захисту застосунку, тому їхня надійність критична. Тестувати їх можна як ізольовано (викликаючи функцію з імітованими `req`, `res`, `next`), так і в складі повного циклу запит—відповідь, як у прикладі нижче.

```javascript
// tests/integration/middleware.test.js
import { describe, test, expect } from 'vitest';
import request from 'supertest';
import app from '../../src/app.js';

describe('Middleware Integration', () => {
    describe('Rate limiting', () => {
        test('блокує запити після перевищення ліміту', async () => {
            // У тестовому середовищі ліміт для /login налаштовано на 5 запитів
            const payload = { email: 'test@example.com', password: 'wrong' };

            for (let i = 0; i < 5; i++) {
                await request(app).post('/api/auth/login').send(payload);
            }

            const response = await request(app)
                .post('/api/auth/login')
                .send(payload)
                .expect(429);

            expect(response.body).toHaveProperty('error');
        });
    });

    describe('CORS', () => {
        test('відповідає на попередній запит браузера (preflight)', async () => {
            const response = await request(app)
                .options('/api/users')
                .set('Origin', 'http://localhost:5173')
                .set('Access-Control-Request-Method', 'GET')
                .expect(204);

            expect(response.headers['access-control-allow-origin']).toBe('http://localhost:5173');
            expect(response.headers['access-control-allow-methods']).toBeDefined();
        });
    });

    describe('Обробка помилок', () => {
        test('повертає 404 для неіснуючих маршрутів', async () => {
            const response = await request(app).get('/api/nonexistent').expect(404);
            expect(response.body).toHaveProperty('error');
        });

        test('не розкриває деталей внутрішніх помилок', async () => {
            // Маршрут /api/test/error підключається лише при NODE_ENV=test
            const response = await request(app).get('/api/test/error').expect(500);

            expect(response.body.error).toBe('Internal server error');
            expect(response.body).not.toHaveProperty('stack');
        });
    });
});
```

Два зауваження. По-перше, запит CORS preflight (`OPTIONS`) пакет `cors` за замовчуванням завершує статусом **204**, а не 200, і заголовок `Access-Control-Allow-Origin` має містити конкретне джерело, якщо воно дозволене. По-друге, тест на «внутрішню помилку» потребує спеціального маршруту, який навмисно викидає виняток; його підключають лише в тестовому середовищі, щоб він ніколи не потрапив у робоче.

Розглядаючи Express 5, пам'ятайте: помилки з `async`-обробників тепер автоматично потрапляють в обробник помилок (лекція 2), тож у тестах достатньо `throw` усередині маршруту.

## Покриття коду та безперервна інтеграція

Написати тести — половина справи. Друга половина: знати, наскільки вони повні (покриття коду), і гарантувати, що вони запускаються автоматично при кожній зміні (безперервна інтеграція).

### Налаштування покриття коду

**Покриття коду (code coverage)** — метрика, що показує, яка частина коду виконується під час тестів. Вона допомагає знайти неперевірені ділянки. Vitest збирає покриття через рушій V8 (пакет `@vitest/coverage-v8`, який ми вже встановили). Налаштування ми вже задали у `vitest.config.js`; запуск:

```bash
npm run test:coverage
```

Пороги (`thresholds`) працюють як «сторож»: якщо покриття впаде нижче 80 %, команда завершиться помилкою, і CI не пропустить зміну. Для критичних каталогів (наприклад, сервісів із бізнес-логікою) можна задати суворіші пороги окремо для шляху — див. документацію опції `coverage.thresholds`.

### Інтерпретація метрик покриття

Звіт покриття містить чотири метрики:

- **Statements** — відсоток виконаних інструкцій;
- **Branches** — відсоток перевірених гілок умовних конструкцій (`if/else`, `switch`, `?:`, `&&`);
- **Functions** — відсоток викликаних функцій;
- **Lines** — відсоток виконаних рядків.

Найінформативніша з них — гілки (branches): рядок може бути виконаний, а його «негативна» гілка — жодного разу.

Приклад консольного звіту:

```
----------------------|---------|----------|---------|---------|-------------------
File                  | % Stmts | % Branch | % Funcs | % Lines | Uncovered Line #s
----------------------|---------|----------|---------|---------|-------------------
All files             |   87.23 |    78.94 |   91.30 |   86.95 |
 src/controllers      |   95.12 |    89.47 |   96.77 |   94.87 |
  authController.js   |   97.56 |    92.31 |     100 |   97.37 | 45,67
  userController.js   |   92.68 |    86.67 |   93.33 |   92.31 | 123,145,167
 src/services         |   89.74 |    84.21 |   94.12 |   89.23 |
  userService.js      |   91.30 |    87.50 |   95.45 |   90.91 | 78,89,134
 src/utils            |   75.61 |    60.00 |   81.82 |   74.39 |
  validation.js       |   68.75 |    55.56 |   77.78 |   67.86 | 34,45,67,89,123
----------------------|---------|----------|---------|---------|-------------------
```

У прикладі найслабше місце — `src/utils/validation.js` (гілок покрито лише 55 %): саме туди слід додати тести. HTML-звіт (`coverage/index.html`) показує вихідний код із підсвіченими непокритими рядками — це найзручніший спосіб працювати зі звітом.

Важливо розуміти, що **високе покриття не гарантує якості тестів**. Можна мати 100 % покриття, а тести лише виконають код і нічого не перевірять (тест без `expect` теж «покриває» рядки). Навпаки, 80 % покриття з осмисленими перевірками цінніше, ніж 100 % поверхових. Тому покриття корисне як **індикатор дір**, а не як ціль сама по собі.

Типові хибні практики:

| Антипатерн | У чому проблема |
| --- | --- |
| Гонитва за 100 % (vanity metric) | час іде на тестування тривіального коду замість критичних місць |
| Тестування реалізації, а не поведінки | тест перевіряє, *як* написано код, і падає при кожному рефакторингу |
| Крихкі тести (brittle tests) | ламаються від незначних змін, які не порушують поведінки |
| Тести, залежні від порядку | один тест залишає дані, від яких залежить інший |

Практичне правило: 80–90 % для бізнес-логіки, менше для «клею» (конфігурація, запуск сервера), а критичні шляхи (автентифікація, платежі, права доступу) перевіряти ретельно незалежно від відсотка.

### Безперервна інтеграція з GitHub Actions

**Безперервна інтеграція (Continuous Integration, CI)** — практика автоматично збирати й перевіряти код при кожній зміні. **Безперервна доставка/розгортання (CD)** продовжує ланцюжок: успішно перевірений код автоматично доставляється на сервер. Разом вони утворюють **конвеєр (pipeline)**:

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

**GitHub Actions** — вбудована в GitHub система автоматизації. Ось її основні поняття:

- **Workflow (робочий процес)** — YAML-файл у `.github/workflows/`, що описує конвеєр;
- **Event (подія)** — те, що запускає процес: `push`, `pull_request`, розклад;
- **Job (завдання)** — група кроків, що виконується на одному виконавці (runner); завдання йдуть паралельно, якщо не вказано `needs`;
- **Step (крок)** — окрема дія: команда `run` або готова дія `uses`;
- **Action (дія)** — повторно використовуваний компонент (`actions/checkout`);
- **Matrix (матриця)** — запуск того самого завдання на різних версіях (наприклад, Node.js 22 і 24);
- **Secrets (секрети)** — зашифровані змінні, недоступні в журналах.

Ефективний конвеєр має бути **швидким** (розробник не чекатиме півгодини), **надійним** (не падає без причини) та **інформативним** (зрозуміло, що зламалося). Він повинен автоматично не пропускати зміни, що не відповідають стандартам якості: у налаштуваннях репозиторію вмикають *branch protection* — злиття в `main` можливе лише після успішного проходження перевірок.

Розглянемо повну конфігурацію для нашого проєкту. Оскільки інтеграційні тести самі піднімають PostgreSQL через Testcontainers, окремий блок `services` не потрібен: на виконавцях GitHub Docker уже встановлено.

```yaml
# .github/workflows/ci.yml
name: CI

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

# Мінімальні права за замовчуванням — принцип найменших привілеїв
permissions:
  contents: read

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        # Node.js 20 вийшов з підтримки у квітні 2026, тому перевіряємо LTS-версії 22 та 24
        node-version: [22.x, 24.x]

    steps:
      - name: Отримати код
        uses: actions/checkout@v6

      - name: Налаштувати Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v6
        with:
          node-version: ${{ matrix.node-version }}
          cache: npm

      - name: Встановити залежності
        run: npm ci

      - name: Перевірка стилю коду (lint)
        run: npm run lint

      - name: Тести з покриттям (модульні та інтеграційні)
        run: npm run test:coverage
        env:
          NODE_ENV: test

      - name: Зберегти звіт покриття
        if: matrix.node-version == '24.x'
        uses: actions/upload-artifact@v4
        with:
          name: coverage-report
          path: coverage/

  security:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v6
      - uses: actions/setup-node@v6
        with:
          node-version: 24.x
          cache: npm
      - run: npm ci
      - name: Аудит залежностей
        run: npm audit --audit-level=high

  build:
    needs: [test, security]
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v6
      - name: Зібрати Docker-образ (перевірка, що збірка можлива)
        run: docker build -t my-api:${{ github.sha }} .

  deploy:
    # Розгортання лише з гілки main і лише після push (не з pull request)
    if: github.ref == 'refs/heads/main' && github.event_name == 'push'
    needs: [build]
    runs-on: ubuntu-latest
    environment: production   # дозволяє вимагати ручне підтвердження
    steps:
      - uses: actions/checkout@v6

      - name: Встановити Railway CLI
        run: npm install -g @railway/cli

      - name: Розгорнути на Railway
        run: railway up --ci --service "$RAILWAY_SERVICE"
        env:
          RAILWAY_TOKEN: ${{ secrets.RAILWAY_TOKEN }}
          RAILWAY_SERVICE: ${{ vars.RAILWAY_SERVICE }}

      - name: Димові тести
        run: |
          curl --fail --silent --show-error \
               --retry 10 --retry-delay 6 --retry-connrefused \
               "${{ vars.PRODUCTION_URL }}/health/ready"
```

Що змінилося порівняно з типовими прикладами минулих років і чому:

- версії дій `checkout` і `setup-node` піднято до v6 (старі, що працюють на Node.js 16, знято з підтримки; `upload-artifact` третьої версії GitHub вимкнув повністю — використовуйте актуальну);
- усі етапи, включно з розгортанням, живуть в одному файлі: залежність `needs` можлива лише між завданнями **одного** workflow;
- замість `sleep 30` для очікування запуску сервісу використано повторні спроби `curl --retry` — це і швидше, і надійніше;
- вимірюємо покриття одним запуском усіх тестів, а не запускаємо тести кілька разів;
- секрети й налаштування розділено: чутливе (`RAILWAY_TOKEN`) — у **secrets**, нечутливе (адреса сервісу) — у **variables**.

Корисно також налаштувати **Dependabot** — сервіс GitHub, який сам створює pull request'и з оновленнями залежностей та дій:

```yaml
# .github/dependabot.yml
version: 2
updates:
  - package-ecosystem: npm
    directory: /
    schedule:
      interval: weekly
  - package-ecosystem: github-actions
    directory: /
    schedule:
      interval: weekly
```

Метрики, за якими оцінюють якість конвеєра: **час збірки (build time)**, **частка успішних запусків**, **частота розгортань** та **час від коміту до робочого середовища (lead time)**. Це чотири показники «DORA», прийняті для оцінки ефективності команд розробки.

### Автоматичне розгортання та стратегії випуску

Автоматичне розгортання — логічне продовження CI. Ключові принципи: **поетапність** (спершу проміжне тестове середовище — staging, тоді робоче — production), **можливість швидкого відкоту (rollback)** та **перевірки після розгортання** (димові тести — коротка серія запитів, що підтверджує «сервіс піднявся й відповідає»).

Середовища розділяють: гілка `develop` може автоматично розгортатися на staging, а розгортання на production відбувається лише з `main` після проходження всіх перевірок, за потреби — з ручним підтвердженням (саме для цього у прикладі вказано `environment: production`).

Спосіб заміни старої версії новою також вибирають свідомо:

| Стратегія | Як працює | Плюси | Мінуси |
| --- | --- | --- | --- |
| **Recreate** (зупинити й запустити) | стару версію зупиняють, потім запускають нову | найпростіша | простій під час оновлення |
| **Rolling** (поступове оновлення) | екземпляри замінюють по одному | без простою, мало ресурсів | дві версії працюють одночасно |
| **Blue-green** | нову версію (green) розгортають поруч зі старою (blue) і перемикають трафік | миттєвий відкіт | вдвічі більше ресурсів |
| **Canary** («канарка») | нову версію спершу отримує невелика частка користувачів | ризик обмежений | складніший моніторинг |

Більшість PaaS-платформ, які ми розглянемо далі, використовують rolling-оновлення за замовчуванням і не замінюють старий екземпляр, доки новий не пройшов перевірку стану (health check) — тому така перевірка є обов'язковою.

### Безпека в тестуванні та конвеєрі

Безпеку перевіряють на кількох рівнях, і всі їх можна автоматизувати:

- **SAST (Static Application Security Testing)** — статичний аналіз коду без запуску: плагін `eslint-plugin-security` для ESLint, Semgrep, **CodeQL** (безкоштовний для публічних репозиторіїв GitHub);
- **Аналіз залежностей** — `npm audit`, сповіщення Dependabot: більшість уразливостей потрапляє в застосунок через сторонні пакети, а не через власний код;
- **DAST (Dynamic Application Security Testing)** — тестування працюючого застосунку «ззовні», наприклад інструментом OWASP ZAP;
- **Сканування секретів** — GitHub автоматично шукає випадково закомічені ключі (secret scanning).

Приклад підключення правил безпеки в ESLint (плоска конфігурація):

```javascript
// eslint.config.js
import security from 'eslint-plugin-security';

export default [
    security.configs.recommended,
    // ...інші правила
];
```

Окремо пишуть інтеграційні тести безпеки — перевірка поведінки, що не повинна порушуватися: обмеження частоти запитів, відмова без токена, неможливість прочитати чужі дані, коректні налаштування CORS, наявність захисних заголовків. Наприклад, якщо застосунок використовує `helmet`, перевіримо це:

```javascript
test('надсилає захисні заголовки', async () => {
    const response = await request(app).get('/health/live');

    expect(response.headers['x-content-type-options']).toBe('nosniff');
    expect(response.headers['strict-transport-security']).toBeDefined();
    expect(response.headers['x-powered-by']).toBeUndefined();
});
```

Найважливіші заголовки: **HSTS** (Strict-Transport-Security) — змушує браузер користуватися лише HTTPS; **CSP** (Content-Security-Policy) — обмежує джерела скриптів, що знижує ризик міжсайтового скриптингу; **X-Content-Type-Options** — забороняє браузеру «вгадувати» тип вмісту. Орієнтиром для переліку ризиків слугує актуальна редакція **OWASP Top 10**: порушення контролю доступу, ін'єкції, помилки автентифікації, небезпечна конфігурація, вразливі компоненти та інші. Багато з цих тем ми детально розглядали в лекції 5.

### Навантажувальне тестування

Функціональні тести відповідають на запитання «чи працює?», а **тести продуктивності** — «чи витримає, і за яких умов?». Розрізняють кілька видів:

| Вид | Мета |
| --- | --- |
| **Load** (навантажувальне) | перевірити роботу за очікуваного навантаження |
| **Stress** (стрес-тестування) | знайти межу, після якої система відмовляє |
| **Spike** (стрибкове) | реакція на раптовий сплеск трафіку |
| **Endurance / soak** (витривалості) | тривала робота: виявлення витоків пам'яті |
| **Volume** (обсягу) | поведінка при великих обсягах даних |

Головні показники: **час відповіді** (зазвичай дивляться на перцентилі — p95, p99, а не на середнє), **пропускна здатність (throughput)** — запитів за секунду, кількість одночасних користувачів, споживання CPU й пам'яті, тривалість запитів до бази даних.

Популярні інструменти: **k6** (сценарії пишуться на JavaScript), **Artillery**, **Apache JMeter**; для аудиту продуктивності клієнтської частини — Lighthouse. Приклад сценарію k6, який 30 секунд імітує 20 одночасних користувачів і сам оцінює результат:

```javascript
// load-test.js — запуск: k6 run load-test.js
import http from 'k6/http';
import { check, sleep } from 'k6';

export const options = {
    vus: 20,
    duration: '30s',
    thresholds: {
        http_req_failed: ['rate<0.01'],     // менше 1 % помилок
        http_req_duration: ['p(95)<500']    // 95 % запитів швидше за 500 мс
    }
};

export default function () {
    const response = http.get('http://localhost:3000/api/products');
    check(response, { 'статус 200': (r) => r.status === 200 });
    sleep(1);
}
```

Навантажувальні тести запускають на окремому (проміжному) середовищі, а не на робочому, і не в кожному коміті, а за розкладом чи перед релізом.

### Налагодження тестів і масштабування набору тестів

Коли тест падає, потрібні інструменти діагностики. Найчастіше корисні такі прапорці Vitest: `-t "частина назви"` — запустити лише вибрані тести; `--reporter=verbose` — докладний вивід; `--bail=1` — зупинитися після першої помилки; режим спостереження `vitest` (без `run`) — миттєвий перезапуск після зміни файла. Для покрокового налагодження в VS Code додайте конфігурацію запуску:

```json
{
    "type": "node",
    "request": "launch",
    "name": "Debug Vitest",
    "autoAttachChildProcesses": true,
    "skipFiles": ["<node_internals>/**", "**/node_modules/**"],
    "program": "${workspaceFolder}/node_modules/vitest/vitest.mjs",
    "args": ["run", "--no-file-parallelism"],
    "smartStep": true,
    "console": "integratedTerminal"
}
```

Рекомендована структура тестів і домовленості про іменування:

```
tests/
├── unit/           # модульні тести
├── integration/    # інтеграційні тести
├── setup/          # глобальні налаштування, допоміжні функції для бази
├── fixtures/       # тестові дані
└── helpers/        # спільні допоміжні функції
```

Файли тестів іменують `*.test.js` (або `*.spec.js`), файли даних — `*.fixture.js`, ручні моки — у каталозі `__mocks__/`. Тестові дані створюють за допомогою **фабричних функцій** (`buildUser({ email: 'x@y.z' })`), які генерують об'єкт зі значеннями за замовчуванням, і **прибирають після кожного тесту**, щоб тести не впливали один на одного.

Коли тестів стає багато, час їх виконання зростає. Основні способи його скоротити: паралельний запуск файлів (типова поведінка Vitest); **шардування (sharding)** — розбиття набору на частини, що виконуються на різних виконавцях CI (`vitest run --shard=1/3`); запуск лише тестів, яких стосується зміна; переносимі контейнери Testcontainers замість спільної бази; база даних у пам'яті чи fake-об'єкти для модульних тестів.

## Конфігурація середовищ

Один і той самий код працює в кількох середовищах: на комп'ютері розробника (development), у тестах (test), на проміжному сервері (staging) і в робочому середовищі (production). Відрізняються вони адресами баз даних, ключами, рівнем журналювання, суворістю обмежень. Правильне керування цими відмінностями — питання і гнучкості, і безпеки: пароль від робочої бази, що потрапив у репозиторій, — це інцидент.

### Принцип 12-Factor App

Джерелом сучасних практик є методологія **Twelve-Factor App** — набір принципів для застосунків, які розгортаються як сервіси. Третій принцип формулює головну ідею: **конфігурація зберігається в змінних середовища, а не в коді**. Критерій простий: якби репозиторій став публічним просто зараз, чи не розкрилося б через це щось секретне? Якщо так — конфігурацію відокремлено недостатньо.

З цього випливає, що правильний підхід — **один код і різні змінні середовища**, а не «окремий конфігураційний файл для кожного середовища» з паролями всередині. Середовища відрізняються значеннями змінних:

| Параметр | Development | Test | Production |
| --- | --- | --- | --- |
| База даних | локальна або Docker | одноразовий контейнер | керована база хмарної платформи |
| Підключення до БД | без SSL | без SSL | з SSL |
| Рівень журналювання | `debug` | `error` | `info` або `warn` |
| Формат журналу | зрозумілий для людини | мінімальний | JSON |
| Обмеження частоти запитів | ліберальне | малі значення для перевірки | суворе |
| Термін дії токена | довгий (зручність) | короткий | короткий |
| Джерела CORS | `localhost` | не важливо | лише домен клієнтської частини |

### Структура конфігурації

Читаємо змінні в одному модулі й перевіряємо їх при запуску. Значення за замовчуванням задаємо лише для нечутливих параметрів; для секретів значення за замовчуванням **небезпечні** — застосунок краще не запустити взагалі, ніж запустити з вигаданим ключем.

Для локальної розробки змінні зручно зберігати у файлі `.env` (він **обов'язково** додається до `.gitignore`), а в репозиторії тримати шаблон `.env.example` без реальних значень. Node.js уміє читати такі файли самостійно, без пакета `dotenv`:

```bash
# запуск із файлом змінних
node --env-file=.env src/server.js

# необов'язковий файл (не помилка, якщо його немає, — зручно для CI та контейнерів)
node --env-file-if-exists=.env src/server.js
```

```bash
# .env.example — шаблон, який потрапляє в репозиторій
NODE_ENV=development
PORT=3000
DATABASE_URL=postgresql://user:password@localhost:5432/appdb
JWT_SECRET=замініть-на-випадковий-рядок-не-менше-32-символів
ALLOWED_ORIGINS=http://localhost:5173
LOG_LEVEL=debug
```

Ось єдиний конфігураційний модуль, що замінює три окремі файли `development.js`, `production.js`, `test.js`:

```javascript
// src/config/index.js
import Joi from 'joi';

const envSchema = Joi.object({
    NODE_ENV: Joi.string().valid('development', 'production', 'test').default('development'),
    PORT: Joi.number().port().default(3000),

    DATABASE_URL: Joi.string().uri({ scheme: ['postgres', 'postgresql'] }).required(),
    DB_SSL: Joi.boolean().default(false),
    DB_POOL_MAX: Joi.number().integer().min(1).default(10),

    JWT_SECRET: Joi.string().min(32).required(),
    JWT_EXPIRES_IN: Joi.string().default('1h'),

    ALLOWED_ORIGINS: Joi.string().default('http://localhost:5173'),
    RATE_LIMIT_MAX: Joi.number().integer().min(1).default(100),
    LOG_LEVEL: Joi.string()
        .valid('error', 'warn', 'info', 'http', 'verbose', 'debug', 'silly')
        .default('info'),
    SENTRY_DSN: Joi.string().uri().allow('').default('')
}).unknown(true); // в середовищі є й інші змінні (PATH, HOME тощо)

const { error, value: env } = envSchema.validate(process.env, { abortEarly: false });

if (error) {
    const problems = error.details.map((detail) => detail.message).join('; ');
    throw new Error(`Некоректна конфігурація: ${problems}`);
}

export const config = {
    env: env.NODE_ENV,
    port: env.PORT,
    database: {
        connectionString: env.DATABASE_URL,
        ssl: env.DB_SSL ? { rejectUnauthorized: false } : false,
        max: env.DB_POOL_MAX
    },
    jwt: {
        secret: env.JWT_SECRET,
        expiresIn: env.JWT_EXPIRES_IN
    },
    cors: {
        origin: env.ALLOWED_ORIGINS.split(',').map((origin) => origin.trim()),
        credentials: true
    },
    rateLimit: {
        windowMs: 15 * 60 * 1000, // 15 хвилин
        max: env.RATE_LIMIT_MAX
    },
    logging: { level: env.LOG_LEVEL },
    monitoring: { sentryDsn: env.SENTRY_DSN }
};
```

Такий підхід дає три переваги: жодних секретів у коді, єдине місце, де видно всі налаштування, і — завдяки перевірці — миттєва зрозуміла помилка при запуску, якщо чогось бракує (див. наступний підрозділ).

### Валідація конфігурацій

Помилку конфігурації найкраще виявити **на старті** застосунку, а не через кілька годин, коли виконається код, що вперше звернувся до відсутнього параметра. Схема валідації перевіряє типи (порт — число в допустимому діапазоні), обов'язковість (адреса бази), допустимі значення (`NODE_ENV`) і мінімальну надійність секретів (довжина JWT-ключа). Вище ми вже використали для цього бібліотеку Joi — та сама, що й для перевірки вхідних даних запитів у лекції 3. Якщо змінної немає, застосунок завершується з повідомленням на кшталт `"JWT_SECRET" is required` — і розробник одразу бачить причину, а платформа розгортання не вважає таку версію справною й не перемикає на неї трафік.

Альтернатива Joi — бібліотека **Zod**, яка стає дедалі популярнішою завдяки інтеграції з TypeScript: описана схема одночасно дає і перевірку, і тип даних.

### Захист чутливих даних

Паролі, ключі API та секрети підпису **ніколи** не зберігаються в коді й не потрапляють у репозиторій. Якщо секрет випадково закомічено, вважайте його скомпрометованим: видалення коміту не допоможе, бо історія зберігається, а боти сканують публічні репозиторії за лічені хвилини. Єдине правильне рішення — **негайно відкликати й замінити** ключ.

Практичні рівні захисту (від простого до складного):

1. **Файл `.env` поза репозиторієм** — для локальної розробки; `.gitignore` та сканування секретів (secret scanning на GitHub, `gitleaks`);
2. **Сховище секретів платформи** — змінні середовища в Railway, Render, DigitalOcean, GitHub Secrets для CI: значення зашифровані й не потрапляють у журнали;
3. **Керовані сервіси секретів** — AWS Secrets Manager, Azure Key Vault, Google Secret Manager, HashiCorp Vault: централізоване зберігання, ротація ключів, журнал доступу;
4. **Короткочасні облікові дані замість довгострокових** — наприклад, автентифікація GitHub Actions у хмарі через OIDC без збереження ключа.

Шифрування секретів усередині самого застосунку потрібне рідко: зазвичай достатньо сховища платформи. Але для дуже конкретних випадків (наприклад, зберігання в базі токенів користувачів зі сторонніх сервісів) застосовують шифрування на рівні застосунку. Ось приклад із алгоритмом AES-256-GCM, який забезпечує і конфіденційність, і виявлення підробки даних:

```javascript
// src/utils/secrets.js
import { createCipheriv, createDecipheriv, randomBytes } from 'node:crypto';

const ALGORITHM = 'aes-256-gcm';

export function generateKey() {
    return randomBytes(32); // 256 біт
}

export function encrypt(text, key) {
    const iv = randomBytes(12); // унікальний вектор для кожного шифрування
    const cipher = createCipheriv(ALGORITHM, key, iv);

    const encrypted = Buffer.concat([cipher.update(text, 'utf8'), cipher.final()]);
    const authTag = cipher.getAuthTag();

    return {
        encrypted: encrypted.toString('hex'),
        iv: iv.toString('hex'),
        authTag: authTag.toString('hex')
    };
}

export function decrypt({ encrypted, iv, authTag }, key) {
    const decipher = createDecipheriv(ALGORITHM, key, Buffer.from(iv, 'hex'));
    decipher.setAuthTag(Buffer.from(authTag, 'hex'));

    const decrypted = Buffer.concat([
        decipher.update(Buffer.from(encrypted, 'hex')),
        decipher.final() // викине помилку, якщо дані змінено
    ]);

    return decrypted.toString('utf8');
}
```

Зверніть увагу: у старих посібниках можна зустріти `crypto.createCipher` — цю функцію **видалено з Node.js** (вона була небезпечною, бо ключ перетворювався на вектор ініціалізації без випадковості). Використовуйте лише `createCipheriv` з унікальним вектором `iv` для кожної операції. Сам ключ шифрування при цьому має зберігатися окремо від даних — у сховищі секретів, а не поруч у базі.

## Розгортання на хмарних платформах

Розгортання (deployment) — це доставка готового коду на сервер, доступний користувачам, і запуск його там. Ще кілька років тому для цього доводилося купувати сервер, налаштовувати операційну систему, встановлювати Node.js і вручну копіювати файли. Сьогодні більшість навчальних і невеликих комерційних проєктів розгортають на хмарних платформах, які беруть на себе більшу частину цієї роботи. Завдання розробника — обрати рівень контролю, який відповідає проєкту, і налаштувати процес так, щоб розгортання було повторюваним і безпечним.

### Моделі хмарних послуг: IaaS, PaaS, SaaS

Хмарні послуги розрізняють за тим, скільки відповідальності бере на себе провайдер:

| Модель | Що надає провайдер | Що робите ви | Приклади |
| --- | --- | --- | --- |
| **IaaS** (Infrastructure as a Service) | віртуальні машини, мережа, диски | ОС, оновлення, Node.js, запуск, безпека сервера | AWS EC2, DigitalOcean Droplets |
| **PaaS** (Platform as a Service) | середовище виконання, збірка, розгортання, масштабування | код застосунку та його конфігурація | Railway, Render, Heroku, DigitalOcean App Platform |
| **SaaS** (Software as a Service) | готовий застосунок | користуєтеся ним | Gmail, GitHub, Google Docs |

Чим ближче до PaaS, тим менше налаштувань і швидший старт, але менший контроль і, як правило, вища ціна за ресурси. Чим ближче до IaaS, тим більше свободи — і тим більше обов'язків: оновлення безпеки, резервні копії, налаштування HTTPS лягають на вас.

### Контейнеризація з Docker

Перш ніж розглядати конкретні платформи, зробимо крок, який полегшує розгортання будь-де: запакуємо застосунок у **контейнер**. Контейнер містить код, потрібну версію Node.js і залежності, тож застосунок працює однаково на комп'ютері розробника, у CI та на сервері — зникає проблема «у мене працює». Для нашого сервісу достатньо невеликого багатоетапного Dockerfile:

```dockerfile
# Dockerfile
# Етап 1: встановлення лише робочих залежностей
FROM node:24-alpine AS deps
WORKDIR /app
COPY package*.json ./
RUN npm ci --omit=dev

# Етап 2: фінальний образ
FROM node:24-alpine
ENV NODE_ENV=production
WORKDIR /app

COPY --from=deps /app/node_modules ./node_modules
COPY package*.json ./
COPY src ./src

# Образ node вже містить непривілейованого користувача node
USER node

EXPOSE 3000

# Запускаємо node напряму, а не через npm start:
# так сигнал завершення (SIGTERM) доходить до застосунку
CMD ["node", "src/server.js"]
```

Що тут важливо:

- **`node:24-alpine`** — образ із поточною LTS-версією Node.js; Alpine дає малий розмір. (Приклади з версією 18 у старих посібниках вже не підтримуються.)
- **Багатоетапна збірка** — на першому етапі встановлюємо залежності, на другому копіюємо лише готовий результат. Так у фінальний образ не потрапляють зайві файли.
- **`npm ci --omit=dev`** замінило застарілий прапорець `--only=production`.
- **Непривілейований користувач (`USER node`)** — якщо зловмисник отримає контроль над процесом, він не матиме прав адміністратора в контейнері.
- **Файл `.dockerignore`** не дозволяє копіювати в образ зайве й небезпечне:

```
node_modules
.git
.env
coverage
tests
*.md
```

Перевірити образ локально: `docker build -t my-api .`, потім `docker run --env-file .env -p 3000:3000 my-api`. Усі платформи, що розглядаються нижче, вміють збирати й запускати такий Dockerfile — або збирають застосунок самі, якщо його немає.

### Розгортання на Railway

Railway — сучасна PaaS-платформа, яка визначає тип проєкту, сама збирає його та запускає. Вона інтегрується з репозиторіями GitHub (кожен push у вибрану гілку автоматично запускає нове розгортання), автоматично видає HTTPS-сертифікат, дозволяє одним кліком додати PostgreSQL і надає панель із журналами та метриками.

Плата — за фактичне використання ресурсів. Постійного безкоштовного плану Railway не має: нові користувачі отримують пробний кредит, далі потрібен платний план. Умови змінюються, тому перед вибором платформи для власного проєкту перевіряйте актуальні тарифи на сайті.

Для стандартного проєкту на Node.js конфігурація може бути мінімальною або відсутньою. Якщо потрібно налаштувати поведінку, створюють файл `railway.json` у корені репозиторію:

```json
{
  "build": {
    "builder": "RAILPACK"
  },
  "deploy": {
    "startCommand": "node src/server.js",
    "healthcheckPath": "/health/ready",
    "restartPolicyType": "ON_FAILURE",
    "restartPolicyMaxRetries": 3
  }
}
```

Збиральником за замовчуванням є **Railpack** — наступник Nixpacks, який використовували в матеріалах попередніх років. Якщо в репозиторії є `Dockerfile`, Railway збере образ за ним. Ключове поле — `healthcheckPath`: платформа не перемкне трафік на нову версію, доки цей маршрут не відповість успішно, — тому збій при старті (наприклад, через помилку конфігурації) не «покладе» справну попередню версію. Точні назви полів завжди звіряйте з поточною документацією платформи.

#### Змінні середовища для Railway

Змінні задають у панелі проєкту (вкладка Variables) або через CLI. Зверніть увагу, що адресу бази даних не вписують вручну: Railway дозволяє посилатися на змінні іншого сервісу проєкту.

```bash
# Змінні середовища сервісу на Railway
NODE_ENV=production
DATABASE_URL=${{Postgres.DATABASE_URL}}   # посилання на сервіс бази даних
JWT_SECRET=<випадковий рядок, див. нижче>
ALLOWED_ORIGINS=https://your-frontend-domain.com
RATE_LIMIT_MAX=100
LOG_LEVEL=info
# PORT платформа задає сама — застосунок має читати його зі змінної середовища
```

Безпечний секрет генерують командою `node -p "require('node:crypto').randomBytes(48).toString('hex')"` — і вставляють значення в панель платформи, а не в код.

Розгортання з командного рядка (окрім автоматичного з GitHub):

```bash
npm install -g @railway/cli
railway login
railway link      # прив'язати каталог до проєкту
railway up        # завантажити код і запустити розгортання
```

### Розгортання на Heroku

Heroku — платформа, яка сформувала сучасне уявлення про PaaS: розгортання командою `git push`, «аддони» для баз даних і черг, зрозумілі поняття. Вона використовує концепцію **dyno** — легкого контейнера, у якому працює процес застосунку; масштабування — збільшення кількості dyno. Heroku досі популярний у командах, які цінують зрілу екосистему та передбачуваність.

Але з часів, коли на ньому починали кожен навчальний проєкт, змінилося головне: **безкоштовний план Heroku скасовано в листопаді 2022 року**. Найдешевші варіанти — платні (dyno класу Eco із «засинанням» після 30 хвилин без запитів і Basic, що працює постійно), а базу даних додають окремим платним аддоном. Тому для навчання й прототипів частіше обирають інші платформи, а Heroku розглядають, коли потрібна його екосистема.

Основні файли налаштувань:

```json
{
  "scripts": {
    "start": "node src/server.js",
    "dev": "node --watch --env-file=.env src/server.js",
    "db:migrate": "node src/db/migrate.js"
  },
  "engines": {
    "node": "24.x"
  }
}
```

Поле `engines.node` повідомляє платформі, яку версію Node.js використовувати. Файл `Procfile` описує типи процесів. Зверніть увагу на **фазу `release`**: команда в ній виконується під час кожного розгортання *до* переключення на нову версію, і саме там слід запускати міграції бази даних (замість застарілого способу через `heroku-postbuild`). Якщо міграція впаде, нова версія просто не буде випущена:

```
web: node src/server.js
release: npm run db:migrate
worker: node src/workers/backgroundJobs.js
```

### Налаштування бази даних для Heroku

Heroku автоматично надає змінну середовища `DATABASE_URL` після додавання аддона PostgreSQL. Дві речі потребують уваги — SSL і кількість з'єднань. З'єднання з базою на Heroku відбувається через SSL; простий і поширений спосіб — вимкнути перевірку сертифіката (`rejectUnauthorized: false`), проте це знижує захист від підміни сервера, тому в серйозних проєктах сертифікат перевіряють. А на початкових тарифах кількість одночасних з'єднань до бази обмежена, тому розмір пулу (`max`) має бути меншим за ліміт, а всі частини застосунку повинні користуватися **одним спільним пулом**.

```javascript
// src/db.js
import pg from 'pg';
import { config } from './config/index.js';

export const pool = new pg.Pool({
    connectionString: config.database.connectionString,
    ssl: config.database.ssl,
    max: config.database.max,          // не більше ліміту тарифу
    idleTimeoutMillis: 30_000,
    connectionTimeoutMillis: 2_000
});
```

Цей самий модуль ми вже використовували в тестах (`tests/setup/database.js`) — саме тому пул варто виносити в окремий файл. Також пам'ятайте, що dyno класу Eco «засинають» без трафіку, тож перший запит після паузи виконується повільно; для постійно доступних сервісів використовують плани, що працюють без засинання.

### Розгортання на Render

Render — ще одна платформа, яка вважається прямою наступницею Heroku: веб-сервіси, фонові процеси, завдання за розкладом, керована PostgreSQL. Її звичайний спосіб роботи — репозиторій підключено, кожен push розгортає нову версію. Є безкоштовний план для веб-сервісів (сервіс «засинає» після періоду бездіяльності, тому перше звернення повільне) і для бази даних (з обмеженим терміном дії), що робить Render зручним для навчальних проєктів.

Конфігурацію можна зберегти як код у файлі `render.yaml` («Blueprint») — тоді все середовище відтворюється однією дією:

```yaml
# render.yaml
services:
  - type: web
    name: my-api
    runtime: node
    plan: free
    buildCommand: npm ci
    startCommand: node src/server.js
    healthCheckPath: /health/ready
    envVars:
      - key: NODE_ENV
        value: production
      - key: DATABASE_URL
        fromDatabase:
          name: my-db
          property: connectionString
      - key: JWT_SECRET
        generateValue: true     # платформа згенерує випадкове значення

databases:
  - name: my-db
    plan: free
```

Підхід «інфраструктура як код» (infrastructure as code) — конфігурація середовища зберігається у репозиторії поруч із кодом, проходить перевірку так само, як код, і не залежить від «налаштувань, які хтось колись вибрав у панелі» — є загальною тенденцією; його підтримують також Railway (`railway.json`) і DigitalOcean (`.do/app.yaml`).

### Розгортання на DigitalOcean

DigitalOcean дає більший контроль над інфраструктурою через кілька сервісів: **App Platform** (PaaS), **Droplets** (віртуальні машини, IaaS) і керований Kubernetes. Це дозволяє обрати рівень абстракції відповідно до потреб проєкту. App Platform — найпростіший варіант, схожий за досвідом на Heroku чи Render; Droplets надають повний контроль над операційною системою; Kubernetes призначений для великих застосунків зі складними вимогами до оркестрації контейнерів.

Конфігурація App Platform у вигляді файла:

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
    run_command: node src/server.js
    environment_slug: node-js
    instance_count: 1
    instance_size_slug: basic-xxs
    envs:
      - key: NODE_ENV
        value: production
      - key: JWT_SECRET
        scope: RUN_TIME
        type: SECRET
        value: ${JWT_SECRET}
      - key: DATABASE_URL
        value: ${db.DATABASE_URL}
    health_check:
      http_path: /health/ready
      initial_delay_seconds: 10
      period_seconds: 10
      timeout_seconds: 5
      success_threshold: 1
      failure_threshold: 3

databases:
  - name: db
    engine: PG
    version: "17"
    size: db-s-dev-database
```

#### Налаштування для Droplet із PM2

Droplet — це віртуальний сервер, на якому все налаштовуєте ви: встановлення Node.js, файервол, обмеження доступу за SSH-ключами, зворотний проксі **Nginx** (він приймає HTTPS-запити й пересилає їх застосунку), сертифікати Let's Encrypt (зазвичай через `certbot`), резервні копії й оновлення безпеки. Це найбільш трудомісткий, але й найдешевший та найгнучкіший варіант, і саме він найкраще показує, що насправді роблять PaaS-платформи «за лаштунками».

Щоб застосунок працював постійно, автоматично перезапускався після збою й використовував усі ядра процесора, його запускають під керуванням менеджера процесів. Популярний варіант для Node.js — **PM2**: він підтримує режим кластера (окремий процес на кожне ядро), перезапуск при збоях чи перевищенні пам'яті, журнали й перезавантаження без простою. Альтернативи — юніт **systemd** (стандартний засіб Linux) або Docker із політикою перезапуску.

Оскільки наш проєкт використовує ES Modules (`"type": "module"`), файл конфігурації PM2 має розширення `.cjs`:

```javascript
// ecosystem.config.cjs — конфігурація PM2
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
        time: true,
        max_memory_restart: '1G'
    }]
};
```

Скрипт розгортання, який виконується на сервері після надходження нового коду:

```bash
#!/bin/bash
# deploy.sh — скрипт розгортання на Droplet
set -euo pipefail   # зупинитися при першій помилці, не ігнорувати незадані змінні

echo "Початок розгортання..."

# Оновлення коду
git pull origin main

# Встановлення лише робочих залежностей
npm ci --omit=dev

# Міграції бази даних
npm run db:migrate

# Перезавантаження без простою
pm2 reload ecosystem.config.cjs --env production

echo "Розгортання завершено."
```

Рядок `set -euo pipefail` — важлива деталь: без нього скрипт продовжить виконання навіть після невдалої міграції й перезапустить застосунок із неузгодженою базою.

### Порівняння платформ

| Платформа | Модель | Контроль | Безкоштовний варіант | Складність | Коли обирати |
| --- | --- | --- | --- | --- | --- |
| **Railway** | PaaS | середній | пробний кредит | низька | швидкий старт, невеликі та навчальні проєкти |
| **Render** | PaaS | середній | так, з обмеженнями | низька | навчальні проєкти, міграція з Heroku |
| **Heroku** | PaaS | середній | немає | низька | наявні проєкти, зріла екосистема аддонів |
| **DigitalOcean App Platform** | PaaS | середній | обмежено (статичні сайти) | низька | помірний контроль, передбачувана ціна |
| **DigitalOcean Droplet** | IaaS | повний | немає | висока | повний контроль, навчання адмініструванню |

Вибір залежить від потреб проєкту, бюджету й того, скільки часу команда готова витрачати на адміністрування. Для більшості навчальних завдань цього курсу достатньо PaaS-платформи; Droplet варто спробувати хоча б раз, щоб розуміти, що відбувається «під капотом».

### Коректне завершення роботи (graceful shutdown)

Під час розгортання платформа зупиняє стару версію: надсилає процесу сигнал **SIGTERM** і через кілька секунд, якщо процес не завершився, примусово вбиває його (SIGKILL). Якщо застосунок просто «падає» на сигнал, усі запити, що виконувалися в цю мить, обриваються з помилкою, а з'єднання з базою залишаються незакритими. **Коректне завершення** означає: перестати приймати нові з'єднання, дочекатися завершення поточних запитів, закрити пул бази даних і лише тоді завершити процес.

```javascript
// src/server.js
import app from './app.js';
import { config } from './config/index.js';
import { pool } from './db.js';
import { logger } from './utils/logger.js';

const server = app.listen(config.port, () => {
    logger.info(`Сервер запущено на порту ${config.port}`);
});

let isShuttingDown = false;

async function shutdown(signal, exitCode = 0) {
    if (isShuttingDown) return;
    isShuttingDown = true;
    logger.info(`Отримано ${signal}, завершуємо роботу`);

    // Страховка: якщо коректне завершення зависло — примусово вийти
    setTimeout(() => {
        logger.error('Час очікування вичерпано, примусове завершення');
        process.exit(1);
    }, 10_000).unref();

    // Припиняємо приймати нові з'єднання, чекаємо завершення поточних
    server.close(async () => {
        await pool.end();
        logger.info('Сервер зупинено');
        process.exit(exitCode);
    });
}

process.on('SIGTERM', () => shutdown('SIGTERM'));
process.on('SIGINT', () => shutdown('SIGINT'));

// Необроблені помилки: записуємо, повідомляємо й завершуємо процес.
// Продовжувати роботу після uncaughtException небезпечно — стан процесу невідомий.
process.on('unhandledRejection', (reason) => {
    logger.error('Unhandled rejection', { reason: String(reason) });
    shutdown('unhandledRejection', 1);
});

process.on('uncaughtException', (error) => {
    logger.error('Uncaught exception', { error: error.message, stack: error.stack });
    shutdown('uncaughtException', 1);
});
```

Цей код пов'язаний з перевірками стану, які ми розглянемо далі: поки сервіс завершує роботу, перевірка готовності (readiness) має повертати 503, щоб балансувальник перестав надсилати запити. Обробники необроблених помилок винесено сюди, у точку запуску, а не в middleware: це стосується всього процесу, а не окремого запиту.

## Моніторинг та логування

Розгорнути застосунок — лише половина справи. Далі він працює без нашого нагляду, і питання «чи все гаразд?» потребує відповіді щохвилини: чому користувач бачить помилку, чому відповіді стали повільними, чи не закінчується пам'ять. Тести (див. початок лекції) ловлять помилки **до** випуску, а моніторинг — **після** нього. Разом вони замикають цикл якості: те, що прослизнуло повз тести, ми принаймні швидко помічаємо й виправляємо.

### Структуроване журналювання з Winston

Найпростіше журналювання — `console.log`. Воно годиться для навчального прикладу, але в робочому середовищі його недостатньо: немає рівнів важливості, немає часових позначок, неможливо змінити деталізацію без правки коду, а головне — рядок тексту незручно шукати й агрегувати. Тому застосовують **структуроване журналювання**: кожен запис — це об'єкт (зазвичай JSON) з окремими полями `timestamp`, `level`, `message` та контекстом. Такий журнал зручно фільтрувати («покажи всі помилки користувача 42 за останню годину») і передавати в системи збирання логів.

Однією з найпоширеніших бібліотек є **Winston**. Вона підтримує кілька рівнів і кілька «транспортів» — місць призначення записів (консоль, файл, зовнішній сервіс). Рівні Winston за замовчуванням — це рівні npm (від найважливішого): `error`, `warn`, `info`, `http`, `verbose`, `debug`, `silly`. Це не те саме, що рівні syslog (RFC 5424), тож не варто плутати назви. Рівень, заданий у налаштуваннях, означає «записувати цей рівень і все важливіше»: при `info` потраплять `error`, `warn` і `info`, а `debug` буде відкинуто.

```javascript
// src/utils/logger.js
import winston from 'winston';
import { config } from '../config/index.js';

const { combine, timestamp, errors, json, colorize, printf } = winston.format;

// У розробці — кольоровий рядок, зрозумілий людині; у production — JSON
const devFormat = combine(
    colorize(),
    timestamp({ format: 'HH:mm:ss' }),
    errors({ stack: true }),
    printf(({ timestamp, level, message, stack, ...meta }) => {
        const details = Object.keys(meta).length ? ` ${JSON.stringify(meta)}` : '';
        return `${timestamp} ${level}: ${stack ?? message}${details}`;
    })
);

const prodFormat = combine(timestamp(), errors({ stack: true }), json());

export const logger = winston.createLogger({
    level: config.logging.level,
    format: config.env === 'production' ? prodFormat : devFormat,
    defaultMeta: { service: 'user-api' },
    transports: [new winston.transports.Console()],
    silent: config.env === 'test' // у тестах не засмічуємо вивід
});
```

Зверніть увагу, що ми пишемо лише в консоль (потік `stdout`), і це свідомий вибір. За принципом 12-Factor застосунок **не керує файлами журналів** сам: він виводить потік подій у стандартний вивід, а платформа (Railway, Render, Docker, Kubernetes) збирає його, зберігає й дає інструменти пошуку. Записи у файли на диску контейнера зникають при кожному перезапуску, а ротація файлів (щоб вони не заповнили диск) — зайва відповідальність для застосунку. Файлові транспорти доречні хіба що на власному сервері (Droplet), де за журнали відповідаєте ви.

Приклад використання в коді:

```javascript
import { logger } from '../utils/logger.js';

logger.info('Користувача створено', { userId: user.id });
logger.warn('Повторна спроба входу', { email, attempts });
logger.error('Помилка запиту до бази даних', { err: error, query: 'SELECT ...' });
```

Що **не** можна журналювати: паролі, токени, повні номери карток, тіла запитів цілком (`req.body` у запиті входу містить пароль), заголовок `Authorization`. Журнали зазвичай доступні ширшому колу людей, ніж база даних, і зберігаються довше, тому витік через них — типова помилка. Якщо потрібно зберегти контекст, журналюйте окремі безпечні поля.

**Pino** — сучасна альтернатива Winston. Вона вважається значно швидшою (пише JSON синхронно-мінімалістично й виносить форматування в окремий процес), тому її часто обирають для сервісів із великим навантаженням і вона є типовим журналювальником у Fastify. Ідея та сама — структуровані записи з рівнями, тож перехід між бібліотеками не становить труднощів. У межах курсу ми залишаємося з Winston: він добре задокументований і має гнучкі формати.

### Middleware для журналювання запитів

Кожен HTTP-запит — подія, яку варто зафіксувати: метод, шлях, код відповіді й тривалість. Це основа для пошуку повільних маршрутів і для розслідування інцидентів. Реалізуємо middleware (див. лекцію 2), який записує запис **після** завершення відповіді, коли вже відомий код статусу:

```javascript
// src/middleware/requestLogger.js
import { randomUUID } from 'node:crypto';
import { logger } from '../utils/logger.js';

export function requestLogger(req, res, next) {
    const startedAt = process.hrtime.bigint();

    // Ідентифікатор запиту дозволяє зв'язати всі записи журналу однієї операції
    req.id = req.get('x-request-id') ?? randomUUID();
    res.set('X-Request-Id', req.id);

    res.on('finish', () => {
        const durationMs = Number(process.hrtime.bigint() - startedAt) / 1e6;
        const level = res.statusCode >= 500 ? 'error' : res.statusCode >= 400 ? 'warn' : 'http';

        logger.log(level, 'Оброблено HTTP-запит', {
            requestId: req.id,
            method: req.method,
            path: req.originalUrl.split('?')[0], // без рядка запиту: у ньому можуть бути токени
            status: res.statusCode,
            durationMs: Math.round(durationMs),
            userId: req.user?.id
        });
    });

    next();
}
```

```javascript
// src/app.js
import express from 'express';
import { requestLogger } from './middleware/requestLogger.js';

const app = express();
app.use(requestLogger); // першим, щоб фіксувати всі запити, зокрема відхилені далі
```

Два прийоми заслуговують на увагу. По-перше, **ідентифікатор запиту** (`requestId`) — наскрізна мітка, за якою в системі збирання логів знаходять усі записи однієї операції; його також повертають клієнту в заголовку, тож користувач може назвати його в службі підтримки. По-друге, ми не пишемо в журнал рядок запиту (`?token=...`) і тіло — лише те, що безпечно й корисно для діагностики.

Готовою альтернативою є пакет `morgan` (формат запису в стилі веб-серверів) або `pino-http`, але власний middleware з десяти рядків краще показує, що відбувається.

### Моніторинг стану системи (health checks)

Платформа розгортання має якось дізнатися, що застосунок живий і готовий приймати запити. Для цього роблять спеціальні маршрути, які **перевіряють** стан. Їх зазвичай розділяють на два, бо запитання різні:

- **Liveness** (`/health/live`) — «процес живий?». Якщо ні, платформа його перезапускає. Перевірка дуже проста й не залежить від зовнішніх ресурсів: якщо база даних тимчасово недоступна, перезапуск застосунку її не полагодить.
- **Readiness** (`/health/ready`) — «чи готовий обробляти запити?». Перевіряє залежності (наприклад, базу даних). Якщо ні, платформа тимчасово **не спрямовує трафік** на цей екземпляр, але й не вбиває його.

```javascript
// src/routes/health.js
import { Router } from 'express';
import { pool } from '../db.js';

export const health = { shuttingDown: false }; // прапорець змінюється в src/server.js

export const healthRouter = Router();

healthRouter.get('/live', (req, res) => {
    res.json({ status: 'ok', uptime: Math.round(process.uptime()) });
});

healthRouter.get('/ready', async (req, res) => {
    if (health.shuttingDown) {
        return res.status(503).json({ status: 'shutting_down' });
    }

    try {
        const startedAt = Date.now();
        await pool.query('SELECT 1'); // використовуємо спільний пул, а не нове з'єднання
        res.json({ status: 'ok', database: { status: 'up', latencyMs: Date.now() - startedAt } });
    } catch (error) {
        res.status(503).json({ status: 'unavailable', database: { status: 'down' } });
    }
});
```

```javascript
// src/app.js
import { healthRouter } from './routes/health.js';

app.use('/health', healthRouter);
```

Кілька важливих зауважень:

1. Перевірка бази використовує **той самий пул з'єднань**, що й решта застосунку. Якщо кожен виклик створюватиме нове з'єднання, ви самі можете вичерпати ліміт з'єднань бази.
2. У разі збою повертаємо код **503 Service Unavailable**, а не 200 із текстом «помилка». Саме за кодом відповіді платформа вирішує, що робити.
3. Прапорець `shuttingDown` виставляється в обробнику `SIGTERM` (див. коректне завершення роботи в розділі про розгортання): щойно платформа вирішила зупинити екземпляр, він перестає бути «готовим», і нові запити йдуть на інші.
4. Не показуйте в публічних відповідях зайвих подробиць (версії бібліотек, рядки підключення, тексти помилок бази) — це допомагає зловмисникам.

Ці маршрути прописують у налаштуваннях платформи (`healthcheckPath` у `railway.json`, `healthCheckPath` у `render.yaml`) і в блоці `healthcheck` для Docker Compose. Саме тому зупинка старого екземпляра при розгортанні відбувається лише тоді, коли новий пройшов перевірку готовності: це основа розгортання без простою.

Окремо від перевірок стану існують **метрики** — числові показники, які збирають із часом: кількість запитів, тривалість відповідей, використання пам'яті, розмір пулу з'єднань. Поширений формат — Prometheus, а для Node.js є бібліотека **prom-client**:

```javascript
// src/routes/metrics.js
import { Router } from 'express';
import client from 'prom-client';

client.collectDefaultMetrics(); // пам'ять, event loop, збирання сміття тощо

export const httpDuration = new client.Histogram({
    name: 'http_request_duration_seconds',
    help: 'Тривалість обробки HTTP-запитів',
    labelNames: ['method', 'route', 'status'],
    buckets: [0.05, 0.1, 0.3, 0.5, 1, 2, 5]
});

export const metricsRouter = Router();

metricsRouter.get('/', async (req, res) => {
    res.set('Content-Type', client.register.contentType);
    res.send(await client.register.metrics());
});
```

Маршрут `/metrics` **не повинен бути загальнодоступним**: він розкриває внутрішню будову системи. Його або закривають мережевими правилами платформи, або захищають авторизацією, або відкривають лише на окремому внутрішньому порту.

### Інтеграція з сервісами моніторингу

Власноруч дивитися журнали й метрики незручно, тому користуються спеціалізованими сервісами. Вони бувають кількох типів:

| Категорія | Приклади | Що дають |
| --- | --- | --- |
| Відстеження помилок | Sentry, Bugsnag | групування однакових помилок, стек викликів, контекст, сповіщення |
| Метрики та панелі | Grafana + Prometheus, Datadog, New Relic | графіки, порогові сповіщення, аналіз тенденцій |
| Збирання журналів | Grafana Loki, Better Stack, ELK, Datadog Logs | пошук і аналіз записів усіх екземплярів |
| Перевірка доступності ззовні | UptimeRobot, Better Stack, Pingdom | «чи відкривається сайт із зовнішнього світу?» |
| Трасування | Jaeger, Tempo, Honeycomb, Datadog APM | шлях запиту через кілька сервісів |

Для навчальних проєктів достатньо безкоштовних рівнів Sentry та UptimeRobot: разом вони відповідають на два головні запитання — «що зламалося?» і «чи доступний застосунок?».

Постачальники відходять від власних агентів, і галузевим стандартом збирання телеметрії стає **OpenTelemetry** (OTel). Це відкритий, не прив'язаний до вендора набір специфікацій і бібліотек: застосунок один раз інструментується за допомогою OTel і надсилає журнали, метрики й трейси до будь-якого сумісного сервісу. Так ви не залежите від конкретного постачальника, а міграція між сервісами не потребує переписування коду. Для Node.js достатньо підключити SDK і автоматичну інструментацію Express, `pg`, `http`:

```javascript
// src/instrumentation.js — завантажується ДО решти коду
import { NodeSDK } from '@opentelemetry/sdk-node';
import { getNodeAutoInstrumentations } from '@opentelemetry/auto-instrumentations-node';
import { OTLPTraceExporter } from '@opentelemetry/exporter-trace-otlp-http';

const sdk = new NodeSDK({
    serviceName: 'user-api',
    traceExporter: new OTLPTraceExporter(), // адреса й ключі беруться зі змінних OTEL_EXPORTER_OTLP_*
    instrumentations: [getNodeAutoInstrumentations()]
});

sdk.start();
```

```bash
# запуск: модуль інструментації завантажується першим
node --import ./src/instrumentation.js src/server.js
```

Ключова вимога: інструментація має бути завантажена **до** імпорту Express та інших бібліотек, інакше вони не будуть «обгорнуті». Саме тому використовують прапорець `--import` (ESM-еквівалент старого `--require`).

### Відстеження помилок та сповіщення

Помилки в робочому середовищі неминучі. Питання в тому, чи дізнаєтеся ви про них першими, чи від розгніваного користувача. **Sentry** перехоплює необроблені винятки, збирає стек викликів, дані про середовище, версію застосунку, ланцюжок дій перед помилкою й групує однакові випадки, щоб тисяча повторень не перетворилась на тисячу листів.

Сучасні версії Sentry SDK (починаючи з 8-ї) побудовані на OpenTelemetry і мають іншу схему підключення, ніж у старих посібниках: **немає** `Sentry.Handlers.requestHandler()` і `Sentry.Handlers.errorHandler()`. Замість цього SDK ініціалізують у окремому файлі, що завантажується першим, а обробник помилок Express додають однією функцією:

```javascript
// src/instrument.js — завантажується першим (node --import ./src/instrument.js ...)
import * as Sentry from '@sentry/node';
import { config } from './config/index.js';

if (config.monitoring.sentryDsn) {
    Sentry.init({
        dsn: config.monitoring.sentryDsn,
        environment: config.env,
        release: process.env.RELEASE_SHA, // версія (хеш коміту) — щоб бачити, який випуск зламався
        tracesSampleRate: config.env === 'production' ? 0.1 : 1.0,
        sendDefaultPii: false // не надсилати персональні дані за замовчуванням
    });
}
```

```javascript
// src/app.js
import * as Sentry from '@sentry/node';
import express from 'express';

const app = express();

// ... усі маршрути ...

// Обробник Sentry — ПІСЛЯ маршрутів, але ДО власного обробника помилок
Sentry.setupExpressErrorHandler(app);

// власний фінальний обробник — див. нижче
app.use(errorHandler);
```

Не менш важливий і власний обробник помилок, який формує відповідь клієнту. Для типових ситуацій зручно ввести **власні класи помилок** із HTTP-кодами (див. лекцію 3): тоді обробник відрізняє «очікувані» помилки (не знайдено, не пройшла валідація) від «неочікуваних» (збій, який треба розслідувати).

```javascript
// src/utils/errors.js
export class AppError extends Error {
    constructor(message, statusCode = 500, code = 'INTERNAL_ERROR') {
        super(message);
        this.name = this.constructor.name;
        this.statusCode = statusCode;
        this.code = code;
        this.expected = statusCode < 500; // операційна помилка, а не збій
    }
}

export class NotFoundError extends AppError {
    constructor(resource = 'Ресурс') {
        super(`${resource} не знайдено`, 404, 'NOT_FOUND');
    }
}

export class ValidationError extends AppError {
    constructor(message, details = []) {
        super(message, 400, 'VALIDATION_ERROR');
        this.details = details;
    }
}
```

```javascript
// src/middleware/errorHandler.js
import { logger } from '../utils/logger.js';
import { config } from '../config/index.js';

export function errorHandler(err, req, res, next) {
    if (res.headersSent) {
        return next(err); // відповідь уже почато — віддаємо стандартному обробнику Express
    }

    const statusCode = err.statusCode ?? 500;
    const isServerError = statusCode >= 500;

    // Журналюємо помилку та контекст, але НЕ тіло запиту (там можуть бути паролі)
    logger.log(isServerError ? 'error' : 'warn', err.message, {
        requestId: req.id,
        method: req.method,
        path: req.originalUrl.split('?')[0],
        status: statusCode,
        code: err.code,
        stack: isServerError ? err.stack : undefined
    });

    res.status(statusCode).json({
        error: {
            code: err.code ?? 'INTERNAL_ERROR',
            // серверні помилки не розкриваємо клієнту
            message: isServerError && config.env === 'production'
                ? 'Внутрішня помилка сервера'
                : err.message,
            ...(err.details && { details: err.details }),
            requestId: req.id
        }
    });
}
```

Ця поведінка перевіряється тестами, які ми писали в розділі про інтеграційне тестування: відповідь 500 не містить стека викликів, а в тілі є `requestId`, за яким розробник знайде запис у журналі.

Сповіщення (alerting) — те, що перетворює моніторинг з «панелі, на яку хтось колись дивиться» на реальний захист. Кілька правил, як робити їх корисними:

- **Сповіщайте про симптоми, а не про причини.** «Частка помилок 5xx перевищила 2 % упродовж 5 хвилин» — це симптом, що зачіпає користувачів. «Завантаження процесора 85 %» — може бути й нормою.
- **Кожне сповіщення має вимагати дії.** Якщо на нього можна не реагувати, воно шкодить: через «втому від сповіщень» справжня проблема губиться серед шуму.
- **Вказуйте пріоритети.** Критичне (сайт недоступний) — негайно в месенджер або на телефон; попередження (місце на диску закінчується) — до звичайного каналу.
- **Додавайте контекст:** посилання на панель, останній випуск, інструкцію дій (runbook).
- **Відстежуйте зв'язок із випусками.** Різкий сплеск помилок одразу після розгортання — сигнал до відкоту.

### Спостережуваність: журнали, метрики, трейси

Розглянуті засоби складають єдину картину — **спостережуваність** (observability): здатність зрозуміти, що відбувається всередині системи, за її зовнішніми сигналами. Класично виділяють три стовпи:

| Сигнал | Відповідає на запитання | Приклад | Інструменти |
| --- | --- | --- | --- |
| Журнали (logs) | Що саме сталося? | «Користувач 42 не зміг увійти: невірний пароль» | Winston, Pino, Loki |
| Метрики (metrics) | Скільки й наскільки швидко? | 120 запитів/с, p95 = 340 мс | Prometheus, prom-client |
| Трейси (traces) | Де витрачено час? | запит пройшов API → база → зовнішній сервіс | OpenTelemetry, Jaeger |

Моніторинг перевіряє те, що ми **передбачили** («чи відповідає `/health/ready`?»), а спостережуваність допомагає розібратися в тому, чого ми **не передбачили** («чому саме цей користувач отримує повільні відповіді?»). Для невеликого сервісу достатньо структурованих журналів, кількох метрик і відстеження помилок; трейси стають необхідними, коли запит проходить через кілька сервісів.

Що саме вимірювати? Практичну відповідь дають **чотири золоті сигнали** (з практики Site Reliability Engineering у Google):

1. **Затримка (latency)** — скільки триває обробка запиту. Дивіться на перцентилі (p95, p99), а не на середнє: середнє приховує повільні відповіді для частини користувачів.
2. **Трафік (traffic)** — скільки запитів надходить.
3. **Помилки (errors)** — яка частка запитів завершується збоєм.
4. **Насичення (saturation)** — наскільки завантажено обмежені ресурси: процесор, пам'ять, пул з'єднань бази.

Щоб перетворити ці вимірювання на обіцянки, вводять три поняття:

| Поняття | Розшифровка | Що означає | Приклад |
| --- | --- | --- | --- |
| **SLI** | Service Level Indicator | вимірюваний показник якості | частка успішних запитів |
| **SLO** | Service Level Objective | внутрішня ціль для SLI | 99,5 % успішних запитів за 30 днів |
| **SLA** | Service Level Agreement | зовнішня угода з наслідками за порушення | повернення коштів, якщо доступність нижча за 99 % |

SLO задає **бюджет помилок**: якщо ціль — 99,5 %, то 0,5 % запитів можуть бути невдалими, і це нормально. Поки бюджет не вичерпано, команда сміливо випускає нові функції; якщо вичерпано — пріоритет переходить до надійності. Так розмова про «стабільність проти швидкості» стає предметною, а не суб'єктивною. Для навчальних проєктів SLA не потрібні, але сформулювати простий SLO (наприклад, «95 % запитів виконуються швидше ніж 500 мс») корисно: це привчає мислити вимірюваними цілями.

## Висновки та найкращі практики

Ця лекція охопила повний шлях коду від комп'ютера розробника до користувача: як перевірити його правильність, як автоматизувати перевірку, як налаштувати середовища, куди розгорнути й як стежити за роботою. Її головна думка проста: **сервер — це не лише код, а й процес його випуску та експлуатації**, і якість цього процесу важить не менше за якість самого коду.

### Тестування

- Будуйте набір тестів за формою піраміди: багато швидких модульних тестів, помірно інтеграційних, кілька наскрізних. Для API інтеграційні тести з реальною базою (Testcontainers) дають найкраще співвідношення довіри до вартості.
- Тестуйте **поведінку**, а не реалізацію; дотримуйтесь патерну AAA; кожен тест має бути незалежним і повторюваним.
- Мокайте лише зовнішні межі (мережа, час, сторонні сервіси), а не власну логіку.
- Покриття коду — індикатор прогалин, а не мета. Поріг у 70–80 % корисний як запобіжник, але не замінює осмислених перевірок.
- Виправляючи помилку, спершу пишіть тест, що її відтворює.

### Автоматизація та безпека

- Кожна зміна проходить автоматичний конвеєр: тести, лінтер, перевірка залежностей, збирання. Гілка `main` захищена: злиття можливе лише після успішного конвеєра.
- Дотримуйтесь мінімальних прав для токенів конвеєра (`permissions: contents: read`), тримайте секрети в GitHub Secrets і закріплюйте версії дій.
- Автоматизуйте оновлення залежностей (Dependabot) і сканування вразливостей (`npm audit`, CodeQL).
- Обирайте стратегію розгортання відповідно до ризиків: для навчальних проєктів вистачить поступового оновлення з перевіркою готовності, для критичних систем — blue-green або canary.

### Конфігурація та розгортання

- Один код, різні змінні середовища; жодних секретів у репозиторії; перевірка конфігурації при запуску.
- Пакуйте застосунок у Docker-образ: він однаково працює на ноутбуку, у CI й на сервері. Використовуйте багатоетапне збирання, актуальну LTS-версію Node.js і запуск від непривілейованого користувача.
- Обробляйте `SIGTERM` і коректно завершуйте роботу: закрити сервер, завершити поточні запити, звільнити пул з'єднань.
- Обирайте платформу за критеріями керованості, вартості та вимог до даних, а не за модою; закладайте можливість переїзду (контейнер, змінні середовища).

### Моніторинг

- Журналюйте структуровано, у стандартний вивід, з ідентифікатором запиту й без чутливих даних.
- Розділяйте `/health/live` та `/health/ready`; не залишайте `/metrics` відкритим.
- Відстежуйте чотири золоті сигнали, формулюйте прості SLO, налаштовуйте сповіщення про симптоми, що потребують дій.
- Підключіть відстеження помилок (Sentry) і зовнішню перевірку доступності ще до першого реального користувача.

### Що далі

Ми завершили розгляд серверної частини. У наступній лекції переходимо до клієнтської: вивчимо бібліотеку React і компонентний підхід до побудови інтерфейсів. Знання цієї лекції нам знадобляться і там: ті самі принципи тестування (Vitest) застосовуються до компонентів, а зібраний застосунок розгортається тим самим конвеєром, що й серверна частина.

### Питання для самоперевірки

1. Чим відрізняються модульні, інтеграційні та наскрізні тести? Чому піраміду тестів будують саме так?
2. Опишіть цикл Red-Green-Refactor. Яку проблему він допомагає уникнути?
3. Чим стаб відрізняється від моку? Коли доцільно, а коли шкідливо використовувати моки?
4. Чому інтеграційні тести API краще запускати проти реальної бази в контейнері, ніж проти моків?
5. Що означає покриття коду 100 % і чому воно не гарантує відсутності помилок?
6. З яких кроків складається конвеєр CI/CD? Чим безперервна доставка відрізняється від безперервного розгортання?
7. Чому конфігурацію зберігають у змінних середовища? Що робити, якщо секрет потрапив у репозиторій?
8. У чому різниця між IaaS, PaaS і SaaS? До якої категорії належать Railway та Droplet?
9. Навіщо багатоетапне збирання Docker-образу й запуск від користувача `node`?
10. Чим відрізняються перевірки liveness і readiness? Що станеться, якщо в liveness перевіряти базу даних?
11. Що таке коректне завершення роботи сервера і що відбудеться без нього при розгортанні нової версії?
12. Які чотири золоті сигнали варто вимірювати? Чому для затримки використовують перцентилі, а не середнє?
13. Чим відрізняються SLI, SLO і SLA?
