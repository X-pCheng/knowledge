默认账号:
`databasir/databasir`

部署完成后可以通过修改数据库的方式修改用户名

# 准备数据库 MySQL

创建库和用户参见 [MySQL 配置与操作](main/MySQL配置与操作.md)

# Docker 部署

## 拉取镜像并修改 tag

```bash
sudo docker pull vrantt/databasir:latest
sudo docker tag vrantt/databasir:latest vrantt/databasir:20240318
```

## 启动容器

```bash
sudo docker stop databasir
sudo docker rm databasir

sudo docker run -d \
--restart unless-stopped \
--name databasir \
--link mysql8:mysql8 \
-p 33012:8080 \
-e TZ=Asia/Shanghai \
-e DATABASIR_DB_URL=mysql8:3306 \
-e DATABASIR_DB_USERNAME=databasir \
-e DATABASIR_DB_PASSWORD=databasir \
-v /home/xpcheng/docker-data/databasir/drivers:/app/drivers \
vrantt/databasir:20240318
```
