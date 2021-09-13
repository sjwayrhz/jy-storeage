#《Go Web编程实战派——从入门到精通》学习笔记之第1章 Go基础入门


### 《Go Web编程实战派——从入门到精通》学习笔记之第1章 Go基础入门
- - <ul><li>- - - <ul><li>- - - - - - - - - - - - - - - - - - - - - - - - - - - 


# 第1章 Go基础入门

## 1.1 安装Go

下载地址：

## 1.2 第一个Go程序

```
//helloWorld.go
//go run helloWorld.go
//go build helloWorld.go
//helloWorld.exe
package main

import "fmt"

func main() {<!-- -->
	fmt.Println("Hello World～")
}

```
1. 包声明
包管理单位

```
package xxx

```
- 目录下同级文件属于同一个包- 包名与目录名可以不同- 有且仅有一个main包（入口包）1. 包导入
调用其他包的变量或方法

```
import "package_name"

```

```
import (
	"os"
	"fmt"
)

```

```
//别名
import (
	alias1 "os"
	alias2 "fmt"
)

```

```
import (
	_ "os" //只初始化包(调用包中init函数)，不使用包中变量或函数
	alias2 "fmt"
)

```
1. main函数
入口函数，只能声明在main包中，有且仅有一个。

```
func 函数名(参数列表) (返回值列表) {<!-- -->
	函数体
}

```

## 1.3 Go基础语法与使用

### 1.3.1 基础语法
1. Go语言标记
Go程序由关键字、标识符、常量、字符串、符号等多种标记组成。

```
fmt . Println ( "Hi" )

```
1. 行分隔符
一般一行一个语句，多个语句；隔开
1. 注释
//单行注释 /* 多行注释 多行注释 */
1. 标识符
标识符通常用来对变量、类型等命名。[a-zA-Z0-9_]组成，不能以数字开始，不能是Go语言关键字
1. 字符串连接
“hello” + " world"
1. 关键字
<th align="left">continue</th><th align="left">for</th><th align="left">import</th><th align="left">return</th><th align="left">var</th>
|------
<td align="left">const</td><td align="left">fallthrough</td><td align="left">if</td><td align="left">range</td><td align="left">type</td>
<td align="left">chan</td><td align="left">else</td><td align="left">goto</td><td align="left">package</td><td align="left">swith</td>
<td align="left">case</td><td align="left">defer</td><td align="left">go</td><td align="left">map</td><td align="left">struct</td>
<td align="left">break</td><td align="left">default</td><td align="left">func</td><td align="left">interface</td><td align="left">select</td>
- 常量相关预定义标识符：true、false、ioto、nil- 类型相关预定义标识符：int、int8、int16、int32、int64、uint、uint8、uint16、uint32、uint64、uintptr、float32、float64、complex128、complex64、bool、byte、rune、string、error- 函数相关预定义标识符：make、len、cap、new、append、copy、close、delete、complex、real、imag、panic、recover1. Go语言空格
var name string name = “y” + “x”

### 1.3.2 变量

变量（variable）是一段或多段用来存储数据的内存，有明确类型。

```
var name type
var c, d *int

```

默认零值或空值，int为0，float为0.0，bool为false，string为""，指针为nil。 建议驼峰命名法totalPrice或下划线命名法total_price。

```
var (
	age int
	name string
	balance float32
)

```

名字 := 表达式 简短模式（short variable declaration）限制：
- 只用于定义变量，同时显示初始化- 表达式自动推导数据类型- 用于函数内部，即不能声明全局变量
```
name, number := "mali", 115

var 变量名 [类型] = 变量值
var language string = "Go"
var language = "Go"
language := "Go"

var (
	变量名1 [变量类型1] = 变量值1
	变量名2 [变量类型2] = 变量值2
)

var 变量名1, 变量名2 = 变量值1, 变量值2

var (
	age int = 18
	name string = "yx"
	balance float32 = 999.9
)
var age, name, balance = 18, "yx", 999.9
age, name, balance := 18, "yx", 999.9

//变量交换值
d, c := "D", "C"
c, d = d, c

```

局部变量，函数体内声明的变量，参数和返回值变量都是局部变量。

```
package main

import "fmt"

func main() {<!-- -->
	var local1, local2, local3 int

	local1 = 8
	local2 = 10
	local3 = local1 + local2 
	
	fmt.Printf("local1=%d, local2=%d, local3=%d\n", local1, local2, local3)
}

```

全局变量，函数体外声明的变量，可以在整个包甚至外部包（被导出）中使用，也可在任何函数中使用。

```
package main

import "fmt"

var global int

func main() {<!-- -->
	var local1, local2 int

	local1 = 8
	local2 = 10
	global = local1 + local2 
	
	fmt.Printf("local1=%d, local2=%d, global=%d\n", local1, local2, global)
}

```

```
package main

import "fmt"

var global int = 8

func main() {<!-- -->
	var global int = 99
	fmt.Printf("global=%d\n", global)
}

```

### 1.3.3 常量

const声明，编译时创建（声明在函数内部也是），存储不会改变的数据，只能是布尔型、数字型（整数、浮点和复数）和字符串型。

```
const 常量名 [类型] = 常量表达式
const pi = 3.1415926

const (
	e = 2.7182818
	pi = 3.1415926
)

```

```
/*
常量间算术、逻辑、比较运算都是常量，常量类型转换，len()，cap()，real()，imag()，complex()和unsafe.Sizeof()等函数调用也是常量结果。
*/
const IPv4Len = 4
func paraseIPv4(s string) IP {<!-- -->
	var p [IPv4Len]byte
	// ...
}

```

```
//itoa用于生成一组以相似规则初始化的常量
type Direction int
const (
	North Direction = itoa
	East
	South
	West
)

```

6种未明确类型的常量类型：无类型的布尔型(true和false)、无类型的整数(0)、无类型的字符(\u0000)、无类型的浮点数(0.0)、无类型的复数(0i)、无类型的字符串("")。 延迟明确常量的具体类型，可以直接用于更多的表达式而不需要显示的类型转换

