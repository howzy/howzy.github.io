---
layout: drafts
title: 在 VSCode 中采用 Prettier 格式化 Vue 项目
tags:
  - Vue
date: 2018-03-19 01:32:08
---

良好的代码风格有助于减少不必要的 bug 并且能有效的提高团队协作。在一个团队中，每个人的书写习惯都会有或多或少的差异，因此借助工具来达成统一规范是很有必要的。

接下来就是我们的主角登场了 prettier - 前端代码格式化神器。写 vue 项目的时候常用的插件就是 vetur 了，在 2017-10-19 发布的 0.10.0 版本中它首次采用了 prettier 来格式化代码。

<!-- more -->

### 一、安装并配置
在扩展商店中搜索并安装 [Prettier - Code formatter](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)

然后在 首选项 -> 设置 中添加如下配置，然后就可以利用 prettier 来格式化代码了。

```
"prettier.semi": false, // 禁用结尾分号
"prettier.singleQuote": true, // 采用单引号
```

### 二、引出问题
上述步骤格式化代码后，会发现 html 没有格式化，这是为什么呢？

通过翻阅 vetur 文档，了解到之前格式化 html 的 js-beautify 将被弃用，目前它默认是处于关闭状态，一旦 prettier 支持 html 的格式化，那么 vetur 将完全的移除 js-beautify。因此在现阶段，为了格式化 html，我们可以手动的开启 js-beautify。

```
"vetur.format.defaultFormatter.html": "js-beautify-html", // 格式化 vue 项目中的 html 代码
```

代码是实现了格式化，下一步我们来检验是否符合 eslint 的规则，在 packag.json 文件的 scripts 中添加脚本命令

```
"lint": "eslint --ext .js,.vue src"
```

npm run lint 进行检测，出现如下报错

```
Missing space before function parentheses  space-before-function-paren
```

> 函数名和括号之间的留空问题任处于争论之中，业界并没有得出一致的结论。eslint 和 prettier 对此的处理不同，因此在选择了 prettier 作为代码格式化工具时需要解决这两者的冲突，我采取的做法是引入 `eslint-config-prettier`（它能关闭所有不需要的或者可能与 prettier 冲突的规则）

```
yarn add eslint-config-prettier -D

// 配置 .eslintrc.js
extends: ['prettier']
```