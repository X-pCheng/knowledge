# 通过 systemd 创建 Docker 系统服务

systemd 可参见: [systemd 安装与使用](main/systemd_install_and_service_exmaple.md)

```bash
[Unit]
Description=Docker Application Container Engine
Documentation=https://docs.docker.com
After=network-online.target
Wants=network-online.target
# Requires=docker.socket

[Service]
Type=notify
ExecStart=/usr/bin/docker
ExecReload=/bin/kill -s HUP $MAINPID
TimeoutSec=0
RestartSec=2
Restart=always

[Install]
WantedBy=multi-user.target
```

# Docker 应用部署

## Affine

参考 [Run AFFiNE with Custom Options](https://docs.affine.pro/docs/self-host-affine/run-affine-with-custom-options)

```bash
docker stop affine
docker rm affine

docker run -d \
--restart unless-stopped \
--name affine \
--link redis:redis \
--link postgres12:postgres12 \
-p 46403:3010 \
-v /share/Container/affine/data:/app/data \
-v /share/Container/affine/config:/root/.affine/config \
-v /share/Container/affine/storage:/root/.affine/storage \
-e TZ=Asia/Shanghai \
-e NODE_OPTIONS=\"--import=./scripts/register.js\" \
-e NODE_ENV=production \
-e AFFINE_CONFIG_PATH=/root/.affine/config \
-e AFFINE_SERVER_PORT=3010 \
-e AFFINE_ADMIN_EMAIL=xxx \
-e AFFINE_ADMIN_PASSWORD=xxx\
-e AFFINE_SERVER_HOST=xxx \
-e MAILER_HOST=smtp.163.com \
-e MAILER_PORT=465 \
-e MAILER_USER=xxx \
-e MAILER_PASSWORD=xxx \
-e MAILER_SENDER=xxx \
-e MAILER_SECURE=true \
-e DATABASE_URL=postgres://affine:affine@postgres12:5432/affine \
-e REDIS_SERVER_HOST=redis \
-e REDIS_SERVER_PORT=6379 \
-e REDIS_SERVER_DATABASE=0 \
--log-driver json-file \
--log-opt max-size=1000m \
ghcr.io/toeverything/affine-graphql:stable sh -c "node ./scripts/self-host-predeploy && node ./dist/index.js"
```

查看运行情况

```bash
docker ps -a
docker logs affine
```

## appflowy

清楚 appflowy 这个[架构](https://docs.appflowy.io/docs/documentation/appflowy-cloud/deployment)很重要
![alt text](../_attch_file/appflowy-arc.png)
因为后面的 docker 部署里面的组件就是这样规划的，清楚后自己调整起来（减少组件）也方便

本示例通过 Docker 部署，遇到也有挺多问题，主要是环境变量的 Docker compose 文件的变化，其他的操作照着官网说明一步步来就可以

最大的不同就是：pg 用的库是新建的 appflowy，不是默认的 postgres，遇到挺多问题

步骤主要是按照 [Github 部署文档](https://github.com/AppFlowy-IO/AppFlowy-Cloud/blob/main/doc/DEPLOYMENT.md)来跟着操作的

### Docker compose

我的 compose 文件只保留了必要的组件，其他的都是本机其他已经有的容器，我的 compose 文件[在这里](main/appflowy_compose_my.md)

### 设置环境变量

环境变量通过`.env`文件进行配置，修修改改反正，自己因为官网提供的我这边没法用，就自己改了一版[环境变量文件](main/appflowy_env_my.md)，里面有新增的环境变量

## Atheos

Atheos 是 Codiad 的一个更新且当前维护的分支，Codiad 是一个基于 Web 的 IDE 框架，占用空间小且要求最低。

Codiad 的构建考虑到了简单性，允许快速、交互式开发，而无需某些大型桌面编辑器的大量开销。话虽如此，即使是 Eclipse、NetBeans 和 Aptana 等 IDE 的用户也发现 Codiad 的简单性带来了巨大的好处。虽然简单性是关键，但我们并没有吝惜功能，并且有一个专门的开发团队积极添加更多功能。

Atheos 正在尽可能地扩展这种理念，试图进一步最小化其占用空间，同时最大化功能和性能。Atheos 的主要目标是从下到上彻底重写每一行代码，包括插件；原因是 Codiad 是经过很长一段时间构建的，并且较旧的文件仍然使用更旧的标准。Codiad 积累了大量技术债务需要解决。

有关该项目的更多信息，请查看[**文档**](https://www.atheos.io/docs)或[**Atheos 网站**](http://www.atheos.io/)

### 创建容器-默认

```bash
sudo docker stop atheos
sudo docker rm atheos

sudo docker run -d \
--restart unless-stopped \
--name atheos \
-p 33016:80 \
-e TZ=Asia/Shanghai \
hlsiira/atheos:latest
```

> 不要打开浏览器去登陆，直接进行下一步

### 挂载镜像并修改配置

```bash
## 拷贝镜像资源到宿主机
sudo docker cp  atheos:/var/www/html /home/xpcheng/docker-data/atheos
## 创建空配置文件
touch /home/xpcheng/docker-data/atheos/config.php
```

```bash
## 修改为默认中文语言
sudo vim /home/xpcheng/docker-data/atheos/traits/i18n.php
```

修改下图中的行

![alt text](../_attch_file/atheos1.png)

替换中文映射文件/home/xpcheng/docker-data/atheos/languages/cn.json 为 [Atheos 中文配置映射](main/atheos_lang_cn_map.md)

```bash
sudo rm -rf /home/xpcheng/docker-data/atheos/languages/cn.json
sudo vim /home/xpcheng/docker-data/atheos/languages/cn.json
```

### 重建容器

```bash
sudo docker stop atheos
sudo docker rm atheos

sudo docker run -d \
--restart unless-stopped \
--name atheos \
-p 33016:80 \
-e TZ=Asia/Shanghai \
-v /home/xpcheng/docker-data/atheos:/var/www/html \
hlsiira/atheos:latest
```

### 扩展能力

您可以轻松扩展以包含您可能需要的工具，并在重新创建容器时准备好它们。只需创建一个 `Dockerfile` 类似的：

```bash
FROM hlsiira/atheos
RUN apt update && apt install -y build-essential python
```

现在您可以构建并使用新映像：

```bash
$ docker build -t atheos .
$ docker run --name atheos --rm -p 8080:80 -d hlsiira/atheos
```

## Databasir

默认账号:
`databasir/databasir`

部署完成后可以通过修改数据库的方式修改用户名

### 准备数据库 MySQL

创建库和用户参见 [MySQL 配置与操作](main/MySQL配置与操作.md)

### 拉取镜像并修改 tag

```bash
sudo docker pull vrantt/databasir:latest
sudo docker tag vrantt/databasir:latest vrantt/databasir:20240318
```

### 启动容器

```bash
sud o docker stop databasir
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

## Etherpad

### 默认 Dockerfile

#### 构建镜像并运行

使用 [Etherpad 默认 Dockerfile](main/Etherpad默认Dockerfile.md)
进入 Etherpad 源码所在目录，并拷贝自定义 Dockerfile 到目录中，执行命令

```bash
docker build --rm --tag etherpad/etherpad .
```

```bash
docker run -d \
--restart unless-stopped \
--name etherpad \
-p 33027:9001 \
-v /home/xpcheng/docker-data/etherpad/data:/opt/etherpad-lite/var \
-u root \
etherpad/etherpad
```

#### 拷贝资源到宿主机

```bash
docker cp etherpad:/opt/etherpad-lite/settings.json /home/xpcheng/docker-data/etherpad/settings.json
docker cp etherpad:/opt/etherpad-lite/APIKEY.txt /home/xpcheng/docker-data/etherpad/config/APIKEY.txt
```

- settings.json：Etherpad 的配置文件
- APIKEY.txt：调用接口需要的 KEY，挂载宿主机可以固定下来，否则每次重建都会变

#### 准备数据库

使用 MySQL 来存储数据，MySQL 数据库相关操作参见 [MySQL 配置与操作](main/MySQL配置与操作.md)
修改`settings.json`中关于数据库的配置(db 开头的参数)

#### 重建容器并启动

用挂载的配置以及数据库存储重建容器并启动

```bash
sudo docker stop etherpad
sudo docker rm etherpad

docker run -d \
--restart unless-stopped \
--name etherpad \
-p 33027:9001 \
-v /home/xpcheng/docker-data/etherpad/data:/opt/etherpad-lite/var \
-v /home/xpcheng/docker-data/etherpad/config/settings.json:/opt/etherpad-lite/settings.json \
-v /home/xpcheng/docker-data/etherpad/config/APIKEY.txt:/opt/etherpad-lite/APIKEY.txt \
-v /home/xpcheng/docker-data/etherpad/upload_image:/opt/etherpad-lite/src/static/upload_image \
-e TZ=Asia/Shanghai \
-u root \
etherpad/etherpad:latest
```

### 自定义插件构建

默认的 Dockerfile 不含其他插件，但是我们可以[Etherpad 自定义 Dockerfile](main/Etherpad自定义Dockerfile.md)

同时调整配置文件`settings.json`关于使用到的插件的的配置，一个示例： [Etherpad 自定义配置](main/Etherpad自定义配置.md)

#### 修改 NPM 源

为了构建镜像时使用 npm，可以把 npm 源切换为国内的，避免网络因素导致构建失败

```bash
### npm修改镜像地址：
### 1、查看一下当前源
npm config get registry
### 2、切换为淘宝源
npm config set registry https://registry.npm.taobao.org/
### 3、还原仓库地址
npm config set registry https://registry.npmjs.org/
### 4、删除会恢复默认镜像
npm config delete registry
```

#### 构建自定义镜像并运行

```bash
docker build --rm --tag xpcheng/etherpad_icd_plugin:20240315 .
```

```bash
docker run -d \
--restart unless-stopped \
--name etherpad \
-p 33027:9001 \
-v /home/xpcheng/docker-data/etherpad/data:/opt/etherpad-lite/var \
-v /home/xpcheng/docker-data/etherpad/config/settings.json:/opt/etherpad-lite/settings.json \
-v /home/xpcheng/docker-data/etherpad/config/APIKEY.txt:/opt/etherpad-lite/APIKEY.txt \
-v /home/xpcheng/docker-data/etherpad/upload_image:/opt/etherpad-lite/src/static/upload_image \
-e TZ=Asia/Shanghai \
-u root \
xpcheng/etherpad_icd_plugin:20240315
```

### Web 笔记管理页面

[Etherpad_manage](main/Etherpad_manage.md)

## FileBrowser

```bash
sudo docker stop filebrowser
sudo docker rm filebrowser

sudo docker run -d \
--restart unless-stopped \
--name filebrowser \
-p 33005:80 \
-v /home/xpcheng/docker-data/filebrowser/data:/srv \
-v /home/xpcheng/docker-data/filebrowser/database:/database \
-v /home/xpcheng/docker-data/filebrowser/config:/config \
filebrowser/filebrowser:s6
```

## Foclboard

```bash
sudo docker stop focalboard
sudo docker rm focalboard

sudo docker run -d \
--restart unless-stopped \
--name focalboard \
-p 33015:8000 \
-v /home/xpcheng/docker-data/focalboard/data:/opt/focalboard/data \
mattermost/focalboard:7.8.9
```

## Heimdall

```bash
docker stop heimdall
docker rm heimdall

sudo docker run -d \
--restart unless-stopped \
--name heimdall \
-p 46401:80 \
-v /share/Container/heidmall/config:/config \
linuxserver/heimdall:2.6.1
```

## kkfileview

```bash
sudo docker stop kkfileview
sudo docker rm kkfileview

sudo docker run -d \
--restart unless-stopped \
--name kkfileview \
-p 33082:8012 \
keking/kkfileview:4.1.0
```

## Memos

- 使用 MySQL 存储，点击查看 [官方说明](https://www.usememos.com/docs/advanced-settings/database)
- 本例使用 MySQL 容器，用 `--link` 参数绑定
- MySQL 创建库和用户参见 [MySQL 配置与操作](main/MySQL配置与操作.md)

```bash
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

## Minio

```bash
  sudo docker stop minio
  sudo docker rm minio

  sudo docker run -d \
  --restart unless-stopped \
  --name minio \
  -p 46490:9000 \
  -p 46499:9090 \
  -e "MINIO_ROOT_USER=admin" \
  -e "MINIO_ROOT_PASSWORD=minio1111" \
  -e "MINIO_DOMAIN=minio.xxx.tech" \
  -e "MINIO_SERVER_URL=https://minio.xxx.tech" \
  -e MINIO_BROWSER_REDIRECT_URL=https://minio-console.xxx.tech \
  -e TZ=Asia/Shanghai \
  -v /home/xpcheng/docker-data/minio/data:/data \
  -v /home/xpcheng/docker-data/minio/config:/root/.minio \
  minio/minio:RELEASE.2024-03-15T01-07-19Z \
server /data --address 0.0.0.0:9000 --console-address 0.0.0.0:9090
```

### Nginx 反代

https://www.minio.org.cn/docs/minio/linux/integrations/setup-nginx-proxy-with-minio.html

## portainer

```bash
docker stop portainer
docker rm portainer

docker run -d \
--restart unless-stopped \
--name portainer \
-p 46402:9000 \
-v /share/Container/portainer/data:/data \
-v /var/run/docker.sock:/var/run/docker.sock \
-e TZ=Asia/Shanghai \
portainer/portainer-ce:2.19.4
```

## RabbitMQ

RabbitMQ 的管理镜像有两个端口，一个 API，一个 web 控制台

```bash
docker stop rabbitmq
docker rm rabbitmq

docker run -d \
--restart unless-stopped \
--hostname rabbitmq_nas \
--name rabbitmq \
-p 46470:5672 \
-p 46472:15672 \
-e RABBITMQ_DEFAULT_USER=admin \
-e RABBITMQ_DEFAULT_PASS=1025 \
-e TZ=Asia/Shanghai \
-v /share/Container/rabbitmq/home:/var/lib/rabbitmq \
-v /share/Container/rabbitmq/conf:/etc/rabbitmq \
rabbitmq:3.13-management
```

部署完成后，浏览器无法访问，可能是没有安装插件，需进入容器手动安装

```bash
docker exec -it rabbitmq bash
rabbitmq-plugins enable rabbitmq_management
```

## Redis

```bash
docker stop redis
docker rm redis

docker run -d \
--restart unless-stopped \
--name redis \
-p 46479:6379 \
-v /share/Container/redis/data:/data \
-e TZ=Asia/Shanghai \
redis:7.2.4
```

## ShowDoc

默认账号：`showdoc/showdoc`

启动后修改数据库的方式可以调整默认用户名

```bash
sudo docker stop showdoc
sudo docker rm showdoc

sudo docker run -d \
--restart unless-stopped \
--name showdoc \
--user=root \
--privileged=true \
-p 33023:80 \
-v /home/xpcheng/docker-data/showdoc/html:/var/www/html \
-e TZ=Asia/Shanghai \
star7th/showdoc:v3.2.5
```

### 如何升级

这里的升级是针对上面 docker 安装方式的升级。

如果你原来是采用非 docker 安装方式（如 php 安装方式）的话，请跳过本部分文字，直接去看下部分。

```bash

### 从原版官方库更新镜像。(中国大陆用户不建议直接使用原版镜像，可以用后面的加速镜像)
docker pull star7th/showdoc

### 中国大陆镜像更新命令（更新后记得执行docker tag命令以进行重命名）
docker pull registry.cn-shenzhen.aliyuncs.com/star7th/showdoc
docker tag registry.cn-shenzhen.aliyuncs.com/star7th/showdoc:latest star7th/showdoc:latest

##后续命令无论使用官方镜像还是加速镜像都需要执行


rm -rf  /showdoc_data/html_bak
#备份。如果可以的话，命令中的html_bak还可以加上日期后缀，以便保留不同日期的多个备份
mv /showdoc_data/html  /showdoc_data/html_bak

### 新建准备存放新版代码的目录
mkdir -p /showdoc_data/html
chmod  -R 777 /showdoc_data/html

### 删除旧容器
docker stop showdoc && docker rm showdoc

#启动showdoc容器
docker run -d --name showdoc --user=root --privileged=true -p 4999:80 \
-v /showdoc_data/html:/var/www/html/ star7th/showdoc

#执行新代码安装。默认安装中文版。如果想安装英文版，将下面参数中的zh改为en
curl http://localhost:4999/install/non_interactive.php?lang=zh

#转移旧数据库
cp  -f  /showdoc_data/html_bak/Sqlite/showdoc.db.php /showdoc_data/html/Sqlite/showdoc.db.php

#转移旧附件数据
cp -r -f /showdoc_data/html_bak/Public/Uploads/. /showdoc_data/html/Public/Uploads

### 重新给权限
chmod  -R 777 /showdoc_data/html

#如果中途出错，请重命名原来的/showdoc_data/html_bak文件为/showdoc_data/html ，然后重启容器便可恢复。

```

### 非 docker 安装方式如何升级

先参考前文，用 docker 方式全新安装一个 showdoc，并且做好数据持久化。
接下来，假设你原来安装的旧 showdoc 已上传到服务器的 /tmp/showdoc 目录，那么

```bash

#转移旧数据库
cp -r -f /tmp/showdoc/Sqlite/showdoc.db.php /showdoc_data/html/Sqlite/showdoc.db.php

#转移旧附件数据
\cp -r -f /tmp/showdoc/Public/Uploads/. /showdoc_data/html/Public/Uploads

```

### 数据备份

备份/showdoc_data/html 目录即可。比如执行下面命令压缩存放

```bash
zip -r /showdoc_data/showdoc_bak.zip  /showdoc_data/html
### 其中showdoc_bak.zip可以用日期后缀命名，以便多个备份。
### 你也可以用定时任务来实现定时备份。
```

## Sshwifty

```bash
docker stop sshwifty
docker rm sshwifty

docker run -d \
--restart unless-stopped \
--name sshwifty \
-p 8182:8182 \
-e SSHWIFTY_SHAREDKEY=1025 \
-e SSHWIFTY_READTIMEOUT=600 \
-e SSHWIFTY_WRITETIMEOUT=60 \
-e "SSHWIFTY_SERVERMESSAGE=[Welcome Sshwifty](https://sshwifty.xxx.tech/)" \
niruix/sshwifty:0.3.6-beta-release
```

## Wiki.js

本示例部署依赖:

1. MySQL，[MySQL 配置与操作](main/MySQL配置与操作.md)

```bash
sudo docker run -d \
--restart unless-stopped \
--name wikijs \
--link mysql8:mysql8 \
-p 46401:3000 \
-e "DB_TYPE=mysql" \
-e "DB_HOST=mysql8" \
-e "DB_PORT=3306" \
-e "DB_USER=wikijs" \
-e "DB_PASS=wikijs" \
-e "DB_NAME=wikijs" \
ghcr.io/requarks/wiki:2.5
```
