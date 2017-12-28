---
title: 跨域之HTML5 postMessage
date: 2016-12-13 10:23:36
tags: 跨域、postMessage、html5
---
　　先了解下什么是同源策略，简单来说，协议+域名+端口三者必须相同，若有一个不同，即为跨域！
　　相关图解如下：
<div align=center>
    ![](http://oi28tswuq.bkt.clouddn.com/images/corssDomain.png-public)
</div>
    
　　HTML5中最酷的新功能之一就是 *跨文档消息传输Cross Document Messaging*。下一代浏览器都将支持这个功能：Chrome 2.0+、Internet Explorer 8.0+, Firefox 3.0+, Opera 9.6+, 和 Safari 4.0+ 。 Facebook已经使用了这个功能，用postMessage支持基于web的实时消息传递。
```
otherWindow.postMessage(message, targetOrigin);
```
otherWindow: 对接收信息页面的window的引用。可以是页面中iframe的contentWindow属性；window.open的返回值；通过name或下标从window.frames取到的值。
message: 所要发送的数据，string类型。
targetOrigin: 用于限制otherWindow，“*”表示不作限制

a.com/index.html中的代码：
```
<iframe id="ifr" src="b.com/index.html"></iframe>
<script type="text/javascript">
window.onload = function() {
    var ifr = document.getElementById('ifr');
    var targetOrigin = 'http://b.com';  // 若写成'http://b.com/c/proxy.html'效果一样
                                        // 若写成'http://c.com'就不会执行postMessage了
    ifr.contentWindow.postMessage('I was there!', targetOrigin);
};
</script>
```

b.com/index.html中的代码：
```
<script type="text/javascript">
    window.addEventListener('message', function(event){
        // 通过origin属性判断消息来源地址
        if (event.origin == 'http://a.com') {
            alert(event.data);    // 弹出"I was there!"
            alert(event.source);  // 对a.com、index.html中window对象的引用
                                  // 但由于同源策略，这里event.source不可以访问window对象
        }
    }, false);
</script>
```
