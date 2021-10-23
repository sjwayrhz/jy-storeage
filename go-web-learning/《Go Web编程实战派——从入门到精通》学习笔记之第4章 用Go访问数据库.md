#《Go Web编程实战派——从入门到精通》学习笔记之第4章 用Go访问数据库


### 《Go Web编程实战派——从入门到精通》学习笔记之第4章 用Go访问数据库
- - <ul><li>- <ul><li>- - - - - - - - - - - - - - - 


# 第4章 用Go访问数据库

## 4.1 MySQL的安装与使用

### 4.1.1 MySQL简介

RDBMS(Relational Database Management System，关系型数据库管理系统)。

### 4.1.2 MySQL安装

下载地址：

（1）删除所有文件和目录，只留下bin目录和share目录。

（2）管理员身份运行CMD，切换到mysql\bin目录，初始化数据库`mysqld --initialize --console`，执行完成后，会输出 root 用户的初始默认密码，会出现data目录。

（3）安装`mysqld install`（默认名字mysql）或`mysqld --install gomysql`，gomysql是自己起的数据库服务名字

（4）启动服务`net start mysql(gomysql)`

（5）登录验证`mysql -u root -p`，输入root 用户的初始默认密码

（6）修改密码`alter user 'root'@'localhost' identified by 'root';`

（7）停止服务`net stop mysql(gomysql)`

（8）卸载`mysqld remove`或`mysqld --remove gomysql`

（9）利用配置文件my.ini（根目录）登录，`mysql`

```
[mysqld]
#设置时区为东八区，此项设置后，在连接MySQL的时候可以不用每次都手动设置时区
#default-time-zone = '+8:00'
#解决导入脚本是function报错
#log_bin_trust_function_creators=1
# 设置3306端口
port=3306
# 设置mysql的安装目录
basedir=D:\\soft\\mysql-8.0.26-winx64
# 设置 mysql数据库的数据的存放目录，MySQL 8+ 不需要以下配置，系统自己生成即可，否则有可能报错
#datadir=D:\soft\mysql-8.0.26-winx64\data
# 允许最大连接数
max_connections=200
# 允许连接失败的次数。
max_connect_errors=10
# 服务端使用的字符集默认为utf8
character-set-server=utf8mb4
# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
# 默认使用“mysql_native_password”插件认证
#default_authentication_plugin=mysql_native_password

[mysql]
# 设置mysql客户端默认字符集
default-character-set=utf8mb4

[client]
# 设置mysql客户端连接服务端时默认使用的端口
# 设置mysql客户端默认字符集
default-character-set=utf8mb4
## mysql -hlocalhost -uroot -P3306 -p
port=3306
user=root
password=root

```

### 4.1.3 MySQL基础入门
1. 数据库管理
```
CREATE DATABASE database_name; -- 创建数据库
USE database_name; #选择数据库
SHOW DATATABE [LIKE 'database_name']; /*查看数据库*/
/*
修改数据库相关参数
*/
ALTER DATABASES database_name;
ALTER DATABASES test default character SET gb2312 collate gb2312_chinese_ci;
#删除数据库
DROP DATABASES [IF EXISTS] database_name;

```
1. 数据表操作
```
#创建数据表
CREATE TABLE &lt;表名&gt; (&lt;列名1&gt;&lt;类型1&gt;[, ...]&lt;列名n&gt;&lt;类型n&gt;)[表选项][分区选项];

CREATE TABLE 'user' (
	`id` int(11) NOT NULL AUTO INCREMENT, 
	`phone` varchar(30) DEFAULT '' COMMENT '手机号', 
	`password` varchar(80) DEFAULT '' COMMENT '密码', 
	`add_time` int(10) DEFAULT '0' COMMENT '添加时间', 
	`last_ip` varchar(50) DEFAULT '' COMMENT '最近ip', 
	`email` varchar(50) DEFAULT '' COMMENT '邮编', 
	`status` tinyint(4) DEFAULT '0' COMMENT '状态', 
	PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=3 DEFAULT CHARSET=utf8;


#查看数据表
DESC[RIBE] user;	#表格信息展示表的字段信息
SHOW CREATE TABLE user;	#以SQL语句形式展示表信息

#修改数据表
ALTER TABLE &lt;表名&gt; [修改选项]
#修改选项
ADD COLUMN &lt;列名&gt; &lt;类型&gt;
CHANGE COLUMN &lt;旧列名&gt; &lt;新列名&gt; &lt;新列类型&gt;
ALTER COLUMN &lt;列名&gt; {SET DEFAULT &lt;默认值&gt;| DROP DEFAULT} 
MODIFY COLUMN &lt;列名&gt; &lt;类型&gt;
DROP COLUNM &lt;列名&gt; 
RENAME [TO] &lt;新表名&gt; 
CHARACTER SET &lt;字符集名&gt;
COLLATE &lt;校对规则名&gt;

ALTER TABLE user ADD username varchar(30) DEFAULT
 '' NULL;
 ALTER TABLE user RENAME TO user_new;
 ALTER TABLE user_new [DEFAULT] CHARACTER SET gb2312 [DEFAULT] COLLATE gb2312_chinese_ci;


#删除数据表
DROP TABLE [IF EXISTS] 表1 [, 表2, 表3 ...]
DROP TABLE user_new;

```
1. 数据库语句
```
#新增数据
INSERT [INTO] 表名 [(列名1，列名2，...)] VALUES (值1，值2，...);
INSERT INTO `user` (`phone`, `password`, `add_time`, `last_ip`, `email`, `status`) VALUES ('138888888', DEFAULT, DEFAULT, '123.55.66.3', 'test@163.com', 1);

#查询数据
SELECT 列名称 FROM 表名称 [查询条件];
SELECT `phone`, `email` FROM user LIMIT 501;
SELECT * FROM user;
SELECT `phone`, `email` FROM user WHERE `status`&gt;0;

#修改数据
UPDATE 表名称 SET 列名称=新值 WHERE更新条件;
UPDATE `user` SET `phone`='1888888' WHERE `id`=4;

#删除数据
DELETE FROM 表名称 WHERE删除条件;
DELETE FROM `user` WHERE `id`=4;
DELETE FROM `user` WHERE `status`&lt;4;
DELETE FROM `user`;

```

### 4.1.4 Go访问MySQL

```
mysql –uroot –p123456 -Dtest&lt;C:\test.sql
source C:\test.sql

```

创建数据库和数据表

```
CREATE DATABASE IF NOT EXISTS chapter4;

USE chapter4;

CREATE TABLE IF NOT EXISTS `user` (
	`uid` BIGINT(20) NOT NULL AUTO_INCREMENT,
	`name` VARCHAR(20) DEFAULT '',
	`phone` VARCHAR(20) DEFAULT '',
	PRIMARY KEY(`uid`)
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8mb4;

INSERT INTO `user` (`name`, `phone`) VALUES ('yx', '138888888');
INSERT INTO `user` (`uid`, `name`, `phone`) VALUES (111, 'yx', '138888888');

```

连接数据库

```
package main

import (
	"database/sql"
	_ "github.com/go-sql-driver/mysql"
	"log"
)

func main() {<!-- -->
	db, err := sql.Open("mysql",
		"root:root@tcp(127.0.0.1:3306)/chapter4")
	if err != nil {<!-- -->
		log.Fatal(err)
	}
	defer db.Close()
}

```

