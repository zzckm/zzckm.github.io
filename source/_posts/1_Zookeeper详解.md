---
title: 'Zookeeper详解'
date: 2019-04-25 21:00:40
tags: [Zookeeper]
categories: Zookeeper
---
# Zookeeper

## **zookeeper概述**
Zookeeper 是一个开源的分布式的，为分布式应用提供协调服务的 Apache 项目。它包含一个简单的原语集，分布式应用程序可以基于它实现同步服务，配置维护和命名
服务等

为什么要使用zookeeper
: 大部分分布式应用需要一个主控、协调器或控制器来管理物理分布的子进程（如资源、任务分配等）.目前，大部分应用需要开发私有的协调程序，缺乏一个通用的机制协调程序的反复编写浪费，且难以形成通用、伸缩性好的协调器
ZooKeeper：提供通用的分布式锁服务，用以协调分布式应用

zookeepr可以做什么
: 

 - Hadoop2.X 
 使用Zookeeper的事件处理确保整个集群只有一个活跃的NameNode,存储配置信息等.
- HBase
使用Zookeeper的事件处理确保整个集群只有一个HMaster,察觉HRegionServer联机和宕机,存储访问控制列表等.
## **zookeeper原理**

工作机制
: 看PPT

zookeeper服务
: ![image_1cl970fsd18381qa1sjt17v217kq26.png-294.7kB][1]

Zookeeper的角色
: 

 - 领导者（leader），负责进行投票的发起和决议，更新系统状态
 - 学习者（learner），包括跟随者（follower）和观察者（observer），follower用于接受客户端请求并给客户端返回结果，在选主过程中参与投票
 Observer可以接受客户端连接，将写请求转发给leader，但observer不参加投票过
程，只同步leader的状态，observer的目的是为了扩展系统，提高读取速度
 - 客户端（client），请求发起方

zookeeper的数据模型
: ![image_1cl96m4h81gkdg291in415t62sjm.png-18.7kB][2]

 - 层次化的目录结构，命名符合常规文件系统规范，类似于Linux
 - 每个节点在zookeeper中叫做znode,并且其有一个唯一的路径标识
 - 节点Znode可以包含数据和子节点，但是EPHEMERAL类型的节点不能有子节点
 - Znode中的数据可以有多个版本，比如某一个路径下存有多个数据版本，那么查询这个路径下的数据就需要带上版本
 - 客户端应用可以在节点上设置监视器
 - 节点不支持部分读写，而是一次性完整读写

zookeeper的应用场景
: 统一命名服务、统一的配置管理、统一集群管理、服务器节点动态上下线、软负载均衡等

 - 统一命名服务
 分布式应用中，通常需要有一套完整的命名规则，既能够产生唯一的名称又便于人识别和记住，通常情况下用树形的名称结构是一个理想的选择，树形的名称结构是一个有层次的目录结构，既对人友好又不会重复。
     - 类似于域名与ip之间对应关系，ip 不容易记住，而域名容易记住
     - 通过名称来获取资源或服务的地址，提供者等信息。
![image_1cl978mfe13hjki212rr19rq1fpo30.png-33.2kB][3]
- 统一的配置管理
配置的管理在分布式应用环境中很常见，例如同一个应用系统需要多台 PC Server 运行，但是它们运行的应用系统的某些配置项是相同的，如果要修改这些相同的配置项，那么就必须同时修改每台运行这个应用系统的 PC Server，这样非常麻烦而且容易出错。
将配置信息保存在 Zookeeper 的某个目录节点中，然后将所有需要修改的应用机器监控配置信息的状态，一旦配置信息发生变化，每台应用机器就会收到 Zookeeper 的通知，然后从 Zookeeper 获取新的配置信息应用到系统中。
**举例**
   分布式环境下，配置文件管理和同步是一个常见问题。
        - 一个集群中，所有节点的配置信息是一致的，比如 Hadoop 集群。
        - 对配置文件修改后，希望能够快速同步到各个节点上。
        
        配置管理可交由ZooKeeper实现。
        - 可将配置信息写入ZooKeeper上的一个znode。
        - 各个节点监听这个znode。
        - 一旦znode中的数据被修改，ZooKeeper将通知各个节点
![image_1cl97hq601s221a8r15111mlkmgo47.png-23.6kB][4]

 - 统一的集群管理
 分布式环境中，实时掌握每个节点的状态是必要的。可根据节点实时状态做出一些调整。
 可交由ZooKeeper实现。
     - 可将节点信息写入ZooKeeper上的一个Znode。
     - 监听这个Znode可获取它的实时状态变化。

        典型应用: HBase中Master状态监控与选举。
        
    - 服务器动态上下线：看PPT
    - 软负载均衡
    ![image_1cl97sh19eo3p641t3j1ncf1lp05h.png-47.1kB][5]
    
 
