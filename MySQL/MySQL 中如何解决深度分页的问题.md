# MySQL 中如何解决深度分页的问题？

```sql
-- 典型分页查询
SELECT * FROM user ORDER BY id LIMIT 100000, 20;
```

在这种情况下，MySQL 需要扫描 100000 行，然后返回 20 行，这造成了大量的资源浪费。所以我们应该从以下几种方式来解决这个问题。

1. 子查询

```sql
-- 优化后查询（先查索引，再关联）
SELECT * FROM user a INNER JOIN (
    SELECT id FROM user ORDER BY id LIMIT 100000, 20
) b ON a.id = b.id;
```

这种情况，先从索引中直接查到第 100000 行，然后获取接下来的 20 行的 id，再通过 id 关联查询主表的数据行，大大减少了查询的行数，提高了查询效率。

2. 记录 id

```sql
-- 记录 id
SELECT * FROM user WHERE id > last_id ORDER BY id LIMIT 20;
```

这种情况适用于连续分页的场景，比如分页查询用户列表，用户列表的 id 是连续的，所以可以记录上一次查询的最后一个 id，然后查询下一个 20 条数据。但是对于随机跳页的场景，这种方式就不适用了。

