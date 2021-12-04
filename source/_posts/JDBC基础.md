---
title: JDBC基础
date: 2021-11-13 14:23:22
tags:
- java
categories:
- 后端
---

# JDBC

> 需要的jar包支持：

- `java.sql`

- `javax.sql`

- `mysql-connector-java`

    连接数据库的驱动（<u>必须要导入!</u>）

## 环境搭建

首先肯定是要有一个mysql的数据库（不管你是本地的还是远程的），然后创建一个数据库，使用idea进行连接(*就是进行配置*)

## 基本代码步骤


导入数据库依赖：

```xml
<dependencies>
    <!-- mysql驱动-->
    <!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>8.0.27</version>
    </dependency>
</dependencies>
```

```java
package com.rokelamen.test;

import java.sql.*;

public class TestJdbc {
    public static void main(String[] args) throws ClassNotFoundException, SQLException {
        // 配置信息
        String url = "jdbc:mysql://*.*.*.*:3306/jdbc?useUnicode=true&characterEncoding=utf8";
        String username = "root";
        String password = "*******";

        // 1. 加载驱动
        Class.forName("com.mysql.cj.jdbc.Driver");
        // 2. 连接数据库, 得到的connection代表数据库
        Connection connection = DriverManager.getConnection(url, username, password);

        // 3. 向数据库发送SQL的对象Statement : CRUD
        Statement statement = connection.createStatement();

        // 4. 编写sql
        String sql = "select * from users;";

        // 5. 执行查询sql语句,返回一个结果集
        ResultSet res = statement.executeQuery(sql);

        while (res.next()) {
            System.out.println("id=" + res.getObject("id"));
            System.out.println("name=" + res.getObject("name"));
            System.out.println("password=" + res.getObject("password"));
            System.out.println("email=" + res.getObject("email"));
            System.out.println("birthday=" + res.getObject("birthday"));
        }

        // 6. 关闭连接，释放资源 -> 先开后关
        res.close();
        statement.close();
        connection.close();
    }
}
```

*当然你要是使用增删改的话（只要不是查），就要用`executeUpdate`, 返回受影响的行数(int)*


### 使用预编译sql

```java
package com.rokelamen.test;

import java.sql.*;

public class TestJdbc02 {
    public static void main(String[] args) throws ClassNotFoundException, SQLException {
        // 配置信息
        String url = "jdbc:mysql://*.*.*.*:3306/jdbc?useUnicode=true&characterEncoding=utf8";
        String username = "root";
        String password = "********";

        // 1. 加载驱动
        Class.forName("com.mysql.cj.jdbc.Driver");
        // 2. 连接数据库, 得到的connection代表数据库
        Connection connection = DriverManager.getConnection(url, username, password);

        // 4. 编写sql
        String sql = "insert into users(id, name, password, email, birthday) values (?, ?, ?, ?, ?);";

        // 5. 使用预编译
        PreparedStatement preparedStatement = connection.prepareStatement(sql);

        preparedStatement.setInt(1, 5);   // 给第一个占位符？赋值为1
        preparedStatement.setString(2, "Rogers");   // 给第二个占位符？赋值
        preparedStatement.setString(3, "123456");   // 给第三个占位符？赋值
        preparedStatement.setString(4, "rokelamen@qq.com");   // 给第四个占位符？赋值
        preparedStatement.setDate(5, new Date(System.currentTimeMillis()));   // 给第五个占位符？赋值

        int i = preparedStatement.executeUpdate();

        if (i > 0) {
            System.out.println("插入成功！");
        }

        // 6. 关闭连接，释放资源 -> 先开后关
        preparedStatement.close();
        connection.close();
    }
}
```

## 事务

> 特性：要么都成功，要么都失败

> ACID原则：保证了数据的安全性

```java
package com.rokelamen.test;

import java.sql.*;

public class TestJdbc03 {
    public static void main(String[] args) throws ClassNotFoundException, SQLException {
        // 配置信息
        String url = "jdbc:mysql://*.*.*.*:3306/jdbc?useUnicode=true&characterEncoding=utf8";
        String username = "root";
        String password = "*******";

        Connection connection = null;

        try {
            // 1. 加载驱动
            Class.forName("com.mysql.cj.jdbc.Driver");
            // 2. 连接数据库, 得到的connection代表数据库
            connection = DriverManager.getConnection(url, username, password);

            // 3. 通知数据库开启事务，false是开启
            connection.setAutoCommit(false);

            // 4. 编写sql
            String sql = "update account set money = money - 100 where name = 'A';";
            connection.prepareStatement(sql).executeUpdate();

            // 制造错误
            int i = 1/0;

            String sql2 = "update account set money = money + 100 where name = 'B';";
            connection.prepareStatement(sql2).executeUpdate();

            connection.commit();    // 如果两条sql都执行成功了，就提交事务
            System.out.println("success");
        }catch (ClassNotFoundException e) {
            try {
                connection.rollback();
            }catch (SQLException e1) {
                e1.printStackTrace();
            }
        }finally {
            try {
                // 6. 关闭连接，释放资源 -> 先开后关
                connection.close();
            }catch (SQLException e) {
                e.printStackTrace();
            }
        }

    }
}
```

## Junit

> 我们创建一个maven模版项目的时候，看到idea每次都会自动导入一个依赖叫`junit`

> 这个依赖就是用来随时随地进行单元测试的，**这样你就可以加一个注解来执行任何你想要执行的方法!**

用法：

```xml
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.13.2</version>
</dependency>
```

```java
class A {
    @Test
    public void test() {
        ...
    }
}
```

<u>注意：`@Test`方法只有在方法上有效</u>

