---
title: 'Spark第三天之SparkSQL'
date: 2019-04-25 23:14:40
tags: [Spark,SparkSQL]
---
# Spark 第三天之Spark SQL

## 1. Spark SQL概述

### **1.1	什么是Spark SQL**
![image_1cmio2ig3j22l44vdi31a1m509.png-58.8kB][1]
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Spark SQL是Spark用来处理结构化数据的一个模块，它提供了一个编程抽象叫做DataFrame并且作为分布式SQL查询引擎的作用。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;我们已经学习了Hive，它是将Hive SQL 转换成 MapReduce 然后提交到集群上执行，大大简化了编写MapReduce的程序的复杂性，由于MapReduce这种计算模型执行效率比较慢。所有Spark SQL的应运而生，它是将Spark SQL转换成RDD，然后提交到集群执行，执行效率非常快！
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;如果说Hive是为了简化MapReduce操作，那么Spark SQL可以说是为了简化RDD的操作。让一个不懂RDD操作的人也可以轻松使用Spark。

- 易整合
![image_1cmioarhitcofuckg21mj819dc1m.png-50.6kB][2]
- 统一的数据访问方式
![image_1cmiobr9f16pf1s3s1bj6n3b9vl23.png-62.6kB][3]
- 兼容Hive
![image_1cmiocej31n931sm4qrsmgi1fad2g.png-74.4kB][4]
- 标准的数据连接
![image_1cmioct9e1c7519la14141tah1v722t.png-54.9kB][5]
![image_1cmiokv241engdid15k31kt72so9.png-30.2kB][6]
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;SparkSQL可以看做是一个转换层，向下对接各种不同的结构化数据源，向上提供不同的数据访问方式。

### **1.2 Spark SQl的数据抽象**
![image_1cmioruf962jkms3t7uke10ahm.png-99.4kB][7]
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在SparkSQL中Spark为我们提供了两个新的抽象，分别是DataFrame和DataSet。他们和RDD有什么区别呢？首先从版本的产生上来看：
RDD (Spark1.0) —> Dataframe(Spark1.3) —> Dataset(Spark1.6)
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;如果同样的数据都给到这三个数据结构，他们分别计算之后，都会给出相同的结果。不同是的他们的执行效率和执行方式。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在后期的Spark版本中，DataSet会逐步取代RDD和DataFrame成为唯一的API接口。

Dataframe
: &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;与RDD类似，DataFrame也是一个分布式数据容器。然而DataFrame更像传统数据库的二维表格，除了数据以外，还记录数据的结构信息，即schema。同时，与Hive类似，DataFrame也支持嵌套数据类型（struct、array和map）。从API易用性的角度上看，DataFrame API提供的是一套高层的关系操作，比函数式的RDD API要更加友好，门槛更低。由于与R和Pandas的DataFrame类似，Spark DataFrame很好地继承了传统单机数据分析的开发体验。
![image_1cmip0l5k1ph1d5de5u11qo13eq13.png-67.3kB][8]
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;上图直观地体现了DataFrame和RDD的区别。左侧的RDD[Person]虽然以Person为类型参数，但Spark框架本身不了解Person类的内部结构。而右侧的DataFrame却提供了详细的结构信息，使得Spark SQL可以清楚地知道该数据集中包含哪些列，每列的名称和类型各是什么。DataFrame多了数据的结构信息，即schema。RDD是分布式的Java对象的集合。DataFrame是分布式的Row对象的集合。DataFrame除了提供了比RDD更丰富的算子以外，更重要的特点是提升执行效率、减少数据读取以及执行计划的优化，比如filter下推、裁剪等。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;DataFrame是为数据提供了Schema的视图。可以把它当做数据库中的一张表来对待。简单来说DataFrame=RDD+Schema
> DataFrame也是懒执行的。
性能上比RDD要高，主要有两方面原因： 
**定制化内存管理:**数据以二进制的方式存在于非堆内存，节省了大量空间之外，还摆脱了GC的限制。
**优化的执行计划:**查询计划通过Spark catalyst optimiser进行优化. 
**Dataframe的劣势**在于在编译期缺少类型安全检查，导致运行时出错.

