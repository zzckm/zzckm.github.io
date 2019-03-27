---
title: ' hbase-note'
date: 2019-03-27 14:38:40
tags: [hbase]
---
<h3>Hbase笔记</h3>
HBase以表的形式存储数据。表有行和列组成。划分为若干个列族。

``create 'test','cf1','cf2'``创建表test 列族cf1,cf2   
``list`` 
查看表

``describle 'test'``  
查看表test结构

``put 'test','jack','cf1:age','18'`` 
根据表名、行键以列族进行插入某行数据

``get 'test','jack'`` 
根据键值查询数据 只能查一行

``scan 'test'`` 
扫描全表数据

``scan 'test',{STARTROW=>'JACK'}``  
从头开始扫描rowkey为jack的行的数据

``count 'test' ``
读取总行数




HBase只支持单行事务，也就是说对一行数据进行修改的时候会对行进行锁定，不想sql可以进行多行锁定