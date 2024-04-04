rabbit 的管理镜像有两个端口，一个 API，一个 web 控制台

# Docker

```bash
docker stop rabbitmq
docker rm rabbitmq

docker run -d \
--restart unless-stopped \
--hostname rabbitmq_nas \
--name rabbitmq \
-p 46470:5672 \
-p 46472:15672 \
-e RABBITMQ_DEFAULT_USER=admin \
-e RABBITMQ_DEFAULT_PASS=1025 \
-e TZ=Asia/Shanghai \
-v /share/Container/rabbitmq/home:/var/lib/rabbitmq \
-v /share/Container/rabbitmq/conf:/etc/rabbitmq \
rabbitmq:3.13-management
```

# FAQ

浏览器无法访问，可能是没有安装插件,需进入容器手动安装

```bash
docker exec -it rabbitmq bash
rabbitmq-plugins enable rabbitmq_management
```
