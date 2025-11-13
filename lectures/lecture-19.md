# Лекція 19 TypeScript у веброзробці

## Вступ до TypeScript: від JavaScript до типобезпеки

### Історична перспектива та еволюція JavaScript

JavaScript народився як динамічна, слабко типізована мова програмування, створена для додавання інтерактивності до вебсторінок. Ця гнучкість була одночасно перевагою та недоліком. З одного боку, вона дозволяла швидко писати код без необхідності явного оголошення типів. З іншого боку, відсутність типів призводила до помилок під час виконання, які могли б бути виявлені на етапі компіляції в типізованих мовах.

З розвитком JavaScript та його використанням для створення великих застосунків обмеження динамічної типізації стали очевидними. Розробники зіткнулися з проблемами підтримуваності коду, складністю рефакторингу та відсутністю надійної підтримки інструментів розробки. У 2012 році Microsoft представила TypeScript як рішення цих проблем, зберігаючи при цьому всю потужність та гнучкість JavaScript.

### Філософія TypeScript: безпека через типи

TypeScript базується на фундаментальному принципі: типи роблять код більш надійним та підтримуваним. Це не просто додавання анотацій типів до JavaScript, а створення екосистеми, де типи допомагають виявляти помилки на етапі розробки, покращують документацію коду та забезпечують потужну підтримку інструментів розробки.

Ключовою особливістю TypeScript є те, що він є надмножиною JavaScript. Це означає, що будь-який валідний JavaScript код є валідним TypeScript кодом. Розробники можуть поступово впроваджувати TypeScript у існуючі проєкти, додаючи типи там, де це найбільш корисно, не переписуючи весь код одразу.

### Проблеми, які вирішує TypeScript

Розглянемо конкретні проблеми JavaScript, які TypeScript вирішує систематично.

**Проблема динамічної типізації**: у JavaScript змінна може містити значення будьякого типу, і цей тип може змінюватися під час виконання. Це призводить до помилок, які важко виявити до запуску програми.

```javascript
// JavaScript: помилка виявиться тільки під час виконання
function calculatePrice(price, quantity) {
    return price * quantity;
}

const total = calculatePrice("100", "5"); // "100100" замість 500
```

TypeScript дозволяє виявити таку помилку на етапі розробки.

```typescript
// TypeScript: помилка виявиться на етапі компіляції
function calculatePrice(price: number, quantity: number): number {
    return price * quantity;
}

const total = calculatePrice("100", "5"); // Помилка компіляції
```

**Проблема відсутності автодоповнення**: у чистому JavaScript IDE не може надійно визначити, які властивості має об'єкт, що призводить до помилок у назвах властивостей.

**Проблема рефакторингу**: зміна сигнатури функції або структури об'єкта у великому проєкті на JavaScript вимагає ретельного пошуку всіх місць використання. TypeScript автоматично виявляє всі місця, де потрібні зміни.

**Проблема документації**: коментарі в JavaScript можуть застаріти та не відповідати реальному коду. Типи в TypeScript є частиною коду та завжди актуальні.

## TypeScript основи та налаштування

### Встановлення та конфігурація проєкту

TypeScript можна встановити глобально або як залежність проєкту. Рекомендований підхід – встановлення як залежності розробки для кожного проєкту окремо, що забезпечує консистентність версій між членами команди.

```bash
# Ініціалізація нового проєкту
npm init -y

# Встановлення TypeScript
npm install --save-dev typescript

# Створення конфігураційного файлу
npx tsc --init
```

Файл `tsconfig.json` є центральним елементом конфігурації TypeScript проєкту. Він визначає опції компілятора, файли для включення та виключення, а також інші налаштування, які впливають на поведінку TypeScript.

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "lib": ["ES2020"],
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "resolveJsonModule": true,
    "declaration": true,
    "declarationMap": true,
    "sourceMap": true,
    "moduleResolution": "node"
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist"]
}
```

Розглянемо найважливіші опції конфігурації.

**target**: визначає, до якої версії ECMAScript компілюватиметься код. Вибір залежить від середовища виконання. Для сучасних браузерів та Node.js можна використовувати ES2020 або новіші версії.

**module**: визначає систему модулів для згенерованого JavaScript коду. Для Node.js зазвичай використовується commonjs, для сучасних браузерів та інструментів збірки – ES2020 або ESNext.

**strict**: вмикає всі суворі перевірки типів. Це рекомендована опція для нових проєктів, оскільки вона забезпечує максимальну типобезпеку. Включає в себе strictNullChecks, strictFunctionTypes, strictBindCallApply та інші перевірки.

**esModuleInterop**: забезпечує сумісність між CommonJS та ES модулями, дозволяючи використовувати import для CommonJS модулів.

**sourceMap**: генерує файли карт джерел, які дозволяють налагоджувати TypeScript код безпосередньо в браузері або налагоджувачі, навіть після компіляції в JavaScript.

### Базові типи та їх використання

TypeScript надає набір примітивних типів, які відповідають типам JavaScript, а також додаткові типи для більш точного моделювання даних.

```typescript
// Примітивні типи
let isDone: boolean = false;
let decimal: number = 6;
let hexadecimal: number = 0xf00d;
let binary: number = 0b1010;
let octal: number = 0o744;
let bigNumber: bigint = 100n;

let color: string = "синій";
let fullName: string = `Іван Петренко`;
let age: number = 30;
let sentence: string = `Привіт, мене звати ${fullName}. Мені ${age} років.`;

// Масиви можна оголошувати двома способами
let list: number[] = [1, 2, 3];
let genericList: Array<number> = [1, 2, 3];

