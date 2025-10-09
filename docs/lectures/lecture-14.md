# Лекція 14. HTTP клієнт та інтеграція з backend

## Вступ до взаємодії frontend з backend

Сучасні вебзастосунки побудовані на архітектурі клієнт-сервер, де frontend відповідає за інтерфейс користувача, а backend обробляє бізнес-логіку та управляє даними. Ефективна комунікація між цими компонентами є критично важливою для створення швидких, надійних та зручних додатків.

HTTP клієнт виступає посередником у цій комунікації, відправляючи запити на сервер та обробляючи отримані відповіді. Вибір та налаштування HTTP клієнта безпосередньо впливає на якість користувацького досвіду, надійність додатку та складність його підтримки.

У React екосистемі існує кілька підходів до організації HTTP комунікації, кожен з яких має свої переваги та оптимальні сценарії використання. Від нативного Fetch API до спеціалізованих бібліотек як Axios та сучасних рішень для управління серверним станом як React Query.

## Axios: потужний HTTP клієнт

### Переваги Axios над Fetch API

Axios є однією з найпопулярніших бібліотек для виконання HTTP запитів у JavaScript. Порівняно з нативним Fetch API, Axios надає більш багатий функціонал та зручніший API.

Основні переваги Axios включають автоматичну трансформацію JSON даних, підтримку старіших браузерів, вбудовану обробку timeout, можливість відміни запитів, перехоплювачі для запитів та відповідей, а також захист від CSRF атак. Fetch API натомість вимагає більше ручної роботи для досягнення аналогічної функціональності.

### Базове налаштування та використання

```javascript
import axios from 'axios';

// Створення інстанса з базовою конфігурацією
const api = axios.create({
    baseURL: 'https://api.example.com',
    timeout: 10000,
    headers: {
        'Content-Type': 'application/json'
    }
});

// Приклад простих запитів
async function fetchUsers() {
    try {
        const response = await api.get('/users');
        return response.data;
    } catch (error) {
        console.error('Помилка отримання користувачів:', error);
        throw error;
    }
}

async function createUser(userData) {
    try {
        const response = await api.post('/users', userData);
        return response.data;
    } catch (error) {
        console.error('Помилка створення користувача:', error);
        throw error;
    }
}

async function updateUser(userId, userData) {
    try {
        const response = await api.put(`/users/${userId}`, userData);
        return response.data;
    } catch (error) {
        console.error('Помилка оновлення користувача:', error);
        throw error;
    }
}

async function deleteUser(userId) {
    try {
        await api.delete(`/users/${userId}`);
    } catch (error) {
        console.error('Помилка видалення користувача:', error);
        throw error;
    }
}
```

### Конфігурація запитів

```javascript
// Запит з параметрами URL
async function searchUsers(query) {
    const response = await api.get('/users/search', {
        params: {
            q: query,
            limit: 20,
            offset: 0
        }
    });
    return response.data;
}

// Запит з кастомними заголовками
async function fetchProtectedData() {
    const response = await api.get('/protected', {
        headers: {
            'Authorization': `Bearer ${localStorage.getItem('token')}`
        }
    });
    return response.data;
}

// Запит з timeout
async function fetchWithTimeout() {
    const response = await api.get('/slow-endpoint', {
        timeout: 5000 // 5 секунд
    });
    return response.data;
}

// Завантаження файлу
async function uploadFile(file) {
    const formData = new FormData();
    formData.append('file', file);
    
    const response = await api.post('/upload', formData, {
        headers: {
            'Content-Type': 'multipart/form-data'
        },
        onUploadProgress: (progressEvent) => {
            const percentCompleted = Math.round(
                (progressEvent.loaded * 100) / progressEvent.total
            );
            console.log(`Завантажено: ${percentCompleted}%`);
        }
    });
    
    return response.data;
}
```

## Interceptors: перехоплювачі запитів та відповідей

### Концепція перехоплювачів

Перехоплювачі в Axios дозволяють глобально обробляти запити перед їх відправленням та відповіді перед тим, як вони потраплять до коду додатку. Це потужний механізм для централізованої обробки аутентифікації, логування, трансформації даних та обробки помилок.