```
var a float32 = math.Pi
var b float64 = math.Pi
var c complex128 = math.Pi

```

### 1.3.4 运算符

运算符是用来在程序运行时执行数学运算或逻辑运算的符号。

```
var a, b, c = 3, 6, 9
d := a + b * c

```

优先级是指，同一表达式中多个运算符，先执行哪一个。

<th align="left">优先级</th><th align="left">分类</th><th align="left">运算符</th><th align="left">结合性</th>
|------
<td align="left">1</td><td align="left">逗号运算符</td><td align="left">,</td><td align="left">从左到右</td>
<td align="left">2</td><td align="left">赋值运算符</td><td align="left">=、+=、-=、*=、/=、%=、&gt;=、&lt;&lt;=、&amp;=、^=、|=</td><td align="left">从右到左</td>
<td align="left">3</td><td align="left">逻辑或</td><td align="left">||</td><td align="left">从左到右</td>
<td align="left">4</td><td align="left">逻辑与</td><td align="left">&amp;&amp;</td><td align="left">从左到右</td>
<td align="left">5</td><td align="left">按位或</td><td align="left">|</td><td align="left">从左到右</td>
<td align="left">6</td><td align="left">按位异或</td><td align="left">^</td><td align="left">从左到右</td>
<td align="left">7</td><td align="left">按位与</td><td align="left">&amp;</td><td align="left">从左到右</td>
<td align="left">8</td><td align="left">等不等</td><td align="left">==、!=</td><td align="left">从左到右</td>
<td align="left">9</td><td align="left">关系运算符</td><td align="left">&lt;、&lt;=、&gt;、&gt;=</td><td align="left">从左到右</td>
<td align="left">10</td><td align="left">位移运算符</td><td align="left">&lt;&lt;、&gt;&gt;</td><td align="left">从左到右</td>
<td align="left">11</td><td align="left">加减法</td><td align="left">+、-</td><td align="left">从左到右</td>
<td align="left">12</td><td align="left">乘除法取余</td><td align="left">*（乘号）、/、%</td><td align="left">从左到右</td>
<td align="left">13</td><td align="left">单目运算符</td><td align="left">!、*（指针）、&amp;（取址）、++、–、+（正号）、-（负号）</td><td align="left">从右到左</td>
<td align="left">14</td><td align="left">后缀运算符</td><td align="left">()、[]</td><td align="left">从左到右</td>

### 1.3.5 流程控制语句

```
if b &gt; 10 {<!-- -->
	return 1
} else if b == 10 {<!-- -->
	return 2
} else {<!-- -->
	return 3
}

```

```
//不支持while和do while
product := 1
for i := 1; i &lt; 5; i++ {<!-- -->
	product *= i
}

j := 0
for {<!-- -->
	j++
	if j &gt; 50 {<!-- -->
		break
	}
}

```

```
JumpLoop:
for i := 0; i &lt; 5; i++ {<!-- -->
	for j := 0; j &lt; 5 ; j++ {<!-- -->
		if i &gt; 2 {<!-- -->
			break JumpLoop
		}
		if j == 2 {<!-- -->
			continue
		}
	}
}

```

```
//for-range可以遍历数组、切片、字符串、map和channel
for key, val := range 复合变量值 {<!-- -->
	//val对应索引值的复制值，只读
}
for position, runeChar := range str {<!-- -->
	//
}

```

```
for key, value := range []int{<!-- -->0, 1, -1, -2} {<!-- -->
	fmt.Printf("key:%d value:%d\n", key, value)
}

```

```
var str = "hi 加油"
for key, value := range str {<!-- -->
	fmt.Printf("key:%d value:0x%x\n", key, value)
}

```

```
m := map[string]int {<!-- -->
	"go": 100,
	"web": 100,
}
//输出无序
for key, value := range m {<!-- -->
	fmt.Printf(key, value)
}

```

```
c := make(chan int)
go func() {<!-- -->
	c &lt;- 7
	c &lt;- 8
	c &lt;- 9
	close(c)
} ()

for v := range c {<!-- -->
	fmt.Println(v)
}

```

```
//_匿名变量，占位符，不参与空间分配，也不占用变量名字。
m := map[string]int {<!-- -->
	"go": 100,
	"web": 100,
}

for _, v := range m {<!-- -->
	fmt.Println(v)
}

for key, _:= range []int{<!-- -->0, 1, -1, -2} {<!-- -->
	fmt.Printf("key:%d\n", key)
}

```

```
//swith-case
//表达式不必为常量，甚至整数，不需通过break跳出。
//各case中类型一致

var a = "love"
switch a {<!-- -->
	default:
	fmt.Println("none")
	case "love":
	fmt.Println("love")
	case "programming":
	fmt.Println("programming")
}

```

```
var a = "love"
switch a {<!-- -->
	default:
	fmt.Println("none")
	case "love", "programming":
	fmt.Println("find")
}

```

```
var r int = 6
switch {<!-- -->
	case r &gt; 1 &amp;&amp; r &lt; 10:
	fmt.Println(r)
}

```

```
func main() {<!-- -->
	var isBreak bool
	for x := 0; x &lt; 20; x++ {<!-- -->
		for y := 0; y &lt; 20; y++ {<!-- -->
			if y == 2 {<!-- -->
				isBreak = true
				break
			}
		}
		if isBreak {<!-- -->
			break
		}
	}
	fmt.Println("over")
}

func main() {<!-- -->
	for x := 0; x &lt; 20; x++ {<!-- -->
		for y := 0; y &lt; 20; y++ {<!-- -->
			if y == 2 {<!-- -->
				goto breakTag
			}
		}
	}
	breakTag:
	fmt.Println("over")
}

```