## zookeeper安装

- 下载
[下载地址][6]
- 配置安装
    ```shell
    [root@master Zookeeper]# tar -zxvf zookeeper-3.4.12.tar.gz
    [root@master conf]# mv zoo_sample.cfg zoo.cfg
    [root@master conf]# vim zoo.cfg
    ## 修改以下内容
    dataDir=/opt/apps/Zookeeper/data
    ## 创建相应目录
    [root@master Zookeeper]# mkdir data
    
    ```
    
配置参数
: 

 - tickTime=2000：通信心跳数，Zookeeper服务器心跳时间，单位毫秒
 Zookeeper使用的基本时间，服务器之间或客户端与服务器之间维持心跳的时间间隔，也就是每个tickTime时间就会发送一个心跳，时间单位为毫秒。
它用于心跳机制，并且设置最小的session超时时间为两倍心跳时间。
- initLimit=10：Leader和Follower初始通信时限
集群中的follower跟随者服务器与leader领导者服务器之间初始连接时能容忍的最多心跳数（tickTime的数量），用它来限定集群中的Zookeeper服务器连接到Leader的时限。
投票选举新leader的初始化时间Follower在启动过程中，会从Leader同步所有最新数据，然后确定自己能够对外服务的起始状态。
Leader允许Follower在initLimit时间内完成这个工作。
- syncLimit=5：Leader 和 Follower 同步通信时限
集群中Leader与Follower之间的最大响应时间单位，假如响应超过syncLimit * tickTime，Leader认为Follwer死掉，从服务器列表中删除Follwer。
在运行过程中，Leader负责与ZK集群中所有机器进行通信，例如通过一些心跳检测机制，来检测机器的存活状态。
如果L发出心跳包在syncLimit之后，还没有从F那收到响应，那么就认为这个F已经不在线了。
- dataDir：数据文件目录+数据持久化路径
保存内存数据库快照信息的位置，如果没有其他说明，更新的事务日志也保存到数据库。
- clientPort=2181：客户端连接端口
监听客户端连接的端口

简单操作
: ```shell
    ## 启动zookeeper服务
    [root@master Zookeeper]# zkServer.sh start
    ZooKeeper JMX enabled by default
    Using config: /opt/apps/Zookeeper/zookeeper-3.4.12/bin/../conf/zoo.cfg
    Starting zookeeper ... STARTED
    ## 查看进程
    [root@master Zookeeper]# jps
    1777 ResourceManager
    9987 QuorumPeerMain
    1413 NameNode
    10006 Jps
    1614 SecondaryNameNode
    ## 查看服务状态
    [root@master Zookeeper]# zkServer.sh status
    ZooKeeper JMX enabled by default
    Using config: /opt/apps/Zookeeper/zookeeper-3.4.12/bin/../conf/zoo.cfg
    Mode: standalone
    ## 启动客户端
    [root@master Zookeeper]# zkCli.sh
    ## 退出客户端
    [zk: localhost:2181(CONNECTED) 1] quit
    ## 停止zookeeper服务器端
    [root@master Zookeeper]# zkServer.sh stop



 ```

## **选举机制**
- 半数机制（Paxos 协议）：集群中半数以上机器存活，集群可用。所以 zookeeper 适合装在奇数台机器上。
- Zookeeper 虽然在配置文件中并没有指定 master 和 slave。但是，zookeeper 工作时，是有一个节点为 leader，其他则为 follower，Leader是通过内部的选举机制临时产生的。
- 以一个简单的例子来说明整个选举的过程。
假设有五台服务器组成的 zookeeper 集群，它们的 id 从 1-5，同时它们都是最新启动的，
![image_1cl99f22213fb170q175515uouf87v.png-89.1kB][7]
     - 服务器 1 启动，此时只有它一台服务器启动了，它发出去的包没有任何响应，所以它的选举状态一直是 LOOKING 状态。
     - 服务器 2 启动，它与最开始启动的服务器 1 进行通信，互相交换自己的选举结果，由于两者都没有历史数据，所以 id 值较大的服务器 2 胜出，但是由于没有达到超过半数以上的服务器都同意选举它(这个例子中的半数以上是 3)，所以服务器 1、2 还是继续保持LOOKING 状态。
     - 服务器 3 启动，根据前面的理论分析，服务器 3 成为服务器 1、2、3 中的老大，而与上面不同的是，此时有三台服务器选举了它，所以它成为了这次选举的 leader。
     - 服务器 4 启动，根据前面的分析，理论上服务器 4 应该是服务器 1、2、3、4 中最大的，但是由于前面已经有半数以上的服务器选举了服务器 3，所以它只能接收当小弟的命了。
     - 服务器 5 启动，同 4 一样当小弟。

