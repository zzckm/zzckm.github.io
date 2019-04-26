---
title: 'Xml & Tomcat'
date: 2019-04-24 23:00:40
tags: [Xml,Tomcat]
---

#Xml & Tomcat

##Xml

>eXtendsible  markup language  可扩展的标记语言

###XML 有什么用?

1. 可以用来保存数据

2. 可以用来做配置文件

3. 数据传输载体

![document.png-27.5kB][1]

##定义xml

> 其实就是一个文件，文件的后缀为 .xml

###. 文档声明

		简单声明， version : 解析这个xml的时候，使用什么版本的解析器解析
		<?xml version="1.0" ?>
	
		encoding : 解析xml中的文字的时候，使用什么编码来翻译
		<?xml version="1.0" encoding="gbk" ?>
	
		standalone  : no - 该文档会依赖关联其他文档 ，  yes-- 这是一个独立的文档
		<?xml version="1.0" encoding="gbk" standalone="no" ?>

###encoding详解

> 在解析这个xml的时候，使用什么编码去解析。   ---解码。 

	 文字， 而是存储这些文字对应的二进制 。 那么这些文字对应的二进制到底是多少呢？ 根据文件使用的编码 来得到。 

> 默认文件保存的时候，使用的是GBK的编码保存。 

所以要想让我们的xml能够正常的显示中文，有两种解决办法

1. 让encoding也是GBK 或者 gb2312 . gbk=gb2312+生僻字+繁体字

2. 如果encoding是 utf-8 ， 那么保存文件的时候也必须使用utf-8

3. 保存的时候见到的ANSI 对应的其实是我们的本地编码 GBK。

为了通用，建议使用UTF-8编码保存，以及encoding 都是 utf-8


###元素定义（标签）

1.  其实就是里面的标签， <> 括起来的都叫元素 。 成对出现。  如下： 

    	<stu> </stu>

2.  文档声明下来的第一个元素叫做根元素 (根标签)

3.  标签里面可以嵌套标签

4.  空标签

    	既是开始也是结束。 一般配合属性来用。
        
    	<age/>


		<stu>
			<name>张三</name>
			<age/>
		</stu>

5. 标签可以自定义。 

   XML 命名规则
   	XML 元素必须遵循以下命名规则：

   	名称可以含字母、数字以及其他的字符 
   	名称不能以数字或者标点符号开始 
   	名称不能以字符 “xml”（或者 XML、Xml）开始 
   	名称不能包含空格 


	命名尽量简单，做到见名知义


###简单元素  & 复杂元素 

* 简单元素 

> 元素里面包含了普通的文字

* 复杂元素

> 元素里面还可以嵌套其他的元素


###属性的定义

> 定义在元素里面， <元素名称  属性名称="属性的值"></元素名称>

		<stus>
			<stu id="10086">
				<name>张三</name>
				<age>18</age>
			</stu>
			<stu id="10087">
				<name>李四</name>
				<age>28</age>
			</stu>
		</stus>



##xml注释：

> 与html的注释一样。 

	<!-- --> 
	如： 
	
		<?xml version="1.0" encoding="UTF-8"?>
		<!-- 
			//这里有两个学生
			//一个学生，名字叫张三， 年龄18岁， 学号：10086
			//另外一个学生叫李四  。。。
		 -->

> xml的注释，不允许放置在文档的第一行。 必须在文档声明的下面。

##CDATA区

* 非法字符

  严格地讲，在 XML 中仅有字符 "<"和"&" 是非法的。省略号、引号和大于号是合法的，但是把它们替换为实体引用是个好的习惯。 

     <   `&lt;`
    &   `&amp;`

如果某段字符串里面有过多的字符， 并且里面包含了类似标签或者关键字的这种文字，不想让xml的解析器去解析。 那么可以使用CDATA来包装。  不过这个CDATA 一般比较少看到。 通常在服务器给客户端返回数据的时候。

	<des><![CDATA[<a href="http://www.baidu.com">我爱黑马训练营</a>]]></des>


