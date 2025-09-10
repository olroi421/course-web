# Лекція 5. Бази даних та ORM

## Вступ до сучасних баз даних у веброзробці

У сучасній веброзробці база даних є критичним компонентом архітектури будь-якого серйозного застосунку. Вибір правильної системи управління базами даних та підходу до роботи з нею визначає масштабованість, продуктивність та надійність всієї системи. Кожна база даних має свої особливості, переваги та недоліки, які необхідно враховувати при проєктуванні архітектури.

Історично склалося, що більшість вебзастосунків використовували реляційні бази даних з SQL інтерфейсом. Проте з розвитком інтернету, зростанням обсягів даних та появою нових типів застосунків виникла потреба в альтернативних підходах до зберігання даних. Так з'явилися NoSQL рішення, які пропонують інші моделі даних та підходи до масштабування.

**Object-Relational Mapping (ORM)** революціонізував спосіб взаємодії розробників з базами даних, дозволивши працювати з даними у звичному об'єктно-орієнтованому стилі, абстрагуючись від специфіки конкретної системи управління базами даних. ORM стала мостом між світом об'єктів у програмуванні та реляційним світом баз даних.

Розуміння фундаментальних відмінностей між різними типами баз даних та вміння правильно застосовувати ORM інструменти є критично важливими навичками для сучасного веброзробника. Правильний вибір може забезпечити ефективність розробки та стабільність роботи застосунку на роки вперед.

## SQL та NoSQL бази даних: філософські відмінності

### Реляційна модель даних та SQL

**SQL (Structured Query Language)** бази даних базуються на реляційній моделі даних, запропонованій Едгаром Коддом у 1970 році. Ця модель організовує дані у вигляді таблиць зі строго визначеними схемами, де кожен рядок представляє запис, а кожен стовпець — атрибут сутності.

Фундаментальні принципи реляційної моделі включають атомарність даних, де кожне значення в таблиці є неподільним, унікальність рядків через первинні ключі, та встановлення зв'язків між таблицями через зовнішні ключі. Ця структурованість забезпечує високу цілісність даних та можливість виконання складних аналітичних запитів.

**ACID властивості** є основою надійності SQL баз даних. Атомарність гарантує, що транзакція виконується повністю або не виконується взагалі. Консистентність забезпечує, що база даних завжди знаходиться в коректному стані. Ізольованість означає, що паралельні транзакції не впливають одна на одну. Довговічність гарантує, що зафіксовані зміни зберігаються назавжди.

```sql
-- Приклад складного SQL запиту з з'єднаннями таблиць
SELECT
    u.username,
    u.email,
    COUNT(p.id) as post_count,
    AVG(r.rating) as avg_rating
FROM users u
LEFT JOIN posts p ON u.id = p.user_id
LEFT JOIN reviews r ON p.id = r.post_id
WHERE u.created_at > '2024-01-01'
GROUP BY u.id, u.username, u.email
HAVING COUNT(p.id) > 5
ORDER BY avg_rating DESC, post_count DESC;
```

Реляційні бази даних відмінно підходять для застосунків з чіткою структурою даних, потребою в складних запитах та високими вимогами до цілісності даних. Фінансові системи, системи обліку, CRM та ERP системи традиційно використовують SQL бази даних через їхню надійність та зрілість.

### NoSQL парадигма та різноманітність підходів

**NoSQL (Not Only SQL)** бази даних виникли як відповідь на обмеження традиційних реляційних систем у контексті обробки великих обсягів даних, потреби в горизонтальному масштабуванні та роботі з неструктурованими даними.

**Документ-орієнтовані бази даних** зберігають дані у вигляді документів, найчастіше в JSON або BSON форматі. MongoDB є найпопулярнішим представником цього типу. Документи можуть мати вкладену структуру та не потребують фіксованої схеми, що дозволяє легко адаптуватися до змін у вимогах застосунку.

```javascript
// Приклад документу в MongoDB
{
  "_id": ObjectId("..."),
  "username": "john_doe",
  "email": "john@example.com",
  "profile": {
    "firstName": "John",
    "lastName": "Doe",
    "dateOfBirth": "1990-05-15",
    "interests": ["programming", "music", "travel"]
  },
  "posts": [
    {
      "title": "My First Post",
      "content": "Hello, world!",
      "createdAt": "2024-01-15",
      "tags": ["introduction", "personal"]
    }
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

**Ключ-значення сховища** такі як Redis пропонують найпростішу модель даних, де кожен запис складається з унікального ключа та відповідного значення. Це забезпечує максимальну швидкість доступу та ідеально підходить для кешування, сесій користувачів та простих лічильників.

**Стовпчикові бази даних** як Cassandra організовують дані по стовпцях замість рядків, що дозволяє ефективно зберігати та обробляти великі обсяги аналітичних даних. **Графові бази даних** як Neo4j спеціалізуються на зберіганні та обробці складних зв'язків між сутностями.

### Порівняльний аналіз SQL та NoSQL підходів

**Масштабованість** є ключовою відмінністю між цими підходами. SQL бази традиційно масштабуються вертикально (збільшення потужності сервера), що має фізичні та економічні обмеження. NoSQL системи зазвичай проєктуються для горизонтального масштабування (додавання серверів), що дозволяє практично необмежене зростання.

**Схема даних** у SQL базах є суворою та потребує визначення перед використанням. Зміни схеми часто вимагають складних міграцій та можуть призвести до простою системи. NoSQL бази пропонують гнучкість схеми, дозволяючи зберігати документи з різною структурою в одній колекції.

**Консистентність даних** в SQL системах забезпечується через ACID властивості, гарантуючи строгу консистентність. NoSQL системи часто обирають модель eventual consistency, де консистентність досягається з часом, але система залишається доступною навіть при частковому збої.

```javascript
// CAP теорема: можна обрати тільки два з трьох
// C - Consistency (Консистентність)
// A - Availability (Доступність)
// P - Partition tolerance (Стійкість до розділення)

