# Лекція 13. Форми та валідація

## Вступ до роботи з формами в React

Форми є невід'ємною частиною більшості вебзастосунків. Вони забезпечують основний спосіб взаємодії користувачів з додатком, збираючи дані для реєстрації, входу, оформлення замовлень, створення контенту та багатьох інших операцій. У React робота з формами має свої особливості, які відрізняють її від традиційних HTML форм.

Основні виклики при роботі з формами в React включають управління станом полів, валідацію даних, обробку відправлення форми та забезпечення гарного користувацького досвіду. Протягом еволюції React спільнота розробила різні підходи та інструменти для ефективної роботи з формами.

## Controlled vs Uncontrolled компоненти

### Концептуальні відмінності

Фундаментальною концепцією в роботі з формами в React є розуміння різниці між контрольованими та неконтрольованими компонентами. Цей вибір визначає архітектуру вашого коду та спосіб управління даними форми.

**Контрольовані компоненти** – це елементи форми, значення яких повністю контролюються React через стан компонента. Кожна зміна в полі миттєво оновлює стан, а значення поля завжди відображає поточний стан. Це забезпечує єдине джерело правди для даних форми.

**Неконтрольовані компоненти** натомість зберігають свій власний стан в DOM, подібно до традиційних HTML елементів. React отримує доступ до їх значень через refs, зазвичай тільки при відправленні форми.

### Контрольовані компоненти: глибоке розуміння

```javascript
function ControlledForm() {
    const [formData, setFormData] = useState({
        username: '',
        email: '',
        password: '',
        agreeToTerms: false,
        country: 'Ukraine'
    });

    // Універсальний обробник для всіх полів
    const handleChange = (event) => {
        const { name, value, type, checked } = event.target;
        
        setFormData(prevData => ({
            ...prevData,
            [name]: type === 'checkbox' ? checked : value
        }));
    };

    const handleSubmit = (event) => {
        event.preventDefault();
        console.log('Надіслані дані:', formData);
    };

    return (
        <form onSubmit={handleSubmit}>
            <input
                type="text"
                name="username"
                value={formData.username}
                onChange={handleChange}
                placeholder="Ім'я користувача"
            />
            
            <input
                type="email"
                name="email"
                value={formData.email}
                onChange={handleChange}
                placeholder="Email"
            />
            
            <input
                type="password"
                name="password"
                value={formData.password}
                onChange={handleChange}
                placeholder="Пароль"
            />
            
            <select
                name="country"
                value={formData.country}
                onChange={handleChange}
            >
                <option value="Ukraine">Україна</option>
                <option value="Poland">Польща</option>
                <option value="Germany">Німеччина</option>
            </select>
            
            <label>
                <input
                    type="checkbox"
                    name="agreeToTerms"
                    checked={formData.agreeToTerms}
                    onChange={handleChange}
                />
                Погоджуюсь з умовами використання
            </label>
            
            <button type="submit">Зареєструватися</button>
        </form>
    );
}
```

Переваги контрольованих компонентів включають повний контроль над даними форми, можливість валідації в реальному часі, легке керування складною логікою та синхронізацію даних між різними частинами інтерфейсу. Однак вони вимагають більше коду та можуть призводити до проблем з продуктивністю при великій кількості полів через часті перерендери.

### Неконтрольовані компоненти: практичне застосування

```javascript
function UncontrolledForm() {
    const usernameRef = useRef();
    const emailRef = useRef();
    const passwordRef = useRef();
    const fileRef = useRef();

    const handleSubmit = (event) => {
        event.preventDefault();
        
        const formData = {
            username: usernameRef.current.value,
            email: emailRef.current.value,
            password: passwordRef.current.value,
            file: fileRef.current.files[0]
        };
        
        console.log('Надіслані дані:', formData);
    };

    return (
        <form onSubmit={handleSubmit}>
            <input
                type="text"
                ref={usernameRef}
                defaultValue="Іван"
                placeholder="Ім'я користувача"
            />
            
            <input
                type="email"
                ref={emailRef}
                placeholder="Email"
            />
            
            <input
                type="password"
                ref={passwordRef}
                placeholder="Пароль"
            />
            
            <input
                type="file"
                ref={fileRef}
            />
            
            <button type="submit">Відправити</button>
        </form>
    );
}
```

Неконтрольовані компоненти особливо корисні для інтеграції з нереактивними бібліотеками, роботи з файловими завантаженнями та оптимізації продуктивності у формах з великою кількістю полів. Вони також зменшують кількість необхідного коду для простих форм.

### Гібридний підхід

