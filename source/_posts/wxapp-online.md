---
title: 简易电商类小程序开发教程
date: 2017-02-06 16:07:17
tags: wxapp
---
电商类平台主要由首页、分类页、商品列表页、商品详情页、购物车、订单结算页和个人中心等几大模块组成。
在开发过程中，也会穿插介绍一些常用简单内置组件的使用，最再后利用小程序的 template 模板，对自定义组件化开发的实现提供三种参考模式。

#### 1、准备开工
先新建一个文件夹 wxapponline 作为项目目录，里面包含：
   1.common 目录——用来放置项目公用的文件
   2.pages 目录——项目页面开发目录，里面的每个子目录代表一 个 独 立 页 面 ， 分 别 包 含 与 目 录 同 名 的 .wxml （ 必须）， .wxss， .js（ 必须）和 .json 文件
   3.templates 目录——用来放置自定义的组件
   4.utils 目录——用来放置工具类 js 文件
   5.app.js——小程序逻辑
   6.app.json——小程序公共设置
   7.app.wxss——小程序公共样式表

#### 2、轮播banner样式定义
和大多数项目一样，最开始是首页的实现。
首 页 在 pages/index 目 录 下 ， 包 括 index.wxml 、index.wxss、 index.js 三个文件。

<font color="red">实现 banner 轮播图，来看代码：</font>
```
/*** index.wxml ***/
<swiper class="banner-swiper"
indicator-dots="{{ indicatorDots }}"
autoplay="{{ autoplay }}" interval="{{ interval }}"
duration="{{ duration }}">
<swiper-item class="swiper-item"
wx:for="{{ banners }}">
<navigator
url="../detail/detail?id={{ item.productId }}">
<image src="{{ item.image }}"
class="slide-image"></image>
</navigator>
</swiper-item>
</swiper>
```
值得注意的是， image 组件会有一个默认的样式.可以到官方文档中去查找images组件的默认样式。默认宽高可能在不同的版本中有不同的值。我们可以根据实际情况给它重新定义一个宽高值。单位：rpx;
友情提：大多数小程序内置组件都会有自己的默认样式，我们可以利用开发工具中的wxml panel，多了解一下这些组件的默认样式，可以避免很多由组件默认样式引起的问题。
另一个注意点是，不要将大图片放到项目目录中，因为小程序代码总大小不能超过 1 MB，而大图片会占用大量的空间，所以要尽量避免在项目目录中放置大图片。
如有需要，建议只在项目目录中放置少量 icon 类的小图片，其他大图片可以上传到自己的服务器或者网盘中，然后在 src 中设置图片的网络资源地址。
在手机测试或者正式上线之前，要注意是否有引用了本地大图片导致代码超出大小限制的问题。

#### 3.准备获取 banner	数据，并绑定到视图层
```
/*** index.js ***/
Page({
data: {
banners: [], //初始化一个 banners 数组
//以下四项设置 swiper 组件的参数值
indicatorDots: true
, autoplay: true, //开启自动切换
interval: 5000, //自动切换时间间隔
duration: 1000 //滑动动画时长}
});
```
在 Page 里的 data 会以 JSON 的形式由逻辑层传至视图层，然后在视图层中使用 Mustache 语法（双大括号）接收传入的数据，实现动态数据的数据绑定。经过绑定后的数据变动可以在视图中实时反应，也可以通过开发者工具中的 AppData Panel 中查看和编辑页面中的所有 data 数据。
友情提醒：当你不知道 data 数据是否被设置成功的时候，可以到 AppData Panel 来查看一下，免去了手动打 console.log 进行调试的麻烦。

