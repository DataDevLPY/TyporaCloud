

## 时间语义



![截屏2021-08-31 下午8.39.46](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220129259.png?token=AWS37JMKCOW7M64MHJLEMOTBTKBCG)



![截屏2021-08-31 下午9.23.43](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220129940.png?token=AWS37JIQYY7EL3HVU4Z2DCTBTKBCI)

```java
package com.peiyang.apitest.window;

import com.peiyang.beans.SensorReading;
import org.apache.flink.api.common.functions.MapFunction;
import org.apache.flink.streaming.api.TimeCharacteristic;
import org.apache.flink.streaming.api.datastream.DataStreamSource;
import org.apache.flink.streaming.api.datastream.SingleOutputStreamOperator;
import org.apache.flink.streaming.api.environment.StreamExecutionEnvironment;
import org.apache.flink.streaming.api.functions.timestamps.AscendingTimestampExtractor;
import org.apache.flink.streaming.api.functions.timestamps.BoundedOutOfOrdernessTimestampExtractor;
import org.apache.flink.streaming.api.windowing.time.Time;

public class WindowTest4_EventTimeWindow {
    public static void main(String[] args) throws Exception {
        StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
        env.setParallelism(2);
        env.setStreamTimeCharacteristic(TimeCharacteristic.EventTime);
        //env.getConfig().setAutoWatermarkInterval(100);

        DataStreamSource<String> inputStream = env.socketTextStream("localhost", 7777);

        SingleOutputStreamOperator<SensorReading> mapStream = inputStream.map(new MapFunction<String, SensorReading>() {
            @Override
            public SensorReading map(String s) throws Exception {
                String[] fields = s.split(",");
                return new SensorReading(fields[0], Long.parseLong(fields[1]), Double.parseDouble(fields[2]));
            }
        });

        //升序数据设置事件时间和watermark
        /**
         mapStream.assignTimestampsAndWatermarks(new AscendingTimestampExtractor<SensorReading>() {
            @Override
            public long extractAscendingTimestamp(SensorReading sensorReading) {
                return (sensorReading.getTimestamp() * 1000L);
            }
        });
         */

        //乱序数据设置事件事件和watermark
        mapStream.assignTimestampsAndWatermarks(new BoundedOutOfOrdernessTimestampExtractor<SensorReading>(Time.seconds(2)) {
            @Override
            public long extractTimestamp(SensorReading sensorReading) {
                return sensorReading.getTimestamp()*1000L;
            }

        });


        env.execute();

    }
}

```



## Watermark

![截屏2021-08-31 下午9.49.09](/Users/peiyang/Typora Notebook/【大数据】/「5」【Flink】/Flink Java/截屏2021-08-31 下午9.49.09.png)



![截屏2021-08-31 下午9.57.30](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220129358.png?token=AWS37JOUOFUKHVKRHCJ6OWLBTKBDG)

![截屏2021-08-31 下午10.02.34](/Users/peiyang/Typora Notebook/【大数据】/「5」【Flink】/Flink Java/截屏2021-08-31 下午10.11.28.png)

