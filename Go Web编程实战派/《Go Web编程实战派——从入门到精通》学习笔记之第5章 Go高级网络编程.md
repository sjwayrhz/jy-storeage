#《Go Web编程实战派——从入门到精通》学习笔记之第5章 Go高级网络编程


### 《Go Web编程实战派——从入门到精通》学习笔记之第5章 Go高级网络编程
- - <ul><li>- <ul><li>- - - - - - - 


# 第5章 Go高级网络编程

## 5.1 Go Socket编程

### 5.1.1 什么是Socket

Socket是计算机网络中的内部端点，用于在节点内发送和接收数据。是一种系统资源，含通信协议、目标地址、状态等。位于应用层和传输层间，连接应用层与传输层。本质上是对传输层TCP/IP的封装，提供Socket API给应用程序调用。

|应用层
|------
|Socket API
|传输层
|网络层
|数据链路层
|物理层
1. Socket是如何工作的
服务器端和客户端通过Socket通信，服务器端Socket监听地址，等待客户端来连接。客户端Socket连接客户端Socket。

建立TCP/IP连接的过程，产生三次网络通信，三次握手。

关闭TCP/IP连接的过程，产生四次网络通信，四次握手。
1. C语言创建服务器端Socket1.  C语言创建客户端Socket 1.  Go语言创建Socket 
服务器端通过net.Listen()方法建立连接并监听指定IP地址和端口号，等待客户端连接。客户端通过net.Dial()函数连接指定IP地址和端口号。

### 5.1.2 客户端Dial()函数的使用

```
func Dial(net, addr string) (Conn, error)
//网络协议
//IP地址或域名，端口号位于":"后

```

```
conn, err := net.Dial("tcp", "192.168.0.1:8087")
conn, err := net.Dial("udp", "192.168.0.2:8088")
conn, err := net.Dial("ip4:icmp", "www.shirdon.com")
conn, err := net.Dial("ip4:1", "10.0.0.8")

```

```
package main

import (
	"bytes"
	"fmt"
	"io"
	"net"
	"os"
)

func main() {<!-- -->
	if len(os.Args) != 2 {<!-- -->
		fmt.Fprintf(os.Stderr, "Usage: %s host:port", os.Args[0])
		os.Exit(1)
	}

	service := os.Args[1]
	conn, err := net.Dial("tcp", service)
	validateError(err)

	_, err = conn.Write([]byte("HEAD / HTTP/1.0\r\n\r\n"))
	validateError(err)

	result, err := fullyRead(conn)
	validateError(err)

	fmt.Println(string(result))
	os.Exit(0)
}

func validateError(err error) {<!-- -->
	if err != nil {<!-- -->
		fmt.Fprintf(os.Stderr, "Fatal error: %s", errr.Error())
		os.Exit(1)
		
	}
}

func fullyRead(conn net.Conn) ([]byte, error) {<!-- -->
	defer conn.Close()

	result := bytes.NewBuffer(nil)
	var buf [512]byte
	for {<!-- -->
		n, err := conn.Read(buf[:])
		result.Write(buf[:n])
		if err != nil {<!-- -->
			if err == io.EOF {<!-- -->
				break
			}
			return nil, err
		}
	}
	return result.Bytes(), nil
}

```

### 5.1.3 客户端DialTCP()函数的使用

```
func DialTCP(network string, laddr, raddr *TCPAddr) (*TCPConn, error)
//network是tcp、tcp4或tcp6
//laddr本地地址，通常为nil
//raddr目标地址

```

```
package main

import (
	"fmt"
	"io/ioutil"
	"net"
	"os"
)

func main() {<!-- -->
	service := "127.0.0.1:8086"
	tcpAddr, err := net.ResolveTCPAddr("tcp", service)
	checkError(err)
	fmt.Println("tcpAddr :")
	typeof(tcpAddr)

	myConn, err1 := net.DialTCP("tcp", nil, tcpAddr)
	checkError(err)
	fmt.Println("myConn :")
	typeof(myConn)

	_, err = myConn.Write([]byte("HEAD / HTTP/1.1\r\n\r\n"))
	checkError(err)

	result, err := ioutil.ReadAll(myConn)
	checkError(err)
	fmt.Println(string(result))
	os.Exit(0)
}

func typeof(v interface{<!-- -->}) {<!-- -->
	fmt.Printf("type is: %T\n", v)
}

func checkError(err error) {<!-- -->
	if err != nil {<!-- -->
		fmt.Println("Error:", errr.Error())
		os.Exit(1)
	}
}

```

