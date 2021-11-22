

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
#         json.dumps(record.asDict())
        producer_instance.send(topic_name, data)
        print(str(data))
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
    
    topic = 'topic_temperature'
    print('Publishing records..')
    producer01 = connect_kafka_producer()
    producer02 = connect_kafka_producer()
    producer03 = connect_kafka_producer()
    
    file1 = 'Melbourne-Airport.csv'
    file2 = 'Melbourne-OlympicPark.csv'
    file3 = 'Essendon-Airport.csv'
    
    with open(file1) as csvfile1:
        with open(file2) as csvfile2:
            with open(file3) as csvfile3:
                reader1 = csv.reader(csvfile1, delimiter=',')
                reader2 = csv.reader(csvfile2, delimiter=',')
                reader3 = csv.reader(csvfile3, delimiter=',')
                next(reader1) # Skip header1
                next(reader2) # Skip header2
                next(reader3) # Skip header3
                for ix, row in enumerate(reader1):
                    # Each stream will send a unique identifier (uid) to know which sensor the record belongs to
                    data1 = {'uid':'1','datetime': str(row[0]), 'temperature' : str(row[1])}
                    data2_row = next(reader2)
                    data2 = {'uid':'2','datetime': str(row[0]), 'temperature' : str(data2_row[1])}
                    data3_row = next(reader3)
                    data3 = {'uid':'3','datetime': str(row[0]), 'temperature' : str(data3_row[1])}
                    publish_message(producer01, topic, data1)
                    publish_message(producer02, topic, data2)
                    publish_message(producer03, topic, data3)
                    sleep(0.7)
                    print ("=========================================================================")
```



![截屏2021-01-23 下午3.48.37](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-23 下午3.48.37.png)