# Лабораторна робота 06 Реалізація розширених можливостей та розгортання клієнтської частини

## 🎯 Мета роботи

Здобути практичні навички впровадження функціональності реального часу (real-time), створення розширених UI-компонентів, оптимізації продуктивності та розгортання React-застосунку на хостинг-платформі.

## ✅ Завдання

### Загальний контекст

Ця лабораторна робота завершує цикл розробки повноцінного вебзастосунку. Здобувачі освіти інтегрують можливості реального часу через WebSocket, створюють розширені інтерактивні компоненти, оптимізують продуктивність та розгортають готовий застосунок у робочому середовищі.

### Технічні завдання

**Рівень 1. Функціональність реального часу та базові компоненти**

1. Встановити та налаштувати Socket.io-client для з'єднання із серверною частиною.
2. Реалізувати сповіщення користувачів у реальному часі.
3. Створити компонент для відображення онлайн-статусу користувачів.
4. Впровадити оновлення списків даних у реальному часі.
5. Створити базові модальні вікна для підтвердження дій.
6. Реалізувати компонент табів для організації контенту.
7. Створити компонент випадного меню (dropdown) з пошуком.

**Рівень 2. Розширені інтерактивні елементи**

8. Впровадити перетягування елементів (drag and drop) для зміни їх порядку.
9. Створити компонент для відображення графіків та діаграм.
10. Реалізувати нескінченне прокручування (infinite scroll) для великих списків даних.
11. Додати завантаження зображень з попереднім переглядом та обрізанням (crop).
12. Створити компонент календаря з можливістю вибору дат.
13. Реалізувати розширений (rich text) текстовий редактор для вмісту.
14. Додати анімації переходів між сторінками та компонентами.

**Рівень 3. Оптимізація та розгортання**

15. Впровадити розбиття коду (code splitting) та відкладене завантаження (lazy loading) компонентів.
16. Оптимізувати розмір збірки (bundle size) через tree shaking та мінімізацію.
17. Налаштувати можливості PWA із service worker та офлайн-режимом.
18. Реалізувати систему кешування запитів на клієнті.
19. Додати SEO-оптимізацію з метатегами та Open Graph.
20. Налаштувати CI/CD для автоматичного розгортання.
21. Розгорнути клієнтську частину на Vercel або Netlify.
22. Провести фінальне тестування всієї системи та виправити баги.

## 👥 Форма виконання роботи

Форма виконання роботи **індивідуальна**.

## 📝 Критерії оцінювання

**Середній рівень (оцінка "задовільно")**

- Встановлено Socket.io-client з базовим підключенням.
- Створено 2–3 прості модальні вікна.
- Реалізовано базову функціональність табів.
- Виконано базове розгортання без оптимізації.
- Відсутні або мінімальні можливості реального часу.
- Немає оптимізації розміру збірки.
- Інтерактивні елементи працюють з недоліками.
- Відсутня документація розгортання.

**Достатній рівень (оцінка "добре")**

- Повністю налаштовано Socket.io зі сповіщеннями в реальному часі.
- Створено базові інтерактивні компоненти: модальні вікна, таби, випадне меню.
- Реалізовано одну додаткову можливість: перетягування елементів або графіки.
- Впроваджено базове відкладене завантаження для великих компонентів.
- Виконано розгортання на Vercel або Netlify.
- Додано базову оптимізацію розміру збірки.
- Інтерфейс працює стабільно з непомітними недоліками.
- Створено базову документацію процесу розгортання.

**Високий рівень (оцінка "відмінно")**

- Повністю виконано всі завдання трьох рівнів.
- Реалізована комплексна функціональність реального часу з різними типами подій.
- Створено розширений набір інтерактивних компонентів.
- Впроваджено перетягування елементів, графіки, нескінченне прокручування.
- Реалізовано PWA з офлайн-підтримкою.
- Проведена глибока оптимізація продуктивності.
- Налаштовано конвеєр CI/CD для автоматичного розгортання.
- Додано SEO-оптимізацію та метатеги.
- Код відповідає принципам чистого коду та кращим практикам.
- Створена повна документація з інструкціями розгортання.
- Продемонстровано глибоке розуміння розгортання у робочому середовищі та оптимізації.

## ⏰ Політика щодо дедлайнів

При порушенні встановленого терміну здачі лабораторної роботи максимальна можлива оцінка становить "добре", незалежно від якості виконаної роботи. Винятки можливі лише за поважних причин, підтверджених документально.

## 📚 Теоретичні відомості

### WebSocket та обмін даними в реальному часі

**WebSocket** — це протокол, який забезпечує двостороннє повнодуплексне з'єднання між клієнтом і сервером через одне TCP-з'єднання. На відміну від HTTP, де запити завжди починає клієнт, WebSocket дозволяє серверу надсилати дані клієнту будь-коли, без попереднього запиту.

**Socket.io** — бібліотека, що надає зручну абстракцію над WebSocket: автоматичне перепідключення, кімнати (rooms) для групування з'єднань, розсилання повідомлень (broadcasting), підтвердження доставки та резервний транспорт HTTP long-polling. За замовчуванням клієнт спершу підключається через long-polling і одразу «піднімається» до WebSocket; примусове `transports: ['websocket']` вимикає резервний механізм, тому без потреби його краще не вказувати. Подія `connect_error` повідомляє про відмову сервера (наприклад, недійсний токен у `auth`).

Типові сценарії: чати, сповіщення, спільне редагування, живі панелі метрик, біржові застосунки. Головна складність у React — **життєвий цикл з'єднання**. Правила, які ми застосовуємо в роботі:

- з'єднання одне на весь застосунок і керується в одному місці (у `Layout`), а не в кожному компоненті-споживачі: інакше при кожному монтуванні виникали б зайві підключення й відключення;
- екземпляр сокета створюється поза React (`autoConnect: false`), тому підписуватися на події можна в будь-який момент, а токен передається лише в момент підключення;
- кожен компонент підписується на події в `useEffect` і **знімає саме свій обробник** у функції очищення (`socket.off(event, handler)`); виклик `socket.off(event)` без обробника видалив би підписки всіх компонентів;
- адреса сервера Socket.io не містить префікса `/api` — той належить лише REST-маршрутам, а в Socket.io частина шляху після домену означає простір імен (namespace).

Дані реального часу добре поєднуються з TanStack Query: замість ручного оновлення масивів у стані достатньо на подію сервера викликати `invalidateQueries`, і список перезавантажиться з актуальними даними.

### Модальні вікна та компоненти перекриття

**Модальні вікна** відображаються поверх основного вмісту й вимагають дії користувача, перш ніж він повернеться до застосунку. Правильна реалізація має врахувати доступність (accessibility): перенесення фокуса у вікно й утримання його всередині (focus trap), закриття клавішею Escape, блокування прокручування сторінки, атрибути ARIA (`role="dialog"`, `aria-modal`, `aria-labelledby`), відтворення поза основним деревом DOM через портал (`createPortal`).

