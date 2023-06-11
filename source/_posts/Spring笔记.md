---
title: Spring笔记(未完成)
tags: [Spring,Ioc,Aop]
---

# Spring

## IOC控制反转

​	概念：实例对象原本的控制权在类，反转后将实例对象的控制权给IOC容器，着重体现在对象的创建上

### IOC案例

①导入依赖 SpringIOC相关依赖

```xml
<dependency>
	<groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId><version>5.1.9.RELEASE</version>
</dependency>
```

②在**resources**文件夹下创建Spring xml配置文件，命名最好为**applicationContext.xml**，xml文件用于bean的配置

![项目结构](https://cdn.jsdelivr.net/gh/muouren7/PIC/img202211131611506.png)

​	假如项目中包含以上类文件，enity中的Student为实体类，dao下的StudentDaoImp为StudentDao接口的实现。以将StudentDaoImp注册为bean为例，可以在xml配置文件中这样写

③xml中bean配置的写法

​	其中、class为全类名，id可以任意起，但bean与bean之间id不能重复，id是在Context（上下文、容器）创建对应类的实例对象时需要。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean class="dao.Imp.StudentDaoImmp" id="StudentDao"></bean>
</beans>
```

④对象的创建

​	在mian方法中或测试方法中写入一下代码

​	容器对象创建的实例对象为**object**类，需要将其强制转换为目标类（使用接口转换）

```java
    public static void main(String[] args){
                ClassPathXmlApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
                StudentDao studentDao = (StudentDao) applicationContext.getBean("StudentDao");
    }
```

### Bean常用属性

+ **id**：bean的唯一标识
+ **class**：全类名，用于反射创建对象
+ **scope**：scope属性有**singleton**和**prototype**两个值，前者IOC容器只会实例化一个bean对象，默认在容器创建时创建对象，后者创建多个对象，每次调用getBean都会创建一个新对象。

## DI依赖注入

​	概念：IOC控制反转应用的一种场景，反转的是对象依赖关系的维护权。（可以理解为IOC实例化目标对象，DI注入实例化目标对象里的属性对象）

### Set方法注入

​	必须有对应属性的set方法

​	使用**property**标签，name对应属性，value为要注入的值，ref为引用其他的bean

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean class="dao.Imp.StudentDaoImmp" id="StudentDao">
        <property name="name" value="张三"></property>
    </bean>
</beans>
```

### 有参构造注入

**	constructor-arg**比奥前标签用于指定构造函数的参数

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean class="enity.Student" id="student">
        <constructor-arg name="id" value="1"></constructor-arg>
        <constructor-arg name="name" value="里斯"></constructor-arg>
    </bean>
</beans>
```

### 复杂类型属性注入

​	**property**子标签，（包含list，set）等。

## Lombok

### 导入依赖

```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.18</version>
</dependency>
```

## 配置文件

### 读取properties文件

​	让Spring读取properties文件内容，再利用其中的键值对。

①设置读取properties

​	在Spring bean配置文件里写

​	其中，location为properties位置，由于打包后资源文件会复制到target中，所有选择classpath:

​	另外，classpath:*properties *代表任意properties文件

```xml
<context:property-placeholder location="classpath:application.properties"></context:property-placeholder>
```

②使用配置文件中的值

​	使用${key}的方式来表示具体的值，另外需要在value属性中使用才可以。

​	properties文件内容

```properties
name=muouren7
```

```xml
<constructor-arg name="name" value="${name}"></constructor-arg>
<!--或者-->
<property name="name" value="${name}"></property>
```

### 引入Spring xml配置文件

​	在主配置文件中通过import标签的resource属性，引入其他xml配置文件

```xml
<import resource="classpath:applicationContext-book.xml" />
```

## 低频知识点

### Bean相关配置

* **name**属性：类似于id，用于bean的命名与标识

* **lazy-init**：如果设置为true，则在第一次获取该对象时才会创建

  ```xml
  <bean class="com.xxx" name="test" lazy-init="true"></bean>>
  ```

* **init-method**：指定初始化方法，容器在创建完对象后会自动调用，*配置的初始化方法只能是空参的*

* **destroy-method**：指定销毁前方法，容器在销毁对象之前会调用

* **factory-bean & factory-method**：

  ```xml
  <!--创建实例工厂-->
  <bean class="com.sangeng.factory. CarFactory"id="carFactory"></bean>
  <!--使用实例工)厂创建Car放入容器-->
  <!--factory-bean用来指定使用哪个工厂对象-->
  <bean factory-bean="carFactory" factory-method="getcar"</bean>
  ```

  ```xml
  <!--使用静态工厂创建Car放入容器-->
  <bean class=" com.sangeng.factory.CarStaticFactory" factory-method="getCar"></bean>
  ```
# Spring注解开发

  ## 注解开发

### 开启组件扫描

```xml
<context:component-scan base-package="com.xxx"/>
```

## IOC相关注解

### @Component,@Controller,@Service,@Repository

## DI相关注解

### @Value

​	用于String Integer等类型的直接注入，不依赖setter方法，支持SpEL表达式（即**${表达式}**）

### @AutoWired

​	Spring会自动给加了该注解的属性注入相同类型的对象

### @Qualifier

​	如果同类型的bean在容器中同时存在多个，则用@AutoWired则会出现错误，此时应该使用@Qualifier注解来指定bean的名字进行注	入，**此注解不能单独使用**

## XML配置文件相关注解

### @Configuration

​	标注在类上，表示当前类是一个配置类，可以用注解类完全替换XML配置文件

​	另外，如果使用了配置类替换XML配置，Spring容器要使用AnnotationConfigApplicationContext

```java
public class Main {
    public static void main(String[] args){
        //ClassPathXmlApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
        AnnotationConfigApplicationContext annotationConfigApplicationContext = new AnnotationConfigApplicationContext(config.class);
        Test test = (Test) annotationConfigApplicationContext.getBean("TestImp");
        test.print();
    }
```



### @ComponentScan

​	用于代替context:component-scan标签来配置组件扫描

```java
package com.Config;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@ComponentScan(basePackages = "com")
public class config {
}
```



### @Bean

​	用于代替bean标签，用于第三方类的注入

 ### @PropertySource

​	用于代替context:property-placeholder，让Spring读取指定的properties文件，

然后使用@Value注解来读取值

# Spring-AOP

## AOP

### 概念

​	Aspect Oriented Programming的缩写，意为：面向切面编程。

​	**SpringAOP：批量对Srping容器中的bean的方法做增强，并且这种增强不会对原来方法中的代码耦合**

### 案例

①：添加依赖

​	必须同时包含SpringIOC的依赖和AOP的依赖

```xml
<!--SpringIOC相关依赖-->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>5.1.9.RELEASE</version>
</dependency>
<!--SpringAOP相关依赖-->
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.8.13</version>
</dependency>
```

**②：开启AOP注解支持**

​	在xml配置文件中加入

```xml
<!--开启AOP注解支持-->
<aop:aspectj-autoproxy></aop:aspectj-autoproxy>
```

③：准备切面类

在类上加入**@Component***和**@Aspect**

使用**@Pointcut**注解来指定要被增强的方法

使用**@Before**注解来给我们的增强代码所在的方法进行标识，并且指明增强代码实在目标代码方法执行前执行

目标代码

```java
@Service("TestImp")
public class TestImp implements Test{
    @Override
    public void print(){
        System.out.println("打印A");
    }
}
```

```java
@Component
@Aspect
public class MyAspect {
    //用Pointcut注解指定目标方法
    //第一个*号代表任意类型返回值，最后一个*号代表任意方法，(..)代表任意参数
    @Pointcut("execution(* com.Test.TestImp.*(..))")
    public void pt(){

    }
	//pt()指在此处切入
    @Before("pt()")
    public void methoodBefore(){
        System.out.println("切入在目标方法执行之前");
    }
}
```

## AOP核心概念

* Joinpoint（连接点）：指那些可以被增强到的点，在Spring中，这些点指的是方法，因为Spring只支持方法的增强

* Pointcut（切入点）：指被增强的连接点（方法）

* Advice（通知：增强）：指具体增强的代码

* Target（目标对象）：被增强的对象就是目标对象

* Aspect（切面）：是切入点与通知的结合

* Proxy（代理）：一个类被AOP增强后，就产生一个结果代理类

  

## 切点确定

### 切点表达式

​	写法：**execution([修饰符] 返回类型 包名.类名.方法名.(参数))**

* 访问修饰符可以省略
* 返回值类型、包名、类名、方法名可以用***号**代表任意
* 包名与类名之间一个点**.**代表当前包下的类，两个点**..**标识当前包及其子包下的类
* 参数可以用两个**..**表示任意个数、任意类型的参数列表

### 切点函数@annotation

写法：**@annotation(注解的全类名)**

实例：

先自定义注解，将自定义注解标注的需要增强的方法上

```java
package com.aspect;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target(ElementType.METHOD)		//表明该注解可以在哪些地方标注
@Retention(RetentionPolicy.RUNTIME)		//该注解可以保存到运行时
public @interface InvokeLog {
}
```

被增强方法

```java
package com.Test;


import com.aspect.InvokeLog;
import org.springframework.stereotype.Service;

@Service("TestImp")
public class TestImp implements Test{
    //加上该自定义注解
    @InvokeLog	
    @Override
    public void print(){
        System.out.println("打印A");
    }
}

```

在切面类里使用**@annotation**

```java
@Component
@Aspect
public class MyAspect {
    //用Pointcut注解指定目标方法
    @Pointcut("@annotation(com.aspect.InvokeLog)")		//此处写自定义注解的全类名
    public void pt(){
        System.out.println("AAA");
    }

