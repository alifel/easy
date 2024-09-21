# server端 connection pool

==官方文档：<https://www.mongodb.com/docs/v5.0/administration/connection-pool-overview/#connection-pool-overview>==

This document describes how to use a connection pool to manage connections between applications and MongoDB instances.

这个文档描述了如何使用连接池去管理应用程序和MongoDb实例之间的连接。

## What is a Connection Pool?

### Definition

A connection pool is a cache of open, ready-to-use database connections maintained by the [driver](https://www.mongodb.com/docs/drivers/). Your application can seamlessly get connections from the pool, perform operations, and return connections back to the pool. Connection pools are thread-safe.

连接池是一组打开的并且可用的数据库连接的缓存，被[驱动程序](https://www.mongodb.com/docs/drivers/)维护。您的应用程序可以无缝地从池中获取连接，执行操作，并将连接返回到池中。连接池是线程安全的。

### Benefits of a Connection Pool

A connection pool helps reduce application latency and the number of times new connections are created.

连接池帮我降低应用程序的延迟和新建连接所需要的时间。

A connection pool creates connections at startup. Applications do not need to manually return connections to the pool. Instead, connections return to the pool automatically.

连接池在启动时创建。应用程序不需要手动将连接返回给池。相反，连接会自动返回给池。

Some connections are active and some are inactive but available. If your application requests a connection and there’s an available connection in the pool, a new connection does not need to be created.

一些连接时活跃的，另外一些是不活跃的，但是可用。如果你的应用程序需要一个连接，在连接池中有一个可用的连接，新的连接不会被创建。

## Create and Use a Connection Pool

### Use an Instance of your Driver's MongoClient Object 使用一个驱动程序的MongoClient对象的实例

Most [driver](https://www.mongodb.com/docs/drivers/) provide an object of type MongoClient.

大多数[驱动程序](https://www.mongodb.com/docs/drivers/)提供一个类型为MongoClient的对象。

Use one MongoClient instance per application unless the application is connecting to many separate clusters. Each MongoClient instance manages its own connection pool to the MongoDB cluster or node specified when the MongoClient is created. MongoClient objects are thread-safe in most drivers.

每个应用程序使用一个MongoClient的实例，除非应用程序连接了许多不同的集群。当MongoClient被创建时，每个MongoClient的实例管理它自己的连接池（到MongoDB集群或单独的节点。MongoClient对象在大多数驱动程序中是线程安全的。

>**Note**
>Store your MongoClient instance in a place that is globally accessible by your application.
>
>**注意**
>将你的MongoClient实例存储在一个全局可访问的地方，由你的应用程序访问。

### Authentication

To use a connection pool with LDAP, see [LDAP Connection Pool Behavior](https://www.mongodb.com/docs/v5.0/core/security-ldap-external/#std-label-ldap-connection-pool-behavior).

使用LDAP连接池，请参阅[LDAP连接池行为](https://www.mongodb.com/docs/v5.0/core/security-ldap-external/#std-label-ldap-connection-pool-behavior)。

## Sharded Cluster Connection Pooling

[mongos](https://www.mongodb.com/docs/v5.0/reference/program/mongos/#mongodb-binary-bin.mongos) routers have connection pools for each node in the cluster. The availability of connections to individual nodes within a sharded cluster affects latency. Operations must wait for a connection to be established.

[mongos](https://www.mongodb.com/docs/v5.0/reference/program/mongos/#mongodb-binary-bin.mongos)路由器为集群中的每个节点维护一个连接池。一个分片集群中各个节点的连接可用性影响延迟。操作必须等待连接的建立。

## Connection Pool Configuration Settings

To configure the connection pool, set the options:

- through the MongoDB URI,
- programmatically when building the MongoClient instance, or
- in your application framework's configuration files.

配置连接池，设置选项：

- 通过MongoDB URI
- 在构建MongoClient实例时，通过编程方式
- 在你的应用程序框架的配置文件中

### Settings

|Setting|Description|
|:---|:---|
|maxPoolSize|Maximum number of connections opened in the pool. When the connection pool reaches the maximum number of connections, new connections wait up until to the value of waitQueueTimeoutMS.Default: 100|
|minPoolSize|Minimum number of connections opened in the pool. The value of minPoolSize must be less than the value of maxPoolSize.Default: 0|
|connectTimeoutMS|Most drivers default to never time out. Some versions of the Java drivers (for example, version 3.7) default to 10.Default: 0 for most drivers. See your driver documentation.|
|socketTimeoutMS|Number of milliseconds to wait before timeout on a TCP connection.Do not use socketTimeoutMS as a mechanism for preventing long-running server operations.Setting low socket timeouts may result in operations that error before the server responds.Default: 0, which means no timeout. See your driver documentation.|
|maxIdleTimeMS|The maximum number of milliseconds that a connection can remain idle in the pool before being removed and closed.Default: See your driver documentation.|
|waitQueueTimeoutMS|Maximum wait time in milliseconds that a thread can wait for a connection to become available. A value of 0 means there is no limit.Default: 0. See your driver documentation.|
|ShardingTaskExecutorPoolMinSize|Minimum number of outbound connections each TaskExecutor connection pool can open to any given mongod instance.Default: 1. See ShardingTaskExecutorPoolMinSize.Parameter only applies to sharded deployments.|
|ShardingTaskExecutorPoolMinSizeForConfigServers|Optional override for ShardingTaskExecutorPoolMinSize to set the minimum number of outbound connections each TaskExecutor connection pool can open to a configuration server.<br>When set to:<br>-1, ShardingTaskExecutorPoolMinSize is used. This is the default.<br>an integer value greater than -1, overrides the minimum number of outbound connections each TaskExecutor connection pool can open to a configuration server.<br>Parameter only applies to sharded deployments.<br>Default: -1<br>New in version 5.0.10.|
|ShardingTaskExecutorPoolMaxSize|Maximum number of outbound connections each TaskExecutor connection pool can open to any given mongod instance.<br>Default: 2 64 - 1. See ShardingTaskExecutorPoolMaxSize.<br>Parameter only applies to sharded deployments.|
|ShardingTaskExecutorPoolMaxSizeForConfigServers|Optional override for ShardingTaskExecutorPoolMaxSize to set the maximum number of outbound connections each TaskExecutor connection pool can open to a configuration server.<br>When set to:<br>-1, ShardingTaskExecutorPoolMaxSize is used. This is the default.<br>an integer value greater than -1, overrides the maximum number of outbound connections each TaskExecutor connection pool can open to a configuration server.<br>Parameter only applies to sharded deployments.<br>Default: -1<br>New in version 5.0.10.|

