---
title: Linux第一天
date: 2019-04-25 23:00:41
tags: [Linux]
---
# **Linux第一天**

## **为什么要学习Linux**

因为后期的Hadoop，Spark等都要运行在Linux上

## **Linux简介**

Unix    1969年
Linux   1991年

Linux是一个自由和开放源代码的操作系统，有很多不同的发行版本，使用的都是Linux内核

开发版和发行版：
: 就是基于Linux的内核，增加一些应用程序，然后增加一些桌面，就是发行版

## **Linux的发行版**

主要有两大阵营
:    Redhat：Redhat，CentOS，suse等
    Redhat与CentOS的区别在于一个提供后期的服务支持，一个不提供
    Debian：Ubuntu，Debian等等
    
## **Linux的应用领域**
1. 企业服务器
2. 嵌入式系统
3. 大型电影的特效处理

## **Linux特点**

1. 开源的
2. 多用户，多任务
3. 速度性能较高
4. 一般情况下，不使用图形化界面

## **CentOS社区版**
主流：Redhat和centos
区别：Redhat和centos相差不大，CentOS基于Redhat的一个开发源代码的企业级Linux发行版，CentOS是不提供后期维护服务的，要想提供就使用Redhat

下载
: https://www.centos.org/

## **Linux与windows**
1. windows:图形化界面/鼠标,linux命令/键盘
2. windows不区分大小写，Linux严格区分大小写
3. 在Linux中一切皆文件，没有所谓的扩展名，扩展名只是为了让管理员能够看到这个文件是什么文件而已


## 常见标识
> ~ 表示当前用户的主目录
/ 表示根目录


## **Linux的目录结构**
> 
**/bin**: (binaries) 存放系统命令的目录，所有用户都可以执行。
**/sbin** : (super user binaries) 保存和系统环境设置相关的命令，只有超级用户可以使用这些命令，有些命令可以允许普通用户查看。
**/usr/bin**：存放系统命令的目录，所有用户可以执行。这些命令和系统启动无关，单用户模式下不能执行
**/usr/sbin**：存放根文件系统不必要的系统管理命令，超级用户可执行
**/root**: 存放root用户的相关文件,root用户的家目录。宿主目录  超级用户
**/home**：用户缺省宿主目录 eg:/home/spark
**/tmp**：(temporary)存放临时文件
**/etc**：(etcetera)系统配置文件
**/usr**：（unix software resource）系统软件共享资源目录，存放所有命令、库、手册页等
**/proc**：虚拟文件系统，数据保存在内存中，存放当前进程信息
**/boot**：系统启动目录

> /dev：(devices)存放设备文件
/sys :虚拟文件系统，数据保存在内存中，主要保存于内存相关信息
/lib：存放系统程序运行所需的共享库
/lost+found：存放一些系统出错的检查结果。
**/var**：(variable) 动态数据保存位置，包含经常发生变动的文件，如邮件、日志文件、计划任务等
/mnt：(mount)挂载目录。临时文件系统的安装点，默认挂载光驱和软驱的目录
/media:挂载目录。 挂载媒体设备，如软盘和光盘
/misc:挂载目录。 挂载NFS服务
**/opt**: 第三方安装的软件保存位置。 习惯放在/usr/local/目录下
/srv : 服务数据目录

## **Linux常用命令**

命令的格式
: 命令名 [-选项] [参数]
说明
: 中括号表示可有可无，大部分命令均会遵从这个格式

cd
: change directory
    切换目录
    语法：cd [目录]
    ```shell
    [zhang@bogon ~]$ cd /
    [zhang@bogon /]$ cd ..
    [zhang@bogon /]$ cd /etc/
    [zhang@bogon etc]$ cd ..
    [zhang@bogon /]$ cd /etc
    [zhang@bogon etc]$ cd .
    #显示并打开到上一次操作的目录
    [zhang@bogon etc]$ cd -
    /etc
    #进入到当前用户的主目录/home/zhang
    [zhang@bogon etc]$ cd ~

    ```

ls
: <span class="red">list</span>

 - 作用: 显示目录和文件
 - 语法：ls [-alrRd] [目录]
        - a: all,显示所有的文件
        - l: long,显示详细信息
        - r: reverse,逆序排序
        - R: 递归显示当前目录下的所有的目录
        - h: 友好显示，大小可以按照K显示
        - t: 按照修改时间排序（降序）
        - ll: 等价于ls -l
    
