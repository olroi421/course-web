# Лекція 20. Сучасні інструменти розробки

## Вступ до сучасної екосистеми інструментів розробки

### Еволюція інструментів веброзробки

Сучасна веброзробка вийшла далеко за межі простого написання коду в текстовому редакторі. Професійні розробники використовують складну екосистему інструментів, які автоматизують рутинні завдання, запобігають помилкам, покращують якість коду та прискорюють процес розробки. Ця еволюція відображає зростання складності вебзастосунків та підвищення стандартів якості в індустрії.

Історично розробники використовували мінімальний набір інструментів: текстовий редактор для написання коду, браузер для тестування та FTP-клієнт для деплою. Проте зростання складності проєктів призвело до появи спеціалізованих інструментів для кожного етапу розробки. Сьогодні типовий проєкт може використовувати десятки різних інструментів, інтегрованих в єдиний автоматизований workflow.

**Ключові категорії сучасних інструментів розробки:**

Системи контролю версій дозволяють відстежувати зміни в коді, працювати командою та керувати різними версіями проєкту. Git став де-facto стандартом в індустрії завдяки своїй гнучкості та потужним можливостям.

Інструменти для забезпечення якості коду включають лінтери, форматери та системи статичного аналізу. Вони автоматично виявляють потенційні проблеми, забезпечують єдиний стиль коду та запобігають типовим помилкам.

Системи автоматизації виконують рутинні завдання: запускають тести, форматують код, створюють збірки. Це економить час та зменшує ймовірність людських помилок.

Інструменти для аналізу та оптимізації допомагають виявляти проблеми з продуктивністю, розміром збірок та іншими аспектами якості застосунку.

Контейнеризація та віртуалізація забезпечують ідентичне середовище розробки для всієї команди та спрощують деплой застосунків.

### Філософія сучасного інструментального підходу

Сучасні інструменти розробки базуються на кількох фундаментальних принципах. Автоматизація рутинних завдань звільняє розробників для творчої роботи та складних завдань. Раннє виявлення проблем дозволяє виправляти помилки на етапі написання коду, а не після деплою. Єдині стандарти коду забезпечують узгодженість в командній розробці. Швидкий зворотний зв'язок через автоматичні перевірки прискорює цикл розробки.

**Принципи побудови ефективного workflow:**

Інструменти повинні допомагати, а не заважати розробці. Це означає швидку роботу, зрозумілі повідомлення про помилки та мінімальну конфігурацію. Автоматизація має бути максимальною там, де це можливо, щоб розробники могли зосередитися на написанні коду. Консистентність конфігурації між різними проєктами спрощує перехід між ними. Інтеграція інструментів в єдину систему створює безперервний потік від написання коду до деплою.

## Git workflow та колаборація

### Фундаментальні концепції Git

**Git** є розподіленою системою контролю версій, яка дозволяє відстежувати зміни в коді, працювати з різними версіями проєкту та співпрацювати з іншими розробниками. На відміну від централізованих систем, кожен розробник має повну копію репозиторію зі всією історією змін.

**Основні концепції Git:**

Репозиторій (repository) є базою даних, яка зберігає всю історію змін проєкту. Локальний репозиторій знаходиться на комп'ютері розробника, віддалений (remote) - на сервері для співпраці.

Коміт (commit) є знімком стану проєкту в певний момент часу. Кожен коміт має унікальний ідентифікатор, автора, дату та повідомлення, що описує зміни.

Гілка (branch) дозволяє розробляти нові функції ізольовано від основного коду. Після завершення роботи гілку можна об'єднати з основною.

Індекс (staging area) є проміжною областю, куди додаються файли перед створенням коміту. Це дозволяє вибірково включати зміни в коміт.

```mermaid
graph LR
    A[Working Directory] -->|git add| B[Staging Area]
    B -->|git commit| C[Local Repository]
    C -->|git push| D[Remote Repository]
    D -->|git pull| C
    C -->|git checkout| A
```

### Базові операції Git

Основні команди Git формують фундамент для роботи з версіями коду. Розуміння їх призначення та правильне використання є критично важливим для ефективної розробки.

```bash
# Ініціалізація нового репозиторію
git init

# Клонування існуючого репозиторію
git clone https://github.com/username/project.git

# Перевірка статусу репозиторію
git status

# Додавання файлів до індексу
git add file.js                    # Конкретний файл
git add src/                       # Вся папка
git add .                          # Всі зміни в поточній директорії

# Створення коміту
git commit -m "Add user authentication"

# Перегляд історії комітів
git log                            # Повна історія
git log --oneline                  # Компактний формат
git log --graph --all              # Графічне представлення гілок

# Відправка змін на віддалений сервер
git push origin main

# Отримання змін з віддаленого сервера
git pull origin main

# Перегляд віддалених репозиторіїв
git remote -v

# Додавання нового віддаленого репозиторію
git remote add upstream https://github.com/original/project.git
```

**Анатомія хорошого коміту:**

Повідомлення коміту має бути інформативним та структурованим. Перший рядок (до 50 символів) містить стислий опис змін у вказівному способі. Якщо потрібні додаткові пояснення, після порожнього рядка слідує детальний опис (до 72 символів на рядок).

```bash
# Хороший коміт
git commit -m "Add password reset functionality

Implement password reset flow with email verification.
Users can now request password reset link via email.
Link expires after 1 hour for security reasons.

Fixes #123"

# Поганий коміт
git commit -m "fixed stuff"
git commit -m "update"
git commit -m "changes"
```

**Конвенції для повідомлень комітів:**

Індустрія виробила стандартні префікси для категоризації комітів. Префікс `feat` позначає нову функціональність, `fix` - виправлення помилок, `docs` - зміни в документації, `style` - форматування коду без зміни логіки, `refactor` - рефакторинг коду, `test` - додавання або виправлення тестів, `chore` - технічні зміни (оновлення залежностей, конфігурацій).

```bash
feat: Add user profile page
fix: Resolve memory leak in data fetching
docs: Update API documentation
style: Format code with Prettier
refactor: Extract validation logic to separate module
test: Add unit tests for authentication
chore: Update dependencies to latest versions
```

