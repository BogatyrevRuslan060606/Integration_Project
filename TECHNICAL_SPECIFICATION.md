# Техническая спецификация: Track&Trace Integration App

## 1. Введение

### 1.1 Цель документа
Данный документ описывает технические требования, архитектуру и спецификацию интеграции с системой Track&Trace для управления маркированными товарами.

### 1.2 Область применения
Система предназначена для автоматизации процессов:
- Получения отчётов о торговых операциях из Track&Trace
- Управления кодами маркировки (GTIN, DataMatrix)
- Отслеживания статусов заказов на коды
- Мониторинга операций с маркированными товарами

## 2. Функциональные требования

### 2.1 Управление отчётами
- **FR-1**: Система должна позволять получать отчёты из Track&Trace API по заданным параметрам (дата, статус)
- **FR-2**: Система должна сохранять полученные отчёты в локальную базу данных
- **FR-3**: Система должна предоставлять фильтрацию отчётов по дате и статусу
- **FR-4**: Система должна отображать детальную информацию о каждой операции в отчёте

### 2.2 Управление кодами маркировки
- **FR-5**: Система должна позволять создавать заказы на эмиссию кодов маркировки
- **FR-6**: Система должна отслеживать статус заказов (в обработке, выполнен, ошибка)
- **FR-7**: Система должна хранить историю всех заказов
- **FR-8**: Система должна позволять просматривать список полученных кодов

### 2.3 Торговые операции
- **FR-9**: Система должна регистрировать торговые операции (выпуск, выбытие, передача)
- **FR-10**: Система должна предоставлять историю операций по коду маркировки
- **FR-11**: Система должна фильтровать операции по типу и периоду

### 2.4 Аутентификация и авторизация
- **FR-12**: Система должна поддерживать OAuth2 аутентификацию с Track&Trace API
- **FR-13**: Система должна автоматически обновлять access токены
- **FR-14**: Система должна кэшировать токены для оптимизации запросов

### 2.5 Фоновые задачи
- **FR-15**: Система должна автоматически опрашивать Track&Trace API для получения новых отчётов
- **FR-16**: Периодичность опроса должна быть конфигурируемой
- **FR-17**: Система должна обрабатывать ошибки при опросе и выполнять retry

## 3. Нефункциональные требования

### 3.1 Производительность
- **NFR-1**: Время ответа API не должно превышать 2 секунды для простых запросов
- **NFR-2**: Время ответа для сложных запросов с фильтрацией не должно превышать 5 секунд
- **NFR-3**: Система должна поддерживать до 100 одновременных пользователей

### 3.2 Надёжность
- **NFR-4**: Система должна иметь доступность 99%
- **NFR-5**: Система должна корректно обрабатывать временные сбои Track&Trace API
- **NFR-6**: Все критические операции должны логироваться

### 3.3 Безопасность
- **NFR-7**: Пароли и токены не должны храниться в коде
- **NFR-8**: Все соединения с внешними API должны использовать HTTPS
- **NFR-9**: Логи не должны содержать чувствительных данных

### 3.4 Масштабируемость
- **NFR-10**: Архитектура должна позволять горизонтальное масштабирование
- **NFR-11**: База данных должна поддерживать рост объёма данных до 1 млн записей

## 4. Архитектура системы

### 4.1 Общая архитектура
```
┌─────────────────────────────────────────────────────────┐
│                    Presentation Layer                   │
│  (ASP.NET Core MVC Controllers + Views)                 │
└────────────────────┬────────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────────┐
│                   Business Logic Layer                  │
│  (Services: Track&TraceApiService, TradeItemService, etc.)   │
└────────────────────┬────────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────────┐
│                    Data Access Layer                    │
│  (Entity Framework Core + Repository Pattern)          │
└────────────────────┬────────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────────┐
│                   PostgreSQL Database                   │
│  (Tables: TradeItems, MarkingCodes, CodeOrders, etc.)  │
└─────────────────────────────────────────────────────────┘
```

### 4.2 Внешние интеграции
- **Track&Trace API**: REST API для работы с маркировкой
- **База данных**: PostgreSQL для хранения данных
- **Кэш**: In-memory cache для токенов и справочных данных

## 5. Модель данных

### 5.1 Сущности

#### TradeItem (Товар)
```csharp
public class TradeItem
{
    public int Id { get; set; }
    public string GTIN { get; set; }           // Глобальный номер товара
    public string Name { get; set; }           // Наименование
    public string Description { get; set; }    // Описание
    public decimal Price { get; set; }         // Цена
    public DateTime CreatedAt { get; set; }    // Дата создания
    public List<MarkingCode> MarkingCodes { get; set; }  // Коды маркировки
}
```

