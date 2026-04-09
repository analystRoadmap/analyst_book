# SOAP

| Грейд | Роль |
|-------|------|
| 🔵 Middle | 🅰️ System Analyst |

> **Почему это важно для аналитика:** SOAP по-прежнему используется в корпоративных системах. Знание основ SOAP критично для работы с legacy-интеграциями и понимания различий между подходами к построению API.

## Что такое SOAP?

**SOAP** (Simple Object Access Protocol) — это протокол обмена структурированными сообщениями в формате XML для взаимодействия между клиентом и сервером. SOAP был разработан для создания веб-сервисов и обеспечивает совместимость между различными платформами и языками программирования.

## Основные характеристики SOAP

### 1. XML-основание
Все сообщения кодируются в XML, что обеспечивает гибкость, но приводит к большему размеру данных.

### 2. Протокол-независимый
SOAP может работать поверх HTTP, SMTP, TCP и других протоколов (хотя в основном используется HTTP).

### 3. Структурированные сообщения
SOAP-сообщение имеет стандартную структуру:

```xml
<?xml version="1.0"?>
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Header>
    <!-- Метаинформация, безопасность -->
  </soap:Header>
  <soap:Body>
    <!-- Основные данные запроса/ответа -->
  </soap:Body>
</soap:Envelope>
```

### 4. Описание через WSDL
Для описания SOAP-сервиса используется язык WSDL (Web Services Description Language):

```xml
<wsdl:service name="UserService">
  <wsdl:port name="UserServicePort" binding="UserServiceBinding">
    <soap:address location="http://api.example.com/soap"/>
  </wsdl:port>
</wsdl:service>
```

### 5. Высокая абстракция
SOAP скрывает детали взаимодействия, позволяя фокусироваться на бизнес-логике.

## Пример SOAP запроса

```xml
<?xml version="1.0"?>
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/"
               xmlns:user="http://api.example.com/user">
  <soap:Body>
    <user:GetUser>
      <user:id>123</user:id>
    </user:GetUser>
  </soap:Body>
</soap:Envelope>

Ответ:
<?xml version="1.0"?>
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Body>
    <user:GetUserResponse xmlns:user="http://api.example.com/user">
      <user:name>John Doe</user:name>
      <user:email>john@example.com</user:email>
    </user:GetUserResponse>
  </soap:Body>
</soap:Envelope>
```

## REST vs SOAP

| Критерий | REST | SOAP |
|----------|------|------|
| **Простота** | Простой, интуитивный | Сложный, формальный |
| **Размер сообщений** | Меньше (JSON) | Больше (XML overhead) |
| **Скорость** | Быстрее | Медленнее |
| **Безопасность** | Зависит от реализации | Встроенная поддержка |
| **Транзакции** | Не поддерживает | Поддерживает (WS-AtomicTransaction) |
| **Современность** | Современный стандарт | Legacy, но все еще используется |
| **Документирование** | OpenAPI/Swagger | WSDL |

## Используется ли SOAP сегодня?

Хотя REST стал доминирующим стилем, SOAP все еще встречается:
- Банковские системы
- Государственные учреждения
- Корпоративные ERP-системы
- Legacy-интеграции
- Системы с требованиями к транзакционности

## Когда использовать SOAP?

- Интеграция с существующими SOAP-сервисами
- Требования к двусторонней безопасности
- Необходимость гарантий доставки сообщений
- Работа с системами в контролируемых сетях (не интернет)

## Когда выбрать REST вместо SOAP?

- Публичные веб-API
- Мобильные и браузерные приложения
- Микросервисная архитектура
- Высоконагруженные системы
- Использование современных фреймворков

## Инструменты и примеры

- **SoapUI** — тестирование SOAP-сервисов
- **Apache CXF** — фреймворк для SOAP-сервисов
- **WS-Security** — расширение для безопасности SOAP
- **WSDL Editor** — графические редакторы для WSDL

## 📚 Ресурсы для изучения

- [W3C SOAP Specification](https://www.w3.org/TR/soap/)
- [SOAP Tutorial](https://www.w3schools.com/xml/xml_soap.asp)
- [WSDL Tutorial](https://www.w3schools.com/xml/xml_wsdl.asp)
- [REST vs SOAP Comparison](https://www.youtube.com/watch?v=ij79SPK89bw)
- [Enterprise Integration Patterns](https://enterpriseintegrationpatterns.com)
- [Web Services Description Language](https://www.w3.org/TR/wsdl/)
