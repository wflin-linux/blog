---
title: Lua
date: 2019-08-27 11:41:03
author: wflin
img: 
coverImg: /medias/banner/11.jpg
top: false
cover: false
toc: true
mathjax: false
summary: 
  redis 秒杀用到了
tags:
  - lua
categories:
  - lua
password:
---
# lua【为了解决高并发秒杀商品冗余问题】

## lua运行的形式

* lua -i 交互式编程

  ```lua
  $ lua -i 
  $ Lua 5.3.0  Copyright (C) 1994-2015 Lua.org, PUC-Rio
  > print("Hello World！")
  Hello World！
  >
  ```

  

* 脚本式编程

  * hello.lua

    ```lua
    print("Hello World！")
    print("www.runoob.com")
    ```

    

    ```lua
    $ lua hello.lua
    Hello World！
    www.runoob.com
    ```

* 也可以修改为一下脚本实现（在开头添加：#!/usr/local/bin/lua）

  ```lua
  #!/usr/local/bin/lua
  
  print("Hello World！")
  print("www.runoob.com")
  ```

* 以上代码中，我们指定了 Lua 的解释器 /usr/local/bin directory。加上 # 号标记解释器会忽略它。接下来我们为脚本添加可执行权限，并执行：chmod +x hello.lua

  ```lua
  ./hello.lua 
  Hello World！
  www.runoob.com
  ```

## 注释方式

* 单行注释

  > **--**

* 多行注释

  > --[[
  >
  > ​	 多行注释
  >
  > --]]

*  Lua 是一个区分大小写的编程语言

* 变量命名规则和C语言的命名规则一致

## 保留关键字

*  以下列出了 Lua 的保留关键词。保留关键字不能作为常量或变量或其他用户自定义标示符：

  |          |       |       |        |
  | -------- | ----- | ----- | ------ |
  | and      | break | do    | else   |
  | elseif   | end   | false | for    |
  | function | if    | in    | local  |
  | nil      | not   | or    | repeat |
  | return   | then  | true  | until  |
  | while    | goto  |       |        |

* 一般约定，以下划线开头连接一串大写字母的名字（比如 _VERSION）被保留用于 Lua 内部全局变量。

## 基本语法

### 全局变量

* 在默认情况下，变量总是认为是全局的。

* 全局变量不需要声明，给一个变量赋值后即创建了这个全局变量，访问一个没有初始化的全局变量也不会出错，只不过得到的结果是：nil

  ```lua
  > print(b)
  nil
  > b=10
  > print(b)
  10
  >
  ```

* 如果你想删除一个全局变量，只需要将变量赋值为nil。

  ```lua
  b = nil
  print(b)      --> nil
  ```

* 这样变量b就好像从没被使用过一样。换句话说, 当且仅当一个变量不等于nil时，这个变量即存在。

## Lua 数据类型

* Lua 是动态类型语言，变量不要类型定义,只需要为变量赋值。 值可以存储在变量中，作为参数传递或结果返回。

* Lua 中有 8 个基本类型分别为：nil、boolean、number、string、userdata、function、thread 和 table。

  | 数据类型 | 描述                                                         |
  | :------- | :----------------------------------------------------------- |
  | nil      | 这个最简单，只有值nil属于该类，表示一个无效值（在条件表达式中相当于false）。 |
  | boolean  | 包含两个值：false和true。                                    |
  | number   | 表示双精度类型的实浮点数                                     |
  | string   | 字符串由一对双引号或单引号来表示                             |
  | function | 由 C 或 Lua 编写的函数                                       |
  | userdata | 表示任意存储在变量中的C数据结构                              |
  | thread   | 表示执行的独立线路，用于执行协同程序                         |
  | table    | Lua 中的表（table）其实是一个"关联数组"（associative arrays），数组的索引可以是数字、字符串或表类型。在 Lua 里，table 的创建是通过"构造表达式"来完成，最简单构造表达式是{}，用来创建一个空表。 |

* 我们可以使用 type 函数测试给定变量或者值的类型：

  ```lua
  print(type("Hello world"))      --> string
  print(type(10.4*3))             --> number
  print(type(print))              --> function
  print(type(type))               --> function
  print(type(true))               --> boolean
  print(type(nil))                --> nil
  print(type(type(X)))            --> string
  ```

### nil（空）

* nil 类型表示一种没有任何有效值，它只有一个值 -- nil，例如打印一个没有赋值的变量，便会输出一个 nil 值：

  ```lua
  > print(type(a))
  nil
  >
  ```

  

* 对于全局变量和 table，nil 还有一个"删除"作用，给全局变量或者 table 表里的变量赋一个 nil 值，等同于把它们删掉，执行下面代码就知：

  ```lua
  tab1 = { key1 = "val1", key2 = "val2", "val3" }
  for k, v in pairs(tab1) do
      print(k .. " - " .. v)
  end
  -------------结果---------------
  >
  1 - val3
  key1 - val1
  key2 - val2
  >
  
  -- 用 nil 删除 table 的第一个元素
  tab1.key1 = nil
  for k, v in pairs(tab1) do
      print(k .. " - " .. v)
  end
  
  -------------结果-----------------
  >
  1 - val3
  key2 - val2
  > 
  ```

