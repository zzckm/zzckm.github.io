---
title: zookeeper部分笔记
date: 2019-03-28 11:16:43
tags: [zookeeper]
categories: zookeeper
---
## Zookeeper简介
Zookeeper 多个相同的节点，每一个里面存储的都一模一样，并且随时同步，且运行时所有数据存放在内存当中。
Zookeeper本身就是高可用，因此只要存在一个Zookeeper节点就可以正常运作


**Paxos算法解决分布式一致的问题**

**raft  zab协议**  解决zookeeper分布式不一致的问题

**高可用： 在Zookeeper中成功创建第一个相关目录 成为master 就是高可用的现象作用**
集群选择奇数，支持少于半数的宕机，少于半数的宕机，可以保持分布式一致性