```java
package com.peiyang.apitest.window;

import com.peiyang.beans.SensorReading;
import org.apache.flink.api.common.functions.MapFunction;
import org.apache.flink.streaming.api.TimeCharacteristic;
import org.apache.flink.streaming.api.datastream.DataStreamSource;
import org.apache.flink.streaming.api.datastream.SingleOutputStreamOperator;
import org.apache.flink.streaming.api.environment.StreamExecutionEnvironment;
import org.apache.flink.streaming.api.functions.timestamps.AscendingTimestampExtractor;
import org.apache.flink.streaming.api.functions.timestamps.BoundedOutOfOrdernessTimestampExtractor;
import org.apache.flink.streaming.api.windowing.time.Time;

public class WindowTest4_EventTimeWindow {
    public static void main(String[] args) throws Exception {
        StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
        env.setParallelism(2);
        env.setStreamTimeCharacteristic(TimeCharacteristic.EventTime);
        //env.getConfig().setAutoWatermarkInterval(100);

        DataStreamSource<String> inputStream = env.socketTextStream("localhost", 7777);

        SingleOutputStreamOperator<SensorReading> mapStream = inputStream.map(new MapFunction<String, SensorReading>() {
            @Override
            public SensorReading map(String s) throws Exception {
                String[] fields = s.split(",");
                return new SensorReading(fields[0], Long.parseLong(fields[1]), Double.parseDouble(fields[2]));
            }
        });

        //升序数据设置事件时间和watermark
        /**
         mapStream.assignTimestampsAndWatermarks(new AscendingTimestampExtractor<SensorReading>() {
            @Override
            public long extractAscendingTimestamp(SensorReading sensorReading) {
                return (sensorReading.getTimestamp() * 1000L);
            }
        });

*/
        //乱序数据设置事件事件和watermark
        mapStream.assignTimestampsAndWatermarks(new BoundedOutOfOrdernessTimestampExtractor<SensorReading>(Time.seconds(2)) {
            @Override
            public long extractTimestamp(SensorReading sensorReading) {
                return sensorReading.getTimestamp()*1000L;
            }

        });
        

        //基于事件时间的开窗集合，统计15s内温度的最小值
        SingleOutputStreamOperator<SensorReading> minTempStream = mapStream.keyBy("id")
                .timeWindow(Time.seconds(15))
                .minBy("temperature");

        minTempStream.print("minTemp");
    
        //mapStream.print();

        env.execute();

    }
}

```



## 延迟或溢出

```java
package com.peiyang.apitest.window;

import com.peiyang.beans.SensorReading;
import org.apache.flink.api.common.functions.MapFunction;
import org.apache.flink.streaming.api.TimeCharacteristic;
import org.apache.flink.streaming.api.datastream.DataStreamSource;
import org.apache.flink.streaming.api.datastream.SingleOutputStreamOperator;
import org.apache.flink.streaming.api.environment.StreamExecutionEnvironment;
import org.apache.flink.streaming.api.functions.timestamps.AscendingTimestampExtractor;
import org.apache.flink.streaming.api.functions.timestamps.BoundedOutOfOrdernessTimestampExtractor;
import org.apache.flink.streaming.api.windowing.time.Time;
import org.apache.flink.util.OutputTag;

public class WindowTest4_EventTimeWindow {
    public static void main(String[] args) throws Exception {
        StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
        env.setParallelism(2);
        env.setStreamTimeCharacteristic(TimeCharacteristic.EventTime);
        //env.getConfig().setAutoWatermarkInterval(100);

        DataStreamSource<String> inputStream = env.socketTextStream("localhost", 7777);

        SingleOutputStreamOperator<SensorReading> mapStream = inputStream.map(new MapFunction<String, SensorReading>() {
            @Override
            public SensorReading map(String s) throws Exception {
                String[] fields = s.split(",");
                return new SensorReading(fields[0], Long.parseLong(fields[1]), Double.parseDouble(fields[2]));
            }
        });

        //升序数据设置事件时间和watermark
        /**
         mapStream.assignTimestampsAndWatermarks(new AscendingTimestampExtractor<SensorReading>() {
            @Override
            public long extractAscendingTimestamp(SensorReading sensorReading) {
                return (sensorReading.getTimestamp() * 1000L);
            }
        });

*/
        //乱序数据设置事件事件和watermark
        mapStream.assignTimestampsAndWatermarks(new BoundedOutOfOrdernessTimestampExtractor<SensorReading>(Time.seconds(2)) {
            @Override
            public long extractTimestamp(SensorReading sensorReading) {
                return sensorReading.getTimestamp()*1000L;
            }

        });

        // 溢出流放入
        OutputTag<SensorReading> outputTag = new OutputTag<SensorReading>("late") {

        };

        //基于事件时间的开窗集合，统计15s内温度的最小值
        SingleOutputStreamOperator<SensorReading> minTempStream = mapStream.keyBy("id")
                .timeWindow(Time.seconds(15))
                .allowedLateness(Time.minutes(1))
                .sideOutputLateData(outputTag)
                .minBy("temperature");

        minTempStream.print("minTemp");
        minTempStream.getSideOutput(outputTag).print("late");

        //mapStream.print();

        env.execute();

    }
}

```

