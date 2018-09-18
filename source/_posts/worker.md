---
title: 谈谈javascript中的多线程
date: 2018-09-18 23:15:08
comment: true
tags: [前端,javascript]
categories: [前端,技术]
---
不存在的，javascript中根本不存在多线程......
先不要慌，没有多线程我们可以模仿多线程呀！定时器、web workder等等。

**存在即合理，为啥javascript需要多线程？**

javascript对于现如今的web应用是不可或缺的，js在其中肩负了所有的交互和逻辑运算。由于浏览器在执行页面的javascript期间无法响应其他的用户事件，当页面中某部分js代码不可避免的要进行大开销的运算时浏览器本身可能变得非常缓慢甚至完全卡死，因此javascript的性能对于应用的流畅及用户体验至关重要。而多线程能解决这个问题。

但是就依据目前的发展来看,javascript中的多线程短时间内将不会实现。不过我们有备选方案。多线程的基本问题是不同的线程可以访问并修改相同的变量，当出现线程A要修改线程B正在修改的变量或类似的情况时，这回导致各种各样的问题。

### 定时器：
在web workers之前有一种方法是通过简单地把运行时间很长的计算拆分成独立的区块，然后使用js的定时器控制其执行：
```bash
var functionState = {};

function expensiveOperation() {
    var startTime = new Date().getMilliseconds();
    while ((new Date().getMilliseonds() - startTime) < 100) {
        //TODO：它用如下方法执行开销很大的运算
        //在迭代的语句块中执行100毫秒内完成的工作，
        //然后修改本函数外部“functionState”中的状态
        
    };
    if(!functionsState.isFinished){
        //退出10毫秒后再次执行expensiveOperation;
        setTimeout(expensiveOperation(), 10);
    };
};
```

用以上这种拆分模块运算的方法能够实现快速相应的界面，但是用这种方法架构运算可能不那么直接（甚至不可行）。同时现代计算机基本都是“多核”的，这意味着他们真正具备并发执行多个线程的能力。而如上使用延时器强制多核中的一个来完成所有的运算，这是在浪费计算机的处理能力。因此在javascript中模拟多线程时使用worker会更加容易且高效。

### Web Workers
以下代码展示了如何创建并启动Worker：
```bash
//创建并开始执行worker
var worker = new Worker("js/decrypt.js");

//注册事件处理程序，当worker给主线程发送信息时执行
worker.onmessage = function(e) {
    alert("the decrypted value is " + e.data);
};

//发送信息给worker，这里是指带解密的值
worker.postMessage(getValueToDecrypt());
```

**接下来看一下设想中的 js/decrypt.js的内容：**
```bash
//注册用来接收主线程信息的处理程序
onmessage = function (e) {
    var valueToDecrypt = e.data;
    //这里实现解密功能

    //把值返回给主线程
    postMessage(decryptedValue);
};
```

在页面上任何开销很大的（例如，长时间运行）javascript操作都应该委托给Worker，因为这将使得应用程序快速运行。