##XML 解析

> 其实就是获取元素里面的字符数据或者属性数据。

###XML解析方式(面试常问)

> 有很多种，但是常用的有两种。

* DOM

* SAX

![parse_type.png-45.9kB][2]

###针对这两种解析方式的API

> 一些组织或者公司， 针对以上两种解析方式， 给出的解决方案有哪些？

		jaxp  sun公司。 比较繁琐

		jdom
		dom4j  使用比较广泛
		
		


###Dom4j 基本用法

		element.element("stu") : 返回该元素下的第一个stu元素
		element.elements(); 返回该元素下的所有子元素。 

1. 创建SaxReader对象

2. 指定解析的xml

3. 获取根元素。

4. 根据根元素获取子元素或者下面的子孙元素


		try {
			//1. 创建sax读取对象
			SAXReader reader = new SAXReader(); //jdbc -- classloader
			//2. 指定解析的xml源
			Document  document  = reader.read(new File("src/xml/stus.xml"));
			
			//3. 得到元素、
			//得到根元素
			Element rootElement= document.getRootElement();
			
			//获取根元素下面的子元素 age
		//rootElement.element("age") 
			//System.out.println(rootElement.element("stu").element("age").getText());


			//获取根元素下面的所有子元素 。 stu元素
			List<Element> elements = rootElement.elements();
			//遍历所有的stu元素
			for (Element element : elements) {
				//获取stu元素下面的name元素
				String name = element.element("name").getText();
				String age = element.element("age").getText();
				String address = element.element("address").getText();
				System.out.println("name="+name+"==age+"+age+"==address="+address);
			}
			
		} catch (Exception e) {
			e.printStackTrace();
		}


SaxReader 创建好对象 。  

Document
Element

1. 看文档

2. 记住关键字 。

3. 有对象先点一下。

4. 看一下方法的返回值。 

5. 根据平时的积累。  getXXX setXXX 


###Dom4j 的 Xpath使用

>  dom4j里面支持Xpath的写法。 xpath其实是xml的路径语言，支持我们在解析xml的时候，能够快速的定位到具体的某一个元素。

1. 添加jar包依赖 

   jaxen-1.1-beta-6.jar

2. 在查找指定节点的时候，根据XPath语法规则来查找

3. 后续的代码与以前的解析代码一样。



			//要想使用Xpath， 还得添加支持的jar 获取的是第一个 只返回一个。 
			Element nameElement = (Element) rootElement.selectSingleNode("//name");
			System.out.println(nameElement.getText());


			System.out.println("----------------");

			//获取文档里面的所有name元素 
			List<Element> list = rootElement.selectNodes("//name");
			for (Element element : list) {
				System.out.println(element.getText());
			}



##XML 约束【了解】

如下的文档， 属性的ID值是一样的。 这在生活中是不可能出现的。 并且第二个学生的姓名有好几个。 一般也很少。那么怎么规定ID的值唯一， 或者是元素只能出现一次，不能出现多次？ 甚至是规定里面只能出现具体的元素名字。 

		<stus>
			<stu id="10086">
				<name>张三</name>
				<age>18</age>
				<address>深圳</address>
			</stu>
			<stu id="10086">
				<name>李四</name>
				<name>李五</name>
				<name>李六</name>
				<age>28</age>
				<address>北京</address>
			</stu>
		</stus>

###DTD

	语法自成一派， 早起就出现的。 可读性比较差。 
![image_1cegiq80lq0717e62ti1nf31cpe11.png-46.2kB][3]


1. 引入网络上的DTD

```xml
   	<!-- 引入dtd 来约束这个xml -->

   	<!--    文档类型  根标签名字 网络上的dtd   dtd的名称   dtd的路径
   	<!DOCTYPE stus PUBLIC "//UNKNOWN/" "unknown.dtd"> -->
```

   2. 引入本地的DTD

