---
title: IE兼容问题之 “SCRIPT5：拒绝访问” (document.domain)
date: 2018-08-17 17:19:50
comment: true
tags: [前端,浏览器]
categories: [前端,技术]
---
你可能也遇到过IE下报：SCRIPT5:：拒绝访问 的错，说实话，这真的是我见过最恶心的兼容问题之一了，效果如下：

<img src="error.png" alt="">

那么出现这个问题的原因是啥呢？
报这个错的原因是你的页面使用了iframe嵌入页面，同时在父窗口或者子窗口设置了document.domain为某个域，但是设置的不一样，或者父窗口设置了，但是子窗口没有设置。这样不仅会导致部分js代码无法执行
而且可能会导致已经发出去的ajax请求在控制台的network中明明看到数据正常返回，但是浏览器完全无视，直接判定请求失败。

__解决方法：__
去掉document.domain的配置，或者父子窗口都设置一样的值。