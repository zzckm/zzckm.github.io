---
title: 'Linux第二天'
date: 2019-04-25 23:00:56
tags: [Linux]
categories: Linux学习
---
# Linux第二天

## 常用命令

gzip
: GNU zip

 1. 作用: 压缩（解压）文件，压缩文件的后缀名为.gz
 2. 语法: gzip [-d] 文件
 3. 特点: 只能压缩文件，不能压缩目录，不保留原文件
 4. 案例:
 ```shell
 [root@localhost test]# ls
services
[root@localhost test]# gzip services 
[root@localhost test]# ls
services.gz
[root@localhost test]# mkdir aaa
[root@localhost test]# ls
aaa  services.gz
[root@localhost test]# gzip aaa
gzip: aaa is a directory -- ignored
[root@localhost test]# gzip -d services.gz 
[root@localhost test]# ls
aaa  services
[root@localhost test]# gzip services 
[root@localhost test]# gunzip services.gz 
[root@localhost test]# ls
aaa  services

 ```

bzip2
: 

 1. 作用: 压缩（解压）文件，压缩后的文件后缀为.bz2
 2. 语法: bzp2 [-kd] [文件]
        - -k: 产生压缩文件后保留原文件
        - -d: 解压缩
 3. 案例:
 ```shell
 [root@localhost test]# ls
aaa  services
[root@localhost test]# bzip2 services 
[root@localhost test]# ls
aaa  services.bz2
[root@localhost test]# bzip2 -d services.bz2 
[root@localhost test]# ls
aaa  services
[root@localhost test]# bzip2 -k services 
[root@localhost test]# ls
aaa  services  services.bz2
[root@localhost test]# bzip2 aaa
bzip2: Input file aaa is a directory.
[root@localhost test]# bunzip2 services.bz2 
 ```
 
zip
: 

 1. 作用: 压缩（解压）文件，压缩文件的后缀为.zip
 2. 语法: zip [-r] [压缩后的文件名] [文件或目录]
        - -r: 压缩目录的时候需要使用
3. 案例：
    ```shell
    [root@localhost test]# zip services 
    
    zip error: Nothing to do! (services.zip)
    [root@localhost test]# ls
    aaa  services
    [root@localhost test]# zip services.zip services
      adding: services (deflated 80%)
    [root@localhost test]# ls
    aaa  services  services.zip
    [root@localhost test]# zip -r services.zip 
    
    zip error: Nothing to do! (services.zip)
    [root@localhost test]# zip -r services.zip se
    	zip warning: name not matched: se
    
    zip error: Nothing to do! (try: zip -r services.zip . -i se)
    [root@localhost test]# unzip services.zip 
    Archive:  services.zip
    replace services? [y]es, [n]o, [A]ll, [N]one, [r]ename: r
    new name: hahaha
      inflating: hahaha                  
    [root@localhost test]# ls
    aaa  hahaha  services  services.zip
    [root@localhost test]# unzip services.zip se
    Archive:  services.zip
    caution: filename not matched:  se
    [root@localhost test]# cd ..
    [root@localhost opt]# ls
    rh  test  vmware-tools-distrib
    [root@localhost opt]# zip test.zip test
      adding: test/ (stored 0%)
    [root@localhost opt]# ll
    总用量 4
    drwxr-xr-x. 2 root root   6 3月  26 2015 rh
    drwxr-xr-x. 3 root root  67 7月  24 10:44 test
    -rw-r--r--. 1 root root 160 7月  24 10:46 test.zip
    drwxr-xr-x. 9 root root 145 3月  22 17:10 vmware-tools-distrib
    [root@localhost opt]# zip -r test.zip test
    updating: test/ (stored 0%)
      adding: test/aaa/ (stored 0%)
      adding: test/services (deflated 80%)
      adding: test/services.zip (stored 0%)
      adding: test/hahaha (deflated 80%)
    [root@localhost opt]# ll
    总用量 400
    drwxr-xr-x. 2 root root      6 3月  26 2015 rh
    drwxr-xr-x. 3 root root     67 7月  24 10:44 test
    -rw-r--r--. 1 root root 409121 7月  24 10:46 test.zip
    drwxr-xr-x. 9 root root    145 3月  22 17:10 vmware-tools-distrib
    
    ```