Dataset
: 

 - 是Dataframe API的一个扩展，是Spark最新的数据抽象
 - 用户友好的API风格，既具有类型安全检查也具有Dataframe的查询优化特性。
 - Dataset支持编解码器，当需要访问非堆上的数据时可以避免反序列化整个对象，提高了效率。
 - 样例类被用来在Dataset中定义数据的结构信息，样例类中每个属性的名称直接映射到DataSet中的字段名称。
 - Dataframe是Dataset的特列，DataFrame=Dataset[Row] ，所以可以通过as方法将Dataframe转换为Dataset。Row是一个类型，跟Car、Person这些的类型一样，所有的表结构信息都用Row来表示。
 - DataSet是强类型的。比如可以有Dataset[Car]，Dataset[Person].

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;DataFrame只是知道字段，但是不知道字段的类型，所以在执行这些操作的时候是没办法在编译的时候检查是否类型失败的，比如你可以对一个String进行减法操作，在执行的时候才报错，而DataSet不仅仅知道字段，而且知道字段类型，所以有更严格的错误检查。就跟JSON对象和类对象之间的类比。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;RDD让我们能够决定怎么做，而DataFrame和DataSet让我们决定做什么，控制的粒度不一样。
     &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![image_1cmipmbtrurtraot1m5tnmkr1g.png-150.3kB][9]

三者的共性
: 

 - RDD、DataFrame、Dataset全都是spark平台下的分布式弹性数据集，为处理超大型数据提供便利
 - 三者都有惰性机制，在进行创建、转换，如map方法时，不会立即执行，只有在遇到Action如foreach时，三者才会开始遍历运算，极端情况下，如果代码里面有创建、转换，但是后面没有在Action中使用对应的结果，在执行时会被直接跳过.
 - 三者都会根据spark的内存情况自动缓存运算，这样即使数据量很大，也不用担心会内存溢出
 - 三者都有partition的概念
 - 三者有许多共同的函数，如filter，排序等
 - 在对DataFrame和Dataset进行操作许多操作都需要spark.implicits._进行支持
 - DataFrame和Dataset均可使用模式匹配获取各个字段的值和类型

三者的区别
: 

 - RDD
     - RDD一般和spark mllib同时使用
     - RDD不支持sparksql操作
 - DataFrame
     - 与RDD和Dataset不同，DataFrame每一行的类型固定为Row，只有通过解析才能获取各个字段的值
     - DataFrame与Dataset一般不与spark mllib同时使用
     - DataFrame与Dataset均支持sparksql的操作，比如select，groupby之类，还能注册临时表/视窗，进行sql语句操作
     - DataFrame与Dataset支持一些特别方便的保存方式，比如保存成csv，可以带上表头，这样每一列的字段名一目了然
 - Dataset
     -  Dataset和DataFrame拥有完全相同的成员函数，区别只是每一行的数据类型不同
     -  DataFrame也可以叫Dataset[Row],每一行的类型是Row，不解析，每一行究竟有哪些字段，各个字段又是什么类型都无从得知，只能用上面提到的getAS方法或者共性中的第七条提到的模式匹配拿出特定字段。而Dataset中，每一行是什么类型是不一定的，在自定义了case class之后可以很自由的获得每一行的信息

## **2. 执行SparkSQL** 

### **2.1 命令式操作**


###  **2.2 代码操作**
```scala
package com.zhiyou100.spark

import org.apache.spark.SparkConf
import org.apache.spark.sql.SparkSession

object TestSparkSql extends App {
  
  val conf = new SparkConf().setAppName("testSparkSql").setMaster("local[2]")
  val spark = SparkSession.builder().config(conf).getOrCreate()
  val sc = spark.sparkContext

  val employee = spark.read.json("C:\\Users\\zhang\\Desktop\\employees.json")
  employee.show()
  employee.createOrReplaceTempView("employee")
  spark.sql("select * from employee").show()
  spark.stop()
}


```

