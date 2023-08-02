---
title: restful
date: 2022-3-25
author: wflin
tags:
 	- javaweb
  	- java
top: false
cover: false
toc: true
mathjax: true
summary: "web"
categories:
  - javaweb
---

## restful

1. 请求方法原则

   * get （查询）

    *      post （添加）
    *      put （全字段更新）
    *      patch（更新部分字段）
    *      delete (删除)

2. 使用复数名词 

   * user ==> users 
   * car === >cars 
   * product == >products

3. 请求和响应时候指定 

   * accpect：@RequestBody  
   * reponse：@ResponseBody

4. 资源唯一标识在通过参数传递时使用路径传参 

   * url?id=21 ====> url/21
   * 排序？sort=name
   * 分页？page=1





```txt
/**
 * restful：
 *  1. 原则：
 *      1. get （查询）
 *      2. post （添加）
 *      3. put （全字段更新）
 *      4. patch（更新部分字段）
 *      5. delete (删除)
 * 2. 使用复数名词 user==>users car===>cars product==>products
 * 3.
 */
```

