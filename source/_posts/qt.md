---
title: qt
author: wflin
date: 2023-08-25
top: false
cover: false
toc: false
mathjax: false
summary: qt
tags:
  - c++
  - qt
categories:
  - qt
---

# 信号和槽

1. 信号：信号指控件发出的特定信号。

   比如按钮信号



![image-20230903164907782](qt/image-20230903164907782.png)



* 槽：就是槽函数的意思，我们可以把槽函数绑定在某一个控件信号上

# 关联信号和槽

## 自动关联

* 手动选择控件，然后右键 -> 转到槽

* 自动关联会给我们的工程添加以下内容

  ![image-20230903165542104](qt/image-20230903165542104.png)

* 槽函数只能声明到 **private slot** 或者 **public slot**

  ![image-20230903165817793](qt/image-20230903165817793.png)

## 手动关联

* 使用 connect 函数

  ```c++
  connect(ui-> reBtn,SIGNAL(clicked()),this,SLOT(on_reBtn_clicked()));
  
  connect(A,SIGNAL(B),C,SLOT(D));
  #当对象 A 发出 B 信号的时候，就会触发对象 c 的槽函数 D
  ```

  ![image-20230903171021719](qt/image-20230903171021719.png)

  ​	![image-20230903171108708](qt/image-20230903171108708.png)

# 控件

## 添加图片

1. 添加 qt 资源文件

   ![image-20230903171248242](qt/image-20230903171248242.png)

2. 添加资源文件 open with-> 资源编辑器

   ![image-20230903171915787](qt/image-20230903171915787.png)

3. 添加前置路径

   ![image-20230903172017953](qt/image-20230903172017953.png)

4. 点击添加文件选项，将要要的图片添加进去

   ![image-20230903172125200](qt/image-20230903172125200.png)

5. 添加 qlabel 控件，更改样式表

   ![image-20230903172252879](qt/image-20230903172252879.png)

6. 添加资源 img（border-images）

   ![image-20230903172503880](qt/image-20230903172503880.png)

7. 效果

   * border-images

   ![image-20230903172626974](qt/image-20230903172626974.png)

* background-images

  ![image-20230903172748573](qt/image-20230903172748573.png)

# 布局

## 水平布局

## 垂直布局

## 栅格化布局

# 界面切换

# qt下的串口编程

# qt网络编程

# qt下操作GPIO

