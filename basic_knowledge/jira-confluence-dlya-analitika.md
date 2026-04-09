# JIRA и Confluence для аналитика

| Грейд | Роль |
|-------|------|
| 🟢 Junior | 🅱️🅰️ BA+SA |

> **Jira + Confluence** — интегрированная пара инструментов: Jira управляет задачами и требованиями, Confluence хранит документацию, вместе они создают единую экосистему управления проектом.

## Почему аналитику нужно владеть Jira и Confluence?

**Без них:**
```
Требования → где-то в Word документе
Задачи → в email письмах
Процессы → только в голове у аналитика
Кто что делает? НЕИЗВЕСТНО
```

**С Jira + Confluence:**
```
Требования → Confluence, ссылается из Jira
Задачи → Jira, привязаны к требованиям
Процессы → Confluence (BPMN, описания)
История → История версий, комментарии, обсуждения
```

## Jira для аналитика

### Основные элементы Jira

**Проект (Project)**
Основной контейнер
```
Project: "E-Commerce Platform"
├─ Epic 1: "User Authentication"
├─ Epic 2: "Product Search"
└─ Epic 3: "Shopping Cart"
```

**Epic**
Большая фича или инициатива
```
Epic: "User Authentication"
- Timeline: Q1 2024
- Team: 5 человек
- Status: In Progress
```

**User Story**
Описание функции с точки зрения пользователя
```
Story: "As a user, I want to reset password"

Acceptance Criteria:
- [ ] User can click "Forgot Password"
- [ ] System sends reset link to email
- [ ] Link expires in 24 hours
- [ ] User sets new password successfully
```

**Task**
Техническая задача (без пользовательской ценности)
```
Task: "Refactor authentication module"
- Assignee: Developer A
- Story Points: 5
- Status: In Progress
```

**Bug**
Ошибка в системе
```
Bug: "Login button doesn't work on mobile"
- Priority: High
- Affected Version: v1.0
- Reporter: QA Engineer
```

### Как организовать Jira для аналитики

**Хороший Jira board для Agile:**

```
To Do  | In Progress | In Review | Testing | Done
───────────────────────────────────────────────
REQ-1  │   REQ-5    │   REQ-8  │ REQ-10 │ REQ-15
REQ-2  │   REQ-6    │   REQ-9  │        │ REQ-16
REQ-3  │   REQ-7    │          │        │
REQ-4  │            │          │        │
```

### User Story Template в Jira

```
Title: [Clear, concise description]

Description:
As a [role/user type],
I want to [action/feature],
so that [benefit/value]

Acceptance Criteria:
- [ ] Criterion 1
- [ ] Criterion 2
- [ ] Criterion 3

Acceptance Tests:
Given [precondition]
When [action]
Then [expected result]

Non-Functional Requirements:
- Performance: < 300ms response time
- Security: HTTPS only
- Availability: 99.9%

Related Issues:
- Links to related requirements
- Dependencies
- Blocked by / Blocks

Attachments:
- Wireframes
- Design mockups
- Process diagrams
```

### Полезные поля для аналитика

| Поле | Зачем | Как использовать |
|------|-------|------------------|
| **Sprint** | На какой спринт планируется | Планирование 2 недели вперед |
| **Story Points** | Как долго разрабатывать | 1-13 (Fibonacci), для оценки |
| **Priority** | Как важно | Highest/High/Medium/Low |
| **Label** | Категоризация | "payment", "frontend", "security" |
| **Custom Field: Approved By** | Кто утвердил требование | Трейсируемость |
| **Linked Issues** | Связи с другими задачами | Зависимости, duplicate, relates to |

### Workflow в Jira

Типичный workflow для User Story:

```
Backlog (To Do)
    ↓ [Sprint Planning]
Ready for Development
    ↓ [Dev берет в работу]
In Progress
    ↓ [Pull Request, code review]
In Review
    ↓ [Merge в main]
Testing
    ↓ [QA проверил, ok]
Done
```

---

## Confluence для аналитика

### Основные элементы Confluence

**Space**
Большая область (проект)
```
Project A Space
├─ Requirements
├─ Processes
├─ Design
└─ Meeting Notes
```

**Page**
Основной элемент (документ)
```
Page: "User Authentication Requirements"
- Table of Contents автоматический
- History изменений
- Комментарии для обсуждения
```

**Template**
Шаблон для быстрого создания страниц
```
Template: "Requirements Template"
├─ Title
├─ Overview
├─ Acceptance Criteria
├─ NFR
├─ Related
└─ Attachments
```

### Структура Confluence для проекта

