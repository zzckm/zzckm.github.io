---
title: 'Hadoop-Mapreduce'
date: 2019-04-25 23:00:48
tags: [Hadoop,Mapreduce]
categories: Hadoop
---
# Hadoop-Mapreduce

## 1. MapReduce入门

1.1 MapReduce 定义
: Mapreduce 是一个分布式运算程序的编程框架，是用户开发“基于 hadoop 的数据分析应用”的核心框架。
Mapreduce 核心功能是将用户编写的业务逻辑代码和自带默认组件整合成一个完整的分布式运算程序，并发运行在一个 hadoop 集群上。

1.2 MapReduce 优缺点
: 

 - **优点**
        - MapReduce 易于编程。它简单的实现一些接口，就可以完成一个分布式程序，这个分布式程序可以分布到大量廉价的 PC 机器上运行。也就是说你写一个分布式程序，跟写一个简单的串行程序是一模一样的。就是因为这个特点使得 MapReduce 编程变得非常流行。
        - 良好的 扩展性。当你的计算资源不能得到满足的时候，你可以通过简单的增加机器来扩展它的计算能力。
        - 高容错性。MapReduce 设计的初衷就是使程序能够部署在廉价的 PC 机器上，这就要求它具有很高的容错性。比如其中一台机器挂了，它可以把上面的计算任务转移到另外一个节点上运行，不至于这个任务运行失败，而且这个过程不需要人工参与，而完全是由Hadoop 内部完成的。
        - 适合 PB 级以上海量数据的离线处理。它适合离线处理而不适合在线处理。比如像毫秒级别的返回一个结果, MapReduce 很难做到。
- **缺点**
MapReduce 不擅长做实时计算、流式计算、DAG（有向图 ）计算。
        - 实时计算。MapReduce 无法像 Mysql 一样，在毫秒或者秒级内返回结果。
        - 流式计算。流式计算的输入数据是动态的，而 MapReduce 的输入数据集是静态的，不能动态变化。这是因为 MapReduce 自身的设计特点决定了数据源必须是静态的。
        - DAG （有向图）计算。多个应用程序存在依赖关系，后一个应用程序的输入为前一个的输出。在这种情况下，MapReduce 并不是不能做，而是使用后，每个 MapReduce 作业的输出结果都会写入到磁盘，会造成大量的磁盘 IO，导致性能非常的低下。
    
## 2. MapReduce的编程思想

图解
: 
![image_1cjoi1fe0phatn11dm8188il049.png-126.6kB][1]

简单说明
: 
 
 - 分布式的运算程序往往需要分成至少 2 个阶段。
 - 第一个阶段的 maptask 并发实例，完全并行运行，互不相干。
 - 第二个阶段的 reduce task 并发实例互不相干，但是他们的数据依赖于上一个阶段的所有 maptask 并发实例的输出。
 - MapReduce 编程模型只能包含一个 map 阶段和一个 reduce 阶段，如果用户的业务逻辑非常复杂，那就只能多个 mapreduce 程序，串行运行。


MapReduce的进程
: 一个完整的 mapreduce 程序在分布式运行时有三类实例进程

    - MrAppMaster：负责整个程序的过程调度及状态协调。
    - MapTask：负责 map 阶段的整个数据处理流程。
    - ReduceTask：负责 reduce 阶段的整个数据处理流程。

MapReduce 编程规范
: 用户编写的程序分成三个部分：Mapper，Reducer，Driver(提交运行 mr 程序的客户端)

 - Mapper 阶段
     - 用户自定义的 Mapper 要继承自己的父类
     - Mapper 的输入数据是 KV 对的形式（KV 的类型可自定义）
     - Mapper 中的业务逻辑写在 map()方法中
     - Mapper 的输出数据是 KV 对的形式（KV 的类型可自定义）
     - map()方法（maptask 进程）对每一个<K,V>调用一次
- Reducer 阶段
        - 用户自定义的 Reducer 要继承自己的父类
        - Reducer 的输入数据类型对应 Mapper 的输出数据类型，也是 KV
        - Reducer 的业务逻辑写在 reduce()方法中
        - Reducetask 进程对每一组相同 k 的<k,v>组调用一次 reduce()方法
- Driver 阶段
整个程序需要一个 Drvier 来进行提交，提交的是一个描述了各种必要信息的 job 对象

