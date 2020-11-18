---
title: golang标准库之log
date: 2020-11-14 23:12:05
tags: [golang,golang标准库,golang日志]
categories: [golang,golang标准库]
---

### 简介
日志记录在日常开发中可谓必不可少的，golang内置的`log`模块可以提供简单的日志记录

### log模块的日志flag
`log`模块通过`flag`标识位设置日志输出的字段，目前只支持以下几种：`Ldata`、`Ltime`、`Lmicroseconds`、`Llongfile`、`Lshortfile`

    const (
        // 字位共同控制输出日志信息的细节。不能控制输出的顺序和格式。
        // 在所有项目后会有一个冒号：2009/01/23 01:23:23.123123 /a/b/c/d.go:23: message
        Ldate         = 1 << iota     // 日期：2009/01/23
        Ltime                         // 时间：01:23:23
        Lmicroseconds                 // 微秒分辨率：01:23:23.123123（用于增强Ltime位）
        Llongfile                     // 文件全路径名+行号： /a/b/c/d.go:23
        Lshortfile                    // 文件无路径名+行号：d.go:23（会覆盖掉Llongfile）
        LstdFlags     = Ldate | Ltime // 标准logger的初始值
    )

### Logger结构体
`log.Logger`结构体是一个日志记录的对象，一般配合`log.New()`函数生成`log.Logger`对象

`log.Logger`结构体原型如下：

    type Logger struct {
        // contains filtered or unexported fields
    }

### New方法
`log.New`方法用来生成`log.Logger`对象，其`out io.Writer`表示输出到实现了`io.Writer`的输出流上，`prefix`表示在每条日志前自动添加的前缀，`flag`是上述`Ldata`之类的用来控制日志输出字段的标志

`log.New`方法原型为：`func New(out io.Writer, prefix string, flag int) *Logger`

样例代码如下：

    package main

    import(
        "os"
        "log"
    )

    func main(){
        logger := log.New(os.Stdout,"test prefix",log.Ldate | log.Ltime | log.Llongfile)
        logger.Println("test log")

    }

### SetFlags方法
`Logger.SetFlags`方法用来设置`flag`日志输出字段的

`Logger.SetFlags`方法原型：`func (l *Logger) SetFlags(flag int)`

样例代码如下：

    package main

    import(
        "os"
        "log"
    )

    func main(){
        logger := log.New(os.Stdout,"",log.Ldate)
        logger.Println("test log")
        logger.SetFlags(log.Ldate | log.Lmicroseconds)
        logger.Println("test log")
    }

### SetPrefix
`Logger.SetPrefix`方法用来设置`prefix`日志前缀的

`Logger.SetPrefix`方法原型为：`func (l *Logger) SetPrefix(prefix string)`

样例代码如下：

    package main

    import(
        "os"
        "log"
    )

    func main(){
        logger := log.New(os.Stdout,"",log.Ldate)
        logger.Println("test log")
        logger.SetPrefix("test prefix")
        logger.Println("test log")
    }

### Logger.Println方法
`Logger.Println`方法用来输出日志，并且自动在行末添加换行符,类似的还有`Logger.Print`、`Logger.Printf`、`Logger.Fatalln`、`Logger.Fatal`、`Logger.Fatalf`、`Logger.Panicln`、`Logger.Panic`、`Logger.Panicf`，使用方法参考`fmt`模块，其中`Fatal`类型日志会在记录日志后调用`os.Exit(1)`推出程序，`Panic`类型日志会在记录日志后**panic**

`Logger.Println`方法原型为：`func (l *Logger) Println(v ...interface{})`

### 通过默认Logger使用log
默认`log`就已经有一个标准的`Logger`、因此可以直接使用`log.SetPrefix`、`log.SetOutput`、`log.SetFlags`、`log.Println`、`log.Print`、`log.Printf`、`log.Fatal`、`log.Fatalln`、`log.Fatalf`、`log.Panic`、`log.Panicln`、`log.Panicf`

样例代码如下（输出到标准输出）：

    package main

    import(
        "log"
    )

    func main(){
        log.Println("test log")
        log.SetPrefix("test prefix")
        log.Println("test log")
        log.SetFlags(log.Ldate | log.Lmicroseconds | log.Llongfile)
        log.Println("test log")
    }

样例代码2如下（输出到文件）：

    package main

    import(
        "os"
        "log"
    )

    func main(){
        if f,err := os.OpenFile("test.log",os.O_CREATE | os.O_TRUNC | os.O_RDWR,0666);err != nil{
            log.Fatalln("open file test.log failed")
        }else{
            log.SetOutput(f)
            log.Println("test log")
        }

    }
