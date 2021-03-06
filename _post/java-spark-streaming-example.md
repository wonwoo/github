
## java example

```java
import com.fasterxml.jackson.databind.ObjectMapper;
import org.apache.spark.SparkConf;
import org.apache.spark.streaming.Duration;
import org.apache.spark.streaming.Time;
import org.apache.spark.streaming.api.java.JavaDStream;
import org.apache.spark.streaming.api.java.JavaPairReceiverInputDStream;
import org.apache.spark.streaming.api.java.JavaStreamingContext;
import org.apache.spark.streaming.kafka.KafkaUtils;

import java.util.HashMap;
import java.util.Map;
import java.util.regex.Pattern;

public final class JavaKafkaWordCount {
    private static final Pattern SPACE = Pattern.compile(" ");

    static ObjectMapper mapper = new ObjectMapper();

    private JavaKafkaWordCount() {
    }

    private static String PATH = "d:/spark/temp";

    public static void main(String[] args) throws Exception {
        System.setProperty("hadoop.home.dir", "D:\\hadoop-common-2.2.0-bin-master");
        SparkConf sparkConf = new SparkConf().setAppName("JavaKafkaWordCount").setMaster("local[*]");
        JavaStreamingContext jssc = new JavaStreamingContext(sparkConf, new Duration(10000));

        jssc.checkpoint("d:/spark/checkout");
        int numThreads = Integer.parseInt("1");
        Map<String, Integer> topicMap = new HashMap<>();
        String[] topics = "test".split(",");
        for (String topic : topics) {
            topicMap.put(topic, numThreads);
        }

        JavaPairReceiverInputDStream<String, String> messages =
                KafkaUtils.createStream(jssc, "127.0.0.1:2181", "group", topicMap);

        JavaDStream<String> wordCounts = messages.map(tweet -> mapper.writeValueAsString(tweet));

//        JavaDStream<String> lines = messages.map((Function<Tuple2<String, String>, String>) Tuple2::_2);
//
//        JavaDStream<String> words = lines.flatMap((FlatMapFunction<String, String>) s -> Arrays.asList(SPACE.split(s)).iterator());
//
//        JavaPairDStream<String, Integer> wordCounts = words.mapToPair(
//                (PairFunction<String, String, Integer>) s -> new Tuple2<>(s, 1)).reduceByKey((Function2<Integer, Integer, Integer>) (i1, i2) -> i1 + i2);
//        outputDir = args(1)
//        wordCounts.foreachRDD(rdd -> {
//                rdd.saveAsTextFile(outputDir)
//        });
        wordCounts.foreachRDD((rdd, time)-> {
            if (!rdd.partitions().isEmpty()) {
                rdd.saveAsTextFile(rddToFileName(PATH, "text", time));
            }
        });
        wordCounts.print(10);
        jssc.start();
//        wordCounts.repartition(1).dstream().saveAsTextFiles(PATH, "txt");
        jssc.awaitTermination();
    }

    private static String rddToFileName(String prefix, String suffix, Time time) {
        String result = String.valueOf(time.milliseconds());
        if (prefix != null && prefix.length() > 0) {
            result = prefix + "-" + result;
        }
        if (suffix != null && suffix.length() > 0) {
            result = result + "." + suffix;
        }
        return result;
    }
}
```


## dependency 
```xml
<dependencies>
    <dependency>
        <groupId>org.apache.spark</groupId>
        <artifactId>spark-core_2.11</artifactId>
        <version>2.0.0</version>
    </dependency>
    <dependency>
        <groupId>org.apache.spark</groupId>
        <artifactId>spark-streaming_2.11</artifactId>
        <version>2.0.0</version>
    </dependency>
    <dependency>
        <groupId>org.apache.spark</groupId>
        <artifactId>spark-sql_2.11</artifactId>
        <version>2.0.0</version>
    </dependency>
    <dependency>
        <groupId>org.apache.spark</groupId>
        <artifactId>spark-mllib_2.11</artifactId>
        <version>2.0.0</version>
    </dependency>
    <dependency>
        <groupId>org.apache.spark</groupId>
        <artifactId>spark-streaming-kafka-0-8_2.11</artifactId>
        <version>2.0.0</version>
    </dependency>
</dependencies>
```
