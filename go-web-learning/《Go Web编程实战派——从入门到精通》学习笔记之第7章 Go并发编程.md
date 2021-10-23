#《Go Web编程实战派——从入门到精通》学习笔记之第7章 Go并发编程


### 《Go Web编程实战派——从入门到精通》学习笔记之第7章 Go并发编程
- - <ul><li>- - - - <ul><li>- - - - - - - - - - - - - 


# 第7章 Go并发编程

## 7.1 并发与并行
1. 并发
Concurrent，多进程指令被CPU快速轮换执行。宏观上，多个进程同时执行。微观上，多个进程非同时执行，时间分成若干段，多个进程快速交替执行。

操作系统进程的并发：CPU划分时间片段（时间区间），进程在时间区间之间来回切换处理。CPU处理速度快，时间间隔处理得当，用户感觉是多个进程同时进行。
1. 并行
Parallel，同一时刻多条指令在多个处理器上同时执行。
1. 区别- 并发偏重于多个任务交替执行，而多个任务间可能是串行的。并发是逻辑上的同时发生（simultaneous）。串行通讯，传输1bit信号。- 并行偏重于同时执行。是物理上的同时发生。并行通讯，传输多bit信号。
## 7.2 进程、线程和协程
1. 进程（Process）
运行中的可执行程序。
1. 线程（Thread）
轻量级进程（Lightweight Process，LWP），程序执行流的最小单位。由线程ID、当前指令指针（PC）、寄存器集合和堆栈组成。

线程是进程的一个实体，与同属同一进程的其他线程共享进程的全部资源。

线程拥有独立栈，共享堆。

线程具有5中状态：初始化、可运行、运行中、阻塞、销毁。

线程和进程都由操作系统管理。线程是最小的执行单元，进程是最小的资源管理单元。
1. 协程（Coroutines）
协程是比线程更加轻量级的一种特殊函数。协程不是操作系统内核管理，而是程序控制，即在用户态执行。提升性能，不会像线程切换那样消耗资源。

一个进程可以包含多个线程，一个线程可以包含多个协程。一个线程中的多个协程始终是串行的（函数都是串行运行），无论CPU多少核。
1. 对比- 协程仅是特殊函数，与线程和进程不是一个纬度。- 一个进程可以包含多个线程，一个线程可以包含多个协程。- 一个线程内的多个协程可以切换，但协程始终是串行执行，无法利用CPU的多核能力。- 进程由操作系统自己的切换策略来切换，用户无感。切换内容包括页全局目录、内核栈和硬件上下文，切换内容被保存在内存中。采用“从用户态到内核态再到用户态”的方式，切换效率低。- 线程由操作系统自己的切换策略来切换，用户无感。切换内容包括内核栈和硬件上下文，切换内容被保存在内核栈中。采用“从用户态到内核态再到用户态”的方式，切换效率中等。- 协程的切换由用户（编程者或应用程序）决定。切换内容包括硬件上下文，切换内容被保存在用户自己的变量（用户栈或堆）中。只有“用户态”，切换效率高。
## 7.3 Go并发模型简介
1. 多线程共享内存模型
访问共享数据（数组、map、结构体或对象等）时，通过锁来访问。衍生出线程安全的数据结构，Go通过sync包来实现。
1. CSP（Communicating Sequential Processes）并发模型
CSP并发模型理念是不通过共享内存来通信，而是通过通信来共享内存。

Go通过goroutine和通道（channel）来实现。goroutine时并发执行单位，类似协程。通道是goroutine间的通信管道，类似UNIX中的管道。

```
package main

import "fmt"

func main() {<!-- -->
	message := make(chan string)

	go func() {<!-- -->messages &lt;- "ping"}()
	
	msg := &lt;-message
	fmt.Println(msg)
}

```

## 7.4 goroutine和channel实现并发

### 7.4.1 goroutine简介

```
go func(param1, param2) {<!-- -->
	//...
}(var1, var2)

```

```
package main

import (
	"fmt"
	"time"
)

func Echo(s string) {<!-- -->
	for i := 0; i &lt; 3; i++ {<!-- -->
		time.Sleep(100*time.Millisecond)
		fmt.Println(s)
	}
}

func main() {<!-- -->
	go Echo("go")
	Echo("web program")
}

```

