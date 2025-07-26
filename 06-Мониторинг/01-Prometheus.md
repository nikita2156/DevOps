# 📊 Prometheus

## 📖 Что такое Prometheus?

**Prometheus** — это open-source система мониторинга и алертинга, разработанная SoundCloud. Она собирает метрики с различных источников, хранит их в временной базе данных и предоставляет мощный язык запросов для анализа данных.

### 🎯 Основные возможности

- **Сбор метрик** — pull-модель сбора данных
- **Хранение данных** — временная база данных
- **Запросы** — язык PromQL для анализа
- **Алертинг** — система уведомлений
- **Визуализация** — интеграция с Grafana

## 🏗️ Архитектура Prometheus

### Компоненты
- **Prometheus Server** — основной сервер сбора и хранения метрик
- **Exporters** — агенты для экспорта метрик
- **Alertmanager** — управление алертами
- **Pushgateway** — промежуточное хранилище для push-метрик
- **Service Discovery** — автоматическое обнаружение целей

### Модель данных
```
Метрика: http_requests_total
Метки: {method="GET", status="200", endpoint="/api"}
Значение: 1234
Временная метка: 1640995200
```

## 🚀 Установка Prometheus

### Docker
```bash
# Создать docker-compose.yml
version: '3.8'
services:
  prometheus:
    image: prom/prometheus:latest
    container_name: prometheus
    ports:
      - "9090:9090"
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
      - prometheus_data:/prometheus
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
      - '--storage.tsdb.path=/prometheus'
      - '--web.console.libraries=/etc/prometheus/console_libraries'
      - '--web.console.templates=/etc/prometheus/consoles'
      - '--storage.tsdb.retention.time=200h'
      - '--web.enable-lifecycle'

volumes:
  prometheus_data:
```

### Ubuntu/Debian
```bash
# Скачать Prometheus
wget https://github.com/prometheus/prometheus/releases/download/v2.45.0/prometheus-2.45.0.linux-amd64.tar.gz
tar xvf prometheus-2.45.0.linux-amd64.tar.gz
cd prometheus-2.45.0

# Создать пользователя
sudo useradd --no-create-home --shell /bin/false prometheus

# Скопировать файлы
sudo cp prometheus /usr/local/bin/
sudo cp promtool /usr/local/bin/
sudo chown prometheus:prometheus /usr/local/bin/prometheus
sudo chown prometheus:prometheus /usr/local/bin/promtool

# Создать директории
sudo mkdir /etc/prometheus
sudo mkdir /var/lib/prometheus
sudo chown prometheus:prometheus /etc/prometheus
sudo chown prometheus:prometheus /var/lib/prometheus
```

## ⚙️ Конфигурация Prometheus

### Основной конфигурационный файл
```yaml
# prometheus.yml
global:
  scrape_interval: 15s
  evaluation_interval: 15s

rule_files:
  - "alert_rules.yml"

alerting:
  alertmanagers:
    - static_configs:
        - targets:
          - alertmanager:9093

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'node-exporter'
    static_configs:
      - targets: ['localhost:9100']

  - job_name: 'docker'
    static_configs:
      - targets: ['localhost:9323']

  - job_name: 'application'
    static_configs:
      - targets: ['localhost:8080']
    metrics_path: '/metrics'
    scrape_interval: 30s
```

### Service Discovery
```yaml
scrape_configs:
  - job_name: 'kubernetes-pods'
    kubernetes_sd_configs:
      - role: pod
    relabel_configs:
      - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
        action: keep
        regex: true
      - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
        action: replace
        target_label: __metrics_path__
        regex: (.+)
      - source_labels: [__address__, __meta_kubernetes_pod_annotation_prometheus_io_port]
        action: replace
        regex: ([^:]+)(?::\d+)?;(\d+)
        replacement: $1:$2
        target_label: __address__
```

## 📊 Экспортеры метрик

