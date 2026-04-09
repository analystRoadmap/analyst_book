# Машинное обучение (Machine Learning)

| Грейд | Роль |
|-------|------|
| 🟠 Senior | 🅰️ SA |

> Машинное обучение позволяет автоматизировать сложные задачи классификации, регрессии и рекомендаций. Аналитику важно понимать типы моделей и их метрики качества для корректной постановки задач.

## Типы машинного обучения

### Supervised Learning (обучение с учителем)

**Задача:** предсказать target переменную на основе признаков.

Нужны исторические примеры (features → target).

**Примеры:**
- Классификация: спам или нет? (features: слова в письме → target: yes/no)
- Регрессия: цена товара? (features: размер, материал → target: цена)

### Unsupervised Learning (обучение без учителя)

**Задача:** найти паттерны в данных без целевой переменной.

**Примеры:**
- Кластеризация: разделить клиентов на сегменты (признаки: возраст, доход, покупки)
- Dimensionality reduction: сжать 100 признаков в 10 главных компонентов

### Reinforcement Learning (обучение через вознаграждение)

**Задача:** агент учится через награды и наказания.

**Примеры:**
- AlphaGo: агент учится играть в Go через миллионы игр
- Рекомендация: система учится давать лучшие рекомендации через клики пользователя

## Основные типы моделей

### Классификация

**Задача:** предсказать категорию (yes/no, spam/ham, category A/B/C).

**Алгоритмы:**
- Logistic Regression — простая, интерпретируемая
- Decision Tree — легко понять, может быть slow
- Random Forest — ensemble, быстро, хорошее качество
- SVM — работает на высокомерных данных
- Neural Network — очень гибкая, требует много данных

**Метрики:**
- **Accuracy** = (TP + TN) / (TP + TN + FP + FN) — % правильных ответов
- **Precision** = TP / (TP + FP) — из предсказанных положительных, сколько правильных
- **Recall** = TP / (TP + FN) — из всех положительных, сколько найдено
- **F1-score** = 2 * (precision * recall) / (precision + recall) — гармоническое среднее
- **AUC-ROC** — кривая под графиком (0-1, выше лучше)

**Пример требования:**
```
Модель: Email Spam Detection

Требования:
- Accuracy >= 95%
- Precision >= 98% (мало false positives — не удаляем важные письма)
- Recall >= 90% (ловим большинство спама)

Входные данные:
- Тема письма
- Тело письма
- Отправитель
- Наличие ссылок и вложений

Выходные данные:
- Вероятность спама (0-100%)
- Классификация: "spam" если > 0.7, иначе "ham"
```

### Регрессия

**Задача:** предсказать число (цена, количество, время).

**Алгоритмы:**
- Linear Regression — простая, быстро
- Ridge/Lasso — linear с регуляризацией (против overfitting)
- Decision Tree Regression — non-linear
- Gradient Boosting — очень мощная (XGBoost, LightGBM)
- Neural Network — максимальная гибкость

**Метрики:**
- **MAE (Mean Absolute Error)** = среднее |y_pred - y_true|
- **RMSE (Root Mean Squared Error)** = sqrt(mean((y_pred - y_true)²))
- **R² (R-squared)** = 1 - (SS_res / SS_tot) — % объясненной дисперсии (0-1)

**Пример требования:**
```
Модель: Sales Forecast

Требования:
- RMSE < 5% от среднего значения
- R² >= 0.85
- Прогноз на 30 дней вперед

Входные данные:
- История продаж (daily sales за 2 года)
- День недели, месяц, праздники
- Маркетинг кампании
- Сезонность

Выходные данные:
- Прогноз продаж на каждый день следующего месяца
- Доверительный интервал (95%)
```

### Кластеризация

**Задача:** разделить данные на группы без целевой переменной.

**Алгоритмы:**
- K-means — быстро, нужно знать количество кластеров
- DBSCAN — находит кластеры любой формы
- Hierarchical Clustering — дерево кластеров

**Метрики:**
- **Silhouette Score** (−1 to 1, выше лучше) — насколько плотные и отделённые кластеры
- **Davies-Bouldin Index** (0 to ∞, ниже лучше) — разделение между кластерами
- **Inertia** (внутри-кластерная дисперсия)

**Пример требования:**
```
Модель: Customer Segmentation

Требования:
- Разделить 100,000 клиентов на 5 сегментов
- Silhouette Score >= 0.4
- Сегменты должны быть интерпретируемы

Входные данные (признаки):
- Lifetime Value (всего потрачено)
- Frequency (сколько раз покупал)
- Recency (дни с последней покупки)

Выходные сегменты:
1. VIP: высокий LTV, частые покупки, свежие
2. Loyal: хороший LTV, частые, но давно не покупал
3. At Risk: потратил, но перестал покупать
4. New: небольшой LTV, первая покупка недавно
5. Lost: старые клиенты, давно не активны
```

## Feature Engineering

**Feature Engineering** — это создание признаков (features) для обучения модели.

### Примеры