### Робота з гілками

Гілки дозволяють розробляти різні функції паралельно без конфліктів. Це одна з найпотужніших можливостей Git.

```bash
# Створення нової гілки
git branch feature/user-authentication

# Перемикання на гілку
git checkout feature/user-authentication

# Створення та перемикання одночасно
git checkout -b feature/user-authentication

# Перегляд всіх гілок
git branch -a

# Видалення гілки
git branch -d feature/user-authentication    # Безпечне видалення
git branch -D feature/user-authentication    # Примусове видалення

# Об'єднання гілок
git checkout main
git merge feature/user-authentication

# Rebase - альтернатива merge
git checkout feature/user-authentication
git rebase main
```

**Стратегії об'єднання гілок:**

Merge створює новий коміт, що об'єднує дві гілки, зберігаючи повну історію. Це безпечний підхід, але може створювати складну історію з багатьма merge-комітами.

Rebase переписує історію, розміщуючи коміти з однієї гілки на вершину іншої. Це створює лінійну історію, але потребує обережності при роботі з публічними гілками.

Squash об'єднує всі коміти з гілки в один, що корисно для об'єднання багатьох дрібних комітів в один осмислений.

```bash
# Merge зі створенням merge-коміту
git merge --no-ff feature/user-authentication

# Merge з fast-forward (якщо можливо)
git merge --ff-only feature/user-authentication

# Squash merge - об'єднати всі коміти в один
git merge --squash feature/user-authentication
git commit -m "Add user authentication feature"
```

### Git Flow та альтернативні workflow

**Git Flow** є популярною моделлю розгалуження для управління розробкою. Вона визначає структуру гілок та правила їх використання.

```mermaid
gitGraph
    commit
    branch develop
    checkout develop
    commit
    branch feature/login
    checkout feature/login
    commit
    commit
    checkout develop
    merge feature/login
    branch release/1.0
    checkout release/1.0
    commit
    checkout main
    merge release/1.0
    checkout develop
    merge release/1.0
```

**Основні гілки в Git Flow:**

Гілка `main` (або `master`) містить production-ready код. Кожен коміт в цій гілці є релізом. Гілка `develop` містить останні завершені розробки для наступного релізу. Це інтеграційна гілка для features.

**Допоміжні гілки:**

Feature branches створюються з `develop` для розробки нових функцій. Після завершення вони об'єднуються назад в `develop`. Найменування: `feature/feature-name`.

Release branches створюються з `develop` для підготовки нового релізу. Дозволені лише виправлення помилок та оновлення метаданих. Після завершення об'єднується в `main` та `develop`. Найменування: `release/version-number`.

Hotfix branches створюються з `main` для термінових виправлень в production. Після завершення об'єднується в `main` та `develop`. Найменування: `hotfix/hotfix-name`.

```bash
# Початок роботи над новою функцією
git checkout develop
git checkout -b feature/user-profile

# Робота над функцією
git add .
git commit -m "feat: Add user profile page"

# Завершення роботи над функцією
git checkout develop
git merge --no-ff feature/user-profile
git branch -d feature/user-profile
git push origin develop

# Підготовка релізу
git checkout develop
git checkout -b release/1.0.0
# Виправлення останніх помилок, оновлення версії

# Завершення релізу
git checkout main
git merge --no-ff release/1.0.0
git tag -a v1.0.0 -m "Release version 1.0.0"
git checkout develop
git merge --no-ff release/1.0.0
git branch -d release/1.0.0

# Термінове виправлення
git checkout main
git checkout -b hotfix/critical-bug
# Виправлення помилки
git checkout main
git merge --no-ff hotfix/critical-bug
git tag -a v1.0.1 -m "Hotfix version 1.0.1"
git checkout develop
git merge --no-ff hotfix/critical-bug
git branch -d hotfix/critical-bug
```

**GitHub Flow - спрощена альтернатива:**

GitHub Flow є простішою моделлю, яка підходить для проєктів з частими релізами та continuous deployment.

Основні принципи GitHub Flow: єдина основна гілка `main`, що завжди готова до деплою; всі зміни розробляються в feature branches; регулярні pull requests для code review; автоматичне тестування перед merge; деплой одразу після merge в `main`.

```bash
# GitHub Flow workflow
git checkout main
git pull origin main
git checkout -b feature/new-feature

# Розробка та коміти
git add .
git commit -m "feat: Implement new feature"

# Відправка на GitHub
git push origin feature/new-feature

# Створення Pull Request через веб-інтерфейс GitHub
# Code review, обговорення, виправлення

# Після схвалення - merge через GitHub
# Автоматичний деплой на production
```

### Розв'язання конфліктів

Конфлікти виникають, коли Git не може автоматично об'єднати зміни. Розуміння причин конфліктів та вміння їх розв'язувати є критично важливою навичкою.

```bash
# Виникнення конфлікту при merge
git checkout main
git merge feature/user-profile
# CONFLICT (content): Merge conflict in src/components/User.jsx

# Перегляд конфліктних файлів
git status
# Both modified:   src/components/User.jsx

# Файл з конфліктом виглядає так:
<<<<<<< HEAD
const userEmail = user.email;
=======
const userEmail = user.emailAddress;
>>>>>>> feature/user-profile

# Розв'язання конфлікту вручну
# Видаляємо маркери конфлікту та залишаємо потрібний код
const userEmail = user.email;

# Позначаємо конфлікт як розв'язаний
git add src/components/User.jsx

# Завершуємо merge
git commit -m "Merge feature/user-profile with conflict resolution"

# Скасування merge, якщо щось пішло не так
git merge --abort
```

**Інструменти для розв'язання конфліктів:**

VS Code має вбудований інструмент для візуального розв'язання конфліктів з кнопками "Accept Current Change", "Accept Incoming Change", "Accept Both Changes".

Git mergetool запускає зовнішні інструменти для розв'язання конфліктів:

