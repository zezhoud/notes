## Redis

### 1 Redis 简介

#### 1.1 Redis的特点

- Redis支持数据的持久化，可以将内存中的数据保持在磁盘中，重启的时候可以再次加载进行使用。
- Redis不仅仅支持简单的key-value类型的数据，同时还提供list，set，zset，hash等数据结构的存储。
- Redis支持数据的备份，即master-slave模式的数据备份。

#### 1.2 Redis 优势

- 性能极高 – Redis能读的速度是110000次/s,写的速度是81000次/s 。
- 丰富的数据类型 – Redis支持二进制案例的 Strings, Lists, Hashes, Sets 及 Ordered Sets 数据类型操作。
- 原子 – Redis的所有操作都是原子性的，同时Redis还支持对几个操作全并后的原子性执行。
- 丰富的特性 – Redis还支持 publish/subscribe, 通知, key 过期等等特性。

#### 1.3 Redis与其他key-value存储有什么不同

- Redis有着更为复杂的数据结构并且提供对他们的原子性操作，这是一个不同于其他数据库的进化路径。Redis的数据类型都是基于基本数据结构的同时对程序员透明，无需进行额外的抽象。
- Redis运行在内存中但是可以持久化到磁盘，所以在对不同数据集进行高速读写时需要权衡内存，应为数据量不能大于硬件内存。在内存数据库方面的另一个优点是， 相比在磁盘上相同的复杂的数据结构，在内存中操作起来非常简单，这样Redis可以做很多内部复杂性很强的事情。 同时，在磁盘格式方面他们是紧凑的以追加的方式产生的，因为他们并不需要进行随机访问。

### 2 Redis 数据类型

Redis支持五种数据类型：string（字符串），hash（哈希），list（列表），set（集合）及zset(sorted set：有序集合)。

#### 2.1 String（字符串）

string是redis最基本的类型，你可以理解成与Memcached一模一样的类型，一个key对应一个value。

string类型是二进制安全的。意思是redis的string可以包含任何数据。比如jpg图片或者序列化的对象 。

string类型是Redis最基本的数据类型，一个键最大能存储512MB。

**实例**

```shell
redis 127.0.0.1:6379> SET name "redis.net.cn"OKredis 127.0.0.1:6379> GET name"redis.net.cn"
```

在以上实例中我们使用了 Redis 的 **SET** 和 **GET** 命令。键为 name，对应的值为redis.net.cn。

**注意：**一个键最大能存储512MB。

#### 2.2 Hash（哈希）

Redis hash 是一个键值对集合。

Redis hash是一个string类型的field和value的映射表，hash特别适合用于存储对象。

**实例**

```shell
redis 127.0.0.1:6379> HMSET user:1 username redis.net.cn password redis.net.cn points 200OKredis 127.0.0.1:6379> HGETALL user:11) "username"2) "redis.net.cn"3) "password"4) "redis.net.cn"5) "points"6) "200"redis 127.0.0.1:6379>
```

以上实例中 hash 数据类型存储了包含用户脚本信息的用户对象。 实例中我们使用了 Redis **HMSET, HEGTALL** 命令，**user:1** 为键值。

每个 hash 可以存储 232 - 1 键值对（40多亿）。

#### 2.3 List（列表）

Redis 列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素导列表的头部（左边）或者尾部（右边）。

**实例**

```shell
redis 127.0.0.1:6379> lpush redis.net.cn redis(integer) 1redis 127.0.0.1:6379> lpush redis.net.cn mongodb(integer) 2redis 127.0.0.1:6379> lpush redis.net.cn rabitmq(integer) 3redis 127.0.0.1:6379> lrange redis.net.cn 0 101) "rabitmq"2) "mongodb"3) "redis"redis 127.0.0.1:6379>
```

列表最多可存储 232 - 1 元素 (4294967295, 每个列表可存储40多亿)。

#### 2.4 Set（集合）

Redis的Set是string类型的无序集合。

集合是通过哈希表实现的，所以添加，删除，查找的复杂度都是O(1)。

**sadd** 命令

添加一个string元素到,key对应的set集合中，成功返回1,如果元素以及在集合中返回0,key对应的set不存在返回错误。

```
sadd key member
```

**实例**

```shell
redis 127.0.0.1:6379> sadd redis.net.cn redis(integer) 1redis 127.0.0.1:6379> sadd redis.net.cn mongodb(integer) 1redis 127.0.0.1:6379> sadd redis.net.cn rabitmq(integer) 1redis 127.0.0.1:6379> sadd redis.net.cn rabitmq(integer) 0redis 127.0.0.1:6379> smembers redis.net.cn 1) "rabitmq"2) "mongodb"3) "redis"
```

**注意：**以上实例中 rabitmq 添加了两次，但根据集合内元素的唯一性，第二次插入的元素将被忽略。

集合中最大的成员数为 232 - 1 (4294967295, 每个集合可存储40多亿个成员)。

#### 2.5 zset(sorted set：有序集合)

Redis zset 和 set 一样也是string类型元素的集合,且不允许重复的成员。

不同的是每个元素都会关联一个double类型的分数。redis正是通过分数来为集合中的成员进行从小到大的排序。

zset的成员是唯一的,但分数(score)却可以重复。

**zadd** 命令

添加元素到集合，元素在集合中存在则更新对应score

```
zadd key score member 
```

**实例**

```shell
redis 127.0.0.1:6379> zadd redis.net.cn 0 redis(integer) 1redis 127.0.0.1:6379> zadd redis.net.cn 0 mongodb(integer) 1redis 127.0.0.1:6379> zadd redis.net.cn 0 rabitmq(integer) 1redis 127.0.0.1:6379> zadd redis.net.cn 0 rabitmq(integer) 0redis 127.0.0.1:6379> ZRANGEBYSCORE redis.net.cn 0 1000 1) "redis"2) "mongodb"3) "rabitmq"
```