```
//goto优势
func main() {<!-- -->
	err := getUserInfo()
	if err != nil {<!-- -->
		fmt.Println(err)
		exitProcess()
	}

	err = getEmail()
	if err != nil {<!-- -->
		fmt.Println(err)
		exitProcess()
	}
	
	fmt.Println("over")
}


func main() {<!-- -->
	err := getUserInfo()
	if err != nil {<!-- -->
		goto doExit
	}

	err = getEmail()
	if err != nil {<!-- -->
		goto doExit
	}
	
	fmt.Println("over")
	return

	doExit:
	fmt.Println(err)
	exitProcess()
}

```

## 1.4 Go数据类型

<th align="left">类型</th><th align="left">说明</th>
|------
<td align="left">布尔型</td><td align="left">true或false</td>
<td align="left">数字类型</td><td align="left">uint8、uint16、uint32、uint64、int8、int16、int32、int64 、float32（IEEE-754）、float64（IEEE-754）、complex64、complex128、byte（uint8）、rune（int32）、uint（32或64）、int（32或64）、uintptr（存放指针）</td>
<td align="left">字符串类型</td><td align="left">一串固定长度的字符连接起来的字符序列，utf-8编码</td>
<td align="left">复合类型</td><td align="left">数组、切片、map、结构体</td>

### 1.4.1 布尔型

只有两个相同类型的值才能比较：
- 值的类型是接口（interface），两者必须都实现了相同的接口- 一个是常量，另一个不是常量，类型必须和常量类型相同- 类型不同，必须转换为相同类型，才能比较
&amp;&amp;优先级高于||，有短路现象。

```
func bool2int(b bool) int {<!-- -->
	if b {<!-- -->
		return 1
	} else {<!-- -->
		return 0
	}
}

func int2bool(i int) bool {<!-- -->return i != 0}

```

### 1.4.2 数字类型

位运算采用补码。int、uint和uintptr，长度由操作系统类型决定。

### 1.4.3 字符串类型

由一串固定长度的字符连接起来的字符序列，utf-8编码。值类型，字节的定长数组。

```
//声明和初始化
str := "string"

```

字符串字面量用"或`创建
- "创建可解析的字符串，支持转义，不能引用多行- `创建原生的字符串字面量，不支持转义，可多行，不能包含反引号字符
```
str1 := "\"hello\"\nI love you"
str2 := `"hello"
I love you
`

```

```
//字符串连接
str := "I love" + " Go Web"
str += " programming"

```

```
package main

import (
	"fmt"
	"unicode/utf8"
)

func main() {<!-- -->
	str := "我喜欢Go Web"
	fmt.Println(len(str))
	fmt.Println(utf8.RuneCountInString(str))
	fmt.Println(str[9])
	fmt.Println(string(str[9]))
	fmt.Println(str[:3])
	fmt.Println(string(str[:3]))
	fmt.Println(str[3:])
	fmt.Println([]rune(str))
}

```

```
package main

import (
	"fmt"
)

func main() {<!-- -->
	str := "我喜欢Go Web"
	chars := []rune(str)
	for ind, char := range chars {<!-- -->
		fmt.Printf("%d: %s\n", ind, string(char))
	}
	for ind, char := range str {<!-- -->
		fmt.Printf("%d: %s\n", ind, string(char))
	}
	for ind, char := range str {<!-- -->
		fmt.Printf("%d: %U %c\n", ind, char, char)
	}
}

```

```
var buffer bytes.Buffer
for {<!-- -->
	if piece, ok := getNextString(); ok {<!-- -->
		buffer.WriteString(piece)
	} else {<!-- -->
		break
	}
}
fmt.Println(buffer.String())

```

不能通过str[i]方式修改字符串中的字符。 只能将字符串内容复制到可写变量（[]byte或[]rune），然后修改。转换类型过程中会自动复制数据。

```
str := "hi 世界"
by := []byte(str)
by[2] = ','
fmt.Printf("%s\n", str)
fmt.Printf("%s\n", by)
fmt.Printf("%s\n", string(by))

```

```
str := "hi 世界"
by := []rune(str)
by[3] = '中'
by[4] = '国'
fmt.Println(str)
fmt.Println(by)
fmt.Println(string(by))

```

### 1.4.4 指针类型

指针类型指存储内存地址的变量类型。

```
var b int = 66
var p * int = &amp;b

```

```
package main

import (
	"fmt"
)

func main() {<!-- -->
	var score int = 100
	var name string = "barry"
	fmt.Printf("%p %p\n", &amp;score, &amp;name)
}

```

```
package main

import (
	"fmt"
)

func main() {<!-- -->
	var address string = "hangzhou, China"
	ptr := &amp;address
	
	fmt.Printf("address type: %T\n", address)
	fmt.Printf("address value: %v\n", address)
	fmt.Printf("address address: %p\n", &amp;address)
	
	fmt.Printf("ptr type: %T\n", ptr)
	fmt.Printf("ptr value: %v\n", ptr)
	fmt.Printf("ptr address: %p\n", &amp;ptr)
	fmt.Printf("point value of ptr : %v\n", *ptr)
}

```

```
package main

import (
	"fmt"
)

func exchange1(c, d int) {<!-- -->
	t := c
	c = d
	d = t
}

func exchange2(c, d int) {<!-- -->
	c, d = d, c
}

func exchange3(c, d *int) {<!-- -->
	t := *c
	*c = *d
	*d = t
}

func exchange4(c, d *int) {<!-- -->
	d, c = c, d
}

func exchange5(c, d *int) {<!-- -->
	*d, *c = *c, *d
}

func main() {<!-- -->
	x, y := 6, 8
	x, y = y, x
	fmt.Println(x, y)

	x, y = 6, 8
	exchange1(x, y)
	fmt.Println(x, y)
	
	x, y = 6, 8
	exchange2(x, y)
	fmt.Println(x, y)
	
	x, y = 6, 8
	exchange3(&amp;x, &amp;y)
	fmt.Println(x, y)
	
	x, y = 6, 8
	exchange4(&amp;x, &amp;y)
	fmt.Println(x, y)
	
	x, y = 6, 8
	exchange5(&amp;x, &amp;y)
	fmt.Println(x, y)
}

```

