---
title: springboot-tools
author: wflin
tags:
  - java
top: false
cover: false
toc: true
mathjax: true
summary: "Java"
categories:
  - Java 开发常用配置
---

# POM

## 清除本地仓库的坏包

```java
//列出File的一些常用操作
public class util {
    /**
     * 遍历指定目录下（包括其子目录）的所有文件，并删除以 lastUpdated 结尾的文件
     * @param dir 目录的位置 path
     * @throws IOException
     */
    public static void listDirectory(File dir) throws IOException {
        if (!dir.exists())
            throw new IllegalArgumentException("目录：" + dir + "不存在.");
        if (!dir.isDirectory()) {
            throw new IllegalArgumentException(dir + " 不是目录。");
        }
        File[] files = dir.listFiles();
        if (files != null && files.length > 0) {
            for (File file : files) {
                if (file.isDirectory())
                    //递归
                    listDirectory(file);
                else{ // 删除以 lastUpdated 结尾的文件
                    String fileName = file.getName();
                    boolean isLastupdated = fileName.toLowerCase().endsWith("lastupdated");
                    if (isLastupdated){
                        boolean is_delete = file.delete();
                        System.out.println("删除的文件名 => " + file.getName() + "  || 是否删除成功？ ==> " + is_delete);
                    }
                }
            }
        }
    }
 
    public static void main(String[] args) throws IOException {
        // 指定maven的本地仓库路径
        listDirectory(new File("path"));
    }
}
```



## boot

### spirngboot

```xml
<boot.version>2.2.2.RELEASE</boot.version>

<!--spring boot 2.2.2-->
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-dependencies</artifactId>
            <version>${boot.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>

 <!--或者-->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>${boot.version}</version>
</parent>

 <!--或者-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <type>pom</type>
    <version>${boot.version}</version>
</dependency>
```

### springtest

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <exclusions>
        <exclusion>
            <groupId>org.junit.vintage</groupId>
            <artifactId>junit-vintage-engine</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```



## mysql

### mysql-5

```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.49</version>
    <scope>runtime</scope>
</dependency>
```

### mysql-8

```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.25</version>
    <scope>runtime</scope>
</dependency>
```

## druid

```xml
 <dependency>
     <groupId>com.alibaba</groupId>
     <artifactId>druid</artifactId>
     <version>1.1.9</version>
</dependency>
```

## junit

```xml
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
</dependency>
```

## lombok

```xml
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.16.10</version>
</dependency>
```

## fastJosn2

```xml
<dependency>
    <groupId>com.alibaba.fastjson2</groupId>
    <artifactId>fastjson2</artifactId>
    <version>2.0.17</version>
</dependency>
```

## mybatis相关

### mybatis-plus

```xml
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.5.1</version>
</dependency>
```

### mybatis

```xml
 <dependency>
     <groupId>org.mybatis.spring.boot</groupId>
     <artifactId>mybatis-spring-boot-starter</artifactId>
     <version>3.4.6</version>
</dependency>
```

## jackson

```xml
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
```



## hutools

```xml
<!--hutool-->
<dependency>
    <groupId>cn.hutool</groupId>
    <artifactId>hutool-all</artifactId>
    <version>5.7.17</version>
</dependency>
```

## redis

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

## dubbo

```xml
<dubbo.version>2.7.8</dubbo.version>

<dependency>
    <groupId>org.apache.dubbo</groupId>
    <artifactId>dubbo-spring-boot-starter</artifactId>
    <version>${dubbo.version}</version>
    <type>pom</type>
</dependency>
```

```xml
<zookeeper.version>2.7.8</zookeeper.version>

<dependency>
    <groupId>org.apache.dubbo</groupId>
    <artifactId>dubbo-registry-zookeeper</artifactId>
    <version>${zookeeper.version}</version>
    <type>pom</type>
</dependency>
```

## zookeeper

### 服务端

```xml
<!-- SpringCloud整合zookeeper客户端 -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zookeeper-discovery</artifactId>
</dependency>
```

### 客户端

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zookeeper-discovery</artifactId>
    <!--先排除自带的zookeeper-->
    <exclusions>
        <exclusion>
            <groupId>org.apache.zookeeper</groupId>
            <artifactId>zookeeper</artifactId>
        </exclusion>
    </exclusions>
</dependency>
<!--添加zookeeper3.4.9版本-->
<dependency>
    <groupId>org.apache.zookeeper</groupId>
    <artifactId>zookeeper</artifactId>
    <version>3.4.9</version>
</dependency>

```

## alibaba 组件

### nacos

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-alibaba-dependencies</artifactId>
    <version>${cloud.version}</version>
    <type>pom</type>
    <scope>import</scope>
</dependency>
```



```xml
<dependency>
    <groupId>com.alibaba.nacos</groupId>
    <artifactId>nacos-client</artifactId>
    <version>1.1.1</version>
</dependency>
```

# 配置

## mysql 

### mysql 8

#### 连接配置

```yaml
spring:
  #数据源
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource
    driver-class-name: com.mysql.jdbc.Driver
    url: jdbc:mysql://ip:port/database?useUnicode=true&useJDBCCompliantTimezoneShift=true&useLegacyDatetimeCode=false&serverTimezone=UTC&characterEncoding=UTF-8&useSSL=false
    username: root
    password: password
