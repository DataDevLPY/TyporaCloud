```python
# import statements
from time import sleep
from json import dumps
from kafka import KafkaProducer
import random
import datetime as dt
import csv
from datetime import datetime


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
    start_index=0
    
    rows_to_send=random.randint(5,10)
    late_rows_to_send=random.randint(1,3)
    late_data_to_send = []
    
    while True:        
        
        ts = {'ts': int(dt.datetime.now().timestamp())}
        to_send = cRows[start_index:start_index+rows_to_send]
        #increase the start index by the number of rows taken
        start_index = start_index+rows_to_send
        #append the timestamp into the object to be sent
        data=[dict(item,**ts) for item in to_send]        
        
        #late data
        to_send = cRows[start_index:start_index+late_rows_to_send]
        #increase the start index by the number of rows taken
        start_index = start_index+late_rows_to_send
        #append the timestamp into the object to be sent        
        late_data=[dict(item,**ts) for item in to_send]
            
        #publish both current and late data
        #the late data (from previous cycle is appended to the current data to be sent)
        data.extend(late_data_to_send)
        publish_message(producer, topic, data)
        
        #display the timestamps being sent
        for d in data:
            print(datetime.fromtimestamp(d['ts']),'impressions:',d['Impressions'])
        print('-------------------------')
        
        #after the current batch is sent, set the late data 
        late_data_to_send=late_data
        
        #reset to start from begining
        if(start_index>=len(cRows)):
            start_index=0
        sleep(5)
        
```

![截屏2021-01-23 下午5.04.13](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-23 下午5.04.13.png)