```
Project Space
├─ 📋 Requirements
│  ├─ Feature 1: User Registration
│  ├─ Feature 2: User Profile
│  ├─ Feature 3: Password Reset
│  └─ NFR: Performance & Security
├─ 🔄 Processes
│  ├─ As-Is: Current Auth Process
│  ├─ To-Be: New Auth Process
│  └─ Integration Points
├─ 🎨 Design
│  ├─ Wireframes (ссылка на Figma)
│  ├─ Design System
│  └─ UI Components
├─ 📊 Analytics
│  ├─ User Flow Diagram
│  ├─ Data Model
│  └─ API Contract
└─ 📝 Meeting Notes
   ├─ Requirements Review (2024-01-15)
   ├─ Design Review (2024-01-16)
   └─ Stakeholder Sync (2024-01-17)
```

### Как писать хорошие страницы в Confluence

**Структура:**
1. **Title** — четкое, не сокращения
2. **Table of Contents** — автоматическое (если есть # заголовки)
3. **Overview** — одно предложение о чем страница
4. **Content** — основная информация
5. **Attachments** — диаграммы, диаграммы, примеры
6. **Related Pages** — ссылки на связанное

**Пример страницы требований:**

```markdown
# User Registration Requirements

## Overview
This document describes the requirements for the user registration feature,
including acceptance criteria, non-functional requirements, and edge cases.

## Background
Users need a simple way to create an account without friction.
Current signup takes 5 steps and has 40% bounce rate.

## User Stories

### Story 1: User can register with email
- As a new user, I want to sign up with my email...

### Story 2: Email validation
- As the system, I want to validate email format...

## Non-Functional Requirements

### Performance
- Registration page loads in < 1 second
- Form submission completes in < 500ms

### Security
- Passwords hashed with bcrypt
- Rate limiting: max 5 attempts per minute

## Related
- [Design Mockups](https://figma.com/...)
- [Jira Epic](https://jira.../browse/PROJ-123)
- [Data Model](http://confluence.../data-model)
```

---

## Интеграция Jira ↔ Confluence

### 1. Embedding Issues in Confluence

В Confluence странице можно встраивать Jira issues:

```
// В Confluence укажи:
{jira:columns=key,summary,type|issues=JQL|maxResults=10}

// Это покажет таблицу с Jira tasks:
| KEY     | Summary                    | Type  |
|---------|---------------------------|-------|
| REQ-1   | User registration form    | Story |
| REQ-2   | Email validation          | Task  |
| REQ-3   | Password reset flow       | Story |
```

### 2. Linking Confluence to Jira

В Jira issue указать ссылку на Confluence требование:

```
Issue: REQ-1
Link Type: "Relates to"
Confluence Page: "User Registration Requirements"
```

### 3. Creating Jira Issues from Confluence

Можно создавать Jira issues прямо из Confluence:

```
В Confluence странице:
[Create Jira Issue] → Opens Jira pre-populated form
Автоматически заполняется:
- Project
- Summary (из контекста Confluence)
- Description (ссылка на Confluence страницу)
```

---

## Best Practices для BA в Jira + Confluence

### Jira Best Practices

✓ **User Stories одного размера** (5-13 story points)
✓ **Четкие Acceptance Criteria** (тестируемые условия)
✓ **Привязка к Confluence требованиям** (одна ссылка)
✓ **Регулярное обновление статуса** (видимость для всех)
✓ **Комментарии вместо переписки** (всё в одном месте)

### Confluence Best Practices

✓ **Одна главная версия документа** (не копии в разных местах)
✓ **Дата обновления видна** (сколько свежа информация?)
✓ **Ссылки на Jira issues** (трассируемость)
✓ **Диаграммы и примеры** (визуализация)
✓ **Разумный размер страниц** (не 50 страниц на один документ)

---

## Типичные ошибки

❌ **Требования только в Jira, без подробности**
→ Используй Confluence для подробных требований, Jira для трекинга

❌ **Разные версии документа в разных местах**
→ Одна версия истины в Confluence с ссылкой из Jira

❌ **Никто не читает Confluence**
→ Делай содержимое ценным (примеры, диаграммы, не скучный текст)

❌ **Jira без контекста** ("Fix bug", "Do something")
→ Всегда объясняй ПОЧЕМУ, ссылайся на требование

---

## 📚 Ресурсы для изучения

- **Atlassian Jira Documentation** — полное руководство по Jira
- **Atlassian Confluence Guide** — как использовать Confluence
- **Agile Documentation Best Practices** — как документировать в Jira
- **Jira Workflow Templates** — готовые workflow'ы
- **Confluence Template Gallery** — примеры хороших страниц
