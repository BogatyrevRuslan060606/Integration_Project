# Track&Trace Integration App

ASP.NET Core приложение для интеграции с системой Track&Trace API - системой отслеживания маркированных товаров в России. Проект демонстрирует опыт системной аналитики, работы с интеграциями, базами данных и API.

## Обзор проекта

Проект представляет собой полнофункциональную систему интеграции с внешним API для управления маркированными товарами. Включает разработку архитектуры, проектирование базы данных, реализацию бизнес-логики и документацию API.

## Технологический стек

### Backend
- **.NET 10.0** - современный фреймворк
- **ASP.NET Core MVC** - веб-фреймворк
- **Entity Framework Core 10.0** - ORM для работы с БД
- **PostgreSQL (Npgsql)** - реляционная база данных
- **AutoMapper** - маппинг DTO и сущностей
- **Serilog** - структурированное логирование
- **Swagger/OpenAPI** - документация API

### Интеграции
- **HttpClient** - взаимодействие с REST API
- **Memory Cache** - кэширование токенов и справочных данных
- **Background Services** - фоновые задачи для polling

## Роль в проекте

### Системная аналитика
- Анализ требований к интеграции с системой Track&Trace 
- Проектирование архитектуры приложения с разделением на слои
- Определение сущностей предметной области (TradeItem, MarkingCode, CodeOrder, CodeOperation)
- Разработка технических спецификаций для API интеграции
- Документирование бизнес-процессов работы с маркировкой

### Работа с базами данных
- Проектирование схемы базы данных PostgreSQL
- Написание SQL-запросов через Entity Framework Core
- Оптимизация запросов с использованием LINQ
- Миграции и инициализация БД
- Отношения между сущностями (One-to-Many, Many-to-Many)

### Интеграции и API
- Реализация REST API клиента для Track&Trace 
- Аутентификация через OAuth2 токены
- Обработка ошибок и retry-логика
- Кэширование токенов для оптимизации запросов
- Фоновый сервис для polling отчётов

### Документация
- Swagger/OpenAPI спецификация для всех endpoints
- README с инструкциями по установке
- Комментирование кода и архитектуры

## Архитектура

### Слои приложения
```
Controllers (Presentation Layer)
    ↓
Services (Business Logic Layer)
    ↓
Data (Data Access Layer)
    ↓
PostgreSQL Database
```

### Основные компоненты

#### Контроллеры (API endpoints)
- `ReportsController` - управление отчётами Track&Trace (CRUD операции)
- `TradeItemsController` - управление товарами
- `CodeOrdersController` - заказы на коды маркировки
- `OperationsController` - торговые операции
- `SettingsController` - конфигурация интеграции

#### Сервисы (бизнес-логика)
- `TrackandTraceApiService` - интеграция с внешним API, аутентификация
- `TradeItemService` - логика работы с товарами
- `CodeOrderService` - управление жизненным циклом заказов
- `TokenCacheService` - кэширование токенов авторизации
- `ReportPollingBackgroundService` - фоновый polling отчётов
- `ReferenceService` - справочные данные (GTIN, product codes)

#### Модели данных
- `TradeItem` - товар с GTIN и атрибутами
- `MarkingCode` - код маркировки (DataMatrix)
- `CodeOrder` - заказ на эмиссию кодов
- `CodeOperation` - операция с кодом (выпуск, выбытие)
- `ApplyReport` - отчёт о нанесении кодов

## Технические детали

### База данных
- PostgreSQL с Entity Framework Core
- Fluent API для конфигурации отношений
- Индексы для оптимизации запросов
- Seed data для инициализации справочников

### API интеграция
- REST API с JSON форматом данных
- OAuth2 авторизация с refresh токенами
- Retry pattern для обработки временных ошибок
- Rate limiting considerations

### Логирование
- Serilog с ротацией по дням
- Уровни: Information, Warning, Error
- Логи в файлы и консоль

## Установка и запуск

### Требования
- .NET 10.0 SDK
- PostgreSQL 12+
- Учетные данные Track&Trace API

### Настройка

1. Клонируйте репозиторий:
```bash
git clone <repository-url>
cd TrackandTraceIntegrationApp
```

2. Настройте строку подключения в `appsettings.json`:
```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Host=localhost;Port=5432;Database=TrackandTraceDB;Username=postgres;Password=YOUR_PASSWORD_HERE"
  }
}
```

3. Восстановите зависимости:
```bash
dotnet restore
```

4. Запустите приложение:
```bash
dotnet run
```

## Доступ
- Web интерфейс: http://localhost:5000
- Swagger документация: http://localhost:5000/swagger
- API endpoints доступны через Swagger UI

## Скриншоты

Скриншоты интерфейса приложения доступны в папке `screenshots/`.

## Документация

Подробная техническая спецификация проекта доступна в [docs/TECHNICAL_SPECIFICATION.md](docs/TECHNICAL_SPECIFICATION.md), включая:
- Функциональные и нефункциональные требования
- Архитектура системы и диаграммы
- Модель данных и ER-диаграммы
- API спецификация
- Требования к безопасности и развертыванию

## Навыки и технологии

В этом проекте продемонстрированы:
- ✅ Системная аналитика и проектирование архитектуры
- ✅ Работа с реляционными БД (PostgreSQL) и SQL
- ✅ Интеграция с внешними REST API
- ✅ OAuth2 аутентификация и управление токенами
- ✅ Кэширование для оптимизации производительности
- ✅ Фоновые задачи и асинхронное программирование
- ✅ Swagger/OpenAPI документация
- ✅ SOLID принципы и паттерны проектирования
- ✅ Unit testing и логирование

## Лицензия
Проект разработан для работодателя.
