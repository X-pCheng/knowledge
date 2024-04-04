# systemd安装与使用

此示例演示如何在 Linux 系统下使用 systemd 来管理 frps 服务，包括启动、停止、配置后台运行和设置开机自启动。

在 Linux 系统下，使用`systemd`可以方便地控制 frps 服务端的启动、停止、配置后台运行以及开机自启动。

# 安装 systemd

如果您的 Linux 服务器上尚未安装 systemd，可以使用包管理器如`yum`（适用于 CentOS/RHEL）或`apt`（适用于 Debian/Ubuntu）来安装它：

```bash
# 使用 yum 安装 systemd（CentOS/RHEL）
yum install systemd

# 使用 apt 安装 systemd（Debian/Ubuntu）
apt install systemd
```

# 创建 service 文件

使用文本编辑器 (如 vim) 在`/etc/systemd/system`目录下创建一个`frps.service`文件，用于配置 frps 服务。

```bash
$ sudo vim /etc/systemd/system/frps.service
```

写入内容

```bash
[Unit]
# 服务名称，可自定义
Description = frp server
After = network.target syslog.target
Wants = network.target

[Service]
Type = simple
# 启动服务的命令，需修改为自己的程序安装路径
ExecStart = /path/to/frps -c /path/to/frps.toml

[Install]
WantedBy = multi-user.target
```

重加载服务

```bash
systemctl daemon-reload
```



# 使用 systemd 管理服务
[systemd基本命令](linux/systemd_command.md)
 

# 设置开机自启动

```Plain&#x20;Text
sudo systemctl enable frps
```

