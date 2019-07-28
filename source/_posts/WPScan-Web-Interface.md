---
title: WPScan Web Interface
tags:
  - tools
  - wpscan
categories:
  - 杂项
copyright: true
abbrlink: 1dd3590f
date: 2019-01-11 15:19:49
---

![](https://ae01.alicdn.com/kf/HTB1NR5jaECF3KVjSZJnq6znHFXa0.jpg)
<!--more-->
该工具作者对 WPScan Web Interface 功能描述如下：

> 用于运行和调度由wpscan实用程序提供支持的WordPress扫描的集中式仪表板。它具有以下功能：
> **1、登录页面 -**
>访问应用程序需要身份验证。
>**2、仪表板页面 -**
>按需扫描：通过提供URL或文本文件立即运行扫描，该URL具有由新行分隔的多个URL作为输入。
>扫描历史记录：查看或删除扫描历史记录和报告。
>计划扫描：将扫描配置为自动运行，或者像Linux中的cron作业一样定期运行。
>预设扫描历史记录：编辑cron规则或删除任何预定扫描。
>**3、报告页面-**
>查看或打印扫描完成后发现的漏洞的详细信息。

详细请转到 https://github.com/cyc10n3/WPScan_Web_Interface/blob/master/README.md

以下是我安装环境：

```bash
OS：Parrot x86_64 GNU/Linux（基于Debian）
nodejs：v8.11.2
npm：5.8.0
```

![](https://ae01.alicdn.com/kf/HTB1KJl_Xkxz61VjSZFr760eLFXaS.png)

由于我所用的系统没有 `npm` 所以执行以下命令进行安装：

```bash
$ sudo apt-get install npm
```

使用Git下载 `WPScan Web Interface` 到本地进行安装

```bash
$ git clone https://github.com/cyc10n3/WPScan_Web_Interface.git

$ cd WPScan_Web_Interface

$ sudo npm install

$ sudo npm start
```

浏览器打开 https://localhost:1337，默认用户密码为：`admin/cyc10n3` 可在该工具文件夹下的`config.json` 修改密码。


![](https://ae01.alicdn.com/kf/HTB14TWpaBCw3KVjSZFl763JkFXaC.png)

**登录页面**

![](https://ae01.alicdn.com/kf/HTB1gDWpaBCw3KVjSZFl763JkFXak.png)

**主页面--Scan**

![](https://ae01.alicdn.com/kf/HTB15mOiav1H3KVjSZFH762KppXa7.png)

**定时扫描**

![](https://ae01.alicdn.com/kf/HTB1OLSsarus3KVjSZKb760qkFXaS.png)

**扫描结果**

![](https://ae01.alicdn.com/kf/HTB1tKekaA9E3KVjSZFG76319XXai.png)

**后台状态**

![](https://ae01.alicdn.com/kf/HTB16FmjawaH3KVjSZFj763FWpXao.png)
