---
title: golang标准库之net/http
date: 2020-11-27 11:40:23
tags: [golang,golang标准库,http]
categories: [golang,golang标准库]
---

## 简介
golang内置了很强大的`http`模块：`net/http`，包括了客户端和服务端实现

## http请求报文
http请求报文格式如下：

    HTTP-METHOD URL HTTP-VERSION
    HTTP-REQUEST-HEADER

    HTTP-REQUEST_BODY

### Request结构体
`http.Request`结构体代表一个`HTTP请求`

`http.Request`结构体原型：

    type Request struct {
        // Method指定HTTP方法（GET、POST、PUT等）。对客户端，""代表GET。
        Method string
        // URL在服务端表示被请求的URI，在客户端表示要访问的URL。
        //
        // 在服务端，URL字段是解析请求行的URI（保存在RequestURI字段）得到的，
        // 对大多数请求来说，除了Path和RawQuery之外的字段都是空字符串。
        // （参见RFC 2616, Section 5.1.2）
        //
        // 在客户端，URL的Host字段指定了要连接的服务器，
        // 而Request的Host字段（可选地）指定要发送的HTTP请求的Host头的值。
        URL *url.URL
        // 接收到的请求的协议版本。本包生产的Request总是使用HTTP/1.1
        Proto      string // "HTTP/1.0"
        ProtoMajor int    // 1
        ProtoMinor int    // 0
        // Header字段用来表示HTTP请求的头域。如果头域（多行键值对格式）为：
        //	accept-encoding: gzip, deflate
        //	Accept-Language: en-us
        //	Connection: keep-alive
        // 则：
        //	Header = map[string][]string{
        //		"Accept-Encoding": {"gzip, deflate"},
        //		"Accept-Language": {"en-us"},
        //		"Connection": {"keep-alive"},
        //	}
        // HTTP规定头域的键名（头名）是大小写敏感的，请求的解析器通过规范化头域的键名来实现这点。
        // 在客户端的请求，可能会被自动添加或重写Header中的特定的头，参见Request.Write方法。
        Header Header
        // Body是请求的主体。
        //
        // 在客户端，如果Body是nil表示该请求没有主体买入GET请求。
        // Client的Transport字段会负责调用Body的Close方法。
        //
        // 在服务端，Body字段总是非nil的；但在没有主体时，读取Body会立刻返回EOF。
        // Server会关闭请求的主体，ServeHTTP处理器不需要关闭Body字段。
        Body io.ReadCloser
        // ContentLength记录相关内容的长度。
        // 如果为-1，表示长度未知，如果>=0，表示可以从Body字段读取ContentLength字节数据。
        // 在客户端，如果Body非nil而该字段为0，表示不知道Body的长度。
        ContentLength int64
        // TransferEncoding按从最外到最里的顺序列出传输编码，空切片表示"identity"编码。
        // 本字段一般会被忽略。当发送或接受请求时，会自动添加或移除"chunked"传输编码。
        TransferEncoding []string
        // Close在服务端指定是否在回复请求后关闭连接，在客户端指定是否在发送请求后关闭连接。
        Close bool
        // 在服务端，Host指定URL会在其上寻找资源的主机。
        // 根据RFC 2616，该值可以是Host头的值，或者URL自身提供的主机名。
        // Host的格式可以是"host:port"。
        //
        // 在客户端，请求的Host字段（可选地）用来重写请求的Host头。
        // 如过该字段为""，Request.Write方法会使用URL字段的Host。
        Host string
        // Form是解析好的表单数据，包括URL字段的query参数和POST或PUT的表单数据。
        // 本字段只有在调用ParseForm后才有效。在客户端，会忽略请求中的本字段而使用Body替代。
        Form url.Values
        // PostForm是解析好的POST或PUT的表单数据。
        // 本字段只有在调用ParseForm后才有效。在客户端，会忽略请求中的本字段而使用Body替代。
        PostForm url.Values
        // MultipartForm是解析好的多部件表单，包括上传的文件。
        // 本字段只有在调用ParseMultipartForm后才有效。
        // 在客户端，会忽略请求中的本字段而使用Body替代。
        MultipartForm *multipart.Form
        // Trailer指定了会在请求主体之后发送的额外的头域。
        //
        // 在服务端，Trailer字段必须初始化为只有trailer键，所有键都对应nil值。
        // （客户端会声明哪些trailer会发送）
        // 在处理器从Body读取时，不能使用本字段。
        // 在从Body的读取返回EOF后，Trailer字段会被更新完毕并包含非nil的值。
        // （如果客户端发送了这些键值对），此时才可以访问本字段。
        //
        // 在客户端，Trail必须初始化为一个包含将要发送的键值对的映射。（值可以是nil或其终值）
        // ContentLength字段必须是0或-1，以启用"chunked"传输编码发送请求。
        // 在开始发送请求后，Trailer可以在读取请求主体期间被修改，
        // 一旦请求主体返回EOF，调用者就不可再修改Trailer。
        //
        // 很少有HTTP客户端、服务端或代理支持HTTP trailer。
        Trailer Header
        // RemoteAddr允许HTTP服务器和其他软件记录该请求的来源地址，一般用于日志。
        // 本字段不是ReadRequest函数填写的，也没有定义格式。
        // 本包的HTTP服务器会在调用处理器之前设置RemoteAddr为"IP:port"格式的地址。
        // 客户端会忽略请求中的RemoteAddr字段。
        RemoteAddr string
        // RequestURI是被客户端发送到服务端的请求的请求行中未修改的请求URI
        // （参见RFC 2616, Section 5.1）
        // 一般应使用URI字段，在客户端设置请求的本字段会导致错误。
        RequestURI string
        // TLS字段允许HTTP服务器和其他软件记录接收到该请求的TLS连接的信息
        // 本字段不是ReadRequest函数填写的。
        // 对启用了TLS的连接，本包的HTTP服务器会在调用处理器之前设置TLS字段，否则将设TLS为nil。
        // 客户端会忽略请求中的TLS字段。
        TLS *tls.ConnectionState
    }

