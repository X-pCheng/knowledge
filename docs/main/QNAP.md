# 威联通 cron 定时任务

在威联通的 QTS 系统中，用户自定义的定时任务要写到`/etc/config/crontab`才能持久化。

下面是正确的做法：

1. 使用 vim 编辑`/etc/config/crontab`
2. 将定时任务增加在最下面，保存并退出

```bash
*/5 * * * * /share/documents/scripts/sh/check_and_resatrt.sh >> /share/documents/logs/cfd.log 2>&1
0/5 * * * * docker exec -d --user www-data nextcloud php -f /var/www/html/cron.php
```

3. 重启 crontab

```bash
sudo crontab /etc/config/crontab
sudo /etc/init.d/crond.sh restart
```

4. 等待重启完成，定时任务就添加完成了

# TS-464/TS-664 drawPic process high CPU fix

[**TS-464/TS-664 drawPic process high CPU fix**](https://forum.qnap.com/viewtopic.php?p=843894&sid=e1dc0a312eb6aaecd1e8e8cd5ea8f8ea#p843894)

- [**Quote**](https://forum.qnap.com/posting.php?mode=quote&p=843894&sid=e1dc0a312eb6aaecd1e8e8cd5ea8f8ea)

[Post](https://forum.qnap.com/viewtopic.php?p=843894&sid=e1dc0a312eb6aaecd1e8e8cd5ea8f8ea#p843894) by [**brazenlash**](https://forum.qnap.com/memberlist.php?mode=viewprofile&u=359849&sid=e1dc0a312eb6aaecd1e8e8cd5ea8f8ea)[ ](https://forum.qnap.com/memberlist.php?mode=viewprofile&u=359849&sid=e1dc0a312eb6aaecd1e8e8cd5ea8f8ea)» Sat Apr 22, 2023 6:01 am

Greetings all,

Passing along a resolution to those who are seeing the drawPic process under System Processes constantly consume 15-20% CPU on the TS-X64 series models. This is apparently a process without a home for the local HDMI out which causes the issue, so may also apply to other models with an HDMI port.

The fix is simple. Open up AppCenter, navigate to HybridDeskStation on the left, and install HybridDesk Station. Once installed, you should see drawPic go away and your CPU usage to go down to normal levels. If you don't see CPU usage normalize, then also install FileStation HD (local display) and that should take care of it.

Cheers.
