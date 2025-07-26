# ☁️ GCP (Google Cloud Platform)

## 📖 Что такое GCP?

**GCP** — облачная платформа от Google для развертывания, масштабирования и управления приложениями и инфраструктурой.

## 🛠️ Основные сервисы GCP
- **Compute Engine** — виртуальные машины
- **Cloud Storage** — объектное хранилище
- **Cloud SQL** — управляемые базы данных
- **VPC** — виртуальные сети
- **IAM** — управление доступом
- **GKE** — Kubernetes Engine
- **Cloud Functions** — serverless
- **Stackdriver** — мониторинг и логирование
- **Deployment Manager** — инфраструктура как код

## 🚀 Быстрый старт

### Регистрация и доступ
- Зарегистрируйтесь на https://console.cloud.google.com/
- Настройте gcloud CLI:
```bash
gcloud init
gcloud auth login
```

### Развертывание VM
```bash
gcloud compute instances create my-vm --zone=us-central1-a --image-family=debian-11 --image-project=debian-cloud
```

### Работа с Cloud Storage
```bash
gsutil mb gs://my-bucket
gsutil cp file.txt gs://my-bucket/
gsutil ls gs://my-bucket/
```

### Пример Deployment Manager
```yaml
resources:
- name: my-vm
  type: compute.v1.instance
  properties:
    zone: us-central1-a
    machineType: zones/us-central1-a/machineTypes/n1-standard-1
    disks:
    - deviceName: boot
      type: PERSISTENT
      boot: true
      autoDelete: true
      initializeParams:
        sourceImage: projects/debian-cloud/global/images/family/debian-11
    networkInterfaces:
    - network: global/networks/default
      accessConfigs:
      - name: External NAT
        type: ONE_TO_ONE_NAT
```

## 🔒 Безопасность в GCP
- Используйте IAM роли и сервисные аккаунты
- Включайте MFA
- Используйте Secret Manager
- Ограничивайте доступ по принципу наименьших привилегий

## 📊 Мониторинг и логирование
- Stackdriver Monitoring
- Stackdriver Logging
- Cloud Audit Logs

## 📝 Полезные команды gcloud/gsutil
```bash
gcloud compute instances list
gsutil ls
gcloud iam service-accounts list
gcloud deployment-manager deployments list
```

## 🛠️ Инфраструктура как код
- Deployment Manager
- Terraform

## 🔗 Связанные разделы
- [[01-AWS|AWS]]
- [[02-Azure|Azure]]
- [[../07-IaC/01-Terraform|Terraform]]
- [[../07-IaC/02-Ansible|Ansible]]

---

*Следующий раздел: [[../05-CI-CD/01-Jenkins|Jenkins]]* 