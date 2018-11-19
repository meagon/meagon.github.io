
---
title: ck代码阅读，数据流
date: 20181120

```
pipeline
```
是一个重要的数据结构
通过函数executeFetchColumns 来拉取数据

`stream_with_non_join_data` 
When executing FULL or RIGHT JOIN, there will be a data stream from which you can read "not joined" rows.
用于UnionBlockInputStream和`ParallelAggregatingBlockInputStream`

