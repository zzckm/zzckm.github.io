---
title: 'Storm'
date: 2019-04-25 23:21:40
tags: [Storm]
---
# Storm

## 为何使用Storm？
Apache Storm是一个免费的开源分布式实时计算系统。Storm可以轻松可靠地处理无限数据流，实时处理Hadoop为批处理所做的工作。Storm非常简单，可以与任何编程语言一起使用，并且使用起来很有趣！

Storm有许多用例：实时分析，在线机器学习，连续计算，分布式RPC，ETL等。Storm很快：一个基准测试表示每个节点每秒处理超过一百万个元组。它具有可扩展性，容错性，可确保您的数据得到处理，并且易于设置和操作。

Storm集成了您已经使用的排队和数据库技术。Storm拓扑消耗数据流并以任意复杂的方式处理这些流，然后在计算的每个阶段之间重新划分流。阅读教程中的更多内容。


## Storm架构
1. Nimbus：master节点：监控topo的运行状态，分发task给supervisor
2. supervisor：从节点。每个这个节点都会有多个worker进程，负责代理task给worker
3. worker：执行特定的task，但是本身不执行任务，创建一个executor，然executor执行
4. task：实际要处理的业务逻辑
5. executor：运行task



## Storm与Hadoop的区别
|storm|Hadoop|
|---|---|
|实时流处理|批处理|
|无状态|有状态|
|使用zookeeper协同的主从架构|不需要zookeeper的主从架构|
|每秒处理数万消息|MR好几分钟甚至好几个小时|
|不会主动停止|终有完成的时候|


## storm的优点

- 跨平台
- 可伸缩
- 低延迟，秒级
- 高容错

## 核心概念
http://storm.apache.org/releases/1.2.2/Concepts.html

- Topologies
实时应用程序的逻辑被打包到Storm拓扑中。Storm拓扑类似于MapReduce作业。一个关键的区别是MapReduce作业最终完成，而拓扑结构永远运行（当然，直到你杀死它）。拓扑是与流分组连接的spouts 和Bolts的图形。形成一个有向图，边就是stream
- Tuple（元组）
主要的数据结构，有序元素的列表
- Stream
Tuple的序列。流是Storm中的核心抽象。流是无限的元组序列，以分布式方式并行处理和创建。流定义了一个模式，该模式命名流的元组中的字段。默认情况下，元组可以包含整数，长整数，短整数，字节，字符串，双精度数，浮点数，布尔值和字节数组。您还可以定义自己的序列化程序，以便可以在元组中本机使用自定义类型。
- Spouts
数据流源头，可以读取kafka队列消息，可以自定义。通常，spouts将从外部源读取元组并将它们发送到拓扑中（例如，Kestrel队列或Twitter API）。Spouts可以发出多个流。
- Bolts
转接头，逻辑处理单元。spout的数据传递给bolt，bolt计算完成后产生新的数据。可以执行过滤，函数，聚合，连接，与数据库交互等操作。
- Task
每个Spouts或Bolts在整个集群中执行任意数量的Task。执行实际的任务处理。
- Workers
Topologies在一个或多个worker 进程中执行。每个worker 进程都是物理JVM，并执行Topologies的所有Task的子集。例如，如果Topologies的组合并行度为300且分配了50个worker，则每个worker将执行6个任务（作为工作线程中的线程）。Storm试图在所有worker之间平均分配任务。
- Nimbus
master节点：监控topo的运行状态，分发task给supervisor，分析top并收集需要运行的task。本身无状态，依靠ZK获取集群的状态
- supervisor
从节点。每个这个节点都会有多个worker进程，负责代理task给worker，worker再孵化执行线程最终运行task，storm使用内部消息系统在nimbus和supervisor之间进行通信。接受nimbus指令，管理worker进程完成task派发。
- Executor
本质上由worker进行孵化出来的一个进程而已，Executor运行任务都属于同一个Spout或bolt


## Storm工作流程
一个工作的Storm集群应该有一个Nimbus和一个或多个supervisors。另一个重要的节点是Apache ZooKeeper，它将用于nimbus和supervisors之间的协调。现在让我们仔细看看Apache Storm的工作流程 

- 最初，nimbus将等待“Storm拓扑”提交给它。

- 一旦提交拓扑，它将处理拓扑并收集要执行的所有任务和任务将被执行的顺序。

