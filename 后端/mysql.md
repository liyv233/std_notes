# 基本概念

数据库连接命令：mysql [-H 127.0.0.1] [-P 3306] -u root -p

- -h：mysql服务所在的主机ip
- -p：mysql服务端口号
- -u：mysql数据库用户名
- -p：mysql数据库用户名对应密码



**数据模型：**mysql是关系型数据库，基于二维表进行数据存储

- 可以通过mysql客户端连接数据库管理系统DBMS，然后通过DBMS操作数据库
- 可以使用 SQL 语句，通过数据库管理系统操作数据库，以及操作数据库中的表结构及数据
- 一个数据库服务器中可以插件多个数据库，一个数据库中耶可以包含多张表，而一张表中又可以包含多行记录



# DDL

Data Definition Language ，数据库定义语言，用于定义数据库对象（数据库，表，字段

##  数据库操作

**查询数据库**

~~~sql
# 查询所有数据库
show databases ;
#查询当前数据库
select database() ;
~~~

**创建数据库**

~~~sql
create database [if not exists] 数据库名 [default charset 字符集] [collate  排序规则] ；
~~~

**删除数据库**

~~~sql
drop database [if exists] 数据库名;
~~~

**切换数据库**

~~~sql
use 数据库名 
~~~



## 表操作

**查询当前数据库所有表**

~~~sql
show tables ;
~~~

**查看指定表结构：**

~~~sql
desc 表名 ;
~~~

**查询指定表的建表语句：**

~~~sql
show create table 表名 ;
~~~

- 有部分我们建表时未指定的参数也会查询到，指为数据库默认值，比如：存储引擎，字符集等

**创建表结构：**

~~~sql
create table 表名 (
	字段1 字段1类型 [comment 字段1注释] ,
    字段2 字段2类型 [comment 字段2注释] ,
    ...
) [comment 表注释] ;
~~~

**添加字段：**

~~~sql
alter table 表名 add 字段名 类型（长度） [comment 注释] [约束]
~~~



**修改数据类型：**

~~~sql
alter table 表名 modify 字段名 新数据类型（长度）
~~~

**修改字段名和数据类型：**

~~~sql
alter table 表名 change 旧字段名 新字段名 类型(长度) [comment] [约束]
~~~

**删除字段：**

~~~sql
alter table 表名 drop 字段名
~~~

**修改表名：**

~~~sql
alter table 表名 rename to 新表名
~~~



**删除表：**

~~~ sql
drop table [if exists] 表名
~~~

**删除指定表，并重新创建表**

~~~sql
truncate table 表名 
~~~

- 在删除表时，表中的数据也会全部删除





## 数据类型

**数值类型：**

~~~sql
tinyint smallint mediumint int bigint float double decimal 
~~~

**字符串类型：**

~~~sql
char varchar tinyblob tinytext blob text mediumblob mediumtext longblob longtext
~~~

- char 是定长字符串，指定长度多长，就占用多少个字符，和字段值的长度无关
- varchar 是变长字符串，指定的长度为最大占用长度
- char 和 varchar 都可以描述字符串，相对来说，char 性能更高，但某些情况下更废内存



### 日期类型：

~~~sql
date	YYYY-MM-DD	日期值		java.sql.Date
time	HH:MM:SS	时间值或持续时间	java.sql.Time
year	YYYY		年份值			java.sql.Date
datetime	YYYY-MM-DD HH:MM:SS		混合日期+时间		java.sql.Timestamp
timestamp	YYYY-MM-DD HH:MM:SS		混合日期+时间,时间戳		java.sql.Timestamp
~~~

- java中的日期表示：java.util.Date , java.sql.Date , java.sql.Time , java.sql.Timestamp
  - 后三个类继承自java.util.Date, 扩展了其功能
  - java.sql.Date 只读取日期
  - java.sql.Time 只读取时间
  - java.sql.Timestamp 读取日期和时间 ，到2038年过期
  - 数据库使用datetime,   java实体类推荐使用String接收



数据库存放 datetime 字段的某个值是 : “2001-12-15 12:30:59”

