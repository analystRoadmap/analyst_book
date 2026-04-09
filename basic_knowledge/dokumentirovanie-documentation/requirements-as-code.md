# Requirements-as-Code

| Грейд | Роль |
|-------|------|
| 🟠 Senior | 🅰️ SA |

> **Requirements-as-Code (RaC)** — парадигма, при которой требования хранятся, версионируются и управляются как исходный код, получая преимущества git-контроля, CI/CD, автоматизации и интеграции с разработкой.

## Революция в управлении требованиями

### Традиционный подход

```
requirements.docx
  ↓ [Часто обновляется вручную]
  ↓ [Сложно отследить изменения]
  ↓ [Расходится с кодом]
  ↓ [Никто не читает]
  →→→ DEAD DOCUMENT
```

### Requirements-as-Code подход

```
requirements/
├─ feature-search.md
├─ feature-filters.md
├─ nfr-performance.md
└─ nfr-security.md
  ↓ [Git контроль]
  ↓ [Pull requests для review]
  ↓ [CI/CD валидирует требования]
  ↓ [Автоматизация генерирует документы]
  →→→ ЖИВОЙ ИСТОЧНИК ИСТИНЫ
```

## Ключевые отличия RaC от традиционного подхода

| Аспект | Традиционно | RaC |
|--------|------------|-----|
| **Формат** | Word, PDF документы | Markdown, Gherkin, YAML |
| **Версионирование** | "version_final_v3" | Git с историей |
| **Обзор изменений** | Email с комментариями | Pull Request с inline comments |
| **Связь с кодом** | Отдельная папка | В одном репо рядом с кодом |
| **Валидация** | Ручная | CI/CD автоматическая |
| **Автоматизация** | Сложно | Легко (скрипты) |

## Структура Requirements-as-Code проекта

### Вариант 1: Отдельная папка в репозитории

```
my-product/
├─ src/ (исходный код)
├─ tests/ (тесты)
├─ docs/ (документация)
├─ requirements/ ← RaC находится здесь
│  ├─ features/
│  │  ├─ search.md
│  │  ├─ filters.md
│  │  └─ sorting.md
│  ├─ nfr/
│  │  ├─ performance.md
│  │  ├─ security.md
│  │  └─ scalability.md
│  ├─ user-stories/
│  │  ├─ user-story-001.md
│  │  └─ user-story-002.md
│  └─ README.md (индекс)
├─ .github/
│  └─ workflows/ (CI/CD для валидации требований)
└─ README.md
```

### Вариант 2: Requirements рядом с кодом (Domain-Driven)

```
my-product/
├─ src/
│  ├─ search/
│  │  ├─ requirements.md ← требования для модуля поиска
│  │  ├─ SearchService.java
│  │  └─ SearchTest.java
│  ├─ filters/
│  │  ├─ requirements.md ← требования для фильтров
│  │  ├─ FilterService.java
│  │  └─ FilterTest.java
│  └─ shared/
│     └─ nfr-requirements.md (глобальные требования)
└─ tests/
```

## Инструменты для Requirements-as-Code

### 1. Markdown + Git

**Простейший и самый популярный вариант**

Формат:
```markdown
# Feature: User Search

## Description
Users should be able to search for other users by name.

## Acceptance Criteria
- [x] Search works with partial names
- [x] Results show user avatar, name, and profile link
- [x] Search is real-time (< 300ms latency)

## Performance Requirements
- Max response time: 500ms for 1M users
- Min 99.9% availability

## Security Requirements
- Don't expose inactive users
- Log all searches for audit

## Related
- [Task #123](../../../jira/task-123.md)
- [User Story #45](../user-stories/story-045.md)
```

### 2. Gherkin (BDD - Behavior Driven Development)

**Для описания поведения системы на языке, понятном всем**

```gherkin
Feature: Search for users

  Scenario: User searches by name
    Given user is logged in
    When user enters "john" in search field
    Then system should return all users with "john" in name
    And results should appear within 300ms

  Scenario: Search with no results
    Given user is logged in
    When user searches for "xyzabc123"
    Then system should show "No users found"
    And log should record this search
```

### 3. YAML для структурированных требований

