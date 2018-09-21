---
title: 在 Vue 组件中加载全局 Sass 变量
date: 2018-09-21 16:53:11
tags:
---


Sass 是一门成熟、稳定和强大的专业级 CSS 扩展语言，它的一些强大的功能比如变量、混合指令等能提高效率。定义这些全局的配置样式 `setting.scss`，然后在组件中引入便可以试用了。

```
// setting.scss
$mainColor: red;

===================

// component.vue
@import 'path-to-your-setting.scss';
.someclass {
  color: $mainColor;
}
```

但是每新建一个组件，需要使用全局变量的时候就需要引入 `setting.scss` 文件，而且不用组件可能引入的路径还不一样，当页面很多的时候还是感觉很麻烦的。
下面针对 `Vue CLI 2.0 和 3.0` 进行配置，以达到不用在每个组件中引入 `setting.scss` 文件便可使用其中的变量或指令等。

<!-- more -->

> 上述的 scss 文件并不包含任何的 css，仅仅只是 `$variables` 和 `mixins` 等，添加到全局，编译之后并不会重复出现在每个组件中，因此性能和文件大小不会受到改变。

**Vue CLI 2.0**

```bash
yarn add -D sass-resources-loader
```

```
// build/utils.js (LINE: 63)
scss: generateLoaders('sass').concat({
  loader: 'sass-resources-loader',
  options: {
    resources: path.resolve(__dirname, 'path-to-your-setting.scss')
  }
})
```

**Vue CLI 3.0**

```
// vue.config.js
module.exports = {
  css: {
    loaderOptions: {
      css: {
        // 传递配置选项到 sass-loader
        sass: {
          data: `@import "path-to-your-setting.scss";`
        }
      }
    }
  }
}
```