- 然后，nimbus将任务均匀分配给所有可用的supervisors。

- 在特定的时间间隔，所有supervisor将向nimbus发送心跳以通知它们仍然活着。
- 当supervisor终止并且不向心跳发送心跳时，则nimbus将任务分配给另一个supervisor。

- 当nimbus本身终止时，supervisor将在没有任何问题的情况下对已经分配的任务进行工作。说白了就是继续执行自己的任务

- 一旦所有的任务都完成后，supervisor将等待新的任务进去。

- 同时，终止nimbus将由服务监控工具自动重新启动。

- 重新启动的网络将从停止的地方继续。同样，终止supervisor也可以自动重新启动。由于网络管理程序和supervisor都可以自动重新启动，并且两者将像以前一样继续，因此Storm保证至少处理所有任务一次。

- 一旦处理了所有拓扑，则nimbus等待新的拓扑到达，并且类似地，管理器等待新的任务。

默认情况下，Storm集群中有两种模式：

- 本地模式
此模式用于开发，测试和调试，因为它是查看所有拓扑组件协同工作的最简单方法。在这种模式下，我们可以调整参数，使我们能够看到我们的拓扑如何在不同的Storm配置环境中运行。在本地模式下，storm拓扑在本地机器上在单个JVM中运行。

- 生产模式
在这种模式下，我们将拓扑提交到工作Storm集群，该集群由许多进程组成，通常运行在不同的机器上。如在storm的工作流中所讨论的，工作集群将无限地运行，直到它被关闭。


## Storm工作实例

**移动呼叫日志分析器**
> 移动呼叫及其持续时间将作为对Storm的输入，Storm将处理和分组在相同呼叫者和接收者之间的呼叫及其呼叫总数。

在我们的场景中，我们需要收集呼叫日志详细信息。呼叫日志的信息包含。

 -  主叫号码
 -  接收号码
 -  持续时间
 
