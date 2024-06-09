# redis对象

```
typedef struct redisObject{
	//对象类型
	unsigned type:4;
	//底层类型
	unsigned encoding:4;
	//指向底层实现数据结构的指针
	void *ptr;
	//引用计数
	int refcount;
	//最后一次被访问时间
	unsigned lru:22;
}robj;
```

## 1、字符串对象

### 操作

- **`‎SET 键值‎`‎ ‎**‎– 设置指定键的值。‎
- **`‎GET 键‎`‎ ‎**‎– 检索指定键的值。‎
- **`‎DEL 键‎`‎ ‎**‎– 删除给定键的值

### 1）int编码

### 2）raw编码

```java
//记录SDS长度
int len;
//记录buf数组未用字节的长度
int free;
//字节数组，用于保存字符串
char buf[];
```

### 3）embstr编码

embstr编码由redisObject和sds直接拼接起来,见下：

<img src="assets/embstr.jpg">

### -Spring调用方法

```java
stringRedisTemplate.opsForValue().对应方法
```

### 转化条件

int--插入字符--》raw

embstr--插入操作--》raw

## 2、列表对象L

### 操作

- **`‎LPUSH‎`**‎ – 将值推送到列表的左端。‎
- **`‎RPUSH‎`**‎ – 将值推送到列表的尾端。‎
- **`‎LRANGE‎`**‎ – 检索一系列项目。‎
- **`‎LPOP/RPOP‎`‎ ‎**‎– 用于显示和删除两端的项目。‎
- **`‎LINDEX‎`‎ ‎**‎– 从列表中的特定位置获取值。

### 1）压缩链表ziplist（由两级组成，顺序表的改版）

- 一级表

<img src="assets/ziplist.jpg">

- 二级节点（entry）

<img src="assets/entry.jpg">

previous_entry_length：前一节点长度

encoding：节点数据类型及长度

context：内容

### 2）列表linkedlist(可以认为就是双向链表)

### -Spring调用方法

```java
stringRedisTemplate.opsForList().对应方法
```

### 转化条件

ziplist--长度或数量超出上限--》linkedlist

## 3、哈希对象H

### 操作

- **`‎HSET‎`**‎ – 将值映射到哈希中的键。‎
- **`‎HGET‎`**‎ – 检索与哈希中的键关联的各个值。‎
- **`‎HGETALL‎`‎ ‎**‎– 显示整个哈希内容。‎
- **`‎HDEL‎`**‎ – 从哈希中删除现有的键值对

### 1）压缩链表ziplist

### 2）哈希表hashtable（使用链地址法）

### -Spring调用方法

```java
stringRedisTemplate.opsForHash().对应方法
```

### 转化条件

ziplist--长度或数量超出上限--》hashtable

## 4、集合对象S

### 操作

- **`‎SADD‎`**‎ – 向集合中添加一个或多个项目。‎
- **`SISMEMBER`**– 找出一个项目是否是集合的一部分。‎
- **`‎SMEMBERS‎`**‎ – 从集合中检索所有项目。‎
- **`‎SREM‎`**‎ – 从集合中删除现有项。‎

### 1）整数集合intset

```
typedef struct intset{
	//编码方式
	uint32_t encoding;//int16,int32,int64
	//长度
	uint32_t length;
	//保存元素的数组
	int8_t contents[];
}intset
```

### 2）哈希表hashtable

### -Spring调用方法

```java
stringRedisTemplate.opsForSet().对应方法
```

### 转化条件

intset--非整数插入或键或值的长度超出上限--》hashtable

## 5、有序集合对象Z

### 操作

- **`‎ZADD‎`‎ ‎**‎– 将具有分数的成员添加到排序集。‎
- **`‎ZRANGE‎`‎ ‎**‎– 根据项目在排序顺序中的位置检索项目。‎**`‎withscores‎`‎ ‎**‎选项生成实际分数值。‎
- **`‎ZRANGEBYSCORE ‎`**‎– 根据定义的分数范围从排序集中提取项目。‎**`‎withscores‎`‎ ‎**‎选项生成实际分数值。‎
- **`‎ZREM‎`‎ –‎**‎从已排序的集中删除项目。

### 1）ziplist

### 2）跳跃表skiplist+字典（二者关系是并列的关系）

- 跳跃表

跳跃表从有序链表中选取部分节点组成一个新的链表（1层），在从新的链表（1层）中选择部分节点再次组成新的链表（2层），依次类推形成多级索引