#### 初始化HTTP请求
`net/http`模块提供来两种方式初始化http请求，注意仅仅是初始化请求，并未发送此请求，如同在`Postman`中配置好了http请求的各种参数，如果需要发送请求还需要配合`http.Client`
##### 通过Request结构体初始化HTTP请求
由于`http.Request`代表一个http请求，因此可以直接初始化`http.Request`结构体来实现初始化一个http请求

样例代码如下：

    package main

    import(
        "log"
        "net/http"
        "net/url"
        "io/ioutil"
    )

    func main(){
        url,_ := url.Parse("http://httpbin.org/get")
        http_request := http.Request{
                Method: "GET",
                URL: url,
            }

        http_client := http.Client{}
        resp,err := http_client.Do(&http_request)
        if err != nil {
            log.Println(err)
        }
        defer resp.Body.Close()

        body,_ := ioutil.ReadAll(resp.Body)
        log.Println(string(body))
    }

##### NewRequest函数初始化HTTP请求
`http.NewRequest`函数能够初始化一个HTTP请求，返回`*Request`和`error`

`http.NewRequest`函数原型：`func NewRequest(method, urlStr string, body io.Reader) (*Request, error)`

样例代码如下：

    package main

    import(
        "log"
        "net/http"
        "io/ioutil"
    )

    func main(){
        http_request,_ := http.NewRequest("GET","http://httpbin.org/get",nil)

        http_client := http.Client{}
        resp,err := http_client.Do(http_request)
        if err != nil {
            log.Println(err)
        }
        defer resp.Body.Close()

        body,_ := ioutil.ReadAll(resp.Body)
        log.Println(string(body))
    }

