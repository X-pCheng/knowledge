# Tomcat9配置crt证书

`tomcat9`目前是无法直接用`crt`进行配置，需要转换为`p12`、`keystore`、`jks`类的文件。

因此，以下转换为`keystore`文件进行配置说明。

转换时需要具有`Java`环境，配置好相应的环境变量。

本例版本为`Java8`。

以下在`Windows10`中进行操作，`Linux`系统命令行操作类似。

## 第一步：检查`Java`环境
--------------

打开`cmd`控制窗口，输入`Java`

```powershell
Microsoft Windows [版本 10.0.19042.1237]
(c) Microsoft Corporation。保留所有权利。

C:\\Users\\hp>java -version
java version "1.8.0_281"
Java(TM) SE Runtime Environment (build 1.8.0_281-b09)
Java HotSpot(TM) 64-Bit Server VM (build 25.281-b09, mixed mode)

C:\\Users\\hp>
```

显示如上版本信息，说明`Java`相关配置已完成。

## 第二步：使用`openssl`将`crt`文件转换为`p12`文件
---------------------------------

`openssl`命令行具体用法可参见：[openssl - OpenSSL command line program](https://www.openssl.org/docs/man3.0/man1/openssl.html)

打开`cmd`，执行如下操作：

```powershell
F:\\>openssl pkcs12 -export -in F:\\mycert.crt -inkey F:mycert.key -out F:\\mycert.p12 -name tomcat
#输入导出文件的密码，后面查看文件和转换时都需要用到
Enter Export Password:
Verifying - Enter Export Password:
F:\\>
```

命令说明：

> \-in ：主crt证书文件目录+文件名
> 
> *   `inkey`：颁布证书携带的`key`文件目录+文件名
> *   `out`：将要输出的`p12`文件目录+文件名
> *   `name`：指定密钥库文件别名

## 第三步：使用`keytool`将`p12`文件转换为`keystore`文件
--------------------------------------

```powershell
F:\\>keytool -importkeystore -v  -srckeystore mycert.p12 -srcstoretype pkcs12 -srcstorepass 123456 -destkeystore mycert.keystore -deststoretype jks -deststorepass 123456
正在将密钥库 mycert.p12 导入到 mycert.keystore...
已成功导入别名 tomcat 的条目。
已完成导入命令: 1 个条目成功导入, 0 个条目失败或取消
[正在存储mycert.keystore]

Warning:
JKS 密钥库使用专用格式。建议使用 "keytool -importkeystore -srckeystore mycert.keystore -destkeystore mycert.keystore -deststoretype pkcs12" 迁移到行业标准格式 PKCS12。
```

命令说明：

> \-srckeystore ：被转换的源文件目录+文件名
> 
> *   `srcstoretype`：被转换的源文件密钥库类型
> *   `srcstorepass` ：被转换的源文件设置的密钥库口令
> *   `destkeystore` ：将输出的结果文件目录+文件名
> *   `deststoretype`：将输出的结果文件密钥库类型
> *   `deststorepass`：将输出的结果文件的密钥库口令，需和`srcstorepass`保持一致

成功后，将在目标路径下找到`mycert.keystore`文件

## 第四步：`tomcat`中配置`keystore`文件
---------------------------

将上面生成的`keystore`文件放置到`%tomcat_home%/conf/`目录下

打开`%tomcat_home%/conf/server.xml`，找到下面的片段：

```xml
<!--
    <Connector port="8443" protocol="org.apache.coyote.http11.Http11AprProtocol"
               maxThreads="150" SSLEnabled="true" >
        <UpgradeProtocol className="org.apache.coyote.http2.Http2Protocol" />
        <SSLHostConfig>
            <Certificate certificateKeyFile="conf/localhost-rsa-key.pem"
                         certificateFile="conf/localhost-rsa-cert.pem"
                         certificateChainFile="conf/localhost-rsa-chain.pem"
                         type="RSA" />
        </SSLHostConfig>
    </Connector>
-->
```

取消注释，然后修改为如下内容（之前未进行配置可以直接在文档中新增）：

```powershell
<Connector port="8443" protocol="org.apache.coyote.http11.Http11Protocol"
           maxThreads="150" SSLEnabled="true" scheme="https" secure="true"
           clientAuth="false" sslProtocol="TLS" 
           keystoreFile="conf/mycert.keystore" 
           keystorePass="123456"  />
```

参数说明：

> port: https的端口,默认8443
> 
> `clientAuth`:设置是否双向验证，默认为`false`，设置为`true`代表双向验证`keystoreFile`
> 
> `keystoreFile`: `keystore`证书的路径
> 
> `keystorePass`: 生成`keystore`时的口令

## 第五步：重启`tomcat`
--------------

以上配置完成重启`tomcat`，用`https`访问成功即说明完成配置。

## 其他
--

生成`keyestore`文件信息，在`cmd`中可以通过以下命来查看密钥库的内容：

```powershell
keytool -list -v -keystore [your keystore file location]
-v 输出详细信息
```

```powershell
F:\\>keytool -list -v -keystore mycert.keystore
输入密钥库口令:
密钥库类型: jks
密钥库提供方: SUN

您的密钥库包含 1 个条目

别名: tomcat
创建日期: 2021-9-26
条目类型: PrivateKeyEntry
证书链长度: 1
[此处省略中间内容]
Warning:
JKS 密钥库使用专用格式。建议使用 "keytool -importkeystore -srckeystore mycert.keystore -destkeystore mycert.keystore -deststoretype pkcs12" 迁移到行业标准格式 PKCS12。
F:\\>	
```