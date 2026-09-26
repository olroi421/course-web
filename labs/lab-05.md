# Лабораторна робота 05 Розроблення основного інтерфейсу та функціоналу

## 🎯 Мета роботи

Здобути практичні навички створення повнофункціонального користувацького інтерфейсу із системою аутентифікації, керуванням станом, валідацією форм та реалізацією CRUD-операцій для взаємодії з API серверної частини.

## ✅ Завдання

### Загальний контекст

Ця лабораторна робота продовжує розробку клієнтської частини проєкту, розпочату в лабораторній роботі 4. Здобувачі освіти інтегрують клієнтську частину з API серверної частини (лабораторні роботи 2–3), створюють повноцінні інтерфейси для роботи з даними та реалізують систему аутентифікації користувачів.

### Технічні завдання

**Рівень 1. Система аутентифікації та базова панель керування (Dashboard)**

1. Створити сторінки Login та Register з відповідними формами.
2. Реалізувати логіку аутентифікації через Context API або Zustand.
3. Створити захищені маршрути (Protected Routes) для сторінок, що потребують входу.
4. Розробити головну панель керування (Dashboard) з навігаційним меню.
5. Реалізувати вихід із системи (logout).
6. Додати індикатори стану завантаження під час запитів.
7. Створити базовий компонент для відображення помилок.

**Рівень 2. CRUD інтерфейси та валідація**

8. Створити сторінки для відображення списків основних сутностей проєкту.
9. Реалізувати сторінки для створення нових записів з формами.
10. Додати сторінки для редагування існуючих записів.
11. Реалізувати підтвердження та функціональність видалення записів.
12. Впровадити валідацію форм через React Hook Form та Zod схеми.
13. Створити повторно використовувані компоненти для відображення даних: Table, List, Grid.
14. Додати пагінацію для списків з великою кількістю записів.

**Рівень 3. Розширений UI/UX та стан**

15. Реалізувати глобальне керування станом застосунку: Zustand для клієнтського стану (сесія користувача), TanStack Query для даних, отриманих із сервера.
16. Створити систему сповіщень про успіх і помилки (toast).
17. Додати функціональність пошуку та фільтрації у списках.
18. Реалізувати сортування даних за різними критеріями.
19. Створити детальні сторінки для перегляду окремих записів.
20. Додати навігацію «хлібні крихти» (breadcrumbs) для зручності користувача.
21. Реалізувати скелетні індикатори завантаження (skeleton loaders).
22. Створити адаптивну бічну панель (sidebar) із можливістю згортання на мобільних пристроях.

### Результат виконання

Після завершення лабораторної роботи здобувач освіти матиме повністю функціональний інтерфейс із системою аутентифікації, CRUD-операціями для всіх основних сутностей проєкту, валідацією форм та зручним користувацьким досвідом.

## 👥 Форма виконання роботи

Форма виконання роботи **індивідуальна**.

## 📝 Критерії оцінювання

**Середній рівень (оцінка "задовільно")**

- Реалізовано базову систему аутентифікації зі сторінками входу та реєстрації.
- Створено прості CRUD-сторінки для 1–2 сутностей.
- Форми працюють без валідації або з базовою валідацією.
- Відсутнє глобальне керування станом.
- Інтерфейс має базову функціональність без додаткових покращень UX.
- Немає обробки помилок або вона мінімальна.
- Код має недоліки у структурі та організації.

**Достатній рівень (оцінка "добре")**

- Повністю реалізована система аутентифікації із захищеними маршрутами (Protected Routes).
- Створено CRUD-інтерфейси для основних сутностей проєкту.
- Впроваджена валідація форм через React Hook Form.
- Використовується Context API (або Zustand) для керування станом.
- Додані базові індикатори завантаження та помилок.
- Реалізована пагінація для списків.
- Інтерфейс є адаптивним та зручним для користувача.
- Код організовано у логічні модулі.

**Високий рівень (оцінка "відмінно")**

- Повністю виконано всі завдання трьох рівнів.
- Реалізована розширена система керування станом із Zustand або аналогічним рішенням.
- Впроваджена складна валідація з Zod схемами.
- Створена система сповіщень (toast).
- Додані пошук, фільтрація та сортування у списках.
- Реалізовані скелетні індикатори завантаження та інші покращення UX.
- Код відповідає принципам чистого коду та має модульну структуру.
- Створена детальна документація компонентів.
- Продемонстровано глибоке розуміння шаблонів проєктування React та керування станом.

## ⏰ Політика щодо дедлайнів

При порушенні встановленого терміну здачі лабораторної роботи максимальна можлива оцінка становить "добре", незалежно від якості виконаної роботи. Винятки можливі лише за поважних причин, підтверджених документально.

## 📚 Теоретичні відомості

### Аутентифікація в React-застосунках

**Аутентифікація** — це процес перевірки особи користувача у вебзастосунку (на відміну від **авторизації**, яка визначає, що саме йому дозволено). У React-застосунках вона зазвичай ґрунтується на токенах JWT: після успішного входу сервер повертає токен доступу, клієнт зберігає його й додає до кожного запиту в заголовок `Authorization: Bearer <токен>`.

Типовий потік: клієнт надсилає облікові дані на сервер, отримує у відповіді токени (сервер із лабораторної роботи 2 повертає `{ user, tokens: { accessToken, refreshToken } }`), зберігає токен доступу, додає його до запитів та перенаправляє користувача на захищену сторінку. Після отримання відповіді 401 сесію слід завершити й очистити збережений токен.

