# Python для аналитика

| Грейд | Роль |
|-------|------|
| 🔵 Middle | 🅰️ SA |

> Python используется для автоматизации, ETL процессов и прототипирования. Базовое понимание синтаксиса и экосистемы помогает аналитику быстро проверять гипотезы и готовить примеры для команды.

## Когда аналитику нужны знания Python

Python популярен в:
- **Data Science и аналитике** — обработка данных, визуализация, ML
- **Автоматизации** — скрипты, CI/CD pipelines, веб-скрейпинг
- **Прототипировании** — быстро создать MVP или proof-of-concept
- **ETL процессах** — загрузка, трансформация, выгрузка данных

## Почему Python полезен для аналитика

### Быстрое прототипирование

Python позволяет за несколько строк кода сделать что-то полезное:

```python
import pandas as pd

# Загрузить CSV файл
df = pd.read_csv('sales.csv')

# Найти средний чек
avg_check = df['amount'].mean()

# Выгрузить результат
df[df['amount'] > avg_check].to_csv('large_orders.csv')
```

Аналитик может сам проверить гипотезу, не дожидаясь разработчика.

### Работа с HTTP и API

```python
import requests
import json

# Загрузить данные с API
response = requests.get('https://api.example.com/products')
products = response.json()

# Обработать
for product in products:
    print(f"{product['name']}: {product['price']}")
```

Аналитик может проверить, правильно ли API возвращает данные, без необходимости писать frontend код.

### Автоматизация рутинных задач

```python
import os
import shutil
from datetime import datetime

# Скопировать все файлы из папки в архив по дате
for filename in os.listdir('inbox'):
    if filename.endswith('.pdf'):
        date_str = datetime.now().strftime('%Y-%m-%d')
        dest_folder = f'archive/{date_str}'
        os.makedirs(dest_folder, exist_ok=True)
        shutil.move(f'inbox/{filename}', f'{dest_folder}/{filename}')
```

Вместо ручного копирования файлов аналитик может автоматизировать.

## Ключевые библиотеки для аналитика

| Библиотека | Применение | Пример |
|-----------|-----------|--------|
| **pandas** | Обработка и анализ данных | `df.groupby('category').sum()` |
| **requests** | HTTP запросы к API | `requests.get('http://...')` |
| **json** | Работа с JSON | `json.loads(api_response)` |
| **sqlite3** | Работа с БД | `cursor.execute('SELECT ...')` |
| **re** | Регулярные выражения | `re.findall(pattern, text)` |
| **matplotlib** | Визуализация | `plt.plot(x, y); plt.show()` |
| **Beautiful Soup** | Парсинг HTML | `soup.find_all('a')` |
| **SQLAlchemy** | ORM для БД | `session.query(User).filter(...)` |

## Типичные задачи для Python

### Задача 1: Загрузить данные из источника и выгрузить в другой

```python
import pandas as pd
from sqlalchemy import create_engine

# Читать из CSV
df = pd.read_csv('source.csv')

# Фильтровать
df = df[df['status'] == 'active']

# Преобразовать
df['amount_usd'] = df['amount_rub'] / 100  # Конвертация

# Выгрузить в PostgreSQL
engine = create_engine('postgresql://user:pass@localhost/db')
df.to_sql('processed_data', engine, if_exists='append')
```

Это ETL скрипт — Extract (читать), Transform (преобразовать), Load (выгрузить).

### Задача 2: Проверить качество данных

```python
import pandas as pd

df = pd.read_csv('data.csv')

print(f"Всего строк: {len(df)}")
print(f"Пустых значений: {df.isnull().sum()}")
print(f"Дублей: {df.duplicated().sum()}")
print(f"Уникальные значения в 'status': {df['status'].unique()}")

# Вывести статистику
print(df.describe())
```

### Задача 3: Автоматизировать отправку уведомлений

