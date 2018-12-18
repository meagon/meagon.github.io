---
title: "Postgres Delete 大量数据"
date: 2018-11-11T00:41:01+08:00
draft: false
author: 次郎
---


postgres 删除大量数据，该表没有 条件索引

tidjoin.c 中有写 join 在tid scan中的优化已经取消了，所以第一个语句不是最优的

大量数据的时候 直接where 就行.

数据量超过一定范围，可以创建新的表，SELECT INTO 导入，rename表名

```
lambda@127:postgres> explain SELECT id FROM t1 where ctid in(SELECT ctid FROM t1
  where id <3);                                                                 
+----------------------------------------------------------------------+
| QUERY PLAN                                                           |
|----------------------------------------------------------------------|
| Merge Semi Join  (cost=121.78..133.38 rows=387 width=4)              |
|   Merge Cond: (t1.ctid = t1_1.ctid)                                  |
|   ->  Sort  (cost=80.64..83.54 rows=1160 width=10)                   |
|         Sort Key: t1.ctid                                            |
|         ->  Seq Scan on t1  (cost=0.00..21.60 rows=1160 width=10)    |
|   ->  Sort  (cost=41.13..42.10 rows=387 width=6)                     |
|         Sort Key: t1_1.ctid                                          |
|         ->  Seq Scan on t1 t1_1  (cost=0.00..24.50 rows=387 width=6) |
|               Filter: (id < 3)                                       |
+----------------------------------------------------------------------+
EXPLAIN
Time: 0.056s
lambda@127:postgres> explain SELECT id FROM t1 where ctid ='(0,1)'::tid;        
+--------------------------------------------------+
| QUERY PLAN                                       |
|--------------------------------------------------|
| Tid Scan on t1  (cost=0.00..4.01 rows=1 width=4) |
|   TID Cond: (ctid = '(0,1)'::tid)                |
+--------------------------------------------------+
EXPLAIN
Time: 0.009s
lambda@127:postgres> explain SELECT id FROM t1 where ctid ='(0,1)'::tid or ctid=
 '(0,3)'::tid;                                                                  
+--------------------------------------------------------------+
| QUERY PLAN                                                   |
|--------------------------------------------------------------|
| Tid Scan on t1  (cost=0.01..8.03 rows=2 width=4)             |
|   TID Cond: ((ctid = '(0,1)'::tid) OR (ctid = '(0,3)'::tid)) |
+--------------------------------------------------------------+
EXPLAIN
Time: 0.008s
lambda@127:postgres> explain SELECT id FROM t1 where id in (1,2,3);             
+----------------------------------------------------+
| QUERY PLAN                                         |
|----------------------------------------------------|
| Seq Scan on t1  (cost=0.00..25.95 rows=17 width=4) |
|   Filter: (id = ANY ('{1,2,3}'::integer[]))        |
+----------------------------------------------------+

```


