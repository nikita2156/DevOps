# 🔄 GitLab CI

## 📖 Что такое GitLab CI?

**GitLab CI/CD** — это встроенная система непрерывной интеграции и доставки в GitLab, позволяющая автоматизировать сборку, тестирование и развертывание проектов.

## 🛠️ Основные понятия
- **Pipeline** — последовательность этапов (stages)
- **Job** — отдельная задача в pipeline
- **Runner** — агент, выполняющий job
- **.gitlab-ci.yml** — файл конфигурации pipeline

## 🚀 Быстрый старт

### Пример .gitlab-ci.yml
```yaml
stages:
  - build
  - test
  - deploy

build:
  stage: build
  script:
    - echo "Building..."

test:
  stage: test
  script:
    - echo "Testing..."

deploy:
  stage: deploy
  script:
    - echo "Deploying..."
  only:
    - main
```

### Запуск pipeline
- Автоматически при push/merge request
- Вручную через UI (Run pipeline)

### Переменные окружения
- UI: Settings → CI/CD → Variables
- В .gitlab-ci.yml:
```yaml
variables:
  ENV: production
```

### Кэширование и артефакты
```yaml
cache:
  paths:
    - node_modules/

artifacts:
  paths:
    - build/
```

### Matrix jobs (динамические job)
```yaml
test:python:
  script: pytest
  image: python:3.10

test:node:
  script: npm test
  image: node:18
```

## 🛡️ Безопасность
- Используйте protected variables для секретов
- Разделяйте окружения (staging, production)
- Используйте approval jobs для production
- Включайте SAST/DAST (Security & Compliance)

## ☸️ Kubernetes и GitLab CI
- Автоматическое развертывание в K8s через Auto DevOps
- Интеграция с Helm, kubectl, Kustomize

## 📝 Полезные команды
```bash
gitlab-runner register
# Локальный запуск job
gitlab-runner exec docker job_name
```

## 🔧 Интеграции
- Slack, Telegram, Email — уведомления
- Docker Registry — публикация образов
- Terraform, Ansible — IaC

## 📊 Мониторинг pipeline
- Вкладка CI/CD → Pipelines
- Просмотр логов, времени выполнения, статусов
- Retry/Cancel pipeline

## 🔗 Связанные разделы
- [[03-GitHub-Actions|GitHub Actions]]
- [[04-ArgoCD|ArgoCD]]
- [[../03-Контейнеризация/03-Kubernetes|Kubernetes]]
- [[../07-IaC/01-Terraform|Terraform]]

---

*Следующий раздел: [[03-GitHub-Actions|GitHub Actions]]* 