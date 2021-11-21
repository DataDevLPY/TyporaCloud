

![截屏2021-09-02 下午10.01.29](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220129183.png?token=AWS37JOVL565VMVZMXNJD73BTKBDQ)





![截屏2021-09-02 下午10.02.36](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220130427.png?token=AWS37JPU56SP7WTNKO3IHRDBTKBHE)



![截屏2021-09-02 下午10.11.27](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220130563.png?token=AWS37JJ635SNTMQXH7SYR2TBTKBHK)

![截屏2021-09-02 下午10.15.05](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220130949.png?token=AWS37JMQFRIJDENM2Z7HBRDBTKBHO)





```java
xpackage com.peiyang.apitest.state;

import com.peiyang.beans.SensorReading;
import org.apache.flink.api.common.functions.MapFunction;
import org.apache.flink.streaming.api.checkpoint.ListCheckpointed;
import org.apache.flink.streaming.api.datastream.DataStreamSource;
import org.apache.flink.streaming.api.datastream.SingleOutputStreamOperator;
import org.apache.flink.streaming.api.environment.StreamExecutionEnvironment;

import java.util.Collections;
import java.util.List;

public class stateTest1_operatorState {
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

        //定义一个有状态的map操作，统计当前数据个数
        SingleOutputStreamOperator<Integer> resultStream = mapStream.map(new MyCountFunction());

        resultStream.print();
        env.execute();
    }

    public static class MyCountFunction implements MapFunction<SensorReading, Integer>, ListCheckpointed, com.peiyang.apitest.state.MyCountFunction {
        //定义一个本地变量，作为算子状态
        private Integer count = 0;

        @Override
        public Integer map(SensorReading sensorReading) throws Exception {
            return count++;
        }

        @Override
        public List snapshotState(long l, long l1) throws Exception {
            return Collections.singletonList(count);
        }

        @Override
        public void restoreState(List list) throws Exception {
            for (Integer num: list ){
                count += num;
            }
        }
    }



}

```





![截屏2021-09-04 上午2.01.38](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220130383.png?token=AWS37JOZMZ3PN73ZMK2ZNPLBTKBHY)

```java
package com.peiyang.apitest.state;

import com.peiyang.beans.SensorReading;
import org.apache.flink.api.common.functions.*;
import org.apache.flink.api.common.state.ListState;
import org.apache.flink.api.common.state.ListStateDescriptor;
import org.apache.flink.api.common.state.ValueState;
import org.apache.flink.api.common.state.ValueStateDescriptor;
import org.apache.flink.configuration.Configuration;
import org.apache.flink.runtime.state.StateSnapshotTransformers;
import org.apache.flink.streaming.api.datastream.DataStreamSource;
import org.apache.flink.streaming.api.datastream.SingleOutputStreamOperator;
import org.apache.flink.streaming.api.environment.StreamExecutionEnvironment;

public class stateTest2_operatorState {
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

        //定义一个有状态的map操作，统计当前数据个数
        SingleOutputStreamOperator<Integer> resultStream = mapStream
                .keyBy("id").
                 map(new keyCountFunction());

        resultStream.print();
        env.execute();
    }


    //自定义RichMapfunction
    public static class keyCountFunction extends RichMapFunction<SensorReading,Integer> {
        private ValueState<Integer> keyCountState;

        //  其他类型状态的声明
        private ListState<String> myListState;


        @Override
        public void open(Configuration parameters) throws Exception {
            keyCountState = getRuntimeContext().getState(new ValueStateDescriptor<Integer>("key-count",Integer.class,0));
            myListState = getRuntimeContext().getListState(new ListStateDescriptor<String>("my-list",String.class));

        }

        @Override
        public Integer map(SensorReading sensorReading) throws Exception {

            for(String str : myListState.get()){
                System.out.println(str);
            }
            myListState.add("hello");

            Integer count = keyCountState.value();
            count++;
            keyCountState.update(count);

            return count;
        }
    }
}
```







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



![截屏2021-09-07 下午8.17.31](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220130640.png?token=AWS37JOVV3HWW2RTULWWNP3BTKBIE)

![截屏2021-09-07 下午8.18.00](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220130726.png?token=AWS37JJKS5K7ZJPLDGNFUALBTKBIG)

 











