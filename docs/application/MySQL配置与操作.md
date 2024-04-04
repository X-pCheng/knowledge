# 保护 MySQL 数据库

MySQL 数据库包安装完成后，mysql-secure-installation 实用程序将自动启动。但是，如果你没有自动启动，可执行以下命令：

```bash
sudo mysql_secure_installation
```

检查数据库对不同用户使用的身份验证方法。你可以通过运行以下命令来执行此操作。

```sql
SELECT user,authentication_string,plugin,host FROM mysql.user;
```

root 用户默认使用 auth_socket 插件进行了身份验证。我们需要使用下面的“ALTER USER”命令切换到“密码验证”的使用。确保使用安全密码（应超过 8 个字符，结合数字、字符串和特殊符号），因为它将替换你在执行上述命令“sudo mysql_secure_installation” 时设置的密码。运行以下命令。

```sql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'your_password';
```

# 密码策略调整

```sql
SHOW VARIABLES LIKE 'validate_password%';
set global validate_password.policy=0;
set global validate_password.length=1;
```

# 创建用户并授权

一切都设置好后，你可以创建一个新用户，你将授予该用户适当的权限。

我们将创建一个用户并分配对所有数据库表的权限以及更改、删除和添加用户权限的权限。

逐行执行下面的命令。

```sql
CREATE USER 'xpcheng'@'%' IDENTIFIED WITH mysql_native_password BY '1111';

GRANT ALL PRIVILEGES ON *.* TO 'xpcheng'@'%' WITH GRANT OPTION;
```

重新加载授权表并将更改更新到 MySQL 数据库。

```sql
FLUSH PRIVILEGES;
```

# 重启服务

一切都设置好后，重启。

```bash
root@XpChengHW14s:/etc/init.d# systemctl restart mysql

root@XpChengHW14s:/etc/init.d# systemctl status mysql
```