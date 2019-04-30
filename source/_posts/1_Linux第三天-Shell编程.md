---
title: 'Linux第三天-Shell编程'
date: 2019-04-25 23:00:55
tags: [Linux,Shell编程]
categories: Linux学习
---
# Linux第三天-Shell编程

## **什么是Shell**
Shell是**命令解释器**(command interpreter)，是Unix操作系统的用户**接口**，程序从用户接口得到输入信息，shell将用户程序及其输入翻译成操作系统内核（kernel）能够识别的指令，并且操作系统内核执行完将返回的输出通过shell再呈现给用户，下图所示用户、shell和操作系统的关系：
![image_1cj6aap5313rs62n1ba91e3u1qefm.png-33.6kB][1]
Shell也是一门**编程语言**，即shell脚本，shell是**解释执行**的脚本语言，可直接调用linux命令。类似于JS
一个系统可以存在多个shell，可以通过`cat /etc/shells`命令查看系统中安装的shell，不同的shell可能支持的命令语法是不相同的
```shell
[root@localhost ~]# cat /etc/shells
/bin/sh
#常用
/bin/bash
#伪用户使用
/sbin/nologin
/usr/bin/sh
/usr/bin/bash
/usr/sbin/nologin
/bin/tcsh
/bin/csh
#在ll的结果冲查找sh
[root@localhost ~]# ll /bin/ | grep sh
#通过下面的操作可以看出sh也是bash
lrwxrwxrwx. 1 root root          4 7月  23 19:29 sh -> bash
```

## **Shell的分类**
操作系统内核（kernel）与shell是独立的套件，而且都可被替换： 
不同的操作系统使用不同的shell； 
同一个kernel之上可以使用不同的shell。
**常见的shell分为两大主流：** 

sh（Linux常用）
: Bourne shell（sh）
Solaris,hpux默认shell
Bourne again shell（bash） ,Linux系统默认shell

csh （Unix常用）
: C shell(csh)
tc shell(tcsh)

查看当前系统使用的shell
: 
    ```shell
    [root@localhost ~]# echo $SHELL
    /bin/bash
    
    ```
## Shell中的环境定义


临时环境变量
: 所谓临时变量是指在用户在当前登陆环境生效的变量，用户登陆系统后，直接在命令行上定义的环境变量便只能在当前的登陆环境中使用。当退出系统后，环境变量将不能下次登陆时继续使用。
    ```shell
    [root@localhost ~]# echo $name
    
    [root@localhost ~]# name=zhangsan
    [root@localhost ~]# echo $name
    zhangsan
    
    ```
    
设置永久环境变量
: 通过将环境变量定义写入到配置文件中，用户每次登陆时系统自动定义，则无需再到命令行重新定义。定义环境变量的常见配置文件如下：

 1. /etc/profile
针对系统所有用户生效，此文件应用于所有用户每次登陆系统时的环境变量定义
2. 用户目录/.bash_profile
针对特定用户生效，当用户登陆系统后，首先继承/etc/profile文件中的定义，再应用用户目录/.bash_profile文件中的定义。

系统预定义的环境变量
: 系统环境变量对所有用户有效，如：`$PATH、$HOME、$SHELL、$PWD`等等，如下用echo命令打印上述的系统环境变量：
    ```shell
    [root@localhost ~]# echo $PATH
    /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin
    [root@localhost ~]# echo $HOME
    /root
    [root@localhost ~]# echo $SHELL
    /bin/bash
    [root@localhost ~]# echo $PWD
    /root
    
    ```

## **创建Shell脚本**
一个shell脚本通常包含如下部分

1. **首行**
    第一行内容在脚本的首行左侧，表示脚本将要调用的shell解释器，内容如下
    ```shell
    #!/bin/bash
    
    ```
    `#！`符号能够被内核识别成是一个脚本的开始，这一行必须位于脚本的首行，/bin/bash是bash程序的绝对路径，在这里表示后续的内容将通过bash程序解释执行。
2. **注释**
注释符号# 放在需注释内容的前面，如下
    ```shell
    #这是我的第一个shell程序
    
    ```
3. **内容**
可执行内容和shell结构

