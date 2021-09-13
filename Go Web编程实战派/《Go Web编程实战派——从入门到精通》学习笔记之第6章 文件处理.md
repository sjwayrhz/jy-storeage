#《Go Web编程实战派——从入门到精通》学习笔记之第6章 文件处理


### 《Go Web编程实战派——从入门到精通》学习笔记之第6章 文件处理
- - <ul><li>- <ul><li>- - - - - - - - - 


# 第6章 文件处理

## 6.1 操作目录与文件

### 6.1.1 操作目录

```
import (
	os
	path
)

```
1. 创建目录
```
//func Mkdir(name string, perm FileMode) error
package main

import (
	"fmt"
	"os"
)

func main() {<!-- -->
	//创建一个名为“test”的目录，perm权限为0777
	err := os.Mkdir("test", 0777)
	if err != nil {<!-- -->
		fmt.Println(err)
	}
}

```

```
//func MkdirAll(path string, perm FileMode) error
package main

import (
	"fmt"
	"os"
)

func main() {<!-- -->
	//根据path创建多级子目录，例如dir1/dir2/dir3
	err :=os.MkdirAll("dir1/dir2/dir3", 0777)
	if err != nil {<!-- -->
		fmt.Println(err)
	}
}

```

```
package main

import (
	"fmt"
	"os"
	"time"
)

func main()  {<!-- -->
	uploadDir := "static/upload/" + time.Now().Format("2006/01/02/")
	err := os.MkdirAll(uploadDir , 777)
	if err!=nil{<!-- -->
		fmt.Println(err)
	}
}

```
1. 重命名目录
```
//func Rename(oldpath, newpath string) error
package main

import (
	"fmt"
	"log"
	"os"
)

func main() {<!-- -->
	//创建一个名为“dir_name1”的目录，perm权限为0777
	err := os.Mkdir("dir_name1", 0777)
	if err != nil {<!-- -->
		fmt.Println(err)
	}
	oldName := "dir_name1"
	newName := "dir_name2"
	//将dir_name1重命名为dir_name2
	err = os.Rename(oldName, newName)
	if err != nil {<!-- -->
		log.Fatal(err)
	}
}

```
1. 删除目录
```
//func Remove(name string) error
//非空目录出错
package main

import (
	"log"
	"os"
)

func main() {<!-- -->
	err := os.Remove("dir1")
	if err != nil {<!-- -->
		log.Fatal(err)
	}
}

```

```
//func RemoveAll(name string) error
package main

import (
	"log"
	"os"
)

func main() {<!-- -->
	//先创建多级子目录
	os.MkdirAll("test1/test2/test3", 0777)
	//删除test1目录及其子目录
	err := os.RemoveAll("test1")
	if err != nil {<!-- -->
		log.Fatal(err)
	}
}

```
1. 遍历目录
```
//func Walk(root string, walkFn WalkFunc) error
package main

import (
	"fmt"
	"os"
	"path/filepath"
)

func scan(path string, f os.FileInfo, err error) error {<!-- -->
	fmt.Printf("Scaned: %s\n", path)
	return nil
}

func main() {<!-- -->
	//根据path创建多级子目录，例如dir1/dir2/dir3
	err :=os.MkdirAll("test_walk/dir2/dir3", 0777)
	if err != nil {<!-- -->
		fmt.Println(err)
	}
	root := `./test_walk`
	err = filepath.Walk(root, scan)
	fmt.Printf("filepath.Walk() returned %v\n", err)
}

```

### 6.1.2 创建文件

```
//func Create(name string) (*File, error)
package main

import (
	"fmt"
	"os"
)

func main() {<!-- -->
	// 创建文件
	//文件的创建，Create会根据传入的文件名创建文件，默认权限是0666
	fp, err := os.Create("./demo.txt") // 如果文件已存在，会将文件清空。
	fmt.Println(fp, err)               // &amp;{0xc000054180} &lt;nil&gt;
	fmt.Printf("%T", fp)               // *os.File 文件指针类型

	if err != nil {<!-- -->
		fmt.Println("文件创建失败。")
		//创建文件失败的原因有：
		//1、路径不存在  2、权限不足  3、打开文件数量超过上限  4、磁盘空间不足等
		return
	}

	// defer延迟调用
	defer fp.Close() //关闭文件，释放资源。
}

```