### **2.3 数据类型转换**
将RDD，DataFrame，DataSet之间进行互相转换

RDD  -》  DataFrame
: 
    
 - 直接手动转换
  ```shell
     scala> val people = spark.read.json("/opt/apps/Spark/spark-2.2.2-bin-hadoop2.7/examples/src/main/resources/people.json")
    people: org.apache.spark.sql.DataFrame = [age: bigint, name: string]
    
    scala> val people1 = sc.textFile("/opt/apps/Spark/spark-2.2.2-bin-hadoop2.7/examples/src/main/resources/people.txt")
    people1: org.apache.spark.rdd.RDD[String] = /opt/apps/Spark/spark-2.2.2-bin-hadoop2.7/examples/src/main/resources/people.txt MapPartitionsRDD[18] at textFile at <console>:24
    
    scala> val peopleSplit = people1.map{x => val strs = x.split(",");(strs(0),strs(1).trim.toInt)}
    peopleSplit: org.apache.spark.rdd.RDD[(String, Int)] = MapPartitionsRDD[19] at map at <console>:26
    
    scala> peopleSplit.collect
    res6: Array[(String, Int)] = Array((Michael,29), (Andy,30), (Justin,19))        
    
    scala> peopleSplit.to
    toDF   toDS   toDebugString   toJavaRDD   toLocalIterator   toString   top
    
    scala> peopleSplit.toDF
    res7: org.apache.spark.sql.DataFrame = [_1: string, _2: int]
    
    scala> peopleSplit.toDF("name","age")
    res8: org.apache.spark.sql.DataFrame = [name: string, age: int]
    
    scala> res8.show
    +-------+---+
    |   name|age|
    +-------+---+
    |Michael| 29|
    |   Andy| 30|
    | Justin| 19|
    +-------+---+
    
    
     
    ```
 - 通过Scala编程实现
    ```shell
    ## 创建 schema 
    scala> val schema = StructType(StructField("name",StringType)::StructField("age",IntegerType)::Nil)
    schema: org.apache.spark.sql.types.StructType = StructType(StructField(name,StringType,true), StructField(age,IntegerType,true))
    
    ## 加载RDD数据
    scala> val rdd = sc.textFile("/opt/apps/Spark/spark-2.2.2-bin-hadoop2.7/examples/src/main/resources/people.txt")
    rdd: org.apache.spark.rdd.RDD[String] = /opt/apps/Spark/spark-2.2.2-bin-hadoop2.7/examples/src/main/resources/people.txt MapPartitionsRDD[1] at textFile at <console>:30
    ## 创建Row对象
    scala> val data = rdd.map{x => val strs = x.split(",");Row(strs(0),strs(1).trim.toInt)}
    data: org.apache.spark.rdd.RDD[org.apache.spark.sql.Row] = MapPartitionsRDD[2] at map at <console>:32
    ## 生成DF
    scala> spark.createDataFrame(data,schema)
    18/09/06 09:45:00 WARN ObjectStore: Version information not found in metastore. hive.metastore.schema.verification is not enabled so recording the schema version 1.2.0
    18/09/06 09:45:00 WARN ObjectStore: Failed to get database default, returning NoSuchObjectException
    18/09/06 09:45:02 WARN ObjectStore: Failed to get database global_temp, returning NoSuchObjectException
    res0: org.apache.spark.sql.DataFrame = [name: string, age: int]
    ```


 - 反射
    ```shell
    scala> case class People(name:String,age:Int)
    defined class People
    
    scala> rdd.map{x => val strs=x.split(",");People(strs(0),strs(1).trim.toInt)}.toDF
    res2: org.apache.spark.sql.DataFrame = [name: string, age: int]
    
    ```

DataFrame -》 RDD
: 
    ```shell
    scala> res8.rdd
    res10: org.apache.spark.rdd.RDD[org.apache.spark.sql.Row] = MapPartitionsRDD[26] at rdd at <console>:31
    
    ```
    
