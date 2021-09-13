#《Go Web编程实战派——从入门到精通》学习笔记之第3章 接收和处理Go Web请求


### 《Go Web编程实战派——从入门到精通》学习笔记之第3章 接收和处理Go Web请求
- - <ul><li>- - <ul><li>- - - - - - - - - - - - - 


# 第3章 接收和处理Go Web请求

## 3.1 简单Go Web服务器

```
package main

import (
	"fmt"
	"log"
	"net/http"
)

func helloWorld(w http.ResponseWriter, r *http.Request) {<!-- -->
	fmt.Fprintf(w, "Hello Go Web!")
}

func main() {<!-- -->
	http.HandleFunc("/hello", helloWorld)
	if err := http.ListenAndServe(":8081", nil); err != nil {<!-- -->
		log.Fatal(err)
	}
}

```

Go Web服务器请求和响应流程：
1. 客户端发送请求；1. 服务器端的多路复用器收到请求；1. 多路复用器根据请求的URL找到注册的处理器，转交请求给处理器；1. 处理器执行程序逻辑，如必要，与数据库交互，得到处理结果；1. 处理器调用模板引擎将指定模板和上一步结果渲染成客户端可识别的数据格式（通常HTML）；1. 服务器将数据通过HTTP响应返回给客户端；1. 客户端拿到数据，执行对应的操作（例如渲染出来呈现给用户）。
## 3.2 接收请求

### 3.2.1 ServeMux和DefaultServeMux
1. ServeMux和DefaultServeMux简介
多路复用器用于转发请求到处理器。

结构体ServeMux中，包含URL到相应处理器的映射。它会根据请求URL找出最匹配的URL，然后调用对应处理器的ServeHTTP()方法来处理请求。

DefaultServeMux是ServeMux的一个实例，默认的多路复用器。

```
var DefaultServeMux = &amp;defaultServeMux
var defaultServeMux ServeMux

```

```
//为指定URL注册处理器
func HandleFunc(pattern string, handler func(ResponseWrite, *Request)) {<!-- -->
	DefaultServeMux.HandleFunc(pattern, handler)
}

```

```
func Handle(pattern string, handler Handler) {<!-- -->
	DefaultServeMux.Handle(pattern, handler)
}

```

```
func (sh serverHandler) ServeHTTP(rw ResponseWriter, req *Request) {<!-- -->
	handler := sh.srv.Handler
	if handler == nil {<!-- -->
		handler = DefaultServeMux
	}
	handler.ServeHTTP(rw, req)
}

```

```
package main

import (
	"fmt"
	"net/http"
)

type handle1 struct{<!-- -->}

func (h1 *serverHandler) ServeHTTP(w ResponseWriter, r *Request) {<!-- -->
	fmt.Fprintf(w, "hi, handle1")
}

type handle2 struct{<!-- -->}

func (h2 *serverHandler) ServeHTTP(w ResponseWriter, r *Request) {<!-- -->
	fmt.Fprintf(w, "hi, handle2")
}

func main() {<!-- -->
	handle1 := handle1{<!-- -->}
	handle2 := handle2{<!-- -->}

	server := http.Server {<!-- -->
		Addr: "0.0.0.0:8085",
		Handler: nil,
	}

	http.Handle("/handle1", &amp;handle1)
	http.Handle("/handle2", &amp;handle2)
	server.ListenAndServe()
}

```

```
package main

import (
	"fmt"
	"log"
	"net/http"
)

func hi(w ResponseWriter, r *Request) {<!-- -->
	fmt.Fprintf(w, "hi, web")
}

func main() {<!-- -->
	mux := http.NewServeMux()
	mux.HandleFunc("/", hi)

	server := &amp;http.Server {<!-- -->
		Addr: "8081",
		Handler: mux,
		ReadTimeot: 5*time.Second,
		WriteTimeot: 5*time.Second,
	}

	if err := server.ListenAndServe(); err != nil {<!-- -->
		log.Fatal(err)
	}
}

```
1. ServeMux的URL路由匹配
```
package main

import (
	"fmt"
	"log"
	"net/http"
)

func indexHandler(w http.ResponseWriter, r *http.Request) {<!-- -->
	fmt.Fprintf(w, "欢迎来到Go Web首页！处理器为：indexHandler！")
}

func hiHandler(w http.ResponseWriter, r *http.Request) {<!-- -->
	fmt.Fprintf(w, "欢迎来到Go Web首页！处理器为：hiHandler！")
}

func webHandler(w http.ResponseWriter, r *http.Request) {<!-- -->
	fmt.Fprintf(w, "欢迎来到Go Web首页！处理器为：webHandler！")
}

func main() {<!-- -->
	mux := http.NewServeMux()
	mux.HandleFunc("/", indexHandler)	// "/", "/hi/"
	mux.HandleFunc("/hi", hiHandler)	// "/hi"
	mux.HandleFunc("/hi/web", webHandler)	// "/hi/web"
	
	server := &amp;http.Server {<!-- -->
		Addr: ":8083",
		Handler: mux,
	}

	if err := server.ListenAndServe(); err != nil {<!-- -->
		log.Fatal(err)
	}
}

```

