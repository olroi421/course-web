# Лабораторна робота 5. Розроблення основного інтерфейсу та функціоналу

## 🎯 Мета роботи

Здобути практичні навички створення повнофункціонального користувацького інтерфейсу з системою аутентифікації, управлінням станом, валідацією форм та реалізацією CRUD операцій для взаємодії з backend API.

## ✅ Завдання

### Загальний контекст

Ця лабораторна робота продовжує розробку frontend частини проєкту, розпочату в лабораторній роботі 4. Здобувачі освіти інтегрують клієнтську частину з backend API, створюють повноцінні інтерфейси для роботи з даними та реалізують систему аутентифікації користувачів.

### Технічні завдання

**Рівень 1. Система аутентифікації та базовий dashboard**

1. Створити сторінки Login та Register з відповідними формами.
2. Реалізувати логіку аутентифікації через Context API або Zustand.
3. Створити Protected Routes для захищених сторінок.
4. Розробити головний Dashboard з навігаційним меню.
5. Реалізувати функціональність logout.
6. Додати індикатори стану завантаження під час запитів.
7. Створити базовий компонент для відображення помилок.

**Рівень 2. CRUD інтерфейси та валідація**

8. Створити сторінки для відображення списків основних сутностей проєкту.
9. Реалізувати сторінки для створення нових записів з формами.
10. Додати сторінки для редагування існуючих записів.
11. Реалізувати підтвердження та функціональність видалення записів.
12. Впровадити валідацію форм через React Hook Form та Zod схеми.
13. Створити переусні компоненти для відображення даних: Table, List, Grid.
14. Додати пагінацію для списків з великою кількістю записів.

**Рівень 3. Розширений UI/UX та стан**

15. Реалізувати глобальне управління станом для всього застосунку.
16. Створити систему сповіщень для success/error повідомлень через toast.
17. Додати функціональність пошуку та фільтрації у списках.
18. Реалізувати сортування даних за різними критеріями.
19. Створити детальні сторінки для перегляду окремих записів.
20. Додати breadcrumbs навігацію для покращення UX.
21. Реалізувати skeleton loaders для покращення сприйняття завантаження.
22. Створити responsive sidebar з можливістю згортання на мобільних пристроях.

### Результат виконання

Після завершення лабораторної роботи здобувач освіти матиме повністю функціональний інтерфейс з системою аутентифікації, CRUD операціями для всіх основних сутностей проєкту, валідацією форм та зручним користувацьким досвідом.

## 👥 Форма виконання роботи

Форма виконання роботи **індивідуальна**.

## 📝 Критерії оцінювання

**Середній рівень (оцінка "задовільно")**

- Реалізовано базову систему аутентифікації з login та register.
- Створено прості CRUD сторінки для 1-2 сутностей.
- Форми працюють без валідації або з базовою валідацією.
- Відсутнє глобальне управління станом.
- Інтерфейс має базову функціональність без додаткових покращень UX.
- Немає обробки помилок або вона мінімальна.
- Код має недоліки у структурі та організації.

**Достатній рівень (оцінка "добре")**

- Повністю реалізована система аутентифікації з Protected Routes.
- Створено CRUD інтерфейси для основних сутностей проєкту.
- Впроваджена валідація форм через React Hook Form.
- Використовується Context API для управління станом.
- Додані базові індикатори завантаження та помилок.
- Реалізована пагінація для списків.
- Інтерфейс є responsive та зручним для користувача.
- Код організовано у логічні модулі.

**Високий рівень (оцінка "відмінно")**

- Повністю виконано всі завдання трьох рівнів.
- Реалізована розширена система управління станом з Zustand або аналогічним рішенням.
- Впроваджена складна валідація з Zod схемами.
- Створена система сповіщень та toast повідомлень.
- Додані пошук, фільтрація та сортування у списках.
- Реалізовані skeleton loaders та інші покращення UX.
- Код відповідає принципам чистого коду та має модульну структуру.
- Створена детальна документація компонентів.
- Продемонстровано глибоке розуміння React patterns та state management.

## ⏰ Політика щодо дедлайнів

При порушенні встановленого терміну здачі лабораторної роботи максимальна можлива оцінка становить "добре", незалежно від якості виконаної роботи. Винятки можливі лише за поважних причин, підтверджених документально.

## 📚 Теоретичні відомості

