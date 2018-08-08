---
title: 用div模拟文本域实现高度自动撑开以及相关问题的解决方法
date: 2017-07-10 15:00:53
comment: true
tags: [前端,javascript]
categories: [前端,技术]
---
前段时间项目里面用到了一个高度随着内容自动撑开的文本域效果，这一点传统的textarea标签是满足不了的，而我们要做的只是在div中加一个contenteditable="true"的属性，以下是现实效果。
<img src="div_text2.png" alt="">
但是接下来就遇到了问题，当你在模拟的文本域中复制内容再粘贴到相同类型的文本域里面时问题就出现了
<img src="div_text1.png" alt="">
没错，你复制的内容在接下来粘贴时会带有html标签和对应的样式内容，这可不是我们想要的效果。说实话，解决这问题还是花了点时间，找了很久才找到最合适的办法。方法就是给这个div的style属性中加上:
    "user-modify:read-write-plaintext-only;-webkit-user-modify:read-write-plaintext-only;"

的样式，这个内容的意思就是指定这个div不管是读取（复制）还是写入都只允许纯文本内容。ok！这样就万事大吉啦？
其实并没有。。。
当你给div加上上述样式后会发现即使把contenteditable的值改为false后他的内容依然是可更改和输入的。这个问题我的做法是动态更改样式，当需要div可输入时加入上述样式，不需要时就删除，因为项目是基于vue这样的数据驱动页面的框架，因此实施起来其实并不麻烦，但是如果不用这样的框架可能就会比较痛苦了。