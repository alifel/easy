# Journaling

To provide durability in the event of a failure, MongoDB uses write ahead logging to on-disk journal files.

为了提供在故障情况下的持久性，MongoDB使用写前日志记录到磁盘上的日志文件。

## Journaling and the WiredTiger Storage Engine

> Important
> The log mentioned in this section refers to the WiredTiger write-ahead log (i.e. the journal) and not the MongoDB log file.
> 本节提到的日志是指WiredTiger预写日志（即日志文件），而非MongoDB的日志文件。

WiredTiger uses checkpoints to provide a consistent view of data on disk and allow MongoDB to recover from the last checkpoint. However, if MongoDB exits unexpectedly in between checkpoints, journaling is required to recover information that occurred after the last checkpoint.

WiredTiger使用检查点来提供磁盘数据的一致视图，并使MongoDB能够从最后一个检查点恢复数据。然而，如果MongoDB在检查点之间意外退出，则需要通过日志记录来恢复最后一个检查点之后发生的数据。

> Note
> You cannot specify --nojournal option or storage.journal.enabled: false for replica set members that use the WiredTiger storage engine.
> 对于使用WiredTiger存储引擎的副本集成员，无法指定 --nojournal 选项或 storage.journal.enabled: false。

With journaling, the recovery process:

1. Looks in the data files to find the identifier of the last checkpoint.

2. Searches in the journal files for the record that matches the identifier of the last checkpoint.

3. Apply the operations in the journal files since the last checkpoint.

启用日志记录后，恢复过程如下：

1. 在数据文件中查找最后一个检查点的标识符。

2. 在日志文件中搜索与最后一个检查点标识符匹配的记录。

3. 从最后一个检查点开始，应用日志文件中的操作。

### Journaling Process

With journaling, WiredTiger creates one journal record for each client initiated write operation. The journal record includes any internal write operations caused by the initial write. For example, an update to a document in a collection may result in modifications to the indexes; WiredTiger creates a single journal record that includes both the update operation and its associated index modifications.

启用日志记录后，WiredTiger为每个客户端发起的写操作创建一条日志记录。该日志记录包括初始写操作导致的任何内部写操作。例如，对集合中文档的更新可能会导致索引的修改；WiredTiger会创建一条包含更新操作及其关联索引修改的日志记录。

MongoDB configures WiredTiger to use in-memory buffering for storing the journal records. Threads coordinate to allocate and copy into their portion of the buffer. All journal records up to 128 kB are buffered.

MongoDB将WiredTiger配置为使用内存缓冲区来存储日志记录。线程协调分配各自的缓冲区并复制数据。所有小于128 kB的日志记录都会被缓存在内存中。

WiredTiger syncs the buffered journal records to disk upon any of the following conditions:

- For replica set members (primary and secondary members):

  - If a write operation includes or implies a write concern of j: true.

  - Additionally for secondary members, after every batch application of the oplog entries.

> Note
> Write concern "majority" implies j: true if the writeConcernMajorityJournalDefault is true.

- At every 100 milliseconds (See storage.journal.commitIntervalMs).

- When WiredTiger creates a new journal file. Because MongoDB uses a journal file size limit of 100 MB, WiredTiger creates a new journal file approximately every 100 MB of data.

在以下情况下，WiredTiger会将缓冲的日志记录同步到磁盘：

- 对于副本集成员（主节点和从节点）：
  - 如果写操作包含或暗示了写关注选项 j: true。
  - 对于从节点，在每批次应用oplog条目后。

> 注意
> 如果 writeConcernMajorityJournalDefault 设置为true，则写关注级别“majority”隐含j: true。

- 每隔100毫秒（详见 storage.journal.commitIntervalMs）。
- 当WiredTiger创建一个新的日志文件时。由于MongoDB的日志文件大小限制为100 MB，WiredTiger大约每100 MB的数据会创建一个新的日志文件。

> Important
> In between write operations, while the journal records remain in the WiredTiger buffers, updates can be lost following a hard shutdown of mongod.
> Tip
> See also:
> The serverStatus command returns information on the WiredTiger journal statistics in the wiredTiger.log field.
> 重要提示
> 在写操作之间，当日志记录仍然保存在WiredTiger缓冲区中时，如果mongod发生硬关机，更新可能会丢失。
> 提示
> 另请参阅：
> serverStatus命令会在 wiredTiger.log 字段中返回有关WiredTiger日志统计信息。

### Journal Files

For the journal files, MongoDB creates a subdirectory named `journal` under the `dbPath` directory. WiredTiger journal files have names with the following format `WiredTigerLog.<sequence>` where `<sequence>` is a zero-padded number starting from `0000000001`.

对于日志文件，MongoDB在 dbPath 目录下创建一个名为 journal 的子目录。WiredTiger的日志文件名称格式为 WiredTigerLog.<sequence>，其中 <sequence> 是从 0000000001 开始的零填充数字。

#### Journal Records

Journal files contain a record per each client initiated write operation

