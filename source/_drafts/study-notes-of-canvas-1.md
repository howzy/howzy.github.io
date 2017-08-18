---
title: Canvas 学习笔记（一）
tags:
 - Canvas
---

Canvas 是一个可以使用脚本（通常为 Javascript）在其中回执图形的 HTML 元素。它可以用于制作照片集或者制作简单的动画。&lt;canvas&gt; 元素不被一些老的浏览器所支持，但是所有的主流浏览器的新近版本都支持。Canvas 的默认大小为 300 x 150 像素。为了在 Canvas 上绘制图形，使用一个 JavaScript 上下文对象，它能动态创建图像。接下来总结一下基本操作。

<!--more-->

### 基本操作

获取上下文对象
```
var ctx = document.createElement('canvas').getContext('2d');
```

> 接下来的操作都是基于上下文对象 ctx。

绘制矩形
```
fillRect(x, y, width, height); // 填充矩形

strokeRect(x, y, width, height); // 描边矩形

clearRect(x, y, width, height); // 清除指定矩形区域
```

绘制路径
```
arc()
```