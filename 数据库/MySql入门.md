#MySql入门

##一. 数据库的概念
```
存储数据的仓库, 本质是一个文件系统, 数据按照特定的格式存储起来.
```

##二. MySql的概念
```
操作&管理数据库的大型软件.
```

##三. SQL语句

###1. 概念
1. 结构化查询语句
2. ISO(国际标准)
3. 各个数据库厂商都支持ISO的SQL语句(普通话), 但通常都在此基础上有自己的扩展(方言).

###2. 分类

####2.1. DDL(Data Definition Language)

对`数据库 database`, `表 table`, `列 column`等数据库对象进行`创建 create`, `修改 alter`, `删除 drop`等操作.

####2.2. DML(Data Manipulation Language)

对数据库中表的记录进行更新, 关键字: `插入 insert`, `删除 delete`, `更新 update`等操作.

####2.3. DQL(Data Query Language)

查询数据库中表的记录. 关键字: select, from, where等.

####2.4. DCL(Data Control Language)

定义数据库的访问权限和安全级别, 创建用户. 关键字: grant等.

####2.5. 其它语句

展示数据库相关的内容等操作. 关键字:show等.

##四. 数据库操作(命令)

###1. 登陆

```
mysql -u用户名 -p密码
```

例如:

```
mysql -uroot -p1234
```

---
*tip:*
我通常的做法是输入`-u用户名 -p`后回车, 然后输入密码, 回车.

<!--图片浏览地址 -> https://github.com/qfeung/SQIMaterials/blob/master/Markdown/MySql_Login.png-->
![](https://github.com/qfeung/SQIMaterials/raw/master/Markdown/MySql_Login.png)	

###2. 常用其它语句
* 查看数据库

```
// 查看所有的数据库
show databases;

// 查看某个数据库的定义信息(包含字符集编码 - character set)
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
###3. DDL(Data Definition Language)

####3.1. 创建数据库

- 语法:

	```
		1. create database 数据库名
		2. create database 数据库名 character set 字符集标识符
	```
	![](https://github.com/qfeung/SQIMaterials/raw/master/Markdown/MySql_CreateDatabase.png)
	![](https://github.com/qfeung/SQIMaterials/raw/master/Markdown/MySql_ShowDataBases.png)
	
####3.2. 删除数据库

- 语法:
	
	```
	drop database 数据库名称
	```
####3.3. 创建表

- 语法
	
	```
	create table 表名(	字段名 类型(长度) [约束],	字段名 类型(长度) [约束],	字段名 类型(长度) [约束]	);
	```
	
*tips:*


```
	1. 主键约束: primary key, 要求被修饰的字段唯一&非空.
	2. 唯一约束: unique, 要求被修饰的字段唯一.
	3. 非空约束: not null, 要求被修饰的字段非空.
```
	
####3.4. 删除表

- 语法
	

	```
	drop table 表名;
	```
####3.5. 修改表

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
	
###4. DML(Data Manipulation Language)
	
####4.1. 插入记录

```
1. into可以省略(发现过程->忘了打into, 结果却完全没问题)
2. 列名与值的个数要相等, 顺序要一致, 类型要一致.
3. 插入的值不能超过限定的最大长度.
3. 值如果是字符串或者日期需要加引号''(一般是单引号).
```

#####4.1.1. 向表中插入某些列

```
insert into 表名 (列名1,列名2,列名3,...) values(值1,值2,值3,...);
```

![向表中插入某些列实例截图](https://github.com/qfeung/SQIMaterials/raw/master/Markdown/MySql_InsertSomeColumns.png)

#####4.1.2. 向表中插入全部列

```
insert into 表名 values(值1,值2,值3,...);
```

![向表中插入所有列实例截图](https://github.com/qfeung/SQIMaterials/raw/master/Markdown/InsertAllColumns.png)

####4.2. 更新记录

#####4.2.1 无条件的更新

```
update 表名 set 字段名=值,字段名=值,...;
```

#####4.2.2 有条件的更新

```
update 表名 set 字段名=值,字段名=值,... where 条件;
```

####4.3 删除记录

* 语法

```
// 不加[where 条件]则清空整个表(不是删除).
delete from 表名 [where 条件];
```

* 面试题

```
题目:删除表中的记录, 你是用`delete from 表名;` 还是 `trucate table 表名;`?
答:两条语句的区别可以从以下两点来分析
	- 删除方式
		* delete是逐条删除, 不清空auto_increment记录数, 所以auto_increment不会重新排列.
		* truncate是直接将表删除, 重建新表, auto_increment将重置为零, 重新开始.
	- 事物方面
		* delete删除的数据, 如果在一个事务中可以找回.
		* trucate删除的数据是无法找回的.
```


	
