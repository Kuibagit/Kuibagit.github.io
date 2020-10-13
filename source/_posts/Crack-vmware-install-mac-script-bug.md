---
title: 破解VMware安装Mac脚本中的Bug
tags:
  - Cracked
  - Bug
categories:
  - 技术水文
copyright: true
abbrlink: 84701d9e
date: 2020-06-26 13:51:49
---

![](https://s1.ax1x.com/2020/06/27/N6gz9g.md.png)
<!--more-->
删除或注释 `win-install.cmd` line 9~23

```bash
chcp 850

net session >NUL 2>&1
if %errorlevel% neq 0 (
    echo Administrator privileges required! 
    exit /b
)

echo.
set KeyName="HKLM\SOFTWARE\Wow6432Node\VMware, Inc.\VMware Player"
:: delims is a TAB followed by a space
for /F "tokens=2* delims=	 " %%A in ('REG QUERY %KeyName% /v InstallPath') do set InstallPath=%%B
echo VMware is installed at: %InstallPath%
for /F "tokens=2* delims=	 " %%A in ('REG QUERY %KeyName% /v ProductVersion') do set ProductVersion=%%B
echo VMware product version: %ProductVersion%

```

在line 24 添加 `set InstallPath=VMware安装路径`

```bash
set InstallPath=C:\Program Files (x86)\VMware\VMware Workstation\
```

在line 47、line 51改为调用python脚本破解：

```
python3 unlocker.py
python3 gettools.py
```

在line 29~line 32中的VMware服务名字是错误的，需要修改正确服务名并加上双引号

```bash
net stop "VMwareHostd" > NUL 2>&1
net stop "VMAuthdService" > NUL 2>&1
net stop "VMnetDHCP" > NUL 2>&1
net stop "VMUSBArbService" > NUL 2>&1
net stop "VMware NAT Service" > NUL 2>&1
```

修改line 57~line 60：

```bash
net start "VMware NAT Service" > NUL 2>&1
net start "VMnetDHCP" > NUL 2>&1
net start "VMAuthdService" > NUL 2>&1
net start "VMUSBArbService" > NUL 2>&1
net start "VMwareHostd" > NUL 2>&1
```

去除破解的 `win-uninstall.cmd` 也类似，修改相应的地方即可。

管理员权限运行 `win-install.cmd` 破解VMware

![](https://s1.ax1x.com/2020/06/27/N6gv4S.md.png)

![](https://s1.ax1x.com/2020/06/27/N6gXAf.png)

注:

多次运行`win-install.cmd`，多次运行容易损坏VMware许可信息，损坏后需要卸载VMware再重新安装。

**Reference**

https://zhuanlan.zhihu.com/p/102534602