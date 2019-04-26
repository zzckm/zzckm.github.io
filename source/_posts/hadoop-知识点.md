---
title: hadoop 知识点
date: 2019-04-22 14:04:55
tags: [hadoop]
---
hadoop 分为三种
商业版  CDH cloudera hadoop--Impala相当于hive
       HDP Hortonworks版本
开源版  hadoop
block是物理的，真正存储的位置在本地磁盘{hadoop.tmp.dir}/dfs/data，是本地存储，是块文件，不是我们所谓的HDFS分布式文件系统。

Zookeeper 作为集群的高可用
Hadoop的运行模式 单机版、伪分布式、完全分布式。
Zookeeper是一个文件系统

Solr集群 全文索引
Oozie 定时调度，

azkcaban
定时调度，Java可以做 linux也可以做


mapreduce 基于磁盘 spark 基于内存