// Кортежі дозволяють описати масиви з фіксованою кількістю елементів
let tuple: [string, number];
tuple = ["привіт", 10]; // Правильно
// tuple = [10, "привіт"]; // Помилка

// Enum для означених наборів значень
enum Color {
    Red = 1,
    Green = 2,
    Blue = 4
}
let c: Color = Color.Green;

// Any тип для випадків, коли тип невідомий або може змінюватися
let notSure: any = 4;
notSure = "може бути рядком";
notSure = false; // або булевим значенням

// Unknown – більш безпечна альтернатива any
let uncertain: unknown = 4;
// uncertain.toFixed(); // Помилка: потрібна перевірка типу
if (typeof uncertain === "number") {
    uncertain.toFixed(); // Правильно після перевірки
}

// Void для функцій без повернення значення
function logMessage(message: string): void {
    console.log(message);
}

// Never для функцій, які ніколи не повертають управління
function throwError(message: string): never {
    throw new Error(message);
}

function infiniteLoop(): never {
    while (true) {}
}

// Null та undefined мають власні типи
let u: undefined = undefined;
let n: null = null;
```

### Інтерфейси та типи: моделювання складних структур

Інтерфейси та типи дозволяють описувати форму об'єктів, створюючи контракти для структур даних у програмі.

```typescript
// Інтерфейс описує структуру об'єкта
interface User {
    id: number;
    username: string;
    email: string;
    firstName?: string; // Необов'язкова властивість
    lastName?: string;
    readonly createdAt: Date; // Тільки для читання
}

// Використання інтерфейсу
const user: User = {
    id: 1,
    username: "ivan.petrenko",
    email: "ivan@example.com",
    createdAt: new Date()
};

// user.createdAt = new Date(); // Помилка: властивість тільки для читання

// Розширення інтерфейсів
interface AdminUser extends User {
    role: "admin" | "superadmin";
    permissions: string[];
}

const admin: AdminUser = {
    id: 2,
    username: "admin",
    email: "admin@example.com",
    createdAt: new Date(),
    role: "admin",
    permissions: ["read", "write", "delete"]
};

// Type aliases надають альтернативний спосіб опису типів
type Point = {
    x: number;
    y: number;
};

// Union types дозволяють значенню бути одним із кількох типів
type StringOrNumber = string | number;

function printValue(value: StringOrNumber): void {
    console.log(value);
}

// Intersection types комбінують кілька типів
type Timestamped = {
    createdAt: Date;
    updatedAt: Date;
};

type NamedEntity = {
    name: string;
};

type TimestampedEntity = NamedEntity & Timestamped;

const entity: TimestampedEntity = {
    name: "Документ",
    createdAt: new Date(),
    updatedAt: new Date()
};
```

### Різниця між interface та type

Інтерфейси та типи мають багато спільного, але існують важливі відмінності, які впливають на вибір між ними.

```typescript
// Інтерфейси можна розширювати через declaration merging
interface Window {
    customProperty: string;
}

interface Window {
    anotherProperty: number;
}

// Тепер Window має обидві властивості

// Type aliases не підтримують declaration merging
type MyType = {
    prop1: string;
};

// type MyType = {
//     prop2: number;
// }; // Помилка: дублікат ідентифікатора

// Type aliases можуть представляти primitive, union, tuple типи
type ID = string | number;
type Coordinate = [number, number];

// Інтерфейси краще підходять для об'єктноорієнтованого програмування
interface Shape {
    area(): number;
}

class Circle implements Shape {
    constructor(private radius: number) {}

    area(): number {
        return Math.PI * this.radius ** 2;
    }
}
```

### Функції у TypeScript

TypeScript додає потужну систему типізації для функцій, що дозволяє точно описувати їхні сигнатури.

```typescript
// Типізація параметрів та значення, що повертається
function add(a: number, b: number): number {
    return a + b;
}

// Необов'язкові параметри
function buildName(firstName: string, lastName?: string): string {
    if (lastName) {
        return `${firstName} ${lastName}`;
    }
    return firstName;
}

// Параметри за замовчуванням
function greet(name: string, greeting: string = "Привіт"): string {
    return `${greeting}, ${name}!`;
}

// Rest параметри
function sum(...numbers: number[]): number {
    return numbers.reduce((total, num) => total + num, 0);
}

// Function type expressions
type MathOperation = (a: number, b: number) => number;

const multiply: MathOperation = (a, b) => a * b;
const divide: MathOperation = (a, b) => a / b;

// Сигнатури викликів для функцій з властивостями
type DescribableFunction = {
    description: string;
    (someArg: number): boolean;
};

function doSomething(fn: DescribableFunction): void {
    console.log(fn.description + " returned " + fn(6));
}

// Перевантаження функцій
function processValue(value: string): string;
function processValue(value: number): number;
function processValue(value: string | number): string | number {
    if (typeof value === "string") {
        return value.toUpperCase();
    }
    return value * 2;
}

const strResult = processValue("hello"); // string
const numResult = processValue(42); // number
```

## Типізація у Node.js проєктах

### Налаштування TypeScript для Node.js

Node.js проєкти мають специфічні вимоги до конфігурації TypeScript, пов'язані з системою модулів, версією ECMAScript та типами Node.js API.

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "lib": ["ES2020"],
    "moduleResolution": "node",
    "rootDir": "./src",
    "outDir": "./dist",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "resolveJsonModule": true,
    "types": ["node"]
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "**/*.spec.ts"]
}
```

Для роботи з Node.js API необхідно встановити типи.

