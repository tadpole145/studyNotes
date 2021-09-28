---
title: Nginx详解
date: 2021-03-25 18:11:20
author: 小蝌蚪
img: 
coverImg: 
top: false
cover: false
toc: true
mathjax: false
summary: 介绍Nginx详细使用
tags: [代理工具]
categories: [工具]
comments: false
---



## Nginx

### 基本概念

#### 什么是Nginx

Nginx是一个高性能的Http和反向代理服务器(也支持正向代理), 优点是占有内存小,并发能力强. Nginx专为性能优化而开发，性能是其最重要的考量.Nginx支持热部署,启动容易.

#### 反向代理

正向代理: 局域网中的客户端要访问Internet,需要通过代理服务器来访问,这种代理服务就称为正向代理.

反向代理: 客户端并不需要知道真正的服务器地址,将请求发送给反向代理服务器,然后由反向代理服务器转发到真正的服务器.

差别: 正向代理的是客户端,服务器不做任何配置,反向代理的是服务器,客户端并不感知.

#### 负载均衡

随着业务增长与数据的增加,服务器从单一变为多个,将原先请求集中到单个服务器上的情况改为将请求分发到多个服务器上,将负载分发到不同的服务器，也就是我们所说的负载均衡

#### 动静分离

为加快网站解析速度,将静/动态页面分由不同服务器来解析,降低单个服务器的压力,加快解析速度.

#### 高可用

