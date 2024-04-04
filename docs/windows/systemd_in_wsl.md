# WSL 中的 systemd

适用于 Linux 的 Windows 子系统 (WSL) 现在支持 systemd，它是许多常用的 Linux 发行版（例如 Ubuntu、Debian 等）使用的 init 系统和服务管理器。 （[什么是 systemd？](https://learn.microsoft.com/zh-cn/windows/wsl/systemd#what-is-systemd-in-linux)）。

init 系统默认值最近已不再是 SystemV，Systemd 现在是使用[`wsl --install`](https://learn.microsoft.com/zh-cn/windows/wsl/install)[ 命令](https://learn.microsoft.com/zh-cn/windows/wsl/install)默认值安装的[Ubuntu 的当前版本默认值](https://canonical.com/blog/ubuntu-desktop-23-04-release-roundup#:%7E:text=Systemd%20becomes%20the%20default%20for%20Ubuntu%20on%20WSL)。 除当前版本的 Ubuntu 以外的 Linux 发行版仍然可能使用 WSL init，它类似于 SystemV init。 若要更改为 SystemD，请参阅[如何启用 systemd](https://learn.microsoft.com/zh-cn/windows/wsl/systemd#how-to-enable-systemd)。

# Linux 中的 systemd 是指什么？

根据[systemd.io](https://systemd.io/)：“systemd 是 Linux 系统的基本构建基块套件。 它提供一个系统和服务管理器，该管理器作为 PID 1 运行并启动系统的其余部分。”

Systemd 主要是一个 init 系统和服务管理器，它包括按需启动守护程序、装载和自动装载点维护、快照支持以及使用 Linux 控制组进行跟踪等功能。

大多数主要的 Linux 发行版现在都运行 systemd，因此在 WSL 上启用它可使体验更接近于使用裸机 Linux。 请参阅[带 systemd 演示的视频公告](https://learn.microsoft.com/zh-cn/windows/wsl/systemd#systemd-demo-video)或下面的[systemd 使用示例](https://learn.microsoft.com/zh-cn/windows/wsl/systemd#systemd-examples)，详细了解 systemd 提供的功能。

# 如何启用 systemd？

Systemd 现在是将使用[`wsl --install`](https://learn.microsoft.com/zh-cn/windows/wsl/install)[ 命令](https://learn.microsoft.com/zh-cn/windows/wsl/install)默认值安装的[Ubuntu 的当前版本默认值](https://canonical.com/blog/ubuntu-desktop-23-04-release-roundup#:%7E:text=Systemd%20becomes%20the%20default%20for%20Ubuntu%20on%20WSL)。

若要为 WSL 2 上运行的任何其他 Linux 发行版启用 systemd（更改默认值，使其不再使用 systemv init）：

确保 WSL 版本为 0.67.6 或更高版本。 （若要检查，请运行`wsl --version`。若要更新，请运行`wsl --update`或[从 Microsoft Store 下载最新版本](https://aka.ms/wslstorepage)。）

打开 Linux 发行版的命令行并输入`cd /`来访问根目录，然后输入`ls`来列出文件。 你将看到一个名为“etc”的目录，其中包含发行版的 WSL 配置文件。 打开此文件，以便可通过输入`nano /etc/wsl.conf`，使用 Nano 文本编辑器进行更新。

在`wsl.conf`文件中添加以下行，你现在已打开此文件来更改用于 systemd 的 init：

```Plain Text
[boot]
systemd=true
```

退出 Nano 文本编辑器（Ctrl + X，选择 Y 来保存更改）。

然后，需要关闭 Linux 发行版。

可以使用 PowerShell 中的`wsl.exe --shutdown`命令重启所有 WSL 实例。
