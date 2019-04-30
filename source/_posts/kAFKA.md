---
title: Kafka
date: 2019-04-23 22:33:29
tags: [kafka]
categories: kafka
---
# kAFKA 

## Kafka概述
Kafka是什么
: 在流式计算中，Kafka一般用来缓存数据，Storm通过消费Kafka的数据进行计算。

 - Apache Kafka是一个开源消息系统，由Scala和Java写成。是由Apache软件基金会开发的一个开源消息系统项目。
 - Kafka最初是由LinkedIn公司开发，并于 2011 年初开源。2012年10月从Apache Incubator毕业。该项目的目标是为处理实时数据提供一个统一、高通量、低等待的平台。
 - Kafka是一个分布式消息队列。Kafka对消息保存时根据Topic进行归类，发送消息者称为Producer，消息接受者称为Consumer，此外kafka集群有多个kafka实例组成，每个实例(server)成为broker。
 - 无论是kafka集群，还是producer和consumer都依赖于zookeeper集群保存一些meta信息，来保证系统可用性。

消息队列内部实现原理
: 
![image_1clkfilp1sf91d9b1ug3s17ao13.png-196.9kB][1]

 - 点对点模式（一对一，消费者主动拉取数据，消息收到后消息清除）
点对点模型通常是一个基于拉取或者轮询的消息传送模型，这种模型从队列中请求信息，而不是将消息推送到客户端。这个模型的特点是发送到队列的消息被一个且只有一个接收者接收处理，即使有多个消息监听者也是如此。
 - 发布/订阅模式（一对多，数据生产后，推送给所有订阅者）
发布订阅模型则是一个基于推送的消息传送模型。发布订阅模型可以有多种不同的订阅者，临时订阅者只在主动监听主题时才接收消息，而持久订阅者则监听主题的所有消息，即使当前订阅者不可用，处于离线状态。

为什么需要消息队列
: 

 - 解耦
 允许你独立的扩展或修改两边的处理过程，只要确保它们遵守同样的接口约束。
 - 冗余
消息队列把数据进行持久化直到它们已经被完全处理，通过这一方式规避了数据丢失风险。许多消息队列所采用的"插入-获取-删除"范式中，在把一个消息从队列中删除之前，需要你的处理系统明确的指出该消息已经被处理完毕，从而确保你的数据被安全的保存直到你使用完毕。
 - 扩展性
因为消息队列解耦了你的处理过程，所以增大消息入队和处理的频率是很容易的，只要另外增加处理过程即可。
 - 灵活性 & 峰值处理能力
在访问量剧增的情况下，应用仍然需要继续发挥作用，但是这样的突发流量并不常见。如果为以能处理这类峰值访问为标准来投入资源随时待命无疑是巨大的浪费。使用消息队列能够使关键组件顶住突发的访问压力，而不会因为突发的超负荷的请求而完全崩溃。
 - 可恢复性
系统的一部分组件失效时，不会影响到整个系统。消息队列降低了进程间的耦合度，所以即使一个处理消息的进程挂掉，加入队列中的消息仍然可以在系统恢复后被处理。
 - 顺序保证
在大多使用场景下，数据处理的顺序都很重要。大部分消息队列本来就是排序的，并且能保证数据会按照特定的顺序来处理。（Kafka保证一个Partition内的消息的有序性）
 - 缓冲
有助于控制和优化数据流经过系统的速度，解决生产消息和消费消息的处理速度不一致的情况。
 - 异步通信
很多时候，用户不想也不需要立即处理消息。消息队列提供了异步处理机制，允许用户把一个消息放入队列，但并不立即处理它。想向队列中放入多少消息就放多少，然后在需要的时候再去处理它们。

