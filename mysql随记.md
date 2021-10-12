# 一、易忘的

## 1、命令归类

### 1.1命令分类

- DQL（数据查询语言）：最常用，select
- DML（数据控制语言）：数据层面，有增删改--insert，delete，update
- DDL（数据定义语言）：表层面，也有增删改--create，drop，alter
- TCL（事务控制语言）：表层面，commit,rollback
- DCL（数据控制语言）：数据层面，grant（授权），revoke（删除权限）

### 1.2登录命令

```mysql
#	-h：ip地址  -p：端口   -u：用户名  -p：密码
PS C:\Users\Y> mysql -hlocalhost -p3306 -uroot -proot
```

- 注意，登录mysql前必须先启动mysql服务，而且必须以管理员身份运行cmd窗口：

```mysql
PS C:\Users\Y> net start mysql
MySQL 服务正在启动 .
MySQL 服务已经启动成功。
```

​			或者在服务中开启mysql服务（我的已经开启了mysql服务）：

![image-20211012184742542](C:\Users\Y\AppData\Roaming\Typora\typora-user-images\image-20211012184742542.png)

## 2、alter改变表结构的几个用法

alter 表名  +  ：

- add 字段 字段类型 ：增加字段类型

  ```mysql
  mysql> alter table t_student add score int;
  Query OK, 6 rows affected (0.01 sec)
  Records: 6  Duplicates: 0  Warnings: 0
  ```

- drop 字段 ：删除字段

  ```mysql
  mysql> alter table t_student drop score;
  Query OK, 6 rows affected (0.01 sec)
  Records: 6  Duplicates: 0  Warnings: 0
  ```

- modify 字段 字段类型：修改字段类型

  ```mysql
  mysql> alter table t_student modify sex char(4);
  Query OK, 0 rows affected (0.00 sec)
  Records: 0  Duplicates: 0  Warnings: 0
  ```

- change 旧字段名称 新字段名称 新字段类型 是否空值等

  ```mysql
  mysql> alter table t_student change sex gender char(4) not null;
  Query OK, 6 rows affected (0.01 sec)
  Records: 6  Duplicates: 0  Warnings: 0
  ```

  

## 3、命令的书写顺序、执行顺序

- 书写顺序：select。。from。。where。。group by。。having。。order by 。。limit。。

- 执行顺序：

  1. from

  2. where

  3. group by

  4. having

  5. select

  6. order by

  7. limit。。

     

## 4、主键约束

### 4.1概念

> 主键约束：就是一种约束（primary key，简称PK）
>
> 主键字段：字段上添加了主键约束，这样的字段叫做--主键字段。
>
> 主键值：主键字段中的每一个值都叫做--主键值。

- 在mysql当中，如果一个字段同时被not null和unique约束的话,该字段自动变成主键字段。（注意：oracle中不一样！）

```mysql
create table t_vip(
			id int,
			name varchar(255) not null unique
		);

		mysql> desc t_vip;
		+-------+--------------+------+-----+---------+-------+
		| Field | Type         | Null | Key | Default | Extra |
		+-------+--------------+------+-----+---------+-------+
		| id    | int(11)      | YES  |     | NULL    |       |
		| name  | varchar(255) | NO   | PRI | NULL    |       |
		+-------+--------------+------+-----+---------+-------+
```

- 什么是主键？有啥用？
  		主键值是每一行记录的唯一标识。
    		主键值是每一行记录的身份证号！！！

- 记住：任何一张表都应该有主键，没有主键，表无效！！

- 主键的特征：not null + unique（主键值不能是NULL，同时也不能重复！）

- 主键约束只能添加1个，复合主键是一个主键约束里包含多个字段。

### 4.2添加主键约束

- 列级约束

  ```mysql
  drop table if exists t_vip;
  		# 1个字段做主键，叫做：单一主键
  		create table t_vip(
  			id int primary key,  #列级约束
  			name varchar(255)
  		);
  		insert into t_vip(id,name) values(1,'zhangsan');
  		insert into t_vip(id,name) values(2,'lisi');
  
  		#错误：不能重复
  		insert into t_vip(id,name) values(2,'wangwu');
  		ERROR 1062 (23000): Duplicate entry '2' for key 'PRIMARY'
  
  		#错误：不能为NULL
  		insert into t_vip(name) values('zhaoliu');
  		ERROR 1364 (HY000): Field 'id' doesn't have a default value
  ```

  

