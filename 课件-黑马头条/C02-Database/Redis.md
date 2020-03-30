# Redis

## 1 Redis事务

#### 基本事务指令

Redis提供了一定的事务支持，可以保证一组操作原子执行不被打断，但是如果执行中出现错误，事务不能回滚，Redis未提供回滚支持。

* `multi` 开启事务
* `exec` 执行事务

```
127.0.0.1:6379> multi
OK
127.0.0.1:6379> set a 100
QUEUED
127.0.0.1:6379> set b 200
QUEUED
127.0.0.1:6379> get a
QUEUED
127.0.0.1:6379> get b
QUEUED
127.0.0.1:6379> exec
1) OK
2) OK
3) "100"
4) "200"
```
使用multi开启事务后，操作的指令并未立即执行，而是被redis记录在队列中，等待一起执行。当执行exec命令后，开始执行事务指令，最终得到每条指令的结果。

```
127.0.0.1:6379> multi
OK
127.0.0.1:6379> set c 300
QUEUED
127.0.0.1:6379> hgetall a
QUEUED
127.0.0.1:6379> set d 400
QUEUED
127.0.0.1:6379> get d
QUEUED
127.0.0.1:6379> exec
1) OK
2) (error) WRONGTYPE Operation against a key holding the wrong kind of value
3) OK
4) "400"
127.0.0.1:6379>
```

如果事务中出现了错误，事务并不会终止执行，而是只会记录下这条错误的信息，并继续执行后面的指令。所以事务中出错不会影响后续指令的执行。

#### Python客户端操作

在Redis的Python 客户端库redis-py中，提供了pipeline (称为流水线 或 管道)，该工具的作用是：

* 在客户端统一收集操作指令
* 补充上multi和exec指令，当作一个事务发送到redis服务器执行

```python
from redis import StrictRedis
r = StrictRedis.from_url('redis://127.0.0.1:6381/0')
pl = r.pipeline()
pl.set('a', 100)
pl.set('b', 200)
pl.get('a')
pl.get('b')
ret = pl.execute()
print(ret) #  [True, True, b'100', b'200']
```

#### watch监视

若在构建的redis事务在执行时依赖某些值，可以使用watch对数据值进行监视。

```
127.0.0.1:6379> set stock 100
OK
127.0.0.1:6379> watch stock
OK
127.0.0.1:6379> multi
OK
127.0.0.1:6379> incrby stock -1
QUEUED
127.0.0.1:6379> incr sales
QUEUED
127.0.0.1:6379> exec
1) (integer) 99
2) (integer) 1
```

事务exec执行前被监视的stock值未变化，事务正确执行。

```
127.0.0.1:6379> set stock 100
OK
127.0.0.1:6379> watch stock
OK
127.0.0.1:6379> multi
OK
127.0.0.1:6379> incrby stock -1
QUEUED
127.0.0.1:6379> incr sales
QUEUED
```

此时在另一个客户端修改stock的值，执行

```
127.0.0.1:6379> incrby stock -2
(integer) 98
```

当第一个客户端再执行exec时

```
127.0.0.1:6379> exec
(nil)
```

表明事务需要监视的stock值发生了变化，事务不能执行了。

**注意：Redis Cluster 集群不支持事务**

## 2 Redis持久化

redis可以将数据写入到磁盘中，在停机或宕机后，再次启动redis时，将磁盘中的备份数据加载到内存中恢复使用。这是redis的持久化。持久化有如下两种机制。

#### RDB 快照持久化

redis可以将内存中的数据写入磁盘进行持久化。在进行持久化时，redis会创建子进程来执行。

**redis默认开启了快照持久化机制。**

进行快照持久化的时机如下

- 定期触发

    redis的配置文件

    ```shell
    #   save <seconds> <changes>
    #
    #   Will save the DB if both the given number of seconds and the given
    #   number of write operations against the DB occurred.
    #
    #   In the example below the behaviour will be to save:
    #   after 900 sec (15 min) if at least 1 key changed
    #   after 300 sec (5 min) if at least 10 keys changed
    #   after 60 sec if at least 10000 keys changed
    #
    #   Note: you can disable saving completely by commenting out all "save" lines.
    #
    #   It is also possible to remove all the previously configured save
    #   points by adding a save directive with a single empty string argument
    #   like in the following example:
    #
    #   save ""
    
    save 900 1
    save 300 10
    save 60 10000
    ```

- BGSAVE 

    执行`BGSAVE`命令，手动触发RDB持久化

- SHUTDOWN

    关闭redis时触发

#### AOF 追加文件持久化