4. **第一个Shell程序**
    ```shell
    #!/bin/bash
    #这是我的第一个shell程序
    echo "你好，世界"
     
    echo "你好，小张"
    
    ```
5. 执行这个脚本
    ```shell
    [root@localhost test]# ./HelloWord.sh
    -bash: ./HelloWord.sh: 权限不够
    [root@localhost test]# chmod 777 HelloWord.sh 
    [root@localhost test]# ./HelloWord.sh
    你好，世界
    你好，小张
    
    ```
6. 执行脚本的三种方式
    - 输入脚本的绝对路径或相对路径
    ```shell
    ./HelloWord.sh
    
    ```
    这种情况下会新开一个shell环境
    - bash或sh +脚本
    ```shell
    bash HelloWord.sh
    ```
    <span class="red">注：当脚本没有x权限时，root和文件所有者通过该方式可以正常执行。</span>
    这种情况下不会新开一个shell环境，以下代码可以证明
    - 在脚本的路径前再加". " 或source
    ```shell
    source HelloWord.sh
    . ./HelloWord.sh
    ```
    ```shell
    [root@localhost test]# echo $name
    zhangsan
    [root@localhost test]# echo "echo \$name" >> HelloWord.sh 
    [root@localhost test]# ./HelloWord.sh 
    你好，世界
    你好，小张
    
    [root@localhost test]# bash HelloWord.sh 
    你好，世界
    你好，小张
    
    [root@localhost test]# . HelloWord.sh 
    你好，世界
    你好，小张
    zhangsan

    ```

## **Shell变量**
**变量：**是shell传递数据的一种方式，用来代表每个取值的符号名。
当shell脚本需要保存一些信息时，如一个文件名或是一个数字，就把它存放在一个变量中。

命名规范
: 

 1. 变量名称可以由**字母**，**数字**和**下划线**组成，但是**不能以数字**开头**，环境变量名建议大写**，便于区分。
 2. 在bash中，变量的默认类型都是**字符串型**，如果要进行数值运算，则必须指定变量类型为**数值型**。
 3. 变量用等号连接值，等号左右两侧**不能有空格**。
 4. 变量的值如果有空格，需要使用**单引号或者双引号包括**。
    ```shell
    [root@localhost test]# n1=1
    [root@localhost test]# n2=2
    [root@localhost test]# n=$n1+$n2
    [root@localhost test]# echo $n
1+2
[root@localhost test]# expr 1 + 2
3
[root@localhost test]# n3 = $n1+$n2
    bash: n3: 未找到命令...
    ```
    
    
变量分类
: Linux Shell中的变量分为用户自定义变量,环境变量，位置参数变量和预定义变量。
<span class="red">可以通过set命令查看系统中存在的所有变量</span>

 1. 系统变量：保存和系统操作环境相关的数据。`$HOME、$PWD、$SHELL、$USER`等等
2. 位置参数变量：主要用来向脚本中传递参数或数据，变量名不能自定义，变量作用固定。
3. 预定义变量：是Bash中已经定义好的变量，变量名不能自定义，变量作用也是固定的。
4. 自定义变量

用户自定义变量
: 用户自定义的变量由字母或下划线开头，由字母，数字或下划线序列组成，并且大小写字母意义不同，变量名长度没有限制。

设置变量
: 习惯上用大写字母来命名变量。变量名以字母表示的字符开头，不能用数字。

变量调用
: 在使用变量时，要在变量名前加上前缀`$`.
使用echo 命令查看变量值。`echo $A`

变量赋值
: 

 1. **定义时赋值**
    变量＝值 
    等号两侧不能有空格
    ```shell
    [root@localhost test]# AGE=18
    ```

    2. **将一个命令的执行结果赋给变量**
    ```shell
    [root@localhost test]# ls
HelloWord.sh
[root@localhost test]# lsvalue=`ls`
[root@localhost test]# echo $lsvalue 
HelloWord.sh
[root@localhost test]# lsvalue=`ll`
[root@localhost test]# echo $lsvalue 
总用量 4 -rwxrwxrwx. 1 root root 104 7月 24 23:04 HelloWord.sh
[root@localhost test]# lsvalue=$(ll)
[root@localhost test]# echo $lsvalue 
总用量 4 -rwxrwxrwx. 1 root root 104 7月 24 23:04 HelloWord.sh
    ```
        反引号，运行里面的命令，并把结果返回给变量lsvalue
