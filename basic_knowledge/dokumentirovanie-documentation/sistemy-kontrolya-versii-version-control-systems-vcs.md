# Системы контроля версий (Version Control Systems, VCS)

| Грейд | Роль |
|-------|------|
| 🔵 Middle | 🅰️ SA |

> **VCS (Version Control Systems)** — инструменты, которые отслеживают все изменения в файлах, сохраняют историю и позволяют командам работать одновременно без конфликтов.

## Почему аналитику нужно понимать VCS?

**Сценарий без VCS:**
```
version1_final.doc
version1_final_FINAL.doc
version1_final_FINAL_v2.doc
version1_final_FINAL_v2_FINAL.doc  ← Какая актуальна?

Кто менял? Когда? Почему? НЕИЗВЕСТНО
```

**С VCS:**
```
git log --oneline
a3f2c1d - Update user stories for payment feature (Alice, 2024-01-15)
b2e1d0c - Fix typo in non-functional requirements (Bob, 2024-01-14)
c1f0e9b - Initial requirements document (Alice, 2024-01-10)

Кто? ALICE/BOB
Когда? ТОЧНАЯ ДАТА
Почему? ОПИСАНО В СООБЩЕНИИ КОММИТА
```

## Основные концепции VCS

### 1. Repository (Хранилище)
Центральное место, где хранятся все версии файлов

### 2. Commit (Фиксация)
Снимок (snapshot) изменений с сообщением

```
Commit: "Add acceptance criteria for login feature"
Файлы: requirements/login.md (+45 строк, -3 строки)
Автор: Alice
Дата: 2024-01-15 10:30
```

### 3. Branch (Ветка)
Отдельная линия разработки

```
main (основная версия)
  ├─ feature/search (разработка функции поиска)
  ├─ feature/filters (разработка фильтров)
  └─ bugfix/ui-issue (исправление баги)
```

### 4. Merge (Слияние)
Объединение изменений из одной ветки в другую

```
feature/search (200 строк новых требований)
           ↓ merge
main (теперь содержит все требования для search)
```

### 5. Conflict (Конфликт)
Когда два человека меняют одно место

```
Alice:  "Пользователь видит результаты"
Bob:    "Пользователь видит результаты и может отфильтровать"

← VCS: Конфликт! Нужно вручную выбрать или объединить
```

## Популярные VCS

### Git
**Самый популярный**, распределённый

```
Структура:
- Local repository (на компьютере)
- Remote repository (на GitHub/GitLab)
- Staging area (что будет в следующем коммите)
```

**Команды:**
```bash
git clone [url]              # Скачать репозиторий
git status                   # Что изменилось?
git add [файл]               # Добавить в staging
git commit -m "сообщение"    # Создать коммит
git push                     # Отправить на сервер
git pull                     # Скачать изменения с сервера
git branch [имя]             # Создать новую ветку
git merge [ветка]            # Слить ветку в текущую
```

### Subversion (SVN)
**Более старый**, централизованный (всё на сервере)

### Mercurial (Hg)
Альтернатива Git, похож по концепции

## Git Workflow для документирования требований

### 1. Простой workflow (Shared Repository)

```
Шаг 1: git clone [repository]
       (скачиваешь существующие требования)

Шаг 2: Редактируешь требования в локальном файле

Шаг 3: git add requirements/
       (добавляешь изменения в staging)

Шаг 4: git commit -m "Add performance requirements for search"
       (сохраняешь с сообщением)

Шаг 5: git push
       (отправляешь на сервер)
```

### 2. Feature Branch workflow (с Pull Requests)

**Более безопасный подход для больших команд:**

```
Шаг 1: git checkout -b feature/new-requirements
       (создаешь новую ветку для своей работы)

Шаг 2: [Редактируешь файлы локально]

Шаг 3: git add . && git commit -m "Add new requirements"

Шаг 4: git push origin feature/new-requirements
       (отправляешь ветку на сервер)

Шаг 5: [Создаешь Pull Request на GitHub/GitLab]

Шаг 6: [Другие смотрят, комментируют, утверждают]

Шаг 7: git merge
       (слияние в main после одобрения)
```

## Применение VCS для документации

### Отслеживание изменений требований

**До версионирования:**
```
requirements.doc версия 5
"Что изменилось с версии 3?"
"Кто менял?"
"Когда это был развертан?"
→ НЕИЗВЕСТНО
```

**С Git:**
```bash
git log --follow requirements/features.md
# Показывает все коммиты, которые трогали этот файл

git diff v1.0 v1.1 requirements/features.md
# Показывает ровно что изменилось между версиями

git blame requirements/features.md
# Показывает кто менял каждую строку и когда
```

### Совместная работа над требованиями

```
Alice работает над Feature A
     ↓ (feature/feature-a)
Bob работает над Feature B
     ↓ (feature/feature-b)

Обе ветки могут быть в разных состояниях
Когда готовы → merge в main
Если конфликт → разруливаем вместе
```

### Версионирование релизов

```
main branch
├─ v1.0 (requirements для первого релиза)
├─ v1.1 (updates после v1.0)
├─ v2.0 (новые требования)
└─ v2.1 (текущая версия)

git tag v2.0 — легко вернуться к требованиям v2.0
```

## Requirements-as-Code подход

Новая парадигма: требования хранятся в Git как код

### Пример структуры

```
/requirements
├─ /features
│  ├─ feature-001.md (Feature: Search)
│  ├─ feature-002.md (Feature: Filters)
│  └─ feature-003.md (Feature: Sorting)
├─ /nfr
│  ├─ performance.md
│  ├─ security.md
│  └─ scalability.md
├─ /user-stories
│  ├─ story-001.md
│  └─ story-002.md
└─ README.md (Оглавление)
```

### Преимущества Requirements-as-Code

✓ **Версионирование** — git отслеживает все изменения
✓ **Совместная разработка** — pull requests для review
✓ **Интеграция с кодом** — требования рядом с реализацией
✓ **CI/CD** — можно проверять требования автоматически
✓ **History** — полная история изменений
✓ **Branches** — разные требования для разных версий продукта

## Лучшие практики использования VCS

### Commits

✓ **Информативные сообщения**
```
✓ "Add acceptance criteria for payment processing"
✓ "Update performance requirements from review"
❌ "Fix stuff"
❌ "Changes"
```

✓ **Атомарные коммиты** (одно изменение = один коммит)
```
✓ 1 коммит: "Add search feature requirements"
❌ 1 коммит: "Add search, update filters, fix typos in 10 файлах"
```

### Branches

✓ **Четкие имена**
```
feature/payment-integration
bugfix/typo-in-requirements
docs/update-guidelines
release/v2.0
```

### Pull Requests

✓ **Обзор перед слиянием** — минимум один approve
✓ **Описание** — что и почему менялось
✓ **Обсуждение** — комментарии прямо в PR

## Типичные ошибки

❌ **Больших файлов в Git** (бинарные файлы, видео)
❌ **Конфиденциальной информации** (пароли, ключи)
❌ **Невозможных для слияния форматов** (Word документы вместо Markdown)
❌ **Плохих сообщений commit'ов** — потом никто не поймет

## 📚 Ресурсы для изучения

- **Pro Git (книга)** — полное руководство по Git
- **GitHub Learning Lab** — интерактивные курсы
- **Atlassian Git Tutorials** — подробные объяснения
- **commitizen** — help с написанием структурированных commit messages
- **pre-commit** — автоматическая проверка перед commit
