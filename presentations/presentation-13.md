# Форми та валідація в React

---

## План лекції

- Controlled vs Uncontrolled компоненти
- React Hook Form основи
- Валідація з Zod схемами
- Кастомні input компоненти
- Файлові завантаження
- UX паттерни для форм

---

## Проблеми роботи з формами

**Основні виклики:**

- Управління станом полів форми
- Валідація даних в реальному часі
- Обробка відправлення форми
- Користувацький досвід (UX)
- Доступність для всіх користувачів

**React пропонує два підходи:**
- Контрольовані компоненти (Controlled)
- Неконтрольовані компоненти (Uncontrolled)

---

## Controlled Components

**Концепція:** значення поля повністю контролюється React через стан

```javascript
function ControlledForm() {
    const [email, setEmail] = useState('');

    return (
        <input
            type="email"
            value={email}
            onChange={(e) => setEmail(e.target.value)}
        />
    );
}
```

**Переваги:**
- Повний контроль над даними
- Валідація в реальному часі
- Легке керування складною логікою

**Недоліки:**
- Більше коду
- Можливі проблеми з продуктивністю

---

## Uncontrolled Components

**Концепція:** значення зберігається в DOM, доступ через refs

```javascript
function UncontrolledForm() {
    const emailRef = useRef();

    const handleSubmit = (e) => {
        e.preventDefault();
        console.log(emailRef.current.value);
    };

    return (
        <form onSubmit={handleSubmit}>
            <input type="email" ref={emailRef} />
            <button type="submit">Відправити</button>
        </form>
    );
}
```

**Переваги:**
- Менше коду
- Краща продуктивність
- Підходить для файлів

---

## React Hook Form: революція в формах

**Чому React Hook Form?**

- 🚀 Висока продуктивність (мінімум ререндерів)
- 📦 Малий розмір бандлу
- ✅ Вбудована валідація
- 🎯 Інтуїтивний API
- 💪 TypeScript підтримка

```bash
npm install react-hook-form
```

---

## React Hook Form: базове використання

```javascript
import { useForm } from 'react-hook-form';

function RegistrationForm() {
    const {
        register,
        handleSubmit,
        formState: { errors }
    } = useForm();

    const onSubmit = (data) => {
        console.log(data);
    };

    return (
        <form onSubmit={handleSubmit(onSubmit)}>
            <input
                {...register('email', {
                    required: 'Email обов\'язковий'
                })}
            />
            {errors.email && <span>{errors.email.message}</span>}

            <button type="submit">Зареєструватися</button>
        </form>
    );
}
```

---

## React Hook Form: складні сценарії

**Динамічні поля з useFieldArray:**

```javascript
const { fields, append, remove } = useFieldArray({
    control,
    name: 'teamMembers'
});

return (
    <>
        {fields.map((field, index) => (
            <div key={field.id}>
                <input {...register(`teamMembers.${index}.name`)} />
                <button onClick={() => remove(index)}>
                    Видалити
                </button>
            </div>
        ))}
        <button onClick={() => append({ name: '' })}>
            Додати учасника
        </button>
    </>
);
```

---

## Zod: типобезпечна валідація

**Переваги Zod:**

- TypeScript-first бібліотека
- Автоматична генерація типів
- Виразний синтаксис
- Асинхронна валідація
- Трансформація даних

```bash
npm install zod @hookform/resolvers
```

---

## Zod: базова схема

```javascript
import { z } from 'zod';
import { zodResolver } from '@hookform/resolvers/zod';

const schema = z.object({
    username: z.string()
        .min(3, 'Мінімум 3 символи')
        .max(20, 'Максимум 20 символів'),

    email: z.string()
        .email('Некоректний email'),

    password: z.string()
        .min(8, 'Мінімум 8 символів')
        .regex(/[A-Z]/, 'Потрібна велика літера'),

    age: z.number()
        .int()
        .min(18, 'Вік від 18 років')
});

type FormData = z.infer<typeof schema>;
```

