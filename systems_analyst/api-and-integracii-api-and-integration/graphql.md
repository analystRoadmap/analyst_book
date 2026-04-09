# GraphQL

| Грейд | Роль |
|-------|------|
| 🟠 Senior | 🅰️ System Analyst |

> **Почему это важно для аналитика:** GraphQL меняет подход к запросам данных, позволяя клиентам получать ровно то, что нужно. Понимание GraphQL критично при проектировании API для приложений с высокой сложностью данных и мобильных клиентов с ограничениями.

## Что такое GraphQL?

**GraphQL** (Graph Query Language) — это язык запросов и runtime для API, разработанный Facebook. GraphQL позволяет клиентам запрашивать точно те данные, которые им нужны, в одном запросе, избегая проблем над-доставки и недо-доставки данных.

## Основные концепции GraphQL

### 1. Гибкость запросов
Клиент определяет структуру ответа, а не сервер:

```graphql
# Запрос на GraphQL
query GetUser {
  user(id: 123) {
    name
    email
    orders {
      id
      total
      items {
        productName
        price
      }
    }
  }
}

# Клиент получит точно эту структуру
{
  "user": {
    "name": "John Doe",
    "email": "john@example.com",
    "orders": [
      {
        "id": 1,
        "total": 99.99,
        "items": [
          {"productName": "Laptop", "price": 999.99}
        ]
      }
    ]
  }
}
```

### 2. Одна точка входа
В отличие от REST, у GraphQL есть один endpoint для всех запросов:
- REST: `/api/users`, `/api/orders`, `/api/products`
- GraphQL: `/graphql` (все запросы здесь)

### 3. Типизированная схема
GraphQL использует строгую типизацию через SDL (Schema Definition Language):

```graphql
type User {
  id: ID!
  name: String!
  email: String!
  age: Int
  orders: [Order!]!
}

type Order {
  id: ID!
  total: Float!
  items: [OrderItem!]!
  status: OrderStatus!
}

enum OrderStatus {
  PENDING
  CONFIRMED
  SHIPPED
  DELIVERED
}

type Query {
  user(id: ID!): User
  users(limit: Int = 10): [User!]!
}

type Mutation {
  createUser(input: CreateUserInput!): User!
  updateUser(id: ID!, input: UpdateUserInput!): User!
}
```

### 4. Три основных операции

| Операция | Описание | Пример |
|----------|---------|--------|
| **Query** | Получение данных (read-only) | Получение списка пользователей |
| **Mutation** | Изменение данных (write) | Создание нового заказа |
| **Subscription** | Real-time обновления | Оповещение при изменении статуса |

### 5. Интроспекция
GraphQL предоставляет встроенный способ исследовать схему:

```graphql
query IntrospectionQuery {
  __schema {
    types {
      name
      description
      fields {
        name
        type { name }
      }
    }
  }
}
```

## GraphQL vs REST

| Критерий | GraphQL | REST |
|----------|---------|------|
| **Гибкость** | Очень гибкий | Фиксированная структура |
| **Over-fetching** | Нет | Возможен (получаете лишние данные) |
| **Under-fetching** | Нет | Возможен (нужны доп. запросы) |
| **Количество запросов** | 1 запрос на экран | N запросов |
| **Простота** | Сложнее учиться | Проще учиться |
| **Производительность** | Может быть лучше/хуже | Предсказуема |
| **Кэширование** | Сложнее (нет стандарта) | Просто (через HTTP) |
| **Используется сегодня** | Растет популярность | Доминирует |

## Пример: Мобильное приложение

**REST подход (3 запроса):**
```
GET /api/users/123         → возвращает все поля пользователя
GET /api/users/123/orders → возвращает все заказы со всеми полями
GET /api/orders/456        → получение одного заказа с деталями
```

**GraphQL подход (1 запрос):**
```graphql
query MobileView {
  user(id: 123) {
    name
    email
    orders {
      id
      total
    }
  }
}
```
Экономия трафика: ~70% для мобильных клиентов!

## Когда использовать GraphQL?

- **Мобильные приложения** — экономия трафика
- **Сложные связанные данные** — многоуровневые отношения
- **Множество клиентов** — каждый запрашивает разные данные
- **BFF (Backend for Frontend)** — разные фронтенды, одна бизнес-логика
- **Быстрое прототипирование** — быстрая разработка клиентов

## Когда выбрать REST?

- Простые CRUD операции
- Публичное API с ограниченным набором эндпоинтов
- Когда производительность гарантируется структурой
- Когда нужна простота интеграции и инструментарий
- Внутренние API между микросервисами

## Сложности GraphQL

- **N+1 Problem** — запросы могут привести к множеству подзапросов БД
- **Кэширование** — сложнее, чем с HTTP GET
- **Complexity** — мощный язык требует контроля сложности
- **Отладка** — сложнее, чем с REST
- **Миграция** — нужно переучивать разработчиков

## Инструменты и экосистема

- **Apollo GraphQL** — популярная библиотека
- **GraphQL.js** — Reference implementation
- **Hasura** — Instant GraphQL API on databases
- **GraphQL Playground** — IDE для тестирования
- **Relay** — Клиентская фреймворк от Facebook
- **Dataloader** — решение для N+1 проблемы

## 📚 Ресурсы для изучения

- [GraphQL Official Documentation](https://graphql.org)
- [How to GraphQL - Interactive Tutorial](https://www.howtographql.com)
- [Apollo GraphQL Documentation](https://www.apollographql.com/docs/)
- [GraphQL Best Practices](https://graphql.org/learn/best-practices/)
- [GraphQL vs REST](https://www.youtube.com/watch?v=PTfZcN20z0w)
- [Roadmap.sh - GraphQL](https://roadmap.sh/api-design#graphql)
- [GraphQL Schema Design](https://www.apollographql.com/docs/apollo-server/schema/schema/)
