---
title: Angular2 中使用 TinyMCE 富文本编辑器
tags:
  - Angular
date: 2017-10-19 10:20:13
---


![ECharts 入门示例](/img/use_Tinymce_with_angular2_20171019.png)

<!--more-->

### 一、安装 TinyMCE

```
npm install --save tinymce
```

### 二、设置 TinyMCE 全局变量

TinyMCE 需要在全局范围内进行工作，通常可以使用像 Webpace 这样的模块加载工具，但是由于使用了 angular-cli.json（文件位于项目的根目录中）配置，这让实现变得相当简单。

只需要在 angular-cli.json 配置文件的 apps[0].scripts 中添加 TinyMCE 的路径，以及要使用的主题和任何插件的路径。例如，如果想要使用 modern 主题和链接、粘贴、表格等插件，只需要将相应的路径添加到配置文件中。

```
"scripts": [
  "../node_modules/tinymce/tinymce.js",
  "../node_modules/tinymce/themes/modern/theme.js",
  "../node_modules/tinymce/plugins/link/plugin.js",
  "../node_modules/tinymce/plugins/paste/plugin.js",
  "../node_modules/tinymce/plugins/table/plugin.js"
]
```

就这样，TinyMCE 便可以在项目的全局范围内使用了。

### 三、申明 TinyMCE

即使经过上面的设置，编译时仍然会报错: "cannot find name 'tinymce'"，所以必须在使用到 TinyMCE 的文件中直接申明，或者在 typings.d.ts（文件位于项目的根目录中）中申明。

```
declare var tinymce: any;
```

### 四、添加皮肤

皮肤文件中含有了文本编辑器中使用到的字体和样式，缺少这些 TinyMCE 将不能工作。最简单的实现方法就是从 node_modules 文件中直接复制到 src/assets 路径下。可以通过终端的命令实现复制，也可以直接手动复制文件。

Macos 和 Linux
```
cp -r node_modules/tinymce/skins src/assets/skins
```

Windows
```
xcopy /I /E node_modules/tinymce/skins src/assets/skins
```

然后，在初始化 TinyMCE 的时候，添加正确的路径就可以了。
```
tinymce.init({
  skin_url: '/assets/skins/lightgray'
});
```

> 注：本文翻译自 TinyMCE 的官方文档 [Angular2 Integration](https://www.tinymce.com/docs/integrations/angular2/)。