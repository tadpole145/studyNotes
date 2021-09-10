## MySQL



#### 连接配置

```properties
#mysql数据库连接-------驱动依赖为8+版本
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/数据库名?serverTimezone=GMT%2B8&characterEncoding=utf-8
spring.datasource.username=root
spring.datasource.password=密码

# mysql驱动的依赖的版本为5+版本
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/数据库名?characterEncoding=utf-8&useSSL=true
spring.datasource.username=root
spring.datasource.password=密码
```

