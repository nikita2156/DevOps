# 📊 Grafana

## 📖 Что такое Grafana?

**Grafana** — это open-source платформа для визуализации и анализа метрик, логов и других данных. Используется для построения дашбордов и мониторинга инфраструктуры, приложений и бизнес-процессов.

### 🎯 Преимущества Grafana
- **Гибкая визуализация** — графики, таблицы, алерты
- **Поддержка множества источников данных** — Prometheus, InfluxDB, Elasticsearch, Loki, MySQL и др.
- **Алертинг** — гибкая система оповещений
- **Пользовательские дашборды** — настройка под любые задачи
- **Плагины** — расширение функционала

## 🚀 Установка Grafana

### Docker
```bash
docker run -d --name=grafana -p 3000:3000 grafana/grafana
```

### Ubuntu/Debian
```bash
sudo apt-get install -y apt-transport-https
sudo apt-get install -y software-properties-common wget
wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
echo "deb https://packages.grafana.com/oss/deb stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
sudo apt-get update
sudo apt-get install grafana
sudo systemctl start grafana-server
sudo systemctl enable grafana-server
```

## ⚙️ Первоначальная настройка
- Вход по адресу: http://localhost:3000 (логин/пароль: admin/admin)
- Добавьте источник данных (Data Source), например Prometheus
- Импортируйте или создайте дашборд

## 🛠️ Основные возможности

### Источники данных
- Prometheus
- InfluxDB
- Elasticsearch
- Loki (логи)
- MySQL/PostgreSQL
- CloudWatch

### Дашборды
- Графики (Graph, Time series)
- Таблицы (Table)
- Pie Chart, Gauge, Bar Gauge
- Heatmap, Stat, Logs

### Алерты
- Настройка алертов на панели
- Интеграция с email, Slack, Telegram, Webhook

### Пользовательские переменные
- Динамические фильтры для дашбордов

### Плагины
- Datasource plugins (Zabbix, Azure Monitor)
- Panel plugins (Pie Chart, Worldmap)
- App plugins (Kubernetes App)

## 📦 Примеры дашбордов
- Системные метрики (CPU, RAM, Disk)
- Метрики приложений (HTTP, DB, API)
- Kubernetes мониторинг
- CI/CD мониторинг
- Бизнес-метрики

## 🔒 Безопасность
- Пользователи и роли (Admin, Editor, Viewer)
- Организации и папки
- Аудит логов
- SSO, OAuth, LDAP

## 📊 Интеграция с Prometheus
- Добавьте Prometheus как Data Source
- Импортируйте готовые дашборды с https://grafana.com/grafana/dashboards
- Используйте PromQL для построения графиков

## 📝 Примеры запросов
```promql
# Количество HTTP запросов
sum(rate(http_requests_total[5m]))

# Загрузка CPU
avg(rate(node_cpu_seconds_total{mode!="idle"}[5m])) by (instance)

# Использование памяти
node_memory_Active_bytes / node_memory_MemTotal_bytes * 100
```

## 🛠️ Управление Grafana
```bash
# Резервное копирование
cp -r /var/lib/grafana /backup/grafana

# Восстановление
cp -r /backup/grafana /var/lib/grafana

# Управление пользователями
grafana-cli admin reset-admin-password newpassword

# Установка плагинов
grafana-cli plugins install grafana-piechart-panel
grafana-cli plugins install grafana-worldmap-panel
```

## 🔗 Связанные разделы
- [[01-Prometheus|Prometheus]]
- [[03-ELK-Stack|ELK Stack]]
- [[../03-Контейнеризация/03-Kubernetes|Kubernetes]]

---

*Следующий раздел: [[03-ELK-Stack|ELK Stack]]* 