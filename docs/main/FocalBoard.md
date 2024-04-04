# Docker

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
