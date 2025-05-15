# Домашнее задание к занятию "6.4. Docker. Часть 2 - Сабитов Юрий"

### Задание 1

Напишите ответ в свободной форме, не больше одного абзаца текста.
Установите Docker Compose и опишите, для чего он нужен и как может улучшить лично вашу жизнь.


Docker Compose — это инструментальное средство, входящее в состав Docker. Оно разработано для помощи в определении и совместном использовании многоконтейнерных приложений. С помощью Docker Compose можно создать файл .yml для определения служб и с помощью одной команды запустить и остановить всё, что нужно при развертывании многоконтейнерных приложений.`


---

### Задание 2

Выполните действия и приложите текст конфига на этом этапе.

Создайте файл docker-compose.yml и внесите туда первичные настройки:

- version;
- services;
- volumes;
- networks.

При выполнении задания используйте подсеть 10.5.0.0/16. Ваша подсеть должна называться: <ваши фамилия и инициалы>-my-netology-hw. Все приложения из последующих заданий должны находиться в этой конфигурации.

```yaml
version: '3'

services:

volumes:

networks:
  SabitovYM-my-netology-hw:
    driver: bridge
    ipam:
      config:
        - subnet: 10.5.0.0/16
          gateway: 10.5.0.1

```
---

### Задание 3

Выполните действия:

Создайте конфигурацию docker-compose для Prometheus с именем контейнера <ваши фамилия и инициалы>-netology-prometheus.
Добавьте необходимые тома с данными и конфигурацией (конфигурация лежит в репозитории в директории 6-04/prometheus ).
Обеспечьте внешний доступ к порту 9090 c докер-сервера.

``` yaml
services:
  prometheus:
    image: prom/prometheus
    container_name: SabitovYM-netology-prometheus
    command: --web.enable-lifecycle --config.file=/etc/prometheus/prometheus.yml
    ports:
      - 9090:9090
    volumes:
      - ./:/etc/prometheus
      - prometheus-data:/prometheus
    networks:
      - SabitovYM-netology-hw
    restart: always

volumes:
  prometheus-data:

networks:
  SabitovYM-netology-hw:
    driver: bridge
    ipam:
      config:
        - subnet: 10.5.0.0/16
          gateway: 10.5.0.1

```
![Скриншот к Заданию 3](https://github.com/user-attachments/assets/a5827ee8-0c1f-4072-9b15-8cac09486362)

---

### Задание 4

Выполните действия:

Создайте конфигурацию docker-compose для Pushgateway с именем контейнера <ваши фамилия и инициалы>-netology-pushgateway.
Обеспечьте внешний доступ к порту 9091 c докер-сервера.

```yaml

  pushgateway:
    image: prom/pushgateway
    container_name: SabitovYM-netology-pushgateway
    ports:
      - 9091:9091
    networks:
      - SabitovYM-netology-hw
    depends_on:
      - prometheus
    restart: unless-stopped


```
![Скриншот к Заданию 4](https://github.com/user-attachments/assets/805782d4-51e7-44f5-a6fc-91f2c492a1f9)

---

### Задание 5
Выполните действия:

Создайте конфигурацию docker-compose для Grafana с именем контейнера <ваши фамилия и инициалы>-netology-grafana.
Добавьте необходимые тома с данными и конфигурацией (конфигурация лежит в репозитории в директории 6-04/grafana.
Добавьте переменную окружения с путем до файла с кастомными настройками (должен быть в томе), в самом файле пропишите логин=<ваши фамилия и инициалы> пароль=netology.
Обеспечьте внешний доступ к порту 3000 c порта 80 докер-сервера.

```yaml
  grafana:
    image: grafana/grafana
    container_name: SabitovYM-netology-grafana
    environment:
     GF_PATHS_CONFIG: /etc/grafana/custom.ini
    ports:
      - 80:3000
    volumes:
      - ./grafana:/etc/grafana
      - grafana-data:/var/lib/grafana
    networks:
      - SabitovYM-netology-hw
    depends_on:
      - pushgateway
    restart: unless-stopped

```

![Скриншот к Заданию 5](https://github.com/user-attachments/assets/815ed922-9923-40f4-b365-0c19cdf26920)

---

### Задание 6
Выполните действия.

Настройте поочередность запуска контейнеров.
Настройте режимы перезапуска для контейнеров.
Настройте использование контейнерами одной сети.
Запустите сценарий в detached режиме.

```yaml                                  
services:
  prometheus:
    container_name: SabitovYM-netology-prometheus
    networks:
      - SabitovYM-netology-hw
    restart: always
  pushgateway:
    container_name: SabitovYM-netology-pushgateway
    networks:
      - SabitovYM-netology-hw
    depends_on:
      - prometheus
    restart: unless-stopped
  grafana:
    container_name: SabitovYM-netology-grafana
    networks:
      - SabitovYM-netology-hw
    depends_on:
      - pushgateway
    restart: unless-stopped
```
![Скриншот к Заданию 6](https://github.com/user-attachments/assets/174bf802-b7a5-4448-98be-adcb233d738c)

---

### Задание 7
Выполните действия.

Выполните запрос в Pushgateway для помещения метрики <ваши фамилия и инициалы> со значением 5 в Prometheus: echo "<ваши фамилия и инициалы> 5" | curl --data-binary @- http://localhost:9091/metrics/job/netology.
Залогиньтесь в Grafana с помощью логина и пароля из предыдущего задания.
Cоздайте Data Source Prometheus (Home -> Connections -> Data sources -> Add data source -> Prometheus -> указать "Prometheus server URL = http://prometheus:9090" -> Save & Test).
Создайте график на основе добавленной в пункте 5 метрики (Build a dashboard -> Add visualization -> Prometheus -> Select metric -> Metric explorer -> <ваши фамилия и инициалы -> Apply.
В качестве решения приложите:

docker-compose.yml целиком;
скриншот команды docker ps после запуске docker-compose.yml;
скриншот графика, постоенного на основе вашей метрики.

![скриншот команды docker ps после запуске docker-compose.yml](https://github.com/user-attachments/assets/b7b2cb58-1c3f-44d5-9652-f17cd0c61f60)
# скриншот команды docker ps после запуске docker-compose.yml
