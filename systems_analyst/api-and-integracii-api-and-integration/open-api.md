# OpenAPI

| Грейд | Роль |
|-------|------|
| 🔵 Middle | 🅰️ System Analyst |

> **Почему это важно для аналитика:** OpenAPI (Swagger) — это стандарт для документирования REST API. Понимание OpenAPI критично при описании требований к API, проектировании API-first подхода и обеспечении взаимопонимания между аналитиками и разработчиками.

## Что такое OpenAPI?

**OpenAPI** (ранее известный как Swagger) — это спецификация для описания REST API в машиночитаемом формате (JSON или YAML). OpenAPI позволяет разработчикам описать API один раз, а затем использовать эту описание для:
- Автоматической генерации документации
- Генерации клиентского кода
- Автоматического тестирования
- Валидации запросов/ответов

## История

- **2010** — Swagger создан Wordnik (стартап)
- **2015** — Swagger передан Linux Foundation и переименован в OpenAPI
- **2020** — OpenAPI 3.0 стал основным стандартом

## Структура OpenAPI документа

OpenAPI документ содержит информацию об API в YAML или JSON:

```yaml
openapi: 3.0.0
info:
  title: User API
  version: 1.0.0
  description: API для управления пользователями

servers:
  - url: https://api.example.com/v1
    description: Production сервер

paths:
  /users:
    get:
      summary: Получить список пользователей
      operationId: getUsers
      parameters:
        - name: limit
          in: query
          schema:
            type: integer
          required: false
      responses:
        '200':
          description: Успешный ответ
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/User'
    post:
      summary: Создать нового пользователя
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CreateUserInput'
      responses:
        '201':
          description: Пользователь создан
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/User'

components:
  schemas:
    User:
      type: object
      properties:
        id:
          type: integer
        name:
          type: string
        email:
          type: string
          format: email
      required:
        - id
        - name
        - email

    CreateUserInput:
      type: object
      properties:
        name:
          type: string
        email:
          type: string
          format: email
      required:
        - name
        - email
```

## Основные компоненты OpenAPI

### 1. Info
Основная информация об API:
```yaml
info:
  title: My API
  version: 1.0.0
  description: Description of API
  contact:
    name: Support
    email: support@example.com
```

### 2. Paths (эндпоинты)
Описание всех доступных эндпоинтов:
```yaml
paths:
  /users/{id}:
    get:
      summary: Get user by ID
      parameters:
        - name: id
          in: path
          required: true
          schema:
            type: integer
```

### 3. Components (переиспользуемые компоненты)
Определения схем, ответов, параметров:
```yaml
components:
  schemas:
    User:
      type: object
      properties:
        id: { type: integer }
        name: { type: string }
```

### 4. Security (безопасность)
Определение методов аутентификации:
```yaml
components:
  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT

security:
  - bearerAuth: []
```

## Основные операции в путях (paths)

| Метод | Описание | Использование |
|-------|---------|---------------|
| **get** | Получение ресурса | Безопасно, идемпотентно |
| **post** | Создание нового ресурса | Может менять состояние |
| **put** | Полное обновление | Полная замена ресурса |
| **patch** | Частичное обновление | Обновление отдельных полей |
| **delete** | Удаление ресурса | Необратимо |

## Коды ответов (HTTP Status Codes)

```yaml
responses:
  '200':
    description: OK - успешно
  '201':
    description: Created - ресурс создан
  '204':
    description: No Content - успешно, нет содержимого
  '400':
    description: Bad Request - неверный запрос
  '401':
    description: Unauthorized - требуется аутентификация
  '403':
    description: Forbidden - доступ запрещен
  '404':
    description: Not Found - не найдено
  '500':
    description: Internal Server Error - ошибка сервера
```

## Пример полного API в OpenAPI

```yaml
openapi: 3.0.0
info:
  title: Blog API
  version: 1.0.0

paths:
  /posts:
    get:
      summary: Get all posts
      parameters:
        - name: page
          in: query
          schema:
            type: integer
          required: false
      responses:
        '200':
          description: List of posts
          content:
            application/json:
              schema:
                type: object
                properties:
                  data:
                    type: array
                    items:
                      $ref: '#/components/schemas/Post'
                  total:
                    type: integer

    post:
      summary: Create new post
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CreatePostInput'
      responses:
        '201':
          description: Post created
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Post'

components:
  schemas:
    Post:
      type: object
      properties:
        id:
          type: integer
        title:
          type: string
        content:
          type: string
        author:
          $ref: '#/components/schemas/Author'
        created_at:
          type: string
          format: date-time

    CreatePostInput:
      type: object
      properties:
        title:
          type: string
        content:
          type: string
      required:
        - title
        - content

    Author:
      type: object
      properties:
        id:
          type: integer
        name:
          type: string
```

## Инструменты для работы с OpenAPI

| Инструмент | Описание | Использование |
|-----------|---------|----------------|
| **Swagger Editor** | Онлайн редактор для написания OpenAPI | Написание и валидация |
| **Swagger UI** | Автоматическая интерактивная документация | Документирование API |
| **Redoc** | Альтернатива Swagger UI | Чистая документация |
| **OpenAPI Generator** | Генерирование кода клиентов и серверов | Код-генерация |
| **Postman** | Тестирование API (импорт OpenAPI) | Тестирование |

## Когда использовать OpenAPI?

- **Документирование API** — основной инструмент
- **Contract-first разработка** — сначала OpenAPI, потом код
- **Генерация клиентского кода** — автоматическое создание SDK
- **Тестирование API** — автоматическая валидация
- **Версионирование API** — отслеживание изменений

## Best Practices

1. **Используйте версионирование** — всегда включайте версию в API (`/v1/`, `/v2/`)
2. **Четкое описание** — добавляйте summary и description к каждой операции
3. **Примеры** — включайте примеры запросов и ответов
4. **Консистентность** — используйте одинаковые паттерны для всех эндпоинтов
5. **Ошибки** — четко описывайте возможные коды ошибок
6. **Безопасность** — явно указывайте требования аутентификации
7. **Поддерживайте актуальность** — обновляйте документацию при изменении API

## OpenAPI in Practice

```yaml
# Минималистичный пример для быстрого старта
openapi: 3.0.0
info:
  title: Simple API
  version: 1.0.0

paths:
  /hello:
    get:
      responses:
        '200':
          description: Hello message
          content:
            application/json:
              schema:
                type: object
                properties:
                  message:
                    type: string
```

## 📚 Ресурсы для изучения

- [OpenAPI Official Specification](https://spec.openapis.org)
- [Swagger Editor - Online Tool](https://editor.swagger.io)
- [Swagger UI Documentation](https://swagger.io/tools/swagger-ui/)
- [OpenAPI 3.0 Guide](https://swagger.io/resources/articles/best-practices-in-api-design/)
- [OpenAPI by Example](https://www.youtube.com/watch?v=aaFDBgPdXw4)
- [Postman OpenAPI Integration](https://learning.postman.com/docs/integrations/available-integrations/working-with-openapi/)
- [Roadmap.sh - API Design](https://roadmap.sh/api-design)