案例分析
: 统计单词的个数

 - 案例1
 ```java
     package com.zhiyou100.hadoop.mr.wordcount;
    import java.io.IOException;
    import org.apache.hadoop.io.IntWritable;
    import org.apache.hadoop.io.LongWritable;
    import org.apache.hadoop.io.Text;
    import org.apache.hadoop.mapreduce.Mapper;
    /**
     * KEYIN: 这个是Map读取到的一行文本的起始偏移量，Long。
     * 		  在hadoop中，已经有了自己的更简单的序列化接口，所以使用LongWritable类
     * VALUEIN：默认情况下， Map读取到的一行文本数据，类型是：Text，其实就是String
     *
     * KEYOUT：根据map中的业务逻辑运算的结果的输出的Key，Text类型，其实就是一个一个的单词
     * 
     * VALUEOUT：完成后输出的value的数据类型，int。在Hadoop中应该是IntWritable
     * @author root 
     *
     */
    public class WordCountMapper extends Mapper<LongWritable, Text, Text, IntWritable>{
    	
    	/**
    	 * 输入：0，zhangsan	lisi	haha
    	 */
    	@Override
    	protected void map(LongWritable key, Text value, Mapper<LongWritable, Text, Text, IntWritable>.Context context)
    			throws IOException, InterruptedException {
    		//打印Key到控制台
    		//System.out.println(key);
    		//将value转换成字符串
    		String line = value.toString();
    		//根据\t进行切分
    		String[] words = line.split("\t");
    		//输出指定的单词，并将单词出现的次数标记为1
    		for(String word:words) {
    			//将单词传递出去：zhangsan，1        lisi，1         haha，1
    			context.write(new Text(word), new IntWritable(1));
    		}
    	}
    }
    package com.zhiyou100.hadoop.mr.wordcount;
    import java.io.IOException;
    import org.apache.hadoop.io.IntWritable;
    import org.apache.hadoop.io.Text;
    import org.apache.hadoop.mapreduce.Reducer;
    /**
     * KEYIN： ValueIN：对应的是Mapper的输出的Key与value
     */
    public class WordCountReducer extends Reducer<Text, IntWritable, Text, IntWritable>{
    	@Override
    	protected void reduce(Text key, Iterable<IntWritable> values,
    			Reducer<Text, IntWritable, Text, IntWritable>.Context context) throws IOException, InterruptedException {
    		int count = 0;
    		for(IntWritable value:values) {
    			count+=value.get();
    		}
    		context.write(key, new IntWritable(count));
    	}
    }
    package com.zhiyou100.hadoop.mr.wordcount;


    import java.net.URI;
    
    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.fs.FileSystem;
    import org.apache.hadoop.fs.Path;
    import org.apache.hadoop.io.IntWritable;
    import org.apache.hadoop.io.Text;
    import org.apache.hadoop.mapreduce.Job;
    import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
    import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
    
    public class WordCount {
    	
    	public static void main(String[] args) throws Exception {
    		//1. 获取配置信息
    		Configuration conf = new Configuration();
    		
    		//2. 设置额外的配置
    		//2.1 Permission denied: user=zhang, access=WRITE, inode="/wc":root:supergroup:drwxr-xr-x
    		//没用：conf.set("HADOOP_USER_NAME", "root");
    		System.setProperty("HADOOP_USER_NAME", "root");
    		
    		FileSystem fs = FileSystem.get(new URI("hdfs://master:9000"), conf, "root");
    		
    		//2.2 Exception message: /bin/bash: 第 0 行:fg: 无任务控制
    		//    设置跨平台
    		conf.set("mapreduce.app-submission.cross-platform", "true");
    		//2.3 Error: Java heap space
    		conf.set("mapred.child.java.opts", "-Xmx512m");
    		
    		//3. 创建Job对象
    		Job job = Job.getInstance(conf, "wc");
    		
    		//4. 指定job的主程序
    		job.setJarByClass(WordCount.class);
    		//4.1 java.lang.ClassNotFoundException: Class com.zhiyou100.hadoop.mr.wordcount.WordCountMapper not found
    		job.setJar("F:\\work\\mr\\wc.jar");
    		//5. 指定Job的Map和Reduce业务类
    		job.setMapperClass(WordCountMapper.class);
    		job.setReducerClass(WordCountReducer.class);
    		
    		//6. 指定Job工作的数据类型
    		job.setMapOutputKeyClass(Text.class);
    		job.setMapOutputValueClass(IntWritable.class);
    		job.setOutputKeyClass(Text.class);
    		job.setOutputValueClass(IntWritable.class);
    		
    		//7. 指定Job的输入及输出目录
    		FileInputFormat.setInputPaths(job, new Path("hdfs://master:9000/wc/input/"));
    		Path outPath = new Path("hdfs://master:9000/wc/output/");
    		if(fs.exists(outPath)) {
    			fs.delete(outPath, true);
    		}
    		FileOutputFormat.setOutputPath(job, outPath);
    		
    		//8. 提交Job
    		System.out.println(job.waitForCompletion(true)?0:1);;
    	}
    
    }



 ```
 
## **2. MapReduce自定义数据类型及排序**

2.1 如何实现自定义数据类型
: &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;其实就是自定义的 Key，value 类型，说白了就是自定义一个 Java 类。那么在 Hadoop 中，自定义的数据类型必须实现 Hadoop 指定的序列化接口。


2.2 什么是序列化
: &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**序列化**就是把内存中的对象，转换成字节序列（或其他数据传输协议）以便于存储（持久化）和网络传输。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**反序列化**就是将收到字节序列（或其他数据传输协议）或者是硬盘的持久化数据，转换成内存中的对象。

2.3 为什么要序列化
: &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;一般来说，“活的”对象只生存在内存里，关机断电就没有了。而且“活的”对象只能由本地的进程使用，不能被发送到网络上的另外一台计算机。然而序列化可以存储“活的” 对象，可以将“活的”对象发送到远程计算机。

2.4 为什么不用 Java 的序列化
: &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Java 的序列化是一个重量级序列化框架（Serializable），一个对象被序列化后，会附带很多额外的信息（各种校验信息，header，继承体系等），不便于在网络中高效传输。所以，hadoop 自己开发了一套序列化机制（Writable），精简、高效。

2.5 为什么序列化对 Hadoop 很重要
: &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;因为 Hadoop 在集群之间进行通讯或者 RPC 调用的时候，需要序列化，而且要求序列化要快，且体积要小，占用带宽要小。所以必须理解 Hadoop 的序列化机制。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;序列化和反序列化在分布式数据处理领域经常出现：进程通信和永久存储。然而 Hadoop 中各个节点的通信是通过远程调用（RPC）实现的，那么 RPC 序列化要求具有以下特点：

    - 紧凑：紧凑的格式能让我们充分利用网络带宽，而带宽是数据中心最稀缺的资源
    - 快速：进程通信形成了分布式系统的骨架，所以需要尽量减少序列化和反序列化的性能开销，这是基本的
    - 可扩展：协议为了满足新的需求变化，所以控制客户端和服务器过程中，需要直接引进相应的协议，这些是新协议，原序列化方式能支持新的协议报文
    - 互操作：能支持不同语言写的客户端和服务端进行交互
    
