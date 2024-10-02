---
title: MySQL学习
date: 2023-01-04 10:24:42
tags: mysql

---

# 1. SQL分类 && 基础

<!-- more -->

## **SQL语句，根据其功能，主要分为四类：DDL、DML、DQL、DCL。**

## **1. DDL( Data Definition Language )**

**含义:** 数据定义语言，用来定义数据库对象(数据库，表， 字段)

### 1. 数据库操作

**查询所有数据库**

```mysql
show databases ;
```

 **查询当前数据库**

```mysql
select database() ;
```

 **创建数据库**

```mysql
create database [ if not exists ] 数据库名 [ default charset 字符集 ] [ collate 排序 规则 ] ;
```

**删除数据库**

```mysql
drop database [ if exists ] 数据库名 ;
# 如果删除一个不存在的数据库，将会报错。此时，可以加上参数 if exists ，如果数据库存在，再执行删除，否则不执行删除。
```

**切换数据库**

```mysql
use 数据库名 ;
# 我们要操作某一个数据库下的表时，就需要通过该指令，切换到对应的数据库下，否则是不能操作的。比如，切换到itcast数据，执行如下SQL：
use itcast;
```

**案例**

```mysql
# 1. 创建一个itcast数据库, 使用数据库默认的字符集。
create database itcast;
# 在同一个数据库服务器中，不能创建两个名称相同的数据库，否则将会报错。
# 可以通过if not exists 参数来解决这个问题，数据库不存在, 则创建该数据库，如果存在，则不创建。
create database if not extists itcast;

# 2. 创建一个itheima数据库，并且指定字符集
create database itheima default charset utf8mb4;
```

### 2. 表操作-查询创建

**查询当前数据库所有表**

```mysql
show tables;
# 比如,我们可以切换到sys这个系统数据库,并查看系统数据库中的所有表结构。
use sys;
show tables;
```

**查看指定表结构**

```mysql
 desc 表名 ;
 # 通过这条指令，我们可以查看到指定表的字段，字段的类型、是否可以为NULL，是否存在默认值等信息
```

**查询指定表的建表语句**

```mysql
show create table 表名 ;
# 通过这条指令，主要是用来查看建表语句的，而有部分参数我们在创建表的时候，并未指定也会查询到，因为这部分是数据库的默认值，如：存储引擎、字符集等。
```

**创建表结构**

```mysql
CREATE TABLE 表名(
    字段1 字段1类型 [ COMMENT 字段1注释 ],
    字段2 字段2类型 [COMMENT 字段2注释 ],
    字段3 字段3类型 [COMMENT 字段3注释 ],
    ......
    字段n 字段n类型 [COMMENT 字段n注释 ]
) [ COMMENT 表注释 ] ;
# 注意: [...] 内为可选参数，最后一个字段后面没有逗号
# 这里比如我们要创建一个tb_user表
create table tb_user(
    id int comment '编号',
    name varchar(50) comment '姓名',
    age int comment '年龄',
    gender varchar(1) comment '性别'
) comment '用户表';
```

### 3. 表操作-数据类型

**数值类型**

|    类型     |  大小  |                     有符号(SIGNED)                     |                      无符号(UNSIGNED)                      |         范围          |
| :---------: | :----: | :----------------------------------------------------: | :--------------------------------------------------------: | :-------------------: |
|   TINYINT   | 1byte  |                      (-128，127)                       |                          (0，255)                          |       小整数值        |
|  SMALLINT   | 2bytes |                    (-32768，32767)                     |                         (0，65535)                         |       大整数值        |
|  MEDIUMINT  | 3bytes |                  (-8388608，8388607)                   |                       (0，16777215)                        |       大整数值        |
| INT/INTEGER | 4bytes |               (-2147483648， 2147483647)               |                      (0，4294967295)                       |       大整数值        |
|   BIGINT    | 8bytes |                    (-2^63，2^63-1)                     |                        (0，2^64-1)                         |      极大整数值       |
|    FLOAT    | 4bytes |       (-3.402823466 E+38， 3.402823466351 E+38)        |          0 和 (1.175494351 E38，3.402823466 E+38)          |    单精度浮点数值     |
|   DOUBLE    | 8bytes | (-1.7976931348623157 E+308， 1.7976931348623157 E+308) | 0 和 (2.2250738585072014 E-308， 1.7976931348623157 E+308) |    双精度浮点数值     |
|   DECIMAL   |        |              依赖于M(精度)和D(标度) 的值               |                依赖于M(精度)和D(标度)的 值                 | 小数 值(精 确定 点数) |

```mysql
# 说明
# 1). 年龄字段 -- 不会出现负数, 而且人的年龄不会太大
	age tinyint unsigned
# 2). 分数 -- 总分100分, 最多出现一位小数
	score double(4,1)
```

**字符串类型**

|    类型    |         大小          |             描述             |
| :--------: | :-------------------: | :--------------------------: |
|    CHAR    |      0-255 bytes      |   定长字符串(需要指定长度)   |
|  VARCHAR   |     0-65535 bytes     |   变长字符串(需要指定长度)   |
|  TINYBLOB  |      0-255 bytes      | 不超过255个字符的二进制数据  |
|  TINYTEXT  |      0-255 bytes      |         短文本字符串         |
|    BLOB    |    0-65 535 bytes     |    二进制形式的长文本数据    |
|    TEXT    |    0-65 535 bytes     |          长文本数据          |
| MEDIUMBLOB |  0-16 777 215 bytes   | 二进制形式的中等长度文本数据 |
| MEDIUMTEXT |  0-16 777 215 bytes   |       中等长度文本数据       |
|  LONGBLOB  | 0-4 294 967 295 bytes |   二进制形式的极大文本数据   |
|  LONGTEXT  | 0-4 294 967 295 bytes |         极大文本数据         |

```mysql
# char 与 varchar 都可以描述字符串，char是定长字符串，指定长度多长，就占用多少个字符，和字段值的长度无关 。而varchar是变长字符串，指定的长度为最大占用长度 。相对来说，char的性能会更高些。
# 如：
# 1). 用户名 username ------> 长度不定, 最长不会超过50
	username varchar(50)
# 2). 性别 gender ---------> 存储值, 不是男,就是女
	gender char(1)
# 3). 手机号 phone --------> 固定长度为11
	phone char(11)
```

**日期时间类型**

|   类型    | 大小 |                    范围                    |        格式         |           描述            |
| :-------: | :--: | :----------------------------------------: | :-----------------: | :-----------------------: |
|   DATE    |  3   |          1000-01-01 至 9999-12-31          |     YYYY-MM-DD      |          日期值           |
|   TIME    |  3   |          -838:59:59 至 838:59:59           |      HH:MM:SS       |     时间值或持续 时间     |
|   YEAR    |  1   |                1901 至 2155                |        YYYY         |          年份值           |
| DATETIME  |  8   | 1000-01-01 00:00:00 至 9999-12-31 23:59:59 | YYYY-MM-DD HH:MM:SS |     混合日期和时 间值     |
| TIMESTAMP |  4   | 1970-01-01 00:00:01 至 2038-01-19 03:14:07 | YYYY-MM-DD HH:MM:SS | 混合日期和时 间值，时间戳 |

```mysql
# 如:
# 1). 生日字段 birthday
	birthday date
# 2). 创建时间 createtime
	createtime datetime
```

**案例**

```mysql
# 设计一张员工信息表，要求如下：
# 1. 编号（纯数字）
# 2. 员工工号 (字符串类型，长度不超过10位)
# 3. 员工姓名（字符串类型，长度不超过10位）
# 4. 性别（男/女，存储一个汉字）
# 5. 年龄（正常人年龄，不可能存储负数）
# 6. 身份证号（二代身份证号均为18位，身份证中有X这样的字符）
# 7. 入职时间（取值年月日即可）
# 对应的建表语句如下:
create table emp(
    id int comment '编号',
    workno varchar(10) comment '工号',
    name varchar(10) comment '姓名',
    gender char(1) comment '性别',
    age tinyint unsigned comment '年龄',
    idcard char(18) comment '身份证号',
    entrydate date comment '入职时间'
) comment '员工表';
```

### 4. 表操作-修改

 **添加字段**

```mysql
ALTER TABLE 表名 ADD 字段名 类型 (长度) [ COMMENT 注释 ] [ 约束 ];
# 案例:
# 为emp表增加一个新的字段”昵称”为nickname，类型为varchar(20)
ALTER TABLE emp ADD nickname varchar(20) COMMENT '昵称';
```

**修改数据类型**

```mysql
ALTER TABLE 表名 MODIFY 字段名 新数据类型 (长度);
```

**修改字段名和字段类型**

```mysql
ALTER TABLE 表名 CHANGE 旧字段名 新字段名 类型 (长度) [ COMMENT 注释 ] [ 约束 ];
 # 案例:
 # 将emp表的nickname字段修改为username，类型为varchar(30)
ALTER TABLE emp CHANGE nickname username varchar(30) COMMENT '昵称';
```

**删除字段**

```mysql
ALTER TABLE 表名 DROP 字段名;
# 案例:
# 将emp表的字段username删除
ALTER TABLE emp DROP username;
```

 **修改表名**

```mysql
ALTER TABLE 表名 RENAME TO 新表名;
# 案例:
# 将emp表的表名修改为 employee
ALTER TABLE emp RENAME TO employee;
```

### 5. 表操作-删除

**删除表**

```mysql
DROP TABLE [ IF EXISTS ] 表名;
# 可选项 IF EXISTS 代表，只有表名存在时才会删除该表，表名不存在，则不执行删除操作(如果不加该参数项，删除一张不存在的表，执行将会报错)。
# 案例:
# 如果tb_user表存在，则删除tb_user表
DROP TABLE IF EXISTS tb_user;
# 删除指定表, 并重新创建表
TRUNCATE TABLE 表名;
# 注意: 在删除表的时候，表中的全部数据也都会被删除。
```



## 2. DML (Data Manipulation Language)

**含义:** 数据操作语言，用来对数据库表中的数据进行增删改

### 1. 添加数据

 **给指定字段添加数据**

```mysql
INSERT INTO 表名 (字段名1, 字段名2, ...) VALUES (值1, 值2, ...);
# 案例: 给employee表所有的字段添加数据
insert into employee(id,workno,name,gender,age,idcard,entrydate) values(1,'1','Itcast','男',10,'123456789012345678','2000-01-01');
# 插入数据完成之后，我们有两种方式，查询数据库的数据：
select * from employee;
insert into employee(id,workno,name,gender,age,idcard,entrydate) values(1,'1','Itcast','男',3,'123456789012345678','2000-01-01');
```

**给全部字段添加数据**

```mysql
INSERT INTO 表名 VALUES (值1, 值2, ...);
# 案例：插入数据到employee表，具体的SQL如下
insert into employee values(2,'2','张无忌','男',18,'123456789012345670','2005-01-01');
```

**批量添加数据**

```mysql
INSERT INTO 表名 (字段名1, 字段名2, ...) VALUES (值1, 值2, ...), (值1, 值2, ...), (值1, 值2, ...) ;
INSERT INTO 表名 VALUES (值1, 值2, ...), (值1, 值2, ...), (值1, 值2, ...) ;
# 案例：批量插入数据到employee表，具体的SQL如下：
insert into employee values(3,'3','韦一笑','男',38,'123456789012345670','2005-01-01'),(4,'4','赵敏','女',18,'123456789012345670','2005-01-01');
# 注意事项:
#     • 插入数据时，指定的字段顺序需要与值的顺序是一一对应的。
#     • 字符串和日期型数据应该包含在引号中。
#     • 插入的数据大小，应该在字段的规定范围内。
```

### **2. 修改数据**

```mysql
UPDATE 表名 SET 字段名1 = 值1 , 字段名2 = 值2 , .... [ WHERE 条件 ] ;
# 案例:
# A. 修改id为1的数据，将name修改为itheima
update employee set name = 'itheima' where id = 1;
# B. 修改id为1的数据, 将name修改为小昭, gender修改为 女
update employee set name = '小昭' , gender = '女' where id = 1;
# C. 将所有的员工入职日期修改为 2008-01-01
update employee set entrydate = '2008-01-01';
# 注意事项:
# 修改语句的条件可以有，也可以没有，如果没有条件，则会修改整张表的所有数据
```

###  3. 删除数据

```mysql
DELETE FROM 表名 [ WHERE 条件 ] ;
# 案例:
# A. 删除gender为女的员工
delete from employee where gender = '女';
# B. 删除所有员工
delete from employee;
# 注意事项:
# 	• DELETE 语句的条件可以有，也可以没有，如果没有条件，则会删除整张表的所有数据。
# 	• DELETE 语句不能删除某一个字段的值(可以使用UPDATE，将该字段值置为NULL即可)。
# 	• 当进行删除全部数据操作时，datagrip会提示我们，询问是否确认删除，我们直接点击Execute即可。
```

## **3. DQL (Data Query Language)**

​	数据查询语言，用来查询数据库中表的记录

**数据准备工作:**

```mysql
drop table if exists employee;
create table emp(
    id int comment '编号',
    workno varchar(10) comment '工号',
    name varchar(10) comment '姓名',
    gender char(1) comment '性别',
    age tinyint unsigned comment '年龄',
    idcard char(18) comment '身份证号',
    workaddress varchar(50) comment '工作地址',
    entrydate date comment '入职时间'
)comment '员工表';

INSERT INTO emp (id, workno, name, gender, age, idcard, workaddress, entrydate)VALUES (1, '00001', '柳岩666', '女', 20, '123456789012345678', '北京', '2000-01-01');
INSERT INTO emp (id, workno, name, gender, age, idcard, workaddress, entrydate)VALUES (2, '00002', '张无忌', '男', 18, '123456789012345670', '北京', '2005-09-01');
INSERT INTO emp (id, workno, name, gender, age, idcard, workaddress, entrydate)VALUES (3, '00003', '韦一笑', '男', 38, '123456789712345670', '上海', '2005-08-01');
INSERT INTO emp (id, workno, name, gender, age, idcard, workaddress, entrydate)VALUES (4, '00004', '赵敏', '女', 18, '123456757123845670', '北京', '2009-12-01');
INSERT INTO emp (id, workno, name, gender, age, idcard, workaddress, entrydate)VALUES (5, '00005', '小昭', '女', 16, '123456769012345678', '上海', '2007-07-01');
INSERT INTO emp (id, workno, name, gender, age, idcard, workaddress, entrydate)VALUES (6, '00006', '杨逍', '男', 28, '12345678931234567X', '北京', '2006-01-01');
INSERT INTO emp (id, workno, name, gender, age, idcard, workaddress, entrydate)VALUES (7, '00007', '范瑶', '男', 40, '123456789212345670', '北京', '2005-05-01');
INSERT INTO emp (id, workno, name, gender, age, idcard, workaddress, entrydate)VALUES (8, '00008', '黛绮丝', '女', 38, '123456157123645670', '天津', '2015-05-01');
INSERT INTO emp (id, workno, name, gender, age, idcard, workaddress, entrydate)VALUES (9, '00009', '范凉凉', '女', 45, '123156789012345678', '北京', '2010-04-01');
INSERT INTO emp (id, workno, name, gender, age, idcard, workaddress, entrydate)VALUES (10, '00010', '陈友谅', '男', 53, '123456789012345670', '上海', '2011-01-01');
INSERT INTO emp (id, workno, name, gender, age, idcard, workaddress, entrydate)VALUES (11, '00011', '张士诚', '男', 55, '123567897123465670', '江苏', '2015-05-01');
INSERT INTO emp (id, workno, name, gender, age, idcard, workaddress, entrydate)VALUES (12, '00012', '常遇春', '男', 32, '123446757152345670', '北京', '2004-02-01');
INSERT INTO emp (id, workno, name, gender, age, idcard, workaddress, entrydate)VALUES (13, '00013', '张三丰', '男', 88, '123656789012345678', '江苏', '2020-11-01');
INSERT INTO emp (id, workno, name, gender, age, idcard, workaddress, entrydate)VALUES (14, '00014', '灭绝', '女', 65, '123456719012345670', '西安', '2019-05-01');
INSERT INTO emp (id, workno, name, gender, age, idcard, workaddress, entrydate)VALUES (15, '00015', '胡青牛', '男', 70, '12345674971234567X', '西安', '2018-04-01');
INSERT INTO emp (id, workno, name, gender, age, idcard, workaddress, entrydate)VALUES (16, '00016', '周芷若', '女', 18, null, '北京', '2012-06-01');
```

### 1. 基础语法

```mysql
SELECT
	字段列表
FROM
	表名列表
WHERE
	条件列表
GROUP BY
	分组字段列表
HAVING
	分组后条件列表
ORDER BY
	排序字段列表
LIMIT
	分页参数
```

### 2. 基础查询

**查询多个字段**

```mysql
SELECT 字段1, 字段2, 字段3 ... FROM 表名 ;
SELECT * FROM 表名 ;
# 注意 : * 号代表查询所有字段，在实际开发中尽量少用（不直观、影响效率）。
```

**字段设置别名**

```mysql
SELECT 字段1 [ AS 别名1 ] , 字段2 [ AS 别名2 ] ... FROM 表名;
SELECT 字段1 [ 别名1 ] , 字段2 [ 别名2 ] ... FROM 表名;
```

**去除重复记录**

```mysql
SELECT DISTINCT 字段列表 FROM 表名;
```

**案例**

