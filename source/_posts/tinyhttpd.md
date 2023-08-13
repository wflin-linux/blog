---
title: thttpd
date: 2023-8-4
author: wflin
tags:
  - thttpd
  - c
top: false
cover: false
toc: false
mathjax: false
summary: "thttpd 部署cgi"
categories:
  - c
---

* 下载源码

  > git clone https://github.com/larryhe/tinyhttpd.git

* 运行 configure 生成 makefile

* make && make install -i

  * i 的原因是忽略一个错误【网上资料说是因为 makefile 中多了一个空格导致的，直接忽略就行】

* 创建  /etc/thttpd.conf 

  ```shell
  dir=/usr/local/www #根目录
  user=root #所属用户权限
  logfile=/var/log/thttpd.log #日志文件存放位置
  pidfile=/var/run/thttpd.pid #pid 存放位置
  port=8080 #端口
  cgipat=/cgi-bin/* #cgi 程序存放位置
  ```

* 启动服务

  > thttpd -C /etc/thttpd.conf

