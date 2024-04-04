# 概述

系统配置了自定义登录页面需要去掉，可以修改`fine_conf_entity 表里id=AppearanceConfig.loginType`字段的`value`值，`1`改成`0`，再重启工程就恢复成默认登录页。

> 其中 0 表示用默认登录页插件，1 表示设置了登录网页

# Windows

1. 打开文件`webapps/webroot/WEB-INF/embed/finedb`下的`db.script`
2. `Ctrl+F`搜索字段`AppearanceConfig.loginType`
3. 修改`value`值为`0`
4. 重启工程就可以跳转为默认登录页面

# Linux

```shell
# 切换到finedb目录下
cd /opt/apache-tomcat-9.0.30/webapps/webroot/WEB-INF/embed/finedb
# 打开db.script文档 
vi db.script
```

1. 按`esc`后输入`/AppearanceConfig.loginType`，`/`是搜索，后面加搜索内容
2. 输入`i`进入编辑模式
3. 修改`value`值为`0`
4. `esc`进入命令模式，输入`wq!`保存并退出

# 参考

- [平台配置存储在 finedb 中的位置](http://knowledge.fanruan.com/doc-view-4411.html)
