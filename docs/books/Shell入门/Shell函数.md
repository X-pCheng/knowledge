# Shell函数



## 1、系统函数

* basename 获取文件名
  ```Plain&#x20;Text
  用法：basename 名称 [后缀]
  　或：basename 选项
  Print NAME with any leading directory components removed.
  If specified, also remove a trailing SUFFIX.

  必选参数对长短选项同时适用。
    -a, --multiple       support multiple arguments and treat each as a NAME
    -s, --suffix=SUFFIX  remove a trailing SUFFIX; implies -a
    -z, --zero           end each output line with NUL, not newline
        --help            显示此帮助信息并退出
        --version         显示版本信息并退出

  Examples:
    basename /usr/bin/sort          -> "sort"
    basename include/stdio.h .h     -> "stdio"
    basename -s .h include/stdio.h  -> "stdio"
    basename -a any/str1 any/str2   -> "str1" followed by "str2"

  ```
* dirname 获取目录
  ```Plain&#x20;Text
  用法：dirname [选项] 名称...
  Output each NAME with its last non-slash component and trailing slashes
  removed; if NAME contains no /'s, output '.' (meaning the current directory).

    -z, --zero     end each output line with NUL, not newline
        --help            显示此帮助信息并退出
        --version         显示版本信息并退出

  Examples:
    dirname /usr/bin/          -> "/usr"
    dirname dir1/str dir2/str  -> "dir1" followed by "dir2"
    dirname stdio.h            -> "."
  ```

## 2、自定义函数

```Plain&#x20;Text
#定义,如果说明了function关键字，funcname()可以写成funcname
[function] funcname()
{
	your program
	[return int]
}
#调用
funcname [para1 para2 ...]

```

* 函数定义必须在调用函数前执行，因为shell是逐行执行的
* 函数的返回值只能通过系统变量`$?`来获取，可以显示加`return`返回，如果不加，将以最后一条命令的运行结果作为返回值。`return`后跟数值`n（0-255）`

```shellscript
#计算两输入参数的和
[xpcheng@xpcheng test_shell]$ vim fun.sh
[xpcheng@xpcheng test_shell]$ cat fun.sh
#!/bin/bash
function two_sum()
{
        sum=0
        sum=$[$1+$2]
        echo $sum
}

read -p 'input your para1:' p1
read -p 'input your para2:' p2

two_sum $p1 $p2
[xpcheng@xpcheng test_shell]$ bash fun.sh
input your para1:2
input your para2:6
8

```