```bash
# Налаштування mergetool
git config --global merge.tool vscode
git config --global mergetool.vscode.cmd 'code --wait $MERGED'

# Використання mergetool
git mergetool
```

### Продвинуті техніки Git

**Інтерактивний rebase** дозволяє редагувати історію комітів: об'єднувати, переупорядковувати, редагувати повідомлення.

```bash
# Інтерактивний rebase останніх 3 комітів
git rebase -i HEAD~3

# У текстовому редакторі з'являється список комітів:
# pick abc123 feat: Add login form
# pick def456 fix: Fix validation
# pick ghi789 style: Format code

# Можливі операції:
# pick - використати коміт
# reword - змінити повідомлення коміту
# edit - зупинитися для редагування
# squash - об'єднати з попереднім комітом
# fixup - як squash, але відкинути повідомлення
# drop - видалити коміт

# Приклад об'єднання комітів:
pick abc123 feat: Add login form
squash def456 fix: Fix validation
squash ghi789 style: Format code
```

**Cherry-pick** дозволяє застосувати конкретний коміт з іншої гілки:

```bash
# Застосувати коміт з іншої гілки
git cherry-pick abc123

# Застосувати кілька комітів
git cherry-pick abc123 def456 ghi789

# Cherry-pick без автоматичного коміту
git cherry-pick -n abc123
```

**Stash** зберігає поточні зміни для подальшого використання:

```bash
# Зберегти поточні зміни
git stash

# Зберегти з описом
git stash save "Work in progress on feature X"

# Переглянути список stashes
git stash list

# Застосувати останній stash
git stash apply

# Застосувати конкретний stash
git stash apply stash@{1}

# Застосувати та видалити stash
git stash pop

# Видалити stash
git stash drop stash@{0}

# Очистити всі stashes
git stash clear

# Створити гілку з stash
git stash branch feature/new-work
```

**Reflog** зберігає історію всіх операцій, навіть видалених комітів:

```bash
# Переглянути історію операцій
git reflog

# Відновити видалений коміт або гілку
git reflog
# Знайти потрібний коміт
git checkout abc123

# Відновити видалену гілку
git checkout -b recovered-branch abc123
```

### Командна колаборація через Pull Requests

**Pull Request (PR)** або **Merge Request (MR)** є механізмом для code review та обговорення змін перед їх інтеграцією в основну гілку.

**Процес створення Pull Request:**

Розробник створює feature branch та працює над функцією. Після завершення відправляє гілку на GitHub. Створює Pull Request через веб-інтерфейс з описом змін. Колеги проводять code review, залишають коментарі. Розробник вносить правки за потреби. Після схвалення PR об'єднується в основну гілку.

```bash
# Підготовка до Pull Request
git checkout -b feature/user-notifications
# Робота над функцією
git add .
git commit -m "feat: Add user notification system"

# Оновлення з основної гілки перед PR
git checkout main
git pull origin main
git checkout feature/user-notifications
git rebase main

# Відправка на GitHub
git push origin feature/user-notifications

# Після цього створюємо PR через веб-інтерфейс GitHub
```

**Структура опису Pull Request:**

Опис має містити чіткий заголовок, що описує зміни. Детальний опис того, що саме змінено та чому. Посилання на issues або tickets, які вирішує PR. Скріншоти або GIF для візуальних змін. Інструкції для тестування змін. Checklist виконаних завдань.

**Приклад шаблону PR:**

```markdown
## Опис змін
Додано систему сповіщень для користувачів з можливістю відправки email та push-повідомлень.

## Тип змін
- [x] Нова функціональність
- [ ] Виправлення помилки
- [ ] Зміна документації

## Пов'язані issues
Closes #123
Related to #456

## Як тестувати
1. Зареєструватися як новий користувач
2. Перейти в налаштування профілю
3. Увімкнути сповіщення
4. Виконати дію, що генерує сповіщення

## Checklist
- [x] Код відповідає стилю проєкту
- [x] Додано тести
- [x] Тести проходять локально
- [x] Оновлено документацію
- [x] Проведено self-review

## Скріншоти
[Додати скріншоти]
```

**Процес code review:**

Рев'ювер перевіряє логіку та архітектуру рішення. Шукає потенційні баги та граничні випадки. Оцінює читабельність та підтримуваність коду. Перевіряє дотримання стандартів проєкту. Тестує зміни локально при необхідності. Залишає конструктивні коментарі з поясненнями.

```bash
# Локальна перевірка PR
git fetch origin
git checkout feature/user-notifications

# Запуск тестів
npm test

# Запуск застосунку
npm run dev

# Після перевірки - схвалення або запит змін через GitHub
```

## ESLint та Prettier: автоматизація якості коду

### ESLint: статичний аналіз JavaScript коду

**ESLint** є інструментом для статичного аналізу JavaScript коду, який виявляє проблеми, забезпечує дотримання стилю коду та запобігає типовим помилкам.

**Філософія ESLint:**

ESLint базується на принципі правил (rules), які можна налаштовувати індивідуально. Кожне правило може бути у стані "error" (помилка, блокує виконання), "warn" (попередження, не блокує) або "off" (вимкнено).

```bash
# Встановлення ESLint
npm install --save-dev eslint

# Ініціалізація конфігурації
npx eslint --init

# Під час ініціалізації ESLint запитає:
# - Як ви хочете використовувати ESLint? (перевірка синтаксису, пошук проблем, code style)
# - Який тип модулів використовує ваш проєкт? (ES modules / CommonJS)
# - Який фреймворк? (React / Vue / None)
# - Чи використовується TypeScript? (Yes / No)
# - Де виконується код? (Browser / Node)
# - Який формат конфігураційного файлу? (JavaScript / YAML / JSON)
```

**Базова конфігурація ESLint:**

Файл `.eslintrc.json` містить налаштування правил для проєкту:

