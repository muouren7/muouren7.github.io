---
title: Java题记
tags: [面试]
---

## Java

### 1、HashMap的底层原理，hashmap与currentHashMap的区别?

* 1、HashMap实现了Map接口，继承了AbstractMap类，数据结构使用的位桶数组，底层使链表或红黑树进行存储；
* 2、默认初始化长度为**16**，扩容加载因子为**0.75**，一旦大于**0.75*16**之后，就会**resize()**进行扩容，扩容**2**倍，即32；
* 3、JDK1.7中数据结构采用位桶数组+链表结构；
* 4、JDK1.8中数据结构采用位桶数组+链表/红黑树；
* 5、支持克隆，无序，线程不同步，非安全。
* 6、HashMap线程不安全，currentHashMap线程安全，currentHashMap使用了分段锁，在并发情况下currentHashMap性能更好，HashMap允许存储空值与空键，currentHashMap不允许存储空值和空键。

### 2、Synchronized和ReentrantLock的区别

* ReentrantLock是可重入锁，就是说同一个线程可以多次获取同一个锁，Synchonized也是可重入的，在持有锁的情况下可以再次请求获取锁，不会产生死锁。
* Synchronized是隐式锁，通过关键字实现，在代码块或方法上修饰，ReentrantLock是显示锁，在代码中通过Lock对象方式进行手动加锁和解锁
* ReentrantLock更灵活，支持公平锁与非公平锁，可以在锁上设置超时时间。
* 低竞争的情况下，Synchronized性能更好，在jvm层面有优化，高竞争的情况下，ReentrantLock可能性能更好，因为更灵活和更细粒程度的控制。

### 3、公平锁与非公平锁的理解

* 公平与非公平是按照获取锁的顺序进行分类的
  * 公平锁：多个线程按照申请锁的顺序进行获取，释放锁之后，等待时间最长的线程将获得锁，避免了饥饿现象，但是会维护一个有序队列来管理等待的线程，有额外开销
  * 非公平锁：没有顺序限制，存在剥夺情况，旧线程还持有锁的时候新线程可能直接给抢了，可能会导致饥饿现象



### 4、SpringBoot的Aop?

* Joinpoint（连接点）：指那些可以被增强到的点，在Spring中，这些点指的是方法，因为Spring只支持方法的增强
* Pointcut（切入点）：指被增强的连接点（方法）
* Aspect（切面）：是切入点与通知的结合
* Advice（增强，通知）：指具体增强的代码
* Target（目标对象）：指具体增强的代码
* Proxy（代理对象）：一个类被AOP增强后，就产生一个结果代理类
* Weaving（织入）：将aspect和其他对象连接起来，并传教adviced object的过程

### 5、redis的数据结构

* **String**：字符串，整数或浮点数
* **List**：列表
* **Set**：无序集合
* **Hash**：无序散列表
* **ZSet**：有序集合

### 4、SpringBoot的核心注解是哪个？它是由哪些注解组成的？

* 最核心的是**@SpringBootApplication**，包含以下三个注解
* **@SpringBootConfiguration**：组合了**@Configuration**注解，实现配置文件的功能。
* **@EnableAutoConfiguration**：打开自当配置功能，可关闭
* **@ComponentScan**：Spring组件扫描

### 6、SpringBoot的装配流程

* 1、启动时加载主配置类，开启自动配置功能@EnableAutoConfiguration
* 2、查询 @EnableAutoConfiguration，其作用是利用AutoConfigurationImportSelectro给容器导入一些组件
* 3、查看 AutoConfigurationImportSelectro，其中 public String[] selectImports(AnnotationMetadata annotationMetadata)方法内，最终调用getCandidateConfigurations()方法
* 4、查看getCandidateConfigurations(AnnotationMetadata metadata , AnnotationAttributes attributes)，获取候选的配置，这个是扫描所有的 jar 包类路径下 **"META-INF/spring.factories"**
* 5、把扫描到的文件包装成 Properties 对象。
* 6、从 properties 中获取到 EnableAutoConfiguration.class类名对应的值，然后把他们添加在容器中。
* 整个过程就是将类路径下的**"META-INF/spring.factories"**里面配置的所有EnableAutoConfiguration的值加入到容器当中。

### 7、讲一下Jvm

jvm包含两个子系统和两个组件：

* 两个子系统为：

  * **类加载器 Class Loader**：将 class 文件装载到 **方法区**

  * **执行引擎 Execution engine**：执行classes中的指令 

