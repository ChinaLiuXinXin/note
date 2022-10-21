# javaweb

#### Servlet：web三大组件之一，是javaEE的规范

​	作用：接受用户的请求，并相应数据给客户端

HttpServlet: 继承并实现了servlet接口的一部分功能

```java
ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        SelectUsersTodo selectuserstodo = context.getBean("selectuserstodo", SelectUsersTodo.class);
//        将java对象转换为json类型
        Gson gson = context.getBean("gson", Gson.class);
        List<Note> notes = selectuserstodo.getNotes();
        String s = gson.toJson(notes);
//        设置客户端参数,解决乱码问题
        resp.setContentType("text/html; charset=UTF-8");
        PrintWriter writer = resp.getWriter();
        writer.println(s);
```



Servletconfig：是一个配置类，通过该配置可以获取到Servlet初始化的信息，可以在项目启动前配置信息到web.xml中对应的servlet中。	

ServletContext：项目的上下文对象，服务器中仅有一个ServletContext对象。

1，像map一样存取数据

```java
ServletContext servletContext = servletConfig.getServletContext();
User user = new User(1, "牛！");
servletContext.setAttribute("value",user);
```

2，获取全局配置信息，在web.xml中配置的

```xml
<context-param>
        <param-name>context</param-name>
        <param-value>123455</param-value>
</context-param>
```

3，获取web服务器的资源目录

```jav
ServletContext servletContext = this.getServletContext();
String realPath = servletContext.getRealPath("/index.jsp");
resp.getWriter().println(realPath);
```

3.2，读取web项目下指定资源的内容

```java
ServletContext servletContext = this.getServletContext();
InputStream resourceAsStream = servletContext.getResourceAsStream("/hello.txt");
InputStreamReader inputStreamReader = new InputStreamReader(resourceAsStream);
int num = 0;
while ((num=inputStreamReader.read())!= -1){
    System.out.println(num);
}
```

3.3，实现servlet的转发

```jav
RequestDispatcher requestDispatcher = servletContext.getRequestDispatcher("/hello");
requestDispatcher.forward(req,resp);
```

​	**需要注意，这里实现的转发，并不会更新客户端的url。**

3.4，servlet重定向

​	1，设置请求头

```java
resp.getWriter().println("old address");
resp.setStatus(302);
resp.setHeader("Location","http://yunote.club");
```

​	2，通过sendRedirect来设置

```java
resp.sendRedirect("http://yunote.club");
```



#### cookie

​	1，设置cookie

```java
Cookie name1 = new Cookie("name",user.name);
```

​	2，获取cookie

```java
Cookie[] cookies = req.getCookies();
```

​	3，设置cookie的路径

```java
cookie.setPath(this.getServletContext().getContextPath() + "/login");
```

需要注意这里的ContextPath并不是指的当前用户所访问的路径，而是上下文对象的路径，如图所示的	application context。

![			](C:\Users\LiuXinXin\AppData\Roaming\Typora\typora-user-images\image-20220919200256638.png)

​	

#### Session

​	作用：对用户的访问进行缓存。

​	执行流程：在用户访问时，如果程序里有req.getSession这行代码，首先会判断用户传上来的cookie中有没有JSESSIONID。如果有，则会生成一个 名为JSESSIONID 的cookie，在用户访问时自动读取，如果没有则会生成一个新的，发送到客户端。

​	同时，也可以通过以下方法判断该Session是新创建出来的还是旧的。

```xml
HttpSession session = req.getSession();
session.isNew();
```

​	Session域数据	

​	servletContext域数据只要不修改不删除就是持久的，而Session中的域数据则表示的是在一段时间内，准确说是在Session生命周期以内。

​	session生存时间：默认30min，可以自行在web.xml中配置超时时长，设置完成后，所有的session超时时长会统一为web.xml中配置的时间。

```xml
<session-config>
	<session-timeout>30</session-timeout>
</session-config>
```

​	自行设置生存时间

```xml
HttpSession session = req.getSession();
//i的单位为秒
session.setMaxInactiveInterval(int i);
```

#### Filter过滤器

​	如何配置：

```xml
<!--    配置拦截器-->
<!--    filter-class表示继承Filter类的拦截对象-->
    <filter>
        <filter-name>test</filter-name>
        <filter-class>com.jun.filter.TestFilter</filter-class>
    </filter>
<!--    url-pattern表示要拦截的路径-->
    <filter-mapping>
        <filter-name>test</filter-name>
        <url-pattern>/getname</url-pattern>
    </filter-mapping>
```

​	很牛x的过滤器

```java
package com.kuang.filter;

import javax.servlet.*;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletRequestWrapper;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.UnsupportedEncodingException;
import java.util.Map;

/**
* 解决get和post请求 全部乱码的过滤器
*/
public class GenericEncodingFilter implements Filter {

   @Override
   public void destroy() {
  }

   @Override
   public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
       //处理response的字符编码
       HttpServletResponse myResponse=(HttpServletResponse) response;
       myResponse.setContentType("text/html;charset=UTF-8");

       // 转型为与协议相关对象
       HttpServletRequest httpServletRequest = (HttpServletRequest) request;
       // 对request包装增强
       HttpServletRequest myrequest = new MyRequest(httpServletRequest);
       chain.doFilter(myrequest, response);
  }

   @Override
   public void init(FilterConfig filterConfig) throws ServletException {
  }

}

//自定义request对象，HttpServletRequest的包装类
class MyRequest extends HttpServletRequestWrapper {

   private HttpServletRequest request;
   //是否编码的标记
   private boolean hasEncode;
   //定义一个可以传入HttpServletRequest对象的构造函数，以便对其进行装饰
   public MyRequest(HttpServletRequest request) {
       super(request);// super必须写
       this.request = request;
  }

   // 对需要增强方法 进行覆盖
   @Override
   public Map getParameterMap() {
       // 先获得请求方式
       String method = request.getMethod();
       if (method.equalsIgnoreCase("post")) {
           // post请求
           try {
               // 处理post乱码
               request.setCharacterEncoding("utf-8");
               return request.getParameterMap();
          } catch (UnsupportedEncodingException e) {
               e.printStackTrace();
          }
      } else if (method.equalsIgnoreCase("get")) {
           // get请求
           Map<String, String[]> parameterMap = request.getParameterMap();
           if (!hasEncode) { // 确保get手动编码逻辑只运行一次
               for (String parameterName : parameterMap.keySet()) {
                   String[] values = parameterMap.get(parameterName);
                   if (values != null) {
                       for (int i = 0; i < values.length; i++) {
                           try {
                               // 处理get乱码
                               values[i] = new String(values[i]
                                      .getBytes("ISO-8859-1"), "utf-8");
                          } catch (UnsupportedEncodingException e) {
                               e.printStackTrace();
                          }
                      }
                  }
              }
               hasEncode = true;
          }
           return parameterMap;
      }
       return super.getParameterMap();
  }

   //取一个值
   @Override
   public String getParameter(String name) {
       Map<String, String[]> parameterMap = getParameterMap();
       String[] values = parameterMap.get(name);
       if (values == null) {
           return null;
      }
       return values[0]; // 取回参数的第一个值
  }

   //取所有值
   @Override
   public String[] getParameterValues(String name) {
       Map<String, String[]> parameterMap = getParameterMap();
       String[] values = parameterMap.get(name);
       return values;
  }
}

```



​	FilterConfig：

​	作用一：