```mysql
# A. 查询指定字段 name, workno, age并返回
select name,workno,age from emp;

# B. 查询返回所有字段
select id ,workno,name,gender,age,idcard,workaddress,entrydate from emp;
select * from emp;

# C. 查询所有员工的工作地址,起别名
select workaddress as '工作地址' from emp;
-- as可以省略
select workaddress '工作地址' from emp;

# D. 查询公司员工的上班地址有哪些(不要重复)
select distinct workaddress '工作地址' from emp;
```

### 3. 条件查询

**语法**

```mysql
SELECT 字段列表 FROM 表名 WHERE 条件列表 ;
```

**条件**



| 常见运算符          |                                          |
| ------------------- | ---------------------------------------- |
| 比较运算符          | 功能                                     |
| >                   | 大于                                     |
| >=                  | 大于等于                                 |
| <                   | 小于                                     |
| <=                  | 小于等于                                 |
| =                   | 等于                                     |
| <> 或 !=            | 不等于                                   |
| BETWEEN ... AND ... | 在某个范围之内(含最小、最大值)           |
| IN(...)             | 在in之后的列表中的值，多选一             |
| LIKE 占位符         | 模糊匹配(_匹配单个字符, %匹配任意个字符) |
| IS NULL             | 是NULL                                   |

| 逻辑运算符 | 功能                        |
| ---------- | --------------------------- |
| AND 或 &&  | 并且 (多个条件同时成立)     |
| OR 或 \|\| | 或者 (多个条件任意一个成立) |
| NOT 或 !   | 非 , 不是                   |

**案例**

```mysql
# A. 查询年龄等于 88 的员工
select * from emp where age = 88;
# B. 查询年龄小于 20 的员工信息
select * from emp where age < 20;
# C. 查询年龄小于等于 20 的员工信息
select * from emp where age <= 20;
# D. 查询没有身份证号的员工信息
select * from emp where idcard is null;
# E. 查询有身份证号的员工信息
select * from emp where idcard is not null;
# F. 查询年龄不等于 88 的员工信息
select * from emp where age != 88;
select * from emp where age <> 88;
# G. 查询年龄在15岁(包含) 到 20岁(包含)之间的员工信息
select * from emp where age >= 15 && age <= 20;
select * from emp where age >= 15 and age <= 20;
select * from emp where age between 15 and 20;
# H. 查询性别为 女 且年龄小于 25岁的员工信息
select * from emp where gender = '女' and age < 25;
# I. 查询年龄等于18 或 20 或 40 的员工信息
select * from emp where age = 18 or age = 20 or age =40;
select * from emp where age in(18,20,40);
# J. 查询姓名为两个字的员工信息 _ %
select * from emp where name like '__';
# K. 查询身份证号最后一位是X的员工信息
select * from emp where idcard like '%X';
select * from emp where idcard like '_________________X';
```

### 4. 聚合函数

**定义:**  将一列数据作为一个整体，进行纵向计算 。

**常见聚合函数**

| 函数  | 功能     |
| ----- | -------- |
| count | 统计数量 |
| max   | 最大值   |
| min   | 最小值   |
| avg   | 平均值   |
| sum   | 求和     |

**语法**

```mysql
SELECT 聚合函数(字段列表) FROM 表名 ;
注意 : NULL值是不参与所有聚合函数运算的。
```

**案例**

```mysql
# A. 统计该企业员工数量
select count(*) from emp; -- 统计的是总记录数
select count(idcard) from emp; -- 统计的是idcard字段不为null的记录数
# 对于count聚合函数，统计符合条件的总记录数，还可以通过 count(数字/字符串)的形式进行统计查询，比如
select count(1) from emp;

# B. 统计该企业员工的平均年龄
select avg(age) from emp;

#C. 统计该企业员工的最大年龄
select max(age) from emp;

# D. 统计该企业员工的最小年龄
select min(age) from emp;

# E. 统计西安地区员工的年龄之和
select sum(age) from emp where workaddress = '西安';
```

### 5. 分组查询

**语法**

```mysql
SELECT 字段列表 FROM 表名 [ WHERE 条件 ] GROUP BY 分组字段名 [ HAVING 分组后过滤条件 ];

```

**where和having的区别**

- 执行时机不同：where是分组之前进行过滤，不满足where条件，不参与分组；而having是分组 之后对结果进行过滤。
- 判断条件不同：where不能对聚合函数进行判断，而having可以。

**注意事项**

1. 分组之后，查询的字段一般为聚合函数和分组字段，查询其他字段无任何意义。
2. 执行顺序: where > 聚合函数 > having 。 
3. 支持多字段分组, 具体语法为 : group by columnA,columnB

**案例**

```mysql
# A. 根据性别分组 , 统计男性员工 和 女性员工的数量
select gender, count(*) from emp group by gender ;
# B. 根据性别分组 , 统计男性员工 和 女性员工的平均年龄
select gender, avg(age) from emp group by gender ;
# C. 查询年龄小于45的员工 , 并根据工作地址分组 , 获取员工数量大于等于3的工作地址
select workaddress, count(*) address_count from emp where age < 45 group byworkaddress having address_count >= 3;
# D. 统计各个工作地址上班的男性及女性员工的数量
select workaddress, gender, count(*) '数量' from emp group by gender , workaddress;
```

### 6. 排序查询

**语法**

```mysql
SELECT 字段列表 FROM 表名 ORDER BY 字段1 排序方式1 , 字段2 排序方式2 ;
```

 **排序方式:**   ASC : 升序(默认值) DESC: 降序

**注意事项:**

1.  如果是升序, 可以不指定排序方式ASC 
2.  如果是多字段排序，当第一个字段值相同时，才会根据第二个字段进行排序 

**案例**

```mysql
# A. 根据年龄对公司的员工进行升序排序
select * from emp order by age asc;
select * from emp order by age;
# B. 根据入职时间, 对员工进行降序排序
select * from emp order by entrydate desc;
# C. 根据年龄对公司的员工进行升序排序 , 年龄相同 , 再按照入职时间进行降序排序
select * from emp order by age asc , entrydate desc;
```

### 7. 分页查询

**语法**

```mysql
SELECT 字段列表 FROM 表名 LIMIT 起始索引, 查询记录数 ;
```

**注意事项:** 

1. 起始索引从0开始，起始索引 = （查询页码 - 1）* 每页显示记录数。
2. 分页查询是数据库的方言，不同的数据库有不同的实现，MySQL中是LIMIT。
3. 如果查询的是第一页数据，起始索引可以省略，直接简写为 limit 10。

**案例**

```mysql
# A. 查询第1页员工数据, 每页展示10条记录
select * from emp limit 0,10;
select * from emp limit 10;
# B. 查询第2页员工数据, 每页展示10条记录 --------> (页码-1)*页展示记录数
select * from emp limit 10,10;
```

### 8. 案例

```mysql
# 1). 查询年龄为20,21,22,23岁的员工信息。
select * from emp where gender = '女' and age in(20,21,22,23);
# 2). 查询性别为 男 ，并且年龄在 20-40 岁(含)以内的姓名为三个字的员工。
select * from emp where gender = '男' and ( age between 20 and 40 ) and name like'___';
# 3). 统计员工表中, 年龄小于60岁的 , 男性员工和女性员工的人数。
select gender, count(*) from emp where age < 60 group by gender;
# 4). 查询所有年龄小于等于35岁员工的姓名和年龄，并对查询结果按年龄升序排序，如果年龄相同按入职时间降序排序。
select name , age from emp where age <= 35 order by age asc , entrydate desc;
# 5). 查询性别为男，且年龄在20-40 岁(含)以内的前5个员工信息，对查询的结果按年龄升序排序，年龄相同按入职时间升序排序。
select * from emp where gender = '男' and age between 20 and 40 order by age asc ,entrydate asc limit 5 ;
```

### 9. 执行顺序

```mysql
# 查询年龄大于15的员工姓名、年龄，并根据年龄进行升序排序。
select name , age from emp where age > 15 order by age asc;
# 在查询时，我们给emp表起一个别名 e，然后在select 及 where中使用该别名。
select e.name , e.age from emp e where e.age > 15 order by age asc;

执行上述SQL语句后，我们看到依然可以正常的查询到结果，此时就说明： from 先执行, 然后where 和 select 执行。那 where 和 select 到底哪个先执行呢?此时，此时我们可以给select后面的字段起别名，然后在 where 中使用这个别名，然后看看是否可以执行成功。

select e.name ename , e.age eage from emp e where eage > 15 order by age asc;
执行上述SQL报错了:由此我们可以得出结论: from 先执行，然后执行 where ， 再执行select 。
接下来，我们再执行如下SQL语句，查看执行效果：

select e.name ename , e.age eage from emp e where e.age > 15 order by eage asc;
结果执行成功。 那么也就验证了: order by 是在select 语句之后执行的。
```

**综上所述，我们可以看到DQL语句的执行顺序为： from ... where ... group by ... having ... select ... order by ... limit ...**

## **4. DCL (Data Control Language)**

​	**定义:**  数据控制语言，用来创建数据库用户、控制数据库的 访问权限.

### 1.  管理用户

**查询用户**

```mysql
select * from mysql.user;
# 其中 Host代表当前用户访问的主机, 如果为localhost, 仅代表只能够在当前本机访问，是不可以远程访问的。 User代表的是访问该数据库的用户名。在MySQL中需要通过Host和User来唯一标识一个用户。
```

**创建用户**

```mysql
CREATE USER '用户名'@'主机名' IDENTIFIED BY '密码';
```

**修改用户密码**

```mysql
ALTER USER '用户名'@'主机名' IDENTIFIED WITH mysql_native_password BY '新密码' ;
```

**删除用户**

```mysql
DROP USER '用户名'@'主机名' ;
```

**注意事项**

-  在MySQL中需要通过用户名@主机名的方式，来唯一标识一个用户。
-  主机名可以使用 % 通配。
-  这类SQL开发人员操作的比较少，主要是DBA（ Database Administrator 数据库 管理员）使用。

**案例**

```mysql
# A. 创建用户itcast, 只能够在当前主机localhost访问, 密码123456;
create user 'itcast'@'localhost' identified by '123456';
# B. 创建用户heima, 可以在任意主机访问该数据库, 密码123456;
create user 'heima'@'%' identified by '123456';
# C. 修改用户heima的访问密码为1234;
alter user 'heima'@'%' identified with mysql_native_password by '1234';
# D. 删除 itcast@localhost 用户
drop user 'itcast'@'localhost'; 
```

### 2. 权限控制

| 权限                | 说明               |
| ------------------- | ------------------ |
| ALL, ALL PRIVILEGES | 所有权限           |
| SELECT              | 查询数据           |
| INSERT              | 插入数据           |
| UPDATE              | 修改数据           |
| DELETE              | 删除数据           |
| ALTER               | 修改表             |
| DROP                | 删除数据库/表/视图 |
| CREATE              | 创建数据库/表      |

```mysql
# 1). 查询权限
SHOW GRANTS FOR '用户名'@'主机名' ;
# 2). 授予权限
GRANT 权限列表 ON 数据库名.表名 TO '用户名'@'主机名';
# 3). 撤销权限
REVOKE 权限列表 ON 数据库名.表名 FROM '用户名'@'主机名';
```

**注意事项：**

- 多个权限之间，使用逗号分隔
- 授权时， 数据库名和表名可以使用 * 进行通配，代表所有。

**案例**

```mysql
# A. 查询 'heima'@'%' 用户的权限
show grants for 'heima'@'%';
# B. 授予 'heima'@'%' 用户itcast数据库所有表的所有操作权限
grant all on itcast.* to 'heima'@'%';
# C. 撤销 'heima'@'%' 用户的itcast数据库的所有权限
revoke all on itcast.* from 'heima'@'%';
```

# 2. 函数

## 1. 字符串函数

| 函数                     | 功能                                                       |
| ------------------------ | ---------------------------------------------------------- |
| CONCAT(S1,S2,...Sn)      | 字符串拼接，将S1，S2，... Sn拼接成一个字符串               |
| LOWER(str)               | 将字符串str全部转为小写                                    |
| UPPER(str)               | 将字符串str全部转为大写                                    |
| LPAD(str,n,pad)          | 左填充，用字符串pad对str的左边进行填充，达到n个字符 串长度 |
| RPAD(str,n,pad)          | 右填充，用字符串pad对str的右边进行填充，达到n个字符 串长度 |
| TRIM(str)                | 去掉字符串头部和尾部的空格                                 |
| SUBSTRING(str,start,len) | 返回从字符串str从start位置起的len个长度的字符串            |

**案例**

```mysql
演示如下：
A. concat : 字符串拼接
select concat('Hello' , ' MySQL');

B. lower : 全部转小写
select lower('Hello');

C. upper : 全部转大写
select upper('Hello');

D. lpad : 左填充  --->  在左边填充, 最后长度是5, 填充 -
select lpad('01', 5, '-'); 
select lpad('Hello', 3, '-');  # Hel

E. rpad : 右填充  --->  在右边填充, 最后长度是5, 填充 -
select rpad('01', 5, '-');

F. trim : 去除首尾空格
select trim(' Hello MySQL ');

G. substring : 截取子字符串   --->  Hello
select substring('Hello MySQL',1,5);

这段代码可以将emp表中 workno 这列的数字 左边补0, 直到一共5位数字
update emp set workno = lpad(workno, 5, '0');
```

## 2. 数值函数

| 函数       | 功能                               |
| ---------- | ---------------------------------- |
| CEIL(x)    | 向上取整                           |
| FLOOR(x)   | 向下取整                           |
| MOD(x,y)   | 返回x/y的模                        |
| RAND()     | 返回0~1内的随机数                  |
| ROUND(x,y) | 求参数x的四舍五入的值，保留y位小数 |

**案例**

```mysql
A. ceil：向上取整
select ceil(1.1);

B. floor：向下取整
select floor(1.9);

C. mod：取模
select mod(7,4);

D. rand：获取随机数
select rand();

E. round：四舍五入
select round(2.344,2);

案例： 通过数据库的函数，生成一个六位数的随机验证码。
思路： 获取随机数可以通过rand()函数，但是获取出来的随机数是在0-1之间的，所以可以在其基础上乘以1000000，然后舍弃小数部分，如果长度不足6位，补0
select lpad(round(rand()*1000000 , 0), 6, '0');
```

## 3. 日期函数

| 函数                              | 功能                                              |
| --------------------------------- | ------------------------------------------------- |
| CURDATE()                         | 返回当前日期                                      |
| CURTIME()                         | 返回当前时间                                      |
| NOW()                             | 返回当前日期和时间                                |
| YEAR(date)                        | 获取指定date的年份                                |
| MONTH(date)                       | 获取指定date的月份                                |
| DAY(date)                         | 获取指定date的日期                                |
| DATE_ADD(date, INTERVAL exprtype) | 返回一个日期/时间值加上一个时间间隔expr后的时间值 |
| DATEDIFF(date1,date2)             | 返回起始时间date1 和 结束时间date2之间的天数      |

**案例**

```mysql
A. curdate：当前日期
select curdate();

B. curtime：当前时间
select curtime();

C. now：当前日期和时间
select now();

D. YEAR , MONTH , DAY：当前年、月、日
select YEAR(now());
select MONTH(now());
select DAY(now());

E. date_add：增加指定的时间间隔
select date_add(now(), INTERVAL 70 YEAR );

F. datediff：获取两个日期相差的天数
select datediff('2021-10-01', '2021-12-01');

案例： 查询所有员工的入职天数，并根据入职天数倒序排序。
思路： 入职天数，就是通过当前日期 - 入职日期，所以需要使用datediff函数来完成。
select name, datediff(curdate(), entrydate) as 'entrydays' from emp order by entrydays desc;
```

## 4. 流程函数

| 函数                                                         | 功能                                                       |
| ------------------------------------------------------------ | ---------------------------------------------------------- |
| IF(value , t , f)                                            | 如果value为true，则返回t，否则返回 f                       |
| IFNULL(value1 , value2)                                      | 如果value1不为空，返回value1，否则 返回value2              |
| CASE WHEN [ val1 ] THEN [res1] ... ELSE [ default ] END      | 如果val1为true，返回res1，... 否 则返回default默认值       |
| CASE [ expr ] WHEN [ val1 ] THEN [res1] ... ELSE [ default ] END | 如果expr的值等于val1，返回 res1，... 否则返回default默认值 |

**案例**

```mysql
A. if
select if(false, 'Ok', 'Error');  Error

B. ifnull
select ifnull('Ok','Default'); OK
select ifnull('','Default');  ''
select ifnull(null,'Default'); Default

C. CASE [ expr ] WHEN [ val1 ] THEN [res1] ... ELSE [ default ] END
需求: 查询emp表的员工姓名和工作地址 (北京/上海 ----> 一线城市 , 其他 ----> 二线城市)
select 
	name,
	(case workaddress when '北京' then '一线城市' when '上海' then '一线城市' else '二线城市' end) as '工作地址' 
from emp;

D. case WHEN [ val1 ] THEN [res1] ... ELSE [ default ] END
select 
	id, 
	name, 
	(case when math >= 85 then '优秀' when math >= 60 then '及格' else '不及格' end) '数学',
	(case when english >= 85 then '优秀' when english >= 60 then '及格' else '不及格' end) '英语' 
from score;
```

## 5. JSON函数

### 1. 各函数表格