```
package main

import (
	"fmt"
	"log"
	"net/http"
)

func hiHandler(w http.ResponseWriter, r *http.Request) {<!-- -->
	fmt.Fprintf(w, "Hi, Go HandleFunc")
}

type welcomeHandler struct {<!-- -->
	Name string
}

func (h welcomeHandler) ServeHTTP(w http.ResponseWriter, r *http.Request) {<!-- -->
	fmt.Fprintf(w, "Hi, %s", h.Name)
}

func main() {<!-- -->
	mux := http.NewServeMux()
	mux.HandleFunc("/hi", hiHandler)
	mux.Handle("/welcome/goweb", welcomeHandler{<!-- -->Name: "test"})
	
	server := &amp;http.Server {<!-- -->
		Addr: ":8085",
		Handler: mux,
	}

	if err := server.ListenAndServe(); err != nil {<!-- -->
		log.Fatal(err)
	}
}

```
1. HttpRouter简介
使用变量实现URL模式匹配。

```
package main

import (
	"log"
	"net/http"
	"github.com/julienschmidt/httprouter"
)

func Index(w http.ResponseWriter, r *http.Request, _ httprouter.Params) {<!-- -->
	w.Write([]byte("Index"))
}

func main() {<!-- -->
	router := httprouter.New()
	router.GET("/", Index)
	log.Fatal(http.ListenAndServe(":8082", router))
}

```

```
package main

import (
	"github.com/julienschmidt/httprouter"
	"net/http"
)

func main() {<!-- -->
	router := httprouter.New()
	router.GET("/default", func(w http.ResponseWriter, r *http.Request, _ httprouter.Params) {<!-- -->
		w.Write([]byte("default get"))
	})
	router.POST("/default", func(w http.ResponseWriter, r *http.Request, _ httprouter.Params) {<!-- -->
		w.Write([]byte("default post"))
	})
	//精确匹配
	router.GET("/user/name", func(w http.ResponseWriter, r *http.Request, p httprouter.Params) {<!-- -->
		w.Write([]byte("user name:" + p.ByName("name")))
	})
	//匹配所有
	router.GET("/user/*name", func(w http.ResponseWriter, r *http.Request, p httprouter.Params) {<!-- -->
		w.Write([]byte("user name:" + p.ByName("name")))
	})
	http.ListenAndServe(":8083", router)
}

```

```
package main

import (
	"log"
	"net/http"
	"github.com/julienschmidt/httprouter"
)

type HostMap map[string]http.Handler

func (hs HostMap) ServeHTTP(w http.ResponseWriter, r *http.Request) {<!-- -->
	//根据域名获取对应的Handler路由，然后调用处理（分发机制）
	if handler := hs[r.Host]; handler != nil {<!-- -->
		handler.ServeHTTP(w, r)
	} else {<!-- -->
		http.Error(w, "Forbidden", 403)
	}
}

func main() {<!-- -->
	userRouter := httprouter.New()
	userRouter.GET("/", func(w http.ResponseWriter, r *http.Request, p httprouter.Params) {<!-- -->
		w.Write([]byte("sub1"))
	})

	dataRouter := httprouter.New()
	dataRouter.GET("/", func(w http.ResponseWriter, r *http.Request, _ httprouter.Params) {<!-- -->
		w.Write([]byte("sub2"))
	})

	//分别用于处理不同的二级域名
	hs := make(HostMap)
	hs["sub1.localhost:8888"] = userRouter
	hs["sub2.localhost:8888"] = dataRouter

	log.Fatal(http.ListenAndServe(":8888", hs))
}

```

```
package main

import (
	"log"
	"net/http"
	"github.com/julienschmidt/httprouter"
)

func main() {<!-- -->
	router := httprouter.New()
	//访问静态文件
	router.ServeFiles("/static/*filepath", http.Dir("./files"))
	log.Fatal(http.ListenAndServe(":8086", router))
}

```

