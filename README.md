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

Главная страница проекта
<img width="1747" height="1088" alt="image" src="https://github.com/user-attachments/assets/ddabffe6-8c63-40e6-8a86-2e2226a14b18" />

Список товаров
<img width="1949" height="809" alt="image" src="https://github.com/user-attachments/assets/c7d98e87-96a8-497c-9a3f-b92179451ecf" />

Карточка товара
<img width="1973" height="1004" alt="image" src="https://github.com/user-attachments/assets/86e3a903-a21d-4b01-b83a-134d7832c8a9" />

Список заказа кодов
<img width="2004" height="656" alt="image" src="https://github.com/user-attachments/assets/20122345-d921-4a9c-8d5b-8b21a3821ae7" />

Создание заказа кодов
<img width="1950" height="850" alt="image" src="https://github.com/user-attachments/assets/291881e2-8f80-4f38-8386-95b123ff8dcd" />

Созданный черновик заказа кодов в списке заказов кодов
<img width="1512" height="392" alt="image" src="https://github.com/user-attachments/assets/0259fc6a-b09c-4d0c-b30b-a2ca36716806" />

Черновик заказа
<img width="1943" height="1046" alt="image" src="https://github.com/user-attachments/assets/834bbe29-4bdc-4fc6-9066-88edc5c85731" />

Отправка заказа в систему Track&Trace
<img width="1974" height="503" alt="image" src="https://github.com/user-attachments/assets/1e6675e7-9114-4a04-85e7-6cef343944f3" />

Получение кодов 
<img width="1603" height="1005" alt="image" src="https://github.com/user-attachments/assets/c9070cf3-4e28-41d6-8dab-112a42b9bb62" />

Список полученных кодов
<img width="1753" height="902" alt="image" src="https://github.com/user-attachments/assets/4b4169f4-af94-4bd3-983e-23b6633e0d72" />

Подтверждение получения кодов
<img width="1708" height="1103" alt="image" src="https://github.com/user-attachments/assets/ccc6a50c-5acb-4488-9a47-c402c2a896be" />

Просмотр списка кодов
<img width="2136" height="1149" alt="image" src="https://github.com/user-attachments/assets/e74553f2-ebb7-44a7-9813-403c9e5c05a2" />

Выполнение операций с кодами
<img width="2271" height="884" alt="image" src="https://github.com/user-attachments/assets/b4df008b-b420-43be-9143-65e6de21653f" />

Просмотр списка отчетов о нанесении
<img width="2062" height="627" alt="image" src="https://github.com/user-attachments/assets/ca27b2bd-efa9-46c0-bb11-e627a5f596e2" />

Основные параметры отчета о нанесении
<img width="2091" height="775" alt="image" src="https://github.com/user-attachments/assets/39b6eaef-228e-4954-8aba-8ca252bcbb84" />

Перечень кодов из отчета о нанесении
<img width="2179" height="592" alt="image" src="https://github.com/user-attachments/assets/13f1633c-04c7-46fa-9ea5-a77467048ece" />

Создание черновика отчета о нанесении
<img width="2287" height="805" alt="image" src="https://github.com/user-attachments/assets/600b6504-c3a7-43c7-95db-98ccbaf763fa" />

Страница с настройками подключения к системе Track&Trace
<img width="2097" height="1002" alt="image" src="https://github.com/user-attachments/assets/e8c6f80a-fe86-42fc-9726-7e8249e21488" />

Страница со Swagger UI
<img width="1916" height="1178" alt="image" src="https://github.com/user-attachments/assets/f8f25880-8322-4ede-ae93-c7886dfaeb00" />


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
