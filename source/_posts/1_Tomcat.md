---
title: 'Tomcat'
date: 2019-04-25 23:22:40
tags: [Tomcat]
categories: JavaWeb
---
# Tomcat

## **一、WEB概述**

 - **原始年代1990-1992:**
1990年，HTML标记语言的出现标志Web开发时代的到来.
**B/S**架构开始在之后的岁月中不断的发展壮大，攻城略地蚕食传统**C/S**的领域。
如同所有的新生事物一样，在web的史前岁月，web的开发技术在在html标记诞生后，无论是在服务端还客户端都缓慢的发展着，在相当长的一个时间内，它并未像今天这样辉煌，甚至于只是静态的文本标识.
**关键字：**HTML
**技术特性：**静态文本显示，表现力和交互能力不足。如：[hao123官网][1]
 - **封建诸侯年代1993-1996:**
1993年，NCSA提出了CGI1.0草案。Web开发终于迎来了它的第二次重大飞跃，伴随着CGI，带来Web的动态处理能力，CGI就是这个时代的国王。(服务器端动态生成内容)
1994年，PHP
1996年，ASP
**关键字：**CGI(Common Gateway Interface )(Perl&&C&&Python)
**技术特性：**实现了客户端和服务器端的动态交互，在程序代码中写html标记，是面向过程的开发方式，用多进程运行。
**注：** CGI是Web服务器端组件，都能**产生Web动态页面**输出
 - **工业文明1996-1999:**
1997年，Sun公司推出了**Servlet规范**。Java阵营终于迎来了自己的web英雄。
1998年**JSP技术诞生**，也是基于Servlet，可以写Java代码，可以使用Servlet+JSP+JavaBean实现JavaWeb开发
1998年，Sun发布了EJB1.0标准（重量级JavaEE规范，目前学习的为轻量级的规范）
1999年，Sun正式发布了J2EE的第一个版本，紧接着，遵循J2EE，为企业级应用提供支持平台的各类应用服务器争先恐后的涌现出来（WebSphere，WebLogic，JBoss），（同时2001微软发布了ASP.NET技术）
##**二、CS和BS的区别**
 - **CS和BS是软件架构模式:**
    **C/S:** Client/Server :客户端/服务端架构:
    **B/S:** Browser/Server:浏览器/服务器架构:
 - **C/S:**
   **VB,Delphi,VC++,C#,Java awt/swing:**比如桌面QQ,扫雷,拱猪等运行在桌面的程序.
   **特点:** 在服务端主要就是一个数据库,把所有业务逻辑以及界面的渲染操作交给客户端完成.
   **优点:** 较安全,用户界面很丰富,用户体验不错等
   **缺点:** 每次升级都需要重新安装,针对于不同的操作系统开发,可移植性很差.
 - **B/S:**
  **JSP,ASP,PHP:**基于浏览器访问的应用,把业务逻辑交给服务端完成,客户端仅仅只做界面渲染和数据交换.
  **特点:** BS是特殊的CS,此时浏览器充当了客户端.基于HTTP协议的.
  **优点:** 只开发服务端,可以跨平台,移植性很强等.
  **缺点:** 安全性较低,用户体验较差等.
  
 - **现在的应用综合了BS和CS的优点:部分应用不再是单纯BS.**
   **富客户端技术:** RIA(Rich Internet Applications), 客户端会处理部分的业务逻辑, 也会做界面的渲染和数据交互. 界面丰富好比是CS.
   EasyUI,Flex,Extjs,Java FX等

   **瘦客户端技术:** 基于传统的html界面,客户端只界面的渲染和数据交互.(传统的BS)
## **三、服务器**
 - **服务器:**
  **软件服务器:**就是一个软件.
  **硬件服务器:**安装了软件服务器的主机，如果是做运维就会接触到，只做开发接触较少

 - **分类:**

    **1.http服务器：**专门处理静态页面的，用的比较少
    **<span style="color:red;">2.JavaWeb服务器：</span>** 比如：Tomcat等. 仅仅实现了JavaEE 13 种规范中的几（6）个规范.(Servlet容器)
    **3.应用服务器:** 实现了JavaEE13种规范.WebSphere(IBM),WebLogic(Oracle),JBoss(red hat)
 - **不同版本服务器所对应的Servlet/JSP规范**
