# ☁️ AWS (Amazon Web Services)

## 📖 Что такое AWS?

**AWS** — крупнейшая облачная платформа, предоставляющая инфраструктуру, сервисы и инструменты для разработки, развертывания и эксплуатации приложений.

## 🛠️ Основные сервисы AWS
- **EC2** — виртуальные серверы
- **S3** — объектное хранилище
- **RDS** — управляемые базы данных
- **VPC** — виртуальные сети
- **IAM** — управление доступом
- **Lambda** — serverless-функции
- **ECS/EKS** — контейнеры и Kubernetes
- **CloudWatch** — мониторинг
- **CloudFormation** — инфраструктура как код

## 🚀 Быстрый старт

### Регистрация и доступ
- Зарегистрируйтесь на https://aws.amazon.com/
- Создайте IAM пользователя с минимальными правами
- Настройте AWS CLI:
```bash
aws configure
```

### Развертывание EC2
```bash
aws ec2 run-instances --image-id ami-12345678 --count 1 --instance-type t2.micro --key-name my-key --security-group-ids sg-123456 --subnet-id subnet-123456
```

### Работа с S3
```bash
aws s3 mb s3://my-bucket
aws s3 cp file.txt s3://my-bucket/
aws s3 ls s3://my-bucket/
```

### Пример CloudFormation
```yaml
Resources:
  MyBucket:
    Type: AWS::S3::Bucket
    Properties:
      BucketName: my-bucket
```

## 🔒 Безопасность в AWS
- Используйте IAM роли и политики
- Включайте MFA для важных аккаунтов
- Не храните ключи в коде, используйте Secrets Manager
- Ограничивайте доступ по принципу наименьших привилегий

## 📊 Мониторинг и логирование
- CloudWatch (метрики, логи, алерты)
- CloudTrail (аудит действий)
- VPC Flow Logs (сетевой трафик)

## 📝 Полезные команды AWS CLI
```bash
aws ec2 describe-instances
aws s3 ls
aws iam list-users
aws cloudformation list-stacks
```

## 🛠️ Инфраструктура как код
- CloudFormation
- Terraform
- CDK (Cloud Development Kit)

## 🔗 Связанные разделы
- [[02-Azure|Azure]]
- [[03-GCP|GCP]]
- [[../07-IaC/01-Terraform|Terraform]]
- [[../07-IaC/02-Ansible|Ansible]]

---

*Следующий раздел: [[02-Azure|Azure]]*
