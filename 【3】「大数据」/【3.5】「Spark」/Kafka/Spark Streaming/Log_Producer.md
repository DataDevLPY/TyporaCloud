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
        print('Message published successfully. Data: ' + str(data))
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
    topic = 'w10_access_log'
    print('Publishing records..')
    producer = connect_kafka_producer()
    
    log_file = 'access_log.txt'
    
    
    #WRITE THE CODE HERE
    #Read the file and publish 1 line every 1 second to the topic
    with open(log_file, 'r') as read_file:
        lines = read_file.readlines()
        
        index = 0
        while True:
            data = lines[index]
            index += 1
            
            publish_message(producer, topic, data)
            if index >=len(lines):
                i = 0
            sleep(1)
```

![截屏2021-01-23 下午2.44.02](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-23 下午2.44.02.png)

