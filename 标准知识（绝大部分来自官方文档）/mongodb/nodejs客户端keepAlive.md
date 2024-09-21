# nodejs客户端keepAlive

==来源，官网：<https://www.mongodb.com/docs/drivers/node/current/faq/#what-does-the-keepalive-option-do->==

The `keepAlive` connection option specifies whether to enable Transmission Control Protocol (TCP) keepalives on a TCP socket. If you enable keepalives, the driver checks whether the connection is active by sending periodic pings to your MongoDB deployment. This functionality only works if your operating system supports the SO_KEEPALIVE socket option.

`keepAlive`连接选项制定了是否开启TCP的keepalive。如果开启，驱动会检测连接是否活跃，通过定期向MongoDB发送ping。这个功能需要操作系统支持`SO_KEEPALIVE`套接字选项。

The `keepAliveInitialDelay` option specifies the number of milliseconds that the driver waits before initiating a keepalive.

`keepAliveInitialDelay`选项制定了驱动在初始化keepalive之前等待的时间，单位毫秒。

The 5.3 driver version release deprecated these options. Starting in version 6.0 of the driver, the keepAlive option is permanently set to true, and the keepAliveInitialDelay is set to 300000 milliseconds (300 seconds).

从5.3开始，这个选项被废弃，从6.0的驱动开始，`keepAlive`选项被永久设置为true，`keepAliveInitialDelay`被设置为300000毫秒（300秒）。

>**Warning**
>If your firewall ignores or drops the keepalive messages, you might not be able to identify dropped connections.
>如果防火墙忽略了keepalive消息，你将无法识别断开的连接。