**1. WebSocket**
WebSocket是HTML5开始提供的一种在单个 TCP 连接上进行全双工通讯的协议。
在WebSocket API中，浏览器和服务器只需要做一个握手的动作，然后，浏览器和服务器之间就形成了一条快速通道。两者之间就直接可以数据互相传送。
**2. EL表达式**
EL（Expression Language） 是为了使JSP写起来更加简单。表达式语言的灵感来自于ECMAScript 和 XPath 表达式语言，它提供了在 JSP 中简化表达式的方法，让Jsp的代码更加简化。
**语法结构:** ${expression}
**3. 个版本对比**  [参考链接][2]
|Servlet|JSP|EL|WebSocket|Tomcat|Java|
|:-:|:-:|:-:|:-:|:-:|:-:|
|4.0|2.3|3.0|1.1|9.0.x|8 and later|
|3.1|2.3|3.0|1.1|8.5.x|7 and later|
|3.1|2.3|3.0|1.1|8.0.x|7 and later|
|3.0|2.2|2.2|1.1|7.0.x|6 and later (WebSocket 1.0 requires 7)|
|2.5|2.1|2.1|N/A|6.0.x|5 and later|
|2.4|2.0|N/A|N/A|5.5.x|1.4 and later|
|2.3|1.2|N/A|N/A|4.1.x|1.3 and later|
|2.2|1.1|N/A|N/A|3.3.x|1.1 and later|
## **四、Tomcat服务器**
### **4.1 Tomcat的安装**
***Tomcat是使用Java语言编写的一个服务器(程序),要运行Tomcat,必须得有jre.***

 - **安装启动:**
 - **版本**
     32位的JDK --->32位的Eclipse--->32位Tomcat
     64位的JDK --->64位的Eclipse--->64位Tomcat
 - **安装目录**不能使中文的，并且安装路径不允许出现空格。
     如: D:\SoftWare\Tomcat\apache-tomcat-8.5.29:我们把该路径称之为Tomcat的***根路径***

 - **启动Tomcat服务器:** 
     Tomcat根/bin/startup.bat
     **注意:** 必须先配置**JAVA_HOME**或者**JRE_HOME**的环境变量:
     一般的我们只配置**JAVA_HOME**:配置为JDK的根路径（即JDK的安装路径）
     **JAVA_HOME**=D:\SoftWare\Java\jdk1.8.0_152
    配置好之后,再点击**Tomcat根路径/bin/startup.bat**:知道控制台没有打印重大的错误,Exception,没有一闪而过,就表示启动成功。
    Tomcat的**默认端口是8080**:
 - **访问:**
     打开浏览器:
     http://服务器所在主机的IP:服务器的端口号/资源名字
     http://服务器所在主机的名字:服务器的端口号/资源名字
    若服务在本机:
     http://本机的IP:服务器的端口号/资源名字
     http://127.0.0.1:服务器的端口号/资源名字
     http://localhost:服务器的端口号/资源名字
 - **Tomcat根下的目录:**
    bin:存放了启动/关闭Tomcat的等应用程序**工具**.
    conf:存放了Tomcat软件的一些**配置文件**.
    lib:存放了Tomcat软件启动运行的**依赖的jar**文件.
    logs:存放Tomcat **日志**记录(成功,失败)
    temp:临时目录,比如把上传的大文件存放于临时目录
    webapps:里面存放需要部署的 JavaWeb **项目目录**.
    work:**工作目录**,存放了jsp翻译成Servlet的java文件以及字节码文件.
### **4.2 Tomcat常见问题**
0. **未配置JAVA_HOME**
![image_1c9oqm1g58f01oalo25ud4dnkm.png-25.4kB][3]
1. **版本不对应**
![image_1c9o5kcbem5v1n9qie311ok1nagm.png-25.4kB][4]
<span style="color:red">原因在于64为的jdk安装了32为的Tomcat</span>
2. **出现404的页面**
![image_1c9o5qum7c1al011cgrhp316jn1g.png-28.6kB][5]
<span style="color:red">我们自己把资源的路径写错了,自己检查,如访问了一个不存在的页面!</span>
3. **Tomcat还未关闭,又再次重新启动Tomcat**
![image_1c9o603331sbbhlsld01p53bg62n.png-20.2kB][6]
出现:java.net.BindException:<span style="color:red"> Address already in use: JVM_Bind</span>异常
      该程序的端口以及被其他程序所占用:
     **注意:**出错之后,要习惯去查看日志信息:
     Tomcat根/logs/catalina.2018-03-29.log
