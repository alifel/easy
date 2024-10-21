# FAQ: Concurency

MongoDB allows multiple clients to read and write the same data. To ensure consistency, MongoDB uses locking and concurrency control to prevent clients from modifying the same data simultaneously. Writes to a single document occur either in full or not at all, and clients always see consistent data.

MongoDB允许多客户端读和写相同的数据。为了确保一致性，MongoDB使用了锁和并发控制去阻止客户端同时修改同一个数据。写入一个单文档，要么完成，要么都不完成，所有客户端会一直看到一致的数据。

## What type of locking does MongoDB use?

MongoDB uses multi-granularity locking [1] that allows operations to lock at the global, database or collection level, and allows for individual storage engines to implement their own concurrency control below the collection level (e.g., at the document-level in WiredTiger).

MongoDB使用多粒度锁，允许操作在全局、数据库或集合级别锁定，并允许各个存储引擎在集合级别以下实现自己的并发控制（例如，在WiredTiger中在文档级别）。

MongoDB uses reader-writer locks that allow concurrent readers shared access to a resource, such as a database or collection.

MongoDB使用读写锁，允许并发读共享访问资源，如数据库或集合。

In addition to a shared (S) locking mode for reads and an exclusive (X) locking mode for write operations, intent shared (IS) and intent exclusive (IX) modes indicate an intent to read or write a resource using a finer granularity lock. When locking at a certain granularity, all higher levels are locked using an intent lock.

此外，除了读（S）锁和写（X）锁之外，还有意图共享（IS）和意图独占（IX）锁，表示使用更细粒度的锁读或写资源。在锁定某个粒度时，所有更高级别的锁都使用意图锁锁定。

For example, when locking a collection for writing (using mode X), both the corresponding database lock and the global lock must be locked in intent exclusive (IX) mode. A single database can simultaneously be locked in IS and IX mode, but an exclusive (X) lock cannot coexist with any other modes, and a shared (S) lock can only coexist with intent shared (IS) locks.

例如，当对集合进行写锁定（使用模式X）时，相应的数据库锁和全局锁必须以意图独占（IX）模式锁定。一个数据库可以同时以IS和IX模式锁定，但独占（X）锁不能与其他模式共存，共享（S）锁只能与其他意图共享（IS）锁共存。

Locks are fair, with lock requests for reads and writes queued in order. However, to optimize throughput, when one lock request is granted, all other compatible lock requests are granted at the same time, potentially releasing the locks before a conflicting lock request is performed. For example, consider a situation where an X lock was just released and the conflict queue contains these locks:

锁是公平的，读写锁请求按顺序排队。然而，为了优化吞吐量，当一个锁请求被授予时，所有其他兼容的锁请求同时被授予，潜在地在发生冲突的锁请求之前释放锁。例如，考虑一个情况，其中X锁刚刚被释放，冲突队列包含这些锁：

IS → IS → X → X → S → IS

In strict first-in, first-out (FIFO) ordering, only the first two IS modes would be granted. Instead MongoDB will actually grant all IS and S modes, and once they all drain, it will grant X, even if new IS or S requests have been queued in the meantime. As a grant will always move all other requests ahead in the queue, no starvation of any request is possible.

在严格的先进先出顺序下，仅仅前2个IS模式会被授予。相反，MongoDB实际上会授予所有IS和S模式，并且在它们都排空之后，即使在此期间已经排队了新的IS或S请求，也会授予X。由于授予总是会将所有其他请求移动到队列的前面，因此任何请求都不会被饿死。

In db.serverStatus() and db.currentOp() output, the lock modes are represented as follows:

在db.serverStatus()和db.currentOp()输出中，锁模式表示如下：

|Lock Mode|Description|
|--|--|
|R|Represents Shared (S) lock.|
|W|Represents Exclusive (X) lock.|
|r|Represents Intent Shared (IS) lock.|
|w|Represents Intent Exclusive (IX) lock.|

## How granular are locks in MongoDB?

