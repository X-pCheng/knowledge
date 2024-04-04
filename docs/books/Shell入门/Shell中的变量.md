# Shell中的变量

## 1、常用系统变量

```Plain&#x20;Text
#当前用户
[xpcheng@xpcheng test_shell]$ echo $USER
xpcheng
#当前用户home目录
[xpcheng@xpcheng test_shell]$ echo $HOME
/home/xpcheng
#当前路径
[xpcheng@xpcheng test_shell]$ echo $PWD
/home/xpcheng/test_shell
#当前Shell使用的解析器
[xpcheng@xpcheng test_shell]$ echo $SHELL
/bin/bash

```

## 2、自定义变量

```Plain&#x20;Text
#定义变量：变量=值，中间不能有空格
[xpcheng@xpcheng test_shell]$ a = 1
-bash: a: 未找到命令
[xpcheng@xpcheng test_shell]$ a =1
-bash: a: 未找到命令
[xpcheng@xpcheng test_shell]$ a= 1
-bash: 1: 未找到命令
[xpcheng@xpcheng test_shell]$ a=1
[xpcheng@xpcheng test_shell]$ echo $a
1

#撤销变量
[xpcheng@xpcheng test_shell]$ unset a
[xpcheng@xpcheng test_shell]$ echo $a

[xpcheng@xpcheng test_shell]$

#声明静态变量：readonly 变量=值，只可使用unset
[xpcheng@xpcheng test_shell]$ readonly a=1
[xpcheng@xpcheng test_shell]$ echo $a
1
[xpcheng@xpcheng test_shell]$ unset a
-bash: unset: a: 无法取消设定: 只读 variable
[xpcheng@xpcheng test_shell]$ echo $a
1
[xpcheng@xpcheng test_shell]$

#把变量提升为全局变量，可供其他程序使用：export 变量

```



## 3、变量定义规则

* 可由数字、字母、下划线组成，不能以数字开头，环境变量建议大写
* 等号两侧不能有空格
* 在bash中变量默认都是字符串，无法直接进行数值运算



```Plain&#x20;Text
[xpcheng@xpcheng test_shell]$ b=1+1
[xpcheng@xpcheng test_shell]$ echo $b
1+1
```

* 变量的值如果有空格，需要使用双引号或单引号括起来

```Plain&#x20;Text
[xpcheng@xpcheng test_shell]$ b=i love shell
-bash: love: 未找到命令
[xpcheng@xpcheng test_shell]$ b='i love shell'
[xpcheng@xpcheng test_shell]$ echo $b
i love shell
```

## 4、特殊变量

* $n
  n为数字，$0代表脚本名称，$1-$9代表第1个到第9个参数，10个及以上的参数必须用${xx}表示，如${10},${15}

```Plain&#x20;Text
[xpcheng@xpcheng test_shell]$ vim para.sh
[xpcheng@xpcheng test_shell]$ cat para.sh
#!/bin/bash
echo "$0 $1 $2"
[xpcheng@xpcheng test_shell]$ bash para.sh /home my_name
para.sh /home my_name
[xpcheng@xpcheng test_shell]$ bash para.sh
para.sh
[xpcheng@xpcheng test_shell]$ bash para.sh /home my_name good
para.sh /home my_name

```

* $#
  获取所有输入参数的个数，常用于循环
  $\*与$@，常用于流程控制，详见流程控制部分

```Plain&#x20;Text
[xpcheng@xpcheng test_shell]$ vim para.sh
[xpcheng@xpcheng test_shell]$ cat para.sh
#!/bin/bash
echo "$0 $1 $2"
echo $#
[xpcheng@xpcheng test_shell]$ chmod 777 para.sh
[xpcheng@xpcheng test_shell]$ ./para.sh /home/ para1 para2 para3
./para.sh /home/ para1
4

```

* $\*
  代表命令行中所有的参数，并将这些参数看作一个整体 $@：代表命令行中所有的参数，当将这些参数看作独立个体

```Plain&#x20;Text
[xpcheng@xpcheng test_shell]$ vim para.sh
[xpcheng@xpcheng test_shell]$ cat para.sh
#!/bin/bash
echo "$0 $1 $2"
echo $#
echo $*
echo $@
[xpcheng@xpcheng test_shell]$ ./para.sh /home/ para1 para2 para3
./para.sh /home/ para1
4
/home/ para1 para2 para3
/home/ para1 para2 para3

```

* $?
  最后一次命令执行的返回状态。
  0    正确执行
  非0    执行出错。具体值由命令自己决定

```Plain&#x20;Text
#正确执行
[xpcheng@xpcheng test_shell]$ ./para.sh
./para.sh
0

[xpcheng@xpcheng test_shell]$ echo $?
0
#执行出错
[xpcheng@xpcheng test_shell]$ xxx
-bash: xxx: 未找到命令
[xpcheng@xpcheng test_shell]$ echo $?
127
```
