默认账号：`showdoc/showdoc`

启动后修改数据库的方式可以调整默认用户名

# Docker

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

# 如何升级

这里的升级是针对上面 docker 安装方式的升级。

如果你原来是采用非 docker 安装方式（如 php 安装方式）的话，请跳过本部分文字，直接去看下部分。

```bash

# 从原版官方库更新镜像。(中国大陆用户不建议直接使用原版镜像，可以用后面的加速镜像)
docker pull star7th/showdoc

# 中国大陆镜像更新命令（更新后记得执行docker tag命令以进行重命名）
docker pull registry.cn-shenzhen.aliyuncs.com/star7th/showdoc
docker tag registry.cn-shenzhen.aliyuncs.com/star7th/showdoc:latest star7th/showdoc:latest

##后续命令无论使用官方镜像还是加速镜像都需要执行


rm -rf  /showdoc_data/html_bak
#备份。如果可以的话，命令中的html_bak还可以加上日期后缀，以便保留不同日期的多个备份
mv /showdoc_data/html  /showdoc_data/html_bak

# 新建准备存放新版代码的目录
mkdir -p /showdoc_data/html
chmod  -R 777 /showdoc_data/html

# 删除旧容器
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

# 重新给权限
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

# 数据备份

备份/showdoc_data/html 目录即可。比如执行下面命令压缩存放

```bash
zip -r /showdoc_data/showdoc_bak.zip  /showdoc_data/html
# 其中showdoc_bak.zip可以用日期后缀命名，以便多个备份。
# 你也可以用定时任务来实现定时备份。
```
