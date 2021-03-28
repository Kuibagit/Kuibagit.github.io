---
title: CsdecryptBeacon
abbrlink: a53b1765
date: 2021-03-27 04:37:45
copyright:
tags:
---

![image-20210309223447383](https://z3.ax1x.com/2021/03/27/6x3f76.png)
<!--more-->

### CS beacon资源解密

原文 https://xz.aliyun.com/t/9224

解压 CS 核心文件 `cobaltstrike.jar` 提内容：

```bash
mkdir -p CSDecrypt/src
unzip CobaltStrike4.1/cobaltstrike.jar -d CSDecrypt/src
cd CSDecrypt/src
touch Csdecrypt.java
```

解密脚本 `Csdecrypt.java` 代码：

```java
// Chang Author: kali
import common.SleevedResource;

import java.io.*;

public class Csdecrypt {
    public static void saveFile(String filename, byte[] data) throws Exception {
        if (data != null) {
            String filepath = filename;
            File file = new File(filepath);
            if (file.exists()) {
                file.delete();
            }
            FileOutputStream fos = new FileOutputStream(file);
            fos.write(data, 0, data.length);
            fos.flush();
            fos.close();
        }
    }

    public static byte[] toByteArray(File f) throws IOException {
        ByteArrayOutputStream bos = new ByteArrayOutputStream((int) f.length());
        BufferedInputStream in = null;
        try {
            in = new BufferedInputStream(new FileInputStream(f));
            int buf_size = 1024;
            byte[] buffer = new byte[buf_size];
            int len = 0;
            while (-1 != (len = in.read(buffer, 0, buf_size))) {
                bos.write(buffer, 0, len);
            }
            return bos.toByteArray();
        } catch (IOException e) {
            e.printStackTrace();
            throw e;
        } finally {
            try {
                in.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
            bos.close();
        }
    }

    public static void main(String[] var0) throws Exception {
        byte[] csdecrypt = new byte[]{1, -55, -61, 127, 102, 0, 0, 0, 100, 1, 0, 27, -27, -66, 82, -58, 37, 92, 51, 85, -114, -118, 28, -74, 103, -53, 6};

        SleevedResource.Setup(csdecrypt);
        byte[] var7 = null;
        File file = new File("/home/kali/Desktop/CSDecrypt/src/sleeve");
        File[] fs = file.listFiles();

        for (File ff : fs) {
            if (!ff.isDirectory())
                var7 = SleevedResource.readResource(ff.getPath());
            saveFile("/home/kali/Desktop/CSDecrypt/src/sleevedecrypt/" + ff.getName(), var7);
            System.out.println("解密成功: " + ff.getName());
        }
    }
}

```

Javac 编译然后执行 `.class` 文件开始解密资源：

```
javac Csdecrypt.java
java Csdecrypt
```

(o゜▽゜)o☆[BINGO!]


