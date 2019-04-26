---
title: Scala
date: 2019-04-01 17:05:37
tags: [scala]
password: 123
---
# scala 简介 
1. 为什么要学习scala 
因为spark是用scala语言编写、
2. scala是什么
 是结合了面向对象和函数式编程思想的一门语言
3. scala适合写计算型的代码
- 是一个静态类型的语言
-  Jvm类型语言
**注：面向对象适合做命令式编程-流程型的代码**

JDK是SDK的一种
+ SDK software devlpoment kit 软件开发工具箱
+ JDK  Java    devlpoment kit Java开发工具箱
+ OOP  AOP  FP  面向对象，面向切面，  函数式编程

  **var** 变量  
  **val** 常量  
  **lazy** 延迟加载：在定义的时候是不执行的，在使用时才执行初始化

### 数据类型
- Any : 所有类的父类
- Nothing 所有类都的子类
- Null 所有引用类的子类
-  Unit

**AnyVal** 
（Byte,Short,Int,Long，Float,Double|Char,Boolean,Unit(空)）
所有值类型的父类
**AnyRef**
所有引用类型的父类

————下图实线表示父子关系， ------表示隐式转换
![](https://i.loli.net/2019/04/01/5ca1d65b48ea9.png)
trainl类似于接口 ，，可以让方法实现多继承  extend a with b,c,d

- {} 表达式 赋值语句等等都是有返回值的
  
- Scala编程语言不支持++ -- 操作，但支持+= -=操作

### 定义函数方法
``def m1(x:Int,Y:Int):Int = {x+y}``
**def** 关键字，用来声明定义方法
**m1** 方法名称
**x,y** 形参
**：Int)** 括号内的属于参数的类型
**（x:Int,y:Int**参数列表
**=** 表示连接方法名称、参数列表以及=后的代码执行体
**{}**  方法体
**)：Int**括号外的:Int表示返回值的类型

`` def cal(x:Int,f:(Int,Int)=>Int,y:Int)=f(x,y)``
**f:(Int,Int)=>Int** 表示参数方法
**f(x,y)** 表示调用方法的格式

**方法转换为函数**
var function1=m1
var function1: （Int，Int）=>int=(x:Int,y:Int)=>{x+y}
var a        :   Int            ={2+6}

纯粹的函数式编程就是调用某一个逻辑的时候，还可以传入另外一个逻辑（方法或函数）


**最终结论**
（方法和函数）都可以作为另外一个（方法或函数）的参数 

## scala练习


``` scala
package com.demo

import scala.util.control.Breaks

/**
  * @Author: 张峥
  * @Date: 2019/4/1 10:41
  */

/**
  * object是关键字 ，只能在object中定义main方法
  */
object HelloWorld {
  //def 关键字 表示方法
  //参数类型写在参数后的【】内
  //【】表示泛型
  //Arrary[String]=String[]
  //unit 表示返回值为空，相当于java中的void
  //scala中没有static关键词，在object里的所有东西都是静态的
  val name="Tom"
  val age:Int =12  //等于Java中的 public final int age=12
  var hobby="足球"




  // hobby:String  可省略：后的类型，这是scala的隐式推断
  def main(args: Array[String]): Unit = {
   /* println("helloword")
    println(s"I am :,$name")
    println(s"age: ,$age")
    println(
      """
         111111111111
         222222222222
         3333333333333
         44444444444
         55555555555
      """)
    def isOdd(a: Int) = {
      if(a%2==0) true else false;
    }

    val a=2
    val result=isOdd(a);
    println(result)
    //if本身可以有值的
    val result2=if(a%2==0) true else false
    print(result2)*/
    bearkDemo();
    hanshu();
  }
  //



//方法
def add(x: Int,y: Int): Int=x+y
  //    cal（）括号里面是输入参数      =f（）后面的是调用方法格式
  def cal(x:Int,f:(Int,Int)=>Int,y:Int)=f(x,y)

  //在scala函数是一个完整的对象，可以作为参数传递
def hanshu(): Unit = {
  val add1=(x:Int,y:Int)=>x+y
  val minus=(x:Int,y:Int)=>x-y
  val mul=(x:Int,y:Int)=>x*y
  val div=(x:Int,y:Int)=>x/y
  add1(1,1)
  val add2=add1;
  add2(2,2)
 println("+ "+cal(3,add1,1))
  println("- "+cal(3,minus,1))
  println("* "+cal(3,mul,1))
  println("/ "+cal(3,div,1))
                     // cal(8,(参数1,参数2)=>表达式,5）
 print("8%5结果等于： "+cal(8,(x:Int,y:Int)=>x%y,5))

}




  /**
    * continue
    */
  def contimueDemo(): Unit = {
     val  loop = new Breaks

      for (a <- 1 to 10) {
        println(a)
        loop.breakable(
        if (a == 5) {
          loop.break();
        }
      )

  }
}

  /**
    * break
    * op: =>unit 代表（）里面的代码块必须不能有输出和输出参数
    * {} 大括号包起来的 是一个代码块，且代码块可以有值  值是最后一行代码
    * 表达式本身是没有值得到，运算表达式！
    */
  def bearkDemo(): Unit = {
    var loop=new Breaks
    var aa=loop.breakable(
      for (a <-  1 to 10){
          if(a==5){
            loop.break();
          }
        println(a)
      }

    )
  }

  //for
  //遍历list  且 过滤list
  def listDemo(): Unit = {
    val numList=List(1,2,3,4,5,6,7,8)
    for(e <- numList
          if e!=3;if e%2==0){
      println(e)
    }

    val newlist=for(e <-numList if e!=3;if e%2==0) yield  e
  }
//to可以用until代替 但左闭右开
  //to 等于
  def forName(): Unit = {
    for(a <- 1 to 10)
      {
        print(a)
      }
  }

  def run(): Unit ={
    println("Zaiwomen ")
  }
}


```

