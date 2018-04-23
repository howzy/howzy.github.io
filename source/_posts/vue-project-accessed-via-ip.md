---
layout: drafts
title: 通过 IP 访问 vue 项目
tags:
  - Vue
date: 2018-04-23 12:53:34
---


最近遇到了一个坑，vue-cli 创建的项目，想要在手机端通过 IP 访问，但是居然访问不了。查看项目配置文件，发现 dev 环境下指定了 `host` 为 `localhost`，导致在电脑上跑起项目，在手机上通过 IP 出问题。

```
// config/index.js
dev: {
  ...
  host: 'localhost'
  ...
}

// webpack.dev.conf.js

const config = require('../config')
const HOST = process.env.HOST
const devWebpackConfig = merge(baseWebpackConfig, {
  ...
  host: HOST || config.dev.host
  ...
}
```

为了能解决目前的问题，只要将 host 指定为 `0.0.0.0` 即可。

```
// config/index.js
dev: {
  ...
  host: '0.0.0.0'
  ...
}
```

> 0.0.0.0: 能指定任何 IPv4 地址