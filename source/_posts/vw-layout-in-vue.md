---
title: 在 Vue 项目中使用 vw 布局
tags:
  - Vue
date: 2018-07-01 23:13:17
---


在工作中移动端适配布局方案最常用的是手淘的 flexible，通过 Hack 手段来根据设备的`dpr`值相应改变`<meta>`标签中 viewport 的值，模拟`vw`的特性。到今天为之，`vw`已经得到大部分浏览器的支持了，在各个浏览器中的兼容性见下图。从数据上看还是非常不错的，正好最近有个新项目可以试一试 vw 方案。

![vw 支持率](/img/vw_layout_in_vue_20180701.png)

<!-- more -->

### 准备工作

先用 `vue-cli` 新建一个项目，采用的 webpack 模板，创建好之后在项目的根目录下有一个`.postcssrc.js`文件，默认情况下已经有了：

```
module.exports = {
    "plugins": {
        "postcss-import": {},
        "postcss-url": {},
        "autoprefixer": {}
    }
}
```

`postcss-import` 主要功能是解决@import 引入路径问题。使用这个插件，可以让你很轻易的使用本地文件、node_modules 或者 web_modules 的文件。这个插件配合 postcss-url 让你引入文件变得更轻松。
`autoprefixer` 是用来自动处理浏览器前缀的一个插件。

### 安装插件

处理项目模板自带的插件之外，还需要引入其他的插件

```
yarn add postcss-px-to-viewport postcss-viewport-units cssnano cssnano-preset-advanced
```

安装成功后，在`.postcssrc.js`文件对新安装的 PostCSS 插件进行配置：

```
module.exports = {
    "plugins": {
        "postcss-import": {},
        "postcss-url": {},
        "autoprefixer": {},
        "postcss-px-to-viewport": {
            viewportWidth: 750,     // (Number) The width of the viewport.
            viewportHeight: 1334,    // (Number) The height of the viewport.
            unitPrecision: 3,       // (Number) The decimal numbers to allow the REM units to grow to.
            viewportUnit: 'vw',     // (String) Expected units.
            selectorBlackList: ['.ignore', '.hairlines'],  // (Array) The selectors to ignore and leave as px.
            minPixelValue: 1,       // (Number) Set the minimum pixel value to replace.
            mediaQuery: false       // (Boolean) Allow px to be converted in media queries.
        },
        "postcss-viewport-units":{},
        "cssnano": {
            preset: "advanced",
            autoprefixer: false,
            "postcss-zindex": false
        }
    }
}
```

`cssnano`主要用来压缩和清理 CSS 代码。在 Webpack 中，cssnano 和 css-loader 捆绑在一起，所以不需要自己加载它。不过你也可以使用 postcss-loader 显式的使用 cssnano。
`postcss-px-to-viewport`插件主要用来把 px 单位转换为 vw、vh、vmin 或者 vmax 这样的视窗单位，也是 vw 适配方案的核心插件之一。
`postcss-viewport-units`插件主要是给 CSS 的属性添加 content 的属性，配合 viewport-units-buggyfill 库给 vw、vh、vmin 和 vmax 做适配的操作。但是这会带来一些副作用，例如 img 元素中的 content 会使图片在部分浏览器下不显示，这时候需要全局添加：

```
img {
    content: normal !important;
}
```
