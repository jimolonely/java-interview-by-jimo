## 为什么使用redis？redis有哪些好处？

1. 快
2. 丰富的数据结构
3. 可灵活扩展，使用Lua脚本
4. 具备队列、集群、可持久化等特性

## 为什么redis这么快？

https://mp.weixin.qq.com/s?__biz=MzkzMDI1NjcyOQ==&mid=2247487752&idx=1&sn=72a1725e1c86bb5e883dd8444e5bd6c4&chksm=c27c533ef50bda288417c31f5210bb16a70361b2e2c344dfffdb079b54b241cd6c62202d9775&scene=21#wechat_redirect

可以从以下几个方面说：
1. 基于内存：要知道，内存速度可比硬盘快千倍；
2. 采用单线程模式：线程切换很耗资源，单线程下的瓶颈不在CPU，而在于网络和内存；
3. 优秀的数据结构：SDS，ziplist，跳表，hash等
4. 采用IO多路复用

## redis可用在哪些场景？

每种数据结构都可以用在一些场景：

* String：缓存、计数器、分布式锁等。
* List：链表、队列、微博关注人时间轴列表等。
* Hash：用户信息、Hash 表等。
* Set：去重、赞、踩、共同好友等。
* Zset：访问量排行榜、点击量排行榜等。

## 关于redis分布式锁怎么使用？以及其存在的很多问题？

https://xiaomi-info.github.io/2019/12/17/redis-distributed-lock/

1. setnx+expire：非原子性
2. Lua或者采用 set ex px nx：还存在时间到了业务没执行完，锁误消除问题
3. 不可重入问题
4. 不可等待锁，需要轮询
5. redis集群问题：集群主备切换和脑裂问题

## 你知道有哪些实现分布式锁的方式吗？

https://mp.weixin.qq.com/s/hoZB0wdwXfG3ECKlzjtPdw

* MySQL
* ZK
* Redis

## java使用lua执行redis命令的方式？
https://www.jianshu.com/p/b35637b03f93

```java
Jedis jedis = RedisManager.getJedis();
String lua = "local num=redis.call('incr',KEYS[1])\n" +
        "if tonumber(num)==1 then\n" +
        "\tredis.call('expire',KEYS[1],ARGV[1])\n" +
        "\treturn 1\n" +
        "elseif tonumber(num)>tonumber(ARGV[2]) then\n" +
        "\treturn 0\n" +
        "else \n" +
        "\treturn 1\n" +
        "end";
List<String> keys = new ArrayList<>();
keys.add("ip:limit:127.0.0.1");
List<String> arggs = new ArrayList<>();
arggs.add("6000");
arggs.add("10");
Object obj = jedis.eval(lua,keys,arggs);
System.out.println(obj);
```

## 对lua脚本了解多少？
是一款轻量级语言，由C开发，非常小巧才一百多k，可以很容易嵌入C/C++系统里实现扩展功能。比如游戏升级，数据库扩展插件（mysql workbench）。

## 如何用zset实现排行榜？

https://duyanghao.github.io/redis-rank/

1. 增加用户分数：`zadd 榜名 用户名 分数`
2. 获取排行榜：`zrevrange 榜名 start end withScores`
3. 删除用户：`zrem`

这里要考虑排行榜刷新问题：

1. 全量更新
2. 批量更新：有变化的才更

为了防止缓存击穿，可以考虑不设置缓存时间，或者查数据库时加锁。

## redis主从、从从同步的过程了解吗？
redis不仅可以主从同步，还可以从从同步。
参考文章：https://segmentfault.com/a/1190000039242024

通过配置`replicaof/slaveof`可以设置主从复制，具体的复制可以分为全量复制、部分复制和命令传播，全量复制使用RDB文件，部分复制会记录偏移量，使用缓冲区，命令传播不仅传输写命令，还有心跳机制。

## 为什么redis6.0引入多线程？又为什么之前是单线程？