```
package main

import (
	"fmt"
	"github.com/julienschmidt/httprouter"
	"log"
	"net/http"
)

func Index(w http.ResponseWriter, r *http.Request, _ httprouter.Params) {<!-- -->
	panic("error")
}

func main() {<!-- -->
	router := httprouter.New()
	router.GET("/", Index)
	//捕获异常
	router.PanicHandler = func(w http.ResponseWriter, r *http.Request, v interface{<!-- -->}) {<!-- -->
		w.WriteHeader(http.StatusInternalServerError)
		fmt.Fprintf(w, "error:%s", v)
	}
	log.Fatal(http.ListenAndServe(":8085", router))
}

```

```
type Router struct {<!-- -->
	RedirecTrailingSlash bool
	RedirectFixedPath bool
	HandleMethodNotAllowed bool
	HandleOPTIONS bool
	NotFound http.Handler
	MethodNotAllowed http.Handler
	PanicHandler func(http.ResponseWriter, *http.Request, interfaace{<!-- -->})
} 

```

### 3.2.2 处理器和处理器函数

处理器是实现了Handler接口的结构。

```
type Handler interface {<!-- -->
	func ServeHTTP(w ResponseWriter, r *Request)
}

```

```
package main

import (
	"fmt"
	"log"
	"net/http"
)

type WelcomeHandler struct {<!-- -->
	Language string
}

func (h WelcomeHandler) ServeHTTP(w http.ResponseWriter, r *http.Request) {<!-- -->
	fmt.Fprintf(w, "%s", h.Language)
}

func main() {<!-- -->
	mux := http.NewServeMux()
	mux.Handle("/cn", WelcomeHandler{<!-- -->Language: "欢迎一起来学Go Web!"})
	mux.Handle("/en", WelcomeHandler{<!-- -->Language: "Welcome you, let's learn Go Web!"})

	server := &amp;http.Server {<!-- -->
		Addr:   ":8082",
		Handler: mux,
	}

	if err := server.ListenAndServe(); err != nil {<!-- -->
		log.Fatal(err)
	}
}

```

处理器函数实现了匿名函数func(w http.ResponseWriter, r *http.Request)。

```
func (mux *ServeMux) HandleFunc(pattern string, handler func(http.ResponseWriter, *http.Request)) {<!-- -->
	if handler == nil {<!-- -->
		panic("http: nil handler")
	}
	mux.Handle(pattern, HandlerFunc(handler))
}

```

```
type HandlerFunc func(w ResponseWriter, r *Request)
func (f HandlerFunc) ServeHTTP(w ResponseWriter, r *Request) {<!-- -->
	f(w, r)
}

```

### 3.2.3 串联处理器和处理器函数

```
package main

import (
	"fmt"
	"net/http"
	"reflect"
	"runtime"
	"time"
)

func main() {<!-- -->
	http.HandleFunc("/", log(index))
	http.ListerAndServe(":8087", nil)
}

func log2(h http.Handler) http.Handler {<!-- -->
	return http.Handler(func(w http.ResponseWriter, r *http.Request) {<!-- -->
		h.ServerHTTP(w, r)
	})
}

func log(h http.HandlerFunc) http.HandlerFunc {<!-- -->
	return func(w http.ResponseWriter, r *http.Request) {<!-- -->
		fmt.Printf("time: %s|handlerfunc: %s\n", time.Now().String(), runtime.FuncForPC(reflect.ValueOf(h).Pointer()).Name())
		h(w, r)
	}
}

func index(w http.ResponseWriter, r *http.Request) {<!-- -->
	fmt.Fprintf(w, "hello index!!")
}

```

### 3.2.4 构建模型

构建模型增删查改数据库。

```
create table if not exists user (
	uid bigint auto_increment primary key,
  	name varchar(20) default '' null,
  	phone varchar(20) default '' null
) charset=utf8mb4 ;

```

```
package model

import (
	"database/sql"
	"fmt"
	_ "github.com/go-sql-driver/mysql"
)

var DB *sql.DB

type User struct {<!-- -->
	Uid   int
	Name  string
	Phone string
}

//初始化数据库连接
func init()  {<!-- -->
	DB, _ = sql.Open("mysql",
		"root:123456@tcp(127.0.0.1:3306)/chapter3")
}

//获取用户信息
func GetUser(uid int) (u User) {<!-- -->
	// 非常重要：确保QueryRow之后调用Scan方法，否则持有的数据库链接不会被释放
	err := DB.QueryRow("select uid, name, phone from `user` where uid=?", uid).Scan(&amp;u.Uid, &amp;u.Name, &amp;u.Phone)
	if err != nil {<!-- -->
		fmt.Printf("scan failed, err:%v\n", err)
		return
	}
	return u
}

```

### 3.2.5 生成HTML表单

view/t3.html

