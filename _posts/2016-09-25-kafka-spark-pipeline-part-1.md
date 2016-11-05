---
layout:     post
title: "Building a Kafka and Spark Streaming pipeline - Part I"
date:       2016-09-25
author:     "Thomas Vincent"
header-img: "img/galaxy.jpg"
---

Many companies across a multitude of industries are currently maintaining data pipelines used to ingest and analyze large data streams. In effect, the proper implementation of such pipelines belongs to the realm of "data engineering", and represents a gateway to interesting data science-related problems. Traditional machine learning methods have been developed to work using batch or offline approaches, but there are fewer options when we start considering solutions for true streaming problems.

In this series of posts, we will build a locally hosted data streaming pipeline to analyze and process data streaming in real-time, and send the processed data to a monitoring dashboard. As the figure below shows, our high-level example of a real-time data pipeline will make use of popular tools including Kafka for message passing, Spark for data processing, and one of the _many_ data storage tools that eventually feeds into internal or external facing products (websites, dashboards etc...)

![Kafka and Spark streaming pipeline](/img/kafka_spark_pipeline.png)

## 1. Setting up your environnment
We will assume that you have nothing installed on your machine. To begin, it is useful to check whether you have `Java` installed or your machine, and if yes, whether it is at `version>=1.8`. 

{% highlight sh %}
TLV-private:ThomasVincent$ java -version
java version "1.8.0_51"
Java(TM) SE Runtime Environment (build 1.8.0_51-b16)
Java HotSpot(TM) 64-Bit Server VM (build 25.51-b03, mixed mode)
{% endhighlight %}

If that is the case, then we can proceed with the next steps, otherwise you might want to install Java using the instructions here: <https://java.com/en/download/help/mac_install.xml>

&nbsp;

## 2. Zookeeper