// SQL бази: зазвичай CA (без P)
// NoSQL бази: зазвичай AP або CP
```

**Продуктивність** залежить від специфіки використання. SQL бази оптимізовані для складних запитів з з'єднаннями таблиць та аналітичних операцій. NoSQL системи показують кращу продуктивність для простих операцій читання/запису великих обсягів даних.

Вибір між SQL та NoSQL не має бути категоричним. Багато сучасних застосунків використовують polyglot persistence — підхід, де різні частини системи використовують найбільш підходящі типи баз даних для своїх специфічних потреб.

## PostgreSQL: потужність реляційної моделі

### Архітектурні особливості PostgreSQL

**PostgreSQL** позиціонується як найбільш просунута система управління реляційними базами даних з відкритим кодом. Її архітектура поєднує надійність традиційних реляційних систем з інноваційними можливостями, які роблять її конкурентоспроможною навіть з комерційними рішеннями.

Система використовує **Multi-Version Concurrency Control (MVCC)** для забезпечення високої продуктивності при паралельному доступі. Замість блокування рядків для читання, MVCC створює версії даних для кожної транзакції, дозволяючи читанням не блокувати записи та навпаки.

**Розширюваність** є однією з ключових переваг PostgreSQL. Система підтримує користувацькі типи даних, функції, оператори та методи індексування. Це дозволяє адаптувати базу даних під специфічні потреби застосунку без зміни основного коду системи.

```sql
-- Створення власного типу даних
CREATE TYPE address AS (
    street VARCHAR(100),
    city VARCHAR(50),
    country VARCHAR(50),
    postal_code VARCHAR(20)
);

-- Створення таблиці з користувацьким типом
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    username VARCHAR(50) UNIQUE NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    home_address address,
    work_address address,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Робота з користувацьким типом
INSERT INTO users (username, email, home_address)
VALUES ('john_doe', 'john@example.com',
        ROW('123 Main St', 'Київ', 'Україна', '01001'));
```

### JSON та NoSQL можливості в PostgreSQL

PostgreSQL пропонує найкращий гібридний підхід, поєднуючи переваги реляційної моделі з гнучкістю документо-орієнтованих систем через нативну підтримку JSON та JSONB типів даних.

**JSONB (JSON Binary)** тип зберігає JSON дані в оптимізованому бінарному форматі, забезпечуючи швидкий пошук та індексування. Це дозволяє зберігати неструктуровані дані в структурованій базі даних без втрати продуктивності.

```sql
-- Створення таблиці з JSONB полем
CREATE TABLE products (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    specifications JSONB,
    metadata JSONB
);

-- Вставка даних з JSON структурою
INSERT INTO products (name, specifications, metadata) VALUES
('Laptop Pro',
 '{"cpu": "Intel i7", "ram": "16GB", "storage": "512GB SSD"}',
 '{"tags": ["electronics", "computer"], "rating": 4.5}');

-- Складні запити по JSON полях
SELECT name, specifications->'cpu' as processor
FROM products
WHERE specifications @> '{"ram": "16GB"}';

-- Індексування JSON полів для швидкого пошуку
CREATE INDEX idx_product_specs_cpu
ON products USING GIN ((specifications->'cpu'));
```

### Продуктивність та оптимізація

**Система індексування** PostgreSQL підтримує різноманітні типи індексів для різних сценаріїв використання. B-Tree індекси підходять для більшості випадків, Hash індекси для точних збігів, GIN та GiST індекси для повнотекстового пошуку та складних типів даних.

```sql
-- Різні типи індексів для різних потреб
CREATE INDEX idx_users_username ON users USING btree(username);
CREATE INDEX idx_users_email_hash ON users USING hash(email);
CREATE INDEX idx_products_search ON products USING gin(to_tsvector('english', name));
CREATE INDEX idx_products_json ON products USING gin(specifications);

-- Аналіз плану виконання запиту
EXPLAIN ANALYZE
SELECT u.username, p.name
FROM users u
JOIN orders o ON u.id = o.user_id
JOIN products p ON o.product_id = p.id
WHERE u.created_at > '2024-01-01';
```

**Партиціонування таблиць** дозволяє розділити великі таблиці на менші частини для поліпшення продуктивності та управління даними.

```sql
-- Створення партиціонованої таблиці за датою
CREATE TABLE sales (
    id SERIAL,
    product_id INTEGER,
    sale_date DATE,
    amount DECIMAL(10,2)
) PARTITION BY RANGE (sale_date);

-- Створення партицій для різних періодів
CREATE TABLE sales_2024_q1 PARTITION OF sales
FOR VALUES FROM ('2024-01-01') TO ('2024-04-01');

CREATE TABLE sales_2024_q2 PARTITION OF sales
FOR VALUES FROM ('2024-04-01') TO ('2024-07-01');
```

PostgreSQL також надає потужні інструменти для моніторингу та налагодження продуктивності, включаючи вбудовані статистики, логування повільних запитів та інструменти аналізу планів виконання.

## Sequelize ORM: об'єктно-реляційне відображення

### Концептуальні основи Sequelize

**Sequelize** є одним з найпопулярніших ORM (Object-Relational Mapping) рішень для Node.js, що дозволяє працювати з реляційними базами даних, використовуючи JavaScript об'єкти замість SQL запитів. Основна філософія Sequelize полягає в абстракції складності SQL та надання розробникам зручного API для роботи з даними.

ORM підхід вирішує проблему **impedance mismatch** — невідповідності між об'єктно-орієнтованою моделлю програмування та реляційною моделлю баз даних. Sequelize автоматично перетворює JavaScript об'єкти в SQL запити та навпаки, дозволяючи розробникам мислити в термінах об'єктів, а не таблиць.

```javascript
// Ініціалізація Sequelize підключення
const { Sequelize } = require('sequelize');

// Підключення до PostgreSQL
const sequelize = new Sequelize({
    dialect: 'postgres',
    host: process.env.DB_HOST || 'localhost',
    port: process.env.DB_PORT || 5432,
    username: process.env.DB_USERNAME,
    password: process.env.DB_PASSWORD,
    database: process.env.DB_NAME,
    logging: false, // Вимкнути логування SQL запитів
    pool: {
        max: 10,     // Максимум підключень в пулі
        min: 0,      // Мінімум підключень в пулі
        acquire: 30000, // Час очікування підключення
        idle: 10000     // Час неактивності перед закриттям
    }
});

// Тестування підключення
async function testConnection() {
    try {
        await sequelize.authenticate();
        console.log('Підключення до бази даних встановлено успішно');
    } catch (error) {
        console.error('Помилка підключення до бази даних:', error);
    }
}
```

### Моделі даних та їх визначення

**Модель** у Sequelize представляє таблицю в базі даних та визначає структуру даних, валідацію, методи та поведінку сутностей. Кожна модель наслідується від базового класу Model та має доступ до всіх ORM можливостей.

```javascript
const { DataTypes, Model } = require('sequelize');

// Визначення моделі користувача
class User extends Model {
    // Методи екземпляру моделі
    getFullName() {
        return `${this.firstName} ${this.lastName}`;
    }

    // Перевірка чи є користувач адміністратором
    isAdmin() {
        return this.role === 'admin';
    }

