# 🐳 Docker Compose

## 📖 Что такое Docker Compose?

**Docker Compose** — это инструмент для определения и управления многоконтейнерными Docker-приложениями с помощью YAML-файлов.

### Преимущества
- Простота запуска сложных приложений
- Одна команда для старта/остановки всех сервисов
- Легко масштабировать и обновлять
- Поддержка переменных окружения

## 🚀 Установка
```bash
sudo apt update
sudo apt install docker-compose
# или через pip
pip install docker-compose
```

## 🛠️ Основные команды
```bash
docker-compose up -d         # Запуск всех сервисов
docker-compose down          # Остановка и удаление
docker-compose ps            # Список сервисов
docker-compose logs -f       # Логи
docker-compose exec web bash # Войти в контейнер
```

## 📦 Пример docker-compose.yml
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

volumes:
  db_data:
```

## 📝 Полезные опции
- **depends_on** — порядок запуска
- **volumes** — постоянные данные
- **networks** — отдельные сети
- **build** — сборка из Dockerfile
- **env_file** — переменные окружения

## 🔒 Безопасность
- Не храните секреты в открытом виде
- Используйте .env файлы
- Ограничивайте права контейнеров

## 🔗 Связанные разделы
- [[01-Docker-основы|Docker основы]]
- [[03-Kubernetes|Kubernetes]]
- [[../05-CI-CD/01-Jenkins|Jenkins]]

---

*Следующий раздел: [[03-Kubernetes|Kubernetes]]* 