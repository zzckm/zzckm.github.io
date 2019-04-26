---
title: 'WEB组件数据交互'
date: 2019-04-23 23:00:40
tags: [WEB]
---
# WEB组件数据交互


> 1. 页面之间要实现跳转
2. 页面之间要实现数据的共享
3. 使用的工具：servlet和jsp

## 一. JSP的出现
> JSP动态网页=Java代码+HTML代码（说白了，在HTML中可以随意的编写Java代码）

## 二. 重定向与转发的区别

转发
: 

 1. 浏览器的地址不会发生改变，比如：地址栏中/login,最终依然为/login
2. 共享同一个请求中的数据
3. 最终地址栏中的地址由谁决定？由最后的servlet中是重定向还是转发决定，如果转发的是jsp页面，那么地址栏中不会有变化，如果是重定向到jsp页面，那么地址栏中有变化。如果最后转发的不是jsp页面，而是一个请求，那么继续循环。
4. 可以访问WEB-INF目录中的资源。
5. 请求转发不能跨域访问，只能跳转到当前应用中的资源

URL重定向
: 

 1. 浏览器的地址会发生改变
 2. 重定向相当于发送了两次请求
 3. 就决定了请求中的数据不能共享
 4. 最终响应给浏览器的地址由该请求中的地址决定
 5. 能够支持跨域访问，说白了可以访问其他应用中的资源
 6. 不能访问WEB-INF目录中的资源
 

什么时候用什么？
: 

 1. 如果请求中需要共享数据，那就来一个转发
 2. 如果需要跨域访问，那就来一个重定向
 3. 如果需要访问WEB-INF下的资源，那就来一个转发
 4. 其他情况下随便选择
 
## 三. Servlet的三大作用域对象

> 作用：共享数据

request
: 每一次请求都是一个新的request对象，如果想共享request数据，那么就使用转发，因为request对象不会重新产生

session
: 每一次会话都是一个新的session对象，一个会话可以有多次请求，这中间是可以共享数据的

application
: 应用对象，服务器软甲启动到关闭，Tomcat启动到关闭，表示一个应用，在一个应用中有且只有一个application对象，作用于整个WEB应用，可以实现数据共享。

|对象名称|对象的类型|如何获取
|---|---|---|
|request|HttpServletRequest|直接通过参数得到
|session|HttpSession|getSession()
|application|ServletContext|getServletContext()

作用域如何共享数据？
: 

 1. 设置作用域中的共享数据（包含了修改）
    作用域对象.setAttribute(String arrtName,Object value);
 2. 获取作用域中的共享数据
    Object value = 作用域对象.getAttribute(String arrtName);
 3. 删除作用域中的共享数据
    作用域对象.removeAttribute(String arrtName)
    

## 四. Jsp的四大作用域对象

page
: 当前jsp页面

request
: 

session
: 

application
: 

|对象名称|对象的类型|描述|
|---|---|---|
|page|PageContext|当前的JSP页面
|request|HTTPServletRequest|当前的请求作用域对象|
|session|HttpSession|当前会话的作用域|
|application|ServletContext|整个应用的作用域

## 五.JSP的九大内置对象

|内置对象的名称|类型|说明|
|---|---|--|
|request|HttpServletRequest|请求对象|
|session|HttpSession|当前会话对象|
|application|ServletContext|应用对象|
|page|Object|当前的Servlet对象|
|pageContext|PageContext|当前JSP作用域对象|
|config|ServletConfig|当前Servlet的配置信息对象|
|out|JSPWriter|字符输出流对象|
|response|HttpServletResponse|响应对象|
|exception|Throwable|异常类型|

## 六. JSP指令

page
: 常用的有language,import,pageEncoding,errorPage
	**作用**：定义JSP页面的各种属性
	**属性**：
	
    language:指示JSP页面中使用脚本语言。默认值java，目前只支持java。
    extends：指示JSP对应的Servlet类的父类。不要修改。
    import：导入JSP中的Java脚本使用到的类或包。（如同Java中的import语句）
            JSP引擎自动导入以下包中的类：
            javax.servlet.*
            javax.servlet.http.*
            javax.servlet.jsp.*
    **注意：**一个import属性可以导入多个包，用逗号分隔。
    sessioin:指示JSP页面是否创建HttpSession对象。默认值是true，创建
    buffer：指示JSP用的输出流的缓存大小.默认值是8Kb。
    autoFlush：自动刷新输出流的缓存。
    isThreadSafe：指示页面是否是线程安全的（过时的）。默认是true。
    true：不安全的。
    false：安全的。指示JSP对应的Servlet实现SingleThreadModel接口。
    errorPage:指示当前页面出错后转向（转发）的页面。
目标页面如果以"/"（当前应用）就是绝对路径。

include
: 包含指令
    静态包含：翻译成servlet的时候就已经将jsp页面进行合并
    动态包含：是将每一个jsp全部翻译成Servlet类之后，在运行阶段，动态的合并到一起
    **什么时候怎么用**
    如果包含的时候，需要传递新的数据，这个时候只能使用动态包含。
    
taglib
: Taglib指令引入一个自定义标签集合的定义，包括库路径、自定义标签

## 七. 标签库
> 清除JSP中的Java代码

1. 在WEB项目引入两个jar包
> 将JAR放到/WEB-INF/lib目录
2. 在JSP页面引入标签库
> <%@taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
3. 简单的使用
```jsp
<!--判断-->
<c:if test="判断条件" var="变量名" scope="page|request|session|application">

</c:if>

<!--多重判断-->
<c:choose>
	<c:when test="${fenshu>90 }">
		优秀
	</c:when>
	<c:when test="${fenshu>70 }">
		良好
	</c:when>
	<c:when test="${fenshu>60 }">
		及格
	</c:when>
	<c:otherwise>
		不及格
	</c:otherwise>
</c:choose>
<!--循环-->
<c:forEach items="${userList }" var="user">
	<tr>
		<td>${user.id }</td>
		<td>${user.getName() }</td>
		<td>${user.number }</td>
		<td>${user.sex }</td>
		<td>${user.age }</td>
		<td>${user.school }</td>
		<td>${user.major }</td>
		<td><a href="delete?id=${user.id }">删除</a></td>
	</tr>
</c:forEach>
<c:forEach begin="1" end="10" var="i" step="2">
	${i }<br>
</c:forEach>
```
## 八. EL表达式语言
