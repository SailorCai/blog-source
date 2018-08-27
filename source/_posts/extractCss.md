---
title: 解决webpack中css独立成文件后图片路径错误的问题
date: 2018-08-27 23:22:34
comment: true
tags: [前端,javascript,webpack]
categories: [前端,webpack]
---
__说明：此配置针对webpack4+__
使用webpack构建项目时将css文件独立出来是常见的做法，在webpack4.0以前是使用extract-text-webpack-plugin，
webpack4.0以后使用mini-css-extract-plugin，这里要讲的是后者
我在mini-css-extract-plugin的filename配置加入了css/路径：
```bash
new MiniCssExtractPlugin({
    filename: devMode ? 'css/[name].css' : 'css/[name].[hash].css',
    chunkFilename: devMode ? 'css/[id].css' : 'css/[id].[hash].css',
}),
```

也就是把独立出来的css文件放入dist下的css文件夹下
然而，build之后发现css中引入的图片并没有正确引入，并且报错了：

<img src="image" alt="">
<img src="image2" alt="">

经过url-loader处理后的图片是导出到了 dist/img/ 目录下

可以看到url是img/webpack.xxx.jpg，而正确的路径应该是 ../img/webpack.xxx.jpg

如果直接把css文件直接放在dist根目录下是没有问题的，但是按照习惯，我们一般会把css文件单独放到一个css文件夹下。

那么我们怎么解决这个问题呢？

在此之前我们先看看网上见得最多的解决方案，也就是在output的配置中加入一个 publicPath: '../' 配置，

<img src="image3" alt="">

我们按照这个配置编译看一下能否解决问题

<img src="image4" alt="">

css中的url路径的确可以了，但是可以看到，控制台出现了更多报错，并且css文件本身也不能正确引入了，因此这种方案在这种情况下是解决不了问题的。

由于output中的publicPatch配置会在所有用相对路径引用资源的地方都将上一个 ‘../’因此导致其他原本正确的路径也错了。

因此正确的解决方案应该是在引入MiniCssExtractPlugin.loader时使用对象方式，并在options目录下添加 publicPath: '../' 配置，这样就只会在css文件中引入的资源中添加 “../”，就不会影响其他文件中的路径了。
```bash
    module: {
        rules: [
            {
                test: /\.(sa|sc|c)ss$/,
                use: [
                    devMode?'style-loader':{
                        loader:MiniCssExtractPlugin.loader,
                        options: {
                            publicPath: '../'
                        }
                    },
                    'css-loader',
                    //'postcss-loader',
                    //'sass-loader'
                ]
            },
        ]
    },

```

配置好后run dev server或者build，就会看到css中的资源正确引入了。

更多配置信息可以参考我之前的博客：[多页项目的webpack配置](https://blog.csdn.net/logan_LG/article/details/81110577)
github仓库地址：https://github.com/SailorCai/muti-page-webpack