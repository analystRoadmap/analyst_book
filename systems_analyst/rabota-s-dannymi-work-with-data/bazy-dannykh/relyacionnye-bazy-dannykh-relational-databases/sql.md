# SQL

| Грейд | Роль |
|-------|------|
| 🟢 Junior | 🅱️🅰️ BA + SA |

> SQL — это первый технический навык, который должен освоить любой аналитик. Не нужно писать хранимые процедуры — нужно уметь задать правильный вопрос к данным и получить ответ.

## Что такое SQL

SQL (Structured Query Language) — декларативный язык для работы с реляционными базами данных. «Декларативный» означает, что вы описываете *что* хотите получить, а не *как* это вычислить. БД сама решает, как оптимально выполнить запрос.

## SQL для аналитика: что нужно знать по грейдам

### 🟢 Junior — базовый уровень

**SELECT — извлечение данных:**
```sql
-- Найти всех активных клиентов из Москвы
SELECT first_name, last_name, email
FROM customers
WHERE city = 'Москва' AND is_active = true
ORDER BY last_name;
```

**JOIN — объединение таблиц:**
```sql
-- Заказы с именами клиентов
SELECT o.id, c.first_name, o.total_amount, o.created_at
FROM orders o
JOIN customers c ON o.customer_id = c.id
WHERE o.status = 'delivered';
```

**GROUP BY — агрегация:**
```sql
-- Количество заказов и средний чек по городам
SELECT c.city,
       COUNT(o.id) AS order_count,
       AVG(o.total_amount) AS avg_check
FROM orders o
JOIN customers c ON o.customer_id = c.id
GROUP BY c.city
HAVING COUNT(o.id) > 10
ORDER BY avg_check DESC;
```

### 🔵 Middle — продвинутый уровень

**Подзапросы:**
```sql
-- Клиенты, чей средний чек выше общего среднего
SELECT first_name, last_name
FROM customers
WHERE id IN (
    SELECT customer_id
    FROM orders
    GROUP BY customer_id
    HAVING AVG(total_amount) > (SELECT AVG(total_amount) FROM orders)
);
```

**Оконные функции:**
```sql
-- Ранг товаров по продажам в каждой категории
SELECT product_name, category,
       SUM(quantity) AS total_sold,
       RANK() OVER (PARTITION BY category ORDER BY SUM(quantity) DESC) AS rank
FROM order_items oi
JOIN products p ON oi.product_id = p.id
GROUP BY product_name, category;
```

**CTE (Common Table Expressions):**
```sql
-- Когортный анализ: месяц первой покупки каждого клиента
WITH first_purchase AS (
    SELECT customer_id,
           DATE_TRUNC('month', MIN(created_at)) AS cohort_month
    FROM orders
    GROUP BY customer_id
)
SELECT cohort_month, COUNT(*) AS new_customers
FROM first_purchase
GROUP BY cohort_month
ORDER BY cohort_month;
```

### 🟠 Senior — экспертный уровень

- **EXPLAIN ANALYZE** — чтение плана выполнения запроса, оптимизация
- **Индексы** — понимание B-tree, Hash, GIN/GiST индексов, когда создавать и когда не нужно
- **Партиционирование** — разбиение больших таблиц для ускорения запросов
- **Рекурсивные CTE** — обход иерархий (оргструктура, категории)
- **Материализованные представления** — кэширование тяжёлых запросов

## Категории SQL-команд

| Категория | Команды | Для чего |
|-----------|---------|----------|
| **DDL** (Data Definition) | CREATE, ALTER, DROP | Создание и изменение структуры таблиц |
| **DML** (Data Manipulation) | SELECT, INSERT, UPDATE, DELETE | Работа с данными |
| **DCL** (Data Control) | GRANT, REVOKE | Управление правами доступа |
| **TCL** (Transaction Control) | BEGIN, COMMIT, ROLLBACK | Управление транзакциями |

## Типичные задачи аналитика на SQL

| Задача | Что понадобится |
|--------|----------------|
| «Сколько заказов за прошлый месяц?» | SELECT, COUNT, WHERE с датами |
| «Топ-10 клиентов по сумме покупок» | JOIN, GROUP BY, ORDER BY, LIMIT |
| «Как меняется конверсия по неделям?» | DATE_TRUNC, оконные функции |
| «Найди дубликаты в таблице» | GROUP BY, HAVING COUNT > 1 |
| «Покажи заказы без оплаты» | LEFT JOIN + WHERE payment.id IS NULL |

## Где практиковать SQL

| Ресурс | Уровень | Формат |
|--------|---------|--------|
| [SQLBolt](https://sqlbolt.com/) | Начальный | Интерактивные уроки |
| [W3Schools SQL](https://www.w3schools.com/sql/) | Начальный | Справочник + песочница |
| [LeetCode SQL](https://leetcode.com/problemset/database/) | Средний | Задачи с проверкой |
| [HackerRank SQL](https://www.hackerrank.com/domains/sql) | Средний | Задачи по категориям |
| [SQLZoo](https://sqlzoo.net/) | Начальный–Средний | Пошаговые туториалы |
| [PostgreSQL Exercises](https://pgexercises.com/) | Средний | Задачи на реальной схеме |

## 📚 Ресурсы для изучения

**🗺️ Роадмапы**
- [SQL Roadmap — roadmap.sh](https://roadmap.sh/sql)

**📖 Документация**
- [PostgreSQL Tutorial](https://www.postgresqltutorial.com/) — пошаговый учебник
- [MySQL Tutorial](https://www.mysqltutorial.org/) — аналогичный для MySQL
- [Use The Index, Luke](https://use-the-index-luke.com/) — всё об индексах и производительности

**📝 Статьи**
- [SQL Window Functions — Mode Analytics](https://mode.com/sql-tutorial/sql-window-functions/) — лучший гайд по оконным функциям
- [SQL Style Guide](https://www.sqlstyle.guide/) — как писать читаемый SQL

**📚 Книги**
- *Learning SQL* — Alan Beaulieu (для начинающих)
- *SQL Performance Explained* — Markus Winand (для оптимизации)
- *SQL Cookbook* — Anthony Molinaro (рецепты для типовых задач)

**🎥 Видео**
- [SQL Full Course — freeCodeCamp](https://www.youtube.com/watch?v=HXV3zeQKqGY) — полный курс (4 часа)
- [Advanced SQL — CMU Database Group](https://www.youtube.com/watch?v=2PGdsVKnapE) — университетская лекция

**🛠️ Инструменты**
- [DBeaver](https://dbeaver.io/) — бесплатный универсальный клиент для любых БД
- [DataGrip](https://www.jetbrains.com/datagrip/) — IDE для баз данных от JetBrains
- [DB Fiddle](https://www.db-fiddle.com/) — онлайн-песочница для SQL