```xml
      <!-- 引入本地的DTD  ： 根标签名字 引入本地的DTD  dtd的位置 -->
      <!-- <!DOCTYPE stus SYSTEM "stus.dtd"> -->
```

2. 直接在XML里面嵌入DTD的约束规则
```xml
   	<!-- xml文档里面直接嵌入DTD的约束法则 -->
   	<!DOCTYPE stus [
   		<!ELEMENT stus (stu)>
   		<!ELEMENT stu (name,age)>
   		<!ELEMENT name (#PCDATA)>
   		<!ELEMENT age (#PCDATA)>
   	]>
   	<stus>
   		<stu>
   			<name>张三</name>
   			<age>18</age>
   		</stu>
   	</stus>
```



```xml
<!ELEMENT stus (stu)>  : stus 下面有一个元素 stu  ， 但是只有一个
<!ELEMENT stu (name , age)>  stu下面有两个元素 name  ,age  顺序必须name-age
<!ELEMENT name (#PCDATA)> 
<!ELEMENT age (#PCDATA)>
<!---<!ATTLIST 元素名称 属性名称 属性类型 默认值>-->
<!ATTLIST stu id CDATA #IMPLIED> stu有一个属性 文本类型， 该属性可有可无
```


		元素的个数：
			＋　一个或多个
			*  零个或多个
			? 零个或一个
		属性的类型定义 
			CDATA : 属性是普通文字
			ID : 属性的值必须唯一,<!-- ID 类型的属性必须为 #REQUIRED -->
		<!ELEMENT stu (name , age)>		按照顺序来 
		<!ELEMENT stu (name | age)>   两个中只能包含一个子元素
###Schema

	其实就是一个xml ， 使用xml的语法规则， xml解析器解析起来比较方便 ， 是为了替代DTD 。
	但是Schema 约束文本内容比DTD的内容还要多。 所以目前也没有真正意义上的替代DTD


	约束文档：
		<!-- xmlns  :  xml namespace : 名称空间 /  命名空间
		targetNamespace :  目标名称空间 。 下面定义的那些元素都与这个名称空间绑定上。 
		elementFormDefault ： 元素的格式化情况。  -->
		<schema xmlns="http://www.w3.org/2001/XMLSchema" 
			targetNamespace="http://www.zhiyou100.com/teacher" 
			elementFormDefault="qualified">
			
			<element name="teachers">
				<complexType>
					<sequence maxOccurs="unbounded">
						<!-- 这是一个复杂元素 -->
						<element name="teacher">
							<complexType>
								<sequence>
									<!-- 以下两个是简单元素 -->
									<element name="name" type="string"></element>
									<element name="age" type="int"></element>
								</sequence>
							</comp lexType>
						</element>
					</sequence>
				</complexType>
			</element>
		</schema>
	
	实例文档：
		<?xml version="1.0" encoding="UTF-8"?>
		<!-- xmlns:xsi : 这里必须是这样的写法，也就是这个值已经固定了。
		xmlns : 这里是名称空间，也固定了，写的是schema里面的顶部目标名称空间
		xsi:schemaLocation : 有两段： 前半段是名称空间，也是目标空间的值 ， 后面是约束文档的路径。
		 -->
		<teachers
			xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
			xmlns="http://www.zhiyou100.com/teacher"
			xsi:schemaLocation="http://www.zhiyou100.com/teacher teacher.xsd"
		>
			<teacher>
				<name>zhangsan</name>
				<age>19</age>
			</teacher>
			<teacher>
				<name>lisi</name>
				<age>29</age>
			</teacher>
			<teacher>
				<name>lisi</name>
				<age>29</age>
			</teacher>
		</teachers>

##名称空间的作用

一个xml如果想指定它的约束规则， 假设使用的是DTD ，那么这个xml只能指定一个DTD  ，  不能指定多个DTD 。 但是如果一个xml的约束是定义在schema里面，并且是多个schema，那么是可以的。简单的说： 一个xml 可以引用多个schema约束。 但是只能引用一个DTD约束。

