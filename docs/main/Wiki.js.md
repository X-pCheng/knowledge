本示例部署依赖:

1. MySQL，[MySQL 配置与操作](main/MySQL配置与操作.md)

# Docker

```bash
sudo docker run -d \
--restart unless-stopped \
--name wikijs \
--link mysql8:mysql8 \
-p 46401:3000 \
-e "DB_TYPE=mysql" \
-e "DB_HOST=mysql8" \
-e "DB_PORT=3306" \
-e "DB_USER=wikijs" \
-e "DB_PASS=wikijs" \
-e "DB_NAME=wikijs" \
ghcr.io/requarks/wiki:2.5
```