实现动态数据绑定的另一个方式是使用 this.setData() 方法 ， this 在 这 里 代 表 的 是 Page ， 而 setData 是在PageX.prototype 中的方法，所以 this.setData() 实际上访问的是 Page.prototype.setData()。它可以用来新增和修改 this.data 里的值，同时还有一个重要的功能是将数据从逻辑层发送到视图层，从而实时改变页面的状态，保持逻辑层和视图层数据的一致性。需要注意的是，如果使用 this.data.property = value，同样可以设置 this.data 的值，但是它不会将数据发送到视图层，无法改变页面的状态。
注意： 一 定 要 避 免 使 用this.data.property = value 的方式设置数据。

#### 4.从服务器获取 banner数据
现在我们的目的是要得到一个 banners 数组，然后利 用 wx:for 将 banners 数 组 中 的 每 一 项 渲 染 到swiper-item 中。我们可以在页面加载（ onLoad） 时，使用 wx.request() 发
起一个 Ajax 请求，来获取到所需要的 banner 列表数据。先 来 看 一 下 wx.request(object) 的 使 用 方 法 。 首 先 是object 可以接受的参数值：
```
1. url：设置发送请求的地址。
2. method：请求方式，默认为 GET，有效值： OPTIONS， GET，HEAD， POST， PUT， DELETE， TRACE， CONNECT。注意，所有值都要使用大写。 经过测试，若使用小写，在安卓中会导致无法发起请求，而在开发者工具和 iOS 中则正常。
3. data：设置请求的参数。如果 method 值为 GET，这个函数会将 data 转换成 query 字符串，拼接在 url 后面，若 url 本身带有同名的 query 参数，那么 data 中的参数会覆盖 url 中的 query 参数；小 程 序 执 行 wx.request(object) 发 送 请 求 并 收 到success（成功）的消息后，会自动执行请求成功返回的回调函数。回调函数执行时，会自动收到类似这样的回调参数：
res = {data: '服务器返回的数据', errMsg: '错误信息', statusCode: HTTP 状态码}
```
特别注意，在开发者工具和 iOS 中， res.statusCode 的类据类型是一个数值， 而在 Android 中的数据类型是一个字符串，所以在判断 res.statusCode 时不能直接使用 === 操作符，而应该用 == 操作符，或者是先对 res.statusCode 进行数据类型转换。现在，就可以在 onLoad 中调用 wx.request() 发起请求，访问获取 banner 列表的服务器 API。
我们将各服务器 API 的访问地址都保存在 API_URL 对象，保存在 utils/config.js 中：
```
/*** config.js ***/
const CONFIG = {
API_URL: {
BANNER_QUERY: http://ifanr.com/api/banner
}
}
module.exports = CONFIG;
然 后 需 要 在 index.js 中 使 用 require 引 入utils/config.js 文件，引入路径为当前文件的相对路径：
/*** index.js ***/
var CONFIG = require('../../utils/config.js');
于是，我们就知道了在小程序中 js 模块化的方法，模块通过module.exports 对外暴露接口，在需要使用这些模块的文件中，使用 require(path) 将公共代码引入。发起 wx.request 请求：
/*** index.js ***/
var CONFIG = require('../../utils/config.js');
Page({
data: {...
},
onLoad: function () {
var _this = this;
wx.request({
url: CONFIG.API_URL.BANNER_QUERY,
method: 'GET',
data: {
limit: 6,
img_size: 'small'
},
success: function (res) {
if (res.statusCode == 200) {
_this.setData({
banners: res.data.objects
});
}
}
});
}
});
```
在 wx.request() 的回调函数中判断 res.statusCode 是否 为 200 ， 若 为 200 则 代 表 请 求 成 功 ， 接 着 将 数 据 保 存 到banners 数组中。我 们 在 success 回 调 函 数 中 使 用_this.setData({ banners: res.data.objects }); 设置banners 的值。
注意 ： 这 里 不 能 直 接 使 用this.setData() ，因为 success 回调函数是一个闭包，我们要先 在 闭 包 外 面 定 义 var _this = this; ， 再调用_this.setData()，以保证闭包内能到正确的 this 值。现在，在视图中我们就能看到完整的 banner 列表了。

