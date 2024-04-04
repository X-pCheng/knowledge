# Docker

```shellscript
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

# Nginx 服务器反向代理 MinIO 配置

https://www.minio.org.cn/docs/minio/linux/integrations/setup-nginx-proxy-with-minio.html
