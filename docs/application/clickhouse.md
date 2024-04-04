# 安装

[Clickhouse 安装](https://clickhouse.com/docs/zh/getting-started/install)

# FAQ

## 启动报错，提示权限不够

```bash
Processing configuration file 'config.xml'.
Couldn't save preprocessed config to /var/lib/clickhouse/preprocessed_configs/config.xml: Access to file denied: /var/lib/clickhouse/preprocessed_configs/config.xml
Logging trace to /var/log/clickhouse-server/clickhouse-server.log
Poco::Exception. Code: 1000, e.code() = 13, Access to file denied: /var/log/clickhouse-server/clickhouse-server.log, Stack trace (when copying this message, always include the lines below):
```

需要注意以下文件夹的所有者是否统一为 clickhouse 用户

```bash
/var/lib/clickhouse
/var/log/clickhouse-server
/etc/clickhouse-server
/etc/clickhouse-client
```

如果没有修改文件夹的所有者，执行启动命令

`clickhouse-server --config-file=/etc/clickhouse-server/config.xml`

可能会遇到如下错误

```bash
Application: DB::Exception: Effective user of the process (root) does not match the owner of the data (clickhouse). Run under ‘sudo -u clickhouse’.
```

提示使用 clickhouse 用户启动

`# sudo -u clickhouse clickhouse-server --config-file=/etc/clickhouse-server/config.xml`

执行后又报错

```bash
Couldn’t save preprocessed config to /var/lib/clickhouse/preprocessed_configs/config.xml: Access to file denied: /var/lib/clickhouse/preprocessed_configs/config.xml
```

此时修改以下几个文件夹用户为 root

```bash
 chown -R root:root /var/lib/clickhouse /var/log/clickhouse-server /etc/clickhouse-server /etc/clickhouse-client
```
