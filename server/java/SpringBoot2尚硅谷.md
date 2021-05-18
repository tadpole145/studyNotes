



注:   本文档基于SpringBoot V2.4.5

## SpringBoot

### SpringBoot 基础入门

#### 什么是SpringBoot

能快速集成Spring各个框架的应用框架.用于简化Spring技术栈的脚手架.

优点: 快速创建Spring应用,自动配置第三方依赖,提供生产级别的监控等

缺点: 版本更新快,内部原理复杂,封装太深,不容易精通

#### 如何使用SpringBoot?

##### 系统版本要求

根据SpringBoot版本参照[官方文档](https://spring.io/projects/spring-boot#learn)

##### maven镜像配置

```xml
<mirrors>
      <mirror>
        <id>nexus-aliyun</id>
        <mirrorOf>central</mirrorOf>
        <name>Nexus aliyun</name>
        <url>http://maven.aliyun.com/nexus/content/groups/public</url>
      </mirror>
  </mirrors>
 
  <profiles>
         <profile>
              <id>jdk-1.8</id>
              <activation>
                <activeByDefault>true</activeByDefault>
                <jdk>1.8</jdk>
              </activation>
              <properties>
                <maven.compiler.source>1.8</maven.compiler.source>
                <maven.compiler.target>1.8</maven.compiler.target>
                <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
              </properties>
         </profile>
  </profiles>
```

##### 创建项目

- 使用idea创建maven工程

- pom.xml添加SpringBoot依赖,web依赖,测试模块

- 编写代码,主程序类

  ```xml
   <parent>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-parent</artifactId>
          <version>2.4.5</version>
      </parent>
  
  <dependencies>
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-web</artifactId>
      </dependency>
  </dependencies>
  
  <dependency>
              <groupId>org.springframework.boot</groupId>
              <!--            测试模块，包括JUnit、Hamcrest、Mockito-->
              <artifactId>spring-boot-starter-test</artifactId>
              <scope>test</scope>
          </dependency>
  ```

```java
@SpringBootApplication
public class Springboot1Application {

    public static void main(String[] args) {
        SpringApplication.run(Springboot1Application.class, args);

    }


}
```

##### 创建项目方法2

直接使用idea,选择Spring initialzr 创建....会自动生成可启动的SpringBoot的Hello应用.

##### 打包

直接在右侧找到mvn脚本,双击package即可..,添加maven插件,默认是打jar包,如果需打其余类型包,可以配置方式指定包类型.

```xml
   <build>
        <plugins>
            <plugin>
<!-- 编译插件, 允许程序以jar包执行-->
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
```

![image-20210514112216731](https://i.loli.net/2021/05/14/HnFdMViBvDCexpc.png)

打包

![image-20210514112007349](https://i.loli.net/2021/05/14/IxekGCzZQ98JRsd.png)

##### 打包踩坑

使用SpringBoot 2.4.x版本都会报错

```xml
Maven clean install: Failed to execute goal org.apache.maven.plugins:maven-resources-plugin:3.2.0:resources
```

参考问题解决:

https://stackoverflow.com/questions/65910112/maven-clean-install-failed-to-execute-goal-org-apache-maven-pluginsmaven-resou

```xml
<!--降级该插件版本即可-->
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-resources-plugin</artifactId>
    <version>3.1.0</version>
</plugin>    
```

查看详细问题日志命令

```
mvn clean install -X
```

##### 修改指定依赖的版本号

```java
//去pom里面的SpringBoot的父工程的父工程里面查看该依赖的引用,然后修改成自己想要的版本,示例:
//1、查看spring-boot-dependencies里面规定当前依赖的版本 用的 key。
//2、在当前项目pom.xml里面重写配置
    <properties>
        <mysql.version>5.1.43</mysql.version>
    </properties>

```

##### 容器讲解

###### 组件注册

**spring mvc 组件注册方案**

新建beans.xml ,然后去注册各个bean.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="person01" class="com.example.springboot1.bean.Person">
        <property name="name" value="张三"/>
        <property name="age" value="18"/>
    </bean>


    <bean id="cat" class="com.example.springboot1.bean.Pet">
        <property name="name" value="tom"/>
        <property name="age" value="2"/>
    </bean>
</beans>
```

**SpringBoot中的组件注册**

通过注解的方式,表明这是一个组件

```java
/**
 * 告诉SpringBoot这是一个配置文件,配置类也是组件.
 * 默认容器中是单例的, 获得的组件是Cglib单例代理类, 每次调用都会去容器里面寻找是否已经存在, 这种比较耗时,叫Full模式
 * 当proxyBeanMethods = false时,获得的是原类,而且不是单例的,每获取一次,都会重新生成一个实例,运行较快, 叫Light模式
 * 应用场景: 组件依赖, 保持容器中的对象是唯一的.
 */
@Configuration(proxyBeanMethods = true)
public class MyConfig {

    @Bean//添加组件bean, 方法名=id=组件名, 返回类型=组件类型,返回值=容器中保存的实例
    public Person person1() {
        return new Person("张三", "18", getPet());
    }

    @Bean("tom")  //自定义组件名,此时方法名无效,自定义名=id=组件名
    public Pet getPet() {

        return new Pet("tomcat", "2");
    }

}
```

获取组件并比较

```java
@SpringBootApplication
public class Springboot1Application {

    public static void main(String[] args) {
        //获取返回的IOC容器
        ConfigurableApplicationContext run = SpringApplication.run(Springboot1Application.class, args);
        String[] names = run.getBeanDefinitionNames();
        for (String name : names) {
            System.out.println(name);
        }


        MyConfig myConfig = run.getBean(MyConfig.class);
        System.out.println(myConfig);
        //com.example.springboot1.config.MyConfig$$EnhancerBySpringCGLIB$$9805ef3@3e6f3bae
        //获得的是CGLib代理类,容器中单例存在
        Pet pet = myConfig.getPet();
        Pet tom = run.getBean("tom", Pet.class);
        System.out.println("pet==tom : " + (pet == tom));

    }


}

```

**条件注入组件**
@Conditional(), 根注解,有很多子类, 根据条件来决定组件是否来注册.

![image-20210514173228479](https://i.loli.net/2021/05/14/RECs6DcIj3Ygv2w.png)



**资源注入组件**@ImportResource ,将以前xml注册的bean,以一种中间过渡的方式,注册进容器.

```java
//放置在myConfig类上
@ImportResource("classpath:beans.xml")
```

**属性注入**

```xml
//配置文件 application.properties
car.brand=比亚迪
car.price=100000
```

**1、@ConfigurationProperties + conponent +bean**

```java
/**
 * 只有在容器中的组件，才会拥有SpringBoot提供的强大功能
 */
@Component
@ConfigurationProperties(prefix = "mycar")
public class Car {

    private String brand;
    private Integer price;

    public String getBrand() {
        return brand;
    }

    public void setBrand(String brand) {
        this.brand = brand;
    }

    public Integer getPrice() {
        return price;
    }

    public void setPrice(Integer price) {
        this.price = price;
    }

    @Override
    public String toString() {
        return "Car{" +
                "brand='" + brand + '\'' +
                ", price=" + price +
                '}';
    }
}
```

 **2、@EnableConfigurationProperties + @ConfigurationProperties**

```java
//适用于没被标注@component的第三方对象

@Configuration(proxyBeanMethods = true))
@EnableConfigurationProperties(Car.class)
public class MyConfig {


}

@ConfigurationProperties(prefix = "car")
public class Car {
    public String name;
    public String brand;
}

//配置文件 application.properties
car.brand=比亚迪
car.price=100000
```



#### SpringBoot的底层原理

```java
@SpringBootApplication
//其实是三个注解的综合
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan
//总的来说,就是标注当前类也是一个配置类,然后指定包规则,批量导入一些组件(SpringBoot框架+用户自定义),同时扫描指定包下的组件.
```

@SpringBootConfiguration

> 就是@Configuration,其底层是@Component,代表该类是一个配置类.

@EnableAutoConfiguration

> ```java
> //自动配置包,并指定默认的包规则
> //利用Registrar给容器中导入一系列组件
> //将指定的一个包下的所有组件导入进来 MainApplication 所在包下。
> @AutoConfigurationPackage
> //1. 利用getAutoConfigurationEntry(annotationMetadata);给容器中批量导入一些组件
> //2、调用List<String> configurations = getCandidateConfigurations(annotationMetadata, attributes)获取到所有需要导入到容器中的配置类
> //3、利用工厂加载 Map<String, List<String>> loadSpringFactories(@Nullable ClassLoader classLoader)；得到所有的组件
> //4、从META-INF/spring.factories位置来加载一个文件。默认扫描我们当前系统里面所有META-INF/spring.factories位置的文件 spring-boot-autoconfigure-2.3.4.RELEASE.jar包里面也有META-INF/spring.factories
> @Import({AutoConfigurationImportSelector.class})
> 
> ```

@ComponentScan

> 扫描指定的包

**xxxxxAutoConfiguration ---> 组件  --->** **xxxxProperties里面拿值  ----> application.properties自定义**

#### 最佳实战

- 引入场景依赖, 参考官方文档看看支持哪些依赖https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-starter

- 查看自动配置了哪些（选做）
  - 自己分析，引入场景对应的自动配置一般都生效了
  - 配置文件中debug=true开启自动配置报告。Negative（不生效）\Positive（生效）

- 修改配置
  - 参照文档修改配置项https://docs.spring.io/spring-boot/docs/current/reference/html/appendix-application-properties.html#common-application-properties
  - 自己分析。xxxxProperties绑定了配置文件的哪些。
  - 自定义加入或者替换组件  @Bean、@Component。。。
  - 自定义器  **XXXXXCustomizer**；

##### 开发小技巧

###### 引入Lombok,简化javaBean

```xml
<!-- springBoot已经集成了Lombok,引入依赖即可-->        
		<dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>

<!--第二步,安装对应插件-->
idea中搜索安装lombok插件
<!--第三步,使用-->
常用的注解
@data  set/get方法
@NoArgsConstructor 无参构造
@AllArgsConstructor 全参构造
@ToString   toString方法
@EqualsAndHashCode  hash方法
@Slf4j   注入日志
如果想用部分有参构造,则需手动处理
```

###### 热更新

```xml
     <!--引入热重启后,任何修改,不用重启项目,只需要build即可,其实本质上也是重启项目,真正的热更新是JRebel,需要付费-->
	<!--第一步:  引入依赖-->
		<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <optional>true</optional>
        </dependency>

	<!--第二步:  build project-->
			Ctrl+F9
```

### SpringBoot核心功能

#### 配置文件

- properties 
-  yaml  推荐使用,参考对应的yaml文档介绍,此处略

###### 增加yaml的提示功能

```xml
  <!--参考官方文档附录,添加依赖,然后打包时候排除该依赖,因为这个只是开发过程中方便开发者使用的-->
		<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
            <optional>true</optional>
        </dependency>


 <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <excludes>
                        <exclude>
                            <groupId>org.springframework.boot</groupId>
                            <artifactId>spring-boot-configuration-processor</artifactId>
                        </exclude>
                    </excludes>
                </configuration>
            </plugin>
        </plugins>
    </build>
```

#### SpringBoot Web开发

##### 静态资源的访问

**存放默认路径**: resources下的

- /static
- /public
- /resources
- /META-INF/resources

**访问方式:**  项目根路径/+资源名

**原理:**  SpringBoot对其做了静态映射/**, 当请求静态资源文件时,会先去让controller处理,如果不能处理,则交给静态资源处理器,如果还是不能处理,则相应404页面. 

面临问题:  由于开发中的权限控制,很多接口都需要登录才可以访问,一旦添加拦截器会默认把图片访问权限也拦截掉,对此的处理方法主要用2种:

1. 修改拦截器规则,过滤掉指定后缀的资源文件;
2. 自定义静态资源的路径,给添加自定义前缀

```yaml
spring:
  mvc:
  #添加res前缀, 访问时路径必须添加res
    static-path-pattern: /res/**
#根据官方文档,自定义存放静态资源的路径,是一个数组,可以存放多个路径,资源必须放置在指定目录下, 以下3种方式均可以
  resources:
  #  static-locations: [classpath:/haha/,classpath:/bb/]
    static-locations: classpath:/haha/,classpath:/bb/
 #    static-locations:
 #       - classpath:/haha/
 #       - classpath:/bb/
 #根据以上配置,如果我们要访问/haha下的a.jpg,其完整路径是: 项目根路径/res/a.jpg
```

##### webjar 支持

自动映射 /[webjars](http://localhost:8080/webjars/jquery/3.5.1/jquery.js)/**  

https://www.webjars.org/

```xml
<!--示例: 添加jQuery的依赖-->
        <dependency>
            <groupId>org.webjars</groupId>
            <artifactId>jquery</artifactId>
            <version>3.5.1</version>
        </dependency>
<!--访问地址：http://localhost:8080/webjars/jquery/3.5.1/jquery.js   后面地址要按照依赖里面的包路径-->
```

##### 欢迎页/主页

支持2种配置方式

- 静态资源路径下放置index.html, 但是如果配置自定义的静态资源访问前缀,会导致index.html不能默认访问

- controller能处理/index

##### 自定义Favicon

将favicon.ico 放在静态资源目录下即可。同样的,如果自定义静态资源前缀会导致访问失效.

##### 静态资源配置原理

- SpringBoot启动默认加载  xxxAutoConfiguration 类（自动配置类）
- SpringMVC功能的自动配置类 WebMvcAutoConfiguration，生效

```java
@Configuration(proxyBeanMethods = false)
@ConditionalOnWebApplication(type = Type.SERVLET)
@ConditionalOnClass({ Servlet.class, DispatcherServlet.class, WebMvcConfigurer.class })
@ConditionalOnMissingBean(WebMvcConfigurationSupport.class)
@AutoConfigureOrder(Ordered.HIGHEST_PRECEDENCE + 10)
@AutoConfigureAfter({ DispatcherServletAutoConfiguration.class, TaskExecutionAutoConfiguration.class,
        ValidationAutoConfiguration.class })
public class WebMvcAutoConfiguration {
    
    //内置静态类xxxAdapter绑定了资源
    @SuppressWarnings("deprecation")
	@Configuration(proxyBeanMethods = false)
	@Import(EnableWebMvcConfiguration.class)
	@EnableConfigurationProperties({ WebMvcProperties.class,//绑定了spring.mvc配置项
			org.springframework.boot.autoconfigure.web.ResourceProperties.class, //绑定了spring.resources配置项
                                    WebProperties.class })//绑定了spring.web配置项
	@Order(0)
	public static class WebMvcAutoConfigurationAdapter implements WebMvcConfigurer {
        	....  //省略部分方法
        
        	//有参构造器,所有参数的值都会从容器中确定
        	public WebMvcAutoConfigurationAdapter(WebProperties webProperties, WebMvcProperties mvcProperties,
				ListableBeanFactory beanFactory, ObjectProvider<HttpMessageConverters> messageConvertersProvider,
				ObjectProvider<ResourceHandlerRegistrationCustomizer> resourceHandlerRegistrationCustomizerProvider,
				ObjectProvider<DispatcherServletPath> dispatcherServletPath,
				ObjectProvider<ServletRegistrationBean<?>> servletRegistrations) {
			this.mvcProperties = mvcProperties;
			this.beanFactory = beanFactory;
			this.messageConvertersProvider = messageConvertersProvider;
			this.resourceHandlerRegistrationCustomizer = resourceHandlerRegistrationCustomizerProvider.getIfAvailable();
			this.dispatcherServletPath = dispatcherServletPath;
			this.servletRegistrations = servletRegistrations;
			this.mvcProperties.checkConfiguration();
		}
        
    }
    
    	//添加资源处理规则
		@Override
		protected void addResourceHandlers(ResourceHandlerRegistry registry) {
			super.addResourceHandlers(registry);
            // 配置addMapping =false ,则禁用资源
			if (!this.resourceProperties.isAddMappings()) {
				logger.debug("Default resource handling disabled");
				return;
			}
			ServletContext servletContext = getServletContext();
            //映射webjars,实际上查找的路径是classpath:/META-INF/resources/webjars/, 其实是配置了个别名.
			addResourceHandler(registry, "/webjars/**", "classpath:/META-INF/resources/webjars/");
			addResourceHandler(registry, this.mvcProperties.getStaticPathPattern(), (registration) -> {
                //添加静态资源映射,这个就是默认配置的静态资源路径数组
				registration.addResourceLocations(this.resourceProperties.getStaticLocations());
				if (servletContext != null) {
					registration.addResourceLocations(new ServletContextResource(servletContext, SERVLET_LOCATION));
				}
			});
		}
    
    	//HandlerMapping：处理器映射。保存了每一个Handler能处理哪些请求。	
		@Bean
		public WelcomePageHandlerMapping welcomePageHandlerMapping(ApplicationContext applicationContext,
				FormattingConversionService mvcConversionService, ResourceUrlProvider mvcResourceUrlProvider) {
			WelcomePageHandlerMapping welcomePageHandlerMapping = new WelcomePageHandlerMapping(
					new TemplateAvailabilityProviders(applicationContext), applicationContext, getWelcomePage(),
					this.mvcProperties.getStaticPathPattern());
			welcomePageHandlerMapping.setInterceptors(getInterceptors(mvcConversionService, mvcResourceUrlProvider));
			welcomePageHandlerMapping.setCorsConfigurations(getCorsConfigurations());
			return welcomePageHandlerMapping;
		}

	WelcomePageHandlerMapping(TemplateAvailabilityProviders templateAvailabilityProviders,
			ApplicationContext applicationContext, Optional<Resource> welcomePage, String staticPathPattern) {
		if (welcomePage.isPresent() && "/**".equals(staticPathPattern)) {
            //要用欢迎页功能，必须是/**
			logger.info("Adding welcome page: " + welcomePage.get());
			setRootViewName("forward:index.html");
		}
        //或者是Template
		else if (welcomeTemplateExists(templateAvailabilityProviders, applicationContext)) {
            // 调用Controller  /index
			logger.info("Adding welcome page template: index");
			setRootViewName("index");
		}
	}
    
 	 
    
    
    
}


//ResourceProperties类,配置资源路径
@ConfigurationProperties(prefix = "spring.resources", ignoreUnknownFields = false)
public class ResourceProperties {

	private static final String[] CLASSPATH_RESOURCE_LOCATIONS = { "classpath:/META-INF/resources/",
			"classpath:/resources/", "classpath:/static/", "classpath:/public/" };

	/**
	 * Locations of static resources. Defaults to classpath:[/META-INF/resources/,
	 * /resources/, /static/, /public/].
	 */
	private String[] staticLocations = CLASSPATH_RESOURCE_LOCATIONS;
```

```xml
spring:
  resources:
    add-mappings: false   禁用所有静态资源规则
```

##### 请求参数处理

http请求支持的请求方式有: get,post ,put ,delete, patch等, 原生客户端都支持这些请求方式,但是web表单提交只支持get,post请求

```java
    @RequestMapping(value = "/user",method = RequestMethod.GET)
	//@GetMapping("/user")  可替换上面的使用方式
    public String getUser(){
        return "GET-张三";
    }

    @RequestMapping(value = "/user",method = RequestMethod.POST)
// @PostMapping("/user")
    public String saveUser(){
        return "POST-张三";
    }


    @RequestMapping(value = "/user",method = RequestMethod.PUT)
   //@PutMapping("/user")
    public String putUser(){
        return "PUT-张三";
    }

    @RequestMapping(value = "/user",method = RequestMethod.DELETE)
//  @DeleteMapping("/user")
    public String deleteUser(){
        return "DELETE-张三";
    }

```

通过查看SpringBoot源码发现,为支持form表单提交put,delete请求,它需要作出如下配置.

```yaml
# 1,手动开启支持HiddenHttpMethodFilter配置
spring:
  mvc:
    hiddenmethod:
      filter:
        enabled: true   #开启页面表单的Rest功能
       
```

```html
<!--2,还是以post提交,增加_method的key值,value对应为put提交方式-->
<form  action="/user" method="post">
    <input name="_method" value="put" type="hidden">
    <input type="submit" value="put提交">
</form>
```

###### 原理

```java
public class WebMvcAutoConfiguration {
    //.....省略部分代码
    @Bean
    //没有自定义HiddenHttpMethodFilter时生效
	@ConditionalOnMissingBean(HiddenHttpMethodFilter.class)
	@ConditionalOnProperty(prefix = "spring.mvc.hiddenmethod.filter", name = "enabled", matchIfMissing = false)//默认不开启spring.mvc.hiddenmethod.filter配置项
	public OrderedHiddenHttpMethodFilter hiddenHttpMethodFilter() {
		return new OrderedHiddenHttpMethodFilter();
	}
}

//OrderedHiddenHttpMethodFilter 继承了该类
public class HiddenHttpMethodFilter extends OncePerRequestFilter {
     protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) throws ServletException, IOException {
        HttpServletRequest requestToUse = request;
         //针对form表单做的特殊处理,先判断是否post请求
        if ("POST".equals(request.getMethod()) && request.getAttribute("javax.servlet.error.exception") == null) {
            //再判断是否包含_method的关键字,如果有,则取对应的value值
            String paramValue = request.getParameter(this.methodParam);
            if (StringUtils.hasLength(paramValue)) {
                //将对应的value转为大写
                String method = paramValue.toUpperCase(Locale.ENGLISH);
                //判断用户传的值是否是隐藏的三种请求方法
                if (ALLOWED_METHODS.contains(method)) {
                    //返回一个经过装饰的HttpMethodRequest,本质也是serverlet,只不过重写了getMethod方法
                    requestToUse = new HiddenHttpMethodFilter.HttpMethodRequestWrapper(request, method);
                }
            }
        }

        filterChain.doFilter((ServletRequest)requestToUse, response);
    }
    
     static {
         //支持put,delet,patch三种方法
        ALLOWED_METHODS = Collections.unmodifiableList(Arrays.asList(HttpMethod.PUT.name(), HttpMethod.DELETE.name(), HttpMethod.PATCH.name()));
    }

    private static class HttpMethodRequestWrapper extends HttpServletRequestWrapper {
        private final String method;

        public HttpMethodRequestWrapper(HttpServletRequest request, String method) {
            super(request);
            this.method = method;
        }
		//重写getMethod方法
        public String getMethod() {
            return this.method;
        }
    }
}



```

如果不想用SpringBoot默认的HiddenHttpMethodFilter,那就需要自定义的HiddenHttpMethodFilter了.

###### 自定义filter

```java
@Configuration(proxyBeanMethods = false)
public class MyConfig {
 
    @Bean
    public HiddenHttpMethodFilter hiddenHttpMethodFilter(){
        HiddenHttpMethodFilter methodFilter = new HiddenHttpMethodFilter();
        //设置自定义方法参数的key值
        methodFilter.setMethodParam("_m");
        return methodFilter;
    }

}
```

form表单中的使用

```html
<form  action="/user" method="post">
    <input name="_m" value="put" type="hidden">
    <input type="submit" value="put提交">
</form>
```