### 6.1.3 打开与关闭文件

```
//func Open(name string) (file *File, err Error)
//func (f *File) Close() error
package main

import (
	"fmt"
	"os"
)

func main() {<!-- -->
	// 打开文件
	file, err := os.Open("open.txt")
	if err != nil {<!-- -->
		fmt.Printf("打开文件出错：%v\n", err)
	}
	fmt.Println(file)
	// 关闭文件
	err = file.Close()
	if err != nil {<!-- -->
		fmt.Printf("关闭文件出错：%v\n", err)
	}
}

```

```
//func OpenFile(name string, flag int, perm uint32) (file *File, err Error)
package main

import (
	"fmt"
	"os"
)

func main() {<!-- -->
	// 读写方式打开文件
	fp, err := os.OpenFile("./open.txt", os.O_CREATE|os.O_APPEND, 0666)

	if err != nil {<!-- -->
		fmt.Println("文件打开失败。")
		return
	}

	// defer延迟调用
	defer fp.Close()  //关闭文件，释放资源。
}

```

### 6.1.4 读写文件
1. 读文件
```
//带缓冲方式读
//func NewReader(rd io.Reader) *Reader
package main

import (
	"bufio"
	"fmt"
	"io"
	"os"
)

func main() {<!-- -->
	// 打开文件
	file, err := os.Open("read.txt")
	if err != nil {<!-- -->
		fmt.Printf("打开文件出错：%v\n", err)
	}
	// 及时关闭文件句柄
	defer file.Close()
	// bufio.NewReader(rd io.Reader) *Reader
	reader := bufio.NewReader(file)
	// 循环读取文件的内容
	for {<!-- -->
		line, err := reader.ReadString('\n') // 读到一个换行符就结束
		if err == io.EOF {<!-- --> // io.EOF表示文件的末尾
			break
		}
		// 输出内容
		fmt.Print(line)
	}
}

```

```
//直接读到内存
//func ReadFile(filename string)([]byte, error)
package main

import (
	"fmt"
	"io/ioutil"
)

func main() {<!-- -->
	// 使用 io/ioutil.ReadFile 方法一次性将文件读取到内存中
	filePath := "read2.txt"
	content, err := ioutil.ReadFile(filePath)
	if err != nil {<!-- -->
		// log.Fatal(err)
		fmt.Printf("读取文件出错：%v", err)
	}
	fmt.Printf("%v\n", content)
	fmt.Printf("%v\n", string(content))
}

```
1. 写文件
```
//func (file *File) Write(b []byte) (n int, err Error)
package main

import (
	"fmt"
	"os"
)

func main() {<!-- -->
	file, err := os.OpenFile("write1.txt", os.O_CREATE|os.O_RDWR, 0666)
	if err != nil {<!-- -->
		fmt.Println(err)
	}
	defer file.Close()

	content := []byte("你好世界！")
	if _, err = file.Write(content); err != nil {<!-- -->
		fmt.Println(err)
	}
	fmt.Println("写入成功！")
}

```

```
//func (file *File) WriteAt(b []byte, off int64) (n int, err Error)
package main

import (
	"fmt"
	"os"
)

func main() {<!-- -->
	file, err := os.Create("writeAt.txt")
	if err != nil {<!-- -->
		panic(err)
	}
	defer file.Close()
	file.WriteString("Go Web编程实战派从入门到精通")
	n, err := file.WriteAt([]byte("Go语言Web"), 24)
	if err != nil {<!-- -->
		panic(err)
	}
	fmt.Println(n)
}

```

```
//func (file *File) WriteString(s string) (ret int, err Error)
package main

import (
	"os"
)

func main() {<!-- -->
	file, err := os.Create("WriteString.txt")
	if err != nil {<!-- -->
		panic(err)
	}
	defer file.Close()
	file.WriteString("Go Web编程实战派从入门到精通")
}

```

