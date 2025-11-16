# Лекція 22. DevOps для веброзробників

## Вступ до DevOps культури

### Філософія DevOps та її значення для веброзробки

**DevOps** представляє собою не просто набір інструментів чи технологій, а фундаментальну зміну культури розробки програмного забезпечення. Це методологія, яка об'єднує розробку (Development) та операційну діяльність (Operations) в єдиний безперервний процес з метою прискорення доставки якісного програмного забезпечення кінцевим користувачам.

Історично розробники та системні адміністратори працювали як окремі команди з різними цілями та метриками успіху. Розробники прагнули швидко випускати нові функції, тоді як операційники зосереджувалися на стабільності та надійності систем. Ця розрізненість створювала конфлікти інтересів та уповільнювала процес доставки програмного забезпечення. DevOps виник як відповідь на цю проблему, пропонуючи культуру співпраці, автоматизації та безперервного вдосконалення.

Для веброзробників DevOps має особливе значення через специфіку вебдодатків. Вебзастосунки повинні бути постійно доступними, швидко оновлюватися та масштабуватися відповідно до навантаження. Традиційний підхід з ручним деплоєм та конфігурацією не відповідає сучасним вимогам до швидкості доставки та надійності.

### Основні принципи DevOps

**Безперервна інтеграція та доставка** утворюють фундамент DevOps практик. Ідея полягає в тому, що код постійно інтегрується, тестується та готується до випуску в продакшн. Кожна зміна коду проходить через автоматизований pipeline, що включає збірку, тестування та валідацію перед тим, як потрапити до користувачів.

**Інфраструктура як код** змінює підхід до управління серверами та середовищами. Замість ручного налаштування серверів через GUI або командний рядок, вся конфігурація описується в коді, який зберігається в системі контролю версій. Це забезпечує відтворюваність середовищ, полегшує масштабування та дозволяє застосовувати до інфраструктури ті ж практики, що й до коду додатку.

**Моніторинг та спостережуваність** дозволяють команді розуміти, що відбувається в системі в реальному часі. Замість реактивного підходу, коли проблеми виявляються після того, як користувачі повідомляють про них, DevOps пропонує проактивний моніторинг з автоматичними алертами та детальною телеметрією.

**Культура відповідальності та автономності** означає, що команди мають повний контроль над життєвим циклом своїх додатків від розробки до експлуатації. Це створює відчуття відповідальності за якість та надійність продукту, оскільки ті ж люди, які пишуть код, також відповідають за його роботу в продакшн.

### Переваги DevOps для вебпроєктів

Впровадження DevOps практик приносить конкретні вимірювані переваги для вебпроєктів. Швидкість доставки нових функцій збільшується в рази завдяки автоматизації процесів збірки, тестування та деплою. Те, що раніше займало години або дні ручної роботи, тепер відбувається автоматично за лічені хвилини.

Надійність додатків значно покращується завдяки автоматизованому тестуванню та можливості швидко відкотити зміни у разі проблем. Кожна зміна коду проходить через комплексний набір тестів, що знижує ймовірність помилок у продакшн. Якщо ж проблема все ж виникає, процес повернення до попередньої версії є простим та швидким.

Масштабованість стає природною властивістю системи, коли інфраструктура описана як код і може бути швидко розгорнута або змінена. Додавання нових серверів, балансувальників навантаження чи баз даних перетворюється на виконання скриптів замість годин ручної конфігурації.

Економія ресурсів досягається через автоматизацію рутинних операцій та оптимальне використання інфраструктури. Команди можуть зосередитися на створенні цінності для користувачів замість витрат часу на ручні операційні задачі.

## CI/CD Pipelines: автоматизація життєвого циклу

### Концептуальні основи CI/CD

**Continuous Integration** передбачає, що розробники регулярно, часто декілька разів на день, інтегрують свій код до спільного репозиторію. Кожна інтеграція автоматично верифікується через збірку проєкту та запуск автоматизованих тестів. Це дозволяє швидко виявляти проблеми інтеграції та конфлікти коду.

**Continuous Delivery** розширює CI, забезпечуючи, що код завжди знаходиться в стані, готовому до випуску. Кожна успішна збірка потенційно може бути розгорнута в продакшн, хоча фактичне розгортання може вимагати ручного підтвердження.

**Continuous Deployment** йде ще далі, автоматично розгортаючи кожну зміну, яка проходить всі етапи pipeline, безпосередньо в продакшн без ручного втручання. Це найвищий рівень автоматизації, який вимагає високої зрілості процесів тестування та моніторингу.

### GitHub Actions: практична реалізація CI/CD

GitHub Actions надає потужну платформу для автоматизації робочих процесів безпосередньо в репозиторії коду. Workflows описуються в YAML файлах та можуть реагувати на різні події в репозиторії.

```mermaid
graph TB
    A[Push to Repository] --> B[Trigger Workflow]
    B --> C[Checkout Code]
    C --> D[Install Dependencies]
    D --> E[Run Tests]
    E --> F{Tests Pass?}
    F -->|Yes| G[Build Application]
    F -->|No| H[Notify Failure]
    G --> I[Run Security Scan]
    I --> J{Security OK?}
    J -->|Yes| K[Deploy to Staging]
    J -->|No| H
    K --> L[Run E2E Tests]
    L --> M{E2E Pass?}
    M -->|Yes| N[Deploy to Production]
    M -->|No| H
    N --> O[Notify Success]
```

#### Базовий CI Workflow для вебзастосунку

