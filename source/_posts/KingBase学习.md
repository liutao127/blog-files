---
title: KingBase学习
date: 2023-03-12 15:29:20
tags: KingBase
---

# 1. 单表查询

<!-- more -->

## 1. DQL语言的语法

| 参数          | 简介                                                         |
| ------------- | ------------------------------------------------------------ |
| SELECT        | 主动作关键字，可以对表执行投影和送择操作。                   |
| DISTINCT      | 用于对结果集去掉重复记录。                                   |
| *             | 代表查询表中的所有列。                                       |
| WHERE         | 指定查询条件，只返回条件为 true的记录。<br />条件表达式用于各种数据类型的字段值的相关比较。<br />常用的条什表达式操作符有>、<、>-、<-、<>(!-)、BETWEEN AND、IN、EXISTS、LIKE、ISNULL。<br />逻辑表达式用于结合或者限制一个或多个条件表达式。<br />常用的逻辑表达式操作符有NOT、AND、OR。 |
| GROUP BY      | 用于对满足条件的记录按指定列执行分组聚合运算。               |
| HAVING        | 用于对分组聚合后的结果集进行筛选，只返回条件为true的记录。   |
| ORDER BY      | 用于对结果集进行排序。<br />支持升序(ASC)、降序(DESC)、多列组合排序。 |
| LIMIT         | 用于返回结果集的前N行（TOP N查询）。                         |
| OFFSET..FETCH | 用于分页查询。                                               |
| FOR           | 用于独占方式查询表。                                         |

## 2. SELECT子句

### 1. 查询所有行和所有列

```mysql
select * from exam.course;
```

### 2. 查询部分列

```mysql
select son AS "学员编号",sname AS "学员姓名" from exam.student;
```

### 3. 单引号和双引号

```mysql
# 1、例如，针对有空格或者特殊字符或者以数字开头的字段别名必须加双引号。
# dual 是 kingbase 数据库中的虚拟表
select 1+1 as "1+1=?" from dual;
# 结果
# 1+1=?
# -----------
# 2
# (1 row)

# 2、例如，针对标量字符串表达式必须用加单引号。
select 'hello,Kingbase!' as Hello from dual;
# 结果
# Hello
# ------------
# Hello,Kingbase!
# (1 row)
```

### 4. 连接运算

```mysql
# 1、字符串的拼接运算。
# 比如，将多列拼接成一个长的字符串、将字符串与列拼接成一个长的字符串。
select iname||'的职称是'||title AS '讲师信息' from exam.instructor;
# 结果
# 讲师信息
# ------------------------
# Li Nan的职称是Senior Instuctor
# Zhang San的职称是Intermediate Instructor
# Ma Tao的职称是Intermediate Instructor
# (7 orws)

# 2、字符串拼接经常用于生成SQL脚本。
# 比如，要删除exam模式下的所有表,可以通过拼接生成如下批量的sql。
select 'DROP TABLE '||schemaname||'.'||tablename||';' AS 'batch_sql' from sys_tables where schemaname=' exam';
# 结果
# batch_sql
# ----------------------
# DROP TABLE exam.instructor_history;
# DROP TABLE exam.course;
# DROP TABLE exam.score;
# DROP TABLE exam.student;
# (5 rows)
```

### 5. 算术运算符

```mysql
# 1、数值的算术运算
select sno,score,score+1 AS new_score from exam.score;
# 结果
# sno  |  score  |  new_score
# ----------------------------------
# 1001 |   85    |    86
# 1001 |   88    |    89
# 1001 |   83    |    84
# 1002 |   94    |    95
# 1002 |   86    |    87
# 1002 |   97    |    98
# (15 rows)

# 2、日期的算术运算
select current_date()+7 AS nextweek from dual;
# 结果
# nextweek
# -------------
# 2021-11-19
# (1 row)
```

### 6. 使用条件表达式

```mysql
# 比如，输出成绩信息，成绩大于等于90时输出“高”，成绩大于等于70并且小于90时输出“中”，其它的输出“低”，可以使用条件表达式case when来实现。
select sno,cno,score,case when score>=90 then '高' when score<90 and score >= 70 then '中' else '低' END AS score_level from exam.score;
# 结果
# sno  |  cno  |  score  |  score_level
# -----------------------------------------
# 1001 |   10  |   85    |  中
# 1001 |   20  |   88    |  中
# 1001 |   30  |   83    |  中
# 1002 |   10  |   95    |  高
# 1002 |   20  |   86    |  中
# 1002 |   30  |   68    |  低
# (15 rows)
```

## 3. WHERE子句

### 1. 等值查询

```mysql
# 1、数值型字段条件匹配。
# 比如，查找分数大于80学员信息。
select sno,cno,score from exam.score where score>80;

# 2、字符型字段条件匹配（等值匹配）。
# 比如，要想查看学员所在城市为Beijing的学员。
select sno,sname,city from exam.student where city='Beijing';

# 3、字符型字段条件匹配(列表匹配）。
# 比如，要想查看学员所在城市为Beijing或者Chongqing 的学员。
select sno,sname,city from exam.student where city in('Beijing','Chongqing');
```

### 2. 多条件匹配

```mysql
# 比如，查找所在城市为为Beijing，工作岗位是Database Engineer的学员信息。
select sno,sname,city,job from exam.student where city='Beijing' and job='Database Engineer';
```

### 3. 逻辑运算

```mysql
# 逻辑运算包含与、或、非，分别使用逻辑运算符AND、OR、NOT表示。
# 1、逻辑运算符的优化级为NOT>AND>OR。
# (1）证明AND优先级高于OR的优先级。
select 1 from dual where 1=1 OR 1=0 AND 1=0;
# 结果:  1 (1 row)
# 说明 where 条件为 true , 那么说明优先进行了AND运算, 即 AND > OR

# (2）证明NOT优先级高于AND优先级。
select 1 from dual where NOT 1=0 AND 1=0;
# 结果:  (0 row)
# 说明 where 条件为 false , 那么说明优先进行了NOT运算, 即 NOT > AND

#2、可以使用括号改变优先级。
# 以下两条语句输出结果不同，说明小括号提升了OR的优先级。
select 1 from dual where 0=9 AND (0=1 OR 1=1);
# 结果: (0 row)
select 1 from dual where 0=9 AND 0=1 OR 1=1;
# 结果 1 (1 row)

# 结论: NOT > AND > OR
```

### 4. 模糊查询

```mysql
# 在查询条件中无法确认完整的条件值时，可以使用模糊查询。模糊查询操作符为like,在匹配值域中可使用通配符%和_，其中“%”代表匹配任意个字符，“_”代表匹配一个字符。
# 1、查询姓名以S字符打头的学员信息。
select sno,sname,city from exam.student where sname like 'S%';

# 2、查询姓名以g字符结尾的学员信息。
select sno,sname,city from exam.student where sname like '%g';

# 3、查找姓名中第二个字符必须为i的学员信息。
select sno,sname,city from exam.student where sname like '_i%';
```

### 5. 范围查询

```mysql
# 比如，查找在2021年7月1日到9月1日参与培训的学员信息。
select sno,sname,reg_date from exam.student where reg_date BETWEEN '2021-07-01' AND '2021-09-01';
```

### 6. 空值查询

```mysql
# 比如，查找工作单位为空的学员信息。
select sno,sname,company from exam.student where company IS null;
# 结果
# sno  |  sname    | company
# ------------------------------
# 1007 |  Fen Xiao |  (这里的结果就是空的!啥都没)
# (1 row)   
```

## 4. ORDER BY 子句

### 1. 升序排列 ASC

```mysql
# 比如，按注册日期升序排列。
# 1、缺省时,ASC为默认选项。
select sno,sname,reg_date from exam.student ORDER BY reg_date;
# 结果是通过 reg_date 进行升序排列的;

# 2、使用 ASC 关键字。
select sno,sname,reg_date from exam.student ORDER BY reg_date ASC;
# 结果是通过 reg_date 进行升序排列的;
```

### 2. 降序排列 DESC

```mysql
# 比如，按注册日期降序排列。
select sno,sname,reg_date from exam.student ORDER BY reg_date DESC;
```

### 3. 引用字段别名排序

```mysql
# 比如，按照姓名排序。
select sname AS name,gender,phone from exam.student ORDER BY name;
# 这里的排序字段是按照sname的别名(AS) --> name;
```

### 4. 引用字段顺序号排序

```mysql
# 比如，按注册日期升序排列。
select sno,sname,reg_date from exam.student ORDER BY 3;
# 这里的 ORDER BY 3, 意思是按照第三列(reg_date)来排序的;
```

### 5. 多列组合排序

```mysql
# 比如，先按所在城市升序排列，同一个城市中再按注册日期降序排列。
select sno,sname,reg_date,city from exam.student ORDER BY city, reg_date DESC;
```

## 5. 限制返回的行数

### 1. 限制返回的行数

```mysql
# 1、使用limit子句。
select sno,sname,city from exam.student LIMIT 3;
# 2、使用子查询。
select * from (select rownum rn,sno,sname,city from exam.student) where rn<=3;
```

### 2. 分页查询

**作用:在一些应用窗口中执行数据检索遇到返回结果集有较多记录时，往往需要利用SQL分布询显示部分记录，以提升数据检索的响应速度和减少网络传输压力，可使用offset fetch来实现。**

```mysql
select sno,sname,city from exam.student OFFSET 3 ROWS FETCH NEXT 3 ROWS ONLY;
```

| 关键字       | 说明                                             |
| ------------ | ------------------------------------------------ |
| offset       | 表示排除结果集前面的N行记录, 即从 N+1 行开始返回 |
| fetch        | 表示从 N+1 行开始总共要返回的记录数              |
| next / first | 没有实际差异, 都表示返回的行数                   |
| row / rows   | 没有实际差异, 为了构建更清晰的语法而已           |

### 3. DISTINCT 关键字

```mysql
# 1、查看student表中的城市的数量（含重复值）。
SELECT count(city)FROM student;
# 2、查看student表中的城市的数量（去掉重复值)。
select count(DISTINCT city) from student;
```

# 2. 多表查询

## 1. 多表查询的用途

1. 多表查询能够执行跨表数据检索，常用于报表和统计分析。
2. 用于连接查询的多张表中具有逻辑相关性的列，可用于连接查询条件。
3. 连接查询的分类。

**连接查询的分类**

1. 按照结果集输出方式分为内连接查询、外连接查询、交叉连接查询。
2. 按照连接条件的比较运算符分为等值连接查询、非等值连接查询。
3. 其他特殊连接方式有自然连接、自连接。

## 2. 内连接

### 1. 概述

**内连接根据两个连接表的同名列或逻辑相关列的值进行比较，返回与连接条件匹配的行数据。如下图所示，内连接只显示两张表匹配的行。**

{% asset_img  内连接-概述.png %}

### 2. 语法大纲

```mysql
T1 {[ INNER ]} JOIN T2 ON boolean_expression
T1 {[ INNER ]} JOIN T2 USING ( join column lsit )
# 注意
# (1)INNER 是可选的
# (2)INNER 是缺省值
```

### 3. 部分参数简介

| 参数       | 简介                                                         |
| ---------- | ------------------------------------------------------------ |
| INNER JOIN | 对于T1表的每一行R1，生成的连接表都有一行对应T2表中的每一个满足和Rl的连接条件的行。 |
| ON         | 0N子句是最常见的连接条件的形式:它接受一个和WHERE子句里用的一样的布尔值表达式。如果两个分别来自T1和T2的行在ON表达式上运算的结果为真，那么它们就算是匹配的行。 |
| USING      | USING是个缩写符号，它允许你利用特殊的情况:连接的两端都具有相同的连接列名。它接受共享列名的一个逗号分隔列表，并且为其中每一个共享列构造一个包含等值比较的连接条件。例如用USING (a， b)连接T1和T2会产生连接条件ON T1.a = T2.a AND T1.b = T2.b。<br/>更进一步，JOIN USING 的输出会废除冗余列:不需要把匹配上的列都打印出来，因为它们必须具有相等的值。不过JOIN ON 会先产生来自T1的所有列，后面跟上所有来自T2的列;而JOIN USING 会先为列出的每一个列对产生一个输出列，然后先跟上来自T1的剩余列，最后跟上来自T2的剩余列。 |