初始化连接

```
package main

import (
	"database/sql"
	"fmt"
	_ "github.com/go-sql-driver/mysql"
)

var db *sql.DB

// 定义一个初始化数据库的函数
func initDB() (err error) {<!-- -->
	//连接数据库
	db, err = sql.Open("mysql", "root:root@tcp(127.0.0.1:3306)/chapter4")
	if err != nil {<!-- -->
		return err
	}
	// 尝试与数据库建立连接（校验dsn是否正确）
	err = db.Ping()
	if err != nil {<!-- -->
		return err
	}
	return nil
}

func main() {<!-- -->
	if err := initDB(); err != nil {<!-- -->
		fmt.Printf("init db failed, err: %v\n", err)
	}
}

```

设置最大连接数 n&lt;=0，无限制，默认0。 不会超过数据库默认配置。

```
func (db *DB) SetMaxOpenConns(n int)

```

设置最大闲置连接数 n&lt;=0，无限制，默认0。 不会超过数据库默认配置。

```
func (db *DB) SetMaxOpenConns(n int)

```

SQL查询
- QueryRow()单行查询
`func (db *DB) QueryRow(query string, args ...interface{}) *Row`

```
package main

import (
	"database/sql"
	"fmt"
	_ "github.com/go-sql-driver/mysql"
)

var db *sql.DB

// 定义一个初始化数据库的函数
func initDB() (err error) {<!-- -->
	//连接数据库
	db, err = sql.Open("mysql", "root:root@tcp(127.0.0.1:3306)/chapter4")
	if err != nil {<!-- -->
		return err
	}
	// 尝试与数据库建立连接（校验dsn是否正确）
	err = db.Ping()
	if err != nil {<!-- -->
		return err
	}
	return nil
}

type User struct {<!-- -->
	Uid int
	Name string
	Phone string
}

func queryRow() {<!-- -->
	var u User;
	if err := db.QueryRow("select uid, name, phone from user where uid=?;", 1).Scan(&amp;u.Uid, &amp;u.Name, &amp;u.Phone); err != nil {<!-- -->
		fmt.Printf("scan failed, err:%v\n", err)
		return
	}
	fmt.Printf("uid:%d name:%s phone:%s\n", u.Uid, u.Name, u.Phone)
}

func main() {<!-- -->
	if err := initDB(); err != nil {<!-- -->
		fmt.Printf("init db failed, err: %v\n", err)
	}
	queryRow()
}

```
- Query()多行查询
`func (db *DB) Query(query string, args ...interface{}) (*Rows, error)`

```
package main

import (
	"database/sql"
	"fmt"
	_ "github.com/go-sql-driver/mysql"
)

var db *sql.DB

// 定义一个初始化数据库的函数
func initDB() (err error) {<!-- -->
	//连接数据库
	db, err = sql.Open("mysql", "root:root@tcp(127.0.0.1:3306)/chapter4")
	if err != nil {<!-- -->
		return err
	}
	// 尝试与数据库建立连接（校验dsn是否正确）
	err = db.Ping()
	if err != nil {<!-- -->
		return err
	}
	return nil
}

type User struct {<!-- -->
	Uid int
	Name string
	Phone string
}

func queryMultiRow() {<!-- -->
	var u User;
	rows, err := db.Query("select uid, name, phone from user where uid&gt;?;", 0)
	if err != nil {<!-- -->
		fmt.Printf("query failed, err:%v\n", err)
		return
	}
	defer rows.Close()

	for rows.Next() {<!-- -->
		err := rows.Scan(&amp;u.Uid, &amp;u.Name, &amp;u.Phone)
		if err != nil {<!-- -->
			fmt.Printf("scan failed, err:%v\n", err)
			return
		}
		fmt.Printf("uid:%d name:%s phone:%s\n", u.Uid, u.Name, u.Phone)
	}
}

func main() {<!-- -->
	if err := initDB(); err != nil {<!-- -->
		fmt.Printf("init db failed, err: %v\n", err)
	}
	queryMultiRow()
}

```
- Exec()执行一次命令（查询、删除、更新、插入等）
`func (db *DB) Exec(query string, args ...interface{}) (Result, error)` 插入数据

```
package main

import (
	"database/sql"
	"fmt"
	_ "github.com/go-sql-driver/mysql"
)

var db *sql.DB

// 定义一个初始化数据库的函数
func initDB() (err error) {<!-- -->
	//连接数据库
	db, err = sql.Open("mysql", "root:root@tcp(127.0.0.1:3306)/chapter4")
	if err != nil {<!-- -->
		return err
	}
	// 尝试与数据库建立连接（校验dsn是否正确）
	err = db.Ping()
	if err != nil {<!-- -->
		return err
	}
	return nil
}

func insertRow() {<!-- -->
	ret, err := db.Exec("insert into user(name, phone) values(?, ?);", "ml", "15906693677")
	if err != nil {<!-- -->
		fmt.Printf("insert failed, err:%v\n", err)
		return
	}
	
	uid, err := ret.LastInsertId()
	if err != nil {<!-- -->
		fmt.Printf("get lastinsert ID failed, err:%v\n", err)
		return
	}
	fmt.Printf("insert success, the id is %d.\n", uid)
	
}

func main() {<!-- -->
	if err := initDB(); err != nil {<!-- -->
		fmt.Printf("init db failed, err: %v\n", err)
	}
	insertRow()
}

```

更新数据

```
package main

import (
	"database/sql"
	"fmt"
	_ "github.com/go-sql-driver/mysql"
)

var db *sql.DB

// 定义一个初始化数据库的函数
func initDB() (err error) {<!-- -->
	//连接数据库
	db, err = sql.Open("mysql", "root:root@tcp(127.0.0.1:3306)/chapter4")
	if err != nil {<!-- -->
		return err
	}
	// 尝试与数据库建立连接（校验dsn是否正确）
	err = db.Ping()
	if err != nil {<!-- -->
		return err
	}
	return nil
}

func updateRow() {<!-- -->
	ret, err := db.Exec("update user set name=? where uid=?;", "tt", 1)
	if err != nil {<!-- -->
		fmt.Printf("update failed, err:%v\n", err)
		return
	}
	
	n, err := ret.RowsAffected()
	if err != nil {<!-- -->
		fmt.Printf("get lastinsert ID failed, err:%v\n", err)
		return
	}
	fmt.Printf("update success, affected rows:%d\n", n)
}

func main() {<!-- -->
	if err := initDB(); err != nil {<!-- -->
		fmt.Printf("init db failed, err: %v\n", err)
	}
	updateRow()
}

```

删除数据

```
package main

import (
	"database/sql"
	"fmt"
	_ "github.com/go-sql-driver/mysql"
)

var db *sql.DB

// 定义一个初始化数据库的函数
func initDB() (err error) {<!-- -->
	//连接数据库
	db, err = sql.Open("mysql", "root:root@tcp(127.0.0.1:3306)/chapter4")
	if err != nil {<!-- -->
		return err
	}
	// 尝试与数据库建立连接（校验dsn是否正确）
	err = db.Ping()
	if err != nil {<!-- -->
		return err
	}
	return nil
}

func deleteRow() {<!-- -->
	ret, err := db.Exec("delete from user where uid=?;", 2)
	if err != nil {<!-- -->
		fmt.Printf("delete failed, err:%v\n", err)
		return
	}
	
	n, err := ret.RowsAffected()
	if err != nil {<!-- -->
		fmt.Printf("get lastinsert ID failed, err:%v\n", err)
		return
	}
	fmt.Printf("update success, affected rows:%d\n", n)
}

func main() {<!-- -->
	if err := initDB(); err != nil {<!-- -->
		fmt.Printf("init db failed, err: %v\n", err)
	}
	deleteRow()
}

```

