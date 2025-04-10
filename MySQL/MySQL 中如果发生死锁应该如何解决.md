# MySQL 中如果发生死锁应该如何解决？

1. 自动检测死锁并处理

MySQL 自带死锁检测机制，当检测到死锁时，数据库会自动回滚其中的一个事务，来解除死锁，通常回滚的是事务中持有资源最少的那个。

同时，当获取锁的时间超过阈值，也会自动释放锁进行回滚。

2. 手动检测死锁并处理

```sql
-- 查看最近发生的死锁信息
SHOW ENGINE INNODB STATUS
-- 重点查看"LATEST DETECTED DEADLOCK"部分
```

找到出现死锁的事务以及它的线程 ID，然后使用 `KILL` 命令终止该线程。