## 3. 外连接

### 1. 概述

(1) 左外连接的结果集包括LEFT OUTER子句中指定的左表的所有行，而不仅仅是连接列所匹配的行。如果左表的某行在右表中没有匹配行，则在相关联的结果集行中来自右表的所有列均为空值。如下图所示，左外连接显示左右两表匹配的行和左表中与右表不匹配的行。

{% asset_img  外连接-概述1.png %}

(2) 右外连接是左向外连接的反向连接。将返回右表的所有行。如果右表的某行在左表中没有匹配行，则在相关联的结果集行中来自左表的所有列均为空值。如下图所示，右外连接显示左右两表匹配的行和右表中与左表不匹配的行。

{% asset_img  外连接-概述2.png %}

(3) 全外连接返回左表和右表中的所有行。当一个表中的某行在另一个表中没有匹配行时，则在相关联的结果集行中来自另一个表的所有列返回空值。如果表之间有匹配行，则整个结果集行包含基表的数据值。如下图所示，全外连接显式两表匹配的行和不匹配的行。

{% asset_img  外连接-概述3.png %}

### 2. 语法大纲

```mysql
T1 { { LEFT | RIGHT | FULL } [OUTER] } JOIN T2 ON boolean_expression
T1 { { LEFT | RIGHT | FULL } [OUTER] } JOIN T2 USING ( join column list )
# 注意
# OUTER 是可选的
# LEFT RIGHT FULL 指示外连接类型
```

### 3. 部分参数简介

| 参数             | 简介                                                         |
| ---------------- | ------------------------------------------------------------ |
| LEFT OUTER JOIN  | 首先，执行一次内连接。然后，为T1中每一个无法在连接条件上匹配T2里任何一行的行返回一个连接行，该连接行中属于T2的列用空值补齐。因此，生成的连接表里为来自T1的每一行都至少包含一行。 |
| RIGHT OUTER JOIN | 首先，执行一次内连接。然后，为T2中每一个无法在连接条件上匹配T1里任何一行的行返回一个连接行，该连接行中属于Tl 的列用空值补齐。因此，生成的连接表里为来自T2的每一行都至少包含一行。 |
| FULL OUTER JOIN  | 首先，执行一次内连接。然后，为Tl中每一个无法在连接条件上匹配T2里任何一行的行返回一个连接行，该连接行中属于T2的列用空值补齐。同样，为T2中每一个无法在连接条件上匹配Tl里任何一行的行返回一个连接行，该连接行中属于Tl的列用空值补齐。 |

## 4. 交叉连接

### 1. 概述

交叉连接返回左右两表基于元组的笛卡尔积

{% asset_img  交叉连接-概述.png %}

### 2. 语法大纲

```mysql
T1 CROSS JOIN T2
```

### 3. 部分参数简介

| 参数       | 简介                                                         |
| ---------- | ------------------------------------------------------------ |
| CROSS JOIN | 对来自于T1和T2的行的每一种可能的组合(即笛卡尔积)，连接表将包含这样一行:它由所有T1里面的列后面跟着所有T2里面的列构成。如果两个表分别有M和N行，连接表将有M*N行。 |

## 5. 等值连接与非等值连接

### 1. 等值连接

  连接条件中的比较运算符为" = "

### 2. 非等值连接

  连接条件中的比较运算符为非" = " , 比如 >= ,  > , <= , < , <> , between ... and ... 等

## 6. 自然连接与自连接

### 1. 自然连接

#### 1. 概述

  自然连接是等值连接的一种特殊形式，使用NATURAL关键字代替连接条件，通过两张或者多张表中所有具有相同名称的字段进行等值连接。多个同名字段连接时，逻辑运算符为AND。在自然连接中遇到关联表中的同名字段具有不匹配的或不可隐式转换的数据类型时，会导致执行出错.

#### 2. 语法大纲

```mysql
I1 NATURAL { [INNER] | { LEFT | RIGHT | FULL } [OUTER] } JOIN T2
```

#### 3. 部分参数简介

| 参数    | 简介                                                         |
| ------- | ------------------------------------------------------------ |
| NATURAL | NATURAL是USING的缩写形式:它形成一个USING列表，该列表由那些在两个表里都出现了的列名组成。和USING一样，这些列只在输出表里出现一次。如果不存在公共列，NATURAL 的行为将和CROSS JOIN ( 交叉连接 \| 笛卡尔积 )一样。 |

### 2. 自连接

  通过为表设置别名将一张表虚拟成两张表后进行连接操作，常用于一张表内列之间具有键值依赖的数据检索。

# 3. 表的定义

## 1. 表的基本概念

1. 在KES中表也称为关系，是从现实中抽象出来的一个关系对象，比如员工表、部门表。
2. 表是由行和列组成二维对象，列代表关系的一个属性，行代表属于关系的一个实体。
3. 数据类型用来定义表中字段所属的类型，必须为每个列指定数据类型。

## 2. 创建普通表

### 1. 概述

  创建表的时候，需要指定表名、列名、列的数据类型、列数据精度、列的缺省值、表的约束等。

### 2. 语法大纲

```mysql
CREATE [ UNLOGGED ] TABLE [ IF NOT EXISTS ] table_name ([
    { column_name data_type [ COLLATE collation ] [ column_constraint [...] ] | table_constraint | LIKE source_table [ like_option ... ] }
    [, ...] ])
[ WITH ( storage_parameter [= value] [, ... ] ) | WITH OIDS | WITHOUT OIDS ]
[ TABLESPACE tablespace_name ]
# 注意: KES 支持创建不带任何列的一张空表
```

### 3. 部分参数简介

| 参数名称          | 简介                                                         |
| ----------------- | ------------------------------------------------------------ |
| TEMPORARY \| TEMP | 表示该表是临时表                                             |
| UNLOGGED          | 指定该表在修改时不会在WAL日志中保存事务日志                  |
| INHERITS          | 创建分区表时会使用, 在子表和它的父表之间创建一种持久的关系   |
| WITH              | 创建表时指定参数, 例如: FILLFACTOR \ OIDS 等                 |
| ON COMMIT         | 指定临时表在一个事务结束时的行为                             |
| TABLESPACE        | 创建表时指定表所存放的表空间                                 |
| column_constraint | [ CONSTRAINT constraint_name ]<br />{ NOT NULL \| NULL \| CHECK ... \| DEFAULT ... \| UNIQUE ... \| PRIMARY KEY ... \| REFERENCES ... [ ON DELETE action ] [ ON UPDATE action ] }<br />[ DEFERRABLE \| NOT DEFERRABLE ] [ INITIALLY DEFERRED \| INITIALLY IMMEDIATE ] |
| table_constraint  | [ CONSTRAINT constraint_name ]<br />{ CHECK ... \| UNIQUE ... \| PRIMARY KEY ... \| EXCLUDE ... \| FOREIGN KEY ...  }<br />[ DEFERRABLE \|  NOT DEFERRABLE] [ INITIALLY DEFERRED \| INITIALLY IMMEDIATE ] |
| like_option       | { INCLUDING \| EXCLUDING }<br />{ DEFAULTS \| CONSTRAINTS \| INDEXES \| STORAGE \| COMMENTS \| ALL } |

## 3. 修改表

### 1. 概述

  使用ALTER TABLE 命令可以修改表名, 列名, 列的精度, 列的默认值, 维护约束, 更改表空间等. 当表中存在记录时, 相关修改要符合现有数据的要求, 否则可能修改失败或者导致数据被截取.

### 2. 语法大纲

```mysql
ALTER TABLE [ IF EXISTS ] [ ONLY ] name [ * ] action [, ... ]
ALTER TABLE [ IF EXISTS ] [ ONLY ] name [ * ] RENAME [ COLUMN ] column_name TO new_column_name
ALTER TABLE [ IF EXISTS ] [ ONLY ] name [ * ] RENAME CONSTRAINT constraint_name TO new_constraint_name
ALTER TABLE [ IF EXISTS ] name RENAME TO new_name
ALTER TABLE [ IF EXISTS ] name SET SCHEMA new_schema
ALTER TABLE ALL IN TABLESPACE name [ OWNED BY role_name [, ... ] ] SET TABLESPACE new_tablespace [ NOWAIT ]
```

### 3. 部分参数简介

| 参数            | 简介                                                         |
| --------------- | ------------------------------------------------------------ |
| ADD COLUMN      | 新增一个列, 新列将被默认值所填充, 如果没有指定 DEFAULT 子句, 则会填充空值 |
| DROP COLUMN     | 移除一个列, 列中的数据将会消失, 涉及到该列的表约束也会被移除 |
| ADD CONSTRAINT  | 增加一个约束, 与表约束的语法相同                             |
| DROP CONSTRAINT | 移除一个约束, 可以用 ' \d 表名 ' 方式获取约束的名称          |
| SET TABLESPACE  | 移动表到其他表空间                                           |
| OWNER TO        | 修改表的拥有者                                               |

## 4. 删除表

### 1. 概述

1. DROP TABLE命令会将表的定义、表的依赖对象、表的数据同时从数据库中删除。
2. 只有表的拥有者、模式拥有者和超级用户能对表执行删除操作。
3. RESTRICT关键字表示如果有对象依赖于该表，则拒绝删除该表，这是默认选项。
4. 要删除一个被外键约束参照引用的表，需要指定CASCADE关键字（只级联删除外键约束，而不会删除原外键表中的数据行）。

### 2. 语法大纲

```mysql
DROP TABLE [ IF EXISTS ] name [ , ... ] [ CASCADE | RESTRICT ]
```

### 3. 部分参数简介

| 参数     | 简介                                             |
| -------- | ------------------------------------------------ |
| RESTRICT | 当有外键约束时删除主表会失败                     |
| CASCADE  | 当有外键约束时删除主表会连同字表中的数据一起删除 |

## 5. 截断表

### 1. 概述

1. TRUNCATE TABLE命令会清空表中的数据，保留表的定义，立即回收表占用的磁盘空间。
2. KES在执行TRUNCATETABLE时会是一个能够快速消空资科表内所有资料的SQL语句，TRUNCATE语句的执行效率要高于delete语句。

### 2. 语法大纲

```mysql
TRUNCATE [ TABLE ] [ ONLY ] name [ * ] [ , ... ] [ RESTART IDENTITY | CONTINUE IDENTITY ] [ CASCADE | RESTRICT ]
```

### 3. 参数简介

| 参数              | 简介                                               |
| ----------------- | -------------------------------------------------- |
| RESTART IDENTITY  | 自动重新开始被截断表的序列                         |
| CONTINUE IDENTITY | 默认不改序列值                                     |
| RESTRICT          | 默认, 表有外键引用则阻止记录被清空                 |
| CASCADE           | 存在主外键关联时, 清空父表时子表关联的行被级联清空 |

## 6. 创建表 --- 例子

### 1. 常规语法创建表

```mysql
# 1. 创建t01表
# (1)表包含id、name、location三个列，类型分别为serial、text、text。
# (2)表存储在sys_default表空间（即默认表空间）。
# (3)使用“\d”命令查看t01表的定义信息。
create table public.t01(id serial,name text,location text);
\d public.t01

# 2. 创建t02表
# (1)表包含code、title、did、date_prod、kind、len字段，类型分别为char、varchar、integer、date、varchar、interval hour to minute,其中 title和 did字段不允许为空。.
# (2）表存储在sys_default表空间（即默认表空间）。
# (3）使用“\d”命令查看t02表的定义信息。
create table t02(code char(5),title varchar(40) NOT NULL,did integer NOT NULL,date_prod date,kind varchar(10),len interval hour to minute);
# 注意: 这里不用 ' public. ' 也可以, 因为他会自己去public文件夹去找
\d t02 
```

### 2. 使用子查询来创建表

```mysql
# 1. 通过复制student表来创建student_m表，只复制原表中部分数据到新表中。
# 注意: 使用子查询创建表时会复制表的字段名和表数据等信息到新表中。
create table student_m AS select * from exam.student where gender='m';
\d student_m

# 2. 通过复制course表来创建course01表, 复制原表中全部数据到新表中。
create table course01 AS table exam.course;
\d course01
```

