# Лекція 16. Тестування React компонентів

## Вступ до тестування frontend додатків

Тестування є невід'ємною частиною сучасної розробки програмного забезпечення. У контексті frontend розробки тестування набуває особливого значення через складність взаємодії користувача з інтерфейсом, асинхронну природу багатьох операцій та високі вимоги до якості користувацького досвіду.

React додатки, завдяки своїй компонентній архітектурі, особливо добре підходять для тестування. Кожен компонент може бути протестований ізольовано, що спрощує виявлення та виправлення помилок на ранніх етапах розробки.

Існує кілька рівнів тестування frontend додатків, кожен з яких має свою мету та особливості реалізації. Unit тести перевіряють окремі функції та компоненти в ізоляції. Integration тести перевіряють взаємодію між компонентами. E2E тести симулюють повний сценарій роботи користувача з додатком.

Правильна стратегія тестування передбачає баланс між різними типами тестів, забезпечуючи достатнє покриття коду при розумних витратах часу та ресурсів на підтримку тестів.

## React Testing Library: філософія та основи

### Концептуальні принципи

React Testing Library базується на фундаментальному принципі: тести повинні працювати з компонентами так само, як з ними працюють користувачі. Замість тестування деталей реалізації, бібліотека заохочує тестування видимої поведінки компонентів.

Цей підхід має кілька важливих переваг. По-перше, тести стають більш стійкими до рефакторингу внутрішньої структури компонентів. По-друге, тести краще відображають реальний користувацький досвід. По-третє, тести природним чином покращують доступність додатку, оскільки бібліотека заохочує пошук елементів за допомогою доступних атрибутів.

### Налаштування тестового середовища

```bash
# Встановлення необхідних пакетів
npm install --save-dev @testing-library/react @testing-library/jest-dom @testing-library/user-event vitest jsdom
```

```javascript
// vite.config.js
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

export default defineConfig({
    plugins: [react()],
    test: {
        globals: true,
        environment: 'jsdom',
        setupFiles: './src/test/setup.js',
    },
});
```

```javascript
// src/test/setup.js
import { expect, afterEach } from 'vitest';
import { cleanup } from '@testing-library/react';
import matchers from '@testing-library/jest-dom/matchers';

// Розширення expect з jest-dom matchers
expect.extend(matchers);

// Очищення після кожного тесту
afterEach(() => {
    cleanup();
});
```

### Базовий приклад тестування

```jsx
// Button.jsx
function Button({ onClick, children, variant = 'primary' }) {
    const className = variant === 'primary' 
        ? 'bg-blue-500 text-white' 
        : 'bg-gray-200 text-gray-900';
    
    return (
        <button onClick={onClick} className={className}>
            {children}
        </button>
    );
}

export default Button;
```

```javascript
// Button.test.jsx
import { describe, it, expect, vi } from 'vitest';
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import Button from './Button';

describe('Button Component', () => {
    it('рендериться з текстом', () => {
        render(<Button>Натисни мене</Button>);
        
        const button = screen.getByRole('button', { name: /натисни мене/i });
        expect(button).toBeInTheDocument();
    });

    it('викликає onClick при натисканні', async () => {
        const handleClick = vi.fn();
        const user = userEvent.setup();
        
        render(<Button onClick={handleClick}>Натисни</Button>);
        
        const button = screen.getByRole('button');
        await user.click(button);
        
        expect(handleClick).toHaveBeenCalledTimes(1);
    });

    it('застосовує правильні стилі для primary варіанту', () => {
        render(<Button variant="primary">Primary</Button>);
        
        const button = screen.getByRole('button');
        expect(button).toHaveClass('bg-blue-500', 'text-white');
    });

    it('застосовує правильні стилі для secondary варіанту', () => {
        render(<Button variant="secondary">Secondary</Button>);
        
        const button = screen.getByRole('button');
        expect(button).toHaveClass('bg-gray-200', 'text-gray-900');
    });
});
```

## Unit тестування компонентів

### Тестування простих компонентів

