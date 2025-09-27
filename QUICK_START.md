# 🚀 Быстрый старт - AIEN Backend API

## 📥 Импорт в Postman

1. **Скачайте файл:** `AIEN_Backend_API.postman_collection.json`
2. **Откройте Postman** → Import → выберите файл
3. **Коллекция готова к использованию!**

## 🧪 Тестирование за 3 шага

### Шаг 1: Проверка сервисов
```
1. Health Checks → API Gateway Health
2. Health Checks → Data Analysis Service Health  
3. Health Checks → File Service Health
```

### Шаг 2: Анализ файла
```
1. Data Analysis → Start Analysis
   - file_id: "test-file-123"
   - user_id: "test-user-123" 
   - file_path: "users/test-user-123/test-file-123/data.csv"

2. Сохраните analysis_id из ответа
```

### Шаг 3: Получение результата
```
1. Data Analysis → Get Analysis Status
   - Используйте analysis_id из шага 2
   - Повторяйте каждые 30 секунд до status: "completed"
```

## 📊 Ожидаемый результат

```json
{
    "status": "completed",
    "result": {
        "data_quality_score": 0.99,
        "ddl_metadata": { ... },
        "recommendations": [ ... ],
        "table_schema": { ... }
    }
}
```

## 🎯 Готовые тестовые файлы

В папке `test_data/`:
- `sales_data.csv` - данные продаж
- `users.json` - пользовательские данные  
- `products.xml` - каталог товаров

## ⚡ Автоматизация

Коллекция автоматически:
- ✅ Генерирует уникальные ID
- ✅ Извлекает analysis_id из ответов
- ✅ Проверяет статус ответов
- ✅ Валидирует структуру данных

## ⏱️ Время выполнения

- **Анализ:** 2-5 минут
- **LLM обрабатывает полный файл**
- **Генерирует качественный DDL**

## 🔧 Настройка переменных

Обновите пути к файлам:
- `csv_file_path`: путь к CSV файлу
- `json_file_path`: путь к JSON файлу
- `xml_file_path`: путь к XML файлу

## 🎉 Готово!

Начинайте тестирование с любыми файлами - система автоматически проанализирует данные и сгенерирует DDL скрипт!