- 表级约束

  ```mysql
  	drop table if exists t_vip;
  		create table t_vip(
  			id int,
  			name varchar(255),
  			primary key(id)  # 表级约束
  		);
  		insert into t_vip(id,name) values(1,'zhangsan');
  
  		#错误
  		insert into t_vip(id,name) values(1,'lisi');
  		ERROR 1062 (23000): Duplicate entry '1' for key 'PRIMARY'
  	
  	  #表级约束主要是给多个字段联合起来添加约束
  		drop table if exists t_vip;
  		# id和name联合起来做主键：复合主键！！！！
  		create table t_vip(
  			id int,
  			name varchar(255),
  			email varchar(255),
  			primary key(id,name)	#复合主键
  		);
  		insert into t_vip(id,name,email) values(1,'zhangsan','zhangsan@123.com');
  		insert into t_vip(id,name,email) values(1,'lisi','lisi@123.com');
  
  		#错误：不能重复
  		insert into t_vip(id,name,email) values(1,'lisi','lisi@123.com');
  		ERROR 1062 (23000): Duplicate entry '1-lisi' for key 'PRIMARY'
  
  		#在实际开发中不建议使用：复合主键。建议使用单一主键！
  		#因为主键值存在的意义就是这行记录的身份证号，只要意义达到即可，单一主键可以做到。
  		#复合主键比较复杂，不建议使用！！！4.3使用主键打
  ```

### 4.3使用主键的注意事项

- 主键值建议使用：int，bigint，char等类型。

  ​	不建议使用：varchar来做主键。主键值一般都是数字，一般都是定长的！

- 主键除了单一主键和复合主键之外，还可以这样进行分类：
  		自然主键：主键值是一个自然数，和业务没关系。
    		业务主键：主键值和业务紧密关联，例如拿银行卡账号做主键值。这就是业务主键！

  ​	在实际开发中使用业务主键多，还是使用自然主键多一些:
  ​		自然主键使用比较多，因为主键只要做到不重复就行，不需要有意义。
  ​		业务主键不好，因为主键一旦和业务挂钩，那么当业务发生变动的时候，
  ​		可能会影响到主键值，所以业务主键不建议使用。**尽量使用自然主键**。

- 在mysql当中，有一种机制，可以帮助我们自动维护一个主键值：**使用auto_increment**

  ```mysql
  drop table if exists t_vip;
  		create table t_vip(
  			id int primary key auto_increment, #auto_increment表示自增，从1开始，以1递增！
  			name varchar(255)
  		);
  		insert into t_vip(name) values('zhangsan');
  		insert into t_vip(name) values('zhangsan');
  		insert into t_vip(name) values('zhangsan');
  		insert into t_vip(name) values('zhangsan');
  		insert into t_vip(name) values('zhangsan');
  		insert into t_vip(name) values('zhangsan');
  		insert into t_vip(name) values('zhangsan');
  		insert into t_vip(name) values('zhangsan');
  		select * from t_vip;
  
  		+----+----------+
  		| id | name     |
  		+----+----------+
  		|  1 | zhangsan |
  		|  2 | zhangsan |
  		|  3 | zhangsan |
  		|  4 | zhangsan |
  		|  5 | zhangsan |
  		|  6 | zhangsan |
  		|  7 | zhangsan |
  		|  8 | zhangsan |
  		+----+----------+
  ```

## 5、外键约束

### 5.1概念

> 外键约束：一种约束（foreign key）
> 外键字段：该字段上添加了外键约束
> 外键值：外键字段当中的每一个值。

需求：设计一张表，来描述学生与班级的信息。

```mysql
		t_class 班级表
		classno(pk)			classname
		------------------------------------------------------
		100					北京市大兴区亦庄镇第二中学高三1班
		101					北京市大兴区亦庄镇第二中学高三1班
	
		t_student 学生表
		no(pk)		name				cno(FK引用t_class这张表的classno)
		----------------------------------------------------------------
		1					jack				100
		2					lucy				100
		3					lilei				100
		4					hanmeimei		100
		5					zhangsan		101
		6					lisi				101
		7					wangwu			101
		8					zhaoliu			101

		当cno字段没有任何约束的时候，可能会导致数据无效。可能出现一个102，但是102班级不存在。
		所以为了保证cno字段中的值都是100和101，需要给cno字段添加外键约束。
		那么：cno字段就是外键字段。cno字段中的每一个值都是外键值。
```

### 5.2注意事项

​			t_class是父表
​			t_student是子表

