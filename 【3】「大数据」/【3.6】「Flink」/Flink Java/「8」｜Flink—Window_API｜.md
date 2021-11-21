

## Window API

![截屏2021-08-28 下午9.30.44](/Users/peiyang/Typora Notebook/【大数据】/「5」【Flink】/Flink Java/截屏2021-08-28 下午9.30.44.png)

![截屏2021-08-28 下午9.31.30](/Users/peiyang/Typora Notebook/【大数据】/「5」【Flink】/Flink Java/截屏2021-08-28 下午9.31.30.png)

![截屏2021-08-28 下午9.31.54](/Users/peiyang/Typora Notebook/【大数据】/「5」【Flink】/Flink Java/截屏2021-08-28 下午9.31.54.png)

 

 ## Window Function

![截屏2021-08-28 下午9.44.47](/Users/peiyang/Typora Notebook/【大数据】/「5」【Flink】/Flink Java/截屏2021-08-28 下午9.44.47.png)

### 增量窗口

```java
package com.peiyang.apitest.window;

import com.peiyang.beans.SensorReading;
import org.apache.flink.api.common.functions.AggregateFunction;
import org.apache.flink.api.common.functions.MapFunction;
import org.apache.flink.streaming.api.datastream.DataStreamSource;
import org.apache.flink.streaming.api.datastream.SingleOutputStreamOperator;
import org.apache.flink.streaming.api.environment.StreamExecutionEnvironment;
import org.apache.flink.streaming.api.windowing.assigners.EventTimeSessionWindows;
import org.apache.flink.streaming.api.windowing.assigners.TumblingProcessingTimeWindows;
import org.apache.flink.streaming.api.windowing.time.Time;

public class WindowTest1 {
    public static void main(String[] args) throws Exception {
        StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
        env.setParallelism(4);
        DataStreamSource<String> inputStream = env.readTextFile("/Users/peiyang/IdeaProjects/FlinkTurtorial/src/main/resources/sensor");

        SingleOutputStreamOperator<SensorReading> ds = inputStream.map(new MapFunction<String, SensorReading>() {
            @Override
            public SensorReading map(String s) throws Exception {
                String[] fields = s.split(",");
                return new SensorReading(fields[0], Long.parseLong(fields[1]), Double.parseDouble(fields[2]));
            }
        });

        //开窗操作
        SingleOutputStreamOperator<Integer> AggStream = ds.keyBy("id")
                //.countWindow(10,2);
                //.window(EventTimeSessionWindows.withGap(Time.minutes(1)));
                //.window(TumblingProcessingTimeWindows.of(Time.seconds(15)));
                .timeWindow(Time.seconds(15))
                .aggregate(new myAggregateFunction());

        AggStream.print();

        env.execute();

    }


    public static class myAggregateFunction implements AggregateFunction<SensorReading, Integer, Integer>{

        @Override
        public Integer createAccumulator() {
            return 0;
        }

        @Override
        public Integer add(SensorReading sensorReading, Integer accumulator) {
            return accumulator + 1;
        }

        @Override
        public Integer getResult(Integer accumulator) {
            return accumulator;
        }

        @Override
        public Integer merge(Integer a, Integer b) {
            return null;
        }
    }

}

```

 

### 全量窗口

```java
package com.peiyang.apitest.window;

import com.peiyang.beans.SensorReading;
import org.apache.commons.collections.IteratorUtils;
import org.apache.flink.api.common.functions.AggregateFunction;
import org.apache.flink.api.common.functions.MapFunction;
import org.apache.flink.api.java.tuple.Tuple;
import org.apache.flink.api.java.tuple.Tuple3;
import org.apache.flink.streaming.api.datastream.DataStreamSource;
import org.apache.flink.streaming.api.datastream.SingleOutputStreamOperator;
import org.apache.flink.streaming.api.environment.StreamExecutionEnvironment;
import org.apache.flink.streaming.api.functions.windowing.ProcessWindowFunction;
import org.apache.flink.streaming.api.functions.windowing.WindowFunction;
import org.apache.flink.streaming.api.windowing.assigners.EventTimeSessionWindows;
import org.apache.flink.streaming.api.windowing.assigners.TumblingProcessingTimeWindows;
import org.apache.flink.streaming.api.windowing.time.Time;
import org.apache.flink.streaming.api.windowing.windows.TimeWindow;
import org.apache.flink.streaming.api.windowing.windows.Window;
import org.apache.flink.util.Collector;

public class WindowTest1 {
    public static void main(String[] args) throws Exception {
        StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
        env.setParallelism(4);
        DataStreamSource<String> inputStream = env.readTextFile("/Users/peiyang/IdeaProjects/FlinkTurtorial/src/main/resources/sensor");

        SingleOutputStreamOperator<SensorReading> ds = inputStream.map(new MapFunction<String, SensorReading>() {
            @Override
            public SensorReading map(String s) throws Exception {
                String[] fields = s.split(",");
                return new SensorReading(fields[0], Long.parseLong(fields[1]), Double.parseDouble(fields[2]));
            }
        });

        //开窗操作

        //1. 增量聚合
        SingleOutputStreamOperator<Integer> AggStream = ds.keyBy("id")
                //.countWindow(10,2);
                //.window(EventTimeSessionWindows.withGap(Time.minutes(1)));
                //.window(TumblingProcessingTimeWindows.of(Time.seconds(15)));
                .timeWindow(Time.seconds(15))
                .aggregate(new myAggregateFunction());

        //2. 全窗口函数
        SingleOutputStreamOperator<Tuple3<String,Long,Integer>> resultStream = ds.keyBy("id")
                .timeWindow(Time.seconds(1))
                //.process(new ProcessWindowFunction<SensorReading, Object, Tuple, TimeWindow>() {
                //    @Override
                //    public void process(Tuple tuple, ProcessWindowFunction<SensorReading, Object, Tuple, TimeWindow>.Context context, Iterable<SensorReading> iterable, Collector<Object> collector) throws Exception {
                //        
                //    }
                //})
                .apply(new myWindowFunction());


        resultStream.print();

        env.execute();

    }


    public static class myAggregateFunction implements AggregateFunction<SensorReading, Integer, Integer>{

        @Override
        public Integer createAccumulator() {
            return 0;
        }

        @Override
        public Integer add(SensorReading sensorReading, Integer accumulator) {
            return accumulator + 1;
        }

        @Override
        public Integer getResult(Integer accumulator) {
            return accumulator;
        }

        @Override
        public Integer merge(Integer a, Integer b) {
            return null;
        }
    }

    public static class myWindowFunction implements WindowFunction<SensorReading,Tuple3<String,Long,Integer>, Tuple, TimeWindow>{


        @Override
        public void apply(Tuple tuple, TimeWindow timeWindow, Iterable<SensorReading> iterable, Collector<Tuple3<String, Long, Integer>> collector) throws Exception {

            String id = tuple.getField(0);
            Long windowEnd = timeWindow.getEnd();
            Integer count = IteratorUtils.toList(iterable.iterator()).size();
            collector.collect(new Tuple3<>(id,windowEnd,count));
        }
    }
}

```