```json
{
  "env": {
    "browser": true,
    "es2021": true,
    "node": true
  },
  "extends": [
    "eslint:recommended",
    "plugin:react/recommended",
    "plugin:react-hooks/recommended"
  ],
  "parserOptions": {
    "ecmaFeatures": {
      "jsx": true
    },
    "ecmaVersion": "latest",
    "sourceType": "module"
  },
  "plugins": [
    "react"
  ],
  "rules": {
    "indent": ["error", 2],
    "linebreak-style": ["error", "unix"],
    "quotes": ["error", "single"],
    "semi": ["error", "always"],
    "no-unused-vars": "warn",
    "no-console": "warn",
    "react/prop-types": "off"
  }
}
```

**Популярні конфігурації ESLint:**

Замість налаштування кожного правила вручну, можна використовувати готові конфігурації:

```bash
# Airbnb style guide - один з найпопулярніших
npm install --save-dev eslint-config-airbnb

# Standard JS - мінімалістичний підхід
npm install --save-dev eslint-config-standard

# Google style guide
npm install --save-dev eslint-config-google
```

```json
{
  "extends": ["airbnb", "airbnb/hooks"]
}
```

**Використання ESLint:**

```bash
# Перевірка всіх файлів
npx eslint .

# Перевірка конкретного файлу
npx eslint src/components/User.jsx

# Автоматичне виправлення проблем
npx eslint src/ --fix

# Ігнорування файлів через .eslintignore
echo "node_modules
dist
build
coverage
*.config.js" > .eslintignore
```

**Інтеграція з package.json:**

```json
{
  "scripts": {
    "lint": "eslint src/",
    "lint:fix": "eslint src/ --fix"
  }
}
```

**Налаштування правил для конкретних випадків:**

ESLint дозволяє вимикати правила для конкретних рядків або файлів:

```javascript
// Вимкнути правило для наступного рядка
// eslint-disable-next-line no-console
console.log('Debug information');

// Вимкнути правило для всього файлу
/* eslint-disable no-console */
console.log('Debug 1');
console.log('Debug 2');

// Вимкнути кілька правил для блоку коду
/* eslint-disable no-console, no-unused-vars */
const debugData = fetchData();
console.log(debugData);
/* eslint-enable no-console, no-unused-vars */
```

### Prettier: автоматичне форматування коду

**Prettier** є opinionated code formatter, який автоматично форматує код згідно з визначеними правилами. На відміну від ESLint, який фокусується на якості коду, Prettier зосереджений виключно на форматуванні.

**Філософія Prettier:**

Prettier має мінімальну кількість опцій конфігурації, що усуває дискусії про стиль коду в команді. Інструмент підтримує JavaScript, TypeScript, JSX, CSS, SCSS, HTML, JSON, Markdown та інші формати.

```bash
# Встановлення Prettier
npm install --save-dev prettier

# Створення конфігураційного файлу
echo {} > .prettierrc.json

# Створення файлу ігнорування
echo "node_modules
dist
build
coverage" > .prettierignore
```

**Конфігурація Prettier:**

Файл `.prettierrc.json` містить налаштування форматування:

```json
{
  "semi": true,
  "trailingComma": "es5",
  "singleQuote": true,
  "printWidth": 80,
  "tabWidth": 2,
  "useTabs": false,
  "arrowParens": "always",
  "endOfLine": "lf",
  "bracketSpacing": true,
  "jsxSingleQuote": false,
  "jsxBracketSameLine": false
}
```

**Використання Prettier:**

```bash
# Форматування всіх файлів
npx prettier --write .

# Перевірка без змін
npx prettier --check .

# Форматування конкретних файлів
npx prettier --write src/**/*.{js,jsx,ts,tsx,css,md}
```

**Інтеграція з package.json:**

```json
{
  "scripts": {
    "format": "prettier --write .",
    "format:check": "prettier --check ."
  }
}
```

### Інтеграція ESLint та Prettier

ESLint та Prettier можуть конфліктувати в питаннях форматування. Для вирішення цієї проблеми використовують спеціальні конфігурації:

```bash
# Встановлення конфігурацій для інтеграції
npm install --save-dev eslint-config-prettier eslint-plugin-prettier
```

**Налаштування ESLint для роботи з Prettier:**

```json
{
  "extends": [
    "eslint:recommended",
    "plugin:react/recommended",
    "prettier"
  ],
  "plugins": ["prettier"],
  "rules": {
    "prettier/prettier": "error"
  }
}
```

Конфігурація `eslint-config-prettier` вимикає всі правила ESLint, які можуть конфліктувати з Prettier. Плагін `eslint-plugin-prettier` запускає Prettier як правило ESLint та повідомляє про різниці як помилки ESLint.

**Єдина команда для лінтингу та форматування:**

```json
{
  "scripts": {
    "lint": "eslint src/",
    "format": "prettier --write .",
    "check": "eslint src/ && prettier --check ."
  }
}
```

### Інтеграція з редакторами коду

**VS Code конфігурація:**

Створення файлу `.vscode/settings.json` для автоматичного форматування при збереженні:

```json
{
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  },
  "eslint.validate": [
    "javascript",
    "javascriptreact",
    "typescript",
    "typescriptreact"
  ]
}
```

**Рекомендовані розширення для VS Code:**

Файл `.vscode/extensions.json` містить список рекомендованих розширень:

```json
{
  "recommendations": [
    "dbaeumer.vscode-eslint",
    "esbenp.prettier-vscode",
    "editorconfig.editorconfig"
  ]
}
```

**EditorConfig** забезпечує базову конфігурацію редактора:

Файл `.editorconfig`:

```ini
root = true

[*]
charset = utf-8
end_of_line = lf
insert_final_newline = true
trim_trailing_whitespace = true

[*.{js,jsx,ts,tsx,json,css,scss,md}]
indent_style = space
indent_size = 2

[*.md]
trim_trailing_whitespace = false
```

## Husky та pre-commit hooks

### Концепція Git Hooks

**Git Hooks** є скриптами, які Git виконує до або після певних подій: commit, push, merge тощо. Вони дозволяють автоматизувати перевірки та дії в процесі розробки.

**Типи Git Hooks:**

Pre-commit hook виконується перед створенням коміту. Використовується для перевірки коду лінтерами, запуску тестів, форматування коду.

