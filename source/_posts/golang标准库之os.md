---
title: golang标准库之os
date: 2020-11-16 00:38:47
tags: [golang,golang标准库]
categories: [golang,golang标准库]
---

### os简介
golang内置了跨平台的操作系统相关的模块：**os**
### Hostname函数
`os.Hostname`用于获取主机名

`os.Hostname`函数原型：`func Hostname() (name string, err error)`

样例代码如下：

    package main

    import(
        "os"
        "log"
    )

    func main(){
        hostname,err := os.Hostname()
        if err != nil {
            log.Fatalln(err)
        }else{
            log.Printf("your computer's hostname is: %s",hostname)
        }
    }


### 环境变量
os模块中提供了操作环境变量的函数，包括替换字符串中的环境变量
#### Getenv函数
`os.Getenv`函数获取环境变量的值，如果该环境变量不存在则返回空字符串

`os.Getenv`函数原型：`func Getenv(key string) string`

样例代码如下：

    package main

    import(
        "log"
        "os"
    )

    func main(){
        env_path := os.Getenv("PATH")
        log.Printf("the value of PATH env is: %s",env_path)

        env_ := os.Getenv("abc")
        log.Printf("the value of abc env is: %s",env_)
    }

#### Setenv函数
`os.Setenv`函数用于设置环境变量，如果出错返回`error`

`os.Setenv`函数原型：`func Setenv(key, value string) error`

样例代码如下：

    package main

    import(
        "log"
        "os"
    )

    func main(){
        err := os.Setenv("abc","abc")
        if err != nil{
            log.Fatalln(err)
        }
        env_abc := os.Getenv("abc")
        log.Printf("the value os abc env is: %s",env_abc)

        err_new := os.Setenv("abc","abc-new")
        if err_new != nil{
            log.Fatalln(err_new)
        }
        env_abc_new := os.Getenv("abc")
        log.Printf("the value os abc env is: %s",env_abc_new)
    }

#### Environ函数
`os.Environ`函数用于获取所有环境变量，返回字符串切片，其中每一个字符串切片格式为：`key=value`

`os.Environ`函数原型为：`func Environ() []string`

样例代码如下：

    package main

    import(
        "log"
        "os"
        "strings"
    )

    func main(){
        env_s := os.Environ()

        log.Println(env_s)


        for _,v := range(env_s){
            env_k := strings.Split(v,"=")[0]
            env_v := strings.Split(v,"=")[1]
            log.Printf("%s: %s",env_k,env_v)
        }
    }

#### Clearenv函数
`os.Clearenv`函数删除所有环境变量

`os.Clearenv`函数原型为：`func Clearenv()`

样例代码如下：

    package main

    import(
        "log"
        "os"
    )

    func main(){
        os.Clearenv()
        env_s := os.Environ()
        log.Printf("env list is: %v",env_s)
    }

#### ExpandEnv
`os.ExpandEnv`将字符串中`${var}`或者`$var`替换为环境变量`var`的值

`os.ExpandEnv`函数原型为：`func ExpandEnv(s string) string`

样例代码如下：

    package main

    import(
        "os"
        "log"
    )

    func main(){
        p := "path env is: ${PATH}"
        g := "goroot env is: $GOROOT"
        h := "h env is: ${h}"

        p_n := os.ExpandEnv(p)
        g_n := os.ExpandEnv(g)
        h_n := os.ExpandEnv(h)

        log.Println(p_n)
        log.Println(g_n)
        log.Println(h_n)
    }

### 程序调用者身份
有时候需要获取运行程序的用户身份，因此`os`模块也提供了对应的函数

#### Getuid函数
`os.Getuid`函数返回调用程序的`用户uid`

`os.Getuid`函数原型：`func Getuid() int`

#### Geteuid函数
`os.Geteuid`函数返回调用程序的`有效用户uid`

`os.Geteuid`函数原型：`func Getegid() int`

#### Getgid函数
`os.Getgid`函数返回调用程序的`用户gid`

`os.Getgid`函数原型：`func Getgid() int`

#### Getegid函数
`os.Getegid`函数返回调用程序的`有效用户gid`

`os.Getegid`函数原型：`func Getegid() int`

#### Getgroups函数
`os.Getgroups`函数返回调用程序的`用户所属的所有组id`

`os.Getgroups`函数原型：`func Getgroups() ([]int, error)`

#### 样例代码

    package main

    import(
        "os"
        "log"
    )

    func main(){
        u_uid := os.Getuid()
        u_gid := os.Getgid()
        u_euid := os.Geteuid()
        u_egid := os.Getegid()
        u_groups,_ := os.Getgroups()
        log.Printf("your uid is: %d",u_uid)
        log.Printf("your euid is: %d",u_euid)
        log.Printf("your gid is: %d",u_gid)
        log.Printf("your egid is: %d",u_egid)
        log.Printf("your groups is: %v",u_groups)
    }

### 程序id
`os`模块提供了获取进程的`pid`以及父进程的`pid`

#### Getpid函数
`os.Getpid`函数返回进程的`pid`

`os.Getpid`函数原型：`func Getpid() int`

#### Getppid函数
`os.Getppid`函数返回父进程的`pid`

`os.Getppid`函数原型：`func Getppid() int`

#### 样例代码

    package main

    import(
        "os"
        "log"
    )

    func main(){
        pid := os.Getpid()
        ppid := os.Getppid()
        log.Printf("the process pid is: %d\nthe process's father pid is: %d",pid,ppid)
    }

### Exit函数
`os.Exit`函数用于以指定状态码退出程序，按照惯例`0`表示成功，`非0`表示失败，并且`defer`不会运行

`os.Exit`函数原型：`func Exit(code int)`

样例代码如下：

    package main

    import(
        "os"
    )

    func main(){
        os.Exit(1)
    }

### 创建目录
`os`模块提供两种模式创建目录`Mkdir`、`MkdirAll`

#### Mkdir函数
`os.Mkdir`函数用于创建目录，但是必须其上级目录已存在，否则报错

`os.Mkdir`函数原型：`func Mkdir(name string, perm FileMode) error`

代码样例如下：

    package main

    import(
        "os"
        "log"
    )

    func main(){
        if err := os.Mkdir("/tmp/test_dir",0666);err != nil {
            log.Fatalln(err)
        }
        if err := os.Mkdir("/tmp/parent/child",0666);err != nil{
            log.Fatalln(err)
        }
    }

#### MkdirAll函数
`os.MkdirAll`函数用于创建目录，包括其上级目录，如果目录已经存在，则返回nil

`os.MkdirAll`函数原型：`func MkdirAll(path string, perm FileMode) error`

样例代码如下：

    package main

    import(
        "os"
        "log"
    )

    func main(){
        if err := os.MkdirAll("/tmp/parent/child",0777);err != nil{
            log.Fatalln(err)
        }

        if err := os.MkdirAll("/tmp/parent/child",0777);err != nil{
            log.Fatalln(err)
        }else{
            log.Println("dir exists")
        }
    }