```javascript
function HybridForm() {
    // Контрольоване поле для email з валідацією в реальному часі
    const [email, setEmail] = useState('');
    const [emailError, setEmailError] = useState('');
    
    // Неконтрольовані поля для інших даних
    const nameRef = useRef();
    const messageRef = useRef();

    const validateEmail = (value) => {
        const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
        if (!emailRegex.test(value)) {
            setEmailError('Некоректний формат email');
        } else {
            setEmailError('');
        }
    };

    const handleEmailChange = (e) => {
        const value = e.target.value;
        setEmail(value);
        validateEmail(value);
    };

    const handleSubmit = (e) => {
        e.preventDefault();
        
        if (emailError) {
            alert('Виправте помилки у формі');
            return;
        }
        
        const data = {
            name: nameRef.current.value,
            email: email,
            message: messageRef.current.value
        };
        
        console.log('Відправлені дані:', data);
    };

    return (
        <form onSubmit={handleSubmit}>
            <input
                type="text"
                ref={nameRef}
                placeholder="Ім'я"
            />
            
            <input
                type="email"
                value={email}
                onChange={handleEmailChange}
                placeholder="Email"
            />
            {emailError && <span className="error">{emailError}</span>}
            
            <textarea
                ref={messageRef}
                placeholder="Повідомлення"
            />
            
            <button type="submit">Надіслати</button>
        </form>
    );
}
```

## React Hook Form: сучасний підхід до форм

### Концепція та переваги

React Hook Form є бібліотекою, яка революціонізувала роботу з формами в React. Її філософія базується на мінімізації перерендерів, використанні неконтрольованих компонентів з refs та наданні потужного API для валідації.

Основні переваги React Hook Form:

- Висока продуктивність через мінімальну кількість ререндерів
- Менший розмір бандлу порівняно з альтернативами
- Вбудована інтеграція з популярними бібліотеками валідації
- Інтуїтивний API з TypeScript підтримкою
- Легка робота зі складними формами та вкладеними об'єктами

### Базове використання

```javascript
import { useForm } from 'react-hook-form';

function RegistrationForm() {
    const {
        register,
        handleSubmit,
        formState: { errors, isSubmitting }
    } = useForm();

    const onSubmit = async (data) => {
        // Симуляція API запиту
        await new Promise(resolve => setTimeout(resolve, 1000));
        console.log('Дані форми:', data);
    };

    return (
        <form onSubmit={handleSubmit(onSubmit)}>
            <div>
                <label htmlFor="username">Ім'я користувача</label>
                <input
                    id="username"
                    {...register('username', {
                        required: "Ім'я користувача обов'язкове",
                        minLength: {
                            value: 3,
                            message: 'Мінімум 3 символи'
                        }
                    })}
                />
                {errors.username && (
                    <span className="error">{errors.username.message}</span>
                )}
            </div>

            <div>
                <label htmlFor="email">Email</label>
                <input
                    id="email"
                    type="email"
                    {...register('email', {
                        required: 'Email обов\'язковий',
                        pattern: {
                            value: /^[^\s@]+@[^\s@]+\.[^\s@]+$/,
                            message: 'Некоректний формат email'
                        }
                    })}
                />
                {errors.email && (
                    <span className="error">{errors.email.message}</span>
                )}
            </div>

            <div>
                <label htmlFor="password">Пароль</label>
                <input
                    id="password"
                    type="password"
                    {...register('password', {
                        required: 'Пароль обов\'язковий',
                        minLength: {
                            value: 8,
                            message: 'Пароль має містити мінімум 8 символів'
                        }
                    })}
                />
                {errors.password && (
                    <span className="error">{errors.password.message}</span>
                )}
            </div>

            <button type="submit" disabled={isSubmitting}>
                {isSubmitting ? 'Обробка...' : 'Зареєструватися'}
            </button>
        </form>
    );
}
```

### Складні сценарії використання

