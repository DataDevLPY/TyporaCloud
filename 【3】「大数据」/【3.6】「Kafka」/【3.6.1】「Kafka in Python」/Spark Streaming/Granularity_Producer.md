```python
# import statements
from time import sleep
from json import dumps
from kafka import KafkaProducer
import random
import datetime as dt
import csv

def publish_message(producer_instance, topic_name, data):
    try:
        producer_instance.send(topic_name, data)
        #print('Message published successfully. Data: ' + str(data))
    except Exception as ex:
        print('Exception in publishing message.')
        print(str(ex))
        
def connect_kafka_producer():
    _producer = None
    try:
        _producer = KafkaProducer(bootstrap_servers=['localhost:9092'],
                                  value_serializer=lambda x: dumps(x).encode('ascii'),
                                  api_version=(0, 10))
    except Exception as ex:
        print('Exception while connecting Kafka.')
        print(str(ex))
    finally:
        return _producer
    
if __name__ == '__main__':
    
    topic = 'week11_orig_data'
    
    print('Publishing records..')
    producer = connect_kafka_producer()
    i=1
    while True:
        #generate object to be sent
        data = {'ts': int(dt.datetime.now().timestamp()), 'value': int(random.randrange(0,90))}
        print(data)
        
        publish_message(producer, topic, data)
        sleep(1)
        i+=1
        if i>100:
            i=1
        
```

![截屏2021-01-23 下午9.09.55](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-23 下午9.09.55.png)

