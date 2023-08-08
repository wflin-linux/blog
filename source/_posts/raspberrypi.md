---
title: RaspberryPi
author: wflin
date: 2023-06-10
top: true
cover: false
toc: true
mathjax: false
summary: 树莓派开发相关
tags:
  - c
  - Linux
categories:
  - 嵌入式
---

# 下载和烧写镜像

* 从官网下载树莓派镜像，使用烧写工具 balena-etcher 烧录镜像

## 串口登录

* 将系统烧写到 SD 卡后，重新拔插 SD 卡，连接到系统 usb，电脑会出现树莓派的 boot 分区

* 修改 config.txt 文件，将内容追加到文件末尾（停止了蓝牙，解除了对串口的占用）

  > dtoverlay=pi3-miniuart-bt

* 覆盖 cmdline.txt，万一失效，所以建议先把这个文件备份一份

  > dwc_otg.lpm_enable=0 console=tty1 console=serial0,115200 root=/dev/mmcblk0p2 rootfstype=ext4 elevator=deadline fsck.repair=yes rootwait

* 使用 USB-TTL转换模块如图连接线,TTL 的 TXD 连接到树莓派的 RXD，另一根也反向接线便可

  ![aHR0cHM6Ly9zeWpzeHhqeS5naXRodWIuaW8vbXlibG9nL2Fzc2V0cy8xLzIwMTgwNjIyLTk5ZjRjNWE1LnBuZw](raspberrypi/aHR0cHM6Ly9zeWpzeHhqeS5naXRodWIuaW8vbXlibG9nL2Fzc2V0cy8xLzIwMTgwNjIyLTk5ZjRjNWE1LnBuZw.png)



* 线接好后插入电脑（电脑有安装了 USB 转串口驱动）在设备管理器看看使用的是哪个 COMS，使用终端工具选择串口和波特率进行连接便可

## SSH登录(我常用)

* 烧写好镜像后，一样把 SD 卡插入电脑，显示出 boot 分区

* 在boot目录添加两个文件

  * ssh（不要后缀，直接名字就叫 ssh，这样就可以开启 ssh）

  * 创建并添加一个文件 `wpa_supplicant.conf`

    ```txt
    ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
    update_config=1
    country=CN
    
    network={
            ssid="无线网名称"
            psk="无线网密码"
    }
    ```

* 添加好两个文件后直接上电，在路由器的管理后台(或者 windows 直接开热点，连接Windows的热点)查看树莓派的IP

* 然后使用ssh 登录到树莓派

* 默认账户：pi

* 默认密码：raspberry

# 库编程

![image-20230808141416928](raspberrypi/image-20230808141416928.png)

## 静态库

* **静态函数库**：是在程序执行前（编译）就加入到目标程序中去了 ；

* **优点**：运行快，发布程序无需提供静态库，因为已经在app中，移植方便

* **缺点**：编译生成的文件大

## 动态库

* **动态函数库**，是在程序执行时动态（临时）由目标程序去调用
* **缺点**： 运行慢
* **优点**： 小



![image-20230808141527323](raspberrypi/image-20230808141527323.png)



## 生成库

### 静态库

* 静态库的生成：格式：xxxx.a 

  ```shell
  gcc calcufuncs.c -c 生成xxx.o文件
  
  ar rcs libcalcufunc.a calcufuncs.o xxx.o文件生成xxx.a静态库文件
  ```

### 动态库

* 动态库的生成：格式：xxxxx.so

  ```shell
  gcc -shared -fpic calcufuncs.c -o libcalc.so
  
  -shared 指定生成动态库
  -fpic 标准，fPIC 选项作用于编译阶段,在生成目标文件时就得使用该选项,以生成位置无关的代码。
  ```

## 库的使用

### 静态库

* ```shell
  gcc calculatorT.c -lcalcufunc -L ./ -o mainProStatic
  
  -lcalcufunc -l #是制定要用的动态库，库名砍头去尾
  -L #告诉gcc编译器从-L制定的路径去找静态库。默认是从/usr/lib  /usr/local/lib去找
  ```

* eg:

  > gcc calculatorT.c -lcalc -L ./ -o main libcalc.so

### 动态库

[推荐阅读博文]([linux动态库(.so)搜索路径(目录)设置方法 - zhangzheng08pku - 博客园 (cnblogs.com)](https://www.cnblogs.com/progamming/p/13043652.html))

* Linux动态库的默认搜索路径是/lib和/usr/lib。动态库被创建后，一般都复制到这两个目录中。当程序执行时需要某动态库，并且该动 态库还未加载到内存中，则系统会自动到这两个默认搜索路径中去查找相应的动态库文件，然后加载该文件到内存中，这样程序就可以使用该动态库中的函数，以及该动态库的其它资源了。在Linux 中，动态库的搜索路径除了默认的搜索路径外，还可以通过以下三种方法来指定。

  * 在配置文件/etc/ld.so.conf中指定动态库搜索路径；

    ```shell
    vi /etc/ld.so.conf #添加 lib目录（绝对路径）
    ldconfig
    ```

    

  * 通过环境变量LD_LIBRARY_PATH指定动态库搜索路径；

    ```shell
    export LD_LIBRARY_PATH=”LD_LIBRARY_PATH:libpath”
    ```

    

  * 在编译目标代码时指定该程序的动态库搜索路径。

    ```shell
    还可以在编译目标代码时指定程序的动态库搜索路径。通过gcc 的参数”-Wl,-rpath,”指定
    ```

    