当进行查找，先从最高层开始找，满足查找条件时向右移动，否则向下移动

- 字典

```
typedef struct dict{
	//类型特定函数
	dict *type;
	//私有数据
	void *privdata;
	//哈希表
	dictnt ht[2];
	/*
		ht[0]用于日常时的正常使用
		ht[1]用于进行扩容，进行扩容时ht[0]的数据转移到ht[1]上，当完成扩容时交换ht[0]和ht[1]
	*/
}
```

### -Spring调用方法

```java
stringRedisTemplate.opsForZset().对应方法
```

### 转化条件

ziplist--长度或数量超出上限--》skiplist+字典

## 6、GEO

GEO就是Geolocation的简写形式，代表地理坐标

* GEOADD：添加一个地理空间信息，包含：经度（longitude）、纬度（latitude）、值（member）
* GEODIST：计算指定的两个点之间的距离并返回
* GEOHASH：将指定member的坐标转为hash字符串形式并返回
* GEOPOS：返回指定member的坐标
* GEORADIUS：指定圆心、半径，找到该圆内包含的所有member，并按照与圆心之间的距离排序后返回。6.以后已废弃
* GEOSEARCH：在指定范围内搜索member，并按照与指定点之间的距离排序后返回。范围可以是圆形或矩形。6.2.新功能
* GEOSEARCHSTORE：与GEOSEARCH功能一致，不过可以把结果存储到一个指定的key。 6.2.新功能

**Spring调用方法**

```java
stringRedisTemplate.opsForGeo().对应方法
```

## 7、BitMap

用0和1标示业务状态，这种思路就称为位图（BitMap）

* SETBIT：向指定位置（offset）存入一个0或1

* GETBIT ：获取指定位置（offset）的bit值

* BITCOUNT ：统计BitMap中值为1的bit位的数量

* BITFIELD ：操作（查询、修改、自增）BitMap中bit数组中的指定位置（offset）的值

* BITFIELD_RO ：获取BitMap中bit数组，并以十进制形式返回

* BITOP ：将多个BitMap的结果做位运算（与 、或、异或）

* BITPOS ：查找bit数组中指定范围内第一个0或1出现的位置

* **Spring调用方法**

  ```java
  stringRedisTemplate.opsForValue().对应方法
  ```

## 8、HyperLogLog

HyperLogLog主要用于统计数据。单个HLL的内存**永远小于16kb**，**内存占用低**的令人发指！作为代价，其测量结果是概率性的，**有小于0.81％的误差**。

**Spring调用方法**

```java
stringRedisTemplate.opsForHyperLogLog().对应方法
```

# RedisTemplate

```java
//Note：StringRedisTemplate == RedisTemplate<String, String>
@AutoWired
StringRedisTemplate redisTemplate;
```

## String型

```
ValueOperations opsForValue = redisTemplate.opsForValue();

opsForValue.set(key, value);	//设置当前的key以及value值

opsForValue.set(key, value, offset);//用 value 参数覆写给定 key 所储存的字符串值，从偏移量 offset 开始

opsForValue.set(key, value, timeout, unit);	 //设置当前的key以及value值并且设置过期时间

opsForValue.setIfAbsent(key, value);//重新设置key对应的值，如果存在返回false，否则返回true

opsForValue.get(key, start, end);	//返回key中字符串的子字符

opsForValue.increment(key,double increment);//以增量的方式将double值存储在变量中

opsForValue.increment(key,long  increment);	//通过increment(K key, long delta)方法以增量方式存储long值（正值则自增，负值则自减）
```

## Hash类型

```java
HashOperations opsForHash = redisTemplate.opsForHash();

BoundHashOperations<String, Object, Object> operations = redisTemplate.boundHashOps();//等价于<K,<K,V>>

opsForHash.get(key, field);	//获取变量中的指定map键是否有值,如果存在该map键则获取值，没有则返回null

opsForHash.entries(key);	//获取变量中的键值对

opsForHash.put(key, hashKey, value);	//新增hashMap值

opsForHash.putAll(key, maps);	//以map集合的形式添加键值对（一个键对应一个值）

opsForHash.putIfAbsent(key, hashKey, value);	//仅当hashKey不存在时才设置

opsForHash.delete(key, fields);	//删除一个或者多个hash表字段
```

## List类型

```JAVA
ListOperations opsForList = redisTemplate.opsForList();

opsForList.leftPushAll(key, list); //新增list或原列表对象后插list

List<String> range = opsForList.range(key, -100, 100);
```

## 其他

