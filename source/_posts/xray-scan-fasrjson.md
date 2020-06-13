---
title: 白嫖Xray批量检测内网Fastjson
tags:
  - Xray
  - Fastjson
categories:
  - 技术水文
copyright: true
abbrlink: 20516be2
date: 2020-06-13 13:14:03
---

![](https://s1.ax1x.com/2020/06/13/tjv4Z8.jpg)
<!--more-->

> 以下简文出自于K师傅手

### 白嫖xray高级版

最近又双叒叕白嫖到了xray的高级版，刚好高级版支持fastjson扫描，刚好解决了我最近工作问题（批量检测内网fastjson）。

![](https://s1.ax1x.com/2020/06/13/tjjApR.jpg)

![](https://s1.ax1x.com/2020/06/13/tjjFh9.jpg)

本次要用到xray高级版，xray反连平台

### 配置xray反连平台

首先先把xray反连平台设置好，将listen_ip修改为本地ip

![image.png](https://s1.ax1x.com/2020/06/13/tjOMDO.png)

### 把当前xray所有的文件都复制到另一个xray2文件

![image.png](https://s1.ax1x.com/2020/06/13/tjOmgx.png)

### 启动反连平台的xray

```
xray_windows_amd64.exe reverse
```

![image.png](https://s1.ax1x.com/2020/06/13/tjOnv6.png)

### 接着再启动批量扫描的xray

```
xray_windows_amd64.exe webscan --plugins fastjson --url-file url.txt --html-output test1.html
```

![image.png](https://s1.ax1x.com/2020/06/13/tjOKKK.png)

### OK,扫描完成

本次我主要是检测内网的fastjson漏洞，不涉及公网的，所以我的配置都是按照内网的设置来的，如果需要扫描公网的自行配制公网的设置就可以了。