2.6 Hadoop常用序列化数据类型与Java中类型对比
: 
  |Java 类型|Hadoop Writable 类型|
  |---|---|
  |boolean|BooleanWritable|
  |byte|ByteWritable|
  |int|IntWritable|
  |float|FloatWritable|
  |long|LongWritable|
  |double|DoubleWritable|
  |string|Text|
  |map|MapWritable|
  |array|ArrayWritable|

2.7 自定义数据类型步骤
: 

 - 必须实现 Writable 接口
 - 反序列化时，需要反射调用空参构造函数，所以必须有空参构造
 - 重写序列化方法 —— write
 - 重写反序列化方法 —— readFields
 - 注意反序列化的顺序和序列化的顺序完全一致
 - 如果需要将自定义的 bean 放在 key 中传输，则还需要实现 comparable 接口，因为 mapreduce 框中的 shuffle 过程一定会对 key 进行排序。

2.8 案例实战
: 
**2.8.1 案例需求**
统计每一个手机号耗费的总上行流量、下行流量、总流量
**2.8.2 案例输入格式**
    ```shell
    1533136934908	13101780174	0a:70:ce:39:11:9f:2G	139.210.235.34	79	21	5883	42147	200
    
    ```
    > 时间戳 手机号码 Mac地址 IP地址 上传包 下载包 上传流量 下载流量 状态码
    
 **2.8.3 案例输出格式**
    ```shell
    13101780174 5883    42147   99999
    手机号      上行总流量  下行总流量  总流量
    
    ```
 **2.8.4 功能分析**
 
 **2.8.5 案例代码**
 ```java
 
 ```
 
## **3. MapReduce架构原理**

**3.1 基本原理**
: 看PPT

3.2 执行流程
: 使用断点执行
waitForCompletion()
进入Job中的1308行submit();
然后Job中的1282行 ensureState(JobState.DEFINE);判断集群的状态
然后Job中的1283行setUseNewAPI();属于新旧API的转换
然后Job中的1284行connect();建立连接
然后进入Cluster.class 82行 initialize(jobTrackAddr, conf);
然后进入到这个方法中，在100行打断点查看clientProtocol为LocalJobRunner
然后进入Job中的1290行
然后进入到JobSubmiter 中的257行 checkSpecs(Job job) 
查看JobConf jConf
然后进入到JobSubmiter 190行 copyAndConfigureFiles(job, submitJobDir);

3.3 切片的具体设置
: 3.3.1 FileInputFormat切片设置

    ```java
    long minSize = Math.max(getFormatMinSplitSize(), getMinSplitSize(job));
    long maxSize = getMaxSplitSize(job);
    mapreduce.input.fileinputformat.split.minsize
    mapreduce.input.fileinputformat.split.maxsize
    long splitSize = computeSplitSize(blockSize, minSize, maxSize);
    protected long computeSplitSize(long blockSize, long minSize,
                                  long maxSize) {
    return Math.max(minSize, Math.min(maxSize, blockSize));
  }
  long bytesRemaining = length;
          while (((double) bytesRemaining)/splitSize > SPLIT_SLOP) {
            int blkIndex = getBlockIndex(blkLocations, length-bytesRemaining);
            splits.add(makeSplit(path, length-bytesRemaining, splitSize,
                        blkLocations[blkIndex].getHosts(),
                        blkLocations[blkIndex].getCachedHosts()));
            bytesRemaining -= splitSize;
          }

          if (bytesRemaining != 0) {
            int blkIndex = getBlockIndex(blkLocations, length-bytesRemaining);
            splits.add(makeSplit(path, length-bytesRemaining, bytesRemaining,
                       blkLocations[blkIndex].getHosts(),
                       blkLocations[blkIndex].getCachedHosts()));
          }
    ```
 
 3.3.2 CombineTextInputFormat切片设置
 :    下周讲
 
 3.3.3 ReduceTask工作机制
 :  
 
 
 
 
   1. 一个MapTask或者一个切片对应于一个Mapper对象
   2. 一个切片可能对应于多个MapTask（如果某一个MapTask没有执行完成就失败了，那么该切片会重新被分配Map执行）
   3. 一个ReduceTask对应于一个输出文件，对应于一个Reducer对象，一个Reducer对象的：setup方法（一次），reduce方法（多次，根据分组的数量来确定），cleanup方法（一次）
   4. 分区数可以完全自定义，但是注意以下规则：
        - 如果reduce个数为1，则分区数无所谓，但是分区的编号返回必须为0,1,2,3,4,5
        - 如果reduce个数不为1，那么分区数必须小于等于reduce的个数
        - 如果分区数大于reduce的个数，而reduce个数不为1，那么就会出现异常
        - reduce个数大于分区的话，会出现一些空文件
    5. MapTask的并行度由谁决定？
    客户端决定——>切片数——>Map数
    Map数>=切片数。
    6. ReduceTask的并行度由谁决定？
   设置ReduceTask任务数
   job.setNumReduceTasks(3)
   注意：
    ReduceTask = 0，表示没有Reduce阶段，那么输出的文件内容为map的输出
    ReduceTask的默认值为1，因此默认只有一个文件输出
    如果数据分布不均匀，那么会出现什么问题——数据倾斜
    ReduceTask的数量也不能随意的设置：节点数，业务需求
    如果分区数不是1，但是reduce数目为1，这个时候分区只有一个。
    如果分区数>reduce数目，但是reduce不为1，会出现下面的错误
    Error: java.io.IOException: Illegal partition for 
    如果分区数=reduce数目，没有任何问题
     如果分区数&lt;reduce数目,则后续的几个文件中的数据为空
     