redis可以将执行的所有指令追加记录到文件中持久化存储，这是redis的另一种持久化机制。

**redis默认未开启AOF机制。**

redis可以通过配置如下项开启AOF机制

```shell
appendonly yes  # 是否开启AOF
appendfilename "appendonly.aof"  # AOF文件
```

AOF机制记录操作的时机

```shell
# appendfsync always  # 每个操作都写到磁盘中
appendfsync everysec  # 每秒写一次磁盘，默认
# appendfsync no  # 由操作系统决定写入磁盘的时机
```

使用AOF机制的缺点是随着时间的流逝，AOF文件会变得很大。但redis可以压缩AOF文件。

#### 结合使用

redis允许我们同时使用两种机制，通常情况下我们会设置AOF机制为everysec 每秒写入，则最坏仅会丢失一秒内的数据。

## 3 Redis高可用

为了保证redis最大程度上能够使用，redis提供了主从同步+Sentinel哨兵机制。

#### Sentinel 哨兵

<https://redis.io/topics/sentinel>

redis提供的哨兵是用来看护redis实例进程的，可以自动进行故障转移，其功能如下：

- **Monitoring**. Sentinel constantly checks if your master and slave instances are working as expected.
- **Notification**. Sentinel can notify the system administrator, another computer programs, via an API, that something is wrong with one of the monitored Redis instances.
- **Automatic failover**. If a master is not working as expected, Sentinel can start a failover process where a slave is promoted to master, the other additional slaves are reconfigured to use the new master, and the applications using the Redis server informed about the new address to use when connecting.
- **Configuration provider**. Sentinel acts as a source of authority for clients service discovery: clients connect to Sentinels in order to ask for the address of the current Redis master responsible for a given service. If a failover occurs, Sentinels will report the new address

在redis安装后，会自带sentinel哨兵程序，修改sentinel.conf配置文件

```shell
bind 127.0.0.1
port 26380
daemonize yes
logfile /var/log/redis-sentinel.log
sentinel monitor mymaster 127.0.0.1 6380 2
sentinel down-after-milliseconds mymaster 30000
sentinel parallel-syncs mymaster 1
sentinel failover-timeout mymaster 180000
```

* sentinel monitor mymaster 127.0.0.1 6380 2 说明
    * mymaster 为sentinel监护的redis主从集群起名
    * 127.0.0.1 6300 为主从中任一台机器地址
    * 2 表示有两台以的sentinel认为某一台redis宕机后，才会进行自动故障转移。

启动方式：

```shell
redis-sentinel sentinel.conf
```

#### 高可用方案注意事项

- 至少三个sentinel以上 
- sentinel要分散运行在不同的机器上

#### Python客户端使用

```python
# redis 哨兵
REDIS_SENTINELS = [
    ('127.0.0.1', '26380'),
    ('127.0.0.1', '26381'),
    ('127.0.0.1', '26382'),
]
REDIS_SENTINEL_SERVICE_NAME = 'mymaster'

from redis.sentinel import Sentinel
_sentinel = Sentinel(REDIS_SENTINELS)
redis_master = _sentinel.master_for(REDIS_SENTINEL_SERVICE_NAME)
redis_slave = _sentinel.slave_for(REDIS_SENTINEL_SERVICE_NAME)
```

使用示例

```python
# 读数据，master读不到去slave读
try:
    real_code = redis_master.get(key)
except ConnectionError as e:
    real_code = redis_slave.get(key)

# 写数据，只能在master里写
try:
    current_app.redis_master.delete(key)
except ConnectionError as e:
    logger.error(e)
```

## 4 Redis集群

<https://redis.io/topics/partitioning>

Reids Cluster集群方案，内部已经集成了sentinel机制来做到高可用。

#### Python客户端

```python
# redis 集群
REDIS_CLUSTER = [
    {'host': '127.0.0.1', 'port': '7000'},
    {'host': '127.0.0.1', 'port': '7001'},
    {'host': '127.0.0.1', 'port': '7002'},
]

from rediscluster import StrictRedisCluster
redis_cluster = StrictRedisCluster(startup_nodes=REDIS_CLUSTER)

# 可以将redis_cluster就当作普通的redis客户端使用
redis_master.delete(key)
```

#### 注意：

* redis cluster 不支持事务
* redis cluster 不支持多键操作，如mset


## 5 用途

- 缓存
- 持久存储
    - 数据库的统计冗余字段 放到 redis中保存

## 6  相关补充阅读

- <https://redis.io/documentation>
- 《Redis实践》 （Redis in action)