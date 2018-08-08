---
title: 多页项目的webpack配置
date: 2018-07-19 11:05:31
comment: true
tags: [前端,webpack]
categories: [前端,技术]
---
在我们使用vue，angular等框架开发的时候，大多数都是构建单页项目。而且，像这样的框架都有对应的命令一步生成webpack配置（比如vue的vue init webpack my-project之类）。

 

但是如果我们要开发多页面项目的时候就没有这么方便了，你必须要自己配置webpack。

 

单页项目和多页项目的区别在于单页项目所有的js，css等资源只需要在入口html文件中引入，你甚至都不需要额外配置，像html-webpack-plugin这样的插件自动就帮你把js和css插入了到入index.html中了。

 

而多页项目下每个页面除了有部分共享资源外，还需要引入每个页面单独的js，css等文件。

 

面对这个区别，关键就在于html-webpack-plugin插件的配置。

首先我们假设所有的页面入口文件都在 src/html/ 下，这样我们可以通过nodejs的fs模块同步读取该目录下的所有文件的名字：
```bash
let htmlArr = fs.readdirSync(path.resolve(__dirname, 'src/html'));
```

htmlArr中将会保存一个html目录下所有文件名的一个数组，然后我们通过遍历这个数组来获取webpack的entry配置对象和html-webpack-plugin的配置对象：

```bash
let entrys = {};
let htmlPlugins = [];
 
for(let item of htmlArr){
  //我们只需要.html前面的文件名
  let name = item.split('.html')[0];
  htmlPlugins.push(new WebpackHtmlPlugin({
    filename: item,
    template: path.resolve(__dirname, `src/html/${item}`),
    //common代表公共模块，name就是html对应的同名js文件
    //这个配置将会自动在生成的html中插入你指定的js
    chunks: ['common', name]
  }));
  //配置入口
  entrys[name] = `./src/js/${name}.js`;
};
```

接下来在webpack配置中传入上面生成的entrys和htmlPlugins配置对象：
```bash
module.exports = merge(base, {
  //传入entry配置
  entry: entrys,
  devtool: "cheap-module-eval-source-map",
  devServer: {
    contentBase: path.join(__dirname, "dist"),
    compress: true,
    port: 8080,
    //hot: true,
    index: 'index.html',
    open: true
  },
  plugins: [
    //传入html-webpack-plugin配置对象
    ...htmlPlugins,
  ]
});

```
以上配置是我开发环境下的配置，生产环境下内容会多一点点。配置好之后编译，你就就会发现html-webpack-plugin为你生成了多个页面入口html：
<img src="muti_webpack1.png" alt="">

其中一个比较关键的地方除了html-webpack-plugin的配置外就是使用node的fs模块同步读取html文件家中所有文件的名字，这样你只要在文件夹中新增一个页面入口webpack就能自动读取到：

 

当然这都是些基础配置，不过已经可以拿来用了。

这里我将我的配置内容上传到了github上：https://github.com/SailorCai/muti-page-webpack