tar
: 

 1. 作用: 文件、目录打包，解包
 2. 语法: tar [-zxvfcj] [压缩后的文件名] [文件或目录]
        - -z：使用gzip命令进行压缩或解压
        - -c：将文件或目录进行打包，后缀是.tar
        - -x：解包（extract）
        - -j：使用bzip2命令压缩或解压
        - -v：显示解压或压缩的过程
        - -f：指定文件名，必须一个选项
 3. 案例:
 ```shell
    [root@localhost test]# cd aaa
[root@localhost aaa]# ls
hahaha  services  services.zip
[root@localhost aaa]# cd ..
[root@localhost test]# tar -cf aa.tar aaa
[root@localhost test]# ls
aaa  aa.tar  hahaha  services  services.zip
[root@localhost test]# tar -xf aa.tar 
[root@localhost test]# ls
aaa  aa.tar  hahaha  services  services.zip
[root@localhost test]# rm -rf aaa
[root@localhost test]# ls
aa.tar  hahaha  services  services.zip
[root@localhost test]# tar -xf aa.tar 
[root@localhost test]# ls
aaa  aa.tar  hahaha  services  services.zip
[root@localhost test]# gzip aa.tar
[root@localhost test]# ls
aaa  aa.tar.gz  hahaha  services  services.zip
[root@localhost test]# tar -zcvf aaa.tar.gz aaa
aaa/
aaa/hahaha
aaa/services
aaa/services.zip
[root@localhost test]# ls
aaa  aaa.tar.gz  aa.tar.gz  hahaha  services  services.zip
[root@localhost test]# rm -rf aaa
[root@localhost test]# ls
aaa.tar.gz  aa.tar.gz  hahaha  services  services.zip
[root@localhost test]# tar -zxvf aaa.tar.gz 
aaa/
aaa/hahaha
aaa/services
aaa/services.zip
[root@localhost test]# ls
aaa  aaa.tar.gz  aa.tar.gz  hahaha  services  services.zip
[root@localhost test]# tar -zxvf aaa.tar.gz -C /usr/
aaa/
aaa/hahaha
aaa/services
aaa/services.zip
[root@localhost test]# cd /usr/
[root@localhost usr]# ls
aaa  bin  etc  games  include  lib  lib64  libexec  local  sbin  share  src  tmp
 ```

shutdown
: 

 1. 作用: 系统关机的命令
 2. 语法: shutdown [-chr] 时间
        - -c: 表示取消操作
        - -h：关机
        - -r：重启
 3. 案例:
 ```shell
 ## 现在立刻马上关机
 [root@localhost ~]# shutdown -h now
 ## 在指定的时间关机
 [root@localhost ~]# shutdown -h 10:40
 [root@localhost ~]# halt
 [root@localhost ~]# poweroff
 [root@localhost ~]# init 0
 ##重启
 [root@localhost ~]# reboot
 [root@localhost ~]# init 6
 ```
 
 常用快捷键操作
 : 
 
  1. ctrl + c 结束当前进程
  2. Ctrl + z 挂起当前进程，放后台
  3. Ctrl + r 查看命令历史（history）
  4. 方向键 上，下：查看执行过的命令
  5. Ctrl + l 清屏（clear）
  

## **vim编辑器**

vim工作模式
: ![image_1cj5605pd1pk8122bkd617mi1p09.png-41.6kB][1]
 命令模式：一般模式
 编辑模式：底行模式，命令行模式
 插入模式：文件编辑模式
 
插入命令
:   
     |命令名|作用|
     |---|---|
     |i|在光标之前插入文本|
     |a|在光标之后插入文本|
     |I（shift+i）|在文本的开始插入文本，行首|
     |A（shift+a）|在文本的结尾插入文本，行末|
     |o|在光标的下方插入新行|
     |O（shift+o）|在光标所处行的上方插入新行|
     
保存和退出
:  
    |命令|作用|
    |---|---|
    |:w|保存修改，但是不退出|
    |:w newFileName|另存为指定文件|
    |:w >> 文件名|将本文件中的内容追加到其他文件中去，，其他文件必须存在|
    |:wq|保存并退出|
    |:q!|不保存并退出|
    |:q|直接退出，但是如果修改了会有提示|
    |:wq!|保存修改并退出，可以忽略文件只读属性|
    
定位命令
:  
    |命令名|作用|
    |---|---|
    |:set nu|设置并显示行号|
    |:set nonu|取消显示行号|
    |gg|直接回到第一行|
    |G（shift+g）|到最后一行|
    |nG|到第n行|
    |:n|定位到第n行|

