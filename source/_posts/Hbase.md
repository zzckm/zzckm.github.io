---
title: Hbase-伪分布
date: 2019-03-27 10:32:34
tags: [Hbase,Hadoop]
---
<h3>Hbase 伪分布部署</h3>
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

 (3)、启动hbase
``` bash
bin/start-hbase.sh
```

**jps**
出现三个H开启的进程则ok
![](https://i.loli.net/2019/03/27/5c9ae56bbd0f6.png)

**然后就可以进入shell进行对hbase的操作。**
``` bash 
bin/hbase shell
```


<h2></h2>