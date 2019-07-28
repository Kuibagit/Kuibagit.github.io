---
title: 用Python给(blog)图片加上自己的水印
tags:
  - 学习
  - Python
categories:
  - 编程语言
copyright: true
abbrlink: ba9aa5f5
date: 2019-07-02 22:42:54
---

![](https://s2.ax1x.com/2019/07/02/ZYmNqI.png)
<!--more-->

原文：**州的先生** 使用Python编写批量添加图片水印程序：一、代码方案 [https://zmister.com/archives/990.html](https://zmister.com/archives/990.html)

### 准备 ###

1、备好自己要加的水印图片，可以用PS制作或者网上找自己喜欢的。

2、待加水印图片

3、编写以下代码

```python
import os, traceback
# from operator import lt

from PIL import Image


# 获取文件夹图片
def get_folder(fpath, wm_file, save_path):
    try:
        img_suffix_list = ['png', 'jpg', 'bmp']
        for i in os.listdir(fpath):
            if i.split('.')[-1] in img_suffix_list:
                img_path = fpath + '/' + i
                img_water_mark(img_file=img_path, wm_file=wm_file, save_path=save_path)
    except Exception as e:
        print(traceback.print_exc())


# 图片添加水印
def img_water_mark(img_file, wm_file, save_path):
    try:
        img = Image.open(img_file)  # 打开图片
        watermark = Image.open(wm_file)  # 打开水印
        img_size = img.size
        wm_size = watermark.size
        # print(wm_size)

        # 如果图片大小小于水印大小
        # if img_size[0] & lt & wm_size[0]:
        watermark.resize(tuple(map(lambda x: int(x * 0.5), watermark.size)))
        print('图片大小：', img_size)
        wm_position = (img_size[0] - wm_size[0], img_size[1] - wm_size[1])  # 默认设定水印位置为右下角
        layer = Image.new('RGBA', img.size)  # 新建一个图层
        layer.paste(watermark, wm_position)  # 将水印图片添加到图层上
        mark_img = Image.composite(layer, img, layer)
        new_file_name = '/new_' + img_file.split('/')[-1]
        mark_img.save(save_path + new_file_name)
    except Exception as e:
        print(traceback.print_exc())


if __name__ == '__main__':
    print("正在合成，请稍等......")
    get_folder(fpath=r"D:\Pychram\blogo\sourceimg", wm_file=r"D:\Pychram\blogo\save.png", save_path=r"D:\Pychram\blogo\sava")
    print("合成完成~")
```

加上水印后

![](https://s2.ax1x.com/2019/07/02/ZYmWd0.png)

### END ###

原作者写的代码中，是有个判断图片是否小于水印图片，这里我把其注释了，因为在自己的环境上代码跑不起来。。。。。