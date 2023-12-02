Docker

## DKR-1 +


## DKR-2 +

docker run -d -p 127.0.0.1:8889:80 --name rbm-dkr-02 -v $(pwd)/data:/internal nginx:stable
docker exec -it rbm-dkr-02 bash
mv nginx.conf /etc/nginx
docker exec -it rbm-dkr-02 nginx -s reload
curl http://127.0.0.1:8889
docker exec -ti rbm-dkr-02 md5sum /etc/nginx/nginx.conf


## DKR-3 
Но


## DKR-4 +

docker volume create rbm-dkr-04-volume

docker run -d--name rbm-dkr-04 -p 127.0.0.1:8891:80-v rbm-dkr-04-volume:/var/log/nginx/external-v $(pwd)/nginx.conf:/etc/nginx/nginx.conf:ronginx:stable
  
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




## DKR 06

docker run -d --name web --log-driver json-file -p 8080:80 nginx