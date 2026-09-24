# Лекція 4. Бази даних та ORM

## Вступ до сучасних баз даних у веброзробці

У попередній лекції ми спроєктували REST API: визначили ресурси, адреси, формати запитів і відповідей, правила валідації та обробки помилок. Проте в тих прикладах за викликами на кшталт `userService.findById()` ховалася «чорна скринька». Настав час її відкрити. Щойно сервер перезапуститься, усе, що зберігалося в його пам'яті, зникне, тож будь-який реальний застосунок потребує постійного сховища даних — бази даних.

База даних є критичним компонентом архітектури будь-якого серйозного застосунку. Від вибору системи керування базами даних (СКБД) і способу роботи з нею залежать масштабованість, продуктивність та надійність усієї системи. Помилку у виборі фреймворку клієнтської частини можна виправити переписуванням інтерфейсу; помилка в моделі даних зазвичай «проростає» в увесь код і дорого коштує роками.

Історично більшість вебзастосунків використовували реляційні бази даних з мовою SQL. З розвитком інтернету, зростанням обсягів даних і появою нових типів застосунків виникла потреба в альтернативних підходах, і з'явилися NoSQL-рішення з іншими моделями даних та способами масштабування. Сьогодні ці два світи багато в чому зблизилися: PostgreSQL уміє зберігати документи JSON, а MongoDB підтримує транзакції.

Окремою темою лекції є **об'єктно-реляційне відображення (Object-Relational Mapping, ORM)** — інструменти, що дозволяють працювати з даними у звичному об'єктно-орієнтованому стилі, не пишучи SQL вручну для кожної операції. ORM стали мостом між світом об'єктів у програмі та світом таблиць у базі даних, але, як і будь-яка абстракція, мають свою ціну, яку треба розуміти.

Лекція побудована так: спочатку порівняємо реляційний і документний підходи, потім детально розглянемо PostgreSQL і роботу з ним через Sequelize, далі — MongoDB і Mongoose, і наостанок — архітектурні патерни, що дозволяють відокремити логіку застосунку від конкретної бази даних: Repository, сервісний шар, пули з'єднань і кешування. Ці патерни знадобляться й у лекції 5, де ми зберігатимемо користувачів, хеші паролів і токени.

## SQL та NoSQL бази даних: філософські відмінності

### Реляційна модель даних та SQL

**SQL-бази даних** ґрунтуються на реляційній моделі даних, запропонованій Едгаром Коддом у 1970 році. Ця модель організовує дані у вигляді таблиць (відношень) зі строго визначеними схемами, де кожен рядок представляє запис, а кожен стовпець — атрибут сутності.

Фундаментальні принципи реляційної моделі включають атомарність значень (кожне значення в клітинці таблиці неподільне), унікальність рядків через первинні ключі та зв'язки між таблицями через зовнішні ключі. Дані нормалізуються: кожен факт зберігається в одному місці, а потрібне представлення «збирається» запитом з кількох таблиць. Така структурованість забезпечує високу цілісність даних і можливість виконувати складні аналітичні запити, про які розробник навіть не думав під час проєктування схеми.

**Властивості ACID** є основою надійності реляційних СКБД:

- **Атомарність (Atomicity)** — транзакція виконується повністю або не виконується взагалі;
- **Узгодженість (Consistency)** — транзакція переводить базу з одного коректного стану в інший, не порушуючи обмежень;
- **Ізольованість (Isolation)** — паралельні транзакції не бачать проміжних результатів одна одної (у межах обраного рівня ізоляції);
- **Довговічність (Durability)** — зафіксовані зміни не зникнуть навіть після збою живлення.

```sql
-- Приклад складного SQL-запиту з з'єднаннями таблиць
SELECT
    u.username,
    u.email,
    COUNT(DISTINCT p.id) AS post_count,
    AVG(r.rating)        AS avg_rating
FROM users u
LEFT JOIN posts p   ON u.id = p.user_id
LEFT JOIN reviews r ON p.id = r.post_id
WHERE u.created_at > '2026-01-01'
GROUP BY u.id, u.username, u.email
HAVING COUNT(DISTINCT p.id) > 5
ORDER BY avg_rating DESC NULLS LAST, post_count DESC;
```

Реляційні бази даних відмінно підходять для застосунків з чіткою структурою даних, складними зв'язками, потребою в довільних запитах і високими вимогами до цілісності. Фінансові системи, облік, CRM та ERP, електронні журнали й системи замовлень традиційно використовують SQL-бази через їхню надійність і зрілість.

### NoSQL парадигма та різноманітність підходів

**NoSQL (Not Only SQL)** — збірна назва для баз даних, що не використовують реляційну модель. Вони виникли як відповідь на обмеження традиційних систем у контексті великих обсягів даних, горизонтального масштабування та роботи з даними змінної структури. Важливо розуміти, що NoSQL — не одна технологія, а кілька принципово різних моделей даних.

**Документо-орієнтовані бази даних** зберігають дані у вигляді документів, найчастіше в JSON-подібному форматі. Найпопулярніший представник — MongoDB, яка зберігає документи в бінарному форматі BSON. Документи можуть мати вкладену структуру, а колекція не вимагає однакової схеми для всіх документів. Головна ідея — зберігати разом те, що разом читається.

```javascript
// Приклад документа в MongoDB
{
  "_id": ObjectId("..."),
  "username": "ivan_p",
  "email": "ivan@example.com",
  "profile": {
    "firstName": "Іван",
    "lastName": "Петренко",
    "dateOfBirth": ISODate("2005-05-15"),
    "interests": ["програмування", "музика", "подорожі"]
  },
  "addresses": [
    { "type": "home", "city": "Луцьк", "postalCode": "43000" }
  ],
  "settings": {
    "theme": "dark",
    "notifications": {
      "email": true,
      "push": false
    }
  }
}
```

**Сховища «ключ-значення»**, такі як Redis або Valkey, пропонують найпростішу модель: кожен запис — це унікальний ключ і пов'язане з ним значення. Дані зберігаються в оперативній пам'яті, тому доступ надзвичайно швидкий. Такі сховища ідеально підходять для кешування, сесій користувачів, лічильників, черг і обмеження частоти запитів.

**Стовпчикові (column-family) бази даних**, як Cassandra, оптимізовані для величезних обсягів записів, розподілених між багатьма серверами. Близькі до них за назвою, але інші за призначенням **колонкові аналітичні СКБД** (наприклад, ClickHouse) зберігають дані по стовпцях і дуже швидко виконують агрегації над мільярдами рядків.

**Графові бази даних**, як Neo4j, спеціалізуються на зберіганні й обході складних зв'язків між сутностями: соціальні мережі, рекомендації, маршрути.

### Порівняльний аналіз SQL та NoSQL підходів

**Масштабованість.** Реляційні бази традиційно масштабуються вертикально — потужнішим сервером, що має фізичні й економічні межі. Горизонтальне масштабування (розподіл даних між кількома серверами) для них можливе, але складне: з'єднання й транзакції між серверами дорогі. Багато NoSQL-систем від початку проєктуються для горизонтального масштабування. Водночас для переважної більшості вебзастосунків одного добре налаштованого сервера PostgreSQL із репліками для читання вистачає з великим запасом.

**Схема даних.** У SQL-базах схема сувора й визначається до запису даних; її зміна вимагає міграцій. NoSQL-бази пропонують гнучкість схеми. Але варто пам'ятати: схема все одно існує — просто вона переїжджає з бази даних у код застосунку. Саме тому для MongoDB використовують Mongoose зі схемами, про що йтиметься далі.

**Узгодженість даних.** SQL-системи гарантують строгу узгодженість через ACID. Багато розподілених NoSQL-систем обирають модель **кінцевої узгодженості (eventual consistency)**: після запису різні вузли деякий час можуть повертати різні значення, але з часом вони зійдуться.

Теоретичною основою цього вибору є **теорема CAP** (Ерік Брюер, 2000). Вона стверджує, що розподілена система не може одночасно гарантувати всі три властивості:

- **C (Consistency)** — кожне читання повертає найсвіжіший запис;
- **A (Availability)** — кожен запит до працюючого вузла отримує відповідь;
- **P (Partition tolerance)** — система продовжує працювати, коли мережа між вузлами розривається.

```mermaid
graph TB
    subgraph "Теорема CAP"
        C[Consistency<br/>Узгодженість]
        A[Availability<br/>Доступність]
        P[Partition Tolerance<br/>Стійкість до розділення]

        C --- A
        A --- P
        P --- C
    end

    subgraph "Вибір під час розділення мережі"
        CP[CP: відмовити у відповіді,<br/>але не віддати застарілі дані<br/>MongoDB за замовчуванням,<br/>PostgreSQL із синхронною реплікацією]
        AP[AP: відповісти будь-що,<br/>узгодити пізніше<br/>Cassandra, DynamoDB]
    end

    P --> CP
    P --> AP
```

Поширене формулювання «можна обрати будь-які дві властивості з трьох» і твердження «SQL-бази — це CA-системи» є спрощенням, яке вводить в оману. У розподіленій системі розриви мережі неминучі, тож від P відмовитися не можна. Справжній вибір стоїть лише в момент розділення: або зберегти узгодженість ціною доступності (CP), або доступність ціною узгодженості (AP). Одиночний сервер PostgreSQL взагалі не є розподіленою системою, тому до нього теорема CAP застосовується лише тоді, коли з'являються репліки.

Розширення теореми, модель **PACELC**, додає важливе уточнення: навіть коли мережа працює нормально (Else), система вибирає між затримкою (Latency) і узгодженістю (Consistency). Наприклад, синхронна реплікація дає узгодженість, але кожен запис чекає підтвердження від репліки.

**Продуктивність** залежить від характеру навантаження, а не від типу бази «взагалі». SQL-бази оптимізовані для складних запитів із з'єднаннями та агрегаціями. Документні бази швидкі, коли весь потрібний об'єкт лежить в одному документі й читається одним зверненням. Твердження «NoSQL швидший за SQL» без уточнення, для якого сценарію, — міф.

| Критерій | Реляційні (PostgreSQL) | Документні (MongoDB) |
|----------|------------------------|----------------------|
| Модель даних | Таблиці, нормалізація, зовнішні ключі | Документи з вкладеними об'єктами |
| Схема | Сувора, у базі даних | Гнучка, контролюється кодом (Mongoose) |
| Зв'язки | З'єднання (JOIN) — сильна сторона | Вкладення або посилання; `$lookup` дорожчий |
| Транзакції | ACID — основа моделі | Є для кількох документів, але це виняток, а не норма |
| Масштабування | Переважно вертикальне + репліки | Вбудований шардинг |
| Типові задачі | Облік, замовлення, фінанси, звітність | Каталоги, контент, профілі, журнали подій |

Вибір між SQL та NoSQL не має бути категоричним. Багато сучасних застосунків використовують **полімодальне зберігання (polyglot persistence)**: кожна частина системи використовує найбільш придатний для неї тип сховища. Ми повернемося до цього у висновках лекції.

## PostgreSQL: потужність реляційної моделі

### Архітектурні особливості PostgreSQL

**PostgreSQL** — найпотужніша СКБД з відкритим кодом, яка поєднує надійність класичних реляційних систем з можливостями, що роблять її конкурентоспроможною з комерційними рішеннями. Стабільна гілка на момент написання лекції — **PostgreSQL 18** (вийшла у вересні 2025 року), яка додала підсистему асинхронного введення-виведення, що помітно пришвидшує читання з диска, збереження статистики планувальника під час оновлення версії та вбудовану функцію генерації ідентифікаторів `uuidv7()`. Наступна версія, PostgreSQL 19, наприкінці вересня 2026 року перебуває на етапі бета-тестування; її випуск очікується восени. Для навчання та нових проєктів беріть останню стабільну версію.