```jsx
// UserCard.jsx
function UserCard({ user }) {
    if (!user) {
        return <div>Користувача не знайдено</div>;
    }

    return (
        <div className="user-card">
            <img src={user.avatar} alt={`${user.name} avatar`} />
            <h2>{user.name}</h2>
            <p>{user.email}</p>
            <span className={user.isActive ? 'active' : 'inactive'}>
                {user.isActive ? 'Активний' : 'Неактивний'}
            </span>
        </div>
    );
}

export default UserCard;
```

```javascript
// UserCard.test.jsx
import { describe, it, expect } from 'vitest';
import { render, screen } from '@testing-library/react';
import UserCard from './UserCard';

describe('UserCard Component', () => {
    const mockUser = {
        name: 'Іван Петренко',
        email: 'ivan@example.com',
        avatar: '/avatar.jpg',
        isActive: true
    };

    it('відображає інформацію про користувача', () => {
        render(<UserCard user={mockUser} />);
        
        expect(screen.getByText('Іван Петренко')).toBeInTheDocument();
        expect(screen.getByText('ivan@example.com')).toBeInTheDocument();
        expect(screen.getByAltText('Іван Петренко avatar')).toBeInTheDocument();
    });

    it('показує статус активного користувача', () => {
        render(<UserCard user={mockUser} />);
        
        const status = screen.getByText('Активний');
        expect(status).toBeInTheDocument();
        expect(status).toHaveClass('active');
    });

    it('показує статус неактивного користувача', () => {
        const inactiveUser = { ...mockUser, isActive: false };
        render(<UserCard user={inactiveUser} />);
        
        const status = screen.getByText('Неактивний');
        expect(status).toBeInTheDocument();
        expect(status).toHaveClass('inactive');
    });

    it('відображає повідомлення коли user відсутній', () => {
        render(<UserCard user={null} />);
        
        expect(screen.getByText('Користувача не знайдено')).toBeInTheDocument();
    });

    it('відображає правильний src аватара', () => {
        render(<UserCard user={mockUser} />);
        
        const avatar = screen.getByAltText('Іван Петренко avatar');
        expect(avatar).toHaveAttribute('src', '/avatar.jpg');
    });
});
```

### Тестування форм

```jsx
// LoginForm.jsx
import { useState } from 'react';

function LoginForm({ onSubmit }) {
    const [email, setEmail] = useState('');
    const [password, setPassword] = useState('');
    const [errors, setErrors] = useState({});

    const validate = () => {
        const newErrors = {};
        
        if (!email) {
            newErrors.email = 'Email обов\'язковий';
        } else if (!/\S+@\S+\.\S+/.test(email)) {
            newErrors.email = 'Некоректний email';
        }
        
        if (!password) {
            newErrors.password = 'Пароль обов\'язковий';
        } else if (password.length < 6) {
            newErrors.password = 'Пароль має містити мінімум 6 символів';
        }
        
        return newErrors;
    };

    const handleSubmit = (e) => {
        e.preventDefault();
        
        const newErrors = validate();
        setErrors(newErrors);
        
        if (Object.keys(newErrors).length === 0) {
            onSubmit({ email, password });
        }
    };

    return (
        <form onSubmit={handleSubmit} aria-label="Форма входу">
            <div>
                <label htmlFor="email">Email</label>
                <input
                    id="email"
                    type="email"
                    value={email}
                    onChange={(e) => setEmail(e.target.value)}
                    aria-invalid={!!errors.email}
                    aria-describedby={errors.email ? 'email-error' : undefined}
                />
                {errors.email && (
                    <span id="email-error" role="alert">
                        {errors.email}
                    </span>
                )}
            </div>

            <div>
                <label htmlFor="password">Пароль</label>
                <input
                    id="password"
                    type="password"
                    value={password}
                    onChange={(e) => setPassword(e.target.value)}
                    aria-invalid={!!errors.password}
                    aria-describedby={errors.password ? 'password-error' : undefined}
                />
                {errors.password && (
                    <span id="password-error" role="alert">
                        {errors.password}
                    </span>
                )}
            </div>

            <button type="submit">Увійти</button>
        </form>
    );
}

export default LoginForm;
```

