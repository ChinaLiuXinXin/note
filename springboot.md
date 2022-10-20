# springboot

#### 	yaml文件

​	作用：实现Springbean的依赖注入·

​	基本格式：

​		1：对象，map

```yaml
#对象、Map格式
k: 
    v1:
    v2:
or {name: tom, id: 12}
```

​		2：字符串，数字，布尔值，不需要加引号，直接写即可

加 ’‘ 时，其值中的特殊字符（\n，，，）不会被转义，”“ 时会转义。

```yaml
person:
  name: 小华
  address: 湖南
  idcard: 1212312
  cat:
    name: 小猫
    id: '00\n7'
    
```

​		3，数组

```yaml
  list: [1,2,3,4,5]
  	or
    - 1
    - 2
    - 3
    - 4
    
```

​		4，占位符

```yaml
  name: ${random.uuid}
  address: 湖南
  idcard: ${random.int} ·
```

​	properties文件解决乱码问题：已经生成的项目再修改是没用的，

![image-20220922205303504](C:\Users\LiuXinXin\AppData\Roaming\Typora\typora-user-images\image-20220922205303504.png)

​	程序的主启动类

```java
//@SpringBootApplication 来标注一个主程序类
//说明这是一个Spring Boot应用
@SpringBootApplication
public class SpringbootApplication {

   public static void main(String[] args) {
     //以为是启动了一个方法，没想到启动了一个服务
      SpringApplication.run(SpringbootApplication.class, args);
   }

}
```

#### 	yaml配置注入

​		1，创建application.yml文件

​		2，使用yml语法配置javabean

​		3，在bean中通过@ConfigurationProperties（prefix=  "yml中的对象名"）注入



#### 	两种注入方式的对比

![image-20220922210558196](C:\Users\LiuXinXin\AppData\Roaming\Typora\typora-user-images\image-20220922210558196.png)

#### 	JSR303数据校验

​	依赖

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-validation</artifactId>
        </dependency>
```

​	注解：@Validated 开启jsr303注解支持

例：

```java

@NotNull(message="名字不能为空")
private String userName;
@Max(value=120,message="年龄最大不能查过120")
private int age;
@Email(message="邮箱格式错误")
private String email;

空检查
@Null       验证对象是否为null
@NotNull    验证对象是否不为null, 无法查检长度为0的字符串
@NotBlank   检查约束字符串是不是Null还有被Trim的长度是否大于0,只对字符串,且会去掉前后空格.
@NotEmpty   检查约束元素是否为NULL或者是EMPTY.
    
Booelan检查
@AssertTrue     验证 Boolean 对象是否为 true  
@AssertFalse    验证 Boolean 对象是否为 false  
    
长度检查
@Size(min=, max=) 验证对象（Array,Collection,Map,String）长度是否在给定的范围之内  
@Length(min=, max=) string is between min and max included.

日期检查
@Past       验证 Date 和 Calendar 对象是否在当前时间之前  
@Future     验证 Date 和 Calendar 对象是否在当前时间之后  
@Pattern    验证 String 对象是否符合正则表达式的规则

.......等等
除此以外，我们还可以自定义一些数据校验规则
```

​	要使得以上数据在后端验证 时生效，还需要再Controlller层增加一个**注解@Valid**

```java
    public Result getUser(@Valid @RequestBody(required = false) User user,)
```



#### 	多环境切换

​	1，**properties**：

​		命名语法，**application-xxx.properties**

```properties
# 应用名称
spring.application.name=TestSpringBoot

# 应用服务 WEB 访问端口
server.port=8080

# 指定使用哪个profile文件
spring.profiles.active=test
```

​	2，**yaml**：

​		命名语法

​			1，**application-xxx.properties**,与properties类似。

​			2，单文件配置：

```yam
#指定使用哪个文件,或者
spring:
  profiles:
    active: test
---
#配置项一
server:
  port: 8083
spring:
  profiles: test
---
#配置项二
server:
  port: 8089
spring:
  profiles: dev
```

​	3，根据文件类型不同以及yaml中选择的配置方式，有如下优先级

```properties
优先级1：项目路径下的config文件夹配置文件
优先级2：项目路径下配置文件
优先级3：资源路径下的config文件夹配置文件
优先级4：资源路径下配置文件
```

​	4，整合mybatis

​		4.1：pom依赖

```xm
<!--        整合mybatis-->
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>2.2.2</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
        </dependency>