PostgreSQL використовує **багатоверсійне керування паралельним доступом (Multi-Version Concurrency Control, MVCC)**. Замість того щоб блокувати рядки під час читання, система зберігає кілька версій рядка, і кожна транзакція бачить узгоджений «знімок» даних. У результаті читання не блокує запис, а запис не блокує читання. Зворотний бік MVCC — старі версії рядків накопичуються, і їх прибирає фоновий процес автоочищення (autovacuum), стан якого варто моніторити на навантажених системах.

**Розширюваність** є однією з ключових переваг PostgreSQL. Система підтримує користувацькі типи даних, функції, оператори й методи індексування, а також розширення. Серед найвідоміших розширень — PostGIS для геоданих, pg_trgm для нечіткого пошуку та pgvector для зберігання векторних подань і пошуку схожих об'єктів, на якому будується багато сучасних застосунків зі штучним інтелектом.

```sql
-- Створення власного складеного типу даних
CREATE TYPE address AS (
    street      VARCHAR(100),
    city        VARCHAR(50),
    country     VARCHAR(50),
    postal_code VARCHAR(20)
);

-- Сучасний спосіб оголошення первинних ключів:
-- IDENTITY замість застарілого SERIAL
CREATE TABLE users (
    id           BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    public_id    UUID NOT NULL DEFAULT uuidv7() UNIQUE,   -- PostgreSQL 18+
    username     VARCHAR(50)  NOT NULL UNIQUE,
    email        VARCHAR(100) NOT NULL UNIQUE,
    home_address address,
    work_address address,
    created_at   TIMESTAMPTZ NOT NULL DEFAULT now()
);

-- Робота з користувацьким типом
INSERT INTO users (username, email, home_address)
VALUES ('ivan_p', 'ivan@example.com',
        ROW('вул. Лесі Українки, 1', 'Луцьк', 'Україна', '43000'));

SELECT username, (home_address).city FROM users;
```

У цьому прикладі варто звернути увагу на три сучасні практики. Замість `SERIAL` використовується стандартна конструкція SQL `GENERATED ALWAYS AS IDENTITY`: вона не створює «прихованої» послідовності з окремими правами доступу й не дозволяє випадково вставити власне значення ідентифікатора. Для часових міток використовується `TIMESTAMPTZ` (з часовим поясом), а не `TIMESTAMP` — інакше дані користувачів з різних часових поясів неминуче переплутаються. А для ідентифікатора, який бачать клієнти API, використано **UUIDv7**: на відміну від випадкового UUIDv4, він упорядкований за часом створення, тому вставки не «розкидаються» по індексу й працюють швидше, а сам ідентифікатор не розкриває, скільки записів у таблиці.

### JSON та NoSQL можливості в PostgreSQL

PostgreSQL пропонує гібридний підхід: поєднує реляційну модель з гнучкістю документних систем через вбудовані типи `JSON` та `JSONB`.

**JSONB (JSON Binary)** зберігає документ у розібраному бінарному форматі. Запис трохи повільніший, ніж у текстовий `JSON`, зате пошук значно швидший, і, головне, JSONB можна індексувати. Це дозволяє зберігати в структурованій базі даних атрибути змінної структури — характеристики товарів різних категорій, налаштування користувача, метадані — не створюючи для кожного атрибута окремий стовпець.

```sql
-- Таблиця з JSONB-полями
CREATE TABLE products (
    id             BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    name           VARCHAR(100) NOT NULL,
    price          NUMERIC(10, 2) NOT NULL,
    specifications JSONB NOT NULL DEFAULT '{}',
    metadata       JSONB NOT NULL DEFAULT '{}'
);

-- Вставка даних
INSERT INTO products (name, price, specifications, metadata) VALUES
('Laptop Pro', 42999.00,
 '{"cpu": "Intel i7", "ram": 16, "storage": "512GB SSD", "weight": 1.4}',
 '{"tags": ["electronics", "computer"], "rating": 4.5}');

-- Оператор -> повертає JSONB, оператор ->> повертає текст
SELECT name, specifications->>'cpu' AS processor
FROM products
WHERE specifications @> '{"ram": 16}';

-- Приведення типу застосовується до текстового значення,
-- тому вираз треба брати в дужки
SELECT name
FROM products
WHERE (specifications->>'weight')::numeric < 1.5;

-- GIN-індекс на весь документ пришвидшує оператор @> (містить)
CREATE INDEX idx_products_specs ON products USING GIN (specifications);

-- B-Tree індекс на вираз — для частих запитів за конкретним ключем
CREATE INDEX idx_products_cpu ON products ((specifications->>'cpu'));
```

Зверніть увагу на різницю між операторами: `->` повертає фрагмент як JSONB, а `->>` — як текст. Приведення `::numeric` має вищий пріоритет, ніж оператор доступу, тому запис `specifications->'weight'::numeric` є помилковим — дужки обов'язкові.

JSONB — потужний інструмент, але не заміна нормалізованій схемі. Правило просте: якщо за полем часто фільтрують, сортують, з'єднують таблиці або на нього посилаються інші записи, — це повноцінний стовпець. У JSONB доречно класти атрибути, структура яких справді відрізняється від запису до запису.

### Продуктивність та оптимізація

**Система індексування** PostgreSQL підтримує кілька типів індексів для різних сценаріїв:

| Тип | Для чого | Приклад |
|-----|----------|---------|
| B-Tree | Рівність, діапазони, сортування — тип за замовчуванням | `email`, `created_at` |
| GIN | Пошук «всередині» значення: JSONB, масиви, повнотекстовий пошук | `specifications @> ...` |
| GiST | Геометрія, діапазони, нечіткий пошук | координати, періоди бронювання |
| BRIN | Дуже великі таблиці, де значення корелюють з фізичним порядком | журнал подій за часом |
| Hash | Лише рівність; на практиці майже завжди достатньо B-Tree | — |

```sql
-- Складений індекс: порядок стовпців має значення
CREATE INDEX idx_orders_user_date ON orders (user_id, created_at DESC);

-- Частковий індекс: індексуємо лише активних користувачів
CREATE INDEX idx_active_users ON users (email) WHERE is_active = true;

-- Повнотекстовий пошук (конфігурація 'simple' працює з будь-якою мовою)
CREATE INDEX idx_products_search
    ON products USING GIN (to_tsvector('simple', name));

-- Аналіз плану виконання запиту
EXPLAIN ANALYZE
SELECT u.username, p.name
FROM users u
JOIN orders o   ON u.id = o.user_id
JOIN products p ON o.product_id = p.id
WHERE u.created_at > '2026-01-01';
```

Кілька практичних зауважень. Обмеження `UNIQUE` уже створює B-Tree індекс, тож додатковий індекс на той самий стовпець (як і Hash-індекс поруч з ним) лише сповільнює запис. Кожен індекс пришвидшує читання, але уповільнює вставку й оновлення, тому створюють їх під реальні запити, а не «про всяк випадок». Команда `EXPLAIN ANALYZE` — головний інструмент діагностики: у її виводі шукайте послідовне сканування (`Seq Scan`) великих таблиць і розбіжність між очікуваною та фактичною кількістю рядків.

**Секціонування (партиціонування) таблиць** дозволяє розділити дуже велику таблицю на менші частини за значенням ключа. Запити, що стосуються одного періоду, читають лише потрібну секцію, а старі дані можна видаляти цілими секціями миттєво.

```sql
-- Секціонована таблиця за датою
CREATE TABLE sales (
    id         BIGINT GENERATED ALWAYS AS IDENTITY,
    product_id BIGINT NOT NULL,
    sale_date  DATE NOT NULL,
    amount     NUMERIC(10, 2) NOT NULL,
    PRIMARY KEY (id, sale_date)
) PARTITION BY RANGE (sale_date);

-- Секції для окремих періодів
CREATE TABLE sales_2026_q1 PARTITION OF sales
    FOR VALUES FROM ('2026-01-01') TO ('2026-04-01');

CREATE TABLE sales_2026_q2 PARTITION OF sales
    FOR VALUES FROM ('2026-04-01') TO ('2026-07-01');
```

Секціонування має сенс для таблиць із десятками мільйонів рядків; для звичайних таблиць воно лише ускладнює схему. PostgreSQL також надає вбудовані засоби моніторингу — представлення `pg_stat_activity`, `pg_stat_user_tables`, розширення `pg_stat_statements` для пошуку найдорожчих запитів — до яких ми повернемося в розділі про пули з'єднань.

## Sequelize ORM: об'єктно-реляційне відображення

### Концептуальні основи Sequelize

Писати SQL вручну для кожної операції API можна, але швидко з'являються проблеми: повторюваний код, ризик SQL-ін'єкцій при неакуратній конкатенації рядків, ручне перетворення рядків таблиці на об'єкти й назад. Цю проблему називають **невідповідністю парадигм (impedance mismatch)**: програма мислить об'єктами з вкладеними колекціями, а база даних — плоскими таблицями.

**ORM** автоматично перетворює об'єкти мови програмування на SQL-запити й навпаки. Розробник описує модель (клас, відображений на таблицю) і працює з нею методами на кшталт `User.findAll()`, а ORM генерує параметризований SQL, що захищає від ін'єкцій.

```mermaid
graph LR
    A[Об'єкт JavaScript] --> B[Sequelize ORM]
    B --> C[SQL-запит]
    C --> D[(PostgreSQL)]
    D --> C
    C --> B
    B --> A

    E[Визначення моделей] --> B
    F[Асоціації] --> B
    G[Валідація] --> B
```

**Sequelize** — один із найпоширеніших ORM для Node.js, що підтримує PostgreSQL, MySQL, MariaDB, SQLite та MS SQL Server. Стабільною гілкою є **Sequelize 6**. Гілка 7 (пакет `@sequelize/core`) з переписаним на TypeScript ядром уже кілька років розвивається у статусі альфа-версії, тому для навчальних і робочих проєктів використовуємо v6.

> **Важливо щодо безпеки.** У березні 2026 року в Sequelize 6 виправлено вразливість CVE-2026-30951: SQL-ін'єкцію через ключі JSON-об'єкта в умовах пошуку за JSON/JSONB-полями. Вразливі всі версії до 6.37.7 включно, тож використовуйте **6.37.8 або новішу**. Загальне правило, яке випливає з цього випадку: ніколи не передавайте в умову `where` об'єкт, отриманий від клієнта без перевірки, — лише значення, перевірені схемою валідації.

```javascript
// src/db/sequelize.js
import { Sequelize } from 'sequelize';

// Підключення до PostgreSQL (драйвер pg встановлюється окремо: npm i sequelize pg)
export const sequelize = new Sequelize(process.env.DATABASE_URL, {
    dialect: 'postgres',
    logging: process.env.NODE_ENV === 'development' ? console.log : false,
    pool: {
        max: 10,        // Максимум з'єднань у пулі
        min: 0,         // Мінімум з'єднань
        acquire: 30000, // Скільки чекати на вільне з'єднання (мс)
        idle: 10000     // Через скільки закривати невикористане з'єднання (мс)
    }
});

// Перевірка з'єднання під час запуску застосунку
export async function connectDatabase() {
    try {
        await sequelize.authenticate();
        console.log('Підключення до бази даних встановлено');
    } catch (error) {
        console.error('Не вдалося підключитися до бази даних:', error.message);
        process.exit(1);
    }
}
```

