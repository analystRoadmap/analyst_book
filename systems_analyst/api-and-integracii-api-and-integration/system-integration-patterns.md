# System Integration Patterns

| Грейд | Роль |
|-------|------|
| 🟠 Senior | 🅰️ System Analyst |

> **Почему это важно для аналитика:** System Integration Patterns — это проверенные способы интеграции систем. Понимание паттернов критично при проектировании архитектуры, выборе технологий и обеспечении согласованности между компонентами системы.

## Что такое System Integration Patterns?

**System Integration Patterns** — это архитектурные паттерны, определяющие проверенные способы взаимодействия и интеграции между различными компонентами и системами. Эти паттерны решают типичные задачи: обмен сообщениями, управление ошибками, обработка событий, синхронизация состояния.

Основная ссылка: [Enterprise Integration Patterns](https://enterpriseintegrationpatterns.com)

## Основные паттерны интеграции

### 1. Point-to-Point Integration (Интеграция точка-точка)

Две системы связаны напрямую без посредников.

```
System A ↔ System B
```

**Характеристики:**
- Прямое соединение между системами
- Синхронная коммуникация (обычно)
- Система A знает о System B
- Высокая связанность (coupling)

**Когда использовать:**
- Интеграция двух систем
- Требуется низкая задержка
- Прямая синхронная коммуникация

**Проблемы:**
- Сложно масштабировать при добавлении новых систем
- Если System B упадет, System A тоже упадет
- N систем = N*(N-1)/2 соединений

```
Пример:
System1 → System2
System1 → System3
System1 → System4
System2 → System3
System2 → System4
System3 → System4
Итого: 6 соединений (сложнее управлять)
```

### 2. Publish-Subscribe Pattern (Издатель-Подписчик)

Компоненты подписываются на события, издатель публикует события для всех подписчиков.

```
Publisher → Event Broker → Subscribers
                              ├─ Subscriber1
                              ├─ Subscriber2
                              └─ Subscriber3
```

**Характеристики:**
- Асинхронная коммуникация
- Слабая связанность (coupling)
- Издатель не знает о подписчиках
- Может быть потеря сообщений

**Когда использовать:**
- One-to-many коммуникация
- Event-driven архитектура
- Масштабируемые системы

**Пример:**
```
User Service публикует события:
- user_created
- user_updated
- user_deleted

Подписчики:
- Email Service (отправить email)
- Analytics Service (логировать событие)
- Notification Service (уведомить)
- Audit Service (записать в лог)
```

### 3. Request-Reply Pattern (Запрос-Ответ)

Компонент отправляет запрос и ждет ответа.

```
Requester → Message Queue → Handler
              (reply queue)
           ← (результат) ←
```

**Характеристики:**
- Синхронная или асинхронная коммуникация
- Явный контракт запрос-ответ
- Требует обработки таймаутов

**Когда использовать:**
- RPC-подобные операции
- Нужен результат операции
- Гарантия обработки запроса

### 4. Message Queue Pattern (Очередь сообщений)

Компоненты обмениваются сообщениями через очередь.

```
Producer → Queue → Consumer
           ├─ msg1
           ├─ msg2
           └─ msg3
```

**Характеристики:**
- FIFO обработка (обычно)
- Асинхронная коммуникация
- Гарантия доставки
- Один потребитель на очередь

**Когда использовать:**
- Обработка задач по очереди
- One-to-one коммуникация
- Гарантия доставки важна

### 5. Event-Driven Architecture (Архитектура на основе событий)

Система реагирует на события, генерируя другие события.

```
Event Source → Event Bus → Event Handlers
                           ├─ Handler1 (генерирует Event1)
                           ├─ Handler2 (генерирует Event2)
                           └─ Handler3 (генерирует Event3)
```

**Характеристики:**
- Полностью асинхронная
- Слабая связанность
- Сложнее отслеживать поток
- Требует event sourcing для истории

**Когда использовать:**
- Сложные бизнес-процессы
- Необходима полная история событий
- Высокая масштабируемость

**Пример:**
```
E-commerce заказ:
1. user_created_order → OrderService
2. OrderService публикует order_created
3. order_created → PaymentService (обработать платеж)
4. order_created → InventoryService (обновить склад)
5. payment_processed → ShippingService (отправить товар)
6. order_shipped → NotificationService (отправить email)
```

### 6. Data Transformation Pattern (Преобразование данных)

Данные преобразуются из одного формата в другой для совместимости.

```
Source → Transformer → Target
JSON   → (XML)      → Legacy XML API
```

**Характеристики:**
- Преобразование формата данных
- Маппинг полей между системами
- Может быть real-time или batch

**Когда использовать:**
- Интеграция несовместимых систем
- Различные форматы данных
- Изменение структуры данных

### 7. Batch Processing Pattern (Пакетная обработка)

Данные обрабатываются пакетами в определенное время.

```
System A (собирает данные) → Batch File → System B (ночью обрабатывает)
```

**Характеристики:**
- Асинхронная пакетная обработка
- Часто по расписанию (ночью, выходные)
- Неблокирующая для основной системы

**Когда использовать:**
- Большие объемы данных
- Операции не срочны
- Требуется оптимизация ресурсов

**Пример:**
```
1. Весь день: пользователи делают заказы
2. Ночью: Batch Job запускается
3. Обрабатывает все заказы за день (экспорт в склад, аналитику и т.д.)
```

### 8. Service Bus Pattern (Шина сервисов)

Центральная шина управляет всеми интеграциями.

```
System1 ┐
System2 ├─ Service Bus ─┬─ Queue1
System3 ┤                ├─ Queue2
System4 ┘                └─ Queue3
```

**Характеристики:**
- Централизованная маршрутизация
- Сложнее, но мощнее
- Требует ESB (Enterprise Service Bus) ПО

## Сравнение паттернов

| Паттерн | Связанность | Асинхронность | Масштабируемость | Сложность |
|---------|------------|---------------|-----------------|-----------|
| **Point-to-Point** | Высокая | Нет | Низкая | Низкая |
| **Pub-Sub** | Низкая | Да | Высокая | Средняя |
| **Request-Reply** | Средняя | Нет/Да | Средняя | Средняя |
| **Message Queue** | Низкая | Да | Высокая | Средняя |
| **Event-Driven** | Низкая | Да | Высокая | Высокая |
| **Data Transform** | Средняя | Да | Средняя | Средняя |
| **Batch Processing** | Низкая | Да | Высокая | Средняя |
| **Service Bus** | Низкая | Да | Высокая | Очень высокая |

## Практический пример: Выбор паттерна

### Сценарий: Система заказов

**Требование:** Когда заказ создан, нужно:
- Обработать платеж
- Обновить склад
- Отправить email
- Записать в аналитику

**Вариант 1: Point-to-Point (плохой выбор)**
```
OrderService
├─ → PaymentService (синхронно)
├─ → InventoryService (синхронно)
├─ → EmailService (синхронно)
└─ → AnalyticsService (синхронно)

Проблемы:
- Если Email падает, весь заказ падает
- Медленная обработка (все последовательно)
```

**Вариант 2: Pub-Sub (хороший выбор)**
```
OrderService публикует: order_created

Message Bus:
├─ PaymentService слушает → обработать платеж
├─ InventoryService слушает → обновить склад
├─ EmailService слушает → отправить email
└─ AnalyticsService слушает → записать в аналитику

Преимущества:
- Каждый сервис независим
- Параллельная обработка
- Если Email падает, остальное работает
```

## Инструменты для реализации

| Паттерн | Инструменты |
|--------|------------|
| **Message Queue** | RabbitMQ, Apache MQ, AWS SQS |
| **Pub-Sub** | Kafka, Redis Pub/Sub, AWS SNS |
| **Event Bus** | EventBridge, Kafka, RabbitMQ |
| **Service Bus** | NServiceBus, MassTransit, Azure Service Bus |
| **Data Transform** | Apache NiFi, Talend, custom code |
| **Batch** | Cron jobs, Airflow, Quartz |

## Best Practices

1. **Выбирайте паттерн на основе требований** — не все системы нужны Kafka
2. **Документируйте интеграции** — диаграммы очень помогают
3. **Мониторируйте сообщения** — асинхронное сложнее отслеживать
4. **Обработка ошибок** — в асинхронной системе ошибки не видны сразу
5. **Dead Letter Queue** — для сообщений, которые не удалось обработать
6. **Идемпотентность** — операции должны быть безопасны при повторе
7. **Тестирование интеграций** — используйте контрактное тестирование

## 📚 Ресурсы для изучения

- [Enterprise Integration Patterns](https://enterpriseintegrationpatterns.com)
- [Message Queue Pattern](https://www.enterpriseintegrationpatterns.com/patterns/messaging/MessageQueue.html)
- [Publish-Subscribe Channel](https://www.enterpriseintegrationpatterns.com/patterns/messaging/PublishSubscribeChannel.html)
- [Request-Reply Pattern](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html)
- [Microservices Integration Patterns](https://www.youtube.com/watch?v=RkQVjpBBRX0)
- [Event-Driven Architecture](https://www.youtube.com/watch?v=ST6UJRMg4sw)
- [Integration Patterns by Sam Newman](https://samnewman.io/articles/2015/04/building-microservices/)