### Аутентифікація у React застосунках

**Аутентифікація** — це процес перевірки ідентичності користувача у вебзастосунку. У React застосунках аутентифікація зазвичай реалізується через токени JWT, які зберігаються на клієнті після успішного входу та відправляються з кожним запитом до захищених endpoint-ів.

Типовий потік аутентифікації включає відправлення облікових даних на сервер, отримання JWT токена у відповіді, збереження токена у localStorage або sessionStorage, додавання токена до заголовків HTTP запитів та перенаправлення користувача до захищених сторінок. Важливо реалізувати автоматичний logout при отриманні 401 помилки та очищення токена при виході користувача.

### React Hook Form

**React Hook Form** — це бібліотека для роботи з формами у React, яка використовує React hooks та нативні HTML форми для досягнення високої продуктивності. На відміну від традиційних підходів з контрольованими компонентами, React Hook Form мінімізує кількість ре-рендерів, використовуючи неконтрольовані компоненти та refs.

Основні переваги React Hook Form включають відмінну продуктивність завдяки мінімальним ре-рендерам, просту інтеграцію з бібліотеками валідації як Zod або Yup, підтримку вкладених об'єктів та масивів, вбудовану обробку помилок та зручний API для роботи з полями форм. Бібліотека також надає hooks для спостереження за значеннями полів, ручного встановлення значень та тригерингу валідації.

### Zod валідація схем

**Zod** — це TypeScript-first бібліотека для валідації схем та парсингу даних. Zod дозволяє декларативно описати структуру та правила валідації даних, автоматично виводить TypeScript типи з схем та забезпечує детальні повідомлення про помилки.

Основні можливості Zod включають визначення типів даних, обов'язкових та опціональних полів, мінімальної та максимальної довжини для рядків, діапазонів для чисел, регулярних виразів для складної валідації, кастомних валідаторів та трансформації даних. Інтеграція Zod з React Hook Form через resolver надає потужну систему валідації з автоматичним виведенням типів.

### Управління станом у React

**Управління станом** — це ключовий аспект розробки React застосунків, особливо коли стан потрібно розділяти між багатьма компонентами. Існує кілька підходів до управління станом, кожен з яких підходить для різних сценаріїв.

**Context API** — це вбудоване React рішення для передачі даних через дерево компонентів без prop drilling. Context особливо корисний для глобальних даних як тема, мова інтерфейсу або дані аутентифікованого користувача. Проте Context може призвести до зайвих ре-рендерів, якщо використовується неправильно.

**Zustand** — це легка бібліотека для управління станом, яка використовує hooks та не потребує провайдерів. Zustand надає простий API для створення stores, підписки на зміни стану та оптимізацію ре-рендерів через селектори. Zustand особливо зручний для середніх за розміром застосунків, де Redux буде надмірним, а Context API недостатнім.

### Protected Routes

**Protected Routes** — це паттерн у React Router для захисту маршрутів, які потребують аутентифікації. Цей підхід передбачає перевірку статусу аутентифікації користувача перед відображенням компонента та перенаправлення неаутентифікованих користувачів на сторінку входу.

Реалізація Protected Routes зазвичай включає створення компонента-обгортки, який перевіряє наявність токена або стан аутентифікації з Context, використання Navigate компонента з React Router для перенаправлення та збереження intended URL для редиректу після успішного входу. Цей паттерн забезпечує безпеку на рівні клієнта, хоча серверна валідація токенів залишається критично важливою.

### CRUD операції у React

**CRUD (Create, Read, Update, Delete)** операції формують основу більшості вебзастосунків для роботи з даними. У React застосунках CRUD операції реалізуються через HTTP запити до backend API з відповідним оновленням локального стану.

Типовий патерн включає виклик API методу через Axios або fetch, відображення індикатора завантаження під час запиту, оновлення локального стану після успішної операції, відображення повідомлення про успіх або помилку та обробку edge cases як мережеві помилки або валідаційні помилки від сервера. Важливо також впроваджувати optimistic updates для покращення UX, особливо для операцій видалення та оновлення.

## 🔗 Додаткові ресурси

