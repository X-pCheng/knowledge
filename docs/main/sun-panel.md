一个服务器、NAS 导航面板、Homepage、浏览器首页。

部署后默认账号：`admin@sun.cc/12345678`

# 二进制文件安装

前往 Github [<u>Releases</u>](https://github.com/hslr-s/sun-panel/releases)下载二进制文件

```bash
[root@ECSNode debian]# cd /opt/program/
[root@ECSNode program]# wget https://github.com/hslr-s/sun-panel/releases/download/v1.4.0-beta24-02-20/sun-panel_v1.4.0-beta24-02-20_linux_amd64.tar.gz

[root@ECSNode program]# mv sun-panel_v1.4.0-beta24-02-20_linux_amd64.tar.gz sun-panel-1.4.0.tar.gz
[root@ECSNode program]# tar -zxvf sun-panel-1.4.0.tar.gz
[root@ECSNode program]# mv sun-panel_v1.4.0-beta24-02-20_linux_amd64 sun-panel
[root@ECSNode program]# ls -l
total 18928
drwxrwxrwx 2 root root     4096 Mar 18 09:47 frps
drwxr-xr-x 3 root root     4096 Mar 17 22:16 nginx
-rw-r--r-- 1 root root  1112471 Apr 12  2023 nginx-1.24.0.tar.gz
drwxr-xr-x 3 root root     4096 Feb 21 11:48 sun-panel
-rw-r--r-- 1 root root 18252676 Mar 20 20:46 sun-panel-1.4.0.tar.gz

[root@ECSNode program]# cd sun-panel/
[root@ECSNode sun-panel]# ls -l
total 30788
-rwxr-xr-x 1 root root 31522672 Feb 21 11:48 sun-panel
drwxr-xr-x 4 root root     4096 Feb 21 11:48 web
```

# 使用 Docker 部署

## 启动容器

```bash
docker stop sun-panel
docker rm sun-panel

docker run -d \
--restart unless-stopped \
--name sun-panel \
-p 3002:3002 \
hslr/sun-panel:1.3.0
```

## 拷贝配置和数据到宿主机

```bash
[root@ECSNode debian]# cd /opt/program/
[root@ECSNode debian]# mkdir sun-panel
[root@ECSNode program]# cd sun-panel/
[root@ECSNode sun-panel]# docker cp sun-panel:/app/conf ./
Emulate Docker CLI using podman. Create /etc/containers/nodocker to quiet msg.
[root@ECSNode sun-panel]# docker cp sun-panel:/app/database ./
Emulate Docker CLI using podman. Create /etc/containers/nodocker to quiet msg.
[root@ECSNode sun-panel]# ls
conf  database
```

## 挂载配置和数据，重建容器

```bash
docker stop sun-panel
docker rm sun-panel

docker run -d \
--restart unless-stopped \
--name sun-panel \
-p 3002:3002 \
-v /opt/program/sun-panel/conf:/app/conf \
-v /opt/program/sun-panel/database:/app/database \
-v /opt/program/sun-panel/custom:/app/web/custom \
-v /opt/program/sun-panel/uploads:/app/uploads \
hslr/sun-panel:1.3.0
```

# 配置系统服务

systemd 可参见: [systemd 安装与使用](main/systemd_install_and_service_exmaple.md)

```bash
[Unit]
# 服务名称，可自定义
Description = sum-panel server
After = network.target syslog.target
Wants = network.target

[Service]
Type = forking
# 启动nginx的命令
ExecStart = /opt/program/sun-panel/sun-panel

[Install]
WantedBy = multi-user.target
```
