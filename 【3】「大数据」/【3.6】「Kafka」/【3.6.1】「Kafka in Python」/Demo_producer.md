```python
# import statements
from time import sleep
from json import dumps
from kafka import KafkaProducer
import random
import datetime as dt


def publish_message(producer_instance, topic_name, key, value):
    try:
        key_bytes = bytes(key, encoding='utf-8')
        value_bytes = bytes(value, encoding='utf-8')
        producer_instance.send(topic_name, key=key_bytes, value=value_bytes)
        producer_instance.flush()
        print('Data: ' + str(data))
    except Exception as ex:
        print('Exception in publishing message.')
        print(str(ex))
        
def connect_kafka_producer():
    _producer = None
    try:
        _producer = KafkaProducer(bootstrap_servers=['localhost:9092'],
                                  api_version=(0, 10))
    except Exception as ex:
        print('Exception while connecting Kafka.')
        print(str(ex))
    finally:
        return _producer
```



```python
topic = 'LectureDemo'
print('Publishing records..')
producer = connect_kafka_producer()

# Data format: time, incoming attendees, outgoing attendees
# Assumption: For the sake of demonstartion we assume 
# - For the first 5 seconds there will be no outgoing attendees.
# - Every second we can have 1 to 5 incoming attendees.
# - Every second we can have 0 to 5 outgoing attendees.
for e in range(100):
    if e < 5:
        data = str(dt.datetime.now().strftime("%X")) + ',' + str(random.randrange(1,5))  + ',-0'
    else:
        data = str(dt.datetime.now().strftime("%X")) + ',' + str(random.randrange(1,5))  + ',-' + str(random.randrange(0,5))
    publish_message(producer, topic, 'parsed', data)
    sleep(1)
```

![截屏2021-01-22 下午7.49.59](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-22 下午7.49.59.png)