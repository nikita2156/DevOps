# ☁️ Azure (Microsoft Azure)

## 📖 Что такое Azure?

**Azure** — облачная платформа от Microsoft для развертывания, управления и мониторинга приложений и инфраструктуры.

## 🛠️ Основные сервисы Azure
- **Virtual Machines** — виртуальные серверы
- **Blob Storage** — объектное хранилище
- **Azure SQL** — управляемые базы данных
- **Virtual Network** — виртуальные сети
- **Azure Active Directory** — управление доступом
- **App Service** — хостинг приложений
- **AKS** — Kubernetes сервис
- **Functions** — serverless
- **Monitor** — мониторинг
- **ARM Templates** — инфраструктура как код

## 🚀 Быстрый старт

### Регистрация и доступ
- Зарегистрируйтесь на https://portal.azure.com/
- Создайте сервисный principal для автоматизации:
```bash
az ad sp create-for-rbac --name myApp --role Contributor --scopes /subscriptions/<SUBSCRIPTION_ID>
```
- Настройте Azure CLI:
```bash
az login
```

### Развертывание VM
```bash
az vm create --resource-group myResourceGroup --name myVM --image UbuntuLTS --admin-username azureuser --generate-ssh-keys
```

### Работа с Blob Storage
```bash
az storage account create --name mystorageaccount --resource-group myResourceGroup --location eastus --sku Standard_LRS
az storage container create --account-name mystorageaccount --name mycontainer
az storage blob upload --account-name mystorageaccount --container-name mycontainer --file file.txt --name file.txt
```

### Пример ARM Template
```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "mystorageaccount",
      "location": "eastus",
      "sku": { "name": "Standard_LRS" },
      "kind": "StorageV2",
      "properties": {}
    }
  ]
}
```

## 🔒 Безопасность в Azure
- Используйте RBAC и группы безопасности
- Включайте MFA
- Используйте Key Vault для хранения секретов
- Ограничивайте доступ по принципу наименьших привилегий

## 📊 Мониторинг и логирование
- Azure Monitor (метрики, логи, алерты)
- Log Analytics
- Activity Log (аудит)

## 📝 Полезные команды Azure CLI
```bash
az vm list
az storage account list
az ad user list
az monitor metrics list
```

## 🛠️ Инфраструктура как код
- ARM Templates
- Terraform
- Bicep

## 🔗 Связанные разделы
- [[01-AWS|AWS]]
- [[03-GCP|GCP]]
- [[../07-IaC/01-Terraform|Terraform]]
- [[../07-IaC/02-Ansible|Ansible]]

---

*Следующий раздел: [[03-GCP|GCP]]* 