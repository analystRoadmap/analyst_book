# Retrieval-Augmented Generation (RAG)

| Грейд | Роль |
|-------|------|
| 🟠 Senior | 🅰️ SA |

> RAG объединяет поиск в корпоративных знаниях с генеративными возможностями LLM. Это позволяет создавать безопасные системы, которые работают с актуальной корпоративной информацией.

## Что такое RAG

**RAG** = Retrieval (поиск) + Augmented (дополненный) + Generation (генерация)

**Идея:** вместо того, чтобы полагаться только на знания модели, загрузить релевантные документы из базы и использовать их для генерации ответа.

```
Традиционный LLM:
Вопрос: "Какая зарплата для роли Data Scientist?"
LLM: "Обычно $80K-150K в США" (обучена на данных до 2023)
Проблема: Может быть неактуально для вашей компании

RAG подход:
Вопрос: "Какая зарплата для роли Data Scientist?"
1. Поиск: найти документ "Salary Guide 2026"
2. LLM читает найденный документ
3. LLM: "Для Data Scientist: $120K-160K (по вашему гайду 2026)"
Результат: Актуально и надежно
```

## Архитектура RAG системы

```
Компоненты:

1. Корпус документов
   - Требования
   - Спецификации
   - Гайды
   - Процессы
   └─ Обновляется регулярно

2. Embeddings и индекс
   - Текст преобразуется в вектора (embeddings)
   - Вектора хранятся в векторной БД
   └─ Быстрый поиск похожих документов

3. Retriever (поиск)
   - При вопросе: найти 3-5 похожих документов
   - Использует semantic поиск (не keyword matching)
   └─ Результат: релевантные фрагменты

4. LLM
   - Читает найденные фрагменты
   - Генерирует ответ на основе контекста
   └─ Результат: точный, обоснованный ответ

5. Chat интерфейс
   - Пользователь задает вопрос
   - Система возвращает ответ + источники
   └─ Результат: transparent и traceable
```

## Процесс RAG

### Фаза 1: Подготовка документов

```
Шаг 1: Загрузить документы
- Требования (PDF, Markdown, Google Docs)
- Спецификации
- Гайды и процессы

Шаг 2: Разделить на chunks (фрагменты)
- Каждый chunk ~500 слов
- Overlap между chunks (для контекста)
- Пример chunk:
  "Требование 2.1: Система должна поддерживать...
   Входные параметры: user_id, product_id
   Выходные данные: recommendation_list
   Требуемое время ответа: < 200ms"

Шаг 3: Создать embeddings
- Каждый chunk преобразуется в вектор (768-1536 dimensions)
- Embedding отражает семантическое значение текста
```

### Фаза 2: Индексирование

```
Шаг 1: Сохранить embeddings в векторной БД
- Pinecone, Weaviate, Milvus, Qdrant

Пример структуры:
{
  "id": "req_2_1_0",
  "text": "Требование 2.1: Система должна поддерживать...",
  "embedding": [0.124, -0.456, 0.789, ...],  // 768 чисел
  "metadata": {
    "source": "requirements.pdf",
    "page": 5,
    "section": "API Requirements"
  }
}

Шаг 2: Построить индекс
- Структура данных для быстрого поиска
- Результат: < 10ms для поиска 5 похожих документов
```

### Фаза 3: Retrieval при вопросе

```
Пользователь: "Какие требования к API?"

Шаг 1: Создать embedding вопроса
- Тот же процесс как для документов
- Embedding вопроса: [0.1, -0.4, 0.8, ...]

Шаг 2: Найти похожие embeddings в индексе
- Similarity search (cosine similarity)
- Результат: top 5 документов с наивысшей similarity

Результаты:
1. "Требование 2.1: API должна...", similarity: 0.92
2. "Требование 2.2: Аутентификация...", similarity: 0.88
3. "Требование 2.3: Rate limiting...", similarity: 0.85
4. ...
```

### Фаза 4: Генерация ответа

```
LLM получает:
1. Исходный вопрос
2. Найденные документы (context window)
3. Инструкция: "Ответь на основе этих документов"

Пример инструкции:
"На основе следующих документов ответь на вопрос.
Если информация отсутствует, скажи 'не найдено'.
Всегда цитируй источник."

LLM генерирует:
"Согласно требованию 2.1, API должна:
- Использовать REST протокол
- Поддерживать HTTPS
- Возвращать JSON ответы
- Иметь rate limit 1000 req/min

Источник: requirements.pdf, страница 5"
```

## Практическая реализация

### Пример: RAG система для корпоративных требований

```
Цель: Аналитик может спросить о любом требовании и получить точный ответ

Документы:
- technical_requirements.pdf (50 страниц)
- API_specification.md (20 страниц)
- Security_guidelines.md (15 страниц)
- Processes_guide.md (30 страниц)

Система RAG:
1. Загрузить документы
2. Разделить на chunks по 500 слов
3. Создать embeddings (OpenAI, Hugging Face)
4. Сохранить в Pinecone

Использование:
Аналитик: "Какое требуемое время ответа для API?"
Система:
1. Найдет chunks где упомянуто "response time"
2. Найдет chunks где упомянуто "performance"
3. LLM: "Согласно requirements.pdf, требуемое время ответа P95 < 200ms.
          Для операций, требующих обработки > 10K данных, допускается < 500ms."
```

