---
title: CDH安装文档-Cloudera Manager
date: 2019-04-25 23:00:42
tags: [CDH]
categories: 大数据应用
---
# CDH安装文档

## 前言
### Cloudera Manager 架构
![image_1cr7ui3hq12jp1qtmcic19c1sa39.png-67.2kB][1]

Cloudera Manager 的架构如上图所示（cs结构），主要由如下几部分组成:

- 服务端/Server:
 Cloudera Manager 的核心。主要用于管理 web server 和应 用逻辑。它用于安装软件，配置，开始和停止服务，以及管理服务运行的集群。

- 代理/agent:
安装在每台主机上。它负责启动和停止的进程，部署配置，触发安装和监控主机。

- 数据库/Database:
存储配置和监控信息。通常可以在一个或多个数据库服务器上运行的多个逻辑数据库。例如，所述的 Cloudera 管理器服务和监视，后台程序使用不同的逻辑数据库。

- Cloudera Repository: ：由cloudera manager 提供的软件分发库

- 客户端/Clients:
提供了一个与 Server 交互的接口:

- 管理平台/Admin Console:提供一个管理员管理集群和 Cloudera Manage 的基于网页的交互界面。
- API:为开发者提供了创造自定义 Cloudera Manager 程序的 API。


## 1. 集群规划

集群节点分配
: 
  |主机名|主机IP|主机内存|硬盘存储|
|---|---|---|---|
|node01|192.168.100.101|7G|70G|
|node02|192.168.100.102|4G|50G|
|node03|192.168.100.103|4G|50G|
|node04|192.168.100.104|4G|50G|
|node05|192.168.100.105|4G|50G|

软件版本
:   
  |软件名称|版本号|
|---|---|
|Java|1.8.0_162|
|MySQL|5.7.24|
|CentOS|CentOS-7-x86_64-DVD-1804|
|CDH|5.15.1|

各软件安装路径
:  
  |软件名|路径地址|
|---|---|
|CM|/opt/apps/cm-5.15.1|
|Java|/opt/apps/Java|


## 2. 软件下载

### 2.1 JDK 下载

jdk-8u162-linux-x64.rpm 下载
[下载地址][2]

注意
: 

    1. JDK必须是64位。不要使用32位JDK。
    2. 已安装的JDK必须是受支持的版本，如[CDH和Cloudera Manager支持的JDK版本][3]中所述。
    3. 在相同版本的JDK必须安装在每个集群主机上。
    4. 这里建议下载rpm版本，自动安装会更好一些

### 2.2 MySQL 下载
以下组件都需要数据库：Cloudera Manager Server，Oozie Server，Sqoop Server，活动监视器，Reports Manager，Hive Metastore Server，Hue Server，Sentry Server，Cloudera Navigator Audit Server和Cloudera Navigator Metadata Server。数据库中包含的数据类型及其相对大小如下：

- Cloudera Manager Server - 包含有关已配置的服务及其角色分配，所有配置历史记录，命令，用户和正在运行的进程的所有信息。这个是相对较小的数据库（<100 MB），非常重要，必须备份。**注意：**重新启动进程时，将使用Cloudera Manager数据库中保存的信息重新部署每个服务的配置。如果此信息不可用，则集群无法启动或正常运行。您必须安排并维护Cloudera Manager数据库的定期备份，以便在丢失此数据库时恢复集群。
- Oozie Server - 包含Oozie工作流，协调器和捆绑数据。可以长得很大。
- Sqoop Server - 包含连接器，驱动程序，链接和作业等实体。相对较小。
- Activity Monitor - 包含有关过去活动的信息。在大型集群中，此数据库可能会变大。只有部署了MapReduce服务，才需要配置活动监视器数据库。
- Reports Manager - 跟踪磁盘利用率和处理活动。中型。
- Hive Metastore Server - 包含Hive元数据。相对较小。
- Hue服务器 - 包含用户帐户信息，作业提交和Hive查询。相对较小。
- Sentry Server - 包含授权元数据。相对较小。
- Cloudera Navigator Audit Server - 包含审计信息。在大型集群中，此数据库可能会变大。
- Cloudera Navigator Metadata Server - 包含授权，策略和审计报告元数据。相对较小。

mysql-5.7.24-1.el7.x86_64.rpm-bundle.tar.gz
[下载地址][4]

### 2.3 CDH 相关下载

