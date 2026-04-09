# Браузер (Web Browser)

| Грейд | Роль |
|-------|------|
| 🟢 Junior | 🅰️ Systems Analyst |

> Браузер - это программа, которая отправляет запросы к веб-серверам, получает ответы и отображает веб-страницы для пользователя.

## Основные компоненты браузера

**Rendering Engine**: обработка HTML, CSS, отображение элементов
- Chrome/Chromium: Blink
- Firefox: Gecko
- Safari: WebKit

**JavaScript Engine**: выполнение JavaScript кода
- Chrome: V8
- Firefox: SpiderMonkey
- Safari: JavaScriptCore

**Networking Layer**: HTTP/HTTPS коммуникация с серверами

**Storage**: cookies, локальное хранилище, кэш

## Процесс загрузки веб-страницы

### 1. Ввод URL

Пользователь вводит адрес в адресную строку браузера:
```
https://www.example.com/page
```

### 2. DNS разрешение

Браузер отправляет DNS запрос:
```
Какой IP у www.example.com?
Ответ: 93.184.216.34
```

Занимает обычно 20-100ms, критично для производительности.

### 3. TCP соединение (Three-way handshake)

Браузер устанавливает TCP соединение с сервером по порту 443 (HTTPS) или 80 (HTTP):
```
Браузер: "Хочу соединиться"
Сервер: "ОК, вот мой номер последовательности"
Браузер: "Согласен, начинаем"
```

Обычно 50-200ms в зависимости от задержки сети.

### 4. TLS Handshake (для HTTPS)

Браузер и сервер договариваются о шифровании:
```
Браузер: "Поддерживаю следующие версии TLS"
Сервер: "Используем TLS 1.3"
[Обмен сертификатами и ключами]
```

Добавляет 50-100ms задержки для новых соединений.

### 5. HTTP запрос

Браузер отправляет HTTP GET запрос:
```
GET /page HTTP/1.1
Host: www.example.com
User-Agent: Mozilla/5.0
Accept: text/html,application/xhtml+xml
Accept-Language: ru-RU,ru;q=0.9
Cookie: sessionid=abc123def456
```

### 6. Получение ответа

Сервер отправляет HTML:
```
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 45678
Set-Cookie: sessionid=abc123def456; Path=/

<!DOCTYPE html>
<html>
  <head>
    <title>Example Page</title>
    <link rel="stylesheet" href="/style.css">
  </head>
  <body>
    <h1>Welcome</h1>
    <script src="/app.js"></script>
  </body>
</html>
```

### 7. Парсинг HTML и построение DOM

Браузер читает HTML строка за строкой:
```
<!DOCTYPE html>
     ↓
<html>
     ↓
<head>
     ↓
<title>Example Page</title>
     ↓
<link rel="stylesheet" href="/style.css">  ← Браузер отправляет запрос на style.css
     ↓
<body>
     ↓
<h1>Welcome</h1>
     ↓
<script src="/app.js"></script>  ← Браузер отправляет запрос на app.js
```

**DOM (Document Object Model)** - дерево структуры страницы:
```
html
├── head
│   ├── title
│   └── link (stylesheet)
└── body
    ├── h1
    └── script
```

### 8. Загрузка ресурсов

Браузер обнаруживает CSS и JavaScript файлы, отправляет запросы:
```
GET /style.css
GET /app.js
GET /image.jpg
GET /api/data (если есть AJAX запросы)
```

Все запросы отправляются в параллель (обычно 6-8 одновременных соединений на домен).

### 9. Построение CSSOM

Браузер парсит CSS и строит **CSSOM (CSS Object Model)**:
```css
h1 { color: blue; font-size: 24px; }
body { background: white; }
```

CSSOM определяет, как выглядит каждый элемент.

### 10. Создание Render Tree

Браузер комбинирует DOM и CSSOM в **Render Tree** (только видимые элементы):
```
html
└── body
    ├── h1 (blue, 24px)
    └── script (не видно, пропускается)
```