3.4 Combine过程
: 

 - combiner 是 MR 程序中 Mapper 和 Reducer 之外的一种组件。
 - combiner 组件的父类就是 Reducer。
 - combiner 和 reducer 的区别在于运行的位置：Combiner 是在每一个 maptask 所在的节点运行;Reducer 是接收全局所有 Mapper 的输出结果；
 - combiner 的意义就是对每一个 maptask 的输出进行局部汇总，以减小网络传输量。
 - combiner 能够应用的前提是不能影响最终的业务逻辑，而且，combiner 的输出 kv 应该跟 reducer 的输入 kv 类型要对应起来。
 

3.5 分组
: 对 reduce 阶段的数据根据某一个或几个字段进行分组。
**3.5.1 案例**
    现在需要求出每一个订单中最贵的商品。
    |订单id| 商品 id| 成交金额|
    |---|---|---|
    |0000001| Pdt_01 |222.8|
    |0000001| Pdt_06 |25.8|
    |0000002| Pdt_03 |522.8|
    |0000002| Pdt_04 |122.4|
    |0000002| Pdt_05 |722.4|
    |0000003| Pdt_01| 222.8|
    |0000003| Pdt_02 |33.8|
    
    文件1：1   222.8
    文件2：2   722.8
    文件3：3   222.8
    
3.6 WritableComparable 排序
: &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**排序**是 MapReduce 框架中最重要的操作之一。Map Task 和 Reduce Task 均会对数据（按照 key）进行排序。该操作属于 Hadoop 的默认行为。任何应用程序中的数据均会被排序，而不管逻辑上是否需要。**默认排序是按照字典顺序排序，且实现该排序的方法是快速排序**。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;对于 Map Task，它会将处理的结果暂时放到一个缓冲区中，当缓冲区使用率达到一定阈值后，再对缓冲区中的数据进行一次排序，并将这些有序数据写到磁盘上，而当数据处理完毕后，它会对磁盘上所有文件进行一次合并，以将这些文件合并成一个大的有序文件。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;对于 Reduce Task，它从每个 Map Task 上远程拷贝相应的数据文件，如果文件大小超过一定阈值，则放到磁盘上，否则放到内存中。如果磁盘上文件数目达到一定阈值，则进行一次合并以生成一个更大文件；如果内存中文件大小或者数目超过一定阈值，则进行一次合并后将数据写到磁盘上。当所有数据拷贝完毕后，Reduce Task 统一对内存和磁盘上的所有数据进行一次合并。
排序的分类：

    - 部分排序：
MapReduce 根据输入记录的键对数据集排序。保证输出的每个文件内部排序。
    - 全排序
    如何用 Hadoop 产生一个全局排序的文件？最简单的方法是使用一个分区。但该方法在处理大型文件时效率极低，因为一台机器必须处理所有输出文件，从而完全丧失了 MapReduce 所提供的并行架构。替代方案：首先创建一系列排好序的文件；其次，串联这些文件；最后，生成一个全局排序的文件。主要思路是使用一个分区来描述输出的全局排序。例如：可以为上述文件创建 3 个分区，在第一分区中，记录的单词首字母 a-g，第二分区记录单词首字母 h-n, 第三分区记录单词首字母 o-z。
    - 辅助排序：（GroupingComparator 分组）
    Mapreduce 框架在记录到达 reducer 之前按键对记录排序，但键所对应的值并没有被排序。甚至在不同的执行轮次中，这些值的排序也不固定，因为它们来自不同的 map 任务且这些 map 任务在不同轮次中完成时间各不相同。一般来说，大多数 MapReduce 程序会避免让 reduce 函数依赖于值的排序。但是，有时也需要通过特定的方法对键进行排序和分组等以实现对值的排序。
    - 二次排序：
在自定义排序过程中，如果 compareTo 中的判断条件为两个即为二次排序。

3.7 Shuffle机制
: Mapreduce 确保每个 reducer 的输入都是按键排序的。系统执行排序的过程（即将 map 输出作为输入传给 reducer）称为 shuffle。
## 4. 自定义输入输出格式
4.1 案例1：出现故障和警告出现次数
:   

4.2 案例2
:  自定义输出类型，把输出类型按照日期，输出到不同的日志文档里面，把同一个月的写在同一个文件夹下，同一天的写在同一个文件中

作业： 去重，TopK，自动读取K V值


## **5. MapReduce高级操作**

## **5.1 Join操作**
Reduce join
:  

 - 原理
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Map 端的主要工作：为来自不同表(文件)的 key/value 对打标签以区别不同来源的记录。然后用连接字段作为 key，其余部分和新加的标志作为 value，最后进行输出。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Reduce 端的主要工作：在 reduce 端以连接字段作为 key 的分组已经完成，我们只需要在每一个分组当中将那些来源于不同文件的记录(在 map 阶段已经打标志)分开，最后进行合并就 ok 了。
- 该方法的缺点
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这种方式的缺点很明显就是会造成 map 和 reduce 端也就是 shuffle 阶段出现大量的数据传输，**效率很低**。



Map join (Distributedcache 分布式缓存)
: 

 - 使用场景：一张表十分小、一张表很大。
 - 解决方案
在 map 端缓存多张表，提前处理业务逻辑，这样增加 map 端业务，减少 reduce 端数据的压力，尽可能的减少数据倾斜。
- 具体办法：采用 distributedcache
        - 在 mapper 的 setup 阶段，将文件读取到缓存集合中。
        - 在驱动函数中加载缓存。


## **5.2 MR串联**
一个稍复杂点的处理逻辑往往需要多个mapreduce程序串联处理，多job的串联可以借助mapreduce框架的JobControl实现

## **5.3 倒排索引**



## **5.4 计数器应用**
在实际生产代码中，常常需要将数据处理过程中遇到的不合规数据行进行全局计数，类似这种需求可以借助mapreduce框架中提供的全局计数器来实现

## **5.5 Hadoop 数据压缩**

