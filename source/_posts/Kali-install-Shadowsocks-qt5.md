---
title: kali安装shadowsocks-qt5
tags:
  - 备忘录
  - kali
  - shadowsocks-qt5
categories:
  - 技术水文
copyright: true
abbrlink: 3dd71f61
date: 2018-07-26 01:38:31
update: 2018-12-31 20:00:00
---

![](https://ae01.alicdn.com/kf/HTB1qUX.awaH3KVjSZFjq6AFWpXar.jpg)
<!--more-->
### 先安装依赖包 ###

```bash
$ sudo apt-get install qt5-qmake qtbase5-dev libqrencode-dev libappindicator-dev libzbar-dev
```

如出现错误，在后面加入 `--fix-missing` 来修正错误


```bash
$ sudo apt-get install qt5-qmake qtbase5-dev libqrencode-dev libappindicator-dev libzbar-dev --fix-missing
```

### 下载deb包 ###

到GitHub下载五个deb包[github](https://github.com/try1try/shadowsocks-qt5)

![](https://ae01.alicdn.com/kf/HTB1mTGaaBaE3KVjSZLe760sSFXaS.png)

下载完后解压并在终端cd到deb包存放的目录下

![](https://ae01.alicdn.com/kf/HTB1M_8.awaH3KVjSZFj763FWpXaX.png)

然后安装**gdebi**（这里我已经安装过了）

![](https://ae01.alicdn.com/kf/HTB1TOCbaCWD3KVjSZSg763CxVXan.png)

然后分别执行 **gdebi** 刚才下载的5个deb包(以`root`权限执行)

```bash
gdebi libssl1.0.0_1.0.1t-1+deb8u7_amd64.deb

gdebi libbotan-1.10-0_1.10.8-2+deb8u1_amd64.deb

gdebi libqtshadowsocks_1.10.0-1_amd64.deb

gdebi libqtshadowsocks-dev_1.10.0-1_amd64.deb

gdebi shadowsocks-qt5_2.8.0-1_amd64.deb
```

![](https://ae01.alicdn.com/kf/HTB1UTKaaBaE3KVjSZLe760sSFXaB.png)

### 在应用找到小飞机并启动它 ###

![](https://ae01.alicdn.com/kf/HTB1fmGXaECF3KVjSZJn762nHFXap.png)

配置小飞机相关参数即可

![](https://ae01.alicdn.com/kf/HTB15_afaBCw3KVjSZFl763JkFXa0.png)

<font color=red size=>更新：2018-10-22</font>

### 另一种安装方式，是通过添加deepin的源来安装 ###

编辑/etc/apt/sources.list文件(需要使用sudo), 在文件最前面添加以下条目(操作前请做好相应备份)

```bash
deb [by-hash=force] http://mirrors.aliyun.com/deepin panda main contrib non-free
```

然后 apt-get update 更新下列表

再执行以下命令进行安装

```bash
$ sudo apt-get install shadowsocks-qt5
```

<font color=red size=>更新：2019-05-27</font>

### 使用github上纸飞机项目给出的可执行程序 ###

download：`https://github.com/shadowsocks/shadowsocks-qt5/releases`

```bash
sudo chmod +x Shadowsocks-Qt5-3.0.1-x86_64.AppImage
./Shadowsocks-Qt5-3.0.1-x86_64.AppImage
```

![](https://ae01.alicdn.com/kf/HTB1Vs9aaBWD3KVjSZKP761p7FXa0.png)