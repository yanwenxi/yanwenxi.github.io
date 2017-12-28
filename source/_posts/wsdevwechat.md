---
title: webstorm开发微信小程序代码提醒
date: 2017-02-04 17:20:57
tags: wechat webstorm
---
使用了微信提供的开发工具是真心难用，卡顿厉害、中英文切写注释换相当不爽、还没办法多开窗口，相信大家也遇到过这种现象。

下边我们介绍下webstorm来开发微信小程序的一些配置：

一、 首先File下找到settings,再找到FileType下Cascading Style Sheet 添加*.wxss

<div align=center>
    ![](http://oi28tswuq.bkt.clouddn.com/images/ws1.jpg-public)
</div>

二、 FileType下HTML 添加*.wxml

<div align=center>
    ![](http://oi28tswuq.bkt.clouddn.com/images/ws2.jpg-public)
</div>

三、 将wecharCode.jar下载下来，然后在webStorm 的 File -> import settings 中导入即可
[wecharCode.jar下载地址-点击即可](https://github.com/qbright/wechatCode-complete)

四、 配置已经完成，接下来你的代码就能出现微信小程序的代码提醒了。

<div align=center>
    ![](http://oi28tswuq.bkt.clouddn.com/images/ws3.jpg-public)
</div>