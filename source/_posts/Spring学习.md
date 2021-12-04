---
title: Spring学习
date: 2021-11-25 17:00:07
author: Rogers Kelamen
index_img: https://gitee.com/rogerskelamen/mdpic/raw/master/img/20211201163001.png
banner_img: https://gitee.com/rogerskelamen/mdpic/raw/master/img/20211201162900.png
tags:
- java
categories:
- 后端
---

# Spring

> 历史

- 2002首次推出框架雏形：interface21框架

- 2004年3月24日发布Spring 1.0版本

> Spring理念

使现有的技术更加容易使用，本身是一个大杂烩，整合了现有技术框架

- SSH -> Strust2 + Spring + Hibernate

- SSM -> SpringMVC + Spring + Mybatis

[官网](https://spring.io/projects/spring-framework)

[官方下载地址](https://repo.spring.io/ui/packages) (需要自己搜索spring)

[GitHub地址](https://github.com/spring-projects/spring-framework)

## 安装步骤

首先在maven中导包 -> Spring Web MVC

```xml
<!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.3.13</version>
</dependency>
```

导这个包的话就可以包含很多包(包括core..)

*如果需要用jdbc的化，就用Spring整合的jdbc*

```xml
<!-- https://mvnrepository.com/artifact/org.springframework/spring-jdbc -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>5.3.13</version>
</dependency>
```

## 优点

- Spring是一个开源的免费框架（容器）

- Spring是一个轻量级的，非侵入式的框架

- 控制反转（IOC），面向切片编程（AOP）

- 支持事务的处理, 能够整合几乎所有的java框架！（就是一个容器，什么都能装！）

总结一下：<u>Spring就是轻量级的控制反转（IOC）和面向切片编程(AOP)的容器框架</u>

## 扩展

![](https://gitee.com/rogerskelamen/mdpic/raw/master/img/20211122141636.png)

- Spring Boot

    - 一个快速开发的脚手架（相当于`vue-cli`）

    - 基于SpringBoot可以快速开发单个微服务

    - **约定大于配置!**

- Spring Cloud

    - SpringCloud基于SpringBoot实现

现在大公司都在使用SpringBoot进行快速开发，学习SpringBoot的前提需要<u>掌握Spring和SpringMVC</u>

> 当然，还是有弊端的！

***发展了太久了，导致配置变得十分繁琐，俗称“配置地狱”***

## IOC

> 控制反转的底层理论？

1. UserDao接口

2. UserDaoImpl实现类

3. UserService业务接口

4. UserServiceImpl业务实现类

即三层架构思想

> 在我们之前的业务中，用户的需求可能会影响我们原来的代码，我们需要根据用户的需求去修改源代码！

```java
private UserDao userDao;

// 利用set动态实现值的注入！
public void setUserDao(UserDao userDao) {
    this.userDao = userDao;
}
```

我们使用一个Set接口实现，已经发生革命性变化！

before -> 程序是主动创建对象，控制权掌握在程序员手里

after -> 使用set注入之后，程序不再具有主动性，而是变成被动接收。

这种思想直接导致：<u>从本质上解决了问题，我们不需要管理对象的创建了</u>。系统耦合性降低，可以更加专注于业务的实现上。

***所以控制反转的意思就是：程序没有主动性，主动权在我们手上***


## HelloSpring

在resources中写一个beans.xml文件(注意，IDEA可能会让你去配置):

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

<!--    使用Spring来创建对象，在Spring这些都称为Bean
        类型 变量名 = new 类型();
        bean = 对象  new Hello();
        id = 变量名
        class = 类
        property 相当于给对象中的属性设置一个值
-->

    <bean id="hello" class="com.rokelamen.pojo.Hello">
        <property name="str" value="Spring"/>
    </bean>

    <!-- more bean definitions go here -->

</beans>
```

然后咱们肯定有一个实体类：

```java
package com.rokelamen.pojo;

public class Hello {

    private String str;

    // 必须要有set这个方法
    public void setStr(String str) {
        this.str = str;
    }

    public String getStr() {
        return str;
    }

    @Override
    public String toString() {
        return "Hello{" +
                "str='" + str + '\'' +
                '}';
    }
}
```

测试一下：

```java
import com.rokelamen.pojo.Hello;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class MyTest {
    public static void main(String[] args) {
        // 获取Spring上下文对象
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        // 我们的对象都放到Spring中管理了，我们要使用，直接去里面取出来就可以了
        Hello hello = (Hello) context.getBean("hello");
        System.out.println(hello.toString());
    }
}
```

现在我们说所谓IOC，就一句话来搞定：对象由Spring来创建，管理，装配


## IOC创建对象的方式

1. 使用无参构造函数创建对象(default)

2. 如果我们需要使用有参构造函数来创建对象

    1. 下标赋值

    ```java
    <bean id="user" class="com.rokelamen.pojo.User">
        <constructor-arg index="0" value="Rogers"/>
    </bean>
    ```

    2. 类型

    ```xml
    <bean id="user" class="com.rokelamen.pojo.User">
        <constructor-arg type="java.lang.String" value="Rogers"/>
    </bean>
    ```

    3. 参数名

    ```xml
    <bean id="user" class="com.rokelamen.pojo.User">
        <constructor-arg name="name" value="Rogers"/>
    </bean>
    ```


**还有一个很重要的一点就是：我们在getBean的时候Spring就已经把容器里面(写在xml中的)的对象都创建了**

## Spring配置

> 首先有一个`beans.xml`配置骨架

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- services -->

</beans>
```

### 别名(alias)

```xml
<!--    就是给这个对象容器对象创一个别名-->
<alias name="user" alias="myUser"/>
```

同时，也可以直接用`<bean>`的`name`属性来赋值取别名(*它甚至可以同时取多个别名，属性值用`,`分隔*)

### Bean配置

- name

    用来取别名

- scope

    用来指定作用域

```xml
<bean id="user" class="com.rokelamen.pojo.User" name="user2" scope="request">
    <constructor-arg name="name" value="Rogers"/>
</bean>
```

### `import`

`import`一般用于团队开发使用，它可以将多个配置文件，导入合并为一个

假设，现在有多个人在开发，这三个人负责不同的类别的类开发，他们都创建了自己的`applicationContext.xml`这个Spring容器，那我们就可以在主配置文件中使用`import`将这些个文件引入

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- services -->

    <import resource="beans.xml"/>
    <import resource="beans1.xml"/>

</beans>
```

## 依赖注入

### 构造器注入

> 请参见"IOC创建对象的方式"

### 通过Set方式注入

> 很重要!

- 依赖注入

    - 依赖：bean对象的创建依赖于容器!

    - 注入：bean对象的所有属性，由容器来注入!

> 环境搭建

1. 复杂类型

2. 真实测试对象

#### 依赖注入

```xml
<bean id="student" class="com.rokelamen.pojo.Student">
<!--        第一种：普通注入，value-->
    <property name="name" value="Rogers"/>
</bean>
```

*要是有其他类型的数据呢？*

```xml
<bean id="address" class="com.rokelamen.pojo.Address">
    <property name="address" value="武汉"/>
</bean>

<bean id="student" class="com.rokelamen.pojo.Student">
<!--        第一种：普通注入，value-->
    <property name="name" value="Rogers"/>

<!--        第二种：Bean注入，ref-->
    <property name="address" ref="address"/>

<!--        数组注入，array -->
    <property name="books">
        <array>
            <value>红楼梦</value>
            <value>西游记</value>
            <value>三国演义</value>
            <value>水浒传</value>
        </array>
    </property>

<!--        list注入，list-->
    <property name="hobbies">
        <list>
            <value>听歌</value>
            <value>代码</value>
            <value>电影</value>
        </list>
    </property>

<!--        Map注入，map > entry-->
    <property name="card">
        <map>
            <entry key="身份证" value="1111"/>
            <entry key="编号" value="2222"/>
        </map>
    </property>

<!--        Set注入，-->
    <property name="games">
        <set>
            <value>LOL</value>
            <value>CSGo</value>
            <value>HearthStone</value>
        </set>
    </property>

<!--        null即boolean-->
    <property name="pet">
        <null/>
    </property>

<!--        Properties注入，props > prop > 内容值在标签中-->
    <property name="info">
        <props>
            <prop key="学号">2019001</prop>
            <prop key="性别">男</prop>
            <prop key="民族">汉族</prop>
        </props>
    </property>
</bean>
```



## JavaBean

> Bean的中文含义是“豆子”，顾名思义JavaBean是一段Java小程序。JavaBean实际上是指一种特殊的Java类，它通常用来实现一些比较常用的简单功能，并可以很容易的被重用或者是插入其他应用程序中去。所有遵循一定编程原则的Java类都可以被称作JavaBean。

所以这里的javabean是一类有特殊功能，遵循一定编程原则的java类

这些类遵循一个接口格式，以便于使函数命名、底层行为以及继承或实现的行为，其最大的优点在于可以实现代码的可重用性。Bean并不需要继承特别的基类(BaseClass)或实现特定的接口(Interface)。Bean的编写规范使Bean的容器(Container)能够分析一个Java类文件，并将其方法(Methods)翻译成属性(Properties)，即把Java类作为一个Bean类使用。Bean的编写规范包括Bean类的构造方法、定义属性和访问方法编写规则。

## CPX

> 嗯？这是什么东西

**记住“擦皮鞋”**

相信我，你会想要记住它的(CPX for ClassPathXmlApplicationContext)

所以每次你想要找到Spring容器的对象类时，你就得要这个ClassPathXmlApplicationContext，而我们按<kbd>CPX</kbd>idea就可以直接生成这个全称