```javascript
// LoginForm.test.jsx
import { describe, it, expect, vi } from 'vitest';
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import LoginForm from './LoginForm';

describe('LoginForm Component', () => {
    it('рендериться з усіма полями', () => {
        render(<LoginForm onSubmit={vi.fn()} />);
        
        expect(screen.getByLabelText(/email/i)).toBeInTheDocument();
        expect(screen.getByLabelText(/пароль/i)).toBeInTheDocument();
        expect(screen.getByRole('button', { name: /увійти/i })).toBeInTheDocument();
    });

    it('дозволяє вводити email та пароль', async () => {
        const user = userEvent.setup();
        render(<LoginForm onSubmit={vi.fn()} />);
        
        const emailInput = screen.getByLabelText(/email/i);
        const passwordInput = screen.getByLabelText(/пароль/i);
        
        await user.type(emailInput, 'test@example.com');
        await user.type(passwordInput, 'password123');
        
        expect(emailInput).toHaveValue('test@example.com');
        expect(passwordInput).toHaveValue('password123');
    });

    it('показує помилку при порожньому email', async () => {
        const user = userEvent.setup();
        render(<LoginForm onSubmit={vi.fn()} />);
        
        const submitButton = screen.getByRole('button', { name: /увійти/i });
        await user.click(submitButton);
        
        expect(screen.getByText(/email обов'язковий/i)).toBeInTheDocument();
    });

    it('показує помилку при некоректному email', async () => {
        const user = userEvent.setup();
        render(<LoginForm onSubmit={vi.fn()} />);
        
        const emailInput = screen.getByLabelText(/email/i);
        await user.type(emailInput, 'invalid-email');
        
        const submitButton = screen.getByRole('button', { name: /увійти/i });
        await user.click(submitButton);
        
        expect(screen.getByText(/некоректний email/i)).toBeInTheDocument();
    });

    it('показує помилку при короткому паролі', async () => {
        const user = userEvent.setup();
        render(<LoginForm onSubmit={vi.fn()} />);
        
        const passwordInput = screen.getByLabelText(/пароль/i);
        await user.type(passwordInput, '123');
        
        const submitButton = screen.getByRole('button', { name: /увійти/i });
        await user.click(submitButton);
        
        expect(screen.getByText(/пароль має містити мінімум 6 символів/i)).toBeInTheDocument();
    });

    it('викликає onSubmit з правильними даними при валідному вводі', async () => {
        const handleSubmit = vi.fn();
        const user = userEvent.setup();
        
        render(<LoginForm onSubmit={handleSubmit} />);
        
        const emailInput = screen.getByLabelText(/email/i);
        const passwordInput = screen.getByLabelText(/пароль/i);
        
        await user.type(emailInput, 'test@example.com');
        await user.type(passwordInput, 'password123');
        
        const submitButton = screen.getByRole('button', { name: /увійти/i });
        await user.click(submitButton);
        
        expect(handleSubmit).toHaveBeenCalledWith({
            email: 'test@example.com',
            password: 'password123'
        });
    });

    it('не викликає onSubmit при невалідних даних', async () => {
        const handleSubmit = vi.fn();
        const user = userEvent.setup();
        
        render(<LoginForm onSubmit={handleSubmit} />);
        
        const submitButton = screen.getByRole('button', { name: /увійти/i });
        await user.click(submitButton);
        
        expect(handleSubmit).not.toHaveBeenCalled();
    });
});
```

## Мокування API викликів

### Створення mock функцій

```javascript
// api.js
export async function fetchUser(userId) {
    const response = await fetch(`/api/users/${userId}`);
    if (!response.ok) {
        throw new Error('Failed to fetch user');
    }
    return response.json();
}

export async function updateUser(userId, data) {
    const response = await fetch(`/api/users/${userId}`, {
        method: 'PUT',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(data)
    });
    if (!response.ok) {
        throw new Error('Failed to update user');
    }
    return response.json();
}
```