    @After("pt()")
    public void methoodBefore(){
        System.out.println("切入在目标方法执行之前");
    }
}
```

## 通知分类

* @Before：前置通知，在方法执行前执行
* @AfterReturning：返回后通知，在目标方法执行后执行，如果出现异常不会执行
* @After：后置通知，在目标方法返回结果之后通知，无论是否异常都会执行
* @AfterThrowing：异常通知，在目标方法抛出异常后通知
* **@Around：环绕通知，围绕着方法执行**

环绕通知@Around实例：

被增强的方法：

```java
@Service("TestImp")
public class TestImp implements Test{
    @Override
    public void print(){
        System.out.println("打印A");
    }
}
```

切面类：

```java
@Component
@Aspect
public class MyAspect {
    //用Pointcut注解指定目标方法
    @Pointcut("execution(* com.Test.TestImp.*(..))")
    public void pt(){
        System.out.println("AAA");
    }

    @Around("pt()")
    public void methoodBefore(ProceedingJoinPoint pjp){	//该参数指向目标方法
        System.out.println("切入在目标方法执行之前");
        try{
            pjp.proceed();		//目标方法执行
        }catch(Throwable throdable){
            throwable.printStackTrace();
        }
    }
}
```

## 获取被增强方法的相关信息

**JoinPoint**

```java
@Component
@Aspect
public class MyAspect {
    //用Pointcut注解指定目标方法
    @Pointcut("@annotation(com.aspect.InvokeLog)")
    public void pt(){
        System.out.println("AAA");
    }