```
package main

import (
	"fmt"
	_ "io"
	"log"
	"net"
)

func Server() {<!-- -->
	l, err := net.Listen("tcp", "127.0.0.1:8088")
	if err != nil {<!-- -->
		log.Fatal(err)
	}
	defer l.Close()

	for {<!-- -->
		conn, err := l.Accept()
		if err != nil {<!-- -->
			log.Fatal(err)
		}
		fmt.Printf("访问客户端信息：con=%v 客户端ip=%v\n", conn, conn.RemoteAddr().String())
		
		go handleConnection(conn)
	}
}

func handleConnection(c net.Conn) {<!-- -->
	defer c.Close()

	for {<!-- -->
		fmt.Printf()
		buf := make([]byte, 1024)
		n, err := c.Read(buf)
		if err != nil {<!-- -->
			log.Fatal(err)
			break
		}

		fmt.Print(string(buf[:n]))
	}
}

func main() {<!-- -->
	Server()
}

```

```
package main

import (
	"buffio"
	"fmt"
	"log"
	"net"
	"os"
	"strings"
)

func Client() {<!-- -->
	conn, err := net.Dial("tcp", "127.0.0.1:8088")
	if err != nil {<!-- -->
		log.Fatal(err)
	}
	defer conn.Close()

	reader := bufio.NewReader(os.Stdin)
	for {<!-- -->
		line, err := reader.ReadString('\n')
		if err != nil {<!-- -->
			log.Fatal(err)
		}
		line = strings.Trim(line, "\r\n")

		if line == "exit" {<!-- -->
			fmt.Println("用户退出客户端")
			break
		}
		
		n, err := conn.Write([]byte(line + "\n"))
		if err != nil {<!-- -->
			log.Fatal(err)
		}
		fmt.Printf("客户端发送了%d字节的数据到服务器端\n", n)
	}
}

func main() {<!-- -->
	Client()
}

```

### 5.1.4 UDP Socket的使用

UDP是无连接的，服务器端只需要指定IP地址和端口号，然后监听该地址，等待客户端并与之建立连接，两端即可通信。

```
func ResolveUDPAddr(network, address string) (*UDPAddr, error)
func ListenUDP(network string, laddr UDPAddr) (UDPConn, error)
func (c *UDPConn) ReadFromUDP(b []byte) (int, *UDPAddr, error)
func (c *UDPConn) WriteToUDP(b []byte, addr *UDPAddr) (int, error)

```

```
package main

import (
	"fmt"
	"net"
)

func main() {<!-- -->
	serverUdpAddr, err := net.ResolveUDPAddr("udp", "127.0.0.1:8023")
	if err != nil {<!-- -->
		fmt.Println("ResolveUDPAddr err:", err)
		return
	}
	
	conn, err := net.ListenUDP("udp", serverUdpAddr)
	if err != nil {<!-- -->
		fmt.Println("ListenUDP err:", err)
		return
	}
	defer conn.Close()
	
	buf := make([]byte, 1024)
	for {<!-- -->
		n, clientUdpAddr, err := conn.ReadFromUDP(buf)
		if err != nil {<!-- -->
			fmt.Println("ReadFromUDP err:", err)
			return
		}
		fmt.Printf("接收到客户端[%s]:%s", clientUdpAddr, string(buf[:n]))

		conn.WriteToUDP([]byte("I am server"), clientUdpAddr)
	}
}

```

```
package main

import (
	"fmt"
	"net"
	"os"
)

func main() {<!-- -->
	conn, err := net.Dial("udp", "127.0.0.1:8023")
	if err != nil {<!-- -->
		fmt.Println("Dial err:", err)
		return
	}
	defer conn.Close()

	go func() {<!-- -->
		str := make([]byte, 1024)
		for {<!-- -->
			n, err := os.Stdin.Read(str)
			if err != nil {<!-- -->
				fmt.Println("os.Stdin.Read err:", err)
				return
			}
			conn.Write(str[:n])
		}
	}()
	
	buf := make([]byte, 1024)
	for {<!-- -->
		n, err := conn.Read(buf)
		if err != nil {<!-- -->
			fmt.Println("Read err:", err)
			return
		}
		fmt.Println("服务器发送来：", string(buf[:n]))
	}
}

```

### 5.1.5 Go Socket创建简易聊天程序

