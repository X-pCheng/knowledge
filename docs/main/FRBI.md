# 通过systemd创建服务
systemd可参见: [systemd 安装与使用](main/systemd_install_and_service_exmaple.md)
```bash
[Unit]
# 服务名称，可自定义
Description = finebi server
After = network.target syslog.target
Wants = network.target

[Service]
Type = forking
# 启动服务的命令，需修改为您的安装路径
ExecStart = /home/xpcheng/tomcat-linux-x64/bin/startup.sh

[Install]
WantedBy = multi-user.target

```
重加载服务
```bash
systemctl daemon-reload
```