---
title: golang标准库之strings
date: 2020-11-14 23:22:21
tags: [golang,golang标准库,golang字符串]
categories: [golang,golang标准库]
---

### 简介
golang的`strings`模块提供了大量的处理字符串的函数

### 字符串分割
#### Split函数
`strings.Split`函数通过指定分割符号分割字符串返回一个字符串类型切片，如果分割符为空字符，则会将字符串分割为一个个的`Unocode`字符

`strings.Split`函数原型：`func Split(s, sep string) []string`

样例代码如下：

    package main

    import(
        "log"
        "strings"
    )

    func main(){
        s := "this is a test string"
        r := strings.Split(s," ")
        log.Println(r)
    }

#### SplitN函数
`strings.SplitN`函数通过指定分割符分割字符串并且返回一个字符串类型切片，其中参数`n`用来控制生成的切片个数，如果分割符为空字符，则会将字符串分割为一个个的`Unocode`字符

`strings.SplitN`函数原型：`func SplitN(s, sep string, n int) []string`

样例代码如下：

    package main

    import(
        "log"
        "strings"
    )

    func main(){
        s := "this/is/a/test/string"
        r := strings.SplitN(s,"/",2)
        log.Println(r)
    }

#### SplitAfter函数
`strings.SplitAfter`函数通过指定分割符分割字符串并且返回一个字符串类型切片，其与`strings.Split`的区别是返回结果中会带上分割符

`strings.SplitAfter`函数原型：`func SplitAfter(s, sep string) []string`

样例代码如下：

    package main

    import(
        "log"
        "strings"
    )

    func main(){
        s := "this/is/a/test/string"
        r := strings.SplitAfter(s,"/")
        log.Println(r)
    }

#### SplitAfterN
`strings.SplitAfter`函数通过指定分割符分割字符串并且返回一个字符串切片，其与`strings.SplitAfter`区别是返回结果中会带上分割符

`strings.SplitAfterN`函数原型：`func SplitAfterN(s, sep string, n int) []string`

样例代码如下：

    package main

    import(
        "log"
        "strings"
    )

    func main(){
        s := "this/is/a/test/string"
        r := strings.SplitAfterN(s,"/",2) 
        log.Println(r)
    }

#### Fields函数
`strings.Fields`函数以空白分割字符串串并且返回一个字符串类型切片，其中空白用`unicode.IsSpace`来确定（可以是一个到多个连续的空白字符）

`strings.Fields`函数原型：`func Fields(s string) []string`

样例代码如下：

    package main

    import(
        "log"
        "strings"
    )

    func main(){
        s := "this    is a       test           string"
        r := strings.Fields(s)
        log.Println(r)
    }

#### FieldsFunc函数
`strings.FieldsFunc`函数通过指定函数来确定分割符，然后通过此分割符切割字符串并且返回一个字符串类型切片

### 字符串修剪
#### Trim
`strings.Trim`函数用来将字符串首尾所有在参数`cutset`中的每一个字符去掉，返回一个新字符串

`strings.Trim`函数原型：`func Trim(s string, cutset string) string`

样例代码如下：

    package main

    import(
        "log"
        "strings"
    )

    func main(){
        s := "   @#! this is a test  !#@"
        r := strings.Trim(s," !@#")
        log.Println(r)
    }

#### TrimLeft函数
`strings.TrimLeft`函数用来将字符串首部所有在参数`cutset`字符串中的每一个字符去掉返回一个新的字符串

`strings.TrimLeft`函数原型：`func TrimLeft(s string, cutset string) string`

样例代码如下：

    package main

    import(
        "log"
        "strings"
    )

    func main(){
        s := "    ! @ # this is a test string     # @ !@#"
        r := strings.TrimLeft(s," !@#")
        log.Println(r)
    }

#### TrimRight函数
`strings.TrimRight`函数用来将字符串尾部所有在参数`cutset`字符串中的每一个字符去掉返回一个新的字符串

`strings.TrimRight`函数原型：`func TrimRight(s string, cutset string) string`

样例代码如下：

    package main

    import(
        "log"
        "strings"
    )

    func main(){
        s := "    ! @ # this is a test string     # @ !@#"
        r := strings.TrimRight(s," !@#")
        log.Println(r)
    }

#### TrimPrefix函数
`strings.TrimPrefix`函数用来去掉一个字符串中的前缀字符串然后返回一个新字符串

`strings.TrimPrefix`函数原型：`func TrimPrefix(s, prefix string) string`

样例代码如下：

    package main

    import(
        "log"
        "strings"
    )

    func main(){
        s := "prefix string this is a test string prefix string"
        r := strings.TrimPrefix(s,"prefix string")
        log.Println(r)
    }

#### TrimSuffix函数
`strings.TrimSuffix`函数用来去掉一个字符串中的后缀字符串然后返回一个新的字符串

`strings.TrimSuffix`函数原型：`func TrimSuffix(s, suffix string) string`

样例代码如下：

    package main

    import(
        "log"
        "strings"
    )

    func main(){
        s := "suffix string this is a test string suffix string"
        r := strings.TrimSuffix(s,"suffix string")
        log.Println(r)
    }

#### TrimSpace函数
`strings.TrimSpace`函数去掉字符串首尾所有的空白返回一个新的字符串

`strings.TrimSpace`函数原型：`func TrimSpace(s string) string`

样例代码如下：

    package main

    import(
        "log"
        "strings"
    )

    func main(){
        s := "  this is a test space      "
        r := strings.TrimSpace(s)
        log.Println(r)
    }

#### TrimFunc
`strings.TrimFunc`函数将字符串首尾部所有满足函数的`unicode`字符去掉

`strings.TrimFunc`函数原型：`func TrimFunc(s string, f func(rune) bool) string`
#### TrimLeftFunc
`strings.TrimLeftFunc`函数将字符串首部所有满足函数的`unicode`字符去掉

`strings.TrimLeftFunc`函数原型：`func TrimLeftFunc(s string, f func(rune) bool) string`
#### TrimRightFunc
`strings.TrimRight`函数将字符串尾部所有满足函数的`unicode`字符去掉

`strings.TrimRightFunc`函数原型：`func TrimRightFunc(s string, f func(rune) bool) string`