goroutine的调度方式是协同式的，没有时间片概念。 goroutine切换时刻：
- 通道接收或发送数据且造成阻塞时。- 新的goroutine被创建。- 造成系统调用被阻塞，如操作文件时。
goroutine在多核CPU环境下可以是并行的。

```
package main

import (
	"fmt"
	"time"
)

func main() {<!-- -->
	for i := 0; i &lt; 5; i++ {<!-- -->
		go Add(i, i)	//返回值被忽略
	}
	time.Sleep(1000*time.Millisecond)
}

func Add(a, b int) int {<!-- -->
	c := a + b
	fmt.Println(c)
	time.Sleep(100*time.Millisecond)
	return c
}

```

### 7.4.2 通道
1. 定义
channel是用来传递数据的数据结构，可以通过通道共享内置类型、命名类型、结构类型和引用类型的值或者指针。一种特殊类型，任何时候，同时只能有一个goroutine访问通道进行发送或接收数据。

类似队列，遵循先入先出（First In First out）规则，保证收发数据的顺序。
1. 声明
```
var channel_name chan type

```
1. 创建
```
在这里插入代码片

```
<li>
## 7.5 sync实现并发

### 7.5.1 竞态

使用并发，可能产生数据争用的竞态问题。

```
func main() {<!-- -->
	fmt.Println(getNumber())
}

func getNumber() int {<!-- -->
	var i int
	go func() {<!-- -->
		i = 6
	}()

	return i
}

```

### 7.5.2 互斥锁

sync.Mutex，用于实现互斥锁，用于读写不确定的场景，全局锁。

```
type Mutex struct {<!-- -->
	state int32	//当前互斥锁的状态
	sema uint32	//控制锁状态的信号量
}

func (m *Mutex) Lock()
func (m *Mutex) Unlock()

```

必须先Lock()，然后Unlock()。 连续Lock()，死锁。 先Unlock()，后Lock()，panic。 可以一个goroutine先Lock()，其他goroutine后Unlock()。

```
package main

import (
	"fmt"
	"sync"
	"time"
)

func main() {<!-- -->
	var mutex sync.Mutex
	wait := sync.WaitGroup{<!-- -->}
	fmt.Println("Locked")
	
	mutex.Lock()

	for i := 1; i &lt;= 5; i++ {<!-- -->
		wait.Add(1)
		go func(i int) {<!-- -->
			defer wait.Done()
			fmt.Println("Not lock:", i)
			mutex.Lock()
			fmt.Println("Locked:", i)
			time.Sleep(time.Second)
			fmt.Println("Unlocked:", i)
			mutex.Unlock()
		}(i)
	}

	time.Sleep(time.Second)
	fmt.Println("Unlocked")

	mutex.Unlock()

	wait.Wait()
}

```

### 7.5.3 读写互斥锁

sync.RWMutex可以多个读锁或者一个写锁，用于读次数远远多于写次数的场景。

```
type RWMutex struct {<!-- -->
	w Mutex
	writerSem uint32
	readerSem uint32
	readerCount int32
	readerWait int32
}

//写操作
func (*RWMutex) Lock()
func (*RWMutex) Unlock()

//读操作
func (*RWMutex) RLock()
func (*RWMutex) RUnlock()

```

```
package main

import (
	"fmt"
	"sync"
	"math/rand"
)

var count int
var rw sync.RWMutex

func main() {<!-- -->
	ch := make(char struct{<!-- -->}, 6)
	
	for i := 0; i &lt; 3; i++ {<!-- -->
		go ReadCount(i, ch)
	}
	
	for i := 0; i &lt; 3; i++ {<!-- -->
		go WriteCount(i, ch)
	}

	for i := 0; i &lt; 6; i++ {<!-- -->
		&lt;-ch
	}
}

func ReadCount(n int, ch chan struct{<!-- -->}) {<!-- -->
	rw.RLock()
	fmt.Printf("goroutine %d 进入读操作...\n", n)
	v := count
	fmt.Printf("goroutine %d 读取结束，值为：%d\n", n, v)
	rw.RUnlock()
	ch &lt;- struct{<!-- -->}{<!-- -->}
}

func WriteCount(n int, ch chan struct{<!-- -->}) {<!-- -->
	rw.Lock()
	fmt.Printf("goroutine %d 进入写操作...\n", n)
	v := rand.Intn(10)
	count = v
	fmt.Printf("goroutine %d 写入结束，值为：%d\n", n, v)
	rw.Unlock()
	ch &lt;- struct{<!-- -->}{<!-- -->}
}

```

