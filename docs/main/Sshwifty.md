# Docker

```bash
docker stop sshwifty
docker rm sshwifty

docker run -d \
--restart unless-stopped \
--name sshwifty \
-p 8182:8182 \
-e SSHWIFTY_SHAREDKEY=1025 \
-e SSHWIFTY_READTIMEOUT=600 \
-e SSHWIFTY_WRITETIMEOUT=60 \
-e "SSHWIFTY_SERVERMESSAGE=[Welcome Sshwifty](https://sshwifty.xxx.tech/)" \
niruix/sshwifty:0.3.6-beta-release
```
