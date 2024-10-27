# Read Concern "local"

(==官方原文地址：<https://www.mongodb.com/docs/v5.0/reference/read-concern-local/>==)

A query with read concern "local" returns data from the instance with no guarantee that the data has been written to a majority of the replica set members (i.e. may be rolled back).

一个带有读关注"local"的查询从实例返回数据，没有保证数据已写入大多数副本集成员（即可能被回滚）。

Read concern "local" is the default for read operations against the primary and secondaries.

写关注“local”是针对主节点和从节点的读操作的默认值。

Regardless of the read concern level, the most recent data on a node may not reflect the most recent version of the data in the system.

无论读关注级别如何，节点上的最新数据可能不反映系统中的最新数据版本。

## Availability

Read concern "local" is available for use with or without causally consistent sessions and transactions.

写关注“local”可用于有或没有因果一致会话和事务。

## Read Concern local and Transactions

You set the read concern at the transaction level, not at the individual operation level. To set the read concern for transactions, see Transactions and Read Concern.

你设置读关注在事务级别，而不是在单个操作级别。要设置事务的读关注，请参阅事务和读关注。

You can create collections and indexes inside a transaction. If explicitly creating a collection or an index, the transaction must use read concern "local". If you implicitly create a collection, you can use any of the read concerns available for transactions.

你可以创建集合和索引在事务中。如果显示的创建一个集合或者索引，事务必须使用“local”读关注。如果你隐士的创建集合，你可使用任意事务可用的读关注

## Example

Consider the following timeline of a write operation Write <sub>0</sub> to a three member replica set:

考虑一个三成员副本集的写操作时间线，Write <sub>0</sub>：

> **NOTE**
> For simplification, the example assumes:
> 为了简化，例子假设：
>
> - All writes prior to Write <sub>0</sub> have been successfully replicated to all members.
> - Write <sub>prev</sub> is the previous write before Write <sub>0</sub>.
> - No other writes have occured after Write <sub>0</sub>.
>
> - Write <sub>0</sub>之前的所有写操作都已经成中的复制到了所有成员
> - Write <sub>prev</sub>在Write <sub>0</sub>之前写入
> - Write <sub>0</sub>之后没有其他写操作

![image](./images/read-concern-write-timeline.svg)

|Time|Event|Most Recent Write|Most Recent Write with "majority" concern|
|---|---|---|---|
|t <sub>0</sub>|Primary applies Write <sub>0</sub>|**Primary**: Write <sub>0</sub><br/>**Secondary 1**: Write <sub>prev</sub><br/>**Secondary 2**: Write <sub>prev</sub>|**Primary**: Write <sub>prev</sub><br/>**Secondary 1**: Write <sub>prev</sub><br/>**Secondary 2**: Write <sub>prev</sub>|
|t <sub>1</sub>|Secondary <sub>1</sub> applies write <sub>0</sub>|**Primary**: Write <sub>0</sub><br/>**Secondary 1**: Write <sub>0</sub><br/>**Secondary 2**: Write <sub>prev</sub>|**Primary**: Write <sub>prev</sub><br/>**Secondary 1**: Write <sub>prev</sub><br/>**Secondary 2**: Write <sub>prev</sub>|
|t <sub>2</sub>|Secondary <sub>2</sub> applies write <sub>0</sub>|**Primary**: Write <sub>0</sub><br/>**Secondary 1**: Write <sub>0</sub><br/>**Secondary 2**: Write <sub>0</sub>|**Primary**: Write <sub>prev</sub><br/>**Secondary 1**: Write <sub>prev</sub><br/>**Secondary 2**: Write <sub>prev</sub>|
|t <sub>3</sub>|Primary is aware of successful replication to Secondary <sub>1</sub> and sends acknowledgement to client|**Primary**: Write <sub>0</sub><br/>**Secondary 1**: Write <sub>0</sub><br/>**Secondary 2**: Write <sub>0</sub>|**Primary**: Write <sub>0</sub><br/>**Secondary 1**: Write <sub>prev</sub><br/>**Secondary 2**: Write <sub>prev</sub>|
|t <sub>4</sub>|Primary is aware of successful replication to Secondary <sub>2</sub>|**Primary**: Write <sub>0</sub><br/>**Secondary 1**: Write <sub>0</sub><br/>**Secondary 2**: Write <sub>0</sub>|**Primary**: Write <sub>0</sub><br/>**Secondary 1**: Write <sub>prev</sub><br/>**Secondary 2**: Write <sub>prev</sub>|
|t <sub>5</sub>|Secondary <sub>1</sub> receives notice (through regular replication mechanism) to update its snapshot of its most recent w: "majority" write|**Primary**: Write <sub>0</sub><br/>**Secondary 1**: Write <sub>0</sub><br/>**Secondary 2**: Write <sub>0</sub>|**Primary**: Write <sub>0</sub><br/>**Secondary 1**: Write <sub>0</sub><br/>**Secondary 2**: Write <sub>prev</sub>|
|t <sub>6</sub>|Secondary <sub>2</sub> receives notice (through regular replication mechanism) to update its snapshot of its most recent w: "majority" write|**Primary**: Write <sub>0</sub><br/>**Secondary 1**: Write <sub>0</sub><br/>**Secondary 2**: Write <sub>0</sub>|**Primary**: Write <sub>0</sub><br/>**Secondary 1**: Write <sub>0</sub><br/>**Secondary 2**: Write <sub>0</sub>|

Then, the following tables summarizes the state of the data that a read operation with "local" read concern would see at time T.

然后，以下表格总结了在时间 T 时，带有“local”读关注的数据状态。

![imgae](./images/read-concern-write-timeline.svg)

|Read Target|Time T|State of Data|
|---|---|---|
|Primary|After t <sub>0</sub>|Data reflects Write <sub>0</sub>|
|Secondary <sub>1</sub>|Before t <sub>1</sub>|Data reflects Write <sub>prev</sub>|
|Secondary <sub>1</sub>|After t <sub>1</sub>|Data reflects Write <sub>0</sub>|
|Secondary <sub>2</sub>|Before t <sub>2</sub>|Data reflects Write <sub>prev</sub>|
|Secondary <sub>2</sub>|After t <sub>2</sub>|Data reflects Write <sub>0</sub>|
