---
title: Hbase-伪分布部署
date: 2019-03-27 10:32:34
tags: [Hbase,Hadoop]
categories: hadoop
---
# Hbase 伪分布部署
伪分布模式需要用到hadoop文件系统 ，所以配置会比单机模式麻烦很多

**(1)、修改conf/hbase-env.sh**
``export HBASE_CLASSPATH=/home/lin/hadoop/hadoop-2.6.1/etc/hadoop``


**(2)、编辑hbase-site.xml**
hbase.rootdir 要配置为hdfs上的路径；hdfs://master:9000/hbase
```xml
<property>
		<name>hbase.rootdir</name>
		<value>hdfs://master:9000/hbase</value>
	</property>
	<property>
		<name>hbase.cluster.distributed</name>
		<value>true</value>
	</property>
 ```

 (3)、启动hbase·
bin/start-hbase.sh
```

**jps**
出现三个H开启的进程则ok
![](https://i.loli.net/2019/03/27/5c9ae56bbd0f6.png)

**然后就可以进入shell进行对hbase的操作。**
``` bash 
bin/hbase shell
```

```bash
 create 'index_t3','cf'

 create 't3','cf'

alter 't3','coprocessor'=>'hdfs://192.168.200.129:9000/hbase/Hbasetest-1.0-SNAPSHOT.jar|com.demo.SecondIndexObserver|1001|'
```
然后就可以测试啦，对于t3进行写入几条语句的操作，然后再看索引表里是否新增了几条对应的操作