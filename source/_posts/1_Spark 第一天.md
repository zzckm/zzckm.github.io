---
title: 'Spark第一天'
date: 2019-04-25 23:13:40
tags: [Spark]
categories: Spark
---
# Spark第一天

## **1. Spark概述**

### **1.1 什么是Spark**

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Spark是一种快速、通用、可扩展的大数据分析引擎，2009年诞生于加州大学伯克利分校AMPLab，2010年开源，2013年6月成为Apache孵化项目，2014年2月成为Apache顶级项目。项目是用Scala进行编写。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;目前，Spark生态系统已经发展成为一个包含多个子项目的集合，其中包含SparkSQL、Spark Streaming、GraphX、MLLib、SparkR等子项目，Spark是基于内存计算的大数据并行计算框架。除了扩展了广泛使用的 MapReduce 计算模型，而且高效地支持更多计算模式，包括交互式查询和流处理。Spark 适用于各种各样原先需要多种不同的分布式平台的场景，包括批处理、迭代算法、交互式查询、流处理。通过在一个统一的框架下支持这些不同的计算，Spark 使我们可以简单而低耗地把各种处理流程整合在一起。而这样的组合，在实际的数据分析 过程中是很有意义的。不仅如此，Spark  的这种特性还大大减轻了原先需要对各种平台分别管理的负担。 
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;大一统的软件栈，各个组件关系密切并且可以相互调用，这种设计有几个好处：1、软件栈中所有的程序库和高级组件 都可以从下层的改进中获益。2、运行整个软件栈的代价变小了。不需要运 行 5 到 10 套独立的软件系统了，一个机构只需要运行一套软件系统即可。系统的部署、维护、测试、支持等大大缩减。3、能够构建出无缝整合不同处理模型的应用。

### **1.2 Hadoop与Spark的区别**
![image_1cmdes1api2akv1194gsr0hnj9.png-110kB][1]

### **1.3 Spark 主要角色**

![image_1cmdevnb21s9kbj0rss1ae9js0m.png-59.9kB][2]

**Spark Core：**实现了 Spark 的基本功能，包含任务调度、内存管理、错误恢复、与存储系统 交互等模块。Spark Core 中还包含了对弹性分布式数据集(resilient distributed dataset，简称RDD)的 API 定义。 
**Spark SQL：**是 Spark 用来操作结构化数据的程序包。通过 Spark SQL，我们可以使用 SQL 或者 Apache Hive 版本的 SQL 方言(HQL)来查询数据。Spark SQL 支持多种数据源，比 如 Hive 表、Parquet 以及 JSON 等。 
**Spark Streaming：**是 Spark 提供的对实时数据进行流式计算的组件。提供了用来操作数据流的 API，并且与 Spark Core 中的 RDD API 高度对应。 
**Spark MLlib：**提供常见的机器学习(ML)功能的程序库。包括分类、回归、聚类、协同过滤等，还提供了模型评估、数据 导入等额外的支持功能。 
**集群管理器：** Spark 设计为可以高效地在一个计算节点到数千个计算节点之间伸缩计 算。为了实现这样的要求，同时获得最大灵活性，Spark 支持在各种集群管理器(cluster manager)上运行，包括 Hadoop YARN、Apache Mesos，以及 Spark 自带的一个简易调度 器，叫作独立调度器。

### **1.4 Spark特点**

快
: 与Hadoop的MapReduce相比，Spark基于内存的运算要快100倍以上，基于硬盘的运算也要快10倍以上。Spark实现了高效的DAG执行引擎，可以通过基于内存来高效处理数据流。计算的中间结果是存在于内存中的。

易用
: Spark支持Java、Python和Scala的API，还支持超过80种高级算法，使用户可以快速构建不同的应用。而且Spark支持交互式的Python和Scala的shell，可以非常方便地在这些shell中使用Spark集群来验证解决问题的方法。

通用
: Spark提供了统一的解决方案。Spark可以用于批处理、交互式查询（Spark SQL）、实时流处理（Spark Streaming）、机器学习（Spark MLlib）和图计算（GraphX）。这些不同类型的处理都可以在同一个应用中无缝使用。Spark统一的解决方案非常具有吸引力，毕竟任何公司都想用统一的平台去处理遇到的问题，减少开发和维护的人力成本和部署平台的物力成本。

兼容性
: Spark可以非常方便地与其他的开源产品进行融合。比如，Spark可以使用Hadoop的YARN和Apache Mesos作为它的资源管理和调度器，器，并且可以处理所有Hadoop支持的数据，包括HDFS、HBase和Cassandra等。这对于已经部署Hadoop集群的用户特别重要，因为不需要做任何数据迁移就可以使用Spark的强大处理能力。Spark也可以不依赖于第三方的资源管理和调度器，它实现了Standalone作为其内置的资源管理和调度框架，这样进一步降低了Spark的使用门槛，使得所有人都可以非常容易地部署和使用Spark。此外，Spark还提供了在EC2上部署Standalone的Spark集群的工具。




## **2. 集群搭建**

### **2.1 集群角色**

两个Master（类似于Hadoop中的yarn ，ResourceManager），多个worker

### **2.2 安装步骤**

**2.2.1 解压**
: 
    ```shell
    [root@master Spark]# tar -zxvf spark-2.2.2-bin-hadoop2.7.tgz
    
    ```

**2.2.2 修改文件名**
: 
    ```shell
    [root@master conf]# mv spark-env.sh.template spark-env.sh
    [root@master conf]# mv slaves.template slaves
    
    ```

**2.2.3 添加配置信息**
: 
    ```shell
    [root@master conf]# vim spark-env.sh
    SPARK_MASTER_HOST=master
    SPARK_MASTER_PORT=7077
    [root@master conf]# vim slaves
    slave1
    slave2
    slave3
    
    ```

**2.2.4 HA配置**
: 
    ```shell
    [root@master conf]# vim spark-env.sh
    export SPARK_DAEMON_JAVA_OPTS="-Dspark.deploy.recoveryMode=ZOOKEEPER -Dspark.deploy.zookeeper.url=master:2181,slave1:2181,slave2,slave3 -Dspark.deploy.zookeeper.dir=/spark"
    
    ```

**2.2.5 启动集群**
: 
```shell
## 在master节点上启动start-all.sh


## 在slave1节点上使用start-master.sh

```



## **3. 执行Spark程序**

```shell
[root@master bin]# ./spark-submit \
--master spark://master:7077,slave1:7077 \ 
--class org.apache.spark.examples.SparkPi \ /opt/apps/Spark/spark-2.2.2-bin-hadoop2.7/examples/jars/spark-examples_2.11-2.2.2.jar  10000

```

## **4. Spark Shell**



```shell
[root@master ~]# /opt/apps/Spark/spark-2.2.2-bin-hadoop2.7/bin/spark-shell
## 在不指定集群的时候（master），可以正常启动shell，但是这种模式只是启动了本地模式。没有与集群建立连接

scala> sc.textFile("hdfs://master:9000/wc.txt").flatMap(_.split(" ")).map((_,1)).reduceByKey(_+_).saveAsTextFile("hdfs://master:9000/out2")

```

在Shell中可以直接编写Spark代码。SparkContext类会默认初始化为sc对象。



## **5. 本地模式执行SPark程序**



  ![1](http://static.zybuluo.com/zhangwen100/e3f7343atttccfyebj5j33rp/image_1cmdes1api2akv1194gsr0hnj9.png)
  ![2](http://static.zybuluo.com/zhangwen100/9jubl5d55y5010bq746nlbyj/image_1cmdevnb21s9kbj0rss1ae9js0m.png)