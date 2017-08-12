---
title: 原生js实现页面滚动
tags:
  - 工具函数
  - 原生js
date: 2017-08-06 22:12:31
---


日常开发中，页面滚动至指定位置这一功能经常会有需求，一般的处理方式都是用 jQuery 或者其他第三方库来实现。但是随着自己平时开发中使用 jQuery 频率在不断减少，就想着用原生 js 来实现，思路还是比较清晰的，提炼了一下核心代码，后续的开发还可以再进一步封装扩展。

<!-- more -->

### 核心代码

```
var requestAnimationFrame = window.requestAnimationFrame || window.webkitRequestAnimationFrame || window.mozRequestAnimationFrame || function (callback) { return window.setTimeout(callback, 1000/60); };

// 缓动算法
var easeInOutQuad = function (t, b, c, d) {
  t /= d / 2;
  if (t < 1) return c / 2 * t * t + b;
  t--;
  return -c / 2 * (t * (t - 2) - 1) + b;
};

/**
 * 滚动函数
 * @param {HTMLDocument} element - 需要滚动的元素
 * @param {Number} to - 目标位置
 * @param {Number} duration - 动画持续时间
 * @param {Function} callback - 回调函数
 */
var scrollTo = function (element, to, duration, callback) {
  var beginPos = element.scrollTop,
      change = to - beginPos,
      startTime = +new Date(), // 滚动开始时间
      runTime = null; // 每次执行滚动动画的初始时间
  
  var animate = function () {
    runTime = +new Date();
    if((runTime - startTime) <= duration) {
      element.scrollTop = Math.floor(easeInOutQuad((runTime - startTime), beginPos, change, duration));
      requestAnimationFrame(animate);
    } else {
      element.scrollTop = to;
      if (callback) return callback();
    }
  };
  requestAnimationFrame(animate);
};
```

### 使用方法

```
// 点击滚动至页面顶部
button.addEventListener('click', function () {
  scrollTo(
    document.body,
    0,
    1000,
    function () {
      console.log('done');
    }
  );
})
```