    // Статичні методи моделі
    static async findByEmail(email) {
        return await this.findOne({ where: { email } });
    }
}

// Ініціалізація моделі з атрибутами та налаштуваннями
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
            len: [3, 50],           // Довжина від 3 до 50 символів
            isAlphanumeric: true,    // Тільки букви та цифри
            notEmpty: true           // Не може бути порожнім
        }
    },
    email: {
        type: DataTypes.STRING(100),
        allowNull: false,
        unique: true,
        validate: {
            isEmail: true,           // Валідація email формату
            notEmpty: true
        }
    },
    firstName: {
        type: DataTypes.STRING(50),
        allowNull: false,
        validate: {
            len: [2, 50],
            isAlpha: true            // Тільки букви
        }
    },
    lastName: {
        type: DataTypes.STRING(50),
        allowNull: false,
        validate: {
            len: [2, 50],
            isAlpha: true
        }
    },
    dateOfBirth: {
        type: DataTypes.DATEONLY,
        validate: {
            isDate: true,
            isBefore: new Date().toISOString() // Не може бути в майбутньому
        }
    },
    role: {
        type: DataTypes.ENUM('user', 'moderator', 'admin'),
        defaultValue: 'user'
    },
    isActive: {
        type: DataTypes.BOOLEAN,
        defaultValue: true
    },
    lastLoginAt: {
        type: DataTypes.DATE
    },
    preferences: {
        type: DataTypes.JSONB,    // Використання JSONB для PostgreSQL
        defaultValue: {}
    }
}, {
    sequelize,
    modelName: 'User',
    tableName: 'users',
    timestamps: true,             // Автоматичні createdAt та updatedAt
    paranoid: true,              // Soft delete (додає deletedAt)
    underscored: true,           // Використання snake_case для полів
    indexes: [
        {
            unique: true,
            fields: ['email']
        },
        {
            fields: ['role']
        },
        {
            type: 'gin',
            fields: ['preferences']  // GIN індекс для JSONB
        }
    ]
});
```

### Міграції: версіонування схеми бази даних

**Міграції** дозволяють версіонувати зміни схеми бази даних та синхронізувати їх між різними середовищами розробки. Кожна міграція містить інструкції для застосування змін (up) та їх відкату (down).

```javascript
// migrations/20240315120000-create-users-table.js
'use strict';

