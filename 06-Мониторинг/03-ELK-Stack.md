# 📊 ELK Stack (Elasticsearch, Logstash, Kibana)

## 📖 Что такое ELK Stack?

**ELK Stack** — это набор инструментов для сбора, хранения, поиска и визуализации логов и событий:
- **Elasticsearch** — поисковый движок и хранилище
- **Logstash** — сбор, обработка и парсинг логов
- **Kibana** — визуализация и анализ данных

### 🎯 Преимущества ELK
- Централизованное логирование
- Мощный поиск и фильтрация
- Визуализация и дашборды
- Гибкая обработка данных
- Масштабируемость

## 🚀 Установка ELK Stack

### Docker Compose
```yaml
version: '3.8'
services:
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:8.13.0
    environment:
      - discovery.type=single-node
      - xpack.security.enabled=false
    ports:
      - "9200:9200"
    volumes:
      - esdata:/usr/share/elasticsearch/data

  logstash:
    image: docker.elastic.co/logstash/logstash:8.13.0
    ports:
      - "5000:5000"
    volumes:
      - ./logstash.conf:/usr/share/logstash/pipeline/logstash.conf
    depends_on:
      - elasticsearch

  kibana:
    image: docker.elastic.co/kibana/kibana:8.13.0
    ports:
      - "5601:5601"
    depends_on:
      - elasticsearch

volumes:
  esdata:
```

## ⚙️ Конфигурация Logstash
```conf
input {
  beats {
    port => 5044
  }
}
filter {
  grok {
    match => { "message" => "%{COMBINEDAPACHELOG}" }
  }
}
output {
  elasticsearch {
    hosts => ["elasticsearch:9200"]
    index => "logs-%{+YYYY.MM.dd}"
  }
}
```

## 🛠️ Основные возможности
- Сбор логов с серверов, приложений, контейнеров
- Парсинг и фильтрация (grok, mutate, date)
- Хранение и поиск по миллионам событий
- Визуализация в Kibana (графики, таблицы, карты)
- Алерты и оповещения

## 📦 Beats
- **Filebeat** — сбор логов с файлов
- **Metricbeat** — сбор метрик
- **Packetbeat** — сетевой анализ
- **Heartbeat** — мониторинг доступности

## 📝 Примеры запросов Kibana
- Поиск по уровню логов: `level:ERROR`
- Поиск по сервису: `service:nginx`
- Поиск по времени: `@timestamp:[now-1h TO now]`
- Агрегация по IP: `terms aggregation on client_ip`

## 🔒 Безопасность
- Роли и пользователи
- TLS/SSL между компонентами
- Аудит логов

## 📊 Интеграция с другими системами
- Prometheus (экспорт метрик)
- Grafana (источник данных)
- SIEM решения

## 🛠️ Управление ELK
```bash
# Проверка статуса
curl -X GET "localhost:9200/_cluster/health?pretty"

# Список индексов
curl -X GET "localhost:9200/_cat/indices?v"

# Удаление индекса
curl -X DELETE "localhost:9200/index_name"

# Импорт дашборда в Kibana
curl -X POST "localhost:5601/api/saved_objects/_import" -H "kbn-xsrf: true" --form file=@dashboard.ndjson
```

## 🔗 Связанные разделы
- [[01-Prometheus|Prometheus]]
- [[02-Grafana|Grafana]]
- [[../03-Контейнеризация/03-Kubernetes|Kubernetes]]

---

*Следующий раздел: [[../03-Контейнеризация/03-Kubernetes|Kubernetes]]* 