Kafka 架构
: 

 - Producer ：消息生产者，就是向kafka broker发消息的客户端。
 - Consumer ：消息消费者，向kafka broker取消息的客户端
 - Topic ：可以理解为一个队列。
 - Consumer Group （CG）：这是kafka用来实现一个topic消息的广播（发给所有的consumer）和单播（发给任意一个consumer）的手段。一个topic可以有多个CG。topic的消息会复制-给consumer。如果需要实现广播，只要每个consumer有一个独立的CG就可以了。要实现单播只要所有的consumer在同一个CG。用CG还可以将consumer进行自由的分组而不需要多次发送消息到不同的topic。
 - Broker ：一台kafka服务器就是一个broker。一个集群由多个broker组成。一个broker可以容纳多个topic。
 - Partition：为了实现扩展性，一个非常大的topic可以分布到多个broker（即服务器）上，一个topic可以分为多个partition，每个partition是一个有序的队列。partition中的每条消息都会被分配一个有序的id（offset）。kafka只保证按一个partition中的顺序将消息发给consumer，不保证一个topic的整体（多个partition间）的顺序。
 - Offset：kafka的存储文件都是按照offset.kafka来命名，用offset做名字的好处是方便查找。例如你想找位于2049的位置，只要找到2048.kafka的文件即可。当然the first offset就是00000000000.kafka

## Kafka集群部署

下载
: [下载地址][2]
[CDH下载地址][3]
![image_1csd49rn65th1b2p1c9fsga1r8a9.png-7.1kB][4]

安装
: 
    ```shell
    
    
    ## 修改server.properties文件
    [root@master config]# vim server.properties
    # 每一台broker必须有唯一的一个id
    broker.id=0
    # 处理网络请求的时候的线程数
    num.network.threads=3
    log.dirs=/opt/apps/Kafka/logs
    zookeeper.connect=master:2181,slave1:2181,slave2:2181,slave3:2181,slave4:2181
    
    [root@master config]# vim producer.properties
    bootstrap.servers=master:9092,slave1:9092,slave2:9092,slave3:9092,slave4:9092
    [root@master config]# vim consumer.properties
    bootstrap.servers=master:9092,slave1:9092,slave2:9092,slave3:9092,slave4:9092
    
    ## 环境变量
    [root@master config]# vim /etc/profile
    #Kafka环境变量
    export KAFKA_HOME=/opt/apps/Kafka/kafka_2.11_2.0.0
    export PATH=$PATH:$KAFKA_HOME/bin
    
    ## 启动服务
    kafka-server-start.sh /opt/apps/Kafka/kafka_2.11_2.0.0/config/server.properties &
    ```

CDH安装
: 




简单操作
: 
 ```shell
 ## 创建主题
 [root@master ~]# kafka-topics.sh --zookeeper master:2181 --create --replication-factor 3 --partitions 1 --topic stormKafka
 
 ## 生产数据（消息）
 kafka-console-producer.sh \
    --broker-list slave1:9092 \
    --topic bigdata

 
 ## 消费数据（消息）
 kafka-console-consumer.sh \
 --bootstrap-server slave4:9092 \
 --consumer.config /opt/apps/_2.0.0/config/consumer.properties \
 --from-beginning \
 --topic bigdata
 
 
 ## 删除主题
 [root@master logs]# kafka-topics.sh --zookeeper master:2181 --delete --topic bigdata
 ## 
 delete.topic.enable=true才可以删除主题
 
 
 ## 查看主题详情
 [root@master ~]# kafka-topics.sh --zookeeper master:2181 --describe --topic bigdata
 ```

## Kafka生产过程分析

写入方式
: producer采用推（push）模式将消息发布到broker，每条消息都被追加（append）到分区（patition）中，属于顺序写磁盘（顺序写磁盘效率比随机写内存要高，保障kafka吞吐率）。

