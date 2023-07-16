---
title: Hello World
date: 2019-10-14 16:06:38
---
Welcome to [Hexo](https://hexo.io/)! This is your very first post. Check [documentation](https://hexo.io/docs/) for more info. If you get any problems when using Hexo, you can find the answer in [troubleshooting](https://hexo.io/docs/troubleshooting.html) or you can ask me on [GitHub](https://github.com/hexojs/hexo/issues).

## Quick Start

### Create a new post

``` bash
$ hexo new "My New Post"
```

More info: [Writing](https://hexo.io/docs/writing.html)

### Run server

``` bash
$ hexo server
```

More info: [Server](https://hexo.io/docs/server.html)

### Generate static files

``` bash
$ hexo generate
```

More info: [Generating](https://hexo.io/docs/generating.html)

### Deploy to remote sites

``` bash
$ hexo deploy
```

More info: [Deployment](https://hexo.io/docs/one-command-deployment.html)

title: 面试真题
tags: [面试]

## 九天信智

### 聚集索引和非聚集索引的区别

* 物理排序和聚集索引的顺序是相同的，一个表也就只能建立一个聚集索引
* 聚集索引的查询性能一般比非聚集索引的查询性能高，聚集索引直接存的数据，非聚集索引存的是主键ID
* 更新，删除操作对聚集索引的影响更大，因为可能要修改索引的物理存储顺序

### 开窗函数



### 说说JS



### 分组查询

group by having

### 闭包

### 说说GC

常见算法：

* 标记-清除：标记所有被引用的对象，在清理阶段清理未被标记的对象，会产生内存碎片
* 复制算法：将内存分为两个区域，使用时将对象全部放在内存的一部分，存满了就将存活的对象移动到另一侧，然后将当前部分的对象全部清除，会浪费一部分内存
* 标记-整理：标记所有存活的对象，让他们往一侧移动，清除掉移动后端的对象，可解决内存碎片的问题
* 分代算法：将对象划为不同的代

### 类加载器

* **Bootstrap Class Loader**（启动类加载器）：最顶层的加载器，由c++编写，不属于Java类
* **Extension Class Loader**（扩展类加载器）：负责加载java的扩展库
* **Application Class Loader**（系统类加载器）：用于加载我们自己写的类

### 存储过程是什么

在数据库中预先定义好的一段可执行的程序代码，可理解为编程语言中的函数

### 抽象类和接口的区别

* 接口：不能实例化，没有构造方法，方法默认是 public abstract修饰，且只能有方法的定义，不能有方法的实现，留给实现类去实现，变量默认是 public static final 修饰，一个类可以实现多个接口，但是只能继承一个抽象类

* 抽象类：有构造方法，但不能进行实例化，成员变量可修改，方法可以有方法体

  

### arraylist 和 linkedlist

均继承AbstractList抽象类，区别在于arraylist底层是由数组实现的，linkedlist由链表实现，其中arraylist初始化容量为10，调用add方法时扩容为原来的1.5倍，调用addAll方法时会在添加后的长度与扩容1.5后的长度中取最大值。arraylist适合随机查询，linkedlist适合修改插入删除等

### mysql聚合函数有哪些

count：计数

avg：计算平均值

max-min：最大最小值

### 进行聚合操作前需要进行什么操作

### JS绑定事件有哪些

click:点击	dblclick:双击	mouseover:鼠标移入	mouseout:鼠标移出	keydown:键盘按下	keyup:键盘弹起

### mysql闭包

### Java三大特征，多态的三个因素

封装：就是把具体操作实现细节给包装起来，对外只提供方法或接口

继承：

多态：继承是多态的基础，方法的重写，向上转型

### Object有哪些方法

clone()：浅克隆

getClass()：获取字节码对象

toString()：转字符串

wait()、noity()、noityAll()、hashcode()、equals()

### 找1到10亿有几个2 的做法

循环取余，转字符串循环查询是否包含2的字符

### 前端获取具体的变量类型

typeof

### js取元素的方法

document.getEmelentById

document.getElementByTag

document.getElementByClass

document.querySelector

### 线程池的创建，拒绝策略

* 最低核心线程数
* 最大核心线程数
* 存活时间
* 时间单位
* 任务队列
* 线程工程
* 拒绝策略
  * **AbortPolicy**：默认情况是抛出异常
  * **CallerRunsPolicy**：由提交任务的线程执行
  * **DiscardPolicy**：直接抛弃，不会由任何通知
  * **DiscardOldestPolicy**：抛弃旧的任务

### Java除socket还有什么能用来网络通讯

URL类

NIO

Netty

HttpClient



## 项目：

### 房产：

#### 登录：

Controller -> Session -> 重定向到前端首页 -> 走拦截器 -> 取session的信息 -> 存入ThreadLocal中

#### 邮箱验证

注册时将用户信息写入数据库，但状态是未激活 ->  生成密钥并存入guava的缓存 -> 如果超时15min则失效 -> （异步）调用邮箱发送（使用SpringMail操作）带上密钥 -> 收到邮件后点击链接进行验证 -> 将用户信息状态修改为激活

#### 密码加密：

MD5 + 盐

#### 图片上传：

设置 **MultipartFile** 参数格式，然后通过读写文件流的方式进行文件上传，保存在指定位置，通过nginx代理静态资源的方式访问图片

#### 热门房源：

将房源信息和房源的点击量存入redis，每次访问房源的详情信息的时候计为一次点击，通过Zet存储并排序，然后每次取前三







|      | springbean的作用域                                           | 类加载器有哪些                                               | Spring框架中用了哪些设计模式                                 | InnoDB 和 MyISAM 有什么区别                                  | count(1)、count(*)与count(列名)的区别                        |
| ---- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 张龙 | 75                                                           | 100                                                          | 60                                                           | 80                                                           | 60                                                           |
| 李应 | 25                                                           | 100                                                          | 30                                                           | 50                                                           | 0                                                            |
|      | Singleton - 每个IoC 容器仅有一个单实例。 Prototype - 每次请求都会产生一个新的实例。 Request - 每一次 HTTP 请求都会产生一个新的实例，并且该 bean 仅在当前 HTTP 请求内有效。 Session - 每一次 HTTP 请求都会产生一个新的 bean，同时该 bean 仅在当前 HTTP session 内有效。 Global-session - 类似于标准的 HTTP Session 作用域，仅在基于 portlet 的 web 应用中才有意义。 | **Bootstrap Class Loader**（启动类加载器）：最顶层的加载器，由c++编写，不属于Java类 **Extension Class Loader**（扩展类加载器）：负责加载java的扩展库 **System Class Loader**（系统类加载器）：用于加载我们自己写的类 | 工厂模式：Spring使用工厂模式，通过FactoryBean和ApplicationContext来创建对象；单例模式：Spring中Bean的创建默认为单例模式；代理模式：Spring的AOP面向切面编程功能，用到了CGLIB动态代理和JDK动态代理；模板方法模式：可以将相同部分的代码放在父类中，而将不同的代码放在不同的子类中，用来解决代码重复的问题。比如RestTemplate，JmsTemplate，JpaTemplate；适配器模式：Spring AOP的增强或者通知（Advice）用到了适配器模式，SpringMVC中也是用到了适配器模式适配Controller； | InnoDB 支持事物，而 MyISAM 不支持事物InnoDB 支持行级锁，表锁，而 MyISAM 支持表级锁InnoDB 支持 MVCC，而 MyISAM 不支持InnoDB 支持外键，而 MyISAM 不支持InnoDB5.7之前不支持全文索引，而 MyISAM 支持nnoDB必须有主键，没有指定会默认生成一个隐藏列作为主键，而MyISAM可以没有 | count(*)：包括了所有的列，相当于行数，在统计结果的时候， 不会忽略列值为NULL count(1)：包括了忽略所有列，用1代表代码行，在统计结果的时候， 不会忽略列值为NULL count(列名)：只包括列名那一列，在统计结果的时候，会忽略列值为空（表示null）的计数， 即某个字段值为NULL时，不统计。 |

