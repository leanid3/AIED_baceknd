# 🧪 Тестирование пользовательского workflow

## 📋 Последовательность действий пользователя

### 1. **Запуск системы**
```bash
# Запуск всех сервисов
./start.sh start

# Проверка статуса
./start.sh status

# Проверка системы
./check-system.sh
```

### 2. **Загрузка файла через API Gateway**

#### CSV файл (332MB)
```bash
curl -X POST http://localhost:8080/v1/files/upload/csv \
  -H "Content-Type: multipart/form-data" \
  -F "file=@_data/csv/part-00000-37dced01-2ad2-48c8-a56d-54b4d8760599-c000.csv" \
  -F "user_id=test-user-1"
```

#### JSON файл (415MB)
```bash
curl -X POST http://localhost:8080/v1/files/upload/json \
  -H "Content-Type: multipart/form-data" \
  -F "file=@_data/json/part1.json" \
  -F "user_id=test-user-2"
```

#### XML файл (937MB)
```bash
curl -X POST http://localhost:8080/v1/files/upload/xml \
  -H "Content-Type: multipart/form-data" \
  -F "file=@_data/xml/part1.xml" \
  -F "user_id=test-user-3"
```

### 3. **Создание диалога в Chat Service**

```bash
# Создание диалога для анализа CSV
curl -X POST http://localhost:8080/v1/dialogs \
  -H "Content-Type: application/json" \
  -d '{
    "user_id": "test-user-1",
    "title": "Анализ CSV данных",
    "initial_message": "Проанализируй загруженный CSV файл"
  }'

# Создание диалога для анализа JSON
curl -X POST http://localhost:8080/v1/dialogs \
  -H "Content-Type: application/json" \
  -d '{
    "user_id": "test-user-2",
    "title": "Анализ JSON данных",
    "initial_message": "Проанализируй загруженный JSON файл"
  }'

# Создание диалога для анализа XML
curl -X POST http://localhost:8080/v1/dialogs \
  -H "Content-Type: application/json" \
  -d '{
    "user_id": "test-user-3",
    "title": "Анализ XML данных",
    "initial_message": "Проанализируй загруженный XML файл"
  }'
```

### 4. **Создание пайплайна анализа**

```bash
# Создание пайплайна для CSV
curl -X POST http://localhost:8080/v1/pipelines \
  -H "Content-Type: application/json" \
  -d '{
    "source": {
      "type": {
        "file": {
          "format": "csv",
          "url": "users/test-user-1/[file_id]/part-00000-37dced01-2ad2-48c8-a56d-54b4d8760599-c000.csv"
        }
      }
    },
    "target": {
      "type": "postgres",
      "table_name": "analyzed_data"
    },
    "user_id": "test-user-1",
    "file_id": "[file_id_from_upload]"
  }'

# Создание пайплайна для JSON
curl -X POST http://localhost:8080/v1/pipelines \
  -H "Content-Type: application/json" \
  -d '{
    "source": {
      "type": {
        "file": {
          "format": "json",
          "url": "users/test-user-2/[file_id]/part1.json"
        }
      }
    },
    "target": {
      "type": "clickhouse",
      "table_name": "json_data"
    },
    "user_id": "test-user-2",
    "file_id": "[file_id_from_upload]"
  }'

# Создание пайплайна для XML
curl -X POST http://localhost:8080/v1/pipelines \
  -H "Content-Type: application/json" \
  -d '{
    "source": {
      "type": {
        "file": {
          "format": "xml",
          "url": "users/test-user-3/[file_id]/part1.xml"
        }
      }
    },
    "target": {
      "type": "hdfs",
      "table_name": "/data/xml_processed"
    },
    "user_id": "test-user-3",
    "file_id": "[file_id_from_upload]"
  }'
```

### 5. **Мониторинг выполнения**

