# 🔄 ArgoCD

## 📖 Что такое ArgoCD?

**ArgoCD** — это инструмент GitOps для автоматического развертывания и управления приложениями в Kubernetes на основе Git-репозиториев.

## 🛠️ Основные понятия
- **GitOps** — управление инфраструктурой через Git
- **Application** — объект ArgoCD, описывающий приложение
- **Sync** — синхронизация состояния кластера с Git
- **Rollback** — откат к предыдущей версии
- **Helm/Kustomize** — поддержка шаблонов

## 🚀 Быстрый старт

### Установка ArgoCD
```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### Доступ к UI
```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
# Откройте https://localhost:8080
```

### Логин
```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

### Пример Application
```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: my-app
  namespace: argocd
spec:
  project: default
  source:
    repoURL: 'https://github.com/org/repo.git'
    targetRevision: HEAD
    path: k8s
  destination:
    server: 'https://kubernetes.default.svc'
    namespace: default
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

## 🛡️ Безопасность
- RBAC для пользователей
- SSO (OIDC, LDAP)
- Шифрование секретов (Sealed Secrets, SOPS)

## ☸️ Интеграции
- Helm, Kustomize, Jsonnet
- Slack, Email, Webhook (уведомления)
- Prometheus, Grafana (мониторинг)

## 📝 Полезные команды
```bash
argocd login localhost:8080
argocd app list
argocd app sync my-app
argocd app rollback my-app 1
```

## 📊 Мониторинг и аудит
- UI и CLI для статусов
- История изменений и откатов
- Метрики Prometheus

## 🔗 Связанные разделы
- [[02-GitLab-CI|GitLab CI]]
- [[03-GitHub-Actions|GitHub Actions]]
- [[../03-Контейнеризация/03-Kubernetes|Kubernetes]]
- [[../06-Мониторинг/01-Prometheus|Prometheus]]

---

*Следующий раздел: [[../07-IaC/01-Terraform|Terraform]]* 