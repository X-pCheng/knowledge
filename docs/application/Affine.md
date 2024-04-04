# Affine

参考 [Run AFFiNE with Custom Options](https://docs.affine.pro/docs/self-host-affine/run-affine-with-custom-options)

# Docker

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