分区（Partition）
:	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Kafka集群有多个消息代理服务器（broker-server）组成，发布到Kafka集群的每条消息都有一个类别，用主题（topic）来表示。通常，不同应用产生不同类型的数据，可以设置不同的主题。一个主题一般会有多个消息的订阅者，当生产者发布消息到某个主题时，订阅了这个主题的消费者都可以接收到生成者写入的新消息。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Kafka集群为每个主题维护了分布式的分区（partition）日志文件，物理意义上可以把主题（topic）看作进行了分区的日志文件（partition log）。主题的每个分区都是一个有序的、不可变的记录序列，新的消息会不断追加到日志中。分区中的每条消息都会按照时间顺序分配到一个单调递增的顺序编号，叫做偏移量（offset），这个偏移量能够唯一地定位当前分区中的每一条消息。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;消息发送时都被发送到一个topic，其本质就是一个目录，而topic是由一些Partition Logs(分区日志)组成，其组织结构如下图所示：
下图中的topic有3个分区，每个分区的偏移量都从0开始，不同分区之间的偏移量都是独立的，不会相互影响。
![image_1clrfrsma1bs71hvama4aho134v13.png-19.1kB][5]
![image_1clrfse9s1akr1sl3ib61to22br1g.png-136kB][6]
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;我们可以看到，每个Partition中的消息都是有序的，生产的消息被不断追加到Partition log上，其中的每一个消息都被赋予了一个唯一的offset值。
发布到Kafka主题的每条消息包括键值和时间戳。消息到达服务器端的指定分区后，都会分配到一个自增的偏移量。原始的消息内容和分配的偏移量以及其他一些元数据信息最后都会存储到分区日志文件中。消息的键也可以不用设置，这种情况下消息会均衡地分布到不同的分区。

 - 分区的原因
     - 方便在集群中扩展，每个Partition可以通过调整以适应它所在的机器，而一个topic又可以有多个Partition组成，因此整个集群就可以适应任意大小的数据了
     - 可以提高并发，因为可以以Partition为单位读写了。
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;传统消息系统在服务端保持消息的顺序，如果有多个消费者消费同一个消息队列，服务端会以消费存储的顺序依次发送给消费者。但由于消息是异步发送给消费者的，消息到达消费者的顺序可能是无序的，这就意味着在并行消费时，传统消息系统无法很好地保证消息被顺序处理。虽然我们可以设置一个专用的消费者只消费一个队列，以此来解决消息顺序的问题，但是这就使得消费处理无法真正执行。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Kafka比传统消息系统有更强的顺序性保证，它使用主题的分区作为消息处理的并行单元。Kafka以分区作为最小的粒度，将每个分区分配给消费者组中不同的而且是唯一的消费者，并确保一个分区只属于一个消费者，即这个消费者就是这个分区的唯一读取线程。那么，只要分区的消息是有序的，消费者处理的消息顺序就有保证。每个主题有多个分区，不同的消费者处理不同的分区，所以Kafka不仅保证了消息的有序性，也做到了消费者的负载均衡。
- 分区的原则
     - 指定了patition，则直接使用
     - 未指定patition但指定key，通过对key的value进行hash出一个patition
     - patition和key都未指定，使用轮询选出一个patition。
    ```java
    //DefaultPartitioner类
public int partition(String topic, Object key, byte[] keyBytes, Object value, byte[] valueBytes, Cluster cluster) {
        List<PartitionInfo> partitions = cluster.partitionsForTopic(topic);
        int numPartitions = partitions.size();
        if (keyBytes == null) {
            int nextValue = nextValue(topic);
            List<PartitionInfo> availablePartitions = cluster.availablePartitionsForTopic(topic);
            if (availablePartitions.size() > 0) {
                int part = Utils.toPositive(nextValue) % availablePartitions.size();
                return availablePartitions.get(part).partition();
            } else {
                // no partitions are available, give a non-available partition
                return Utils.toPositive(nextValue) % numPartitions;
            }
        } else {
            // hash the keyBytes to choose a partition
            return Utils.toPositive(Utils.murmur2(keyBytes)) % numPartitions;
        }
    }

    ```

副本（Replication）
: 同一个partition可能会有多个replication（对应 server.properties 配置中的 default.replication.factor=N）。没有replication的情况下，一旦broker 宕机，其上所有 patition 的数据都不可被消费，同时producer也不能再将数据存于其上的patition。引入replication之后，同一个partition可能会有多个replication，而这时需要在这些replication之间选出一个leader，producer和consumer只与这个leader交互，其它replication作为follower从leader 中复制数据。

