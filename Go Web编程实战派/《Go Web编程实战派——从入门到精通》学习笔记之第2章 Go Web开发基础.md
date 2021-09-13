#《Go Web编程实战派——从入门到精通》学习笔记之第2章 Go Web开发基础


### 《Go Web编程实战派——从入门到精通》学习笔记之第2章 Go Web开发基础
- - <ul><li>- - <ul><li>- - - - - - - - - - - 


# 第2章 Go Web开发基础

## 2.1 helloWorldWeb

```
//helloWorldWeb.go
//go run helloWorldWeb.go
//127.0.0.1
package main
import (
	"fmt"
	"net/http"
)

func hello(w http.ResponseWriter, r *http.Request) {<!-- -->
	fmt.Fprintf(w, "Hello World")
}

func main() {<!-- -->
	server := &amp;http.Server {<!-- -->
		Addr: "0.0.0.0:80",
	}
	http.HandleFunc("/", hello)
	server.ListenAndServe()
}

```

## 2.2 Web程序运行原理简介

### 2.2.1 Web基本原理
1. 运行原理
（1）用户打开客户端浏览器，输入URL地址。 （2）客户端浏览器通过HTTP协议向服务器端发送浏览请求。 （3）服务器端通过CGI程序接收请求，调用解释引擎处理“动态内容”，访问数据库并处理数据，通过HTTP协议将得到的处理结果返回给客户端浏览器。 （4）客户端浏览器解释并显示HTML页面。
1. DNS（Domain Name System，域名系统）
将主机名和域名转换为IP地址。 DNS解析过程： （1）用户打开浏览器，输入URL地址。浏览器从URL中抽取域名（主机名），传给DNS应用程序的客户端。 （2）DNS客户端向DNS服务器端发送查询报文，其中包含主机名。 （3）DNS服务器端向DNS客户端发送回答报文，其中包含该主机名对应IP地址。 （4）浏览器收到DNS的IP地址后，向该IP地址定位的HTTP服务器端发起TCP连接。

### 2.2.2 HTTP简介

HTTP（Hyper Text Transfer Protocal，超文本传输协议），简单请求-响应协议，运行在TCP协议上，无状态。它指定客户端发送给服务器端的消息和得到的响应。请求和响应消息头是ASCII码；消息内容则类似MIME格式。

### 2.2.3 HTTP请求

客户端发送到服务器端的请求消息
1. 请求行（Request Line）
请求方法、URI、HTTP协议/协议版本组成。

<th align="left">请求方法</th><th align="left">方法描述</th>
|------
<td align="left">GET</td><td align="left">请求页面，并返回页面内容，请求参数包含在URL中，提交数据最多1024byte</td>
<td align="left">HEAD</td><td align="left">类似GET，只获取报头</td>
<td align="left">POST</td><td align="left">提交表单或上传文件，数据（含请求参数）包含在请求体中</td>
<td align="left">PUT</td><td align="left">取代指定内容的文档</td>
<td align="left">DELETE</td><td align="left">删除指定资源</td>
<td align="left">OPTIONS</td><td align="left">查看服务器的性能</td>
<td align="left">CONNECT</td><td align="left">服务器当作跳板，访问其他网页</td>
<td align="left">TRACE</td><td align="left">回显服务器收到的请求，用于测试或诊断</td>
1. 请求头（Request Header）
<th align="left">请求头</th><th align="left">示例</th><th align="left">说明</th>
|------
<td align="left">Accept</td><td align="left">Accept: text/plain, text/html</td><td align="left">客户端能够接收的内容类型</td>
<td align="left">Accept-charset</td><td align="left">Accept-charset: iso-8859-5</td><td align="left">字符编码集</td>
<td align="left">Accept-Encoding</td><td align="left">Accept-Encoding: compress, gzip</td><td align="left">压缩编码类型</td>
<td align="left">Accept-Language</td><td align="left">Accept-Language: en, zh</td><td align="left">语言</td>
<td align="left">Accept-Ranges</td><td align="left">Accept-Ranges: bytes</td><td align="left">子范围字段</td>
<td align="left">Authorization</td><td align="left">Authorization: Basic dbXleoOEpePOetpoe2Ftyd==</td><td align="left">授权证书</td>
<td align="left">Cache-Control</td><td align="left">Cache-Control: no-cache</td><td align="left">缓存机制</td>
<td align="left">Connection</td><td align="left">Connection: close</td><td align="left">是否需要持久连接（HTTP1.1默认持久连接）</td>
<td align="left">Cookie</td><td align="left">Cookie: $version=1; Skin=new;</td><td align="left">请求域名下的所有cookie值</td>
<td align="left">Content-Length</td><td align="left">Content-Length: 348</td><td align="left">内容长度</td>
1. 请求体（Request Body）
HTTP请求中传输数据的实体。

