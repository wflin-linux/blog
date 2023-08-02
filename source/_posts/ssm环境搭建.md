---
title: ssm 环境搭建
date: 2021-08-27 11:41:03
author: wflin
top: false
cover: false
toc: false
mathjax: false
summary: 
  ssm 环境搭建
tags:
  - java
	- spring
	- mybatis
categories:
 	- java
	- javaweb
password:
---

## 一、IDEA 创建 POM 项目

* 打开 idea 选择新建项目，并选择 maven 的模板文件,模板为图片光标所指的那一个

  **maven-archetype-webapp**

  ![image-20221212220330593](ssm%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA/image-20221212220330593.png)

* 设置项目名和项目保存位置

  * 项目的包的命名顺序一般为域名倒序指的是：假设要建立一个 商店的项目，而这个商店的域名是 shop.test.com,那么这个项目的包的顺序应该是 com.test.shop，一般我们选取 项目名字作为项目的包名就是说 Artifactid 为 shop

  ![image-20221212215058992](ssm%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA/image-20221212215058992.png)

* 上一步选择 next 之后 会进行 maven 配置（如果你的 idea 之前在 setting中配置好的那么这一步应该是啥也不用改），点击 finish

  ![image-20221212215639842](ssm%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA/image-20221212215639842.png)

* 完成之后会进入 idea 工程的主界面

  ![image-20221212220510195](ssm%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA/image-20221212220510195.png)

* 创建 Javaweb 的目录结构

  * 在 main 目录下创建 java 和 resource 文件夹

  ![image-20221212220720662](ssm%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA/image-20221212220720662.png)

  创建后

  ![image-20221212220741561](ssm%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA/image-20221212220741561.png)

  注意：如果你所创建的 java 和 resource 文件夹没有变颜色可以通过选中文件夹然后鼠标右键的 mark as 进行颜色标注,如果标记错误可以通过 unmark as 取消后重新标记

  ![image-20221212221036715](ssm%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA/image-20221212221036715.png)

## 二、添加项目 POM 的依赖

### pom.xml

* **读者自行设定自己使用 MySQL 的版本**

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
    <spring.version>5.1.6.RELEASE</spring.version>
    <mysql.version>8.0.25</mysql.version> <!--使用的是 MySQL8是这个版本 -->
    <mysql.version>5.1.49</mysql.version> <!--使用的是 MySQL5是这个版本 -->
  </properties>
  <dependencies>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-core</artifactId>
      <version>${spring.version}</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>${spring.version}</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-beans</artifactId>
      <version>${spring.version}</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-test</artifactId>
      <version>${spring.version}</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-web</artifactId>
      <version>${spring.version}</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>${spring.version}</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-aop</artifactId>
      <version>${spring.version}</version>
    </dependency>

    <!--lombok-->

    <dependency>
      <groupId>org.projectlombok</groupId>
      <artifactId>lombok</artifactId>
      <version>1.16.10</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-expression</artifactId>
      <version>${spring.version}</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-tx</artifactId>
      <version>${spring.version}</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-jdbc</artifactId>
      <version>${spring.version}</version>
    </dependency>
    <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>druid</artifactId>
      <version>1.1.9</version>
    </dependency>
    <!--<dependency>-->
    <!--  <groupId>com.baomidou</groupId>-->
    <!--  <artifactId>mybatis-plus</artifactId>-->
    <!--  <version>3.1.0</version>-->
    <!--</dependency>-->
        <dependency>
          <groupId>org.mybatis</groupId>
          <artifactId>mybatis-spring</artifactId>
          <version>1.3.2</version>
        </dependency>
        <dependency>
          <groupId>org.mybatis</groupId>
          <artifactId>mybatis</artifactId>
          <version>3.4.6</version>
        </dependency>
    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>${mysql.version}</version>
    </dependency>
    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>javax.servlet-api</artifactId>
      <version>4.0.1</version>
      <scope>provided</scope>
    </dependency>

    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-core</artifactId>
      <version>2.9.5</version>
    </dependency>
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-databind</artifactId>
      <version>2.9.5</version>
    </dependency>
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-annotations</artifactId>
      <version>2.9.5</version>
    </dependency>
    <!-- https://mvnrepository.com/artifact/org.json/json -->
    <dependency>
      <groupId>org.json</groupId>
      <artifactId>json</artifactId>
      <version>20180130</version>
    </dependency>
  </dependencies>