| 分类         | 函数               | 描述                                                         |
| ------------ | ------------------ | ------------------------------------------------------------ |
| 创建json     | json_array         | 创建json数组                                                 |
|              | json_object        | 创建json对象                                                 |
|              | json_quote         | 将json转成json字符串类型                                     |
| 查询json     | json_contains      | 判断是否包含某个json值                                       |
|              | json_contains_path | 判断某个路径下是否包含sjon值                                 |
|              | json_extract       | 提取json值                                                   |
|              | column->path       | json_extract的简介写法,  MySQL5.7.9开始支持                  |
|              | column->>path      | json_unquote(column->path)的简介写法                         |
|              | json_keys          | 提取json中的键值为json数组                                   |
|              | json_search        | 按给定字符串关键字搜索json, 返回匹配的路径                   |
| 修改json     | json_append        | 废弃, MySQL 5.7.9 开始改名为 json_array_append               |
|              | json_array_append  | 末尾添加数组元素, 如果原有值是数值或json对象, 则转成数组后, 再添加元素 |
|              | json_array_insert  | 插入数组元素                                                 |
|              | json_insert        | 插入值(插入新值, 但不替换已经存在的旧值)                     |
|              | json_merge         | 合并json数组或对象                                           |
|              | json_remove        | 删除json数据                                                 |
|              | json_replace       | 替换值(只替换已经存在的旧值)                                 |
|              | json_set           | 设置值(替换旧值, 并插入不存在的新值)                         |
|              | json_unquote       | 去除json字符串的引号, 将值转成string类型                     |
| 返回json属性 | json_depth         | 返回json文档的最大深度                                       |
|              | json_length        | 返回json文档的长度                                           |
|              | json_type          | 返回json值的类型                                             |
|              | json_valid         | 判断是否为合法json文档                                       |

### 2. 概述