3. **将一个变量赋给另一个变量**
    ```shell
    [root@localhost test]# test=hahaha
    [root@localhost test]# echo $test
hahaha
[root@localhost test]# newtest=lalalatest
[root@localhost test]# echo $newtest
    lalalatest
    [root@localhost test]# newtest=lalala$test
[root@localhost test]# echo $newtest
    lalalahahaha
    [root@localhost test]# newtest=lalala${test}
[root@localhost test]# echo $newtest
    lalalahahaha
    [root@localhost test]# newtest=lalala"$test"
[root@localhost test]# echo $newtest
    lalalahahaha
    
    ```
        单引号和双引号的区别：
        现象：单引号里的内容会全部输出，而双引号里的内容会有变化
        原因：单引号会将所有特殊字符脱意
      ```shell
      [root@localhost test]# newtest=lalala'$test'
[root@localhost test]# echo $newtest
lalala$test
[root@localhost test]# newtest=lalala"$test"
[root@localhost test]# echo $newtest
lalalahahaha
      ```
      
删除变量
:  
    ```shell
    [root@localhost test]# unset newtest
[root@localhost test]# echo $newtest


    ```
注意: readonly修饰的变量不能删除
用户自定义的变量，作用域为当前的shell环境。
    ```shell
    [root@localhost test]# readonly
    test=12
    [root@localhost test]# unset test
    -bash: unset: test: 无法反设定: 只读 variable
    ```

环境变量
: 用户自定义变量只在当前的shell中生效，而环境变量会在当前shell和其所有子shell中生效。如果把环境变量写入相应的配置文件，那么这个环境变量就会在所有的shell中生效。
export 变量名=变量值   申明变量
作用域：当前shell以及所有的子shell
```shell
[root@localhost test]# nn=1
[root@localhost test]# export nnn=2
[root@localhost test]# echo $nn
1
[root@localhost test]# echo $nnn
2
[root@localhost test]# bash
[root@localhost test]# echo $nn

[root@localhost test]# echo $nnn
2

```
位置参数变量
: 
|变量|描述|
|---|---|
|`$n`|n为数字，`$0`代表命令本身，`$1-$9`代表第一到第9个参数,十以上的参数需要用大括号包含，如`${10}`。|
|`$*`|代表命令行中所有的参数，把所有的参数看成一个整体。以`$1 $2 … $n`的形式输出所有参数
|`$@`|	代表命令行中的所有参数，把每个参数区分对待。以`"$1" "$2" … "$n"` 的形式输出所有参数|
|`$#`|代表命令行中所有参数的个数。添加到shell的参数个数|

    ```shell
    #!/bin/bash
    echo '$0='$0
    echo "$1="$1
    echo "\$2="$2
    echo "\$*="$@
    echo "\$@="$@
    echo "\$#="$#
    
    [root@localhost test]# ./haha.sh zhang 12 11 13 15
    $0=./loc.sh
    zhang=zhang
    $2=12
    $*=zhang 12 11 13 15
    $@=zhang 12 11 13 15
    $#=5
    
    ```
    
    **`$*` 和 `$@`的区别**
    `$*` 和 `$@` 都表示传递给函数或脚本的所有参数，不被双引号" "包含时，都以`"$1" "$2" … "$n"` 的形式输出所有参数
    当它们被双引号" "包含时，`"$*"` 会将所有的参数作为一个整体，以`"$1 $2 … $n"`的形式输出所有参数；`"$@"` 会将各个参数分开，以`"$1" "$2" … "$n"` 的形式输出所有参数
    代码如下
    ```shell
    #!/bin/bash
    echo '$0='$0
    echo "$1="$1
    echo "\$2="$2
    echo "\$*="$@
    echo "\$@="$@
    echo "\$#="$#
    
    echo "这是\$*的结果——不加引号"
    for i in $*
    do
    echo $i
    done
    
    echo "这是\$@的结果"
    for i in $@
    do
    echo $i
    done
    
    echo "这是\$*的结果——加双引号"
    for i in "$*"
    do
    echo $i
    done
    
    echo "这是\$@的结果——加双引号"
    for i in "$@"
    do
    echo $i
    done
    
    ```