```jsx
// UserProfile.jsx
import { useState, useEffect } from 'react';
import { fetchUser, updateUser } from './api';

function UserProfile({ userId }) {
    const [user, setUser] = useState(null);
    const [isLoading, setIsLoading] = useState(true);
    const [error, setError] = useState(null);
    const [isEditing, setIsEditing] = useState(false);

    useEffect(() => {
        loadUser();
    }, [userId]);

    const loadUser = async () => {
        try {
            setIsLoading(true);
            setError(null);
            const data = await fetchUser(userId);
            setUser(data);
        } catch (err) {
            setError(err.message);
        } finally {
            setIsLoading(false);
        }
    };

    const handleUpdate = async (newData) => {
        try {
            const updated = await updateUser(userId, newData);
            setUser(updated);
            setIsEditing(false);
        } catch (err) {
            setError(err.message);
        }
    };

    if (isLoading) return <div>Завантаження...</div>;
    if (error) return <div role="alert">Помилка: {error}</div>;
    if (!user) return null;

    return (
        <div>
            <h1>{user.name}</h1>
            <p>{user.email}</p>
            {isEditing ? (
                <button onClick={() => handleUpdate({ name: 'Новий ім\'я' })}>
                    Зберегти
                </button>
            ) : (
                <button onClick={() => setIsEditing(true)}>
                    Редагувати
                </button>
            )}
        </div>
    );
}

export default UserProfile;
```

```javascript
// UserProfile.test.jsx
import { describe, it, expect, vi, beforeEach } from 'vitest';
import { render, screen, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import UserProfile from './UserProfile';
import * as api from './api';

// Мокування модуля api
vi.mock('./api');

describe('UserProfile Component', () => {
    const mockUser = {
        id: 1,
        name: 'Іван Петренко',
        email: 'ivan@example.com'
    };

    beforeEach(() => {
        vi.clearAllMocks();
    });

    it('показує loading стан під час завантаження', () => {
        api.fetchUser.mockImplementation(() => new Promise(() => {})); // Never resolves
        
        render(<UserProfile userId={1} />);
        
        expect(screen.getByText('Завантаження...')).toBeInTheDocument();
    });

    it('відображає дані користувача після завантаження', async () => {
        api.fetchUser.mockResolvedValue(mockUser);
        
        render(<UserProfile userId={1} />);
        
        await waitFor(() => {
            expect(screen.getByText('Іван Петренко')).toBeInTheDocument();
        });
        
        expect(screen.getByText('ivan@example.com')).toBeInTheDocument();
    });

    it('показує помилку при невдалому завантаженні', async () => {
        api.fetchUser.mockRejectedValue(new Error('Network error'));
        
        render(<UserProfile userId={1} />);
        
        await waitFor(() => {
            expect(screen.getByRole('alert')).toHaveTextContent('Помилка: Network error');
        });
    });

    it('викликає API з правильним userId', async () => {
        api.fetchUser.mockResolvedValue(mockUser);
        
        render(<UserProfile userId={1} />);
        
        await waitFor(() => {
            expect(api.fetchUser).toHaveBeenCalledWith(1);
        });
    });

    it('дозволяє оновлювати дані користувача', async () => {
        const updatedUser = { ...mockUser, name: 'Новий ім\'я' };
        api.fetchUser.mockResolvedValue(mockUser);
        api.updateUser.mockResolvedValue(updatedUser);
        
        const user = userEvent.setup();
        render(<UserProfile userId={1} />);
        
        await waitFor(() => {
            expect(screen.getByText('Іван Петренко')).toBeInTheDocument();
        });
        
        const editButton = screen.getByRole('button', { name: /редагувати/i });
        await user.click(editButton);
        
        const saveButton = screen.getByRole('button', { name: /зберегти/i });
        await user.click(saveButton);
        
        await waitFor(() => {
            expect(api.updateUser).toHaveBeenCalledWith(1, { name: 'Новий ім\'я' });
        });
    });
});
```

### Використання MSW для мокування HTTP запитів

```bash
npm install --save-dev msw
```

