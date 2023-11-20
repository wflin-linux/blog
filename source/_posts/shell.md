---
title: shell
date: 2023-11-10
author: wflin
tags:
  - shell
top: false
cover: false
toc: false
mathjax: false
summary: "shell"
categories:
  - shell
---

推荐阅读：

## IF

### if []

```shell
#!/bin/bash 
if [ condition ] 
then 
 commands
fi
```

### **test**命令的数值比较功能

|   比较    |          描述          |
| :-------: | :--------------------: |
| n1 -eq n2 |   检查n1是否与n2相等   |
| n1 -ge n2 | 检查n1是否大于或等于n2 |
| n1 -gt n2 |    检查n1是否大于n2    |
| n1 -le n2 | 检查n1是否小于或等于n2 |
| n1 -lt n2 |    检查n1是否小于n2    |
| n1 -ne n2 |   检查n1是否不等于n2   |

### 字符串比较测试

| str1 = str2  | 检查str1是否和str2相同 |
| ------------ | ---------------------- |
| str1 != str2 | 检查str1是否和str2不同 |
| str1 < str2  | 检查str1是否比str2小   |
| str1 > str2  | 检查str1是否比str2大   |
| -n str1      | 检查str1的长度是否非0  |
| -z str1      | 检查str1的长度是否为   |

### 案例

* test7.sh

  ```shell
  #!/bin/bash 
  # testing string equality 
  testuser=rich 
  # 
  if [ $USER = $testuser ] 
  then 
   echo "Welcome $testuser" 
  fi
  ```

* test8.sh

  ```shell
  #!/bin/bash 
  # testing string equality 
  testuser=baduser 
  # 
  if [ $USER != $testuser ] 
  then 
   echo "This is not $testuser" 
  else 
   echo "Welcome $testuser"
  fi
  ```

### 注意

* 要测试一个字符串是否比另一个字符串大就是麻烦的开始。当要开始使用测试条件的大于或小于功能时，就会出现两个经常困扰shell程序员的问题： 
* 大于号和小于号必须转义，否则shell会把它们当作重定向符号，把字符串值当作文件名；
* 大于和小于顺序和sort命令所采用的不同。
* 在编写脚本时，第一条可能会导致一个不易察觉的严重问题。下面的例子展示了shell脚本编程初学者时常碰到的问题。

### 案例

* ```shell
  #!/bin/bash 
  # mis-using string comparisons 
  # 
  val1=baseball 
  val2=hockey 
  # 
  if [ $val1 > $val2 ] 
  then 
   echo "$val1 is greater than $val2" 
  else 
   echo "$val1 is less than $val2" 
  fi
  ```

* test9.sh

  ```shell
  #!/bin/bash 
  # mis-using string comparisons 
  # 
  val1=baseball 
  val2=hockey 
  # 
  if [ $val1 \> $val2 ]
  then 
   echo "$val1 is greater than $val2" 
  else 
   echo "$val1 is less than $val2" 
  fi
  ```

  

## 文件比较

### test 命令的文件比较功能

| 比较            | 描述                                     |
| --------------- | ---------------------------------------- |
| -d file         | 检查file是否存在并是一个目录             |
| -e file         | 检查file是否存在                         |
| -f file         | 检查file是否存在并是一个文件             |
| -r file         | 检查file是否存在并可读                   |
| -s file         | 检查file是否存在并非空                   |
| -w file         | 检查file是否存在并可写                   |
| -x file         | 检查file是否存在并可执行                 |
| -O file         | 检查file是否存在并属当前用户所有         |
| -G file         | 检查file是否存在并且默认组与当前用户相同 |
| file1 -ot file2 | 检查file1是否比file2旧                   |
| file1 -nt file2 | 检查file1是否比file2新                   |

### 案例模板

#### 检查目录

```shell
#!/bin/bash 
# Look before you leap 
# 
jump_directory=/home/arthur 
# 
if [ -d $jump_directory ] 
then 
 echo "The $jump_directory directory exists" 
 cd $jump_directory 
 ls 
else 
 echo "The $jump_directory directory does not exist" 
fi
```

#### 检查对象是否存在

