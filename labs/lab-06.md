# Лабораторна робота 6. Реалізація розширених можливостей та деплой frontend

## 🎯 Мета роботи

Здобути практичні навички впровадження real-time функціональності, створення розширених UI компонентів, оптимізації продуктивності та деплою React застосунку на хостинг платформу.

## ✅ Завдання

### Загальний контекст

Ця лабораторна робота завершує цикл розробки повноцінного вебзастосунку. Здобувачі освіти інтегрують real-time можливості через WebSocket, створюють розширені інтерактивні компоненти, оптимізують продуктивність та деплоять готовий застосунок на production середовище.

### Технічні завдання

**Рівень 1. Real-time функціональність та базові компоненти**

1. Встановити та налаштувати Socket.io-client для з'єднання з backend.
2. Реалізувати real-time сповіщення для користувачів.
3. Створити компонент для відображення онлайн статусу користувачів.
4. Впровадити real-time оновлення списків даних.
5. Створити базові модальні вікна для підтвердження дій.
6. Реалізувати компонент табів для організації контенту.
7. Створити компонент dropdown меню з пошуком.

**Рівень 2. Розширені інтерактивні елементи**

8. Впровадити drag and drop функціональність для переміщення елементів.
9. Створити компонент для відображення графіків та діаграм.
10. Реалізувати infinite scroll для великих списків даних.
11. Додати image upload з preview та crop функціональністю.
12. Створити компонент календаря з можливістю вибору дат.
13. Реалізувати богатий текстовий редактор для контенту.
14. Додати анімації переходів між сторінками та компонентами.

**Рівень 3. Оптимізація та деплой**

15. Впровадити code splitting та lazy loading для компонентів.
16. Оптимізувати bundle size через tree shaking та мінімізацію.
17. Налаштувати PWA можливості з service worker та offline режимом.
18. Реалізувати систему кешування запитів на клієнті.
19. Додати SEO оптимізацію з meta tags та Open Graph.
20. Налаштувати CI/CD для автоматичного деплою.
21. Деплоїти frontend на Vercel або Netlify.
22. Провести фінальне тестування всієї системи та виправити баги.

## 👥 Форма виконання роботи

Форма виконання роботи **індивідуальна**.

## 📝 Критерії оцінювання

**Середній рівень (оцінка "задовільно")**

- Встановлено Socket.io-client з базовим підключенням.
- Створено 2-3 прості модальні вікна.
- Реалізовано базову функціональність табів.
- Виконано базовий деплой без оптимізації.
- Відсутні або мінімальні real-time можливості.
- Немає оптимізації bundle size.
- Інтерактивні елементи працюють з недоліками.
- Відсутня документація деплою.

**Достатній рівень (оцінка "добре")**

- Повністю налаштовано Socket.io з real-time сповіщеннями.
- Створено базові інтерактивні компоненти: модалки, таби, dropdown.
- Реалізовано одну додаткову можливість: drag and drop або графіки.
- Впроваджено базовий lazy loading для великих компонентів.
- Виконано деплой на Vercel або Netlify.
- Додано базову оптимізацію bundle size.
- Інтерфейс працює стабільно з непомітними недоліками.
- Створено базову документацію процесу деплою.

**Високий рівень (оцінка "відмінно")**

- Повністю виконано всі завдання трьох рівнів.
- Реалізована комплексна real-time функціональність з різними типами подій.
- Створено розширений набір інтерактивних компонентів.
- Впроваджено drag and drop, графіки, infinite scroll.
- Реалізовано PWA з offline підтримкою.
- Проведена глибока оптимізація продуктивності.
- Налаштовано CI/CD pipeline для автоматичного деплою.
- Додано SEO оптимізацію та meta tags.
- Код відповідає принципам чистого коду та best practices.
- Створена повна документація з інструкціями деплою.
- Продемонстровано глибоке розуміння production deployment та оптимізації.

## ⏰ Політика щодо дедлайнів

