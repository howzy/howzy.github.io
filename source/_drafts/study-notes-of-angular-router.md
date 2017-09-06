---
title: Angular 路由与导航
tags:
  - Angular
---

### 前言

### 路由配置

立即加载模块和惰性加载模块的区别与使用方法

立即加载模块在父模块的 imports 中导入，在自身路由模块中配置路由

惰性加载和重新配置工作只会发生一次，也就是在该路由首次被请求时。在后续的请求中，该模块和路由都是立即可用的。
loadChildren: 'app/crisis-center/crisis-center.module#CrisisCenterModule'


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