```java
redisTemplate.hasKey(key);				//判断是否有key所对应的值，有则返回true，没有则返回false

redisTemplate.opsForValue().get(key);	//有则取出key值所对应的值

redisTemplate.delete(key);				//删除单个key值

redisTemplate.delete(keys); 			//其中keys:Collection<K> keys

redisTemplate.dump(key);				//将当前传入的key值序列化为byte[]类型

redisTemplate.expire(key, timeout, unit);	//设置过期时间

redisTemplate.expireAt(key, date);		//设置过期时间

redisTemplate.keys(pattern);			//查找匹配的key值，返回一个Set集合类型

redisTemplate.rename(oldKey, newKey);	//返回传入key所存储的值的类型

redisTemplate.renameIfAbsent(oldKey, newKey);	//如果旧值存在时，将旧值改为新值

redisTemplate.randomKey();				//从redis中随机取出一个key

redisTemplate.getExpire(key);			//返回当前key所对应的剩余过期时间

redisTemplate.getExpire(key, unit);		//返回剩余过期时间并且指定时间单位

redisTemplate.persist(key);				//将key持久化保存

redisTemplate.move(key, dbIndex);		//将当前数据库的key移动到指定redis中数据库当中
```



# 持久化

Redis有两种持久化方案：

- RDB持久化
- AOF持久化



## RDB持久化

RDB全称Redis Database Backup file（Redis数据备份文件），也被叫做Redis数据快照。简单来说就是把内存中的所有数据都记录到磁盘中。当Redis实例故障重启后，从磁盘读取快照文件，恢复数据。快照文件称为RDB文件，默认是保存在当前运行目录。

**RDB持久化在四种情况下会执行：**

- 执行save命令
- 执行bgsave命令，即后台save
- Redis停机时
- 触发RDB条件时，见下

Redis内部有触发RDB的机制，可以在redis.conf文件中找到，格式如下：

```properties
# 900秒内，如果至少有1个key被修改，则执行bgsave ， 如果是save "" 则表示禁用RDB
save 900 1  
save 300 10  
save 60 10000 
```

## AOF持久化

AOF全称为Append Only File（追加文件）。Redis处理的每一个写命令都会记录在AOF文件，可以看做是命令日志文件。

### AOF配置开启及配置记录策略

AOF默认是关闭的，需要修改redis.conf配置文件来开启AOF：

```properties
# 是否开启AOF功能，默认是no
appendonly yes
# AOF文件的名称
appendfilename "appendonly.aof"
```



AOF的命令记录的频率也可以通过redis.conf文件来配：

```properties
# 表示每执行一次写命令，立即记录到AOF文件
appendfsync always 
# 写命令执行完先放入AOF缓冲区，然后表示每隔1秒将缓冲区数据写到AOF文件，是默认方案
appendfsync everysec 
# 写命令执行完先放入AOF缓冲区，由操作系统决定何时将缓冲区内容写回磁盘
appendfsync no
```



三种策略对比：（这里的盘指AOF文件）

![image-20210725151654046](assets/image-20210725151654046.png)

### AOF文件重写

因为是记录命令，AOF文件会比RDB文件大的多。而且AOF会记录对同一个key的多次写操作，但只有最后一次写操作才有意义时转化为一次对key的写操作。

- 可以通过执行bgrewriteaof命令，可以让AOF文件执行重写功能

- Redis也会在触发阈值时自动去重写AOF文件。阈值也可以在redis.conf中配置：

```properties
# AOF文件比上次文件 增长超过多少百分比则触发重写
auto-aof-rewrite-percentage 100
# AOF文件体积最小多大以上才触发重写 
auto-aof-rewrite-min-size 64mb 
```

## RDB与AOF对比

RDB和AOF各有自己的优缺点，如果对数据安全性要求较高，在实际开发中往往会**结合**两者来使用。

![image-20210725151940515](assets/image-20210725151940515.png)

# 主从复制(解决高并发读)

## 全量同步（SYNC）

主从第一次建立连接时，会执行**全量同步**，将master节点的所有数据都拷贝给slave节点

### master如何判断slave是否为第一次来连接

有几个概念，可以作为判断依据：

- **Replication Id**：服务器运行id，是数据集的标记，id一致则说明是同一数据集。每一个master都有唯一的replid，slave则会继承master节点的replid
- **offset**偏移量，随着记录在repl_baklog中的数据增多而逐渐增大。slave完成同步时也会记录当前同步的offset。如果slave的offset小于master的offset，说明slave数据落后于master，需要更新。