```javascript
import { useForm, useFieldArray } from 'react-hook-form';

function ProjectForm() {
    const {
        register,
        control,
        handleSubmit,
        watch,
        setValue,
        formState: { errors }
    } = useForm({
        defaultValues: {
            projectName: '',
            teamMembers: [{ name: '', role: '' }],
            priority: 'medium'
        }
    });

    const { fields, append, remove } = useFieldArray({
        control,
        name: 'teamMembers'
    });

    // Спостереження за змінами в полі
    const projectName = watch('projectName');

    const onSubmit = (data) => {
        console.log('Дані проєкту:', data);
    };

    return (
        <form onSubmit={handleSubmit(onSubmit)}>
            <div>
                <label>Назва проєкту</label>
                <input
                    {...register('projectName', {
                        required: 'Назва проєкту обов\'язкова'
                    })}
                />
                {errors.projectName && (
                    <span className="error">{errors.projectName.message}</span>
                )}
            </div>

            <div>
                <h3>Учасники команди</h3>
                {fields.map((field, index) => (
                    <div key={field.id} className="team-member">
                        <input
                            {...register(`teamMembers.${index}.name`, {
                                required: "Ім'я обов'язкове"
                            })}
                            placeholder="Ім'я"
                        />
                        
                        <input
                            {...register(`teamMembers.${index}.role`, {
                                required: 'Роль обов\'язкова'
                            })}
                            placeholder="Роль"
                        />
                        
                        <button
                            type="button"
                            onClick={() => remove(index)}
                        >
                            Видалити
                        </button>
                        
                        {errors.teamMembers?.[index] && (
                            <span className="error">
                                Заповніть всі поля учасника
                            </span>
                        )}
                    </div>
                ))}
                
                <button
                    type="button"
                    onClick={() => append({ name: '', role: '' })}
                >
                    Додати учасника
                </button>
            </div>

            <div>
                <label>Пріоритет</label>
                <select {...register('priority')}>
                    <option value="low">Низький</option>
                    <option value="medium">Середній</option>
                    <option value="high">Високий</option>
                </select>
            </div>

            {projectName && (
                <div className="preview">
                    Попередній перегляд: Проєкт "{projectName}"
                </div>
            )}

            <button type="submit">Створити проєкт</button>
        </form>
    );
}
```

## Валідація з Zod схемами

### Введення в Zod

Zod є TypeScript-first бібліотекою для валідації схем, яка надає потужний та типобезпечний спосіб визначення структури даних та їх валідації. Інтеграція Zod з React Hook Form створює ідеальну комбінацію для роботи з формами.

### Базова інтеграція

```javascript
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { z } from 'zod';

// Визначення схеми валідації
const registrationSchema = z.object({
    username: z.string()
        .min(3, 'Ім\'я користувача має містити мінімум 3 символи')
        .max(20, 'Ім\'я користувача не може перевищувати 20 символів')
        .regex(/^[a-zA-Z0-9_]+$/, 'Тільки літери, цифри та підкреслення'),
    
    email: z.string()
        .email('Некоректний формат email')
        .toLowerCase(),
    
    password: z.string()
        .min(8, 'Пароль має містити мінімум 8 символів')
        .regex(/[A-Z]/, 'Пароль має містити хоча б одну велику літеру')
        .regex(/[a-z]/, 'Пароль має містити хоча б одну малу літеру')
        .regex(/[0-9]/, 'Пароль має містити хоча б одну цифру'),
    
    confirmPassword: z.string(),
    
    age: z.number()
        .int('Вік має бути цілим числом')
        .min(18, 'Вік має бути не менше 18 років')
        .max(120, 'Некоректний вік'),
    
    website: z.string()
        .url('Некоректний URL')
        .optional()
        .or(z.literal('')),
    
    agreeToTerms: z.boolean()
        .refine(val => val === true, {
            message: 'Необхідно прийняти умови використання'
        })
}).refine(data => data.password === data.confirmPassword, {
    message: 'Паролі не співпадають',
    path: ['confirmPassword']
});

// TypeScript тип на основі схеми
type RegistrationFormData = z.infer<typeof registrationSchema>;

function RegistrationFormWithZod() {
    const {
        register,
        handleSubmit,
        formState: { errors, isSubmitting }
    } = useForm<RegistrationFormData>({
        resolver: zodResolver(registrationSchema)
    });

    const onSubmit = async (data: RegistrationFormData) => {
        console.log('Валідні дані:', data);
    };

    return (
        <form onSubmit={handleSubmit(onSubmit)}>
            <div>
                <input
                    {...register('username')}
                    placeholder="Ім'я користувача"
                />
                {errors.username && (
                    <span className="error">{errors.username.message}</span>
                )}
            </div>

            <div>
                <input
                    {...register('email')}
                    type="email"
                    placeholder="Email"
                />
                {errors.email && (
                    <span className="error">{errors.email.message}</span>
                )}
            </div>

            <div>
                <input
                    {...register('password')}
                    type="password"
                    placeholder="Пароль"
                />
                {errors.password && (
                    <span className="error">{errors.password.message}</span>
                )}
            </div>

            <div>
                <input
                    {...register('confirmPassword')}
                    type="password"
                    placeholder="Підтвердження паролю"
                />
                {errors.confirmPassword && (
                    <span className="error">{errors.confirmPassword.message}</span>
                )}
            </div>

            <div>
                <input
                    {...register('age', { valueAsNumber: true })}
                    type="number"
                    placeholder="Вік"
                />
                {errors.age && (
                    <span className="error">{errors.age.message}</span>
                )}
            </div>

            <div>
                <input
                    {...register('website')}
                    placeholder="Веб-сайт (необов'язково)"
                />
                {errors.website && (
                    <span className="error">{errors.website.message}</span>
                )}
            </div>

            <div>
                <label>
                    <input
                        {...register('agreeToTerms')}
                        type="checkbox"
                    />
                    Погоджуюсь з умовами використання
                </label>
                {errors.agreeToTerms && (
                    <span className="error">{errors.agreeToTerms.message}</span>
                )}
            </div>

            <button type="submit" disabled={isSubmitting}>
                Зареєструватися
            </button>
        </form>
    );
}
```

