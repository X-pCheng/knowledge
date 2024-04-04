# 概述

修改 `FineDB` 中 `FINE_CONF_ENTITY` 表的`SystemConfig.serverInit`参数，将字段值改 `fail` ，修改后重启 BI 工程，配置生效。

# Finedb为内置库

## Windows

1. 打开文件`webapps/webroot/WEB-INF/embed/finedb/db.script`

2. `Ctrl+F`搜索字段`SystemConfig.serverInit`

3. 修改`success`改成`false`

4. 重启工程

## Linux
```shell
# 切换到finedb目录下
cd /opt/apache-tomcat-9.0.30/webapps/webroot/WEB-INF/embed/finedb
# 打开db.script文档 
vi db.script 
```

1. 按`esc`后输入`/SystemConfig.serverInit`，`/`是搜索，后面加搜索内容
2. 输入`i`进入编辑模式
3. 修改`value`值为`false`
4. `esc`进入命令模式，输入`wq!`保存并退出

# Finedb为外置库

## 方法一

数据库中修改`FINE_CONF_ENTITY` 表中的`SystemConfig.serverInit`参数改为`false`或者`fail`
```sql
INSERT INTO FINE_CONF_ENTITY
VALUES
('SystemConfig.serverInit','fail')
```

## 方法二

用可视化插件BI前台修改参数并重启
[FINE_CONF_ENTITY可视化配置- FineBI帮助文档 FineBI帮助文档 (fanruan.com)](https://help.fanruan.com/finebi/doc-view-1235.html?source=4)

# 补充
重启后若没有跳转到重新设置管理员密码的界面则访问`url`路径可手动修改为`ip:port/webroot/decision/login/initialization`