    @After("pt()")
    public void methoodBefore(JoinPoint jp){	//参数包含增强目标的信息
       Object ta = jp.getTarget();
        System.out.println(ta);
    }
}
```

## 多切面顺序问题

使用**@Order(1)**

数字越小，优先级越高

# AOP原理-动态代理



## JDK动态代理

​	JDK动态代理使用的**java.lang.reflect.Proxy**这个类来实现的。要求被代理（被增强）的类实现了接口。并且JDK代理只能对接口中的方法进行增强。

示例：

```java
public class Main {
    public static void main(String[] args){
        //使用动态代理增强TestImp中的print方法
        TestImp testImp = new TestImp();
        ClassLoader classLoader = Main.class.getClassLoader();
        Class<?>[] interfaces = TestImp.class.getInterfaces();
        //prox是代理对象
        Test prox = (Test) Proxy.newProxyInstance(classLoader, interfaces, new InvocationHandler() {
            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                if(method.getName().equals("print")){
                    System.out.println("已经增强");
                }
                method.invoke(testImp,args);
                return null;
            }
        });
        prox.print();
    }
}
```

## Cglib动态代理

​	使用的是org.springframework.cglib.proxy.Enhancer类进行实现的

示例：先要导入SpringIOC相关依赖

```java
public class Main {
    public static void main(String[] args){
        //使用动态代理增强TestImp中的print方法
        Enhancer enhancer = new Enhancer();
        //设置父类的字节码对象
        enhancer.setSuperclass(TestImp.class);
        enhancer.setCallback(new MethodInterceptor() {
            @Override
            public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {

                if(method.getName().equals("print")){
                    System.out.println("被增强");
                }

                Object ret = methodProxy.invokeSuper(o,objects);
                return null;
            }
        });
        //生成代理对象
        TestImp proxy = (TestImp) enhancer.create();
        proxy.print();
    }
}
```

## 区别

JDK动态代理要求被代理的类必须要实现接口，生成的代理对象相当于被代理对象的兄弟

Cglib的动态代理不要求被代理的类实现接口，生成的代理对象相当于被代对象的子类对象

Spring默认情况下使用的是JDK动态代理，当使用不了JDK动态代理才会使用Cglib的动态代理

### 切换动态代理方式

**proxy-target-class**属性为true，则切换至Cglib代理

注解配置

```xml
<aop:aspectj-autoproxy proxy-target-class="true"></aop:aspectj-autoproxy>
```

XML配置

```xml
<aop:config proxy-target-class="true"></aop:config>
```

# Spring整合Junit

## ①导入依赖

```xml
<!--        junit-->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
<!--        Spring整合Junit-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>5.1.9.RELEASE</version>
        </dependency>
