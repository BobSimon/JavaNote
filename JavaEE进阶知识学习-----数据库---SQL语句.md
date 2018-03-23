##1.使用sql语句来管理数据库
###1.1创建数据表
格式：CREATE TABLE 表名 (字段名1 字段类型，字段名2 字段类型...)
例如：

	CREATE TABLE T_person (
		NAME VARCHAR(20) ,
		phonenumber VARCHAR (20) ,
		age INT
	)
###1.2定义非空约束
在创建表的时候要求表中某些字段是必填项，就要使用非空约束。
格式：CREATE TABLE 表名 (字段名1 字段类型 NOT NULL，字段名2 字段类型...)
例如：

	CREATE TABLE T_person (
		NAME VARCHAR(20) NOT NULL  ,
		phonenumber VARCHAR (20) NOT NULL ,
		age INT
	)
###1.3定义默认值
创建表的时候需要为某一个字段设定一个默认值的时候。
格式：CREATE TABLE 表名 (字段名1 字段类型 NOT NULL DEFAULT '张三'，字段名2 字段类型...)
例如：

	CREATE TABLE T_person (
		NAME VARCHAR(20) NOT NULL DEFAULT '张三' ,
		phonenumber VARCHAR (20) NOT NULL ,
		age INT
	)
###1.4定义主键
主键列就是可以唯一定位一条数据记录，在与外键关联的数据表也要具备具备主键，使用PRIMARY KEY关键字，
例如：
	
	CREATE TABLE T_person (
		id INT NOT NULL ,
		NAME VARCHAR(20) NOT NULL DEFAULT '张三' ,
		phonenumber VARCHAR (20) NOT NULL ,
		age INT,
		PRIMARY KEY (id) 
	)
###1.5定义外键
外键就是体现数据库关系的。使用外键才能将表与表之间连接起来，使用FOREIGN KEY关键字。
例如：

	CREATE TABLE deptment(
		id INT NOT NULL PRIMARY KEY,
		deptname VARCHAR(30)
	)
	CREATE TABLE employee(
		id INT NOT NULL PRIMARY KEY,
		empname VARCHAR(30),
		deptid INT,
		FOREIGN KEY(deptid) REFERENCES deptment (id)
	)
###1.6修改存在的表
表创建后可以对表进行添加字段操作。
格式：ALTER TABLE 表名 ADD 字段名 字段类型(大小)
例如：

	ALTER TABLE deptment ADD deptlevel VARCHAR(20)
删除已创建表中的某一个字段的操作。
格式：ALTER TABLE 表名 DROP 字段名
例如：

	ALTER TABLE deptment DROP deptlevel
###1.7删除表
这里说的是删除表包括表中的所有数据。这里的方式与sqlserver不一致
格式：DROP TABLE 表名
例如：

	DROP TABLE employee