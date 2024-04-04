```ini
# This file is a template for docker compose deployment
# Copy this file to .env and change the values as needed

# AppFlowy Cloud
## URL that connects to the gotrue docker container
APPFLOWY_GOTRUE_BASE_URL=http://gotrue:9999
## URL that connects to the postgres docker container
# APPFLOWY_DATABASE_URL=postgres://postgres:password@postgres:5432/postgres
APPFLOWY_ACCESS_CONTROL=true


# admin frontend
## URL that connects to redis docker container
ADMIN_FRONTEND_REDIS_URL=redis://redis:6379
## URL that connects to gotrue docker container
ADMIN_FRONTEND_GOTRUE_URL=http://gotrue:9999
# ADMIN_FRONTEND_GOTRUE_URL=https://gotrue.xpcheng.tech
ADMIN_FRONTEND_APPFLOWY_CLOUD_URL=http://appflowy_cloud:8000
# ADMIN_FRONTEND_APPFLOWY_CLOUD_URL=https://appflowy.xpcheng.tech

# ADMIN_FRONTEND_APPFLOWY_CLOUD_GATEWAY_URL=http://appflowy_cloud:8100

# gotrue
# authentication key, change this and keep the key safe and secret
# self defined key, you can use any string
GOTRUE_JWT_SECRET=xx
# Expiration time in seconds for the JWT token
GOTRUE_JWT_EXP=7200

# User sign up will automatically be confirmed if this is set to true.
# If you have OAuth2 set up or smtp configured, you can set this to false
# to enforce email confirmation or OAuth2 login instead.
# If you set this to false, you need to either set up SMTP
GOTRUE_MAILER_AUTOCONFIRM=false
# Number of emails that can be per minute
GOTRUE_RATE_LIMIT_EMAIL_SENT=100

# If you intend to use mail confirmation, you need to set the SMTP configuration below
# You would then need to set GOTRUE_MAILER_AUTOCONFIRM=false
# Check for logs in gotrue service if there are any issues with email confirmation
GOTRUE_SMTP_HOST=smtp.163.com
GOTRUE_SMTP_PORT=465
GOTRUE_SMTP_USER=xx@163.com
GOTRUE_SMTP_PASS=xx
GOTRUE_SMTP_ADMIN_EMAIL=xx@163.com

# This user will be created when AppFlowy Cloud starts successfully
# You can use this user to login to the admin panel
GOTRUE_ADMIN_EMAIL=xx@163.com
GOTRUE_ADMIN_PASSWORD=xx

# User will be redirected to this after Email or OAuth login
# Change this to your own domain where you host the docker-compose or gotrue
# If you are using a different domain, you need to change the redirect_uri in the OAuth2 configuration
# Make sure that this domain is accessible to the user
GOTRUE_SITE_URL=http://gotrue:3000

API_EXTERNAL_URL=http://gotrue:9999

# In docker environment, `postgres` is the hostname of the postgres service
# GoTrue connect to postgres using this url
# GOTRUE_DATABASE_URL=postgres://supabase_auth_admin:root@postgres:5432/postgres


# Refer to this for details: https://github.com/AppFlowy-IO/AppFlowy-Cloud/blob/main/doc/AUTHENTICATION.md
# Google OAuth2
GOTRUE_EXTERNAL_GOOGLE_ENABLED=false
GOTRUE_EXTERNAL_GOOGLE_CLIENT_ID=
GOTRUE_EXTERNAL_GOOGLE_SECRET=
GOTRUE_EXTERNAL_GOOGLE_REDIRECT_URI=http://gotrue:9999/callback
# GitHub OAuth2
GOTRUE_EXTERNAL_GITHUB_ENABLED=true
GOTRUE_EXTERNAL_GITHUB_CLIENT_ID=xx
GOTRUE_EXTERNAL_GITHUB_SECRET=xx
GOTRUE_EXTERNAL_GITHUB_REDIRECT_URI=http://gotrue:9999/callback
# Discord OAuth2
GOTRUE_EXTERNAL_DISCORD_ENABLED=false
GOTRUE_EXTERNAL_DISCORD_CLIENT_ID=
GOTRUE_EXTERNAL_DISCORD_SECRET=
GOTRUE_EXTERNAL_DISCORD_REDIRECT_URI=http://gotrue:9999/callback

# File Storage
# This is where storage like images, files, etc. will be stored
# By default, Minio is used as the default file storage which uses host's file system
APPFLOWY_S3_USE_MINIO=true
APPFLOWY_S3_MINIO_URL=http://minio:9000 # change this if you are using a different address for minio
APPFLOWY_S3_ACCESS_KEY=appflowy
APPFLOWY_S3_SECRET_KEY=appflowy
APPFLOWY_S3_BUCKET=appflowy
#APPFLOWY_S3_REGION=us-east-1


# Log level for the appflowy-cloud service
RUST_LOG=info
# RUST_LOG=debug


# PgAdmin
# Optional module to manage the postgres database
# You can access the pgadmin at http://your-host/pgadmin
# Refer to the APPFLOWY_DATABASE_URL for password when connecting to the database
PGADMIN_DEFAULT_EMAIL=admin@example.com
PGADMIN_DEFAULT_PASSWORD=xx

# Portainer (username: admin)
PORTAINER_PASSWORD=xx

# Cloudflare tunnel token
# CLOUDFLARE_TUNNEL_TOKEN=

# If you are using a different postgres database, change the following values
APPFLOWY_DATABASE_NAME=appflowy
APPFLOWY_DATABASE_URL=postgres://appflowy:appflowy@postgres12:5432/appflowy
GOTRUE_DATABASE_URL=postgres://supabase_auth_admin:root@postgres12:5432/appflowy

# AppFlowy AI
OPENAI_API_KEY=xx

# redis 
APPFLOWY_REDIS_URI=redis://redis:6379

# Collaboration History
COLLAB_HISTORY_REDIS_URL=redis://redis:6379
```