### 1.4.5 复合类型
1. 数组类型
数组是具有相同唯一类型的一组已编号且长度固定的数据项的序列，可以是任意原始类型，整数、字符串、自定义类型等。

```
var array [10]int
var numbers = [5]float32{<!-- -->100.0, 8.0, 9.4, 6.8, 30.1}
var numbers = [...]float32{<!-- -->100.0, 8.0, 9.4, 6.8, 30.1}

```

```
package main

import (
	"fmt"
)


func main() {<!-- -->
	var arr [6]int
	var i, j int
	for i = 0; i &lt; 6; i++ {<!-- -->
		arr[i] = i + 66
	}
	for j = 0; j &lt; 6; j++ {<!-- -->
		fmt.Printf("arr[%d] = %d\n", j, arr[j])
	}
}

```
1. 结构体类型
结构体是由0或多个任意类型的数据构成的数据集合。

```
type 类型名 struct {<!-- -->
	字段1 类型1
	结构体成员2 类型2
}

```

```
type Pointer struct {<!-- -->
	A float32
	B float32
}

type Color struct {<!-- -->
	Red, Green, Blue byte
}

variable_name := struct_variable_type {<!-- -->value1, value2, ...}
variable_name := struct_variable_type {<!-- -->key2: value2, key1: value1, ...}

```

```
package main

import "fmt"

type Book struct {<!-- -->
	title string
	author string
	subject string
	press string
}

func main() {<!-- -->
	fmt.Println(Book{<!-- -->author: "yx", title: "学习 Go Web"})
	
	var bookGo Books
	bookGo.title = "学习 Go Web"
	bookGo.author = "yx"
	bookGo.subject = "Go"
	bookGo.press = "电力工业出版社"
	fmt.Printf("bookGo.title: %s\n", bookGo.title)
	fmt.Printf("bookGo.author: %s\n", bookGo.author)
	fmt.Printf("bookGo.subject: %s\n", bookGo.subject)
	fmt.Printf("bookGo.press: %s\n", bookGo.press)
	printBook(bookGo)
	printBook(&amp;bookGo)
}

func printBook(book Books) {<!-- -->
	fmt.Printf("book.title: %s\n", book.title)
	fmt.Printf("book.author: %s\n", book.author)
	fmt.Printf("book.subject: %s\n", book.subject)
	fmt.Printf("book.press: %s\n", book.press)
}

func printBook2(book *Books) {<!-- -->
	fmt.Printf("book.title: %s\n", book.title)
	fmt.Printf("book.author: %s\n", book.author)
	fmt.Printf("book.subject: %s\n", book.subject)
	fmt.Printf("book.press: %s\n", book.press)
}

```
1. 切片类型
slice是对数组或切片连续片段的引用。 切片内部结构包含内存地址pointer、大小len和容量cap。

```
slice[开始位置:结束位置]
//不含结束位置

```

```
var sliceBuilder [20]int
for i := 0; i &lt; 20; i++ {<!-- -->
	sliceBuilder[i] = i + 1
}
fmt.Println(sliceBuilder[5:15])
fmt.Println(sliceBuilder[15:])
fmt.Println(sliceBuilder[:2])

```

```
b := []int{<!-- -->6, 7, 8}
fmt.Println(b[:])
fmt.Println(b[0:0])

```

```
var sliceStr []string
var sliceNum []int
var emptySliceNum = []int{<!-- -->}
fmtp.Println(sliceStr, sliceNum, emptySliceNum)
fmtp.Println(len(sliceStr), len(sliceNum), (emptySliceNum))
fmtp.Println(sliceStr == nil, sliceNum == nil, emptySliceNum == nil)

```

```
slice1 := make([]int, 6)
slice2 := make([]int, 6, 10)
fmtp.Println(slice1, slice2)
fmtp.Println(len(slice1), len(slice2))
fmtp.Println(cap(slice1), cap(slice2))

```
1. map类型
关联数组，字典，元素对（pair）的无序集合，引用类型。

```
var name map[key_type]value_type

```

```
var literalMap map[string]string
var assignedMap map[string]string
literalMap = map[string]string{<!-- -->"first": "go", "second": "web"}
createdMap := make(map[string]float32)
assignedMap = literalMap	//引用
createdMap["k1"] = 99
createdMap["k2"] = 199
assignedMap["second"] = "program"

fmt.Println(literalMap["first"])
fmt.Println(literalMap["second"])
fmt.Println(literalMap["third"])
fmt.Println(createdMap["k2"])

```

```
createdMap := new(map[string]float32)
//错误
//声明了一个未初始化的变量并取了它的地址

```

```
//map到达容量上限，自动增1
make(map[key_type]value_type, cap)
map := make(map[string]float32, 100)

achievement := map[string]float32{<!-- -->
	"zhang": 99.5, "xiao": 88,
	"wange": 96, "ma": 100,
}

```

```
map1 := make(map[int][]int)
map2 := make(map[int]*[]int)

```

## 1.5 函数

### 1.5.1 声明函数

```
func function_name([parameter list]) [return_types] {<!-- -->
	//函数体
}

```

```
package main

import "fmt"

func main() {<!-- -->
	array := []int{<!-- -->6, 8, 10}
	var ret int
	ret = min(array)
	fmt.Println("最小值是: %d\n", ret)
}

func min(arr []int) (m int) {<!-- -->
	m = arr[0]
	for _, v := range arr {<!-- -->
		if v &lt; m {<!-- -->
			m = v
		}
	}
	return
}

```

```
package main

import "fmt"

func compute(x, y int) (int, int) {<!-- -->
	return x+y, x*y
}
func main() {<!-- -->
	a, b := compute(6, 8)
	fmt.Println(a, b)
}

```

```
package main

import "fmt"

func change(a, b int) (x, y int) {<!-- -->
	x = a + 100
	y = b + 100
	return
	//return x, y
	//return y, x
}
func main() {<!-- -->
	a := 1
	b := 2
	c, d := compute(a, b)
	fmt.Println(c,d)
}

```

