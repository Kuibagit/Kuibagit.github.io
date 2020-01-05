---
title: Windows privileged
tags:
  - windows
  - vuln
categories:
  - 漏洞复现
copyright: true
abbrlink: 1143e85d
date: 2019-08-25 08:30:16
---

![](https://ae01.alicdn.com/kf/H3d8d7218658f418d9326f5b2147f9119C.png)
<!--more--->
### Foreword ###

很长一段时间没关注tw了，翻了翻推文，发现一则【windows提权大杀器】的推文？慢悠悠的点击看了推文与文中的截图： `ctftool.exe` 第一眼看到ctf字样，脑中闪现：CTF比赛的产物？

![](https://ae01.alicdn.com/kf/H3d8d7218658f418d9326f5b2147f9119C.png)

后面翻了翻这“大杀器”作者的blog[文章](https://googleprojectzero.blogspot.com/2019/08/down-rabbit-hole.html)，CTF疑似微软的某种协议简写。(Google搜了一遍，没找到CTF协议具体是什么，咱啥也没明白，啥也不敢问，只是知道该协议有漏洞，可被利用进行提权)

### ;D ###

复现利用

`https://github.com/taviso/ctftool`

按作者readme中的描述，他是使用 vs 2019 与 GUI make 构建的 `ctftool` 。这两样自己机子都装有，vs 2017版应该也可以吧，然后就屁颠屁颠的去下载源码尝试自己构建。一构建一大推的报错，然后，然后，然后瞬间放弃直接del了，还是去下载作者编译好的吧。。。。。

运行 `ctftoll.exe` 执行脚本

![](https://ae01.alicdn.com/kf/H41f72b7ba8e24bf38dd0a552cb71a5286.png)

后面重复测试，发现自安全软件已加入指纹库了。。。。。

![](https://ae01.alicdn.com/kf/H2498e3e2f18347728ea51e49ac6fa85aB.png)

### Video ###

<div class="aspect-ratio"><iframe src="//player.bilibili.com/player.html?aid=65238139&cid=113228058&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"></iframe></div>

**Reference**

https://github.com/taviso/ctftool

https://googleprojectzero.blogspot.com/2019/08/down-rabbit-hole.html
