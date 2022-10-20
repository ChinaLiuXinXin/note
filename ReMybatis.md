# mybatis

​	类路径：目前理解，打包后的classes下的文件

​	参考文章：https://segmentfault.com/a/1190000015802324



#### **构建SqlSessionFactory**	

​	1，通过mybatis提供的工具类，读取配置文档	

```java
String resource = "mybatis-config.xml";
try {
    InputStream inputStream = Resources.getResourceAsStream(resource);
    sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
} catch (IOException e) {
    e.printStackTrace();
}
```

​	2，通过SqlSessionFactory创建SQL session实例

​	3，使用后记得关闭



#### mybatis配置文件

​	properties：配置数据库环境,如下图例子所示，可以通过resource可以从外部引入properties文件，通过子标签properties也可以配置数据库属性，还可以通过创建Java对象时传递。

```xml
<properties resource="org/mybatis/example/config.properties">
  <property name="username" value="dev_user"/>
  <property name="password" value="F2Fa3!33TYyg"/>
</properties>
```

​	database.properties

```properties
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/mybatis?useSSL=true&amp;useUnicode=true&amp;characterEncoding=UTF-
username=root
password=hsp
```

​	settings

​		需要重点了解的几个东西，驼峰映射，缓存开启等，需要找文档看，不需要背。

​	typeAliases

​		经常需要书写的类如果一直使用全类名会很麻烦，代码也很冗余，可以通过type Aliases将javabean的类名首字母小写作为其别名，在配置的时候可以简化类名。

​	environments

​		单词结尾带s说明其内部可以配置多种环境，其属性default表示默认使用哪一个环境配置。

```xml
<environments default="development">
  <environment id="development">
    <transactionManager type="JDBC">
      <property name="..." value="..."/>
    </transactionManager>
    <dataSource type="POOLED">
      <property name="driver" value="${driver}"/>
      <property name="url" value="${url}"/>
      <property name="username" value="${username}"/>
      <property name="password" value="${password}"/>
    </dataSource>
  </environment>
</environments>
```

​	映射器（mappers）：配置好数据库后，需要告知mybatis到哪儿去找sql语句。这里主要了解三种

​	1，相对于类路径的资源引用

```xml
<mappers>
  <mapper resource="org/mybatis/builder/AuthorMapper.xml"/>
  <mapper resource="org/mybatis/builder/BlogMapper.xml"/>
  <mapper resource="org/mybatis/builder/PostMapper.xml"/>
</mappers>
```

​	2，根据mapper接口与mapper.xml的一一对应关系（名字取一样）。

```xml
<mappers>
  <mapper class="org.mybatis.builder.AuthorMapper"/>
  <mapper class="org.mybatis.builder.BlogMapper"/>
  <mapper class="org.mybatis.builder.PostMapper"/>
</mappers>
```

​	3，扫描包，mapper.xml文件需要和mapper接口一致

```xml
<mappers>
  <package name="org.mybatis.builder"/>
</mappers>
```

#### xml映射器

​	xml映射器主要用来书写增删改查，官方文档写得很清楚，看个例子就明白了。

`````xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.kuang.dao.UserMapper">

    <select id="getUser" parameterType="int" resultType="User">
    	select * from username where id = #{} and name = ${}
    </select>
    
    <select id="getTeacher" parameterType="int" resultMap="studentMap"></select>

    <resultMap id="studentMap" type="user"></resultMap>

    <update id="updateUser" parameterType="int"></update>

    <delete id="deleteUser" parameterType="int"></delete>
    
</mapper>
`````

​	在该例子中，可以看到，在mapper标签中namespace表示该mapper绑定到哪一个接口，在mapper标签里又有三种标签，通过它们可以实现基本的增删改查，其中有一些共通的东西。

​	id：mapper中的唯一标注名，与mapper接口中的方法名保持一致。

​	resultMap：结果映射

​	resultType：本质上依旧是resultMap，结果映射，但mybatis有优化 机制，不需要自行配置。

​	parameterType：参数的Java类型，参照java类型别名或自定义。

​	#{}与${}：不太懂，尽量使用#{}更安全。

​	#{}会将值包裹在一个引号中传入sql，因此值只会识别为一个值。而${}则不会，它会将值原封不动传入其中，容易出现sql注入风险

​	**结果映射**：在数据库查询结果与javabean之间映射。

```xml
<resultMap id="studentMap" type="user">
    <id column="" property=""></id>
    <result column="" property=""></result>
</resultMap>
```

​	resultMap内部的标签，id为标注主键？，result配置主键与javabean中的属性或字段映射关系，重点了解一下，collection用于标注官网太复杂了，别看了，，，，，

​	1，集合嵌套查询，多表查询时会用到。

​	2，驼峰映射：数据库中并不存在驼峰写法,因此需要在数据库列名与java字段的驼峰写法之间实现自动映射的话可以在setting中开启驼峰映射

```xml
<settings>
    <!-- 驼峰映射 -->
    <setting name="mapUnderscoreToCamelCase" value="true"></setting>
</settings>
```

​	3，多对一查询：通过association映射主键

```xml
<resultMap id="setTeacher" type="student">
    <result property="id" column="id"></result>
    <result property="name" column="sname"></result>
    <association property="teacher" javaType="teacher">
        <result property="name" column="tname"></result>
    </association>
</resultMap>
```

​	4，一对多查询：通过collection映射集合

```xml
<select id="getTeacher" resultMap="TeacherMap" parameterType="int">
    select * from teacher where id = #{id}
</select>
<resultMap id="TeacherMap" type="Teacher">
    <collection property="students" ofType="student">
        <result property="name" column="name"></result>
        <result property="id" column="id"></result>
    </collection>
</resultMap>
```

​	缓存：了解即可，后续使用redis实现分布式缓存

​	1，一级缓存：默认是开启的，就是一次SqlSession查询

​	2，二级缓存：开启后，一级缓存结束时数据会进入二级缓存，二级缓存的作用域是对应mapper.xml文件中所有查询语句。

```xml
<cache
  eviction="FIFO"
  flushInterval="60000"
  size="512"
  readOnly="true"/>
```

​	**动态Sql**

​	直接看官网比较好，重点记住where，where在某些特殊的情况下能够发挥奇用。