## Scala集合

### 数组（Array）
**分成两大类**
* 定长数组：Array(Insert,delete是没有的，只能进行修改操作)
* 变长数组：ArrayBuffer 缓冲数组
    没有指定素组长度，理论上可以无限增加元素
+= 表示追加一个元素或者多个元素
++= 表示追加一个Array或者ArrayBuffer或者其他的seq类型的实现类的对象
insert(a,b) a是位置  b是需要追加的元素
remove（a,b）a是位置， b是个数


**要点**
var aa=new Array[Int](3)
打印为：aa=new Array(0.0.0))

var aa=Array[Int](3)
打印为：aa=new Array(3)

var aa=Array.apply(3)
打印为：aa=new Array(3)

1. 如果偶在构造数组的时候带了new表示new申请一个固定长度的数组
2. 如果没有使用new，其实也在构造数组，只不过调用了Array.apply(3)

定长数组转换成变长数组
aa.Buffer
变长数组转换成定长数组
aa.toArray

##### 数组的常用操作
  

### 集合（Seq 序列/列表，Set集合，Map映射）
Scala集合分为可变和不可变的集合
函数作为对象 可以作为参数传递--
::
+:
用于追加集合里的值，合并为一个新的集合

```Scala
package com.demo

/**
  * @Author: 张峥
  * @Date: 2019/4/2 9:23
  */
object  ListDemo{
  def main(args: Array[String]): Unit = {
    var l1=List(1,2,13,4,5)
    var l2=List("henan 1000","hebei 800","jiangsu 1200","shanghai 1100")
    //排序
    l2.sortWith((x,y)=>x.split(" ")(1).toInt<y.split(" ")(1).toInt).foreach(println)
    print("----------------------- fileter ----------------------------")
    l2.filter(x=>x.split(" ")(1).toInt>1000).foreach(println)
    l2.filter(x=>x.split(" ")(1).toInt>1000).map(s=>s.split("")(0)).foreach(println)
    l2.map(e=>if(e.split(" ")(1).toInt>1000) println(e))

    //reduce排序  x用来获取reduce的返回值的默认为0  y 为每次从list获取的值
    println("aaa "+l1.reduce((x,y)=>if(x<y) y else x))
    println("aaa "+l1.reduce((x,y)=>if(x<y) y else x))
    //特殊写法
    l1.reduce((n1,n2)=>n1+n2)
    l1.reduce(_+_)//效果等于上方的效果
    //排序



    l1.filter(x=>x%2==0).foreach(println)
    l1.filter(y=>y>2&&y<4).foreach(println)
    //foreach 和map唯一区别 有无输出返回值
    println(sum(l1))
     // listTest1
  }
  def sum(list:List[Int]):Int={if(list.size==1) list.head else list.head+sum(list.tail) }
  def listTest1: Unit ={
    val l1=List(1,2,3,4)
    //map（）对集合的每个元素做一个操作
    val l0=List(2,3,4)
    val l12=l1.map(x=>(l0.map(y=>x*y)).sum)
    println(l12)

    var l9=l1.map(x=>x+1)
    var l10=l1.map(x=>if(x%2==0) "X"+x else "Y"+x)
    println(l10)
    val l2=l1 :: l1
    val l3="1213"+:l2
    val l4=l2:+4
    val l5=Nil //空集合
    val l6=4::Nil::l1
    //::: 用于集合对集合
    val l7=l1:::(4::Nil)
    //删除元素，会生成新的 被删除过的集合
    val l8=l1.drop(1)
    println(l1)
    println(l2)
  }
}

```


## Scala Java互操作
由于Java和Scala的编译后的都是.class文件，都是通过JVM进行操作的，因此可以互相调用
Scala可以调用Java生成的类，反之亦然。使用时只要注意语法规范即可

特殊情况:
java不能使用scala的不可变集合

## 隐式转换
在scala中，当对象调用一个它本身并没有的方法时，编译器会从当前代码上下文中寻找拥有此方法的对象，然后在原对象的所拥有的方法中追加此方法。这个转换过程，不需要通过开发者手动操作，是编译器在背后默默地转换。因此称之为隐式转换。
这样可以再不更改原来代码的基础上，增加新的功能。提高代码的可维护性。
类似于设计模式中的代理模式。


**java集合转换成scala定长集合**
``` Java
package com.demo;

import java.util.ArrayList;

/**
 * @Author: 张峥
 * @Date: 2019/4/8 9:19
 */
public class javazChange {
    public static void main(String[] args) {

        ArrayList<String> l=getList();
    }

    public static ArrayList<String> getList() {
        return new ArrayList<String>();
    }
```

``` Java
package com.demo

/**
  * @Author: 张峥
  * @Date: 2019/4/8 9:19
  */
object scalaChange {

  def main(args: Array[String]): Unit = {

    val l=javazChange.getList();
    l.add("123")
    //把java集合转换成scala不可变集合(隐式转换)
    import  collection.JavaConverters._
    val l1=l.asScala.toList

  }
}
```