### nil 作为比较时应该加上双引号 `“ `

* 

  ```lua
  > type(X)
  nil
  > type(X)==nil
  false
  > type(X)=="nil"
  true
  >
  ```

* **type(X)==nil** 结果为 **false** 的原因是 type(X) 实质是返回的 **"nil"** 字符串，是一个 string 类型

  ```lua
  type(type(X))==string
  ```

  

### boolean（布尔）

* boolean 类型只有两个可选值：true（真） 和 false（假），Lua 把 false 和 nil 看作是 false，其他的都为 true，数字 0 也是 true:

* 实例  `test.lua`

  ```lua
  print(type(true))
  print(type(false))
  print(type(nil))
   
  if false or nil then
      print("至少有一个是 true")
  else
      print("false 和 nil 都为 false")
  end
  
  if 0 then
      print("数字 0 是 true")
  else
      print("数字 0 为 false")
  end
  ```

* 代码执行结果

  ```lua
  $ lua test.lua 
  boolean
  boolean
  nil
  false 和 nil 都为 false
  数字 0 是 true
  ```

### number（数字）

* Lua 默认只有一种 number 类型 -- double（双精度）类型（默认类型可以修改 luaconf.h 里的定义），以下几种写法都被看作是 number 类型

* 实例

  ```lua
  print(type(2))
  print(type(2.2))
  print(type(0.2))
  print(type(2e+1))
  print(type(0.2e-1))
  print(type(7.8263692594256e-06))
  ```

* 以上代码执行结果

  ```lua
  number
  number
  number
  number
  number
  number
  ```

### String （字符串）

* 字符串由一对双引号或者单引号表示

  ```lua
  string1 = "this is string1"
  string2 = 'this is string2'
  ```

* 也可以用 2 个方括号 “[[]]” 来表示 “一块” 字符串

* 实例：`test.lua`

  ```lua
  html = [[
  <html>
  <head></head>
  <body>
      <a href="http://www.wflin.ltd/">wflin</a>
  </body>
  </html>
  ]]
  print(html)
  ```

* 以下代码执行结果为

  ```html
  <html>
  <head></head>
  <body>
      <a href="http://www.wflin.ltd/">wflin</a>
  </body>
  </html>
  ```

* 在对一个数字字符串上进行算术操作时，Lua 会尝试将这个数字字符串转成一个数字:

  ```lua
  > print("2" + 6)
  8.0
  > print("2" + "6")
  8.0
  > print("2 + 6")
  2 + 6
  > print("-2e2" * "6")
  -1200.0
  > print("error" + 1)
  stdin:1: attempt to perform arithmetic on a string value
  stack traceback:
          stdin:1: in main chunk
          [C]: in ?
  >
  ```

* 以上代码中"error" + 1执行报错了，字符串连接使用的是 **`..`** ，如

  ```lua
  > print("a" .. 'b')
  ab
  > print(157 .. 428)
  157428
  > 
  ```

* 使用 # 来计算字符串的长度，放在字符串前面，如下实例

  ```lua
  > len = "www.wflin.ltd"
  > print(#len)
  12
  > print(#"www.wflin.ltd")
  14
  >
  ```

### table（表）

* 在 Lua 里，table 的创建是通过"构造表达式"来完成，最简单构造表达式是{}，用来创建一个空表。也可以在表里添加一些数据，直接初始化表

* 实例 `table_test.lua`

  ```lua
  -- 创建一个空的 table
  local tbl1 = {}
   
  -- 直接初始表
  local tbl2 = {"apple", "pear", "orange", "grape"}
  ```

  

* Lua 中的表（table）其实是一个"关联数组"（associative arrays），数组的索引可以是数字或者是字符串。

* 实例

  ```lua
  -- table_test.lua 脚本文件
  a = {}
  a["key"] = "value"
  key = 10
  a[key] = 22
  a[key] = a[key] + 11
  for k, v in pairs(a) do
      print(k .. " : " .. v)
  end
  ```

* 脚本执行结果为

  ```lua
  $ lua table_test.lua 
  key : value
  10 : 33
  ```

* 不同于其他语言的数组把 0 作为数组的初始索引，在 Lua 里表的默认初始索引一般以 1 开始

* 实例

  ``` lua
  -- table_test2.lua 脚本文件
  local tbl = {"apple", "pear", "orange", "grape"}
  for key, val in pairs(tbl) do
      print("Key", key)
  end
  ```

* 执行结果为

  ```lua
  $ lua table_test2.lua 
  Key    1
  Key    2
  Key    3
  Key    4
  ```

* table 不会固定长度大小，有新数据添加时 table 长度会自动增长，没初始的 table 都是 nil。

* 实例

  ```lua
  -- table_test3.lua 脚本文件
  a3 = {}
  for i = 1, 10 do
      a3[i] = i
  end
  a3["key"] = "val"
  print(a3["key"])
  print(a3["none"])
  ```

* 执行结果为

  ```lua
  $ lua table_test3.lua 
  val
  nil
  ```

  

