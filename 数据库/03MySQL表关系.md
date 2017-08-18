# MySQL表关系

`20:30上课！！！`

#### 1.数据的增删改

#### 2.外键约束要求

#### 3.表关系

#### 4.外键约束的参照操作

#### 5.数据库的三范式



### 1.数据的增删改

**插入数据**

方法一：

```mysql
INSERT [INTO] table_name [(column_name,...)] 
{VALUES|VALUE} ({expr|DEFAULT},...),(...),...;   #使用[]表示可选项，{}表示二选一
```

方法二：

```mysql
INSERT [INTO] tbl_name SET col_name={expr|DEFAULT},...;
```

```mysql
mysql> CREATE TABLE `information`(
    -> `id` INT PRIMARY KEY AUTO_INCREMENT,
    -> `address` VARCHAR(20) NOT NULL,
    -> `number` INT DEFAULT 1
    -> );

mysql> INSERT INTO `information`(`address`,`number`)
    -> VALUES ('changsha',0731),
    -> ('guangzhou',DEFAULT)  #可以使用默认值
    -> ;

mysql> INSERT INTO `information` SET `address`='beijing' ,`number`=010;

#这两种插入方法，推荐大家使用第一种，使用更多
```

**更新数据**

```mysql
UPDATE  tb_name 
SET col_name1={expr1|DEFAULT}[,col_name2={expr2|DEFAULT}]...
[WHERE where_condition];
```

```mysql
mysql> SELECT * FROM `information`;

mysql> UPDATE `information` SET `address` ='shanghai',`number`=021
    -> WHERE `id`=10;
#虽然where是可选的，但是在实际当中，where往往是必须带上的，很少有情况更新整张表的某个字段，切记切记！！

mysql> CREATE TABLE `province`(
    -> `id` INT PRIMARY KEY AUTO_INCREMENT,
    -> `province` VARCHAR(20) NOT NULL,
    -> `capital` VARCHAR(20) NOT NULL,
    -> `code` INT DEFAULT 01
    -> );
    
mysql> INSERT INTO `province`(`province`,`capital`,`code`)
    -> VALUES('Beijing','beijing',01),
    -> ('Hunan','changsha',0731);

mysql> UPDATE `information` i ,`province` p
    -> SET i.`address` = p.`province`
    -> WHERE i.`number` = p.`code`;
    
mysql> SELECT * FROM `information`;
#以上是用一张表数据更新另一张表的基本用法，在Oracle和DB2中有个merge into，当更新的数据量相当大的时候，上面的方式会很慢，但是使用merge into却要快很多，大家今后如果使用到这两个数据库的时候，可以去了解一下。
```

**删除数据**

```mysql
DELETE FROM tbl_name [WHERE where_conditon]; 
```

不添加WHERE则会删除全部记录

```mysql
mysql>SELECT * FROM `tb2`;

mysql> DELETE FROM `tb2` WHERE `id`=4;
mysql> DELETE FROM `tb2` WHERE `id` IN (4,5);
#同样的，删除的数据的时候一定要带上where，否则就是整张表都清空！

mysql> SELECT * FROM `tb2`;
mysql> TRUNCATE TABLE `tb2`;
mysql> SELECT * FROM `tb2`;
#这里的TRUNCATE是清空的意思，这里是清空表，不返回清楚的数据，执行非常快，常用在临时表里面。
```



### 2.外键约束要求

外键约束`FOREIGN KEY`，保持数据一致性，完整性实现一对一或一对多关系。

##### 外键约束的要求：

数据表的存储引擎只能为InnoDB
外键列和参照列数据类型一致
外键必须关联到键上面去

