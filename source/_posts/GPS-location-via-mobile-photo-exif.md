---
title: 记一次失败的手机图片定位
tags:
  - 学习
  - Python
categories:
  - 编程语言
copyright: true
abbrlink: f8cabab2
date: 2019-05-11 22:24:06
---

![](https://ae01.alicdn.com/kf/HTB1Cb5jaBGw3KVjSZFDq6xWEpXak.jpg)
<!--more-->

## 前言 ##

“之前”网上有条消息(好像是《社会工程：安全体系中的人性漏洞》一书里面的，网上也有相关的例子)--可以从一张图片定位出该图片的拍摄地点，从而知道“你”曾经去过的一些地方。

## 正文 ##

网上已经有蛮多的例子了，以下我也是网上找的代码，然后进行改改组合起来，代码渣渣，大佬勿喷。

### 准备阶段 ###

**百度地图AK**

**手机原图照片(手机开启GPS且相机设置记录位置信息拍的照片)**

步骤：

使用python的`exifread`模块进行读取照片的exif信息，然后把里面的经纬度，拍照手机等信息提取出来。
提取出来后再格式化转换一下经纬度，然后调用百度度API进行查询，最后提取【省、市、区、街道】(格式化)等信息


代码如下：

```python
# -*-coding: utf-8-*-
# _Author: Kuibahd 
# Time：2019/5/10
from __future__ import division
import exifread, requests, json, re


# 提取img信息并获取拍照地点
def imageread():
    f = open("D:\\Pychram\\img\\IMG_20190510_201621.jpg", 'rb')
    imagetext = exifread.process_file(f)
    # for key in imagetext:
    #     print(key, ":", imagetext[key])
    f.close()
    make = ''
    ns = ''
    ms = ''
    for q in imagetext:
        if q == "Image Make":
            make = imagetext[q]
        elif q == "Image Model":
            model = imagetext[q]
            print("拍摄图片手机：", str(make) + ' ' + str(model))
        elif q == "Image DateTime":
            print("图片拍摄时间：", imagetext[q])
        elif q == "GPS GPSLongitude":
            ns = imagetext[q]
            # print("GPS经度：", imagetext[q], imagetext['GPS GPSLatitudeRef'])
            print("GPS经度：", imagetext[q])
        elif q == "GPS GPSLatitude":
            ms = imagetext[q]
            # print("GPS纬度：", imagetext[q], imagetext['GPS GPSLongitudeRef'])
            print("GPS纬度：", imagetext[q])

    # 下面转换有些问题导致最后定位偏离了
    strs1 = str(ns)  # 经度
    strs2 = str(ms)  # 纬度
    # 格式化经度
    e1 = strs1[:4] + "°" + strs1[6:]
    e2 = e1[:6] + "′" + e1[8:]
    e3 = e2[1:9] + "″"
    # print(e3)

    # 转换经度
    x1 = re.split("°|′|″", e3)[:3]
    x1 = list(map(int, x1))
    # print(x1[0] + x1[1] / 60 + x1[2] / 3600)
    lng = str(x1[0] + x1[1] / 60 + x1[2] / 3600)

    # 格式纬度
    n1 = strs2[:3] + "°" + strs2[5:]
    n2 = n1[:6] + "′" + n1[7:]
    n3 = n2[1:9] + "″"
    # print(n3)

    # 转换纬度
    x2 = re.split("°|′|″", n3)[:3]
    x2 = list(map(int, x2))
    # print(x2[0] + x2[1] / 60 + x2[2] / 3600)
    lat = str(x2[0] + x2[1] / 60 + x2[2] / 3600)

    # 根据经纬度获取地址
    ak = "XXXXXXXXXX"  # 百度map AK
    url = "http://api.map.baidu.com/geocoder/v2/"
    items = "?callback=renderReverse&location={},{}&output=json&pois=1&ak={}".format(lat, lng, ak)
    req = requests.get(url + items, timeout=2)
    # print(items)
    data = req.text

    # 提取信息
    data = data.replace("renderReverse&&renderReverse(", "")
    data = data[:-1]
    baiduAddr = json.loads(data)
    province = baiduAddr["result"]["addressComponent"]["province"]
    city = baiduAddr["result"]["addressComponent"]["city"]
    district = baiduAddr["result"]["addressComponent"]["district"]
    street = baiduAddr["result"]["addressComponent"]["street"]
    formatted_address = baiduAddr["result"]["formatted_address"]
    new_line = street + "|" + formatted_address + "|" + str(lng) + "|" + str(
        lat) + "|" + province + "|" + city + "|" + district
    print("*****************************************\n拍摄图片位置：")
    print(new_line)


if __name__ == '__main__':
    imageread()

```

最终结果：

![](https://ae01.alicdn.com/kf/HTB1bSOiaBKw3KVjSZTE763uRpXaZ.png)

## END ##

由于经纬度格式化、转换那里不是很懂，最后的定位结果(都)是偏离。。。。

![](https://ae01.alicdn.com/kf/HTB1TfqcavWG3KVjSZFg762TspXaQ.png)

**Reference**

http://www.cnblogs.com/shiguangliangchunshanbo/p/9745848.html
https://blog.csdn.net/oYeZhou/article/details/82012471
https://blog.csdn.net/ChenVast/article/details/81669788
https://blog.csdn.net/tyt_XiaoTao/article/details/80410279
https://www.zhihu.com/question/27043850
https://www.cnblogs.com/shijingjing07/p/7474570.html