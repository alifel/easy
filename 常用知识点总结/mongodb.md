# mongodb

## 原子性和并发控制

**关于原子性和并发，官方文档描述不多，会有很多困惑，但是官方文档有一个例子，知道这个例子，大部分关于并发的问题都能迎刃而解。这个例子要仔细看，2次同时的执行，只有1个能执行成功。同时要晓得，mongodb的所有更新操作都是原子性的。就这2点，就可以解决你所遇到的绝大部分关于并发的问题。注意，用着2个确定的点去解决，其他为止情况都按你想到的（不管真的是不是这样）的最坏的情况去考虑。**

原文：<https://www.mongodb.com/docs/v5.0/core/write-operations-atomicity/#concurrency-control>

Concurrency control allows multiple applications to run concurrently without causing data inconsistency or conflicts.

A findAndModify operation on a document is atomic: if the find condition matches a document, the update is performed on that document. Concurrent queries and additional updates on that document are not affected until the current update is complete.

Consider the following example:

```shell
db.myCollection.insertMany( [
   { _id: 0, a: 1, b: 1 },
   { _id: 1, a: 1, b: 1 }
] )
```

```shell
db.myCollection.findAndModify( {
   query: { a: 1 },
   update: { $inc: { b: 1 }, $set: { a: 2 } }
} )
```

After the findAndModify operations are complete, it is guaranteed that a and b in both documents are set to 2.