## 7.6 Go并发的Web应用

### 7.6.1 自增整数生成器

```
package main

import "fmt"

func IntegerGenerator() chan int {<!-- -->
	var ch chan int = make(chan int)
	go func() {<!-- -->
		for i := 0; ; i++ {<!-- -->
			ch &lt;- i
		}
	}()
	return ch
}

func main() {<!-- -->
	generator := IntegerGenerator()
	for i := 0; i &lt; 100; i++ {<!-- -->
		fmt.Println(&lt;-generator)
	}
}

```

### 7.6.2 并发的消息发送器

```
package main

import "fmt"

func SendNotification(user string) chan string {<!-- -->
	notifications := make(char string, 500)

	go func() {<!-- -->
		notifications &lt;- fmt.Sprintf("Hi %s, welcome!", user)
	}()

	return notifications
}

func main() {<!-- -->
	barry := SendNotification("barry")
	shirdon := SendNotification("shirdon")

	fmt.Println(&lt;-barry)
	fmt.Println(&lt;-shirdon )
}

```

### 7.6.3 多路复合计算器

```
package main

import (
	"fmt"
	"math/rand"
	"time"
)

func doCompute(x int) int {<!-- -->
	time.Sleep(time.Duration(rand.Intn(10)) * time.Millisecond)
	return 1+x
}

func branch(x int) chan int {<!-- -->
	ch := make(chan int)
	go func() {<!-- -->
		ch &lt;- doCompute(x)
	}()
	return ch
}

func Recombination(chs... chan int) chan int {<!-- -->
	ch := make(chan int)
	for _, c := range chs {<!-- -->
		go func(c chan int) {<!-- -->ch &lt;- &lt;-c}(c)
	}
	return ch
}
/*
func Recombination(chs... chan int) chan int {
	ch := make(chan int)
	go func() {
		for i := 0; i &lt; len(chs); i++ {
			select {
			case v1 := &lt;-chs[i]:
				ch &lt;- v1
			}
		}
	}()
	return ch
}
*/

func main() {<!-- -->
	result := Recombination(branch(10), branch(20), branch(30))

	for i := 0; i &lt; 3; i++ {<!-- -->
		fmt.Println(&lt;-result)
	}
}

```

### 7.6.4 select创建多通道监听器

```
package main

import (
	"fmt"
)

func foo(x int) int {<!-- -->
	ch := make(chan int)
	go func() {<!-- -->
		ch &lt;- x
	}()
	return ch
}

func main() {<!-- -->
	ch1, ch2, ch3 := foo(3), foo(6), foo(9)
	ch := make(chan int)

	go func() {<!-- -->
		for {<!-- -->
			select {<!-- -->
			case v1 := &lt;-ch1:
				ch &lt;- v1
			case v2 := &lt;-ch2:
				ch &lt;- v2
			case v3 := &lt;-ch3:
				ch &lt;- v1
			}
		}
	}()
	/*
	go func() {
		timeout := time.After(1*time.Second)
		for isTimeout := false; !isTimeout; {
			select {
			case v1 := &lt;-ch1:
				ch &lt;- v1
			case v2 := &lt;-ch2:
				ch &lt;- v2
			case v3 := &lt;-ch3:
				ch &lt;- v1
			case &lt;-timeout:
				isTimeout = true
			}
		}
	}()
	*/
	for i := 0; i &lt; 3; i++ {<!-- -->
		fmt.Println(&lt;-ch)
	}
}

```

### 7.6.5 无缓冲通道阻塞主线

```
package main

import (
	"fmt"
)

func main() {<!-- -->
	ch, quit := make(chan int), make(chan int)

	go func() {<!-- -->
		ch &lt;- 8
		quit &lt;- 1
	}()
	
	for isQuit:= false; !isQuit; {<!-- -->
		select {<!-- -->
		case v := &lt;-ch:
			fmt.Printf("received %d from ch", v)
		case &lt;-quit:
			isQuit= true
		}
	}
}

```