由于我们没有呼叫日志的实时信息，我们将生成假呼叫日志。假信息将使用Random类创建。完整的程序代码如下。
```java
import java.util.*;
import backtype.storm.tuple.Fields;
import backtype.storm.tuple.Values;

import backtype.storm.topology.IRichSpout;
import backtype.storm.topology.OutputFieldsDeclarer;
import backtype.storm.spout.SpoutOutputCollector;
import backtype.storm.task.TopologyContext;

/**
 * Spout类，负责产生数据流
 */
public class CallLogReaderSpout implements IRichSpout {
   //Spout输出收集器
   private SpoutOutputCollector collector;
   //是否完成
   private boolean completed = false;
	
   //上下文
   private TopologyContext context;
	
   //随机数生成器
   private Random random = new Random();
   private Integer idx = 0;

   @Override
   public void open(Map conf, TopologyContext context, SpoutOutputCollector collector) {
      this.context = context;
      this.collector = collector;
   }
    //下一个元组
   @Override
   public void nextTuple() {
      if(this.idx <= 1000) {
         List<String> mobileNumbers = new ArrayList<String>();
         mobileNumbers.add("101");
         mobileNumbers.add("102");
         mobileNumbers.add("103");
         mobileNumbers.add("104");

         Integer localIdx = 0;
         while(localIdx++ < 100 && this.idx++ < 1000) {
            //主叫
            String caller = mobileNumbers.get(random.nextInt(4));
            //被叫
            String callee = mobileNumbers.get(random.nextInt(4));
				
            while(caller == callee) {
               callee = mobileNumbers.get(random.nextInt(4));
            }
			//模拟通话时长	
            Integer duration = randomGenerator.nextInt(60);
            //输出的元组数据
            this.collector.emit(new Values(caller, callee, duration));
         }
      }
   }
    //定义输出的字段名称
   @Override
   public void declareOutputFields(OutputFieldsDeclarer declarer) {
      declarer.declare(new Fields("from", "to", "duration"));
   }

   //
   @Override
   public void close() {}

   public boolean isDistributed() {
      return false;
   }

   @Override
   public void activate() {}

   @Override 
   public void deactivate() {}

   @Override
   public void ack(Object msgId) {}

   @Override
   public void fail(Object msgId) {}

   @Override
   public Map<String, Object> getComponentConfiguration() {
      return null;
   }
}


```
```java
import java.util.HashMap;
import java.util.Map;

import backtype.storm.tuple.Fields;
import backtype.storm.tuple.Values;
import backtype.storm.task.OutputCollector;
import backtype.storm.task.TopologyContext;

import backtype.storm.topology.IRichBolt;
import backtype.storm.topology.OutputFieldsDeclarer;
import backtype.storm.tuple.Tuple;

//创建通话记录的bolt
public class CallLogCreatorBolt implements IRichBolt {
   //创建OutputCollector实例，收集并发出元组以产生输出
   private OutputCollector collector;

   @Override
   public void prepare(Map conf, TopologyContext context, OutputCollector collector) {
      this.collector = collector;
   }

   @Override
   public void execute(Tuple tuple) {
      //获取主叫
      String from = tuple.getString(0);
      //获取被叫
      String to = tuple.getString(1);
      //获取通话时长
      Integer duration = tuple.getInteger(2);
      //产生新的元组
      collector.emit(new Values(from + " - " + to, duration));
   }

   @Override
   public void cleanup() {}
   //重新定义输出字段的名称
   @Override
   public void declareOutputFields(OutputFieldsDeclarer declarer) {
      declarer.declare(new Fields("call", "duration"));
   }
	
   @Override
   public Map<String, Object> getComponentConfiguration() {
      return null;
   }
}



```
```java
import java.util.HashMap;
import java.util.Map;

import backtype.storm.tuple.Fields;
import backtype.storm.tuple.Values;
import backtype.storm.task.OutputCollector;
import backtype.storm.task.TopologyContext;
import backtype.storm.topology.IRichBolt;
import backtype.storm.topology.OutputFieldsDeclarer;
import backtype.storm.tuple.Tuple;
//通话记录计数器
public class CallLogCounterBolt implements IRichBolt {
   Map<String, Integer> counterMap;
   private OutputCollector collector;

   @Override
   public void prepare(Map conf, TopologyContext context, OutputCollector collector) {
      this.counterMap = new HashMap<String, Integer>();
      this.collector = collector;
   }

   @Override
   public void execute(Tuple tuple) {
      //通话记录：
      String call = tuple.getString(0);
      //本次通话时长
      Integer duration = tuple.getInteger(1);
		
      if(!counterMap.containsKey(call)){
         counterMap.put(call, 1);
      }else{
         Integer c = counterMap.get(call) + 1;
         counterMap.put(call, c);
      }
	  //消息被确认
      collector.ack(tuple);
   }

   @Override
   public void cleanup() {
      for(Map.Entry<String, Integer> entry:counterMap.entrySet()){
         System.out.println(entry.getKey()+" : " + entry.getValue());
      }
   }

   @Override
   public void declareOutputFields(OutputFieldsDeclarer declarer) {
      declarer.declare(new Fields("call"));
   }
	
   @Override
   public Map<String, Object> getComponentConfiguration() {
      return null;
   }
	
}

```

```java
public class App{
    public static void main(String[] args){
    TopologyBuilder builder = new TopologyBuilder();
    //设置spout
    builder.setSpout("spout", new CallLogReaderSpout());
    //设置bolt
    builder.setBolt("creator-bolt", new CallLogCreatorBolt()).shuffleGrouping("spout");
    builder.setBolt("counter-bolt", new CallLogCounterBolt()).fieldsGrouping("creator-bolt", new Fields("call"));
    
    Config config = new Config();
    config.setDebug(true);
    //本地模式运行
    LocalCluster cluster = new LocalCluster();
    //集群模式
    StormSubmitter.submitTopology("mytopo", conf, builder.createTopology());
      cluster.submitTopology("LogAnalyserStorm", config, builder.createTopology());
      Thread.sleep(10000);
		
      //停止集群（类似于kill）
		
      cluster.shutdown();
    
    
    }


}



```
在集群上部署topology，导出jar
```shell
storm jar path/to/allmycode.jar org.me.MyTopology arg1 arg2 arg3

```

## Storm 实现wordcount


## Stream Grouping详解
Storm里面有7种类型的stream grouping