```javascript
// mocks/handlers.js
import { http, HttpResponse } from 'msw';

export const handlers = [
    http.get('/api/users/:userId', ({ params }) => {
        const { userId } = params;
        
        return HttpResponse.json({
            id: userId,
            name: 'Іван Петренко',
            email: 'ivan@example.com'
        });
    }),

    http.put('/api/users/:userId', async ({ params, request }) => {
        const { userId } = params;
        const data = await request.json();
        
        return HttpResponse.json({
            id: userId,
            ...data
        });
    }),

    http.get('/api/users', () => {
        return HttpResponse.json([
            { id: 1, name: 'Іван' },
            { id: 2, name: 'Марія' }
        ]);
    })
];
```

```javascript
// mocks/server.js
import { setupServer } from 'msw/node';
import { handlers } from './handlers';

export const server = setupServer(...handlers);
```

```javascript
// src/test/setup.js
import { beforeAll, afterEach, afterAll } from 'vitest';
import { server } from '../mocks/server';

// Запуск сервера перед всіма тестами
beforeAll(() => server.listen());

// Скидання handlers після кожного тесту
afterEach(() => server.resetHandlers());

// Закриття сервера після всіх тестів
afterAll(() => server.close());
```

## Integration тести

### Тестування взаємодії компонентів

```jsx
// TodoList.jsx
import { useState } from 'react';

function TodoList() {
    const [todos, setTodos] = useState([]);
    const [inputValue, setInputValue] = useState('');

    const addTodo = (e) => {
        e.preventDefault();
        if (inputValue.trim()) {
            setTodos([...todos, {
                id: Date.now(),
                text: inputValue,
                completed: false
            }]);
            setInputValue('');
        }
    };

    const toggleTodo = (id) => {
        setTodos(todos.map(todo =>
            todo.id === id ? { ...todo, completed: !todo.completed } : todo
        ));
    };

    const deleteTodo = (id) => {
        setTodos(todos.filter(todo => todo.id !== id));
    };

    return (
        <div>
            <h1>Список завдань</h1>
            
            <form onSubmit={addTodo}>
                <input
                    type="text"
                    value={inputValue}
                    onChange={(e) => setInputValue(e.target.value)}
                    placeholder="Додати завдання"
                    aria-label="Нове завдання"
                />
                <button type="submit">Додати</button>
            </form>

            <ul>
                {todos.map(todo => (
                    <li key={todo.id}>
                        <input
                            type="checkbox"
                            checked={todo.completed}
                            onChange={() => toggleTodo(todo.id)}
                            aria-label={`Позначити "${todo.text}" як виконане`}
                        />
                        <span className={todo.completed ? 'completed' : ''}>
                            {todo.text}
                        </span>
                        <button
                            onClick={() => deleteTodo(todo.id)}
                            aria-label={`Видалити "${todo.text}"`}
                        >
                            Видалити
                        </button>
                    </li>
                ))}
            </ul>

            {todos.length === 0 && (
                <p>Немає завдань. Додайте нове!</p>
            )}
        </div>
    );
}

export default TodoList;
```

