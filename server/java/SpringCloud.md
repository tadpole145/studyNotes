---
title: SpringCloud笔记
date: 2021-05-23 19:10:24
author: 小蝌蚪
img: 
coverImg: 
top: false
cover: false
toc: true
mathjax: false
summary: 介绍SpringCloud的使用与集成
tags: [SpringCloud]
categories: [开发教程]
comments: false
---





## SpringCloud

笔记来源于[B站尚硅谷](https://www.bilibili.com/video/BV18E411x7eT)

#### 使用须知

##### 技术要求

- java8
- maven
- git/github
- Nginx
- RabbitMQ
- SpringBoot2

知识章节图谱

![image-20210615151621637](https://gitee.com/tadpole145/images/raw/main/20210615151621.png)



##### 相关文档地址

[官网文档地址](https://spring.io/projects/spring-cloud#overview) ,   [中文版文档地址](https://www.springcloud.cc/),   [github地址](https://github.com/spring-cloud/spring-cloud-release)

版本选择,参考官方文档.

##### 组件升级

![image-20210615160824886](https://gitee.com/tadpole145/images/raw/main/20210615160824.png)

#### 微服务架构编码构建

##### 工程搭建步骤

- New Project
- 选择maven版本
- 字符编码修改File Encoding--> UTF-8
- 注解生效激活 Annotation Processors
- 编译版本选择 java compiler

##### 父工程的pom文件管理

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.example</groupId>
    <artifactId>springcloud</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>pom</packaging>


    <!-- 统一管理jar包版本 -->
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
        <junit.version>4.12</junit.version>
        <log4j.version>1.2.17</log4j.version>
        <lombok.version>1.16.18</lombok.version>
        <mysql.version>8.0.17</mysql.version>
        <druid.version>1.1.16</druid.version>
        <mybatis.spring.boot.version>1.3.0</mybatis.spring.boot.version>
    </properties>

    <!--只声明依赖,并不实现引入, 子模块实现了对应的依赖,无需指定版本号,默认使用父工程的版本 -->
    <dependencyManagement>
        <dependencies>
            <!--spring boot 2.2.2-->
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>2.2.2.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <!--spring cloud Hoxton.SR1-->
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR1</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <!--spring cloud alibaba 2.1.0.RELEASE-->
            <dependency>
                <groupId>com.alibaba.cloud</groupId>
                <artifactId>spring-cloud-alibaba-dependencies</artifactId>
                <version>2.1.0.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <!--mysql-->
            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <version>${mysql.version}</version>
            </dependency>
            <!--mysql-->
            <dependency>
                <groupId>com.alibaba</groupId>
                <artifactId>druid</artifactId>
                <version>${druid.version}</version>
            </dependency>
            <!--mybatis-->
            <dependency>
                <groupId>org.mybatis.spring.boot</groupId>
                <artifactId>mybatis-spring-boot-starter</artifactId>
                <version>${mybatis.spring.boot.version}</version>
            </dependency>
            <!--junit-->
            <dependency>
                <groupId>junit</groupId>
                <artifactId>junit</artifactId>
                <version>${junit.version}</version>
            </dependency>
            <!--log4j-->
            <dependency>
                <groupId>log4j</groupId>
                <artifactId>log4j</artifactId>
                <version>${log4j.version}</version>
            </dependency>
            <dependency>
                <groupId>org.projectlombok</groupId>
                <artifactId>lombok</artifactId>
                <version>${lombok.version}</version>
                <optional>true</optional>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <!--热启动插件-->
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <fork>true</fork>
                    <addResources>true</addResources>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>

```

##### 微服务模块构建

- 建module
- 改pom
- 写yaml
- 主启动
- 业务类
  - 建表
  - 写entities
  - dao
  - service
  - controller

##### 热部署

1. 当前module添加热部署工具依赖

   ```xml
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-devtools</artifactId>
       <scope>runtime</scope>
       <optional>true</optional>
   </dependency>
   
   ```

2. 父pom配置文件中添加插件

```xml
<build>
    <!--下面一项选填-->
    <fileName>你自己的工程名字<fileName>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <configuration>
                <fork>true</fork>
                <addResources>true</addResources>
            </configuration>
        </plugin>
    </plugins>
</build>
```

3. 设置--build--compile勾选如下选项

![image-20210623103352028](https://gitee.com/tadpole145/images/raw/main/20210623103352.png)

4. 在项目任何地方,按住ctrl+alt+shift+/, 选中第一个Registy, 勾选如下选项

![image-20210623103613494](https://gitee.com/tadpole145/images/raw/main/20210623103613.png)

5. 重启IDE即可

