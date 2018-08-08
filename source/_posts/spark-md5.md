---
title: 前端通过spark-md5.js计算本地文件md5
date: 2018-06-20 15:43:09
comment: true
tags: [前端,javascript]
categories: [前端,技术]
---
### 背景：
说到本人第一次使用spark-md5.js还是差不多一年以前的时候了，当时后台老大说要搞一个文件分片上传的功能。我当时就心想：what？啥是文件分片上传，完全没听过好吗？
至于我当时内心那个慌就不多描述了，总之文件分片上传需要一个识别文件的唯一标识，而md5是非常合适的。spark-md5.js就是前端在文件上传前在本地计算md5的很可靠的方案
spark-md5.js是外国人写的，如果英文底子好且想了解更多信息可以到npm网站了解：https://www.npmjs.com/package/spark-md5

### 使用：
首先要做的当然是在html文件中引入spark-md5.js咯，根据自己的需求可以引入压缩版或者开发版

在此之前必须说明，这里用到了html5提供的FileReader接口，目前实现了这个接口的浏览器有FireFox3.6+ 、chrome6+、IE10+，因此如果需要更多的兼容的话、抱歉，我目前也没有找到好的方法
关于FileReader,这里有一篇高质量的博文可以了解一下：https://blog.csdn.net/jackfrued/article/details/8967667

这里提供了两个方法；一种是用SparkMD5.hashBinary( ) 直接将整个文件的二进制码传入直接返回文件的md5、这种方法对于小文件会比较有优势——简单并且速度快。
另一种方法是利用js中File对象的slice( )方法（File.prototype.slice( )）将文件分片后逐个传入spark.appendBinary( )方法来计算、最后通过spark.end( )方法输出结果，很明显，这种方法对于大型文件会非常有利——不容易出错，并且能够提供计算的进度信息

我们开始吧，接下来上代码：
#### 首先第一种方法：
``` bash
  var running = false;    //running用于判断是否正在计算md5
  function doNormalTest( input ) {    //这里假设直接将文件选择框的dom引用传入
      
      if (running) {    // 如果正在计算、不允许开始下一次计算
          return;
      }

      var fileReader = new FileReader(),    //创建FileReader实例
          time;

      fileReader.onload = function (e) {    //FileReader的load事件，当文件读取完毕时触发
          running = false;

          // e.target指向上面的fileReader实例
          if (file.size != e.target.result.length) {    //如果两者不一致说明读取出错
             alert("ERROR:Browser reported success but could not read the file until the end.");
          } else {
              console.log(Finished loading!success!!);
               return SparkMD5.hashBinary(e.target.result);    //计算md5并返回结果
               
          }
      };

      fileReader.onerror = function () {    //如果读取文件出错，取消读取状态并弹框报错
          running = false;
          alert("ERROR:FileReader onerror was triggered, maybe the browser aborted due to high memory usage.");
      };

      running = true;
      fileReader.readAsBinaryString( input.files[0] );    //通过fileReader读取文件二进制码
  };

```

#### 接下上第二种方法：

```bash
function doIncrementalTest( input ) {    //这里假设直接将文件选择框的dom引用传入
    if (running) {
        return;
    }

    //这里需要用到File的slice( )方法，以下是兼容写法
    var blobSlice = File.prototype.slice || File.prototype.mozSlice || File.prototype.webkitSlice,
        file = input.files[0],
        chunkSize = 2097152,                           // 以每片2MB大小来逐次读取
        chunks = Math.ceil(file.size / chunkSize),
        currentChunk = 0,
        spark = new SparkMD5(),    //创建SparkMD5的实例
        time,
        fileReader = new FileReader();

    fileReader.onload = function (e) {

        console("Read chunk number (currentChunk + 1) of  chunks ");

        spark.appendBinary(e.target.result);                 // append array buffer
        currentChunk += 1;

        if (currentChunk < chunks) {
            loadNext();
        } else {
            running = false;
            console.log("Finished loading!");
            return spark.end();     // 完成计算，返回结果
        }
    };

    fileReader.onerror = function () {
        running = false;
        console.log("something went wrong");
    };

    function loadNext() {
        var start = currentChunk * chunkSize,
            end = start + chunkSize >= file.size ? file.size : start + chunkSize;

        fileReader.readAsBinaryString(blobSlice.call(file, start, end));
    }

    running = true;
    loadNext();
} 

```
接下来你只要在input的onchange事件处理程序中调用doNormalTest或doIncrementalTest方法，并将input元素的dom节点传入就可以了

除此之外，作者在他的demo里面也有使用的详细实例。如果觉得不明白可以直接通过下面的下载方法下载后看demo

### 下载：
#### 最后，如果你需要这个工具可以通过npm直接安装，
```bash
$ npm i spark-md5
```

或者到作者的github上直接下载：
https://github.com/satazor/js-spark-md5
