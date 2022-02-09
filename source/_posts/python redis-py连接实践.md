---
layout: post
title: python redis-py连接实践
categories: python
date: 2022-02-09 19:43:11
tags:
    - python
    - redis
    - 连接
---
### 0x01 Redis四种模式

这里的模式主要是说的Redis Server的部署模式，有四种：单机模式、主从模式、哨兵模式、集群模式

这四种模式的详细介绍可以参考这篇文章：[https://juejin.cn/post/6844904191236767751](https://juejin.cn/post/6844904191236767751)

主从模式的Redis数据库，单点连接主节点。这种模式主要如果主节点挂掉，从库会切换为主节点，但是主节点IP已经变化，应用服务需要修改IP地址，重新发布。

建议哨兵模式，这种主要连接的是哨兵节点（一个或多个组成），访问redis集群的数据都是通过哨兵集群的，redis集群出现问题，应用服务不用更改访问地址

<!--more-->

### 0x02 连接池

#### ConnectionPool

redis-py 使用 connection pool 来管理对一个 redis server 的所有连接，避免每次建立、释放连接的开销。

默认，每个Redis客户端实例都会维护一个自己的连接池。可以直接建立一个连接池ConnectionPool，然后作为参数初始化Redis，这样就可以实现多个 Redis 实例共享一个连接池。

redis 取出的结果默认是字节，我们可以设定 decode_responses=True 改成字符串。

```python
import redis    # 导入redis 模块
 
pool = redis.ConnectionPool(host='localhost', port=6379, decode_responses=True)
r = redis.Redis(host='localhost', port=6379, decode_responses=True)
```

连接池大小默认是2 ** 31，可以通过设置max_connections来限制连接池大小

```python
import redis    # 导入redis 模块
 
pool = redis.ConnectionPool(host='localhost', port=6379, max_connections=40, decode_responses=True)  # 连接池大小设置为40
r = redis.Redis(host='localhost', port=6379, decode_responses=True)
```

因为Redis服务器设置的连接数是有限的，在大并发的情况下，可能会导致客户端连接数超过redis server设置的最大连接数，从而会报错

但是也不能设置太小，连接池设置太小会导致在大并发的时候没有可用连接而抛出异常，可以看源码中这两个函数：

```python
def get_connection(self, command_name, *keys, **options):
        "Get a connection from the pool"
        self._checkpid()
        with self._lock:
            try:
                connection = self._available_connections.pop()   # 当可用连接数为空时，pop会抛出IndexError的异常，如果这个时候连接数已达到最大链接数，在创建连接的时候会报错
            except IndexError:
                connection = self.make_connection()
            self._in_use_connections.add(connection)
 
        try:
            # ensure this connection is connected to Redis
            connection.connect()
            # connections that the pool provides should be ready to send
            # a command. if not, the connection was either returned to the
            # pool before all data has been read or the socket has been
            # closed. either way, reconnect and verify everything is good.
            try:
                if connection.can_read():
                    raise ConnectionError('Connection has data')
            except ConnectionError:
                connection.disconnect()
                connection.connect()
                if connection.can_read():
                    raise ConnectionError('Connection not ready')
        except BaseException:
            # release the connection back to the pool so that we don't
            # leak it
            self.release(connection)
            raise
 
        return connection
 
def make_connection(self):
        "Create a new connection"
        if self._created_connections >= self.max_connections:   # 在这块会抛出Too many connections的连接异常
            raise ConnectionError("Too many connections")
        self._created_connections += 1
        return self.connection_class(**self.connection_kwargs)
```

报错信息：
![连接数报错](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/blog_redis_py_01.png)

#### BlockingConnectionPool

BlockingConnectionPool和ConnectionPool功能是一样的，但是是线程安全的，在多线程的环境下，建议用BlockingConnectionPool，比如gunicorn使用了多线程的worker。同时，当所有连接都在使用时，获取可用连接不会抛异常，而是可以设置等待时间。

```python
# Block forever.
pool = BlockingConnectionPool(timeout=None)
 
# Raise a ``ConnectionError`` after five seconds if a connection is
# not available.
pool = BlockingConnectionPool(timeout=5)
```

### 0x03 timeout相关

在初始化连接的时候，可以设置一些超时时间。

**socket_timeout**

指Redis发出命令接收响应的时间不能超过此参数设置时间. 如果超过了此时间, 将会抛出异常:redis.exceptions.TimeoutError: Timeout reading from socket, 即读取响应超时。

建议设置这个时间，防止程序读取redis数据超时导致服务卡住，同时增加对这个的异常处理。

**socket_connect_timeout**

指Redis建立连接超时时间. 当设置此参数时, 如果在此时间内没有建立连接, 将会抛出异常redis.exceptions.TimeoutError: Timeout connecting to server。

socket_connect_timeout不设置时，这个值等于socket_timeout。

可以只设置socket_timeout

**retry_on_timeout**

Boolean类型，建议设置为True

当设置False时, 一个命令超时后, 将会直接抛出timeout异常。

当设置为True时, 命令超时后,将会重试一次, 重试成功则正常返回; 失败则抛出timeout异常。


### 0x04 Sentinel（哨兵）连接

Sentinel一般包含：Sentinel地址列表，master name，redis auth，分别填入下面

```python
from redis.sentinel import Sentinel
conf = {
    'sentinel': [('x.x.x.1', 26379), ('x.x.x.2', 26379), ('x.x.x.3', 26379)],   # Sentinel地址列表
    'master_group_name': 'mymaster',  # master name
    'connection_conf': {
        'password': 'xxxxx',   # redis auth
        'socket_timeout': 0.5,
        'retry_on_timeout': True,
        'socket_keepalive': True,
        'max_connections': 20,
        'encoding': 'utf8',
        'decode_responses': True,
        'client_name': 'sentinel_client'  # 客户端连接名称
    }
}
sentinel = Sentinel(conf['sentinel'], **conf['connection_conf'])
redis_cli_0 = sentinel.master_for(conf['master_group_name'], db=0)
redis_cli_1 = sentinel.master_for(conf['master_group_name'], db=1)
```

Sentinel连接方式和普通连接方式类似，master_for函数会返回一个Redis实例，但是sentinel的连接池不同，连接池类型是SentinelConnectionPool。

里面逻辑主要新增：

connect创建连接时，每次都会先去Sentinel查询一次master的地址。

master宕机发生主从切换时，有两种场景，第一种是原master宕机了，这时候这时候客户端无法连接到master, 会产生ConnectionError或者TimeoutError异常。第二种是原master会变成slave，这样就会返回ReadOnlyError异常，会被转换为ConnectionError。

无论哪种情况，都会在execute_command里调用Connection里的disconnect方法。

这样在下次再使用这个连接时，因为连接断开了，就会再次调用connect创建连接，而connect调用connection_pool的get_master_address方法，这里调用get_master_address获取master的地址，而且都是实时获取的，如果发现master地址变了，就会断开所有的连接，重新连接。

redis-py的Sentinel维持master的地址的方式是每次创建连接时都会去动态获取一次master的地址，而不是每次查询时都去获取一次master。不然的话，每一次请求都实际需要两次请求，吞吐量就下降了不少。而检查到master的异常后，会断开所有连接，然后重连

### 0x05 使用建议

单py文件，无需对redis-py进一步封装，直接用原生的就好，因为本身有连接池，可以直接在py文件中创建对应db的连接，比如使用db 0，创建一个redis_cli_0，其他地方需要用到直接导入即可，单例模式。

### 0x06 相关Redis命令

**看Redis当前连接数**

```bash
127.0.0.1:6379> info clients
# Clients
connected_clients: 2  # 已连接客户端数
client_recent_max_input_buffer: 2
client_recent_max_output_buffer:0
```

**查看具体连接信息**

![具体连接信息](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/blog_redis_py_02.png)

具体含义可参考：[https://www.runoob.com/redis/server-client-list.html](https://www.runoob.com/redis/server-client-list.html)

这里的name是指客户端连接的名称，我们可以再创建连接的时候通过client_name设置，低版本不支持这个参数

**关闭客户端连接**

```bash
client kill ip:port
```
![关闭客户端连接](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/blog_redis_py_03.png)

**查看Redis最大连接数**

```bash
127.0.0.1:6379> config get maxclients
1) "maxclients"
2) "10000"
```

**查看空闲连接多少时间会关闭**

默认为0，指redis server永远不会关闭空闲连接

```bash
127.0.0.1:6379> config get timeout
1) "timeout"
2) "0"
```

### 0x07 参考资料

[https://juejin.cn/post/6844904191236767751](https://juejin.cn/post/6844904191236767751)

[https://www.runoob.com/redis/redis-tutorial.html](https://www.runoob.com/redis/redis-tutorial.html)

[https://huangzhw.github.io/2019/03/23/how-redis-py-sentinel-work/](https://huangzhw.github.io/2019/03/23/how-redis-py-sentinel-work/)