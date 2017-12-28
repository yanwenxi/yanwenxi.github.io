---
title: 跨平台APP的构建方式
date: 2016-12-13 13:17:45
tags: app,跨平台
---
　　“一次构建，多处运行”，跨平台APP带着这股风潮把火烧到了前端，为开发者带来无尽的遐想。现有的流行跨平台框架有以下:

### 1. 基于单WebView的开发框架。
　　开发者可以使用现有的最新web技术，开发出单页面web应用。同时利用JSBridge，又能获取原生的API，从而使web应用具有了原生应用的功能。Cordova+IONIC可以说是这个潮流的代表，也是跨平台APP的先锋。然而这类跨平台应用的缺点是不流畅，在安卓手机上体验较差。

### 2. Hybrid方向。
　　也就是原生应用配合HTML5技术，让APP具有了部分跨平台的功能。Hybrid也是现在各大互联网企业采用较多的跨平台开发方式。这类APP在体验上优于单WebView的APP，并且能够极大提高开发效率。然而这种方式离“一次构建，多处运行”的设想还是有很大距离，毕竟依然需要针对不同的移动平台进行原生开发。

### 3. 使用Javascript开发纯原生应用。
　　既然单WebView性能有缺憾，Hybrid技术栈又比较高，那么Javascript开发纯原生应用就孕育而生。这个方向的代表有[ReactNative](https://facebook.github.io/react-native/), [Weex](http://alibaba.github.io/weex/)和[Nativescript](https://www.nativescript.org/)。

　　和ReactNative相比，Nativescript最大的特点是可以获得100%的原生API。也就是说，开发者可以通过Javascript获取和原生开发语言同样多的原生接口。通过例子可以体会到“获取所有原生API”带来的巨大好处。NativeScript是一款使用JavaScript语言来构建跨平台原生移动应用的开源框架，支持iOS、Android和Windows Phone。
　　使用Nativescript和Angular2可以很高效地构建跨平台APP！！！
    NativeScript一个缺点：无法与原生项目融合，即你只能纯写个NativeScript的应用，不可能把它抽离出来作为某原生应用的一部分来出现。