​	MySQL里的json分为json array和json object。表示整个json对象，在索引数据时用**下标**(对于json array，从0开始)或**键值**(对于json object，含有特殊字符的key要用"括起来，比如.“my name”)。

```js
例如：[3, {“a”: [5, 6], “b”: 10}, [99, 100]]，那么：
$[0]：3
$[1]： {“a”: [5, 6], “b”: 10}
$[2] ：[99, 100]
$[3] ： NULL
$[1].a：[5, 6]
$[1].a[1]：6
$[1].b：10
$[2][0]：99
```

### 3. 比较规则

json中的数据可以用 =, <, <=, >, >=, <>, !=, and <=> 进行比较。但json里的数据类型可以是多样的，那么在不同类型之间进行比较时，就有优先级了，**高优先级的要大于低优先级的**（可以用JSON_TYPE()函数查看类型）。优先级从高到低如下：

```js
BLOB
BIT
OPAQUE
DATETIME
TIME
DATE
BOOLEAN
ARRAY
OBJECT
STRING
INTEGER, DOUBLE
NULL
```

### 4. 常用函数

#### 1. 创建函数

**JSON_ARRAY**

```mysql
# JSON_ARRAY(val1,val2,val3…)
# 生成一个包含指定元素的json数组。
mysql> SELECT JSON_ARRAY(1, "abc", NULL, TRUE, CURTIME());
+---------------------------------------------+
| JSON_ARRAY(1, "abc", NULL, TRUE, CURTIME()) |
+---------------------------------------------+
| [1, "abc", null, true, "11:30:24.000000"]   |
+---------------------------------------------+
```

**JSON_OBJECT**

```mysql
# SON_OBJECT(key1,val1,key2,val2…)
# 生成一个包含指定K-V对的json object。如果有key为NULL或参数个数为奇数，则抛错。
mysql> SELECT JSON_OBJECT('id', 87, 'name', 'carrot');
+-----------------------------------------+
| JSON_OBJECT('id', 87, 'name', 'carrot') |
+-----------------------------------------+
| {"id": 87, "name": "carrot"}            |
+-----------------------------------------+
```

**JSON_QUOTE**

```mysql
# JSON_QUOTE(json_val)
# 将json_val用"号括起来。
mysql> SELECT JSON_QUOTE('null'), JSON_QUOTE('"null"');
+--------------------+----------------------+
| JSON_QUOTE('null') | JSON_QUOTE('"null"') |
+--------------------+----------------------+
| "null"             | "\"null\""           |
+--------------------+----------------------+

mysql> SELECT JSON_QUOTE('[1, 2, 3]');
+-------------------------+
| JSON_QUOTE('[1, 2, 3]') |
+-------------------------+
| "[1, 2, 3]"             |
+-------------------------+
```

**CONVERT**

```mysql
# CONVERT(json_string,JSON)
mysql> select CONVERT('{"mail": "amy@gmail.com", "name": "Amy"}',JSON);
+----------------------------------------------------------+
| CONVERT('{"mail": "amy@gmail.com", "name": "Amy"}',JSON) |
+----------------------------------------------------------+
| {"mail": "amy@gmail.com", "name": "Amy"}                 |
+----------------------------------------------------------+
```

#### 2. 查询函数

**JSON_CONTAINS**

```mysql
# JSON_CONTAINS(json_doc, val[, path])
# 查询json文档是否在指定path包含指定的数据，包含则返回1，否则返回0。如果有参数为NULL或path不存在，则返回NULL。
mysql> SET @j = '{"a": 1, "b": 2, "c": {"d": 4}}';
mysql> SET @j2 = '1';
mysql> SELECT JSON_CONTAINS(@j, @j2, '$.a');
+-------------------------------+
| JSON_CONTAINS(@j, @j2, '$.a') |
+-------------------------------+
|                             1 |
+-------------------------------+
mysql> SELECT JSON_CONTAINS(@j, @j2, '$.b');
+-------------------------------+
| JSON_CONTAINS(@j, @j2, '$.b') |
+-------------------------------+
|                             0 |
+-------------------------------+
 
mysql> SET @j2 = '{"d": 4}';
mysql> SELECT JSON_CONTAINS(@j, @j2, '$.a');
+-------------------------------+
| JSON_CONTAINS(@j, @j2, '$.a') |
+-------------------------------+
|                             0 |
+-------------------------------+
mysql> SELECT JSON_CONTAINS(@j, @j2, '$.c');
+-------------------------------+
| JSON_CONTAINS(@j, @j2, '$.c') |
+-------------------------------+
|                             1 |
+-------------------------------+
```

**JSON_CONTAINS_PATH**

```mysql
# JSON_CONTAINS_PATH(json_doc, one_or_all, path[, path] …)
# 查询是否存在指定路径，存在则返回1，否则返回0。如果有参数为NULL，则返回NULL。
# one_or_all只能取值"one"或"all"，one表示只要有一个存在即可；all表示所有的都存在才行。
mysql> SET @j = '{"a": 1, "b": 2, "c": {"d": 4}}';
mysql> SELECT JSON_CONTAINS_PATH(@j, 'one', '$.a', '$.e');
+---------------------------------------------+
| JSON_CONTAINS_PATH(@j, 'one', '$.a', '$.e') |
+---------------------------------------------+
|                                           1 |
+---------------------------------------------+

mysql> SELECT JSON_CONTAINS_PATH(@j, 'all', '$.a', '$.e');
+---------------------------------------------+
| JSON_CONTAINS_PATH(@j, 'all', '$.a', '$.e') |
+---------------------------------------------+
|                                           0 |
+---------------------------------------------+

mysql> SELECT JSON_CONTAINS_PATH(@j, 'one', '$.c.d');
+----------------------------------------+
| JSON_CONTAINS_PATH(@j, 'one', '$.c.d') |
+----------------------------------------+
|                                      1 |
+----------------------------------------+

mysql> SELECT JSON_CONTAINS_PATH(@j, 'one', '$.a.d');
+----------------------------------------+
| JSON_CONTAINS_PATH(@j, 'one', '$.a.d') |
+----------------------------------------+
|                                      0 |
+----------------------------------------+
```

**JSON_EXTRACT**

```mysql
# JSON_EXTRACT(json_doc, path[, path] …)
# 从json文档里抽取数据。如果有参数有NULL或path不存在，则返回NULL。如果抽取出多个path，则返回的数据封闭在一个json array里。
mysql> SELECT JSON_EXTRACT('[10, 20, [30, 40]]', '$[1]');
+--------------------------------------------+
| JSON_EXTRACT('[10, 20, [30, 40]]', '$[1]') |
+--------------------------------------------+
| 20                                         |
+--------------------------------------------+

mysql> SELECT JSON_EXTRACT('[10, 20, [30, 40]]', '$[1]', '$[0]');
+----------------------------------------------------+
| JSON_EXTRACT('[10, 20, [30, 40]]', '$[1]', '$[0]') |
+----------------------------------------------------+
| [20, 10]                                           |
+----------------------------------------------------+

mysql> SELECT JSON_EXTRACT('[10, 20, [30, 40]]', '$[2][*]');
+-----------------------------------------------+
| JSON_EXTRACT('[10, 20, [30, 40]]', '$[2][*]') |
+-----------------------------------------------+
| [30, 40]                                      |
+-----------------------------------------------+

# 在MySQL 5.7.9+里可以用"->"替代。
mysql> SELECT c, JSON_EXTRACT(c, "$.id"), g
     > FROM jemp
     > WHERE JSON_EXTRACT(c, "$.id") > 1
     > ORDER BY JSON_EXTRACT(c, "$.name");
+-------------------------------+-----------+------+
| c                             | c->"$.id" | g    |
+-------------------------------+-----------+------+
| {"id": "3", "name": "Barney"} | "3"       |    3 |
| {"id": "4", "name": "Betty"}  | "4"       |    4 |
| {"id": "2", "name": "Wilma"}  | "2"       |    2 |
+-------------------------------+-----------+------+
3 rows in set (0.00 sec)
 
mysql> SELECT c, c->"$.id", g
     > FROM jemp
     > WHERE c->"$.id" > 1
     > ORDER BY c->"$.name";
+-------------------------------+-----------+------+
| c                             | c->"$.id" | g    |
+-------------------------------+-----------+------+
| {"id": "3", "name": "Barney"} | "3"       |    3 |
| {"id": "4", "name": "Betty"}  | "4"       |    4 |
| {"id": "2", "name": "Wilma"}  | "2"       |    2 |
+-------------------------------+-----------+------+
3 rows in set (0.00 sec)

# 在MySQL 5.7.13+，还可以用"->>"表示去掉抽取结果的"号，下面三种效果是一样的：
#	 JSON_UNQUOTE( JSON_EXTRACT(column, path) )
#	 JSON_UNQUOTE(column -> path)
#	 column->>path
mysql> SELECT * FROM jemp WHERE g > 2;
+-------------------------------+------+
| c                             | g    |
+-------------------------------+------+
| {"id": "3", "name": "Barney"} |    3 |
| {"id": "4", "name": "Betty"}  |    4 |
+-------------------------------+------+
2 rows in set (0.01 sec)
 
mysql> SELECT c->'$.name' AS name    
    ->     FROM jemp WHERE g > 2;
+----------+
| name     |
+----------+
| "Barney" |
| "Betty"  |
+----------+
2 rows in set (0.00 sec)
 
mysql> SELECT JSON_UNQUOTE(c->'$.name') AS name
    ->     FROM jemp WHERE g > 2;
+--------+
| name   |
+--------+
| Barney |
| Betty  |
+--------+
2 rows in set (0.00 sec)
 
mysql> SELECT c->>'$.name' AS name
    ->     FROM jemp WHERE g > 2;
+--------+
| name   |
+--------+
| Barney |
| Betty  |
+--------+
2 rows in set (0.00 sec)
```

**JSON_KEYS**

```mysql
# JSON_KEYS(json_doc[, path])
# 获取json文档在指定路径下的所有键值，返回一个json array。如果有参数为NULL或path不存在，则返回NULL。
mysql> SELECT JSON_KEYS('{"a": 1, "b": {"c": 30}}');
+---------------------------------------+
| JSON_KEYS('{"a": 1, "b": {"c": 30}}') |
+---------------------------------------+
| ["a", "b"]                            |
+---------------------------------------+

mysql> SELECT JSON_KEYS('{"a": 1, "b": {"c": 30}}', '$.b');
+----------------------------------------------+
| JSON_KEYS('{"a": 1, "b": {"c": 30}}', '$.b') |
+----------------------------------------------+
| ["c"]                                        |
+----------------------------------------------+
```

**JSON_SEARCH**

```mysql
# JSON_SEARCH(json_doc, one_or_all, search_str[, escape_char[, path] …])
# 查询包含指定字符串的paths，并作为一个json array返回。如果有参数为NUL或path不存在，则返回NULL。
# one_or_all："one"表示查询到一个即返回；"all"表示查询所有。
# search_str：要查询的字符串。 可以用LIKE里的’%'或‘_’匹配。
# path：在指定path下查。
mysql> SET @j = '["abc", [{"k": "10"}, "def"], {"x":"abc"}, {"y":"bcd"}]';
 
mysql> SELECT JSON_SEARCH(@j, 'one', 'abc');
+-------------------------------+
| JSON_SEARCH(@j, 'one', 'abc') |
+-------------------------------+
| "$[0]"                        |
+-------------------------------+
 
mysql> SELECT JSON_SEARCH(@j, 'all', 'abc');
+-------------------------------+
| JSON_SEARCH(@j, 'all', 'abc') |
+-------------------------------+
| ["$[0]", "$[2].x"]            |
+-------------------------------+
 
mysql> SELECT JSON_SEARCH(@j, 'all', 'ghi');
+-------------------------------+
| JSON_SEARCH(@j, 'all', 'ghi') |
+-------------------------------+
| NULL                          |
+-------------------------------+
 
mysql> SELECT JSON_SEARCH(@j, 'all', '10');
+------------------------------+
| JSON_SEARCH(@j, 'all', '10') |
+------------------------------+
| "$[1][0].k"                  |
+------------------------------+
 
mysql> SELECT JSON_SEARCH(@j, 'all', '10', NULL, '$');
+-----------------------------------------+
| JSON_SEARCH(@j, 'all', '10', NULL, '$') |
+-----------------------------------------+
| "$[1][0].k"                             |
+-----------------------------------------+
 
mysql> SELECT JSON_SEARCH(@j, 'all', '10', NULL, '$[*]');
+--------------------------------------------+
| JSON_SEARCH(@j, 'all', '10', NULL, '$[*]') |
+--------------------------------------------+
| "$[1][0].k"                                |
+--------------------------------------------+
 
mysql> SELECT JSON_SEARCH(@j, 'all', '10', NULL, '$**.k');
+---------------------------------------------+
| JSON_SEARCH(@j, 'all', '10', NULL, '$**.k') |
+---------------------------------------------+
| "$[1][0].k"                                 |
+---------------------------------------------+
 
mysql> SELECT JSON_SEARCH(@j, 'all', '10', NULL, '$[*][0].k');
+-------------------------------------------------+
| JSON_SEARCH(@j, 'all', '10', NULL, '$[*][0].k') |
+-------------------------------------------------+
| "$[1][0].k"                                     |
+-------------------------------------------------+
 
mysql> SELECT JSON_SEARCH(@j, 'all', '10', NULL, '$[1]');
+--------------------------------------------+
| JSON_SEARCH(@j, 'all', '10', NULL, '$[1]') |
+--------------------------------------------+
| "$[1][0].k"                                |
+--------------------------------------------+
 
mysql> SELECT JSON_SEARCH(@j, 'all', '10', NULL, '$[1][0]');
+-----------------------------------------------+
| JSON_SEARCH(@j, 'all', '10', NULL, '$[1][0]') |
+-----------------------------------------------+
| "$[1][0].k"                                   |
+-----------------------------------------------+
 
mysql> SELECT JSON_SEARCH(@j, 'all', 'abc', NULL, '$[2]');
+---------------------------------------------+
| JSON_SEARCH(@j, 'all', 'abc', NULL, '$[2]') |
+---------------------------------------------+
| "$[2].x"                                    |
+---------------------------------------------+
 
mysql> SELECT JSON_SEARCH(@j, 'all', '%a%');
+-------------------------------+
| JSON_SEARCH(@j, 'all', '%a%') |
+-------------------------------+
| ["$[0]", "$[2].x"]            |
+-------------------------------+
 
mysql> SELECT JSON_SEARCH(@j, 'all', '%b%');
+-------------------------------+
| JSON_SEARCH(@j, 'all', '%b%') |
+-------------------------------+
| ["$[0]", "$[2].x", "$[3].y"]  |
+-------------------------------+
 
mysql> SELECT JSON_SEARCH(@j, 'all', '%b%', NULL, '$[0]');
+---------------------------------------------+
| JSON_SEARCH(@j, 'all', '%b%', NULL, '$[0]') |
+---------------------------------------------+
| "$[0]"                                      |
+---------------------------------------------+
 
mysql> SELECT JSON_SEARCH(@j, 'all', '%b%', NULL, '$[2]');
+---------------------------------------------+
| JSON_SEARCH(@j, 'all', '%b%', NULL, '$[2]') |
+---------------------------------------------+
| "$[2].x"                                    |
+---------------------------------------------+
 
mysql> SELECT JSON_SEARCH(@j, 'all', '%b%', NULL, '$[1]');
+---------------------------------------------+
| JSON_SEARCH(@j, 'all', '%b%', NULL, '$[1]') |
+---------------------------------------------+
| NULL                                        |
+---------------------------------------------+
 
mysql> SELECT JSON_SEARCH(@j, 'all', '%b%', '', '$[1]');
+-------------------------------------------+
| JSON_SEARCH(@j, 'all', '%b%', '', '$[1]') |
+-------------------------------------------+
| NULL                                      |
+-------------------------------------------+
 
mysql> SELECT JSON_SEARCH(@j, 'all', '%b%', '', '$[3]');
+-------------------------------------------+
| JSON_SEARCH(@j, 'all', '%b%', '', '$[3]') |
+-------------------------------------------+
| "$[3].y"                                  |
+-------------------------------------------+
```

#### 3. 修改函数

**JSON_APPEND / JSON_ARRAY_APPEND**

```mysql
# JSON_ARRAY_APPEND(json_doc, path, val[, path, val] …)
# 在指定path的json array尾部追加val。如果指定path是一个json object，则将其封装成一个json array再追加。如果有参数为NULL，则返回NULL。
mysql> SET @j = '["a", ["b", "c"], "d"]';
mysql> SELECT JSON_ARRAY_APPEND(@j, '$[1]', 1);
+----------------------------------+
| JSON_ARRAY_APPEND(@j, '$[1]', 1) |
+----------------------------------+
| ["a", ["b", "c", 1], "d"]        |
+----------------------------------+
mysql> SELECT JSON_ARRAY_APPEND(@j, '$[0]', 2);
+----------------------------------+
| JSON_ARRAY_APPEND(@j, '$[0]', 2) |
+----------------------------------+
| [["a", 2], ["b", "c"], "d"]      |
+----------------------------------+
mysql> SELECT JSON_ARRAY_APPEND(@j, '$[1][0]', 3);
+-------------------------------------+
| JSON_ARRAY_APPEND(@j, '$[1][0]', 3) |
+-------------------------------------+
| ["a", [["b", 3], "c"], "d"]         |
+-------------------------------------+
 
mysql> SET @j = '{"a": 1, "b": [2, 3], "c": 4}';
mysql> SELECT JSON_ARRAY_APPEND(@j, '$.b', 'x');
+------------------------------------+
| JSON_ARRAY_APPEND(@j, '$.b', 'x')  |
+------------------------------------+
| {"a": 1, "b": [2, 3, "x"], "c": 4} |
+------------------------------------+
mysql> SELECT JSON_ARRAY_APPEND(@j, '$.c', 'y');
+--------------------------------------+
| JSON_ARRAY_APPEND(@j, '$.c', 'y')    |
+--------------------------------------+
| {"a": 1, "b": [2, 3], "c": [4, "y"]} |
+--------------------------------------+
 
mysql> SET @j = '{"a": 1}';
mysql> SELECT JSON_ARRAY_APPEND(@j, '$', 'z');
+---------------------------------+
| JSON_ARRAY_APPEND(@j, '$', 'z') |
+---------------------------------+
| [{"a": 1}, "z"]                 |
+---------------------------------+
```

**JSON_ARRAY_INSERT**

```mysql
# JSON_ARRAY_INSERT(json_doc, path, val[, path, val] …)
# 在path指定的json array元素插入val，原位置及以右的元素顺次右移。如果path指定的数据非json array元素，则略过此val；如果指定的元素下标超过json array的长度，则插入尾部。
mysql> SET @j = '["a", {"b": [1, 2]}, [3, 4]]';
mysql> SELECT JSON_ARRAY_INSERT(@j, '$[1]', 'x');
+------------------------------------+
| JSON_ARRAY_INSERT(@j, '$[1]', 'x') |
+------------------------------------+
| ["a", "x", {"b": [1, 2]}, [3, 4]]  |
+------------------------------------+

mysql> SELECT JSON_ARRAY_INSERT(@j, '$[100]', 'x');
+--------------------------------------+
| JSON_ARRAY_INSERT(@j, '$[100]', 'x') |
+--------------------------------------+
| ["a", {"b": [1, 2]}, [3, 4], "x"]    |
+--------------------------------------+

mysql> SELECT JSON_ARRAY_INSERT(@j, '$[1].b[0]', 'x');
+-----------------------------------------+
| JSON_ARRAY_INSERT(@j, '$[1].b[0]', 'x') |
+-----------------------------------------+
| ["a", {"b": ["x", 1, 2]}, [3, 4]]       |
+-----------------------------------------+

mysql> SELECT JSON_ARRAY_INSERT(@j, '$[2][1]', 'y');
+---------------------------------------+
| JSON_ARRAY_INSERT(@j, '$[2][1]', 'y') |
+---------------------------------------+
| ["a", {"b": [1, 2]}, [3, "y", 4]]     |
+---------------------------------------+

mysql> SELECT JSON_ARRAY_INSERT(@j, '$[0]', 'x', '$[2][1]', 'y');
+----------------------------------------------------+
| JSON_ARRAY_INSERT(@j, '$[0]', 'x', '$[2][1]', 'y') |
+----------------------------------------------------+
| ["x", "a", {"b": [1, 2]}, [3, 4]]                  |
+----------------------------------------------------+
```

**JSON_INSERT / JSON_REPLACE / JSON_SET**

```mysql
# JSON_INSERT(json_doc, path, val[, path, val] …)
# 在指定path下插入数据，如果path已存在，则忽略此val（不存在才插入）。
mysql> SET @j = '{ "a": 1, "b": [2, 3]}';
mysql> SELECT JSON_INSERT(@j, '$.a', 10, '$.c', '[true, false]');
+----------------------------------------------------+
| JSON_INSERT(@j, '$.a', 10, '$.c', '[true, false]') |
+----------------------------------------------------+
| {"a": 1, "b": [2, 3], "c": "[true, false]"}        |
+----------------------------------------------------+

# JSON_REPLACE(json_doc, path, val[, path, val] …)
# 替换指定路径的数据，如果某个路径不存在则略过（存在才替换）。如果有参数为NULL，则返回NULL。
mysql> SET @j = '{ "a": 1, "b": [2, 3]}';
mysql> SELECT JSON_REPLACE(@j, '$.a', 10, '$.c', '[true, false]');
+-----------------------------------------------------+
| JSON_REPLACE(@j, '$.a', 10, '$.c', '[true, false]') |
+-----------------------------------------------------+
| {"a": 10, "b": [2, 3]}                              |
+-----------------------------------------------------+

# JSON_SET(json_doc, path, val[, path, val] …)
# 设置指定路径的数据（不管是否存在）。如果有参数为NULL，则返回NULL。
mysql> SET @j = '{ "a": 1, "b": [2, 3]}';
mysql> SELECT JSON_SET(@j, '$.a', 10, '$.c', '[true, false]');
+-------------------------------------------------+
| JSON_SET(@j, '$.a', 10, '$.c', '[true, false]') |
+-------------------------------------------------+
| {"a": 10, "b": [2, 3], "c": "[true, false]"}    |
+-------------------------------------------------+

mysql> SELECT JSON_INSERT(@j, '$.a', 10, '$.c', '[true, false]');
+----------------------------------------------------+
| JSON_INSERT(@j, '$.a', 10, '$.c', '[true, false]') |
+----------------------------------------------------+
| {"a": 1, "b": [2, 3], "c": "[true, false]"}        |
+----------------------------------------------------+

mysql> SELECT JSON_REPLACE(@j, '$.a', 10, '$.c', '[true, false]');
+-----------------------------------------------------+
| JSON_REPLACE(@j, '$.a', 10, '$.c', '[true, false]') |
+-----------------------------------------------------+
| {"a": 10, "b": [2, 3]}                              |
+-----------------------------------------------------+
```

**JSON_MERGE**

```mysql
# JSON_MERGE(json_doc, json_doc[, json_doc] …)
# merge多个json文档。规则如下：
# 如果都是json array，则结果自动merge为一个json array；
# 如果都是json object，则结果自动merge为一个json object；
# 如果有多种类型，则将非json array的元素封装成json array再按照规则一进行mege。
mysql> SELECT JSON_MERGE('[1, 2]', '[true, false]');
+---------------------------------------+
| JSON_MERGE('[1, 2]', '[true, false]') |
+---------------------------------------+
| [1, 2, true, false]                   |
+---------------------------------------+

mysql> SELECT JSON_MERGE('{"name": "x"}', '{"id": 47}');
+-------------------------------------------+
| JSON_MERGE('{"name": "x"}', '{"id": 47}') |
+-------------------------------------------+
| {"id": 47, "name": "x"}                   |
+-------------------------------------------+
mysql> SELECT JSON_MERGE('1', 'true');
+-------------------------+
| JSON_MERGE('1', 'true') |
+-------------------------+
| [1, true]               |
+-------------------------+

mysql> SELECT JSON_MERGE('[1, 2]', '{"id": 47}');
+------------------------------------+
| JSON_MERGE('[1, 2]', '{"id": 47}') |
+------------------------------------+
| [1, 2, {"id": 47}]                 |
+------------------------------------+
```

**JSON_REMOVE**

```mysql
# JSON_REMOVE(json_doc, path[, path] …)
# 移除指定路径的数据，如果某个路径不存在则略过此路径。如果有参数为NULL，则返回NULL。
mysql> SET @j = '["a", ["b", "c"], "d"]';
mysql> SELECT JSON_REMOVE(@j, '$[1]');
+-------------------------+
| JSON_REMOVE(@j, '$[1]') |
+-------------------------+
| ["a", "d"]              |
+-------------------------+
```

**JSON_UNQUOTE**

```mysql
# JSON_UNQUOTE(val)
# 去掉val的引号。如果val为NULL，则返回NULL。
mysql> SET @j = '"abc"';
mysql> SELECT @j, JSON_UNQUOTE(@j);
+-------+------------------+
| @j    | JSON_UNQUOTE(@j) |
+-------+------------------+
| "abc" | abc              |
+-------+------------------+

mysql> SET @j = '[1, 2, 3]';
mysql> SELECT @j, JSON_UNQUOTE(@j);
+-----------+------------------+
| @j        | JSON_UNQUOTE(@j) |
+-----------+------------------+
| [1, 2, 3] | [1, 2, 3]        |
+-----------+------------------+
```

#### 4. 特性查询

**JSON_DEEPTH**

```mysql
# JSON_DEPTH(json_doc)
# 获取json文档的深度。如果参数为NULL，则返回NULL。
# 空的json array、json object或标量的深度为1。
mysql> SELECT JSON_DEPTH('{}'), JSON_DEPTH('[]'), JSON_DEPTH('true');
+------------------+------------------+--------------------+
| JSON_DEPTH('{}') | JSON_DEPTH('[]') | JSON_DEPTH('true') |
+------------------+------------------+--------------------+
|                1 |                1 |                  1 |
+------------------+------------------+--------------------+

mysql> SELECT JSON_DEPTH('[10, 20]'), JSON_DEPTH('[[], {}]');
+------------------------+------------------------+
| JSON_DEPTH('[10, 20]') | JSON_DEPTH('[[], {}]') |
+------------------------+------------------------+
|                      2 |                      2 |
+------------------------+------------------------+

mysql> SELECT JSON_DEPTH('[10, {"a": 20}]');
+-------------------------------+
| JSON_DEPTH('[10, {"a": 20}]') |
+-------------------------------+
|                             3 |
+-------------------------------+
```

**JSON_LENGTH**

```mysql
# JSON_LENGTH(json_doc[, path])
# 获取指定路径下的长度。如果参数为NULL，则返回NULL。
# 长度的计算规则：
# 标量的长度为1；
# json array的长度为元素的个数；
# json object的长度为key的个数。
mysql> SELECT JSON_LENGTH('[1, 2, {"a": 3}]');
+---------------------------------+
| JSON_LENGTH('[1, 2, {"a": 3}]') |
+---------------------------------+
|                               3 |
+---------------------------------+

mysql> SELECT JSON_LENGTH('{"a": 1, "b": {"c": 30}}');
+-----------------------------------------+
| JSON_LENGTH('{"a": 1, "b": {"c": 30}}') |
+-----------------------------------------+
|                                       2 |
+-----------------------------------------+

mysql> SELECT JSON_LENGTH('{"a": 1, "b": {"c": 30}}', '$.b');
+------------------------------------------------+
| JSON_LENGTH('{"a": 1, "b": {"c": 30}}', '$.b') |
+------------------------------------------------+
|                                              1 |
+------------------------------------------------+
```

**JSON_TYPE**

```mysql
# JSON_TYPE(json_val)
# 获取json文档的具体类型。如果参数为NULL，则返回NULL。
```

**JSON_VALID**

```mysql
# JSON_VALID(val)
# 判断val是否为有效的json格式，是为1，不是为0。如果参数为NUL，则返回NULL。
mysql> SELECT JSON_VALID('{"a": 1}');
+------------------------+
| JSON_VALID('{"a": 1}') |
+------------------------+
|                      1 |
+------------------------+

mysql> SELECT JSON_VALID('hello'), JSON_VALID('"hello"');
+---------------------+-----------------------+
| JSON_VALID('hello') | JSON_VALID('"hello"') |
+---------------------+-----------------------+
|                   0 |                     1 |
+---------------------+-----------------------+
```

# 3. 约束

## 1. 概述

**概念:**约束是作用于表中字段上的规则，用于限制存储在表中的数据。

**目的:**保证数据库中数据的正确、有效性和完整性。

**分类:**

| 约束                      | 描述                                                     | 关键字      |
| ------------------------- | -------------------------------------------------------- | ----------- |
| 非空约束                  | 限制该字段的数据不能为null                               | NOT NULL    |
| 唯一约束                  | 保证该字段的所有数据都是唯一、不重复的                   | UNIQUE      |
| 主键约束                  | 主键是一行数据的唯一标识，要求非空且唯一                 | PRIMARY KEY |
| 默认约束                  | 保存数据时，如果未指定该字段的值，则采用默认值           | DEFAULT     |
| 检查约束(8.0.16版本 之后) | 保证字段值满足某一个条件                                 | CHECK       |
| 外键约束                  | 用来让两张表的数据之间建立连接，保证数据的一致性和完整性 | FOREIGN KEY |

**注意:**约束是作用于表中字段上的，可以在创建表/修改表的时候添加约束。

## 2. 语法

**添加外键**

```mysql
CREATE TABLE 表名(
    字段名 数据类型,
    ...
    [CONSTRAINT] [外键名称] FOREIGN KEY (外键字段名) REFERENCES 主表 (主表列名)
);
ALTER TABLE 表名 ADD CONSTRAINT 外键名称 FOREIGN KEY (外键字段名) REFERENCES 主表 (主表列名) ;

案例: 为emp表的dept_id字段添加外键约束,关联dept表的主键id。
alter table emp add constraint fk_emp_dept_id foreign key (dept_id) references dept(id);
alter table emp add constraint fk_emp_dept_id foreign key (dept_id) references dept(id);
添加了外键约束之后，我们再到dept表(父表)删除id为1(dept_id=5)的记录，此时将会报错，不能删除或更新父表记录，因为存在外键约束。
```

**删除外键**

```mysql
ALTER TABLE 表名 DROP FOREIGN KEY 外键名称;
案例: 删除emp表的外键fk_emp_dept_id。
alter table emp drop foreign key fk_emp_dept_id;
```

## 3. 删除/更新行为

**添加了外键之后，再删除父表数据时产生的约束行为，我们就称为删除/更新行为。具体的删除/更新行 为有以下几种:**

| 行为        | 说明                                                         |
| ----------- | ------------------------------------------------------------ |
| NO ACTION   | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则不允许删除/更新。 (与 RESTRICT 一致) 默认行为 |
| RESTRICT    | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则不允许删除/更新。 (与 NO ACTION 一致) 默认行为 |
| CASCADE     | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有，则也删除/更新外键在子表中的记录。 |
| SET NULL    | 当在父表中删除对应记录时，首先检查该记录是否有对应外键，如果有则设置子表中该外键值为null（这就要求该外键允许取null）。 |
| SET DEFAULT | 父表有变更时，子表将外键列设置成一个默认的值 (Innodb不支持)  |

**CASCADE语法**

```mysql
ALTER TABLE 表名 ADD CONSTRAINT 外键名称 FOREIGN KEY (外键字段) REFERENCES 主表名 (主表字段名) ON UPDATE CASCADE ON DELETE CASCADE;

alter table emp add constraint fk_emp_dept_id foreign key (dept_id) references dept(id) on update cascade on delete cascade ;

```

**SET NULL语法**

```mysql
alter table emp add constraint fk_emp_dept_id foreign key (dept_id) references dept(id) on update set null on delete set null ;
```

# 4. 多表查询

## 1. 多表关系

- 一对多(多对一)
- 多对多
- 一对一

### 1. 一对多

案例: 部门 与 员工的关系 

关系: 一个部门对应多个员工，一个员工对应一个部门 

实现: 在多的一方建立外键，指向一的一方的主键

### 2. 多对多

案例: 学生 与 课程的关系 

关系: 一个学生可以选修多门课程，一门课程也可以供多个学生选择 

实现: 建立第三张中间表，中间表至少包含两个外键，分别关联两方主键

### 3. 一对一

案例: 用户 与 用户详情的关系 

关系: 一对一关系，多用于单表拆分，将一张表的基础字段放在一张表中，其他详情字段放在另 一张表中，以提升操作效率 

实现: 在任意一方加入外键，关联另外一方的主键，并且设置外键为唯一的(UNIQUE)

## 2.多表查询

**定义:**多表查询就是指从多张表中查询数据。

### **1. 分类**

**连接查询**

- 内连接：相当于查询A、B交集部分数据 
- 外连接： 
  - 左外连接：查询左表所有数据，以及两张表交集部分数据 
  - 右外连接：查询右表所有数据，以及两张表交集部分数据 
- 自连接：当前表与自身的连接查询，自连接必须使用表别名

**子查询**

## 3. 内连接

**隐式内连接**

```mysql
SELECT 字段列表 FROM 表1 , 表2 WHERE 条件 ... ;
```

**显式内连接**

```mysql
SELECT 字段列表 FROM 表1 [ INNER ] JOIN 表2 ON 连接条件 ... ;
```

**案例**

```mysql
A. 查询每一个员工的姓名 , 及关联的部门的名称 (隐式内连接实现)
 表结构: emp , dept
 连接条件: emp.dept_id = dept.id
select emp.name , dept.name from emp, dept where emp.dept_id = dept.id ;
-- 为每一张表起别名,简化SQL编写
select e.name, d.name from emp e , dept d where e.dept_id = d.id;
 
 
B. 查询每一个员工的姓名 , 及关联的部门的名称 (显式内连接实现) --- INNER JOIN ... ON ...
 表结构: emp , dept
 连接条件: emp.dept_id = dept.id
select e.name, d.name from emp e inner join dept d on e.dept_id = d.id;
-- 为每一张表起别名,简化SQL编写
select e.name, d.name from emp e join dept d on e.dept_id = d.id;
```

**注意事项: 一旦为表起了别名，就不能再使用表名来指定对应的字段了，此时只能够使用别名来指定字 段。**

## 4. 外连接

**左外连接**

```mysql
SELECT 字段列表 FROM 表1 LEFT [ OUTER ] JOIN 表2 ON 条件 ... ;
左外连接相当于查询表1(左表)的所有数据，当然也包含表1和表2交集部分的数据。
```

**右外连接**

```mysql
SELECT 字段列表 FROM 表1 RIGHT [ OUTER ] JOIN 表2 ON 条件 ... ;
右外连接相当于查询表2(右表)的所有数据，当然也包含表1和表2交集部分的数据
```

**案例**

```mysql
A. 查询emp表的所有数据, 和对应的部门信息
 由于需求中提到，要查询emp的所有数据，所以是不能内连接查询的，需要考虑使用外连接查询。
 表结构: emp, dept
 连接条件: emp.dept_id = dept.id
select e.*, d.name from emp e left outer join dept d on e.dept_id = d.id;
select e.*, d.name from emp e left join dept d on e.dept_id = d.id;

B. 查询dept表的所有数据, 和对应的员工信息(右外连接)
由于需求中提到，要查询dept表的所有数据，所以是不能内连接查询的，需要考虑使用外连接查询。
 表结构: emp, dept
 连接条件: emp.dept_id = dept.id
select d.*, e.* from emp e right outer join dept d on e.dept_id = d.id;
select d.*, e.* from dept d left outer join emp e on e.dept_id = d.id;
```

**注意事项： 左外连接和右外连接是可以相互替换的，只需要调整在连接查询时SQL中，表结构的先后顺序就可以了。而我们在日常开发使用时，更偏向于左外连接。**

## 5. 自连接

**定义: 自连接查询，顾名思义，就是自己连接自己，也就是把一张表连接查询多次。**

```mysql
SELECT 字段列表 FROM 表A 别名A JOIN 表A 别名B ON 条件 ... ;

A. 查询员工 及其 所属领导的名字
 表结构: emp
select a.name , b.name from emp a , emp b where a.managerid = b.id;

B. 查询所有员工 emp 及其领导的名字 emp , 如果员工没有领导, 也需要查询出来
 表结构: emp a , emp b
select a.name '员工', b.name '领导' from emp a left join emp b on a.managerid =b.id;
```

**注意事项: 在自连接查询中，必须要为表起别名，要不然我们不清楚所指定的条件、返回的字段，到底是哪一张表的字段。**

## 6. 联合查询

**定义: 对于union查询，就是把多次查询的结果合并起来，形成一个新的查询结果集。**

```mysql
# 对于联合查询的多张表的列数必须保持一致，字段类型也需要保持一致。
# union all 会将全部的数据直接合并在一起(不管有没有重复的数据)，union 会对合并之后的数据去重。
SELECT 字段列表 FROM 表A ...
UNION [ ALL ]
SELECT 字段列表 FROM 表B ....;

案例:
A. 将薪资低于 5000 的员工 , 和 年龄大于 50 岁的员工全部查询出来.当前对于这个需求，我们可以直接使用多条件查询，使用逻辑运算符 or 连接即可。 那这里呢，我们也可以通过union/union all来联合查询.
select * from emp where salary < 5000
union all
select * from emp where age > 50;
```

**注意:  如果多条查询语句查询出来的结果，字段数量不一致，在进行union/union all联合查询时，将会报错。**

## 7. 子查询

### 1. 定义

**定义: SQL语句中嵌套SELECT语句，称为嵌套查询，又称子查询。**

```mysql
# 子查询外部的语句可以是INSERT / UPDATE / DELETE / SELECT 的任何一个。
SELECT * FROM t1 WHERE column1 = ( SELECT column1 FROM t2 );
```

### 2. 分类

根据**子查询结果**不同，分为：

- A. 标量子查询（子查询结果为单个值） 
- B. 列子查询(子查询结果为一列) 
- C. 行子查询(子查询结果为一行) 
- D. 表子查询(子查询结果为多行多列)

根据**子查询位置**，分为：

- A. WHERE之后 
- B. FROM之后 
- C. SELECT之后

### 3. 标量子查询

**定义: 子查询返回的结果是单个值（数字、字符串、日期等），最简单的形式，这种子查询称为标量子查询。**

**常用的操作符：= <> > >= < <=**

**案例**

```mysql
# A. 查询 "销售部" 的所有员工信息
# 完成这个需求时，我们可以将需求分解为两步：
# 查询 "销售部" 部门ID
select id from dept where name = '销售部';
# 根据 "销售部" 部门ID, 查询员工信息
select * from emp where dept_id = (select id from dept where name = '销售部');

# B. 查询在 "方东白" 入职之后的员工信息
# 完成这个需求时，我们可以将需求分解为两步：
# 查询 方东白 的入职日期
select entrydate from emp where name = '方东白';
# 查询指定入职日期之后入职的员工信息
select * from emp where entrydate > (select entrydate from emp where name = '方东白');
```

### 4. 列子查询

**定义: 子查询返回的结果是一列（可以是多行），这种子查询称为列子查询。**

**常用的操作符：IN 、NOT IN 、 ANY 、SOME 、 ALL**

| 操作符 | 描述                                   |
| ------ | -------------------------------------- |
| IN     | 在指定的集合范围之内，多选一           |
| NOT IN | 不在指定的集合范围之内                 |
| ANY    | 子查询返回列表中，有任意一个满足即可   |
| SOME   | 与ANY等同，使用SOME的地方都可以使用ANY |
| ALL    | 子查询返回列表的所有值都必须满足       |

**案例**

```mysql
# A. 查询 "销售部" 和 "市场部" 的所有员工信息
# 分解为以下两步:
# 查询 "销售部" 和 "市场部" 的部门ID
select id from dept where name = '销售部' or name = '市场部';
# 根据部门ID, 查询员工信息
select * from emp where dept_id in (select id from dept where name = '销售部' orname = '市场部');

# B. 查询比 财务部 所有人工资都高的员工信息
# 分解为以下两步:
# 查询所有 财务部 人员工资
select id from dept where name = '财务部';
select salary from emp where dept_id = (select id from dept where name = '财务部');
# 比 财务部 所有人工资都高的员工信息
select * from emp where salary > all ( select salary from emp where dept_id = (select id from dept where name = '财务部') );

# C. 查询比研发部其中任意一人工资高的员工信息
# 分解为以下两步:
# 查询研发部所有人工资
select salary from emp where dept_id = (select id from dept where name = '研发部');
# 比研发部其中任意一人工资高的员工信息
select * from emp where salary > any ( select salary from emp where dept_id = (select id from dept where name = '研发部') );
```

### 5. 行子查询

**定义: 子查询返回的结果是一行（可以是多列），这种子查询称为行子查询。**

**常用的操作符：= 、<> 、IN 、NOT IN**

**案例**

```mysql
# A. 查询与 "张无忌" 的薪资及直属领导相同的员工信息 ;
# 这个需求同样可以拆解为两步进行:
# 查询 "张无忌" 的薪资及直属领导
select salary, managerid from emp where name = '张无忌';
# 查询与 "张无忌" 的薪资及直属领导相同的员工信息 ;
select * from emp where (salary,managerid) = (select salary, managerid from emp where name = '张无忌');
```

### 6. 表子查询

**定义: 子查询返回的结果是多行多列，这种子查询称为表子查询。**

**常用的操作符：IN**

**案例**

```mysql
# A. 查询与 "鹿杖客" , "宋远桥" 的职位和薪资相同的员工信息
# 分解为两步执行:
# 查询 "鹿杖客" , "宋远桥" 的职位和薪资
select job, salary from emp where name = '鹿杖客' or name = '宋远桥';
# 查询与 "鹿杖客" , "宋远桥" 的职位和薪资相同的员工信息
select * from emp where (job,salary) in ( select job, salary from emp where name ='鹿杖客' or name = '宋远桥' );

# B. 查询入职日期是 "2006-01-01" 之后的员工信息 , 及其部门信息
# 分解为两步执行:
# 入职日期是 "2006-01-01" 之后的员工信息
select * from emp where entrydate > '2006-01-01';
# 查询这部分员工, 对应的部门信息
select e.*, d.* from (select * from emp where entrydate > '2006-01-01') e left join dept d on e.dept_id = d.id ;
```

## 8. 多表查询案例

```mysql
# 1). 查询员工的姓名、年龄、职位、部门信息 （隐式内连接）
# 表: emp , dept
# 连接条件: emp.dept_id = dept.id
select e.name, e.age, e.job, d.name from emp e, dept d where e.dept_id = d.id;

# 2). 查询年龄小于30岁的员工的姓名、年龄、职位、部门信息（显式内连接）
# 表: emp , dept
# 连接条件: emp.dept_id = dept.id
select e.name , e.age , e.job , d.name from emp e inner join dept d on e.dept_id = d.id where e.age < 30;

# 3). 查询拥有员工的部门ID、部门名称 (distinct: 去除重复记录)
# 表: emp , dept
# 连接条件: emp.dept_id = dept.id
select distinct d.id, d.name from emp e, dept d where e.dept_id = d.id;

# 4). 查询所有年龄大于40岁的员工, 及其归属的部门名称; 如果员工没有分配部门, 也需要展示出来(外连接)
# 表: emp , dept
# 连接条件: emp.dept_id = dept.id
select e.*, d.name from emp e left join dept d on e.dept_id = d.id where e.age > 40;

# 5). 查询所有员工的工资等级
# 表: emp , salgrade
# 连接条件 : emp.salary >= salgrade.losal and emp.salary <= salgrade.hisal
# 方式一:
select e.*, s.grade, s.losal, s.hisal from emp e, salgrade s where e.salary >= s.losal and e.salary <= s.hisal;
# 方式二:
select e.*, s.grade, s.losal, s.hisal from emp e, salgrade s where e.salary between s.losal and s.hisal;

# 6). 查询 "研发部" 所有员工的信息及工资等级
# 表: emp , salgrade , dept
# 连接条件 : emp.salary between salgrade.losal and salgrade.hisal , emp.dept_id = dept.id
# 查询条件 : dept.name = '研发部'
select e.*, s.grade from emp e, dept d, salgrade s where e.dept_id = d.id and (e.salary between s.losal and s.hisal) and d.name = '研发部';

# 7). 查询 "研发部" 员工的平均工资
# 表: emp , dept
# 连接条件 : emp.dept_id = dept.id
select avg(e.salary) from emp e, dept d where e.dept_id = d.id and d.name = '研发部';

# 8). 查询工资比 "灭绝" 高的员工信息。
# ①. 查询 "灭绝" 的薪资
select salary from emp where name = '灭绝';
# ②. 查询比她工资高的员工数据
select * from emp where salary > (select salary from emp where name = '灭绝');

# 9). 查询比平均薪资高的员工信息
# ①. 查询员工的平均薪资
select avg(salary) from emp;
# ②. 查询比平均薪资高的员工信息
select * from emp where salary > (select avg(salary) from emp);

# 10). 查询低于本部门平均工资的员工信息
# ①. 查询指定部门平均薪资
select avg(e1.salary) from emp e1 where e1.dept_id = 1;
select avg(e1.salary) from emp e1 where e1.dept_id = 2;
# ②. 查询低于本部门平均工资的员工信息
select * from emp e2 where e2.salary < (select avg(e1.salary) from emp e1 wheree1.dept_id = e2.dept_id);

# 11). 查询所有的部门信息, 并统计部门的员工人数
select d.id, d.name, ( select count(*) from emp e where e.dept_id = d.id ) '人数' from dept d;

# 12). 查询所有学生的选课情况, 展示出学生名称, 学号, 课程名称
# 表: student , course , student_course
# 连接条件: student.id = student_course.studentid , course.id = student_course.courseid
select s.name, s.no, c.name from student s, student_course sc, course c where s.id = sc.studentid and sc.courseid = c.id;

# 备注: 以上需求的实现方式可能会很多, SQL写法也有很多，只要能满足我们的需求，查询出符合条件的记录即可。
```

# 5. 事务

## 1. 控制事务

### 方式一

```mysql
# 1). 查看/设置事务提交方式
SELECT @@autocommit ;
SET @@autocommit = 0 ;  -- 设置提交方式为手动提交

# 2). 提交事务
COMMIT;

# 3). 回滚事务
ROLLBACK;

# 注意：上述的这种方式，我们是修改了事务的自动提交行为, 把默认的自动提交修改为了手动提交, 此时我们执行的DML语句都不会提交, 需要手动的执行commit进行提交
```

### 方式二

```mysql
# 1). 开启事务
START TRANSACTION 或 BEGIN ;

# 2). 提交事务
COMMIT;

# 3). 回滚事务
ROLLBACK;
```

### 转账案例

```mysql
-- 开启事务
start transaction

-- 1. 查询张三余额
select * from account where name = '张三';
-- 2. 张三的余额减少1000
update account set money = money - 1000 where name = '张三';
-- 3. 李四的余额增加1000
update account set money = money + 1000 where name = '李四';

-- 如果正常执行完毕, 则提交事务
commit;

-- 如果执行过程中报错, 则回滚事务
rollback;
```

## 2. 事务的四大特性

- **原子性（Atomicity）**：事务是不可分割的最小操作单元，要么全部成功，要么全部失败。 
- **一致性（Consistency）**：事务完成时，必须使所有的数据都保持一致状态。 
- **隔离性（Isolation）**：数据库系统提供的隔离机制，保证事务在不受外部并发操作影响的独立 环境下运行。 
- **持久性（Durability）**：事务一旦提交或回滚，它对数据库中的数据的改变就是永久的。

上述就是事务的四大特性，简称ACID。

## 3. 并发事务问题

**定义:** 并发事务所引发的问题是**多个并发事务**在**执行的过程当中**, 所出现的 **脏读, 不可重复读, 幻读** 的问题

| 问题       | 描述                                                         |
| ---------- | ------------------------------------------------------------ |
| 脏读       | 一个事务读到另一个事务还没有提交的数据  ---   ( A事务读取到 B事务更新数据 但未提交事务的数据 ) |
| 不可重复读 | 一个事务先后读取同一条记录, 但两次读取的数据不同, 称之为不可重复读  ---  ( B事务在A事务读取了记录之后更新了这个数据, 然后A又读的时候数据就发生了变化 ) |
| 幻读       | 一个事务按照条件查询条件时, 没有对应的数据行, 但是在插入数据时, 又发现这行数据已经出现, 好像出现了'幻影'  ---   ( A事务在确定没有id为3的数据之后, 进行插入数据, 这个时候B事务在A事务插入之前插入了id为3的数据, 并提交, A事务插入失败报错说存在这条数据, 但是A事务查询的时候依然告诉数据库依然告诉A事务没有id为3的数据 ) |

## 4. 事务的隔离级别

**注意：事务隔离级别越高，数据越安全，但是性能越低。**

为了解决并发事务所引发的问题，在数据库中引入了事务隔离级别。主要有以下几种：

对号指的是: 当前隔离级别下, 事务问题存在

叉号指的是: 当前隔离级别下, 事务的问题不存在, 即已解决

| 隔离级别                          | 脏读 | 不可重复读 | 幻读 |
| --------------------------------- | ---- | ---------- | ---- |
| 读未提交 -> Read uncommitted      | √    | √          | √    |
| 读已提交 -> Read committed        | ×    | √          | √    |
| 可重复读 -> Repeatable Read(默认) | ×    | ×          | √    |
| 串行化 -> Serializable            | ×    | ×          | ×    |

**解决方式:**

- Read committed: A事务读取到的数据一直是开始的数据, 直到B事务提交更新数据事务之后, A才会读取到新的数据
- Repeatable Read(默认): A事务读取事务的时候一直都是刚开始的数据, 即使B事务提交了更新数据事务, 直到A事务重新执行的时候, A事务才会读到最新的数据
- Serializable: A事务在读取到没有 id = 4 的数据之后, 进行 insert 操作, 这个时候B事务也进行 insert 操作并未提交, 这个时候B事务会进入等待状态, A事务的 insert 操作会执行成功, 并且在 A事务提交事务 之后, B事务才会进行 insert 操作, 并且这个时候 B事务会报错, 说 此时已经有 id = 4 的数据了

```mysql
# 1). 查看事务隔离级别
SELECT @@TRANSACTION_ISOLATION;

# 2). 设置事务隔离级别 -session/global指当前会话的隔离级别/全局隔离级别
SET [ SESSION | GLOBAL ] TRANSACTION ISOLATION LEVEL { READ UNCOMMITTED | READ COMMITTED | REPEATABLE READ | SERIALIZABLE }

# 设置隔离级别
set session transaction isolation level read uncommitted;
```

# 6.MySQL存储引擎

**MySQL体系结构**

{% asset_img  存储引擎-1.jpg %}

```mysql
# 1). 建表时指定存储引擎
CREATE TABLE 表名(
    字段1 字段1类型 [ COMMENT 字段1注释 ] ,
    ......
    字段n 字段n类型 [COMMENT 字段n注释 ]
) ENGINE = INNODB [ COMMENT 表注释 ] ;

# 2). 查询当前数据库支持的存储引擎
 show engines;
```

**案例:**

```mysql
# A. 查询建表语句 --- 默认存储引擎: InnoDB
show create table account;
# B. 查询当前数据库支持的存储引擎
show engines ;
# C. 创建表 my_myisam , 并指定MyISAM存储引擎
create table my_myisam(
    id int,
    name varchar(10)
) engine = MyISAM ;
# D. 创建表 my_memory , 指定Memory存储引擎
create table my_memory(
    id int,
    name varchar(10)
) engine = Memory ;
```

# 7. 索引

## 1. 索引概述

​	索引（index）是帮助MySQL高效获取数据的数据结构(有序)。在数据之外，数据库系统还维护着满足特定查找算法的数据结构，这些数据结构以某种方式引用（指向）数据，这样就可以在这些数据结构 上实现高级查找算法，这种数据结构就是索引。

## 2. 索引演示

执行语句: 

```mysql
select * from user where age = 45;
```

在无索引情况下，就需要从第一行开始扫描，一直扫描到最后一行，我们称之为 全表扫描，性能很低。

{% asset_img  索引-1.jpg %}

如果我们针对于这张表建立了索引，**假设( 注意, 这里只是假设!!! 并不是索引的真实结构!!! )**索引结构就是二叉树，那么也就意味着，会对age这个字段建 立一个二叉树的索引结构。此时我们在进行查询时，只需要扫描三次就可以找到数据了，极大的提高的查询的效率。

{% asset_img  索引-2.jpg %}

## 3. 索引特点

| 优势                                                         | 劣势                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 提高数据检索的效率，降低数据库的IO成本                       | 索引列也是要占用空间的。                                     |
| 通过索引列对数据进行排序，降低数据排序的成本，降低CPU的消耗。 | 索引大大提高了查询效率，同时却也降低更新表的速度，如对表进行INSERT、UPDATE、DELETE时，效率降低。 |

##  4. 索引结构

**概述**

MySQL的索引是在存储引擎层实现的，不同的存储引擎有不同的索引结构，主要包含以下几种：

| 索引结构            | 描述                                                         |
| ------------------- | ------------------------------------------------------------ |
| B+Tree索引          | 最常见的索引类型，大部分引擎都支持 B+ 树索引                 |
| Hash索引            | 底层数据结构是用哈希表实现的, 只有精确匹配索引列的查询才有效, 不支持范围查询 |
| R-tree(空间索引）   | 空间索引是MyISAM引擎的一个特殊索引类型，主要用于地理空间数据类型，通常使用较少 |
| Full-text(全文索引) | 是一种通过建立倒排索引,快速匹配文档的方式。类似于Lucene,Solr,ES |



上述是MySQL中所支持的所有的索引结构，接下来，我们再来看看不同的存储引擎对于索引结构的支持情况

| 索引        | InnoDB        | MyISAM | Memory |
| ----------- | ------------- | ------ | ------ |
| B+tree索引  | 支持          | 支持   | 支持   |
| Hash索引    | 不支持        | 不支持 | 支持   |
| R-tree 索引 | 不支持        | 支持   | 不支持 |
| Full-text   | 5.6版本后支持 | 支持   | 不支持 |

**注意： 我们平常所说的索引，如果没有特别指明，都是指B+树结构组织的索引。**

### 1. 二叉树( 左小右大 )

{% asset_img  索引-3.jpg %}

**如果选择二叉树作为索引结构，会存在以下缺点：** 

- 顺序插入时，会形成一个链表，查询性能大大降低。 
- 大数据量情况下，层级较深，检索速度慢。

**即使使用红黑树, 也会存在一个缺点:**

- 大数据量情况下，层级较深，检索速度慢。

### 2. B-Tree ( 多路平衡查找树 ) 

**知识点:  树的度数指的是一个节点的子节点个数。**

{% asset_img  索引-4.jpg %}

**案例:**

{% asset_img  索引-5.jpg %}

```http
https://www.cs.usfca.edu/~galles/visualization/BTree.html
```

**特点:**

- 5阶的B树，每一个节点最多存储4个key，对应5个指针。 
- 一旦节点存储的key数量到达5，就会裂变，中间元素向上分裂。 
- 在B树中，非叶子节点和叶子节点都会存放数据。

### 3. B+Tree

**标准B+Tree**

{% asset_img  索引-6.jpg %}

**MySQL索引数据结构对于经典的B+Tree进行了优化, 在原B+Tree的基础上, 增加了一个指向相邻叶子结点的链表指针, 就形成了带有顺序指针的B+Tree, 提高区间访问的性能.**

{% asset_img  索引-7.jpg %}

### 4. B-Tree和B+Tree的区别

B+Tree 与 B-Tree相比，主要有以下三点区别：

- 所有的数据都会出现在叶子节点。 
- 叶子节点形成一个单向链表。 
- 非叶子节点仅仅起到索引数据作用，具体的数据都是在叶子节点存放的。

### 5. Hash结构

 **结构** 

​	哈希索引就是采用一定的hash算法，将键值换算成新的hash值，映射到对应的槽位上，然后存储在 hash表中。

**特点**

- Hash索引只能用于对等比较(=，in)，不支持范围查询（between，>，< ，...） 
- 无法利用索引完成排序操作 
- 查询效率高，通常(不存在hash冲突的情况)只需要一次检索就可以了，效率通常要高于B+tree索引

 **存储引擎支持**

​	在MySQL中，支持hash索引的是Memory存储引擎。 而InnoDB中具有自适应hash功能，hash索引是InnoDB存储引擎根据B+Tree索引在指定条件下自动构建的。

{% asset_img  索引-8.jpg %}

### 6. 思考题

**为什么InnoDB存储引擎选择使用B+tree索引结构?**

-  相对于二叉树，层级更少，搜索效率高； 
-  对于B-tree，无论是叶子节点还是非叶子节点，都会保存数据，这样导致一页中存储 的键值减少，指针跟着减少，要同样保存大量数据，只能增加树的高度，导致性能降低； 
-  相对Hash索引，B+tree支持范围匹配及排序操作；

## 5. 索引分类

### 1. 索引分类

| 分类     | 含义                                                 | 特点                     | 关键字   |
| -------- | ---------------------------------------------------- | ------------------------ | -------- |
| 主键索引 | 针对表中主键创建的索引                               | 默认自动创建, 只能有一个 | PRIMARY  |
| 唯一索引 | 避免同一个表中某数据列中的重复                       | 可以有多个               | UNIQUE   |
| 常规索引 | 快速定位特定数据                                     | 可以有多个               |          |
| 全文索引 | 全文索引查找的是文本中的关键词, 而不是比较索引中的值 | 可以有多个               | FULLTEXT |

### 2. 聚集索引 && 二级索引

| 分类                      | 含义                                                        | 特点                |
| ------------------------- | ----------------------------------------------------------- | ------------------- |
| 聚集索引(Clustered Index) | 将数据存储与索引放到了一块，索引结构的叶子 节点保存了行数据 | 必须有,而且只有一个 |
| 二级索引(Secondary Index) | 将数据与索引分开存储，索引结构的叶子节点关 联的是对应的主键 | 可以存在多个        |

**聚集索引选取规则:**

- 如果存在主键，主键索引就是聚集索引。 
- 如果不存在主键，将使用第一个唯一（UNIQUE）索引作为聚集索引。 
- 如果表没有主键，或没有合适的唯一索引，则InnoDB会自动生成一个rowid作为隐藏的聚集索 引。

**聚集索引和二级索引的具体结构如下：**

{% asset_img  索引-9.jpg %}

- 聚集索引的叶子节点下挂的是这一行的数据 。 
- 二级索引的叶子节点下挂的是该字段值对应的主键值。

**接下来，我们来分析一下，当我们执行如下的SQL语句时，具体的查找过程是什么样子的。**

{% asset_img  索引-10.jpg %}

**具体过程如下:** 

1. 由于是根据name字段进行查询，所以先根据name='Arm'到name字段的二级索引中进行匹配查找。但是在二级索引中只能查找到 Arm 对应的主键值 10。 
2. 由于查询返回的数据是*，所以此时，还需要根据主键值10，到聚集索引中查找10对应的记录，最终找到10对应的行row。 
3. 最终拿到这一行的数据，直接返回即可。

**回表查询**： 这种先到二级索引中查找数据，找到主键值，然后再到聚集索引中根据主键值，获取数据的方式，就称之为回表查询。

### 3. 思考题

```mysql
以下两条SQL语句，那个执行效率高? 为什么?
A. select * from user where id = 10 ;
B. select * from user where name = 'Arm' ;
备注: id为主键，name字段创建的有索引；
解答：
	A 语句的执行性能要高于B 语句。因为A语句直接走聚集索引，直接返回数据。 而B语句需要先查询name字段的二级索引，然后再查询聚集索引，也就是需要进行回表查询。


InnoDB主键索引的B+tree高度为多高呢?
假设:
一行数据大小为1k，一页中可以存储16行这样的数据。InnoDB的指针占用6个字节的空间，主键即使为bigint，占用字节数为8。
高度为2：
    n * 8 + (n + 1) * 6 = 16*1024 , 算出n约为 1170
    1171* 16 = 18736
    也就是说，如果树的高度为2，则可以存储 18000 多条记录。
高度为3：
    1171 * 1171 * 16 = 21939856
    也就是说，如果树的高度为3，则可以存储 2200w 左右的记录。
```

## 6. 索引语法

### 1. 创建索引

```mysql
CREATE [ UNIQUE | FULLTEXT ] INDEX index_name ON table_name ( index_col_name,... ) ;
```

### 2. 查看索引

```mysql
SHOW INDEX FROM table_name ;
```

### 3. 删除索引

```mysql
DROP INDEX index_name ON table_name ;
```

### 4. 案例

```mysql
# A. name字段为姓名字段，该字段的值可能会重复，为该字段创建索引
create index idx_user_name on tb_user(name);

# B. phone手机号字段的值，是非空，且唯一的，为该字段创建唯一索引
create unique index idx_user_phone on tb_user(phone);

# C. 为profession、age、status创建联合索引
create index idx_user_pro_age_sta on tb_user(profession, age, status);

# D. 为email建立合适的索引来提升查询效率
create index idx_email on tb_user(email);

# E. 查看tb_user表中所有的索引
show index from tb_user
```

## 7. SQL性能分析

### 1. SQL执行效率

​	MySQL 客户端连接成功后，通过 show [session|global] status 命令可以提供服务器状态信 息。通过如下指令，可以查看当前数据库的INSERT、UPDATE、DELETE、SELECT的访问频次：

```mysql
-- session 是查看当前会话 ;
-- global 是查询全局数据 ;
SHOW GLOBAL STATUS LIKE 'Com_______';
```

{% asset_img  索引-11.jpg %}

- Com_delete: 删除次数 
- Com_insert: 插入次数 
- Com_select: 查询次数 
- Com_update: 更新次数

**通过上述指令，我们可以查看到当前数据库到底是以查询为主，还是以增删改为主，从而为数据 库优化提供参考依据。 如果是以增删改为主，我们可以考虑不对其进行索引的优化。 如果是以 查询为主，那么就要考虑对数据库的索引进行优化了。**

### 2. 慢查询日志

​	慢查询日志记录了所有执行时间超过指定参数（long_query_time，单位：秒，默认10秒）的所有 SQL语句的日志。 

​	MySQL的慢查询日志默认没有开启，我们可以查看一下系统变量 slow_query_log。

​	如果要开启慢查询日志，需要在MySQL的配置文件（/etc/my.cnf）中配置如下信息：

```MYSQL
# 开启MySQL慢日志查询开关
slow_query_log = 1
# 设置慢日志的时间为2秒，SQL语句执行时间超过2秒，就会视为慢查询，记录慢查询日志
long_query_time = 2
```

​	配置完毕之后，通过以下指令重新启动MySQL服务器进行测试，查看慢日志文件中记录的信息 /var/lib/mysql/localhost-slow.log。

```mysql
systemctl restart mysqld
```

然后，再次查看开关情况，慢查询日志就已经打开了。

### 3. profile详情

​	show profiles 能够在做SQL优化时帮助我们了解时间都耗费到哪里去了。通过have_profiling 参数，能够看到当前MySQL是否支持profile操作：

```mysql
SELECT @@have_profiling ;
```

​	可以通过set语句在 session/global级别开启profiling：

```mysql
SET profiling = 1;
```

​	执行一系列的业务SQL的操作，然后通过如下指令查看指令的执行耗时：

```mysql
-- 查看每一条SQL的耗时基本情况
show profiles;

-- 查看指定query_id的SQL语句各个阶段的耗时情况
show profile for query query_id;

-- 查看指定query_id的SQL语句CPU的使用情况
show profile cpu for query query_id;
```

### 4.  explain

​	EXPLAIN 或者 DESC命令获取 MySQL 如何执行 SELECT 语句的信息，包括在 SELECT 语句执行 过程中表如何连接和连接的顺序。

```mysql
-- 直接在select语句之前加上关键字 explain / desc
EXPLAIN SELECT 字段列表 FROM 表名 WHERE 条件 ;
```

**Explain 执行计划中各个字段的含义:**

| 字段         | 含义                                                         |
| ------------ | ------------------------------------------------------------ |
| id           | select查询的序列号，表示查询中执行select子句或者是操作表的顺序 (id相同，执行顺序从上到下；id不同，值越大，越先执行)。 |
| select_type  | 表示 SELECT 的类型，常见的取值有 SIMPLE（简单表，即不使用表连接 或者子查询）、PRIMARY（主查询，即外层的查询）、 UNION（UNION 中的第二个或者后面的查询语句）、 SUBQUERY（SELECT/WHERE之后包含了子查询）等 |
| type         | 表示连接类型，性能由好到差的连接类型为NULL、system、const、 eq_ref、ref、range、 index、all 。 |
| possible_key | 显示可能应用在这张表上的索引，一个或多个。                   |
| key          | 实际使用的索引，如果为NULL，则没有使用索引。                 |
| key_len      | 表示索引中使用的字节数， 该值为索引字段最大可能长度，并非实际使用长 度，在不损失精确性的前提下， 长度越短越好 。 |
| rows         | MySQL认为必须要执行查询的行数，在innodb引擎的表中，是一个估计值， 可能并不总是准确的。 |
| filtered     | 表示返回结果的行数占需读取行数的百分比， filtered 的值越大越好。 |
| Extra        | Using where; Using Index: 查找使用了索引，但是需要的数据都在索引列中能找到，所以不需 要回表查询数据<br />Using index condition:  查找使用了索引，但是需要回表查询数据 |

## 8. 索引使用

###  最左前缀法则

​	如果索引了多列（联合索引），要遵守最左前缀法则。最左前缀法则指的是查询从索引的最左列开始， 并且不跳过索引中的列。如果跳跃某一列，索引将会部分失效(后面的字段索引失效)。

###  范围查询

​	联合索引中，出现范围查询(>,<)，范围查询右侧的列索引失效。所以，在业务允许的情况下，尽可能的使用类似于 >= 或 <= 这类的范围查询，而避免使用 > 或 < 。

### 索引失效情况

####  1. 索引列运算  

​	不要在索引列上进行运算操作， 索引将失效。

####  2. 字符串不加引号  

​	字符串类型字段使用时，不加引号，索引将失效。

​	如果字符串不加单引号，对于查询结果，没什么影响，但是数 据库存在隐式类型转换，索引将失效。

#### 3. 模糊查询

​	如果仅仅是尾部模糊匹配，索引不会失效。如果是头部模糊匹配，索引失效。

#### 4. or连接条件

​	用or分割开的条件， 如果or前的条件中的列有索引，而后面的列中没有索引，那么涉及的索引都不会被用到。当or连接的条件，左右两侧字段都有索引时，索引才会生效。

####  5. 数据分布影响

​	如果MySQL评估使用索引比全表更慢，则不使用索引。

​	相同的SQL语句，只是传入的字段值不同，最终的执行计划也完全不一样，这是因为MySQL在查询时，会评估使用索引的效率与走全表扫描的效率，如果走全表扫描更快，则放弃索引，走全表扫描。 因为索引是用来索引少量数据的，如果通过索引查询返回大批量的数据，则还不 如走全表扫描来的快，此时索引就会失效。

### SQL提示

```mysql
# use index ： 建议MySQL使用哪一个索引完成此次查询（仅仅是建议，mysql内部还会再次进 行评估）。
explain select * from tb_user use index(idx_user_pro) where profession = '软件工程';
# ignore index ：  忽略指定的索引。
explain select * from tb_user ignore index(idx_user_pro) where profession = '软件工程';
# force index ： 强制使用索引。
explain select * from tb_user force index(idx_user_pro) where profession = '软件工程';
```

### 覆盖索引

​	尽量使用覆盖索引，减少select *。 覆盖索引是指 查询使用了索引，并 且需要返回的列，在该索引中已经全部能够找到 。

### 前缀索引

​	当字段类型为字符串（varchar，text，longtext等）时，有时候需要索引很长的字符串，这会让 索引变得很大，查询时，浪费大量的磁盘IO， 影响查询效率。此时可以只将字符串的一部分前缀，建 立索引，这样可以大大节约索引空间，从而提高索引效率。

```mysql
# 1). 语法
create index idx_xxxx on table_name(column(n)) ;
create index idx_email_5 on tb_user(email(5));
# 2). 前缀长度
select count(distinct email) / count(*) from tb_user ;
select count(distinct substring(email,1,5)) / count(*) from tb_user ;
```

3). 前缀索引的查询流程

{% asset_img  索引-12.jpg %}

### 单列索引与联合索引

​	单列索引：即一个索引只包含单个列。 

​	联合索引：即一个索引包含了多个列。

​	在业务场景中，如果存在多个查询条件，考虑针对于查询字段建立索引时，建议建立联合索引， 而非单列索引。

## 9. 索引设计原则

- 针对于数据量较大，且查询比较频繁的表建立索引。 
- 针对于常作为查询条件（where）、排序（order by）、分组（group by）操作的字段建立索 引。 
- 尽量选择区分度高的列作为索引，尽量建立唯一索引，区分度越高，使用索引的效率越高。 
- 如果是字符串类型的字段，字段的长度较长，可以针对于字段的特点，建立前缀索引。 
- 尽量使用联合索引，减少单列索引，查询时，联合索引很多时候可以覆盖索引，节省存储空间， 避免回表，提高查询效率。 
- 要控制索引的数量，索引并不是多多益善，索引越多，维护索引结构的代价也就越大，会影响增 删改的效率。 1 create unique index idx_user_phone_name on tb_user(phone,name); 
- 如果索引列不能存储NULL值，请在创建表时使用NOT NULL约束它。当优化器知道每列是否包含 NULL值时，它可以更好地确定哪个索引最有效地用于查询。

# 8. SQL优化

## 1. 插入数据

### insert

```mysql
# 如果我们需要一次性往数据库表中插入多条记录，可以从以下三个方面进行优化。
insert into tb_test values(1,'tom');
insert into tb_test values(2,'cat');
insert into tb_test values(3,'jerry');
.....

# 1). 优化方案一 批量插入数据
Insert into tb_test values(1,'Tom'),(2,'Cat'),(3,'Jerry');
# 2). 优化方案二 手动控制事务
start transaction;
    insert into tb_test values(1,'Tom'),(2,'Cat'),(3,'Jerry');
    insert into tb_test values(4,'Tom'),(5,'Cat'),(6,'Jerry');
    insert into tb_test values(7,'Tom'),(8,'Cat'),(9,'Jerry');
commit;
# 3). 优化方案三 主键顺序插入，性能要高于乱序插入。
主键乱序插入 : 8 1 9 21 88 2 4 15 89 5 7 3
主键顺序插入 : 1 2 3 4 5 7 8 9 15 21 88 89
```

### 大批量插入数据

```mysql
# 如果一次性需要插入大批量数据(比如: 几百万的记录)，使用insert语句插入性能较低，此时可以使用MySQL数据库提供的load指令进行插入。操作如下：可以执行如下指令，将数据脚本文件中的数据加载到表结构中：
-- 客户端连接服务端时，加上参数 -–local-infile
mysql –-local-infile -u root -p
-- 设置全局参数local_infile为1，开启从本地加载文件导入数据的开关
set global local_infile = 1;
-- 执行load指令将准备好的数据，加载到表结构中
load data local infile '/root/sql1.log' into table tb_user fields
terminated by ',' lines terminated by '\n' ;
# 主键顺序插入性能高于乱序插入
```

## 2. 主键优化

###  数据组织方式

​	在InnoDB存储引擎中，表数据都是根据主键顺序组织存放的，这种存储方式的表称为索引组织表 (index organized table IOT)。

​	在InnoDB引擎中，数据行是记录在逻辑结构 page 页中的，而每一个页的大小是固定的，默认16K。 那也就意味着， 一个页中所存储的行也是有限的，如果插入的数据行row在该页存储不小，将会存储 到下一个页中，页与页之间会通过指针连接。

### 页分裂

​	页可以为空，也可以填充一半，也可以填充100%。每个页包含了2-N行数据(如果一行数据过大，会行溢出)，根据主键排列。

###  页合并

​	当删除一行记录时，实际上记录并没有被物理删除，只是记录被标记（flaged）为删除并且它的空间 变得允许被其他记录声明使用。

​	当页中删除的记录达到 MERGE_THRESHOLD（默认为页的50%），InnoDB会开始寻找最靠近的页（前 或后）看看是否可以将两个页合并以优化空间使用。

​	这个里面所发生的合并页的这个现象，就称之为 "页合并"。

`知识小贴士： MERGE_THRESHOLD：合并页的阈值，可以自己设置，在创建表或者创建索引时指定。`

### 索引设计原则

- 满足业务需求的情况下，尽量降低主键的长度。 
- 插入数据时，尽量选择顺序插入，选择使用AUTO_INCREMENT自增主键。 
- 尽量不要使用UUID做主键或者是其他自然主键，如身份证号。 
- 业务操作时，避免对主键的修改。

## 3. order by优化

MySQL的排序，有两种方式： 

Using filesort : 通过表的索引或全表扫描，读取满足条件的数据行，然后在排序缓冲区sort buffer中完成排序操作，所有不是通过索引直接返回排序结果的排序都叫 FileSort 排序。 

Using index : 通过有序索引顺序扫描直接返回有序数据，这种情况即为 using index，不需要 额外排序，操作效率高。 

对于以上的两种排序方式，Using index的性能高，而Using filesort的性能低，我们在优化排序 操作时，尽量要优化为 Using index。

**出order by优化原则:**

- A. 根据排序字段建立合适的索引，多字段排序时，也遵循最左前缀法则。 
- B. 尽量使用覆盖索引。 
- C. 多字段排序, 一个升序一个降序，此时需要注意联合索引在创建时的规则（ASC/DESC）。 
- D. 如果不可避免的出现filesort，大数据量排序时，可以适当增大排序缓冲区大小 sort_buffer_size(默认256k)。

## 4. group by优化

**在分组操作中，我们需要通过以下两点进行优化，以提升性能：** 

- A. 在分组操作时，可以通过索引来提高效率。 
- B. 分组操作时，索引的使用也是满足最左前缀法则的。

## 5. limit优化

在数据量比较大时，如果进行limit分页查询，在查询时，越往后，分页查询效率越低。

**优化思路: 一般分页查询时，通过创建 覆盖索引 能够比较好地提高性能，可以通过覆盖索引加子查 询形式进行优化。**

## 6. count优化

### 1. 概述

`select count(*) from tb_user ;`

- MyISAM 引擎把一个表的总行数存在了磁盘上，因此执行 `count(*)`的时候会直接返回这个 数，效率很高； 但是如果是带条件的count，MyISAM也慢。 
- InnoDB 引擎就麻烦了，它执行 count(*) 的时候，需要把数据一行一行地从引擎里面读出 来，然后累积计数。 

**如果说要大幅度提升InnoDB表的count效率，主要的优化思路：自己计数(可以借助于redis这样的数 据库进行,但是如果是带条件的count又比较麻烦了)。**

### 2. count用法

​	count() 是一个聚合函数，对于返回的结果集，一行行地判断，如果 count 函数的参数不是 NULL，累计值就加 1，否则不加，最后返回累计值。

用法：count（*）、count（主键）、count（字段）、count（数字）

| count用法    | 含义                                                         |
| ------------ | ------------------------------------------------------------ |
| count(主 键) | InnoDB 引擎会遍历整张表，把每一行的 主键id 值都取出来，返回给服务层。 服务层拿到主键后，直接按行进行累加(主键不可能为null) |
| count(字 段) | 没有not null 约束 : InnoDB 引擎会遍历整张表把每一行的字段值都取出 来，返回给服务层，服务层判断是否为null，不为null，计数累加。 有not null 约束：InnoDB 引擎会遍历整张表把每一行的字段值都取出来，返 回给服务层，直接按行进行累加。 |
| count(数 字) | InnoDB 引擎遍历整张表，但不取值。服务层对于返回的每一行，放一个数字“1” 进去，直接按行进行累加。 |
| count(*)     | InnoDB引擎并不会把全部字段取出来，而是专门做了优化，不取值，服务层直接 按行进行累加。 |

**按照效率排序的话，count(字段) < count(主键 id) < count(1) ≈ `count(*)`，所以尽 量使用 `count(*)`。**

## 7. update优化

我们主要需要注意一下update语句执行时的注意事项。

`update course set name = 'javaEE' where id = 1 ;`

当我们在执行删除的SQL语句时，会锁定id为1这一行的数据，然后事务提交之后，行锁释放。

但是当我们在执行如下SQL时。 

`update course set name = 'SpringBoot' where name = 'PHP' ;`

当我们开启多个事务，在执行上述的SQL时，我们发现行锁升级为了表锁。 导致该update语句的性能 大大降低。

**InnoDB的行锁是针对索引加的锁，不是针对记录加的锁 ,并且该索引不能失效，否则会从行锁升级为表锁 。**

# 9. 视图

## 1. 介绍

视图（View）是一种虚拟存在的表。视图中的数据并不在数据库中实际存在，行和列数据来自定义视 图的查询中使用的表，并且是在使用视图时动态生成的。 

通俗的讲，视图只保存了查询的SQL逻辑，不保存查询结果。所以我们在创建视图的时候，主要的工作 就落在创建这条SQL查询语句上。

## 2. 语法

```mysql
# 创建
CREATE [OR REPLACE] VIEW 视图名称[(列名列表)] AS SELECT语句 [ WITH [ CASCADED | LOCAL ] CHECK OPTION ]

# 查询
查看创建视图语句：SHOW CREATE VIEW 视图名称;
查看视图数据：SELECT * FROM 视图名称 ...... ;

# 修改
# 方式一：
CREATE [OR REPLACE] VIEW 视图名称[(列名列表)] AS SELECT语句 [ WITH [ CASCADED | LOCAL ] CHECK OPTION ]
# 方式二：
ALTER VIEW 视图名称[(列名列表)] AS SELECT语句 [ WITH [ CASCADED | LOCAL ] CHECK OPTION ]

# 删除
DROP VIEW [IF EXISTS] 视图名称 [,视图名称] ...

# 演示
-- 创建视图
create or replace view stu_v_1 as select id,name from student where id <= 10;
-- 查询视图
show create view stu_v_1;
select * from stu_v_1;
select * from stu_v_1 where id < 3;
-- 修改视图
create or replace view stu_v_1 as select id,name,no from student where id <= 10;
alter view stu_v_1 as select id,name from student where id <= 10;
-- 删除视图
drop view if exists stu_v_1;
-- 插入数据
insert into stu_v_1 values(6,'Tom');
insert into stu_v_1 values(17,'Tom22');
```

## 3. 检查选项

当使用WITH CHECK OPTION子句创建视图时，MySQL会通过视图检查正在更改的每个行，例如 插入，更新，删除，以使其**符合**视图的定义。 

MySQL**允许**基于另一个视图创建视图，它**还会**检查依赖视图中的规则以保持一致性。为了确定检查的范围，mysql提供了两个选项： CASCADED 和 LOCAL ，默认值为 CASCADED 。

### CASCADED 级联

​	比如，v2视图是基于v1视图的，如果在v2视图创建的时候指定了检查选项为 cascaded，但是v1视图创建时未指定检查选项。 则在执行检查时，不仅会检查v2，还会级联检查v2的关联视图v1。

### LOCAL 本地

​	比如，v2视图是基于v1视图的，如果在v2视图创建的时候指定了检查选项为 local ，但是v1视图创建时未指定检查选项。 则在执行检查时，只会检查v2，不会检查v2的关联视图v1。

## 4. 视图的更新

​	要使视图可更新，视图中的行与基础表中的行之间必须存在一对一的关系。如果视图包含以下任何一 项，则该视图不可更新： 

- A. 聚合函数或窗口函数（SUM()、 MIN()、 MAX()、 COUNT()等） 
- B. DISTINCT 
- C. GROUP BY 
- D. HAVING 
- E. UNION 或者 UNION ALL

```mysql
create view stu_v_count as select count(*) from student;
# 上述的视图中，就只有一个单行单列的数据，如果我们对这个视图进行更新或插入的，将会报错
insert into stu_v_count values(10); -- 报错
```

## 5. 视图作用

**1). 简单** 