```

* 加入之后点击 maven 刷新

  

  ![image-20221212221530194](ssm%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA/image-20221212221530194.png)

  * 点击导入刷新，（最好保证网络通畅，因为断网的话文件会损坏，maven 没有断电续传）

  ![image-20221212221638192](ssm%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA/image-20221212221638192.png)

## 三、 在 resource 目录下创建以下文件

### MySQL5

#### spring-database.xml 

* 项目数据库的配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/mvc
        http://www.springframework.org/schema/mvc/spring-mvc.xsd
        http://www.springframework.org/schema/tx
       http://www.springframework.org/schema/tx/spring-tx.xsd">

    <!--1. 配置数据源:阿里德鲁伊数据源  -->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <!--1. mysql 的版本，这个版本是 5 的版本  -->
        <property name="driverClassName" value="com.mysql.jdbc.Driver" />
        <!-- 修改为自己的数据库的 ip 和 端口 ->
        <property name="url" value="jdbc:mysql://IP:Port/database" />
		<!--填入自己的用户名和密码 -->
        <property name="username" value="root" />
        <property name="password" value="root" />
    </bean>

    <!--2. 配置mybatis的SqlSession的工厂: SqlSessionFactoryBean dataSource:引用数据源 -->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <!--dataSource属性指定要用到的连接池-->
        <property name="dataSource" ref="dataSource" />
        <!--configLocation属性指定mybatis的核心配置文件-->
        <property name="configLocation" value="classpath:mybatis.xml" />
        <!-- 自动扫描mapping.xml文件 -->
        <property name="mapperLocations" value="classpath:mapper/*.xml"/>
    </bean>

    <!--3. 自动扫描mybatis中pojo接口包，
           并且自动注入第二项创建的sessionfactory，初始化所有的mapper接口对象 -->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.test.shop.mapper" />
    </bean>

    <!--4. 扫描service注解 -->
    <context:component-scan base-package="com.test.shop.service"/>

    <!--5. 配置事务管理器  -->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource" />
    </bean>

    <!--6. 开启注解进行事务管理   transaction-manager：引用上面定义的事务管理器-->
    <tx:annotation-driven transaction-manager="transactionManager" />

</beans>
```



### 注意点：

* 上面的 MySQL 的 URL 写法只支持 MySQL5 的版本，Mysql8的不是这么配置

### MySQL8：(自行比较和 5 的 url)

#### spring-database.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/tx
       http://www.springframework.org/schema/tx/spring-tx.xsd">

    <!--1. 配置数据源:阿里数据源  -->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="com.mysql.cj.jdbc.Driver" />
        <property name="url" value="jdbc:mysql://IP:port/database?allowMultiQueries=true&amp;useSSL=false&amp;serverTimezone=GMT&amp;characterEncoding=utf-8&amp;autoReconnect=true" />
        <property name="username" value="root" />
        <property name="password" value="root" />
    </bean>

    <!--2. 配置mybatis的SqlSession的工厂: SqlSessionFactoryBean dataSource:引用数据源 -->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <!--dataSource属性指定要用到的连接池-->
        <property name="dataSource" ref="dataSource" />
        <!--configLocation属性指定mybatis的核心配置文件-->
        <property name="configLocation" value="classpath:mybatis.xml" />
        <!-- 自动扫描mapping.xml文件 -->
        <property name="mapperLocations" value="classpath:mapper/*.xml"/>
    </bean>

    <!--3. 自动扫描mybatis中pojo接口包，
           并且自动注入第二项创建的sessionfactory，初始化所有的mapper接口对象 -->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.blog.mapper" />
    </bean>

    <!--4. 扫描service注解 -->
    <context:component-scan base-package="com.blog.service"/>

    <!--5. 配置事务管理器  -->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource" />
    </bean>

    <!--6. 开启注解进行事务管理   transaction-manager：引用上面定义的事务管理器-->
    <tx:annotation-driven transaction-manager="transactionManager" />

</beans>
```

## 四、创建 springmvc 的配置文件

### spring-mvc.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/mvc
        http://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <!--1.开启Springioc 自动扫描注解包  只对mvc的包做扫描  -->
    <context:component-scan base-package="com.test.shop.controller"/>

    <!--2. 开启注解，对jackson数据支持 -->
    <mvc:default-servlet-handler/>
    
    <!--自定义消息转换器的编码,解决后台传输json回前台时，中文乱码问题 -->
    <bean id="stringHttpMessageConverter"
          class="org.springframework.http.converter.StringHttpMessageConverter">
        <constructor-arg value="UTF-8" />
    </bean>

    <mvc:default-servlet-handler/>
    <mvc:annotation-driven>
        <mvc:message-converters>
            <ref bean="stringHttpMessageConverter" />
        </mvc:message-converters>
    </mvc:annotation-driven>



    <!--3.配置视图解析器  -->
    <!-- 配置视图解析器:如何把handle方法返回值解析为实际的物理视图
         name="prefix"代表前面的目录，为空代表根目录  name="suffix"代表返回的地址为后缀为.JSP的文件 -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/" />
        <property name="suffix" value=".jsp" />
    </bean>
</beans>
```

## 五、mybatis 的配置文件

### mybatis.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <settings>
        <!--如果value为true的时候，当返回内容为空时，那么返回的是一个空的对象，而不是null
            如果这里的value为false(默认值)，mybatis在没有查询到内容的时候返回null-->
        <setting name="returnInstanceForEmptyRow" value="true"/>
        <!--显示sql语句，此行配置如果删除，则不在后台显示SQL语句-->
        <setting name="logImpl" value="STDOUT_LOGGING"/>

        <!--开启驼峰映射-->
        <setting name="mapUnderscoreToCamelCase" value="true" />
    </settings>

    <mappers>
        <!-- 自动扫描包内的Mapper接口与配置文件
             接口文件名要与mapper文件名相同
             纳入spring管理之后，不再需要扫描-->
        <!--<package name="cn/thinknovo/pojo"/>-->
    </mappers>
