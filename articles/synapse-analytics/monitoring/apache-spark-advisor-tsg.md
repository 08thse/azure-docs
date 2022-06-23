---
title: Spark Advisor
description: Spark Advisor would be a system to automatically analyze commands/queries, and show the appropriate advise when customer execute code or query.
services: synapse-analytics 
author: jejiang
ms.author: jejiang
ms.reviewer: sngun 
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 06/23/2022
ms.custom: references_regions
---

# Spark Advisor

Spark Advisor would be a system to automatically analyze commands/queries, and show the appropriate advise when customer execute code or query. After apply the advise, you would have chance to improve your execution performance, decrease cost and fix the execution failures.


## Advises provided

### 1. May return inconsistent results when using 'randomSplit'
Inconsistent or inaccurate results may be returned when working with the results of the 'randomSplit' method. Use Apache Spark (RDD) caching before using the 'randomSplit' method.

Method randomSplit() is equivalent to performing sample() on your data frame multiple times, with each sample re-fetching, partitioning, and sorting your data frame within partitions. The data distribution across partitions and sorting order is important for both randomSplit() and sample(). If either change upon data re-fetch, there may be duplicates or missing values across splits and the same sample using the same seed may produce different results.

These inconsistencies may not happen on every run, but to eliminate them completely, cache your data frame, repartition on a column(s), or apply aggregate functions such as groupBy.

### 2. Table/View Name is already in use
A view already exists with the same name as the created table, or a table already exists with the same name as the created view. 
When this name is used in queries or applications, only the view will be returned no matter which one created first. To avoid conflicts, rename either the table or the view. 

### 3. Hints related advise
#### 3.1 Unable to recognize a hint
The selected query contains a hint that is not recognized. Verify that the hint is spelled correctly.

```scala
spark.sql("SELECT /*+ unknownHint */ * FROM t1")
```

#### 3.2 Unable to find a specified Relation name(s)
Unable to find the relation(s) specified in the hint. Verify that the relation(s) are spelled correctly and accessible within the scope of the hint.

```scala
spark.sql("SELECT /*+ BROADCAST(unknownTable) */ * FROM t1 INNER JOIN t2 ON t1.str = t2.str")
```

#### 3.3 A hint in the query prevents another hint from being applied
The selected query contains a hint that prevents another hint from being applied.

```scala
spark.sql("SELECT /*+ BROADCAST(t1), MERGE(t1, t2) */ * FROM t1 INNER JOIN t2 ON t1.str = t2.str")
```

### 4. Enable 'spark.advise.divisionExprConvertRule.enable' to reduce rounding error propagation
This query contains the expression with Double type. We recommend that you enable the configuration 'spark.advise.divisionExprConvertRule.enable' which can help reduce the division expressions and to reduce the rounding error propagation.

```text
"t.a/t.b/t.c" convert into "t.a/(t.b * t.c)"
```

### 5. Enable 'spark.advise.nonEqJoinConvertRule.enable' to improve query performance
This query contains time consuming join due to "Or" condition within query. We recommend that you enable the configuration 'spark.advise.nonEqJoinConvertRule.enable' which can help to convert the join triggered by "Or" condition to SMJ or BHJ to accelerate this query.