```javascript
import axios from 'axios';

const api = axios.create({
    baseURL: 'https://api.example.com',
    timeout: 10000
});

// Перехоплювач запитів
api.interceptors.request.use(
    (config) => {
        // Додавання токена аутентифікації до кожного запиту
        const token = localStorage.getItem('authToken');
        if (token) {
            config.headers.Authorization = `Bearer ${token}`;
        }
        
        // Логування запиту
        console.log('Запит відправлено:', {
            method: config.method,
            url: config.url,
            data: config.data
        });
        
        // Додавання timestamp
        config.metadata = { startTime: new Date() };
        
        return config;
    },
    (error) => {
        console.error('Помилка при відправці запиту:', error);
        return Promise.reject(error);
    }
);

// Перехоплювач відповідей
api.interceptors.response.use(
    (response) => {
        // Обчислення часу запиту
        const endTime = new Date();
        const duration = endTime - response.config.metadata.startTime;
        
        console.log('Відповідь отримано:', {
            url: response.config.url,
            status: response.status,
            duration: `${duration}ms`
        });
        
        return response;
    },
    (error) => {
        console.error('Помилка відповіді:', error);
        return Promise.reject(error);
    }
);

export default api;
```

### Обробка оновлення токенів

```javascript
import axios from 'axios';

const api = axios.create({
    baseURL: 'https://api.example.com'
});

let isRefreshing = false;
let failedQueue = [];

const processQueue = (error, token = null) => {
    failedQueue.forEach(prom => {
        if (error) {
            prom.reject(error);
        } else {
            prom.resolve(token);
        }
    });
    
    failedQueue = [];
};

api.interceptors.response.use(
    (response) => response,
    async (error) => {
        const originalRequest = error.config;
        
        // Якщо отримали 401 і це не запит на оновлення токену
        if (error.response?.status === 401 && !originalRequest._retry) {
            if (isRefreshing) {
                // Якщо токен вже оновлюється, додаємо запит у чергу
                return new Promise((resolve, reject) => {
                    failedQueue.push({ resolve, reject });
                })
                    .then(token => {
                        originalRequest.headers.Authorization = `Bearer ${token}`;
                        return api(originalRequest);
                    })
                    .catch(err => Promise.reject(err));
            }
            
            originalRequest._retry = true;
            isRefreshing = true;
            
            try {
                const refreshToken = localStorage.getItem('refreshToken');
                const response = await axios.post('/auth/refresh', {
                    refreshToken
                });
                
                const { accessToken } = response.data;
                localStorage.setItem('authToken', accessToken);
                
                api.defaults.headers.common.Authorization = `Bearer ${accessToken}`;
                originalRequest.headers.Authorization = `Bearer ${accessToken}`;
                
                processQueue(null, accessToken);
                
                return api(originalRequest);
            } catch (err) {
                processQueue(err, null);
                
                // Перенаправлення на сторінку входу
                localStorage.removeItem('authToken');
                localStorage.removeItem('refreshToken');
                window.location.href = '/login';
                
                return Promise.reject(err);
            } finally {
                isRefreshing = false;
            }
        }
        
        return Promise.reject(error);
    }
);

export default api;
```

## Обробка помилок API

### Типізація помилок

```javascript
class APIError extends Error {
    constructor(message, status, data) {
        super(message);
        this.name = 'APIError';
        this.status = status;
        this.data = data;
    }
}

class NetworkError extends Error {
    constructor(message) {
        super(message);
        this.name = 'NetworkError';
    }
}

class ValidationError extends APIError {
    constructor(message, errors) {
        super(message, 422, errors);
        this.name = 'ValidationError';
        this.errors = errors;
    }
}
```

### Централізована обробка помилок

