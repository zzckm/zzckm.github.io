---
title: 'Cookie与Session'
date: 2019-04-25 23:00:43
tags: [Javase]
---
# Cookie与Session

## 1. Servlet后续详解
### 1.1 一个Servlet可以有多个 `<url-pattern>`
### 1.2 配置Servlet可以使用通配符（*）
    *：表示任意字符
    A. /*：可以使用任意的字符访问当前的Servlet
    B. /xxx/*：xxx表示某一个模块，一般用于权限验证
    C. *.action,*.do等等
### 1.3 自定义的`<servlet-name>`
    注意这个内容不能为default
### 1.4 自定义启动Servlet启动时刻
    在执行Servlet的初始化操作的时候，如果初始化的处理工作比较复杂，那么第一个请求的用户的体验会非常差，那么可以设置Servlet执行初始化的时刻为服务器启动的时候
```xml
<servlet>
  	<servlet-name>register</servlet-name>
  	<servlet-class>com.zhiyou100.um.servlet.RegisterServlet</servlet-class>
  	<!--
  		配置Servlet启动时刻和顺序
  		内容：数字，数字越小初始化越早
  	-->
  	<load-on-startup>0</load-on-startup>
  </servlet>
```
## 2. HTTP协议无状态问题
### 2.1 什么是会话
    会话就是在打开浏览器后和服务器可以进行多次交流，即请求与响应
    注意：在一次会话中可以有多次请求和多次接受响应
### 2.2 无状态
    HTTP是无状态协议，也就是没有记忆力，每次请求之间无法共享数据。这就无法知道会话什么时候结束，什么时候开始，也无法确定发出请求的用户身份。
    出现的问题：请求之间无法实现数据的共享
### 2.3 会话跟踪技术
    一个会话中共享数据就是会话跟踪技术。
### 2.4 无状态问题解决方案
    A. 使用参数拼接在请求的URL后面，实现数据的传递
    问题：可以解决数据共享的问题，但是这种方式会将参数暴露在地址栏中，不安全
    B. Cookie
    C. Session
## 3. Cookie
### 3.1 Cookie是什么？
    Cookie：小甜点
    特点：客户端的技术，将共享数据保存在客户端（即浏览器）中
    在第一次请求的时候，将共享数据发送到浏览器中进行保存，以后每次请求都将之前保存的共享数据发送到到服务器。
### 3.2 Cookie的解释
![image_1cb61m8u313dp1ipq172mbqb1vpsp.png-29.9kB][1]
```java
//创建一个Cookie，类似于办卡
Cookie c = new Cookie("usernumber", u.getNumber());
//将Cookie返回给response，即将卡交给客户
resp.addCookie(c);
```
<div style="text-align:center">第一次请求</div>
![无标题.png-109.2kB][2]
<div style="text-align:center">非第一次请求</div>
![无标题.png-104.4kB][3]
### 3.3 Cookie的操作细节
![image_1cfn8nm3e13e1d8ubf47kl1rl59.png-90.4kB][4]




#### 3.3.1 创建Cookie对象，设置共享数据
```java
Cookie c = new Cookie(String name,String value);
//一个Cookie只能存储一个字符串类型的数据，不能存储其他类型的数据
```

#### 3.3.2 将Cookie响应给刘篮球
```java
response对象.addCookie(c);
```
#### 3.3.3 获取请求中的Cookie信息
```java
Cookie[] cs = request对象.getCookies();
for(Cookie c:cs){
    if("usernumber".equals(c.getName())){
        String value = c.getValue();
    }
}
```
#### 3.3.4 修改Cookie中的共享数据
    A. 重新创建一个新的cookie，名称和要修改的数据的Cookie的名称一样
    B. 先获取到要修改的cookie对象，再调用setValue(String newValue)重新设置值
    注意：修改Cookie中的数据，需要再次发送给浏览器
#### 3.3.5 操作Cookie的生命周期
```java
//默认是在关闭浏览器的时候销毁
c.setMaxAge(int expiry)
```
    expiry>0：设置Cookie能够存活expiry秒，即使关闭浏览器，不影响Cookie中的共享数据。比如：十天内免登录：setMaxAge(60*60*24*10)
    expiry=0：立即删除当前的Cookie信息
    expiry<0：缺省值，关闭浏览器的时候销毁
![image_1cb63umlp1rap1arv1sib4umj4t2b.png-40.8kB][5]
#### 3.3.6 删除Cookie中的共享数据
```java
setMaxAge(0);
```
#### 3.3.7 Cookie中的key和value不支持中文
```java
//设置编码
Cookie c = new Cookie("usernumber",URLEncoder.encode(number,"UTF-8"));
//获取的时候设置解码
usernumber = URLDecoder.decode(value,"UTF-8");
```
#### 3.3.7 Cookie的作用范围
Cookie在创建的时候会根据当前的Servlet的相对路径设置自己的路径，比如当前Servlet的路径为/login/loginServlet,则相对路径为/login/,那么只有在访问/login/下面的资源的时候，才能够将该Cookie发送到服务器。
```java
//设置Cookie的路径
setPath(String uri);
//Cookie对象.setPath("/")，表示当前项目中所有的资源都能够共享该Cookie信息
```
多个项目之间共享数据，则需要设置域范围，比如：
wenku.baidu.com
pan.baidu.com
Cookie对象.setDomain("baidu.com");
### 3.4 Cookie的作用与缺陷
#### 3.4.1 作用是实现会话跟踪
#### 3.4.2 Cookie的缺陷

     1. 获取Cookie信息很麻烦
     2. Cookie不支持中文
     3. 一个Cookie只能存储一个字符串类型的数据
     4. Cookie在浏览器中有数量的限制
        一个浏览器对一个站点最多存储20条Cookie信息
        一个浏览器最多只能存储300个Cookie
     5. 共享数据是保存在浏览器中的，很容易造成数据的泄露（不安全）

## 4. Session
### 4.1 Session是什么
    Session是服务端的会话技术，是将数据保存在服务端。其实底层就是Cookie。
### 4.2 Session的操作
#### 4.2.1 获取Session对象
```java
request对象.getSession()：和参数为true的一样
request对象.getSession(true)：获取Session对象，如果没有session对象，直接创建一个新的返回，缺省值
request对象.getSession(false)：获取Session对象，如果没有返回null
```
#### 4.2.2 设置共享数据
    Session对象.setAttribute(String name,Object value)
    Session可以存储任何类型的数据，比如登录用户的信息，可以封装到User对象中。
#### 4.2.3 修改Session中的共享数据
    重新设置一个同名的共享数据即可
#### 4.2.4 获取共享数据
    Object value = Session对象.getAttribute(String name);
#### 4.2.5 删除Session中的共享数据
    Session对象.removeAttribute(String name);
#### 4.2.6 销毁Session
    Session对象.invalidate()
#### 4.2.7 Session的命名规范
    Session中共享数据的属性名的命名规范：XXX_IN_SESSION
    Session对象.setAttribute("USER_IN_SESSION",user)
#### 4.2.8 Session中的序列化与反序列化
Session中存储的对象通常需要实现序列化接口

    在网络之间传输的数据格式为二进制数据
    序列化：将对象转换成二进制数据
    反序列化：将二进制数据转换成对象
#### 4.2.9 Session的超时管理
超时：在访问当前的资源的过程中，不和网页进行任何交互，超过设定的时间就是超时
```java
setMaxInactiveInterval(int interval)
```
在服务器中默认的配置为30分钟，通常不需要修改。
## 5. Cookie与Session的区别

 1. Cookie中只能保存ASCII字符串，Session中可以保存任意类型的数据，甚至Java Bean乃至任何Java类、对象等
 2. 隐私策略不同。Cookie存储在客户端，对客户端是可见的，可被客户端窥探、复制、修改。而Session存储在服务器上，不存在敏感信息泄露的风险
 3. 有效期不同。Cookie的过期时间可以被设置很长。Session依赖于名为JSESSIONI的Cookie，其过期时间默认为-1，只要关闭了浏览器窗口，该Session就会过期，因此Session不能完成信息永久有效。如果Session的超时时间过长，服务器累计的Session就会越多，越容易导致内存溢出。
 4. 服务器压力不同。每个用户都会产生一个session，如果并发访问的用户过多，就会产生非常多的session，耗费大量的内存。因此，诸如Google、Baidu这样的网站，不太可能运用Session来追踪客户会话。
 5. 浏览器支持不同。Cookie运行在浏览器端，若浏览器不支持Cookie，需要运用Session和URL地址重写。
 6. 跨域支持不同。Cookie支持跨域访问（设置domain属性实现跨子域），Session不支持跨域访问
 
 


  [1]: http://static.zybuluo.com/zhangwen100/hr8rfwtbbklois5v8h3xkufu/image_1cb61m8u313dp1ipq172mbqb1vpsp.png
  [2]: http://static.zybuluo.com/zhangwen100/bpdh2ecpnjpn74d3bmq8889p/%E6%97%A0%E6%A0%87%E9%A2%98.png
  [3]: http://static.zybuluo.com/zhangwen100/b08y5lr89hx35ebmn1ds97uk/%E6%97%A0%E6%A0%87%E9%A2%98.png
  [4]: http://static.zybuluo.com/zhangwen100/cqsbdpcq7zfq56i0gww8sig8/image_1cfn8nm3e13e1d8ubf47kl1rl59.png
  [5]: http://static.zybuluo.com/zhangwen100/0zpjhk8oe9ve6df7ru9yees5/image_1cb63umlp1rap1arv1sib4umj4t2b.png