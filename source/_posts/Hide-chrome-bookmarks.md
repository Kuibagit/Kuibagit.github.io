---
title: 隐藏Chrome书签栏
tags:
  - Chrome
categories:
  - 杂项
copyright: true
abbrlink: 3ca0a42e
date: 2020-01-11 17:20:51
---

![](https://ae01.alicdn.com/kf/H7b8f123b3b7c429ea12026ac5ba07e60Y.jpg)
<!--more-->

### 隐藏Chrome顶部的书签栏有方式###

- 1、使用官方说的：`Ctrl+Shift+B`
- 2、Chrome扩展
- 3、修改注册表

对于第1、2两种没什么可说的，只是“临时性”隐藏，且，只对正在浏览页面标签页有效，打开新的标签页还是可以看到，治标不治本。

比较治本的就是第3种--修改注册表

### 操作 ###

打开注册表 `HKEY_LOCAL_MACHINE\SOFTWARE\Policies` 项，新建一个 `Google`项，然后其再创建子项 `Chrome`，最后创建一个名为 `BookmarkBarEnabled` 的 DWORD(32位值)，数值默认为0就行，重新打开Chrome即可。

### END ###

书签栏隐藏后

- 可以在Chrome的菜单选项进入书签（不是很方便，但可以接受）
- 菜单选项会多出一个【由贵单位管理】