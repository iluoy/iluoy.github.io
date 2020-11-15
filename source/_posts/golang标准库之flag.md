---
title: golang标准库之flag
date: 2020-11-14 10:34:56
tags: [golang,golang标准库,golang命令行]
categories: [golang,golang标准库]
---

### 测试

测试代码

    package main
    import(
        "flag"
    )
    var(
        name  string
        age   int
        email string
    )
    func main(){
        flag.StringVar(&name,"name","","user's name")
        flag.IntVar(&age,"age",0,"user's age")
        flag.StringVar(&email,"email","","user's email")
        flag.Parse()
    }