### 1.5.2 函数参数
1. 参数使用- 形参：定义函数时，用于接收外部传入的数据。- 实参：调用函数时，传给形参的实际的数据。1. 可变参数
```
func myFunc(arg ...string) {<!-- -->
	for _, v := range arg {<!-- -->
		fmt.Printf("the string is: %s\n", v)
	}
}

```
1. 参数传递- 值传递
```
package main

import "fmt"

func exchange(a, b int) {<!-- -->
	var tmp int
	tmp = a
	a = b
	b = tmp
}
func main() {<!-- -->
	a := 1
	b := 2
	fmt.Printf("交换前a=%d\n", a)
	fmt.Printf("交换前b=%d\n", b)
	exchange(a, b)
	fmt.Printf("交换后a=%d\n", a)
	fmt.Printf("交换后b=%d\n", b)
}

```
- 引用传递
```

package main

import "fmt"

func exchange(a, b *int) {<!-- -->
	var tmp int
	tmp = *a
	*a = *b
	*b = tmp
}
func main() {<!-- -->
	a := 1
	b := 2
	fmt.Printf("交换前a=%d\n", a)
	fmt.Printf("交换前b=%d\n", b)
	exchange(&amp;a, &amp;b)
	fmt.Printf("交换后a=%d\n", a)
	fmt.Printf("交换后b=%d\n", b)
}

```

### 1.5.3 匿名函数

匿名函数（闭包），一类无须定义标识符（函数名）的函数或子程序。
1. 定义
```
func (参数列表) (返回值列表) {<!-- -->
	//函数体
}

```

```
package main

import "fmt"

func main() {<!-- -->
	x, y := 6, 8
	defer func(a int) {<!-- -->
		fmt.Println("defer x, y = ", a, y) //y为闭包引用
	}(x)
	x += 10
	y += 100
	fmt.Println(x, y)
}
/*
输出
16 108
defer x,y = 6 108
*/

```
1. 调用- 定义时调用
```
package main

import "fmt"

func main() {<!-- -->
	//定义匿名函数并赋值给f变量
	f := func(data int) {<!-- -->
		fmt.Println("closure", data)
	}
	f(6)

	//直接声明并调用
	func(data int) {<!-- -->
		fmt.Println("closure, directly", data)
	}(8)
}

```
- 回调函数（call then back）
```
package main

import "fmt"

func visitPrint(list []int, f func(int)) {<!-- -->
	for _, value := range list {<!-- -->
		f(value)
	}
}

func main() {<!-- -->
	sli := []int{<!-- -->1, 6, 8}
	visitPrint(sli, func(value int) {<!-- -->
		fmt.Println(value)
	})
}

```

### 1.5.4 defer延迟语句

defer用于函数结束（return或panic）前最后执行的动作，便于及时的释放资源（数据库连接、文件句柄、锁等）。

defer语句执行逻辑：
1. 函数执行到defer时，将defer后的语句压入专门存储defer语句的栈中，然后继续执行函数下一个语句。1. 函数执行完毕，从defer栈顶依次取出语句执行（先进后出，后进先出）。1. defer语句放在defer栈时，相关值会复制入栈中。
```
package main

import "fmt"

func main() {<!-- -->
	deferCall()
}

func deferCall() {<!-- -->
	defer func1()
	defer func2()
	defer func3()
}

func func1() {<!-- -->
	fmt.Println("A")
}

func func2() {<!-- -->
	fmt.Println("B")
}

func func3() {<!-- -->
	fmt.Println("C")
}

//输出
//C
//B
//A

```

```
package main

import "fmt"

var name string = "go"

func myfunc() string {<!-- -->
	defer func() {<!-- -->
		name = "python"	//最后一个动作，修改全局变量name为"python"
	}()

	fmt.Printf("myfunc()函数里的name: %s\n", name)//全局变量name（"go"）未修改
	return name	//倒数第二个动作，将全局变量name（"go"）赋值给myfunc函数返回值
}

func main() {<!-- -->
	myname := myfunc()
	fmt.Printf("main()函数里的name: %s\n", name)
	fmt.Printf("main()函数里的myname: %s\n", myname)
}

//输出
//myfunc()函数里的name: go
//main()函数里的name: python
//main()函数里的myname: go

```

defer常用应用场景：
1. 关闭资源。 创建资源（数据库连接、文件句柄、锁等）语句下一行，defer语句注册关闭资源，避免忘记。1. 和recover()函数一起使用。 程序宕机或panic时，recover()函数恢复执行，而不报错。
```
func f() {<!-- -->
    defer func() {<!-- -->
        if r := recover(); r != nil {<!-- -->
            fmt.Println("Recovered in f", r)
        }
    }()	//func()函数含recover，不可封装成外部函数调用，必须defer func(){}()匿名函数调用
    fmt.Println("Calling g.")
    g(0)
    fmt.Println("Returned normally from g.")
}

func g(i int) {<!-- -->
    if i &gt; 3 {<!-- -->
        fmt.Println("Panicking!")
        panic(fmt.Sprintf("%v", i))
    }
    defer fmt.Println("Defer in g", i)
    fmt.Println("Printing in g", i)
    g(i + 1)
}

```

## 1.6 Go面向对象编程

### 1.6.1 封装

隐藏对象属性和实现细节，仅公开访问方式。 Go使用结构体封装属性。

```
type Triangle struct {<!-- -->
	Bottom float32
	Height float32
}

```

方法（Methods）是作用在接收者（receiver）（某种类型的变量）上的函数。

```
func (recv recv_type) methodName(parameter_list) (return_value_list) {<!-- -->...}

```

```
package main

import "fmt"

type Triangle struct {<!-- -->
	Bottom float32
	Height float32
}

func (t *Triangle) Area() float32 {<!-- -->
	return (t.Bottom * t.Height) / 2
}

func main() {<!-- -->
	t := Triangle(6, 8)
	fmt.Println(t.Area())
}

```

