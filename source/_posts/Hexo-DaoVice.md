---
title: Hexo-DaoVice
date: 2019-03-27 11:43:29
tags: [Hexo]
categories: Hexo
---
## 注册
<hr/>
首先需要注册一个 DaoVoice，点击注册。

注册成功后，进入后台控制台，进入到 应用设置-->安装到网站 页面，可以得到一个 app_id：
![](https://i.loli.net/2019/03/27/5c9af1cd7a200.png)

## 设置
以 next 主题为例，打开 themes/next/layout/_partials/head.swig 文件中添加如下代码，位置随意：
```
{% if theme.daovoice %}
  <script>
  (function(i,s,o,g,r,a,m){i["DaoVoiceObject"]=r;i[r]=i[r]||function(){(i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;a.charset="utf-8";m.parentNode.insertBefore(a,m)})(window,document,"script",('https:' == document.location.protocol ? 'https:' : 'http:') + "//widget.daovoice.io/widget/0f81ff2f.js","daovoice")
  daovoice('init', {
      app_id: "{{theme.daovoice_app_id}}"
    });
  daovoice('update');
  </script>
{% endif %}
```

在主题配置文件 _config.yml，添加如下代码：

```
# Online contact 
daovoice: true
daovoice_app_id: 这里输入前面获取的app_id
```
**hexo s 调试模式下进行调试，效果满意后部署就可以看到最终效果啦**

