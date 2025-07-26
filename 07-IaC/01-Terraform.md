# 🔧 Terraform

## 📖 Что такое Terraform?

**Terraform** — это инструмент для управления инфраструктурой как код (Infrastructure as Code) от HashiCorp. Он позволяет описывать, планировать и создавать инфраструктуру с помощью декларативных конфигурационных файлов.

### 🎯 Преимущества Terraform

- **Декларативный подход** — описываете желаемое состояние
- **Мультиоблачный** — поддержка AWS, Azure, GCP и других
- **Версионирование** — контроль версий инфраструктуры
- **Повторяемость** — одинаковые результаты при каждом запуске
- **Автоматизация** — интеграция с CI/CD

## 🚀 Установка Terraform

### Linux
```bash
# Скачать Terraform
wget https://releases.hashicorp.com/terraform/1.5.0/terraform_1.5.0_linux_amd64.zip

# Распаковать
unzip terraform_1.5.0_linux_amd64.zip

# Переместить в PATH
sudo mv terraform /usr/local/bin/

# Проверить установку
terraform version
```

### macOS
```bash
# Установка через Homebrew
brew install terraform

# Или скачать вручную
curl -O https://releases.hashicorp.com/terraform/1.5.0/terraform_1.5.0_darwin_amd64.zip
unzip terraform_1.5.0_darwin_amd64.zip
sudo mv terraform /usr/local/bin/
```

### Windows
```bash
# Скачать с официального сайта
# https://releases.hashicorp.com/terraform/1.5.0/terraform_1.5.0_windows_amd64.zip

# Распаковать и добавить в PATH
```

## 📝 Основы синтаксиса

### Структура файлов
```
project/
├── main.tf          # Основная конфигурация
├── variables.tf     # Переменные
├── outputs.tf       # Выходные значения
├── providers.tf     # Провайдеры
├── terraform.tfvars # Значения переменных
└── .tfstate         # Состояние (автоматически)
```

### Базовый пример
```hcl
# providers.tf
terraform {
  required_version = ">= 1.0"
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region = "us-west-2"
}

# main.tf
resource "aws_instance" "web" {
  ami           = "ami-12345678"
  instance_type = "t2.micro"
  
  tags = {
    Name = "WebServer"
    Environment = "Production"
  }
}

# variables.tf
variable "instance_type" {
  description = "EC2 instance type"
  type        = string
  default     = "t2.micro"
}

variable "environment" {
  description = "Environment name"
  type        = string
  validation {
    condition     = contains(["dev", "staging", "prod"], var.environment)
    error_message = "Environment must be dev, staging, or prod."
  }
}

# outputs.tf
output "instance_id" {
  description = "ID of the EC2 instance"
  value       = aws_instance.web.id
}

output "public_ip" {
  description = "Public IP of the EC2 instance"
  value       = aws_instance.web.public_ip
}
```

## 🔄 Основные команды

### Инициализация
```bash
# Инициализация проекта
terraform init

# Инициализация с плагинами
terraform init -upgrade

# Инициализация с backend
terraform init -backend-config="bucket=my-terraform-state"
```

### Планирование и применение
```bash
# Планирование изменений
terraform plan

# Планирование с переменными
terraform plan -var="instance_type=t2.small"

# Применение изменений
terraform apply

# Применение с подтверждением
terraform apply -auto-approve

# Применение с переменными
terraform apply -var-file="production.tfvars"
```

### Управление состоянием
```bash
# Просмотр состояния
terraform show

# Список ресурсов
terraform state list

# Детали ресурса
terraform state show aws_instance.web

# Перемещение ресурса
terraform state mv aws_instance.web aws_instance.web_new

# Удаление из состояния
terraform state rm aws_instance.web
```

### Уничтожение инфраструктуры
```bash
# Планирование уничтожения
terraform plan -destroy

# Уничтожение
terraform destroy

# Уничтожение конкретного ресурса
terraform destroy -target=aws_instance.web
```

## 🏗️ Ресурсы и модули

### Создание ресурсов
```hcl
# EC2 Instance
resource "aws_instance" "web" {
  ami           = data.aws_ami.ubuntu.id
  instance_type = var.instance_type
  
  vpc_security_group_ids = [aws_security_group.web.id]
  subnet_id              = aws_subnet.public.id
  
  user_data = templatefile("${path.module}/user_data.sh", {
    server_name = "Web Server"
  })
  
  tags = {
    Name = "${var.environment}-web-server"
  }
}

# Security Group
resource "aws_security_group" "web" {
  name_prefix = "web-sg-"
  vpc_id      = aws_vpc.main.id
  
  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  
  ingress {
    from_port   = 443
    to_port     = 443
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}
```

### Использование модулей
```hcl
# Создание модуля
# modules/ec2/main.tf
resource "aws_instance" "this" {
  ami           = var.ami
  instance_type = var.instance_type
  
  tags = merge(var.tags, {
    Name = var.name
  })
}

# modules/ec2/variables.tf
variable "ami" {
  description = "AMI ID"
  type        = string
}

variable "instance_type" {
  description = "Instance type"
  type        = string
  default     = "t2.micro"
}

variable "name" {
  description = "Instance name"
  type        = string
}

variable "tags" {
  description = "Additional tags"
  type        = map(string)
  default     = {}
}

# modules/ec2/outputs.tf
output "instance_id" {
  description = "Instance ID"
  value       = aws_instance.this.id
}

output "public_ip" {
  description = "Public IP"
  value       = aws_instance.this.public_ip
}

# Использование модуля
module "web_server" {
  source = "./modules/ec2"
  
  ami           = "ami-12345678"
  instance_type = "t2.small"
  name          = "web-server"
  
  tags = {
    Environment = "Production"
    Project     = "WebApp"
  }
}
```

