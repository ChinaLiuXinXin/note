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