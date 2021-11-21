

```java
package com.peiyang.apitest.tableapi;

import com.peiyang.beans.SensorReading;
import org.apache.flink.api.common.functions.MapFunction;
import org.apache.flink.streaming.api.datastream.DataStreamSource;
import org.apache.flink.streaming.api.datastream.SingleOutputStreamOperator;
import org.apache.flink.streaming.api.environment.StreamExecutionEnvironment;
import org.apache.flink.table.api.Table;
import org.apache.flink.table.api.java.StreamTableEnvironment;
import org.apache.flink.types.Row;


public class Example {
    public static void main(String[] args) throws Exception {
        StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
        env.setParallelism(1);

        //1. 读取数据
        DataStreamSource<String> inputStream = env.readTextFile("/Users/peiyang/IdeaProjects/FlinkTurtorial/src/main/resources/sensor");


        //2. 转换POJO
        SingleOutputStreamOperator<SensorReading> ds = inputStream.map(new MapFunction<String, SensorReading>() {
            @Override
            public SensorReading map(String s) throws Exception {
                String[] fields = s.split(",");
                return new SensorReading(fields[0], Long.parseLong(fields[1]), Double.parseDouble(fields[2]));
            }
        });


        //3. 创建表环境
        StreamTableEnvironment tableEnv = StreamTableEnvironment.create(env);

        //4. 基于流创建一张表
        Table dataTable = tableEnv.fromDataStream(inputStream);

        //5. 调用table api进行转换操作
        Table resultTable = dataTable.select("*");

        //6. 执行sql
        tableEnv.createTemporaryView("sensor", dataTable);

        String sql = "select * from sensor";
        Table resultSqlTable = tableEnv.sqlQuery(sql);

        tableEnv.toAppendStream(resultTable, org.apache.flink.types.Row.class).print("result");
        tableEnv.toAppendStream(resultSqlTable, Row.class).print("sql");

        env.execute();


    }
}

```

![截屏2021-09-16 下午10.00.50](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-09-16%20%E4%B8%8B%E5%8D%8810.00.50.png?token=AWS37JKHUFMFQXZW7KSRUEDBTIPWM)







![截屏2021-09-19 下午2.08.02](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-09-19%20%E4%B8%8B%E5%8D%882.08.02.png?token=AWS37JNVX3NOMHR4SOSXVF3BTIPWK)

## 输出

![image-20210920230405947](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/image-20210920230405947.png?token=AWS37JK4YDNEJEZMKHU3VJTBTIPWO)

![image-20210920230414780](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/image-20210920230414780.png?token=AWS37JM543LGG3ZVF34U7QLBTIPWS)

![image-20210920230421966](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/image-20210920230421966.png?token=AWS37JPXHVZFTSXHV2YCSIDBTIPW2)





## 创建视图

![image-20210920225947216](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/image-20210920225947216.png?token=AWS37JK6Y7XQ5TYZJPOQJXDBTIPW2)

## 动态表

![image-20210920225939414](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/image-20210920225939414.png?token=AWS37JIERB6WJY2ULW334TTBTIPXG)

![image-20210920225846983](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/image-20210920225846983.png?token=AWS37JNKLGS3233GETJTEPDBTIPXU)![image-20210920225855843](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/image-20210920225855843.png?token=AWS37JJIXX3IOORJBBU7OW3BTIPY4)

![image-20210920225909571](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/image-20210920225909571.png?token=AWS37JNIE6MFY5XJCEMJWEDBTIPYA)

![image-20210920225918740](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/image-20210920225918740.png?token=AWS37JNER73AXVNAV7KHPQDBTIPZQ)

## 水位线

