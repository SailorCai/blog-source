---
title: url-loader不能处理html中引入的图片问题的解决方案
date: 2018-08-27 00:44:27
comment: true
tags: [前端,javascript]
categories: [前端,技术]
---
在我们使用webpack进行项目构建的时候，url-loader是个非常有用的工具，
与file-loader相比，url-loader能将图片大小在limit限定值之内的将图片转译成base64格式的字符串，
这样能直接减少很多不必要的http请求，在应用性能提升上的效益还是非常可观的。

 
我们项目中引入图片的方式基本上可以分为三种：
1、在js中import图片然后赋值给图片的src属性
```bash
import logo from '../img/logo.png';
document.getElementById('box').src = logo;
```

2、css中设置元素背景图片
```bash
.box{background-image: url(../img/logo.png)}
```

然后在js中通过import ‘@/css/index.css’的形式引入

3、在html的img标签中直接写入src属性，且一般是相对路径
```bash
<img src="img/logo.png" />
```

对于前两种情况，由于都是通过import的方式引入的图片和css文件，因此图片能得到正确处理，但是第三种请况是得不到处理的。

解决方案：
在配置中加入html-withimg-plugin

1、下载：npm install html-withimg-plugin --save-dev

2、在html-webpack-plugin的配置中加入如下配置：
```bash
new WebpackHtmlPlugin({
        filename: item,
        template: 'html-withimg-loader!'+path.resolve(__dirname, 'src/html/index.html'),
        chunks: ['js/common', name]
    })

```

完成配置后再次编译就会发现html中引入的图片也得到了相应的处理

更多配置信息可以参考我之前的博客：[多页项目的webpack配置](https://blog.csdn.net/logan_LG/article/details/81110577)
github仓库地址：https://github.com/SailorCai/muti-page-webpack