```
Raw data:
- customer_age: 45
- last_purchase_date: 2024-01-15
- total_spent: 1500

Feature engineering:

1. From dates:
   - days_since_purchase: 85 (2026-04-09 - 2024-01-15)
   - is_recent_customer: 0 (> 60 дней)
   - month_of_signup: 3

2. From numerical:
   - log_total_spent: 7.31 (log(1500))
   - avg_order_value: 150 (total_spent / num_orders)

3. From categories:
   - is_premium_customer: 1 (if total_spent > 1000)
   - country_region: 'EU' (from country code)
```

**Для аналитика:** хорошие признаки важнее сложного алгоритма. Потратьте 80% времени на feature engineering.

## Train, Validation, Test Split

```
Data: 10000 samples

Split:
- Train: 6000 (60%) — обучение модели
- Validation: 2000 (20%) — tuning гиперпараметров
- Test: 2000 (20%) — финальная оценка (не трогаем в процессе)

Process:
1. Обучить на Train
2. Проверить на Validation, выбрать лучший hyperparameters
3. Финальный тест на Test (это истина, которую отчитываем)

Важно: Test data должны быть абсолютно независимы!
```

## Метрики качества по типам задач

| Задача | Основная метрика | Дополнительная | Когда важна |
|--------|------------------|----------------|-----------|
| **Classification Binary** | Accuracy / F1 | Precision, Recall | Зависит от use case |
| **Classification Multi** | Macro F1 | Per-class precision/recall | Дисбаланс классов |
| **Regression** | MAE или RMSE | R² | Зависит от масштаба |
| **Ranking** | NDCG, MAP | Precision@K | Рекомендации |
| **Clustering** | Silhouette | Davies-Bouldin | Интерпретируемость |

## Проблемы ML моделей

### Class Imbalance

```
Проблема: 99% класс "не спам", 1% класс "спам"

Модель может учиться: "всегда предсказывай not spam"
Accuracy: 99% (правильно), но Recall для спама: 0%

Решение:
1. Oversampling: дублировать меньший класс
2. Undersampling: удалять из большего класса
3. Weighted loss: штраф за ошибку на меньшем классе
4. Use different metric: F1 или AUC-ROC вместо Accuracy
```

### Temporal Data

```
Проблема: Предсказание цен на акции
Данные: история цен последние 10 лет

НЕПРАВИЛЬНО:
- Перемешать данные случайно
- Train на 2015-2023, Test на 2015-2023 (смешано)
- Результат: модель видит будущее!

ПРАВИЛЬНО:
- Временной порядок важен
- Train на 2015-2023, Test на 2024 (только будущее)
- Time series cross-validation (rolling window)
```

## Развертывание модели

```
Этапы:

1. Batch Prediction (offline)
   - Раз в неделю/день
   - Обработать всех клиентов
   - Сохранить результаты в БД
   - Приложение читает результаты
   Pros: Дешево, простая
   Cons: Данные не свежие

2. Real-time Prediction (online)
   - Модель развернута как сервис
   - API эндпоинт принимает features
   - Возвращает предсказание в < 100ms
   Pros: Свежие данные
   Cons: Требует инфраструктура, дорого

3. Streaming
   - События в реальном времени (Kafka)
   - Модель обработает каждое событие
   - Результат отправляется куда-то (БД, очередь)
   Pros: Максимально свежие, масштабируемо
   Cons: Самое сложное, требует опыта
```

## Мониторинг ML моделей

**Data Drift:** входные данные изменились

```
Проблем: Модель была обучена на мобильных пользователях,
но внезапно 80% пользователей — десктоп.

Сигнал: Accuracy упадет, потому что distribution другая.

Решение: Retrain модель на новых данных.
```

**Model Decay:** качество модели со временем деградирует

```
Проблема: Каждый месяц качество на 1% хуже

Причина: Бизнес изменился, новые признаки важны, старые неактуальны

Мониторинг:
- Отслеживать качество метрик каждый месяц
- Если упадет > 5% → начать retraining
```

## Требование для ML модели в production

```
Модель: Product Recommendation

Требования:
1. Quality
   - Click-through rate increase: > 5%
   - Conversion rate increase: > 2%
   - Revenue per user: > 10%

2. Performance
   - Latency: P95 < 100ms
   - Throughput: 10000 req/sec

3. Monitoring
   - Daily model quality metrics
   - Alert если CTR упадет > 1% от baseline
   - Data drift detection

4. Deployment
   - Gradual rollout: 1% → 10% → 100%
   - A/B test: old model vs new model

5. Maintenance
   - Retrain monthly
   - Update training data with recent interactions
```

## 📚 Ресурсы для изучения

- [Scikit-learn Documentation](https://scikit-learn.org/) — ML библиотека для Python
- [Andrew Ng ML Course](https://www.coursera.org/learn/machine-learning) — курс основателя Coursera
- [Kaggle Competitions](https://www.kaggle.com/competitions) — практика на real datasets
- [The Hundred-Page ML Book](http://mlbook.explained.ai/) — краткая книга про ML
- [MLOps.community](https://mlops.community/) — best practices для production ML