### Требования для RAG системы

```
Требование: RAG система для корпоративной документации

Компоненты:
1. Document ingestion
   - Загрузка PDF, Markdown, Google Docs
   - Автоматическое обновление при изменении

2. Embeddings
   - Model: OpenAI text-embedding-3-small
   - Dimension: 1536
   - Update: автоматически при новых документах

3. Vector store
   - Pinecone (для масштабируемости)
   - Replication factor: 3 (для надежности)

4. Retriever
   - Top K: 5 (вернуть 5 похожих документов)
   - Similarity threshold: 0.7 (отсеяь low-quality matches)
   - Timeout: 5 seconds

5. LLM
   - Model: Claude 3.5 (за точность)
   - Temperature: 0 (детерминированно)

6. Safety & Access Control
   - Документы с меткой "confidential" → только для авторизованных
   - Audit logging (кто спросил что)
   - DLP (Data Loss Prevention) check перед ответом

Качество:
- Accuracy: >= 90% (правильные ответы)
- Precision: >= 85% (найденные documents релевантны)
- Latency: < 3 seconds (total response time)
- Hallucination rate: < 5% (LLM не выдумывает)

Мониторинг:
- Daily: accuracy на тестовом наборе questions
- Weekly: review неправильных ответов
- Monthly: update документов, переиндексирование
```

## Проблемы и решения

### Проблема: Низкое качество retrieval

```
Проблема: Система находит документы, не относящиеся к вопросу
Вопрос: "Какая зарплата для Data Scientist?"
Найденные документы: про обработку изображений (ML тема, но не зарплата)

Решение:
1. Использовать better embedding model (OpenAI 3-large вместо 3-small)
2. Добавить metadata filtering (filter by section: "HR" вместо "Engineering")
3. Hybrid search (combine semantic + keyword search)
4. Re-rank результаты с cross-encoder моделью
```

### Проблема: Старевание документов

```
Проблема: LLM отвечает на основе старых документов
Вопрос: "Какие текущие требования к производительности?"
Ответ: использует спецификацию от 6 месяцев назад

Решение:
1. Добавить metadata "last_updated": дата
2. Приоритизировать свежие документы в retrieval
3. Автоматическое переиндексирование при изменении (webhook)
4. Version control документов (keep history)
```

### Проблема: Слишком много контекста

```
Проблема: Найдено 10 релевантных документов, но LLM теряется в контексте

Решение:
1. Limit к 3-5 документам
2. Использовать LLM с larger context window (Claude 200K токенов)
3. Summarize документы перед отправкой LLM
4. Hierarchical retrieval (find section, then subsection)
```

### Проблема: Confidential информация

```
Проблема: RAG система содержит чувствительные данные
Требование: Некоторые документы доступны только определенным пользователям

Решение:
1. Add access control metadata
   ```
   {
     "id": "salary_2026",
     "text": "Data Scientist: $120K-160K",
     "access_level": ["HR", "Finance"]  // только эти роли
   }
   ```

2. Filter retrieval results по текущему user role
   ```
   def retrieve(query, user_role):
     results = vector_db.search(query, top_k=5)
     filtered = [r for r in results if user_role in r.metadata.access_level]
     return filtered
   ```

3. Audit logging: логировать все вопросы и ответы
```

## Лучшие практики

1. **Качество документов** — garbage in, garbage out
   - Документы должны быть чистые, структурированные
   - Регулярное обновление
   - Версионирование

2. **Оптимизация chunks** — не слишком маленькие, не слишком большие
   - Рекомендация: 300-600 слов
   - Overlap: 50-100 слов между chunks

3. **Embedding модель** — важна для качества
   - OpenAI (лучшее, но платное)
   - Hugging Face (бесплатное, но похуже)
   - Local models (для конфиденциальности)

4. **Retrieval стратегия** — гибрид лучше всего
   - Semantic search (embeddings)
   - Keyword search (BM25)
   - Metadata filtering
   - Re-ranking

5. **Мониторинг и feedback** — постоянное улучшение
   - Отслеживать quality метрики
   - Собирать user feedback
   - Регулярно retrain embeddings

## 📚 Ресурсы для изучения

- [LangChain RAG Documentation](https://python.langchain.com/docs/use_cases/question_answering/) — framework для RAG
- [LLamaIndex](https://www.llamaindex.ai/) — еще один RAG framework
- [OpenAI Embeddings API](https://platform.openai.com/docs/guides/embeddings) — embeddings от OpenAI
- [Vector Databases](https://www.pinecone.io/) — Pinecone для хранения embeddings
- [RAG Best Practices](https://arxiv.org/abs/2309.01431) — research paper про RAG
