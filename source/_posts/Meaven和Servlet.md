---
title: Meaven和Servlet
date: 2021-11-12 20:25:31
author: Rogers Kelamen
index_img: https://gitee.com/rogerskelamen/mdpic/raw/master/img/20211112215409.png
banner_img: https://gitee.com/rogerskelamen/mdpic/raw/master/img/20211112215833.png
tags:
- java
categories:
- 后端
---


# Maven

> 安装配置就不说了，都不知道配了多少次了：下载解压，改镜像源，设置maven仓库目录，最后override IDEA的默认设置

> 此Maven笔记是基于IDEA专业版的

> **记住：Maven约定大于配置**

## Maven Projects

> 通过点击Maven项目中的Maven Projects进入

### Lifecycle

都是maven的命令行操作

- clean

    删除运行后生成的文件

### Plugins

安装的插件，你也可以删掉

### Dependencies

项目的依赖

## `pom.xml`

> 首先`pom.xml`是maven的核心

你看到的这个文件的目录结构可能是这样的:

```xml
<?xml version="1.0" encoding="UTF-8"?>

<!-- Maven版本和头文件 -->
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <!-- 配置GAV -->
  <groupId>top.rokelamen</groupId>
  <artifactId>javaweb-01-maven</artifactId>
  <version>1.0-SNAPSHOT</version>

  <!-- 项目的打包方式 -->
  <packaging>war</packaging>

  <!-- 配置 -->
  <properties>
    <!-- 项目的默认构建编码 -->
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <!-- 编译版本 -->
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
  </properties>

  <!-- 项目依赖 -->
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>
  </dependencies>

  <!-- 项目构建用的东西 -->
  <build>
    <finalName>javaweb-01-maven</finalName>
    <pluginManagement><!-- lock down plugins versions to avoid using Maven defaults (may be moved to parent pom) -->
      <plugins>
        <plugin>
          <artifactId>maven-clean-plugin</artifactId>
          <version>3.1.0</version>
        </plugin>
        <!-- see http://maven.apache.org/ref/current/maven-core/default-bindings.html#Plugin_bindings_for_war_packaging -->
        <plugin>
          <artifactId>maven-resources-plugin</artifactId>
          <version>3.0.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.8.0</version>
        </plugin>
        <plugin>
          <artifactId>maven-surefire-plugin</artifactId>
          <version>2.22.1</version>
        </plugin>
        <plugin>
          <artifactId>maven-war-plugin</artifactId>
          <version>3.2.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-install-plugin</artifactId>
          <version>2.5.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-deploy-plugin</artifactId>
          <version>2.8.2</version>
        </plugin>
      </plugins>
    </pluginManagement>
  </build>
</project>
```

**在`dependencies`这里，maven最强大的地方就是可以通过指定一个依赖之后，安装这个依赖所依赖的所有依赖(实锤了，就是npm的功能)**

### 如果maven资源导出出了问题

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

## `servlet`

> `servlet`其实没那么神秘，虽然被成为小服务程序或者是服务连接器，**其实就是一个客户端(浏览器)到服务器的连接服务，我们编写`servlet`程序就是为浏览器发送数据服务编写程序**

> 简单来说就是和服务器请求和返回有关(http request, http response)

### 编写两步走

- 编写一个类，实现Servlet接口

- 把开发好的Java类部署到Web服务器中

### steps

继承使用`HttpServlet`接口  -> 重写那些请求方式

`HttpServlet`这个接口里面默认就是有那些请求方法的实现方法(但是很简单)，我们要做的就是重写那些方法

*使用重写一个接口的快捷键是<kbd>Ctrl</kbd>+<kbd>o</kbd>*

**我们通常需要重写的方法就是`doGet`, `doPost`**

### 编写Servlet映射

为什么编写映射: 我们写的是java程序，但是要通过浏览器访问，浏览器需要连接的是web服务器，所以我们需要在web服务中注册写好的servlet，还需要一个浏览器可以访问的路径。

