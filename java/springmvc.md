# springmvc

#### 	第一个坑：外部添加的依赖需要手动添加进tomcat容器中，否则就会报错，找不到依赖

![image-20220918162518346](C:\Users\LiuXinXin\AppData\Roaming\Typora\typora-user-images\image-20220918162518346.png)

大概长这样，考虑一些是不是iml文件有问题

![image-20220920211849847](C:\Users\LiuXinXin\AppData\Roaming\Typora\typora-user-images\image-20220920211849847.png)

**碰到请求无响应的问题，请检查上述两种情况。**或者配置文件的class有没有填错，多踩坑就行了

#### 	第一个springmvc程序

​	pom.xml依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>SpringMVC</artifactId>
        <groupId>com.blue</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>springmvc-01-serlvet</artifactId>

    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
    </properties>

    <dependencies>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>servlet-api</artifactId>
            <version>2.5</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet.jsp</groupId>
            <artifactId>jsp-api</artifactId>
            <version>2.2</version>
        </dependency>
    </dependencies>

</project>

```



​	**web.xml**配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <!--1.注册DispatcherServlet-->
    <servlet>
        <servlet-name>springmvc</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!--关联一个springmvc的配置文件:【servlet-name】-servlet.xml-->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc-servlet.xml</param-value>
        </init-param>
        <!--启动级别-1-->
        <load-on-startup>1</load-on-startup>
    </servlet>

    <!--/ 匹配所有的请求；（不包括.jsp）-->
    <!--/* 匹配所有的请求；（包括.jsp）-->
    <servlet-mapping>
        <servlet-name>springmvc</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

</web-app>
```

​	springxml文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">
 
<!--    添加处理器映射器-->
 
    <bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>
<!--    添加 处理器适配器-->
    <bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"/>
    <!--视图解析器:DispatcherServlet给他的ModelAndView-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="InternalResourceViewResolver">
        <!--前缀-->
        <property name="prefix" value="/WEB-INF/jsp/"/>
        <!--后缀-->
        <property name="suffix" value=".jsp"/>
    </bean>
 
    <!--Handler-->
    <bean id="/hello" class="com.chang.controller.HelloController"/>
 
</beans>
```



#### 实现SpringMVC

其中实现是springmvc实现的部分，虚线是需要我们自己实现的部分。

![img](https://img-blog.csdnimg.cn/e471b0e5f55249f198226bfeed3972fa.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBALUJsdWUu,size_20,color_FFFFFF,t_70,g_se,x_16)

​	DispatcherServlet：请求分发器，前端控制器。整个SpringMVC的核心，用户的所有请求都会经由他处理，用户视图也会经由他处理解析，现在就不需要配置繁琐的servlet映射，而是将其交由springMVC进行管理，开发者只需要配置handler去调用service层。

HandlerMapping：请求映射器，根据前端传过来的url查找指定的Handler。

HandlerExecution：handler执行器将解析后的Handler信息回传给DispatcherServlet。

ViewResolver：解析视图，如果不写解析哪个视图，那么SpringMVC会根据handler名去找对应的视图。

Controller：继承该类可以获得控制器功能，通过controller可以调用请求分发器，调用service层与数据库交互，将数据填入视图中。

#### 注解实现SpringMVC

```xml
 
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xmlns:context="http://www.springframework.org/schema/context"
      xmlns:mvc="http://www.springframework.org/schema/mvc"
      xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/mvc
       https://www.springframework.org/schema/mvc/spring-mvc.xsd">
 
   <!-- 自动扫描包，让指定包下的注解生效,由IOC容器统一管理 -->
   <context:component-scan base-package="com.chang.controller"/>
   <!-- 让Spring MVC不处理静态资源 -->
   <mvc:default-servlet-handler />
   <!--支持mvc注解驱动 -->
   <mvc:annotation-driven />
 
   <!-- 视图解析器 -->
   <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver"
         id="internalResourceViewResolver">
       <!-- 前缀 -->
       <property name="prefix" value="/WEB-INF/jsp/" />
       <!-- 后缀 -->
       <property name="suffix" value=".jsp" />
   </bean>
 
</beans>
```

​	**@Controller**：在**开启spring注解支持**以后，可以用此注解来标注控制器。

​	**@RequestMapping("/helloServlet")**：该注解可以标注类，可以标注方法，可以同时标注类和方法，但光标注类是没有意义的，通过浏览器访问时，根本得不到任何方法，标注类的作用是为URI加上一层路径，不加也无妨。

​	**@PostMapping**：以post方式请求的注解

​	**@GetMapping**：以get方式请求的注解

#### 	RestFul风格

​	结合@RequestMapping注解可以实现RestFul风格，RestFul风格就是说将一段路径截取出来，用于实现传递参数的功能。也就是路由中为什么会有params参数的原因。同时也可以定义请求方法，例：@RequestMapping(value = "/hello",method = {RequestMethod.POST})。其中methdo参数就是用于传递参数。

```java
    @GetMapping("/gorun/{a}/{b}")
    public String hello(@PathVariable int a, @PathVariable int b, HttpServletRequest req, HttpServletResponse resp) throws IOException {
        String servletPath = req.getServletPath();
        resp.getWriter().println(a+b);
        return null;
    }
```



​	小黄鸭调试法：向别人讲解代码的同时自己想到了解决方案。



#### **spring实现重定向与转发**

​	第一种方式

```java
    @Controller
public class ResultSpringMVC {
   @RequestMapping("/rsm/t1")
   public String test1(){
       //转发
       return "/index.jsp";
  }

   @RequestMapping("/rsm/t2")
   public String test2(){
       //转发二
       return "forward:/index.jsp";
  }

   @RequestMapping("/rsm/t3")
   public String test3(){
       //重定向
       return "redirect:/index.jsp";
  }
}
```

​	重定向

​	

#### 数据处理

​		接受前端传来的参数，直接填入方法的参数就行了

```java
    @RequestMapping("/getDate")
    public String getDate(String name, int id, Model model){
        System.out.println("name："+name);
        System.out.println("id:"+ id);
        return "hello";
    }
```

​		方法名不同，通过加注解解决

```java
    @RequestMapping("/getDate")
    public String getDate(@RequestParam("username") String name, int id, Model model){
        System.out.println("name："+name);
        System.out.println("id:"+ id);
        return "hello";
    }
```

​		通过表单提交数据时候，前后端数据一定要对得上。否则会报错

![image-20220921164103316](C:\Users\LiuXinXin\AppData\Roaming\Typora\typora-user-images\image-20220921164103316.png)

​		解决乱码问题

​		加过滤器,设置相应头即可