[manifest.json][5]
[CDH-5.15.1-1.cdh5.15.1.p0.4-el7.parcel][6]
[CDH-5.15.1-1.cdh5.15.1.p0.4-el7.parcel.sha1][7]
[cloudera-manager-centos7-cm5.15.1_x86_64.tar.gz][8]



## 3. 基本配置

### 3.0 网路配置
此配置在这里不再过多赘述，在centos 7安装过程中就可以将网络配置完成

### 3.1 修改主机名并配置hosts（所有节点）
```shell
## 修改主机名（这里使用的是centos 7的命令，建议在安装的时候就配置好）
[root@localhost ~]# hostnamectl set-hostname node01

## 修改hosts文件
[root@node01 ~]# vim /etc/hosts
## 注释前两行，添加以下内容
#127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
#::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
192.168.100.101 node01
192.168.100.102 node02
192.168.100.103 node03
192.168.100.104 node04
192.168.100.105 node05
```

### 3.2 小工具安装（所有节点）
```shell
## 安装vim编辑器
[root@node01 ~]# yum -y install vim

## 安装上传下载工具
[root@node01 ~]# yum -y install lrzsz

## 安装pstree
[root@node01 ~]# yum -y install psmisc

[root@node01 init.d]# yum -y install httpd
[root@node01 init.d]# yum -y install mod_ssl
[root@node01 init.d]# yum -y install rpcbind
[root@node01 ~]# systemctl start rpcbind
[root@node01 java]# yum install -y python-lxml
```


### 3.3 NTP服务器配置（所有节点，用于几个节点的时间同步）
集群中所有主机必须保持时间同步，如果时间相差较大会引起各种问题。 
```shell
[root@node01 ~]# vim  /etc/ntp.conf
## 修改内容为
server 0.pool.ntp.org
server 1.pool.ntp.org
server 2.pool.ntp.org
[root@node01 ~]# systemctl start ntpd
[root@node01 ~]# systemctl restart ntpd
[root@node01 ~]# systemctl enable ntpd
[root@node01 ~]# ntpdate -u time.nist.gov
 2 Nov 13:12:53 ntpdate[31560]: step time server 132.163.96.1 offset 1812.928750 sec
[root@node01 ~]# hwclock --systohc

```

### 3.4 关闭防火墙（所有节点）
```shell
## 关闭防火墙
[root@node01 ~]# systemctl stop firewalld
## 禁止开机启动防火墙
[root@node01 ~]# systemctl disable firewalld
## 关闭SELinux（重启生效）
## SELinux主要作用就是最大限度地减小系统中服务进程可访问的资源（最小权限原则）。如果开启，可能会导致文件权限修改不了等问题
[root@node01 ~]# vim /etc/selinux/config
SELINUX=disabled


```