* 两个组件：

  * **本地接口 Native Interface**：
  * **运行时数据区 Runtime Data Area**：
    * **虚拟机栈**：为每一个java方法分配一直**栈帧**，栈帧保存该方法的**出入口**，**局部变量表**，**参数**等。
    * **本地方法栈**：与虚拟机栈类似，不过保存的是JNI里的方法。
    * **程序计数器**：保存当前线程执行指令，用于线程切换后对线程任务进行恢复。
    * **Java堆**：用于保存对象和数组。
    * **方法区**：用于存储类信息，常量，静态变量等。

### 8、SpringMVC五大组件

* **DispatcherServlet** ：接受所有的请求并分发到相应的处理器上
* **HandlerMapping**：将请求映射到具体的处理器（Handler）上
* **HandlerAdapter**：根据不同类型的处理器，调用相应的方法进行请求
* **ViewResolver**：将处理器处理完的逻辑视图名解析为具体的视图
* **View**：渲染视图

### 9、SpringMVC执行流程

* 客户端发起请求，**DispatcherServlet** 拦截请求
* DispatherServlet 根据请求信息调用 **HandlerMapping**。HandlerMapping 根据 url 去匹配能处理的 Handler（就是Controller）,并将请求和涉及到的拦截器以及 Handler 一起封装。
* DispatherServlet 调用 **HandlerAdapter** 适配执行 Handler
* Handler 完成处理后，返回一个 ModelAndView 对象给 DispatcherServlet
* ViewResolver 根据逻辑 View 查找实际 View
* DispaterServlet 将返回的 Model 传递给 View
* 将 View 返回给请求者

### 10、JavaBean的生命周期

* 实例化Bean：通过构造或反射的方式生成对象
* 属性赋值：实例化后通过 **set()** 方法对属性进行赋值
* 初始化：执行初始化方法，如连接数据库，加载配置文件等
* 使用：JavaBean被其他组件或代码使用
* 销毁：通过使用 **DisposableBean** 接口或者使用 **@PreDestroy** 来定义销毁方法

### 11、applicationContext与BeanFactory的区别

* 共同点：都是spring中用于管理获取容器中的bean的
* 区别：
  * BeanFactory 是 spring 中最基本的容器，提供了 IOC 与 DI 注入功能
  * ApplicationContext 是 BeanFactory 的子接口
  * BeanFactory 在初始化容器时不会直接实例化bean对象，而是第一次获取时才实例化
  * ApplicationContext 初始化时会对所有单例的 bean 进行实例化

### 12、Spring中的声明式事务是如何实现的

通过 **aop** 和 **事务管理器** 实现的

* 配置事务管理器：负责协调事务的开始，提交或回滚等操作。
* 启用声明式事务：使用**@EnableTransactionManagement** 注解或 **<tx:annotation-driven>** 配置开启声明式事务
* 定义事务边界：在需要进行事务管理的方法上使用 **@Transactional** 
* 切面拦截：通过 **aop** 拦截匹配的方法，讲事务管理逻辑织入到目标方法前后

### 13、事务的传播行为

* REQUIRED（默认）：若存在，则加入，不存在，则创建
* SUPPORTS：若存在，则加入，不存在，以非实物事务方式运行
* MANDATORY：若存在，则加入，不存在，抛异常
* REQUIRES_NEW：不管是否存在，都会创建新的事务，会把原来的事务挂起
* NOT_SUPPORTED：以非事务的方式执行，若存在，则挂起
* NEVER：以非实物方式运行，若存在，则挂起
* NESTED：若存在，则嵌套，不存在，则新建

### 14、CAS

cas：campare and swap 比较和交换

用于保证多线程环境下的原子操作，包含三个参数，分别是 **内存地址（变量的引用）**，**旧的预期值**，**新的值**

先读取内存中的当前值，然后与旧的预期值进行比较，若相同，说明未被修改，则更新；若不同，说明被修改，则不更新，避免锁机制的开销

**aba问题**：比如线程A将共享变量的值改为了 1 ，然后线程B将这个值改为了 2 ，然后又改回了 1 ，整个过程中，线程A通过cas操作发现变量仍是 1，但实际上已经被修改过了，cas操作并不正确。一般通过引入版本号的方式避免 aba 问题。

### 15、java的类加载器有哪些

* **Bootstrap Class Loader**（启动类加载器）：最顶层类加载器，由c++实现
* **Extension Class Loader**（扩展类加载器）：负责加载java的扩展库
* **System Class Loader**（系统类加载器）：用户加载我们写的类的

### 16、双亲委派机制

指加载一个类的时候会首先委派给父类加载器加载，如果父类加载器加载不了，再由自身加载器加载，可保证类的加载是有序的，不会重复加载。

### 17、GC回收

常见回收算法

