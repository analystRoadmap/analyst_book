# gRPC

| Грейд | Роль |
|-------|------|
| 🟠 Senior | 🅰️ System Analyst |

> **Почему это важно для аналитика:** gRPC — это современная альтернатива REST для высоконагруженных систем. Понимание gRPC критично при архитектурном проектировании микросервисных систем и выборе между различными технологиями API.

## Что такое gRPC?

**gRPC** (gRPC Remote Procedure Call) — это открытый framework для удаленного вызова процедур (RPC), разработанный Google. gRPC использует HTTP/2 и Protocol Buffers для эффективной коммуникации между сервисами на различных платформах и языках программирования.

## Основные характеристики gRPC

### 1. HTTP/2 Protocol
gRPC работает поверх HTTP/2, что обеспечивает:
- Мультиплексирование (множественные запросы в одном соединении)
- Двусторонний поток данных
- Компрессию заголовков
- Быструю передачу данных

### 2. Protocol Buffers (protobuf)
Для сериализации данных используется protobuf вместо JSON/XML:
- Меньший размер данных (3-10 раз меньше, чем JSON)
- Быстрая сериализация/десериализация
- Строгая типизация
- Версионирование

Пример определения protobuf:
```protobuf
syntax = "proto3";

service UserService {
  rpc GetUser (UserRequest) returns (UserResponse);
  rpc ListUsers (Empty) returns (stream UserResponse);
}

message UserRequest {
  int32 id = 1;
}

message UserResponse {
  int32 id = 1;
  string name = 2;
  string email = 3;
}
```

### 3. Четыре типа взаимодействия

| Тип | Описание | Пример |
|-----|---------|--------|
| **Unary** | Один запрос → один ответ | Получение пользователя по ID |
| **Server Streaming** | Один запрос → поток ответов | Список пользователей (постепенно) |
| **Client Streaming** | Поток запросов → один ответ | Загрузка файла частями |
| **Bidirectional** | Поток запросов ↔ поток ответов | Chat, real-time synchronization |

### 4. Автоматическое генерирование кода
На основе .proto файла генерируется код клиента и сервера для различных языков (Go, Java, Python, Node.js и т.д.).

## gRPC vs REST

| Критерий | gRPC | REST |
|----------|------|------|
| **Протокол** | HTTP/2 | HTTP/1.1 |
| **Формат** | protobuf (двоичный) | JSON/XML (текстовый) |
| **Производительность** | Очень высокая | Средняя |
| **Размер данных** | ~3KB | ~10KB+ |
| **Задержка** | Очень низкая | Средняя |
| **Поддержка потоков** | Встроенная | Требует WebSocket |
| **Просмотр в браузере** | Нет | Да |
| **Learning curve** | Выше | Ниже |
| **Использование** | Микросервисы | Публичные API |

## Когда использовать gRPC?

- **Микросервисная архитектура** — коммуникация между сервисами
- **Высоконагруженные системы** — требуется минимальная задержка
- **Real-time приложения** — потоки данных (мониторинг, streaming)
- **Мобильные backend** — эффективное использование трафика
- **Внутренние API** — между сервисами в одной организации

## Когда выбрать REST?

- Публичные API для сторонних приложений
- Веб-браузеры нужны как клиенты
- Простота интеграции важнее, чем производительность
- Отладка и мониторинг через стандартные инструменты
- CRUD операции без сложных паттернов

## Пример gRPC сервиса

```protobuf
// user_service.proto
syntax = "proto3";

package user.v1;

service UserService {
  // Unary RPC
  rpc GetUser (GetUserRequest) returns (User);

  // Server streaming
  rpc ListUsers (Empty) returns (stream User);

  // Client streaming
  rpc CreateUsers (stream CreateUserRequest) returns (CreatedCount);

  // Bidirectional streaming
  rpc Chat (stream Message) returns (stream Message);
}

message GetUserRequest {
  int32 user_id = 1;
}

message User {
  int32 id = 1;
  string name = 2;
  string email = 3;
}

message Empty {}
```

## Инструменты и экосистема

- **grpcurl** — командная строка для тестирования gRPC
- **grpc-web** — для использования gRPC из браузера
- **gRPC Gateway** — конвертирует gRPC в REST
- **BloomRPC** — GUI-клиент для тестирования
- **Protocol Buffers Compiler** — компилирует .proto в код

## Сложности при внедрении

- Круче learning curve для разработчиков
- Сложнее отлаживать (двоичный формат)
- Требует HTTP/2 и настройки мониторинга
- Меньше стандартных инструментов для работы
- Сложнее публиковать публичное API

## 📚 Ресурсы для изучения

- [gRPC Official Documentation](https://grpc.io)
- [Protocol Buffers Guide](https://developers.google.com/protocol-buffers)
- [gRPC by Example](https://grpc.io/docs/what-is-grpc/introduction/)
- [gRPC Performance Benchmarks](https://ghz.sh)
- [Roadmap.sh - gRPC](https://roadmap.sh/api-design#grpc)
- [Building Microservices with gRPC](https://www.youtube.com/watch?v=Naonb2XD_2Q)
- [Enterprise Integration Patterns](https://enterpriseintegrationpatterns.com)