### 2.2.4 HTTP响应

服务器端返回给客户端。
1. 响应状态码（Response Status Code）
表示服务器的响应状态。

<th align="left">状态码</th><th align="left">说明</th><th align="left">详情</th>
|------
<td align="left">100</td><td align="left">继续</td><td align="left">服务器收到部分请求，等待客户端继续提出请求</td>
<td align="left">101</td><td align="left">切换协议</td><td align="left">请求者已要求服务器切换协议，服务器已确认并准备切换协议</td>
<td align="left">200</td><td align="left">成功</td><td align="left">成功处理请求</td>
<td align="left">201</td><td align="left">已创建</td><td align="left">服务器创建了新的资源</td>
<td align="left">202</td><td align="left">已接受</td><td align="left">已接收请求，但尚未处理</td>
<td align="left">203</td><td align="left">非授权信息</td><td align="left">成功处理请求，但返回信息来自另一个源</td>
<td align="left">204</td><td align="left">无内容</td><td align="left">成功处理请求，无返回内容</td>
<td align="left">205</td><td align="left">重置内容</td><td align="left">成功处理请求，内容重置</td>
<td align="left">206</td><td align="left">部分内容</td><td align="left">成功处理部分内容</td>
<td align="left">300</td><td align="left">多种选择</td><td align="left">可执行多种操作</td>
<td align="left">301</td><td align="left">永久移动</td><td align="left">永久重定向</td>
<td align="left">302</td><td align="left">临时移动</td><td align="left">暂时重定向</td>
<td align="left">303</td><td align="left">查看其他位置</td><td align="left">重定向目标文档应通过GET获取</td>
<td align="left">304</td><td align="left">未修改</td><td align="left">使用上次网页资源</td>
<td align="left">305</td><td align="left">使用代理</td><td align="left">应使用代理访问</td>
<td align="left">307</td><td align="left">临时重定向</td><td align="left">临时从其他位置响应</td>
<td align="left">400</td><td align="left">错误请求</td><td align="left">无法解析</td>
<td align="left">401</td><td align="left">未授权</td><td align="left">无身份验证或验证未通过</td>
<td align="left">403</td><td align="left">禁止访问</td><td align="left">拒绝</td>
<td align="left">404</td><td align="left">未找到</td><td align="left">找不到</td>
<td align="left">405</td><td align="left">方法禁用</td><td align="left">禁用指定方法</td>
<td align="left">406</td><td align="left">不接受</td><td align="left">无法使用内容响应</td>
<td align="left">407</td><td align="left">需要代理授权</td><td align="left">需要使用代理授权</td>
<td align="left">408</td><td align="left">请求超时</td><td align="left">请求超时</td>
<td align="left">409</td><td align="left">冲突</td><td align="left">完成请求时发生冲突</td>
<td align="left">410</td><td align="left">已删除</td><td align="left">资源永久删除</td>
<td align="left">411</td><td align="left">需要有效长度</td><td align="left">不接受标头字段不含有效内容长度</td>
<td align="left">412</td><td align="left">未满足前提条件</td><td align="left">服务器未满足某个前提条件</td>
<td align="left">413</td><td align="left">请求实体过大</td><td align="left">超出能力</td>
<td align="left">414</td><td align="left">请求URI过长</td><td align="left">网址过长，无法处理</td>
<td align="left">415</td><td align="left">不支持类型</td><td align="left">格式不支持</td>
<td align="left">416</td><td align="left">请求范围不符</td><td align="left">页面无法提供请求范围</td>
<td align="left">417</td><td align="left">未满足期望值</td><td align="left">未满足期望请求标头字段</td>
<td align="left">500</td><td align="left">服务器内部发生错误</td><td align="left">服务器错误</td>
<td align="left">501</td><td align="left">未实现</td><td align="left">不具备功能</td>
<td align="left">502</td><td align="left">错误网关</td><td align="left">收到无效响应</td>
<td align="left">503</td><td align="left">服务不可用</td><td align="left">无法使用</td>
<td align="left">504</td><td align="left">网关超时</td><td align="left">没及时收到请求</td>
<td align="left">505</td><td align="left">HTTP版本不支持</td><td align="left">不支持HTTP协议版本</td>
1. 响应头（Response Headers）
包含服务器对请求的应答信息。

