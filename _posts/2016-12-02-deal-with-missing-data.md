---
file_name: "_posts/2016-12-02-deal-with-missing-data.md"
layout: post
title:  "Deal with missing data"
date:   2016-12-3 12:00:00 +0800
# modified: 2016-12-3 09:30:48 +0800
categories: spark sql
tags: scala pipeline
author: iampaul83
permalink: /spark/sql/deal-with-missing-data
sidebar:
  nav: "spark"
---

Spark SQL有一個`DataFrameNaFunctions`可以很方便地處理missing data


## DataFrameNaFunctions

> 這邊的NA指的是 __missing value__ （跟程式沒關），而`NaN`跟`null`是在scala裡面會看到的資料。
> 在文件裡面，會用 __null or NaN__ 這兩個字，例如：
> “Returns a new DataFrame that drops rows containing any __null or NaN__ values in the specified columns.”。
> 在 __DataFrameNaFunctions__ 沒有區分NaN與null，一律當做missing value處理。

[DataFrameNaFunctions](http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.DataFrameNaFunctions)有三種方法：
- `drop`: 把有NA的row刪掉
- `fill`: 把NA用指定的資料填滿
- `replace`: 這個跟NA沒關係。把一個值用另一個值取代，例如 "N" -> "UNKNOWN"

---

用`dataframe.na`取得`DataFrameNaFunctions`

```scala
val df = spark.read
    .parquet("hdfs://namenode-0.hdfs.mesos:9001/parquet/ehave.parquet")
val newDf = df.na.drop()
```

## drop

把有NA的row刪掉

### drop mode

- __any__: 一個row如果有**任何一個**NA，則drop
- __all__: 一個row如果**全部**欄位都是NA，則drop

> __口訣__： drop if ___any___ column is NA, drop if ___all___ the columns is NA

```scala
// 預設any
val dfWithoutNA = df.na.drop()
val dffWithoutDummyData = df.na.drop("all")
```

### 指定欄位

只會檢查指定欄位中的NA

```scala
// df2的col1與col2**不會有**NA
val df2 = df.na.drop(Array("col1", "col2"))

// df3的col1與col2**不會都是**NA
val df3 = df.na.drop("all", Array("col1", "col2"))
```

---

> 以下施工中，請看[DataFrameNaFunctions](http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.DataFrameNaFunctions)

## fill

把NA用指定的資料填滿


----

## replace

這個跟NA沒關係。把一個值用另一個值取代，例如 "N" -> "UNKNOWN"
