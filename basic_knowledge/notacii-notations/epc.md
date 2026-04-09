# EPC (Event-driven Process Chain)

| Грейд | Роль |
|---|---|
| 🔵 Middle | BA |

> **EPC — это legacy нотация** для моделирования процессов в SAP системах. Используйте BPMN для новых проектов.

## Историческая справка

**EPC (Event-driven Process Chain)** — это нотация, разработанная в Германии в 1990-х годах для методологии ARIS (Architecture of Integrated Information Systems). Широко использовалась в SAP консалтинге.

```
Статус EPC:
⚠️ LEGACY - в новых проектах используйте BPMN
✓ Все еще актуальна в:
  - SAP системах
  - Старых проектах
  - Консультирующих компаниях (Deloitte, Accenture)
```

## Основные элементы EPC

### 1. Событие (Event)

```
┌─────────────────┐
│ Hexagon / овал  │
│                 │ ← Что-то произойдет
│ "Order Received"│
│                 │
└─────────────────┘
```

События в EPC:
- **Start Event** — начало процесса
- **Intermediate Event** — событие внутри процесса
- **End Event** — конец процесса

### 2. Функция (Function)

```
┌─────────────────┐
│ Прямоугольник   │
│  "Verify Order" │ ← Действие/операция
│                 │
└─────────────────┘
```

Функции:
- **Manual Function** — человек делает
- **Automatic Function** — система делает
- **Complex Function** — подпроцесс

### 3. Логический оператор (Logical Connector)

```
  ●  ← AND (все ветви)
 /|\
○ ○ ○

  ●  ← XOR/OR (одна из ветвей)
 / \
○   ○

  ●  ← AND/OR (комбинация)
```

Операторы:
- **AND** — все потоки должны выполниться
- **XOR (Exclusive OR)** — только один поток
- **OR (Inclusive OR)** — один или больше

### 4. Информационный объект (Information Object)

```
  ┌─────┐
  │Data │
  └─────┘
    │
    ↓ (связь с функцией)
 ┌──────┐
 │ Func │
 └──────┘
```

Примеры информационных объектов:
- Documents (Invoice, PO)
- Data (Customer info)
- Messages (Email)

## Синтаксис EPC

### Правила потока

```
Event → Function → Event → Function → Event

┌─────────┐    ┌──────────┐    ┌──────────┐
│ Order   │    │ Verify   │    │ Order OK │
│ Received├───→│ Order    ├───→│          │
│         │    │          │    │          │
└─────────┘    └──────────┘    └──────────┘

(Event)      (Function)        (Event)
```

### Условные ветвления

```
          ┌──────────┐
          │ Verify   │
          │ Order    │
          └────┬─────┘
               ↓
          ┌─────────┐
          │ Valid?  │ ← Логический оператор (XOR)
          └────┬────┘
          ┌────┴──────┐
          │ Yes    No │
          ↓           ↓
    ┌──────────┐ ┌─────────┐
    │ Process  │ │ Cancel  │
    │ Order   │ │ Order  │
    └──────────┘ └─────────┘
```

## Пример: Обработка заказа в EPC

```
┌──────────────┐
│ Order Arrive │  ← Start Event
└──────┬───────┘
       ↓
┌──────────────────┐
│ Receive Order    │  ← Function (manual)
│ (check email)    │
└──────┬───────────┘
       ↓
┌──────────────────┐
│ Order Received   │  ← Event
└──────┬───────────┘
       ↓
┌──────────────────┐
│ Check Stock      │  ← Function (automatic, от системы)
└──────┬───────────┘
       ↓
┌──────────────────┐
│ Stock Available? │  ← Event (логический оператор XOR)
└───┬──────────┬───┘
    │ Yes   No │
    ↓         ↓
┌──────────┐ ┌───────────────────┐
│Process   │ │ Send Notification │  ← Functions
│Payment   │ │ (Out of Stock)    │
└────┬─────┘ └──────┬────────────┘
     ↓              ↓
 ┌────────┐    ┌─────────────┐
 │Payment │    │ Order       │
 │Processed   │ Cancelled   │
 └────┬───┘    └──────┬──────┘
      │               │
      └───────┬───────┘
              ↓
        ┌──────────────┐
        │ Send Email   │  ← Function (send email)
        │ Confirmation │
        └────┬─────────┘
             ↓
        ┌──────────┐
        │ Order    │
        │ Completed│  ← End Event
        └──────────┘
```

## EPC vs BPMN

### Различия

| Аспект | EPC | BPMN |
|---|---|---|
| **Возраст** | 1990s (legacy) | 2000s+ (современный) |
| **Ориентация** | На события | На процесс и события |
| **Нотация** | Проще (5 символов) | Богаче (20+ символов) |
| **Экосистема** | SAP/ARIS | Стандарт ISO |
| **Инструменты** | ARIS, SAP | draw.io, BPMN.io, Camunda |
| **Для новых проектов** | НЕТ | ДА |

### Пример одного процесса в BPMN и EPC

**BPMN:**
```
Customer → [Browse] → [Add Cart] → [Checkout] → Event:Payment OK → [Ship]
```

**EPC:**
```
Event:Customer browsing → [Browse Task] → Event:Item selected →
[Add to Cart] → Event:Checkout started → [Process Payment] →
Event:Payment OK → [Prepare Shipment] → Event:Ready to Ship
```

## Когда еще используется EPC?

