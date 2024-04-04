# Shell脚本入门

## 1、脚本格式

脚本以`#!/bin/bash`开头（指定解析器）。

**Shell中引号有单引号和双引号之分**，二者的主要区别在于，被单引号括起来的字符都是普通字符，就算特殊字符也不再有特殊含义；而被双引号括起来的字符中，"$"、"\\"和反引号是拥有特殊含义的，"$"代表引用变量的值，而反引号代表引用命令。

## 2、第一个Shell脚本：输出helloword

```shellscript
[xpcheng@xpcheng ~]$ vim helloword
		#!/bin/bash
		echo 'helloword xpcheng'
[xpcheng@xpcheng ~]$ ll
总用量 4
-rw-rw-r--. 1 xpcheng xpcheng 37 8月   1 01:22 helloword
drwxrwxr-x. 2 xpcheng xpcheng 90 6月  28 15:17 software
[xpcheng@xpcheng ~]$ sh helloword
helloword xpcheng

[xpcheng@xpcheng ~]$ bash helloword
helloword xpcheng

[xpcheng@xpcheng ~]$ sh /home/xpcheng/helloword
helloword xpcheng

```

```shellscript
#但是以下命令执行失败
[xpcheng@xpcheng ~]$ ./helloword
-bash: ./helloword: 权限不够

#而该文件的权限如下
[xpcheng@xpcheng ~]$ ll
总用量 4
-rw-rw-r--. 1 xpcheng xpcheng 37 8月   1 01:22 helloword
drwxrwxr-x. 2 xpcheng xpcheng 90 6月  28 15:17 software

#并没有可执行权限，所以需要修改权限
[xpcheng@xpcheng ~]$ chmod 777 helloword
[xpcheng@xpcheng ~]$ ll
总用量 4
-rwxrwxrwx. 1 xpcheng xpcheng 37 8月   1 01:22 helloword
drwxrwxr-x. 2 xpcheng xpcheng 90 6月  28 15:17 software
[xpcheng@xpcheng ~]$ ./helloword
helloword xpcheng

```

调用sh helloword或者bash helloword与./helloword的本质： 前者是sh或者bash解析器来执行该文件，所以脚本本身不需要可执行权限；而后者是需要脚本自身执行脚本内容，所以需要可执行权限。

## 3、第二个Shell：多命令执行

创建一个文件，并在文件中添加任意字符串。

```shellscript
[xpcheng@xpcheng ~]$ cd test_shell/
[xpcheng@xpcheng test_shell]$ vim batch.sh
#这里>>是重定向，向至指定文件末尾追加内容
[xpcheng@xpcheng test_shell]$ cat batch.sh
#!/bin/bash
cd /home/xpcheng/test_shell/
touch multi_lines.sh
echo 'multiple lines' >> multi_lines.sh
echo '2 multip lines' >> multi_lines.sh
[xpcheng@xpcheng test_shell]$ bash batch.sh
[xpcheng@xpcheng test_shell]$ ll
总用量 8
-rw-rw-r--. 1 xpcheng xpcheng 142 8月   1 01:38 batch.sh
-rw-rw-r--. 1 xpcheng xpcheng  30 8月   1 01:38 multi_lines.sh
[xpcheng@xpcheng test_shell]$ cat multi_lines.sh
multiple lines
2 multip lines

```