因此，**master判断一个节点是否是第一次同步的依据，就是看replid是否一致**。

### 具体流程

如图：

![image-20210725152700914](assets/image-20210725152700914.png)



完整流程描述：

- slave节点请求增量同步
- master节点判断replid，发现不一致，拒绝增量同步
- master将执行bgsave生成RDB，发送RDB到slave
- slave清空本地数据，加载master的RDB
- master将RDB期间的命令记录在repl_baklog，并持续将log中的命令发送给slave
- slave执行接收到的命令，保持与master之间的同步

## 增量同步（部分重同步PSYNC）

全量同步需要先做RDB，然后将RDB文件通过网络传输个slave，成本太高了。因此除了第一次做全量同步，其它大多数时候slave与master都是做**增量同步**。

### master如何判断slave与自己的数据差异在哪里

通过repl_baklog进行判断，这个文件是一个固定大小的数组，只不过数组是环形，也就是说**角标到达数组末尾后，会再次从0开始读写**，这样数组头部的数据就会被覆盖。

repl_baklog中会记录Redis处理过的命令日志及offset，包括master当前的offset，和slave已经拷贝到的offset：

![image-20210725153359022](assets/image-20210725153359022.png) 

当然如果出现了未同步数据超出了上限，则会改为进行全量同步

### 具体流程

什么是增量同步？就是只更新slave与master存在差异的部分数据。如图：

![image-20210725153201086](assets/image-20210725153201086.png)

完整流程描述：

- slave节点请求增量同步
- master节点判断replid，发现一致，回复continue
- master获取repl_baklog的offset后的数据，并发给slave
- slave执行接收到的命令

## 主从同步优化

主从同步可以保证主从数据的一致性，非常重要。

可以从以下几个方面来优化Redis主从就集群：

1）提升全量同步效率

- 在master中配置repl-diskless-sync yes启用无磁盘复制，避免全量同步时的磁盘IO。（使用网络进行全量同步）
- Redis单节点上的内存占用不要太大，减少RDB传输数据量

2）尽量减少全量同步

- 适当提高repl_baklog的大小，发现slave宕机时尽快实现故障恢复，尽可能避免全量同步
- 限制一个master上的slave节点数量，如果实在是太多slave，则可以采用主-从-从链式结构，减少master压力

# 哨兵模式(解决高可用)

Note：哨兵模式是主从复制的一种

## 哨兵结构

哨兵的结构如图：

![image-20210725154528072](E:/java/StudyNotes-main/Redis/Redis高级篇-分布式缓存/assets/image-20210725154528072.png)

## 哨兵作用

### 监控

**作用：Sentinel 会不断检查您的master和slave是否按预期工作**

Sentinel基于心跳机制监测服务状态，每隔1秒向集群的每个实例发送ping命令：

•主观下线：如果某sentinel节点发现某实例未在规定时间响应，则认为该实例**主观下线**。

•客观下线：若超过指定数量（quorum）的sentinel都认为该实例主观下线，则该实例**客观下线**。quorum值最好超过Sentinel实例数量的一半。

![image-20210725154632354](E:/java/StudyNotes-main/Redis/Redis高级篇-分布式缓存/assets/image-20210725154632354.png)



### 故障恢复

**作用：如果master故障，Sentinel会将一个slave提升为master。当故障实例恢复后也以新的master为主**

#### 选举master

- 首先会判断slave节点与master节点断开时间长短，如果超过指定值（down-after-milliseconds * 10）则会排除该slave节点
- 然后判断slave节点的slave-priority值，越小优先级越高，如果是0则永不参与选举
- 如果slave-prority一样，则判断slave节点的offset值，越大说明数据越新，优先级越高
- 最后是判断slave节点的运行id大小，越小优先级越高。



#### 进行切换

流程如下：

- sentinel给备选的slave1节点发送slaveof no one命令，让该节点成为master
- sentinel给所有其它slave发送slaveof 192.168.150.101 7002 命令，让这些slave成为新master的从节点，开始从新的master上同步数据。
- 最后，sentinel将故障节点标记为slave，当故障节点恢复后会自动成为新的master的slave节点



![image-20210725154816841](E:/java/StudyNotes-main/Redis/Redis高级篇-分布式缓存/assets/image-20210725154816841.png)

### **通知**

**作用：Sentinel充当Redis客户端的服务发现来源，当集群发生故障转移时，会将最新信息推送给Redis的客户端**

