### Zookeeper简介与应用

##### 什么是zookeeper

Zookeeper 是一个开源的分布式的，为分布式框架提供协调服务的 Apache 项目。

Zookeeper 从设计模式角度来理解：是一个基于观察者模式设计的分布式服务管理框架，它负责存储和管理大家都关心的数据，然后接受观察者的注册，一旦这些数据的状态发生变化， Zookeeper就将负责通知已经在 Zookeeper 上注册的那些观察者做出相应的反应。

##### 特点

- Zookeeper：一个领导者（Leader），多个跟随者（Follower）组成的集群。
- 集群中只要有半数以上节点存活，Zookeeper集群就能正常服务。所以Zookeeper适合安装奇数台服务器。
- 全局数据一致：每个Server保存一份相同的数据副本，Client无论连接到哪个Server，数据都是一致的。
- 更新请求顺序执行，来自同一个Client的更新请求按其发送顺序依次执行。
- 数据更新原子性，一次数据更新要么成功，要么失败。
- 实时性，在一定时间范围内，Client能读到最新数据。

##### 应用场景

- 统一命名服务(一个域名映射多个IP)
- 统一配置管理(配置信息写入Znode节点)
- 统一集群管理(客户端监听ZNode获取实时状态变化)
- 服务节点动态上下线(客户端动态获取当前服务器状态)
- 软负载均衡(同nigx功能, 动态分配客户请求到不同ip)

### Zookeeper的下载与安装

##### 下载