![image_1cj6ghfa98pi1ka1a1uet4b69.png-90.4kB][2]
 ![image_1cj6giaut1e6g15sv1kp9oe1hrjm.png-4kB][3]
 
 **shift指令：**参数左移，每执行一次，参数序列顺次左移一个位置，`$#` 的值减1，用于分别处理每个参数，移出去的参数不再可用
 ```shell
    #!/bin/bash
    echo '$0='$0
    echo "$1="$1
    echo "\$2="$2
    echo "\$*="$@
    echo "\$@="$@
    echo "\$#="$#
    
    shift
    
    echo "\$1="$1
    echo "\$#="$#
 ```
![image_1cj6h0kl81hrfr691qpr16dk1ijs13.png-36.7kB][4]

预定义变量
: 
     |命令|描述|
     |---|---|
     |`$?`| 	执行上一个命令的返回值   执行成功，返回0，执行失败，返回非0（具体数字由命令决定）|
    |`$$`	|当前进程的进程号（PID），即当前脚本执行时生成的进程号|
    |`$!`	|后台运行的最后一个进程的进程号（PID），最近一个被放入后台执行的进程   &|
    ```shell
    #!/bin/bash
    pwd
    echo "\$$="$$
    ls /etc > out.log &
    echo "\$!="$!
    
    ```
    ![image_1cj6hjpkptlv1bopueml4eik91g.png-19.8kB][5]
    ```shell
    [root@localhost test]# pwd
/usr/test
[root@localhost test]# echo $?
0
[root@localhost test]# cdc
bash: cdc: 未找到命令...
[root@localhost test]# echo $?
127
    ```

read命令
: read [选项] 值
read -p(提示语句) -n(字符个数) -t(等待时间，单位为秒) –s(隐藏输入) 
    ```shell
    #!/bin/bash
    read -t 10 -p "请输入你的名字" name
    echo $name

    ```
    ![image_1cj6i78t31ir31knt3laoo1q2o1t.png-17.8kB][6]
    ```shell
    #!/bin/bash
    read -t 10 -sp "请输入你的名字" name
    echo $name

    ```
    ![image_1cj6i8toqv571deqa35are1i302q.png-16.9kB][7]
    ```shell
    #!/bin/bash
    read -t 10 -n 1 -p "请输入你的性别[m,w]" sex
echo $sex

    ```

运算符
: 格式 :expr m + n 或`$((m+n))` 注意expr运算符间要有空格
expr命令：对整数型变量进行算术运算
    ```shell
    [root@localhost test]# vi read.sh
    [root@localhost test]# vi read.sh
    [root@localhost test]# n1=1
    [root@localhost test]# n2=2
    [root@localhost test]# num=n1+n2
    [root@localhost test]# echo $num
n1+n2
[root@localhost test]# num=$n1+$n2
[root@localhost test]# echo $num
    1+2
[root@localhost test]# num=`expr $n1 + $n2`
    [root@localhost test]# echo $num
3
[root@localhost test]# num=$(($n1 + $n2))
    [root@localhost test]# echo $num
    3
    
    ```
    测试加减乘除
    