#### MarkingCode (Код маркировки)
```csharp
public class MarkingCode
{
    public int Id { get; set; }
    public string Code { get; set; }           // DataMatrix код
    public string SerialNumber { get; set; }   // Серийный номер
    public int TradeItemId { get; set; }       // Ссылка на товар
    public DateTime CreatedAt { get; set; }
    public DateTime? AppliedAt { get; set; }    // Дата нанесения
    public TradeItem TradeItem { get; set; }
}
```

#### CodeOrder (Заказ на коды)
```csharp
public class CodeOrder
{
    public int Id { get; set; }
    public string OrderNumber { get; set; }     // Номер заказа
    public int Quantity { get; set; }          // Количество кодов
    public string Status { get; set; }         // Статус (Pending, Completed, Error)
    public DateTime CreatedAt { get; set; }
    public DateTime? CompletedAt { get; set; }
}
```

#### CodeOperation (Операция с кодом)
```csharp
public class CodeOperation
{
    public int Id { get; set; }
    public string OperationType { get; set; }   // Тип (Issue, Disposal, Transfer)
    public string MarkingCode { get; set; }     // Код маркировки
    public DateTime OperationDate { get; set; }
    public string Description { get; set; }
}
```

### 5.2 ER-диаграмма
```
TradeItem (1) ----< (N) MarkingCode
TradeItem (1) ----< (N) CodeOperation
CodeOrder (1) ----< (N) MarkingCode
```

## 6. API спецификация

### 6.1 Аутентификация
```
POST /api/auth/token
Request:
{
  "client_id": "string",
  "client_secret": "string",
  "grant_type": "client_credentials"
}

Response:
{
  "access_token": "string",
  "refresh_token": "string",
  "expires_in": 3600
}
```

### 6.2 Отчёты
```
GET /api/reports
Query Parameters:
- startDate: datetime (optional)
- endDate: datetime (optional)
- status: string (optional)

Response:
{
  "reports": [
    {
      "id": "string",
      "date": "datetime",
      "status": "string",
      "operations": [...]
    }
  ]
}
```

### 6.3 Заказы на коды
```
POST /api/codeorders
Request:
{
  "gtin": "string",
  "quantity": 100,
  "description": "string"
}

Response:
{
  "orderId": "string",
  "status": "Pending"
}
```

## 7. Технологический стек

### 7.1 Backend
- .NET 10.0
- ASP.NET Core MVC
- Entity Framework Core 10.0
- PostgreSQL (Npgsql)

### 7.2 Интеграции
- HttpClient
- AutoMapper
- Memory Cache

### 7.3 Логирование
- Serilog
- Serilog.Sinks.File
- Serilog.Sinks.Console

### 7.4 Документация
- Swagger/OpenAPI (Swashbuckle.AspNetCore)

## 8. Развертывание

### 8.1 Требования к окружению
- .NET 10.0 Runtime
- PostgreSQL 12+
- 2GB RAM минимум
- 10GB дисковое пространство

### 8.2 Конфигурация
Параметры конфигурации хранятся в `appsettings.json`:
- ConnectionStrings: строка подключения к БД
- Track&TraceApiSettings: URL API, credentials
- PollingSettings: интервал опроса

## 9. Логирование и мониторинг

### 9.1 Логи
- Формат: JSON
- Ротация: ежедневно
- Хранение: 30 дней
- Уровни: Information, Warning, Error

### 9.2 Метрики
- Количество запросов к API
- Время ответа
- Количество ошибок
- Статус фоновых задач

## 10. Безопасность

### 10.1 Управление секретами
- Использование User Secrets для разработки
- Environment Variables для продакшена
- Azure Key Vault (опционально)

### 10.2 Защита данных
- HTTPS для всех внешних соединений
- Хеширование паролей
- Валидация входных данных

## 11. Тестирование

### 11.1 Unit тесты
- Тесты сервисов бизнес-логики
- Тесты валидации моделей
- Тесты маппинга DTO

### 11.2 Integration тесты
- Тесты интеграции с БД
- Тесты API endpoints
- Тесты внешних интеграций (mock)

## 12. Приложение

### 12.1 Справочная информация
- Документация Track&Trace API: [ссылка]
- GTIN specification: [ссылка]
- DataMatrix standard: [ссылка]

### 12.2 Изменения
- v1.0 - Initial release
