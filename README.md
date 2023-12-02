Docker

## DKR-1 +


## DKR-2 +

docker run -d -p 127.0.0.1:8889:80 --name rbm-dkr-02 -v $(pwd)/

data:/internal nginx:stable

docker exec -it rbm-dkr-02 bash

mv nginx.conf /etc/nginx

docker exec -it rbm-dkr-02 nginx -s reload

curl http://127.0.0.1:8889

docker exec -ti rbm-dkr-02 md5sum /etc/nginx/nginx.conf


## DKR-3 +


docker run -d -p 127.0.0.1:8890:80 --name rbm-dkr-03 -v /home/user/nginx.conf:/etc/nginx/nginx.conf nginx:stable

md5sum nginx.conf

docker exec -it rbm-dkr-03 md5sum /etc/nginx/nginx.conf

nano updnginx.conf

cp updnginx.conf nginx.conf

docker exec rbm-dkr-03 nginx -s reload


## DKR-4 +

docker volume create rbm-dkr-04-volume

docker run -d--name rbm-dkr-04 -p 127.0.0.1:8891:80 -v rbm-dkr-04-volume:/var/log/nginx/external-v $(pwd)/nginx.conf:/etc/nginx/nginx.conf:ronginx:stable
  
curl 127.0.0.1:8891


docker volume ls


ls -la /var/lib/docker/volumes/rbm-dkr-04-volume/_data



## DKR 05 +
docker run -d --name rbm-dkr-05-run-$(cat /dev/urandom | tr -cd 'a-f0-9' | head -c 10) nginx:stable

docker run -d --name rbm-dkr-05-stop nginx:stable

docker ps | tee /home/user/ps.txt

docker stop rbm-dkr-05-stop

docker stop $(docker ps -q)

docker rm $(docker ps -aq)




## DKR 06 +?


docker run -d -p 127.0.0.1:8892:80 --name rbm-dkr-06-local --log-driver local --log-opt max-size=10mb nginx:stable

curl --silent http://127.0.0.1:8892 > /dev/null

docker inspect rbm-dkr-06-local

cat /var/lib/docker/containers/$(docker ps -q --filter "name=rbm-dkr-06-local")/$(docker ps -q --filter "name=rbm-dkr-06-local")-json.log


sudo nano /etc/docker/daemon.json

{
  "log-driver": "local",
  "log-opts": {
    "max-size": "10m"
  }
}

sudo systemctl restart docker

docker run -d -p 127.0.0.1:8893:80 --name rbm-dkr-06-global nginx:stable

## DKR 07 +

docker pull nginx:stable-alpine

docker tag nginx:stable-alpine nginx:rbm-dkr-07

docker images

docker run -d --name rbm-dkr-07 nginx:rbm-dkr-07

docker ps


## DKR 08 +
```
// Используем базовый образ nginx:stable
FROM nginx:stable

// Копируем скачанный конфигурационный файл внутрь контейнера
COPY nginx.conf /etc/nginx/nginx.conf
```



Сборка образа с тегом rbm-dkr-08
```
docker build -t nginx:rbm-dkr-08 .
```
 Вывод списка образов на хосте
```
docker images
```
Запуск контейнера в фоновом режиме, слушающего на 127.0.0.1:8900
```
docker run -d -p 127.0.0.1:8900:80 --name nginx-container nginx:rbm-dkr-08
```
Вывод списка запущенных контейнеров
```
docker ps
```



Проверка работы контейнера
```
curl 127.0.0.1:8900
```

## DKR 09 +

```
# Используем базовый образ ubuntu:18.04
FROM ubuntu:18.04

# Устанавливаем пакет nginx
RUN apt-get update && \
    apt-get install -y nginx

# Копируем конфигурационный файл nginx
COPY nginx.conf /etc/nginx/nginx.conf

# Устанавливаем основную рабочую директорию
WORKDIR /etc/nginx/

# Определяем Volume
VOLUME /var/lib/nginx

# Устанавливаем точку входа
ENTRYPOINT ["nginx"]

# Определяем параметры CMD, как в образе nginx:stable
CMD ["-g", "daemon off;"]
```


```
docker build -t nginx:rbm-dkr-09 .

```

```
docker run -d -p 127.0.0.1:8901:80 --name nginx-container nginx:rbm-dkr-09
```