Рядок підключення (`postgres://user:password@host:5432/dbname`) береться зі змінної середовища — як ви пам'ятаєте з лекції 2, Node.js уміє завантажувати файл `.env` вбудованою опцією `--env-file`. Облікові дані бази даних ніколи не записуються в код.

### Моделі даних та їх визначення

**Модель** у Sequelize представляє таблицю й визначає структуру даних, валідацію, методи та поведінку сутності. Модель оголошується як клас, що наслідує `Model`, і ініціалізується описом атрибутів.

```javascript
// src/models/user.js
import { DataTypes, Model } from 'sequelize';
import { sequelize } from '../db/sequelize.js';

// Літери будь-якої абетки (зокрема українські), пробіл, апостроф, дефіс
const NAME_PATTERN = /^[\p{L}\s'’-]+$/u;

export class User extends Model {
    // Метод екземпляра
    getFullName() {
        return `${this.firstName} ${this.lastName}`;
    }

    isAdmin() {
        return this.role === 'admin';
    }

    // Статичний метод моделі
    static findByEmail(email) {
        return this.findOne({ where: { email: email.toLowerCase() } });
    }

    // Приховуємо службові поля при серіалізації у JSON
    toJSON() {
        const { passwordHash, deletedAt, ...safe } = this.get();
        return safe;
    }
}

User.init({
    id: {
        type: DataTypes.UUID,
        defaultValue: DataTypes.UUIDV4,
        primaryKey: true
    },
    username: {
        type: DataTypes.STRING(50),
        allowNull: false,
        unique: true,
        validate: {
            len: [3, 50],
            is: /^[a-z0-9_]+$/i       // Латинські літери, цифри, підкреслення
        }
    },
    email: {
        type: DataTypes.STRING(100),
        allowNull: false,
        unique: true,
        set(value) {
            // Нормалізація перед збереженням
            this.setDataValue('email', value.trim().toLowerCase());
        },
        validate: {
            isEmail: true
        }
    },
    passwordHash: {
        type: DataTypes.STRING,
        allowNull: false             // Хеш пароля — див. лекцію 5
    },
    firstName: {
        type: DataTypes.STRING(50),
        allowNull: false,
        validate: {
            len: [2, 50],
            is: NAME_PATTERN         // isAlpha відкинув би кирилицю!
        }
    },
    lastName: {
        type: DataTypes.STRING(50),
        allowNull: false,
        validate: {
            len: [2, 50],
            is: NAME_PATTERN
        }
    },
    dateOfBirth: {
        type: DataTypes.DATEONLY,
        validate: {
            isDate: true,
            isNotInFuture(value) {
                if (value && new Date(value) > new Date()) {
                    throw new Error('Дата народження не може бути в майбутньому');
                }
            }
        }
    },
    role: {
        type: DataTypes.ENUM('user', 'moderator', 'admin'),
        allowNull: false,
        defaultValue: 'user'
    },
    isActive: {
        type: DataTypes.BOOLEAN,
        allowNull: false,
        defaultValue: true
    },
    lastLoginAt: {
        type: DataTypes.DATE
    },
    preferences: {
        type: DataTypes.JSONB,       // Специфічний для PostgreSQL тип
        allowNull: false,
        defaultValue: {}
    }
}, {
    sequelize,
    modelName: 'User',
    tableName: 'users',
    timestamps: true,        // Автоматичні createdAt та updatedAt
    paranoid: true,          // М'яке видалення: destroy() лише заповнює deletedAt
    underscored: true,       // Імена стовпців у snake_case: first_name, created_at
    version: true,           // Поле version для оптимістичного блокування
    indexes: [
        { fields: ['role'] },
        { using: 'gin', fields: ['preferences'] }
    ]
});
```

Розберімо найважливіші рішення в цій моделі.

**Валідація імен.** Вбудований валідатор `isAlpha` за замовчуванням перевіряє лише англійську абетку, і ім'я «Олександр» він відкине. Тому для імен використано регулярний вираз з класом `\p{L}` (будь-яка літера) та прапорцем `u` — той самий, що й у схемі валідації API з лекції 3. Валідація моделі не замінює валідацію на рівні API: вона є останнім рубежем перед записом у базу.

**Хеш замість пароля.** Модель зберігає не пароль, а його хеш у полі `passwordHash`, а метод `toJSON()` гарантує, що це поле ніколи не потрапить у відповідь API, навіть якщо розробник забуде його прибрати вручну.

**Опції моделі.** `paranoid: true` вмикає м'яке видалення, про яке йшлося в лекції 3: `user.destroy()` не видаляє рядок, а заповнює `deletedAt`, і всі запити автоматично такі рядки пропускають. `underscored: true` узгоджує стиль: у JavaScript — `camelCase`, у базі — `snake_case`. `version: true` додає поле `version`, яке збільшується з кожним оновленням: якщо два запити одночасно редагують запис, другий отримає помилку `OptimisticLockError`. Це саме те поле, з якого в лекції 3 ми будували ETag для захисту від втрачених оновлень.

Про ідентифікатори: тут використано UUIDv4, який генерує сам Sequelize. Якщо ви працюєте з PostgreSQL 18, можна доручити генерацію базі даних і використати впорядкований UUIDv7 (у міграції — значення за замовчуванням `uuidv7()`).

### Міграції: версіонування схеми бази даних

Модель описує, якою схема **має бути**, але не змінює наявну базу даних. Метод `sequelize.sync()`, який створює таблиці за моделями, зручний для швидких експериментів, але в реальному проєкті неприпустимий: він не вміє безпечно змінювати таблиці з даними, а його варіант `sync({ alter: true })` здатен видалити стовпець разом з даними.

**Міграції** розв'язують цю проблему. Кожна міграція — окремий файл з інструкціями для застосування змін (`up`) і їх відкату (`down`). Міграції зберігаються в системі контролю версій разом з кодом, виконуються послідовно, а спеціальна службова таблиця в базі пам'ятає, які з них уже застосовано. Завдяки цьому схема бази даних на комп'ютері кожного розробника, на тестовому сервері й у робочому середовищі гарантовано однакова. По суті, міграції — це Git для схеми бази даних.

Міграції Sequelize створюються й виконуються за допомогою пакета `sequelize-cli`:

```javascript
// npx sequelize-cli migration:generate --name create-users-table
// npx sequelize-cli db:migrate          — застосувати всі нові міграції
// npx sequelize-cli db:migrate:undo     — відкотити останню

// migrations/20260915120000-create-users-table.cjs
// Файли міграцій sequelize-cli пишуться у форматі CommonJS;
// у проєкті з "type": "module" вони мають розширення .cjs
'use strict';

module.exports = {
    async up(queryInterface, Sequelize) {
        await queryInterface.createTable('users', {
            id: {
                type: Sequelize.UUID,
                primaryKey: true,
                allowNull: false,
                defaultValue: Sequelize.literal('gen_random_uuid()')
            },
            username: {
                type: Sequelize.STRING(50),
                allowNull: false,
                unique: true
            },
            email: {
                type: Sequelize.STRING(100),
                allowNull: false,
                unique: true
            },
            password_hash: {
                type: Sequelize.STRING,
                allowNull: false
            },
            first_name: {
                type: Sequelize.STRING(50),
                allowNull: false
            },
            last_name: {
                type: Sequelize.STRING(50),
                allowNull: false
            },
            date_of_birth: {
                type: Sequelize.DATEONLY
            },
            role: {
                type: Sequelize.ENUM('user', 'moderator', 'admin'),
                allowNull: false,
                defaultValue: 'user'
            },
            is_active: {
                type: Sequelize.BOOLEAN,
                allowNull: false,
                defaultValue: true
            },
            last_login_at: {
                type: Sequelize.DATE
            },
            preferences: {
                type: Sequelize.JSONB,
                allowNull: false,
                defaultValue: {}
            },
            version: {
                type: Sequelize.INTEGER,
                allowNull: false,
                defaultValue: 0
            },
            created_at: {
                type: Sequelize.DATE,
                allowNull: false,
                defaultValue: Sequelize.fn('now')
            },
            updated_at: {
                type: Sequelize.DATE,
                allowNull: false,
                defaultValue: Sequelize.fn('now')
            },
            deleted_at: {
                type: Sequelize.DATE
            }
        });

        await queryInterface.addIndex('users', ['role']);
        await queryInterface.addIndex('users', ['preferences'], {
            using: 'gin',
            name: 'users_preferences_gin_index'
        });
    },

    async down(queryInterface) {
        await queryInterface.dropTable('users');
        // ENUM-тип у PostgreSQL створюється окремо й не видаляється разом із таблицею
        await queryInterface.sequelize.query('DROP TYPE IF EXISTS "enum_users_role";');
    }
};
```

Кожна наступна зміна схеми — нова міграція. Чинні міграції після того, як їх застосовано на спільних середовищах, ніколи не редагують: якщо в міграції помилка, пишуть нову, яка її виправляє.

```javascript
// migrations/20260920140000-add-phone-to-users.cjs
'use strict';

module.exports = {
    async up(queryInterface, Sequelize) {
        await queryInterface.addColumn('users', 'phone_number', {
            type: Sequelize.STRING(20),
            allowNull: true           // Новий стовпець у таблиці з даними — лише nullable
        });

        await queryInterface.addIndex('users', ['phone_number']);
    },

    async down(queryInterface) {
        await queryInterface.removeColumn('users', 'phone_number');
    }
};
```

Зверніть увагу: нове поле в таблиці, де вже є дані, додається як необов'язкове. Обов'язковість вмикають окремою міграцією після того, як для наявних рядків заповнено значення. Це частина ширшої практики «розширити, перенести, звузити», яка дозволяє змінювати схему без зупинки застосунку. Валідатори моделі (як-от формат телефону) описуються в моделі, а не в міграції: міграція змінює лише структуру бази.

### Асоціації між моделями

**Асоціації** описують зв'язки між моделями. На їх основі Sequelize знає, які зовнішні ключі використовувати в з'єднаннях, і додає до екземплярів зручні методи (`user.getOrders()`, `order.addProduct()`).

Розглянемо модель інтернет-магазину: категорії містять товари, користувачі роблять замовлення, а кожне замовлення містить кілька товарів у певній кількості.