删除命令
:  
    |命令名|作用|
    |---|---|
    |x|删除光标所在位置的字符|
    |nx|删除从光标位置开始计算的后面n个字符|
    |dd|删除光标所在行|
    |ndd|删除光标所在行以及光标后面的n-1行|
    |:n1,n2d|删除指定范围的行，:5,9d 表示删除5,6,7,8,9这几行|
    |dG|删除光标所在行到最后一行|
    |D|删除从光标位置到行尾|
 
 复制和剪切的命令
 :  
    |命令名|作用|
    |---|---|
    |yy,y,Y|复制当前行|
    |p|粘贴，粘贴到光标所在行的下方|
    |P|粘贴，粘贴到光标所在行的上方|
    |nyy|复制当前行以及以下n-1行|
    |dd|剪切当前行|
    |ndd|剪切当前行及以下行共n行|

 替换和取消的命令
 :  
    |命令名|作用|
    |---|---|
    |r|替换光标位置的字符|
    |R|从光标位置开始替换，直到esc结束|
    |u|取消上一步操作|
    |Ctrl+r|返回到新的状态，直到最新|
    
替换和搜索的命令
:   %表示全文，g表示的全局替换，s表示的开始，c表示替换要询问
    |命令名|作用|
    |---|---|
    |/字符串|向后搜索指定的字符串|
    |?字符串|向前搜索指定的字符串|
    |n|搜索字符串的下一个出现的位置，与搜索顺序相同|
    |N|搜索字符串的上一个出现的位置，与搜索顺序相反|
    |:%s/老字符串/新字符串/g||
    |:n1,n2s/老字符串/新字符串/g|在指定范围内替换指定字符串|
    案例
    ```shell
    :11155,11175s/^/#aaaaaa/g
    :r !pwd
    
    ```

可视化字符模式
: 
    |命令名|作用|
    |---|---|
    |v|字符视图模式|
    |V|行视图视图模式|
    

## Linux高级设置    

IP地址的修改
: 

 1. 可视化修改
 2. setup启动虚拟界面进行配置（7以下，不包含7）
 3. **修改配置文件**
    ```shell
    #编辑文件
    [root@localhost test]# vim
    /etc/sysconfig/network-scripts/ifcfg-ens33
    #重启网卡服务，但是是centos6的模式，7中已经取消
    [root@localhost test]# service network restart
    #重启网卡服务，这个在7中可以用，6中不识别
    [root@localhost test]# systemctl restart network
    ```


ping
: 

 1. 作用：测试网络的连通性
 2. 语法: ping [-c] IP地址
 
ifconfig
: 

 1. 作用: 显示网卡的配置信息
 2. 语法: ifconfig [-a] [网卡设备的名称]
 3. 案例:
 ```shell
 [root@localhost ~]# ifconfig
 [root@localhost ~]# ifconfig -a
 [root@localhost ~]# ifconfig -a ens33
 
 ```
 
netstat
: 

 1. 作用: 用于检测主机的网络配置状况
 2. 语法: netstat [-atunl]
        - -a: 显示所有连接的端口信息
        - -t：仅显示tcp通讯相关信息
        - -u：仅显示ucp通讯相关信息
        - -n: 使用数字方式显示地址和端口
        - -l: 显示监听中的服务的socket
 3. 案例：
 ```shell
 [root@localhost ~]# netstat
 [root@localhost ~]# netstat -tuln
 [root@localhost ~]# netstat -ua
 [root@localhost ~]# netstat -ta
 
 ```

## 进程管理命令

看PPT

## 用户管理
useradd
: 

 1. 语法: useradd [选项] 用户名

passwd
: 

 1. 语法：passwd [用户名]


userdel
: 

 1. 语法: userdel [-r] 用户名
        - -r：删除账号的时候，删除宿主目录

## 磁盘空间命令
df

* -h:易读方式显示
* -M:以MB方式显示
* -k:

du 查看文件或目录的大小
du [-ahsb] [文件名，目录]

free


## 权限相关
![image_1cj5qjudmvu81qfuq5p1let1dhjm.png-60.2kB][2]
 
 d: 这个位置表示文件的类型：d，l，-
 接下来的3位
 rwx：这三位表示当前用户对该文件（目录的权限）
    r： 表示读-4，w表示写-2，x表示执行-1
 接下来的3位
 r-x：这三位表示当前用户所属组对该文件的权限
 最后3位
 r-x：这个表示其他用户的对该文件的权限


修改文件的权限
chmod 777 文件名或目录名
chmod 666 文件名或目录名

  ![1]( http://static.zybuluo.com/zhangwen100/gef0oiw0qan1zcq1xbrqbwho/image_1cj5605pd1pk8122bkd617mi1p09.png)
  ![2]( http://static.zybuluo.com/zhangwen100/v23a7syr4gzo5ti8qm1b085q/image_1cj5qjudmvu81qfuq5p1let1dhjm.png)