### 3. 使用 like 语法来创建表

```mysql
# 1. 使用like创建表, 非空约束会默认复制到新表中
# 例如: 根据t02表新建t03表, 并查看t03表的信息
create table t03 ( like t02 including defaults including constraints including indexes );
# 注意: 使用like语法创建表时, 不会复制原表的数据到新表中
```

## 7. 修改表 --- 例子

### 1. 重命名表名, 列名, 约束名

```mysql
# 1. 修改course01表的名字为courseOLD
alter table course01 rename to courseOLD;

# 2. 修改courseOLD表的cname列的名字为cno_name
alter table courseOLD rename cname to cno_name;

# 3. 修改exam.soure表的外键约束cno_fk的名字为course_con_fk
alter table exam.score rename constraint cno_fk to course_con_fk;
```

### 2. 修改表的模式

**移动表(包含该表拥有的索引, 约束和序列)到另一个模式下, 必须由该表的拥有者或者超级用户执行, 同时执行者必须拥有新模式上的create特权.**

```mysql
# 1. 移动exam.score表到新建的sch01模式下
alter table exam.score set schema sch01;
```

### 3. 移动表到其他表空间

```mysql
# 1. 新建表空间 tbs01 和 tbs02
# (1) 创建表空间所使用的目录并设置目录的属主, 属组和文件系统权限
# (2) 创建两个表空间分别指向对应的目录
# 2. 查看待移动表的原始空间, 然后迁移表到表空间 tbs01 中
select table_name, tablespace_name from dba_tables where table_name IN ('course', 'student');
# 结果 (2 rows)
alter table exam.course set tablespace tbs01;
alter table exam.student set tablespace tbs01;
select table_name, tablespace_name from dba_tables where table_name IN ('course', 'student');
# 结果 (2 rows)
# 3. 将tbs01表空间中的所有表全部迁移到表空间 tbs02
alter table ALL IN tablespace tbs01 set tablespace tbs02;
select table_name, tablespace_name from dba_tables where table_name IN ('course', 'student');
# 结果 (2 rows)
```

### 4. 修改表的其他定义

```mysql
# 1. 在表 t02 中新加列 address , 类型为 varchar , 精度为 30
alter table t02 add column address varchar(30);

# 2. 删除表 t02 中的 address 字段
alter table t02 drop column address;

# 3. 修改表 t02 的部分字段类型
alter table t02 alter column did type number(10. 2). alter column kind type varchar(20);
```

## 8. 截断表 --- 例子

```mysql
# 1. 使用 TRUNCATE 命令清空 courseold 表中的数据
select count(*) from courseold;
# count
# ------------
#   3
# (1 row)
truncate courseold
select count(*) from courseold;
# count
# -------------
#   0
# (1 row)
```

## 9. 删除表 --- 例子

```mysql
# 删除 t02 表
drop table if exists t02;
```

## 10. 临时表

### 1. 会话级临时表

#### 1. 概述

  默认情况下创建的临时表为会话级临时表．只在创建临时表的会话中可使用，表结构在整个会话周期中一直保留。不同的会话可以创建同名的临时表。
  可以使用ON COMMIT PRESERVE ROWS（默认，事务结束时保留临时表记录)或者ON COMMIT DELETEROWS（事务结束时清除临时表记录）关键字指定事务结束后临时表中数据是否被清除。

#### 2. 示例

```mysql
create temporary table temp01(id int primary key,name varchar(20)) on commit preserve rows;
create temporary table temp02(id int primary key,name varchar(20)) on commit delete rows;
```

### 2. 事务级临时表

#### 1. 概述

  使用ON COMMIT DROP 关键字创建事务级临时表, 表的创建和使用需要放在同一个事务中, 事务结束后表结构和表中数据都会被删除.

#### 2. 示例

```mysql
create temporary table temp03(id int primary key,name varchar(20)) on commit drop;
# 注意:
# 1. 在 KES 中无论是会话级级别还是事务级别的临时表, 当会话结束时表结构都会消失
# 2. 在创建临时表时会自动自动为其分配一个模式(SYS_TEMP_xx) , 不能在创建临时表时指定模式
```

## 11. 小结

### 1. KES支持的数据类型

**主要有以下几大类**

| 数值类型        | 几何类型     | 位串类型 | 域类型              |
| --------------- | ------------ | -------- | ------------------- |
| 字符类型        | 货币类型     | UUID类型 | 位类型              |
| 二进制类型      | 枚举类型     | XML类型  | 范围类型            |
| 日期 / 时间类型 | 数组类型     | JSON类型 | 全文检索类型        |
| 布尔类型        | 网络地址类型 | 复合类型 | 对象标识符类型(oid) |

### 2. 常用数据类型说明

| 类型              | 长度   | 变长 / 定长      | 取值范围                                                     |
| ----------------- | ------ | ---------------- | ------------------------------------------------------------ |
| int               | 4字节  | 常用的整数       | -2147483648 到 +2147483647                                   |
| serial            | 4字节  | 自增型           | 1 到 +2147483647                                             |
| decimal           | 变长   | 用户自定义精度   | 无限制                                                       |
| numeric 或 number | 变长   | 用户自定义精度   | 无限制, 与 decimal 等效                                      |
| char(n)           | 自定义 | 定长, 不足补空白 | 用于定义固定长度的字符串<br />n为精度,单位为byte或char, 默认是char |
| varchar(n)        | 自定义 | 变长, 用户定义   | 用于定义不定长度的字符串                                     |
| text              | 不固定 | 变长, 无长度限制 | 用于定义位置长度的大文本                                     |
| date              | 4字节  | 日期             | 精度到天                                                     |
| time              | 8字节  | 时间             | 精度毫秒                                                     |
| timestamp         | 8字节  | 包括日期和时间   | 精度毫秒, 1纳秒 / 17 位                                      |
| interval          | 12字节 | 时间间隔         | 精度毫秒                                                     |
| bytea             | 4字节+ | 二进制数据       | 用于存储图片等二进制数据                                     |

# 4. 表的约束

## 1. 主键约束

### 1. 创建表时定义主键约束

```mysql
# 1. 在列级定义主键约束, 创建t01表, 并在id列上创建主键约束
create table t01 (id integer primary key,name text,score numeric);
# 2. 在表级定义主键约束, 创建t01表, 并在id列上创建主键约束
create table t01 (
    id integer,
    name text,
    score numeric, 
    primary key(id)
);
```

### 2. 修改表时增加主键约束

```mysql
# 1. 创建t01表
create table t01 (
	id integer,
    name text,
    score numeric
);
# 2. 修改t01表, 为id列创建主键约束
alter table t01 add constraint pk_t01 primary key(id);
```

### 3. 验证主键约束

```mysql
# 1. 往t01表中插入2条id相同的数据
insert into t01 values(100, 'accounting1', 1800);
# 无报错
insert into t01 values(100, 'accounting2', 2000);
# 报错 -> 错误: 重复键违反唯一约束"pk_t01"
#		 键值 "(id)=(100)"已经存在
```

## 2. 外键约束

### 1. 创建外键约束

```mysql
# 1. 在被参照表中创建主键约束, 创建t01表, 并在id列上定义主键约束
create table t01(id integer primary key,name text,score numeric);
# 2. 在参照表中创建外键约束, 创建t02表, 并在no列上定义外键约束, 引用t01表的id主键列
create table t02(
	no integer constraint t02_fk references t01(id),
    name text
);
# 3. 验证外键约束, 往t01表中插入1行数据, 再往t02表插入2行数据, 其中1行数据违反外键约束
insert into t01 values(100, 'accounting1', 90);
insert into t02 values(100, 'accounting1');
insert into t02 values(200, 'accounting2');
# 报错 -> 错误: 插入或更新表 "t02" 违反外键约束 "t02_fk"
#		 键值对(no)=(200)没有在表 "t01" 中出现
```

### 2. 外键约束级联控制

```mysql
# 创建t02表, 在no列上创建主键约束
# 创建t01表, 在id列上创建外键约束, 参照t02表的no列并设置为级联控制
# 往t02表中插入1条记录, 往t01表中插入1条满足外键约束的记录
# 更新t02表的no字段(因为存在外键关联导致更新主键列失败)
# 删除t02表中的记录(因为子表中参照的外键设置为级联删除, 所以可以成功删除父表元组)
create table t02(no integer primary key,name text);
create table t01(id integer references t02(no) ON delete cascade, name text,score numeric);
insert into t02 values (100, 'accounting1');
insert into t01 values (100, 'accounting1', 90);
update t02 set no=200 where no=100;
# 报错 -> 错误: 在 "t02" 上的更新或删除操作违反了在 "t01" 上的外键约束 "t01_id_fkey"
#		 键值对(no)=(100)仍然是从表"t01"引用的
delete from f02 where no=100;
select * from t01 where id=100;
# 结果 (0 rows)

# 注意
# 存在外键依赖时, 不可以直接对父表的主键列执行更新操作
# 使用 ON DELETE CASCADE 选项可以在删除父表元素时实现级联删除子表元组
# 使用 ON DELETE SET NULL 选项可以在删除父表时实现级联更新子表元组的外键列为空值
```

## 3. 非空约束 not null

### 1. 创建表时定义非空约束

```mysql
# 创建t01表, 并在id列上创建非空约束
create table t01 (
	id integer not null,
    name text,
    score numeric
);
```

### 2. 修改表时增加非空约束

```mysql
# 修改t01表, 并在name列上创建非空约束
alter table t01 alter column name set not null;
```

### 3. 验证非空约束

```mysql
# 往t01表中插入2条数据, 其中1行数据违反非空约束
insert into t01 values(100, 'accounting1', 1800);
insert into t01 values(200, null, null);
# 报错 -> 错误: 在字段"name"中空值违反了非空约束
#		 失败, 行包含(200, null, null)
```

## 4. 唯一约束 unique

### 1. 创建表时定义唯一约束

```mysql
# 创建t01表, 并在id列上创建唯一约束
create table t01(
	id integer unique,
    name text,
    score numeric
);
```

### 2. 修改表时增加唯一约束

```mysql
# 修改t01表, 并在name列上增加唯一约束
alter table t01 add constraint uk_t01 unique(name);
```

### 3. 验证唯一约束

```mysql
# 往t01表中插入2条记录, 其中1行数据违反唯一约束
insert into t01 values(100, 'accounting', 95);
insert into t01 values(200, 'accounting', 90);
# 报错 -> 错误: 重复键违反唯一约束 "uk_t01"
# 		 键值"(name)=(accounting)"已经存在
```

## 5. CHECK约束

### 1. 创建表时定义CHECK约束

```mysql
# 创建t01表, 并在score列上创建check约束, 要求score取值必须大于0
create table t01(
    id integer,
    name text,
    score numeric check (score > 0)
);
```

### 2. 修改表时增加CHECK约束

```mysql
# 修改t01表, 并在score列上新增CHECK约束, 要求score取值必须小于100
alter table t01 add constraint t01_score_ck CHECK (score<100);
```

### 3. 验证CHECK约束

```mysql
# 往t01表中插入2条数据, 其中1行数据违反CHECK约束
insert into t01 values(100, 'accounting1', 90);
insert into t01 values(100, 'accounting2', 150);
# 报错: -> 关系 "t01" 的新列违反了检查约束 "t01_score_ck"
#		 失败, 行包含(100, accounting2, 150)
```

## 6. 约束的状态控制

### 1. enable + validate

```mysql
# 创建测试表t01,并为id列定义CHECK约束，要求id列值必须大于10。
# 插入一条无效的数据到t01表中（执行报错）。
# 通过数据字典表查看约束的信息。
create table t01(id int,name varchar(10));
alter table t01 add constraint t01_ck CHECK(id>10);
insert into t01 values(5, 'a');
# 报错 -> id必须 > 10
select constraint_name,status,validated from user_constraints where table_name='t01';
# 结果
# constraint_name | status | validated
# ----------------------------------------
#     t01_ck      | ENABLE | VALIDATED
# ( 1 row )
```