### Складні схеми валідації

```javascript
import { z } from 'zod';

// Вкладені об'єкти та масиви
const projectSchema = z.object({
    title: z.string().min(1, 'Назва обов\'язкова'),
    
    description: z.string()
        .min(10, 'Опис має містити мінімум 10 символів')
        .max(500, 'Опис не може перевищувати 500 символів'),
    
    budget: z.object({
        amount: z.number().positive('Сума має бути позитивною'),
        currency: z.enum(['UAH', 'USD', 'EUR'])
    }),
    
    tags: z.array(z.string())
        .min(1, 'Додайте хоча б один тег')
        .max(5, 'Максимум 5 тегів'),
    
    team: z.array(
        z.object({
            name: z.string().min(1, 'Ім\'я обов\'язкове'),
            role: z.enum(['developer', 'designer', 'manager']),
            email: z.string().email('Некоректний email')
        })
    ).min(1, 'Команда має містити хоча б одного учасника'),
    
    startDate: z.string()
        .refine(val => !isNaN(Date.parse(val)), {
            message: 'Некоректна дата'
        })
        .transform(val => new Date(val)),
    
    status: z.enum(['planning', 'active', 'completed', 'cancelled'])
        .default('planning')
});

// Умовна валідація
const taskSchema = z.object({
    title: z.string().min(1),
    priority: z.enum(['low', 'medium', 'high']),
    assigneeRequired: z.boolean(),
    assignee: z.string().optional()
}).refine(
    data => {
        if (data.assigneeRequired) {
            return data.assignee && data.assignee.length > 0;
        }
        return true;
    },
    {
        message: 'Виконавець обов\'язковий для цього завдання',
        path: ['assignee']
    }
);

// Асинхронна валідація
const usernameSchema = z.string()
    .min(3)
    .refine(
        async (username) => {
            // Перевірка на сервері, чи ім'я користувача вільне
            const response = await fetch(`/api/check-username?username=${username}`);
            const data = await response.json();
            return data.available;
        },
        {
            message: 'Це ім\'я користувача вже зайняте'
        }
    );
```

## Кастомні input компоненти

### Принципи створення багаторазових компонентів

Створення кастомних input компонентів дозволяє інкапсулювати логіку, стилізацію та поведінку полів форми, роблячи код більш підтримуваним та послідовним.

```javascript
import { forwardRef } from 'react';

// Базовий input компонент
const Input = forwardRef(({ 
    label, 
    error, 
    helperText,
    ...props 
}, ref) => {
    return (
        <div className="input-wrapper">
            {label && (
                <label htmlFor={props.id || props.name}>
                    {label}
                    {props.required && <span className="required">*</span>}
                </label>
            )}
            
            <input
                ref={ref}
                className={`input ${error ? 'input-error' : ''}`}
                {...props}
            />
            
            {error && (
                <span className="error-message">{error}</span>
            )}
            
            {helperText && !error && (
                <span className="helper-text">{helperText}</span>
            )}
        </div>
    );
});

// Використання з React Hook Form
function FormWithCustomInputs() {
    const {
        register,
        handleSubmit,
        formState: { errors }
    } = useForm();

    return (
        <form onSubmit={handleSubmit(data => console.log(data))}>
            <Input
                label="Електронна пошта"
                type="email"
                {...register('email', { required: 'Email обов\'язковий' })}
                error={errors.email?.message}
                helperText="Ми ніколи не передамо вашу пошту третім особам"
            />
            
            <Input
                label="Пароль"
                type="password"
                {...register('password', { required: 'Пароль обов\'язковий' })}
                error={errors.password?.message}
                required
            />
            
            <button type="submit">Увійти</button>
        </form>
    );
}
```

### Складні кастомні компоненти

