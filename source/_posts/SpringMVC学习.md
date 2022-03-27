---
title: SpringMVC学习
date: 2021-12-03 22:59:16
updated: 2021-12-07 20:19:00
author: Rogers Kelamen
index_img: https://s2.loli.net/2022/03/26/eTz3qIAxQtnpoJY.png
banner_img: https://s2.loli.net/2022/03/26/dKJNZaixAnyFUDp.png
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
<bean name="/hello" class="com.rokelamen.controller.HelloController"/>
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

![](https://s2.loli.net/2022/03/27/IAb9Gr58jURncLk.png)

看一下tomcat下面有没有这个lib，如果没有的话就需要手动来来创建文件夹导入所有的依赖

### 为什么要改成这样的javaWeb框架(SpringMVC)

我们就完全摆脱了写servlet，并且编写配置文件的冗余，只需要来指明一下是哪个文件来暴露给网络(<u>直接写Controller！</u>)

![](https://s2.loli.net/2022/03/26/zVOaf7Qd6GDlKkN.png)

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
    <context:component-scan base-package="com.rokelamen.controller"/>


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

## 讲一下Controller

- 控制器负责提供访问应用程序的行为，通常通过接口定义或者注解的方式实现。

- 控制器负责解析用户的请求并将其转化为一个模型Model

- 在SpringMVC中，一个Controller可以包含多个方法

- 在SpringMVC中，对于Controller的配置方法有很多


```
@Component -> 组件
@Service   -> service
@Controller -> controller
@Repository -> dao
```

## `@RequestMapping`

> `@RequestMapping`注解就是用于映射url到控制器类或者一个特定到处理程序方法。可用于类或者方法上，用于类就是类中所有的请求方法都是以该地址作为父路径

## `RESTful`风格

对比一下：

```
原始的url api:
localhost:8080/method?add=1&name=sdf

RESTful风格：
localhost:8080/method/add/1/2/3
```

1. 我们可以先定义一个RestFul风格的Controller，然后使用`@PathVariable`注解让方法中的参数绑定到一个uri模版变量中

```java
@Controller
public class Test {

    @RequestMapping("/add/{a}/{b}")
    public String test (@PathVariable int a, @PathVariable int b, Model model) {
        int res = a + b;
        model.addAttribute("msg", "结果为" + res);

        return "test";
    }
}
```

这样我们就可以使用`localhost:8080/add/1/2`来传递参数，相比较原来的方法`localhost:8080/add?a=1&b=2`的方式显然舒畅很多

*然后我们通过RequestMapping中的参数指定请求方式:*

```java
@RequestMapping(value = "/add/{a}/{b}", method = RequestMethod.POST)
```


## 解决乱码

> 当前端传一个参数过来，到后端来到时候已经就是乱码了，很显然我们需要写一个过滤器来解决这个问题

```java
public class EncodingFilter implements Filter {
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {

    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        servletRequest.setCharacterEncoding("utf-8");
        servletResponse.setCharacterEncoding("utf-8");

        filterChain.doFilter(servletRequest, servletResponse);
    }

    @Override
    public void destroy() {

    }
}
```

## 前端提交一个对象

> 大多数情况下，前端是会使用ajax来传过来一个json对象(我们离前后端分离又近了一步)

*首先我们可以直接在参数里面要求传入一个对象(User)，匹配User对象中的所有字段名，只有所有的名字都匹配成功才行*

json的本质：<u>就是因为我们想要传递的参数过多，所以json就可以满足一次性传递多个参数的问题</u>

### 后端处理json

> 首先就是前后端分离时代：后端提供数据接口，前端渲染数据，展示页面

```java
@Controller
public class ControllerJson {

    @ResponseBody   // 不会走视图解析器了
    @RequestMapping("/j1")
    public String json1() {
        // 创建一个对象
        User user = new User(1, "Rogers", "20");

        return user.toString();
    }
}
```

**目前用到比较多的后端对象转化为json数据的就是`Jackson`**

```java
@Controller
public class ControllerJson {

    @ResponseBody   // 不会走视图解析器了，而是直接返回一个字符串
    @RequestMapping(value = "/j1", produces = "application/json;charset=utf-8")
    public String json1() throws JsonProcessingException {
        // 引入jackson的ObjectMapper
        ObjectMapper mapper = new ObjectMapper();

        // 创建一个对象
        User user = new User(1, "Roger", "20");

        String json = mapper.writeValueAsString(user);
        return json;
    }
}
```

<u>需要注意的是SpringMVC提供了一个专门解决乱码问题的方法：</u>

> 乱码统一解决

上一种方法比较麻烦，如果项目中有许多请求则每一个都要添加，可以通过Spring配置统一指定，这样就不用每次都去处理了！

我们可以在springmvc的配置文件上添加一段消息StringHttpMessageConverter转换配置！


```xml
<mvc:annotation-driven>
   <mvc:message-converters register-defaults="true">
       <bean class="org.springframework.http.converter.StringHttpMessageConverter">
           <constructor-arg value="UTF-8"/>
       </bean>
       <bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">
           <property name="objectMapper">
               <bean class="org.springframework.http.converter.json.Jackson2ObjectMapperFactoryBean">
                   <property name="failOnEmptyBeans" value="false"/>
               </bean>
           </property>
       </bean>
   </mvc:message-converters>
</mvc:annotation-driven>
```

---

> 如果我们需要返回多个对象呢？

```java
@ResponseBody   // 不会走视图解析器了，而是直接返回一个字符串
@RequestMapping(value = "/j2", produces = "application/json;charset=utf-8")
public String json2() throws JsonProcessingException {
    // 引入jackson的ObjectMapper
    ObjectMapper mapper = new ObjectMapper();

    List<User> userList = new ArrayList<>();

    // 创建一个对象
    User user1 = new User(1, "Roger", "20");
    User user2 = new User(1, "Roger", "20");
    User user3 = new User(1, "Roger", "20");
    User user4 = new User(1, "Roger", "20");

    userList.add(user1);
    userList.add(user2);
    userList.add(user3);
    userList.add(user4);

    return mapper.writeValueAsString(userList);;
}
```

---

> 修改数据格式为时间戳

```java
@ResponseBody   // 不会走视图解析器了，而是直接返回一个字符串
@RequestMapping(value = "/j3", produces = "application/json;charset=utf-8")
public String json3() throws JsonProcessingException {
    // 引入jackson的ObjectMapper
    ObjectMapper mapper = new ObjectMapper();

    // 不使用时间戳方式
    mapper.configure(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS, false);

    SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");

    mapper.setDateFormat(sdf);

    // 创建一个对象
    Date date = new Date();

    return mapper.writeValueAsString(date);
}
```

直接封装成工具类:

```java
public class JsonUtils {

    public static String getJson(Object object) throws JsonProcessingException {
        return getJson(object, "yyyy-MM-dd HH:mm:ss");
    }

    public static String getJson(Object object, String dataFormat) throws JsonProcessingException {
        ObjectMapper mapper = new ObjectMapper();

        // 不使用时间戳方式
        mapper.configure(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS, false);

        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");

        mapper.setDateFormat(sdf);

        return mapper.writeValueAsString(object);
    }
}
```

## 使用lombok工具

> 这个工具其实就是来写实体类偷懒用的

1. 添加依赖(`pom.xml`中)：

```xml
<dependencies>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <version>1.18.22</version>
    </dependency>
</dependencies>
```

2. 在实体类中使用(比如pojo中的User类):


```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class User {
    private String name;
    private int age;
    private String gender;
}
```

- `@Data`

  添加所有的get，set方法

- `@AllArgsConstructor`

  创建带有所有类成员的构造器

- `@NoArgsConstructor`

  创建无参构造器


## 拦截器(Interceptor)

1. 在config包中增加拦截器

```java
public class MyInterceptor implements HandlerInterceptor {

    // return true; 执行下一个拦截器，放行
    // return false; 不执行下一个拦截器
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("======处理前====");
        return true;
    }

    // 拦截日志
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("=====处理后====");
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("========清理======");
    }
}
```

2. 在配置文件中注册并配置拦截器

```xml
<!--设置拦截器-->
<mvc:interceptors>
    <mvc:interceptor>
        <mvc:mapping path="/**"/>
        <bean class="com.rokelamen.config.MyInterceptor"/>
    </mvc:interceptor>
</mvc:interceptors>
```

## 解决跨域问题

### 直接加`@CrossOrigin`

对的，你就直接在接口上写一个`@CrossOrigin`注解就行了：

![](https://s2.loli.net/2022/03/26/hEGQ3S154PYzOFl.png)

*或者你直接在控制器上面加也行：*

![](https://s2.loli.net/2022/03/26/2cPZVRApDgeLlNW.png)

如果写详细一点就是这样的：

```java
@CrossOrigin(origins = "*")
```

### 写到配置中去


> 显然我们要是在每一个控制器中都这么配一下的话，绝对会很麻烦的，我们建立一个配置类来管理所有的跨域

```java
@Configuration
public class CorsConfig implements WebMvcConfigurer {
    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/**")
                .allowedOrigins("*")
                .allowedHeaders("*")
                .allowedMethods("*")
                .maxAge(3600);
    }
}
```
