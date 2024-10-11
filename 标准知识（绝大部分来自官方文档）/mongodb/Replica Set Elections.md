# Replica Set Elections

Replica sets use elections to determine which set member will become primary. Replica sets can trigger an election in response to a variety of events, such as:

- Adding a new node to the replica set,
- Initiating a replica set,
- Performing replica set maintenance using methods such as rs.stepDown() or rs.reconfig(), and
- The secondary members losing connectivity to the primary for more than the configured timeout (10 seconds by default).

副本集使用选举来确定哪个节点将成为主节点。副本集可以在多种事件下触发选举，例如：

- 向副本集添加新节点，
- 启动副本集，
- 使用rs.stepDown()或rs.reconfig()等方法执行副本集维护，
- 副本集的secondary成员与主节点失去连接超过配置的超时时间（默认为10秒）。

In the following diagram, the primary node was unavailable for longer than the configured timeout and triggers the automatic failover process. One of the remaining secondaries calls for an election to select a new primary and automatically resume normal operations.

在下图中，主节点由于超过配置的超时时间而无法访问，触发自动故障转移过程。剩余的secondary之一发起选举以选择新的主节点，并自动恢复正常操作。

![image](./images/replica-set-trigger-election.bakedsvg.svg)

The replica set cannot process write operations until the election completes successfully. The replica set can continue to serve read queries if such queries are configured to run on secondaries.

副本集在选举成功完成之前无法处理写操作。如果配置为在secondary上运行，副本集可以继续处理读查询。

The median time before a cluster elects a new primary should not typically exceed 12 seconds, assuming default replica configuration settings. This includes time required to mark the primary as unavailable and call and complete an election. You can tune this time period by modifying the settings.electionTimeoutMillis replication configuration option. Factors such as network latency may extend the time required for replica set elections to complete, which in turn affects the amount of time your cluster may operate without a primary. These factors are dependent on your particular cluster architecture.

在默认副本配置设置下，集群选举新主节点的时间通常不应超过12秒。这包括标记主节点为不可用、发起并完成选举所需的时间。可以通过修改settings.electionTimeoutMillis复制配置选项来调整此时间。网络延迟等因素可能会延长副本集选举完成所需的时间，从而影响集群在无主节点情况下正常运行的时间。这些因素取决于特定的集群架构。

Your application connection logic should include tolerance for automatic failovers and the subsequent elections. MongoDB drivers can detect the loss of the primary and automatically retry certain write operations a single time, providing additional built-in handling of automatic failovers and elections:

Compatible drivers enable retryable writes by default

你的应用程序连接逻辑应包括对自动故障转移和后续选举的容忍度。MongoDB驱动程序可以检测主节点的丢失，并自动重试1次必要的写操作，从而提供对自动故障转移和选举的额外内置处理：

兼容的驱动程序默认启用可重试写入

## Factors and Conditions that Affect Elections

### Replication Election Protocol

Replication protocolVersion: 1 reduces replica set failover time and accelerate the detection of multiple simultaneous primaries.

You can use catchUpTimeoutMillis to prioritize between faster failovers and preservation of w:1 writes.

For more information on pv1, see Self-Managed Replica Set Protocol Version.