写入流程
: producer写入消息流程如下：
![image_1clrgajjd134qg0139khveqcb1t.png-98.4kB][7]

 - producer先从zookeeper的 "/brokers/.../state"节点找到该partition的leader
 - producer将消息发送给该leader
 - leader将消息写入本地log
 - followers从leader pull消息，写入本地log后向leader发送ACK
 - leader收到所有ISR中的replication的ACK后，增加HW（high watermark，最后commit 的offset）并向producer发送ACK

Broker 保存消息
: 

 - 存储方式
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;物理上把topic分成一个或多个patition（对应 server.properties 中的num.partitions=3配置），每个patition物理上对应一个文件夹（该文件夹存储该patition的所有消息和索引文件）
 - 存储策略
无论消息是否被消费，kafka都会保留所有消息。有两种策略可以删除旧数据：
     - 基于时间：log.retention.hours=168
     - 基于大小：log.retention.bytes=1073741824
需要注意的是，因为Kafka读取特定消息的时间复杂度为O(1)，即与文件大小无关，所以这里删除过期文件与提高 Kafka 性能无关。

Zookeeper存储结构
: ![image_1clrgn2u4c361hi31lk7157q1o6u2a.png-101.3kB][8]

/controller_epoch
就是一个数字，第一次为1，后续的时候只要中央控制器发生变更（选举出来的），这个值就会+1

/controller
存储的是中央控制器所在的broker的信息
{"version":1,"brokerid":60,"timestamp":"1542495029190"}

/consumers/console-consumer-95629/ids/console-consumer-95629_node03-1542495483815-afd89b49
/主节点/消费者组的ID/ids/消费者的ID
{"version":1,"subscription":{"hehe":1},"pattern":"white_list","timestamp":"1542495483878"}
{版本，订阅的topic列表{tiopic名称:消费者中topic消费者的线程数}}


## Kafka消费过程分析
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;kafka提供了两套consumer API：高级Consumer API和低级API。

消费模型
: &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;消息由生产者发布到Kafka集群后，会被消费者消费。消息的消费模型有两种：推送模型（push）和拉取模型（pull）。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;基于推送模型（push）的消息系统，由消息代理记录消费者的消费状态。消息代理在将消息推送到消费者后，标记这条消息为已消费，但这种方式无法很好地保证消息被处理。比如，消息代理把消息发送出去后，当消费进程挂掉或者由于网络原因没有收到这条消息时，就有可能造成消息丢失（因为消息代理已经把这条消息标记为已消费了，但实际上这条消息并没有被实际处理）。如果要保证消息被处理，消息代理发送完消息后，要设置状态为“已发送”，只有收到消费者的确认请求后才更新为“已消费”，这就需要消息代理中记录所有的消费状态，这种做法显然是不可取的。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Kafka采用拉取模型，由消费者自己记录消费状态，每个消费者互相独立地顺序读取每个分区的消息。如下图所示，有两个消费者（不同消费者组）拉取同一个主题的消息，消费者A的消费进度是3，消费者B的消费进度是6。消费者拉取的最大上限通过最高水位（watermark）控制，生产者最新写入的消息如果还没有达到备份数量，对消费者是不可见的。这种由消费者控制偏移量的优点是：消费者可以按照任意的顺序消费消息。比如，消费者可以重置到旧的偏移量，重新处理之前已经消费过的消息；或者直接跳到最近的位置，从当前的时刻开始消费。

高级API
: 

 - 高级API优点
     - 高级API 写起来简单
     - 不需要自行去管理offset，系统通过zookeeper自行管理。
     - 不需要管理分区，副本等情况，.系统自动管理。
     - 消费者断线会自动根据上一次记录在zookeeper中的offset去接着获取数据（默认设置1分钟更新一下zookeeper中存的offset）
     - 可以使用group来区分对同一个topic 的不同程序访问分离开来（不同的group记录不同的offset，这样不同程序读取同一个topic才不会因为offset互相影响）
 - 高级API缺点
     - 不能自行控制offset（对于某些特殊需求来说）
     - 不能细化控制如分区、副本、zk等