Pre-push hook виконується перед відправкою змін на сервер. Корисний для запуску всіх тестів та складніших перевірок.

Commit-msg hook перевіряє повідомлення коміту. Забезпечує дотримання конвенцій для повідомлень.

```mermaid
sequenceDiagram
    participant Dev as Розробник
    participant Hook as Git Hook
    participant Linter as ESLint/Prettier
    participant Tests as Test Suite
    participant Repo as Repository

    Dev->>Hook: git commit
    Hook->>Linter: Перевірка коду
    alt Лінтинг пройшов
        Linter-->>Hook: ✓ Успішно
        Hook->>Tests: Запуск тестів
        alt Тести пройшли
            Tests-->>Hook: ✓ Успішно
            Hook->>Repo: Створити коміт
            Repo-->>Dev: ✓ Коміт створено
        else Тести провалилися
            Tests-->>Hook: ✗ Помилка
            Hook-->>Dev: ✗ Коміт відхилено
        end
    else Лінтинг провалився
        Linter-->>Hook: ✗ Помилка
        Hook-->>Dev: ✗ Коміт відхилено
    end
```

### Husky: автоматизація Git Hooks

**Husky** спрощує налаштування та використання Git Hooks, дозволяючи визначати їх у package.json або окремих файлах.

```bash
# Встановлення Husky
npm install --save-dev husky

# Ініціалізація Husky
npx husky init

# Створення pre-commit hook
npx husky add .husky/pre-commit "npm test"
```

**Базова конфігурація Husky:**

Після ініціалізації створюється папка `.husky` з файлами hooks. Файл `.husky/pre-commit`:

```bash
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

npm run lint
npm run format:check
```

**Конфігурація в package.json:**

```json
{
  "scripts": {
    "prepare": "husky install",
    "lint": "eslint src/",
    "format": "prettier --write .",
    "format:check": "prettier --check .",
    "test": "jest"
  }
}
```

### lint-staged: оптимізація перевірок

**lint-staged** запускає лінтери тільки для файлів, які додані до staging area, що значно прискорює перевірки.

```bash
# Встановлення lint-staged
npm install --save-dev lint-staged
```

**Конфігурація lint-staged:**

Файл `.lintstagedrc.json`:

```json
{
  "*.{js,jsx,ts,tsx}": [
    "eslint --fix",
    "prettier --write"
  ],
  "*.{css,scss}": [
    "prettier --write"
  ],
  "*.{json,md}": [
    "prettier --write"
  ]
}
```

**Інтеграція з Husky:**

Файл `.husky/pre-commit`:

```bash
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

npx lint-staged
```

**Повна конфігурація в package.json:**

```json
{
  "scripts": {
    "prepare": "husky install"
  },
  "lint-staged": {
    "*.{js,jsx,ts,tsx}": [
      "eslint --fix",
      "prettier --write"
    ],
    "*.{css,scss,json,md}": [
      "prettier --write"
    ]
  }
}
```

### Commitlint: валідація повідомлень комітів

**Commitlint** перевіряє повідомлення комітів на відповідність конвенціям.

```bash
# Встановлення commitlint
npm install --save-dev @commitlint/cli @commitlint/config-conventional
```

**Конфігурація commitlint:**

Файл `.commitlintrc.json`:

```json
{
  "extends": ["@commitlint/config-conventional"],
  "rules": {
    "type-enum": [
      2,
      "always",
      [
        "feat",
        "fix",
        "docs",
        "style",
        "refactor",
        "test",
        "chore",
        "revert"
      ]
    ],
    "subject-max-length": [2, "always", 100],
    "subject-case": [2, "always", "sentence-case"]
  }
}
```

**Інтеграція з Husky:**

```bash
# Створення commit-msg hook
npx husky add .husky/commit-msg 'npx --no -- commitlint --edit $1'
```

Тепер commitlint буде перевіряти кожне повідомлення коміту:

```bash
# Коміт, що пройде перевірку
git commit -m "feat: Add user authentication"

# Коміт, що не пройде перевірку
git commit -m "added some stuff"
# ✗ subject may not be empty [subject-empty]
# ✗ type may not be empty [type-empty]
```

### Комплексна система перевірок

**Повна конфігурація з усіма інструментами:**

Файл `package.json`:

```json
{
  "scripts": {
    "prepare": "husky install",
    "lint": "eslint src/ --max-warnings=0",
    "lint:fix": "eslint src/ --fix",
    "format": "prettier --write .",
    "format:check": "prettier --check .",
    "type-check": "tsc --noEmit",
    "test": "jest",
    "test:coverage": "jest --coverage"
  },
  "lint-staged": {
    "*.{js,jsx,ts,tsx}": [
      "eslint --fix --max-warnings=0",
      "prettier --write",
      "jest --findRelatedTests --bail"
    ],
    "*.{css,scss}": [
      "prettier --write"
    ],
    "*.{json,md}": [
      "prettier --write"
    ]
  }
}
```

Файл `.husky/pre-commit`:

```bash
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

# Перевірка типів TypeScript
npm run type-check

# Лінтинг та форматування змінених файлів
npx lint-staged
```

Файл `.husky/commit-msg`:

```bash
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

# Перевірка повідомлення коміту
npx --no -- commitlint --edit $1
```

Файл `.husky/pre-push`:

```bash
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

# Запуск всіх тестів перед push
npm run test

# Перевірка покриття тестами
npm run test:coverage
```

**Обхід hooks при необхідності:**

Іноді потрібно створити коміт без перевірок, наприклад для WIP (Work In Progress) комітів:

```bash
# Обійти pre-commit hook
git commit --no-verify -m "WIP: Experimental feature"

# Або скорочена форма
git commit -n -m "WIP: Experimental feature"
```

Проте це слід використовувати обережно та тільки у виняткових випадках.

## Bundle аналіз та оптимізація

### Розуміння процесу збірки

Сучасні вебзастосунки використовують bundler'и для об'єднання модулів у оптимізовані файли. Розуміння процесу збірки критично важливе для оптимізації продуктивності.