pwd
: <span class="red">print working directory</span>

 - 作用: 在控制台显示当前工作的目录
 - 语法: pwd [-LP]
        - L 显示的链接的路径，当前路径，默认
        - P 物理路径
    ```shell
    [zhang@bogon ~]$ ls
    公共  模板  视频  图片  文档  下载  音乐  桌面
    [zhang@bogon ~]$ ls -a
    .   .bash_history  .bash_profile  .cache   .esd_auth      .local    公共  视频  文档  音乐
    ..  .bash_logout   .bashrc        .config  .ICEauthority  .mozilla  模板  图片  下载  桌面
    [zhang@bogon ~]$ ls -l
    总用量 0
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 公共
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 模板
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 视频
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 图片
    drwxr-xr-x. 3 zhang zhang 34 7月  23 14:26 文档
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 下载
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 音乐
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 桌面
    [zhang@bogon ~]$ ls -l -a
    总用量 36
    drwx------. 14 zhang zhang 4096 7月  23 2018 .
    drwxr-xr-x.  3 root  root    19 7月  23 2018 ..
    -rw-------.  1 zhang zhang   89 7月  23 14:46 .bash_history
    -rw-r--r--.  1 zhang zhang   18 8月   3 2017 .bash_logout
    -rw-r--r--.  1 zhang zhang  193 8月   3 2017 .bash_profile
    -rw-r--r--.  1 zhang zhang  231 8月   3 2017 .bashrc
    drwx------. 15 zhang zhang 4096 7月  23 13:09 .cache
    drwxr-xr-x. 16 zhang zhang 4096 7月  23 14:26 .config
    -rw-------.  1 zhang zhang   16 7月  23 2018 .esd_auth
    -rw-------.  1 zhang zhang  314 7月  23 2018 .ICEauthority
    drwx------.  3 zhang zhang   19 7月  23 2018 .local
    drwxr-xr-x.  5 zhang zhang   54 7月  23 13:09 .mozilla
    drwxr-xr-x.  2 zhang zhang    6 7月  23 2018 公共
    drwxr-xr-x.  2 zhang zhang    6 7月  23 2018 模板
    drwxr-xr-x.  2 zhang zhang    6 7月  23 2018 视频
    drwxr-xr-x.  2 zhang zhang    6 7月  23 2018 图片
    drwxr-xr-x.  3 zhang zhang   34 7月  23 14:26 文档
    drwxr-xr-x.  2 zhang zhang    6 7月  23 2018 下载
    drwxr-xr-x.  2 zhang zhang    6 7月  23 2018 音乐
    drwxr-xr-x.  2 zhang zhang    6 7月  23 2018 桌面
    [zhang@bogon ~]$ ls -la
    总用量 36
    drwx------. 14 zhang zhang 4096 7月  23 2018 .
    drwxr-xr-x.  3 root  root    19 7月  23 2018 ..
    drwxr-xr-x.  2 zhang zhang    6 7月  23 2018 公共
    drwxr-xr-x.  2 zhang zhang    6 7月  23 2018 模板
    drwxr-xr-x.  2 zhang zhang    6 7月  23 2018 视频
    drwxr-xr-x.  2 zhang zhang    6 7月  23 2018 图片
    drwxr-xr-x.  3 zhang zhang   34 7月  23 14:26 文档
    drwxr-xr-x.  2 zhang zhang    6 7月  23 2018 下载
    drwxr-xr-x.  2 zhang zhang    6 7月  23 2018 音乐
    drwxr-xr-x.  2 zhang zhang    6 7月  23 2018 桌面
    [zhang@bogon ~]$ ls -lr
    总用量 0
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 桌面
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 音乐
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 下载
    drwxr-xr-x. 3 zhang zhang 34 7月  23 14:26 文档
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 图片
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 视频
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 模板
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 公共
    [zhang@bogon ~]$ ls -lR
    .:
    总用量 0
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 公共
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 模板
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 视频
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 图片
    drwxr-xr-x. 3 zhang zhang 34 7月  23 14:26 文档
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 下载
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 音乐
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 桌面
    
    ./公共:
    总用量 0
    [zhang@bogon ~]$ ls -l
    总用量 0
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 公共
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 模板
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 视频
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 图片
    drwxr-xr-x. 3 zhang zhang 34 7月  23 14:26 文档
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 下载
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 音乐
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 桌面
    [zhang@bogon ~]$ ls -lh
    总用量 0
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 公共
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 模板
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 视频
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 图片
    drwxr-xr-x. 3 zhang zhang 34 7月  23 14:26 文档
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 下载
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 音乐
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 桌面
    [zhang@bogon ~]$ cd /usr
    [zhang@bogon usr]$ ls
    bin  etc  games  include  lib  lib64  libexec  local  sbin  share  src  tmp
    [zhang@bogon usr]$ ls -lh
    总用量 252K
    dr-xr-xr-x.   2 root root  44K 7月  23 14:42 bin
    drwxr-xr-x.   2 root root    6 11月  5 2016 etc
    drwxr-xr-x.   2 root root    6 11月  5 2016 games
    drwxr-xr-x.  35 root root 4.0K 7月  23 14:42 include
    dr-xr-xr-x.  44 root root 4.0K 7月  23 14:42 lib
    dr-xr-xr-x. 138 root root  72K 7月  23 14:42 lib64
    drwxr-xr-x.  43 root root  12K 7月  23 14:42 libexec
    drwxr-xr-x.  12 root root  131 7月  23 2018 local
    dr-xr-xr-x.   2 root root  20K 7月  23 14:42 sbin
    drwxr-xr-x. 226 root root 8.0K 7月  23 2018 share
    drwxr-xr-x.   4 root root   34 7月  23 2018 src
    lrwxrwxrwx.   1 root root   10 7月  23 2018 tmp -> ../var/tmp
    [zhang@bogon usr]$ ls -l
    总用量 252
    dr-xr-xr-x.   2 root root 45056 7月  23 14:42 bin
    drwxr-xr-x.   2 root root     6 11月  5 2016 etc
    drwxr-xr-x.   2 root root     6 11月  5 2016 games
    drwxr-xr-x.  35 root root  4096 7月  23 14:42 include
    dr-xr-xr-x.  44 root root  4096 7月  23 14:42 lib
    dr-xr-xr-x. 138 root root 73728 7月  23 14:42 lib64
    drwxr-xr-x.  43 root root 12288 7月  23 14:42 libexec
    drwxr-xr-x.  12 root root   131 7月  23 2018 local
    dr-xr-xr-x.   2 root root 20480 7月  23 14:42 sbin
    drwxr-xr-x. 226 root root  8192 7月  23 2018 share
    drwxr-xr-x.   4 root root    34 7月  23 2018 src
    lrwxrwxrwx.   1 root root    10 7月  23 2018 tmp -> ../var/tmp
    [zhang@bogon usr]$ cd ~
    [zhang@bogon ~]$ ls -t
    公共  模板  视频  图片  下载  音乐  桌面  文档
    [zhang@bogon ~]$ ls
    公共  模板  视频  图片  文档  下载  音乐  桌面
    [zhang@bogon ~]$ ls -lt
    总用量 0
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 公共
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 模板
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 视频
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 图片
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 下载
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 音乐
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 桌面
    drwxr-xr-x. 3 zhang zhang 34 7月  23 14:26 文档
    [zhang@bogon ~]$ ll
    总用量 0
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 公共
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 模板
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 视频
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 图片
    drwxr-xr-x. 3 zhang zhang 34 7月  23 14:26 文档
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 下载
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 音乐
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 桌面
    [zhang@bogon ~]$ ls -l
    总用量 0
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 公共
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 模板
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 视频
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 图片
    drwxr-xr-x. 3 zhang zhang 34 7月  23 14:26 文档
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 下载
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 音乐
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 桌面
    [zhang@bogon ~]$ ll
    总用量 0
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 公共
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 模板
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 视频
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 图片
    drwxr-xr-x. 3 zhang zhang 34 7月  23 14:26 文档
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 下载
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 音乐
    drwxr-xr-x. 2 zhang zhang  6 7月  23 2018 桌面
    [zhang@bogon ~]$ la
    bash: la: 未找到命令...
    [zhang@bogon ~]$ pwd
    /home/zhang
    [zhang@bogon ~]$ cd /etc/init.d
    [zhang@bogon init.d]$ pwd
    /etc/init.d
    [zhang@bogon init.d]$ pwd -P
    /etc/rc.d/init.d
    [zhang@bogon init.d]$ 
    
    ```