# 分片集群（解决高可用、高并发写和海量数据存储）

**分片集群即将数据切分到不同master进行保存**，每个节点都有多个slave，master之间通过ping检测彼此是否存活，请求访问时访问集群任意节点，最终都会被转发到正确节点

## 分片集群结构

使用分片集群可以解决上述问题，如图:

![image-20210725155747294](E:\java\StudyNotes-main\Redis\Redis高级篇-分布式缓存\assets\image-20210725155747294.png)



## 散列插槽

**Redis会把每一个master节点映射到0~16383共16384个插槽（hash slot）上**

数据key不是与节点绑定，而是与插槽绑定。redis会根据key的有效部分计算哈希值，对16384取余即为最后的插槽值

- key的有效部分
  - key中包含"{}"，且“{}”中至少包含1个字符，“{}”中的部分是有效部分，如{a}num的有效部分为a
  - key中不包含“{}”，整个key都是有效部分，如num的有效部分为num

#  缓存更新

缓存更新是redis为了节约内存而设计出来的一个东西，主要是因为内存数据宝贵，当我们向redis插入太多数据，此时就可能会导致缓存中的数据过多，所以redis会对部分数据进行更新，或者把他叫为淘汰更合适。

**内存淘汰：**redis自动进行，当redis内存达到咱们设定的max-memery的时候，会自动触发淘汰机制，淘汰掉一些不重要的数据(可以自己设置策略方式，见下面的内存淘汰策略)

**超时剔除：**当我们给redis设置了过期时间ttl之后，redis会将超时的数据进行删除，方便咱们继续使用缓存

**主动更新：**我们可以手动调用方法把缓存删掉，通常用于解决缓存和数据库不一致问题

![1653322506393](assets/1653322506393.png)

# 缓存同步

大多数情况下，浏览器查询到的都是缓存数据，如果缓存数据与数据库数据存在较大差异，可能会产生比较严重的后果。

所以我们必须保证数据库数据、缓存数据的一致性，这就是缓存与数据库的同步。

## 多级缓存

多级缓存就是充分利用请求处理的每个环节，分别添加缓存，减轻Tomcat压力，提升服务性能：

- 浏览器访问静态资源时，优先读取浏览器本地缓存(强缓存和协商缓存)
- 访问非静态资源（ajax查询数据）时，访问服务端
- 请求到达Nginx后，优先读取Nginx本地缓存
- 如果Nginx本地缓存未命中，则去直接查询Redis（不经过Tomcat）
- 如果Redis查询未命中，则查询Tomcat
- 请求进入Tomcat后，优先查询JVM进程缓存
- 如果JVM进程缓存未命中，则查询数据库

![image-20210821075558137](assets/image-20210821075558137.png)

## 数据库缓存同步

由于我们的**缓存的数据源来自于数据库**,而数据库的**数据是会发生变化的**,因此,如果当数据库中**数据发生变化,而缓存却没有同步**,此时就会有**一致性问题存在**,其后果是:

用户使用缓存中的过时数据,就会产生类似多线程数据安全问题,从而影响业务,产品口碑等;怎么解决呢？有如下几种方案

1. Cache Aside Pattern 人工编码方式：缓存调用者在更新完数据库后再去更新缓存，也称之为双写方案

**Note1：采用删除缓存而非更新缓存，因为数据库对数据进行更改的数据可能不是什么热点数据，则本次更新动作意义并不大**

**Note2：采用该方案应先操作数据库，再删除缓存，因为因为缓存的写入通常要远远快于数据库的写入**

2. Read/Write Through Pattern : 由系统本身完成，将缓存和数据库整合为一个服务，由系统本身去保证一致性

3. Write Behind Caching Pattern ：调用者只操作缓存，其他线程去异步处理数据库，实现最终一致

## ----数据同步策略

缓存数据同步的常见方式有三种：

**设置有效期**：给缓存设置有效期，到期后自动删除。再次查询时更新

- 优势：简单、方便
- 缺点：时效性差，缓存过期之前可能不一致
- 场景：更新频率较低，时效性要求低的业务

**同步双写**：在修改数据库的同时，直接修改（或删除）缓存

- 优势：时效性强，缓存与数据库强一致
- 缺点：有代码侵入，耦合度高；
- 场景：对一致性、时效性要求较高的缓存数据

**异步通知：**修改数据库时发送事件通知，相关服务监听到通知后修改缓存数据

- 优势：低耦合，可以同时通知多个缓存服务
- 缺点：时效性一般，可能存在中间不一致状态
- 场景：时效性要求一般，有多个服务需要同步