低级API
: 

 - 低级 API 优点
     - 能够让开发者自己控制offset，想从哪里读取就从哪里读取。
     - 自行控制连接分区，对分区自定义进行负载均衡
     - 对zookeeper的依赖性降低（如：offset不一定非要靠zk存储，自行存储offset即可，比如存在文件或者内存中）
 - 低级API缺点
 太过复杂，需要自行控制offset，连接哪个分区，找到分区leader 等。

消费者组
: 消费者是以consumer group消费者组的方式工作，由一个或者多个消费者组成一个组，共同消费一个topic。每个分区在同一时间只能由group中的一个消费者读取，但是多个group可以同时消费这个partition。在图中，有一个由三个消费者组成的group，有一个消费者读取主题中的两个分区，另外两个分别读取一个分区。某个消费者读取某个分区，也可以叫做某个消费者是某个分区的拥有者。
在这种情况下，消费者可以通过水平扩展的方式同时读取大量的消息。另外，如果一个消费者失败了，那么其他的group成员会自动负载均衡读取之前失败的消费者读取的分区。


消费方式
: push（推）模式很难适应消费速率不同的消费者，因为消息发送速率是由broker决定的。它的目标是尽可能以最快速度传递消息，但是这样很容易造成consumer来不及处理消息，典型的表现就是拒绝服务以及网络拥塞。而pull模式则可以根据consumer的消费能力以适当的速率消费消息。
对于Kafka而言，pull模式更合适，它可简化broker的设计，consumer可自主控制消费消息的速率，同时consumer可以自己控制消费方式——即可批量消费也可逐条消费，同时还能选择不同的提交方式从而实现不同的传输语义。
pull模式不足之处是，如果kafka没有数据，消费者可能会陷入循环中，一直等待数据到达。为了避免这种情况，我们在我们的拉请求中有参数，允许消费者请求在等待数据到达的“长轮询”中进行阻塞（并且可选地等待到给定的字节数，以确保大的传输大小）。

代码案例
: 
    ```java
    
    
    ```


## Kafka producer拦截器(interceptor)

拦截器原理
: Producer拦截器(interceptor)是在Kafka 0.10版本被引入的，主要用于实现clients端的定制化控制逻辑。
对于producer而言，interceptor使得用户在消息发送前以及producer回调逻辑前有机会对消息做一些定制化需求，比如修改消息等。同时，producer允许用户指定多个interceptor按序作用于同一条消息从而形成一个拦截链(interceptor chain)。Intercetpor的实现接口是org.apache.kafka.clients.producer.ProducerInterceptor，其定义的方法包括：

 - configure(configs)
获取配置信息和初始化数据时调用。
 - onSend(ProducerRecord)：
该方法封装进KafkaProducer.send方法中，即它运行在用户主线程中。Producer确保在消息被序列化以及计算分区前调用该方法。用户可以在该方法中对消息做任何操作，但最好保证不要修改消息所属的topic和分区，否则会影响目标分区的计算
 - onAcknowledgement(RecordMetadata, Exception)：
该方法会在消息被应答或消息发送失败时调用，并且通常都是在producer回调逻辑触发之前。onAcknowledgement运行在producer的IO线程中，因此不要在该方法中放入很重的逻辑，否则会拖慢producer的消息发送效率
 - close：
关闭interceptor，主要用于执行一些资源清理工作
如前所述，interceptor可能被运行在多个线程中，因此在具体实现时用户需要自行确保线程安全。另外倘若指定了多个interceptor，则producer将按照指定顺序调用它们，并仅仅是捕获每个interceptor可能抛出的异常记录到错误日志中而非在向上传递。这在使用过程中要特别留意。



## kafka Streams
Kafka Streams。Apache Kafka开源项目的一个组成部分。是一个功能强大，易于使用的库。用于在Kafka上构建高可分布式、拓展性，容错的应用程序。

Kafka Streams特点
: 

 - 功能强大 
高扩展性，弹性，容错 
 - 轻量级 
无需专门的集群 
一个库，而不是框架
 - 完全集成 