名称空间的作用就是在 写元素的时候，可以指定该元素使用的是哪一套约束规则。  默认情况下 ，如果只有一套规则，那么都可以这么写

	<name>张三</name>

	<aa:name></aa:name>
	<bb:name></bb:name>



###程序架构

网页游戏


* C/S(client/server)

> QQ 微信 LOL

优点：

	有一部分代码写在客户端， 用户体验比较好。 

缺点： 

	服务器更新，客户端也要随着更新。 占用资源大。 


* B/S(browser/server)

> 网页游戏 ， WebQQ ...

优点： 

	客户端只要有浏览器就可以了。 	占用资源小， 不用更新。 

缺点：

	用户体验不佳。 

###服务器

> 其实服务器就是一台电脑。 配置比一般的要好。


###Web服务器软件 

> 客户端在浏览器的地址栏上输入地址 ，然后web服务器软件，接收请求，然后响应消息。 
> 处理客户端的请求， 返回资源 | 信息

 Web应用  需要服务器支撑。 index.html

	Tomcat  apache

	WebLogic BEA
	Websphere IBM  
	
	IIS   微软


###Tomcat安装

1. 直接解压 ，然后找到bin/startup.bat

2. 可以安装

启动之后，如果能够正常看到黑窗口，表明已经成功安装。 为了确保万无一失， 最好在浏览器的地址栏上输入 ： http://localhost:8080 , 如果有看到内容 就表明成功了。

3. 如果双击了startup.bat,  看到一闪而过的情形，一般都是 JDK的环境变量没有配置。 


###Tomcat目录介绍

bin##

		> 包含了一些jar ,  bat文件 。  startup.bat

conf##
​	
		tomcat的配置 	server.xml  web.xml

lib 

	  	tomcat运行所需的jar文件

logs

		运行的日志文件
temp

		临时文件

webapps##

		发布到tomcat服务器上的项目，就存放在这个目录。	

work(目前不用管)

		jsp翻译成class文件存放地
​	


##如何把一个项目发布到tomcat中

> 需求： 如何能让其他的电脑访问我这台电脑上的资源 。 stu.xml

	localhost : 本机地址

###1.  拷贝这个文件到webapps/ROOT底下， 在浏览器里面访问：

		http://localhost:8080/stu.xml

	* 在webaps下面新建一个文件夹xml  , 然后拷贝文件放置到这个文件夹中

​	
		http://localhost:8080/xml/stu.xml

		http://localhost:8080 ： 其实对应的是到webapps/root
		http://localhost:8080/xml/ : 对应是 webapps/xml
	
		使用IP地址访问：
	
		http://192.168.37.48:8080/xml/stu.xml

###2. 配置虚拟路径


使用localhost：8080 打开tomcat首页， 在左侧找到tomcat的文档入口， 点击进去后， 在左侧接着找到 Context入口，点击进入。

	http://localhost:8080/docs/config/context.html

1. 在conf/server.xml 找到host元素节点。

2. 加入以下内容。


   		<!-- docBase ：  项目的路径地址 如： D:\xml02\person.xml
   		path : 对应的虚拟路径 一定要以/打头。
   		对应的访问方式为： http://localhost:8080/a/person.xml -->
   		<Context docBase="D:\xml02" path="/a"></Context>

3. 在浏览器地址栏上输入： http://localhost:8080/a/person.xml


###3. 配置虚拟路径

1. 在tomcat/conf/catalina/localhost/ 文件夹下新建一个xml文件，名字可以自己定义。 person.xml

2. 在这个文件里面写入以下内容

   	<?xml version='1.0' encoding='utf-8'?>
   	<Context docBase="D:\xml02"></Context>

3. 在浏览器上面访问

   http://localhost:8080/person/xml的名字即可

###给Eclipse配置Tomcat


