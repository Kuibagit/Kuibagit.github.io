---
title: Windows任意文件读取0day复现
tags:
  - windows
  - vuln
categories:
  - 漏洞复现
copyright: true
abbrlink: db84c847
date: 2018-12-28 22:53:51
---

![](https://ae01.alicdn.com/kf/HTB103ykaBaE3KVjSZLeq6xsSFXaX.jpg)
<!--more-->

> <font color=#1bcc7c size=7>前言</font>

>最近国外安全研究员 SandboxEscaper又一次在推特上公布了新的Windows 0 day漏洞细节及PoC。此次披露的漏洞可造成任意文件读取。该漏洞可允许低权限用户或恶意程序读取目标Windows主机上任意文件的内容，但不可对文件进行写入操作。所有windows系列都将受此漏洞影响。

一个普通用户 `read` 正常情况下是不能访问其他用户的家目录的。

![](https://ae01.alicdn.com/kf/HTB10MykaBaE3KVjSZLe760sSFXaZ.png)

以下使用 `SandboxEscaper` 大牛公布的PoC可以绕过并成功读取另外一个用户在桌面上的文件。

![](https://ae01.alicdn.com/kf/HTB1puysarys3KVjSZFn760FzpXah.png)

成功读取txt文件

![](https://ae01.alicdn.com/kf/HTB10wykaBaE3KVjSZLe760sSFXaX.png)

大牛的博客以及PoC地址：
>https://sandboxescaper.blogspot.com/2018/12/readfile-0day.html

复现参考链接：
>https://thehackernews.com/2018/12/windows-zero-day-exploit.html