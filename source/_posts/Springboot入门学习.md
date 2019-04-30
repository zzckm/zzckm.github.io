---
title: Springboot入门学习
date: 2019-04-19 21:27:51
tags: [Java,Spring,Springboot]
categories: Java框架
---


# Springboot入门介绍 
Spring 框架对于很多 Java 开发人员来说都不陌生。自从 2002 年发布以来，Spring 框架已经成为企业应用开发领域非常流行的基础框架。有大量的企业应用基于 Spring 框架来开发。Spring 框架包含几十个不同的子项目，涵盖应用开发的不同方面。如此多的子项目和组件，一方面方便了开发人员的使用，另外一个方面也带来了使用方面的问题。每个子项目都有一定的学习曲线。开发人员需要了解这些子项目和组件的具体细节，才能知道如何把这些子项目整合起来形成一个完整的解决方案。在如何使用这些组件上，并没有相关的最佳实践提供指导。对于新接触 Spring 框架的开发人员来说，并不知道如何更好的使用这些组件。Spring 框架的另外一个常见问题是要快速创建一个可以运行的应用比较麻烦。Spring Boot 是 Spring 框架的一个新的子项目，用于创建 Spring 4.0 项目。它的开发始于 2013 年。2014 年 4 月发布 1.0.0 版本。它可以自动配置 Spring 的各种组件，并不依赖代码生成和 XML 配置文件。Spring Boot 也提供了对于常见场景的推荐组件配置。Spring Boot 可以大大提升使用 Spring 框架时的开发效率
## 简介：
使用Spring boot ，可以轻松的创建独立运行的程序，非常容易构建独立的服务组件，是实现分布式架构、微服务架构利器。
Spring boot简化了第三方包的引用，通过提供的starter，简化了依赖包的配置

## Spring boot的优点
轻松创建独立的Spring应用程序。
内嵌Tomcat、jetty等web容器，不需要部署WAR文件。
提供一系列的“starter” 来简化的Maven配置，不需要添加很多依赖。
开箱即用，尽可能自动配置Spring。



## Springboot依赖说明

|依赖包|解释说明|
|---|---|
|spring-boot-starter|核心 POM，包含自动配置支持、日志库和对 YAML 配置文件的支持。|
|spring-boot-starter-amqp |通过 spring-rabbit 支持 AMQP
|spring-boot-starter-aop|包含 spring-aop 和 AspectJ 来支持面向切面编程（AOP）。
|spring-boot-starter-batch|支持 Spring Batch，包含 HSQLDB。
|spring-boot-starter-data-jpa|包含 spring-data-jpa、spring-orm 和 Hibernate 来支持 JPA。
|spring-boot-starter-data-mongodb|包含 spring-data-mongodb 来支持  。
|spring-boot-starter-data-rest|通过 spring-data-rest-webmvc 支持以 REST 方式暴露 Spring Data 仓库。
|spring-boot-starter-jdbc|支持使用 JDBC 访问数据库
|spring-boot-starter-security|包含 spring-security。
|spring-boot-starter-test|包含常用的测试所需的依赖，如 JUnit、Hamcrest、Mockito 和 spring-test 等。
|spring-boot-starter-velocity|支持使用 Velocity 作为模板引擎。
|spring-boot-starter-web|支持 Web 应用开发，包含 Tomcat 和 spring-mvc。
|spring-boot-starter-websocket|支持使用 Tomcat 开发 WebSocket 应用。
|spring-boot-starter-ws|支持 Spring Web Services
|spring-boot-starter-actuator|添加适用于生产环境的功能，如性能指标和监测等功能。
|spring-boot-starter-remote-shell|添加远程 SSH 支持
|spring-boot-starter-jetty|使用 Jetty 而不是默认的 Tomcat 作为应用服务器。
|spring-boot-starter-log4j|添加 Log4j 的支持
|spring-boot-starter-logging|使用 Spring Boot 默认的日志框架 Logback
|spring-boot-starter-tomcat|使用 Spring Boot 默认的 Tomcat 作为应用服务器。
注:tomcat和jetty用其一就可以了


## Springboot 启动方式