<th align="left">响应头</th><th align="left">说明</th>
|------
<td align="left">Allow</td><td align="left">服务器支持的请求方法</td>
<td align="left">Content-Encondig</td><td align="left">文档编码方法。</td>
<td align="left">Content-Length</td><td align="left">内容长度，浏览器持久HTTP连接时需要</td>
<td align="left">Content-Type</td><td align="left">文档的MIME类型</td>
<td align="left">Date</td><td align="left">GMT时间</td>
<td align="left">Expires</td><td align="left">过期时间后，不再缓存</td>
<td align="left">Last-Modified</td><td align="left">文档最后改动时间。通过比较客户端头if-Modified-Since，可能返回304（Not Modified）。</td>
<td align="left">Location</td><td align="left">客户端应去哪里提取文档。</td>
<td align="left">Refresh</td><td align="left">浏览器应刷新时间，秒</td>
<td align="left">Server</td><td align="left">服务器名字</td>
<td align="left">Set-Cookie</td><td align="left">设置页面关联Cookie</td>
<td align="left">WWW-Authenticate</td><td align="left">客户应在Authorization中提供授权信息，通常返回401。</td>
1. 响应体（Response Body）
HTTP请求返回的内容。 HTML，二进制数据，JSON文档，XML文档等。

### 2.2.5 URI与URL
1. URI（Uniform Resource Identifier，统一资源标识符）
用来标识Web上每一种可用资源，概念。由资源的命名机制、存放资源的主机名、资源自身的名称等组成。
1. URL（Uniform Resource Locator，统一资源定位符）
用于描述网络上的资源（描述信息资源的字符串），实现。使用统一格式，包括文件、服务器地址和目录等。

```
scheme://host[:port#]/path/.../[?query-string][#anchor]
//协议（服务方式）
//主机域名或IP地址（可含端口号）
//具体地址，目录和文件名等

```
1. URN（Uniform Resource Name，统一资源名）
带有名字的因特网资源，是URL的更新形式，不依赖位置，可减少失效链接个数。

### 2.2.6 HTTPS简介

HTTPS（Hyper Text Transfer Protocol over SecureSocket Layer），在HTTP基础上，通过传输加密和身份认证保证传输过程的安全型。HTTP + SSL/TLS

TLS（Transport Layer Security，传输层安全性协议），及其前身SSL（Secure Socket Layer，安全套接字层），保障通信安全和数据完整性。

### 2.2.7 HTTP2简介
1. HTTP协议历史- HTTP 0.9 只支持GET方法，不支持MIME类型和HTTP各种头信息等。- HTTP 1.0 增加很多方法、各种HTTP头信息，以及对多媒体对象的处理。- HTTP 1.1 主流HTTP协议，改善结构性缺陷，明确语义，增删特性，支持更复杂的Web应用程序。- HTTP 2 优化性能，兼容HTTP 1.1语义，是二进制协议，头部采用HPACK压缩，支持多路复用、服务器推送等。1. HTTP 1.1与HTTP 2的对比- 头信息压缩 HTTP 1.1中，每一次发送和响应，都有HTTP头信息。HTTP 2压缩头信息，减少带宽。- 推送功能 HTTP 2之前，只能客户端发送数据，服务器端返回数据。HTTP2中，服务器可以主动向客户端发起一些数据传输（如css和png等），服务器可以并行发送html，css，js等数据。
### 2.2.8 Web应用程序的组成
1. 处理器（hendler） 接收HTTP请求并处理。调用模板引擎生成html文档返给客户端。
MVC软件架构模型
- 模型（Model） 处理与业务逻辑相关的数据，以及封装对数据的处理方法。有对数据直接访问的权力，例如访问数据库。- 视图（View） 实现有目的的显示数据，一般没有程序的逻辑。- 控制器（Controller） 组织不同层面，控制流程，处理用户请求，模型交互等事件，并做出响应。1. 模板引擎（template engine）
分离界面与数据（内容），组合模板（template）与数据（data），生成html文档。 分为置换型（模板内容中特定标记替换）、解释型和编译型等。