### 3.5 SSH免密码（所有节点）
```shell
## 先ssh一下，产生相应的隐藏目录
[root@node01 Java]# ssh localhost
## 进入到用户主目录的.ssh目录
[root@node01 Java]# cd ~/.ssh
## 产生公钥和私钥
[root@node01 .ssh]# ssh-keygen -t rsa -P ''
## 将每台机器上的id_rsa.pub公钥内容复制到authorized_keys文件中
[root@node01 .ssh]# cp id_rsa.pub authorized_keys
## 将所有的authorized_keys文件进行合并（最简单的方法是将其余节点的文件内容追加到node01主机上）
[root@node02 .ssh]# cat ~/.ssh/authorized_keys | ssh root@node01 'cat >> ~/.ssh/authorized_keys'
[root@node03 .ssh]# cat ~/.ssh/authorized_keys | ssh root@node01 'cat >> ~/.ssh/authorized_keys'
[root@node04 .ssh]# cat ~/.ssh/authorized_keys | ssh root@node01 'cat >> ~/.ssh/authorized_keys'
[root@node05 .ssh]# cat ~/.ssh/authorized_keys | ssh root@node01 'cat >> ~/.ssh/authorized_keys'

## 查看node01上的authorized_keys文件内容，类似如下即可
eys
[root@node01 .ssh]# more authorized_keys 
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQD4AUA+aUYpSYEWnOhD8apy7nNGZvwYGQhn3aA8Q6cn2fLYN2NVipOeAqXmFD4ao0gPF
oqzt+ylxb3xGZ5QHKLQpDGSHyspXaJV+ow13IwDKpGuWjN9NgrldCw2eCYDBffEVnWVSRTSGw7KdJYdmE8cBxziDDQPBBIiDz8uXLeEf6
RafvgJXm+NmGVIxKs+dj/Bve7dIEVBtCxhEWcUQZXGEGl0H0JxwRWRACgYXgJg6oVnaRA8T0b1CUdvekiH7hnoWAgSSHgFnTdhzcicAZe
GESCCjJDqYmm8Njbpr/X98uyBOtojMS1d9kYqtO85QN7/VG/n1Hc5aLvZLYFDvwN9 root@node01
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC8ZiYNqOkm3HfheBehnA/D+1qk1T/7/DmNjfCTgEHG4HhrhghC7RUH2Nbrar29Ad0Lm
H48jbtgHeftIadoXvEqojc9wOKuIQkJmyu/7ab0t35/W4fbA8x+dH23Gr1ZV05OiqaT/QrCbaOxx/oC2QSyh5mukP5PIyMNlskO4ywgr2
QINST5qur4F7xfR+yzZH/j8Bo9aUKlKpSgXacfkhnWBV5L8BJEnEgAUIutIN8ZOIF08HW7YO8sYsyAy4Ram4M81SuCpe01DfbWInnX37j
5lthcw3I0cpZ4txhSZsM1BtRUpNsU24TB8ryCEIiGHvFJd6ljgfG2kKoFqgUUSqZ9 root@node04
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCSZxq0qWHdUIDOYypw2qzrt9rNwUOyABUxyVjuxpDEOD3td7X5D9h+fQjPYwhwUcVHN
tEApsvvV2Rbp6gn8eGUIBlkX2rJCfbsJIJSJTRq9E8LiRcDSNdbqqjzIpf/qbIcYRl/2MBxS60k2wQE6/cCBwWpil+YyIplttI58y/CfU
t6wygy2LEeMox4VrljgdTj27hhrIIzL7K8BLJcpPS9hgK2oMd3/YNeM8RhStR6SdlSNd06s9GD0xMDfY1IzAqd+7DzGp3WZOuQAOjgD2E
j1bMjta5B4pg84HH+qLdwU2i+Yt/ycDAZBREadVWBWS+DTQdPof5wYgDXXOET4SU1 root@node02
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDN177q22IpskSzCzLQBRGTguJWBKR+EJ/qgE0f4vNOJgHvZyWOObabHQOnktRonKEW4
ShKyK1w9xf406YK7FTeyOHrYb+j3Zi+A0YbWgjScd6XxmMLQDVmVgJhUMSgAVL+4s9rrQ1g5baVU8W5O2xr0dVSXXWF5khtV5aCfxU/O1
Mkmbt9WiCfp5I6biwXQj41Sm77l8EDSm1rijmXi0zc3uxtEqvqDJXV0A6s7vpQLJ9sw4kkwFdki1hwU9irG+E9ci70egZr3wuBYEdGzmJ
9UNiCUvNjS5S0Q4FvjptyOcfad/7Oqbch2JNCWRSLu5Vyi9bcK+ulKOtmS+IX6Ivl root@node05
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC4nV01w7sPiOBO8Be/8OfTFYZxC3xSoR4Tm1wgXbUa3nJpsjqw/blIeEUWwb18MRZ7V
1EEvJw+/HXh4ZiEALHAb/KxeH/BNifJJ4BZO/SMXOmQKupX4LzSzBoBAxDvY8c9+E2EImP2v1Bd4Hqf7Wfs/UJlW0BAgNbDJTpje+CFQn
lOQPQC2zxuhWUm2yuRe2kuxAki5m7j1N2JeQFyUtTArXeWRRfRpTCK56MGfGYET65Wb1uFYANklo1LmcFHgw41haWj3suRjm3uqwSLfn3
LCsY5lOGn3L6iuNBoVkIuEcllET4dIUiX8Yaajs1/m/Lqcx2GrhOJHQ35HKl+AUFf root@node03
## 将node01上的authorized_keys文件分发到其他主机上
[root@node01 .ssh]# scp ~/.ssh/authorized_keys root@node02:~/.ssh/
[root@node01 .ssh]# scp ~/.ssh/authorized_keys root@node03:~/.ssh/
[root@node01 .ssh]# scp ~/.ssh/authorized_keys root@node04:~/.ssh/
[root@node01 .ssh]# scp ~/.ssh/authorized_keys root@node05:~/.ssh/
## 每台机器之间进行ssh免密码登录操作，包括自己与自己
```