Де зберігати токен? Найпростіший варіант — `localStorage`: він переживає перезавантаження сторінки, але доступний будь-якому JavaScript на сторінці, тому XSS-вразливість дозволить його викрасти. Безпечніша схема, яка використовується в промислових системах, — тримати токен оновлення в `httpOnly`-кукі (недоступній для JavaScript), а короткоживучий токен доступу лише в пам'яті застосунку. У навчальній роботі ми свідомо обираємо простіший варіант і зберігаємо токен доступу в `localStorage` через Zustand, але ви маєте розуміти його обмеження й уміти пояснити, чому токен доступу має жити недовго (у лабораторній роботі 2 — 15 хвилин).

### React Hook Form

**React Hook Form** — це бібліотека для роботи з формами, яка реєструє поля через `ref` і читає їхні значення безпосередньо з DOM. На відміну від «контрольованих» форм, де кожне натискання клавіші змінює стан React і перемальовує компонент, React Hook Form майже не спричиняє повторних відтворень (ре-рендерів), тому добре працює навіть зі складними формами.

Основні можливості: реєстрація полів функцією `register`, обробка надсилання через `handleSubmit`, стан форми (`errors`, `isSubmitting`, `isDirty`), ручне встановлення помилок (`setError`, зокрема для помилок, які повернув сервер), спостереження за значеннями (`watch`), вкладені об'єкти та масиви полів. Бібліотека інтегрується з Zod, Yup та іншими засобами валідації через пакет `@hookform/resolvers`. Оскільки `register` повертає `ref`, ваш власний компонент `Input` має передавати його на нативний `<input>`: у React 19 достатньо звичайного пропа `ref` (обгортка `forwardRef` більше не потрібна) — саме так реалізовано `Input` у лабораторній роботі 4.

### Zod: схеми та валідація

**Zod** — це TypeScript-first бібліотека для опису схем даних і їх перевірки. Одна схема водночас є правилами валідації й джерелом типів (`z.infer<typeof schema>`), тож типи форми та правила не розходяться.

У лабораторній роботі використовується Zod 4. Порівняно з третьою версією змінився синтаксис: для формату email застосовують `z.email()` замість `z.string().email()`, а текст помилки задають параметром `error` (`{ error: 'Повідомлення' }`) замість `message`. Для форм важливо пам'ятати, що поле `<input>` завжди дає рядок: порожнє необов'язкове числове поле — це `''`, а не `undefined`, тому перед перевіркою числа порожній рядок перетворюють на `undefined` (`z.preprocess`). Через такі перетворення в схеми є два типи — **вхідний** (`z.input`, що вводить користувач) і **вихідний** (`z.output`, що отримує обробник після валідації); React Hook Form 7 дозволяє вказати обидва: `useForm<Input, Context, Output>`.

### Керування станом у React: клієнтський стан і дані сервера

У застосунку зазвичай співіснують два різні види стану, і змішувати їх в одному сховищі — типова помилка.

**Клієнтський стан** належить лише застосунку: чи відкрите меню, яка тема, хто ввійшов у систему. Для нього підходять `useState`, Context API або **Zustand**. **Context API** — вбудований у React механізм передавання даних деревом компонентів без «прокидання пропсів» (prop drilling), але зміна значення контексту перемальовує всіх його споживачів. **Zustand** — легка бібліотека, що не потребує провайдерів: створюється сховище (store), а компоненти підписуються на потрібну частину стану через селектори й перемальовуються лише тоді, коли ця частина змінилася. Middleware `persist` автоматично зберігає стан у `localStorage`.

**Дані сервера** (списки користувачів, товарів, замовлень) — це кеш чужих даних, які можуть змінитися без відома застосунку. Для них ручне зберігання у Zustand (`loading`, `error`, `fetchUsers`, `deleteUser`) вимагає самостійно реалізовувати кешування, повторні запити, скасування застарілих відповідей і синхронізацію після змін. Ці завдання вирішує **TanStack Query** (раніше React Query): `useQuery` виконує запит і кешує результат за **ключем запиту** (`['users', { page, search }]`), а `useMutation` виконує зміну на сервері, після якої ми позначаємо відповідні ключі застарілими через `invalidateQueries`, і бібліотека сама перезавантажує актуальні дані. Тому в цій роботі Zustand відповідає лише за сесію користувача, а всі списки й записи з сервера отримуються через TanStack Query.

### Захищені маршрути (Protected Routes)

**Захищений маршрут** — це шаблон, за яким компонент-обгортка перевіряє, чи користувач аутентифікований, і або показує сторінку, або перенаправляє на сторінку входу (компонент `Navigate`). Щоб після входу повернути користувача туди, куди він ішов, поточне розташування передають у `state` перенаправлення й зчитують на сторінці входу через `useLocation`.

Захист на клієнті — це лише зручність інтерфейсу: користувач може змінити код у браузері. Справжню безпеку забезпечує серверна частина, яка перевіряє токен у кожному запиті (`authMiddleware` із лабораторної роботи 2) і повертає 401 або 403.

### CRUD-операції в React

**CRUD (Create, Read, Update, Delete)** — базові операції над даними. Типовий цикл в інтерфейсі: виклик API (Axios), показ стану завантаження (індикатор або скелет), відображення результату або помилки, сповіщення про успіх і синхронізація відображуваних даних із сервером після зміни.

Синхронізувати дані можна по-різному. Найнадійніший спосіб — після успішної мутації перезавантажити дані з сервера (`invalidateQueries`): сервер залишається єдиним джерелом істини. Швидший для користувача, але складніший підхід — **оптимістичне оновлення**: інтерфейс змінюється одразу, ще до відповіді сервера, а в разі помилки зміни відкочуються. Його доцільно застосовувати для дій, які майже завжди успішні (позначка «вподобано», перемикання статусу), і не варто — для критичних операцій (оплата, видалення без можливості скасування).

