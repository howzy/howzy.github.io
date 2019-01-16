---
title: 在 SPA 单页面应用中配置微信 JS-SDK
tags:
  - Vue
  - 微信
date: 2018-10-14 23:39:01
---


**使用微信 JS-SDK 步骤：**

- 绑定域名：进入微信公众平台“公众号设置”的“功能设置”里填写“JS接口安全域名”

- 引入 JS 文件

- 通过 `config` 接口注入权限验证配置：所有需要使用 JS-SDK的页面必须先注入配置信息。同一个 url 仅需调用一次，对于变化 url 的 SPA ，可在每次 url 变化时进行调用，Android 6.2版本之前，微信客户端不支持 pushState 的 H5 新特性，所以使用 pushState 来实现路由的会导致签名失败。

- 通过 `ready` 接口处理成功验证

- 通过 `error` 接口处理失败验证

<!-- more -->

### 微信 config

本文主要总结一下 SPA 采用 history 路由模式时的配置以及常见的坑点。

history 模式利用的是 H5 的 `pushState`，它能更新 url 却不刷新页面，这是个很不错的特性。但是在 IOS 和 Android 中这一新特性存在不同的表现形式，在 IOS 中记录的始终是首次进入应用的路径。如下表格：

| 设备 | 进入链接 | 跳转链接 | 浏览器认为的url |
|-----|:-------:|:------:|:---:|
| Android | http://xxx/pageA | http://xxx/pageB | http://xxx/pageB |
| IOS | http://xxx/pageA | http://xxx/pageB | http://xxx/pageA |

因此，Android 端的路径每次都在变化，而 IOS 端只记录第一次进入应用的路径，当重新刷新页面时浏览器认为的路径才会被更新。那么微信配置的 URL 签名就需要针对这两个系统分别实现。大致的思路是在路由导航守卫中设置签名所需的URL。

```
// vuex 中存储首次进入应用的 url
SET_WX_JS_URL(state, url) {
  state.wx_js_url = url
}

// 路由导航守卫
router.afterEach(to => {
  if (!store.state.wx_js_url) {
    store.commit('SET_WX_JS_URL', document.URL)
  }
  let _url = window.location.href
  // 非 ios 设备
  if　(!window.__wxjs_is_wkwebview) {
    _url = window.location.origin + to.fullPath
  }
  // ios 设备
  if (window.__wxjs_is_wkwebview) {
    _url = store.state.wx_js_url
  }
  _url = encodeURIComponent(_url)

  // 根据　_url　向后端获取微信配置信息　data
  ...
  wx.config({
    debug: false,
    appId: data.appId, // 必填，公众号的唯一标识
    timestamp: data.timestamp, // 必填，生成签名的时间戳
    nonceStr: data.noncestr, // 必填，生成签名的随机串
    signature: data.signature, // 必填，签名
    jsApiList: jsApiList // 必填，需要使用的JS接口列表
  })
})
```

> `__wxjs_is_wkwebview`　是微信JS挂载在 window 对象下的一个属性，微信 iOS 客户端采用 WKWebview 内核。这一属性可以用来区分 iOS 设备和 Android 设备。

### 总结

至此，微信config 算是完成了，但还有一点需要注意的是，虽然微信配置签名的URL在 iOS 设备和 Android 设备中不同，但是微信分享的URL还是以最终要分享的路径来完成 wx.share。