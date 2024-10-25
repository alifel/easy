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

**关于upset的并发控制，在高并发情况下，update设置upset=true会有并发问题，可能会导致插入相同的文档2次。这个问题可以通过查询条件中放唯一索引解决（防止唯一索引后，同时发生的2个更新，一个成功后，另外一个要么成功插入新数据，要么失败）。**

**建立唯一索引，为了保证2个更新都成功，没有失败的情况发生，做到一下5点，就可以：**

1. 目标集合有唯一索引（能引发重复key错误的）
2. 更新操作不能时updateMany，或者updateOne设置了multi=true
3. 更新匹配的条件是下面2条之一：
   - 单独的相等谓语判断，如 { "fieldA" : "valueA" }
   - 相等谓语的逻辑与，如 { "fieldA" : "valueA", "fieldB" : "valueB" }
4. 在更新匹配条件中的相等谓语的field要匹配唯一索引中的field。（比如更新条件中有2个使用了逻辑与的相等谓语，那么要匹配一个同时包含这2个谓语的唯一索引）
5. 更新操作不能修改任何唯一索引中涉及的field。

官方文档原文：<https://www.mongodb.com/docs/v5.0/reference/method/db.collection.update/#upsert-with-duplicate-values>

Upsert with Duplicate Values

Upserts can create duplicate documents, unless there is a unique index to prevent duplicates.

Consider an example where no document with the name Andy exists and multiple clients issue the following command at roughly the same time:

```shell
db.people.update(
   { name: "Andy" },
   { $inc: { score: 1 } },
   {
     upsert: true,
     multi: true
   }
)
```

If all update() operations finish the query phase before any client successfully inserts data, and there is no unique index on the name field, each update() operation may result in an insert, creating multiple documents with name: Andy.

A unique index on the name field ensures that only one document is created. With a unique index in place, the multiple update() operations now exhibit the following behavior:

- Exactly one update() operation will successfully insert a new document.

- Other update() operations either update the newly-inserted document or fail due to a unique key collision.In order for other update() operations to update the newly-inserted document, all of the following conditions must be met:

  - The target collection has a unique index that would cause a duplicate key error.目标集合有唯一索引，这个索引会导致key重复错误

  - The update operation is not updateMany or multi is false.更新操作不能是updateMany，或者multi为false

  - The update match condition is either 更新匹配条件是下面2者之一:

    - A single equality predicate. For example { "fieldA" : "valueA" } 单个相等的相等谓语

    - A logical AND of equality predicates. For example { "fieldA" : "valueA", "fieldB" : "valueB" } 多个相等的相等谓语的逻辑与

  - The fields in the equality predicate match the fields in the unique index key pattern.更新匹配条件中的相等谓语的field要匹配唯一索引中的field（==:pill:**自己总结完全匹配，包括数量**==）

  - The update operation does not modify any fields in the unique index key pattern.更新操作不能修改任何唯一索引中涉及的field

  The following table shows examples of upsert operations that, when a key collision occurs, either result in an update or fail.

  |Unique Index Key Pattern|Update Operation|Result|
  |--|--|--|
  |`{ name : 1 }`|`db.people.updateOne({ name: "Andy" },{ $inc: { score: 1 } }, { upsert: true })`|The score field of the matched document is incremented by 1.|
  |`{ name : 1 }`|`db.people.updateOne({ name: { $ne: "Joe" } },{ $set: { name: "Andy" } },{ upsert: true })`|The operation fails because it modifies the field in the unique index key pattern (name).|
  |`{ name : 1 }`|`db.people.updateOne({ name: "Andy", email: "andy@xyz.com" }, { $set: { active: false } },{ upsert: true })`|The operation fails because the equality predicate fields (name, email) do not match the index key field (name).|
  