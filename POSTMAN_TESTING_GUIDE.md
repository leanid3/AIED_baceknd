# 🚀 AIEN Backend API - Postman Testing Guide

## 📋 Обзор

Эта коллекция Postman позволяет тестировать полный цикл работы AIEN Backend API:
- Загрузка файлов (CSV, JSON, XML)
- Анализ данных с помощью LLM
- Генерация DDL скриптов
- Получение результатов анализа

## 🛠️ Настройка

### 1. Импорт коллекции
1. Откройте Postman
2. Нажмите `Import`
3. Выберите файл `AIEN_Backend_API.postman_collection.json`

### 2. Настройка переменных
В коллекции уже настроены переменные:
- `base_url`: http://localhost:8080 (API Gateway)
- `analysis_url`: http://localhost:8083 (Data Analysis Service)
- `file_url`: http://localhost:8081 (File Service)

### 3. Настройка путей к файлам
Обновите переменные для ваших тестовых файлов:
- `csv_file_path`: путь к CSV файлу
- `json_file_path`: путь к JSON файлу  
- `xml_file_path`: путь к XML файлу

## 🧪 Тестовые сценарии

### Сценарий 1: Музейные билеты (CSV)
```json
{
    "file_id": "museum-tickets-001",
    "user_id": "user-123",
    "file_path": "users/user-123/museum-tickets-001/museum_tickets.csv"
}
```

### Сценарий 2: Данные продаж (CSV)
```json
{
    "file_id": "sales-data-002", 
    "user_id": "user-456",
    "file_path": "users/user-456/sales-data-002/sales_data.csv"
}
```

### Сценарий 3: Пользовательские данные (JSON)
```json
{
    "file_id": "user-data-003",
    "user_id": "user-789", 
    "file_path": "users/user-789/user-data-003/users.json"
}
```

## 📊 Структура API

### Health Checks
- `GET /health` - проверка состояния сервисов

### File Upload
- `POST /api/v1/files/upload` - загрузка файлов
  - Поддерживаемые типы: CSV, JSON, XML
  - Параметры: file, user_id, file_type

### Data Analysis
- `POST /api/v1/analysis/start` - запуск анализа
  - Параметры: file_id, user_id, file_path
- `GET /api/v1/analysis/status/{analysis_id}` - статус анализа

## 🔄 Последовательность тестирования

### 1. Проверка здоровья сервисов
```
1. API Gateway Health
2. Data Analysis Service Health  
3. File Service Health
```

### 2. Загрузка файла
```
1. Upload CSV File
2. Сохраните file_id из ответа
```

### 3. Анализ данных
```
1. Start Analysis (используйте file_id из шага 2)
2. Сохраните analysis_id из ответа
3. Get Analysis Status (проверяйте каждые 30 секунд)
```

### 4. Получение результатов
```
1. Проверьте статус анализа
2. Получите DDL скрипт
3. Скачайте полный результат
```

## 📈 Ожидаемые результаты

### Успешный анализ возвращает:
```json
{
    "analysis_id": "analysis_xxx",
    "status": "completed",
    "progress": 100,
    "result": {
        "data_quality_score": 0.99,
        "ddl_metadata": { ... },
        "recommendations": [ ... ],
        "storage_recommendation": { ... },
        "table_schema": { ... }
    }
}
```

### DDL скрипт включает:
- CREATE TABLE с правильными типами
- CHECK constraints для валидации
- Индексы для оптимизации
- Комментарии для полей

## ⚠️ Важные моменты

### Время выполнения
- Анализ может занять **2-5 минут** для больших файлов
- LLM обрабатывает полный файл, не только заголовки
- Используйте `Get Analysis Status` для проверки прогресса

### Ограничения
- Максимальный размер файла: **10MB**
- Таймаут анализа: **10 минут**
- Поддерживаемые форматы: CSV, JSON, XML

### Отладка
- Проверьте логи сервисов: `docker-compose logs data-analysis-service`
- DDL скрипты сохраняются в `/tmp/analysis_{id}.json`
- При ошибках используйте fallback данные

## 🎯 Автоматизация

### Pre-request Scripts
- Автоматическая генерация уникальных ID
- Установка timestamp для уникальности

### Test Scripts  
- Автоматическое извлечение analysis_id
- Проверка статуса ответов
- Валидация структуры данных

## 📝 Примеры файлов для тестирования

### CSV файл (museum_tickets.csv)
```csv
created;order_status;ticket_status;ticket_price;visitor_category;event_id;is_active;valid_to;count_visitor;is_entrance;is_entrance_mdate;event_name;event_kind_name;spot_id;spot_name;museum_name;start_datetime;ticket_id;update_timestamp;client_name;name;surname;client_phone;museum_inn;birthday_date;order_number;ticket_number
2021-08-18T16:01:14.583+03:00;PAID;PAID;0.0;Обучающиеся по очной форме обучения;7561;true;2021-08-18;1;true;2021-08-18T19:14:45.427+03:00;Бальный танец;консультация;274010;Шверника ул. 13;Центр культуры;2021-08-18 17:00:00;1778482;2021-08-18T16:01:15.682+03:00;ШУКУРОВ РУСЛАН;КИРИЛЛ;ШУКУРОВ;79859482165;3832203597;;75343-483088;07a16922-969c-1033-a23f-f20b57dcf045
```

### JSON файл (users.json)
```json
[
    {
        "id": 1,
        "name": "Иван Иванов",
        "email": "ivan@example.com",
        "age": 25,
        "city": "Москва",
        "registration_date": "2021-01-15T10:30:00Z"
    },
    {
        "id": 2, 
        "name": "Петр Петров",
        "email": "petr@example.com",
        "age": 30,
        "city": "Санкт-Петербург",
        "registration_date": "2021-02-20T14:45:00Z"
    }
]
```

## 🚀 Готово к тестированию!

Импортируйте коллекцию и начинайте тестирование с разными файлами. Система автоматически:
- Анализирует структуру данных
- Генерирует качественные DDL скрипты
- Предоставляет рекомендации по хранению
- Создает индексы и ограничения