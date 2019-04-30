---
title: 'Scala 之高级操作'
date: 2019-04-25 23:02:40
tags: [Scala]
categories: Scala
---
# Scala 之高级操作

## 面向对象

### 类

class

### 单例对象

object

### 接口

trait

### 继承与实现接口

extends

### 抽象类

abstract

## 2. Scala 的并发编程——Actor

> 注：我们现在学的Scala Actor是scala 2.10.x版本及以前版本的Actor。
Scala在2.11.x版本中将Akka加入其中，作为其默认的Actor，老版本的Actor已经废弃

### 2.1 什么是Scala Actor

概念
: Scala中的Actor能够实现并行编程的强大功能，它是基于事件模型的并发机制，Scala是运用消息（message）的发送、接收来实现多线程的。使用Scala能够更容易地实现多线程应用的开发。

传统java并发编程与Scala Actor编程的区别
: ![image_1cm5qspl5ag1ast1auirrifi19.png-52.4kB][1]
对于Java，我们都知道它的多线程实现需要对共享资源（变量、对象等）使用synchronized 关键字进行代码块同步、对象锁互斥等等。而且，常常一大块的try…catch语句块中加上wait方法、notify方法、notifyAll方法是让人很头疼的。原因就在于Java中多数使用的是可变状态的对象资源，对这些资源进行共享来实现多线程编程的话，控制好资源竞争与防止对象状态被意外修改是非常重要的，而对象状态的不变性也是较难以保证的。 而在Scala中，我们可以通过复制不可变状态的资源（即对象，Scala中一切都是对象，连函数、方法也是）的一个副本，再基于Actor的消息发送、接收机制进行并行编程

Actor方法执行顺序
: 
 
 1. 首先调用start()方法启动Actor
 2. 调用start()方法后其act()方法会被执行
 3. 向Actor发送消息

发送消息的方式
: 
    |格式|描述|
     |---|---|
      |!|	发送异步消息，没有返回值。|
    |!?	|发送同步消息，等待返回值。|
    |!!	|发送异步消息，返回值是 Future[Any]。|


### 2.2 案例

简单案例
: 


## Scala 高阶函数

### 样例类



  ![1]( http://static.zybuluo.com/zhangwen100/390lzr8bgxy1u0qp13ri03o0/image_1cm5qspl5ag1ast1auirrifi19.png)