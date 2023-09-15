---
title: 编码常用类和方法
author: wflin
date: 2023-2-19
top: false
cover: false
toc: false
mathjax: false
summary: 平时编码一些常用方法的封装
tags:
  - java
  - vue
  - javascript
categories:
  - code-tools
---

# java

## boot常用pom

```xml
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>

        <!--jar 管理-->
        <boot.version>2.2.2.RELEASE</boot.version>
        <mysql.vsersion>5.1.49</mysql.vsersion>
		<!--<mysql.version>8.0.25</mysql.version>-->
        <druid.version>1.2.3</druid.version>
        <lombok.version>1.18.24</lombok.version>
        <log4j.version>1.2.17</log4j.version>
        <mybatisPlus.version>3.5.1</mybatisPlus.version>
        <hutools.version>5.7.17</hutools.version>
    </properties>

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


    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <version>${boot.version}</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
            <scope>test</scope>
            <version>${boot.version}</version>
        </dependency>

        <!--连接数据库-->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>${druid.version}</version>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>${mysql.version}</version>
            <scope>runtime</scope>
        </dependency>

        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>${lombok.version}</version>
        </dependency>
        
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>${log4j.version}</version>
        </dependency>

        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>${mybatisPlus.version}</version>
        </dependency>
        
        <!--hutool-->
        <dependency>
            <groupId>cn.hutool</groupId>
            <artifactId>hutool-all</artifactId>
            <version>${hutools.version}</version>
        </dependency>
    </dependencies>
```

[如果要更换或者添加某些依赖的版本](./springboot-tools.html)

## 常用Java类

### 跨域配置

```java
@Configuration
public class CorsConfig implements WebMvcConfigurer {

    static final String ORIGINS[] =new String[] {"GET","POST","HEAD","PUT","DELETE","OPTIONS"};

    @Override
    public void addCorsMappings(CorsRegistry registry) {
        WebMvcConfigurer.super.addCorsMappings(registry);
        registry.addMapping("/**")
            .allowedOrigins("*")
            .allowedMethods("GET","POST","HEAD","PUT","DELETE","OPTIONS")
            .allowCredentials(true)
            .maxAge(3600)
            .allowedHeaders("*");
    }
```





### 生成token

```java
public class JWTUtils {
    final static String SING = "fajhf@@$";

    /**
     * 生成 token,header.payload.sing
     *
     * @param map 要放入 token 的字段
     * @return token  返回 token
     */
    public static  String getToken(Map<String,String> map){
        Calendar instance = Calendar.getInstance();
        // 默认7 天过期
        instance.add(Calendar.DATE,7);
        // 创建 JWTbuilder
        JWTCreator.Builder builder = JWT.create();
        // 遍历map
        // payload
        map.forEach((k,v)->{
            builder.withClaim(k,v);
        });
        // 指定过期
        builder.withExpiresAt(instance.getTime());
        //sgin
        String token = builder.sign(Algorithm.HMAC256(SING));
        return token;
    }

    /**
     * 验证 token
     * @param token 传入要验证的 token
     */
    public static DecodedJWT verifyToekn(String token){
       return JWT.require(Algorithm.HMAC256(SING)).build().verify(token);
    }


    /**
     *  这一步不是必须，可以直接整合到验证 token 去
     * @param token
     * @return
     */
    public static DecodedJWT getTokenInfo(String token){
        DecodedJWT verify = JWT.require(Algorithm.HMAC256(SING)).build().verify(token);
        return verify;
    }
}
```



### 拦截器

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

```java
public class MyInterceptor  implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("========1=======");
        String token = request.getHeader("token");
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



### 生成验证码

```java
public class VerifyCodeUtil {

    public static String getVerifyCode() {
        String str = "";
        char[] ch = {'0', '1', '2', '3', '4', '5', '6', '7', '8', '9'};
        Random random = new Random();
        for (int i = 0; i < 6; i++) {
            char num = ch[random.nextInt(ch.length)];
            str += num;
        }
        return str;
    }

