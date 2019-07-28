---
title: NNCTF-2018(部分)
tags:
  - CTF-WP
categories:
  - 技术水文
copyright: true
abbrlink: 1137788d
date: 2018-12-20 14:06:33
---

![](https://ae01.alicdn.com/kf/HTB1zQOhav1G3KVjSZFkq6yK4XXaK.jpg)
<!--more-->

## WEB: ##

### 超简单的web题 ###

直接截断拿到flag

```
http://gxnnctf.gxsosec.cn:12311/?no=1%00-
```

![](https://ae01.alicdn.com/kf/HTB1y.mkaBiE3KVjSZFM762QhVXa5.png)

### Sqli??? ###

看看这边`/.git/`返回403 感觉存在`git漏洞`

![](https://ae01.alicdn.com/kf/HTB1n1mjav1H3KVjSZFH762KppXaI.png)

随后用python恢复一下git文件恢复。

![](https://ae01.alicdn.com/kf/HTB1vWOkaEKF3KVjSZFE760ExFXau.png)
 
可以看到验证了git文件泄露的存在， 跑出来一个`index.php `

![](https://ae01.alicdn.com/kf/HTB1v_mjav5G3KVjSZPx762I3XXat.png)

对`index.php`代码审计

![](https://ae01.alicdn.com/kf/HTB1zOKqaBCw3KVjSZR0762cUpXay.png)

`if($username === 'guest'){    if($username === 'admin'){ `  
条件是两个查询 一个是`guest `一个是`admin` 可见可能是利用条件语句来查询数据库。

`if(preg_match('#sleep|benchmark|floor|rand|count|select|from|\(|\)|time|date|sec|day#is',$ip)){`

以上也可见过滤了括号等等。

Sql绕过

看源码感觉是第一次查询为`guest`第二次查询为`admin`
那我们是不是可以使用`mysql`的变量绕过这个，第一次查询变量值为`1`，第二次变量查询值为`2` ，可以想到使用`case when @x is null then @x:=2 ELSE @x:=1`。 
执行同样的语句，第一次为`guest`

![](https://ae01.alicdn.com/kf/HTB12r5kavWG3KVjSZPc762kbXXan.png)

第二次变量赋值为`x=1`

![](https://ae01.alicdn.com/kf/HTB1ljClaCSD3KVjSZFK76210VXal.png)

```
http://gxnnctf.gxsosec.cn:12312/index.php?backdoor=Melonrind&id=case%20when%20@x%20IS%20NULL%20THEN%20@x:=2%20ELSE%20@x:=1%20end 
```
![](https://ae01.alicdn.com/kf/HTB1kJekaEGF3KVjSZFm762qPXXal.png)

## 白帽子商城 ##

分析：注册新账号，初始金额1000刀，要购买帽子，一级帽子500刀，购买成功则提升一级，帽子的价格也随之上调1$.购买第一个帽子后，就发现余额不足以继续购买了.

![](https://ae01.alicdn.com/kf/HTB1kK1kaECF3KVjSZJn762nHFXaD.png)

重新注册个新账号，尝试条件竞争，打开burp，点击购买，抓取数据

![](https://ae01.alicdn.com/kf/HTB18EKjavWG3KVjSZFg762TspXax.png)

发送到Intruder，点击Positions，点击clea，点击Payload，选择类型为Null，调大线程和发送次数

![](https://ae01.alicdn.com/kf/HTB1.ZqlaA5E3KVjSZFC762uzXXaX.png)

![](https://ae01.alicdn.com/kf/HTB1uLOjaxiH3KVjSZPf760BiVXai.png)

发现竞争失败。

后面测试，发现修改【order】的参数可以免费购买帽子，但是始终绕不过购买第二个帽子
于是，猜想这里应该存在条件竞争，那再新注册个账号，修改数据先达到购买第二个帽子的页面，这里使用脚本来登录两个会话同时进行购买帽子

![](https://ae01.alicdn.com/kf/HTB1wJqkaEWF3KVjSZPh760clXXaI.png)

![](https://ae01.alicdn.com/kf/HTB1a6KlaCSD3KVjSZFK76210VXax.png)

使用脚本同时购买第二个帽子

![](https://ae01.alicdn.com/kf/HTB1Ob9laBGE3KVjSZFh763kaFXal.png)

刷新浏览器，已经get到flag

![](https://ae01.alicdn.com/kf/HTB127alaBOD3KVjSZFF763n9pXay.png)

## MISC: ##

### 这是啥 ###

尝试了栅栏密码和凯撒密码，发现有点像凯撒密码

![](https://ae01.alicdn.com/kf/HTB1xaGtaqWs3KVjSZFx761WUXXaF.png)

GFE-YLCERCNSNLA-AIX{N-PYNET-TSTMYRA}
把上面这一串分为6组6个字符串

```
GFE-YL
CERCNS
NLA-AI
X{N-PY
NET-TS
TMYRA} 
```

每组按照`143526`的顺序排序，最后拿到flag

```
GXNNCT F{LEEM ENATRY ----CR YPATNA LYISS}
```

### MISC2 ###

分析：

解压下载的压缩包，发现里面有`40个flag.zip`包，并且每个压缩包文件都有3个字节大小的名为`flag.txt`的文件。那就尝试`crc32`碰撞看看能不能还原压缩包里面的内容。

![](https://ae01.alicdn.com/kf/HTB1YmGjav5G3KVjSZPx762I3XXa0.png)

从网上搜索一波，看看有没有相似的，于是找到一遍文章`https://www.cnblogs.com/WangAoBo/p/6951160.html`，直接拿现成脚本代码来跑。

![](https://ae01.alicdn.com/kf/HTB1Q0ikaEWF3KVjSZPh760clXXaF.png)

这里坑啊，跑了半天什么都没跑出来。后来仔细审了下该脚本，脚本里的CrackCrc()有`4个for循环`，对应的应该是压缩包里的大小为`4`个字节的文件，但是现有的压缩包大小是`3`个字节，于是修改下代码去掉一个for循环， 最终脚本，如下：

![](https://ae01.alicdn.com/kf/HTB1VbytaqSs3KVjSZPi763siVXaU.png)

What，这是什么啊，新手的我一脸懵逼。后面baidu、Google了一波，原来这个是ASCII码，把ASCII转换成对应的字符串，然后再base64解码即可，

![](https://ae01.alicdn.com/kf/HTB1L5CqaBCw3KVjSZR0762cUpXah.png)

![](https://ae01.alicdn.com/kf/HTB1HcymaBSD3KVjSZFq7634bpXav.png)

### 太简单了 ###

解压压缩包，里面这有个没有尾缀名的`flag`文件，老规矩，`file`一下看看是什么文件，是个`zip`压缩包，直接加上为尾缀名，解压

![](https://ae01.alicdn.com/kf/HTB1Jn5laBWD3KVjSZKP761p7FXaW.png)

解压不了，提示文件损坏。直接扔winhex，发现文件头被改了，直接改回来

![](https://ae01.alicdn.com/kf/HTB1iSX_Xkxz61VjSZFr760eLFXaJ.png)

### Txt: ###

下载下来是一个txt文件，但是复制的时候好像有点问题

打开winhex下查看，发现隐藏了一些东西。

谷歌zero-width，找到原题：

`https://rawsec.ml/en/HackIT-2018-write-ups/`

![](https://ae01.alicdn.com/kf/HTB10ECkaBiE3KVjSZFM762QhVXao.png)

在wirteup部分找到解密的链接，复制文本里的内容直接解密得到flag

`https://offdev.net/demos/zwsp-steg-js`

![](https://ae01.alicdn.com/kf/HTB12ZmkaEGF3KVjSZFm762qPXXa7.png)

## Pwn ##

### format ###

> 格式化字符串盲打 通过printf的函数地址确定libc基址，从而获得system地址。
> send字符串“/bin/sh;”，那么在调用printf(“/bin/sh;”)的时候实际上调用的是system(“/bin/sh;”)，
> 通过格式化字符串漏洞达到任意读和任意写功能通过使其泄露信息从而实现目的。

![](https://ae01.alicdn.com/kf/HTB1aYOmaBSD3KVjSZFq7634bpXaT.png)

## MOBILE ##

### IOS_200 ##

> 题目描述：IOS送分题

压缩包下载下来解压，先file一下看看，发现里面的【UnlockMyIphone】是64位的app文件

![](https://ae01.alicdn.com/kf/HTB1Vy1kaEGF3KVjSZFv762_nXXaI.png)

直接扔进IDA，搜索一波flag，key之类的关键字，搜到一串私钥，仔细看，发现这是不需要密钥就可以解密的

![](https://ae01.alicdn.com/kf/HTB1wnmjav5G3KVjSZPx762I3XXaS.png)

私钥后面跟着两串类似加密的字符串，不管那多，把私钥和这两串文本复制出来先。

![](https://ae01.alicdn.com/kf/HTB19RR_Xkxz61VjSZFt761DSVXaJ.png)

百度 在线rsa加密解密，找到以下站点进行解密（这里好像有个小坑，百度出来的前三个站点解不了，不知什么情况）

![0x31.png](https://ae01.alicdn.com/kf/HTB1FHSqaBGw3KVjSZFw762Q2FXae.png)

## BASIC:###

### Her Majesty Queen Elizabeth II：###

```
FE&pd8dMFLR%)(DsGbhi@/dKPNR'*TUm?\tlr.7RV
```

直接base92解码拿到flag

![](https://ae01.alicdn.com/kf/HTB1HuyqaBKw3KVjSZFO761rDVXaT.png)

## 真—签到题：##

关注公众号拿flag

## Crypto: ##

### 维吉尼亚遇上困难：###

这一段应该是维吉尼亚密码

```
BZGTNPMMCGZFPUWJCUIGRWXPFNLHZCKOAPGLKYJNRAQFIUYRAVGNPANUMDQOAHMWTGJDXGOMPJPTKAAVZIUIWKVTUCWBWNFWDFUMPJWPMQGPTNWXTSDPLPMWJAXUHHXWPFXXGVAPFNTXVFKOYIRBOQJHCBVWVFYCGQFGUSUBDWVIYATJGTBNDKGHCTMTWIUEFJITVUGJHHIMUVJICUWYQWYGGUWPUUCWIFGWUANILKPHDKOSPJTTWJQOJHXLBJAPZHVQWPDYPGLLGDBCHTGIZCCMEGVIIJLIFFBHSMEGUJHRXBOQUBDNASPEUCWNGWSNWXTSDPLPMWJAIUHUMWPSYCTUWFBMIAMKVBNTDMQNBVDKILQSSDYVWVXIGDQFIBHSLEAVDBXGOLGDBCHTGIZVNFQFKTNGRWXUDCTGKWCOXIXKZPPFDZGXNBAXLGGWBLTLWCKOXAR
````

直接搜索找到解密网址

`https://guballa.de/vigenere-solver`

![](https://ae01.alicdn.com/kf/HTB14vKqaBKw3KVjSZTE763uRpXaK.png)

### Shamir重要数据损坏 ###

> 某集团总裁Shamir将自己使用的笔记本电脑上重要的秘密数据分割成5份子秘密数据，并分别存放在5个存储设备上，其中可以由至少3份子秘密数据联合参与运算，才能重构原来的秘密数据。分割方案使用的参数模数为5987。由于Shamir使用的笔记本电脑感染病毒致使该重要秘密数据损坏无法修复，于是Shamir让技术人员通过存放在编号为5、7、9的三个存储设备的子秘密数据进行重构重要秘密数据，其中编号5的存储设备存放的数据为（5,2258）、编号为7的存储设备存放的数据为（7,2424）、编号为9的存储设备存放的数据为（9,2630）。请问技术人员重构出来的重要秘密数据是多少？

```
	 1  5  25
A =	 1  7  49
	 1  9  81
```


由矩形对应

		a11 a12 a13
		a21 a22 a23
		a31 a32 a331

由矩阵A得`|A|=a11*a22*a33+a12*a23*a31+a13*a21*a32-a13*a22*a31-a12*a21*a33-a11*a23*a32=16`
计算出矩阵A的伴随矩阵

```
A11=a22 * a33 - a23 * a32=7*81-9*49=126
A12= -a21 * a33 + a23 * a31=-1*81+1*49=-32
A13= a21 * a32 - a22 * a31=1*9-1*7=2
A21= -a12 * a33 + a13 * a32=-5*81+25*9=-180
A22=a11*a33-a31*a13=1*81-1*25=56
A23=-a11*a32+a31*a13=-1*9+1*5=-4
A31=a12*a23-a22*a13=5*49-7*25=70
A32=-a11*a33+a21*13=-1*49+1*25=-24
A33=a11*a22-a21*a12=1*7-1*5=2
```

所以伴随矩阵得：

```
	A11  A21  A31
	A12  A22  A32
	A13  A23  A33

	A11  A21 A31         126/16     -180/16   70/16          7.875  -11.25   4.375
A*=16/1	A12  A22 A32    =    -32/16       56/16   -24/16   =   -2     3.5      -1.5
	A13  A23 A33          2/16       -4/16      2/16         0.125  -0.25    0.125

2258x7.875-2424x11.25+2530x4.375=2018 
```