**Популярні bundler'и:**

Webpack є найпотужнішим та найконфігурованішим bundler'ом з великою екосистемою плагінів. Vite використовує ES modules в development та Rollup для production, забезпечуючи блискавичну швидкість. Parcel надає zero-config підхід з автоматичною оптимізацією. esbuild є надшвидким bundler'ом написаним на Go.

```mermaid
graph TB
    A[Source Code] --> B[Entry Point]
    B --> C[Module Resolution]
    C --> D[Dependency Graph]
    D --> E[Transformations]
    E --> F[Code Splitting]
    F --> G[Minification]
    G --> H[Output Bundles]

    E --> E1[Babel]
    E --> E2[TypeScript]
    E --> E3[CSS Processing]
```

### Аналіз розміру збірки

**webpack-bundle-analyzer** візуалізує розмір та структуру bundle:

```bash
# Встановлення для Webpack
npm install --save-dev webpack-bundle-analyzer

# Для Vite
npm install --save-dev rollup-plugin-visualizer
```

**Конфігурація для Webpack:**

```javascript
// webpack.config.js
const { BundleAnalyzerPlugin } = require('webpack-bundle-analyzer');

module.exports = {
  // ... інша конфігурація
  plugins: [
    new BundleAnalyzerPlugin({
      analyzerMode: 'static',
      reportFilename: 'bundle-report.html',
      openAnalyzer: false
    })
  ]
};
```

**Конфігурація для Vite:**

```javascript
// vite.config.js
import { defineConfig } from 'vite';
import { visualizer } from 'rollup-plugin-visualizer';

export default defineConfig({
  plugins: [
    visualizer({
      filename: './dist/stats.html',
      open: true,
      gzipSize: true,
      brotliSize: true
    })
  ]
});
```

**Запуск аналізу:**

```bash
# Створення production збірки з аналізом
npm run build

# Або додати спеціальний скрипт
# package.json
{
  "scripts": {
    "analyze": "npm run build && open dist/stats.html"
  }
}
```

### Техніки оптимізації bundle

**Code Splitting** розділяє код на менші частини, які завантажуються за потреби:

```javascript
// Динамічний імпорт компонентів
const UserProfile = lazy(() => import('./components/UserProfile'));

// Використання в додатку
function App() {
  return (
    <Suspense fallback={<Loading />}>
      <Routes>
        <Route path="/profile" element={<UserProfile />} />
      </Routes>
    </Suspense>
  );
}

// Route-based code splitting
const Dashboard = lazy(() => import('./pages/Dashboard'));
const Settings = lazy(() => import('./pages/Settings'));
const Analytics = lazy(() => import('./pages/Analytics'));
```

**Tree Shaking** видаляє невикористаний код:

```javascript
// Правильний імпорт для tree shaking
import { specific, functions } from 'large-library';

// Неправильний - імпортує всю бібліотеку
import * as library from 'large-library';

// Приклад з lodash
// Погано - 70KB
import _ from 'lodash';

// Добре - 5KB
import debounce from 'lodash/debounce';

// Або використовувати lodash-es для ES modules
import { debounce } from 'lodash-es';
```

**Оптимізація залежностей:**

```bash
# Аналіз розміру пакетів
npx bundle-phobia <package-name>

# Знаходження дублікатів
npx depcheck

# Альтернативи важким бібліотекам
# moment.js (67KB) → date-fns (21KB) → day.js (2KB)
npm uninstall moment
npm install dayjs

# lodash → lodash-es для кращого tree shaking
```

**Налаштування Vite для оптимізації:**

```javascript
// vite.config.js
export default defineConfig({
  build: {
    rollupOptions: {
      output: {
        manualChunks: {
          'react-vendor': ['react', 'react-dom', 'react-router-dom'],
          'ui-vendor': ['@radix-ui/react-dialog', '@radix-ui/react-dropdown'],
          'utils': ['date-fns', 'lodash-es']
        }
      }
    },
    chunkSizeWarningLimit: 1000,
    minify: 'terser',
    terserOptions: {
      compress: {
        drop_console: true,
        drop_debugger: true
      }
    }
  }
});
```

### Оптимізація завантаження

**Preload та Prefetch:**

```html
<!-- Preload критичних ресурсів -->
<link rel="preload" href="/fonts/main.woff2" as="font" type="font/woff2" crossorigin>
<link rel="preload" href="/critical.css" as="style">

<!-- Prefetch для майбутніх сторінок -->
<link rel="prefetch" href="/dashboard.js">
<link rel="prefetch" href="/profile.js">
```

**Lazy Loading зображень:**

```jsx
// React компонент для lazy loading
function LazyImage({ src, alt, ...props }) {
  const [imageSrc, setImageSrc] = useState('placeholder.jpg');
  const imgRef = useRef();

  useEffect(() => {
    const observer = new IntersectionObserver(([entry]) => {
      if (entry.isIntersecting) {
        setImageSrc(src);
        observer.disconnect();
      }
    });

    if (imgRef.current) {
      observer.observe(imgRef.current);
    }

    return () => observer.disconnect();
  }, [src]);

  return <img ref={imgRef} src={imageSrc} alt={alt} {...props} />;
}
```

**Compression налаштування:**

```javascript
// vite.config.js
import viteCompression from 'vite-plugin-compression';

export default defineConfig({
  plugins: [
    viteCompression({
      algorithm: 'gzip',
      ext: '.gz'
    }),
    viteCompression({
      algorithm: 'brotliCompress',
      ext: '.br'
    })
  ]
});
```

## Docker для веброзробників

### Концепція контейнеризації

**Docker** дозволяє упакувати застосунок з усіма залежностями в ізольоване середовище, яке можна запустити на будь-якій системі.

**Переваги Docker для веброзробки:**

Консистентність середовища забезпечує ідентичну поведінку на всіх машинах команди. Ізоляція дозволяє запускати різні версії залежностей для різних проєктів. Швидке розгортання спрощує перенесення застосунку між середовищами. Масштабованість полегшує горизонтальне масштабування застосунку.

