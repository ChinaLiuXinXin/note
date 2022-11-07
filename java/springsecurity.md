## SpringSecurity

​	主要功能：完成对用户的认证（登录）与授权（不同用户对某些文件或功能拥有不同的权限）。

​	

​	**JWT**：前后端分离项目权限验证机制。

​		组成部分：

​			1，header：保存数据加密形式。

​			2，playload：保存用户信息。

​			3，signature：保存签名后的字段。

​		jwt种类：

​			1，nosecurejwt

​			2，JWS

​			3，JWE

​		加密方式：

​			1，对称加密

​			2，非对称加密

​		盐值：将用户密码与一段随机产生的密钥组合加密，组合方式任意，增加了随机性，可以加大破解难度。

​	**认证**：springSecurity默认的认证是不通过数据库查询的，若需要自定义数据源，需要实现springSecirity的一个Service接口。同时，用户密码是通过hash算法加密的。

```java
@Service("userDetailsService")
public class TestService implements UserDetailsService {
    @Autowired
    UserMapper userMapper;
    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        QueryWrapper queryWrapper = new QueryWrapper();
        queryWrapper.eq("name",username);
        Users users = userMapper.selectOne(queryWrapper);
        if(users == null){
            throw new UsernameNotFoundException("用户不存在");
        }
        //配置角色,在对接口设置权限后会用到。
        //BCryptPasswordEncoder：springSecirity中的一个一个加密工具。
        List<GrantedAuthority> auth =
                AuthorityUtils.commaSeparatedStringToAuthorityList("jaji");
        return new User(users.name,new BCryptPasswordEncoder().encode("123"),auth);
    }
}
```



​	**授权**：授权是指对不同的用户，实施不同的访问策略。也就是有些接口关用户登录是没有用的，还需要一定的权限才可以使用。

```java
//继承WebSecurityConfigurerAdapter后重写的一个方法,现已被标注废弃，可以通过配置组件的方式导入。
@Override
protected void configure(HttpSecurity http) throws Exception {
    http.formLogin()
        .loginPage("/login.html")//自定义登录页面
        .loginProcessingUrl("/api/login")//登录接口
        .defaultSuccessUrl("/api/hello").permitAll()//登录成功后跳转到该地址
        .failureForwardUrl("/test/over")//失败后跳转到的地址
        .and().authorizeRequests()
        .antMatchers("/test/index").hasAnyAuthority("jack")//配置访问对象。
        .anyRequest().authenticated()
        .and().csrf().disable();
}
```

​	**RBAC权限控制**：基于角色的权限控制,由五张表构成。

​		1，用户表：sys_user

​		2，角色表：sys_role

​		3，用户角色表：sys_user_role

​		4，权限表：sys_menu

​		5，角色权限表：sys_role_menu