mkdir
: make directory 

 - 作用：创建新目录
 - 语法：mkdir [-p] 目录名
        - p 父目录不存在则先创建父目录
    ```shell
    [zhang@bogon ~]$ ls
    公共  模板  视频  图片  文档  下载  音乐  桌面
    [zhang@bogon ~]$ mkdir test/zhangsan/hahahaha
    mkdir: 无法创建目录"test/zhangsan/hahahaha": 没有那个文件或目录
    [zhang@bogon ~]$ mkdir -p test/zhangsan/hahahaha
    [zhang@bogon ~]$ ls
    test  公共  模板  视频  图片  文档  下载  音乐  桌面
    [zhang@bogon ~]$ mkdir -p test1 test2 test3
    [zhang@bogon ~]$ ls
    test  test1  test2  test3  公共  模板  视频  图片  文档  下载  音乐  桌面
    [zhang@bogon ~]$ mkdir -p test4/{ha,ha2,ha3}
    [zhang@bogon ~]$ ls
    test  test1  test2  test3  test4  公共  模板  视频  图片  文档  下载  音乐  桌面
    [zhang@bogon ~]$ cd test4
    [zhang@bogon test4]$ ls
    ha  ha2  ha3
    [zhang@bogon test4]$ mkdir -p /ha/haha
    
    ```

