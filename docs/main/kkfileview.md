# Docker

```bash
sudo docker stop kkfileview
sudo docker rm kkfileview

sudo docker run -d \
--restart unless-stopped \
--name kkfileview \
-p 33082:8012 \
keking/kkfileview:4.1.0

```