module.exports = {
    up: async (queryInterface, Sequelize) => {
        await queryInterface.createTable('users', {
            id: {
                type: Sequelize.UUID,
                defaultValue: Sequelize.UUIDV4,
                primaryKey: true,
                allowNull: false
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
            firstName: {
                type: Sequelize.STRING(50),
                allowNull: false
            },
            lastName: {
                type: Sequelize.STRING(50),
                allowNull: false
            },
            dateOfBirth: {
                type: Sequelize.DATEONLY
            },
            role: {
                type: Sequelize.ENUM('user', 'moderator', 'admin'),
                defaultValue: 'user'
            },
            isActive: {
                type: Sequelize.BOOLEAN,
                defaultValue: true
            },
            lastLoginAt: {
                type: Sequelize.DATE
            },
            preferences: {
                type: Sequelize.JSONB,
                defaultValue: {}
            },
            createdAt: {
                type: Sequelize.DATE,
                allowNull: false,
                defaultValue: Sequelize.NOW
            },
            updatedAt: {
                type: Sequelize.DATE,
                allowNull: false,
                defaultValue: Sequelize.NOW
            },
            deletedAt: {
                type: Sequelize.DATE
            }
        });

        // Додавання індексів
        await queryInterface.addIndex('users', ['email'], { unique: true });
        await queryInterface.addIndex('users', ['role']);
        await queryInterface.addIndex('users', ['preferences'], {
            using: 'gin',
            name: 'users_preferences_gin_index'
        });
    },

    down: async (queryInterface, Sequelize) => {
        await queryInterface.dropTable('users');
    }
};
```

```javascript
// Міграція для додавання нового поля
// migrations/20240320140000-add-phone-to-users.js
'use strict';

module.exports = {
    up: async (queryInterface, Sequelize) => {
        await queryInterface.addColumn('users', 'phoneNumber', {
            type: Sequelize.STRING(20),
            validate: {
                isPhoneNumber: true
            }
        });

        await queryInterface.addIndex('users', ['phoneNumber']);
    },

    down: async (queryInterface, Sequelize) => {
        await queryInterface.removeColumn('users', 'phoneNumber');
    }
};
```

### Асоціації між моделями

**Асоціації** визначають зв'язки між різними моделями та автоматично створюють відповідні зовнішні ключі та методи для роботи з пов'язаними даними.

```javascript
// Модель категорії товарів
class Category extends Model {}
Category.init({
    id: {
        type: DataTypes.UUID,
        defaultValue: DataTypes.UUIDV4,
        primaryKey: true
    },
    name: {
        type: DataTypes.STRING(100),
        allowNull: false,
        unique: true
    },
    description: DataTypes.TEXT,
    isActive: {
        type: DataTypes.BOOLEAN,
        defaultValue: true
    }
}, { sequelize, modelName: 'Category' });

// Модель товару
class Product extends Model {}
Product.init({
    id: {
        type: DataTypes.UUID,
        defaultValue: DataTypes.UUIDV4,
        primaryKey: true
    },
    name: {
        type: DataTypes.STRING(200),
        allowNull: false
    },
    description: DataTypes.TEXT,
    price: {
        type: DataTypes.DECIMAL(10, 2),
        allowNull: false,
        validate: {
            min: 0
        }
    },
    sku: {
        type: DataTypes.STRING(50),
        unique: true
    },
    stockQuantity: {
        type: DataTypes.INTEGER,
        defaultValue: 0
    }
}, { sequelize, modelName: 'Product' });

// Модель замовлення
class Order extends Model {}
Order.init({
    id: {
        type: DataTypes.UUID,
        defaultValue: DataTypes.UUIDV4,
        primaryKey: true
    },
    orderNumber: {
        type: DataTypes.STRING(20),
        unique: true,
        allowNull: false
    },
    totalAmount: {
        type: DataTypes.DECIMAL(10, 2),
        allowNull: false
    },
    status: {
        type: DataTypes.ENUM('pending', 'confirmed', 'shipped', 'delivered', 'cancelled'),
        defaultValue: 'pending'
    }
}, { sequelize, modelName: 'Order' });

// Проміжна модель для зв'язку Many-to-Many
class OrderItem extends Model {}
OrderItem.init({
    quantity: {
        type: DataTypes.INTEGER,
        allowNull: false,
        validate: {
            min: 1
        }
    },
    unitPrice: {
        type: DataTypes.DECIMAL(10, 2),
        allowNull: false
    },
    totalPrice: {
        type: DataTypes.DECIMAL(10, 2),
        allowNull: false
    }
}, { sequelize, modelName: 'OrderItem' });

// Визначення асоціацій
// One-to-Many: Категорія має багато товарів
Category.hasMany(Product, {
    foreignKey: 'categoryId',
    as: 'products'
});
Product.belongsTo(Category, {
    foreignKey: 'categoryId',
    as: 'category'
});

// One-to-Many: Користувач має багато замовлень
User.hasMany(Order, {
    foreignKey: 'userId',
    as: 'orders'
});
Order.belongsTo(User, {
    foreignKey: 'userId',
    as: 'user'
});

// Many-to-Many: Замовлення містить багато товарів через OrderItem
Order.belongsToMany(Product, {
    through: OrderItem,
    foreignKey: 'orderId',
    as: 'products'
});
Product.belongsToMany(Order, {
    through: OrderItem,
    foreignKey: 'productId',
    as: 'orders'
});
```

### Складні запити та транзакції

```javascript
// Складні запити з включенням пов'язаних даних
async function getUserWithOrders(userId) {
    return await User.findByPk(userId, {
        include: [
            {
                model: Order,
                as: 'orders',
                where: { status: ['confirmed', 'shipped'] },
                include: [
                    {
                        model: Product,
                        as: 'products',
                        through: { attributes: ['quantity', 'unitPrice'] }
                    }
                ]
            }
        ],
        order: [
            ['orders', 'createdAt', 'DESC']
        ]
    });
}

// Робота з транзакціями
async function createOrderWithItems(userId, orderData, items) {
    const transaction = await sequelize.transaction();

    try {
        // Створення замовлення
        const order = await Order.create({
            userId,
            orderNumber: generateOrderNumber(),
            totalAmount: orderData.totalAmount,
            status: 'pending'
        }, { transaction });

        // Додавання товарів до замовлення
        for (const item of items) {
            await OrderItem.create({
                orderId: order.id,
                productId: item.productId,
                quantity: item.quantity,
                unitPrice: item.unitPrice,
                totalPrice: item.quantity * item.unitPrice
            }, { transaction });

            // Зменшення кількості товару на складі
            await Product.decrement('stockQuantity', {
                by: item.quantity,
                where: { id: item.productId },
                transaction
            });
        }

        await transaction.commit();
        return order;
    } catch (error) {
        await transaction.rollback();
        throw error;
    }
}
```

## MongoDB та Mongoose: документо-орієнтований підхід

### Філософія документо-орієнтованого підходу

**MongoDB** представляє кардинально інший підхід до організації даних порівняно з реляційними системами. Замість таблиць з фіксованими схемами MongoDB використовує колекції документів, де кожен документ є самодостатньою одиницею інформації в JSON-подібному форматі (BSON).

Ця модель природно відображає структуру даних у більшості застосунків, де інформація часто організована ієрархічно та має вкладені об'єкти. Документо-орієнтований підхід дозволяє зберігати пов'язані дані разом, зменшуючи потребу в складних з'єднаннях та покращуючи продуктивність читання.

```javascript
// Встановлення підключення до MongoDB
const mongoose = require('mongoose');

async function connectToDatabase() {
    try {
        await mongoose.connect(process.env.MONGODB_URI, {
            useNewUrlParser: true,
            useUnifiedTopology: true,
            maxPoolSize: 10,        // Максимум підключень в пулі
            serverSelectionTimeoutMS: 5000, // Таймаут вибору сервера
            socketTimeoutMS: 45000, // Таймаут сокету
            family: 4              // Використання IPv4
        });

        console.log('Підключення до MongoDB встановлено успішно');
    } catch (error) {
        console.error('Помилка підключення до MongoDB:', error);
        process.exit(1);
    }
}

// Обробка подій підключення
mongoose.connection.on('connected', () => {
    console.log('Mongoose підключено до MongoDB');
});

mongoose.connection.on('error', (err) => {
    console.error('Mongoose помилка підключення:', err);
});

mongoose.connection.on('disconnected', () => {
    console.log('Mongoose відключено від MongoDB');
});
```

### Схеми та моделі в Mongoose

**Mongoose** надає структуру та валідацію для MongoDB документів через систему схем. Схема визначає структуру документів, типи даних, валідацію та поведінку моделі.

```javascript
const mongoose = require('mongoose');
const { Schema } = mongoose;

// Схема для адреси (вкладений об'єкт)
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
        maxlength: [50, 'Назва країни не може перевищувати 50 символів']
    },
    postalCode: {
        type: String,
        required: [true, 'Поштовий код є обов\'язковим'],
        match: [/^\d{5}(-\d{4})?$/, 'Некоректний формат поштового коду']
    }
}, { _id: false }); // Вимкнути автогенерацію _id для вкладених документів