- Shuffle Grouping: 随机分组， 随机派发stream里面的tuple，保证每个bolt接收到的tuple数目大致相同。
- Fields Grouping：按字段分组，比如按userid来分组，具有同样userid的tuple会被分到相同的Bolts里的一个task，而不同的userid则会被分配到不同的bolts里的task。
- All Grouping：广播发送，对于每一个tuple，所有的bolts都会收到。
- Global Grouping：全局分组， 这个tuple被分配到storm中的一个bolt的其中一个task。再具体一点就是分配给id值最低的那个task。
- Non Grouping：不分组，这stream grouping个分组的意思是说stream不关心到底谁会收到它的tuple。目前这种分组和Shuffle grouping是一样的效果， 有一点不同的是storm会把这个bolt放到这个bolt的订阅者同一个线程里面去执行。
- Direct Grouping： 直接分组， 这是一种比较特别的分组方法，用这种分组意味着消息的发送者指定由消息接收者的哪个task处理这个消息。只有被声明为Direct Stream的消息流可以声明这种分组方法。而且这种消息tuple必须使用emitDirect方法来发射。消息处理者可以通过TopologyContext来获取处理它的消息的task的id （OutputCollector.emit方法也会返回task的id）。
- Local or shuffle grouping：如果目标bolt有一个或者多个task在同一个工作进程中，tuple将会被随机发生给这些tasks。否则，和普通的Shuffle Grouping行为一致。



<figure>
<svg version="1.1" id="anim1" width="100%" height="100%" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px" viewBox="0 0 552 350" style="enable-background:new 0 0 552 350;" xml:space="preserve">
<style type="text/css">
    #anim1 .st2{opacity:0.5502;filter:url(#filter);enable-background:new ;}
    #anim1 .st3{fill:#FFFFFF;}
    #anim1 .st4{fill:none;stroke:#E6E6E6;stroke-width:2;stroke-linecap:round;}
    #anim1 .st5{fill:none;stroke:url(#green-path-1);stroke-width:2;stroke-linecap:round;}
    #anim1 .st6{fill:none;stroke:url(#green-path-2);stroke-width:2;stroke-linecap:round;}
    #anim1 .st7{fill:none;stroke:url(#green-path-3);stroke-width:2;stroke-linecap:round;}
    #anim1 .st8{fill:none;stroke:url(#green-path-4);stroke-width:2;stroke-linecap:round;}
    #anim1 .st9{fill:#FFFFFF;stroke:#E6E6E6;stroke-width:2;}
    #anim1 .st10{fill:none;stroke:#E6E6E6;stroke-width:2;}
    #anim1 .st20{fill:none;stroke:#999999;stroke-width:3;stroke-linecap:round;stroke-linejoin:round;}
</style>
<filter width="200%" height="200%" id="filter" filterUnits="objectBoundingBox" y="-50%" x="-50%">
    <feGaussianBlur in="SourceGraphic" stdDeviation="18.8378906"></feGaussianBlur>
</filter>
<g id="a2">
<path id="white-circle-bg-1" class="st3" d="M7.5,65.3A50,50 0,1,1 107.5,65.3A50,50 0,1,1 7.5,65.3" style="stroke-dasharray: 315, 355; stroke-dashoffset: 0;"></path>
<path id="white-circle-bg-2" class="st3" d="M154.5,65.3A50,50 0,1,1 254.5,65.3A50,50 0,1,1 154.5,65.3" style="stroke-dasharray: 315, 355; stroke-dashoffset: 0;"></path>
<path id="white-circle-bg-3" class="st3" d="M300.5,65.3A50,50 0,1,1 400.5,65.3A50,50 0,1,1 300.5,65.3" style="stroke-dasharray: 315, 355; stroke-dashoffset: 0;"></path>
<path id="white-circle-bg-4" class="st3" d="M443.5,65.3A50,50 0,1,1 543.5,65.3A50,50 0,1,1 443.5,65.3" style="stroke-dasharray: 315, 355; stroke-dashoffset: 0;"></path>
<path id="gray-outline-1" class="st4" d="M56.9,114.6C29.5,114.3,7.5,92,7.5,64.6c0-27.6,22.4-50,50-50s50,22.4,50,50c0,27.6-22.4,50-50,50v15c0,5.5,4.5,10,10,10h15l148-0.1" data-ignore="none"></path>
<path id="gray-outline-2" class="st4" d="M203.5,114.6c-27.2-0.5-49-22.7-49-50c0-27.6,22.4-50,50-50s50,22.4,50,50c0,27.6-22.4,50-50,50v15c0,5.5,4.5,10,10,10h15h38c5.5,0,10,4.5,10,10v15" data-ignore="none"></path>
<path id="gray-outline-3" class="st4" d="M349.7,114.6c-27.3-0.4-49.2-22.6-49.2-50c0-27.6,22.4-50,50-50s50,22.4,50,50c0,27.6-22.4,50-50,50v15c0,5.5-4.5,10-10,10h-15h-38c-5.5,0-10,4.5-10,10v15" data-ignore="none"></path>
<path id="gray-outline-4" class="st4" d="M494,114.6c-0.2,0-0.4,0-0.5,0c-27.6,0-50-22.4-50-50c0-27.6,22.4-50,50-50s50,22.4,50,50c0,27.3-21.8,49.5-49,50v15c0,5.5-4.5,10-10,10h-15h-87.3h-47.7" data-ignore="none"></path>