### 2. disable + novalidate

```mysql
# 修改t01表的t01_ck约束为disable novalidate。
# 插入一条无效的数据到t01表中（执行成功).
# 插入一条有效的数据到t01表中 (执行成功)
# 通过数据字典表查看约束的信息。
alter table t01 DISABLE NOVALIDATE constraint t01_ck;
insert into t01 values(2, '2');
insert into t01 values(20, '2');
select constraint_name,status,validated from user_constraints where table_name='t01';
# 结果
# constraint_name | status  | validated
# ---------------------------------------
#     t01_ck      | DISABLE | NOVALIDATED
# ( 1 row )
```

### 3. disable + validate

```mysql
# 调整约束t01_ck 为disable validate状态会失败。
# 删除违规数据。
# 调整约束t01_ck 为 disable validate状态成功。
# 对t01表执行DML操作，查看执行结果。
# 通过数据字典表查看约束的信息。
alter table t01 DISABLE VALIDATE constraint t01_ck;
# 错误: 一些行违反了检查约束"t01_ck"
delete from t01 where id<10;
alter table t01 DISABLE VALIDATE constraint t01_ck;
insert into t01 values(30, '30');
# 报错
update t01 set id = id+1;
# 报错
select constraint_name,status,validated from user_constraints where table_name='t01';
# constraint_name | status | validated
# ---------------------------------------
#      t01_ck     | DISABLE | VALIDATED
```

### 4. enable+novalidate

```mysql
# 禁用约束t01_ck,插入一条违反约束的记录到t01表中。
# 调整约束t01_ck 为enable novalidate状态。
# 插入一条违反约束的记录到to1表中。
# 插入一条正常的记录到to1表中。
alter table t01 DISABLE NOVALIDATE constraint t01_ck;
insert into t01 values(2, '2');
alter table t01 ENABLE NOVALIDATE constraint t01_ck;
insert into t01 values(1, '1');
# 错误: 关系"t01"的新列违反了检查约束"t01_ck"
insert into t01 values(13, '13');
```

## 7. 延迟约束

### 1. 创建延迟约束

```mysql
# 创建测试表t01,并在 id列上创建主键约束。
# 创建测试表t02,并为id列定义延迟外键约束，参照t01表的id列。
# 显式开启事务。
# 往子表t02中插入一条数据，执行成功。
# 往父表t01插入一条数据。
# 结束事务，自动提交成功。
# 通过数据字典表查看约束的信息。
create table t01(id int primary key,name varchar(10));
create table t02(id int constraint f02_fk references t01(id) initially deferred,name varchar(10));
insert into t02 values(20, 't02');
insert into t01 values(20, 't01');
select constraint_name,deferred from user_constraints where table_name='t02';
# 结果
# constraint_name | defferred
# -------------------------------
#     t02_fk      |  DEFERRED
# ( 1row )
```

### 2. 在事务中控制延迟约束

```mysql
# 修改t02表的 t02_fk 约束为非延迟。
# 显式开启事务，在事务中临时修改t02_fk 约束为延迟约束。
# 往子表t02中插入一条数据，执行成功。
# 往父表t01插入一条数据。
# 结束事务，自动提交成功。
alter table t02 alter constraint t02_fk INITIALLYT IMMEDIATE;
alter table t02 alter constraint t02_fk DEFERRABLE;
set constraints t02_fk deferred;
insert into t02 values(30, 't02');
insert into t01 values(30, 't01');
# 注意
# 延迟约束只能在事务中使用
# 提交事务时总会验证表中的数据是否满足约束,不满足时导致事务失败
```

## 8. 小结

### 1. 创建表并定义相关约束的一个完整例子

```mysql
# 学员表
create table student (
	sno numeric(4, 0),
    sname varchar(20),
    sex varchar(2),
    phone numeric(11, 0) not null,
    id_no varchar(18),
    city varchar(20),
    reg_time date,
    job varchar(20),
    company varchar(30),
    constraint sno_cno_pk primary key(sno),
    constraint phone_uk unique(phone),
    constraint id_no_uk unique(id_no),
    constraint sex_ck check(sex='m' or sex='f')
);
```

```mysql
# 课程表
create table course (
	cno numeric(2, 0),
    cname varchar(30) not null,
    clevel varchar(13),
    pass_mark numeric(5, 0) not null,
    constraint cno_pk primary key(cno),
    constraint pass_mark_ck check(pass_mark>0)
);
```

```mysql
# 讲师表
create table instructor(
	ino numeric(2, 0),
    iname varchar(20) not null,
    title varchar(20),
    constraint ino_pk primary key(ino)
);
```

```mysql
# 成绩表
create table score (
	cno numeric(2, 0),
	sno numeric(4, 0),
	ino numeric(2, 0),
	exam_time date not null,
    score numeric(5, 0),
    certificate boolean, -- certificate: 证书
    constraint score_pk primary key(cno,sno,ino,exam_time),
    constraint score_ck check(score>=0),
    constraint cno_fk foreign key(cno) references course(cno),
    constraint sno_fk foreign key(sno) references student(sno),
    constraint ino_fk foreign key(ino) references instructor(ino)
);
```

### 2. 使用约束时的一些注意事项

1. 一张表只能有一个主键约束，其它非主键列可以结合使用唯一约束+非空约束实现类似的功能。
2. check 约束的定义必须是一个条件表达式，表达式要有确定的结果，要么为trte，要么为false。
3. 我们将列的默认值定义也可以看做是一种定义在表上的约束，用于列的自动填充的缺省值。
4. 定义外键约束的列可以与被参照的父表的中的列拥有不同的名称，但列的数据类型必须要匹配。

# 5. 外部表

## 1. 外部表的概念

1. 外部表是一种虚拟表，直接在本地数据库中访问来自外部数据源的数据。
2. 数据本体是存在于外部数据源中的，该数据源甚至可能存在于另外一台物理服务器上。
3. 外部表支持映射到以下类型的数据源( 1. CSV文件.  2. KES数据库. )。

## 2. 数据库外部表

### 1. 手动创建外部表

```mysql
# 1. 安装扩展包
create extension kingbase_fdw;
# 2. 创建外部服务器
create server kingbase_server foreign data wrapper kingbase_fdw options (host '192.168.0.1', port '54321', dbname 'test');
# 3. 创建用户映射关系
# 3.1 所谓'用户映射'是指在远端服务器的某个角色和本地服务器的某个角色之间建立对应关系.
# 3.2 映射关系中的远端角色必须是一个已存在的角色, 并且有登录权限
create user mapping for public server kingbase_server options (user 'system', password 'kingbase');
# 4. 创建外部表
# 4.1 映射到远端数据库上的remote_public.student表
# 4.2 默认情况下, 映射到远端 KES 数据库的外部表都是可读写的
# 4.3 前提是映射关系中所使用的远端数据库角色对映射的远端表要有修改权限
create foreign table remote_public.student(
	sno numeric(4, 0),
    sname varchar(20),
    gender varchar(2),
    phone numeric(11, 0) not null,
    id_no varchar(18),
    city varchar(20),
    reg_date date,
    job varchar(30),
    company varchar(30)
) server kingbase_server options(schema_name 'public', table_name 'student');
\dE remote_public.*
# 5. 访问外部表
select * from remote_public.student;
# 6. 删除外部表
drop foreign table remote_public.student;
```

### 2. 自动创建外部表

1. import foreign schema 命令实现外部表的自动创建
2. 每个 FDW 在执行外部表结构导入时都可以设置一些服务端参数

| 参数            | 介绍                                                         |
| --------------- | ------------------------------------------------------------ |
| import_collate  | (1)是否将远端KES服务器上的字符排序规则设置也导入到本地外部表<br />(2)默认是 true |
| import_default  | (1)是否将远端KES服务器上的字段默认值属性也导入到本地外部表中<br />(2)默认是 false |
| import_not_null | (1)是否将远端KES服务器上的字段 NOT NULL 属性导入到本地外部表中<br />(2)默认是true |

3. 导入一个模式中的所有表结构

   (1) 远端 KES 服务器public模式下的表全部导入本地 remote public 模式中.

   (2) 本地表和远端表名称相同

   ```mysql
   import foreign schema public from server kingbase_server into remote_public options (import_default 'true');
   \dE remote_public.*
   ```

## 3. 小结

### 1. 外部表管理

```mysql
# 1. 更改你指向的服务器
alter server kingbase_server options (set host 'IP');
# 主机, 端口和database这几项的连接设置的更改只对新建立的会话生效, 不会影响已有的会话
# 2. 设置外部表为只读或读写模式
alter foreign table remote_public.student options (add updatable 'false');
alter foreign table remote_public.student options (set updatable 'true');
# 3. 更改映射字段, 添加或删除列
alter foreign table remote_public.student drop column sno;
```

### 2. 导入部分表

```mysql
# 1. LIMIT TO 和 EXCEPT 子句
# 1.1 LIMIT TO 用于指定哪些表要导入
# 1.2 EXCEPT 用于指定哪些表不要导入
# 注意: EXCEPT 与 LIMIT TO 语法类似, 效果相反
# 2. LIMIT TO 只导入student和course这两张表
# 3. LIMIT TO 后面指定的表在远端KES服务器上不存在, 系统会直接忽略掉, 不会报错
import foreign schema public limit to (student, course) from server kingbase_server into remote_public;
import foreign schema public except (student, course) from server kingbase_server into remote_public;
```

# 6. 分区表

## 1. 为啥要用分区表

1.  提升SQL运行效率。
   (1) 通常情况下，查询一张大表时会变慢。比如一张100G的销售表，要统计某个省范围内订单的平均销售额度，可能消耗几分钟的时间。
   (2) 如果使用分区表，查询数据时只需扫描特定的分区，可大大降低扫描时间，提高查询速度。
2. 具有更高的可用性。
   (1) 俗话说的好，不要将所有的鸡蛋都放到一个篮子中，对于数据库而言也是如此。比如，一个数百GB的表，当某一个数据页面损坏引起整个表不可用时，必会引起更严重的后果。
   (2) 分区表具有物理分散、逻辑统一的特性，更容易实现类似切片方式的分散存储管理。

## 2. 怎么创建分区表

1. 继承式分区

   KES V7及之前的版本提供了一种“手动”使用分区表的方式，需要利用“继承+触发器”的来实现分区表，步骤较为繁琐，需定义父表、子表、子表约束、创建子表索引，在主表上创建触发器等。

2. 声明式分区

   KES V8R6开始支持使用声明式创建（CREATE TABLE ... PARTITION BY...)分区表。父表默认是不存储任何数据的，只用于分区表定义和对象引用。

## 3. 三种分区策略

1. Range分区

   表数据根据条件被划分多个“范围”，分配给不同分区的值的范围之间没有重叠。例如: 可以按日期范围或特定业务对象的标识符范围来进行分区，如果在内置分区中使用range时支持interval分区。

2. List分区

   通过显示列出键值来对表进行分区, 常用于大表中键值不是特别多的情况

3. Hash分区

   通过为每个分区执行模数和余数来对表进行分区. 每个分区将保存行, 分区键的哈希值除以指定的模数将产生指定的余数.

## 4. 声明式创建分区表

### 1. PARTITION BY LIST

```mysql
# 将学员表student按所在城市使用partition by list 创建分区
# 1. 创建student分区表
create table public.student(
	sno numeric(4,0),
    sname character varying(20 char),
    gender character varying(2 char),
    phone numeric(11,0),
    id_no character varying(18 char),
    city character varying(20 char),
    reg_date date,
    job character varying(30 char),
    company character varying(30 char)
) partition by list(city);
# 2. 创建子分区
# 下面的例子创建4个分区，将城市为Beijing 和 Shanghai的数据存入student_p1分区，将城市为Tianjin 和 Guangzhou的数据存入student_p2分区﹐将城市为Chongqing 和 Chengdu的数据存入student_p3分区，其它的数据存入student_p4默认分区。
create table public.student_p1 partition of student for values in ('Beijing', 'Shanghai');
create table public.student_p2 partition of student for values in ('Tianjin', 'Guangzhou');
create table public.student_p3 partition of student for values in ('Chongqing', 'Chengdu');
create table public.student_p4 partition of student default;
# 3. 插入测试数据, 执行数据查询, 查看SQL执行计划
insert into public.student select * from exam.student;
explain select * from public.student;
# 结果 ( 800 rows )
explain select * from public.student where city='Chongqing';
# 结果 ( 2 rows )
```