```javascript
import { useState, forwardRef } from 'react';

// Password input з індикатором надійності
const PasswordInput = forwardRef(({ 
    label, 
    error,
    showStrength = false,
    ...props 
}, ref) => {
    const [password, setPassword] = useState('');
    const [showPassword, setShowPassword] = useState(false);

    const calculateStrength = (pass) => {
        let strength = 0;
        if (pass.length >= 8) strength++;
        if (/[A-Z]/.test(pass)) strength++;
        if (/[a-z]/.test(pass)) strength++;
        if (/[0-9]/.test(pass)) strength++;
        if (/[^A-Za-z0-9]/.test(pass)) strength++;
        return strength;
    };

    const strength = calculateStrength(password);
    const strengthLabels = ['Дуже слабкий', 'Слабкий', 'Середній', 'Сильний', 'Дуже сильний'];
    const strengthColors = ['red', 'orange', 'yellow', 'lightgreen', 'green'];

    const handleChange = (e) => {
        setPassword(e.target.value);
        props.onChange?.(e);
    };

    return (
        <div className="password-input-wrapper">
            {label && <label>{label}</label>}
            
            <div className="password-input-container">
                <input
                    ref={ref}
                    type={showPassword ? 'text' : 'password'}
                    value={password}
                    onChange={handleChange}
                    className={error ? 'input-error' : ''}
                    {...props}
                />
                
                <button
                    type="button"
                    onClick={() => setShowPassword(!showPassword)}
                    className="toggle-password"
                >
                    {showPassword ? '🙈' : '👁️'}
                </button>
            </div>
            
            {showStrength && password.length > 0 && (
                <div className="password-strength">
                    <div className="strength-bar">
                        {[...Array(5)].map((_, i) => (
                            <div
                                key={i}
                                className="strength-segment"
                                style={{
                                    backgroundColor: i < strength ? strengthColors[strength - 1] : '#e0e0e0'
                                }}
                            />
                        ))}
                    </div>
                    <span className="strength-label">
                        {strengthLabels[strength - 1] || 'Дуже слабкий'}
                    </span>
                </div>
            )}
            
            {error && <span className="error-message">{error}</span>}
        </div>
    );
});

// Select з пошуком
const SearchableSelect = forwardRef(({ 
    options,
    label,
    error,
    placeholder = 'Виберіть опцію...',
    ...props 
}, ref) => {
    const [isOpen, setIsOpen] = useState(false);
    const [searchTerm, setSearchTerm] = useState('');
    const [selectedValue, setSelectedValue] = useState('');

    const filteredOptions = options.filter(option =>
        option.label.toLowerCase().includes(searchTerm.toLowerCase())
    );

    const handleSelect = (value) => {
        setSelectedValue(value);
        setIsOpen(false);
        setSearchTerm('');
        
        // Виклик onChange для React Hook Form
        const event = {
            target: {
                name: props.name,
                value: value
            }
        };
        props.onChange?.(event);
    };

    const selectedOption = options.find(opt => opt.value === selectedValue);

    return (
        <div className="searchable-select-wrapper">
            {label && <label>{label}</label>}
            
            <div className="searchable-select">
                <div
                    className={`select-trigger ${error ? 'error' : ''}`}
                    onClick={() => setIsOpen(!isOpen)}
                >
                    {selectedOption ? selectedOption.label : placeholder}
                    <span className="arrow">{isOpen ? '▲' : '▼'}</span>
                </div>
                
                {isOpen && (
                    <div className="select-dropdown">
                        <input
                            type="text"
                            className="search-input"
                            placeholder="Пошук..."
                            value={searchTerm}
                            onChange={(e) => setSearchTerm(e.target.value)}
                            onClick={(e) => e.stopPropagation()}
                        />
                        
                        <div className="options-list">
                            {filteredOptions.length > 0 ? (
                                filteredOptions.map(option => (
                                    <div
                                        key={option.value}
                                        className={`option ${option.value === selectedValue ? 'selected' : ''}`}
                                        onClick={() => handleSelect(option.value)}
                                    >
                                        {option.label}
                                    </div>
                                ))
                            ) : (
                                <div className="no-options">Нічого не знайдено</div>
                            )}
                        </div>
                    </div>
                )}
            </div>
            
            <input
                ref={ref}
                type="hidden"
                value={selectedValue}
                {...props}
            />
            
            {error && <span className="error-message">{error}</span>}
        </div>
    );
});
```

## Файлові завантаження

### Базове завантаження файлів

