# MySQL数据库

`20:30上课！！！`

#### 1.单表查询

#### 2.多表查询

#### 3.MySQL函数

#### 4.查询SQL的优化



### 1.单表查询

查询需要数据，我们先插入一些数据。

```mysql
mysql> SELECT * FROM `department`;
mysql> SELECT * FROM `student`;
mysql> SELECT * FROM `student_details`;
mysql> SELECT * FROM `course`;
mysql> SELECT * FROM `select`;

#学院表添加数据
mysql> INSERT INTO `department`(`name`,`code`)
    -> VALUES('计算机学院',2),
    -> ('通信学院',3),
    -> ('外国语学院',4),
    -> ('化工学院',5);
    
#学生表添加数据
mysql> INSERT INTO `student`(`name`,`dep_id`)
    -> VALUES('小明',3),
    -> ('小明爸',4),
    -> ('小明妈',5),
    -> ('小新',6);
    
#学生详细信息表添加数据
mysql> INSERT INTO `student_details`(`id`,`id_card`,`telephone`)
    -> VALUES(3,4303,137),
    -> (8,4304,155),
    -> (9,4305,136),
    -> (10,3701,158),
    -> (11,2301,188);

#课程表添加数据
mysql> INSERT INTO `course`(`name`)
    -> VALUES('心理学'),
    -> ('音乐鉴赏'),
    -> ('近代史'),
    -> ('影视鉴赏');

#选课表里里面插入数据
mysql> INSERT INTO `select`(`stu_id`,`coures_id`)
    -> VALUES(8,1),
    -> (8,2),
    -> (8,3),
    -> (9,2),
    -> (9,4),
    -> (10,1),
    -> (10,3),
    -> (10,2),
    -> (11,1),
    -> (11,3);    
```

##### 查询所有记录

`SELECT * FROM  tb_name;` 

##### 查询选中列记录

`SELECT col_name1,col_name2 FROM tb_name;  ` 

##### 查询指定条件下的记录

`SELECT col_name FROM  tb_name  WHERE 条件`

##### 查询后为列取别名

`SELECT  col_name  AS  new_name  FROM  tab_name`

```mysql
#查询所以记录
mysql> SELECT * FROM `student`;

#查询选中的记录
mysql> SELECT `name` FROM `student`;

#查询指定条件下的记录
mysql> SELECT * FROM `student` WHERE `id`>3;

#查询中的别名
mysql> SELECT s.`name` AS 姓名,s.`dep_id` 学院id FROM `student` s;
#可以给查询的表命名别名，也可以给查的字段重命名，最好养成给表命别名的习惯，尤其在多表查询的时候
```



### 2.多表查询

