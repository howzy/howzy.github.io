---
title: forEach、for-in和for循环之间的异同
tags:
  - 原生js
date: 2018-10-29 17:53:37
---


`forEach`、`for-in`、`for` 三者都是循环，但是在不同的场景下，三者的表现也是各不相同的。先上结论，然后再看看相应的例子。

- 一般情况下，for 循环是效率最高的
- forEach 和 for-in 在`稀疏数组`的情况下效率比 for 循环高
- forEach 不能中断循环
- for-in 是循环可枚举属性，当数组原型上扩展了一些属性，那么这些属性也会被循环到。循环的每一个值的类型是 'string'。

<!-- more -->

### 一、效率

**1. 普通数组**

循环1到1000的数组
```
var arr = [];
for (var i = 0; i < 1000; i++) {
  arr.push(i);
}
```

打印执行时间
```
var forEachCount = 0, forCount = 0, forInCount = 0;

console.time('forEach');
arr.forEach(function (v, i) {
  forEachCount++;
});
console.timeEnd('forEach');

console.time('for');
for (var j = 0, l = arr.length; j < l; j++) {
  forCount++;
}
console.timeEnd('for');

console.time('for-in');
for (var k in arr) {
  forInCount++;
}
console.timeEnd('for-in');

console.log('forEachCount: ', forEachCount);
console.log('forCount: ', forCount);
console.log('forInCount: ', forInCount);

// 最终输出：
forEach: 0.06689453125ms
for: 0.05908203125ms
for-in: 0.0830078125ms
forEachCount: 1000
forCount: 1000
forInCount: 1000
```

**2. 稀疏数组**

由上述输出结果可知，for 循环的效率要高一些，接下来测试一下稀疏数组的情况。

```
var arr = new Array(1000);
arr[0] = 1;
arr[99] = 2;
arr[999] = 3;
```

创建好稀疏数组之后，重复上述的 ‘打印执行时间’ 步骤。

```
// 最终输出：
forEach: 0.035888671875ms
for: 0.05908203125ms
for-in: 0.01123046875ms
forEachCount: 3
forCount: 1000
forInCount: 3
```

可知，forEach 和 for-in 循环只执行了三次，跳过了值为空的项，并且总体执行时间比 for 循环更快。

### 二、特性

```
var arr = [1, 2, 3];
var forEachCount = 0, forCount = 0, forInCount = 0;

// 扩展数组原型属性
Array.prototype.test = 'test';

arr.forEach(function(v, i) {
  forEachCount++;
  if (i === 1) break;
});

for (var j = 0, l = arr.length; j < l; j++) {
  forCount++;
  if (j === 1) break;
}

for (var k in arr) {
  console.log(k);
  forInCount++;
}

console.log('forEachCount: ', forEachCount);
console.log('forCount: ', forCount);
console.log('forInCount: ', forInCount);

// 最终输出
0
1
2
test
forEachCount: 3
forCount: 2
forInCount: 4
```

正常的打印次数是 3 次，因此 for 循环被中断了，forEach 没有被中断。for-in 循环把可枚举的属性都循环了。