![img](https://gitee.com/tadpole145/images/raw/main/20210611094502.png)

### Nginx的安装

[官网地址](http://nginx.org/), 支持linux, windows系统

#### Nginx相关依赖

- gcc
- pcre
- openssl
- zlib

#### 安装方法以及常用命令

1. 命令行安装(可以一次安装多个)

```java
//安装 nginx 需要先将官网下载的源码进行编译，编译依赖 gcc 环境
$ yum install gcc-c++
// PCRE(Perl Compatible Regular Expressions) 是一个Perl库，包括 perl 兼容的正则表达式库。nginx 的 http 模块使用 pcre 来解析正则表达式，所以需要在 linux 上安装 pcre 库，pcre-devel 是使用 pcre 开发的一个二次开发库。nginx也需要此库。
$ yum install -y pcre pcre-devel
//zlib 库提供了很多种压缩和解压缩的方式， nginx 使用 zlib 对 http 包的内容进行 gzip
$ yum install -y zlib zlib-devel
//OpenSSL 是一个强大的安全套接字层密码库，囊括主要的密码算法、常用的密钥和证书封装管理功能及 SSL 协议，并提供丰富的应用程序供测试或其它目的使用。nginx 不仅支持 http 协议，还支持 https（即在ssl协议上传输http）
$ yum install -y openssl openssl-devel

```

2. 下载压缩包,手动安装

   - 下载对应的压缩包
     - 官网下载对应格式的安装包
     - **使用`wget`命令下载（推荐）**。确保系统已经安装了wget，如果没有安装，执行 yum install wget 安装。
   - 解压缩安装包

   ```cmd
   $ tar -zxvf nginx-1.19.0.tar.gz
   ```

   -  进入目录,进行配置

   ```cmd
   $ cd nginx-1.19.0
   $ ./configure  //默认配置
   
   ```

   - 安装

   ```cmd
   $ make && make install
   ```

### Nginx常用命令

使用命令之前,必须进入Nginx的目录才行

```java
//切换目录
cd /usr/local/nginx/sbin
//查看版本号
 $ ./nginx -v
//查看安装路径
$ whereis nginx
//启动
./nginx 
//关闭
$ ./nginx -s stop
//重新加载
./nginx -s reload
 //查看开放的端口号
firewall-cmd --list-all
    
```

### 修改Nginx的配置文件

配置文件路径: /usr/local/nginx/conf/nginx.conf

#### 配置文件的组成

##### 1. 全局块

从配置文件开始到event块之间的内容,主要设置一些影响Nginx的服务器整体运行的配置指令,包括配置运行Nginx服务器的用户(组),允许生成worker process数(越大,支持并发处理越多),进程PID存放路径,日志存放路径,类型, 以及配置文件的引入等.

##### 2.enents块

events块涉及的指令主要影响Nginx服务器与用户的网络连接,灵活配置

##### 3. http块

配置最频繁的块.代理,缓存,日志定义等绝大多数功能和第三方模块的配置在这儿.

###### http全局块

指令包括文件引入, MIME-TYPE定义,日志自定义,连接超时自定义,单链请求数上限等.

###### server块

和虚拟主机有密切关系,每个http块可以包括多个server块,每个server可以当做一个虚拟主机,每个server块可以分为全局server块+多个location块.

- 全局server块: 本虚拟机的监听配置和本虚拟机的名称或IP配置;
- location块, 作用是基于接收到的请求字符串,对虚拟主机之外的名称进行匹配,对特定请求进行处理.地址定向,数据缓存,和应答控制功能,第三方模块配置都在这.

###### location说明

语法如下:

```java
location [ = | ~ | ~* | ^~] uri {

}
//1、=: 用于不含正则表达式的uri前，要求请求字符串与uri严格匹配，如果匹配成功，就停止继续向下搜索并立即处理该请求
//2、~: 用于表示uri包含正则表达式，并且区分大小写
//3、~*: 用于表示uri包含正则表达式，并且不区分大小写
//4、^~: 用于不含正则表达式的uri前，要求Nginx服务器找到标识uri和请求字	符串匹配度最高的location后，立即使用此location处理请求，而不再使用location	块中的正则uri和请求字符串做匹配
//注意: 如果uri包含正则表达式，则必须要有~或者~*标识。
```



### Nginx的配置实例

#### 反向代理实例

##### 实现效果

使用nginx反向代理，根据访问的路径跳转到不同端口的服务中。例如, 访问www.123.com, nginx暴露的端口是127.0.0.1:1000, 服务器tomcat地址是127.0.0.1:8081, 输入域名能打开tomcat主页

##### 准备工作

- 准备tomcat服务器
- 在本机host里面设置域名与Nginx端口的映射

- 开放对外访问的端口

默认linux防火墙屏蔽了对外访问的端口,如果需要开放,则需要手动打开

```java
	firewall-cmd --add-port=8080/tcp --permanent
    //重启防火墙
    firewall-cmd-reload
   //查看开放的端口号
	firewall-cmd --list-all
```

##### 设置Nginx配置文件

进入到Nginx的配置文件(/usr/local/nginx/conf/nginx.conf)中,修改参数

![image-20210610155016946](https://i.loli.net/2021/06/10/Aj8zqTgr5iNPth1.png)

#### 反向代理实例2

##### 实现效果

监听同一个端口,根据不同的关键词转发到不同的服务器

##### 配置清单

![image-20210610162713693](https://i.loli.net/2021/06/10/PxRlBh7qDIkvjwF.png)

#### 负载均衡实例

##### 实现效果

一个机器配置有多套相同服务的tomcat, 访问指定端口的时候, 平均分发请求到不同的tomcat中

##### 配置清单

![img](https://gitee.com/tadpole145/images/raw/main/20210610172950.png)

##### 负载均衡分配策略

1. 轮询(默认), 每个请求按时间顺序逐一分配到不 同的后端服务器，如果后端服务器down掉，能自动剔除

2. weigth; 权重默认为1,权重越高被分配的客户端越多。指定轮询几率，weight和访问比率成正比，用于后端服务器性能不均的情况。
3. **ip_hash**; 每个请求按访问ip的hash结果分配, 这样每个访客固定访问一个后端服务器,可以解诀session的问题。
4. fair (第三方); 按后端服务器的响应时间来分配请求，响应时间短的优先分配

```java
upstream server_pool 
	server 192.168.5.21:80;
	server 192.168.5.22:80;
	fair;
//ip_hash;
//weigth;
}
//weight
upstream server_pool 
    weigth;
	server 192.168.5.21:80 weight=1;
	server 192.168.5.22:80 weight=2;
//ip_hash;
 
}
```

#### 动静分离实例

![img](https://gitee.com/tadpole145/images/raw/main/20210610175500.png)

##### 配置挂单

![image-20210610181706153](https://gitee.com/tadpole145/images/raw/main/20210610181706.png)

#### 高可用配置实例

##### 准备条件

(1) 需要两台nginx服务器。
		(2) 需要keepalived, 检测Nginx是否还在运行的脚本
		(3) 需要虚拟ip,提供对外访问的虚拟地址.

##### 安装keepalived

- 命令行安装: $ yum install keepalived

- 安装包手动安装

默认安装路径: /etc/keepalived,  

$ rpm -q -a keepalived    #查看是否已经安装上

安装之后，在etc里面生成目录keepalived, 有配置文件keepalived.conf

##### 配置步骤

1. 修改keepalived的配置文件keepalived.conf

```js
global_defs {
	notification_email {
	  acassen@firewall.loc
	  failover@firewall.loc
	  sysadmin@firewall.loc
	}
	notification_email_from Alexandre.Cassen@firewall.loc
	smtp_ server 192.168.17.129
	smtp_connect_timeout 30
	router_id LVS_DEVEL	# LVS_DEVEL,主机名,这字段在/etc/hosts文件中看；通过它访问到主机
}

vrrp_script chk_http_ port {
	script "/usr/local/src/nginx_check.sh"
	interval 2   # (检测脚本执行的间隔)2s
	weight 2  #权重，如果这个脚本检测为真，服务器权重+2
}

vrrp_instance VI_1 {
	state BACKUP   # 备份服务器上将MASTER 改为BACKUP
	interface ens33 //网卡名称, 通过ifconfig查看当前服务器的网卡
	virtual_router_id 51 # 主、备机的virtual_router_id必须相同
	priority 100   #主、备机取不同的优先级，主机值较大，备份机值较小
	advert_int 1	#每隔1s发送一次心跳
	authentication {	# 校验方式， 类型是密码，密码1111
        auth type PASS
        auth pass 1111
    }
	virtual_ipaddress { # 虛拟ip
		192.168.17.50 // 虛拟ip地址
	}
}
```

2. 在路径/usr/local/src/ 下新建检测脚本 nginx_check.sh

```js
#! /bin/bash
A=`ps -C nginx -no-header | wc - 1`
if [ $A -eq 0];then
	/usr/local/nginx/sbin/nginx
	sleep 2
	if [`ps -C nginx --no-header| wc -1` -eq 0 ];then
		killall keepalived
	fi
fi
```

3. 把两台服务器上nginx和keepalived启动

```yaml
$ systemctl start keepalived.service		#keepalived启动
$ ps -ef I grep keepalived		#查看keepalived是否启动

```

### Nginx的工作原理

一个Nginx client,里面包含有一个master进程和1个或者多个worker进程,worker进程数量最好和cpu内核数量保持一致,才能最大发挥cpu性能.当有请求过来时,master进程会通知worker进程去争抢该任务

###### **master进程职责**

- 读取并验证配置信息。
- 创建，绑定，关闭套接字。
- 启动，终止，维护worker进程的个数。

###### worker进程职责

- 接受，传入并处理来自客户端的连接；
- 提供反向代理等功能；

###### Nginx支持热部署

修改配置文件后,如果配置文件有误,reload后，master会提示配置错误，并不会影响请求的处理。如果主配置文件发生改变，那么并不会立刻影响到WORKER进程，而是MASTER等到WORKER进程的连接请求处理完毕后KILL掉这个WORKER进程，然后重新生成一个WORKER进程，这样这个WORKER进程就将以新的配置启动了。也就是说，老的连接用老的配置，新的连接用新的配置。重新加载配置文件不会中断正在处理的请求。

###### worker进程如何处理请求

一个请求可以由一个worker进程处理并只能由这个worker进程完全处理。Nginx在内部其实是维护了一个accept_mutex，其实就是一个锁，确保在某一时刻，一个请求只能被一个worker进程捕获。当一个worker进程在accept这个连接之后，就开始读取请求，解析请求，处理请求，产生数据后，再返回给客户端，最后才断开连接，这样一个完整的请求就是这样的了。Nginx采用了异步非阻塞事件驱动的方式来处理请求的

###### **异步/非阻塞/事件驱动**

如果发生IO中断，那么你去做你的事情，但是过一段时间来看看IO调用是否结束，这就是非阻塞.为了更高效，Nginx利用了LINUX的EPOLL模型,它提供一种事件驱动机制，它可以监控多个事件是否准备好了，如果准备好了，那么就放入EPOLL队列中。这种机制是异步的。通过这样，WORKER进程只需要循环处理EPOLL队列中的请求，我们只需要在请求间不断切换

```js
# 设置worker数量
worker.processes 4 

# work绑定cpu(4work绑定4cpu)
worker_cpu_affinity 0001 0010 0100 1000

# work绑定cpu (4work绑定8cpu中的4个)
worker_cpu_affinity 0000001 00000010 00000100 00001000

```



![img](https://gitee.com/tadpole145/images/raw/main/20210611170714.png)

![img](https://gitee.com/tadpole145/images/raw/main/20210611170922.png)

##### **连接数worker_ connection**

1. 发送请求，占用了woker的几个连接数? 

访问静态资源占用2个(请求与返回), 动态资源占用4个,因为worker还要与tomcat服务器进行连接.

2. nginx有一个master,有四个woker,每个woker支持最大的连接数1024,支持的最大并发数是多少?

普通的静态访问最大并发数是: worker connections * worker processes /2，
而如果是HTTP作为反向代理来说，最大并发数量应该是worker connections * worker processes/4

