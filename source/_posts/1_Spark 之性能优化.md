---
title: 'Spark 之性能优化'
date: 2019-04-25 23:11:40
tags: [Spark]
categories: Spark
---
# Spark 之性能优化

## 1. Spark的通信架构

Spark作为分布式计算框架，多个节点的设计与相互通信模式是其重要的组成部分。

Spark一开始使用 Akka 作为内部通信部件。在Spark 1.3的时候，为了解决大块数据（如Shuffle）的传输问题，Spark引入了Netty通信框架。到了 Spark 1.6, Spark可以配置使用 Akka 或者 Netty 了，这意味着 Netty 可以完全替代 Akka了。再到 Spark 2, Spark 已经完全抛弃 Akka了，全部使用Netty了。
为什么呢？官方的解释是：

- 很多Spark用户也使用Akka，但是由于Akka不同版本之间无法互相通信，这就要求用户必须使用跟Spark完全一样的Akka版本，导致用户无法升级Akka。
- Spark的Akka配置是针对Spark自身来调优的，可能跟用户自己代码中的Akka配置冲突。
- Spark用的Akka特性很少，这部分特性很容易自己实现。同时，这部分代码量相比Akka来说少很多，debug比较容易。如果遇到什么bug，也可以自己马上fix，不需要等Akka上游发布新版本。而且，Spark升级Akka本身又因为第一点会强制要求用户升级他们使用的Akka，对于某些用户来说是不现实的。





