# trilium-cn

## 拉取镜像

```bash
docker pull nriver/trilium-cn:latest
```

## 无挂载启动

```bash
docker stop trilium
docker rm trilium

docker run -d \
--restart unless-stopped \
--name trilium \
--user=root \
-p 46410:8080 \
-e TZ=Asia/Shanghai \
nriver/trilium-cn:latest
```

## 拷贝数据与配置

```bash
docker cp trilium:/root/trilium-data/ /share/Container/trilium/
mv /share/Container/trilium/trilium-data /share/Container/trilium/data

```

默认端口是 8080，如果要自定义端口，修改默认配置文件 config.ini 中的端口为自定义端口（可选）

## 重建容器并启动

```bash
docker stop trilium
docker rm trilium

docker run -d \
--restart unless-stopped \
--name trilium \
--user=root \
-p 46410:8888 \
-v /share/Container/trilium/data:/root/trilium-data \
-e TZ=Asia/Shanghai \
nriver/trilium-cn:latest
```

# trilium-en

## 拉取镜像

```bash
docker pull zadam/trilium:latest
```

## 无挂载启动

```bash
docker stop trilium
docker rm trilium

docker run -d \
--restart unless-stopped \
--name trilium \
--user=root \
-p 46410:8080 \
-e TZ=Asia/Shanghai \
zadam/trilium:latest
```

## 拷贝数据与配置

```bash
docker cp trilium:/home/node/trilium-data/ /share/Container/trilium/
mv /share/Container/trilium/trilium-data /share/Container/trilium/data
```

默认端口是 8080，如果要自定义端口，修改默认配置文件 config.ini 中的端口为自定义端口（可选）

## 重建容器并启动

```bash
docker stop trilium
docker rm trilium

docker run -d \
--restart unless-stopped \
--name trilium \
--user=root \
-p 46410:8888 \
-v /share/Container/trilium/data:/home/node/trilium-data \
-e TZ=Asia/Shanghai \
zadam/trilium:latest
```
