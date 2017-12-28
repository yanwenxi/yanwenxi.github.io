---
title: 如何在github上下载单个文件夹？
date: 2016-3-12 15:19:07
tags: git github
---
### 第一种方式：
Git1.7.0以后加入了Sparse Checkout模式，这使得Check Out指定文件或者文件夹成为可能。

具体实现如下：
```
$mkdir project_folder
$cd project_folder
$git init
$git remote add -f origin <url>   url要为https格式的。
```
上面的代码会帮助你创建一个空的本地仓库，同时将远程Git Server URL加入到Git Config文件中。

接下来，我们在Config中允许使用Sparse Checkout模式：
```
$git config core.sparsecheckout true
```

接下来你需要告诉Git哪些文件或者文件夹是你真正想Check Out的，你可以将它们作为一个列表保存在 .git/info/sparse-checkout 文件中。 
例如：
```
$echo “libs” >> .git/info/sparse-checkout
$echo “apps/register.go” >> .git/info/sparse-checkout
$echo “resource/css” >> .git/info/sparse-checkout
```
最后，你只要以正常方式从你想要的分支中将你的项目拉下来就可以了：
```
$git pull origin master
```
### 第二种方式：
可以用谷歌的一款插件——Octotree,安装上此插件，当打开github页面时，页面的左上角会出现一个小箭头，即左侧的工具栏 , 它相当于一个文件浏览器 , 可以看到整个项目的文件目录 , 在鼠标移动到相应文件下还会出现下载按钮 , 也很方便下载文件。如下图
![github](http://oi28tswuq.bkt.clouddn.com/images/git-single.png)