```shell
#!/bin/bash 
# Check if either a directory or file exists 
# 
location=$HOME 
file_name="sentinel" 
# 
if [ -e $location ] 
then #Directory does exist 
 echo "OK on the $location directory." 
 echo "Now checking on the file, $file_name." 
 # 
 if [ -e $location/$file_name ] 
 then #File does exist 
 echo "OK on the filename" 
 echo "Updating Current Date..." 
 date >> $location/$file_name 
 # 
 else #File does not exist 
 echo "File does not exist" 
 echo "Nothing to update" 
 fi 
# 
else #Directory does not exist 
 echo "The $location directory does not exist." 
 echo "Nothing to update" 
fi 
#
```

####  检查文件

```shell
# Check if either a directory or file exists 
# 
item_name=$HOME 
echo 
echo "The item being checked: $item_name" 
echo 
# 
if [ -e $item_name ] 
then #Item does exist 
 echo "The item, $item_name, does exist." 
 echo "But is it a file?" 
 echo 
 # 
 if [ -f $item_name ] 
 then #Item is a file 
 echo "Yes, $item_name is a file." 
 # 
 else #Item is not a file 
 echo "No, $item_name is not a file." 
 fi 
# 
else #Item does not exist 
 echo "The item, $item_name, does not exist." 
 echo "Nothing to update" 
fi 
#
```

#### 检查是否可读

```shell
#!/bin/bash 
# testing if you can read a file 
pwfile=/etc/shadow 
# 
# first, test if the file exists, and is a file 
if [ -f $pwfile ] 
then 
 # now test if you can read it 
 if [ -r $pwfile ] 
 then 
 tail $pwfile 
 else 
 echo "Sorry, I am unable to read the $pwfile file" 
 fi 
else 
 echo "Sorry, the file $file does not exist" 
fi
```

#### 检查空文件

```shell
#!/bin/bash 
# Testing if a file is empty 
# 
file_name=$HOME/sentinel 
# 
if [ -f $file_name ] 
then 
 if [ -s $file_name ] 
 then 
 echo "The $file_name file exists and has data in it." 
 echo "Will not remove this file." 
# 
 else 
 echo "The $file_name file exists, but is empty." 
 echo "Deleting empty file..." 
 rm $file_name 
 fi 
else 
 echo "File, $file_name, does not exist." 
fi
```

#### 检查是否可写

```shell
#!/bin/bash 
# Check if a file is writable. 
# 
item_name=$HOME/sentinel 
echo 
echo "The item being checked: $item_name" 
echo
 echo "Yes, $item_name is a file." 
 echo "But is it writable?" 
 echo 
 [...]
 # 
 if [ -w $item_name ] 
 then #Item is writable 
 echo "Writing current time to $item_name" 
 date +%H%M >> $item_name 
 # 
 else #Item is not writable 
 echo "Unable to write to $item_name" 
 fi 
 # 
 else #Item is not a file 
 echo "No, $item_name is not a file." 
 fi
 [...]
```

#### 检查文件是否可以执行

```shell
#!/bin/bash 
# testing file execution 
# 
if [ -x test16.sh ] 
then 
 echo "You can run the script: " 
 ./test16.sh 
else 
 echo "Sorry, you are unable to execute the script" 
fi
```

#### 检查所属关系

```shell
#!/bin/bash 
# check file ownership 
# 
if [ -O /etc/passwd ] 
then 
 echo "You are the owner of the /etc/passwd file"
else 
 echo "Sorry, you are not the owner of the /etc/passwd file" 
fi 
```

#### 检查默认属组关系

```shell
#!/bin/bash 
# check file group test 
# 
if [ -G $HOME/testing ] 
then 
 echo "You are in the same group as the file" 
else 
 echo "The file is not owned by your group" 
fi
```

## **case** 命令

**语法**：

```shell
case variable in 
pattern1 | pattern2) commands1;; 
pattern3) commands2;; 
*) default commands;; 
esac
```

**解释**：case命令会将指定的变量与不同模式进行比较。如果变量和模式是匹配的，那么shell会执行为该模式指定的命令。可以通过竖线操作符在一行中分隔出多个模式模式。星号会捕获所有与已知模式不匹配的值。这里有个将if-then-else程序转换成用case命令的例子

### 案例

```shell
#!/bin/bash 
# using the case command 
# 
case $USER in 
rich | barbara) 
 echo "Welcome, $USER" 
 echo "Please enjoy your visit";; 
testing) 
 echo "Special testing account";; 
jessica) 
 echo "Do not forget to log off when you're done";; 
*) 
 echo "Sorry, you are not allowed here";; 
esac
```

