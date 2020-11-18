---
title: golang标准库之errors
date: 2020-11-18 20:02:14
tags: [golang,golang标准库]
categories: [golang,golang标准库]
---

### golang错误
golang内建的`error`接口用来约定表示错误信息，`nil`表示没有错误，`error`接口只有一个`Error() string`方法，也就是说任何只要实现了`Error() string`方法的结构体就实现了`error`接口

`error`接口原型如下：

    type error interface {
        Error() string
    }

自定义错误样例代码如下：

    package main

    import(
        "fmt"
    )

    type MyError struct {
        ErrorMsg string
    }

    func (e *MyError) Error() string{
        return e.ErrorMsg
    }

    func t() error{
        return &MyError{ErrorMsg: "this is a custom test error",}
    }

    func main(){
        if err := t();err != nil{
            fmt.Println(err)
        }
    }

### errors标准库
`errors`标准库提供了创建错误类型的函数`New`从而达到十分简便的方式就可以定义各种错误类型了

### New函数
`errors.New`函数通过接受字符串用来生成错误类型

`errors.New`函数原型：func New(text string) error

样例代码如下：

    package main

    import(
        "fmt"
        "errors"
    )

    func TestError() error {
        return errors.New("this is a error by errors.New")

    }
    func main(){
        if err := TestError(); err != nil{
            fmt.Println(err)
        } 
    }

