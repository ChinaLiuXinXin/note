# Gson

使用场景：解析客户端发来的json数据

```java
Gson gson = new Gson();
//将json数据流reader传入到fromjson方法中，并传入相应的bean对象，Gson会将json数据转换为相应的JavaBean。
User user1 = gson.fromJson(reader, User.class);
```