For most read and write operations, WiredTiger uses optimistic concurrency control. WiredTiger uses only intent locks at the global, database and collection levels. When the storage engine detects conflicts between two operations, one will incur a write conflict causing MongoDB to transparently retry that operation.

对于大多数读写操作，WiredTiger使用乐观并发控制。WiredTiger在全局、数据库和集合级别仅使用意图锁。当存储引擎检测到两个操作之间的冲突时，其中一个操作将导致写冲突，导致MongoDB透明地重试该操作。

Some global operations, typically short lived operations involving multiple databases, still require a global "instance-wide" lock. Some other operations, such as renameCollection, still require an exclusive database lock in certain circumstances.

一些全局操作，通常是涉及多个数据库的短期操作，仍然需要全局“实例范围”锁。其他一些操作，如renameCollection，在某些情况下仍然需要独占数据库锁。

## How do I see the status of locks on my mongod instances?

For reporting on lock utilization information on locks, use any of these methods:

为了报告锁利用信息，可以使用以下任何一种方法：

- db.serverStatus(),
- db.currentOp(),
- mongotop
- mongostat, and/or
- the MongoDB Cloud Manager or Ops Manager, an on-premise solution available in MongoDB Enterprise Advanced

Specifically, the locks document in the output of serverStatus, or the locks field in the current operation reporting provides insight into the type of locks and amount of lock contention in your mongod instance.

具体而言，serverStatus 输出中的 locks 文档，或当前操作报告中的 locks 字段，能够提供有关锁类型以及你的 mongod 实例中锁争用情况的深入信息。

In db.serverStatus() and db.currentOp() output, the lock modes are represented as follows:

在db.serverStatus()和db.currentOp()输出中，锁模式表示如下：

|Lock Mode|Description|
|--|--|
|R|Represents Shared (S) lock.|
|W|Represents Exclusive (X) lock.|
|r|Represents Intent Shared (IS) lock.|
|w|Represents Intent Exclusive (IX) lock.|

To terminate an operation, use db.killOp().

## Does a read or write operation ever yield the lock?

In some situations, read and write operations can yield their locks.

在一些情况下，读和写操作会释放他们的锁

Long running read and write operations, such as queries, updates, and deletes, yield locks under many conditions. MongoDB operations can also yield locks between individual document modifications in write operations that affect multiple documents.

长时间运行的读写操作（如查询、更新和删除）在许多情况下会释放锁。此外，MongoDB 的操作在影响多个文档的写操作中，也可能在每次修改单个文档之间释放锁。

For storage engines supporting document level concurrency control, such as WiredTiger, yielding is not necessary when accessing storage as the intent locks, held at the global, database and collection level, do not block other readers and writers. However, operations will periodically yield, such as:

- to avoid long-lived storage transactions because these can potentially require holding a large amount of data in memory;
- to serve as interruption points so that you can kill long running operations;
- to allow operations that require exclusive access to a collection such as index/collection drops and creations.

对于支持文档级并发控制的存储引擎（如 WiredTiger），在访问存储时不需要释放锁，因为在全局、数据库和集合级别持有的意向锁不会阻塞其他读写操作。然而，操作仍会定期释放锁，例如：

- 为了避免长时间存储事务，因为这些事务可能需要在内存中保留大量数据；
- 作为中断点，便于终止长时间运行的操作；
- 允许某些需要对集合进行排他访问的操作，如索引/集合的删除和创建。

## What locks are taken by some common client operations?

The following table lists some operations and the types of locks they use for document level locking storage engines:

下面是一些操作及其在文档级锁定存储引擎中使用的锁类型：

|Operation|Database|Collection|
|--|--|--|
|Issue a query|r (Intent Shared)|r (Intent Shared)|
|Insert data|w (Intent Exclusive)|w (Intent Exclusive)|
|Remove data|w (Intent Exclusive)|w (Intent Exclusive)|
|Update data|w (Intent Exclusive)|w (Intent Exclusive)|
|Perform Aggregation|r (Intent Shared)|r (Intent Shared)|
|Create an index| |W (Exclusive)|
|List collections|r (Intent Shared)| |
|Map-reduce|W (Exclusive) and R (Shared)|w (Intent Exclusive) and r (Intent Shared)|