### 3.6 JDK安装（所有节点）
```shell
## 创建Java相应目录
[root@node01 ~]# mkdir -p /opt/apps/Java
## 通过rz命令上传文件
[root@node01 ~]# cd /opt/apps/Java
[root@node01 Java]# rz

## 建议使用以下方式安装——省事而且不会出错
[root@node01 Java]# rpm -ivh jdk-8u162-linux-x64.rpm 
准备中...                          ################################# [100%]
正在升级/安装...
   1:jdk1.8-2000:1.8.0_162-fcs        ################################# [100%]
Unpacking JAR files...
	tools.jar...
	plugin.jar...
	javaws.jar...
	deploy.jar...
	rt.jar...
	jsse.jar...
	charsets.jar...
	localedata.jar...
## 测试安装是否生效
[root@node01 Java]# java -version
java version "1.8.0_162"
Java(TM) SE Runtime Environment (build 1.8.0_162-b12)
Java HotSpot(TM) 64-Bit Server VM (build 25.162-b12, mixed mode)
## 在环境变量中配置JAVA_HOME
[root@node01 Java]# vim /etc/profile
#添加以下内容
export JAVA_HOME=/usr/java/jdk1.8.0_162
```

{
name:"zhangsan",


}


### 3.7 安装MySQL数据库（主节点安装即可）

```shell
## 注意：MySQL只需要在CM server主机上安装即可

## 首先要写在数据库mariadb
[root@node01 MySQL]# rpm -qa | grep mariadb
mariadb-libs-5.5.56-2.el7.x86_64
[root@node01 MySQL]# rpm -e --nodeps mariadb-libs-5.5.56-2.el7.x86_64
## 否则会报一下错误
error: Failed dependencies:
	mysql-community-common(x86-64) >= 5.7.9 is needed by mysql-community-libs-5.7.24-1.el7.x86_64
	mariadb-libs is obsoleted by mysql-community-libs-5.7.24-1.el7.x86_64

## 创建相应的MySQL目录（并不会安装在这个目录）
[root@node01 ~]# mkdir /opt/apps/MySQL
[root@node01 ~]# cd /opt/apps/MySQL
## 使用rz命令上传
[root@node01 MySQL]# rz

## 解包（注意：不是解压）
[root@node01 MySQL]# tar -xvf mysql-5.7.24-1.el7.x86_64.rpm-bundle.tar
## 开始安装：注意安装顺序
[root@node01 MySQL]# rpm -ivh mysql-community-common-5.7.24-1.el7.x86_64.rpm
[root@node01 MySQL]# rpm -ivh mysql-community-libs-5.7.24-1.el7.x86_64.rpm
[root@node01 MySQL]# rpm -ivh mysql-community-libs-compat-5.7.24-1.el7.x86_64.rpm
## 不安装的话会出现错误，缺少这个依赖
[root@node01 MySQL]# yum -y install net-tools
[root@node01 MySQL]# rpm -ivh mysql-community-client-5.7.24-1.el7.x86_64.rpm
[root@node01 MySQL]# rpm -ivh mysql-community-server-5.7.24-1.el7.x86_64.rpm

## 启动mysql服务
[root@node01 MySQL]# systemctl start mysqld

## 修改配置文件：详细看原来的Hadoop安装文档上的MySQL安装
[root@node01 MySQL]# vim /etc/my.cnf
# CDH官方推荐以下配置
transaction-isolation = READ-COMMITTED
key_buffer_size = 32M
max_allowed_packet = 32M
thread_stack = 256K
thread_cache_size = 64
query_cache_limit = 8M
query_cache_size = 64M
query_cache_type = 1
max_connections = 550
log_bin=/var/lib/mysql/mysql_binary_log
server_id=1
binlog_format = mixed
read_buffer_size = 2M
read_rnd_buffer_size = 16M
sort_buffer_size = 8M
join_buffer_size = 8M
# InnoDB settings
innodb_file_per_table = 1
innodb_flush_log_at_trx_commit  = 2
innodb_log_buffer_size = 64M
innodb_buffer_pool_size = 4G
innodb_thread_concurrency = 8
innodb_flush_method = O_DIRECT
innodb_log_file_size = 512M
sql_mode=STRICT_ALL_TABLES
# 取消密码安全策略
validate_password=off
character_set_server=utf8
init_connect='SET NAMES utf8'

#重新启动mysql服务
[root@master opt]# systemctl restart mysqld
## 查看初始化密码
[root@node01 MySQL]# cat /var/log/mysqld.log |grep password
2018-10-30T17:06:31.899394Z 1 [Note] A temporary password is generated for root@localhost: 54epwShQg9)B
2018-10-30T17:14:39.775316Z 0 [Note] Shutting down plugin 'validate_password'
2018-10-30T17:14:40.796592Z 0 [Note] Shutting down plugin 'sha256_password'
2018-10-30T17:14:40.796596Z 0 [Note] Shutting down plugin 'mysql_native_password'
2018-10-30T17:14:42.674505Z 0 [Note] Plugin 'validate_password' is disabled.
## 进入mysql客户端
[root@node01 MySQL]# mysql -uroot -p
## 修改密码
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY '123456';
## 修改root的远程访问权限
## root代表用户名 , %代表任何主机都可以访问 , 123456为root访问的密码
mysql> GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '123456' WITH GRANT OPTION; 
## flush privileges刷新MySQL的系统权限,使其即时生效，否则就重启服务器
mysql> FLUSH PRIVILEGES;
## 创建数据库，以备后用
mysql> CREATE DATABASE scm DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
Query OK, 1 row affected (0.01 sec)

mysql> CREATE DATABASE amon DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
Query OK, 1 row affected (0.00 sec)

mysql> CREATE DATABASE rman DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
Query OK, 1 row affected (0.00 sec)

mysql> CREATE DATABASE hue DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
Query OK, 1 row affected (0.00 sec)

mysql> CREATE DATABASE metastore DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
Query OK, 1 row affected (0.00 sec)

mysql> CREATE DATABASE sentry DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
Query OK, 1 row affected (0.00 sec)

mysql> CREATE DATABASE nav DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
Query OK, 1 row affected (0.00 sec)

mysql> CREATE DATABASE navms DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
Query OK, 1 row affected (0.00 sec)

mysql> CREATE DATABASE oozie DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
Query OK, 1 row affected (0.00 sec)
## 退出
mysql> exit;
```