## 🔐 Управление секретами

### Переменные окружения
```bash
# Установка переменных
export TF_VAR_db_password="secret123"
export TF_VAR_api_key="key123"

# Использование в Terraform
variable "db_password" {
  description = "Database password"
  type        = string
  sensitive   = true
}
```

### HashiCorp Vault
```hcl
# Получение секретов из Vault
data "vault_generic_secret" "database" {
  path = "secret/database"
}

resource "aws_db_instance" "main" {
  password = data.vault_generic_secret.database.data["password"]
}
```

### AWS Secrets Manager
```hcl
# Получение секрета
data "aws_secretsmanager_secret" "database" {
  name = "prod/database"
}

data "aws_secretsmanager_secret_version" "database" {
  secret_id = data.aws_secretsmanager_secret.database.id
}

locals {
  db_creds = jsondecode(data.aws_secretsmanager_secret_version.database.secret_string)
}

resource "aws_db_instance" "main" {
  password = local.db_creds.password
}
```

## 📊 Backend конфигурация

### S3 Backend
```hcl
terraform {
  backend "s3" {
    bucket = "my-terraform-state"
    key    = "prod/terraform.tfstate"
    region = "us-west-2"
    
    dynamodb_table = "terraform-locks"
    encrypt        = true
  }
}
```

### Remote Backend
```hcl
terraform {
  backend "remote" {
    organization = "my-org"
    
    workspaces {
      name = "production"
    }
  }
}
```

### Local Backend
```hcl
terraform {
  backend "local" {
    path = "terraform.tfstate"
  }
}
```

## 🔄 Workspaces

### Управление workspace
```bash
# Создание workspace
terraform workspace new staging

# Переключение workspace
terraform workspace select production

# Список workspace
terraform workspace list

# Удаление workspace
terraform workspace delete staging
```

### Использование в коде
```hcl
locals {
  environment = terraform.workspace
}

resource "aws_instance" "web" {
  tags = {
    Environment = local.environment
    Workspace   = terraform.workspace
  }
}
```

## 🐳 Terraform с Docker

### Docker Provider
```hcl
provider "docker" {
  host = "unix:///var/run/docker.sock"
}

resource "docker_image" "nginx" {
  name = "nginx:latest"
}

resource "docker_container" "web" {
  name  = "web-server"
  image = docker_image.nginx.image_id
  
  ports {
    internal = 80
    external = 8080
  }
}
```

### Docker Compose через Terraform
```hcl
resource "docker_network" "private_network" {
  name = "my_private_network"
}

resource "docker_container" "app" {
  name  = "app"
  image = "myapp:latest"
  
  networks_advanced {
    name = docker_network.private_network.name
  }
}

resource "docker_container" "db" {
  name  = "database"
  image = "postgres:13"
  
  networks_advanced {
    name = docker_network.private_network.name
  }
}
```

## ☁️ Облачные провайдеры

### AWS
```hcl
provider "aws" {
  region = "us-west-2"
  
  default_tags {
    tags = {
      Environment = "Production"
      ManagedBy   = "Terraform"
    }
  }
}

resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"
  
  tags = {
    Name = "main-vpc"
  }
}
```

### Azure
```hcl
provider "azurerm" {
  features {}
}

resource "azurerm_resource_group" "main" {
  name     = "my-resource-group"
  location = "West US 2"
}

resource "azurerm_virtual_network" "main" {
  name                = "main-vnet"
  resource_group_name = azurerm_resource_group.main.name
  location            = azurerm_resource_group.main.location
  address_space       = ["10.0.0.0/16"]
}
```

### Google Cloud
```hcl
provider "google" {
  project = "my-project-id"
  region  = "us-central1"
}

resource "google_compute_network" "main" {
  name                    = "main-network"
  auto_create_subnetworks = false
}

resource "google_compute_subnetwork" "main" {
  name          = "main-subnet"
  ip_cidr_range = "10.0.1.0/24"
  network       = google_compute_network.main.id
}
```

## 🔍 Data Sources

### Поиск существующих ресурсов
```hcl
# Поиск AMI
data "aws_ami" "ubuntu" {
  most_recent = true
  
  filter {
    name   = "name"
    values = ["ubuntu/images/hvm-ssd/ubuntu-focal-20.04-amd64-server-*"]
  }
  
  filter {
    name   = "virtualization-type"
    values = ["hvm"]
  }
  
  owners = ["099720109477"] # Canonical
}

# Поиск VPC
data "aws_vpc" "default" {
  default = true
}

# Поиск subnet
data "aws_subnet" "selected" {
  vpc_id = data.aws_vpc.default.id
  availability_zone = "us-west-2a"
}
```

## 🔄 Conditional Resources

### Условное создание ресурсов
```hcl
variable "create_database" {
  description = "Whether to create database"
  type        = bool
  default     = false
}

resource "aws_db_instance" "main" {
  count = var.create_database ? 1 : 0
  
  allocated_storage = 20
  engine            = "mysql"
  instance_class    = "db.t3.micro"
  
  tags = {
    Name = "main-database"
  }
}

# Использование условного ресурса
output "database_endpoint" {
  value = var.create_database ? aws_db_instance.main[0].endpoint : null
}
```

## 🔗 Связанные разделы

- [[02-Ansible|Ansible]]
- [[03-Puppet|Puppet]]
- [[../04-Облачные-платформы/01-AWS|AWS]]
- [[../04-Облачные-платформы/02-Azure|Azure]]

---

*Следующий раздел: [[02-Ansible|Ansible]]* 