---
title: Ngnix
date: 2019-08-27
author: wflin
top: false
cover: false
toc: false
mathjax: false
summary: 
  nginx 的学习笔记
tags:
  - java
categories:
  - nginx
password:
---



## 配置文件说明

```shell
#user  nobody; #以啥子用户运行
worker_processes  1; #开启一个worker进程（和机器配置有关，动态调整）
 
#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;
 
#pid        logs/nginx.pid; #nginx pid
 
 
events {
    worker_connections  1024; #一个 worker 的动态连接数量
}
 
 
http {
    include       mime.types; #支持接收和响应的类型
    default_type  application/octet-stream; #默认的类型 
    #access_log  logs/access.log  main; #连接日志
 
    sendfile        on; #开启高效网络传输，0拷贝
    #tcp_nopush     on;
 
    #keepalive_timeout  0;
    keepalive_timeout  65; #tcp连接的过期时间
 
    #gzip  on;
 
    # 定义一个虚拟主机
    server {
        listen       80; #监听端口
        server_name  localhost; #主机名（或者解析的域名）
 
 
 
        location / {
            root   html; #文件根目录
            index  index.html index.htm; #默认页名称
        }
 
        error_page   500 502 503 504  /50x.html; #报错码对应页
        location = /50x.html {
            root   html;
        }
    }
 
}
```



## 虚拟主机

* 原本一台服务器只能对应一个站点，通过虚拟主机技术可以虚拟化成多个站点同时对外提供服务

### servername匹配规则

* 需要注意的是servername匹配分先后顺序，写在前面的匹配上就不会继续往下匹配了。

  * 完整匹配： 我们可以在同一servername中匹配多个域名

    > server_name 域名1 域名2

  * 通配符匹配

    > server_name *.多级域名 （泛解析）

  * 通配符结束匹配

    > server_name 域名.*;

  * 正则匹配

    > server_name ~^[0-9]+\.域名$;



## 反向代理

#### 单个代理

```shell
location / {
	proxy_pass http://192.168.96.3：8080;
}
```



### 多个代理

```shell
server{
    listen       9000;
    listen  [::]:9000;
    server_name  192.168.96.3;

    #access_log  /var/log/nginx/host.access.log  main;

    location ~/edu/ {
        proxy_pass http://192.168.96.3:8080;
    }

    location ~/vod/ {
        proxy_pass http://192.168.96.3:8081;
    }

    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
}
```

## 负载均衡

* http 中加入负载均衡的规则

```shell
http{
	upstream myserver{
        server 192.168.96.3:8080;
        server 192.168.96.3:8081;
   	}
}
```

* server 中配置

```shell
server{
	location / {
        proxy_pass http://myserver;
    }
}
```

### 负载均衡策略

#### 轮询（默认）

>每个请求按照时间顺序逐一分配到不同后端服务器，如果后端服务器 down 掉，能自动删除

#### weight（权重）

> weight 代表权重默认是 1，权重越高被分配到的客户端越多

```shell
upstream myserver{
        server 192.168.96.3:8080 weight=5;
        server 192.168.96.3:8081;
   }
   
# 说明：这时候 8080 端口被分配到5个客户端之后 8081 端口才会被分配到 1 个

upstream myserver{
        server 192.168.96.3:8080 weight=5;
        server 192.168.96.3:8081 weight=10;
   }
# 说明：这时候 8081 端口被分配到 10 个客户端之后 8081 端口才会被分配到 5 个
```

#### ip_hash

> 每个请求按访问ip的 hash 结果分配，这样每个访客固定访问一个后端服务器，可以解决 session 的问题

```shell
upstream myserver{
    ip_hash;
	server 192.168.96.3:8080;
	server 192.168.96.3:8081;
   }
```

#### fair

> 按照后端服务器的响应时间来分配请求，响应时间短的优先分配

```shell
upstream myserver{
    server 192.168.96.3:8080;
    server 192.168.96.3:8081;
    # 需要单独配置
    fair;
}
```

## nginx 动静分离

### 配置反向代理

```shell
location / {
    proxy_pass http://127.0.0.1:8080; #(有这个 proxy_pass 后面的root 和 index 就失效了)
    root html;
    index index.html index.htm;
}
```

### 精确匹配

```shell
# css 的 location
location /css {
    root /usr/local/nginx/static;
    index index.html index.htm;
}

# 图片 的 location
location /images {
    root /usr/local/nginx/static;
    index index.html index.htm;
}

# js 的 location
location /js {
    root /usr/local/nginx/static;
    index index.html index.htm;
}
```

### 正则匹配（多）

#### 匹配说明:

* `/ 通用匹配，任何请求都会匹配到`
* `= 精准匹配，不是以指定模式开头`
* `~ 正则匹配，区分大小写`
* `~* 正则匹配，不区分大小写`
* `^~ 非正则匹配，匹配以指定模式开头的location`

#### 使用正则

```shell
# css 的 location
location ~*/(js|img|css) {
    root /usr/local/nginx/static;
    index index.html index.htm;
}
```

## location匹配说明

* `多个正则location直接按书写顺序匹配，成功后就不会继续往后面匹配`
* `普通（非正则）location会一直往下，直到找到匹配度最高的（最大前缀匹配）`
* `当普通location与正则location同时存在，如果正则匹配成功,则不会再执行普通匹配`
* `所有类型location存在时，“=”匹配 > “^~”匹配 > 正则匹配 > 普通（最大前缀匹配）`

## docker 运行一个 nginx

* > docker run -d -p80:80 --name ng nginx

访问 192.168.151.135:80  访问到 nginx 首页

* 进入容器查看 nginx 文件 docker exec -it ng bash
  * 配置文件：/etc/niginx/conf.d
  * 日志文件：/var/log/nginx
  * 页面文件：/usr/share/nignx/html
* 更改 index.html 内容
  * nginx -s reload
* 重新访问，发现页面已经更改