```yaml
name: CI Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest

    strategy:
      matrix:
        node-version: [18.x, 20.x]

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Run linter
        run: npm run lint

      - name: Run type checking
        run: npm run type-check

      - name: Run unit tests
        run: npm run test:unit -- --coverage

      - name: Upload coverage reports
        uses: codecov/codecov-action@v3
        with:
          files: ./coverage/lcov.info
          flags: unittests
          name: codecov-umbrella

      - name: Build application
        run: npm run build

      - name: Cache build artifacts
        uses: actions/cache@v3
        with:
          path: |
            dist
            .next
          key: ${{ runner.os }}-build-${{ github.sha }}
```

#### Складний CD Workflow з декількома середовищами

```yaml
name: CD Pipeline

on:
  workflow_run:
    workflows: ["CI Pipeline"]
    types: [completed]
    branches: [main]

env:
  REGISTRY: ghcr.io
  IMAGE_NAME: ${{ github.repository }}

jobs:
  build-and-push:
    runs-on: ubuntu-latest
    if: ${{ github.event.workflow_run.conclusion == 'success' }}

    permissions:
      contents: read
      packages: write

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v3

      - name: Log in to Container Registry
        uses: docker/login-action@v3
        with:
          registry: ${{ env.REGISTRY }}
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - name: Extract metadata
        id: meta
        uses: docker/metadata-action@v5
        with:
          images: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}
          tags: |
            type=ref,event=branch
            type=sha,prefix={{branch}}-
            type=semver,pattern={{version}}

      - name: Build and push Docker image
        uses: docker/build-push-action@v5
        with:
          context: .
          push: true
          tags: ${{ steps.meta.outputs.tags }}
          labels: ${{ steps.meta.outputs.labels }}
          cache-from: type=gha
          cache-to: type=gha,mode=max

  deploy-staging:
    needs: build-and-push
    runs-on: ubuntu-latest
    environment:
      name: staging
      url: https://staging.example.com

    steps:
      - name: Deploy to staging
        uses: appleboy/ssh-action@v1.0.0
        with:
          host: ${{ secrets.STAGING_HOST }}
          username: ${{ secrets.STAGING_USER }}
          key: ${{ secrets.STAGING_SSH_KEY }}
          script: |
            cd /opt/app
            docker compose pull
            docker compose up -d
            docker compose exec -T app npm run migrate

      - name: Run smoke tests
        run: |
          sleep 30
          curl -f https://staging.example.com/health || exit 1

      - name: Notify deployment
        uses: 8398a7/action-slack@v3
        with:
          status: ${{ job.status }}
          text: 'Staging deployment completed'
          webhook_url: ${{ secrets.SLACK_WEBHOOK }}

  deploy-production:
    needs: deploy-staging
    runs-on: ubuntu-latest
    environment:
      name: production
      url: https://example.com

    steps:
      - name: Create deployment
        uses: chrnorm/deployment-action@v2
        id: deployment
        with:
          token: ${{ github.token }}
          environment: production

      - name: Deploy to production
        uses: appleboy/ssh-action@v1.0.0
        with:
          host: ${{ secrets.PROD_HOST }}
          username: ${{ secrets.PROD_USER }}
          key: ${{ secrets.PROD_SSH_KEY }}
          script: |
            cd /opt/app
            docker compose pull
            docker compose up -d --no-deps app
            docker compose exec -T app npm run migrate

      - name: Update deployment status
        if: always()
        uses: chrnorm/deployment-status@v2
        with:
          token: ${{ github.token }}
          deployment-id: ${{ steps.deployment.outputs.deployment_id }}
          state: ${{ job.status }}

      - name: Notify production deployment
        uses: 8398a7/action-slack@v3
        if: always()
        with:
          status: ${{ job.status }}
          text: 'Production deployment ${{ job.status }}'
          webhook_url: ${{ secrets.SLACK_WEBHOOK }}
```

### Автоматизоване тестування в pipeline

Автоматизоване тестування є критичною частиною CI/CD процесу. Різні типи тестів виконуються на різних етапах pipeline, створюючи багаторівневу систему перевірки якості коду.

#### Структура тестування

```yaml
name: Comprehensive Testing

on:
  push:
    branches: [main, develop]

jobs:
  unit-tests:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20.x'
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Run unit tests
        run: npm run test:unit -- --coverage --maxWorkers=2

      - name: Upload results
        uses: actions/upload-artifact@v3
        with:
          name: unit-test-results
          path: coverage/

  integration-tests:
    runs-on: ubuntu-latest
    needs: unit-tests

    services:
      postgres:
        image: postgres:15
        env:
          POSTGRES_PASSWORD: testpassword
          POSTGRES_DB: testdb
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
        ports:
          - 5432:5432

      redis:
        image: redis:7
        options: >-
          --health-cmd "redis-cli ping"
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
        ports:
          - 6379:6379

    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20.x'
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Run database migrations
        env:
          DATABASE_URL: postgresql://postgres:testpassword@localhost:5432/testdb
        run: npm run migrate

      - name: Run integration tests
        env:
          DATABASE_URL: postgresql://postgres:testpassword@localhost:5432/testdb
          REDIS_URL: redis://localhost:6379
        run: npm run test:integration

  e2e-tests:
    runs-on: ubuntu-latest
    needs: integration-tests

    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20.x'
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Install Playwright
        run: npx playwright install --with-deps

      - name: Build application
        run: npm run build

      - name: Run E2E tests
        run: npm run test:e2e

      - name: Upload Playwright report
        if: always()
        uses: actions/upload-artifact@v3
        with:
          name: playwright-report
          path: playwright-report/
          retention-days: 30
```

## Environment Management: управління середовищами

### Концепція множинних середовищ

Сучасна веброзробка передбачає наявність декількох ізольованих середовищ, кожне з яких має свою специфічну мету в життєвому циклі розробки та доставки програмного забезпечення.

