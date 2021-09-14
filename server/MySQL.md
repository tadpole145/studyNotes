## MySQL

mysql是一款免费的关系型数据库,它沿用了Linux的理念, 一切皆是文件, mysql底层也是文件,记录着各个数据.

### 安装与配置

#### 5.7版本 

#### 8.0+版本

### 图形化界面工具

#### Navicat

#### sqlyog

### 基本知识

#### 数据库操作 C[create]R[read]U[update]D[delete]

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



#### 行操作

###### 插入行数据

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

