## SpringBoot 笔记



> 学习资源地址: https://blog.didispace.com/spring-boot-learning-2x/



#### SpringBoot 2.0x

##### web项目创建

通过在线[网站](http://start.spring.io/)或者Idea中的Spring Initializr配置SpringBoot项目,pom.xml中引入web依赖就可本地访问了

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

##### 属性配置

配置文件:  application.properties

除了spring内置的属性,这里主要讲解自定义属性的配置与使用

###### 属性的使用

```xml
k1=你好
k2=中国
# 参数间的引用
k3=${k1}+${k2}
#随机字符串
random_string=${random.value} 
#随机int
random_int=${random.int}
#限制左右范围的随机整数10-20
random_int_limit=${random.int(10,20)}
#限制右范围的随机整数, 0-10
random_int_limit_right=${random.int(10)}
#随机长整形
random_long=${random.long}
```

###### 自定义属性的获取

```java
@Component
public class MyProperties {

    @Value("${k1}")
    private String name;
    @Value("${k2}")
    private String title;

    // 省略getter和setter

}
```

###### 命令行赋值

```xml
# 通过使用–server.port属性来设置xxx.jar应用的端口为8888。
# 连续的两个减号--就是对application.properties中的属性值进行赋值的标识。
java -jar xxx.jar --server.port=8888
# 等价于 
server.port=8888
```

命令行访问不安全----->设置屏蔽命令行访问

```java
SpringApplication.setAddCommandLineProperties(false)
```

###### 多环境配置

- `application-dev.properties`：开发环境
- `application-test.properties`：测试环境
- `application-prod.properties`：生产环境

于哪个具体的配置文件会被加载，需要在`application.properties`文件中通过`spring.profiles.active`属性来设置

如`spring.profiles.active=test`就会加载`application-test.properties`配置文件内容

##### RESTful API与单元测试

- `@Controller`：修饰class，用来创建处理http请求的对象
- `@RestController`：Spring4之后加入的注解，原来在`@Controller`中返回json需要`@ResponseBody`来配合，如果直接用`@RestController`替代`@Controller`就不需要再配置`@ResponseBody`，默认返回json格式。
- `@RequestMapping`：配置url映射

```java
@RestController
public class HelloController {

    @RequestMapping("/hello")
    public String index() {
        return "hello world! 这是一个web返回";
    }
}
```