---

## Zod + React Hook Form

```javascript
const {
    register,
    handleSubmit,
    formState: { errors }
} = useForm<FormData>({
    resolver: zodResolver(schema)
});

const onSubmit = (data: FormData) => {
    // Дані вже провалідовані та типізовані!
    console.log(data);
};

return (
    <form onSubmit={handleSubmit(onSubmit)}>
        <input {...register('username')} />
        {errors.username && (
            <span>{errors.username.message}</span>
        )}
        {/* інші поля */}
    </form>
);
```

---

## Складні схеми Zod

**Вкладені об'єкти та умовна валідація:**

```javascript
const projectSchema = z.object({
    title: z.string().min(1),
    budget: z.object({
        amount: z.number().positive(),
        currency: z.enum(['UAH', 'USD', 'EUR'])
    }),
    tags: z.array(z.string())
        .min(1, 'Хоча б один тег')
        .max(5, 'Максимум 5 тегів'),
    team: z.array(
        z.object({
            name: z.string(),
            role: z.enum(['developer', 'designer'])
        })
    ).min(1)
}).refine(
    data => data.budget.amount > 1000,
    { message: 'Бюджет має бути більше 1000' }
);
```

---

## Кастомні Input компоненти

**Принципи створення:**

- Інкапсуляція логіки та стилів
- Підтримка forwardRef для refs
- Доступність (a11y)
- Гнучкість через props
- Консистентний дизайн

```javascript
const Input = forwardRef(({
    label,
    error,
    ...props
}, ref) => {
    return (
        <div>
            {label && <label>{label}</label>}
            <input ref={ref} {...props} />
            {error && <span>{error}</span>}
        </div>
    );
});
```

---

## Password Input з індикатором

```javascript
const PasswordInput = forwardRef(({
    showStrength = false,
    ...props
}, ref) => {
    const [password, setPassword] = useState('');
    const [showPassword, setShowPassword] = useState(false);

    const strength = calculateStrength(password);

    return (
        <div>
            <input
                ref={ref}
                type={showPassword ? 'text' : 'password'}
                value={password}
                onChange={(e) => setPassword(e.target.value)}
                {...props}
            />
            <button onClick={() => setShowPassword(!showPassword)}>
                {showPassword ? '🙈' : '👁️'}
            </button>
            {showStrength && <StrengthBar strength={strength} />}
        </div>
    );
});
```

---

## Файлові завантаження

**Базовий підхід:**

```javascript
function FileUpload() {
    const { register, watch } = useForm();
    const file = watch('avatar');

    return (
        <div>
            <input
                type="file"
                accept="image/*"
                {...register('avatar', {
                    validate: {
                        fileSize: files => {
                            if (files[0]?.size > 5000000) {
                                return 'Максимум 5MB';
                            }
                            return true;
                        }
                    }
                })}
            />
            {file && file[0] && (
                <ImagePreview file={file[0]} />
            )}
        </div>
    );
}
```

---

## Drag & Drop завантаження

```javascript
import { useDropzone } from 'react-dropzone';

function DragDropUpload() {
    const { getRootProps, getInputProps, isDragActive } = useDropzone({
        accept: { 'image/*': ['.jpeg', '.png'] },
        maxSize: 5242880,
        onDrop: (acceptedFiles) => {
            console.log(acceptedFiles);
        }
    });

    return (
        <div {...getRootProps()}
             className={isDragActive ? 'active' : ''}>
            <input {...getInputProps()} />
            <p>Перетягніть файли або клацніть для вибору</p>
        </div>
    );
}
```

---

## UX паттерни: інлайн валідація

```javascript
function SmartValidation() {
    const { register, formState: { errors, touchedFields } }
        = useForm({ mode: 'onBlur' });

    return (
        <div>
            <input {...register('email', {
                required: true,
                pattern: /^[^\s@]+@[^\s@]+\.[^\s@]+$/
            })} />

            {touchedFields.email && !errors.email && (
                <span className="success">✓ Email валідний</span>
            )}

            {errors.email && (
                <span className="error">
                    {errors.email.message}
                </span>
            )}
        </div>
    );
}
```

