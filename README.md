# 

![image](https://github.com/piroxide/crazyton/blob/main/405283539-83cfa9c5-2a02-41db-bbd7-559ab54600e5.png)

## Быстрый старт

Устанавливает утилиту wget на систему

     sudo yum install wget

![image](https://github.com/piroxide/crazyton/blob/main/2.png)


Скачиваем файл репозитория

     sudo wget -P /etc/yum.repos.d/ https://download.docker.com/linux/centos/docker-ce.repo

![image](https://github.com/piroxide/crazyton/blob/main/3.png)


Устанавливаем docker

     sudo yum install docker-ce docker-ce-cli containerd.io

![image](https://github.com/piroxide/crazyton/blob/main/4.png)

Запускаем его и разрешаем автозапуск

     sudo systemctl enable docker --now

![image](https://github.com/piroxide/crazyton/blob/main/5.png)



# Установка compose

Для начала нужно убедиться в наличии пакета curl

     sudo yum install curl

![image](https://github.com/piroxide/crazyton/blob/main/6.png)



Объявление переменной COMVER, полученной в результате curl запроса, хранящей в себе номер последней
версии Docker Compose

     COMVER=$(curl -s https://api.github.com/repos/docker/compose/releases/latest | grep 'tag_name' | cut -d\" -f4)

Теперь скачиваем скрипт docker-compose последней версии, используя объявленную ранее переменную и помещаем его в каталог /usr/bin

     sudo curl -L "https://github.com/docker/compose/releases/download/$COMVER/docker-compose-$(uname -s)-$(uname -m)" -o /usr/bin/docker-compose

![image](https://github.com/piroxide/crazyton/blob/main/7.png)



Предоставление прав на выполнение файла docker-compose

     sudo chmod +x /usr/bin/docker-compose

Проверка установленной версии Docker Compose

     sudo docker-compose --version

![image](https://github.com/piroxide/crazyton/blob/main/8.png)


# Делаем grafana

Установка git

     sudo yum install git

![image](https://github.com/piroxide/crazyton/blob/main/9.png)



Этот код скачивает содержимое репозитория skl256/grafana_stack_for_docker

     sudo git clone https://github.com/skl256/grafana_stack_for_docker.git

![image](https://github.com/piroxide/crazyton/blob/main/10.png)



Заходит в папку - cd

     cd grafana_stack_for_docker

cd .. - возвращает в папку выше


(После этого можно вставлять готовый docker-compose)

Cоздаем папки двумя разными способами

     sudo mkdir -p /mnt/common_volume/swarm/grafana/config

     sudo mkdir -p /mnt/common_volume/grafana/{grafana-config,grafana-data,prometheus-data,loki-data,promtail-data}

Выдаем права

     sudo chown -R $(id -u):$(id -g) {/mnt/common_volume/swarm/grafana/config,/mnt/common_volume/grafana}

Создаем файл

     sudo touch /mnt/common_volume/grafana/grafana-config/grafana.ini

Копирование файлов

     sudo cp config/* /mnt/common_volume/swarm/grafana/config/

Переименовывание файла

     sudo mv grafana.yaml docker-compose.yaml

![image](https://github.com/piroxide/crazyton/blob/main/11.png)


Собрать докер (нужно запускать из папки где docker-compose.yaml)

     sudo docker compose up -d

Опустить докер - sudo docker compose stop

![image](https://github.com/piroxide/crazyton/blob/main/12.png)




# Начинаем чистку файлов

Команда открывает файл docker-compose.yaml в текстовом редакторе vi с правами суперпользователя

     sudo vi docker-compose.yaml

Что-бы что-то изменить в тесковом редакторе нужно нажать insert на клавиатуре

Затем в docker-compose нужно вставить node-exporter и удалить ненужные файлы (но у нас уже вставлен готовый докер)

![image](https://github.com/user-attachments/assets/14df36d8-22eb-42ea-b544-b1269c0a0393)

выйти не сохраняясь из vim - esc -> :q!

выйти сохраняясь из vim - esc -> :wq!

Заходим в другую папку 

     sudo cd /mnt/common_volume/swarm/grafana/config

Открываем файл prometheus.yaml в текстовом редакторе vi с правами суперпользователя

     sudo vi prometheus.yaml

![image](https://github.com/piroxide/crazyton/blob/main/13.png)



Далее нужно исправить targets: на exporter:9100

![image](https://github.com/user-attachments/assets/d6dc7b39-175e-44e1-8fe3-e244cd85c825)

* Для docker compose
  
```
git clone https://github.com/skl256/grafana_stack_for_docker.git && \ нажимаем y
cd grafana_stack_for_docker && \
sudo mkdir -p /mnt/common_volume/swarm/grafana/config && \
sudo mkdir -p /mnt/common_volume/grafana/{grafana-config,grafana-data,prometheus-data,loki-data,promtail-data} && \
sudo chown -R $(id -u):$(id -g) {/mnt/common_volume/swarm/grafana/config,/mnt/common_volume/grafana} && \
touch /mnt/common_volume/grafana/grafana-config/grafana.ini && \
cp config/* /mnt/common_volume/swarm/grafana/config/ && \
mv grafana.yaml docker-compose.yaml && \

```

 * Устанавливаем последнею версию и утилитю docker-compose

```
sudo yum install curl && \
COMVER=$(curl -s https://api.github.com/repos/docker/compose/releases/latest | grep 'tag_name' | cut -d" -f4) && \
sudo curl -L "https://github.com/docker/compose/releases/download/$COMVER/docker-compose-$(uname -s)-$(uname -m)" -o /usr/bin/docker-compose && \
sudo chmod +x /usr/bin/docker-compose && \
docker-compose --version && \
```

* Установка Docker

```
sudo wget -P /etc/yum.repos.d/ https://download.docker.com/linux/centos/docker-ce.repo
sudo yum install docker-ce docker-ce
sudo systemctl enable docker --now
docker compose up -d
```
              
## Конфигурация сервисов
### Prometheus
* После Установки докера пишем ` sudo vi docker-compose.yaml ` (Обязательно что бы был расширения.yaml)Нас перекидывает на текстовый редактор:
  * Нас перекинет в текстовый редактор
   * Что-бы что-то изменить в тесковом редакторе нажмите `insert` на клавиатуре
   * Что бы сохранить что-то в этом документе нажимаем Esc пишем  `:wq!`
 В этом текставом редакторе мы должны поставить node-exporter после services

```
  node-exporter:
    image: prom/node-exporter
    volumes:
      - /proc:/host/proc:ro
      - /sys:/host/sys:ro
      - /:/rootfs:ro
    container_name: exporter < запомнить название name
    hostname: exporter
    command:
      - --path.procfs=/host/proc
      - --path.sysfs=/host/sys
      - --collector.filesystem.ignored-mount-points
      - ^/(sys|proc|dev|host|etc|rootfs/var/lib/docker/containers|rootfs/var/lib/docker/overlay2|rootfs/run/docker/netns|rootfs/var/lib/docker/aufs)($$|/)
    ports:
      - 9100:9100
    restart: unless-stopped
    environment:
      TZ: "Europe/Moscow"
    networks:
      - default
```
Далее  
```
cd
cd /mnt/common_volume/swarm/grafana/config 
sudo vi prometheus.yaml 
```
* В этом файле нужно изменить первый ip Адрес на тот, который мы должны были запомнить 
  * Вставляем в первый targets 
  * После двоеточие цифры оставляем

### Grafana
* переходим на сайт `localhost:3000`
  
* User & Password GRAFANA: admin
  * Код графаны: 3000
  * Код прометеуса: http://prometheus:9090

* в меню выбираем вкладку Dashboards и создаем Dashboard
  * ждем кнопку +Add visualization, а после "Configure a new data source"
  * выбираем Prometheus

* Connection
  * `http://prometheus:9090`

* Authentication
  * Basic authentication
    * User: `admin`
    * Password: `admin`
   * Нажимаем на Save & test и должно показывать зелёную галочку

* в меню выбираем вкладку Dashboards и создаем Dashboard
  * ждем кнопку "Import dashboard"
  * `Find and import dashboards for common applications at grafana.com/dashboards: 1860`  //ждем кнопку Load
  * Select Prometheus ждем кнопку "Import"
  
> [!NOTE]
> Всё должно выглядить как на скриншоте `https://github.com/AndreyFed/Docker/blob/main/grafana_stack_for_docker/Screenshot_Prometheus.png`

Если не работает то переходим в 

```
cd grafana_stack_for_docker
sudo docker compose stop
sudo docker compose up -d
```
То есть перезапускаем докер

### VicroriaMetrics

* Для начала изменим docker-compose.yaml:
```
cd grafana_stack_for_docker
sudo vi docker-compose.yaml
```
После чего в в самом текстовом редакторе после `prometheus` вставляем
```
  vmagent:
    container_name: vmagent
    image: victoriametrics/vmagent:v1.105.0
    depends_on:
      - "victoriametrics"
    ports:
      - 8429:8429
    volumes:
      - vmagentdata:/vmagentdata
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
    command:
      - "--promscrape.config=/etc/prometheus/prometheus.yml"
      - "--remoteWrite.url=http://victoriametrics:8428/api/v1/write"
    restart: always
  # VictoriaMetrics instance, a single process responsible for
  # storing metrics and serve read requests.
  victoriametrics:
    container_name: victoriametrics
    image: victoriametrics/victoria-metrics:v1.105.0
    ports:
      - 8428:8428
      - 8089:8089
      - 8089:8089/udp
      - 2003:2003
      - 2003:2003/udp
      - 4242:4242
    volumes:
      - vmdata:/storage
    command:
      - "--storageDataPath=/storage"
      - "--graphiteListenAddr=:2003"
      - "--opentsdbListenAddr=:4242"
      - "--httpListenAddr=:8428"
      - "--influxListenAddr=:8089"
      - "--vmalert.proxyURL=http://vmalert:8880"
    restart: always
```
Сохраняем и закрываем 

* Захом в connection
  * там где мы писали `http:prometheus:9090` пишем  `http:victoriametrics:9090` И заменяем имя из "Prometheus-2" в "Vika"
  * нажимаем на dashboards add visualition выбираем "Vika"
  * снизу меняем на "cod"
  * Переходим в терминал и пишем

```
echo -e "# TYPE OILCOINT_metric1 gauge\nOILCOINT_metric1 0" | curl --data-binary @- http://localhost:8428/api/v1/import/prometheus  
curl -G 'http://localhost:8428/api/v1/query' --data-urlencode 'query=OILCOINT_metric1'

```
(Значение 0 меняем на любое другое)
* Копируем переменную OILCOINT_metric1 и вставляем в cod
* Нажимаем run 
![image](https://github.com/user-attachments/assets/49d9f76a-a545-485d-b191-ae67ebf6ddc3)
* Должно получится следующее 







  


 
