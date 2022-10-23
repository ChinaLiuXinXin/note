**分页插件**

```xm
//依赖
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper</artifactId>
    <version>5.3.2</version>
</dependency>
//安装插件，在mybatis配置文件中
<plugins>
    <!-- com.github.pagehelper为PageHelper类所在包名 -->
    <plugin interceptor="com.github.pagehelper.PageInterceptor">
    	<!-- 使用下面的方式配置参数，后面会有所有的参数介绍 -->
    	<property name="param1" value="value1"/>
    </plugin>
</plugins>

//在springboot中使用
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper-spring-boot-starter</artifactId>
    <version>{{最新版本}}</version>
</dependency>
//引入自动生效
详细配置查看官网
```

​	使用

```java
//调用PageHelper的静态方法，设置第几页（index),每页数据量最大值（num），且该条记录仅在下一次查询生效。
PageHelper.startPage(index,num);
SqlSession session = MybatisTool.getSession();
BookMapper mapper = session.getMapper(BookMapper.class);
List<Book> book = mapper.getBook();
for (Book book1 : book) {
    System.out.println(book1);
}
```

