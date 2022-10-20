---
title: 'Mybatis入门示例'
date: 2022-10-14 16:06:38
tags: [mybatis]
published: true
hideInList: false
feature: 
isTop: false
---
# Mybatis
## mybatis入门配置
1、在项目工程下导入**mybatis**的**jar**包，然后创建mybatis的配置文件，创建**mybatis-config.xml**文件，然后添加以下内容：
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>	<!--value中为连接数据库的驱动包名类名-->
                <property name="url" value="jdbc:mysql://localhost:3306"/>	<!--连接数据库url地址-->
                <property name="username" value="root"/>		<!--用户名-->
                <property name="password" value="123456"/>		<!--密码-->
            </dataSource>
        </environment>
    </environments>
</configuration>
```
2、配置数据库字段与实体类的映射，可以使用**lombok**+**注解**的方式，同时创建映射关系的**TestMapper.xml**
```java
import lombok.Data;
@Data
public class User {
    int N_USER_ID;				//字段名最好与数据库中的字段名保持一致，否则可能会映射失败
    String VC_LOGIN_NAME;
    String VC_PASSWORD;
}
```
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="TestMapper">
    <select id="selectUser" resultType="Test.User">		<!--resultType中填写数据库数据要映射的实体类，比如这里要映射到Test包下的User类中-->
        select * from t_user
    </select>
</mapper>
```
3、然后再在**mybatis-config.xml**中插入以下代码
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>	<!--value中为连接数据库的驱动包名类名-->
                <property name="url" value="jdbc:mysql://localhost:3306"/>	<!--连接数据库url地址-->
                <property name="username" value="root"/>		<!--用户名-->
                <property name="password" value="123456"/>		<!--密码-->
            </dataSource>
        </environment>
    </environments>
    
    <mappers>
    	<mapper url="file:TestMapper.xml"/>		<!--url中是mapper映射文件的地址-->
    </mappers>
    
</configuration>
```
4、在主方法中写下以下代码，并运行
```java
package Test;

import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;
import java.util.List;

public class MainTest {
    public static void main(String[] args) throws FileNotFoundException {
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(new FileInputStream("mybatis-config.xml"));
        try (SqlSession sqlSession = sqlSessionFactory.openSession(true)){
            List<User> list = sqlSession.selectList("selectUser");		//参数为mapper映射中的id，执行指定mapper中的语句
            for(User user:list){
                System.out.println(user.getN_USER_ID()+"    "+user.getVC_LOGIN_NAME()+"     "+user.getVC_PASSWORD());
            }
        }
    }
}
```