```javascript
import axios from 'axios';
import { toast } from 'react-toastify';

const api = axios.create({
    baseURL: 'https://api.example.com'
});

api.interceptors.response.use(
    (response) => response,
    (error) => {
        if (error.response) {
            // Сервер відповів зі статусом помилки
            const { status, data } = error.response;
            
            switch (status) {
                case 400:
                    toast.error('Некоректний запит. Перевірте введені дані.');
                    throw new APIError('Bad Request', 400, data);
                    
                case 401:
                    toast.error('Необхідна авторизація');
                    // Можна перенаправити на сторінку логіну
                    throw new APIError('Unauthorized', 401, data);
                    
                case 403:
                    toast.error('Доступ заборонено');
                    throw new APIError('Forbidden', 403, data);
                    
                case 404:
                    toast.error('Ресурс не знайдено');
                    throw new APIError('Not Found', 404, data);
                    
                case 422:
                    // Помилки валідації
                    const validationErrors = data.errors || {};
                    throw new ValidationError('Validation failed', validationErrors);
                    
                case 500:
                    toast.error('Помилка сервера. Спробуйте пізніше.');
                    throw new APIError('Server Error', 500, data);
                    
                default:
                    toast.error('Щось пішло не так');
                    throw new APIError('Unknown Error', status, data);
            }
        } else if (error.request) {
            // Запит був відправлений, але відповідь не отримана
            toast.error('Проблема з підключенням до сервера');
            throw new NetworkError('No response from server');
        } else {
            // Помилка при налаштуванні запиту
            toast.error('Помилка виконання запиту');
            throw new Error(error.message);
        }
    }
);

export default api;
```

### Використання в компонентах

```javascript
import { useState } from 'react';
import api from './api';

function UserProfile() {
    const [user, setUser] = useState(null);
    const [error, setError] = useState(null);
    const [validationErrors, setValidationErrors] = useState({});

    const updateProfile = async (formData) => {
        try {
            setError(null);
            setValidationErrors({});
            
            const response = await api.put('/profile', formData);
            setUser(response.data);
            
            toast.success('Профіль оновлено успішно');
        } catch (err) {
            if (err instanceof ValidationError) {
                setValidationErrors(err.errors);
                
                // Відображення помилок валідації
                Object.values(err.errors).forEach(errorMsg => {
                    toast.error(errorMsg);
                });
            } else if (err instanceof APIError) {
                setError(err.message);
            } else if (err instanceof NetworkError) {
                setError('Відсутнє підключення до інтернету');
            } else {
                setError('Невідома помилка');
            }
        }
    };

    return (
        <div>
            {error && <div className="error-banner">{error}</div>}
            
            <form onSubmit={(e) => {
                e.preventDefault();
                const formData = new FormData(e.target);
                updateProfile(Object.fromEntries(formData));
            }}>
                <input
                    name="name"
                    placeholder="Ім'я"
                />
                {validationErrors.name && (
                    <span className="error">{validationErrors.name}</span>
                )}
                
                <input
                    name="email"
                    type="email"
                    placeholder="Email"
                />
                {validationErrors.email && (
                    <span className="error">{validationErrors.email}</span>
                )}
                
                <button type="submit">Оновити профіль</button>
            </form>
        </div>
    );
}
```

## Loading стани та UX

### Управління станами завантаження

```javascript
import { useState } from 'react';
import api from './api';

function DataList() {
    const [data, setData] = useState([]);
    const [isLoading, setIsLoading] = useState(false);
    const [isRefreshing, setIsRefreshing] = useState(false);
    const [error, setError] = useState(null);

    const fetchData = async (showRefresh = false) => {
        try {
            if (showRefresh) {
                setIsRefreshing(true);
            } else {
                setIsLoading(true);
            }
            setError(null);
            
            const response = await api.get('/data');
            setData(response.data);
        } catch (err) {
            setError(err.message);
        } finally {
            setIsLoading(false);
            setIsRefreshing(false);
        }
    };

    const deleteItem = async (id) => {
        try {
            await api.delete(`/data/${id}`);
            
            // Оптимістичне оновлення UI
            setData(prevData => prevData.filter(item => item.id !== id));
            
            toast.success('Елемент видалено');
        } catch (err) {
            // Відновлення попереднього стану при помилці
            toast.error('Не вдалося видалити елемент');
            fetchData(true);
        }
    };

    if (isLoading) {
        return (
            <div className="loading-container">
                <div className="spinner" />
                <p>Завантаження даних...</p>
            </div>
        );
    }

    if (error) {
        return (
            <div className="error-container">
                <p className="error-message">{error}</p>
                <button onClick={() => fetchData()}>
                    Спробувати знову
                </button>
            </div>
        );
    }

    return (
        <div>
            <div className="header">
                <h1>Список даних</h1>
                <button
                    onClick={() => fetchData(true)}
                    disabled={isRefreshing}
                >
                    {isRefreshing ? 'Оновлення...' : 'Оновити'}
                </button>
            </div>
            
            {data.length === 0 ? (
                <div className="empty-state">
                    <p>Немає даних для відображення</p>
                </div>
            ) : (
                <ul className="data-list">
                    {data.map(item => (
                        <li key={item.id}>
                            {item.name}
                            <button onClick={() => deleteItem(item.id)}>
                                Видалити
                            </button>
                        </li>
                    ))}
                </ul>
            )}
        </div>
    );
}
```

