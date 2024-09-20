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