```xml
<!--  注册Servlet-->
  <servlet>
    <servlet-name>hello</servlet-name>
    <servlet-class>com.rokelamen.servlet.HelloServlet</servlet-class>
  </servlet>

<!--  请求路径-->
  <servlet-mapping>
    <servlet-name>hello</servlet-name>
    <url-pattern>/hello</url-pattern>
  </servlet-mapping>
```

然后就需要配置tomcat服务器，最后启动test

### HttpServlet的使用

```java
// this.getInitParameter()  初始化参数
// this.getServletConfig()  servlet配置
// this.getServletContext()  servlet上下文
```

#### `getInitParameter`和`getServletConfig`

> 可以在`web.xml`进行配置和修改

*前者基本没什么用*

#### `ServletContext`

web容器启动时，它会为每一个web程序都创建一个对应ServletContext对象，它代表当前的web应用

```java
ServletContext servletContext = this.getServletContext();
```

##### 共享数据

在一个servlet中保存的数据，可以在另一个servlet中拿到

```java
public class HelloServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // this.getInitParameter()  初始化参数
        // this.getServletConfig()  servlet配置
        // this.getServletContext()  servlet上下文
        ServletContext servletContext = this.getServletContext();
        System.out.println("Hello World");
        String username = "Java";
        servletContext.setAttribute("username", username);
    }
}

public class GetServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        ServletContext servletContext = this.getServletContext();
        String username = (String) servletContext.getAttribute("username");

        resp.setContentType("text/html");
        resp.setCharacterEncoding("utf-8");
        resp.getWriter().println("名字为" + username);
    }
}
```

```xml
  <servlet>
    <servlet-name>hello</servlet-name>
    <servlet-class>com.rokelamen.servlet.HelloServlet</servlet-class>
  </servlet>
  <servlet-mapping>
    <servlet-name>hello</servlet-name>
    <url-pattern>/hello</url-pattern>
  </servlet-mapping>

  <servlet>
    <servlet-name>getname</servlet-name>
    <servlet-class>com.rokelamen.servlet.GetServlet</servlet-class>
  </servlet>
  <servlet-mapping>
    <servlet-name>getname</servlet-name>
    <url-pattern>/getname</url-pattern>
  </servlet-mapping>
```

##### 获取初始化参数

```java
public class ServletDemo01 extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        ServletContext context = this.getServletContext();

        String url = context.getInitParameter("url");
        resp.getWriter().print(url);
    }
}
```

```xml
<!--  配置web应用的初始化参数-->
  <context-param>
    <param-name>url</param-name>
    <param-value>jdbc:mysql://localhost:3306/mybatis</param-value>
  </context-param>
```

##### 请求转发

```java
public class ServletDemo02 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        ServletContext context = this.getServletContext();
        System.out.println("进入了ServletDemo02");

        // 请求转发一下, gp就是转发到的路径
        context.getRequestDispatcher("/gp").forward(req, resp);
    }
}
```

*记住：请求转发不是重定向!*

> 在编写请求转发的功能时，getRequestDispatcher分成两种，可以用request调用（也就是使用请求），也可以用getServletContext()调用

> 不同的是，request.getRequestDispatcher(url)的url可以是相对路径也可以是绝对路径。

***最终的答案：因为java很可能会把这个路径当成相对路径，所以，我们直接写这个文件名就行了（如果是同一个路径的话）***

##### 读取资源文件

Properties

- 在java目录下新建一个properties

- 在resources目录下新建一个properties

发现都被打包到了同一个文件目录下，classes，俗称路径为classpath

使用一个文件流

```java
public class ServletDemo03 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        ServletContext context =  this.getServletContext();
        InputStream is = context.getResourceAsStream("/WEB-INF/classes/db.properties");
        Properties prop = new Properties();
        prop.load(is);
        String username = prop.getProperty("username");
        String password = prop.getProperty("password");

        resp.getWriter().print(username + ": " + password);
    }
}
```

### `HttpServletResponse`

