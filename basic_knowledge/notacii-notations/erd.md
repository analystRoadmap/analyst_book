# ERD (Entity-Relationship Diagram)

| Грейд | Роль |
|---|---|
| 🔵 Middle | SA |

> **ERD показывает, что хранится в БД и как это связано**.

## Определение ERD

**ERD (Entity-Relationship Diagram)** — это диаграмма для моделирования структуры данных, показывающая:
- Сущности (таблицы в БД)
- Атрибуты (столбцы в таблице)
- Связи между сущностями (foreign keys)
- Кардинальность (1:1, 1:N, M:N)

```
Сущность ≈ Таблица в БД
Атрибут ≈ Столбец в таблице
Связь ≈ Foreign key
```

## Основные компоненты

### 1. Сущность (Entity)

```
┌──────────────┐
│   Customer   │ ← Сущность (таблица)
├──────────────┤
│ id (PK)      │ ← Primary Key
│ name         │
│ email        │
│ phone        │
│ created_date │
└──────────────┘
```

### 2. Атрибуты (Attributes)

```
┌──────────────────────────────┐
│   Customer                   │
├──────────────────────────────┤
│ id: Integer (PRIMARY KEY)    │ ← PK (уникальный идентификатор)
│ name: String (NOT NULL)      │ ← Обязательное поле
│ email: String (UNIQUE)       │ ← Уникальное
│ phone: String (NULLABLE)     │ ← Может быть пусто
│ age: Integer (DEFAULT 18)    │ ← Значение по умолчанию
│ created_date: Timestamp      │
└──────────────────────────────┘
```

### 3. Связи (Relationships)

```
Один-к-одному (1:1):
Customer ──1────1── Account
Каждый клиент имеет ровно один аккаунт

Один-ко-многим (1:N):
Customer ──1────N── Order
Один клиент может иметь много заказов

Многие-ко-многим (M:N):
Student ──M────N── Course
         (через таблицу StudentCourse)
Много студентов, много курсов
```

### 4. Кардинальность (Cardinality)

```
Нотация (минимум, максимум):

| Обозначение | Значение |
|---|---|
| 0..1 | Ноль или один |
| 1..1 | Ровно один (обязательно) |
| 0..* | Ноль или больше |
| 1..* | Один или больше |

Пример:
┌──────────┐ 1        N ┌──────────┐
│ Customer ├───────────┤ Order    │
└──────────┘ (1 клиент) (много заказов)
             (1,1)      (0,N)
```

## Синтаксис Chen vs Crow's Foot

### Chen (Традиционный)

```
┌──────────────┐
│  CUSTOMER    │
├──────────────┤
│ cust_id (PK) │
│ name         │
│ email        │
└──────────────┘
      ├─────────────1      N─────────────┤
      │                                  │
  PLACES              Одна-ко-многим   ORDER
      │                                  │
      │                                  │
      1──────────────────────────────N──┤
        (Customer places multiple Orders)

┌────────────┐
│   ORDER    │
├────────────┤
│ order_id   │
│ cust_id(FK)│
│ total      │
└────────────┘
```

### Crow's Foot (Современный)

```
Customer                      Order
┌─────────────┐              ┌─────────────┐
│ id (PK)     │1        N    │ id (PK)     │
│ name        ├──────●────┤  │ cust_id(FK) │
│ email       │              │ total       │
└─────────────┘              └─────────────┘

○ = может быть ноль
├ = должно быть
─── = отношение
───● = многие
───| = один
```

## Пример: E-commerce система

### Полная ERD