实体类中使用String接收到的是: “2001-12-15 12:30:59.0” (即默认会带上 ’ .0 ')



**格式化日期类型：**

**使用SimpleDateSormat格式化为String：**

~~~java
SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
String format = sdf.format(birthday);
~~~

- SimpleDateFormat是一个线程不安全的类



**使用DateTimeFormatter格式化**

~~~java
 // 获取当前时间，并且将他转化为指定格式
 String currentTime = LocalDateTime.now().format(DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss"));
~~~



#### Spring框架中将Date的格式化方案

**使用 @JsonFormat 注解：**

使用maven引入@JsonFormat所需要的包

~~~java
 <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-annotations</artifactId>
            <version>2.8.8</version>
        </dependency>
 
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.8.8</version>
        </dependency>
 
        <dependency>
            <groupId>org.codehaus.jackson</groupId>
            <artifactId>jackson-mapper-asl</artifactId>
            <version>1.9.13</version>
		</dependency>
~~~



在实体类中添加注解

~~~java
  @JsonFormat(pattern="yyyy-MM-dd HH:mm:ss",timezone="GMT+8")
    private Date  birthday;
//  pattern:是你需要转换的时间日期的格式
//  timezone：是时间设置为东八区，避免时间在转换中有误差
// @JsonFormat注解可以在属性的上方，同样可以在属性对应的get方法上，两种方式没有区别

~~~

 

**使用@DateTimeFormat注解：**

使用maven引入

~~~java
 <!-- joda-time -->
        <dependency>
            <groupId>joda-time</groupId>
            <artifactId>joda-time</artifactId>
            <version>2.3</version>
        </dependency>
~~~



在controller层我们使用spring mvc 表单自动封装映射对象时，我们在对应的接收前台数据的对象的属性上加@DateTimeFormat

~~~
@DateTimeFormat(pattern = "yyyy-MM-dd")
    @JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss",timezone="GMT+8")
    private Date symstarttime;
    
~~~

-  注解@JsonFormat主要是后台到前台的时间格式的转换

- 注解@DataFormAT主要是前后到后台的时间格式的转换



# DML

Data Manipulation Language （数据操作语言），用于对数据库中表的数据记录进行增删改

- insert
- update
- delete



## 添加数据



**给指定字段添加数据：**

~~~sql
insert into 表名 (字段名1，字段名2，...) values (值1，值2，...)
~~~

**给全部字段添加数据：**

~~~sqlite
insert into 表名 values (值1，值2，...)
~~~

**批量添加数据：**

~~~
insert into 表名 values (值1，值2，...) ,  (值1，值2，...),...
~~~



- 插入数据时，指定的字段顺序需要与值的顺序对应
- 字符串和日期类型的数据应该包含在引号中
- 插入数据的大小，应该在字段规定的范围内



## 修改数据

~~~sql
update 表名 set 字段名1 = 值1 ,  字段名2 = 值2 , ... [where 条件]
~~~

- 没有条件，则修改整张表中的数据



## 删除数据

~~~sql
delete from 表名 [where 条件]
~~~

- 没有条件，则删除整张表中的数据

- delete 语句不能删除某一个字段的值（应该使用update，将该字段甚至为null）
- delete 删除的是行



# DQL

Data Query Language （数据查询语言 ，用来查询数据库中表的记录

- select

**DQL 查询语句，语法结构如下：**

~~~sql
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
~~~

**执行顺序：**

~~~sql
FROM
表名列表
WHERE
条件列表
GROUP BY
分组字段列表
HAVING
分组后条件列表
SELECT    
字段列表
ORDER BY
排序字段列表
LIMIT
分页参数
~~~



## 基础查询



**查询所有**

~~~sql
select * from 表名
~~~

**查询多个字段**

~~~sql
selecrt 字段1,字段2,... from 表名
~~~



**字段设置别名：**

~~~sql
select 字段1 [as 别名1] ,... from 表名
~~~



### 去重

**去除重复记录：**

~~~sql
select distinct 字段列表 from 表名
~~~



**根据某些字段的去重查询：**

~~~sql
# 不考虑查询其他字段
select distinct c_name,c_year,c_month from table

select c_name,c_year,c_month from table 
group by c_name,c_year,c_month

# 考虑查询其他字段
# 如果其他字段所有结果值都想保留，建议直接用group by 和group_concat即可
select c_name,c_year,c_month,group_concat(',') c_values from table
group by c_name,c_year,c_month
~~~

**根据某些字段的去重查询，查询重复项以外的全部数据:**

一般去重是根据时间、ID等，如时间最新/ID最大/value最大等等；

~~~sql
# 重复数据中ID小的是原始项，ID大的是重复项；
select * from tableA where c_id in
(select min(c_id) minid from tableA group by c_name,c_year,c_month)

select * from tableA where c_id not in
(select min(c_id) minid from tableA group by c_name,c_year,c_month
 having count(*)>1 )


# 根据某些字段的去重查询，查询重复项（不包含原始项，只查询重复项）
select * from tableA where c_id not in 
(select min(c_id) minid from tableA group by c_name,c_year,c_month)
~~~



**根据某些字段，查询出所有重复的数据（包含原始项和重复项）**

~~~sql
select * from tableA a right join
(select c_name,c_year,c_month from table A
group by c_name,c_year,c_month
having count(*)>1) b
on a.c_name=b.c_name
and a.c_year=b.c_year
and a.c_month=b.c_month
~~~



**根据某些字段，删除重复的数据（ID最小的是要保留的数据，其他都是不要的）**

~~~sql
# 先把查询处理的id结果，as 一张表，再做delete操作
delete from tableA where c_id in ( #删除a
select * from
(select c_id from tableA where c_id not in  # a:非原始项的数据
(select min(c_id) from tableA group by c_name,c_year,c_month) #查询原始项
) a
)

delete from tableA where c_id in(
select * from 
(select c_id from tableA where c_id in
(select max(c_id) from tableA
group by c_name,c_year,c_month
having count(*)>1 )
) a
)
~~~





## 条件查询

~~~sql
select 字段列表 from 表名
~~~

**条件：**

- **常用比较运算符：**

~~~sql
> 	>=	<	<=	=
<> / !=   不等于
between ... and ... 	在某个范围内（含最小，最大值）
in(...) 	在in之后的列表中的值，多选一
like 占位符		模糊匹配（_匹配单个字符，%匹配任意字符
is null 	是null
~~~

- **常用逻辑运算符：**

~~~sql
and / && 
or /  ||
not /!  
~~~

~~~sql
# 查询姓名为两个字的员工信息 _ %
select * from emp where name like '__';
#  查询身份证号最后一位是X的员工信息
select * from emp where idcard like '%X';
select * from emp where idcard like '_________________X';
~~~



## 聚合函数

将一列数据作为一个整体，进行纵向计算 。

**语法：**

~~~sql
SELECT 聚合函数(字段列表) FROM 表名 ; 1
~~~

-  NULL值是不参与所有聚合函数运算的

**常见的聚合函数：**

~~~sql
count 		统计数量
max			最大值
min			最小值
avg			平均值
sum			求和
~~~



~~~sql
select count(*) from emp; -- 统计的是总记录数
select count(idcard) from emp; -- 统计的是idcard字段不为null的记录数

-- 对于count聚合函数，统计符合条件的总记录数，还可以通过 count(数字/字符串)的形式进行统计查询
select count(1) from emp;

-- 统计西安地区员工的年龄之和
select sum(age) from emp where workaddress = '西安';
~~~



## 分组查询

- 语法

~~~sql
SELECT 字段列表 FROM 表名 [ WHERE 条件 ] GROUP BY 分组字段名 [ HAVING 分组
后过滤条件 ];
~~~

- 分组之后，查询的字段一般为聚合函数和分组字段，查询其他字段无任何意义。
-  执行顺序: where > 聚合函数 > having 。
- 支持多字段分组, 具体语法为 : group by columnA,columnB



**where与having区别**

- 执行时机不同：where是分组之前进行过滤，不满足where条件，不参与分组；而having是分组

  之后对结果进行过滤

- 判断条件不同：where不能对聚合函数进行判断，而having可以。



~~~sql
--  根据性别分组 , 统计男性员工 和 女性员工的数量
select gender, count(*) from emp group by gender ;

--  查询年龄小于45的员工 , 并根据工作地址分组 , 获取员工数量大于等于3的工作地址
select workaddress, count(*) address_count from emp where age < 45 group by
workaddress having address_count >= 3;
~~~



## 排序查询

**语法：**

~~~sql
SELECT 字段列表 FROM 表名 ORDER BY 字段1 排序方式1 , 字段2 排序方式2 ;
~~~

- 如果是多字段排序，当第一个字段值相同时，才会根据第二个字段进行排序 ;

 **排序方式：**

- ASC : 升序(默认值)
- DESC: 降序

~~~sql
--  根据年龄对公司的员工进行升序排序 , 年龄相同 , 再按照入职时间进行降序排序
select * from emp order by age asc , entrydate desc;
~~~





## 分页查询

**语法**：

~~~sql
SELECT 字段列表 FROM 表名 LIMIT 起始索引, 查询记录数 ; 
~~~

-  起始索引从0开始，**起始索引 = （查询页码 - 1）* 每页显示记录数**
- 分页查询是数据库的方言，不同的数据库有不同的实现，MySQL中是LIMIT。
-  如果查询的是第一页数据，起始索引可以省略，直接简写为 limit 10。

~~~sql
--  查询第2页员工数据, 每页展示10条记录 --------> (页码-1)*页展示记录数
select * from emp limit 10,10;
~~~



~~~sql
--  查询性别为 男 ，并且年龄在 20-40 岁(含)以内的姓名为三个字的员工。
select * from emp where gender = '男' and ( age between 20 and 40 ) and name like
'___';
~~~



## 用户管理

-  在MySQL中需要通过用户名@主机名的方式，来唯一标识一个用户。
- 主机名可以使用 % 通配。

**查询用户：**

~~~sql
select * from mysql.user;
~~~

-  Host代表当前用户访问的主机, 如果为localhost, 仅代表只能够在当前本机访问，是不可以

  远程访问的。 User代表的是访问该数据库的用户名。在MySQL中需要通过Host和User来唯一标识一

  个用户

**创建用户：**

~~~sql
CREATE USER '用户名'@'主机名' IDENTIFIED BY '密码'; 1
~~~

 **修改用户密码：**

~~~sql
ALTER USER '用户名'@'主机名' IDENTIFIED WITH mysql_native_password BY '新密码' ;
~~~

 **删除用户:**

~~~sql
DROP USER '用户名'@'主机名' ;
~~~



~~~sql
-- 创建用户itcast, 只能够在当前主机localhost访问, 密码123456;
create user 'itcast'@'localhost' identified by '123456';

-- 创建用户heima, 可以在任意主机访问该数据库, 密码123456;
create user 'heima'@'%' identified by '123456';

--  修改用户heima的访问密码为1234;
alter user 'heima'@'%' identified with mysql_native_password by '1234';

-- 删除 itcast@localhost 用户
drop user 'itcast'@'localhost';
~~~



## 权限控制

**常用权限：**

~~~sql
ALL, ALL PRIVILEGES		-- 所有权限
SELECT				-- 查询数据				
INSERT				-- 插入数据
UPDATE				-- 修改数据
DELETE				-- 删除数据
ALTER				-- 修改表
DROP				-- 删除数据库/表/视图
CREATE				-- 创建数据库/表
~~~



**查询权限:**

~~~sql
SHOW GRANTS FOR '用户名'@'主机名' ;
~~~

**授予权限:**

~~~sql
GRANT 权限列表 ON 数据库名.表名 TO '用户名'@'主机名'; 
~~~

**撤销权限:**

~~~sql
REVOKE 权限列表 ON 数据库名.表名 FROM '用户名'@'主机名';
~~~



-  多个权限之间，使用逗号分隔
- 授权时， 数据库名和表名可以使用 * 进行通配，代表所有。



~~~sql
--  查询 'heima'@'%' 用户的权限
show grants for 'heima'@'%';

-- 授予 'heima'@'%' 用户itcast数据库所有表的所有操作权限
grant all on itcast.* to 'heima'@'%';

-- 撤销 'heima'@'%' 用户的itcast数据库的所有权限
revoke all on itcast.* from 'heima'@'%';
~~~



# 函数



函数 是指一段可以直接被另一段程序调用的程序或代码。

MySQL中的函数主要分为以下四类： 字符串函数、数值函数、日期函数、流程函数。



## 字符串函数

- **CONCAT(S1,S2,...Sn)**  ：字符串拼接，将S1，S2，... Sn拼接成一个字符串
- **LOWER(str)**  ： 将字符串str全部转为小写

- **UPPER(str)** ：将字符串str全部转为大写
- **LPAD(str,n,pad)**：左填充，用字符串pad对str的左边进行填充，达到n个字符串长度
- **RPAD(str,n,pad)**：右填充，用字符串pad对str的右边进行填充，达到n个字符串长度
- **TRIM(str)** ：去掉字符串头部和尾部的空格：
- **SUBSTRING(str,start,len)** ：返回从字符串str从start位置起的len个长度的字符串

~~~sql
-- 企业员工的工号，统一为5位数，目前不足5位数的全部在前面补0
update emp set workno = lpad(workno, 5, '0'); 1
~~~



## 数值函数

- **CEIL(x)** ：向上取整

- **FLOOR(x)** ：向下取整

- **MOD(x,y)** ：返回x/y的模

- **RAND()** ：返回0~1内的随机数

- **ROUND(x,y)** ：求参数x的四舍五入的值，保留y位小数



通过数据库的函数，生成一个六位数的随机验证码：

~~~sql
-- 思路： 获取随机数可以通过rand()函数，但是获取出来的随机数是在0-1之间的，所以可以在其基础上乘以1000000，然后舍弃小数部分，如果长度不足6位，补0
select lpad(round(rand()*1000000 , 0), 6, '0');
~~~



## 日期函数

**CURDATE()** ：返回当前日期

**CURTIME()** ：返回当前时间

**NOW()** ：返回当前日期和时间

**YEAR(date)** ：获取指定date的年份

**MONTH(date)** ：获取指定date的月份

**DAY(date)** ：获取指定date的日期

**DATE_ADD(date, INTERVAL exprtype)**：返回一个日期/时间值加上一个时间间隔expr后的时间值

**DATEDIFF(date1,date2)**：返回起始时间date1 和 结束时间date2之间的天数

~~~sql
--  date_add：增加指定的时间间隔
select date_add(now(), INTERVAL 70 YEAR ); 
-- datediff：获取两个日期相差的天数
select datediff('2021-10-01', '2021-12-01');
~~~



查询所有员工的入职天数，并根据入职天数倒序排序

~~~sql
-- 思路： 入职天数，就是通过当前日期 - 入职日期，所以需要使用datediff函数来完成。
select name, datediff(curdate(), entrydate) as 'entrydays' from emp order by entrydays desc;
~~~



## 流程函数

- **IF(value , t , f)**：如果value为true，则返回t，否则返回false

- **IFNULL(value1 , value2)**：如果value1不为空，返回value1，否则返回value2

- **CASE WHEN [ val1 ] THEN [res1] ...ELSE [ default ] END** ：如果val1为true，返回res1，... 否则返回default默认值

- **CASE [ expr ] WHEN [ val1 ] THEN[res1] ... ELSE [ default ] END** ：如果expr的值等于val1，返回res1，... 否则返回default默认值

~~~sql
-- 查询emp表的员工姓名和工作地址 (北京/上海 ----> 一线城市 , 其他 ----> 二线城市)
--  case when then else end
select name,
( case workaddress when '北京' then '一线城市' when '上海' then '一线城市' else
'二线城市' end ) as '工作地址'
from emp;
~~~



# 约束

**概念：**约束是作用于表中字段上的规则，用于限制存储在表中的数据。

**目的**：保证数据库中数据的正确、有效性和完整性



**分类：**

~~~sq
NOT NULL		-- 非空约束 ， 限制该字段的数据不能为null 
UNIQUE		-- 唯一约束 ，保证该字段的所有数据都是唯一、不重复的 
PRIMARY	KEY	-- 主键约束 ，主键是一行数据的唯一标识，要求非空且唯一
DEFAULT			-- 默认约束 ，保存数据时，如果未指定该字段的值，则采用默认值 
CHECK		-- 检查约束(8.0.16版本之后)，保证字段值满足某一个条件 
FOREIGN KEY			-- 外键约束，用来让两张表的数据之间建立连接，保证数据的一致性和完整性
~~~

- 约束是作用于表中字段上的，可以在创建表/修改表的时候添加约束



## 外键约束

外键：用来让两张表的数据之间建立连接，从而保证数据的一致性和完整性。

**语法：**

~~~sql
--  添加外键
ALTER TABLE 表名 ADD CONSTRAINT 外键名称 FOREIGN KEY (外键字段名) REFERENCES 主表 (主表列名) ;
-- 删除外键
ALTER TABLE 表名 DROP FOREIGN KEY 外键名称;

CREATE TABLE 表名(
字段名 数据类型,
...
[CONSTRAINT] [外键名称] FOREIGN KEY (外键字段名) REFERENCES 主表 (主表列名)
);

-- 为emp表的dept_id字段添加外键约束,关联dept表的主键id。
alter table emp add constraint fk_emp_dept_id foreign key (dept_id) references dept(id);
~~~



## 删除/更新行为

添加了外键之后，再删除父表数据时产生的约束行为，我们就称为删除/更新行为。

具体的删除/更新行为有以下几种:

- **NO ACTION**：当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则不允许删除/更新。 (与 RESTRICT 一致) 默认行为

- **RESTRICT**：当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则不允许删除/更新。 (与 NO ACTION 一致) 默认行为

- **CASCADE**：当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有，则也删除/更新外键在子表中的记录。

- **SET NULL**：当在父表中删除对应记录时，首先检查该记录是否有对应外键，如果有则设置子表中该外键值为null（这就要求该外键允许取null）。

- **SET DEFAULT**：父表有变更时，子表将外键列设置成一个默认的值 (Innodb不支持)



**语法：**

~~~sql
ALTER TABLE 表名 ADD CONSTRAINT 外键名称 FOREIGN KEY (外键字段) REFERENCES 主表名 (主表字段名) 
ON UPDATE CASCADE ON DELETE CASCADE;
~~~



# 多表查询

**多表关系：**项目开发中，在进行数据库表结构设计时，会根据业务需求及业务模块之间的关系，分析并设计表结构，由于业务之间相互关联，所以各个表结构之间也存在着各种联系，基本上分为三种：

- **一对多(多对一)**：
  - 案例: 部门 与 员工的关系
  - 关系: 一个部门对应多个员工，一个员工对应一个部门
  - 实现: 在多的一方建立外键，指向一的一方的主键

- **多对多**
  - 案例: 学生 与 课程的关系
  - 关系: 一个学生可以选修多门课程，一门课程也可以供多个学生选择
  - 实现: 建立第三张中间表，中间表至少包含两个外键，分别关联两方主键
- **一对一**
  - 案例: 用户 与 用户详情的关系
  - 关系: 一对一关系，多用于单表拆分，将一张表的基础字段放在一张表中，其他详情字段放在另一张表中，以提升操作效率
  - 实现: 在任意一方加入外键，关联另外一方的主键，并且设置外键为唯一的(UNIQUE)



多表查询就是指从多张表中查询数据。

原来查询单表数据，执行的SQL形式为：select * from emp;

那么我们要执行多表查询，就只需要使用逗号分隔多张表即可，如： `select * from emp , dept;`  但此时会存在笛卡尔积错误



**笛卡尔积:** 笛卡尔乘积是指在数学中，两个集合A集合 和 B集合的所有组合情况。

而在多表查询中，我们是需要消除无效的笛卡尔积的，只保留两张表关联部分的数据

在SQL语句中，如何来去除无效的笛卡尔积呢？ 我们可以给多表查询加上连接查询的条件即可

`select * from emp , dept where emp.dept_id = dept.id;`



**多表查询分类：**

- 连接查询
  - 内连接：相当于查询A、B交集部分数据
  - 外连接：左外连接：查询左表所有数据，以及两张表交集部分数据
  - 右外连接：查询右表所有数据，以及两张表交集部分数据
  - 自连接：当前表与自身的连接查询，自连接必须使用表别名

- 子查询



## 内连接

内连接查询的是两张表交集部分的数据。

内连接的语法分为两种: 隐式内连接、显式内连接。

~~~sql
-- 隐式内连接
SELECT 字段列表 FROM 表1 , 表2 WHERE 条件 ... ; 
-- 显式内连接
SELECT 字段列表 FROM 表1 [ INNER ] JOIN 表2 ON 连接条件 ... ;
~~~



~~~sql
-- 查询每一个员工的姓名 , 及关联的部门的名称 (隐式内连接实现)
select e.name,d.name from emp e , dept d where e.dept_id = d.id;
--  (显式内连接实现) --- INNER JOIN ...ON ...
select e.name, d.name from emp e join dept d on e.dept_id = d.id;
~~~

- 一旦为表起了别名，就不能再使用表名来指定对应的字段了，此时只能够使用别名来指定字段。



## 外连接

外连接分为两种，分别是：左外连接 和 右外连接。

左外连接相当于查询表1(左表)的所有数据，当然也包含表1和表2交集部分的数据

右外连接相当于查询表2(右表)的所有数据，当然也包含表1和表2交集部分的数据。

~~~sql
--  左外连接
SELECT 字段列表 FROM 表1 LEFT [ OUTER ] JOIN 表2 ON 条件 ... ; 
-- 右外连接
SELECT 字段列表 FROM 表1 RIGHT [ OUTER ] JOIN 表2 ON 条件 ... ;
~~~



~~~sql
-- 查询emp表的所有数据, 和对应的部门信息
-- 由于需求中提到，要查询emp的所有数据，所以是不能内连接查询的，需要考虑使用外连接查询。
-- 表结构: emp, dept
-- 连接条件: emp.dept_id = dept.id
select e.*, d.name from emp e left outer join dept d on e.dept_id = d.id;

-- 查询dept表的所有数据, 和对应的员工信息(右外连接)
-- 由于需求中提到，要查询dept表的所有数据，所以是不能内连接查询的，需要考虑使用外连接查询
select d.*, e.* from emp e right outer join dept d on e.dept_id = d.id;
select d.*, e.* from dept d left outer join emp e on e.dept_id = d.id;
~~~

- 左外连接和右外连接是可以相互替换的，只需要调整在连接查询时SQL中，表结构的先后顺序就可以了。而我们在日常开发使用时，更偏向于左外连接



## 自连接

**自连接查询：**自连接查询，顾名思义，就是自己连接自己，也就是把一张表连接查询多次。

**语法：**

~~~sql
SELECT 字段列表 FROM 表A 别名A JOIN 表A 别名B ON 条件 ... ;
~~~

- 对于自连接查询，可以是内连接查询，也可以是外连接查询。



~~~sql
--  查询员工 及其 所属领导的名字
select a.name , b.name from emp a , emp b where a.managerid = b.id; 

--  查询所有员工 emp 及其领导的名字 emp , 如果员工没有领导, 也需要查询出来
select a.name '员工', b.name '领导' from emp a left join emp b on a.managerid = b.id;
~~~

- 在自连接查询中，必须要为表起别名，要不然我们不清楚所指定的条件、返回的字段，到底是哪一张表的字段。



## 联合查询

对于union查询，就是把多次查询的结果合并起来，形成一个新的查询结果集。

~~~sql
SELECT 字段列表 FROM 表A ...
UNION [ ALL ]
SELECT 字段列表 FROM 表B ....
~~~

- 对于联合查询的多张表的列数必须保持一致，字段类型也需要保持一致
- union all 会将全部的数据直接合并在一起，union 会对合并之后的数据去重。

~~~sql
-- 将薪资低于 5000 的员工 , 和 年龄大于 50 岁的员工全部查询出来.
-- 当前对于这个需求，我们可以直接使用多条件查询，使用逻辑运算符 or 连接即可。也可以通过union/union all来联合查询
select * from emp where salary < 5000
union 
select * from emp where age > 50;
~~~

- 如果多条查询语句查询出来的结果，字段数量不一致，在进行union/union all联合查询时，将会报错。



## 子查询

SQL语句中嵌套SELECT语句，称为嵌套查询，又称子查询

~~~sql
SELECT * FROM t1 WHERE column1 = ( SELECT column1 FROM t2 );
~~~

- 子查询外部的语句可以是INSERT / UPDATE / DELETE / SELECT 的任何一个。



**分类：**

根据子查询结果不同，分为：

- 标量子查询（子查询结果为单个值）

-  列子查询(子查询结果为一列)

- 行子查询(子查询结果为一行)

- 表子查询(子查询结果为多行多列)



根据子查询位置，分为：

- WHERE之后

-  FROM之后

- SELECT之后



### 标量子查询

子查询返回的结果是单个值（数字、字符串、日期等），最简单的形式，这种子查询称为标量子查询。

常用的操作符：= <> > >= < <= 

~~~sql
-- 查询 "销售部" 的所有员工信息
-- 完成这个需求时，我们可以将需求分解为两步：
-- 1： 查询 "销售部" 部门ID
-- 2：根据 "销售部" 部门ID, 查询员工信息
select * from emp where dept_id = (select id from dept where name = '销售部');
~~~



### 列子查询

子查询返回的结果是一列（可以是多行），这种子查询称为列子查询

常用的操作符：IN 、NOT IN 、 ANY 、SOME 、 ALL

- **IN** ：在指定的集合范围之内，多选一

- **NOT IN** ：不在指定的集合范围之内

- **ANY** ：子查询返回列表中，有任意一个满足即可

- **SOME** ：与ANY等同，使用SOME的地方都可以使用ANY

- **ALL** ：子查询返回列表的所有值都必须满足

~~~sql
-- 查询比 财务部 所有人工资都高的员工信息
-- 1:查询所有 财务部 人员工资
-- 2:比 财务部 所有人工资都高的员工信息
select * from emp where salary > all ( select salary from emp where dept_id =
(select id from dept where name = '财务部') );
~~~



### 行子查询

子查询返回的结果是一行（可以是多列），这种子查询称为行子查询

常用的操作符：= 、<> 、IN 、NOT IN

~~~sql
-- 查询与 "张无忌" 的薪资及直属领导相同的员工信息 ;
-- 1：查询 "张无忌" 的薪资及直属领导
-- 2：查询与 "张无忌" 的薪资及直属领导相同的员工信息 ;
select * from emp where (salary,managerid) = (select salary, managerid from emp
where name = '张无忌');
~~~



### 表子查询

子查询返回的结果是多行多列，这种子查询称为表子查询。

常用的操作符：IN

~~~sql
--  查询与 "鹿杖客" , "宋远桥" 的职位和薪资相同的员工信息
-- 1：查询 "鹿杖客" , "宋远桥" 的职位和薪资
-- 2：查询与 "鹿杖客" , "宋远桥" 的职位和薪资相同的员工信息
select * from emp where (job,salary) in ( select job, salary from emp where name =
'鹿杖客' or name = '宋远桥' );

--   查询入职日期是 "2006-01-01" 之后的员工信息 , 及其部门信息
-- 1：入职日期是 "2006-01-01" 之后的员工信息
-- 2： 查询这部分员工, 对应的部门信息;
select e.*, d.* from (select * from emp where entrydate > '2006-01-01') e left
join dept d on e.dept_id = d.id ;
~~~



### 相关子查询

**非相关子查询**的执行不依赖与外部的查询。

- 先执行子查询，再执行外部查询

- 执行子查询，其结果不被显示，而是传递给外部查询，作为外部查询的条件使用。 

- 非相关子查询是独立于外部查询的子查询，子查询总共执行一次，执行完毕后将值传递给外部查询。
- 相关子查询的执行依赖于外部查询的数据，外部查询执行一行，子查询就执行一次。故非相关子查询比相关子查询效率高。



**相关子查询**的执行依赖于外部查询。**多数情况下是子查询的WHERE子句中引用了外部查询的表**。

- 执行过程：
  （1）从外层查询中取出一个元组，将元组相关列的值传给内层查询。
  （2）执行内层查询，得到子查询操作的值。
  （3）外查询根据子查询返回的结果或结果集得到满足条件的行。
  （4）然后外层查询取出下一个元组重复做步骤1-3，直到外层的元组全部处理完毕。 　　



**EXISTS 与 NOT EXISTS关键字**

关联子查询通常也会和EXISTS操作符一起来使用,用来检查在子查询中是否存在满足条件的行

**EXISTS：**

- 如果在子查询中不存在满足条件的行:
  - 条件返回FALSE
  - 继续在子查询中查找

- 如果在子查询中存在满足条件的行:
  - 不再子查询中继续查找
  - 条件返回TRUE

**NOT EXISTS** ：

表示如果不存在,,返回TRUE ,否则返回FALSE

- 如果在子查询中存在满足条件的行:
  - 条件返回FALSE
  - 继续在子查询中查找

- 如果在子查询中不存在满足条件的行:
  - 不再子查询中继续查找
  - 条件返回TRUE

~~~sql
# 查找选修了‘2’号课程的学生的姓名
SELECT Sname FROM Student WHERE  # student表中的值与子查询一个个匹配 --> 子查询执行了m次（m为student表中的记录数
     Sno=  (
                 SELECT SC.Sno  
                 FROM SC
                 WHERE SC.Sno=Student.Sno #从语义上看，此处判定验证查询的结果是否与Student.Sno相等，与判定查询结果是否											为空是等价的
                             AND Cno=‘2’
              )
    
# 查询没有选修2号课程的学生姓名
SELECT Sname FROM Student WHERE   # student表中的值与子查询一个个匹配
    NOT EXISTS  (   # 如果存在（即选修了2号课程，返回false，继续执行，不存在（即没有选秀二号课程，返回true
                   SELECT SC.Sno 
                   FROM SC
                   WHERE SC.Sno=Student.Sno 
                             AND Cno=‘2’
                  )

# 查询所有学生都选修了的课程的学分
select cno, credit from course c where 
not exists ( #子查询1 子查询2返回false时，子查询1返回true
    select sno from student s where 
    not exists (#子查询2
        #某课程存在sc.sno = s.sno，返回false继续匹配，当所有学生都选修了某课程时，子查询2返回false
        select sno,cno from  sc where  sc.cno = c.cno and  sc.sno = s.sno
                                        )
);
~~~





# 事务

事务 是一组操作的集合，它是一个不可分割的工作单位，事务会把所有的操作作为一个整体一起向系

统提交或撤销操作请求，即这些操作要么同时成功，要么同时失败。

就比如: 张三给李四转账1000块钱，张三银行账户的钱减少1000，而李四银行账户的钱要增加

1000。 这一组操作就必须在一个事务的范围内，要么都成功，要么都失败。

- 正常情况: 转账这个操作, 需要分为以下这么三步来完成 , 三步完成之后, 张三减少1000, 而李四增加1000, 转账成功 

- 异常情况: 转账这个操作, 也是分为以下这么三步来完成 , 在执行第三步是报错了, 这样就导致张三减少1000块钱, 而李四的金额没变, 这样就造成了数据的不致, 就出现问题了。

为了解决上述的问题，就需要通过数据的事务来完成，我们只需要在业务逻辑执行之前开启事务，执行

完毕后提交事务。如果执行过程中报错，则回滚事务，把数据恢复到事务开始之前的状态

默认MySQL的事务是自动提交的，也就是说，当执行完一条DML语句时，MySQL会立即隐

式的提交事务。



**语法：**

~~~sql
--  查看/设置事务提交方式
SELECT @@autocommit ;
SET @@autocommit = 0 ;
-- 提交事务
COMMIT;
--  回滚事务
ROLLBACK;
-- 开启事务
START TRANSACTION 或 BEGIN ;
~~~

- 上述的这种方式，我们是修改了事务的自动提交行为, 把默认的自动提交修改为了手动提交, 此时我们执行的DML语句都不会提交, 需要手动的执行commit进行提交。



**事务的四大特性：**

- 原子性（Atomicity）：事务是不可分割的最小操作单元，要么全部成功，要么全部失败。

- 一致性（Consistency）：事务完成时，必须使所有的数据都保持一致状态。

- 隔离性（Isolation）：数据库系统提供的隔离机制，保证事务在不受外部并发操作影响的独立环境下运行。

- 持久性（Durability）：事务一旦提交或回滚，它对数据库中的数据的改变就是永久的。

上述就是事务的四大特性，简称ACID。



**并发事务问题：**

-  赃读：一个事务读到另外一个事务还没有提交的数据
-  不可重复读：一个事务先后读取同一条记录，但两次读取的数据不同，称之为不可重复读。
- 幻读：一个事务按照条件查询数据时，没有对应的数据行，但是在插入数据时，又发现这行数据已经存在，好像出现了 "幻影"



**事务的隔离级别：**

**隔离级别** 

~~~
							脏读		不可重复读		 幻读

Read uncommitted 			√ 			√ 			√
	
Read committed				× 			√ 			√

Repeatable Read(默认)		   × 			× 			√

Serializable				× 			× 			×
~~~



~~~sql
--  查看事务隔离级别
SELECT @@TRANSACTION_ISOLATION;
--  设置事务隔离级别
SET [ SESSION | GLOBAL ] TRANSACTION ISOLATION LEVEL { READ UNCOMMITTED |READ COMMITTED | REPEATABLE READ | SERIALIZABLE }
~~~

- 事务隔离级别越高，数据越安全，但是性能越低。





























































































