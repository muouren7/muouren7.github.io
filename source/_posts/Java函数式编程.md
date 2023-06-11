---
title: Java函数式编程
tags: [Lambda,函数式编程]	

---

# Lambda表达式

## 概述

​	Lambda是JDK8中一个语法糖。他可以对某些匿名内部类的写法进行简化。它是函数式编程思想的一个重要体现。让我们不用关注是什么对象。而是更关注我们对数据进行了什么操作。

## 核心原则

可推导可省略

## 基本格式

```
(参数列表)->{代码}
```

**使用Lambda表达式的重要前提是必须有接口,且该接口只有一个抽象方法（也称为函数式接口）**

例1：

```java
new Thread(new Runnab1e( {
    @Override
    pub1ic void run(){
    	System.out.println("你知道吗我比你想象的更想在你身边");
    }
}).start();

```

改写后：

```java
new Thread(()->{
    	System.out.println("你知道吗我比你想象的更想在你身边");
	}
).start();
```

# Stream流

## 概述

​	Java8的Stream使用的是函数式编程模式，如同它的名字一样，它可以被用来对集合或数组进行链状流式的操作。可以更方便的让我们对集合或数组操作。

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
@EqualsAndHashcode //用于后期的去重使用
public c1ass Author {
    //id
    private Long id;
    //姓名
    private string name;
    //年龄
    private Integer age;
    //简介
    private string intro;
    //作品
    private List<Book> books;
}

```

## 快速入门

```java
//打印所有年龄小于18的作家的名字，并且要注意去重
List<Author> authors = getAuthors();
authors.
	stream()//把集合转换成流
	.distinct()//先去除重复的作家|
	.fi1ter(author -> author . getAge()<18)//筛选年龄小于18的
	.forEach(author -> System.out.print1n(author . getName() );//遍历打印名字

```

```java
@SpringBootTest()
public class TestDemo {

    @Test
    public void test(){
        List<Student> list = new ArrayList<>();
        Student student = new Student("muouren7",16);
        Student student1 = new Student("muouren7",16);
        Student student2 = new Student("muouren2",11);
        Student student3 = new Student("muouren3",20);
        Student student4 = new Student("muouren4",130);
        list.add(student);
        list.add(student1);
        list.add(student2);
        list.add(student3);
        list.add(student4);

        list.stream()
                .distinct()
                .forEach(s->{
                    System.out.println(s.getLastName()+s.getAge());
                });
    }
}

/*
打印结果
    muouren716
    muouren211
    muouren320
    muouren4130
*/
```

# Optional

示例

```java
@SpringBootTest()
public class TestDemo {

    @Test
    public void test(){
        Optional<Student> SS = Optional.ofNullable(new Student("muouren7",17));
        SS.ifPresent(s->{
            System.out.println(s.getLastName()+s.getAge());
        });
    }
}
```

