- The journal record includes any internal write operations caused by the initial write. For example, an update to a document in a collection may result in modifications to the indexes; WiredTiger creates a single journal record that includes both the update operation and its associated index modifications.
- Each record has a unique identifier.
- The minimum journal record size for WiredTiger is 128 bytes.

日志文件包含每个客户端发起的写操作的记录：

- 日志记录包括由初始写操作引发的任何内部写操作。例如，更新集合中的一个文档可能会导致索引的修改；WiredTiger会创建一个包含更新操作及其关联索引修改的单一日志记录。
- 每条记录都有一个唯一的标识符。
- WiredTiger的日志记录的最小大小为128字节。

#### Compression

By default, MongoDB configures WiredTiger to use snappy compression for its journaling data. To specify a different compression algorithm or no compression, use the `storage.wiredTiger.engineConfig.journalCompressor` setting. For details, see Change WiredTiger Journal Compressor.

默认情况下，MongoDB配置WiredTiger使用Snappy压缩来处理日志数据。要指定不同的压缩算法或取消压缩，可以使用 storage.wiredTiger.engineConfig.journalCompressor 设置。详情请参阅更改WiredTiger日志压缩器。

> Note
> If a log record less than or equal to 128 bytes (the mininum log record size for WiredTiger), WiredTiger does not compress that record.
> 如果日志记录小于或等于128字节（WiredTiger的最小日志记录大小），WiredTiger不会压缩该记录。

#### Journal File Size Limit

WiredTiger journal files have a maximum size limit of approximately 100 MB. Once the file exceeds that limit, WiredTiger creates a new journal file.

WiredTiger的日志文件大小限制为约100 MB。一旦文件超过此限制，WiredTiger会创建一个新的日志文件。

WiredTiger automatically removes old journal files and maintains only the files needed to recover from the last checkpoint. To determine how much disk space to set aside for journal files, consider the following:

- The default maximum size for a checkpoint is 2 GB
- Additional space may be required for MongoDB to write new journal files while recovering from a checkpoint
- MongoDB compresses journal files
- The time it takes to restore a checkpoint is specific to your use case
- If you override the maximum checkpoint size or disable compression, your calculations may be significantly different

WiredTiger会自动删除旧的日志文件，仅保留从最后一个检查点恢复所需的文件。要确定为日志文件预留多少磁盘空间，请考虑以下几点：

- 检查点的默认最大大小为2 GB
- 从检查点恢复时，MongoDB可能需要额外空间来写入新的日志文件
- MongoDB会压缩日志文件
- 恢复检查点所需时间取决于具体使用场景
- 如果覆盖了最大检查点大小或禁用压缩，所需空间可能会显著不同

For these reasons, it is difficult to calculate exactly how much additional space you need. Over-estimating disk space is always a safer approach.

因此，很难准确计算所需的额外空间。预留更多磁盘空间通常是更安全的做法。

> Important
> If you do not set aside enough disk space for your journal files, the MongoDB server will crash.
> 如果未为日志文件预留足够的磁盘空间，MongoDB服务器将崩溃。

#### Pre-Allocation

WiredTiger pre-allocates journal files.

## Journaling and the In-Memory Storage Engine

In MongoDB Enterprise, the In-Memory Storage Engine is part of general availability (GA). Because its data is kept in memory, there is no separate journal. Write operations with a write concern of `j: true` are immediately acknowledged.

在MongoDB Enterprise中，内存存储引擎已正式发布。由于数据存储在内存中，因此没有单独的日志文件。写关注为 j: true 的写操作会立即被确认。

If any voting member of a replica set uses the in-memory storage engine, you must set `writeConcernMajorityJournalDefault` to `false`.

如果副本集中的任何投票成员使用内存存储引擎，则必须将 writeConcernMajorityJournalDefault 设置为 false。

> Note
> Starting in version 4.2 (and 4.0.13 and 3.6.14 ), if a replica set member uses the in-memory storage engine (voting or non-voting) but the replica set has `writeConcernMajorityJournalDefault` set to true, the replica set member logs a startup warning.
> 从版本4.2（以及4.0.13和3.6.14）开始，如果副本集成员使用内存存储引擎（无论是否为投票成员），但副本集的 writeConcernMajorityJournalDefault 设置为true，该副本集成员在启动时会记录警告。

With `writeConcernMajorityJournalDefault` set to false, MongoDB does not wait for `w: "majority"` writes to be written to the on-disk journal before acknowledging the writes. As such, `"majority"` write operations could possibly roll back in the event of a transient loss (e.g. crash and restart) of a majority of nodes in a given replica set.

在 writeConcernMajorityJournalDefault 设置为false的情况下，MongoDB不会等待 w: "majority" 的写操作写入磁盘日志文件后再确认。因此，如果给定副本集的大多数节点暂时丢失（如崩溃并重启），则“majority”写操作可能会被回滚。

> Tip
> See also:
> In-Memory Storage Engine: Durability