---

## Багатокрокові форми

```javascript
function MultiStepForm() {
    const [step, setStep] = useState(1);
    const { register, trigger } = useForm();

    const nextStep = async () => {
        const fieldsToValidate = getFieldsForStep(step);
        const isValid = await trigger(fieldsToValidate);

        if (isValid) {
            setStep(step + 1);
        }
    };

    return (
        <div>
            <ProgressBar currentStep={step} totalSteps={3} />

            {step === 1 && <PersonalInfo register={register} />}
            {step === 2 && <AddressInfo register={register} />}
            {step === 3 && <PaymentInfo register={register} />}

            <button onClick={nextStep}>Далі</button>
        </div>
    );
}
```

---

## Автозбереження та відновлення

```javascript
function AutoSaveForm() {
    const { watch, reset } = useForm();
    const formData = watch();

    // Автозбереження після затримки
    useEffect(() => {
        const timer = setTimeout(() => {
            localStorage.setItem('draft', JSON.stringify(formData));
        }, 1000);
        return () => clearTimeout(timer);
    }, [formData]);

    // Відновлення при завантаженні
    useEffect(() => {
        const saved = localStorage.getItem('draft');
        if (saved) {
            reset(JSON.parse(saved));
        }
    }, []);

    return <form>{/* ... */}</form>;
}
```

---

## Доступність форм

**Основні принципи:**

- Використовуйте `<label>` для всіх полів
- Додавайте `aria-label` та `aria-describedby`
- Позначайте помилки через `aria-invalid`
- Забезпечте навігацію клавіатурою
- Використовуйте семантичні елементи

```javascript
<div>
    <label htmlFor="email">Email</label>
    <input
        id="email"
        aria-invalid={!!errors.email}
        aria-describedby={errors.email ? 'email-error' : undefined}
    />
    {errors.email && (
        <span id="email-error" role="alert">
            {errors.email.message}
        </span>
    )}
</div>
```

---

## Найкращі практики

**Вибір підходу:**
- Контрольовані компоненти для складних форм
- React Hook Form для оптимальної продуктивності
- Zod для типобезпечної валідації

**Валідація:**
- Клієнтська валідація покращує UX
- Серверна валідація обов'язкова для безпеки
- Надавайте чіткі повідомлення про помилки

**UX:**
- Інлайн валідація для швидкого зворотного зв'язку
- Прогрес-індикатори для багатокрокових форм
- Автозбереження для довгих форм

---

## Приклад: повна форма реєстрації

```javascript
const schema = z.object({
    name: z.string().min(2),
    email: z.string().email(),
    password: z.string().min(8)
}).refine(data => data.password.includes(data.name) === false, {
    message: 'Пароль не може містити ім\'я'
});

function Registration() {
    const { register, handleSubmit, formState: { errors } }
        = useForm({ resolver: zodResolver(schema) });

    const onSubmit = async (data) => {
        try {
            await api.register(data);
            navigate('/dashboard');
        } catch (error) {
            toast.error('Помилка реєстрації');
        }
    };

    return <form onSubmit={handleSubmit(onSubmit)}>{/* ... */}</form>;
}
```

---

## Корисні ресурси

**Бібліотеки:**
- [React Hook Form](https://react-hook-form.com/)
- [Zod](https://zod.dev/)
- [React Dropzone](https://react-dropzone.js.org/)

**Статті:**
- [Controlled vs Uncontrolled Components](https://react.dev/learn/sharing-state-between-components)
- [Form Validation Best Practices](https://web.dev/sign-in-form-best-practices/)

**Інструменти:**
- [Formik](https://formik.org/) - альтернатива RHF
- [Yup](https://github.com/jquense/yup) - альтернатива Zod
