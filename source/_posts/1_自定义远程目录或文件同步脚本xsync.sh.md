---
title: '自定义远程目录或文件同步脚本xsync.sh'
date: 2019-04-15 13:00:40
tags: [Java]
categories: Shell编程
---
# 自定义远程目录或文件同步脚本xsync.sh


---

```shell
#!/bin/bash
#获取输入参数个数，如果没有参数，直接退出
pcount=$#  
if(( pcount<1 )); then  
     echo no args;  
     exit;  
fi  
  
#获取文件名称  
p1=$1;  
fname=`basename $p1`;  
#echo fname=$fname;  
  
#获取上级目录的绝对路径  
pdir=`cd -P $(dirname $p1) ; pwd`  
#echo pdir=$pdir ;  
  
#获取当前用户名称  
cuser=`whoami`  
  
#循环  
for((host=130;host<134;host=host+1)) ; do  
   echo ------------------ yla$host ----------------------  
   #echo $pdir/$fname $cuser@yla$host:$pdir  
   rsync -rvl $pdir/$fname $cuser@yla$host:$pdir  
done
```




