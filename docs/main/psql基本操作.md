## 启动停止命令

```bash
xpcheng@XpChengHW14s:~$ /etc/init.d/postgresql

Usage: /etc/init.d/postgresql {start|stop|restart|reload|force-reload|status} [version ..]
```

## 创建用户&修改密码&授权

进入数据库

```bash
xpcheng@XpChengHW14s:~$ sudo -u postgres psql
```

执行命令

```sql
postgres=# \l

                                             List of databases

   Name    |  Owner   | Encoding | Collate |  Ctype  | ICU Locale | Locale Provider |   Access privileges

-----------+----------+----------+---------+---------+------------+-----------------+-----------------------

 postgres  | postgres | UTF8     | C.UTF-8 | C.UTF-8 |            | libc            |

 template0 | postgres | UTF8     | C.UTF-8 | C.UTF-8 |            | libc            | =c/postgres          +

           |          |          |         |         |            |                 | postgres=CTc/postgres

 template1 | postgres | UTF8     | C.UTF-8 | C.UTF-8 |            | libc            | =c/postgres          +

           |          |          |         |         |            |                 | postgres=CTc/postgres

(3 rows)

postgres=# \du;
                                   List of roles
 Role name |                         Attributes                         | Member of

-----------+------------------------------------------------------------+-----------

 postgres  | Superuser, Create role, Create DB, Replication, Bypass RLS | {}


postgres=# CREATE USER root WITH PASSWORD '1025';

CREATE ROLE

postgres=# ALTER USER affine PASSWORD 'affine';

postgres=# ALTER USER root WITH SUPERUSER;


postgres=# grant all privileges on database affine to affine;
postgres=# GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA auth TO appflowy;
postgres=# GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO appflowy;

```

## 开启远程连接

PostgreSQL 支持多种身份认证方式。最常用的方法如下：

- `Trust`: 只要满足`pg_hba.conf`定义的条件，一个角色就可以不使用密码就能连接服务器
- `Password`: 通过密码，一个角色可以连接服务器。密码可以被存储为`scram-sha-256`,`md5`, 和`password(明文)`。
- `Ident`: 仅仅支持 TCP/IP 连接。它通常通过一个可选的用户名映射表，获取客户端操作系统用户名。
- `Peer`: 和`Ident`一样，仅仅支持本地连接。

PostgreSQL 客户端身份验证通常被定义在`pg_hba.conf`文件中。默认情况下，对于本地连接，PostgreSQL 被设置成身份认证防范`peer`。

允许远程访问需要修改配置文件，注意要用超级用户。

### 修改`pg_hba.conf`

```sql
postgres=# show config_file;

               config_file

-----------------------------------------

 /etc/postgresql/15/main/postgresql.conf

(1 row)

postgres=# \q
```

```bash
xpcheng@XpChengHW14s:~$ cat /etc/postgresql/15/main/postgresql.conf
xpcheng@XpChengHW14s:~$ cd /etc/postgresql/15/main/

xpcheng@XpChengHW14s:/etc/postgresql/15/main$ ls -l

total 60

drwxr-xr-x 2 postgres postgres  4096 Oct 29 14:51 conf.d

-rw-r--r-- 1 postgres postgres   315 Oct 29 14:51 environment

-rw-r--r-- 1 postgres postgres   143 Oct 29 14:51 pg_ctl.conf

-rw-r----- 1 postgres postgres  5001 Oct 29 15:10 pg_hba.conf

-rw-r----- 1 postgres postgres  1636 Oct 29 14:51 pg_ident.conf

-rw-r--r-- 1 postgres postgres 29707 Oct 29 14:51 postgresql.conf

-rw-r--r-- 1 postgres postgres   317 Oct 29 14:51 start.conf

xpcheng@XpChengHW14s:/etc/postgresql/15/main$ vim pg_hba.conf
```

可以看到默认的配置文件中有一条关于 IPv4 的规则

```ini
# IPv4 local connections:
host    all             all             127.0.0.1/32            md5
```

如果想要允许 192.168.1 网段的所有主机通过 md5 的形式进行连接，请增加下面的这句：

```ini
# Allow 192.168.1.XXX hosts to connect via md5
host    all             all             192.168.1.0/24          md5
```

如果想要允许所有的主机通过 md5 的形式进行连接，请增加下面的这句：

```ini
# Allow all hosts to connect via md5
host    all             all             0.0.0.0/0               md5
```

### 修改`postgresql.conf`

在这个配置文件中，可以看到下面的内容：

```ini
# - Connection Settings -

#listen_addresses = 'localhost'         # what IP address(es) to listen on;
                                        # comma-separated list of addresses;
                                        # defaults to 'localhost'; use '*' for all
                                        # (change requires restart)
port = 5432                             # (change requires restart)
max_connections = 100                   # (change requires restart)
```

根据提示信息，我们在`#listen_addresses = 'localhost'`行后，在`port`行前增加一行:

```ini
listen_addresses = '*'
port = 5432                             # (change requires restart)
max_connections = 100                   # (change requires restart)
```
