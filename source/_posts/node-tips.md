---
title: node一些小知识点积累
date: 2016-8-15 22:10:58
tags: node
---
#### nodemon
每次我们对app.js文件作修改，都需要停止服务器并且重启。在开发过程中，这个过程太麻烦了，所以我们建议不使用node.js，而是使用nodemon服务器。要安装nodemon，需再次使用npm：
$ npm install --save-dev nodemon
$ nodemon app.js
每次对app.js文件作修改并且保存时， nodemon会自动重启Node应用。
#### npm install –d
-d标志告诉npm把依赖项安装在本地。这个语法十分明确：可以丢开这个-d，因为它被设置为默认将依赖项安装到本地。