```javascript
import { useState } from 'react';
import { useForm } from 'react-hook-form';

function FileUploadForm() {
    const [preview, setPreview] = useState(null);
    const { register, handleSubmit, watch } = useForm();

    // Спостереження за змінами файлу
    const file = watch('avatar');

    // Генерація превью для зображень
    useState(() => {
        if (file && file[0]) {
            const reader = new FileReader();
            reader.onloadend = () => {
                setPreview(reader.result);
            };
            reader.readAsDataURL(file[0]);
        }
    }, [file]);

    const onSubmit = async (data) => {
        const formData = new FormData();
        formData.append('avatar', data.avatar[0]);
        formData.append('name', data.name);

        try {
            const response = await fetch('/api/upload', {
                method: 'POST',
                body: formData
            });
            const result = await response.json();
            console.log('Файл завантажено:', result);
        } catch (error) {
            console.error('Помилка завантаження:', error);
        }
    };

    return (
        <form onSubmit={handleSubmit(onSubmit)}>
            <input
                {...register('name', { required: true })}
                placeholder="Ваше ім'я"
            />
            
            <div>
                <label>Аватар</label>
                <input
                    type="file"
                    accept="image/*"
                    {...register('avatar', {
                        required: 'Файл обов\'язковий',
                        validate: {
                            fileSize: files => {
                                if (files[0]?.size > 5000000) {
                                    return 'Розмір файлу не може перевищувати 5MB';
                                }
                                return true;
                            },
                            fileType: files => {
                                const allowedTypes = ['image/jpeg', 'image/png', 'image/gif'];
                                if (!allowedTypes.includes(files[0]?.type)) {
                                    return 'Підтримуються тільки JPEG, PNG та GIF';
                                }
                                return true;
                            }
                        }
                    })}
                />
                
                {preview && (
                    <div className="image-preview">
                        <img src={preview} alt="Превью" />
                    </div>
                )}
            </div>
            
            <button type="submit">Завантажити</button>
        </form>
    );
}
```

### Drag and Drop завантаження

```javascript
import { useCallback, useState } from 'react';
import { useDropzone } from 'react-dropzone';

function DragDropUpload() {
    const [files, setFiles] = useState([]);
    const [uploadProgress, setUploadProgress] = useState({});

    const onDrop = useCallback((acceptedFiles) => {
        setFiles(prev => [...prev, ...acceptedFiles.map(file => 
            Object.assign(file, {
                preview: URL.createObjectURL(file)
            })
        )]);
    }, []);

    const { getRootProps, getInputProps, isDragActive } = useDropzone({
        onDrop,
        accept: {
            'image/*': ['.jpeg', '.jpg', '.png', '.gif']
        },
        maxSize: 5242880, // 5MB
        multiple: true
    });

    const uploadFile = async (file, index) => {
        const formData = new FormData();
        formData.append('file', file);

        try {
            const xhr = new XMLHttpRequest();
            
            xhr.upload.addEventListener('progress', (e) => {
                if (e.lengthComputable) {
                    const percentComplete = (e.loaded / e.total) * 100;
                    setUploadProgress(prev => ({
                        ...prev,
                        [index]: percentComplete
                    }));
                }
            });

            xhr.addEventListener('load', () => {
                if (xhr.status === 200) {
                    console.log('Файл завантажено:', file.name);
                }
            });

            xhr.open('POST', '/api/upload');
            xhr.send(formData);
        } catch (error) {
            console.error('Помилка завантаження:', error);
        }
    };

    const removeFile = (index) => {
        setFiles(prev => prev.filter((_, i) => i !== index));
    };

    return (
        <div className="drag-drop-container">
            <div
                {...getRootProps()}
                className={`dropzone ${isDragActive ? 'active' : ''}`}
            >
                <input {...getInputProps()} />
                {isDragActive ? (
                    <p>Перетягніть файли сюди...</p>
                ) : (
                    <p>Перетягніть файли сюди або клацніть для вибору</p>
                )}
            </div>

            {files.length > 0 && (
                <div className="files-list">
                    <h3>Вибрані файли:</h3>
                    {files.map((file, index) => (
                        <div key={index} className="file-item">
                            <img
                                src={file.preview}
                                alt={file.name}
                                className="file-preview"
                            />
                            
                            <div className="file-info">
                                <p className="file-name">{file.name}</p>
                                <p className="file-size">
                                    {(file.size / 1024).toFixed(2)} KB
                                </p>
                                
                                {uploadProgress[index] !== undefined && (
                                    <div className="progress-bar">
                                        <div
                                            className="progress-fill"
                                            style={{ width: `${uploadProgress[index]}%` }}
                                        />
                                        <span>{uploadProgress[index].toFixed(0)}%</span>
                                    </div>
                                )}
                            </div>
                            
                            <button
                                type="button"
                                onClick={() => removeFile(index)}
                                className="remove-button"
                            >
                                ✕
                            </button>
                        </div>
                    ))}
                    
                    <button
                        onClick={() => files.forEach((file, index) => uploadFile(file, index))}
                        className="upload-all-button"
                    >
                        Завантажити всі файли
                    </button>
                </div>
            )}
        </div>
    );
}
```

## UX паттерни для форм

### Інлайн валідація та миттєві підказки