### 设置HTTP请求头
`net/http`提供原生方式以及简便方式添加请求头，原生方式通过`http.Header`直接创建`Header`，简便方式通过封装好的`Request`方法，原生方式自由度高，可定制度强，简便方式就是简便
#### 添加Authorization头部(basic auth)
`Authorization`头部用于`http basic auth`

样例代码一如下：

    package main

    import(
        "log"
        "net/http"
        "net/url"
        "io/ioutil"
    )

    func main(){
        header := map[string][]string{
                "Authorization": {"YWRtaW46YWRtaW4xMjM="},
            }

        url,_ := url.Parse("http://httpbin.org/get")

        http_request := http.Request{
                Method: "GET",
                URL: url,
                Header: header,
            }

        http_client := http.Client{}

        resp,_ := http_client.Do(&http_request)

        defer resp.Body.Close()

        body,_ := ioutil.ReadAll(resp.Body)

        log.Println(string(body))

    }

样例代码二如下：

    package main

    import(
        "log"
        "net/http"
        "net/url"
        "io/ioutil"
    )

    func main(){
        url,_ := url.Parse("http://httpbin.org/get")
        http_request := http.Request{
                Method: "GET",
                URL: url,
                Header: map[string][]string{},
            }

        http_request.SetBasicAuth("admin","admin123")

        http_client := http.Client{}

        resp,_ := http_client.Do(&http_request)

        defer resp.Body.Close()

        body,_ := ioutil.ReadAll(resp.Body)

        log.Println(string(body))

    }


样例代码三如下：

    package main

    import(
        "log"
        "net/http"
        "net/url"
        "io/ioutil"
    )

    func main(){
        url,_ := url.Parse("http://httpbin.org/get")
        http_request := http.Request{
                Method: "GET",
                URL: url,
                Header: map[string][]string{},
            }

        http_request.Header.Set("Authorization","YWRtaW46YWRtaW4xMjM=")

        http_client := http.Client{}

        resp,_ := http_client.Do(&http_request)

        defer resp.Body.Close()

        body,_ := ioutil.ReadAll(resp.Body)

        log.Println(string(body))

    }

## HTTP HEADER
`http.Header`表示http header

`http.Header`原型：`type Header map[string][]string`

样例代码如下：

    package main

    import(
        "log"
        "net/http"
        "net/url"
        "io/ioutil"
    )

    func main(){
        header := map[string][]string{
                "Accept-Encoding": {"gzip, deflate"},
                "Accept-Language": {"en-us"},
                "Connection": {"keep-alive"},
            }

        url,_ := url.Parse("http://httpbin.org/get")

        http_request := http.Request{
                Method: "GET",
                URL: url,
                Header: header,
            }
        
        http_client := http.Client{}
        resp,err := http_client.Do(&http_request)
        if err != nil{
            log.Println(err)
        }
        defer resp.Body.Close()

        body,_ := ioutil.ReadAll(resp.Body)
        log.Println(string(body))

    }
### http.Header.Set
`Header.Set`方法原型：`func (h Header) Set(key, value string)`
### http.Header.Get
`Header.Get`方法原型：`func (h Header) Get(key string) string`
### http.Header.Add
`Header.Add`方法原型：`func (h Header) Add(key, value string)`
### http.Header.Del
`Header.Del`方法原型：`func (h Header) Del(key string)`

## HTTP客户端
`http.Client`结构体表示一个客户端