RDD -》  DataSet
: 
    ```shell
    scala> peopleSplit.toDS
    res11: org.apache.spark.sql.Dataset[(String, Int)] = [_1: string, _2: int]
    
    scala> case class People(name:String,age:Int)
    defined class People
    
    scala> val peopleDSSplit = people1.map{x => val strs = x.split(","); People(strs(0),strs(1).trim.toInt)}
    peopleDSSplit: org.apache.spark.rdd.RDD[People] = MapPartitionsRDD[27] at map at <console>:28
    
    scala> peopleDSSplit.toDS
    res12: org.apache.spark.sql.Dataset[People] = [name: string, age: int]
    
    scala> res12.show
+-------+---+
|   name|age|
+-------+---+
|Michael| 29|
|   Andy| 30|
| Justin| 19|
+-------+---+

    
    ```
    
DataSet -》 RDD
: 
    
    ```shell
    scala> res12.rdd
    res14: org.apache.spark.rdd.RDD[People] = MapPartitionsRDD[32] at rdd at <console>:33
    
    scala> res14.map(_.name).collect
    res15: Array[String] = Array(Michael, Andy, Justin)
    
    
    ```


DataSet -》  DataFrame
: 
    ```shell
    scala> res12.toDF
    res16: org.apache.spark.sql.DataFrame = [name: string, age: int]
    
    
    
    ```


DataFrame -》 Datset
:   
    ```shell
    scala> res16.as[People]
    res17: org.apache.spark.sql.Dataset[People] = [name: string, age: int]
    
    
    ```

### **2.4 SQL的执行模式**

DSL风格语法
: 
    ```sql
    scala> val peopleDF = rdd.map{x => val strs=x.split(",");People(strs(0),strs(1).trim.toInt)}.toDF
    peopleDF: org.apache.spark.sql.DataFrame = [name: string, age: int]
    
    scala> peopleDF.select("name").show
    +-------+
    |   name|
    +-------+
    |Michael|
    |   Andy|
    | Justin|
    +-------+
    scala> peopleDF.filter($"age">20).show
    +-------+---+
    |   name|age|
    +-------+---+
    |Michael| 29|
    |   Andy| 30|
    +-------+---+
    scala> peopleDF.groupBy("age").count.show
    +---+-----+                                                                     
    |age|count|
    +---+-----+
    | 19|    1|
    | 29|    1|
    | 30|    1|
    +---+-----+

    
    ```




SQL风格语法
: 
    ```sql
    ## 创建表
    
    ## 借助于SparkSession
    ## createOrReplaceTempView：Session内可以访问，一旦session 停止，表自动删除。不需要前缀，如果表存在，会自动替换
    ## createGlobalOrReplaceTempView：一个应用级别的访问，多个session之间可以访问，但是一旦SparkContext关闭，也会删除。需要前缀
    ## createTempView：Session内可以访问，一旦session 停止，表自动删除。不需要前缀，如果表存在则报错
    ## createGlobalTempView：一个应用级别的访问，多个session之间可以访问，但是一旦SparkContext关闭，也会删除
    
    
    scala> spark.sql("select * from people").show
    +-------+---+
    |   name|age|
    +-------+---+
    |Michael| 29|
    |   Andy| 30|
    | Justin| 19|
    +-------+---+
    scala> spark.newSession.sql("select * from people").show
    ## 出错
    
    scala> spark.newSession.sql("select * from global_temp.people").show
    +-------+---+
    |   name|age|
    +-------+---+
    |Michael| 29|
    |   Andy| 30|
    | Justin| 19|
    +-------+---+
    scala> spark.sql("select * from global_temp.people").show
    +-------+---+
    |   name|age|
    +-------+---+
    |Michael| 29|
    |   Andy| 30|
    | Justin| 19|
    +-------+---+
    
    ```


## 3. 自定义函数

### 3.1 UDF函数

