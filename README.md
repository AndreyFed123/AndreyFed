# 

## Быстрый старт

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
1) `git clone https://github.com/skl256/grafana_stack_for_docker.git `
   после вписания этой команды спрашивают об установлении пакета [N/y] вибираем y

![image](https://github.com/user-attachments/assets/a094b1e1-fbae-4aff-ad44-a19c78b5d4e1)

3) `cd grafana_stack_for_docker`
   
![image](https://github.com/user-attachments/assets/936bb14a-6479-4ea1-a7b2-bd6087586682)
 
3) `sudo mkdir -p /mnt/common_volume/swarm/grafana/config`

![image](https://github.com/user-attachments/assets/e8c8078e-1cf6-4019-8136-2af990990800)

4) `sudo mkdir -p /mnt/common_volume/grafana/{grafana-config,grafana-data,prometheus-data,loki-data,promtail-data} `

![image](https://github.com/user-attachments/assets/6a9bd0fc-2e22-4b64-a6d4-131036525bde)

5) `sudo chown -R $(id -u):$(id -g) {/mnt/common_volume/swarm/grafana/config,/mnt/common_volume/grafana} && \
touch /mnt/common_volume/grafana/grafana-config/grafana.ini && \ `

![image](https://github.com/user-attachments/assets/126e9fe3-bf9f-4a42-8884-1f38a7372c4c)

6) `touch /mnt/common_volume/grafana/grafana-config/grafana.ini `

![image](https://github.com/user-attachments/assets/45042295-f2ab-40cd-a2ef-634106663b1b)

8) `cp config/* /mnt/common_volume/swarm/grafana/config/` 

![image](https://github.com/user-attachments/assets/8f05b63c-c981-411b-a943-91f91fac8e07)

9) `mv grafana.yaml docker-compose.yaml `

![image](https://github.com/user-attachments/assets/0b54a2eb-2a08-47f1-bb4f-b80e08d039a6)

10)  `sudo docker compose up -d`

![image](https://github.com/user-attachments/assets/0c570ab8-a834-451d-a2a9-01df103301cc)

![image](https://github.com/user-attachments/assets/39bf2dfa-9e11-4095-8283-0710cab17667)

**Устанавливаем последнею версию и утилитю docker-compose**

1) `sudo yum install curl`

![image](https://github.com/user-attachments/assets/eb9a4a4f-72aa-4a9d-98a2-e95d0a903aa3)

2) `COMVER=$(curl -s https://api.github.com/repos/docker/compose/releases/latest | grep 'tag_name' | cut -d\" -f4)`

![image](https://github.com/user-attachments/assets/4be240f5-bd9f-4cd8-b977-f191e75b9062)

3) `curl -L "https://github.com/docker/compose/releases/download/$COMVER/docker-compose-$(uname -s)-$(uname -m)" -o /usr/bin/docker-compose`

![image](https://github.com/user-attachments/assets/b0162140-ec0b-4cd6-b76f-e6955e71744c)

4) `sudo chmod +x /usr/bin/docker-compose`

5) `sudo docker-compose --version`

![image](https://github.com/user-attachments/assets/0b0f5862-e030-4dc3-bdc6-e4e6d5f8e5b1)

## **Установка и настройка Docker**

1) `sudo wget -P /etc/yum.repos.d/ https://download.docker.com/linux/centos/docker-ce.repo
`

![image](https://github.com/user-attachments/assets/f8041f63-d355-46f9-8208-6e40a871597d)

2) ` sudo yum install docker-ce docker-ce`

![image](https://github.com/user-attachments/assets/a51b6314-c1cf-4dc2-8a11-fa2f73299701)

![image](https://github.com/user-attachments/assets/c8756496-b0e2-47a1-b168-cd00747e5bde)

              
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







  


 
