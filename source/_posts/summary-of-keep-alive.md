---
title: 组件缓存之 keep-alive 实践总结
tags:
  - Vue
date: 2018-04-16 15:45:26
---


keep-alive: vue 的一个抽象组件，用它来包裹动态组件时，会缓存不活动的组件实例，而不是销毁它们。
这是官方文档给出的定义，从这句话中我们不难知道 keep-alive 这一特性对页面渲染有一定的性能优化。本文将从`生命周期`、`基本用法`、`应用场景`三个方面介绍 keep-alive。

<!-- more -->

### 一、生命周期

利用 keep-alive 缓存的组件，其自身的生命周期会有一些变动:

- 新增:
  activated: 组件被激活之后调用，发生在 updated 生命周期之后
  deactivated: 组件停用时调用
- 不触发: beforeDestroy、destroyed

> a.在 2.2.0 及其更高版本中，`activated` 和 `deactivated` 将会在 `<keep-alive>` 树内的所有嵌套组件中触发。
> b.因为组件被缓存了，不会被销毁，所以不触发 `beforeDestroy`、`destroyed` 也就很好理解了。

### 二、基本用法

```
<keep-alive>
  <!-- 组件将被缓存 -->
  <component :is="view"></component>
<keep-alive>

<keep-alive>
  <!-- 根据条件判断选择缓存组件 -->
  <comp-a v-if="a > 1"></comp-a>
  <comp-b v-else></comp-b>
</keep-alive>
```

> `<keep-alive>` 是用在其一个直属的子组件被开关的情形。如果你在其中有 `v-for` 则不会工作。

---

2.1.0 新增 include 和 exclude 用于指定或排除组件缓存，以 include 为例，exclude 同理。

```
// 仅组件名为 a 和 b 的组件将被缓存

<!-- 逗号分隔字符串 -->
<keep-alive includ="a, b">
  <component :is="view"></component>
</keep-alive>

<!-- 正则表达式 (使用 `v-bind`) -->
<keep-alive :include="/a|b/">
  <component :is="view"></component>
</keep-alive>

<!-- 数组 (使用 `v-bind`) -->
<keep-alive :include="['a', 'b']">
  <component :is="view"></component>
</keep-alive>
```

### 三、应用场景

在单页面应用中结合 vue-router 实现部分页面组件缓存就是一个很常见的应用场景，`keep-alive` 能通过 `v-if` 条件判断或 `include`、`exclude` 选择性的缓存组件，后者需要指定组件名称，因此 `v-if` 更为常用。

```
<keep-alive>
  <router-view v-if="$route.meta.keepAlive">
    <!-- 需要缓存的视图组件 -->
  </router-view>
</keep-alive>
<router-view v-if="!$route.meta.keepAlive">
  <!-- 不需要缓存的视图组件 -->
</router-view>
```

---

路由配置

```
routes: [
  {
    path: '/home',
    component: Home,
    meta: {
      keepAlive: true
    }
  },
  {
    path: '/user/:id',
    component: User,
    meta: {
      keepAlive: false
    }
  }
]
```

> 掌握了上面这个例子的思路，那么就可以结合 `路由守卫` 以及 `路由原数据 meta` 做一些有趣的事情了

```
beforeRouteLeave(to, from, next) {
  <!-- 当前路由跳转到其他路由时，可以自定义目标路由的组件是否需要缓存， It's awesome!! -->
  to.meta.keepAlive = true
}
```

### 参考
- [vue#keep-alive](https://cn.vuejs.org/v2/api/#keep-alive)
- [vue-router 之 keep-alive](https://www.jianshu.com/p/0b0222954483)