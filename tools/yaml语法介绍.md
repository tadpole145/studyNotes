

[官网地址](https://yaml.org/spec/)

#### 概念

官方给出的定义: YAML 是 "YAML Ain't a Markup Language"（YAML 不是一种标记语言）的递归缩写。在开发的这种语言时，YAML 的意思其实是："Yet Another Markup Language"（仍是一种标记语言）。

我的理解是, yaml同md,xml,一样,都属于一种标记语言,都有自己的语法规则. 其文件的后缀为.yml/ .yaml

#### 为什么要学习使用yaml?

相比于xml,它更加简洁,而且功能更强大,特别适合用来表达或编辑数据结构、各种配置文件、倾印调试内容、文件大纲, 并且也越来也流行.

#### 基本语法规则

- 大小写敏感
- 冒号后面必须要有一个空格
- 使用缩进表示层级关系
- 缩进不允许使用tab，只允许空格
- 缩进的空格数不重要，只要相同层级的元素左对齐即可
- '#'表示注释
- ${} 占位符

#### 数据类型

YAML 支持以下几种数据类型：

- 对象：键值对的集合，又称为映射（mapping）/ 哈希（hashes） / 字典（dictionary）
- 数组：一组按次序排列的值，又称为序列（sequence） / 列表（list）
- 纯量（scalars）：单个的、不可再分的值

#### YAML对象

1. 使用冒号结构表示 **key: value**，冒号后面要加一个空格。
2. 使用 **key:{key1: value1, key2: value2, ...}**。
3. 使用缩进表示层级关系；

```yaml
key: 
    child-key: value1
    child-key2: value2
```

#### YAML数组

以 **-** 开头的行表示构成一个数组：

```yaml
- A
- B
- C
```

YAML 支持多维数组，可以使用行内表示：

```yaml
key: [value1, value2, ...]
```

数据结构的子成员是一个数组，则可以在该项下面缩进一个空格。

```
-
 - A
 - B
 - C
```

示例:

```yaml
companies:
    -
        id: 1
        name: company1
        price: 200W
    -
        id: 2
        name: company2
        price: 500W
        
 # 等价于       
companies: [{id: 1,name: company1,price: 200W},{id: 2,name: company2,price: 500W}]        
```

#### 纯量

纯量是最基本的，不可再分的值，类似于java中的基本数据类型. 包括：

```yaml
boolean: 
    - TRUE  #true,True都可以
    - FALSE  #false，False都可以
float:
    - 3.14
    - 6.8523015e+5  #可以使用科学计数法
int:
    - 123
    - 0b1010_0111_0100_1010_1110    #二进制表示
null:
    nodeName: 'node'
    parent: ~  #使用~表示null
string:
    - 哈哈
    - 'Hello world'  #可以使用双引号或者单引号包裹特殊字符
    - newline
      newline2    #字符串可以拆成多行，每一行会被转化成一个空格
date:
    - 2018-02-17    #日期必须使用ISO 8601格式，即yyyy-MM-dd
datetime: 
    -  2018-02-17T15:02:31+08:00    #时间使用ISO 8601格式，时间和日期之间使用T连接，最后使用+代表时区
```

#### 引用

**&** 用来建立锚点（defaults），**<<** 表示合并到当前数据，***** 用来引用锚点。

```yaml
defaults: &defaults
  adapter:  postgres
  host:     localhost

development:
  database: myapp_development
  <<: *defaults

test:
  database: myapp_test
  <<: *defaults
  
  #等价于
  defaults:
  adapter:  postgres
  host:     localhost

development:
  database: myapp_development
  adapter:  postgres
  host:     localhost

test:
  database: myapp_test
  adapter:  postgres
  host:     localhost
```

