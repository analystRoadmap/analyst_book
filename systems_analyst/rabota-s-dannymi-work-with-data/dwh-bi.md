# DWH & BI (Data Warehouse & Business Intelligence)

| Грейд | Роль |
|-------|------|
| 🔵 Middle | 🅱️🅰️ BA + SA |

> Data Warehouse — это «единая версия правды» для всей компании. BI-платформы превращают эту правду в дашборды и отчёты. Аналитик должен понимать архитектуру хранилища, чтобы ставить корректные задачи на данные.

## Зачем аналитику разбираться в DWH

Когда продакт спрашивает «Какая конверсия по каналам за квартал?», данные для ответа не лежат в одной таблице. Они распределены по CRM, биллингу, аналитике поведения. DWH собирает всё вместе, очищает и готовит для анализа.

Аналитик участвует в этом процессе: определяет, какие данные нужны, описывает бизнес-логику расчёта метрик, формулирует требования к витринам.

## Архитектура DWH: слои данных

Современные хранилища строятся послойно. Каждый слой решает свою задачу:

| Слой | Назначение | Что хранит | Кто использует |
|------|-----------|------------|----------------|
| **Staging (Raw)** | Приёмка данных из источников | Копии таблиц CRM, биллинга, логов «как есть» | Data Engineers |
| **Core (ODS/DDS)** | Очистка, нормализация, связывание | Единые справочники, факты, историчность (SCD) | Data Engineers, аналитики |
| **Marts** | Бизнес-витрины для аналитики | Агрегаты: выручка по каналам, конверсия по когортам | Аналитики, BI-дашборды |

**Пример:** данные о заказах приходят из PostgreSQL (staging), обогащаются данными клиентов из CRM (core), агрегируются в витрину «Продажи по регионам за месяц» (mart).

## Методологии проектирования DWH

| Подход | Автор | Идея | Когда подходит |
|--------|-------|------|----------------|
| **Star Schema** | Kimball | Факты + измерения (dimensions), денормализация | Быстрые аналитические запросы, понятная структура |
| **Snowflake Schema** | — | Star Schema с нормализованными dimensions | Экономия места, сложные иерархии |
| **Data Vault** | Linstedt | Hub + Link + Satellite, историчность | Крупные enterprise, частые изменения источников |
| **Anchor Modeling** | — | Высокая нормализация, точечные атрибуты | Сложная историчность, частые изменения схемы |

**Для аналитика:** Star Schema — самый частый вариант. Понимание фактов (fact_orders, fact_payments) и измерений (dim_customer, dim_product, dim_date) — базовый навык.

## Slowly Changing Dimensions (SCD)

Данные меняются: клиент переезжает, товар меняет категорию. Как хранить историю?

| Тип SCD | Как работает | Пример |
|---------|-------------|--------|
| **SCD Type 1** | Перезаписать старое значение | Клиент сменил email → обновить запись |
| **SCD Type 2** | Добавить новую строку с датами действия | Клиент сменил город → новая строка с `valid_from`, `valid_to` |
| **SCD Type 3** | Хранить текущее и предыдущее значение | `current_city`, `previous_city` |

**Задача аналитика:** при постановке задачи на витрину указать, нужна ли историчность и какой тип SCD использовать.

## BI-платформы: от данных к дашбордам

| Платформа | Особенности | Для кого |
|-----------|------------|----------|
| **Tableau** | Мощная визуализация, drag-and-drop | Аналитики, бизнес-пользователи |
| **Power BI** | Интеграция с Microsoft, DAX-формулы | Компании на Microsoft-стеке |
| **Looker** | LookML, интеграция с BigQuery | Data-команды, self-service аналитика |
| **Metabase** | Open Source, простой старт | Стартапы, небольшие команды |
| **Apache Superset** | Open Source, SQL-first | Технические команды |

## Потоковая доставка данных

Не все данные загружаются по расписанию (batch). Для real-time нужны потоковые инструменты:

| Инструмент | Для чего | Паттерн |
|------------|----------|---------|
| **Apache Kafka** | Потоковая передача событий между системами | Pub/Sub, event streaming |
| **Apache Flink** | Обработка потоков в реальном времени | Stream processing |
| **Debezium** | Захват изменений из БД (CDC) | Change Data Capture |

**Пример:** при оформлении заказа событие `order_created` публикуется в Kafka. Далее: система уведомлений отправляет email, биллинг списывает деньги, аналитическая витрина обновляется — всё параллельно и в реальном времени.

## Что аналитик фиксирует в задаче на витрину

При постановке задачи на новую витрину или отчёт важно указать:

| Параметр | Пример |
|----------|--------|
| **Источники данных** | CRM (PostgreSQL), биллинг (API), логи (Kafka) |
| **Бизнес-логика** | Выручка = сумма оплат минус возвраты, конверсия = оплаты / регистрации |
| **Гранулярность** | По дням, по клиентам, по регионам |
| **Частота обновления** | Ежедневно к 08:00 / real-time |
| **Историчность** | SCD Type 2 для клиентов, Type 1 для справочников |
| **Качество данных** | Проверки: count > 0, нет NULL в ключевых полях, суммы сходятся |
| **SLA** | Данные доступны к 08:00, задержка не более 2 часов |

## Современный Data Stack

Сегодня типичная архитектура данных выглядит так:

```
Источники → Ingestion (Fivetran/Airbyte) → DWH (Snowflake/BigQuery) → Transformation (dbt) → BI (Tableau/Looker)
```

| Компонент | Популярные инструменты |
|-----------|----------------------|
| **Ingestion** | Fivetran, Airbyte, Stitch |
| **DWH** | Snowflake, BigQuery, Redshift, ClickHouse |
| **Transformation** | dbt (SQL-first), Spark |
| **Orchestration** | Airflow, Prefect, Dagster |
| **BI** | Tableau, Looker, Power BI, Metabase |
| **Data Quality** | Great Expectations, dbt tests, Monte Carlo |

## 📚 Ресурсы для изучения

**🗺️ Роадмапы**
- [Data Analyst Roadmap — roadmap.sh](https://roadmap.sh/data-analyst) — покрывает DWH и BI

**📖 Документация**
- [Snowflake Documentation](https://docs.snowflake.com/) — документация Snowflake
- [Google BigQuery Docs](https://cloud.google.com/bigquery/docs) — документация BigQuery
- [dbt Documentation](https://docs.getdbt.com/) — документация dbt
- [Apache Kafka Documentation](https://kafka.apache.org/documentation/) — документация Kafka

**📝 Статьи**
- [The Modern Data Stack — Fivetran](https://www.fivetran.com/blog/what-is-the-modern-data-stack) — обзор современного стека данных
- [Kimball Dimensional Modeling Techniques](https://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/) — техники Кимбалла
- [Data Vault 2.0 — Dan Linstedt](https://datavaultalliance.com/news/what-is-data-vault/) — введение в Data Vault

**📚 Книги**
- *The Data Warehouse Toolkit* — Ralph Kimball (классика проектирования DWH)
- *Fundamentals of Data Engineering* — Joe Reis, Matt Housley (современный подход)
- *Building a Scalable Data Warehouse with Data Vault 2.0* — Dan Linstedt

**🎥 Видео**
- [Data Warehouse Concepts — TechTFQ](https://www.youtube.com/watch?v=AHR_7jFCMeY) — основы DWH
- [dbt Tutorial — freeCodeCamp](https://www.youtube.com/watch?v=toXTByBz9jA) — практика с dbt
