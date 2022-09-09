---
title: Read Cassandra API table data using Spark
titleSufix: Azure Cosmos DB
description: This article describes how to read data from Cassandra API tables in Azure Cosmos DB.
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 06/02/2020
ms.custom: seodec18
---

# Read data from Azure Cosmos DB Cassandra API tables using Spark

 This article describes how to read data stored in Azure Cosmos DB Cassandra API from Spark.

## Cassandra API configuration
```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra

//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")
//Throughput-related...adjust as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```
## Dataframe API

### Read table using session.read.format command

```scala
val readBooksDF = sqlContext
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load

readBooksDF.explain
readBooksDF.show
```
### Read table using spark.read.cassandraFormat 

```scala
val readBooksDF = spark.read.cassandraFormat("books", "books_ks", "").load()
```

### Read specific columns in table

```scala
val readBooksDF = spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .select("book_name","book_author", "book_pub_year")

readBooksDF.printSchema
readBooksDF.explain
readBooksDF.show
```

### Apply filters

You can push down predicates to the database to allow for better optimized Spark queries. A predicate is a condition on a query that returns true or false, typically located in the WHERE clause. A predicate push down filters the data in the database query, reducing the number of entries retrieved from the database and improving query performance. By default the Spark Dataset API will automatically push down valid WHERE clauses to the database. 

```scala
val readBooksDF = spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .select("book_name","book_author", "book_pub_year")
  .filter("book_pub_year > 1891")
//.filter("book_name IN ('A sign of four','A study in scarlet')")
//.filter("book_name='A sign of four' OR book_name='A study in scarlet'")
//.filter("book_author='Arthur Conan Doyle' AND book_pub_year=1890")
//.filter("book_pub_year=1903")  

readBooksDF.printSchema
readBooksDF.explain
readBooksDF.show
```

The PushedFilters section of the physical plan includes the GreaterThan push down filter. 

:::image type="content" source="./media/cassandra-spark-read-ops/pushdown-predicates.png" alt-text="partitions":::

## RDD API

### Read table
```scala
val bookRDD = sc.cassandraTable("books_ks", "books")
bookRDD.take(5).foreach(println)
```

### Read specific columns in table

```scala
val booksRDD = sc.cassandraTable("books_ks", "books").select("book_id","book_name").cache
booksRDD.take(5).foreach(println)
```

## SQL Views 

### Create a temporary view from a dataframe

```scala
spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load.createOrReplaceTempView("books_vw")
```

### Run queries against the view

```sql
select * from books_vw where book_pub_year > 1891
```

## Next steps

The following are additional articles on working with Azure Cosmos DB Cassandra API from Spark:
 
 * [Upsert operations](cassandra-spark-upsert-ops.md)
 * [Delete operations](cassandra-spark-delete-ops.md)
 * [Aggregation operations](cassandra-spark-aggregation-ops.md)
 * [Table copy operations](cassandra-spark-table-copy-ops.md)

