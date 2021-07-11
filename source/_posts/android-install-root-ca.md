---
title: Android安装根证书到系统
abbrlink: 84477c27
date: 2019-05-16 09:40:08
tags:
    - CA证书
    - Burpsuite
categories:
  - 技术水文
copyright: true
---

![](https://ae01.alicdn.com/kf/HTB13y5aaEWF3KVjSZPhq6xclXXa2.jpg)
<!--more-->

update：2021-06-26 

1、如还是无法抓到数据包，则安装低版本微信(v7.0.22)或许可行。

2、现在openssl 版本基本是大于1.0版本的，所以文件名应该为： `9a5ba575.0` 。

## 前言 ##

最近公司有小程序要测试，Burpsuite无法抓到数据包，https证书已经安装了，还是无法捉到数据包。
Google找到一下文章 [安卓微信7.0不能抓 https](https://testerhome.com/topics/17746 "安卓微信 7.0 不能抓 https")。

微信不再信任Android 7及之后用户安装的证书，可是我用的是Android 5.1的咋也不能正常捉包呢，不管了。手机刚好有root权限，那就把证书安到系统下试试。

## 正文 ##

步骤分为：
1、从Brupsuite中导出证书文件 `cert.der`
2、使用Openssl生成pem文件(这里我把ca证书复制到kali操作，Windows可安装 [Git Bahs](https://git-scm.com/downloads)，自带Openssl)
3、读取取ca证书hash值
4、重命名pem文件

Openssl 版本小于于 1.0 使用以下命令：

```bash
openssl x509 -in cert.der -inform der -outform pem -out cert.pem

openssl x509 -inform PEM -subject_hash -in cert.pem | head -1

cat cert.pem > 7bf17d07.0

openssl x509 -inform PEM -text -in cert.pem -out /dev/null >> 7bf17d07.0
```

![](https://ae01.alicdn.com/kf/HTB1ABOXaxiH3KVjSZPf760BiVXaO.png)

Openssl 版本大于 1.0 使用以下命令：

```bash
openssl x509 -inform DER -in cacert.der -out cacert.pem

openssl x509 -inform PEM -subject_hash_old -in cacert.pem | head -1

mv cacert.pem 9a5ba575.0
```

![](https://s1.ax1x.com/2020/04/30/JqiW5R.png)

把`7bf17d07.0` 或 `9a5ba575.0`  传到到手机上，

然后再复制到 `/system/etc/security/cacerts/` 目录下(以下使用RE文件管理器进行操作)，并修改其权限为`644`

![](https://ae01.alicdn.com/kf/HTB1qZ9caBWD3KVjSZKP761p7FXaP.png)

重启手机，ca证书已经安装到系统下，此时就可以正常捉到小程序的数据包了。

![](https://ae01.alicdn.com/kf/HTB1pRCXaxiH3KVjSZPf760BiVXaK.png)

![2021-06-26_22-29-16.png](https://i.loli.net/2021/07/11/N8L7qRwxElK94Je.png)



**Reference**

https://testerhome.com/topics/17746
https://www.codercto.com/a/71371.html
http://www.gtopia.org/blog/2010/02/der-vs-crt-vs-cer-vs-pem-certificates/