SQL执行过程：
1. 客户端对SQL语句进行占位符替换，得到完整SQL语句；1. 客户端发送完整SQL语句到MySQL服务器端；1. MySQL服务器端执行完整SQL语句，结果返回给客户端
预处理执行过程：
1. 将SQL语句分为命令部分和数据部分；1. 命令部分发送给MySQL服务器端，MySQL服务器端执行SQL预处理；1. 数据部分发送给MySQL服务器端，MySQL服务器端对SQL语句进行占位符替换；1. MySQL服务器端执行完整SQL语句，结果返回给客户端。
预处理优化MySQL服务器重复执行SQL语句的问题，提升性能。提前让服务器编译，一次编译多次执行。

MySQL预处理 `func (db *DB) Prepare(query string) (*Stmt, error)`

```
func prepareQuery() {<!-- -->
	stmt, err := db.Prepare("select uid, name, phone from `user` where uid &gt; ?;")
	if err != nil {<!-- -->
		fmt.Printf("prepare failed, err:%v\n", err)
		return
	}
	defer stmt.Close()
	
	rows, err := stmt.Query(0)
	if err != nil {<!-- -->
		fmt.Printf("query failed, err:%v\n", err)
		return
	}
	defer rows.Close()
	
	// 循环读取结果集中的数据
	for rows.Next() {<!-- -->
		err := rows.Scan(&amp;u.Uid, &amp;u.Name, &amp;u.Phone)
		if err != nil {<!-- -->
			fmt.Printf("scan failed, err:%v\n", err)
			return
		}
		fmt.Printf("uid:%d name:%s phone:%s\n", u.Uid, u.Name, u.Phone)
	}
}

```

```
func prepareInsert() {<!-- -->
	stmt, err := db.Prepare("insert into user(username, phone) values (?, ?);")
	if err != nil {<!-- -->
		fmt.Printf("prepare failed, err:%v\n", err)
		return
	}
	defer stmt.Close()
	
	_, err = stmt.Exec("barry", "18799887766")
	if err != nil {<!-- -->
		fmt.Printf("insert failed, err:%v\n", err)
		return
	}
	_, err = stmt.Exec("jim", "18988888888")
	if err != nil {<!-- -->
		fmt.Printf("insert failed, err:%v\n", err)
		return
	}
	fmt.Println("insert success.")
}

```

事务是一个最小的、不可再分的工作单位，对应一个完整业务，需多次执行DML（INSERT，UPDATE，DELETE等）语句。事务处理用来维护数据库的完整性，成批SQL语句要么都执行，要么不执行。

事务的ACID属性

<th align="left">属性</th><th align="left">解释</th>
|------
<td align="left">原子性（Atomicity，不可分割性）</td><td align="left">事务的所有操作要么全部完成，要么不执行。若执行错误，会被回滚（Rollback）到之前状态。</td>
<td align="left">一致性（Consistency）</td><td align="left">事务执行前后，未破坏数据库的完整性。</td>
<td align="left">隔离性（Isolation，独立性）</td><td align="left">允许多个事务同时读写数据。避免多个事务交叉执行导致数据不一致。分为不同级别，读未提交（read uncommitted）、读提交（read committed）、可重复读（repeatable）和串行化（serializable）。</td>
<td align="left">持久性（Durability）</td><td align="left">事务处理结束，对数据的修改是永久的。</td>

开始事务 `func (db *DB) Begin() (*Tx, error)` 提交事务 `func (tx *Tx) Commit() error` 回滚事务 `func (tx *Tx) Rollback() error`

```
func transaction() {<!-- -->
	tx, err := db.Begin() // 开启事务
	if err != nil {<!-- -->
		if tx != nil {<!-- -->
			tx.Rollback() // 回滚
		}
		fmt.Printf("begin trans failed, err:%v\n", err)
		return
	}
	_, err = tx.Exec("update user set username='james' where uid=?", 1)
	if err != nil {<!-- -->
		tx.Rollback() // 回滚
		fmt.Printf("exec sql1 failed, err:%v\n", err)
		return
	}
	_, err = tx.Exec("update user set username='james' where uid=?", 3)
	if err != nil {<!-- -->
		tx.Rollback() // 回滚
		fmt.Printf("exec sql2 failed, err:%v\n", err)
		return
	}
	err = tx.Commit() // 提交事务
	if err != nil {<!-- -->
		tx.Rollback() // 回滚
		fmt.Printf("commit failed, err:%v\n", err)
		return
	}
	fmt.Println("exec transaction success!")
}

```

SQL注入指，通过执行恶意SQL语句，将SQL代码插入数据库查询中，控制数据库服务器。使用SQL注入漏洞绕过应用程序验证（登录验证，身份验证和授权）；绕过网页，获取数据库内容；恶意修改、删除和增加数据库内容。

```
sqlInject("xxx' or 1=1#")
sqlInject("xxx' union select * from user #")
sqlInject("xxx' and (select count(-) from user) &lt;10 #")

```

```
func sqlInject(name string) {<!-- -->
	sqlStr := fmt.Sprintf("select uid, name, phone from user where name='%s'", name)
	fmt.Printf("SQL:%s\n", sqlStr)
	ret, err := db.Exec(sqlStr)
	if err != nil {<!-- -->
		fmt.Printf("update failed, err:%v\n", err)
		return
	}
	n, err := ret.RowsAffected() // 操作影响的行数
	if err != nil {<!-- -->
		fmt.Printf("get RowsAffected failed, err:%v\n", err)
		return
	}
	fmt.Printf("get success, affected rows:%d\n", n)
}

```

SQL防御措施：
1. 禁止变量直接写入SQL语句。1. 用户分级管理，控制用户权限。1. 检查用户输入，转换或过滤单引号、双引号、冒号等字符。1. 加密数据库信息。
## 4.2 Redis的安装及使用

Redis是一个开源、ANSI C语言编写、支持网络、可基于内存亦可持久化的日志型、Key-Value型数据库。数据结构服务器，可用于缓存、事件发布或订阅、高速队列等场景。

### 4.2.1 Redis的安装

下载地址：

```
redis-server.exe redis.windows.conf

```

```
redis-cli.exe -h 127.0.0.1 -p 6379

```

### 4.2.2 Redis基础入门
1. 字符串（String）
Key和Value组成。类比变量。

```
keys *	//查看所有key

set key value	//创建
set abc " a b c"

get key	//读取key，不存在返回nil

set key new_value	//不存在创建，存在则修改

set key value NX	//不存在创建，存在则返回nil

append key value	//不存在创建，存在则拼接字符串，返回len(拼接后字符串)

//key对应string非数字，报错
incr key	//数字value+1

decr key	//数字value-1

incrby key n	//数字value+n

decrby key n	//数字value-n

del key	//删除，存在返回1，不存在返回0

```
1. 哈希（Hash）
Key，Field和Value组成。类比结构体。

