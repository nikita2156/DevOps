# 🐳 Docker основы

## 📖 Что такое Docker?

**Docker** — это платформа для разработки, доставки и запуска приложений в контейнерах. Контейнеры позволяют упаковать приложение со всеми его зависимостями в стандартизированный блок.

### 🎯 Преимущества Docker

- **Портативность** — работает одинаково на любой платформе
- **Изоляция** — приложения не влияют друг на друга
- **Эффективность** — меньше ресурсов чем виртуальные машины
- **Скорость** — быстрый запуск и остановка
- **Версионирование** — контроль версий образов

## 🏗️ Архитектура Docker

### Компоненты
- **Docker Daemon** — фоновый процесс, управляющий контейнерами
- **Docker Client** — CLI для взаимодействия с daemon
- **Docker Registry** — репозиторий образов (Docker Hub)
- **Docker Images** — шаблоны для создания контейнеров
- **Docker Containers** — запущенные экземпляры образов

## 🚀 Установка Docker

### Ubuntu/Debian
```bash
# Удалить старые версии
sudo apt remove docker docker-engine docker.io containerd runc

# Установить зависимости
sudo apt update
sudo apt install apt-transport-https ca-certificates curl gnupg lsb-release

# Добавить GPG ключ
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

# Добавить репозиторий
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Установить Docker
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io

# Добавить пользователя в группу docker
sudo usermod -aG docker $USER
```

### CentOS/RHEL
```bash
# Установить зависимости
sudo yum install -y yum-utils

# Добавить репозиторий
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

# Установить Docker
sudo yum install docker-ce docker-ce-cli containerd.io

# Запустить и включить Docker
sudo systemctl start docker
sudo systemctl enable docker

# Добавить пользователя в группу
sudo usermod -aG docker $USER
```

## 🖼️ Работа с образами

### Поиск образов
```bash
docker search nginx              # Поиск образа nginx
docker search --filter stars=100 nginx  # Поиск с фильтром
```

### Загрузка образов
```bash
docker pull nginx                # Загрузить образ nginx
docker pull nginx:latest         # Загрузить конкретную версию
docker pull ubuntu:20.04        # Загрузить Ubuntu 20.04
```

### Просмотр образов
```bash
docker images                    # Список всех образов
docker image ls                  # Альтернативная команда
docker images --format "table {{.Repository}}\t{{.Tag}}\t{{.Size}}"
```

### Удаление образов
```bash
docker rmi nginx                 # Удалить образ
docker rmi $(docker images -q)  # Удалить все образы
docker image prune               # Удалить неиспользуемые образы
```

## 📦 Работа с контейнерами

### Запуск контейнеров
```bash
# Простой запуск
docker run nginx

# Запуск в фоновом режиме
docker run -d nginx

# Запуск с именем
docker run -d --name my-nginx nginx

# Запуск с пробросом портов
docker run -d -p 8080:80 nginx

# Запуск с переменными окружения
docker run -d -e MYSQL_ROOT_PASSWORD=password mysql

# Запуск с монтированием томов
docker run -d -v /host/path:/container/path nginx
```

### Управление контейнерами
```bash
docker ps                       # Активные контейнеры
docker ps -a                    # Все контейнеры
docker stop container_id        # Остановить контейнер
docker start container_id       # Запустить контейнер
docker restart container_id     # Перезапустить контейнер
docker rm container_id          # Удалить контейнер
docker rm -f container_id       # Принудительно удалить
```

### Информация о контейнерах
```bash
docker logs container_id        # Логи контейнера
docker logs -f container_id     # Следить за логами
docker inspect container_id     # Подробная информация
docker top container_id         # Процессы в контейнере
docker stats                    # Статистика всех контейнеров
```

### Взаимодействие с контейнерами
```bash
# Выполнить команду в контейнере
docker exec container_id ls

# Интерактивная оболочка
docker exec -it container_id bash

# Копировать файлы
docker cp container_id:/path/file /host/path/
docker cp /host/file container_id:/path/
```