```javascript
// src/models/shop.js
import { DataTypes, Model } from 'sequelize';
import { sequelize } from '../db/sequelize.js';
import { User } from './user.js';

export class Category extends Model {}
Category.init({
    id: { type: DataTypes.UUID, defaultValue: DataTypes.UUIDV4, primaryKey: true },
    name: { type: DataTypes.STRING(100), allowNull: false, unique: true },
    description: DataTypes.TEXT,
    isActive: { type: DataTypes.BOOLEAN, allowNull: false, defaultValue: true }
}, { sequelize, modelName: 'Category', underscored: true });

export class Product extends Model {}
Product.init({
    id: { type: DataTypes.UUID, defaultValue: DataTypes.UUIDV4, primaryKey: true },
    name: { type: DataTypes.STRING(200), allowNull: false },
    description: DataTypes.TEXT,
    price: {
        type: DataTypes.DECIMAL(10, 2),  // Гроші — лише DECIMAL, ніколи FLOAT
        allowNull: false,
        validate: { min: 0 }
    },
    sku: { type: DataTypes.STRING(50), unique: true },
    stockQuantity: {
        type: DataTypes.INTEGER,
        allowNull: false,
        defaultValue: 0,
        validate: { min: 0 }
    }
}, { sequelize, modelName: 'Product', underscored: true });

export class Order extends Model {}
Order.init({
    id: { type: DataTypes.UUID, defaultValue: DataTypes.UUIDV4, primaryKey: true },
    orderNumber: { type: DataTypes.STRING(20), unique: true, allowNull: false },
    totalAmount: { type: DataTypes.DECIMAL(10, 2), allowNull: false },
    status: {
        type: DataTypes.ENUM('pending', 'confirmed', 'shipped', 'delivered', 'cancelled'),
        allowNull: false,
        defaultValue: 'pending'
    }
}, { sequelize, modelName: 'Order', underscored: true });

// Проміжна модель зв'язку «багато до багатьох» з власними атрибутами
export class OrderItem extends Model {}
OrderItem.init({
    quantity: { type: DataTypes.INTEGER, allowNull: false, validate: { min: 1 } },
    unitPrice: { type: DataTypes.DECIMAL(10, 2), allowNull: false },
    totalPrice: { type: DataTypes.DECIMAL(10, 2), allowNull: false }
}, { sequelize, modelName: 'OrderItem', underscored: true });

// Один до багатьох: категорія має багато товарів
Category.hasMany(Product, { foreignKey: 'categoryId', as: 'products' });
Product.belongsTo(Category, { foreignKey: 'categoryId', as: 'category' });

// Один до багатьох: користувач має багато замовлень
User.hasMany(Order, { foreignKey: 'userId', as: 'orders' });
Order.belongsTo(User, { foreignKey: 'userId', as: 'user' });

// Багато до багатьох: замовлення містить багато товарів через OrderItem
Order.belongsToMany(Product, { through: OrderItem, foreignKey: 'orderId', as: 'products' });
Product.belongsToMany(Order, { through: OrderItem, foreignKey: 'productId', as: 'orders' });
```

Асоціації оголошуються парами (`hasMany` + `belongsTo`), щоб зв'язок можна було обходити в обидва боки. Псевдонім `as` задає ім'я, під яким пов'язані дані з'являться в об'єкті та за яким їх запитують в `include`. Грошові суми завжди зберігаються в `DECIMAL`: числа з рухомою комою не можуть точно представити 0,1, і похибки округлення в сумах замовлень неприпустимі. Зверніть увагу, що Sequelize повертає значення `DECIMAL` як рядки — саме щоб не втратити точність.

### Складні запити та транзакції

Опція `include` дозволяє одним запитом отримати пов'язані дані — Sequelize згенерує SQL з відповідними з'єднаннями.

```javascript
import { Op } from 'sequelize';

// Користувач із підтвердженими та відправленими замовленнями і товарами в них
export function getUserWithOrders(userId) {
    return User.findByPk(userId, {
        include: [
            {
                model: Order,
                as: 'orders',
                where: { status: { [Op.in]: ['confirmed', 'shipped'] } },
                required: false,         // LEFT JOIN: користувач без замовлень теж повернеться
                include: [
                    {
                        model: Product,
                        as: 'products',
                        through: { attributes: ['quantity', 'unitPrice'] }
                    }
                ]
            }
        ],
        order: [[{ model: Order, as: 'orders' }, 'createdAt', 'DESC']]
    });
}
```

**Транзакції** гарантують, що кілька пов'язаних змін або виконаються всі, або жодна. Класичний приклад — оформлення замовлення: створити замовлення, додати позиції й зменшити залишки на складі. Якщо товару не вистачить на третій позиції, перші дві зміни мають бути скасовані.

Sequelize пропонує два стилі транзакцій. У **керованих транзакціях** ви передаєте функцію: якщо вона завершилася успішно, транзакція фіксується, якщо кинула виняток — автоматично відкочується. Це безпечніший стиль, бо неможливо забути `commit` чи `rollback`.

```javascript
import { sequelize } from '../db/sequelize.js';

export async function createOrderWithItems(userId, items) {
    return sequelize.transaction(async (transaction) => {
        const order = await Order.create({
            userId,
            orderNumber: generateOrderNumber(),
            totalAmount: 0,
            status: 'pending'
        }, { transaction });

        let total = 0;

        for (const item of items) {
            // Атомарне зменшення залишку з перевіркою в одному SQL-запиті:
            // UPDATE ... SET stock_quantity = stock_quantity - :q
            // WHERE id = :id AND stock_quantity >= :q
            // Для PostgreSQL метод повертає [змінені рядки, кількість змінених рядків]
            const [, affectedCount] = await Product.decrement('stockQuantity', {
                by: item.quantity,
                where: {
                    id: item.productId,
                    stockQuantity: { [Op.gte]: item.quantity }
                },
                transaction
            });

            if (affectedCount === 0) {
                // Виняток → автоматичний відкат усієї транзакції
                throw new ConflictError(`Недостатньо товару ${item.productId} на складі`);
            }

            const product = await Product.findByPk(item.productId, { transaction });
            const lineTotal = Number(product.price) * item.quantity;
            total += lineTotal;

            await OrderItem.create({
                orderId: order.id,
                productId: item.productId,
                quantity: item.quantity,
                unitPrice: product.price,
                totalPrice: lineTotal.toFixed(2)
            }, { transaction });
        }

        await order.update({ totalAmount: total.toFixed(2) }, { transaction });
        return order;
    });
}
```

Два моменти заслуговують на увагу. По-перше, кожен запит усередині транзакції отримує параметр `{ transaction }`: запит без нього виконається поза транзакцією на іншому з'єднанні з пулу. По-друге, перевірка залишку й зменшення виконуються одним запитом із умовою `stockQuantity >= quantity`. Варіант «прочитати залишок, перевірити в JavaScript, потім записати» має стан гонитви: два одночасні замовлення можуть обидва прочитати «залишилося 1» і обидва його списати. Ціну ми беремо з бази, а не з запиту клієнта — клієнт не повинен визначати, скільки коштує товар.

### Проблема N+1

Найпоширеніша проблема продуктивності застосунків з ORM має власну назву — **проблема N+1 запитів**. Вона виникає, коли код отримує список з N записів одним запитом, а потім для кожного з них окремо довантажує пов'язані дані. Замість одного-двох запитів до бази йде N+1, і на списку зі 100 замовлень сторінка раптом робить 101 звернення до бази.

```javascript
// ❌ N+1: 1 запит на замовлення + 50 запитів на користувачів
const orders = await Order.findAll({ limit: 50 });
for (const order of orders) {
    const user = await order.getUser();          // Окремий SELECT на кожній ітерації!
    console.log(order.orderNumber, user.email);
}

// ✅ Один запит із з'єднанням
const ordersWithUsers = await Order.findAll({
    limit: 50,
    include: [{ model: User, as: 'user', attributes: ['id', 'email'] }]
});

// ✅ Для колекцій (hasMany) — окремий другий запит замість
// «роздування» результату з'єднанням: 2 запити замість N+1
const users = await User.findAll({
    limit: 20,
    include: [{ model: Order, as: 'orders', separate: true, order: [['createdAt', 'DESC']] }]
});
```

Підступність N+1 у тому, що на комп'ютері розробника з десятком тестових записів проблему не видно. Тому під час розробки вмикайте журналювання SQL-запитів (`logging: console.log`) і звертайте увагу на однакові запити, що повторюються в циклі. Опція `attributes` в прикладі — ще одна корисна звичка: не вибирайте всі стовпці, якщо потрібні два.

### Сучасні альтернативи: Prisma, Drizzle і конструктори запитів

Sequelize — зрілий інструмент, але не єдиний. За останні роки в екосистемі Node.js і TypeScript помітно зросла популярність двох інших ORM, і з ними варто бути знайомим.

**Prisma** використовує підхід «спочатку схема»: модель даних описується в окремому файлі власною мовою опису схем, з якого генерується типізований клієнт і міграції. У версії 7 (листопад 2025) Prisma відмовилася від окремого рушія на Rust на користь реалізації на TypeScript і WebAssembly, що суттєво зменшило розмір пакета й покращило роботу в безсерверних середовищах.

```prisma
// schema.prisma
model User {
  id        String   @id @default(uuid())
  email     String   @unique
  firstName String
  orders    Order[]
  createdAt DateTime @default(now())
}

model Order {
  id     String @id @default(uuid())
  user   User   @relation(fields: [userId], references: [id])
  userId String
}
```

```javascript
// Запит із пов'язаними даними
const users = await prisma.user.findMany({
    where: { email: { endsWith: '@example.com' } },
    include: { orders: true }
});
```

**Drizzle** використовує підхід «спочатку код»: схема описується звичайним кодом TypeScript/JavaScript, а API запитів навмисно повторює структуру SQL. Його девіз можна перефразувати як «знаєш SQL — знаєш Drizzle». Він дуже легкий і не має кроку генерації коду; станом на вересень 2026 року стабільна гілка — 0.45, версія 1.0 у бета-тестуванні.

```javascript
import { pgTable, uuid, varchar, timestamp } from 'drizzle-orm/pg-core';
import { eq } from 'drizzle-orm';

export const users = pgTable('users', {
    id: uuid('id').primaryKey().defaultRandom(),
    email: varchar('email', { length: 100 }).notNull().unique(),
    createdAt: timestamp('created_at', { withTimezone: true }).defaultNow()
});

const [user] = await db.select().from(users).where(eq(users.email, 'ivan@example.com'));
```

**Конструктори запитів (query builders)**, як Knex.js, займають проміжну позицію між «чистим» SQL і ORM: вони дають програмний інтерфейс для побудови запитів, параметризацію й міграції, але не відображають рядки на класи моделей.

| Інструмент | Підхід | Сильні сторони | Коли обирати |
|------------|--------|----------------|--------------|
| Sequelize 6 | Класичний ORM, моделі-класи | Зрілість, багато СКБД, м'яке видалення, хуки | JavaScript-проєкти, наявна кодова база |
| Prisma 7 | Схема → згенерований клієнт | Найкраща типізація й зручність, міграції | Проєкти на TypeScript, команди без глибокого знання SQL |
| Drizzle | Схема в коді, API як SQL | Легкість, прозорість запитів | Команди, що добре знають SQL, безсерверні середовища |
| Knex.js | Конструктор запитів | Повний контроль над SQL | Складні запити, звітність |
| Драйвер `pg` | Чистий SQL | Максимальна продуктивність і контроль | Критичні ділянки, аналітика |

Принципи, які ви вивчаєте на прикладі Sequelize, — моделі, міграції, асоціації, транзакції, проблема N+1 — однакові для всіх цих інструментів, змінюється лише синтаксис.

## MongoDB та Mongoose: документо-орієнтований підхід

### Філософія документо-орієнтованого підходу

**MongoDB** пропонує інший спосіб організації даних. Замість таблиць з фіксованими схемами вона використовує **колекції документів**, де кожен документ — самодостатня одиниця інформації в JSON-подібному форматі BSON, що доповнює JSON типами дат, бінарних даних, точних десяткових чисел та ідентифікаторів `ObjectId`.

Ця модель природно відображає структуру даних у багатьох застосунках, де інформація ієрархічна: профіль користувача з адресами й налаштуваннями, стаття з тегами, замовлення з позиціями. Пов'язані дані, які завжди читаються разом, зберігаються в одному документі й отримуються одним зверненням без з'єднань.

