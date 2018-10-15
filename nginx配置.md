---
title: nginx使用
date: 2018-10-10 21:50:00
tags: [nginx]
categories: [系统工具] 

---
# nginx使用
## nginx安装
1. 安装必要文件  

    ```bash
    yum -y install make zlib zlib-devel gcc-c++ libtool  openssl openssl-devel
    ```
2. 配置依赖PCRE
  
    ```bash
    #下载PCRE
    wget http://downloads.sourceforge.net/project/pcre/pcre/8.35/pcre-8.35.tar.gz
    #解压
    tar zxvf pcre-8.35.tar.gz
    #进入文件夹并编译安装
    cd pcre-8.35
    ./configure
    make && make install
    #查看pcre版本，显示正常则安装成功
    pcre-config --version
    ```
    <!--more-->
3. 安装nginx  

    ```bash
    #下载nginx
    wget http://nginx.org/download/nginx-1.6.2.tar.gz
    #解压安装包
    tar zxvf nginx-1.6.2.tar.gz
    #进入目录
    cd nginx-1.6.2
    #编译安装(使用默认配置)
    ./configure
    make
    make install
    #查看nginx版本
    nginx -v
    ```

## nginx配置
### 默认的配置  
> 如果使用的是默认配置，以下路径均不用更改；更改了某一路径的情况下需使用更改后的路径。    

```bash
# 文件/usr/local/nginx/conf/nginx.conf, 按需要修改

#定义Nginx运行的用户和用户组
#user  nobody;

#nginx进程数，建议设置为等于CPU总核心数。
worker_processes  1;

#全局错误日志定义类型，[ debug | info | notice | warn | error | crit ]
#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#进程文件
#pid        logs/nginx.pid;


events {
    #单个进程最大连接数
    worker_connections  1024;
}


http {
    #文件扩展名与文件类型映射表
    include       mime.types;
    
    #默认文件类型
    default_type  application/octet-stream;

    #log日志格式
    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;
    
    #开启高效文件传输模式，sendfile指令指定nginx是否调用sendfile函数来输出文件，对于普通应用设为 on，如果用来进行下载等应用磁盘IO重负载应用，可设置为off，以平衡磁盘与网络I/O处理速度，降低系统的负载。注意：如果图片显示不正常把这个改 成off。
    sendfile        on;
    
    #防止网络阻塞
    #tcp_nopush     on;

    #长连接超时时间，单位是秒
    #keepalive_timeout  0;
    keepalive_timeout  65;

    #开启gzip压缩输出
    #gzip  on;

    #server块，配置文件引入 -> 在proxy/下存放不同项目的配置文件
    include /opt/proxy/*.conf;

    #虚拟主机的配置，建议将每个server配置分文件存放，用include引入，结构更清晰
    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #     #监听端口
    #    listen       8000;
    #    listen       somename:8080;
    #     #域名可以有多个，用空格隔开
    #    server_name  somename  alias  another.alias;
    #    #默认编码
    #    charset utf-8;

    #    location / {
    #        #转发地址
    #        proxy_pass http://127.0.0.1:4000/home/;
    #    }
    
    #    #尽快发送收到的数据
    #    tcp_nodelay     on;
    #    设置请求头
    #    proxy_set_header Host            $host;
    #    proxy_set_header X-Real-IP       $remote_addr;
    #    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    
    #    root   html;
    #    index  index.html index.htm;
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}
}
```
### location模式匹配
```bash
location [=|~|~*|^~|@] /uri/ {
  ...
}
```
* `~` 表示区分大小写的正则匹配
* `~*` 表示不区分大小写的正则匹配
* `^~` 表示uri以某个常规字符串开头，不是正则匹配。如果该选项匹配，只匹配该选项，不匹配别的选项，一般用来匹配目录
* `=` 表示精确匹配
* `@` 定义一个命名的 location，使用在内部定向时，例如 error_page  
【注意】无正则表达式的location匹配顺序按照路径匹配最大长度进行匹配，而正则表达式的location匹配顺序按照配置文件中的先后顺序进行匹配。`/`匹配所有的路径，仅当其余locatioin均匹配失败的时候匹配`/`。    
**优先级：** `=` > `^~ 路径` > `~` = `~*` > `直接路径` > `/`

### 模式匹配示例（反向代理）
下面举几个例子：  

```bash
location /ab {
    proxy_pass http://localhost:8888/test1/;
}
location = /abcde/12345 {
    proxy_pass http://localhost:8888/test2/;
}    
location ^~ /abcde/123 {
    proxy_pass http://localhost:8888/test3/;
}
location ~* /abcde/\d*{
    proxy_pass http://localhost:8888;
    rewrite . /test4/ break;
}
location ~ /abc {
    proxy_pass http://localhost:8888;
    rewrite . /test5/ break;
}
location /abcd {
    proxy_pass http://localhost:8888/test6/;
}
location / {
    proxy_pass http://localhost:8888/test7/;
}
```
给出几组输入的路径  

* `/abcde/12345`：匹配顺序为 2 > 3 > 4 > 5 > 6 > 1 > 7，将重定向到`/test2/`
* `/abcde/12345/`：匹配顺序为 3 > 4 > 5 > 6 > 1 > 7，将重定向到`/test3/45/`
* `/abcde/12`：匹配顺序为 4 > 5 > 6 > 1 > 7，将重定向到`/test4/`
* `/abcd/`：匹配顺序为 5 > 6 > 1 > 7，将重定向到`/test5/`
* `/abbbbbbb/`：匹配顺序为 1 > 7， 将重定向到`/test1/bbbbbb/`
* `/random/`：只能匹配7，将重定向到`/test7/random/`
* `/12/abcde/12222abc`：匹配顺序为 4 > 5，将重定向到`/test4/`。  
* 实际上，能匹配4的必定能被5匹配，如果将test4和test5的上下位置换一换，test4就可以退休了。另外，`/abcd`也会被`~ /abc`屏蔽。  

> 关于出现的`rewrite`指令大家可以参考：【还没写好】。由于location的匹配为正则匹配，不能在反向代理(proxy_pass)中指定目录(会报`cannot have URI part in location`错误)，但可以通过`rewrite`进行拼接。

在写代理路径和跳转地址时要注意以下几点：  

1. 地址进行拼接的时候，被代理的请求路径将会舍弃代理路径部分，将剩下来的部分拼接在地址后面进行代理。因此需要注意`\`的位置，处理不好时跳转地址会出现`//`或者缺少`\`的情况。
2. 跳转地址仅到端口结束(如`http://localhost:8888`)，则拼接地址时会把整个请求路径进行拼接(不会舍弃代理路径部分)
3. 尽量少出现某个代理会被屏蔽的情况。
4. 还在想。。。