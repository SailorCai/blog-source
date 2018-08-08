---
title: babel-loader，babel-core，babel-polify，babel-plugin-transform-runtime之间的关系
date: 2018-07-15 17:36:06
comment: true
tags: [前端,webpack]
categories: [前端,技术]
---
babel-core在npm官网中的解释是：Babel compiler core.

也就是Babel编译器的核心，因此意味着如果要使用babel-loader进行es6的转码你首先必须得安装babel-core
```bash
  $ npm install --save-dev babel-core
```
babel-core安装了就行，无需手动配置和引入。

搞懂了babel-loader和babel-core的关系，那babel-polify和babel-plugin-transform-runtime又是啥呢？

由于Babel默认只转换新的javascript句法（syntax），而不转换新的API，比如Iterator、Generator、Set、Maps、Proxy、Reflect、Symbol、Promise等全局对象，以及一些定义在全局对象上的方法（比如Object.assign）都不会转码。



因此，如果你的代码中用到了这些API那么你需要一个垫片库，babel polyfill就是这个作用。

babel polyfill有三种：
```bash
* babel-runtime
* babel-plugin-transform-runtime
* babel-polyfill
```

transform-runtime在使用webpack打包时需要配置到 .babelrc文件的的plugins中
```bash
"plugins": [
        ["transform-runtime", {
                "helpers": false,
                "polyfill": true,
                "regenerator": true,
                "moduleName": "babel-runtime"
        }]
]
```

后面的配置项不设置都将默认为true，其实可以完全像下面这样配置
```bash
"plugins": ["transform-runtime"]
```

babel-runtime和babel-plugin-transform-runtime的区别是使用babel-runtime时每次要转码一个api都需要手动添加

require('babel-runtime')；

而babel-plugin-transform-runtime会由工具自动添加，主要的功能是为api提供沙箱的垫片方案，不会污染全局的api。



最后，babel-polyfill则是通过修改全局prototype来实现API的垫片的，因此比较适合用在单独运行的项目中。