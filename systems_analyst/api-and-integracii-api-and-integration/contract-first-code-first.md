# Contract First / Code First

| Грейд | Роль |
|-------|------|
| 🔵 Middle | 🅰️ System Analyst |

> **Почему это важно для аналитика:** Contract First и Code First — это два различных подхода к разработке API. Выбор между ними влияет на процесс разработки, качество API и взаимодействие между командами. Понимание обоих подходов необходимо при планировании проектов и выборе методологии.

## Основные определения

### Contract First (Соглашение в приоритете)
**Определи контракт (интерфейс) первым**, затем пиши код, который реализует контракт.

```
1. Определить контракт (OpenAPI, WSDL, .proto)
2. Разработчик A пишет код на основе контракта
3. Разработчик B пишет код на основе контракта
4. Оба кода работают вместе (контракт гарантирует совместимость)
```

### Code First (Сначала код)
**Пиши код сначала**, затем автоматически генерируй контракт/схему.

```
1. Разработчик A пишет код (классы, методы)
2. Автоматически генерируется контракт из кода
3. Разработчик B видит контракт и пишет свой код
```

## Contract First подход

### Суть Contract First

```
Контракт (OpenAPI/WSDL/protobuf) ← Источник истины
          ↙                      ↘
    Frontend разработчик    Backend разработчик
    генерирует типы          пишет реализацию
    для своего кода         на основе контракта
```

### Процесс Contract First

```
1. Аналитик + Backend разработчик определяют контракт

   /api/users GET
   /api/users POST

   User {
     id: integer
     name: string
     email: string
   }

2. OpenAPI документ готов ✓

3. Frontend разработчик
   - Видит OpenAPI
   - Генерирует TypeScript типы
   - Пишет UI на основе контракта
   - Может тестировать с mock сервером

4. Backend разработчик
   - Видит OpenAPI
   - Пишет реализацию
   - Гарантирует, что API соответствует контракту

5. Интеграция ✓ (работает сразу, т.к. оба следуют контракту)
```

### Плюсы Contract First

✅ **Согласованность:** Оба конца API разработаны в соответствии с одним контрактом
✅ **Параллельная разработка:** Frontend и Backend могут работать независимо
✅ **Документация:** Контракт — это документация
✅ **Тестирование:** Можно писать тесты на основе контракта до реализации
✅ **Code generation:** Автоматическая генерация кода клиента/сервера
✅ **Breaking changes видны сразу:** Изменение контракта видно всем

### Минусы Contract First

❌ **Медленнее стартовать:** Нужно сначала определить контракт
❌ **Сложнее для быстрых изменений:** Каждое изменение влияет на контракт
❌ **Требует дисциплины:** Все должны следовать контракту
❌ **Инструменты:** Нужны инструменты для работы с контрактами

### Примеры Contract First

**OpenAPI (REST API)**
```yaml
openapi: 3.0.0
paths:
  /users:
    get:
      responses:
        200:
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/User'

components:
  schemas:
    User:
      type: object
      properties:
        id: {type: integer}
        name: {type: string}
```

**Protocol Buffers (gRPC)**
```protobuf
syntax = "proto3";

service UserService {
  rpc GetUser(GetUserRequest) returns (User);
}

message User {
  int32 id = 1;
  string name = 2;
  string email = 3;
}
```

**GraphQL Schema (GraphQL)**
```graphql
type User {
  id: ID!
  name: String!
  email: String!
}

type Query {
  user(id: ID!): User
  users: [User!]!
}
```

## Code First подход

### Суть Code First

```
Backend код (классы, методы) ← Источник истины
             ↓
    Автоматическая генерация контракта
             ↓
    Frontend разработчик видит контракт
             ↓
    Frontend пишет код
```

### Процесс Code First

```
1. Backend разработчик пишет код

   class User {
     int id;
     String name;
     String email;
   }

   @GetMapping("/users")
   public List<User> getUsers() { ... }

2. Инструмент (например, Swagger) генерирует OpenAPI из кода

   Аннотации в коде:
   @ApiOperation("Get all users")
   @ApiResponse(code = 200, message = "Success")

3. OpenAPI документ автоматически сгенерирован ✓

4. Frontend разработчик видит сгенерированный OpenAPI

5. Frontend пишет код на основе сгенерированного контракта

6. Интеграция ✓
```

### Плюсы Code First

✅ **Быстро стартовать:** Пиши код, контракт генерируется
✅ **Гибкость:** Легко вносить изменения в код
✅ **Меньше дублирования:** Нет отдельного контракта
✅ **Реальность:** Контракт отражает реальный код

### Минусы Code First

❌ **Контракт зависит от реализации:** Код определяет интерфейс
❌ **Сложнее параллельная разработка:** Frontend ждет Backend
❌ **Документация может быть неполной:** Разработчики забывают аннотации
❌ **Breaking changes не видны в коде:** Нужно смотреть сгенерированный контракт
❌ **Более тесная связь:** Изменение реализации влияет на интерфейс

### Примеры Code First

