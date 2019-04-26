---
title: 'Hadoop-sqoop'
date: 2019-04-25 23:00:49
tags: [Hadoop,sqoop]
---
# Hadoop-sqoop

## sqoop 简介

算是一个Hadoop和其他关系型数据库存储之间的一个数据传输工具。

## sqoop的原理

通过shell命令，底层会将命令转换成MapReduce程序实现。主要针对InputFormat和outputformat

## sqoop的安装

下载地址
: http://mirrors.hust.edu.cn/apache/sqoop/1.4.7/

安装步骤
: 
 
 - sqoop上传至集群的某一台机器
 - 解压sqoop
 [root@master Sqoop]# tar -zxvf sqoop-1.4.7.bin__hadoop-2.6.0.tar.gz
 - 重命名
 [root@master Sqoop]# mv sqoop-1.4.7.bin__hadoop-2.6.0 sqoop-1.4.7
 - 修改配置文件sqoop-env.sh(没有需要cp)
 ```shell
 [root@master conf]# vim sqoop-env.sh
 添加以下内容
 export HIVE_HOME=/opt/apps/Hive/hive-2.3.3
 export HADOOP_COMMON_HOME=/opt/apps/Hadoop/hadoop-2.7.6
 export HADOOP_MAPRED_HOME=/opt/apps/Hadoop/hadoop-2.7.6
 ```
 - 拷贝mysql的驱动到 sqoop lib目录
 - 配置环境变量
 ```shell
 [root@master lib]# vim /etc/profile
 添加以下内容
 #Sqoop环境变量
 export SQOOP_HOME=/opt/apps/Sqoop/sqoop-1.4.7
 export PATH=$PATH:$SQOOP_HOME/bin
 [root@master lib]# source /etc/profile
 ```
 - 验证环境变量是否配置成功
 ```shell
 [root@master lib]# sqoop version
 出现以下信息
 18/08/15 10:47:54 INFO sqoop.Sqoop: Running Sqoop version: 1.4.7
 Sqoop 1.4.7
 git commit id 2328971411f57f0cb683dfb79d19d4d19d185dd8
 Compiled by maugli on Thu Dec 21 15:59:58 STD 2017
 ```
 - 简单验证sqoop是否能够连接mysql数据库
 ```shell
 [root@master lib]# sqoop list-databases --connect jdbc:mysql:///?useSSL=false --username root --password 123456
 ```

## 数据的导入

最简单的导入操作
: 从关系型数据库向大数据集群HDFS，Hive，HBase中传输数据，叫做导入。使用import关键字
    ```shell
    [root@master lib]# sqoop import \
    --connect jdbc:mysql://master:3306/mysql_bigdata?useSSL=false \
    --username root \
    --password 123456 \
    --table student \
    --target-dir /hive/mysql_bigdata \
    --delete-target-dir \
    --num-mappers 1 \
    --fields-terminated-by "\t"
    
    ```

查询导入
: 通过SQL语句将查询结果导入到HDFS
    ```shell
    [root@master lib]# sqoop import \
    > --connect jdbc:mysql://master:3306/mysql_bigdata?useSSL=false \
    > --username root \
    > --password 123456 \
    > --target-dir /hive/mysql_bigdata1 \
    > --delete-target-dir \
    > --num-mappers 1 \
    > --fields-terminated-by '\t' \
    > --query 'select * from student where id=1001 and $CONDITIONS'
    
    ```
    **注意：**
    
    - 使用query选项就不能使用table
    - 使用where的时候必须加上and $CONDITIONS
    - 不要使用双引号

导入指定的列
: 就是通过sqoop（SQL HADOOP）直接将mysql中的表中的某些列数据直接导入HDFS
    ```shell
    [root@master lib]# sqoop import \
    > --connect jdbc:mysql://master:3306/mysql_bigdata?useSSL=false \
    > --username root \
    > --password 123456 \
    > --table student \
    > --target-dir /hive/mysql_bigdata2 \
    > --delete-target-dir \
    > --num-mappers 1 \
    > --fields-terminated-by '\t' \
    > --columns id
    
    ```
**注意：**
columns如果有多列，那么就使用逗号分隔(id,name,hahah)，分割时不要添加空格(id, name, hahah)


关键字筛选导入数据
: where的用法     

    ```shell
    [root@master ~]# sqoop import \
    > --connect jdbc:mysql://master:3306/mysql_bigdata?useSSL=false \
    > --username root \
    > --password 123456 \
    > --target-dir /hive/mysql_bigdata3 \
    > --delete-target-dir \
    > --fields-terminated-by '\t' \
    > --table student \
    > --where "id=1001" \
    > -m 1
    ```

数据导入到Hive
: 

 ```shell
 [root@master ~]# sqoop import 
 --connect jdbc:mysql://master:3306/mysql_bigdata?useSSL=false 
 --username root 
 --password 123456 
 --table student 
 -m 1 
 --hive-import 
 --fields-terminated-by '\t' 
 --hive-overwrite 
 --delete-target-dir 
 --hive-table mysql_import_student
 ```
**注意:**
本过程分成两个步骤：第一步，将数据导入到HDFS上，第二步，创建表，然后将数据迁移到Hive库中


