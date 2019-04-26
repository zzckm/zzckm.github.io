---
title: 'Hadoop-Eclipse插件编译'
date: 2019-04-25 23:00:45
tags: [Hadoop,Eclipse]
---
# Hadoop-Eclipse插件编译

**1. 安装ant**
: 

 - 下载
    [下载地址][1]
 - 配置环境变量
    ```shell
    ANT_HOME=D:\SoftWare\Tools\apache-ant-1.9.13
    Path=原路径;%ANT_HOME%\bin
    ```
     ![image_1cjnc9n6gt55kb11p1i5id1oet9.png-24.5kB][2]
 - 测试是否安装成功
    ```shell
    ant -version
    ```
     ![image_1cjnce6me19101j8hdn21nvli2v13.png-39kB][3]

**2. 下载Hadoop**
: 

 - 下载
    [下载地址][4]
 - 解压
    ![image_1cjnckctj1o3r143a13dstk31pa72g.png-73.5kB][5]
**注：** hadoop文件存放目录不要带有空格，不然编译不成功

**3. 下载Eclipse**
: 

 - 下载
 [下载地址][6]
- 解压
![image_1cjncpovh8u9pfk145fptc1etm2t.png-85.4kB][7]

**4. 下载hadoop2x-eclipse-plugin源码包**
: 

 - 下载
 [下载地址][8]

**5. 修改配置文件**
: 

 - 修改bulid.xml
 ![image_1cjnd1m2c1h9ad3p1m1ro1n178u3q.png-105.2kB][9]
    ```shell
     <target name="compile" depends="init, ivy-retrieve-common" unless="skip.contrib">
        <echo message="contrib: ${name}"/>
        <javac
         encoding="${build.encoding}"
         srcdir="${src.dir}"
         includes="**/*.java"
         destdir="${build.classes}"
         debug="${javac.debug}"
         deprecation="${javac.deprecation}">
         <classpath refid="classpath"/>
        </javac>
      </target>
    ```
        ```shell
        <target name="compile" unless="skip.contrib">
            <echo message="contrib: ${name}"/>
    <javac
             encoding="${build.encoding}"
             srcdir="${src.dir}"
             includes="**/*.java"
             destdir="${build.classes}"
             debug="${javac.debug}"
             deprecation="${javac.deprecation}">
             <classpath refid="classpath"/>
            </javac>
      </target>
        ```
        
 - 修改
 ![image_1cjnd7gqs1db91v6i11kh12ae6i47.png-116.6kB][10]
 ![image_1cjndk6641mo9a61r2mltr5av4k.png-246.9kB][11]
 ```shell
 ##以及下面的内容
 slf4j-api.version=1.7.25
 slf4j-log4j12.version=1.7.25
 ##下面这个文件还得从其他lib目录复制过来
 htrace.version=3.1.0-incubating

 ```
 

**6. 开始执行编译**    
: 
 ```shell
 D:\SoftWare\Tools\hadoop2x-eclipse-plugin-master\src\contrib\eclipse-plugin>ant jar -Dhadoop.version=2.7.6 -Declipse.home=D:\SoftWare\Eclipse\work-eclipse -Dhadoop.home=F:\hadoop-2.7.6
 
 ```

**7. 编译结果在一下目录**
: ![image_1cq2rtt82tq212ds1vg41ag94he9.png-21.9kB][12]


  [1]: http://archive.apache.org/dist/ant/binaries/
  [2]: http://static.zybuluo.com/zhangwen100/t85jgia7brv215zc48vp8pei/image_1cjnc9n6gt55kb11p1i5id1oet9.png
  [3]: http://static.zybuluo.com/zhangwen100/503jfvaz6o337en8joq0ai91/image_1cjnce6me19101j8hdn21nvli2v13.png
  [4]: https://mirrors.tuna.tsinghua.edu.cn/apache/hadoop/common/
  [5]: http://static.zybuluo.com/zhangwen100/n8xlzfgdtpevhtxbbjmw7p03/image_1cjnckctj1o3r143a13dstk31pa72g.png
  [6]: http://www.eclipse.org/downloads/download.php?file=/technology/epp/downloads/release/photon/R/eclipse-java-photon-R-win32-x86_64.zip
  [7]: http://static.zybuluo.com/zhangwen100/orsrgmj7o7lda85sswwxrclo/image_1cjncpovh8u9pfk145fptc1etm2t.png
  [8]: https://codeload.github.com/winghc/hadoop2x-eclipse-plugin/zip/master
  [9]: http://static.zybuluo.com/zhangwen100/svzace3pekdfahymmyw13sli/image_1cjnd1m2c1h9ad3p1m1ro1n178u3q.png
  [10]: http://static.zybuluo.com/zhangwen100/4e0laes86qoao7tfg22f0gdn/image_1cjnd7gqs1db91v6i11kh12ae6i47.png
  [11]: http://static.zybuluo.com/zhangwen100/hb9uzncme761fmaw8xpkyavi/image_1cjndk6641mo9a61r2mltr5av4k.png
  [12]: http://static.zybuluo.com/zhangwen100/rdg6y6lmc1m9hd79xtp72nie/image_1cq2rtt82tq212ds1vg41ag94he9.png