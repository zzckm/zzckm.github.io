---
title: spark-入门笔记
date: 2019-04-08 10:29:07
tags: [spark,scala]
categories: spark
---

# Spark
可使用Java、Scala、Python、R和Sql 快速编写应用程序

## rdd
(Resilient Distributed Datasets)弹性分布式数据集
* 弹性：表示数据是可恢复的 可容错
    利用的是血缘（依赖）关系
* 分布式：数据来源于分布式集群（如 hdfs，hbase,kafka）
* 数据集: 数据组织成一个集合(List)
  

## spark 运行模式：
1. local本地模式 local[n] n表示使用n个线程，如果使用local[*]表示根据cpu核数确定，一般用于测试
2. standalone  spark集群模式，需要搭建spark集群  例如：spark://192.168.200.129:7777...
3. yarn模式 不需要搭建spark集群，直接将spark应用提交到yarn运行。类似于mapreduce
4. mesos k8s。。。。。

## rdd操作

### sc=sparkcontext
  1. **加载数据转换成rdd** —spark操作的对象是rdd，因此需要把相关集合对象转换成rdd

``` Scala
val data = Array(1, 2, 3, 4, 5)
val distData = sc.parallelize(data)
// 4是分区数量
val distData = sc.parallelize(data，4)
```
2. 默认获取的数据会被分布式的存储在多个分区中，除非在sc下的方法中增加分区数量如：（，1）
3. rdd数据源的两种，
a. paralize
b. 外部数据源
    textFile：spark记载rdd是lazy懒加载，也就是计算发生的时候才去加载，可以传入通配符，读取目录下所有的文件机或者压缩文件，默认情况下，一块就是一个分区，可以让分区大于快熟，不能让分区少于快数


``` Scala
scala > al rdd5=sc.textFile("hdfs://192.168.200.129:9000/hbase/MasterProcWALs/state-00000000000000000009.log")
//获取使用分区个数
scala > rdd5.partitions.size
//将使用保存在该路径下
scala > rdd5.saveAsTextFile("/log.txt")
```

**tranformations**:由rdd生成新的rdd 如 **map filter  groupby reducebykey**
**actions** :由rdd生成，返回一个结果**reduce saveasTextFile  foreach  count**

注：对于转换次数比较多的rdd可以使用cache（persist方法，将rdd缓存到内存中，以空间换时间，减少计算时间）。

### job-任务运行的部分
当rdd运行到action方法时（**reduce、 SaveAsTextFile、foreach count**），会触发job的运行。
由sparkcontext向spark集群提交job
### stage：
* spark将job划分成1到多个stage
* stage与stage之间通过shuffle划分
  注：如果在job过程中出现shuffle 就会划分成新的stage
* **发生shuffle的情况**有：**group by ,reduce by key , join ,distinct**....

**stage里是不会发生shuffle,但在stage与stage可能会发生shuffle操作
suffle操作相当于有网络通信**

**stage内部计算是并行的 不会发生网络通信
stage之间计算时串行的 会发生网络通信**

**注**： shuffle执行，就会多一个stage   action执行就是一个job  job一定比stage少， stage一定等于或多于job 一个job可以包含一个或者多个stage
### task
一个分区上的一次计算称为task
多个task组成stage

### driver
driver 运行sparkcontext的应用，用于做任务job的提交
### executor
executor：运行任务，对数据进行具体的计算

## 闭包
闭包：函数访问了外部的变量

driver里的代码是没有进行对数据做处理的代码是driver,executor 如果调用了driver的变量就会出错 形成闭包的状态
>{ **driver**
   val conf=new SparkConf()
    //设置app的名字
      .setAppName("demo")
    //设置spark运行模式 这里以local为例
      .setMaster("local[2]")
  val sc=new SparkContext(conf)
}
{ **executor**
     val rdd=sc.parallelize(Range(1,10000),4)
    Thread.sleep(10000)
}


