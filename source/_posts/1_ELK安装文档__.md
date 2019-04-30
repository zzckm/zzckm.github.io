---
title: ELK安装文档
date: 2019-04-25 23:00:40
tags: [ELK]
categories: ELK日志分析平台
---

**ELK安装文档**
-------

# 1. 安装准备
---
## 1.1 操作环境
    操作系统环境：Centos7 1708版本
    JDK版本：jdk-8u144-linux-x64.tar.gz
    Elasticsearch版本：5.6.3
    Logstash版本：5.6.3
    Kibana版本：5.6.3
# 2. 安装上传文件和下载文件的插件（可以不安装）
---
鉴于Elasticsearch用户只能是非root用户，因此后面的所有操作都在es账户下执行
```shell
[root@master ~]# yum -y install lrzsz
```
# 3. 安装sudo命令并配置
---
由于这里采用的是最小化安装，因此sudo命令不可用。
## 3.1 安装sudo插件
```shell
[root@master ~]# yum -y install sudo
```
## 3.2 修改`/etc/sudoers`文件的权限
```shell
[root@master ~]# chmod u+w /etc/sudoers
```
```java
package com.zhiyou100.abstrac;

public class Test {
	final static int width = 100;
	public static void main(String[] args) {
		USB.read();
	}
	public static void eat() {
		
	}
	
}

```
## 3.3 开始编辑`/etc/sudoers`文件
```shell
[root@master ~]# vi /etc/sudoers
```
## 3.4 找到`root    ALL=(ALL)       ALL`行，在下面添加如下内容
```shell
es      ALL=(ALL)       NOPASSWD: ALL
```
## 3.5 去掉`#%wheel  ALL=(ALL)         NOPASSWD: ALL`的#号
## 3.6 保存并退出
## 3.7将es用户调整至wheel用户组中，这样以后使用sudo命令的时候就不用输入密码了。
```shell
[root@master ~]# gpasswd -a es wheel
```
## 3.8 恢复`/etc/sudoers`文件的权限
```shell
[root@master ~]# chmod u-w /etc/sudoers
```
然后你就可以肆无忌惮的使用es用户了
#4. 切换至`es`用户
---
```shell
[root@slave3 ~]# su es
```
#5. 创建相应目录
---
```shell
[es@master ~]$ sudo mkdir -p /opt/SoftWare/Java
[es@master ~]$ sudo mkdir -p /opt/SoftWare/ES
[es@master ~]$ sudo mkdir -p /opt/SoftWare/Kibana
[es@master ~]$ sudo mkdir -p /opt/SoftWare/Logstash
```
#6. 关闭防火墙
---
```shell
[es@master Java]$ sudo systemctl stop firewalld
[es@master Java]$ sudo systemctl disable firewalld.service
```
#7. 安装jdk环境
---
## 7.1 上传tar包
```shell
[es@master Java]$ sudo rz
```
## 7.1 卸载JDK
## 7.2 解压jdk
```shell
[es@master Java]$ sudo tar -zxvf jdk-8u144-linux-x64.tar.gz
```
## 7.3 配置环境变量
```shell
[es@master Java]$ sudo vi /etc/profile
```
添加一下内容：
```shell
#JDK1.8
export JAVA_HOME=/opt/SoftWare/Java/jdk1.8.0_144
export JRE_HOME=/opt/SoftWare/Java/jdk1.8.0_144/jre
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin
```
使修改生效
```shell
[es@master Java]$ source /etc/profile
```
## 7.4 将master节点上的jdk远程拷贝到其他节点上
```shell
[es@master Java]$ sudo scp -r jdk1.8.0_144 root@192.168.100.101:/opt/SoftWare/Java/
[es@master Java]$ sudo scp -r jdk1.8.0_144 root@192.168.100.102:/opt/SoftWare/Java/
[es@master Java]$ sudo scp -r jdk1.8.0_144 root@192.168.100.103:/opt/SoftWare/Java/
```
#8. Elasticsearch的安装
---
Elasticsearch的安装比较简单，但是有一些细节要注意，在这里咱们采用tar包进行安装
## 8.1 将Elasticsearch上传至VM
可以在命令行中输入`rz`调出上传窗口即可：如`[root@master ES]# rz`。
## 8.2 解压Elasticsearch压缩包
```shell
[root@master ES]# tar zxvf elasticsearch-5.6.3.tar.gz
```
## 8.3 创建相关目录并赋权限给es
```shell
[root@master ~]# mkdir -p /var/elasticsearch/log
[root@master ~]# mkdir -p /var/elasticsearch/data
[root@master ~]# mkdir -p /var/elasticsearch/plugins
[root@master ~]# chown -R es:es /opt/SoftWare/ES/elasticsearch-5.6.3
[root@master ~]# chown -R es:es /var/elasticsearch/log
[root@master ~]# chown -R es:es /var/elasticsearch/data
[root@master ~]# chown -R es:es /var/elasticsearch/plugins
```
## 8.4 配置核心文件
```shell
################################### Cluster ################################### 
# 代表一个集群,集群中有多个节点,其中有一个为主节点,这个主节点是可以通过选举产生的,主从节点是对于集群内部来说的. 
# es的一个概念就是去中心化,字面上理解就是无中心节点,这是对于集群外部来说的,因为从外部来看es集群,在逻辑上是个整体,你与任何一个节点的通信和与整个es集群通信是等价的。 
# cluster.name可以确定集群名称,当elasticsearch集群在同一个网段中,elasticsearch会自动的找到具有相同cluster.name的elasticsearch服务,这个时候需要开启多播服务才行（不建议）
# 所以当同一个网段具有多个elasticsearch集群时cluster.name就成为同一个集群的标识. 
#此为必须修改项
cluster.name: zhiyou 
#################################### Node ##################################### 
# 节点名称同理,可自动生成也可手动配置. （建议手动配置）
node.name: node0

# 允许此节点是否可以成为一个master节点,es是默认集群中的第一台机器为master,如果这台机器停止就会重新选举master. 在新版本中默认没有此参数，但是可以直接写上，不写默认为true
node.master: true 

# 允许该节点存储数据(默认开启，同上) 
node.data: true 

# 配置文件中给出了三种配置高性能集群拓扑结构的模式,如下： 
# 1. 如果你想让节点从不选举为主节点,只用来存储数据,可作为负载器 
# node.master: false 
# node.data: true 
# node.ingest: true  #默认true

# 2. 如果想让节点成为主节点,且不存储任何数据,并保有空闲资源,可作为协调器 
# node.master: true 
# node.data: false
# node.ingest: true

# 3. 如果想让节点既不称为主节点,又不成为数据节点,那么可将他作为搜索器,从节点中获取数据,生成搜索结果等 
# node.master: false 
# node.data: false 
# node.ingest: true
#

# 4. 仅作为协调器 
# node.master: false 
# node.data: false
# node.ingest: false

# 监控集群状态有一下插件和API可以使用: 
# 每个节点都可以定义一些与之关联的通用属性，用于后期集群进行碎片分配时的过滤
# node.rack: rack314 

# 设置一台服务器能运行的节点数,一般为1就好,因为一般情况下一台机器只跑一个节点
node.max_local_storage_nodes: 1 

#################################### Index #################################### 
# 设置索引的分片数,默认为5 
index.number_of_shards: 5 

# 设置索引的副本数,默认为1: 
index.number_of_replicas: 2 

# 配置文件中提到的最佳实践是,如果服务器够多,可以将分片提高,尽量将数据平均分布到大集群中去
# 同时,如果增加副本数量可以有效的提高搜索性能 
# 需要注意的是,"number_of_shards" 是索引创建后一次生成的,后续不可更改设置 
# "number_of_replicas" 是可以通过API去实时修改设置的 

#################################### Paths #################################### 
# 配置文件存储位置 
# path.conf: /path/to/conf 

# 数据存储位置(单个目录设置) 
path.data: /var/elasticsearch/data 
# 多个数据存储位置,有利于性能提升 
# path.data: /path/to/data1,/path/to/data2 

# 临时文件的路径 
# path.work: /path/to/work 

# 日志文件的路径 
path.logs: /var/elasticsearch/log 

# 插件安装路径 
# path.plugins: /var/elasticsearch/plugins 

################################### Memory #################################### 
# 当JVM开始写入交换空间时（swapping）ElasticSearch性能会低下,你应该保证它不会写入交换空间 
# 设置这个属性为true来锁定内存,同时也要允许elasticsearch的进程可以锁住内存,linux下可以通过 `ulimit -l unlimited` 命令 
bootstrap.mlockall: true 

# 确保 ES_MIN_MEM 和 ES_MAX_MEM 环境变量设置为相同的值,以及机器有足够的内存分配给Elasticsearch 
# 注意:内存也不是越大越好,一般64位机器,最大分配内存别才超过32G 

############################## Network And HTTP ############################### 
# 设置绑定的ip地址,可以是ipv4或ipv6的,默认为0.0.0.0 
network.bind_host: 192.168.100.100   #只有本机可以访问http接口

# 设置其它节点和该节点交互的ip地址,如果不设置它会自动设置,值必须是个真实的ip地址 
network.publish_host: 192.168.100.100 

# 同时设置bind_host和publish_host上面两个参数 
network.host: 192.168.100.100    #绑定监听IP
# 设置节点间交互的tcp端口,默认是9300 
transport.tcp.port: 9300 

# 设置是否压缩tcp传输时的数据，默认为false,不压缩
transport.tcp.compress: true 

# 设置对外服务的http端口,默认为9200 
http.port: 9200 

# 设置请求内容的最大容量,默认100mb 
# http.max_content_length: 100mb 

# 使用http协议对外提供服务,默认为true,开启 
# http.enabled: false 

###################### 使用head等插件监控集群信息，需要打开以下配置项 ###########
http.cors.enabled: true
http.cors.allow-origin: "*"
http.cors.allow-credentials: true

################################### Gateway ################################### 
# gateway的类型,默认为local即为本地文件系统,可以设置为本地文件系统 
# gateway.type: local 

# 下面的配置控制怎样以及何时启动一整个集群重启的初始化恢复过程 
# (当使用shard gateway时,是为了尽可能的重用local data(本地数据)) 

# 一个集群中的N个节点启动后,才允许进行恢复处理 
# gateway.recover_after_nodes: 1 

# 设置初始化恢复过程的超时时间,超时时间从上一个配置中配置的N个节点启动后算起 
# gateway.recover_after_time: 5m 

# 设置这个集群中期望有多少个节点.一旦这N个节点启动(并且recover_after_nodes也符合), 
# 立即开始恢复过程(不等待recover_after_time超时) 
# gateway.expected_nodes: 2

 ############################# Recovery Throttling ############################# 
# 下面这些配置允许在初始化恢复,副本分配,再平衡,或者添加和删除节点时控制节点间的分片分配 
# 设置一个节点的并行恢复数 
# 1.初始化数据恢复时,并发恢复线程的个数,默认为4 
# cluster.routing.allocation.node_initial_primaries_recoveries: 4 

# 2.添加删除节点或负载均衡时并发恢复线程的个数,默认为2 
# cluster.routing.allocation.node_concurrent_recoveries: 2 

# 设置恢复时的吞吐量(例如:100mb,默认为0无限制.如果机器还有其他业务在跑的话还是限制一下的好) 
# indices.recovery.max_bytes_per_sec: 20mb 

# 设置来限制从其它分片恢复数据时最大同时打开并发流的个数,默认为5 
# indices.recovery.concurrent_streams: 5 
# 注意: 合理的设置以上参数能有效的提高集群节点的数据恢复以及初始化速度 

################################## Discovery ################################## 
# 设置这个参数来保证集群中的节点可以知道其它N个有master资格的节点.默认为1,对于大的集群来说,可以设置大一点的值(2-4) 
# discovery.zen.minimum_master_nodes: 1 
# 探查的超时时间,默认3秒,提高一点以应对网络不好的时候,防止脑裂 
# discovery.zen.ping.timeout: 3s 

# 设置是否打开多播发现节点.默认是true. (尽可能使用单播模式)
# 当多播不可用或者集群跨网段的时候集群通信还是用单播吧 
# discovery.zen.ping.multicast.enabled: false 

# 这是一个集群中的主节点的初始列表,当节点(主节点或者数据节点)启动时使用这个列表进行探测 
discovery.zen.ping.unicast.hosts: ["192.168.100.100:9300", "192.168.100.101:9300", "192.168.100.102:9300", "192.168.100.103:9300"] 

# Slow Log部分与GC log部分略,不过可以通过相关日志优化搜索查询速度 

################  X-Pack ###########################################
# 官方插件 相关设置请查看此处
############## Memory(重点需要调优的部分) ################ 
# Cache部分: 
# es有很多种方式来缓存其内部与索引有关的数据.其中包括filter cache 

# filter cache部分: 
# filter cache是用来缓存filters的结果的.默认的cache type是node type.node type的机制是所有的索引内部的分片共享filter cache.node type采用的方式是LRU方式.即:当缓存达到了某个临界值之后，es会将最近没有使用的数据清除出filter cache.使让新的数据进入es. 

# 这个临界值的设置方法如下：indices.cache.filter.size 值类型：eg.:512mb 20%。默认的值是10%。 

# out of memory错误避免过于频繁的查询时集群假死 
# 1.设置es的缓存类型为Soft Reference,它的主要特点是据有较强的引用功能.只有当内存不够的时候,才进行回收这类内存,因此在内存足够的时候,它们通常不被回收.另外,这些引用对象还能保证在Java抛出OutOfMemory异常之前,被设置为null.它可以用于实现一些常用图片的缓存,实现Cache的功能,保证最大限度的使用内存而不引起OutOfMemory.在es的配置文件加上index.cache.field.type: soft即可. 

# 2.设置es最大缓存数据条数和缓存失效时间,通过设置index.cache.field.max_size: 50000来把缓存field的最大值设置为50000,设置index.cache.field.expire: 10m把过期时间设置成10分钟. 
# index.cache.field.max_size: 50000 
# index.cache.field.expire: 10m 
# index.cache.field.type: soft 

# field data部分&&circuit breaker部分： 
# 用于fielddata缓存的内存数量,主要用于当使用排序,faceting操作时,elasticsearch会将一些热点数据加载到内存中来提供给客户端访问,但是这种缓存是比较珍贵的,所以对它进行合理的设置. 

# 可以使用值：eg:50mb 或者 30％(节点 node heap内存量),默认是：unbounded #indices.fielddata.cache.size： unbounded 
# field的超时时间.默认是-1,可以设置的值类型: 5m #indices.fielddata.cache.expire: -1 

# circuit breaker部分: 
# 断路器是elasticsearch为了防止内存溢出的一种操作,每一种circuit breaker都可以指定一个内存界限触发此操作,这种circuit breaker的设定有一个最高级别的设定:indices.breaker.total.limit 默认值是JVM heap的70%.当内存达到这个数量的时候会触发内存回收

# 另外还有两组子设置： 
#indices.breaker.fielddata.limit:当系统发现fielddata的数量达到一定数量时会触发内存回收.默认值是JVM heap的70% 
#indices.breaker.fielddata.overhead:在系统要加载fielddata时会进行预先估计,当系统发现要加载进内存的值超过limit * overhead时会进行进行内存回收.默认是1.03 
#indices.breaker.request.limit:这种断路器是elasticsearch为了防止OOM(内存溢出),在每次请求数据时设定了一个固定的内存数量.默认值是40% 
#indices.breaker.request.overhead:同上,也是elasticsearch在发送请求时设定的一个预估系数,用来防止内存溢出.默认值是1 

# Translog部分: 
# 每一个分片(shard)都有一个transaction log或者是与它有关的预写日志,(write log),在es进行索引(index)或者删除(delete)操作时会将没有提交的数据记录在translog之中,当进行flush 操作的时候会将tranlog中的数据发送给Lucene进行相关的操作.一次flush操作的发生基于如下的几个配置 
#index.translog.flush_threshold_ops:当发生多少次操作时进行一次flush.默认是 unlimited #index.translog.flush_threshold_size:当translog的大小达到此值时会进行一次flush操作.默认是512mb 
#index.translog.flush_threshold_period:在指定的时间间隔内如果没有进行flush操作,会进行一次强制flush操作.默认是30m #index.translog.interval:多少时间间隔内会检查一次translog,来进行一次flush操作.es会随机的在这个值到这个值的2倍大小之间进行一次操作,默认是5s 
#index.gateway.local.sync:多少时间进行一次的写磁盘操作,默认是5s 
```
一般情况下，配置以下内容即可：编辑`elasticsearch.yml`文件
```shell
[root@master config]# vi elasticsearch.yml
# 添加一下内容：
# 集群名称
cluster.name: zhiyou
# 节点名称
node.name: node0
# 数据文件的路径
path.data: /var/elasticsearch/data
# 日志文件的路径
path.logs: /var/elasticsearch/log
# 锁定内存
bootstrap.memory_lock: false
# 本节点的IP即本机的IP
network.host: 192.168.100.100
# 访问端口号，API交互的端口
http.port: 9200
# 设置自动创建索引
action.auto_create_index: true
# 设置transport交互的IP和端口号
transport.host: 192.168.100.100
transport.tcp.port: 9300
transport.tcp.compress: true
# 单播模式发现其他节点，注意为9300端口
discovery.zen.ping.unicast.hosts: ["192.168.100.100:9300", "192.168.100.101:9300", "192.168.100.102:9300", "192.168.100.103:9300"]
#
discovery.zen.minimum_master_nodes: 3
# 设置下面的参数，这样head插件才能使用
http.cors.enabled: true
http.cors.allow-origin: "*"
http.cors.allow-credentials: true
```
## 8.5 配置JVM
```shell
[root@master config]# vi jvm.options
# 修改为以下设置：因为我的虚拟机给的内存为2G
-Xms1g
-Xmx1g
```
## 8.6 配置数据节点
很明显，前面那一台节点咱们设置的是不存储数据的，一般来说咱们都会固定master节点，如果条件允许，可以多设置几台都行。在这里，我只配置了一台master节点，其余的三个节点设置为数据节点
在master节点上使用远程拷贝将elasticsearch主目录拷贝到其他节点上
```shell
[root@master ES]# scp -r elasticsearch-5.6.3 root@192.168.100.101:/opt/SoftWare/ES/
[root@master ES]# scp -r elasticsearch-5.6.3 root@192.168.100.102:/opt/SoftWare/ES/
[root@master ES]# scp -r elasticsearch-5.6.3 root@192.168.100.103:/opt/SoftWare/ES/
```
在数据节点上修改相关配置,其余的可以不修改了
```shell
# 节点名称
node.name: node1
# 本节点的IP即本机的IP
network.host: 192.168.100.101
# 设置transport交互的IP和端口号
transport.host: 192.168.100.101
```
## 8.7 给数据节点创建相关目录并赋权限给es
```shell
[root@master ~]# mkdir -p /var/elasticsearch/log
[root@master ~]# mkdir -p /var/elasticsearch/data
[root@master ~]# chown -R es:es /opt/SoftWare/ES/elasticsearch-5.6.3
[root@master ~]# chown -R es:es /var/elasticsearch/log
[root@master ~]# chown -R es:es /var/elasticsearch/data
```
## 8.8 修改系统配置文件（每台节点均需要配置并重启生效）
```shell
#编辑文件1：/etc/security/limits.conf
[root@slave3 ~]# vi /etc/security/limits.conf   添加以下内容
*       hard    nofile  131072
*       soft    nofile  65536
*       hard    nproc   4096
*       soft    nproc   2048
es      soft    memlock unlimited
es      hard    memlock unlimited
#编辑文件1：/etc/sysctl.conf    添加以下内容
[root@slave3 ~]# vi /etc/sysctl.conf
vm.max_map_count=262144
```
## 8.9 可能的错误集锦：
### 8.9.1 can not run elasticsearch as root
```shell
#错误详情：
[2017-11-01T10:32:44,124][WARN ][o.e.b.ElasticsearchUncaughtExceptionHandler] [node2] uncaught exception in thread [main]
org.elasticsearch.bootstrap.StartupException: java.lang.RuntimeException: can not run elasticsearch as root
#解决方案：
不要使用root用户启动，使用非root用户启动方可解决问题
```
### 8.9.2 Unable to lock JVM Memory: error=12, reason=无法分配内存
```shell
#错误详情：
[2017-11-01T10:32:04,993][WARN ][o.e.b.JNANatives         ] Unable to lock JVM Memory: error=12, reason=无法分配内存
[2017-11-01T10:32:04,998][WARN ][o.e.b.JNANatives         ] This can result in part of the JVM being swapped out.
[2017-11-01T10:32:04,998][WARN ][o.e.b.JNANatives         ] Increase RLIMIT_MEMLOCK, soft limit: 65536, hard limit: 65536
[2017-11-01T10:32:04,998][WARN ][o.e.b.JNANatives         ] These can be adjusted by modifying /etc/security/limits.conf, for example: 
	# allow user 'es' mlockall
	es soft memlock unlimited
	es hard memlock unlimited
#解决方案：
编辑文件/etc/security/limits.conf，添加一下内容（重启生效）
[root@slave3 ~]# vi /etc/security/limits.conf
es      soft    memlock unlimited
es      hard    memlock unlimited
```
### 8.9.3 ERROR: [1] bootstrap checks failed
```shell
#错误详情：
[2017-11-01T11:18:54,289][INFO ][o.e.b.BootstrapChecks    ] [node3] bound or publishing to a non-loopback or non-link-local address, enforcing bootstrap checks
ERROR: [1] bootstrap checks failed
[1]: max file descriptors [4096] for elasticsearch process is too low, increase to at least [65536]
#解决方案：
编辑文件/etc/security/limits.conf，添加以下内容（重启生效）
[root@slave3 ~]# vi /etc/security/limits.conf
*       hard    nofile  131072
*       soft    nofile  65536
*       hard    nproc   4096
*       soft    nproc   2048
```
### 8.9.4 ERROR: [1] bootstrap checks failed
```shell
#错误详情：
[2017-11-01T11:18:54,289][INFO ][o.e.b.BootstrapChecks    ] [node3] bound or publishing to a non-loopback or non-link-local address, enforcing bootstrap checks
ERROR: [1] bootstrap checks failed
[1]: max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]
#解决方案：
编辑文件/etc/sysctl.conf，添加以下内容（重启生效）
[root@slave3 ~]# vi /etc/sysctl.conf
vm.max_map_count=262144
```
### 8.9.5 access denied ("javax.management.MBeanTrustPermission" "register")
```shell
#错误详情：
2017-11-01 10:46:27,754 main ERROR Could not register mbeans java.security.AccessControlException: access denied ("javax.management.MBeanTrustPermission" "register")
	at java.security.AccessControlContext.checkPermission(AccessControlContext.java:472)
	at java.lang.SecurityManager.checkPermission(SecurityManager.java:585)
	at com.sun.jmx.interceptor.DefaultMBeanServerInterceptor.checkMBeanTrustPermission(DefaultMBeanServerInterceptor.java:1848)
#解决方案：
给elasticsearch-5.6.3的主目录分配权限
[es@master elasticsearch-5.6.3]$ sudo chown -R es:es elasticsearch-5.6.3
```
## 8.10 测试集群环境是否搭建成功
```shell
#每台节点都进入到es主目录，使用es用户
[es@master elasticsearch-5.6.3]# bin/elasticsearch
#在浏览器的地址栏中输入
http://192.168.100.103:9200/
#出现以下内容，说明当前es节点已启动
{
  "name" : "node3",
  "cluster_name" : "zhiyou",
  "cluster_uuid" : "7kO5OPi8RoWUuXfEBYPITw",
  "version" : {
    "number" : "5.6.3",
    "build_hash" : "1a2f265",
    "build_date" : "2017-10-06T20:33:39.012Z",
    "build_snapshot" : false,
    "lucene_version" : "6.6.1"
  },
  "tagline" : "You Know, for Search"
}
#在浏览器中输入
http://192.168.100.100:9200/_cluster/health
#出现以下内容，说明es集群已搭建成功（看status和number_of_nodes是否准确）
{"cluster_name":"zhiyou","status":"green","timed_out":false,"number_of_nodes":4,"number_of_data_nodes":4,"active_primary_shards":0,"active_shards":0,"relocating_shards":0,"initializing_shards":0,"unassigned_shards":0,"delayed_unassigned_shards":0,"number_of_pending_tasks":0,"number_of_in_flight_fetch":0,"task_max_waiting_in_queue_millis":0,"active_shards_percent_as_number":100.0}
```
#9. 安装Head插件
---
## 9.1 下载
[点击下载](https://codeload.github.com/mobz/elasticsearch-head/zip/master)
## 9.2 解压
由于最小化安装，故没有解压zip文件的命令，首先安装unzip解压软件，然后再进行解压
```shell
[es@master plugins]$ sudo yum install zip unzip
[es@master plugins]$ unzip elasticsearch-head-master.zip 
```
## 9.3 安装HTTP
由于这个插件就是HTML5写的，就是一个前端的一个东西，只需要set到HTTP服务中即可
```shell
#安装HTTP
[es@master plugins]$ sudo yum install httpd -y
```
## 9.4 复制head到HTTP的指定目录
```shell
[es@master ~]$ cd /var/www/html
[es@master html]$ sudo cp -r /opt/SoftWare/ES/plugins/head /var/www/html/
```
## 9.5 启动ES集群和HTTPD服务
```shell

[es@master ~]# sudo systemctl restart httpd
```
访问IP地址如下说明已经启动，可以设置开机自启
![image_1btt80jm0qspmmrk121ksqp559.png-555.7kB][1]

```shell
# 启动ES集群
[es@master ~]# cd /opt/SoftWare/ES/elasticsearch-5.6.3
[es@master elasticsearch-5.6.3]# bin/elasticsearch
```
出现下面的类似界面
![image_1btt9a90v8qaqfo1a5i130htnm.png-111.2kB][2]
# 10. 安装Kibana
---
## 10.1 下载
[单击下载](https://artifacts.elastic.co/downloads/kibana/kibana-5.6.3-linux-x86_64.tar.gz)
## 10.2 解压
```shell
[root@master Kibana]# tar kibana-5.6.3-linux-x86_64.tar.gz
# 名字太长了也不好，就mv一下了
[root@master Kibana]# mv kibana-5.6.3-linux-x86_64 kibana-5.6.3
```
## 10.3 配置
```shell
[root@master Kibana]# cd kibana-5.6.3

#修改一下内容即可
# Kibana服务端口
server.port: 5601
# kibana访问的IP
server.host: "192.168.100.100"
#elasticsearch的master地址
elasticsearch.url: "http://192.168.100.100:9200"
#kibana默认的元数据索引
kibana.index: ".kibana"
```
## 10.4 启动Kibana
```shell
[root@master kibana-5.6.3]# bin/kibana
```
出现下面的界面，可能会因为数据的问题有点不一样
![image_1btta457a3d01e371c3fn3c1dav13.png-45.8kB][3]
![image_1btta50r5vikqra135r1dv7lfp20.png-199.8kB][4]
第一次启动会提示配置一个索引，其实就是默认的一个搜索位置

# 11. 安装Logstash
---
## 11.1 下载Logstash
[点击下载](https://artifacts.elastic.co/downloads/logstash/logstash-5.6.3.tar.gz)
## 11.2 解压缩
```shell
[root@master Logstash]# tar -zxvf logstash-5.6.3.tar.gz
```
## 11.3 运行Logstash
```shell
# 在这里可以使用标准输入线测试一下logstash
[root@master logstash-5.6.3]# bin/logstash -e 'input { stdin{} } output {stdout{} }'
```
出现下面的内容说明启动成功
![image_1bttcndg518kq1enu8tg3v14f32d.png-16.1kB][5]
然后练习以下输入输出
### 11.3.1 codec
```shell
[root@master logstash-5.6.3]# bin/logstash -e 'input { stdin{} } output {stdout{codec => rubydebug} }'
```
![image_1bttcvo60152g36i5oa19ei1u7v2q.png-45.7kB][6]
### 11.3.2 输出es集群中
注意：老版本和新版本不同，注意看[官方文档](https://www.elastic.co/guide/en/logstash/current/config-examples.html)
```shell
[root@master logstash-5.6.3]# bin/logstash -e 'input { stdin{} } output {elasticsearch { hosts => ["192.168.100.100:9200"] } stdout{} }'
```
# 12. 使用ELK进行整合操作
## 12.1 编写数据读取的配置文件
```shell
[root@master logstash-5.6.3]# vi /var/logstash/config/std.conf
```
添加如下内容
```json
input {
 stdin {}
}

output {
 elasticsearch {
   hosts => ["192.168.100.100:9200"]
 }
 stdout {
   codec => rubydebug
 }
}
```
应用
```shell
[root@master logstash-5.6.3]# bin/logstash -f /var/logstash/config/std.conf
```
## 12.2 file输入详解
```shell
#监听文件的路径
path => ["/var/logstash/data/*","/var/logstash/test.log"]
#排除不想监听的文件
exclude => "1.log"
#添加自定义的字段
add_field => {"test"=>"test"}
#增加标签
tags => "tag1"
#设置新事件的标志
delimiter => "\n"
#设置多长时间扫描目录，发现新文件
discover_interval => 15
#设置多长时间检测文件是否修改
stat_interval => 1
#监听文件的起始位置，默认是end
start_position => beginning
#监听文件读取信息记录的位置
sincedb_path => "/var/logstash/test.log"
#设置多长时间会写入读取的位置信息
sincedb_write_interval => 15
```


  ![1](http://static.zybuluo.com/zhangwen100/psxk09q4hdm57j07beib6h3p/image_1btt80jm0qspmmrk121ksqp559.png)
  ![2](http://static.zybuluo.com/zhangwen100/f6ich2owz7dnzerr0t0t6mep/image_1btt9a90v8qaqfo1a5i130htnm.png)
  ![3](http://static.zybuluo.com/zhangwen100/rhssfvrdloocdimd8tcvt4ln/image_1btta457a3d01e371c3fn3c1dav13.png)
 ![4](http://static.zybuluo.com/zhangwen100/jbknyal00b9licrsl01ekc5h/image_1btta50r5vikqra135r1dv7lfp20.png)
  ![5](http://static.zybuluo.com/zhangwen100/4a1srf8cinw3a5mqdn964gyw/image_1bttcndg518kq1enu8tg3v14f32d.png)
  ![6](http://static.zybuluo.com/zhangwen100/92eyje4tnjw7wa51bruesb9n/image_1bttcvo60152g36i5oa19ei1u7v2q.png)
  ![7](http://static.zybuluo.com/zhangwen100/cfwumzoreip8u0snqqu0ajcc/image_1c12nrt1g12vc1na5nki1isrfoc16.png)