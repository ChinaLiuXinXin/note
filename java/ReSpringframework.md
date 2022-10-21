# spring

​	依赖

```xml
    <dependencies>
        <!-- Spring核心依赖 -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
            <version>4.3.7.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-beans</artifactId>
            <version>4.3.7.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>4.3.7.RELEASE</version>
        </dependency>

        <!-- Spring dao依赖 -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>4.3.7.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-tx</artifactId>
            <version>4.3.7.RELEASE</version>
        </dependency>

        <!-- Spring web依赖 -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-web</artifactId>
            <version>4.3.7.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>4.3.7.RELEASE</version>
        </dependency>

        <!-- Spring test依赖：方便做单元测试和集成测试 -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>4.3.7.RELEASE</version>
        </dependency>

    </dependencies>

```

​	**ioc容器**：实现了控制反转，将控制权转交给了用户，或者说使用者，而不会在一开始就定死了。

​	1，bean配置文件，可以有多个，通过import标签引入。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">
    <inport resource="service.xml"/>
    <bean name="user" class="club.yunote.pojo.User">
        <constructor-arg index="0" value="121"/>
        <constructor-arg index="1" value="123"/>
    </bean>

    <alias name="user" alias="user2"></alias>
</beans>
```

​	2，创建实例

```xml
ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
User user = context.getBean("user2", User.class);
System.out.println(user);
```

​	3，对象构造方式

​		无参构造：默认的构造方式

​		有参构造：通过bean的子标签标注，其中index表示构造函数中参数的下表

```xml
<bean name="user" class="club.yunote.pojo.User">
    <constructor-arg index="0" value="121"/>
    <constructor-arg index="1" value="123"/>
</bean>
```

​	4，依赖注入：对象信息注入的方式，创建对象，注入对象，全部交给SpringIOC容器吧，只需要在配置文件中书写对应的配置即可。

而注入依赖主要基于两种方式，一是通过setting（property），二是构造函数（constructor-arg）

```xml
<bean name="user" class="club.yunote.pojo.User">
    <constructor-arg index="0" value="小明"/>
    <property name="teacher" ref="teacher"></property>
</bean>

<bean name="teacher" class="club.yunote.pojo.Teacher">
    <constructor-arg index="0" value="晓晓"></constructor-arg>
    <constructor-arg index="1" value="1"></constructor-arg>
</bean>
```

​	4,1：构造函数注入，可以通过 设置索引注入，也可以在为参数指定类型后注入。而如果构造函数参数类型是一个bean对象，则无需声明类型，因为它相对于spring是已知的。

```xml
<bean name="user" class="club.yunote.pojo.User">
    <constructor-arg type="java.lang.String" value="小明"/>
    <constructor-arg type="int" value="2"/>
//一个对象实例化时只能有一个构造函数参与，下方例子表示在对象以及被配置到ioc容器中以后，无需声明其类型，直接使用ref属性引入即可。
    <constructor-arg ref="teacher"/>
</bean>
```

​	4,2：通过setter方法注入

```xm
<bean name="user" class="club.yunote.pojo.User" >
    <property name="id" value="123"/>
    <property name="name" value="小孩子"/>
    <property name="teacher" ref="teacher"/>
</bean>
```

​	5：p命名 空间与c命名空间

​	可以看到，无论是构造函数注入，还是setter方法注入，两者都过于繁琐，存在可以优化的空间。因此spring提供了两者的简写形式，但需要引入两个约束文件。

```xml
xmlns:p="http://www.springframework.org/schema/p"
xmlns:c="http://www.springframework.org/schema/c"
```

​	6，自动装配（autowire）

​	首先要说明，无论是byName or byType，这都是居于对象中的属性或者setter方法中的名字或类型，而与通过哪种方式匹配bean无关。自动装配主要有两种方式，一是通过字段名（byName），二是通过字段类型（byType），先看例子

```xml
//byName的意思确实是通过属性名来设置字段，但这一切都是基于规范的javabean写法，也就是setxxx方法名编写必须规范，因为spring实际上是通过setter方法对属性值赋值的，如果set方法不规范，是不会赋值成功的。
<bean id="bean1" class="com.jun.pojo.Bean1" autowire="byName" ></bean>
//byType是通过属性类型来匹配上下文中的bean，而属性类型spring可以通过反射来获取到，因此无关属性名称。
<bean id="bean1" class="com.jun.pojo.Bean1" autowire="byType" ></bean>
```

​	注解实现自动装配：@Autowire，@Resource，@Qualifier，以上三种都可以实现。

​	注意！**自动装配接口的时候，装配的的是其实现类，因此需要通过@Primary注明哪个子类**

​	但要记得先开启注解支持哦

```xml
<context:component-scan base-package="扫描的包路径"/>
```

​	开启注解支持后，很多东西都不需要自行在xml文件中配置了。

​	使用@Compoment可以将类注册到IOC容器中

![26b51f6c340908068635c4e0230f8e3](C:\Users\LIUXIN~1\AppData\Local\Temp\WeChat Files\26b51f6c340908068635c4e0230f8e3.png)

7，scoped（bean作用域）

​	实际上是在讲设计模式，常用的两种为单例模原型模式，有时间好好看看不。



8，免xml配置文件通过Java类配置ioc容器

​	先看例子

```java
@Configuration
public class BeanConfig {
    @Bean
    public User getUser(){
        return new User();
    }

