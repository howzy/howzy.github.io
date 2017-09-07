---
title: Angular 路由与导航
tags:
  - Angular
---

路由模块是 Angular 体系中比较重要的模块，由各种复杂组件组成的应用，需要通过路由进行视图间的切换。Angular 路由默认采用的是 pushState 风格，现代的 HTML5 浏览器是最早支持 pushState 的，它需要在应用的 index.html 中添加一个 &lt;base href="/"&gt; 才能正常工作。当引用 CSS 文件，脚本和图片时，浏览器会用 href 属性的值作为相对 URL 前缀。

<!--more-->

### 一、路由配置

Angular 的路由器是一个可选的服务，在配置路由之前需要从 **@angular/router** 包中引入路由模块。路由器的配置分为两步，首先定义路由，然后将定义好的路由配置到对应的模块中。

**1. 基本路由配置**

```
import { RouterModule, Routes } from '@angular/router';

// step-1 定义路由数组
const appRoutes: Routes = [
  { path: 'crisis-center', component: CrisisListComponent }, // 普通路由
  { path: 'hero/:id', component: HeroDetailComponent }, // :id 是一个路由参数的令牌(Token)
  { path: '', redirectTo: '/heroes', pathMatch: 'full' }, // 空路径('')表示应用的默认路由，重定向到 '/heroes'
  { path: '**', component: PageNotFoundComponent } // 通配路由，与上述定义的路由都不匹配时，会采用通配路由
];

// step-2 在对应的模块中的 imports 属性中配置定义好的路由
RoterModule.forRoot(appRoutes)

```

> step-1: 路由定义中的 path 不能以斜杠(/)开头

> step-2: RouterModule.forRoot() 方法只能用于整个项目的根模块中(通常定义为AppModule)，除了根模块之外的任何模块只能用 RouterModule.forChild() 方法来配置路由

**2. 子路由配置**

子路由的定义和普通路由的定义差不多，多添加一个 children 属性定义，同时要在父组件中指定子路由出口

```
// 定义路由
{
  path: 'crisis-center',
  component: CrisisListComponent,
  children: [
    {
      path: ':id',
      component: CrisisDetailComponent
    }
  ]
}

// 在父组件的 HTML 模板中指定子路由出口
<p>This is father component.</p>
<router-outlet></router-outlet>
```

**3. 立即加载模块和惰性加载模块的配置**

当产品的业务比较复杂的时候，抽离出特定的业务模块是个较为合理的做法，这些模块的加载可以分为立即加载和惰性加载。从字面上来理解，立即加载是在应用启动的时候加载相关代码，而惰性加载则是在该路由首次被请求的时候，在后续的请求中，该模块和路由都是立即可用的。

A) 立即加载模块的路由在该模块中定义，然后在该模块的 imports 属性中配置定义好的路由，接下来只需要在根模块中引入该模块。

> 注意要记得在根模块中引入立即加载模块，否者会报错提示无法匹配相关路由。

B) 惰性模块是在根模块路由定义的 loadChildren 属性中指定，惰性模块内部的路由和立即加载模块的定义方式一样，都是在自身模块，形式上的区别在于引入的方式

```
// 在根模块的路由中
{
  path: 'crisis-center',
  loadChildren: 'app/crisis-center/crisis-center.module#CrisisCenterModule'
}
```

> 惰性加载模块内部路由，默认是在 '/crisis-center' 这一相对路径下

### 命令式导航

navigate
相对导航 { relativeTo: this.route }
当调用路由器的navigateByUrl时，总是要指定完整的绝对路径。

### 路由参数的传递与获取

ActivatedRoute, ParamMap

### 子路由

children

### 路由守卫

CanActivate
CanActivateChild
CanDeactivate