```
		删除表的顺序：
			先删子，再删父。

		创建表的顺序：
			先创建父，再创建子。

		删除数据的顺序：
			先删子，再删父。

		插入数据的顺序：
			先插入父，再插入子。
```

- ​	思考：子表中的外键引用的父表中的某个字段，被引用的这个字段必须是主键吗？
  ​		不一定是主键，但至少具有unique约束。

- ​	测试：外键可以为NULL吗？
  ​		外键值可以为NULL。

## 6、事务

### 6.1概念

事务：就是批量的DML语句同时成功，或者同时失败。

本质：一个事务其实就是多条DML语句同时成功，或者同时失败。

事务对应的英语单词是：transaction。

### 6.2注意事项

- 只有DML语句才会有事务这一说，其它语句和事务无关！！！
  	insert、delete、update只有以上的三个语句和事务有关系，其它都没有关系。

- 因为 只有以上的三个语句是数据库表中数据进行增、删、改的。
  只要你的操作一旦涉及到数据的增、删、改，那么就一定要考虑安全问题，数据安全第一位！！！

### 6.3事务的执行

​	在事务的执行过程中，每一条DML的操作都会记录到“事务性活动的日志文件”中。
​	在事务的执行过程中，我们可以提交事务，也可以回滚事务。

	提交事务：
		commit语句。
		清空事务性活动的日志文件，将数据全部彻底持久化到数据库表中。
		提交事务标志着，事务的结束。并且是一种全部成功的结束。
	
	回滚事务：
		rollback语句。（回滚永远都是只能回滚到上一次的提交点！）
		将之前所有的DML操作全部撤销，并且清空事务性活动的日志文件
		回滚事务标志着，事务的结束。并且是一种全部失败的结束。

### 6.4测试

- mysql默认情况下是支持自动提交事务，即每执行一条DML语句，则提交一次。

- 这种自动提交实际上是不符合我们的开发习惯，因为一个业务通常是需要多条DML语句共同执行才能完成的，为了保证数据的安全，必须要求同时成功之后再提交，所以不能执行一条就提交一条。


- 必须将mysql的自动提交机制关闭掉：先执行start transaction。

演示测试：

```mysql
---------------------------------回滚事务----------------------------------------
		mysql> use bjpowernode;
		Database changed
		mysql> select * from dept_bak;
		Empty set (0.00 sec)

		mysql> start transaction;
		Query OK, 0 rows affected (0.00 sec)

		mysql> insert into dept_bak values(10,'abc', 'tj');
		Query OK, 1 row affected (0.00 sec)

		mysql> insert into dept_bak values(10,'abc', 'tj');
		Query OK, 1 row affected (0.00 sec)

		mysql> select * from dept_bak;
		+--------+-------+------+
		| DEPTNO | DNAME | LOC  |
		+--------+-------+------+
		|     10 | abc   | tj   |
		|     10 | abc   | tj   |
		+--------+-------+------+
		2 rows in set (0.00 sec)

		mysql> rollback;
		Query OK, 0 rows affected (0.00 sec)

		mysql> select * from dept_bak;
		Empty set (0.00 sec)


		---------------------------------提交事务----------------------------------------
		mysql> use bjpowernode;
		Database changed
		mysql> select * from dept_bak;
		+--------+-------+------+
		| DEPTNO | DNAME | LOC  |
		+--------+-------+------+
		|     10 | abc   | bj   |
		+--------+-------+------+
		1 row in set (0.00 sec)

		mysql> start transaction;
		Query OK, 0 rows affected (0.00 sec)

		mysql> insert into dept_bak values(20,'abc')
		Query OK, 1 row affected (0.00 sec)

		mysql> insert into dept_bak values(20,'abc')
		Query OK, 1 row affected (0.00 sec)

		mysql> insert into dept_bak values(20,'abc')
		Query OK, 1 row affected (0.00 sec)

		mysql> commit;
		Query OK, 0 rows affected (0.01 sec)

		mysql> select * from dept_bak;
		+--------+-------+------+
		| DEPTNO | DNAME | LOC  |
		+--------+-------+------+
		
		|     10 | abc   | bj   |
		|     20 | abc   | tj   |
		|     20 | abc   | tj   |
		|     20 | abc   | tj   |
		+--------+-------+------+
		4 rows in set (0.00 sec)

		mysql> rollback;
		Query OK, 0 rows affected (0.00 sec)

		mysql> select * from dept_bak;
		+--------+-------+------+
		| DEPTNO | DNAME | LOC  |
		+--------+-------+------+
		|     10 | abc   | bj   |
		|     20 | abc   | tj   |
		|     20 | abc   | tj   |
		|     20 | abc   | tj   |
		+--------+-------+------+
		4 rows in set (0.00 sec)
```