5.5.1 概述
: &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;压缩技术能够有效减少底层存储系统（HDFS）读写字节数。压缩提高了网络带宽和磁盘空间的效率。在 Hadoop 下，尤其是数据规模很大和工作负载密集的情况下，使用数据压缩显得非常重要。在这种情况下，I/O 操作和网络数据传输要花大量的时间。还有，Shuffle 与 Merge 过程同样也面临着巨大的 I/O 压力。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;鉴于磁盘 I/O 和网络带宽是 Hadoop 的宝贵资源，数据压缩对于节省资源、最小化磁盘 I/O 和网络传输非常有帮助。不过，尽管压缩与解压操作的 CPU 开销不高，其性能的提升和资源的节省并非没有代价。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;如果磁盘 I/O 和网络带宽影响了 MapReduce 作业性能，在任意 MapReduce 阶段启用压缩都可以改善端到端处理时间并减少 I/O 和网络流量。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;压缩 Mapreduce 的一种优化策略：通过压缩编码对 Mapper 或者 Reducer 的输出进行压缩，以减少磁盘 IO ，提高 MR 程序运行速度（但相应增加了 cpu 运算负担）。
**注意：**压缩特性运用得当能提高性能，但运用不当也可能降低性能。
**基本原则**
 
 - 运算密集型的 job，少用压缩
 - IO 密集型的 job，多用压缩

5.5.2 MR 支持的压缩编码
: 
  |压缩格式| hadoop 自带？| 算法 |文件扩展名 |是否可切分|换成压缩格式后，原来的程序是否需要修改|
  |---|---|---|---|---|---|
  |DEFAULT|是，直接使用|DEFAULT|.deflate|否|和文本处理一样，不需要修改|
  |Gzip|是，直接使用|DEFAULT|.gz|否|和文本处理一样，不需要修改|
  |bzip2|是，直接使用|bzip2|.bz2|是|和文本处理一样，不需要修改|
  |LZO|否，需要安装|LZO|.lzo|是|需要建索引，还需要指定输入格式|
  |Snappy|否，需要安装|Snappy|.snappy|否|和文本处理一样，不需要修改|
 为了支持多种压缩/解压缩算法，Hadoop 引入了编码/解码器，如下表所示
  |压缩格式|对应的编码/解码器|
  |---|---|
  |DEFLATE|org.apache.hadoop.io.compress.DefaultCodec|
  |gzip|org.apache.hadoop.io.compress.GzipCodec|
  |bzip2|org.apache.hadoop.io.compress.BZip2Codec|
  |LZO|com.hadoop.compression.lzo.LzopCodec|
  |Snappy|org.apache.hadoop.io.compress.SnappyCodec|
  压缩性能的比较
  |压缩算法|原始文件大小|压缩文件大小|压缩速度|解压速度|
  |---|---|---|---|---|
  |gzip|8.3GB| 1.8GB| 17.5MB/s| 58MB/s|
  |bzip2| 8.3GB| 1.1GB| 2.4MB/s |9.5MB/s|
  |LZO| 8.3GB |2.9GB| 49.3MB/s |74.6MB/s|
  ![image_1ck7vuneq13ultn7q6k8ud1kqq9.png-224.1kB][2]
    
5.5.3  压缩方式的选择
: 
 
  - gzip 压缩
  **优点：**压缩率比较高，而且压缩/解压速度也比较快；hadoop 本身支持，在应用中处理 gzip 格式的文件就和直接处理文本一样；大部分 linux 系统都自带 gzip 命令，使用方便。
**缺点：**不支持 split。
**应用场景：**当每个文件压缩之后在 130M 以内的（1 个块大小内），都可以考虑用 gzip压缩格式。例如说一天或者一个小时的日志压缩成一个 gzip 文件，运行 mapreduce 程序的时候通过多个 gzip 文件达到并发。hive 程序，streaming 程序，和 java 写的 mapreduce 程序完全和文本处理一样，压缩之后原来的程序不需要做任何修改。
  - bzip2 压缩
  **优点：**支持 split；具有很高的压缩率，比 gzip 压缩率都高；hadoop 本身支持，但不支持 native；在 linux 系统下自带 bzip2 命令，使用方便。
**缺点：**压缩/解压速度慢；不支持 native。
**应用场景：**适合对速度要求不高，但需要较高的压缩率的时候，可以作为 mapreduce 作业的输出格式；或者输出之后的数据比较大，处理之后的数据需要压缩存档减少磁盘空间并且以后数据用得比较少的情况；或者对单个很大的文本文件想压缩减少存储空间，同时又需要支持 split，而且兼容之前的应用程序（即应用程序不需要修改）的情况。
  - LZO 压缩
  **优点：**压缩/解压速度也比较快，合理的压缩率；支持 split，是 hadoop 中最流行的压缩
**格式：**可以在 linux 系统下安装 lzop 命令，使用方便。
**缺点：**压缩率比 gzip 要低一些；hadoop 本身不支持，需要安装；在应用中对 lzo 格式的文件需要做一些特殊处理（为了支持 split 需要建索引，还需要指定 inputformat 为 lzo 格式）。
**应用场景：**一个很大的文本文件，压缩之后还大于 200M 以上的可以考虑，而且单个文件越大，lzo 优点越越明显。
  - snappy 压缩
  **优点：**高速压缩速度和合理的压缩率。
**缺点：**不支持 split；压缩率比 gzip 要低；hadoop 本身不支持，需要安装；
**应用场景：**当 Mapreduce 作业的 Map 输出的数据比较大的时候，作为 Map 到 Reduce 的中间数据的压缩格式；或者作为一个 Mapreduce 作业的输出和另外一个 Mapreduce 作业的输入。

