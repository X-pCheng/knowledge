[PostgreSQL Downloads](https://www.postgresql.org/download/)

# Docker

## 下载镜像

从在线存储库**下载 PostgreSQL Docker 镜像**（docker images 可列出安装在系统上的 Docker 镜像）。

本次指定版本为 12.3

```bash
docker pull postgres:12.3

docker images
```

## 启动镜像

```bash
docker stop postgres12
docker rm postgres12

docker run -d \
--restart unless-stopped \
--name postgres12 \
-p 46454:5432 \
-v /share/Container/postgres/data:/var/lib/postgresql/data \
-e POSTGRES_PASSWORD=1025 \
postgres:12.3
```

# psql 基本操作

[psql 基本操作](main/psql基本操作.md)
