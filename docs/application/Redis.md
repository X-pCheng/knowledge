# Redis

# Docker

```bash
docker stop redis
docker rm redis

docker run -d \
--restart unless-stopped \
--name redis \
-p 46479:6379 \
-v /share/Container/redis/data:/data \
-e TZ=Asia/Shanghai \
redis:7.2.4
```
