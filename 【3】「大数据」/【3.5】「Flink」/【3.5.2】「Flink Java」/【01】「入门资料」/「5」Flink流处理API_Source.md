

## 流处理API   -- Source



### 从集合读取数据

```java
package com.peiyang.apitest;

import com.peiyang.beans.SensorReading;
import org.apache.flink.streaming.api.datastream.DataStreamSource;
import org.apache.flink.streaming.api.environment.StreamExecutionEnvironment;

import java.io.Serializable;
import java.util.Arrays;

public class SourceTest1_Collection {
    public static void main(String[] args) throws Exception {
        //创建执行环境
        StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment().setParallelism(2);

        DataStreamSource<SensorReading> sensorDataStreamSource = env.fromCollection(Arrays.asList(
                new SensorReading("sensor_1", 1547718199L, 35.8),
                new SensorReading("sensor_6`", 1547723349L, 15.8),
                new SensorReading("sensor_7", 1547713239L, 20.8),
                new SensorReading("sensor_10", 1547718299L, 5.8)
        ));

        DataStreamSource<Integer> IntegerDataStreamSource = env.fromElements(1, 2, 10, 200);

        sensorDataStreamSource.print();
        IntegerDataStreamSource.print();

        env.execute("SensorReading");

    }
}
```



### 从Source文件读取

```java
package com.peiyang.apitest;

import org.apache.flink.streaming.api.datastream.DataStreamSource;
import org.apache.flink.streaming.api.environment.StreamExecutionEnvironment;

public class SourceTest2_File {
    public static void main(String[] args) throws Exception {
        StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment().setParallelism(2);

        DataStreamSource<String> stringDataStreamSource = env.readTextFile("/Users/peiyang/IdeaProjects/FlinkTurtorial/src/main/resources/sensor");

        stringDataStreamSource.print();

        env.execute();
    }
}
```



## 连接kafka

```java
package com.peiyang.apitest;

import org.apache.flink.api.common.serialization.SimpleStringSchema;
import org.apache.flink.streaming.api.datastream.DataStreamSource;
import org.apache.flink.streaming.api.environment.StreamExecutionEnvironment;
import org.apache.flink.streaming.connectors.kafka.FlinkKafkaConsumer011;
import org.apache.flink.streaming.connectors.kafka.FlinkKafkaConsumer09;

import java.util.Properties;

public class SourceTest3_Kafka {
    public static void main(String[] args) throws Exception {
        StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment().setParallelism(2);

        Properties properties = new Properties();
        properties.setProperty("bootstrap.servers","localhost:9092");
        properties.setProperty("group.id","consumer-group");
        properties.setProperty("key.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
        properties.setProperty("value.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
        properties.setProperty("auto.offset.reset","latest");


        DataStreamSource<String> stringDataStreamSource =
                env.addSource(new FlinkKafkaConsumer011<String>("sensor",new SimpleStringSchema(),properties));

        stringDataStreamSource.print();

        env.execute();
    }
}
```



```shell
## 创建zookeeper
docker run -d --name zookeeper01 -p 2181:2181 -v /etc/localtime:/etc/localtime wurstmeister/zookeeper

## 创建kafka
docker run -d --name kafka01 -p 9092:9092 -e KAFKA_BROKER_ID=0 -e KAFKA_ZOOKEEPER_CONNECT=192.168.137.67:2181/kafka -e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://192.168.137.67:9092 -e KAFKA_LISTENERS=PLAINTEXT://0.0.0.0:9092  wurstmeister/kafka

## kafka中的操作
./bin/kafka-server-start.sh -daemon ./config/server.properties
./bin/kafka-console-producer.sh --broker-list localhost:9092 --topic sensor

./bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic sinktest
```



## 自定义Source

 ```java
 package com.peiyang.apitest;
 
 import com.peiyang.beans.SensorReading;
 import org.apache.flink.streaming.api.datastream.DataStreamSource;
 import org.apache.flink.streaming.api.environment.StreamExecutionEnvironment;
 import org.apache.flink.streaming.api.functions.source.SourceFunction;
 
 import java.util.HashMap;
 import java.util.Random;
 
 public class SourceTest4_UDF  {
     public static void main(String[] args) throws Exception {
         StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
         env.setParallelism(1);
 
 
         DataStreamSource<SensorReading> objectDataStreamSource = env.addSource(new MySensorSource());
 
         objectDataStreamSource.print();
 
         env.execute();
 
     }
 
 
     // 实现自定义的SourceFunction
     public static class MySensorSource implements SourceFunction<SensorReading>{
 
         // 定义一个标识位，控制数据的产生
         private boolean running = true;
 
         @Override
         public void run(SourceContext<SensorReading> sourceContext) throws Exception {
 
             // 定义一个随机数发生器
             Random random = new Random();
 
             // 设置10个传感器的初始温度
             HashMap<String, Double> sensorTempMap = new HashMap<>();
             for(int i=0;i<10;i++){
                 sensorTempMap.put("sensor_" + String.valueOf(i+1) , 60 + random.nextGaussian()*20);
             }
 
             while(running){
                 for(String sensorId:sensorTempMap.keySet()){
 
                     // 在当前温度基础上随机波动
                     Double newtemp = sensorTempMap.get(sensorId) + random.nextGaussian();
                     sensorTempMap.put(sensorId,newtemp);
                     sourceContext.collect(new SensorReading(sensorId,System.currentTimeMillis(), newtemp));
                 }
 
                 // 控制输出速度
                 Thread.sleep(1000L);
             }
         }
 
         @Override
         public void cancel() {
             running = false;
         }
     }
 
 }
 
 ```

