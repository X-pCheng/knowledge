# Docker

```bash
docker stop portainer
docker rm portainer

docker run -d \
--restart unless-stopped \
--name portainer \
-p 46402:9000 \
-v /share/Container/portainer/data:/data \
-v /var/run/docker.sock:/var/run/docker.sock \
-e TZ=Asia/Shanghai \
portainer/portainer-ce:2.19.4
```