访问权限指类属性是公开还是私有的，Go通过首字母大小写来控制可见性。 常量、变量、类型、接口、结构体、函数等若是大写字母开头，则能被其他包访问或调用（public）；非大写开头则只能包内使用（private）。

```
package person
type Student struct {<!-- -->
	name string
	score float32
	Age int
}

package pkg
import (
	person
	"fmt"
)
s := new(person.Student)
s.name = "yx" //错误
s.Age = 22
fmt.Println(s.Age)

```

```
package person

type Student struct {<!-- -->
	name string
	score float32
}

func (s *Student) GetName() string {<!-- -->
	return s.name
}

func (s *Student) SetName(newName string) {<!-- -->
	s.name = newName
}

package main

import (
	person
	"fmt"
)

func main() {<!-- -->
	s := new(person.Student)
	s.SetName("yx")
	s.Age = 22
	fmt.Println(s.GetName())
}

```

### 1.6.2 继承

结构体中内嵌匿名类型的方法来实现继承。

```
type Engine interface {<!-- -->
	Run()
	Stop()
}

type Bus struct {<!-- -->
	Engine
}

func (c *Bus) Working() {<!-- -->
	c.Run()
	c.Stop()
}

```

### 1.6.3 多态

多态指不同对象中同种行为的不同实现方法，通过接口实现。

```
package main

import (
	"fmt"
)

type Shape interface {<!-- -->
	Area() float32
}

type Square struct {<!-- -->
	sideLen float32
}

func (sq *Square) Area() float32 {<!-- -->
	return sq.sideLen * sq.sideLen
}

type Triangle struct {<!-- -->
	Bottom float32
	Height float32
}

func (t *Triangle) Area() float32 {<!-- -->
	return t.Bottom * t.Height
}

func main() {<!-- -->
	t := &amp;Tri8angle{<!-- -->6, 8}
	s := &amp;Square{<!-- -->}
	shapes := []Shape{<!-- -->t, s}
	for n, _ := range shapes {<!-- -->
		fmt.Println("图形数据：", shapes[n])
		fmt.Println("面积：", shapes[n].Area())
	}
}

```

## 1.7 接口

### 1.7.1 接口定义

接口类型是对其他类型行为的概括与抽象，定义了零及以上个方法，但没具体实现这些方法。 接口本质上是指针类型，可以实现多态。

```
//接口定义格式
type 接口名称 interface {<!-- -->
	method1(参数列表) 返回值列表
	method2(参数列表) 返回值列表
	//...
	methodn(参数列表) 返回值列表
}

```

空接口（interface{}），无任何方法声明，类似面向对象中的根类型，c中的void*，默认值nil。实现接口的类型支持相等运算，才能比较。

```
var var1, var2 interface{<!-- -->}
fmt.Println(var1 == nil, var1 == var2)
var1, var2 = 66, 88
fmt.Println(var1 == var2)

```

```
//比较map[string]interface{}
func CompareTwoMapInterface(data1 map[string]interface{<!-- -->}, data2 map[string]interface{<!-- -->}) bool {<!-- -->
	keySlice := make([]string, 0)
	dataSlice1 := make([]interface{<!-- -->}, 0)
	dataSlice2 := make([]interface{<!-- -->}, 0)
	for key, value := range data1 {<!-- -->
		keySlice = append(keySlice, key)
		dataSlice1 = append(dataSlice1, value)
	}
	for _, key := range keySlice {<!-- -->
		if data, ok := data2[key]; ok {<!-- -->
			dataSlice2 = append(dataSlice2, data)
		} else {<!-- -->
			return false
		}
	}
	dataStr1, _ := json.Marshal(dataSlice1)
	dataStr2, _ := json.Marshal(dataSlice2)

	return string(dataStr1) == string(dataStr2)
}

```

### 1.7.2 接口赋值

接口不支持直接实例化，但支持赋值操作。
1. 实现接口的对象实例赋值给接口
要求该对象实例实现了接口的所有方法。

```
type Num int

func (x Num) Equal(i Num) bool {<!-- -->
	return x == i
}

func (x Num) LessThan(i Num) bool {<!-- -->
	return x &lt; i
}

func (x Num) MoreThan(i Num) bool {<!-- -->
	return x &gt; i
}

func (x *Num) Multiple(i Num) {<!-- -->
	*x = *x * i
}

func (x *Num) Divide(i Num) {<!-- -->
	*x = *x / i
}

type NumI interface {<!-- -->
	Equal(i Num) bool
	LessThan(i Num) bool
	MoreThan(i Num) bool
	Multiple(i Num)
	Divide(i Num)
}

//&amp;Num实现NumI所有方法
//Num未实现NumI所有方法
var x Num = 8
var y NumI = &amp;x

/*
Go语言会根据非指针成员方法，自动生成对应的指针成员方法
func (x Num) Equal(i Num) bool
func (x *Num) Equal(i Num) bool
*/

```
1. 一个接口赋值给另一个接口
两个接口拥有相同的方法列表（与顺序无关），则等同，可相互赋值。

```
package oop1

type NumInterface1 interface {<!-- -->
	Equal(i int) bool
	LessThan(i int) bool
	BiggerThan(i int) bool
}

package oop2

type NumInterface2 interface {<!-- -->
	Equal(i int) bool
	BiggerThan(i int) bool
	LessThan(i int) bool
}

type Num int

//int不能改为Num
func (x Num) Equal(i int) bool {<!-- -->
	return int(x) == i
}

func (x Num) LessThan(i int) bool {<!-- -->
	return int(x) &lt; i
}

func (x Num) BiggerThan(i int) bool {<!-- -->
	return int(x) &gt; i
}

var f1 Num = 6
var f2 oop1.NumInterface1 = f1
var f3 oop2.NumInterface2 = f2

```

若接口A的方法列表是接口B的方法列表的子集，则接口B可以直接赋值给接口A。

