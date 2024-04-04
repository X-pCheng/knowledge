# Docker 部署

```bash
docker stop mysql8
docker rm mysql8

docker run -d \
--restart unless-stopped \
--name mysql8 \
-p 46406:3306 \
-v /share/Container/mysql8/mysql-files:/var/lib/mysql-files \
-v /share/Container/mysql8/logs:/var/log/mysql \
-v /share/Container/mysql8/data:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD=1025 \
-e TZ=Asia/Shanghai \
mysql:8.3.0

```

# 基本命令

参见 [MySQL 配置与操作](main/MySQL配置与操作.md)

# FAQ

## 连接时报错：Public Key Retrieval is not allowed

### 方案一

在命令行模式下进入 mysql，输入以下命令:

```sql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'root';
```

或者

```sql
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'root';
```

### 方案二

在配置数据源时设置属性`allowPublicKeyRetrieval=true`