```bash
npm install --save-dev @types/node
```

### Типізація Express застосунків

Express.js є найпопулярнішим фреймворком для створення вебзастосунків на Node.js. TypeScript значно покращує досвід розробки з Express, надаючи типобезпеку для запитів, відповідей та middleware.

```typescript
import express, { Request, Response, NextFunction } from 'express';

const app = express();

// Типізація базових обробників
app.get('/api/users', (req: Request, res: Response) => {
    res.json({ users: [] });
});

// Розширення типів Request для додавання кастомних властивостей
interface AuthRequest extends Request {
    user?: {
        id: number;
        email: string;
        role: string;
    };
}

// Middleware з типізацією
const authMiddleware = (
    req: AuthRequest,
    res: Response,
    next: NextFunction
): void => {
    const token = req.headers.authorization;

    if (!token) {
        res.status(401).json({ error: 'Unauthorized' });
        return;
    }

    // Логіка перевірки токену
    req.user = {
        id: 1,
        email: 'user@example.com',
        role: 'user'
    };

    next();
};

// Використання типізованого middleware
app.get('/api/profile', authMiddleware, (req: AuthRequest, res: Response) => {
    if (!req.user) {
        res.status(401).json({ error: 'Unauthorized' });
        return;
    }

    res.json({ user: req.user });
});

// Типізація параметрів маршрутів
interface UserParams {
    userId: string;
}

app.get('/api/users/:userId', (
    req: Request<UserParams>,
    res: Response
) => {
    const userId = parseInt(req.params.userId);
    res.json({ userId });
});

// Типізація body запиту
interface CreateUserBody {
    email: string;
    password: string;
    firstName: string;
    lastName: string;
}

app.post('/api/users', (
    req: Request<{}, {}, CreateUserBody>,
    res: Response
) => {
    const { email, password, firstName, lastName } = req.body;
    // Логіка створення користувача
    res.status(201).json({ message: 'User created' });
});

// Типізація query параметрів
interface SearchQuery {
    q?: string;
    page?: string;
    limit?: string;
}

app.get('/api/search', (
    req: Request<{}, {}, {}, SearchQuery>,
    res: Response
) => {
    const { q, page = '1', limit = '10' } = req.query;
    const pageNum = parseInt(page);
    const limitNum = parseInt(limit);

    res.json({
        query: q,
        page: pageNum,
        limit: limitNum,
        results: []
    });
});
```

### Організація типів у великих проєктах

У великих проєктах важливо організувати типи так, щоб їх було легко знайти, використовувати повторно та підтримувати.

```typescript
// src/types/user.types.ts
export interface User {
    id: number;
    email: string;
    firstName: string;
    lastName: string;
    role: UserRole;
    createdAt: Date;
    updatedAt: Date;
}

export enum UserRole {
    Admin = 'admin',
    User = 'user',
    Moderator = 'moderator'
}

export interface CreateUserDTO {
    email: string;
    password: string;
    firstName: string;
    lastName: string;
}

export interface UpdateUserDTO {
    firstName?: string;
    lastName?: string;
    email?: string;
}

export interface UserResponse {
    id: number;
    email: string;
    firstName: string;
    lastName: string;
    role: UserRole;
}

// src/types/api.types.ts
export interface ApiResponse<T> {
    success: boolean;
    data?: T;
    error?: string;
    message?: string;
}

export interface PaginatedResponse<T> {
    items: T[];
    total: number;
    page: number;
    limit: number;
    totalPages: number;
}

export interface ApiError {
    statusCode: number;
    message: string;
    errors?: Record<string, string[]>;
}

// src/services/user.service.ts
import { User, CreateUserDTO, UpdateUserDTO, UserResponse } from '../types/user.types';
import { ApiResponse, PaginatedResponse } from '../types/api.types';

export class UserService {
    async createUser(data: CreateUserDTO): Promise<ApiResponse<UserResponse>> {
        // Логіка створення користувача
        return {
            success: true,
            data: {
                id: 1,
                email: data.email,
                firstName: data.firstName,
                lastName: data.lastName,
                role: UserRole.User
            }
        };
    }

    async getUserById(id: number): Promise<ApiResponse<User | null>> {
        // Логіка отримання користувача
        return {
            success: true,
            data: null
        };
    }

    async getUsers(
        page: number,
        limit: number
    ): Promise<ApiResponse<PaginatedResponse<UserResponse>>> {
        // Логіка отримання списку користувачів
        return {
            success: true,
            data: {
                items: [],
                total: 0,
                page,
                limit,
                totalPages: 0
            }
        };
    }

    async updateUser(
        id: number,
        data: UpdateUserDTO
    ): Promise<ApiResponse<UserResponse>> {
        // Логіка оновлення користувача
        return {
            success: true,
            data: {
                id,
                email: 'updated@example.com',
                firstName: data.firstName || 'Default',
                lastName: data.lastName || 'User',
                role: UserRole.User
            }
        };
    }

    async deleteUser(id: number): Promise<ApiResponse<void>> {
        // Логіка видалення користувача
        return {
            success: true
        };
    }
}
```

### Типізація роботи з базами даних

При роботі з базами даних типізація допомагає забезпечити відповідність між моделями даних та структурою бази.