* 标记-清除：标记存活的对象，清除未标记的对象，会产生内存碎片
* 复制算法：将内存分为两部分，每次只用其中的一部分，当这部分用满后，将存活的对象移动到另一部分，然后清除当前空间的所有对象，简单高效，但是会浪费一部分内存
* 标记-整理：标记所有活动的对象，将他们向另外一端移动，然后清除移动后端的对象，可解决内存碎片问题，但需要额外的移动操作
* 分代算法：

### 如何保证线程安全

* 加锁
* 使用原子操作，例如cas
* 使用线程安全的数据结构，例如 CopyOnWriteArrayList
* 线程通信，使用wait，noity，noityAll

## Mysql

### 1、Mysql索引结构

* 类型

  * **B-Tree**索引：平衡树，适用于范围查询和精准匹配查询。MySql中的InnoDB默认使用B-Tree，B-Tree适合处理有序数据和范围查询

  * **Hash**索引：Hash索引使用哈希表来存键值对，适用于等值查找，不适合范围查询或排序操作

  * **Full-Text**：Full-Text索引用于全文搜索，可以高效的进行文本匹配检索，MyISAM和InnoDB都支持Full-Text

  * **R-Tree**：R-Tree适用于多维数据和范围查询，MyISAM和InnoDB都支持

* 功能

  * **主键索引（Primary Key Index）**：用于唯一标识表中的每一行数据，并确保主键值的唯一性。主键索引可以是单列主键索引或复合主键索引。

  * **唯一索引（Unique Index）**：确保索引列的值在表中是唯一的。与主键索引不同的是，唯一索引可以允许空值。

  * **普通索引（Index）**：也称为非唯一索引，用于加快查询操作的速度。普通索引没有唯一性限制，可以包含重复值和空值。

  * **全文索引（Full-Text Index）**：用于全文搜索，对于包含大量文本内容的列（如文本、文本段落或文档），可以加快全文搜索的效率。

  * **空间索引（Spatial Index）**：用于支持地理空间数据类型（如点、线、面）的快速空间查询。




### 2、什么是索引，其优缺点

* 索引概念与目录相似，是根据某些列的数据排序，生成独立的索引表，当某列创建了索引，如果该列出现在查询条件中，那么数据库系统会自动引用该索引。
* 优点：
  * 1、加快检索速度
  * 2、加速表和表的连接
  * 3、在实现数据的参照完整性方面有意义，如在外键列上创建索引可以避免死锁，也可以防止父表更新主键时，数据库对子表的全盘锁定
  * 减少磁盘IO
* 缺点：
  * 1、索引只能建在表上，不能创建在视图上
  * 2、索引的创建和维护要消耗时间
  * 对表进行增删改操作时，系统要有额外的时间对索引进行维护更新

### 3、索引什么时候会失效

* 1、like 以 % 开头，索引失效
* 2、or语句前后没有同时使用索引失效
* 3、不符合最佳左前缀法则
* 4、is not null 索引失效
* 5、where中 出现不等于 
* 6、select * 可能导致失效
* 7、对索引列进行运算，一定导致索引失效

### 4、聚簇索引与非聚簇索引的区别

* 执行效率：聚簇索引更快，因为聚簇索引存储的是数据，而非聚簇索引存储的是主键ID，需要回表查询
* 数量上：聚簇索引一个表只能由一个，而非聚簇索引可以有多个

### 5、Mysql中 select for update有什么含义，会锁表还是锁行

​	select for update会将查询的行加上排他锁，是行锁，且该语句只能在事务中使用。

### 6、IN 和 EXISTS 的区别

* IN 检查给定值是否在列表或子查询中，返回true或false
* EXISTS 主要检查子查询中是否有结果，返回true或false

### 7、union与union all	 

union用于合并两或多个个查询集，并且会进行去重，如果想保留重复的部分则使用 union all

## Vue

### 1、router与route的区别

* router 是 vue.js 的路由管理器，负责整个路由的配置
* route 是 VueRouter 的一个实例对象，代表当前的路由
* router 通过配置和管理，决定哪一个 route是当前激活的

### 2、computed和watch的区别

* **computed**：计算属性，根据依赖的数据的变化而自动更新，其值可像普通股属性一样在模板中使用
* **watch**：监听器，监听单个数据或对象，当数据发生改变时可以执行自定义的回调函数

### 3、Vue常用指令

* **v-once**：只执行一次渲染，当数据发生改变，不再变化
* **v-show**：接受一个表达式或布尔值，相当于加了一个display
* **v-if、v-else、v-else-if**：与v-show效果相同，频繁切换使用v-show效率更高
* **v-for**：用于遍历数组，列表
* **v-text、v-html**：插入文本，插入html
* **v-bind**：为属性绑定值
* **v-on**：为标签绑定事件
* **v-model**：双向绑定





