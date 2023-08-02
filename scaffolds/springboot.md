---
title: springboot学习
date: 2021-10-29
author: wflin
tags:
  - java
  - springboot
top: false
cover: false
toc: true
mathjax: true
summary: "springboot"
categories:
  - java
---

## Springboot 自动装配

- 自动配好Tomcat

- - 引入Tomcat依赖。
  - 配置Tomcat

- ```xml
  <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-tomcat</artifactId>
        <version>2.3.4.RELEASE</version>
        <scope>compile</scope>
      </dependency>
  <dependency>
  ```

- 自动配好SpringMVC

- - 引入SpringMVC全套组件
  - 自动配好SpringMVC常用组件（功能）

- 自动配好Web常见功能，如：字符编码问题

- - SpringBoot帮我们配置好了所有web开发的常见场景

- 默认的包结构

- - 主程序所在包及其下面的所有子包里面的组件都会被默认扫描进来
  - 无需以前的包扫描配置
  - 想要改变扫描路径，@SpringBootApplication(`scanBasePackages=包路径【从根包开始写】`)

- - - 或者@ComponentScan 指定扫描路径

```java
@SpringBootApplication
等同于
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan("包路径")
```

- 各种配置拥有默认值

- - 默认配置最终都是映射到某个类上，如：MultipartProperties
  - 配置文件的值最终会绑定某个类上，这个类会在容器中创建对象

- 按需加载所有自动配置项

- - 非常多的starter
  - 引入了哪些场景这个场景的自动配置才会开启
  - SpringBoot所有的自动配置功能都在 spring-boot-autoconfigure 包里面

### 注解解释

#### 1、@Configuration

- 基本使用
- **Full模式与Lite模式**

- - 示例
  - 最佳实战

- - - 配置 类组件之间无依赖关系用Lite模式加速容器启动过程，减少判断
    - 配置类组件之间有依赖关系，方法会被调用得到之前单实例组件，用Full模式



```java
#############################Configuration使用示例######################################################
/**
 * 1、配置类里面使用@Bean标注在方法上给容器注册组件，默认也是单实例的
 * 2、配置类本身也是组件
 * 3、proxyBeanMethods：代理bean的方法
 *      Full(proxyBeanMethods = true)、【保证每个@Bean方法被调用多少次返回的组件都是单实例的】
 *      Lite(proxyBeanMethods = false)【每个@Bean方法被调用多少次返回的组件都是新创建的】
 *      组件依赖必须使用Full模式默认。其他默认是否Lite模式
 *
 *
 *
 */
@Configuration(proxyBeanMethods = false) //告诉SpringBoot这是一个配置类 == 配置文件
public class MyConfig {

    /**
     * Full:外部无论对配置类中的这个组件注册方法调用多少次获取的都是之前注册容器中的单实例对象
     * @return
     */
    @Bean //给容器中添加组件。以方法名作为组件的id。返回类型就是组件类型。返回的值，就是组件在容器中的实例
    public User user01(){
        User zhangsan = new User("zhangsan", 18);
        //user组件依赖了Pet组件
        zhangsan.setPet(tomcatPet());
        return zhangsan;
    }

    @Bean("tom")
    public Pet tomcatPet(){
        return new Pet("tomcat");
    }
}


################################@Configuration测试代码如下########################################
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan("com.atguigu.boot")
public class MainApplication {

    public static void main(String[] args) {
        //1、返回我们IOC容器
        ConfigurableApplicationContext run = SpringApplication.run(MainApplication.class, args);

        //2、查看容器里面的组件
        String[] names = run.getBeanDefinitionNames();
        for (String name : names) {
            System.out.println(name);
        }

        //3、从容器中获取组件

        Pet tom01 = run.getBean("tom", Pet.class);

        Pet tom02 = run.getBean("tom", Pet.class);

        System.out.println("组件："+(tom01 == tom02));


        //4、com.atguigu.boot.config.MyConfig$$EnhancerBySpringCGLIB$$51f1e1ca@1654a892
        MyConfig bean = run.getBean(MyConfig.class);
        System.out.println(bean);

        //如果@Configuration(proxyBeanMethods = true)代理对象调用方法。SpringBoot总会检查这个组件是否在容器中有。
        //保持组件单实例
        User user = bean.user01();
        User user1 = bean.user01();
        System.out.println(user == user1);


        User user01 = run.getBean("user01", User.class);
        Pet tom = run.getBean("tom", Pet.class);

        System.out.println("用户的宠物："+(user01.getPet() == tom));



    }
}
```

#### 2、@Bean、@Component、@Controller、@Service、@Repository



#### 3、@ComponentScan、@Import

```java
 * 4、@Import({User.class, DBHelper.class})
 *      给容器中自动创建出这两个类型的组件、默认组件的名字就是全类名
 *
 *
 *
 */

@Import({User.class, DBHelper.class})
@Configuration(proxyBeanMethods = false) //告诉SpringBoot这是一个配置类 == 配置文件
public class MyConfig {
}
```

@Import 高级用法： https://www.bilibili.com/video/BV1gW411W7wy?p=8