### 3 Redis 命令

本地启动 redis 客户端，打开终端并输入命令 **redis-cli**。该命令会连接本地的 redis 服务。

```
$ redis-cli
```

如果需要在远程 redis 服务上执行命令，同样我们使用的也是 **redis-cli** 命令。

```
$ redis-cli -h host -p port -a password
$ redis-cli -h 127.0.0.1 -p 6379 -a "mypass"
```

#### 3.1 Redis 键命令

| 命令               | 描述                                                | 实例                         |
| :----------------- | :-------------------------------------------------- | ---------------------------- |
| del key            | key 存在时删除 key                                  | del test                     |
| dump key           | 序列化给定 key ，并返回被序列化的值                 | dump test                    |
| exists key         | 检查给定 key 是否存在                               | exists test                  |
| expire key time    | 设置 key 的过期时间，key 过期后将不再可用           | expire test 60               |
| pexpire key time   | 设置 key 的过期时间（毫秒），key 过期后将不再可用   | pexpire test 3600            |
| pexpireat key time | 设置 key 的过期时间（时间戳），key 过期后将不再可用 | pexpireat test 1555555555005 |
| keys pattern       | 查找所有符合给定模式 pattern 的 key                 | keys tes*                    |
| move key db        | 将当前数据库的 key 移动到给定的数据库 db 当中       | move test 1                  |
| persist key        | 移除给定 key 的过期时间，使得 key 永不过期          | persist test                 |
| ttl key            | 以秒为单位返回 key 的剩余过期时间                   | ttl test                     |
| pttl key           | 以毫秒为单位返回 key 的剩余过期时间                 | pttl test                    |
| randomkey          | 从当前数据库中随机返回一个 key                      | randomkey                    |
| rename key key     | 修改 key 的名称                                     | rename name name1            |
| renamenx key key   | 新的 key 不存在时修改 key 的名称                    | rename name name1            |
| type key           | 返回 key 所储存的值的类型                           | type name                    |

#### 3.2 Key的层级结构

Redis没有类似MySQL中的Table的概念，那该如何区分不同类型的key？

例如，需要存储用户.商品信息到redis，有一个用户id是1，有一个商品id恰好也是1，此时如果使用id作为key，那就会冲突了，该怎么办？

可以通过给key添加前缀加以区分，不过这个前缀不是随便加的，有一定的规范：Redis的key允许有多个单词形成层级结构，多个单词之间用':'隔开

```markdown
项目名:业务名:类型ID
```

可以根据自己的需求来删除或添加词条

例如项目名称叫 project，有user和product两种不同类型的数据，我们可以这样定义key：

- user相关的key：**project:user:1**

- product相关的key：**project:product:1**

如果Value是一个Java对象，例如一个User对象，则可以将对象序列化为JSON字符串后存储：

| **KEY**           | **VALUE**                                 |
| ----------------- | ----------------------------------------- |
| project:user:1    | {"id":1, "name": "Jack", "age": 21}       |
| project:product:1 | {"id":1, "name": "小米11", "price": 4999} |

#### 3.3 Redis 字符串(String)命令

| 命令                      | 描述                                                         | 实例                             |
| ------------------------- | ------------------------------------------------------------ | -------------------------------- |
| set key value             | 设置指定 key 的值                                            | set name Tim                     |
| get key                   | 获取指定 key 的值                                            | get name                         |
| setrange key offset value | 用指定的字符串覆盖给定 key 所储存的字符串值，覆盖的位置从偏移量 offset 开始 | setrange name 1 ch               |
| getrange key start end    | 获取存储在指定 key 中字符串的子字符串                        | getrange name 0 -1               |
| getset key value          | 设置指定 key 的值，并返回 key 旧的值                         | getset name Kiven                |
| setbit key offset         | 对 key 所储存的字符串值，设置或清除指定偏移量上的位(bit)     | setbit bit 10086 1               |
| getbit key offset         | 对 key 所储存的字符串值，获取指定偏移量上的位(bit)           | getbit bit 10086                 |
| mset k1 v1 k2 v2 ..       | 同时设置一个或多个 key-value 对                              | mset key1 "Hello" key2 "World"   |
| mget k1 k2 ...            | 返回所有(一个或多个)给定 key 的值                            | mget test1 test2                 |
| setex key time value      | 为指定的 key 设置值及其过期时间。如果 key 已经存在， SETEX 命令将会替换旧的值 | set name 10 Tim                  |
| psetex key time value     | 以毫秒为单位设置 key 的生存时间                              | psetex mykey 1000 "Hello"        |
| setnx key value           | 指定的 key 不存在时，为 key 设置指定的值                     | setnx name Tim                   |
| strlen key                | 获取指定 key 所储存的字符串值的长度                          | strlen name                      |
| msetnx k1 v1 k2 v2 ...    | 所有给定 key 都不存在时，同时设置一个或多个 key-value 对     | msetnx key1 "Hello" key2 "World" |
| incr key                  | 将 key 中储存的数字值增一                                    | incr age                         |
| incrby key value          | 将 key 中储存的数字加上指定的增量值                          | incrby age 2                     |
| incrbyfloat key value     | 将 key 中储存的值加上指定的浮点数增量值                      | incrbyfloat age 0.1              |
| decr key                  | 将 key 中储存的数字值减一                                    | decr age                         |
| decrby key value          | 将 key 所储存的值减去指定的减量值                            | decrby age 2                     |
| append key value          | 为指定的 key 追加值                                          | append name KK                   |

#### 3.4 Redis 哈希(Hash)命令

Redis hash 是一个string类型的field和value的映射表，hash特别适合用于存储对象

