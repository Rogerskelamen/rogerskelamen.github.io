---
title: Mybatis学习
date: 2021-11-21 17:38:40
author: Rogers Kelamen
index_img: https://s2.loli.net/2022/03/26/lzriHPtIU39ZoNf.png
banner_img: https://s2.loli.net/2022/03/26/HVpYPun5XEGT8bR.png
tags:
- java
categories:
- 后端
---


# Mybatis

> 首先Mybatis是用来做数据持久层的，你不用再用jdbc去写那些繁琐的sql语句了

[官方中文文档](https://mybatis.org/mybatis-3/zh/index.html)

[Github地址](https://github.com/mybatis/mybatis-3)

## 持久化

数据持久化

持久化就是将程序的数据在持久状态和瞬时状态转化的过程

> 为什么要持久化?

- 有一些对象数据，不能让他用完就丢掉

- 内存太贵

## 持久层

> 那些是持久层?

Dao层，Service层，Controller层...

- 完成持久化工作的代码块

- 层界限十分明显

## Mybatis的优势

- 帮助程序员将数据存入到数据库中

- 方便易用

- 传统的JDBC代码复杂，为了简化，框架化，自动化

- 具体优势：

1. 解除sql与程序代码的耦合：通过提供DAO层，将业务逻辑和数据访问逻辑分离，使系统的设计更清晰，更易维护，更易单元测试。sql和代码的分离，提高了可维护性。

2. 提供映射标签，支持对象与数据库的orm字段关系映射

3. 提供对象关系映射标签，支持对象关系组建维护

4. 提供xml标签，支持编写动态sql

**最重要的是：它是使用的最多的SSM框架中的一员**

## 步骤

1. 搭建数据库:

```sql
create database `mybatis`;

use `mybatis`;

create table `user`(
  `id` int(20) primary key auto_increment,
  `name` varchar(30) default null,
  `pwd` varchar(30) default null
)engine=INNODB default charset=utf8;

insert into user(id, name, pwd)
values
(1, '小明', '12345'),
(2, '小红', '12345'),
(3, '小强', '12345')
```

2. 新建项目，并将这个项目变成父工程

3. 导入依赖：

```xml
<!--    导入依赖-->
<dependencies>
<!--        mysql驱动-->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>8.0.27</version>
    </dependency>
<!--        mybatis-->
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.5.7</version>
    </dependency>
<!--        junit-->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.13.2</version>
    </dependency>
</dependencies>
```

### 创建模块Module

1. 编写`mybatis`的核心配置文件

在`/main/resources`下创建一个文件名叫`mybatis-config`:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<!--核心配置文件-->
<configuration>

    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://*.*.*.*:3306/mybatis?useSSL=true&amp;useUnicode=true&amp;characterEncoding=UTF-8&amp;serverTimezone=GMT"/>
                <property name="username" value="root"/>
                <property name="password" value="*****"/>
            </dataSource>
        </environment>
    </environments>

    <mappers>
        <mapper resource="org/mybatis/example/BlogMapper.xml"/>
    </mappers>
</configuration>
```

*为什么要设置配置文件：这里面定义了所有的环境，相当于告诉mybatis，我这个数据库在哪，怎么连的(只告诉idea是不行的)*


2. 编写mybatis工具类

在com.rokelamen.utils下创建一个工具类：

```java
package com.rokelamen.utils;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.IOException;
import java.io.InputStream;

// sqlSessionFactory -> sqlSession
public class MybatisUtils {

    private static SqlSessionFactory sqlSessionFactory;

    static {
        try {
            // 获取sqlSessionFactory对象
            String resource = "mybatis-config.xml";
            InputStream inputStream = Resources.getResourceAsStream(resource);
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    // 既然已经有了sqlSessionFactory，就可以获取到SqlSession到实例了
    public static SqlSession getSqlSession() {
        return sqlSessionFactory.openSession();
    }
}
```

**这个主要是要获取到SqlSession实例对象**

3. 编写实体类

```java
package com.rokelamen.pojo;

public class User {
    private int id;
    private String name;
    private String pwd;

    public User() {
    }

    public User(int id, String name, String pwd) {
        this.id = id;
        this.name = name;
        this.pwd = pwd;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getPwd() {
        return pwd;
    }

    public void setPwd(String pwd) {
        this.pwd = pwd;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", pwd='" + pwd + '\'' +
                '}';
    }
}
```

4. Dao接口

```java
package com.rokelamen.dao;

import com.rokelamen.pojo.User;

import java.util.List;

public interface UserDao {
    List<User> getUserList();
}
```

5. 接口实现类由原来的UserDaoImpl转变成一个Mapper配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!--namespace=绑定一个对应到Dao/Mapper接口-->
<mapper namespace="com.rokelamen.dao.UserDao">
<!--    select查询语句-->
    <select id="getUserList" resultType="com.rokelamen.pojo.User">
        select * from mybatis.user;
    </select>
</mapper>
```

### 测试

注意点：

你可能需要在核心的配置文件中注册`mappers`

- junit测试

```java
package com.rokelamen.dao;

import com.rokelamen.pojo.User;
import com.rokelamen.utils.MybatisUtils;
import junit.framework.TestCase;
import org.apache.ibatis.session.SqlSession;
import org.junit.Test;

import java.util.List;

public class UserDaoTest{
    @Test
    public void test() {

        // 获得SqlSession对象
        SqlSession sqlSession = MybatisUtils.getSqlSession();

        // 执行SQL
        UserDao userDao = sqlSession.getMapper(UserDao.class);
        List<User> userList = userDao.getUserList();

        for (User user :
                userList) {
            System.out.println(user);
        }

        //关闭SqlSession
        sqlSession.close();
    }
}
```

测试可能遇到的问题：

1. 配置文件没有注册

2. 绑定接口错误

3. 方法名不对

4. 返回类型不对

5. Maven导出资源问题

6. 如果是用`go to`生成的test类，就需要把`extends`删除


```xml
<!--在build中配置resources，来防止我们资源导出失败的问题-->
<build>
    <resources>
        <resource>
            <directory>src/main/resources</directory>
            <includes>
                <include>**/*.properties</include>
                <include>**/*.xml</include>
            </includes>
            <filtering>false</filtering>
        </resource>
        <resource>
            <directory>src/main/java</directory>
            <includes>
                <include>**/*.properties</include>
                <include>**/*.xml</include>
            </includes>
            <filtering>false</filtering>
        </resource>
    </resources>
</build>
```

## CRUD

1. namespace

namespace中的包名要和Dao/mapper接口的包名一致

2. select

选择，查询语句

> 首先是在Mapper.xml文件里面写

- id: 就是对应的namespace中的方法名

- resultType: Sql语句执行的返回值类型

- parameterType: 参数类型

*以前是创建一个Dao层的实现类，现在是直接在`xml`文件中配置这个sql语句*

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!--namespace=绑定一个对应到Dao/Mapper接口-->
<mapper namespace="com.rokelamen.dao.UserMapper">
<!--    select查询语句-->
    <select id="getUserList" resultType="com.rokelamen.pojo.User">
        select * from mybatis.user;
    </select>

    <select id="getUserById" parameterType="int" resultType="com.rokelamen.pojo.User">
        select * from mybtis.user where id = #{id}
    </select>
</mapper>
```

**可以看到我们直接将参数写到了`#{}`这样的一个表达式里面，接下来就可以直接在大括号`{}`里面写上在定义类中的传入参数**

3. Insert

```xml
<insert id="addUser" parameterType="com.rokelamen.pojo.User">
    insert into mybatis.user(id, name, pwd) values (#{id}, #{name}, #{pwd})
</insert>
```

```java
// 增删改需要提交事务
@Test
public void addUser() {
    SqlSession sqlSession = MybatisUtils.getSqlSession();

    UserMapper mapper = sqlSession.getMapper(UserMapper.class);

    int rok = mapper.addUser(new User(4, "Rok", "111111"));
    if (rok > 0) {
        System.out.println("插入成功!");
    }

    // 提交事务
    sqlSession.commit();

    sqlSession.close();
}
```

4. Update

```xml
<update id="updateUser" parameterType="com.rokelamen.pojo.User">
    update mybatis.user
    set name = #{name}, pwd = #{pwd}
    where id = #{id};
</update>
```

```java
// 更改
@Test
public void updateUser() {
    SqlSession sqlSession = MybatisUtils.getSqlSession();

    UserMapper mapper = sqlSession.getMapper(UserMapper.class);

    int mick = mapper.updateUser(new User(4, "mick", "22222"));
    if (mick > 0) {
        System.out.println("更新成功！");
    }
    sqlSession.commit();

    sqlSession.close();
}
```

5. Delete

```xml
<delete id="deleteUser" parameterType="int">
    delete
    from mybatis.user
    where id = #{id};
</delete>
```

```java
// 删除一个用户
@Test
public void deleteUser() {
    SqlSession sqlSession = MybatisUtils.getSqlSession();

    UserMapper mapper = sqlSession.getMapper(UserMapper.class);

    int i = mapper.deleteUser(4);

    if (i > 0) {
        System.out.println("删除成功！");
    }
    sqlSession.commit();

    sqlSession.close();
}
```

## 万能的Map

假设，我们的实体类，或者数据库中的表，字段或者参数过多，我们应当考虑使用Map

```java
int addUser2(Map<String, Object> map);
```

```xml
<insert id="addUser2" parameterType="map">
    insert into mybatis (id, name, pwd)
    values (#{userid}, #{username}, #{password});
</insert>
```

```java
public void addUser2() {
    SqlSession sqlSession = MybatisUtils.getSqlSession();

    UserMapper mapper = sqlSession.getMapper(UserMapper.class);

    Map<String, Object> map = new HashMap<String, Object>();
    map.put("userid", 5);
    map.put("username", "hello");
    map.put("password", "555555");


    mapper.addUser2(map);
    sqlSession.close();
}
```

- Map传递参数，直接在sql中取出key即可

`parameterType="map"`

- 对象传递参数，直接在sql中取出对象的属性即可

`parameterType="Object"`

- 多个参数用Map，或者注解

### 模糊查询

> 首先肯定使用`like`来将数据进行模糊查询的筛选

1. java代码执行的时候，传递通配符`% %`

2. 在sql拼接中使用通配符

```xml
<select id="getUserLike" parameterType="String" resultType="com.rokelamen.pojo.User">
    select * from mybatis.user where name like #{value}
</select>
```

```java
@Test
public void getUserLike() {
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);

    List<User> userList = mapper.getUserLike("%小%");

    for (User user :
            userList) {
        System.out.println(user);
    }

    sqlSession.close();
}
```

## 配置解析(mybatis-config.xml)

### 环境配置(environment)

使用多个`environment`

- dataSource

Mybatis默认的事务管理器就是JDBC，连接池：`POOLED`

### 属性(properties)

我们可以通过`properties`属性来实现引用配置文件

这些属性都是可外部配置且可动态替换的，既可以在典型的Java属性文件中配置，亦可以通过`properties`元素的子元素来传递(`db.properties`)

编写一个数据库连接配置文件：

```properties
driver=com.mysql.cj.jdbc.Driver
url=jdbc:mysql://*.*.*.*:3306/mybatis?useSSL=true&useUnicode=true&characterEncoding=UTF-8&serverTimezone=GMT
username=root
password=*******
```

然后直接引入就好啦

```xml
<!--    引入外部配置文件-->
<properties resource="db.properties"/>

<environments default="development">
    <environment id="development">
        <transactionManager type="JDBC"/>
        <dataSource type="POOLED">
            <property name="driver" value="${driver}"/>
            <property name="url" value="${url}"/>
            <property name="username" value="${username}"/>
            <property name="password" value="${password}"/>
        </dataSource>
    </environment>
</environments>
```

*当然我们就可以直接用`${}`来表示配置字符串了*

> 这个重要点在于我们可以通过外部文件来引入连接字符串

### 别名(typeAlias)

> 起别名的意义是用来减少类名中的**包路径**冗余

> 反正还可以解决一系列的冗余吧

```xml
<!--    可以给实体类起别名-->
<typeAliases>
    <typeAlias type="com.rokelamen.pojo.User" alias="User"/>
</typeAliases>
```

当然也可以指定一个包名，Mybatis会把包名下面的搜索需要的Java Bean：

扫描实体类的包，它的默认别名就为这个类的类名，首字母小写!

```xml
<!--    可以给实体类起别名-->
<typeAliases>
    <package name="com.rokelamen.pojo"/>
</typeAliases>
```

注意这里：

```xml
<select id="getUserList" resultType="user">
    select * from mybatis.user;
</select>
```

**实体类的名字最好是写小写，当然我们也可以用大写(也不会错)**

#### 注解`@Alias()`

> 我们可以直接在实体类上写注解来随便起别名（灵活多了），mybatis特别提供的

```java
@Alias("hello")
public void User{
    ...
}
```

*当然了，我们还是要写`<package>`*

### 设置(Settings)

> 这是 MyBatis 中极为重要的调整设置，它们会改变 MyBatis 的运行时行为。

- `cacheEnabled`

开启缓存

- `lazyLoadingEnabled`

开启懒加载

- `logImpl`

指定mybatis的日志实现形式

### 其他设置

- `typeHandlers`

类型处理器

- `objectFactory`

对象工厂

- plugins（推荐的插件）

    - mybatis-generator-core

    - mybatis-plus(这个是真的好用)

### 映射器(mappers)

MapperRegistry: 注册绑定我们的Mapper文件

- 方式一

使用相对于类的路径的资源(resource)引用：

```xml
<mappers>
    <mapper resource="com/rokelamen/dao/UserMapper.xml"/>
</mappers>
```

- 方式二

使用class文件绑定注册(class):

```xml
<mappers>
    <mapper class="com.rokelamen.dao.UserMapper"/>
</mappers>
```

*注意点：但是这个Mapper必须和xml配置文件同名，同时也必须在同一个包下*

- 方式三

直接使用包名，来覆盖所有的配置文件

```xml
<mappers>
    <package name="com.rokelamen.dao"/>
</mappers>
```

**感觉这个才是最强大的**

<u>但是这个的注意点也是和方式二相同</u>

## 日志

> 如果一个数据库操作，出现了异常，我们需要排错，就需要人性化一点的日志(不得不说java的报错真难看)

之前 -> `sout`, `debug`

现在 -> 日志工厂

- `LOG4J`

需要我们来导入依赖

- `STDOUT_LOGGING`

集成到mybatis中了已经

> 在mybatis核心配置文件中(mybatis-config.xml)配置日志

```xml
<settings>
    <setting name="logImpl" value="STDOUT_LOGGING"/>
</settings>
```

### Log4j

> 什么是Log4j

Apache Log4j是一个基于Java的日志记录工具。它是由Ceki Gülcü首创的，现在则是Apache软件基金会的一个项目。 log4j是几种Java日志框架之一。

1. *首先导入一波依赖*

```xml
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
```

2. 在resource上创建`log4j.properties`

```xml
#将等级为DEBUG的日志信息输出到console和file这两个目的地，console和file的定义在下面的代码
log4j.rootLogger=DEBUG,console,file

#控制台输出的相关设置
log4j.appender.console = org.apache.log4j.ConsoleAppender
log4j.appender.console.Target = System.out
log4j.appender.console.Threshold=DEBUG
log4j.appender.console.layout = org.apache.log4j.PatternLayout
log4j.appender.console.layout.ConversionPattern=[%c]-%m%n

#文件输出的相关设置
log4j.appender.file = org.apache.log4j.RollingFileAppender
log4j.appender.file.File=./log/app.log
log4j.appender.file.MaxFileSize=10mb
log4j.appender.file.Threshold=DEBUG
log4j.appender.file.layout=org.apache.log4j.PatternLayout
log4j.appender.file.layout.ConversionPattern=[%p][%d{yy-MM-dd}][%c]%m%n

#日志输出级别
log4j.logger.org.mybatis=DEBUG
log4j.logger.java.sql=DEBUG
log4j.logger.java.sql.Statement=DEBUG
log4j.logger.java.sql.ResultSet=DEBUG
log4j.logger.java.sql.PreparedStatement=DEBUG
```

3. 配置log4j为日志的实现

```xml
<settings>
<!--        标准的日志工厂实现-->
    <setting name="logImpl" value="LOG4J"/>
</settings>
```

#### 简单使用

1. 首先导入`org.apache.log4j.Logger`

2. 获取日志对象，参数为当前的类:

```java
static Logger logger = Logger.getLogger(UserMapperTest.class);
```

3. 写日志级别（有三种）：

```java
@Test
public void testLog4j() {
    logger.info("info: 进入了testLog4j方法");
    logger.debug("debug: 进入了testLog4j方法");
    logger.error("error: 进入了testLog4j方法");
}
```

## 使用注解

我们可以直接在控制类上的方法上写sql语句(使用注解):

```java
public interface UserMapper {
    @Select("select * from user")
    List<User> getUser();
}
```

但是还是需要在核心配置文件中绑定接口：

```xml
<mappers>
    <mapper class="com.rokelamen.dao.UserMapper"/>
</mappers>
```

<u>使用注解来映射简单语句会使代码显得更加简洁，但对于稍微复杂一点的语句，Java 注解不仅力不从心，还会让你本就复杂的 SQL 语句更加混乱不堪。 因此，如果你需要做一些很复杂的操作，最好用 XML 来映射语句。</u>

*本质：反射机制实现!*

