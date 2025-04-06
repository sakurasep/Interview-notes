# MySQL 中使用索引一定有效吗？如何排查索引效果？

不一定有效。

## 索引失效的常见场景

1. 使用了联合索引但是不符合最左前缀原则

```sql
假设建立了 name_age_id 的联合索引
SELECT * FROM users WHERE age = 18 and id = 1;
```

2. 隐形的类型转换

隐形的类型转换本质还是因为索引中出现了函数CAST()，因为如下所示

```sql
SELECT * FROM users WHERE name = 123;

其实执行的是
SELECT * FROM users WHERE CAST(name AS signed int) = 123;

```

3. 索引中使用了函数

```sql
SELECT * FROM orders WHERE DATE(create_time) = '2025-01-01';
```

4. 使用模糊查询时错误使用前导通配符

```sql
错误示例
SELECT * FROM articles WHERE title LIKE '%数据库%';

正确示例
SELECT * FROM articles WHERE title LIKE '数据库%';
```

5. 使用过范围查询后的列

这种情况主要是设计联合索引的时候出现问题，应该将有可能需要进行范围查询的列靠右。

```sql
错误示例
SELECT * FROM orders WHERE amount > 100 AND status = 1;、

正确示例
SELECT * FROM orders WHERE status = 1 AND amount > 100;
```

## 如何排查索引效果

使用 EXPLAIN 分析

使用EXPLAIN会出现几个关键的字段，如下所示。

* type: 从优到差为 system > const > eq_ref > ref > range > index > ALL
* key: 实际使用的索引
* rows: 预估的扫描行数
* Extra: **Using index: 覆盖索引** **Using filesort: 需要额外排序** **Using temporary: 使用临时表**