```java
package com.peiyang.apitest.tableapi;

import com.peiyang.beans.SensorReading;
import org.apache.flink.api.common.functions.MapFunction;
import org.apache.flink.streaming.api.TimeCharacteristic;
import org.apache.flink.streaming.api.datastream.DataStreamSource;
import org.apache.flink.streaming.api.datastream.SingleOutputStreamOperator;
import org.apache.flink.streaming.api.environment.StreamExecutionEnvironment;
import org.apache.flink.streaming.api.functions.timestamps.BoundedOutOfOrdernessTimestampExtractor;
import org.apache.flink.streaming.api.windowing.time.Time;
import org.apache.flink.table.api.Table;
import org.apache.flink.table.api.java.StreamTableEnvironment;
import org.apache.flink.types.Row;

public class TableTest5_TimeWindow {
    public static void main(String[] args) throws Exception {
        StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
        env.setParallelism(1);
        env.setStreamTimeCharacteristic(TimeCharacteristic.EventTime);

        //1. 读取数据
        DataStreamSource<String> inputStream = env.readTextFile("/Users/peiyang/IdeaProjects/FlinkTurtorial/src/main/resources/sensor");


        //2. 转换POJO
        SingleOutputStreamOperator<SensorReading> ds = inputStream.map(new MapFunction<String, SensorReading>() {
            @Override
            public SensorReading map(String s) throws Exception {
                String[] fields = s.split(",");
                return new SensorReading(fields[0], Long.parseLong(fields[1]), Double.parseDouble(fields[2]));
            }
        }).assignTimestampsAndWatermarks(new BoundedOutOfOrdernessTimestampExtractor<SensorReading>(Time.seconds(2)) {
            @Override
            public long extractTimestamp(SensorReading sensorReading) {
                return sensorReading.getTimestamp() * 1000L;
            }
        });


        StreamTableEnvironment tableEnv = StreamTableEnvironment.create(env);


        // 将流转换成表，定义时间特性
        //Table dataTable = tableEnv.fromDataStream(ds, "id, timestamp as ts, temperature as temp, pt.proctime");
        Table dataTable = tableEnv.fromDataStream(ds, "id, timestamp.rowtime as ts, temperature as temp");

        dataTable.printSchema();
        //tableEnv.toAppendStream(dataTable, Row.class).print();


        env.execute();


    }
}

```











## 窗口

![image-20210920225815938](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/image-20210920225815938.png?token=AWS37JIAHLNABCNDUFJTXXDBTIPZ6)

![image-20210920225806801](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/image-20210920225806801.png?token=AWS37JM5ACE6MYCC6POFYJTBTIP2E)

![image-20210920225759147](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/image-20210920225759147.png?token=AWS37JLWFXMQUTZLZHEVZITBTIP22)

### Group Window

![image-20210920225829904](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/image-20210920225829904.png?token=AWS37JMDPCGM6FA2HC3II3DBTIP2G)

![image-20210920225752879](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/image-20210920225752879.png?token=AWS37JL7DVJUK4QKWHDYWC3BTIP2S)

```java
package com.peiyang.apitest.tableapi;

import com.peiyang.beans.SensorReading;
import org.apache.flink.api.common.functions.MapFunction;
import org.apache.flink.streaming.api.TimeCharacteristic;
import org.apache.flink.streaming.api.datastream.DataStreamSource;
import org.apache.flink.streaming.api.datastream.SingleOutputStreamOperator;
import org.apache.flink.streaming.api.environment.StreamExecutionEnvironment;
import org.apache.flink.streaming.api.functions.timestamps.BoundedOutOfOrdernessTimestampExtractor;
import org.apache.flink.streaming.api.windowing.time.Time;
import org.apache.flink.table.api.Table;
import org.apache.flink.table.api.Tumble;
import org.apache.flink.table.api.java.StreamTableEnvironment;
import org.apache.flink.types.Row;

public class TableTest5_TimeWindow {
    public static void main(String[] args) throws Exception {
        StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
        env.setParallelism(1);
        env.setStreamTimeCharacteristic(TimeCharacteristic.EventTime);

        //1. 读取数据
        DataStreamSource<String> inputStream = env.readTextFile("/Users/peiyang/IdeaProjects/FlinkTurtorial/src/main/resources/sensor");


        //2. 转换POJO
        SingleOutputStreamOperator<SensorReading> ds = inputStream.map(new MapFunction<String, SensorReading>() {
            @Override
            public SensorReading map(String s) throws Exception {
                String[] fields = s.split(",");
                return new SensorReading(fields[0], Long.parseLong(fields[1]), Double.parseDouble(fields[2]));
            }
        }).assignTimestampsAndWatermarks(new BoundedOutOfOrdernessTimestampExtractor<SensorReading>(Time.seconds(2)) {
            @Override
            public long extractTimestamp(SensorReading sensorReading) {
                return sensorReading.getTimestamp() * 1000L;
            }
        });


        StreamTableEnvironment tableEnv = StreamTableEnvironment.create(env);


        // 4. 将流转换成表，定义时间特性
        //Table dataTable = tableEnv.fromDataStream(ds, "id, timestamp as ts, temperature as temp, pt.proctime");
        //Table dataTable = tableEnv.fromDataStream(ds, "id, timestamp.rowtime as rt, temperature as temp");
        Table dataTable = tableEnv.fromDataStream(ds, "id, timestamp as ts, rt.rowtime, temperature as temp");


        dataTable.printSchema();
        //tableEnv.toAppendStream(dataTable, Row.class).print();

        tableEnv.createTemporaryView("sensor",dataTable);

        // 5.窗口操作
        //5。1 Group Window
        //table API
        Table resultTable = dataTable.window(Tumble.over("10.seconds").on("rt").as("tw"))
                .groupBy("id, tw")
                .select(("id,id.count,temp.avg,tw.end"));

        //SQL
        Table resultSqlTable = tableEnv.sqlQuery("select id, count(id) as cnt, avg(temp) as avgTemp, tumble_end(rt, interval '10' second )"+
                "from sensor group by id, tumble(rt, interval '10' second)");


        //  打印
        tableEnv.toRetractStream(resultTable, Row.class).print("agg");
        tableEnv.toRetractStream(resultSqlTable, Row.class).print("sql");

        env.execute();


    }
}

```





