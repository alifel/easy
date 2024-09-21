# mongodb

**mongodb的nodejs客户端创建的连接，在`control+c`退出后，连接会自动断开，不用手动释放**

下面代码，测试同时创建了约40个连接，等了3分钟，仍然保持，连接数不变，等`control+c`退出后，相关连接全部立刻释放。

```typescript
import { MongoClient } from "mongodb";
import * as dotenv from "dotenv";
import path from "path";
dotenv.config({
  path: path.resolve(
    __dirname,
    process.env.NODE_ENV === "production" ? ".env.production" : ".env"
  ),
});

const test = async () => {
  const client = new MongoClient(
    `mongodb://${process.env.MONGODB_HOST}:${process.env.MONGODB_PORT}`,
    {
      auth: {
        username: process.env.MONGODB_USERNAME,
        password: process.env.MONGODB_PASSWORD,
      },
      maxPoolSize: 200,
    }
  );
  const db = client.db("live_game");
  const collection = db.collection("users");
  //   const r = await collection.insertOne({
  //     // _id: new ObjectId(generateUUID()),
  //     age: 10,
  //     id: generateUUID(),
  //   });
  // const r = await collection.findOne({
  //   id: "fffbbe4f-6883-43ed-bc88-e4445d213f4d",
  // });
  for (let i = 0; i < 1000; i++) {
    collection.findOne({
      id: "fffbbe4f-6883-43ed-bc88-e4445d213f4d",
    });
  }
  // console.log(r);
  // return r;
};
test();
```

---

**mongodb的最大连接数windows为1,000,000，linux为RLIMIT_NOFILE\* 0.8**

==原文：<https://www.mongodb.com/docs/manual/reference/configuration-options/#mongodb-setting-net.maxIncomingConnections>==

>Type: integer
>
>Default (Windows): 1,000,000
>Default (Linux): (RLIMIT_NOFILE) * 0.8
>The maximum number of simultaneous connections that mongos or mongod accepts. This setting has no effect if it is higher than your operating system's configured maximum connection tracking threshold.
>
>Do not assign too low of a value to this option, or you may encounter errors during normal application operation.
>
>This is particularly useful for a mongos if you have a client that creates multiple connections and allows them to timeout rather than closing them.
>
>In this case, set maxIncomingConnections to a value slightly higher than the maximum number of connections that the client creates, or the maximum size of the connection pool.
>
>This setting prevents the mongos from causing connection spikes on the individual shards. Spikes like these may disrupt the operation and memory allocation of the sharded cluster.

linux查询rlimit_nofile的命令为`ulimit -n`

---

**mongodb通过host自身的`keepAlive`进行相关`keepAlive`的配置，无论客户端还是服务端**

您可以通过下面的官方文档内容get到这一点：

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

==(【非文档内容，自己添加】:pill:docker中设置`net.ipv4.tcp_keepalive_time`无效，需要通过docker run命令的`--sysctl`参数设置，例如：`--sysctl net.ipv4.tcp_keepalive_time=600`)==

==(来源官网：<https://www.mongodb.com/zh-cn/docs/v5.0/faq/diagnostics/>)==

---

**docker 启动`mongodb`**

```shell
docker run --name mongo-dev -p 27018:27017 -e MONGO_INITDB_ROOT_USERNAME=admin -e MONGO_INITDB_ROOT_PASSWORD=123456Mongodb --sysctl net.ipv4.tcp_keepalive_time=120 -d mongo
```

>--sysctl net.ipv4.tcp_keepalive_time=120 为了设置linux的keepalive时间，官方推荐120s，默认2小时。

---

**mongodb nodejs客户端，`maxIdleTimeMS`默认为0， `maxPoolSize`默认为100，`minPoolSize`默认为0，`waitQueueTimeoutMS`默认为0，`connectTimeoutMS`默认为30000，`socketTimeoutMS`默认为0**

==经过测试`maxIdleTimeMS`与`socketTimeoutMS`不论设置多少，都不会发生超时，也观测不到相关链接释放的表现，不过还好，默认值都为0，就先不关注这2个值了==

```typescript
import { MongoClient } from "mongodb";
import * as dotenv from "dotenv";
import path from "path";
dotenv.config({
  path: path.resolve(
    __dirname,
    process.env.NODE_ENV === "production" ? ".env.production" : ".env"
  ),
});