```
hset key field value	//添加1个键值对
hmset key field1 value1 [field2 value2]	//添加多个键值对
hsetnx key field value	//已存在字段则不修改

hget key field 	//获得1个字段值
hmget key field1 [field2]	//获得多个字段值
hgetall key 	//获得所有字段名和值
HEXISTS key field	//字段存在返回1，不存在返回0
hlen key 	//获得字段数量

```
1. 列表（List）
类比管道。

```
lpush key value	//左边插入值
rpush key value	//右边插入值

llen key 	//获得列表长度

lrange key 开始索引 结束索引	//0最左边数据，-1最右边数据

lpop key	//弹出最左边数据
rpop key	//弹出最右边数据

```
1. 集合（Set）
数据无序，不重复。

```
sadd key value1 value2 ***	//添加set元素

scard key	//返回集合中元素数量

smembers key	//返回集合中所有元素

```
1. 有序集合（Sorted Sets）
集合中的数据有序。关联double类型分数。元素不能重复，分数可以重复。

```
zadd key score1 member1 [score2 member2]	//添加数据

zadd key NX score member	//不存在则修改数据，存在则不修改

//按分数值递增获取分数区间[min max]的数据
zrangebyscore key min max [WITHSCORES] [LIMIT offset count]

//指定成员排名，分数递增
zrank key member

zscore key member	//获取成员分数，不是成员或key不存在返回nil

zcount key min max 	//获取指定分数区间，成员个数

```

### 4.2.3 Go访问Redis
1. Redis连接
```
package main

import (
	"fmt"
	"github.com/gomodule/redigo/redis"
)

func main() {<!-- -->
	conn, err := redis.Dial("tcp", "localhost:6379")
	if err != nil {<!-- -->
		fmt.Println("conn redis failed, err:", err)
		return
	}
	defer conn.Close()
}

```
1. Redis设置和获取字符串
```
package main

import (
	"fmt"
	"github.com/gomodule/redigo/redis"
)

func main() {<!-- -->
	conn, err := redis.Dial("tcp", "localhost:6379")
	if err != nil {<!-- -->
		fmt.Println("conn redis failed, err:", err)
		return
	}
	defer conn.Close()
	
	res, err := conn.Do("Set", "username", "jack");
	if err != nil {<!-- -->
		fmt.Println(err)
		return
	}
	fmt.Println(res)

	res, err = redis.String(conn.Do("Get", "username"))
	if err != nil {<!-- -->
		fmt.Println(err)
		return
	}
	fmt.Println(res)
}

```
1. Redis批量设置和获取字符串
```
package main

import (
	"fmt"
	"github.com/gomodule/redigo/redis"
)

func main() {<!-- -->
	conn, err := redis.Dial("tcp", "localhost:6379")
	if err != nil {<!-- -->
		fmt.Println("conn redis failed, err:", err)
		return
	}
	defer conn.Close()
	
	res, err := conn.Do("MSet", "username", "jack", "phone", "123456789")
	if err != nil {<!-- -->
		fmt.Println(err)
		return
	}
	fmt.Println(res)
	
	res2, err := redis.Strings(conn.Do("MGet", "username", "phone"))
	if err != nil {<!-- -->
		fmt.Println(err)
		return
	}
	fmt.Printf("%T\n", res2)
	fmt.Println(res2)

```
1. Redis hash操作
```
package main

import (
	"fmt"
	"github.com/gomodule/redigo/redis"
)

func main() {<!-- -->
	conn, err := redis.Dial("tcp", "localhost:6379")
	if err != nil {<!-- -->
		fmt.Println("conn redis failed, err:", err)
		return
	}
	defer conn.Close()
	
	res, err := conn.Do("HSet", "names", "jim", "barry")
	if err != nil {<!-- -->
		fmt.Println(err)
		return
	}
	fmt.Println(res)

	res2, err := conn.Do("HGet", "names", "jim")
	//res2, err := redis.Strings(conn.Do("HGet", "names", "jim"))
	if err != nil {<!-- -->
		fmt.Println("hget error: ", err)
		return
	}
	fmt.Printf("%T\n", res2)
	fmt.Println(res2)
	fmt.Println(string(res2.([]uint8)))

```
1. 设置过期时间
```
	res, err := conn.Do("expire", "names", 10)
	if err != nil {<!-- -->
		fmt.Println("expire error: ", err)
		return
	}
	fmt.Println(res)

```
1. Redis队列
```

package main

import (
	"fmt"
	"github.com/gomodule/redigo/redis"
)

func main() {<!-- -->
	conn, err := redis.Dial("tcp", "localhost:6379")
	if err != nil {<!-- -->
		fmt.Println("conn redis failed, err:", err)
		return
	}
	defer conn.Close()
	
	res, err := conn.Do("lpush", "Queue", "jim", "barry", 9)
	if err != nil {<!-- -->
		fmt.Println("lpush error: ", err)
		return
	}
	fmt.Println(res)

	for {<!-- -->
		r, err := redis.String(conn.Do("lpop", "Queue"))
		if err != nil {<!-- -->
			fmt.Println("lpop error: ", err)
			break
		}
		fmt.Println(r)
	}
	
	res4, err := redis.Int(conn.Do("llen", "Queue"))
	if err != nil {<!-- -->
		fmt.Println("llen error: ", err)
		return
	}
	fmt.Println(res4)
}

```
1. Redis连接池
建立网络连接耗时，连接池实现多个客户端与服务器连接且不释放，需要时获取已建立的连接，使用完后还给连接池。

```
package main

import (
	"fmt"
	"github.com/gomodule/redigo/redis"
)

var pool *redis.Pool

func init() {<!-- -->
	pool = &amp;redis.Pool{<!-- -->
		MaxIdle:     16,	//最大空闲连接数
		MaxActive:   1024,	//最大激活连接数
		IdleTimeout: 300,	//最大空闲连接等待时间
		Dial: func() (redis.Conn, error) {<!-- -->
			return redis.Dial("tcp", "localhost:6379")
		},
	}
}

func main() {<!-- -->
	c := pool.Get()
	defer c.Close()

	_, err := c.Do("Set", "username", "jack")
	if err != nil {<!-- -->
		fmt.Println(err)
		return
	}
	
	r, err := redis.String(c.Do("Get", "username"))
	if err != nil {<!-- -->
		fmt.Println(err)
		return
	}
	fmt.Println(r)
}

```
1. Redis管道
客户端发送多个命令到服务器端而无须等待响应，最后一次性读取多个响应。