```mysql
#添加外键的格式:
ALTER TABLE yourtablename  #需要添加外键的表
    ADD [CONSTRAINT 外键名] FOREIGN KEY [id] (index_col_name, ...)
    REFERENCES tbl_name (index_col_name, ...)
    [ON DELETE {CASCADE | SET NULL | NO ACTION | RESTRICT}]
    [ON UPDATE {CASCADE | SET NULL | NO ACTION | RESTRICT}]
 
 CASCADE    删除包含与已删除键值有参照关系的所有记录
 SET NULL   修改包含与已删除键值有参照关系的所有记录，使用NULL值替换（只能用于已标记为NOT NULL的字段）
 RESTRICT   拒绝删除要求，直到使用删除键值的辅助表被手工删除，并且没有参照时(这是默认设置，也是最安全的设置)
 NO ACTION  啥也不做
```

##### 一对多关系

`一对多`与`多对一`是一个概念，指的是一个实体的某个数据与另外一个实体的多个数据有关联关系。

举例，学校中一个学院可以有很多的学生，而一个学生只属于某一个学院（通常情况下），学院与学生之间的关系就是一对多的关系，通过外键关联来实现这种关系。

```mysql
#创建学院表：
mysql> CREATE TABLE `department`(
    -> `id` INT PRIMARY KEY AUTO_INCREMENT,
    -> `name` VARCHAR(10) NOT NULL,
    -> `code` INT NOT NULL
    -> );
#创建学生表
mysql> CREATE TABLE `student`(                                                                                                 
    -> `id` INT PRIMARY KEY AUTO_INCREMENT,
    -> `name` VARCHAR(10) NOT NULL,
    -> `dep_id` INT,
    -> CONSTRAINT `stu_dep_for_key` FOREIGN KEY (`dep_id`) REFERENCES `department`(`id`) ON DELETE RESTRICT                     
    -> );

#插入数据
mysql> INSERT INTO `department`(`name`,`code`)
    -> VALUES('理学院',01),
    -> ('计算机学院',02)
    -> ;
mysql> SELECT * FROM `department`;

mysql> INSERT INTO `student`(`name`,`dep_id`)
    -> VALUES('budong',1),
    -> ('Tuple',1),
    -> ('Which',2);
mysql> SELECT * FROM `student`;
```

##### 一对一关系

举例，学生表中有学号、姓名、学院，但学生还有些比如电话，家庭住址等比较私密的信息，这些信息不会放在学生表当中，会新建一个学生的详细信息表来存放。这时的学生表和学生的详细信息表两者的关系就是一对一的关系，因为一个学生只有一条详细信息。用外键加主键的方式来实现这种关系。

```mysql
mysql> DESCRIBE `student`;

#建立学生的详细信息表
mysql> CREATE TABLE `student_details`(
    -> `id` INT PRIMARY KEY,
    -> `id_card` INT NOT NULL UNIQUE KEY,
    -> `telephone` INT,
    -> CONSTRAINT `stu_deta_for_key` FOREIGN KEY (`id`) REFERENCES `student`(`id`) ON DELETE CASCADE
    -> );
#插入数据
mysql> INSERT INTO `student_details`(`id`,`id_card`,`telephone`)
    -> VALUES(1,4301,133),
    -> (2,4302,133);
#这里信息一一对应，所以一般会同步插入    
```

##### 多对多关系

一个实体的数据对应另外一个实体的多个数据，另外实体的数据也同样对应当前实体的多个数据。

举例，学生要报名选修课，一个学生可以报名多门课程，一个课程有很多的学生报名，那么学生表和课程表两者就形成了多对多关系。对于多对多关系，需要创建第三张关系表，关系表中通过外键加主键的形式实现这种关系。

```mysql
#创建课程表
mysql> CREATE TABLE `course`(
    -> `id` INT PRIMARY KEY AUTO_INCREMENT,
    -> `name` VARCHAR(20) NOT NULL
    -> );

mysql> CREATE TABLE `select`(
    -> `stu_id` INT,
    -> `coures_id` INT,
    -> PRIMARY KEY(`stu_id`,`coures_id`),
    -> CONSTRAINT `select_stu_id_for_key` FOREIGN KEY (`stu_id`) REFERENCES `student`(`id`),
    -> CONSTRAINT `select_coures_id_for_key` FOREIGN KEY (`coures_id`) REFERENCES `course`(`id`)
    -> );
```



