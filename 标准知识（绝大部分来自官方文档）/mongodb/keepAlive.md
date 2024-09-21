# keepAlive

==(来源官网：<https://www.mongodb.com/zh-cn/docs/v5.0/faq/diagnostics/>)==

If you experience network timeouts or socket errors in communication between clients and servers, or between members of a sharded cluster or replica set, check the TCP keepalive value for the affected systems.

如果客户端和服务器之间、群集中的成员之间或群集中的成员之间存在网络超时或套接字错误，请检查受影响系统的 `TCP keepalive` 值。

Many operating systems set this value to 7200 seconds (two hours) by default. For MongoDB, you will generally experience better results with a shorter keepalive value, on the order of 120 seconds (two minutes).

==很多操作系统默认将该值设置为 7200 秒（两小时）。对于 MongoDB，使用较短的 keepalive 值（大约为 120 秒（两分钟））通常会获得更好的结果。==

If your MongoDB deployment experiences keepalive-related issues, you must alter the keepalive value on all affected systems. This includes all machines running mongod or mongos processes and all machines hosting client processes that connect to MongoDB.

==如果您的 MongoDB 部署遇到与 keepalive 相关的问题，必须更改所有受影响系统上的 keepalive 值。这包括运行 mongod 或 mongos 进程的所有机器以及所有持有客户端进程（连接到MongoDB）的机器。==

**Adjusting the TCP keepalive value:**

linux:

To view the keepalive setting on Linux, use one of the following commands:

在linux上查看keepalive的设置，使用下面的命令：

```shell
sysctl net.ipv4.tcp_keepalive_time
```

Or:

```shell
cat /proc/sys/net/ipv4/tcp_keepalive_time
```

The value is measured in seconds.

这些值的单位是秒

Although the setting name includes ipv4, the tcp_keepalive_time value applies to both IPv4 and IPv6.

尽管设置的名字为ipv4，但是该值适用于ipv4和ipv6。

To change the tcp_keepalive_time value, you can use one of the following commands, supplying a <value> in seconds:

更改tcp_keepalive_time的值，使用下面的命令，并提供一个以秒为单位的<value>：

```shell
sudo sysctl -w net.ipv4.tcp_keepalive_time=<value>
```

Or:

```shell
echo <value> | sudo tee /proc/sys/net/ipv4/tcp_keepalive_time
```

These operations do not persist across system reboots. To persist the setting, add the following line `to /etc/sysctl.conf`, supplying a `<value>` in seconds, and reboot the machine:

这些相关操作在系统重启时不会一直存在。为了让其一直有效，添加下面的行到`/etc/sysctl.conf`，并提供一个以秒为单位的`<value>`，然后重启机器：

```shell
net.ipv4.tcp_keepalive_time = <value>
```

Keepalive values greater than 300 seconds, (5 minutes) will be overridden on mongod and mongos sockets and set to 300 seconds.

==Keepalive值小于300秒，在mongod和mongos的sockets中将会被重置为300秒。==

You will need to restart mongod and mongos processes for new system-wide keepalive settings to take effect.

==你需要重启mongod和mongos进程，对于新的系统范围的keepalive设置才会生效。==

==(【非文档内容，自己总结】:pill:docker中设置`net.ipv4.tcp_keepalive_time`无效，需要通过docker run命令的`--sysctl`参数设置，例如：`--sysctl net.ipv4.tcp_keepalive_time=600`)==
