# 🔄 GitHub Actions

## 📖 Что такое GitHub Actions?

**GitHub Actions** — это CI/CD платформа, встроенная в GitHub, позволяющая автоматизировать сборку, тестирование и развертывание проектов с помощью workflow-файлов.

## 🛠️ Основные понятия
- **Workflow** — автоматизированный процесс (pipeline)
- **Job** — отдельная задача в workflow
- **Step** — шаг внутри job
- **Action** — переиспользуемый шаг (действие)
- **Runner** — агент для выполнения job
- **.github/workflows/** — директория с workflow-файлами

## 🚀 Быстрый старт

### Пример workflow
```yaml
name: CI/CD Pipeline
on:
  push:
    branches: [ main ]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Set up Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
      - name: Install dependencies
        run: npm install
      - name: Run tests
        run: npm test
      - name: Build
        run: npm run build
```

### Секреты и переменные
- Settings → Secrets and variables
- Использование в workflow:
```yaml
env:
  SECRET_KEY: ${{ secrets.SECRET_KEY }}
```

### Matrix builds
```yaml
strategy:
  matrix:
    node: [14, 16, 18]
steps:
  - uses: actions/setup-node@v3
    with:
      node-version: ${{ matrix.node }}
```

### Кэширование
```yaml
- name: Cache node modules
  uses: actions/cache@v3
  with:
    path: node_modules
    key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
```

## 🛡️ Безопасность
- Используйте secrets для хранения ключей
- Огранивайте доступ к workflow через branch protection
- Используйте review для production workflow

## ☸️ Kubernetes и GitHub Actions
- Деплой через kubectl, Helm, ArgoCD
- Использование GitOps

## 📝 Полезные действия (actions)
- actions/checkout — клонирование репозитория
- actions/setup-node — установка Node.js
- docker/build-push-action — работа с Docker
- hashicorp/setup-terraform — Terraform
- azure/login, aws-actions/configure-aws-credentials — облака

## 🔧 Интеграции
- Slack, Telegram, Email — уведомления
- DockerHub, GitHub Container Registry
- Terraform, Ansible, Pulumi

## 📊 Мониторинг workflow
- Вкладка Actions в репозитории
- Просмотр логов, статусов, времени
- Re-run, Cancel workflow

## 🔗 Связанные разделы
- [[02-GitLab-CI|GitLab CI]]
- [[04-ArgoCD|ArgoCD]]
- [[../03-Контейнеризация/03-Kubernetes|Kubernetes]]
- [[../07-IaC/01-Terraform|Terraform]]

---

*Следующий раздел: [[04-ArgoCD|ArgoCD]]* 