---
title: MySQL知识详解
date: 2020-10-28 17:10:24
author: 小蝌蚪
img: 
coverImg: 
top: false
cover: false
toc: true
mathjax: false
summary: 介绍MySQL的详细安装使用
tags: [MySQL]
categories: [数据库]
comments: false
---





## [MySQL](https://www.mysql.com/)

mysql是一款免费的关系型数据库,它沿用了Linux的理念, 一切皆是文件, mysql底层其实也是文件,通过文件记录着各个数据. 另外,mysql跟linux一样, 对用户权限控制粒度划分很细,需要严格控制好root权限哦.

### 下载与安装配置

#### 下载

官网地址:  https://www.mysql.com/

 本人使用win系统,因此使用win版本的mysql记录下载教程,其余系统操作步骤一样,只是最后一步,切换系统,选择对应的mysql即可.

现在windows几乎都是64位操作系统,如果你使用的32位,直接第三步选择32位安装文件即可,此处以64位操作系统讲解

![mysql下载](https://gitee.com/tadpole145/images/raw/main/20210926134159.png)

##### mysql的版本说明

1.  MySQL Community Server 社区版本，开源免费，但不提供官方技术支持。
2. MySQL Enterprise Edition 企业版本，需付费，可以试用30天。
3. MySQL Cluster 集群版，开源免费。可将几个MySQL Server封装成一个Server。
4. MySQL Cluster CGE 高级集群版，需付费。
5. MySQL Workbench（GUITOOL）一款专为MySQL设计的ER/数据库建模工具。它是著名的数据库设计工具DBDesigner4的继任者。MySQLWorkbench又分为两个版本，分别是社区版（MySQL Workbench OSS）、商用版（MySQL WorkbenchSE）。



Generally Available（GA）Release 是指软件的通用版本，一般指正式发布的版本。

由于mysql8不再提供win64的msi安装包,故此处以5+版本做说明

- mysql-5.5.19-win32.msi，windows安装包，msi安装包是用msiexec安装完成的。windows下双击, 然后根据向导安装即可，跟安装微信一样简单方便。
- mysql-5.5.19.zip，这个是windows源文件，需要编译。不建议使用 
- mysql-5.5.19-win32.zip，这个文件解包后即可使用，是编译好的windows32位MySQL。需要手工配置, 32位,64位win系统都可以使用
- mysql-5.5.19-win64.zip , 64位win系统专用的文件压缩包,需要手工配置, 一般我们下载此版本



#### 安装

##### MSI文件安装配置

如果是下载的MSI安装文件, 跟着安装向导,一步步选择配置即可

##### zip文件安装与配置

此处以C盘安装配置做讲解,实际工作中,建议安装在非系统盘,本人一般习惯在D盘新建一个dev目录,安装开发用到的各种软件.

5+版本和8+版本手动安装配置操作一样, 步骤如下:  

1. 将加压后的mysql文件目录放在指定文件夹,此处的解压文件目录实际上就是安装目录.
2. 以管理员身份运行命令行窗口

![1727568-20190915194138600-1666145922](https://gitee.com/tadpole145/images/raw/main/20210926141131.jpg)

3. 切换路径到mysql的bin目录下

![1727568-20190915194319422-1269147823](https://gitee.com/tadpole145/images/raw/main/20210926141228.jpg)

4. 安装mysql的服务：mysqld --install

![1727568-20190915194355509-621408710](https://gitee.com/tadpole145/images/raw/main/20210926141949.jpg)

5. 初始化mysql，在这里，初始化会产生一个随机密码,如下图框框所示，记住这个密码，后面会用到(mysqld --initialize --console)

![1727568-20190915194537624-1288770126](https://gitee.com/tadpole145/images/raw/main/20210926142057.jpg)

6. 开启mysql的服务(net start mysql)

![1727568-20190915194809019-447378790](https://gitee.com/tadpole145/images/raw/main/20210926142138.jpg)

7. 登录验证，mysql是否安装成功！(要注意上面产生的随机密码，不包括前面符号前面的空格，否则会登陆失败)，如果和下图所示一样，则说明你的mysql已经安装成功！注意，，一定要先开启服务，不然会登陆失败，出现拒绝访问的提示符！！！

![1727568-20190915194855594-1799062186](https://gitee.com/tadpole145/images/raw/main/20210926142644.jpg)

8. 修改密码. 由于初始化产生的随机密码太复杂，，不便于我们登录mysql

![1727568-20190915195232011-1164270181](https://gitee.com/tadpole145/images/raw/main/20210926142741.jpg)

9. 配置全局变量, 建议配置在用户环境变量,而非系统环境变量

![1727568-20190915195658938-215279743](https://gitee.com/tadpole145/images/raw/main/20210926143040.jpg)

![1727568-20190915195808186-1920486822](https://gitee.com/tadpole145/images/raw/main/20210926143149.jpg)

10. 指定mysql存放数据的目录地址, 一般我建议就在当前安装目录下或者平级目录下新建一个mysqlData文件夹.
11. 创建mysql的配置文件;  在mysql目录下创建一个ini或cnf配置文件，在这里我创建的是ini配置文件，里面写的代码是mysql的一些基本配置

```ini
[mysqld]
# 设置3306端口
port=3306
# 设置mysql的安装目录
basedir=D:\mysql\mysql-8.0.17-winx64
# 设置mysql数据库的数据的存放目录, 根据第10步你创建的目录名字一样
datadir=D:\mysql\mysql-8.0.17-winx64\mysqlData
# 允许最大连接数
max_connections=200
# 允许连接失败的次数。
max_connect_errors=10
# 服务端使用的字符集默认为utf8mb4
character-set-server=utf8mb4
# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
# 默认使用“mysql_native_password”插件认证
#mysql_native_password
default_authentication_plugin=mysql_native_password
[mysql]
# 设置mysql客户端默认字符集
default-character-set=utf8mb4
[client]
# 设置mysql客户端连接服务端时默认使用的端口
port=3306
default-character-set=utf8mb4
```

![image-20210926144929477](https://gitee.com/tadpole145/images/raw/main/20210926144929.png)

#### 常用命令

```bash
# 1. 安装服务
mysqld --install
# 2. 初始化
mysqld --initialize --console
# 3. 开启服务
net start mysql
# 4. 关闭服务
net stop mysql
# 5. 删除服务
sc delete mysql
# 6.登录
mysql -u root -p
```





### 图形化界面工具

#### Navicat

一款收费的数据库视图工具.

建议使用之前,看下基本教程,此处教程略

#### sqlyog

一款免费的数据库视图工具,提供免费的社区版和收费的专业版, 访问[官网](https://www.webyog.com/)地址即可下载.

建议使用之前,看下基本教程,此处教程略

### 基本知识

mysql基础知识主要是了解相关基础概念与学会简单使用, 详细原理参考下面的进阶.

#### 数据库操作 [create]R[read]U[update]D[delete]

##### 连接数据库

mysql -u 账号 -p

![image-20210913154342947](https://gitee.com/tadpole145/images/raw/main/20210913154342.png)



##### 创建数据库

###### 图形化创建

![image-20210913140536761](https://gitee.com/tadpole145/images/raw/main/20210913140543.png)

###### sql语句创建表

```sql
CREATE DATABASE [IF NOT EXISTS] db_name [create_specification  create_specification]
```

示例: 

```sql
 CREATE DATABASE  IF NOT EXISTS db2 CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci
```

- [ xxx] 代表该参数可选,非必输入项;
- IF NOT EXISTS ,如果不添加, 当存在该数据库时候会报错,添加该语句后,如果存在,就不创建,也不会报错.
- db_name最好使用`xxx`, 使用反引号(win系统中的tab上面的那个键)能让表名更安全, 特别是涉及到关键字的时候,如果不使用反引号,是会报错的.
- create_specification是创建数据库的参数,对应图形化创建中的字符集与数据排序规则;
	- CHARACTER SET xxx
		- 默认字符集为utf8, 一般指定为utf8mb4, 它支持表情,是utf8的增强版
	- COLLATE  xxx
		- 默认utf8_bin, 区分大小写,当切换字符集后,一般选择utf8mb4_general_ci, 它不区分大小写.

##### 查看数据库

- 查看所有的数据库:  show databases;       注意,结尾带分号.

![image-20210913154519443](https://gitee.com/tadpole145/images/raw/main/20210913154519.png)

- 查看已经创建的数据库的定义信息:  show create database db_name

![image-20210913154103173](https://gitee.com/tadpole145/images/raw/main/20210913154103.png)

##### 使用数据库

```mysql
# 使用db2020这个数据库
use db2020;
```



##### 删除数据库

```sql
drop database [if exists] xxx;   #注意,结尾带分号.
```

![image-20210913154702914](https://gitee.com/tadpole145/images/raw/main/20210913154702.png)



#### 表操作

##### 创建

在指定数据库中,执行如下命令:

```mysql
create table xx (field dataType, field dataType,...,field dataType) character set 字符集 collate 校对规则 engine 引擎
```

- field,字段名,也就是列名, 有多种数据格式可选;
- character set ,默认使用所在数据库的字符集, 一般不会在表里面指定;
- 校对规则也是默认使用所在数据库的校对规则,一般不需要指定
- engine, 存在多种选项,默认是innodb.后面会详细介绍.

![image-20210913170929920](https://gitee.com/tadpole145/images/raw/main/20210913170929.png)

###### 关键字

comment, 解释字段

```mysql
create table tb_01(
	`id` bigint(20) not null unique auto_increment comment '编号',
    `user_name` varchar(20) not null default '' comment '用户名'
) comment ='用户表'
```



##### 字段数据类型

![image-20210913171831012](https://gitee.com/tadpole145/images/raw/main/20210913171831.png)

![image-20210914101253462](https://gitee.com/tadpole145/images/raw/main/20210914101253.png)

###### 符号整数

```sql
#默认有符号
create table t1 (param tinyint); 
#unsigned 无符号
create table t1 (param tinyint unsigned); 
```

###### 位类型bit[M]

- bit字段显示时, 按照位的方式显示;
- 如果只有0,1,可以考虑使用bit(1), 以节约空间
- M指位数,默认1, 最多64位.

![image-20210914105131428](https://gitee.com/tadpole145/images/raw/main/20210914105131.png)

###### Decimal[M,D]类型

表示列可以存储`D`位小数的`M`位数。

- 可以支持更加精确的小数位, M是总精度位数, D是小数点的位数,

- M最大65, 默认是10,  D最大30 ,默认是0;

	示例:

	```sql
	amount DECIMAL(6,2);
	# amount列最多可以存储6位数字，小数位数为2位; 因此，amount列的范围是从-9999.99到9999.99。
	```

	

###### 字符串的基本使用

- char(N)
	- 存储空间固定,最大为255==字符==(非字节)
	- N指存储的==字符==个数
	- 长度不够时内部存储使用**空格**填充。
	- 若字段本身末尾存在空格，检索出来自动截断末尾空格
	- 若字段本身前端存在空格，是不会截断的。
	- 当输入的字符长度超过指定长度时，char会截取超出的字符。并发出warning警告
	- 适用于固定长度的存储, 比如手机号,身份证号等

示例:  char(4), 指可以存储4个字符(中英文,数字,符号都算1个字符), 你存一个0,和存储"你好中国"占用的存储空间是一致的,因为存储空间在指定的时候就固定了.

![image-20210914113418121](https://gitee.com/tadpole145/images/raw/main/20210914113418.png)

关于char最大255长度是字符还是字节, 翻了一些资料,有人说是字节有人说是字符,最后只好自己亲自测试了. 最终确认是可以存储255个字符.

![image-20210914133653731](https://gitee.com/tadpole145/images/raw/main/20210914133653.png)

- vechar(N)

	- 存储空间不固定，根据字段长度动态决定。
	- N指存储的字符数, 数字字母中文符号均当做一个字符
	- 当指定字段长度后, 实际存储长度不足时,会根据实际位数分配位数.
	- 最大长度为 65535 字节, N代表存储的字符数,最大存储字符个数需要根据编码来确定
	- 最大长度 = 字符长度 + [长度记录：(1~3) B] + [null标志位：1B]
	- 需要额外的1~3个字节记录字符串的长度，字符串长度小于255字节使用1个字节，否则使用2个。

	例如： 当定义一个字段类型为 varchar(5)，表示最大支持存储5个字符串,存储时,数字字母汉字符号均被当做一个字符串, 同样是一个字符串, "a", 和字符串"中", 占用的存储空间是不一致的.

	当插入的值为"abc"这一字符串时，那么实际存储大小为3个字节，除此之外，varchar还需要使用1个额外字节（因为‘abc’字节数＜255）来记录字符串的长度。而当插入"abc中国"时, 如果是gbk编码(1汉字=2字节), 则中国占用4字节,总字节数是3+4+1=8字节,如果是utf8(1汉字=3字节),则"中国"占用6字节,总共占用3+6+1=10字节

	![image-20210914114243447](https://gitee.com/tadpole145/images/raw/main/20210914114243.png)



###### 添加自动更新当前时间

```sql
login_time TIMESTAMP  #字段名
NOT NULL DEFAULT CURRENT_TIMESTAMP
ON UPDATE CURRENT_TIMESTAMP
```



##### 修改

###### 增加指定位置的列

```sql
# 在emp表的resume列后面增加imgae列
ALTER TABLE emp ADD image VARCHAR(30) AFTER `resume`
```

###### 修改列宽度

```sql
#将name的宽度从20修改到100,并设置默认值
ALTER TABLE emp MODIFY `name` VARCHAR(100) NOT NULL DEFAULT ''
```

###### 修改列名

```mysql
ALTER TABLE emp1 CHANGE `name` user_name VARCHAR(50) NOT NULL DEFAULT ''
```



###### 修改表名

```mysql
RENAME TABLE emp TO emp1
```

###### 改变表字符集

```mysql
ALTER TABLE emp1 CHARSET utf8mb4
```



##### 查看

```mysql
#查看表结构, 表名为emp
DESC  emp
```



##### 删除

###### 删除指定列

```mysql
#示例: 删除emp表中的job列
ALTER TABLE emp DROP job
```

###### 删除表

```mysql
# drop 是直接删除表信息，速度最快，但是无法找回数据
drop table user;
```

###### 删除表数据

```mysql
# truncate 是删除表数据，不删除表的结构，速度排第二，但不能与where一起使用
truncate table user;
```

###### 删除表指定行

```mysql
#delete 是删除表中的数据，不删除表结构，速度最慢，但可以与where连用，可以删除指定的行
delete from user where user_id = 1;
```

###### 不同点

- 语句类型：delete语句是数据库操作语言（DML），truncate，drop是数据库定义语言（DDL）；

- 效率：一般来说 drop > truncate> delete；

- 是否删除表结构：truncate和delete 只删除数据不删除表结构，truncate 删除后将重建索引（新插入数据后id从0开始记起），而 delete不会删除索引 （新插入的数据将在删除数据的索引后继续增加），drop语句将删除表的结构包括依赖的约束，触发器，索引等；

- 安全性：drop和truncate删除时不记录MySQL日志，不能回滚，delete删除会记录MySQL日志，可以回滚；

- 返回值：delete 操作后返回删除的记录数，而 truncate 返回的是0或者-1（成功则返回0，失败返回-1）；



##### 表复制

###### 表数据复制

指将一个表中的数据,插入自己或者另外一张表的过程,

```mysql
-- 演示表之间复制, 将emp表中的五列数据复制到tab01中去
INSERT INTO tab01
(id, `name`, sal, job,deptno)
SELECT empno, ename, sal, job, deptno FROM emp;

-- 表的自我复制, 将tab01的数据复制一份插入自身表
INSERT INTO tab01
SELECT * FROM tab01;

```



###### 表结构复制

指把一个表的结构复制到另外一张表.

```mysql
-- 复制emp表的结构  
create table tab2 like emp;
```



示例:  删除一个表中重复的数据

```mysql
-- 方法有多种,此处举例一种我测试验证通过的

-- 人为增加重复数据
INSERT INTO dept 
SELECT * FROM dept
-- 1.创建临时表
CREATE TABLE temp LIKE dept

-- 2.通过distinct查询,将dept的数据复制到temp
INSERT INTO temp 
SELECT DISTINCT *  FROM dept

-- 3 删除原dept表
DROP TABLE dept
-- 4 将temp表改名为dept
RENAME TABLE temp TO dept
```





#### 行操作

##### 插入行数据

```mysql
# 插入一条记录
INSERT INTO emp1(id, user_name,sex, salary, `resume`) VALUES(8,'阿宝','女',8000,'会计');
#插入多条记录
INSERT INTO emp1(id, user_name,sex, salary, `resume`) VALUES(10,'小王','女',7000,'人事'),(9,'小张','男',9000,'开发');
```

注意事项

- 插入的数据类型应与对应字段类型一致
- 数据长度应在列规定的范围内
- values中的数据位置应与需要插入的列一一对应;
- 字符和日期类型数据应该包含在单引号中;
- 如果列未指定非空,则可以插入null
- 给表中所有字段添加内容,则可以不写前面的字段名;
- 一次插入多行数据时,只需要以逗号分隔,添加一份新数据即可.



##### 更新行操作

```mysql
update tb_name set col_name=expr1[, col_name2=expr2] [where condition]
```

示例:

```mysql
-- 1. 更新指定列的所有行  (一列)
UPDATE emp1 SET salary =5000
-- 2. 更新小王的薪水为8000 (一行)
UPDATE emp1 SET salary = 8000 WHERE user_name="小王"
-- 更新小张的生日为"1990-1-1", 薪水在原来的基础上加1K (多列一行)
UPDATE emp1 SET birthday="1990-1-1", salary=salary+1000 WHERE user_name="小张"
```

注意事项

- update可以更新指定列的所有行, 要慎用, 一般带着指定条件更新;
- 如果需要修改多个列,则可以通过字段set col1 =exp1, col2=expr2来指定;

##### 删除行

```mysql
delete from tb_name [where condition]
```

示例:

```mysql
-- 1. 删除指定行
DELETE FROM emp1 WHERE user_name='阿宝'
-- 2. 删除表所有的行
DELETE FROM emp1  等价于  truncate table emp1
```

注意事项:

- 删除语句应该带where条件,不然会删除整个表的数据
- 当删除表的所有数据时候, 不会删除表结构, 如果需要删除整表,参考表删除操作.

##### 查询语句

```mysql
-- 指定列名
select [distinct] *|[column1, column2, column3....] from tb_name
-- 通过表达式查询并排序
select * |[column1 | expr1 [as] col1, column2 | expr2 as col2...] from tb_name where conditions order by col_name asc|desc 
 
 -- 别名的使用, as可省略, 别名如果出现空格,需要双引号
SELECT id,`name` user_name FROM tb2
SELECT id, `name` AS "User Name" FROM tb2
SELECT id, `name`   "User Name" FROM tb2
```

注意事项:

- distinct, 除重, *代表所有列
- column指定列名,可以一次查询1或者多列;
- 可以通过表达式计算得出虚拟列;
- 可以通过as关键字自定义列的别名;
- 可以通过where进行条件过滤查询;
- asc(ascend )代表升序,默认不写, desc(descend)代表降序;
- group by为分组函数, 用于对查询结果进行分组统计, having可选, 代表使用子句对分组后的结果进行过滤.
- dual 是mysql中的亚元表,当没有表时,可以使用它替代.
- as 别名, 其中as可忽略, 别名如果是多个单词组成,则需要双引号括起来.



###### 函数

- 使用统计函数
- 求和函数
- 平均函数

- 分组函数

```mysql
-- 使用统计函数
select count(*)| count(列名) from student where conditions
-- 求和函数
SELECT SUM(math)[, sum(chinese),sum(english)...] FROM student;
-- 平均函数
SELECT AVG(math) FROM student;
-- 最大/最小函数
SELECT MAX(math) AS math_high_socre, MIN(math)   AS math_low_socre
FROM student;
-- 分组函数
select col1, col2,col3.. from tb_name group by colname [having conditions]

```

- 字符串函数

 ![image-20210922132135301](https://gitee.com/tadpole145/images/raw/main/20210922144132.png)

- 数学函数

![image-20210922144558443](https://gitee.com/tadpole145/images/raw/main/20210922144558.png)



-  时间日期函数

![image-20210922145816130](https://gitee.com/tadpole145/images/raw/main/20210922145816.png)

-  加密函数

![image-20210922171522816](https://gitee.com/tadpole145/images/raw/main/20210922171522.png)

> user(), 显示结果为:  用户@IP 地址, 可以查看用户名, ip

-  流程控制函数

![image-20210922171755993](https://gitee.com/tadpole145/images/raw/main/20210922171756.png)



部分示例

```mysql
--  统计每个学生的总分
SELECT `name`, (chinese+english+math) FROM student;
--  在所有学生总分加 10 分的情况
SELECT `name`, (chinese + english + math + 10) FROM student;
--  使用别名表示学生分数。
SELECT `name` AS '名字', (chinese + english + math + 10) AS total_score
FROM student;
-- 查询学生表中韩xx名字, 总分, 并以总分升序排列,
SELECT `name`, (chinese + english + math) AS total_score FROM student
WHERE `name` LIKE '韩%'
ORDER BY total_score asc;


-- 显示每个部门的平均工资和最高工资
SELECT MAX(sal), AVG(sal),deptno FROM emp GROUP BY deptno
-- 显示每个部门每种岗位的平均工资与最低工资
SELECT MAX(sal), AVG(sal),deptno, job FROM emp GROUP BY deptno,job
-- 显示平均工资低于 2000 的部门号和它的平均工资 
SELECT AVG(sal) AS avg_sal, deptno FROM emp GROUP BY deptno HAVING avg_sal <2000

-- 以首字母小写的方式显示所有员工emp表的姓名
-- 思路:  获取ename, 截取第一个字符转小写, 拼接后面的字符.
SELECT CONCAT(LCASE(SUBSTRING(ename,1,1)),   SUBSTRING(ename,2)) AS new_name FROM emp;
SELECT CONCAT(LCASE(LEFT(ename,1)),   SUBSTRING(ename,2)) AS new_name
FROM emp;
```

在 where 子句中经常使用的运算符

![image-20210922101218801](https://gitee.com/tadpole145/images/raw/main/20210922101218.png)



###### 模糊查询

使用like操作符

- %: 匹配多个字符
- _ :匹配单个字符

示例

```mysql
--   如何显示首字符为 S 的员工姓名和工资?
SELECT ename, sal FROM emp WHERE ename LIKE 'S%'
--   如何显示第三个字符为大写 O 的所有员工的姓名和工资?
SELECT ename, sal FROM emp WHERE ename LIKE '__O%'
--    如何显示没有上级的雇员的情况
SELECT * FROM emp WHERE mgr IS NULL;
```

###### 分页查询

```mysql
select ... limit start,rows
-- 表示从start+1开始,往后查询rows行数据..  
```

示例

```mysql
 -- 按雇员的 id 号升序取出，  每页显示 3 条记录，请显示  第 1  页
SELECT * FROM emp ORDER BY empno LIMIT 0, 3;
```

###### 分组查询

语法:

```mysql
select * from tb_name group by col1
```

注意事项

- 如果select语句同时包含group by, having, order by, limit , 那么它们之间的顺序是group by-->having--> order by-->limit , 如果顺序错乱会导致查询出错.

示例

```mysql
-- (1)  显示每种岗位的雇员总数、平均工资。
SELECT COUNT(*) as emp_total, AVG(sal) as avg_sal, job FROM emp GROUP BY job;
-- (2)  显示雇员总数，以及获得补助的雇员数。
SELECT COUNT(*), COUNT(comm) FROM emp
-- (3) 统计没有获得补助的雇员数
SELECT COUNT(*), COUNT(IF(comm IS NULL, 1, NULL))FROM emp
SELECT COUNT(*), COUNT(*) - COUNT(comm) FROM emp
-- (4)综合案例:  请统计各个部门的平均工资， 并且是大于 1000 ，并且按照平均工资从高到低排序然后取出前2条
SELECT deptno, AVG(sal) AS avg_sal FROM emp GROUP BY deptno HAVING   avg_sal > 1000 ORDER BY avg_sal DESC LIMIT 0,2
```



###### 多表查询

笛卡尔集: 多个表之间,由于查询条件的缺乏,导致每张表的每条数据都是对其它整表的映射. 比如A表有8条数据, B表5条, 如果没有任何条件限制,去查询

```mysql
select * from tableA, tableB;

```

这样会导致查询出来的数据有8*5=40条, 这就是所谓的笛卡尔集.

当涉及多表查询时, 查询条件不应该少于(表个数-1)个条件. 这样才能避免出现笛卡尔集.

示例

```mysql
-- 显示各个员工的姓名，工资，及其工资的级别
select ename, sal, grade from emp , salgrade where sal between losal and hisal;
```



**自连接**

> 同一张表的连接查询.(把一张表当做2张表)

示例

```mysql
-- 显示公司员工名字和他的上级的名字
SELECT worker.ename AS '职员名' ,   boss.ename AS '上级名' FROM emp worker, emp boss WHERE worker.mgr = boss.empno;
# 表取别名不需要as, 列取别名才需要.
```



###### 子查询

- 子查询:   指嵌入在其它 sql 语句中的 select 语句,也叫嵌套查询
- 单行单列子查询: 指只返回一行数据的子查询语句;

- 多行单列子查询:  指返回多行数据的子查询     使用关键字  in, all, any
- 临时表查询(多行多列)
- 一行多列子查询

示例

```mysql
-- (单行示例) 如何显示与 SMITH 同一部门的所有员工?
-- 1. 查询出smith所在的部门编号;  2. 根据编号查询所有的员工
SELECT * FROM emp
WHERE deptno = (
    SELECT deptno
    FROM emp
    WHERE ename = 'SMITH'
)

-- (多行示例) :如何查询和部门 10 的工作相同的雇员的名字、岗位、工资、部门号,  但是不含 10 号部门自己的雇员.
-- 思考:  1.查出10号部门的工种;  2. 查出指定列中工种IN 步骤一中的数据,  3. 排除10号部门
select ename, job, sal, deptno
from emp
where job in (
    SELECT DISTINCT job
    FROM emp
    WHERE deptno = 10
) and deptno != 10
# DISTINCT去重

--  查询scshop中各个类别中,价格最高的商品信息(id,类别,价格,商品名).
# 思路: 1.查询各个类别中价格最高的表;  2 查询商品信息表,  3,把步骤1,2得到的表当做临时表,用于匹配步骤2的表临时表的信息,取交集
select goods_id, ecs_goods.cat_id, goods_name,shop_price 
form (
	select cat_id , max(shop_price) as max_price 
    from ecs_goods group by cat_id
)where temp.cat_id = ecs_goods.cat_id and temp.max_price =ecs_goods.shop_price


-- 显示工资比部门 30 的所有员工的工资高的员工的姓名、工资和部门号
-- 思考:  1, 求出30部门的所有员工工资, 2 使用all操作符
SELECT ename, sal, deptno
FROM emp
WHERE sal > ALL(
    SELECT sal
    FROM emp
        WHERE deptno = 30
)
-- 方法二,  1,查出30部门的最高工资, 2, 查出大于最高工资即可.
SELECT ename, sal, deptno
FROM emp
WHERE sal > (
    SELECT MAX(sal)
    FROM emp
    WHERE deptno = 30
)

-- 如何显示工资比部门 30 的其中一个员工的工资高的员工的姓名、工资和部门号
-- 与上面一样, all关键字改为any即可, 或者求出部门30的工资的最小值

-- 一行多列, 多个列值相等
--  如何查询与 allen 的部门和岗位完全相同的所有雇员(并且不含 allen 本人)
SELECT *
FROM emp
WHERE (deptno , job) = (
        SELECT deptno , job
        FROM emp
        WHERE ename = 'ALLEN'
) AND ename != 'ALLEN'

```

##### 合并查询

主要用于where条件不太好写的时候, 使用不太多.

union all  : 不去重

union : 去重

```mysql
-- 示例
SELECT ename,sal,job FROM emp WHERE sal>2500 
UNION ALL
SELECT ename,sal,job FROM emp WHERE job='MANAGER'  
```

##### 表外连接

左外连接:  左侧表完全显示,就称之为左外连接;

右外连接:  右侧表完全显示, 就称之为右外连接;

```mysql
-- 语法如下
select... from table1 left join table2 on conditions
-- 示例,显示所有人的成绩，如果没有成绩，也要显示该人的姓名和 id 号,成绩显示为空
SELECT `name`, stu.id, grade
FROM stu LEFT JOIN exam
ON stu.id = exam.id;
```



#### 约束

用于确保数据库中的数据满足特定的商业规则,主要包括:

- not null
- unique
- primary key
- foreign key
- check

##### primary key约束

语法:    字段名  字段类型  primary key

用于标识唯一的行数据, 方便提升查询效率

- 当定义为主键后, 该字段不能为空,也不能重复;
- 一张表最多只能有一个主键, 可以是复合主键

```mysql
-- 示例
-- 1. 单一主键
id int primary key
-- 或者
id int,
primary key(id)
 
 
--  2. 复合主键
create table t1
(
	id int,
    user_name varchar(32),
    email varchar(32),
    primary key(id,user_name)
)
```

##### not null约束

非空,没啥知识点,

##### unique 约束

唯一,  代表不能重复,  但是null不是具体的值, 可以插入多个null.

 unique not null  使用效果类似  primary key, 区别在于unique可以有多个, 但是一张表只能有一个primary key.

#####  foreign key(外键)

用于定义主表和从表之间的约束关系, 外键约束定义在从表上

```mysql
-- 语法
foreign key(本表字段名) references 主表(字段名)
```

- 主表的主键必须是primary key 或者unique ;
- 表的引擎必须是Innodb才行;
- 外键字段类型应该与主键字段类型一致.
- 外键字段类型的值必须在主键中出现或者为null(前提是主键可为null);
- 一旦建立主外键关系,数据就不可随意删除了.

![image-20210923173423424](https://gitee.com/tadpole145/images/raw/main/20210923173423.png)



##### check约束

用于强制行数据必须满足的条件, mysql8.0.16以前只支持check语法, 并不会生效, 8.0.16版本后完全支持check约束.

```mysql
-- 示例
--  测试
CREATE TABLE t23 (
id INT PRIMARY KEY,
`name` VARCHAR(32) ,
sex VARCHAR(6) CHECK (sex IN('man','woman')),
sal DOUBLE CHECK ( sal > 1000 AND sal < 2000)
);
--  添加数据
INSERT INTO t23
VALUES(1, 'jack', 'mid', 1);
```



#### 自增长

```mysql
-- 语法
id int primary key auto_increment

-- 添加自增长字段的几种方式, 假设id字段自增长
insert into xxx(id, col2...) values (null, v2...)
insert into xxx(col2,col3....) values (v2, v3...)
insert into xxx values (null ,v2,v3....)
```

注意事项:

- 一般自增长和primary key/ unique搭配使用;
- 自增长修饰的字段为整形, 虽然小数类型也可,但是很少这样使用;
- 自增默认初始值为1, 也可以指定默认初始值
- 添加数据时, 如果指定了自增字段的值,则以指定值为准, 后面的数据在指定值基础上加1

```mysql
-- 指定自增初始值示例
CREATE TABLE t24
(id INT PRIMARY KEY AUTO_INCREMENT ,
`name` VARCHAR(32)NOT NULL DEFAULT ''
) AUTO_INCREMENT=100

--  修改默认的自增长开始值
ALTER TABLE t24 AUTO_INCREMENT = 10
```



#### 索引

给数据库的字段增加索引,会导致文件存储空间增加,但是会大大提高数据的查询效率. 因为数据查询是使用最多最频繁的操作.提升搜索效率有助于缓解服务器压力.

##### 原理

没有索引的搜索是全表扫描,会挨个对比,找到所需要的结果.使用索引后,会形成一个索引的二叉树(会增加存储空间), 搜索效率变成了$\log_2n$, 但是也会降低删除操作的效率.

##### 索引类型

1. 主键索引, primary key
2. 唯一索引,  unique
3. 普通索引 index
4. 全文索引 (适用于引擎为MyISAM), 开发中一般使用Solr和ElasticSearch框架

##### 索引使用

```mysql
--  添加索引
create [unique] index idx_name on tab_name(col_name[(length)]) [asc|desc]
alter table tab_name add index [idx_name] (col_name) #添加普通索引
alter table tab_name add primary key(col_name) #添加主键索引
-- 示例
create unique index id_idx on tab25(id)
CREATE TABLE t26 (
id INT ,
`name` VARCHAR(32));
ALTER TABLE t26 ADD PRIMARY KEY(id)

-- 删除索引
-- 删除普通索引
drop index idx_name on table tab_name
alter table tab_name drop index idx_name
-- 删除主键索引, 因为一张表最多只有一个主键索引
alter table tab_name drop primary key  

-- 修改索引 
1. 删除原索引
2. 增加新索引

-- 查询索引
show index from tab_name
show indexes from tab_name
show keys from tab_name
desc tab_name
```

注意事项

- 较频繁的作为条件查询字段应该创建索引,不会出现在条件查询中的字段不该创建索引
- 唯一性太差的字段不适合单独创建索引
- 更新频繁的字段不适合创建索引



#### 事务

事务是用于保证数据的一致性, 它由一组相关的dml(增删改)语句组成,该组操作要么全部成功,要么全部失败.

mysql数据库控制台事务的几个操作

```mysql
start transaction 或者 set autocommit = off  #开启一个事务
savepoint pointName  #设置备份点
rollback to pointName  #回滚到指定备份点
rollback   #回滚到开启事务点
commit  #提交事务, 也是结束事务
```

 注意事项:

- 如果不开启事务,默认情况下, dml操作是自动提交,不能回滚;
- 开启事务后, 在提交之前,可以指定回滚点, 不指定就默认回滚到开启事务的状态;
- 回滚只能向前,不能向后.  比如开启事务后,先后创建了A,B 二个备份点, 第一次回滚到A后, 是不能再次回滚到B的,因为在回滚到A时,已经把A后的数据给清除了.
- mysql的事务机制需要innodb的存储引擎才可以使用.



##### 事务的隔离级别

多个连接开启各自事务操作的数据库中的数据时,数据库系统要负责隔离操作, 以保证各个连接在获取数据时候的准确性.  如果不考虑隔离性,可能会产生脏读, 不可重复读, 幻读.

- 脏读

> 当一个事务读到了另外一个事务尚未提交的数据时,产生脏读.
>
> 大白话解释:  你看到了你不该看的东西,就是脏东西.

- 不可重复读

> 同一次查询在同一事务中多次进行,由于其它事务提交事务所做的修改或者删除,导致每次查询的结果不可重复

- 幻读

> 同一次查询在同一事务中多次进行,由于其它事务提交事务所做的插入操作,每次返回不同的结果集,此时发生幻读.

![image-20210924161409695](https://gitee.com/tadpole145/images/raw/main/20210924161409.png)



##### 隔离级别的设置

```mysql
-- 1.查询当前会话隔离级别
select @@tx_isolation
-- 2. 查看系统当前隔离级别
select @@global.tx_isolation
-- 3. 设置当前会话隔离级别, xxx代表上图4种级别
set session transaction isolation level xxx
-- 4. 设置全局会话隔离级别, xxx代表上图4种级别
set global transaction isolation level xxx
-- 5, 修改全局级别,也可以在mysql的安装目录的ini文件中添加设置
transaction-isolation =xxx
-- 6 repeatable read为mysql的默认级别,一般没必要修改
```

##### 事务ACID

- 原子性Atomicity : 指事务是一个不可分割的单位, 要么全部成功,要么全部失败;
- 一致性Consistency:  事务必须使数据库从一个一致性状态变动到另一个一致性状态;
- 隔离性Isolation:  多个用户并发访问数据库时,为了防止当前会话不被其他会话操作干扰;
- 持久性Durability: 事务一旦被提交,对数据库中的数据的改变就是永久性的.



#### 表类型与存储引擎

mysql表类型由存储引擎决定, 总共有6种

- csv
- memory
- archive
- mrg_myisam
- myisam
- innodb

![image-20210924174541539](https://gitee.com/tadpole145/images/raw/main/20210924174541.png)

注意事项:

- MyISAM不支持事务+外键,但是访问与插入速度快;
- InNoDB支持事务安全,插入效率比较低, 对比与MYISAM,由于其支持B+树索引,导致存储空间相对较大,但是查询效率很高.
- MEMORY在内存中创建表,访问速度非常快, 默认使用hash索引,但是一旦mysql服务关掉,其数据也会丢失, 表结构还在.



如何选择合适的引擎

1. 如果应用无需事务,优先选择MYISAM,速度快, 否则选择InNoDB;
2. Memory在内存中,无I/O等待, 适合存储变化频繁的不重要数据, 比如用户在线状态.

##### 修改引擎

```mysql
alter table tbName engine = engineName
```



#### 视图

视图是一个虚拟表,其内容来自于真实的表(基表),目的是为了控制访问权限. 比如某类角色只能访问用户表中的部分列字段.

- 视图不会生成一个新的表,只会生成一个对应的视图结构文件;

- 视图可以修改基表, 基表的变化也会引起视图的变化.
- 视图也可以再创建视图.



##### 视图的使用

```mysql
-- 视图的增删改查
create view view_name as select语句
alter view view_name as select语句
show create view view_name
drop view view_name1, view_name2
```

##### 视图的优点

- 安全.  视图可以隔离原基表的指定列数据,给予不同的权限的人,查看不同的字段.
- 性能.  关系数据库经常分表存储,如果使用外键建立这些表之间的关系,这样做比较麻烦而且查询效率比较低, 通过视图可以快速进行查询.
- 灵活. 系统中如果有旧表,由于存在设计缺陷即将被废弃,但是生产上有些地方使用到了这些表,不能轻易修改,此时可以新建视图将其映射到那么即将废弃的表,就可以安全的升级数据库.

#### mysql权限管理

##### 创建用户

```mysql
-- 创建用户,并指定可登录的ip
create user '用户名'@'允许登录的ip' identified by 'password'
-- 示例:
-- 1.只允许本机登录
CREATE USER 'jack'@'localhost' IDENTIFIED BY '123456'
-- 2.只允许某个指定的ip或者ip点登录, %代表通配符
CREATE USER 'lancy'@'192.168.%.%' IDENTIFIED BY '123456'
-- 3.任意ip登录,  
CREATE USER 'tom' IDENTIFIED BY '123456'
CREATE USER 'tom'@'%' IDENTIFIED BY '123456'


-- 删除某个用户, 注意删除必须与创建用户的ip一致
drop user '用户名'@'ip'
-- 1. 删除本机用户jack
drop user 'jack'@'localhost'
-- 2. 删除全网用户tom
drop user 'tom'


-- 用户密码修改
-- 1. 修改自己密码
set password = password('new password')
-- 2. 修改他人密码(需要有修改权限)
set password for '用户名'@'ip' = password('new pwd')
```

##### 权限列表

![image-20210926105419382](https://gitee.com/tadpole145/images/raw/main/20210926105419.png)

##### 授予用户指定权限

```mysql
-- 授予权限基本语法, 对象包含表,视图,存储过程等
grant 权限 on 数据库.对象 to '用户名'@'ip' [identified by '用户密码']
  -- 1. 多个权限,使用逗号分隔
  grant select[, delete,create...] on....
  -- 2. 授予所有权限, 使用all
   grant all on ...
  -- 3 *代表通配符, *.*代表本系统中所有数据库的所有对象, 库.*代表该库的所有对象
  
  
  -- 回收用户授权
  revoke 权限列表 on 库.对象 from '用户名'@'ip'
  -- 5.7以后版本权限立马生效, 在早期版本,权限执行后,并没有立马生效,可以执行指令
   flush privileges
   
```









#### 备份与恢复

介绍下命令行操作, 图形化操作比较简单, 备份的时候,选中数据库,导出即可, 恢复的时候,执行备份sql即可,

##### 备份

###### 数据库备份

在==dos==执行命令:  

```bash
mysqldump -u 用户名 -p -B 数据库1 数据库2 数据库n > 文件名绝对地址
```

示例如下: 

![image-20210913160856354](https://gitee.com/tadpole145/images/raw/main/20210913160856.png)

###### 表备份

在dos命令窗口输入命令: 

```bash
mysqldump -u 用户名 -p 数据库 表1 表2 表n > 备份文件绝对地址
```

示例如下

![image-20210913161520210](https://gitee.com/tadpole145/images/raw/main/20210913161520.png)

##### 恢复

数据库与表恢复操作一样

进入mysql后,再执行如下命令:  

```mysql
source 备份sql绝对路径
```

![image-20210913162628250](https://gitee.com/tadpole145/images/raw/main/20210913162628.png)

 

 









#### 连接配置

```properties
#mysql数据库连接-------驱动依赖为8+版本
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/数据库名?serverTimezone=GMT%2B8&characterEncoding=utf-8
spring.datasource.username=root
spring.datasource.password=密码

# mysql驱动的依赖的版本为5+版本
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/数据库名?characterEncoding=utf-8&useSSL=true
spring.datasource.username=root
spring.datasource.password=密码
```



### mysql进阶

进阶部分,介绍mysql原理性的东西以及实现方法.

#### mysql的索引

索引的本质是数据结构,目的是为了高效的获取数据.
