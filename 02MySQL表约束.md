# MySQL表约束

`20:30上课!!!`

#### 1.表结构操作

#### 2.非空约束

#### 3.唯一约束

#### 4.主键约束

#### 5.自增长

#### 6.默认约束

#### 7.外键约束



###1.表结构操作

`ALTER TABLE 'tbname'`

增加 ：ADD
删除 ：DROP 
修改 ：MODIFY  #改列的数据类型

​	    CHANGE  #改列名和数据类型

​	    RENAME  #改表名

```mysql
#ADD
mysql> ALTER TABLE `tb1` 
    -> ADD (`age` INT,
    -> `number` INT);
     
#DROP
mysql> ALTER TABLE `tb1` 
    -> DROP `number`;

#MODIFY
mysql> ALTER TABLE `tb1`
    -> MODIFY `age` VARCHAR(4);

#CHANGE
mysql> ALTER TABLE `tb1`  
    -> CHANGE `age` `ages` INT;    
    
#RENAME
mysql> ALTER TABLE `tb1` RENAME `tb2`;

#表名和字段名，尽量避免修改，即便是在封装得很好的情况下也要修改代码，如果是上线的东西就更加不要修改
```



###2.非空约束

数据库字段的某个值是否可以为空，`NULL`  字段值可以为空，`NOT NULL`  字段值不能为空。

```mysql
mysql> DESC `tb2`;
mysql> SELECT * FROM `tb2`；

mysql> INSERT INTO `tb2`(`id`)
    -> VALUES(4);
#允许为空，所以字段不添加值也可以插入

mysql> ALTER TABLE `tb2`
    -> MODIFY `id` INT NOT NULL;
mysql> DESC `tb2`;
mysql> INSERT `tb2`(`name`) VALUES('haha');
ERROR 1364 (HY000): Field 'id' doesn't have a default value
#在限定字段不能为空之后，插入数据的时候就必须插入数据，否则就会报错
```



### 3.唯一约束

字段添加唯一约束之后，该字段的值不重复，也就是该字段的值在该表中唯一`unique key` 

```mysql
#添加唯一约束
ALTER TABLE tbl_name ADD [CONSTRAINT[symbol]]
UNIQUE [INDEX|KEY] [index_name] [index_type]
(index_col_name)

#删除唯一约束
ALTERT TABLE tbl_name DROP {INDEX|KEY} index_name
```

```mysql
mysql> ALTER TABLE `tb2` ADD UNIQUE(`id`);
mysql> INSERT INTO `tb2`(`id`)
    -> VALUES(1);
ERROR 1062 (23000): Duplicate entry '1' for key 'id'
#已经添加的值不能再重复的插入
```

### 4.主键约束

主键保证记录的唯一性，主键自动为`NOT NULL`
每张数据表只能存在一个主键
`NOT NULL + UNIQUE KEY`

一个`UNIQUE KEY` 又是一个`NOT NULL`的时候，那么它被当做`PRIMARY KEY `主键
当一张表里没有一个主键的时候，第一个出现的非空且为唯一的列被视为有主键。

```mysql
#添加主键约束
ALTER TABLE tbl_name ADD [CONSTRAINT[sysbol]]
PRIMARY KEY [index_type] (index_col_name)

#删除主键约束
ALTER TABLE tbl_name DROP PRIMARY KEY
```

```mysql
mysql> DESCRIBE `tb2`;
#id没有申明它为主键，但是它有唯一约束和非空约束，就会默认为主键

#在创建表时声明为主键
mysql> CREATE TABLE `user`(
    -> `id` INT PRIMARY KEY,
    -> `name` VARCHAR(10),
    -> `number` INT
    -> );
mysql> DESC `user`;

#删除主键
mysql> ALTER TABLE `user` DROP PRIMARY KEY;
mysql> DESC `user`;

#添加主键
mysql> ALTER TABLE `user` ADD 
    -> PRIMARY KEY(`id`);

#插入值
mysql> INSERT INTO `user`(`id`,`name`,`number`)
    -> VALUES(1,'budong',1),
    -> (3,'tuple',3);
    
#再次插入就会报主键冲突    
mysql> INSERT INTO `user`(`id`,`name`,`number`) VALUES(1,'budong',1), (3,'tuple',3);
ERROR 1062 (23000): Duplicate entry '1' for key 'PRIMARY'
    
#大家要学会看错误码 https://wenku.baidu.com/view/f5ed2897250c844769eae009581b6bd97f19bc9d
```



### 5.自增长`AUTO_INCREMENT`

`AUTO_INCREMENT` 自动编号，且必须与主键组合使用
默认情况下，起始值为1，每次的增量为1。
当插入记录时，如果为`AUTO_INCREMENT`数据列明确指定了一个数值，则会出现两种情况，

情况一，如果插入的值与已有的编号重复，则会出现出错信息，因为AUTO_INCREMENT数据列的值必须是唯一的；

情况二，如果插入的值大于已编号的值，则会把该插入到数据列中，并使在下一个编号将从这个新值开始递增。也就是说，可以跳过一些编号。如果自增序列的最大值被删除了，则在插入新记录时，该值被重用。

```mysql
#添加自增长
mysql> ALTER TABLE `user` CHANGE `id` `id` INT NOT NULL AUTO_INCREMENT;
mysql> DESCRIBE `user`;
mysql> SELECT * FROM `user`;

#插入值
mysql> INSERT INTO `user`(`name`,`number`)
    -> VALUES('take',2),
    -> ('which',4)
    -> ;    
mysql> SELECT * FROM `user`;

#删除自增长
mysql> ALTER TABLE `user` CHANGE `id` `id` INT NOT NULL ;
mysql> DESCRIBE `user`;
```



### 6.默认约束

`DEFAULT `（默认约束）
初始值设置，插入记录时，如果没有明确为字段赋值，则自动赋予默认值。

```mysql
#添加/删除默认约束
ALTER TABLE tbl_name ALTER [COLUMN] col_name
{SET DEFAULT literal | DROP DEFAULT}
```

```mysql
#设置默认值
mysql> ALTER TABLE `user` ALTER `number` SET DEFAULT 0;
mysql> DESCRIBE `user`;    

#插入值
mysql> ALTER TABLE `user` CHANGE `id` `id` INT NOT NULL AUTO_INCREMENT;
mysql> INSERT INTO `user`(`name`) VALUES('rock');
mysql> SELECT * FROM `user`;
```

###  

### 7.外键约束`FOREING KEY `

```mysql
#外键约束要求数据表的存储引擎只能为InnoDB

#查看当前mysql服务器支持的存储引擎
SHOW ENGINES;

#编辑数据表的默认存储引擎

MySQL配置文件  ->/etc/my.cnf

[mysqld]
default-storage-engine=INNODB

#改完配置文件要重启服务
sudo service mysqld restart
```

```mysql
例：
mysql> CREATE TABLE `department`(
    -> `d_id` INT PRIMARY KEY AUTO_INCREMENT,
    -> `name` VARCHAR(20) NOT NULL
    -> );
Query OK, 0 rows affected (0.02 sec)

mysql> CREATE TABLE `students`(
    -> `s_id` INT PRIMARY KEY AUTO_INCREMENT,
    -> `name` VARCHAR(20) NOT NULL,
    -> `dept_id` INT,
    -> FOREIGN KEY(`dept_id`) REFERENCES `department`(`d_id`)
    -> );
Query OK, 0 rows affected (0.02 sec)
```