</g>

<g id="a1">

<linearGradient id="green-path-1" gradientUnits="userSpaceOnUse" x1="-93.7909" y1="220.2818" x2="-93.7909" y2="219.1985" gradientTransform="matrix(-100 0 0 100 -9260.0879 -21896.832)">
<stop offset="0" style="stop-color:#00BFB3"></stop>
<stop offset="1" style="stop-color:#31C8FA"></stop>
</linearGradient>

<path id="green-path-1b" class="st5" d="M56.9,114.6C29.5,114.3,7.5,92,7.5,64.6c0-27.6,22.4-50,50-50s50,22.4,50,50c0,27.6-22.4,50-50,50v15c0,5.5,4.5,10,10,10h15l148-0.1" style="stroke-dasharray: 508, 548; stroke-dashoffset: 0;"></path>

<image id="Bitmap-1" opacity="1" x="20" y="25" width="80" height="80" xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="/cn/assets/blt96a73ceabd7b6203/beats-01.svg"></image>

<linearGradient id="green-path-2" gradientUnits="userSpaceOnUse" x1="-93.2909" y1="220.4485" x2="-93.2909" y2="219.2818" gradientTransform="matrix(-100 0 0 100 -9113.0879 -21896.832)">
<stop offset="0" style="stop-color:#00BFB3"></stop>
<stop offset="1" style="stop-color:#31C8FA"></stop>
</linearGradient>

<path id="green-path-2b" class="st6" d="M203.5,114.6c-27.2-0.5-49-22.7-49-50c0-27.6,22.4-50,50-50s50,22.4,50,50c0,27.6-22.4,50-50,50v15c0,5.5,4.5,10,10,10h15h38c5.5,0,10,4.5,10,10v15" style="stroke-dasharray: 428, 468; stroke-dashoffset: 0;"></path>

<image id="Bitmap-2" opacity="1" x="165" y="25" width="80" height="80" xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="/cn/assets/bltf7e873716e4478cb/GitHub-01.svg" style="display: inline; opacity: 0.129832;"></image>

<linearGradient id="green-path-3" gradientUnits="userSpaceOnUse" x1="-91.8022" y1="220.4485" x2="-91.8022" y2="219.2818" gradientTransform="matrix(100 0 0 100 9519.2246 -21896.832)">
<stop offset="0" style="stop-color:#00BFB3"></stop>
<stop offset="1" style="stop-color:#31C8FA"></stop>
</linearGradient>

<path id="green-path-3b" class="st7" d="M349.7,114.6c-27.3-0.4-49.2-22.6-49.2-50c0-27.6,22.4-50,50-50s50,22.4,50,50c0,27.6-22.4,50-50,50v15c0,5.5-4.5,10-10,10h-15h-38c-5.5,0-10,4.5-10,10v15" style="stroke-dasharray: 428, 468; stroke-dashoffset: 0;"></path>

<image id="Bitmap-3" opacity="1" x="310" y="25" width="80" height="80" xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="/cn/assets/blt9be07c84e4c2bc10/http-01.svg" style="display: inline; opacity: 0.137673;"></image>

<linearGradient id="green-path-4" gradientUnits="userSpaceOnUse" x1="-92.2322" y1="220.2818" x2="-92.2322" y2="219.1985" gradientTransform="matrix(100 0 0 100 9662.2246 -21896.832)">
<stop offset="0" style="stop-color:#00BFB3"></stop>
<stop offset="1" style="stop-color:#31C8FA"></stop>
</linearGradient>

