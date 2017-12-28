---
title: 移动端遇到的坑-fixed不兼容
date: 2015-12-16 12:41:23
tags: 移动端、fixed
---
### fixed不兼容，解决办法用iscroll.js

移动端大多数浏览器不兼容position:fixed，而pc端只有ie6不兼容position:fixed    
在移动端开发中，使用position:fixed固定定位，很多手机支持不好，尤其是在定位的区域还需要输入内容调取虚拟键盘的时候会出现很多很多的问题
解决这个问题，就不能使用固定定位了，而是使用局部滚动处理（isScroll.js）
实现竖向局部滚动，需要把局部滚动的最外层容器的高度设定好。
实现横向局部滚动，需要把局部滚动的最外层容器的宽度设定好。
实现局部滚动：
```
var   实例=new IScroll（需要局部滚动的区域，{参数配置}）;
```
初始化成功后，只对当前容器中的第一个子元素实现了滚动操作。
为了让滚动条只显示在局部滚动的区域，需要给局部滚动的最外层容器加上position:relative;
```
var myscroll=new IScroll(要实现局部滚动区域的类名，{
    scrollbars:true，
    fadeScrollbars:true,//滚动时显示滚动条，否则滚动条隐藏。
    mouseWheel:true,
    bounce:false  //禁止运动到边界反弹。
    click:true //为了防止滑动过程中的误操作，默认把点击事件关闭了，想让点击时需要开启。
});
```

* myScroll.refresh:当滚动区域的内容发生改变的时候,我们让当前的实例刷新一下,这样的话滚动区域的相关值都会跟着重新计算
* myScroll.scrollToElement([ele],[time]):用多长的时间滚动到具体的某一个元素位置
* myScroll.scrollTo(x,y,time):滚动到具体的坐标位置

以上常用 ，下面的可以看文档了解学习下，毕竟艺多不压人，哈哈！！！
```
options.useTransform     true
options.useTransition      true
options.HWCompositing  true

options.bounce  true
options.click  false
options.mouseWheel  false
options.scrollX、options.scrollY

options.tap  element.addEventListener('tap', doSomething, false); \\ Native or tap: 'myCustomTapEvent'

scrollbars  flase
fadeScrollbars  false

myScroll.destroy();
myScroll = null;
```

beforeScrollStart，在用户触摸屏幕但还没有开始滚动时触发。
scrollCancel，滚动初始化完成，但没有执行。
scrollStart，开始滚动
scroll，内容滚动时触发，只有在scroll-probe.js版本中有效，请参考onScroll event。
scrollEnd，停止滚动时触发。
flick，用户打开左/右。
zoomStart，开始缩放。
zoomEnd，缩放结束。
