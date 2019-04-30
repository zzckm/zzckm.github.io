---
title: 'Servlet总结'
date: 2019-04-25 23:06:40
tags: [Servlet]
categories: JavaWeb
---
# Servlet总结
一. 创建Java Web项目
  
  :  1. 开发模式切换到Java EE
     2. 配置服务器
     ![image_1cg0g07rjos6teo54vtlu1vqf9.png-163.3kB][1]
     3. 创建Java Web项目
     4. web.xml要注意勾选

二. 创建Servlet
: 
 
    1. 创建一个实现Servlet接口的类或者创建一个直接继承或者间接继承HttpServlet的类
    * 该类必须存在无参构造方法
    * 重写doGet，doPost或者service方法
    
    * **生命周期：**
      
        - 创建 服务器来创建，可以手动更改创建时机。可以修改`<load-on-startup></load-on-startup>`配置。
        - 初始化 创建完对象，不需要第一次请求即可初始化。
        - 执行 每一次请求都会执行service方法。
        - 销毁 服务器正常关闭的时候执行destroy方法。
        
    2. 修改配置文件web.xml
        * 创建servlet
        ```XML
        <servlet>
            <servlet-name>login</servlet-name>
            <servlet-class>com.zhiyou100.servlet.DemoServlet1</servlet-class>
            <load-on-startup>1</load-on-startup>
        </servlet>
        ```
        * 创建servlet-mapping（里面的servlet-name必须存在）
        ```xml
        <servlet-mapping>
            <servlet-name>login</servlet-name>
            <!--url-pattern可以有多个，但是一个只能对应于一个Servlet-name-->
            <url-pattern>/login</url-pattern>
        </servlet-mapping>
        ```
    3. servlt请求
        请求的地址必须在url-pattern中有一个与之对应（匹配）
        
三. 数据共享问题
: 
    1. Servlet三大作用域
    
    |作用域对象名称|类型|
    |--|--|
    |request|HttpRequest|
    |Session|HttpSession|
    |application|ServletContext|
    
    2. Jsp的四大作用域
    
    |作用域对象名称|类型|
    |--|--|
    |request|HttpServletRequest|
    |Session|HttpSession|
    |application|ServletContext|
    |page|PageContext|
    
    3. Jsp的九大内置对象
    
    |对象名称|类型|
    |--|--|
    |request|HttpServletRequest|
    |Session|HttpSession|
    |application|ServletContext|
    |page|Object|
    |pageContext|PageContext|
    |out|JSPWriter|
    |exception|Throwable|
    |config|ServletConfig|
    |response|HttpResponse|

 4. cookie
     - 如何设置cookie
     - Cookie c = new Cookie();
     - response.addCookie(c);
        
 5. 啊啊
      
        
    



  ![1]( http://static.zybuluo.com/zhangwen100/7xq3yjmr951ddwjhrsm579m4/image_1cg0g07rjos6teo54vtlu1vqf9.png)