```javascript
import { useState } from 'react';
import { useForm } from 'react-hook-form';

function SmartValidationForm() {
    const {
        register,
        handleSubmit,
        formState: { errors, touchedFields },
        watch
    } = useForm({ mode: 'onBlur' });

    const password = watch('password');
    const [isCheckingUsername, setIsCheckingUsername] = useState(false);

    const checkUsername = async (username) => {
        setIsCheckingUsername(true);
        // Симуляція API запиту
        await new Promise(resolve => setTimeout(resolve, 500));
        setIsCheckingUsername(false);
        
        // Перевірка на сервері
        return username !== 'admin';
    };

    return (
        <form onSubmit={handleSubmit(data => console.log(data))}>
            <div className="form-field">
                <label>Ім'я користувача</label>
                <input
                    {...register('username', {
                        required: 'Обов\'язкове поле',
                        validate: async (value) => {
                            const isAvailable = await checkUsername(value);
                            return isAvailable || 'Це ім\'я вже зайняте';
                        }
                    })}
                />
                
                {isCheckingUsername && (
                    <span className="checking">Перевірка доступності...</span>
                )}
                
                {touchedFields.username && !errors.username && !isCheckingUsername && (
                    <span className="success">✓ Ім'я доступне</span>
                )}
                
                {errors.username && (
                    <span className="error">{errors.username.message}</span>
                )}
            </div>

            <div className="form-field">
                <label>Пароль</label>
                <input
                    type="password"
                    {...register('password', {
                        required: 'Обов\'язкове поле',
                        minLength: {
                            value: 8,
                            message: 'Мінімум 8 символів'
                        }
                    })}
                />
                
                {password && (
                    <div className="password-requirements">
                        <p className={password.length >= 8 ? 'met' : ''}>
                            ✓ Мінімум 8 символів
                        </p>
                        <p className={/[A-Z]/.test(password) ? 'met' : ''}>
                            ✓ Одна велика літера
                        </p>
                        <p className={/[0-9]/.test(password) ? 'met' : ''}>
                            ✓ Одна цифра
                        </p>
                    </div>
                )}
                
                {errors.password && (
                    <span className="error">{errors.password.message}</span>
                )}
            </div>

            <button type="submit">Зареєструватися</button>
        </form>
    );
}
```

### Багатокрокові форми

```javascript
import { useState } from 'react';
import { useForm } from 'react-hook-form';

function MultiStepForm() {
    const [step, setStep] = useState(1);
    const totalSteps = 3;
    
    const {
        register,
        handleSubmit,
        trigger,
        formState: { errors }
    } = useForm();

    const nextStep = async () => {
        // Валідація поточного кроку
        const fieldsToValidate = getFieldsForStep(step);
        const isValid = await trigger(fieldsToValidate);
        
        if (isValid && step < totalSteps) {
            setStep(step + 1);
        }
    };

    const prevStep = () => {
        if (step > 1) {
            setStep(step - 1);
        }
    };

    const getFieldsForStep = (currentStep) => {
        switch (currentStep) {
            case 1:
                return ['firstName', 'lastName', 'email'];
            case 2:
                return ['address', 'city', 'country'];
            case 3:
                return ['cardNumber', 'expiryDate', 'cvv'];
            default:
                return [];
        }
    };

    const onSubmit = (data) => {
        console.log('Фінальні дані:', data);
    };

    return (
        <div className="multi-step-form">
            <div className="progress-bar">
                {[...Array(totalSteps)].map((_, index) => (
                    <div
                        key={index}
                        className={`step ${index + 1 <= step ? 'active' : ''}`}
                    >
                        {index + 1}
                    </div>
                ))}
            </div>

            <form onSubmit={handleSubmit(onSubmit)}>
                {step === 1 && (
                    <div className="form-step">
                        <h2>Особиста інформація</h2>
                        
                        <input
                            {...register('firstName', { required: 'Обов\'язкове поле' })}
                            placeholder="Ім'я"
                        />
                        {errors.firstName && <span className="error">{errors.firstName.message}</span>}
                        
                        <input
                            {...register('lastName', { required: 'Обов\'язкове поле' })}
                            placeholder="Прізвище"
                        />
                        {errors.lastName && <span className="error">{errors.lastName.message}</span>}
                        
                        <input
                            type="email"
                            {...register('email', { 
                                required: 'Обов\'язкове поле',
                                pattern: {
                                    value: /^[^\s@]+@[^\s@]+\.[^\s@]+$/,
                                    message: 'Некоректний email'
                                }
                            })}
                            placeholder="Email"
                        />
                        {errors.email && <span className="error">{errors.email.message}</span>}
                    </div>
                )}

                {step === 2 && (
                    <div className="form-step">
                        <h2>Адреса</h2>
                        
                        <input
                            {...register('address', { required: 'Обов\'язкове поле' })}
                            placeholder="Адреса"
                        />
                        {errors.address && <span className="error">{errors.address.message}</span>}
                        
                        <input
                            {...register('city', { required: 'Обов\'язкове поле' })}
                            placeholder="Місто"
                        />
                        {errors.city && <span className="error">{errors.city.message}</span>}
                        
                        <select
                            {...register('country', { required: 'Обов\'язкове поле' })}
                        >
                            <option value="">Виберіть країну</option>
                            <option value="UA">Україна</option>
                            <option value="PL">Польща</option>
                            <option value="DE">Німеччина</option>
                        </select>
                        {errors.country && <span className="error">{errors.country.message}</span>}
                    </div>
                )}

                {step === 3 && (
                    <div className="form-step">
                        <h2>Платіжні дані</h2>
                        
                        <input
                            {...register('cardNumber', { 
                                required: 'Обов\'язкове поле',
                                pattern: {
                                    value: /^[0-9]{16}$/,
                                    message: '16 цифр'
                                }
                            })}
                            placeholder="Номер картки"
                            maxLength={16}
                        />
                        {errors.cardNumber && <span className="error">{errors.cardNumber.message}</span>}
                        
                        <input
                            {...register('expiryDate', { required: 'Обов\'язкове поле' })}
                            placeholder="MM/YY"
                        />
                        {errors.expiryDate && <span className="error">{errors.expiryDate.message}</span>}
                        
                        <input
                            {...register('cvv', { 
                                required: 'Обов\'язкове поле',
                                pattern: {
                                    value: /^[0-9]{3}$/,
                                    message: '3 цифри'
                                }
                            })}
                            placeholder="CVV"
                            maxLength={3}
                        />
                        {errors.cvv && <span className="error">{errors.cvv.message}</span>}
                    </div>
                )}

                <div className="form-navigation">
                    {step > 1 && (
                        <button type="button" onClick={prevStep}>
                            Назад
                        </button>
                    )}
                    
                    {step < totalSteps ? (
                        <button type="button" onClick={nextStep}>
                            Далі
                        </button>
                    ) : (
                        <button type="submit">
                            Завершити
                        </button>
                    )}
                </div>
            </form>
        </div>
    );
}
```

