# Hologres除法报错

背景是从mysql迁移脚本到Hologres，因为除数为0时不会报错而是返回`null`

但是Hologres会报错，无法运行

针对这个问题，Hologres提供了一个解决方案

[MySQL至Hologres语句、函数的区别](https://help.aliyun.com/zh/hologres/user-guide/migrate-data-from-mysql-to-hologres?spm=a2c3c.ALYWebVC.0.0&shareId=12c40ceea3340709f65277782b7a49ec)

主要说明MySQL数据平滑迁移至Hologres，迁移完成后MySQL与Hologres查询语句与函数的使用区别

# DBeaver UE破解验证分析
吾爱破解论坛的源码解析[DBeaver UE破解验证分析](https://www.52pojie.cn/thread-1668629-1-1.html)

# PD16.5数据建模下载
[地址](https://www.fujieace.com/software/powerdesigner.html)

# 通过JS让浏览器页面进入可编辑状态
主要是为了跳过一下网页在复制的时候需要你登录的场景，进入编辑状态后直接`Ctrl+X`进行剪切，可以绕开登陆

```javascript
document.body.contentEditable=true
```
# split切分大文本文件
windows下，打开大文本文件时，有时候会直接卡死，通过这个命令按一定条件切分，达到拆解可预览的目的
```bash
split E:/fanruan.log E:/fanruan/fanruan -b 20m -a 3 -d
split -b 10M -d -a 3 /e/fanruan/fanruanlog.1121 /e/fanruan/fanruanlog.1121_

# 1. 按大小分割文件
split -b 1000000000 test.log
# -b 参数表示按字节大小进行分割，在数字后边要指定被分割的文件名。
# 这里在输入文件名时有个小技巧，可以直接把该文件拖动到cmd窗口中，会自动输入该文件的具体目录。
# 这里的文件还可以使用通配符，比如split -b 1000000000 *。
# 这个命令表示按1000000000byte的大小进行分割，近似于1GB，大概是953MB的大小。
# 对于这个6GB大小的文件test.log，会被分割成6个小文件。
# 这些小文件的命名是有规律的：xaa、xab、xac、xad、xae、xaf。
# 如果你分割了非常多的小文件，当文件名到了xyz之后，会变成xzaaa、xzaab、xzaac、xzaad……
# 所以不用担心小文件过多而导致文件重名什么的。
# 当然，上边的这种写法不够人性化
# 我们可以使用其他的单位来指定分割的大小：k、m。k表示KB，m表示MB。
split -b 100k test.log表示将test.log按照100KB的大小进行分割。
split -b 100m test.log表示将test.log按照100MB的大小进行分割。
# 2. 按照所有行数加起来的最大字节数进行分割
split -C 100k test.log
# -C 参数表示按照所有行数加起来的最大字节数进行分割
# 同样可以使用k或者m作为单位，其实效果和上边的-b差不多，只是在切割时将尽量维持每行的完整性。
# 3. 按照行数进行分割
split -l 1000 test.log
split -1000 test.log
# -l 参数表示按照行数进行分割，即一个小文件中最多有多少行
# -l number可以缩写成-number，上边的命令表示按照1000行一个小文件进行分割。
```

# Certbot 免费证书申请及续期
参考文章[在这](https://juejin.cn/post/7205839782381928508)
## 证书申请
[Certbot 证书申请](main/certbot_ssl_get.md)
## 证书续期
[Certbot 证书续期](main/certbot_ssl_renewal.md)

# Tomcat9配置SSL证书
[Tomcat9 配置 SSL 证书](main/tomcat_config_ssl.md)
