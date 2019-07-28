---
title: Middleware version information leaked
tags:
  - Web安全测试
categories:
  - 转载搬运
abbrlink: 5be9406d
date: 2019-03-05 09:13:45
copyright:
---

![](https://ae01.alicdn.com/kf/HTB1kfGiaBGE3KVjSZFh763kaFXa4.png)
<!--more-->

**原文：倾旋 https://payloads.online/archivers/2018-04-18/2**

## 0x00 中间件版本信息泄露 ##

### 什么是中间件版本信息泄露 ###

通常在HTTP报文的响应头中存在的标志、版本号等信息均属于中间件的版本信息泄露。

### 中间件版本信息泄露的特点 ###

通常出现在默认安装好的Web中间件上，大部分管理员都不会修改这个标志。

## 0x01 中间件版本信息泄露-风险等级 ##

** `低` **

## 0x02 中间件版本信息泄露-原理 ##

由于各大Web服务中间件为了打造品牌效应而在HTTP响应头中添加了标志信息、版本号。

## 0x03 中间件版本信息泄露-常见场景 ##

- Tomcat
- Nginx
- Apache
- IIs
- ...均有此类现象

## 0x04 测试方案 ##

使用CURL发送OPTIONS、GET、POST、HEAD等请求，查看响应头中的Server行。

```bash
curl -I -X GET http://192.168.33.133/mutillidae/
```

![](https://ae01.alicdn.com/kf/HTB1NxmnaBKw3KVjSZFO761rDVXa2.png)

## 0x05 修复方案 ##

### Apache ###

将以下配置加入 `conf/httpd.conf`：

```
ServerTokens Prod
ServerSignature Off
```

### PHP ###

修改php配置文件 `php.ini` 将 `expose_php On` 改为：`expose_php Off`。

### IIS ###

**移除Server**

借助IIS URL Rewrite Module，添加如下的重写规则。

```
<rewrite>
        <allowedServerVariables>
            <add name="REMOTE_ADDR" />
        </allowedServerVariables>            
        <outboundRules>
            <rule name="REMOVE_RESPONSE_SERVER">
                <match serverVariable="RESPONSE_SERVER" pattern=".*" />
                <action type="Rewrite" />
            </rule>
        </outboundRules>
</rewrite>
```

存放在 `C:\Windows\System32\inetsrv\config\applicationHost.config `中。

**移除X-AspNet-Version**

在 `web.config` 的 `<httpRuntime>` 中添加 `enableVersionHeader="false"`。


```
<httpRuntime enableVersionHeader="false" />
```

**移除X-AspNetMvc-Version**

在 `Application_Start()` 中添加如下代码:


```
protected void Application_Start() 
{ 
    MvcHandler.DisableMvcResponseHeader = true; 
}
```

**移除X-Powered-By**

在IIS Manager的HTTP Response Headers中移除X-Powered-By

![](https://ae01.alicdn.com/kf/HTB15FyiaA9E3KVjSZFG76319XXaa.png)

### Nginx ###

在 `conf/nginx.conf`加入

```
server_tokens off;
```

### Tomact ###

到apache-tomcat安装目录下的lib子文件夹，找到catalina.jar这包，并进行解解压。

修改 `lib\catalina.zip\org\apache\catalina\util\ServerInfo.properties`

```
server.info=X
server.number=5.5
server.built=Dec 1 2015 22:30:46 UTC
```

**Referrer:** https://blog.csdn.net/A11085013/article/details/78864807