```typescript
// Типізація з Prisma
import { PrismaClient, User, Post } from '@prisma/client';

const prisma = new PrismaClient();

// Prisma автоматично генерує типи на основі схеми
async function getUserWithPosts(userId: number): Promise<User & { posts: Post[] } | null> {
    return await prisma.user.findUnique({
        where: { id: userId },
        include: { posts: true }
    });
}

// Типізація для часткових вибірок
type UserWithEmail = Pick<User, 'id' | 'email'>;

async function getUserEmails(): Promise<UserWithEmail[]> {
    return await prisma.user.findMany({
        select: {
            id: true,
            email: true
        }
    });
}

// Типізація для створення записів
type CreateUserInput = Omit<User, 'id' | 'createdAt' | 'updatedAt'>;

async function createUser(data: CreateUserInput): Promise<User> {
    return await prisma.user.create({
        data
    });
}
```

## React з TypeScript: патерни та найкращі практики

### Налаштування React проєкту з TypeScript

Сучасні інструменти створення React застосунків підтримують TypeScript з коробки.

```bash
# Створення нового проєкту з Vite
npm create vite@latest my-app -- --template react-ts

# Або з Create React App
npx create-react-app my-app --template typescript
```

### Типізація функціональних компонентів

React з TypeScript надає спеціальні типи для компонентів, пропсів та станів.

```typescript
import React, { FC, ReactNode } from 'react';

// Базовий функціональний компонент з типізованими пропсами
interface ButtonProps {
    children: ReactNode;
    onClick: () => void;
    variant?: 'primary' | 'secondary' | 'danger';
    disabled?: boolean;
    className?: string;
}

export const Button: FC<ButtonProps> = ({
    children,
    onClick,
    variant = 'primary',
    disabled = false,
    className = ''
}) => {
    return (
        <button
            onClick={onClick}
            disabled={disabled}
            className={`btn btn-${variant} ${className}`}
        >
            {children}
        </button>
    );
};

// Альтернативний спосіб без FC
export function ButtonAlt({
    children,
    onClick,
    variant = 'primary',
    disabled = false
}: ButtonProps) {
    return (
        <button
            onClick={onClick}
            disabled={disabled}
            className={`btn btn-${variant}`}
        >
            {children}
        </button>
    );
}
```

### Типізація хуків

React хуки отримують потужну типізацію в TypeScript, що робить роботу зі станом та ефектами більш безпечною.

```typescript
import { useState, useEffect, useCallback, useMemo, useRef } from 'react';

// useState з явною типізацією
function Counter() {
    const [count, setCount] = useState<number>(0);
    const [user, setUser] = useState<User | null>(null);

    // TypeScript автоматично визначає тип на основі початкового значення
    const [text, setText] = useState(''); // string
    const [isLoading, setIsLoading] = useState(false); // boolean

    return <div>{count}</div>;
}

// useEffect з типізацією
function UserProfile({ userId }: { userId: number }) {
    const [user, setUser] = useState<User | null>(null);
    const [loading, setLoading] = useState(true);
    const [error, setError] = useState<Error | null>(null);

    useEffect(() => {
        let cancelled = false;

        async function fetchUser() {
            try {
                setLoading(true);
                const response = await fetch(`/api/users/${userId}`);
                const data = await response.json();

                if (!cancelled) {
                    setUser(data);
                }
            } catch (err) {
                if (!cancelled) {
                    setError(err instanceof Error ? err : new Error('Unknown error'));
                }
            } finally {
                if (!cancelled) {
                    setLoading(false);
                }
            }
        }

        fetchUser();

        return () => {
            cancelled = true;
        };
    }, [userId]);

    if (loading) return <div>Завантаження...</div>;
    if (error) return <div>Помилка: {error.message}</div>;
    if (!user) return <div>Користувача не знайдено</div>;

    return <div>{user.firstName} {user.lastName}</div>;
}

// useCallback з типізацією
interface FormProps {
    onSubmit: (data: FormData) => void;
}

interface FormData {
    email: string;
    password: string;
}

function LoginForm({ onSubmit }: FormProps) {
    const [formData, setFormData] = useState<FormData>({
        email: '',
        password: ''
    });

    const handleSubmit = useCallback((e: React.FormEvent) => {
        e.preventDefault();
        onSubmit(formData);
    }, [formData, onSubmit]);

    const handleChange = useCallback((field: keyof FormData) => {
        return (e: React.ChangeEvent<HTMLInputElement>) => {
            setFormData(prev => ({
                ...prev,
                [field]: e.target.value
            }));
        };
    }, []);

    return (
        <form onSubmit={handleSubmit}>
            <input
                type="email"
                value={formData.email}
                onChange={handleChange('email')}
            />
            <input
                type="password"
                value={formData.password}
                onChange={handleChange('password')}
            />
            <button type="submit">Увійти</button>
        </form>
    );
}

// useMemo з типізацією
function ExpensiveComponent({ items }: { items: number[] }) {
    const sortedItems = useMemo<number[]>(() => {
        return [...items].sort((a, b) => a - b);
    }, [items]);

    const statistics = useMemo(() => {
        const sum = sortedItems.reduce((acc, val) => acc + val, 0);
        const avg = sum / sortedItems.length;

        return {
            sum,
            avg,
            min: sortedItems[0],
            max: sortedItems[sortedItems.length - 1]
        };
    }, [sortedItems]);

    return (
        <div>
            <p>Сума: {statistics.sum}</p>
            <p>Середнє: {statistics.avg}</p>
        </div>
    );
}

// useRef з типізацією
function FocusableInput() {
    const inputRef = useRef<HTMLInputElement>(null);

    const handleFocus = () => {
        inputRef.current?.focus();
    };

    return (
        <div>
            <input ref={inputRef} type="text" />
            <button onClick={handleFocus}>Фокус на інпуті</button>
        </div>
    );
}
```

### Типізація подій

