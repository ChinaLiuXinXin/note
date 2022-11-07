**Swagger**

```xml
//依赖需要注意版本
<!--Swagger-UI API文档生产工具-->
2.7与spring2.2.0兼容
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.7.0</version>
</dependency>
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.7.0</version>
</dependency>
```

配置类：

```java
public class SwaggerConfig{
    @Bean
    public Docket docket() {
        // 创建一个 swagger 的 bean 实例,可以创建多个，对应到页面中会有多个分组
        return new Docket(DocumentationType.SWAGGER_2)
                // 配置基本信息
                .apiInfo(apiInfo())
                ;
    }

    // 基本信息设置
    private ApiInfo apiInfo() {
        Contact contact = new Contact(
                "xixixi", // 作者姓名
                "xixixi", // 作者网址
                "765418577@qq.com"); // 作者邮箱
        return new ApiInfoBuilder()
                .title("yunote-接口文档") // 标题
                .description("") // 描述
                .termsOfServiceUrl("https://www.baidu.com") // 跳转连接
                .version("1.0") // 版本
                .license("Swagger-的使用(详细教程)")
                .licenseUrl("https://blog.csdn.net/xhmico/article/details/125353535")
                .contact(contact)
                .build();
    }
}
```

