# Для начала нужно установить Linux Oracle на VirtualBox:

Установить образ Linux

Выделить 4 ядра

Выделить 4096 мб оперативной памяти



# Установка docker

Устанавливает утилиту wget на систему

`sudo yum install wget`

![image](https://github.com/user-attachments/assets/6a775b7a-5f2f-447a-bad5-95174877f65a)


Скачиваем файл репозитория

`sudo wget -P /etc/yum.repos.d/ https://download.docker.com/linux/centos/docker-ce.repo`

![image](https://github.com/user-attachments/assets/159cae5f-0bff-4505-b3c9-159bb7381eae)


Устанавливаем docker

`sudo yum install docker-ce docker-ce-cli containerd.io`

![image](https://github.com/user-attachments/assets/b04a5a61-fa83-49f5-946b-17c47cebd0d0)


Запускаем его и разрешаем автозапуск

`sudo systemctl enable docker --now`

![image](https://github.com/user-attachments/assets/a15b59e6-f0a9-4797-a880-1b3efa1187d0)


# Установка compose

Для начала нужно убедиться в наличии пакета curl

`sudo yum install curl`

![image](https://github.com/user-attachments/assets/1de39b6f-56fc-427f-8c64-e58bf2999925)


Объявление переменной COMVER, полученной в результате curl запроса, хранящей в себе номер последней
версии Docker Compose

`COMVER=$(curl -s https://api.github.com/repos/docker/compose/releases/latest | grep 'tag_name' | cut -d\" -f4)`

Теперь скачиваем скрипт docker-compose последней версии, используя объявленную ранее переменную и помещаем его в каталог /usr/bin

`sudo curl -L "https://github.com/docker/compose/releases/download/$COMVER/docker-compose-$(uname -s)-$(uname -m)" -o /usr/bin/docker-compose`

![image](https://github.com/user-attachments/assets/d209eb53-a0a1-4029-95f5-1a4378824c7e)


Предоставление прав на выполнение файла docker-compose

`sudo chmod +x /usr/bin/docker-compose`

Проверка установленной версии Docker Compose

`sudo docker-compose --version`

![image](https://github.com/user-attachments/assets/3a446f52-35dd-4b4b-94d1-bb81ed6f5173)




# Делаем grafana

Установка git

`sudo yum install git`

![image](https://github.com/user-attachments/assets/b0bceb86-0ab7-47d4-aed6-79c46869efd4)


Этот код скачивает содержимое репозитория skl256/grafana_stack_for_docker

`sudo git clone https://github.com/skl256/grafana_stack_for_docker.git`

Заходит в папку - cd

`cd grafana_stack_for_docker`

cd .. - возвращает в папку выше


![image](https://github.com/user-attachments/assets/284a3260-5279-473b-8a6d-d0ac67aa2fd6)


Cоздаем папки двумя разными способами

`sudo mkdir -p /mnt/common_volume/swarm/grafana/config`

`sudo mkdir -p /mnt/common_volume/grafana/{grafana-config,grafana-data,prometheus-data,loki-data,promtail-data}`

Выдаем права

`sudo chown -R $(id -u):$(id -g) {/mnt/common_volume/swarm/grafana/config,/mnt/common_volume/grafana}`

Создаем файл

`sudo touch /mnt/common_volume/grafana/grafana-config/grafana.ini`

Копирование файлов

`sudo cp config/* /mnt/common_volume/swarm/grafana/config/`

Переименовывание файла

`sudo mv grafana.yaml docker-compose.yaml`


![image](https://github.com/user-attachments/assets/8b294d77-f55d-4034-97bd-92fa96bb750c)


Собрать докер (нужно запускать из папки где docker-compose.yaml)

`sudo docker compose up -d`

Опустить докер - sudo docker compose stop


![image](https://github.com/user-attachments/assets/ef6e4959-d09f-473f-b399-c59baec32e3b)




# Начинаем чистку файлов

Команда открывает файл docker-compose.yaml в текстовом редакторе vi с правами суперпользователя

`sudo vi docker-compose.yaml`

Что-бы что-то изменить в тесковом редакторе нужно нажать insert на клавиатуре

Затем в docker-compose нужно вставить node-exporter и удалить ненужные файлы (можно вставить готовый докер)


![image](https://github.com/user-attachments/assets/e7d42e03-97e0-4402-8f01-6fa37159048c)


выйти не сохраняясь из vim - `esc -> :q!`

выйти сохраняясь из vim - `esc -> :wq!`

Заходим в другую папку 

`cd /mnt/common_volume/swarm/grafana/config/`

Открываем файл prometheus.yaml в текстовом редакторе vi с правами суперпользователя

`sudo vi prometheus.yaml`


![image](https://github.com/user-attachments/assets/f0cb6e43-0d11-4577-94ab-b1fce8866bef)


Далее нужно исправить targets: на exporter:9100


![image](https://github.com/user-attachments/assets/1d212dbf-58d4-4230-b574-9d0021536457)




# Делаем grafana на сайте

- Заходим на сайт localhost:3000

- Регестрируемся на сайте
     - User и Password - admin, потом где он предлагает установить пароль, нажимаем - скип

- Открываем Dashboards - create dashboard - configure a new data source - prometheus
     - Connection: http://prometheus:9090
     - Authentication - basic authentication - admin admin
     - save and test

- Dashboards - import dashboard
     - Find and import... - 1860 - load
     - prometheus - prometheus
     - import



![image](https://github.com/user-attachments/assets/68c3e2b0-468f-447f-b898-59c4a102663e)



# Делаем VictoriaMetrics

Для начала зайдем в нужную папку

`cd grafana_stack_for_docker`

Открываем docker-compose

`sudo vi docker-compose.yaml`

После prometheus вставляем vmagent (но у нас уже вставлен готовый докер)


![image](https://github.com/user-attachments/assets/cf273d16-81f0-464b-a9b4-02952bb7dc9a)


Открываем grafana на сайте и также создаем dashboard, но в Connection пишем http://victoriametrics:8428

Заменяем имя из "Prometheus-2" в "Vika" нажимаем на dashboards add visualition выбираем "Vika" снизу меняем на "code"

В терминале пишем:

`echo -e "# TYPE OILCOINT_metric1 gauge\nOILCOINT_metric1 0" | curl --data-binary @- http://localhost:8428/api/v1/import/prometheus`

команда отправляет бинарные данные (например, метрики в формате Prometheus) на локальный сервер

Потом вводим команду которая делает запрос к API для получения данных по метрике OILCOINT_metric1

`curl -G 'http://localhost:8428/api/v1/query' --data-urlencode 'query=OILCOINT_metric1'`

Значение 0 меняем на любое другое


![image](https://github.com/user-attachments/assets/e8a75e4e-d245-4653-aae3-d49b9d7a1c6c)


Заходим на сайт localhost:8428 и открываем vmui

Копируем переменную OILCOINT_metric1 и вставляем в query


![image](https://github.com/user-attachments/assets/8db5fe42-c790-4d05-bb36-ad946a5c697f)


Нажимаем run

Копируем переменную OILCOINT_metric1 и вставляем в code

![image](https://github.com/user-attachments/assets/48e0b7b3-04cf-4854-a37a-801e0d1dc4d2)