```
package main

import (
	"fmt"
	"net"
	"time"
)

var ConnSlice map[net.Conn]*Heartbeat

type Heartbeat struct {<!-- -->
	endTime int64 //过期时间
}

func main() {<!-- -->
	ConnSlice = map[net.Conn]*Heartbeat{<!-- -->}
	l, err := net.Listen("tcp", "127.0.0.1:8086")
	if err != nil {<!-- -->
		fmt.Println("服务器启动失败")
	}
	defer l.Close()
	for {<!-- -->
		conn, err := l.Accept()
		if err != nil {<!-- -->
			fmt.Println("Error accepting: ", err)
		}
		fmt.Printf("Received message %s -&gt; %s \n", conn.RemoteAddr(), conn.LocalAddr())
		ConnSlice[conn] = &amp;Heartbeat{<!-- -->
			endTime: time.Now().Add(time.Second * 5).Unix(), //初始化过期时间
		}
		go handelConn(conn)
	}
}
func handelConn(c net.Conn) {<!-- -->
	buffer := make([]byte, 1024)
	for {<!-- -->
		n, err := c.Read(buffer)
		if ConnSlice[c].endTime &gt; time.Now().Unix() {<!-- -->
			ConnSlice[c].endTime = time.Now().Add(time.Second * 5).Unix() //更新心跳时间
		} else {<!-- -->
			fmt.Println("长时间未发消息断开连接")
			return
		}
		if err != nil {<!-- -->
			return
		}
		//如果是心跳检测，那就不要执行剩下的代码
		if string(buffer[0:n]) == "1" {<!-- -->
			c.Write([]byte("1"))
			continue
		}
		for conn, heart := range ConnSlice {<!-- -->
			if conn == c {<!-- -->
				continue
			}
			//心跳检测 使用懒惰更新,需要发送数据的时候才检查规定时间内有没有数据到达
			if heart.endTime &lt; time.Now().Unix() {<!-- -->
				delete(ConnSlice, conn) //从房间列表中删除连接，并且关闭
				conn.Close()
				fmt.Println("删除连接", conn.RemoteAddr())
				fmt.Println("现在存有链接", ConnSlice)
				continue
			}
			conn.Write(buffer[0:n])
		}
	}
}

```

```
package main

import (
	"bufio"
	"fmt"
	"net"
	"os"
	"time"
)

func main() {<!-- -->
	server := "127.0.0.1:8086"
	tcpAddr, err := net.ResolveTCPAddr("tcp4", server)
	if err != nil {<!-- -->
		Log(os.Stderr, "Fatal error:", err.Error())
		os.Exit(1)
	}
	conn, err := net.DialTCP("tcp", nil, tcpAddr)
	if err != nil {<!-- -->
		Log("Fatal error:", err.Error())
		os.Exit(1)
	}
	Log(conn.RemoteAddr().String(), "connect success!")
	Sender(conn)
	Log("end")
}
func Sender(conn *net.TCPConn) {<!-- -->
	defer conn.Close()
	sc := bufio.NewReader(os.Stdin)
	go func() {<!-- -->
		t := time.NewTicker(time.Second) //创建定时器,用来实现定期发送心跳包给服务端
		defer t.Stop()
		for {<!-- -->
			&lt;-t.C
			_, err := conn.Write([]byte("1"))
			if err != nil {<!-- -->
				fmt.Println(err.Error())
				return
			}
		}
	}()
	name := ""
	fmt.Println("请输入聊天昵称") //用户聊天的昵称
	fmt.Fscan(sc, &amp;name)
	msg := ""
	buffer := make([]byte, 1024)
	_t := time.NewTimer(time.Second * 5) //创建定时器,每次服务端发送消息就刷新时间
	defer _t.Stop()

	go func() {<!-- -->
		&lt;-_t.C
		fmt.Println("服务器出现故障，断开链接")
		return
	}()
	for {<!-- -->
		go func() {<!-- -->
			for {<!-- -->
				n, err := conn.Read(buffer)
				if err != nil {<!-- -->
					return
				}
				_t.Reset(time.Second * 5) //收到消息就刷新_t定时器，如果time.Second*5时间到了，那么就会&lt;-_t.C就不会阻塞，代码会往下走，return结束
				if string(buffer[0:1]) != "1" {<!-- --> //心跳包消息定义为字符串"1",不需要打印出来
					fmt.Println(string(buffer[0:n]))
				}
			}
		}()
		fmt.Fscan(sc, &amp;msg)
		i := time.Now().Format("2006-01-02 15:04:05")
		conn.Write([]byte(fmt.Sprintf("%s\n\t%s: %s", i, name, msg))) //发送消息
	}
}
func Log(v ...interface{<!-- -->}) {<!-- -->
	fmt.Println(v...)
	return
}

```