## 如何做商品列表
#### 1.为布局而生的 Flex
传统的布局方式一般都是基于 div 盒子模型，利用 float，position， display 来进行布局。每个前端开发者对这些布局方式都非常熟悉。对一些特殊的布局来说，使用这些属性并不方便，由此还衍生出各种 hack 方案。直到CSS3 提出了专为布局而生的解决方案，就是 Flex 布局。但是，由于浏览器的兼容问题，大多数开发者都不敢将 Flex 布局直接应用到实际项目之中。在常规项目中，大量应用 Flex 布局还需要一个过程。

而对于 WeChat Only 的小程序来说，则完全不用担心有浏览器兼容的问题，它只要适配微信客户端即可。同时，由于小程序的布局不会非常复杂，所以，也不用过多地担心大量使用 Flex 引起的性能问题。我们可以放心地在小程序中使用 Flex 来处理布局。
#### 2.如何更方便地使用 rpx
rpx 的全称是 responsive pixel，它是小程序自己定义的一个尺寸单位，可以根据当前设备的屏幕宽度进行自适应。小程序中规定，所有的设备屏幕宽度都为 750rpx，根据设备屏
幕实际宽度的不同， 1rpx 所代表的实际像素值也不一样。

例如，在 iPhone 6 上，屏幕实际宽度为 375px，则 750rpx =375px， 1rpx = 0.5px；而在 iPhone 5 上，屏幕实际宽度为 320px，则 750rpx = 320px， 1rpx = 0.42px。
其实，我们并不必关心每种设备屏幕下 1rpx 到底代表多少个像素，只要抓住 「所有的设备屏幕宽度都为 750rpx」这个原则，就能很好地实现对任意设备屏幕大小的自适应布局。强烈建议用 iPhone 6 作为视觉稿的标准，即将视觉稿总宽度设成 750px。这样，开发者就能很方便地对相关的尺寸进行量取。比如，在总宽度为 750px 的 iPhone 6 视觉稿中，量取一个图片的宽度为 200px，那么，这个图片的宽度即可设置为 200rpx。

简单一句话解释：量取多少就设置多少。

