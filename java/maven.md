# Maven

**maven创建项目**

​	1，创建Java项目

```xml
mvn archetype:generate
//接下来命令行中会提示输入坐标
<groupId>com.liu.maven</groupId> //
<artifactId>pro01-maven-java</artifactId>
<version>1.0-SNAPSHOT</version>
```

​	2，创建web项目

```xml
mvn archetype:generate -DarchetypeGroupId=org.apache.maven.archetypes -DarchetypeArtifactId=maven-archetype-webapp -DarchetypeVersion=1.4
依次输入坐标？
```

​	

**让web工程依赖java工程**

​	也就是引入jar包，因为引入的Java工程最终会变成jar包，存放到web工程的lib目录下，可以在打包后的压缩包中查看。

​	查看当前工程所依赖的jar包

```xml
mvn dependency:list
org.hamcrest:hamcrest-core:jar:1.3:compile
格式：”group : artifactId : 打包方式 : scope“
```

**依赖的传递**：如果依赖的项目依赖了其他项目，依赖能否传递取决于其scope范围，倘若是Test，Provided则不能够传递。

有时候需要排除某些jar包，防止其与其他版本冲突，可以通过exclusion组织依赖的传递

```xm
<exclusions>
    <!-- 在exclude标签中配置一个具体的排除 -->
    <exclusion>
    	<!-- 指定要排除的依赖的坐标（不需要写version） -->
        <groupId>commons-logging</groupId>
        <artifactId>commons-logging</artifactId>
    </exclusion>
</exclusions>
```