```java
public class FileServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 1. 获取下载文件的路径
//        String realPath = this.getServletContext().getRealPath("/avatar.jpg");
        String realPath = "/Users/rok/Documents/MyProgram/Java/javaweb-02-servlet/response/src/main/resources/avatar.jpg";
        System.out.println("下载的文件路径: " + realPath);

        // 2. 下载的文件名
        String fileName = realPath.substring(realPath.lastIndexOf("/") + 1);
        // 3. 设置浏览器支持下载文件
        resp.setHeader("Content-Disposition", "attachment;filename=" + URLEncoder.encode(fileName, "UTF-8"));
        // 4. 获取下载文件的输入流
        FileInputStream in = new FileInputStream(realPath);
        // 5. 创建缓冲区
        int len = 0;
        byte[] buffer = new byte[1024];
        // 6. 获取OutputStream对象
        ServletOutputStream out = resp.getOutputStream();
        // 7. 将FileOutputStream 流写入到buffer缓冲区中，使用OutputStream将缓冲区数据输出到客户端
        while ((len = in.read(buffer)) > 0) {
            out.write(buffer, 0, len);
        }

        // 关闭流
        in.close();
        out.close();
    }
}
```

#### `resp.set`

我这里说的是所有的`HttpServletResponse resp`的方法，比如`setContentType`, `setDateHeader`

- `setContentType`

    返回的文件类型

    resp.setContentType("image/jpeg")

- `setDateHeader`

    有关网站的缓存，下面是不让浏览器来缓存

    resp.setDateHeader("expires", -1)

*当然你还可以直接用`setHeader`直接对返回头尽情操作*

- `setHeader`

    resp.setHeader("Cache-control", "no-cache")

    这个也是对浏览器不缓存

#### 实现重定向

就拿用户登录这个场景来说：

最重要的就是`sendRedirect()`方法

```java
void sendRedirect(String val) throws IOException;
```

这个方式简单来理解就是用户访问一个接口之后，在顺利完成某些操作之后，这个接口直接去调用另一个接口

```java
public class RedirectServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.sendRedirect("/r/down");
    }
}
```

所以resp主要干啥的？

> 可以这么说，既然`req`是用来接收前端传过来的参数和数据，经过接口内部对数据处理之后，那么`resp`就是用来对返回的数据进行设置，以及之后的重定向等问题

### `HttpServletRequest`

> 通过这个对象我们可以获取客户端的所有发过来的信息

#### `req.get`

因为主要是获取参数的，所以只记录以下几种:

- `req.getparameter(String s)`

- `req.getParameterValues(String[] s)`

#### 获取参数，请求转发

```java
public class LoginServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        req.setCharacterEncoding("utf-8");
        resp.setCharacterEncoding("utf-8");

        String username = req.getParameter("username");
        String password = req.getParameter("password");
        String[] hobbies = req.getParameterValues("hobbies");
        System.out.println("=========================");
        System.out.println(username);
        System.out.println(password);
        System.out.println(Arrays.toString(hobbies));
        System.out.println("=========================");

        // 请求转发
        req.getRequestDispatcher("success.jsp").forward(req, resp);

    }
}
```

*可以发现，请求转发不变url，但是重定向会变url*

### Session & Cookie

> 表示两种保存会话的技术

- cookie

    客户端技术(响应，请求)，客户端browser得到一个用户的令牌token，以后用户凭令牌通行

- session

    服务器技术，利用这个技术，可以保存用户的会话信息（即记录这条会话：我已经访问过了，你给我通行）

<u>在哪会用到，肯定是登录逻辑啊</u>

#### Cookie

> 我们使用cookie主要用于以下几步：

1. 从请求中拿到cookie信息

2. 服务器响应给客户端cookie

```java
Cookie[] cookies = req.getCookies(); // 获得cookie
cookie.getName();   // 获得cookie中的key
cookie.getValue();  // 获得cookie中的value
new Cookie("lastLoginTime", System.currentTimeMillis() + "");   // 新建一个cookie
cookie.setMaxAge(24 * 60 * 60);  // 设置cookie的有效期
resp.addCookie(cookie);     // 返回给客户端一个cookie
```

> 小知识

删除Cookie：

- 不设置有效期，关闭浏览器，自动失效；