### Skeleton screens

```javascript
function SkeletonCard() {
    return (
        <div className="skeleton-card">
            <div className="skeleton skeleton-avatar" />
            <div className="skeleton skeleton-title" />
            <div className="skeleton skeleton-text" />
            <div className="skeleton skeleton-text short" />
        </div>
    );
}

function UserList() {
    const [users, setUsers] = useState([]);
    const [isLoading, setIsLoading] = useState(true);

    useEffect(() => {
        fetchUsers();
    }, []);

    const fetchUsers = async () => {
        setIsLoading(true);
        try {
            const response = await api.get('/users');
            setUsers(response.data);
        } finally {
            setIsLoading(false);
        }
    };

    return (
        <div className="user-list">
            {isLoading ? (
                <>
                    {[...Array(5)].map((_, i) => (
                        <SkeletonCard key={i} />
                    ))}
                </>
            ) : (
                users.map(user => (
                    <UserCard key={user.id} user={user} />
                ))
            )}
        </div>
    );
}
```

## Кешування запитів

### Простий кеш в пам'яті

```javascript
class APICache {
    constructor(ttl = 300000) { // 5 хвилин за замовчуванням
        this.cache = new Map();
        this.ttl = ttl;
    }

    generateKey(url, params) {
        return `${url}?${JSON.stringify(params || {})}`;
    }

    get(url, params) {
        const key = this.generateKey(url, params);
        const cached = this.cache.get(key);
        
        if (!cached) return null;
        
        // Перевірка чи не застарів кеш
        if (Date.now() - cached.timestamp > this.ttl) {
            this.cache.delete(key);
            return null;
        }
        
        return cached.data;
    }

    set(url, params, data) {
        const key = this.generateKey(url, params);
        this.cache.set(key, {
            data,
            timestamp: Date.now()
        });
    }

    clear() {
        this.cache.clear();
    }

    invalidate(url, params) {
        const key = this.generateKey(url, params);
        this.cache.delete(key);
    }
}

const apiCache = new APICache();

// Wrapper функція для запитів з кешуванням
async function cachedGet(url, params = {}, options = {}) {
    const { useCache = true, cacheTime = 300000 } = options;
    
    if (useCache) {
        const cached = apiCache.get(url, params);
        if (cached) {
            console.log('Використання кешованих даних');
            return cached;
        }
    }
    
    const response = await api.get(url, { params });
    const data = response.data;
    
    if (useCache) {
        apiCache.set(url, params, data);
    }
    
    return data;
}

// Використання
async function fetchUsers() {
    return cachedGet('/users', {}, { cacheTime: 600000 }); // 10 хвилин
}
```

## React Query для data fetching

### Введення в React Query

React Query революціонізував підхід до управління серверним станом у React додатках. Замість ручного управління станами завантаження, кешування та синхронізації даних, React Query надає декларативний API для роботи з асинхронними даними.

```javascript
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import api from './api';

// Функції для API запитів
const fetchUsers = async () => {
    const response = await api.get('/users');
    return response.data;
};

const fetchUser = async (userId) => {
    const response = await api.get(`/users/${userId}`);
    return response.data;
};

const createUser = async (userData) => {
    const response = await api.post('/users', userData);
    return response.data;
};

const updateUser = async ({ userId, userData }) => {
    const response = await api.put(`/users/${userId}`, userData);
    return response.data;
};

const deleteUser = async (userId) => {
    await api.delete(`/users/${userId}`);
};

// Компонент зі списком користувачів
function UserList() {
    const {
        data: users,
        isLoading,
        isError,
        error,
        refetch
    } = useQuery({
        queryKey: ['users'],
        queryFn: fetchUsers,
        staleTime: 300000, // 5 хвилин
        cacheTime: 600000 // 10 хвилин
    });

    if (isLoading) {
        return <div>Завантаження користувачів...</div>;
    }

    if (isError) {
        return (
            <div>
                <p>Помилка: {error.message}</p>
                <button onClick={() => refetch()}>Спробувати знову</button>
            </div>
        );
    }

    return (
        <div>
            <button onClick={() => refetch()}>Оновити</button>
            <ul>
                {users.map(user => (
                    <li key={user.id}>{user.name}</li>
                ))}
            </ul>
        </div>
    );
}
```