### 3.7 安装Cloudera Manager Server （主节点操作）

```shell
## 上传cloudera-manager-centos7-cm5.15.1_x86_64.tar.gz到/opt/apps目录
[root@node01 apps]# rz
## 解压
[root@node01 apps]# tar -zxvf cloudera-manager-centos7-cm5.15.1_x86_64.tar.gz
## 删除这个压缩包
[root@node01 apps]# rm -rf cloudera-manager-centos7-cm5.15.1_x86_64.tar.gz
## ls查看如下
[root@node01 apps]# ls
cloudera  cm-5.15.1  Java  MySQL

## 为Cloudera Manager建立数据库
## 上传mysql驱动jar包到/usr/share/java
[root@node01 apps]# mkdir -p /usr/share/java
[root@node01 apps]# cd /usr/share/java/
[root@node01 java]# rz
[root@node01 java]# mv mysql-connector-java-5.1.47-bin.jar mysql-connector-java.jar 
[root@node01 java]# ls
mysql-connector-java.jar

## 启动脚本：进入到/opt/apps/cm-5.15.1/share/cmf/schema目录
[root@node01 schema]# ./scm_prepare_database.sh mysql scm root
Enter SCM password: 
JAVA_HOME=/usr/java/jdk1.8.0_162
Verifying that we can write to /opt/apps/cm-5.15.1/etc/cloudera-scm-server
Creating SCM configuration file in /opt/apps/cm-5.15.1/etc/cloudera-scm-server
groups: cloudera-scm: no such user
Executing:  /usr/java/jdk1.8.0_162/bin/java -cp /usr/share/java/mysql-connector-java.jar:/usr/share/java/oracle-connector-java.jar:/usr/share/java/postgresql-connector-java.jar:/opt/apps/cm-5.15.1/share/cmf/schema/../lib/* com.cloudera.enterprise.dbutil.DbCommandExecutor /opt/apps/cm-5.15.1/etc/cloudera-scm-server/db.properties com.cloudera.cmf.db.
Wed Oct 31 01:25:14 CST 2018 WARN: Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6.26+ and 5.7.6+ requirements SSL connection must be established by default if explicit option isn't set. For compliance with existing applications not using SSL the verifyServerCertificate property is set to 'false'. You need either to explicitly disable SSL by setting useSSL=false, or set useSSL=true and provide truststore for server certificate verification.
2018-10-31 01:25:15,925 [main] INFO  com.cloudera.enterprise.dbutil.DbCommandExecutor  - Successfully connected to database.
All done, your SCM database is configured correctly!



## 配置Cloudera Manager Agent(每台节点)

## 修改CM主机地址：目录/opt/apps/cm-5.15.1/etc/cloudera-scm-agent
[root@node01 cloudera-scm-agent]# vim config.ini
## 修改如下
# Hostname of the CM server.
server_host=node01
## 拷贝cm目录到其他节点
[root@node01 apps]# scp -r cm-5.15.1 root@node02:/opt/apps/
[root@node01 apps]# scp -r cm-5.15.1 root@node03:/opt/apps/
[root@node01 apps]# scp -r cm-5.15.1 root@node04:/opt/apps/
[root@node01 apps]# scp -r cm-5.15.1 root@node05:/opt/apps/

## 准备Parcels，用以安装CDH
## 上传下面几个文件到指定目录：/opt/apps/cloudera/parcel-repo
# CDH-5.15.1-1.cdh5.15.1.p0.4-el7.parcel
# CDH-5.15.1-1.cdh5.15.1.p0.4-el7.parcel.sha
# manifest.json

## 创建cloudera-scm用户（每台节点均执行）
[root@node01 ~]# useradd --system --home=/opt/apps/cm-5.15.1/run/cloudera-scm-server/ --no-create-home --shell=/bin/false --comment "Cloudera SCM User" cloudera-scm


```