```mermaid
graph TB
    subgraph "Колекція users у MongoDB"
        D1[Документ 1<br/>профіль + 1 адреса]
        D2[Документ 2<br/>профіль + 3 адреси<br/>+ соцмережі]
        D3[Документ 3<br/>вкладені налаштування]
    end

    subgraph "Переваги"
        F1[Гнучка схема]
        F2[Горизонтальне масштабування]
        F3[Структура як в об'єктах застосунку]
    end

    D1 --> F1
    D2 --> F2
    D3 --> F3
```

Для роботи з MongoDB з Node.js використовують або офіційний драйвер `mongodb`, або **Mongoose** — бібліотеку об'єктно-документного відображення (ODM), що додає схеми, валідацію, хуки та зручний API моделей. Актуальна основна гілка — **Mongoose 9**. Вона перейшла на драйвер MongoDB 7 і відмовилася від застарілих стилів коду, тож багато прикладів з інтернету, написаних для попередніх версій, більше не працюють. Ми звертатимемо на це увагу далі.

```javascript
// src/db/mongoose.js
import mongoose from 'mongoose';

export async function connectMongo() {
    // Опції useNewUrlParser і useUnifiedTopology давно вилучено —
    // у сучасних версіях вони не потрібні й не підтримуються
    await mongoose.connect(process.env.MONGODB_URI, {
        maxPoolSize: 10,                  // Максимум з'єднань у пулі
        serverSelectionTimeoutMS: 5000    // Скільки чекати на доступний сервер
    });
    console.log('Підключення до MongoDB встановлено');
}

// Обробка подій з'єднання
mongoose.connection.on('error', (err) => {
    console.error('Помилка з\'єднання з MongoDB:', err.message);
});

mongoose.connection.on('disconnected', () => {
    console.warn('З\'єднання з MongoDB втрачено');
});
```

### Схеми та моделі в Mongoose

Гнучкість схеми MongoDB — це можливість, а не рекомендація зберігати будь-що. Якщо в колекції `users` частина документів має поле `email`, частина — `mail`, а частина — `e_mail`, код, що з ними працює, швидко перетворюється на набір перевірок. **Mongoose** повертає контроль: схема визначає структуру документів, типи, валідацію, значення за замовчуванням та індекси.

```javascript
// src/models/user.mongo.js
import mongoose from 'mongoose';
const { Schema } = mongoose;

const NAME_PATTERN = /^[\p{L}\s'’-]+$/u;

// Схема вкладеного об'єкта адреси
const addressSchema = new Schema({
    street: {
        type: String,
        required: [true, 'Вулиця є обов\'язковою'],
        trim: true,
        maxlength: [100, 'Назва вулиці не може перевищувати 100 символів']
    },
    city: {
        type: String,
        required: [true, 'Місто є обов\'язковим'],
        trim: true,
        maxlength: [50, 'Назва міста не може перевищувати 50 символів']
    },
    country: {
        type: String,
        required: [true, 'Країна є обов\'язковою'],
        trim: true,
        default: 'Україна'
    },
    postalCode: {
        type: String,
        required: [true, 'Поштовий індекс є обов\'язковим'],
        match: [/^\d{5}$/, 'Поштовий індекс України складається з 5 цифр']
    }
}, { _id: false }); // Вкладеним об'єктам власний _id не потрібен

// Основна схема користувача
const userSchema = new Schema({
    username: {
        type: String,
        required: [true, 'Ім\'я користувача є обов\'язковим'],
        unique: true,
        trim: true,
        lowercase: true,
        minlength: [3, 'Мінімум 3 символи'],
        maxlength: [30, 'Максимум 30 символів'],
        match: [/^[a-z0-9_]+$/, 'Лише латинські літери, цифри та підкреслення']
    },
    email: {
        type: String,
        required: [true, 'Email є обов\'язковим'],
        unique: true,
        lowercase: true,
        trim: true,
        // Проста перевірка формату; повну валідацію виконує схема API
        match: [/^[^\s@]+@[^\s@]+\.[^\s@]+$/, 'Некоректний формат email']
    },
    passwordHash: {
        type: String,
        required: true,
        select: false        // Не повертається запитами, якщо не запитано явно
    },
    profile: {
        firstName: {
            type: String,
            required: [true, 'Ім\'я є обов\'язковим'],
            trim: true,
            maxlength: 50,
            match: [NAME_PATTERN, 'Ім\'я може містити лише літери']
        },
        lastName: {
            type: String,
            required: [true, 'Прізвище є обов\'язковим'],
            trim: true,
            maxlength: 50,
            match: [NAME_PATTERN, 'Прізвище може містити лише літери']
        },
        dateOfBirth: {
            type: Date,
            validate: {
                validator: (value) => value < new Date(),
                message: 'Дата народження не може бути в майбутньому'
            }
        },
        avatar: {
            type: String,
            match: [/^https:\/\/.+/i, 'Аватар має бути HTTPS-посиланням']
        }
    },
    addresses: {
        home: addressSchema,
        work: addressSchema
    },
    preferences: {
        theme: {
            type: String,
            enum: ['light', 'dark', 'auto'],
            default: 'auto'
        },
        language: {
            type: String,
            enum: ['uk', 'en'],
            default: 'uk'
        },
        notifications: {
            email: { type: Boolean, default: true },
            push: { type: Boolean, default: true },
            sms: { type: Boolean, default: false }
        }
    },
    role: {
        type: String,
        enum: {
            values: ['user', 'moderator', 'admin'],
            message: 'Роль має бути: user, moderator або admin'
        },
        default: 'user'
    },
    isActive: {
        type: Boolean,
        default: true
    },
    lastLoginAt: Date,
    loginCount: {
        type: Number,
        default: 0,
        min: 0
    },
    tags: [{
        type: String,
        trim: true,
        lowercase: true
    }],
    socialMedia: {
        github: String,
        linkedin: String
    }
}, {
    timestamps: true,       // Автоматичні createdAt та updatedAt
    versionKey: false,      // Не додавати службове поле __v
    collection: 'users'
});

// Індекси. unique: true у полях уже створює унікальні індекси для email і username,
// тому повторно їх не оголошуємо
userSchema.index({ 'profile.lastName': 1, 'profile.firstName': 1 });
userSchema.index({ role: 1, isActive: 1 });
userSchema.index({ tags: 1 });
userSchema.index({ createdAt: -1 });

// Текстовий індекс для пошуку з вагами полів
userSchema.index({
    username: 'text',
    'profile.firstName': 'text',
    'profile.lastName': 'text'
}, {
    weights: {
        username: 10,
        'profile.firstName': 5,
        'profile.lastName': 5
    },
    default_language: 'none' // Без мовних правил: коректно працює з українськими словами
});
```

Важливий нюанс: опція `unique: true` у схемі Mongoose — це не валідатор, а вказівка створити унікальний індекс у MongoDB. Саме індекс, а не перевірка в коді, гарантує, що двох користувачів з однаковим email не з'явиться. Порушення індексу повертається як помилка з кодом `11000`, яку глобальний обробник помилок API має перетворювати на відповідь `409 Conflict`.

### Віртуальні поля та методи

**Віртуальні поля** — обчислювані властивості, які не зберігаються в базі даних, а обчислюються під час читання документа. Методи екземпляра й статичні методи дозволяють тримати логіку, пов'язану з сутністю, поруч з її описом.

```javascript
// Віртуальні поля: не зберігаються в базі, обчислюються при зверненні
userSchema.virtual('fullName').get(function () {
    return `${this.profile.firstName} ${this.profile.lastName}`;
});

userSchema.virtual('age').get(function () {
    if (!this.profile.dateOfBirth) return null;
    const today = new Date();
    const birthDate = this.profile.dateOfBirth;
    let age = today.getFullYear() - birthDate.getFullYear();
    const monthDiff = today.getMonth() - birthDate.getMonth();

    if (monthDiff < 0 || (monthDiff === 0 && today.getDate() < birthDate.getDate())) {
        age--;
    }
    return age;
});

userSchema.virtual('initials').get(function () {
    const first = this.profile.firstName ?? '';
    const last = this.profile.lastName ?? '';
    return `${first.charAt(0)}${last.charAt(0)}`.toUpperCase();
});

// Методи екземпляра
userSchema.methods.getPublicProfile = function () {
    return {
        id: this._id,
        username: this.username,
        fullName: this.fullName,
        initials: this.initials,
        avatar: this.profile.avatar,
        role: this.role,
        memberSince: this.createdAt
    };
};

userSchema.methods.canModerate = function () {
    return ['admin', 'moderator'].includes(this.role);
};

// Статичні методи моделі
userSchema.statics.findByEmail = function (email) {
    return this.findOne({ email: email.toLowerCase() });
};

userSchema.statics.findActiveUsers = function () {
    return this.find({ isActive: true }).sort({ createdAt: -1 });
};

userSchema.statics.searchUsers = function (searchTerm, limit = 10) {
    return this.find(
        { $text: { $search: searchTerm } },
        { score: { $meta: 'textScore' } }
    ).sort({ score: { $meta: 'textScore' } }).limit(limit);
};

// Агрегація: статистика за ролями
userSchema.statics.getUserStats = function () {
    return this.aggregate([
        {
            $group: {
                _id: '$role',
                total: { $sum: 1 },
                active: { $sum: { $cond: ['$isActive', 1, 0] } }
            }
        },
        {
            $project: {
                _id: 0,
                role: '$_id',
                total: 1,
                active: 1,
                inactive: { $subtract: ['$total', '$active'] }
            }
        }
    ]);
};
```

Про віртуальні поля треба пам'ятати дві речі. По-перше, за ними неможливо фільтрувати: запит `User.find({ age: { $gte: 18 } })` нічого не знайде, бо поля `age` у базі немає. Для такого фільтра треба порівнювати саме `profile.dateOfBirth` з обчисленою датою. По-друге, за замовчуванням віртуальні поля не потрапляють у результат `toJSON()`; щоб вони з'явилися у відповіді API, у схемі вмикають опцію `toJSON: { virtuals: true }` або формують відповідь явно, як у методі `getPublicProfile()`.

### Middleware та хуки

**Хуки (middleware)** Mongoose — функції, що виконуються до (`pre`) або після (`post`) певних операцій: збереження, валідації, видалення, запитів. Вони зручні для нормалізації даних, обчислення похідних полів і дій, які мають відбуватися завжди.

**Зміна в Mongoose 9, яку треба знати.** Pre-хуки більше не отримують функцію `next()`. Хук має бути або синхронною функцією, або асинхронною (`async`), або повертати Promise; щоб перервати операцію, у ньому кидають виняток. Код, що викликає `next()` у pre-хуку, у дев'ятій версії не працює. Причина зміни — повноцінні стеки викликів для асинхронних помилок: виклик `next()` розривав ланцюжок async-викликів, і в стеку помилки губилися імена функцій. Тому асинхронним хукам варто давати імена — вони з'являться у стеку при помилці.

```javascript
// Pre-хук: виконується перед збереженням
userSchema.pre('save', async function prepareUserBeforeSave() {
    // Запам'ятовуємо, чи документ новий: після збереження isNew стає false
    this.$locals.wasNew = this.isNew;

    if (this.isNew && !this.profile.avatar) {
        const name = encodeURIComponent(this.fullName);
        this.profile.avatar = `https://ui-avatars.com/api/?name=${name}&size=200`;
    }

    if (this.isModified('tags')) {
        // Прибираємо дублікати тегів
        this.tags = [...new Set(this.tags)];
    }

    // Перервати збереження можна, кинувши виняток:
    // throw new Error('Причина відмови');
});

