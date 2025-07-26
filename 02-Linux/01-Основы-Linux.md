# 🐧 Основы Linux

## 📖 Введение в Linux

Linux — это семейство операционных систем с открытым исходным кодом, основанных на ядре Linux. В DevOps Linux является основной платформой для развертывания и управления серверами.

### 🎯 Почему Linux для DevOps?

- **Открытый исходный код** — полный контроль над системой
- **Стабильность** — высокая надежность и производительность
- **Безопасность** — меньше уязвимостей по сравнению с Windows
- **Гибкость** — возможность настройки под любые требования
- **Экономичность** — отсутствие лицензионных отчислений

## 🖥️ Основные дистрибутивы

### Red Hat Family
- **Red Hat Enterprise Linux (RHEL)** — корпоративный дистрибутив
- **CentOS** — бесплатная версия RHEL
- **Fedora** — тестовая платформа для новых технологий

### Debian Family
- **Debian** — стабильный дистрибутив
- **Ubuntu** — популярный дистрибутив для разработки
- **Linux Mint** — пользовательский дистрибутив

### Arch Family
- **Arch Linux** — минималистичный дистрибутив
- **Manjaro** — дружественный Arch

## 💻 Основные команды

### Навигация по файловой системе
```bash
pwd                     # Показать текущий путь
ls                      # Список файлов
ls -la                 # Подробный список с скрытыми файлами
cd /path/to/directory  # Перейти в директорию
cd ~                   # Перейти в домашнюю директорию
cd ..                  # Перейти на уровень выше
```

### Работа с файлами
```bash
cp source destination   # Копировать файл
mv source destination   # Переместить/переименовать файл
rm filename            # Удалить файл
rm -rf directory      # Удалить директорию рекурсивно
mkdir directory       # Создать директорию
touch filename        # Создать пустой файл
```

### Просмотр файлов
```bash
cat filename          # Показать содержимое файла
less filename         # Просмотр файла постранично
head -n 10 filename  # Первые 10 строк
tail -n 10 filename  # Последние 10 строк
tail -f filename     # Следить за файлом в реальном времени
```

### Поиск файлов
```bash
find /path -name "*.txt"    # Найти файлы по имени
find /path -type f -mtime -7  # Файлы измененные за 7 дней
grep "pattern" filename     # Поиск текста в файле
grep -r "pattern" /path    # Рекурсивный поиск
```

## 🔧 Управление пакетами

### APT (Debian/Ubuntu)
```bash
sudo apt update              # Обновить список пакетов
sudo apt upgrade             # Обновить установленные пакеты
sudo apt install package     # Установить пакет
sudo apt remove package      # Удалить пакет
sudo apt search package      # Поиск пакета
```

### YUM/DNF (Red Hat/CentOS)
```bash
sudo yum update              # Обновить систему
sudo yum install package     # Установить пакет
sudo yum remove package      # Удалить пакет
sudo yum search package      # Поиск пакета
```

### Snap (Ubuntu)
```bash
sudo snap install package    # Установить snap пакет
sudo snap remove package     # Удалить snap пакет
snap list                    # Список установленных snap
```

## 👤 Управление пользователями

### Основные команды
```bash
sudo useradd username        # Создать пользователя
sudo userdel username        # Удалить пользователя
sudo passwd username         # Установить пароль
sudo usermod -aG group user # Добавить пользователя в группу
id username                 # Информация о пользователе
who                         # Кто в системе
w                           # Подробная информация о пользователях
```

### Права доступа
```bash
chmod 755 filename          # Изменить права доступа
chmod +x filename          # Добавить право на выполнение
chown user:group filename   # Изменить владельца
chgrp group filename        # Изменить группу
```

### Sudo
```bash
sudo visudo                 # Редактировать sudoers
sudo -l                     # Список разрешенных команд
sudo -u user command        # Выполнить команду от имени пользователя
```

## 🔍 Системная информация

### Информация о системе
```bash
uname -a                    # Информация о ядре
cat /etc/os-release         # Информация о дистрибутиве
lscpu                       # Информация о процессоре
free -h                     # Использование памяти
df -h                       # Использование диска
```

### Процессы
```bash
ps aux                      # Все процессы
ps aux | grep process       # Поиск процесса
top                         # Интерактивный мониторинг процессов
htop                        # Улучшенный top
kill PID                    # Завершить процесс
killall process_name        # Завершить все процессы с именем
```

### Сетевые соединения
```bash
ip addr                     # IP адреса
ip route                    # Таблица маршрутизации
netstat -tuln              # Сетевые соединения
ss -tuln                   # Современная замена netstat
ping hostname               # Проверка соединения
traceroute hostname         # Трассировка маршрута
```

## 🛠️ Системные службы

### Systemd
```bash
sudo systemctl start service    # Запустить службу
sudo systemctl stop service     # Остановить службу
sudo systemctl restart service  # Перезапустить службу
sudo systemctl enable service   # Включить автозапуск
sudo systemctl disable service  # Отключить автозапуск
sudo systemctl status service   # Статус службы
```

### Просмотр логов
```bash
sudo journalctl -u service      # Логи службы
sudo journalctl -f              # Следить за логами в реальном времени
sudo journalctl --since "1 hour ago"  # Логи за последний час
```

## 🔒 Безопасность

### Firewall (UFW)
```bash
sudo ufw enable               # Включить firewall
sudo ufw disable              # Отключить firewall
sudo ufw allow 22             # Разрешить порт 22
sudo ufw deny 80              # Запретить порт 80
sudo ufw status               # Статус firewall
```

### SSH
```bash
ssh user@hostname             # Подключение по SSH
ssh-keygen -t rsa             # Генерация SSH ключей
ssh-copy-id user@hostname     # Копирование публичного ключа
```

### Файл SSH конфигурации
```bash
# ~/.ssh/config
Host myserver
    HostName 192.168.1.100
    User myuser
    Port 2222
    IdentityFile ~/.ssh/id_rsa
```

## 📊 Мониторинг системы

### Системные ресурсы
```bash
vmstat 1                     # Статистика виртуальной памяти
iostat 1                     # Статистика ввода/вывода
iotop                        # Мониторинг дисковых операций
nethogs                      # Мониторинг сетевого трафика
```

### Дисковое пространство
```bash
du -sh /path                 # Размер директории
du -h | sort -hr             # Сортировка по размеру
ncdu                         # Интерактивный анализ диска
```

## 🐳 Docker в Linux

### Установка Docker
```bash
# Ubuntu/Debian
sudo apt update
sudo apt install docker.io
sudo systemctl enable docker
sudo usermod -aG docker $USER

# CentOS/RHEL
sudo yum install docker
sudo systemctl enable docker
sudo usermod -aG docker $USER
```

### Основные команды Docker
```bash
docker ps                    # Список контейнеров
docker images                # Список образов
docker run image            # Запустить контейнер
docker stop container       # Остановить контейнер
docker logs container       # Логи контейнера
```

## 🔗 Связанные разделы

- [[02-Управление-процессами|Управление процессами]]
- [[03-Сетевые-настройки|Сетевые настройки]]
- [[04-Безопасность|Безопасность]]
- [[../03-Контейнеризация/01-Docker-основы|Docker основы]]

---

*Следующий раздел: [[02-Управление-процессами|Управление процессами]]* 