```mermaid
graph TB
    subgraph "Development"
        A[Source Code]
        B[Dockerfile]
    end

    subgraph "Docker Image"
        C[Node.js Runtime]
        D[Dependencies]
        E[Application Code]
    end

    subgraph "Docker Container"
        F[Running Application]
        G[Isolated Environment]
    end

    A --> B
    B --> C
    B --> D
    B --> E
    C --> F
    D --> F
    E --> F
    F --> G
```

### Dockerfile для Node.js застосунку

**Базовий Dockerfile:**

```dockerfile
# Використовуємо офіційний Node.js образ
FROM node:20-alpine

# Встановлюємо робочу директорію
WORKDIR /app

# Копіюємо package.json та package-lock.json
COPY package*.json ./

# Встановлюємо залежності
RUN npm ci --only=production

# Копіюємо решту файлів
COPY . .

# Будуємо застосунок
RUN npm run build

# Відкриваємо порт
EXPOSE 3000

# Запускаємо застосунок
CMD ["npm", "start"]
```

**Multi-stage build для оптимізації:**

```dockerfile
# Stage 1: Build
FROM node:20-alpine AS builder

WORKDIR /app

COPY package*.json ./
RUN npm ci

COPY . .
RUN npm run build

# Stage 2: Production
FROM node:20-alpine

WORKDIR /app

# Копіюємо тільки необхідне з попереднього stage
COPY --from=builder /app/package*.json ./
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules

EXPOSE 3000

CMD ["node", "dist/server.js"]
```

**Development Dockerfile:**

```dockerfile
# Dockerfile.dev
FROM node:20-alpine

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .

EXPOSE 5173

CMD ["npm", "run", "dev"]
```

### Docker Compose для комплексних застосунків

**docker-compose.yml** дозволяє визначити та запустити багатоконтейнерні застосунки:

```yaml
version: '3.8'

services:
  # Frontend (React)
  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile.dev
    ports:
      - "5173:5173"
    volumes:
      - ./frontend:/app
      - /app/node_modules
    environment:
      - VITE_API_URL=http://localhost:3000
    depends_on:
      - backend

  # Backend (Node.js + Express)
  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile.dev
    ports:
      - "3000:3000"
    volumes:
      - ./backend:/app
      - /app/node_modules
    environment:
      - NODE_ENV=development
      - DATABASE_URL=postgresql://user:password@postgres:5432/mydb
    depends_on:
      - postgres
      - redis

  # PostgreSQL Database
  postgres:
    image: postgres:15-alpine
    ports:
      - "5432:5432"
    environment:
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=password
      - POSTGRES_DB=mydb
    volumes:
      - postgres_data:/var/lib/postgresql/data

  # Redis Cache
  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"

volumes:
  postgres_data:
```

**Використання Docker Compose:**

```bash
# Запуск всіх сервісів
docker-compose up

# Запуск у фоновому режимі
docker-compose up -d

# Зупинка сервісів
docker-compose down

# Перебудова образів
docker-compose build

# Перегляд логів
docker-compose logs -f

# Виконання команд в контейнері
docker-compose exec backend npm run migrate

# Очищення volumes
docker-compose down -v
```

### .dockerignore файл

Файл `.dockerignore` визначає файли, які не повинні копіюватись в Docker образ:

```
node_modules
npm-debug.log
dist
build
.git
.gitignore
.env
.env.local
.vscode
.idea
coverage
*.md
```

### Оптимізація Docker образів

**Використання .dockerignore** зменшує розмір контексту збірки. **Multi-stage builds** видаляють зайві залежності з production образу. **Layer caching** оптимізує час збірки через правильний порядок інструкцій. **Alpine images** використовують мінімальні базові образи.

```dockerfile
# Оптимізований production Dockerfile
FROM node:20-alpine AS builder

WORKDIR /app

# Спочатку копіюємо тільки package.json для кешування
COPY package*.json ./
RUN npm ci --only=production && npm cache clean --force

COPY . .
RUN npm run build && \
    rm -rf src tests docs

# Мінімальний production образ
FROM node:20-alpine

RUN apk add --no-cache tini

WORKDIR /app

COPY --from=builder /app/package*.json ./
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/dist ./dist

USER node

EXPOSE 3000

ENTRYPOINT ["/sbin/tini", "--"]
CMD ["node", "dist/server.js"]
```

## VS Code розширення для продуктивності

### Обов'язкові розширення для веброзробки

**ESLint** інтегрує ESLint в VS Code з підсвічуванням помилок в реальному часі та швидкими виправленнями.

**Prettier** форматує код автоматично при збереженні файлу.

**GitLens** розширює можливості Git в VS Code, показуючи авторів кожного рядка коду, історію змін та інші корисні функції.

**Error Lens** виводить помилки та попередження прямо в редакторі, біля рядків коду.

**Auto Rename Tag** автоматично перейменовує парні HTML/JSX теги.

**Bracket Pair Colorizer** (вбудовано в VS Code) підсвічує відповідні дужки різними кольорами.

```json
// .vscode/extensions.json
{
  "recommendations": [
    "dbaeumer.vscode-eslint",
    "esbenp.prettier-vscode",
    "eamodio.gitlens",
    "usernamehw.errorlens",
    "formulahendry.auto-rename-tag",
    "editorconfig.editorconfig"
  ]
}
```

### Розширення для React розробки

**ES7+ React/Redux/React-Native snippets** надає snippets для швидкого написання React компонентів.

**React Developer Tools** (браузерне розширення) дозволяє інспектувати React компоненти та їх стан.

```javascript
// Приклади snippets:
// rafce → React Arrow Function Component with Export
import React from 'react';

const ComponentName = () => {
  return <div></div>;
};

export default ComponentName;

// useState → useState Hook
const [state, setState] = useState(initialState);

// useEffect → useEffect Hook
useEffect(() => {

}, []);
```

### Налаштування VS Code

**settings.json** містить персональні налаштування редактора:

