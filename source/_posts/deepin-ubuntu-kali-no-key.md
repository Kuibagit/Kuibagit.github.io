---
title: deepin-ubuntu-kali没有签名无法签证仓库源
tags:
  - 备忘录
  - kali
categories:
  - 技术水文
copyright: true
abbrlink: f1373c55
date: 2018-12-04 15:07:37
---

![](https://ae01.alicdn.com/kf/HTB1Laqbav1H3KVjSZFBq6zSMXXaq.jpg)
<!--more-->

## <font color=#1bcc7c size=>#</font> deepin/ubuntu/kali更换仓库源提示没有签名 ##

![](https://ae01.alicdn.com/kf/HTB1dUKaav1H3KVjSZFH762KppXaT.png)

通过以下命令添加公钥即可

```bash
$ sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 公钥后八位
```

![](https://ae01.alicdn.com/kf/HTB1Z1KiaBGw3KVjSZFD760WEpXa5.png)

再重新执行 **apt-get update** 获取软件更新列表

![](https://ae01.alicdn.com/kf/HTB1KV9baxiH3KVjSZPf760BiVXai.png)