```
&lt;!DOCTYPE html&gt;
&lt;html&gt;
  &lt;head&gt;
    &lt;meta http-equiv="Content-Type" content="text/html; charset=utf-8"&gt;
    &lt;title&gt;Welcome to my page&lt;/title&gt;
  &lt;/head&gt;
  &lt;body&gt;
    &lt;ul&gt;
    {<!-- -->{range .}}
		&lt;h1 style="text-align:center"&gt;{<!-- -->{.}}&lt;/h1&gt;
    {<!-- -->{end}}
        &lt;h1 style="text-align:center"&gt;Welcome to my page&lt;/h1&gt;
        &lt;p style="text-align:center"&gt;this is the user info page&lt;/p&gt;
    &lt;/ul&gt;
  &lt;/body&gt;
&lt;/html&gt;

```

```
package controller

import (
	"fmt"
	"gitee.com/shirdonl/goWebActualCombat/chapter3/model"
	"html/template"
	"net/http"
	"strconv"
)

type UserController struct {<!-- -->
}

func (c UserController) GetUser(w http.ResponseWriter, r *http.Request)  {<!-- -->
	query := r.URL.Query()
	uid, _ := strconv.Atoi(query["uid"][0])

	//此处调用模型从数据库中获取数据
	user := model.GetUser(uid)
	fmt.Println(user)

	t, _ := template.ParseFiles("view/t3.html")
	userInfo := []string{<!-- -->user.Name, user.Phone}
	t.Execute(w, userInfo)
}

```

```
package main

import (
	"gitee.com/shirdonl/goWebActualCombat/chapter3/controller"
	"log"
	"net/http"
)

func main() {<!-- -->
	http.HandleFunc("/getUser", controller.UserController{<!-- -->}.GetUser)
	if err := http.ListenAndServe(":8088", nil); err != nil {<!-- -->
		log.Fatal(err)
	}
}

```

## 3.3 处理请求

### 3.3.1 了解Request结构体

```
type Request struct {<!-- -->
	Method string
	URL *url.URL
	Proto string
	ProtoMajor int
	ProtoMinor int
	Header Header
	Body io.ReadCloser
	GetBody func() (io.ReadCloser, error)
	ContentLength int64
	TransferEncoding []string
	Close bool
	Host string
	Form url.Values
	PostForm url.Values
	MultipartForm *multipart.Form
	Trailer Header
	RemoteAddr string
	RequestURI string
	TLS *tls.ConnectionState
	Cancel &lt;-chan struct{<!-- -->}
	Response *Response
	ctx context.Context
}

```

```
package main

import (
	"fmt"
	"log"
	"net/http"
	"strings"
)

func request(w http.ResponseWriter, r *http.Request) {<!-- -->
	fmt.Println("Request解析")
	fmt.Println("method:", r.Method)
	fmt.Println("RequestURI:", r.RequestURI)
	fmt.Println("URL.Path:", r.URL.Path)
	fmt.Println("URL.RawQuery:", r.URL.RawQuery)
	fmt.Println("URL.Fragment:", r.URL.Fragment)
	fmt.Println("Proto:", r.Proto)
	fmt.Println("ProtoMajor:", r.ProtoMajor)
	fmt.Println("ProtoMinor:", r.ProtoMinor)

	for k, v := range r.Header {<!-- -->
		for _, vv := range v {<!-- -->
			fmt.Println("header key:" + k + " value:" + vv)
		}
	}
	
	isMultipart := false
	for _, v := r.Header["Content-Type"] {<!-- -->
		if strings.Index(v, "multipart/form-data") != -1 {<!-- -->
			isMultipart = true
		}
	}
	if isMultipart == true {<!-- -->
		r.ParseMultipartForm(128)
		fmt.Println("解析方式：ParseMultipartForm")
	} else {<!-- -->
		r.ParseForm()
		fmt.Println("解析方式：ParseForm")
	}
	
	fmt.Println("ContentLength:", r.ContentLength)
	fmt.Println("Close:", r.Close)
	fmt.Println("Host:", r.Host)
	fmt.Println("RemoteAddr:", r.RemoteAddr)
	fmt.Fprintf(w, "hello, let's go!")
}

func main() {<!-- -->
	http.HandleFunc("hello", request)
	if err := http.ListenAndServe(":8081", nil); err != nil {<!-- -->
		log.Fatal("ListenAndServe:", err)
	}
}

```

### 3.3.2 请求URL

```
scheme://[userinfo@]host/path[?query][#fragment]

```

```
type URL struct {<!-- -->
	Scheme string
	Opaque string
	User *Userinfo
	Host string
	Path string
	RawPath string
	ForceQuery bool
	RawQuery string
	Fragment string
}

```

```
func Parse(rawurl string) (*URL, error)

```

