



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

##### 请求映射原理

![image.png](https://i.loli.net/2021/05/19/RevEgby3WZ7AXho.png)

每个请求,最后都会执行doDispatch方法.

```java
/**
 * 所有的http请求都会走该方法,具体执行,所有请求将依次通过应用servlet的HandlerMappings获得能处理该请求的HandlerAdapter。 
 */
protected void doDispatch(HttpServletRequest request, HttpServletResponse response) throws Exception {
   HttpServletRequest processedRequest = request;
   HandlerExecutionChain mappedHandler = null;
   boolean multipartRequestParsed = false;

   WebAsyncManager asyncManager = WebAsyncUtils.getAsyncManager(request);

   try {
      ModelAndView mv = null;
      Exception dispatchException = null;
	//上面都是一些变量的初始化
      try {
          //检查是否为文件传输请求
         processedRequest = checkMultipart(request);
         multipartRequestParsed = (processedRequest != request);

        //找到当前请求使用哪个Handler（Controller的方法）处理
         mappedHandler = getHandler(processedRequest);
         if (mappedHandler == null) {
            noHandlerFound(processedRequest, response);
            return;
         }

         // Determine handler adapter for the current request.
         HandlerAdapter ha = getHandlerAdapter(mappedHandler.getHandler());

         // Process last-modified header, if supported by the handler.
         String method = request.getMethod();
         boolean isGet = "GET".equals(method);
         if (isGet || "HEAD".equals(method)) {
            long lastModified = ha.getLastModified(request, mappedHandler.getHandler());
            if (new ServletWebRequest(request, response).checkNotModified(lastModified) && isGet) {
               return;
            }
         }

         if (!mappedHandler.applyPreHandle(processedRequest, response)) {
            return;
         }

         // Actually invoke the handler.
         mv = ha.handle(processedRequest, response, mappedHandler.getHandler());

         if (asyncManager.isConcurrentHandlingStarted()) {
            return;
         }

         applyDefaultViewName(processedRequest, mv);
         mappedHandler.applyPostHandle(processedRequest, response, mv);
      }
      catch (Exception ex) {
         dispatchException = ex;
      }
      catch (Throwable err) {
         // As of 4.3, we're processing Errors thrown from handler methods as well,
         // making them available for @ExceptionHandler methods and other scenarios.
         dispatchException = new NestedServletException("Handler dispatch failed", err);
      }
      processDispatchResult(processedRequest, response, mappedHandler, mv, dispatchException);
   }
   catch (Exception ex) {
      triggerAfterCompletion(processedRequest, response, mappedHandler, ex);
   }
   catch (Throwable err) {
      triggerAfterCompletion(processedRequest, response, mappedHandler,
            new NestedServletException("Handler processing failed", err));
   }
   finally {
      if (asyncManager.isConcurrentHandlingStarted()) {
         // Instead of postHandle and afterCompletion
         if (mappedHandler != null) {
            mappedHandler.applyAfterConcurrentHandlingStarted(processedRequest, response);
         }
      }
      else {
         // Clean up any resources used by a multipart request.
         if (multipartRequestParsed) {
            cleanupMultipart(processedRequest);
         }
      }
   }
}
```

![image.png](https://i.loli.net/2021/05/19/OcdEnXUvWYJLbkA.png)

**RequestMappingHandlerMapping**：保存了所有@RequestMapping 和handler的映射规则.

**WelcomePageHandlerMapping**  : 用于存储访问index的访问规则

如果我们需要一些自定义的映射处理，我们也可以自己给容器中放**HandlerMapping**。自定义 **HandlerMapping**

##### 请求参数与注解

- @PathVariable、路径变量
- @RequestHeader、请求头
- @ModelAttribute、 
- @RequestParam、请求参数
- @MatrixVariable、
- @CookieValue、cookie值
- @RequestBody   请求体
- @RequestAttribute 设置请求转发传递参数



> ```java
> @RestController
> public class ParameterTestController {
> 
> 
>     //  car/2/owner/zhangsan  restful请求风格使用
>     @GetMapping("/car/{id}/owner/{username}")
>     public Map<String,Object> getCar(@PathVariable("id") Integer id,   //PathVariable,  动态替换请求路径的值
>                                      @PathVariable("username") String name,
>                                      @PathVariable Map<String,String> pv,  //自动将路径变量值存进map
>                                      @RequestHeader("User-Agent") String userAgent, //获取指定的请求头
>                                      @RequestHeader Map<String,String> header,  //获取所有的请求头
>                                      //car?age=18&inters=basketball  拼接请求链接时候使用RequestParam
>                                      @RequestParam("age") Integer age,  //请求参数
>                                      @RequestParam("inters") List<String> inters,
>                                      //自动将拼接请求链接的参数放入map集合中
>                                      @RequestParam Map<String,String> params,
>                                      @CookieValue("_ga") String _ga,
>                                      @CookieValue("_ga") Cookie cookie){
> 
> 
>         Map<String,Object> map = new HashMap<>();
> 
> //        map.put("id",id);
> //        map.put("name",name);
> //        map.put("pv",pv);
> //        map.put("userAgent",userAgent);
> //        map.put("headers",header);
>         map.put("age",age);
>         map.put("inters",inters);
>         map.put("params",params);
>         map.put("_ga",_ga);
>         System.out.println(cookie.getName()+"===>"+cookie.getValue());
>         return map;
>     }
> 
> 
>     @PostMapping("/save")
>     public Map postMethod(@RequestBody String content){
>         Map<String,Object> map = new HashMap<>();
>         map.put("content",content);
>         return map;
>     }
> 
> 
>     //1、语法： 请求路径：/cars/sell;low=34;brand=byd,audi,yd
>     //2、SpringBoot默认是禁用了矩阵变量的功能
>     //      手动开启：原理。对于路径的处理。UrlPathHelper进行解析。
>     //              removeSemicolonContent（移除分号内容）支持矩阵变量的
>     //3、矩阵变量必须有url路径变量才能被解析
>     @GetMapping("/cars/{path}")
>     public Map carsSell(@MatrixVariable("low") Integer low,
>                         @MatrixVariable("brand") List<String> brand,
>                         @PathVariable("path") String path){
>         Map<String,Object> map = new HashMap<>();
> 
>         map.put("low",low);
>         map.put("brand",brand);
>         map.put("path",path);
>         return map;
>     }
> 
>     // /boss/1;age=20/2;age=10
> 
>     @GetMapping("/boss/{bossId}/{empId}")
>     public Map boss(@MatrixVariable(value = "age",pathVar = "bossId") Integer bossAge,
>                     @MatrixVariable(value = "age",pathVar = "empId") Integer empAge){
>         Map<String,Object> map = new HashMap<>();
> 
>         map.put("bossAge",bossAge);
>         map.put("empAge",empAge);
>         return map;
> 
>     }
> }
> ```

 转发并传递参数到下个页面@RequestAttribute

```java
@Controller
public class ParameterTestController {

    @GetMapping("/toIndex")
    public String goIndex() {
        return "forward:/index.html";  //转发到index页面
    }
    
    
    @GetMapping("/toSuccess")
    public String toSuccess(HttpServletRequest request) {
        //通过HttpServletRequest设置参数
        request.setAttribute("msg" ,"属性值");
        return "forward:/success";
    }

    @ResponseBody
    @GetMapping("/success")
    public Map<String, Object> success(@RequestAttribute("msg")String msg, HttpServletRequest request) {
        //方法1.  直接通过@RequestAttribute接受参数
        Map<String,Object> map = new HashMap<>();
        map.put("msg", msg);
        //方法2, 通过HttpServletRequest的getAttribute获取
        String attribute = (String) request.getAttribute("msg");
        return map;
    }
    
}
```

##### 复杂参数

**Map**、**Model（map、model里面的数据会被放在request的请求域  request.setAttribute）、HttpServletRequest、**Errors/BindingResult、**RedirectAttributes（ 重定向携带数据）**、**ServletResponse（response）**、SessionStatus、UriComponentsBuilder、ServletUriComponentsBuilder

```java
//放置数据
Map<String,Object> map,  Model model, HttpServletRequest request 都是可以给request域中放数据，
//获取数据
request.getAttribute();
//原理
Map、Model类型的参数，会返回 mavContainer.getModel（）；---> BindingAwareModelMap 是Model 也是Map, 里面保存着之前存放的kev,value值
 然后这个BindingAwareModelMap会被加入到mergeModel中,最后会被遍历,被request.setAttribute放置在请求域中.
```

##### 自定义对象参数传递

```java
/**
 *     HTML 页面直接传输对应的自定义对象属性值
 *     姓名： <input name="userName"/> <br/>
 *     年龄： <input name="age"/> <br/>
 *     生日： <input name="birth"/> <br/>
 *     宠物姓名：<input name="pet.name"/><br/>
 *     宠物年龄：<input name="pet.age"/>
 */
@Data
public class Person {
    
    private String userName;
    private Integer age;
    private Date birth;
    private Pet pet;
    
}

@Data
public class Pet {

    private String name;
    private String age;

}

 
```

##### 参数传递原理

- HandlerMapping中找到能处理请求的Handler（Controller.method()）
- 为当前Handler 找一个适配器 HandlerAdapter； **RequestMappingHandlerAdapter**
- 适配器执行目标方法并确定方法参数的每一个值

![image.png](https://i.loli.net/2021/05/21/8aftckTJXV3DEdC.png)

- 执行目标方法

```java

mav = invokeHandlerMethod(request, response, handlerMethod); //执行目标方法

//ServletInvocableHandlerMethod
Object returnValue = invokeForRequest(webRequest, mavContainer, providedArgs);
//获取方法的参数值
Object[] args = getMethodArgumentValues(request, mavContainer, providedArgs);
```

- 使用参数解析器解析参数,支持就调用 resolveArgument

- 使用返回处理器进行处理

- 目标方法执行完成,所有数据都放在ModelAndViewContainer中,包含要去的页面地址View。还包含Model数据。

- 处理派发结果**processDispatchResult**(processedRequest, response, mappedHandler, mv, dispatchException);

  

  renderMergedOutputModel(mergedModel, getRequestToExpose(request), response);

###### 自定义 Converter

```java
    //1、WebMvcConfigurer定制化SpringMVC的功能
	//解析cat,3这样的自定义Pet数据
    @Bean
    public WebMvcConfigurer webMvcConfigurer(){
        return new WebMvcConfigurer() {
            @Override
            public void configurePathMatch(PathMatchConfigurer configurer) {
                UrlPathHelper urlPathHelper = new UrlPathHelper();
                // 不移除；后面的内容。矩阵变量功能就可以生效
                urlPathHelper.setRemoveSemicolonContent(false);
                configurer.setUrlPathHelper(urlPathHelper);
            }

            @Override
            public void addFormatters(FormatterRegistry registry) {
                registry.addConverter(new Converter<String, Pet>() {

                    @Override
                    public Pet convert(String source) {
                        // 啊猫,3
                        if(!StringUtils.isEmpty(source)){
                            Pet pet = new Pet();
                            String[] split = source.split(",");
                            pet.setName(split[0]);
                            pet.setAge(Integer.parseInt(split[1]));
                            return pet;
                        }
                        return null;
                    }
                });
            }
        };
    }
```

##### 数据响应与内容协商

![image-20210521144010324](https://i.loli.net/2021/05/21/cqPLHQEl2BKCybM.png)

web场景自动引入了json场景,默认返回json数据,如果需要返回其余类型数据,比如xml,则需要引入xml依赖.

```xml
 <dependency>
            <groupId>com.fasterxml.jackson.dataformat</groupId>
            <artifactId>jackson-dataformat-xml</artifactId>
</dependency>
```



###### SpringMVC支持的返回值

```java
ModelAndView
Model
View
ResponseEntity 
ResponseBodyEmitter
StreamingResponseBody
HttpEntity
HttpHeaders
Callable
DeferredResult
ListenableFuture
CompletionStage
WebAsyncTask
有 @ModelAttribute 且为对象类型的
@ResponseBody 注解 ---> RequestResponseBodyMethodProcessor；
```

###### 默认的MessageConverter

![image.png](https://i.loli.net/2021/05/21/VIkPUOcNMKnJv7F.png)

```
0 - 只支持Byte类型的
1 - String
2 - String
3 - Resource
4 - ResourceRegion
5 - DOMSource.class \ SAXSource.class) \ StAXSource.class \StreamSource.class \Source.class
6 - MultiValueMap
7 - true 
8 - true
9 - 支持注解方式xml处理的。
```

###### 开启浏览器参数内容协商功能

```yaml
#1. 开启请求参数内容协商模式
spring:
    contentnegotiation:
      favor-parameter: true  
#2 浏览器请求地址中输入format参数,比如
localhost:8080/test?format=json
```

##### 内容协商原理

1. 判断当前响应头中是否已经有确定的媒体类型。MediaType
2. 获取客户端（PostMan、浏览器）支持接收的内容类型。（获取客户端Accept请求头字段）
3. 遍历循环所有当前系统的 **MessageConverter**，看谁支持操作这个对象
4. 找到支持操作Person的converter，把converter支持的媒体类型统计出来。
5. 客户端需要【application/xml】。服务端能力【10种、json、xml】
6. 进行内容协商的最佳匹配媒体类型
7. 用 支持 将对象转为 最佳匹配媒体类型 的converter。调用它进行转化 。

##### 自定义消息处理器

```java
//添加自定义的消息处理器
 @Bean
    public WebMvcConfigurer webMvcConfigurer() {
        return new WebMvcConfigurer() {
            @Override
            public void extendMessageConverters(List<HttpMessageConverter<?>> converters) {
                converters.add(new MyCustomMessageConverter());

            }
            
            
        };
    }


//自定义消息处理器
public class MyCustomMessageConverter implements HttpMessageConverter<Person> {

    //支持读该自定义数据类型,客户端传给服务端, 如果没这个需求,可默认返回false
    @Override
    public boolean canRead(Class clazz, MediaType mediaType) {
        return false;
    }

    @Override
    public boolean canWrite(Class clazz, MediaType mediaType) {
        //此处我们是自定义person的协议, 所以只有该类为person时候,才支持写操作
        return clazz.isAssignableFrom(Person.class);  //判断该类是否是person的超类
    }

    //服务器要统计所有支持的mediaTypes,这儿需要添加自定义数据类型,解析并返回
    @Override
    public List<MediaType> getSupportedMediaTypes() {
        return MediaType.parseMediaTypes("application/x-custom");
    }

    @Override
    public Person read(Class<? extends Person> clazz, HttpInputMessage inputMessage) throws IOException, HttpMessageNotReadableException {
        return null;
    }

    @Override
    public void write(Person person, MediaType contentType, HttpOutputMessage outputMessage) throws IOException, HttpMessageNotWritableException {
        //自定义数据的写出, 此处约定自定义数据为属性以分号分割
        String data = person.getName() + ";" + person.getAge();
        //获取输出流,将数据写出去
        OutputStream body = outputMessage.getBody();
        body.write(data.getBytes());
    }


}


```

##### 自定义参数内容协商策略

根据源码可知,SpringBoot支持2种消息策略来返回浏览器所支持的消息格式.原生客户端根据请求头设置消息策略,浏览器无法修改请求头,而是通过增加请求参数来定义消息策略的.自定义如下:

```java
 @Bean
    public WebMvcConfigurer webMvcConfigurer() {
        return new WebMvcConfigurer() {
            @Override
            public void extendMessageConverters(List<HttpMessageConverter<?>> converters) {
                converters.add(new MyCustomMessageConverter());

            }

            @Override
            public void configureContentNegotiation(ContentNegotiationConfigurer configurer) {
                //此处的configurer会覆盖掉原来的消息策略,必须添加上
                //设置请求参数消息策略
                HashMap<String, MediaType> mediaTypes = new HashMap<>();
                mediaTypes.put("json", MediaType.APPLICATION_JSON);
                mediaTypes.put("xml", MediaType.APPLICATION_XML);
                //将自定义的请求参数key值与定义的消息格式配对
                mediaTypes.put("custom",  MediaType.parseMediaType("application/x-custom"));
                ParameterContentNegotiationStrategy paramStrategy = new ParameterContentNegotiationStrategy(mediaTypes);
                //设置自定义消息请求参数的K值, 默认是format
                paramStrategy.setParameterName("msgType");
                //获取请求体策略
                HeaderContentNegotiationStrategy headerStrategy = new HeaderContentNegotiationStrategy();
                configurer.strategies(Arrays.asList(paramStrategy, headerStrategy));
            }
        };
    }
```

##### 自定义拦截器配置

```java
 @Override
            public void addInterceptors(InterceptorRegistry registry) {
                registry.addInterceptor(new HandlerInterceptor() {
                    @Override
                    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
//                        String token = request.getHeader("Authorization");
//                        if (ObjectUtils.isEmpty(token)) {
//                            request.setAttribute("code",304);
//                            response.setStatus(304);
//                            return false;
//                        }
                        return true;
                    }
                })
                    //拦截所有的请求
                .addPathPatterns("/**")
                    //过滤指定的请求
                .excludePathPatterns("*/*.png","/login","/*.png");
            }
```

##### 拦截器原理

**1、**根据当前请求，找到**HandlerExecutionChain【**可以处理请求的handler以及handler的所有 拦截器】

**2、**先来**顺序执行** 所有拦截器的 preHandle方法

- 1、如果当前拦截器prehandler返回为true。则执行下一个拦截器的preHandle
- 2、如果当前拦截器返回为false。直接   倒序执行所有已经执行了的拦截器的  afterCompletion；

**3、如果任何一个拦截器返回false。直接跳出不执行目标方法**

**4、所有拦截器都返回True。执行目标方法**

**5、倒序执行所有拦截器的postHandle方法。**

**6、前面的步骤有任何异常都会直接倒序触发** afterCompletion

7、页面成功渲染完成以后，也会倒序触发 afterCompletion

![image.png](https://i.loli.net/2021/05/26/Q6zN31UZrWRPuGM.png)

##### 文件上传

```java
    /**
     * MultipartFile 自动封装上传过来的文件
     * @param email
     * @param username
     * @param headerImg
     * @param photos
     * @return
     */
    @PostMapping("/upload")
    public String upload(@RequestParam("email") String email,
                         @RequestParam("username") String username,
                         @RequestPart("headerImg") MultipartFile headerImg,
                         @RequestPart("photos") MultipartFile[] photos) throws IOException {

        log.info("上传的信息：email={}，username={}，headerImg={}，photos={}",
                email,username,headerImg.getSize(),photos.length);

        if(!headerImg.isEmpty()){
            //保存到文件服务器，OSS服务器
            String originalFilename = headerImg.getOriginalFilename();
            headerImg.transferTo(new File("H:\\cache\\"+originalFilename));
        }

        if(photos.length > 0){
            for (MultipartFile photo : photos) {
                if(!photo.isEmpty()){
                    String originalFilename = photo.getOriginalFilename();
                    photo.transferTo(new File("H:\\cache\\"+originalFilename));
                }
            }
        }


        return "main";
    }

```

```yaml
servlet:
    multipart:
      max-file-size: 10MB    #修改单个文件最大限制为10MB,默认是1MB
      max-request-size: 100MB  # 修改单次请求最大上传文件的总大小为100MB,默认是10MB
```

##### 文件上传原理

**文件上传自动配置类-MultipartAutoConfiguration-****MultipartProperties**

- 自动配置好了 **StandardServletMultipartResolver  【文件上传解析器】**
- **原理步骤**

- **1、请求进来使用文件上传解析器判断（**isMultipart**）并封装（**resolveMultipart，**返回**MultipartHttpServletRequest**）文件上传请求**
- **2、参数解析器来解析请求中的文件内容封装成MultipartFile**
- **3、将request中文件信息封装为一个Map；**MultiValueMap<String, MultipartFile>

**FileCopyUtils**。实现文件流的拷贝

##### 异常处理

###### 错误处理

- 默认情况下，Spring Boot提供`/error`处理所有错误的映射
- 对于机器客户端，它将生成JSON响应，其中包含错误，HTTP状态和异常消息的详细信息。对于浏览器客户端，响应一个“ whitelabel”错误视图，以HTML格式呈现相同的数据.

- **要对其进行自定义，添加****`View`****解析为`error```**
- 要完全替换默认行为，可以实现 `ErrorController `并注册该类型的Bean定义，或添加`ErrorAttributes类型的组件`以使用现有机制并替换其内容。
- error/下的4xx，5xx页面会被自动解析；

###### 定制错误处理逻辑

- error/404.html  error/5xx.html；有精确的错误状态码页面就匹配精确，没有就找 4xx.html；如果都没有就触发默认的错误白页
- @ControllerAdvice+@ExceptionHandler处理全局异常；底层是 **ExceptionHandlerExceptionResolver 支持的**

##### web原生组件servlet,Filter,Listener的注入

方式一.

> ```
> 在Springboot1Application上添加@ServletComponentScan注解
> 自定义MyServlet类, Filter类,或者Listener类
> ```

```java
// 1.扫描指定包名下的自定义组件
@ServletComponentScan(basePackages = "com.exanple.springboot")
@SpringBootApplication
public class Springboot1Application {

    public static void main(String[] args) {
        //获取返回的IOC容器
        ConfigurableApplicationContext run = SpringApplication.run(Springboot1Application.class, args);

    }


}
//2. 自定义servlet组件
//添加注解,并指定访问路径, 该自定义组件不会被拦截器拦截.
@WebServlet(urlPatterns = "/my")
public class MyServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        super.doGet(req, resp);
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        super.doPost(req, resp);
    }
}
//自定义过滤器
//过滤指定路径下的请求
@WebFilter(urlPatterns = {"/css/*","/images/*"})
public class MyFilter implements Filter {
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        
    }

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {

    }

    @Override
    public void destroy() {

    }
}
//自定义监听器
@WebListener
public class MyServletContextListener implements ServletContextListener {
    @Override
    public void contextInitialized(ServletContextEvent sce) {
        //监听项目初始化
    }

    @Override
    public void contextDestroyed(ServletContextEvent sce) {
        //监听项目被正常关闭

    }
}

```

方法二

使用RegistrationBean,  ServletRegistrationBean/FilterRegistrationBean/ServletListenerRegistrationBean

```java
//添加注解Configuration, bean注解,同时去掉MyServlet上的WebServlet注解
@Configuration
public class MyRegistConfig {

    @Bean
    public ServletRegistrationBean myServlet(){
        MyServlet myServlet = new MyServlet();

        return new ServletRegistrationBean(myServlet,"/my","/my02");
    }


    @Bean
    public FilterRegistrationBean myFilter(){

        MyFilter myFilter = new MyFilter();
//        return new FilterRegistrationBean(myFilter,myServlet());
        FilterRegistrationBean filterRegistrationBean = new FilterRegistrationBean(myFilter);
        filterRegistrationBean.setUrlPatterns(Arrays.asList("/my","/css/*"));
        return filterRegistrationBean;
    }

    @Bean
    public ServletListenerRegistrationBean myListener(){
        MySwervletContextListener mySwervletContextListener = new MySwervletContextListener();
        return new ServletListenerRegistrationBean(mySwervletContextListener);
    }
}
```

##### 切换嵌入式Servlet容器

- 默认支持的webServer
  - `Tomcat`, `Jetty`,netty,  or `Undertow`
  - `ServletWebServerApplicationContext 容器启动寻找ServletWebServerFactory 并引导创建服务器`

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <exclusions>
        <!--排除默认的tomcat服务器,然后引入想要支持的web服务器--->
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

###### 原理

- SpringBoot应用启动发现当前是Web应用。web场景包-导入tomcat
- web应用会创建一个web版的ioc容器`ServletWebServerApplicationContext` 
- `ServletWebServerApplicationContext`  启动的时候寻找 **`ServletWebServerFactory`**`（Servlet 的web服务器工厂---> Servlet 的web服务器）`  
- SpringBoot底层默认有很多的WebServer工厂；`TomcatServletWebServerFactory`, `JettyServletWebServerFactory`, or `UndertowServletWebServerFactory`
- `底层直接会有一个自动配置类。ServletWebServerFactoryAutoConfiguration`
- `ServletWebServerFactoryAutoConfiguration导入了ServletWebServerFactoryConfiguration（配置类）`
- `ServletWebServerFactoryConfiguration 配置类 根据动态判断系统中到底导入了那个Web服务器的包。（默认是web-starter导入tomcat包），容器中就有 TomcatServletWebServerFactory`
- `TomcatServletWebServerFactory 创建出Tomcat服务器并启动；TomcatWebServer 的构造器拥有初始化方法initialize---this.tomcat.start();`
- `内嵌服务器，就是手动把启动服务器的代码调用（tomcat核心jar包存在）`

### 数据访问

####  使用jdbc数据库连接的配置

```xml
		<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>

```

```yaml
spring:
  datasource:
     url: jdbc:mysql://localhost:3306/db1
     username: root
     password: 123
     driver-class-name: com.mysql.jdbc.Driver
```

#### 使用Druid数据源

[druid官方github地址](https://github.com/alibaba/druid)

集成的2种方式

- 自定义

- 找官方提供的starer (**推荐**)

具体集成步骤,参照github官方文档.

#### 整合MyBatis

参考[github地址](https://github.com/mybatis), [官网文档地址](https://mybatis.org/mybatis-3/zh/getting-started.html) 引入依赖

```xml
 		<dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>2.1.4</version>
        </dependency>
```

###### **通过配置文件方式**,  使用步骤如下:

- 导入mybatis官方starter
- 编写mapper接口。标准@Mapper注解
- 编写sql映射文件并绑定mapper接口
- 在application.yaml中指定Mapper配置文件的位置，以及指定全局配置文件的信息 （建议；**配置在mybatis.configuration中,而不是写在专门的配置文件中**）
- 通过service调用mapper接口并返回结果

```java
//步骤二
@Mapper
public interface TestMapper {
    Account getAcc(int id);
}

```

```xml
<!--步骤三--->
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.example.springboot1.mapper.TestMapper">
    <select id="getAcc" resultType="com.example.springboot1.bean.Account">
        select * from t1 where id =#{id}
    </select>

</mapper>
```

```yaml
# 步骤四
mybatis:
#  config-location: classpath:mybatis/mybatis-config.xml  #开启了configuration,则不能指定配置文件,二者只能存在一个.
  mapper-locations: classpath:mybatis/mapper/*.xml
  configuration: #mybatis全局配置, 推荐使用
    map-underscore-to-camel-case: true
```

```xml
<!--mybatis全局配置的另外一种方式,不推荐-->
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

    <settings>
<!--        开启驼峰命名匹配, 没有提示,建议使用配置文件配置-->
        <setting name="mapUnderscoreToCamelCase" value="true"/>
    </settings>

</configuration>
```

```java
    //步骤五
    @Service
    public class AccService {
        @Autowired
        TestMapper mTestMapper;

        public Account getAcc(int id) {
            return mTestMapper.getAcc(id);
        }
    }

//在controller中调用

    @Autowired
    AccService mAccService;


    @GetMapping("/acc/{id}")
    public Account getAcc(@PathVariable("id") int id) {
        Account acc = mAccService.getAcc(id);
        return acc;
    }
```



###### 通过注解整合Mybatis

步骤与上面一样,唯一区别是第四步中的mapper映射文件使用了注解替代.

```java
@Mapper
public interface TestMapper {
    //需要xml配置映射文件,以及对应的sql语句
    Account getAcc(int id);

	//通过注解直接配置,推荐使用.
    @Select("select * form t1 where id=#{id}")
    Account getAccount(int id);
}
```

###### **最佳实战：**

- 引入mybatis-starter
- **配置application.yaml中，配置configration**
- 编写Mapper接口并标注@Mapper注解
- 简单方法直接注解方式
- 复杂方法编写mapper.xml进行绑定映射
- *在主程序入口标注@MapperScan("com.atguigu.admin.mapper") 简化，其他的接口就可以不用标注@Mapper注解*

#### 整合 MyBatis-Plus 完成CRUD

[MyBatis-Plus](https://github.com/baomidou/mybatis-plus)（简称 MP）是一个 [MyBatis](http://www.mybatis.org/mybatis-3/) 的增强工具，在 MyBatis 的基础上只做增强不做改变，为简化开发、提高效率而生。

[mybatis plus 官网](https://baomidou.com/)

优点:   只需要我们的Mapper继承 BaseMapper 就可以拥有crud能力,而无需手写sql语句.



#### 整合Redis

Redis 是一个开源（BSD许可）的，内存中的数据结构存储系统，它可以用作数据库、**缓存**和消息中间件。 它支持多种类型的数据结构，如 [字符串（strings）](http://www.redis.cn/topics/data-types-intro.html#strings)， [散列（hashes）](http://www.redis.cn/topics/data-types-intro.html#hashes)， [列表（lists）](http://www.redis.cn/topics/data-types-intro.html#lists)， [集合（sets）](http://www.redis.cn/topics/data-types-intro.html#sets)， [有序集合（sorted sets）](http://www.redis.cn/topics/data-types-intro.html#sorted-sets) 与范围查询， [bitmaps](http://www.redis.cn/topics/data-types-intro.html#bitmaps)， [hyperloglogs](http://www.redis.cn/topics/data-types-intro.html#hyperloglogs) 和 [地理空间（geospatial）](http://www.redis.cn/commands/geoadd.html) 索引半径查询。 Redis 内置了 [复制（replication）](http://www.redis.cn/topics/replication.html)，[LUA脚本（Lua scripting）](http://www.redis.cn/commands/eval.html)， [LRU驱动事件（LRU eviction）](http://www.redis.cn/topics/lru-cache.html)，[事务（transactions）](http://www.redis.cn/topics/transactions.html) 和不同级别的 [磁盘持久化（persistence）](http://www.redis.cn/topics/persistence.html)， 并通过 [Redis哨兵（Sentinel）](http://www.redis.cn/topics/sentinel.html)和自动 [分区（Cluster）](http://www.redis.cn/topics/cluster-tutorial.html)提供高可用性（high availability）。

```xml
<!---引入依赖-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
```

###### redis环境搭建

1、阿里云按量付费redis。经典网络

2、申请redis的公网连接地址

3、修改白名单  允许0.0.0.0/0 访问





### 单元测试

##### JUnit5的变化

JUnit5与之前版本的Junit框架有很大的不同。由三个不同子项目的几个不同模块组成。**JUnit 5 = JUnit Platform + JUnit Jupiter + JUnit Vintage**

- **JUnit Platform**: 是在jvm上启动测试框架的基础,不仅支持junit自制的测试引擎,其它测试引擎也都可以接入.

- **JUnit Juptiter**: 提供Junit5的新的编程模型,是Junit5新特性的核心,内部包含了一个测试引擎,用于在Junit Platform上运行.

- **Junit Vintage:** 提供兼容Junit4.x, 3.x老版本的测试引擎.

SpringBoot 2.4+版本默认移除了对Vintage的依赖,如果需要兼容,则需要手动添加依赖

```xml
<dependency>
    <groupId>org.junit.vintage</groupId>
    <artifactId>junit-vintage-engine</artifactId>
    <scope>test</scope>
    <exclusions>
        <exclusion>
            <groupId>org.hamcrest</groupId>
            <artifactId>hamcrest-core</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

##### junit5的集成

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-test</artifactId>
  <scope>test</scope>
</dependency>
```

##### JUnit5常用注解

- @Test : 表示为测试方法.与JUnit4中的Test不一样的是,它不能声明任何属性,拓展的测试将会由Jupiter提供额外测试.
- @parameterizedTest: 参数化测试
- @RepeatedTest: 表示方法可重复执行
- @DisplayName: 为测试类或者测试方法设置别名
- BeforeEach: 表示在每个单元测试之前执行
- AfterEach: 表示在每个单元测试之后执行
- BeforeAll: 表示在所有单元测试之前执行
- AfterAll : 表示在所有单元测试之后执行
- @Tag: 表示单元测试中的类别,类似于Junit4中的@Categories
- @Disabled: 表示测试类和测试方法不执行, 类似于Junit4中的ignore
- @Timeout: 表示测试方法运行超过了指定时间会返回错误
- @ExtendWith: 为测试类或者方法提供扩展类引用.

##### 断言

用于对测试中需要满足的条件进行验证.

- 简单断言: 用于对单个值进行简单的验证;

| 方法            | 说明                                 |
| --------------- | ------------------------------------ |
| assertEquals    | 判断两个对象或两个原始类型是否相等   |
| assertNotEquals | 判断两个对象或两个原始类型是否不相等 |
| assertSame      | 判断两个对象引用是否指向同一个对象   |
| assertNotSame   | 判断两个对象引用是否指向不同的对象   |
| assertTrue      | 判断给定的布尔值是否为 true          |
| assertFalse     | 判断给定的布尔值是否为 false         |
| assertNull      | 判断给定的对象引用是否为 null        |
| assertNotNull   | 判断给定的对象引用是否不为 null      |

- 复杂断言: 用于判断2个对象或者原始类型的数组是否合理.

- 组合断言: assertAll 方法接受多个 org.junit.jupiter.api.Executable 函数式接口的实例作为要验证的断言，可以通过 lambda 表达式很容易的提供这些断言

```java
@Test
@DisplayName("assert all")
public void all() {
 assertAll("Math",
    () -> assertEquals(2, 1 + 1),
    () -> assertTrue(1 > 0)
 );
}
```

- 异常断言: 抛出异常

```java
@Test
@DisplayName("异常测试")
public void exceptionTest() {
    ArithmeticException exception = Assertions.assertThrows(
           //扔出断言异常
            ArithmeticException.class, () -> System.out.println(1 % 0));

}
```

- 超时断言

```java
@Test
@DisplayName("超时测试")
public void timeoutTest() {
    //如果测试方法时间超过1s将会异常
    Assertions.assertTimeout(Duration.ofMillis(1000), () -> Thread.sleep(500));
}
```

##### 前置条件

当条件为真时,执行后面的语句,否则不予执行..

```java
@DisplayName("前置条件")
public class AssumptionsTest {
 private final String environment = "DEV";
 
 @Test
 @DisplayName("simple")
 public void simpleAssume() {
    assumeTrue(Objects.equals(this.environment, "DEV"));
    assumeFalse(() -> Objects.equals(this.environment, "PROD"));
 }
 
    //ssumingThat 的参数是表示条件的布尔值和对应的 Executable 接口的实现对象。只有条件满足时，Executable 对象才会被执行；当条件不满足时，测试执行并不会终止。
 @Test
 @DisplayName("assume then do")
 public void assumeThenDo() {
    assumingThat(
       Objects.equals(this.environment, "DEV"),
       () -> System.out.println("In DEV")
    );
 }
}
```



##### 嵌套测试

通过 Java 中的内部类和@Nested 注解实现嵌套测试，从而可以更好的把相关的测试方法组织在一起。在内部类中可以使用@BeforeEach 和@AfterEach 注解，而且嵌套的层次没有限制。

```java
@DisplayName("A stack")
class TestingAStackDemo {

    Stack<Object> stack;

    @Test
    @DisplayName("is instantiated with new Stack()")
    void isInstantiatedWithNew() {
        new Stack<>();
    }

    @Nested
    @DisplayName("when new")
    class WhenNew {

        @BeforeEach
        void createNewStack() {
            stack = new Stack<>();
        }

        @Test
        @DisplayName("is empty")
        void isEmpty() {
            assertTrue(stack.isEmpty());
        }

        @Test
        @DisplayName("throws EmptyStackException when popped")
        void throwsExceptionWhenPopped() {
            assertThrows(EmptyStackException.class, stack::pop);
        }

        @Test
        @DisplayName("throws EmptyStackException when peeked")
        void throwsExceptionWhenPeeked() {
            assertThrows(EmptyStackException.class, stack::peek);
        }

        @Nested
        @DisplayName("after pushing an element")
        class AfterPushing {

            String anElement = "an element";

            @BeforeEach
            void pushAnElement() {
                stack.push(anElement);
            }

            @Test
            @DisplayName("it is no longer empty")
            void isNotEmpty() {
                assertFalse(stack.isEmpty());
            }

            @Test
            @DisplayName("returns the element when popped and is empty")
            void returnElementWhenPopped() {
                assertEquals(anElement, stack.pop());
                assertTrue(stack.isEmpty());
            }

            @Test
            @DisplayName("returns the element when peeked but remains not empty")
            void returnElementWhenPeeked() {
                assertEquals(anElement, stack.peek());
                assertFalse(stack.isEmpty());
            }
        }
    }
}
```

结论: 外部类不能访问内部类,而内部类可以访问外部类.

##### 参数化测试

**@ValueSource**: 为参数化测试指定入参来源，支持八大基础类以及String类型,Class类型

**@NullSource**: 表示为参数化测试提供一个null的入参

**@EnumSource**: 表示为参数化测试提供一个枚举入参

**@CsvFileSource**：表示读取指定CSV文件内容作为参数化测试入参

**@MethodSource**：表示读取指定方法的返回值作为参数化测试入参(注意方法返回需要是一个流)

可以支持外部的各类入参, 如:CSV,YML,JSON 文件甚至方法的返回值也可以作为入参。只需要去实现**ArgumentsProvider**接口，任何外部文件都可以作为它的入参。

```java
@ParameterizedTest
@ValueSource(strings = {"one", "two", "three"})
@DisplayName("参数化测试1")
public void parameterizedTest1(String string) {
    System.out.println(string);
    Assertions.assertTrue(StringUtils.isNotBlank(string));
}


@ParameterizedTest
@MethodSource("method")    //指定方法名
@DisplayName("方法来源参数")
public void testWithExplicitLocalMethodSource(String name) {
    System.out.println(name);
    Assertions.assertNotNull(name);
}

static Stream<String> method() {
    return Stream.of("apple", "banana");
}
```

##### 迁移指南

1. 导入Vintage兼容包
2. 修改相关注解,具体如下

```xml
<dependency>
    <groupId>org.junit.vintage</groupId>
    <artifactId>junit-vintage-engine</artifactId>
    <scope>test</scope>
    <exclusions>
        <exclusion>
            <groupId>org.hamcrest</groupId>
            <artifactId>hamcrest-core</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

在进行迁移的时候需要注意如下的变化：

- 注解在 org.junit.jupiter.api 包中，断言在 org.junit.jupiter.api.Assertions 类中，前置条件在 org.junit.jupiter.api.Assumptions 类中。
- 把@Before 和@After 替换成@BeforeEach 和@AfterEach。
- 把@BeforeClass 和@AfterClass 替换成@BeforeAll 和@AfterAll。
- 把@Ignore 替换成@Disabled。
- 把@Category 替换成@Tag。
- 把@RunWith、@Rule 和@ClassRule 替换成@ExtendWith

### 指标监控

未来每一个微服务在云上部署以后，我们都需要对其进行监控、追踪、审计、控制等。SpringBoot就抽取了Actuator场景，使得我们每个微服务快速引用即可获得生产级别的应用监控、审计等功能。

##### 集成

```xml
  <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
```

##### 使用

自定义配置属性

```yaml
management:
  endpoints:
    enabled-by-default: true #暴露所有端点信息
    web:
      exposure:
        include: '*'  #以web方式暴露,默认是通过jconsole
```



访问 http://localhost:8080/actuator/**

最常用的Endpoint

- **Health：监控状况**
- **Metrics：运行时指标**
- **Loggers：日志记录**



##### 管理Endpoints

```yaml
management:
  endpoints:
    enabled-by-default: false #设置默认关闭所有
  endpoint: #手动开启指定的Endpoint
    beans:
      enabled: true
    health:
      enabled: true
```

##### 定制 Endpoint

###### 定制Health

```java
import org.springframework.boot.actuate.health.Health;
import org.springframework.boot.actuate.health.HealthIndicator;
import org.springframework.stereotype.Component;

@Component
public class MyHealthIndicator implements HealthIndicator {

    @Override
    public Health health() {
        int errorCode = check(); // perform some specific health check
        if (errorCode != 0) {
            return Health.down().withDetail("Error Code", errorCode).build();
        }
        return Health.up().build();
    }

}

构建Health
Health build = Health.down()
                .withDetail("msg", "error service")
                .withDetail("code", "500")
                .withException(new RuntimeException())
                .build();
```

###### 显示模块详细信息

```yaml
management:
    health:
      enabled: true
      show-details: always #总是显示详细信息。可显示每个模块的状态信息
```

###### 定制info

```java
import java.util.Collections;

import org.springframework.boot.actuate.info.Info;
import org.springframework.boot.actuate.info.InfoContributor;
import org.springframework.stereotype.Component;

@Component
public class ExampleInfoContributor implements InfoContributor {

    @Override
    public void contribute(Info.Builder builder) {
        builder.withDetail("example",
                Collections.singletonMap("key", "value"));
    }

}
```

```yaml
info:
  appName: boot-admin
  version: 2.0.1
  mavenProjectName: @project.artifactId@  #使用@@可以获取maven的pom文件值
  mavenProjectVersion: @project.version@
```

###### 定制Metrics信息

```java
class MyService{
    Counter counter;
    public MyService(MeterRegistry meterRegistry){
         counter = meterRegistry.counter("myservice.method.running.counter");
    }

    public void hello() {
        counter.increment();
    }
}


//也可以使用下面的方式
@Bean
MeterBinder queueSize(Queue queue) {
    return (registry) -> Gauge.builder("queueSize", queue::size).register(registry);
}
```

###### 自定义Endpoint

```java
@Component
@Endpoint(id = "container")
public class DockerEndpoint {


    @ReadOperation
    public Map getDockerInfo(){
        return Collections.singletonMap("info","docker started...");
    }

    @WriteOperation
    private void restartDocker(){
        System.out.println("docker restarted....");
    }

}
```



##### 第三方可视化界面集成

[项目地址](https://github.com/codecentric/spring-boot-admin), 具体操作可参考官网说明.



### Profile

##### 配置文件方式

- 默认配置文件 application.properties/application.yaml, 任何时候都会加载
- 指定环境配置文件 application-{env}.yaml, 环境名字自定义
- 激活指定环境

```properties
spring.profiles.active=dev
```

- 默认配置与环境配置同时生效,如果都配置了同一配置项,则指定环境配置优先

- 命令行修改指定配置项,可修改任意值,优先级高于配置文件

```
java -jar xxx.jar --spring.profiles.active=prod  --person.name=haha
```

##### @Profile条件装配功能

```java
@ConfigurationProperties("person")//指定配置文件中配置项
@Profile("dev")//指定生效的环境
@component
public class ProductionConfiguration {

    // ...

}
```

##### 批量加载 profile分组

```properties
spring.profiles.group.production[0]=proddb
spring.profiles.group.production[1]=prodmq
spring.profiles.group.test[0]=test0

#使用：#激活production组
spring.profiles.active=production
```

##### 配置文件优先级

(1) classpath 根路径

(2) classpath 根路径下config目录

(3) jar包当前目录

(4) jar包当前目录的config目录

(5) /config子目录的直接子目录

此优先级与逐渐增加, 越后面的优先级越高, 指定环境优先，外部优先，后面的可以覆盖前面的同名配置项

### 自定义starter

#####  starter的启动原理

- stater-pom引入autoConfigurer包,
- autoconfigure包中配置使用 META-INF/spring.factories 中 EnableAutoConfiguration 的值，使得项目启动加载指定的自动配置类

- **编写自动配置类 xxxAutoConfiguration -> xxxxProperties**
  - **@Configuration**
  - **@Conditional**
  - **@EnableConfigurationProperties**
  - **@Bean**
  - ......

**引入starter** **--- xxxAutoConfiguration --- 容器中放入组件 ---- 绑定xxxProperties ----** **配置项**



##### 自定义步骤

- 新建一个starter工程,命名为xxx--spring-boot-starter
- 新建一个xxx--spring-boot-starter-autoconfigure 的module,并让starter依赖它
- 编写功能类, 绑定配置文件,并标注生效的条件.

```java
@Configuration(proxyBeanMethods = true)
//绑定配置文件
@EnableConfigurationProperties(PersonProperties.class)
public class MyConfig {
	@ConditionalOnBean(PersonService.class)
    @Bean//添加组件bean, 方法名=id=组件名, 返回类型=组件类型,返回值=容器中保存的实例
    public PersonService personService() {
        return new PersonService();
    }
}


@ConfigurationProperties("com.person")
@Data// lambok的set,get注解
public class PersoonProperties {
    private String name;
    private Integer age;
}

```



- 创建META-INF/spring.factories ,配置EnableAutoConfiguration 的值,即autoconfiguration的完整类路径.

- 发布到maven仓库.  执行 maven-->install命令



### SpringBoot原理解析

#### SpringBoot的启动分析

```java
//run方法执行, 先创建后运行
new SpringApplication(primarySources).run(args);
//对象初始化
public SpringApplication(ResourceLoader resourceLoader, Class<?>... primarySources) {
		this.resourceLoader = resourceLoader;
		Assert.notNull(primarySources, "PrimarySources must not be null");
		this.primarySources = new LinkedHashSet<>(Arrays.asList(primarySources));
		this.webApplicationType = WebApplicationType.deduceFromClasspath();
		this.bootstrapRegistryInitializers = getBootstrapRegistryInitializersFromSpringFactories();
		setInitializers((Collection) getSpringFactoriesInstances(ApplicationContextInitializer.class));
		setListeners((Collection) getSpringFactoriesInstances(ApplicationListener.class));
		this.mainApplicationClass = deduceMainApplicationClass();
	}
```

1. 创建SpringApplication对象
   - 初始化一些变量与信息
   - 判断当前应用的类型(响应式还是原生servlet,根据DispatcherHandler类型判断)
   - 初始启动引导器**bootstrapRegistryInitializers**, 去spring.factories文件中找 org.springframework.boot.Bootstrapper
   - 初始化 **initializers**, 去**spring.factories**查找
   - 初始化**ApplicationListener** ,去**spring.factories**查找
2. 运行SpringApplication

- ```java
  public ConfigurableApplicationContext run(String... args) {
  		StopWatch stopWatch = new StopWatch();
      //1. 记录应用的启动时间
  		stopWatch.start();
      //2.创建引导上下文(Context环境), 让之前获取的启动引导bootstrapRegistryInitializers执行initialize方法
  		DefaultBootstrapContext bootstrapContext = createBootstrapContext();
  		ConfigurableApplicationContext context = null;
      //3.让当前应用进入headless模式.无需显示器也能运行
  		configureHeadlessProperty();
      //4.去spring.factories找 SpringApplicationRunListener
  		SpringApplicationRunListeners listeners = getRunListeners(args);
      //5遍历 SpringApplicationRunListener 调用 starting 方法；告诉监听者项目正在启动
  		listeners.starting(bootstrapContext, this.mainApplicationClass);
  		try {
              //6. 保存命令行参数
  			ApplicationArguments applicationArguments = new DefaultApplicationArguments(args);
              //7 准备运行环境
              	//a.根据webApplicationType返回当前应用的运行环境, 默认StandardServletEnvironment
              	//b.配置环境信息, 读取所有配置源的属性值
              	//c. 绑定环境信息
              	//d.监听器调用 listener.environmentPrepared()；通知所有的监听器当前环境准备完成
  			ConfigurableEnvironment environment = prepareEnvironment(listeners, bootstrapContext, applicationArguments);
  			configureIgnoreBeanInfo(environment);
              //8. 打印banner
  			Banner printedBanner = printBanner(environment);
              //9 创建IOC容器
  			context = createApplicationContext();
  			context.setApplicationStartup(this.applicationStartup);
              //准备ApplicationContext IOC容器的基本信息
              	//a.保存环境信息
              	//b.IOC容器的后置处理流程。
              	//c.应用初始器applyInitializers
              	//遍历所有的 ApplicationContextInitializer 。调用 initialize.。来对ioc容器进行初始化扩展功能
              `	//遍历所有的 listener 调用 contextPrepared。EventPublishRunListenr；通知所有的监听器contextPrepared
                  //所有的监听器 调用 contextLoaded。通知所有的监听器 contextLoaded；
  			prepareContext(bootstrapContext, context, environment, listeners, applicationArguments, printedBanner);
              //刷新IOC容器。refreshContext,创建容器中的所有组件
  			refreshContext(context);
              //通知IOC容器刷新完成
  			afterRefresh(context, applicationArguments);
  			stopWatch.stop();
  			if (this.logStartupInfo) {
  				new StartupInfoLogger(this.mainApplicationClass).logStarted(getApplicationLog(), stopWatch);
  			}
              //所有监听 器 调用 listeners.started(context); 通知所有的监听器 started
  			listeners.started(context);
              //调用所有的runners,比如ApplicationRunner,CommandLineRunner然后合并
  			callRunners(context, applicationArguments);
  		}
  		catch (Throwable ex) {
              //调用所有 Listener 的 failed；通知所有的监听器 failed
  			handleRunFailure(context, ex, listeners);
  			throw new IllegalStateException(ex);
  		}
  
  		try {
              //执行run方法
  			listeners.running(context);
  		}
  		catch (Throwable ex) {
  			handleRunFailure(context, ex, null);
  			throw new IllegalStateException(ex);
  		}
  		return context;
  	}
  ```

补充资料: 

-  [Headless模式](http://jimolonely.github.io/2019/03/26/java/039-java-headless/)
- [configureHeadlessProperty()方法](https://blog.csdn.net/zzb5682119/article/details/92796522)

#### 自定义监听器

- spring.factories 配置文件配置
  - ApplicationContextInitializer
  - ApplicationListener
  - SpringApplicationRunListener

- 使用@component放置容器中即可
  - ApplicationRunner 
  - CommandLineRunner

<img src="https://i.loli.net/2021/06/09/5USkdeLfapHmOKI.png" alt="image-20210609153803237"  />

![image-20210609153836237](https://i.loli.net/2021/06/09/ZJlcVBCYFWota5K.png)