## 4. 创建快照


## 4. 启动

### 4.1 启动cloudera-scm-server
```shell

[root@node01 ~]# cd /opt/apps/cm-5.15.1/etc/init.d/
[root@node01 init.d]# ./cloudera-scm-server start

```

### 4.2 启动cloudera-scm-agent
```shell
[root@node01 ~]# cd /opt/apps/cm-5.15.1/etc/init.d/
[root@node01 init.d]# ./cloudera-scm-agent start
```

### 4.3 进入web界面开始安装
http://192.168.100.101:7180/cmf/login
![image_1cr86pb1v1sls5tg1lml1mjn1ql9m.png-48.4kB][9]
![image_1cr86sad41aje1lcn11hj1llc1ca013.png-202kB][10]
![image_1cr8704k3q8skga69n1thj2a71g.png-130.4kB][11]
![image_1cr871dst164r6iq65713rfi781t.png-152.3kB][12]
![image_1cr875jm9da7pvo1kd515fd1efg2a.png-127.8kB][13]
![image_1cr877kuc1rv54qi1upro8n13be2n.png-127.5kB][14]
![image_1cr87bf9h1hiq4gt1r883jb1d9f34.png-132.6kB][15]

#### 4.3.1 接下来重启server
```shell
[root@node01 init.d]# ./cloudera-scm-server restart
Stopping cloudera-scm-server:                              [  确定  ]
Starting cloudera-scm-server:                              [  确定  ]

```
#### 4.3.2 重启之后，仍然正常登录到这一步
![image_1cr87ja8e1u98t3seek1s9311b03h.png-143.8kB][16]
![image_1cr87loa2t011ik1ts41ngp1jq33u.png-72.4kB][17]
![image_1cr87vrvi1l9vhtl1jmv1scbioh4b.png-75.2kB][18]
![image_1cr882f9jd3r1bs31498vrpvd4o.png-153kB][19]

```shell
## 出现以上警告，则在所有主机上执行下面的命令
[root@node01 init.d]# echo 10 > /proc/sys/vm/swappiness
[root@node01 init.d]# echo never > /sys/kernel/mm/transparent_hugepage/defrag
[root@node01 init.d]# echo never > /sys/kernel/mm/transparent_hugepage/enabled
[root@node01 init.d]# echo never > /sys/kernel/mm/transparent_hugepage/defrag >> /etc/rc.local
[root@node01 init.d]# echo never > /sys/kernel/mm/transparent_hugepage/enabled >> /etc/rc.local

```
![image_1cr887909t6q1rfggtjiivhv055.png-130.7kB][20]
![image_1cr888o2t10sdm601hlg1njn17ho5i.png-149.8kB][21]
![image_1cr88g66aomtgone8llrqi4e5v.png-117.1kB][22]
![image_1cr88k1fc4gdvi6qob8rn1p4k6s.png-155.8kB][23]




![image_1cr88lvu8e891vjovde1mm5ht779.png-151kB][24]