import { v4 as uuidv4 } from "uuid";

function generateUUID(): string {
  return uuidv4();
}

const test = async () => {
  const client = new MongoClient(
    `mongodb://${process.env.MONGODB_HOST}:${process.env.MONGODB_PORT}`,
    {
      auth: {
        username: process.env.MONGODB_USERNAME,
        password: process.env.MONGODB_PASSWORD,
      },
      maxPoolSize: 200,
      // maxIdleTimeMS: 3000,
      socketTimeoutMS: 3000,
    }
  );
  const db = client.db("live_game");
  const collection = db.collection("users");
  //   const r = await collection.insertOne({
  //     // _id: new ObjectId(generateUUID()),
  //     age: 10,
  //     id: generateUUID(),
  //   });
  // const r = await collection.findOne({
  //   id: "fffbbe4f-6883-43ed-bc88-e4445d213f4d",
  // });
  for (let i = 0; i < 1; i++) {
    const r = await collection.findOne({
      id: "707db87b-4bdf-4aee-b7fc-780979ef46c6",
    });
    // const r = await collection.insertOne({
    //   // _id: new ObjectId(generateUUID()),
    //   age: 10,
    //   id: generateUUID(),
    // });
    console.log(r);
    setTimeout(async () => {
      const r = await collection.findOne({
        id: "fffbbe4f-6883-43ed-bc88-e4445d213f4d",
      });
      console.log(r);
    }, 7000);
  }
  // console.log(r);
  // return r;
};
test();
```

==mongodb nodejs相关官方文档 <https://www.mongodb.com/docs/drivers/node/current/fundamentals/connection/connection-options/>==

==mongodb服务端相关官方文档<https://www.mongodb.com/docs/v5.0/administration/connection-pool-overview/#connection-pool-overview>、<https://www.mongodb.com/docs/v5.0/reference/connection-string-options/#connection-options>==

maxIdleTimeMS：Specifies the amount of time, in milliseconds, a connection can be idle before it's closed. Specifying 0 means no minimum.

maxIdleTimeMS：指定一个时间，单位浩渺，这是连接被关闭前可以空间的时间。指定0，意味着没有最小值。

maxPoolSize：Specifies the maximum number of clients or connections the driver can create in its connection pool. This count includes connections in use.

maxPollSize：指定驱动可以在它的连接池创建的clients或congnections的最大数量。这个数量包括已经被使用的数量。

minPoolSize：Specifies the number of connections the driver creates and maintains in the connection pool even when no operations are occurring. This count includes connections in use.

minPoolSize：指定驱动在连接池中创建和维护的连接数量，即使没有操作发生。这个数量包括被已被使用的数量。

waitQueueTimeoutMS：Specifies the amount of time, in milliseconds, spent attempting to check out a connection from a server's connection pool before timing out.

waitQueueTimeoutMS：指定一个时间，单位毫秒，这是尝试从服务端的连接池中获取连接，在超时前经过的时间。

connectTimeoutMS：Specifies the amount of time, in milliseconds, to wait to establish a single TCP socket connection to the server before raising an error. Specifying 0 disables the connection timeout.

connectTimeoutMS：指定一个时间，单位毫秒，这是建立单个TCP socket连接到服务器发生错误前等待的时间。指定0关闭连接超时。

socketTimeoutMS：Specifies the amount of time, in milliseconds, spent attempting to send or receive on a socket before timing out. Specifying 0 means no timeout.

socketTimeoutMS：指定一个时间，单位毫秒，这是在socket上发送或接收数据，在超时前经过的时间。指定0意味着没有超时。

---

**mongodb服务端没有空闲连接最大空闲时间的配置，仅仅只在客户端（驱动）中找到了相关配置`maxIdleTimeMS`，问了一下gpt，说mongodb服务端确实没有，依赖于客户端的相关配置，这个说法仅供参考，但目前没有在文档中找到向左的论述**

---