## 2.3 net/http包

### 2.3.1 创建简单服务器端
1. 创建和解析HTTP服务器端
```
package main

import (
	"net/http"
)

func sayHello(w http.ResponseWriter, req *http.Request) {<!-- -->
	w.Write([]byte("Hello World"))
}

func main() {<!-- -->
	//注册路由
	http.HandleFunc("/hello", sayHello)
	//开启对客户端的监听
	http.ListenAndServe(":8080", nil)
}

```

```
http.HandleFunc()函数

//输入参数：监听端口号和事件处理器handler
http.ListenAndServe()函数

type Handler interface {<!-- -->
	ServeHTTP(ResponseWriter, *Request)
}

type HandlerFunc func(ResponseWriter, *Request)

func (f HandlerFunc) ServeHTTP(w ResponseWriter, r *Request) {<!-- -->
	f(w, r)
}

```

```
package main

import (
	"net/http"
)

type Refer struct {<!-- -->
	handler http.Handler
	refer string
}

func (this *Refer) ServeHTTP(w http.ResponseWriter, r *http.Request) {<!-- -->
	if r.Referer() == this.refer {<!-- -->
		this.handler.ServeHTTP(w, r)
	} else {<!-- -->
		w.WriteHeader(403)
	}
}

func myHandler(w http.ResponseWriter, req *http.Request) {<!-- -->
	w.Write([]byte("this is handler"))
}

func hello(w http.ResponseWriter, req *http.Request) {<!-- -->
	w.Write([]byte("hello"))
}

func main() {<!-- -->
	referer := &amp;Refer{<!-- -->
		handler: http.HandlerFunc(myHandler),
		refer: "www.shirdon.com",
	}
	http.HandleFunc("/hello", hello)
	http.ListenAndServe(":8080", referer)
}

```
1. 创建和解析HTTPS服务器端
```
//证书文件路径，私钥文件路径
func (srv *Server) ListenAndServeTLS(certFile, keyFile string) error

```

```
package main

import (
	"log"
	"net/http"
)

func handle(w http.ResponseWriter, r *http.Request) {<!-- -->
	log.Printf("Got connection: %s", r.Proto)
	w.Write([]byte("Hello this is a HTTP 2 message!"))
}

func main() {<!-- -->
	srv := &amp;http.Server{<!-- -->Addr: ":8088", Handler: http.HandlerFunc(handle)}
	log.Printf("Serving on https://0.0.0.0:8088")
	log.Fatal(srv.ListenAndServeTLS("server.crt", "server.key"))
}

```

### 2.3.2 创建简单的客户端

```
//src/net/http/client.go
var DefaultClient = &amp;Client{<!-- -->}

func Get(url string) (resp *Response, err error) {<!-- -->
	return DefaultClient.Get(url)
}

func (c *Client) Get(url string) (resp *Response, err error) {<!-- -->
	req, err := NewRequest("GET", url, nil)
	if err != nil {<!-- -->
		return nil, err
	}
	return c.Do(req)
}

func Post(url, contentType string, body io.Reader) (resp *Response, err error) {<!-- -->
	return DefaultClient.Post(url, contentType, body)
}

func (c *Client) Post(url, contentType string, body io.Reader) (resp *Response, err error) {<!-- -->
	req, err := NewRequest("POST", url, body)
	if err != nil {<!-- -->
		return nil, err
	}
	req.Header.set("Content-Type", contentType)
	return c.Do(req)
}

```