При порушенні встановленого терміну здачі лабораторної роботи максимальна можлива оцінка становить "добре", незалежно від якості виконаної роботи. Винятки можливі лише за поважних причин, підтверджених документально.

## 📚 Теоретичні відомості

### WebSocket та Real-time комунікація

**WebSocket** — це протокол, який забезпечує двостороннє повнодуплексне з'єднання між клієнтом та сервером через єдине TCP з'єднання. На відміну від HTTP, де клієнт ініціює запити, WebSocket дозволяє серверу надсилати дані клієнту в будь-який момент без попереднього запиту.

Socket.io — це бібліотека, яка надає абстракцію над WebSocket з додатковими можливостями як автоматичне перепідключення, rooms для групування з'єднань, broadcasting повідомлень та fallback до HTTP long-polling для старих браузерів. У React застосунках Socket.io-client використовується для встановлення з'єднання з Socket.io сервером та обробки real-time подій.

Типові use case для WebSocket включають чати та месенджери, сповіщення в реальному часі, онлайн ігри, collaborative editing, live оновлення дашбордів з метриками та біржові торгові платформи. Важливо правильно управляти життєвим циклом WebSocket з'єднань у React компонентах через useEffect hook для уникнення витоків пам'яті.

### Модальні вікна та компоненти перекриття

**Модальні вікна** — це UI компоненти, які відображаються поверх основного контенту та вимагають взаємодії користувача перед поверненням до застосунку. Правильна реалізація модальних вікон вимагає врахування accessibility, управління фокусом, обробки клавіші Escape та блокування прокручування body.

Сучасний підхід до створення модальних вікон у React включає використання порталів через ReactDOM.createPortal для рендерингу поза DOM ієрархією батьківського компонента, управління станом відкриття через hooks, trap фокусу всередині модального вікна та semantic HTML з ARIA атрибутами для accessibility.

### Drag and Drop функціональність

**Drag and Drop** дозволяє користувачам переміщувати елементи інтерфейсу через перетягування мишею або дотиком. У React існує кілька підходів до реалізації drag and drop, від нативного HTML5 Drag and Drop API до бібліотек як react-dnd або dnd-kit.

React DnD надає декларативний API для створення складних drag and drop інтерфейсів з підтримкою різних типів перетягуваних об'єктів, drop zones з різними правилами прийому, preview під час перетягування та мульти-сенсорну підтримку. Dnd-kit є більш сучасною альтернативою з кращою продуктивністю, меншим розміром bundle та вбудованою підтримкою accessibility.

### Візуалізація даних та графіки

**Візуалізація даних** критично важлива для представлення складної інформації у зрозумілому форматі. У React екосистемі існує багато бібліотек для створення графіків та діаграм, кожна з своїми перевагами.

Recharts — це бібліотека, побудована на React компонентах та D3, яка надає декларативний API для створення різних типів графіків. Chart.js з react-chartjs-2 wrapper пропонує простий API та широкий вибір типів візуалізацій. Victory — це модульна бібліотека для створення кастомних візуалізацій з анімаціями та інтерактивністю.

При виборі бібліотеки важливо враховувати розмір bundle, продуктивність при великих datasets, можливості кастомізації та документацію. Для складних візуалізацій може знадобитися пряме використання D3.js з інтеграцією через useEffect hooks.

### Code Splitting та Lazy Loading

**Code Splitting** — це техніка розбиття JavaScript bundle на менші частини, які завантажуються за потребою замість одного великого файлу. React надає вбудовану підтримку code splitting через React.lazy() та Suspense компоненти.

Lazy loading дозволяє відкласти завантаження компонентів до моменту, коли вони реально потрібні користувачу. Це значно зменшує initial bundle size та прискорює завантаження застосунку. Найкращі практики включають lazy loading route компонентів, великих бібліотек як text editors або image processors та модальних вікон, які рідко використовуються.

### Progressive Web Apps

