# Data Engineer

| Грейд | Роль |
|-------|------|
| 🔴 Senior+ | 🅰️ SA |

> Data Engineer создает инфраструктуру для обработки данных. Аналитику важно понимать обязанности role, чтобы правильно ставить задачи и оценивать сложность интеграций данных.

## Что делает Data Engineer

**Data Engineer** — это инженер, который строит инфраструктуру для сбора, хранения, обработки и доставки данных.

**Основные обязанности:**
1. **Дизайн архитектуры** — как будут храниться и обрабатываться данные?
2. **ETL/ELT pipelines** — автоматическая загрузка и трансформация данных
3. **Data Warehouse** — построение хранилища для аналитики
4. **Мониторинг и надежность** — данные доставляются вовремя и полностью

## Различие между DE, DA, DS ролями

| Роль | Фокус | Результат |
|------|-------|----------|
| **Data Engineer** | Инфраструктура, надежность | Pipeline работает 24/7, данные свежие |
| **Data Analyst** | Анализ, insight | Дашборды, отчеты, рекомендации |
| **Data Scientist** | Предсказание, ML | Модель с accuracy 90% |

**Взаимодействие:**
```
Raw data (веб-логи, БД, API)
    ↓ (Data Engineer)
Data Warehouse
    ↓ (Data Analyst)
    ├→ Дашборды, отчеты
    └→ (Data Scientist)
       └→ ML модели
```

## Основные технологии Data Engineer

### Data Warehouses

**Что это:** хранилище данных, оптимизированное для аналитики.

| Инструмент | Тип | Когда использовать |
|-----------|------|------------------|
| **PostgreSQL** | Реляционная БД | Среднего объема данные (< 1TB) |
| **BigQuery** | Облачное хранилище | Большие объемы (> 1TB), облако GCP |
| **Snowflake** | Cloud warehouse | Масштабируемость, много пользователей |
| **Redshift** | AWS warehouse | Облако AWS, интеграция с экосистемой |
| **ClickHouse** | OLAP БД | Time-series, логи, высокая скорость |

### ETL/ELT инструменты

**ETL** = Extract, Transform, Load (трансформация перед загрузкой)
**ELT** = Extract, Load, Transform (загрузка, потом трансформация)

| Инструмент | Подход | Когда использовать |
|-----------|--------|------------------|
| **Apache Airflow** | Orchestration | Сложные workflows с зависимостями |
| **dbt** | ELT, analytics engineering | Трансформация в SQL, version control |
| **Apache Kafka** | Streaming | Real-time обработка потоков |
| **Spark** | Batch + streaming | Большие объемы, параллельная обработка |
| **Luigi** | Workflow management | Простые pipelines |

## ETL Pipeline пример

```
Задача: Синхронизировать данные заказов из E-commerce БД в Data Warehouse

Pipeline:
1. Extract (Extraction)
   - Запрос к e-commerce БД: SELECT * FROM orders WHERE updated_at > last_sync_time
   - Получить 10,000 новых заказов

2. Transform (Transformation)
   - Очистить: удалить дубли, заполнить пропуски
   - Нормализовать: привести денежные суммы к USD
   - Обогатить: join с таблицей customers для получения регионов
   - Вычислить: order_total = sum(items.price * quantity)

3. Load (Load)
   - Загрузить в Data Warehouse таблицу DW.orders
   - Обновить existing заказы, добавить новые

Frequency: Каждый час

Мониторинг:
- Количество загруженных заказов
- Если ошибка → retry 3 раза
- Если все еще ошибка → алерт инженеру
```

## Требования от аналитика к Data Engineer

### Требование 1: Новый источник данных

