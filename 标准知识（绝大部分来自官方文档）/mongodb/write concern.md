# Write concern

Write concern describes the level of acknowledgment requested from MongoDB for write operations to a standalone mongod, replica sets, or sharded clusters. In sharded clusters, mongos instances will pass the write concern on to the shards.

写关注描述了从MongoDB请求的写操作的确认级别，以用于独立mongod、副本集或分片集群。在分片集群中，mongos实例会将写关注传递给分片。

> **NOTE**
>
> For multi-document transactions, you set the write concern at the transaction level, not at the individual operation level. Do not explicitly set the write concern for individual write operations in a transaction.
>
> If you specify a "majority" write concern for a multi-document transaction and the transaction fails to replicate to the calculated majority of replica set members, then the transaction may not immediately roll back on replica set members. The replica set will be eventually consistent. A transaction is always applied or rolled back on all replica set members.
>
> 对于多文档事务，您在事务级别设置写关注，而不是在单个操作级别设置。不要在事务中为单个写操作显式设置写关注。
>
> 如果您为多文档事务指定"majority"写关注，并且事务未能复制到副本集计算多数的成员，则事务可能不会立即在副本集成员上回滚。副本集将是最终一致的。事务总是在所有副本集成员中被接受或者回滚。

Replica sets and sharded clusters support setting a global default write concern. Operations which do not specify an explicit write concern inherit the global default write concern settings. See setDefaultRWConcern for more information.

副本集和分片集群支持设置全局默认写关注。未指定显式写关注的操作继承全局默认写关注设置。有关更多信息，请参阅setDefaultRWConcern。

To learn more about setting the write concern for deployments hosted in MongoDB Atlas, see Build a Resilient Application with MongoDB Atlas

有关在MongoDB Atlas中设置写关注的更多信息，请参阅使用MongoDB Atlas构建弹性应用程序。

## Write Concern Specification

Write concern can include the following fields:

写关注可以包括以下字段：

```shell
{ w: <value>, j: <boolean>, wtimeout: <number> }
```

- the w option to request acknowledgment that the write operation has propagated to a specified number of mongod instances or to mongod instances with specified tags.

- the j option to request acknowledgment that the write operation has been written to the on-disk journal, and

- the wtimeout option to specify a time limit to prevent write operations from blocking indefinitely.

- w 选项请求确认写操作已传播到指定数量的mongod实例或具有指定标签的mongod实例。
- j 选项请求确认写操作已写入磁盘日志。
- wtimeout 选项指定一个时间限制，以防止写操作无限期地阻塞。

### w Option

The w option requests acknowledgment that the write operation has propagated to a specified number of mongod instances or to mongod instances with specified tags. If the write concern is missing the w field, MongoDB sets the w option to the default write concern.

w 选项请求确认写操作已传播到指定数量的mongod实例或具有指定标签的mongod实例。如果写关注缺少 w 字段，MongoDB 将 w 选项设置为默认写关注。

> **NOTE**
> If you use the setDefaultRWConcern to set the default write concern, you must specify a w field value.
>
> 如果你使用了setDefaultRWConcern来设置默认写关注，你必须指定一个w字段值。  

Using the w option, the following w: `<value>` write concerns are available:

使用w选项，以下w: `<value>`写关注是可用的：

|Value|Description|
|:---|:---|
|"majority"|Requests acknowledgment that write operations have been durably committed to the calculated majority of the data-bearing voting members (i.e. primary and secondaries with members[n].votes greater than 0). { w: "majority" } is the default write concern for most MongoDB deployments. See Implicit Default Write Concern.<br/>请求确认写操作已持久地提交到数据承载投票成员的计算多数（即具有大于0的成员[n].votes的主节点和次节点）。{ w: "majority" }是大多数MongoDB部署的默认写关注。参见隐式默认写关注。<br/>|

==中间有一部分还未翻译.......==

## Implicit Default Write Concern

Starting in MongoDB 5.0, the implicit default write concern is `w: majority`. However, special considerations are made for deployments containing arbiters:

- The voting majority of a replica set is 1 plus half the number of voting members, rounded down. If the number of data-bearing voting members is not greater than the voting majority, the default write concern is `{ w: 1 }`.

- In all other scenarios, the default write concern is `{ w: "majority" }`.

- For a sharded cluster, the default write concern is always retrieved from the config server. Since the config server must have zero arbiters, the implicit default write concern for a sharded cluster is always `"majority"`. Even if a shard is in a Primary-Secondary-Arbiter topology, it still has a default write concern of `"majority"`. Starting in MongoDB 5.1, this configuration is disallowed if the cluster-wide write concern is not set.

从MongoDB 5.0开始，默认隐式写关注为 w: majority。但对于包含仲裁者的部署，需做特别考虑：

