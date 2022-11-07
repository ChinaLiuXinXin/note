## redis

**基本数据类型**

![image-20221030151810045](C:\Users\LiuXinXin\AppData\Roaming\Typora\typora-user-images\image-20221030151810045.png)



**帮助指令**

​	1，help	name

**通用指令**

​	1，keys

​	2，del

​	4，expire

​	5，exists

​	5，ttl



**常用指令**
字符串操作：

​	1，set

​	2，get

​	3，mset	a 1 b 2 ,,,

​	4，mget	a b c ,,,

​	5，incr	a

​	6，incrby	a	num

​	7，incrbyfloat	float_num	num

​	8，setex	keys	index

​	9，setnx	keys	index	



层级数据：

​	表示形式：通过【：】设置key值值实现层级结构

​	![image-20221030174103305](C:\Users\LiuXinXin\AppData\Roaming\Typora\typora-user-images\image-20221030174103305.png)

​	

​	**hash类型**：传统的数据类型为了存储对象采用的是json的形式，在修改对象内的值时很不方便，在redis中存在hash类型的数据，可以对其中字段进行方便的读取与修改。

​	指令：读取与修改命令与string类型基本相同，多写，多用help，无法死记硬背。

​	**list类型**：list是列表数据类型，其中的数据是无序的，查询速度一般，插入和删除速度很快。

​	基本指令

​		1，lpush	key	element

​		2，lpop	key

​		3，rpush	key	element

​		4，rpop	key

​		5，lrange	key	start	end

​		6，blpop和brpop 会等待一定时间再执行。

​	**set**

​		1，sadd

​		2，sismenber

​		3，srem

​		4，sinter	sdiff	sunion	

​		5， sinterstore	sdiffstore	sunionstore

​		6，spop

​	**sortedset**：可排序，查询速度快，元素不重复

​		1，在set基础之上还需要并入一个参数score，其他就没什么了

​	**jbdis**

```xml
<!--		jedis驱动-->
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>4.3.1</version>
</dependency>
```

​	java配置信息

```java
Jedis jedis = new Jedis("8.134.32.226",6379);
jedis.auth("123456");
jedis.select(0);
jedis.set("a","12");

jedis.close();
```

​	连接池

```java
static {
    JedisPoolConfig jedisPoolConfig = new JedisPoolConfig();
    //设置最大连接数
    jedisPoolConfig.setMaxTotal(8);
    //设置最大空闲数量
    jedisPoolConfig.setMaxIdle(8);
    //设置最小空闲连接 
    jedisPoolConfig.setMinIdle(5);
    //
    //        jedisPoolConfig.setMaxWait();
    jedisPool = new JedisPool(jedisPoolConfig,"8.134.32.226",6379,1000,"123456");
}
//将对象放回连接池
public static void close(Jedis jedis){
    if(jedis != null){
        jedisPool.returnResource(jedis);
    }
}
```

​	spring整合redis

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
<!--		连接池依赖-->
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-pool2</artifactId>
</dependency>
```

```yam
//spring boot配置	
spring:
  redis:
    database: 0
    host: 8.134.32.226
    port: 6379
    password: 123456
    lettuce:
      pool:
        max-active: 8	//最大连接数
        max-idle: 8	//最大空闲数
        min-idle: 5	//最小空闲数量
        max-wait: 100	//等待时间
```

**序列化**：将对象的状态存储到文件中

**反序列化**：将文件形式的对象重新读取转化为原有形式

```java
@Test
public void TestSerializable() throws IOException {
    //        序列化
    FileOutputStream fileOutputStream = new FileOutputStream("D:\\test\\hello.txt");
    ObjectOutputStream objectOutputStream = new ObjectOutputStream(fileOutputStream);
    objectOutputStream.writeObject(new User("jack",100));
    objectOutputStream.flush();
    objectOutputStream.close();
}

@Test
public void TestRe() throws ClassNotFoundException, IOException {
    //        反序列化
    ObjectInputStream objectInputStream = new ObjectInputStream(new FileInputStream("D:\\test\\hello.txt"));
    User user = (User) objectInputStream.readObject();
    System.out.println(user.getName());
    System.out.println(user.getAge());
}
```

在redis中存在序列化的需求，例如当我们需要将对象存储到redis数据库中时，此时就存在序列化方式的问题。

​	1，手动配置序列化方式，就是为存入redis中的数据的值配置序列化方式，例如说key，value，哈希key，哈希value，，，

```java
    @Bean
    public RedisTemplate<String,Object> redisTemplate(RedisConnectionFactory redisConnectionFactory){
//
        RedisTemplate<String, Object> stringObjectRedisTemplate = new RedisTemplate<>();
//        连接工厂
        stringObjectRedisTemplate.setConnectionFactory(redisConnectionFactory);
//        序列化工具
        GenericJackson2JsonRedisSerializer jsonRedisSerializer = new GenericJackson2JsonRedisSerializer();
//        key的序列化
        stringObjectRedisTemplate.setKeySerializer(RedisSerializer.string());
        stringObjectRedisTemplate.setValueSerializer(jsonRedisSerializer);
        return stringObjectRedisTemplate;
    }
```

​	2，使用spring默认的配置方式，自行序列化，自行确定以什么方式存入redis中

```java
	@Test
	void getJack(){
//		手动序列化注入redis数据库
		ValueOperations<String, String> stringStringValueOperations = stringRedisTemplate.opsForValue();
		String s = stringStringValueOperations.get("user:jack");
		Gson gson = new Gson();
		User user = gson.fromJson(s, User.class);
		System.out.println(user.getName());
		System.out.println(user.getAge());
	}
```

