---
title: 'Redis'
date: 2019-04-25 23:01:40
tags: [Redis]
categories: Redis
---
# Redis

## 1. Redis 简介

Redis 是一个开源（BSD许可）的，内存中的数据结构存储系统，它可以用作数据库、缓存和消息中间件。 它支持多种类型的数据结构，如 字符串（strings）， 散列（hashes）， 列表（lists）， 集合（sets）， 有序集合（sorted sets） 与范围查询， bitmaps， hyperloglogs 和 地理空间（geospatial） 索引半径查询。 Redis 内置了 复制（replication），LUA脚本（Lua scripting）， LRU驱动事件（LRU eviction），事务（transactions） 和不同级别的 磁盘持久化（persistence）， 并通过 Redis哨兵（Sentinel）和自动 分区（Cluster）提供高可用性（high availability）。


### 1.1 Redis 与 Memcached

Redis 是一个高性能的key-value数据库。 Redis的出现，很大程度补偿了memcached这类key-value数据库存储的不足，在部分场合可以对其他数据库起到很好的补充作用。它提供了Java，C/C++，C#，PHP，JavaScript，Perl，Object-C，Python，Ruby，Erlang等客户端，使用很方便

这里所说的数据库和传统意义上的数据库不太一样。传统意义上的数据库：SQL Server、MySQL、Oracle， 我们称之为关系型数据库（RDBMS）, 而Redis我们称之为NoSQL数据库。

Memcached和Redis都是主流的缓存数据库，在技术选型时，经常会拿出来做比较
: 

 - **Memcached**
     - 很早出现的NoSql数据库
     - 数据都在内存中，一般不持久化
     - 支持简单的key-value模式
     - 多线程+锁（memcached）
     - 一般是作为缓存数据库辅助持久化的数据库
 - Redis	
     - 几乎覆盖了Memcached的绝大部分功能
     - 数据都在内存中，支持持久化，主要用作备份恢复
     - 除了支持简单的key-value模式，还支持多种数据结构的存储，比如 list、set、hash、zset等。
     - 单线程+多路IO复用
     - 一般是作为缓存数据库辅助持久化的数据库
     
 > Redis是一个开源的key-value存储系统。和Memcached类似，它支持存储的value类型相对更多，包括string(字符串)、list(链表)、set(集合)、zset(sorted set --有序集合)和hash（哈希类型）。这些数据类型都支持push/pop、add/remove及取交集并集和差集及更丰富的操作，而且这些操作都是原子性的。在此基础上，Redis支持各种不同方式的排序。与memcached一样，为了保证效率，数据都是缓存在内存中。区别的是Redis会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件，并且在此基础上实现了master-slave(主从)同步。
     

### 1.2 Redis 的安装

下载
: [下载地址][1]

解压
: 
    ```shell
    [root@master Redis]# tar -zxvf redis-4.0.10.tar.gz
    
    ```
    
安装
: 
    ```shell
    ## 先编译，后安装。编译前请确认是否安装c，c++编译器
    
    ## 安装C编译器
    [root@master redis-4.0.10]# yum -y install gcc
    ## 安装C++编译器
    [root@master redis-4.0.10]# yum -y install gcc-c++
    ## 开始编译
    [root@master redis-4.0.10]# make MALLOC=libc
    ## 开始安装
    [root@master redis-4.0.10]# make install
    ## 进入到相应的bin目录
    [root@master redis-4.0.10]# cd /usr/local/bin/
    
    ```

命令说明
: 
    |文件|描述|
    |---|---|
    |redis-benchmark|	性能测试工具，可以在自己本子运行，看看自己本子性能如何(服务启动起来后执行)|
    |redis-check-aof|	修复有问题的AOF文件|
    |redis-check-dump|	修复有问题的dump.rdb文件|
    |redis-sentinel|	redis集群使用|
    |redis-server|	redis服务器启动命令|
    |redis-cli	|客户端，操作入口|
 
Redis启动
: 
    ```shell
    [root@master /]# redis-server
    
    ```
    
![image_1cnp7in987t21rl41ccopr4f22p.png-143kB][2]

设置后台启动
: 
```shell
## 备份配置文件
[root@master redis-4.0.10]# mkdir /myredis
[root@master redis-4.0.10]# cp redis.conf /myredis/
## 修改配置文件
[root@master redis-4.0.10]# cd /myredis/
[root@master myredis]# vim redis.conf 
## 修改daemonize no为daemonize yes
[root@master myredis]# redis-server /myredis/redis.conf
## 启动客户端
[root@master myredis]# redis-cli
## 在客户端中关闭服务端
127.0.0.1:6379> shutdown
## 直接关闭服务端
[root@master myredis]# redis-cli shutdown

```

> MySQL默认有4个数据库information_schema, performance_schema, test, mysql，用于存储自身相关的内容，如果我们想要使用，可以使用test库或根据需要创建自己的数据库。

> Redis默认提供了16个库，类似数组下标从0开始，初始默认使用0号库，可以在客户端中采用命令select <dbid> 来切换数据库，如：select 8
默认情况下，Redis没有密码就可以访问。如果考虑安全性，也可以增加密码访问。如果设定密码了，那么所有的库的密码相同，要么都OK要么一个也连接不上。


## 2. 数据类型

Redis的五大数据类型

string
: 

 - String是Redis最基本的类型，你可以理解成与Memcached一模一样的类型，一个key对应一个value
 - String类型是二进制安全的。意味着Redis的string可以包含任何数据。比如jpg图片或者序列化的对象。
 - String类型是Redis最基本的数据类型，一个Redis中字符串value最多可以是512M。

list
: 

 - 单键多值
 - Redis 列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素到列表的头部（左边）或者尾部（右边）
 - 它的底层实际是个双向链表，对两端的操作性能很高，通过索引下标的操作中间的节点性能会较差

set
: 

 - Redis set对外提供的功能与list类似是一个列表的功能，特殊之处在于set是可以自动排重的，当你需要存储一个列表数据，又不希望出现重复数据时，set是一个很好的选择，并且set提供了判断某个成员是否在一个set集合内的重要接口，这个也是list所不能提供的。
 - Redis的Set是string类型的无序集合。它底层其实是一个value为null的hash表,所以添加，删除，查找的复杂度都是O(1)。
  ![1]( http://www.redis.cn/download.html
  ![2]( http://static.zybuluo.com/zhangwen100/eqy1rd8l7v3qdrzbpsxc1l7d/image_1cnp7in987t21rl41ccopr4f22p.png)