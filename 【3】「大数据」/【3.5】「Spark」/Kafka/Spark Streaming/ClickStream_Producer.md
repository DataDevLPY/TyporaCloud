```python
# import statements
from time import sleep
from json import dumps
from kafka import KafkaProducer
import random
import datetime as dt
import csv


def readCSVFile(fileName):
    list=[]
    with open('clickstream.csv', 'rt') as f:
        reader = csv.DictReader(f)
        for row in reader:
            list.append({'Clicks':int(row['Clicks']),'Impressions':int(row['Impressions'])})
    return list

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
    
    topic = 'clickstream'
    cRows = readCSVFile('clickstream.csv')
    
    print('Publishing records..')
    producer = connect_kafka_producer()
    
    #sending single object
    rows_to_send=1
    start_index=0
    while True:        
        to_send = cRows[start_index]        
        #increase the start index by the number of rows taken
        start_index = start_index+rows_to_send
        
        #define the current timestamp
        ts = {'ts': int(dt.datetime.now().timestamp())}
        
        #append the timestamp into the object to be sent
        data=dict(to_send,**ts)
        print(data)
        
        publish_message(producer, topic, data)
        
        #reset to start from begining
        if(start_index>=len(cRows)):
            start_index=0
        sleep(1)
        
```

![截屏2021-01-23 下午1.27.25](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-23 下午1.27.25.png)