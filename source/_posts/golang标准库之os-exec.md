---
title: golang标准库之os/exec
date: 2020-11-16 19:36:52
tags: [golang,golang标准库]
categories: [golang,golang标准库]
---

### 简介
很多时候我们都通过直接调用外部系统命令以减少工作量,golang已内置了`os/exec`用来执行外部命令,`os/exec`模块主要通过`Cmd结构体`来实现执行外部命令,具体可见：[os/exec](https://studygolang.com/pkgdoc)
### Cmd结构体
`Cmd`表示一个准备执行或者正在执行中的外部命令,其中`Cmd结构体`中使用最多的字段有：`Path`、`Args`、`Dir`、`Stdio`、`Stdout`、`Stderr`其结构体原型如下：

    type Cmd struct {
        // Path是将要执行的命令的路径。
        //
        // 该字段不能为空，如为相对路径会相对于Dir字段。
        Path string
        // Args保管命令的参数，包括命令名作为第一个参数；如果为空切片或者nil，相当于无参数命令。
        //
        // 典型用法下，Path和Args都应被Command函数设定。
        Args []string
        // Env指定进程的环境，如为nil，则是在当前进程的环境下执行。
        Env []string
        // Dir指定命令的工作目录。如为空字符串，会在调用者的进程当前目录下执行。
        Dir string
        // Stdin指定进程的标准输入，如为nil，进程会从空设备读取（os.DevNull）
        Stdin io.Reader
        // Stdout和Stderr指定进程的标准输出和标准错误输出。
        //
        // 如果任一个为nil，Run方法会将对应的文件描述符关联到空设备（os.DevNull）
        //
        // 如果两个字段相同，同一时间最多有一个线程可以写入。
        Stdout io.Writer
        Stderr io.Writer
        // ExtraFiles指定额外被新进程继承的已打开文件流，不包括标准输入、标准输出、标准错误输出。
        // 如果本字段非nil，entry i会变成文件描述符3+i。
        //
        // BUG: 在OS X 10.6系统中，子进程可能会继承不期望的文件描述符。
        // http://golang.org/issue/2603
        ExtraFiles []*os.File
        // SysProcAttr保管可选的、各操作系统特定的sys执行属性。
        // Run方法会将它作为os.ProcAttr的Sys字段传递给os.StartProcess函数。
        SysProcAttr *syscall.SysProcAttr
        // Process是底层的，只执行一次的进程。
        Process *os.Process
        // ProcessState包含一个已经存在的进程的信息，只有在调用Wait或Run后才可用。
        ProcessState *os.ProcessState
        // 内含隐藏或非导出字段
    }

### Command函数
一般而言都是通过`os.Command`函数来实现Cmd初始化,`os.Command`仅仅设定了`Cmd`的`Path`和`Args`字段,因此在使用`os.Command`初始化`Cmd`后使用其返回值来配置`Stdin`、`Stdout`、`Stderr`等属性

Command函数原型为：`func Command(name string, arg ...string) *Cmd`

样例代码如下：

    package main
    
    import(
        "os"
        "os/exec"
        "fmt"
    )

    func main(){
        // 执行ls -al命令
        cmd := exec.Command("ls","-al")

        // 设置执行ls -al命令时候标准输入、标准输出、标准错误输出输出,os.Stdin、os.Stdout、os.Stderr是os模块的变量
        cmd.Stdin = os.Stdin
        cmd.Stdout = os.Stdout
        cmd.Stderr = os.Stderr

        // Cmd.Run用来运行命令,注意此命令会阻塞程序,如果想要不阻塞则使用Cmd.Start和Cmd.Wait
        if err := cmd.Run();err != nil{
            fmt.Println(err)
        }
    }

### Run方法
如上述，`Cmd.run`方法执行外部命令并且会阻塞直至外部命令执行完成

`Cmd.Run`方法原型: `func (c *Cmd) Run() error`

样例代码如下:

    package main
    
    import(
        "os"
        "os/exec"
        "fmt"
    )

    func main(){
        // 执行sleep命令,睡眠10s
        cmd := exec.Command("sleep","10")

        // 设置执行sleep 10命令时候标准输入、标准输出、标准错误输出输出,os.Stdin、os.Stdout、os.Stderr是os模块的变量
        cmd.Stdin = os.Stdin
        cmd.Stdout = os.Stdout
        cmd.Stderr = os.Stderr

        // Cmd.Run用来运行命令,注意此命令会阻塞程序,如果想要不阻塞则使用Cmd.Start和Cmd.Wait
        err := cmd.Run()

        fmt.Println(err)

        fmt.Println("command exec finished")
    }

### Start方法
如上述，`Cmd.Start`方法执行外部命令并且不会阻塞,通常`Cmd.Start`配合`Cmd.Wait`、`Cmd.StdinPipe`、`Cmd.StdoutPipe`、`Cmd.StderrPipe`一起使用

`Cmd.Run`方法原型: `func (c *Cmd) Start() error`

样例代码如下:

    package main
    
    import(
        "os/exec"
        "fmt"
    )

    func main(){
        // 执行sleep命令,睡眠10s
        cmd := exec.Command("sleep","10")

        // Cmd.Run用来运行命令,注意此命令会阻塞程序,如果想要不阻塞则使用Cmd.Start和Cmd.Wait
        err := cmd.Start()
        fmt.Println("command exec not finished")
        cmd.Wait()
        fmt.Println("command exec finished")
        fmt.Println(err)
    }

### Wait方法
`Cmd.Wait`会阻塞直到命令执行完毕，`Cmd.Wait`的命令必须是被`Cmd.Start`方法开始执行的

`Cmd.Wait`方法原型：`func (c *Cmd) Wait() error`

样例代码见上

### Output方法
`Cmd.Output`方法执行外部命令并且返回标准输出的切片

`Cmd.Output`方法原型：`func (c *Cmd) Output() ([]byte, error)`

样例代码如下：

    package main

    import(
        "os/exec"
        "fmt"
    )

    func main(){
        cmd := exec.Command("ls","-a","-l")
        if b,err := cmd.Output(); err != nil{
            fmt.Println(err)
        }else{
            fmt.Printf("output is %s\n",b)
        }
    }

### CombinedOutput方法
`Cmd.CombinedOutput`方法执行外部命令并且返回标准输出和标准错误输出合并的切片

`Cmd.CombinedOutput`方法原型：`func (c *Cmd) CombinedOutput() ([]byte, error)`

样例代码如下：

    package main

    import(
        "fmt"
        "os/exec"
    )

    func main(){
        cmd := exec.Command("ls","-al")

        if b,err := cmd.CombinedOutput();err != nil{
            fmt.Println(err)
        }else{
            fmt.Printf("output is %s\n",b)
        }
    }

### LookPath函数
`LookPath`函数从环境变量中搜索可执行文件，如果参数有斜杠，则只在当前目录搜索，返回完整路径或者相对当前目录的相对路径

`LookPath`函数原型: `func LookPath(file string) (string, error)`

样例代码如下：

    package main

    import(
        "fmt"
        "os/exec"
    )

    func main(){
        if file_path,err := exec.LookPath("ls");err != nil{
            fmt.Println(err)
        }else{
            fmt.Printf("exec file path is: %s\n",file_path)
        }

        if file_path1,err := exec.LookPath("ls/a.out");err != nil{
            fmt.Println(err)
        }else{
            fmt.Printf("exec file path is: %s\n",file_path1)
        }
    }