> **NOTE**
> Creating an index requires an exclusive (W) lock on a collection. However, the lock is not retained for the full duration of the index build process.
> 创建索引需要对集合的独占（W）锁。然而，锁不会保留在整个索引构建过程的持续时间内。
> For more information, see [Index Builds on Populated Collections](https://www.mongodb.com/docs/v5.0/core/index-creation/#std-label-index-operations).

## Which administrative commands lock a database?

Some administrative commands can exclusively lock a database for extended time periods. For large clusters, consider taking the mongod instance offline so that clients are not affected. For example, if a mongod is part of a replica set, take the mongod offline and let other members of the replica set process requests while maintenance is performed.

一些管理命令可以独占锁定数据库，以进行扩展时间段的锁定。对于大型集群，考虑将mongod实例脱机，以避免影响客户端。例如，如果mongod是副本集的一部分，请将mongod脱机，并让副本集的其他成员处理请求，同时执行维护。

### Administrative Commands Taking Extended Locks

These administrative operations require an exclusive lock at the database level for extended periods:

这些管理操作需要对数据库进行长时间的独占锁定：

- cloneCollectionAsCapped command
- convertToCapped command

In addition, the renameCollection command and corresponding db.collection.renameCollection() shell method take the following locks:

此外，renameCollection命令和相应的db.collection.renameCollection() shell方法会获取以下锁：

|Command|Lock behavior|
|--|--|
|renameCollection database command|If renaming a collection within the same database, the renameCollection command takes an exclusive (W) lock on the source and target collections.<br/>If the target namespace is in a different database as the source collection, The renameCollection command takes an exclusive (W) lock on the target database when renaming a collection across databases and blocks other operations on that database until it finishes.|
|renameCollection() shell helper method|The renameCollection() method takes an exclusive (W) lock on the source and target collections, and cannot move a collection between databases.|

|命令|锁行为|
|--|--|
|renameCollection 数据库命令|如果在一个数据库中重命名集合，renameCollection命令会获取对源集合和目标集合的独占（W）锁。<br/>如果目标命名空间在源集合的不同数据库中，则renameCollection命令在跨数据库重命名集合时会获取对目标数据库的独占（W）锁，并阻止对该数据库上的其他操作，直到它完成。|
|renameCollection() shell helper method|renameCollection()方法会获取对源集合和目标集合的独占（W）锁，并且不能在数据库之间移动集合。|

## Which administrative commands lock a collection?

These administrative operations require an exclusive lock at the collection level:

这些管理操作需要对集合进行独占锁定：

- create command and corresponding db.createCollection() and db.createView() shell methods

- create命令和相关的db.createCollection()、db.createView()shell方法

- createIndexes command and corresponding db.collection.createIndex() and db.collection.createIndexes() shell methods

- createIndexes命令和相关的db.collection.createIndex()、db.collection.createIndexes() shell方法

- drop command and corresponding db.collection.drop() shell methods

- drop命令和相关的db.collection.drop() shell方法

- dropIndexes command and corresponding db.collection.dropIndex() and db.collection.dropIndexes() shell methods

- dropIndexes命令和相关的db.collection.dropIndex()、db.collection.dropIndexes() shell方法

- the renameCollection command and corresponding db.collection.renameCollection() shell method take the following locks, depending on version:

  - For renameCollection and db.collection.renameCollection(): If renaming a collection within the same database, the operation takes an exclusive (W) lock on the source and target collections.

  - For renameCollection only: If the target namespace is in a different database as the source collection, the operation takes an exclusive (W) lock on the target database when renaming a collection across databases and blocks other operations on that database until it finishes.

- renameCollection命令和相应的db.collection.renameCollection() shell方法，取决于版本：

  - 如果在一个数据库中重命名集合，操作会获取对源集合和目标集合的独占（W）锁。

  - 仅对于renameCollection：如果目标命名空间在源集合的不同数据库中，操作会在跨数据库重命名集合时获取对目标数据库的独占（W）锁，并阻止对该数据库上的其他操作，直到它完成。

- the reIndex command and corresponding db.collection.reIndex() shell method obtain an exclusive (W) lock on the collection and block other operations on the collection until finished.

- reIndex命令和相应的db.collection.reIndex() shell方法获取对集合的独占（W）锁，并阻止对该集合上的其他操作，直到完成。

- the replSetResizeOplog command takes an exclusive (W) lock on the oplog collection and blocks other operations on the collection until it finishes.

- replSetResizeOplog命令获取对oplog集合的独占（W）锁，并阻止对该集合上的其他操作，直到完成。

## Does a MongoDB operation ever lock more than one database

These MongoDB operations may obtain and hold a lock on more than one database:

这些MongoDB操作可能会获取并保持对多个数据库的锁定：

|Operation|Behavior|
|--|--|
|reIndex db.collection.reIndex()|These operations only obtain an exclusive (W) collection lock instead of a global exclusive lock.|
|renameCollection|This operation obtains an exclusive (W) lock on the target database, an intent shared (r) lock on the source database, and a shared (S) lock on the source collection when renaming a collection across databases.<br/><br/>When renaming a collection in the same database, the operation only requires exclusive (W) locks on the source and target collections.|
|replSetResizeOplog|This operation only obtains an exclusive (W) lock on the oplog collection instead of a global exclusive lock.|

|Operation|Behavior|
|--|--|
|reIndex db.collection.reIndex()|这些操作仅获取集合的独占（W）锁，而不是全局独占锁。|
|renameCollection|这个操作获取对目标数据库的独占（W）锁，对源数据库的意图共享（r）锁，以及对源集合的共享（S）锁，当跨数据库重命名集合时。<br/><br/>当在同一个数据库中重命名集合时，操作仅需要对源集合和目标集合的独占（W）锁。|
|replSetResizeOplog|这个操作仅获取对oplog集合的独占（W）锁，而不是全局独占锁。|

## How does sharding affect concurrency?

Sharding improves concurrency by distributing collections over multiple mongod instances, allowing shard servers (specifically, mongos processes) to run concurrently with the downstream mongod instances.

Sharding通过将集合分布到多个mongod实例上，提高了并发性，允许分片服务器（特别是mongos进程）与下游mongod实例并发运行。

In a sharded cluster, locks apply to each individual shard, not to the whole cluster; i.e. each mongod instance is independent of the others in the sharded cluster and uses its own locks. The operations on one mongod instance do not block the operations on any others.

在分片集群中，锁仅适用于每个单独的分片，而不是整个集群；即每个mongod实例在分片集群中独立于其他实例，并使用自己的锁。一个mongod实例上的操作不会阻止其他实例上的操作。

## How does concurrency affect a replica set primary?

With replica sets, when MongoDB writes to a collection on the primary, MongoDB also writes to the primary's oplog, which is a special collection in the local database. Therefore, MongoDB must lock both the collection's database and the local database. The mongod must lock both databases at the same time to keep the database consistent and ensure that write operations, even with replication, are all or nothing operations.

使用副本集，当MongoDB在主节点上写一个集合时，MongoDB也会写主节点的oplog，这是一个本地数据库中的特殊集合。因此，MongoDB必须锁集合的数据库和本地数据库。mongod必须同时锁数据库以保证数据库的一致性，确保这个写操作，即使在副本集上，要么全部ok，要么全部不ok。

When writing to a replica set, the lock's scope applies to the primary.

当写入副本集时，锁的范围时primary。

## How does concurrency affect secondaries?

In replication, MongoDB does not apply writes serially to secondaries. Secondaries collect oplog entries in batches and then apply those batches in parallel. Writes are applied in the order that they appear in the oplog.

在副本集合中，MongoDB不会按顺序应用到secondaries。 Secondaries按批次手机oplog，然后并发的批量的应用这个oplog。写操作时按照在oplog中的顺序被应用的。

Reads that target secondaries read from a WiredTiger snapshot of the data if the secondary is undergoing replication. This allows the read to occur simultaneously with replication, while still guaranteeing a consistent view of the data.

读取secondaries的读取时从WiredTiger的快照中读取的，如果secondary正在复制。这允许读取与复制同时发生，同时仍然保证数据的一致性。

## Does MongoDB support transactions?

Because a single document can contain related data that would otherwise be modeled across separate parent-child tables in a relational schema, MongoDB's atomic single-document operations already provide transaction semantics that meet the data integrity needs of the majority of applications. One or more fields may be written in a single operation, including updates to multiple sub-documents and elements of an array. The guarantees provided by MongoDB ensure complete isolation as a document is updated; any errors cause the operation to roll back so that clients receive a consistent view of the document.

由于单个文档可以包含相关数据，而这些数据在关系型模式中通常会被建模为不同的父子表，MongoDB 的单文档原子操作已经提供了满足大多数应用程序数据完整性需求的事务语义。在单次操作中可以写入一个或多个字段，包括对多个子文档和数组元素的更新。MongoDB 提供的保证确保在更新文档时实现完全隔离；如果发生错误，操作会回滚，确保客户端始终获得文档的一致视图。

For situations that require atomicity of reads and writes to multiple documents (in a single or multiple collections), MongoDB supports distributed transactions, including transactions on replica sets and sharded clusters.

对于需要对多个文档（单个集合或多个集合）进行原子性读写的情况，MongoDB 支持分布式事务，包括在副本集和分片集群上的事务。

For more information, see transactions.

> **Important**
> in most cases, a distributed transaction incurs a greater performance cost over single document writes, and the availability of distributed transactions should not be a replacement for effective schema design. For many scenarios, the denormalized data model (embedded documents and arrays) will continue to be optimal for your data and use cases. That is, for many scenarios, modeling your data appropriately will minimize the need for distributed transactions.
> 在大多数情况下，分布式事务比单文档写入的性能成本更高，分布式事务的可用性不应取代有效的模式设计。对于许多场景，非规范化数据模型（嵌入文档和数组）将继续是您的数据和用例的最佳选择。也就是说，对于许多场景，适当地建模数据将最大限度地减少对分布式事务的需求。
> For additional transactions usage considerations (such as runtime limit and oplog size limit), see also Production Considerations.
> 有关分布式事务使用情况的更多信息（例如运行时限制和oplog大小限制），请参阅生产注意事项。

## What isolation guarantees does MongoDB provide?

Depending on the read concern, clients can see the results of writes before the writes are durable. To control whether the data read may be rolled back or not, clients can use the readConcern option.

依赖于read concern，客户端可以在写入持久化之前看到写入的结果。为了控制读取的数据是否可以回滚，客户端可以使用readConcern选项。

## What are lock-free read operations?

New in version 5.0.

5.0开始的新功能

A lock-free read operation runs immediately: it is not blocked when another operation has an exclusive (X) write lock on the collection.

无锁的读取操作可以立即执行：当另一个操作在集合上持有排他性 (X) 写锁时，它不会被阻塞。

Starting in MongoDB 5.0, the following read operations are not blocked when another operation holds an exclusive (X) write lock on the collection:

从 MongoDB 5.0 开始，以下读取操作在另一个操作持有集合上的排他性 (X) 写锁时不会被阻塞：

- find
- count
- distinct
- aggregate
- mapReduce
- listCollections
- listIndexes

When writing to a collection, mapReduce and aggregate hold an intent exclusive (IX) lock. Therefore, if an exclusive X lock is already held on a collection, mapReduce and aggregate write operations are blocked.

当写入集合时，mapReduce和aggregate持有意图独占（IX）锁。因此，如果集合上已经持有独占（X）锁，mapReduce和aggregate的写操作将被阻塞。

For information, see:

- [Read Isolation, Consistency, and Recency](https://www.mongodb.com/docs/v5.0/core/read-isolation-consistency-recency/)
- [Atomicity and Transactions](https://www.mongodb.com/docs/v5.0/core/write-operations-atomicity/)
- [Read Concern](https://www.mongodb.com/docs/v5.0/reference/read-concern/)