    public static String getVerifyCode(int codeSize) {
        String str = "";
        char[] ch = {'0', '1', '2', '3', '4', '5', '6', '7', '8', '9'};
        Random random = new Random();
        for (int i = 0; i < codeSize; i++) {
            char num = ch[random.nextInt(ch.length)];
            str += num;
        }
        return str;
    }
}
```



### Redis序列化和缓存管理

```java
@EnableCaching
@Configuration
public class RedisConfig extends CachingConfigurerSupport {

    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory factory) {
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        RedisSerializer<String> redisSerializer = new StringRedisSerializer();
        Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
        ObjectMapper om = new ObjectMapper();
        om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
        jackson2JsonRedisSerializer.setObjectMapper(om);
        template.setConnectionFactory(factory);
//key序列化方式
        template.setKeySerializer(redisSerializer);
//value序列化
        template.setValueSerializer(jackson2JsonRedisSerializer);
//value hashmap序列化
        template.setHashValueSerializer(jackson2JsonRedisSerializer);
        return template;
    }

    @Bean
    public CacheManager cacheManager(RedisConnectionFactory factory) {
        RedisSerializer<String> redisSerializer = new StringRedisSerializer();
        Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
//解决查询缓存转换异常的问题
        ObjectMapper om = new ObjectMapper();
        om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
        jackson2JsonRedisSerializer.setObjectMapper(om);
// 配置序列化（解决乱码的问题）,过期时间600秒
        RedisCacheConfiguration config = RedisCacheConfiguration.defaultCacheConfig()
                .entryTtl(Duration.ofSeconds(600))
                .serializeKeysWith(RedisSerializationContext.SerializationPair.fromSerializer(redisSerializer))
                .serializeValuesWith(RedisSerializationContext.SerializationPair.fromSerializer(jackson2JsonRedisSerializer))
                .disableCachingNullValues();
        RedisCacheManager cacheManager = RedisCacheManager.builder(factory)
                .cacheDefaults(config)
                .build();
        return cacheManager;
    }
}
```



### 响应体封装

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
@Accessors(chain = true)
public class Result implements Serializable {
    /**
     * 返回值
     */
    private Integer code;

    /**
     * 提示信息
     */
    private String msg;

    /**
     * 数据
     */
    private Object data;

    /**
     * 响应成功无信息
     * @return
     */
    public static Result success(){
        return new Result().setCode(200).setMsg("success");
    }

    /**
     * 响应成功携带信息
     * @param msg
     * @return
     */
    public static Result success(String msg){
        return success().setMsg(msg);
    }

    /**
     * 响应成功携带信息和数据
     * @param msg
     * @param data
     * @return
     */
    public static Result success(String msg,Object data){
        return success().setMsg(msg).setData(data);
    }

    /**
     * 响应成功只携带数据
     * @param data
     * @return
     */
    public static Result success(Object data){
        return success().setData(data);
    }

    /**
     * 响应失败无提示
     * @return
     */
    public static Result failure(){
        return new Result().setCode(201);
    }

    /**
     * 响应失败有提示
     * @param msg
     * @return
     */
    public static Result failure(String msg){
        return failure().setMsg(msg);
    }

    /**
     * 响应失败有提示带数据
     * @param msg
     * @return
     */
    public static Result failure(String msg,Object error){
        return failure().setMsg(msg).setData(error);
    }
}
```



### ThreadLocal线程变量

```java
public class ManagerThreadLocalUtil {

    private ManagerThreadLocalUtil() {
    }

    // 线程变量隔离
    private static final ThreadLocal<Manager> LOCAL = new ThreadLocal<>();

    public static void put(DTO dto) {
        LOCAL.set(sysManager);
    }

    public static Manager get() {
        return LOCAL.get();
    }

    public static void remove() {
        LOCAL.remove();
    }
}
```



### 加密

```java
public class PasswordEncoder {

    public static String encode(String password) {
        // 生成盐
        String salt = RandomUtil.randomString(20);
        // 加密
        return encode(password,salt);
    }
    private static String encode(String password, String salt) {
        // 加密
        return salt + "@" + DigestUtils.md5DigestAsHex((password + salt).getBytes(StandardCharsets.UTF_8));
    }
    public static Boolean matches(String encodedPassword, String rawPassword) {
        if (encodedPassword == null || rawPassword == null) {
            return false;
        }
        if(!encodedPassword.contains("@")){
            throw new RuntimeException("密码格式不正确！");
        }
        String[] arr = encodedPassword.split("@");
        // 获取盐
        String salt = arr[0];
        // 比较
        return encodedPassword.equals(encode(rawPassword, salt));
    }
}
```



### 正则方法

```java
public class RegexUtils {
    /**
     * 是否是无效手机格式
     * @param phone 要校验的手机号
     * @return true:符合，false：不符合
     */
    public static boolean isPhoneInvalid(String phone){
        return mismatch(phone, RegexPatterns.PHONE_REGEX);
    }
    /**
     * 是否是无效邮箱格式
     * @param email 要校验的邮箱
     * @return true:符合，false：不符合
     */
    public static boolean isEmailInvalid(String email){
        return mismatch(email, RegexPatterns.EMAIL_REGEX);
    }

    /**
     * 是否是无效验证码格式
     * @param code 要校验的验证码
     * @return true:符合，false：不符合
     */
    public static boolean isCodeInvalid(String code){
        return mismatch(code, RegexPatterns.VERIFY_CODE_REGEX);
    }

    // 校验是否不符合正则格式
    private static boolean mismatch(String str, String regex){
        if (StrUtil.isBlank(str)) {
            return true;
        }
        return !str.matches(regex);
    }
}
```