```
package main

import (
	"fmt"
	"github.com/gomodule/redigo/redis"
)

func main() {<!-- -->
	c, err := redis.Dial("tcp", "localhost:6379")
	if err != nil {<!-- -->
		fmt.Println("conn redis failed, err:", err)
		return
	}
	defer c.Close()

	c.Send("SET", "username1", "jim")	//输出缓冲区写入命令
	c.Send("SET", "username2", "jack")

	c.Flush()	//清空输出缓冲区，并写入服务器端

	v, err := c.Receive()	//FIFO顺序读取服务器端响应
	fmt.Printf("v:%v,err:%v\n", v, err)
	v, err = c.Receive()
	fmt.Printf("v:%v,err:%v\n", v, err)

	v, err = c.Receive() // 一直等待
	fmt.Printf("v:%v,err:%v\n", v, err)
}

```
1. Redis并发
```
package main

import (
	"fmt"
	"github.com/gomodule/redigo/redis"
)

func main() {<!-- -->
	conn, err := redis.Dial("tcp", "localhost:6379")
	if err != nil {<!-- -->
		fmt.Println("connect redis error :", err)
		return
	}
	defer conn.Close()
	conn.Send("HSET", "students", "name", "jim", "age", "19")
	conn.Send("HSET", "students", "score", "100")
	conn.Send("HGET", "students", "age")
	conn.Flush()

	res1, err := conn.Receive()
	fmt.Printf("Receive res1:%v\n", res1)
	res2, err := conn.Receive()
	fmt.Printf("Receive res2:%v\n", res2)
	res3, err := conn.Receive()
	fmt.Printf("Receive res3:%s\n", res3)
}

```
1. Redis事务- MULTI：开启事务- EXEC：执行事务- DISCARD：取消事务- WATCH：监视事务中的建变化，一旦改变则取消事务
```
package main

import (
	"fmt"
	"github.com/gomodule/redigo/redis"
)

func main() {<!-- -->
	conn, err := redis.Dial("tcp", "localhost:6379")
	if err != nil {<!-- -->
		fmt.Println("connect redis error :", err)
		return
	}
	defer conn.Close()
	
	conn.Send("MULTI")
	conn.Send("INCR", "foo")
	conn.Send("INCR", "bar")
	r, err := conn.Do("EXEC")
	if err != nil {<!-- -->
		conn.Send("DISCARD")
		//conn.Do("DISCARD")
	}
	fmt.Println(r)
}

```

## 4.3 MongoDB的安装及使用

### 4.3.1 MongoDB的安装

下载地址： （1）创建`/MongoDB/data/db`目录 （2）运行 MongoDB 服务器，`mongod.exe --dbpath c:\data\db` （3）连接 MongoDB ，`mongo.exe`

### 4.3.2 MongoDB基础入门
1. MongoDB简介
基于分布式文件存储的非关系型数据库（NoSQL），为Web应用提供可扩展的高性能数据存储解决方案。
1. 数据库操作
启动数据库 `mongod`

数据库连接 `mongodb://[username:password@]host1[:port1][, host2[:port2], ...[/[database][?options]]`

```
mongodb://localhost
mongodb://localhost, localhost:27018, localhost:27019

```

创建数据库 `use DATABASE_NAME`

```
use mongo_db

```

删除数据库 `db.dropDatabase()`
1. 集合操作
创建集合 `db.createCollection(name, options)`

```
db.createCollection("my_collection")

```

删除集合 `db.collection.drop()`

```
db.my_collection.drop()

```
1. 文档操作
插入文档 `db.collection.insert(document)` `db.collection.save(document)`

```
db.my_collection.insert({<!-- -->"name": "tt", "address": "hz"})

```

查询文档 `db.collection.find(query, projection)`

```
db.my_collection.find({<!-- -->})
db.my_collection.find({<!-- -->"name": "tt", "address": "hz"})
db.my_collection.find({<!-- -->"name": {<!-- -->"$ne": "hz"}})
db.my_collection.find({<!-- -->}).count()
db.my_collection.find({<!-- -->}).limit(1)
db.my_collection.find({<!-- -->}).sort({<!-- -->"name": 1})

```

修改文档 `db.collection.update(&lt;query&gt;, &lt;update&gt;, {upsert: &lt;boolean&gt;, multi: &lt;boolean&gt;, writeConcern: &lt;document&gt;})`

```
db.my_collection.update({<!-- -->'name': 'barry'}, {<!-- -->$set: {<!-- -->'address': 'hz'}})

```

`db.collection.save(&lt;document&gt;, {writeConcern: &lt;document&gt;})`

```
db.my_collection.save({<!-- -->"_id": ObjectId("deetete"), "name": "jack", "address": "hz"})

```

删除文档 `db.collection.remove(&lt;query&gt;, {justOne: &lt;boolean&gt;, writeConcern: &lt;document&gt;})`

```
do.my_collection.remove({<!-- -->'name': 'jack'})

```

去重文档 `db.collection.distinct(field, query, options)`

```
db.my_collection.distinct("name", {<!-- -->"address": {<!-- -->"$ne": "sh"}})

```

