---
title: Linux系统学习
date: 2021-04-08 10:59:23
author: 小蝌蚪
img: 
coverImg: 
top: false
cover: false
toc: true
mathjax: false
summary: Linux从0到入门
tags: [系统]
categories: [开发教程]
comments: false
---





## Linux系统学习

[官网地址](https://www.linux.org/), 本笔记是根据B占韩顺平老师的[一周学会Linux](https://www.bilibili.com/video/BV1Sv411r7vd)而总结的.

#### linux介绍

是一种免费使用和自由传播的类UNIX操作系统.目前我们常见到的是基于linux内核的各种发行版本.

![image-20210628101916046](https://gitee.com/tadpole145/images/raw/main/20210628101916.png)

本次视频学习内容

![image-20210628102146861](https://gitee.com/tadpole145/images/raw/main/20210628102147.png)

#### linux基础

##### Linux系统安装

- 找一台真正安装了linux系统的机器, 比如阿里云,腾讯云等.一般新人会有优惠.
- 安装虚拟机VM(virtual machine), 然后在VM上安装linux发行版Centos.

###### vm虚拟机安装

[vmware官网](https://www.vmware.com/cn.html)--->产品下载---->workstation Pro

![image-20210628112313505](https://gitee.com/tadpole145/images/raw/main/20210628112313.png)

点进去后,可以根据名字寻找,下拉到底部,也可以在搜索框输入“workstation Pro”

![image-20210628112637862](https://gitee.com/tadpole145/images/raw/main/20210628112637.png)

###### vm安装注意事项(以windows为例)

- 开机在BIOS系统中开启虚拟化设备支持设置, [教程](https://jingyan.baidu.com/article/ab0b56305f2882c15afa7dda.html)

本人联想台式机,与教程并不一样,记录下流程:  开机--->不停按F1--->进入BIOS--->高级菜单--->CPU菜单--->intel virtualization技术.

- 以管理员身份运行软件
- 输入软件注册码

###### Centos下载与安装

去镜像网站下载,此处列举2个镜像

[阿里云开源镜像](https://mirrors.aliyun.com/)

[华为云开源镜像](https://mirrors.huaweicloud.com/home)

安装, 参考老师的教程.

##### 虚拟机的克隆

- 方式一, 直接copy一份已存在的linux系统安装文件夹,然后使用vm虚拟机打开文件,选择后缀为wmx的文件.
- 使用wm虚拟机,选择指定系统,右键选择clone, 建议完整克隆.

##### VMTools安装

wmtools是一个win/linux系统共享文件工具,安装参照老师教程

注意点:  若提示普通用户无权限,可切换到root用户

使用:  选择centos--->右键--->设置-->选项--->共享文件夹--->启用,添加文件目录.

##### Linux目录

在linux系统中,一切皆文件.  顶层目录结构为“/”, 其余目录皆在它的子目录.

###### 应用目录

- /bin (/usr/bin, /usr/local/bin), binary的缩写,存放最经常使用的命令.
- /sbin(/usr/bin, /usr/local/bin) s是super的意思,存放系统管理员使用的系统程序
- /home, 存放普通用户的主目录,每个用户都会在此目录下创建一个以自己账户名为名字的文件夹.
- /root, 系统管理员的用户主目录.
- /etc, 系统管理所需要的配置文件和子目录.
- /usr, 存放用户的应用程序和文件
- /tmp, 存放临时文件
- /mnt, 挂载临时的外部文件系统,比如共享文件夹.
- /opt 用户下载的安装软件存放目录,比如xx压缩包
- /usr/local 软件安装后所在的安装目录.一般是通过编译源码的方式安装.

###### 系统目录

- /lib, 系统开机需要的最基本的动态连接共享库
- /lost+found, 一般为空,系统非法关机才会存储一些文件.
- /boot, 存放启动linux的一些核心文件.
- /proc, 系统内存的映射,访问该目录获取系统信息.
- /srv, service的缩写,存放一些服务启动后所需要提取的数据
- /sys, 2.6系统中的一个文件夹
- /dev,类似于win中的设备管理器,把所有硬件使用文件存储
- /media 将外部设备挂载到该目录
- /var, 经常存放一些log类的文件
- /selinux, 安装子系统,控制程序只能访问特定文件.

#### linux实战

##### linux远程登录

###### 连接工具

- 命令行工具Xshell6
- 文件传输工具Xftp6

其余同类软件可备选

###### 连接步骤

1. 获取linux系统的公网IP.   终端命令行窗口输入命令: ifconfig
2. xshell6新建会话,设置主机地址.

![image-20210629104532643](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20210629104532643.png)

3. 连接成功后,测试是否可以输入命令

##### vi/vim介绍

linux系统内置vi文本编辑器.vim可以看做是vi的增强版.它具有类似于ide的基本功能,比如显示行号,自动代码补充,编译等功能.它具有三种工作模式

##### 三种模式

- 一般模式(默认)

- 插入模式,  按i,I, O ,o,A,r,R 任一字母进入该模式.. 建议i, 代表insert
- 命令行模式, 一般模式下按:或者/进入

模式切换: ESC, 退出当前模式

![image-20210629114357558](https://gitee.com/tadpole145/images/raw/main/20210629114357.png)

##### 常用快捷键

1. 拷贝当前行: yy,拷贝多行,比如从当前开始下面5行,  5yy,粘贴:  p.  此操作需要在一般模式下.
2. 删除当前行 dd, 删除多行, 5dd
3. 文件中查找某个单词.  切换到命令行, 输入/关键字, 回车, 输入n,查找下一个.
4. 设置行号, 命令行下输入   : set nu   , 取消行号, 命令行下输入 :set nonu
5. 快速定位到首行/尾行, 一般模式下输入gg/G
6. 撤销上一次输入, 一般模式下输入u
7. 更多命令,参考教程.

![image-20210629134932842](https://gitee.com/tadpole145/images/raw/main/20210629134933.png)

##### 开关机/注销命令

- shutdown -h now   立刻关机, h=halt
- shutdown -h  1    1分钟后关机等同于shutdown
- shutdown -r now  立刻重启,  r=reboot
- halt   关机
- reboot  重启
- sync 同步内存数据到磁盘... 建议注销/关机前同步
- su - 管理员用户名    切换管理员用户名,当然也可以切换普通用户
- logout  注销用户, 仅在运行级别有效,图形界面无效.

虽然现在shutdown/reboot/halt执行前均sync了,但是还是建议手动执行一次sync

##### 用户管理

```java
//管理员登录,才有权限对用户进行增删操作
useradd 用户名    //新增用户,默认目录在/home/xxx
useradd -d 指定目录 用户名  //新增用户并指定目录名.
passwd 用户名     //给xxx用户设置密码
userdel xxx    //删除xxx, 不删除工作目录
userdel -r xxx   //删除xxx同时删除他的工作目录
id xxx   //查询xxx的信息
su - xxx  //切换用户,高权限-->低权限,无需密码,反之是需要的.
exit/logout  //切回到原来用户
    
//用户分组
groupadd 组名    //新增一个组
groupdel 组名    //删除一个组
useradd -g 用户组 用户名   //新增一个用户,并指定到xx组, 如果不指定,默认是放在以自己名字为单独的用户组
usermod -g 用户组 用户名    //将xxx移到指定的用户组

    
//用户和组相关的文件
/etc/passwd   用户user的配置文件,记录用户的各种信息
 登录名:密码:用户标识号:组标识号:注释性描述:主目录:登录shell
 root:x:0:0:root:/root:/bin/bash  
     
 /etc/shadow   口令的配置文件
 每行的含义：登录名:加密口令:最后一次修改时间:最小时间间隔:最大时间间隔:警告时间:不活动时间:失效时间:标志
     
/etc/group  组的配置文件,记录linux包含的组信息
每行含义：组名:口令:组标识号:组内用户列表
   
```



##### 运行级别

linux系统存在7个运行级别,常用的运行级别是3,5.我们可以手动的指定默认运行级别.

0. 关机
1. 单用户(找回丢失密码)
2. 多用户状态无网络服务
3. 多用户状态有网络服务
4. 系统未使用保留给用户
5. 图形界面
6. 重启系统

###### 切换运行级别

终端输入:  init x,  x代表0-6这7个运行级别.

设置默认的运行级别:   systemctl set-default xx.target        multi=user.target 代表级别3,     graphical.target 代表运行级别5.

查看当前运行级别:   systemctl get-default



##### 找回root密码(CentOS7教程)

1, 开机界面,出现CentOS Linux时候,按“e”, 进入编辑页面.

==注意: 进入编辑界面后,无法切换到主机win系统,无法使用截图或者其余按键调用别的软件,请事先准备好要输入的命令.第2步完成后,后面可恢复正常==

2, 进入编辑界面后,使用方向键移动光标,找到以“linux16”为开头的所在行数,在行的最后面输入 init=/bin/sh, 然后按ctrl+x进入单用户模式.

3, 在界面闪烁处输入: mount -o remount,rw /      ,完成后按enter

![image-20210630102548673](https://gitee.com/tadpole145/images/raw/main/20210630102548.png)

4, 接着输入passwd,按Enterh后输入密码,然后再次输入确认密码.

![image-20210630102643568](https://gitee.com/tadpole145/images/raw/main/20210630102643.png)

5, 输入: touch /.autorelabel  ; 完成后按enter

![image-20210630102729209](https://gitee.com/tadpole145/images/raw/main/20210630102729.png)

6, 输入: exec /sbin/init  ; 完成后按enter,等待系统自动修改密码(需等待较长时间,大概需要2分钟).然后系统会自动重启.

![image-20210630102841919](https://gitee.com/tadpole145/images/raw/main/20210630102841.png)

##### 常用指令

###### 帮助类指令

man xx      获得xx的帮助信息

```java
//示例
man ls    //查看ls命令的详细信息
```

help        获得shell内置命令的帮助信息

###### 目录指令

pwd   显示当前工作目录的绝对路径

ls  [选项] [目录或者文件]   查看指定目录的详细信息

- -a : 显示所有目录,包含隐藏文件
- -l  : 以列表方式显示
- 其余的参照选项命令参照指示,  可以组合使用,比如-al(la, 不区分先后)

cd [参数]      //切换到指定目录

cd ~     //返回自己的家目录

mkdir [选项] 目录名    //用于创建目录

常用选项: -p, 创建多级目录

注意事项:  创建一级子目录,可以直接使用,创建多级子目录,需要用到绝对路径.

```java
//当前用户目录为/home/user1
mkdir dog   //当前目录下直接创建dog文件夹
mkdir -p /home/user1/animal/cat   //创建多级目录
```

rmdir [选项] 要删除的目录

rm -rf xx    //递归删除xx及其子目录

```java
//当前用户目录为/home/user1
rmdir dog   //删除当前目录下dog文件夹
rm -rf /home/user1/animal/cat   //递归删除孙子级别目录cat及其子目录.如果是直接子目录可以相对路径,如果不是,则必须使用绝对路径.
```

touch xx    //创建xx空文件

```java
touch hello.java     //创建hello的空文件
```

cp [选项] source dest   //从源文件(夹)复制到目标文件(夹)

-r  //递归复制整个文件夹

\cp : 强制覆盖不提示, 示例: \cp -r /home/bbb  /opt

注意事项:  当前目录下可以直接文件(夹)名, 其余目录是绝对路径.

mv指令:   

- 当前目录下使用, 重命名  mv oldName  newName
- 不同目录下,移动功能  mv  /home/bb  /opt    //将home目录下的bb文件夹整体移动到opt目录下

cat : 查看文件内容, 只能浏览而不能修改. 为了浏览方便,一般会带上管道命令 | more

cat [选项] 文件名 

-n  : 显示行号

```java
//示例
cat -n /etc/profile | more
```

more指令是基于VI编辑器的文本过滤器,它以全屏幕的方式按页显示文件内容, more指令内置了若干快捷键.详细操作如下: 

![image-20210630161534097](https://gitee.com/tadpole145/images/raw/main/20210630161534.png)

less:  分屏查看文件内容,功能与more指令类似.但是更强大,支持显示各种中断,加载文件是按需加载,而不是一次性加载,节省内存.

基本语法:   less  文件完整路径

![image-20210630162912653](https://gitee.com/tadpole145/images/raw/main/20210630162912.png)

echo: 输出内容到控制台

语法:   echo [选项] [输出内容]

```java
//示例
echo $HOSTNAME   //输出当前主机名
echo "hello"    //输出hello
```

head:  用于显示文件的开头部分内容. 默认显示前面10行.

基本语法:  head 文件,  head -n 5 文件路径

```java
head /etc/profile		//查看前10行
head -n /etc/profile   //查看前5行
```

tail 输出文件末尾内容,默认10行

基本语法: tail 文件路径

```java
tail /etc/profile   //查看文件末尾10行
tail -n 5 /etc/profile   //查看文件末尾5行
tail -f /etc/profile   //实时追踪该文档的所有更新
```

“>” 输出重定向, >>追加指令

```java
ls -l > a.txt   //将当前目录下的子文件信息列出并写入a.txt文件中
ls -al >> a.txt   //将当前目录下的文件信息追加到a.txt中
cat 文件1 >文件2    //将1中的内容完整覆盖到2中,类似于复制文件并重命名
 echo "hello" >> a.txt   //将hello追加到a.txt中.
```

ln 连接符,类似于win里面的快捷方式.

基本语法:  ln -s [原路径] [软连接名]   //给源文件创建一个软连接

```java
ln -s /root /home/myroot   //将myroot连接到/root目录.
 rm /home/myroot 删除软连接.
```

history:  查看已经执行过的历史命令.

```java
history   //显示所有的历史命令
history 5   //显示前5个
!5    //执行编号为5的历史指令
```

###### 时间日期类

date 显示当前日期

```java
date   //显示当前详细的时间信息
date +%Y / "+%Y" 显示当前年份
date +%m    //显示当前月份
date +%d    //显示当前天
date "+%Y-+%m-+%d %H:%M:%S"   //显示当前年月日时分秒
date -s "2020-11-03 20:02:10" //将时间设置为想设置的时间
 
 //cal指令
  cal  //显示当前日历
  cal 2021 //显示2021年历
```

###### 搜索查找类

find 从指定目录下遍历其各个子目录, 将满足条件的显示在终端.

基本语法:   find [搜索范围] [选项]

![image-20210630180643326](https://gitee.com/tadpole145/images/raw/main/20210630180643.png)

```java
find /home -name hello.java   //在home目录下查找hello.java文件
find /home -name *.java   //在home目录下查找.java后缀的文件    
find /opt -user tom   //在opt目录下查找tom创建的文件
    
// +大于, -小于, 不写就是等于,  k=kb, M=Mb, G=Gb    
find /opt -size +200M  //在opt目录下查找大于200M的文件.
```

locate可以快速定位文件路径.它是利用实现事先建立的系统中所有文件名称以及路径的locate数据库实现快速定位文件. 所以使用之前需要updatedb.类似于win系统中的everything搜索工具.

```java
locate hello.txt   //查找hello.txt文件位置
    
```

grep, 过滤查找,一般和管道符“|”合用.表示将前一个命令处理的结果输出传递给后面的命令来处理.

基本语法:  grep [选项] 查找内容 源文件

![image-20210701091938336](https://gitee.com/tadpole145/images/raw/main/20210701091938.png)

```java
//示例
grep -n "yes" /home/hello.txt 		//在hello.txt中查找yes并显示行号
```

###### 压缩/解压缩类

 gzip 压缩文件, gunzip解压缩文件

zip [选项] xxx.zip  sourceFile 	

unzip [选项] xxx.zip 

-r 递归压缩;   -d 目录 :指定解压后文件的存放目录

tar 打包指令,格式为.tar.gz

基本语法:   tar [选项]  xxx.tar.gz  sourceFile

![image-20210701102034843](https://gitee.com/tadpole145/images/raw/main/20210701102034.png)

```java
gzip  文件       	//将文件压缩为*.gz文件
gunzip 文件.gz   //解压缩文件
    
    //-----zip指令
zip -r myhome.zip /home/ 	//将home文件夹压缩为myhome.zip
unzip -d /opt/tmp /home/myhome.zip  	//将myhome.zip解压到tmp目录下.
    
//tar指令
 tar -zcvf pc.tar.gz /home/file1.txt /home/file2.txt	//将file1.file2两个文件打包到pc.tar.gz文件中
tar -zxvf pc.tar.gz  	//将pc.tar.gz解压缩.
tar -zxvf pc.tar.gz -C /opt  	//将pc.tar.gz解压缩到指定目录.
```



基本语法: 