React події мають специфічні типи, які забезпечують типобезпеку при обробці взаємодій користувача.

```typescript
import React, { ChangeEvent, MouseEvent, FormEvent, KeyboardEvent } from 'react';

function EventHandlers() {
    // Обробка кліків
    const handleClick = (e: MouseEvent<HTMLButtonElement>) => {
        console.log('Button clicked:', e.currentTarget.value);
    };

    // Обробка зміни input
    const handleChange = (e: ChangeEvent<HTMLInputElement>) => {
        console.log('Input changed:', e.target.value);
    };

    // Обробка зміни textarea
    const handleTextareaChange = (e: ChangeEvent<HTMLTextAreaElement>) => {
        console.log('Textarea changed:', e.target.value);
    };

    // Обробка зміни select
    const handleSelectChange = (e: ChangeEvent<HTMLSelectElement>) => {
        console.log('Select changed:', e.target.value);
    };

    // Обробка submit форми
    const handleSubmit = (e: FormEvent<HTMLFormElement>) => {
        e.preventDefault();
        const formData = new FormData(e.currentTarget);
        console.log('Form submitted:', Object.fromEntries(formData));
    };

    // Обробка натискання клавіш
    const handleKeyDown = (e: KeyboardEvent<HTMLInputElement>) => {
        if (e.key === 'Enter') {
            console.log('Enter pressed');
        }
    };

    return (
        <form onSubmit={handleSubmit}>
            <input onChange={handleChange} onKeyDown={handleKeyDown} />
            <textarea onChange={handleTextareaChange} />
            <select onChange={handleSelectChange}>
                <option value="1">Опція 1</option>
            </select>
            <button onClick={handleClick}>Надіслати</button>
        </form>
    );
}
```

### Контекст з TypeScript

Context API в React з TypeScript вимагає ретельної типізації для забезпечення типобезпеки в усьому дереві компонентів.

```typescript
import React, { createContext, useContext, useState, ReactNode } from 'react';

// Типізація стану автентифікації
interface AuthUser {
    id: number;
    email: string;
    firstName: string;
    lastName: string;
    role: string;
}

interface AuthContextType {
    user: AuthUser | null;
    isAuthenticated: boolean;
    login: (email: string, password: string) => Promise<void>;
    logout: () => void;
    updateUser: (updates: Partial<AuthUser>) => void;
}

// Створення контексту з типом
const AuthContext = createContext<AuthContextType | undefined>(undefined);

// Provider з типізацією
interface AuthProviderProps {
    children: ReactNode;
}

export function AuthProvider({ children }: AuthProviderProps) {
    const [user, setUser] = useState<AuthUser | null>(null);

    const login = async (email: string, password: string): Promise<void> => {
        try {
            const response = await fetch('/api/auth/login', {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify({ email, password })
            });

            if (!response.ok) {
                throw new Error('Login failed');
            }

            const userData = await response.json();
            setUser(userData);
        } catch (error) {
            console.error('Login error:', error);
            throw error;
        }
    };

    const logout = () => {
        setUser(null);
    };

    const updateUser = (updates: Partial<AuthUser>) => {
        if (user) {
            setUser({ ...user, ...updates });
        }
    };

    const value: AuthContextType = {
        user,
        isAuthenticated: user !== null,
        login,
        logout,
        updateUser
    };

    return <AuthContext.Provider value={value}>{children}</AuthContext.Provider>;
}

// Хук для використання контексту з перевіркою типів
export function useAuth(): AuthContextType {
    const context = useContext(AuthContext);

    if (context === undefined) {
        throw new Error('useAuth must be used within AuthProvider');
    }

    return context;
}

// Використання типізованого контексту
function UserProfile() {
    const { user, logout, updateUser } = useAuth();

    if (!user) {
        return <div>Please log in</div>;
    }

    return (
        <div>
            <h1>{user.firstName} {user.lastName}</h1>
            <p>{user.email}</p>
            <button onClick={logout}>Вийти</button>
        </div>
    );
}
```

## Generic типи та utility types

### Розуміння Generic типів

Generic типи дозволяють створювати компоненти, функції та класи, які можуть працювати з різними типами даних, зберігаючи при цьому типобезпеку.

```typescript
// Базовий generic function
function identity<T>(arg: T): T {
    return arg;
}

const numberIdentity = identity<number>(42); // number
const stringIdentity = identity<string>("hello"); // string
const autoInferred = identity(true); // boolean (тип визначений автоматично)

// Generic з обмеженнями
interface Lengthwise {
    length: number;
}

function logLength<T extends Lengthwise>(arg: T): T {
    console.log(arg.length);
    return arg;
}

logLength("hello"); // Працює, рядки мають length
logLength([1, 2, 3]); // Працює, масиви мають length
logLength({ length: 10, value: 3 }); // Працює, об'єкт має length
// logLength(42); // Помилка: number не має властивості length

// Generic з кількома типовими параметрами
function pair<K, V>(key: K, value: V): [K, V] {
    return [key, value];
}

const stringNumberPair = pair<string, number>("age", 25);
const autoInferredPair = pair("name", "Ivan"); // [string, string]

// Generic класи
class DataStore<T> {
    private items: T[] = [];

    add(item: T): void {
        this.items.push(item);
    }

    get(index: number): T | undefined {
        return this.items[index];
    }

    getAll(): T[] {
        return [...this.items];
    }

    remove(index: number): T | undefined {
        return this.items.splice(index, 1)[0];
    }

    filter(predicate: (item: T) => boolean): T[] {
        return this.items.filter(predicate);
    }
}

const numberStore = new DataStore<number>();
numberStore.add(1);
numberStore.add(2);

const userStore = new DataStore<User>();
userStore.add({ id: 1, email: 'user@example.com', firstName: 'Ivan', lastName: 'Petrenko', role: UserRole.User, createdAt: new Date(), updatedAt: new Date() });

// Generic інтерфейси
interface ApiResponse<T> {
    data: T;
    status: number;
    message: string;
}

interface PaginatedData<T> {
    items: T[];
    total: number;
    page: number;
    pageSize: number;
}

async function fetchUsers(): Promise<ApiResponse<PaginatedData<User>>> {
    const response = await fetch('/api/users');
    return response.json();
}
```

