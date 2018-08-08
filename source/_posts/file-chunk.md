---
title: 通过File对象的slice()方法实现文件分片上传
date: 2018-08-08 16:04:09
comment: true
tags: [前端,javascript]
categories: [前端,技术]
---
__上传大文件时由于各种原因突然中断，然后整个文件都要从头开始上传，这种情况真的是非常让人抓狂，而文件分片上传也许能避免这种尴尬__

 

文件分片上传就是把文件分成一个个小块然后逐个上传，以文件的md5码作为文件的唯一标识，这样即使上传到一半中断了，等到下次再上传的时候我们通过对比文件的md5来确认是否是相同文件，确认后就可以在上次的基础上继续上传。

 

说到md5，我之前有一篇文章介绍了一个计算md5的工具了解一下：https://blog.csdn.net/logan_LG/article/details/80748093

 

不过有点遗憾的是，如果文件很大，那么计算md5也会消耗比较长的时间。不过这点时间相对于整个文件重新上传来说还是非常值得的。

 

这次的尝试是在腾讯的一个内网项目中，这需要后台同学的配合。我和后台的约定是：

__1、两个接口：__

__第一个是请求上传接口，需要传入的参数包括__

        1）文件名

        2）文件大小

        3）文件md5码

        4）游戏id

        5）page_type

__其中游戏id和page_type是由于场景所导致的参数差异，因此只有前三个参数是必须的。__

这个接口返回的主要参数包括：chunk_list、chunk_size、chunk_count

chunk_list是一个数组，里面是目前未上传分片的编号，比如说文件总大小为1000M，每个分片2M，这样一共就需要上传500个分片，那么chunk_list中包含的就是从1到500的数字，当然、如果这次是接着上次的继续上传那么chunk_list中可能只包含其中一部分，而且不一定是连续的数字，可能是......100、145、146、147......，这是因为如果其中一片报错我们会跳过当前分片继续上传下一个分片，等到上传到最后我们会再请求一次第一个接口来确认chunk_list中是否还有分片未上传。

chunk_size参数就是后台定好的每个分片的大小

chunk_count是总分片数量

 

__第二个接口是上传分片接口，需要传入的是一个FormData对象的实例：__
```bash
​var form = new FormData();
form.append('file_name', file_name);    //文件名参数
form.append('file_size', file_size);            //文件大小
form.append('file_md5', data.file_md5);    //文件md5码
form.append('chunk_md5', md5);            //分片md5码
form.append('chunk_idx', data.count);        //分片编号
form.append('chunk_size',  Math.min(data.chunk_size, (end-start)));    //分片大小
form.append('game_id', data.game_id);    //游戏id
form.append('page_type', 'upload_chunk');   
var blobSlice = File.prototype.slice || File.prototype.mozSlice || File.prototype.webkitSlice;       //兼容方式获取slice方法
var chunk_file = blobSlice.call(data.file, data.start, data.end);
chunk_file.name = file_name;
form.append('chunk_file', chunk_file);        //文件分片
 
```

通过FormData的append( )方法来添加参数

同样，最后两个参数不是必须的

 

这里需要说明一下、FormData是XMLHttpRequest Level 2添加的一个新接口， 利用FormData对象,我们可以通过JavaScript用一些键值对来模拟一系列表单控件,我们还可以使用XMLHttpRequest的send( )方法来异步的提交这个"表单".比起普通的ajax,使用FormData的最大优点就是我们可以异步上传一个二进制文件。FormData兼容性如下：
<img src="file_chunk1.png" alt="">

__以上是我和后台的约定，分片上传的步骤如下：__

    1、获取要上传的文件，通过上面提到的工具计算文件md5码

    2、把上个步骤计算的md5码和文件名、文件大小传给第一个接口并发送请求

    3、依据第一个接口返回的chunk_list等参数递归调用第二个接口，一片一片上传文件分片。

    4、所有分片上传完成后再次请求第一个接口，如果chunk_list中没有成员说明所有分片上传完成。

完成上面的步骤，后台在确认所有分片的上传完成后就可以进行分片合并了。

 

__除此之外我们可以在分片的基础上分多条线同时上传文件（一次性多次调第二个接口而无需等接口返回后在递归中才第二次调用，这相当于是多线程同时上传），这样上传速度将是加倍的__

 

此外我再第三个步骤中使用了数组的shift( )方法来获取当前分片的编号，这样上传掉一片移除掉一个编号会非常直观，而且应用在多线同时上传也会非常方便。

 

好了，这次的文字组织的可能有点乱，因为这次分片上传的尝试其实是一年前的事情了，因此组织起来比较困难了。这也说明一个真理：写博客要及时啊！

 

最后我把这次分片上传的js代码放到了github上：https://github.com/SailorCai/file-chunk

不过可能帮助不会很大，因为时间久远，代码中没有足够的注释，也没有demo可供参考，并且由于是一次内网的尝试 所以代码有点乱。