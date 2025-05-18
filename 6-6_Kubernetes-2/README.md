### Задание 1
#### Выполните действия:

1. Создайте свой кластер с помощью kubeadm.
2. Установите любой понравившийся CNI плагин.
3. Добейтесь стабильной работы кластера.

![Скриншот к заданию 1](https://github.com/user-attachments/assets/ef505f51-5e30-4164-aa51-2800f45ef1a2)

---

### Задание 2
Есть файл с деплоем:
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
        image: bitnami/redis
        env:
         - name: REDIS_PASSWORD
           value: password123
        ports:
        - containerPort: 6379
```
#### Выполните действия:

1. Создайте Helm Charts.
2. Добавьте в него сервис.
3. Вынесите все нужные, на ваш взгляд, параметры в values.yaml.
4. Запустите чарт в своём кластере и добейтесь его стабильной работы.
---