# 缓存回收

## 过期key策略

Redis支持如下两种过期策略：

### 惰性删除

客户端访问一个key的时候，Redis会先检查它的过期时间，如果发现过期就立刻删除这个key。

### 定期删除

Redis会将设置了过期时间的key放到一个独立的字典expires中，并对该字典进行每秒10次的过期扫描，

过期扫描不会遍历字典中所有的key，而是采用了一种简单的贪心策略。该策略的删除逻辑如下：

从过期字典中随机选择20个key；

删除这20个key中已过期的key；

如果已过期key的比例超过25%，则重复步骤1。

## 内存淘汰策略

内存淘汰：就是当Redis内存使用达到设置的上限时，主动挑选部分key删除以释放更多内存的流程。

Redis支持8种不同策略来选择要删除的key：

* noeviction： 不淘汰任何key，但是内存满时不允许写入新数据，默认就是这种策略。
* volatile-ttl： 对设置了TTL的key，比较key的剩余TTL值，TTL越小越先被淘汰
* allkeys-random：对全体key ，随机进行淘汰。也就是直接从db->dict中随机挑选
* volatile-random：对设置了TTL的key ，随机进行淘汰。也就是从db->expires中随机挑选。
* allkeys-lru： 对全体key，基于LRU算法进行淘汰
* volatile-lru： 对设置了TTL的key，基于LRU算法进行淘汰
* allkeys-lfu： 对全体key，基于LFU算法进行淘汰
* volatile-lfu： 对设置了TTL的key，基于LFI算法进行淘汰
  比较容易混淆的有两个：
  * LRU（Least Recently Used），最少最近使用。用当前时间减去最后一次访问时间，这个值越大则淘汰优先级越高。
  * LFU（Least Frequently Used），最少频率使用。会统计每个key的访问频率，值越小淘汰优先级越高。

# 缓存的三大问题

## 缓存穿透问题（缓存空查询）

缓存穿透 ：缓存穿透是指客户端请求的数据在缓存中和数据库中都不存在，这样缓存永远不会生效，这些请求都会打到数据库。

解决方案：

**缓存空对象：**当我们客户端访问不存在的数据时，就是哪怕这个数据在数据库中也不存在，我们也把这个空数据存入到redis中去，这样，下次用户过来访问这个不存在的数据，那么在redis中也能找到这个数据就不会进入到缓存了

* 优点：实现简单，维护方便
* 缺点：
  * 额外的内存消耗
  * 可能造成短期的不一致

**布隆过滤：**布隆过滤器其实采用的是哈希思想来解决这个问题，通过一个庞大的二进制数组，走哈希思想去判断当前这个要查询的这个数据是否存在，如果布隆过滤器判断存在，则放行，这个请求会去访问redis，哪怕此时redis中的数据过期了，但是数据库中一定存在这个数据，在数据库中查询出来这个数据后，再将其放入到redis中

* 优点：内存占用较少，没有多余key
* 缺点：
  * 实现复杂
  * 存在误判可能

## 缓存雪崩问题（大量缓存同时突然失效）

缓存雪崩是指在同一时段大量的缓存key同时失效或者Redis服务宕机，导致大量请求到达数据库，带来巨大压力。

解决方案：

* 给不同的Key的TTL添加随机值
* 利用Redis集群提高服务的可用性
* 给缓存业务添加本地ehcache缓存 + hystrix进行限流&降级
* 给业务添加多级缓存

## 缓存击穿问题（热点突然失效）

缓存击穿问题也叫热点Key问题，就是一个被高并发访问并且缓存重建业务较复杂的key突然失效了，无数的请求访问会在瞬间给数据库带来巨大的冲击。

解决方案

**互斥锁**：查询缓存未命中时，必须先获取互斥锁成功才能访问数据库，如果获取失败则进行自旋等待直到超时退。出。

**逻辑过期**：我们把过期时间设置在 redis的value中，redis中的数据过期了不会去进行删除，当查询缓存发现数据未过期则直接返回，若数据过期了就开辟一个新线程进行数据更新，并返回过期的数据给原线程。

# 网络模型和通信协议

## Redis网络模型-Redis是单线程的吗？

* 如果仅仅聊Redis的核心业务部分（网络IO和键值对读写），答案是单线程
* 如果是聊整个Redis，那么答案就是多线程，如持久化、异步删除、集群同步等

## Redis为什么快？

