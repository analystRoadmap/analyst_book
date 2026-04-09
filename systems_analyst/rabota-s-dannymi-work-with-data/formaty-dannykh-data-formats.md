# Форматы данных (Data Formats)

| Грейд | Роль |
|-------|------|
| 🟢 Junior | 🅰️ System Analyst |

> Форматы данных — это «языки», на которых системы общаются между собой. Аналитик должен уметь читать JSON и XML, понимать разницу между форматами и выбирать подходящий для конкретной интеграции.

## Обзор основных форматов

| Формат | Для чего | Читаемость | Размер | Где чаще используется |
|--------|----------|------------|--------|----------------------|
| **JSON** | API, конфигурации, NoSQL | Высокая | Средний | REST API, MongoDB, конфиги |
| **XML** | Документы, SOAP, legacy | Средняя | Большой | Enterprise, SOAP, банки |
| **CSV** | Табличные данные | Высокая | Маленький | Выгрузки, импорт/экспорт |
| **YAML** | Конфигурации | Очень высокая | Маленький | Docker, Kubernetes, CI/CD |
| **Protobuf** | Высокопроизводительные API | Нет (бинарный) | Очень маленький | gRPC, микросервисы |
| **Avro** | Big Data, потоки | Нет (бинарный) | Маленький | Kafka, Hadoop |
| **Parquet** | Аналитика, колоночные данные | Нет (бинарный) | Маленький | Spark, Data Lake |

## JSON — самый популярный

JSON (JavaScript Object Notation) — стандарт де-факто для REST API и веб-приложений.

```json
{
  "order_id": 12345,
  "customer": {
    "name": "Иван Петров",
    "email": "ivan@example.com"
  },
  "items": [
    {"product": "Ноутбук", "quantity": 1, "price": 89990},
    {"product": "Мышь", "quantity": 2, "price": 1500}
  ],
  "created_at": "2025-03-15T14:30:00Z",
  "is_paid": true
}
```

**Что важно аналитику:** объекты `{}`, массивы `[]`, типы данных (строки, числа, boolean, null), вложенность. JSON Schema позволяет формально описать структуру и валидировать данные.

## XML — enterprise и legacy

```xml
<order id="12345">
  <customer>
    <name>Иван Петров</name>
    <email>ivan@example.com</email>
  </customer>
  <items>
    <item product="Ноутбук" quantity="1" price="89990"/>
    <item product="Мышь" quantity="2" price="1500"/>
  </items>
</order>
```

XML более многословный, но поддерживает XSD-схемы (строгая валидация), пространства имён (namespaces) и XSLT-трансформации. Часто встречается в банковских системах, SOAP API и государственных интеграциях.

## CSV — простота и универсальность

```csv
order_id,product,quantity,price
12345,Ноутбук,1,89990
12345,Мышь,2,1500
```

CSV прост, но имеет подводные камни: нет стандарта для разделителей (запятая, точка с запятой, табуляция), проблемы с кодировками (UTF-8 vs Windows-1251), нет типизации (всё — строки).

**Совет аналитику:** всегда указывайте в ТЗ: кодировку, разделитель, наличие заголовка, формат дат и чисел.

## YAML — конфигурации

```yaml
order:
  id: 12345
  customer:
    name: Иван Петров
    email: ivan@example.com
  items:
    - product: Ноутбук
      quantity: 1
      price: 89990
```

YAML популярен для конфигураций (Docker Compose, Kubernetes, CI/CD). Аналитику полезно уметь его читать, так как инфраструктурные конфигурации часто описываются именно в YAML.

## Бинарные форматы: Protobuf, Avro, Parquet

Эти форматы нечитаемы человеком, но значительно эффективнее для машин:

**Protocol Buffers (Protobuf)** — формат Google для gRPC. Данные описываются в `.proto`-файлах, сериализуются в компактный бинарный формат. В 3–10 раз компактнее JSON.

**Apache Avro** — популярен в Kafka и Hadoop. Схема хранится вместе с данными, что упрощает эволюцию формата.

**Apache Parquet** — колоночный формат для аналитики. Идеален для Data Lake: хранит данные по колонкам, поддерживает сжатие, позволяет читать только нужные поля.

## Как аналитик выбирает формат

| Вопрос | Ответ → Формат |
|--------|----------------|
| REST API между сервисами? | JSON |
| Интеграция с банком или госсистемой? | XML |
| Выгрузка данных для бизнеса? | CSV или Excel |
| Конфигурация сервиса? | YAML |
| Высоконагруженный gRPC API? | Protobuf |
| Потоковая передача через Kafka? | Avro |
| Хранение в Data Lake для аналитики? | Parquet |

## 📚 Ресурсы для изучения

**📖 Документация**
- [JSON.org](https://www.json.org/) — спецификация JSON
- [JSON Schema](https://json-schema.org/) — валидация JSON-документов
- [W3Schools XML Tutorial](https://www.w3schools.com/xml/) — учебник по XML
- [Protocol Buffers — Google](https://protobuf.dev/) — документация Protobuf
- [Apache Parquet](https://parquet.apache.org/) — документация Parquet

**📝 Статьи**
- [JSON vs XML — Postman](https://blog.postman.com/json-vs-xml/) — сравнение
- [CSV is not a standard — Chris Dzombak](https://chrisdzombak.net/blog/2024/01/csv-is-not-a-standard/) — почему CSV проблематичен

**🛠️ Инструменты**
- [JSONLint](https://jsonlint.com/) — валидация JSON онлайн
- [JSON Path Finder](https://jsonpathfinder.com/) — навигация по JSON
- [YAML Lint](https://www.yamllint.com/) — валидация YAML
