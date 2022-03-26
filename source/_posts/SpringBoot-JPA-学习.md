---
title: SpringBoot JPA 学习
date: 2021-11-28 10:00:20
index_img: https://s2.loli.net/2022/03/26/GBK3QpjrdZchibz.png
banner_img: https://s2.loli.net/2022/03/26/cQ14OHDXrT7mil3.png
author: Rogers Kelamen
tags:
- java
categories:
- 后端
---


# SpringBoot JPA

> 虽然我还没学完SpringBoot，但是我们还是可以直接去开一个JPA项目来了解这个数据持久化层的应用。

## Start

> 使用IDEA的Spring构建(`Spring Initializr`)来快速完成SpringBoot JPA项目的搭建

*在依赖中选中`SQL`中的`Spring Data JPA`和`MySql Driver`*(当然这是使用mysql数据库)

## Connect with DataBase

在resources中生成的`application.properties`写入数据库中的配置：

```properties
# datasource四大件
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/jpa
spring.datasource.username=root
spring.datasource.password=12345678

# 设置web服务器的端口
server.port=8080

# 开启jpa的逆向工程(开发阶段设置为true，线上环境为false)
spring.jpa.generate-ddl=true
# 生成数据表的规则
spring.jpa.hibernate.ddl-auto=update
# 开启sql语句提示
spring.jpa.show-sql=true
```

### `dll-auto`的详解

- `create`

    每次运行springboot就会删除原来的表，重新创建新表(`drop-create`)

- `create-drop`

    每次运行创建数据表，运行完毕后将数据表删除

- `update`

    当实体类和数据库中的数据表不对应时，重新更新表

- `none`

    功能无效，不自动创建数据表

### 使用了`dll-auto`还是不生成表？

> 有很多可能会造成这种情况，需要根据具体情况来分析

这里有一份[错误排除文章](http://www.codebaoku.com/it-java/it-java-222622.html)，总有一种解决方式适合你😆


## 编写实体类(pojo)

```java
package com.rokelamen.jpa.pojo;

import javax.persistence.*;

// Entity注解可以使实体类和数据表联系起来
// name 属性指定表名
@Entity(name = "t_user")
public class User {

    // Id注解指定类成员为主键
    @Id
    // GeneratedValue指定了主键的生成策略
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int id;

    // Column表示此类成员为普通字段
    @Column
    private String username;

    @Column
    private String password;

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }
}
```

*需要注意的是，如果`pojo`在启动包的同级的话，就无法生成和关联数据表(也就是说必须要是启动包的子包)。要是想要查找到不是子包的`pojo`，就得这样:*

```java
// 启动类中
package com.rokelamen.jpa;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.autoconfigure.domain.EntityScan;

@SpringBootApplication
@EntityScan("com.rokelamen.pojo")
public class SpringbootJpaTestApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringbootJpaTestApplication.class, args);
    }

}
```

加上`@EntityScan`注解

## dao层继承Jpa仓库

```java
package com.rokelamen.jpa.dao;

import com.rokelamen.jpa.pojo.User;
import org.springframework.data.jpa.repository.JpaRepository;

public interface UserDao extends JpaRepository<User, Integer> {

}
```

## 编写`Controller`

```java
package com.rokelamen.jpa.controller;

import com.rokelamen.jpa.dao.UserDao;
import com.rokelamen.jpa.pojo.User;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@Controller
@RequestMapping(path = "/jpa")
public class MainController {

    @Autowired

    private UserDao userDao;

    @GetMapping(path = "/add")
    public @ResponseBody String addNewUser(@RequestParam String username,
                                           @RequestParam String password) {
        User user = new User();
        user.setUsername(username);
        user.setPassword(password);
        userDao.save(user);
        return "Saved!";
    }

    @GetMapping(path = "/getall")
    public @ResponseBody
    List<User> getAllUser() {
        return userDao.findAll();
    }
}
```

