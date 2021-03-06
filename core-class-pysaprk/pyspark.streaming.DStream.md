---
title: pyspark.streaming.DStream
---

## 类pyspark.streaming.DStream

A Discretized Stream (DStream), the basic abstraction in Spark Streaming, is a continuous sequence of RDDs (of the same type) representing a continuous stream of data (see RDD in the Spark core documentation for more details on RDDs).
"离散流" (DStream)是Spark流中的基本抽象,它是表示连续数据流的Rdd(相同类型)的连续序列。

DStreams can either be created from live data (such as, data from TCP sockets, Kafka, Flume, etc.) using a StreamingContext or it can be generated by transforming existing DStreams using operations such as map, window and reduceByKeyAndWindow. While a Spark Streaming program is running, each DStream periodically generates a RDD, either from live data or by transforming the RDD generated by a parent DStream

Dstream可以使用流上下文从实时数据(例如来自TCP sockets、Kafka、Flume等的数据)创建,也可以通过使用map、window和reduceBykeyandandWindow等操作转换现有Dstream来生成。在运行Spark流程序时, 每个DStream都会定期从实时数据或通过转换父DStream生成的RDD生成RDD

DStreams internally is characterized by a few basic properties 
在DStreams内部特点是，有一些基本的属性

A list of other DStreams that the DStream depends on    DStreams依赖链
A time interval at which the DStream generates an RDD   DStream生成RDD的时间间隔
A function that is used to generate an RDD after each time interval 一个用于在一段时间生成RDD的函数

### 类pyspark.streaming.DStream 参数
class pyspark.streaming.DStream(jdstream, ssc, jrdd_deserializer)

###  pyspark.streaming.DStream类函数和属性

-   **cache()**
    Persist the RDDs of this DStream with the default storage level (MEMORY_ONLY).
    使用默认存储级别 (MEMORY_ONLY)保留此DStream的Rdd。

-   **checkpoint(interval)**
    Enable periodic checkpointing of RDDs of this DStream
    启用此 DStream 的 Rdd 的定期检查点,设置定期检查时间

对于pyspark.streaming.DStream 中的相关函数，和spark.RDD中的大致一样，由于都是对RDD的操作。这里只列举一些DStream中特有的。

-   **pprint(num=10)**
    Print the first num elements of each RDD generated in this DStream.
    输出显示在此DStream中生成的每个RDD的第一个num元素。

-   **slice(begin, end)**
    Return all the RDDs between ‘begin’ to ‘end’ (both included),begin, end could be datetime.datetime() or unix_timestamp
    返回在begin和end间的RDD；begin和end可以是datetime.datetime时间或unix_timestamp

-   **window(windowDuration, slideDuration=None)**
    Return a new DStream in which each RDD contains all the elements in seen in a sliding window of time over this DStream.
    返回一个新的 DStream, 其中每个 RDD 包含在此 DStream 上的时间滑动窗口中看到的所有元素。

    windowDuration – width of the window; must be a multiple of this DStream’s batching interval
    窗口持续时间–窗口的宽度;必须是此DStream的批处理间隔的倍数

    slideDuration – sliding interval of the window (i.e., the interval after which the new DStream will generate RDDs); must be a multiple of this DStream’s batching interval
    滑动持续时间–窗口的滑动间隔 (即, 在该时间间隔之后, 新的 DStream 将生成 Rdd);必须是此 DStream 的批处理间隔的倍数




