# Location

Nginx location 用于匹配请求 URI，匹配上之后按照其定义的方式对该请求进行处理。

[loacation 官方说明](https://link.juejin.cn/?target=http%3A%2F%2Fnginx.org%2Fen%2Fdocs%2Fhttp%2Fngx_http_core_module.html%23location)

## 语法

```Plain Text
Syntax:	location [ = | ~ | ~* | ^~ ] uri { ... }
location @name { ... }
Default:	—
Context:	server, location
```

## 匹配顺序

location 的匹配并不完全按照其在配置文件中出现的顺序来匹配，请求 URI 会按如下规则进行匹配：

1. Nginx 首先跟进前缀字符定义的 location，选择最长的并且记录下。
2. 如果最长的前缀匹配使用了`^~`修饰符，那就直接采用该配置，不再检查正则。如果是精准匹配`=`，精准匹配成功则会立即停止其他类型匹配；
3. `=`和`^~`均未匹配成功前提下，查找正则匹配 \~ 和 \~\* 。当同时有多个正则匹配时，按其在配置文件中出现的先后顺序优先匹配，命中则立即停止其他类型匹配；
4. 所有正则匹配均未成功时，返回步骤 1 中暂存的普通前缀匹配（不带参数 ^\~ ）结果

优先级由高到低：

```Plain Text
1. location =    # 精准匹配
2. location ^~   # 带参前缀匹配
3. location ~    # 正则匹配（区分大小写）
4. location ~*   # 正则匹配（不区分大小写）
5. location /a   # 普通前缀匹配，优先级低于带参数前缀匹配。
6. location /    # 任何没有匹配成功的，都会匹配这里处理
```

Nginx 会首先会检查多个 location 中是否有普通的 uri 匹配，如果有多个匹配，会先记住匹配度最高的那个。然后再检查正则匹配，这里正则匹配是有顺序的，从上到下依次匹配，一旦匹配成功，则结束检查，并就会使用这个 location 块处理此请求。如果正则匹配全部失败，就会使用刚才记录普通 uri 匹配度最高的那个 location 块处理此请求。

## 示例

```Plain Text
location  = / {
  # 精确匹配 / ，主机名后面不能带任何字符串
  [ configuration A ]
}
location  / {
  # 因为所有的地址都以 / 开头，所以这条规则将匹配到所有请求
  # 但是正则和最长字符串会优先匹配
  [ configuration B ]
}
location /documents/ {
  # 匹配任何以 /documents/ 开头的地址，匹配符合以后，还要继续往下搜索
  # 只有后面的正则表达式没有匹配到时，这一条才会采用这一条
  [ configuration C ]
}
location ~ /documents/Abc {
  # 匹配任何以 /documents/Abc 开头的地址，匹配符合以后，还要继续往下搜索
  # 只有后面的正则表达式没有匹配到时，这一条才会采用这一条
  [ configuration CC ]
}
location ^~ /images/ {
  # 匹配任何以 /images/ 开头的地址，匹配符合以后，停止往下搜索正则，采用这一条。
  [ configuration D ]
}
location ~* \.(gif|jpg|jpeg)$ {
  # 匹配所有以 gif,jpg或jpeg 结尾的请求
  # 然而，所有请求 /images/ 下的图片会被 config D 处理，因为 ^~ 到达不了这一条正则
  [ configuration E ]
}
location /images/ {
  # 字符匹配到 /images/
  [ configuration F ]
}
location /images/abc {
  # 字符匹配到 /images/abc
  [ configuration G ]
}
location ~ /images/abc/ {
	# 正则匹配 /images/abc/
	# 先最长匹配 [ config G ] 开头的地址，继续往下搜索，匹配到这一条正则
    [ configuration H ]
}

```

- / -> config A 精确完全匹配，即使/index.html 也匹配不了
- /downloads/download.html -> config B 匹配 B 以后，往下没有任何匹配，采用 B
- /images/1.gif -> configuration D ^\~ 优先
- /images/abc/def -> config H 最长匹配到 H，除了按照上述的修饰符匹配优先级，还有一条最长匹配的规则。按照这里的配置 D/F/G 均匹配过，但是最后以正则最长的匹配为准。如果在匹配中去掉 H 这条配置，那么就会匹配到 G。**如果去掉 G 这条规则，会匹配到 D，也就是存在 G 的时候虽然 ^\~ 优先级高于正则但是低于空前缀(location /images/abc)的精确匹配，当请求 /images/abc/def 会先被 G 匹配上，然后再继续寻找正则匹配。**

```Plain Text
<!--Locations tried-->
prefix match for location: /
priority prefix match for location: /images/
prefix match for location: /images/
prefix match for location: /images/abc
case sensitive regex match for location: /images/abc/

```

- /documents/document.html -> config C 匹配到 C，往下没有任何匹配，采用 C
- /documents/1.jpg -> configuration E 匹配到 C，往下正则匹配到 E
- /documents/Abc.jpg -> config CC 最长匹配到 C，往下正则顺序匹配到 CC，不会往下到 E

## 命名 location

> The “`@`” prefix defines a named location. Such a location is not used for a regular request processing, but instead used for request redirection. They cannot be nested, and cannot contain nested locations.

```Plain Text
location / {
    try_files $uri $uri/ @custom
}
location @custom {
    # ...do something
}

```

# proxy_pass

proxy_pass 属于`nginx_http_proxy_module`中的指令，用于设置代理。

## 语法

```Plain Text
Syntax:	proxy_pass URL;
Default:	—
Context:	location, if in location, limit_except

```

> Sets the protocol and address of a proxied server and an optional URI to which a location should be mapped. As a protocol, “`http`” or “`https`” can be specified.

proxy_pass 后面设置代理服务器的协议、地址，后面可以携带 URI。

## proxy_pass URL 的两种形式

这里的是否携带 URI，是指协议、域名、端口之后没有携带更详细路径。

### 带 URI

形如`https://test.com/、https://test.com/aa`，即只要是域名后面有`/`的这种形式。

> If the`proxy_pass`directive is specified with a URI, then when a request is passed to the server, the part of a[normalized](https://link.juejin.cn/?target=http%3A%2F%2Fnginx.org%2Fen%2Fdocs%2Fhttp%2Fngx_http_core_module.html%23location)request URI matching the location is replaced by a URI specified in the directive:

```Plain Text
location /proxy/api {
	proxy_pass https://proxy.com/test/v1;
}

```

访问**/proxy/api**/aa/bb 会变成实际访问`https://proxy.com/test/v1/aa/bb`。也就是 location 匹配到 path 会被 URL 里面的地址给替换掉。

### 不带 URI

> If`proxy_pass`is specified without a URI, the request URI is passed to the server in the same form as sent by a client when the original request is processed, or the full normalized request URI is passed when processing the changed URI

```Plain Text
location /proxy/api {
	proxy_pass https://proxy.com;
}

```

访问 /proxy/api/aa/bb 会变成实际访问`https://proxy.com/proxy/api/aa/bb`。也就是 location 匹配到的 path 会被直接透传给 proxy。

## 其他情况

### location 是正则或者在命名 location 中

proxy_pass 需要被设置为不带 URI 的形式

### 使用了 rewrite 规则，并且用于处理请求

```Plain Text
location /name/ {
    rewrite    /name/([^/]+) /users?name=$1 break;
    proxy_pass http://127.0.0.1;
}
```

这种情况下 proxy_pass 后面即使是携带了 URI 的也会被忽略，被 rewrite 改写后的 URI 会被传递给设置的 proxy（剔除掉设置的 URI 部分）。

```Plain Text
location /get {
        rewrite ^/get/(.*) /get?path=$1 break;
        proxy_pass http://3.233.172.144/node;
}
```

访问`/get/11111`会变成`http://3.233.172.144/get?path=11111`

### 在 proxy_pass 中使用的变量

```Plain Text
location /name/ {
    proxy_pass http://nginx.com$request_uri;
}

```

会替换 location 匹配的 path 部分。访问 /name/1111 会变成`nginx.com/name/1111`

## 参考文档

[nginx proxy 官方文档](https://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_pass)

[proxy_pass url 反向代理的坑](https://xuexb.github.io/learn-nginx/example/proxy_pass.html#url-%E5%8F%AA%E6%98%AF-host)

[Nginx 中文文档](https://github.com/DocsHome/nginx-docs)

[Nginx 的 location 规则迷之匹配](https://hqidi.com/95.html)

[一文理清 nginx 中的 location 配置（系列一）](https://segmentfault.com/a/1190000022315733)

[Nginx location directive examples](https://www.digitalocean.com/community/tutorials/nginx-location-directive)
