# Redis 中常见的数据类型有哪些？

## 最基础的数据类型

1. String（字符串）

可存文本、数字、二进制数据。适用于缓存（存储临时数据），计数器（统计数量）。

2. Hash（哈希）

存储对象。适用于存储某一个含有多个属性的事物。

3. List（列表）

有序的，可重复的元素的集合。适用于消息队列，历史记录等。

4. Set（集合）

无序的，唯一的元素集合。适用于标签系统，以及唯一用户的统计。

5. Sorted Set（有序集合）

带排序权重的唯一元素的集合。适用于排行榜。