### 第一种
```java
@EnableAutoConfiguration
//路径可以扩大至com,zhiyou.web 相当于web包下的都会被启动
@ComponentScan(basePackages={"com.zhiyou.web.controller",""})
publicq class App{
public static void main(String[] args){
//启动springboot项目
SpringApplication.run(UserController.class,args);
}
}
```
### 第二种方式【常用】
maven项目里的springboot
定义main类
```java
@EnableAutoConfiguration
@ComponenetScan(basePackages="com.zhiyou100.controller")//如果需要加多个的话="{"",""}"
public class App{        
        public static void main(String[]args){
        //启动springboot项目
        SpringApplication.run(App.class,args);
}
```
![](https://i.loli.net/2019/04/19/5cb9d474ca862.png)

## 常用注解
```java
@RestCibtriller   //申明Rest风格的控制器—不仅有Controller功能 把参数写到链接里 而不是？问号后面了
@EnableAutoConfiguration//自动配置Spring配置文件 (在配置完main类之后就可以不用写该注解了)
public class 类名{

@RequestMapping("hello/{name}")  //映射路径
@ResponseBody //把下方return的输出转换成json格式输出到前端(返回Json数据)
 //@PathVariable("name")用户获取映射路径所传参数，格式 参数名称和映射路径的参数名称相同的话@PathVariable()可以不填内容
      public String hello(@PathVariable("name") String name){
            return name+“hello,Spring boot";
     }
}
//写完之后  输出localhost:8080/hello/zhangsan 就会出现 zhangsan hello.Spring boot
```

![](https://i.loli.net/2019/04/19/5cb9d54164039.png)


## 全局捕获异常
@ExceptionHandler 表示拦截异常
@ControllerAdvice 
controller 的一个辅助类，最常用的就是作为全局异常处理的切面类
可以指定扫描范围
约定了几种可行的返回值，如果是直接返回 model 类的话，需要使用

@ResponseBody 进行 json 转换

在com.gyf.web.exception包中定义一个全局异常类
```java    
    @ControllerAdvice//控制器切面
    public class GlobalExceptionHandler {
        @ExceptionHandler(RuntimeException.class)//捕获运行时异常
        @ResponseBody//返回json
        public Map<String,Object> exceptionHander(){//处理异常方法
            Map<String, Object> map = new HashMap<String, Object>();
            map.put("errorCode", "101");
            map.put("errorMsg", "系統错误!");
            return map;
        }
    }
```
在启动spring中，配置扫描包为com.gyf.web
![](https://i.loli.net/2019/04/19/5cb9d759c0260.png)
在某个映射的方法中添加个int i = 10/0的算术异常
![](https://i.loli.net/2019/04/19/5cb9d775b41ec.png)
访问上的个路径结果为 
![](https://i.loli.net/2019/04/19/5cb9d77f960a7.png)

 ## 传参方方式
 ### 后端向前端传值 加判断-列表排序

**建立一个控制器**
``` java
/**
* @RestController 用于写ApI, 给移动客户端提供数据，一般返回json数据
* @Controller一般用于写后台）（）
* 本demo 介绍如何后端向前端传值
*/
@Controller
@RequestMapping("stu")
public class StudentController {
@RequestMapping("list")
//Model 是springmvc的传数据至前端的，只要在方法的参数里写了这个参数对象，前端就可以获取该Model参数对象的值，
//springBoot可以直接使用map 也可以达到Model的效果，并且 springboot也可以使用Model
public String list(Model model){
model.addAttribute("username","zz");
model.addAttribute("age","22");
ArrayList<Student> students = new ArrayList<>();
students.add(new Student(110,"张峥","男"));
students.add(new Student(110,"lisi","男"));
students.add(new Student(110,"wangwu","男"));
model.addAttribute("stuList",students);
return "stu/list";
}
}
```

**对应页面 freemark模板**
```html 
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Title</title>
</head>
<body>
欢迎${username}
<#--可以对model数据进行判断-->
<#if (age< 18)>-小朋友
<#elseif (age>30)>-大叔
<#else>帅哥美女
</#if>
<table border="1">
<tr>
<td>ID</td>
<td>名字</td>
<td>性别</td>
</tr>
<#--？reverse倒叙-->
<#--?sort_by排序-->
<#list stuList?sort_by("id")?reverse as stu >

<tr>
<td> ${stu.id}</td>
<td> ${stu.username}</td>
<td> ${stu.gender}</td>
</tr>
</#list>
</table>
</body>
</html>
```

### 前端通过url向后端传值
```java
@RestController
@RequestMapping("user")
public class UserController {
@ResponseBody()
@RequestMapping("{id}")
//@PathVariable("name")用户获取映射路径所传参数，格式 参数名称和映射路径的参数名称相同的话@PathVariable()内可以不填内容
public User uerInfo(@PathVariable("id") Integer id1){
User user = new User( "admin", "123");
user.setId(id1);
return user;
}
} 
```
数据库配置文件
```properties 
#数据库配置
spring.datasource.url=jdbc:mysql://localhost:3306/sys?serverTimezone=UTC&useSSL=false
spring.datasource.username=root
spring.datasource.password=123456
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
```