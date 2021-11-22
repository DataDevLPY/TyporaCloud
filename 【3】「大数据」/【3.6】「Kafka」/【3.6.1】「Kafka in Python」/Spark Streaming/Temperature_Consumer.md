## Multiple sensors measuring the same things

```python
# Making imports
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

from kafka import KafkaConsumer
import datetime as dt
import json

# this line is needed for the inline display of graphs in Jupyter Notebook
%matplotlib notebook

topic = 'topic_temperature'

def connect_kafka_consumer():
    _consumer = None
    try:
         _consumer = KafkaConsumer(topic,
                                   consumer_timeout_ms=10000, # stop iteration if no message after 10 sec
                                   bootstrap_servers=['localhost:9092'],
                                   value_deserializer=lambda m: json.loads(m.decode('utf-8')),
                                   api_version=(0, 10))
    except Exception as ex:
        print('Exception while connecting Kafka')
        print(str(ex))
    finally:
        return _consumer

def plotSensorGraphs(df_orig,reduction,graph_title,ax_orig,ax_reduced):
    # Plot
    # Original graph
    ax_orig.clear()
    df_orig.plot(y=0,use_index=True,legend=False,ax=ax_orig)
    ax_orig.set(xlabel='Time', ylabel='Temperature', title=graph_title)
    # Reduced graph
    ax_reduced.clear()
    df_reduced = df_orig.resample(reduction).mean()
    df_reduced.plot(y=0,use_index=True,legend=False,ax=ax_reduced)
    ax_reduced.set(xlabel='Time', ylabel='Temperature', title=graph_title)
```

### Method 1: Reduce and then Merge

#### Step 1: Reduce the granularity of each individual sensor.

```python
def consume_messages(consumer):
    try:
        fig = plt.figure(figsize=(12,8))
        fig.subplots_adjust(hspace=0.8)
        # Create canvas with the 6 graphs (3 original + 3 reduced)
        ax_1a = fig.add_subplot(231)
        ax_1b = fig.add_subplot(234)
        ax_2a = fig.add_subplot(232)
        ax_2b = fig.add_subplot(235)
        ax_3a = fig.add_subplot(233)
        ax_3b = fig.add_subplot(236)
        
        fig.show() # displaying the figure
        fig.canvas.draw() # drawing on the canvas
        
        sensors = {}
        df_1a = pd.DataFrame(columns=['Temperature'], index=pd.to_datetime([]))
        df_2a = pd.DataFrame(columns=['Temperature'], index=pd.to_datetime([]))
        df_3a = pd.DataFrame(columns=['Temperature'], index=pd.to_datetime([]))
        # Dictionary with sensor ids and their dataframes
        sensors = {'1':df_1a, '2':df_2a, '3':df_3a}
        
        # Waiting for messages
        for message in consumer:
            data = message.value # Json object sent by each sensor
            # Add record to dataframe
            if data['uid'] in sensors:
                df_orig = sensors[data['uid']]
                record_time = pd.to_datetime(data['datetime'], infer_datetime_format=True)
                df_orig.loc[record_time] = pd.Series([float(data['temperature'])],['Temperature'])
                if data['uid'] == '1':
                    plotSensorGraphs(df_orig,'3H','Melbourne Airport',ax_1a,ax_1b)
                elif data['uid'] == '2':
                    plotSensorGraphs(df_orig,'3H','Melbourne OlympicPark',ax_2a,ax_2b)
                elif data['uid'] == '3':
                    plotSensorGraphs(df_orig,'3H','Essendon Airport',ax_3a,ax_3b)
                
            fig.canvas.draw() # drawing on the canvas
    except Exception as ex:
        print(str(ex))
```



```python
consumer = connect_kafka_consumer()
consume_messages(consumer)
```



#### Step 2: Merge reduced granularity into one data stream.



```python
def consume_messages(consumer):
    try:
        fig = plt.figure(figsize=(9,8))
        fig.subplots_adjust(hspace=0.8)
        # Create canvas with the 2 graphs (each sensor + combined)
        ax_all = fig.add_subplot(211)
        ax_reduced = fig.add_subplot(212)
        
        fig.show() # displaying the figure
        fig.canvas.draw() # drawing on the canvas
        
        sensors = {}
        df_1a = pd.DataFrame(columns=['Temperature'], index=pd.to_datetime([]))
        df_2a = pd.DataFrame(columns=['Temperature'], index=pd.to_datetime([]))
        df_3a = pd.DataFrame(columns=['Temperature'], index=pd.to_datetime([]))
        # Dictionary with sensor ids and their dataframes
        sensors = {'1':{'orig':df_1a,'reduced':None},
                   '2':{'orig':df_2a,'reduced':None},
                   '3':{'orig':df_3a,'reduced':None}}
        
        # Function to plot the graphs
        def plotSensorGraphs(df_merge,series_merged_avg):
            # Plot
            # Original 3 graphs
            ax_all.clear()
            df_merge.plot(y=[0,1,2],use_index=True,legend=False,ax=ax_all)
            ax_all.legend(["Melbourne Airport","Melbourne Olympic Park","Essendon Airport"]);
            ax_all.set(xlabel='Time', ylabel='Temperature', title='Melbourne')
            # Reduced graph
            ax_reduced.clear()
            series_merged_avg.plot(use_index=True,ax=ax_reduced)
            ax_reduced.legend(["Average 3hours"]);
            ax_reduced.set(xlabel='Time', ylabel='Temperature', title='Melbourne')
        
        df_1,df_2,df_3 = sensors['1']['orig'],sensors['2']['orig'],sensors['3']['orig']
        
        # Waiting for messages
        for message in consumer:
            data = message.value # Json object sent by each sensor
            if data['uid'] in sensors:
                df_orig = sensors[data['uid']]['orig']
                record_time = pd.to_datetime(data['datetime'], infer_datetime_format=True)
                df_orig.loc[record_time] = pd.Series([float(data['temperature'])],['Temperature'])
                # Reduced granularity of data
                sensors[data['uid']]['reduced'] = df_orig.resample('3H').mean()
                # Merge reduced data
                df_merge = df_1.merge(df_2, left_index=True, right_index=True).merge(df_3, left_index=True, right_index=True)
                series_merged_avg = df_merge.mean(axis = 1, skipna = True)
                try:
                    plotSensorGraphs(df_merge,series_merged_avg)
                except Exception as ex:
                    print(str(ex))
            fig.canvas.draw() # drawing on the canvas
    except Exception as ex:
        print(str(ex)) 
```



```python
consume_messages(consumer)
```