主要业务是**单线程**的，避免了线程切换和竞争所产生的消耗；

Redis的大部分操作是在**内存**上完成的，这是它实现高性能的一个重要原因；

Redis采用了**IO多路复用**机制，使其在网络IO操作中能并发处理大量的客户端请求，实现高吞吐率。

## Redis通信协议-RESP协议

Redis是一个CS架构的软件，通信一般分两步（不包括pipeline和PubSub）：

客户端（client）向服务端（server）发送一条命令

服务端解析并执行命令，返回响应结果给客户端

因此客户端发送命令的格式、服务端响应结果的格式必须有一个规范，这个规范就是通信协议。

而在Redis中采用的是RESP（Redis Serialization Protocol）协议：

Redis 1.2版本引入了RESP协议

Redis 2.0版本中成为与Redis服务端通信的标准，称为RESP2

Redis 6.0版本中，从RESP2升级到了RESP3协议，增加了更多数据类型并且支持6.0的新特性--客户端缓存

但目前，默认使用的依然是RESP2协议，也是我们要学习的协议版本（以下简称RESP）。

在RESP中，通过首字节的字符来区分不同数据类型，常用的数据类型包括5种：

**单行字符串：**首字节是 ‘+’ ，后面跟上单行字符串，以CRLF（ "\r\n" ）结尾。例如返回"OK"： "+OK\r\n"

**错误（Errors）：**首字节是 ‘-’ ，与单行字符串格式一样，只是字符串是异常信息，例如："-Error message\r\n"

**数值：**首字节是 ‘:’ ，后面跟上数字格式的字符串，以CRLF结尾。例如：":10\r\n"

**多行字符串：**首字节是 ‘$’ ，表示二进制安全的字符串，最大支持512MB：

如果大小为0，则代表空字符串："$0\r\n\r\n"

如果大小为-1，则代表不存在："$-1\r\n"

**数组：**首字节是 ‘*’，后面跟上数组元素个数，再跟上元素，元素数据类型不限:

# 秒杀问题和分布式锁

## 如何保证秒杀时的线程安全？（即保证查询库存与扣减库存的一致性）

使用乐观锁，只有当扣减操作时的实际库存与查询库存时的数值一致时才可以进行扣除操作。

## 如何改进高并发导致乐观锁的失败率较高？

使用乐观锁，当进行扣减操作时的实际库存只要大于0就可以进行扣除操作。

## 如果保证一个用户只能抢一个优惠券？

- 使用悲观锁synchronized（一般更新使用乐观锁，插入只能使用悲观锁），锁住用户id的在串池中的常量，保证一个库存表中该用户只能有一个优惠券订单

- 由于需要同时对库存表和订单表进行操作，必须加上事务，保证所有操作必须同时成功或失败



## 1、如何保证分布式系统的锁对象统一？（为什么不可以使用synchronized进行加锁）

- synchronized 只是本地锁，锁的也只是当前jvm下的对象，而分布式集群节点之间无法共享synchronized锁
- 利用Redis自带的互斥锁setnx代替synchronized（Redis对所有进程均可见），使用key锁住用户id

## 2、设置好了锁，但是服务出现宕机，没有执行删除锁逻辑，这就造成了死锁

解决：设置过期时间

## 3、如何防止线程1的锁被线程2释放？

对比key对应的value是否为自己的线程id，只有为自己的线程id时才可以释放锁

## 4、将要删除的时候锁过期了，别人重新进行了加锁，则会删除了别人的锁（如何保证判断与释放锁操作的原子性？）

使用lua脚本，保证获取锁和删除锁操作的原子性

## 总结如何实现分布式锁

利用Redis自带的互斥锁setnx锁住用户线程的id并设置锁的过期时间，释放锁前判断当前线程是否为自己的线程，只有为自己的线程id时才可以释放锁，并使用lua脚本保证判断和释放锁的操作的原子性