100%的Kafka 0.10.0版本兼容
易于集成到现有的应用程序 
 - 实时性
毫秒级延迟 
并非微批处理 
窗口允许乱序数据 
允许迟到数据

为什么要有Kafka Stream
: 当前已经有非常多的流式处理系统，最知名且应用最多的开源流式处理系统有Spark Streaming和Apache Storm。Apache Storm发展多年，应用广泛，提供记录级别的处理能力，当前也支持SQL on Stream。而Spark Streaming基于Apache Spark，可以非常方便与图计算，SQL处理等集成，功能强大，对于熟悉其它Spark应用开发的用户而言使用门槛低。另外，目前主流的Hadoop发行版，如Cloudera和Hortonworks，都集成了Apache Storm和Apache Spark，使得部署更容易。
既然Apache Spark与Apache Storm拥用如此多的优势，那为何还需要Kafka Stream呢？主要有如下原因。
 
 - Spark和Storm都是流式处理框架，而Kafka Stream提供的是一个基于Kafka的流式处理类库。框架要求开发者按照特定的方式去开发逻辑部分，供框架调用。开发者很难了解框架的具体运行方式，从而使得调试成本高，并且使用受限。而Kafka Stream作为流式处理类库，直接提供具体的类给开发者调用，整个应用的运行方式主要由开发者控制，方便使用和调试。
 - 虽然Cloudera与Hortonworks方便了Storm和Spark的部署，但是这些框架的部署仍然相对复杂。而Kafka Stream作为类库，可以非常方便的嵌入应用程序中，它对应用的打包和部署基本没有任何要求。
 - 就流式处理系统而言，基本都支持Kafka作为数据源。例如Storm具有专门的kafka-spout，而Spark也提供专门的spark-streaming-kafka模块。事实上，Kafka基本上是主流的流式处理系统的标准数据源。换言之，大部分流式系统中都已部署了Kafka，此时使用Kafka Stream的成本非常低。
 - 使用Storm或Spark Streaming时，需要为框架本身的进程预留资源，如Storm的supervisor和Spark on YARN的node manager。即使对于应用实例而言，框架本身也会占用部分资源，如Spark Streaming需要为shuffle和storage预留内存。但是Kafka作为类库不占用系统资源。
 - 由于Kafka本身提供数据持久化，因此Kafka Stream提供滚动部署和滚动升级以及重新计算的能力。
 - 由于Kafka Consumer Rebalance机制，Kafka Stream可以在线动态调整并行度。



总结
: Kafka是一个分布式消息系统，每个记录包含key+value+timestap
producer：消息的生产者
consumer：消息的消费者
consumer group：消费者组
bootstrap-server：集群服务器
topic：主题
partition：分区
replication-：副本数
zookeeper：Hadoop高可用（HDFS ha，RM ha），Hbase，Kafka，Storm

kafka数据丢失问题
: 


  [1]: http://static.zybuluo.com/zhangwen100/4swzv8nm82pcg556pm2aznq0/image_1clkfilp1sf91d9b1ug3s17ao13.png
  [2]: http://mirrors.shu.edu.cn/apache/kafka/2.0.0/
  [3]: http://archive.cloudera.com/kafka/parcels/latest/
  [4]: http://static.zybuluo.com/zhangwen100/qtbayfm2hzcb4r274d75grj5/image_1csd49rn65th1b2p1c9fsga1r8a9.png
  [5]: http://static.zybuluo.com/zhangwen100/ky3ho4onnf3w70mkhpzlsyl0/image_1clrfrsma1bs71hvama4aho134v13.png
  [6]: http://static.zybuluo.com/zhangwen100/eo1ktd1lz83h42waoglk4162/image_1clrfse9s1akr1sl3ib61to22br1g.png
  [7]: http://static.zybuluo.com/zhangwen100/pid4w815hayevxyapduujgp2/image_1clrgajjd134qg0139khveqcb1t.png
  [8]: http://static.zybuluo.com/zhangwen100/9m254hyf9xgwt1673o1qactj/image_1clrgn2u4c361hi31lk7157q1o6u2a.png

