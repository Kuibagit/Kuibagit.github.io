---
title: 初次使用BeautifulSoup(bs4)小记
date: 2019-05-14 09:49:19
tags:
  - 学习
  - Python
  - 爬虫
categories:
  - 编程语言
copyright: true
abbrlink: f939aff4
---

![](https://ae01.alicdn.com/kf/HTB1rJGdaBGE3KVjSZFhq6AkaFXaN.jpg)
<!--more-->
<div class="yinyue" style="text-align:center"><iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="//music.163.com/outchain/player?type=2&id=1358800103&auto=1&height=66"></iframe></div>

## Foreword ##

某天，逛到了某社区一帖子，发现[帖子](http://hongdou.gxnews.com.cn/viewthread-16250447-1.html)里的妹子图片，嗯还蛮不错的。帖子有7页，每页20张图，要是每张都手动下载，那岂不“累死了”？不存在的，祭出py download it。

## Main body ##

初次使用BeautifulSoup,代码不入眼，大佬勿喷~

大体步骤：
    1、获取7个页面url
    2、获取单个页面里所有图片的url
    3、下载所有图片
    4、提取文件名并入库保存图片

代码如下：

```python
# -*-coding: utf-8-*-
# _Author: Kuibahd
# _Time: 2019/5/14

import requests, os, time
from bs4 import BeautifulSoup


def get_image():
    for x in range(1, 7):
        url = "http://hongdou.gxnews.com.cn/viewthread-16250447-" + str(x) + ".html"
        print(url)
        header = {
            'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/50.0.2661.102 UBrowser/6.1.2107.204 Safari/537.36'
        }
        html = requests.get(url, headers=header)
        soup = BeautifulSoup(html.text, 'lxml')
        all_src = soup.find_all('div', class_='viewmessage')
        # print(all_src[0].img['data-original'])
        i2 = len(all_src)
        for i in range(0, i2):
            # print(all_src[i].img['data-original'])
            img_url = all_src[i].img['data-original']
            html = requests.get(img_url, headers=header)
            # Extract the file name from the url
            file_name = os.path.basename(img_url)
            fp = open('./image/%s' % file_name, 'wb')
            fp.write(html.content)
            fp.close()


if __name__ == '__main__':
    print("************************\n[*] 正在下载图片...")
    start = time.time()
    get_image()
    end = time.time()
    print("下载完成，共耗时：%f s" % (end - start))

```

![](https://ae01.alicdn.com/kf/HTB1dM9iaBKw3KVjSZTE763uRpXav.png)

![](https://ae01.alicdn.com/kf/HTB1LCN2Xkxz61VjSZFt761DSVXan.png)

## END ##

单线程+300k左右的网速下载居然花了十多分钟。。。。。多线程后面再说吧^><^