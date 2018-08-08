---
title: 执行git add . 后仍然报changes not staged for commit错误的解决方法
date: 2018-08-08 09:32:12
comment: true
tags: [前端,git]
categories: [前端,技术]
---
如果你的git仓库下面还有另外一个clone过来的git仓库，那么当你正常git add . 然后git commit的时候一定会得到如下图的报错：
<img src="Image.png" alt="">

并且上传到仓库的文件夹是空的

这个时候如果你去网上查找解决方案，可能10个人有九个人会告诉你是因为commit之前没有执行git add导致的，最后一个人可能知道原因，但是提供的方法可能并不能很好的解决问题

这里给出完整的解决方案：

#### 1、先强行删除clone来的目录下的 .git 文件夹
     删除方式：在该目录下打开命令行工具，执行 rd/s/q .git命令

    删除成功后执行ls .git命令提示如下内容说明删除成功
<img src="Image2.png" alt="">

#### 2、删除仓库中的空文件夹
    1）git rm -r --cached "themes/materail"

    2）git commit -m "remove empty folder"

    3）git push origin master

 

#### 3、回到仓库根目录重新提交代码
    1）git add .

    2）git commit -m "repush"

    3）git push origin master

 

这样就能保证不会报上面的错，并且删除了空文件夹，重新把clone下来的目录上传到了仓库中

__最后解释一下出现这种情况的原因__

由于你 clone 下来的文件夹也是一个 git 仓库，因此正常的 git add . 是无法提交该文件夹下的文件的，所以我们要做的就是删除文件夹下的 .git 文件夹，这样就可以通过  git add . 命令来提交内容了。