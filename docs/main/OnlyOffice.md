本示例部署依赖:

1. MySQL，[MySQL 配置与操作](main/MySQL配置与操作.md)
2. Redis，[Redis](main/Redis.md)
3. rabbitmq，[rabbitmq](main/rabbitmq.md)

# Docker

```bash
docker pull onlyoffice/documentserver:8.0.1

docker stop onlyoffice
docker rm onlyoffice

docker run -d \
--restart unless-stopped \
--name onlyoffice \
--link mysql8:mysql8 \
--link redis:redis \
--link rabbitmq:rabbitmq \
-p 46409:80 \
-v /share/Container/onlyoffice_cus/logs:/var/log/onlyoffice  \
-v /share/Container/onlyoffice_cus/data:/var/www/onlyoffice/Data \
-v /share/Container/onlyoffice_cus/cache:/var/lib/onlyoffice \
-e TZ=Asia/Shanghai \
-e JWT_SECRET=TNXXXXXXXXXN0MKFBNudf \
-e DB_TYPE=mysql \
-e DB_HOST=mysql8 \
-e DB_PORT=3306 \
-e DB_NAME=onlyoffice \
-e DB_UAER=onlyoffice \
-e DB_PWD=onlyoffice \
-e AMQP_URI=amqp://admin:1025@rabbitmq:5672 \
-e AMQP_TYPE=rabbitmq \
-e REDIS_SERVER_HOST=redis \
-e REDIS_SERVER_PORT=6379 \
onlyoffice/documentserver:8.0.1
```

> /var/log/onlyoffice 对于 ONLYOFFICE 文档日志
>
> /var/www/onlyoffice/Data 证书
>
> /var/lib/onlyoffice 用于文件缓存
>
> /var/lib/postgresql 用于数据库

可设置的环境变量参见：https://github.com/ONLYOFFICE/Docker-DocumentServer?tab=readme-ov-file#available-configuration-parameters

# FAQ

## An exception occurred inside the document service: Invalid token

参见：https://api.onlyoffice.com/editors/signature/

## Error: Configuration property "wopi.dummy.sampleFilePath" is not defined

```json
"wopi": {
   "enable": true,
   "dummy": {
     "enable": true,
     "sampleFilePath": ""
   }
 }
```

参见：

- https://api.onlyoffice.com/editors/wopi/

- https://helpcenter.onlyoffice.com/installation/docs-developer-configuring.aspx

## Configuration property "maxNameLength" is not defined

参见：https://github.com/ONLYOFFICE/DocumentServer/issues/367
![alt text](../_attch_file/onlyoffice-bug.png)