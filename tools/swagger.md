## Swagger详解

#### 简介

[Swagger](https://swagger.io/) 是一个规范和完整的框架，用于生成、描述、调用和可视化 RESTful 风格的 接口文档。

[Spring-fox](https://github.com/springfox/springfox) 是根据代码生成接口文档,它利用AOP特性,把Swagger集成进来,本质上是对swagger的封装.

#### 基础使用

`Swagger` 目前有 `2.x` 和 `3.x` 两个主流版本，配置略有不同。

##### 2.x版本的集成

1. 在pom文件下引入依赖包

```xml
<!-- 
去mvnrepository去搜索, 注意版本差异
https://mvnrepository.com/artifact/io.springfox/springfox-swagger2 -->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.9.2</version>
</dependency>
<!-- UI依赖包 -->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.9.2</version>
</dependency>

```



2. 开启`@EnableSwagger2` 注解
   - 方式1: 主启动类开启该注解
   - 方式2(推荐):  新建一个配置类，通过 `@EnableSwagger2` 注解启用 Swagger, 

```java
@Configuration
@EnableSwagger2   // 开启Swagger
public class SwaggerConfig { 

}
```



3. 在http://localhost:8080/swagger-ui.html查看生成的接口文档.
4. 实例化 `Docket` 的时候，需要传入 `DocumentationType.SWAGGER_2`

##### Swagger 3.X在SpringBoot中的集成

1. pom引入对应的starter启动器依赖

```xml
<!-- https://mvnrepository.com/artifact/io.springfox/springfox-boot-starter -->
<dependency>
  <groupId>io.springfox</groupId>
  <artifactId>springfox-boot-starter</artifactId>
  <version>3.0.0</version>
</dependency>
```

2. 使用 @EnableOpenApi 注解开启 Swagger
   - 方式1: 主启动类开启该注解
   - 方式2(推荐):  新建一个配置类，通过 `@EnableOpenApi ` 注解启用 Swagger, 

3. 访问 http://localhost:8080/swagger-ui/
4. 实例化 Docket 的时候，需要传入 DocumentationType.OAS_30。



#### 自定义配置信息

- 自定义文档基础信息
- 自定义扫描包路径
- 自定义注解,过滤某些接口

```java
//2.x版本
@Configuration
@EnableSwagger2
public class SwaggerConfig {

    @Bean
    public Docket getDocket() {
        Docket docket = new Docket(DocumentationType.SWAGGER_2);
        //设置文档消息
        docket.apiInfo(getApiInfo());
        //设置过滤条件
        docket.select()
                //扫描指定路径下的包(包括子包)
                .apis(RequestHandlerSelectors.basePackage("com.example.swagger"));
        return docket;
    }

    /**
     *
     * @return 关于文档的基本信息
     */
    private ApiInfo getApiInfo() {
        return new ApiInfoBuilder()
                .title("接口文档的title")
                .description("接口文档的描述")
                .version("1.0")
                .contact(new Contact("xxx公司","公司url","xxx@gmail.com"))
                .build();
    }
}

```

```java
//3.x版本
@Configuration
@EnableOpenApi 
public class SwaggerConfig {

    @Value("${spring.profiles.active:NA}")
    private String active;

    @Bean
    public Docket createRestApi() {
        return new Docket(DocumentationType.SWAGGER_2)  // OAS_30
                .enable("dev".equals(active))  // 仅在开发环境开启Swagger
                .apiInfo(apiInfo())
                .host("http://www.example.com")  // Base URL
                .select()
                .apis(RequestHandlerSelectors.basePackage("com.example.controller"))
                .paths(PathSelectors.any())
                .build();
    }

    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title("API文档")
                .description("这是描述信息")
                .contact(new Contact("张三", null, null))
                .version("1.0")
                .build();
    }
}
```







#### 自定义注解

通过自定义注解,搭配过滤器功能使用,可以动态进行接口分类,从而达到某些目的,比如配置某些接口信息不生成文档,或者归类.

```java
//使用示例, 配置该注解的接口不展示
@Target(value = {ElementType.METHOD, ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
public @interface SwaggerIgnore {
    String value() default "";

}

//调用注解
@Configuration
@EnableSwagger2
public class SwaggerConfig {

    @Bean
    public Docket getDocket() {
        Docket docket = new Docket(DocumentationType.SWAGGER_2);
        //设置文档消息
        docket.apiInfo(getApiInfo());
        //设置分组, 一般开发会有多组api接口,比如面向后台管理系统,面向web客户端,app端的接口
        docket.groupName("adminApi");
        //方式1:  设置过滤条件
        docket.select()
                //以下多个过滤条件,可以选择一个,也可以结合多个使用
                //过滤带有@SwaggerIgnore注解(自定义注解)的方法
                .apis(Predicates.not(RequestHandlerSelectors.withMethodAnnotation(SwaggerIgnore.class)))
                //多个路径,任意匹配一个即可
                .paths(Predicates.or(
                        PathSelectors.regex("/api2/.*"),
                        PathSelectors.regex("/api3/.*")
                ))
                .build();


        return docket;
    }


 


    /**
     * @return 关于文档的基本信息
     */
    private ApiInfo getApiInfo() {
        return new ApiInfoBuilder()
                .title("接口文档的title")
                .description("接口文档的描述")
                .version("1.0")
                .contact(new Contact("xxx公司", "公司url", "xxx@gmail.com"))
                .build();
    }
}

```

#### 内置注解

- @Api , 一般作用于controller类上,通过 `tags` 指定描述信息;
- @ApiOperation 主要作用于方法上,描述该方法的作用;
- @ApiParam 主要作用方法于形参前面，可以描述参数的作用;
-  @ApiIgnore  作用于方法上,被注解后不生成文档;
- @ApiModel 作用于实体类上,可以通过 `value` 指定别名，不指定时默认为类名
- @ApiModelProperty 作用于实体类中的属性
- @ApiImplicitParam 作用于方法上,用于解释参数

![image-20210818204637079](C:\Users\小蝌蚪\AppData\Roaming\Typora\typora-user-images\image-20210818204637079.png)

```java
//示例如下
@RestController
@RequestMapping("/api")
@Api(tags = {"标签1", "标签2"}) //给当前类打标签.写几个标签就展示几个控制器,建议1个即可
public class SwaggerController {

    /**
     * @ApiOperation : 标注在方法上
     *         - value  给接口起别名
     *         - notes  接口说明描述
     *
     */
    @GetMapping("/test1")
    @ApiOperation(value = "get请求", nickname = "昵称", notes = "笔记")
    public String test1() {
        return "test1";
    }

    @PostMapping("/test2")
    public String test2(@ApiParam(value = "参数说明解释", name = "字段别名", required = true,example = "张三")
                                     String name) {
        return name;
    }


    @GetMapping("/test3")
    @ApiIgnore  //文档忽略此接口
    public String test3() {
        return "test1";
    }


    @GetMapping("/test4")
    @ApiImplicitParam(name = "m",value = "参数描述",required = true,dataType = "键值对",paramType = "String",allowMultiple = true)
    public String test4(String m, String b) {
        return "test4";
    }

    @PostMapping("/test5")
    public Person test5(
            @ApiParam(value = "name,age必传",e)
            @RequestBody Person person) {
        return person;
    }


}



//-----------------------------------------------实体属性示例----------------------------------------
@ApiModel(value = "person实体",description = "包含3个属性信息")
public class Person implements Serializable {

@ApiModelProperty(name = "别名name",value = "name属性注释", example = "zhangsan")
    private String name;
    @ApiModelProperty(name = "别名sex",value = "sex属性注释", example = "男")
    private String sex;
    @ApiModelProperty(name = "别名age",value = "age属性注释", example = "18")
    private Integer age;

    public Person() {
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getSex() {
        return sex;
    }

    public void setSex(String sex) {
        this.sex = sex;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }


    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", sex='" + sex + '\'' +
                ", age=" + age +
                '}';
    }
}

```

对于map参数的方法,如果参数小于5个，推荐使用 @ApiImplicitParams的方式单独封装每一个参数；如果参数大于5个，采用定义一个对象去封装所有参数的属性,配合@RequestBody注解，然后使用@APiParam的方式

在GET请求中，参数在Body体里面,不能使用@RequestBody。在POST请求，可以使用@RequestBody和@RequestParam，当参数为对象时,使用@RequestBody，当参数为基本类型或者String, Map等,@RequestParam,也可以不使用注解,建议使用. 

ps:  当不使用注解时,从swagger看post请求,看起来很像是get拼接,其实并非如此,使用postman测试即可.

swagger在生产环境不能对外暴露,可以使用@Profile({“dev”, “prod”,“pre”})指定可以使用的环境. 不过一般在配置类对所有接口进行控制,而不会针对当个controller或者接口进行控制.

