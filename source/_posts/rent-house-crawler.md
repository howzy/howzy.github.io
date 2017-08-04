---
title: 豆瓣租房信息爬虫 - 总结
tags:
  - 爬虫
  - Nodejs
  - MongoDB
date: 2017-08-03 23:50:22
---


近段时间，自己租的房子快要到期了，综合考虑之后，打算重新租一间离公司近一些的，毕竟拥堵的交通无形间增加了很大的时间成本。说到租房，浏览各种租房信息网站是不可避免的，大量的信息，各种对比，想想就让人心力交瘁。脑海里灵光一闪，何不做一个爬虫去收集并处理租房信息呢？

有想法之后就着手去做了，利用空余时间捣鼓捣鼓，总算是有了个雏形。开个定时调用，坐等邮件更新，收集信息的效率可谓提高不少呀。但是在写这篇总结的时候，看看自己写的代码，尴尬癌都犯了，代码真不够优雅美丽。不过需求摆在那里，还是先实现功能再说吧，毕竟高质量的代码都是改出来的。不闲扯了，直接进入主题。

### 实现思路

- 使用 axios 获取豆瓣租房小组 HTML 页面
- 使用 cheerio 根据目标关键词过滤租房信息
- 使用 mongoose 将过滤后的信息存入 MongoDB 数据库
- 使用 nodemailer 将过滤后的信息发送至指定邮箱

<!-- more -->

项目源码地址：<a href="https://github.com/zhonce/rent-house-crawler.git" target="_blank">rent-house-crawler</a>

### 踩坑与提升

> 前段时间学习了 MongoDB 的一些基础知识，正好借着这个 demo 能实践一下。

**1. mongoose 连接数据库**

mongoose 连接数据库有两种方式: mongoose.connect() 和 mongoose.createConnection(). 两者的区别在于，前者创建的连接是 default conncetion, 返回 mongoose 实例，而后者创建的是一个新的连接，并返回这个新连接。

```
// 如果只需连接一个数据库，可以使用 mongoose.connect()
mongoose.connect('mongodb://localhost/test'); // 返回 mongoose 实例

var db = mongoose.connection; // db 是 mongoose 实例的连接

userSchema = new mongoose.Schema({ name: String });
userModel = mongoose.model('User', userSchema);

=====================================================================

// 如果不止连接一个数据库，则使用 mongoose.createConnection()
var db = mongoose.createConnection('mongodb://localhost/test'); // db 是返回的新连接

userSchema = new mongoose.Schema({ name: String });
userModel = db.model('User', userSchema); // 当前激活的连接不是默认的，此处用 mongoose.model() 不能按预期工作
```

**2. 数据去重**

```
// 在 mongoose 中可以在创建数据模型的时候为目标字段指定 SchemaType 为 unique
TopicSchema = new mongoose.Schema({
  title: { type: String, unique: true }
});

=====================================================================

// 在mongo Shell 中可以为字段创建唯一索引
db.test.createIndex({ title: 1 }, { unique: true });
```

**3. 更新数据并返回更新后的数据**

```
// mongoose
Topic.findOneAndUpdate(
  { title: '标题' },
  { $set: { details: '详情' } },
  { new: true }
);

=====================================================================

// mongo Shell

// 方法一
db.topic.findOneAndUpdate(
  { title: '标题' },
  { $set: { details: '详情' } },
  { returnNewDocument: true }
);

// 方法二
db.topic.findAndModify({
  query: { title: '标题' },
  update: { $set: { details: '详情' } },
  new: true
});
```

### 新技能 Get

> 初次使用第三方库 <a href="https://nodemailer.com/about/" target="_blank" title="官方教程">nodemailer</a> 发送邮件，官网的教程还是非常简洁明了的，这里就搬运一下。

**运行环境**

- Node.js v6+

**安装**

```
npm install nodemailer --save
```

**用法**

```
'use strict';
const nodemailer = require('nodemailer');

// 使用默认 SMTP 传输创建可重用的传输器对象
let transporter = nodemailer.createTransport({
  host: 'smtp.example.com', // 例如QQ邮箱：smtp.qq.com
  port: 465,
  secure: true, // 值为 true 时端口为 465，值为 false 时端口为587
  auth: {
    user: 'username@example.com',
    pass: 'userpass' // 开启 POP3/SMTP 服务之后获得的授权码
  }
});

// 使用 unicode 编码设置电子邮件数据
let mailOptions = {
  from: 'username@example.com', // 发件人邮箱
  to: 'receiver1@example.com, receiver2@example.com', // 收件人邮箱列表
  subject: '', // 邮件标题
  html: '' // 邮件内容
};

// 使用定义的传输对象发送邮件
transporter.sendMail(mailOptions, (error, info) => {
  if (error) {
    // 错误处理
  }
  console.log(info.messageId, info.response);
});
```
