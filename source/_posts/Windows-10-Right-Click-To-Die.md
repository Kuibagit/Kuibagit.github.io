---
title: windows 10 右键卡死
tags:
  - 备忘录
  - win10
categories:
  - 杂项
copyright: true
abbrlink: 19398ee8
date: 2018-12-03 01:29:45
---

![](https://ae01.alicdn.com/kf/HTB1dsejaEGF3KVjSZFvq6z_nXXac.jpg)
<!--more-->

### 前言 ###

> 今天自己的电脑不知道怎么回事，右键打开所有的文件都打开不了，电脑直接卡死并且弹出【此应用无法在你的电脑上运行】，What？？

![S](https://ae01.alicdn.com/kf/HTB1mD9oaBKw3KVjSZFOq6yrDVXaY.jpg)

> 我打开个txt都打不开了？直接双击打开又可以正常打开，而右键却不行。起初我还以为电脑中病毒了，于是度娘一波，然而找到的都是些某某软件打不开之类的，说什么修改软件属性，以兼容模式运行啥的。但是我的不是应用打不开啊，而是所有文档类的右键打不开，右键就直接卡死。难道还要我重装系统不成？重装，那是不存在的！捣搞了好半天才发现原来是【Adobe】惹的祸

![](https://ae01.alicdn.com/kf/HTB1O_9oaBKw3KVjSZFO761rDVXaV.png)

> Adobe会在右键菜单生产快捷方式，而且Adobe有个插件老是开机自启，禁都禁不掉，但是在几天前我就把那插件改了个名字，用个txt文件替换了它。鼠标右键文件时，电脑找不到Adobe的索引加载不出来，所以电脑会卡死并且会弹出【此应用无法在你的电脑上运行】的提示。于是乎才有了此文。

![](https://ae01.alicdn.com/kf/HTB1KM5kaBWD3KVjSZFs763qkpXaj.png)

### 解决方法 ###

ps：Adobe的插件我是不想让它重新启用的，所以继续使用txt文本替换那个插件【改成exe尾缀】

打开安全软件，360、电脑管家之类的【我用的是火绒】，然后找到右键管理，把Adobe的选项给去掉即可。

![](https://ae01.alicdn.com/kf/HTB10NCkaBWD3KVjSZKP761p7FXaS.png)

**去掉Adobe选项**

![](https://ae01.alicdn.com/kf/HTB1t91raq1s3KVjSZFA760_ZXXaW.png)
