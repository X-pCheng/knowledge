# 默认 Dockerfile

## 构建镜像并运行

使用 [Etherpad 默认 Dockerfile](application/etherpad/Etherpad默认Dockerfile.md)
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

## 拷贝资源到宿主机

```bash
docker cp etherpad:/opt/etherpad-lite/settings.json /home/xpcheng/docker-data/etherpad/settings.json
docker cp etherpad:/opt/etherpad-lite/APIKEY.txt /home/xpcheng/docker-data/etherpad/config/APIKEY.txt
```

- settings.json：Etherpad 的配置文件
- APIKEY.txt：调用接口需要的 KEY，挂载宿主机可以固定下来，否则每次重建都会变

## 准备数据库

使用 MySQL 来存储数据，MySQL 数据库相关操作参见 [MySQL 配置与操作](application/MySQL配置与操作.md)
修改`settings.json`中关于数据库的配置(db 开头的参数)

## 重建容器并启动

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

# 自定义插件构建

默认的 Dockerfile 不含其他插件，但是我们可以[Etherpad 自定义 Dockerfile](application/etherpad/Etherpad自定义Dockerfile.md)

同时调整配置文件`settings.json`关于使用到的插件的的配置，一个示例： [Etherpad 自定义配置](application/etherpad/Etherpad自定义配置.md)

## 修改 NPM 源

为了构建镜像时使用 npm，可以把 npm 源切换为国内的，避免网络因素导致构建失败

```bash
# npm修改镜像地址：
# 1、查看一下当前源
npm config get registry
# 2、切换为淘宝源
npm config set registry https://registry.npm.taobao.org/
# 3、还原仓库地址
npm config set registry https://registry.npmjs.org/
# 4、删除会恢复默认镜像
npm config delete registry
```

## 构建自定义镜像并运行

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
