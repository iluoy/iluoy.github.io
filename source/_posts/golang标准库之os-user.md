---
title: golang标准库之os/user
date: 2020-11-16 22:44:14
tags: [golang,golang标准库]
categories: [golang,golang标准库]
---

### 简介
golang内置了获取系统用户的模块: `os/user`,`os/user`可以通过id获取用户信息，也可以通过用户名获取用户信息

### User结构体
`os/user`用户信息通过`user.User`结构体来定义，通常情况下使用`user.Current`、`user.Lookup`、`user.LookupId`这三个函数来初始化`user.User`

`user.User`结构体原型如下：

    type User struct {
        Uid      string // 用户ID
        Gid      string // 初级组ID
        Username string //用户名
        Name     string
        HomeDir  string //用户家目录
    }

### Current函数
`user.Current`函数用来获取当前用户信息

`user.Current`函数原型： `func Current() (*User, error)`

样例代码如下：

    package main

    import(
        "fmt"
        "os/user"
    )

    func main(){
        if user,err := user.Current(); err != nil{
            fmt.Println(err)
        }else{
            fmt.Printf("current user's uid is: %s\n",user.Uid)
            fmt.Printf("current user's gid is: %s\n",user.Gid)
            fmt.Printf("current user's username is: %s\n",user.Username)
            fmt.Printf("current user's name is: %s\n",user.Name)
            fmt.Printf("current user's homedir is: %s\n",user.HomeDir)
        }
    }

### Lookup函数
`user.Lookup`函数通过用户名获取用户信息

`user.Lookup`函数原型：`func Lookup(username string) (*User, error)`

样例代码如下：

    package main
    
    import(
        "fmt"
        "os/user"
    )

    func main(){
        if user,err := user.Lookup("root"); err != nil{
            fmt.Println(err)
        }else{
            fmt.Printf("current user's uid is: %s\n",user.Uid)
            fmt.Printf("current user's gid is: %s\n",user.Gid)
            fmt.Printf("current user's username is: %s\n",user.Username)
            fmt.Printf("current user's name is: %s\n",user.Name)
            fmt.Printf("current user's homedir is: %s\n",user.HomeDir)
        }
    }

### LookupId函数
`user.LookupId`函数通过用户id获取用户信息

`user.LookupId`函数原型：`func LookupId(uid string) (*User, error)`

样例代码如下：

    package main

    import(
        "fmt"
        "os/user"
    )

    func main(){
        if user,err := user.LookupId("1");err != nil{
            fmt.Println(err)
        }else{
            fmt.Printf("current user's uid is: %s\n",user.Uid)
            fmt.Printf("current user's gid is: %s\n",user.Gid)
            fmt.Printf("current user's username is: %s\n",user.Username)
            fmt.Printf("current user's name is: %s\n",user.Name)
            fmt.Printf("current user's homedir is: %s\n",user.HomeDir)
        }
    }