### 2. PARTITION BY RANGE

```mysql
# 将学员表student按报名时间使用partition by range 创建分区表
# 1. 创建student分区表
create table public.student(
	#....(同上)
) partition by range (reg_date);
# 2. 创建子分区
create table public.student_p1 partition of student for values from ('2021-01-01') TO ('2021-03-31');
create table public.student_p2 partition of student for values from ('2021-04-01') TO ('2021-06-31');
create table public.student_p3 partition of student for values from ('2021-07-01') TO ('2021-09-31');
create table public.student_p4 partition of student default;
explain select * from public.student where reg_date between '2021-02-01' and '2021-02-28';
```

### 3. PARTITION BY HASH

```mysql
# 将学员表student按学员编号使用PARTITION BY HASH创建分区表
# 1. 创建student分区表
create table public.student(
	#....(同上)
) partition by hash (sno);
# 2. 创建子分区
create table student_p1 partition of student for values with (modulus 4, remainder 0);
create table student_p2 partition of student for values with (modulus 4, remainder 1);
create table student_p3 partition of student for values with (modulus 4, remainder 2);
create table student_p4 partition of student for values with (modulus 4, remainder 3);
explain select * from public.student where sno=5;
```

## 5. 通过表继承和触发器创建分区表

```mysql
# 1. 创建父表
create table student(sid int,name text,reg_date date not null);
# 2. 创建子表
create table student_2019(check(reg_date>='2019-01-01' and reg_date<'2020-01-01')) inherits(student);
create table student_2020(check(reg_date>='2020-01-01' and reg_date<'2021-01-01')) inherits(student);
create table student_2021(check(reg_date>='2021-01-01' and reg_date<'2022-01-01')) inherits(student);
# 3. 查看父表
\d+ student
# 触发器
tri_students_insert before insert on student for each row execute function fun_students_insert();
# 子表
student_2019, student_2020, student_2021
# 4. 创建触发器函数
create or replace function fun_students_insert() returns trigger as $$ 
begin
	if(new.reg_date >= '2019-01-01' and new.reg_date < '2020-01-01')
	then insert into student_2019 values(new.*);
	elseif(new.reg_date >= '2020-01-01' and new.reg_date < '2021-01-01')
	then insert into student_2020 values(new.*);
	else
	insert into student_2021 values(new.*);
	end if;
	return null;
end;$$
language plsql;
# 5. 创建触发器
create trigger tri_students_insert before insert on student for each row execute procedure fun_students_insert();
# 6. 插入测试数据
insert into student values(1001, 'LiMing', '2019-01-03');
insert into student values(1002, 'ZhaoHai', '2020-05-13');
insert into student values(1003, 'SunYang', '2021-09-23');
# 7. 测试
explain select * from student where reg_date > '2021-01-01';
```

## 6. 小结

### 1. 分区表注意事项

1. 插入数据时按分区定义将数据存储在对应的分区中，如果插入的数据找不到任何满足定义条件的分区．会导致数据插入失败。因此．通常需要定义一个默认的分区用于存储非固定属性的数据。
2. 查询整个分区表时，SQL根据查询条件执行分析后会自动跳过不需要扫描的分区。在某些特殊场景下，也可以显式指定要查询的分区。
3. 创建子分区后通常需要为每个分区创建单独索引，这对提高查询性能很有益处。

### 2. 分区表扫描控制

1. 如果只查询分区表某个分区的数据，KES可能仍会扫描所有分区，这大大降低了查询性能。KES提供了参数constraint_exclusion，从而避免扫描所有分区。

2. 参数constraint_exclusion控制查询规划器对表约束的使用，以优化查询，这个参数有以下三个选项。

   | 参数值    | 简介                                                         |
   | --------- | ------------------------------------------------------------ |
   | off       | 不使用表上的constraint来生成计划                             |
   | on        | 如果设置成on, 则对所有表生效, 生成PLAN时会考虑表上的constraint |
   | partition | 默认选项, 只对分区表生效, 从而避免扫描分区表所有分区         |

# 7. Toast行外存储

## 1. 什么是TOAST?

1. 普通数据行的存储技术

   - KES数据块是数据在文件存储中的基本单位, 默认的大小为8KB
   - KES不允许一行数据跨块存储
   - KES无法直接存储非常大的字段值

2.  TOAST主要用于存储一个大字段的值

   - TOAST是' The Oversized-Attribute Storage Technique ' (超尺寸属性存储技术)的缩写.
   - TOAST技术将大型字段值压缩或切片成多个物理行, 这种存储方式叫(行外存储)

3. TOAST适用场景

   | 适用TOAST的字段示例                                          | 不适用TOAST的字段示例                                        |
   | ------------------------------------------------------------ | ------------------------------------------------------------ |
   | 数据类型是变长的属性, 能够存储较大的字段值.<br />1. text<br />2. xml<br />3. jsonb, json | 数据类型是整数, 浮点数, 固定长度的数据, 只能存储较小的字段值.<br />1. Character<br />2. Integer<br />3. smallserial |

## 2. TOAST存储方式解析

{% asset_img  TOAST存储方式.png %}

## 3. TOAST表结构

**(上图的toast表)**

| 字段名称   | 说明                                                         |
| ---------- | ------------------------------------------------------------ |
| chunk_id   | (1) 用来表示特定TOAST值的OID<br />(2) 具有同样chunk_id值的所有行组成blog表的TOAST字段的一行数据 |
| chunk_seq  | 用来表示该行数据在整个数据中的位置                           |
| chunk_data | 该Chunk实际的数据                                            |

**在chunk_id和chunk_seq上有一个唯一索引, 提供对值的快速检索.**

## 4. 计算totast空间用到的内置函数

| 内置函数                             | 返回类型 | 说明                                                     |
| ------------------------------------ | -------- | -------------------------------------------------------- |
| sys_indexes_size(regclass)           | bigint   | 关联指定表OID或表名的表索引的使用总磁盘空间              |
| sys_relation_size(relation regclass) | bigint   | pg_relation_size(... 'main')的缩写                       |
| sys_size_pretty(numeric)             | text     | 把以字节计算的数值转换成一个人类易读写的尺寸单位         |
| sys_total_relation_size(regclass)    | bigint   | 指定表OID或表名使用的总磁盘空间, 包括所有索引和TOAST数据 |

## 5. 通过sys_relation_size计算占用空间

```mysql
# 1. blog表(不包含toast表)
select sys_size_pretty(sys_relation_size('public.blog'));
# 输出: 8192bytes
# 2. toast表(不包含索引)
select sys_size_pretty(sys_relation_size('pg_toast.pg_toast_17359'));
# 输出: 376kb
# 3. toast索引
select sys_size_pretty(sys_relation_size('pg_toast.pg_toast_17359_index'));
# 输出: 16kb
# 4. toast表fsm文件
select sys_size_pretty(sys_relation_size('pg_toast.pg_toast_17359', 'fsm'));
# 输出: 24kb
```

## 6. 通过sys_total_relation_size计算占用空间

```mysql
# 1. toast表和索引
select sys_size_pretty(sys_total_relation_size('pg_toast.pg_toast_17359'));
# 输出: 416kb
# 2. blog表(包含toast表, 索引)
select sys_size_pretty(sys_total_relation_size('blog'));
# 输出: 424kb
```

## 7. 小结

### 1. TOAST优势

1. 可以存储超长超大字段，避免之前不能直接存储的限制。
2. SELECT 语句使用较短的键值进行匹配的，那么执行器的大多数工作都将使用主表完成。
3. TOAST在物理上与普通表是分离的，检索查询时不检索到大字段会极大地加快速度．

### 2. TOAST缺点

1. 大字段的索引创建, 有可能会失败
2. 大字段的更新慢

# 8. 索引

## 1. 创建索引

### 1. 创建非唯一索引

```mysql
# 1、创建测试表t01,并插入1万行数据。
# 2、收集 t01表的统计信息。
# 3、在id列上创建名为idx_t01_id 的非唯一索引。
# 4、使用元命令查看idx_t01_id索引的信息。
# 5、测试查询是否使用索引扫描
create table t01(id int,name text);
insert into t01 select generate_series(1,10000),MD5(random());
create index idx_t01_id on t01(id);
\di+ idx_t01_id;
explain select * from t01 where id=6888;
```

### 2. 创建唯一索引

```mysql
# 1、创建测试表t01.
# 2、在id列上创建名为idx_t01_id的唯一索引。
# 3、使用函数查看idx_t01_id索引的定义信息。
create table t01(id int,name text);
create unique index idx_t01_id on t01(id);
select sys_get_indexdef('idx_t01_id'::regclass::oid);
```

### 3. 创建表达式索引

**注:表达式索引是对字段进行某种运算或套用函数之后的结果创建索引，查询时也必须使用相同的表达式或函数才会使用索引扫描。**

```mysql
# 1、创建测试表t01。
# 2、在name列上创建名为idx_t01_name的表达式索引。
# 3、使用函数查看idx_t01_name索引的定义信息。
create table t01(id int,name text);
create index idx_t01_name on t01(left(name,1));
select sys_get_indexdef('idx_t01_name'::regclass::oid);
```

### 4. 创建部分索引

**注:部分索引指只对感兴趣的数据行创建索引，此种方式创建索引需要使用WHERE条件。查询时必须指定符合创建部分索引时指定的条件才会使用索引扫描。**

```mysql
# 1、创建测试表t01。
# 2、在name列上创建名为idx_t01_id的部分索引。
# 3、使用函数查看idx_t01_id索引的定义信息。
create table t01(id int,name text);
cteate index idx_t01_id on t01(id) where id between 500 and 1000;
select sys_get_indexdef('idx_t01_id'::regclass::oid);
```

### 5. 复合索引

**注:建一个复合索引(c1, c2,c3)，功能上相当于建了(c1),(c1, c2) ,(c1,c3)， (c1, c2,c3)四个索引。查询时指定的条件必须从索引首列开始引用才会使用索引扫描。**

```mysql
# 1、创建测试表t01。
# 2、在id、name 列上创建名为idx_t01_id的复合索引。
# 3、使用函数查看idx_t01_id索引的定义信息。
create table t01(id int,name text);
create index idx_t01 on t01(id,name);
select sys_get_indexdef('idx_t01'::regclass::oid);
```

## 2. 修改索引

### 1. 重命名索引

```mysql
# 1、使用system用户登录test数据库。
# 2、创建测试表t01,并插入1万行数据。
# 3、在id列上增加名为 pk_t01的主键约束（系统自动创建跟主键约束同名的索引)。
# 4、使用元命令查看 t01表的id列上是否已创建索引。
# 5、修改pk_t01的索引名称为idx_t01_id。
# 6、查看索引修改结果。
create table t01(id int,name text);
insert into t01 select generate_series(1, 10000),MD5(random());
alter table t01 add constraint pk_t01 primary key(id);
\d+ t01
alter index pk_t01 rename to idx_t01_id;
\d+ t01
```

### 2. 移动索引到其他空间

```mysql
# 1. 创建空间tbs01 和 tbs02
# 2. 查看待移动索引的原始索引空间, 然后迁移索引到索引空间tbs01中
select table_name, index_name, tablespace_name from dba_indexes where index_name in ('SNO_CON_PK', 'SCORE_PK');
alter index exam.SNO_CNO_PK set tablespace tbs01;
alter index exam.SCORE_PK set tablespace tbs01;
select table_name, index_name, tablespace_name from dba_indexes where index_name in ('SNO_CNO_PK');
# 3. 将tbs01索引空间中的所有索引全部迁移到索引空间tbs02
alter index all in tablespace tbs01 set tablespace tbs02;
select table_name, index_name, tablespace_name from dba_indexes where index_name in('SNO_CNO_PK', 'SCORE_PK');
```