// Post-хук: виконується після збереження
userSchema.post('save', function afterUserSaved(doc) {
    if (doc.$locals.wasNew) {
        console.log(`Створено користувача: ${doc.email}`);
        // Тут можна поставити в чергу вітальний лист
    }
});

// Хук видалення документа: прибираємо пов'язані дані
userSchema.pre('deleteOne', { document: true, query: false }, async function cleanupUserData() {
    await mongoose.model('Post').deleteMany({ author: this._id });
});
```

Чого в хуках робити **не** варто. Не перевіряйте в `pre('save')` унікальність email запитом до бази: між перевіркою та записом інший запит може встигнути створити такого самого користувача — гарантію дає лише унікальний індекс. Не додавайте «невидимих» фільтрів у хуки запитів (наприклад, автоматичне приховування неактивних користувачів у `pre(/^find/)`): такий код ламає очікування — розробник пише `findById` і не розуміє, чому документ не знаходиться. Явний фільтр у сервісі кращий за неявний. І пам'ятайте, що хуки `save` не спрацьовують для масових операцій на кшталт `updateMany()`.

### Робота з документами та запити

```javascript
export const User = mongoose.model('User', userSchema);

async function demonstrateMongooseQueries(passwordHash) {
    // Створення документа
    const user = await User.create({
        username: 'ivan_p',
        email: 'Ivan.Petrenko@example.com',   // Збережеться в нижньому регістрі
        passwordHash,                          // Отримання хешу — у лекції 5
        profile: {
            firstName: 'Іван',
            lastName: 'Петренко',
            dateOfBirth: new Date('2005-05-15')
        },
        addresses: {
            home: {
                street: 'вул. Лесі Українки, 1',
                city: 'Луцьк',
                postalCode: '43000'
            }
        },
        preferences: { theme: 'dark' },
        tags: ['developer', 'nodejs', 'react']
    });
    console.log('Створено користувача:', user.fullName);

    // Пошук: повнолітні активні розробники.
    // Фільтруємо за збереженим полем dateOfBirth, а не за віртуальним age
    const adultBirthDate = new Date();
    adultBirthDate.setFullYear(adultBirthDate.getFullYear() - 18);

    const developers = await User.find({
        isActive: true,
        'profile.dateOfBirth': { $lte: adultBirthDate },
        tags: { $in: ['developer', 'designer'] }
    })
        .select('username email profile role createdAt')
        .sort({ createdAt: -1 })
        .limit(10)
        .lean();   // Прості об'єкти замість документів Mongoose — швидше для читання

    // Агрегація: кількість користувачів і середній вік за містами
    const usersByCity = await User.aggregate([
        { $match: { isActive: true } },
        {
            $group: {
                _id: '$addresses.home.city',
                count: { $sum: 1 },
                avgAge: {
                    $avg: {
                        $dateDiff: {
                            startDate: '$profile.dateOfBirth',
                            endDate: '$$NOW',
                            unit: 'year'
                        }
                    }
                }
            }
        },
        { $sort: { count: -1 } }
    ]);

    // Атомарне оновлення операторами MongoDB
    const updated = await User.findByIdAndUpdate(
        user._id,
        {
            $set: { lastLoginAt: new Date(), 'preferences.theme': 'light' },
            $inc: { loginCount: 1 },
            $addToSet: { tags: 'javascript' }   // Додасть, лише якщо ще немає
        },
        { returnDocument: 'after', runValidators: true }
    );

    // Текстовий пошук
    const searchResults = await User.searchUsers('Петренко');
    console.log(`Знайдено ${searchResults.length} користувачів`);
}
```

Метод `.lean()` варто використовувати для запитів лише на читання, результат яких одразу йде у відповідь API: він повертає звичайні об'єкти JavaScript без накладних витрат документів Mongoose (але й без віртуальних полів і методів). Оператори оновлення `$set`, `$inc`, `$addToSet` виконуються атомарно на сервері бази даних, тож паралельні запити не затирають зміни один одного — на відміну від схеми «прочитати документ, змінити в JavaScript, зберегти».

### Вбудовування чи посилання: моделювання зв'язків

Головне питання проєктування схеми MongoDB — **вкладати пов'язані дані в документ чи зберігати окремо з посиланням**. Для реляційної бази відповідь завжди одна — нормалізація; для документної вона залежить від того, як дані використовуються.

```mermaid
graph LR
    subgraph "Вбудовування"
        U1[Користувач] --> A1[адреси]
        U1 --> S1[налаштування]
    end

    subgraph "Посилання"
        U2[Користувач] -.->|author: ObjectId| P1[Пост 1]
        U2 -.->|author: ObjectId| P2[Пост 2]
        U2 -.->|author: ObjectId| P3[Пост N...]
    end
```

**Вбудовуйте**, коли пов'язані дані:
- завжди читаються разом з батьківським документом;
- належать лише йому (адреси, налаштування, позиції замовлення);
- мають обмежену кількість (кілька адрес, а не тисячі коментарів).

**Використовуйте посилання**, коли пов'язані дані:
- необмежено зростають (пости користувача, коментарі);
- спільні для багатьох документів (категорія товару);
- часто змінюються незалежно від батьківського документа.

Обмеження на розмір документа (16 МБ) робить необмежено зростаючі вкладені масиви не лише повільними, а й небезпечними. Для посилань Mongoose пропонує `populate()`, що довантажує пов'язані документи додатковим запитом:

```javascript
const postSchema = new Schema({
    title: { type: String, required: true },
    content: String,
    author: { type: Schema.Types.ObjectId, ref: 'User', required: true, index: true }
}, { timestamps: true });

export const Post = mongoose.model('Post', postSchema);

// populate виконує другий запит до колекції users — не з'єднання
const posts = await Post.find()
    .sort({ createdAt: -1 })
    .limit(20)
    .populate('author', 'username profile.firstName profile.lastName');
```

Якщо ваші дані переважно складаються з посилань і вам постійно потрібні `populate()` чи `$lookup`, це сигнал, що дані реляційні за природою і, можливо, для них краще підходить PostgreSQL. MongoDB також підтримує транзакції для кількох документів, але вони доступні лише в наборі реплік і використовуються як виняток: добре спроєктована документна схема робить більшість змін атомарними в межах одного документа.

## Патерни роботи з базами даних

Досі ми зверталися до моделей Sequelize чи Mongoose безпосередньо. У невеликому застосунку це нормально, але з ростом проєкту виникають проблеми: код маршрутів переплітається з деталями ORM, одні й ті самі запити дублюються в різних місцях, а тестування бізнес-логіки вимагає справжньої бази даних. Архітектурні патерни цього розділу розділяють відповідальність на шари, кожен з яких відповідає за своє.

```mermaid
graph TD
    R[Маршрути Express<br/>HTTP: запит → відповідь] --> S[Сервісний шар<br/>бізнес-правила, кешування]
    S --> Rep[Repository<br/>доступ до даних]
    Rep --> ORM[Sequelize / Mongoose]
    ORM --> DB[(База даних)]
    S --> C[(Кеш Redis/Valkey)]
```

### Repository Pattern

**Патерн Repository** приховує деталі доступу до даних за інтерфейсом з методами, що мають зміст у термінах предметної області: `findByEmail`, `findActive`, `create`. Сервісний шар працює з цим інтерфейсом і не знає, яка база даних за ним стоїть. Це дає три вигоди: запити зосереджені в одному місці, заміна ORM чи бази даних зачіпає лише репозиторій, а для тестів сервісу репозиторій легко підмінити реалізацією в пам'яті.

Правильний спосіб підтримати дві бази даних — не один клас, що «вгадує», з яким ORM працює, а **один контракт і дві реалізації**. У JavaScript контракт описують документацією (у TypeScript — інтерфейсом):

```javascript
/**
 * Контракт репозиторію користувачів.
 * Кожна реалізація повертає прості об'єкти { id, email, ... },
 * а не моделі конкретного ORM.
 *
 * @typedef {Object} UserRepository
 * @property {(id: string) => Promise<object|null>} findById
 * @property {(email: string) => Promise<object|null>} findByEmail
 * @property {(email: string) => Promise<object|null>} findCredentialsByEmail — з хешем пароля, лише для входу
 * @property {(opts: {limit:number, offset:number, isActive?:boolean}) => Promise<{rows: object[], count: number}>} findPage
 * @property {(term: string, limit?: number) => Promise<object[]>} search
 * @property {(data: object) => Promise<object>} create
 * @property {(id: string, data: object) => Promise<object|null>} update
 * @property {(id: string) => Promise<boolean>} delete
 * @property {(id: string) => Promise<void>} recordLogin
 */
```

```javascript
// src/repositories/sequelize-user.repository.js
import { Op } from 'sequelize';
import { sequelize } from '../db/sequelize.js';
import { User } from '../models/user.js';

export class SequelizeUserRepository {
    async findById(id) {
        const user = await User.findByPk(id);
        return user?.toJSON() ?? null;
    }

    async findByEmail(email) {
        const user = await User.findOne({ where: { email: email.toLowerCase() } });
        return user?.toJSON() ?? null;
    }

    // toJSON() прибирає хеш пароля, тому для перевірки під час входу
    // (лекція 5) потрібен окремий, явно названий метод
    async findCredentialsByEmail(email) {
        const user = await User.findOne({
            where: { email: email.toLowerCase(), isActive: true },
            attributes: ['id', 'email', 'role', 'passwordHash']
        });
        return user?.get({ plain: true }) ?? null;
    }

    async findPage({ limit = 20, offset = 0, isActive } = {}) {
        const where = isActive === undefined ? {} : { isActive };
        const { rows, count } = await User.findAndCountAll({
            where,
            limit,
            offset,
            order: [['createdAt', 'DESC']]
        });
        return { rows: rows.map(r => r.toJSON()), count };
    }

    async search(term, limit = 10) {
        const pattern = `%${term}%`;
        const users = await User.findAll({
            where: {
                [Op.or]: [
                    { username: { [Op.iLike]: pattern } },
                    { email: { [Op.iLike]: pattern } },
                    { firstName: { [Op.iLike]: pattern } },
                    { lastName: { [Op.iLike]: pattern } }
                ]
            },
            limit
        });
        return users.map(u => u.toJSON());
    }

    async create(data) {
        return (await User.create(data)).toJSON();
    }

    async update(id, data) {
        const user = await User.findByPk(id);
        if (!user) return null;
        await user.update(data);     // Спрацюють валідатори й оптимістичне блокування
        return user.toJSON();
    }

    async delete(id) {
        return (await User.destroy({ where: { id } })) > 0;
    }

    async recordLogin(id) {
        // Атомарне оновлення в одному SQL-запиті
        await User.update(
            { lastLoginAt: new Date(), loginCount: sequelize.literal('login_count + 1') },
            { where: { id } }
        );
    }
}
```

```javascript
// src/repositories/mongoose-user.repository.js
import { User } from '../models/user.mongo.js';

const toPlain = (doc) => doc && { id: doc._id.toString(), ...doc, _id: undefined };

export class MongooseUserRepository {
    async findById(id) {
        return toPlain(await User.findById(id).lean());
    }

    async findByEmail(email) {
        return toPlain(await User.findOne({ email: email.toLowerCase() }).lean());
    }

    async findCredentialsByEmail(email) {
        return toPlain(await User.findOne({ email: email.toLowerCase(), isActive: true })
            .select('+passwordHash')     // Поле з select: false додаємо явно
            .lean());
    }