**Progressive Web App (PWA)** — це вебзастосунок, який використовує сучасні веб-можливості для надання user experience, схожого на нативні мобільні застосунки. Ключові характеристики PWA включають можливість роботи офлайн, встановлення на домашній екран, push сповіщення та швидке завантаження.

Service Worker — це скрипт, який працює у фоновому режимі незалежно від вебсторінки та забезпечує можливості як кешування ресурсів, перехоплення мережевих запитів та background sync. Workbox — це набір бібліотек від Google, який спрощує створення PWA через готові стратегії кешування та precaching конфігурацію.

### Оптимізація продуктивності

**Оптимізація продуктивності** React застосунків включає багато аспектів від зменшення bundle size до оптимізації рендерингу компонентів. Основні техніки включають використання React.memo для мемоізації компонентів, useMemo та useCallback для оптимізації обчислень та функцій, virtualization для великих списків через react-window або react-virtualized.

Bundle size оптимізація досягається через tree shaking невикористаного коду, compression з gzip або brotli, code splitting та lazy loading, аналіз bundle через webpack-bundle-analyzer та заміну великих бібліотек на легші альтернативи. Image оптимізація включає використання WebP формату, responsive images з srcset, lazy loading зображень та CDN для доставки статичних ресурсів.

### Деплой на Vercel та Netlify

**Vercel** та **Netlify** — це платформи для хостингу frontend застосунків з автоматичним CI/CD, serverless functions та глобальною CDN. Обидві платформи надають безкоштовні плани для особистих проєктів та інтегруються з GitHub для автоматичного деплою при push до репозиторію.

Vercel особливо оптимізований для Next.js застосунків, але чудово працює з будь-якими React проєктами. Netlify надає додаткові можливості як форми, identity management та split testing. Процес деплою включає підключення GitHub репозиторію, налаштування build команди та директорії з production файлами, конфігурацію environment variables та налаштування кастомного домену.

## 🔗 Додаткові ресурси

