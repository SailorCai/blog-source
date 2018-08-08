---
title: 关于jquery的click()和trigger('click')方法的一点问题
date: 2017-9-20 15:13:05
comment: true
tags: [前端,javascript]
categories: [前端,技术]
---
在原生js中如果要模拟事件其实比较麻烦的事情，因为要考虑到各个浏览器对dom标准的兼容，同时还要进行多个步骤（创建event对象、初始化、正式触发等步骤）。不过jquery就有一个非常简单好用的click()或者trigger('click')方法来模拟点击事件

这两个方法可以说是非常简单好用了。但是这里我其实要说的是这个方法的一点问题。

问题出现的场景是：当某个元素的点击事件需要经过判断后才触发时——也就是在点击后需要延迟一定时间的情况下，click()或者trigger()方法无法再触发click事件
比如一个< input type="file">元素，如下图：
<img src="jq_click1.png" alt="">

如果我们要在点击‘判断权限’按钮后发送一个请求到后台，并在确认权限后用click()或trigger('click')触发< input type="file">元素的click来选择文件。那么很有可能在请求返回后完全触发不了< input type="file">元素的click事件，也不能选择文件。
    
几次遇到这种情况，后来我用setTimeout();模拟了类似的情况。代码如下：

``` bash
  setTimeout(function(){
      $(".fileInput").click();
  }, 1000);
```
通过运行以上js代码我发现，只要延时超过1s，click事件就触发不了。想了很久没有想通为什么，也没有研究过jquery源码（当然同样的情况可能在使用原生方法模拟click事件时也存在）。
有没有知道的同学来交流一下。