**Development середовище** використовується розробниками для локальної розробки та тестування нових функцій. Це середовище найбільш нестабільне та може містити незавершений код. Воно часто запускається локально на комп'ютерах розробників, але може також існувати як спільне середовище для команди.

**Staging середовище** є максимально наближеною копією production середовища. Воно використовується для фінального тестування перед випуском в продакшн, включаючи інтеграційне тестування, перевірку продуктивності та user acceptance testing. Дані в staging можуть бути анонімізованою копією production даних або згенерованими тестовими даними.

**Production середовище** це те, що бачать кінцеві користувачі. Воно має найвищі вимоги до надійності, безпеки та продуктивності. Будь-які зміни в production мають бути ретельно протестовані та контрольовані.

### Управління конфігурацією середовищ

Різні середовища потребують різної конфігурації, яка має керуватися безпечно та зручно. Використання змінних середовища є стандартною практикою для досягнення цієї мети.

#### Структура змінних середовища

```bash
# .env.development
NODE_ENV=development
PORT=3000
DATABASE_URL=postgresql://localhost:5432/myapp_dev
REDIS_URL=redis://localhost:6379
API_BASE_URL=http://localhost:3000
LOG_LEVEL=debug
ENABLE_DEBUG=true

# .env.staging
NODE_ENV=staging
PORT=3000
DATABASE_URL=postgresql://staging-db.example.com:5432/myapp_staging
REDIS_URL=redis://staging-redis.example.com:6379
API_BASE_URL=https://staging.example.com
LOG_LEVEL=info
ENABLE_DEBUG=false

# .env.production
NODE_ENV=production
PORT=3000
DATABASE_URL=postgresql://prod-db.example.com:5432/myapp_prod
REDIS_URL=redis://prod-redis.example.com:6379
API_BASE_URL=https://api.example.com
LOG_LEVEL=warn
ENABLE_DEBUG=false
```

#### Управління секретами через GitHub

GitHub Secrets надає безпечний спосіб зберігання та використання конфіденційної інформації в workflows.

```yaml
name: Deploy with Secrets

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Create .env file
        run: |
          cat << EOF > .env.production
          DATABASE_URL=${{ secrets.DATABASE_URL }}
          JWT_SECRET=${{ secrets.JWT_SECRET }}
          API_KEY=${{ secrets.API_KEY }}
          STRIPE_SECRET_KEY=${{ secrets.STRIPE_SECRET_KEY }}
          AWS_ACCESS_KEY_ID=${{ secrets.AWS_ACCESS_KEY_ID }}
          AWS_SECRET_ACCESS_KEY=${{ secrets.AWS_SECRET_ACCESS_KEY }}
          SENTRY_DSN=${{ secrets.SENTRY_DSN }}
          EOF

      - name: Deploy application
        run: |
          # Деплой з використанням створеного .env файлу
          ./deploy.sh
```

### Docker та контейнеризація

Контейнеризація забезпечує консистентність між різними середовищами, вирішуючи класичну проблему "works on my machine". Docker дозволяє пакувати застосунок разом з усіма його залежностями в стандартизований контейнер.

#### Багатоетапна збірка Docker

```dockerfile
# Етап 1: Збірка застосунку
FROM node:20-alpine AS builder

WORKDIR /app

# Копіювання файлів залежностей
COPY package*.json ./
COPY prisma ./prisma/

# Встановлення залежностей
RUN npm ci --only=production && \
    npm cache clean --force

# Генерація Prisma клієнта
RUN npx prisma generate

# Копіювання вихідного коду
COPY . .

# Збірка застосунку
RUN npm run build

# Етап 2: Production образ
FROM node:20-alpine AS production

WORKDIR /app

# Встановлення dumb-init для правильної обробки сигналів
RUN apk add --no-cache dumb-init

# Створення непривілейованого користувача
RUN addgroup -g 1001 -S nodejs && \
    adduser -S nodejs -u 1001

# Копіювання тільки необхідних файлів з builder
COPY --from=builder --chown=nodejs:nodejs /app/node_modules ./node_modules
COPY --from=builder --chown=nodejs:nodejs /app/dist ./dist
COPY --from=builder --chown=nodejs:nodejs /app/prisma ./prisma
COPY --from=builder --chown=nodejs:nodejs /app/package*.json ./

# Перемикання на непривілейованого користувача
USER nodejs

# Відкриття порту
EXPOSE 3000

# Healthcheck
HEALTHCHECK --interval=30s --timeout=3s --start-period=40s \
  CMD node -e "require('http').get('http://localhost:3000/health', (r) => {process.exit(r.statusCode === 200 ? 0 : 1)})"

# Запуск застосунку
ENTRYPOINT ["dumb-init", "--"]
CMD ["node", "dist/main.js"]
```

#### Docker Compose для локальної розробки

```yaml
version: '3.8'

services:
  app:
    build:
      context: .
      target: development
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=development
      - DATABASE_URL=postgresql://postgres:password@postgres:5432/myapp
      - REDIS_URL=redis://redis:6379
    volumes:
      - .:/app
      - /app/node_modules
    depends_on:
      postgres:
        condition: service_healthy
      redis:
        condition: service_healthy
    command: npm run dev

  postgres:
    image: postgres:15-alpine
    ports:
      - "5432:5432"
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: password
      POSTGRES_DB: myapp
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./init-db.sql:/docker-entrypoint-initdb.d/init.sql
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s
      timeout: 5s
      retries: 5

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 10s
      timeout: 3s
      retries: 5

  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf:ro
    depends_on:
      - app

volumes:
  postgres_data:
  redis_data:
```

## Database Migrations у Production

### Стратегії міграцій баз даних

Міграції баз даних у production середовищі потребують особливої уваги, оскільки помилки можуть призвести до втрати даних або простою системи. Існують різні підходи до управління міграціями, кожен з яких має свої переваги та обмеження.