```
/*
func (file *File) WriteString(s string) (ret int, err Error) {
	return f.Write([]byte(s))
}
*/
package main

import (
	"fmt"
	"os"
)

func main() {<!-- -->
	//新建文件
	fout, err := os.Create("./write4.txt")
	if err != nil {<!-- -->
		fmt.Println(err)
		return
	}
	defer fout.Close()
	for i := 0; i &lt; 5; i++ {<!-- -->
		outstr := fmt.Sprintf("%s:%d\r\n", "Hello Go", i) //Sprintf格式化
		// 写入文件
		fout.WriteString(outstr)            //string信息
		fout.Write([]byte("i love go\r\n")) //byte类型
	}
}

```

### 6.1.5 移动和重命名文件

```
//func Rename(oldpath, newpath string) error
package main

import (
	"fmt"
	"os"
)

func main() {<!-- -->
	//创建一个名为“test_rename.txt”的空文件
	_, err := os.Create("./test_rename.txt") // 如果文件已存在，会将文件清空。
	if err != nil {<!-- -->
		fmt.Println(err)
	}
	//创建一个名为“test_rename”的目录，perm权限为0777
	err = os.Mkdir("test_rename", 0777)
	//将test_rename.txt移动到test_rename目录，并将名字重命名为test_rename_new.txt
	err = os.Rename("./test_rename.txt", "./test_rename/test_rename_new.txt")
	if err != nil {<!-- -->
		fmt.Println(err)
		return
	}
}

```

### 6.1.6 删除文件

```
//func Remove(name string) error
//func RemoveAll(name string) error
package main

import (
	"fmt"
	"os"
)

func main() {<!-- -->
	//创建一个名为“test_rename”的目录，perm权限为0777
	err := os.Mkdir("test_remove", 0777)
	if err != nil {<!-- -->
		fmt.Println(err)
	}
	fmt.Println("created dir:test_remove")
	//创建一个名为“test_remove1.txt”的空文件
	_, err = os.Create("./test_remove/test_remove1.txt") // 如果文件已存在，会将文件清空。
	if err != nil {<!-- -->
		fmt.Println(err)
	}
	fmt.Println("created file:test_remove1.txt")
	_, err = os.Create("./test_remove/test_remove2.txt")
	if err != nil {<!-- -->
		fmt.Println(err)
	}
	fmt.Println("created file:test_remove2.txt")
	_, err = os.Create("./test_remove/test_remove3.txt")
	if err != nil {<!-- -->
		fmt.Println(err)
	}
	fmt.Println("created file:test_remove3.txt")
	err = os.Remove("./test_remove/test_remove1.txt")
	if err != nil {<!-- -->
		fmt.Printf("removed ./test_remove/test_remove1.txt err : %v\n", err)
	}
	fmt.Println("removed file:./test_remove/test_remove1.txt")
	err = os.RemoveAll("./test_remove")
	if err != nil {<!-- -->
		fmt.Printf("remove all ./test_remove err : %v\n", err)
	}
	fmt.Println("removed all files:./test_remove")
}

```

### 6.1.7 复制文件

```
//func Copy(dst Writer, src Reader) (written int64, err error)
package main

import (
	"fmt"
	"io"
	"os"
)

func main() {<!-- -->
	//先创建一个名为：test_copy1.zip文件
	_, err := os.Create("./test_copy1.zip") // 如果文件已存在，会将文件清空。
	if err != nil {<!-- -->
		fmt.Println(err)
	}
	//打开文件test_copy1.zip，获取文件指针
	srcFile, err := os.Open("./test_copy1.zip")
	if err != nil {<!-- -->
		fmt.Printf("open file err = %v\n", err)
		return
	}

	defer srcFile.Close()

	//打开文件要复制的新文件名test_copy2.zip，获取文件指针
	dstFile, err := os.OpenFile("./test_copy2.zip", os.O_WRONLY|os.O_CREATE, 0755)
	if err != nil {<!-- -->
		fmt.Printf("open file err = %v\n", err)
		return
	}

	defer dstFile.Close()

	//通过Copy方法
	result, err := io.Copy(dstFile, srcFile)

	if err == nil {<!-- -->
		fmt.Println("复制成功，复制的字节数为: ", result)
	}
}

```