5.5.4 压缩位置选择
: 压缩可以在 MapReduce 作用的任意阶段启用。

 - **输入端采用压缩**
 在有大量数据并计划重复处理的情况下，应该考虑对输入进行压缩。然而，你无须显示指定 使 用 的 编 解 码 方 式 。**Hadoop自动检查文件扩展名，如果扩展名能够匹配，就会用恰当的编解码方式对文件进行压缩和解压。**否则，Hadoop就不会使用任何编解码器。
 - **mapper 输出采用压缩**
 当map任务输出的中间数据量很大时，应考虑在此阶段采用压缩技术。这能显著改善内部数据 Shuffle 过程 ， 而 Shuffle 过程在 Hadoop 处理过程中是资源消耗最多的环节。**如果发现数据量大造成网络传输缓慢，应该考虑使用压缩技术。**可用于压缩mapper输出的快速编解码器包括LZO或者Snappy。
**注：** LZO是供Hadoop压缩数据用的通用压缩编解码器。其设计目标是达到与硬盘读取速度相当的压缩速度，因此速度是优先考虑的因素，而不是压缩率。与gzip编解码器相比，它的压缩速度是gzip的5倍，而解压速度是gzip的2倍。同一个文件用LZO压缩后比用gzip压缩后大50%，但比压缩前小25%~50%。这对改善性能非常有利，map阶段完成时间快4倍。
 - **reducer 输出采用压缩**
 在此阶段启用压缩技术**能够减少要存储的数据量，因此降低所需的磁盘空间。**当mapreduce作业形成作业链条时，因为第二个作业的输入也已压缩，所以启用压缩同样有效。

5.5.5  压缩配置参数
: 要在 Hadoop 中启用压缩，可以配置如下参数
 ```xml
   <!--
  输入压缩
  Hadoop 使用文件扩展名判断是否支持某种编解码器
  -->
 <property>
      <name>io.compression.codecs</name>
      <value>org.apache.hadoop.io.compress.DefaultCodec,org.apache.hadoop.io.compress.GzipCodec,org.apache.hadoop.io.compress.BZip2Codec
      </value>
</property>
<!--mapper 输出,在 mapred-site.xml中配置-->
<property>
      <name>mapreduce.map.output.compress</name>
      <value>false</value>
      <description>这个参数设为 true 启用压缩
      </description>
</property>
<property>
      <name>mapreduce.map.output.compress.codec</name>
      <value>org.apache.hadoop.io.compress.DefaultCodec</value>
      <description>使用 LZO 或 snappy 编解码器在此阶段压缩数据</description>
</property>
<!--Reduce输出-->
<property>
  <name>mapreduce.output.fileoutputformat.compress</name>
  <value>false</value>
  <description>这个参数设为 true 启用压缩</description>
</property>
<property>
  <name>mapreduce.output.fileoutputformat.compress.type</name>
  <value>RECORD</value>
  <description>SequenceFile 输出使用的压缩类型 ：NONE 和 BLOCK</description>
</property>
<property>
  <name>mapreduce.output.fileoutputformat.compress.codec</name>
  <value>org.apache.hadoop.io.compress.DefaultCodec</value>
  <description>使用标准工具或者编解码器，如gzip 和 bzip2</description>
</property>

 ```
 
5.5.6 案例实战
: 

 - 数据流的压缩和解压缩
 CompressionCodec 有两个方法可以用于轻松地压缩或解压缩数据。要想对正在被写入一个输出流的数据进行压缩，我们可以使用 **createOutputStream(OutputStreamout)** 方法创建一个 **CompressionOutputStream** ，将其以压缩格式写入底层的流。相反，要想对从输入流读取而来的数据进行解压缩，则调用 **createInputStream(InputStreamin)** 函数，从而获得一个 **CompressionInputStream**，从而从底层的流读取未压缩的数据。
 - Map 输出端采用压缩
 - Reduce 输出端采用压缩

## **5.6 Hadoop优化**

5.6.1 MapReduce 跑的慢的原因
: Mapreduce 程序效率的瓶颈在于两点：
 
 - 计算机性能
CPU、内存、磁盘健康、网络
 - I/O 操作优化
        - 数据倾斜
        - map 和 reduce 数设置不合理
        - map 运行时间太长，导致 reduce 等待过久
        - 小文件过多
        - 大量的不可分块的超大文件
        - spill 次数过多
        - merge 次数过多等

5.6.2 MapReduce 优化方法
: MapReduce 优化方法主要从六个方面考虑：数据输入、Shuffle 过程、Reduce 阶段、IO 传输、数据倾斜问题和常用的调优参数。

 - 数据输入
        - 合并小文件：在执行 mr 任务前将小文件进行合并，大量的小文件会产生大量的map 任务，增大 map 任务装载次数，而任务的装载比较耗时，从而导致 mr 运行较慢。
        - 采用 CombineTextInputFormat来作为输入，解决输入端大量小文件场景。
 - Shuffle 过程
        - 减少溢写(spill)次数：通过调整 io.sort.mb 及 sort.spill.percent 参数值，增大触发 spill 的内存上限，减少 spill 次数，从而减少磁盘 IO。
        - 减少合并(merge)次数：通过调整 io.sort.factor 参数，增大 merge 的文件数目，减少 merge 的次数，从而缩短 mr 处理时间。
        - 在 map 之后，在不影响业务逻辑前提下，先进行 combine 处理，减少 I/O。
 - Reduce 阶段
        - 合理设置 map 和 reduce 的个数：两个都不能设置太少，也不能设置太多。太少，会导致 task 等待，延长处理时间；太多，会导致 map、reduce 任务间竞争资源，造成处理超时等错误。
        - 设置 map 、reduce 共存：调整 slowstart.completedmaps 参数，使 map 运行到一定程度后，reduce 也开始运行，减少 reduce 的等待时间。
        - 规避使用 reduce ：因为 reduce 在用于连接数据集的时候将会产生大量的网络消耗。
        - 合理设置 reduce 端的 buffer 。默认情况下，数据达到一个阈值的时候，buffer 中的数据就会写入磁盘，然后 reduce 会从磁盘中获得所有的数据。也就是说，buffer 和 reduce 是没有直接关联的，中间多个一个写磁盘->读磁盘的过程，既然有这个弊端，那么就可以通过参数来配置，使得 buffer 中的一部分数据可以直接输送到 reduce，从而减少 IO 开销：mapred.job.reduce.input.buffer.percent，默认为 0.0。当值大于 0 的时候，会保留指定比例的内存读 buffer 中的数据直接拿给 reduce 使用。这样一来，设置 buffer 需要内存，读取数据需要内存，reduce 计算也要内存，所以要根据作业的运行情况进行调整。
 - IO 传输
        - 采用数据压缩的方式，减少网络 IO 的的时间。安装 Snappy 和 LZO 压缩编码器。
        - 使用 SequenceFile 二进制文件。
  - 数据倾斜问题
        - 数据倾斜现象
            数据频率倾斜——某一个区域的数据量要远远大于其他区域。
            数据大小倾斜——部分记录的大小远远大于平均值。
        - 如何收集倾斜数据
            在 reduce 方法中加入记录 map 输出键的详细情况的功能。
        - 减少数据倾斜的方法
            - 抽样和范围分区