```
type NumInterface1 interface {<!-- -->
	Equal(i int) bool
	LessThan(i int) bool
	BiggerThan(i int) bool
}

type NumInterface2 interface {<!-- -->
	Equal(i int) bool
	BiggerThan(i int) bool
	LessThan(i int) bool
	Sum(i int)
}

type Num int

func (x Num) Equal(i int) bool {<!-- -->
	return int(x) == i
}

func (x Num) LessThan(i int) bool {<!-- -->
	return int(x) &lt; i
}

func (x Num) BiggerThan(i int) bool {<!-- -->
	return int(x) &gt; i
}

func (x *Num) Sum(i int) {<!-- -->
	*x = *x + Num(i)
}

var f1 Num = 6
var f2 NumInterface2 = &amp;f1
var f3 NumInterface1 = f2

```

### 1.7.3 接口查询

程序运行时，询问接口指向的对象是否时某个类型。

```
var filewriter Write = ...
if filew, ok := filewriter.(*File); ok {<!-- -->
	//...
}

```

```
slice := make([]int, 0)
slice = append(slice, 6, 7, 8)
var I interface{<!-- -->} = slice
if res, ok := I.([]int); ok {<!-- -->
	fmt.Println(res) //[6 7 8]
	fmt.Println(ok) //true
}

```

```
func Len(array interface{<!-- -->}) int {<!-- -->
	var length int
	
	switch b := array.(type) {<!-- -->
	case nil:
		length = 0
	case []int:
		length = len(b)
	case []string:
		length = len(b)
	case []float32:
		length = len(b)
	default:
		length = 0
	}
	return length
}

```

### 1.7.4 接口组合

接口间通过嵌套创造出新接口。

```
type Interface1 interface {<!-- -->
	Write(p []byte) (n int, err error)
}

type Interface2 interface {<!-- -->
	Close() error
}

type InterfaceCombine interface {<!-- -->
	Interface1
	Interface2
}

```

### 1.7.5 接口应用
1. 类型推断
类型推断可将接口还原为原始类型，或用来判断是否实现了某种更具体的接口类型。

```
package main

import "fmt"

func main() {<!-- -->
	var a interface{<!-- -->} = func(a int) string {<!-- -->
		rteurn fmt.Sprintf("d:%d", a)
	}
	
	switch b := a.(type) {<!-- -->
	case nil:
		fmt.Println("nil")
	case *int:
		fmt.Println(*b)
	case func(int) string:
		fmt.Println(b(66))
	case fmt.Stringer:
		fmt.Println(b)
	default:
		fmt.Println("unknown")
	}
}

```
1. 实现多态功能
```
package main

import "fmt"

type Message interface {<!-- -->
	sending()
}

type User struct {<!-- -->
	name string
	phone string
}

func (u *User) sending() {<!-- -->
	fmt.Printf("Sending user phone to %s&lt;%s&gt;\n", u.name, u.phone)
}

type admin struct {<!-- -->
	name string
	phone string
}

func (a *admin) sending() {<!-- -->
	fmt.Printf("Sending admin phone to %s&lt;%s&gt;\n", a.name, a.phone)
}

func main() {<!-- -->
	bill := User{<!-- -->"Barry", "barry@gmail.com"}
	sendMessage(&amp;bill)
	
	lisa := admin{<!-- -->"Barry", "barry@gmail.com"}
	sendMessage(&amp;lisa)
}

func sendMessage(n Message) {<!-- -->
	n.sending()
}

```

## 1.8 反射

### 1.8.1 反射的定义

反射指，编译时不知道变量的具体类型，运行时（Run time）可以访问、检测和修改状态或行为的能力。

reflect包定义了接口和结构体，获取类型信息。
- reflect.Type接口提供类型信息- reflect.Value结构体提供值相关信息，可以获取甚至改变类型的值
```
func TypeOf(i interface{<!-- -->}) Type
func ValueOf(i interface{<!-- -->}) Value

```

### 1.8.2 反射的三大法则
1. 接口类型变量转换为反射类型对象
```
package main

import (
	"fmt"
	"reflect"
)

func main() {<!-- -->
	var x float64 = 3.4
	fmt.Println("type:", reflect.TypeOf(x))
	fmt.Println("value:", reflect.ValueOf(x))
	
	v := reflect.ValueOf(x)
	fmt.Println("type:", v.Type())
	fmt.Println("kind is float64:", v.Kind() == reflect.Float64)
	fmt.Println("value:", v.Float())
}
//输出
//type: float64
//value: 3.4
//kind is float64: true
//type: float64
//value: 3.4

```
1. 反射类型对象转换为接口类型变量
```
func (v Value) Interface() interface{<!-- -->}
y := v.Interface().(float64)
fmt.Println(y)

```

```
package main

import (
	"fmt"
	"reflect"
)

func main() {<!-- -->
	var name interface{<!-- -->} = "shirdon"
	fmt.Printf("原始接口变量类型为%T，值为%v\n", name, name)
	
	t := reflect.TypeOf(name)
	v := reflect.ValueOf(name)
	fmt.Printf("Type类型为%T，值为%v\n", t, t)
	fmt.Printf("Value类型为%T，值为%v\n", v, v)
	
	i := v.Interface()
	fmt.Printf("新对象interface{}类型为%T，值为%v\n", i, i)
}
//输出
//原始接口变量类型为string，值为shirdon
//Type类型为*reflect.rtype，值为string
//Value类型为reflect.Value，值为shirdon
//新对象interface{}类型为string，值为shirdon

```
1. 修改反射类型对象，其值必须是可写的（settable）
reflect.TypeOf()和reflect.ValueOf()函数中若传递的不是指针，则只是变量复制，对该反射对象修改，不会影响原始变量。 反射对象可写性要点:
- 变量指针创建的反射对象- CanSet()可判断- Elem()返回指针指向的数据
```
package main

import (
	"fmt"
	"reflect"
)

func main() {<!-- -->
	var name string = "shirdon"
	//var name int = 12
	
	v1 := reflect.ValueOf(&amp;name)
	v2 := v1.Elem()
	fmt.Println("可写性:", v1.CanSet())
	fmt.Println("可写性:", v2.CanSet())
}

//输出
//可写性：false
//可写性：true

```

