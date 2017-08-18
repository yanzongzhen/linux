# MySQL基础

#### 1.数据库基本概念

#### 2.MySQL的初步了解

#### 3.创建/删除数据库

#### 4.创建表以及表的操作



### 1.数据库基本概念

​	`数据库管理系统`(DataBase-Management System,DBMS)由一个相互关联的数据的集合和一组用以访问这些数据的程序组成。这个数据集合通常称作`数据库`(database)。

​	设计数据库的目的是为了管理大量数据。或者换句话来说，`python`或者其他语言，也是可以管理数据的，我们可以把数据放在文本里面，然后去管理，也是可以做到的，但是缺点也是很明显的，这样会异常麻烦，当我们要查询某个数据的时候，要写很的代码，但是用数据库，一条`sql`语句就搞定啦，所以使用数据库能够极大的提高我们的工作效率，同时数据库还有提供`事务`的功能。

​	`关系数据库`基于`关系模型`，使用一系列表来表达数据以及这些数据之间的关系。`MySQL`就是`关系型数据库`。关系模型已经成为当今主要的数据模型，它比之前的的网络模型和层次模型简化了编程者的工作。现在开始流行的`NoSQL`，泛指非关系型的数据库。

###2.MySQL的初步了解

​	`MySQL`是一个开放源代码的关系型数据库，有企业版和社区版，其体积小、速度快、总体拥有成本低，因此应用广泛。

`linux`上`mysql`配置文件及目录是`etc/my.cnf`，`windows`上面是安装目录下的`my.ini`。如果需要改`MySQL`的一些设置就需要在配置文件里面去改。

想要进入数据库先要满足几个条件：

1.`mysql`服务已经开启。

2.你有用户名及密码。

`linux` 上查看服务：`service mysqld status` 

`windows`上打开任务管理器查看服务

##### 连接数据库

```
本地连接：
mysql -u用户名 -p
输入密码

远程连接：
mysql  -hIP地址  -P端口 -u用户 -p
输入密码
```

查看有哪些数据库：
`SHOW DATABASES;`

使用/进入某个数据库

`USE mysql;`

判断是否在哪个数据库里:
`SELECT DATABASE();`

查看当前用户：
`SELECT user();` 



### 3.创建/删除数据库

`CREATE  {DATABASE | SCHEMA} [IF NOT EXISTS] db_name`

```
mysql> CREATE DATABASE `mydb`;
Query OK, 1 row affected (0.00 sec)
mysql> CREATE DATABASE `mydb`;
ERROR 1007 (HY000): Can't create database 'mydb'; database exists
mysql> CREATE DATABASE IF NOT EXISTS `mydb`;
Query OK, 1 row affected, 1 warning (0.00 sec)
mysql> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mydb               |
| mysql              |
| performance_schema |
| sys                |
| test               |
+--------------------+
6 rows in set (0.00 sec)
mysql>
```

#### 删除数据库

`DROP {DATABASE | SCHEMA} [IF EXISTS] dbname;`

```
mysql> DROP DATABASE `mydb`;
Query OK, 0 rows affected (0.00 sec)
mysql> DROP DATABASE `mydb`;
ERROR 1008 (HY000): Can't drop database 'mydb'; database doesn't exist
mysql> DROP DATABASE IF EXISTS `mydb`;
Query OK, 0 rows affected, 1 warning (0.00 sec)
mysql>
```

##### 注意：

```
MySQL 语句的规范
关键字与函数名称全部大写
数据库名称、表名称、字段名称全部小写，用反引号括起来
SQL语句必须以分号结尾
```

##### 打开数据库

USE 数据库名称

进入mysql后，使用`SELECT DATABASE();` 后会发现当前并没有进入到某个数据库中，需要使用`use`来进入某个数据库中。



### 4.创建表以及表的操作

##### 查看数据库中的表

数据表（或称表）
是数据库最重要的组成部分之一，是其他对象的基础

查看数据表列表
`SHOW TABLES [FROM db_name]`

`SHOW TABLES`查看当前数据库中的数据表。

`SHOW TABLES FROM 'mysql'`查看`mysql`这个数据库中的数据表。

##### 创建数据表

```
CREATE TABLE [IF NOT EXISTS] table_name(
   column_name data_type,
)
```

```
例：
mysql> CREATE TABLE `tb1`(
    -> `id` INT,
    -> `name` VARCHAR(20)
    -> );
Query OK, 0 rows affected (0.02 sec)
mysql>
```

数据类型：
INT   整数类型
VARCHAR  变长字符串

查看创建的表:

`SHOW CREATE TABLE tb_name;`

```
例：
mysql> SHOW CREATE TABLE `tb1`;
************** 1. row **********************
       Table: tb1
Create Table: CREATE TABLE `tb1` (
  `id` int(11) DEFAULT NULL,
  `name` varchar(20) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8
1 row in set (0.00 sec)
mysql>
```

查看数据表结构
`DESCRIBE tb_name;`
`SHOW COLUMNS FROM 'tb_name';` 

```
例：
mysql> DESCRIBE `tb1`;
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| id    | int(11)     | YES  |     | NULL    |       |
| name  | varchar(20) | YES  |     | NULL    |       |
+-------+-------------+------+-----+---------+-------+
2 rows in set (0.00 sec)
```

##### 删除数据表

`DROP TABLE 'tablename';` 

##### 修改数据表结构

```
添加单列:
ALTER TABLE tb1_name ADD [COLUNM] col_name
column_definition [FIRST|AFTER col-name]
```

```
例：
mysql> ALTER TABLE `tb1`
    -> ADD `age` INT
    -> ;
Query OK, 0 rows affected (0.03 sec)
Records: 0  Duplicates: 0  Warnings: 0
mysql> ALTER TABLE `tb1`
    -> ADD `number` INT FIRST
    -> ;
Query OK, 0 rows affected (0.03 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

```
添加多列:
ALTER TABLE tbl_name ADD [COLUMN]
(col_name column_definition,...)
```

```
例：
mysql> ALTER TABLE `tb1`
    -> ADD (`aa` INT,
    ->      `bb` INT,
    ->      `cc` INT
    -> );
Query OK, 0 rows affected (0.02 sec)
Records: 0  Duplicates: 0  Warnings: 0
mysql>
```

```
删除数据表中的列
ALTER TABLE tbl_name DROP [COLUMN] col_name ;
```

```
例：
mysql> ALTER TABLE `tb1`
    -> DROP `aa`
    -> ;
Query OK, 0 rows affected (0.03 sec)
Records: 0  Duplicates: 0  Warnings: 0
mysql> ALTER TABLE `tb1`
    -> DROP `bb`,
    -> DROP `cc`
    -> ;
Query OK, 0 rows affected (0.03 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

##### 向数据表中输入数据

`INSERT [INTO] tb1_name [(col_name,..)] VALUES(val,...)` 

```
例：
mysql> INSERT INTO `tb1`(`id`,`name`)
    -> VALUES(1,'rose'),
    ->       (2,'daxia')
    -> ;
Query OK, 2 rows affected (0.00 sec)
Records: 2  Duplicates: 0  Warnings: 0

mysql> SELECT * FROM `tb1`;
+------+------+--------+------+
| id   | name | gender | age  |
+------+------+--------+------+
|    1 | rose | f      |   18 |
| NULL | taka | NULL   |   18 |
+------+------+--------+------+
2 rows in
```



​	