**联表查询[join](https://dev.mysql.com/doc/refman/5.7/en/join.html)**

*内连接`[INNER| CROSS] JOIN`*

在`MySQL`里面`inner join` 和`cross join`是一样的，只是写法有点不一样，当使用`JOIN`时，默认是`inner join`

```mysql
mysql> SELECT * FROM `department` INNER JOIN `student`;
mysql> SELECT * FROM `department` , `student`;
mysql> SELECT * FROM `department` CROSS JOIN `student`;
#这三个是都是等价的，常用上两种
```

上面的是`无条件连接`，又名`交叉连接`/`笛卡尔连接`，第一张表种的每一向会和另一张表的每一项依次组合。

除了无条件连接外，还有 有条件连接，只需要在无条件连接后面加上ON子句，就是有条件，这是时候数据会根据连接条件来进行连接。

```mysql
mysql> SELECT * FROM `department` INNER JOIN `student` ON `department`.`id` = `student`.`dep_id`;

mysql> SELECT * FROM `department` p , `student` s WHERE  p.`id`=s.`dep_id`;

mysql> SELECT * FROM `department` p CROSS JOIN `student` s WHERE p.`id` = s.`dep_id`;
```



*左连接`LEFT JOIN`*

`A LEFT JOIN B ` 会以左边的表为主，展式左边表的所有数据，展式右边表中符合`ON`子句中条件的数据，没有为空。

```mysql
mysql> SELECT * FROM `student` s LEFT JOIN `student_details` sd ON s.`id`=sd.`id`;
mysql> DELETE FROM `student_details` WHERE `id`=11;
mysql> SELECT * FROM `student` s LEFT JOIN `student_details` sd ON s.`id`=sd.`id`;

#注意以下两条SQL的区别
mysql> SELECT * FROM `student` s LEFT JOIN `student_details` sd ON s.`id`=sd.`id` AND sd.`id` IS NULL;
mysql> SELECT * FROM `student` s LEFT JOIN `student_details` sd ON s.`id`=sd.`id` WHERE sd.`id` IS NULL;
#在 ON 后的时候，是在表关联的时候执行的，在 WHERE 后面是在关联之后判断的，执行顺序会在后面讲解
```

*右连接`RIGHT JOIN`*

右连接和左连接类似，只是作用相反

```mysql
mysql> SELECT * FROM `student` s RIGHT JOIN `student_details` sd ON s.`id`=sd.`id`;
#这里展式出所有 `student_details` 中的数据，`student` 中多出的数据不会展式
```

*全连接`UNION`*

`UNION` 用于合并两个或多个 `SELECT` 语句的**结果集**，并**消去**表中任何**重复行**。

```mysql
mysql> SELECT s.`name`,c.`name` FROM `student` s LEFT JOIN `select` se ON s.`id`=se.`stu_id` LEFT JOIN `course` c ON c.`id`=se.`coures_id`
    -> UNION
    -> SELECT s.`name`,c.`name` FROM `student` s LEFT JOIN `select` se ON s.`id`=se.`stu_id` LEFT JOIN `course` c ON c.`id`=se.`coures_id`
    -> ;
#两个同样的SQL，但是数据没有重复的，如果如要重复的，使用 UNION ALL 即可。
```



**子表查询**

在一个SQL语句中出现两个SQL语句，就是子表查询

```mysql
SELECT
	s.`name`,
	e.`name`
FROM
	`student` s 
LEFT JOIN(
		SELECT
			se.`stu_id`,
			c.`name`
		FROM
			`select` se
		JOIN `course` c ON se.`coures_id` = c.`id`
	) e ON s.`id`=e.`stu_id`
#LEFT JOIN 里面也有一个查询语句，这种格式，希望大家能够记住，今后写子查询的时候也用这种格式

SELECT
	*
FROM
	`student` s
WHERE
	s.`dep_id` = (
		SELECT
			`id`
		FROM
			`department` d
		WHERE
			d.`name` = '外国语学院'
	)
#子查询不但可以放在JOIN 哪里，也可以放在 WHERE 后面
```



**查询的其他操作**

*排序`ORDER BY`*

我们可以对查询出来的结果进行排序，`ASC`升序(默认)   `DESC`降序

```mysql
mysql> SELECT * FROM `student` s JOIN `student_details` sd ON s.`id`=sd.`id` ORDER BY s.`id`;

mysql> SELECT * FROM `student` s JOIN `student_details` sd ON s.`id`=sd.`id` ORDER BY  s.`id` DESC;

```

*限制显示的行数`LIMIT`*

```mysql
mysql> SELECT * FROM `student` s JOIN `student_details` sd ON s.`id`=sd.`id` ORDER BY  s.`id` DESC LIMIT 3;
#限制显示出的记录数
```

*分组查询`GROUP BY`*

```mysql
mysql> SELECT d.`id`,d.`name`,COUNT(*) FROM `department` d LEFT JOIN `student` s ON d.`id`=s.`dep_id` GROUP BY d.`id`,d.`name`;

#分组是个常见的操作，常用于分组统计，使用GROUP BY后，会按照GROUP BY后面的字段进行分组，且必须是明确的字段，不能是*，因此SELECT后面也不能是*

mysql> SELECT d.`id`,d.`name`,COUNT(*) FROM `department` d LEFT JOIN `student` s ON d.`id`=s.`dep_id` GROUP BY d.`id`,d.`name` HAVING COUNT(*)>1;

#使用 HAVING 可以对分组之后的结果进行筛选，注意：HAVING 后的字段必须是SELECT后出现过的
```





### 3.[MySQL函数](https://wenku.baidu.com/view/26cb7b959b89680203d825c1.html)

MySQL同样也有很多的函数，这些函数可以方便的我们处理一些我们查询出来的数据，合理使用这些函数，可以减少我们的代码，对于我们拿的数据进行处理提供更加便捷的方式，但是MySQL的函数也是十分多的，我这里举几个常见的。

```mysql
#处理NULL，把NULL处理成自己指定的数据
mysql> SELECT s.`name`,IFNULL(sd.`id_card`,430) FROM `student` s LEFT JOIN `student_details` sd ON s.`id`=sd.`id`;
#在Oracle和DB2中使用的NVL，用法一样

#字符串长度截取，left是从左边开始截取，right是用右边开始截取
mysql> SELECT LEFT(s.`name`,2),IFNULL(sd.`id_card`,430) FROM `student` s LEFT JOIN `student_details` sd ON s.`id`=sd.`id`;

mysql> SELECT RIGHT(s.`name`,2),IFNULL(sd.`id_card`,430) FROM `student` s LEFT JOIN `student_details` sd ON s.`id`=sd.`id`;
#SUBSTRING也是字符截取，但是可以指定其实和结束的位置
mysql> SELECT SUBSTRING(s.`name`,2,5),IFNULL(sd.`id_card`,430) FROM `student` s LEFT JOIN `student_details` sd ON s.`id`=sd.`id``;

#字符串拼接
mysql> SELECT CONCAT(s.`name`,sd.`id_card`) FROM `student` s LEFT JOIN `student_details` sd ON s.`id`=sd.`id`;

#类型转换CAST
mysql> SELECT CAST(sd.`id_card` AS CHAR) FROM `student` s LEFT JOIN `student_details` sd ON s.`id`=sd.`id`;

#CONVERT也是类型转换
mysql> SELECT CONVERT(s.`dep_id`,SIGNED) FROM `student` s ;

#DAY时间函数
mysql> SELECT DAY('2017-08-18')-DAY('2017-08-01');
#查看当前时间
mysql> SELECT NOW();
#时间函数也是用的比较多的函数，大家需要处理时间格式的时候，在晚上找下资料一般都能解决
```

`MySQL`的函数还有很多，许多也和我们平常用的一样，比如`ABS`,`MAX`,`MIN`,`ROUND`,`AVG`,`SUM`等等，用法也是一样的，大家如果有需求，直接查下资料就可以，如果需要写原生的`SQL`，能够用`MySQL`的函数处理的就尽量用`MySQL`自带的函数。



### 4.查询SQL的优化

MySQL的执行顺序

```mysql
1.FORM: 对FROM的左边的表和右边的表计算笛卡尔积。产生虚表VT1

2.ON: 对虚表VT1进行ON筛选，只有那些符合<join-condition>的行才会被记录在虚表VT2中。

3.JOIN： 如果指定了OUTER JOIN（比如left join、 right join），那么保留表中未匹配的行就会作为外部行添加到虚拟表VT2中，产生虚拟表VT3, rug from子句中包含两个以上的表的话，那么就会对上一个join连接产生的结果VT3和下一个表重复执行步骤1~3这三个步骤，一直到处理完所有的表为止。

4.WHERE： 对虚拟表VT3进行WHERE条件过滤。只有符合<where-condition>的记录才会被插入到虚拟表VT4中。

5.GROUP BY: 根据group by子句中的列，对VT4中的记录进行分组操作，产生VT5.

6.CUBE | ROLLUP: 对表VT5进行cube或者rollup操作，产生表VT6.

7.HAVING： 对虚拟表VT6应用having过滤，只有符合<having-condition>的记录才会被 插入到虚拟表VT7中。

8.SELECT： 执行select操作，选择指定的列，插入到虚拟表VT8中。

9.DISTINCT： 对VT8中的记录进行去重。产生虚拟表VT9.

10.ORDER BY: 将虚拟表VT9中的记录按照<order_by_list>进行排序操作，产生虚拟表VT10.

11.LIMIT：取出指定行的记录，产生虚拟表VT11, 并将结果返回。

```

通过上面的执行顺序不难想到，要想SQL执行更快，就必须把筛选条件尽量的往前面放。如下：

```mysql
SELECT
	s.`name`,
	e.`name`
FROM
	`student` s 
LEFT JOIN(
		SELECT
			se.`stu_id`,
			c.`name`
		FROM
			`select` se
		JOIN `course` c ON se.`coures_id` = c.`id`
	) e ON s.`id`=e.`stu_id`


SELECT
	*
FROM
	`student` s
WHERE
	s.`dep_id` = (
		SELECT
			`id`
		FROM
			`department` d
		WHERE
			d.`name` = '外国语学院'
	)
```

在这两个例子中，第一个SQL中的子表只会被查询一次，但是在第二个SQL中，子表会被执行n次，这个n取决student表中的数据条数，如果子表的数据量很大的话，那么SQL的执行速度会十分慢。

这是典型的通过执行顺序来优化SQL，除此之外，要想SQL执行快一点，应该尽量避免模糊匹配，如：like,in,not in 等这些匹配条件。

还有几点建议给大家：

1.尽量避免整表扫描，如SELECT *

2.建立合适的索引

3.使用合适的存储引擎

4.在JOIN中，尽量用小表LEFT JOIN 大表

5.除非十分必要，尽量不要使用ORDER BY,GROUP BY 和 DISTINCT(去重)，尽量用索引来代替



### 总结

数据的查询是使用数据库最基本，最常用的操作，大家一定要十分熟练，尤其是多表查询的时候，一点要先弄清楚业务需求，再去写相应的`SQL`语句。

在查询过程中，大家可以采用先分再总的顺序来查询，先分多个`SQL`把各个表中需要的数据查询出来，在把这些数据组合起来，在组合的过程用，合理的使用各种`JOIN`方法，最常用的是`LEFT JOIN`。

SQL的优化是数据库里面永恒的话题，几乎是无止境的，上面介绍的是几点基本的原则，想要写出好的SQL，需要大家不断的积累和总结。



#### 数据库的后续学习建议

我们这几天的学习，基本上把数据库里面基本的操作都学习了一遍，其实也不多，多操作就好，但是想做DBA还远远不够，加上每个数据库都不一样，一般的DBA只能是某个数据库或者一两个数据库的DBA，不过只是单纯的想更加深入的学习数据库可以先去理解数据库的索引，视图等功能，之后再考虑分区分页等存储问题，再后面可以考虑主从，分布式的实现方法和原理，这些是对有兴趣的同学给个参考的学习路线，希望大家都能够熟练的使用数据库，写出漂亮的SQL。



