---
title: 'Servlet'
date: 2019-04-25 23:05:40
tags: [Servlet]
categories: JavaWeb
---
# Servlet

## Servlet是什么
> Servlet其实就是一个规范，也可以说就是一个Java程序，这个程序运行在服务器上，用于接收和响应客户端的请求。

## 创建一个Hello Servlet

1. 创建WEB项目
    ![image_1ceks61uo15vk9jql57mm6f2u9.png-110.7kB][1]
2. 创建一个HelloServlet
> 实现Servlet接口
3. 配置web.xml中的servlet及映射如下
    ```
    <!-- 告诉tomcat，我这个项目有一个Servlet，名字是hello，对应的类是com.zhiyou100.servlet.HelloServlet -->
  <servlet>
        <servlet-name>hello</servlet-name>
  	    <servlet-class>com.zhiyou100.servlet.HelloServlet</servlet-class>
  </servlet>
  <!-- 注册一个servlet映射，servlet-name 具体要指向哪一个servlet ，url-pattern是客户端请求资源地址-->
  <servlet-mapping>
  	    <servlet-name>hello</servlet-name>
  	    <url-pattern>/s</url-pattern>
  </servlet-mapping>
      
    ```
4. 启动
5. 执行过程
    ![image_1cel04jpj12qe19ap124g10ebr6c39.png-119.1kB][2]

## Servlet生命周期
生命周期
: 创建——>初始化——>执行程序——>销毁

1. 创建：
    是由服务器进行创建，因此Servlet必须有无参构造方法，并且不能是private，默认的以及protected修饰权限
2. 初始化：
    是第一次请求时调用。但是不绝对，可以通过web.xml文件的配置提前启动。
    配置如下：
    ![image_1celb64amfb7178u1hu71cp8q4j66.png-23.6kB][3]
3. 执行
    每次请求执行一次
4. 销毁
    服务器正常关闭时执行，执行destroy()方法，销毁Servlet对象。该方法是在Tomcat正常关闭的时候才会执行。一般情况下，不会往里面写内容。


  ![1]( http://static.zybuluo.com/zhangwen100/ir3ehsendsm0wn57fbwnhmep/image_1ceks61uo15vk9jql57mm6f2u9.png)
  ![2]( http://static.zybuluo.com/zhangwen100/2rpqmrlmn4c8ewax4lnoo3kc/image_1cel04jpj12qe19ap124g10ebr6c39.png)
  ![3]( http://static.zybuluo.com/zhangwen100/7dbmv085zlhyykq5aj2momdd/image_1celb64amfb7178u1hu71cp8q4j66.png)