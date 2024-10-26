# Journal

A sequential, binary transaction log used to bring the database into a valid state in the event of a hard shutdown. Journaling writes data first to the journal and then to the core data files. MongoDB enables journaling by default for 64-bit builds of MongoDB version 2.0 and newer. Journal files are pre-allocated and exist as files in the data directory. See Journaling.

一种顺序的二进制事务日志，用于在系统意外关闭的情况下将数据库恢复到有效状态。日志记录首先将数据写入日志文件，然后再写入核心数据文件。对于MongoDB 2.0及以上版本的64位构建，MongoDB默认启用日志记录功能。日志文件会预先分配空间，并以文件的形式存在于数据目录中。详见日志记录。