| 命令                        | 描述                                             | 实例                                   |
| --------------------------- | ------------------------------------------------ | -------------------------------------- |
| hdel key field              | 删除哈希表 key 中的一个或多个指定字段            | hdel myhash field1                     |
| hexists key field           | 查看哈希表的指定字段是否存在                     | hexists myhash field1                  |
| hget key field              | 返回哈希表中指定字段的值                         | hget myhash field1                     |
| hgetall key                 | 返回哈希表中，所有的字段和值                     | hgetall myhash                         |
| hincrby key field num       | 为哈希表中的字段值加上指定增量值                 | hincrby myhash field 1                 |
| hincrbyfloat key field num  | 为哈希表中的字段值加上指定浮点数增量值           | hincrbyfloat myhash field 0.1          |
| hkeys key                   | 获取哈希表中的所有字段名                         | hkeys myhash                           |
| hlen key                    | 获取哈希表中字段的数量                           | hlen myhash                            |
| hmget key field1 field2 ... | 返回哈希表中，一个或多个给定字段的值             | hmget myhash field1 field2             |
| hmset key field1 value1 ... | 同时将多个 field-value (字段-值)对设置到哈希表中 | hmset myhash field1 "foo" field2 "bar" |
| hset key field value        | 为哈希表中的字段赋值                             | hset myhash field "foo"                |
| hsetnx key field value      | 为哈希表中不存在的的字段赋值                     | hsetnx myhash field "foo"              |
| hvals key                   | 返回哈希表所有字段的值                           | hvals myhash                           |

#### 3.5 Redis 列表(list)命令

列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素导列表的头部（左边）或者尾部（右边）

| 命令                                             | 描述                                                         | 实例                            |
| ------------------------------------------------ | ------------------------------------------------------------ | ------------------------------- |
| blpop key time                                   | 移出并获取列表的第一个元素                                   | blpop test 10                   |
| brpop key time                                   | 移出并获取列表的最后一个元素                                 | brpop test 10                   |
| brpoplpush key1 key2 time                        | 从列表中弹出一个值，将弹出的元素插入到另外一个列表中并返回它 | brpoplpush test1  test2 1       |
| lindex key index                                 | 通过索引获取列表中的元素                                     | lindex test 0                   |
| linsert key before\|after exists_value new_value | 在列表的元素前或者后插入元素                                 | linsert test before java python |
| llen key                                         | 返回列表的长度                                               | lken test                       |
| lpop key                                         | 移除并返回列表的第一个元素                                   | lpop test                       |
| lpush key value1 value2 ...                      | 将一个或多个值插入到列表头部                                 | lpush test python css           |
| lpushx key value1 value2 ...                     | 将一个或多个值插入到已存在的列表头部                         | lpushx test python css          |
| lrange key start end                             | 返回列表中指定区间内的元素                                   | lrange test 0 -1                |
| lrem key count value                             | 根据参数 COUNT 的值，移除列表中与参数 VALUE 相等的元素       | lrem test 2 java                |
| lset key index value                             | 通过索引来设置元素的值                                       | lset test 1 css                 |
| ltrim key start end                              | 让列表只保留指定区间内的元素，不在指定区间之内的元素都将被删除 | ltrim test 1 3                  |
| rpop key                                         | 移除并返回列表的最后一个元素                                 | rpop key                        |
| rpoplpush source_key new_key                     | 移除列表的最后一个元素，并将该元素添加到另一个列表并返回     | rpoplpush test1 test2           |
| rpush key value1 value2 ...                      | 将一个或多个值插入到列表的尾部                               | rpush test jquery               |
| rpushx key value1 value2 ...                     | 将一个或多个值插入到已存在的列表尾部                         | rpushx key value1 value2 ...    |

#### 3.5 Redis 集合(set)命令

Redis的Set是string类型的无序集合。集合成员是唯一的，这就意味着集合中不能出现重复的数据

| 命令                              | 描述                                                        | 实例                          |
| --------------------------------- | ----------------------------------------------------------- | ----------------------------- |
| sadd key value1 value2 ...        | 将一个或多个成员元素加入到集合中                            | sadd test java                |
| scard key                         | 返回集合中元素的数量                                        | scard test                    |
| sdiff key1 key2                   | 返回给定集合之间的差集                                      | sdiff test1 test2             |
| sdiffstore new_key key1 key2      | 将给定集合之间的差集存储在指定的集合中                      | sdiffstore test3 test1 test2  |
| sinter key1 key2                  | 返回给定所有给定集合的交集                                  | sinter test1 test2            |
| sinterstore new_key key1 key2     | 将给定集合之间的交集存储在指定的集合中                      | sinterstore test3 test1 test2 |
| sismember key value               | 判断成员元素是否是集合的成员                                | sismember test java           |
| smembers key                      | 返回集合中的所有的成员                                      | smembers test                 |
| smove source_key new_key field    | 将指定成员 member 元素从 source 集合移动到 destination 集合 | smove test1 test2 java        |
| spop key                          | 移除并返回集合中的一个随机元素                              | spop test                     |
| srandmember key [count]           | 返回集合中的一个或多个随机元素                              | srandmember test              |
| srem key field                    | 移除集合中的一个或多个成员元素                              | srem test java                |
| sunion key1 key2 ...              | 返回给定集合的并集                                          | sunion test1 test2            |
| sunionstore new_key key1 key2 ... | 将给定集合的并集存储在指定的集合 destination 中             | sunionstore test3 test1 test2 |
| sscan key cursor match            | 用于迭代集合键中的元素                                      | sscan test 0 match t*         |

#### 3.6 Redis 有序集合(sorted set)命令

Redis 有序集合和集合一样也是string类型元素的集合,且不允许重复的成员

