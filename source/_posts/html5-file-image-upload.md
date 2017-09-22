---
title: 基于HTML5的多图上传预览
tags:
  - 工具函数
  - 原生js
date: 2017-09-22 22:24:24
---


HTML5 带来了许多新的特性以及便利，图片上传预览用到了 HTML5 的 FileReader 接口和 Drag 事件。最终的效果图如下，本篇文章主要就拖拽选取图片以及图片预览的核心代码进行讲解。

![图片上传预览](/img/image_upload_20170922.png)

<!-- more -->

### 一、拖拽选取图片

拖拽选取图片的原理在于监听拖拽事件，并阻止浏览器默认事件（将图片拖入浏览器会默认打开图片的本地链接）。

> 选取多张图片需要在 file 控件中设置 multiple 属性
> &lt;input type="file" accept="image/jpeg,image/png,image/gif" multiple&gt;

```
// 监听拖拽事件
(function () {

  // 假设作为拖拽区域的 DOM 元素为 dragDrop，示例图中的蓝色虚线框元素;
  dragDrop.addEventListener('dragover', function(e) {
    e.preventDefault();
  }, false);

  dragDrop.addEventListener('drop', function(e) {
    e.preventDefault();

    // 到这一步开始处理图片预览的逻辑
  }, false);
})();
```

### 二、图片预览

```
function previewImg(e) {
  var files = e.files, // 选取的文件数组
      html = '',
      i = 0;
  
  var appendImage = function() {
    var file = files[i];
    if (file) {
      var reader = new FileReader();

      // reader 对象完成读取文件后，会触发 onload 事件
      reader.onload = function(e) {
        html += "<img src=\"" + e.target.resut + "\">";
        i++;
        appendImage(); // 递归的方式添加图片列表
      }

      // 读取文件
      reader.readAsDataURL(file);
    } else {
      // 添加完所有选中的图片列表后，将拼接好的 html 插入到文档中
      // 假设展示预览图片的 DOM 元素为 previewBox
      previewBox.innerHTML = html;
    }
  }
}
```
