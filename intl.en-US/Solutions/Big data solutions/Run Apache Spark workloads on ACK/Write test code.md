---
keyword: [Spark benchmarks, Spark SQL, TPC-DS, Create an image]
---

# Write test code

Before you use the benchmarks to test the performance of Spark, write the code to generate test data based on TPC-DS and the code to run Spark SQL. This topic describes how to write the code and create an image.

[Set up a test environment](/intl.en-US/Solutions/Big data solutions/Run Apache Spark workloads on ACK/Set up a test environment.md)

The registry.cn-beijing.aliyuncs.com/yukong/ack-spark-benchmark:1.0.0 image is prepared. You can use it to simplify the testing procedure. For information about the code of the image, see [benchmark-for-spark](https://github.com/AliyunContainerService/benchmark-for-spark).

## Before you begin

The test code is written based on two Databricks-based tools: the TPC-DS test package and tpcds-kit. tpcds-kit generates test data.

1.  Run the following command to put the JAR dependencies of TPC-DS into a package:

    ```
    git clone https://github.com/databricks/spark-sql-perf.git
    sbt package
    ```

    The spark-sql-perf\_2.11-0.5.1-SNAPSHOT JAR package is generated. You can use it as the dependency of the test.

2.  Run the following commands to compile tpcds-kit:

    ```
    git clone https://github.com/davies/tpcds-kit.git
    yum install gcc gcc-c++ bison flex cmake ncurses-devel
    cd tpcds-kit/tools
    cp Makefile.suite Makefile# Replicate the Makefile.suite file and save the replica as the Makefile file.
    make  
    # Verify the result of the compilation.
    ./dsqgen --help
    ```

    After you compile the code, a binary executable program is generated. In this topic, dsdgen is used to generate test data, and dsqgen is used to generate queries.


## Write code

1.  To generate test data, create and configure the DataGeneration.scala file by running the following code:

    ```
    package com.aliyun.spark.benchmark.tpcds
    
    import com.databricks.spark.sql.perf.tpcds.TPCDSTables
    import org.apache.log4j.{ Level, LogManager}
    import org.apache.spark.sql.SparkSession
    
    import scala.util.Try
    
    object DataGeneration {
      def main(args: Array[String]) {
        val tpcdsDataDir = args(0)
        val dsdgenDir = args(1)
        val format = Try(args(2).toString).getOrElse("parquet")
        val scaleFactor = Try(args(3).toString).getOrElse("1")
        val genPartitions = Try(args(4).toInt).getOrElse(100)
        val partitionTables = Try(args(5).toBoolean).getOrElse(false)
        val clusterByPartitionColumns = Try(args(6).toBoolean).getOrElse(false)
        val onlyWarn = Try(args(7).toBoolean).getOrElse(false)
    
        println(s"DATA DIR is $tpcdsDataDir")
        println(s"Tools dsdgen executable located in $dsdgenDir")
        println(s"Scale factor is $scaleFactor GB")
    
        val spark = SparkSession
          .builder
          .appName(s"TPCDS Generate Data $scaleFactor GB")
          .getOrCreate()
    
        if (onlyWarn) {
          println(s"Only WARN")
          LogManager.getLogger("org").setLevel(Level.WARN)
        }
    
        val tables = new TPCDSTables(spark.sqlContext,
          dsdgenDir = dsdgenDir,
          scaleFactor = scaleFactor,
          useDoubleForDecimal = false,
          useStringForDate = false)
    
        println(s"Generating TPCDS data")
    
        tables.genData(
          location = tpcdsDataDir,
          format = format,
          overwrite = true, // overwrite the data that is already there
          partitionTables = partitionTables,  // create the partitioned fact tables
          clusterByPartitionColumns = clusterByPartitionColumns, // shuffle to get partitions coalesced into single files.
          filterOutNullPartitionValues = false, // true to filter out the partition with NULL key value
          tableFilter = "", // "" means generate all tables
          numPartitions = genPartitions) // how many dsdgen partitions to run - number of input tasks.
    
        println(s"Data generated at $tpcdsDataDir")
    
        spark.stop()
      }
    }
    ```

2.  To query data, create and configure the BenchmarkSQL.scala file by running the following code:

    ```
    package com.aliyun.spark.benchmark.tpcds
    
    import com.databricks.spark.sql.perf.tpcds.{ TPCDS, TPCDSTables}
    import org.apache.spark.sql.SparkSession
    import org.apache.spark.sql.functions._
    import org.apache.spark.sql.functions.col
    import org.apache.log4j.{ Level, LogManager}
    import scala.util.Try
    
    object BenchmarkSQL {
      def main(args: Array[String]) {
        val tpcdsDataDir = args(0)
        val resultLocation = args(1)
        val dsdgenDir = args(2)
        val format = Try(args(3).toString).getOrElse("parquet")
        val scaleFactor = Try(args(4).toString).getOrElse("1")
        val iterations = args(5).toInt
        val optimizeQueries = Try(args(6).toBoolean).getOrElse(false)
        val filterQueries = Try(args(7).toString).getOrElse("")
        val onlyWarn = Try(args(8).toBoolean).getOrElse(false)
    
        val databaseName = "tpcds_db"
        val timeout = 24*60*60
    
        println(s"DATA DIR is $tpcdsDataDir")
    
        val spark = SparkSession
          .builder
          .appName(s"TPCDS SQL Benchmark $scaleFactor GB")
          .getOrCreate()
    
        if (onlyWarn) {
          println(s"Only WARN")
          LogManager.getLogger("org").setLevel(Level.WARN)
        }
    
        val tables = new TPCDSTables(spark.sqlContext,
          dsdgenDir = dsdgenDir,
          scaleFactor = scaleFactor,
          useDoubleForDecimal = false,
          useStringForDate = false)
    
        if (optimizeQueries) {
          Try {
            spark.sql(s"create database $databaseName")
          }
          tables.createExternalTables(tpcdsDataDir, format, databaseName, overwrite = true, discoverPartitions = true)
          tables.analyzeTables(databaseName, analyzeColumns = true)
          spark.conf.set("spark.sql.cbo.enabled", "true")
        } else {
          tables.createTemporaryTables(tpcdsDataDir, format)
        }
    
        val tpcds = new TPCDS(spark.sqlContext)
    
        var query_filter : Seq[String] = Seq()
        if (! filterQueries.isEmpty) {
          println(s"Running only queries: $filterQueries")
          query_filter = filterQueries.split(",").toSeq
        }
    
        val filtered_queries = query_filter match {
          case Seq() => tpcds.tpcds2_4Queries
          case _ => tpcds.tpcds2_4Queries.filter(q => query_filter.contains(q.name))
        }
    
        // Start experiment
        val experiment = tpcds.runExperiment(
          filtered_queries,
          iterations = iterations,
          resultLocation = resultLocation,
          forkThread = true)
    
        experiment.waitForFinish(timeout)
    
        // Collect general results
        val resultPath = experiment.resultPath
        println(s"Reading result at $resultPath")
        val specificResultTable = spark.read.json(resultPath)
        specificResultTable.show()
    
        // Summarize results
        val result = specificResultTable
          .withColumn("result", explode(col("results")))
          .withColumn("executionSeconds", col("result.executionTime")/1000)
          .withColumn("queryName", col("result.name"))
        result.select("iteration", "queryName", "executionSeconds").show()
        println(s"Final results at $resultPath")
    
        val aggResults = result.groupBy("queryName").agg(
          callUDF("percentile", col("executionSeconds").cast("long"), lit(0.5)).as('medianRuntimeSeconds),
          callUDF("min", col("executionSeconds").cast("long")).as('minRuntimeSeconds),
          callUDF("max", col("executionSeconds").cast("long")).as('maxRuntimeSeconds)
        ).orderBy(col("queryName"))
        aggResults.repartition(1).write.csv(s"$resultPath/summary.csv")
        aggResults.show(105)
    
        spark.stop()
      }
    }
    ```


## Create an image

After you compile the test code into a JAR package, you can create a test image by combining this JAR package with other JAR dependencies of the test. You can use the following dockerfile:

```
FROM registry.cn-hangzhou.aliyuncs.com/acs/spark:ack-2.4.5-f757ab6
RUN mkdir -p /opt/spark/jars
RUN mkdir -p /tmp/tpcds-kit
COPY ./target/scala-2.11/ack-spark-benchmark-assembly-0.1.jar /opt/spark/jars/
COPY ./lib/*.jar /opt/spark/jars/
COPY ./tpcds-kit/tools.tar.gz /tmp/tpcds-kit/
RUN cd /tmp/tpcds-kit/ && tar -xzvf tools.tar.gz
```

[Run Spark benchmarks on ACK]()