https://segmentfault.com/a/1190000040376111

之前是单线程是因为`CPU`并不是`redis`的瓶颈，内存和网络才是。而且单线程对于实现来说更容易，不需要考虑并发锁问题，线程切换问题。
而现在随着多核技术的发展，为了更充分利用硬件优势，才出了多线程。不过，6.0仅限于网络请求的多线程，命令处理依然是单线程。
而且多线程默认是没开启的，看起来更像是一个过度版本。

## redis缓存过期删除策略有哪些？内存淘汰策略有哪些？
https://cloud.tencent.com/developer/article/1643921

过期策略：指带有过期时间的键的清除

1. 定时过期
2. 惰性过期
3. 定期过期

淘汰策略：指在内存达到最大值之后如何删除内存中key的策略

LRU，random，不删除，ttl优先

## redis持久化策略有什么？
AOF和RDB

RDB：Redis Database
* 将数据以二进制形式定期落盘，当然是异步的，不影响主进程。

AOF：Append only File
* 开启后，会将命令以追加的方式写到文件里。并且有3种保存方式(save和flush是不同的操作)：
    1. 不保存
    2. 每秒保存
    3. 每条命令都保存

总结
* 相对来说，AOF的文件会大一些，所以可以通过AOF重写来让文件变小。
* RDB因为是定期，可能会丢数据多一些。
* 总体来说，这俩结合起来用最好，因为AOF数据更全，优先会使用AOF来恢复数据。


## redis如何实现延时队列？
使用`zset`，时间戳作为分数，通过`zrangebyscore`查询指定区间的时间段数据。

## redis发布订阅模式的缺点？Stream呢？

1. 无法持久化
2. 没有`ACK`机制，也无法保证消息连续

从5.0开始，redis有了`stream`，更像个消息队列了，轻量级的。

## 对redis事务的理解？

* redis事务是将一系列命令打包给redis执行，除非命令有错，否则就会全部执行，不受其他命令打扰。
* redis事务不支持回滚，为什么呢？可能是实现成本和价值不匹配。

事务通过以下命令实现：
```shell
multi
...
exec/discard
```

## 有哪些redis集群模式？
https://xie.infoq.cn/article/f9710e0d2b9c27114005e81b0

1. 主从复制
2. 哨兵模式
3. 官方`redis cluster`

* 哨兵模式着眼于高可用，当`master`挂掉时，让一个`slave`提升为`master`。
* `redis cluster`着眼于扩展性，将数据分片存储。


## redis cluster的原理是什么？

1. 数据分散存储，按照16384个`slot`划分（通过`Key`的`CRC`后做`hash`）；
2. 节点间相互连接

当其中节点挂了，他负责的槽位数据就不可用了。

## redis cluster down的原因？

一般是节点挂了后，槽点数据缺失，可以通过
* `redis-cli —cluster fix` 修复

https://juejin.cn/post/6971713870616330254

## redis的管道和事务的区别？

https://juejin.cn/post/6926414112788316174

* 管道是将一批命令发给服务器执行，可以节省多次网络往返带宽RTT。但是多个客户端都使用管道执行多条命令时并不能保证每个客户端的原子性，虽然可以保证其执行顺序。
* 而事务能做到原子性。
* 同样可以做到原子性的是Lua脚本。

## jedis,lettuce,redisson有什么区别？

* jedis模拟了redis原生命令的方式，单机和集群版要分开创建。
* lettuce具有异步调用方式，底层采用`netty`，`RedisTemplate`底层默认用，上手难度高一些；
* redisson是官方推荐，其关注隔离redis底层信息，提供更高级的使用方式，封装了很多数据结构，方便业务集成。

## 缓存穿透，缓存击穿（失效），缓存雪崩的区别和应对方案是什么？

https://mp.weixin.qq.com/s/PZWe5x9cOqrhVCSxOBdVNA



## 布隆过滤器理论与实践？
https://juejin.cn/post/6844904007790673933