`$()`与`${}`的区别
:  `$( )`的用途和反引号 \`\`一样，用来表示优先执行的命令
`${ }` 就是取变量了 
`$((运算内容))` 适用于数值运算

条件测试
: 

 1. **内置test命令**
内置test命令常用操作符号[]表示，将表达式写在[]中，如下：
[ expression ]  
或者：
test expression 
**注意：** expression首尾都有个空格
测试范围：整数、字符串、文件
表达式的结果为真，则test的返回值为0，否则为非0。
当表达式的结果为真时，则变量$?的值就为0，否则为非0
2. **字符串测试**
test  str1 == str2    测试字符串是否相等 = 
test  str1 != str2    测试字符串是否不相等
test  str1            测试字符串是否**不为空**,不为空为true反之为false
test  -n str1     测试字符串是否不为空
test  -z  str1    测试字符串是否为空
![image_1cj6jrggo15d21tg510tu1kqi18v837.png-78.5kB][8]
； 命令连接符号
&& 逻辑与 条件满足，才执行后面的语句
[ -z “\$name” ] && echo  invalid  || echo ok
||  逻辑或，条件不满足，才执行后面的语句
test “\$name” == ”yangmi” && echo ok  || echo  invalid
3. **整数测试**
test   int1 -eq  int2    测试整数是否相等 equals
test   int1 -ge  int2    测试int1是否>=int2
test   int1 -gt  int2    测试int1是否>int2
test   int1 -le  int2    测试int1是否<=int2
test   int1 -lt  int2    测试int1是否<int2
test   int1 -ne  int2    测试整数是否不相等
4. **文件测试**
test  -d  file      指定文件是否目录
test  –e  file     文件是否存在 exists
test  -f  file     指定文件是否常规文件
test –L File     文件存在并且是一个符号链接 
test  -r  file    指定文件是否可读
test  -w  file    指定文件是否可写
test  -x  file    指定文件是否可执行
5. **多重条件测试**
条件1 –a 条件2 逻辑与  两个都成立，则为真
条件1 –o 条件2 逻辑或 	只要有一个为真，则为真
！ 条件			逻辑非	取反
```shell
[root@localhost test]# [ "11" == "11" -a "11" == "13" ] && echo "成功" || echo "失败"
失败
[root@localhost test]# [ "11" == "11" -a "11" == "11" ] && echo "成.." || echo "失败"
成功

```

## **流程控制语句**

if
: 
    ```shell
    #!/bin/bash
    read -p "请输出一个名字" name
    echo $name
    
    if [ "$name" == root ]
    then
        echo "你好，管理员"
    elif [ "$name" == hadoop ];then
        echo "hadoop是我的最爱"
    else
       echo "我的名字是"$name
    fi
    
    ```
    ![image_1cj6krps21ut7d0n1s8t1n9n183444.png-83.3kB][9]
   
case
: case命令是一个多分支的if/else命令，case变量的值用来匹配value1,value2,value3等等。匹配到后则执行跟在后面的命令直到遇到双分号为止(;;)case命令以esac作为终止符。
    ```shell
    #!/bin/bash
    
    case $1 in
    start)
            echo "启动一个程序"
            ;;
    restart)
            echo "重启一个程序"
            ;;
    stop)
            echo "停止一个程序"
            ;;
    *)
            echo "命令有误"
    esac
    ```
    ![image_1cj6lc8n51e6n1bne1dscli81tbe4h.png-56.3kB][10]
    
for循环
: 
    ```shell
    [root@localhost test]# for i in 1 2 3 4 5;do echo $i;done
[root@localhost test]# for i in {1,2,3,4,5};do echo $i;done
    [root@localhost test]# for i in {1..5};do echo $i;done
[root@localhost test]# for i in {2..5};do echo $i;done
    
    ```
    ```shell
    #i/bin/bash
    for((i=1;i<10;i+=2))
    do
    echo $i
    done
    
    sum=0
    for((i=1;i<=100;i++))
    do
        sum=$(($sum + $i))
done
echo $sum

    ```
    ![image_1cj6lrkaoaabip04os1slig1b4u.png-15.8kB][11]

while循环
: 
    ```shell
    #!/bin/bash
    sum=0
    i=0
    while((i<=100))
    do
            sum=$(($sum + $i))
            i=$(($i + 1))
    done
    
    echo $sum
    ```
    ![image_1cj6m3vvv17q814ev6ghhuu174f5b.png-10.9kB][12]
    
    ```shell
    sum=0
    i=0
    while [ $i -le 100 ]
    do
            sum=$(($sum + $i))
            i=$(($i + 1))
    done
    
    echo $sum

    ```

## **自定义函数**
函数代表着一个或一组命令的集合，表示一个功能模块，常用于模块化编程。以下是关于函数的一些重要说明：

 - 在shell中，函数必须先定义，再调用
 - 使用return value来获取函数的返回值
 - 函数在当前shell中执行，可以使用脚本中的变量。
 格式:
```shell
#!/bin/bash
function start(){
echo "启动一个程序"
}
stop(){
echo "停止一个程序"
}