需求： 在每一行数据的name列值的前面haha
: 
    ```shell
    scala> spark.sql("select * from people").show
    +-------+---+
    |   name|age|
    +-------+---+
    |Michael| 29|
    |   Andy| 30|
    | Justin| 19|
    +-------+---+
    
    scala> spark.udf.register("add",(x:String)=>"hehe"+x)
    res22: org.apache.spark.sql.expressions.UserDefinedFunction = UserDefinedFunction(<function1>,StringType,Some(List(StringType)))
    
    scala> spark.sql("select add(name),age from people").show
    +-------------+---+
    |UDF:add(name)|age|
    +-------------+---+
    |  heheMichael| 29|
    |     heheAndy| 30|
    |   heheJustin| 19|
    +-------------+---+
    ```


### 3.2 UDAF函数
需要通过继承 UserDefinedAggregateFunction 来实现自定义聚合函数。案例：计算一下员工的平均工资

弱类型聚合函数
```scala
package com.zhiyou100.spark

import org.apache.spark.SparkConf
import org.apache.spark.sql.{Row, SparkSession}
import org.apache.spark.sql.expressions.{MutableAggregationBuffer, UserDefinedAggregateFunction}
import org.apache.spark.sql.types._

/**
  * 弱类型的
  * 计算员工的平均薪资
  */
class AverageSalaryRuo extends UserDefinedAggregateFunction{
  //输入的数据的格式
  override def inputSchema: StructType = StructType(StructField("salary",IntegerType) :: Nil)
  //每个分区中共享的数据变量结构
  override def bufferSchema: StructType = StructType(StructField("sum",LongType) :: StructField("count",IntegerType):: Nil)
  //输出的数据的类型
  override def dataType: DataType = DoubleType
  //表示如果有相同的输入是否会存在相同的输出，是：true
  override def deterministic: Boolean = true
  //初始化的每个分区共享变量
  override def initialize(buffer: MutableAggregationBuffer): Unit = {
    buffer(0) = 0L
    buffer(1) = 0
  }
  //每一个分区的每一条数据聚合的时候进行buffer的更新
  override def update(buffer: MutableAggregationBuffer, input: Row): Unit = {
    //将buffer中的薪资总和的数据进行更新，原数据加上新输入的数据，buffer就类似于resultSet
    buffer(0) = buffer.getLong(0) + input.getInt(0)
    //每添加一个薪资，就将员工的个数加1
    buffer(1) = buffer.getInt(1)+1
  }
  //将每个分区的输出合并
  override def merge(buffer1: MutableAggregationBuffer, buffer2: Row): Unit = {
    buffer1(0) = buffer1.getLong(0) + buffer2.getLong(0)
    buffer1(1) = buffer1.getInt(1)+buffer2.getInt(1)
  }
  //获取最终的结果
  override def evaluate(buffer: Row): Any = {
    //计算平均薪资并返回
    buffer.getLong(0).toDouble/buffer.getInt(1)
  }
}
object AverageSalaryRuo extends App{
  val conf = new SparkConf().setAppName("udaf").setMaster("local[3]")
  val spark = SparkSession.builder().config(conf).getOrCreate()
  val data = spark.read.json("C:\\Users\\zhang\\Desktop\\employees.json")
  data.createOrReplaceTempView("employee")
  //注册自定义聚合函数
  spark.udf.register("avgSalary",new AverageSalaryRuo)
  spark.sql("select avgSalary(salary) from employee").show()
  spark.stop()
}


```

