## redis命令

#### key

1、keys * 查看所有key

2、flushall（清空全部数据库）、flushdb（清空当前库）

3、del 删除key键

4、dbsize 查看数据库key的数量

5、查看指定的key

​	keys ?aa匹配aaa、baa.....

​	keys *aa匹配 aaaa bbbbaa ......



#### String

1、set key value 会覆盖

2、setnx key value 不会覆盖，存在返回0

3、strlen key 获取指定key所储存的字符串值的长度

4、append key '' 用于为指定的key追加值

5、get key

6、getdel key 先获取到指定的key后，再删除获取的那个key；最终返回被删除的值 **6.2.0版本可用**

7、getset key value 设置更新key值

8、incr key 数字加一

9、incrby key increment 增加指定长度

10、decr key 数字减一

11、decrby key decrement 减少指定长度



#### Hash

1、hset key field value [field value...] 存，会覆盖

2、hsetnx key field value 存，不会覆盖

3、hget key field 取

4、hmget key field [field...] 取多个

5、hdel key field [field] 删除一个或多个

6、hexists key field 判断指定field是否存在，存在返回1，不存在返回0

7、hgetall key 获取所有的field和value

![image-20231024113659948](C:\Users\小月亮\AppData\Roaming\Typora\typora-user-images\image-20231024113659948.png)

8、hkeys key（返回存储在key中哈希表的所有field）、hvals key（返回存储在key中哈希表的所有value）

![image-20231024114045409](C:\Users\小月亮\AppData\Roaming\Typora\typora-user-images\image-20231024114045409.png)

9、hincrby key field increment 正数加负数减

![image-20231024133906498](C:\Users\小月亮\AppData\Roaming\Typora\typora-user-images\image-20231024133906498.png)

10、hincrbyfloat key field increment 小数

![image-20231024134116487](C:\Users\小月亮\AppData\Roaming\Typora\typora-user-images\image-20231024134116487.png)



#### List类型

1、lpush key element [element...] 头插法

2、lpushx key element [element...] 在集合左边插入，key不存在无法插入

3、rpush key element [element...] 尾插法

4、rpushx key element [element...] 在集合右边插入，key不存在无法插入

5、lrange key start stop 查询集合元素，lrange key 0 -1（查全部）

4、lpop key左弹出、rpop key右弹出

5、llen key 获取集合元素个数

6、lindex key index（返回集合key里索引index位置存储的元素，0~n从左往右索引、-1~-n从右往左索引）

7、lset key index element（设置集合key中index位置的元素值为新的element，index为正数则从头到位索引，为负数从尾到头索引查询）



#### Set类型

1、sadd key member [member...]将一个或多个元素加入到集合中,返回添加成功的个数

2、srem key member [member...]删除指定元素

3、spop key [count] 随机删除一个或多个元素

4、srandmember key [count] 随机返回集合key中的一个或多个随机元素，若返回个数的count大于集合总数则返回全部

![image-20231024135944595](C:\Users\小月亮\AppData\Roaming\Typora\typora-user-images\image-20231024135944595.png)

5、scard key 返回集合中元素的数量

6、smember key 返回存储在key中的集合的所有的成员

7、sismember key member 判断member是否是key的成员，是返回1，不是返回0



#### redis模式

单机模式、主从模式、哨兵模式、集群模式

1、单机模式

​	优点：部署简单、成本低

​	缺点：不可靠，单击有宕机的风险

2、主从模式

​	主节点、从节点，**数据只能从主节点复制到从节点**

​	设置：slaveof 主节点IP端口

​	优点：主节点宕机，从节点可以顶上来

​				扩展主节点的读能力

​	缺点：主节点宕机，需要人工干预，修改应用的主节点地址，让从节点复制新的主节点

3、哨兵模式

​	在复制的基础上，哨兵实现了故障自动修复。

​	哨兵模式监控原理：

​		每个sentinel







