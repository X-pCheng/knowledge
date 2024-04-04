# Docker

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
