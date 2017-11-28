#MySql入门
##数据库的概念
```
存储数据的仓库, 本质是一个文件系统, 数据按照特定的格式存储起来.
```

##MySql的概念
```
操作&管理数据库的大型软件.
```

##SQL语句
###概念
1. 结构化查询语句
2. ISO(国际标准)
3. 各个数据库厂商都支持ISO的SQL语句(普通话), 但通常都在此基础上有自己的扩展(方言).

###分类
####DDL(Data Definition Language)
对`数据库 database`, `表 table`, `列 column`等数据库对象进行`创建 create`, `修改 alter`, `删除 drop`等操作.
####DML(Data Manipulation Language)
对数据库中表的记录进行更新, 关键字: `插入 insert`, `删除 delete`, `更新 update`等操作.
####DQL(Data Query Language)
查询数据库中表的记录. 关键字: select, from, where等.
####DCL(Data Control Language)
定义数据库的访问权限和安全级别, 创建用户. 关键字: grant等.
####其它语句
展示数据库相关的内容等操作. 关键字:show等.

##数据库操作(命令)
###登陆
mysql -u用户名 -p密码
例如:
```
mysql -uroot -p1234
```

---
*tip:*
我通常的做法是输入`-u用户名 -p`后回车, 然后输入密码, 回车.

<!--图片浏览地址 -> https://github.com/qfeung/SQIMaterials/blob/master/Markdown/MySql_Login.png-->
![](https://github.com/qfeung/SQIMaterials/raw/master/Markdown/MySql_Login.png)	

###常用其它语句
* 查看数据库

```
// 查看所有的数据库
show databases;

// 查看某个数据库的定义信息
show create database 数据库名;
```

* 切换数据库

```
use 数据库名;
```

* 查看正在使用的数据库

```
select database();
```
* 查看表

```
// 查看数据库中所有的表
show tables;

// 查看表结构
desc 表名;
```
###DDL
* 创建数据库
	- 语法:

	```
		1. create database 数据库名
		2. create database 数据库名 character set 字符集标识符
	```
	![](https://github.com/qfeung/SQIMaterials/raw/master/Markdown/MySql_CreateDatabase.png)
	![](https://github.com/qfeung/SQIMaterials/raw/master/Markdown/MySql_ShowDataBases.png)
	
* 删除数据库
	- 语法:
	
	```
	drop database 数据库名称
	```
* 创建表
	- 语法
	
	```
	create table 表名(	字段名 类型(长度) [约束],	字段名 类型(长度) [约束],	字段名 类型(长度) [约束]);
	```
*tips:*

	```
	1. 主键约束: primary key, 要求被修饰的字段唯一&非空.
	2. 唯一约束: unique, 要求被修饰的字段唯一.
	3. 非空约束: not null, 要求被修饰的字段非空.
	```
	
* 删除表
	- 语法

	```
	drop table 表名;
	```
* 修改表
	- 语法
	
	```
	// 添加列
	alter table 表名 add 列名 类型(长度) [约束];
	// 修改列的类型, 长度&约束
	alter table 表名 modify 列名 类型(长度) [约束];
	// 修改列名
	alter table 表名 change 旧列名 新列名 类型(长度) [约束];
	// 删除列
	alter table 表名 drop 列名;
	// 修改表名
	rename table 表名 to 新表名;
	// 修改字符集
	alter table 表名 character set 字符集;
	```
	

	