```
func NewRequest(method, url string, body io.Reader) (*Request, error)
//请求类型
//请求地址
//若body实现io.Closer接口，则Request返回值的Body字段会被设置为body值，并被Client的Do()、Post()和PostForm()方法关闭。

```
1. 创建GET请求
```
package main

import (
	"fmt"
	"io/ioutil"
	"net/http"
)

func main() {<!-- -->
	resp, err := http.Get("https://www.baidu.com")
	if err != nil {<!-- -->
		fmt.Println("err:", err)
	}
	closer := resp.Body
	bytes, err := ioutil.ReadAll(closer)
	fmt.Println(string(bytes))
}

```
1. 创建POST请求
```
package main

import (
	"bytes"
	"fmt"
	"io/ioutil"
	"net/http"
)

func main() {<!-- -->
	url := "https://www.shirdon.com/comment/add"
	body := `{"userId": 1, "articleId": 1, "comment": 这是一条评论}`
	resp, err := http.Post(url, "application/x-www-form-urlencoded", bytes.NewBuffer([]byte(body)))
	if err != nil {<!-- -->
		fmt.Println("err:", err)
	}
	bytes, err := ioutil.ReadAll(resp.Body)
	fmt.Println(string(bytes))
}

```
1. 创建PUT请求
```
package main

import (
	"fmt"
	"io/ioutil"
	"net/http"
	"strings"
)

func main() {<!-- -->
	url := "https://www.shirdon.com/comment/update"
	payload := strings.NewReader(`{"userId": 1, "articleId": 1, "comment": 这是一条评论}`)
	req, _ := http.NewRequest("PUT", url, payload)
	req.Header.Add("Content-Type", "application/json")
	res, err := http.DefaultClient.Do(req)
	if err != nil {<!-- -->
		fmt.Println("err:", err)
	}
	defer res.Body.Close()
	bytes, err := ioutil.ReadAll(res.Body)
	fmt.Println(string(res))
	fmt.Println(string(bytes))
}

```
1. 创建DELETE请求
```
package main

import (
	"fmt"
	"io/ioutil"
	"net/http"
	"strings"
)

func main() {<!-- -->
	url := "https://www.shirdon.com/comment/delete"
	payload := strings.NewReader(`{"userId": 1, "articleId": 1, "comment": 这是一条评论}`)
	req, _ := http.NewRequest("DELETE", url, payload)
	req.Header.Add("Content-Type", "application/json")
	res, err := http.DefaultClient.Do(req)
	if err != nil {<!-- -->
		fmt.Println("err:", err)
	}
	defer res.Body.Close()
	bytes, err := ioutil.ReadAll(res.Body)
	fmt.Println(string(res))
	fmt.Println(string(bytes))
}

```
1. 请求头设置
```
type Header map[string][]string

```

```
headers := http.Header{<!-- -->"token": {<!-- -->"feeowiwpor23dlspweh"}}
headers.Add("Accept-Charset", "UTF-8")
headers.Set("Host", "www.shirdon.com")
headers.Set("Location", "www.baidu.com")

```

## 2.4 html/template包

text/template处理任意格式的文本，html/template生成可对抗代码注入的安全html文档。

### 2.4.1 模板原理
1. 模板和模板引擎
模板是事先定义好的不变的html文档，模板渲染使用可变数据替换html文档中的标记。 模板用于显示和数据分离（前后端分离）。模板技术，本质是模板引擎利用模板文件和数据生成html文档。
1. Go语言模板引擎- 模板文件后缀名通常为.tmpl和.tpl，UTF-8编码- 模板文件中{<!-- -->{和}}包裹和标识传入数据- 点号（.）访问数据，{<!-- -->{.FieldName}}访问字段- 除{<!-- -->{和}}包裹内容外，其他内容原样输出
使用： （1）定义模板文件 按照相应语法规则去定义。 （2）解析模板文件 创建指定模板名称的模板对象

```
func New(name string) *Template

```

解析模板内容

```
func (t *Template) Parse(src string) (*Template, error)

```

解析模板文件

```
func ParseFiles(filenames...string) (*Template, error)

```

正则匹配解析文件，template.ParaeGlob(“a*”)

```
func ParseGlob(pattern string) (*Template, error)

```

（3）渲染模板文件

```
func (t *Template) Execute(wr io.Writer, data interface{<!-- -->}) error

//配合ParseFiles()函数使用，需指定模板名称
func (t *Template) ExecuteTemplate(wr io.Writer, name string, data interface{<!-- -->}) error

```

