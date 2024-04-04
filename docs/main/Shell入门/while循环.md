# while循环

```shellscript
while [ condition ]
do
	your program
done
```

```shellscript

#1-99累计求和
[xpcheng@xpcheng test_shell]$ vim while.sh
[xpcheng@xpcheng test_shell]$ cat while.sh
#!/bin/bash
s=0
i=0
while [ $i -le 100 ]
do
        s=$[$s+$i]
        i=$[$i+1]
done
echo $s
[xpcheng@xpcheng test_shell]$ bash while.sh
5050

```