| 命令                                            | 描述                                                         | 实例                            |
| ----------------------------------------------- | ------------------------------------------------------------ | ------------------------------- |
| zadd key value1 value2 ...                      | 将一个或多个成员元素及其分数值加入到有序集当中               | zadd test1 1 java 2 python      |
| zcard key                                       | 计算集合中元素的数量                                         | zcard test                      |
| zcount key min max                              | 计算有序集合中指定分数区间的成员数量                         | zcount test 1 2                 |
| zincrby key increment member                    | 对有序集合中指定成员的分数加上增量 increment                 | zincrby myzset 2 "hello"        |
| zinterstore new_key num key1 key2               | 计算给定的一个或多个有序集的交集，其中给定 key 的数量必须以 numkeys 参数指定，并将该交集(结果集)储存到 destination | zinterstore test3 2 test1 test2 |
| zlexcount key min max                           | 在计算有序集合中指定字典区间内成员数量                       |                                 |
| zrange key start stop [WITHSCORES]              | 返回有序集中，指定区间内的成员                               | zrange myzset 0 -1              |
| zrangebylex key start stop [LIMIT offset count] | 通过字典区间返回有序集合的成员                               |                                 |
| zrangebyscore                                   | 返回有序集合中指定分数区间的成员列表                         |                                 |
| zrank key value                                 | 返回有序集中指定成员的索引                                   | zrank test python               |
| zrem key value                                  | 移除有序集中的一个或多个成员，不存在的成员将被忽略           | zrem test java                  |
| zremrangebylex key min max                      | 移除有序集合中给定的字典区间的所有成员                       |                                 |
| zremrangebyrank key start stop                  | 移除有序集中，指定排名(rank)区间内的所有成员                 |                                 |
| zremrangebyscore key min max                    | 移除有序集中，指定分数（score）区间内的所有成员              |                                 |
| zrevrange key start stop                        | 返回有序集中，指定区间内的成员                               | zrevrange test1 0 -1            |
| Zrevrangebyscore key max min                    | 返回有序集中指定分数区间内的所有的成员                       |                                 |
| zrevrank key value                              | 返回有序集中成员的排名                                       |                                 |
| zscore key value                                | 返回有序集中，成员的分数值                                   | zscore test python              |
| zunionstore new_key                             | 计算给定的一个或多个有序集的并集，其中给定 key 的数量必须以 numkeys 参数指定，并将该并集(结果集)储存到 destination |                                 |
| zscan key cursor match count                    | 迭代有序集合中的元素                                         |                                 |

#### 3.7 Redis HyperLogLog命令

Redis HyperLogLog 是用来做基数统计的算法，HyperLogLog 的优点是，在输入元素的数量或者体积非常非常大时，计算基数所需的空间总是固定 的、并且是很小的

基数：比如数据集 {1, 3, 5, 7, 5, 7, 8}， 那么这个数据集的基数集为 {1, 3, 5 ,7, 8}, 基数(不重复元素)为5。 基数估计就是在误差可接受的范围内，快速计算基数

| 命令                 | 描述                                        | 实例                     |
| -------------------- | ------------------------------------------- | ------------------------ |
| pfadd key value1 ... | 将所有元素参数添加到 HyperLogLog 数据结构中 | pfadd keys 1 2 1 3 4 5 4 |
| pfcount key ...      | 返回给定 HyperLogLog 的基数估算值           | pfcount keys             |
| pgmerge              | 将多个 HyperLogLog 合并为一个 HyperLogLog   | pgmerge hll3 hll1 hll2   |

### 4 Java的redis客户端

![image-20221122174457750](assets/image-20221122174457750.png)

标记为❤的就是推荐使用的java客户端，包括：

- Jedis和Lettuce：这两个主要是提供了Redis命令对应的API，方便我们操作Redis，而SpringDataRedis又对这两种做了抽象和封装，因此我们后期会直接以SpringDataRedis来学习。
- Redisson：是在Redis基础上实现了分布式的可伸缩的java数据结构，例如Map.Queue等，而且支持跨进程的同步机制：Lock.Semaphore等待，比较适合用来实现特殊的功能需求。

#### 4.1 Jedis

##### 4.1.1 项目测试

1、引入依赖：

```xml
<!--jedis-->
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>3.7.0</version>
</dependency>
<!--单元测试-->
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter</artifactId>
    <version>5.7.0</version>
    <scope>test</scope>
</dependency>
```

2、建立连接

新建一个单元测试类，内容如下：

```java
private Jedis jedis;

@BeforeEach
void setUp() {
    // 1.建立连接
    // jedis = new Jedis("192.168.150.101", 6379);
    jedis = JedisConnectionFactory.getJedis();
    // 2.设置密码
    jedis.auth("123321");
    // 3.选择库
    jedis.select(0);
}
```

3、测试：

```java
@Test
void testString() {
    // 存入数据
    String result = jedis.set("name", "ANNA");
    System.out.println("result = " + result);
    // 获取数据
    String name = jedis.get("name");
    System.out.println("name = " + name);
}

@Test
void testHash() {
    // 插入hash数据
    jedis.hset("user:1", "name", "Jack");
    jedis.hset("user:1", "age", "21");

    // 获取
    Map<String, String> map = jedis.hgetAll("user:1");
    System.out.println(map);
}
```

4、释放资源

```java
@AfterEach
void tearDown() {
    if (jedis != null) {
        jedis.close();
    }
}
```

##### 4.1.2 Jedis连接池

Jedis本身是线程不安全的，并且频繁的创建和销毁连接会有性能损耗，因此推荐使用Jedis连接池代替Jedis的直连方式

1、创建Jedis的连接池

```java
public class JedisConnectionFacotry {

     private static final JedisPool jedisPool;

     static {
         //配置连接池
         JedisPoolConfig poolConfig = new JedisPoolConfig();
         poolConfig.setMaxTotal(8);
         poolConfig.setMaxIdle(8);
         poolConfig.setMinIdle(0);
         poolConfig.setMaxWaitMillis(1000);
         //创建连接池对象
         jedisPool = new JedisPool(poolConfig,
                 "192.168.150.101",6379,1000,"123321");
     }

     public static Jedis getJedis(){
          return jedisPool.getResource();
     }
}
```

**代码说明：**