## spark Java Api

新建一个maven或者Java项目，然后新建一个文件scala 于原本的资源包同目录，然后设置该scala文件为资源包，且右击项目→_→ AddFramework support→_→ 在左侧菜单栏选择scala →_→选择应用 这样就可以使用scala的class以及object了
``` Scala
package com.day01

import org.apache.spark.{SparkConf, SparkContext}

/**
  * @Author: 张峥
  * @Date: 2019/4/8 17:39
  */
object sparkCoreDemo {
  def main(args: Array[String]): Unit = {
    val conf=new SparkConf()
    //设置app的名字
      .setAppName("demo")
    //设置spark运行模式 这里以local为例
      .setMaster("local[2]")
    /*
    spark 运行模式：
    1，local本地模式 local[n] n表示使用n个线程，如果使用local[*]表示根据cpu核数确定，一般用于测试
    2，standalone  spark集群模式，需要搭建spark集群
    3，yarn模式 不需要搭建spark集群，直接将spark应用提交到yarn运行。类似于mapreduce
    4.mesos k8s。。。。。
     */
    val sc=new SparkContext(conf)
    val rdd=sc.parallelize(Range(1,10000),4)

    Thread.sleep(10000)

  }
}

```
### Api



#### transformations
* map
* reduceByKey 
* groupByKey
![](https://i.loli.net/2019/04/09/5cac1714ddff7.png)
* Api高性能算子-**mapPartition** 
  某些情况下需要频繁的创建和销毁资源，高性能算子可以在只在分区上创建或销毁。不用在每个
元素上创建或销毁。提升了性能。
![](https://i.loli.net/2019/04/09/5cac0c4d1570e.png)
* mapPartitionWithindex 再给你个索引下标
* sample 抽样
* union 并集
* intersection 交集
* **aggregateByKey 聚合 分为三段：**
1.  给定初始值，
2.  进行分块聚合运算,
3.  块与块之间的聚合运算
![](https://i.loli.net/2019/04/09/5cac127497880.png)
* sortByKey 按key排序
* join
* cogroup 分组
* cartesian 笛卡尔积
* coalesce  合并  减少rdd分区的数量  过滤大型数据集后，可以更有效的运行操作
* repatition  重新分区 随机重新分区，增加或者减少分区数量，进行平衡  **用于数据倾斜**
   默认是has分区 但使用has分区-相同的key一定分到一个分区



#### actions 
1111


### Spark 应用
spark 应用提交与部署

spark应用部署到yarn有两种模式
cluster:driver运行在集群的某一台主机上
client:driver运行在客户端

首先 在idea中编译好自己的jar包之后
pom.xml中 加入

**scala代码** 
注意需要注释掉spark的运行模式，因为后面会直接使用在yarn上
且在最后要写好 输出到什么地方 
``` Scala
package day02

import org.apache.spark.{SparkConf, SparkContext}

/**
  * @Author: 张峥
  * @Date: 2019/4/9 10:35
  */
object SparkWordCount {
  def main(args: Array[String]): Unit = {
    val conf=new SparkConf()
      //设置app的名字
      .setAppName("demo")
      //设置spark运行模式 这里以local为例
     // .setMaster("local[4]")
/*
reduceByKey 只作用于 keyValue格式下的
 */
    val sc=new SparkContext(conf)
    val rdd=sc.parallelize(List("hello","hello","world","github"))
    rdd.map(x=>(x,1)).reduceByKey(_+_).take(3).foreach(println)
    rdd.map(x=>(x,1)).countByKey().foreach(println)
    rdd.map(x=>(x,1)).groupByKey().map(kv=>(kv._1,kv._2.sum)).foreach(println)
    //rdd.groupBy(x=>x).map(kv=>(kv._1,kv._2.size)).foreach(println)
    // 结果打包到该路径下
    rdd.groupBy(x=>x).map(kv=>(kv._1,kv._2.size)).saveAsTextFile("hdfs://192.168.200.129:9000/SparkWordCount")
  }
} 
```

打包前需要在pom.xml中加入下列代码以防scala代码不编译


``` XML 
<build>
        <plugins>
        <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.6.1</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
            <plugin>
                <groupId>net.alchim31.maven</groupId>
                <artifactId>scala-maven-plugin</artifactId>
                <version>3.2.2</version>
            </plugin>


        </plugins>
    </build>
  ```

  然后再maven里clean后 点M字符的命令行，
  ![](https://i.loli.net/2019/04/09/5cac4df389de7.png)
  再选中需要导入的**模块项目** 打入以下命令-
  ``clean scala:compile compile package -DskipTests=true``
  ![](https://i.loli.net/2019/04/09/5cac4e392d7af.png)
  首次加载需要下载很多东西，稍等一会，然后完成后就可以打包了

  打包完成之后放入linux /tmp/spark目录下
  
  然后进入spark  bin目录下 
  ``
  spark-submit --class day02.SparkWordCount    --master yarn     --deploy-mode cluster    /tmp/spark/sparkDemo101-1.0-SNAPSHOT.jar 
  ``
  注：如果运行报错没有权限的话，在当前目录下输入
  ``chmod +x -R ./*``
  运行最后出现以下，则为正确
  ![](https://i.loli.net/2019/04/09/5cac65c43c62d.png)
  然后可进入ip:8088 查看状态


### 全局变量
#### 广播变量:
可以再exector中使用，可以分发给各个分布式节点使用,但不能修改
**作用**：一般用于大小表的join,小标广播能够节省网络传输，避免shuffle map join
实用于广播维度表给事实表使用
![](https://i.loli.net/2019/04/10/5cad4e0442073.png)
#### 累加器
作用于driver， exector只有向driver发送累加请求，由driver进行累加


### 窄依赖 宽依赖
窄依赖：子rdd的一个分区，只依赖与父rdd的一个分区
宽依赖：子rdd的一个分区 依赖于父rdd的多个分区

区分的关键在于shuffle
不发生shuffle是窄依赖，发生shuffle是宽依赖

### rpc 远程方法调用 
**传输通道**
socket网络通信
**客户端**
动态代理
**服务端**
反射

#### 客户端  client.java

这是一个远程累加器的服务端客户端的案例 注意看客户端的前段注释理解！

``` Java
package com.day03.rpc;

import java.io.InputStream;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;
import java.io.OutputStream;
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;
import java.net.Socket;

/**
 * 输出流和输入流是一个通道，每个端都有自己的输入输出，两者方向不同各为其主
 * 而让通道以及request和response可以结合使用的方法时socket 网络通信
 *
 * 客户端通过动态代理 获取向服务端发送请求和接收数据信息的功能
 * 运行过程：
 * 1，建立连接之后将方法名和参数放在request请求之中，开启输出流，然后写进客户端向服务端方向的输出流里
 * 2，服务端开启输入流，接收客户端的request请求，获取其中的方法名和参数，然后通过反射机制调用方法且生成返回值
 * 3，将方法生成的返回值放入response中，再开启服务端的输出流，并将结果返回值 写进服务端指向客户端的输出流，
 * 4，客户端得到服务端的回应，开启客户端的输入流接收response的返回值 输出即可
 *
 *
 * 形象理解
 * A区为服务端 B区为客户端  快递就是request ，回应消息 response
 * A区和B区之间的公路就是通道
 * A的输入流就是通过公路B区到A区的  输出流就是通过公路A区到B区的
 * 快递员通过将快递从B送到A，A收到了就打电话回应消息
 */
public class Client {
    public static void main(String[] args) {
        //通过动态代理 向服务端发送请求

        Acc proxy = (Acc) Proxy.newProxyInstance(Request.class.getClassLoader(), new Class[]{Acc.class}, new InvocationHandler() {
            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                Request request = new Request();
                request.setMethod(method.getName());
                request.setPara(args);
                //建立与服务端的链接
                Socket socket = new Socket("192.168.1.129", 999);
                OutputStream outputStream = socket.getOutputStream();
                ObjectOutputStream oos = new ObjectOutputStream(outputStream);
                //将请求以及你需要的方法名以及参数放入request中，写入到输出流里“暂存”
                oos.writeObject(request);


                //得到服务端的相应
                InputStream inputStream = socket.getInputStream();
                //对象流 反序列化
                ObjectInputStream ois = new ObjectInputStream(inputStream);
                Response response = (Response) ois.readObject();
                //通过服务端的执行后的结果，
                return response.getResult();
            }
        });


        int result = proxy.add(10000);

        System.out.println(result);

        /*AccImpl acc = new AccImpl();
        int add = acc.add(10);*/

    }
}

```
#### 服务端 server.java
``` Java
package com.day03.rpc;

import java.io.*;
import java.net.ServerSocket;
import java.net.Socket;

public class Server {
    public static void main(String[] args) throws Exception, ClassNotFoundException {
        //需要使用的方法
        AccImpl acc = new AccImpl();
        //建立通信连接
        ServerSocket serverSocket = new ServerSocket(999);
        for(;;){
            //接收客户端的连接
            Socket client = serverSocket.accept();
            //开启输入流，为了接收客户端请求
            InputStream inputStream = client.getInputStream();
            ObjectInputStream ois = new ObjectInputStream(inputStream);
            //接受来自客户端的请求  请求包括 方法名和参数
            Request request = (Request) ois.readObject();
            //AccImpl acc = new AccImpl();//需要使用的方法,如果将acc的实例化放到循环外就是会形成累加
            //需要通过反射机制 ,来调用方法,使用方法返回返回值给result
            Object result = AccImpl.class.getMethod(request.getMethod(), int.class).invoke(acc, request.getPara());
            //System.out.println("server result:"+request);
            //输出访问客户端的ip以及方法返回的结果
            System.out.println("client"+client.getInetAddress().getHostAddress()+" ，result："+(int)result);
            //将结果发送回去
            //将结果保存在response中，然后通过输出流将结果保存在response中
            Response response = new Response();
            response.setResult(result);
            //开启输出流，写入含有result的response对象
            OutputStream outputStream = client.getOutputStream();
            ObjectOutputStream oos = new ObjectOutputStream(outputStream);
            oos.writeObject(response);

        }
    }
}



```
#### 方法的接口 Acc
``` Java
package com.day03.rpc;

public interface Acc {
    int add(int num);
}
```

#### 方法 AccImp.java
``` Java
package com.day03.rpc;

import java.lang.reflect.Method;

public class AccImpl implements Acc {
    private int acc;
    @Override
    public int add(int num) {
        acc+=num;
        return acc;
    }

  /*  public static void main(String[] args) throws Exception, IllegalAccessException, InstantiationException, NoSuchMethodException {
        Acc object = (Acc) Class.forName("com.day03.rpc.AccImpl").newInstance();
        //知道方法名 调用方法  通过反射
        Method method = Class.forName("com.day03.rpc.AccImpl").getDeclaredMethod("add", int.class);
        int result = (int) method.invoke(object, 10);
        System.out.println(result);


    }*/
}

```

#### request和response 
``` Java
package com.day03.rpc;

import java.io.Serializable;
//需要反序列化接口实现
/**
 * Request在这里只是用来保存客户端向服务端发送的数据信息
 */
public class Request implements Serializable {
    private String method;
    private Object[] para;

    public String getMethod() {
        return method;
    }

    public void setMethod(String method) {
        this.method = method;
    }

    public Object[] getPara() {
        return para;
    }

    public void setPara(Object[] para) {
        this.para = para;
    }
}

//-----------------------------------------------------------------//

package com.day03.rpc;

import java.io.Serializable;
//需要反序列化接口实现
/**
 * Respnse在这里只是用来保存服务端向客户端发送的数据信息
 */
public class Response implements Serializable {
    private Object result;

    public Object getResult() {
        return result;
    }

    public void setResult(Object result) {
        this.result = result;
    }
}

```

### Spark SQL
#### sql 命令行
DataFrames
在spark bin目录下打开，命令行窗口 输入.\spark-shell --master local[2]
进入scala命令行
```  Scala
  scala > val df = spark.read.json("../examples/src/main/resources/people.json")
  scala > df.show
  scala > df.createOrReplaceTempView("people")
  //sparksql（）这句话获得的是一个DataFrames 需要show才能以结构化显示
  scala > spark.sql("select * from people where age>20").show
  ```


#### sql 案例代码
注：本地连接虚拟机的mysql出现拒绝连接的问题 此时给其授权 
> grant all privileges on *.* to root@'%' identified by "gxyzxf"；
> flush privileges;
``` Scala
package com.day03
import java.util.Properties

import org.apache.spark.sql.SparkSession
import org.joda.time.DateMidnight.Property
/**
  * @Author: 张峥
  * @Date: 2019/4/10 14:26
  */
object SparkSql {
  //定义case class
  case class  Record(name:String,age:Int)
  case class  Word(word:String)

  def main(args: Array[String]): Unit = {
    val spark = SparkSession
      .builder()
      .appName("Spark SQL basic example")
      .master("local[*]")
      //.config("spark.some.config.option", "some-value")
      .getOrCreate()

    // For implicit conversions like converting RDDs to DataFrames
    import spark.implicits._
    val df= spark.read.json("D:\\zz\\Tools\\spark-2.3.0-bin-hadoop2.7\\spark-2.3.0-bin-hadoop2.7\\examples\\src\\main\\resources\\people.json")
    //df.show()
    //读取jdbc数据
    val prop=new Properties()
    prop.setProperty("user","root")
    prop.setProperty("password","123456")
    prop.setProperty("driver","com.mysql.jdbc.Driver")
    spark.read.jdbc("jdbc:mysql://master1:3306/test","test",prop).show()

    //在实际操作中，很多数据一开始，没有模式，如日志，文本文件
    //可以先将数据变换成rdd  然后再将rdd转化成dataframe
    //dataframe就是给一个rdd加一个模式 使其结构化
    val rdd=spark.sparkContext.parallelize(List("tom,18","jerry,2"))

    //case class  Record(name:String,age:Int)
    val df2=rdd.map(s=>{
      val parts=s.split(",")
      Record(parts(0),parts(1).toInt)
    }).toDF()
    df2.show()
    //用sparksql实现wordcount
    //case class  Word(word:String)
    val df3=spark.sparkContext.parallelize(List("a","a","a","b"))
    df3.map(s=>Word(s)).toDF().createOrReplaceTempView("wc")
    spark.sql("select word,count(word) cw from wc group by word order by cw desc").show()
  }

}

```
运行结果
![](https://i.loli.net/2019/04/10/5cad9172d5fc4.png)
![](https://i.loli.net/2019/04/10/5cada1f1279f3.png)

### rdd-数据类型
#### dataframe
就是给一个rdd加一个模式 使其结构化
sparksession
sparkcontext
例如 sparksession.sparkcontext

#### dataset


当使用api操作数据的时候，建议使用dataset
当通过sql操作的时候，两种都行



#### 自定义函数

```Scala
package day04

import com.day03.SparkSql.Record

/**
  * @Author: 张峥
  * @Date: 2019/4/11 9:22
  */
object SparkDataSetDemo {
  case class  Record(name:String,age:Int)
  def main(args: Array[String]): Unit = {
   import  org.apache.spark.sql.SparkSession
    val spark=SparkSession
      .builder()
      .appName("Spark SQL basic example")
      .master("local[*]")
      //.config("spark.some.config.option", "some-value")
      .getOrCreate()

    val rdd=spark.sparkContext.parallelize(List("tom,18","jerry,2"))

    //隐式转换
    import spark.implicits._

    val ds=rdd.map(s=>{
      val parts=s.split(",")
      Record(parts(0),parts(1).toInt)
    }).toDS()
    val df=rdd.map(s=>{
      val parts=s.split(",")
      Record(parts(0),parts(1).toInt)
    }).toDF()


    //怎么变成rdd

    //建议使用 ：dataset：rdd[Object] dataset 可以通过面向对象的方式来操作数据
    ds.filter(record=>record.age>3).show()
    //dataframe=dataset[row]  dataframe通过的是sql方式进行操作数据
    df.filter(row=>row.getAs[Int]("age")>18)

    //dataSet 和 dataFrame 可以相互转换 建议使用dataset

    //无论使用dataset 还是dataFrame 变成表操作都一样
    ds.createOrReplaceTempView("t1")
    df.createOrReplaceTempView("t2")

    //自定义sql函数
    val toUpper=(word:String)=>word.substring(0,1).toUpperCase()+word.substring(1)
    //注册函数
    spark.sqlContext.udf.register("toUpper",toUpper)
    //自定义函数用于sql中
    spark.sql("select toUpper(name),age from t1 where age>1").show()


    //ds.show()
  }
}


```

### 小结
**针对以上代码进行小结**
**rdd**
数据文件直接new SparkContext().textFile("//")  将文件读取进来 成为rdd
或者自定义集合数组成为rdd new SparkContext().parallelize(List(""))
在rdd弹性分布式结果集中可以使用api进行调用

**dataFrame dataSet**
两种数据类型
1. 将结构化文件通过SparkSession.read.api("//") 注：api里得有相应的格式
2. 非结构化的文件只能先通过textfile 成为rdd ,然后导包 import spark.implicits._隐式转换的形式，再rdd().toDF 或者rdd().toDS 将rdd转换成dataFrame或者dataSet
3. 建立的集合也是同2需要从rdd进行转换


sparkcontext rdd
sparksession  dataframe dataset
sparkstreaming  dstream

dataFrame 针对于sql方式操作的
dataSet 通过面向对象进行的操作的





## 大数据处理的两种:
### 实时/流处理（real-time）:
数据的产生于数据的计算完成相隔时间很短，大约在秒级
**sparkstreaming** /storm
#### SparkStreaming
一般SparkStreaming 配合kafka使用 一方面原因是因为利用kafka的缓存通道，

sparkstreaming 有状态计算
状态：一个动态的属性 如累计



##### checkpoint
![](https://i.loli.net/2019/04/12/5cb0486575139.png)

##### UpdateStateByKey 状态计算
**注**：如果报错java.lang.NoSuchMethodError: net.jpountz.lz4.LZ4BlockInputStream
**原因**：应用在执行时对数据解码（反序列化）时，使用了默认的lz4解压缩算法，在spark-core中依赖的lz4版本是1.4，而kafka-client中依赖的lz4版本是1.3版本，在生成解压器时，版本不兼容异常。
解决方法：
1. conf = SparkConf().set("spark.io.compression.codec", "snappy")
2. pom.xml
  ``` XML
   <dependency>
            <groupId>org.apache.spark</groupId>
            <artifactId>spark-streaming-kafka-0-10_2.11</artifactId>
            <exclusions>
                <exclusion>
                    <artifactId>lz4</artifactId>
                    <groupId>net.jpountz.lz4</groupId>
                </exclusion>
            </exclusions>
            <version>2.3.0</version>
        </dependency>  
  ```

#### 窗口计算
计算最近一段时间的数据运算


### 离线处理（off-line）:
存储后处理 时间间隔长  mapreduce hive spark sparksql .....















Parquet?