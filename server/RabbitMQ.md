---
title: RabitMQ介绍与使用
date: 2021-06-11 13:49:12
author: 小蝌蚪
img: 
coverImg: 
top: false
cover: false
toc: true
mathjax: false
summary: 介绍Rabbit消息中间件的安装与使用
tags: [消息中间件]
categories: [开发教程]
comments: false
---





## RabbitMQ

资料来源: [B站视频](https://www.bilibili.com/video/BV1dE411K7MG), 笔记与源文档略有差异,根据自己的理解.重组了部分内容.

#### MQ介绍

##### 什么是MQ?

MQ(Message Quene): 典型的生产者与消费者模型,生产者不断向消息队列中生产消息,消费者不断向队列中获取消息,整个过程是异步的,只负责消息的存取,而不涉及到业务逻辑的侵入.

##### 当前主流MQ

-  ActiveMQ

优点: Apache出品,完全支持JMS规范的消息中间件,丰富的API,多种集群架构模式

缺点: 性能不高,主要是中小型企业在使用.

-  Kafka

Apache开源的顶级项目,它是一个分布式发布-订阅消息系统,其主要特点是基于pull模式来处理消息消费,追求高吞吐量.

缺点: 不支持事务,对消息的重复,丢失,错误没有严格的限制.

- RocketMQ

阿里开源,纯java开发,具有高吞吐量,高可用性,适合大规模分布式系统应用的特点,其思路起源于kafka,对消息的可靠性传输和事务性做了优化.开源版不支持事务,收费版支持.

- RabbitMQ

使用Erlang语言开发的消息队列系统,基于AMQP协议来实现,其主要特征是面向消息,队列,路由,可靠性, 安全. AMQR协议更多用在企业系统内对数据的一致性,稳定性,可靠性要求高的场景,对性能和吞吐量在其次.

#### RabbitMQ

##### AMQP 协议

 		AMQP（advanced message queuing protocol）`在2003年时被提出，最早用于解决金融领不同平台之间的消息传递交互问题。顾名思义，AMQP是一种协议，更准确的说是一种binary wire-level protocol（链接协议）。这是其和JMS的本质差别，AMQP不从API层进行限定，而是直接定义网络交换的数据格式。这使得实现了AMQP的provider天然性就是跨平台的。以下是AMQP协议模型:

![在这里插入图片描述](https://gitee.com/tadpole145/images/raw/main/20210615142230.png)