[官网地址](https://zookeeper.apache.org/ )

![image-20210708153437377](https://gitee.com/tadpole145/images/raw/main/20210708153438.png)

![image-20210708153513783](https://gitee.com/tadpole145/images/raw/main/20210708153513.png)

###### 注意事项

-bin.tar.gz  是已经编译好的包, 不带bin的需要自己编译. 因此下载时候要选择-bin.tar.gz

![img](https://gitee.com/tadpole145/images/raw/main/20210708153757.png)

##### 安装

- 需要java环境, 事先安装好jdk,并配置好环境变量 	
- 使用xftp6将下载的zookeeper推送到linux系统, 不要直接从win系统拖送过去...拖过去会导致文件不全,安装不成功.==一定要注意不要直接拖文件==
- 解压到指定目录

```java
tar -zxvf apache-zookeeper-3.5.7-bin.tar.gz -C /opt/module/ 
//注意, 安装到指定目录,一定要带上-C, 代表change到新目录.
```

- 修改名称(可选)

```java
 mv  apache-zookeeper-3.5.7  zookeeper-3.5.7
```

- 修改配置

```java
1. 将/opt/module/zookeeper-3.5.7/conf 这个路径下的 zoo_sample.cfg 修改为 zoo.cfg；
2. 打开 zoo.cfg 文件，修改 dataDir 路径 dataDir=/opt/module/zookeeper-3.5.7/zkData 
    //存放数据的文件夹自定义名字,最好放在zookeeper安装目录下
```

- 配置环境变量

```java
1) 打开配置文件, 命令终端输入指令:
    vi /etc/profile
2) 在文件末端输入如下数据,然后保存并退出
export ZOOKEEPER_HOME=/你的安装目录/zookeeper-3.5.7/
export PATH=$ZOOKEEPER_HOME/bin:$PATH
export PATH
3) 刷新环境变量, 终端输入如下指令
 [root@master ~]# source /etc/profile

```

###### 启动/关闭服务,终端输入命令

```java
zkServer.sh start   //启动服务
zkServer.sh status   //查看状态
zkCli.sh     //启动客户端
jps     //查看进程是否启动
zkServer.sh stop  //关闭服务
zkServer.sh restart  //重启服务
quit //退出

```

![image-20210708160337775](https://gitee.com/tadpole145/images/raw/main/20210708160337.png)

##### 配置参数解读

Zookeeper中的配置文件zoo.cfg中参数含义解读如下： 

![image-20210708174503821](https://gitee.com/tadpole145/images/raw/main/20210708174503.png)

- tickTime = 2000：单次通信心跳时间，Zookeeper服务器与客户端心跳时间，单位毫秒 
- initLimit = 10：Leader-Fellower初始通信时限, 最多能容忍的心跳次数. 
- syncLimit = 5：Leader-Fellower同步通信最多能容忍的心跳次数. 如果超过syncLimit * tickTime，Leader认为Follwer死掉，从服务器列表中删除Follwer。
- dataDir：保存Zookeeper中的数据 , 自定义存放位置
- clientPort = 2181：客户端连接端口，通常不做修改。 

### Zookeeper集群

#### 集群安装

1. 在每台服务器上安装好zookeeper, 操作与单台一致.
2. zkData 目录下创建一个 myid 的文件 ,在文件中添加与 server 对应的编号（注意：上下不要有空行，左右不要有空格） 
3. 拷贝步骤2中的配置文件到其余服务器的zkData目录下,修改对应的sid值.
4.  zoo.cfg 配置文件内增加如下配置

```java
#######################cluster########################## 
server.2=hadoop102:2888:3888 
server.3=hadoop103:2888:3888 
server.4=hadoop104:2888:3888 
 /**
 *参数解读  server.A=B:C:D。
 	A: serviceId(sid),与myid中的数值一致
 	B: 服务器主机名
 	C: Follower 与集群中的 Leader 服务器交换信息的端口
 	D: Leader挂掉后,用来执行重新选举时服务器相互通信的端口
 *
 */
```

5. 同步 zoo.cfg 配置文件 

```java
//终端执行命令
xsync zoo.cfg 
```

#### 选举机制

##### 相关概念

###### 选举原则

一个集群里面只有一个Leader,其余全部为Follower,  leader通过选举产生,zookeeper 默认的算法是 FastLeaderElection，采用投票数大于半数则胜出
的逻辑。

###### 选举状态

- LOOKING：寻找leader状态，该状态下，服务器认为当前集群没有leader，会发起leader选举。在选举过程中，所有服务器的状态都是LOOKING。
- FOLLOWING：跟随者状态，该状态下，当前服务器是follower，并且知道leader是谁。此时选举已经结束。
- LEADING：领导者状态，该状态下，当前服务器是leader，会与follower维持心跳检测。此时选举已经结束。
- OBSERVING：观察者状态，该状态下的服务器是observer，不参与选举。目的是为了降低多台服务器进行选举的复杂度,同时又能对外提供更庞大的服务集群.



###### 选票数据结构

每个服务器在进行leader选举时，都会发送以下几个关键属性信息：

- logicalclock：投票轮次，自增的，volatile的，初始值为1，也就是第一轮选举。

- state：当前服务器的状态。

- self_id：当前服务器的myid。

- self_zxid：当前服务器的最新的zxid,也是事务编号,它是一个8字节的整形数字,分为2部分,前32位用来记录epoch,后32位用于计数.
	- epoch, 纪元朝代的意思,每选举一次会增1
	- 计数器,记做DataId吧, 数据每写入一次+1,数据越大,代表服务器上的数据越新
- vote_id：当前服务器推举的leader服务器的myid。

- vote_zxid：当前服务器推举的leader服务器的最新的zxid。



###### 选举轮次

 Zookeeper选举机制有一个前提条件：在一个轮次的选举中，所有选票必须属于该轮次。在选举的某一时刻，确实可能存在某张选票不属于该轮次的情况。所以Zookeeper在选举过程中，始终会先核对选票的轮次。

###### 选举权重

epoch > dataId > myId,  最后获取票数(大于一半)最多的单选leader

#####  选举

###### 初始化选举

假设有5台服务器参与竞选,myid编号分别为1-5, 并依次启动,情况如下:

- 服务器1启动, epoch,dataid均为0, myid=1,由于没有其它服务器,投票给自己,此时状态为looking
- 服务器2启动, 也是投票给自己, epoch,dataid均为0, myid=2, 同时通过广播与其它服务器(此时只有1)交换结果,经过对比,服务器2胜出,服务器1的票投给2 ,由于得票数不到一半,还是保持looking状态
- 服务器3启动,投票给自己,通过广播交换结果,发现服务器3的myid最大,服务器1,2投票给3,此时3得票3,超过一半.成为leader,服务器1,2成为follower.
- 服务器4启动,投票给自己,然后广播交换数据,发现已经有leader了,然后自动变为follower.
- 服务器5启动,投票给自己,广播交换数据后,发现已经有leader,然后自动变为follower

###### 非初始化选举

情形一:   集群中某一个follewer节点掉线,重新上线后会发起选举,广播交换数据,发现已经存在leader,则自动变为follower,并从leader那更新同步数据

情形二:  leader宕机, 集群会进行崩溃恢复。ZAB 的崩溃恢复分成三个阶段：

**Leader election**:  比较各个子节点的epoch值, dataId, myId,根据值大小选举出准leader, 并改变各节点的状态值.

**Discovery:** 在从节点中发现最新的 ZXID 和事务日志。了防止某些意外情况，比如因网络原因在上一阶段产生多个 Leader 的情况。所以这一阶段，Leader 集思广益，接收所有 Follower 发来各自的最新 epoch 值。Leader 从中选出最大的 epoch，基于此值加 1，生成新的 epoch 分发给各个 Follower。各个 Follower 收到全新的 epoch 后，返回 ACK 给 Leader，带上各自最大的 ZXID 和历史事务日志。Leader 选出最大的 ZXID，并更新自身历史日志。

***Synchronization***

同步阶段，把 Leader 刚才收集得到的最新历史事务日志，同步给集群中所有的 Follower。只有当半数 Follower 同步成功，这个准 Leader 才能成为正式的Leader。

##### 数据写入

ZAB 的数据写入涉及到 Broadcast 阶段，简单来说，就是 Zookeeper 常规情况下更新数据的时候，由 Leader 广播到所有的 Follower。其过程如下：

- 客户端发出写入数据请求给任意 Follower。
- Follower 把写入数据请求转发给 Leader。
- Leader 采用二阶段提交方式，先发送 Propose 广播给 Follower。
- Follower 接到 Propose 消息，写入日志成功后，返回 ACK 消息给 Leader。
- Leader 接到半数以上ACK消息，返回成功给客户端，并且广播 Commit 请求给 Follower



### 参考文献

[尚硅谷Zookeeper 3.5.7版本教程](https://www.bilibili.com/video/BV1to4y1C7gw)

[Zookeeper简介](https://zhuanlan.zhihu.com/p/112710111)

[图解 ZooKeeper的选举机制](https://baijiahao.baidu.com/s?id=1693897375037977921&wfr=spider&for=pc)



 