**Forward-only migrations** передбачає, що міграції виконуються тільки вперед без можливості відкату. Це найпростіший підхід, але він вимагає ретельного планування та тестування перед виконанням.

**Reversible migrations** дозволяє відкочувати зміни в разі проблем. Кожна міграція містить як up метод для застосування змін, так і down метод для їх скасування.

**Blue-green deployments** для баз даних передбачає підтримку двох версій схеми одночасно під час перехідного періоду, що дозволяє безперешкодно переключатися між версіями застосунку.

### Практична реалізація міграцій з Prisma

```typescript
// prisma/schema.prisma
datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

generator client {
  provider = "prisma-client-js"
}

model User {
  id        String   @id @default(cuid())
  email     String   @unique
  name      String?
  password  String
  role      Role     @default(USER)
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
  posts     Post[]

  @@index([email])
  @@map("users")
}

model Post {
  id          String   @id @default(cuid())
  title       String
  content     String?
  published   Boolean  @default(false)
  authorId    String
  author      User     @relation(fields: [authorId], references: [id], onDelete: Cascade)
  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt

  @@index([authorId])
  @@index([published, createdAt])
  @@map("posts")
}

enum Role {
  USER
  ADMIN
}
```

#### Автоматизація міграцій в CI/CD

```yaml
name: Database Migration

on:
  push:
    branches: [main]
    paths:
      - 'prisma/schema.prisma'
      - 'prisma/migrations/**'

jobs:
  migrate:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: '20.x'
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Create migration backup
        env:
          DATABASE_URL: ${{ secrets.DATABASE_URL }}
        run: |
          TIMESTAMP=$(date +%Y%m%d_%H%M%S)
          echo "Creating backup before migration: backup_${TIMESTAMP}"
          # Тут код для створення бекапу

      - name: Run migrations
        env:
          DATABASE_URL: ${{ secrets.DATABASE_URL }}
        run: |
          npx prisma migrate deploy

      - name: Verify migration
        env:
          DATABASE_URL: ${{ secrets.DATABASE_URL }}
        run: |
          npx prisma migrate status

      - name: Notify on failure
        if: failure()
        uses: 8398a7/action-slack@v3
        with:
          status: failure
          text: 'Database migration failed!'
          webhook_url: ${{ secrets.SLACK_WEBHOOK }}
```

### Безпечні практики міграцій

Виконання міграцій у production вимагає дотримання певних правил для мінімізації ризиків.

```typescript
// scripts/safe-migration.ts
import { PrismaClient } from '@prisma/client';
import { execSync } from 'child_process';
import * as fs from 'fs';

interface MigrationConfig {
  backupBeforeMigration: boolean;
  testMigrationFirst: boolean;
  notifyOnCompletion: boolean;
  rollbackOnFailure: boolean;
}

class SafeMigrationRunner {
  private prisma: PrismaClient;
  private config: MigrationConfig;
  private backupPath: string;

  constructor(config: MigrationConfig) {
    this.prisma = new PrismaClient();
    this.config = config;
    this.backupPath = `backups/backup_${Date.now()}.sql`;
  }

  async run(): Promise<void> {
    console.log('Starting safe migration process...');

    try {
      // Крок 1: Створення бекапу
      if (this.config.backupBeforeMigration) {
        await this.createBackup();
      }

      // Крок 2: Тестування міграції на копії
      if (this.config.testMigrationFirst) {
        await this.testMigration();
      }

      // Крок 3: Виконання міграції
      console.log('Applying migration to production database...');
      execSync('npx prisma migrate deploy', { stdio: 'inherit' });

      // Крок 4: Верифікація міграції
      await this.verifyMigration();

      // Крок 5: Сповіщення про успіх
      if (this.config.notifyOnCompletion) {
        await this.notifySuccess();
      }

      console.log('Migration completed successfully!');
    } catch (error) {
      console.error('Migration failed:', error);

      // Відкат у разі помилки
      if (this.config.rollbackOnFailure && this.config.backupBeforeMigration) {
        await this.rollback();
      }

      throw error;
    } finally {
      await this.prisma.$disconnect();
    }
  }

  private async createBackup(): Promise<void> {
    console.log(`Creating backup at ${this.backupPath}...`);

    const databaseUrl = process.env.DATABASE_URL;
    if (!databaseUrl) {
      throw new Error('DATABASE_URL not configured');
    }

    // Створення директорії для бекапів
    if (!fs.existsSync('backups')) {
      fs.mkdirSync('backups', { recursive: true });
    }

    // Виконання pg_dump для PostgreSQL
    execSync(
      `pg_dump ${databaseUrl} > ${this.backupPath}`,
      { stdio: 'inherit' }
    );

    console.log('Backup created successfully');
  }

  private async testMigration(): Promise<void> {
    console.log('Testing migration on a copy of the database...');

    // Створення тимчасової бази даних
    const testDbUrl = process.env.TEST_DATABASE_URL;
    if (!testDbUrl) {
      throw new Error('TEST_DATABASE_URL not configured');
    }

    // Відновлення бекапу в тестову БД
    execSync(
      `psql ${testDbUrl} < ${this.backupPath}`,
      { stdio: 'inherit' }
    );

    // Виконання міграції на тестовій БД
    const originalUrl = process.env.DATABASE_URL;
    process.env.DATABASE_URL = testDbUrl;

    try {
      execSync('npx prisma migrate deploy', { stdio: 'inherit' });
      console.log('Test migration successful');
    } finally {
      process.env.DATABASE_URL = originalUrl;
    }
  }

  private async verifyMigration(): Promise<void> {
    console.log('Verifying migration...');

    // Перевірка з'єднання
    await this.prisma.$queryRaw`SELECT 1`;

    // Перевірка міграцій
    const migrations = await this.prisma.$queryRaw`
      SELECT migration_name, finished_at
      FROM _prisma_migrations
      ORDER BY finished_at DESC
      LIMIT 5
    `;

    console.log('Recent migrations:', migrations);
  }

  private async rollback(): Promise<void> {
    console.log('Rolling back migration...');

    const databaseUrl = process.env.DATABASE_URL;
    if (!databaseUrl) {
      throw new Error('DATABASE_URL not configured');
    }

    // Відновлення з бекапу
    execSync(
      `psql ${databaseUrl} < ${this.backupPath}`,
      { stdio: 'inherit' }
    );

    console.log('Rollback completed');
  }

  private async notifySuccess(): Promise<void> {
    // Відправка сповіщення через Slack, email тощо
    console.log('Sending success notification...');
  }
}

// Запуск міграції
const runner = new SafeMigrationRunner({
  backupBeforeMigration: true,
  testMigrationFirst: true,
  notifyOnCompletion: true,
  rollbackOnFailure: true
});

runner.run().catch(console.error);
```

