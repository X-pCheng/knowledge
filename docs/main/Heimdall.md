# Docker

```bash
docker stop heimdall
docker rm heimdall

sudo docker run -d \
--restart unless-stopped \
--name heimdall \
-p 46401:80 \
-v /share/Container/heidmall/config:/config \
linuxserver/heimdall:2.6.1

```