```
package main

import (
	"net/url"
	"fmt"
)

func main() {<!-- -->
	path := "http://localhost:8082/article?id=1"
	p, _ := url.Parse(path)
	fmt.Println(p.Host)
	fmt.Println(p.User)
	fmt.Println(p.RawQuery)
	fmt.Println(p.RequestURI())
}

```

### 3.3.3 请求头

```
type Header map[string][]string

```

```
func (h Header) Get(key string)
func (h Header) Set(key, value string)
func (h Header) Add(key, value string)
func (h Header) Del(key string)
func (h Header) Write(w io.Writer) error

```

```
type Greeting struct {<!-- -->
	Message string `json:"message"`
}

func Hello(w http.ResponseWriter, r *http.Request) {<!-- -->
	greeting := Greeting{<!-- -->
		"一起学习"
	}

	message, _ := json.Marshal(greeting)
	w.Header().set("Content-Type", "application/json")
	w.Write(message)
}

func main() {<!-- -->
	http.HandleFunc("/", Hello)
	err := http.ListenAndServe(":8086", nil)
	if err != nil {<!-- -->
		fmt.Println(err)
	}
}

```

### 3.3.4 请求体

```
type ReadCloser interface {<!-- -->
	Reader
	Closer
}

```

```
type Readerinterface {<!-- -->
	Read(p []byte) (n int, err error)
}

```

```
package main

import (
	"fmt"
	"net/http"
)

func getBody(w http.ResponseWriter, r *http.Request) {<!-- -->
	len := r.ConterntLength
	body := make([]byte, len)
	r.Body.Read(body)
	fmt.Fprintln(w, string(body))
}

func main() {<!-- -->
	http.HandleFunc("/getBody", getBody)
	err := http.ListenAndServe(":8082", nil)
	if err != nil {<!-- -->
		fmt.Println(err)
	}
}

```

### 3.3.5 处理HTML表单

HTML表单的enctype属性决定表单的内容类型（content type）。 （1）application/x-www-form-urlencoded 表单的默认编码，表单中的数据编码为键值对，且所有字符会被编码（空格转换为“+”号，特殊符号转换为ASCII HEX值）。
- method方法为GET时，表单数据转换为"nam1=value1&amp;name2=value2&amp;…"，拼接到URL后面，"?"分隔。加密采用的编码字符集取决于浏览器。- method方法为POST时，数据添加到HTTP Body中，浏览器根据网页的ContenrType(“text/html; charset=UTF-8”)对表单数据编码。
（2）multipart/form-data 上传二进制文件，不对字符编码，POST方式。对表单以控件为单位分隔，每部分加上Content-Disposition(form-data|file)、Content-Type(默认text/plain)、name(控件name)等信息，并加上分隔符(边界boundary)。

（3）text/plain 向服务器传递大量纯文本信息，空格转换为加号（+），不对特殊字符编码。

Form字段支持URL编码，键值来源是URL和表单。 PostForm字段支持URL编码，键值来源是表单，只用于获取表单键值。

```
func process(w http.ResponseWriter, r *http.Request) {<!-- -->
	r.ParseForm()
	fmt.Fprintln(w, "表单键值对和URL键值对：", r.Form)
	fmt.Fprintln(w, "表单键值对：", r.PostForm)
}

```

```
&lt;!DOCTYPE html&gt;
&lt;html lang="en"&gt;
&lt;head&gt;
	&lt;meta http-equiv="Content-Type" content="text/html" charset="UTF-8"&gt;
	&lt;title&gt;Form提交&lt;/title&gt;
&lt;/head&gt;
&lt;body&gt;
&lt;form action="http://127.0.0.1:8089?name=go&amp;color=green" method="post" enctype="application/x-www-form-urlencoded"&gt;
	&lt;input type="text" name="name" value="shirdon"/&gt;
	&lt;input type="text" name="color" value="green"/&gt;
	&lt;input type="submit"/&gt;
&lt;/form&gt;
&lt;/body&gt;
&lt;/html&gt;

```

MultipartForm字段支持multipart/form-data编码，键值来源是表单，用于文件上传。

```
func dataProcess(w http.ResponseWriter, r *http.Request) {<!-- -->
	r.ParseMultipartForm(1024)
	fmt.Fprintln(w, "表单键值对：", r.MultipartForm)
}

```

```
&lt;!DOCTYPE html&gt;
&lt;html lang="en"&gt;
&lt;head&gt;
	&lt;meta http-equiv="Content-Type" content="text/html" charset="UTF-8"&gt;
	&lt;title&gt;upload上传文件&lt;/title&gt;
&lt;/head&gt;
&lt;body&gt;
&lt;form action="http://localhost:8089/file" method="post" enctype="multipart/form-data"&gt;
	&lt;input type="file" name="uploaded"/&gt;
	&lt;input type="submit"/&gt;
&lt;/form&gt;
&lt;/body&gt;
&lt;/html&gt;

```