- 副本集的投票多数是1加上投票成员总数的一半（向下取整）。如果数据承载的投票成员数量不大于投票多数，则默认写关注为 `{ w: 1 }`。
- 在所有其他情况下，默认写关注为 `{ w: "majority" }`。
- 对于分片集群，默认写关注始终从配置服务器获取。由于配置服务器不能有仲裁者，分片集群的隐式默认写关注始终为 `"majority"`。即使某个分片采用Primary-Secondary-Arbiter（主-从-仲裁者）拓扑结构，它的默认写关注仍为 `"majority"`。从MongoDB 5.1起，如果集群级别写关注未设置，该配置不被允许。

Specifically, MongoDB uses the following formula to determine the default write concern:

具体而言，MongoDB使用以下公式来确定默认写关注：

```shell
if [ (#arbiters > 0) AND (#non-arbiters <= majority(#voting-nodes)) ]
    defaultWriteConcern = { w: 1 }
else
    defaultWriteConcern = { w: "majority" }
```

For example, consider the following deployments and their respective default write concerns:

例如，考虑以下部署及其对应的默认写关注：

|Non-Arbiters|Arbiters|Voting Nodes|Majority of Voting Nodes|Implicit Default Write Concern|
|--|--|--|--|--|
|2|1|3|2|`{ w: 1 }`|
|4|1|5|3|`{ w: "majority" }`|

- In the first example:

  - There are 2 non-arbiters and 1 arbiter for a total of 3 voting nodes.

  - The majority of voting nodes (1 plus half of 3, rounded down) is 2.

  - The number of non-arbiters (2) is equal to the majority of voting nodes (2), resulting in an implicit write concern of `{ w: 1 }`.

- In the second example:

  - There are 4 non-arbiters and 1 arbiter for a total of 5 voting nodes.

  - The majority of voting nodes (1 plus half of 5, rounded down) is 3.

  - The number of non-arbiters (4) is greater than the majority of voting nodes (3), resulting in an implicit write concern of `{ w: "majority" }`.

- 在第一个例子中：
  - 有2个非仲裁者和1个仲裁者，共3个投票节点。
  - 投票节点的多数（3的一半加1，向下取整）是2。
  - 非仲裁者数量（2）等于投票节点的多数（2），因此隐式写关注为 { w: 1 }。
- 在第二个例子中：
  - 有4个非仲裁者和1个仲裁者，共5个投票节点。
  - 投票节点的多数（5的一半加1，向下取整）是3。
  - 非仲裁者数量（4）大于投票节点的多数（3），因此隐式写关注为 { w: "majority" }。

## Acknowledgment Behavior

The w option and the j option determine when mongod instances acknowledge write operations.

`w` 选项和 `j` 选项决定了 `mongod` 实例确认写操作的时间。

### Standalone

A standalone mongod acknowledges a write operation either after applying the write in memory or after writing to the on-disk journal. The following table lists the acknowledgment behavior for a standalone and the relevant write concerns:

独立的 `mongod` 节点会在将写操作应用到内存后或写入磁盘日志后确认该写操作。下表列出了独立节点的确认行为以及相关的写关注：

||`j` is unspecified|`j:true`|`j:false`|
|--|--|--|--|
|`w: 1`|In memory|On-disk journal|In memory|
|`w: "majority"`|On-disk journal if running with journaling|On-disk journal|In memory|

||`j` 未指定|`j:true`|`j:false`|
|--|--|--|--|
|`w: 1`|在内存中|磁盘日志|在内存中|
|`w: "majority"`|如果启用日志记录，则为磁盘日志|磁盘日志|在内存中|

> Note
> With writeConcernMajorityJournalDefault set to false, MongoDB does not wait for w: "majority" writes to be written to the on-disk journal before acknowledging the writes. As such, "majority" write operations could possibly roll back in the event of a transient loss (e.g. crash and restart) of a majority of nodes in a given replica set.
> 注意  
> 如果 `writeConcernMajorityJournalDefault` 设置为 `false`，MongoDB 在确认 `w: "majority"` 写操作时，不会等待该操作写入磁盘日志。因此，如果副本集的大多数节点暂时丢失（例如崩溃并重启），则“majority”写操作可能会被回滚。

==中间有一部分还未翻译.......==

## Additional Information

==中间有一部分还未翻译.......==

### Calculating Majority for Write Concern

> **TIP**
>
> The rs.status() returns the writeMajorityCount field which contains the calculated majority number.
> rs.status()返回writeMajorityCount字段，其中包含计算的多数数量。

The majority for write concern "majority" is calculated as the smaller of the following values:

- the majority of all voting members (including arbiters) vs.

- the number of all data-bearing voting members.

写关注"majority"的多数计算为以下较小值：

- 所有投票成员（包括仲裁者）的多数.

- 所有数据承载投票成员的数量。

==中间有一部分还未翻译.......==
