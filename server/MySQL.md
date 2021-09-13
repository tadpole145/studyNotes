## MySQL



### 安装与配置

#### 5.7版本 

#### 8.0+版本

### 图形化界面工具

#### Navicat

#### sqlyog

### 基本使用

#### 数据库操作

##### 连接数据库

mysql -u 账号 -p

![image-20210913154342947](https://gitee.com/tadpole145/images/raw/main/20210913154342.png)



##### 创建数据库

###### 图形化创建

![image-20210913140536761](https://gitee.com/tadpole145/images/raw/main/20210913140543.png)

###### sql语句创建表

CREATE DATABASE [IF NOT EXISTS] db_name [create_specification  create_specification]

示例: 

>  CREATE DATABASE  IF NOT EXISTS db2 CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci

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

##### 删除数据库

drop database [if exists] xxx;   注意,结尾带分号.

![image-20210913154702914](https://gitee.com/tadpole145/images/raw/main/20210913154702.png)



#### 表操作

##### 创建

在指定数据库中,执行如下命令:

create table xx (field dataType, field dataType,...,field dataType) character set 字符集 collate 校对规则 engine 引擎

- field,字段名,也就是列名, 有多种数据格式可选;
- character set ,默认使用所在数据库的字符集, 一般不会在表里面指定;
- 校对规则也是默认使用所在数据库的校对规则,一般不需要指定
- engine, 存在多种选项,默认是innodb.后面会详细介绍.

![image-20210913170929920](https://gitee.com/tadpole145/images/raw/main/20210913170929.png)

##### 字段数据类型

![image-20210913171831012](https://gitee.com/tadpole145/images/raw/main/20210913171831.png)



##### 查看

##### 删除



#### 备份与恢复

介绍下命令行操作, 图形化操作比较简单, 备份的时候,选中数据库,导出即可, 恢复的时候,执行备份sql即可,

##### 备份

###### 数据库备份

在==dos==执行命令:  mysqldump -u 用户名 -p -B 数据库1 数据库2 数据库n > 文件名绝对地址

示例如下: 

![image-20210913160856354](https://gitee.com/tadpole145/images/raw/main/20210913160856.png)

###### 表备份

在dos命令窗口输入命令: mysqldump -u 用户名 -p 数据库 表1 表2 表n > 备份文件绝对地址

示例如下

![image-20210913161520210](https://gitee.com/tadpole145/images/raw/main/20210913161520.png)

##### 恢复

数据库与表恢复操作一样

进入mysql后,再执行如下命令:  source 备份sql绝对路径

![image-20210913162628250](https://gitee.com/tadpole145/images/raw/main/20210913162628.png)

 

#### 









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

