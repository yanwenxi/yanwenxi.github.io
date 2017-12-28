---
title: 使浏览器后退按钮失效及HTML5-window.history
date: 2016-11-13 18:03:29
tags: window.history html5
---
### 防止页面后退（使浏览器后退按钮失效）

　　*原理*：用新页面的URL替换当前的历史纪录，这样浏览历史记录中就只有一个页面，后退按钮永远失效。

　注：history.go和history.back（包括用户按浏览器历史前进后退按钮）触发，页面由于使用pushState修改了history，会触发popstate事件。【代码如下】
```
       <script type="text/javascript">
            jQuery(document).ready(function ($) {
                if (window.history && window.history.pushState) {
    　　　　$(window).on('popstate', function () {
      　　    window.history.forward(1);
    　　　　});
  　　　　}
　　　　});
　　</script>
```

### 其中涉及到的方法详细介绍：

1. window.history：表示window对象的历史记录

2. 历史记录的前进和后退

    window. history.forward() --- 此方法加载历史列表中的下一个 URL，同浏览器中点击向前按钮；
    window. history.back() --- 此方法加载历史列表中的前一个 URL，同浏览器中点击后退按钮。
 - 可移动到指定历史记录点：
    通过指定一个相对于当前页面位置的数值，你可以使用 go() 方法从当前会话的历史记录中加载页面,（当前页面位置索引值为0，上一页就是-1，下一页为1）  如：
     要后退一页（相当于调用back()）： window.history.go(-1);
       　　　　
     向前移动一页（相当于调用forward()）：window.history.go(1);
        　　　　
　   window.history.length：可以查看length属性值，可知道历史记录栈中共有多少个记录点。

3. 操作历史记录点

    HTML5的新API扩展了window.history，可实现存储、替换当前历史记录点，以及监听历史记录点。

　　1、存储、替换当前历史记录点
        创建当前历史记录点pushState(state, title, url)：创建（添加）一个新的history实体，
                    state：状态对象，记录历史记录点的额外对象（要跳转的URL），可以为空；
                    title：页面标题，目前所有浏览器都不支持；
                    url：可选的url，浏览器不会检查url是否存在，只改变url，url必须同域。
                    window.history.pushState(json,”",”http://www.qingdou.me/post-1.html”);
        替换当前历史记录点replaceState()：修改当前的history实体，不会新增。
                    类似replace(url)，要更新当前历史记录的状态对象或URL时，使用replaceState()方法会更合适。
    
　　2 、监听历史记录点onpopstate()

　　　  当history实体被改变时，popstate事件将会发生； onhashchange()可监听URL的hash部分。
     
     3、读取现有state

        当页面加载时，它可能会有一个非空的state对象。当页面重新加载，页面将收到onload事件，但不会有popstate事件。
        然而，如果你读取history.state属性，将在popstate事件发生后得到这个state对象。

   注：window.history.forward();确实这个方法在IE和谷歌浏览器中还是起作用的，但在火狐浏览器中却不起作用了
    经过多次测试后发现加入window.onbeforeunload这个方法后在火狐中就起作用了。代码如下：
   ```
    <script language="JavaScript">  
          window.history.forward();  
          window.onbeforeunload=function (){   
                  
            }  //onbeforeunload方法中什么也不用写。
    </script>
   ```
#### 其他方法参考：

 1. 利用JS产生一个“前进”的动作，以抵消后退功能，这种方法应该是最简洁的，并且不需要考虑用户连点两次或多次“后退”的情况，缺点是当用户端禁用了JavaScript之后即失效。 
 ```
 <script language="JavaScript"> 
 javascript:window.history.forward(1); 
 </script> 
 ```
 2. 用location.replace从一个页面转到另一个页面。这种方法的原理是，用新页面的URL替换当前的历史纪录，这样浏览历史记录中就只有一个页面，后退按钮永远不会变为可用。我想这可能正是许多人所寻求的方法，但这种方法仍旧不是任何情况下的最好方法。这种方法的缺点在于：简单地运用Response.Redirect将不再有效，这是因为每次用户从一个页面转到另一个页面，我们都必须用客户端代码清除location.history。另外还要注意，这种方法清除的是最后一个访问历史记录，而不是全部的访问记录。 
 ```
  <A HREF="logout.do" onclick="javascript:location.replace(this.href); event.returnValue=false; "> 
  Logout (Back Disabled) 
  </A> 
  ```