- 设置有效期时间为0；

    ```java
    Cookie cookie = new Cookie("name", "value");    // 这个必须要和你想要删除的cookie一致

    // 设置有效期时间为0；
    cookie.setMaxAge(0);
    resp.addCookie(cookie);
    ```

#### Session

- 服务器会给每一个用户（浏览器）创建一个Session对象

    浏览器存储Session的本质就是在cookie中加了一个叫session的对象

- 一个Session独占一个浏览器，只要浏览器没关闭，这个Session就存在

- 用户登录之后，整个网站都可以访问！ --> 保存用户信息: 保存购物车信息等

```java
public class Session01 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 解决乱码
        resp.setCharacterEncoding("utf-8");
        req.setCharacterEncoding("utf-8");
        resp.setContentType("text/html;charset=utf-8");

        // 得到session
        HttpSession session = req.getSession();

        // 在session中存入信息
        session.setAttribute("name", new Person("Rogers", 20));

        // 获取Session的ID
        String id = session.getId();

        // 判断Session是不是新创建的
        if (session.isNew()) {
            resp.getWriter().write("session创建成功! ID: " + id);
        }else {
            resp.getWriter().write("session已经在服务器中存在，ID: " + id);
        }
    }
}

public class Session02 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 解决乱码
        resp.setCharacterEncoding("utf-8");
        req.setCharacterEncoding("utf-8");
        resp.setContentType("text/html;charset=utf-8");

        // 得到session
        HttpSession session = req.getSession();

        // 获取session中的值
        Person person = (Person) session.getAttribute("name");

        System.out.println(person.toString());

    }
}
```

<u>这样就是另一种数据共享的方式，但是相比较ServletContext数据共享，这种方式更好(ServletContext容易卡服务器，而Session保证浏览器关闭后即可释放数据缓存)</u>

> 然后我们注销一下Session

```java
public class SessionDestroy extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        HttpSession session = req.getSession();
        session.removeAttribute("name");
        // 手动注销Session
        session.invalidate();
    }
}
```

#### 两种的区别

- Cookie是把用户的数据写给用户的浏览器，浏览器保存（可保存多个）

- Session把用户的数据写到用户独占Session中，服务器端保存。

- Session对象由服务器创建，其底层原理是创建Cookie

#### 最后数据共享

**Session适合单用户, 多信息存储**

**而ServletContext基于`ApplicationContext`适合于多用户共享数据，存储少量信息**

## `JSP`

> 什么是jsp

> `Java Server Page`: java服务器端页面，也和Servlet一样，用于动态Web技术


### `jsp`和`html`的区别

- HTML只给用户静态的数据，没有数据交互

- jsp页面中可以嵌入Java代码，为用户提供动态数据

**你会发现`jsp`和`ejs`有很多共同点!**


### jsp本质

<u>首先记住，`jsp`本质上就是`servlet`</u>

在jsp页面中：

只要是Java代码就会原封不动输出；

如果是HTML代码就会转化为`out.print("<html>\r\n")`

这样的格式输出到前端

### `jsp`基本语法

> jsp作为java页面级的扩展，完全支持java语法

jsp表达式

```jsp
<%--    JSP表达式
  作用：用来将程序输出，输出到客户端
--%>
<%= new java.util.Date()%>
```

jsp脚本片段

```jsp
<%--  jsp脚本片段--%>
  <%
    int sum = 0;
    for (int i = 0; i < 100; i++) {
      sum += i;
    }
    out.println("<h1>Sum = " + sum + "</h1>");
  %>
```

**说到底，jsp表达式就是数据输出，jsp脚本片段就是数据处理**

然后就是许多的骚操作：

在代码中嵌入HTML元素：

```jsp
<%!
  static {
    System.out.println("Loading Servlet!");
  }

  private int globalVar = 0;

  public void rogers() {
    System.out.println("进入了方法Rogers!");
  }
%>
```

这种JSP声明就会编译到JSP生成Java的类中，其他的，就会被生成到`_jspService`方法中

*注意：HTML注释还是会显示在页面上，所以统一用JSP注释方式：`<%-- --%>`*

