### Задание 1
#### Выполните действия:

1. Запустите Kubernetes локально, используя k3s или minikube на свой выбор.
2. Добейтесь стабильной работы всех системных контейнеров.

![Скриншот к заданию 1](https://github.com/user-attachments/assets/9ac3e56e-0190-48f5-a136-cd91254ce779)

---

### Задание 2
Есть файл с деплоем:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis
spec:
  selector:
    matchLabels:
      app: redis
  replicas: 1
  template:
    metadata:
      labels:
        app: redis
    spec:
      containers:
      - name: master
        image: bitnami/redis
        env:
         - name: REDIS_PASSWORD
           value: password123
        ports:
        - containerPort: 6379
```
---
#### Выполните действия:

1. Измените файл с учётом условий:
   - redis должен запускаться без пароля;
   - создайте Service, который будет направлять трафик на этот Deployment;
   - версия образа redis должна быть зафиксирована на 6.0.13.
2. Запустите Deployment в своём кластере и добейтесь его стабильной работы.

```yaml
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis
spec:
  selector:
    matchLabels:
      app: redis
  replicas: 1
  template:
    metadata:
      labels:
        app: redis
    spec:
      containers:
      - name: master
        image: bitnami/redis:6.0.13
        env:
         - name: ALLOW_EMPTY_PASSWORD
           value: "yes"
        ports:
        - containerPort: 6379
```
```yaml
apiVersion: v1
kind: Service
metadata:
  name: redis-service
spec:
  selector:
    app: redis
  ports:
    - protocol: TCP
      port: 6379
      targetPort: 6379
```
![Скриншот к заданию 2](https://github.com/user-attachments/assets/e00c3145-47e2-4934-a782-2fd58ac9ddc2)

---

### Задание 3
#### Выполните действия:

1. Напишите команды kubectl для контейнера из предыдущего задания:
   - выполнения команды ps aux внутри контейнера;
   - просмотра логов контейнера за последние 5 минут;
   - удаления контейнера;
   - проброса порта локальной машины в контейнер для отладки.
2. В качестве решения пришлите получившиеся команды.
![Скриншот к заданию 3](https://github.com/user-attachments/assets/fd197db3-d724-4ab6-ae5d-89c3653d7773)

```yaml
kubectl exec redis-54457d549d-2qqsj -- ps aux
kubectl logs --since=25m redis-54457d549d-2qqsj
kubectl delete -f redis.yaml && kubectl delete -f redis-service.yaml
kubectl port-forward redis-54457d549d-2qqsj 1055:6739
```
---
### Задание 4*
Есть конфигурация nginx:
```
location / {
    add_header Content-Type text/plain;
    return 200 'Hello from k8s';
}
```
#### Выполните действия:

1. Напишите yaml-файлы для развёртки nginx, в которых будут присутствовать:
   - ConfigMap с конфигом nginx;
   - Deployment, который бы подключал этот configmap;
   - Ingress, который будет направлять запросы по префиксу /test на наш сервис.
2. В качестве решения пришлите получившийся файл.
```
```
---
