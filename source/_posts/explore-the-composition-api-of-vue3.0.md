---
title: 初探 Vue 3.0 之 Composition API
tags:
  - Vue
date: 2019-12-31 10:30:11
---


Vue 灵活和轻量的特性使它对于想要迅速搭建中小型应用程序的开发人员来说非常适合。但是 Vue 当前的 API 在维护不断增长的应用程序方面有一定的局限性。这是因为组件实例是通过组件选项（data、methods、lifecycle等）配置的，而不是通过逻辑组织代码。
随着更多的组件选项的添加以及代码库的扩大，开发人员可能会发现自己正在与其他团队成员创建的组件进行交互，而这些组件的改动有时会出现一些令人困惑的问题。
幸运的是，Vue 3.0 通过推出 Composition API 解决了这一问题。我理解为这是一个基于函数的逻辑复用机制，旨在促进组件变得庞大是更易于维护。本文将探究 Composition API 如何改善我们编写代码的方式。

<!-- more -->

假设现在有一个 TodoList 的应用，代码如下。

![TodoList demo](/img/explore-the-composition-api-of-vue3.0_20191231-1.png)

这个 TodoList 应用具有添加任务、完成任务、取消完成任务和删除任务的功能，使用了四个组件选项 components、data、computed 和 methods。将来我们可能还会向这个组件继续添加新功能，也可能其他页面需要复用 TodoList 的功能。Vue 2.0 的传统解决方案是将这些复用的逻辑抽离成 Mixins，然而在大型项目中 Mixins 的缺陷非常明显，例如 `命名空间冲突`、`数据来源不清晰`等。除此之外，Vue 社区借鉴了 React 的 HOC 理念实现了基于 Vue 的高阶组件方案，但是其实现繁杂，且额外增加了组件实例的性能开销，最终并没有被官方推荐。

Vue 3.0 带来了解决方案：Composition API，具体用法请参考[官方文档](https://vue-composition-api-rfc.netlify.com/)。下面我们使用 Composition API 进行重构。

##### 1. 首先安装 Composition API

```
yarn add @vue/composition-api
```

![TodoList demo](/img/explore-the-composition-api-of-vue3.0_20191231-2.png)

##### 2. 然后在 main.js 中使用

![TodoList demo](/img/explore-the-composition-api-of-vue3.0_20191231-3.png)

##### 3. 对可复用逻辑进行重构抽离

![TodoList demo](/img/explore-the-composition-api-of-vue3.0_20191231-3.png)

##### 4. 最终在需要的地方引入复用逻辑

![TodoList demo](/img/explore-the-composition-api-of-vue3.0_20191231-4.png)

##### 总结

上面这个TodoList 非常简单，可能对与逻辑复用这点体现的不是很充分，但是其提供的函数式逻辑组合复用的理念是值得回味以及更深入的探究的。
