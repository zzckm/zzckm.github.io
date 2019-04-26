---
title: 'Scala基础'
date: 2019-04-25 23:03:40
tags: [Scala]
---
# Scala基础

## **1. Scala 介绍**

### 1.1  什么是 Scala
Scala 是一种多范式的编程语言，其设计的初衷是要集成面向对象编程和函数式编程的各种特性 。 Scala 运行于 Java 平台 （ Java 虚拟机 ）， 并兼容现有的 Java 程序 。
![image_1cm0mnjs3fvp1l2e1pca1nh11ve19.png-694.4kB][1]
### **1.2  为什么要学 Scala**
 - 优雅：这是框架设计师第一个要考虑的问题，框架的用户是应用开发程序员，API 是否优雅直接影响用户体验。
 - 速度快：Scala 语言表达能力强，一行代码抵得上 Java 多行，开发速度快；Scala 是静态编译的，所以和 JRuby,Groovy 比起来速度会快很多。
 - 能融合到 Hadoop 生态圈：Hadoop 现在是大数据事实标准，Spark 并不是要取代 Hadoop，而是要完善 Hadoop 生态。JVM 语言大部分可能会想到 Java，但 Java 做出来的 API 太丑，或者想实现一个优雅的 API 太费劲。
<center>![image_1cm0msml54s817neot313e3lpjm.png-340.4kB][2]</center>

## **2. 开发环境准备**

### **2.1 安装Java**

### **2.2 安装Scala**


## **3. 基本语法**

### **3.1 cmd命令初体验**

### **3.2 声明值和变量**
Scala声明变量有两种方式，一个用val，一个用var。
val / var 变量名 : 变量类型 = 变量值。
val定义的值是不可变的，它不是一个常量，是不可变量，或称之为只读变量。

- scala默认为匿名变量分配val
- val定义的变量虽然不能改变其引用的内存地址，但是可以改变其引用的对象的内部的其他属性值。
- 为了减少可变性引起的bug，应该尽可能地使用不可变变量。变量类型可以省略，解析器会根据值进行推断。val和var声明变量时都必须初始化。




### **3.3 数据类型**
Scala 和 Java 一样，有 7 种数值类型 Byte、Char、Short、Int、Long、Float 和 Double（无包装类型）和 Boolean、Unit 类型.
**注意:** Unit 表示无值，和其他语言中 void 等同。用作不返回任何结果的方法的结果类型。Unit
只有一个实例值，写成()。
|类型|描述|
|---|---|
|Boolean|	true 或者 false|
|Byte	|8位, 有符号|
|Short	|16位, 有符号|
|Int	|32位, 有符号|
|Long	|64位, 有符号|
|Char	|16位, 无符号|
|Float	|32位, 单精度浮点数|
|Double	|64位, 双精度浮点数|
|String|	其实就是由Char数组组成|
与Java中的数据类型不同，Scala并不区分基本类型和引用类型，所以这些类型都是对象，可以调用相对应的方法。String直接使用的是java.lang.String. 不过，由于String实际是一系列Char的不可变的集合，Scala中大部分针对集合的操作，都可以用于String，具体来说，String的这些方法存在于类scala.collection.immutable.StringOps中。
由于String在需要时能隐式转换为StringOps，因此不需要任何额外的转换，String就可以使用这些方法。
每一种数据类型都有对应的Rich* 类型，如RichInt、RichChar等，为数值类型提供了更多的有用操作。

### **3.4 常用类型结构图**

Scala中，所有的值都是类对象，而所有的类，包括值类型，都最终继承自一个统一的根类型Any。统一类型，是Scala的又一大特点。更特别的是，Scala中还定义了几个底层类（Bottom Class），比如Null和Nothing。

 - Null是所有引用类型的子类型，而Nothing是所有类型的子类型。Null类只有一个实例对象，null，类似于Java中的null引用。null可以赋值给任意引用类型，但是不能赋值给值类型。
 - Nothing，可以作为没有正常返回值的方法的返回类型，非常直观的告诉你这个方法不会正常返回，而且由于Nothing是其他任意类型的子类，他还能跟要求返回值的方法兼容。
 - Unit类型用来标识过程，也就是没有明确返回值的函数。 由此可见，Unit类似于Java里的void。Unit只有一个实例，()，这个实例也没有实质的意义。
 
![image_1cm0p1biefv43em11s7s1tkvd1g.png-79.9kB][3]

### **3.5 运算符的重载**
+-\*/%可以完成和Java中相同的工作，但是有一点区别，他们都是方法。你几乎可以用任何符号来为方法命名。
**注意：** Scala中没有++、--操作符，需要通过+=、-=来实现同样的效果。

### **3.6 函数与方法**
在scala中，一般情况下我们不会刻意的去区分函数与方法的区别，但是他们确实是不同的。

案例
: 
    ```scala
    import scala.math._
    //_和java中*类似
    object TestScala01 {
      def main(args: Array[String]): Unit = {
        /**
          * 定义变量：var，val
          * var/val 变量名[:数据类型] = 变量值
          */
        val name = "张三"
        //name = "李四"  不能直接重新赋值
        var age = 17
        //定义变量的时候，也可以直接指定数据类型
        var banji:String = "大数据2期"
    
        println(age)
    
        //调用一个函数，求一下平方根
        var c = 5
    
        println(sqrt(c))
      }
    
    }
    
    ```

