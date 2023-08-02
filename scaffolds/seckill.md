---
title: Redis秒杀
date: 2019-08-27 11:41:03
author: wflin
img: 
coverImg: /medias/banner/10.jpg
top: false
cover: false
toc: true
mathjax: false
summary: 
  springboot+redis 的秒杀笔记
tags:
  - java
categories:
  - Java
password:
---
# 一

创建 2 数据库

* stock

```sql
DROP TABLE IF EXISTS `stock`;
CREATE TABLE `stock`  (
  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT '主键',
  `name` varchar(50) CHARACTER SET latin1 COLLATE latin1_swedish_ci NULL DEFAULT NULL COMMENT '名称',
  `count` int(11) NULL DEFAULT NULL COMMENT '库存',
  `sale` int(11) NULL DEFAULT NULL COMMENT '已售',
  `version` int(11) NULL DEFAULT NULL COMMENT '乐观锁，版本号',
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = InnoDB AUTO_INCREMENT = 1 CHARACTER SET = latin1 COLLATE = latin1_swedish_ci ROW_FORMAT = Dynamic;

```

* stock_order

```sql
DROP TABLE IF EXISTS `stock_order`;
CREATE TABLE `stock_order`  (
  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT '主键',
  `sid` int(11) NULL DEFAULT NULL COMMENT '库存ID',
  `name` varchar(30) CHARACTER SET latin1 COLLATE latin1_swedish_ci NULL DEFAULT NULL COMMENT '商品名称',
  `create_time` datetime NULL DEFAULT NULL ON UPDATE CURRENT_TIMESTAMP COMMENT '创建时间',
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = InnoDB AUTO_INCREMENT = 1 CHARACTER SET = latin1 COLLATE = latin1_swedish_ci ROW_FORMAT = Dynamic;

```

