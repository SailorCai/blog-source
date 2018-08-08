---
title: 本地仓库每次向github提交代码都需要输入账号密码的解决方法
date: 2018-07-03 21:20:06
comment: true
tags: [前端,git]
categories: [前端,技术]
---
当我们向github远程仓库push代码的时候可能会出现每次都要求输入账号密码的问题。

其实会出现这个问题的原因只是因为你在添加origin的时候使用的是https的方式，

最简单的解决办法就是移除https的方式，添加ssh的方式：

首先在命令行执行：git remote rm origin
<img src="git_pass1.png" alt="">

然后到git仓库页面，如上图、点击Use SSH切换，复制下面框中的地址

在命令行输入：git remote add origin 加上你刚刚复制的地址，然后回车。

接下来就不需要再输入账号密码啦！

当然，还有另外一种解决方式，请移步另一位同学的博客：https://blog.csdn.net/alfredseng/article/details/68061260