### 2.4.2 使用html/template包
1. 第1个模板
template_example.tmpl

```
&lt;!DOCTYPE html&gt;
&lt;html lang="en"&gt;
&lt;head&gt;
    &lt;meta charset="UTF-8"&gt;
    &lt;meta name="viewport" content="width=device-width, initial-scale=1.0"&gt;
    &lt;title&gt;模板使用示例&lt;/title&gt;
&lt;/head&gt;
&lt;body&gt;
   &lt;p&gt;加油，小伙伴， {<!-- -->{ . }} &lt;/p&gt;
&lt;/body&gt;
&lt;/html&gt;

```

```
package main

import (
	"fmt"
	"html/template"
	"net/http"
)

func helloHandleFunc(w http.ResponseWriter, r *http.Request) {<!-- -->
	// 1. 解析模板
	t, err := template.ParseFiles("./template_example.tmpl")
	if err != nil {<!-- -->
		fmt.Println("template parsefile failed, err:", err)
		return
	}
	// 2.渲染模板
	name := "我爱Go语言"
	t.Execute(w, name)
}

func main() {<!-- -->
	http.HandleFunc("/", helloHandleFunc)
	http.ListenAndServe(":8086", nil)
}

```
1. 模板语法
模板语法都包含在{<!-- -->{和}}中间。

```
type UserInfo struct {<!-- -->
	Name string
	Gender string
	Age int
}

func sayHello(w http.ResponseWriter, r *http.Request) {<!-- -->
	tmpl, err := template.ParseFiles("./hello.html")
	if err != nil {<!-- -->
		fmp.Println("create template failed, err:", err)
		return
	}

	user := UserInfo {<!-- -->
		Name: "张三",
		Gender: "男",
		Age: 28,
	}
	tmpl.Execute(w, user)
}

```

```
&lt;!DOCTYPE html&gt;
&lt;html lang="en"&gt;
&lt;head&gt;
    &lt;meta charset="UTF-8"&gt;
    &lt;meta name="viewport" content="width=device-width, initial-scale=1.0"&gt;
    &lt;meta http-equiv="X-UA-Compatible" content="ie=edge"&gt;
    &lt;title&gt;Hello&lt;/title&gt;
&lt;/head&gt;
&lt;body&gt;
   &lt;p&gt;Hello {<!-- -->{.Name}}&lt;/p&gt;
   &lt;p&gt;性别：{<!-- -->{.Gender}}&lt;/p&gt;
   &lt;p&gt;年龄：{<!-- -->{.Age}}&lt;/p&gt;
&lt;/body&gt;
&lt;/html&gt;

```

常用语法：
- 注释
```
{<!-- -->{<!-- -->/* 这是一个注释，不会解析 */}}

```
- 管道（pipeline） 产生数据的操作，{<!-- -->{.Name}}等。支持|链接多个命令，类似UNIX下管道。- 变量 变量捕获管道的执行结果。
```
$variable := pipeline

```
- 条件判断
```
{<!-- -->{<!-- -->if pipeline}} T1 {<!-- -->{<!-- -->end}}
{<!-- -->{<!-- -->if pipeline}} T1 {<!-- -->{<!-- -->else}} T0 {<!-- -->{<!-- -->end}}
{<!-- -->{<!-- -->if pipeline}} T1 {<!-- -->{<!-- -->else if pipeline}} T0 {<!-- -->{<!-- -->end}}

```
- range关键字
```
{<!-- -->{<!-- -->range pipeline}} T1 {<!-- -->{<!-- -->end}}
{<!-- -->{<!-- -->range pipeline}} T1 {<!-- -->{<!-- -->else}} T0 {<!-- -->{<!-- -->end}}

```

