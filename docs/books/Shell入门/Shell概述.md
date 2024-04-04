# Shell概述

Shell是一个命令行解释器，它接受应用程序/用户命令，然后调用操作系统内核。 Shell还是一种功能相当强大的编程语言，易编写，易测试，灵活性强。

```shellscript
#显示当前用到的脚本解释器，sh、bash常用，其他的还有nologin、dash、tcsh、csh、python等等
[xpcheng@xpcheng bin]$ cat /etc/shells
/bin/sh
/bin/bash
/usr/bin/sh
/usr/bin/bash

#sh最后其实也是调用bash
[xpcheng@xpcheng bin]$ cd /bin
[xpcheng@xpcheng bin]$ ll | grep 'bash'
-rwxr-xr-x. 1 root root      1150704 7月  22 2020 bash
lrwxrwxrwx. 1 root root           10 7月  22 2020 bashbug -> bashbug-64
-rwxr-xr-x. 1 root root         7348 7月  22 2020 bashbug-64
lrwxrwxrwx. 1 root root            4 7月  22 2020 sh -> bash

#查看默认shell解释器
[xpcheng@xpcheng bin]$ echo $SHELL
/bin/bash
```