```javascript
// TodoList.test.jsx
import { describe, it, expect } from 'vitest';
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import TodoList from './TodoList';

describe('TodoList Integration Tests', () => {
    it('дозволяє повний CRUD workflow', async () => {
        const user = userEvent.setup();
        render(<TodoList />);

        // Початковий стан
        expect(screen.getByText(/немає завдань/i)).toBeInTheDocument();

        // Створення завдання
        const input = screen.getByLabelText(/нове завдання/i);
        await user.type(input, 'Купити молоко');
        await user.click(screen.getByRole('button', { name: /додати/i }));

        expect(screen.getByText('Купити молоко')).toBeInTheDocument();
        expect(input).toHaveValue('');

        // Додавання ще одного завдання
        await user.type(input, 'Прочитати книгу');
        await user.click(screen.getByRole('button', { name: /додати/i }));

        expect(screen.getByText('Прочитати книгу')).toBeInTheDocument();

        // Позначення як виконане
        const checkbox = screen.getByLabelText(/позначити "купити молоко" як виконане/i);
        await user.click(checkbox);

        const completedText = screen.getByText('Купити молоко');
        expect(completedText).toHaveClass('completed');

        // Видалення завдання
        const deleteButton = screen.getByLabelText(/видалити "купити молоко"/i);
        await user.click(deleteButton);

        expect(screen.queryByText('Купити молоко')).not.toBeInTheDocument();
        expect(screen.getByText('Прочитати книгу')).toBeInTheDocument();
    });

    it('не додає порожні завдання', async () => {
        const user = userEvent.setup();
        render(<TodoList />);

        const input = screen.getByLabelText(/нове завдання/i);
        await user.type(input, '   ');
        await user.click(screen.getByRole('button', { name: /додати/i }));

        expect(screen.getByText(/немає завдань/i)).toBeInTheDocument();
    });

    it('очищує input після додавання завдання', async () => {
        const user = userEvent.setup();
        render(<TodoList />);

        const input = screen.getByLabelText(/нове завдання/i);
        await user.type(input, 'Тестове завдання');
        await user.click(screen.getByRole('button', { name: /додати/i }));

        expect(input).toHaveValue('');
    });
});
```

## E2E тестування з Playwright

### Налаштування Playwright

```bash
npm init playwright@latest
```

```javascript
// playwright.config.js
import { defineConfig, devices } from '@playwright/test';

export default defineConfig({
    testDir: './e2e',
    fullyParallel: true,
    forbidOnly: !!process.env.CI,
    retries: process.env.CI ? 2 : 0,
    workers: process.env.CI ? 1 : undefined,
    reporter: 'html',
    use: {
        baseURL: 'http://localhost:5173',
        trace: 'on-first-retry',
    },
    projects: [
        {
            name: 'chromium',
            use: { ...devices['Desktop Chrome'] },
        },
        {
            name: 'firefox',
            use: { ...devices['Desktop Firefox'] },
        },
        {
            name: 'webkit',
            use: { ...devices['Desktop Safari'] },
        },
    ],
    webServer: {
        command: 'npm run dev',
        url: 'http://localhost:5173',
        reuseExistingServer: !process.env.CI,
    },
});
```

### E2E тести

```javascript
// e2e/auth.spec.js
import { test, expect } from '@playwright/test';

test.describe('Authentication Flow', () => {
    test('користувач може зареєструватися', async ({ page }) => {
        await page.goto('/register');

        // Заповнення форми реєстрації
        await page.fill('input[name="name"]', 'Іван Петренко');
        await page.fill('input[name="email"]', 'ivan@example.com');
        await page.fill('input[name="password"]', 'securePassword123');
        await page.fill('input[name="confirmPassword"]', 'securePassword123');

        // Відправка форми
        await page.click('button[type="submit"]');

        // Перевірка редіректу
        await expect(page).toHaveURL('/dashboard');
        
        // Перевірка привітального повідомлення
        await expect(page.locator('text=Вітаємо, Іван Петренко!')).toBeVisible();
    });

    test('користувач може увійти', async ({ page }) => {
        await page.goto('/login');

        await page.fill('input[name="email"]', 'ivan@example.com');
        await page.fill('input[name="password"]', 'securePassword123');
        await page.click('button[type="submit"]');

        await expect(page).toHaveURL('/dashboard');
    });

    test('показує помилку при неправильних даних', async ({ page }) => {
        await page.goto('/login');

        await page.fill('input[name="email"]', 'wrong@example.com');
        await page.fill('input[name="password"]', 'wrongPassword');
        await page.click('button[type="submit"]');

        // Перевірка повідомлення про помилку
        await expect(page.locator('text=Некоректний email або пароль')).toBeVisible();
    });

    test('користувач може вийти', async ({ page }) => {
        // Спочатку входимо
        await page.goto('/login');
        await page.fill('input[name="email"]', 'ivan@example.com');
        await page.fill('input[name="password"]', 'securePassword123');
        await page.click('button[type="submit"]');

        await expect(page).toHaveURL('/dashboard');

        // Виходимо
        await page.click('button:has-text("Вийти")');
        
        await expect(page).toHaveURL('/');
    });
});
```