## 5.2 Go RPC编程

### 5.2.1 什么是RPC

### 5.2.2 Go RPC应用
1. gob编码RPC
```
package main

import (
	"fmt"
	"net/http"
	"net/rpc"
)

type Algorithm int

//参数结构体
type Args struct {<!-- -->
	X, Y int
}

//参数结构体
//type Response int

//定义一个方法求两个数的和
//该方法的第一个参数为输入参数，第二个参数为返回值
func (t *Algorithm) Sum(args *Args, reply *int) error {<!-- -->
	*reply = args.X + args.Y
	fmt.Println("Exec Sum ", reply)
	return nil
}

func main() {<!-- -->
	//实例化
	algorithm := new(Algorithm)
	fmt.Println("Algorithm start", algorithm)
	//注册服务
	rpc.Register(algorithm)
	rpc.HandleHTTP()
	if err := http.ListenAndServe(":8808", nil); err != nil {<!-- -->
		fmt.Println("err=====", err.Error())
	}
}

```

```
package main

import (
	"fmt"
	"log"
	"net/rpc"
	"os"
	"strconv"
)

//参数结构体
type ArgsTwo struct {<!-- -->
	X, Y int
}

func main() {<!-- -->
	client, err := rpc.DialHTTP("tcp", "127.0.0.1:8808")
	if err != nil {<!-- -->
		log.Fatal("在这里地方发生错误了：DialHTTP", err)
	}
	//获取第一个输入值
	i1, _ := strconv.Atoi(os.Args[1])
	//获取第二个输入值
	i2, _ := strconv.Atoi(os.Args[2])
	args := ArgsTwo{<!-- -->i1, i2}
	var reply int
	//调用命名函数，等待它完成，并返回其错误状态。
	err = client.Call("Algorithm.Sum", args, &amp;reply)
	if err != nil {<!-- -->
		log.Fatal("Call Sum algorithm error:", err)
	}
	fmt.Printf("Algorithm 和为: %d+%d=%d\n", args.X, args.Y, reply)
}

```
1. json编码RPC
```
package main

import (
	"fmt"
	"net"
	"net/rpc"
	"net/rpc/jsonrpc"
)

//使用Go提供的net/rpc/jsonrpc标准包
func init() {<!-- -->
	fmt.Println("JSON编码RPC，不是gob编码，其他的和RPC概念一模一样，")
}

type ArgsLanguage struct {<!-- -->
	Java, Go string
}

type Programmer string

func (m *Programmer) GetSkill(al *ArgsLanguage, skill *string) error {<!-- -->
	*skill = "Skill1:" + al.Java + "，Skill2" + al.Go
	return nil
}

func main() {<!-- -->
	//实例化
	str := new(Programmer)
	//注册服务
	rpc.Register(str)

	tcpAddr, err := net.ResolveTCPAddr("tcp", ":8085")
	if err != nil {<!-- -->
		fmt.Println("ResolveTCPAddr err=", err)
	}

	listener, err := net.ListenTCP("tcp", tcpAddr)
	if err != nil {<!-- -->
		fmt.Println("tcp listen err=", err)
	}

	for {<!-- -->
		conn, err := listener.Accept()
		if err != nil {<!-- -->
			continue
		}
		jsonrpc.ServeConn(conn)
	}
}

```

```
package main

import (
	"fmt"
	"log"
	"net/rpc/jsonrpc"
)

// 参数结构体可以和服务端不一样
// 但是结构体里的字段必须一样
type Send struct {<!-- -->
	Java, Go string
}

func main() {<!-- -->
	fmt.Println("client start......")
	client, err := jsonrpc.Dial("tcp", "127.0.0.1:8085")
	if err != nil {<!-- -->
		log.Fatal("Dial err=", err)
	}
	send := Send{<!-- -->"Java", "Go"}
	var receive string
	err = client.Call("Programmer.GetSkill", send, &amp;receive)
	if err != nil {<!-- -->
		fmt.Println("Call err=", err)
	}
	fmt.Println("receive", receive)
}

```

## 5.3 微服务

### 5.3.2 gRPC框架搭建微服务

https://github.com/protocolbuffers/protobuf/releases

go get github.com/golang/protobuf go get github.com/golang/protobuf/protoc-gen-go