**Spring Boot + Springdoc-openapi (Java)**
```java
@RestController
@RequestMapping("/api/users")
public class UserController {

  @GetMapping
  @Operation(summary = "Get all users")
  @ApiResponse(responseCode = "200", description = "Success")
  public List<User> getUsers() {
    return userService.findAll();
  }
}

// Контракт генерируется автоматически из аннотаций
```

**FastAPI (Python)**
```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/users")
def get_users() -> List[User]:
    """Get all users"""
    return userService.find_all()

# OpenAPI генерируется автоматически
```

**NestJS (Node.js)**
```typescript
@Controller('users')
export class UserController {
  @Get()
  @ApiOperation({ summary: 'Get all users' })
  @ApiResponse({ status: 200, type: User })
  findAll(): User[] {
    return this.userService.findAll();
  }
}

// Swagger документ генерируется из декораторов
```

## Сравнение Contract First и Code First

| Критерий | Contract First | Code First |
|----------|---|---|
| **Скорость старта** | Медленнее | Быстрее |
| **Документирование** | Явно | Автоматическое |
| **Параллельная разработка** | Да | Нет |
| **Гибкость** | Сложнее менять | Легче менять |
| **Согласованность** | Гарантируется контрактом | Зависит от разработчиков |
| **Breaking changes** | Видны сразу | Нужно проверять |
| **Инструменты** | OpenAPI, protobuf, WSDL | Аннотации, генераторы |
| **Когда использовать** | Большие команды, публичные API | Стартапы, быстрая разработка |

## Гибридный подход (Best Practice)

На практике лучше использовать оба подхода:

```
1. Contract First для контрактов, которые используют другие команды
2. Code First для быстрого прототипирования

Или:

1. Code First для первой версии
2. Contract First для стабилизированного API
```

### Рекомендуемая стратегия

```
День 1-2: Code First
├─ Быстро написать первую версию API
├─ Генерировать OpenAPI из кода
└─ Показать клиентам

День 3+: Contract First
├─ "Заморозить" контракт (OpenAPI)
├─ Backend улучшает реализацию
├─ Frontend разрабатывает на основе контракта
└─ Изменения контракта обсуждаются и планируются
```

## Практический пример: E-commerce API

### Contract First approach

```yaml
# api-contract.yaml (День 1)
openapi: 3.0.0
paths:
  /products:
    get:
      responses:
        200:
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/Product'
  /orders:
    post:
      requestBody:
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CreateOrderRequest'
      responses:
        201:
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Order'

components:
  schemas:
    Product:
      type: object
      properties:
        id: {type: integer}
        name: {type: string}
        price: {type: number}
```

Теперь:
- **Frontend** может писать UI на основе этого контракта
- **Backend** может писать реализацию на основе этого контракта
- Оба знают точно, что ожидать

### Code First approach

```java
// Day 1: Backend пишет код
@RestController
@RequestMapping("/api/products")
public class ProductController {

  @GetMapping
  public List<Product> getProducts() {
    return productService.findAll();
  }
}

// OpenAPI генерируется автоматически из кода
// Frontend видит сгенерированный контракт и пишет UI
```

Плюсы:
- Быстрее стартовать
- Меньше дублирования

Минусы:
- Frontend может начать только когда Backend готов
- Если backend изменится, контракт изменится

## Когда использовать какой подход?

| Сценарий | Рекомендуется |
|----------|--------------|
| Публичное API, много клиентов | Contract First |
| Интеграция с legacy системами | Contract First |
| Большая команда, разные подразделения | Contract First |
| Быстрый стартап, маленькая команда | Code First |
| Микросервисы внутри компании | Гибридный (Code First + Contract First) |
| Критически важный сервис | Contract First |
| Экспериментальный API | Code First |

## Best Practices

1. **Используйте оба подхода** — Contract First для стабильных API, Code First для прототипов
2. **Документируйте решение** — почему выбран данный подход
3. **Версионируйте контракты** — контракт должен быть версионирован
4. **Contract testing** — автоматически проверяйте соответствие контракту
5. **Consumer-driven contracts** — позвольте потребителям определять контракт
6. **Code reviews** — тщательно проверяйте изменения контракта

## Инструменты

### Contract First инструменты
- **OpenAPI Generator** — генерировать код из OpenAPI
- **Swagger Editor** — писать OpenAPI
- **Protocol Buffers** — определять контракты gRPC
- **GraphQL SDL** — определять контракты GraphQL

### Code First инструменты
- **Springdoc-openapi** — генерировать OpenAPI из Java кода
- **FastAPI** — автоматический OpenAPI в Python
- **NestJS Swagger** — генерировать OpenAPI из NestJS
- **Swagger/OpenAPI annotations** — аннотации для генерации

## 📚 Ресурсы для изучения

- [OpenAPI Specification](https://spec.openapis.org)
- [Contract Testing with PACT](https://pact.foundation)
- [Protocol Buffers Documentation](https://developers.google.com/protocol-buffers)
- [Consumer-Driven Contracts](https://martinfowler.com/articles/consumerDrivenContracts.html)
- [API Contract Design](https://www.youtube.com/watch?v=IM4GH6iSDk8)
- [Contract-First vs Code-First](https://www.openapis.org)
- [Swagger/OpenAPI Code Generation](https://openapi-generator.tech)