```
func upload(w http.ResponseWriter, r *http.Request) {<!-- -->
	if r.Method == "GET" {<!-- -->
		t, _ := template.ParseFiles("upload.html")
		t.Execute(w, nil)
	} else {<!-- -->
		r.ParseMultipartForm(4096)
		fileHeader := r.MultipartForm.File["uploaded"][0]
		file, err := fileHeader.Open()
		if err != nil {<!-- -->
			fmt.Println("error")
			return
		}
		data, err := ioutil.ReadAll(file)
		if err != nil {<!-- -->
			fmt.Println("error")
			return
		}
		fmt.Fprintln(w, string(data))
	}
}

```

### 3.3.6 ResponseWriter原理

```
type ResponseWriter interface {<!-- -->
	Header() Header
	Write([]byte) (int, error)
	WriteHeader(statusCode int)
}

```

```
func (c *conn) readRequest(ctx context.Context) (w *response, err error) {<!-- -->
	w = &amp;response{<!-- -->
		conn:	c,
		cancelCtx:	cancelCtx,
		req:	req,
		reqBody:	req.Body,
		handlerHeader:	make(Header),
		contentLength:	-1,
		closeNotifyCh:	make(chan bool, 1),
		wants10KeepAlive:	req.wantsHttp10KeepAlive(),
		wantsClose:	req.wantsClose(),
	}
	if isH2Upgrade {<!-- -->
		w.closeAfterReply = true
	}
	w.cr.res = w
	w.w = newBufioWriterSize(&amp;w.cw, bufferBeforeChunkingSizw)
	return w, nil
}

```

WriteHeader()

```
package main

import (
	"fmt"
	"net/http"
)

func noAuth(w http.ResponseWriter, r *http.Request) {<!-- -->
	w.WriteHeader(401)	//默认200
	fmt.Fprintln(w, "未授权，认证后才能访问该接口！")
}

func main() {<!-- -->
	http.HandleFunc("/noAuth", noAuth)
	err := http.ListenAndServe(":8086", nil)
	if err != nil {<!-- -->
		fmt.Println(err)
	}
}

```

Header()

```
package main

import (
	"fmt"
	"net/http"
)

func Redirect(w http.ResponseWriter, r *http.Request)  {<!-- -->
	// 设置一个 301 重定向，重定向无需响应体。
	w.Header().Set("Location", "https://www.shirdon.com")
	w.WriteHeader(301)	//WriteHeader()调用后，无法设置响应头。
}

func main() {<!-- -->
	http.HandleFunc("/redirect", Redirect)
	err := http.ListenAndServe(":8086", nil)
	if err != nil {<!-- -->
		fmt.Println(err)
	}
}

```

Write()

```
package main

import (
	"fmt"
	"net/http"
)

func Welcome(w http.ResponseWriter, r *http.Request)  {<!-- -->
	w.Write([]byte("你好～，欢迎一起学习《Go Web编程实战派从入门到精通》！"))
}

func main() {<!-- -->
	http.HandleFunc("/welcome", Welcome)
	err := http.ListenAndServe(":8086", nil)
	if err != nil {<!-- -->
		fmt.Println(err)
	}
}

```

响应头中Content-Type根据传入数据自行判断

```
package main

import (
	"fmt"
	"net/http"
)

func Home(w http.ResponseWriter, r *http.Request)  {<!-- -->
	html := `&lt;html&gt; 
        &lt;head&gt;
            &lt;title&gt;Write方法返回HTML文档&lt;/title&gt;
        &lt;/head&gt; 
        &lt;body&gt;
            &lt;h1&gt;你好，欢迎一起学习《Go Web编程实战派从入门到精通》
        &lt;/body&gt; 
    &lt;/html&gt;`
	w.Write([]byte(html))
}

func main() {<!-- -->
	http.HandleFunc("/", Home)
	err := http.ListenAndServe(":8086", nil)
	if err != nil {<!-- -->
		fmt.Println(err)
	}
}

```

```
package main

import (
	"encoding/json"
	"fmt"
	"net/http"
)

type Greeting struct {<!-- -->
	Message string `json:"message"`
}
func Hello(w http.ResponseWriter, r *http.Request)  {<!-- -->
	// 返回 JSON 格式数据
	greeting := Greeting{<!-- -->
		"欢迎一起学习《Go Web编程实战派从入门到精通》",
	}
	message, _ := json.Marshal(greeting)
	w.Header().Set("Content-Type", "application/json")
	w.Write(message)
}

func main() {<!-- -->
	http.HandleFunc("/", Hello)
	err := http.ListenAndServe(":8086", nil)
	if err != nil {<!-- -->
		fmt.Println(err)
	}
}

```

