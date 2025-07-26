# 🔄 Jenkins

## 📖 Что такое Jenkins?

**Jenkins** — это open-source сервер автоматизации, который помогает автоматизировать процессы сборки, тестирования и развертывания программного обеспечения.

### 🎯 Преимущества Jenkins

- **Открытый исходный код** — бесплатный и настраиваемый
- **Большая экосистема** — множество плагинов
- **Поддержка множества технологий** — Java, Python, Node.js, Docker и др.
- **Распределенная архитектура** — возможность масштабирования
- **Интеграция с Git** — автоматические сборки при коммитах

## 🚀 Установка Jenkins

### Требования
- Java 8 или выше
- Минимум 256MB свободной памяти
- 1GB свободного места на диске

### Установка на Ubuntu/Debian
```bash
# Добавить репозиторий
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'

# Установить Jenkins
sudo apt update
sudo apt install jenkins

# Запустить и включить Jenkins
sudo systemctl start jenkins
sudo systemctl enable jenkins

# Проверить статус
sudo systemctl status jenkins
```

### Установка на CentOS/RHEL
```bash
# Добавить репозиторий
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key

# Установить Jenkins
sudo yum install jenkins

# Запустить и включить Jenkins
sudo systemctl start jenkins
sudo systemctl enable jenkins
```

### Установка через Docker
```bash
# Запустить Jenkins в контейнере
docker run -d \
  --name jenkins \
  -p 8080:8080 \
  -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home \
  jenkins/jenkins:lts

# Получить пароль администратора
docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```

## 🔧 Первоначальная настройка

### Первый запуск
1. Откройте браузер и перейдите на `http://localhost:8080`
2. Введите пароль администратора (находится в `/var/lib/jenkins/secrets/initialAdminPassword`)
3. Выберите "Install suggested plugins"
4. Создайте администратора
5. Настройте URL Jenkins

### Установка плагинов
```bash
# Основные плагины для DevOps
- Git plugin
- Docker plugin
- Pipeline plugin
- Blue Ocean plugin
- Credentials plugin
- SSH plugin
- Email Extension plugin
```

## 📝 Создание первого Job

### Freestyle Project
1. **Создание проекта**
   - Нажмите "New Item"
   - Выберите "Freestyle project"
   - Введите имя проекта

2. **Настройка Git**
   ```bash
   # Repository URL
   https://github.com/username/repository.git
   
   # Credentials
   Username: your-username
   Password: your-token
   ```

3. **Настройка сборки**
   ```bash
   # Build triggers
   Poll SCM: H/5 * * * *  # Каждые 5 минут
   
   # Build steps
   Execute shell:
   mvn clean compile
   mvn test
   mvn package
   ```

### Pipeline Project
```groovy
pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Build') {
            steps {
                sh 'mvn clean compile'
            }
        }
        
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        
        stage('Package') {
            steps {
                sh 'mvn package'
            }
        }
        
        stage('Deploy') {
            steps {
                sh 'docker build -t myapp .'
                sh 'docker run -d -p 8080:8080 myapp'
            }
        }
    }
    
    post {
        always {
            cleanWs()
        }
        success {
            echo 'Build successful!'
        }
        failure {
            echo 'Build failed!'
        }
    }
}
```

## 🔄 Declarative Pipeline

### Базовый pipeline
```groovy
pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = 'myapp'
        DOCKER_TAG = 'latest'
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Build') {
            steps {
                sh 'mvn clean compile'
            }
        }
        
        stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    publishTestResults testResultsPattern: '**/test-results/*.xml'
                }
            }
        }
        
        stage('SonarQube') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh 'mvn sonar:sonar'
                }
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}:${DOCKER_TAG}")
                }
            }
        }
        
        stage('Deploy to Staging') {
            when {
                branch 'develop'
            }
            steps {
                sh 'docker run -d -p 8080:8080 myapp:latest'
            }
        }
        
        stage('Deploy to Production') {
            when {
                branch 'main'
            }
            steps {
                input 'Deploy to production?'
                sh 'docker run -d -p 80:8080 myapp:latest'
            }
        }
    }
    
    post {
        always {
            cleanWs()
        }
        success {
            emailext (
                subject: "Build Successful: ${env.JOB_NAME}",
                body: "Build ${env.BUILD_NUMBER} was successful!",
                to: 'team@company.com'
            )
        }
        failure {
            emailext (
                subject: "Build Failed: ${env.JOB_NAME}",
                body: "Build ${env.BUILD_NUMBER} failed!",
                to: 'team@company.com'
            )
        }
    }
}
```

## 🐳 Jenkins с Docker

### Docker Agent
```groovy
pipeline {
    agent {
        docker {
            image 'maven:3.8.1-openjdk-11'
            args '-v /var/run/docker.sock:/var/run/docker.sock'
        }
    }
    
    stages {
        stage('Build') {
            steps {
                sh 'mvn clean compile'
            }
        }
        
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        
        stage('Package') {
            steps {
                sh 'mvn package'
            }
        }
    }
}
```

