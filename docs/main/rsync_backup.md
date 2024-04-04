# rsync 备份

通过 rsync 实现备份任务，cron 进行定时调度，最终实现 Linux 服务器之间的备份。

本例以 NAS 定时从其他 Linux 服务同步文件与数据到 NAS 服务器备份为例进行操作说明。

# 准备工具

1. rsync
2. ssh
3. 威联通 NAS 和一台普通 Linux（debian 或 centos）

# NAS 创建密钥

为了后面的 ssh 免密登录做准备，而且要创建`cron`执行用户的密钥

```bash
[xpcheng@nas464 ~]$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/share/homes/xpcheng/.ssh/id_rsa):
/share/homes/xpcheng/.ssh/id_rsa already exists.
Overwrite (y/n)? y
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /share/homes/xpcheng/.ssh/id_rsa.
Your public key has been saved in /share/homes/xpcheng/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:N08j43oUAakKbYV9gUkt24CtsGbZ87+uGIypBW1ktLw xpcheng@nas464
The key's randomart image is:
...此处省略
```

# Alinode1 创建密钥

Alinode1 是阿里云服务器，类 Centos 系统，创建密钥的过程和 NAS 一样

> 理论上，NAS 远程去拉取数据备份过来，所以这一步不是必要的

# 配置 ssh 免密访问(NAS->Alinode1)

在 alinode1 服务器中添加前面 NAS 服务器对应用户的公钥，网上有很多教程，不赘述了

# 编写 rsync 脚本

这个是一个脚本示例，请根据自身环境做调整。

[rsync 示例](main/rsync_backup_example.md)

# cron 配置调度

nas 中为 rsync 添加定时任务执行，定时同步其他服务器数据到 nas

由于 nas 为威联通的 QTS 系统，cron 配置方式见

```bash
* */2 * * * /share/homes/xpcheng/backup_remote/backup_remote.sh
```

# 重启 crontab

```Plain Text
sudo crontab /etc/config/crontab
sudo /etc/init.d/crond.sh restart
```