### Mutations та оновлення кешу

```javascript
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';

function UserManager() {
    const queryClient = useQueryClient();

    // Query для отримання користувачів
    const { data: users } = useQuery({
        queryKey: ['users'],
        queryFn: fetchUsers
    });

    // Mutation для створення користувача
    const createMutation = useMutation({
        mutationFn: createUser,
        onSuccess: (newUser) => {
            // Оптимістичне оновлення кешу
            queryClient.setQueryData(['users'], (old) => [...old, newUser]);
            
            toast.success('Користувача створено');
        },
        onError: (error) => {
            toast.error('Помилка створення користувача');
        }
    });

    // Mutation для оновлення користувача
    const updateMutation = useMutation({
        mutationFn: updateUser,
        onMutate: async ({ userId, userData }) => {
            // Скасування всіх запитів, що виконуються
            await queryClient.cancelQueries({ queryKey: ['users'] });
            
            // Збереження попереднього стану
            const previousUsers = queryClient.getQueryData(['users']);
            
            // Оптимістичне оновлення
            queryClient.setQueryData(['users'], (old) =>
                old.map(user =>
                    user.id === userId ? { ...user, ...userData } : user
                )
            );
            
            return { previousUsers };
        },
        onError: (err, variables, context) => {
            // Відновлення попереднього стану при помилці
            queryClient.setQueryData(['users'], context.previousUsers);
            toast.error('Помилка оновлення');
        },
        onSettled: () => {
            // Повторне завантаження даних після завершення
            queryClient.invalidateQueries({ queryKey: ['users'] });
        }
    });

    // Mutation для видалення користувача
    const deleteMutation = useMutation({
        mutationFn: deleteUser,
        onSuccess: (_, userId) => {
            queryClient.setQueryData(['users'], (old) =>
                old.filter(user => user.id !== userId)
            );
            toast.success('Користувача видалено');
        }
    });

    const handleCreate = (userData) => {
        createMutation.mutate(userData);
    };

    const handleUpdate = (userId, userData) => {
        updateMutation.mutate({ userId, userData });
    };

    const handleDelete = (userId) => {
        if (window.confirm('Видалити користувача?')) {
            deleteMutation.mutate(userId);
        }
    };

    return (
        <div>
            <UserForm onSubmit={handleCreate} />
            <UserList
                users={users}
                onUpdate={handleUpdate}
                onDelete={handleDelete}
            />
        </div>
    );
}
```

### Залежні запити та паралельне завантаження

```javascript
// Залежні запити
function UserProfile({ userId }) {
    // Спочатку завантажується користувач
    const { data: user } = useQuery({
        queryKey: ['user', userId],
        queryFn: () => fetchUser(userId)
    });

    // Потім завантажуються пости користувача
    const { data: posts } = useQuery({
        queryKey: ['posts', userId],
        queryFn: () => fetchUserPosts(userId),
        enabled: !!user // Запит виконується тільки якщо user завантажений
    });

    if (!user) return <div>Завантаження...</div>;

    return (
        <div>
            <h1>{user.name}</h1>
            {posts && (
                <div>
                    <h2>Пости</h2>
                    {posts.map(post => (
                        <PostCard key={post.id} post={post} />
                    ))}
                </div>
            )}
        </div>
    );
}

// Паралельне завантаження
function Dashboard() {
    const { data: users } = useQuery({
        queryKey: ['users'],
        queryFn: fetchUsers
    });

    const { data: posts } = useQuery({
        queryKey: ['posts'],
        queryFn: fetchPosts
    });

    const { data: stats } = useQuery({
        queryKey: ['stats'],
        queryFn: fetchStats
    });

    // Всі запити виконуються паралельно
    const isLoading = !users || !posts || !stats;

    if (isLoading) {
        return <div>Завантаження dashboard...</div>;
    }

    return (
        <div>
            <StatsSummary stats={stats} />
            <UserList users={users} />
            <PostList posts={posts} />
        </div>
    );
}
```

## Оптимістичні оновлення

### Концепція оптимістичних оновлень

Оптимістичні оновлення покращують користувацький досвід, миттєво відображаючи зміни в UI ще до отримання відповіді від сервера. Якщо запит завершується помилкою, зміни відкочуються до попереднього стану.