### Over Window

![image-20210920225743166](/Users/peiyang/Library/Application Support/typora-user-images/image-20210920225743166.png)

![image-20210920225729410](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/image-20210920225729410.png?token=AWS37JJSDOVFLDNQ25UFRPLBTIP4K)



![image-20210920225735594](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/image-20210920225735594.png?token=AWS37JONQMHMBO4OSKHRSPDBTIP4E)

```java
package com.peiyang.apitest.tableapi;

import com.peiyang.beans.SensorReading;
import org.apache.flink.api.common.functions.MapFunction;
import org.apache.flink.streaming.api.TimeCharacteristic;
import org.apache.flink.streaming.api.datastream.DataStreamSource;
import org.apache.flink.streaming.api.datastream.SingleOutputStreamOperator;
import org.apache.flink.streaming.api.environment.StreamExecutionEnvironment;
import org.apache.flink.streaming.api.functions.timestamps.BoundedOutOfOrdernessTimestampExtractor;
import org.apache.flink.streaming.api.windowing.time.Time;
import org.apache.flink.table.api.Over;
import org.apache.flink.table.api.Table;
import org.apache.flink.table.api.Tumble;
import org.apache.flink.table.api.java.StreamTableEnvironment;
import org.apache.flink.types.Row;

public class TableTest5_TimeWindow {
    public static void main(String[] args) throws Exception {
        StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
        env.setParallelism(1);
        env.setStreamTimeCharacteristic(TimeCharacteristic.EventTime);

        //1. 读取数据
        DataStreamSource<String> inputStream = env.readTextFile("/Users/peiyang/IdeaProjects/FlinkTurtorial/src/main/resources/sensor");


        //2. 转换POJO
        SingleOutputStreamOperator<SensorReading> ds = inputStream.map(new MapFunction<String, SensorReading>() {
            @Override
            public SensorReading map(String s) throws Exception {
                String[] fields = s.split(",");
                return new SensorReading(fields[0], Long.parseLong(fields[1]), Double.parseDouble(fields[2]));
            }
        }).assignTimestampsAndWatermarks(new BoundedOutOfOrdernessTimestampExtractor<SensorReading>(Time.seconds(2)) {
            @Override
            public long extractTimestamp(SensorReading sensorReading) {
                return sensorReading.getTimestamp() * 1000L;
            }
        });


        StreamTableEnvironment tableEnv = StreamTableEnvironment.create(env);


        // 4. 将流转换成表，定义时间特性
        //Table dataTable = tableEnv.fromDataStream(ds, "id, timestamp as ts, temperature as temp, pt.proctime");
        //Table dataTable = tableEnv.fromDataStream(ds, "id, timestamp.rowtime as rt, temperature as temp");
        Table dataTable = tableEnv.fromDataStream(ds, "id, timestamp as ts, rt.rowtime, temperature as temp");


        dataTable.printSchema();
        //tableEnv.toAppendStream(dataTable, Row.class).print();

        tableEnv.createTemporaryView("sensor",dataTable);

        // 5.窗口操作
        //5。1 Group Window
        //table API
        Table resultTable = dataTable.window(Tumble.over("10.seconds").on("rt").as("tw"))
                .groupBy("id, tw")
                .select(("id,id.count,temp.avg,tw.end"));

        //SQL
        Table resultSqlTable = tableEnv.sqlQuery("select id, count(id) as cnt, avg(temp) as avgTemp, tumble_end(rt, interval '10' second )"+
                "from sensor group by id, tumble(rt, interval '10' second)");


        //  打印
        //tableEnv.toRetractStream(resultTable, Row.class).print("agg");
        //tableEnv.toRetractStream(resultSqlTable, Row.class).print("sql");



        // 5.2 Over Window
        // table api
        Table overResult = dataTable.window(Over.partitionBy("id").orderBy("rt").preceding("2.rows").as("ow"))
                .select("id, rt, id.count over ow, temp.avg over ow");

        //SQL
        Table overSqlTable = tableEnv.sqlQuery("select id, rt, count(id) over ow, avg(temp) over ow " +
                "from sensor "+
                "window ow as (partition by id order by rt rows between 2 preceding and current row)");
        tableEnv.toRetractStream(overResult, Row.class).print("agg");
        tableEnv.toRetractStream(overSqlTable, Row.class).print("sql");

        env.execute();


    }
}

```









