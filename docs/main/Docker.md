# 通过 systemd 创建服务

systemd 可参见: [systemd 安装与使用](main/systemd_install_and_service_exmaple.md)

```bash
[Unit]
Description=Docker Application Container Engine
Documentation=https://docs.docker.com
After=network-online.target
Wants=network-online.target
# Requires=docker.socket

[Service]
Type=notify
ExecStart=/usr/bin/docker
ExecReload=/bin/kill -s HUP $MAINPID
TimeoutSec=0
RestartSec=2
Restart=always

[Install]
WantedBy=multi-user.target
```