### Node Exporter
```bash
# Установка Node Exporter
docker run -d \
  --name node-exporter \
  -p 9100:9100 \
  -v "/proc:/host/proc:ro" \
  -v "/sys:/host/sys:ro" \
  -v "/:/rootfs:ro" \
  prom/node-exporter \
  --path.procfs=/host/proc \
  --path.sysfs=/host/sys \
  --collector.filesystem.ignored-mount-points=^/(sys|proc|dev|host|etc)($$|/)
```

### Docker Exporter
```bash
# Включить метрики Docker
# В /etc/docker/daemon.json
{
  "metrics-addr": "0.0.0.0:9323",
  "experimental": true
}

# Перезапустить Docker
sudo systemctl restart docker
```

### Application Exporter
```python
# Python приложение с метриками
from prometheus_client import start_http_server, Counter, Histogram
import time

# Метрики
REQUEST_COUNT = Counter('http_requests_total', 'Total HTTP requests', ['method', 'endpoint'])
REQUEST_LATENCY = Histogram('http_request_duration_seconds', 'HTTP request latency')

# Экспорт метрик
start_http_server(8080)

@app.route('/api/data')
@REQUEST_LATENCY.time()
def get_data():
    REQUEST_COUNT.labels(method='GET', endpoint='/api/data').inc()
    return {'data': 'example'}
```

## 🔍 PromQL - язык запросов

### Базовые запросы
```promql
# Все метрики с именем http_requests_total
http_requests_total

# Метрики с фильтром по меткам
http_requests_total{method="GET", status="200"}

# Сумма всех запросов
sum(http_requests_total)

# Среднее время ответа за последние 5 минут
rate(http_request_duration_seconds_sum[5m]) / rate(http_request_duration_seconds_count[5m])
```

### Агрегации
```promql
# Группировка по методу
sum by (method) (http_requests_total)

# Группировка по статусу и эндпоинту
sum by (status, endpoint) (http_requests_total)

# Процент ошибок
sum(rate(http_requests_total{status=~"5.."}[5m])) / sum(rate(http_requests_total[5m])) * 100
```

### Функции
```promql
# Изменение за последний час
increase(http_requests_total[1h])

# Среднее значение за 5 минут
avg_over_time(cpu_usage_percent[5m])

# Максимальное значение за час
max_over_time(memory_usage_bytes[1h])

# Квантиль 95% времени ответа
histogram_quantile(0.95, rate(http_request_duration_seconds_bucket[5m]))
```

## 🚨 Система алертинга

### Правила алертов
```yaml
# alert_rules.yml
groups:
  - name: example
    rules:
      - alert: HighRequestLatency
        expr: histogram_quantile(0.95, rate(http_request_duration_seconds_bucket[5m])) > 0.5
        for: 10m
        labels:
          severity: warning
        annotations:
          summary: "High request latency on {{ $labels.instance }}"
          description: "95th percentile latency is {{ $value }}s"

      - alert: HighErrorRate
        expr: sum(rate(http_requests_total{status=~"5.."}[5m])) / sum(rate(http_requests_total[5m])) > 0.05
        for: 5m
        labels:
          severity: critical
        annotations:
          summary: "High error rate on {{ $labels.instance }}"
          description: "Error rate is {{ $value | humanizePercentage }}"

      - alert: InstanceDown
        expr: up == 0
        for: 1m
        labels:
          severity: critical
        annotations:
          summary: "Instance {{ $labels.instance }} down"
          description: "{{ $labels.instance }} has been down for more than 1 minute"
```

### Alertmanager конфигурация
```yaml
# alertmanager.yml
global:
  smtp_smarthost: 'localhost:587'
  smtp_from: 'alertmanager@company.com'

route:
  group_by: ['alertname']
  group_wait: 10s
  group_interval: 10s
  repeat_interval: 1h
  receiver: 'web.hook'

receivers:
  - name: 'web.hook'
    webhook_configs:
      - url: 'http://127.0.0.1:5001/'

  - name: 'email'
    email_configs:
      - to: 'team@company.com'
        send_resolved: true

  - name: 'slack'
    slack_configs:
      - api_url: 'https://hooks.slack.com/services/YOUR/SLACK/WEBHOOK'
        channel: '#alerts'
        title: '{{ template "slack.title" . }}'
        text: '{{ template "slack.text" . }}'
```