Робити все це вручну складно, тому використовують готові безголові (headless) бібліотеки, які дають поведінку й доступність, але не нав'язують вигляд: **Headless UI**, Radix UI, React Aria. У курсі використовується Headless UI 2: компонент `Dialog` з частинами `DialogBackdrop`, `DialogPanel`, `DialogTitle`; анімація вмикається атрибутом `transition`, а її стани описуються класами Tailwind із префіксами `data-closed:` та `data-enter:`. Сучасним нативним варіантом є також HTML-елемент `<dialog>`, який уже підтримують усі браузери.

### Перетягування елементів (Drag and Drop)

**Drag and Drop** дозволяє переміщувати елементи інтерфейсу мишею або дотиком. Нативний HTML5 Drag and Drop API незручний і погано працює на сенсорних екранах, тому у React-застосунках користуються бібліотеками. **react-dnd** тривалий час був стандартом, але майже не розвивається. **dnd-kit** — сучасна модульна альтернатива з вищою продуктивністю, підтримкою сенсорних пристроїв і вбудованою доступністю: за допомогою `KeyboardSensor` елементи можна переміщувати клавіатурою.

Основні поняття dnd-kit: `DndContext` (контекст із датчиками та обробниками подій), `SortableContext` (список, що сортується), `useSortable` (хук для елемента) і `arrayMove` (утиліта для зміни порядку). Стан списку ми зберігаємо самі, а бібліотека лише повідомляє в `onDragEnd`, який елемент над яким відпустили.

### Візуалізація даних і графіки

**Візуалізація даних** подає складну інформацію в зрозумілому вигляді. **Recharts** — бібліотека з декларативним API на основі React-компонентів (`LineChart`, `BarChart`, `Tooltip`, `Legend`), яка добре підходить для типових панелей керування. Альтернативи: Chart.js із обгорткою react-chartjs-2 (простий API, полотно `canvas`, добре працює з великими наборами даних), Visx і Victory (гнучкі модульні набори) та безпосереднє використання D3.js для нестандартних візуалізацій.

Обираючи бібліотеку, зважайте на розмір збірки (Recharts помітно збільшує пакет — див. розділ про розбиття коду), продуктивність на великих наборах даних, гнучкість налаштування та якість документації.

### Розбиття коду та відкладене завантаження

**Розбиття коду (code splitting)** — це поділ JavaScript-пакета на менші файли (chunks), які завантажуються за потреби замість одного великого. React підтримує його через `React.lazy()` і компонент `Suspense`, який показує запасний вміст, поки chunk завантажується. Збирач (Vite/Rolldown) автоматично виділяє в окремий файл усе, що імпортується динамічно через `import()`.

Найефективніше відкладати завантаження сторінок (за маршрутами), великих бібліотек (графіки, текстові редактори, обробка зображень) та рідко використовуваних модальних вікон. Хороший орієнтир: початкове завантаження має містити лише те, що потрібне для першого екрана. У цій роботі бібліотека Recharts потрапляє лише у chunk сторінки Dashboard і не збільшує розмір початкового завантаження. Щоб побачити склад пакета, використовують візуалізатор, наприклад `rollup-plugin-visualizer`.

### Progressive Web Apps

**Progressive Web App (PWA)** — вебзастосунок, що використовує сучасні можливості браузера, щоб поводитися як нативний: працювати офлайн, встановлюватися на головний екран, швидко запускатися. Ключові складники: маніфест (`manifest.webmanifest`: назва, іконки, кольори, режим відображення), **service worker** та HTTPS.

**Service Worker** — скрипт, що працює у фоні незалежно від сторінки та може перехоплювати мережеві запити, кешувати ресурси та синхронізувати дані у фоні. **Workbox** — набір бібліотек Google з готовими стратегіями кешування: `CacheFirst` (спершу кеш — для статичних файлів), `NetworkFirst` (спершу мережа, за її відсутності кеш — для API), `StaleWhileRevalidate` (віддати з кешу й одночасно оновити його). Плагін `vite-plugin-pwa` генерує service worker і маніфест автоматично. Пам'ятайте про безпеку: не кешуйте відповіді з персональними даними надовго, бо після виходу з облікового запису вони залишаться на пристрої.

### Оптимізація продуктивності

Вимірюйте, перш ніж оптимізувати. Основні метрики **Core Web Vitals**: LCP (швидкість відображення найбільшого елемента, ціль — до 2,5 с), INP (швидкість реакції на дії користувача, до 200 мс; замінила метрику FID) та CLS (візуальна стабільність, до 0,1). Виміряти їх можна в Lighthouse (вкладка DevTools) або в PageSpeed Insights.

Оптимізація відтворення. `React.memo` пропускає повторне відтворення компонента, якщо його пропси не змінилися (для функцій у пропсах потрібен `useCallback`, для обчислень — `useMemo`). Проте в сучасному React це переважно завдання **React Compiler**: він автоматично мемоізує компоненти під час збірки, тому ручні `memo`/`useMemo`/`useCallback` варто додавати лише за результатами вимірювань. Для дуже великих списків застосовують віртуалізацію (TanStack Virtual, react-window): у DOM існують лише видимі рядки.

Оптимізація розміру збірки: tree shaking (вилучення невикористаного коду; допомагають іменовані імпорти замість імпорту всієї бібліотеки), розбиття коду, стиснення gzip або brotli на боці хостингу, заміна важких бібліотек легшими. Оптимізація зображень: формати WebP та AVIF, адаптивні зображення (`srcset`), атрибут `loading="lazy"`, доставка через CDN.

### SEO для односторінкових застосунків

Односторінковий застосунок (SPA) віддає порожній `index.html`, а вміст з'являється після виконання JavaScript. Сучасні пошукові системи виконують JavaScript, але індексують такі сторінки повільніше та менш надійно. Мінімум для SPA: осмислені `<title>` та `description`, коректний `lang`, теги Open Graph для попереднього перегляду посилання в месенджерах і соцмережах, `robots.txt` та `sitemap.xml`. Якщо SEO критичне (публічні каталоги, блоги), використовують відтворення на сервері (SSR) або генерацію статичних сторінок у фреймворках на кшталт Next.js чи React Router у режимі фреймворка.

### Розгортання на Vercel та Netlify

**Vercel** та **Netlify** — платформи для розміщення клієнтських застосунків з автоматичним CI/CD, глобальною CDN і серверними функціями. Обидві інтегруються з GitHub: кожен `push` у гілку запускає збірку й розгортання, а кожен Pull Request отримує власну тимчасову адресу для перегляду (preview).

Процес розгортання: підключити репозиторій, вказати команду збірки (`npm run build`) та каталог результату (`dist`), задати змінні середовища й, за потреби, власний домен. Три важливі особливості для нашого застосунку:

- змінні з префіксом `VITE_` підставляються **під час збірки**, тому після їх зміни потрібне повторне розгортання;
- для SPA потрібне правило перезапису (rewrite) усіх шляхів на `index.html`, інакше пряме відкриття адреси `/users` дасть помилку 404;
- адреса розгорнутої клієнтської частини має бути вказана у змінній `FRONTEND_URL` на сервері (налаштування CORS та Socket.io), а адреси API та Socket.io — у змінних `VITE_API_URL` і `VITE_SOCKET_URL` клієнта.

