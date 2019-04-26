---
title: 'Servlet的作用域和内置对象'
date: 2019-04-25 23:07:40
tags: [Servlet]
---
# Servlet的作用域和内置对象

## 1. 三大作用域对象
作用域(scope):作用是实现在多个web组件(Servlet/JSP)之间的数据共享
**Servlet三大作用域**:

| 名称       |   类型 | 描述|
| --------   | -----  | ----  |
|request|HttpServletReuqest|将数据放在请求作用域中,在一次请求中实现数据的共享,比如请求转发|
|session|HttpSession|将数据放在当前的会话作用域中,只要浏览器不关闭,都能共享|
|application|ServletContext|将无数据放在当前应用作用域中,应用在服务器启动的时候创建,关闭的时候销毁|
操作作用域中的共享数据
1.设置共享数据

    void setAttribute(String name, Object o)  
          name:参数的名称
          o:参数值
2.获取共享数据

    Object getAttribute(String name)  :根据指定的名称获取共享数据
3.修改共享数据

    重新设置一个同名的共享数据
4.删除共享数据

    void removeAttribute(String name)  ;根据指定的名称删除共享数据
**共享数据放在哪一个作用域中就只能在哪一个作用域中获取**
## 2. JSP三大指令
**标准指令**:设定JSP网页的整体配置信息
**特点**:

    并不向客户端产生任何输出，
    指令在JSP整个文件范围内有效
    为翻译阶段提供了全局信息
    
**指令的语法格式**:

    <%@ 指令名称   属性名=属性值   属性名=属性值%>
1.page
	**作用**：定义JSP页面的各种属性
	**属性**：
	
    language:指示JSP页面中使用脚本语言。默认值java，目前只支持java。
    extends：指示JSP对应的Servlet类的父类。不要修改。
    import：导入JSP中的Java脚本使用到的类或包。（如同Java中的import语句）
            JSP引擎自动导入以下包中的类：
            javax.servlet.*
            javax.servlet.http.*
            javax.servlet.jsp.*
    注意：一个import属性可以导入多个包，用逗号分隔。
    sessioin:指示JSP页面是否创建HttpSession对象。默认值是true，创建
    buffer：指示JSP用的输出流的缓存大小.默认值是8Kb。
    autoFlush：自动刷新输出流的缓存。
    isThreadSafe：指示页面是否是线程安全的（过时的）。默认是true。
    true：不安全的。
    false：安全的。指示JSP对应的Servlet实现SingleThreadModel接口。
    errorPage:指示当前页面出错后转向（转发）的页面。
目标页面如果以"/"（当前应用）就是绝对路径。
    配置全局错误提示页面：web.xml
```xml
 <error-page>
	<exception-type>java.lang.Exception</exception-type>
	<location>/error.jsp</location>
  </error-page>
  <error-page>
	<error-code>404</error-code>
	<location>/404.jsp</location>
  </error-page>
```
    isErrorPage:指示当前页面是否产生Exception对象。
    contentType：指定当前页面的MIME类型。作用与Servlet中的response.setContentType()作用完全一致
    pageEncoding：通知引擎读取JSP时采用的编码（因为要翻译）,还有contentType属性的作用。
    isELIgnored:是否忽略EL表达式。${1+1}。默认值是false。
    page指令最简单的使用方式：<%@ page pageEncoding="UTF-8"%>
2.include（静态包含,开发中能用静的不用动的）

	作用：包含其他的组件。
	语法：<%@include file=""%>file指定要包含的目标组件。路径如果以"/"（当前应用）就是绝对路径
	原理：把目标组件的内容加到源组件中，输出结果。
	动态包含：
		采用动作元素：<jsp:include page=""/>路径如果以"/"（当前应用）就是绝对路径。
3.taglib 

	作用：引入外部的标签
	语法：<%@taglib uri="标签名称空间" prefix="前缀"%>
		<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
## 3. 九大内置对象
|名称|类型|描述|
| --------   | -----  | ----  |
|pageContext|PageContext|表示当前的JSP对象|
|request|HttpServletRequest|表示一次请求对象|
|session|HttpSession|表示一次会话对象,session="true"|
|application|ServletContext|表示当前应用对象|
|response|HttpServletResponse|表示一次响应对象|
|exception|Throwable|表示异常对象,isErrorPage="true"|
|config|ServletConfig|表示当前JSP的配置对象|
|out|JspWriter|表示一个输出流对象
|page|Object|表示当前页面|
## 4. JSP的四大作用域
| 名称       |   类型 | 描述|
| --------   | -----  | ----  |
|pageContext|PageContext|当前的JSp对象
|request|HttpServletReuqest|一次请求对象
|session|HttpSession|一次会话对象|
|application|ServletContext|当前应用对象|
## 5. 静态包含与动态包含 
**静态包含**:

    <%@include file="被包含的页面的路径"%>
    包含的时机:在JSP文件被翻译的时候合并在一起
    最终翻译得到一个java文件
**动态包含**:

    <jsp:include page="被包含页面的路径"></jsp:include>
    包含的时机:在运行阶段合并代码
    最终得到两个java文件
**动态包含和静态包含的选择**:

    如果被包含的页面如果是静态页面,那么使用静态包含
    如果被包含的如果是动态页面,那么使用动态包含

**在实际开始中通常将被包含的jsp页面的后缀名设置为jspf**