可以通过对原始数据进行抽样得到的结果集来预设分区边界值。
            - 自定义分区
基于输出键的背景知识进行自定义分区。例如，如果 map 输出键的单词来源于一本书。且其中某几个专业词汇较多。那么就可以自定义分区将这这些专业词汇发送给固定的一部分 reduce 实例。而将其他的都发送给剩余的 reduce 实例。
            - Combine
使用 Combine 可以大量地减小数据倾斜。在可能的情况下，combine 的目的就是聚合并精简数据。
            - 采用 Map Join，尽量避免 Reduce Join 。
 - 常用的调优参数
        - **资源相关参数**
        以下参数是在用户自己的 mr 应用程序中配置就可以生效（mapred-default.xml）

        |配置参数|参数说明|
        |---|---|
        |mapreduce.map.memory.mb|一个 Map Task 可使用的资源上限（单位:MB），默认为 1024。如果 Map Task 实际使用的资源量超过该值，则会被强制杀死。|
|mapreduce.reduce.memory.mb|一个 Reduce Task 可使用的资源上限（单位:MB），默认为 1024。如果 Reduce Task实际使用的资源量超过该值，则会被强制杀死。|
|mapreduce.map.cpu.vcores|每个 Map task 可使用的最多 cpu core 数目，默认值: 1|
|mapreduce.reduce.cpu.vcores|每个 Reduce task 可使用的最多 cpu core 数目，默认值: 1|
|mapreduce.reduce.shuffle.parallelcopies|每个 reduce 去 map 中拿数据的并行数。默认值是 5|
|mapreduce.reduce.shuffle.merge.percentbuffer| 中的数据达到多少比例开始写入磁盘。默认值 0.66|
|mapreduce.reduce.shuffle.input.buffer.percentbuffer| 大小占 reduce 可用内存的比例。默认值 0.7|
|mapreduce.reduce.input.buffer.percent|指定多少比例的内存用来存放 buffer 中的数据，默认值是 0.0|
应该在 yarn 启动之前就配置在服务器的配置文件中才能生效（yarn-default.xml）
|配置参数|参数说明|
        |---|---|
|yarn.scheduler.minimum-allocation-mb 1024|给应用程序 container 分配的最小内存|
|yarn.scheduler.maximum-allocation-mb 8192 |给应用程序 container 分配的最大内存|
|yarn.scheduler.minimum-allocation-vcores 1 |每个 container 申请的最小 CPU 核数|
|yarn.scheduler.maximum-allocation-vcores 32 |每个 container 申请的最大 CPU 核数|
|yarn.nodemanager.resource.memory-mb 8192 |给 containers 分配的最大物理内存|
shuffle 性能优化的关键参数，应在 yarn 启动之前就配置好（mapred-default.xml）
|配置参数|参数说明|
        |---|---|
|mapreduce.task.io.sort.mb 100 |shuffle 的环形缓冲区大小，默认 100m|
|mapreduce.map.sort.spill.percent 0.8| 环形缓冲区溢出的阈值，默认 80%|
        - **容错相关参数(mapreduce 性能优化)**
        |配置参数|参数说明|
        |---|---|
        |mapreduce.map.maxattempts|每个 Map Task 最大重试次数，一旦重试参数超过该值，则认为 Map Task 运行失败，默认值：4。|
|mapreduce.reduce.maxattempts|每个 Reduce Task 最大重试次数，一旦重试参数超过该值，则认为 Map Task 运行失败，默认值：4。|
|mapreduce.task.timeout|Task 超时时间，经常需要设置的一个参数，该参数表达的意思为：如果一个 task 在一定时间内没有任何进入，即不会读取新的数据，也没有输出数据，则认为该 task 处于 block 状态，可能是卡住了，也许永远会卡主，为了防止因为用户程序永远 block 住不退出，则强制设置了一个该超时时间（单位毫秒），默认是 600000。如果你的程序对每条输入数据的处理时间过长（比如会访问数据库，通过网络拉取数据等），建议将该参数调大，该 参 数 过 小 常 出 现 的 错 误 提 示 是 “AttemptID:attempt_14267829456721_123456_m_000224_0 Timed out after 300 secsContainer killed by theApplicationMaster.”。
    
5.6.3 HDFS 小文件优化方法
: 

 - HDFS 小文件弊端
 HDFS 上每个文件都要在 namenode 上建立一个索引，这个索引的大小约为 150byte，这样当小文件比较多的时候，就会产生很多的索引文件，一方面会大量占用 namenode 的内存空间，另一方面就是索引文件过大是的索引速度变慢。