### **3.7 字符串的格式化输出**
```scala
//Java中的输出：正常输出
println("name = "+name,"age = "+age)
/*
  f:插值器，允许创建一个格式化的字符串，C语言里面printf
  %s,%d，%f等等：都是对于这个值的类型的说明
  $name%s表示的是以String的变量来打印name的值
 */
println(f"name = $name%s age = $age%2.6f")

/*
  s:插值器，允许在处理字符串的时候直接使用变量的值，类似于shell
 */
println(s"name = $name age = $age")
println(s"2 + 3 = ${2+3}")
println("2 + 3 = "+(2+3))

```


## **4. 控制结构**

### **4.1 if else**
scala中没有三目运算符，因为根本不需要。scala中if else表达式是有返回值的，如果if或者else返回的类型不一样，就返回Any类型（所有类型的公共超类型）。
```scala
var age = 19
var res =
  if(age > 40){
    "年龄大于40"
  }else{
    "年龄小于40"
  }
println(res)

var res1 = if(age>40) "年龄大于40" else "年龄小于40"
println(res1)
var res2 = if(age>40) "年龄大于40" else age
println(res2)



```

### 4.2 while
在scala中和Java中是一样的。while的返回值为Unit类型(())
```scala
import scala.util.control.Breaks
object TestScala04_while {

  def main(args: Array[String]): Unit = {
    var k = 1
    while(k<=10){
      k+=1
    }
    val break = new Breaks
    break.breakable{
      var i = 1
      while(i<10){
        println(i)
        i+=1;
        if(i == 5)
          break.break()
      }
    }
  }
}


```
**注意：**  中断循环的方法：1. 用Boolean类型的变量来控制。2. 使用嵌套函数，使用函数中return。3. 使用Breaks对象的break方法



总结
: 

 - 变量的声明
    val：初始化之后不能再次被赋值
    var：初始化之后可以多次被赋值
    
 - 类型
    Any（老祖宗），Anyval，Anyref，Null，Nothing
 - if else
    有返回值，可以返回不同类型的值
 - while
    无返回值，如果说有返回值（Unit）也可以
    break的用法
 - for

### 4.3 for

{}和()对于for表达式来说都可以。for 推导式有一个不成文的约定：当for 推导式仅包含单一表达式时使用原括号，当其包含多个表达式时使用大括号。值得注意的是，使用原括号时，早前版本的Scala 要求表达式之间必须使用分号。


## **5. 函数**

基本格式
: 
def 函数名(参数名1:参数类型1，参数名2:参数类型2):返回值类型 = {函数体}
    ```scala
    object TestScala07_function {
      //def 函数名(参数名1:参数类型1，参数名2:参数类型2):返回值类型 = {函数体}
      def main(args: Array[String]): Unit = {
        f1("孙康")
        println(sum(1,2,3,4,5,6,7,8,9,10))
        println( TestScala07_function.sum(1,2,3,4,5,6,7,8,9,10))
    
        f6("哈哈哈哈")
        f6("lalal",10)
        println("----------------------")
        println(f(4))
      }
      //定义一个没有返回值的函数
      def f1(str:String):Unit={
        println(s"哈哈哈哈，我是第一个函数$str")
      }
      //定义一个隐式的没有返回值的函数
      def f2(str:String)={
        println(s"啦啦啦啦啦，我是$str")
      }
      //定义一个多返回值的函数，但是可以不写返回类型，默认为Any
      def f3(n:Int)={
        if(n>10)
          "大于10"
        else
          n
      }
      //定义一个返回特定类型的函数
      def f4(score:Int):String={
        if(score>60) "及格"
        else "不及格"
      }
    
      //定义一个不含参数的函数
      def f5() = {
        println("这是一个无参的函数")
      }
      //定义一个不限量参数
      def sum(args:Int*) ={
        var result = 0
        for(arg <- args){
          result+=arg
        }
        result
      }
      //带默认值参数的函数
      def f6(str:String,length:Int=5)={
        println(s"$str----$length")
      }
    
      //定义一个递归函数 1 1 2 3 5 8 13 21 34 55 斐波那契序列
      // f(n) = f(n-1) + f(n -2)
      //递归函数：一般情况下，必须指定返回值类型。
      def f(n:Int):Int={
        if(n<1) {
          println("输入格式有误")
          0
        }
        else{
          if(n == 1 || n ==2) 1
          else f(n-1)+f(n-2)
        }
    
      }
    
    
    
    }
    
    
    ```

## **6. 过程**
就是返回类型为Unit的函数就是过程

## **7. 数据结构**

### **7.1 数组**


### **7.2 元组**


### **7.3 List**

### **7.4 Queue**




  [1]: http://static.zybuluo.com/zhangwen100/2tabo6wr0eapgbzufc1ofrh5/image_1cm0mnjs3fvp1l2e1pca1nh11ve19.png
  [2]: http://static.zybuluo.com/zhangwen100/oi7yhu9v3rmyc1lhxvqcoils/image_1cm0msml54s817neot313e3lpjm.png
  [3]: http://static.zybuluo.com/zhangwen100/fmr60xjk8f99i0dsofgslq8w/image_1cm0p1biefv43em11s7s1tkvd1g.png