-  JedisConnectionFacotry：工厂设计模式是实际开发中非常常用的一种设计模式，我们可以使用工厂，去降低代的耦合，比如Spring中的Bean的创建，就用到了工厂设计模式

- 静态代码块：随着类的加载而加载，确保只能执行一次，我们在加载当前工厂类的时候，就可以执行static的操作完成对 连接池的初始化

- 最后提供返回连接池中连接的方法.

2、使用连接池

使用了连接池后，当关闭连接其实并不是关闭，而是将Jedis还回连接池的。

```java
    @BeforeEach
    void setUp(){
        //建立连接
        /*jedis = new Jedis("127.0.0.1",6379);*/
        jedis = JedisConnectionFacotry.getJedis();
         //选择库
        jedis.select(0);
    }

   @AfterEach
    void tearDown() {
        if (jedis != null) {
            jedis.close();
        }
    }
```

#### 4.2 SpringDataRedis

SpringData是Spring中数据操作的模块，包含对各种数据库的集成，其中对Redis的集成模块就叫做SpringDataRedis，官网地址：https://spring.io/projects/spring-data-redis

* 提供了对不同Redis客户端的整合（Lettuce和Jedis）
* 提供了RedisTemplate统一API来操作Redis
* 支持Redis的发布订阅模型
* 支持Redis哨兵和Redis集群
* 支持基于Lettuce的响应式编程
* 支持基于JDK.JSON.字符串.Spring对象的数据序列化及反序列化
* 支持基于Redis的JDKCollection实现

SpringDataRedis中提供了RedisTemplate工具类，其中封装了各种对Redis的操作。并且将不同数据类型的操作API封装到了不同的类型中：

![1652976773295](assets/1652976773295.png)

##### 4.2.1 RedisTemplate

RedisTemplate可以接收任意Object作为值写入Redis，但是可读性较差，建议引入jackson转换，自定义RedisTemplate的序列化方式，代码如下：

```java
@Configuration
public class RedisConfig {

    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory connectionFactory){
        // 创建RedisTemplate对象
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        // 设置连接工厂
        template.setConnectionFactory(connectionFactory);
        // 创建JSON序列化工具
        GenericJackson2JsonRedisSerializer jsonRedisSerializer = 
            							new GenericJackson2JsonRedisSerializer();
        // 设置Key的序列化
        template.setKeySerializer(RedisSerializer.string());
        template.setHashKeySerializer(RedisSerializer.string());
        // 设置Value的序列化
        template.setValueSerializer(jsonRedisSerializer);
        template.setHashValueSerializer(jsonRedisSerializer);
        // 返回
        return template;
    }
}
```

整体可读性有了很大提升，并且能将Java对象自动的序列化为JSON字符串，并且查询时能自动把JSON反序列化为Java对象。不过，其中记录了序列化时对应的class名称，目的是为了查询时实现自动反序列化。这会带来额外的内存开销。

##### 4.2.2 StringRedisTemplate

SpringDataRedis提供了RedisTemplate的子类：StringRedisTemplate，它的key和value的序列化方式默认就是String方式。

![image-20221122221949477](assets/image-20221122221949477.png)

需要手动将对象转换成JSON格式后，以String的形式存储再Redis中。

### 5 Redis应用

#### 5.1 Redis代替Session

每个tomcat中都有一份属于自己的session,假设用户第一次访问第一台tomcat，并且把自己的信息存放到第一台服务器的session中，但是第二次这个用户访问到了第二台tomcat，那么在第二台服务器上，肯定没有第一台服务器存放的session，所以此时整个登录拦截功能就会出现问题，如何解决这个问题呢？早期的方案是session拷贝，就是说虽然每个tomcat上都有不同的session，但是每当任意一台服务器的session修改时，都会同步给其他的Tomcat服务器的session，这样的话，就可以实现session的共享了

但是这种方案具有两个大问题

1、每台服务器中都有完整的一份session数据，服务器压力过大。

2、session拷贝数据时，可能会出现延迟

![1653069893050](assets/1653069893050.png)

因此可以考虑使用redis存储用户数据，代替session完成所需的功能。当用户登录系统后，系统随机生成一段Token发送给用户，当用户下次发起请求时，携带系统发送的Token，系统接受来自用户的请求后，先校验用户的Token是否和存储在Redis中的该用户的Token是否一致，或有无过期等，满足条件才允许放行。

```java
@Override
public Result login(LoginFormDTO loginForm, HttpSession session) {
    // 1.校验手机号
    String phone = loginForm.getPhone();
    if (RegexUtils.isPhoneInvalid(phone)) {
        // 2.如果不符合，返回错误信息
        return Result.fail("手机号格式错误！");
    }
    // 3.从redis获取验证码并校验
    String cacheCode = stringRedisTemplate.opsForValue().get(LOGIN_CODE_KEY + phone);
    String code = loginForm.getCode();
    if (cacheCode == null || !cacheCode.equals(code)) {
        // 不一致，报错
        return Result.fail("验证码错误");
    }

    // 4.一致，根据手机号查询用户 select * from tb_user where phone = ?
    User user = query().eq("phone", phone).one();

    // 5.判断用户是否存在
    if (user == null) {
        // 6.不存在，创建新用户并保存
        user = createUserWithPhone(phone);
    }

    // 7.保存用户信息到 redis中
    // 7.1.随机生成token，作为登录令牌
    String token = UUID.randomUUID().toString(true);
    // 7.2.将User对象转为HashMap存储
    UserDTO userDTO = BeanUtil.copyProperties(user, UserDTO.class);
    Map<String, Object> userMap = BeanUtil.beanToMap(userDTO, new HashMap<>(),
            CopyOptions.create()
                    .setIgnoreNullValue(true)
                    .setFieldValueEditor((fieldName, fieldValue) -> fieldValue.toString()));
    // 7.3.存储
    String tokenKey = LOGIN_USER_KEY + token;
    stringRedisTemplate.opsForHash().putAll(tokenKey, userMap);
    // 7.4.设置token有效期
    stringRedisTemplate.expire(tokenKey, LOGIN_USER_TTL, TimeUnit.MINUTES);

    // 8.返回token
    return Result.ok(token);
}
```