```
package main

import (
	"fmt"
	"io"
	"log"
	"os"
)

//自定义复制方法
func DoCopy(srcFileName string, dstFileName string) {<!-- -->
	//打开源文件
	srcFile, err := os.Open(srcFileName)
	if err != nil {<!-- -->
		log.Fatalf("源文件读取失败,err:%v\n", err)
	}
	defer func() {<!-- -->
		err = srcFile.Close()
		if err != nil {<!-- -->
			log.Fatalf("源文件关闭失败,err:%v\n", err)
		}
	}()

	//创建目标文件,稍后会向这个目标文件写入拷贝内容
	distFile, err := os.Create(dstFileName)
	if err != nil {<!-- -->
		log.Fatalf("目标文件创建失败,err:%v\n", err)
	}
	defer func() {<!-- -->
		err = distFile.Close()
		if err != nil {<!-- -->
			log.Fatalf("目标文件关闭失败,err:%v\n", err)
		}
	}()
	//定义指定长度的字节切片,每次最多读取指定长度
	var tmp = make([]byte, 1024*4)
	//循环读取并写入
	for {<!-- -->
		n, err := srcFile.Read(tmp)
		n, _ = distFile.Write(tmp[:n])
		if err != nil {<!-- -->
			if err == io.EOF {<!-- -->
				return
			} else {<!-- -->
				log.Fatalf("拷贝过程中发生错误,错误err:%v\n", err)
			}
		}
	}
}

func main() {<!-- -->
	//先创建一个.zip文件
	_, err := os.Create("./test.zip") // 如果文件已存在，会将文件清空。
	if err != nil {<!-- -->
		fmt.Println(err)
	}
	//复制一个名为test2.zip的文件
	DoCopy("./test.zip", "./test2.zip")
}

```

### 6.1.9 文件链接

```
//硬链接
//func Link(oldname, newname string) error
package main

import (
	"fmt"
	"os"
)

func main() {<!-- -->
	// 创建文件
	//文件的创建，Create会根据传入的文件名创建文件，默认权限是0666//-rw-r--r--
	fp, err := os.Create("./link1.txt") // 如果文件已存在，会将文件清空。
	// defer延迟调用
	defer fp.Close() //关闭文件，释放资源。
	if err != nil {<!-- -->
		fmt.Println("文件创建失败。")
	}
	err = os.Link("link1.txt", "link2.txt")
	if err != nil {<!-- -->
		fmt.Println("err:", err)
	}
}

```

```
//软链接
//func SymLink(oldname, newname string) error
package main

import (
	"fmt"
	"os"
)

func main() {<!-- -->
	// 创建文件
	//文件的创建，Create会根据传入的文件名创建文件，默认权限是0666//-rw-r--r--
	fp, err := os.Create("./link2.txt") // 如果文件已存在，会将文件清空。
	// defer延迟调用
	defer fp.Close() //关闭文件，释放资源。
	if err != nil {<!-- -->
		fmt.Println("文件创建失败。")
	}
	err = os.Symlink("link2.txt", "link3.txt")
	if err != nil {<!-- -->
		fmt.Println("err:", err)
	}
}

```

## 6.2 处理XML文件

XML(eXtensible Markup Language，可扩展标记语言)

### 6.2.1 解析XML文件

```
//func Unmarshal(data []byte, v interface{}) error

```

Go解析XML原则：
- 结构体<li>
```
&lt;!--email_config.xml--&gt;
&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;config&gt;
    &lt;smtpServer&gt;smtp.163.com&lt;/smtpServer&gt;
    &lt;smtpPort&gt;25&lt;/smtpPort&gt;
    &lt;sender&gt;test@163.com&lt;/sender&gt;
    &lt;senderPassword&gt;123456&lt;/senderPassword&gt;
    &lt;receivers flag="true"&gt;
        &lt;user&gt;shirdonliao@gmail.com&lt;/user&gt;
        &lt;user&gt;test99999@qq.com&lt;/user&gt;
    &lt;/receivers&gt;
&lt;/config&gt;

```

