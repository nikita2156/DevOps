# 🔧 Puppet

## 📖 Что такое Puppet?

**Puppet** — это инструмент для управления конфигурациями и автоматизации инфраструктуры, использующий декларативный язык описания состояния системы.

## 🛠️ Основные понятия
- **Manifest** — файл с описанием конфигурации (.pp)
- **Module** — переиспользуемый набор manifest'ов
- **Class** — логическая группа настроек
- **Resource** — управляемый объект (file, package, service)
- **Node** — управляемый сервер
- **Puppet Master/Agent** — архитектура client-server

## 🚀 Быстрый старт

### Установка
```bash
# На master
sudo apt install puppetserver
# На агенте
sudo apt install puppet-agent
```

### Пример manifest
```puppet
class nginx {
  package { 'nginx':
    ensure => installed,
  }
  service { 'nginx':
    ensure => running,
    enable => true,
  }
}

node 'web1.example.com' {
  include nginx
}
```

### Применение manifest
```bash
puppet apply nginx.pp
```

## 📝 Полезные команды
```bash
puppet agent --test
puppet resource service nginx
puppet module install puppetlabs-nginx
```

## 🔒 Безопасность
- Используйте SSL для связи master-agent
- Разделяйте окружения (production, staging)
- Минимизируйте права Puppet

## 🛠️ Best practices
- Используйте модули с Puppet Forge
- Разделяйте код по ролям и профилям
- Документируйте manifest'ы
- Используйте version control для кода

## 🔧 Интеграции
- AWS, Azure, GCP (cloud modules)
- Docker, Kubernetes
- CI/CD (Jenkins, GitLab CI)

## 📚 Полезные ресурсы
- [Документация Puppet](https://puppet.com/docs/)
- [Puppet Forge](https://forge.puppet.com/)

## 🔗 Связанные разделы
- [[01-Terraform|Terraform]]
- [[02-Ansible|Ansible]]
- [[../04-Облачные-платформы/01-AWS|AWS]]
- [[../05-CI-CD/01-Jenkins|Jenkins]]

---

*Следующий раздел: [[../08-Безопасность/01-Основы-безопасности|Основы безопасности]]* 