`http.Client`结构体原型如下：

    type Client struct {
        // Transport指定执行独立、单次HTTP请求的机制。
        // 如果Transport为nil，则使用DefaultTransport。
        Transport RoundTripper
        // CheckRedirect指定处理重定向的策略。
        // 如果CheckRedirect不为nil，客户端会在执行重定向之前调用本函数字段。
        // 参数req和via是将要执行的请求和已经执行的请求（切片，越新的请求越靠后）。
        // 如果CheckRedirect返回一个错误，本类型的Get方法不会发送请求req，
        // 而是返回之前得到的最后一个回复和该错误。（包装进url.Error类型里）
        //
        // 如果CheckRedirect为nil，会采用默认策略：连续10此请求后停止。
        CheckRedirect func(req *Request, via []*Request) error
        // Jar指定cookie管理器。
        // 如果Jar为nil，请求中不会发送cookie，回复中的cookie会被忽略。
        Jar CookieJar
        // Timeout指定本类型的值执行请求的时间限制。
        // 该超时限制包括连接时间、重定向和读取回复主体的时间。
        // 计时器会在Head、Get、Post或Do方法返回后继续运作并在超时后中断回复主体的读取。
        //
        // Timeout为零值表示不设置超时。
        //
        // Client实例的Transport字段必须支持CancelRequest方法，
        // 否则Client会在试图用Head、Get、Post或Do方法执行请求时返回错误。
        // 本类型的Transport字段默认值（DefaultTransport）支持CancelRequest方法。
        Timeout time.Duration
    }

初始化HTTP客户端方式：`http_cient := http.Client{xx}`,其中零值的`http.Client`表示一个默认的客户端

### Do方法
`Client.Do`方法发送指定请求

`Client.Do`方法原型：`func (c *Client) Do(req *Request) (resp *Response, err error)`

### Head方法
`Client.Head`方法向指定`url`发送`HEAD`请求，当`Client`为零值时候与`http.Head`函数等价

`Client.Head`方法原型：`func (c *Client) Head(url string) (resp *Response, err error)`

样例代码如下：
    
    package main

    import(
        "log"
        "net/http"
    )

    func main(){
        http_client := http.Client{}

        resp,_ := http_client.Head("http://www.baidu.com")

        defer resp.Body.Close()

        for h_k,h_v := range(resp.Header){
            log.Printf("%s=>%s",h_k,h_v)
        }

    }

### Get方法
`Client.Get`方法向指定`url`发送`GET`请求，当`Client`为零值时候与`http.Get`函数等价

`Client.Get`方法原型：`func (c *Client) Get(url string) (resp *Response, err error)`

### Post方法
`Client.Post`方法向指定`url`发送`POST`请求，当`Client`为零值时候与`http.Post`函数等价

`Client.Post`方法原型：`func (c *Client) Post(url string, bodyType string, body io.Reader) (resp *Response, err error)`

### PostForm方法
`Client.PostForm`方法向指定`url`发送`POST`请求，post数据类型为："application/x-www-form-urlencoded"，数据为：`url.Values`，当`Client`为零值时候与`http.PostForm`函数等价

`Client.PostForm`方法原型：`func (c *Client) PostForm(url string, data url.Values) (resp *Response, err error)`

### Get函数
`http.Get`函数参考`Client.Get`方法

`http.Get`函数原型：`func Get(url string) (resp *Response, err error)`
### Post函数
`http.Post`函数参考`Client.Post`方法

`http.Post`函数原型：`func Post(url string, bodyType string, body io.Reader) (resp *Response, err error)`

#### 发送json数据
发送json需要设置请求头：`Content-Type: application/json`

样例代码如下：

    package main

    import(
        "log"
        "strings"
        "net/http"
        "io/ioutil"
    )

    func main(){
        data := `{
                "name": "liuoy",
                "age": 11
            }`

        content_type := "applicaion/json"

        resp,err := http.Post("http://httpbin.org/post",content_type,strings.NewReader(data))

        if err != nil {
            log.Println(err)
        }

        defer resp.Body.Close()

        b,_ := ioutil.ReadAll(resp.Body)

        log.Println(string(b))
    }

#### 发送表单数据
发送表单数据需求设置请求头：`Content-Type: application/x-www-form-urlencoded`

样例代码如下：

    package main

    import(
        "log"
        "strings"
        "net/http"
        "io/ioutil"
    )

    func main(){
        data := `name=iluoy&age=11`

        content_type := "application/x-www-form-urlencoded"

        resp,err := http.Post("http://httpbin.org/post?k1=v1&k2=v2",content_type,strings.NewReader(data))

        if err != nil {
            log.Println(err)
        }

        defer resp.Body.Close()

        b,err := ioutil.ReadAll(resp.Body)

        if err != nil {
            log.Println(err)
        }

        log.Println(string(b))

    }