### Вбудовані utility types

TypeScript надає набір utility types, які дозволяють трансформувати існуючі типи.

```typescript
interface User {
    id: number;
    email: string;
    firstName: string;
    lastName: string;
    age: number;
    role: string;
}

// Partial - робить всі властивості необов'язковими
type PartialUser = Partial<User>;
// Еквівалентно:
// {
//   id?: number;
//   email?: string;
//   firstName?: string;
//   ...
// }

function updateUser(id: number, updates: Partial<User>): void {
    // Можна передати лише ті поля, які потрібно оновити
}

// Required - робить всі властивості обов'язковими
type RequiredUser = Required<PartialUser>;

// Readonly - робить всі властивості тільки для читання
type ReadonlyUser = Readonly<User>;

const user: ReadonlyUser = {
    id: 1,
    email: 'user@example.com',
    firstName: 'Ivan',
    lastName: 'Petrenko',
    age: 25,
    role: 'user'
};

// user.email = 'new@example.com'; // Помилка

// Pick - вибирає підмножину властивостей
type UserPreview = Pick<User, 'id' | 'email' | 'firstName' | 'lastName'>;
// {
//   id: number;
//   email: string;
//   firstName: string;
//   lastName: string;
// }

// Omit - виключає властивості
type UserWithoutId = Omit<User, 'id'>;
type PublicUser = Omit<User, 'email' | 'age'>;

// Record - створює тип з набором властивостей певного типу
type UserRoles = 'admin' | 'user' | 'moderator';
type RolePermissions = Record<UserRoles, string[]>;

const permissions: RolePermissions = {
    admin: ['read', 'write', 'delete'],
    user: ['read'],
    moderator: ['read', 'write']
};

// Exclude - виключає типи з union
type AllRoles = 'admin' | 'user' | 'moderator' | 'guest';
type AuthenticatedRoles = Exclude<AllRoles, 'guest'>; // 'admin' | 'user' | 'moderator'

// Extract - залишає тільки ті типи, які є в обох union
type AdminOrModerator = Extract<AllRoles, 'admin' | 'moderator'>; // 'admin' | 'moderator'

// NonNullable - виключає null та undefined
type MaybeString = string | null | undefined;
type DefiniteString = NonNullable<MaybeString>; // string

// ReturnType - отримує тип значення, що повертається функцією
function createUser() {
    return {
        id: 1,
        email: 'user@example.com'
    };
}

type CreatedUser = ReturnType<typeof createUser>;
// {
//   id: number;
//   email: string;
// }

// Parameters - отримує типи параметрів функції
function updateProfile(userId: number, data: Partial<User>): void {}

type UpdateProfileParams = Parameters<typeof updateProfile>;
// [number, Partial<User>]

// Комбінування utility types
type CreateUserDTO = Omit<User, 'id' | 'createdAt' | 'updatedAt'>;
type UpdateUserDTO = Partial<CreateUserDTO>;
type UserResponse = Readonly<Pick<User, 'id' | 'email' | 'firstName' | 'lastName'>>;
```

### Створення власних utility types

```typescript
// Deep Partial - рекурсивно робить всі властивості необов'язковими
type DeepPartial<T> = {
    [P in keyof T]?: T[P] extends object ? DeepPartial<T[P]> : T[P];
};

interface ComplexUser {
    id: number;
    profile: {
        firstName: string;
        lastName: string;
        address: {
            street: string;
            city: string;
        };
    };
}

type PartialComplexUser = DeepPartial<ComplexUser>;
// Всі вкладені властивості також стають необов'язковими

// Nullable - додає null до типу
type Nullable<T> = T | null;

type NullableUser = Nullable<User>;
// User | null

// DeepReadonly - рекурсивно робить всі властивості readonly
type DeepReadonly<T> = {
    readonly [P in keyof T]: T[P] extends object ? DeepReadonly<T[P]> : T[P];
};

// ValueOf - отримує union типів всіх значень об'єкта
type ValueOf<T> = T[keyof T];

type UserFieldTypes = ValueOf<User>;
// number | string (типи всіх властивостей User)

// PromiseType - витягує тип з Promise
type PromiseType<T> = T extends Promise<infer U> ? U : T;

type UserPromise = Promise<User>;
type ExtractedUser = PromiseType<UserPromise>; // User

// Функція з generic обмеженнями
function pluck<T, K extends keyof T>(items: T[], key: K): T[K][] {
    return items.map(item => item[key]);
}

const users: User[] = [
    { id: 1, email: 'user1@example.com', firstName: 'Ivan', lastName: 'Petrenko', age: 25, role: 'user' },
    { id: 2, email: 'user2@example.com', firstName: 'Olena', lastName: 'Shevchenko', age: 30, role: 'admin' }
];

const emails = pluck(users, 'email'); // string[]
const ages = pluck(users, 'age'); // number[]
// const invalid = pluck(users, 'invalid'); // Помилка компіляції
```