![image_1cr4i1pr2189r166rn3ucn1k0q1p.png-120.5kB][25]
![image_1cr4i2ij41mfqv4qa4iv841mn026.png-108.8kB][26]
![image_1cr4i3odi1ndsh1qiehvms1a3j.png-100.5kB][27]
![image_1cr4i48vm1ih1p6s1hlhirh1jd540.png-114.4kB][28]
后面一步一步走即可

## 4.4 设置开机自启
```shell
## 1. 复制脚本 cloudera-scm-server 到 server 主机上/etc/init.d

## 2. 复制脚本 cloudera-scm-agent 到所有的 agent 主机上

## 3. 设置agent开机自启
# 3.1 每台主机均执行添加到开机自启项
[root@node01 init.d]# chkconfig --add cloudera-scm-agent
# 3.2 设置自启
[root@node01 init.d]# chkconfig cloudera-scm-agent on
# 3.3 展示自启内容，2-5开启说明配置成功
[root@node01 init.d]# chkconfig --list cloudera-scm-agent 

注：该输出结果只显示 SysV 服务，并不包含
原生 systemd 服务。SysV 配置数据
可能被原生 systemd 配置覆盖。 

      要列出 systemd 服务，请执行 'systemctl list-unit-files'。
      查看在具体 target 启用的服务请执行
      'systemctl list-dependencies [target]'。

cloudera-scm-agent	0:关	1:关	2:开	3:开	4:开	5:开	6:关

## 4. 设置server开机自启
[root@node01 init.d]# chkconfig --add cloudera-scm-server 
[root@node01 init.d]# chkconfig cloudera-scm-server on
[root@node01 init.d]# chkconfig --list cloudera-scm-server 

注：该输出结果只显示 SysV 服务，并不包含
原生 systemd 服务。SysV 配置数据
可能被原生 systemd 配置覆盖。 

      要列出 systemd 服务，请执行 'systemctl list-unit-files'。
      查看在具体 target 启用的服务请执行
      'systemctl list-dependencies [target]'。

cloudera-scm-server	0:关	1:关	2:开	3:开	4:开	5:开	6:关

## 5. 修改配置信息，否则失败

# 5.1 修改cloudera-scm-server与agent脚本中的CMF_DEFAULTS
[root@node01 init.d]# vim /etc/init.d/cloudera-scm-agent
CMF_DEFAULTS=${CMF_DEFAULTS:-/opt/apps/cm-5.15.1/etc/default}


## 6. 错误信息集锦
## 6.1 错误1
[root@node01 init.d]# systemctl status cloudera-scm-server 
● cloudera-scm-server.service - LSB: Cloudera SCM Server
   Loaded: loaded (/etc/rc.d/init.d/cloudera-scm-server; bad; vendor preset: disabled)
   Active: failed (Result: exit-code) since 日 2018-11-04 09:24:49 CST; 6min ago
     Docs: man:systemd-sysv-generator(8)
  Process: 943 ExecStart=/etc/rc.d/init.d/cloudera-scm-server start (code=exited, status=1/FAILURE)

11月 04 09:24:49 node01 systemd[1]: Starting LSB: Cloudera SCM Server...
11月 04 09:24:49 node01 cloudera-scm-server[943]: File not found: /usr/sbin/cmf-server
11月 04 09:24:49 node01 systemd[1]: cloudera-scm-server.service: control process exite...s=1
11月 04 09:24:49 node01 systemd[1]: Failed to start LSB: Cloudera SCM Server.
11月 04 09:24:49 node01 systemd[1]: Unit cloudera-scm-server.service entered failed state.
11月 04 09:24:49 node01 systemd[1]: cloudera-scm-server.service failed.
Hint: Some lines were ellipsized, use -l to show in full.
## 6.1 解决方案
修改cloudera-scm-server与agent脚本中的CMF_DEFAULTS
```