### Multi-stage Docker
```groovy
pipeline {
    agent any
    
    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("myapp:${env.BUILD_NUMBER}")
                }
            }
        }
        
        stage('Test Docker Image') {
            steps {
                script {
                    docker.image("myapp:${env.BUILD_NUMBER}").inside {
                        sh 'npm test'
                    }
                }
            }
        }
        
        stage('Push to Registry') {
            steps {
                script {
                    docker.withRegistry('https://registry.company.com', 'registry-credentials') {
                        docker.image("myapp:${env.BUILD_NUMBER}").push()
                        docker.image("myapp:${env.BUILD_NUMBER}").push('latest')
                    }
                }
            }
        }
    }
}
```

## 🔐 Управление секретами

### Credentials Plugin
```groovy
pipeline {
    agent any
    
    environment {
        DOCKER_REGISTRY = credentials('docker-registry')
        SSH_KEY = credentials('ssh-private-key')
    }
    
    stages {
        stage('Deploy') {
            steps {
                sshagent(['ssh-private-key']) {
                    sh '''
                        ssh user@server "docker pull myapp:latest"
                        ssh user@server "docker stop myapp || true"
                        ssh user@server "docker run -d --name myapp -p 80:8080 myapp:latest"
                    '''
                }
            }
        }
    }
}
```

### HashiCorp Vault
```groovy
pipeline {
    agent any
    
    stages {
        stage('Get Secrets') {
            steps {
                script {
                    def secrets = vault.getSecret('secret/myapp')
                    env.DB_PASSWORD = secrets.data.password
                    env.API_KEY = secrets.data.api_key
                }
            }
        }
        
        stage('Deploy') {
            steps {
                sh '''
                    echo "Using database password: ${DB_PASSWORD}"
                    echo "Using API key: ${API_KEY}"
                '''
            }
        }
    }
}
```

## 📊 Мониторинг и отчеты

### Test Results
```groovy
pipeline {
    agent any
    
    stages {
        stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    publishTestResults testResultsPattern: '**/test-results/*.xml'
                    publishCoverage adapters: [jacocoAdapter('**/target/site/jacoco/jacoco.xml')]
                }
            }
        }
    }
}
```

### Code Coverage
```groovy
pipeline {
    agent any
    
    stages {
        stage('Test with Coverage') {
            steps {
                sh 'mvn clean test jacoco:report'
            }
            post {
                always {
                    publishCoverage adapters: [jacocoAdapter('**/target/site/jacoco/jacoco.xml')]
                }
            }
        }
    }
}
```

## 🔄 Webhooks и интеграции

### GitHub Webhook
```groovy
pipeline {
    agent any
    
    triggers {
        GenericTrigger(
            genericVariables: [
                [key: 'ref', value: '$.ref'],
                [key: 'repository', value: '$.repository.name']
            ],
            causeString: 'Triggered by GitHub',
            token: 'your-token'
        )
    }
    
    stages {
        stage('Build') {
            steps {
                echo "Building ${env.repository} from ${env.ref}"
                sh 'mvn clean compile'
            }
        }
    }
}
```

### Slack Integration
```groovy
pipeline {
    agent any
    
    stages {
        stage('Build') {
            steps {
                sh 'mvn clean compile'
            }
        }
    }
    
    post {
        success {
            slackSend(
                channel: '#devops',
                color: 'good',
                message: "Build ${env.JOB_NAME} #${env.BUILD_NUMBER} successful!"
            )
        }
        failure {
            slackSend(
                channel: '#devops',
                color: 'danger',
                message: "Build ${env.JOB_NAME} #${env.BUILD_NUMBER} failed!"
            )
        }
    }
}
```

## 🛠️ Управление Jenkins

### Backup и Restore
```bash
# Backup Jenkins
tar -czf jenkins-backup.tar.gz /var/lib/jenkins

# Restore Jenkins
tar -xzf jenkins-backup.tar.gz -C /
```

### Управление плагинами
```bash
# Установка плагина через CLI
jenkins-cli install-plugin plugin-name

# Обновление плагинов
jenkins-cli upgrade-plugins
```

### Мониторинг Jenkins
```bash
# Проверка статуса
curl -s http://localhost:8080/api/json | jq '.'

# Статистика сборок
curl -s http://localhost:8080/job/job-name/api/json | jq '.'
```

## 🔗 Связанные разделы

- [[02-GitLab-CI|GitLab CI]]
- [[03-GitHub-Actions|GitHub Actions]]
- [[04-ArgoCD|ArgoCD]]
- [[../03-Контейнеризация/01-Docker-основы|Docker основы]]

---

*Следующий раздел: [[02-GitLab-CI|GitLab CI]]* 