# concurrency control

Concurrency control ensures that database operations can be executed concurrently without compromising correctness. Pessimistic concurrency control, such as that used in systems with locks, blocks any potentially conflicting operations even if they may not conflict. Optimistic concurrency control, the approach used by WiredTiger, delays checking until after a conflict may have occurred, ending and retrying one of the operations in any write conflict.

并发控制确保数据库操作可以同时执行，而不影响其正确性。悲观并发控制（如使用锁的系统）会阻止任何可能冲突的操作，即使它们实际上可能不会发生冲突。而乐观并发控制（如WiredTiger采用的方式）则延迟检查，直到可能发生冲突后才进行检测，并在发生写冲突时终止并重试其中一个操作。