```
package main

import (
	"log"
	"os"
	"text/template"
)

func main() {<!-- -->
	//创建一个模版
	rangeTemplate := `
{<!-- -->{if .Kind}}
{<!-- -->{range $i, $v := .MapContent}}
{<!-- -->{$i}} =&gt; {<!-- -->{$v}} , {<!-- -->{$.OutsideContent}}
{<!-- -->{end}}
{<!-- -->{else}}
{<!-- -->{range .MapContent}}
{<!-- -->{.}} , {<!-- -->{$.OutsideContent}}
{<!-- -->{end}}    
{<!-- -->{end}}`

	str1 := []string{<!-- -->"第一次 range", "用 index 和 value"}
	str2 := []string{<!-- -->"第二次 range", "没有用 index 和 value"}

	type Content struct {<!-- -->
		MapContent     []string
		OutsideContent string
		Kind           bool
	}
	var contents = []Content{<!-- -->
		{<!-- -->str1, "第一次外面的内容", true},
		{<!-- -->str2, "第二次外面的内容", false},
	}

	// 创建模板并将字符解析进去
	t := template.Must(template.New("range").Parse(rangeTemplate))

	// 接收并执行模板
	for _, c := range contents {<!-- -->
		err := t.Execute(os.Stdout, c)
		if err != nil {<!-- -->
			log.Println("executing template:", err)
		}
	}
}
/*
//输出
0 =&gt; 第一次 range, 第一次外面的内容
1 =&gt; 用 index 和 value, 第一次外面的内容

第二次 range, 第二次外面的内容
没有用 index 和 value, 第二次外面的内容
*/

```
- with关键字
```
{<!-- -->{<!-- -->with pipeline}} T1 {<!-- -->{<!-- -->end}}
{<!-- -->{<!-- -->with pipeline}} T1 {<!-- -->{<!-- -->else}} T0 {<!-- -->{<!-- -->end}}

```
- 比较函数 比较函数只适用于基本函数（或重定义的基本类型，如type Banance float32），整数和浮点数不能相互比较。 布尔函数将任何类型的零值视为假。 只有eq可以接受2个以上参数。
```
{<!-- -->{<!-- -->eq arg1 arg2 arg3}}
eq
ne
lt
le
gt
ge

```
- 预定义函数
<th align="left">函数名</th><th align="left">功能</th>
|------
<td align="left">and</td><td align="left">返回第1个空参数或最后一个参数，所有参数都执行。and x y等价于if x then y else x</td>
<td align="left">or</td><td align="left">返回第1个非空参数或最后一个参数，所有参数都执行。and x y等价于if x then y else x</td>
<td align="left">not</td><td align="left">非</td>
<td align="left">len</td><td align="left">长度</td>
<td align="left">index</td><td align="left">index y 1 2 3, index[1][2][3]</td>
<td align="left">print</td><td align="left">fmt.Sprint</td>
<td align="left">printf</td><td align="left">fmt.Sprintf</td>
<td align="left">println</td><td align="left">fmt.Sprintln</td>
<td align="left">html</td><td align="left">html逸码等价表示</td>
<td align="left">urlquery</td><td align="left">可嵌入URL查询的逸码等价表示</td>
<td align="left">js</td><td align="left">JavaScript逸码等价表示</td>
<td align="left">call</td><td align="left">call func a b, func(a, b);1或2个返回值，第2个为error，非nil会中断并返回给调用者。</td>
- 自定义函数
模板对象t的函数字典加入funcMap内的键值对。funcMap某个值不是函数类型，或该函数类型不符合要求，会panic。返回*Template便于链式调用。

```
func (t *Template) Funcs(funcMap FuncMap) *Template

```

FuncMap映射函数要求1或2个返回值，第2个为error，非nil会中断并返回给调用者。

```
type FuncMap map[string]interface{<!-- -->}

```

```
package main

import (
	"fmt"
	"html/template"
	"io/ioutil"
	"net/http"
)

func Welcome() string {<!-- --> //没参数
	return "Welcome"
}

func Doing(name string) string {<!-- --> //有参数
	return name + ", Learning Go Web template "
}

func sayHello(w http.ResponseWriter, r *http.Request) {<!-- -->
	htmlByte, err := ioutil.ReadFile("./funcs.html")
	if err != nil {<!-- -->
		fmt.Println("read html failed, err:", err)
		return
	}
	// 自定义一个匿名模板函数
	loveGo := func() (string) {<!-- -->
		return "欢迎一起学习《Go Web编程实战派从入门到精通》"
	}
	// 采用链式操作在Parse()方法之前调用Funcs添加自定义的loveGo函数
	tmpl1, err := template.New("funcs").Funcs(template.FuncMap{<!-- -->"loveGo": loveGo}).Parse(string(htmlByte))
	if err != nil {<!-- -->
		fmt.Println("create template failed, err:", err)
		return
	}
	funcMap := template.FuncMap{<!-- -->
		//在FuncMap中声明相应要使用的函数，然后就能够在template字符串中使用该函数
		"Welcome": Welcome,
		"Doing":   Doing,
	}
	name := "Shirdon"
	tmpl2, err := template.New("test").Funcs(funcMap).Parse("{<!-- -->{Welcome}}&lt;br/&gt;{<!-- -->{Doing .}}")
	if err != nil {<!-- -->
		panic(err)
	}

	// 使用user渲染模板，并将结果写入w
	tmpl1.Execute(w, name)
	tmpl2.Execute(w, name)
}

func main() {<!-- -->
	http.HandleFunc("/", sayHello)
	http.ListenAndServe(":8087", nil)
}

```