```javascript
// e2e/todo.spec.js
import { test, expect } from '@playwright/test';

test.describe('Todo List E2E', () => {
    test.beforeEach(async ({ page }) => {
        // Авторизація перед кожним тестом
        await page.goto('/login');
        await page.fill('input[name="email"]', 'test@example.com');
        await page.fill('input[name="password"]', 'password123');
        await page.click('button[type="submit"]');
        await page.waitForURL('/dashboard');
    });

    test('повний workflow з завданнями', async ({ page }) => {
        await page.goto('/todos');

        // Додавання першого завдання
        await page.fill('input[placeholder="Додати завдання"]', 'Купити продукти');
        await page.click('button:has-text("Додати")');

        await expect(page.locator('text=Купити продукти')).toBeVisible();

        // Додавання другого завдання
        await page.fill('input[placeholder="Додати завдання"]', 'Прибрати квартиру');
        await page.press('input[placeholder="Додати завдання"]', 'Enter'); // Використання Enter

        await expect(page.locator('text=Прибрати квартиру')).toBeVisible();

        // Позначення першого завдання як виконане
        await page.check('input[type="checkbox"]:near(:text("Купити продукти"))');
        
        // Перевірка що завдання має клас completed
        await expect(page.locator('text=Купити продукти')).toHaveClass(/completed/);

        // Видалення другого завдання
        await page.click('button[aria-label*="Видалити"][aria-label*="Прибрати квартиру"]');
        
        await expect(page.locator('text=Прибрати квартиру')).not.toBeVisible();
        await expect(page.locator('text=Купити продукти')).toBeVisible();
    });

    test('фільтрація завдань', async ({ page }) => {
        await page.goto('/todos');

        // Додавання кількох завдань
        const tasks = ['Завдання 1', 'Завдання 2', 'Завдання 3'];
        for (const task of tasks) {
            await page.fill('input[placeholder="Додати завдання"]', task);
            await page.click('button:has-text("Додати")');
        }

        // Позначення деяких як виконані
        await page.check('input[type="checkbox"]:near(:text("Завдання 1"))');
        await page.check('input[type="checkbox"]:near(:text("Завдання 2"))');

        // Фільтр: Всі
        await page.click('button:has-text("Всі")');
        await expect(page.locator('.todo-item')).toHaveCount(3);

        // Фільтр: Активні
        await page.click('button:has-text("Активні")');
        await expect(page.locator('.todo-item')).toHaveCount(1);
        await expect(page.locator('text=Завдання 3')).toBeVisible();

        // Фільтр: Виконані
        await page.click('button:has-text("Виконані")');
        await expect(page.locator('.todo-item')).toHaveCount(2);
    });

    test('зберігає завдання після перезавантаження', async ({ page }) => {
        await page.goto('/todos');

        await page.fill('input[placeholder="Додати завдання"]', 'Стійке завдання');
        await page.click('button:has-text("Додати")');

        // Перезавантаження сторінки
        await page.reload();

        // Перевірка що завдання все ще є
        await expect(page.locator('text=Стійке завдання')).toBeVisible();
    });
});
```

## Test-driven development

### TDD цикл: Red-Green-Refactor

Test-Driven Development передбачає написання тестів перед реалізацією функціональності. Процес складається з трьох етапів:

1. Red: написання тесту, який не проходить
2. Green: написання мінімального коду для проходження тесту
3. Refactor: покращення коду без зміни функціональності