强类型聚合函数
: 
    ```scala
    package com.zhiyou100.spark
    
    import org.apache.spark.SparkConf
    import org.apache.spark.sql.expressions.Aggregator
    import org.apache.spark.sql.{Encoder, Encoders, SparkSession}
    
    /**
      * 弱类型的
      * 计算员工的平均薪资
      */
    
    //对于强类型来说，无非就是借助于样例类
    case class Employee(name:String,salary:Long)
    case class Average(var sum:Long,var count:Int)
    class AverageSalaryQiang extends Aggregator[Employee,Average,Double]{
      //初始化方法
      override def zero: Average = Average(0L,0)
      //一个分区内的聚合调用，类似于update方法
      override def reduce(b: Average, a: Employee): Average = {
        b.sum = b.sum + a.salary
        b.count = b.count + 1
        b
      }
    
      override def merge(b1: Average, b2: Average): Average = {
        b1.sum = b1.sum + b2.sum
        b1.count = b1.count + b2.count
        b1
      }
    
      //最终的计算结果
      override def finish(reduction: Average): Double = {
        reduction.sum.toDouble /reduction.count
      }
    
      //对buffer编码
      override def bufferEncoder: Encoder[Average] = Encoders.product
      //对out编码
      override def outputEncoder: Encoder[Double] = Encoders.scalaDouble
    }
    object AverageSalaryQiang extends App{
      val conf = new SparkConf().setAppName("udaf").setMaster("local[3]")
      val spark = SparkSession.builder().config(conf).getOrCreate()
      import  spark.implicits._
      val employee = spark.read.json("C:\\Users\\zhang\\Desktop\\employees.json").as[Employee]
      employee.show()
      employee.createOrReplaceTempView("employee")
      //注册自定义函数
      val aaa = new AverageSalaryQiang().toColumn.name("aaaa")
      spark.sql("select * from employee").show()
      //spark.sql("select aaaa(salary) from employee").show()
      employee.select(aaa).show()
      spark.stop()
    }
    
    
    ```

### 3.3 开窗函数

over（）开窗函数
: 

 - 在使用聚合函数后，会将多行变成一行，而开窗函数是将一行变成多行；
 - 并且在使用聚合函数后，如果要显示其他的列必须将列加入到group by中，而使用开窗函数后，可以不使用group by，直接将所有信息显示出来。
 - 开窗函数适用于在每一行的最后一列添加聚合函数的结果。

开窗函数作用
: 

 - 为每条数据显示聚合信息.(聚合函数() over())
 - 为每条数据提供分组的聚合函数结果(聚合函数() over(partition by 字段) as 别名) 
         --按照字段分组，分组后进行计算
 - 与排名函数一起使用(row number() over(order by 字段) as 别名)
 
常用分析函数：（最常用的应该是1.2.3 的排序）
: 
 
  - row_number() over(partition by ... order by ...)
  - rank() over(partition by ... order by ...)
  - dense_rank() over(partition by ... order by ...)
  - count() over(partition by ... order by ...)
  - max() over(partition by ... order by ...)
  - min() over(partition by ... order by ...)
  - sum() over(partition by ... order by ...)
  - avg() over(partition by ... order by ...)
  - first_value() over(partition by ... order by ...)
  - last_value() over(partition by ... order by ...)
  - lag() over(partition by ... order by ...)
  - lead() over(partition by ... order by ...)
