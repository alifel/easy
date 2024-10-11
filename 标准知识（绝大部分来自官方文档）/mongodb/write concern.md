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


Calculating Majority for Write Concern

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