Умови безкоштовних планів змінюються, тому перед вибором перевіряйте їх на сайті платформи. На момент написання безкоштовний план Vercel (Hobby) призначений лише для некомерційного використання, а безкоштовний план Netlify обмежений місячним лімітом кредитів. Серверну частину з лабораторної роботи 3 розміщують окремо (наприклад, на Render).

## 🔗 Додаткові ресурси

- [Socket.io: документація клієнта](https://socket.io/docs/v4/client-api/)
- [Headless UI: Dialog](https://headlessui.com/react/dialog)
- [dnd-kit документація](https://docs.dndkit.com/)
- [Recharts документація](https://recharts.org/)
- [React: lazy та Suspense](https://react.dev/reference/react/lazy)
- [React Compiler](https://react.dev/learn/react-compiler)
- [vite-plugin-pwa](https://vite-pwa-org.netlify.app/)
- [Workbox документація](https://developer.chrome.com/docs/workbox/)
- [Web Vitals](https://web.dev/articles/vitals)
- [Vercel документація](https://vercel.com/docs)
- [Netlify документація](https://docs.netlify.com/)
- [GitHub Actions документація](https://docs.github.com/actions)

## ▶️ Хід роботи

### Крок 1. Налаштування Socket.io-client

Встановити Socket.io-client для обміну даними в реальному часі.

```bash
npm install socket.io-client
```

Додати адресу сервера Socket.io у `.env` та `.env.example`. **Без префікса `/api`**: він належить лише REST-маршрутам.

```bash
VITE_API_URL=http://localhost:3000/api
VITE_SOCKET_URL=http://localhost:3000
```

Створити модуль з'єднання у файлі `src/services/socket.ts`. Екземпляр сокета створюється один раз і не підключається автоматично: токен передається в момент виклику `connectSocket`, а компоненти можуть підписуватися на події будь-коли.

```typescript
import { io, type Socket } from 'socket.io-client';

// Адреса сервера Socket.io — БЕЗ префікса /api: він належить лише REST-маршрутам.
// Інакше Socket.io сприйме "/api" як назву простору імен (namespace).
const SOCKET_URL = import.meta.env.VITE_SOCKET_URL ?? 'http://localhost:3000';

// Єдиний екземпляр на весь застосунок. З'єднання не встановлюється, доки не буде токена,
// але підписуватися на події (socket.on) компоненти можуть у будь-який момент.
export const socket: Socket = io(SOCKET_URL, { autoConnect: false });

export function connectSocket(token: string) {
  if (socket.active) socket.disconnect(); // токен змінився: перепідключаємося з новим
  socket.auth = { token }; // сервер перевіряє його в io.use(...) (лабораторна робота 3)
  socket.connect();
}

export function disconnectSocket() {
  socket.disconnect();
}
```

Створити хук `src/hooks/useSocketConnection.ts`, що відкриває з'єднання після входу й закриває його після виходу. Його викликають **один раз** у компоненті `Layout`, а не в кожному споживачі.

```typescript
import { useEffect } from 'react';
import { useAuthStore } from '@/stores/authStore';
import { connectSocket, disconnectSocket } from '@/services/socket';

// Викликається ОДИН раз (у Layout): з'єднання належить застосунку, а не окремому компоненту.
// Вхід у систему відкриває з'єднання, вихід — закриває.
export function useSocketConnection() {
  const token = useAuthStore((state) => state.accessToken);

  useEffect(() => {
    if (!token) return;
    connectSocket(token);
    return disconnectSocket;
  }, [token]);
}
```

Додати виклик у `src/components/layout/Layout.tsx`.

```typescript
export default function Layout() {
  useSocketConnection(); // одне з'єднання Socket.io на весь застосунок
  // ...
}
```

Створити хук `src/hooks/useSocketEvent.ts` для підписки на події. Він знімає саме власний обробник, а `useEffectEvent` дозволяє використовувати актуальні значення стану в обробнику без повторної підписки на кожне відтворення.

```typescript
import { useEffect, useEffectEvent } from 'react';
import { socket } from '@/services/socket';

// Підписка на подію на час життя компонента. Знімається саме цей обробник (за посиланням),
// тому підписки інших компонентів на ту саму подію не зачіпаються.
export function useSocketEvent<T>(event: string, handler: (payload: T) => void) {
  const onEvent = useEffectEvent(handler); // завжди актуальний обробник без повторних підписок

  useEffect(() => {
    const listener = (payload: T) => onEvent(payload);
    socket.on(event, listener);
    return () => {
      socket.off(event, listener);
    };
  }, [event]);
}
```

> **Перевірка.** Сервер із лабораторної роботи 3 приймає підключення лише з дійсним JWT у `auth.token`. Увійдіть у застосунок і переконайтеся, що у вкладці Network (фільтр WS) з'явилося з'єднання зі статусом 101. Після виходу воно має закритися.

### Крок 2. Сповіщення в реальному часі

Створити сховище сповіщень у файлі `src/stores/notificationsStore.ts`. Кількість непрочитаних обчислюється в селекторі: компонент перемальовується лише тоді, коли змінилося саме це число.

```typescript
import { create } from 'zustand';

export interface AppNotification {
  id: string;
  type: 'info' | 'success' | 'warning' | 'error';
  title: string;
  message: string;
  timestamp: number;
  read: boolean;
}

interface NotificationsState {
  notifications: AppNotification[];
  add: (n: Pick<AppNotification, 'type' | 'title' | 'message'>) => void;
  markAsRead: (id: string) => void;
  markAllAsRead: () => void;
  clearAll: () => void;
}

export const useNotificationsStore = create<NotificationsState>((set) => ({
  notifications: [],

  add: (n) =>
    set((state) => ({
      // зберігаємо не більше 50 останніх, щоб список не ріс безмежно
      notifications: [
        { ...n, id: crypto.randomUUID(), timestamp: Date.now(), read: false },
        ...state.notifications,
      ].slice(0, 50),
    })),

  markAsRead: (id) =>
    set((state) => ({
      notifications: state.notifications.map((n) => (n.id === id ? { ...n, read: true } : n)),
    })),

  markAllAsRead: () =>
    set((state) => ({ notifications: state.notifications.map((n) => ({ ...n, read: true })) })),

  clearAll: () => set({ notifications: [] }),
}));

// Похідне значення обчислюємо в селекторі: компонент перемальовується лише при зміні числа
export const selectUnreadCount = (state: NotificationsState) =>
  state.notifications.filter((n) => !n.read).length;
```

Створити компонент дзвіночка у файлі `src/components/NotificationBell.tsx`. Сервер надсилає подію `notification` в особисту кімнату користувача `user:<id>` (лабораторна робота 3), наприклад: `io.to('user:2').emit('notification', { type: 'info', title: 'Нове замовлення', message: '№ 42' })`.

```typescript
import { useState } from 'react';
import { selectUnreadCount, useNotificationsStore } from '@/stores/notificationsStore';
import { useSocketEvent } from '@/hooks/useSocketEvent';

interface NotificationPayload {
  type?: 'info' | 'success' | 'warning' | 'error';
  title?: string;
  message: string;
}

export default function NotificationBell() {
  const [isOpen, setIsOpen] = useState(false);
  const notifications = useNotificationsStore((state) => state.notifications);
  const unreadCount = useNotificationsStore(selectUnreadCount);
  const { add, markAsRead, markAllAsRead } = useNotificationsStore.getState();

  // Сервер надсилає подію 'notification' в особисту кімнату користувача (лабораторна робота 3)
  useSocketEvent<NotificationPayload>('notification', (data) =>
    add({ type: data.type ?? 'info', title: data.title ?? 'Сповіщення', message: data.message })
  );

  return (
    <div className="relative">
      <button
        type="button"
        onClick={() => setIsOpen((open) => !open)}
        aria-label={`Сповіщення: непрочитаних ${unreadCount}`}
        aria-expanded={isOpen}
        className="relative rounded-full p-2 hover:bg-gray-100 dark:hover:bg-gray-800"
      >
        <span aria-hidden="true">🔔</span>
        {unreadCount > 0 && (
          <span className="absolute right-0 top-0 flex h-5 w-5 items-center justify-center rounded-full bg-red-500 text-xs text-white">
            {unreadCount}
          </span>
        )}
      </button>

      {isOpen && (
        <div className="absolute right-0 z-50 mt-2 w-80 rounded-lg border bg-white shadow-lg dark:border-gray-700 dark:bg-gray-900">
          <div className="flex items-center justify-between border-b p-4 dark:border-gray-700">
            <h3 className="font-semibold">Сповіщення</h3>
            <button type="button" onClick={markAllAsRead} className="text-sm text-primary-600 hover:underline">
              Прочитати всі
            </button>
          </div>
          <ul className="max-h-96 overflow-y-auto">
            {notifications.length === 0 && <li className="p-4 text-center text-gray-500">Немає сповіщень</li>}
            {notifications.map((n) => (
              <li key={n.id}>
                <button
                  type="button"
                  onClick={() => markAsRead(n.id)}
                  className={`block w-full border-b p-4 text-left hover:bg-gray-50 dark:border-gray-700 dark:hover:bg-gray-800 ${
                    n.read ? '' : 'bg-blue-50 dark:bg-blue-950'
                  }`}
                >
                  <span className="block font-medium">{n.title}</span>
                  <span className="block text-sm text-gray-600 dark:text-gray-400">{n.message}</span>
                  <span className="text-xs text-gray-400">{new Date(n.timestamp).toLocaleString('uk-UA')}</span>
                </button>
              </li>
            ))}
          </ul>
        </div>
      )}
    </div>
  );
}
```

Підключити `<NotificationBell />` у `Header` для аутентифікованого користувача.

**Оновлення списків у реальному часі (завдання 4).** Замість ручного редагування масивів достатньо перезавантажити дані з сервера, коли той повідомляє про зміну. Додайте в `UsersListPage` підписку (сервер має надсилати подію після створення, зміни чи видалення запису):

```typescript
useSocketEvent('users:changed', () => queryClient.invalidateQueries({ queryKey: ['users'] }));
```

Онлайн-статус користувачів (завдання 3) реалізуйте за тим самим принципом: сервер відстежує підключення й відключення (`io.on('connection')`, `socket.on('disconnect')`), розсилає події `user:online` та `user:offline`, а клієнт зберігає множину ідентифікаторів користувачів онлайн у сховищі Zustand.

### Крок 3. Створення модальних вікон

Встановити Headless UI 2.

```bash
npm install @headlessui/react
```

Створити повторно використовуваний компонент `Modal` у файлі `src/components/common/Modal.tsx`. У другій версії Headless UI замість `Transition`/`Transition.Child`/`Dialog.Panel` використовуються `DialogBackdrop`, `DialogPanel`, `DialogTitle` та атрибут `transition`. Прозорість фону задається записом `bg-black/25` (класи `bg-opacity-*` у Tailwind 4 вилучено).

```typescript
import type { ReactNode } from 'react';
import { Dialog, DialogBackdrop, DialogPanel, DialogTitle } from '@headlessui/react';

interface ModalProps {
  isOpen: boolean;
  onClose: () => void;
  title: string;
  children: ReactNode;
  size?: 'sm' | 'md' | 'lg';
}

const sizeClasses = {
  sm: 'max-w-md',
  md: 'max-w-lg',
  lg: 'max-w-2xl',
};

export default function Modal({ isOpen, onClose, title, children, size = 'md' }: ModalProps) {
  return (
    // Headless UI v2: атрибут transition вмикає анімацію, а стани data-closed задають її стилями Tailwind
    <Dialog open={isOpen} onClose={onClose} transition className="relative z-50">
      <DialogBackdrop
        transition
        className="fixed inset-0 bg-black/25 duration-300 ease-out data-closed:opacity-0"
      />
      <div className="fixed inset-0 flex items-center justify-center overflow-y-auto p-4">
        <DialogPanel
          transition
          className={`w-full ${sizeClasses[size]} rounded-lg bg-white p-6 shadow-xl duration-300 ease-out data-closed:scale-95 data-closed:opacity-0 dark:bg-gray-800`}
        >
          <DialogTitle className="mb-4 text-xl font-bold">{title}</DialogTitle>
          {children}
        </DialogPanel>
      </div>
    </Dialog>
  );
}
```

На основі `Modal` створити діалог підтвердження `src/components/common/ConfirmDialog.tsx`. Він замінює `window.confirm()` зі сторінки списку користувачів (лабораторна робота 5): доступний з клавіатури, відповідає дизайну застосунку й не блокує потік виконання.

```typescript
import Modal from './Modal';
import Button from './Button';

interface ConfirmDialogProps {
  isOpen: boolean;
  title: string;
  message: string;
  confirmText?: string;
  isLoading?: boolean;
  onConfirm: () => void;
  onCancel: () => void;
}

// Заміна window.confirm(): доступна з клавіатури, стилізована, не блокує потік виконання
export default function ConfirmDialog({
  isOpen,
  title,
  message,
  confirmText = 'Підтвердити',
  isLoading,
  onConfirm,
  onCancel,
}: ConfirmDialogProps) {
  return (
    <Modal isOpen={isOpen} onClose={onCancel} title={title} size="sm">
      <p className="mb-6 text-gray-600 dark:text-gray-300">{message}</p>
      <div className="flex justify-end gap-3">
        <Button variant="secondary" onClick={onCancel}>
          Скасувати
        </Button>
        <Button variant="danger" onClick={onConfirm} isLoading={isLoading}>
          {confirmText}
        </Button>
      </div>
    </Modal>
  );
}
```

Використання в `UsersListPage`: зберігаємо користувача, якого збираються видалити, і показуємо діалог, поки це значення не порожнє.

```typescript
const [userToDelete, setUserToDelete] = useState<User | null>(null);

// у рядку таблиці
<Button size="sm" variant="danger" onClick={() => setUserToDelete(user)}>Видалити</Button>

// наприкінці розмітки сторінки
<ConfirmDialog
  isOpen={userToDelete !== null}
  title="Видалення користувача"
  message={`Видалити користувача ${userToDelete?.name}? Цю дію не можна скасувати.`}
  confirmText="Видалити"
  isLoading={deleteUser.isPending}
  onConfirm={() => userToDelete && deleteUser.mutate(userToDelete.id)}
  onCancel={() => setUserToDelete(null)}
/>
```

Після успішного видалення (`onSuccess` мутації) обов'язково скидайте `setUserToDelete(null)`, щоб закрити діалог.

Компоненти табів і випадного меню з пошуком (завдання 6–7) також створіть на основі Headless UI: `TabGroup`, `TabList`, `Tab`, `TabPanels`, `TabPanel` та `Combobox`.

### Крок 4. Перетягування елементів (Drag and Drop)

Встановити dnd-kit.

```bash
npm install @dnd-kit/core @dnd-kit/sortable @dnd-kit/utilities
```

Створити компонент зі списком, що сортується, у файлі `src/components/TaskBoard.tsx`. Зверніть увагу на два моменти: тип події `DragEndEvent` замість `any` та перевірку `over` — вона дорівнює `null`, якщо елемент відпустили поза списком (у первісному коді це призводило до помилки).

```typescript
import { useState, type ReactNode } from 'react';
import {
  DndContext,
  closestCenter,
  KeyboardSensor,
  PointerSensor,
  useSensor,
  useSensors,
  type DragEndEvent,
} from '@dnd-kit/core';
import {
  arrayMove,
  SortableContext,
  sortableKeyboardCoordinates,
  useSortable,
  verticalListSortingStrategy,
} from '@dnd-kit/sortable';
import { CSS } from '@dnd-kit/utilities';

function SortableItem({ id, children }: { id: string; children: ReactNode }) {
  const { attributes, listeners, setNodeRef, transform, transition } = useSortable({ id });

  const style = {
    transform: CSS.Transform.toString(transform),
    transition,
  };

  return (
    <div ref={setNodeRef} style={style} {...attributes} {...listeners}>
      {children}
    </div>
  );
}

interface Task {
  id: string;
  title: string;
  status: string;
}

export default function TaskBoard() {
  const [tasks, setTasks] = useState<Task[]>([
    { id: '1', title: 'Завдання 1', status: 'todo' },
    { id: '2', title: 'Завдання 2', status: 'todo' },
    { id: '3', title: 'Завдання 3', status: 'todo' },
  ]);

  // Клавіатурний датчик робить перетягування доступним без миші (Пробіл — взяти, стрілки — рух)
  const sensors = useSensors(
    useSensor(PointerSensor),
    useSensor(KeyboardSensor, { coordinateGetter: sortableKeyboardCoordinates })
  );

  const handleDragEnd = ({ active, over }: DragEndEvent) => {
    // over === null, якщо елемент відпустили поза списком
    if (over && active.id !== over.id) {
      setTasks((items) => {
        const oldIndex = items.findIndex((item) => item.id === active.id);
        const newIndex = items.findIndex((item) => item.id === over.id);
        return arrayMove(items, oldIndex, newIndex);
      });
    }
  };

  return (
    <DndContext sensors={sensors} collisionDetection={closestCenter} onDragEnd={handleDragEnd}>
      <SortableContext items={tasks.map((t) => t.id)} strategy={verticalListSortingStrategy}>
        <div className="space-y-2">
          {tasks.map((task) => (
            <SortableItem key={task.id} id={task.id}>
              <div className="cursor-move rounded-lg border bg-white p-4 shadow dark:border-gray-700 dark:bg-gray-900">
                {task.title}
              </div>
            </SortableItem>
          ))}
        </div>
      </SortableContext>
    </DndContext>
  );
}
```

### Крок 5. Додавання графіків

Встановити Recharts (версія 3).

```bash
npm install recharts
```

Створити компонент з графіком у файлі `src/components/StatsChart.tsx`.

```typescript
import { CartesianGrid, Legend, Line, LineChart, ResponsiveContainer, Tooltip, XAxis, YAxis } from 'recharts';

interface ChartData {
  name: string;
  value: number;
}

interface StatsChartProps {
  data: ChartData[];
  title: string;
}

export default function StatsChart({ data, title }: StatsChartProps) {
  return (
    <div className="rounded-lg bg-white p-6 shadow dark:bg-gray-900">
      <h3 className="mb-4 text-xl font-bold">{title}</h3>
      <ResponsiveContainer width="100%" height={300}>
        <LineChart data={data}>
          <CartesianGrid strokeDasharray="3 3" />
          <XAxis dataKey="name" />
          <YAxis />
          <Tooltip />
          <Legend />
          <Line type="monotone" dataKey="value" name={title} stroke="#3B82F6" strokeWidth={2} />
        </LineChart>
      </ResponsiveContainer>
    </div>
  );
}
```

Розмістіть `StatsChart` та `TaskBoard` на сторінці Dashboard (`DashboardPage.tsx`). Дані для графіка у власному проєкті отримуйте з API через `useQuery`.

**Підказки до завдань 10–14 рівня 2.** Для решти інтерактивних компонентів скористайтеся перевіреними бібліотеками:

- нескінченне прокручування — `useInfiniteQuery` з TanStack Query та `IntersectionObserver` для елемента-«сторожа» в кінці списку;
- завантаження зображень із попереднім переглядом — `URL.createObjectURL(file)` та `FormData` для надсилання на файловий сервіс із лабораторної роботи 2; обрізання — `react-easy-crop`;
- календар і вибір дат — `react-day-picker`;
- розширений текстовий редактор — Tiptap (підключайте його через `lazy`, бо він важкий);
- анімації переходів — бібліотека Motion (пакет `motion`, імпорт з `motion/react`) або CSS-переходи Tailwind.

### Крок 6. Оптимізація продуктивності

**Розбиття коду за маршрутами.** Замінити статичні імпорти сторінок у `src/App.tsx` на `lazy` і обгорнути маршрути в `Suspense`. Кожна така сторінка потрапить в окремий файл і завантажуватиметься лише за потреби.

```typescript
import { lazy, Suspense } from 'react';
import { BrowserRouter, Route, Routes } from 'react-router';
import Layout from '@/components/layout/Layout';
import ProtectedRoute from '@/components/ProtectedRoute';
import Loading from '@/components/common/Loading';
import HomePage from '@/pages/HomePage';
import NotFoundPage from '@/pages/NotFoundPage';
import LoginPage from '@/pages/LoginPage';
import RegisterPage from '@/pages/RegisterPage';

// Кожна lazy-сторінка потрапляє в окремий файл (chunk) і завантажується лише за потреби
const AboutPage = lazy(() => import('@/pages/AboutPage'));
const DashboardPage = lazy(() => import('@/pages/DashboardPage'));
const UsersListPage = lazy(() => import('@/pages/UsersListPage'));
const UserCreatePage = lazy(() => import('@/pages/UserCreatePage'));

export default function App() {
  return (
    <BrowserRouter>
      <Suspense fallback={<Loading />}>
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
      </Suspense>
    </BrowserRouter>
  );
}
```

Створити простий компонент `src/components/common/Loading.tsx` для запасного вмісту.

```typescript
export default function Loading() {
  return (
    <div role="status" className="flex justify-center p-12">
      <div className="h-10 w-10 animate-spin rounded-full border-4 border-gray-200 border-t-primary-600" />
      <span className="sr-only">Завантаження...</span>
    </div>
  );
}
```

**Аналіз пакета.** Виконайте `npm run build` і порівняйте розміри файлів: бібліотека Recharts має потрапити лише в chunk `DashboardPage`, а не в основний. Для наочного аналізу встановіть візуалізатор і згенеруйте карту пакета:

```bash
npm install -D rollup-plugin-visualizer
```

```typescript
// vite.config.ts
import { visualizer } from 'rollup-plugin-visualizer';

plugins: [/* ... */, visualizer({ filename: 'stats.html', gzipSize: true })],
```

Додайте `stats.html` до `.gitignore`. Після збірки відкрийте файл у браузері та знайдіть найбільші залежності.

**Мемоізація.** `React.memo` має сенс для компонентів, що часто відтворюються з однаковими пропсами (елементи великих списків). Функції, які передаються у пропсах, потрібно стабілізувати через `useCallback`, інакше нове посилання щоразу «ламатиме» мемоізацію.

```typescript
import { memo } from 'react';

interface UserCardProps {
  name: string;
  email: string;
  onDelete: () => void;
}

// memo пропускає повторне відтворення, якщо пропси не змінилися.
// Це працює лише якщо onDelete стабільний (обгорнутий у useCallback у батьківському компоненті).
const UserCard = memo(function UserCard({ name, email, onDelete }: UserCardProps) {
  return (
    <div className="rounded-lg bg-white p-4 shadow dark:bg-gray-900">
      <h3 className="font-bold">{name}</h3>
      <p className="text-gray-600 dark:text-gray-400">{email}</p>
      <button type="button" onClick={onDelete}>
        Видалити
      </button>
    </div>
  );
});

export default UserCard;
```

> **Примітка.** У проєктах із React Compiler (плагін `babel-plugin-react-compiler`) така мемоізація виконується автоматично. У навчальному проєкті додавайте `memo` лише там, де ви виміряли проблему за допомогою React DevTools Profiler.

### Крок 7. Налаштування PWA та SEO

Встановити плагін PWA.

```bash
npm install -D vite-plugin-pwa
```

Підготувати іконки застосунку в каталозі `public/`: `pwa-192x192.png`, `pwa-512x512.png` та `apple-touch-icon.png` (180×180). Швидко створити їх із логотипа можна, наприклад, за допомогою `@vite-pwa/assets-generator`.

Налаштувати плагін у файлі `vite.config.ts`. Кешування API-запитів застосовується до шляхів `/api/`; адаптуйте умову до адреси вашої серверної частини (якщо API розміщене на іншому домені, порівнюйте `url.origin`).

```typescript
/// <reference types="vitest/config" />
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import tailwindcss from '@tailwindcss/vite';
import { VitePWA } from 'vite-plugin-pwa';

export default defineConfig({
  plugins: [
    react(),
    tailwindcss(),
    VitePWA({
      registerType: 'autoUpdate',
      includeAssets: ['favicon.svg', 'apple-touch-icon.png'],
      manifest: {
        name: 'My React App',
        short_name: 'MyApp',
        description: 'Сучасний React-застосунок',
        lang: 'uk',
        theme_color: '#3B82F6',
        background_color: '#ffffff',
        display: 'standalone',
        icons: [
          { src: 'pwa-192x192.png', sizes: '192x192', type: 'image/png' },
          { src: 'pwa-512x512.png', sizes: '512x512', type: 'image/png' },
        ],
      },
      workbox: {
        runtimeCaching: [
          {
            // API-запити: спершу мережа, за її відсутності — кеш (до 5 хвилин)
            urlPattern: ({ url }) => url.pathname.startsWith('/api/'),
            handler: 'NetworkFirst',
            options: {
              cacheName: 'api-cache',
              networkTimeoutSeconds: 5,
              expiration: { maxEntries: 50, maxAgeSeconds: 300 },
            },
          },
        ],
      },
    }),
  ],
  resolve: { tsconfigPaths: true },
  test: {
    environment: 'jsdom',
    setupFiles: ['./src/test/setup.ts'],
    globals: true,
  },
});
```

Перевірка: виконайте `npm run build && npm run preview`, відкрийте застосунок, у DevTools → Application переконайтеся, що service worker зареєстровано, а маніфест коректний. Потім у вкладці Network вимкніть мережу (Offline) і перезавантажте сторінку: оболонка застосунку має відкритися з кешу. Service worker не працює в режимі `npm run dev` за замовчуванням.

**SEO та Open Graph.** Доповнити `index.html` метатегами. Замініть `your-app.vercel.app` на реальну адресу після розгортання.

```html
<!doctype html>
<html lang="uk">
  <head>
    <meta charset="UTF-8" />
    <link rel="icon" type="image/svg+xml" href="/favicon.svg" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>My React App</title>
    <meta name="description" content="Короткий опис застосунку для пошукових систем (до 160 символів)." />
    <meta name="theme-color" content="#3B82F6" />
    <link rel="apple-touch-icon" href="/apple-touch-icon.png" />

    <!-- Open Graph: попередній перегляд посилання в соцмережах і месенджерах -->
    <meta property="og:type" content="website" />
    <meta property="og:title" content="My React App" />
    <meta property="og:description" content="Короткий опис застосунку." />
    <meta property="og:image" content="https://your-app.vercel.app/pwa-512x512.png" />
    <meta property="og:url" content="https://your-app.vercel.app/" />
    <meta name="twitter:card" content="summary" />
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="/src/main.tsx"></script>
  </body>
</html>
```

### Крок 8. Розгортання на Vercel

Створити файл `vercel.json` у корені проєкту. Vercel сам розпізнає проєкт Vite (команду збірки й каталог `dist`), тому потрібне лише правило перезапису для маршрутизації SPA.

```json
{
  "rewrites": [{ "source": "/(.*)", "destination": "/index.html" }]
}
```

Підключити проєкт до Vercel через GitHub:

1. Зареєструватися на vercel.com через обліковий запис GitHub.
2. Натиснути «Add New… → Project» та обрати GitHub-репозиторій.
3. У розділі Environment Variables задати `VITE_API_URL` та `VITE_SOCKET_URL` з адресами розгорнутої серверної частини (вони підставляються під час збірки).
4. Після розгортання скопіювати адресу застосунку й вказати її в змінній `FRONTEND_URL` на сервері (CORS та Socket.io), після чого перезапустити сервер.
5. Далі Vercel автоматично розгортає проєкт при кожному `push` у гілку `main`, а для Pull Request створює тимчасову адресу для перегляду.

**Альтернатива: Netlify.** Створити файл `netlify.toml` у корені проєкту й підключити репозиторій на netlify.com («Add new site → Import an existing project»). Змінні середовища задаються в Site configuration → Environment variables.

```toml
[build]
  command = "npm run build"
  publish = "dist"

[[redirects]]
  from = "/*"
  to = "/index.html"
  status = 200
```

> **Обмеження безкоштовних планів.** Vercel Hobby призначений для некомерційного використання, Netlify Free обмежений місячним лімітом кредитів. Актуальні умови перевіряйте на сайтах платформ.

### Крок 9. Налаштування CI/CD

**CI (безперервна інтеграція)** автоматично перевіряє кожну зміну: лінтинг, тести, збірку. **CD (безперервне розгортання)** публікує застосунок, якщо перевірки пройдено. Створити робочий процес GitHub Actions у файлі `.github/workflows/deploy.yml`. Розгортання виконується лише з гілки `main` і лише після успішних перевірок; для Pull Request запускаються тільки перевірки.

```yaml
name: CI/CD

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v5

      - name: Налаштування Node.js
        uses: actions/setup-node@v5
        with:
          node-version: 24
          cache: npm

      - name: Встановлення залежностей
        run: npm ci

      - name: Лінтинг
        run: npm run lint

      - name: Тести
        run: npm test

      - name: Збірка
        run: npm run build

  deploy:
    # Розгортання лише після успішних перевірок і лише з гілки main
    needs: test
    if: github.event_name == 'push' && github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    env:
      VERCEL_ORG_ID: ${{ secrets.VERCEL_ORG_ID }}
      VERCEL_PROJECT_ID: ${{ secrets.VERCEL_PROJECT_ID }}
    steps:
      - uses: actions/checkout@v5

      - uses: actions/setup-node@v5
        with:
          node-version: 24

      - name: Встановлення Vercel CLI
        run: npm install --global vercel@latest

      - name: Отримання налаштувань проєкту
        run: vercel pull --yes --environment=production --token=${{ secrets.VERCEL_TOKEN }}

      - name: Збірка
        run: vercel build --prod --token=${{ secrets.VERCEL_TOKEN }}

      - name: Розгортання
        run: vercel deploy --prebuilt --prod --token=${{ secrets.VERCEL_TOKEN }}
```

Налаштування:

1. У GitHub: Settings → Secrets and variables → Actions додати секрети `VERCEL_TOKEN` (створюється в Vercel: Account Settings → Tokens), `VERCEL_ORG_ID` та `VERCEL_PROJECT_ID` (значення з файлу `.vercel/project.json`, який з'являється після виконання `npx vercel link` у каталозі проєкту).
2. Щоб Vercel не розгортав проєкт двічі (власною інтеграцією з GitHub і цим процесом), вимкніть автоматичні розгортання з Git у налаштуваннях проєкту Vercel (Settings → Git) або залиште лише одну з двох схем.
3. Для схеми з Netlify замініть завдання `deploy` на розгортання через Netlify CLI (`netlify deploy --prod`) із секретами `NETLIFY_AUTH_TOKEN` і `NETLIFY_SITE_ID`.

> Змінні `VITE_*` у CI-збірці беруться з налаштувань проєкту на платформі (команда `vercel pull` завантажує їх).

### Крок 10. Автоматизоване та фінальне тестування

Робочий процес із кроку 9 виконує `npm test`, тому тести мають бути налаштовані. Встановити Vitest і бібліотеки тестування компонентів (Vitest 5 сумісний із Vite 8).

```bash
npm install -D vitest jsdom @testing-library/react @testing-library/user-event @testing-library/jest-dom
```

Додати скрипт у `package.json`.

```json
"scripts": {
  "test": "vitest run"
}
```

Налаштувати блок `test` у `vite.config.ts` (див. крок 7): середовище `jsdom`, файл підготовки та глобальні функції `test`/`expect`. Створити файл `src/test/setup.ts`. У jsdom немає `matchMedia`, який використовує хук теми, тому його імітують.

```typescript
import '@testing-library/jest-dom/vitest';

// jsdom не реалізує matchMedia
window.matchMedia = ((query: string) => ({
  matches: false,
  media: query,
  addEventListener() {},
  removeEventListener() {},
})) as never;
```

Додати типи глобальних функцій Vitest у `tsconfig.app.json`.

```json
"types": ["vite/client", "vitest/globals"]
```

Приклад тесту сповіщень: справжній сокет підміняється простим емітером, тому мережа не потрібна. Тест перевіряє, що подія збільшує лічильник, а після розмонтування компонент знімає власний обробник (це саме та помилка життєвого циклу, про яку йшлося в теорії).

```typescript
import { act, render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import NotificationBell from './NotificationBell';
import { useNotificationsStore } from '@/stores/notificationsStore';
import { socket } from '@/services/socket';

// Підміняємо справжній сокет мінімальним емітером подій: мережа в модульних тестах не потрібна
vi.mock('@/services/socket', () => {
  type Handler = (payload: unknown) => void;
  const handlers = new Map<string, Set<Handler>>();
  return {
    socket: {
      on: (event: string, h: Handler) => handlers.set(event, (handlers.get(event) ?? new Set()).add(h)),
      off: (event: string, h: Handler) => handlers.get(event)?.delete(h),
      emit: (event: string, payload: unknown) => handlers.get(event)?.forEach((h) => h(payload)),
      count: (event: string) => handlers.get(event)?.size ?? 0,
    },
  };
});

const fake = socket as unknown as { emit: (e: string, p: unknown) => void; count: (e: string) => number };

beforeEach(() => useNotificationsStore.getState().clearAll());

test('подія notification збільшує лічильник, клік позначає сповіщення прочитаним', async () => {
  const u = userEvent.setup();
  render(<NotificationBell />);
  expect(screen.getByRole('button', { name: /непрочитаних 0/ })).toBeInTheDocument();

  act(() => fake.emit('notification', { title: 'Нове замовлення', message: '№ 42' }));
  expect(screen.getByRole('button', { name: /непрочитаних 1/ })).toBeInTheDocument();

  await u.click(screen.getByRole('button', { name: /Сповіщення/ }));
  await u.click(screen.getByText('Нове замовлення'));
  expect(screen.getByRole('button', { name: /непрочитаних 0/ })).toBeInTheDocument();
});

test('при розмонтуванні компонент знімає власний обробник', () => {
  const { unmount } = render(<NotificationBell />);
  expect(fake.count('notification')).toBe(1);
  unmount();
  expect(fake.count('notification')).toBe(0);
});
```

Напишіть також тести для діалогу підтвердження (відкриття, підтвердження, закриття клавішею Escape) та однієї зі сторінок зі списком (мок сервісу, пагінація, видалення).

**Ручне тестування.** Проведіть комплексну перевірку всіх функцій застосунку:

- аутентифікацію та захищені маршрути;
- усі CRUD-операції;
- функціональність реального часу (відкрийте застосунок у двох вікнах);
- адаптивність на різних розмірах екрана (DevTools → режим пристрою);
- роботу PWA в офлайн-режимі;
- продуктивність через Lighthouse (вкладка DevTools) на розгорнутій версії;
- виправте знайдені помилки та недоліки.

### Крок 11. Фінальне оновлення README.md

Створити повний фінальний README.md для завершеного проєкту:

````markdown
# Назва проєкту

> Повнофункціональний вебзастосунок (full-stack) на React та Node.js

## 🎯 Опис проєкту
[Детальний опис предметної області, цілей та можливостей застосунку]

## ✨ Функціональність

### Серверна частина
- ✅ RESTful API на Node.js + Express
- ✅ База даних PostgreSQL з Prisma ORM
- ✅ Аутентифікація JWT (токени доступу й оновлення)
- ✅ WebSocket (Socket.io) для обміну даними в реальному часі
- ✅ Файловий сервіс
- ✅ Документація API (Swagger)

### Клієнтська частина
- ✅ React 19 + TypeScript
- ✅ Система аутентифікації
- ✅ CRUD-інтерфейси для всіх сутностей
- ✅ Оновлення в реальному часі через Socket.io
- ✅ Валідація форм (React Hook Form + Zod)
- ✅ Перетягування елементів (dnd-kit)
- ✅ Графіки та візуалізації
- ✅ PWA з офлайн-підтримкою
- ✅ Адаптивний дизайн

## 🛠 Технології

### Серверна частина
- Node.js 24 LTS
- Express.js 5
- PostgreSQL
- Prisma ORM 7
- Socket.io
- JWT + Argon2id (хешування паролів)
- Multer (файли)

### Клієнтська частина
- React 19
- TypeScript
- Vite
- Tailwind CSS 4
- React Router 8
- Axios
- Socket.io-client
- Zustand (клієнтський стан) та TanStack Query (дані сервера)
- React Hook Form + Zod
- Recharts (графіки)
- dnd-kit (перетягування)

## 📦 Встановлення та запуск

### Вимоги
- Node.js 24 LTS
- PostgreSQL 14+
- npm

### Серверна частина

```bash
# Клонування репозиторію
git clone [URL]
cd backend

# Встановлення залежностей
npm install

# Налаштування .env
cp .env.example .env
# Встановити DATABASE_URL та інші змінні

# Міграція бази даних і генерація клієнта Prisma
npx prisma migrate dev
npx prisma generate

# Запуск сервера
npm run dev
```

### Клієнтська частина

```bash
cd frontend

# Встановлення залежностей
npm install

# Налаштування .env
cp .env.example .env
# Встановити VITE_API_URL=http://localhost:3000/api
# та VITE_SOCKET_URL=http://localhost:3000

# Запуск у режимі розробки
npm run dev
```

## 🚀 Розгортання

### Серверна частина
Розгорнуто на: [Render/Railway/…]
URL: https://api.example.com

### Клієнтська частина
Розгорнуто на: Vercel/Netlify
URL: https://myapp.vercel.app

### Змінні середовища
Перелік змінних для обох частин (без значень секретів) і порядок налаштування:
`FRONTEND_URL` на сервері, `VITE_API_URL` і `VITE_SOCKET_URL` на клієнті.

## 📁 Структура проєкту

### Серверна частина
```
backend/
├── src/
│   ├── controllers/    # Бізнес-логіка
│   ├── routes/         # API-маршрути
│   ├── middleware/     # Проміжні обробники
│   ├── socket/         # Socket.io
│   └── utils/          # Допоміжні функції
├── prisma/
│   └── schema.prisma   # Схема БД
└── uploads/            # Завантажені файли
```

### Клієнтська частина
```
frontend/
├── src/
│   ├── components/     # React-компоненти
│   ├── pages/          # Сторінки
│   ├── services/       # API-сервіси та Socket.io
│   ├── stores/         # Сховища Zustand
│   ├── schemas/        # Схеми Zod
│   ├── types/          # Типи TypeScript
│   └── hooks/          # Власні хуки
└── public/             # Статичні файли
```

## 📸 Скріншоти

### Головна сторінка
[Скріншот]

### Dashboard
[Скріншот]

### CRUD-інтерфейс
[Скріншот]

### Оновлення в реальному часі
[Скріншот]

## 🔑 Тестові облікові дані

```
Email: demo@example.com
Password: password123
```

## 📊 Продуктивність

Вкажіть **власні виміряні** значення (Lighthouse на розгорнутій версії):

- Performance / Accessibility / Best Practices / SEO: [значення]
- Розмір початкового завантаження (gzip): [значення]
- LCP: [значення], INP: [значення], CLS: [значення]

## 🔐 Безпека

- JWT-токени з обмеженим терміном дії (токен оновлення з ротацією)
- Хешування паролів Argon2id
- Налаштування CORS
- Валідація вхідних даних на клієнті та сервері
- Захист від SQL-ін'єкцій завдяки Prisma
- Захист від XSS (екранування у React, заголовки helmet)
- Обмеження частоти запитів (rate limiting)

## 📝 Документація API

Swagger-документація доступна за адресою: `/api/docs`

## 🧪 Тестування

```bash
# Тести серверної частини
cd backend
npm test

# Тести клієнтської частини
cd frontend
npm test
```

## 👨‍💻 Автор

**[Ваше ім'я]**
- Група: [Номер групи]
- Email: [email]
- GitHub: [@username](https://github.com/username)
````

### Крок 12. Здача роботи

Зробити фінальний коміт усіх змін до GitHub репозиторію. Переконатися, що:
- README.md містить всю необхідну інформацію;
- додані скріншоти всіх ключових екранів;
- застосунок успішно розгорнутий та доступний онлайн;
- в README.md вказані робочі посилання на версії у робочому середовищі;
- додані тестові облікові дані для перевірки;
- робочий процес CI у GitHub Actions виконується успішно (лінтинг, тести, збірка).

Здати роботу через Moodle, вставивши посилання на GitHub репозиторій.

[⬆️ Здати лабораторну роботу](https://moodle.vcolnuft.volyn.ua/moodle/course/view.php?id=17#section-2)

## ❓ Контрольні запитання

1. Поясніть різницю між WebSocket та HTTP. Коли доцільно використовувати WebSocket, а коли достатньо HTTP?
2. Як правильно керувати життєвим циклом з'єднання Socket.io в React-застосунку? Чому з'єднання створюють один раз, а не в кожному компоненті, і чому важливо знімати власний обробник події?
3. Що таке портали React і як вони використовуються для модальних вікон? Які вимоги доступності має виконувати модальне вікно?
4. Порівняйте бібліотеки для перетягування елементів у React. Які переваги має dnd-kit і як забезпечується доступність з клавіатури?
5. Поясніть концепцію розбиття коду. Як працюють `React.lazy()` і `Suspense` та як збирач формує окремі chunks?
6. Що таке Service Worker і як він забезпечує офлайн-функціональність у PWA? Чим відрізняються стратегії `CacheFirst`, `NetworkFirst` і `StaleWhileRevalidate` та коли застосовувати кожну?
7. Які метрики продуктивності важливі для вебзастосунків (LCP, INP, CLS)? Як їх виміряти та покращити?
8. Порівняйте Vercel та Netlify для розгортання React-застосунків. Навіщо SPA потрібне правило перезапису на `index.html` і чому змінні `VITE_*` потребують повторної збірки?
9. Що таке CI/CD і які переваги дає автоматизація розгортання? Чому розгортання виконують лише після успішних тестів і лише з головної гілки?
10. Як забезпечити SEO для односторінкових застосунків і в яких випадках потрібні SSR або генерація статичних сторінок?