touch
: 

 - 作用: 创建空文件或者更新文件的时间
 - 语法: touch 文件名
 

cp
: copy

 - 作用: 复制文件或目录
 - 语法：cp [-rp] 源文件或目录 目的地目录
        - r: 递归
        - p: 保留文件的属性
    ```shell
    [root@bogon etc]# cp -r /etc/* ./etc/

    ```

mv 
: move

 - 作用：移动文件或目录，更改文件或目录的名字
 - 语法: mv 源文件或目录 目的地目录
 ```shell
 [root@bogon mv]# mv /home/zhang/etc .
 [root@bogon mv]# mv etc xiaozhou
 
 ```
 
rm
: remove

 - 作用: 删除文件
 - 语法：rm [-rf] 文件或目录
        - r: 递归
        - f: 强制删除文件或目录，即使原文件的属性是只读模式，也无需确认
    ```shell
    # 只能用于删除文件，但是有提示
    [root@bogon zhang]# rm pinforc 
    # 递归但是有提示
    [root@bogon zhang]# rm -r kernel
    ##常用下面的方式
    [root@bogon zhang]# rm -rf kernel
    ```
 
 cat
 : 
 
  - 作用: 显示文件的内容
  - 语法：cat [-n] [文件名]
           - A 显示所有的文件内容，包含隐藏字符
           - n 显示行号
    ```shell
    [root@bogon mv]# cat hahha/services
    [root@bogon mv]# cat -A hahha/services
    [root@bogon mv]# cat -n hahha/services
    
    ```
    
    
more
: 

 - 作用: 分页显示文件内容
 - 语法：more [文件名]
    空格或者f：显示下一页
    回车：显示下一行
    q或Q 退出

head
: 
    
     - 作用: 显示文件的前几行（默认10行）
     - 语法：head [-n] [文件名]
     
     ```shell
     [root@bogon mv]# head hahha/services
     [root@bogon mv]# head -20 hahha/services
    
     ```

tail
: 

 - 作用: 显示文件的后几行（动态显示）
 - 语法: tail [-nf] [文件名]
        - n: 指定显示的行数
        - f: 动态显示文件的内容（follow）

        ```shell
        [root@bogon mv]# tail hahha/services
        [root@bogon mv]# tail -20 hahha/services 
        [root@bogon mv]# tail -f hahha/services 
        ```

ln
: link

 - 作用: 产生链接文件
 - 语法：
        ln -s [源文件] [目标文件] 创建的是软连接
        ln [源文件] [目标文件] 创建的是硬链接
    ```shell
    #创建软链接：类似于快捷方式，但是不占用很大的空间，修改或编辑的仍然是源文件
    [root@bogon mv]# ln -s hahha/services ./service.soft
    #创建硬链接：类似于拷贝，但是不一样。会占用源文件的空间大小，修改或编辑仍然是源文件
    [root@bogon mv]# ln hahha/services  ./service.hard
    ```

man
: manual

 - 作用: 获取命令或配置文件的帮助信息
 - 语法: man [命令/配置文件]
 说明: 调用more命令来查看帮助文档

