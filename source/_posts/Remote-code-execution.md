---
title: 远程代码执行
tags:
  - Web安全测试
categories:
  - 转载搬运
abbrlink: 3b22e763
date: 2019-03-02 17:49:22
copyright:
---

![](https://ae01.alicdn.com/kf/HTB1luqraqWs3KVjSZFxq6yWUXXaS.jpg)
<!--more-->

**原文：倾旋 https://payloads.online/archivers/2018-03-22/1**

## 0x00 远程代码执行-介绍 ##

### 什么是远程代码执行 ###

远程命令执行 英文名称：RCE (remote code execution) ，简称RCE漏洞，是指用户通过浏览器提交执行命令，由于服务器端没有针对执行函数做过滤，导致在没有指定绝对路径的情况下就执行命令，可能会允许攻击者通过改变 $PATH 或程序执行环境的其他方面来执行一个恶意构造的代码。

### 远程代码执行的特点 ###

远程代码执行是指攻击者可能会通过远调用的方式来攻击或控制计算机设备，无论该设备在哪里。

## 0x01 远程代码执行-风险等级 ##

** `高` ** 

## 0x02 远程代码执行-原理 ##

由于开发人员编写源码，没有针对代码中可执行的特殊函数入口做过滤，导致客户端可以提交恶意构造语句提交，并交由服务器端执行。命令注入攻击中WEB服务器没有过滤类似system(),eval()，exec()等函数是该漏洞攻击成功的最主要原因。

## 0x03 远程代码执行-常见场景 ##

### 使用了危险函数的Web应用 ###

### 低版本的Java语言Struts框架 ###

## 0x04 测试方案 ##

### PHP中常见场景-模板引擎代码执行 ###

**Smarty 简介**

> Smarty是一个PHP的模板引擎。更明确来说，它可以帮助开发者更好地 分离程序逻辑和页面显示。最好的例子，是当程序员和模板设计师是不同的两个角色的情况，而且 大部分时候都不是同一个人的情况。
>
> CVE-ID : CVE-2017-1000480

**产生原因：** 由于未对用户的输入点进行过滤，导致经过eval函数，造成代码执行。

**测试Payload：**

![0x01.png](https://ae01.alicdn.com/kf/HTB1vFCjaA9E3KVjSZFG76319XXap.png)

### Java Struts2(S2-045) ###

**Struts 简介**

>Struts2是一个基于MVC设计模式的Web应用框架，它本质上相当于一个servlet，在MVC设计模式中，Struts2作为控制器(Controller)来建立模型与视图的数据交互。Struts 2是Struts的下一代产品，是在 struts 1和WebWork的技术基础上进行了合并的全新的Struts 2框架。
>
> CVE-ID : CVE-2017-5638

**产生原因：**由于未对用户输入点进行过滤，被带入ErrorMessage，当做OGLN表达式解析，造成代码执行。

**测试Payload：**

![0x02.png](https://ae01.alicdn.com/kf/HTB18NChawmH3KVjSZKz7622OXXaW.png)

## 0x05 修复方案 ##

升级插件、框架新版本。