### 3. 修改索引的填充因子

```mysql
# 1、使用system用户登录test数据库。
# 2、创建测试表t01 。
# 3、在id列上创建名为idx_t01_id的索引。
# 4、使用函数查看idx_t01_id索引信息。
# 5、修改idx_t01_id索引的填充因子为75。
# 6、使用函数查看idx_t01_id索引修改后的信息。
create table t01(id int,name text);
create index idx_t01_id on t01(id);
select sys_get_indexdef('idx_t01_id'::regclass::oid);
alter index idx_t01_id set(fillfactor = 75);
select sys_get_indexdef('idx_t01_id'::regclass::oid);
```

## 3. 重建索引

### 1. 索引膨胀时重建索引

```mysql
# 1、使用system 用户登录test数据库。
# 2、创建测试表t01,并插入100万行数据。
# 3、在id列上创建名为idx_t0l_id的索引。
# 4、使用元命令查看idx_t01_id索引信息。
# 5、删除t01表中50%的数据。
# 6、使用元命令查看idx_t01_id索引信息（索引空间未释放）。
# 7、重建idx_t01_id索引。
# 8、使用元命令查看idx_t01_id索引信息（索引空间释放）。
create table t01(id int,name text);
insert into t01 select generate_series(1,1000000),MD5(random());
create index idx_t01_id on t01(id);
\di+ idx_t01_id;
delete from t01 where id <= 25000 or id > 75000;
\di+ idx_t01_id;
reindex index idx_t01_id;
\di+ idx_t01_id;
```

### 2. 索引文件损坏时重建索引

```mysql
# 1、使用system用户登录test数据库。
# 2、创建测试表t01,并插入100万行数据。
# 3、在 id列上创建名为idx_t01_id的索引。
# 4、查看idx_t01_id索引的存储信息。
# 5、清空idx_t01_id索引的存储文件。
# 6、测试使用索引扫描查询数据（执行失败)。
# 7、重建idx_t01_id索引。
# 8、测试使用索引扫描查询数据（执行成功)。
create index idx_t01_id on t01(id);
select sys_relation_filepath('idx_t01_id');
/data/base/16168/16637
select * from t01 where id=9999;
# 报错: 无法读取文件'base/16168/16637'的块0:只读取了8192字节的0
reindex index idx_t01_id;
select * from t01 where id=9999;
```

### 3. 重建索引小结

{% asset_img  重建索引小结.png %}

## 4. 删除索引

### 1. 使用DROP INDEX 删除索引

```mysql
# 1、使用system用户登录test 数据库。
# 2、创建测试表t01,并插入1万行数据。
# 3、在id列上创建名为idx_t01_id的索引。
# 4、查看查询 t01表的SQL执行计划（索引扫描）。
# 5、删除idx_t01_id索引。
# 6、查看查询t01表的SQL执行计划(全表扫描)。
create index idx_t01_id on t01(id);
explain select * from t01 where id=9999;
drop index idx_t01_id;
```

### 2. 删除主键约束时会自动删除索引

```mysql
# 1、使用system用户登录test数据库。
# 2、创建测试表t01,并插入1万行数据。
# 3、在 id列上增加名为pk_t01的主键约束。
# 4、查看t01表中自动创建的索引信息。
# 5、删除主键约束。
# 6、查看t01表中的索引是否被自动删除。
alter table t01 add constraint pk_t01 primary key (id);
\d+ t01;
# 会看到, indexes下面有'pk_t01'primary key, btree(id)
alter table t01 drop constraint pk_t01
\d+ t01;
# 发现, indexes没了
```

## 5. 小结

### 1. 优点

1. 合理使用索引可以大大提高数据的检索速度。
2. 唯一性索引可保证表中每一行数据的唯一性。
3. 索引可以加速表和表之间的连接以及实现数据的参考完整性。
4. 在使用分组和排序子句进行数据检索时，索引可以显著减少分组和排序的时间。

### 2. 缺点

1. 索引需要占用物理空间.
2. 增删改表的记录行的时候，索引需要动态维护，这降低了性能。
3. 建立和删除索引比较耗时，消耗服务器资源。

# 9. 视图

## 1. 创建视图

### 1. 创建一个只现实基表中部分字段的视图

```mysql
create view v01 AS select sno,sname,city,job from exam.student where job='Database Engineer';
```

### 2. WITH LOCAL CHECK OPTION选项

```mysql
# 1、概述。
# WITH LOCAL CHECK OPTION选项用于DML操作时，对新数据检验只需满足本视图定义时的条件.
# 2、示例。
# (1）基于v01视图创建v02视图。
# 要求1:使用WITH LOCAL CHECK OPTION选项。
# 要求2: v02只显示city为 Beijing的学员信息。
# (2)针对视图v02进行INSERT和UPDATE时。
# 现象1:新记录行不满足“city=Beijing”时，则拒绝。
# 现象2:新记录行满足“city=Bcijing”时，则允许。
create view v02 AS select * from v01 where city='Beijing' with local check option;
update v02 set city='Shanghai' where city='Beijing';
# 错误: 新行违反了视图v02的检查选项
update v02 set job='Sales' where job='Database Engineer';
# successful
```

### 3. WITH CASCADED CHECK OPTION 选项

```mysql
# 1、概述。
# WITH CASCADED CHECK OPTION选项用于DML操作时，对新数据检验必须满足本视图和所有级联视图定义时的条件。
# 2、示例。
# (1)基于vo1视图创建v03视图。
# 要求1:使用WITH CASCADED CHECK OPTION.
# 要求2:显示city 为Chongqing 的学员信息。
# (2）针对新视图v03进行INSERT和 UPDATE时。
# 现象1:不满足city='Chongqing'，会拒绝。
# 现象2:不满足job='Database engineer’时，会拒绝。
create view v03 AS select * from v01 where city='Chongqing' with cascaded check option;
update v03 set city='Shanghai' where city='Chongqing';
# 报错
update v03 set job='Sales' where job='Database Engineer';
# 报错
```

### 4. 无效视图

```mysql
# 场景1: 基表不存在时,使用force选项强制创建视图名称
create force view v04 AS select * from t01;
# 警告: view created with comilation errors
select * from v04;
# 错误: view 'v04' is still invalid after recompiling

# 场景2: 把有效图所依赖的基表后被删除后也会变为无效
create table t02(id int);
create view v05 AS select * from t02;
drop table t02;
# 注意: 视图 v05 依赖于 表 t02
select * from v05;
# 错误: view 'v05' is still invalid after recompiling
```

## 2. 修改视图

### 1. 为一个可更新视图附加一个默认列值

```mysql
# 当为视图的字段设置默认值后，通过视图向基表插入数据时，系统会使用视图字段的默认值对基表字段进行填充。
create table base_table(id int,ts timestamptz);
create view a_view AS select * from base_table;
alter view a_view alter column ts set default now();
insert into base_table(id) values(1);
select * from base_table;
insert into a_view(id) values(2);
select * from base_table;
```

### 2. 修改视图名称

```mysql
alter view a_view rename to a_new_view;
```

## 3. 删除视图

### 1. 删除视图

```mysql
drop view v05;
```

### 2. 使用cascade选项删除视图

```mysql
# 使用cascade选项删除视图时, 系统会递归删除依赖此视图的其他视图
drop view v01 cascade;
# 注意: 串联删除2个其他对象
# detail: 递归删除视图v02
# 递归删除视图v03
```

### 3. 使用restrict选项删除视图

```mysql
# 使用restrict选项删除视图时, 依赖此视图的其他视图会变得无效
# restrict选项是默认选项
create view v06 AS select * from exam.student;
create view v07 AS select * from v06;
drop view v06 restrict;
# 注意: 视图v07依赖于视图v06;
select * from v07;
# 错误: view 'v07' is stil invalid after recompiling
```

## 4. comment添加注释

**使用comment命令为视图或视图中的字段添加注释, 可以方便使用者了解视图的业务含义**

### 1. 对视图添加注释

```mysql
create view v08 AS select sno,sname,city,job from exam.student;
comment on view v08 is 'View based on table exam.student';
```

### 2. 对视图的列添加注释

```mysql
comment on column v08.name is 'name of exam.student';
```

### 3. KSQL中查看注释信息

```mysql
\dv+ v08
```

### 4. 删除视图的注释

```mysql
comment on view v08 IS '';
```

## 5. 小结

### 1. KES V8R6支持只读视图吗?

**KES不支持使用with read only选项来创建只读视图，可通过设置用户对视图的访问权限来实现类似的功能。**

### 2. 物化视图和普通视图的比较

1. 物化视图也是由查询来定义的一种视图。