1. 在server里面 右键新建一个服务器， 选择到apache分类， 找到对应的tomcat版本， 接着一步一步配置即可。
2. 配置完毕后， 在server 里面， 右键刚才的服务器，然后open  ， 找到上面的Server Location , 选择中间的 Use Tomcat installation...

3. 创建web工程， 在WebContent下定义html文件， 右键工程， run as server 


##总结：

	xml

		1. 会定义xml

		2. 会解析xml

			dom4j  基本解析

			Xpath手法


	tomcat

		1. 会安装 ，会启动 ， 会访问。

		2. 会设置虚拟路径

		3. 给eclipse配置tomcat



​	

#Http协议

* 什么是协议

> 双方在交互、通讯的时候， 遵守的一种规范、规则。

* http协议

> 针对网络上的客户端 与 服务器端在执行http请求的时候，遵守的一种规范。 其实就是规定了客户端在访问服务器端的时候，要带上哪些东西， 服务器端返回数据的时候，也要带上什么东西。 


* 版本
	
	1.0

			请求数据，服务器返回后， 将会断开连接
	
	1.1

		请求数据，服务器返回后， 连接还会保持着。 除非服务器 | 客户端 关掉。 有一定的时间限制，如果都空着这个连接，那么后面会自己断掉。
	

###演示客户端 如何 与服务器端通讯。

> 在地址栏中键入网络地址 回车  或者是平常注册的时候，点击了注册按钮 ， 浏览器都能显示出来一些东西。那么背地里到底浏览器和服务器是怎么通讯。 它们都传输了哪些数据。


1. 安装抓包工具 HttpWatch (IE插件)

2. 打开tomcat. 输入localhost:8080 打开首页

3. 在首页上找到Example 字样 

> 6.x 和 7.x 的文档页面有所不同，但是只要找到example就能够找到例子工程

4. 选择 servlet 例子 ---> Request Parameter

![icon](img/img01.png)

接着点击Request  Parameters 的 Execute超链接


![icon](img/img02.png)

执行tomcat的例子，然后查看浏览器和 tomcat服务器的对接细节

![icon](img/img03.png)


###Http请求数据解释 

> 请求的数据里面包含三个部分内容 ： 请求行 、 请求头 、请求体

* 请求行

		POST /examples/servlets/servlet/RequestParamExample HTTP/1.1 

		POST ： 请求方式 ，以post去提交数据
			
		/examples/servlets/servlet/RequestParamExample
		请求的地址路径 ， 就是要访问哪个地方。
	
		HTTP/1.1 协议版本

