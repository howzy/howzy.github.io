---
title: 科学上网教程
date: 2018-06-24 13:20:42
tags:
---


- Windows、Mac 系统下谷歌浏览器科学上网
- Ubuntu 系统下谷歌浏览器科学上网
- Ubuntu 系统 GenPAC 全局代理

<!-- more -->

### 一、Windows、Mac 系统

1.  下载 Shadowsocks 客户端
    1.  [Windows 版本](https://github.com/shadowsocks/shadowsocks-windows/releases)
    2.  [Mac 版本](https://github.com/shadowsocks/ShadowsocksX-NG/releases/)
2.  获取 ss 并配置 Shadowsocks 客户端
    1.  https://my.ishadowx.net/
    2.  https://get.ss8.fun/
    3.  https://doub.io/sszhfx/
3.  Chrome 浏览器安装 SwitchyOmega 插件
    1.  下载 [SwitchyOmega](https://github.com/FelisCatus/SwitchyOmega/releases) crx 文件，并直接拖入扩展程序
4.  配置 SwitchyOmega
    1.  a. 进入插件设置页面
    2.  b. 新建情景模式（情景模式类型选择代理服务器），创建
    3.  c. 代理协议选择 SOCKS5，代理服务器填写 127.0.0.1，代理端口 1080
    4.  d. 点击应用选项
    5.  e. 点击自动切换，点击应用选项
    6.  f. 规则列表格式选择 AutoProxy
    7.  g. 规则列表网址：https://raw.githubusercontent.com/gfwlist/gfwlist/master/gfwlist.txt
    8.  h. 点击立即更新情景模式 --> 应用选项

### 二、Ubuntu 系统

1.安装 Shadowsocks-Qt5 客户端

```
// 加入 PPA 源
sudo add-apt-repository ppa:hzwhuang/ss-qt5

// 更新和安装
sudo apt-get update
sudo apt-get install shadowsocks-qt5
```

> Ubuntu 18.04 版本安装 Shadowsocks-Qt5 客户端时，添加 PPA 源会报错提示 bionic Release Not Found，这是因为目前（2018.06.24）官方还没有 18.04 版本的源
> 只需要修改至低版本的源就可以了，比如 Ubuntu 17 版本 artful

```
sudo vim /etc/apt/sources.list.d

deb http://ppa.launchpad.net/hzwhuang/ss-qt5/ubuntu artful main
# deb-src http://ppa.launchpad.net/hzwhuang/ss-qt5/ubuntu bionic main
# deb-src http://ppa.launchpad.net/hzwhuang/ss-qt5/ubuntu bionic main
```

安装成功之后就可以打开 Shadowsocks-Qt5 客户端了，接下来的步骤和上述的 2、3、4 一样，至此就可以实现 Chrome 浏览器智能代理。

### 三、Ubuntu 系统 [GenPAC](https://github.com/JinnLynn/genpac) 全局代理

1.安装 GenPAC

```
// 首先安装 pip
sudo apt-get install python-pip

// 安装 genpac
pip install genpac
// 或从 github 安装开发版本
pip install https://github.com/JinnLynn/genpac/archive/master.zip

// 更新
pip install --upgrade genpac
// 或从 github 更新开发版本
pip install --upgrade https://github.com/JinnLynn/genpac/archive/master.zip
```

2.使用 GenPAC 生成 pac 文件(shadowsocks 需要在连接状态)

```
genpac --pac-proxy "SOCKS5 127.0.0.1:1080" --gfwlist-proxy="SOCKS5 127.0.0.1:1080" --gfwlist-url=https://raw.githubusercontent.com/gfwlist/gfwlist/master/gfwlist.txt -o="autoproxy.pac"
```

> 执行上述命令之后会在当前终端目录下生成 autoproxy.pac 文件，例如路径为：/home/用户名/autoproxy.pac

3.更改系统网络代理设置
进入代理设置 `System settings > Network > Network Proxy`
设置 Method 为 Automatic
设置 Configuration URL 为 autoproxy.pac 文件的路径， 例如：file:///home/用户名/autoproxy.pac