#### 5.2 Redis应用于缓存

**缓存(**Cache)，就是数据交换的**缓冲区**，俗称的缓存就是**缓冲区内的数据**，一般从数据库中获取，存储于本地代码，例如:

```java
例1:Static final ConcurrentHashMap<K,V> map = new ConcurrentHashMap<>(); 本地用于高并发

例2:static final Cache<K,V> USER_CACHE = CacheBuilder.newBuilder().build(); 用于redis等缓存

例3:Static final Map<K,V> map =  new HashMap(); 本地缓存
```

由于其被**Static**修饰，所以随着类的加载而被加载到**内存之中**，作为本地缓存，由于其又被**final**修饰，所以其引用(例3:map)和对象(例3:new HashMap())之间的关系是固定的，不能改变，因此不用担心赋值(=)导致缓存失效

##### 5.2.1 为什么要使用缓存

缓存数据存储于代码中,而代码运行在内存中,内存的读写性能远高于磁盘,缓存可以大大降低**用户访问并发量带来的**服务器读写压力

实际开发过程中,企业的数据量,少则几十万,多则几千万,这么大数据量,如果没有缓存来作为"避震器",系统是几乎撑不住的,所以企业会大量运用到缓存技术;

但是缓存也会增加代码复杂度和运营的成本:

![image-20220523214414123](assets/image-20220523214414123.png)

##### 5.2.2 如何使用缓存

实际开发中,会构筑多级缓存来使系统运行速度进一步提升,例如:本地缓存与redis中的缓存并发使用

**浏览器缓存**：主要是存在于浏览器端的缓存

**应用层缓存：**可以分为tomcat本地缓存，比如之前提到的map，或者是使用redis作为缓存

**数据库缓存：**在数据库中有一片空间是 buffer pool，增改查数据都会先加载到mysql的缓存中

**CPU缓存：**当代计算机最大的问题是 cpu性能提升了，但内存读写速度没有跟上，所以为了适应当下的情况，增加了cpu的L1，L2，L3级的缓存

##### 5.2.3 缓存的应用

可以将访问频次较高的数据缓存再Redis中，当用户下次访问时，业务并不会先去数据库中进行查找，而是现在Reids中查找数据，如果Redis中不存在用户需要的数据，再去数据库中查找，查找到后再保存到Redis中，如此可以提高系统的性能，节省了很多访问数据库的IO操作。

![1653322097736](assets/1653322097736.png)

##### 5.2.4 缓存更新策略

缓存更新是redis为了节约内存而设计出来的一个东西，主要是因为内存数据宝贵，当我们向redis插入太多数据，此时就可能会导致缓存中的数据过多，所以redis会对部分数据进行更新，或者把他叫为淘汰更合适。

**内存淘汰：**redis自动进行，当redis内存达到咱们设定的max-memery的时候，会自动触发淘汰机制，淘汰掉一些不重要的数据(可以自己设置策略方式)

**超时剔除：**当我们给redis设置了过期时间ttl之后，redis会将超时的数据进行删除，方便咱们继续使用缓存

**主动更新：**我们可以手动调用方法把缓存删掉，通常用于解决缓存和数据库不一致问题

##### 5.2.5 缓存不一致问题

由于我们的**缓存的数据源来自于数据库**,而数据库的**数据是会发生变化的**,因此,如果当数据库中**数据发生变化,而缓存却没有同步**,此时就会有**一致性问题存在**,其后果是:

用户使用缓存中的过时数据,就会产生类似多线程数据安全问题,从而影响业务,产品口碑等;怎么解决呢？有如下几种方案

Cache Aside Pattern 人工编码方式：缓存调用者在更新完数据库后再去更新缓存，也称之为双写方案

Read/Write Through Pattern : 由系统本身完成，数据库与缓存的问题交由系统本身去处理

Write Behind Caching Pattern ：调用者只操作缓存，其他线程去异步处理数据库，实现最终一致

**综合考虑使用方案一**

操作缓存和数据库时有三个问题需要考虑：

如果采用第一个方案，那么假设我们每次操作数据库后，都操作缓存，但是中间如果没有人查询，那么这个更新动作实际上只有最后一次生效，中间的更新动作意义并不大，我们可以把缓存删除，等待再次查询时，将缓存中的数据加载出来

* 删除缓存还是更新缓存？
    * 更新缓存：每次更新数据库都更新缓存，无效写操作较多
    * 删除缓存：更新数据库时让缓存失效，查询时再更新缓存

* 如何保证缓存与数据库的操作的同时成功或失败？
    * 单体系统，将缓存与数据库操作放在一个事务
    * 分布式系统，利用TCC等分布式事务方案

应该具体操作缓存还是操作数据库，我们应当是先操作数据库，再删除缓存，原因在于，如果你选择第一种方案，在两个线程并发来访问时，假设线程1先来，他先把缓存删了，此时线程2过来，他查询缓存数据并不存在，此时他写入缓存，当他写入缓存后，线程1再执行更新动作时，实际上写入的就是旧的数据，新的数据被旧数据覆盖了。

* 先操作缓存还是先操作数据库？
    * 先删除缓存，再操作数据库
    * 先操作数据库，再删除缓存

![1653323595206](assets/1653323595206.png)

确定了采用删除策略，来解决双写问题，当我们修改了数据之后，然后把缓存中的数据进行删除，查询时发现缓存中没有数据，则会从mysql中加载最新的数据，从而避免数据库和缓存不一致的问题

![image-20221123145422324](assets/image-20221123145422324.png)

#### 5.3 缓存中存在的问题