* 请求头

		Accept: application/x-ms-application, image/jpeg, application/xaml+xml, image/gif, image/pjpeg, application/x-ms-xbap, */*
		Referer: http://localhost:8080/examples/servlets/servlet/RequestParamExample
		Accept-Language: zh-CN
		User-Agent: Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 6.1; WOW64; Trident/4.0; SLCC2; .NET CLR 2.0.50727; .NET CLR 3.5.30729; .NET CLR 3.0.30729; Media Center PC 6.0; .NET4.0C; .NET4.0E)
		Content-Type: application/x-www-form-urlencoded
		Accept-Encoding: gzip, deflate
		Host: localhost:8080
		Content-Length: 31
		Connection: Keep-Alive
		Cache-Control: no-cache

		Accept: 客户端向服务器端表示，我能支持什么类型的数据。 
		Referer ： 真正请求的地址路径，全路径
		Accept-Language: 支持语言格式
		User-Agent: 用户代理 向服务器表明，当前来访的客户端信息。 
		Content-Type： 提交的数据类型。经过urlencoding编码的form表单的数据
		Accept-Encoding： gzip, deflate ： 压缩算法 。 
		Host ： 主机地址
		Content-Length： 数据长度
		Connection : Keep-Alive 保持连接
		Cache-Control ： 对缓存的操作

* 请求体

>浏览器真正发送给服务器的数据 
	
		发送的数据呈现的是key=value ,如果存在多个数据，那么使用 &

		firstname=zhang&lastname=sansan


###Http响应数据解析

> 请求的数据里面包含三个部分内容 ： 响应行 、 响应头 、响应体

	HTTP/1.1 200 OK
	Server: Apache-Coyote/1.1
	Content-Type: text/html;charset=ISO-8859-1
	Content-Length: 673
	Date: Fri, 17 Feb 2017 02:53:02 GMT

	...这里还有很多数据...

* 响应行
	
		HTTP/1.1 200 OK

		协议版本  

		状态码 

			咱们这次交互到底是什么样结果的一个code. 
		
			200 : 成功，正常处理，得到数据。
	
			403  : for bidden  拒绝
			404 ： Not Found
			500 ： 服务器异常

		OK

			对应前面的状态码  

* 响应头

		Server:  服务器是哪一种类型。  Tomcat
	
		Content-Type ： 服务器返回给客户端你的内容类型

		Content-Length ： 返回的数据长度

		Date ： 通讯的日期，响应的时间		


##Get 和  Post请求区别

![icon](img/img04.png)

* post

		1. 数据是以流的方式写过去，不会在地址栏上面显示。  现在一般提交数据到服务器使用的都是POST
	
		2. 以流的方式写数据，所以数据没有大小限制。

* get

		1. 会在地址栏后面拼接数据，所以有安全隐患。 一般从服务器获取数据，并且客户端也不用提交上面数据的时候，可以使用GET
	
		2. 能够带的数据有限， 1kb大小


###Web资源

在http协议当中，规定了请求和响应双方， 客户端和服务器端。与web相关的资源。 

有两种分类

* 静态资源

	html 、 js、 css

* 动态资源

	servlet/jsp


##Servlet


* servlet是什么?

> 其实就是一个java程序，运行在我们的web服务器上，用于接收和响应 客户端的http请求。 

> 更多的是配合动态资源来做。 当然静态资源也需要使用到servlet，只不过是Tomcat里面已经定义好了一个 DefaultServlet
 


###Hello Servlet

1. 得写一个Web工程 ， 要有一个服务器。

2. 测试运行Web工程

		1. 新建一个类， 实现Servlet接口
	
		2. 配置Servlet ， 用意： 告诉服务器，我们的应用有这么些个servlet。

			在webContent/WEB-INF/web.xml里面写上以下内容。


		  <!-- 向tomcat报告， 我这个应用里面有这个servlet， 名字叫做HelloServlet , 具体的路径是com.zhiyou100.servlet.HelloServlet -->
		  <servlet>
		  	<servlet-name>HelloServlet</servlet-name>
		  	<servlet-class>com.zhiyou100.servlet.HelloServlet</servlet-class>
		  </servlet>
		  
		  <!-- 注册servlet的映射。  servletName : 找到上面注册的具体servlet，  url-pattern: 在地址栏上的path 一定要以/打头 -->
		  <servlet-mapping>
		  	<servlet-name>HelloServlet</servlet-name>
		  	<url-pattern>/a</url-pattern>
		  </servlet-mapping>

		3. 在地址栏上输入 http://localhost:8080/项目名称/a


###Servlet执行过程

![icon](img/img05.png)


###Servlet的通用写法

		Servlet (接口)
			|
			|
		GenericServlet
			|
			|
		HttpServlet （用于处理http的请求）
		

1. 定义一个类，继承HttpServlet 复写doGet 和 doPost

![icon](img/img06.png)



##Servlet的生命周期

* 生命周期

> 从创建到销毁的一段时间

* 生命周期方法

> 从创建到销毁，所调用的那些方法。


* init方法

		在创建该servlet的实例时，就执行该方法。
		一个servlet只会初始化一次， init方法只会执行一次
		默认情况下是 ： 初次访问该servlet，才会创建实例。 

* service方法

		只要客户端来了一个请求，那么就执行这个方法了。
	 	 该方法可以被执行很多次。 一次请求，对应一次service方法的调用

* destroy方法

		
		  servlet销毁的时候，就会执行该方法
		
		  	1. 该项目从tomcat的里面移除。
		  	2. 正常关闭tomcat就会执行 shutdown.bat
		 

> doGet 和 doPost不算生命周期方法，所谓的生命周期方法是指，从对象的创建到销毁一定会执行的方法， 但是这两个方法，不一定会执行。

###让Servlet创建实例的时机 提前。

1. 默认情况下，只有在初次访问servlet的时候，才会执行init方法。 有的时候，我们可能需要在这个方法里面执行一些初始化工作，甚至是做一些比较耗时的逻辑。 

2. 那么这个时候，初次访问，可能会在init方法中逗留太久的时间。 那么有没有方法可以让这个初始化的时机提前一点。 

3. 在配置的时候， 使用load-on-startup元素来指定， 给定的数字越小，启动的时机就越早。 一般不写负数， 从2开始即可。 


		<servlet>
		  	<servlet-name>HelloServlet04</servlet-name>
		  	<servlet-class>com.zhiyou100.servlet.HelloServlet04</servlet-class>
		  	<load-on-startup>2</load-on-startup>
		  </servlet>




##ServletConfig

>Servlet的配置，通过这个对象，可以获取servlet在配置的时候一些信息

> 先说 ， 在写怎么用， 最后说有什么用。


		//1. 得到servlet配置对象 专门用于在配置servlet的信息
		ServletConfig config = getServletConfig();
		
		//获取到的是配置servlet里面servlet-name 的文本内容
		String servletName = config.getServletName();
		System.out.println("servletName="+servletName);
		
		
		//2、。 可以获取具体的某一个参数。 
		String address = config.getInitParameter("address");
		System.out.println("address="+address);


		//3.获取所有的参数名称
		Enumeration<String> names = config.getInitParameterNames();
		//遍历取出所有的参数名称
		while (names.hasMoreElements()) {
			String key = (String) names.nextElement();
			String value = config.getInitParameter(key);
			System.out.println("key==="+key + "   value="+value);
			
		}

###为什么需要有这个ServletConfig

1. 未来我们自己开发的一些应用，使用到了一些技术，或者一些代码，我们不会。 但是有人写出来了。它的代码放置在了自己的servlet类里面。 

2. 刚好这个servlet 里面需要一个数字或者叫做变量值。 但是这个值不能是固定了。 所以要求使用到这个servlet的公司，在注册servlet的时候，必须要在web.xml里面，声明init-params


在开发当中比较少用。

刚才的这个实验， 希望基础好或者感兴趣的同学，回去自己做一下。



##总结

* Http协议
	
		1. 使用HttpWacht 抓包看一看http请求背后的细节。 
	
		2. 基本了解 请求和响应的数据内容
	
				请求行、 请求头 、请求体
				响应行、响应头、响应体
	
		3. Get和Post的区别

* Servlet【重点】
	
		1. 会使用简单的servlet
	
			1.写一个类，实现接口Servlet
			2. 配置Servlet
			3. 会访问Setvlet
	
		2. Servlet的生命周期

			init 一次 创建对象 默认初次访问就会调用或者可以通过配置，让它提前 load-on-startup
			service	多次，一次请求对应一次service
			destory 一次 销毁的时候 从服务器移除 或者 正常关闭服务器

		3. ServletConfig

			获取配置的信息， params


  [1]: http://static.zybuluo.com/zhangwen100/amaocx39cegctq6frkyz92ct/document.png
  [2]: http://static.zybuluo.com/zhangwen100/6dh3okiqhp29ki5v8jjkc4hi/parse_type.png
  [3]: http://static.zybuluo.com/zhangwen100/6vcp845xkpau1n5fpohiepxn/image_1cegiq80lq0717e62ti1nf31cpe11.png