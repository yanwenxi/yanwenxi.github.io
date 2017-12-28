---
title: 前端路由的两种实现原理及相关api介绍
date: 2016-08-16 15:01:06
tags:  路由 html5
---
   早期的路由都是后端实现的，直接根据 url 来 reload 页面，页面变得越来越复杂服务器端压力变大，随着 ajax 的出现，页面实现非 reload 就能刷新数据，也给前端路由的出现奠定了基础。我们可以通过记录 url 来记录 ajax 的变化，从而实现前端路由。

   本文主要讲两种主流方式实现前端路由。
#### History API
   不细说每一个 API 的用法，重点说其中的两个新增的API history.pushState 和 history.replaceState
   
   这两个 API 都接收三个参数，分别是
   
   - 状态对象（state object） — 一个JavaScript对象，与用pushState()方法创建的新历史记录条目关联。无论何时用户导航到新创建的状态，popstate事件都会被触发，并且事件对象的state属性都包含历史记录条目的状态对象的拷贝。
   - 标题（title） — FireFox浏览器目前会忽略该参数，虽然以后可能会用上。考虑到未来可能会对该方法进行修改，传一个空字符串会比较安全。或者，你也可以传入一个简短的标题，标明将要进入的状态。
   -  地址（URL） — 新的历史记录条目的地址。浏览器不会在调用pushState()方法后加载该地址，但之后，可能会试图加载，例如用户重启浏览器。新的URL不一定是绝对路径；如果是相对路径，它将以当前URL为基准；传入的URL与当前URL应该是同源的，否则，pushState()会抛出异常。该参数是可选的；不指定的话则为文档当前URL。
   相同之处是两个 API 都会操作浏览器的历史记录，而不会引起页面的刷新。
   
   不同之处在于，pushState会增加一条新的历史记录，而replaceState则会替换当前的历史记录。
   
   我们在控制台输入
   ```
   window.history.pushState(null, null, "https://www.baidu.com/?name=orange");
   好，我们观察此时的 url 变成了这样
   https://www.baidu.com/?name=orange
   ```
   其他的用法如下：
   ```
   window.history.pushState(null, null, "https://www.baidu.com/name/orange");
   //url: https://www.baidu.com/name/orange
   
   window.history.pushState(null, null, "?name=orange");
   //url: https://www.baidu.com?name=orange
   
   window.history.pushState(null, null, "name=orange");
   //url: https://www.baidu.com/name=orange
   
   window.history.pushState(null, null, "/name/orange");
   //url: https://www.baidu.com/name/orange
   
   window.history.pushState(null, null, "name/orange");
   //url: https://www.baidu.com/name/orange
   ```
   注：注意:这里的 url 不支持跨域，当我们把 www.baidu.com 换成 baidu.com 时就会报错。
   接上文说，每次改变 url 页面并没有刷新，同样根据上文所述，浏览器会产生历史记录，这就是实现页面无刷新情况下改变 url 的前提。

#### hash
   我们经常在 url 中看到 #，这个 # 有两种情况，一个是我们所谓的锚点，比如典型的回到顶部按钮原理等，路由里的 # 不叫锚点，我们称之为 hash，大型框架的路由系统大多都是哈希实现的。
    
   同样我们需要一个根据监听哈希变化触发的事件 —— hashchange 事件
    
   我们用 window.location 处理哈希的改变时不会重新渲染页面，而是当作新页面加到历史记录中，这样我们跳转页面就可以在 hashchange 事件中注册 ajax 从而改变页面内容。