- [Socket.io Client документація](https://socket.io/docs/v4/client-api/)
- [React DnD документація](https://react-dnd.github.io/react-dnd/)
- [Recharts документація](https://recharts.org/)
- [Workbox документація](https://developer.chrome.com/docs/workbox/)
- [Vercel документація](https://vercel.com/docs)
- [Netlify документація](https://docs.netlify.com/)
- [Web Vitals](https://web.dev/vitals/)

## ▶️ Хід роботи

### Крок 1. Налаштування Socket.io-client

Встановити Socket.io-client для real-time комунікації.

```bash
npm install socket.io-client
```

Створити сервіс для управління WebSocket з'єднанням у файлі `src/services/socket.service.ts`.

```typescript
import { io, Socket } from 'socket.io-client';

class SocketService {
  private socket: Socket | null = null;

  connect(token: string) {
    this.socket = io(import.meta.env.VITE_API_URL, {
      auth: { token },
      transports: ['websocket'],
    });

    this.socket.on('connect', () => {
      console.log('WebSocket connected');
    });

    this.socket.on('disconnect', () => {
      console.log('WebSocket disconnected');
    });

    return this.socket;
  }

  disconnect() {
    if (this.socket) {
      this.socket.disconnect();
      this.socket = null;
    }
  }

  on(event: string, callback: (...args: any[]) => void) {
    this.socket?.on(event, callback);
  }

  emit(event: string, data?: any) {
    this.socket?.emit(event, data);
  }

  off(event: string) {
    this.socket?.off(event);
  }
}

export default new SocketService();
```

Створити hook для використання Socket.io у компонентах.

```typescript
import { useEffect } from 'react';
import { useAuthStore } from '../stores/authStore';
import socketService from '../services/socket.service';

export const useSocket = () => {
  const token = useAuthStore((state) => state.token);

  useEffect(() => {
    if (token) {
      socketService.connect(token);

      return () => {
        socketService.disconnect();
      };
    }
  }, [token]);

  return socketService;
};
```

### Крок 2. Реалізація real-time сповіщень

Створити store для управління сповіщеннями у файлі `src/stores/notificationsStore.ts`.

```typescript
import { create } from 'zustand';

interface Notification {
  id: string;
  type: 'info' | 'success' | 'warning' | 'error';
  title: string;
  message: string;
  timestamp: Date;
  read: boolean;
}

interface NotificationsState {
  notifications: Notification[];
  addNotification: (notification: Omit<Notification, 'id' | 'timestamp' | 'read'>) => void;
  markAsRead: (id: string) => void;
  clearAll: () => void;
  unreadCount: () => number;
}

export const useNotificationsStore = create<NotificationsState>((set, get) => ({
  notifications: [],

  addNotification: (notification) => {
    const newNotification: Notification = {
      ...notification,
      id: Math.random().toString(36),
      timestamp: new Date(),
      read: false,
    };

    set((state) => ({
      notifications: [newNotification, ...state.notifications],
    }));
  },

  markAsRead: (id) => {
    set((state) => ({
      notifications: state.notifications.map((n) =>
        n.id === id ? { ...n, read: true } : n
      ),
    }));
  },

  clearAll: () => set({ notifications: [] }),

  unreadCount: () => {
    return get().notifications.filter((n) => !n.read).length;
  },
}));
```

Створити компонент для відображення сповіщень.

```typescript
import React, { useEffect } from 'react';
import { useSocket } from '../hooks/useSocket';
import { useNotificationsStore } from '../stores/notificationsStore';

const NotificationBell: React.FC = () => {
  const socket = useSocket();
  const { notifications, addNotification, unreadCount } = useNotificationsStore();
  const [isOpen, setIsOpen] = React.useState(false);

  useEffect(() => {
    socket.on('notification', (data) => {
      addNotification({
        type: data.type,
        title: data.title,
        message: data.message,
      });
    });

    return () => {
      socket.off('notification');
    };
  }, [socket, addNotification]);

  return (
    <div className="relative">
      <button
        onClick={() => setIsOpen(!isOpen)}
        className="relative p-2 rounded-full hover:bg-gray-100"
      >
        <svg className="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M15 17h5l-1.405-1.405A2.032 2.032 0 0118 14.158V11a6.002 6.002 0 00-4-5.659V5a2 2 0 10-4 0v.341C7.67 6.165 6 8.388 6 11v3.159c0 .538-.214 1.055-.595 1.436L4 17h5m6 0v1a3 3 0 11-6 0v-1m6 0H9" />
        </svg>
        {unreadCount() > 0 && (
          <span className="absolute top-0 right-0 bg-red-500 text-white text-xs rounded-full w-5 h-5 flex items-center justify-center">
            {unreadCount()}
          </span>
        )}
      </button>

      {isOpen && (
        <div className="absolute right-0 mt-2 w-80 bg-white rounded-lg shadow-lg border z-50">
          <div className="p-4 border-b">
            <h3 className="font-semibold">Сповіщення</h3>
          </div>
          <div className="max-h-96 overflow-y-auto">
            {notifications.length === 0 ? (
              <p className="p-4 text-gray-500 text-center">Немає сповіщень</p>
            ) : (
              notifications.map((notification) => (
                <div
                  key={notification.id}
                  className={`p-4 border-b hover:bg-gray-50 ${!notification.read ? 'bg-blue-50' : ''}`}
                >
                  <h4 className="font-medium">{notification.title}</h4>
                  <p className="text-sm text-gray-600">{notification.message}</p>
                  <span className="text-xs text-gray-400">
                    {notification.timestamp.toLocaleString()}
                  </span>
                </div>
              ))
            )}
          </div>
        </div>
      )}
    </div>
  );
};

export default NotificationBell;
```

### Крок 3. Створення модальних вікон

Встановити бібліотеку для модальних вікон або створити власну реалізацію.

```bash
npm install @headlessui/react
```

Створити переусний компонент Modal.

```typescript
import React, { Fragment } from 'react';
import { Dialog, Transition } from '@headlessui/react';

interface ModalProps {
  isOpen: boolean;
  onClose: () => void;
  title: string;
  children: React.ReactNode;
  size?: 'sm' | 'md' | 'lg';
}

const Modal: React.FC<ModalProps> = ({
  isOpen,
  onClose,
  title,
  children,
  size = 'md',
}) => {
  const sizeClasses = {
    sm: 'max-w-md',
    md: 'max-w-lg',
    lg: 'max-w-2xl',
  };

  return (
    <Transition appear show={isOpen} as={Fragment}>
      <Dialog as="div" className="relative z-50" onClose={onClose}>
        <Transition.Child
          as={Fragment}
          enter="ease-out duration-300"
          enterFrom="opacity-0"
          enterTo="opacity-100"
          leave="ease-in duration-200"
          leaveFrom="opacity-100"
          leaveTo="opacity-0"
        >
          <div className="fixed inset-0 bg-black bg-opacity-25" />
        </Transition.Child>

        <div className="fixed inset-0 overflow-y-auto">
          <div className="flex min-h-full items-center justify-center p-4">
            <Transition.Child
              as={Fragment}
              enter="ease-out duration-300"
              enterFrom="opacity-0 scale-95"
              enterTo="opacity-100 scale-100"
              leave="ease-in duration-200"
              leaveFrom="opacity-100 scale-100"
              leaveTo="opacity-0 scale-95"
            >
              <Dialog.Panel className={`w-full ${sizeClasses[size]} bg-white rounded-lg shadow-xl`}>
                <div className="p-6">
                  <Dialog.Title className="text-xl font-bold mb-4">
                    {title}
                  </Dialog.Title>
                  {children}
                </div>
              </Dialog.Panel>
            </Transition.Child>
          </div>
        </div>
      </Dialog>
    </Transition>
  );
};

export default Modal;
```

### Крок 4. Реалізація Drag and Drop

Встановити бібліотеку dnd-kit для drag and drop.

```bash
npm install @dnd-kit/core @dnd-kit/sortable @dnd-kit/utilities
```

Створити компонент зі списком, що сортується.

```typescript
import React, { useState } from 'react';
import {
  DndContext,
  closestCenter,
  KeyboardSensor,
  PointerSensor,
  useSensor,
  useSensors,
} from '@dnd-kit/core';
import {
  arrayMove,
  SortableContext,
  sortableKeyboardCoordinates,
  useSortable,
  verticalListSortingStrategy,
} from '@dnd-kit/sortable';
import { CSS } from '@dnd-kit/utilities';

interface SortableItemProps {
  id: string;
  children: React.ReactNode;
}

const SortableItem: React.FC<SortableItemProps> = ({ id, children }) => {
  const {
    attributes,
    listeners,
    setNodeRef,
    transform,
    transition,
  } = useSortable({ id });

  const style = {
    transform: CSS.Transform.toString(transform),
    transition,
  };

  return (
    <div ref={setNodeRef} style={style} {...attributes} {...listeners}>
      {children}
    </div>
  );
};

interface Task {
  id: string;
  title: string;
  status: string;
}

const TaskBoard: React.FC = () => {
  const [tasks, setTasks] = useState<Task[]>([
    { id: '1', title: 'Завдання 1', status: 'todo' },
    { id: '2', title: 'Завдання 2', status: 'todo' },
    { id: '3', title: 'Завдання 3', status: 'todo' },
  ]);

  const sensors = useSensors(
    useSensor(PointerSensor),
    useSensor(KeyboardSensor, {
      coordinateGetter: sortableKeyboardCoordinates,
    })
  );

  const handleDragEnd = (event: any) => {
    const { active, over } = event;

    if (active.id !== over.id) {
      setTasks((items) => {
        const oldIndex = items.findIndex((item) => item.id === active.id);
        const newIndex = items.findIndex((item) => item.id === over.id);
        return arrayMove(items, oldIndex, newIndex);
      });
    }
  };

  return (
    <DndContext
      sensors={sensors}
      collisionDetection={closestCenter}
      onDragEnd={handleDragEnd}
    >
      <SortableContext items={tasks.map((t) => t.id)} strategy={verticalListSortingStrategy}>
        <div className="space-y-2">
          {tasks.map((task) => (
            <SortableItem key={task.id} id={task.id}>
              <div className="p-4 bg-white rounded-lg shadow border cursor-move">
                {task.title}
              </div>
            </SortableItem>
          ))}
        </div>
      </SortableContext>
    </DndContext>
  );
};

export default TaskBoard;
```

### Крок 5. Додавання графіків

Встановити Recharts для візуалізації даних.

```bash
npm install recharts
```

Створити компонент з графіком.

```typescript
import React from 'react';
import { LineChart, Line, XAxis, YAxis, CartesianGrid, Tooltip, Legend, ResponsiveContainer } from 'recharts';

interface ChartData {
  name: string;
  value: number;
}

interface StatsChartProps {
  data: ChartData[];
  title: string;
}

const StatsChart: React.FC<StatsChartProps> = ({ data, title }) => {
  return (
    <div className="bg-white p-6 rounded-lg shadow">
      <h3 className="text-xl font-bold mb-4">{title}</h3>
      <ResponsiveContainer width="100%" height={300}>
        <LineChart data={data}>
          <CartesianGrid strokeDasharray="3 3" />
          <XAxis dataKey="name" />
          <YAxis />
          <Tooltip />
          <Legend />
          <Line type="monotone" dataKey="value" stroke="#3B82F6" strokeWidth={2} />
        </LineChart>
      </ResponsiveContainer>
    </div>
  );
};

export default StatsChart;
```

### Крок 6. Оптимізація продуктивності

Впровадити lazy loading для route компонентів.

```typescript
import React, { lazy, Suspense } from 'react';
import { BrowserRouter, Routes, Route } from 'react-router-dom';
import Loading from './components/common/Loading';

const HomePage = lazy(() => import('./pages/HomePage'));
const DashboardPage = lazy(() => import('./pages/DashboardPage'));
const UsersPage = lazy(() => import('./pages/UsersPage'));

const App: React.FC = () => {
  return (
    <BrowserRouter>
      <Suspense fallback={<Loading />}>
        <Routes>
          <Route path="/" element={<HomePage />} />
          <Route path="/dashboard" element={<DashboardPage />} />
          <Route path="/users" element={<UsersPage />} />
        </Routes>
      </Suspense>
    </BrowserRouter>
  );
};

export default App;
```

Додати React.memo для компонентів, що часто рендеряться.

```typescript
import React from 'react';

interface UserCardProps {
  name: string;
  email: string;
  onDelete: () => void;
}

const UserCard: React.FC<UserCardProps> = React.memo(({ name, email, onDelete }) => {
  return (
    <div className="p-4 bg-white rounded-lg shadow">
      <h3 className="font-bold">{name}</h3>
      <p className="text-gray-600">{email}</p>
      <button onClick={onDelete}>Видалити</button>
    </div>
  );
});

export default UserCard;
```

### Крок 7. Налаштування PWA

Встановити Vite PWA plugin.

```bash
npm install vite-plugin-pwa -D
```

Налаштувати PWA у файлі `vite.config.ts`.

```typescript
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import { VitePWA } from 'vite-plugin-pwa';

export default defineConfig({
  plugins: [
    react(),
    VitePWA({
      registerType: 'autoUpdate',
      includeAssets: ['favicon.ico', 'apple-touch-icon.png'],
      manifest: {
        name: 'My React App',
        short_name: 'MyApp',
        description: 'Modern React Application',
        theme_color: '#3B82F6',
        icons: [
          {
            src: 'pwa-192x192.png',
            sizes: '192x192',
            type: 'image/png',
          },
          {
            src: 'pwa-512x512.png',
            sizes: '512x512',
            type: 'image/png',
          },
        ],
      },
      workbox: {
        runtimeCaching: [
          {
            urlPattern: /^https:\/\/api\.example\.com\/.*/i,
            handler: 'NetworkFirst',
            options: {
              cacheName: 'api-cache',
              expiration: {
                maxEntries: 50,
                maxAgeSeconds: 300,
              },
            },
          },
        ],
      },
    }),
  ],
});
```

### Крок 8. Деплой на Vercel

Створити файл `vercel.json` у корені проєкту.

```json
{
  "buildCommand": "npm run build",
  "outputDirectory": "dist",
  "devCommand": "npm run dev",
  "installCommand": "npm install",
  "framework": "vite",
  "rewrites": [
    {
      "source": "/(.*)",
      "destination": "/index.html"
    }
  ]
}
```

Підключити проєкт до Vercel через GitHub:

1. Зареєструватися на vercel.com через GitHub акаунт.
2. Натиснути "New Project" та обрати GitHub репозиторій.
3. Налаштувати environment variables у розділі Settings.
4. Vercel автоматично деплоїть проєкт при кожному push до main branch.

### Крок 9. Налаштування CI/CD

Створити GitHub Actions workflow у файлі `.github/workflows/deploy.yml`.

```yaml
name: Deploy to Vercel

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Run tests
        run: npm test

      - name: Build project
        run: npm run build

      - name: Deploy to Vercel
        uses: amondnet/vercel-action@v20
        with:
          vercel-token: ${{ secrets.VERCEL_TOKEN }}
          vercel-org-id: ${{ secrets.ORG_ID }}
          vercel-project-id: ${{ secrets.PROJECT_ID }}
```

### Крок 10. Фінальне тестування

Провести комплексне тестування всіх функцій застосунку:

- перевірити аутентифікацію та авторизацію;
- протестувати всі CRUD операції;
- перевірити real-time функціональність;
- протестувати responsive дизайн на різних пристроях;
- перевірити роботу офлайн режиму для PWA;
- протестувати продуктивність через Lighthouse;
- виправити знайдені баги та недоліки.

### Крок 11. Фінальне оновлення README.md

Створити повний фінальний README.md для завершеного проєкту:

```markdown
# Назва проєкту

> Повнофункціональний full-stack вебзастосунок з React + Node.js

## 🎯 Опис проєкту
[Детальний опис предметної області, цілей та можливостей застосунку]

## ✨ Функціональність

### Backend
- ✅ RESTful API на Node.js + Express
- ✅ PostgreSQL база даних з Prisma ORM
- ✅ JWT аутентифікація
- ✅ WebSocket для real-time комунікації
- ✅ Файловий сервіс
- ✅ API документація (Swagger)

### Frontend
- ✅ React 18 + TypeScript
- ✅ Система аутентифікації
- ✅ CRUD інтерфейси для всіх сутностей
- ✅ Real-time оновлення через Socket.io
- ✅ Валідація форм (React Hook Form + Zod)
- ✅ Drag & Drop функціональність
- ✅ Графіки та візуалізації
- ✅ PWA з offline підтримкою
- ✅ Responsive дизайн

## 🛠 Технології

### Backend
- Node.js 18+
- Express.js
- PostgreSQL
- Prisma ORM
- Socket.io
- JWT + bcrypt
- Multer (файли)

### Frontend
- React 18
- TypeScript
- Vite
- Tailwind CSS
- React Router v6
- Axios
- Socket.io-client
- Zustand (state management)
- React Hook Form + Zod
- Recharts (графіки)
- dnd-kit (drag & drop)

## 📦 Встановлення та запуск

### Вимоги
- Node.js 18+
- PostgreSQL 14+
- npm або yarn

### Backend

\`\`\`bash
# Клонування репозиторію
git clone [URL]
cd backend

# Встановлення залежностей
npm install

# Налаштування .env
cp .env.example .env
# Встановити DATABASE_URL та інші змінні

# Міграція бази даних
npx prisma migrate dev

# Запуск сервера
npm run dev
\`\`\`

### Frontend

\`\`\`bash
cd frontend

# Встановлення залежностей
npm install

# Налаштування .env
cp .env.example .env
# Встановити VITE_API_URL=http://localhost:3000/api

# Запуск у режимі розробки
npm run dev
\`\`\`

## 🚀 Production Deployment

### Backend
Задеплоєно на: [Render/Railway/Heroku]
URL: https://api.example.com

### Frontend
Задеплоєно на: Vercel/Netlify
URL: https://myapp.vercel.app

## 📁 Структура проєкту

### Backend
\`\`\`
backend/
├── src/
│   ├── controllers/    # Бізнес-логіка
│   ├── routes/         # API маршрути
│   ├── middleware/     # Middleware функції
│   ├── services/       # Сервісний шар
│   └── utils/          # Допоміжні функції
├── prisma/
│   └── schema.prisma   # Схема БД
└── uploads/            # Завантажені файли
\`\`\`

### Frontend
\`\`\`
frontend/
├── src/
│   ├── components/     # React компоненти
│   ├── pages/         # Сторінки
│   ├── services/      # API сервіси
│   ├── stores/        # Zustand stores
│   ├── schemas/       # Zod схеми
│   ├── types/         # TypeScript типи
│   └── hooks/         # Кастомні hooks
└── public/            # Статичні файли
\`\`\`

## 📸 Скріншоти

### Головна сторінка
[Скріншот]

### Dashboard
[Скріншот]

### CRUD інтерфейс
[Скріншот]

### Real-time функціональність
[Скріншот]

## 🔑 Тестові облікові дані

\`\`\`
Email: demo@example.com
Password: password123
\`\`\`

## 📊 Продуктивність

- Lighthouse Score: 95+
- Bundle Size: < 200KB (gzipped)
- First Contentful Paint: < 1.5s
- Time to Interactive: < 3.5s

## 🔐 Безпека

- JWT токени з expiration
- Bcrypt хешування паролів
- CORS налаштування
- Input валідація на клієнті та сервері
- SQL injection захист через Prisma
- XSS захист

## 📝 API Документація

Swagger документація доступна за адресою: `/api/docs`

## 🧪 Тестування

\`\`\`bash
# Backend тести
cd backend
npm test

# Frontend тести
cd frontend
npm test
\`\`\`


## 👨‍💻 Автор

**[Ваше ім'я]**
- Група: [Номер групи]
- Email: [email]
- GitHub: [@username](https://github.com/username)


```

### Крок 12. Здача роботи

Зробити фінальний коміт усіх змін до GitHub репозиторію. Переконатися, що:
- README.md містить всю необхідну інформацію;
- додані скріншоти всіх ключових екранів;
- застосунок успішно задеплоєний та доступний онлайн;
- в README.md вказані робочі посилання на production версії;
- додані тестові облікові дані для перевірки.

Здати роботу через Moodle, вставивши посилання на GitHub репозиторій.

[Здати лабораторну роботу](https://moodle.vcolnuft.volyn.ua/moodle/course/view.php?id=17#section-2)

## ❓ Контрольні запитання

1. Поясніть різницю між WebSocket та HTTP протоколами. Коли доцільно використовувати WebSocket?
2. Як правильно управляти життєвим циклом WebSocket з'єднань у React компонентах?
3. Що таке React порталі та як вони використовуються для створення модальних вікон?
4. Порівняйте різні бібліотеки для drag and drop у React. Які переваги має dnd-kit?
5. Поясніть концепцію code splitting та як React.lazy() працює під капотом.
6. Що таке Service Worker та як він забезпечує offline функціональність у PWA?
7. Які метрики продуктивності важливі для вебзастосунків? Як їх виміряти та покращити?
8. Порівняйте Vercel та Netlify для деплою React застосунків. Які їх основні відмінності?
9. Що таке CI/CD та які переваги дає автоматизація процесу деплою?
10. Як забезпечити SEO оптимізацію для Single Page Applications?
