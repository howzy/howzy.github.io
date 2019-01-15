---
title: 在 Vue2.0 中使用 BetterScroll 的总结
tags:
  - Vue
  - BetterScroll
date: 2019-01-14 00:29:01
---


`BetterScroll` 是一款能解决移动端各种滚动场景需求的插件，平时用的也比较多，这篇文章主要是总结一下在使用过程中遇到的一些问题以及对应的解决方法。插件的具体用法，可以查看 [BetterScroll 官方文档](https://ustbhuangyi.github.io/better-scroll/doc/zh-hans/)。

### 一、滚动原理

浏览器的滚动条大家都会遇到，当页面内容的高度超过视口高度的时候，会出现纵向滚动条；当页面内容的宽度超过视口宽度的时候，会出现横向滚动条。也就是当我们的视口展示不下内容的时候，会通过滚动条的方式让用户滚动屏幕看到剩余的内容。同理，使用 BetterScroll 插件需要一个`高度固定`，且设置了`溢出隐藏`的父容器。当无法滚动时，很大几率是没有满足前面说的滚动原理，可以通过查看 BetterScroll 实例的 `hasVerticalScroll` (垂直滚动)、`hasHorizontalScroll` (水平滚动)属性的值是否为 true 来判断。

<!-- more -->

### 二、滚动条定位

有这么一个业务场景，浏览页表页时，点击其中的一条记录进入相应的详情页，然后在返回列表页，此时比较好的用户体验是定位到原先浏览到的位置。为了实现这个需求，就需要记录滚动条的位置。在 Vue 项目中，Vue-Router 路由库有个 `scrollBehavior` 方法可以实现这样的效果。它能记录页面的滚动条位置，并可以根据需要滚动到原先的位置。**但是有一点需要注意的是，这个功能只在支持 history.pushState 的浏览器中可用，并且它处理的是整个页面的滚动条位置，既 body 元素。**

上面说到 BetterScroll 的滚动原理，它需要一个固定高度的父容器，因此滚动的是容器内部，那么 body 元素也就没有滚动了，这会导致 scrollBehavior 不起作用。没关系，解决方法总是比问题多。

- 方案一：采用嵌套路由的形式，在上面模拟的场景中，列表页就是父路由，详情页就是子路由，通过局对定位的 z-index 层级来控制详情页的展示。当跳转到详情页时，列表页并不会销毁，那么返回到列表页时也就保留在原来的滚动位置了。
- 方案二：根据方案一的思路，页面缓存是一个关键点，因此也可以采用 Vue 内置组件 keep-alive 来实现。

>需要注意的是：在路由跳转时应采用 `编程式导航: router.push(...)` 而不要使用 `声明式导航: <router-link to="..."></router-link>`，因为声明式导航会在返回列表页时使页面滚动到顶部。

### 三、滚动条定位在 iOS 系统中的 bug

上面的方案二在 Android 端中表现的很正常，但是在 iOS 端却没有效果，页面依旧滚动到顶部。找了好久也没找到原因所在，但是总要解决这个问题吧，所以需要手动记录滚动位置，在下次返回该页面是，将页面滚动到记录的位置。大致思路如下代码所示。

```
beforeRouteEnter(to, from, next) {
  next(vm => {
    // 进入页面之前，若存在滚动位置，则初始化该位置
    if (to.meta.scrollTop) {
      vm._initPosition(to.meta.scrollTop)
    }
  })
},
beforeRouteLeave(to, from, next) {
  // 假设 BetterScroll 实例为 scroll
  if (this.scroll) {
    // absStartY 记录的是滚动的位置,当页面离开时将它保存在路由 meta 中
    from.meta.scrollTop = this.scroll.absStartY
  }
  next()
},
methods: {
  _initPosition(scrollTop) {
    // 刷新 scroll 实例，保证滚动效果正常
    this.scroll.refresh()
    // 滚动到记录的位置
    this.scroll.scrollTo(0, scrollTop)
  }
}
```

### 四、解决 iOS 系统滚动黏滞

BetterScroll 滚动的是内部区域，body 元素其实是没有滚动的。但是在 iOS 系统中，内部元素在滚动的时候整个页面也会跟着滚动，有黏滞的感觉，视觉上滚动就会很不流畅。为了解决这个问题，可以通过阻止 touchmove 的默认动作来屏蔽 body 元素的‘滚动’效果。可以在希望阻止 touchmove 的任意元素上添加 `@touchmove.prevent`，比如项目根元素。

```
<div id="app" @touchmove.prevent>...</div>
```

最后，本文只是提供了思路，具体代码可以参照 [scroll-demo](https://github.com/howzy/scroll-demo)。