```
func (v Value) SetBool(x bool)
func (v Value) SetBytes(x []byte)
func (v Value) SetFloat(x float64)
func (v Value) SetInt(x int64)
func (v Value) SetString(x string)

```

```
package main

import (
	"fmt"
	"reflect"
)

func main() {<!-- -->
	var name string = "shirdon"
	fmt.Println("name原始值:", name)
	
	v1 := reflect.ValueOf(&amp;name)
	v2 := v1.Elem()
	
	v2.SetString("yx")
	fmt.Println("反射对象修改后，name值:", name)
}

//输出
//name原始值: shirdon
//反射对象修改后，name值: yx

```

## 1.9 goroutine简介

每一个并发执行的活动叫goroutine。

```
go func_name()

```

```
package main

import (
	"fmt"
	"time"
)

func hello() {<!-- -->
	fmt.Println("hello")
}

func main() {<!-- -->
	go hello()
	time.Sleep(1*time.Second)
	fmt.Println("end")
}

```

## 1.10 单元测试（go test）

testing库，*_test.go文件。

```
//sum.go
package testexample

func Min(arr []int) (min int) {<!-- -->
	min = arr[0]
	for _, v := range arr {<!-- -->
		if v &lt; min {<!-- -->
			min = v
		}
	}
	return
}

//sum_test.go
package testexample

import (
	"fmt"
	"testing"
)

func TestMin(t *testing.T) {<!-- -->
	array := []int{<!-- -->6, 8, 10}
	ret := Min(array)
	fmt.Println(ret)
}

//go test
//go test -v
//go test -v -run="Test"

```

<th align="left">参数</th><th align="left">作用</th>
|------
<td align="left">-v</td><td align="left">打印每个测试函数的名字和运行时间</td>
<td align="left">-c</td><td align="left">生成测试可执行文件，但不执行，默认命名pkg.test</td>
<td align="left">-i</td><td align="left">重新安装运行测试依赖包，但不编译和运行测试代码</td>
<td align="left">-o</td><td align="left">指定生成测试可执行文件的名称</td>

## 1.11 Go编译与工具

### 1.11.1 编译（go build）

```
//build
//----main.go
//----utils.go

//main.go
package main

import (
	"fmt"
)

func main() {<!-- -->
	printString()
	fmt.Println("go build")
}

//utils.go
package main

import "fmt"

func printString() {<!-- -->
	fmt.Println("test")
}

//cd build
//go build
//go build main.go utils.go
//go build -o file.exe main.go utils.go

```

```
//pkg
//----mainpkg.go
//----buildpkg.go

//mainpkg.go
package main

import (
	"fmt"
	"pkg"
)

func main() {<!-- -->
	pkg.CallFunc()
	fmt.Println("go build")
}

//buildpkg.go
package pkg

import "fmt"

func CallFunc() {<!-- -->
	fmt.Println("test")
}
//go build .../pkg

```

```
//compile.go
package main

import (
	"fmt"
)

func main() {<!-- -->
	fmt.Println("go build")
}
//CGO_ENABLED=1 GOOS=linux GOARCH=amd64 go build compile.go

```
- CGO_ENABLED: 是否使用C语言的Go编译器；- GOOS：目标操作系统- GOARCH：目标操作系统的架构
|系统编译参数|架构
|------
|linux(&gt;=Linux 2.6)|386 / amd64 / arm
|darwin(OS X(Snow Lepoard + Lion))|386 / amd64
|freebsd(&gt;=FreeBSD 7)|386 / amd64
|windows(&gt;=Windows 2000)|386 / amd64

<th align="left">附加参数</th><th align="left">作用</th>
|------
<td align="left">-v</td><td align="left">编译时显示包名</td>
<td align="left">-p n</td><td align="left">开启并发编译，默认值为CPU逻辑核数</td>
<td align="left">-a</td><td align="left">强制重新构建</td>
<td align="left">-n</td><td align="left">打印编译时会用到的所有命令，但不真正执行</td>
<td align="left">-x</td><td align="left">打印编译时会用到的所有命令</td>
<td align="left">-race</td><td align="left">开启竞态检测</td>

### 1.11.2 编译后运行（go run）

编译后直接运行，且无可执行文件。

```
//hello.go
package main

import (
	"fmt"
)

func main() {<!-- -->
	fmt.Println("go run")
}
//go run hello.go

```

### 1.11.3 编译并安装（go install）

类似go build，只是编译中间文件放在$GOPATH/pkg目录下，编译结果放在$GOPATH/bin目录下。

```
//install
//|----main.go
//|----pkg
//    |----installpkg.go

//main.go
package main

import (
	"fmt"
	"pkg"
)

func main() {<!-- -->
	pkg.CallFunc()
	fmt.Println("go build")
}

//installpkg.go
package pkg

import "fmt"

func CallFunc() {<!-- -->
	fmt.Println("test")
}
//go install

```

### 1.11.4 获取代码（go get）

动态远程拉取或更新代码包及其依赖包，自动完成编译和安装。需要安装Git，SVN，HG等。

<th align="left">标记名称</th><th align="left">标记描述</th>
|------
<td align="left">-d</td><td align="left">只下载，不安装</td>
<td align="left">-f</td><td align="left">使用-u时才有效，忽略对已下载代码包导入路径的检查。适用于从别人处Fork代码包</td>
<td align="left">-fix</td><td align="left">下载代码包后先修正，然后编译和安装</td>
<td align="left">-insecure</td><td align="left">运行非安全scheme(如HTTP)下载代码包。</td>
<td align="left">-t</td><td align="left">同时下载测试源码文件中的依赖代码包</td>
<td align="left">-u</td><td align="left">更新已有代码包及其依赖包</td>

```
go get -u github.com/shirdon1/TP-Link-HS110

```