## Backup та Recovery стратегії

### Філософія резервного копіювання

Резервне копіювання є критично важливою частиною операційної діяльності будь-якого продакшн застосунку. Стратегія бекапів має враховувати два ключові показники: Recovery Point Objective (RPO) визначає максимально допустимий обсяг втрати даних, а Recovery Time Objective (RTO) визначає максимально допустимий час простою.

### Автоматизовані бекапи PostgreSQL

```bash
#!/bin/bash
# scripts/backup-database.sh

set -e

# Конфігурація
BACKUP_DIR="/backups/postgresql"
RETENTION_DAYS=30
DATABASE_NAME="myapp_production"
TIMESTAMP=$(date +%Y%m%d_%H%M%S)
BACKUP_FILE="${BACKUP_DIR}/${DATABASE_NAME}_${TIMESTAMP}.sql.gz"
S3_BUCKET="s3://myapp-backups/database"

# Створення директорії для бекапів
mkdir -p "$BACKUP_DIR"

# Створення бекапу
echo "Creating backup: ${BACKUP_FILE}"
pg_dump "$DATABASE_NAME" | gzip > "$BACKUP_FILE"

# Перевірка успішності створення бекапу
if [ $? -eq 0 ]; then
    echo "Backup created successfully"

    # Завантаження в S3
    echo "Uploading to S3..."
    aws s3 cp "$BACKUP_FILE" "$S3_BUCKET/" --storage-class STANDARD_IA

    # Видалення старих локальних бекапів
    echo "Cleaning up old backups..."
    find "$BACKUP_DIR" -name "*.sql.gz" -mtime +${RETENTION_DAYS} -delete

    # Верифікація бекапу
    echo "Verifying backup..."
    gunzip -t "$BACKUP_FILE"

    echo "Backup process completed successfully"
else
    echo "Backup failed!" >&2
    exit 1
fi
```

#### Автоматизація через GitHub Actions

```yaml
name: Automated Database Backup

on:
  schedule:
    # Щоденно о 2:00 UTC
    - cron: '0 2 * * *'
  workflow_dispatch:

jobs:
  backup:
    runs-on: ubuntu-latest

    steps:
      - name: Install PostgreSQL client
        run: |
          sudo apt-get update
          sudo apt-get install -y postgresql-client

      - name: Create backup
        env:
          DATABASE_URL: ${{ secrets.DATABASE_URL }}
          AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
          AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        run: |
          TIMESTAMP=$(date +%Y%m%d_%H%M%S)
          BACKUP_FILE="backup_${TIMESTAMP}.sql.gz"

          pg_dump ${DATABASE_URL} | gzip > ${BACKUP_FILE}

          # Завантаження в S3
          aws s3 cp ${BACKUP_FILE} s3://myapp-backups/${BACKUP_FILE}

      - name: Verify backup
        run: |
          gunzip -t backup_*.sql.gz

      - name: Notify on failure
        if: failure()
        uses: 8398a7/action-slack@v3
        with:
          status: failure
          text: 'Database backup failed!'
          webhook_url: ${{ secrets.SLACK_WEBHOOK }}
```

### Процес відновлення з бекапу