```javascript
// Counter.test.jsx - Спочатку пишемо тести
import { describe, it, expect } from 'vitest';
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import Counter from './Counter';

describe('Counter Component - TDD', () => {
    // RED: Тест не проходить, компонент ще не створений
    it('відображає початкове значення 0', () => {
        render(<Counter />);
        expect(screen.getByText('0')).toBeInTheDocument();
    });

    // RED: Тест не проходить
    it('збільшує лічильник при натисканні на кнопку збільшення', async () => {
        const user = userEvent.setup();
        render(<Counter />);
        
        const incrementButton = screen.getByRole('button', { name: /збільшити/i });
        await user.click(incrementButton);
        
        expect(screen.getByText('1')).toBeInTheDocument();
    });

    // RED: Тест не проходить
    it('зменшує лічильник при натисканні на кнопку зменшення', async () => {
        const user = userEvent.setup();
        render(<Counter />);
        
        const decrementButton = screen.getByRole('button', { name: /зменшити/i });
        await user.click(decrementButton);
        
        expect(screen.getByText('-1')).toBeInTheDocument();
    });

    // RED: Тест не проходить
    it('скидає лічильник до 0 при натисканні на кнопку скидання', async () => {
        const user = userEvent.setup();
        render(<Counter />);
        
        const incrementButton = screen.getByRole('button', { name: /збільшити/i });
        await user.click(incrementButton);
        await user.click(incrementButton);
        
        const resetButton = screen.getByRole('button', { name: /скинути/i });
        await user.click(resetButton);
        
        expect(screen.getByText('0')).toBeInTheDocument();
    });
});
```

```jsx
// Counter.jsx - GREEN: Мінімальна реалізація для проходження тестів
import { useState } from 'react';

function Counter() {
    const [count, setCount] = useState(0);

    return (
        <div>
            <p>{count}</p>
            <button onClick={() => setCount(count + 1)}>Збільшити</button>
            <button onClick={() => setCount(count - 1)}>Зменшити</button>
            <button onClick={() => setCount(0)}>Скинути</button>
        </div>
    );
}

export default Counter;
```

```jsx
// Counter.jsx - REFACTOR: Покращення коду
import { useState } from 'react';

function Counter({ initialValue = 0 }) {
    const [count, setCount] = useState(initialValue);

    const increment = () => setCount(prev => prev + 1);
    const decrement = () => setCount(prev => prev - 1);
    const reset = () => setCount(initialValue);

    return (
        <div className="counter">
            <div className="counter-display">
                <span className="counter-value">{count}</span>
            </div>
            <div className="counter-controls">
                <button onClick={decrement} aria-label="Зменшити">
                    -
                </button>
                <button onClick={reset} aria-label="Скинути">
                    Скинути
                </button>
                <button onClick={increment} aria-label="Збільшити">
                    +
                </button>
            </div>
        </div>
    );
}

export default Counter;
```

## Висновки та найкращі практики

Тестування React додатків є критично важливим для забезпечення якості, надійності та довгострокової підтримуваності коду. Основні принципи ефективного тестування:

**Тестуйте поведінку, а не реалізацію:** React Testing Library заохочує тестування компонентів з точки зору користувача. Уникайте тестування внутрішніх деталей реалізації, які можуть змінитися при рефакторингу.

**Пишіть доступні тести:** використовуйте семантичні queries як getByRole, getByLabelText. Це не тільки робить тести більш стійкими, але й покращує доступність вашого додатку.

**Мокуйте розумно:** мокуйте зовнішні залежності як API виклики, але уникайте надмірного мокування внутрішніх компонентів. MSW є чудовим рішенням для мокування HTTP запитів.

**Балансуйте типи тестів:** дотримуйтесь принципу піраміди тестування – багато unit тестів, помірна кількість integration тестів та кілька критичних E2E тестів.

**Використовуйте TDD для складної логіки:** написання тестів перед кодом допомагає краще зрозуміти вимоги та створює більш якісний дизайн коду.

**Підтримуйте тести в актуальному стані:** тести повинні розвиватися разом з кодом. Застарілі або ламані тести гірші за відсутність тестів взагалі.

**Оптимізуйте час виконання:** використовуйте паралелізацію, правильно організовуйте setup та teardown, уникайте зайвих асинхронних операцій у тестах.

Розуміння та застосування цих принципів дозволить створювати надійні, підтримувані тести, які дійсно покращують якість вашого коду та впевненість у його коректності, забезпечуючи стабільність додатку при його розвитку та рефакторингу.
