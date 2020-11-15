---
title: golang标准库之flag
date: 2020-11-14 10:34:56
tags: [golang,golang标准库,golang命令行]
categories: [golang,golang标准库]
---

### 简介

golang标准库[flag](https://studygolang.com/pkgdoc)用于解析命令行参数,虽然现在更多使用的是flag的换代产品[pflag](https://github.com/ogier/pflag),但是由于pflag兼容flag,因此了解flag是十分有必要的

### 参数类型
flag支持的参数类型相对于pflag比较少
### flag.Type()

    package main
    import(
        "flag"
    )

    func main(){
        var name = flag.String("name","","user name")
        flag.Parse()
    }
### flag.TypeVar()
