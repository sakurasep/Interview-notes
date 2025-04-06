# MySQL 中如何进行 SQL 调优？

简单的从三方面来考虑
* SQL 语句优化
* 索引设计优化
* 使用 EXPLAIN 进行分析

## 语句优化

1. 精简查询字段

比如说尽量少用`SELECT *`，只查询必要的字段

2. 多表 join 的优化

比如说 user 表和 order 表，在对两个表进行关联的时候，注意使用索引字段进行连接
```sql
-- 优化前（无索引字段连接）
SELECT * FROM users u JOIN orders o ON u.username = o.customer_name;

-- 优化后（使用索引字段连接）
ALTER TABLE users ADD INDEX (user_id);
ALTER TABLE orders ADD INDEX (user_id);
SELECT * FROM users u JOIN orders o ON u.user_id = o.user_id;
```

其次就是优化一下连接类型，尽量用小表驱动大表

|   连接类型    |           使用场景           |              示例              |
| :-----------: | :--------------------------: | :----------------------------: |
|  INNER JOIN   |  只查询有匹配的记录（默认）  |      `JOIN orders ON...`       |
|   LEFT JOIN   |       保留左表所有记录       |    `LEFT JOIN orders ON...`    |
|  RIGHT JOIN   | 保留右表所有记录（较少使用） |   `RIGHT JOIN orders ON...`    |
| STRAIGHT_JOIN |      强制指定表连接顺序      | `STRAIGHT_JOIN A JOIN B ON...` |

3. 控制结果集大小

使用 LIMIT 分页或者是使用 WHERE 条件过滤

## 索引优化

主要是注意一下 SQL 语句使用的时候索引是否失效

比如说 SQL 语句中不要进行函数计算等操作，使用 like 进行模糊匹配的时候要注意不要使用前导匹配符，然后就是联合索引注意最左匹配原则等等。

## 慢 SQL

慢 SQL 是指执行时间超过预设阈值的 SQL 语句，通常表现为：

- 执行时间过长（如超过 1 秒）
- 消耗大量系统资源（CPU、内存、I/O）
- 导致连接堆积，影响整体数据库性能

```sql
SET GLOBAL slow_query_log = 'ON';
SET GLOBAL long_query_time = 1; -- 超过1秒记录
SET GLOBAL log_queries_not_using_indexes = 'ON';
```

查到具体是哪一句 SQL 然后用 EXPLAIN 进行分析

## 使用 EXPLAIN 进行分析

1. 检查 type 列

```
system > const > eq_ref > ref > range > index > ALL
```

2. 注意 key 列，看看 SQL 语句是否和自己预想中使用的索引一致
3. 估算 row 列，尽量使得 row 值接近实际的数据返回行数
4. 注意 Extra 列的提示