<path id="green-path-4b" class="st8" d="M494,114.6c-0.2,0-0.4,0-0.5,0c-27.6,0-50-22.4-50-50c0-27.6,22.4-50,50-50s50,22.4,50,50c0,27.3-21.8,49.5-49,50v15c0,5.5-4.5,10-10,10h-15h-87.3h-47.7" style="stroke-dasharray: 495, 535; stroke-dashoffset: 0;"></path>

<image id="Bitmap-4" opacity="1" x="455" y="25" width="80" height="80" xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="/cn/assets/bltb4167870b42fce56/IRC-01.svg" style="display: inline; opacity: 0.143497;"></image>
</g>

<g id="a3">

<path id="gray-circle-1" class="st9" d="M128.5,348.3c19.3,0,35-15.7,35-35s-15.7-35-35-35s-35,15.7-35,35S109.2,348.3,128.5,348.3z" data-ignore="none"></path>

<image id="Bitmap-5" opacity="1" x="103" y="288" width="50" height="50" xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="https://www.elastic.co/cn/assets/bltb5d092d864eb080d/Elasticsearch-02.svg"></image>

<path id="gray-circle-2" class="st9" d="M228.5,348.3c19.3,0,35-15.7,35-35s-15.7-35-35-35s-35,15.7-35,35S209.2,348.3,228.5,348.3z" data-ignore="none"></path>

<image id="Bitmap-6" opacity="1" x="203" y="288" width="50" height="50" xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="https://www.elastic.co/cn/assets/blt08e6eee450ecd75e/Email-02.svg"></image>

<path id="gray-circle-3" class="st9" d="M326.5,348.3c19.3,0,35-15.7,35-35s-15.7-35-35-35s-35,15.7-35,35S307.2,348.3,326.5,348.3z" data-ignore="none"></path>

<image id="Bitmap-7" opacity="1" x="302" y="288" width="50" height="50" xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="/cn/assets/bltf361d26f5edc8763/S3-02.svg"></image>

<path id="gray-circle-4" class="st9" d="M428.5,348.3c19.3,0,35-15.7,35-35s-15.7-35-35-35s-35,15.7-35,35S409.2,348.3,428.5,348.3z" data-ignore="none"></path>

<image id="Bitmap-8" opacity="1" x="400" y="283" width="60" height="60" xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="https://www.elastic.co/cn/products/logstash/cn/assets/blt3076bd904482ae55/HDFS-02.svg"></image>

<path id="gray-circle-path-1" class="st10" d="M248.5,254.3h-105h-5c-5.5,0-10,4.5-10,10v15" data-ignore="none"></path>
<path id="gray-circle-path-2" class="st10" d="M277.5,229.3v15c0,5.5-4.5,10-10,10h-24h-5c-5.5,0-10,4.5-10,10v15" data-ignore="none"></path>
<path id="gray-circle-path-3" class="st10" d="M308.3,254.3h105.2h5c5.5,0,10,4.5,10,10v15" data-ignore="none"></path>
<path id="gray-circle-path-4" class="st10" d="M277.5,229.3v15c0,5.5,4.5,10,10,10h24h5c5.5,0,10,4.5,10,10v15" data-ignore="none"></path>
<path id="center-sq" class="st9" d="M313.5,172.3c0-5.5-4.5-10-10-10h-50c-5.5,0-10,4.5-10,10v50c0,5.5,4.5,10,10,10h50c5.5,0,10-4.5,10-10V172.3z" data-ignore="none"></path>

<g id="Group-4" transform="translate(515.000000, 306.000000)">
<path id="Stroke-1_1_" class="st20" d="M-221.5-88.5v-11" data-ignore="none"></path>
<path id="Stroke-3_1_" class="st20" d="M-231.5-88.5v-11" data-ignore="none"></path>
<path id="Stroke-5" class="st20" d="M-241.5-88.5v-11" data-ignore="none"></path>
<path id="Stroke-7" class="st20" d="M-251.5-88.5v-11" data-ignore="none"></path>
<path id="Stroke-9" class="st20" d="M-251.5-107.5h30" data-ignore="none"></path>
<path id="Stroke-11" class="st20" d="M-249.5-128.5h26c1.1,0,2,0.9,2,2v9c0,1.1-0.9,2-2,2h-26c-1.1,0-2-0.9-2-2v-9
C-251.5-127.6-250.6-128.5-249.5-128.5L-249.5-128.5z" data-ignore="none"></path>
</g>
</g>
</svg>
</figure>