### `jsp`指令

- `@page`

    能干啥呢：

    1. 首先导包:

    ```jsp
    <%@ page import="java.util.*" %>
    ```

    2. 定制错误页面：

    ```jsp
    <%@ page errorPage="error/500.jsp" %>
    ```

*当然我们可以直接配置`web.xml`:*

    ```xml
    <error-page>
        <error-code>404</error-code>
        <location>/error/404.jsp</location>
    </error-page>
    ```

    3. 开启el表达式

    ```jsp
    <%@ page isELIgnored="false" %>
    ```

- `@include`

    1. 前端页面组件

    ```jsp
    <%@ include file="component/header.jsp" %>
    ```

    *这个相当于是两个文件拼接*

*同样我们使用jsp标签也可以完成组件拼接*

    ```jsp
    <jsp:include page="/component/header.jsp"/>
    ```

    *这个就相当于是引用而已*


- `@taglib`


## 9大内置对象

- `PageContext`

- `Request`

存储数据 -> 数据共享

- `Response`

- `Session`

存储数据 -> 数据共享

- `Application` -> [ServletContext]

存储数据 -> 数据共享

- `config` -> [ServletConfig]

- `out`

- `page`

- `exception`

那么有这四种数据的存储方式，我们到底怎么用：

```jsp
<%
    pageContext.setAttribute("name1", "Rogers1");   // 保存的数据只在这个页面中有效
    request.setAttribute("name2", "Rogers2");   // 保存的数据只在一次请求中有效，请求转发会携带这个数据
    session.setAttribute("name3", "Rogers3");   // 保存的数据只在一次会话中有效，关闭浏览器失效
    application.setAttribute("name4", "Rogers4");   // 保存的数据在服务器中有效，关闭服务器失效
%>

<%
    String name1 = (String) pageContext.findAttribute("name1");
    String name2 = (String) pageContext.findAttribute("name2");
    String name3 = (String) pageContext.findAttribute("name3");
    String name4 = (String) pageContext.findAttribute("name4");
%>
<h3>${name1}</h3>
<h3>${name2}</h3>
<h3>${name3}</h3>
<h3>${name4}</h3>
```

**这个结果就是所有的数据都可以得到，所以本质上说每一个存储的变量通过`pageContext`都能得到**

*建议数据输出就用el表达式，`${}`*

当然这样是等价的：

```jsp
pageContext.setAttribute("hello", "hello1", PageContext.SESSION_SCOPE);
session.setAttribute("hello", "hello1");
```

这里是双亲委派机制, 就是: 从最低，层级式开始找, 无论找没找到都返回上一级一直找到等级上级有就替换

### 直接通过pageContext转发

```jsp
<%
    pageContext.forward("/index.jsp");
%>
```

#### jsp标签传值

```jsp
<jsp:forward page="jsptag2.jsp">
  <jsp:param name="name" value="rogers"/>
  <jsp:param name="age" value="20"/>
</jsp:forward>

<%
    request.getParameter("name");
%>
```

### JSTL表达式

> JSTL标签库就是为了弥补HTML标签的不足，它自定义了许多的标签，可供我们使用，标签功能和java代码一样

- `@taglib`

    引入JSTL核心标签库，我们才能使用JSTL标签

    ```jsp
    <%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
    ```

el表达式可以获取表单中的数据

```java
<input type="text" name="username" value="${param.username}">
```

**就算已经在Maven中导了包，在Tomcat中还是需要引入jstl的包，否则会报错：jstl解析错误**

> 我还是老老实实写java代码吧，这玩意不适合我

### `JavaBean`

`JavaBean`特定写法：

- 必须要有一个无参构造

- 属性必须私有化

- 必须有对应的get/set方法

> 为什么这么规定

**因为`JavaBean`一般用来和数据库的字段做映射(简单的ORM)**


<font size=5>ORM</font>

对象关系映射

- 表 -> 类

- 字段 -> 属性

- 行记录 -> 对象

