---
title: BitMap算法
date: 2019-04-22 23:10:01
tags: [算法,BitMap]
---
# BitMap算法
1）可进行数据的快速查找，判重，删除，一般来说数据范围是int的10倍以下。
2）去重数据而达到压缩数据
3) 用于爬虫系统中url去重、解决全组合问题。

## BitMap应用：排序示例
假设我们要对0-7内的5个元素(4,7,2,5,3)排序（这里假设这些元素没有重复）。那么我们就可以采用Bit-map的方法来达到排序的目的。要表示8个数，我们就只需要8个Bit（1Bytes），首先我们开辟1Byte的空间，将这些空间的所有Bit位都置为0(如下图：)
![](https://raw.githubusercontent.com/zzckm/PicGoImg/master/codeImages/20190422231214.png)

然后遍历这5个元素，首先第一个元素是4，那么就把4对应的位置为1（可以这样操作 p+(i/8)|(0×01<<(i%8)) 当然了这里的操作涉及到Big-ending和Little-ending的情况，这里默认为Big-ending。不过计算机一般是小端存储的，如intel。小端的话就是将倒数第5位置1）,因为是从零开始的，所以要把第五位置为一（如下图）：
![](https://raw.githubusercontent.com/zzckm/PicGoImg/master/codeImages/20190422231236.png)
然后再处理第二个元素7，将第八位置为1,，接着再处理第三个元素，一直到最后处理完所有的元素，将相应的位置为1，这时候的内存的Bit位的状态如下：
![](https://raw.githubusercontent.com/zzckm/PicGoImg/master/codeImages/20190422231303.png)

然后我们现在遍历一遍Bit区域，将该位是一的位的编号输出（2，3，4，5，7），这样就达到了排序的目的。
## bitmap排序复杂度分析
Bitmap排序需要的时间复杂度和空间复杂度依赖于数据中最大的数字。

bitmap排序的时间复杂度不是O(N)的，而是取决于待排序数组中的最大值MAX，在实际应用上关系也不大，比如我开10个线程去读byte数组，那么复杂度为:O(Max/10)。也就是要是读取的，可以用多线程的方式去读取。时间复杂度方面也是O(Max/n)，其中Max为byte[]数组的大小，n为线程大小。

假设最大数值为11，一个byte是占8个bit的 因此需要2个bytes 16bit 假设整数有：1.2.5.7.11
![](https://raw.githubusercontent.com/zzckm/PicGoImg/master/codeImages/20190422231754.png)
当传入一个值来判断是否存在于这个整数向量内时
```java
public void add(int num){
        // num/8得到byte[]的index
        int arrayIndex =num/8= num >> 3; 
        
        // num%8得到在byte[index]的位置
        int position =num%8= num & 0x07; 
        
        //将1左移position后，那个位置自然就是1，然后和以前的数据做|，这样，那个位置就替换成1了。
        bits[arrayIndex] |= 1 << position; 
    }
```
通过