## Типізація API responses

### Створення типобезпечних API клієнтів

При роботі з API важливо забезпечити відповідність між типами на frontend та backend.

```typescript
// Типи для API відповідей
interface ApiResponse<T> {
    success: boolean;
    data?: T;
    error?: ApiError;
    message?: string;
}

interface ApiError {
    code: string;
    message: string;
    details?: Record<string, string[]>;
}

interface PaginatedResponse<T> {
    items: T[];
    total: number;
    page: number;
    pageSize: number;
    totalPages: number;
}

// Базовий API клієнт
class ApiClient {
    private baseUrl: string;

    constructor(baseUrl: string = '/api') {
        this.baseUrl = baseUrl;
    }

    private async request<T>(
        endpoint: string,
        options?: RequestInit
    ): Promise<ApiResponse<T>> {
        try {
            const response = await fetch(`${this.baseUrl}${endpoint}`, {
                headers: {
                    'Content-Type': 'application/json',
                    ...options?.headers
                },
                ...options
            });

            const data = await response.json();

            if (!response.ok) {
                return {
                    success: false,
                    error: data
                };
            }

            return {
                success: true,
                data
            };
        } catch (error) {
            return {
                success: false,
                error: {
                    code: 'NETWORK_ERROR',
                    message: error instanceof Error ? error.message : 'Unknown error'
                }
            };
        }
    }

    async get<T>(endpoint: string): Promise<ApiResponse<T>> {
        return this.request<T>(endpoint, { method: 'GET' });
    }

    async post<T, D = unknown>(
        endpoint: string,
        data: D
    ): Promise<ApiResponse<T>> {
        return this.request<T>(endpoint, {
            method: 'POST',
            body: JSON.stringify(data)
        });
    }

    async put<T, D = unknown>(
        endpoint: string,
        data: D
    ): Promise<ApiResponse<T>> {
        return this.request<T>(endpoint, {
            method: 'PUT',
            body: JSON.stringify(data)
        });
    }

    async delete<T>(endpoint: string): Promise<ApiResponse<T>> {
        return this.request<T>(endpoint, { method: 'DELETE' });
    }
}

// Типізовані ендпоінти для користувачів
class UserApi {
    private client: ApiClient;

    constructor(client: ApiClient) {
        this.client = client;
    }

    async getUsers(
        page: number = 1,
        pageSize: number = 10
    ): Promise<ApiResponse<PaginatedResponse<User>>> {
        return this.client.get<PaginatedResponse<User>>(
            `/users?page=${page}&pageSize=${pageSize}`
        );
    }

    async getUserById(id: number): Promise<ApiResponse<User>> {
        return this.client.get<User>(`/users/${id}`);
    }

    async createUser(data: CreateUserDTO): Promise<ApiResponse<User>> {
        return this.client.post<User, CreateUserDTO>('/users', data);
    }

    async updateUser(
        id: number,
        data: UpdateUserDTO
    ): Promise<ApiResponse<User>> {
        return this.client.put<User, UpdateUserDTO>(`/users/${id}`, data);
    }

    async deleteUser(id: number): Promise<ApiResponse<void>> {
        return this.client.delete<void>(`/users/${id}`);
    }
}

// Використання типізованого API клієнта
async function loadUserData() {
    const apiClient = new ApiClient();
    const userApi = new UserApi(apiClient);

    const response = await userApi.getUserById(1);

    if (response.success && response.data) {
        const user = response.data; // TypeScript знає, що це User
        console.log(`${user.firstName} ${user.lastName}`);
    } else if (response.error) {
        console.error('Error:', response.error.message);
    }
}
```

### Валідація та типові гарди

TypeScript не перевіряє типи під час виконання, тому важливо додати валідацію для даних, отриманих з API.

```typescript
// Type guards для валідації
function isUser(value: unknown): value is User {
    return (
        typeof value === 'object' &&
        value !== null &&
        'id' in value &&
        typeof (value as any).id === 'number' &&
        'email' in value &&
        typeof (value as any).email === 'string' &&
        'firstName' in value &&
        typeof (value as any).firstName === 'string' &&
        'lastName' in value &&
        typeof (value as any).lastName === 'string'
    );
}

function isArrayOfUsers(value: unknown): value is User[] {
    return Array.isArray(value) && value.every(isUser);
}

// Використання type guard
async function fetchUserSafely(id: number): Promise<User | null> {
    const response = await fetch(`/api/users/${id}`);
    const data = await response.json();

    if (isUser(data)) {
        return data;
    }

    console.error('Invalid user data received');
    return null;
}

// Валідація з Zod
import { z } from 'zod';

const UserSchema = z.object({
    id: z.number(),
    email: z.string().email(),
    firstName: z.string(),
    lastName: z.string(),
    age: z.number().min(0).max(150),
    role: z.enum(['admin', 'user', 'moderator'])
});

type UserFromSchema = z.infer<typeof UserSchema>;

async function fetchUserWithValidation(id: number): Promise<User | null> {
    const response = await fetch(`/api/users/${id}`);
    const data = await response.json();

    try {
        const validatedUser = UserSchema.parse(data);
        return validatedUser;
    } catch (error) {
        if (error instanceof z.ZodError) {
            console.error('Validation errors:', error.errors);
        }
        return null;
    }
}
```

## Migration стратегії: від JavaScript до TypeScript

### Поступове впровадження TypeScript

Міграція великого JavaScript проєкту на TypeScript може здаватися складною, але правильна стратегія робить процес керованим та безпечним.