## 📈 Интеграция с Grafana

### Datasource конфигурация
```yaml
# grafana-datasource.yml
apiVersion: 1

datasources:
  - name: Prometheus
    type: prometheus
    access: proxy
    url: http://prometheus:9090
    isDefault: true
```

### Пример дашборда
```json
{
  "dashboard": {
    "title": "Application Metrics",
    "panels": [
      {
        "title": "Request Rate",
        "type": "graph",
        "targets": [
          {
            "expr": "sum(rate(http_requests_total[5m])) by (method)",
            "legendFormat": "{{method}}"
          }
        ]
      },
      {
        "title": "Error Rate",
        "type": "graph",
        "targets": [
          {
            "expr": "sum(rate(http_requests_total{status=~\"5..\"}[5m])) / sum(rate(http_requests_total[5m])) * 100",
            "legendFormat": "Error Rate %"
          }
        ]
      },
      {
        "title": "Response Time 95th Percentile",
        "type": "graph",
        "targets": [
          {
            "expr": "histogram_quantile(0.95, rate(http_request_duration_seconds_bucket[5m]))",
            "legendFormat": "95th Percentile"
          }
        ]
      }
    ]
  }
}
```

## 🐳 Prometheus в Kubernetes

### Helm установка
```bash
# Добавить репозиторий
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update

# Установить Prometheus
helm install prometheus prometheus-community/kube-prometheus-stack
```

### ServiceMonitor
```yaml
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: myapp-monitor
  namespace: monitoring
spec:
  selector:
    matchLabels:
      app: myapp
  endpoints:
  - port: metrics
    path: /metrics
    interval: 30s
```

### PodMonitor
```yaml
apiVersion: monitoring.coreos.com/v1
kind: PodMonitor
metadata:
  name: myapp-pod-monitor
  namespace: monitoring
spec:
  selector:
    matchLabels:
      app: myapp
  podMetricsEndpoints:
  - port: metrics
    path: /metrics
    interval: 30s
```

## 🔧 Мониторинг Prometheus

### Метрики самого Prometheus
```promql
# Количество активных целей
up

# Время последнего успешного scrape
prometheus_target_scrape_pool_sync_total

# Количество ошибок scrape
prometheus_target_scrape_pool_sync_failed_total

# Использование памяти
process_resident_memory_bytes

# Количество запросов к API
prometheus_http_requests_total
```

### Здоровье системы
```bash
# Проверка статуса
curl http://localhost:9090/-/healthy

# Проверка готовности
curl http://localhost:9090/-/ready

# Метрики системы
curl http://localhost:9090/metrics
```

## 🛠️ Оптимизация производительности

### Настройки хранения
```yaml
storage:
  tsdb:
    retention.time: 15d
    retention.size: 50GB
    wal-compression: true
    out-of-order-time-window: 10m
```

### Настройки scrape
```yaml
global:
  scrape_interval: 15s
  scrape_timeout: 10s
  evaluation_interval: 15s

scrape_configs:
  - job_name: 'fast-metrics'
    scrape_interval: 5s
    scrape_timeout: 3s
  - job_name: 'slow-metrics'
    scrape_interval: 60s
    scrape_timeout: 30s
```

## 🔗 Связанные разделы

- [[02-Grafana|Grafana]]
- [[03-ELK-Stack|ELK Stack]]
- [[../05-CI-CD/01-Jenkins|Jenkins]]
- [[../03-Контейнеризация/03-Kubernetes|Kubernetes]]

---

*Следующий раздел: [[02-Grafana|Grafana]]* 