## 错误总结
```shell
## Unable to verify database connection.
[root@node01 java]# yum install -y python-lxml

## Hue启动出错
[root@node01 init.d]# yum -y install httpd
[root@node01 init.d]# yum -y install mod_ssl

## No portmap or rpcbind service is running on this host. Please start portmap or rpcbind service before attempting to start the NFS Gateway role on this host.
[root@node01 init.d]# yum -y install rpcbind
```

  ![1](http://static.zybuluo.com/zhangwen100/ogbw8dju2fvc1o2tb2vjndkd/image_1cr7ui3hq12jp1qtmcic19c1sa39.png)
  [2](https://download.oracle.com/otn/java/jdk/8u181-b13/96a7b8442fe848ef90c96a2fad6ed6d1/jdk-8u181-linux-x64.tar.gz?AuthParam=1540826002_cc60927e5e66e3999064c255b8e675da
  [3](https://www.cloudera.com/documentation/enterprise/release-notes/topics/rn_consolidated_pcm.html#pcm_jdk
  [4](https://dev.mysql.com/downloads/file/?id=481064
  [5](http://archive.cloudera.com/cdh5/parcels/latest/manifest.json
  [6](http://archive.cloudera.com/cdh5/parcels/5.15.1/
  [7](http://archive.cloudera.com/cdh5/parcels/5.15.1/
  [8](http://archive.cloudera.com/cm5/cm/5/
  ![9]( http://static.zybuluo.com/zhangwen100/caom6uixock1i7hrvog9h6ob/image_1cr86pb1v1sls5tg1lml1mjn1ql9m.png)
  ![10]( http://static.zybuluo.com/zhangwen100/ktds3ociczb6v1hmedex4da8/image_1cr86sad41aje1lcn11hj1llc1ca013.png)
  ![11](http://static.zybuluo.com/zhangwen100/8j4dqs87h9a9bz2pkcydu3su/image_1cr8704k3q8skga69n1thj2a71g.png)
  ![12]( http://static.zybuluo.com/zhangwen100/658tslyfpe0v2wksj47qadpi/image_1cr871dst164r6iq65713rfi781t.png)
  ![13]( http://static.zybuluo.com/zhangwen100/e2sul6wwhd79v7o7fw5ft013/image_1cr875jm9da7pvo1kd515fd1efg2a.png)
  ![14](http://static.zybuluo.com/zhangwen100/rvlacm9glpp91hrdrqfu0h6z/image_1cr877kuc1rv54qi1upro8n13be2n.png)
  ![15]( http://static.zybuluo.com/zhangwen100/gym33jt5gpx2c4r8oqsjf410/image_1cr87bf9h1hiq4gt1r883jb1d9f34.png)
  ![16](http://static.zybuluo.com/zhangwen100/0jyrcaioj1iz6kmbsg72zxui/image_1cr87ja8e1u98t3seek1s9311b03h.png)
  ![17]( http://static.zybuluo.com/zhangwen100/fblifuir1024kxbffioodtwd/image_1cr87loa2t011ik1ts41ngp1jq33u.png)
  ![18]( http://static.zybuluo.com/zhangwen100/z3qpqekpsn67rb891qftu7o1/image_1cr87vrvi1l9vhtl1jmv1scbioh4b.png)
  ![19]( http://static.zybuluo.com/zhangwen100/tjmaer7yd84iabkfq6yfpu0d/image_1cr882f9jd3r1bs31498vrpvd4o.png)
  ![20]( http://static.zybuluo.com/zhangwen100/8r1yq6nfu8zn0wndp7j2xgdn/image_1cr887909t6q1rfggtjiivhv055.png)
  ![21]( http://static.zybuluo.com/zhangwen100/uezb8ezaqkwrkcyxd8vhryae/image_1cr888o2t10sdm601hlg1njn17ho5i.png)
  ![22]( http://static.zybuluo.com/zhangwen100/4mx4ssqf0mk9nonlettnlqac/image_1cr88g66aomtgone8llrqi4e5v.png)
  ![23](http://static.zybuluo.com/zhangwen100/cnfcq9v0ymv9tqxfac5qtmw7/image_1cr88k1fc4gdvi6qob8rn1p4k6s.png)
  ![24]( http://static.zybuluo.com/zhangwen100/xb388330wilubzeomsc8o9bt/image_1cr88lvu8e891vjovde1mm5ht779.png)
  ![25]( http://static.zybuluo.com/zhangwen100/ttsmoygr7t2tpce73k9p0plq/image_1cr4i1pr2189r166rn3ucn1k0q1p.png)
  ![26]( http://static.zybuluo.com/zhangwen100/92isrcqeic9dvwp2ms0tfkbi/image_1cr4i2ij41mfqv4qa4iv841mn026.png)
  ![27]( http://static.zybuluo.com/zhangwen100/72nhqt1u4ag6y71tcji49juk/image_1cr4i3odi1ndsh1qiehvms1a3j.png)
  ![28]( http://static.zybuluo.com/zhangwen100/82qfkwnhyrp8ti0e9i8p5rtq/image_1cr4i48vm1ih1p6s1hlhirh1jd540.png)