```typescript
// scripts/restore-database.ts
import { execSync } from 'child_process';
import * as readline from 'readline';

interface RestoreOptions {
  backupFile: string;
  targetDatabase: string;
  createNewDatabase?: boolean;
  verifyAfterRestore?: boolean;
}

class DatabaseRestorer {
  async restore(options: RestoreOptions): Promise<void> {
    console.log('Database Restoration Process');
    console.log('============================');

    // Підтвердження від користувача
    const confirmed = await this.confirmRestore(options);
    if (!confirmed) {
      console.log('Restoration cancelled by user');
      return;
    }

    try {
      // Створення нової бази даних якщо потрібно
      if (options.createNewDatabase) {
        await this.createDatabase(options.targetDatabase);
      }

      // Відновлення з бекапу
      await this.performRestore(options);

      // Верифікація
      if (options.verifyAfterRestore) {
        await this.verifyRestore(options.targetDatabase);
      }

      console.log('Restoration completed successfully');
    } catch (error) {
      console.error('Restoration failed:', error);
      throw error;
    }
  }

  private async confirmRestore(options: RestoreOptions): Promise<boolean> {
    const rl = readline.createInterface({
      input: process.stdin,
      output: process.stdout
    });

    return new Promise((resolve) => {
      rl.question(
        `WARNING: This will restore ${options.targetDatabase} from ${options.backupFile}.\n` +
        `All existing data will be replaced. Continue? (yes/no): `,
        (answer) => {
          rl.close();
          resolve(answer.toLowerCase() === 'yes');
        }
      );
    });
  }

  private async createDatabase(dbName: string): Promise<void> {
    console.log(`Creating database: ${dbName}`);
    execSync(`createdb ${dbName}`, { stdio: 'inherit' });
  }

  private async performRestore(options: RestoreOptions): Promise<void> {
    console.log('Restoring database...');

    const isCompressed = options.backupFile.endsWith('.gz');
    const restoreCommand = isCompressed
      ? `gunzip -c ${options.backupFile} | psql ${options.targetDatabase}`
      : `psql ${options.targetDatabase} < ${options.backupFile}`;

    execSync(restoreCommand, { stdio: 'inherit' });
  }

  private async verifyRestore(dbName: string): Promise<void> {
    console.log('Verifying restored database...');

    // Перевірка кількості таблиць
    const tableCount = execSync(
      `psql ${dbName} -t -c "SELECT COUNT(*) FROM information_schema.tables WHERE table_schema = 'public'"`,
      { encoding: 'utf-8' }
    ).trim();

    console.log(`Tables found: ${tableCount}`);

    // Перевірка можливості підключення
    execSync(`psql ${dbName} -c "SELECT 1"`, { stdio: 'inherit' });
  }
}

// Використання
const restorer = new DatabaseRestorer();
restorer.restore({
  backupFile: process.argv[2] || 'backup_latest.sql.gz',
  targetDatabase: process.argv[3] || 'myapp_restored',
  createNewDatabase: true,
  verifyAfterRestore: true
}).catch(console.error);
```

## Monitoring та Alerting

### Системи моніторингу для вебзастосунків

Ефективний моніторинг дозволяє проактивно виявляти та вирішувати проблеми до того, як вони вплинуть на користувачів. Сучасні системи моніторингу надають детальну телеметрію про різні аспекти роботи застосунку.

```mermaid
graph TB
    subgraph "Application Layer"
        APP[Web Application]
        API[API Endpoints]
    end

    subgraph "Monitoring Systems"
        METRICS[Metrics Collection]
        LOGS[Log Aggregation]
        TRACES[Distributed Tracing]
    end

    subgraph "Storage & Analysis"
        PROM[Prometheus]
        LOKI[Loki]
        JAEGER[Jaeger]
    end

    subgraph "Alerting & Visualization"
        GRAFANA[Grafana Dashboards]
        ALERTS[Alert Manager]
        NOTIFY[Notifications]
    end

    APP --> METRICS
    API --> METRICS
    APP --> LOGS
    API --> LOGS
    APP --> TRACES
    API --> TRACES

    METRICS --> PROM
    LOGS --> LOKI
    TRACES --> JAEGER

    PROM --> GRAFANA
    LOKI --> GRAFANA
    JAEGER --> GRAFANA

    PROM --> ALERTS
    ALERTS --> NOTIFY
```

### Інструментація застосунку

```typescript
// src/monitoring/metrics.ts
import { Registry, Counter, Histogram, Gauge } from 'prom-client';

class MetricsCollector {
  private registry: Registry;

  // HTTP метрики
  public httpRequestDuration: Histogram;
  public httpRequestTotal: Counter;
  public httpRequestErrors: Counter;

  // Бізнес метрики
  public activeUsers: Gauge;
  public ordersProcessed: Counter;
  public paymentFailures: Counter;

  // Системні метрики
  public databaseConnections: Gauge;
  public cacheHitRate: Gauge;

  constructor() {
    this.registry = new Registry();

    // HTTP метрики
    this.httpRequestDuration = new Histogram({
      name: 'http_request_duration_seconds',
      help: 'Duration of HTTP requests in seconds',
      labelNames: ['method', 'route', 'status_code'],
      buckets: [0.001, 0.005, 0.015, 0.05, 0.1, 0.5, 1, 5],
      registers: [this.registry]
    });

    this.httpRequestTotal = new Counter({
      name: 'http_requests_total',
      help: 'Total number of HTTP requests',
      labelNames: ['method', 'route', 'status_code'],
      registers: [this.registry]
    });

    this.httpRequestErrors = new Counter({
      name: 'http_request_errors_total',
      help: 'Total number of HTTP request errors',
      labelNames: ['method', 'route', 'error_type'],
      registers: [this.registry]
    });

    // Бізнес метрики
    this.activeUsers = new Gauge({
      name: 'active_users',
      help: 'Number of currently active users',
      registers: [this.registry]
    });

    this.ordersProcessed = new Counter({
      name: 'orders_processed_total',
      help: 'Total number of processed orders',
      labelNames: ['status'],
      registers: [this.registry]
    });

    this.paymentFailures = new Counter({
      name: 'payment_failures_total',
      help: 'Total number of payment failures',
      labelNames: ['reason'],
      registers: [this.registry]
    });

    // Системні метрики
    this.databaseConnections = new Gauge({
      name: 'database_connections',
      help: 'Number of active database connections',
      registers: [this.registry]
    });

    this.cacheHitRate = new Gauge({
      name: 'cache_hit_rate',
      help: 'Cache hit rate percentage',
      registers: [this.registry]
    });
  }

  getRegistry(): Registry {
    return this.registry;
  }

  async getMetrics(): Promise<string> {
    return this.registry.metrics();
  }
}

export const metrics = new MetricsCollector();
```

#### Middleware для збору метрик