- 解决方案
        - Hadoop Archive
        是一个高效地将小文件放入 HDFS 块中的文件存档工具，它能够将多个小文件打包成一个 HAR 文件，这样就减少了 namenode 的内存使用。
        - Sequence file
        sequence file 由一系列的二进制 key/value 组成，如果 key 为文件名，value 为文件内容，则可以将大批小文件合并成一个大文件。
        - CombineFileInputFormat
        CombineFileInputFormat 是一种新的 inputformat，用于将多个文件合并成一个单独的 split，另外，它会考虑数据的存储位置。
        - 开启 JVM 重用
        对于大量小文件 Job，可以开启 JVM 重用会减少 45%运行时间。
JVM 重用理解：一个 map 运行一个 jvm，重用的话，在一个 map 在 jvm 上运行完毕后，jvm 继续运行其他 map。
具体设置：mapreduce.job.jvm.numtasks 值在 10-20 之间。
         
## 5.7 Hadoop的HA
在HBase结束后讲解
## 6. Yarn架构原理

6.1 Hadoop1.x 和 和 Hadoop2.x 架构区别
: 在 Hadoop1.x 时代，Hadoop 中的 MapReduce 同时处理业务逻辑运算和资源的调度，耦合性较大。
在 Hadoop2.x 时代，增加了 Yarn。Yarn 只负责资源的调度，MapReduce 只负责运算。

6.2 Yarn 是什么
: Yarn 是一个资源调度平台，负责为运算程序提供服务器运算资源，相当于一个分布式的操作系统平台，类似于任务管理器，而 MapReduce 等运算程序则相当于运行于操作系统之上的应用程序。

6.3 Yarn 基本组件
: YARN 主要由 ResourceManager、NodeManager、ApplicationMaster 和 Container 等组件构成。

6.4 Yarn工作机制
: 工作机制详解
 
 - Mr 程序提交到客户端所在的节点。
 - Yarnrunner 向 Resourcemanager 申请一个 Application。
 - rm将该应用程序的资源路径返回给 yarnrunner。
 - 该程序将运行所需资源提交到 HDFS 上。
 - 程序资源提交完毕后，申请运行 mrAppMaster。
 - RM 将用户的请求初始化成一个 task。
 - 其中一个 NodeManager 领取到 task 任务。
 - 该 NodeManager 创建容器 Container，并产生 MRAppmaster。
 - Container 从 HDFS 上拷贝资源到本地。
 - MRAppmaster 向 RM 申请运行 maptask 资源。
 - RM 将运行 maptask 任务分配给另外两个 NodeManager，另两个 NodeManager 分别领取任务并创建容器。
 - MR 向两个接收到任务的 NodeManager 发送程序启动脚本，这两个 NodeManager 分别启动 maptask，maptask 对数据分区排序。
 - MrAppMaster 等待所有 maptask 运行完毕后，向 RM 申请容器，运行 reduce task。
 - reduce task 向 maptask 获取相应分区的数据。
 - 程序运行完毕后，MR 会向 RM 申请注销自己。

6.5 资源调度器
: 目前，Hadoop 作业调度器主要有三种：FIFO、Capacity Scheduler 和 Fair Scheduler。Hadoop2.7.6 默认的资源调度器是 Capacity Scheduler。具体设置详见：**yarn-default.xml** 文件
    ```xml
    <property>
        <description>The class to use as the resource scheduler.</description>
        <name>yarn.resourcemanager.scheduler.class</name>
        <value>org.apache.hadoop.yarn.server.resourcemanager.scheduler.capacity.CapacityScheduler</value>
  </property>
  
    ```
    - 先进先出调度器（FIFO）
    - 容量调度器（Capacity Scheduler）
    - 公平调度器（Fair Scheduler）
    
6.6 任务的推测执行
: 

 - 作业完成时间取决于最慢的任务完成时间
 一个作业由若干个 Map 任务和 Reduce 任务构成。因硬件老化、软件 Bug 等，某些任务可能运行非常慢。
**典型案例：**系统中有 99%的 Map 任务都完成了，只有少数几个 Map 老是进度很慢，完不成，怎么办？
- 推测执行机制
发现拖后腿的任务，比如某个任务运行速度远慢于任务平均速度。为拖后腿任务启动一个备份任务，同时运行。谁先运行完，则采用谁的结果。
- 执行推测任务的前提条件
        - 每个 task 只能有一个备份任务
        - 当前 job 已完成的 task 必须不小于 0.05（5%）
        -  开启推测执行参数设置。Hadoop2.7.6 mapred-site.xml 文件中默认是打开的。
        ```xml
        <property>
          <name>mapreduce.map.speculative</name>
          <value>true</value>
          <description>If true, then multiple instances of some map tasks 
                       may be executed in parallel.</description>
        </property>
        
        <property>
          <name>mapreduce.reduce.speculative</name>
          <value>true</value>
          <description>If true, then multiple instances of some reduce tasks 
                       may be executed in parallel.</description>
        </property>
        ```
- 不能启用推测执行机制情况
     - 任务间存在严重的负载倾斜
     - 特殊任务，比如任务向数据库中写数据
        
![image_1cjq3hkab12uruaf9mqrob2eo9.png-45.5kB][3]


  ![1](http://static.zybuluo.com/zhangwen100/58fam2ltmkwdafzwh9bz1jcd/image_1cjoi1fe0phatn11dm8188il049.png)
  ![2](http://static.zybuluo.com/zhangwen100/r7v96uf0cfwm0rodayev4nb6/image_1ck7vuneq13ultn7q6k8ud1kqq9.png)
  ![3](http://static.zybuluo.com/zhangwen100/v6pdlks370tkrbtdxg2ae2af/image_1cjq3hkab12uruaf9mqrob2eo9.png)