- [React Hook Form документація](https://react-hook-form.com/)
- [Zod документація](https://zod.dev/)
- [Zustand документація](https://github.com/pmndrs/zustand)
- [React Router Authentication](https://reactrouter.com/en/main/start/tutorial#adding-a-no-match-route)
- [JWT токени пояснення](https://jwt.io/introduction)
- [React Context API](https://react.dev/reference/react/useContext)

## ▶️ Хід роботи

### Крок 1. Налаштування управління станом

Встановити Zustand для управління глобальним станом.

```bash
npm install zustand
```

Створити store для аутентифікації у файлі `src/stores/authStore.ts`.

```typescript
import { create } from 'zustand';
import { persist } from 'zustand/middleware';

interface User {
  id: number;
  name: string;
  email: string;
}

interface AuthState {
  user: User | null;
  token: string | null;
  isAuthenticated: boolean;
  login: (user: User, token: string) => void;
  logout: () => void;
}

export const useAuthStore = create<AuthState>()(
  persist(
    (set) => ({
      user: null,
      token: null,
      isAuthenticated: false,
      login: (user, token) => {
        localStorage.setItem('token', token);
        set({ user, token, isAuthenticated: true });
      },
      logout: () => {
        localStorage.removeItem('token');
        set({ user: null, token: null, isAuthenticated: false });
      },
    }),
    {
      name: 'auth-storage',
    }
  )
);
```

### Крок 2. Створення форм аутентифікації

Встановити необхідні бібліотеки для роботи з формами.

```bash
npm install react-hook-form @hookform/resolvers zod
```

Створити Zod схему для валідації у файлі `src/schemas/auth.schema.ts`.

```typescript
import { z } from 'zod';

export const loginSchema = z.object({
  email: z.string().email('Невірний формат email'),
  password: z.string().min(6, 'Пароль має містити мінімум 6 символів'),
});

export const registerSchema = z.object({
  name: z.string().min(2, "Ім'я має містити мінімум 2 символи"),
  email: z.string().email('Невірний формат email'),
  password: z.string().min(6, 'Пароль має містити мінімум 6 символів'),
  confirmPassword: z.string(),
}).refine((data) => data.password === data.confirmPassword, {
  message: 'Паролі не співпадають',
  path: ['confirmPassword'],
});

export type LoginFormData = z.infer<typeof loginSchema>;
export type RegisterFormData = z.infer<typeof registerSchema>;
```

Створити компонент LoginPage у файлі `src/pages/LoginPage.tsx`.

```typescript
import React from 'react';
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { useNavigate, Link } from 'react-router-dom';
import { loginSchema, LoginFormData } from '../schemas/auth.schema';
import { useAuthStore } from '../stores/authStore';
import api from '../services/api';
import Button from '../components/common/Button';
import Input from '../components/common/Input';

const LoginPage: React.FC = () => {
  const navigate = useNavigate();
  const login = useAuthStore((state) => state.login);

  const {
    register,
    handleSubmit,
    formState: { errors, isSubmitting },
    setError,
  } = useForm<LoginFormData>({
    resolver: zodResolver(loginSchema),
  });

  const onSubmit = async (data: LoginFormData) => {
    try {
      const response = await api.post('/auth/login', data);
      login(response.data.user, response.data.token);
      navigate('/dashboard');
    } catch (error: any) {
      setError('root', {
        message: error.response?.data?.message || 'Помилка входу',
      });
    }
  };

  return (
    <div className="min-h-screen flex items-center justify-center bg-gray-50">
      <div className="max-w-md w-full bg-white rounded-lg shadow-lg p-8">
        <h2 className="text-3xl font-bold text-center mb-6">Вхід</h2>

        <form onSubmit={handleSubmit(onSubmit)} className="space-y-4">
          <div>
            <label className="block text-sm font-medium mb-1">Email</label>
            <Input
              type="email"
              {...register('email')}
              placeholder="your@email.com"
            />
            {errors.email && (
              <p className="text-red-500 text-sm mt-1">{errors.email.message}</p>
            )}
          </div>

          <div>
            <label className="block text-sm font-medium mb-1">Пароль</label>
            <Input
              type="password"
              {...register('password')}
              placeholder="••••••••"
            />
            {errors.password && (
              <p className="text-red-500 text-sm mt-1">{errors.password.message}</p>
            )}
          </div>

          {errors.root && (
            <p className="text-red-500 text-sm">{errors.root.message}</p>
          )}

          <Button
            type="submit"
            variant="primary"
            className="w-full"
            isLoading={isSubmitting}
          >
            Увійти
          </Button>
        </form>

        <p className="text-center mt-4 text-sm">
          Немає акаунту?{' '}
          <Link to="/register" className="text-blue-600 hover:underline">
            Зареєструватися
          </Link>
        </p>
      </div>
    </div>
  );
};

export default LoginPage;
```

### Крок 3. Створення Protected Routes

Створити компонент для захищених маршрутів у файлі `src/components/ProtectedRoute.tsx`.

```typescript
import React from 'react';
import { Navigate } from 'react-router-dom';
import { useAuthStore } from '../stores/authStore';

interface ProtectedRouteProps {
  children: React.ReactNode;
}

const ProtectedRoute: React.FC<ProtectedRouteProps> = ({ children }) => {
  const isAuthenticated = useAuthStore((state) => state.isAuthenticated);

  if (!isAuthenticated) {
    return <Navigate to="/login" replace />;
  }

  return <>{children}</>;
};

export default ProtectedRoute;
```

Оновити роутинг у файлі `src/App.tsx`.

```typescript
import React from 'react';
import { BrowserRouter, Routes, Route } from 'react-router-dom';
import Layout from './components/layout/Layout';
import ProtectedRoute from './components/ProtectedRoute';
import HomePage from './pages/HomePage';
import LoginPage from './pages/LoginPage';
import RegisterPage from './pages/RegisterPage';
import DashboardPage from './pages/DashboardPage';

const App: React.FC = () => {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/login" element={<LoginPage />} />
        <Route path="/register" element={<RegisterPage />} />

        <Route path="/" element={<Layout />}>
          <Route index element={<HomePage />} />
          <Route
            path="dashboard"
            element={
              <ProtectedRoute>
                <DashboardPage />
              </ProtectedRoute>
            }
          />
        </Route>
      </Routes>
    </BrowserRouter>
  );
};

export default App;
```

### Крок 4. Створення Dashboard

Створити головну сторінку Dashboard у файлі `src/pages/DashboardPage.tsx`.

```typescript
import React from 'react';
import { useAuthStore } from '../stores/authStore';
import { Link } from 'react-router-dom';

const DashboardPage: React.FC = () => {
  const user = useAuthStore((state) => state.user);

  return (
    <div>
      <h1 className="text-3xl font-bold mb-6">
        Вітаємо, {user?.name}!
      </h1>

      <div className="grid grid-cols-1 md:grid-cols-3 gap-6">
        <Link
          to="/users"
          className="p-6 bg-white rounded-lg shadow hover:shadow-lg transition-shadow"
        >
          <h3 className="text-xl font-semibold mb-2">Користувачі</h3>
          <p className="text-gray-600">Управління користувачами системи</p>
        </Link>

        <Link
          to="/products"
          className="p-6 bg-white rounded-lg shadow hover:shadow-lg transition-shadow"
        >
          <h3 className="text-xl font-semibold mb-2">Продукти</h3>
          <p className="text-gray-600">Каталог продуктів</p>
        </Link>

        <Link
          to="/orders"
          className="p-6 bg-white rounded-lg shadow hover:shadow-lg transition-shadow"
        >
          <h3 className="text-xl font-semibold mb-2">Замовлення</h3>
          <p className="text-gray-600">Перегляд та управління замовленнями</p>
        </Link>
      </div>
    </div>
  );
};

export default DashboardPage;
```

### Крок 5. Створення CRUD компонентів

Створити сервіс для роботи з сутністю у файлі `src/services/users.service.ts`.

```typescript
import api from './api';
import { User } from '../types/api.types';

export const usersService = {
  getAll: async () => {
    const response = await api.get<User[]>('/users');
    return response.data;
  },

  getById: async (id: number) => {
    const response = await api.get<User>(`/users/${id}`);
    return response.data;
  },

  create: async (data: Omit<User, 'id'>) => {
    const response = await api.post<User>('/users', data);
    return response.data;
  },

  update: async (id: number, data: Partial<User>) => {
    const response = await api.put<User>(`/users/${id}`, data);
    return response.data;
  },

  delete: async (id: number) => {
    await api.delete(`/users/${id}`);
  },
};
```

Створити store для управління списком користувачів у файлі `src/stores/usersStore.ts`.

```typescript
import { create } from 'zustand';
import { User } from '../types/api.types';
import { usersService } from '../services/users.service';

interface UsersState {
  users: User[];
  loading: boolean;
  error: string | null;
  fetchUsers: () => Promise<void>;
  deleteUser: (id: number) => Promise<void>;
}

export const useUsersStore = create<UsersState>((set) => ({
  users: [],
  loading: false,
  error: null,

  fetchUsers: async () => {
    set({ loading: true, error: null });
    try {
      const users = await usersService.getAll();
      set({ users, loading: false });
    } catch (error: any) {
      set({ error: error.message, loading: false });
    }
  },

  deleteUser: async (id: number) => {
    try {
      await usersService.delete(id);
      set((state) => ({
        users: state.users.filter((user) => user.id !== id),
      }));
    } catch (error: any) {
      set({ error: error.message });
    }
  },
}));
```

Створити сторінку зі списком користувачів у файлі `src/pages/UsersListPage.tsx`.

```typescript
import React, { useEffect } from 'react';
import { Link } from 'react-router-dom';
import { useUsersStore } from '../stores/usersStore';
import Button from '../components/common/Button';

const UsersListPage: React.FC = () => {
  const { users, loading, error, fetchUsers, deleteUser } = useUsersStore();

  useEffect(() => {
    fetchUsers();
  }, [fetchUsers]);

  if (loading) {
    return <div className="text-center">Завантаження...</div>;
  }

  if (error) {
    return <div className="text-red-500">Помилка: {error}</div>;
  }

  return (
    <div>
      <div className="flex justify-between items-center mb-6">
        <h1 className="text-3xl font-bold">Користувачі</h1>
        <Link to="/users/create">
          <Button variant="primary">Додати користувача</Button>
        </Link>
      </div>

      <div className="bg-white rounded-lg shadow overflow-hidden">
        <table className="min-w-full">
          <thead className="bg-gray-50">
            <tr>
              <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase">
                ID
              </th>
              <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase">
                Ім'я
              </th>
              <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase">
                Email
              </th>
              <th className="px-6 py-3 text-right text-xs font-medium text-gray-500 uppercase">
                Дії
              </th>
            </tr>
          </thead>
          <tbody className="divide-y divide-gray-200">
            {users.map((user) => (
              <tr key={user.id}>
                <td className="px-6 py-4">{user.id}</td>
                <td className="px-6 py-4">{user.name}</td>
                <td className="px-6 py-4">{user.email}</td>
                <td className="px-6 py-4 text-right space-x-2">
                  <Link to={`/users/${user.id}`}>
                    <Button size="sm" variant="secondary">
                      Переглянути
                    </Button>
                  </Link>
                  <Link to={`/users/${user.id}/edit`}>
                    <Button size="sm" variant="secondary">
                      Редагувати
                    </Button>
                  </Link>
                  <Button
                    size="sm"
                    variant="danger"
                    onClick={() => {
                      if (confirm('Видалити користувача?')) {
                        deleteUser(user.id);
                      }
                    }}
                  >
                    Видалити
                  </Button>
                </td>
              </tr>
            ))}
          </tbody>
        </table>
      </div>
    </div>
  );
};

export default UsersListPage;
```

### Крок 6. Створення форми додавання/редагування

Створити Zod схему для валідації користувача у файлі `src/schemas/user.schema.ts`.

```typescript
import { z } from 'zod';

export const userSchema = z.object({
  name: z.string().min(2, "Ім'я має містити мінімум 2 символи"),
  email: z.string().email('Невірний формат email'),
  age: z.number().min(18, 'Вік має бути не менше 18').optional(),
  phone: z.string().regex(/^\+380\d{9}$/, 'Невірний формат телефону').optional(),
});

export type UserFormData = z.infer<typeof userSchema>;
```

Створити сторінку створення користувача у файлі `src/pages/UserCreatePage.tsx`.

```typescript
import React from 'react';
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { useNavigate } from 'react-router-dom';
import { userSchema, UserFormData } from '../schemas/user.schema';
import { usersService } from '../services/users.service';
import Button from '../components/common/Button';
import Input from '../components/common/Input';

const UserCreatePage: React.FC = () => {
  const navigate = useNavigate();

  const {
    register,
    handleSubmit,
    formState: { errors, isSubmitting },
    setError,
  } = useForm<UserFormData>({
    resolver: zodResolver(userSchema),
  });

  const onSubmit = async (data: UserFormData) => {
    try {
      await usersService.create(data);
      navigate('/users');
    } catch (error: any) {
      setError('root', {
        message: error.response?.data?.message || 'Помилка створення',
      });
    }
  };

  return (
    <div className="max-w-2xl mx-auto">
      <h1 className="text-3xl font-bold mb-6">Новий користувач</h1>

      <form onSubmit={handleSubmit(onSubmit)} className="bg-white rounded-lg shadow p-6 space-y-4">
        <div>
          <label className="block text-sm font-medium mb-1">Ім'я</label>
          <Input {...register('name')} placeholder="Іван Петренко" />
          {errors.name && (
            <p className="text-red-500 text-sm mt-1">{errors.name.message}</p>
          )}
        </div>

        <div>
          <label className="block text-sm font-medium mb-1">Email</label>
          <Input type="email" {...register('email')} placeholder="ivan@example.com" />
          {errors.email && (
            <p className="text-red-500 text-sm mt-1">{errors.email.message}</p>
          )}
        </div>

        <div>
          <label className="block text-sm font-medium mb-1">Вік (опціонально)</label>
          <Input
            type="number"
            {...register('age', { valueAsNumber: true })}
            placeholder="25"
          />
          {errors.age && (
            <p className="text-red-500 text-sm mt-1">{errors.age.message}</p>
          )}
        </div>

        <div>
          <label className="block text-sm font-medium mb-1">Телефон (опціонально)</label>
          <Input {...register('phone')} placeholder="+380123456789" />
          {errors.phone && (
            <p className="text-red-500 text-sm mt-1">{errors.phone.message}</p>
          )}
        </div>

        {errors.root && (
          <p className="text-red-500 text-sm">{errors.root.message}</p>
        )}

        <div className="flex gap-4">
          <Button type="submit" variant="primary" isLoading={isSubmitting}>
            Створити
          </Button>
          <Button
            type="button"
            variant="secondary"
            onClick={() => navigate('/users')}
          >
            Скасувати
          </Button>
        </div>
      </form>
    </div>
  );
};

export default UserCreatePage;
```

### Крок 7. Додавання toast сповіщень

Встановити бібліотеку для toast повідомлень.

```bash
npm install react-hot-toast
```

Додати ToastProvider у файл `src/App.tsx`.

```typescript
import { Toaster } from 'react-hot-toast';

const App: React.FC = () => {
  return (
    <BrowserRouter>
      <Toaster position="top-right" />
      {/* Routes */}
    </BrowserRouter>
  );
};
```

Використовувати toast у компонентах.

```typescript
import toast from 'react-hot-toast';

const onSubmit = async (data: UserFormData) => {
  try {
    await usersService.create(data);
    toast.success('Користувача успішно створено');
    navigate('/users');
  } catch (error: any) {
    toast.error(error.response?.data?.message || 'Помилка створення');
  }
};
```

### Крок 8. Додавання пошуку та фільтрації

Розширити store для підтримки пошуку у файлі `src/stores/usersStore.ts`.

```typescript
interface UsersState {
  // ... інші поля
  searchQuery: string;
  setSearchQuery: (query: string) => void;
  filteredUsers: () => User[];
}

export const useUsersStore = create<UsersState>((set, get) => ({
  // ... інші методи
  searchQuery: '',

  setSearchQuery: (query: string) => set({ searchQuery: query }),

  filteredUsers: () => {
    const { users, searchQuery } = get();
    if (!searchQuery) return users;

    return users.filter((user) =>
      user.name.toLowerCase().includes(searchQuery.toLowerCase()) ||
      user.email.toLowerCase().includes(searchQuery.toLowerCase())
    );
  },
}));
```

Додати компонент пошуку у список користувачів.

```typescript
const UsersListPage: React.FC = () => {
  const { searchQuery, setSearchQuery, filteredUsers } = useUsersStore();
  const users = filteredUsers();

  return (
    <div>
      <Input
        placeholder="Пошук користувачів..."
        value={searchQuery}
        onChange={(e) => setSearchQuery(e.target.value)}
        className="mb-4"
      />
      {/* Таблиця користувачів */}
    </div>
  );
};
```

### Крок 9. Створення skeleton loaders

Створити компонент Skeleton у файлі `src/components/common/Skeleton.tsx`.

```typescript
import React from 'react';

interface SkeletonProps {
  className?: string;
}

const Skeleton: React.FC<SkeletonProps> = ({ className = '' }) => {
  return (
    <div className={`animate-pulse bg-gray-200 rounded ${className}`} />
  );
};

export const TableSkeleton: React.FC = () => {
  return (
    <div className="space-y-4">
      {[...Array(5)].map((_, i) => (
        <div key={i} className="flex gap-4">
          <Skeleton className="h-12 w-12" />
          <Skeleton className="h-12 flex-1" />
          <Skeleton className="h-12 w-32" />
        </div>
      ))}
    </div>
  );
};

export default Skeleton;
```

Використовувати skeleton під час завантаження.

```typescript
if (loading) {
  return <TableSkeleton />;
}
```

### Крок 10. Оновлення README.md

Оновити файл `README.md` у корені проєкту, додавши інформацію про нову функціональність:

```markdown
# Назва проєкту

## Опис проєкту
[Оновлений опис з урахуванням нового функціоналу]

## Функціональність
- ✅ Система аутентифікації (JWT)
- ✅ Protected Routes
- ✅ CRUD операції для всіх сутностей
- ✅ Валідація форм (React Hook Form + Zod)
- ✅ Управління станом (Zustand)
- ✅ Toast сповіщення
- ✅ Пошук та фільтрація
- ✅ Skeleton loaders

## Технології
- React 18 + TypeScript
- Vite
- Tailwind CSS
- React Router v6
- Axios
- React Hook Form
- Zod
- Zustand
- React Hot Toast

## API Endpoints
Опис основних endpoint-ів backend API, з якими взаємодіє frontend.

## Встановлення та запуск

### Вимоги
- Node.js 18+
- Backend сервер запущений на localhost:3000

### Інструкції
\`\`\`bash
# Клонування репозиторію
git clone [URL репозиторію]

# Встановлення залежностей
npm install

# Налаштування .env
cp .env.example .env
# Встановити VITE_API_URL=http://localhost:3000/api

# Запуск у режимі розробки
npm run dev
\`\`\`

## Структура проєкту
\`\`\`
src/
├── components/
│   ├── common/        # Базові UI компоненти
│   ├── layout/        # Layout компоненти
│   └── ProtectedRoute.tsx
├── pages/             # Сторінки застосунку
├── services/          # API сервіси
├── stores/            # Zustand stores
├── schemas/           # Zod валідаційні схеми
├── types/             # TypeScript типи
└── hooks/             # Кастомні hooks
\`\`\`

## Скріншоти
[Додати скріншоти: login, dashboard, список користувачів, форма створення]

## Використання

### Аутентифікація
\`\`\`
Email: demo@example.com
Password: password123
\`\`\`

## Автор
[Ваше ім'я, група]
```

### Крок 11. Здача роботи

Зробити коміт усіх змін до GitHub репозиторію з описовим повідомленням. Переконатися, що:
- README.md актуалізовано з новою функціональністю;
- додані скріншоти основних екранів;
- застосунок працює коректно з backend API.

Здати роботу через Moodle, вставивши посилання на GitHub репозиторій.

[⬆️ Здати лабораторну роботу](https://moodle.vcolnuft.volyn.ua/moodle/course/view.php?id=17#section-2)

## ❓ Контрольні запитання

1. Поясніть різницю між контрольованими та неконтрольованими компонентами у React. Який підхід використовує React Hook Form?
2. Як працює JWT аутентифікація? Де зберігається токен на клієнті та які є альтернативи?
3. Що таке Zod та які переваги він надає порівняно з іншими бібліотеками валідації?
4. Поясніть концепцію Protected Routes та як вона реалізується у React Router.
5. Порівняйте Context API та Zustand для управління станом. Коли краще використовувати кожен з підходів?
6. Що таке optimistic updates та коли їх доцільно використовувати у CRUD операціях?
7. Як реалізувати централізовану обробку помилок у React застосунку?
8. Поясніть паттерн Container/Presentational компонентів та його переваги.
9. Що таке skeleton loaders та як вони покращують користувацький досвід?
10. Як забезпечити синхронізацію локального стану з даними на сервері після CRUD операцій?