```javascript
import { useState } from 'react';
import { useMutation, useQueryClient } from '@tanstack/react-query';

function TodoList() {
    const [todos, setTodos] = useState([]);
    const queryClient = useQueryClient();

    const toggleTodoMutation = useMutation({
        mutationFn: async ({ id, completed }) => {
            await api.patch(`/todos/${id}`, { completed });
        },
        onMutate: async ({ id, completed }) => {
            // Оптимістичне оновлення
            setTodos(prev =>
                prev.map(todo =>
                    todo.id === id ? { ...todo, completed } : todo
                )
            );
        },
        onError: (err, { id, completed }, context) => {
            // Відкат при помилці
            setTodos(prev =>
                prev.map(todo =>
                    todo.id === id ? { ...todo, completed: !completed } : todo
                )
            );
            toast.error('Не вдалося оновити завдання');
        },
        onSuccess: () => {
            queryClient.invalidateQueries(['todos']);
        }
    });

    const handleToggle = (id, completed) => {
        toggleTodoMutation.mutate({ id, completed: !completed });
    };

    return (
        <ul>
            {todos.map(todo => (
                <li key={todo.id}>
                    <input
                        type="checkbox"
                        checked={todo.completed}
                        onChange={() => handleToggle(todo.id, todo.completed)}
                    />
                    <span className={todo.completed ? 'completed' : ''}>
                        {todo.title}
                    </span>
                </li>
            ))}
        </ul>
    );
}
```

### Оптимістичне видалення

```javascript
function DeleteButton({ itemId }) {
    const queryClient = useQueryClient();
    const [isDeleting, setIsDeleting] = useState(false);

    const deleteMutation = useMutation({
        mutationFn: async () => {
            await new Promise(resolve => setTimeout(resolve, 2000)); // Симуляція затримки
            await api.delete(`/items/${itemId}`);
        },
        onMutate: () => {
            setIsDeleting(true);
            
            // Зберігаємо попередній стан
            const previousItems = queryClient.getQueryData(['items']);
            
            // Оптимістичне видалення з UI
            queryClient.setQueryData(['items'], (old) =>
                old.filter(item => item.id !== itemId)
            );
            
            return { previousItems };
        },
        onError: (err, variables, context) => {
            // Відновлення при помилці
            queryClient.setQueryData(['items'], context.previousItems);
            toast.error('Не вдалося видалити елемент');
            setIsDeleting(false);
        },
        onSuccess: () => {
            toast.success('Елемент видалено');
        }
    });

    return (
        <button
            onClick={() => deleteMutation.mutate()}
            disabled={isDeleting}
            className={isDeleting ? 'deleting' : ''}
        >
            {isDeleting ? 'Видалення...' : 'Видалити'}
        </button>
    );
}
```

## Висновки та найкращі практики

Ефективна інтеграція frontend з backend є критично важливою для створення високоякісних вебзастосунків. Основні принципи, які варто пам'ятати:

**Вибір HTTP клієнта:** Axios надає більш багатий функціонал порівняно з Fetch API, особливо для складних додатків. Використовуйте interceptors для централізованої обробки аутентифікації та помилок.

**Обробка помилок:** створюйте чіткі класи помилок, централізовано обробляйте різні типи помилок та надавайте зрозумілі повідомлення користувачам. Завжди майте план дій для кожного типу помилки.

**Користувацький досвід:** використовуйте skeleton screens замість звичайних спіннерів, показуйте прогрес довгих операцій та застосовуйте оптимістичні оновлення для покращення сприйнятої продуктивності.

**Управління станом:** React Query значно спрощує роботу з серверним станом, автоматично кешує дані, синхронізує стан між компонентами та надає потужні інструменти для mutations.

**Оптимізація продуктивності:** кешуйте запити де це доречно, використовуйте паралельне завантаження для незалежних даних та мінімізуйте кількість запитів через правильне проектування API.

**Безпека:** ніколи не зберігайте чутливі дані в localStorage без шифрування, використовуйте HTTPS для всіх запитів та правильно обробляйте токени оновлення.

Розуміння та застосування цих принципів дозволить створювати надійні, швидкі та зручні у використанні вебзастосунки, які забезпечать високу якість взаємодії користувачів з вашим продуктом.