```typescript
// src/middleware/metrics.middleware.ts
import { Request, Response, NextFunction } from 'express';
import { metrics } from '../monitoring/metrics';

export function metricsMiddleware() {
  return (req: Request, res: Response, next: NextFunction) => {
    const start = Date.now();

    // Підрахунок загальної кількості запитів
    metrics.httpRequestTotal.inc({
      method: req.method,
      route: req.route?.path || req.path,
      status_code: res.statusCode
    });

    // Відстеження тривалості запиту
    res.on('finish', () => {
      const duration = (Date.now() - start) / 1000;

      metrics.httpRequestDuration.observe(
        {
          method: req.method,
          route: req.route?.path || req.path,
          status_code: res.statusCode
        },
        duration
      );

      // Підрахунок помилок
      if (res.statusCode >= 400) {
        metrics.httpRequestErrors.inc({
          method: req.method,
          route: req.route?.path || req.path,
          error_type: res.statusCode >= 500 ? 'server_error' : 'client_error'
        });
      }
    });

    next();
  };
}
```

### Налаштування алертів

```yaml
# prometheus/alerts.yml
groups:
  - name: application_alerts
    interval: 30s
    rules:
      # Високий рівень помилок HTTP
      - alert: HighHTTPErrorRate
        expr: |
          rate(http_request_errors_total[5m]) > 0.05
        for: 5m
        labels:
          severity: warning
        annotations:
          summary: "High HTTP error rate detected"
          description: "Error rate is {{ $value }} errors/sec (threshold: 0.05)"

      # Повільні запити
      - alert: SlowHTTPRequests
        expr: |
          histogram_quantile(0.95,
            rate(http_request_duration_seconds_bucket[5m])
          ) > 2
        for: 10m
        labels:
          severity: warning
        annotations:
          summary: "95th percentile latency is high"
          description: "95% of requests take more than {{ $value }}s"

      # Висока кількість активних з'єднань до БД
      - alert: HighDatabaseConnections
        expr: database_connections > 80
        for: 5m
        labels:
          severity: warning
        annotations:
          summary: "High number of database connections"
          description: "Current connections: {{ $value }} (threshold: 80)"

      # Низький hit rate кешу
      - alert: LowCacheHitRate
        expr: cache_hit_rate < 70
        for: 15m
        labels:
          severity: info
        annotations:
          summary: "Cache hit rate is low"
          description: "Hit rate: {{ $value }}% (threshold: 70%)"

      # Критичні помилки сервера
      - alert: CriticalServerErrors
        expr: |
          rate(http_request_errors_total{error_type="server_error"}[5m]) > 0.01
        for: 2m
        labels:
          severity: critical
        annotations:
          summary: "Critical server errors detected"
          description: "Server error rate: {{ $value }} errors/sec"

  - name: business_alerts
    interval: 1m
    rules:
      # Високий рівень невдалих платежів
      - alert: HighPaymentFailureRate
        expr: |
          rate(payment_failures_total[10m]) > 0.1
        for: 5m
        labels:
          severity: warning
        annotations:
          summary: "High payment failure rate"
          description: "Payment failure rate: {{ $value }} failures/sec"

      # Падіння активних користувачів
      - alert: ActiveUsersDropped
        expr: |
          (active_users - active_users offset 1h) / active_users offset 1h < -0.5
        for: 10m
        labels:
          severity: warning
        annotations:
          summary: "Significant drop in active users"
          description: "Active users dropped by {{ $value | humanizePercentage }}"
```

### Інтеграція з системами сповіщень

```typescript
// src/monitoring/alerting.ts
import axios from 'axios';

interface Alert {
  severity: 'info' | 'warning' | 'critical';
  title: string;
  description: string;
  metadata?: Record<string, any>;
}

class AlertingService {
  private slackWebhook: string;
  private pagerDutyKey: string;

  constructor() {
    this.slackWebhook = process.env.SLACK_WEBHOOK_URL || '';
    this.pagerDutyKey = process.env.PAGERDUTY_API_KEY || '';
  }

  async sendAlert(alert: Alert): Promise<void> {
    // Відправка в Slack
    if (this.slackWebhook) {
      await this.sendToSlack(alert);
    }

    // Відправка в PagerDuty для критичних алертів
    if (alert.severity === 'critical' && this.pagerDutyKey) {
      await this.sendToPagerDuty(alert);
    }

    // Логування
    console.log('[ALERT]', JSON.stringify(alert, null, 2));
  }

  private async sendToSlack(alert: Alert): Promise<void> {
    const color = {
      info: '#36a64f',
      warning: '#ff9800',
      critical: '#ff0000'
    }[alert.severity];

    const payload = {
      attachments: [
        {
          color,
          title: alert.title,
          text: alert.description,
          fields: alert.metadata
            ? Object.entries(alert.metadata).map(([key, value]) => ({
                title: key,
                value: String(value),
                short: true
              }))
            : undefined,
          footer: 'Monitoring System',
          ts: Math.floor(Date.now() / 1000)
        }
      ]
    };

    try {
      await axios.post(this.slackWebhook, payload);
    } catch (error) {
      console.error('Failed to send Slack alert:', error);
    }
  }

  private async sendToPagerDuty(alert: Alert): Promise<void> {
    const payload = {
      routing_key: this.pagerDutyKey,
      event_action: 'trigger',
      payload: {
        summary: alert.title,
        severity: alert.severity,
        source: 'monitoring-system',
        custom_details: alert.metadata
      }
    };

    try {
      await axios.post('https://events.pagerduty.com/v2/enqueue', payload);
    } catch (error) {
      console.error('Failed to send PagerDuty alert:', error);
    }
  }
}

export const alerting = new AlertingService();
```

### Health Check endpoints