#### 2.1. Installing Zookeeper
Zookeeper is an Apache project specifically built with the intention of helping us build and maintain distributed applications. In short, it is an invaluable tool to take much of the heavy lifting out of building distributed processes. Some further explanations and useful links can be found at the following [StackOverflow link](http://stackoverflow.com/questions/3662995/explaining-apache-zookeeper)

To begin, go ahead and download Zookeeper (Release 3.4.9 (stable) ) from [this link](http://zookeeper.apache.org/releases.html). Once the tar-zipped file has been downloaded, move it to your working directory, unpack it, and change your working directory to the Zookeeper directory.

{% highlight sh %}
TLV-private:ThomasVincent$ mv zookeeper-3.4.9.tar.gz /path_to_working_directory/
TLV-private:ThomasVincent$ tar -zxf zookeeper-3.4.9.tar.gz
TLV-private:ThomasVincent$ cd zookeeper-3.4.9
{% endhighlight %}

&nbsp;

#### 2.2. Configuring Zookeeper
At this point, you can create a new directory `data` using the `mkdir` command.

{% highlight sh %}
TLV-private:ThomasVincent$ mkdir data
{% endhighlight %}

and also edit the Zookeeper configuration file located in the `conf` directory with the command 

{% highlight sh %}
vim conf/zoo.cfg
{% endhighlight %}

Note that vim will automatically create the `zoo.cfg` file if it does not already exist. You will usually find a heavily commented `conf/zoo_sample.cfg` file in most default installations of Zookeeper, but if not, insert the following 5 lines in the configuration file.

{% highlight sh %}
tickTime=2000
dataDir=/path_to_your_working_directory/zookeeper-3.4.9/data
clientPort=2181
initLimit=5
syncLimit=2
{% endhighlight %}

&nbsp;

#### 2.3. Starting Zookeeper
We are now ready to start our Zookeeper server, which can be achieved by running the `zkServer.sh` shell script:

{% highlight sh %}
TLV-private:ThomasVincent$ bin/zkServer.sh start
{% endhighlight %}

After executing this command, you should see the following output:

{% highlight sh %}
TLV-private:ThomasVincent$ ZooKeeper JMX enabled by default
Using config: /Users/ThomasVincent/Desktop/StatOfMind/kafka_spark_pipeline/zookeeper-3.4.9/bin/../conf/zoo.cfg
Starting zookeeper ... STARTED
{% endhighlight %}

You can also launch the Zookeeper CLI, which will allow you to connect to the Zookeeper server:

{% highlight sh %}
TLV-private:ThomasVincent$ bin/zkCli.sh
{% endhighlight %}

Executing this command should generate a fair amount of output, but you should see the following:

{% highlight sh %}
Connecting to localhost:2181
...
...
...
Welcome to ZooKeeper!
...
...
...
WATCHER::
WatchedEvent state:SyncConnected type:None path:null
{% endhighlight %}

&nbsp;

## 3. Apache Kafka
Apache Kafka is a high-throughput distributed messaging system in which multiple producers send data to a Kafka cluster and which in turn serves them to consumers. Because of its efficiency and resiliency, it has become one of the _de facto_ tool to consume and publish streaming data, with applications ranging from AdTech, IoT and logging data.


#### 3.1. Installing Kafka
Let's start by downloading the Kafka binary and installing it on our machine. For this, you will need to get version 2.11, which can be obtained [here](http://www.webhostingjams.com/mirror/apache/kafka/0.10.0.1/kafka_2.11-0.10.0.1.tgz). Once this is done, simply move the Kafka binary to your working directory (for simplicity, let's say the one where you placed your ZooKeeper binary) and unzip the Kafka binary.

{% highlight sh %}
TLV-private:ThomasVincent$ tar -zxf kafka_2.11-0.10.0.1.tgz
TLV-private:ThomasVincent$ cd kafka_2.11-0.10.0.1
{% endhighlight %}

&nbsp;

#### 3.2. Starting the Kafka server

Because Kafka depends on Zookeeper to maintain and distribute tasks, we need to start ZooKeeper before starting the Kafka broker.

{% highlight sh %}
TLV-private:ThomasVincent$ bin/zookeeper-server-start.sh config/zookeeper.properties
TLV-private:ThomasVincent$ bin/kafka-server-start.sh config/server.properties
{% endhighlight %}

You are now ready to start your Kafka server, using the command below

{% highlight sh %}
TLV-private:ThomasVincent$ bin/kafka-server-start.sh config/server.properties
{% endhighlight %}

Executing this command will generate a large number of logging lines, but the start and end should look like the following:

{% highlight sh %}
[2016-09-25 11:26:38,298] INFO starting (kafka.server.KafkaServer)
[2016-09-25 11:26:38,348] INFO Connecting to zookeeper on localhost:2181 (kafka.server.KafkaServer)
...
...
...
[2016-09-25 11:26:42,039] INFO [Kafka Server 0], started (kafka.server.KafkaServer)
{% endhighlight %}

&nbsp;

#### 3.3. Starting your first Kafka topic

Next, you can initialize a Kafka topic by using the `kafka-topics.sh` utility. In a new terminal window, type the command below to create a new topic called `test-topic` with a single partition and one replica factor.
{% highlight sh %}
TLV-private:ThomasVincent$ bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test-topic

Created topic "topic-name".
...
...
...
[2016-09-25 11:34:32,245] INFO Partition [test-topic,0] on broker 0: No checkpointed highwatermark is found for partition [test-topic,0] (kafka.cluster.Partition)
{% endhighlight %}

We can also list the topics currently in the Kafka server by using the `kafka-topics.sh` utility script
{% highlight sh %}
TLV-private:ThomasVincent$ bin/kafka-topics.sh --list --zookeeper localhost:2181
test-topic
{% endhighlight %}

&nbsp;

#### 3.4. Producing and consuming messages with Kafka

None of what we have done is very useful if no data is sent to the Kafka brokers. Here, we will configure a producer to send messages to our broker. Configurations for a single producer can be found in `config/server.properties`. A quick check in this file tells us that our broker listens to `localhost:9092`. Therefore, we use the `kafka-console-producer.sh` utility to create a producer to send messages to `localhost:9092` under our topic of choice. Once the producer is running, it will wait for input from `stdin` and publish to the Kafka cluster. The default setting is to have every new line be published as a new message, but tailored producer properties can be specified in the `config/producer.properties` file. The command below starts a producer and writes a couple of messages to stdin:

{% highlight sh %}
TLV-private:ThomasVincent$ bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test-topic
Hello
This is a test
Hello again
{% endhighlight %}

We can then consume those messages using the `kafka-console-consumer.sh` utility script

{% highlight sh %}
TLV-private:ThomasVincent$ bin/kafka-console-consumer.sh --zookeeper localhost:2181 --from-beginning --topic test-topic
Hello
This is a test
Hello again
{% endhighlight %}

So our consumer is successfully reading messages from our producer (via the broker). If you continue typing random messages in the producer terminal window, you will see them be printed out in the consumer terminal window.

&nbsp;

## 4. Setting up Spark
So far we have initialized Zookeeper, set up a Kafka cluster, started a producer that sends messages to a Kafka broker, and a a consumer that reads all messages send by the producer. In a real-world setting, this last step would be used to ingest, transform and possibly analyze the incoming data. Tools such as Spark or Storm work are some of the popular options used with Kafka for this type of use-case. In this series, we will leverage Spark Streaming to process incoming data. To begin we can download the Spark binary at the link [here](Download http://spark.apache.org/downloads.html) (click on option 4) and go ahead and install Spark. Note, it may also be wortwhile to include the following in your `.bashrc` file so that you do not have to repeat these steps every time you launch a new shell:

{% highlight sh %}
export SPARK_HOME=/your_path_to_spark_directory/spark
export PYTHONPATH=$SPARK_HOME/python/:$PYTHONPATH
PYTHONPATH=$SPARK_HOME/python/lib/py4j-0.8.2.1-src.zip:$PYTHONPATH
{% endhighlight %}

&nbsp;

## 4. Word count with Kafka and Spark Streaming

In this first part of the series, we will implement a very simplistic word count script (the "Hello World!" equivalent for Spark). However, it also seems vapid to limit ourselves to such an easy example when we have such great technology at our disposal, so the second part of this series will focus on implementing more complicated examples that may be applicable in real life scenarios.

&nbsp;

#### 4.1. The most vanilla word count script

First, let's start by writing our word count script using the Spark Python API (PySpark), which conveniently exposes the Spark programming model to Python. You can copy the chunk of code below into a file called `kafka_wordcount.py` to be placed in your working directory. While the code is self-explanatory, it is important to note that we are making use of Spark Streaming, a module built on top of Spark Core. Spark Streaming leverages Spark Core's fast scheduling capability to perform streaming analytics and ingests data in mini-batches while performing RDD transformations on those mini-batches of data.

{% highlight python %}
import sys
from pyspark import SparkContext
from pyspark import SparkConf
from pyspark.streaming import StreamingContext
from pyspark.streaming.kafka import KafkaUtils
from pyspark.sql.context import SQLContext

if __name__ == '__main__':
    if len(sys.argv) != 3:
        print("Usage: kafka_wordcount.py <zk> <topic>", file=sys.stderr)
        exit(-1)

    sc = SparkContext(appName="PythonStreamingKafkaWordCount")
    ssc = StreamingContext(sc, 1)

    zkQuorum, topic = sys.argv[1:]
    kvs = KafkaUtils.createStream(ssc, zkQuorum, "spark-streaming-consumer", {topic: 1})
    lines = kvs.map(lambda x: x[1])
    lines.pprint()

    counts = lines.flatMap(lambda line: line.split(" ")) \
                  .map(lambda word: (word, 1)) \
                  .reduceByKey(lambda a, b: a+b)
    counts.pprint()

    ssc.start()
    ssc.awaitTermination()
{% endhighlight %}

You can now open up a new terminal window and from your working directory, input the following command:

{% highlight sh %}
TLV-private:ThomasVincent$ bin/spark-submit --jars external/kafka-assembly/target/scala-*/spark-streaming-kafka-assembly-*.jar /Users/ThomasVincent/Desktop/kafka_wordcount.py localhost:2181 test-topic
{% endhighlight %}

If you continue typing random sentences in the producer terminal window, you will now see the output of your wordcount script being returned in your consumer terminal window! Congratulations, you have just successfully ran your first Kafka / Spark Streaming pipeline.

&nbsp;

#### 4.2. Leveraging DataFrame and SparkSQL

PySpark comes with a number of useful modules that make working with data a whole lot easier. Two particurlaly oft-used modules are SparkSQL and DataFrame, which both provide support for processing structured and semi-structured data. For more information on the many useful features of these two modules, please refer to this [link](https://www.tutorialspoint.com/spark_sql/spark_sql_quick_guide.htm). For now, we can further extend our word count example by integrating the DataFrame and SparkSQL features of Spark.

Copy the chunk of code below into a file called `kafka_spark_dataframes.py` and place it in your working directory.

{% highlight python %}
import sys
from pyspark import SparkContext
from pyspark import SparkConf
from pyspark.streaming import StreamingContext
from pyspark.streaming.kafka import KafkaUtils
from pyspark.sql import Row, DataFrame, SQLContext


def getSqlContextInstance(sparkContext):
    if ('sqlContextSingletonInstance' not in globals()):
        globals()['sqlContextSingletonInstance'] = SQLContext(sparkContext)
    return globals()['sqlContextSingletonInstance']


# Convert RDDs of the words DStream to DataFrame and run SQL query
def process(time, rdd):
    print("========= %s =========" % str(time))
    try:
        # Get the singleton instance of SparkSession
        sqlContext = getSqlContextInstance(rdd.context)

        # Convert RDD[String] to RDD[Row] to DataFrame
        #rowRdd = rdd.map(lambda w: Row(word=w))
        rowRdd = rdd.map(lambda w: Row(word=w[0], cnt=w[1]))
        #rowRdd.pprint()
        wordsDataFrame = sqlContext.createDataFrame(rowRdd)
        wordsDataFrame.show()

        # Creates a temporary view using the DataFrame.
        wordsDataFrame.createOrReplaceTempView("words")

        # Do word count on table using SQL and print it
        wordCountsDataFrame = \
             spark.sql("select SUM(cnt) as total from words")
        wordCountsDataFrame.show()
    except:
       pass

if __name__ == '__main__':
    if len(sys.argv) != 3:
        print("Usage: kafka_spark_dataframes.py <zk> <topic>", file=sys.stderr)
        exit(-1)

    sc = SparkContext(appName="PythonStreamingKafkaWordCount")
    sqlContext = SQLContext(sc)
    ssc = StreamingContext(sc, 1)


    zkQuorum, topic = sys.argv[1:]
    kvs = KafkaUtils.createStream(ssc, zkQuorum, "spark-streaming-consumer", {topic: 1})
    lines = kvs.map(lambda x: x[1])
    words = lines.flatMap(lambda line: line.split(" ")) \
        .map(lambda word: (word, 1)) \
        .reduceByKey(lambda a, b: a+b)

    words.foreachRDD(process)

    ssc.start()
    ssc.awaitTermination()
{% endhighlight %}

In a new terminal window, input the following command (and make sure you stop the previous word count script!):

{% highlight sh %}
TLV-private:ThomasVincent$ bin/spark-submit --jars external/kafka-assembly/target/scala-*/spark-streaming-kafka-assembly-*.jar /Users/ThomasVincent/Desktop/kafka_spark_dataframes.py localhost:2181 test-topic
{% endhighlight %}

Once again, typing random sentences in the producer terminal window will return a wordcount in your consumer terminal window! The only difference to the previous script is that we are now leveraging the SparkSQL module to perform this task.

&nbsp;

## Next Steps

As mentioned previously, the word count script represents a basic use of the technology at hand, and does not do justice to the capabilities of the tools. In the next part of this blog series, we will look to implement some more complicated scenarios, with a focus on data science rather than straightforward engineering pipeline.
