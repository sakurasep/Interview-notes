# MySQL 事务的二阶段提交是什么？

MySQL 事务的二阶段提交是为了确保 redo log 和 binlog 的一致性而使用的一种机制。确保 MySQL 在崩溃恢复的时候不会出现数据丢失和数据不一致的问题。

二阶段提交的流程如下：

1. 准备阶段：

在事务提交的时候，MySQL 首先会记录 redo log，并将其标记为 prepare 状态，表示事务已经准备好提交但是还没有提交完成。

2. 提交阶段：

当 redo log 的状态变成 prepare 状态后，MySQL 会写入 binlog，当 binlog 写入完成后，MySQL 会将 redo log 标记为 commit 状态，完成整个事务的提交过程。