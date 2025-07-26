# ☸️ Kubernetes

## 📖 Что такое Kubernetes?

**Kubernetes** (k8s) — это open-source система оркестрации контейнеров для автоматизации развертывания, масштабирования и управления приложениями в контейнерах.

### 🎯 Преимущества Kubernetes
- **Автоматизация** — развертывание, масштабирование, обновление
- **Высокая доступность** — самовосстановление, балансировка нагрузки
- **Масштабируемость** — горизонтальное и вертикальное масштабирование
- **Гибкость** — поддержка разных облаков и on-premise
- **Расширяемость** — плагины, CRD, операторы

## 🚀 Установка Kubernetes

### Minikube (локально)
```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
minikube start
```

### Kubeadm (кластер)
```bash
# На всех нодах
sudo apt-get update && sudo apt-get install -y apt-transport-https curl
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl

# На master-ноде
sudo kubeadm init --pod-network-cidr=10.244.0.0/16
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

# Установка сетевого плагина (Flannel)
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```

### Managed Kubernetes
- **AWS EKS**
- **Google GKE**
- **Azure AKS**

## ⚙️ Основные объекты Kubernetes
- **Pod** — минимальная единица развертывания
- **Deployment** — управление версиями и масштабированием
- **Service** — доступ к Pod'ам (ClusterIP, NodePort, LoadBalancer)
- **ConfigMap/Secret** — хранение конфигураций и секретов
- **Ingress** — маршрутизация HTTP(S) трафика
- **Namespace** — изоляция ресурсов
- **Volume/PersistentVolume** — хранение данных
- **StatefulSet** — управление stateful-приложениями
- **DaemonSet** — запуск на каждой ноде
- **Job/CronJob** — фоновые и периодические задачи

## 🛠️ Основные команды kubectl
```bash
kubectl get nodes
kubectl get pods -A
kubectl describe pod pod_name
kubectl logs pod_name
kubectl exec -it pod_name -- bash
kubectl apply -f manifest.yaml
kubectl delete -f manifest.yaml
kubectl rollout status deployment deployment_name
kubectl scale deployment deployment_name --replicas=3
kubectl port-forward service/service_name 8080:80
kubectl get events --sort-by='.lastTimestamp'
```

## 📦 Примеры манифестов

### Deployment
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.25
        ports:
        - containerPort: 80
```

### Service
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: LoadBalancer
```

### Ingress
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: example-ingress
spec:
  rules:
  - host: example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: nginx-service
            port:
              number: 80
```

### ConfigMap
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
  namespace: default
  labels:
    app: myapp
    environment: production
data:
  APP_ENV: production
  LOG_LEVEL: info
```

### Secret
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-secret
type: Opaque
data:
  username: YWRtaW4=
  password: cGFzc3dvcmQ=
```

## 🔒 Безопасность Kubernetes
- RBAC (роли и права)
- Network Policies
- Secrets Management
- Admission Controllers
- Pod Security Policies
- Аудит логов

## 📊 Мониторинг и логирование
- Prometheus + Grafana (метрики)
- ELK Stack (логи)
- Loki (логи)
- Jaeger/Zipkin (трейсинг)

## 🛡️ Best Practices
- Используйте namespaces для изоляции
- Минимизируйте права сервис-аккаунтов
- Используйте readiness/liveness probes
- Храните секреты в Secret, а не в ConfigMap
- Используйте resource requests/limits
- Регулярно обновляйте кластеры и плагины

## 🛠️ Инструменты
- **Helm** — менеджер пакетов для Kubernetes
- **Kustomize** — управление конфигурациями
- **kubectx/kubens** — быстрое переключение контекстов и пространств
- **Lens** — GUI для управления кластерами
- **Stern** — просмотр логов
- **K9s** — терминальный UI

## 🔗 Связанные разделы
- [[01-Docker-основы|Docker основы]]
- [[../06-Мониторинг/01-Prometheus|Prometheus]]
- [[../06-Мониторинг/02-Grafana|Grafana]]
- [[../06-Мониторинг/03-ELK-Stack|ELK Stack]]

---

*Следующий раздел: [[../06-Мониторинг/01-Prometheus|Prometheus]]* 