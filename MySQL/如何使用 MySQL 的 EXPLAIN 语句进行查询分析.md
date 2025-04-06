## 使用 EXPLAIN 进行分析

1. 检查 type 列

```
system > const > eq_ref > ref > range > index > ALL
```

2. 注意 key 列，看看 SQL 语句是否和自己预想中使用的索引一致
3. 估算 row 列，尽量使得 row 值接近实际的数据返回行数
4. 注意 Extra 列的提示