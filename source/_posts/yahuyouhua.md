---
title: 雅虎网站性能优化的 14 条规则
date: 2016-07-04 14:52:55
tags: 性能优化
---
1. 尽可能减少 HTTP 请求数
2. 使用 CDN（内容分发网络）
3. 为文件头指定 Expires 或 Cache-Control，使内容具有缓存性
4. 使用 Gzip 压缩内容
5. 把 CSS 放到顶部
6. 把 JavaScript 放在底部
7. 避免在 CSS 中使用 Expressions
8. 把 JavaScript 和 CSS 都放到外部文件中
9. 减少 DNS 查找次数
10. 压缩 JavaScript 和 CSS
11. 避免重定向
12. 剔除重复的 JavaScript 和 CSS
13. 配置 Etags
14. 使 AJAX 缓存

#### 对上述规则的分析

一、 代码编写方面的规则
- 把 CSS 放到顶部
- 把 JavaScript 放在底部
- 把 JavaScript 和 CSS 都放到外部文件中
- 避免在 CSS 中使用 Expressions（css表达式）
- 使 AJAX 缓存

二、 打包方面的规则：
- 尽可能减少 HTTP 请求数
- 压缩 JavaScript 和 CSS
- 剔除重复的 JavaScript 和 CSS
 
三、 部署方面的规则：
- 使用 CDN（内容分发网络）
- 为文件头指定 Expires 或 Cache-Control，使内容具有缓存性
- 使用 Gzip 压缩内容
- 减少 DNS 查找次数
- 避免重定向
- 配置 Etags
 
#### 对规则的实践

- 部署方面的规则，应用 Nginx 为静态文件添加 Expires 跟 Cache-Control 头， 配置 Etags，并启用 Gzip 压缩。并且避免在 Nginx 中做重定向，有条件的话可以 启用 CDN，并优化网络配置以减少 DNS 查找次数。
- 代码编写方面的规则，需要在编写代码种形成规范。默认使用类似 jQuery 这样的库 便可以对 AJAX 进行缓存。
- 打包方面 webpack 可以合并压缩 JavaScript 与 CSS 文件, 小图片的合并（css雪碧图）或者字体图标或者用base64 用以减少 HTTP 请求数。