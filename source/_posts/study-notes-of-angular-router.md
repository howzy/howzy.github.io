---
title: Angular 路由与导航
tags:
  - Angular
date: 2017-09-08 17:31:36
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

### 二、命令式导航

命令式导航需要借助 Router 类，在构造函数中注入它。

```
// step-1
import { Router } from '@angular/router';

// step-2
constructor(private router: Router) {}
```

注入 Router 类之后，就可以使用它的 navigate() 或者 navigateByUrl() 方法进行导航。当调用 navigateByUrl 方法时，总是要指定完整的**绝对路径**。

```
// 无参数导航
this.router.navigate(['/']);

// 带参数导航，这一形式匹配之前配置好的参数路由 '/hero/:id'
this.router.navigate(['/hero', hero.id]);

// this.router.navigateByUrl('/heroes');
```

navigate() 和 navigateByUrl() 方法都接受第二个可选参数 NavigationExtras 类，这个类可以配置路由的参数。

**1. 相对于当前的激活路由设置导航: relativeTo**

```
// 路由定义
{
  path: 'parent',
  component: ParentComponent,
  children: [
    { path: 'list', component: ListComponent },
    { path: 'child', component: ChildComponent }
  ]
}

// 从 child 路由导航到 list 路由
@Component({...})
class ChildComponent {
  constructor(private router: Router, private route: ActivatedRoute) {}

  go() {
    this.router.navigate(['../list'], { relativeTo: this.route });
  }
}
```

**2. 设置查询参数: queryParams**

```
// 导航到 '/results?page=1'
this.router.navigate(['/results'], { queryParams: { page: 1 } });
```

**3. 设置 hash 片段: fragment**

```
// 导航到 '/results#top'
this.router.navigate(['/results'], { fragment: { 'top' } });
```

**4. 设置查询参数策略: queryParamsHandling**

```
// preserve: 保留当前路由中的查询参数
// 从 '/results?page=1' 导航到 '/view?page=1'
this.router.navigate(['/view'], { queryParamsHandling: 'preserve' });

// merge: 合并当前路由中的查询参数
// 从 '/results?page=1' 导航到 '/view?page=1&page=2'
this.router.navigate(['/view'], { queryParams: { page: 2 }, queryParamsHandling: 'merge' });
```

**5. 保留当前路由中的 hash 片段: preserveFragment**

```
// 从 '/results#top' 导航到 '/view#top'
this.router.navigate(['/view'], { preserveFragment });
```

### 三、路由参数获取

路由中的参数有三种可能形式：路由令牌参数(params)，查询参数(queryParams)，Hash 片段(fragment)，上文中讲解了他们的传递设置，接下来总结一下他们的获取方法。路由参数的获取需要注入 ActivatedRoute 类。这三种形式的获取方式类似，下面以路由令牌参数为例子。

```
// step-1
import { ActivatedRoute } from '@angular/router';

// step-2
constructor(private route: ActivatedRoute) {}
```

**1. 可观察对象 Observable 形式**

```
// 路由定义：path: 'hero/:id'
// 路由地址：'/hero/1'
this.route.params.subscribe(param => let heroId = param['id']);

// paramsMap 是 params 的 map 形式，使用可观察对象的 switchMap 操作符
this.route.paramsMap.switchMap(params => let heroId = params.get('id'));
```

**2. 快照 Snapshot 形式**

```
this.route.snapshot.params['id'];

this.route.snapshot.paramsMap.get('id');
```

> 可观察对象在订阅之后能自动推送订阅值的变化，snapshot 则是在组件渲染的时候定格的一个状态，除非组件重新渲染，否则值不变。

### 四、路由守卫

有些业务模块需要根据特定权限才能访问，在路由访问的时候进行拦截，判断权限，根据权限判断结果再进行相应的导航。Angular 中的路由守卫提供了这一功能。

**1. 创建 CanActivate 服务**

```
import { Injectable } from '@angular/core';
import { CanActivate, ActivatedRouteSnapshot, RouterStateSnapshot } from '@angular/router';

@Injectable()
export class AuthGrard implement CanActivate {
  canActivate(route: ActivatedRouteSnapshot, state: RouterStateSnapshot): boolean {
    // 权限判断之后返回相应的状态
    // false 表示无权访问路由
    return false;
  }
}
```

**2. 注册 CanActivate 服务并配置路由守卫**

```
// 路由定义
{
  path: 'admin',
  component: AdminComponent,
  canActivate: [AuthGrard]
}

// 在该模块的 providers 属性中注入路由守卫服务
providers: [AuthGrard]
```
> 子路由的守卫 CanActivateChild 服务和路由守卫定义类似，在子路由定义中配置守卫。