## 节点类型

Znode有两种类型：
: 

 - 短暂（ephemeral）：客户端和服务器端断开连接后，创建的节点自己删除
 - 持久（persistent）：客户端和服务器端断开连接后，创建的节点不删除

Znode有四种形式的目录节点（默认是persistent ）
: 

 - 持久化目录节点（PERSISTENT）
	客户端与zookeeper断开连接后，该节点依旧存在
 - 持久化顺序编号目录节点（PERSISTENT_SEQUENTIAL）
	客户端与zookeeper断开连接后，该节点依旧存在，只是Zookeeper给该节点名称进行顺序编号
 - 临时目录节点（EPHEMERAL）
客户端与zookeeper断开连接后，该节点被删除
 - 临时顺序编号目录节点（EPHEMERAL_SEQUENTIAL）
客户端与zookeeper断开连接后，该节点被删除，只是Zookeeper给该节点名称进行顺序编号

## 完全分布式安装
在单机的基础上添加以下操作
```shell
## 在zoo.cfg中添加以下内容
##  配置集群
server.1=master:2888:3888
server.2=slave1:2888:3888
server.3=slave2:2888:3888
##配置参数详情:
## server.A=B:C:D
## A :表示一个数字，这个数字表示第几号服务器，配置在myid的文件中
## B :服务的地址，也就是IP地址，但是IP不好记，所以配置了Hosts文件，让这个IP映射为域名
## C :是本台服务器与集群中的Leader服务器交换信息的端口
## D :万一leader挂了，就需要重新来选举Leader，用这个端口进行选举投票

## 创建一个新的配置文件myid
[root@master data]# vim myid
## 添加一个数字作为本机的ID

```



## 命令行操作
```shell
[zk: localhost:2181(CONNECTED) 0] help
	stat path [watch]               #查看节点的状态
	set path data [version]         #设置节点的数据
	ls path [watch]                 #查看当前节点中所包含的内容
	ls2 path [watch]                #查看当前节点的数据并且能够看到更新次数等数据
	delete path [version]           #删除节点
	sync path                       #同步节点
	rmr path                        #递归删除节点
	get path [watch]                #获取节点的数据
	create [-s] [-e] path data acl  #创建节点并添加数据，-s带序号，-e临时节点
	quit                            #退出 
[zk: localhost:2181(CONNECTED) 14] stat /yangxiu
cZxid = 0x900000015                     #创建节点的事务ID，是Zookeeper中所有修改的总的次序，每一个修改都会有一个这样的ID
ctime = Mon Aug 20 02:52:21 EDT 2018    #创建的时间
mZxid = 0x900000015                     #最后更新的zxid
mtime = Mon Aug 20 02:52:21 EDT 2018    #最后修改的时候
pZxid = 0x900000015                     #最后更新的子节点的zxid
cversion = 0                            #更新子节点的次数（不包含对子节点数据的修改）
dataVersion = 0                         #数据的变化版本号
aclVersion = 0                          #访问控制列表的变化号
ephemeralOwner = 0x0                    #如果不是临时节点则为0，是临时节点则为拥有这的session id
dataLength = 2                          #数据长度
numChildren = 0                         #子节点的个数

```



## API操作




  [1]: http://static.zybuluo.com/zhangwen100/det0qodvd17vuvdtootp8v10/image_1cl970fsd18381qa1sjt17v217kq26.png
  [2]: http://static.zybuluo.com/zhangwen100/iih58c9bz0g3qy9y6ohpy9r4/image_1cl96m4h81gkdg291in415t62sjm.png
  [3]: http://static.zybuluo.com/zhangwen100/fi3i3bhvp0csocuf6tyixcua/image_1cl978mfe13hjki212rr19rq1fpo30.png
  [4]: http://static.zybuluo.com/zhangwen100/ad8bzqgtbwh9r754as47qnrz/image_1cl97hq601s221a8r15111mlkmgo47.png
  [5]: http://static.zybuluo.com/zhangwen100/2d92x9epb8nrgxqlnenec8u5/image_1cl97sh19eo3p641t3j1ncf1lp05h.png
  [6]: http://zookeeper.apache.org/
  [7]: http://static.zybuluo.com/zhangwen100/rgt163zys83bufcyq7a8hyot/image_1cl99f22213fb170q175515uouf87v.png