### 4.外键约束的参照操作：

```mysql
1.CASCADE   从父表删除或更新时自动删除或更新子表中的匹配行
2.SET NULL  从父表删除或更新行时，设置子表中的外键列为NULL。如果使用该选项，必须保证子表列没有指定NOT NULL
3.RESTRICT  拒绝对父表的删除或更新操作
4.NO ACTION 标准的SQL关键字，在mysql中与RESTRICT作用相同
```

```mysql
on delete RESTRICT   #拒绝父表删除，当子表中有记录关联，则父表中不能删除该记录，除非子表更改关联记录
on update CASCADE    #父表删除，子表对应记录也删除，父表更新，子表也更新

#这里的删除更新值的是子表关联父表中的父表字段，不是父表所有的字段都不能更新
```



```mysql
mysql> SELECT * FROM `department`;
+----+-----------------+------+
| id | name            | code |
+----+-----------------+------+
|  1 | 理学院           |    1 |
|  2 | 计算机学院       |    2 |
+----+-----------------+------+

mysql> SELECT * FROM `student`;
+----+--------+--------+
| id | name   | dep_id |
+----+--------+--------+
|  1 | budong |      1 |
|  2 | Tuple  |      1 |
|  3 | Which  |      2 |
+----+--------+--------+

mysql> DELETE FROM `department` WHERE `id`=1;
ERROR 1451 (23000): Cannot delete or update a parent row: a foreign key constraint fails (`mydb`.`student`, CONSTRAINT `stu_dep_for_key` FOREIGN KEY (`dep_id`) REFERENCES `department` (`id`))
#因为外键关联的原因，不能删除父表中的记录

mysql> UPDATE `student` SET `dep_id`=1 WHERE `id`=3;
mysql> DELETE FROM `department` WHERE `id`=2;
#因为student表中的dep_id没有值关联department中的id=2的数据，所以可以删除department表中id=2的数据
```



### 5.[数据库的三范式](https://www.zhihu.com/question/24696366)

第一范式（1NF）：符合1NF的关系中的每个属性都不可再分。1NF是所有关系型数据库的最基本要求。

第二范式（2NF）：2NF在1NF的基础之上，消除了非主属性对于码的部分函数依赖。

第三范式（3NF）：3NF在2NF的基础之上，消除了非主属性对于码的传递函数依赖。

```
在上面的department和student表中，虽然可以把两个表合在一起，但是那样就会造成数据的冗余，不满足第一范式。
第二范式简单的说就是表里的每列与主属性是完全依赖，如果有部分依赖应该分离出来，形成一对多的关系。
第三范式简单的说就是所有的非主属性只在整个数据库里面出现一次。
这个解释是不严格的解释，讲三范式是告诉大家一个方向，如果今后大家要设计表结构的时候，应该从哪些方向去考虑。
```



### 总结

数据的增删改，相对用得最多的是增改，大家要熟练地写出来。

表关系我们常用外键来表示，但是也不是说非得用外键才能表示变关系，这个根据实际情况来灵活处理的，在是用外键的时候要记住外键的使用条件：`数据表的存储引擎只能为InnoDB`、`外键列和参照列数据类型一致`和`外键必须关联到键上面去`

外键的好处与不方便的地方都是在于外键约束，外键约束合理的使用能够保证数据的一致性，但是滥用同样会带来很多的麻烦。

数据库的三范式是设计数据库的基本思路 ，它的作用就是减少数据的冗余，减少脏数据的的出现，保证数据的稳健。除此之外，还有4NF、5NF和BCNF，这些都是数据库设计表结构的时候用的，如果有需要，大家可以尝试着去学习一下。