```jsp
<jsp:useBean id="people" class="com.rokelamen.pojo.People" scope="page" />

<jsp:setProperty name="people" property="id" value="1"/>
<jsp:setProperty name="people" property="name" value="Ro"/>
<jsp:setProperty name="people" property="age" value="20"/>
<jsp:setProperty name="people" property="address" value="Wuhan"/>

id: <jsp:getProperty name="people" property="id"/>
name: <jsp:getProperty name="people" property="name"/>
age: <jsp:getProperty name="people" property="age"/>
address: <jsp:getProperty name="people" property="address"/>
```

**当然你先要在包里面有这个People类**

## Filter

Filter: 过滤器，用来过滤网站的数据

- 处理中文乱码

- 登录验证等

### 开发步骤

1. 导包

2. 编写过滤器（确认得是servlet的Filter）

    不要忘了用chain进行来接请求

    ```java
        public class CharacterEncodingFilter implements Filter {

        // 初始化
        public void init(FilterConfig filterConfig) throws ServletException {
            System.out.println("CharacterEncodingFilter初始化");
        }

        /*
        1. 所有过滤器中的代码，在过滤特定请求的时候都会执行
        2. 必须要让过滤器继续通行
         */
        public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
            request.setCharacterEncoding("utf-8");
            response.setCharacterEncoding("utf-8");
            response.setContentType("text/html;charset=UTF-8");

            System.out.println("CharacterEncodingFilter执行前...");
            chain.doFilter(request,response);
            System.out.println("CharacterEncodingFilter执行后...");
        }

        // 销毁
        public void destroy() {
            System.out.println("CharacterEncodingFilter销毁");
        }
    }
    ```

3. 配置过滤器

    ```xml
    <filter>
        <filter-name>CharacterEncodingFilter</filter-name>
        <filter-class>com.rokelamen.filter.CharacterEncodingFilter</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>CharacterEncodingFilter</filter-name>
<!--        只要是/servlet的任何请求，都会经过这个过滤器-->
        <url-pattern>/servlet/*</url-pattern>
    </filter-mapping>
    ```

### 监听器

编写监听器：

```java
// 统计网站的在线人数：统计session个数
public class OnlineCountListener implements HttpSessionListener {

    // 创建session监听：看你的一举一动
    // 一旦创建一个session就会触发一次这个事件
    public void sessionCreated(HttpSessionEvent se) {
        ServletContext ctx = se.getSession().getServletContext();

        System.out.println(se.getSession().getId());

        Integer onlineCount = (Integer) ctx.getAttribute("OnlineCount");

        if (onlineCount == null) {
            onlineCount = new Integer(1);
        }else {
            int count = onlineCount.intValue();
            onlineCount = new Integer(count + 1);
        }

        ctx.setAttribute("OnlineCount", onlineCount);
    }

    // 销毁session监听
    public void sessionDestroyed(HttpSessionEvent se) {
        ServletContext ctx = se.getSession().getServletContext();
        se.getSession().invalidate();
        System.out.println(se.getSession().getId());

        Integer onlineCount = (Integer) ctx.getAttribute("OnlineCount");

        if (onlineCount == null) {
            onlineCount = new Integer(1);
        }else {
            int count = onlineCount.intValue();
            onlineCount = new Integer(count - 1);
        }

        ctx.setAttribute("OnlineCount", onlineCount);
    }
}
```

配置监听器:

```xml
<!--    创建监听器-->
<listener>
    <listener-class>com.rokelamen.listener.OnlineCountListener</listener-class>
</listener>
```

### Filter验证案例

[gitee](https://gitee.com/rogerskelamen/javaweb-filter.git)

### Problems

#### jsp页面开启el表达式

jsp要访问绝对路径的时候，可以使用el表达式：

src="${pageContext.request.contextPath}/js/cc.js">

需要注意的是，要开启EL表达式：

<%@page isELIgnored="false"%>

或者在web.xml中整体的开启或者关闭EL表达式：

```xml
<jsp-config>

<jsp-property-group>

<url-pattern>*.jsp</url-pattern>

<el-ignored>false</el-ignore>

</jsp-property-group>

</jsp-config>
```