## 数据导出
```shell
[root@master ~]# sqoop export \
> --connect jdbc:mysql://master:3306/mysql_bigdata?useSSL=false \
> --username root \
> --password 123456 \
> --table student1 \
> -m 1 \
> --export-dir /hive/mysql_bigdata \
> --input-fields-terminated-by '\t'

```
**注意：**
mysql中的表如果不存在，不会自动创建。
数据是追加的。主键唯一的情况容易出现问题。


## 脚本打包
使用opt文件打包sqoop的命令。
opt文件内容
```shell
export
--connect
jdbc:mysql://master:3306/mysql_bigdata?useSSL=false
--username
root
--password
123456
--table
student
-m
1
--export-dir
/hive/mysql_bigdata
--input-fields-terminated-by
'\t'

```
**注意：**
原本要按照空格分隔的直接换行输入即可，不需要\
执行脚本
```shell
[root@master sqoop]# sqoop --options-file job_HDFS2MYSQL.opt

```


## Sqoop常用命令
|命令|描述|
|---|---|
|import|将数据导入到集群，HDFS，HIve，HBase|
|export|将集群的数据导出到传统数据库中|
|job| 用来生成一个sqoop任务的，生成后，该任务不执行，等待使用命令执行|
|list-databases|显示所有的数据库名字|
  |list-tables|显示某个数据库下的所有的表的名字
|codegen|获取数据库某张表数据生java 并且打包成jar|
|import-all-tables|将某一个数据库下的所有的表导入到集群|
|merge|将HDFS下不同目录下的数据合并在一起，然后存放到指定目录|
|metastore|记录sqoop job的元数据信息，如果不启动metastore实例，可以在sqoop-site.xml中配置相关路径|
|create-hive-table|创建Hive表|
|eval|查看SQL的运行结果|
|import-mainframe|从其他服务器上导入数据到HDFS|

## 参数详解

公共参数：数据库连接
: |参数|描述|
|---|---|
|--connect|连接数据库的URL|
|--connection-manager|使用管理类|
|--driver|手动指定要使用的JDBC驱动程序类|
|--hadoop-mapred-home|覆盖$ HADOOP_MAPRED_HOME|
|--help|打印使用说明|
|--password-file|设置包含验证密码的文件的路径|
|-P|从控制台读取密码|
|--password|设置验证密码|
|--username|设置认证用户名|
|--verbose|工作时打印更多信息

公共参数：import
: |参数|描述|
 |---|---|
|--fields-terminated-by |设定每个字段以什么样的符号结果，默认为逗号|
|--lines-terminated-by |每一行以什么样的字符结束，默认为\n|
|--append|将数据附加到HDFS中的现有数据集|
|--as-textfile|以纯文本格式导入数据（默认）|
|--columns <col,col,col…>|要从表导入的列|
|--delete-target-dir|删除导入目标目录（如果存在）|
|--table <table-name>|要阅读的表格|
|--target-dir <dir>	|HDFS目的地目录|
|--where <where clause>|导入期间要使用的WHERE子句|
|-z,--compress|启用压缩|
|--compression-codec <c>	|使用Hadoop编解码器（默认gzip）|
|-m,--num-mappers <n>|使用n个 map任务并行导入|
|-e,--query <statement>|	导入结果statement。|
|--optionally-enclosed-by <char>|给有双引号或者单引号的字段前后加上指定的字符|
|--enclosed-by|给字段的值前后加上指定的字符|
|--escaped-by|对字段中的双引号加转义符|

公用参数：export
: |参数|描述|
|---|---|
|--input-fields-terminated-by|导出数据中字段分隔符|
|--input-lines-terminated-by|导出数据中行分隔符|

公用参数：hive
: |参数|描述|
|---|---|
|--hive-import|将数据从传统数据库中导入到Hive表中|
|--hive-overwrite|覆盖已存在的数据|
|--create-hive-table|more是false，如果表已经存在，则会创建失败|
|--hive-table|hive中表的名字|
|--hive-partition-key|创建分区，后面直接跟的就是分区名，类型默认为string|
|--hive-partition-value|导入数据的时候，指定一下是哪个分区|


## 案例实操

- 增量导入
    ```shell
    [root@master sqoop]# sqoop import \
    > --connect jdbc:mysql://master:3306/mysql_bigdata?useSSL=false \
    > --username root \
    > --password 123456 \
    > --table student \
    > -m 1 \
    > --fields-terminated-by '\t' \
    > --target-dir /hive/mysql_bigdata5 \
    > --append
    
    ```
## 错误解决办法
- HIVE_CONF_DIR
```shell
export HIVE_CONF_DIR=$HIVE_HOME/conf
ClassNotFound
export HADOOP_CLASSPATH=$HADOOP_CLASSPATH:$HIVE_HOME/lib/*
```

- NoSuchMethod
```shell
##删除一下jar包，sqoop的lib目录
jackson-core-2.3.1.jar
jackson-databind-2.3.1
jackson-annotations-2.3.1.jar
```
- register相关问题
```shell
在这个文件/opt/apps/Java/jdk1.8.0_172/jre/lib/security/java.policy最后的}之前添加
permission javax.management.MBeanTrustPermission "register";

```
![image_1ckugsidd4j61pmm1vrg1q0e5lo9.png-30.2kB][1]

  
  
  


  [1]: http://static.zybuluo.com/zhangwen100/9bxz5imqqpqfea8m9j225i4a/image_1ckugsidd4j61pmm1vrg1q0e5lo9.png