```

## ②编写测试类

在测试类上加

**@RunWith(SpringJUnit4ClassRunner.class)**，指定测试运行与Spring环境

**@ContextConfiguration**：指定Spring容器创建需要的配置文件或配置类

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = "classpath:applicationContext.xml")
//@ContextConfiguration(classes = config.class)
public class SpingTest {

    @Autowired
    private TestImp testImp;

    @Test
    public void test(){
        testImp.print();
    }
}
```

# Spring整合Mybatis

[官方文档]([mybatis-spring –](http://mybatis.org/spring/zh/getting-started.html))

## 导入依赖

```xml
<!--        spring-jdbc-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>5.1.9.RELEASE</version>
        </dependency>
        <!--        mybatis整合到Spring的整合包-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>2.0.4</version>
        </dependency>
        <!--        mybatis依赖-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.4</version>
        </dependency>
        <!--        mysql驱动-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.47</version>
        </dependency>
<!--        druid数据源-->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>1.1.16</version>
        </dependency>
```

## Spring文件配置

```xml
<!--读取properties文件-->
<context:property-placeholder location="classpath: jdbc.properties"></context:property-placeholder>

<!--创建连接池注入容器-->
<bean class="com.alibaba. druid. pool.DruidDataSource" id="dataSource">
	<property name="ur1" value="$ {jdbc.ur1]}"></property>
	<property name="username" value="$ {jdbc.username}"</property>
    <property name="password"value="$ {jdbc.password}"</property>
	<property name="driverClassName" value="${jdbc.driver}"></property></bean>
<bean class="org.mybatis.spring.SqlSessionFactoryBean"id="sessionFactoryBean">
	<property name="dataSource" ref="dataSource"</property>
	<!--配置mybatis配置文件的路径-->
	<property name="configLocation"value="classpath:mybatis-config.xml"</property></bean>

<!--mapper扫描配置，扫描到的mapper对象会被注入Spring容器中-->
<bean class=" org.mybatis.spring.mapper. MapperScannerConfigurer" id="mapperScannerConfigurer">
	<property name="basePackage" value="com.sangeng.dao"></property>
</ bean>

```

# Spring声明式事务

## 事务回顾

概念：保证一组数据库的操作，要么同时成功，要么同时失败

例：A给B转账

​		A的前减少

​		B的钱增加

### 四大特性

* 隔离性

  事务之间相互隔离，成功失败互不影响

* 原子性

  不可分割的整体

* 一致性

  事务前后数据状态一致，要么都成功，要么都失败

* 持久性

  事务被提交，事务对数据的操作就真的发生了变化，即使数据库故障也不会影响	





