    async findPage({ limit = 20, offset = 0, isActive } = {}) {
        const filter = isActive === undefined ? {} : { isActive };
        const [rows, count] = await Promise.all([
            User.find(filter).sort({ createdAt: -1 }).skip(offset).limit(limit).lean(),
            User.countDocuments(filter)
        ]);
        return { rows: rows.map(toPlain), count };
    }

    async search(term, limit = 10) {
        const users = await User.find(
            { $text: { $search: term } },
            { score: { $meta: 'textScore' } }
        ).sort({ score: { $meta: 'textScore' } }).limit(limit).lean();
        return users.map(toPlain);
    }

    async create(data) {
        return toPlain((await User.create(data)).toObject());
    }

    async update(id, data) {
        const user = await User.findByIdAndUpdate(id, { $set: data }, {
            returnDocument: 'after',
            runValidators: true
        }).lean();
        return toPlain(user);
    }

    async delete(id) {
        const result = await User.deleteOne({ _id: id });
        return result.deletedCount > 0;
    }

    async recordLogin(id) {
        await User.updateOne({ _id: id }, {
            $set: { lastLoginAt: new Date() },
            $inc: { loginCount: 1 }
        });
    }
}
```

Обидві реалізації мають однакові методи й повертають дані в однаковій формі, тому решта застосунку не помічає різниці. Зверніть увагу, що репозиторій повертає прості об'єкти, а не моделі ORM: якби сервіс отримував екземпляр моделі Sequelize, він міг би викликати його методи (`user.save()`), і абстракція «протекла» б.

### Data Access Layer Pattern

Над репозиторієм розташовується **сервісний шар** — тут живуть бізнес-правила, координація кількох репозиторіїв, кешування та побічні дії на кшталт надсилання листів. Сервіс отримує свої залежності через конструктор (**впровадження залежностей**, dependency injection), тож у тестах їх можна замінити імітаціями.

```javascript
// src/services/user.service.js
import { ConflictError, BusinessRuleError, NotFoundError } from '../errors.js';

export class UserService {
    constructor({ userRepository, emailService, cacheService }) {
        this.users = userRepository;
        this.email = emailService;
        this.cache = cacheService;
    }

    async createUser(userData) {
        await this.validateUserData(userData);

        const user = await this.users.create(userData);

        await this.cache.set(`user:${user.id}`, user, 300);   // 5 хвилин
        // Лист не повинен ламати реєстрацію, якщо поштовий сервіс недоступний
        this.email.sendWelcome(user.email, user.firstName).catch(err =>
            console.error('Не вдалося надіслати вітальний лист:', err.message)
        );

        return user;
    }

    async getUserById(id) {
        const user = await this.cache.getOrLoad(`user:${id}`, 300, () => this.users.findById(id));
        if (!user) throw new NotFoundError(`Користувача з ID ${id} не знайдено`);
        return user;
    }

    async updateUser(id, updateData) {
        const user = await this.users.update(id, updateData);
        if (!user) throw new NotFoundError(`Користувача з ID ${id} не знайдено`);

        // Інвалідація: видаляємо застарілий запис, а не перезаписуємо його
        await this.cache.delete(`user:${id}`);
        return user;
    }

    async searchUsers(term, limit = 10) {
        return this.cache.getOrLoad(`search:${term}:${limit}`, 60, () =>
            this.users.search(term, limit)
        );
    }

    async validateUserData(userData) {
        const existing = await this.users.findByEmail(userData.email);
        if (existing) {
            throw new ConflictError('Користувач з таким email вже існує');
        }

        if (userData.dateOfBirth && this.calculateAge(userData.dateOfBirth) < 13) {
            throw new BusinessRuleError([{
                field: 'dateOfBirth',
                code: 'AGE_BELOW_MINIMUM',
                message: 'Користувач повинен бути старше 13 років'
            }]);
        }
    }

    calculateAge(dateOfBirth) {
        const today = new Date();
        const birthDate = new Date(dateOfBirth);
        let age = today.getFullYear() - birthDate.getFullYear();
        const monthDiff = today.getMonth() - birthDate.getMonth();
        if (monthDiff < 0 || (monthDiff === 0 && today.getDate() < birthDate.getDate())) {
            age--;
        }
        return age;
    }
}
```

Складання застосунку зводиться до того, щоб в одному місці створити потрібні реалізації й передати їх сервісам. Змінити базу даних — означає змінити один рядок:

```javascript
// src/container.js
import { SequelizeUserRepository } from './repositories/sequelize-user.repository.js';
import { UserService } from './services/user.service.js';

