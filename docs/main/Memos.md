# Docker

- 使用 MySQL 存储，点击查看 [官方说明](https://www.usememos.com/docs/advanced-settings/database)
- 本例使用 MySQL 容器，用 `--link` 参数绑定
- MySQL 创建库和用户参见 [MySQL 配置与操作](main/MySQL配置与操作.md)

```shellscript
docker run -d \
--restart unless-stopped \
--name memos \
--link mysql8:mysql8 \
-p 46452:5230 \
-v /share/Container/memos/memos:/var/opt/memos \
-e TZ=Asia/Shanghai \
-e MEMOS_DRIVER=mysql \
-e "MEMOS_DSN=memos:memos@tcp(mysql8)/memos" \
neosmemo/memos:stable
```