### 4.3.3 Go访问MongoDB
1. 连接数据库
```
package main

import (
	"context"
	"fmt"
	"go.mongodb.org/mongo-driver/mongo"
	"go.mongodb.org/mongo-driver/mongo/options"
	"time"
)
func main() {<!-- -->
	var (
		client     *mongo.Client
		err        error
		db         *mongo.Database
		collection *mongo.Collection
	)
	//连接MongoDB
	if client, err = mongo.Connect(context.TODO(), options.Client().ApplyURI("mongodb://localhost:27017").SetConnectTimeout(5*time.Second)); err != nil {<!-- -->
		fmt.Print(err)
		return
	}
	
	//检查连接
	err = client.Ping(context.TODO(), nil)
	if err != nil {<!-- -->
		fmt.Print(err)
		return
	}
	
	//选择数据库 my_db
	db = client.Database("my_db")

	//选择表 my_collection
	collection = db.Collection("my_collection")
	fmt.Println(collection)
}

```
1. 插入一条数据
```
package main

import (
	"context"
	"fmt"
	"go.mongodb.org/mongo-driver/bson/primitive"
	"go.mongodb.org/mongo-driver/mongo"
	"go.mongodb.org/mongo-driver/mongo/options"
	"time"
)

type ExecTime struct {<!-- -->
	StartTime int64 `bson:"startTime"` //开始时间
	EndTime   int64 `bson:"endTime"`   //结束时间
}

type LogRecord struct {<!-- -->
	JobName string `bson:"jobName"` //任务名
	Command string `bson:"command"` //shell命令
	Err     string `bson:"err"`     //脚本错误
	Content string `bson:"content"` //脚本输出
	Tp      ExecTime                //执行时间
}

func main() {<!-- -->
	var (
		client     *mongo.Client
		err        error
		collection *mongo.Collection
		iResult    *mongo.InsertOneResult
		id         primitive.ObjectID
	)
	
	
	if client, err = mongo.Connect(context.TODO(), options.Client().ApplyURI("mongodb://localhost:27017").SetConnectTimeout(5*time.Second)); err != nil {<!-- -->
		fmt.Print(err)
		return
	}
	
	//选择数据库my_db里的某个表
	collection = client.Database("my_db").Collection("my_collection")

	//插入某一条数据
	logRecord := LogRecord{<!-- -->
		JobName: "job1",
		Command: "echo 1",
		Err:     "",
		Content: "1",
		Tp: ExecTime{<!-- -->
			StartTime: time.Now().Unix(),
			EndTime:   time.Now().Unix() + 10,
		},
	}
	if iResult, err = collection.InsertOne(context.TODO(), logRecord); err != nil {<!-- -->
		fmt.Print(err)
		return
	}
	
	//_id:默认生成一个全局唯一ID
	id = iResult.InsertedID.(primitive.ObjectID)
	fmt.Println("自增ID", id.Hex())
}

```
1. 批量插入数据
```
package main

import (
	"context"
	"fmt"
	"go.mongodb.org/mongo-driver/bson/primitive"
	"go.mongodb.org/mongo-driver/mongo"
	"go.mongodb.org/mongo-driver/mongo/options"
	"log"
	"time"
)

type ExecTime struct {<!-- -->
	StartTime int64 `bson:"startTime"` //开始时间
	EndTime   int64 `bson:"endTime"`   //结束时间
}

type LogRecord struct {<!-- -->
	JobName string `bson:"jobName"` //任务名
	Command string `bson:"command"` //shell命令
	Err     string `bson:"err"`     //脚本错误
	Content string `bson:"content"` //脚本输出
	Tp      ExecTime                //执行时间
}

func main() {<!-- -->
	var (
		client     *mongo.Client
		err        error
		collection *mongo.Collection
		result    *mongo.InsertManyResult
		id         primitive.ObjectID
	)
	
	
	if client, err = mongo.Connect(context.TODO(), options.Client().ApplyURI("mongodb://localhost:27017").SetConnectTimeout(5*time.Second)); err != nil {<!-- -->
		fmt.Print(err)
		return
	}
	
	//选择数据库my_db里的某个表
	collection = client.Database("my_db").Collection("test")

	//批量插入
	result, err = collection.InsertMany(context.TODO(), []interface{<!-- -->}{<!-- -->
		LogRecord{<!-- -->
			JobName: "job multil1",
			Command: "echo multil1",
			Err:     "",
			Content: "1",
			Tp: ExecTime{<!-- -->
				StartTime: time.Now().Unix(),
				EndTime:   time.Now().Unix() + 10,
			},
		},
		LogRecord{<!-- -->
			JobName: "job multil2",
			Command: "echo multil2",
			Err:     "",
			Content: "2",
			Tp: ExecTime{<!-- -->
				StartTime: time.Now().Unix(),
				EndTime:   time.Now().Unix() + 10,
			},
		},
	});
	if err != nil {<!-- -->
		log.Fatal(err)
	}
	if result == nil {<!-- -->
		log.Fatal("result nil")
	}
	
	for _, v := range result.InsertedIDs {<!-- -->
		id = v.(primitive.ObjectID)
		fmt.Println("自增ID", id.Hex())
	}
}

```
1. 查询数据
```
package main

import (
	"context"
	"fmt"
	"go.mongodb.org/mongo-driver/mongo"
	"go.mongodb.org/mongo-driver/mongo/options"
	"log"
	"time"
)

type ExecTime struct {<!-- -->
	StartTime int64 `bson:"startTime"` //开始时间
	EndTime   int64 `bson:"endTime"`   //结束时间
}

type LogRecord struct {<!-- -->
	JobName string `bson:"jobName"` //任务名
	Command string `bson:"command"` //shell命令
	Err     string `bson:"err"`     //脚本错误
	Content string `bson:"content"` //脚本输出
	Tp      ExecTime                //执行时间
}

//查询实体
type FindByJobName struct {<!-- -->
	JobName string `bson:"jobName"` //任务名
}

func main() {<!-- -->
	var (
		client     *mongo.Client
		err        error
		collection *mongo.Collection
		cursor     *mongo.Cursor
	)
	
	
	if client, err = mongo.Connect(context.TODO(), options.Client().ApplyURI("mongodb://localhost:27017").SetConnectTimeout(5*time.Second)); err != nil {<!-- -->
		fmt.Print(err)
		return
	}
	
	//选择数据库my_db里的某个表
	collection = client.Database("my_db").Collection("test")
	cond := FindByJobName{<!-- -->JobName: "job multil1"}
	if cursor, err = collection.Find(
		context.TODO(),
		cond,
		options.Find().SetSkip(0),
		options.Find().SetLimit(2)); err != nil {<!-- -->
		fmt.Println(err)
		return
	}
	defer func() {<!-- -->
		if err = cursor.Close(context.TODO()); err != nil {<!-- -->
			log.Fatal(err)
		}
	}()

	//遍历游标获取结果数据
	for cursor.Next(context.TODO()) {<!-- -->
		var lr LogRecord
		//反序列化Bson到对象
		if cursor.Decode(&amp;lr) != nil {<!-- -->
			fmt.Print(err)
			return
		}
		fmt.Println(lr)
	}
	
	var results []LogRecord
	if err = cursor.All(context.TODO(), &amp;results); err != nil {<!-- -->
		log.Fatal(err)
	}
	
	for _, result := range results {<!-- -->
		fmt.Println(result)
	}
}

```

```
package main

import (
	"context"
	"fmt"
	"go.mongodb.org/mongo-driver/bson"
	"go.mongodb.org/mongo-driver/mongo"
	"go.mongodb.org/mongo-driver/mongo/options"
	"log"
	"time"
)

type ExecTime struct {<!-- -->
	StartTime int64 `bson:"startTime"` //开始时间
	EndTime   int64 `bson:"endTime"`   //结束时间
}

type LogRecord struct {<!-- -->
	JobName string `bson:"jobName"` //任务名
	Command string `bson:"command"` //shell命令
	Err     string `bson:"err"`     //脚本错误
	Content string `bson:"content"` //脚本输出
	Tp      ExecTime                //执行时间
}

//查询实体
type FindByJobName struct {<!-- -->
	JobName string `bson:"jobName"` //任务名
}

func main() {<!-- -->
	var (
		client     *mongo.Client
		err        error
		collection *mongo.Collection
		cursor     *mongo.Cursor
	)
	
	
	if client, err = mongo.Connect(context.TODO(), options.Client().ApplyURI("mongodb://localhost:27017").SetConnectTimeout(5*time.Second)); err != nil {<!-- -->
		fmt.Print(err)
		return
	}
	
	//选择数据库my_db里的某个表
	collection = client.Database("my_db").Collection("test")
	filter := bson.M{<!-- -->"jobName":"job multil1"}
	if cursor, err = collection.Find(
		context.TODO(),
		filter,
		options.Find().SetSkip(0),
		options.Find().SetLimit(2)); err != nil {<!-- -->
		log.Fatal(err)
	}
	defer func() {<!-- -->
		if err = cursor.Close(context.TODO()); err != nil {<!-- -->
			log.Fatal(err)
		}
	}()
	
	var results []LogRecord
	if err = cursor.All(context.TODO(), &amp;results); err != nil {<!-- -->
		log.Fatal(err)
	}
	
	for _, result := range results {<!-- -->
		fmt.Println(result)
	}
}

```
1. BSON复合查询
BSON，JSON的二进制表示。 两大类型表示BSON数据： 一、D类型 4个子类
- D：BSON文档，有序map- M：无序map- A：一个BOSN数组- E：D中的一个元素
```
bson.D{<!-- -->{<!-- -->
	"name",
	bson.D{<!-- -->{<!-- -->
		"$in",
		bson.A{<!-- -->"Jim", "Jack"},
	}}
}}

```

二、Raw类型 验证字节切片，反序列化BSON

```
package main

import (
	"fmt"
	"go.mongodb.org/mongo-driver/bson"
)

func main() {<!-- -->
	testM := bson.M{<!-- -->
		"jobName": "job multi1",
	}
	var raw bson.Raw
	tmp, _ := bson.Marshal(testM)
	bson.Unmarshal(tmp, &amp;raw)

	fmt.Println(testM)
	fmt.Println(raw)
}

```