```
package main

import (
	"encoding/xml"
	"fmt"
	"io/ioutil"
	"os"
)

type EmailConfig struct {<!-- -->
	XMLName  xml.Name `xml:"config"`
	SmtpServer string `xml:"smtpServer"`
	SmtpPort int `xml:"smtpPort"`
	Sender string `xml:"sender"`
	SenderPassword string `xml:"senderPassword"`
	Receivers EmailReceivers `xml:"receivers"`
}

type EmailReceivers struct {<!-- -->
	Flag string `xml:"flag,attr"`
	User []string `xml:"user"`
}

func main() {<!-- -->
	file, err := os.Open("email_config.xml")
	if err != nil {<!-- -->
		fmt.Printf("error: %v", err)
		return
	}
	defer file.Close()
	data, err := ioutil.ReadAll(file)
	if err != nil {<!-- -->
		fmt.Printf("error: %v", err)
		return
	}
	v := EmailConfig{<!-- -->}
	err = xml.Unmarshal(data, &amp;v)
	if err != nil {<!-- -->
		fmt.Printf("error: %v", err)
		return
	}

	fmt.Println(v)
	fmt.Println("SmtpServer is : ",v.SmtpServer)
	fmt.Println("SmtpPort is : ",v.SmtpPort)
	fmt.Println("Sender is : ",v.Sender)
	fmt.Println("SenderPasswd is : ",v.SenderPassword)
	fmt.Println("Receivers.Flag is : ",v.Receivers.Flag)
	for i,element := range v.Receivers.User {<!-- -->
		fmt.Println(i,element)
	}
}

```

```
:输出
{<!-- -->{<!-- --> config} smtp.163.com 25 test@163.com 123456 {<!-- -->true [shirdonliao@gmail.com test99999@qq.com]}}
SmtpServer is :  smtp.163.com
SmtpPort is :  25
Sender is :  test@163.com
SenderPasswd is :  123456
Receivers.Flag is :  true
0 shirdonliao@gmail.com
1 test99999@qq.com

```

### 6.2.2 生成XML文件

```
//func Marshal(v interface{}) ([]byte, error)

//增加前缀和缩进
//func MarshalIndent(v interface{}, prefix, indent string) ([]byte, error)

```

```
package main

import (
	"encoding/xml"
	"fmt"
	"os"
)

type Languages struct {<!-- -->
	XMLName xml.Name `xml:"languages"`
	Version string `xml:"version,attr`
	Lang []Language `xml:"language"`
}

type Language struct {<!-- -->
	Name string `xml:"name"`
	Site string `xml:"site`
}

func main() {<!-- -->
	v := &amp;Languages{<!-- -->Version: "2"}
	v.Lang = append(v.Lang, Language{<!-- -->"JAVA", "https://www.java.com/"})
	v.Lang = append(v.Lang, Language{<!-- -->"Go", "https://golang.org/"})
	output, err := xml.MarshalIndent(v, " ", " ")
	if err != nil {<!-- -->
		fmt.Printf("error %v", err)
		return
	}
	file, _ := os.Create("languages.xml")
	defer file.Close()
	file.Write([]byte(xml.Header))	//生成XML头
	file.Write(output)
}

```

```
&lt;!--languages.xml--&gt;
&lt;?xml version="1.0" encoding="UTF-8"?&gt;
 &lt;languages&gt;
  &lt;Version&gt;2&lt;/Version&gt;
  &lt;language&gt;
   &lt;name&gt;JAVA&lt;/name&gt;
   &lt;Site&gt;https://www.java.com/&lt;/Site&gt;
  &lt;/language&gt;
  &lt;language&gt;
   &lt;name&gt;Go&lt;/name&gt;
   &lt;Site&gt;https://golang.org/&lt;/Site&gt;
  &lt;/language&gt;
 &lt;/languages&gt;

```
