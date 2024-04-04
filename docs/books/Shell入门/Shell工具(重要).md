# Shell工具(重要)



## cut

剪切数据 常用选项-f、-d

```Plain&#x20;Text
用法：cut [选项]... [文件]...
Print selected parts of lines from each FILE to standard output.
如果没有指定文件，或者文件为"-"，则从标准输入读取。
  -b, --bytes=列表              只选中指定的这些字节
  -c, --characters=列表         只选中指定的这些字符
  -d, --delimiter=分界符        使用指定分界符代替制表符作为区域分界（默认为制表符）
  -f, --fields=LIST       select only these fields;  also print any line
                            that contains no delimiter character, unless
                            the -s option is specified
  -n                      with -b: don't split multibyte characters
      --complement              补全选中的字节、字符或域
  -s, --only-delimited          不打印没有包含分界符的行
      --output-delimiter=字符串 使用指定的字符串作为输出分界符，默认采用输入
                                的分界符
  -z, --zero-terminated    以 NUL 字符而非换行符作为行尾分隔符

```

```Plain&#x20;Text
[xpcheng@xpcheng test_shell]$ vim cut.txt
[xpcheng@xpcheng test_shell]$ cat cut.txt
guangdong guangzhou
gd shenzhen nanshan
guizhou guiyang huaxi
gz zunyi huonghg
sichuan chengduo
[xpcheng@xpcheng test_shell]$ cut -f 2,3 -d ' ' cut.txt
guangzhou
shenzhen nanshan
guiyang huaxi
zunyi huonghg
chengduo
[xpcheng@xpcheng test_shell]$ cut -f 1 -d ' ' cut.txt
guangdong
gd
guizhou
gz
sichuan

```

```Plain&#x20;Text
#运用管道符|，将前面的结果传给后面
[xpcheng@xpcheng test_shell]$ cat cut.txt
guangdong guangzhou
gd shenzhen nanshan
guizhou guiyang huaxi
gz zunyi huonghg
sichuan chengduo
[xpcheng@xpcheng test_shell]$ cat cut.txt | grep "gu" | cut -f 2 -d ' '
guangzhou
guiyang

```

```Plain&#x20;Text
#获取系统变量第二个":"后面的所有路径
[xpcheng@xpcheng test_shell]$ echo $PATH
/home/xpcheng/.local/bin:/home/xpcheng/bin:/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/opt/module/java/jdk1.8.0_291/bin:/opt/module/hadoop/hadoop-3.2.2/bin:/opt/module/hadoop/hadoop-3.2.2/sbin
#获取PATH，通过管道符传递，-f 2-表示获取第二列及后面所有列
[xpcheng@xpcheng test_shell]$ echo $PATH |cut -d ':' -f 2-
/home/xpcheng/bin:/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/opt/module/java/jdk1.8.0_291/bin:/opt/module/hadoop/hadoop-3.2.2/bin:/opt/module/hadoop/hadoop-3.2.2/sbin

```

```Plain&#x20;Text
#获取ip
[xpcheng@xpcheng test_shell]$ ifconfig
ens33: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.10.50  netmask 255.255.255.0  broadcast 192.168.10.255
        inet6 fe80::6c95:7396:fcd0:f4f7  prefixlen 64  scopeid 0x20<link>
        ether 00:0c:29:fa:b0:36  txqueuelen 1000  (Ethernet)
        RX packets 5269  bytes 539871 (527.2 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 2824  bytes 352693 (344.4 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

[xpcheng@xpcheng test_shell]$ ifconfig  ens33
ens33: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.10.50  netmask 255.255.255.0  broadcast 192.168.10.255
        inet6 fe80::6c95:7396:fcd0:f4f7  prefixlen 64  scopeid 0x20<link>
        ether 00:0c:29:fa:b0:36  txqueuelen 1000  (Ethernet)
        RX packets 5300  bytes 542781 (530.0 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 2844  bytes 355005 (346.6 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

[xpcheng@xpcheng test_shell]$ ifconfig  ens33 | grep 'inet '
        inet 192.168.10.50  netmask 255.255.255.0  broadcast 192.168.10.255
[xpcheng@xpcheng test_shell]$ ifconfig  ens33 | grep 'inet ' | cut -f 2 -d 't'
 192.168.10.50  ne
[xpcheng@xpcheng test_shell]$ ifconfig  ens33 | grep 'inet ' | cut -f 2 -d 't' | cut -f 2 -d ' '
192.168.10.50

```