start
```
传参
```shell
#!/bin/bash
function start(){
echo "启动一个程序"
echo $#
}
stop(){
echo "停止一个程序"
}

start $*

```
带返回值
```shell
#!/bin/bash
function start(){
echo "启动一个程序"
echo $#
return $?
}
stop(){
echo "停止一个程序"
}

a=`start $*`
echo "\$a"$a

```
**注意：**
如果函数名后没有（），在函数名和{ 之间，必须要有空格以示区分。
函数返回值，只能通过$? 系统变量获得，可以显示加：return 返回值，如果不加，将以最后一条命令运行结果，作为返回值。 return后跟数值n(0-255)

脚本调试
: 

 1. bash -x script
这将执行该脚本并显示所有变量的值。
2. bash -n script
不执行脚本只是检查语法的模式，将返回所有语法错误。
3. bash –v script
执行并显示脚本内容
4. 在shell脚本里添加  
set -x  对部分脚本调试

## **awk和sed**
cut
: 

 1. cut [选项]  文件名 默认分割符是制表符
2. 选项
    - -f 列号：    提取第几列
    - -d 分隔符：    按照指定分隔符分割列
    
    cut -f  2  aa.txt   提取第二列
    cut -d ":" -f 1,3 /etc/passwd  以:分割，提取第1和第3列
    cat /etc/passwd | grep /bin/bash | grep -v root | cut -d ":" -f 1    获取所有可登陆的普通用户用户名
cut的局限性    不能分割空格   df -h  不能使用cut分割
df -h | grep sda1 | cut -f 5







  ![1](http://static.zybuluo.com/zhangwen100/l0g3ya4o9zdkb8unpgsimmwd/image_1cj6aap5313rs62n1ba91e3u1qefm.png)
  ![2]( http://static.zybuluo.com/zhangwen100/kq5os9k7twb2j6i66c9x7zg9/image_1cj6ghfa98pi1ka1a1uet4b69.png)
  ![3]( http://static.zybuluo.com/zhangwen100/vfzhku7ixjnddh3gp8s50ua8/image_1cj6giaut1e6g15sv1kp9oe1hrjm.png)
  ![4]( http://static.zybuluo.com/zhangwen100/zmldxhc5rtw0dcd1xmekwjny/image_1cj6h0kl81hrfr691qpr16dk1ijs13.png)
  ![5]( http://static.zybuluo.com/zhangwen100/2kxp8e2jte0oqlcuwbptq86y/image_1cj6hjpkptlv1bopueml4eik91g.png)
  ![6]( http://static.zybuluo.com/zhangwen100/gmj3lhbl7gobahe63qwxfurg/image_1cj6i78t31ir31knt3laoo1q2o1t.png)
  ![7]( http://static.zybuluo.com/zhangwen100/v3864cbl23ompazqmkjtpj7q/image_1cj6i8toqv571deqa35are1i302q.png)
  ![8]( http://static.zybuluo.com/zhangwen100/r74tjdxwsj73q5aio2fynxt8/image_1cj6jrggo15d21tg510tu1kqi18v837.png)
  ![9]( http://static.zybuluo.com/zhangwen100/glam2ocmkz46lrdh3k0vsj83/image_1cj6krps21ut7d0n1s8t1n9n183444.png)
  ![10]( http://static.zybuluo.com/zhangwen100/ra6m3vnrigb6bt3ardlueaza/image_1cj6lc8n51e6n1bne1dscli81tbe4h.png)
  ![11]( http://static.zybuluo.com/zhangwen100/qt8gb5piwvm0zq2d4cp1sv6m/image_1cj6lrkaoaabip04os1slig1b4u.png)
  ![12]( http://static.zybuluo.com/zhangwen100/cv16dtphrvx2rh5dl07pwakl/image_1cj6m3vvv17q814ev6ghhuu174f5b.png)