## 🔗 Додаткові ресурси

- [React Hook Form документація](https://react-hook-form.com/)
- [Zod документація](https://zod.dev/)
- [Zod 4: що змінилося](https://zod.dev/v4/changelog)
- [Zustand документація](https://zustand.docs.pmnd.rs/)
- [TanStack Query документація](https://tanstack.com/query/latest)
- [React Router: навігація та `Navigate`](https://reactrouter.com/)
- [JWT: вступ](https://jwt.io/introduction)
- [React: Context API](https://react.dev/reference/react/useContext)
- [OWASP: зберігання токенів та XSS](https://cheatsheetseries.owasp.org/cheatsheets/HTML5_Security_Cheat_Sheet.html#local-storage)

## ▶️ Хід роботи

### Крок 1. Налаштування керування станом

Встановити Zustand для клієнтського стану.

```bash
npm install zustand
```

Створити сховище аутентифікації у файлі `src/stores/authStore.ts`. Це **єдине** місце, де живе сесія користувача: middleware `persist` сам зберігає її в `localStorage` (ключ `auth-storage`) і відновлює після перезавантаження сторінки, тож дублювати токен у `localStorage` вручну не потрібно.

```typescript
import { create } from 'zustand';
import { persist } from 'zustand/middleware';
import type { User } from '@/types/api.types';

interface AuthState {
  user: User | null;
  accessToken: string | null;
  login: (user: User, accessToken: string) => void;
  logout: () => void;
}

// Єдине джерело правди про сесію: persist сам зберігає стан у localStorage
export const useAuthStore = create<AuthState>()(
  persist(
    (set) => ({
      user: null,
      accessToken: null,
      login: (user, accessToken) => set({ user, accessToken }),
      logout: () => set({ user: null, accessToken: null }),
    }),
    { name: 'auth-storage' }
  )
);
```

Оновити `src/services/api.ts` з лабораторної роботи 4: interceptor тепер бере токен зі сховища, а при відповіді 401 завершує сесію. Перенаправлення на `/login` виконає `ProtectedRoute` (крок 3), тому `window.location.href` більше не потрібен, і застосунок не перезавантажується.

```typescript
import axios from 'axios';
import { useAuthStore } from '@/stores/authStore';

const api = axios.create({
  baseURL: import.meta.env.VITE_API_URL ?? 'http://localhost:3000/api',
  headers: { 'Content-Type': 'application/json' },
});

// Interceptor запиту: додає токен доступу зі сховища Zustand
api.interceptors.request.use((config) => {
  const token = useAuthStore.getState().accessToken;
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});

// Interceptor відповіді: при 401 завершує сесію.
// ProtectedRoute побачить порожній токен і сам перенаправить на /login.
api.interceptors.response.use(
  (response) => response,
  (error) => {
    if (error.response?.status === 401) {
      useAuthStore.getState().logout();
    }
    return Promise.reject(error);
  }
);

export default api;
```

Додати тип відповіді аутентифікації до `src/types/api.types.ts` (форма відповіді збігається з сервером із лабораторної роботи 2).

```typescript
export interface AuthResponse {
  message: string;
  user: User;
  tokens: { accessToken: string; refreshToken: string };
}
```

### Крок 2. Створення форм аутентифікації

Встановити бібліотеки для роботи з формами. Для Zod потрібна версія 4.

```bash
npm install react-hook-form @hookform/resolvers zod
```

Створити схеми валідації у файлі `src/schemas/auth.schema.ts`. Мінімальна довжина пароля (8 символів) збігається з перевіркою на сервері.

```typescript
import { z } from 'zod';

export const loginSchema = z.object({
  email: z.email({ error: 'Невірний формат email' }),
  password: z.string().min(8, { error: 'Пароль має містити мінімум 8 символів' }),
});

export const registerSchema = z
  .object({
    name: z.string().min(2, { error: "Ім'я має містити мінімум 2 символи" }),
    email: z.email({ error: 'Невірний формат email' }),
    password: z.string().min(8, { error: 'Пароль має містити мінімум 8 символів' }),
    confirmPassword: z.string(),
  })
  .refine((data) => data.password === data.confirmPassword, {
    error: 'Паролі не збігаються',
    path: ['confirmPassword'],
  });

export type LoginFormData = z.infer<typeof loginSchema>;
export type RegisterFormData = z.infer<typeof registerSchema>;
```

Щоб не дублювати розмітку «підпис + поле + помилка» у кожній формі, створити компонент `src/components/common/FormField.tsx`.

```typescript
import type { ReactNode } from 'react';

interface FormFieldProps {
  label: string;
  error?: string;
  children: ReactNode;
}

// Підпис + поле + повідомлення про помилку: прибирає дублювання розмітки в усіх формах
export default function FormField({ label, error, children }: FormFieldProps) {
  return (
    <label className="block">
      <span className="mb-1 block text-sm font-medium">{label}</span>
      {children}
      {error && (
        <span role="alert" className="mt-1 block text-sm text-red-500">
          {error}
        </span>
      )}
    </label>
  );
}
```

Створити сторінку входу `src/pages/LoginPage.tsx`. Зверніть увагу: сервер повертає токен у `res.tokens.accessToken`, а помилки сервера показуємо через `setError('root', ...)`.

```typescript
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { Link, useLocation, useNavigate } from 'react-router';
import { isAxiosError } from 'axios';
import { loginSchema, type LoginFormData } from '@/schemas/auth.schema';
import { useAuthStore } from '@/stores/authStore';
import api from '@/services/api';
import type { AuthResponse } from '@/types/api.types';
import Button from '@/components/common/Button';
import Input from '@/components/common/Input';
import FormField from '@/components/common/FormField';

export default function LoginPage() {
  const navigate = useNavigate();
  const location = useLocation();
  const login = useAuthStore((state) => state.login);
  // Куди повернути користувача після входу (його зберіг ProtectedRoute)
  const from = (location.state as { from?: { pathname: string } } | null)?.from?.pathname ?? '/dashboard';

  const {
    register,
    handleSubmit,
    setError,
    formState: { errors, isSubmitting },
  } = useForm<LoginFormData>({ resolver: zodResolver(loginSchema) });

  const onSubmit = async (data: LoginFormData) => {
    try {
      const { data: res } = await api.post<AuthResponse>('/auth/login', data);
      login(res.user, res.tokens.accessToken);
      navigate(from, { replace: true });
    } catch (error) {
      const message = isAxiosError(error) ? error.response?.data?.error : undefined;
      setError('root', { message: message ?? 'Помилка входу' });
    }
  };

  return (
    <div className="flex min-h-screen items-center justify-center bg-gray-50 dark:bg-gray-950">
      <div className="w-full max-w-md rounded-lg bg-white p-8 shadow-lg dark:bg-gray-900">
        <h2 className="mb-6 text-center text-3xl font-bold">Вхід</h2>

        <form onSubmit={handleSubmit(onSubmit)} className="space-y-4" noValidate>
          <FormField label="Email" error={errors.email?.message}>
            <Input type="email" placeholder="your@email.com" {...register('email')} />
          </FormField>

          <FormField label="Пароль" error={errors.password?.message}>
            <Input type="password" placeholder="••••••••" {...register('password')} />
          </FormField>

          {errors.root && (
            <p role="alert" className="text-sm text-red-500">
              {errors.root.message}
            </p>
          )}

          <Button type="submit" className="w-full" isLoading={isSubmitting}>
            Увійти
          </Button>
        </form>

        <p className="mt-4 text-center text-sm">
          Немає акаунта?{' '}
          <Link to="/register" className="text-primary-600 hover:underline">
            Зареєструватися
          </Link>
        </p>
      </div>
    </div>
  );
}
```

Сторінку реєстрації `src/pages/RegisterPage.tsx` створіть за аналогією. Поле `confirmPassword` призначене лише для перевірки на клієнті, тому на сервер його не надсилають.

```typescript
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { Link, useNavigate } from 'react-router';
import { isAxiosError } from 'axios';
import toast from 'react-hot-toast';
import { registerSchema, type RegisterFormData } from '@/schemas/auth.schema';
import api from '@/services/api';
import Button from '@/components/common/Button';
import Input from '@/components/common/Input';
import FormField from '@/components/common/FormField';

export default function RegisterPage() {
  const navigate = useNavigate();
  const {
    register,
    handleSubmit,
    setError,
    formState: { errors, isSubmitting },
  } = useForm<RegisterFormData>({ resolver: zodResolver(registerSchema) });

  const onSubmit = async ({ name, email, password }: RegisterFormData) => {
    try {
      // confirmPassword на сервер не надсилаємо
      await api.post('/auth/register', { name, email, password });
      toast.success('Обліковий запис створено. Тепер увійдіть.');
      navigate('/login');
    } catch (error) {
      const message = isAxiosError(error) ? error.response?.data?.error : undefined;
      setError('root', { message: message ?? 'Помилка реєстрації' });
    }
  };

  return (
    <div className="flex min-h-screen items-center justify-center bg-gray-50 dark:bg-gray-950">
      <div className="w-full max-w-md rounded-lg bg-white p-8 shadow-lg dark:bg-gray-900">
        <h2 className="mb-6 text-center text-3xl font-bold">Реєстрація</h2>

        <form onSubmit={handleSubmit(onSubmit)} className="space-y-4" noValidate>
          <FormField label="Ім'я" error={errors.name?.message}>
            <Input {...register('name')} />
          </FormField>
          <FormField label="Email" error={errors.email?.message}>
            <Input type="email" {...register('email')} />
          </FormField>
          <FormField label="Пароль" error={errors.password?.message}>
            <Input type="password" {...register('password')} />
          </FormField>
          <FormField label="Підтвердження пароля" error={errors.confirmPassword?.message}>
            <Input type="password" {...register('confirmPassword')} />
          </FormField>

          {errors.root && (
            <p role="alert" className="text-sm text-red-500">
              {errors.root.message}
            </p>
          )}

          <Button type="submit" className="w-full" isLoading={isSubmitting}>
            Зареєструватися
          </Button>
        </form>

        <p className="mt-4 text-center text-sm">
          Вже є акаунт?{' '}
          <Link to="/login" className="text-primary-600 hover:underline">
            Увійти
          </Link>
        </p>
      </div>
    </div>
  );
}
```

### Крок 3. Створення захищених маршрутів

Створити компонент для захищених маршрутів у файлі `src/components/ProtectedRoute.tsx`. Він зберігає поточне розташування в `state.from`, щоб після входу повернути користувача на потрібну сторінку.

```typescript
import type { ReactNode } from 'react';
import { Navigate, useLocation } from 'react-router';
import { useAuthStore } from '@/stores/authStore';

interface ProtectedRouteProps {
  children: ReactNode;
}

export default function ProtectedRoute({ children }: ProtectedRouteProps) {
  const isAuthenticated = useAuthStore((state) => state.accessToken !== null);
  const location = useLocation();

  if (!isAuthenticated) {
    // state.from дозволяє повернути користувача на потрібну сторінку після входу
    return <Navigate to="/login" replace state={{ from: location }} />;
  }

  return <>{children}</>;
}
```

Оновити маршрутизацію у файлі `src/App.tsx` (сторінки `UsersListPage` і `UserCreatePage` з'являться в кроках 6–7).

```typescript
import { BrowserRouter, Route, Routes } from 'react-router';
import Layout from '@/components/layout/Layout';
import ProtectedRoute from '@/components/ProtectedRoute';
import HomePage from '@/pages/HomePage';
import AboutPage from '@/pages/AboutPage';
import NotFoundPage from '@/pages/NotFoundPage';
import LoginPage from '@/pages/LoginPage';
import RegisterPage from '@/pages/RegisterPage';
import DashboardPage from '@/pages/DashboardPage';
import UsersListPage from '@/pages/UsersListPage';
import UserCreatePage from '@/pages/UserCreatePage';

export default function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/login" element={<LoginPage />} />
        <Route path="/register" element={<RegisterPage />} />

        <Route path="/" element={<Layout />}>
          <Route index element={<HomePage />} />
          <Route path="about" element={<AboutPage />} />
          <Route
            path="dashboard"
            element={
              <ProtectedRoute>
                <DashboardPage />
              </ProtectedRoute>
            }
          />
          <Route
            path="users"
            element={
              <ProtectedRoute>
                <UsersListPage />
              </ProtectedRoute>
            }
          />
          <Route
            path="users/create"
            element={
              <ProtectedRoute>
                <UserCreatePage />
              </ProtectedRoute>
            }
          />
          <Route path="*" element={<NotFoundPage />} />
        </Route>
      </Routes>
    </BrowserRouter>
  );
}
```

### Крок 4. Створення панелі керування та виходу із системи

Створити головну сторінку Dashboard у файлі `src/pages/DashboardPage.tsx`. Картки розділів описано масивом, а не повторено розміткою.

```typescript
import { Link } from 'react-router';
import { useAuthStore } from '@/stores/authStore';

const sections = [
  { to: '/users', title: 'Користувачі', text: 'Керування користувачами системи' },
  { to: '/products', title: 'Продукти', text: 'Каталог продуктів' },
  { to: '/orders', title: 'Замовлення', text: 'Перегляд та керування замовленнями' },
];

export default function DashboardPage() {
  const user = useAuthStore((state) => state.user);

  return (
    <div>
      <h1 className="mb-6 text-3xl font-bold">Вітаємо, {user?.name}!</h1>

      <div className="grid grid-cols-1 gap-6 md:grid-cols-3">
        {sections.map((s) => (
          <Link
            key={s.to}
            to={s.to}
            className="rounded-lg bg-white p-6 shadow transition-shadow hover:shadow-lg dark:bg-gray-900"
          >
            <h3 className="mb-2 text-xl font-semibold">{s.title}</h3>
            <p className="text-gray-600 dark:text-gray-400">{s.text}</p>
          </Link>
        ))}
      </div>
    </div>
  );
}
```

Оновити `src/components/layout/Header.tsx`: для аутентифікованого користувача показати посилання на розділи, ім'я та кнопку виходу; для гостя — посилання «Увійти».

```typescript
import { Link, useNavigate } from 'react-router';
import { useTheme } from '@/hooks/useTheme';
import { useAuthStore } from '@/stores/authStore';
import Button from '@/components/common/Button';

export default function Header() {
  const { theme, toggleTheme } = useTheme();
  const user = useAuthStore((state) => state.user);
  const logout = useAuthStore((state) => state.logout);
  const navigate = useNavigate();

  const handleLogout = () => {
    logout();
    navigate('/login');
  };

  return (
    <header className="bg-white shadow-md dark:bg-gray-900">
      <nav className="container mx-auto flex items-center justify-between px-4 py-4">
        <Link to="/" className="text-2xl font-bold text-primary-600">
          MyApp
        </Link>
        <div className="flex items-center gap-6">
          <Link to="/" className="hover:text-primary-600">
            Головна
          </Link>
          <Link to="/about" className="hover:text-primary-600">
            Про нас
          </Link>
          {user ? (
            <>
              <Link to="/dashboard" className="hover:text-primary-600">
                Панель
              </Link>
              <Link to="/users" className="hover:text-primary-600">
                Користувачі
              </Link>
              <span className="text-sm text-gray-500">{user.name}</span>
              <Button size="sm" variant="secondary" onClick={handleLogout}>
                Вийти
              </Button>
            </>
          ) : (
            <Link to="/login" className="hover:text-primary-600">
              Увійти
            </Link>
          )}
          <button type="button" onClick={toggleTheme} aria-label="Перемкнути тему">
            {theme === 'dark' ? '☀️' : '🌙'}
          </button>
        </div>
      </nav>
    </header>
  );
}
```

### Крок 5. Робота з даними сервера через TanStack Query

Встановити TanStack Query та бібліотеку сповіщень (використовується в кроках 6–8).

```bash
npm install @tanstack/react-query react-hot-toast
```

Підключити `QueryClientProvider` (і `Toaster` для сповіщень із кроку 8) у файлі `src/main.tsx`. Параметр `staleTime` визначає, як довго дані вважаються свіжими й не запитуються повторно.

```typescript
import { StrictMode } from 'react';
import { createRoot } from 'react-dom/client';
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';
import { Toaster } from 'react-hot-toast';
import App from './App';
import './index.css';

const queryClient = new QueryClient({
  defaultOptions: {
    queries: { staleTime: 30_000, retry: 1 }, // 30 с дані вважаються свіжими
  },
});

createRoot(document.getElementById('root')!).render(
  <StrictMode>
    <QueryClientProvider client={queryClient}>
      <Toaster position="top-right" />
      <App />
    </QueryClientProvider>
  </StrictMode>
);
```

Створити сервіс для роботи з сутністю у файлі `src/services/users.service.ts`. Він лише виконує HTTP-запити й повертає дані; кешуванням і станом завантаження займеться TanStack Query. Список повертається у форматі з лабораторної роботи 2: `{ data, pagination }`. Адаптуйте адреси та типи до власних сутностей (`products`, `orders` тощо).

```typescript
import api from './api';
import type { PaginatedResponse, User } from '@/types/api.types';
import type { UserFormData } from '@/schemas/user.schema';

export interface UsersQuery {
  page?: number;
  limit?: number;
  search?: string;
}

export const usersService = {
  getAll: async (params: UsersQuery = {}) => {
    const { data } = await api.get<PaginatedResponse<User>>('/users', { params });
    return data;
  },

  getById: async (id: number) => {
    const { data } = await api.get<User>(`/users/${id}`);
    return data;
  },

  create: async (payload: UserFormData) => {
    const { data } = await api.post<User>('/users', payload);
    return data;
  },

  update: async (id: number, payload: Partial<UserFormData>) => {
    const { data } = await api.put<User>(`/users/${id}`, payload);
    return data;
  },

  remove: async (id: number) => {
    await api.delete(`/users/${id}`);
  },
};
```

### Крок 6. Сторінка зі списком: пагінація, пошук і видалення

Пошук має надсилати запит не після кожного натискання клавіші, а після паузи в наборі. Для цього створити хук `src/hooks/useDebounce.ts`.

```typescript
import { useEffect, useState } from 'react';

// Повертає значення із затримкою: запит пошуку йде, коли користувач зробив паузу в наборі
export function useDebounce<T>(value: T, delay = 300): T {
  const [debounced, setDebounced] = useState(value);

  useEffect(() => {
    const timer = setTimeout(() => setDebounced(value), delay);
    return () => clearTimeout(timer);
  }, [value, delay]);

  return debounced;
}
```

Створити сторінку зі списком користувачів у файлі `src/pages/UsersListPage.tsx`. Розгляньте, як вона працює:

- ключ запиту `['users', { page, search }]` містить усі параметри, тому зміна сторінки або пошукового рядка автоматично запускає новий запит, а вже отримані сторінки беруться з кешу;
- `placeholderData: keepPreviousData` залишає на екрані попередню сторінку, доки завантажується наступна, тож таблиця не «блимає»;
- після успішного видалення `invalidateQueries({ queryKey: ['users'] })` позначає застарілими всі закешовані списки користувачів, і TanStack Query перезавантажує ті, що зараз на екрані;
- станів `loading` та `error` у власному сховищі більше немає: їх дають `isPending` та `isError`.

```typescript
import { useState } from 'react';
import { Link } from 'react-router';
import { keepPreviousData, useMutation, useQuery, useQueryClient } from '@tanstack/react-query';
import toast from 'react-hot-toast';
import { usersService } from '@/services/users.service';
import { useDebounce } from '@/hooks/useDebounce';
import Button from '@/components/common/Button';
import Input from '@/components/common/Input';
import { TableSkeleton } from '@/components/common/Skeleton';

export default function UsersListPage() {
  const [page, setPage] = useState(1);
  const [search, setSearch] = useState('');
  const debouncedSearch = useDebounce(search);
  const queryClient = useQueryClient();

  // Ключ запиту містить параметри: зміна page або пошуку автоматично запускає новий запит
  const { data, isPending, isError, error } = useQuery({
    queryKey: ['users', { page, search: debouncedSearch }],
    queryFn: () => usersService.getAll({ page, limit: 10, search: debouncedSearch || undefined }),
    placeholderData: keepPreviousData, // під час завантаження сторінки лишається попередня
  });

  const deleteUser = useMutation({
    mutationFn: usersService.remove,
    onSuccess: () => {
      toast.success('Користувача видалено');
      // Сервер — джерело істини: позначаємо всі списки користувачів застарілими й перезавантажуємо
      return queryClient.invalidateQueries({ queryKey: ['users'] });
    },
    onError: () => toast.error('Не вдалося видалити користувача'),
  });

  return (
    <div>
      <div className="mb-6 flex items-center justify-between">
        <h1 className="text-3xl font-bold">Користувачі</h1>
        <Link to="/users/create">
          <Button>Додати користувача</Button>
        </Link>
      </div>

      <Input
        aria-label="Пошук користувачів"
        placeholder="Пошук користувачів..."
        value={search}
        onChange={(e) => {
          setSearch(e.target.value);
          setPage(1); // новий пошук завжди починається з першої сторінки
        }}
        className="mb-4"
      />

      {isPending && <TableSkeleton />}
      {isError && (
        <p role="alert" className="text-red-500">
          Помилка: {error.message}
        </p>
      )}

      {data && (
        <>
          <div className="overflow-hidden rounded-lg bg-white shadow dark:bg-gray-900">
            <table className="min-w-full">
              <thead className="bg-gray-50 dark:bg-gray-800">
                <tr>
                  {['ID', "Ім'я", 'Email', 'Дії'].map((title) => (
                    <th key={title} className="px-6 py-3 text-left text-xs font-medium uppercase text-gray-500">
                      {title}
                    </th>
                  ))}
                </tr>
              </thead>
              <tbody className="divide-y divide-gray-200 dark:divide-gray-700">
                {data.data.map((user) => (
                  <tr key={user.id}>
                    <td className="px-6 py-4">{user.id}</td>
                    <td className="px-6 py-4">{user.name}</td>
                    <td className="px-6 py-4">{user.email}</td>
                    <td className="space-x-2 px-6 py-4 text-right">
                      <Link to={`/users/${user.id}/edit`}>
                        <Button size="sm" variant="secondary">
                          Редагувати
                        </Button>
                      </Link>
                      <Button
                        size="sm"
                        variant="danger"
                        disabled={deleteUser.isPending}
                        onClick={() => {
                          if (confirm(`Видалити користувача ${user.name}?`)) {
                            deleteUser.mutate(user.id);
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

          <div className="mt-4 flex items-center justify-center gap-4">
            <Button variant="secondary" disabled={page === 1} onClick={() => setPage((p) => p - 1)}>
              Назад
            </Button>
            <span>
              {data.pagination.page} / {data.pagination.totalPages}
            </span>
            <Button variant="secondary" disabled={!data.pagination.hasMore} onClick={() => setPage((p) => p + 1)}>
              Далі
            </Button>
          </div>
        </>
      )}
    </div>
  );
}
```

> **Примітка.** `confirm()` — найпростіший спосіб підтвердити видалення. Для оцінки «відмінно» замініть його власним модальним вікном (див. лабораторну роботу 6).

### Крок 7. Форма створення запису

Створити схему валідації користувача у файлі `src/schemas/user.schema.ts`. Порожнє необов'язкове поле форми дає `''`, тому його потрібно перетворити на `undefined` до перевірки (без цього порожній вік не пройде валідацію числа).

```typescript
import { z } from 'zod';

export const userSchema = z.object({
  name: z.string().min(2, { error: "Ім'я має містити мінімум 2 символи" }),
  email: z.email({ error: 'Невірний формат email' }),
  // Порожнє поле форми — це '', а не undefined: перетворюємо його перед перевіркою числа
  age: z.preprocess(
    (v) => (v === '' ? undefined : v),
    z.coerce.number().int({ error: 'Вік має бути цілим числом' }).min(18, { error: 'Вік має бути не менше 18' }).optional()
  ),
  phone: z.preprocess(
    (v) => (v === '' ? undefined : v),
    z.string().regex(/^\+380\d{9}$/, { error: 'Формат телефону: +380XXXXXXXXX' }).optional()
  ),
});

// Тип входу форми (рядки з полів) і тип виходу (після перетворень) відрізняються
export type UserFormInput = z.input<typeof userSchema>;
export type UserFormData = z.output<typeof userSchema>;
```

Створити сторінку створення у файлі `src/pages/UserCreatePage.tsx`. Запис на сервер виконує `useMutation`; після успіху ми інвалідуємо кеш списку, щоб новий запис одразу з'явився.

```typescript
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { useNavigate } from 'react-router';
import { useMutation, useQueryClient } from '@tanstack/react-query';
import { isAxiosError } from 'axios';
import toast from 'react-hot-toast';
import { userSchema, type UserFormData, type UserFormInput } from '@/schemas/user.schema';
import { usersService } from '@/services/users.service';
import Button from '@/components/common/Button';
import Input from '@/components/common/Input';
import FormField from '@/components/common/FormField';

export default function UserCreatePage() {
  const navigate = useNavigate();
  const queryClient = useQueryClient();

  // Три типи: вхід форми, контекст (не використовується) і вихід після перетворень Zod
  const {
    register,
    handleSubmit,
    formState: { errors },
  } = useForm<UserFormInput, unknown, UserFormData>({ resolver: zodResolver(userSchema) });

  const createUser = useMutation({
    mutationFn: usersService.create,
    onSuccess: async () => {
      await queryClient.invalidateQueries({ queryKey: ['users'] });
      toast.success('Користувача успішно створено');
      navigate('/users');
    },
    onError: (error) => {
      const message = isAxiosError(error) ? error.response?.data?.error : undefined;
      toast.error(message ?? 'Помилка створення');
    },
  });

  return (
    <div className="mx-auto max-w-2xl">
      <h1 className="mb-6 text-3xl font-bold">Новий користувач</h1>

      <form
        onSubmit={handleSubmit((data) => createUser.mutate(data))}
        className="space-y-4 rounded-lg bg-white p-6 shadow dark:bg-gray-900"
        noValidate
      >
        <FormField label="Ім'я" error={errors.name?.message}>
          <Input placeholder="Іван Петренко" {...register('name')} />
        </FormField>
        <FormField label="Email" error={errors.email?.message}>
          <Input type="email" placeholder="ivan@example.com" {...register('email')} />
        </FormField>
        <FormField label="Вік (необов'язково)" error={errors.age?.message}>
          <Input type="number" placeholder="25" {...register('age')} />
        </FormField>
        <FormField label="Телефон (необов'язково)" error={errors.phone?.message}>
          <Input placeholder="+380123456789" {...register('phone')} />
        </FormField>

        <div className="flex gap-4">
          <Button type="submit" isLoading={createUser.isPending}>
            Створити
          </Button>
          <Button type="button" variant="secondary" onClick={() => navigate('/users')}>
            Скасувати
          </Button>
        </div>
      </form>
    </div>
  );
}
```

Сторінку редагування (`/users/:id/edit`) реалізуйте самостійно за цим самим шаблоном: дані запису завантажте через `useQuery({ queryKey: ['users', id], ... })` і передайте у `defaultValues` (або `reset`) форми, а збереження виконайте через `useMutation` із `usersService.update`.

### Крок 8. Сповіщення (toast)

Бібліотеку `react-hot-toast` уже встановлено й підключено в `main.tsx` (компонент `Toaster`, крок 5). Виклик `toast.success()` та `toast.error()` можна робити з будь-якого місця: обробника, мутації чи навіть interceptor-а Axios. У кроках 6–7 сповіщення вже використано в `onSuccess` та `onError` мутацій. Централізувати повідомлення про помилки можна в одному місці, наприклад:

```typescript
import { isAxiosError } from 'axios';

export function getErrorMessage(error: unknown, fallback = 'Сталася помилка'): string {
  return isAxiosError(error) ? (error.response?.data?.error ?? fallback) : fallback;
}
```

### Крок 9. Скелетні індикатори завантаження

Створити компонент у файлі `src/components/common/Skeleton.tsx`. Атрибути `aria-busy` і `aria-label` повідомляють про завантаження користувачам екранних читачів.

```typescript
interface SkeletonProps {
  className?: string;
}

export default function Skeleton({ className = '' }: SkeletonProps) {
  return <div className={`animate-pulse rounded bg-gray-200 dark:bg-gray-700 ${className}`} />;
}

export function TableSkeleton({ rows = 5 }: { rows?: number }) {
  return (
    <div className="space-y-4" aria-busy="true" aria-label="Завантаження">
      {Array.from({ length: rows }, (_, i) => (
        <div key={i} className="flex gap-4">
          <Skeleton className="h-12 w-12" />
          <Skeleton className="h-12 flex-1" />
          <Skeleton className="h-12 w-32" />
        </div>
      ))}
    </div>
  );
}
```

Використання: у `UsersListPage` скелет показується, поки триває перше завантаження (`isPending`).

```typescript
{isPending && <TableSkeleton />}
```

### Крок 10. Оновлення README.md

Оновити файл `README.md` у корені проєкту, додавши інформацію про нову функціональність:

````markdown
# Назва проєкту

## Опис проєкту
[Оновлений опис з урахуванням нового функціоналу]

## Функціональність
- ✅ Аутентифікація (JWT)
- ✅ Захищені маршрути (Protected Routes)
- ✅ CRUD-операції для сутностей проєкту
- ✅ Валідація форм (React Hook Form + Zod)
- ✅ Клієнтський стан (Zustand) і дані сервера (TanStack Query)
- ✅ Сповіщення (toast)
- ✅ Пошук, пагінація
- ✅ Скелетні індикатори завантаження

## Технології
- React 19 + TypeScript
- Vite
- Tailwind CSS 4
- React Router 8
- Axios
- React Hook Form + Zod 4
- Zustand
- TanStack Query
- React Hot Toast

## API-ендпоїнти
Опис основних ендпоїнтів серверної частини, з якими взаємодіє застосунок.

## Встановлення та запуск

### Вимоги
- Node.js 24 LTS
- Серверна частина запущена на localhost:3000

### Інструкції
```bash
# Клонування репозиторію
git clone [URL репозиторію]

# Встановлення залежностей
npm install

# Налаштування .env
cp .env.example .env
# Встановити VITE_API_URL=http://localhost:3000/api

# Запуск у режимі розробки
npm run dev
```

## Структура проєкту
```
src/
├── components/
│   ├── common/        # Базові UI-компоненти
│   ├── layout/        # Компоненти розмітки
│   └── ProtectedRoute.tsx
├── pages/             # Сторінки застосунку
├── services/          # API-сервіси
├── stores/            # Сховища Zustand
├── schemas/           # Схеми валідації Zod
├── types/             # Типи TypeScript
└── hooks/             # Власні хуки
```

## Скріншоти
[Додати скріншоти: вхід, панель керування, список користувачів, форма створення]

## Використання

### Тестовий обліковий запис
```
Email: demo@example.com
Password: password123
```

## Автор
[Ваше ім'я, група]
````

### Крок 11. Здача роботи

Зробити коміт усіх змін до GitHub репозиторію з описовим повідомленням. Переконатися, що:
- README.md актуалізовано з новою функціональністю;
- додані скріншоти основних екранів;
- застосунок працює коректно з API серверної частини;
- команди `npm run lint` і `npm run build` виконуються без помилок.

Здати роботу через Moodle, вставивши посилання на GitHub репозиторій.

[⬆️ Здати лабораторну роботу](https://moodle.vcolnuft.volyn.ua/moodle/course/view.php?id=17#section-2)

## ❓ Контрольні запитання

1. Поясніть різницю між контрольованими та неконтрольованими компонентами в React. Який підхід використовує React Hook Form і чому це зменшує кількість повторних відтворень?
2. Як працює аутентифікація за JWT? Де зберігається токен на клієнті, які ризики має `localStorage` і які є альтернативи?
3. Що таке Zod і які переваги він дає порівняно з ручною валідацією? Чим відрізняються `z.input` та `z.output` і навіщо `z.preprocess` у схемі з необов'язковим числовим полем?
4. Поясніть концепцію захищених маршрутів (Protected Routes) і як вона реалізується в React Router. Чому клієнтського захисту недостатньо?
5. Порівняйте Context API та Zustand. Чому дані, отримані із сервера, краще зберігати не в Zustand, а в TanStack Query?
6. Що таке ключ запиту (`queryKey`) в TanStack Query і як він пов'язаний із кешем? Що робить `invalidateQueries`?
7. Що таке оптимістичні оновлення і коли їх доцільно застосовувати в CRUD-операціях?
8. Як реалізувати централізовану обробку помилок у React-застосунку (interceptors Axios, Error Boundary, toast)?
9. Навіщо потрібен debounce для пошукового поля? Що відбувалося б без нього?
10. Що таке скелетні індикатори завантаження і як вони покращують сприйняття швидкості інтерфейсу?