## 文件

### 配置文件

* application.yaml

  ```yaml
  spring:
    mvc:
      view:
        prefix: classpath:/templates/
        suffix: .html
    resources:
      static-locations: classpath:/templates/,classpath:/static/
    servlet:
      multipart:
        max-file-size: 500MB
        max-request-size: 500MB
  #  激活配置哪一个配置
    profiles:
      active: local  # 本地配置生效
  ```

  

* application-local.yaml **本地开发环境**

  ```yaml
  #  指定文件上传位置
  file:
    upload:
      dir: D:\aliyunPan\Down
    download:
      dir: D:\aliyunPan\Down
  ```

* application-prod.yaml **生产环境**

  ```yaml
  #  指定文件上传位置
  file:
    upload:
      dir: /home/root/image
    download:
      dir: D:\aliyunPan\Down  
  ```

  

### 文件上传

```java
@Controller
@Slf4j
@RequestMapping("newfile")
public class NewUploadCtroller {
    @Value("${file.upload.dir}")
    private String realPath;
    @PostMapping("uploadByJarDeploy")
    public String uploadByJarDeploy(MultipartFile file2) throws IOException {
        //文件名
        String originalFilename = file2.getOriginalFilename();
        log.info("文件名：{}",originalFilename);
        log.info("文件大小：{}",file2.getSize());
        log.info("文件类型：{}",file2.getContentType());
        // 改名
        String ext = originalFilename.substring(originalFilename.lastIndexOf("."));
        String newFileName =  new SimpleDateFormat("yyyMMddHHmmSS").format(new Date())+ UUID.randomUUID().toString()+ext;
        //  上传到的位置
        file2.transferTo(new File(realPath,newFileName));
        return "index";
    }
}
```

### 文件下载

```java
@RestController
@RequestMapping("download")
@Slf4j
public class DownLoadController {

    @Value("${file.download.dir}")
    private String downPath;

    @GetMapping("down")
    public void download(String fileName, HttpServletResponse response) throws IOException {
        log.info("下载的文件名：{}", fileName);
        log.info("当前下载文件目录：{}", downPath);
        // 去指定目录读取文件
        File file = new File(downPath,fileName);
        // 将文件读取为文件输入流
        FileInputStream is = new FileInputStream(file);
        // 2.5 响应流之前一定要设置以附件的方式下载，不然就是自动下载并打开
        response.setHeader("content-disposition","attachment;fileName="+ URLEncoder.encode(fileName,"UTF-8"));
        // 3. 获取响应输出流
        ServletOutputStream os = response.getOutputStream();
        // 4. 输入流复制给输出流
        int len = 0;
        byte[] b = new byte[1024];
        while (true) {
            len = is.read(b);
            if (len == -1) break;
            os.write(b, 0, len);
        }
        // 释放资源
        is.close();
    }
}
```

### 文件下载简化

```java
@RestController
@RequestMapping("download")
@Slf4j
public class DownLoadController {

    @Value("${file.download.dir}")
    private String downPath;

    @GetMapping("down")
    public void download(String fileName, HttpServletResponse response) throws IOException {
        log.info("下载的文件名：{}", fileName);
        log.info("当前下载文件目录：{}", downPath);
        // 1. 去指定目录读取文件
        File file = new File(downPath,fileName);
        // 2. 将文件读取为文件输入流
        FileInputStream is = new FileInputStream(file);
        // 2.5 响应流之前一定要设置以附件的方式下载，不然就是自动下载并打开
        response.setHeader("content-disposition","attachment;fileName="+ URLEncoder.encode(fileName,"UTF-8"));
        // 3. 获取响应输出流
        ServletOutputStream os = response.getOutputStream();
        // 4. 输入流复制给输出流
        FileCopyUtils.copy(is, os);
    }
}

```



## 拦截器

### 自定义拦截器

```java
public class MyInterceptor  implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("========1=======");
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("=========3========");
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("=========4========");
    }
}

```

### 注册拦截器

* springboot 1.x 版本

```java
@Configuration
public class InterceptorConfig extends WebMvcConfigurationSupport {
    @Override
    protected void addInterceptors(InterceptorRegistry registry) {
        registry
                // 添加拦截器
                .addInterceptor(new MyInterceptor())
                //添加拦截路径
                .addPathPatterns("/hello/**")
                //添加哪些请求路径不经过拦截器
                .excludePathPatterns("/hello/world");
    }
}
```

* springboot 2.x 版本

```java
@Configuration
public class InterceptorConfig implements WebMvcConfigurer {
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry
                // 添加拦截器
                .addInterceptor(new MyInterceptor())
                //添加拦截路径
                .addPathPatterns("/hello/**")
                //添加哪些请求路径不经过拦截器
                .excludePathPatterns("/hello/world");
    }
}
```



## War 包部署

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

  ```java
  /* 一旦使用 war 包部署注意
  	1. application.yml 中配置 port context-path 先效
  	2. 访问时使用打成 war 包的名字和外部 tomcat 端口号进行访问项目
  */
  ```

  





