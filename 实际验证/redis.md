# redis

**redis，服务端默认配置默认`timeout 0`，代表服务端不会主动断开连接。**

---

**redis，服务端默认`tcp-keepalive "300"`，代表每300秒会发送一个keepalive包**

redis 6.2 默认配置文档<https://raw.githubusercontent.com/redis/redis/6.2/redis.conf>

下面是文档中关于该值的解释：

```shell
# TCP keepalive.
#
# If non-zero, use SO_KEEPALIVE to send TCP ACKs to clients in absence
# of communication. This is useful for two reasons:
# 如果该值非0，在连接空闲时，使用SO_KEEPALIVE发送TCP ACKs到客户端，下面2种情况非常有用：
#
# 1) Detect dead peers.
# 2) Force network equipment in the middle to consider the connection to be
#    alive.
# 1) 检测死对等体
# 2）强制网络设备中间设备认为连接是存活的
#
# On Linux, the specified value (in seconds) is the period used to send ACKs.
# Note that to close the connection the double of the time is needed.
# On other kernels the period depends on the kernel configuration.
# 在Linux中，该值（以秒为单位）是发送ACK的周期。
# 注意，要关闭连接，需要双倍的时间。
# 在其他内核中，该周期取决于内核配置。
#
# A reasonable value for this option is 300 seconds, which is the new
# Redis default starting with Redis 3.2.1.
# 这个选项的合理值是300秒，这是从Redis 3.2.1开始使用的新值
```

---

**redis 最大连接10000默认`# maxclients 10000`，但是如果process file limit(指的是操作系统对单个进程可以打开的文件描述符数量的限制，在 Unix-like 系统中，几乎所有东西都被视为文件，包括网络套接字)设置的比较小，那么以该值为准，该值具体查询方法`ulimit -n`**

```shell
################################### CLIENTS ####################################

# Set the max number of connected clients at the same time. By default
# this limit is set to 10000 clients, however if the Redis server is not
# able to configure the process file limit to allow for the specified limit
# the max number of allowed clients is set to the current file limit
# minus 32 (as Redis reserves a few file descriptors for internal uses).
#
# Once the limit is reached Redis will close all the new connections sending
# an error 'max number of clients reached'.
#
# IMPORTANT: When Redis Cluster is used, the max number of connections is also
# shared with the cluster bus: every node in the cluster will use two
# connections, one incoming and another outgoing. It is important to size the
# limit accordingly in case of very large clusters.
#
# maxclients 10000

# 设置同时连接的最大客户端数量。默认情况下，
# 这个限制设置为10000个客户端。但是，如果Redis服务器无法
# 配置进程文件限制以允许指定的限制值，
# 则允许的最大客户端数量将被设置为当前文件限制
# 减去32（因为Redis为内部使用保留了一些文件描述符）。
#
# 一旦达到限制，Redis将关闭所有新的连接，
# 并发送一个错误消息"已达到最大客户端数量"。
#
# 重要提示：当使用Redis集群时，最大连接数也
# 与集群总线共享：集群中的每个节点将使用两个
# 连接，一个用于入站，另一个用于出站。在非常大的集群情况下，
# 相应地调整这个限制很重要。
#
# maxclients 10000
```

来源，官方文档默认配置：<https://raw.githubusercontent.com/redis/redis/6.2/redis.conf>
