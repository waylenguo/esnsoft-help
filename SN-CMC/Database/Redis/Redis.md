# Redis

作者：朱郁琼

## 简介

Redis 是一个高性能的key-value数据库。

Redis 与其他key-value缓存产品相比有以的特性：

- Redis支持数据的持久化，可以将内存中的数据保存在磁盘中，重启的时候可以再次加载进行使用。
- Redis不仅仅支持简单的key-value类型的数据，同时还提供list，set，zset，hash等数据结构的存储。
- Redis支持数据的备份，即master-slave模式的数据备份。
- 性能极高。Redis读的速度是110000次/s,写的速度是81000次/s 。
- Redis的所有操作都是原子性的，意思就是要么成功执行要么失败完全不执行。单个操作是原子性的。多个操作也支持事务，即原子性，通过MULTI和EXEC指令包起来。
- Redis还支持 publish/subscribe, 通知, key 过期等特性。

## 安装

Redis官方仅提供了linux版本的Redis，官方网站为 https://redis.io/download 。
windows系统可从https://github.com/MicrosoftArchive/redis/releases 下载。
下载完成后，运行redis-server.exe 就可以启动一个redis服务。
运行  redis-cli.exe 就可以直接启动一个redis 客户端连接本机的redis服务。

## 数据类型

### String

String类型是Redis最基本的数据类型，也可以包含任何二进制数据。比如jpg图片或者序列化的对象 。
对于String类型的value，一个键最大能存储512MB。

### List

List是简单的字符串列表，按照插入顺序排序。你可以添加一个元素到列表的头部（左边）或者尾部（右边）。

### Hash

Hash 是一个String类型的键值(key=>value)对集合。

### Set

Set是String类型的无序集合。

### Sorted set

Sorted set 是String类型的有序集合。

## 常用命令

Redis命令速查表可参考官方文档： https://redis.io/commands

### Key相关

keys命令会阻塞线程，可能会造成Redis服务卡顿，导致其余指令可能会延时甚至超时报错，所以线上禁止使用。

- DEL key [key...]
删除 key和key存储的内容
例子：
```
redis> SET key1 "Hello"
"OK"
redis> SET key2 "World"
"OK"
redis> DEL key1 key2 key3
(integer) 2
redis>
```

- EXISTS key [key...]
是否存在key
例子：
```
redis> SET key1 "Hello"
"OK"
redis> EXISTS key1
(integer) 1
```
- KEYS pattern
找到匹配pattern的所有key，支持通配。只支持以下3种匹配方式。

例子：

```
redis> KEYS a??     // ?匹配单个字符

redis> KEYS *   //*匹配任意数量的任意字符

redis> KEYS [a-z]    //[]匹配指定字符

```

- SCAN cursor [MATCH pattern] \[COUNT count]
patten表达式格式同keys命令。
在线上可使用SCAN命令替代KEYS命令来查找数据。


例子：

```
redis> SCAN 0 match Login.* count 100
1) "0"
2) 1) "Login.20190312"
    2) "Login.20190313"

```
<span style="color:red;">注：管控中心的Redis工具，使用的是SCAN命令，从0条起始查询全部。</span>


### String相关

- SET key value
设置 key的字符串值。
例子：
```
redis> SET mykey "Hello"
"OK"
```

- GET key
取key的字符串值。
例子：

```
redis> GET mykey
"Hello"

```

- INCR key
将key 存储的值加 1

- INCRBY key increment
将key 存储的值加 increment

- DECR key
将key 存储的值减 1

- DECRBY key decrement
将key 存储的值减 increment

## 配置

底层通过Spring支持注入了两个 Redis客户端对象。一个用于应用层开发，一个用于分码缓存。
在开发环境中，公共配置在 config配置工程中 'core.inf'文件中定义，例如snadk-config中的定义：

单实例
```
#应用实例
snsoft.redis=true #启用redis
snsoft.redis.host=127.0.0.1 #非集群 redis 服务ip
snsoft.redis.port=6379 # 非集群 redis 端口

#分码缓存
snsoft.mc.redis=true
snsoft.mc.redis.host=10.8.6.1
snsoft.mc.redis.port=6379

```
集群环境下 Redis配置如下：
```
#集群
snsoft.redis=true #启用redis
snsoft.redis.cluster.nodes=127.0.0.1:7000,127.0.0.1:7001,127.0.0.1:7002,127.0.0.1:7003,127.0.0.1:7004,127.0.0.1:7005  #集群节点

snsoft.mc.redis=true
snsoft.mc.redis.cluster.nodes=10.8.6.1:7000,10.8.6.1:7001

```
在生产环境中，如上Redis 配置需要在'System.properties'文件中设置。

## Redis开发

### RedisCacheFactory

当开发人员需要使用Redis做一些缓存操作的事情，例如 计数、缓存队列等，可以使用底层封装的 RedisCacheFactory 对象，不需要直接使用 RedisTemplate实例。
此时，代码中注入名为 "SN-CORE.CacheFactory" 的实例，通过工厂方法即可获得响应的缓存服务。

例如计数器的示例：
```
CounterCache counter = factory.getCounterCache("Service.InvokeCounter");
counter.incr();

```
### RedisTemplate
开发人员有需求使用多个服务进行消息通知/订阅时，除了使用JMS以外，
还可以使用Redis的 Pub/Sub功能，此时应当注入名为"redisTemplate"的RedisTemplate实例。

```
1.实现消息订阅监听
public class SampleMessageListener implements MessageListener
{
    private final RedisTemplate<String,?>	redisTemplate;
	@Override
	public void onMessage(Message message, byte[] pattern)
	{
	    Object o = redisTemplate.getValueSerializer().deserialize(message.getBody());
	    ...//处理你获得订阅的消息的业务逻辑
	}
}

2.消息订阅
    RedisMessageListenerContainer c = new RedisMessageListenerContainer();
    c.setConnectionFactory(redisTemplate.getConnectionFactory());
    c.addMessageListener(new SampleMessageListener(redisTemplate), new ChannelTopic("SampleTopic_1"));
    //Topic分为 ChannelTopic和 PatternTopic。ChannelTopic按指定的字符串精确匹配推送，PatternTopic按pattern通配。
    //同一个监听支持订阅多个Topic。

3.消息发布
    redisTemplate.convertAndSend("SampleTopic_1", messageBody);
    //messageBody 可序列化的对象.

```