</configuration>
```

## 六、更换或者下载 web.xml（怎么下载自行百度）

* 更换原因：这个模板默认的配置文件版本是 3 的版本不支持注解开发，所以要升级为 4 的版本，直接复制下面就行。

### web.xml

* 模板有大锅（要替换 web.xml 文件）

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
  <!--Spring核心监听器 -->
  <!--在服务器启动时加载Spring容器，且只会加载一次 -->
  <context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:spring-database.xml</param-value>
  </context-param>
  <listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
  </listener>

  <!--配置springmvc -->
  <servlet>
    <servlet-name>dispatcherServlet</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <!--配置初始化参数：作用是配置SpringMVC配置文件的位置和名称 -->
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:spring-mvc.xml</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
  </servlet>

  <servlet-mapping>
    <servlet-name>dispatcherServlet</servlet-name>
    <url-pattern>/</url-pattern>
  </servlet-mapping>

  <!--配置由Spring 提供的针对中文乱码的编码过滤器 -->
  <!-- 编码过滤器 -->
  <filter>
    <filter-name>CharacterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
      <param-name>encoding</param-name>
      <param-value>UTF-8</param-value>
    </init-param>
  </filter>
  <filter-mapping>
    <filter-name>CharacterEncodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
  </filter-mapping>
</web-app>
```

## 七、mybatis 的模板文件（在mybatis 写 sql 的文件）

### mybatis-template.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.xly.mapper.UserMapper">
    
</mapper>
```

# 整个项目搭建完成

## 包结构

>java
>
>​	com.xxx
>
>​		controller
>
>​		mapper
>
>​		entity
>
>​		service
>
>​		service
>
>​				serviceImpl
>
>​		utils
>
>resource
>
>​	mapper/*.xml

![image-20221212223821563](ssm%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA/image-20221212223821563.png)

## 在controller里编写测试

```java
@RestController
public class UserController{
   @GetMapping()
   public String test(){
       return "test";
   }
}
```

## 配置tomcat

* 第一步

![image-20221212224008654](ssm%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA/image-20221212224008654.png)

* 第二步

![image-20221212224128421](ssm%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA/image-20221212224128421.png)

![image-20221212224208815](ssm%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA/image-20221212224208815.png)

* 第三步

![image-20221212224243218](ssm%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA/image-20221212224243218.png)

* 第四步 ： 配置 tomcat 的位置

![image-20221212224310996](ssm%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA/image-20221212224310996.png)

* 第五步

![image-20221212224354239](ssm%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA/image-20221212224354239.png)

* 第六步

![image-20221212224428293](ssm%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA/image-20221212224428293.png)

* 第七步

![image-20221212224502890](ssm%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA/image-20221212224502890.png)

* 第八步

![image-20221212224529214](ssm%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA/image-20221212224529214.png)

* 第九步

![image-20221212224557045](ssm%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA/image-20221212224557045.png)

* 第十步

### 启动服务访问并访问

* [http://127.0.0.1:8080](http://127.0.0.1:8080)

# 实例测试

## SQL 建表

```sql
CREATE TABLE `tb_user` (
	`id` INT NOT NULL AUTO_INCREMENT,
	`user_name` VARCHAR ( 255 ) CHARACTER 
	SET utf8 COLLATE utf8_bin DEFAULT NULL,
	`password` VARCHAR ( 255 ) COLLATE utf8_bin DEFAULT NULL,
PRIMARY KEY ( `id` ) 
) ENGINE = INNODB AUTO_INCREMENT = 8 DEFAULT CHARSET = utf8mb3 COLLATE = utf8_bin;
```

## spring 代码

### User.java

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class User implements Serializable {
    private Long id;
    private String userName;
    private String password;
}
```

### Usercontroller.java

```java
@RestController
@RequestMapping("user")
public class UserController {

    @Resource
    private UserService userService;

    @GetMapping()
    public List<User> queryUsers(){
        return userService.queryUsers();
    }
}
```

### UserService.java  -->(interface)

```java
List<User> queryUsers();
```

### UserServiceImpl.java

```java
public List<User> queryUsers() {
    return userMapper.selectUsers();
}
```

### UserMapper.java  -->(interface)

```java
List<User> selectUsers();
```

### UserMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.test.mapper.UserMapper">

    <select id="selectUsers" resultType="com.test.pojo.User">
        select id,user_name,password
        from tb_user
    </select>

</mapper>
```

## 注意：

* **如果前面在 spring-database.xml 中注释掉了扫描 xml 文件的配置的话，需要先开启之后再运行（因为有时候不注释配置文件报错会导致项目无法运行成功，所以在没有业务的时候为了测试系统框架是否搭建成功，需要将 扫描 xml 的配置注释，有业务的时候再放开就行了）**

  ![image-20221219223620502](ssm%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA/image-20221219223620502.png)

## 测试：

### 浏览器输入：[http://127.0.0.1:8080/user](http://127.0.0.1:8080/user)

### 结果显示：

![image-20221219224142096](ssm%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA/image-20221219224142096.png)