lag 和lead 可以获取结果集中，按一定排序所排列的当前行的上下相邻若干offset 的某个行的某个列(不用结果集的自关联）；
lag ，lead 分别是向前，向后；
lag 和lead 有三个参数，第一个参数是列名，第二个参数是偏移的offset，第三个参数是超出记录窗口时的默认值

案例实战
: 
![image_1cmmpgfn07vudg5177i1sa51uq09.png-60.9kB][10]
```scala
数据格式
姓名    班级    分数


```

## 4. Spark SQL的数据源


### 4.1 与Hive集成

4.1.1 使用内置的Hive
: 

```shell
[root@master spark-2.2.2-bin-hadoop2.7]# /opt/apps/Spark/spark-2.2.2-bin-hadoop2.7/bin/spark-shell --master spark://master:7077 --total-executor-cores 3 --executor-memory 512m --conf spark.sql.warehouse.dir=hdfs://master:9000/spark/warehouse

scala> spark.sql("create table student(name String,age Int)")
scala> spark.sql("load data local inpath '/opt/test/spark/student' into table student")
scala> spark.sql("select * from student").show
## 只有在第一次操作的时候需要指定数据文件存放的目录，第二次不需要指定。

```


4.1.2 使用外置的Hive
:  

```shell
## 将Hive的配置文件放到spark 的conf目录
[root@master conf]# ln -s /opt/apps/Hive/hive-2.3.3/conf/hive-site.xml ./hive-site.xml
## 将Hive中的mysql的驱动jar包上传到spark中
[root@master lib]# cp mysql-connector-java-5.1.46-bin.jar /opt/apps/Spark/spark-2.2.2-bin-hadoop2.7/jars

```

- 启动spark-shell可以直接通过Hive的元数据信息进行Hive的管理
- Spark SQL其实就是整合了很多数据库的操作，在于Hive整合的时候，只要有了Hive的元数据信息，以及元数据存储的数据的驱动就可以直接进行管理


### **4.2 输入输出格式**
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Spark SQL的DataFrame接口支持多种数据源的操作。一个DataFrame可以进行RDDs方式的操作，也可以被注册为临时表。把DataFrame注册为临时表之后，就可以对该DataFrame执行SQL查询。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Spark SQL的默认数据源为Parquet格式。数据源为Parquet文件时，Spark SQL可以方便的执行所有的操作。修改配置项spark.sql.sources.default，可修改默认数据源格式。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;当数据源格式不是parquet格式文件时，需要手动指定数据源的格式。数据源格式需要指定全名（例如：org.apache.spark.sql.parquet），如果数据源格式为内置格式，则只需要指定简称定json, parquet, jdbc, orc, libsvm, csv, text来指定数据的格式。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;可以通过SparkSession提供的read.load方法用于通用加载数据，使用write和save保存数据。
[数据源][11]
```shell
scala> employee.write.format("jdbc").option("url","jdbc:mysql://master:3306/mysql_bigdata").option("user","root").option("password","123456").option("dbtable","employee").mode("overwrite").save()


```

[保存数据的方式][12]

### **4.3 与数据库交互**

将数据导入MySQL
:   
    ```shell
    scala> val employee = spark.read.json("/opt/apps/Spark/spark-2.2.2-bin-hadoop2.7/examples/src/main/resources/employees.json")
    employee: org.apache.spark.sql.DataFrame = [name: string, salary: bigint]
    
    scala> employee.show
    +-------+------+
    |   name|salary|
    +-------+------+
    |Michael|  3000|
    |   Andy|  4500|
    | Justin|  3500|
    |  Berta|  4000|
    +-------+------+
    
    
    scala> employee.write.format("jdbc").option("url","jdbc:mysql://master:3306/mysql_bigdata").option("user","root").option("password","123456").option("dbtable","employee").save()
    
    ```

读取MySQL中的数据
:   
    ```shell
    scala> spark.read.format("jdbc").option("url","jdbc:mysql://master:3306/mysql_bigdata").option("user","root").option("password","123456").option("dbtable","student").load().show
    
    ```

## 5. Spark SQL架构原理

### 5.1 Spark SQL 的模块
Spark SQL模块划分为Core、caralyst、hive和hive- ThriftServer四大模块。
![image_1cml86ltg16l2eio14m963b1q2rm.png-14.6kB][13]


### 5.2 Spark SQL的运行
![image_1cml8bq7t3go1moefiirs01uh41g.png-93.6kB][14]

- 使用SessionCatalog保存元数据
在解析SQL语句之前，会创建SparkSession，或者如果是2.0之前的版本初始化SQLContext，SparkSession只是封装了SparkContext和SQLContext的创建而已。会把元数据保存在SessionCatalog中，涉及到表名，字段名称和字段类型。创建临时表或者视图，其实就会往SessionCatalog注册
- 解析SQL,使用ANTLR生成未绑定的逻辑计划
当调用SparkSession的sql或者SQLContext的sql方法，我们以2.0为准，就会使用SparkSqlParser进行解析SQL. 使用的ANTLR进行词法解析和语法解析。它分为2个步骤来生成Unresolved LogicalPlan：词法分析：Lexical Analysis，负责将token分组成符号类，构建一个分析树或者语法树AST
- 使用分析器Analyzer绑定逻辑计划
在该阶段，Analyzer会使用Analyzer Rules，并结合SessionCatalog，对未绑定的逻辑计划进行解析，生成已绑定的逻辑计划。
- 使用优化器Optimizer优化逻辑计划
优化器也是会定义一套Rules，利用这些Rule对逻辑计划和Exepression进行迭代处理，从而使得树的节点进行和并和优化
- 使用SparkPlanner生成物理计划
SparkSpanner使用Planning Strategies，对优化后的逻辑计划进行转换，生成可以执行的物理计划SparkPlan.
- 使用QueryExecution执行物理计划
此时调用SparkPlan的execute方法，底层其实已经再触发JOB了，然后返回DataFrame

## **6. 案例实战**

### **6.1 数据说明**
数据集是货品交易数据集。
![image_1cml6v59u8p31c501bj97h31d859.png-64.1kB][15]
每个订单可能包含多个货品，每个订单可以产生多次交易，不同的货品有不同的单价。

### **6.2 需求**

- 统计所有订单中每年的销售单数、销售总额
- 统计每年最大金额订单的销售额
- 统计每年最畅销货品（哪个货品销售额amount在当年最高，哪个就是最畅销货品）


  [1]: http://static.zybuluo.com/zhangwen100/wz16d2sf49lv214honl95n1m/image_1cmio2ig3j22l44vdi31a1m509.png
  [2]: http://static.zybuluo.com/zhangwen100/f0ob8qrn64bk0eh6b69o40do/image_1cmioarhitcofuckg21mj819dc1m.png
  [3]: http://static.zybuluo.com/zhangwen100/s6w2ht1grmme7fxz0twt2k9m/image_1cmiobr9f16pf1s3s1bj6n3b9vl23.png
  [4]: http://static.zybuluo.com/zhangwen100/j6m690urvdi9ixikhet13v7p/image_1cmiocej31n931sm4qrsmgi1fad2g.png
  [5]: http://static.zybuluo.com/zhangwen100/7lkpy3h7gs64s1ahuy262rx1/image_1cmioct9e1c7519la14141tah1v722t.png
  [6]: http://static.zybuluo.com/zhangwen100/ugyb2nwgb5s13wnqt89z4n68/image_1cmiokv241engdid15k31kt72so9.png
  [7]: http://static.zybuluo.com/zhangwen100/6ppsc1z0yet35uma6hlwuz89/image_1cmioruf962jkms3t7uke10ahm.png
  [8]: http://static.zybuluo.com/zhangwen100/nkux489j4jvaj5fkgyf8vj6x/image_1cmip0l5k1ph1d5de5u11qo13eq13.png
  [9]: http://static.zybuluo.com/zhangwen100/uoiul4v1iubq2ab6lutths66/image_1cmipmbtrurtraot1m5tnmkr1g.png
  [10]: http://static.zybuluo.com/zhangwen100/gos4pjreefaejnehj7ylvoql/image_1cmmpgfn07vudg5177i1sa51uq09.png
  [11]: http://spark.apache.org/docs/2.2.2/sql-programming-guide.html#data-sources
  [12]: http://spark.apache.org/docs/2.2.2/sql-programming-guide.html#save-modes
  [13]: http://static.zybuluo.com/zhangwen100/euemtz5n6ouss6pxhz9en9w6/image_1cml86ltg16l2eio14m963b1q2rm.png
  [14]: http://static.zybuluo.com/zhangwen100/qo976u7wbxk8xgvmd8fcmgmo/image_1cml8bq7t3go1moefiirs01uh41g.png
  [15]: http://static.zybuluo.com/zhangwen100/sc0hnsjqtpbs940fnx9t7b2k/image_1cml6v59u8p31c501bj97h31d859.png