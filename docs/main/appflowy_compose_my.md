```docker
# Essential services for AppFlowy Cloud

version: '3'
services:
  # nginx:
  #   container_name: appflowy_nginx
  #   restart: on-failure
  #   image: nginx
  #   ports:
  #     - 80:80   # Disable this if you are using TLS
  #     - 443:443
  #   volumes:
  #     - ./nginx/nginx.conf:/etc/nginx/nginx.conf
  #     - ./nginx/ssl/certificate.crt:/etc/nginx/ssl/certificate.crt
  #     - ./nginx/ssl/private_key.key:/etc/nginx/ssl/private_key.key

  gotrue:
    image: appflowyinc/gotrue:${GOTRUE_VERSION:-latest}
    container_name: gotrue
    build:
      context: .
      dockerfile: docker/gotrue.Dockerfile
    network_mode: bridge
    external_links:
      - postgres12
    restart: on-failure
    ports:
    #   - 46427:3000 
      - 46428:9999 # API 调用
    environment:
      # There are a lot of options to configure GoTrue. You can reference the example config:
      # https://github.com/supabase/gotrue/blob/master/example.env
      - GOTRUE_SITE_URL=${GOTRUE_SITE_URL}                           # redirected to AppFlowy application
      - URI_ALLOW_LIST=*                                              # adjust restrict if necessary
      - GOTRUE_JWT_SECRET=${GOTRUE_JWT_SECRET}                        # authentication secret
      - GOTRUE_JWT_EXP=${GOTRUE_JWT_EXP}
      - GOTRUE_DB_DRIVER=postgres
      - API_EXTERNAL_URL=${API_EXTERNAL_URL}
      - DATABASE_URL=${GOTRUE_DATABASE_URL}
      - PORT=9999
      - GOTRUE_SMTP_HOST=${GOTRUE_SMTP_HOST}                          # e.g. smtp.gmail.com
      - GOTRUE_SMTP_PORT=${GOTRUE_SMTP_PORT}                          # e.g. 465
      - GOTRUE_SMTP_USER=${GOTRUE_SMTP_USER}                          # email sender, e.g. noreply@appflowy.io
      - GOTRUE_SMTP_PASS=${GOTRUE_SMTP_PASS}                          # email password
      - GOTRUE_MAILER_URLPATHS_CONFIRMATION=/gotrue/verify
      - GOTRUE_MAILER_URLPATHS_INVITE=/gotrue/verify
      - GOTRUE_MAILER_URLPATHS_RECOVERY=/gotrue/verify
      - GOTRUE_MAILER_URLPATHS_EMAIL_CHANGE=/gotrue/verify
      - GOTRUE_SMTP_ADMIN_EMAIL=${GOTRUE_SMTP_ADMIN_EMAIL}                # email with admin privileges e.g. internal@appflowy.io
      - GOTRUE_SMTP_MAX_FREQUENCY=${GOTRUE_SMTP_MAX_FREQUENCY:-1ns}       # set to 1ns for running tests
      - GOTRUE_RATE_LIMIT_EMAIL_SENT=${GOTRUE_RATE_LIMIT_EMAIL_SENT:-100} # number of email sendable per minute
      - GOTRUE_MAILER_AUTOCONFIRM=${GOTRUE_MAILER_AUTOCONFIRM:-false}     # change this to true to skip email confirmation
      # Google OAuth config
      - GOTRUE_EXTERNAL_GOOGLE_ENABLED=${GOTRUE_EXTERNAL_GOOGLE_ENABLED}
      - GOTRUE_EXTERNAL_GOOGLE_CLIENT_ID=${GOTRUE_EXTERNAL_GOOGLE_CLIENT_ID}
      - GOTRUE_EXTERNAL_GOOGLE_SECRET=${GOTRUE_EXTERNAL_GOOGLE_SECRET}
      - GOTRUE_EXTERNAL_GOOGLE_REDIRECT_URI=${GOTRUE_EXTERNAL_GOOGLE_REDIRECT_URI}
      # GITHUB OAuth config
      - GOTRUE_EXTERNAL_GITHUB_ENABLED=${GOTRUE_EXTERNAL_GITHUB_ENABLED}
      - GOTRUE_EXTERNAL_GITHUB_CLIENT_ID=${GOTRUE_EXTERNAL_GITHUB_CLIENT_ID}
      - GOTRUE_EXTERNAL_GITHUB_SECRET=${GOTRUE_EXTERNAL_GITHUB_SECRET}
      - GOTRUE_EXTERNAL_GITHUB_REDIRECT_URI=${GOTRUE_EXTERNAL_GITHUB_REDIRECT_URI}
      # Discord OAuth config
      - GOTRUE_EXTERNAL_DISCORD_ENABLED=${GOTRUE_EXTERNAL_DISCORD_ENABLED}
      - GOTRUE_EXTERNAL_DISCORD_CLIENT_ID=${GOTRUE_EXTERNAL_DISCORD_CLIENT_ID}
      - GOTRUE_EXTERNAL_DISCORD_SECRET=${GOTRUE_EXTERNAL_DISCORD_SECRET}
      - GOTRUE_EXTERNAL_DISCORD_REDIRECT_URI=${GOTRUE_EXTERNAL_DISCORD_REDIRECT_URI}

  appflowy_cloud:
    image: appflowyinc/appflowy_cloud:${BACKEND_VERSION:-latest}
    container_name: appflowy_cloud
    build:
      context: .
      dockerfile: Dockerfile
      args:
        FEATURES: ""
    depends_on: 
      - gotrue
    links: 
      - gotrue
    network_mode: bridge
    external_links:
      - postgres12
      - redis
      - minio
    restart: on-failure
    ports:
      - 46429:8000
    environment:
      - RUST_LOG=${RUST_LOG:-info}
      - APPFLOWY_ENVIRONMENT=production
      - APPFLOWY_DATABASE_NAME=${APPFLOWY_DATABASE_NAME}
      - APPFLOWY_DATABASE_URL=${APPFLOWY_DATABASE_URL}
      - APPFLOWY_REDIS_URI=${APPFLOWY_REDIS_URI}
      - APPFLOWY_GOTRUE_JWT_SECRET=${GOTRUE_JWT_SECRET}
      - APPFLOWY_GOTRUE_JWT_EXP=${GOTRUE_JWT_EXP}
      - APPFLOWY_GOTRUE_BASE_URL=${APPFLOWY_GOTRUE_BASE_URL}
      - APPFLOWY_GOTRUE_EXT_URL=${API_EXTERNAL_URL}
      - APPFLOWY_GOTRUE_ADMIN_EMAIL=${GOTRUE_ADMIN_EMAIL}
      - APPFLOWY_GOTRUE_ADMIN_PASSWORD=${GOTRUE_ADMIN_PASSWORD}
      - APPFLOWY_S3_USE_MINIO=${APPFLOWY_S3_USE_MINIO}
      - APPFLOWY_S3_MINIO_URL=${APPFLOWY_S3_MINIO_URL}
      - APPFLOWY_S3_ACCESS_KEY=${APPFLOWY_S3_ACCESS_KEY}
      - APPFLOWY_S3_SECRET_KEY=${APPFLOWY_S3_SECRET_KEY}
      - APPFLOWY_S3_BUCKET=${APPFLOWY_S3_BUCKET}
      - APPFLOWY_S3_REGION=${APPFLOWY_S3_REGION}
      - APPFLOWY_AI_URL=${APPFLOWY_AI_URL}
      - APPFLOWY_ACCESS_CONTROL=${APPFLOWY_ACCESS_CONTROL}

  admin_frontend:
    image: appflowyinc/admin_frontend:${BACKEND_VERSION:-latest}
    container_name: admin_frontend
    network_mode: bridge
    depends_on: 
      - gotrue
      - appflowy_cloud
    links: 
      - gotrue
      - appflowy_cloud
    external_links:
      - redis
    build:
      context: .
      dockerfile: ./admin_frontend/Dockerfile
    restart: on-failure
    ports:
      - 46430:3000
    environment:
      - RUST_LOG=${RUST_LOG:-info}
      - ADMIN_FRONTEND_REDIS_URL=${ADMIN_FRONTEND_REDIS_URL:-redis://redis:6379}
      - ADMIN_FRONTEND_GOTRUE_URL=${ADMIN_FRONTEND_GOTRUE_URL:-http://gotrue:9999}
      - ADMIN_FRONTEND_APPFLOWY_CLOUD_URL=${ADMIN_FRONTEND_APPFLOWY_CLOUD_URL:-http://appflowy_cloud:8000}
      - ADMIN_FRONTEND_APPFLOWY_CLOUD_GATEWAY_URL=${ADMIN_FRONTEND_APPFLOWY_CLOUD_GATEWAY_URL}

#  collab_history:
#    restart: on-failure
#    build:
#      context: .
#      dockerfile: ./services/collab-history/Dockerfile
#    image: appflowyinc/collab_history:${COLLAB_HISTORY_VERSION:-latest}
#    environment:
#      - RUST_LOG=${RUST_LOG:-info}
#      - COLLAB_HISTORY_REDIS_URL=${COLLAB_HISTORY_REDIS_URL:-redis://redis:6379}
# volumes:
#   postgres_data:
#   minio_data:

```