（1）聚合查询

```
package main

import (
	"context"
	"fmt"
	"go.mongodb.org/mongo-driver/bson"
	"go.mongodb.org/mongo-driver/mongo"
	"go.mongodb.org/mongo-driver/mongo/options"
	"log"
	"time"
)

func main() {<!-- -->
	var (
		client     *mongo.Client
		collection *mongo.Collection
		err        error
		cursor     *mongo.Cursor
	)
	
	if client, err = mongo.Connect(context.TODO(), options.Client().ApplyURI("mongodb://localhost:27017").SetConnectTimeout(5*time.Second)); err != nil {<!-- -->
		fmt.Print(err)
		return
	}
	
	collection = client.Database("my_db").Collection("test")
	//按照jobName分组,countJob中存储每组的数目
	groupStage := mongo.Pipeline{<!-- -->bson.D{<!-- -->
		{<!-- -->"$group", bson.D{<!-- -->
			{<!-- -->"_id", "$jobName"},
			{<!-- -->"countJob", bson.D{<!-- -->
				{<!-- -->"$sum", 1},
			}},
		}},
	}}
	if cursor, err = collection.Aggregate(context.TODO(), groupStage, ); err != nil {<!-- -->
		log.Fatal(err)
	}
	defer func() {<!-- -->
		if err = cursor.Close(context.TODO()); err != nil {<!-- -->
			log.Fatal(err)
		}
	}()
	var results []bson.M
	if err = cursor.All(context.TODO(), &amp;results); err != nil {<!-- -->
		log.Fatal(err)
	}
	for _, result := range results {<!-- -->
		fmt.Println(result)
	}
}

```

（2）

```
在这里插入代码片

```

（3）

## 4.4 Go的常见ORM库

### 4.4.1 什么是ORM

### 4.4.2 Gorm的安装及使用

```
package main

import (
	_ "github.com/mattn/go-sqlite3"
	"github.com/jinzhu/gorm"
	"crypto/md5"
	"encoding/hex"
	"fmt"
	"log"
	"os"
)

// 数据表结构体类
type GormUser struct {<!-- -->
	ID       uint   `json:"id"`
	Phone    string `json:"phone"`
	Name     string `json:"name"`
	Password string `json:"password"`
}

//md5加密
func md5Password(str string) string {<!-- -->
	h := md5.New()
	h.Write([]byte(str))
	return hex.EncodeToString(h.Sum(nil))
}

func main() {<!-- -->
	db, err := gorm.Open("sqlite3", `D:\soft\sqlite\gorm.db`)
	if err != nil {<!-- -->
		panic(err)
	}
	defer db.Close()
	
	db.DB().SetMaxIdleConns(10)
	db.DB().SetMaxOpenConns(10)
	
	db.AutoMigrate(&amp;GormUser{<!-- -->})
		
	//创建用户
	GormUser := GormUser{<!-- -->
		Phone:    "13888888888",
		Name:     "Shirdon",
		Password: md5Password("666666"), //用户密码
	}
	db.Save(&amp;GormUser) //保存到数据库
	//db.Create(&amp;GormUser) //保存到数据库
	
	//删除用户
	//var GormUser = new(GormUser)
	db.Where("phone = ?", "13888888888").Delete(&amp;GormUser)
	
	//查询用户
	//var GormUser = new(GormUser)
	db.Where("phone = ?", "18888888888").Find(&amp;GormUser)
	//db.First(&amp;GormUser, "phone = ?", "18888888888")
	fmt.Println(GormUser)
	
	//更新用户及错误处理
	//var GormUser = new(GormUser)
	err = db.Model(&amp;GormUser).Where("phone = ?", "18888888888").Update("phone", "13888888888").Error
	if err != nil {<!-- -->
		panic(err)
	}
	
	//事务处理
	//开启事务
	tx := db.Begin()
	/*
	GormUser := GormUser{
		Phone:    "18888888888",
		Name:     "Shirdon",
		Password: md5Password("666666"), //用户密码
	}
	*/
	if err := tx.Create(&amp;GormUser).Error; err != nil {<!-- -->
		//事务回滚
		tx.Rollback()
		fmt.Println(err)
	}
	db.First(&amp;GormUser, "phone = ?", "18888888888")
	//事务提交
	tx.Commit()
	
	//日志处理
	db.LogMode(true)
	db.SetLogger(log.New(os.Stdout, "\r\n", 0))
}

```

### 4.4.3 Beego ORM

## 4.5 SQLite的安装及使用

SQLite是一个进程内的库，实现了自给自足的、无服务器的、零配置的、事务性的 SQL 数据库引擎。

### 4.5.1 SQLite的安装

下载地址： （1）下载sqlite-dll-win64-x64-3360000.zip和sqlite-tools-win32-x86-3360000.zip文件 （2）启动`sqlite3`

### 4.5.2 SQLite基础入门
1. 数据库管理
```
sqlite3 test.db -- 创建数据库
.open test.db -- 创建数据库
 .databases -- 查看数据库
 .quit -- 退出
 sqlite3 testDB.db .dump &gt; testDB.sql -- 导出数据库到txt文件
 sqlite3 testDB.db &lt; testDB.sql -- txt文件导入数据库
 ATTACH DATABASE 'test.db' as 'TEST'; -- 附加数据库
 DETACH DATABASE 'TEST'; -- 分离数据库

```
1. 数据表操作
```
-- 创建数据表
CREATE TABLE database_name.table_name(
   column1 datatype  PRIMARY KEY(one or more columns),
   column2 datatype,
   column3 datatype,
   .....
   columnN datatype,
);

CREATE TABLE COMPANY(
   ID INT PRIMARY KEY     NOT NULL,
   NAME           TEXT    NOT NULL,
   AGE            INT     NOT NULL,
   ADDRESS        CHAR(50),
   SALARY         REAL
);

-- 查看数据表
 .tables
 
-- 得到表的完整信息
.schema COMPANY

-- 删除数据表
DROP TABLE database_name.table_name;
DROP TABLE COMPANY;

```
1. 数据库语句
```
-- 新增数据
INSERT INTO TABLE_NAME [(column1, column2, column3,...columnN)]  
VALUES (value1, value2, value3,...valueN);

INSERT INTO TABLE_NAME VALUES (value1,value2,value3,...valueN);

INSERT INTO COMPANY (ID,NAME,AGE,ADDRESS,SALARY)
VALUES (6, 'Kim', 22, 'South-Hall', 45000.00 );

INSERT INTO COMPANY VALUES (7, 'James', 24, 'Houston', 10000.00 );

-- 查询数据
SELECT column1, column2, columnN FROM table_name;
.header on
.mode column
SELECT * FROM COMPANY;
SELECT ID, NAME, SALARY FROM COMPANY;
 
#修改数据
UPDATE table_name
SET column1 = value1, column2 = value2...., columnN = valueN
WHERE [condition];

UPDATE COMPANY SET ADDRESS = 'Texas' WHERE ID = 6;
 UPDATE COMPANY SET ADDRESS = 'Texas', SALARY = 20000.00;


#删除数据
DELETE FROM table_name WHERE [condition];

DELETE FROM COMPANY WHERE ID = 7;
DELETE FROM COMPANY;

```