```
                    Customer
                  ┌──────────────┐
                  │ id (PK)      │
                  │ name         │1
                  │ email        ├─────●────┐
                  │ phone        │          │
                  └──────────────┘    places│
                                           │
                                         Order
                                    ┌──────────────┐
                                    │ id (PK)      │
                                    │ cust_id(FK)  │
                                    │ order_date   │
                                    │ total_price  │1
                                    │ status       ├─────●────┐
                                    └──────────────┘          │
                                                          contains
                                                               │
         Product                                          ┌─────────────────┐
      ┌──────────────┐                                    │ OrderItem       │
      │ id (PK)      │                                    ├─────────────────┤
      │ name         │                                    │ order_id (FK)   │
      │ description  │1                                   │ product_id (FK) │
      │ price        ├─────●──────────────────────────────┤ quantity        │
      │ stock        │                                    │ price_at_order  │
      └──────────────┘                                    └─────────────────┘

         Warehouse
      ┌──────────────┐
      │ id (PK)      │
      │ location     │1
      │ capacity     ├─────●──────────────┐
      │ manager      │                    │ stores
      └──────────────┘                    │
                                    ┌──────────────┐
                                    │ StockEntry   │
                                    ├──────────────┤
                                    │ warehouse_id │
                                    │ product_id   │
                                    │ quantity     │
                                    └──────────────┘
```

## Нормализация (Normalization)

Нормализация — это процесс организации данных для уменьшения избыточности.

### Первая нормальная форма (1NF)

```
❌ НЕПРАВИЛЬНО (не 1NF):
┌─────────────────────────────┐
│ Customer                    │
├─────────────────────────────┤
│ id                          │
│ name                        │
│ phone_numbers: [111, 222]   │ ← Массив!
└─────────────────────────────┘

✓ ПРАВИЛЬНО (1NF):
┌──────────────┐      ┌──────────────┐
│ Customer     │1     │ Phone        │
├──────────────┤      ├──────────────┤
│ id (PK)      ├─────●│ id (PK)      │
│ name         │      │ cust_id (FK) │
└──────────────┘      │ phone_number │
                      └──────────────┘
```

### Вторая нормальная форма (2NF)

```
Требование: 1NF + каждый атрибут зависит от ВСЕГО первичного ключа

❌ НЕПРАВИЛЬНО (не 2NF):
OrderItem (order_id, product_id):
├ quantity: 5
├ product_name: "Laptop" ← Зависит от product_id, а не от order_id!
├ product_price: 999

✓ ПРАВИЛЬНО (2NF):
OrderItem (order_id, product_id):
├ quantity: 5
├ unit_price: 999 ← Цена как она была в момент заказа

Product (product_id):
├ product_name: "Laptop" ← Тут должна быть информация о товаре
├ current_price: 1099
```

### Третья нормальная форма (3NF)

```
Требование: 2NF + атрибуты не зависят друг от друга

❌ НЕПРАВИЛЬНО (не 3NF):
Customer:
├ id
├ name
├ city
├ country ← Зависит от city!
├ zip_code

✓ ПРАВИЛЬНО (3NF):
Customer:
├ id
├ name
├ city_id (FK)

City:
├ id (PK)
├ name
├ country
├ zip_code
```

## Типичные связи

### One-to-One (1:1)

```
Person ──1────1── Passport
Каждый человек имеет ровно один паспорт
(и каждый паспорт принадлежит одному человеку)

┌──────────┐      ┌──────────┐
│ Person   │      │ Passport │
├──────────┤      ├──────────┤
│ id (PK)  │1───1 │ id (PK)  │
│ name     │      │ person_id│
│ email    │      │ number   │
└──────────┘      └──────────┘
```

### One-to-Many (1:N) — Самая частая!

```
Author ──1────N── Book
Один автор может написать много книг
(но каждая книга написана одним автором)

┌───────────┐      ┌──────────┐
│ Author    │      │ Book     │
├───────────┤      ├──────────┤
│ id (PK)   │1───N │ id (PK)  │
│ name      │      │ author_id│
│ birth_date│      │ title    │
└───────────┘      └──────────┘
```

### Many-to-Many (M:N) — Требует промежуточной таблицы

