---
title: samba
author: wflin
date: 2023-6-10
tags:
  - iot
  - linux
top: false
cover: false
toc: true
mathjax: true
summary: "samba 服务安装和关联windwos"
categories:
  - linux
---

# 安装samba

> sudo apt-get install samba



# 修改配置文件

> sudo nano /etc/samba/smb.conf

向文件末尾添加内容

```shell
[ubuntu] #标识随便起，只要不和上面重名
	commen = ubuntu # 服务的名字
	path = /home/ubuntu #共享路径
	writable = yes #提供写的权限（一般这个就够了） 
	valid user = ubuntu #路径所属用户（我是用当前用户就叫 ubuntu ，所以我写这个）
	create mask = 0777 #设置这个文件夹的权限
```



# 连接samba

* win+r 输入ip

  > //:ip

* 这时候会弹出认证界面，然后你输入用户名和密码后会发现拒绝连接，是因为 samba 的服务的认证不是走的 ubuntu 用户名和密码这一套，是它自己，但是在刚刚的过程中好向我们并没有设置密码的相关操作。

# 设置samba密码

* > sudo smbpasswd -a ubuntu #刚刚配置的 samba 的用户名

* 两次输入密码后就可以正常连接和使用了

# 注意

* 如果开启了防火墙的情况下需要放行端口
* tcp
  * 139
  * 445
* udp
  * 137
  * 138