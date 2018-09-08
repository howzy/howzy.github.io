---
title: 'Vue 项目中 -webkit-box-orient:vertical 打包后丢失的解决方法'
date: 2018-09-08 12:21:20
tags:
---


由于布局长度要求，当文本溢出时需要用省略号代替多余内容，根据情况不同，会有单行文本溢出和多行文本溢出。

**单行**
```
.ellipsis {
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}
```

**多行**
```
.ellipsis2 {
  overflow: hidden;
  text-overflow: ellipsis;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
}
```
> 多行文本溢出只有 webkit 内核的浏览器支持，移动端浏览器绝大部分是 webkit 内核。

<!-- more -->

使用 `vue-cli` 构建的项目，在开发环境中运行都是没问题的，但是打包之后会发现 `-webkit-box-orient: vertical` 这一样式丢失。查找之后发现是 vue-cli 集成的一个 css 压缩插件 `optimize-css-assets-webpack-plugin` 惹的祸。

### 解决方法一

package.json 文件中的 browserlist 字段表示的是浏览器环境， postcss、bable 等插件会基于这些环境做一些兼容性处理。
```
// package.json 文件

"browserslist": [
  "> 1%",
  "last 2 versions",
  "not ie <= 8",
  "iOS >= 6",
  "Android > 4.1",
  "Firefox > 20"
]
```
> 这样 postcss 在处理的时候就会自动加上兼容对应浏览器版本的一些属性, `-webkit-box-orient: vertical` 属性就不会丢失了。

### 解决方法二

`optimize-css-assets-webpack-plugin` 插件能压缩 css 代码，但是它存在的 bug 让我们决定采用其他方式来替代它的压缩功能。
```
// build/webpack.prod.conf.js 文件
// 注释如下代码
const OptimizeCSSPlugin = require('optimize-css-assets-webpack-plugin')

new OptimizeCSSPlugin({
  cssProcessorOptions: config.build.productionSourceMap
    ? { safe: true, map: { inline: false } }
    : { safe: true }
})

===============

// 在 build/utils.js 里加入 minimize: true，压缩css
const cssLoader = {
  loader: 'css-loader',
  options: {
    sourceMap: options.sourceMap,
    minimize: true
  }
}
```