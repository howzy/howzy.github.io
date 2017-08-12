---
title: Vue 中按钮组轮播的实现思路
tags:
  - Vue
  - 轮播
date: 2017-08-12 23:52:30
---


![step1](/img/carousel_list_20170812.png)

上图中的类型入口按钮组算是比较通用的功能模块，基本上各种电商类的 app 都会存在这一模块，入口少的就一两行排列，入口稍微多一些的就采用轮播实现左右滑动。饿了么 app 就是采用左右滑动的一个实例，它用的是 Vue 的框架，下面就分享一下实现思路。

<!-- more -->

从后端接口获取的数据一般会是数组列表的形式，那么可以按照一个页面中需要显示的入口按钮数量,分割返回的数组列表

```
// 模板编译挂载之后（生命周期）
mounted() {
  // 从相关接口获取数据后，对返回的数组列表 res 进行分割处理
  let resArr = res.contact([]);
  let foodArr = [];
  for (let i = 0; i < resArr.length; i += 8) { // 假设每8个为一组
    foodArr.push(resArr.slice(i, i+8));
  }
}
```

> 左右滑动的效果采用 <a href="http://www.swiper.com.cn/" target="_blank">swiper</a> 插件实现，需要注意的是插件的初始化需要在数据处理好之后进行

获得处理好之后的数据，便可以进行**双重循环**来渲染组件了

```
<div class="swiper-container">
  <div class="swiper-wrapper">
    <div class="swiper-slide food_types_container" v-for="(item, index) in foodArr" :key="index">
      <router-link :to="foodItem.link" v-for="foodItem in item" :key="foodItem.id">
        <figure>
          <img :src="foodItem.image_url">
          <figcaption>{{foodItem.title}}</figcaption>
        </figure>
      </router-link>
    </div>
  </div>
  <div class="swiper-pagination"></div>
</div>
```