### 7.6.6 筛选法求素数

```
package main

import (
	"fmt"
)

func IntegerGenerator() chan int {<!-- -->
	var ch chan int = make(chan int)

	go func() {<!-- -->
		for i := 2; ; i++ {<!-- -->
			ch &lt;- i
		}
	}()

	return ch
}

func Filter(in chan int, number int) chan int {<!-- -->
	out := make(chan int)

	go func() {<!-- -->
		for {<!-- -->
			i := &lt;-in
			if i%number != 0 {<!-- -->
				out &lt;- i
			}
		}
	}()
	
	return out
}

func main() {<!-- -->
	const max = 100
	numbers := IntegerGenerator()
	number := &lt;-numbers

	for number &lt;= max {<!-- -->
		fmt.Println(number)
		numbers = Filter(numbers, number)
		number = &lt;-numbers
	}
}

```

### 7.6.7 随机数生成器

```
package main

import (
	"fmt"
)

func randGenerator() chan int {<!-- -->
	var ch chan int = make(chan int)

	go func() {<!-- -->
		for {<!-- -->
			select {<!-- -->
			case ch &lt;- 0:
			case ch &lt;- 1:
			}
		}
	}()

	return ch
}

func main() {<!-- -->
	generator := IntegerGenerator()
	
	for i := 0; i &lt; 10; i++ {<!-- -->
		fmt.Println(&lt;-generator)
	}
}

```

### 7.6.8 定时器

```
package main

import (
	"fmt"
	"time"
)

func Timer(duration time.Duration) chan bool {<!-- -->
	var ch chan bool = make(chan bool)

	go func() {<!-- -->
		time.Sleep(duration)
		ch &lt;- true
	}()

	return ch
}

func main() {<!-- -->
	timeout := Timer(5*time.Second)
	
	for {<!-- -->
		select {<!-- -->
		case &lt;-timeout:
			fmt.Println("already 5s!")
			return
		}
	}
}

```

### 7.6.9 并发的Web爬虫

```
package main

import (
	"fmt"
	"time"
)

func Get(url string) (result string, err error) {<!-- -->
	resp, err := http.Get(url)
	if err != nil {<!-- -->
		return
	}
	defer resp.Body.Close()

	buf := make([]byte, 4*1024)
	for {<!-- -->
		n, err := resp.Body.Read(buf)
		if err != nil {<!-- -->
			if err == io.EOF {<!-- -->
				err = nil
				fmt.Println("文件读取完毕")
				break
			} else {<!-- -->
				fmt.Println("resp.Body.Read err = ", err)
				break
			}
		}
		result += string(buf[:n])
	}
	
	return
}

func SpiderPage(i int, page chan&lt;- int) {<!-- -->
	url := "https://github.com/search?q=go&amp;type=Repositories&amp;p=1" + strconv.Itoa((i-1)*50)
	fmt.Println("正在爬取第%d个网页\n", i)
	result, err := Get(url)
	if err != nil {<!-- -->
		fmt.Println("http.Get err = ", err)
		return
	}
	
	filename := "page" + strconv.Itoa(i) + ".html"
	f, err := os.Create(filename)
	if err != nil {<!-- -->
		fmt.Println("os.Create err = ", err)
		return
	}
	f.WriteString(result)
	f.Close()

	page &lt;- i
} 

func Run(start, end int) {<!-- -->
	fmt.Printf("正在爬到第%d页到第%d页\n", start, end)
	page := make(chan int)
	for i := start; i &lt;= end; i++ {<!-- -->
		go SpiderPage(i, page)
	}
	for i := start; i &lt;= end; i++ {<!-- -->
		fmt.Printf("第%d个页面爬取完成\n", &lt;-page)
	}
}

func main() {<!-- -->
	var start, end int
	fmt.Printf("请输入起始页数字&gt;=1：&gt; ")
	fmt.Scan(&amp;start)
	fmt.Printf("请输入结束页数字：&gt; ")
	fmt.Scan(&amp;end)
	
	Run(start, end)
}

```