```java
package com.largeProject.utils;

public abstract class RegexPatterns {
    /**
     * 手机号正则
     */
    public static final String PHONE_REGEX = "^1([38][0-9]|4[579]|5[0-3,5-9]|6[6]|7[0135678]|9[89])d{8}$";
    /**
     * 邮箱正则
     */
    public static final String EMAIL_REGEX = "^[a-zA-Z0-9_-]+@[a-zA-Z0-9_-]+(.[a-zA-Z0-9_-]+)+$";
    /**
     * 密码正则。4~32位的字母、数字、下划线
     */
    public static final String PASSWORD_REGEX = "^w{4,32}$";
    /**
     * 验证码正则, 6位数字或字母
     */
    public static final String VERIFY_CODE_REGEX = "^[a-zA-Zd]{6}$";

}
```



### mybatisplus分页插件

```java
@Configuration
@MapperScan("mapperpath")
public class MybatisPlusConfig {

    /**
     * 新的分页插件,一缓和二缓遵循mybatis的规则,需要设置 MybatisConfiguration#useDeprecatedExecutor = false 避免缓存出现问题(该属性会在旧插件移除后一同移除)
     */
    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor() {
        MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
        interceptor.addInnerInterceptor(new PaginationInnerInterceptor(DbType.MYSQL));
        return interceptor;
    }
}
```



### 下划线转驼峰

```java
    /**
     * 驼峰命名转下划线分割
     *
     * @param str
     * @return
     */
    public static String toUnderScoreCase(String str) {
        if (str == null) {
            return null;
        }
        StringBuilder sb = new StringBuilder();
        // 前置字符是否大写
        boolean preCharIsUpperCase = true;
        // 当前字符是否大写
        boolean curreCharIsUpperCase = true;
        // 下一字符是否大写
        boolean nexteCharIsUpperCase = true;
        for (int i = 0; i < str.length(); i++) {
            char c = str.charAt(i);
            if (i > 0) {
                preCharIsUpperCase = Character.isUpperCase(str.charAt(i - 1));
            } else {
                preCharIsUpperCase = false;
            }

            curreCharIsUpperCase = Character.isUpperCase(c);

            if (i < (str.length() - 1)) {
                nexteCharIsUpperCase = Character.isUpperCase(str.charAt(i + 1));
            }

            if (preCharIsUpperCase && curreCharIsUpperCase && !nexteCharIsUpperCase) {
                sb.append(SEPARATOR);
            } else if ((i != 0 && !preCharIsUpperCase) && curreCharIsUpperCase) {
                sb.append(SEPARATOR);
            }
            sb.append(Character.toLowerCase(c));
        }

        return sb.toString();
    }
```



# JavaScript

## vue

### axios的封装

```javascript
import axios from "axios";

let commonURL = 'url';
// 设置后台服务地址
axios.defaults.baseURL = commonURL;
axios.defaults.timeout = 30 * 1000;
// 请求处理
axios.interceptors.request.use(config => {
    let token = sessionStorage.getItem("token");
    if (token) {
        config.headers['Authorization'] = token;  // 设置请求头
    }

    return config
}, error => {
    return Promise.reject(error)
});
// 响应处理
axios.interceptors.response.use(response => {
    let res = response.data;
    // 如果是返回的文件
    if (response.config.responseType === 'blob') {
        return res
    }
    // 兼容服务端返回的字符串数据
    if (typeof res === 'string') {
        res = res ? JSON.parse(res) : res
    }
    // 当权限验证不通过的时候给出提示
    if (res.code === '401') {
        // ElementUI.Message({
        //     message: res.msg,
        //     type: 'error'
        // });
        this.$router.push("/login")
    }
    return res;
}, error => {
    // console.log('err' + error) // for debug
    return Promise.reject(error)
})
export default axios
```



## uniapp

### uni.request的封装

```javascript
export const BASE_URL = 'url'
export const myRequest = (option)=>{
	return new Promise((resolve,reject)=>{
		uni.request({
			url:BASE_URL+option.url,
			method:option.method || 'GET',
			data:option.data || {},
			header: {Authorization:uni.getStorageSync('token')},
			success: (res) => {
				
				if(res.statusCode !== 200){
					
					uni.showToast({
						title:'数据请求失败!'
					})
					
					return
				}
				resolve(res)
			},
			fail: (err) => {
				uni.showToast({
					title:'服务器异常!'
				})
				reject(err)
			}
		})
	})
}
```







## 待续...