4. **Tomcat下的配置文件的结构不能乱改**
```shell
29-Mar-2018 14:13:12.745 严重 [main] org.apache.tomcat.util.digester.Digester.fatalError Parse Fatal Error at line 165 column 7: 元素类型 "Host" 必须由匹配的结束标记 "</Host>" 终止。
 org.xml.sax.SAXParseException; systemId: file:/D:/SoftWare/Tomcat/apache-tomcat-8.5.29/conf/server.xml; lineNumber: 165; columnNumber: 7; 元素类型 "Host" 必须由匹配的结束标记 "</Host>" 终止。
```
5. **要保证XML内容编码和文件编码相同,若有中文,建议使用UTF-8,否则不能使用中文**
```shell
29-Mar-2018 14:24:21.195 警告 [main] org.apache.catalina.startup.Catalina.load Catalina.start using conf/server.xml: 
 com.sun.org.apache.xerces.internal.impl.io.MalformedByteSequenceException: 1 字节的 UTF-8 序列的字节 1 无效。
```
### **4.3 常见配置**
1. **端口号的配置**
Tomcat的默认端口是8080,HTTP协议的默认端口是80;
**步骤:**
    1.1 进入Tomcat根/conf/找到server.xml文件
    1.2 默认是在第70行,Connector元素的 port属性:
    1.3 配置为80端口(80端口是http协议的默认端口):
![image_1c9o9be6d1i21nakmhu1ts719a25b.png-12.6kB][7]
2. **虚拟主机的配置**
![image_1c9o9a9511od4eem1ojl18kt2u24u.png-33.7kB][8]
## **五、HTTP协议**
HTTP：**特点:无状态**, 默认端口就是80 
https: 收费，比HTTP安全

1. **什么是HTTP**
WEB浏览器与WEB服务器之间的一问一答的交互过程必须遵循一定的规则，这个规则就是HTTP协议。
HTTP是hypertext transfer protocol（超文本传输协议）的简写，它是TCP/IP协议之上的一个应用层协议，用于定义WEB浏览器与WEB服务器之间交换数据的过程以及数据本身的格式。
2. **HTTP协议到底约束了什么**
![image_1c9q96t8hko31teplk21uru1oit16.png-26.7kB][9]
    1. 约束了浏览器以何种格式向服务端发送数据:
    2. 约束了服务器应该以何种格式来接收客户端发送的数据:
    3. 约束了服务器应该以何种格式来反馈数据给浏览器;
    4. 约束了浏览器应该以何种格式来接收服务器反馈的数据.
3. **请求与响应**
    浏览器给服务器发送数据:一次请求
    服务器给浏览器反馈数据:一次响应
4. **HTTP协议的版本** 
	1. HTTP/1.0
	**方式：**
        若请求的有N个资源,得建立N次连接,发送N次请求,接收N次响应,关闭N次连接.
        每次请求的之间都要建立单独的连接,请求,响应,响应完关闭该次连接:
    **缺点:** 每请求一个资源都要单独的建立新的连接,请求完并关闭连接.
    **解决方案:** 能在一次连接之间,多次请求,多次响应,响应完之后再关闭连接.
    2. HTTP1.1规范:
    **方式：**    
        能在一次连接之间,多次请求,多次响应,响应完之后再关闭连接.
5. **使用浏览器查看响应和请求头信息**
    1. **请求消息的结构：**
一个请求行、若干请求头、以请求体，其中的一些请求头和实体内容都是可选的，请求头和实体内容之间要用空行隔开。
    2. **请求行:**
    GET /books/java.html HTTP/1.1
     请求行信息分3段:
     请求方式: GET
     请求资源:/books/java.html
     协议的版本:HTTP/1.1
    3. **请求头：**
    ![image_1c9qr2ts7l7o16vr1fppcel1n111m.png-39.3kB][10]
常见的请求头:
 - Accept：浏览器可接受的MIME类型（Tomcat安装目录/conf/web.xml中查找）
 - Accept-Charset：告知服务器，客户端支持哪种字符集
 - Accept-Encoding：浏览器能够进行解码的数据编码方式
 - Accept-Language：浏览器支持的语言。
 - Referer：当前页面由哪个页面访问过来的。
 - <span style="color:red">Content-Type：通知服务器，请求正文的MIME类型。取值：application/x-www-form-urlencoded默认值.对应的是form表单的enctype属性</span>