    @Bean
    public Teacher getTeacher(){
        return new Teacher();
    }
}

```

​	如例子中所示，通过Java类配置bean的方式，按照如上语法，在方法上配置@Bean，中方法名是配置后bean的名字。

​	也可以通过自动扫描配置，这样就不要配置方法了，在类上标注@ComponentScan("扫描路径")即可。bean的名字默认是类名首字母小写，如果你的类名不是驼峰写法，则可能有所不同，尽量遵循规则。

​	**AOP动态代理**

​	自定义切面

​	execution表达式可以学习一下

```xml
任意公共方法的执行：
execution(public * *(..))
任何一个以“set”开始的方法的执行：
execution(* set*(..))
AccountService 接口的任意方法的执行：
execution(* com.xyz.service.AccountService.*(..))
定义在service包里的任意方法的执行：
execution(* com.xyz.service.*.*(..))
定义在service包和所有子包里的任意类的任意方法的执行：
execution(* com.xyz.service..*.*(..))
定义在pointcutexp包和所有子包里的JoinPointObjP2类的任意方法的执行：
execution(* com.test.spring.aop.pointcutexp..JoinPointObjP2.*(..))")
***> 最靠近(..)的为方法名,靠近.*(..))的为类名或者接口名,如上例的JoinPointObjP2.*(..))

pointcutexp包里的任意类.
within(com.test.spring.aop.pointcutexp.*)
pointcutexp包和所有子包里的任意类.
within(com.test.spring.aop.pointcutexp..*)
实现了MyInterface接口的所有类,如果MyInterface不是接口,限定MyInterface单个类.
this(com.test.spring.aop.pointcutexp.MyInterface)
***> 当一个实现了接口的类被AOP的时候,用getBean方法必须cast为接口类型,不能为该类的类型.

带有@MyTypeAnnotation标注的所有类的任意方法.
@within(com.elong.annotation.MyTypeAnnotation)
@target(com.elong.annotation.MyTypeAnnotation)
带有@MyTypeAnnotation标注的任意方法.
@annotation(com.elong.annotation.MyTypeAnnotation)
***> @within和@target针对类的注解,@annotation是针对方法的注解
```



```xml
<bean id="diy" class="demo4.log.diy.Diy"></bean>
<aop:config>
    <aop:aspect ref="diy">
        <aop:pointcut id="pointcut" expression="execution(* demo3.service.UserimplService.*(..))"/>
        <aop:before method="before" pointcut-ref="pointcut"/>
        <aop:after method="after" pointcut-ref="pointcut"/>
        <aop:around method="round" pointcut-ref="pointcut"/>
    </aop:aspect>
</aop:config>
```



​	继承spring接口

```xml
//这里仅仅展示了两个接口，详情请见包：org.springframework.aop
<bean id="log" class="demo3.log.Log"></bean>
<bean id="afterlog" class="demo3.log.AfterLog"></bean>
<!--    配置aop-->
<aop:config>
    <aop:pointcut id="pointcut" expression="execution(* demo3.service.UserimplService.*(..))"/>
    <aop:advisor advice-ref="log" pointcut-ref="pointcut"/>
    <aop:advisor advice-ref="afterlog" pointcut-ref="pointcut"/>
</aop:config>
```

​	**整合mybatis**

​	pom依赖

```xm
<dependency>
  <groupId>org.mybatis</groupId>
  <artifactId>mybatis-spring</artifactId>
  <version>2.0.7</version>
</dependency>
```

​	要完整地实现整个mybatis流程，需要数据源（datasource），SqlSession工厂（SqlSessionFactory)，以及Sqlsession。

​	只要将上述三种对象，配置到spring的IOC容器中即可。

```xml
<!--    使用spring的数据源替代mysql的数据源-->
    <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=true&amp;useUnicode=true&amp;characterEncoding=UTF8"/>
        <property name="username" value="root"/>
        <property name="password" value="hsp"/>
    </bean>

    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>
<!--        绑定mybatis配置文件-->
<!--        <property name="configLocation" value="classpath:mybatis-config.xml"/>-->
        <property name="mapperLocations" value="com/jun/dao/UserMapper.xml"/>
<!--        <property name="mapperLmocations" value="classpath:com/jun/dao/*.xml"/>-->
    </bean>

    <bean id="sqlsession" class="org.mybatis.spring.SqlSessionTemplate">
        <constructor-arg index="0" ref="sqlSessionFactory"/>
    </bean>

    <bean id="usermappertemplate" class="com.jun.dao.UserMapperTemplate">
<!--        只能使用构造器注入-->
        <property name="sqlsession" ref="sqlsession"/>
    </bean>

<!--    第二种方式-->
    <bean id="usermapper2" class="com.jun.dao.UserMapper1">
        <property name="sqlSessionFactory" ref="sqlSessionFactory"/>
    </bean>
```

​	事务部分好好找时间学一下，现在还学不明白。
