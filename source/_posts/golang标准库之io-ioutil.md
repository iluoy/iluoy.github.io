---
title: golang标准库之io/ioutil
date: 2020-11-19 21:03:21
tags: [golang,golang标准库]
categories: [golang,golang标准库]
---

### 简介
`io/ioutil`标准库提供了很多有用的有关`io`的函数

### ReadAll函数
`ioutil.ReadAll`函数从`io.Reader`中读取内容直到`EOF`或者`error`，读取成功后返回的`err`为`nil`

`ioutil.ReadAll`函数原型：`func ReadAll(r io.Reader) ([]byte, error)`

样例代码如下：

    package main

    import(
        "os"
        "log"
        "io/ioutil"
        "encoding/json"
    )

    type Person struct{
        Name string `json:"name"`
        Age  int    `json:"age"`
    }

    func main(){
        var person Person

        f,err := os.OpenFile("test.json",os.O_RDONLY,0666)
        if err != nil {
            log.Fatalln(err)
        }
        r,err := ioutil.ReadAll(f)
        if err != nil{
            log.Fatalln(err)
        }

        if err := json.Unmarshal(r,&person);err != nil{
            log.Fatalln(err)
        }else{
            log.Printf("user's name is: %s",person.Name)
            log.Printf("user's age is: %d",person.Age)
        }
    }

### ReadFile函数
`ioutil.ReadFile`函数从指定文件读取内容直到`EOF`或者`error`，读取成功后`err`为`nil`

`ioutil.ReadFile`函数原型为：`func ReadFile(filename string) ([]byte, error)`

样例代码如下：

    package main

    import(
        "log"
        "io/ioutil"
        "encoding/json"
    )

    type Person struct{
        Name string `json:"name"`
        Age  int    `json:"age"`
    }

    func main(){

        var person Person

        r,err := ioutil.ReadFile("test.json")

        if err != nil {
            log.Fatalln(err)
        }

        if err := json.Unmarshal(r,&person);err != nil{
            log.Fatalln(err)
        }else{
            log.Printf("user's name is: %s",person.Name)
            log.Printf("user's age is: %d",person.Age)
        }
    }

### WriteFile函数
`ioutil.WriteFile`函数向指定文件写入内容，如果文件不存在则按照指定权限创建文件，否则写入文件前清空内容

`ioutil.WriteFile`函数原型为： func WriteFile(filename string, data []byte, perm os.FileMode) error

样例代码如下：

    package main

    import(
        "log"
        "io/ioutil"
    )

    func main(){

        data := []byte(`{"name": "yao","age": 11}`)

        if err := ioutil.WriteFile("test1.json",data,0666);err != nil{
            log.Fatalln(err)
        }else{
            log.Println("write success")
        }
    }

### TempDir函数
`ioutil.TempDir`函数用来生成临时目录，但是调用此函数的用户需要自行删除临时目录，而且约定是必须的，否则就不叫临时目录了，如果`dir`为空则使用默认临时目录（例如linux、mac为/tmp）：见`os.TempDir`

`ioutil.TempDir`函数原型：`func TempDir(dir, prefix string) (name string, err error)`

样例代码如下：

    package main

    import(
        "log"
        "io/ioutil"
    )

    func main(){
        if dir,err := ioutil.TempDir("/tmp","tmpdir");err != nil{
            log.Fatalln(err)
        }else{
            log.Printf("tempdir is: %s",dir)
        }
    }

### TempFile
`ioutil.TempFile`用来生成临时文件，但是调用此函数的用户需要自行删除临时文件，而且约定是必须的，否则就不叫临时文件了，如果`dir`为空则使用默认临时目录（例如linux、mac为/tmp）：见`os.TempDir`

`ioutil.TempFile`函数原型：`func TempFile(dir, prefix string) (f *os.File, err error)`

样例代码如下：

    package main

    import(
        "log"
        "io/ioutil"
        "encoding/json"
    )

    type Person struct{
        Name string `json:"name"`
        Age  int    `json:"age"`
    }

    func main(){
        person := Person{
                Name: "iluoy",
                Age: 11,
            }

        f,err := ioutil.TempFile("/tmp","tempfile")

        if err != nil{
            log.Fatalln(err)
        }

        encoder := json.NewEncoder(f)
        if err := encoder.Encode(&person); err != nil{
            log.Fatalln(err)
        }else{
            log.Println("create tempfile success")
        }
    }

### ReadDir函数
`ioutil.ReadDir`函数用来获取指定目录的目录信息

`ioutil.ReadDir`函数原型：`func ReadDir(dirname string) ([]os.FileInfo, error)`

样例代码如下：

    package main

    import(
        "log"
        "io/ioutil"
    )

    func main(){
        if f,err := ioutil.ReadDir("/tmp"); err != nil{
            log.Fatalln(err)
        }else{
            for _,file_info := range(f){
                log.Println(file_info.Name())
            }
        }
    }