##### 5.3.1 缓存穿透

缓存穿透是指客户端请求的数据在缓存中和数据库中都不存在，这样缓存永远不会生效，这些请求都会打到数据库。

常见的解决方案有两种：

缓存空对象

优点：实现简单，维护方便

缺点：

* 额外的内存消耗
* 可能造成短期的不一致

布隆过滤

优点：内存占用较少，没有多余key

缺点：

* 实现复杂
* 存在误判可能

**缓存空对象思路分析：**当我们客户端访问不存在的数据时，先请求redis，但是此时redis中没有数据，此时会访问到数据库，但是数据库中也没有数据，这个数据穿透了缓存，直击数据库，我们都知道数据库能够承载的并发不如redis这么高，如果大量的请求同时过来访问这种不存在的数据，这些请求就都会访问到数据库，简单的解决方案就是哪怕这个数据在数据库中也不存在，我们也把这个数据存入到redis中去，这样，下次用户过来访问这个不存在的数据，那么在redis中也能找到这个数据就不会进入到缓存了

**布隆过滤：**布隆过滤器其实采用的是哈希思想来解决这个问题，通过一个庞大的二进制数组，走哈希思想去判断当前这个要查询的这个数据是否存在，如果布隆过滤器判断存在，则放行，这个请求会去访问redis，哪怕此时redis中的数据过期了，但是数据库中一定存在这个数据，在数据库中查询出来这个数据后，再将其放入到redis中，假设布隆过滤器判断这个数据不存在，则直接返回。

这种方式优点在于节约内存空间，存在误判，误判原因在于：布隆过滤器走的是哈希思想，只要哈希思想，就可能存在哈希冲突

**解决缓存穿透的问题**

在原来的逻辑中，如果发现这个数据在mysql中不存在，直接就返回404了，这样是会存在缓存穿透问题的

现在的逻辑中：如果这个数据不存在，我们不会返回404 ，还是会把这个数据写入到Redis中，并且将value设置为空，欧当再次发起查询时，我们如果发现命中之后，判断这个value是否是null，如果是null，则是之前写入的数据，证明是缓存穿透数据，如果不是，则直接返回数据。

缓存穿透的解决方案有哪些？

* 缓存null值
* 布隆过滤
* 增强id的复杂度，避免被猜测id规律
* 做好数据的基础格式校验
* 加强用户权限校验
* 做好热点参数的限流

##### 5.3.2 缓存雪崩

缓存雪崩是指在同一时段大量的缓存key同时失效或者Redis服务宕机，导致大量请求到达数据库，带来巨大压力。

解决方案：

* 给不同的Key的TTL添加随机值
* 利用Redis集群提高服务的可用性
* 给缓存业务添加降级限流策略
* 给业务添加多级缓存

![](assets/1653327884526.png)

##### 5.3.3 缓存击穿

缓存击穿问题也叫热点Key问题，就是一个被高并发访问并且缓存重建业务较复杂的key突然失效了，无数的请求访问会在瞬间给数据库带来巨大的冲击。

常见的解决方案有两种：

* 互斥锁
* 逻辑过期

逻辑分析：假设线程1在查询缓存之后，本来应该去查询数据库，然后把这个数据重新加载到缓存的，此时只要线程1走完这个逻辑，其他线程就都能从缓存中加载这些数据了，但是假设在线程1没有走完的时候，后续的线程2，线程3，线程4同时过来访问当前这个方法， 那么这些线程都不能从缓存中查询到数据，那么他们就会同一时刻来访问查询缓存，都没查到，接着同一时间去访问数据库，同时的去执行数据库代码，对数据库访问压力过大

![](assets/1653328022622.png)

解决方案一、使用锁来解决：

因为锁能实现互斥性。假设线程过来，只能一个人一个人的来访问数据库，从而避免对于数据库访问压力过大，但这也会影响查询的性能，因为此时会让查询的性能从并行变成了串行，我们可以采用tryLock方法 + double check来解决这样的问题。

假设现在线程1过来访问，他查询缓存没有命中，但是此时他获得到了锁的资源，那么线程1就会一个人去执行逻辑，假设现在线程2过来，线程2在执行过程中，并没有获得到锁，那么线程2就可以进行到休眠，直到线程1把锁释放后，线程2获得到锁，然后再来执行逻辑，此时就能够从缓存中拿到数据了。

![1653328288627](assets/1653328288627.png)

解决方案二、逻辑过期方案

方案分析：我们之所以会出现这个缓存击穿问题，主要原因是在于我们对key设置了过期时间，假设我们不设置过期时间，其实就不会有缓存击穿的问题，但是不设置过期时间，这样数据不就一直占用我们内存了吗，我们可以采用逻辑过期方案。

我们把过期时间设置在 redis的value中，注意：这个过期时间并不会直接作用于redis，而是我们后续通过逻辑去处理。假设线程1去查询缓存，然后从value中判断出来当前的数据已经过期了，此时线程1去获得互斥锁，那么其他线程会进行阻塞，获得了锁的线程他会开启一个 线程去进行 以前的重构数据的逻辑，直到新开的线程完成这个逻辑后，才释放锁， 而线程1直接进行返回，假设现在线程3过来访问，由于线程线程2持有着锁，所以线程3无法获得锁，线程3也直接返回数据，只有等到新开的线程2把重建数据构建完后，其他线程才能走返回正确的数据。

这种方案巧妙在于，异步的构建缓存，缺点在于在构建完缓存之前，返回的都是脏数据。

![1653328663897](assets/1653328663897.png)

**互斥锁方案：**由于保证了互斥性，所以数据一致，且实现简单，因为仅仅只需要加一把锁而已，也没其他的事情需要操心，所以没有额外的内存消耗，缺点在于有锁就有死锁问题的发生，且只能串行执行性能肯定受到影响

