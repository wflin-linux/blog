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