​	视图不仅可以简化用户对数据的理解，也可以简化他们的操作。那些被经常使用的查询可以被定义为视图，从而使得用户不必为以后的操作每次指定全部的条件。 

**2). 安全** 

​	数据库可以授权，但不能授权到数据库特定行和特定的列上。通过视图用户只能查询和修改他们所能见到的数据。

**3). 数据独立** 

​	视图可帮助用户屏蔽真实表结构变化带来的影响。

## 6. 案例

```mysql
# 1). 为了保证数据库表的安全性，开发人员在操作tb_user表时，只能看到的用户的基本字段，屏蔽手机号和邮箱两个字段。
create view tb_user_view as select id,name,profession,age,gender,status,createtime from tb_user;
select * from tb_user_view;


# 2). 查询每个学生所选修的课程（三张表联查），这个功能在很多的业务中都有使用到，为了简化操作，定义一个视图。
create view tb_stu_course_view as select s.name student_name , s.no student_no , c.name course_name from student s, student_course sc , course c where s.id = sc.studentid and sc.courseid = c.id;
select * from tb_stu_course_view;
```

# 10. 存储过程

## 1. 介绍

​	存储过程是事先经过编译并存储在数据库中的一段 SQL 语句的集合，调用存储过程可以简化应用开发 人员的很多工作，减少数据在数据库和应用服务器之间的传输，对于提高数据处理的效率是有好处的。 