help
: 

 - 作用: 查看shell内置命令的帮助信息
 - 语法: help 命令
 ```shell
 [root@bogon mv]# help cd
 [root@bogon mv]# type cd
 [root@bogon mv]# type ls
 [root@bogon mv]# ls --help
 ```

find
: 
 
 - 作用: 查找文件或目录
 - 语法: find [搜索路径] [匹配条件]
        - -name 按照名称查找
        - -iname 按照名称查找
             - *: 匹配所有
             - ?: 匹配单个字符
        - -size 按照文件大小查找
            以block为单位，一个block是512B，1K=2block，+表示大于，-表示小于，不写表示等于
        - -type 
            f 二进制文件，l表示软连接文件，d表示目录，c表示字符文件
    - 规则: 查询范围要缩小，查询条件要精准
            
    ```shell
    [root@bogon mv]# find /etc -name "init"
/etc/sysconfig/init
/etc/selinux/targeted/active/modules/100/init
/etc/selinux/targeted/tmp/modules/100/init
[root@bogon mv]# find /etc -iname "init"
/etc/sysconfig/init
/etc/selinux/targeted/active/modules/100/init
/etc/selinux/targeted/tmp/modules/100/init
/etc/gdm/Init
[root@bogon mv]# find /etc -name "init*"
/etc/sysconfig/network-scripts/init.ipv6-global
/etc/sysconfig/init
/etc/init.d
/etc/rc.d/init.d
/etc/selinux/targeted/active/modules/100/init
/etc/selinux/targeted/contexts/initrc_context
/etc/selinux/targeted/tmp/modules/100/init
/etc/iscsi/initiatorname.iscsi
/etc/inittab
[root@bogon mv]# find /etc -name "init???"
/etc/inittab
[root@bogon mv]# find /etc -name init???
/etc/inittab
[root@bogon mv]# find /etc -name init*
/etc/sysconfig/network-scripts/init.ipv6-global
/etc/sysconfig/init
/etc/init.d
/etc/rc.d/init.d
/etc/selinux/targeted/active/modules/100/init
/etc/selinux/targeted/contexts/initrc_context
/etc/selinux/targeted/tmp/modules/100/init
/etc/iscsi/initiatorname.iscsi
/etc/inittab
[root@bogon mv]# find /etc -name *init
/etc/security/namespace.init
/etc/gdbinit
/etc/X11/xinit
/etc/sysconfig/init
/etc/selinux/targeted/active/modules/100/init
/etc/selinux/targeted/tmp/modules/100/init
[root@bogon mv]# find /etc -size 2048
[root@bogon mv]# find /etc -size -2048
[root@bogon mv]# find /etc -size +2048
    ```


grep
: 

 - 作用: 在文件中搜寻匹配的字符所在的行并输出
 - 语法: grep [-cinv] '要搜索的字符串' 文件名
        - -c: 表示要输出的匹配的行的次数（输出多少行）
        - -i: 忽略大小写
        - -n: 显示匹配的行及行号
        - -v: 反向选择，显示不包含匹配的文本的行
        
    ```shell
    [root@bogon mv]# grep udp /etc/services 
    [root@bogon mv]# grep -v ^# /etc/inittab
    [root@bogon mv]# grep -c udp /etc/services 
5389
[root@bogon mv]# grep -n udp /etc/services 
    ```

which
: 

 - 作用: 显示系统命令所在的目录

 ```shell
 [root@bogon mv]# which ls
alias ls='ls --color=auto'
	/usr/bin/ls
[root@bogon mv]# which cd
/usr/bin/cd
[root@bogon mv]# which cc
/usr/bin/cc
[root@bogon mv]# which cf
/usr/bin/which: no cf in (/usr/local/bin:/usr/local/sbin:/usr/bin:/usr/sbin:/bin:/sbin:/home/zhang/.local/bin:/home/zhang/bin)

 ```
 
whereis
: 

 - 作用: 搜索命令所在的目录，配置文件所在的目录，以及帮助文档的路径

    ```shell
    [root@bogon mv]# whereis passwd
    passwd: /usr/bin/passwd /etc/passwd /usr/share/man/man1/passwd.1.gz /usr/share/man/man5/passwd.5.gz
    [root@bogon mv]# which passwd
    /usr/bin/passwd
    [root@bogon mv]# man 5 passwd
    
    ```
 











    


