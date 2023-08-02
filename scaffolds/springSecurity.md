---
title: SpringSecurity
date: 2022-4-20
author: wflin
tags:
  - security
  - java
top: false
cover: false
toc: true
mathjax: true
summary: "权限认证"
categories:
  - java
---

## 搭建 security 环境 - springboot

1. pom 配置
   
   ```xml
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-security</artifactId>
   </dependency>
   ```
   
2. 直接运行

   ```markdown
   # user
   用户名：user
   # password
   密码随机
   ```

3. Web 权限方案

   * 认证
   * 授权

4. 设置登陆的用户名和密码

   ```markdown
   # 设置登陆的用户名和密码
   1. 通过配置文件
   2. 通过配置类
   3. 自定义编写实现类
   ```

   * 配置文件

     ```yaml
     # 修改用户名和密码
     spring:
       security:
         user:
           name: wflin
           password: wflin
     ```

   * 通过配置类

     ```java
     @Configuration
     public class SecurityConfig  extends WebSecurityConfigurerAdapter {
         @Override
         protected void configure(AuthenticationManagerBuilder auth) throws Exception {
             BCryptPasswordEncoder passwordEncoder = new BCryptPasswordEncoder();
             // 加密
             String password = passwordEncoder.encode("wflin");
             // 设置用户名和密码       
             auth.inMemoryAuthentication().
                 withUser("wflin").
                 password(password).
                 roles("wflin");
         }
         
         @Bean
         PasswordEncoder password(){
             return new BCryptPasswordEncoder();
         }
     }
     ```

   * 

5. 