## 自定义函数

![image-20210920225603377](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/image-20210920225603377.png?token=AWS37JI675K3RMKSUF5XRCTBTIP42)



![image-20210920225629667](/Users/peiyang/Library/Application Support/typora-user-images/image-20210920225629667.png)



![image-20210920225637685](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/image-20210920225637685.png?token=AWS37JOYQYCQ26JA3HKNTYLBTIP5I)



![image-20210920225646138](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/image-20210920225646138.png?token=AWS37JO73EXZMTCHRNJKVGTBTIP6I)

```java
package com.peiyang.apitest.udf;

import com.peiyang.beans.SensorReading;
import lombok.Data;
import org.apache.flink.api.common.functions.MapFunction;
import org.apache.flink.streaming.api.datastream.DataStreamSource;
import org.apache.flink.streaming.api.datastream.SingleOutputStreamOperator;
import org.apache.flink.streaming.api.environment.StreamExecutionEnvironment;
import org.apache.flink.table.api.Table;
import org.apache.flink.table.api.java.StreamTableEnvironment;
import org.apache.flink.table.functions.ScalarFunction;
import org.apache.flink.types.Row;

public class UdfTest1_ScalarFunction {
    public static void main(String[] args) {
        StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
        env.setParallelism(1);

        StreamTableEnvironment tableEnv = StreamTableEnvironment.create(env);

        //1. 读取数据
        DataStreamSource<String> inputStream = env.readTextFile("/Users/peiyang/IdeaProjects/FlinkTurtorial/src/main/resources/sensor");


        //2. 转换POJO
        SingleOutputStreamOperator<SensorReading> ds = inputStream.map(new MapFunction<String, SensorReading>() {
            @Override
            public SensorReading map(String s) throws Exception {
                String[] fields = s.split(",");
                return new SensorReading(fields[0], Long.parseLong(fields[1]), Double.parseDouble(fields[2]));
            }
        });

        // 3. 将流转换成表

        Table sensorTable = tableEnv.fromDataStream(ds, "id,timestamp as ts,temperature");



        // 4. 自定义标亮函数，实现求id的hash值
        // 4.1 table API
        HashCode hashCode = new HashCode(23);
        //需要在环境中注册UDF
        tableEnv.registerFunction("hashCode", hashCode);

        Table resultTable = sensorTable.select("id, ts, hashCode(id)");

        // 4.2 SQL
        tableEnv.createTemporaryView("sensor",sensorTable);
        Table resultSqlTable = tableEnv.sqlQuery("select id, ts, hashCode(id) from sensor");

        tableEnv.toAppendStream(resultTable, Row.class).print("result");
        tableEnv.toAppendStream(resultSqlTable, Row.class).print("sql");




    }

    // 实现自定义的ScalarFunction
    @Data
    public static class HashCode extends ScalarFunction{

        private int factor = 13;

        public HashCode(int factor) {
            this.factor = factor;
        }

        public int eval(String str){
            return str.hashCode() * factor;
        }
    }


}

```

![image-20210920225656177](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/image-20210920225656177.png?token=AWS37JIUTJFCZZKOEEQ66ODBTIP6W)

![image-20210920225702495](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/image-20210920225702495.png?token=AWS37JPYUZSTY6TCRKKP2ADBTIP6M)

![image-20210920225708097](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/image-20210920225708097.png?token=AWS37JNZXNGCN3UVGYCO45DBTIP6U)
