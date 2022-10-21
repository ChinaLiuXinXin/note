# SpringBoot

​	**Spring原理**

​	基础注解

​	@Configuration：通过java来配置Bean，SpirngBoot2中新增了一个属性：proxyBeanMethods = false表示关闭代理检查，此时外界通过java配置类调用其内部的方法时，不会检查bean是否已经存在实例，而是会返回一个新对象。

​	@import（{类.class，可以填入多个}）：引入bean到Java中,此时bean在ioc容器中的名字为此类的全路径。

​	@Conditional：条件判断注入

​	@importResource：将配置文件导入到ioc容器中。

​	@ConfigurationProperties(prefix="配置名")：通过配置文件注入依赖

​	@EnableConfigurationProperties（配置类.class）：引入被@ConfigurationProperties标识的类，且被标识类不需要@Compoment

​	**当配置不生效或者报错的时候，想想是不是标错了位置，一般就配置类与非配置类两种**