```



#### mybatis-plus配置

```yaml
mybatis-plus:
  mapper-locations: classpath:mapper/**/*.xml
  typeAliasesPackage: com.wflin.pojo.*
  configuration:
    map-underscore-to-camel-case: false   	# 关闭驼峰
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
  global-config:
    db-config:
      id-type: auto
```



## springboot+mybatis-plus+druid

```yaml
spring:
  #数据源
  datasource:
  	driver-class-name: com.mysql.jdbc.Driver
  	type: com.alibaba.druid.pool.DruidDataSource
    url: jdbc:mysql://ip:port/database?useUnicode=true&useJDBCCompliantTimezoneShift=true&useLegacyDatetimeCode=false&serverTimezone=UTC&characterEncoding=UTF-8&useSSL=false
    username: root
    password: nba1234567

  jackson:
    default-property-inclusion: non_null # JSON处理时忽略非空字段
    date-format: yyyy-MM-dd HH:mm:ss
    time-zone: GMT+8

mybatis-plus:
  mapper-locations: classpath:mapper/**/*.xml
  typeAliasesPackage: com.wflin.pojo.*
  configuration:
    map-underscore-to-camel-case: false   	# 关闭驼峰
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
  global-config:
    db-config:
      id-type: auto
```

## redis

### springboot+redisTemplate

```yaml
spring:
  redis:
    host: "192.168.43.21"
    port: 6389
    database: 0
    connect-timeout: 1800000
    lettuce:
      pool:
        max-active: 20
        max-wait: -1
        max-idle: 5
        min-idle: 0
```



### Jedis连接

```java
 //创建 Jedis 对象
  Jedis jedis = new Jedis("192.168.43.21", 6379);
  //测试是否可以连接上
  String ping = jedis.ping();
  System.out.println(ping);
  jedis.close();
```





# 打包

## jar包

### 默认打包插件

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <configuration>
                <executable>true</executable>
            </configuration>
        </plugin>
    </plugins>
</build>
```



### jar 包部署

```xml
<build>
    <plugins>
        <!--打包时跳过单元测试插件-->
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <configuration>
                <source>1.8</source>
                <target>1.8</target>
                <encoding>UTF-8</encoding>
                <fork>true</fork>
            </configuration>
        </plugin>

        <!--打包插件-->
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <version>${boot.version}</version>
            <configuration>
                <!--设置主类位置-->
                <!-- com.ccww.Application -->
                <mainClass>主类路径</mainClass>
            </configuration>
            <executions>
                <execution>
                    <id>repackage</id>
                    <goals>
                        <goal>repackage</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```



### 将有webapp的路径的也打入jar包

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <configuration>
                <source>1.8</source>
                <target>1.8</target>
                <encoding>UTF-8</encoding>
                <fork>true</fork>
            </configuration>
        </plugin>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <version>2.3.7.RELEASE</version>
            <configuration>
                <!--设置主类位置-->
                <!-- com.ccww.Application -->
                <mainClass>主类路径</mainClass>
            </configuration>
            <executions>
                <execution>
                    <id>repackage</id>
                    <goals>
                        <goal>repackage</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>

    <resources>
        <resource>
            <directory>${basedir}/src/main/webapp</directory>
            <!--注意此次必须要放在此目录下才能被访问到-->
            <targetPath>META-INF/resources</targetPath>
            <includes>
                <include>**/**</include>
            </includes>
        </resource>
        <resource>
            <directory>${basedir}/src/main/resources</directory>
            <includes>
                <include>**/**</include>
            </includes>
        </resource>
    </resources>
</build>
```



## war包

### war 包部署(boot 打war包)

* 配置打包方式

```xml
<--! 加入打包方式 -->
<packaging>war</packaging>
```



* 在插件中指定插入类

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <!--使用热部署出现中文乱码解决方案-->
            <configuration>
                <fork>true</fork>
                <!-- 增加 JVM 参数-->
                <jvmArguements>Dfile.encoding=UTF-8</jvmArguements>
                <!-- 指定主类入口-->
                <mainClass>主类</mainClass>
            </configuration>
        </plugin>
    </plugins>
</build>
```



* 排除内嵌 tomcat


```xml
<!--去掉内 tomcat -->
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-tomcat</artifactId>
	<scope>provided</scope>
</dependency>
<!--去掉使用内嵌 tomcat 解析 jsp-->
<dependency>
	<groupId>org.apache.tomcat</groupId>
	<artifactId>tomcat-jsp-api</artifactId>
	<scope>provided</scope>
</dependency>
```



* 配置入口类


```java
@SpringBootApplication
@MapperScan("com.wflin.dao")
public class MainApplication extends SpringBootServletInitializer{

	public static void main(String[] args) {
		SpringApplication.run(MainApplication.class, args);
	}

	@Override
	protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
		return builder.sources(MainApplication.class);
	}
}
```



* 打包测试

```txt
一旦使用 war 包部署注意
	1. application.yml 中配置 port context-path 先效
	2. 访问时使用打成 war 包的名字和外部 tomcat 端口号进行访问项目

```