<!--阿里的数据库连接池-->
        <!-- https://mvnrepository.com/artifact/com.alibaba/druid -->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>1.1.21</version>
        </dependency>
```

​		4.2：spring注入

```yaml
  datasource:
    username: root
    password: 123456
    #?serverTimezone=UTC解决时区的报错
    url: jdbc:mysql://localhost:3306/springboot?serverTimezone=UTC&useUnicode=true&characterEncoding=utf-8
    driver-class-name: com.mysql.cj.jdbc.Driver
    type: com.alibaba.druid.pool.DruidDataSource

    #Spring Boot 默认是不注入这些属性值的，需要自己绑定
    #druid 数据源专有配置
    initialSize: 5
    minIdle: 5
    maxActive: 20
    maxWait: 60000
    timeBetweenEvictionRunsMillis: 60000
    minEvictableIdleTimeMillis: 300000
    validationQuery: SELECT 1 FROM DUAL
    testWhileIdle: true
    testOnBorrow: false
    testOnReturn: false
    poolPreparedStatements: true

    #配置监控统计拦截的filters，stat:监控统计、log4j：日志记录、wall：防御sql注入
    #如果允许时报错  java.lang.ClassNotFoundException: org.apache.log4j.Priority
    #则导入 log4j 依赖即可，Maven 地址：https://mvnrepository.com/artifact/log4j/log4j
    filters: stat,wall,log4j
    maxPoolPreparedStatementPerConnectionSize: 20
    useGlobalDataSourceStat: true
    connectionProperties: druid.stat.mergeSql=true;druid.stat.slowSqlMillis=500
```

### 	拦截器（HandlerInterceptor）

​	实现步骤，继承HandlerInterceptor接口，实现三个方法

```java
public class LoginHandlerInterceptor implements HandlerInterceptor {
//    在请求进来之前做的一些业务逻辑
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        response.setContentType("text/html;charset=UTF-8");
        response.getWriter().println("没有权限访问");

        return true;
    }
//		在页面渲染完成之后调用的方法
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        HandlerInterceptor.super.afterCompletion(request, response, handler, ex);
    }
//     在请求完成之后做的一些事情
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        HandlerInterceptor.super.postHandle(request, response, handler, modelAndView);
    }
}
```

​	2，将该实现类注册到spring Mvc中

```java
@Configuration
public class WebConfigretion implements WebMvcConfigurer {
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        //addPathPatterns代表需要拦截哪些请求
        registry.addInterceptor(new LoginHandlerInterceptor()).addPathPatterns("/**")
            //excludePathPatterns代表排除哪些接口
            .excludePathPatterns("/","/user/text/todo");
    }
}
```

​	拦截器执行顺序

​	1，通过@Order注解指定

​	2，拦截器方法调用顺序

​		首先会依次调用所有拦截器的preHandle方法，执行完最后一个方法以后，会倒序执行postHandle方法，其中任何一个步骤出现错误。都会触发afterCompretion方法，顺序进来，倒叙出去。

![image-20221006213327235](C:\Users\LiuXinXin\AppData\Roaming\Typora\typora-user-images\image-20221006213327235.png)

​	Servlet中用/*匹配 所有路径，springMVC中用/**。

​	关于springboot中自定义servlet，springboot中自定义javaweb中的三大组件有两种方式，一是使用servlet原生注解，配合@ServletCompomentScan（）实现

```java
@ServletComponentScan
@SpringBootApplication
public class TestSpringBootApplication {
```



​	二是通过ServletRegistrationBean类，将Servlet对象存入其中，并将ServletRegistrationBean对象注入IOC容器中。

```java
    @Bean
    public ServletRegistrationBean getServlet(){
        TestServlet testServlet = new TestServlet();
        ServletRegistrationBean servletRegistrationBean = new ServletRegistrationBean(testServlet,"/hi");
        return servletRegistrationBean;
    }
```

### 数据库操作

​	**配置jdbc**

​	1：引入依赖，导入jdbc驱动是必须的，二数据库驱动则根据个人使用数据库情况来确定。

```xml
<!--        导入jdbc驱动-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jdbc</artifactId>
        </dependency>

        <!--        导入数据库驱动-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.49</version>
        </dependency>
```

​	还需要注意驱动的版本问题，MySQL有默认的版本，若不写版本号，则会默认注入springboot自动配置的版本，若需要指定，则可以加入版本号，或者在properties中指定版本。

```xml
    <properties>
        <java.version>1.8</java.version>
        <mysql.version>5.1.49</mysql.version>
    </properties>
```

