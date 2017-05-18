---
layout: post
cover: 'assets/images/cover7.jpg'
title: nginx.config配置文件说明
date:   2017-05-18 12:36:00
tags: IT 前端
subclass: 'post tag-test tag-content'
categories: 'casper'
navigation: True
logo: 'assets/images/ghost.png'
---




### nginx.config文件

> 在nginx目录的config文件夹下,比如```/usr/local/nginx/config```。

#### 配置文件的结构
---

* __全局块__：配置影响nginx全局的指令。一般有运行nginx服务器的用户组，nginx进程pid存放路径，日志存放路径，配置文件引入，允许生成worker process数等。

* __events块__：配置影响nginx服务器或与用户的网络连接。有每个进程的最大连接数，选取哪种事件驱动模型处理连接请求，是否允许同时接受多个网路连接，开启多个网络连接序列化等。

* __http块__：可以嵌套多个server，配置代理，缓存，日志定义等绝大多数功能和第三方模块的配置。如文件引入，mime-type定义，日志自定义，是否使用sendfile传输文件，连接超时时间，单连接请求数等。

* __server块__：配置虚拟主机的相关参数，一个http中可以有多个server。

* __location块__：配置请求的路由，以及各种页面的处理情况。

##### nginx.config文件说明

```

#################### 全局块 #####################

user www www;                                   # 配置用户或者组，默认为nobody nobody。
worker_processes auto;                          # 允许进程数，默认为1

error_log /data/wwwlogs/error_nginx.log crit;   # 错误日志路径，级别。这个设置可以放入全局块，http块，server块，级别以此为：debug|info|notice|warn|error|crit|alert|emerg
pid /var/run/nginx.pid;                         # nginx进程pid存放路径
worker_rlimit_nofile 51200;                     # 一个进程所能够打开的fd的最大数

#################### events块 #####################

events {
    use epoll;                                  # 事件驱动模型，select|poll|kqueue|epoll|resig|/dev/poll|eventport
    worker_connections 51200;                   # 每个进程的最大连接数，默认为512
    multi_accept on;                            # 是否允许同时接受多个网路连接，默认为off
    # accept_mutex on;                          # 设置网路连接序列化（是否允许互斥锁），防止惊群现象发生，默认为on
}                                               # 惊群现象：一个网路连接到来，多个睡眠的进程被同事叫醒，但只有一个进程能获得链接，这样会影响系统性能。

#################### http块 #####################

http {

    ################ http全局块 #################

    #可以嵌套多个server，配置代理

    include mime.types;                          # 文件扩展名与文件类型映射表              
    default_type application/octet-stream;       # 默认文件类型，默认为text/plain
    #access_log off;                             # 取消服务日志           
    server_names_hash_bucket_size 128;
    client_header_buffer_size 32k;
    large_client_header_buffers 4 32k;
    client_max_body_size 1024m;
    # sendfile_max_chunk 100k;                   # 每个进程每次调用传输数量不能大于设定的值，默认为0，即不设上限。
    sendfile on;                                 # 允许sendfile方式传输文件，默认为off，可以在http块，server块，location块。
    tcp_nopush on;
    keepalive_timeout 120;                       # 连接超时时间，默认为75s，可以在http，server，location块。

    server_tokens off;
    tcp_nodelay on;

    fastcgi_connect_timeout 300;
    fastcgi_send_timeout 300;
    fastcgi_read_timeout 300;
    fastcgi_buffer_size 64k;
    fastcgi_buffers 4 64k;
    fastcgi_busy_buffers_size 128k;
    fastcgi_temp_file_write_size 128k;

    #Gzip Compression
    gzip on;
    gzip_buffers 16 8k;
    gzip_comp_level 6;
    gzip_http_version 1.1;
    gzip_min_length 256;
    gzip_proxied any;
    gzip_vary on;
    gzip_types
        text/xml application/xml application/atom+xml application/rss+xml application/xhtml+xml image/svg+xml
        text/javascript application/javascript application/x-javascript
        text/x-json application/json application/x-web-app-manifest+json
        text/css text/plain text/x-component
        font/opentype application/x-font-ttf application/vnd.ms-fontobject
        image/x-icon;
    gzip_disable "MSIE [1-6]\.(?!.*SV1)";

    #If you have a lot of static files to serve through Nginx then caching of the files' metadata (not the actual files' contents) can save some latency.
    open_file_cache max=1000 inactive=20s;
    open_file_cache_valid 30s;
    open_file_cache_min_uses 2;
    open_file_cache_errors on;

    # error_page 404 https://www.error.com;     #错误页

    ################ http server块 #################
   
    # 配置虚拟主机的相关参数，一个http中可以有多个server。

    server {
        listen 80;                                          # 监听端口
        server_name _;                                      # 监听地址     
        access_log /data/wwwlogs/access_nginx.log combined; # combined为日志格式的默认值
        root /data/wwwroot/default;                         # 根目录
        index index.html index.htm index.php;

####################### 配置请求的路由，以及各种页面的处理情况。###########################

        location /nginx_status {   # 请求的url过滤，正则匹配，下同
            stub_status on;
            access_log off;
            allow 127.0.0.1;       # 允许的ip  
            deny all;              # 拒绝的ip
            }
        location ~ [^/]\.php(/|$) {
            #fastcgi_pass remote_php_ip:9000;
            fastcgi_pass unix:/dev/shm/php-cgi.sock;
            fastcgi_index index.php;
            include fastcgi.conf;
            }
        location ~ .*\.(gif|jpg|jpeg|png|bmp|swf|flv|ico)$ {
            expires 30d;
            access_log off;
            }
        location ~ .*\.(js|css)?$ {
            expires 7d;
            access_log off;
            }
    }

########################## 第三方模配置导入 #############################
    include vhost/*.conf;       # 可以是用户自定义的配置模块
}

```

配置完成之后可通过命令``` #/usr/local/nginx/sbin/nginx -s reload ```重启nginx生效