#### 3.Flex配合rpx的使用案例
现在，我们就在小程序中使用 Flex 和 rpx 进行布局，体验一下这种解决方案的便利。
第一个例子：货架列表效果图：
<div align=center>
 ![](http://oi28tswuq.bkt.clouddn.com/images/wxapponline2.jpg)
</div>

首先还是贴两段代码，以下是 WXML 的实现：
```
/*** index.wxml ***/
<view class="shelf-nav">
<view class="shelf-nav-item"
wx:for="{{ shelfNavList }}">
<navigator
url="../list/list?id={{ item.id }}">
<image src="{{ item.cover_image }}"
class="shelf-nav-item__image"> </image>
<text>{{ item.name }}</text>
</navigator>
</view>
</view>
```
然后 WXSS 的代码如下，在关键代码处已经给出简要注释：
```
/*** index.wxss ***/
.shelf-nav {
display: flex; // 设 置 display: flex; 将 它 变 为flex 布局的元素
flex-wrap: wrap; //当子元素总宽度超过父元素宽度时换行显示
padding: 30rpx;
}
.shelf-nav-item {
width: 25%; //因为每行显示 4 个货架，所以宽度设置为25%;
margin-bottom: 20rpx;
text-align: center; //让它中间的图片和标题居中显示
}
.shelf-nav-item__image {
width: 130rpx; //在视觉稿中量取图片宽高为 130px
height: 130rpx; //于是设置 width 和 height 都设置为 130rpx;
border-radius: 50%; //把图片设置成圆形
border: 1px solid #d9d9d9; //加上外边框
}
```
简单几步就完成了货架列表的布局， 并且完美兼容各种大小的设备屏幕。

第二个例子：效果图如下：
<div align=center>
 ![](http://oi28tswuq.bkt.clouddn.com/images/wxapponline3.png-public)
</div>

如上图所示， 需要做到的效果是标题居左，「查看更多」（即...）的图标居右并且垂直居中。
代码结构如下：
```
/*** index.wxml ***/
<view class="shelf-header">
<view class="shelf-title">
<text
class="shelf-title-en">{{ shelf.english_name }}</
text>
<text
class="shelf-title-cn">{{ shelf.name }}</text>
</view>
<view class="shelf-more">
<navigator
url="../list/list?id={{ shelf.id }}"
class="ifanrin-more-icon ifanrin"> </navigator></view>
</view>
```
有经验的同学一眼就可以看出，要实现需求需要：
1/让 .shelf-title 向左浮动
2/让 .shelf-more 水平方向右对齐、垂直方向居中
3/最后还要给 .shelf-header clearfix 一下
那现在来看看 Flex 是怎么做的：
```
/*** index.wxss ***/
.shelf-header {
display: flex; //设置为 flex 布局的元素
justify-content: space-between; //均匀排列每个元
素，首、末两元素分别位于起点和终点
}
.shelf-more {
align-self: center; //设置居中
}
```
只要区区几行代码，就能轻松使用 Flex 调整排版。
接下来，发起一个请求获取数据，然后setData() 绑定数据：
```
/*** index.js ***/
Page(){
...,
onLoad: function () {
var that = this;
wx.request({
url: CONFIG.API_URL.SHELF_QUERY,
method: 'GET',
data: {
img_size: 'small'
},success: function (res) {
that.setData({
shelfNavList: res.data.objects
});
}
});
},
...
}
```
此时列表页就基本完成了。

#### 4、template模板的使用
效果图如下：

<div align=center>
 ![](http://oi28tswuq.bkt.clouddn.com/images/wxapponline1.jpg)
</div>

不难发现，这两个页面的列表的样式是一模一样的。那么，我们就可以把每个商品视为一个模块，暂且把这个商品模块称为 product-card，我们可以将它用 template 封装起来，
在需要的地方就将它引入并进行调用。这里先要弄清楚一个概念， template 的主要功能更多的是在于定义一个 WXML 代码片段，然后在不同的地方调用。template 拥有自己的作用域， 而且只能使用 data 传入的数据。接下来看看template 到底如何使用。

<font color="red">1.定义 template 模板</font>

为了方便代码组织，我们在 templates 目录下，新建一个productCard 文 件 夹 ， 并 在 product-card 文 件 夹 下 新 建productCard.wxml 和 productCard.wxss 文件。代码如下：
```
<template name="productCard">
<view class="product-card">
<navigator
url="../detail/detail?id={{ id }}">
<view style="background-image:
url({{ cover_image }})" class="product-cover">
</view>
<view>
<text
class="product-title">{{ title }}</text>
<text class="product-price">
￥{{ price }}</text>
</view>
</navigator>
</view>
</template>
```
使用 name 属性，定义模板的名字，然后将代码片断保存在template 中。

<font color="red">2.引入 template 模板</font>
以首页为例，当要使用到 productCard 模板时，我们只需要使用 import 引入模板。在需要显示的位置，外层用 wx:for 循环渲染列表， template为子项，使用 is 声明需要的使用的模板，用 data 传入数据：
```
/*** index.wxml ***/
<import
src="../../templates/productCard/productCard.wxml
" />
<view class="product-list">
<block wx:for="{{ productNewList }}" <template
is="productCard" data="{{ ...item }}" /> </block>
</view>
留意 data="{{ ...item }} 的写法， item 是 wx:for 中代表数组当前项的默认变量名，
 item 前面的 ... 操作符相当于ES6 中的展开运算符，可用于需要解构赋值的地方.通 过 解 构 ，
  template 中 就 可 以 直 接 写 成 {{ id }} ，{{ cover_image }} ， 而 不 用 写 {{ item.id }} ，
  {{ item.cover_image }}。它的意义在于实现了 template 与 wx:for-item 之间的解耦，
  比如这里设置了 wx:for-item="product"，我们只要设改变data="{{ ...product }}" 就可以了。
如果数据没有通过解构，就要将 template 的 {{ item.id }}修改成 {{ product.id }}，很不方便。
接着，是 productCard.wxss 的引入，先在 productCard写好样式，这里就不贴代码了
```
#### 5.模板的 WXSS文件如何引入
在哪里可以引入 WXSS 文件？一种方法是在用到 productCard 模板的页面里引入，在这里是在 list.wxss 中 import 进来。另一种方法是，直接在 app.wxss 中引入。相比较于上一种方法，这个方法只需要一次引入，而所有用到 productCard 模板的页面都不用再去引入 productCard.wxss 了。
```
/*** app.wxss ***/
@import "./templates/productCard/productCard.wxss";
```
获取商品列表数据，渲染视图先通过 onLoad 的 options 取得 id：
```
/*** list.js ***/
onLoad: function (options) {
var id = options.id;
this.setData({
shelfId: id
});
}
```
最 后 ， 调 用 wx:request() 获 取 商 品 列 表 数 据 ， 通 过setData() 设置 productNewList，即可在视图层渲染出完整的列表。
```
/*** list.js ***/
onLoad: function (options) {
...
wx.request({
url: CONFIG.API_URL.PRODUCT_LIST,
method: 'GET',
data: {
shelf__id: this.data.shelfId
},
success: function (res) {
if (res.status == 200) {
that.setData({
productNewList: res.data.objects
});
}
}
});
...
}
```
如上面的代码所示，现在小程序会一次性将所有商品列表查询并渲染至页面中。现实中，这种做法显然是不科学的。我们还需要一个「加载更多」的功能。这个功能要求我们做到：用户访问时，页面首次加载 20 个商品，点击列表底部的「查看更多」按钮可再多加载 10 个商品。

## 数据加载和图文排版
####  1.「查看更多」功能
效果图如下：

<div align=center>
 ![](http://oi28tswuq.bkt.clouddn.com/images/wxapponline4.png-public)
</div>

一、 调用 Toast 提示
通过调用 wx.showToast() ，可以实现上图中部的 Toast 提示（加载中）。微信提供的 Toast 组件有 success 和 loading 两种状态（旧版本的 loading 组件已经被废弃）。
```
/***list.js***/
wx.showToast({
title: '成功', //提示的文字信息icon: 'success', //图标，只支持"success"、 "loading"
duration: 2000, //提示的延迟时间(毫秒)，最大为 10000
success: function() {}, //调用成功的回调函数
fail: function() {}, //调用失败的回调函数
//接口调用结束的回调函数（调用成功、失败都会执行）
complete: function() {}
})
```
需要注意的是， 当 showToast 执行时，整个页面是不可点击的。所以，不用考虑用户意外触发多次「查看更多」的问题。调用 wx.hideToast() 即可隐藏 Toast 提示框。现 在 ， 我 们 来 思 考 一 个 问 题 。 上 面 说到 wx.showToast 的 duration 最大值为 10000 毫秒，也就意味着当请求时间大于 10 秒，若没有返回数据时， loading 提示框会自动消失。

当然这会带来一些问题：
1.「查看更多」按钮重新变回可点击状态，可能造成请求的重复提交。
2.加载提示消失与数据成功加载之间出现时间差。
如何解决这个问题呢？其实不难！！！
先给 loading 设置 10000 毫秒延迟时间，超过 10000 毫秒后，即使请求成功，也不保存任何数据。同时，将按钮文字改回「查看更多」即可。
我 们 初 始 化 一 个 timeoutFlag 的 对 象 ， 使 用 timer =setTimeout() 设 置 9800 毫 秒 延 时 。 9800 毫 秒 后 ，以 timer 为 key 设置一个对象 { isTimeout: true }，每次发起请求时都会有一个 timer 作为标识。

若请求成功时 timeoutFlag.timer 的值为 { isTimeout:true }，则不处理请求成功后的数据。为什么要设置 9800 毫秒而不是 10000 毫秒呢？
那是因为 setTimeout 的计时并不准确，我们给出 200 毫秒的提前量，以防止代码出错。下面就来封装一个 _setTimeoutFlag 方法：
```
/***list.js***/
_setTimeoutFlag: function () {
var that = this;
var timer = null;
timer = setTimeout(function () {
that.setData({
tipText: '查看更多'
});
timeoutFlag.timer = { isTimeout: true };
}, 9800);
timeoutFlag.timer = { isTimeout: false };
return timer;
}
```
二、 构建与实现 loadMore 函数
我们需要一个 loadMore 函数，来做到数据的分页查询。一般，都是通过设置两个查询参数来控制，分别是每次请求条数 limit 和每次请求起始数 offset（即从第几条获取数据）。以我们的需求为例：
1.第一次获取 20 条： limit=20 & offset=0
2.第二次再新增 10 条： limit=10 0& offset=20
3.第三次再新增 10 条： limit=10 & offset=30
当然，根据各自不同的分页实现， limit 和 offset 的命名也不相同，我们只要了解原理即可。

根据以上逻辑，当用户点击「查看更多」按钮后，小程序在不同情况下应该给出相应的反应：
1.按钮文字变为「正在加载...」，页面弹出「加载中...」的loading 提示框，设置 offset + 10 并发起请求；
2.有新数据加载时，按钮文字变回「查看更多」，关闭 loading提示框，保存当前 offset；
3.无新数据加载时，按钮文字变为「没有更多啦」，关闭loading 提示框。
4.请求失败时，按钮文字变为「查看更多」，关闭 loading 提示框

先在 list.wxml 中设置按钮，并绑定点击事件 loadMore;。
```
/***list.wxml***/
<view class="btn-load-more" bindtap="loadMore">
{{ tipText }}
</view>
```
根 据 上 面 的 流 程 ， 封 装 一下 _getProductList 和 loadMore 方法。
```
/***list.js***/
loadMore: function () {var that = this;
this.setData({
tipText: '正在加载...'
});
wx.showToast({
title: '加载中...',
icon: 'loading',
duration: 10000
});
var timer = that._setTimeoutFlag();
this._getMoreProductList(timer);
},
_getMoreProductList: function (timer) {
var that = this;
wx.request({
url: CONFIG.API_URL.PRODUCT_LIST +
this.data.orderBy,
method: 'GET',
data: {
limit: 10,
offset: this.data.offset + 10,
shelf__id: this.data.shelfId,
img_size: 'small'
},
success: function (data) {
if (!timeoutFlag.timer.isTimeout) {
if (data.data.objects.length > 1) {
var objects = that.data.productList
.concat(data.data.objects);
that.setData({
productList: objects,
offset: that.data.offset + 10,
tipText: '查看更多'});
} else {
that.setData({
tipText: '没有更多啦'
});
}
}
clearTimeout(timer);
wx.hideToast();
},
fail: function () {
that.setData({
tipText: '查看更多'
});
wx.hideToast();
}
});
}
```
至此，已经完整实现了商品列表页的开发，接下来进入商品详情页.

#### 2.商品详情页与图文排版实现
效果图如下：

<div align=center>
 ![](http://oi28tswuq.bkt.clouddn.com/images/wxapponline5.png)
</div>
目前商品详情的图文介绍，一般都是通过后台的富文本编辑器来排版创建的，数据都是以 HTML 代码的形式保存下来，然后在页面上直接渲染出来。但是对于小程序来说， HTML形式的数据不能直接拿来使用，因为小程序根本不能解析 HTML 代码。此时，我们需要处理由富文本编辑器所创建的数据，将其转换为JSON 格式的数据，以便于在小程序里使用。
现在有两种思路去解决这个问题：
1.由前端去解析 HTML 格式的数据，用正则匹配 HTML 标签，然后转换成 JSON 数据；
2.交由后端去解析，同时新增一个专用的 API，返回解析过的 JSON数据。
这里选用的是第二种方案，获取到的 JSON 数据格式如下:

```
{
"description": [
{
"content": "xxx.jpg",
"type": "image"
},
{
"content": "商品᧿述文字",
"type": "text"
}
]
}
```
我 们 只 要 将 获 取 的 数 据 用 wx:for 渲 染 出 来 ， 渲 染 时 判断 type 选择对应的 text 或 image 组件即可。
```
/***detail.wxml***/
<block wx:for="{{ description }}">
<view
class="description-img"
wx:if="{{ item.type == 'image' }}">
<image
class="description-img-content"
src="{{ item.content }}">
</image>
</view>
<view
class="description-text"
wx:elif="{{ item.type == 'text' }}">
<text class="description-text-content">
{{ item.content }}
</text>
</view>
</block>
```
仔细观察一下，可以发现，每一张图片的宽高都发生了不同程度的裁剪、拉伸或压缩。这是由于 image 组件的默认宽高导致的，这个问题在上面也说过，要特别留意小程序内置组件的默认样式，它们很可能会引起各种各样的问题。我们的期望是，图片能够按照原有的宽高比例进行显示，并且每一张图片的宽度都能撑满整个内容区域。这应该如何处理呢？这里就需要用到 image 组件的 load 事件，使用 bindload 属性将事件绑定在 image 组件中。我 们 可 以 用 event.detail = {height:' 图 片 高 度 px',width:'图片宽度 px'} 的方式调整图片宽高尺寸。
我们定义一个 imageLoad 函数，作为 image 组件的 load 事件的处理函数，用 data-index 保存当前 wx:for 的 index 值作当前图片的索引值。detail.wxml 改写 image 组件如下：
```
<image
class="description-img-content"
src="{{ item.content }}"
bindload="imageLoad"
data-index="{{ index }}">
</image>
detail.js 定义 imageLoad 函数如下：
data: {
images: {}
},
imageLoad: function (e) {
var realityWidth = e.detail.width; //获取图片实际宽度
var realityHeight = e.detail.height; //获取图片实际高度//获取图片的实际宽高比例
var proportion = realityWidth / realityHeight;//设置图片的渲染宽度， 750rpx - (2 * 40rpx) = 670rpx;
//父元素的左右 padding 都是 40rpx
var viewWidth = 670;//按比例计算出图片的渲染高度
var viewHeight = 670 / proportion;
var images = this.data.images;//将图片的 data-index 值作为 images 对象的 key
//用来储存该图片的计算后的渲染宽高
images[e.currentTarget.dataset.index] = {
width: viewWidth,
height: viewHeight
};
this.setData({
images: images
});
}
```
此 时 ，就 可 以 在 image 组 件 中 获 取到 images[index].width 和 images[index].height，并通过设置行内的 style 给每一个图片设置宽高了。
detail.wxml 改写 image 组件如下：
```
<image class="description-img-content"
src="{{ item.content }}" bindload="imageLoad"
data-index="{{ index }}"
style="width:{{ images[index].width }}rpx;
height:{{ images[index].height }}rpx">
</image>
```
此时在预览页面时，你会发现图片等就会正常显示了。

#### 内容总结提示
- Toast 提示：
1.Toast 提 示 可 以 使 用 wx.showToast() 来 调 用 ， 使用 wx.hideToast() 隐藏。l 开发者可以自定义 Toast 展示时间、展示文案等。
2.Toast 最长展示时间不能超过 10000 毫秒，开发者需要做好超时处理工作。

- 图文排版：
1.如果服务器中的数据是 HTML 数据，需要转换为 JSON，小程序才能解析。
2.直接将图片进行排版，图片宽高会出现问题。开发者需要处理好图片的宽高问题。
