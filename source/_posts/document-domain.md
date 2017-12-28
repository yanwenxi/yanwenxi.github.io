---
title: js设置document.domain实现跨域的相关注意事项
date: 2016-12-13 09:54:28
tags: js、跨域、document.domain
---
　　对于主域相同而子域不同的例子，可以通过设置document.domain的办法来解决。具体的做法是可以在http://www.a.com/a.html和http://script.a.com/b.html两个文件中分别加上document.domain = ‘a.com’；
　　本文实例分析了js设置document.domain实现跨域的注意点,仅供参考，具体分析如下：
```
　　document.domain
```
　　用来得到当前网页的域名。
　　比如在控制台输入：
　　代码如下:
　　```
    console.log(document.domain);//www.webywx.com
    ```
　　我们也可以给document.domain属性赋值，不过是有限制的，你只能赋成当前的域名或者基础域名。
　　例如：代码如下:
```
    document.domain="webywx.com";
    document.domain="www.webywx.com";
```
　　上面的赋值都是成功的，因为www.webywx.com是当前的域名，而webywx.com是基础域名。
　　但是下面的赋值就会出来"参数无效"的错误：
　　```
　　document.domain="yanwenxi.site"
    document.domain="www.yanwenxi.site"
    ```
　　因为yanwenxi.site与www.yanwenxi.site不是当前的域名也不是当前域名的基础域名，所以会有参数错误出现。
　　这是为了防止有人恶意修改document.domain来实现跨域偷取数据。
### 利用document.domain 实现跨域：
　　*前提条件*：这两个域名必须属于同一个基础域名!而且所用的协议，端口都要一致，否则无法利用document.domain进行跨域
　　Javascript出于对安全性的考虑，而禁止两个或者多个不同域的页面进行互相操作。
　　相同域的页面在相互操作的时候不会有任何问题。
　　比如在：aaa.com的一个网页(a.html)里面 利用iframe引入了一个bbb.com里的一个网页(b.html)。
　　这时在a.html里面可以看到b.html里的内容，但是却不能利用javascript来操作它。因为这两个页面属于不同的域，在操作之前，js会检测两个页面的域是否相等，如果相等，就允许其操作，如果不相等，就会拒绝操作。
　　这里不可能把a.html与b.html利用JS改成同一个域的。因为它们的基础域名不相等。(强制用JS将它们改成相等的域的话会报跟上面一样的"参数无效错误。")
　　所以如果在a.html里引入aaa.com里的另一个网页，是不会有这个问题的，因为域相等。
　　有另一种情况，两个子域名：
```
　　aaa.xxx.com
　　bbb.xxx.com
```
　　aaa里的一个网页(a.html)引入了bbb 里的一个网页(b.html)，
　　这时a.html里同样是不能操作b.html里面的内容的。
　　因为document.domain不一样，一个是aaa.xxx.com，另一个是bbb.xxx.com。
　　这时我们就可以通过Javascript，将两个页面的domain改成一样的，
　　需要在a.html里与b.html里都加入：
　　```
　　document.domain = "xxx.com";
    ```
　　这样这两个页面就可以互相操作了。也就是实现了同一基础域名之间的"跨域"。
　　备注：这种方式适用于{www.webywx.com, webywx.com, script.webywx.com, css.webywx.com}中的任何页面相互通信。某一页面的domain默认等于window.location.hostname。主域名是不带www的域名，例如webywx.com，主域名前面带前缀的通常都为二级域名或多级域名，例如www.a.com其实是二级域名.