### 11. Layout (Reflow)

Браузер вычисляет позицию и размер каждого элемента:
```
h1: x=10, y=10, width=200, height=30
```

Это дорогая операция, может быть узким местом.

### 12. Paint (Rendering)

Браузер "рисует" каждый пиксель:
```
Отрисовка фона
Отрисовка текста
Отрисовка границ
```

### 13. Выполнение JavaScript

Браузер выполняет JavaScript код:
```javascript
document.querySelector('h1').addEventListener('click', function() {
  alert('Hello!');
});
```

JavaScript может изменить DOM, что требует перерисовки (Reflow/Paint).

### 14. Отображение страницы

Браузер отображает визуально полную страницу пользователю.

## Критические метрики производительности

**First Contentful Paint (FCP)**: время, когда браузер отображает первый контент
- Цель: < 1.8 секунд

**Largest Contentful Paint (LCP)**: время загрузки самого крупного элемента
- Цель: < 2.5 секунд

**Cumulative Layout Shift (CLS)**: мера нестабильности макета (элементы скачут)
- Цель: < 0.1

**Time to Interactive (TTI)**: время, когда страница полностью интерактивна
- Цель: < 3.8 секунд

## Оптимизация загрузки страницы

**1. Минимизация requests**:
- Объединить CSS файлы в один
- Объединить JavaScript в один
- Использовать CSS Sprites для изображений

**2. Минимизация размера**:
- Минификация (удаление пробелов, переименование переменных)
- Сжатие (gzip, brotli)
- Компрессия изображений (JPEG вместо PNG)

**3. Кэширование**:
- Browser cache: браузер сохраняет статические файлы
- CDN: доставка контента с сервера ближе к пользователю
- Service Worker: локальный кэш для offline работы

**4. Lazy Loading**:
- Загружать изображения только когда они видны в viewport
- Отложить загрузку скриптов (async, defer)

**5. Progressive Enhancement**:
- Сначала показать основной контент, потом улучшения
- Не блокировать рендеринг на JavaScript

## Пример анализа для аналитика

**Проблема**: "Сайт открывается медленно на мобильной сети"

Анализируем по этапам:
1. **DNS**: 50ms (нормально)
2. **TCP+TLS**: 150ms (мобильная сеть медленная)
3. **HTML загрузка**: 200ms
4. **CSS загрузка**: 300ms (большой CSS файл?)
5. **JavaScript загрузка**: 500ms (большой JS файл?)
6. **Парсинг и отображение**: 300ms
7. **Итого**: ~1.5 секунд

Решение: минимизировать размер CSS и JavaScript, использовать CDN для быстрой доставки.

## Cookies и Session

**Cookies**: браузер сохраняет небольшие текстовые файлы, отправляет их в каждом запросе
```
Set-Cookie: sessionid=abc123; Path=/; HttpOnly; Secure; SameSite=Strict
```

- HttpOnly: JavaScript не может получить доступ (безопасность)
- Secure: передается только по HTTPS
- SameSite: защита от CSRF атак

**Local Storage**: браузер сохраняет данные (до 5-10MB)
```javascript
localStorage.setItem('user', 'John');
```

**Session Storage**: как Local Storage, но очищается при закрытии браузера

## Developer Tools

Браузер предоставляет инструменты для диагностики:
- **Network Tab**: все HTTP запросы, размеры, время загрузки
- **Performance Tab**: график загрузки, FCP, LCP, TTI
- **Console**: ошибки JavaScript, логи
- **Elements**: DOM структура, CSS стили каждого элемента

### 📚 Ресурсы для изучения

- [Web.dev - How Browsers Work](https://web.dev/howbrowserswork/)
- [MDN - How Browsers Work](https://developer.mozilla.org/en-US/docs/Web/Performance/How_browsers_work)
- [YouTube: Процесс загрузки веб-страницы](https://www.youtube.com/watch?v=jBvkN8_c7t8)
- [Google - Web Performance](https://developers.google.com/web/fundamentals/performance)
