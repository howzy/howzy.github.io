---
title: 浅析 Cookie 和 Web Storage 的区别
date: 2017-07-27 22:34:03
tags: [cookie, localStorage, sessionStorage]
---

随着 Web 应用的出现，也产生了对于能够直接在客户端上存储用户信息能力的要求。想法很合乎逻辑，属于某个特定用户的信息应该存储在该用户的机器上。将数据存储在本地，也能减少向服务器发起请求所带来的消耗。

主要的客户端本地存储的解决方案有以下三种

- cookie
- localStorage
- sessionStorage

后面两种属于 HTML5 的 Web Storage，在 HTML5 之前，cookie 是最主要的。本文主要讲解 cookie, localStorage, sessionStorage 三种本地存储方案的区别以及使用方法。

<!-- more -->

### 基本概念 ###

**1.Cookie**

HTTP cookie（也称为网络cookie，Internet cookie，浏览器cookie或简单cookie）是一个从网站发送的一小部分数据，并在用户浏览时由用户的浏览器存储在用户的计算机上。该标准要求服务器对任意 HTTP 请求发送 Set-Cookie HTTP 头作为响应的一部分。

**2.Web Storage**

Web存储（有时称为DOM存储（文档对象模型存储））提供用于在Web浏览器中存储数据的Web应用软件方法和协议。Web存储支持持久性数据存储，类似于Cookie，但容量大大增加，并且没有信息存储在HTTP请求头中。

Web Storage 有两种主要的存储类型，localStorage 本地存储以及 sessionStorage 会话存储。

### 区别 ###

**1.生命周期**

  cookie: 设置过期时间，到期时被清除；未设置过期时间，则浏览器关闭时被清除

  localStorage: 除非被清除，否则永久保存

  sessionStorag: 会话级别，关闭浏览器或页面时被清除

**2.限制**

  cookie: Firefox 限制 50 个，Opera 限制 30 个，IE7+ 限制 50 个，Safari 和 Chrome 没有硬性限制。一般浏览器限制 cookie 大小为 4kb 以内

  localStorage: Chrome 和 Safari 对每个源的限制在 2.5MB。而 iOS 版 Safari 和 Android 版 WebKit 的限制也是 2.5MB。

  sessionStorag: Chrome、Safari、iOS 版 Safari 和 Android 版 WebKit 限制 2.5MB。IE8+ 和 Opera 限制 5MB。

### 基本操作 ###

**1.Cookie**

> 由于 JavaScript 中读写 cookie 不是非常直观，常常需要写一些工具函数来简化 cookie 功能。

```
var CookieUtil = {
  // 读取
  get: function (name) {
    var cookieName = encodeURIComponent(name) + "=",
        cookieStart = document.cookie.indexOf(cookieName),
        cookieValue = null;

    if (cookieName > -1) {
      var cookieEnd = document.cookie.indexOf(";", cookieStart);
      if (cookieEnd == -1) {
        cookieEnd = document.cookie.length;
      }
      cookieValue = decodeURIComponent(document.cookie.substring(cookieStart + cookieName.length, cookieEnd));
    }

    return cookieValue;
  },

  // 写入
  set:function (name, value, expires, path, domain, secure) {
    var cookieText = encodeURIComponent(name) + "=" + encodeURIComponent(value);

    if (expires instanceof Date) {
      cookieText += "; expires=" + expires.toGMTString();
    }

    if (path) {
      cookieText += "; path=" + path;
    }

    if (domain) {
      cookieText += "; domain=" + domain;
    }

    if (secure) {
      cookieText += "; secure=" + secure;
    }

    document.cookie = cookieText;
  },

  // 删除
  unset: function (name, path, domain, secure) {
    this.set(name, "", new Date(0), path, domain, secure);
  }
};
```

**2.Web Storage**

> loclaStorage 和 sessionStorage 的操作方法一样，下面就以 localStorage 为例。

```
// 读取
localStorage.getItem("key"); // 根据指定名字获取对应的值

localStorage.key(index); // 获得 index 位置处的值的名字

// 写入
localStorage.setItem("key", "value"); // 如果存储对象，需要调用 JSON.stringify() 来序列化对象

// 删除
localStorage.removeItem("key"); // 删除由 name 设置的值

localStorage.clear(); // 清除所有值
```

### 总结 ###

对比了 Cookie 和 Web Storage 之间的异同，cookie 只能存储少量数据，它更适合于与服务器间的通讯存储，对于相对较大的数据，Web Storage 在性能上有更多的优势。至于 Web Storage 定义的两种存储数据的对象：sessionStorage 和 localStorage，前者严格用于在一个浏览器会话中存储数据，例如敏感的用户登录信息，需要在浏览器关闭后立即删除；后者用于跨会话持久化数据并遵循跨域安全策略。