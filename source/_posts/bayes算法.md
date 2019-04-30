---
title: bayes算法
date: 2019-04-22 23:24:49
tags: [bayes,算法]
categories: 算法
---
# 贝叶斯定理
 每次提到贝叶斯定理，我心中的崇敬之情都油然而生，倒不是因为这个定理多高深，而是因为它特别有用。这个定理解决了现实生活里经常遇到的问题：已知某条件概率，如何得到两个事件交换后的概率，也就是在已知P(A|B)的情况下如何求得P(B|A)。这里先解释什么是条件概率：
 表示事件B已经发生的前提下，事件A发生的概率，叫做事件B发生下事件A的条件概率。其基本求解公式为：![](https://raw.githubusercontent.com/zzckm/PicGoImg/master/codeImages/20190422232825.png)

 贝叶斯定理之所以有用，是因为我们在生活中经常遇到这种情况：我们可以很容易直接得出P(A|B)，P(B|A)则很难直接得出，但我们更关心P(B|A)，贝叶斯定理就为我们打通从P(A|B)获得P(B|A)的道路。
下面不加证明地直接给出贝叶斯定理：
![](https://raw.githubusercontent.com/zzckm/PicGoImg/master/codeImages/20190422232841.png)
      

      
# 朴素贝叶斯分类
> 朴素贝叶斯分类是一种十分简单的分类算法，叫它朴素贝叶斯分类是因为这种方法的思想真的很朴素，朴素贝叶斯的思想基础是这样的：对于给出的待分类项，求解在此项出现的条件下各个类别出现的概率，哪个最大，就认为此待分类项属于哪个类别。通俗来说，就好比这么个道理，你在街上看到一个黑人，我问你你猜这哥们哪里来的，你十有八九猜非洲。为什么呢？因为黑人中非洲人的比率最高，当然人家也可能是美洲人或亚洲人，但在没有其它可用信息下，我们会选择条件概率最大的类别，这就是朴素贝叶斯的思想基础。

![](https://raw.githubusercontent.com/zzckm/PicGoImg/master/codeImages/20190422233021.png)
![](https://raw.githubusercontent.com/zzckm/PicGoImg/master/codeImages/20190422233050.png)