2. 物化视图本身会存储数据，是一种特殊的物理表。

   (1）它是针对基表数据查询的副本，或者用来生成基表数据汇总后的报表。

   (2）物化视图存储的数据类似于基于数据的快照，可刷新物化视图来查看最新的基表数据

   (3）物化视图可以大大提升复杂查询的效率。

# 10. 物化视图

## 1. 什么是物化视图?

物化视图会把视图可见范围内的数据在本地缓存下来，当成一张本地表来使用。

1. 物化视图最典型的应用场景。

   (1)物化视图用于在OLAP(在线分析与处理）领域，是用于加速时效性要求不高的长时间复杂杳询。

   (2）物化视图支持建立索引以加快查询速度。

2. 物化视图刷新。
   (1)创建物化视图，数据将会全量缓存。

   (2)刷新物化视图，数据将会增量缓存。

## 2. 物化视图的缺点

1. 不支持通过CREATE OR REPLACE 语法重建物化视图。

   (1）重建只能先删除再重建。

   (2）删除以后，该视图上所有的索引都会丢失。

2. KES不支持自动刷新物化视图。

   (1）每次刷新数据时都要执行一次REFRESH MATERIALIZED VIEW操作。

   (2）要想实现自动刷新，必须使用类似 crontab、pgAgent定时任务或者是触发器。

3. 刷新物化视图是一个阻塞操作。

   物化视图在刷新期间是无法访问的。

4. CONCURRENTLY并发刷新物化视图

   (1）如果没有这个选项，可能会阻塞其他尝试从物化视图中读取的连接。

   (2）前提条件是物化视图上要有一个唯一索引。

   (3）实现无锁刷新的代价就是，刷新期间进行视图访间，那么刷新时间会变长。

## 3. 物化视图的管理

### 1. 建立物化视图

```mysql
# 创建public.facts表
create table public.facts(
	fact_type_id integer not null,
    tract_id character varying(11) not null,
    yr integer not null,
    val numeric(12,3),
    perc numeric(6,2)
)
# 基于public.facts表上创建public.vw_facts_2021_materialized物化视图
create materialized view public.vw_facts_2021_materialized AS select fact_type_id,val,yr,tract_id from public.facts where yr=2021;
```

### 2. 在物化视图上建立索引

```mysql
create unique index ix on public.vw_facts_2021_materialized(tract_id, fact_type_id,yr)
```

### 3. 刷新物化视图

```mysql
refresh materialized view concurrently public.vw_facts_2021_materialized;
```

### 4. 聚簇物化视图

```mysql
cluster public.vw_facts_2021_materialized using ix;
# 注意
# 1、指定聚簇操作所依据的索引名。执行过以后，系统就会自动记下该表是依据哪个索引进行聚簇排序的，后面再次执行聚簇操作时系统会自动使用该索引，所以索引名仅在首次聚簇操作时需要，后续不再需要。
# 2、每次刷新过物化视图后，都需要重新对其进行一次聚簇排序操作
```

### 5. 删除物化视图

```mysql
drop materialized view public.vw_facts_2021_materialized;
```

# 11. 序列

## 1. 什么是序列

1. 序列对象就是用CREATE SEQUENCE创建的特殊的单行表，它本质上是一个数字生成器。
2. 序列对象通常用于为行或者表生成唯一的标识符，也可用于计数。
3. KES 为自增列提供了一个特殊的数据类型serial，会在自增列上引用一个自动创建的序列。
4. 我们需要引用存在等间隔的自增长的数据时，可以使用自定义的序列。

## 2. 创建序列

### 1. 显示创建序列

```mysql
# 1、创建序列seq01，选项默认。
# 2、创建t01表，并设置ID列的默认值引用seq01序列。
# 3、向t01表插入两行数据。
# 4、查看t01表的数据。
create sequence seq01;
create table t01(id int default nextval('seq01'),name text);
insert into t01(name) values('a'),('b');
select * from t01;
# id name
#  1   a
#  2   b
```

### 2. 隐式创建序列

```mysql
# 1、创建t02表。
# 2、在t02表的ID列设置serial数据类型。
# 3、查看t02表。
create table t02(id serial,name text);
insert into t02(name) values('a'),('b');
select * from t02;
# id name
#  1  a
#  2  b
```

### 3. 查看序列

````mysql
# 1、可在ksql中使用元命令\ds来查看序列。
# 2、可使用数据字典sys_sequences或user_sequences等来查看序列的详细信息。
# 3、可使用“select * from序列;”来查看序列的引用状态。
\ds
\x
select * from sys_sequences where sequencename='seq01';
select * from seq01;
select * from t02_id_seq;
````

## 3. 修改序列

```mysql
# 修改序列的起始值和步长
# 1. 修改seq01序列的起始值为100,步长为2。
# 2. 往t01表再插入2条记录。
# 3. 查看t01表的数据。
alter sequence seq01 start with 100 increment by 2;
insert into t01(name) values('c'),('d');
select * from t01;
# 结果
# id   name
# 1		a
# 2		b
# 4		c
# 6		d
# (4 rows)
# 注意
# 序列的初始值只会影响该序列首次被使用时的取值起点。
# 修改已经被使用过的序列的初始值，不会影响序列后续生成值的变化。
# 如果是一个可循环的序列，序列到达边界值时会从另一个边界值开始进行下一次循环。

# 重置序列的当前值
# 1. 查看seq01序列的当前值。
# 2. 使用setval函数重置seq01序列的当前值。
# 3. 查看seq01序列的当前值。
# 4. 查看seq01序列的下一个值。
select currval('seq01'); # 6
select setval('seq01', 10); # 10
select currval('seq01'); # 10
select nextval('seq01'); # 12
```

## 4. 删除序列

```mysql
# 删除显示创建的序列
# 1、使用默认选项删除seq01序列失败。
# 2、使用cascade选项删除seq01序列成功。
# 3、查看t01表的定义信息，ID列默认值取消了对序列的引用。
drop sequence seq01;
# 报错: 无法删除序列seq01, 因为有其它对象依赖它
# detail: 表t01的列id的缺省值依赖于序列seq01
# hint: 使用drop .. cascade 把依赖对象一并删除
drop sequence seq01 cascade
# 注意: 递归删除表t01的列id的缺省值
\d t01

# 删除隐式创建的序列
# 1、使用默认选项删除t02_id_seq序列失败。
# 2、使用cascade 选项删除t02_id_seq序列成功。
# 3、查看t02表的定义信息，ID列默认值取消了对序列的引用。
drop sequence t02_id_seq;
# 报错: 无法删除序列t02_id_seq 因为有其它对象依赖它
# detail: 表t02的列id的缺省值依赖于序列t02_id_seq
# hint: 使用drop .. cascade 把依赖对象一并删除
drop sequence t02_id_seq cascade;
# 注意: 递归删除表 t02 的列id的缺省值
\d t02
```

## 5. 小结

### 1. 表引用序列作为主键时可能产生GAP的原因

1. 多用户并发引用了序列
2. 多对象同时引用了序列
3. 用户回滚了DML操作
4. 序列缓存值因数据库异常崩溃导致丢失

# 12. 数据操作

## 1. 插入数据

### 1. 插入单行数据

```mysql
create table t01 (
	code char(5) constraint pkey primary key default 'UA000',
    title varchar(40) not null default 'KES',
    did integer not null default 0,
    date_prod date default now(),
    kind varchar(10),
    len interval hour to munute
);

insert into t01 values ('UA502', 'Bananas', 105, '1971-07-13', 'Comedy', '82 munutes');
# 注意
# (1) 指定的值域列表必须匹配字段列表的数目和顺序。
# (2) 时间和字符串数据类型的值必须加单引号。
# (3) 插入的值和字段类型必须匹配或者能被隐式转换。
```

### 2. 使用默认值插入数据

```mysql
insert into t01(code, title, did, date_prod, kind) values('t_601', 'Yojimbo', 106, default, 'Drama');
insert into t01 default values;
# 注意:
# 可以使用DEFAULT关键字来显式为字段赋予默认值。
# 未赋值的字段如果定义了DEFAULT值，则插入DEFAULT值，否则插入NULL值。
# 插入记录时对未定义默认值的非空列必须显式指定一个非空值，否则插入操作会报错。
```

### 3. 使用NULL为字段插入控制

```mysql
insert into exam.course values(40, 'KCD', null, 80);
```

### 4. 一次插入多行数据

```mysql
# 1. 使用一条values子句往t01表插入两行数据
insert into t01 (code, title, did, date_prod, kind) values ('B6717', 'TAMPOPO', 110, '1985-02-10', 'Comedy'), ('HG120', 'The Dinner Gane', 140, DEFAULT, 'Comedy');
select * from t01;
# 注意
# KES 除了支持通过标准SQL进行单条数据插入，还支持使用values子句一次插入多条记录。
# 多条记录之间使用逗号分隔。

# 2. 使用子查询插入批量数据
create table t02 as select code, title, did, date_prod from t01 where 1=2;
insert into t02 select code, title, did, date_prod from t01 where did > 100;
select * from t02;
# 注意
# 子查询的字段顺序要与目标表的字段顺序一致
```

## 2. 更新数据

###  1. 单列全表更新

```mysql
create table student01 as select * from exam.student;
update student01 set city='Schanghai';
```

### 2. 多列全表更新

```mysql
update student01 set city='Guangzhou',job='Developer';
```

### 3. 部分记录更新

```mysql
update student01 set city='Beijing' where sno=1002;
```

### 4. 参照其他表数据更新

```mysql
update student01 s01 set city=s.city,job=s.job from exam.student s where s01.sno=s.sno;
```

### 5. 关联子查询更新

```mysql
update student01 set sname=null,city=null;
update student01 s01 set (sname,city)=(select sname,city from exam.student s where s.sno=s01.sno);
```

## 3. 删除数据

### 1. 删除全部记录

```mysql
delete from student01;
# 注意 删除大表中的所有数据时, 建议使用truncate来清空表, 效率更高
```

### 2. 删除部分记录

```mysql
insert into student01 select * from exam.student;
delete student01 where city='Beijing'
```

### 3. 使用子查询构造删除数据的条件

```mysql
delete from student01 where job=(select job from student01 where sno=1001) and sno<>1001;
```

## 4. 合并数据

```mysql
# 1、更新student01表的sname字段为null。
# 2、参照student表，使student01和 student两表的sname字段保持同步。
# 3、将 student01表中缺失的数据参照student表插入到student01表中。
update student01 set sname=null;
merge into student01 s01 using exam.student s on (s.sno=s01.sno) when matched then update set s01.sname=s.name when not matched then insert(sno,sname) values(s.sno,s.name);
```

## 5. 小结

### 1. 避免一次插入/修改/删除数据过多

1. 如果开启了事务自动提交，每一条DML操作都产生一个独立的事务且会生成事务日志。
2. 使用“INSERT+子查询”插入数据可能导致大事务，会生成大量的事务日志而增加服务器的I/O消耗，尽量在子查询中添加WHERE条件以减少单次插入的数据量。

### 2. update技巧

```mysql
# 1. 直接将函数/变量赋值给字段
update t01 set LastDate=date() where UserID='aasdd';
# 2. 基于字段当前的值更新某些字段
update t01 set clickcnt=clickcnt+1 where ID=xxx;
# 3. 基于一个字段的值更新另一个字段的值
update t01 set Lastdate=regdate where xxx;
```

# 13. 集合运算, 子查询, 伪列

## 1.  集合运算

{% asset_img  集合运算.png %}

```mysql
# union
# 示例:查询讲师岗位信息，同一个讲师在不同时期任相同岗位的信息只显示一次。
select ino,title from exam.instructor 
union 
select ino,title from exam.instructor_history;

# union ALL
# 示例:查询讲师岗位信息，同一个讲师在不同时期任同一岗位的信息显示多次。
select ino,title from exam.instructor
union all
select ino,title from exam.instructor_history;

# intersect
# 示例:查询经多次调岗又换回到原岗位的讲师信息。
select ino,title from exam.instructor
intersect
select ino,title from exam.instructor_history;

# except
# 示例:查询讲师的历史的岗位信息，同一个讲师的历史岗位和当前岗位相同的记录不显示。
select ino,title from exam.instructor
except
select ino,title from exam.instructor_history;

# 使用null值匹配的数目
# 示例:查询在讲师编号为104的讲师岗位信息并显示岗位调整的时间。
select ino,title,null from exam.instructor where ino=104
union all
select ino,title,res_date from exam.instructor_history where ino=104;
```

## 2. 子查询

```mysql
# 单行子查询
# 1、示例:查询考试成绩为97分的学员信息。
select sno,gender,sname,job from exam.student where sno=(select sno from exam.score where score=97);
# 2、示例:查询考试分数最低的学员信息。
select sno,gender,sname,job from exam.student where sno=(select sno from exam.score where score=(select min(score) from exam.score) );

# 多行子查询
# 1、示例:查询所有与学号为1001、1008的学员在同一个公司的学员信息。
select sno,gender,sname,job,company from exam.student where company in (select company from exam.student where sno=1001 or sno=1008);
# 2、示例:查询比初级考试成绩中的最低分数高的其它级别的考试成绩信息。
select * from exam.score where score > any(select score from exam.score where cno=10) and cno <> '10';
# 3、示例:查询比初级考试成绩中的最高分数高的其它级别的考试成绩信息。
select * from exam.score where score > all(select score from exam.score where cno=10) and cno <> '10';

# 多列子查询
# 示例:查询跟学号为1001和1004的学员同职位且在同一个城市的学员信息。
select sno,gender,sname,job,company from exam.student where (job,city) in (select job,city from exam.student where sno in (1001,1004));
# 注意: 多列子查询转换为多个单列子查询时可能导致查询结果不相同

# 标量子查询
# 示例:查询每门课课程信息, 并统计通过该课程考试的学员数
select *,(select count(*) from exam.score s where s.cno = c.cno and s.score > 70) as pass_count from exam.course c;

# 关联子查询
# 示例:查询大于每门课程考试成绩的平均分数的所有考试成绩信息
select * from exam.score outer_table where score > (select avg(score) from exam.score inner_table where inner_table.cno=outer_table.cno);
```

## 3. 伪列

```mysql
# 在Top-N查询中使用rownum
# 示例:查询考试分数按从高到低排名前三的学生信息。
# 注意:
# 	ROWNUM作为条件判断时，必须包含从1开始的范围，否则不会输出结果。
# 	ROWNUM<10或者ROWNUM BETWEEN 1 AND 10，是正确的使用方法。
# 	ROWNUM>10或者ROWNUM BETWEEN 5 AND 10，是错误的使用方法。
select sno,cno,score from (select sno,cno,score from exam.score order by score desc) where rownum <= 3;

# 在层次化查询中使用level
# 示例:创建具有层次逻辑关系字段的测试表，并执行层次化查询。
# 1、创建t01表，并插入样例数据，parent _id列参照id列的值。
# 2、遍历t01表，查看人员信息及其直接上级人员姓名。
# 3、按职级从高到低遍历且只显示前两级的人员信息。
create table t01 (id int,name text,parent_id int);
insert into t01 values(101,'ZhouSiqi', null);
insert into t01 values(102, 'Zhangwei', 101);
insert into t01 values(105, 'ChenHang', 102);
select level,id,name,parent_id,prior name as leader_name from t01 strat with id=101 connect by prior id=parent_id;
select level,id,name,parent_id,prior name as leader_name from t01 start with id=101 connect by level<3 and prior id=parent_id;

# CONNECT_BY_ISLEAF
# 在分层查询中，使用CONNECT_BY_ISLEAF能够指出给定行是否能够被继续展开没有子元组的元组认为是一个叶节点。叶节点的CONNECT_BY_ISLEAF值为1，非叶节点的CONNECT_BY_ISLEAF值为0。
# 示例:按职级查询人员信息，标识出普通人员和管理者。
select level,id,name,parent_id,CONNECT_BY_ISLEAF as leaf,prior name as leader_name from t01 start with id=101 connect by prior id=parent_id;

# CONNECT_BY_ISCYCLE和NOCYCLE
# 注意:CONNECT_BY_ISCYCLE为1代表这个元组有子元组，且子元组又是它的祖先元组，即数据成环;CONNECT_BY_ISCYCLE为0表示数据未成环。NOCYCLE 关键字可以让KES在有环的情况下也能返回记录。
# 示例:将样例表t01的数据修改为循环参照后再执行层次化查询。
# 1、修改记录id为101的MANAGER_ID为102，造成100和102数据成环。
# 2、不使用NOCYCLE执行层次查询将导致错误。
# 3、使用NOCYCLE可成功返回结果。
update t01 set parent_id=102 where id=101;
select level,id,name,parent_id,prior name as leader_name from t01 start with id=101 CONNECT BY PRIOR id=parent_id;
# 报错: CONNET BY loop found in user provided data
select level,id,name,parent_id,CONNECT_BY_ISCYCLE as cycle,prior name as leader_name from t01 start with id=101 CONNECT BY NOCYCLE PRIOR id=parent_id;
# 注意
# START WITH表示查询起点，一定要有符合条件的记录，否则整个查询返回空集。
# CONNECT BY 控制分层查询的顺序，“PIROR父键列”表示TOP-DOWN遍历，“PIROR子键列”。表示 DOWN-TOP遍历。
# 使用WHERE过滤记录，CONNECT BY后面使用AND带条件表示裁剪节点及此节点下整个分枝。
# 如果不能确保数据是否有成环元纽,最佳实践是使用NOCYCLE关键，避免错误发生。

# nextval和currval
# 1、创建序列并查询序列生成的值。
# 2、创建表并设置列的默认值为序列生成值。
# 3、为表插入记录并查询表的记录。
create sequence seq01;
select seq01.nextval;
select seq01.currval;
create table t01(id int default next('seq01'),name text);
insert into t01(name) values('a');
insert into t01(name) values('b');
select * from t01;
# 注意
# 用户会话中必须先查询序列的NEXTVAL值后才能查询序列的CURRVAL值。
# 在未重新查询序列的NEXTVAL前，当前会话查询的CURRVAL值保持不变。
# 不同的用户会话查询到的NEXTVAL和 CURRVAL是不一样的。
# 使用序列的值作为列值插入时，因回滚、多用户并发访问序列等可能导致列值出现GAP。
```

## 4. 小结

### 1. rownum 和 ctid 有什么不同?

1. rownum是查询结果集中数据行的逻辑序号
2. ctid是查询结果集中数据行的物理地址, 格式为(blockid, itemid)

注意: 例如ctid(1,30), 其中的1表示数据块id, 30表示为该数据块的第30条记录

### 2. 执行Top-N查询的几种方式

``` mysql
# 1. 使用嵌套子查询+rownum
select sno,cno,score from (select sno,cno,score from exam.score order by score desc) where rownum <= 3;

# 2. 使用order by + limit
select sno,cno,score from exam.score order by score desc limit 3;

# 3. 使用分析函数row_number
select sno,cno,score from (
	select sno,cno,score,row_number() over (order by score desc) as nl from exam.score
) where nl <= 3;
```

# 14. 内置单行函数

## 1. 字符函数

### 大小写处理函数

| 执行                             | 输出          |
| -------------------------------- | ------------- |
| select lower('Hello, World!');   | hello, world! |
| select upper('Hello, World!');   | HELLO, WORLD! |
| select initcap('Hello, World!'); | Hello, World! |

### 字符控制函数

{% asset_img  字符控制函数.png %}

## 2. 数字函数

### 常用数字函数

{% asset_img  常用数字函数.png %}

### 进制转换

{% asset_img  进制转换.png %}

## 3. 日期函数

### 获取日期和事件

```mysql
# 获取系统date
select corrent_date, sysdate;
# 2021-11-05 2021-11-05 15:09:50

# 获取系统time
select current_time,localtime,current_time(0),localtime(0);
# 15:10:25.436227+08  15:10:25.436227  15:10:25+08  15:10:25

# 获取系统的timestamp(date+time)
select current_timestamp,now,systimestamp,localtimestamp,localtimestamp(0),current_timestamp(0);
# current_timestamp 2021-11-05 15:12:08.252946+08 
# now 2021-11-05 15:12:08.252946+08
# now 2021-11-05 15:12:08.252946+08
# localtimestamp 2021-11-0515:12:08.252946
# localtimestamp 2021-11-0515:12:08
# current_timestamp 2021-11-05 15:12:08108
\x
# 扩展显示已关闭
```

### 日期时间处理函数

{% asset_img  日期时间处理函数.png %}

## 4. 类型转换函数

**类型转换函数是对KES中不同数据类型进行转换的函数，主要包含数字、字符、日期三种格式的相互转换。**

{% asset_img  类型转换函数.png %}

### 数值转换为字符串

{% asset_img  数值转换为字符串.png %}

### 字符串转换为数值

{% asset_img  字符串转换为数值.png %}

### 日期转换为字符串

{% asset_img  日期转换为字符串.png %}

###  字符串转换为日期

{% asset_img  字符串转换为日期.png %}

## 5. 空值处理函数

**null 值是没有定义,不确定的未知值。比如一个案件的追踪表,警方在对犯罪分子一无所知,犯罪分子性别一栏还不确定，这时就可以把性别栏设置为未知,当案件侦破到一定程度,警方知道了犯罪分子的性别,既从未知变成了已知，也就是由NULL变成男或女。**

### NVL

```mysql
# 运算符遇到空值使用NVL函数处理
select 10+NULL # 结果啥都没有
select 10+NVL(NULL, 0) # 结果为: 10
```

### NVL2

**NVL2函数接受三个参数，当第一个参数为空返会带三个参数，不为空则返回第二个参数。**

```mysql
select NVL2(null,1,2); # 结果: 2
select NVL2(10,1,2); # 结果: 1
```

### NULLIF

**NULLIF函数接受两个参数，当两个参数相同时返回空值，当两个参数不同时返回第一个参数。**

```mysql
select nullif(1,1); # 无结果
select nullif(1,2); # 1
```

### COALESCE

**COALESCE函数接受多个参数，返回第一个不为空的参数。**

```mysql
select coalesce(null,1,2); # 1
select sname,coalesce(id_no,company) from exam.student;
```

## 6. 小结

### 时间日期操作符

{% asset_img  日期时间处理小结.png %}

### 数据类型隐式转换

```mysql
# 1、什么是隐式转换?
# 所谓隐式转换，就是系统默认的转换，其本质是小存储容量数据类型自动转换为大存储容量数据类型。参与运算（算数运算和赋值运算)的操作数和结果类型必须一致，当不一致时，满足两个条件时，系统会自动完成类型转换(隐式转换)。
# 2、隐式转换的规则。
# (1）两种类型兼容，比如都是数字类型。
# (2）目标类型大于源类型，例如，double>int。3、举例:列出TEXT类型转换的支持情况。
select castsource::regtype,casttarget::regtype from sys_cast where castsource='text'::regtype and castcontext='i';
castsource casttarget
TEXT REGCLASS
TEXT CHARACTER
TEXT CHARACTER VARYING
TEXT NAME
TEXT NUMERIC
TEXT TINYINT
TEXT SMALLINT
TEXT INTEGER
TEXT BIGINT
TEXT REAL
TEXT DOUBLE PRECISION
TEXT DATE
TEXT TIME WITHOUT TIME ZONE
TEXT TIMESTAMP WITHOUT TIME ZONE
TEXT TIMESTAMP WITH TIME ZONE
TEXT INTERVAL
TEXT TIME WITH TIME ZONE
(17 rows)
# 注:其中的字i，对应英文单词Implicit (中文意思为含蓄的、暗示的),指隐式置换。
```

### 单行函数嵌套

**1、简介。**

(1）单行函数可以嵌套使用。

(2）单选函数嵌套时从最里层函数开始由内向外依次执行。

(3）单行函数可以嵌套任意层。

{% asset_img  单行函数嵌套使用.png %}

**2、函数嵌套使用。**

例:显示学生的身份证号，没登记身份证号的显示’None’。

```mysql
select sname,id_no,NVL(TO_CHAR(id_no), 'None') from exam.student;
```

# 15. 内置多行函数

## 1. 常用多行函数

{% asset_img  常用多行函数.png %}

## 2. 多行函数语法大纲

```mysql
select column,group_function(column) from table
[where condition]
[group by group_by_expression]
[having group_function(column)]
[order by column];
```

{% asset_img  多行函数语法参数.png %}

## 3. 多行函数基本用法

### AVG和SUM函数

```mysql
# 查看所有学员的平均成绩和总成绩。
select sno,AVG(score),SUM(score) from exam.score group by sno;
```

### MIN和MAX函数

```mysql
# 查看所有学员最早和最晚的考试日期
select sno,MIN(exam_date),MAX(exam_date) from exam.score group by sno;
```

### 分组函数处理控制

```mysql
# 1. 当分组函数遇到NULL值时,则会忽略NULL值
# 如下语句按照性别分组统计人数, 公司列为空的记录会被忽略
select gender,count(company) from exam.student group by gender;

# 2. 在分组统计中使用NVL函数处理空值记录
# 使用NVL函数将company列为空时返回为'None', 此时统计结果才包含所有学员
select gender,count(NVL(company, 'None')) from exam.student group by gender;
```

## 4. 分组统计

### 单列分组

```mysql
# 按照课程号分组,统计每门课程的考试人数
select cno, count(*) from exam.score group by cno;
```

### 多列组合分组

```mysql
# 按课程编号和讲师编号进行分组,统计每个讲师所授课程的学员参与人数
select cno,icno,count(*) from exam.score group by cno,ino;
```

### 使用AVG函数

```mysql
# 按课程分组,统计学员的平均成绩
select cno,AVG(score) from exam.score group by cno;
```

### 使用MAX函数

```mysql
# 按课程分组,查询每门课程中最高的分数
select cno,MAX(score) from exam.score group by cno;
```

### 聚合函数不能嵌套调用

```mysql
select cno,MIN(AVG(score)) from exam.score;
# 报错: 不允许嵌套调用聚合函数
```

### 分组聚合运算中列的使用规则

```mysql
# 1、分组聚合查询中，在查询字段列表中未使用组函数的列必须放在 group by子句中。
select cno,MIN(score) from exam.score;
# 报错: score.cno 必须出现在group by 子句中或者在聚合函数中使用

# 2、用于分组的列可以不出现在查询字段列表中。
select count(sno) from exam.student group by gender;
```

## 5. 对分组过滤

### 使用HAVING子句实现分组过滤

```mysql
# having子句中可以使用分组函数，其位置要放在group by子句后面，order by子句前面。
# 例:按课程分组，查询合格的成绩中的最高和最低分数。
select cno,MIN(score),MAX(score) from exam.score group by cno having MIN(score>=70) order by 2;
```

### 使用子查询实现分组过滤

```mysql
# 例:按课程分组，查询合格的成绩中的最高和最低分数。
select cno,smin,smax from (select cno,MIN(score) smin,MAX(score) smax from exam.score group by cno) where smin >= 70;
```

## 6. 小结

**使用分组函数的注意事项**

1. 分组函数能出现在SELECT子句中或HAVING子句中，不能出现在WHERE子句中。
2. 在SELECT列表中所有未包含在分组函数中的列都应该包含在GROUP BY子句中。
3. 包含在GROUP BY子句中的列不必包含在SELECT列表中。
4. SQL中的关键字出现顺序不能颠倒，必须是 select,from,where,group by, having, order by。
5. 除count(*)外，分组函数会自动忽略NULL值所在的行。
6. 分组聚合运算中，不允许嵌套调用聚合函数。