```
1. SAP Projects:
   - Все крупные SAP имплементации моделируют в ARIS
   - EPC диаграммы документируют SAP процессы
   - Консультанты SAP знают EPC лучше BPMN

2. Legacy Systems:
   - Старые проекты до 2010 года часто в EPC
   - Документация может быть в EPC
   - Нужно понимать для maintenance

3. Некоторые компании:
   - Некоторые консалтинговые фирмы используют ARIS/EPC
   - "Мы так всегда делали" менталитет
   - Миграция на BPMN идет медленно

4. Нишевые области:
   - Некоторые регуляторные требования могут требовать EPC
   - Исторические системы (банки, страховки)
```

## Компоненты EPC (подробнее)

### Функции EPC

```
Нормальный прямоугольник ← Manual Function (человек)
        ┌────────────┐
        │ Verify Order   │
        │ (manual check) │
        └────────────┘

Прямоугольник с волн. линией ← Automatic Function (система)
        ┌∼∼∼∼∼∼∼∼∼∼∼∼┐
        │ Check Stock    │
        │ (automatic)    │
        └∼∼∼∼∼∼∼∼∼∼∼∼┘

Прямоугольник внутри шестиугольника ← Composite Function
        ┌────────────┐
        │ ┌────────┐ │
        │ │Subprocess   │
        │ └────────┘ │
        └────────────┘
```

### Операторы в EPC (детально)

```
AND (AND Split):
    ┌───────┐
    │ Event │
    └───┬───┘
        │
        ◇ ← AND оператор
       /|\
      / │ \
     /  │  \
    ↓   ↓   ↓
   F1  F2  F3 ← Все функции выполняются!

XOR (Exclusive OR Split):
    ┌───────┐
    │ Event │
    └───┬───┘
        │
        ◇ ← XOR оператор
       / \
      /   \
     ↓     ↓
    F1     F2 ← Только ОДНА функция выполняется!

OR (Inclusive OR Split):
    ┌───────┐
    │ Event │
    └───┬───┘
        │
        ◇ ← OR оператор
       / \
      /   \
     ↓     ↓
    F1     F2 ← F1 или F2 или обе!
```

## Role-based View (дорожки в EPC)

```
┌──────────────────────────────────────────────┐
│ Customer                                     │
├──────────────────────────────────────────────┤
│ Event:Order [Browse] Event:Ready            │
│ Ready      ─────→       to Order             │
└──────────────────────────────────────────────┘

┌──────────────────────────────────────────────┐
│ Sales Department                             │
├──────────────────────────────────────────────┤
│                [Verify]        [Send]        │
│                 Order   →    Confirmation    │
└──────────────────────────────────────────────┘

┌──────────────────────────────────────────────┐
│ Warehouse                                    │
├──────────────────────────────────────────────┤
│                          [Pack]   [Ship]     │
│                          Item  →   Item      │
└──────────────────────────────────────────────┘
```

## Инструменты для EPC

### ARIS (ARIS Platform)

```
Software AG's ARIS:
✓ Стандартный инструмент для EPC
✓ Интеграция с SAP
✓ Мощный для больших проектов
✗ Очень дорого (тысячи $ в год)
✗ Сложный интерфейс
```

### Альтернативы

```
draw.io (с расширениями для EPC):
✓ Бесплатно
✓ Можно нарисовать EPC
✗ Не все символы идеальны

Visio (Microsoft):
✓ Можно нарисовать EPC
✓ Интегрирован в Office
✗ Требует лицензию
```

## Миграция с EPC на BPMN

```
EPC элемент → BPMN эквивалент

Event → Event (Start, Intermediate, End)
Function → Task (User, Service, Manual)
AND operator → Parallel Gateway
XOR operator → Exclusive Gateway
OR operator → Inclusive Gateway
Information Object → Data Object

Пример миграции:

EPC:  Event→[Func1]→Event→[Func2]→Event
      (обязательно Event между функциями)

BPMN: [Task1]→[Task2]
      (события опциональны)
```

## Best Practices для EPC

```
1. ЕСЛИ вы ДОЛЖНЫ использовать EPC (SAP проект):
   ✓ Используйте стандартные символы ARIS
   ✓ Dokumentируйте функции с RACI матрицей
   ✓ Версионируйте диаграммы (v1.0, v1.1)
   ✗ Не используйте сложные комбинации операторов

2. ДЛЯ НОВЫХ проектов:
   ✗ НЕ используйте EPC - это LEGACY!
   ✓ Используйте BPMN вместо этого
   ✓ Если окружающие используют EPC, предложите BPMN как дополнение

3. ДЛЯ ДОКУМЕНТАЦИИ:
   ✓ Если эксп. система в EPC, дополните BPMN версией
   ✓ Помогите команде мигрировать (обучение)
   ✗ Не создавайте новые EPC диаграммы
```

## 📚 Ресурсы для изучения

- "ARIS Process Performance Manager" — SAP документация
- "Event-Driven Process Chains" — Original paper by Scheer
- ARIS handbook (https://www.arisbpmn.com)
- SAP training materials
- **ЛУЧШЕ:** Выучите BPMN вместо EPC!

## Рекомендация

Если вы новичок в нотациях:
- Изучайте **BPMN**, не EPC
- EPC нужна только если вы работаете на SAP проектах
- Даже в SAP проектах современные компании переходят на BPMN
- Время EPC прошло (2000s), сейчас эра BPMN (2010s+)
