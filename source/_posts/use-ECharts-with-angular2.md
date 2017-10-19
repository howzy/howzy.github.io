---
title: Angular2 中使用 ECharts 图表
tags:
  - Angular
date: 2017-10-13 17:28:08
---


使用 ECharts 图表库的主要流程是：首先引入 js 库，其次初始化作为图表载体的 dom 元素，然后设置图表配置项。在 angular 中可以利用 **自定义指令** 的形式提炼出图表的初始化和配置。先上代码，然后在讲解一下需要注意的点。

![ECharts 入门示例](/img/use_ECharts_with_angular2_20171013.png)

<!--more-->

### 一、引入 js 库

```
npm install echarts --save
```

### 二、自定义指令

考虑到图表的数据来源常见的为异步获取，所以选择在 OnChanges 生命周期接收到数据后去配置图表

```
import { Directive, ElementRef, Input, OnInit, OnChanges, SimpleChange } from '@angular/core';

import * as echarts from 'echarts';
import Echarts = echarts.Echarts;
import EchartOption = echarts.EchartOption;

@Directive({
  selector: 'echart'
})
export class EchartOptionDirective implements OnInit, OnChanges {
  private chart: Echarts;

  // 图表配置项
  @Input() chartType: EchartOption;

  constructor(private el: ElementRef) { }

  ngOnInit() {
    // 初始化图表载体的 dom 元素
    this.chart = echarts.init(this.el.nativeElement);
  }

  ngOnChanges(changes: {[propKey: string]: SimpleChange}) {
    if (this.chartType && this.chart) {
      // 在图表初始化后，接收到传入的配置参数后配置图表
      this.chart.setOption(this.chartType);
    }
  }
}
```

### 三、在组件中使用自定义的指令

```
import { Component, OnInit } from '@angular/core';

@Component({
  template: `<echart [chartType]="option" class="my-echart"></echart>`,
  styles: `
    .my-echart {
      width: 100%;
      height: 400px;
      display: block;
    }
  `
})
export class AppComponent implements OnInit {
  // ECharts 图表配置项，具体参见官方文档
  option = {
    title: {
      text: 'ECharts 入门示例'
    },
    tooltip: {},
    legend: {
      data: ['销量']
    },
    xAxis: {
      data: ["衬衫", "羊毛衫", "雪纺衫", "裤子", "高跟鞋", "袜子"]
    },
    yAxis: {},
    series: [{
      name: '销量',
      type: 'bar',
      data: []
    }]
  };

  ngOnInit() {
    // 异步获取数据之后，将所需数据配置好
    this.option.series[0].data = [5, 20, 36, 10, 10, 20];

    // 改变对象的引用，触发输入属性的 ngOnChanges 事件
    this.option = Object.assign({}, this.option); 
  }
}
```

> 上面代码中有两处需要注意的地方：其一是图表载体的元素需要指定 **宽和高**，否则图表将不会展示。其二是需要 **改变输入属性**（JS 中对象的引用改变和对象的属性值的改变是不一样的），这是为了触发 ngOnChanges 事件然后才能将数据配置成功。

### 四、图表自适应

上述过程完成了图表的展示，美中不足的是当窗口尺寸变化时，图表的尺寸不会改变，为了让图表自适应，需要调用 Echarts 的 resize 事件。在自定义指令中利用 Observable 监听窗口尺寸的变化。

```
import { Observable } from "rxjs/Observable";
import 'rxjs/add/observable/fromEvent';
import 'rxjs/add/operator/debounceTime';


ngOnInit() {
  // 初始化图表载体的 dom 元素
  this.chart = echarts.init(this.el.nativeElement);

  Observable.fromEvent(window, 'resize')
    .debounceTime(100)
    .subscribe(event => {
      this.chart.resize();
    });
}
```