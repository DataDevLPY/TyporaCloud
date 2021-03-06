

```python
# import statements
from kafka import KafkaConsumer
import matplotlib.pyplot as plt

# this line is needed for the inline display of graphs in Jupyter Notebook
%matplotlib notebook

topic = 'LectureDemo'

def connect_kafka_consumer():
    _consumer = None
    try:
         _consumer = KafkaConsumer(topic,
                                   consumer_timeout_ms=10000, # stop iteration if no message after 10 sec
                                   auto_offset_reset='latest', # comment this if you don't want to consume latest available message
                                   bootstrap_servers=['localhost:9092'],
                                   api_version=(0, 10))
    except Exception as ex:
        print('Exception while connecting Kafka')
        print(str(ex))
    finally:
        return _consumer

def init_plots():
    try:
        width = 9
        height = 6
        fig = plt.figure(figsize=(width,height)) # create new figure
        ax = fig.add_subplot(111) # adding the subplot axes to the given grid position
        fig.suptitle('What is the total number of attendees up to tx?') # giving figure a title
        ax.set_xlabel('Time')
        ax.set_ylabel('Total Number of Attendees')
        fig.show() # displaying the figure
        fig.canvas.draw() # drawing on the canvas
        return fig, ax
    except Exception as ex:
        print(str(ex))
    
def consume_messages(consumer, fig, ax):
    try:
        total_count = 0
        # container for x and y values
        x, y = [], []
        for message in consumer:
            data = str(message.value.decode('utf-8')).split(',')
            event_time = data[0]
            x.append(event_time) 
            incoming_count = int(data[1])
            outgoing_count = int(data[2])
            total_count += (incoming_count + outgoing_count)
            y.append(total_count)
            # we start plotting only when we have 10 data points
            if len(y) > 10:
                ax.clear()
                ax.plot(x, y)
                text = 'Time={}\nValue={}'.format(event_time, total_count)
                ax.annotate(text, xy=(event_time, total_count), xytext=(event_time, total_count), arrowprops=dict(facecolor='red', shrink=0.05),)
                ax.set_xlabel('Time')
                ax.set_ylabel('Total Number of Attendees')
                fig.canvas.draw()
                x.pop(0) # removing the item in the first position
                y.pop(0)
        plt.close('all')
    except Exception as ex:
        print(str(ex))
```



```python
consumer = connect_kafka_consumer()
fig, ax = init_plots()
consume_messages(consumer, fig, ax)
```

![??????2021-01-22 ??????7.52.47](/Users/peiyang/Library/Application Support/typora-user-images/??????2021-01-22 ??????7.52.47.png)