```json
// tsconfig.json для поступової міграції
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "lib": ["ES2020"],
    "allowJs": true,
    "checkJs": false,
    "strict": false,
    "noImplicitAny": false,
    "esModuleInterop": true,
    "skipLibCheck": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules"]
}
```

Стратегія міграції.

**Крок 1**: встановіть TypeScript та налаштуйте базову конфігурацію з allowJs: true. Це дозволить TypeScript та JavaScript файлам співіснувати в одному проєкті.

**Крок 2**: перейменуйте файли поступово з .js на .ts, починаючи з найпростіших модулів. Додавайте типи там, де TypeScript скаржиться на помилки.

**Крок 3**: поступово збільшуйте строгість перевірок, вмикаючи опції strict по одній. Починайте з noImplicitAny, потім strictNullChecks, і так далі.

**Крок 4**: додайте типи для зовнішніх бібліотек через @types пакети або створіть власні declaration файли.

**Крок 5**: рефакторинг коду для використання TypeScript можливостей, таких як enum, interface, та generic типи.

### Практичний приклад міграції

```typescript
// До міграції (JavaScript)
// user.service.js
class UserService {
    constructor(apiClient) {
        this.apiClient = apiClient;
    }

    async getUser(id) {
        const response = await this.apiClient.get(`/users/${id}`);
        return response.data;
    }

    async createUser(userData) {
        const response = await this.apiClient.post('/users', userData);
        return response.data;
    }
}

// Після міграції (TypeScript)
// user.service.ts
import { ApiClient } from './api-client';
import { User, CreateUserDTO, ApiResponse } from './types';

export class UserService {
    constructor(private apiClient: ApiClient) {}

    async getUser(id: number): Promise<User> {
        const response = await this.apiClient.get<User>(`/users/${id}`);
        if (!response.success || !response.data) {
            throw new Error('Failed to fetch user');
        }
        return response.data;
    }

    async createUser(userData: CreateUserDTO): Promise<User> {
        const response = await this.apiClient.post<User, CreateUserDTO>(
            '/users',
            userData
        );
        if (!response.success || !response.data) {
            throw new Error('Failed to create user');
        }
        return response.data;
    }
}
```

### Робота з існуючими бібліотеками без типів

```typescript
// Створення declaration файлу для бібліотеки без типів
// types/legacy-library.d.ts
declare module 'legacy-library' {
    export interface Config {
        apiKey: string;
        endpoint: string;
    }

    export function initialize(config: Config): void;
    export function fetchData(id: string): Promise<any>;
}

// Використання
import { initialize, fetchData } from 'legacy-library';

initialize({
    apiKey: 'key',
    endpoint: 'https://api.example.com'
});
```

## Висновки та найкращі практики

### Ключові принципи роботи з TypeScript

TypeScript не просто додає типи до JavaScript — він змінює підхід до розробки, роблячи код більш надійним та підтримуваним. Розуміння основних принципів TypeScript допомагає писати якісніший код та уникати типових помилок.

**Використовуйте строгі налаштування**: вмикайте strict режим у tsconfig.json для максимальної типобезпеки. Це може здаватися складним спочатку, але значно зменшує кількість помилок під час виконання.

**Віддавайте перевагу типовому виведенню**: TypeScript має потужну систему виведення типів. Не додавайте типи там, де вони очевидні з контексту. Код стає чистішим та легшим для читання.

**Використовуйте interface для об'єктів та type для union**: хоча interface та type часто взаємозамінні, є усталена практика використовувати interface для опису форми об'єктів та type для створення union типів та алгоритмічних типів.

**Створюйте власні type guards**: для валідації даних під час виконання створюйте функції type guards, які перевіряють структуру даних та повідомляють TypeScript про тип.

**Уникайте надмірного використання any**: тип any вимикає перевірку типів. Використовуйте unknown замість any, коли тип невідомий, та додайте перевірки перед використанням.

**Організовуйте типи в окремих файлах**: у великих проєктах тримайте типи в окремих файлах з суфіксом .types.ts для легшого знаходження та використання.

**Використовуйте utility types**: TypeScript надає багато вбудованих utility types, як Partial, Pick, Omit. Використовуйте їх замість створення дублікатів типів.

**Документуйте складні типи**: додавайте коментарі до складних типів, особливо generic типів з обмеженнями, щоб інші розробники могли зрозуміти їхню мету.

### TypeScript у командній розробці

TypeScript особливо цінний у командній розробці, де він служить живою документацією та допомагає підтримувати консистентність коду між різними членами команди.

**Узгодьте стиль кодування**: використовуйте ESLint з TypeScript плагіном для забезпечення консистентного стилю коду в команді.

**Проводьте code review**: звертайте увагу на якість типів під час code review. Погано типізований TypeScript код може бути гірший за JavaScript.

**Навчайте нових членів команди**: TypeScript має крутішу криву навчання порівняно з JavaScript. Виділяйте час на навчання нових членів команди.

**Підтримуйте типи актуальними**: при зміні API або структур даних, оновлюйте відповідні типи негайно.

### Майбутнє TypeScript у веброзробці

TypeScript продовжує еволюціонувати, додаючи нові можливості, які роблять розробку ще більш безпечною та зручною. Знання TypeScript стає стандартом для професійних веброзробників, особливо при роботі з великими проєктами та в командах.

Розуміння TypeScript, його можливостей та обмежень є критично важливим для сучасної веброзробки. Інвестиція часу в навчання TypeScript окупається через зменшення кількості помилок, покращення підтримуваності коду та підвищення продуктивності розробки.