### 计数窗口

```java
package com.peiyang.apitest.window;

import com.peiyang.beans.SensorReading;
import org.apache.flink.api.common.functions.AggregateFunction;
import org.apache.flink.api.common.functions.MapFunction;
import org.apache.flink.api.java.tuple.Tuple2;
import org.apache.flink.streaming.api.datastream.DataStreamSource;
import org.apache.flink.streaming.api.datastream.SingleOutputStreamOperator;
import org.apache.flink.streaming.api.environment.StreamExecutionEnvironment;

public class WindowTest2 {
    public static void main(String[] args) throws Exception {
        StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
        env.setParallelism(4);

        DataStreamSource<String> inputStream = env.socketTextStream("localhost", 7777);
        //env.execute();

        SingleOutputStreamOperator<SensorReading> ds = inputStream.map(new MapFunction<String, SensorReading>() {
            @Override
            public SensorReading map(String s) throws Exception {
                String[] fields = s.split(",");
                return new SensorReading(fields[0], Long.parseLong(fields[1]), Double.parseDouble(fields[2]));
            }
        });

        //开计数窗口测试
        SingleOutputStreamOperator<Double> avgTempResultStream = ds.keyBy("id")
                .countWindow(4, 2)
                .aggregate(new myAvgTemp());

        avgTempResultStream.print();
        env.execute();

    }

    public static class myAvgTemp implements AggregateFunction<SensorReading, Tuple2<Double,Integer>, Double>{

        @Override
        public Tuple2<Double, Integer> createAccumulator() {
            return new Tuple2<>(0.0, 0);
        }

        @Override
        public Tuple2<Double, Integer> add(SensorReading sensorReading, Tuple2<Double, Integer> accumulator) {
            return new Tuple2<>(accumulator.f0 + sensorReading.getTemperature(),accumulator.f1 + 1);
        }

        @Override
        public Double getResult(Tuple2<Double, Integer> accumulator) {
            return accumulator.f0 / accumulator.f1;
        }

        @Override
        public Tuple2<Double, Integer> merge(Tuple2<Double, Integer> acc1, Tuple2<Double, Integer> acc2) {
            return new Tuple2<>(acc1.f0+acc2.f0, acc2.f1+acc2.f1);
        }
    }


}
```



## 其他可选API

![截屏2021-08-31 下午4.19.33](/Users/peiyang/Typora Notebook/【大数据】/「5」【Flink】/Flink Java/截屏2021-08-31 下午4.19.33.png)



```java
package com.peiyang.apitest.window;

import com.peiyang.beans.SensorReading;
import org.apache.flink.api.common.functions.MapFunction;
import org.apache.flink.streaming.api.datastream.DataStreamSource;
import org.apache.flink.streaming.api.datastream.SingleOutputStreamOperator;
import org.apache.flink.streaming.api.environment.StreamExecutionEnvironment;
import org.apache.flink.streaming.api.windowing.time.Time;
import org.apache.flink.util.OutputTag;

public class WindowTest3 {
    public static void main(String[] args) throws Exception {

        StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
        env.setParallelism(2);

        DataStreamSource<String> inputStream = env.socketTextStream("localhost", 7777);

        SingleOutputStreamOperator<SensorReading> mapStream = inputStream.map(new MapFunction<String, SensorReading>() {
            @Override
            public SensorReading map(String s) throws Exception {
                String[] fields = s.split(",");
                return new SensorReading(fields[0], Long.parseLong(fields[1]), Double.parseDouble(fields[2]));
            }
        });


        // 其他可选API
        OutputTag<SensorReading> outputTag = new OutputTag<SensorReading>("late") {
        };

        SingleOutputStreamOperator<SensorReading> sumStream = mapStream.keyBy("id")
                .timeWindow(Time.seconds(15))
                //.trigger()
                //.evictor()
                .allowedLateness(Time.minutes(1))
                .sideOutputLateData(outputTag)
                .sum("temperature");

        sumStream.getSideOutput(outputTag).print("late");

        env.execute();

    }
}

```



![截屏2021-08-31 下午7.04.15](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-08-31 下午7.04.15.png)