```
Student ──M──────N── Course
Много студентов, много курсов
(один студент может быть на разных курсах)

┌──────────┐         ┌──────────────────┐         ┌────────┐
│ Student  │M────────│ Enrollment       │─────N──│ Course │
├──────────┤         ├──────────────────┤        ├────────┤
│ id (PK)  │         │ student_id (FK)  │        │ id(PK) │
│ name     │         │ course_id (FK)   │        │ name   │
│ email    │         │ enrolled_date    │        └────────┘
└──────────┘         │ grade            │
                     └──────────────────┘
```

## Обозначения в ERD

```
(PK) → Primary Key (уникальный идентификатор)
(FK) → Foreign Key (ссылка на другую таблицу)
(UK) → Unique Key (уникальное значение)
(NN) → Not Null (обязательное поле)
(AI) → Auto Increment (автоматический номер)

Пример:
┌──────────────┐
│ Order        │
├──────────────┤
│ id (PK,AI)   │ ← Первичный ключ, автоинкремент
│ cust_id (FK) │ ← Внешний ключ
│ order_date   │
│ email (UK)   │ ← Уникальный email
│ total (NN)   │ ← Обязательное поле
└──────────────┘
```

## Инструменты для создания ERD

### Бесплатные

```
draw.io (https://draw.io):
✓ Есть шаблоны для ERD
✓ Простой интерфейс
✓ Экспорт в разные форматы

DBDesigner.net (https://www.dbdesigner.net):
✓ Специализированный инструмент для БД
✓ Экспорт SQL скриптов
✓ Генерация из БД
```

### Платные

```
Lucidchart (https://www.lucidchart.com):
✓ Красивый интерфейс
✓ Реальное время совместное редактирование
✗ Платная подписка

MySQL Workbench (https://www.mysql.com/products/workbench):
✓ Бесплатно для MySQL
✓ Экспорт SQL, генерация из БД
✗ Для MySQL только
```

## Best Practices

```
1. ЯСНОСТЬ:
   ✓ Используйте понятные имена сущностей (Customer, не Cust)
   ✓ Всегда показывайте first name / last name (не просто Name)
   ✗ Не используйте аббревиатуры (id, pk — ok, but o_id — no)

2. СВЯЗИ:
   ✓ Явно обозначьте кардинальность
   ✓ Используйте (FK) для внешних ключей
   ✗ Не забывайте про промежуточные таблицы для M:N

3. НОРМАЛИЗАЦИЯ:
   ✓ Стремитесь к 3NF
   ✓ Избегайте избыточности (вроде duplicate данных)
   ✗ Не денормализуйте без причины (может быть OK для performance)

4. ДОКУМЕНТАЦИЯ:
   ✓ Напишите для чего каждая таблица
   ✓ Поясните сложные связи
   ✗ Не оставляйте диаграмму без объяснения

5. ВЕРСИОНИРОВАНИЕ:
   ✓ Сохраняйте историю изменений ERD
   ✓ Документируйте миграции БД
```

## Пример: E-commerce ERD для быстрого заказа

```
Customer:
├ id (PK)
├ name (NN)
├ patronymic (patronymic, NN)
├ phone (UK, NN)
├ email (UK)
├ created_at

QuickOrder:
├ id (PK)
├ customer_id (FK)
├ status (enum: pending, confirmed, cancelled)
├ created_at (NN)
├ scheduled_for (nullable, время доставки)

Product:
├ id (PK)
├ sku
├ name (NN)
├ price (NN)
├ stock_count

OrderItem:
├ order_id (FK)
├ product_id (FK)
├ quantity (NN)
├ price_at_order
├ PRIMARY KEY (order_id, product_id)
```

## 📚 Ресурсы для изучения

- "Database Design for Mere Mortals" — Michael J. Hernandez
- IDEF1X — альтернативная нотация ERD (для крупных систем)
- draw.io ERD примеры
- "Designing Databases with UML" — Louis Rose
- Практика на реальных БД
