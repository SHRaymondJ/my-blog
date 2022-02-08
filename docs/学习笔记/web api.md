---
title: web api
date: 2022-02-08 12:40:18
permalink: /pages/81668f/
categories:
  - 学习笔记
tags:
  - javascript
---
# JS Web API

## 1. DOM（Document Object Model）
本质是从 html 文件解析出来的一棵树
### dom 常用操作API
1. 节点操作: querySelectorAll, getElementById, getElementsByTagName等等
2. 结构操作: appendChild, createElement, removeChild, parentNode等等
### attribute 和 property 的区别
1. property: 修改对象属性，不会体现在 html 结构上
2. attribute: 修改 html 属性，会改变 html 结构
3. 两者都会引起 DOM 重新渲染
### 如何优化 DOM 操作
1. 减少 DOM 查询，给 DOM 查询做缓存
2. 一次性插入多个节点，避免多次插入（可以将节点都先插入到 createDocumentFragment()文档片段中）
## 2. BOM（Browser Object Model）
### 常用API
1. navigator (浏览器的信息)
    navigator.userAgent(ua, 浏览器的信息)
2. screen (屏幕的信息)
    screen.width/ screen.height
3. location (地址的信息)
    ```javascript
    // https://etravel.com.cn/login/loginPage.html?name=123&number=456#input
    location.href   // https://etravel.com.cn/login/loginPage.html?name=123&number=456#input
    location.protocol   // https:
    location.host   // etravel.com.cn
    location.pathname   // /login/loginPage.html
    location.search // ?name=123&number=456
    location.hash   // #input
    ```
4. history (前进后退的信息)
## 3. 事件绑定
1. 事件绑定
`addEventListener`
2. 事件冒泡
```javascript
event.preventDefault(); // 阻止默认事件
event.stopPropagation();    // 阻止冒泡 
```
3. 事件代理
- 概念：
如果要同时给很多元素都绑定相同的操作，可以在他们的父元素上绑定事件，同时判断点击目标target，进行操作（例如瀑布流）
- 优点：
    代码简洁
    减少浏览器内存占用

### 瀑布流加载更多，怎么进行事件绑定
- 事件代理，挂到父元素上
- e.target 获取目标元素
- e.target.matches 判断是否匹配元素
## 4. ajax
### XMLHttpRequest
ajax 基于 XMLHttpRequest 实现
### xhr.readyState
- 0-(未初始化) 还没调用 send() 方法
- 1-(载入) 已调用 send() 方法
- 2-(载入完成) send() 方法执行完成， 已经接收到全部响应
- 3-(交互) 正在解析响应内容
- 4-(完成) 相应内容解析完成，可以在客户端调用
### xhr.status 状态码
- 2xx - 表示成功处理请求，如200
- 3xx - 需要重定向，浏览器直接跳转，如301 302 304
- 4xx - 客户端请求错误， 如404 403
- 5xx - 服务器端错误
### 跨域：同源策略，跨域解决方案
- 什么是跨域？同源策略
    - ajax 请求时，**浏览器**要求网页和server必须同源（安全）
    - 同源：协议、域名、端口三者必须完全一致
    > 图片、css、js 无视同源策略
    > - `<img />` 可以统计打点，可以使用第三方统计服务
    > - `<link /><script/>` 可以引用CDN，CDN通常都是外域
    > - `<script />` 可以实现JSONP
    - 所有跨域都必须经过 server 端允许和配合
    - 没有经过 server 端允许就实现的跨域，说明浏览器有漏洞，有风险
- JSONP
本质上就是利用js可以跨域的特性，get请求获得API
- CORS（服务端支持）
服务器端的设置，NodeJS或者C#之类的后端语言可以设置
- ajax 常用插件
1. JQuery ajax  基于 XMLHttpRequest
2. fetch    兼容性不是100%，返回的是 Promise
3. axios    基于 XMLHttpRequest，返回的是 Promise
## 5. 存储
cookie、localStorage 和 sessionStorage 的区别
### cookie
- cookie 最初是用来浏览器向server进行传值的，会随着 http 请求发送出去
- 只是“借用”来做本地存储
- 最大存储  4kb
- 只能用 document.cookie = '...' 来修改，太简陋

### localStorage 和 sessionStorage 
- H5 专门为了存储设计的，可以存储 5MB
- API 简单易用，setItem, getItem
- 不会随着 http 请求发送出去
- localStorage 永久存在，除非代码或者人为清除
- sessionStorage 跟着会话结束，浏览器关了自动清空
- 一般用 localStorage 多一些