## sed

一种流编辑器一次处理一行内容，处理时将当前的行存储在临时缓冲区中，称为“模式空间“。接着用sed命令处理缓冲区中的内容，处理完成后，把缓冲区的内容送往屏幕。接着处理下一行，这样不断重复，直到文件末尾。文件内容并没有改变，除非使用了重定向存储输出。

```Plain&#x20;Text
用法: sed [选项]... {脚本(如果没有其他脚本)} [输入文件]...

  -n, --quiet, --silent
                 取消自动打印模式空间
  -e 脚本, --expression=脚本
                 添加“脚本”到程序的运行列表
  -f 脚本文件, --file=脚本文件
                 添加“脚本文件”到程序的运行列表
  --follow-symlinks
                 直接修改文件时跟随软链接
  -i[SUFFIX], --in-place[=SUFFIX]
                 edit files in place (makes backup if SUFFIX supplied)
  -c, --copy
                 use copy instead of rename when shuffling files in
  -E, -r, --regexp-extended
                 use extended regular expressions in the script
                 (for portability use POSIX -E).
  -s, --separate
                 consider files as separate rather than as a single,
                 continuous long stream.
      --sandbox
                 operate in sandbox mode (disable e/r/w commands).
如果没有 -e, --expression, -f 或 --file 选项，那么第一个非选项参数被视为sed脚本。其他非选项参数被视为输入文件，如果没有输入文件，那么程序将从标准输入读取数据。

```

```Plain&#x20;Text
[xpcheng@xpcheng test_shell]$ cp cut.txt sed.txt
[xpcheng@xpcheng test_shell]$ ll
总用量 48
-rw-rw-r--. 1 xpcheng xpcheng 142 8月   1 01:38 batch.sh
-rw-rw-r--. 1 xpcheng xpcheng 122 8月   1 03:15 case.sh
-rw-rw-r--. 1 xpcheng xpcheng  96 8月   1 14:16 cut.txt
-rw-rw-r--. 1 xpcheng xpcheng  89 8月   1 13:07 for2.sh
-rw-rw-r--. 1 xpcheng xpcheng  68 8月   1 03:27 for.sh
-rw-rw-r--. 1 xpcheng xpcheng 147 8月   1 13:58 fun.sh
-rw-rw-r--. 1 xpcheng xpcheng  87 8月   1 03:05 if.sh
-rw-rw-r--. 1 xpcheng xpcheng  30 8月   1 01:38 multi_lines.sh
-rw-rw-r--. 1 xpcheng xpcheng   0 8月   1 02:52 parameter.sh
-rwxrwxrwx. 1 xpcheng xpcheng  52 8月   1 02:11 para.sh
-rw-rw-r--. 1 xpcheng xpcheng  68 8月   1 13:28 read.sh
-rw-rw-r--. 1 xpcheng xpcheng  96 8月   1 14:50 sed.txt
-rw-rw-r--. 1 xpcheng xpcheng  80 8月   1 13:20 while.sh
[xpcheng@xpcheng test_shell]$ cat sed.txt
guangdong guangzhou
gd shenzhen nanshan
guizhou guiyang huaxi
gz zunyi huonghg
sichuan chengduo

```

```Plain&#x20;Text
#在文件第二行增加打印"beijing hd"
[xpcheng@xpcheng test_shell]$ sed '2a beijing hd' sed.txt
guangdong guangzhou
gd shenzhen nanshan
beijing hd
guizhou guiyang huaxi
gz zunyi huonghg
sichuan chengduo
[xpcheng@xpcheng test_shell]$ cat sed.txt
guangdong guangzhou
gd shenzhen nanshan
guizhou guiyang huaxi
gz zunyi huonghg
sichuan chengduo

#删除包含"gd"的行，这里d应为在字符后面所以需要'/'来区分是命令
[xpcheng@xpcheng test_shell]$ sed '/gd/d' sed.txt
guizhou guiyang huaxi
gz zunyi huonghg

#替换"gd"为”gz"，g全局替换
[xpcheng@xpcheng test_shell]$ sed "s/gd/gz/g" sed.txt
guangzong guangzhou
gz shenzhen nanshan
guizhou guiyang huaxi
gz zunyi huonghg
sichuan chengzuo

#删除第二行并将"gd"为"gz"
[xpcheng@xpcheng test_shell]$ sed -e '2d' -e "s/gd/gz/g" sed.txt
guangzong guangzhou
guizhou guiyang huaxi
gz zunyi huonghg
sichuan chengzuo

```

