---
title: 初学Go语言笔记--1
tags:
  - 学习
  - Go
categories:
  - 编程语言
copyright: true
abbrlink: ac7b27c4
date: 2018-12-12 00:23:57
password:
---

![](https://ae01.alicdn.com/kf/HTB1eSKbaBiE3KVjSZFMq6zQhVXaK.jpg)
<!--more-->

## 示例 hello_word.go ##

```Go
package main
import "fmt"

func mian() {
    fmt.Println("Hello Word!")
}
```

`import "fmt"` ==> 使用`fmt`包（函数，或其他元素），fmt包实现了格式化IO（输入/输出）的函数。

**导入多个包：**

```Go
import "fmt"
import "os"
或：
import (
    "fmt"
    "os"
)
```

**注：**
如果导入一个包却没有使用它，则程序在运行时会报错：`import and not used：os`

## 命名规范 ##

干净、可读的代码和简洁性是 Go 追求的主要目标。通过 gofmt 来强制实现统一的代码风格。Go 语言中对象的命名也应该是简洁且有意义的。像 Java 和 Python 中那样使用混合着大小写和下划线的冗长的名称会严重降低代码的可读性。名
称不需要指出自己所属的包，因为在调用的时候会使用包名作为限定符。返回某个对象的函数或方法的名称一般都是使用名词，没有 `Get...`之类的字符，如果是用于修改某个对象，则使用 `SetName`。有必须要的话可以使用大小写混合
的方式，如 MixedCaps 或 mixedCaps，而不是使用下划线来分割多个名称。

## 常量 ##

常量使用关键字 `const` 定义

常量定义格式：`const identifiler [type] = value` ,如：

    const Pi = 3.14159

类型说明符【type】可以省略，编译器会自动根据值推断其值类型

- 显示类型定义：`const b = string = "abc"`
- 隐式类型定义：`const b = "abc"`

## 变量声明 ##

使用 `var` 关键字 `var identifier type`

```Go
var a int
var b bool
var str string
或：
var (
	a int
	b bool
	str string
)
```

这种因式分解关键字的写法一般用于声明全局变量。

### 示例 ###

```Go
package main
import (
	"fmt"
	"runtime"
	"os"
)
func main() {
	var goos string = runtime.GOOS
	fmt.Printf("The operating system is: %s\n", goos)
	path := os.Getenv("PATH")
	fmt.Printf("Path is %s\n", path)
}
```

在 Windows 下运行这段代码，则会输出 `The operating system is: windows` 以及相应的环境变量的值；如果在Linux 下运行这段代码，则会输出 `The operating system is: linux` 以及相应的的环境变量的值。

## init函数 ##

变量除了可以在全局声明中初始化，也可以在 init 函数中初始化。这是一类非常特殊的函数，它不能够被人为调用，而是在每个包完成初始化后自动执行，并且执行优先级比 main 函数高。每个源文件都只能包含一个 init 函数。初始化总是以单线程执行，并且按照包的依赖关系顺序执行。一个可能的用途是在开始执行程序之前对数据进行检验或修复，以保证程序状态的正确性。

示例：

```Go
package trans

import "math"

var Pi float64

func init() {
	Pi = 4 * math.Atan(1) // init() function computes Pi
}
```

它在init函数中计算变量Pi的初始值。

以上示例中导入了包trans，并且使用到了变量Pi：

```Go
package
	
import (
	"fmt"
	"./trans"
)

var twooPi = 2 * trans.Pi

fuc main() {
	fmt.Printf("2*Pi = %g\n", rowPi) // 2*Pi = 6.283185307179586
```
