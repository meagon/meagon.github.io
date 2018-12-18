---
title: "Ck Source Code Read"
date: 2018-12-19T00:38:54+08:00
draft: false
author: "次郎"
---



```
pipeline
```
是一个重要的数据结构
通过函数executeFetchColumns 来拉取数据

`stream_with_non_join_data` 

When executing FULL or RIGHT JOIN, there will be a data stream from which you can read "not joined" rows.
用于UnionBlockInputStream和`ParallelAggregatingBlockInputStream`