​	存储过程思想上很简单，就是数据库 SQL 语言层面的代码封装与重用。

## 2. 特点

- 封装，复用 -----------------------> 可以把某一业务SQL封装在存储过程中，需要用到的时候直接调用即可。 
- 可以接收参数，也可以返回数据 --------> 再存储过程中，可以传递参数，也可以接收返回值。 
- 减少网络交互，效率提升 -------------> 如果涉及到多条SQL，每执行一次都是一次网络传输。 而如果封装在存储过程中，我们只需要网络交互一次可能就可以了。

## 3. 基本语法

### 创建

```mysql
CREATE PROCEDURE 存储过程名称 ([ 参数列表 ])
BEGIN
	-- SQL语句
END ;
```

### 调用

```mysql
CALL 名称 ([ 参数 ]);
```

### 查看

```mysql
SELECT * FROM INFORMATION_SCHEMA.ROUTINES WHERE ROUTINE_SCHEMA = 'xxx'; -- 查询指定数据库的存储过程及状态信息
SHOW CREATE PROCEDURE 存储过程名称 ; -- 查询某个存储过程的定义
```

### 删除

```mysql
DROP PROCEDURE [IF EXISTS] 存储过程名称;
```

### 案例

```mysql
-- 存储过程基本语法
-- 创建
create procedure p1()
begin
	select count(*) from student;
end;

-- 调用
call p1();

-- 查看
show create procedure p1;

-- 删除
drop procedure if exists p1;
```

