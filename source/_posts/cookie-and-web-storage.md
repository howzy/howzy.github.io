---
title: Web 数据存储
date: 2017-07-27 22:34:03
tags: [cookie, localStorage, sessionStorage]
---

随着 Web 应用的出现，也产生了对于能够直接在客户端上存储用户信息能力的要求。想法很合乎逻辑，属于某个特定用户的信息应该存储在该用户的机器上。将数据存储在本地，也能减少向服务器发起请求所带来的消耗

主要的客户端本地存储的解决方案有以下三种

- cookie
- localStorage
- sessionStorage

后面两种属于 HTML5 的 Web Storage，在 HTML5 之前，cookie 是最主要的。本文主要讲解 cookie, localStorage, sessionStorage 三种本地存储方案的区别以及使用方法。

<!-- more -->

### 基本概念 ###

#### 1. Cookie ####

HTTP cookie（也称为网络cookie，Internet cookie，浏览器cookie或简单cookie）是一个从网站发送的一小部分数据，并在用户浏览时由用户的浏览器存储在用户的计算机上。该标准要求服务器对任意 HTTP 请求发送 Set-Cookie HTTP 头作为响应的一部分。

#### 2. Web Storage ####

Web存储（有时称为DOM存储（文档对象模型存储））提供用于在Web浏览器中存储数据的Web应用软件方法和协议。Web存储支持持久性数据存储，类似于Cookie，但容量大大增加，并且没有信息存储在HTTP请求头中。

Web Storage 有两种主要的存储类型，localStorage 本地存储以及 sessionStorage 会话存储。

### 区别 ###

- 生命周期

  cookie: 设置过期时间，到期时被清除；未设置过期时间，则浏览器关闭时被清除

  localStorage: 除非被清除，否则永久保存

  sessionStorag: 会话级别，关闭浏览器或页面时被清除

- 限制

  cookie: Firefox 限制 50 个，Opera 限制 30 个，IE7+ 限制 50 个，Safari 和 Chrome 没有硬性限制。一般浏览器限制 cookie 大小为 4kb 以内

  localStorage: Chrome 和 Safari 对每个源的限制在 2.5MB。而 iOS 版 Safari 和 Android 版 WebKit 的限制也是 2.5MB。

  sessionStorag: Chrome、Safari、iOS 版 Safari 和 Android 版 WebKit 限制 2.5MB。IE8+ 和 Opera 限制 5MB。
