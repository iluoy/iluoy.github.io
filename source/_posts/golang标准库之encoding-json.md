---
title: golang标准库之encoding/json
date: 2020-11-17 22:05:55
tags: [golang,golang标准库]
categories: [golang,golang标准库]
---

### 简介
golang内置了`encoding/json`用来解析json对象，关于json详见[json rfc标准](https://tools.ietf.org/html/rfc4627)

### Marshal函数
`json.Marshal`函数通过golang对象返回json字符串

`json.Marshal`函数原型: `func Marshal(v interface{}) ([]byte, error)`

代码样例如下：

    package main

    import(
        "fmt"
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
        
        r,_ := json.Marshal(person)
        fmt.Printf("person info is: %s\n",r)
    }

> 注意`type:"JSON_FILD"`中冒号后面不能有空格

### Unmarshal函数
`json.Unmarshal`函数将json字符串转换为golang对象

`json.Unmarshal`函数原型：`func Unmarshal(data []byte, v interface{}) error`

样例代码如下：

    package main

    import(
        "fmt"
        "encoding/json"
    )

    type Person struct{
        Name string `json:"name"`
        Age  int    `json:"age"`
    }
    func main(){
        var person Person

        j_str := []byte(`{"name": "yaoyao","age": 11}`)
        if err := json.Unmarshal(j_str,&person);err != nil{
            fmt.Println(err)
        }else{
            fmt.Printf("person's name is: %s\n",person.Name)
            fmt.Printf("person's age is: %d\n",person.Age)
        }
    }

### Decoder结构体
`json.Decoder`结构体用于从`io.Reader`将json字符串转换为golang对象，通常使用`json.NewDecoder`函数生成`json.Decoder`

`json.Decoder`结构体原型：

    type Decoder struct {
        // 内含隐藏或非导出字段
    }

### NewDecoder函数
`json.NewDecoder`函数用来从`io.Reader`输入流生成`json.Decoder`对象，通常配合`Decoder.Decode`方法一起使用从而实现从`io.Reader`输入流将json字符串解析为golang对象，例如从json文件中解析json字符串为golang对象

`json.NewDecoder`函数原型：`func NewDecoder(r io.Reader) *Decoder`

样例代码如下：

    package main

    import(
        "fmt"
        "os"
        "encoding/json"
    )

    type Person struct{
        Name string `json:"name"`
        Age  int    `json:"age"`
    }
    func main(){
        var person Person
        f,_ := os.OpenFile("./person.json",os.O_RDONLY,0666)
        decoder := json.NewDecoder(f)
        err := decoder.Decode(&person)
        if err != nil {
            fmt.Println(err)
        }else{
            fmt.Printf("person's name is: %s\n",person.Name)
            fmt.Printf("person's age is: %d\n",person.Age)
        }
    }
### Decode方法
`Decoder.Decode`方法从`io.Reader`输入流将json字符串转换为golang对象

`Decoder.Decode`方法原型：`func (dec *Decoder) Decode(v interface{}) error`

样例代码如上

### Encoder结构体
`json.Encoder`结构体用于将golang对象通过`io.Writer`输出流转换为json字符串

`json.Encoder`结构体原型如下：

    type Encoder struct {
        // 内含隐藏或非导出字段
    }

### NewEncoder函数
`json.NewEncoder`函数用来生成`json.Encoder`对象，通常结合`Encoder.Encode`方法将golang对象通过`io.Writer`输出流转换为json字符串，例如：将golang对象转换为json后写入json文件中保存

`json.NewEncoder`函数原型：`func NewEncoder(w io.Writer) *Encoder`

样例代码如下：

    package main

    import(
        "os"
        "fmt"
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

        f,_ := os.OpenFile("person1.json",os.O_CREATE|os.O_RDWR|os.O_TRUNC,0666)
        encoder := json.NewEncoder(f)
        if err := encoder.Encode(person);err != nil{
            fmt.Println(err)
            f.Close()
        }else{
            f.Close()
        }
    }

### Encode方法
`Encoder.Encode`方法用来将golang对象解析为json字符串并发送往`io.Writer`输出流

`Encoder.Encode`方法原型：`func (enc *Encoder) Encode(v interface{}) error`

样例代码如上