### 6.5事务的特性

- 原子性
- 一致性
- 隔离性（重点）
- 持久性

#### 6.5.1隔离性

- 事务与事务之间有隔离，例如A教室和B教室中间有一道墙，这道墙可以很厚，也可以很薄。这就是事务的隔离级别。这道墙越厚，表示隔离级别就越高。

- 隔离有4个级别

  1. 读未提交：read uncommitted（最低的隔离级别）

     ```
     即没有提交就读到了，事务A可以读取到事务B未提交的数据。
     这种隔离级别存在的问题就是：
     		脏读现象！(Dirty Read)
     		我们称读到了脏数据。
     		这种隔离级别一般都是理论上的，大多数的数据库隔离级别都是二档起步
     ```

  2.  读已提交：read committed《提交之后才能读到》

     ```
     事务A只能读取到事务B提交之后的数据，解决了脏读的现象。
     这种隔离级别存在的问题：
     	 不可重复读取数据。
     不可重复读取数据：
     	 在事务开启之后，第一次读到的数据是3条，当前事务还没有结束，可能第二次再读取的时候，读到的数据是4条，
     	 3不等于4，称为不可重复读取。
     这种隔离级别是比较真实的数据，每一次读到的数据是绝对的真实。
     oracle数据库默认的隔离级别是：read committed
     ```

  3.  可重复读：repeatable read《提交之后也读不到，永远读取的都是刚开启事务时的数据》

     ```
     可重复读取：
     	 事务A开启之后，不管是多久，每一次在事务A中读取到的数据都是一致的。即使事务B将数据已经修改，并且提交了，事务A
     	 读取到的数据还是没有发生改变，这就是可重复读。
     可重复读解决的问题：
     		解决了不可重复读取数据。
     可重复读存在的问题：
     		可以会出现幻影读。
     		每一次读取到的数据都是幻象。不够真实！
     		例如：早晨9点开始开启了事务，只要事务不结束，到晚上9点，读到的数据还是那样！
     		读到的是假象。不够绝对的真实。
     mysql中默认的事务隔离级别就是这个！！！！！！！！！！
     ```

  4. 序列化/串行化：serializable（最高的隔离级别）

     ```
     这是最高隔离级别，效率最低。解决了所有的问题。
     			这种隔离级别表示事务排队，不能并发！
     			synchronized，线程同步（事务同步）
     			每一次读取到的数据都是最真实的，并且效率是最低的。
     ```

#### 6.5.2验证各种隔离级别

- 查看隔离级别

  ```mysql
  SELECT @@tx_isolation
  +-----------------+
  | @@tx_isolation  |
  +-----------------+
  | REPEATABLE-READ |
  +-----------------+
  #mysql默认的隔离级别
  ```

  

- 测试mysql

  ```mysql
  #被测试的表t_user
  #验证：read uncommited
  mysql> set global transaction isolation level read uncommitted;
  事务A												事务B
  --------------------------------------------------------------------------------
  use bjpowernode;
  													use bjpowernode;
  start transaction;
  select * from t_user;
  													start transaction;
  													insert into t_user values('zhangsan');
  select * from t_user;
  
  
  
  
  #验证：read commited
  mysql> set global transaction isolation level read committed;
  事务A												事务B
  --------------------------------------------------------------------------------
  use bjpowernode;
  													use bjpowernode;
  start transaction;
  													start transaction;
  select * from t_user;
  													insert into t_user values('zhangsan');
  select * from t_user;
  													commit;
  select * from t_user;
  
  
  
  
  #验证：repeatable read
  mysql> set global transaction isolation level repeatable read;
  事务A												事务B
  --------------------------------------------------------------------------------
  use bjpowernode;
  													use bjpowernode;
  start transaction;
  													start transaction;
  select * from t_user;
  													insert into t_user values('lisi');
  													insert into t_user values('wangwu');
  													commit;
  select * from t_user;
  
  
  
  
  
  #验证：serializable
  mysql> set global transaction isolation level serializable;
  事务A												事务B
  --------------------------------------------------------------------------------
  use bjpowernode;
  													use bjpowernode;
  start transaction;
  													start transaction;
  select * from t_user;
  insert into t_user values('abc');
  													select * from t_user;
  
  ```

  