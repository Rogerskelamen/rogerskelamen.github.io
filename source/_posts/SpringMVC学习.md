---
title: SpringMVC学习
date: 2021-12-03 22:59:16
author: Rogers Kelamen
index_img: https://gitee.com/rogerskelamen/mdpic/raw/master/img/20211203230502.png
banner_img: https://gitee.com/rogerskelamen/mdpic/raw/master/img/20211203230214.png
tags:
- java
categories:
- 后端
---


# SpringMVC

> ssm: Spring + SpringMVC + mybatis

> 还有一点需要明确的是SpringMVC是针对javaWeb的,所以MVC中的Controll就是对应javaWeb的servlet

因为Spring是一个大杂烩，所以我们可以将SpringMVC中的所有要用到的bean，注册到Spring中。

## 什么是MVC

MVC: 模型(dao, service)，视图(jsp)，控制器(Servlet)

## 依赖导入

- `junit`

- `spring-webmvc`

- `servlet`

- `jsp`

- `jstl`


## 步骤

1. 注册`DispatcherServlet`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <!--配置DispatcherServlet：这个是SpringMVC的核心；请求分发器，前端控制器-->
    <servlet>
        <servlet-name>springmvc</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!--DispatcherServlet要绑定Spring的配置文件-->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc-servlet.xml</param-value>
        </init-param>
        <!--启动级别: 1-->
        <load-on-startup>1</load-on-startup>
    </servlet>

    <!--
     在SpringMVC中,   / /*
     /  ->  只会匹配所有的请求，不会去匹配jsp
     /* ->  匹配所有的请求, 包括jsp
    -->
    <servlet-mapping>
        <servlet-name>springmvc</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</web-app>
```

2. 关联一个配置文件

关联到springmvc-servlet.xml文件中, 这个文件就是之前讲Spring的IOC容器：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- 处理器映射器 -->
    <bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>
    <!-- 处理器适配器 -->
    <bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"/>

    <!-- 视图解析器: 模版引擎 Thymeleaf -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="internalResourceViewResolver">
        <!--前缀-->
        <property name="prefix" value="/WEB-INF/jsp/"/>
        <!--后缀-->
        <property name="suffix" value=".jsp"/>
    </bean>

</beans>
```

3. 创建一个控制器(controller)

```java
import org.springframework.web.servlet.ModelAndView;
import org.springframework.web.servlet.mvc.Controller;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public class HelloController implements Controller {
    @Override
    public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {

        ModelAndView mv = new ModelAndView();

        // 业务代码
        String result = "HelloSpringMVC";
        mv.addObject("msg", result);

        // 视图跳转
        mv.setViewName("test");

        return mv;
    }
}
```

4. 给springIOC容器注册一个叫hello的bean

```xml
<bean id="/hello" class="com.rokelamen.controller.HelloController"/>
```

5. 编写相应的jsp页面，显示存放的数据

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    ${msg}
</body>
</html>
```

### 404报错?

![](https://gitee.com/rogerskelamen/mdpic/raw/master/img/20211128210328.png)

看一下tomcat下面有没有这个lib，如果没有的话就需要手动来来创建文件夹导入所有的依赖

### 为什么要改成这样的javaWeb框架(SpringMVC)

我们就完全摆脱了写servlet，并且编写配置文件的冗余，只需要来指明一下是哪个文件来暴露给网络(<u>直接写Controller！</u>)

![](https://gitee.com/rogerskelamen/mdpic/raw/master/img/20211129151919.png)

这里面的实线都是SpringMVC帮我们做好的，我们只需要去实现虚线的位置


## 注解版

> 我们其实可以完全用注解来代替之前的那些繁琐配置，不过你用过之后就不想思考上面👆的原理了😂

1. 还是先配置一下`web.xml`，即配置一下DispatcherServlet，让它工作起来:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <!--配置DispatcherServlet：这个是SpringMVC的核心；请求分发器，前端控制器-->
    <servlet>
        <servlet-name>springmvc</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!--DispatcherServlet要绑定Spring的配置文件-->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc-servlet.xml</param-value>
        </init-param>
        <!--启动级别: 1-->
        <load-on-startup>1</load-on-startup>
    </servlet>

    <!--
     在SpringMVC中,   / /*
     /  ->  只会匹配所有的请求，不会去匹配jsp
     /* ->  匹配所有的请求, 包括jsp
    -->
    <servlet-mapping>
        <servlet-name>springmvc</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

</web-app>
```

2. 然后配置关联的`resources/springmvc-servlet.xml`，使我们的注解得以生效:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/mvc
        https://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <!-- 自动扫描包，让指定包下的注解生效,由IOC容器统一管理 -->
    <context:component-scan base-package="com.rokemen.controller"/>


    <!-- 让Spring MVC不处理静态资源 -->
    <mvc:default-servlet-handler />

    <!--
    支持mvc注解驱动
        在spring中一般采用@RequestMapping注解来完成映射关系
        要想使@RequestMapping注解生效
        必须向上下文中注册DefaultAnnotationHandlerMapping
        和一个AnnotationMethodHandlerAdapter实例
        这两个实例分别在类级别和方法级别处理。
        而annotation-driven配置帮助我们自动完成上述两个实例的注入。
     -->
    <mvc:annotation-driven />

    <!-- 视图解析器 -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver"
          id="internalResourceViewResolver">
        <!-- 前缀 -->
        <property name="prefix" value="/WEB-INF/jsp/" />
        <!-- 后缀 -->
        <property name="suffix" value=".jsp" />
    </bean>

</beans>
```


3. 正式通过注解来写Controller：

```java
@Controller
@RequestMapping("/HelloController")
public class HelloController {

    //真实访问地址 : 项目名/HelloController/hello
    @RequestMapping("/hello")
    public String sayHello(Model model){
        //向模型中添加属性msg与值，可以在JSP页面中取出并渲染
        model.addAttribute("msg","hello,SpringMVC");
        //web-inf/jsp/hello.jsp
        return "hello";
    }
}
```

第一个`@RequestMapping`是在类上面写了，就是说如果请求这个sayHello接口就需要将类的路径拼接上，也就是`/HelloController/hello`

<u>我们还可以在类上面加上一个`@RestController`注解，这样使用`return ...`返回的就不是一个视图了，而是简单的json数据(前后端分离！)</u>

```java
@Controller
@RestController
public class HelloController {

    @RequestMapping("/hello")
    public String hello (Model model) {
        // 封装数据
        model.addAttribute("msg", "Hello, SpringMVCAnnotation!");

        return "hello";  // 会被视图解析器处理
    }
}
```

