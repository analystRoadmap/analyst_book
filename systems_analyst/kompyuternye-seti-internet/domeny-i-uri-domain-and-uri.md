# Домены и URI (Domain and URI)

| Грейд | Роль |
|-------|------|
| 🟢 Junior | 🅰️ Systems Analyst |

> Доменное имя - это адрес веб-сайта (example.com), который людям проще запомнить, чем IP-адрес (192.168.1.1).

## Доменные имена (Domain Names)

**Доменное имя** - это иерархическая, легко запоминаемая метка для доступа к ресурсам в интернете.

Пример: `www.google.com`
- `com` - верхнеуровневый домен (Top-Level Domain, TLD)
- `google` - доменное имя второго уровня (Second-Level Domain, SLD)
- `www` - поддомен (subdomain)

## Структура доменного имени

```
www.example.com
│   │         │
│   │         └─ TLD (Top-Level Domain) = .com
│   └─────────── SLD (Second-Level Domain) = example
└─────────────── Subdomain = www
```

### Виды доменов

**Верхнеуровневые домены (TLD)**:
- **.com**: коммерческие (google.com)
- **.org**: организации (wikipedia.org)
- **.net**: сетевые (example.net)
- **.ru**: Россия (yandex.ru)
- **.de**: Германия (example.de)
- **.uk**: Великобритания (bbc.co.uk)
- **.gov**: правительственные (nasa.gov)
- **.edu**: образовательные (mit.edu)

**Поддомены (Subdomains)**:
- `www` - "World Wide Web" (исторически для веб-сайта)
- `mail` - почтовый клиент
- `api` - API сервер
- `cdn` - доставка контента
- `blog` - блог

Пример:
```
https://www.example.com     - основной веб-сайт
https://api.example.com     - API сервер
https://cdn.example.com     - доставка статических файлов
https://blog.example.com    - блог
```

## Регистрация доменов

Доменное имя не приобретается навечно, а **арендуется** на определенный период:
- Обычно на 1 год
- Можно продлить перед истечением
- Администратор получает доступ к управлению DNS записями

Популярные регистраторы доменов:
- GoDaddy, Namecheap, 1&1
- Яндекс.Домены, REG.RU (для .ru доменов)

## URI, URL, URN - в чем разница?

Часто эти термины путают, но они имеют разные значения:

### URI (Uniform Resource Identifier)

**URI** - универсальный идентификатор ресурса, общее название для адреса ресурса в сети.

Включает и URL, и URN.

**Примеры**:
```
https://example.com/page
book://isbn/0-395-36341-1
```

### URL (Uniform Resource Locator)

**URL** - указывает где находится ресурс и как к нему обратиться.

Содержит протокол (http, https, ftp), доменное имя и путь.

**Структура URL**:
```
https://www.example.com:8080/path/to/page?param1=value1&param2=value2#section
│      │  │                 │                │                          │
│      │  │                 │                └─ Query String (параметры)
│      │  │                 └──────── Path (путь к ресурсу)
│      │  └──────────────── Host (доменное имя или IP)
│      └───────────────── Port (опционально, 80 по умолчанию для HTTP)
└─────────────────────── Scheme/Protocol (протокол)
```

**Примеры URL**:
```
https://www.example.com
https://www.example.com/page
https://api.example.com/v1/users/123
https://github.com/search?q=python
https://en.wikipedia.org/wiki/Programming#History
```

### URN (Uniform Resource Name)

**URN** - только имя ресурса, но не указывает как его получить.

**Примеры URN**:
```
urn:isbn:0-395-36341-1      (имя книги)
urn:ietf:rfc:3986           (имя документа)
```

URN редко используется в повседневной разработке.

## Сравнение таблица

| Аспект | URI | URL | URN |
|--------|-----|-----|-----|
| **Определение** | Идентификатор ресурса | Адрес и способ доступа | Только имя ресурса |
| **Содержит протокол** | Может | Всегда | Нет |
| **Содержит путь** | Может | Обычно | Нет |
| **Пример** | http://example.com/page | http://example.com/page | urn:isbn:123456 |
| **Используется** | Теоретически | Практически везде | Редко |

## Query Parameters (Параметры запроса)

URL может содержать параметры для передачи данных серверу:

```
https://example.com/search?q=python&sort=date&page=2
                           │       │   │    │  │    │
                           │       │   │    │  │    └─ Значение параметра
                           │       │   │    │  └───── Имя параметра
                           │       │   │    └──────── Разделитель (&)
                           │       │   └───────────── Имя параметра
                           │       └──────────────── Значение параметра
                           └──────────────────────── Вопросительный знак (начало параметров)
```

- `q=python` - поисковый запрос
- `sort=date` - сортировка по дате
- `page=2` - вторая страница результатов

## Фрагменты (Hash/Anchor)

URL может содержать фрагмент для навигации в пределах страницы:

```
https://en.wikipedia.org/wiki/Programming#History
                                        │
                                        └─ Фрагмент (якорь)
```

Фрагмент:
- Не отправляется на сервер
- Браузер переходит на элемент с id="History"
- Используется для внутренней навигации

## DNS vs URL vs IP

Различие в иерархии:

```
Пользователь вводит в браузер:     https://www.example.com/page

     ↓ Браузер отправляет DNS запрос

DNS резолвер преобразует:          www.example.com → 93.184.216.34

     ↓ Браузер подключается

TCP соединение к:                  93.184.216.34:443

     ↓ Браузер отправляет

HTTP запрос для:                   GET /page HTTP/1.1
                                   Host: www.example.com
```

## Практический пример для аналитика

### Анализ URL при интеграции с API

API endpoint:
```
https://api.github.com/repos/torvalds/linux/issues?state=open&per_page=10&page=1
```

Разбор:
- **Протокол**: https (безопасный)
- **Хост**: api.github.com (API сервер GitHub)
- **Путь**: /repos/torvalds/linux/issues (ресурс: issues в репозитории)
- **Параметры**:
  - state=open (открытые issues)
  - per_page=10 (10 элементов на странице)
  - page=1 (первая страница)

### SEO и доменные имена

Для поисковой оптимизации:
- Короткие доменные имена лучше запоминаются
- Релевантные ключевые слова в домене помогают SEO
- Поддомены (www, api, blog) должны быть настроены правильно
- HTTPS обязателен для всех доменов

## Защита доменов

**WHOIS Privacy**: скрывает личную информацию владельца домена

**Domain Lock**: защита от несанкционированной передачи домена

**SSL Certificate**: шифрование при HTTPS, необходимо для всех доменов

### 📚 Ресурсы для изучения

- [MDN - Domain Names](https://developer.mozilla.org/en-US/docs/Glossary/Domain_name)
- [MDN - URL](https://developer.mozilla.org/en-US/docs/Glossary/URL)
- [IETF RFC 3986 - URI](https://tools.ietf.org/html/rfc3986)
- [roadmap.sh - Backend](https://roadmap.sh/backend)
