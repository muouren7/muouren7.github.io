---

title: SpringBoot笔记（未完成）
tags: [SpringBoot]
---

# SpringBoot笔记

[官方文档]([Spring Boot](https://spring.io/projects/spring-boot))

## 快速入门

### 案例

①：继承父工程

在pom.xml文件中添加以下配置，继承**spring-boot-starter-parent**这个父工程

```xml
<parent>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-parent</artifactId>
    <version>2.5.0</version>
</parent>
```

②：添加依赖

```xml
<dependencies>
	<dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
```

③：创建启动类

创建main方法后在主类上添加**@SpringBootApplication**注释

```java
@SpringBootApplication
public class Application {
    public static void main(String[] args){
        SpringApplication.run(Application.class,args);
    }
}
```

### 打包运行

①：添加Maven插件

```xml
    <build>
        <plugins>
            <!--springboot打包插件-->
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
```

②：点击IDEA右侧的**package**

![点击](https://cdn.jsdelivr.net/gh/muouren7/PIC/img202211181528830.png)

③：最后在cmd中输入**java -jar 打包好的文件.jar**运行

## 起步依赖

SpringBoot依靠父工程的版本锁定和starter机制简化对依赖的管理

### 依赖冲突

比如，A依赖C（低版本），B也依赖C（高版本）。都是同一个依赖而版本不同时会出现。

### 解决方法

排除低版本的依赖，留下高版本的依赖

## 版本锁定

在父工程中指定的版本号，尽可能避免依赖冲突

## starter机制

一个starter针对一种特定的场景，其内部引入了该场景所需的依赖。

命名规律

* 官方starter **sping-boot-starter-应用场景**，例如spring-boot-starter-data-jpg

* 非官方starter **应用场景-spring-boot-starter** 例如mybatis-spring-boot-starter

  

## 读取YML配置

### @Value注解

此注解只能获取简单类型的值（8种基本数据类型及其包装类 ，String，Date）

另外，加了@Value的类必须交给Spring容器管理

```yaml
student:
	lastName: muouren7
```

```java
@Controller
public class Test {
    @Value("${student.lastName}")
    private String name;

    @RequestMapping("/")
    @ResponseBody
    public String test(){
        return name;
    }
}
```

### @ConfigurationProperties

作用在对应的实体类上，且实体类要加上**@Component**注解

使用时用**@Autowired**自动注入

yml配置

```yaml
student:
	lastName: muouren7
	age: 17
```

实体类	

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
@Component
@ConfigurationProperties(prefix = "student")
public class Student {
    private String lastName;
    private int age;
}
```

控制类
```java
@Controller
public class Test {
    @Autowired
    private Student student;

    @RequestMapping("/")
    @ResponseBody
    public String test(){
        return student.getLastName() + student.getAge();
    }
}
```

## 热部署

在idea设置中打开自动构建

![自动构建](https://cdn.jsdelivr.net/gh/muouren7/PIC/img202211191457799.png)

设置运行程序运行期间自动启动

ctrl + shift + alt + / 这组快捷键后有一个小弹窗，点击Registry就会进入以下界面，找到以下配置勾选

![设置](https://cdn.jsdelivr.net/gh/muouren7/PIC/img202211191504454.png)

新版的在高级设置里

![高级设置](https://cdn.jsdelivr.net/gh/muouren7/PIC/img202211191506497.png)



### 使用

①：添加依赖

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <optional>true</optional>
        </dependency>
```

②：触发热部署

当修改完代码或者静态资源后，可以切换到其他软件让IDEA自动编译，自动编译后就会触发热部署

使用ctrl + F9手动出发重新编译

# SpringBoot整合JUnit

## 使用

①：添加依赖

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
        </dependency>
```

②：编写测试类

```java
@SpringBootTest(classes = Application.class)
public class StudentTest {
    @Resource
    private Student student;
    
    @Test
    public void test(){
        System.out.println(student);
    }
}
```

# 整合Mybatis

## 步骤

①：依赖

```xml
        <!--mybatis启动器-->
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>2.2.0</version>
        </dependency>
        <!--mysql驱动器-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
        </dependency>
```

②：配置数据库信息

```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/blogtest
    usename: root
    password: 123456
    driver-class-name: com.mysql.cj.jdbc.Driver
```

③：配置mybatis相关配置

```yaml
mybatis:
	mapper-location: classpath:mapper/*Mapper.xml	### mapper映射文件
	type-aliases-package: com.xxxx.xxx			### 配置哪个包下的类有默认的的别名
```

④：编写Mapper接口

注意在接口处加上@Mapper和@Repository

```java
@Repository
@Mapper
public interface UserMapper{
    public List<User> findAll();
}
```

# Web开发

## 静态资源的存放与访问

​	静态资源可以存放到 **resources/static** （或者 **resources/public** 或者 **resources/resources** 或者 **resources/META-INF/resources**）中即可。

​	例如要访问文件：resources/static/index.html 只需要在访问时资源路径写成/index.html即可

​	示例：访问文件：resources/static/pages/login.html ，访问资源路径写成/pages/login.html

### 修改静态资源访问路径

SpringBoot的默认静态资源路径匹配为/**  （表示当前路径）。可以通过修改 spring.mvc.static-path-pattern 这个配置进行修改。

例如：想在访问的路径前加上/res，比如/res/index.html才能访问到static目录中，我们可以修改如下

application.yml

```yaml
spring:
  mvc:
    static-path-pattern: /res/**
```

### 修改静态资源的存放目录

示例：此时静态资源目录不再是/static目录，而是resources目录下的Img目录

```yaml
spring:
  web:
    resources:
      static-locations: classpath:/Img/
```

配置多个静态资源目录

```yaml
spring:
  web:
    resources:
      static-locations:
      - classpath:/Img/
      - classpath:/static/
```

## 获取路径参数

请求url：/test/1

```java
@Controller
public class Test {
    @RequestMapping("/test/{id}")
    @ResponseBody
    public String test(@PathVariable("id") Integer id){
        return id.toString();
    }
}
```

## 获取请求体json参数

导入依赖，SpringBoot启动器中默认已导入，在控制器参数上加上@RequestBody注解

```xml
        <!--json转换-->
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.9.0</version>
        </dependency>
```

```java
@Controller
public class Test {

    @RequestMapping("/test/{id}")
    @ResponseBody
    public String test(@RequestBody Student student){
        return student.toString();
    }
}
```

## 获取QueryString格式参数

参数格式：url? key1=value1 && key2=value2

示例：url：**http://127.0.0.1:8080/test?lastName=muouren7&age=18**

①：使用@RequestParam注解

```java
@Controller
public class Test {

    @RequestMapping("/test")
    @ResponseBody
    public String test(@RequestParam String lastName, @RequestParam Integer age){ //@RequestParam注解可加可不加
        return lastName + age;
    }
}
```

②：使用@Validated参数

```java
@Controller
@CrossOrigin
public class Test {

    @RequestMapping("/test")
    @ResponseBody
    public RestRult test(@Validated Student student){
        RestRult<Student> restRult = new RestRult<>(200,"aaa",student);
        return restRult;
    }
}
```



## 响应体响应数据

### 数据放到响应体

导入依赖，SpringBoot启动器中默认已导入

```xml
        <!--json转换-->
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.9.0</version>
        </dependency>
```

使用@ResponseBody注解，将数据放入响应体并转换为json

```java
@Controller
public class Test {

    @RequestMapping("/test")
    @ResponseBody
    public Student test(@RequestParam String lastName, @RequestParam Integer age){
        return new Student(lastName,age);
        //return student.getLastName() + student.getAge();
    }
}
```

### 统一响应内容格式

返回类

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
@JsonInclude(JsonInclude.Include.NON_NULL)	//当某个属性为null时，则在序列化时过滤掉
public class RestRult<T> {
    private Integer code;
    private String msg;
    private T data;
}
```



## 跨域

### ①：使用@CrossOrigin

该注解可以加在控制器方法或类上

```java
@Controller
@CrossOrigin		//在类上表示当前类所有方法均可跨域
public class Test {

    @RequestMapping("/test")
    @ResponseBody
    public RestRult test(@RequestParam String lastName, @RequestParam Integer age){
        System.out.println(lastName+age);
        RestRult<Student> restRult = new RestRult<>(200,"aaa",new Student(lastName,age));
        return restRult;
    }
}
```





### ②：使用WebMvcConfigurer的addCorsMappings方法配置CorsInterceptor

当跨域允许携带cookie时，域名就不能为 *

```java
@Configuration
public class config {
    @Bean
    public WebMvcConfigurer configurer(){
        return new WebMvcConfigurer() {
            @Override
            public void addCorsMappings(CorsRegistry corsRegistry){
                corsRegistry.addMapping("/api/**");		//可以通过的域名地址
            }
        };
    }
}
```

```java
@Configuration
public class config implements WebMvcConfigurer {

    @Override
    public void addCorsMappings(CorsRegistry registry){
            //设置允许跨域的路径（哪些接口）
        registry.addMapping("/**")
                //设置允许跨域请求的域名
                .allowedOrigins("*")
                //是否允许带cookie
                .allowCredentials(true)
                //允许的请求方式
                .allowedMethods("GET","POST","DELETE","PUT")
                //允许的请求头
                .allowedHeaders("*")
                //跨域允许时间
                .maxAge(3600);
    }
}
```

## Token生成

[jjwt开源地址]([jwtk/jjwt： Java JWT： JSON Web Token for Java and Android (github.com)](https://github.com/jwtk/jjwt#quickstart))

导入依赖

```xml
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-api</artifactId>
    <version>0.11.5</version>
</dependency>
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-impl</artifactId>
    <version>0.11.5</version>
    <scope>runtime</scope>
</dependency>
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-jackson</artifactId> <!-- or jjwt-gson if Gson is preferred -->
    <version>0.11.5</version>
    <scope>runtime</scope>
</dependency>
```

工具类

```java
@Component
public class Token {
    Key key = Keys.secretKeyFor(SignatureAlgorithm.HS256);

    public String createToken(String subject){

        String jws = Jwts.builder().setSubject(subject).signWith(key).setExpiration(new Date(new Date().getTime() + 5000)).compact();
        return jws;
    }

    public void varity(String jws){
        try {
            String subject = Jwts.parserBuilder().setSigningKey(key).build().parseClaimsJws(jws).getBody().getSubject();
            System.out.println("验证通过");
            System.out.println(subject);
        }catch (JwtException e){
            System.out.println("验证失败");
        }
    }
}
```

测试类

```java
@SpringBootTest
public class TokenTest {

    @Autowired
    Token tk;

    @Test
    public void test() throws InterruptedException {
        String token = tk.createToken("AAA");
        System.out.println(token);
        System.out.println("---------------");
        Thread.sleep(5000);
        tk.varity(token);
    }

}
```

## 拦截器

### 拦截器与过滤器的区别

​	①、拦截器(Interceptor)只对action请求起作用 即对外访问路径而过滤器(Filter)则可以对几乎所有的请求都能起作用 包括css js等资源文件
​	②、拦截器(Interceptor)是在Servlet和Controller控制器之间执行而过滤器(Filter)是在请求进入Tomcat容器之后 但是在请求进入Servlet之前执行

### 创建并配置拦截器

①：创建类并实现接口

```java
import org.springframework.web.servlet.HandlerInterceptor;

public class MyInterceptor implements HandlerInterceptor {
}
```

②：实现方法

```java
@Component
public class MyInterceptor implements HandlerInterceptor {

    @Autowired
    private Token token;

    //在handler方法执行之前会被调用，验证header里面的token
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        String tk = request.getHeader("Token");
        if(token.varity(tk)){
            return true;
        }else {
            return false;
        }
    }

    //执行后调用
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception{
        System.out.println("postHandle");
    }

    //最终再调用
    @Override
    public void afterCompletion(HttpServletRequest request,HttpServletResponse response,Object handler,Exception ex) throws Exception{
        System.out.println("afterCompletion");
    }
}
```

③：配置拦截器

```java
@Configuration
public class config implements WebMvcConfigurer {
    @Autowired
    private MyInterceptor myInterceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(myInterceptor)  //添加拦截器对象
                .addPathPatterns("/**") //对任意路径拦截
                .excludePathPatterns("/login"); //配置排除路径
    }
}
```

## 自定义参数解析（暂无）

​		如果我们想实现像获取请求体中的数据那样，在Handler方法的参数上增加一个@ResponseBody注解就可以获取到对应的数据的话。
​		可以使用HandlerMethodArgumentResolver来实现自定义的参数解析。

## SpringBoot声明式事务

直接在需要事务控制的方法上加上对应的注解**@Transactional**

```java
@service
public class UserserviceImp1 imp1ements Userservcie {
    @Autowired
    private userMapper userMapper;
    @Override
    pub1ic List<user> findAll(){
    	return userMapper.findA77();
    }
    @Override
    @Transactional
    public void insertUser(
        //添加2个用户到数据库
        user user = new user(nu11 , "sg666",15 ,"上海");
        user user2 = new user(nu11 , "sg777" ,16,"北京");
        userMapper.insertuser(user)
        System. out.print1n(1/0);
        userMapper.insertUser(user2);
    }
}

```

### 切换动态代理

在配置文件中添加以下配置，**true**代表Cglib，**false**代表jdk代理

```yaml
  spring:
      aop:
        proxy-target-class: false #切换动态代理方式
```

## AOP

### 使用

添加依赖

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-aop</artifactId>
        </dependency>
```

## 日志

开启日志

```yaml
debug: true #开启日志
logging:
	level:
		com.xxxx: debug #设置包下的日志级别
```







