```typescript
// src/routes/health.routes.ts
import { Router, Request, Response } from 'express';
import { PrismaClient } from '@prisma/client';
import Redis from 'ioredis';

const router = Router();
const prisma = new PrismaClient();
const redis = new Redis(process.env.REDIS_URL);

interface HealthStatus {
  status: 'healthy' | 'degraded' | 'unhealthy';
  timestamp: string;
  version: string;
  checks: {
    [key: string]: {
      status: 'up' | 'down';
      message?: string;
      responseTime?: number;
    };
  };
}

// Базовий health check
router.get('/health', async (req: Request, res: Response) => {
  const status: HealthStatus = {
    status: 'healthy',
    timestamp: new Date().toISOString(),
    version: process.env.APP_VERSION || 'unknown',
    checks: {}
  };

  try {
    // Перевірка бази даних
    const dbStart = Date.now();
    await prisma.$queryRaw`SELECT 1`;
    status.checks.database = {
      status: 'up',
      responseTime: Date.now() - dbStart
    };
  } catch (error) {
    status.checks.database = {
      status: 'down',
      message: error instanceof Error ? error.message : 'Unknown error'
    };
    status.status = 'unhealthy';
  }

  try {
    // Перевірка Redis
    const redisStart = Date.now();
    await redis.ping();
    status.checks.redis = {
      status: 'up',
      responseTime: Date.now() - redisStart
    };
  } catch (error) {
    status.checks.redis = {
      status: 'down',
      message: error instanceof Error ? error.message : 'Unknown error'
    };
    status.status = 'degraded';
  }

  // HTTP статус код відповідно до стану
  const httpStatus = status.status === 'healthy' ? 200 :
                     status.status === 'degraded' ? 200 : 503;

  res.status(httpStatus).json(status);
});

// Детальний health check
router.get('/health/detailed', async (req: Request, res: Response) => {
  const checks = await Promise.allSettled([
    checkDatabase(),
    checkRedis(),
    checkDiskSpace(),
    checkMemory(),
    checkExternalAPIs()
  ]);

  const results = checks.map((check, index) => ({
    name: ['database', 'redis', 'disk', 'memory', 'external_apis'][index],
    status: check.status === 'fulfilled' ? check.value : { status: 'down', error: check.reason }
  }));

  res.json({
    timestamp: new Date().toISOString(),
    checks: results
  });
});

async function checkDatabase() {
  const start = Date.now();
  await prisma.$queryRaw`SELECT 1`;
  return { status: 'up', responseTime: Date.now() - start };
}

async function checkRedis() {
  const start = Date.now();
  await redis.ping();
  return { status: 'up', responseTime: Date.now() - start };
}

async function checkDiskSpace() {
  // Реалізація перевірки дискового простору
  return { status: 'up', available: '50GB' };
}

async function checkMemory() {
  const used = process.memoryUsage();
  return {
    status: 'up',
    heapUsed: `${Math.round(used.heapUsed / 1024 / 1024)}MB`,
    heapTotal: `${Math.round(used.heapTotal / 1024 / 1024)}MB`
  };
}

async function checkExternalAPIs() {
  // Перевірка зовнішніх API
  return { status: 'up' };
}

export default router;
```

## Висновки та найкращі практики

### Ключові принципи успішного DevOps

**Автоматизація є основою** ефективного DevOps процесу. Кожна повторювана задача має бути автоматизована, від запуску тестів до деплою в продакшн. Це не тільки економить час, але й зменшує ймовірність помилок, викликаних людським фактором.

**Моніторинг та спостережуваність** дозволяють розуміти реальний стан системи та швидко реагувати на проблеми. Системи мають бути інструментовані з самого початку, а не як додаткова функція, додана пізніше. Детальна телеметрія допомагає не тільки виявляти проблеми, але й розуміти їх причини.

**Безпека має бути інтегрована** в кожен етап pipeline, від перевірки залежностей на вразливості до аудиту конфігурацій інфраструктури. Безпека не є окремим етапом, а повинна бути невід'ємною частиною процесу розробки та доставки.

**Культура експериментування** та безперервного вдосконалення є критично важливою. Команди мають почуватися комфортно, випробовуючи нові підходи та інструменти, навчаючись на помилках та постійно покращуючи свої процеси.

### Практичні рекомендації для веброзробників

Починайте з простого CI/CD pipeline та поступово розширюйте його можливості. Не намагайтеся впровадити всі практики одночасно. Автоматизуйте найбільш критичні та часто повторювані процеси спочатку, а потім розширюйте автоматизацію на інші області.

Інвестуйте час у написання якісних тестів. Автоматизоване тестування є основою впевненості в змінах коду. Комбінація unit, integration та end-to-end тестів забезпечує комплексне покриття різних аспектів застосунку.

Документуйте свої процеси та рішення. Документація pipeline, стратегій деплою та процедур відновлення має бути доступною та актуальною. Це особливо важливо для нових членів команди та в критичних ситуаціях.

Регулярно переглядайте та оптимізуйте свої процеси. DevOps не є статичним набором практик. Технології та інструменти постійно еволюціонують, і важливо залишатися в курсі нових можливостей та адаптувати свої процеси відповідно.

Створюйте культуру відповідальності, де кожен член команди розуміє важливість якості, безпеки та надійності. DevOps це не тільки про інструменти та процеси, але й про людей та їхню спільну відповідальність за успіх продукту.

### Майбутнє DevOps у веброзробці

DevOps продовжує еволюціонувати разом з технологіями. Serverless архітектури, GitOps підходи, контейнеризація та оркестрація Kubernetes стають все більш поширеними. Штучний інтелект та машинне навчання починають впроваджуватися для автоматичного виявлення аномалій, оптимізації ресурсів та передбачення проблем.

Проте, незалежно від технологій, які приходять і йдуть, фундаментальні принципи DevOps залишаються незмінними: автоматизація, співпраця, безперервне вдосконалення та орієнтація на доставку цінності користувачам. Розуміння цих принципів та вміння застосовувати їх на практиці є ключовою компетенцією сучасного веброзробника.
