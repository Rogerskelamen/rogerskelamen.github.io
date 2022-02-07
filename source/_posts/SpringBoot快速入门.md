---
title: SpringBoot快速入门
date: 2022-01-27 19:15:16
updated: 2022-02-07 20:09:00
index_img: https://gitee.com/rogerskelamen/mdpic/raw/master/img/20220127192112.png
banner_img: https://gitee.com/rogerskelamen/mdpic/raw/master/img/20220127192255.png
tags:
- java
categories:
- 后端
---


# 第一个SpringBoot程序

到底多简单：

- jdk8

- maven3

- springboot

- IDEA

> 官方：提供了一个快速生成的网站！IDEA集成了这个网站

- 可以在官网直接下载后，导入idea开发[官网](https://start.spring.io/)

- 使用IDEA快速开发

# 原理初探

## 启动器(starter)

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-*</artifactId>
</dependency>
```

就是一堆特定jar包的集合，这些jar包配合使用满足了部分的开发场景

**我们需要什么功能，添加对应的启动器(starter)就好了**

## 注解

```text
@SpringBootConfiguration: Springboot的配置
    @Configuration: Spring配置类
    @Component: 说明这也是一个Spring组件

@EnableAutoConfiguration: 自动配置
    @AutoConfigurationPackeage: 自动配置包
    @Import(AutoConfigurationPackages.Register.class) 
```

# 直接开始吧（blog前后端分离项目)

父工程`pom.xml`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.rokelamen</groupId>
    <artifactId>blog-parent</artifactId>
    <version>1.0-SNAPSHOT</version>
    <modules>
        <module>blog-api</module>
    </modules>
    <packaging>pom</packaging>  <!--表明是父工程-->

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.6.3</version>
        <relativePath/>
    </parent>

    <properties>
        <!--工程构建时的编码-->
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <!--日志输出的编码-->
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <java.version>1.8</java.version>
    </properties>

    <dependencyManagement>
        <dependencies>

            <dependency>
                <groupId>com.alibaba</groupId>
                <artifactId>fastjson</artifactId>
                <version>1.2.79</version>
            </dependency>

            <dependency>
                <groupId>commons-collections</groupId>
                <artifactId>commons-collections</artifactId>
                <version>3.2.2</version>
            </dependency>

            <!-- https://mvnrepository.com/artifact/com.baomidou/mybatis-plus-boot-starter -->
            <dependency>
                <groupId>com.baomidou</groupId>
                <artifactId>mybatis-plus-boot-starter</artifactId>
                <version>3.5.0</version>
            </dependency>
            <dependency>
                <groupId>org.projectlombok</groupId>
                <artifactId>lombok</artifactId>
                <version>1.18.22</version>
            </dependency>

            <!--时间处理类-->
            <!-- https://mvnrepository.com/artifact/joda-time/joda-time -->
            <dependency>
                <groupId>joda-time</groupId>
                <artifactId>joda-time</artifactId>
                <version>2.10.13</version>
            </dependency>

        </dependencies>
    </dependencyManagement>

    <!--打包部署的插件-->
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <version>2.6.3</version>
            </plugin>
        </plugins>
    </build>

</project>
```

子项目的`pom.xml`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>blog-parent</artifactId>
        <groupId>com.rokelamen</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>blog-api</artifactId>

    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
    </properties>

    <dependencies>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
            <!--排除默认使用的logback-->
            <exclusions>
                <exclusion>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-starter-logging</artifactId>
                </exclusion>
            </exclusions>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-log4j2</artifactId>
            <version>2.6.3</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-aop</artifactId>
            <version>2.6.3</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-mail</artifactId>
            <version>2.6.3</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <version>2.6.3</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <version>2.6.3</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
            <version>2.6.3</version>
        </dependency>

        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.2.79</version>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.27</version>
        </dependency>

        <!--配置文件提示-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
            <version>2.6.3</version>
        </dependency>

        <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-lang3</artifactId>
            <version>3.12.0</version>
        </dependency>

        <dependency>
            <groupId>commons-collections</groupId>
            <artifactId>commons-collections</artifactId>
            <version>3.2.2</version>
        </dependency>

        <!-- https://mvnrepository.com/artifact/com.baomidou/mybatis-plus-boot-starter -->
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>3.5.0</version>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.22</version>
        </dependency>

        <!--时间处理类-->
        <!-- https://mvnrepository.com/artifact/joda-time/joda-time -->
        <dependency>
            <groupId>joda-time</groupId>
            <artifactId>joda-time</artifactId>
            <version>2.10.13</version>
        </dependency>

    </dependencies>

</project>
```


## 数据库连接和mybatis-plus配置

```properties
server.port=8888
spring.application.name=rok_blog

# 数据库的配置
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://***:3306/blog?characterEncoding=utf-8&useSSL=false&serverTimezone=GMT
spring.datasource.username=root
spring.datasource.password=***

# mybatis-plus
mybatis-plus.configuration.log-impl=org.apache.ibatis.logging.stdout.StdOutImpl
# 配置数据表前缀
mybatis-plus.global-config.db-config.table-prefix=ms_
```

## 创建启动类

```java
package com.rokelamen.blog;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class BlogApp {
    public static void main(String[] args) {
        SpringApplication.run(BlogApp.class, args);
    }
}
```


## 创建mybatis-plus的全局配置文件类

```java
package com.rokelamen.blog.config;

import com.baomidou.mybatisplus.extension.plugins.MybatisPlusInterceptor;
import com.baomidou.mybatisplus.extension.plugins.inner.PaginationInnerInterceptor;
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
@MapperScan("com.rokelamen.blog.mapper") // 这里可能要改
public class MybatisPlusConfig {
    // 分页插件
    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor() {
        MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
        interceptor.addInnerInterceptor(new PaginationInnerInterceptor());
        return interceptor;
    }
}
```

## 配置跨域请求

`WebMVCConfig.java`:

```java
package com.rokelamen.blog.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.CorsRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class WebMVCConfig implements WebMvcConfigurer {

    @Override
    public void addCorsMappings(CorsRegistry registry) {
        // 跨域配置
        registry.addMapping("/**").allowedOrigins("http://localhost:8080");
    }
}
```


## 开始写pojo，mapper，service，controller

`Article.java`:

```java
package com.rokelamen.blog.pojo;

import lombok.Data;

@Data
public class Article {
    public static final int Article_TOP = 1;
    public static final int Article_Common = 0;

    private Long id;

    private Integer commentCounts;

    /**
     * 创建时间
     */
    private Long createDate;

    private String summary;

    private String title;

    private Integer viewCounts;

    /**
     * 置顶
     */
    private Integer weight = Article_Common;

    private Long authorId;

    private Long bodyId;

    private Integer categoryId;

}
```

`ArticleMapper.java`:

```java
package com.rokelamen.blog.mapper;

import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import com.rokelamen.blog.pojo.Article;
import org.apache.ibatis.annotations.Mapper;

@Mapper
public interface ArticleMapper extends BaseMapper<Article> {
}
```

**注意，这里我们直接用了mybatisplus的BaseMapper类来简化sql语句**


`ArticleService.java`:

```java
package com.rokelamen.blog.service;

import com.rokelamen.blog.vo.Result;
import com.rokelamen.blog.vo.params.PageParams;

public interface ArticleService {
    /**
     * 分页查询文章列表
     * @param pageParams 分页参数
     * @return 文章数据的Vo形式
     */
    Result listArticle(PageParams pageParams);
}
```

*写好一个接口，那么我们当然要实现它*

`ArticleServiceImpl.java`:

```java
package com.rokelamen.blog.service.impl;

import com.baomidou.mybatisplus.core.conditions.query.LambdaQueryWrapper;
import com.baomidou.mybatisplus.extension.plugins.pagination.Page;
import com.rokelamen.blog.mapper.ArticleMapper;
import com.rokelamen.blog.pojo.Article;
import com.rokelamen.blog.service.ArticleService;
import com.rokelamen.blog.vo.ArticleVo;
import com.rokelamen.blog.vo.Result;
import com.rokelamen.blog.vo.params.PageParams;
import org.joda.time.DateTime;
import org.springframework.beans.BeanUtils;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.ArrayList;
import java.util.List;

@Service
public class ArticleServiceImpl implements ArticleService {
    @Autowired
    private ArticleMapper articleMapper;

    @Override
    public Result listArticle(PageParams pageParams) {
        /**
         * 1. 分页查询查询artilce数据表
         */
        Page<Article> page = new Page<>(pageParams.getPage(), pageParams.getPageSize());
        LambdaQueryWrapper<Article> queryWrapper = new LambdaQueryWrapper<>();
        // 是否置顶进行排序
        // order by create_date desc
        queryWrapper.orderByDesc(Article::getWeight, Article::getCreateDate);
        Page<Article> articlePage = articleMapper.selectPage(page, queryWrapper);
        List<Article> records = articlePage.getRecords();
        // 能直接返回吗？当然不能,需要返回一个VO对象
        List<ArticleVo> articleVoList = copyList(records);
        return Result.success(articleVoList);
    }

    // 将集合类型的article转化为集合类型的articleVo
    private List<ArticleVo> copyList(List<Article> records) {
        List<ArticleVo> articleVoList = new ArrayList<>();
        for (Article article :
                records) {
            articleVoList.add(copy(article));
        }
        return articleVoList;
    }

    private ArticleVo copy(Article article) {
        ArticleVo articleVo = new ArticleVo();
        BeanUtils.copyProperties(article, articleVo);

        articleVo.setCreateDate(new DateTime(article.getCreateDate()).toString("yyyy-MM-dd HH:mm"));
        return articleVo;
    }
}
```

<u>在这里需要注意的就是我们需要将pojo再解耦一层，将pojo转化为VO(View Object)这个好处就是前端需要什么就后端就传入什么样的VO(灵活好更改，pojo层不用改动)</u>


`ArticleController.java`:

```java
package com.rokelamen.blog.controller;

import com.rokelamen.blog.service.ArticleService;
import com.rokelamen.blog.vo.Result;
import com.rokelamen.blog.vo.params.PageParams;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

// 使用json格式进行交互
@RestController
@RequestMapping("/articles")
public class ArticleController {
    @Autowired
    private ArticleService articleService;

    /**
     * 首页文章列表
     * @param pageParams
     * @return Result对象来判断是否传入成功
     */
    @PostMapping
    public Result listArticle(@RequestBody PageParams pageParams) {
        return articleService.listArticle(pageParams);
    }
}
```


## VO类和传递参数实体类

`ArticleVo.java`:

```java
package com.rokelamen.blog.vo;

import lombok.Data;

import java.util.List;

@Data
public class ArticleVo {
    private Long id;

    private String title;

    private String summary;

    private int commentCounts;

    private int viewCounts;

    private int weight;

    /**
     * 创建时间
     */
    private String createDate;

    private String author;

    // private ArticleBodyVo body;

    private List<TagVo> tags;

    // private List<CategoryVo> categories;
}
```

**这个VO类就是我上面说的，用来专门传给前端的格式数据类**


`PageParams.java`:

```java
package com.rokelamen.blog.vo.params;

import lombok.Data;

@Data
public class PageParams {
    private int page = 1;
    private int pageSize = 10;
}
```

<u>这种类是为了管理前端传递参数(因为前端如果传递RequestBody一定是对象或者json字符串)而创建的类，一般放在`vo/params`中</u>

## Result类（格式化接口数据输出）

`Result.java`:

```java
package com.rokelamen.blog.vo;

import lombok.AllArgsConstructor;
import lombok.Data;

@Data
@AllArgsConstructor
public class Result {
    private boolean success;

    private int code;

    private String msg;

    private Object data;

    public static Result success(Object data) {
        return new Result(true, 200, "success", data);
    }

    public static Result fail(int code, String msg) {
        return new Result(false, code, msg, null);
    }
}
```

*一般将这个类和VO对象一起，放在`vo`包中*

## 前端接上接口

```js
async testForArticle () {
  let body = {
    page: 1,
    pageSize: 2
  }
  // var config = {
  //   headers: {
  //     'Content-Type': 'application/json;charset=utf-8'
  //   }
  // }
  const { data: res } = await this.$http.post(`articles`, body)
  console.log(res)
}
```

![](https://gitee.com/rogerskelamen/mdpic/raw/master/img/20220126143504.png)


# 统一异常处理

> 不管是controller层，还是service，dao层都有可能报异常，如果是意料之外的异常，我们就需要统一的管理和记录，这样不管是维护调试方面还是在前端用户提示方面，都会更加规范和友好

在`com.rokelamen.blog.handler.AllExceptionHandler.java`中：

```java
package com.rokelamen.blog.handler;

import com.rokelamen.blog.vo.Result;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.ResponseBody;

// 对加了@Controller注解的方法进行拦处理 AOP的实现
@ControllerAdvice
public class AllExceptionHandler {

    // 进行异常处理，处理Exception.class的异常
    @ExceptionHandler(Exception.class)
    @ResponseBody
    public Result doException(Exception ex) {
        ex.printStackTrace();
        return Result.fail(999, "系统异常");
    }
}
```

> 因为这里是对所有的Exception异常处理(包括了Controller层的异常)，所以我们一旦有接口数据出错，我们就可以将我们想要的信息传递给前端


# 登录问题

## JWT

> 登录使用JWT技术，jwt可以生成一个加密的token，作为用户登录的令牌，当用户登录成功后，发放给客户端。请求需要登录的资源或者接口的时候，将token携带，后端验证token是否合法。

> jwt有三部分组成：A.B.C

- A: Header, {"type": "JWT", "alg": "HS256"}固定

- B: playload, 存放信息，比如用户id，过期时间等，可以被解密，不能存放敏感信息(如密码)

- C: 签证，A和B加上密钥加密而成，只要密钥不丢失，可以认为是安全的

<u>而jwt验证，主要就是验证C部分是否合法</u>

## 使用

> 导入依赖

```xml
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt</artifactId>
    <version>0.9.1</version>
</dependency>
```

编写工具类`utils.JWTUtils.java`:

```java
package com.rokelamen.blog.utils;

import io.jsonwebtoken.Jwt;
import io.jsonwebtoken.JwtBuilder;
import io.jsonwebtoken.Jwts;
import io.jsonwebtoken.SignatureAlgorithm;

import java.util.Date;
import java.util.HashMap;
import java.util.Map;

public class JWTUtils {

    // 定义密钥
    private static final String jwtToken = "123456Rogers%%Code";

    public static String createToken(Long userId) {
        Map<String, Object> claims = new HashMap<>();
        claims.put("userId", userId);
        JwtBuilder jwtBuilder = Jwts.builder()
                .signWith(SignatureAlgorithm.HS256, jwtToken)  // 签发算法，密钥为jwtToken
                .setClaims(claims)  // body 数据，要唯一，自行设置
                .setIssuedAt(new Date()) // 设置签发时间
                .setExpiration(new Date(System.currentTimeMillis() + 24 * 60 * 60 * 1000)); // 设置一天的有效时间
        String token = jwtBuilder.compact();
        return token;
    }

    public static Map<String, Object> checkToken(String token) {
        try {
            Jwt parse = Jwts.parser().setSigningKey(jwtToken).parse(token);
            return (Map<String, Object>) parse.getBody();
        }catch (Exception e) {
            e.printStackTrace();
        }
        return null;
    }

    public static void main(String[] args) {
        String token = JWTUtils.createToken(100L);
        System.out.println(token);
        Map<String, Object> map = JWTUtils.checkToken(token);
        System.out.println(map.get("userId"));
    }
}
```

编写Login接口和实现`LoginServiceImpl`:

```java
package com.rokelamen.blog.service.impl;

import com.rokelamen.blog.pojo.SysUser;
import com.rokelamen.blog.service.LoginService;
import com.rokelamen.blog.service.SysUserService;
import com.rokelamen.blog.utils.JWTUtils;
import com.rokelamen.blog.vo.ErrorCode;
import com.rokelamen.blog.vo.Result;
import com.rokelamen.blog.vo.params.LoginParams;
import org.apache.commons.lang3.StringUtils;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class LoginServiceImpl implements LoginService {
    @Autowired
    private SysUserService sysUserService;

    @Override
    public Result login(LoginParams loginParams) {
        /**
         * 1. 检查参数是否合法
         * 2. 根据用户名和密码去user表中查询 是否存在
         * 3. 如果不存在 登录失败
         * 4. 如果存在，使用jwt生成 token 返回给前端
         * 5. token放入redis中，redis token: user信息 设置过期时间
         * （登录认证的时候 先去认证token字符串是否合法，去redis认证是否存在）
         */
        String account = loginParams.getAccount();
        String password = loginParams.getPassword();
        if (StringUtils.isBlank(account) || StringUtils.isBlank(password)) {
            return Result.fail(ErrorCode.PARAMS_ERROR.getCode(), ErrorCode.PARAMS_ERROR.getMsg());
        }
        SysUser user = sysUserService.findUser(account, password);
        if (user == null) {
            return Result.fail(ErrorCode.ACCOUNT_ERROR.getCode(), ErrorCode.ACCOUNT_ERROR.getMsg());
        }

        String token = JWTUtils.createToken(user.getId());

        return Result.success(token);
    }
}
```

### 配合redis使用

导入依赖：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
    <version>2.6.3</version>
</dependency>
```

增加redis的配置`application.properties`:

```properties
spring.redis.host=localhost
spring.redis.port=6379
```

### 登录服务实现

`LoginServiceImpl.java`:

```java
package com.rokelamen.blog.service.impl;

import com.alibaba.fastjson.JSON;
import com.rokelamen.blog.pojo.SysUser;
import com.rokelamen.blog.service.LoginService;
import com.rokelamen.blog.service.SysUserService;
import com.rokelamen.blog.utils.JWTUtils;
import com.rokelamen.blog.vo.ErrorCode;
import com.rokelamen.blog.vo.Result;
import com.rokelamen.blog.vo.params.LoginParams;
import org.apache.commons.codec.digest.DigestUtils;
import org.apache.commons.lang3.StringUtils;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.stereotype.Service;

import java.util.concurrent.TimeUnit;

@Service
public class LoginServiceImpl implements LoginService {
    @Autowired
    private SysUserService sysUserService;

    @Autowired
    private RedisTemplate<String, String> redisTemplate;

    private static final String slat = "123456Rogers%%Code";

    @Override
    public Result login(LoginParams loginParams) {
        /**
         * 1. 检查参数是否合法
         * 2. 根据用户名和密码去user表中查询 是否存在
         * 3. 如果不存在 登录失败
         * 4. 如果存在，使用jwt生成 token 返回给前端
         * 5. token放入redis中，redis token: user信息 设置过期时间
         * （登录认证的时候 先去认证token字符串是否合法，去redis认证是否存在）
         */
        String account = loginParams.getAccount();
        String password = loginParams.getPassword();
        if (StringUtils.isBlank(account) || StringUtils.isBlank(password)) {
            return Result.fail(ErrorCode.PARAMS_ERROR.getCode(), ErrorCode.PARAMS_ERROR.getMsg());
        }

        // 加密password，因为数据库中的密码都是加密存放的
        password = DigestUtils.md5Hex(password + slat);
        SysUser user = sysUserService.findUser(account, password);

        if (user == null) {
            return Result.fail(ErrorCode.ACCOUNT_ERROR.getCode(), ErrorCode.ACCOUNT_ERROR.getMsg());
        }

        String token = JWTUtils.createToken(user.getId());

        // 在redis缓存中存储token信息
        redisTemplate.opsForValue().set("TOKEN_" + token, JSON.toJSONString(user), 1, TimeUnit.DAYS);

        return Result.success(token);
    }
}
```

*这里需要一个codec的包:*

```xml
<dependency>
    <groupId>commons-codec</groupId>
    <artifactId>commons-codec</artifactId>
</dependency>
```

## 使用token对登录状态进行验证

> 以获取当前用户信息为例

`LoginServiceImpl.java`:

```java
@Override
public SysUser checkToken(String token) {
    if (StringUtils.isBlank(token)) {
        return null;
    }

    // 解析token, 如果解析错误直接返回
    Map<String, Object> stringObjectMap = JWTUtils.checkToken(token);
    if (stringObjectMap == null) {
        return null;
    }

    // 验证redis中是否有这个token(是否过期)
    String userJson = redisTemplate.opsForValue().get("TOKEN_" + token);
    if (StringUtils.isBlank(userJson)) {
        return null;
    }

    SysUser sysUser = JSON.parseObject(userJson, SysUser.class);
    return sysUser;
}
```

`SysUserServiceImpl.java`:

```java
@Override
public Result findUserByToken(String token) {
    /**
     * 1. token合法性校验
     *  是否为空，解析是否成功 redis是否存在
     * 2. 如果校验失败 返回错误
     * 3. 如果成功，返回对应的结果LoginUserVo
     */
    SysUser sysUser = loginService.checkToken(token);
    if (sysUser == null) {
        return Result.fail(ErrorCode.TOKEN_ERROR.getCode(), ErrorCode.TOKEN_ERROR.getMsg());
    }

    LoginUserVo loginUserVo = new LoginUserVo();
    loginUserVo.setId(sysUser.getId());
    loginUserVo.setAccount(sysUser.getAccount());
    loginUserVo.setAvatar(sysUser.getAvatar());
    loginUserVo.setNickname(sysUser.getNickname());

    return Result.success(loginUserVo);
}
```

> 这时候会出现bean的循环引用，这时候用一条语句就可以让spring帮我们自动忽略

```properties
# 配置可以有bean的循环引用（但是应该避免）
spring.main.allow-circular-references = true
```

#### 登出

> 登出就直接删除redis缓存就好啦

```java
@Override
public Result logout(String token) {
    redisTemplate.delete("TOKEN_" + token);
    return Result.success(null);
}
```

## 统一管理需要登录token的接口(使用拦截器)

`handler`包下`LoginInterceptor.java`:

```java
package com.rokelamen.blog.handler;

import com.alibaba.fastjson.JSON;
import com.rokelamen.blog.pojo.SysUser;
import com.rokelamen.blog.service.LoginService;
import com.rokelamen.blog.service.TokenService;
import com.rokelamen.blog.vo.ErrorCode;
import com.rokelamen.blog.vo.Result;
import lombok.extern.slf4j.Slf4j;
import org.apache.commons.lang3.StringUtils;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;
import org.springframework.web.method.HandlerMethod;
import org.springframework.web.servlet.HandlerInterceptor;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@Component
@Slf4j
public class LoginInterceptor implements HandlerInterceptor {
    @Autowired
    private TokenService tokenService;

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        // 在执行controller方法之前进行执行
        /**
         * 1. 需要判断 请求的接口路径 是否为HandlerMethod (controller方法)
         * 2. 判断token是否为空，如果为空 未登录
         * 3. 如果token不为空，登录验证loginService checkToken
         * 4. 如果认证成功 放行即可
         */
        if (!(handler instanceof HandlerMethod)) {
            // handler 可能是RequestResourceHandler springboot 程序
            return true;
        }
        String token = request.getHeader("Authorization");

        log.info("===============request start==================");
        String requestURI = request.getRequestURI();
        log.info("request uri: {}", requestURI);
        log.info("request method: {}", request.getMethod());
        log.info("token: {}", token);
        log.info("===============request end==================");

        if (StringUtils.isBlank(token)) {
            Result result = Result.fail(ErrorCode.NO_LOGIN.getCode(), ErrorCode.NO_LOGIN.getMsg());
            response.setContentType("application/json;charset=utf-8");
            response.getWriter().println(JSON.toJSONString(result));
            return false;
        }
        SysUser sysUser = tokenService.checkToken(token);
        if (sysUser == null) {
            Result result = Result.fail(ErrorCode.NO_LOGIN.getCode(), ErrorCode.NO_LOGIN.getMsg());
            response.setContentType("application/json;charset=utf-8");
            response.getWriter().println(JSON.toJSONString(result));
            return false;
        }
        // 登录验证成功，放行
        return true;
    }
}
```

**不要忘了还要在springmvc中注册这个拦截器:**

`WebMVCConfig.java`中：

```java
@Autowired
private LoginInterceptor loginInterceptor;

@Override
public void addCorsMappings(CorsRegistry registry) {
    // 跨域配置
    registry.addMapping("/**").allowedOrigins("http://localhost:8080");
}

@Override
public void addInterceptors(InterceptorRegistry registry) {
    //
    registry.addInterceptor(loginInterceptor)
            .addPathPatterns("/**")
            .excludePathPatterns("/login")
            .excludePathPatterns("/register");
}
```

## 用户信息本地保存

> 这里说的本地保存是保存在后端服务器中，不是使用类似前端vuex的状态管理器

> 使用ThreadLocal（利用多线程）

写一个`UserThreadLocal.java`类：

```java
package com.rokelamen.blog.utils;

import com.rokelamen.blog.pojo.SysUser;

public class UserThreadLocal {

    private UserThreadLocal() {}

    private static final ThreadLocal<SysUser> LOCAL = new ThreadLocal<>();

    public static void put(SysUser sysUser) {
        LOCAL.set(sysUser);
    }

    public static SysUser get() {
        return LOCAL.get();
    }

    public static void remove() {
        LOCAL.remove();
    }
}
```

然后我们在`LoginInterceptor.java`的`preHandle`方法中加上：

```java
// 登录验证成功，放行
        // 希望在controller中 直接获取用户的信息 怎么获取?
UserThreadLocal.put(sysUser);
return true;
```

<u>这样我们就拿到了这个user信息并且将其存入到了UserThreadLocal中</u>

用：


```java
SysUser sysUser = UserThreadLocal.get();
```

**但是要记得拦截器处理完之后还要进行删除：**

```java
@Override
public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
    // 如果不删除ThreadLocal中用完的信息 会有内存泄漏的风险
    UserThreadLocal.remove();
}
```

# 问题：ThreadLocal为什么要用

> 如果不用这种方式，会有内存泄漏的风险

# 线程池的使用(文章查看时阅读数加一)

1. 配置线程池

在`config`包中`ThreadPoolConfig.java`:

```java
package com.rokelamen.blog.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.scheduling.annotation.EnableAsync;
import org.springframework.scheduling.concurrent.ThreadPoolTaskExecutor;

import java.util.concurrent.Executor;

@Configuration
@EnableAsync    // 开启多线程
public class ThreadPoolConfig {

    @Bean("taskExecutor")
    public Executor asyncServiceExecutor() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();

        // 设置核心线程数
        executor.setCorePoolSize(5);

        // 设置最大线程数
        executor.setMaxPoolSize(20);

        // 配置队列大小
        executor.setQueueCapacity(Integer.MAX_VALUE);

        // 设置线程活跃时间(秒）
        executor.setKeepAliveSeconds(60);

        // 设置默认线程名称
        executor.setThreadNamePrefix("Rokelamen博客项目");

        // 等待所有任务结束后再关闭线程池
        executor.setWaitForTasksToCompleteOnShutdown(true);

        // 执行初始化
        executor.initialize();

        return executor;
    }
}
```

2. 将所执行的异步服务丢入到线程池中

> 比如ThreadService的`updateArticleViewCount`方法：

```java
package com.rokelamen.blog.service;

import com.rokelamen.blog.mapper.ArticleMapper;
import com.rokelamen.blog.pojo.Article;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.scheduling.annotation.Async;
import org.springframework.stereotype.Component;

@Component
public class ThreadService {

    @Autowired
    private ArticleMapper articleMapper;

    @Async("taskExecutor")
    public void updateArticleViewCount(Article article) {
        try {
            Thread.sleep(5000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

*产生的效果就是：当一个服务调用了这个方法，这个服务可以立即返回响应数据，但是调用的方法会异步的执行(双线程执行)，知道调用方法执行完*


现在我们就用线程池解决文章阅读数的增加

```java
package com.rokelamen.blog.service;

import com.baomidou.mybatisplus.core.conditions.query.LambdaQueryWrapper;
import com.baomidou.mybatisplus.core.conditions.update.LambdaUpdateWrapper;
import com.rokelamen.blog.mapper.ArticleMapper;
import com.rokelamen.blog.pojo.Article;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.scheduling.annotation.Async;
import org.springframework.stereotype.Component;

@Component
public class ThreadService {

    @Autowired
    private ArticleMapper articleMapper;

    @Async("taskExecutor")
    public void updateArticleViewCount(Article article) {

        Integer viewCounts = article.getViewCounts();
        Article articleUpdate = new Article();
        articleUpdate.setViewCounts(viewCounts + 1);
        LambdaUpdateWrapper<Article> updateWrapper = new LambdaUpdateWrapper<>();
        updateWrapper.eq(Article::getId, article.getId());
        updateWrapper.eq(Article::getViewCounts, viewCounts); // 乐观锁，如果操作的时候发现与期望的阅读量不一致，修改失败
        // update article set view_count=100 where view_count=99 and id=1
        articleMapper.update(articleUpdate, updateWrapper);
        try {
            Thread.sleep(5000);
            System.out.println("更新完成了....");
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

# AOP日志管理

> 很多时候我们有这样的需求：要是每次有接口调用的时候我能知道调用的是哪个模块里面的哪个接口，访问的ip地址是多少，调用这个接口花了多少时间就好了。那么我们需要的是统一管理接口日志信息，这种方式是使用切面编程管理，首先我们有一个获取文章的日志注解：

```java
@PostMapping
// 加上此注解，代表要对此接口记录日志
@LogAnnotation(module="文章", operate="获取文章列表")
public Result listArticle(@RequestBody PageParams pageParams) {
    return articleService.listArticle(pageParams);
}
```

1. 创建包`common.aop`并编写注解`LogAnnotation.java`:

```java
package com.rokelamen.blog.common.aop;

import java.lang.annotation.*;

// Type代表可以放在类上面 Method代表可以放在方法上
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface LogAnnotation {

    String module() default "";

    String operate() default "";
}
```

2. 开发切面`LogAspect.java`:

```java
package com.rokelamen.blog.common.aop;

import com.alibaba.fastjson.JSON;
import com.rokelamen.blog.utils.HttpContextUtils;
import com.rokelamen.blog.utils.IpUtils;
import lombok.extern.slf4j.Slf4j;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Pointcut;
import org.aspectj.lang.reflect.MethodSignature;
import org.springframework.stereotype.Component;

import javax.servlet.http.HttpServletRequest;
import java.lang.reflect.Method;

@Component
@Aspect // 切面 定义了通知和切点的关系
@Slf4j
public class LogAspect {

    @Pointcut("@annotation(com.rokelamen.blog.common.aop.LogAnnotation)")
    public void pt() {}

    // 环绕通知
    @Around("pt()")
    public Object log(ProceedingJoinPoint joinPoint) throws Throwable {
        long beginTime = System.currentTimeMillis();
        // 执行操作
        Object result = joinPoint.proceed();
        // 执行时长（毫秒）
        long time = System.currentTimeMillis() - beginTime;
        // 保存日志
        recordLog(joinPoint, time);
        return result;
    }

    private void recordLog(ProceedingJoinPoint joinPoint, long time) {
        MethodSignature signature = (MethodSignature) joinPoint.getSignature();
        Method method = signature.getMethod();
        LogAnnotation annotation = method.getAnnotation(LogAnnotation.class);
        log.info("====================log start=====================");
        log.info("module: {}", annotation.module());
        log.info("operation: {}", annotation.operation());

        // 请求的方法名
        String className = joinPoint.getTarget().getClass().getName();
        String methodName = signature.getName();
        log.info("request method: {}", className + "." + methodName + "()");

        // 请求参数
        Object[] args = joinPoint.getArgs();
        String params = JSON.toJSONString(args[0]);
        log.info("params: {}", params);

        // 获取request 设置IP地址
        HttpServletRequest request = HttpContextUtils.getHttpServletRequest();
        log.info("ip: {}", IpUtils.getIpAddr(request));

        log.info("excute time: {} ms", time);
        log.info("====================log end=====================");
    }
}
```

*当然，还需要两个工具类:*

```java
package com.rokelamen.blog.utils;

import org.springframework.web.context.request.RequestContextHolder;
import org.springframework.web.context.request.ServletRequestAttributes;

import javax.servlet.http.HttpServletRequest;

public class HttpContextUtils {
    public static HttpServletRequest getHttpServletRequest() {
        return ((ServletRequestAttributes) RequestContextHolder.getRequestAttributes()).getRequest();
    }
}
```

```java
package com.rokelamen.blog.utils;

import lombok.extern.slf4j.Slf4j;
import org.apache.commons.lang3.StringUtils;

import javax.servlet.http.HttpServletRequest;

/**
 * 获取Ip
 *
 */
@Slf4j
public class IpUtils {

    /**
     * 获取IP地址
     * <p>
     * 使用Nginx等反向代理软件， 则不能通过request.getRemoteAddr()获取IP地址
     * 如果使用了多级反向代理的话，X-Forwarded-For的值并不止一个，而是一串IP地址，X-Forwarded-For中第一个非unknown的有效IP字符串，则为真实IP地址
     */
    public static String getIpAddr(HttpServletRequest request) {
        String ip = null, unknown = "unknown", seperator = ",";
        int maxLength = 15;
        try {
            ip = request.getHeader("x-forwarded-for");
            if (StringUtils.isEmpty(ip) || unknown.equalsIgnoreCase(ip)) {
                ip = request.getHeader("Proxy-Client-IP");
            }
            if (StringUtils.isEmpty(ip) || ip.length() == 0 || unknown.equalsIgnoreCase(ip)) {
                ip = request.getHeader("WL-Proxy-Client-IP");
            }
            if (StringUtils.isEmpty(ip) || unknown.equalsIgnoreCase(ip)) {
                ip = request.getHeader("HTTP_CLIENT_IP");
            }
            if (StringUtils.isEmpty(ip) || unknown.equalsIgnoreCase(ip)) {
                ip = request.getHeader("HTTP_X_FORWARDED_FOR");
            }
            if (StringUtils.isEmpty(ip) || unknown.equalsIgnoreCase(ip)) {
                ip = request.getRemoteAddr();
            }
        } catch (Exception e) {
            log.error("IpUtils ERROR ", e);
        }

        // 使用代理，则获取第一个IP地址
        if (StringUtils.isEmpty(ip) && ip.length() > maxLength) {
            int idx = ip.indexOf(seperator);
            if (idx > 0) {
                ip = ip.substring(0, idx);
            }
        }

        return ip;
    }

    /**
     * 获取ip地址
     *
     * @return
     */
    public static String getIpAddr() {
        HttpServletRequest request = HttpContextUtils.getHttpServletRequest();
        return getIpAddr(request);
    }
}
```

3. 效果

![](https://gitee.com/rogerskelamen/mdpic/raw/master/img/20220207143120.png)

*突然就好起来了*


# 文件上传（图片上传）

使用七牛云进行文件对象存储：

1. maven依赖

```xml
<dependency>
  <groupId>com.qiniu</groupId>
  <artifactId>qiniu-java-sdk</artifactId>
  <version>[7.7.0, 7.7.99]</version>
</dependency>
```

2. 编写工具类`QiniuUtils.java`:

```java
package com.rokelamen.blog.utils;

import com.alibaba.fastjson.JSON;
import com.qiniu.http.Response;
import com.qiniu.storage.Configuration;
import com.qiniu.storage.Region;
import com.qiniu.storage.UploadManager;
import com.qiniu.storage.model.DefaultPutRet;
import com.qiniu.util.Auth;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;
import org.springframework.web.multipart.MultipartFile;

@Component
public class QiniuUtils {

    public static final String url = "r6xa76ri6.hd-bkt.clouddn.com";

    @Value("${qiniu.accessKey}")
    private String accessKey;
    @Value("${qiniu.accessSecretKey}")
    private String accessSecretKey;

    public boolean upload(MultipartFile file,String fileName){

        //构造一个带指定 Region 对象的配置类
        com.qiniu.storage.Configuration cfg = new Configuration(Region.region0());
        //...其他参数参考类注释
        UploadManager uploadManager = new UploadManager(cfg);
        //...生成上传凭证，然后准备上传
        String bucket = "rok";
        //默认不指定key的情况下，以文件内容的hash值作为文件名
        try {
            byte[] uploadBytes = file.getBytes();
            Auth auth = Auth.create(accessKey, accessSecretKey);
            String upToken = auth.uploadToken(bucket);
            Response response = uploadManager.put(uploadBytes, fileName, upToken);
            //解析上传成功的结果
            DefaultPutRet putRet = JSON.parseObject(response.bodyString(), DefaultPutRet.class);
            return true;
        } catch (Exception ex) {
            ex.printStackTrace();
        }
        return false;
    }

}
```

3. 引入到Controller中：

```java
@RestController
@RequestMapping("upload")
public class UploadController {

    @Autowired
    private QiniuUtils qiniuUtils;

    @PostMapping
    @LogAnnotation(module = "上传图片", operation = "图片上传")
    public Result upload(@RequestParam("image")MultipartFile file) {
        // 原始文件名称 比如 aa.png
        String originalFileName = file.getOriginalFilename();
        // 唯一的文件名称(肯定不能用原始文件名)
        String fileName = UUID.randomUUID().toString() + "." + StringUtils.substringAfterLast(originalFileName, ".");
        // 上传文件 上传到哪呢？ 七牛云

        boolean upload = qiniuUtils.upload(file, fileName);
        if (upload) {
            return Result.success(QiniuUtils.url + fileName);
        }else {
            return Result.fail(20001, "上传失败");
        }
    }
}
```

4. 设置最大的文件上传大小：


```properties
# 上传图片总的最大值
spring.servlet.multipart.max-request-size=20MB
# 单个文件最大值
spring.servlet.multipart.max-file-size=2MB
```

<u>需要注意的是，这个上传服务就不能用AOP日志注解了，因为在上传的过程中，会有复杂的类型转换，其中的JSON转换会提示报错（但是不影响成功上传）</u>

## 前端vue代码怎么写呢？

> 当然是使用ElementUI啦,免去文件类型转换

```vue
<template>
  <el-upload
    action="http://localhost:8888/upload"
    name="image"
    multiple
    :on-success="uploadSuccess"
    :limit="3">
    <el-button size="small" type="primary">点击上传</el-button>
    <div slot="tip" class="el-upload__tip">只能上传jpg/png文件，且不超过500kb</div>
  </el-upload>
</template>

<script>
methods: {
  uploadSuccess(response) {
    console.log(response);
  },
}
</script>
```


# 杂项(记录问题)

## 1. Mybatis-plus无法进行多表查询

> 这时候就需要我们去自己编写查询语句(sql: select)

在`resource/com/rokelamen/blog/mapper`中添加

`TagMapper.xml`(编写联合查询或者子查询):

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.rokelamen.blog.mapper.TagMapper">

    <!-- List<Tag> findTagsByArticleId(Long articleId);-->
    <select id="findTagsByArticleId" parameterType="long" resultType="com.rokelamen.blog.pojo.Tag">
--         select id, avatar, tag_name as tagName
--         from ms_tag
--         where id in
--         (select tag_id from ms_article_tag where article_id=#{articleId})
        select ms_tag.id, avatar, tag_name as tagName
        from ms_tag inner join ms_article_tag mat on ms_tag.id = mat.tag_id and mat.article_id = #{articleId};
    </select>
</mapper>
```

**需要注意的就是这个路径必须和`java`中的路径相对应，这样mybatis-plus才能识别出来**


## 2. 基本类型在实体类中会给默认值

> 比如在实体类中写上private int count;那么每次数据表要是有update操作,这个count就会初始化为0。这个时候我们就要使用Integer来定义这个count

## 3. 出现前端拿到id精度损失

> 这个问题出现的原因是数据库中使用的bigint类型，后端采用Long数据类型，mybatis-plus在映射表结构时会出现很大的数字，这时候传递给前端这么大的数字肯定会出现精度损失


解决:<u>在Vo实体类中的id属性加上一个注解，将传给前端的id转换为字符串</u>

```java
// 防止前端精度损失，把id转为String
@JsonSerialize(using = ToStringSerializer.class)
private Long id;
```
