---
title: Canvas 学习笔记（一）
tags:
  - Canvas
date: 2017-08-19 11:36:15
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
beginPath(); // 新建一条路径

moveTo(x, y); // 移动至绘制起点

lineTo(x, y); // 绘制一条从当前位置到指定x以及y位置的直线

arc(x, y, radius, startAngle, endAngle, anticlockwise); // 画一个以（x,y）为圆心的以radius为半径的圆弧（圆），从startAngle开始到endAngle结束，按照anticlockwise给定的方向（默认为顺时针 false）来生成

arcTo(x1, y1, x2, y2, radius); // 根据给定的控制点和半径画一段圆弧，再以直线连接两个控制点

quadraticCurveTo(cp1x, cp1y, x, y); // 绘制二次贝塞尔曲线，cp1x,cp1y为一个控制点，x,y为结束点

bezierCurveTo(cp1x, cp1y, cp2x, cp2y, x, y); // 绘制三次贝塞尔曲线，cp1x,cp1y为控制点一，cp2x,cp2y为控制点二，x,y为结束点
```

> 路径绘制完之后，closePath() 闭合路径，fill() 通过填充路径的内容区域生成实心图形（会自动闭合路径，可以不用调用 closePath() 函数），stroke() 通过线条来绘制图形轮廓

绘制样式和颜色
```
fillStyle = "color"; // 设置图形的填充颜色

strokeStyle = "color"; // 设置图形轮廓的颜色

createLinearGradient(x1, y1, x2, y2); // 渐变的起点 (x1,y1) 与终点 (x2,y2) 的线性渐变

createRadialGradient(x1, y1, r1, x2, y2, r2); // 圆1 到圆2 的径向渐变
```

绘制文本
```
fillText(text, x, y [, maxWidth]); // 在指定的(x,y)位置填充指定的文本，绘制的最大宽度是可选的

strokeText(text, x, y [, maxWidth]); // 在指定的(x,y)位置绘制文本边框，绘制的最大宽度是可选的

font = value; // 设置字体

textAlign = value; // 文本对齐方式：'start', 'end', 'left', 'right' or 'center'

textBaseline = value; // 基线对齐方式：'top', 'hanging', 'middle', 'alphabetic', 'ideographic', 'bottom'

shadowOffsetX, shadowOffsetY, shadowBlur, shadowColor // 阴影，和 CSS3 属性一样
```

状态的保存和恢复

> save 和 restore 方法用来保存和恢复 canvas 状态的。Canvas 的状态就是当前画面应用的所有样式和变形。

```
ctx.fillStyle = "red";
ctx.save();
ctx.fillStyle = "blue";
ctx.restore(); // 恢复上次状态的保存值
ctx.fillRect(0, 0, 100, 100); // 红色矩形
```

变形
```
rotate(angle); // 围绕原点旋转图像

scale(x, y); // 缩放

translate(x, y); // 将坐标原点平移到(x, y)
```