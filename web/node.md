[官网](https://nodejs.org/zh-cn/),  [中文网](http://nodejs.cn/)

#### 概念(What)

Node.js 是一个能在服务器端运行的js开放源代码运行环境. 它采用Google开发的V8引擎运行js代码,使用事件驱动,非阻塞,和异步I/O模型等技术来提高性能,可优化程序的传输量与规模.

它是用于web服务器开发的,对比使用java开发服务端可方便初学者更好理解.

| 开发语言   | 构建,包管理器 | 运行平台   | 运行环境 |
| ---------- | ------------- | ---------- | -------- |
| java       | maven         | java虚拟机 | JRE      |
| JavaScript | npm           | v8引擎     | node.js  |
|            |               |            |          |

注: *运行时环境* 是一种旨在运行其他软件的软件。作为Java的运行时环境，JRE包含Java类库，Java类加载器和Java虚拟机。

#### 特点

Node.js 应用程序运行于单个进程中，无需为每个请求创建新的线程。 Node.js 在其标准库中提供了一组异步的 I/O 原生功能（用以防止 JavaScript 代码被阻塞）

#### 创立的技术背景(Why)

Node.js之父Ryan Dahl的工作是用C/C++写高性能Web服务,但是用C/C++写就太痛苦了。于是他开始设想用高级语言开发Web服务,虽然很多语言同时提供同步IO和异步IO,但是开发人员一旦使用同步IO,就懒得使用异步IO了,而JavaScript是单线程执行，根本不能进行同步IO操作，所以Ryan Dahl选择了它作为开发语言.而对于运行时引擎, Ryan选择了google刚开源的v8引擎,2009年，Ryan正式推出了基于JavaScript语言和V8引擎的开源Web服务器项目，命名为Node.js.

Node为什么会火起来呢？

- 最大的优势是借助JavaScript天生的事件驱动机制加V8高性能引擎，使编写高性能Web服务轻而易举。在它上面做的每一件事，每一个独立的调用和操作，都是一系列的异步回调.
- js是web开发者熟知的语言,大家不用学习额外的后端语言就可以直接使用js去开发服务端.
- 支持对象数据库,比如MongoDB等.
- 因为js的广泛流行,有强大的开发者基础与IDE工具,此外开发出的web服务可随处托管.

####  模块化

一个js文件就是一个模块, 通过require("模块路径")函数引入外部的模块.返回值即为该模块对象

```js
var md= require('./test.js');
```

模块中的变量和函数在其它模块中是无法访问的,如果需要访问,则要通过==exports== 或者==module.exports==对外暴露, 暴露对象的时候使用==module.exports==

```js
exports.x="我是变量";
exports.fun=function (){
    
};
```

核心模块的引入, 直接require("模块名")

node中有个全局对象global,作用于网页中的window类似,全局变量都会作为global的属性保存,而全局中创建的函数则会作为global的方法保存

```js
a =10;   //没有变量var 声明
console.log(global.a);
//函数

```

证明模块是封装在一个函数里面执行,由node.js引擎调用

```js
//执行
console.log(arguments.callee + "");
//结果
function (exports, require, module, __filename, __dirname) {
console.log(arguments.callee + "");
}

```

####  包

包实际上就是一个压缩文件,主要由以下2部分构成

- 包结构
- 包描述文件

解压后应当包含如下文件

```
-package.json 描述文件  *必须要
-bin 可执行二进制文件
-lib js代码
-doc 文档
-test 单元测试
```

#### NPM (Node Package Manager)

类比于Maven, NPM用于第三方模块的发布,安装,依赖等.借助于NPM,Node与第三方模块之间形成了一个很好的生态系统.

##### 常用命令

```
查看版本 npm -v / npm version
搜索包名  npm search 包名
下载当前项目所依赖的所有包: npm install
安装: npm install/i 包名
初始化,创建包描述文件: npm init
全局安装: npm install 包名 -g
全局并添加依赖: npm install/i 包名 --save
删除包: npm remove/r 包名
更新次版本以及补丁版本: npm update
运行任务: npm run <task-name>
查看全局位置: npm root -g
查看npm安装包列表: npm list
查看npm全局安装包列表: npm list -g
查看指定包的最新版本: npm view 包名 version
查看指定包的以前版本列表: npm view 包名 versions
安装指定包名的指定版本: npm install 包名@版本号   
//示例: npm install cowsay@1.2.0  全局安装: npm install -g webpack@4.16.4
发觉软件包的新版本: npm outdated
将所有软件包更新到新的主版本: npm install -g npm-check-updates   然后运行 ncu -u,再运行npm update
卸载指定包名:  npm uninstall 包名
卸载依赖项: npm uninstall -S <package-name>
卸载开发依赖项: npm uninstall -D <package-name>
卸载全局包: npm uninstall -g <package-name>

```

#### 版本语义控制

所有的版本都有 3 个数字：`x.y.z`。

- 第一个数字是主版本。
- 第二个数字是次版本。
- 第三个数字是补丁版本。

当发布新的版本时，不仅仅是随心所欲地增加数字，还要遵循以下规则：

- 当进行不兼容的 API 更改时，则升级主版本。
- 当以向后兼容的方式添加功能时，则升级次版本。
- 当进行向后兼容的缺陷修复时，则升级补丁版本。

版本前面一般还添加了一些符号,具体语义如下所示: 

- `^`: 只会执行不更改最左边非零数字的更新。 如果写入的是 `^0.13.0`，则当运行 `npm update` 时，可以更新到 `0.13.1`、`0.13.2` 等，但不能更新到 `0.14.0` 或更高版本。 如果写入的是 `^1.13.0`，则当运行 `npm update` 时，可以更新到 `1.13.1`、`1.14.0` 等，但不能更新到 `2.0.0` 或更高版本。
- `~`: 如果写入的是 `〜0.13.0`，则当运行 `npm update` 时，会更新到补丁版本：即 `0.13.1` 可以，但 `0.14.0` 不可以。
- `>`: 接受高于指定版本的任何版本。
- `>=`: 接受等于或高于指定版本的任何版本。
- `<=`: 接受等于或低于指定版本的任何版本。
- `<`: 接受低于指定版本的任何版本。
- `=`: 接受确切的版本。
- `-`: 接受一定范围的版本。例如：`2.1.0 - 2.6.2`。
- `||`: 组合集合。例如 `< 2.1 || > 2.6`。

可以合并其中的一些符号，例如 `1.0.0 || >=1.1.0 <1.2.0`，即使用 1.0.0 或从 1.1.0 开始但低于 1.2.0 的版本。

还有其他的规则：

- 无符号: 仅接受指定的特定版本（例如 `1.2.1`）。
- `latest`: 使用可用的最新版本。



##### 镜像配置 (备用,推荐使用官方)

[淘宝NPM镜像地址](http://npm.taobao.org/)

配置命令:

```
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

使用方式

```
cnpm install express
```

#### Node基础

##### Buffer

> 缓冲区, 结构和数组类似, 由于数组不能存储二进制文件,而图片,视频文件都是以二进制文件保存的,故buffer专门用于存储二进制数据.但是显示的时候会以16进制显示.输出的时候是10进制.无需引入模块,直接使用即可.
>
> 构造函数已废弃,推荐使用Buffer.alloc(int length)函数.buffer大小一旦确定后,就不可更改.

##### fs 文件系统

> 直接引入模块,无需下载. fs是Node来操作系统中的文件的核心模块.

我学习nodejs主要是为了搭建个人博客,并没有想深入学习的念想. 想学的话,语法还是看[官方文档](http://nodejs.cn/learn)吧