### 提示(delimiter)

​	**在cmd命令行中创建存储过程的时候, 中间的sql语句会以 ; 结束, 这个时候系统会认为语句已经结束了, 所以就会报错, 所以这个时候需要重新指定语句的结束符号, 通过delimiter可以自定义结束符.**

## 4. 变量

**在MySQL中变量分为三种类型: 系统变量、用户定义变量、局部变量。**

### 系统变量

​	系统变量是MySQL服务器提供，不是用户定义的，属于服务器层面。分为全局变量（GLOBAL）、会话变量（SESSION）。

```mysql
# 1). 查看系统变量
SHOW [ SESSION | GLOBAL ] VARIABLES ; -- 查看所有系统变量, 如果不指定默认是全局
SHOW [ SESSION | GLOBAL ] VARIABLES LIKE '......'; -- 可以通过LIKE模糊匹配方式查找变量
SELECT @@[SESSION | GLOBAL].系统变量名; -- 查看指定变量的值

# 2). 设置系统变量
SET [ SESSION | GLOBAL ] 系统变量名 = 值 ;
SET @@[SESSION | GLOBAL].系统变量名 = 值 ;


# 注意:
# 如果没有指定SESSION/GLOBAL，默认是SESSION，会话变量。
# mysql服务重新启动之后，所设置的全局参数会失效，要想不失效，可以在 /etc/my.cnf 中配置。
# A. 全局变量(GLOBAL): 全局变量针对于所有的会话。
# B. 会话变量(SESSION): 会话变量针对于单个会话，在另外一个会话窗口就不生效了。
```

**案例**

```mysql
-- 查看系统变量
show session variables ;
show session variables like 'auto%';
show global variables like 'auto%';
select @@global.autocommit;
select @@session.autocommit;
-- 设置系统变量
set session autocommit = 1;
insert into course(id, name) VALUES (6, 'ES');
set global autocommit = 0;
select @@global.autocommit;
```

