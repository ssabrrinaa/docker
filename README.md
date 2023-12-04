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

## DKR-10 

```
# Используем базовый образ nginx с параметризуемой версией
ARG NG_VERSION
FROM nginx:${NG_VERSION}

# Устанавливаем переменную окружения с именем NG_VERSION
ENV NG_VERSION=${NG_VERSION}

# Создаем файл /opt/$ARG_FILE
ARG ARG_FILE
RUN touch "/opt/${ARG_FILE}"

# Передача аргумента для переменной окружения REBRAINME
ENV REBRAINME=DKR10

```

```
docker build -t nginx:rbm-dkr-10 --build-arg NG_VERSION=1.21.3 --build-arg ARG_FILE=myfile -f /home/user/Dockerfile /home/user
```

```
docker run -d --name rbm-dkr-10 -e REBRAINME=DKR10 nginx:rbm-dkr-10
```


Выведем переменные окружения внутри контейнера:
```
docker exec rbm-dkr-10 env
```


Выведем список файлов в директории /opt/ внутри контейнера:
```
docker exec rbm-dkr-10 ls /opt/
```

## DKR-11 +

```
dd if=/dev/zero of=./testfile bs=1M count=10
```

Dockerfile 

```
FROM ubuntu:20.04
ENV testenv1=env1
#создадим пользователя
RUN groupadd --gid 2000 user && useradd --uid 2000 --gid 2000 --shell /bin/bash --create-home user
#посмотрим состояние кэша apt до установки nginx
RUN ls -lah /var/lib/apt/lists/
RUN apt-get update -y && apt-get install nginGO111MODULE
#Скопируем наш тестовый файл
COPY testfile .
#Сменим права
RUN chown user:user testfile
USER user
CMD ["sleep infinity"]
```


```
docker build -t rbm-dkr-11:default --network=host .
```

```
docker inspect rbm-dkr-11:default
```


```
docker history rbm-dkr-11:default --no-trunc
```

4.1) Убедитесь, что у всех слоев есть размер.
4.2) Три директивы, создающие слои: RUN, COPY, и ADD.
4.3) Размер добавленный директивой RUN chown user:user testfile можно увидеть в выводе команды docker history.
4.4) Размер образа можно узнать с помощью docker images.

```
FROM ubuntu:20.04
ENV testenv1=env1
#создадим пользователя
RUN groupadd --gid 2000 user && useradd --uid 2000 --gid 2000 --shell /bin/bash --create-home user
#посмотрим состояние кэша apt до установки nginx
RUN ls -lah /var/lib/apt/lists/
RUN apt-get update -y && apt-get install nginx -y && rm -rf /var/lib/apt/lists/*
#Повторно проверим состояние кэша apt
RUN ls -lah /var/lib/apt/lists/
#Очистим кэш
RUN rm -rf /var/lib/apt/lists/*
RUN ls -lah /var/lib/apt/lists/
#Скопируем наш тестовый файл и назначим пользователя user владельцем
COPY --chown=user:user testfile .
USER user
CMD ["sleep", "infinity"]
```

```
docker build -t rbm-dkr-11:optimized .
```

```
docker history rbm-dkr-11:optimized --no-trunc
```

## DKR-12 +


Загрузка образа nginx:stable-alpine
```
docker pull nginx:stable-alpine
```

Добавление нового тега к образу
```
docker tag nginx:stable-alpine nginx:rbm-dkr-12
```

Удаление образа nginx:stable-alpine
```
docker rmi nginx:stable-alpine
```

Повторная загрузка образа nginx:stable-alpine
```
docker pull nginx:stable-alpine
```

Вывод списка образов и сохранение в файл
```
docker images | tee /home/user/images.txt
```

Удаление всех образов nginx одной командой
```
docker images | grep nginx | awk '{print $3}' | xargs docker rmi
```

Запуск контейнера в фоне с именем rbm-dkr-12
```
docker run -d --name rbm-dkr-12 nginx:stable-alpine
```

Попытка удалить образ nginx:stable-alpine без флагов
```
docker rmi nginx:stable-alpine
```

Удаление образа nginx:stable-alpine с флагом --force
```
docker rmi --force nginx:stable-alpine
```


Перезапуск контейнера
```
docker restart rbm-dkr-12
```


## DKR-13 +
Dockerfile
```
FROM alpine:latest
ARG MYARG
RUN apk update && apk add build-base
```

Сборка образа с именем cache:1

```
time docker build -t cache:1 .
```

Сборка образа с именем cache:2
```
time docker build -t cache:2 .
```

```
time docker build --no-cache -t cache:2 .  
```

Сборка образа без использования кэша
```
time docker build --no-cache -t cache:3 --build-arg MYARG=3 .
```

Повторная сборка образа с использованием кэша
```
time docker build -t cache:3 --build-arg MYARG=3 .
```

Сборка образа с именем cache:4 и новым значением MYARG
```
time docker build -t cache:4 --build-arg MYARG=4 .
```

Использование docker inspect для поиска аргумента MYARG
```
docker inspect --format='{{index .Config.Labels "MYARG"}}' cache:4
```

```
docker image history cache:1
```


## DKR-14 +


Dockerfile
```
# Multi-stage build
FROM golang:1.19-alpine AS builder

WORKDIR /app
COPY main.go .
ENV GO111MODULE auto

RUN go mod init main && \
    go mod tidy && \
    go build -o app

# Execution stage
FROM alpine:3.10.3

WORKDIR /app
COPY --from=builder /app/app .

CMD ["./app"]
```

```
docker build -t dkr-14-gocalc .
```


```
docker history dkr-14-gocalc
```


## DKR-15 +

Dockerfile 
```
FROM golang:1.19-alpine
WORKDIR /build
COPY main.go .
ENV GO111MODULE auto
RUN go mod init main && \
    go mod tidy && \
    go build -o app
FROM alpine:3.10.3
COPY --from=0 /build/app /app
ARG SECRET
RUN echo $SECRET > /secret.txt
CMD ["/app"]
```

```
docker build --build-arg SECRET=verySecret -t gocalc .
```

## DKR-16 -


