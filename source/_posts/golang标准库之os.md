---
title: golang标准库之os
date: 2020-11-16 00:38:47
tags: [golang,golang标准库]
categories: [golang,golang标准库]
---

### os简介
golang内置了跨平台的操作系统相关的模块：**os**
### 环境变量
os模块中提供了操作环境变量的函数

| 函数原型 | 功能 |
| :----    | :---- |
| func Getenv(key string) string | 获取环境变量的值,如果不存在此变量则返回空字符串 |

例子：

    package main
    import(
        "os"
        "fmt"
    )
    
    func main(){
        if env_path := os.Getenv("PATH");env_path != nil{
            fmt.Printf("the value of PATH env is: %s\n",env_path)
        }else{
            fmt.Println("the env of PATH not found!")
        }
    }