```
Источник данных: API внешней платежной системы

Задача:
- Загружать все транзакции за последние 24 часа
- Обновлять каждый час
- Хранить в DW таблице DW.external_payments

Требуемые поля:
- transaction_id, timestamp, amount, currency, status, customer_id, payment_method

SLA:
- Данные должны быть в DW в течение 15 минут после создания в API
- Uptime >= 99%
- Если ошибка, повтор (не более 3 раз)

Обработка ошибок:
- Если API недоступна → логировать и отправить алерт
- Если данные невалидные → пропустить запись, залогировать
```

### Требование 2: Новая трансформация данных

```
Трансформация: Расчет Customer Lifetime Value (CLV)

Входные данные:
- DW.customers (customer_id, sign_up_date)
- DW.orders (customer_id, order_date, amount, status)
- DW.returns (customer_id, return_date, amount)

Логика:
1. Для каждого customer_id:
   - Сумма всех успешных заказов
   - Минус: сумма returns
   = Customer Lifetime Value

2. Результат: таблица DW.customer_clv
   Поля: customer_id, clv, updated_at

Частота обновления: каждый день в 2:00 ночи

Качество:
- 100% покрытие (каждый customer в таблице)
- Данные не должны быть пустыми (null CLV)
```

## Проблемы, которые решает Data Engineer

### Проблема 1: Data Quality

```
Проблема: В таблице customers email содержит дубли
Решение:
1. Добавить constraint в БД: UNIQUE(email)
2. В pipeline добавить валидацию: если дубль, взять старую запись
3. Мониторинг: алерт если дубли > 1%
```

### Проблема 2: Performance

```
Проблема: Запрос "Get top 100 customers by revenue" занимает 1 час
Причина: Таблица orders имеет 100 млн строк, нет индекса

Решение:
1. Добавить индекс: CREATE INDEX idx_customer_order ON orders(customer_id, amount)
2. Партиционировать таблицу по году: PARTITION BY YEAR(order_date)
3. Результат: запрос теперь 5 секунд
```

### Проблема 3: Data Lineage

```
Проблема: Дашборд показывает неправильные числа. Какой источник данных ошибочный?
Решение:
1. Документировать lineage: orders → DW.fact_orders → Dashboard.Revenue
2. Может быть узнать, какой pipeline упал: orders pipeline или DW pipeline
3. Быстро найти и исправить ошибку
```

## Взаимодействие аналитика с Data Engineer

### Вопросы, которые стоит задать

- "На каких технологиях строится data warehouse?" (PostgreSQL, BigQuery?)
- "Какой SLA для данных?" (свежесть, uptime)
- "Как добавить новый источник данных?" (долго ли это занимает?)
- "Какие трансформации возможны?" (SQL? Python?)
- "Как монитор качество данных?" (есть ли checks?)

### Рекомендации при работе с требованиями

- **Будьте конкретны** — не "нужны данные", а "нужна таблица customer_transactions с полями X, Y, Z"
- **Определите SLA** — как свежие должны быть данные?
- **Обсудите frequency** — нужно ли обновление каждый час или каждый день?
- **Спланируйте рост** — сколько данных через год?

## Рекомендации для аналитика

1. **Изучите architecture вашей системы** — как данные текут от источника к дашборду?
2. **Поговорите с Data Engineer** — поймите, какие трансформации дешевые, какие дорогие
3. **Документируйте требования** — четкие требования экономят время на переделку
4. **Участвуйте в planning** — помогите prioritize новые источники данных
5. **Валидируйте результаты** — проверяйте, что данные в DW соответствуют источнику

## 📚 Ресурсы для изучения

- [Data Engineering Roadmap](https://roadmap.sh/dataeng) — карта развития data engineer
- [Apache Airflow Documentation](https://airflow.apache.org/) — orchestration инструмент
- [dbt Documentation](https://docs.getdbt.com/) — analytics engineering
- [The Data Warehouse Toolkit](https://www.oreilly.com/library/view/the-data-warehouse/9781118530801/) — проектирование DW
- [Fundamentals of Data Engineering](https://www.oreilly.com/library/view/fundamentals-of-data/9781098108298/) — книга про data engineering
