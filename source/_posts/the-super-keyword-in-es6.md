---
title: ES6 中的 super 关键字
tags:
  - ES6
  - 原生js
date: 2019-01-08 20:29:17
---


ES6 的 super 用在 Class 的继承中,既可以当作函数使用，也可以当作对象使用。下面总结一下 super 的使用以及需要注意的地方。

**1. 子类的构造函数必须执行一次 super 函数，super() 代表的是父类的构造函数，返回子类的实例，换句话说只有调用了父类的构造函数才会有子类的 this。**

```
class A {}

// 不调用 super 函数，Javascript 引擎会报错
class B extends A {
  constructor() {
    console.log(this); // ReferenceError
  }
}
```

**2. super 作为函数，只能在子类的构造函数中调用。super 作为对象，则可以在任何地方调用。**

```
class A {}

class B extends A {
  constructor() {
    super();              // Correct
    console.log(super.x); // Correct
  }

  m() {
    super();              // SyntaxError
    console.log(super.x); // Correct
  }
}
```

<!-- more -->

**3. 在普通方法中调用 super，它指向父类的原型对象。被调用的父类原型方法内部的 this 指向当前子类实例。**

```
class A {
  constructor() {
    this.x = 1;
  }

  // 类的所有方法都定义在类的 prototype 属性上面，既原型对象。
  print() {
    console.log(this.x);
  }
}

class B extends A {
  constructor() {
    super();
    this.x = 2;
  }

  m() {
    super.print();
  }
}

let b = new B();
b.m(); // 2
```

**4. 由于 this 指向子类实例，所以如果通过 super 对某个属性赋值，这时 super 就是 this，赋值的属性会变成子类实例的属性。**

```
class A {
  constructor() {
    this.x = 1;
  }
}

class B extends A {
  constructor() {
    super();
    this.x = 2;
    super.x = 3; // 赋值会在子类实例中生效
    console.log(super.x); // undefined, 因为父类 A 的原型上没有 x 属性
    console.log(this.x); // 3
  }
}

let b = new B();
```

**5. 在静态方法中调用 super，它指向父类。被调用的父类方法（既父类的静态方法）内部的 this 指向当前子类。**

```
class A {
  constructor() {
    this.x = 1;
  }
  
  static print() {
    console.log(this.x);
  }

  print() {
    console.log('此例中不会执行这个函数')
  }
}

class B extends A {
  constructor() {
    super();
    this.x = 2;
  }

  static m() {
    super.print(); // super 指向父类，print 是父类的静态方法
  }
}

B.m();    // undefined
B.x = 3;
B.m();    // 3
```