```bash
# Проверка статуса пайплайна
curl -X GET http://localhost:8080/v1/pipelines/[pipeline_id]

# Проверка статуса DAG в Airflow
curl -X GET http://localhost:8082/api/v1/dags/data_analysis_test-user-1

# Проверка логов выполнения
docker-compose logs -f orchestration-service
```

### 6. **Проверка результатов**

```bash
# Получение информации о файле
curl -X GET http://localhost:8080/v1/files/[file_id]?user_id=test-user-1

# Получение истории диалога
curl -X GET http://localhost:8080/v1/dialogs/[dialog_id]/messages?user_id=test-user-1

# Проверка результатов в Airflow
curl -X GET http://localhost:8082/api/v1/dags/data_analysis_test-user-1/dagRuns
```

## 🔍 Ожидаемые результаты

### 1. **Загрузка файлов**
- ✅ Файлы сохраняются в MinIO
- ✅ Метаданные сохраняются в PostgreSQL
- ✅ Возвращается file_id для дальнейшего использования

### 2. **Создание диалогов**
- ✅ Диалоги создаются в Chat Service
- ✅ Сообщения сохраняются в базе данных
- ✅ Возвращается dialog_id

### 3. **Создание пайплайнов**
- ✅ DAG создается в Airflow
- ✅ Запускается анализ через LLM Service
- ✅ Генерируются рекомендации по структуре данных

### 4. **Выполнение анализа**
- ✅ LLM анализирует структуру данных
- ✅ Генерируются DDL скрипты
- ✅ Создаются рекомендации по оптимизации

### 5. **Уведомления**
- ✅ Статус обновляется в Chat Service
- ✅ Пользователь получает результаты анализа
- ✅ Создаются отчеты по качеству данных

## 🚨 Возможные проблемы и решения

### Проблема: Файл слишком большой
**Решение**: Увеличить лимиты gRPC в конфигурации

### Проблема: LLM не отвечает
**Решение**: Проверить статус Ollama и загрузить модель

### Проблема: Airflow не запускает DAG
**Решение**: Проверить подключение к Airflow и права доступа

### Проблема: MinIO недоступен
**Решение**: Проверить статус MinIO и настройки подключения

## 📊 Метрики для мониторинга

### Время выполнения
- Загрузка файла: < 30 секунд
- Анализ структуры: < 2 минут
- Генерация DDL: < 1 минуты
- Создание DAG: < 10 секунд

### Использование ресурсов
- Память: < 2GB на сервис
- CPU: < 50% на сервис
- Диск: < 1GB для временных файлов

### Качество данных
- Точность анализа: > 90%
- Полнота рекомендаций: > 80%
- Время отклика API: < 5 секунд

## 🔧 Команды для отладки

```bash
# Проверка всех сервисов
./check-system.sh

# Логи конкретного сервиса
docker-compose logs -f file-service
docker-compose logs -f chat-service
docker-compose logs -f llm-service
docker-compose logs -f orchestration-service

# Проверка подключений
docker-compose exec api-gateway ping file-service
docker-compose exec api-gateway ping chat-service

# Проверка базы данных
docker-compose exec postgres psql -U testuser -d testdb -c "SELECT * FROM files LIMIT 5;"

# Проверка MinIO
docker-compose exec minio mc ls minio/files
```

## 📝 Логирование

Все действия пользователя логируются в соответствующих сервисах:
- **File Service**: Загрузка, сохранение, удаление файлов
- **Chat Service**: Создание диалогов, отправка сообщений
- **LLM Service**: Анализ данных, генерация рекомендаций
- **Orchestration Service**: Создание DAG, мониторинг выполнения

## 🎯 Критерии успеха

1. ✅ Все файлы успешно загружаются
2. ✅ Диалоги создаются и работают
3. ✅ Пайплайны создаются в Airflow
4. ✅ LLM анализирует данные
5. ✅ Пользователь получает результаты
6. ✅ Система работает стабильно под нагрузкой