```yaml
requirement:
  id: REQ-001
  title: "User search by name"
  status: Approved
  priority: High
  author: Alice
  created: 2024-01-10
  updated: 2024-01-15

acceptance_criteria:
  - "Search accepts partial names"
  - "Results sorted alphabetically"
  - "Max 100 results displayed"

nfr:
  performance:
    max_response_time: "300ms"
    min_availability: "99.9%"
  security:
    - "Exclude inactive users"
    - "Log searches for audit"
```

## CI/CD валидация требований

### Пример GitHub Actions

```yaml
name: Validate Requirements

on: [push, pull_request]

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2

      # Проверить, что все требования заполнены
      - name: Check required fields
        run: |
          find requirements/ -name "*.md" -exec \
          grep -l "^# " {} \; && \
          grep -l "Acceptance Criteria" {} \; && \
          grep -l "Status:" {} \; || exit 1

      # Проверить, что нет дублей ID
      - name: Check for duplicate IDs
        run: |
          IDS=$(grep -r "id:" requirements/ | cut -d: -f3 | sort)
          if [ "$(echo "$IDS" | uniq -d)" != "" ]; then
            echo "Duplicate IDs found!"
            exit 1
          fi

      # Генерировать документацию
      - name: Generate documentation
        run: |
          python scripts/generate_docs.py \
            --input requirements/ \
            --output docs/requirements.html

      # Отправить в Confluence
      - name: Update Confluence
        run: |
          python scripts/sync_to_confluence.py \
            --token ${{ secrets.CONFLUENCE_TOKEN }} \
            --space PROJECT
```

## Автоматизация на основе Requirements-as-Code

### 1. Генерирование документации

```python
# Скрипт генерирует HTML из Markdown требований
python scripts/generate_docs.py \
  --input requirements/ \
  --output docs/requirements.html \
  --format html
```

### 2. Синхронизация с Jira

```python
# Требования автоматически создают задачи в Jira
python scripts/sync_to_jira.py \
  --requirements requirements/features/*.md \
  --project MYPROJECT \
  --epic "Feature Development"
```

### 3. Трейсируемость (Traceability)

```python
# Связать требование → тест → код
# Если требование изменилось → перепроверить все связанные
python scripts/generate_traceability.py \
  --requirements requirements/features/*.md \
  --tests tests/*.test.js \
  --code src/**/*.js \
  --output traceability_matrix.csv
```

## Best Practices для Requirements-as-Code

### Файлы и структура

✓ **Одно требование = один файл** (или логическая группа)
✓ **Понятные имена файлов** (feature-search.md, nfr-performance.md)
✓ **Четкая иерархия** (features/, nfr/, user-stories/)
✓ **README на каждом уровне** (что находится в этой папке)

### Формат и содержание

✓ **Markdown для читаемости** (легче читать в GitHub, простой синтаксис)
✓ **YAML фронт-матер для метаданных** (статус, автор, дата, ID)
✓ **Gherkin для сценариев** (бизнес могут понять)
✓ **Ссылки вместо копирования** (не повторяйся)

### Версионирование и обзор

✓ **Pull requests для всех изменений** (как в коде)
✓ **Минимум 2 approvals** для важных требований
✓ **Changelog** (что и когда изменилось)
✓ **Tags для релизов** (git tag v1.0-requirements)

## Проблемы и их решения

| Проблема | Решение |
|----------|---------|
| **Требования старые и неправильные** | CI/CD проверяет дату обновления, требует ежегодный ревью |
| **Сложно слияние больших документов** | Маленькие файлы вместо одного большого |
| **Разработчики игнорируют требования** | Требования в одном репо с кодом, не в отдельной папке |
| **Нет связи требование↔код** | Автоматическая генерация трейсируемости |

## Миграция на Requirements-as-Code

**Этап 1 (неделя 1-2):** Настроить структуру репозитория
**Этап 2 (неделя 3-4):** Перенести существующие требования в Markdown
**Этап 3 (неделя 5):** Настроить CI/CD валидацию
**Этап 4 (неделя 6+):** Автоматизация (Jira sync, документация)

## 📚 Ресурсы для изучения

- **Gherkin Syntax** — BDD language for requirements
- **Keep a Changelog** — управление версиями документов
- **Living Documentation** — требования как часть кода
- **GitHub Actions Documentation** — CI/CD для требований
- **Architectural Decision Records (ADR)** — документирование решений как код