MIME的英文全称是"Multipurpose Internet Mail Extensions" 多用途互联网邮件扩展，它是一个互联网标准，在1992年最早应用于电子邮件系统，但后来也应用到浏览器。服务器会将它们发送的多媒体数据的类型告诉浏览器，**文件内容的类型:**
**MIME类型就是设定某种扩展名的文件用一种应用程序来打开的方式类型**
 - Content-Length：请求正文的长度
 - If-Modified-Since:通知服务器，缓存的文件的最后修改时间。
 - <span style="color:red">User-Agent:通知服务器，浏览器类型.</span>
 - Connection:表示是否需要持久连接。如果服务器看到这里的值为“Keep-Alive”，或者看到请求使用的是HTTP 1.1（HTTP 1.1默认进行持久连接 )
 - <span style="color:red">Cookie:这是最重要的请求头信息之一（会话有关）</span>
    4. **请求体:** 使用POST请求的时候才有
    5. **响应消息结构：**
    一个状态行、若干响应头、以及实体内容，其中的一些消息头和实体内容都是可选的，响应头和实体内容之间要用空行隔开。
    6. **状态行**
     HTTP/1.1 200 OK
     HTTP/1.1 404 Not Found
    常见的响应状态码:
     - 200:表示完全正常,OK:
     - 404:表示客户端有问题,就是我们的请求资源的路径不存在.
     - 500:表示服务端有问题,不是说是服务器有问题,而是我们的后台Java代码有问题.
    7. **响应头**
    ![image_1c9qsj1g9hal8p414b1vr4n552t.png-52.6kB][11]
    - Location：制定转发的地址。需与302/307响应码一同使用
    - Server：告知客户端服务器使用的容器类型
    - Content-Encoding：告知客户端服务器发送的数据所采用的压缩格式
    - Content-Length：告知客户端正文的长度
    - Content-Type：告知客户端正文的MIME类型
        Conent-Type:text/html;charset=UTF-8
    - Refresh：定期刷新。还可以刷新到其他资源
	    Refresh:3;URL=otherurl
	    3秒后刷新到otherurl这个页面
    - Content-Disposition：指示客户端以下载的方式保存文件。
	    Content-Disposition：attachment;filename=2.jpg
    - Expires：网页的有效时间。单位是毫秒(等于-1时表示页面立即过期)
	    Cache-Control：no-cache
	    Pragma：no-cache
	    控制客户端不要缓存
    - Set-Cookie:SS=Q0=5Lb_nQ; path=/search服务器端发送的Cookie（会话有关）
6. **GET和POST的请求区别**
    **GRT和POST都是请求方式:**
    - GET:
    浏览器地址栏:http://localhost/form.html?name=neld&age=18
    请求行:
     GET /form.html?name=neld&age=18 HTTP/1.1
     请求方式是:GET
     请求资源是: /form.html?name=neld&age=18
     请求资源包括请求参数:第一个参数使用?和资源连接,其他的参数使用&符号连接.
     缺点:暴露请求信息,**不安全**. ***请求信息不超过1kb***.这样就限定了GET方式不能做图片上传.（受限于url长度，具体的数值取决于浏览器和服务器的限制）
    直接在地里输入地址,回车请求,
    超链接的请求都属于GET方式.
    表单中的method=get
    - POST:
    浏览器地址栏:http://localhost/form.html#
    不再有请求信息:
请求行:
   POST /form.html HTTP/1.1
    POST方式的参数全部在请求的实体中:
    隐藏了请求信息,较安全:POST方式没有限制请求的数据大小,可以做图片上传.
    目前HTTP的请求方式只有两种:GET/POST:
    doPost/doGet

  ![1]( https://www.hao123.com/index.html
  ![2]( http://tomcat.apache.org/whichversion.html
  ![3]( http://static.zybuluo.com/zhangwen100/fifystschif29ryzyenxwhsf/image_1c9oqm1g58f01oalo25ud4dnkm.png)
  ![4]( http://static.zybuluo.com/zhangwen100/6xfhp9pb7ivjgft8xd6sszfw/image_1c9o5kcbem5v1n9qie311ok1nagm.png)
 ![5]( http://static.zybuluo.com/zhangwen100/ws28rks2is59phte1mib1z5x/image_1c9o5qum7c1al011cgrhp316jn1g.png)
  ![6]( http://static.zybuluo.com/zhangwen100/x4b0jfq7xx5ijplv9v89q2bs/image_1c9o603331sbbhlsld01p53bg62n.png)
  ![7]( http://static.zybuluo.com/zhangwen100/sevhe9914oeb4i6bedrz6rqp/image_1c9o9be6d1i21nakmhu1ts719a25b.png)
  ![8]( http://static.zybuluo.com/zhangwen100/iw0p0hf03tuvxa648v6kuexz/image_1c9o9a9511od4eem1ojl18kt2u24u.png)
  ![9]( http://static.zybuluo.com/zhangwen100/40rfiqay0cr4mi7p42l7dq48/image_1c9q96t8hko31teplk21uru1oit16.png)
  ![10]( http://static.zybuluo.com/zhangwen100/6xyo1yi4iq0ax2n1uhiyq3ov/image_1c9qr2ts7l7o16vr1fppcel1n111m.png)
  ![11]( http://static.zybuluo.com/zhangwen100/fwvy1hnftsamg2vupzb3xh3f/image_1c9qsj1g9hal8p414b1vr4n552t.png)
