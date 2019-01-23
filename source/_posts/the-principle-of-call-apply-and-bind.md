---
title: call、apply 和 bind 函数的内部原理
tags:
  - 原生js
date: 2019-01-23 23:38:03
---


call、apply 和 bind 函数有着相似的功能，它们能改变函数运行时的上下文环境 `this`。但是在使用上又存在着一些差异，本文将通过手动实现其核心功能来阐明内部原理。

### 一、call

> 语法：func.call(thisArg, arg1, arg2, ...)

```
Function.prototype.myCall = function(ctx) {
  if (typeof this !== 'function') {
    throw new Error('error')
  }

  ctx = ctx || window
  ctx.fn = this
  const args = [...arguments].slice(1)
  let result = ctx.fn(...args)
  delete ctx.fn
  return result
}
```

<!-- more -->

- 首先 ctx 为可选参数，默认为 window
- 将函数挂到 ctx 对象上，使得 this 指向 ctx
- call 可以接收多个参数，因此需要将参数剥离出来
- 调用函数并将对象上的函数删除

### 二、apply

> 语法：func.apply(thisArg, [argsArray])

```
Function.prototype.myApply = function(ctx) {
  if (typeof this !== 'function') {
    throw new Error('error')
  }

  ctx = ctx || window
  ctx.fn = this
  let result

  if (arguments[1]) {
    result = ctx.fn(...arguments[1])
  } else {
    result = ctx.fn()
  }
  delete ctx.fn
  return result
}
```

- apply 的实现与 call 类似，区别在于函数参数是以数组的形式

### 三、bind

> 语法： func.bind(thisArg[, arg1[, arg2[, ...]]])

```
Function.prototype.myBind = function(ctx) {
  if (typeof this !== 'function') {
    throw new Error('error')
  }

  let _this = this
  let args = [...arguments].slice(1)

  return function F() {
    if (this instanceof F) {
      return new _this(...args, ...arguments)
    }
    return _this.apply(ctx, args.concat(...arguments))
  }
}
```

- bind 返回的是一个函数，对于函数来说有两种调用方式，一种是直接调用，一种是通过 `new` 的方式
- 直接调用时，需要注意参数形式，参数可以在 bind 函数中，或者在 bind 返回的函数中，因此需要拼接两边的参数。例如： f.bind(obj, 1)(2)
- 通过 `new` 的方式时，既绑定函数作为构造函数被调用，此时原来提供的 `this` 会被忽略，不过提供的参数列表仍然会插入到构造函数调用时的参数列表之前。