**逻辑过期方案：** 线程读取过程中不需要等待，性能好，有一个额外的线程持有锁去进行重构数据，但是在重构数据完成前，其他的线程只能返回之前的数据，且实现起来麻烦

![1653357522914](assets/1653357522914.png)

##### 5.3.4 互斥锁解决缓存击穿

核心思路：相较于原来从缓存中查询不到数据后直接查询数据库而言，现在的方案是进行查询之后，如果从缓存没有查询到数据，则进行互斥锁的获取，获取互斥锁后，判断是否获得到了锁，如果没有获得到，则休眠，过一会再进行尝试，直到获取到锁为止，才能进行查询

如果获取到了锁的线程，再去进行查询，查询后将数据写入redis，再释放锁，返回数据，利用互斥锁就能保证只有一个线程去执行操作数据库的逻辑，防止缓存击穿

![1653357860001](assets/1653357860001.png)

**操作锁的代码：**

核心思路就是利用redis的setnx方法来表示获取锁，该方法含义是redis中如果没有这个key，则插入成功，返回1，在stringRedisTemplate中返回true，  如果有这个key则插入失败，则返回0，在stringRedisTemplate返回false，我们可以通过true，或者是false，来表示是否有线程成功插入key，成功插入的key的线程我们认为他就是获得到锁的线程。

```java
private boolean tryLock(String key) {
    Boolean flag = stringRedisTemplate.opsForValue().setIfAbsent(key, "1", 10, TimeUnit.SECONDS);
    return BooleanUtil.isTrue(flag);
}

private void unlock(String key) {
    stringRedisTemplate.delete(key);
}
```

**操作代码：**

```java
 public Shop queryWithMutex(Long id)  {
        String key = CACHE_SHOP_KEY + id;
        // 1、从redis中查询商铺缓存
        String shopJson = stringRedisTemplate.opsForValue().get("key");
        // 2、判断是否存在
        if (StrUtil.isNotBlank(shopJson)) {
            // 存在,直接返回
            return JSONUtil.toBean(shopJson, Shop.class);
        }
        //判断命中的值是否是空值
        if (shopJson != null) {
            //返回一个错误信息
            return null;
        }
        // 4.实现缓存重构
        //4.1 获取互斥锁
        String lockKey = "lock:shop:" + id;
        Shop shop = null;
        try {
            boolean isLock = tryLock(lockKey);
            // 4.2 判断否获取成功
            if(!isLock){
                //4.3 失败，则休眠重试
                Thread.sleep(50);
                return queryWithMutex(id);
            }
            //4.4 成功，根据id查询数据库
             shop = getById(id);
            // 5.不存在，返回错误
            if(shop == null){
                 //将空值写入redis
                stringRedisTemplate.opsForValue().set(key,"",CACHE_NULL_TTL,TimeUnit.MINUTES);
                //返回错误信息
                return null;
            }
            //6.写入redis
            stringRedisTemplate.opsForValue().set(key,JSONUtil.toJsonStr(shop),CACHE_NULL_TTL,TimeUnit.MINUTES);

        }catch (Exception e){
            throw new RuntimeException(e);
        }
        finally {
            //7.释放互斥锁
            unlock(lockKey);
        }
        return shop;
    }
```

#####  5.3.5 逻辑过期解决缓存击穿

**需求：修改根据id查询商铺的业务，基于逻辑过期方式来解决缓存击穿问题**

思路分析：当用户开始查询redis时，判断是否命中，如果没有命中则直接返回空数据，不查询数据库，而一旦命中后，将value取出，判断value中的过期时间是否满足，如果没有过期，则直接返回redis中的数据，如果过期，则在开启独立线程后直接返回之前的数据，独立线程去重构数据，重构完成后释放互斥锁。

![1653360308731](assets/1653360308731.png)



如果封装数据：因为现在redis中存储的数据的value需要带上过期时间，此时要么你去修改原来的实体类，要么你

**步骤一、**

新建一个实体类，我们采用第二个方案，这个方案，对原来代码没有侵入性。

```
@Data
public class RedisData {
    private LocalDateTime expireTime;
    private Object data;
}
```

**步骤二、**

在**ShopServiceImpl** 新增此方法，利用单元测试进行缓存预热

![1653360807133](assets/1653360807133.png)

**在测试类中**

![1653360864839](assets/1653360864839.png)

步骤三：正式代码

**ShopServiceImpl**

```java
private static final ExecutorService CACHE_REBUILD_EXECUTOR = Executors.newFixedThreadPool(10);
public Shop queryWithLogicalExpire( Long id ) {
    String key = CACHE_SHOP_KEY + id;
    // 1.从redis查询商铺缓存
    String json = stringRedisTemplate.opsForValue().get(key);
    // 2.判断是否存在
    if (StrUtil.isBlank(json)) {
        // 3.存在，直接返回
        return null;
    }
    // 4.命中，需要先把json反序列化为对象
    RedisData redisData = JSONUtil.toBean(json, RedisData.class);
    Shop shop = JSONUtil.toBean((JSONObject) redisData.getData(), Shop.class);
    LocalDateTime expireTime = redisData.getExpireTime();
    // 5.判断是否过期
    if(expireTime.isAfter(LocalDateTime.now())) {
        // 5.1.未过期，直接返回店铺信息
        return shop;
    }
    // 5.2.已过期，需要缓存重建
    // 6.缓存重建
    // 6.1.获取互斥锁
    String lockKey = LOCK_SHOP_KEY + id;
    boolean isLock = tryLock(lockKey);
    // 6.2.判断是否获取锁成功
    if (isLock){
        CACHE_REBUILD_EXECUTOR.submit( ()->{

            try{
                //重建缓存
                this.saveShop2Redis(id,20L);
            }catch (Exception e){
                throw new RuntimeException(e);
            }finally {
                unlock(lockKey);
            }
        });
    }
    // 6.4.返回过期的商铺信息
    return shop;
}
```

### 
