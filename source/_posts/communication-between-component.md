---
title: Angular 组件通讯
tags:
  - Angular
date: 2017-09-17 23:32:16
---


组件间的数据交互是非常常见的，总结一下自己平时用的比较多的通讯形式。首先构思一个业务场景：有个推送消息的组件 A ，需要在另一个组件 C 中接收推送来的消息。

![业务场景](/img/component_communication_20170917.png)

<!-- more -->

### 一、通过绑定输入输出型数据

组件 B 通过 Output 推送数据。

```
import { Component, Output, EventEmitter } from '@angular/core';

@Component({
  selector: 'app-message',
  template: `
    <button (click)="send()">sendMessage</button>
    <button (click)="clear()">clearMessage</button>
  `
})
export class AppMessageComponent {
  @Output() sendMessage: EventEmitter<any> = new EventEmitter();

  send() {
    this.sendMessage.emit({ text: 'Message from Home Component to App Component!' });
  }

  clear() {
    this.sendMessage.emit(null);
  }
}
```

组件 A 接收组件 B 触发的 sendMessage 事件，并获取通过事件传递的值。

```
import { Component } from '@angular/core';

@Component({
  selector: 'app-parent',
  template: `
    <app-message (sendMessage)="onSendMessage($event)"></app-message>
    <p>{{ message?.text }}</p>
  `
})
export class AppParentComponent {
  message: any;
  
  onSendMessage(message) {
    this.message = message;
  }
}
```

> 上面的例子直接在组件 A 中展示获取的数据，如果希望传递到组件 C 中，则可以在组件 C 中通过 Input 绑定输入属性。

### 二、通过服务来通讯

上述例子的场景比较简单，只是父子组件间的通讯，如果需要通讯的两个组件关系比较复杂，则可以通过服务共享同一个实例来传递数据。

```
// message.service.ts
import { Injectable } from '@angular/core';
import { Observable } from 'rxjs/Observable';
import { Subject } from 'rxjs/Subject';

@Injectable()
export class MessageService {
  private subject = new Subject<any>();

  sendMessage(message: string) {
    this.subject.next({ text: message });
  }

  clearMessage() {
    this.subject.next();
  }

  getMessage(): Observable<any> {
    return this.subject.asObservable();
  }
}
```

> rxjs 库的观察者模式，观察者在订阅主题 subject 之后能自动接收主题的推送。在组件 B 中调用该服务来更新主题。

```
import { Component } from '@angular/core';
import { MessageService } from './message.service.ts';

@Component({
  selector: 'app-message',
  template: `
    <button (click)="send()">sendMessage</button>
    <button (click)="clear()">clearMessage</button>
  `
})
export class AppMessageComponent {
  constructor(private messageService: MessageService) { }

  sendMessage(): void { // 发送消息
    this.messageService.sendMessage('Message from Home Component to App Component!');
  }

  clearMessage(): void { // 清除消息
    this.messageService.clearMessage();
  }
}
```

在组件 C 中订阅消息主题

```
import { Component, OnDestroy } from '@angular/core';
import { Subscription } from 'rxjs/Subscription';
import { MessageService } from './message.service';

@Component({
  selector: 'app-c',
  template: `
    <app-message></app-message>
    <p>{{ message?.text }}</p>
  `
})
export class AppCComponent {
  message: any;
  subscription: Subscription;

  constructor(private messageService: MessageService) {
    this.subscription = this.messageService.getMessage()
      .subscribe(message => {
        this.message = message;
      });
  }

  ngOnDestroy() {
    this.subscription.unsubscribe();
  }
}
```
> 组件 C 在被销毁的时候调用 unsubscribe() 退订主题，这是为了防止内存泄露。
