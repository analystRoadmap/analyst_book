# WebSocket

| Грейд | Роль |
|-------|-----|
| 🔵 Middle | 🅰️ System Analyst |

> **Почему это важно для аналитика:** WebSocket нужен для real-time приложений (чаты, уведомления, игры). Понимание WebSocket помогает выбирать правильный протокол для систем, требующих двусторонней коммуникации в реальном времени.

## Что такое WebSocket?

**WebSocket** — это протокол связи, обеспечивающий двусторонний (full-duplex) обмен данными между клиентом и сервером через одно постоянное соединение. WebSocket решает ограничение HTTP — необходимость создавать новое соединение для каждого запроса.

## Как WebSocket работает

### Этап 1: Handshake (установление соединения)

```
GET /chat HTTP/1.1
Host: api.example.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: x3JJHMbDL1EzLkh9GBhXDw==
Sec-WebSocket-Version: 13

Ответ:
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: HSmrc0sMlYUkAGmm5OPpG2HaGWk=
```

### Этап 2: Full-duplex коммуникация
После handshake, обе стороны могут отправлять данные в любой момент.

## Основные характеристики

### 1. Постоянное соединение
Соединение остается открытым, пока одна из сторон его не закроет. Это исключает overhead на создание новых соединений.

### 2. Двусторонняя коммуникация
И клиент, и сервер могут инициировать отправку данных без ожидания ответа.

### 3. Низкая задержка
Отсутствие overhead на создание соединений = минимальная задержка (~50-100ms vs 200-500ms для HTTP polling).

### 4. Эффективное использование ресурсов
Одно соединение вместо сотни HTTP-запросов = меньше нагрузки на сервер.

## Пример WebSocket коммуникации

```javascript
// Клиент
const ws = new WebSocket('ws://api.example.com/chat');

// Когда соединение установлено
ws.onopen = () => {
  console.log('Connected');
  ws.send(JSON.stringify({ type: 'join', room: 'lobby' }));
};

// Получение сообщений от сервера
ws.onmessage = (event) => {
  const message = JSON.parse(event.data);
  console.log('New message:', message.text);
};

// Обработка ошибок
ws.onerror = (error) => {
  console.log('WebSocket error:', error);
};

// Закрытие соединения
ws.onclose = () => {
  console.log('Disconnected');
};
```

## WebSocket vs HTTP Polling

| Критерий | WebSocket | HTTP Polling | HTTP Long Polling |
|----------|-----------|--------------|-------------------|
| **Задержка** | ~50ms | ~5000ms (зависит от интервала) | ~1000ms |
| **Нагрузка на сервер** | Низкая | Высокая | Средняя |
| **Трафик** | Низкий | Высокий | Средний |
| **Двусторонность** | Встроенная | Требует опросов | Требует опросов |
| **Сложность** | Средняя | Низкая | Низкая |
| **Масштабируемость** | Требует балансировки сессий | Хорошо масштабируется | Средняя |

## Сравнение решений для real-time

| Решение | Когда использовать | Плюсы | Минусы |
|---------|-------------------|-------|--------|
| **WebSocket** | Чаты, live notifications | Низкая задержка, двусторонний | Требует сессий, сложнее |
| **Server-Sent Events (SSE)** | Однонаправленные обновления | Просто реализовать | Только сервер→клиент |
| **HTTP Polling** | Редкие обновления | Очень просто | Высокая задержка, трафик |
| **Message Queue (Kafka)** | Масштабируемые события | Асинхронность, масштабируемость | Сложнее, больше latency |

## Когда использовать WebSocket?

- **Чаты и сообщения в реальном времени**
- **Live notifications** — оповещения о событиях
- **Мониторинг и dashboard** — live метрики и графики
- **Онлайн игры** — синхронизация состояния
- **Финансовые платформы** — live котировки
- **Совместное редактирование** — Google Docs-подобные приложения
- **Live streaming** — трансляции, видео

## Когда выбрать альтернативу?

- **REST** — статичные данные, простые запросы
- **SSE** — обновления только от сервера (проще чем WebSocket)
- **Message Brokers** — распределенные системы, асинхронность
- **Server-Sent Events** — когда двусторонность не критична

## Практические примеры

### Пример 1: Чат-комната
```javascript
// Сервер (Node.js с Socket.io)
io.on('connection', (socket) => {
  socket.on('message', (msg) => {
    io.emit('message', msg); // Отправить всем
  });
});
```

### Пример 2: Live notifications
```javascript
// Клиент подписывается на обновления
ws.addEventListener('message', (event) => {
  const notification = JSON.parse(event.data);
  showNotification(notification.title, notification.body);
});
```

## Инструменты и библиотеки

- **Socket.io** — популярная библиотека (Node.js)
- **Strophe.js** — XMPP клиент с WebSocket
- **PusherJS** — платный сервис для real-time
- **Firebase Realtime Database** — облачное решение Google
- **ws** — простой WebSocket сервер для Node.js

## Сложности при использовании

- **Load balancing** — нужна сессионная привязка между клиентом и сервером
- **Масштабируемость** — сложнее масштабировать, чем REST (HTTP stateless)
- **Отладка** — сложнее тестировать и отлаживать
- **Firewall и proxy** — некоторые proxy блокируют WebSocket
- **Fallback** — нужна поддержка HTTP polling для старых браузеров

## 📚 Ресурсы для изучения

- [WebSocket MDN Documentation](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket)
- [RFC 6455 - The WebSocket Protocol](https://tools.ietf.org/html/rfc6455)
- [Socket.io Documentation](https://socket.io/docs/)
- [Real-time Web Apps with WebSocket](https://www.youtube.com/watch?v=8ARodQ4Wlf4)
- [HTML5 WebSocket Tutorial](https://www.w3schools.com/html/html5_websocket.asp)
- [Enterprise Integration Patterns - Event-Driven](https://enterpriseintegrationpatterns.com)
- [Server-Sent Events Alternative](https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events)
