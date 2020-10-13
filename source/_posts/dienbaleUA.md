---
title: 禁用联软准入助手&卡巴斯基
tags:
  - Cracked
  - Something
categories:
  - 技术水文
copyright: true
date: 2020-07-25 13:52:51
---

![](https://s1.ax1x.com/2020/10/14/04356A.md.jpg)

联软准入助手太恶心，装上了它就被监控着了，而且打开一些非“正规”授权的软件都会遭受它的拦截。卡巴斯基则是把电脑中“不安全”的软件、脚本杀得片甲不留，那个心疼啊 ..@_@.. 需要把它们干掉才行！

![](https://s1.ax1x.com/2020/10/14/041IaT.png)

### 前置条件 ###

PE启动盘一个

### Start ###

进PE，使用PE注册表加载系统注册表文件（不懂请自行Google），名称随意，修改前注意备份。

**联软准入助手**

注册表文件：`C:\Windows\System32\config\SYSTEM`

![](https://s1.ax1x.com/2020/10/14/041bRJ.png)

修改 `UniAccessAgent`、`UniAccessAgentDaemon`、`UniKInject` 项的 `Start` 为手动或者禁用，即3或者4：

![](https://s1.ax1x.com/2020/10/14/041HG4.png)

**卡巴斯基**

修改 `AVP`、`avpsus`、`klnagent` 项

![](https://s1.ax1x.com/2020/10/14/041oIU.png)

修改完，菜单点击文件－卸载配置单元，重启电脑即可。

![](https://s1.ax1x.com/2020/10/14/0417iF.png)