## 3.4 了解session和cookie

### 3.4.1 session和cookie简介
1. session和cookie
session和cookie用于弥补HTTP的无状态特性（每次请求无记录）。 （1）session是什么 服务器端为客户端请求开辟的内存空间是session对象，存储结构为ConcurrentHashMap，可以存储会话操作记录。 （2）session判断同一会话 服务器端第一次收到请求，开辟session空间（创建session对象），同时生成sessionId，通过"Set-Cookie: JSESSIONID=XXXXXX"发给客户端。

客户端收到响应后，设置JSESSIONID=XXXXXX的cookie信息，cookie过期时间为浏览器会话结束。

以后客户端请求，添加cookie信息（含sessionId），服务器端读取请求头中cookie信息，获得JSESSIONID，sessionId。 （3）session缺点 若服务器A存储session（负载均衡），访问转发到服务器B，session失效。 （4）cookie是什么 服务器端发送到客户端Web浏览器的一小块数据，含Web cookie和浏览器cookie。

用途： 会话管理：登录、购物车、游戏得分等 个性化：用户偏好、主题等设置 追踪：记录和分析用户行为

cookie用作一般客户端存储，随每个请求发送，降低性能。 （5）session和cookie区别 客户端可以选择禁用cookie，无法禁用服务器端的session。 session能够存储任意类型的对象，cookie只能存储String类型对象。
1. 创建cookie
（1）Set-Cookie Set-Cookie将cookie从服务器端发送到客户端。 （2）会话cookie 客户端关闭时cookie会被删除，但Web浏览器可能会使用会话还原。 （3）永久性cookie 不会在客户端关闭时过期，而是到达指定日期（Expires）或特定时间长度（Max-Ae）后过期。 （4）安全cookie HTPPS协议通过加密发送到服务器。
1. cookie的作用域
Domain和Path标识定义cookie应被发给哪些URL。Domain指定主机，一般包含子域名（Domain=baidu.com，包含news.baidu.com），默认当前主机（不含子域名）；Path=/test,匹配/test, /test/news/, /test/news/id。

### 3.4.2 cookie

```
type Cookie struct {<!-- -->
	Name string
	Value string
	Path string
	Domain string
	Expires time.Time
	RawExpires string
	MaxAge int
	secure bool
	HttpOnly bool
	Raw string
	Unparsed []string
}

```
1. 这种cookie
```
package main

import (
	"fmt"
	"net/http"
)

func testHandle(w http.ResponseWriter, r *http.Request) {<!-- -->
	c, err := r.Cookie("test_cookie")
	fmt.Printf("cookie:%#v, err:%v\n", c, err)

	cookie := &amp;http.Cookie{<!-- -->
		Name:   "test_cookie",
		Value:  "krrsklHhefUUUFSSKLAkaLlJGGQEXZLJP",
		MaxAge: 3600,
		Domain: "localhost",
		Path:   "/",
	}

	http.SetCookie(w, cookie)

	//在具体数据返回之前设置cookie，否则cookie种不上
	w.Write([]byte("hello"))
}

func main() {<!-- -->
	http.HandleFunc("/", testHandle)
	http.ListenAndServe(":8085", nil)
}

```
1. 获取cookie
```
package main

import (
	"fmt"
	"io/ioutil"
	"net/http"
	"net/url"
	"strings"
)

func main()  {<!-- -->
	CopeHandle("GET", "https://www.baidu.com", "")
}

//http请求处理
func CopeHandle(method, urlVal, data string)  {<!-- -->
	client := &amp;http.Client{<!-- -->}
	var req *http.Request

	if data == "" {<!-- -->
		urlArr := strings.Split(urlVal,"?")
		if len(urlArr)  == 2 {<!-- -->
			urlVal = urlArr[0] + "?" + getParseParam(urlArr[1])
		}
		req, _ = http.NewRequest(method, urlVal, nil)
	}else {<!-- -->
		req, _ = http.NewRequest(method, urlVal, strings.NewReader(data))
	}

	cookie := &amp;http.Cookie{<!-- -->Name: "X-Xsrftoken", Value: "abccadf41ba5fasfasjijalkjaqezgbea3ga", HttpOnly: true}
	req.AddCookie(cookie)

	//添加header
	req.Header.Add("X-Xsrftoken","aaab6d695bbdcd111e8b681002324e63af81")

	resp, err := client.Do(req)

	if err != nil {<!-- -->
		fmt.Println(err)
	}
	defer resp.Body.Close()
	b, _ := ioutil.ReadAll(resp.Body)
	fmt.Println(string(b))
}

//将get请求的参数进行转义
func getParseParam(param string) string  {<!-- -->
	return url.PathEscape(param)
}

```