export const userService = new UserService({
    userRepository: new SequelizeUserRepository(),   // або new MongooseUserRepository()
    emailService,
    cacheService
});
```

### Connection Pooling та оптимізація

Встановлення з'єднання з базою даних — дорога операція: мережеве рукостискання, шифрування, аутентифікація, виділення процесу на сервері PostgreSQL. Якби кожен HTTP-запит відкривав і закривав власне з'єднання, більшу частину часу сервер витрачав би саме на це. **Пул з'єднань** тримає набір відкритих з'єднань і видає їх запитам у тимчасове користування.

```mermaid
graph LR
    subgraph "Запити до застосунку"
        A1[Запит 1]
        A2[Запит 2]
        A3[Запит N]
    end

    subgraph "Пул з'єднань"
        P1[З'єднання 1]
        P2[З'єднання 2]
        P3[З'єднання 3]
        P4[З'єднання 4 вільне]
        P5[З'єднання 5 вільне]
    end

    subgraph "База даних"
        DB[(PostgreSQL)]
    end

    A1 --> P1
    A2 --> P2
    A3 --> P3

    P1 --> DB
    P2 --> DB
    P3 --> DB
    P4 --> DB
    P5 --> DB
```

Розмір пулу — компроміс. Замалий пул змушує запити чекати на вільне з'єднання. Завеликий — перевантажує базу даних: кожне з'єднання PostgreSQL є окремим процесом з власною пам'яттю, а загальна кількість з'єднань обмежена параметром `max_connections` (за замовчуванням 100). Якщо запущено п'ять екземплярів застосунку з пулом у 20 з'єднань, це вже весь ліміт. Для великої кількості екземплярів між застосунком і базою ставлять зовнішній пул з'єднань, наприклад PgBouncer.

Клас нижче централізує ініціалізацію всіх сховищ і, що не менш важливо, їх коректне закриття при зупинці застосунку:

```javascript
// src/db/database-manager.js
import { Sequelize } from 'sequelize';
import mongoose from 'mongoose';
import { createClient } from 'redis';

export class DatabaseManager {
    connections = new Map();

    async initialize(config) {
        if (config.postgres) {
            const sequelize = new Sequelize(config.postgres.url, {
                dialect: 'postgres',
                logging: false,
                pool: { max: 20, min: 0, acquire: 30000, idle: 10000 },
                dialectOptions: {
                    ssl: config.postgres.ssl ? { require: true } : false,
                    connectTimeout: 20000
                }
            });
            await sequelize.authenticate();
            this.connections.set('postgres', sequelize);
        }

        if (config.mongodb) {
            await mongoose.connect(config.mongodb.uri, {
                maxPoolSize: 20,
                minPoolSize: 5,
                maxIdleTimeMS: 30000,
                serverSelectionTimeoutMS: 5000
            });
            this.connections.set('mongodb', mongoose.connection);
        }

        if (config.redis) {
            // node-redis v5; той самий клієнт працює і з Valkey
            const redis = createClient({
                url: config.redis.url,
                socket: {
                    // Затримка перед повторним підключенням: 100 мс, 200 мс, ... до 3 с
                    reconnectStrategy: (retries) => Math.min(retries * 100, 3000)
                }
            });
            redis.on('error', (err) => console.error('Помилка Redis:', err.message));
            await redis.connect();
            this.connections.set('redis', redis);
        }
    }

    get(type) {
        return this.connections.get(type);
    }

    async closeAll() {
        for (const [type, connection] of this.connections) {
            try {
                if (type === 'redis') {
                    connection.destroy();
                } else {
                    await connection.close();
                }
                console.log(`З'єднання ${type} закрито`);
            } catch (error) {
                console.error(`Помилка закриття з'єднання ${type}:`, error.message);
            }
        }
        this.connections.clear();
    }
}

// Коректна зупинка: завершуємо запити й закриваємо з'єднання
process.on('SIGTERM', async () => {
    await databaseManager.closeAll();
    process.exit(0);
});
```

Моніторинг стану баз даних дозволяє помітити проблеми раніше, ніж користувачі: вичерпання пулу, зростання кількості з'єднань, що «зависли» у транзакції, збої. Найпростіша форма — кінцева точка перевірки працездатності (health check), яку опитують засоби розгортання й моніторингу, і періодичний збір метрик:

```javascript
// src/db/health-monitor.js
export class DatabaseHealthMonitor {
    constructor(databaseManager) {
        this.db = databaseManager;
        this.lastReport = null;
    }

    start(intervalMs = 30000) {
        this.timer = setInterval(() => this.collect().catch(console.error), intervalMs);
        this.timer.unref();   // Таймер не заважає процесу завершитися
    }

    async collect() {
        const report = { timestamp: new Date().toISOString() };

        const postgres = this.db.get('postgres');
        if (postgres) {
            // Скільки з'єднань у якому стані (active, idle, idle in transaction)
            const [rows] = await postgres.query(`
                SELECT state, COUNT(*)::int AS count
                FROM pg_stat_activity
                WHERE datname = current_database()
                GROUP BY state
            `);
            report.postgres = rows;
        }

        const mongo = this.db.get('mongodb');
        if (mongo) {
            const status = await mongo.db.admin().serverStatus();
            report.mongodb = {
                connections: status.connections,
                operations: status.opcounters
            };
        }

        this.lastReport = report;
        return report;
    }
}

// Кінцева точка для балансувальника та систем моніторингу
app.get('/health', async (req, res) => {
    try {
        await databaseManager.get('postgres').authenticate();
        res.json({ status: 'ok', databases: healthMonitor.lastReport });
    } catch {
        res.status(503).json({ status: 'unavailable' });
    }
});
```

Стан `idle in transaction` у статистиці PostgreSQL — поширений тривожний сигнал: з'єднання відкрило транзакцію й не завершило її (найчастіше — забутий `commit` у некерованій транзакції). Такі з'єднання утримують блокування й не повертаються в пул.

### Кешування та оптимізація запитів

Найшвидший запит до бази даних — той, якого не було. **Кеш** зберігає результати дорогих операцій у швидкому сховищі, найчастіше в Redis або Valkey (сумісний з Redis форк під ліцензією BSD, що з'явився у 2024 році після зміни ліцензії Redis; для застосунку вони взаємозамінні й працюють з тим самим клієнтом).

Найпоширеніша стратегія — **cache-aside** («кеш збоку»): застосунок спочатку шукає дані в кеші; якщо не знайшов — читає з бази й кладе результат у кеш із обмеженим терміном життя (TTL). Для даних, які читають дуже часто, додають ще й **локальний кеш** у пам'яті процесу: він найшвидший, але в кожного екземпляра застосунку свій, тож термін життя там має бути коротким.

```mermaid
graph TB
    A[Запит клієнта] --> B[Сервісний шар]
    B --> C{Локальний кеш?}
    C -->|Влучання| D[Повернути дані]
    C -->|Промах| E{Redis/Valkey?}
    E -->|Влучання| F[Оновити локальний кеш<br/>Повернути дані]
    E -->|Промах| G[Запит до бази даних]
    G --> H[Записати в обидва кеші<br/>Повернути дані]
```

```javascript
// src/services/cache.service.js
export class CacheService {
    constructor(redisClient, { localTtlMs = 5000, localMaxSize = 1000 } = {}) {
        this.redis = redisClient;
        this.local = new Map();
        this.localTtlMs = localTtlMs;
        this.localMaxSize = localMaxSize;
        this.stats = { localHits: 0, hits: 0, misses: 0 };
    }

    async get(key) {
        // 1. Локальний кеш процесу (найшвидший, короткий TTL)
        const localEntry = this.local.get(key);
        if (localEntry && localEntry.expires > Date.now()) {
            this.stats.localHits++;
            return localEntry.value;
        }
        this.local.delete(key);

        // 2. Спільний кеш Redis/Valkey
        try {
            const cached = await this.redis.get(key);
            if (cached !== null) {
                this.stats.hits++;
                const value = JSON.parse(cached);
                this.setLocal(key, value);
                return value;
            }
        } catch (error) {
            // Недоступність кешу не повинна ламати застосунок — просто йдемо в базу
            console.error('Помилка читання кешу:', error.message);
        }

        this.stats.misses++;
        return null;
    }

    async set(key, value, ttlSeconds = 300) {
        this.setLocal(key, value);
        try {
            await this.redis.set(key, JSON.stringify(value), { EX: ttlSeconds });
        } catch (error) {
            console.error('Помилка запису в кеш:', error.message);
        }
    }

    async delete(key) {
        this.local.delete(key);
        try {
            await this.redis.del(key);
        } catch (error) {
            console.error('Помилка видалення з кешу:', error.message);
        }
    }

    // Cache-aside в одному методі: взяти з кешу або завантажити й закешувати
    async getOrLoad(key, ttlSeconds, loader) {
        const cached = await this.get(key);
        if (cached !== null) return cached;

        const value = await loader();
        if (value !== null && value !== undefined) {
            await this.set(key, value, ttlSeconds);
        }
        return value;
    }

    // Видалення групи ключів за шаблоном, наприклад 'search:*'.
    // SCAN обходить ключі порціями і не блокує сервер, на відміну від KEYS
    async invalidate(pattern) {
        for (const key of this.local.keys()) {
            if (matchesPattern(key, pattern)) this.local.delete(key);
        }
        // У node-redis v5 ітератор повертає масиви ключів (порції)
        for await (const keys of this.redis.scanIterator({ MATCH: pattern, COUNT: 100 })) {
            if (keys.length > 0) await this.redis.del(keys);
        }
    }

    setLocal(key, value) {
        if (this.local.size >= this.localMaxSize) {
            // Найпростіше витіснення: видаляємо найстаріший запис
            this.local.delete(this.local.keys().next().value);
        }
        this.local.set(key, { value, expires: Date.now() + this.localTtlMs });
    }

    getStats() {
        const total = this.stats.localHits + this.stats.hits + this.stats.misses;
        const hitRate = total > 0
            ? (((this.stats.localHits + this.stats.hits) / total) * 100).toFixed(1)
            : '0.0';
        return { ...this.stats, hitRate: `${hitRate}%`, localSize: this.local.size };
    }
}

function matchesPattern(key, pattern) {
    const regex = new RegExp('^' + pattern.split('*').map(escapeRegExp).join('.*') + '$');
    return regex.test(key);
}

function escapeRegExp(text) {
    return text.replace(/[.*+?^${}()|[\]\\]/g, '\\$&');
}
```

Кілька рішень у цьому класі принципові. Помилки кешу перехоплюються й не пробиваються вище: кеш — це оптимізація, і його недоступність має лише сповільнювати застосунок, а не ламати його. Для видалення групи ключів використано `SCAN`, а не `KEYS`: команда `KEYS` перебирає весь простір ключів за одну операцію й на великій базі може заблокувати Redis на секунди. А сучасний клієнт node-redis використовує Promise і опції на кшталт `{ EX: ttl }` замість застарілих функцій зворотного виклику та `setex`.

**Автоматичне кешування методів.** В інших мовах для цього часто використовують декоратори, і в старих прикладах для Node.js можна побачити запис `@cached(300)` над методом класу. Декоратори поки не підтримуються Node.js без транспіляції (TypeScript чи Babel), тому в чистому JavaScript те саме робиться функцією вищого порядку, яка «обгортає» метод:

```javascript
// Обгортка: повертає функцію з тією самою сигнатурою, але з кешуванням
export function withCache(cache, fn, { ttl = 300, key }) {
    return async (...args) => cache.getOrLoad(key(...args), ttl, () => fn(...args));
}

// Кешований репозиторій поверх будь-якої реалізації контракту
export function createCachedUserRepository(repository, cache) {
    return {
        ...repository,
        findById: withCache(cache, (id) => repository.findById(id), {
            ttl: 300,
            key: (id) => `user:${id}`
        }),
        findByEmail: withCache(cache, (email) => repository.findByEmail(email), {
            ttl: 60,
            key: (email) => `user:email:${email.toLowerCase()}`
        }),
        async update(id, data) {
            const result = await repository.update(id, data);
            // Інвалідуємо все, що стосується цього користувача
            await cache.delete(`user:${id}`);
            if (result?.email) await cache.delete(`user:email:${result.email}`);
            return result;
        }
    };
}
```

Відома приказка каже, що в програмуванні є дві складні речі: іменування та інвалідація кешу. Кожен кеш — це компроміс між швидкістю та свіжістю даних. Тому кешують дані, які читають значно частіше, ніж змінюють, TTL обирають з урахуванням того, наскільки критична застарілість, а після зміни даних кеш явно очищують.

## Висновки та рекомендації щодо вибору

### Критерії вибору між SQL та NoSQL

Вибір типу бази даних повинен ґрунтуватися на вимогах проєкту, а не на популярності технології. Корисно поставити собі кілька запитань.

```mermaid
flowchart TD
    A[Вибір типу БД] --> B{Структура даних}

    B -->|Чітка схема<br/>Багато зв'язків| C[Реляційна БД]
    B -->|Ієрархічні документи<br/>Змінна структура| D[NoSQL]

    C --> E{Вимоги до<br/>узгодженості}
    E -->|Критично важливо| F[PostgreSQL<br/>MySQL]
    E -->|Потрібна й гнучкість полів| F2[PostgreSQL + JSONB]

    D --> H{Тип NoSQL}
    H --> I[Документна<br/>MongoDB]
    H --> J[Ключ-значення<br/>Redis / Valkey]
    H --> K[Графова<br/>Neo4j]
    H --> L[Аналітична<br/>ClickHouse]
```

**Структура даних.** Якщо дані мають чітку схему та багато зв'язків (замовлення, оплати, студенти й оцінки), реляційна база буде природним вибором. Якщо дані ієрархічні, читаються цілими документами й мають різну структуру (каталог товарів різних категорій, контент, журнали подій), документна база дасть більше гнучкості.

**Вимоги до узгодженості** критичні для фінансових, медичних систем і будь-яких систем, де помилка має серйозні наслідки. Тут ACID-транзакції реляційних баз незамінні. Там, де кінцева узгодженість прийнятна (стрічка соціальної мережі, лічильник переглядів), NoSQL може дати кращу продуктивність і доступність.

**Масштаб.** Горизонтальне масштабування — реальна перевага деяких NoSQL-систем, але вона потрібна значно рідше, ніж про неї говорять. Для переважної більшості вебзастосунків PostgreSQL із репліками для читання та кешем забезпечує достатню продуктивність з меншою складністю.

Якщо сумніваєтеся — починайте з PostgreSQL. Завдяки JSONB він закриває більшість сценаріїв, для яких обирають документні бази, і при цьому зберігає транзакції, з'єднання й зрілі інструменти.

### Переваги ORM та альтернативні підходи

**ORM** значно прискорюють розробку, захищають від SQL-ін'єкцій через параметризацію запитів і дозволяють працювати в звичній об'єктній парадигмі. Sequelize та Mongoose надають валідацію, міграції, керування зв'язками й хуки. Сучасні Prisma та Drizzle додають до цього сувору типізацію.

Проте ORM — абстракція, яка «протікає». Розробник, що не розуміє, який SQL генерується, рано чи пізно отримає проблему N+1, вибірку всіх стовпців там, де потрібні два, або повільний запит без індексу. **Прямі SQL-запити** залишаються потрібними для складної аналітики, оптимізації критичних місць і специфічних можливостей бази даних. Оптимальний підхід для більшості проєктів — гібридний: ORM для рутинних операцій, SQL для складних завдань, і завжди — знання SQL.

### Рекомендації для різних типів проєктів

**Стартапи та MVP** цінують швидкість змін. Тут добре працює PostgreSQL з JSONB для «нестабільних» частин моделі або MongoDB з Mongoose для швидкого прототипування без детального проєктування схеми. Важливо лише не плутати відсутність схеми в базі з відсутністю схеми взагалі.

**Корпоративні та облікові застосунки** потребують надійності й узгодженості. PostgreSQL з ORM (Sequelize, Prisma) і дисципліною міграцій — надійна основа для складних бізнес-процесів.

**Системи з високим навантаженням** зазвичай використовують полімодальне зберігання:

```mermaid
graph TB
    A[Сучасний вебзастосунок] --> B[Користувачі, замовлення<br/>PostgreSQL]
    A --> C[Сесії, кеш, ліміти запитів<br/>Redis / Valkey]
    A --> D[Контент, журнали подій<br/>MongoDB]
    A --> E[Аналітика<br/>ClickHouse]
    A --> F[Пошук<br/>Elasticsearch / OpenSearch]
```

Кожне сховище в такій архітектурі — це окремий сервіс, який треба розгортати, оновлювати, резервувати й моніторити. Тому нове сховище додають лише тоді, коли наявні справді не справляються, а не заради різноманітності.

**Аналітичні платформи** поєднують транзакційні бази для оперативних даних з колонковими сховищами для аналітики, а дані між ними переносять процеси ETL (вилучення, перетворення, завантаження).

Найважливіша рекомендація — **еволюційний підхід** до архітектури даних. Починайте з простого рішення, що задовольняє поточні потреби, вимірюйте (журнал повільних запитів, `EXPLAIN ANALYZE`, статистика кешу) і ускладнюйте архітектуру лише тоді, коли вимірювання показують реальне вузьке місце.

У наступній лекції ми використаємо все вивчене для захисту застосунку: зберігатимемо в базі хеші паролів і refresh-токени, використаємо Redis для сесій та обмеження частоти запитів, а патерн Repository допоможе реалізувати перевірку прав доступу до ресурсів.

## Питання для самоперевірки

1. Поясніть властивості ACID. Яку проблему розв'язує кожна з них?
2. Чому твердження «SQL-бази — це CA-системи» є некоректним? Що насправді стверджує теорема CAP і що додає модель PACELC?
3. Чим `JSONB` відрізняється від `JSON` у PostgreSQL? Коли атрибут краще зберігати в JSONB, а коли — окремим стовпцем?
4. Навіщо потрібні міграції, якщо Sequelize вміє створювати таблиці методом `sync()`?
5. Що таке проблема N+1 запитів? Як її виявити й усунути в Sequelize?
6. Чому перевірка унікальності email запитом перед вставкою не гарантує унікальності? Що гарантує?
7. Як змінилися pre-хуки в Mongoose 9 і чому?
8. За якими критеріями обирають між вбудовуванням і посиланням у MongoDB?
9. Які переваги дає патерн Repository? Чому репозиторій має повертати прості об'єкти, а не моделі ORM?
10. Що таке стратегія cache-aside? Чому для видалення ключів за шаблоном використовують `SCAN`, а не `KEYS`?