// Основна схема користувача
const userSchema = new Schema({
    username: {
        type: String,
        required: [true, 'Ім\'я користувача є обов\'язковим'],
        unique: true,
        trim: true,
        lowercase: true,
        minlength: [3, 'Ім\'я користувача має містити мінімум 3 символи'],
        maxlength: [30, 'Ім\'я користувача не може перевищувати 30 символів'],
        match: [/^[a-zA-Z0-9_]+$/, 'Ім\'я користувача може містити тільки букви, цифри та підкреслення']
    },
    email: {
        type: String,
        required: [true, 'Email є обов\'язковим'],
        unique: true,
        lowercase: true,
        trim: true,
        match: [/^\w+([.-]?\w+)*@\w+([.-]?\w+)*(\.\w{2,3})+$/, 'Некоректний email формат']
    },
    profile: {
        firstName: {
            type: String,
            required: [true, 'Ім\'я є обов\'язковим'],
            trim: true,
            maxlength: [50, 'Ім\'я не може перевищувати 50 символів']
        },
        lastName: {
            type: String,
            required: [true, 'Прізвище є обов\'язковим'],
            trim: true,
            maxlength: [50, 'Прізвище не може перевищувати 50 символів']
        },
        dateOfBirth: {
            type: Date,
            validate: {
                validator: function(value) {
                    return value < new Date();
                },
                message: 'Дата народження не може бути в майбутньому'
            }
        },
        avatar: {
            type: String,
            match: [/^https?:\/\/.*\.(jpg|jpeg|png|gif)$/i, 'Некоректний URL аватара']
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
            default: 'light'
        },
        language: {
            type: String,
            enum: ['uk', 'en', 'ru'],
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
    lastLoginAt: {
        type: Date
    },
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
        facebook: String,
        twitter: String,
        linkedin: String,
        github: String
    }
}, {
    timestamps: true,           // Автоматичні createdAt та updatedAt
    versionKey: false,         // Вимкнути __v поле
    collection: 'users'        // Явно вказати назву колекції
});

// Індекси для покращення продуктивності
userSchema.index({ email: 1 }, { unique: true });
userSchema.index({ username: 1 }, { unique: true });
userSchema.index({ 'profile.firstName': 1, 'profile.lastName': 1 });
userSchema.index({ role: 1, isActive: 1 });
userSchema.index({ tags: 1 });
userSchema.index({ createdAt: -1 });

// Складний текстовий індекс для пошуку
userSchema.index({
    username: 'text',
    'profile.firstName': 'text',
    'profile.lastName': 'text',
    email: 'text'
}, {
    weights: {
        username: 10,
        'profile.firstName': 5,
        'profile.lastName': 5,
        email: 1
    }
});
```

### Віртуальні поля та методи

```javascript
// Віртуальні поля - обчислювані властивості
userSchema.virtual('profile.fullName').get(function() {
    return `${this.profile.firstName} ${this.profile.lastName}`;
});

userSchema.virtual('profile.age').get(function() {
    if (!this.profile.dateOfBirth) return null;
    const today = new Date();
    const birthDate = new Date(this.profile.dateOfBirth);
    let age = today.getFullYear() - birthDate.getFullYear();
    const monthDiff = today.getMonth() - birthDate.getMonth();

    if (monthDiff < 0 || (monthDiff === 0 && today.getDate() < birthDate.getDate())) {
        age--;
    }
    return age;
});

// Віртуальне поле для отримання ініціалів
userSchema.virtual('profile.initials').get(function() {
    const firstName = this.profile.firstName || '';
    const lastName = this.profile.lastName || '';
    return `${firstName.charAt(0)}${lastName.charAt(0)}`.toUpperCase();
});

// Методи екземпляру
userSchema.methods.getPublicProfile = function() {
    return {
        id: this._id,
        username: this.username,
        profile: {
            fullName: this.profile.fullName,
            avatar: this.profile.avatar,
            initials: this.profile.initials
        },
        role: this.role,
        isActive: this.isActive,
        memberSince: this.createdAt
    };
};

userSchema.methods.updateLastLogin = function() {
    this.lastLoginAt = new Date();
    this.loginCount += 1;
    return this.save();
};

userSchema.methods.isAdmin = function() {
    return this.role === 'admin';
};

userSchema.methods.canModerate = function() {
    return ['admin', 'moderator'].includes(this.role);
};

// Статичні методи моделі
userSchema.statics.findByEmail = function(email) {
    return this.findOne({ email: email.toLowerCase() });
};

userSchema.statics.findActiveUsers = function() {
    return this.find({ isActive: true }).sort({ createdAt: -1 });
};

userSchema.statics.searchUsers = function(searchTerm, limit = 10) {
    return this.find(
        { $text: { $search: searchTerm } },
        { score: { $meta: 'textScore' } }
    ).sort({ score: { $meta: 'textScore' } }).limit(limit);
};

userSchema.statics.getUserStats = function() {
    return this.aggregate([
        {
            $group: {
                _id: '$role',
                count: { $sum: 1 },
                activeCount: {
                    $sum: { $cond: ['$isActive', 1, 0] }
                }
            }
        },
        {
            $project: {
                role: '$_id',
                _id: 0,
                totalUsers: '$count',
                activeUsers: '$activeCount',
                inactiveUsers: { $subtract: ['$count', '$activeCount'] }
            }
        }
    ]);
};
```

### Middleware та хуки

```javascript
// Pre middleware - виконується перед операцією
userSchema.pre('save', async function(next) {
    // Цей middleware виконається тільки для нових документів
    if (this.isNew) {
        console.log(`Створюється новий користувач: ${this.email}`);

        // Автоматичне встановлення ініціалів
        if (!this.profile.avatar && this.profile.firstName && this.profile.lastName) {
            // Тут можна генерувати аватар з ініціалами
            this.profile.avatar = `https://ui-avatars.com/api/?name=${this.profile.firstName}+${this.profile.lastName}&size=200`;
        }
    }

    // Валідація перед збереженням
    if (this.isModified('email')) {
        const existingUser = await this.constructor.findOne({
            email: this.email,
            _id: { $ne: this._id }
        });

        if (existingUser) {
            const error = new Error('Email вже використовується');
            return next(error);
        }
    }

    next();
});

// Post middleware - виконується після операції
userSchema.post('save', function(doc, next) {
    console.log(`Користувач збережений: ${doc.email}`);

    // Тут можна відправити welcome email для нових користувачів
    if (doc.isNew) {
        // await sendWelcomeEmail(doc.email);
    }

    next();
});

userSchema.pre('deleteOne', { document: true, query: false }, function(next) {
    console.log(`Видаляється користувач: ${this.email}`);
    // Тут можна очистити пов'язані дані
    next();
});

// Middleware для populate
userSchema.pre(/^find/, function(next) {
    // Автоматично виключити неактивних користувачів (якщо не вказано інакше)
    if (!this.getQuery().isActive) {
        this.find({ isActive: { $ne: false } });
    }
    next();
});
```

### Робота з документами та запити

```javascript
// Створення моделі
const User = mongoose.model('User', userSchema);

// Приклади використання
async function demonstrateMongooseQueries() {
    try {
        // Створення нового користувача
        const newUser = new User({
            username: 'john_doe',
            email: 'john.doe@example.com',
            profile: {
                firstName: 'John',
                lastName: 'Doe',
                dateOfBirth: new Date('1990-05-15')
            },
            addresses: {
                home: {
                    street: '123 Main St',
                    city: 'Київ',
                    country: 'Україна',
                    postalCode: '01001'
                }
            },
            preferences: {
                theme: 'dark',
                language: 'uk',
                notifications: {
                    email: true,
                    push: true,
                    sms: false
                }
            },
            tags: ['developer', 'nodejs', 'react']
        });

        await newUser.save();
        console.log('Новий користувач створений:', newUser.profile.fullName);

        // Пошук користувачів з різними критеріями
        const users = await User.find({
            role: 'user',
            isActive: true,
            'profile.age': { $gte: 18, $lte: 65 },
            tags: { $in: ['developer', 'designer'] }
        })
        .select('username email profile.fullName role createdAt')
        .sort({ createdAt: -1 })
        .limit(10);

        // Складний пошук з агрегацією
        const usersByCountry = await User.aggregate([
            { $match: { isActive: true } },
            { $group: {
                _id: '$addresses.home.country',
                count: { $sum: 1 },
                avgAge: { $avg: '$profile.age' }
            }},
            { $sort: { count: -1 } }
        ]);

        // Оновлення документа
        await User.findByIdAndUpdate(
            newUser._id,
            {
                $set: {
                    lastLoginAt: new Date(),
                    'preferences.theme': 'light'
                },
                $inc: { loginCount: 1 },
                $addToSet: { tags: 'javascript' }
            },
            { new: true, runValidators: true }
        );

        // Текстовий пошук
        const searchResults = await User.find(
            { $text: { $search: 'john developer' } },
            { score: { $meta: 'textScore' } }
        ).sort({ score: { $meta: 'textScore' } });

        console.log(`Знайдено ${searchResults.length} користувачів`);

    } catch (error) {
        console.error('Помилка при роботі з MongoDB:', error);
    }
}
```

## Паттерни роботи з базами даних

### Repository Pattern

**Repository Pattern** абстрагує логіку доступу до даних та забезпечує централізоване місце для всіх операцій з базою даних. Цей паттерн особливо корисний при роботі з різними типами баз даних або при потребі легко замінити ORM.

```javascript
// Абстрактний базовий репозиторій
class BaseRepository {
    constructor(model) {
        this.model = model;
    }

    async findById(id) {
        return await this.model.findByPk ?
            await this.model.findByPk(id) :
            await this.model.findById(id);
    }

    async findAll(options = {}) {
        const { limit = 50, offset = 0, where = {}, order = [] } = options;

        if (this.model.findAndCountAll) {
            // Sequelize
            return await this.model.findAndCountAll({
                where,
                limit,
                offset,
                order
            });
        } else {
            // Mongoose
            const total = await this.model.countDocuments(where);
            const rows = await this.model.find(where)
                .limit(limit)
                .skip(offset)
                .sort(order.reduce((acc, [field, direction]) => {
                    acc[field] = direction === 'ASC' ? 1 : -1;
                    return acc;
                }, {}));

            return { rows, count: total };
        }
    }

    async create(data) {
        return await this.model.create(data);
    }

    async update(id, data) {
        if (this.model.update) {
            // Sequelize
            await this.model.update(data, { where: { id } });
            return await this.model.findByPk(id);
        } else {
            // Mongoose
            return await this.model.findByIdAndUpdate(id, data, {
                new: true,
                runValidators: true
            });
        }
    }

    async delete(id) {
        if (this.model.destroy) {
            // Sequelize
            return await this.model.destroy({ where: { id } });
        } else {
            // Mongoose
            return await this.model.findByIdAndDelete(id);
        }
    }
}

// Спеціалізований репозиторій для користувачів
class UserRepository extends BaseRepository {
    constructor(userModel) {
        super(userModel);
    }

    async findByEmail(email) {
        if (this.model.findOne) {
            // Sequelize та Mongoose мають схожий API для findOne
            return await this.model.findOne({
                where: { email } // Sequelize
            }) || await this.model.findOne({ email }); // Mongoose
        }
    }

    async findActiveUsers(limit = 50) {
        const options = {
            where: { isActive: true },
            limit,
            order: [['createdAt', 'DESC']]
        };
        return await this.findAll(options);
    }

    async searchUsers(searchTerm, options = {}) {
        const { limit = 10 } = options;

        if (this.model.findAndCountAll) {
            // Sequelize з PostgreSQL повнотекстовим пошуком
            return await this.model.findAndCountAll({
                where: {
                    [Op.or]: [
                        { username: { [Op.iLike]: `%${searchTerm}%` } },
                        { email: { [Op.iLike]: `%${searchTerm}%` } },
                        { firstName: { [Op.iLike]: `%${searchTerm}%` } },
                        { lastName: { [Op.iLike]: `%${searchTerm}%` } }
                    ]
                },
                limit
            });
        } else {
            // Mongoose з текстовим пошуком
            const total = await this.model.countDocuments({
                $text: { $search: searchTerm }
            });
            const users = await this.model.find(
                { $text: { $search: searchTerm } },
                { score: { $meta: 'textScore' } }
            )
            .sort({ score: { $meta: 'textScore' } })
            .limit(limit);

            return { rows: users, count: total };
        }
    }

    async getUserStats() {
        if (this.model.findAll) {
            // Sequelize агрегація
            return await this.model.findAll({
                attributes: [
                    'role',
                    [sequelize.fn('COUNT', '*'), 'total'],
                    [sequelize.fn('COUNT', sequelize.where(
                        sequelize.col('isActive'), true
                    )), 'active']
                ],
                group: ['role']
            });
        } else {
            // Mongoose агрегація
            return await this.model.aggregate([
                {
                    $group: {
                        _id: '$role',
                        total: { $sum: 1 },
                        active: {
                            $sum: { $cond: ['$isActive', 1, 0] }
                        }
                    }
                },
                {
                    $project: {
                        role: '$_id',
                        _id: 0,
                        total: 1,
                        active: 1,
                        inactive: { $subtract: ['$total', '$active'] }
                    }
                }
            ]);
        }
    }

    async updateLastLogin(userId) {
        return await this.update(userId, {
            lastLoginAt: new Date(),
            loginCount: this.model.sequelize ?
                sequelize.literal('login_count + 1') : // Sequelize
                { $inc: { loginCount: 1 } } // Mongoose (потрібен окремий виклик)
        });
    }
}
```

### Data Access Layer Pattern

```javascript
// Сервіс шар для бізнес-логіки
class UserService {
    constructor(userRepository, emailService, cacheService) {
        this.userRepository = userRepository;
        this.emailService = emailService;
        this.cacheService = cacheService;
    }

    async createUser(userData) {
        // Валідація бізнес-правил
        await this.validateUserData(userData);

        // Створення користувача
        const user = await this.userRepository.create(userData);

        // Кешування
        await this.cacheService.set(`user:${user.id}`, user, 300); // 5 хвилин

        // Відправка welcome email
        await this.emailService.sendWelcomeEmail(user.email, user.profile.firstName);

        return user;
    }

    async getUserById(id) {
        // Спочатку перевіряємо кеш
        let user = await this.cacheService.get(`user:${id}`);

        if (!user) {
            user = await this.userRepository.findById(id);
            if (user) {
                await this.cacheService.set(`user:${id}`, user, 300);
            }
        }

        return user;
    }

    async updateUser(id, updateData) {
        // Валідація оновлень
        await this.validateUpdateData(updateData);

        const user = await this.userRepository.update(id, updateData);

        // Оновлення кешу
        await this.cacheService.delete(`user:${id}`);
        await this.cacheService.set(`user:${id}`, user, 300);

        return user;
    }

    async searchUsers(searchTerm, options = {}) {
        // Кешування результатів пошуку
        const cacheKey = `search:${searchTerm}:${JSON.stringify(options)}`;
        let result = await this.cacheService.get(cacheKey);

        if (!result) {
            result = await this.userRepository.searchUsers(searchTerm, options);
            await this.cacheService.set(cacheKey, result, 60); // 1 хвилина
        }

        return result;
    }

    async validateUserData(userData) {
        // Перевірка унікальності email
        const existingUser = await this.userRepository.findByEmail(userData.email);
        if (existingUser) {
            throw new Error('Користувач з таким email вже існує');
        }

        // Інші бізнес-правила валідації
        if (userData.profile.dateOfBirth) {
            const age = this.calculateAge(userData.profile.dateOfBirth);
            if (age < 13) {
                throw new Error('Користувач повинен бути старше 13 років');
            }
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

### Connection Pooling та оптимізація

```javascript
// Конфігурація пулу підключень для різних баз даних
class DatabaseManager {
    constructor() {
        this.connections = new Map();
    }

    async initializeConnections(config) {
        // PostgreSQL з Sequelize
        if (config.postgres) {
            const sequelize = new Sequelize({
                ...config.postgres,
                pool: {
                    max: 20,        // Максимум підключень
                    min: 0,         // Мінімум підключень
                    acquire: 30000, // Максимальний час очікування підключення (мс)
                    idle: 10000     // Час простою перед закриттям підключення
                },
                logging: config.postgres.logging || false,
                dialectOptions: {
                    ssl: config.postgres.ssl || false,
                    connectTimeout: 20000
                }
            });

            await sequelize.authenticate();
            this.connections.set('postgres', sequelize);
        }

        // MongoDB з Mongoose
        if (config.mongodb) {
            await mongoose.connect(config.mongodb.uri, {
                ...config.mongodb.options,
                maxPoolSize: 20,        // Максимум підключень в пулі
                minPoolSize: 5,         // Мінімум підключень в пулі
                maxIdleTimeMS: 30000,   // Час простою підключення
                serverSelectionTimeoutMS: 5000,
                heartbeatFrequencyMS: 10000
            });

            this.connections.set('mongodb', mongoose.connection);
        }

        // Redis для кешування
        if (config.redis) {
            const Redis = require('redis');
            const redisClient = Redis.createClient({
                ...config.redis,
                retry_strategy: (options) => {
                    if (options.error && options.error.code === 'ECONNREFUSED') {
                        console.error('Redis сервер відмовив в підключенні');
                        return new Error('Redis сервер недоступний');
                    }
                    if (options.total_retry_time > 1000 * 60 * 60) {
                        console.error('Час повторних спроб підключення до Redis вичерпано');
                        return new Error('Час повторних спроб вичерпано');
                    }
                    if (options.attempt > 10) {
                        return undefined;
                    }
                    return Math.min(options.attempt * 100, 3000);
                }
            });

            await redisClient.connect();
            this.connections.set('redis', redisClient);
        }
    }

    getConnection(type) {
        return this.connections.get(type);
    }

    async closeAllConnections() {
        for (const [type, connection] of this.connections) {
            try {
                if (type === 'postgres') {
                    await connection.close();
                } else if (type === 'mongodb') {
                    await connection.close();
                } else if (type === 'redis') {
                    await connection.quit();
                }
                console.log(`Підключення ${type} закрито`);
            } catch (error) {
                console.error(`Помилка при закритті підключення ${type}:`, error);
            }
        }
        this.connections.clear();
    }
}

// Монітор продуктивності та здоров'я бази даних
class DatabaseHealthMonitor {
    constructor(databaseManager) {
        this.databaseManager = databaseManager;
        this.metrics = {
            queries: new Map(),
            connections: new Map(),
            errors: new Map()
        };
    }

    startMonitoring(intervalMs = 30000) {
        setInterval(async () => {
            await this.collectMetrics();
        }, intervalMs);
    }

    async collectMetrics() {
        // Моніторинг PostgreSQL
        const postgres = this.databaseManager.getConnection('postgres');
        if (postgres) {
            try {
                const [results] = await postgres.query(`
                    SELECT
                        state,
                        COUNT(*) as count
                    FROM pg_stat_activity
                    WHERE datname = current_database()
                    GROUP BY state
                `);

                this.metrics.connections.set('postgres', {
                    timestamp: new Date(),
                    connections: results
                });
            } catch (error) {
                this.recordError('postgres', error);
            }
        }

        // Моніторинг MongoDB
        const mongodb = this.databaseManager.getConnection('mongodb');
        if (mongodb) {
            try {
                const admin = mongodb.db.admin();
                const serverStatus = await admin.serverStatus();

                this.metrics.connections.set('mongodb', {
                    timestamp: new Date(),
                    connections: serverStatus.connections,
                    operations: serverStatus.opcounters
                });
            } catch (error) {
                this.recordError('mongodb', error);
            }
        }
    }

    recordError(database, error) {
        const errorKey = `${database}:${error.name}`;
        const current = this.metrics.errors.get(errorKey) || 0;
        this.metrics.errors.set(errorKey, current + 1);

        console.error(`Database error [${database}]:`, error.message);
    }

    getHealthReport() {
        return {
            connections: Object.fromEntries(this.metrics.connections),
            errors: Object.fromEntries(this.metrics.errors),
            timestamp: new Date()
        };
    }
}
```

### Кешування та оптимізація запитів

```javascript
// Сервіс кешування з різними стратегіями
class CacheService {
    constructor(redisClient) {
        this.redis = redisClient;
        this.localCache = new Map(); // Локальний кеш для часто використовуваних даних
        this.cacheStats = {
            hits: 0,
            misses: 0,
            localHits: 0
        };
    }

    async get(key, options = {}) {
        const { useLocal = true, ttl } = options;

        // Спочатку перевіряємо локальний кеш
        if (useLocal && this.localCache.has(key)) {
            const cached = this.localCache.get(key);
            if (cached.expires > Date.now()) {
                this.cacheStats.localHits++;
                return cached.value;
            } else {
                this.localCache.delete(key);
            }
        }

        // Перевіряємо Redis кеш
        try {
            const cached = await this.redis.get(key);
            if (cached) {
                this.cacheStats.hits++;
                const value = JSON.parse(cached);

                // Додаємо в локальний кеш
                if (useLocal) {
                    this.localCache.set(key, {
                        value,
                        expires: Date.now() + (ttl || 300000) // 5 хвилин за замовчуванням
                    });
                }

                return value;
            }
        } catch (error) {
            console.error('Redis cache error:', error);
        }

        this.cacheStats.misses++;
        return null;
    }

    async set(key, value, ttl = 300) {
        try {
            // Зберігаємо в Redis
            await this.redis.setex(key, ttl, JSON.stringify(value));

            // Зберігаємо в локальному кеші
            this.localCache.set(key, {
                value,
                expires: Date.now() + ttl * 1000
            });
        } catch (error) {
            console.error('Redis cache set error:', error);
        }
    }

    async invalidate(pattern) {
        try {
            const keys = await this.redis.keys(pattern);
            if (keys.length > 0) {
                await this.redis.del(...keys);
            }

            // Очищуємо локальний кеш
            for (const key of this.localCache.keys()) {
                if (key.match(pattern.replace('*', '.*'))) {
                    this.localCache.delete(key);
                }
            }
        } catch (error) {
            console.error('Redis cache invalidation error:', error);
        }
    }

    getStats() {
        const total = this.cacheStats.hits + this.cacheStats.misses;
        return {
            ...this.cacheStats,
            hitRate: total > 0 ? (this.cacheStats.hits / total * 100).toFixed(2) + '%' : '0%',
            localCacheSize: this.localCache.size
        };
    }
}

// Декоратор для автоматичного кешування методів
function cached(ttl = 300, keyGenerator = null) {
    return function(target, propertyName, descriptor) {
        const method = descriptor.value;

        descriptor.value = async function(...args) {
            const cacheKey = keyGenerator ?
                keyGenerator.call(this, ...args) :
                `${target.constructor.name}:${propertyName}:${JSON.stringify(args)}`;

            // Перевіряємо кеш
            if (this.cacheService) {
                const cached = await this.cacheService.get(cacheKey);
                if (cached !== null) {
                    return cached;
                }
            }

            // Виконуємо оригінальний метод
            const result = await method.apply(this, args);

            // Кешуємо результат
            if (this.cacheService && result !== null) {
                await this.cacheService.set(cacheKey, result, ttl);
            }

            return result;
        };

        return descriptor;
    };
}

// Приклад використання декоратора кешування
class OptimizedUserRepository extends UserRepository {
    constructor(userModel, cacheService) {
        super(userModel);
        this.cacheService = cacheService;
    }

    @cached(300, function(id) { return `user:${id}`; })
    async findById(id) {
        return await super.findById(id);
    }

    @cached(60, function(email) { return `user:email:${email}`; })
    async findByEmail(email) {
        return await super.findByEmail(email);
    }

    @cached(600, function() { return 'user:stats'; })
    async getUserStats() {
        return await super.getUserStats();
    }

    // Інвалідація кешу після оновлення
    async update(id, data) {
        const result = await super.update(id, data);

        // Очищуємо пов'язаний кеш
        await this.cacheService.invalidate(`user:${id}*`);
        await this.cacheService.invalidate('user:stats');

        return result;
    }
}
```

## Висновки та рекомендації щодо вибору

### Критерії вибору між SQL та NoSQL

Вибір між SQL та NoSQL базами даних повинен базуватися на специфічних вимогах проєкту, а не на популярності або особистих уподобаннях розробників. **Структурованість даних** є першим критерієм: якщо дані мають чітку схему та складні зв'язки, SQL бази будуть природним вибором. Для неструктурованих або динамічних даних NoSQL рішення забезпечать більшу гнучкість.

**Вимоги до консистентності** критично важливі для фінансових систем, медичних застосунків або будь-яких систем, де помилка може мати серйозні наслідки. У таких випадках ACID властивості SQL баз незамінні. Для застосунків, де eventual consistency прийнятна (соціальні мережі, контент-платформи), NoSQL може забезпечити кращу продуктивність.

**Масштабованість** та очікувані навантаження визначають архітектурний підхід. Якщо проєкт потребує горизонтального масштабування для обробки мільйонів користувачів, NoSQL системи мають архітектурні переваги. Для застосунків з обмеженою аудиторією SQL бази забезпечують достатню продуктивність з меншою складністю.

### Переваги ORM та альтернативні підходи

**ORM підхід** значно прискорює розробку, забезпечує безпеку від SQL ін'єкцій та дозволяє працювати в звичній об'єктно-орієнтованій парадигмі. Sequelize та Mongoose надають потужні інструменти для валідації, міграцій та управління зв'язками між сутностями. Автоматична генерація запитів зменшує кількість boilerplate коду та помилок.

Проте **прямі SQL запити** залишаються необхідними для складних аналітичних операцій, оптимізації продуктивності критичних частин системи та використання специфічних можливостей бази даних. Гібридний підхід, де ORM використовується для рутинних операцій, а прямі запити для складних завдань, часто є оптимальним рішенням.

**Query builders** як Knex.js займають проміжну позицію, забезпечуючи програмний інтерфейс для побудови запитів без повноцінного ORM overhead. Цей підхід підходить для команд, що потребують більшого контролю над запитами при збереженні зручності програмного інтерфейсу.

### Рекомендації для різних типів проєктів

**Стартапи та MVP проєкти** можуть скористатися гнучкістю NoSQL для швидкого прототипування та адаптації до змінних вимог. MongoDB з Mongoose дозволяє швидко розпочати розробку без детального проєктування схеми.

**Корпоративні застосунки** зазвичай потребують надійності та консистентності SQL систем. PostgreSQL з Sequelize забезпечує потужну основу для складних бізнес-процесів з можливістю використання JSONB для гнучких частин схеми.

**High-load системи** можуть використовувати polyglot persistence: PostgreSQL для критичних транзакційних даних, MongoDB для контенту та логів, Redis для кешування та сесій. Такий підхід дозволяє використовувати сильні сторони кожної технології.

**Аналітичні платформи** можуть поєднувати транзакційні бази для оперативних даних з колоночними сховищами для аналітики. ETL процеси забезпечують синхронізацію між системами.

Найважливішою рекомендацією є **еволюційний підхід** до архітектури даних. Розпочинайте з простих рішень, що задовольняють поточні потреби, і масштабуйте архітектуру в міру зростання вимог. Гарний моніторинг та профілювання допоможуть виявити вузькі місця та прийняти обґрунтовані рішення щодо оптимізації.

Розуміння принципів роботи різних типів баз даних, вміння правильно проєктувати схеми та ефективно використовувати ORM інструменти є фундаментальними навичками сучасного веброзробника. Інвестиції в глибоке вивчення цих технологій окупляться підвищенням якості та продуктивності розроблюваних систем.
