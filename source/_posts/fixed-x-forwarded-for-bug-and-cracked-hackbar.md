---
title: Fixed X-Forwarded for and Cracked HackBar
tags:
  - HackBar
  - Cracked
categories:
  - 技术水文
copyright: true
abbrlink: eea35710
date: 2020-04-25 02:24:19
---

![](https://s1.ax1x.com/2020/05/08/YnCX8g.png)
<!--more-->

Firefox下x_forwarded_for无法保存设置好的headers

### x_forwarded_for ###

从Firefox插件目录，复制x_forwarded_for出来并解压

编辑 `options.js` 156行，在newSettings前添加 `let`

![](https://s1.ax1x.com/2020/05/08/YnCOPS.png)


编辑 `options.html`

删除 163行的 `<script src="browser-polyfill.js"></script>`

![](https://s1.ax1x.com/2020/05/08/YnCbUf.png)

修改 `manifest.json` 41行的id值，修改为任意值

![](https://s1.ax1x.com/2020/05/08/YnCTbt.png)

删除存 `META-INF` 整个文件夹

打包为zip，然后上传到Firefox开发者中心进行签名

访问 `https://addons.mozilla.org/zh-CN/developers/` 使用Firefox账号登录（没有账号请自行注册），登录后，提交新附件组件，选【我自己托管】，按提示操作即可。成功提交后，5分钟左右即可下载签名好的插件。

![](https://s1.ax1x.com/2020/05/08/YnCq58.png)

### HackBar ###

提取 插件并解压，然后编辑 `theme\js\hackbar-panel.js`

注释 21~25行

```js
function disable_hackbar(message=null) {
    //$('#alert-license').removeClass('hidden');
    //if(message){
    //    $('#alert-license span').text(message);
    //}
    license_ok = true;
}
```

![](https://s1.ax1x.com/2020/05/08/YnCHVP.png)

删除存 META-INF，重新打包为zip，然后上传到Firefox开发者中心进行签名，签名完下载安装即可。

![](https://s1.ax1x.com/2020/05/08/YnCX8g.png)

**Reference**

https://www.bilibili.com/video/BV1j4411X7Ep?from=search&seid=13023994289158065445

