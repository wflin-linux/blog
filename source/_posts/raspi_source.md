---
date: 2021-11-20
title: 树莓派国内镜像源
author: wflin
tags:
  - linux
  - raspberrypi
top: false
cover: false
toc: true
mathjax: true
summary: "树莓开发第一步"
categories:
  - "raspberrypi"
---
# 树莓派国内镜像源地址

* 查看树莓版本命令：

  ```shell
  lsb_release -a
  ```

## buster 版本

### 中科大

在`/etc/apt/sources.list` 加入

```shell
#----中科大
deb http://mirrors.ustc.edu.cn/raspbian/raspbian/ buster main contrib non-free rpi
deb-src http://mirrors.ustc.edu.cn/raspbian/raspbian/ buster main contrib non-free rpi
```

在 `/etc/apt/sources.list.d/raspi.list` 加入

```shell
#--中科大--raspi.list
deb http://mirrors.ustc.edu.cn/archive.raspberrypi.org/debian/ buster main ui
deb-src http://mirrors.ustc.edu.cn/archive.raspberrypi.org/debian/ buster main ui
```

### 阿里云

在`/etc/apt/sources.list` 加入

```shell
#----阿里云
deb http://mirrors.aliyun.com/raspbian/raspbian/ buster main non-free contrib
deb-src http://mirrors.aliyun.com/raspbian/raspbian/ buster main non-free contrib
```

在 `/etc/apt/sources.list.d/raspi.list` 加入

```shell
#--阿里云--raspi.list
deb http://mirrors.aliyun.com/raspberrypi/ buster main ui
deb-src http://mirrors.aliyun.com/raspberrypi/ buster main ui
```

### 清华大学

在`/etc/apt/sources.list` 加入

```shell
#-----清华大学
deb http://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/ buster main non-free contrib rpi
deb-src http://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/ buster main non-free contrib rpi
```

在 `/etc/apt/sources.list.d/raspi.list` 加入

```shell
#--清华大学---raspi.list
deb http://mirrors.tuna.tsinghua.edu.cn/raspberrypi/ buster main ui
deb-src http://mirrors.tuna.tsinghua.edu.cn/raspberrypi/ buster main ui
```

### 整合源

在`/etc/apt/sources.list` 加入

```shell
# buster source.list
#-----清华大学
deb http://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/ buster main non-free $
deb-src http://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/ buster main non-f$

#----中科大
deb http://mirrors.ustc.edu.cn/raspbian/raspbian/ buster main contrib non-free r$
deb-src http://mirrors.ustc.edu.cn/raspbian/raspbian/ buster main contrib non-fr$

#----阿里云
deb http://mirrors.aliyun.com/raspbian/raspbian/ buster main non-free contrib
deb-src http://mirrors.aliyun.com/raspbian/raspbian/ buster main non-free contrib
```

在 `/etc/apt/sources.list.d/raspi.list` 加入

```shell
# buster rasp.list
#--清华大学--raspi.list
deb http://mirrors.tuna.tsinghua.edu.cn/raspberrypi/ buster main ui
deb-src http://mirrors.tuna.tsinghua.edu.cn/raspberrypi/ buster main ui

#--中科大--raspi.list
deb http://mirrors.ustc.edu.cn/archive.raspberrypi.org/debian/ buster main ui
deb-src http://mirrors.ustc.edu.cn/archive.raspberrypi.org/debian/ buster main ui

#--阿里云--raspi.list
deb http://mirrors.aliyun.com/raspberrypi/ buster main ui
deb-src http://mirrors.aliyun.com/raspberrypi/ buster main ui
```

##  stretch

### 中科大

在`/etc/apt/sources.list` 加入

```shell
#-----中科大
deb http://mirrors.ustc.edu.cn/raspbian/raspbian/ stretch main contrib non-free rpi
deb-src http://mirrors.ustc.edu.cn/raspbian/raspbian/ stretch main contrib non-free rpi
```

在 `/etc/apt/sources.list.d/raspi.list` 加入

```shell
#--中科大--rasp.list
deb http://mirrors.ustc.edu.cn/archive.raspberrypi.org/ stretch main ui
deb-src http://mirrors.ustc.edu.cn/archive.raspberrypi.org/ stretch main ui
```

### 阿里云

在`/etc/apt/sources.list` 加入

```shell
#-----阿里云
deb http://mirrors.aliyun.com/raspbian/raspbian/ stretch main non-free contrib
deb-src http://mirrors.aliyun.com/raspbian/raspbian/ stretch main non-free contrib
```

在 `/etc/apt/sources.list.d/raspi.list` 加入

```shell
#--阿里云--rasp.list
deb http://mirrors.aliyun.com/raspberrypi/ stretch main ui
deb-src http://mirrors.aliyun.com/raspberrypi/ stretch main ui
```

### 清华源

在`/etc/apt/sources.list` 加入

```shell
#-----清华大学
deb http://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/ stretch main contrib non-free
deb-src http://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/ stretch main contrib non-free
```

在 `/etc/apt/sources.list.d/raspi.list` 加入

```shell
#--清华大学--rasp.list
deb http://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/ stretch main ui
deb-src http://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/ stretch main ui
```

### 整合源

在`/etc/apt/sources.list` 加入

```shell
# stretch source.list
#-----中科大
deb http://mirrors.ustc.edu.cn/raspbian/raspbian/ stretch main contrib non-free rpi
deb-src http://mirrors.ustc.edu.cn/raspbian/raspbian/ stretch main contrib non-free rpi

#-----阿里云
deb http://mirrors.aliyun.com/raspbian/raspbian/ stretch main non-free contrib
deb-src http://mirrors.aliyun.com/raspbian/raspbian/ stretch main non-free contrib

#-----清华大学
deb http://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/ stretch main contrib non-free
deb-src http://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/ stretch main contrib non-free
```

在 `/etc/apt/sources.list.d/raspi.list` 加入

```shell
#--中科大--rasp.list
deb http://mirrors.ustc.edu.cn/archive.raspberrypi.org/ stretch main ui
deb-src http://mirrors.ustc.edu.cn/archive.raspberrypi.org/ stretch main ui

#--阿里云--rasp.list
deb http://mirrors.aliyun.com/raspberrypi/ stretch main ui
deb-src http://mirrors.aliyun.com/raspberrypi/ stretch main ui

#--清华大学--rasp.list
deb http://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/ stretch main ui
deb-src http://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/ stretch main ui
```

### 