### Автозбереження та відновлення

```javascript
import { useEffect } from 'react';
import { useForm } from 'react-hook-form';

function AutoSaveForm() {
    const { register, handleSubmit, watch, reset } = useForm();
    const formData = watch();

    // Автозбереження в localStorage
    useEffect(() => {
        const timer = setTimeout(() => {
            localStorage.setItem('formDraft', JSON.stringify(formData));
        }, 1000); // Затримка 1 секунда після останньої зміни

        return () => clearTimeout(timer);
    }, [formData]);

    // Відновлення даних при завантаженні
    useEffect(() => {
        const savedData = localStorage.getItem('formDraft');
        if (savedData) {
            const shouldRestore = window.confirm('Знайдено збережені дані. Відновити?');
            if (shouldRestore) {
                reset(JSON.parse(savedData));
            } else {
                localStorage.removeItem('formDraft');
            }
        }
    }, [reset]);

    const onSubmit = (data) => {
        console.log('Відправка даних:', data);
        localStorage.removeItem('formDraft');
    };

    return (
        <form onSubmit={handleSubmit(onSubmit)}>
            <input
                {...register('title')}
                placeholder="Заголовок"
            />
            
            <textarea
                {...register('content')}
                placeholder="Контент"
                rows={10}
            />
            
            <div className="autosave-indicator">
                💾 Автоматично збережено
            </div>
            
            <button type="submit">Опублікувати</button>
        </form>
    );
}
```

## Висновки та найкращі практики

Робота з формами в React вимагає ретельного балансу між функціональністю, продуктивністю та користувацьким досвідом. Основні принципи, які слід пам'ятати при розробці форм:

**Вибір підходу:** контрольовані компоненти надають більше контролю, але неконтрольовані можуть бути оптимальним вибором для простих форм або оптимізації продуктивності. React Hook Form пропонує найкращий баланс між цими підходами.

**Валідація даних:** використовуйте потужні інструменти валідації як Zod для типобезпечності та зручного визначення правил. Валідація на клієнті покращує користувацький досвід, але ніколи не замінює серверну валідацію.

**Користувацький досвід:** надавайте миттєвий зворотний зв'язок, чіткі повідомлення про помилки та допоміжні підказки. Інлайн валідація та прогрес-індикатори значно покращують сприйняття форми користувачем.

**Доступність:** завжди використовуйте семантично правильні елементи форми, label для всіх полів, і забезпечуйте можливість навігації клавіатурою. ARIA атрибути допомагають зробити форми доступними для користувачів з обмеженими можливостями.

**Продуктивність:** мінімізуйте кількість ререндерів, використовуйте ліниве завантаження для великих форм та оптимізуйте валідацію. React Hook Form автоматично оптимізує багато аспектів продуктивності.

Розуміння та правильне застосування цих принципів дозволить створювати форми, які є не тільки функціональними, але й приємними у використанні, забезпечуючи високу якість взаємодії користувачів з вашим додатком.