### 4.5.3 Go访问SQLite

安装msys2 配置gcc目录，set Path=%Path%;C:\msys64\mingw64\bin `go get github.com/mattn/go-sqlite3`

```
sqlite3 test.db &lt; test.sql

```

创建数据库和数据表

test.sql

```
CREATE TABLE `user` (
	`uid` INT PRIMARY KEY NOT NULL,
	`name` TEXT NOT NULL,
	`phone` CHAR(50)
);

INSERT INTO `user` (`uid`, `name`, `phone`) VALUES (15, 'yx', '138888888');
INSERT INTO `user` (`uid`, `name`, `phone`) VALUES (111, 'yx', '138888888');

```

连接数据库

```
package main

import (
	"database/sql"
	_ "github.com/mattn/go-sqlite3"
	"log"
)

func main() {<!-- -->
	db, err := sql.Open("sqlite3.exe", `D:\soft\sqlite\test.db`)
	if err != nil {<!-- -->
		log.Fatal(err)
	}
	defer db.Close()
}

```

初始化连接

```
package main

import (
	"database/sql"
	"fmt"
	_ "github.com/mattn/go-sqlite3"
)

var db *sql.DB

// 定义一个初始化数据库的函数
func initDB() (err error) {<!-- -->
	//连接数据库
	db, err = sql.Open("sqlite3", `D:\soft\sqlite\test.db`)
	if err != nil {<!-- -->
		return err
	}
	// 尝试与数据库建立连接（校验dsn是否正确）
	err = db.Ping()
	if err != nil {<!-- -->
		return err
	}
	return nil
}

func main() {<!-- -->
	if err := initDB(); err != nil {<!-- -->
		fmt.Printf("init db failed, err: %v\n", err)
	}
}

```

设置最大连接数 n&lt;=0，无限制，默认0。 不会超过数据库默认配置。

```
func (db *DB) SetMaxOpenConns(n int)

```

设置最大闲置连接数 n&lt;=0，无限制，默认0。 不会超过数据库默认配置。

```
func (db *DB) SetMaxOpenConns(n int)

```

SQL查询
- QueryRow()单行查询
`func (db *DB) QueryRow(query string, args ...interface{}) *Row`

```
package main

import (
	"database/sql"
	"fmt"
	_ "github.com/mattn/go-sqlite3"
)

var db *sql.DB

// 定义一个初始化数据库的函数
func initDB() (err error) {<!-- -->
	//连接数据库
	db, err = sql.Open("sqlite3", `D:\soft\sqlite\test.db`)
	if err != nil {<!-- -->
		return err
	}
	// 尝试与数据库建立连接（校验dsn是否正确）
	err = db.Ping()
	if err != nil {<!-- -->
		return err
	}
	return nil
}

type User struct {<!-- -->
	Uid int
	Name string
	Phone string
}

func queryRow() {<!-- -->
	var u User;
	if err := db.QueryRow("select uid, name, phone from user where uid=?;", 111).Scan(&amp;u.Uid, &amp;u.Name, &amp;u.Phone); err != nil {<!-- -->
		fmt.Printf("scan failed, err:%v\n", err)
		return
	}
	fmt.Printf("uid:%d name:%s phone:%s\n", u.Uid, u.Name, u.Phone)
}

func main() {<!-- -->
	if err := initDB(); err != nil {<!-- -->
		fmt.Printf("init db failed, err: %v\n", err)
	}
	queryRow()
}

```
- Query()多行查询
`func (db *DB) Query(query string, args ...interface{}) (*Rows, error)`

```
package main

import (
	"database/sql"
	"fmt"
	_ "github.com/mattn/go-sqlite3"
)

var db *sql.DB

// 定义一个初始化数据库的函数
func initDB() (err error) {<!-- -->
	//连接数据库
	db, err = sql.Open("sqlite3", `D:\soft\sqlite\test.db`)
	if err != nil {<!-- -->
		return err
	}
	// 尝试与数据库建立连接（校验dsn是否正确）
	err = db.Ping()
	if err != nil {<!-- -->
		return err
	}
	return nil
}

type User struct {<!-- -->
	Uid int
	Name string
	Phone string
}

func queryMultiRow() {<!-- -->
	var u User;
	rows, err := db.Query("select uid, name, phone from user where uid&gt;?;", 0)
	if err != nil {<!-- -->
		fmt.Printf("query failed, err:%v\n", err)
		return
	}
	defer rows.Close()

	for rows.Next() {<!-- -->
		err := rows.Scan(&amp;u.Uid, &amp;u.Name, &amp;u.Phone)
		if err != nil {<!-- -->
			fmt.Printf("scan failed, err:%v\n", err)
			return
		}
		fmt.Printf("uid:%d name:%s phone:%s\n", u.Uid, u.Name, u.Phone)
	}
}

func main() {<!-- -->
	if err := initDB(); err != nil {<!-- -->
		fmt.Printf("init db failed, err: %v\n", err)
	}
	queryMultiRow()
}

```
- Exec()执行一次命令（查询、删除、更新、插入等）
`func (db *DB) Exec(query string, args ...interface{}) (Result, error)`

```
package main

import (
	"database/sql"
	"fmt"
	_ "github.com/mattn/go-sqlite3"
)

var db *sql.DB

// 定义一个初始化数据库的函数
func initDB() (err error) {<!-- -->
	//连接数据库
	db, err = sql.Open("sqlite3", `D:\soft\sqlite\test.db`)
	if err != nil {<!-- -->
		return err
	}
	// 尝试与数据库建立连接（校验dsn是否正确）
	err = db.Ping()
	if err != nil {<!-- -->
		return err
	}
	return nil
}

//插入数据
func insertRow() {<!-- -->
	ret, err := db.Exec("insert into user(uid, name, phone) values(?, ?, ?);", 12, "ml", "15906693677")
	if err != nil {<!-- -->
		fmt.Printf("insert failed, err:%v\n", err)
		return
	}
	
	uid, err := ret.LastInsertId()
	if err != nil {<!-- -->
		fmt.Printf("get lastinsert ID failed, err:%v\n", err)
		return
	}
	fmt.Printf("insert success, the id is %d.\n", uid)
	
}

//更新数据
func updateRow() {<!-- -->
	ret, err := db.Exec("update user set name=? where uid=?;", "tt", 12)
	if err != nil {<!-- -->
		fmt.Printf("update failed, err:%v\n", err)
		return
	}
	
	n, err := ret.RowsAffected()
	if err != nil {<!-- -->
		fmt.Printf("get lastinsert ID failed, err:%v\n", err)
		return
	}
	fmt.Printf("update success, affected rows:%d\n", n)
}

//删除数据
func deleteRow() {<!-- -->
	ret, err := db.Exec("delete from user where uid=?;", 12)
	if err != nil {<!-- -->
		fmt.Printf("delete failed, err:%v\n", err)
		return
	}
	
	n, err := ret.RowsAffected()
	if err != nil {<!-- -->
		fmt.Printf("get lastinsert ID failed, err:%v\n", err)
		return
	}
	fmt.Printf("delete success, affected rows:%d\n", n)
}

func main() {<!-- -->
	if err := initDB(); err != nil {<!-- -->
		fmt.Printf("init db failed, err: %v\n", err)
	}
	insertRow()
	updateRow()
	deleteRow()
}

```