```java
public Map<String, List<Catalog2Vo>> getCatalogJsonDbWithRedisLock() {
    String uuid = UUID.randomUUID().toString();//线程id，可以使用随机的uuid来代替
    ValueOperations<String, String> ops = stringRedisTemplate.opsForValue();
    //设置锁并设置对应的线程id和过期时间,ops.setIfAbsent(key, value) == setnx key value为如果不存在则设置并返回true
    Boolean lock = ops.setIfAbsent("lock", uuid,500, TimeUnit.SECONDS);
    if (lock) {
        Map<String, List<Catalog2Vo>> categoriesDb = getCategoryMap();
        String lockValue = ops.get("lock");
        // 使用lua脚本保证get和delete操作的为同一把锁,返回1代表成功，返回0代表失败
        String script = "if redis.call(\"get\",KEYS[1]) == ARGV[1] then\n" +
            "    return redis.call(\"del\",KEYS[1])\n" +
            "else\n" +
            "    return 0\n" +
            "end";
        stringRedisTemplate.execute(
            new DefaultRedisScript<Long>(script, Long.class), // 脚本和返回类型
            Arrays.asList("lock"), // 参数KEYS
            lockValue); // 参数ARGV
        return categoriesDb;
    }else {
        // 睡眠0.1s后，重新调用（自旋）
        try {
            Thread.sleep(100);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        return getCatalogJsonDbWithRedisLock();
    }
}
```

## 分布式锁-redission

### 使用方法

- 引入依赖：

```java
<dependency>
	<groupId>org.redisson</groupId>
	<artifactId>redisson</artifactId>
	<version>3.13.6</version>
</dependency>
```

- 配置Redisson客户端：

```java
@Configuration
public class RedissonConfig {

    @Bean
    public RedissonClient redissonClient(){
        // 配置
        Config config = new Config();
        config.useSingleServer().setAddress("redis://192.168.200.130:6379")
            .setPassword("123");
        // 创建RedissonClient对象
        return Redisson.create(config);
    }
}

```

#### 可重入锁

以可重入锁为例，读写锁、信号量（在redis中设置其上限）、闭锁（使用lock.trySetCount(count)设置其上限）等同理

- Redission版

```java
@Resource
private RedissionClient redisson;
……
//获取一把锁，只要锁的名字相同，就是同一把锁
RLock lock = redissonClient.getLock("lock");
```

锁的续期：如果未设置锁的超时时间，则默认加的锁都是30s的时间  1）锁的自动续期，如果业务超长，运行期间自动给锁续上新的30s。不用担心业务时间长，锁自动过期被删除。

2）加锁的业务只要运行完成，就不会给当前锁续期，即使不手动解锁，锁默认在30s之后自动删除，可以通过修改Config.lockWatchdogTimeout来另行指定看门狗的锁续期时间。


- JUC版

```
ReentrantLock lock = new ReentrantLock();
```

#### 读写锁

- Redission版

```java
@Resource
private RedissionClient redisson;
……
RReadWriteLock lock = redisson.getReadWriteLock("wr-lock");
RLock rLock = lock.writeLock();//改数据加写锁
RLock rLock = lock.readLock();//读数据加读锁
```

- JUC版

```java
ReentrantReadWriteLock rw = new ReentrantReadWriteLock();
ReentrantReadWriteLock.ReadLock r = rw.readLock();
ReentrantReadWriteLock.WriteLock w = rw.writeLock();
```

#### 信号量

- Redission版

```java
@Resource
private RedissionClient redisson;
……
RSemaphore park = redisson.getSemaphore("park");
```

- JUC版

```java
Semaphore semaphore = new Semaphore(3);
```

#### 闭锁

- Redission版

```java
@Resource
private RedissionClient redisson;
……
RCountDownLatch latch = redisson.getCountDownLatch("door");
latch.trySetCount(10);
```

- JUC版

```java
CountDownLatch latch = new CountDownLatch(10);
```



# Lua脚本

Redis提供了Lua脚本功能，在一个脚本中编写多条Redis命令，确保多条命令执行时的原子性（因为redis的核心业务是单线程的）。

- 执行redis命令

```
redis.call('命令名称', 'key', '其它参数', ...)
```

- 定义变量

```lua
a = 5               -- 全局变量
local b = 5         -- 局部变量
```

- 传递参数

```lua
KEYS[1]、KEY[2]…… 		--传入的key
ARGV[1] 、KEY[2]……		--传入的其他参数
```

## java调用lua脚本

```java
private static final DefaultRedisScript<Long> UNLOCK_SCRIPT;
    static {
        UNLOCK_SCRIPT = new DefaultRedisScript<>();
        UNLOCK_SCRIPT.setLocation(new ClassPathResource("unlock.lua"));
        UNLOCK_SCRIPT.setResultType(Long.class);
    }

public void unlock() {
    // 调用lua脚本
    stringRedisTemplate.execute(
            UNLOCK_SCRIPT,//脚本名
            Collections.singletonList(KEY_PREFIX + name),//redis的键集合
            ID_PREFIX + Thread.currentThread().getId());//其他传入参数集合
}
```