### 用户定义变量

​	用户定义变量 是用户根据需要自己定义的变量，用户变量不用提前声明，在用的时候直接用 "@变量名" 使用就可以。其作用域为当前连接。

```mysql
# 1). 赋值
# 方式一:
SET @var_name = expr [, @var_name = expr] ... ;
SET @var_name := expr [, @var_name := expr] ... ;
# 赋值时，可以使用 = ，也可以使用 := 。
# 方式二:
SELECT @var_name := expr [, @var_name := expr] ... ;
SELECT 字段名 INTO @var_name FROM 表名;

# 2). 使用
SELECT @var_name ;
```

**注意: 如果用到了一个变量, 但是变量还未声明或赋值, 那么拿到的是一个NULL.**

**案例**

```mysql
-- 赋值
set @myname = 'itcast';
set @myage := 10;
set @mygender := '男',@myhobby := 'java';
select @mycolor := 'red';
select count(*) into @mycount from tb_user;
-- 使用
select @myname,@myage,@mygender,@myhobby;
select @mycolor , @mycount;
select @abc; -- NULL
```

### 局部变量

​	局部变量 是根据需要定义的在局部生效的变量，访问之前，需要DECLARE声明。可用作存储过程内的 局部变量和输入参数，局部变量的范围是在其内声明的BEGIN ... END块。

```mysql
# 1). 声明
DECLARE 变量名 变量类型 [DEFAULT ... ] ;
# 变量类型就是数据库字段类型：INT、BIGINT、CHAR、VARCHAR、DATE、TIME等。

# 2). 赋值
SET 变量名 = 值 ;
SET 变量名 := 值 ;
SELECT 字段名 INTO 变量名 FROM 表名 ... ;
```

**案例**

```mysql
-- 声明局部变量 - declare
-- 赋值
create procedure p2()
begin
	declare user_count int default 1;
    -- set user_count = 2;
    select count(*) into user_count from tb_user;
    select user_count;
end;
```

## 5. if 语法

​	if语法就是用来做判断的.

### 语法结构

```mysql
IF 条件1 THEN
.....
ELSEIF 条件2 THEN -- 可选
.....
ELSE -- 可选
.....
END IF;
# 在if条件判断的结构中，ELSE IF 结构可以有多个，也可以没有。 ELSE结构可以有，也可以没有。
```

### 案例

```mysql
# 根据定义的分数score变量，判定当前分数对应的分数等级。
# 	score >= 85分，等级为优秀。
# 	score >= 60分 且 score < 85分，等级为及格。
# 	score < 60分，等级为不及格。
create procedure p3()
begin
    declare score int default 58;
    declare result varchar(10);
    if score >= 85 then
    	set result := '优秀';
    elseif score >= 60 then
    	set result := '及格';
    else
    	set result := '不及格';
    end if;
    select result;
end;
call p3();
```

## 6. 参数

### 介绍

​	参数的类型，主要分为以下三种：IN、OUT、INOUT。 具体的含义如下：

| 类型  | 含义                                         | 备注 |
| ----- | -------------------------------------------- | ---- |
| IN    | 该类参数作为输入, 也就是需要调用时传入的值   | 默认 |
| OUT   | 该类参数作为输出, 也就是该参数可以作为返回值 |      |
| INOUT | 既可以作为输入参数, 也可以作为输出参数       |      |

用法

```mysql
CREATE PROCEDURE 存储过程名称 ([ IN/OUT/INOUT 参数名 参数类型 ])
BEGIN
	-- SQL语句
END ;
```

### 案例

```mysql
# 1. 根据传入参数score，判定当前分数对应的分数等级，并返回。
# 	score >= 85分，等级为优秀。
# 	score >= 60分 且 score < 85分，等级为及格。
# 	score < 60分，等级为不及格。
CREATE DEFINER=`root`@`localhost` PROCEDURE `p2`(in sc int, out grade varchar(5))
begin
    if sc >= 85 then
		set grade = '优秀';
	elseif sc >= 65 then
		set grade = '良好';
	else set grade = '不及格';
    end if;
end;
# 这里的 @result 是用户自定义的变量
call p2(88, @result);
select @result;
```

```mysql
# 2. 将传入的200分制的分数，进行换算，换算成百分制，然后返回。
CREATE DEFINER=`root`@`localhost` PROCEDURE `p3`(INOUT score double)
begin
	set score = score*0.5;
end
# 这里在运行的时候要先进行定义, 否则没有参数进行接收返回值
set @score = 75;
call p3(@score);
select @score;
```

## 7. case 语法

**注意：如果判定条件有多个，多个条件之间，可以使用 and 或 or 进行连接。**

​	case结构及作用，和我们在基础篇中所讲解的流程控制函数很类似。有两种语法格式：

### 语法一:

```mysql
-- 含义： 当case_value的值为 when_value1时，执行statement_list1，当值为 when_value2时， 执行statement_list2， 否则就执行 statement_list
CASE case_value
    WHEN when_value1 THEN statement_list1
    [ WHEN when_value2 THEN statement_list2] ...
    [ ELSE statement_list ]
END CASE;
```

### 语法二:

```mysql
-- 含义： 当条件search_condition1成立时，执行statement_list1，当条件search_condition2成立时，执行statement_list2， 否则就执行 statement_list
CASE
    WHEN search_condition1 THEN statement_list1
    [WHEN search_condition2 THEN statement_list2] ...
    [ELSE statement_list]
END CASE;
```

### 案例

```mysql
# 根据传入的月份，判定月份所属的季节（要求采用case结构）。
# 	1-3月份，为第一季度
# 	4-6月份，为第二季度
# 	7-9月份，为第三季度
# 	10-12月份，为第四季度
CREATE DEFINER=`root`@`localhost` PROCEDURE `p4`(in month int)
begin
	declare result varchar(5);
	case
		when month >= 10 and month <= 12 then
			set result = '第四季度';
		when month >= 7 and month <= 9 then
			set result = '第三季度';
		when month >= 4 and month <= 6 then
			set result = '第二季度';
		when month >= 1 and month <= 3 then
			set result = '第一季度';
		else set result = '月份有误';
	end case;
	# 这里 concat 是字符串拼接方法, 后面的 res 是给列名起了个别名
    select concat('您输入输入的月份是: ' , month , ', 属于的季度是: ', result) res;
end;
# 调用
call p4(2);
```

## 8. while 语法

### 介绍

​	while 循环是有条件的循环控制语句。满足条件后，再执行循环体中的SQL语句。

### 语法

```mysql
-- 先判定条件，如果条件为true，则执行逻辑，否则，不执行逻辑
WHILE 条件 DO
	SQL逻辑...
END WHILE;
```

### 案例

```mysql
# 计算从1累加到n的值，n为传入的参数值。
CREATE DEFINER=`root`@`localhost` PROCEDURE `p5`(in num int)
begin
	declare temp int default 0;
    while num > 0 do
		set temp = temp + num;
        set num = num - 1;
	end while;
    select temp '最终结果为';
end;
# 调用
call p5(3);
```

## 9. repeat 语法

### 介绍

​	repeat是有条件的循环控制语句, 当满足until声明的条件的时候，则退出循环。

### 语法

```mysql
repeat
	SQL...逻辑
	until 条件
end repeat;
```

### 案例

```mysql
# 计算从1累加到n的值，n为传入的参数值。(使用repeat实现)
CREATE DEFINER=`root`@`localhost` PROCEDURE `p6`(in num int)
begin
	declare temp int default 0;
    repeat
		set temp = temp + num;
		set num = num - 1;
	# 注意!!!!!  until 后面的条件是不用加 分号 的!!!!
	until num <= 0
	end repeat;
    select temp '最终结果为';
end
# 调用
call p6(30);
```

## 10. loop 语法

### 介绍

​	LOOP 实现简单的循环，如果不在SQL逻辑中增加退出循环的条件，可以用其来实现简单的死循环。

### 语法

​	LOOP可以配合一下两个语句使用： 

- LEAVE ：配合循环使用，退出循环。 
- ITERATE：必须用在循环中，作用是跳过当前循环剩下的语句，直接进入下一次循环。

```mysql
[begin_label:] LOOP
	SQL逻辑...
END LOOP [end_label];

LEAVE label; -- 退出指定标记的循环体
ITERATE label; -- 直接进入下一次循环
# 上述语法中出现的 begin_label，end_label，label 指的都是我们所自定义的标记。
```

### 案例

```mysql
# 计算从1累加到n的值，n为传入的参数值。
CREATE DEFINER=`root`@`localhost` PROCEDURE `p7`(in num int)
begin
	declare result int default 0;
    total:loop
		if num <= 0 then
			leave total;
		else 
		    set result = result + num;
			set num = num - 1;
		end if;
	end loop total;
    select result;
end;
# 调用
call p7(30);
```

```mysql
# 计算从1到n之间的偶数累加的值，n为传入的参数值。
CREATE DEFINER=`root`@`localhost` PROCEDURE `p8`(in num int)
begin
	declare result int default 0;
    total:loop
    	# 这里也可以用 %
    	# if n % 2 = 0
		if mod(num, 2) = 0 && num > 0 then
			set result = result + num;
            set num = num - 1;
		elseif num > 0 then
			set num = num - 1;
            iterate total;
		else leave total;
		end if;
    end loop total;
    select result '最终结果为';
end;
# 调用
call p8(10);
```

## 11. cursor 游标

### 介绍

​	游标（CURSOR）是用来存储查询结果集的数据类型 , 在存储过程和函数中可以使用游标对结果集进 行循环的处理。

### 语法

​	游标的使用包括游标的声明、OPEN、FETCH 和 CLOSE，其语法分别如下。

```mysql
# A. 声明游标
DECLARE 游标名称 CURSOR FOR 查询语句 ;
# B. 打开游标
OPEN 游标名称 ;
# C. 获取游标记录
FETCH 游标名称 INTO 变量 [, 变量 ] ;
# D. 关闭游标
CLOSE 游标名称 ;
```

### 案例

```mysql
# 根据传入的参数uage，来查询用户表tb_user中，所有的用户年龄小于等于uage的用户姓名 （name）和专业（profession），并将用户的姓名和专业插入到所创建的一张新表 (id,name,profession)中。
# 虽然这个案例中的代码可以获取到结果, 但是是会报错的, 这个因为循环跳不出去, 循环中的变量拿不到数据 的原因, 所以就需要条件处理程序
CREATE DEFINER=`root`@`localhost` PROCEDURE `p9`(in uid int)
begin
	declare uname varchar(11);
    declare uscore int;
    declare mycursor cursor for select `name`, `score` from score where id < uid;
    drop table if exists tb_score_pro;
    create table if not exists tb_score_pro(
		id int primary key auto_increment,
        name varchar(11),
        score int
    );
    open mycursor;
    while true do 
		fetch mycursor into uname, uscore;
        insert into tb_score_pro values (null, uname, uscore);
    end while;
    close mycursor;
end;
# 调用
call p9(5);
```

### 用条件处理解决bug

```mysql
CREATE DEFINER=`root`@`localhost` PROCEDURE `p10`(in uid int)
begin
	declare uname varchar(11);
    declare uscore int;
    declare mycursor cursor for select `name`, `score` from score where id < uid;
    # 就加下面任意一行代码就可以
    -- declare exit handler for SQLSTATE '02000' close mycursor;
    declare exit handler for not found close mycursor;
    
    drop table if exists tb_score_pro;
    create table if not exists tb_score_pro(
		id int primary key auto_increment,
        name varchar(11),
        score int
    );
    open mycursor;
    while true do 
		fetch mycursor into uname, uscore;
        insert into tb_score_pro values (null, uname, uscore);
    end while;
    close mycursor;
end
```



## 12. 条件处理程序

### 介绍

​	条件处理程序（Handler）可以用来定义在流程控制结构执行过程中遇到问题时相应的处理步骤。

### 语法

```mysql
DECLARE handler_action HANDLER FOR condition_value [, condition_value] ... statement ;
# handler_action 的取值：
    CONTINUE: 继续执行当前程序
    EXIT: 终止执行当前程序
# condition_value 的取值：
    SQLSTATE sqlstate_value: 状态码，如 02000
    SQLWARNING: 所有以01开头的SQLSTATE代码的简写
    NOT FOUND: 所有以02开头的SQLSTATE代码的简写
    SQLEXCEPTION: 所有没有被SQLWARNING 或 NOT FOUND捕获的SQLSTATE代码的简写
```

**具体的错误状态码，可以参考官方文档：**

https://dev.mysql.com/doc/refman/8.0/en/declare-handler.html 

https://dev.mysql.com/doc/mysql-errors/8.0/en/server-error-reference.html

### 案例

案例就是上面游标的案例  ->  解决的bug

## 13. 存储函数

### 介绍

存储函数是有返回值的存储过程，存储函数的参数只能是IN类型的。

### 语法

```mysql
CREATE FUNCTION 存储函数名称 ([ 参数列表 ])
RETURNS type [characteristic ...]
BEGIN
    -- SQL语句
    RETURN ...;
END ;

# characteristic说明：
# 	DETERMINISTIC：相同的输入参数总是产生相同的结果。
# 	NO SQL ：不包含 SQL 语句。
# 	READS SQL DATA：包含读取数据的语句，但不包含写入数据的语句。
```

### 案例

```mysql
# 计算从1累加到n的值，n为传入的参数值。
CREATE DEFINER=`root`@`localhost` FUNCTION `fun1`(num int) RETURNS int(11)
    DETERMINISTIC
begin
	declare temp int default 0;
    while num >= 0 do 
		set temp = temp + num;
		set num = num - 1;
    end while;
    return temp;
end;
# 调用
select fun1(100);
```

## 14. 触发器

### 介绍

触发器是与表有关的数据库对象，指在insert/update/delete之前(BEFORE)或之后(AFTER)，触发并执行触发器中定义的SQL语句集合。触发器的这种特性可以协助应用在数据库端确保数据的完整性 , 日志记录 , 数据校验等操作 。 

使用别名OLD和NEW来引用触发器中发生变化的记录内容，这与其他的数据库是相似的。现在触发器还只支持行级触发，不支持语句级触发(行级触发: SQL语句影响了几行, 就触发几次.  语句级触发: SQL语句只要执行, 就会触发一次)。

### 触发器类型

| 触发器类型      | NEW 和 OLD                                              |
| --------------- | ------------------------------------------------------- |
| INSERT 型触发器 | NEW 表示将要或者已经新增的数据                          |
| UPDATE 型触发器 | OLD 表示修改之前的数据 , NEW 表示将要或已经修改后的数据 |
| DELETE 型触发器 | OLD 表示将要或者已经删除的数据                          |

### 语法

```mysql
# 1). 创建
CREATE TRIGGER trigger_name
BEFORE/AFTER INSERT/UPDATE/DELETE
ON tbl_name FOR EACH ROW -- 行级触发器
BEGIN
	trigger_stmt ;
END;

# 2). 查看
SHOW TRIGGERS;

# 3). 删除
DROP TRIGGER [schema_name.]trigger_name ; -- 如果没有指定 schema_name，默认为当前数据库 。
```

### 案例

```mysql
# 通过触发器记录 tb_user 表的数据变更日志，将变更日志插入到日志表user_logs中, 包含增加,修改,删除 ;
# A. 插入数据触发器
create trigger tb_user_insert_trigger
	after insert on tb_user for each row
begin
	insert into user_logs(id, operation, operate_time, operate_id, operate_params) VALUES (null, 'insert', now(), new.id, concat('插入的数据内容为: id=',new.id,',name=',new.name, ', phone=', NEW.phone, ', email=', NEW.email, ', profession=', NEW.profession));
end;

# 测试
-- 查看
show triggers ;
-- 插入数据到tb_user
insert into tb_user(id, name, phone, email, profession, age, gender, status, createtime) VALUES (26,'三皇子','18809091212','erhuangzi@163.com','软件工程',23,'1','1',now());
-- 测试完毕之后，检查日志表中的数据是否可以正常插入，以及插入数据的正确性。



# B. 修改数据触发器
create trigger tb_user_update_trigger
	after update on tb_user for each row
begin
	insert into user_logs(id, operation, operate_time, operate_id, operate_params) VALUES (null, 'update', now(), new.id, concat('更新之前的数据: id=',old.id,',name=',old.name, ', phone=',
old.phone, ', email=', old.email, ', profession=', old.profession, ' | 更新之后的数据: id=',new.id,',name=',new.name, ', phone=', NEW.phone, ', email=', NEW.email, ', profession=', NEW.profession));
end;

-- 测试
-- 查看
show triggers ;
-- 更新
update tb_user set profession = '会计' where id = 23;
update tb_user set profession = '会计' where id <= 5;
-- 测试完毕之后，检查日志表中的数据是否可以正常插入，以及插入数据的正确性。



# C. 删除数据触发器
create trigger tb_user_delete_trigger
	after delete on tb_user for each row
begin
	insert into user_logs(id, operation, operate_time, operate_id, operate_params) VALUES (null, 'delete', now(), old.id, concat('删除之前的数据: id=',old.id,',name=',old.name, ', phone=', old.phone, ', email=', old.email, ', profession=', old.profession));
end;

-- 测试
-- 查看
show triggers ;
-- 删除数据
delete from tb_user where id = 26;
-- 测试完毕之后，检查日志表中的数据是否可以正常插入，以及插入数据的正确性。
```