#### 发送args数据
发送args数据需要在url中添加`?k1=v1&k2=v2`类似数据

样例代码如下：

    package main

    import(
        "log"
        "strings"
        "net/http"
        "io/ioutil"
    )

    func main(){
        url := "http://httpbin.org/post?k1=v1&k2=v2"

        content_type := "application/json"

        data := `
            {
                "name": "iluoy",
                "age": 11

            }
        `

        resp,err := http.Post(url,content_type,strings.NewReader(data))

        if err != nil {
            log.Println(err)
        }

        defer resp.Body.Close()

        b,err := ioutil.ReadAll(resp.Body)

        if err != nil {
            log.Println(err)
        }

        log.Println(string(b))

    }

#### 发送XML数据
发送xml数据需要设置请求头为：`Content-Type: application/xml`

样例代码如下：

    package main

    import(
        "log"
        "strings"
        "net/http"
        "io/ioutil"
    )

    func main(){
        data := `
            <person>
                <name>iluoy</name>
                <age>11</age>
            </person>
        `

        content_type := "application/xml"

        resp,err := http.Post("http://httpbin.org/post",content_type,strings.NewReader(data))

        if err != nil {
            log.Println(err)
        }

        defer resp.Body.Close()
        b,_ := ioutil.ReadAll(resp.Body)

        log.Println(string(b))

    }
### Head函数
`http.Head`函数参考`Client.Head`方法

`http.Head`函数原型：`func Head(url string) (resp *Response, err error)`
### PostForm函数
`http.PostForm`函数参考`Client.PostForm`方法

`http.PostForm`函数原型：`func PostForm(url string, data url.Values) (resp *Response, err error)`

## http响应报文
http响应报文格式如下

    HTTP_VERSION STATUS_CODE STATUS_MSG
    HTTP_HEADERS

    HTTP_BODY

### Handler
`http.Handler`结构体对象注册到http服务端用于为匹配到路径提供服务

`http.Handler`原型：
    
    type Handler interface {
        ServeHTTP(ResponseWriter, *Request)
    }

### ServeMux
`http.ServeMux`用于注册路径与handler对应关系

### http服务端
`http.Server`结构体表示`http`服务端

`http.Server`结构体原型：

    type Server struct {
        Addr           string        // 监听的TCP地址，如果为空字符串会使用":http"
        Handler        Handler       // 调用的处理器，如为nil会调用http.DefaultServeMux
        ReadTimeout    time.Duration // 请求的读取操作在超时前的最大持续时间
        WriteTimeout   time.Duration // 回复的写入操作在超时前的最大持续时间
        MaxHeaderBytes int           // 请求的头域最大长度，如为0则用DefaultMaxHeaderBytes
        TLSConfig      *tls.Config   // 可选的TLS配置，用于ListenAndServeTLS方法
        // TLSNextProto（可选地）指定一个函数来在一个NPN型协议升级出现时接管TLS连接的所有权。
        // 映射的键为商谈的协议名；映射的值为函数，该函数的Handler参数应处理HTTP请求，
        // 并且初始化Handler.ServeHTTP的*Request参数的TLS和RemoteAddr字段（如果未设置）。
        // 连接在函数返回时会自动关闭。
        TLSNextProto map[string]func(*Server, *tls.Conn, Handler)
        // ConnState字段指定一个可选的回调函数，该函数会在一个与客户端的连接改变状态时被调用。
        // 参见ConnState类型和相关常数获取细节。
        ConnState func(net.Conn, ConnState)
        // ErrorLog指定一个可选的日志记录器，用于记录接收连接时的错误和处理器不正常的行为。
        // 如果本字段为nil，日志会通过log包的标准日志记录器写入os.Stderr。
        ErrorLog *log.Logger
        // 内含隐藏或非导出字段
    }