```python
import smtplib
from email.mime.text import MIMEText

# Прочитать список email-ов
with open('recipients.txt') as f:
    recipients = f.readlines()

# Отправить письмо каждому
for email in recipients:
    msg = MIMEText(f'Привет, {email}! Ваш отчет готов.')
    msg['Subject'] = 'Уведомление'
    msg['From'] = 'noreply@example.com'

    server = smtplib.SMTP('smtp.example.com')
    server.send_message(msg)
    server.quit()
```

## Python для тестирования требований

### Пример: Проверить, правильно ли работает API

```python
import requests
import pytest

def test_create_order():
    # Отправить запрос
    response = requests.post('http://localhost:8000/api/orders', json={
        'customer_id': 123,
        'items': [{'product_id': 1, 'qty': 2}],
        'total': 500
    })

    # Проверить статус
    assert response.status_code == 201

    # Проверить данные ответа
    data = response.json()
    assert data['id'] > 0
    assert data['status'] == 'pending'

def test_get_order_not_found():
    response = requests.get('http://localhost:8000/api/orders/999999')
    assert response.status_code == 404
```

Аналитик может писать такие тесты, чтобы проверить, что API работает правильно.

## Интеграция Python с требованиями

### Когда аналитик ставит задачу разработчику на Python

```
Требование: Отчет о продажах каждый день в 9:00

Реализация на Python:
1. Скрипт, который:
   - Читает данные из БД (начало дня = 00:00, конец = 23:59:59)
   - Вычисляет: общие продажи, средний чек, топ товары, топ клиенты
   - Генерирует HTML отчет
   - Отправляет email менеджерам

2. Запуск: cron job каждый день в 9:00
   0 9 * * * /usr/bin/python3 /scripts/daily_report.py

3. Требования:
   - Отчет должен прийти в течение 1 минуты
   - Если скрипт упал, отправить ошибку по email
   - Логировать выполнение
```

## Ограничения Python для аналитика

### Python не подходит для:

**Высоконагруженных приложений** — Python медленнее Java или Go при большой нагрузке.

**Фронтенд разработки** — Python не работает в браузере.

**Мобильных приложений** — нет стандартного способа писать мобильные приложения.

**Реального времени** — Python не гарантирует быстрое выполнение.

### Но Python идеален для:

**Анализа данных** — pandas, numpy, scikit-learn.

**Автоматизации** — скрипты, которые выполняются периодически.

**Прототипирования** — быстро проверить идею.

**Интеграции систем** — скрипт, который читает из одной системы и пишет в другую.

## Рекомендации для аналитика

1. **Установите Python** — даже если не будете писать код, нужен для экспериментов
2. **Изучите pandas** — основная библиотека для анализа данных
3. **Научитесь писать простые скрипты** — автоматизировать рутинные задачи
4. **Используйте Jupyter Notebook** — интерактивное окружение для экспериментов
5. **Не пытайтесь писать production код** — это должны делать разработчики

## Минимальный набор Python для аналитика

```python
# 1. Работа с файлами
with open('data.txt', 'r') as f:
    lines = f.readlines()

# 2. Обработка данных
data = [int(x) for x in lines]
average = sum(data) / len(data)

# 3. HTTP запросы
import requests
response = requests.get('http://api.example.com/data')
json_data = response.json()

# 4. Работа с БД
import sqlite3
conn = sqlite3.connect('database.db')
cursor = conn.cursor()
cursor.execute('SELECT * FROM users')
rows = cursor.fetchall()

# 5. Сохранение результатов
import json
with open('results.json', 'w') as f:
    json.dump(results, f, indent=2)
```

Этого достаточно для большинства аналитических задач.

## 📚 Ресурсы для изучения

- [Python Official Documentation](https://docs.python.org/3/) — официальная документация
- [Pandas Tutorial](https://pandas.pydata.org/docs/getting_started/index.html) — введение в pandas
- [Automate the Boring Stuff with Python](https://automatetheboringstuff.com/) — автоматизация с Python
- [Real Python](https://realpython.com/) — туториалы и статьи про Python
- [Jupyter Notebook Tutorial](https://jupyter.org/try) — попробовать Jupyter в браузере