## 📝 Dockerfile

### Создание образа
```dockerfile
# Базовый образ
FROM ubuntu:20.04

# Метаданные
LABEL maintainer="devops@company.com"
LABEL version="1.0"

# Установка переменных окружения
ENV DEBIAN_FRONTEND=noninteractive

# Обновление системы
RUN apt update && apt upgrade -y

# Установка пакетов
RUN apt install -y nginx

# Копирование файлов
COPY index.html /var/www/html/

# Открытие порта
EXPOSE 80

# Команда запуска
CMD ["nginx", "-g", "daemon off;"]
```

### Сборка образа
```bash
docker build -t myapp:1.0 .    # Сборка с тегом
docker build -f Dockerfile.prod -t myapp:prod .  # Указать файл
```

### Многоэтапная сборка
```dockerfile
# Этап сборки
FROM node:16 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Этап продакшн
FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

## 🔗 Docker Compose

### Создание docker-compose.yml
```yaml
version: '3.8'

services:
  web:
    image: nginx:alpine
    ports:
      - "80:80"
    volumes:
      - ./html:/usr/share/nginx/html
    depends_on:
      - db

  db:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: password
      MYSQL_DATABASE: myapp
    volumes:
      - db_data:/var/lib/mysql

  redis:
    image: redis:alpine
    ports:
      - "6379:6379"

volumes:
  db_data:
```

### Команды Docker Compose
```bash
docker-compose up              # Запустить сервисы
docker-compose up -d           # Запустить в фоне
docker-compose down            # Остановить сервисы
docker-compose ps              # Статус сервисов
docker-compose logs            # Логи всех сервисов
docker-compose logs service    # Логи конкретного сервиса
docker-compose exec service bash  # Войти в контейнер
```

## 🗄️ Управление данными

### Тома (Volumes)
```bash
# Создание тома
docker volume create mydata

# Использование тома
docker run -v mydata:/app/data nginx

# Просмотр томов
docker volume ls
docker volume inspect mydata
```

### Bind Mounts
```bash
# Монтирование директории
docker run -v /host/path:/container/path nginx

# Монтирование файла
docker run -v /host/file:/container/file nginx
```

### Docker Networks
```bash
# Создание сети
docker network create mynetwork

# Использование сети
docker run --network mynetwork nginx

# Просмотр сетей
docker network ls
docker network inspect mynetwork
```

## 🔒 Безопасность Docker

### Принципы безопасности
- Используйте минимальные базовые образы
- Не запускайте контейнеры от root
- Регулярно обновляйте образы
- Сканируйте образы на уязвимости

### Безопасный Dockerfile
```dockerfile
FROM node:16-alpine

# Создать пользователя
RUN addgroup -g 1001 -S nodejs
RUN adduser -S nextjs -u 1001

# Установить зависимости
COPY package*.json ./
RUN npm ci --only=production

# Копировать код
COPY . .

# Передать права
RUN chown -R nextjs:nodejs /app
USER nextjs

EXPOSE 3000
CMD ["npm", "start"]
```

### Сканирование уязвимостей
```bash
# Docker Scout (встроенный)
docker scout cves nginx

# Trivy
trivy image nginx

# Snyk
snyk container test nginx
```

## 📊 Мониторинг Docker

### Основные команды
```bash
docker stats                    # Статистика в реальном времени
docker system df               # Использование диска
docker system prune            # Очистка неиспользуемых ресурсов
```

### Логирование
```bash
# Настройка драйвера логирования
docker run --log-driver json-file --log-opt max-size=10m nginx

# Просмотр логов
docker logs --tail 100 container_id
```

## 🔗 Связанные разделы

- [[02-Docker-Compose|Docker Compose]]
- [[03-Kubernetes|Kubernetes]]
- [[../02-Linux/01-Основы-Linux|Основы Linux]]
- [[../05-CI-CD/01-Jenkins|Jenkins]]

---

*Следующий раздел: [[02-Docker-Compose|Docker Compose]]* 