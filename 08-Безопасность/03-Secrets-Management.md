# 🛡️ Secrets Management (Управление секретами)

## 📖 Введение

Secrets Management — это практика безопасного хранения, передачи и использования чувствительных данных (пароли, ключи, токены) в инфраструктуре и приложениях.

## 🛠️ Основные подходы
- **Не храните секреты в коде и репозиториях**
- Используйте специальные хранилища (Vault, AWS Secrets Manager, Azure Key Vault, GCP Secret Manager)
- Ограничивайте доступ по принципу наименьших привилегий
- Используйте автоматическую ротацию секретов
- Логируйте доступ к секретам

## 🔧 Популярные инструменты
- **HashiCorp Vault** — универсальное хранилище секретов
- **AWS Secrets Manager** — для AWS
- **Azure Key Vault** — для Azure
- **GCP Secret Manager** — для GCP
- **Kubernetes Secrets** — для K8s
- **SOPS, Sealed Secrets** — для GitOps

## 🚀 Примеры использования

### HashiCorp Vault
```bash
vault kv put secret/myapp password=123456
vault kv get secret/myapp
```

### AWS Secrets Manager
```bash
aws secretsmanager create-secret --name mydb --secret-string '{"password":"123456"}'
aws secretsmanager get-secret-value --secret-id mydb
```

### Kubernetes Secrets
```bash
kubectl create secret generic mysecret --from-literal=password=123456
kubectl get secret mysecret -o yaml
```

## 🛡️ Best practices
- Используйте отдельные секреты для разных окружений
- Внедряйте автоматическую ротацию
- Не передавайте секреты через переменные окружения без необходимости
- Используйте шифрование на уровне хранения и передачи
- Внедряйте аудит доступа

## 📚 Полезные ресурсы
- [HashiCorp Vault](https://www.vaultproject.io/)
- [AWS Secrets Manager](https://aws.amazon.com/secrets-manager/)
- [OWASP Secrets Management](https://owasp.org/www-project-secrets-management/)

## 🔗 Связанные разделы
- [[01-Основы-безопасности|Основы безопасности]]
- [[02-Сканирование-уязвимостей|Сканирование уязвимостей]]
- [[../03-Контейнеризация/03-Kubernetes|Kubernetes]]

---

*Следующий раздел: [[../09-Производительность/01-Оптимизация-производительности|Оптимизация производительности]]* 