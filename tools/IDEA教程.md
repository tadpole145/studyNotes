---
title: IDEA介绍与使用
date: 2020-12-23 20:30:12
author: 小蝌蚪
img: 
coverImg: 
top: false
cover: false
toc: true
mathjax: false
summary: 介绍IDEA的常用操作命令与使用
tags: [软件]
categories: [工具]
comments: false
---



### IDEA介绍与使用

==强烈建议看如下列举的参考文章,我记录的只是我遇到的或者我觉得有用的, 我使用的IDEA版本为 2020.1版本.==

[IDEA配置小结](https://jadyer.cn/2016/04/20/idea-config/)

[github教程1](https://hub.fastgit.org/judasn/IntelliJ-IDEA-Tutorial)

[github教程2](https://hub.fastgit.org/guobinhit/intellij-idea-tutorial)

[idea综合教程](https://github.com/xiaoxiunique/awesome-IntelliJ-IDEA)



#### 设置入口

之前使用IDEA时,经常发现,打开项目后,在设置Settings里面配置好了某些配置,比如File Encoding,  Maven配置后,后面再次新建项目时候,这些设置又都变成了默认设置,经过查阅才发现,IDEA打开项目后,更改的是本项目设置,如果要更改默认设置,有2个入口,如下所示:

==所以, 第一次配置的时候,千万别在File-->settings(ctrl+Alt+s)里面配置,那个是针对当前项目的配置.==

1. IDEA启动界面

![idea配置入口](https://gitee.com/tadpole145/images/raw/main/20210810145835.png)

2. 打开项目后,File--> New Project Settings

![image-20210810144915178](https://gitee.com/tadpole145/images/raw/main/20210810144915.png)

#### 配置文件目录

```java
//与以前不一样了,新安装的软件都在appData目录下,也可以手动修改
C:\Users\admin\AppData\Roaming\JetBrains\IntelliJIdea2020.1
```

#### 打包java项目

File-->Project Structure-->artifacts-->add-->选择自己想打的包,然后build-->build artifacts, 在项目目录的output里面能找到该jar包.

#### 常用快捷键与代码模板

```
方法模板
psvm :  main
sout: 打印日志
```

#### 设置参数修改

```java
//设置搜索关键字,由于idea版本不一致,导致设置的地方可能有更改,直接提供关键之搜索更有效

字体设置: font
鼠标调字体大小:  mouse control
鼠标悬浮获取类/方法提示: quick documentation
自动导包: auto import
行号: show line numbers
方法间隔符: show method separators
忽略大小写: match case
tabs多行显示: show tabs in one row
编码设置:  file encodings, 全部设置为utf-8,并勾选native-to-ASCII
自动编译: compiler
快捷键: keymap
打开idea手动选择项目: reopen last project on star
显示内存使用: 双击shift-->show memory或者setting:show memory
空闲时候自动保存: save file
自动更新: auto update
设定折叠或展开的代码类型: Code Folding
星号标识编辑过的文件: Mark modified
快捷键自动生成变量使用自定义前后缀: Code Generation
日志多行打印: use soft wraps in console
```

#### 快捷键

```
//此处只列举我觉得有用的

查看自定义代码模板: ctrl+j
格式化: ctrl+alt+L
查看最近修改的代码: ctrl+E
方法参数提示: ctrl+P
包裹选中代码: ctrl+alt+T
复制一行: ctrl+D
删除行: ctrl+Y
自动选中: ctrl+W
反选中: ctrl+shift+W
大小写: ctrl+shitf+U
查找类文件: 双击shift
查找: ctrl+F
替换: ctrl+R
全局修改: shift+F6
撤回: ctrl+Z
反撤回: ctrl+shift+z
单行注释: ctrl+/
多行注释: ctrl+shift+/
修改文件模板: file and code Templates
.后缀自动补全代码: postfix completion
显示文档: ctrl+Q
当前方法的父类的方法 / 接口定义: ctrl+U
光标所在的方法/变量的接口或是定义处: ctrl+B/ctrl+鼠标左键
版本控制提交项目: ctrl+K
版本控制更新项目: ctrl+T
当前类的层次结构: ctrl+h
当前类可重写的方法: ctrl+O
可继承的方法: ctrl+I
格式化代码: ctrl+shift+L
快速提取常量: ctrl+shift+C
快速提取方法: ctrl+shift+f
大小写切换: ctrl+shift+u
对当前类生成单元测试类: ctrl+shift+T
弹出缓存的最近拷贝的内容管理器弹出层; ctrl+shift+v
自动结束代码，行末自动添加分号: ctrl+shift+enter

```



#### 自定义代码模板

1. 设置(ctrl+Alt+S)--> Editor-->Live Templates;
2. 选择要添加的项目
3. 右上角添加templates
4. 输入自己的示例,并选择生效的语言,如果有默认值可以设置默认值,再保存

![image-20210203161416270](https://raw.githubusercontent.com/tadpole145/images/main/image-20210203161416270.png)

#### 使用界面介绍

```
显示toolBar: view-->appearance-->toolbar

```



#### 常用插件推荐

#####  easy_javadoc

###### 功能介绍

一个可以快速为`Java`的类、方法、属性加注释的插件，还支持自定义注释样式.

在为类、方法、属性加注释时，不仅会生成注释，还是会将对应变量、类、方法翻译成中文名，不过翻译的怎么样还要取决于你的命名水平。

###### 使用方法

单个注释: 选中需要注释的对象(类,属性,方法), 快捷键 ctrl+\

全部注释:  选中类, 快捷键 ctrl+shift+ \

**tips**:  该快捷键为**win**系统使用, 如果是mac系统,则参考[easy_javadoc插件github官方说明文档](https://github.com/starcwang/easy_javadoc)

![v2-466acecd4b2b12e43482c9722c603cc8_720w](https://gitee.com/tadpole145/images/raw/main/20210907161806.webp)



##### [Easy Code](https://plugins.jetbrains.com/plugin/10954-easy-code)

###### 功能介绍

- 基于IntelliJ IDEA开发的代码生成插件，支持自定义任意模板（Java，html，js，xml）。
- 只要是与数据库相关的代码都可以通过自定义模板来生成。支持数据库类型与java类型映射关系配置。
- 支持同时生成生成多张表的代码。每张表有独立的配置信息。完全的个性化定义，规则由你设置。

###### 使用方法

由于功能强大, 建议参考[官方文档](https://github.com/makejavas/EasyCode)

MybatisPlus也可以生成模板代码,还可以搭配Lombok设置生成策略, 如果习惯使用Lombok的用户,建议还是使用MybatisPlus推荐的代码生成

###### Tips

- 使用mysql8.0+时,使用database连接mysql,需要添加serverTimezone=Asia/Shanghai参数

- ![image-20210907170658568](https://gitee.com/tadpole145/images/raw/main/20210907170841.png)
- 需要指定连接的数据库名

![image-20210907170821018](https://gitee.com/tadpole145/images/raw/main/20210907170821.png)



##### [RestfulToolkit](https://plugins.jetbrains.com/plugin/10292-restfultoolkit)

1. 根据 URL 直接跳转到对应的方法定义 ( Ctrl \ or **Ctrl Alt N** );
2. 提供了一个 Services tree 的显示窗口;
3. 一个简单的 http 请求工具;
4. 在**请求方法(选中,右键)**上添加了有用功能: 复制生成 URL;,复制方法参数...
5. 其他功能: **java 类(选中,右键)**上添加 Convert to JSON 功能，格式化 json 数据 ( Windows: Ctrl + Enter; Mac: Command + Enter )。

搜索定位controller,复制url, 根据类生成json,这些功能都是很实用的.

##### String Manipulation

###### 功能介绍

一个比较实用的字符串转换工具，比如我们平时的变量命名可以一键转换驼峰等格式，还支持对字符串的各种加、解密（MD5、Base64等）操作

###### 使用方法

- 选中字段名, 右键, 选中加密方式,即可变为加密后的字符串,

- alt+M, 也可以出来功能选项,不过功能不如右键全面

- 转换大小写:  选中, alt+enter

##### [Translation](https://github.com/YiiGuxing/TranslationPlugin)

###### 功能介绍

一个很方便的翻译插件，比如选中代码、控制台的报错信息可直接翻译。

###### 使用方法

翻译指定单词或者句子:

- 选择文本 > 单击鼠标右键 > 翻译
- 选中文本,按快捷键ctrl+shift+Y

显示显示翻译对话框: Ctrl + Shift + O  (win)

翻译文档注释: 光标在文档注释块内时,右键-->翻译文档

更多使用方式,详见[官方文档](https://yiiguxing.gitee.io/translation-plugin/start.html)

##### Vcs Auto Update(Git Auto Pull)

团队多人开发项目时，由于频繁提交代码，等我在`commit`本地代码的时必须先进行`pull`，否则就会代码冲突产生`merge`记录。

`GitAutoPull`插件帮我们在`push`前先进行`pull`，避免了不必要的代码冲突。

##### GenerateAllSetter

实际的开发中，可能会经常为某个对象中多个属性进行 `set` 赋值，尽管可以用`BeanUtil.copyProperties()`方式批量赋值，但这种方式有一些弊端，存在属性值覆盖的问题，所以不少场景还是需要手动 `set`。如果一个对象属性太多 `set` 起来也很痛苦，`GenerateAllSetter`可以一键将对象属性都 `set` 出来。

###### 使用方式

光标放在new的对象这行代码任意位置,   快捷键：Alt+Enter



##### Alibaba Java Coding Guidelines

阿里定义的java编码规范, 当你的代码出现了不符合规范的时候,ide会自动给出提示.

##### GsonFormat\GsonFormatPlus

将`JSON`字符串自动转换成`Java`实体类的工具

快捷键：`Alt+ S`

##### Maven Helper

`Maven Helper` 是解决`Maven`依赖冲突的利器，可以快速查找项目中的依赖冲突。安装后打开`pom`文件，底部有 `Dependency Analyzer`视图。显示红色表示存在依赖冲突，点进去直接在包上右键`Exclude`排除，`pom`文件中会做出相应排除包的操作。

- Conflicts(冲突)
- All Dependencies as List(列表形式查看所有依赖)
- All Dependencies as Tree(树结构查看所有依赖)，并且这个页面还支持搜索。

##### MyBatisX

###### 功能介绍

安装后,mapper中的接口文件和xml左侧会出现小鸟标志,点击可以互相跳转

![Screenshot 1](https://gitee.com/tadpole145/images/raw/main/20210910161525.gif)



##### [MybatisLogFormat](https://plugins.jetbrains.com/plugin/14292-mybatislogformat)

将mybatis的执行语句转换成sql语句





##### ConvertYamlAndProperties

配置文件properties和yaml互相转换, 建议转换之前做好备份哦.

![show.gif](https://gitee.com/tadpole145/images/raw/main/20210910162037.gif)



##### Lombok

通过注解,设置JavaBean的构造方法,set,get方法等.



##### [Grep Console](https://plugins.jetbrains.com/plugin/7125-grep-console)

通过关键词过滤,高亮指定文本等,创建新的控制台窗口, 用于控制台分析日志.











#### 常见问题

##### Plugins 连不上、打不开

一、设置 http proxy—>勾上Auto-detect proxy setting,参照下图，加上地址 http://127.0.0.1:1080 (未测试)或者https://plugins.jetbrains.com/ (已测试成功), 然后重启即可

![img](https://img.jbzj.com/file_images/article/202006/2020062511464535.jpg)



##### plugins下载连接超时

查询ip,修改host. 参考[一分钟解决IDEA下载插件超时问题的解决](https://blog.csdn.net/daiqi5527153/article/details/109347095)