funcs.html

```
&lt;!DOCTYPE html&gt;
&lt;html lang="en"&gt;
	&lt;head&gt;
		&lt;meta charset="UTF-8"&gt;
		&lt;meta name="viewport" content="width=device-width, initial-scale=1.0"&gt;
		&lt;meta http-equiv="X-UA-Compatible" content="ie=edge"&gt;
		&lt;title&gt;tmpl test&lt;/title&gt;
	&lt;/head&gt;
	&lt;body&gt;
		&lt;h1&gt;{<!-- -->{loveGo}}&lt;/h1&gt;
	&lt;/body&gt;
&lt;/html&gt;

```
- 模板嵌套 可以通过文件嵌套和define定义
```
{<!-- -->{<!-- -->define "name"}} T {<!-- -->{<!-- -->end}}

```

```
{<!-- -->{<!-- -->template "name"}}
{<!-- -->{<!-- -->template "name" pipeline}}

```

```
{<!-- -->{<!-- -->block "name" pipeline}} T {<!-- -->{<!-- -->end}}
//等价于
{<!-- -->{<!-- -->define "name"}} T {<!-- -->{<!-- -->end}}
{<!-- -->{<!-- -->template "name" pipeline}}

```

t.html

```
&lt;!DOCTYPE html&gt;
&lt;html lang="en"&gt;
&lt;head&gt;
    &lt;meta charset="UTF-8"&gt;
    &lt;meta name="viewport" content="width=device-width, initial-scale=1.0"&gt;
    &lt;meta http-equiv="X-UA-Compatible" content="ie=edge"&gt;
    &lt;title&gt;tmpl test&lt;/title&gt;
&lt;/head&gt;
&lt;body&gt;
&lt;h1&gt;测试嵌套template语法&lt;/h1&gt;
&lt;hr&gt;
{<!-- -->{template "ul.html"}}
&lt;hr&gt;
{<!-- -->{template "ol.html"}}
&lt;/body&gt;
&lt;/html&gt;
{<!-- -->{define "ol.html"}}
&lt;h1&gt;这是ol.html&lt;/h1&gt;
&lt;ol&gt;
    &lt;li&gt;I love Go&lt;/li&gt;
    &lt;li&gt;I love java&lt;/li&gt;
    &lt;li&gt;I love c&lt;/li&gt;
&lt;/ol&gt;
{<!-- -->{end}}

```

ul.html

```
&lt;ul&gt;
    &lt;li&gt;注释&lt;/li&gt;
    &lt;li&gt;日志&lt;/li&gt;
    &lt;li&gt;测试&lt;/li&gt;
&lt;/ul&gt;

```

```
package main

import (
	"fmt"
	"html/template"
	"net/http"
)

//定义一个UserInfo结构体
type UserInfo struct {<!-- -->
	Name string
	Gender string
	Age int
}

func tmplSample(w http.ResponseWriter, r *http.Request) {<!-- -->
	tmpl, err := template.ParseFiles("./t.html", "./ul.html")
	if err != nil {<!-- -->
		fmt.Println("create template failed, err:", err)
		return
	}
	user := UserInfo{<!-- -->
		Name:   "张三",
		Gender: "男",
		Age:    28,
	}
	tmpl.Execute(w, user)
	fmt.Println(tmpl)
}

func main() {<!-- -->
	http.HandleFunc("/", tmplSample)
	http.ListenAndServe(":8087", nil)
}

```