## awk

文本分析工具把文件逐行读入，以空格作为默认分隔符将每行切片，切开的部分再进行分析处理。

```Plain&#x20;Text
用法：awk [选项] -f 脚本文件 [--] 文件 ...
用法：awk [选项] [--] '程序' 文件 ...
用法：awk [选项] 'pattern1{action1} pattern2{action2} ...' 文件
patterns正则表达式，action匹配后需要执行的代码
POSIX 选项：            GNU 长选项：(标准)
        -f 脚本文件             --file=脚本文件
        -F fs                   --field-separator=fs
        -v var=val              --assign=var=val

```

```Plain&#x20;Text
#筛选"gd"，并输出第一第二列
[xpcheng@xpcheng test_shell]$ awk -F " " '/gd/{print $1" "$2}' awk.txt
guangdong guangzhou
gd shenzhen
sichuan chengduo

#以上基础上，在输出前打印"user xpcheng"，在输出后条件"this is other"
[xpcheng@xpcheng test_shell]$ awk -F " " 'BEGIN{print "user xpcheng"} /gd/{print $1" "$2} END{print "This is other"}' awk.txt
user xpcheng
guangdong guangzhou
gd shenzhen
sichuan chengduo
This is other
#BEGIN所有数据读取行前执行，END所有数据读取行后执行

```

```Plain&#x20;Text
#将某一列数值+1后输出，这里要注意的是变量i在使用的时候不是$i，而是直接使用i
#如果使用的是$i，那么将是去对应的列
[xpcheng@xpcheng test_shell]$ awk -F : -v i=1 '{print $3+i}' /etc/passwd
1
2
3
4

[xpcheng@xpcheng test_shell]$ awk -F : '{print $3 $4}' /etc/passwd
00
11
22
34

[xpcheng@xpcheng test_shell]$ awk -F : -v i=3 '{print $i+$4}' /etc/passwd
0
2
4
7

```

```Plain&#x20;Text
#统计awk.txt每行的行号，域的个数
[xpcheng@xpcheng test_shell]$ cat awk.txt
guangdong guangzhou
gd shenzhen nanshan
guizhou guiyang huaxi
gz zunyi huonghg
sichuan chengduo
[xpcheng@xpcheng test_shell]$ awk -F " " '{print "filename:"FILENAME", linenumber:"NR",columns:"NF}' awk.txt
filename:awk.txt, linenumber:1,columns:2
filename:awk.txt, linenumber:2,columns:3
filename:awk.txt, linenumber:3,columns:3
filename:awk.txt, linenumber:4,columns:3
filename:awk.txt, linenumber:5,columns:2

```

## sort

将文件进行排序，并将排序结果标准输出

```Plain&#x20;Text
用法：sort [选项]... [文件]...
　或：sort [选项]... --files0-from=F
Write sorted concatenation of all FILE(s) to standard output.

如果没有指定文件，或者文件为"-"，则从标准输入读取。

必选参数对长短选项同时适用。
排序选项：

  -b, --ignore-leading-blanks   忽略前导的空白区域
  -d, --dictionary-order        只考虑空白区域和字母字符
  -f, --ignore-case             忽略字母大小写
  -h, --human-numeric-sort    使用易读性数字(例如： 2K 1G)
  -n, --numeric-sort          按照数值大小排序
  -r, --reverse               倒序
	-k, --key=KEYDEF            通过指定列排序
  -o, --output=文件             将结果写入到文件而非标准输出
  -t, --field-separator=分隔符  使用指定的分隔符代替非空格到空格的转换

```