### 3.4.3 session
1. 定义Session接口
```
type Session interface {<!-- -->
	Set(key, value interface{<!-- -->}) error
	Get(key interface{<!-- -->}) interface{<!-- -->}
	Delete(key interface{<!-- -->}) error
	SeeeionID() string
}

```
1. 创建Sessiong管理器
```
type Provider interface {<!-- -->
	SessionInit(sessionId string) (Session, error)
	SessionRead(sessionId string) (Session, error)
	SessionDestroy(sessionId string) error
	GarbageCollector(maxLifeTime int64)
}

```

```
var providers = make(map[string]Provider)
func RegisterProvider(name string, provider Provider) {<!-- -->
	if provider == nil {<!-- -->
		panic("session: Register provider is nil")
	}
	if _, p := providers[name]; p {<!-- -->
		panic("session: Register provider is existed")
	}
	provider[name] = provider
}

```

```
type SessionManager struct {<!-- -->
	cookieName string
	lock sync.Mutex
	provider Provider
	maxLifeTime int64
}

func NewSessionMaanger(providerName, cookieName string, maxLifetime int64) (*SessionManager, error) {<!-- -->
	provider, ok := providers[providerName]
	if !ok {<!-- -->
		return nil, fmt.Errorf("session: unknown provide %q (forgotten import?)", providerName)
	}

	return &amp;SessionManager{<!-- -->
		cookieName: cookieName,
		maxLifeTime: maxLifetime,
		provider: provider,
	}, nil
}

```

```
var globalSession *SessionManager
func init() {<!-- -->
	glovalSession, _ = NewSessionManager("memory", "sessionId", 3600)
}

```
1. 创建全局唯一sessionId
```
func (manager *SessionManager) GetSessionId() string {<!-- -->
	b := make([]byte, 32)
	if _, err := io.ReadFull(rand.Reader, b); err != nil {<!-- -->
		return ""
	}
	return base64.URLEncoding.EncodeToString(b)
}

```
1. 来访用户获取或分配session
```
func (manager *SessionManager) SessionBegin(w http.ResponseWriter, r *http.Request) (session Session) {<!-- -->
	manager.lock.Lock()
	defer manager.lock.Unlock()

	cookie, err := r.Cookie(manager.cookieName)
	if err != nil || cookie.Value == "" {<!-- -->
		sessionId := manager.GetSessionId()
		session, _ = manager.provider.SessionInit(sessionId)
		cookie := http.Cookie {<!-- -->
			Name: manager.cookieName,
			Value: url.QueryEscape(sessionId),
			Path: "/"
			HttpOnly: true,
			MaxAge: int(manager.maxLifeTime),
		}
		http.SetCookie(w, &amp;cookie)
	} else {<!-- -->
		sessionId, _ := url.QueryUnescape(cookie.Value)
		session, _ = manager.provider.SessionRead(sessionId)
	}
	return session
}

```

```
func login(w http.ResponseWriter, r *http.Request) {<!-- -->
	session := globalSession.SessionBegin(w, r)
	r.ParseForm()
	name := session.Get("username")
	if name != nil {<!-- -->
		session.Set("username", r.Form["username"])
	}
}

```
1. 注销session
```
func (manager *SessionManagre) SessionDestroy(w http.ResponseWriter, r *http.Request) {<!-- -->
	cookie, err := r.Cookie(manager.cookieName)
	if err != nil || cookie.Value == "" {<!-- -->
		return
	}
	
	manager.lock.Lock()
	defer manager.lock.Unlock()

	manager.provider.SessionDestroy(cookie.Value)
	exporedTime := time.Now()
	newCookie := http.Cookie{<!-- -->
		Name: manager.cookieName,
		Path: "/",
		HttpOnly: true,
		Expires: expiredTime,
		MaxAge: -1,
	}
	http.SetCookie(w, &amp;newCookie)
}

```
1. 删除session
```
func init() {<!-- -->
	go globalSession.GarbageCollector()
}

func (manager *SessionManager) GarbageCollector() {<!-- -->
	manager.lock.Lock()
	defer manager.lock.Unlock()
	manager.provider.GargageCollector(manager.maxLifeTime)
	
	time.AfterFunc(time.Duration(manager.maxLifeTime), func() {<!-- -->
		manager.GarbageCollector()
	})
}

```
