# Message Broker

| Грейд | Роль |
|-------|------|
| 🟠 Senior | 🅰️ System Analyst |

> **Почему это важно для аналитика:** Message Brokers — это критический компонент распределенных систем. Понимание брокеров сообщений необходимо для проектирования асинхронных систем, масштабируемых архитектур и интеграции микросервисов.

## Что такое Message Broker?

**Message Broker** (брокер сообщений) — это промежуточное ПО (middleware), которое обеспечивает асинхронный обмен сообщениями между компонентами системы. Брокер действует как посредник: одно приложение (producer) отправляет сообщение в брокер, а другое приложение (consumer) получает его позже.

## Основная архитектура

```
Producer → Message Broker (очередь/топик) → Consumer
  (отправитель)        (промежуточное хранилище)      (получатель)

Преимущества:
- Producer и Consumer не знают друг о друге
- Producer не ждет Consumer
- Consumer может получить сообщение позже
- Можно масштабировать Consumer независимо
```

## Основные компоненты Message Broker

### 1. Очередь сообщений (Queue)
Сообщения обрабатываются в порядке FIFO (First In, First Out):

```
Producer:
  send(message1) → Queue
  send(message2) → Queue
  send(message3) → Queue

Consumer:
  receive() → message1 (первое)
  receive() → message2 (второе)
  receive() → message3 (третье)
```

**Используется для:** один Producer → один Consumer

### 2. Топик (Topic / Pub-Sub)
Сообщения публикуются в топик, все подписчики получают копию:

```
Producer:
  publish(message) → Topic

Consumers подписаны на Topic:
  Consumer1 получает сообщение
  Consumer2 получает сообщение
  Consumer3 получает сообщение
```

**Используется для:** один Producer → много Consumers

### 3. Гарантия доставки

| Гарантия | Описание | Риск |
|----------|---------|------|
| **At Most Once** | Сообщение может быть потеряно | Потеря данных |
| **At Least Once** | Сообщение гарантировано доставлено | Дубликаты |
| **Exactly Once** | Сообщение доставлено ровно один раз | Сложная реализация |

## Популярные Message Brokers

### RabbitMQ
- Стабильный, надежный
- Очереди и Pub/Sub
- Хороший выбор для старта
- Легче изучать и настраивать

```
Producer → RabbitMQ Exchange → Queue → Consumer
```

### Apache Kafka
- Масштабируемый до петабайтов
- Event streaming (хранит историю)
- Лучше для big data
- Сложнее изучать, требует кластера

```
Producer → Kafka Topic (партиционированный) → Consumer Group
```

## RabbitMQ vs Kafka

| Параметр | RabbitMQ | Kafka |
|----------|----------|-------|
| **Сложность** | Проще | Сложнее |
| **Масштабируемость** | До миллионов сообщений/сек | Миллиарды сообщений/сек |
| **Хранение** | В памяти + диск | Постоянное хранилище |
| **История** | Удаляются после обработки | Сохраняются долго |
| **Latency** | ~1ms | ~10ms |
| **Consumer Groups** | Нет встроено | Встроены |
| **Использование** | Очереди, микросервисы | Event streaming, логирование |

## Пример архитектуры с Message Broker

```
Заказ пользователя:

1. Пользователь → API сервис (OrderService)
2. OrderService отправляет сообщение "order_created" в RabbitMQ
3. OrderService возвращает 202 Accepted (асинхронно)

4. Параллельно несколько сервисов обрабатывают событие:
   - PaymentService слушает "order_created" → обрабатывает платеж
   - EmailService слушает "order_created" → отправляет email
   - NotificationService слушает "order_created" → отправляет уведомление
   - InventoryService слушает "order_created" → обновляет склад

5. Если что-то сломалось, сообщение остается в очереди, Consumer может retry
```

## Когда использовать Message Broker?

### Используйте Message Broker для:

- **Асинхронные операции** — отправка email, загрузка файлов
- **Микросервисная архитектура** — децентрализованная коммуникация
- **Масштабируемые системы** — обработка всплесков нагрузки
- **Event-driven архитектура** — реагирование на события
- **Отвязка сервисов** — Producer не зависит от Consumer
- **Гарантия доставки** — сообщение не потеряется

### Когда выбрать REST/synchronous:

- Требуется немедленный ответ
- Простые CRUD операции
- Отладка и тестирование важнее, чем масштабируемость

## Practical Example: RabbitMQ

```javascript
// Producer (Node.js)
const amqp = require('amqplib');

async function sendOrder(orderData) {
  const connection = await amqp.connect('amqp://localhost');
  const channel = await connection.createChannel();

  await channel.assertQueue('orders');

  channel.sendToQueue('orders', Buffer.from(JSON.stringify(orderData)));
  console.log('Order sent to queue');
}

// Consumer (Node.js)
async function processOrders() {
  const connection = await amqp.connect('amqp://localhost');
  const channel = await connection.createChannel();

  await channel.assertQueue('orders');

  channel.consume('orders', (msg) => {
    const order = JSON.parse(msg.content.toString());
    console.log('Processing order:', order);

    // Process order...
    channel.ack(msg); // Acknowledge after successful processing
  });
}
```

## Practical Example: Kafka

```python
# Producer (Python)
from kafka import KafkaProducer
import json

producer = KafkaProducer(
    bootstrap_servers=['localhost:9092'],
    value_serializer=lambda v: json.dumps(v).encode('utf-8')
)

order = {'id': 1, 'customer': 'John', 'total': 99.99}
producer.send('orders-topic', value=order)

# Consumer (Python)
from kafka import KafkaConsumer
import json

consumer = KafkaConsumer(
    'orders-topic',
    bootstrap_servers=['localhost:9092'],
    value_deserializer=lambda m: json.loads(m.decode('utf-8')),
    group_id='order-processors'
)

for message in consumer:
    print(f'Processing order: {message.value}')
```

## Сложности и consideration

1. **Доставка** — гарантировать, что каждое сообщение обработано ровно один раз сложно
2. **Порядок** — в распределенной системе сложно гарантировать порядок обработки
3. **Monitoring** — сложнее отслеживать, что происходит в системе
4. **Debugging** — асинхронные ошибки сложнее найти
5. **Operational Complexity** — требуется дополнительное ПО для управления

## Инструменты для мониторинга

- **RabbitMQ Management UI** — встроенный интерфейс управления
- **Kafka Manager** — управление Kafka кластером
- **DataDog** — мониторинг брокеров
- **Prometheus** — метрики Message Brokers

## 📚 Ресурсы для изучения

- [RabbitMQ Official Documentation](https://www.rabbitmq.com/documentation.html)
- [Apache Kafka Official Documentation](https://kafka.apache.org/documentation/)
- [Message Queue Comparison](https://www.youtube.com/watch?v=LoWAz6_Ylvs)
- [RabbitMQ vs Kafka: Which one to choose?](https://www.cloudamqp.com/blog/when-to-use-rabbitmq-or-apache-kafka.html)
- [Enterprise Integration Patterns - Messaging](https://enterpriseintegrationpatterns.com/patterns/messaging/)
- [Asynchronous Processing with Message Brokers](https://www.youtube.com/watch?v=DCooZ-KzmnQ)
- [Roadmap.sh - Message Brokers](https://roadmap.sh/backend#message-brokers)