```json
{
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true,
    "source.organizeImports": true
  },
  "editor.tabSize": 2,
  "editor.fontSize": 14,
  "editor.lineHeight": 22,
  "editor.fontFamily": "'Fira Code', 'Courier New', monospace",
  "editor.fontLigatures": true,
  "editor.bracketPairColorization.enabled": true,
  "editor.guides.bracketPairs": true,
  "editor.inlineSuggest.enabled": true,
  "editor.suggestSelection": "first",
  "editor.minimap.enabled": false,
  "editor.renderWhitespace": "boundary",
  "files.autoSave": "onFocusChange",
  "files.trimTrailingWhitespace": true,
  "files.insertFinalNewline": true,
  "terminal.integrated.fontSize": 14,
  "workbench.colorTheme": "One Dark Pro",
  "workbench.iconTheme": "material-icon-theme",
  "javascript.updateImportsOnFileMove.enabled": "always",
  "typescript.updateImportsOnFileMove.enabled": "always",
  "emmet.includeLanguages": {
    "javascript": "javascriptreact"
  }
}
```

### Snippets для швидкості розробки

**Користувацькі snippets** створюють шаблони коду:

```json
// .vscode/snippets.code-snippets
{
  "React Functional Component": {
    "prefix": "rfc",
    "body": [
      "interface ${1:ComponentName}Props {",
      "  $2",
      "}",
      "",
      "export const ${1:ComponentName} = ({ $3 }: ${1:ComponentName}Props) => {",
      "  return (",
      "    <div>",
      "      $0",
      "    </div>",
      "  );",
      "};"
    ],
    "description": "React Functional Component with TypeScript"
  },
  "React Hook Form": {
    "prefix": "rhf",
    "body": [
      "const {",
      "  register,",
      "  handleSubmit,",
      "  formState: { errors }",
      "} = useForm<$1>();",
      "",
      "const onSubmit = (data: $1) => {",
      "  $0",
      "};"
    ]
  }
}
```

### Keybindings для продуктивності

**keybindings.json** налаштовує клавіатурні комбінації:

```json
[
  {
    "key": "ctrl+shift+d",
    "command": "editor.action.duplicateSelection"
  },
  {
    "key": "ctrl+shift+k",
    "command": "editor.action.deleteLines"
  },
  {
    "key": "ctrl+shift+alt+down",
    "command": "editor.action.copyLinesDownAction"
  }
]
```

## Висновки та найкращі практики

### Інтеграція інструментів в workflow

Ефективний workflow розробки базується на правильній інтеграції інструментів у єдину систему. Кожен інструмент має своє призначення та працює разом з іншими для забезпечення якості коду та продуктивності команди.

**Послідовність впровадження інструментів:**

Першим кроком є налаштування системи контролю версій Git з правильним workflow. Далі додаємо автоматичне форматування та лінтинг для забезпечення стилю коду. Git hooks автоматизують перевірки перед комітом. Аналіз bundle виявляє проблеми з розміром. Docker забезпечує консистентність середовища. VS Code розширення підвищують персональну продуктивність.

### Автоматизація як філософія

Сучасна розробка передбачає максимальну автоматизацію рутинних завдань. Інструменти повинні працювати в фоні, не відволікаючи розробника від написання коду. Автоматизація забезпечує консистентність, зменшує кількість помилок та прискорює процес розробки.

**Ключові принципи автоматизації:**

Перевірки повинні запускатися автоматично при комітах та push. Форматування відбувається при збереженні файлу без ручного втручання. Тести запускаються локально перед відправкою коду. Bundle аналіз включений в CI/CD pipeline. Деплой автоматизований через Docker та оркестрацію.

### Баланс між якістю та продуктивністю

Важливо знайти баланс між строгістю інструментів та швидкістю розробки. Занадто багато перевірок можуть уповільнити workflow, але їх відсутність призводить до проблем з якістю коду.

**Рекомендації для балансу:**

Pre-commit hooks повинні виконуватись швидко, до 10 секунд. Складні перевірки переносимо в pre-push hooks або CI. Використовуємо lint-staged для перевірки тільки змінених файлів. Налаштовуємо кешування для прискорення повторних перевірок. Дозволяємо обхід hooks для WIP комітів, але з обов'язковим виправленням перед merge.

### Командні стандарти

Єдині стандарти критично важливі для командної розробки. Всі члени команди повинні використовувати однакові налаштування інструментів.

**Обов'язкові файли в репозиторії:**

Файли `.eslintrc.json`, `.prettierrc.json`, `.editorconfig` визначають стиль коду. Папка `.vscode/` містить рекомендовані розширення та налаштування. Папка `.husky/` з налаштованими hooks. Файл `docker-compose.yml` для локальної розробки. Документація `CONTRIBUTING.md` з інструкціями для нових розробників.

### Continuous Integration

Інструменти розробки інтегруються в CI/CD pipeline для автоматизації перевірок на сервері:

```yaml
# .github/workflows/ci.yml
name: CI

on:
  pull_request:
  push:
    branches: [main, develop]

jobs:
  quality:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '20'
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Lint
        run: npm run lint

      - name: Format check
        run: npm run format:check

      - name: Type check
        run: npm run type-check

      - name: Test
        run: npm run test:coverage

      - name: Build
        run: npm run build

      - name: Bundle analysis
        run: npm run analyze
```

### Еволюція інструментів

Екосистема веброзробки постійно еволюціонує. Нові інструменти з'являються, старі оновлюються. Важливо слідкувати за трендами, але не впроваджувати кожну новинку без критичного аналізу.

**Критерії оцінки нових інструментів:**

Інструмент має вирішувати реальну проблему команди. Має активну підтримку та спільноту. Легко інтегрується з існуючими інструментами. Не вимагає значних змін в workflow. Має хорошу документацію та приклади використання.

Сучасна веброзробка неможлива без правильно налаштованих інструментів. Інвестиція часу в початкове налаштування окупається багаторазово через підвищення продуктивності, покращення якості коду та зменшення кількості помилок. Ключ до успіху - це поступове впровадження інструментів з постійною оптимізацією workflow під потреби конкретної команди та проєкту.
