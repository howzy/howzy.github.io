---
title: Angular2 项目部署
tags:
  - Angular
date: 2017-11-08 13:32:35
---


本文记录一下 Angular2 项目部署的大致流程，以便日后回顾。

打包代码,采用生产环境模式 --prod，并设置项目的根目录 --base-href

```
ng build --prod --base-href './projectName'
```

> 当项目不是放在服务器的根目录，而是在子目录下时，若不设置打包的相对目录，就会报404错误，找不到js、css等文件，因此在打包的时候设置 --base-href 参数即可。

将打包好的项目用 Tomcat 部署

把项目源码放到 Tomcat 安装目录的 webapps 子目录中，删除其中的ROOT文件夹，修改Tomcat部署配置

```
# server.xml 文件
// 在Host标签中添加下列代码，更改发布目录
<Context path="" docBase="\projectName"/>
```