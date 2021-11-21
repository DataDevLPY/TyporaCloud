```python
#Requires python geohash
#Install geohash using !pip install python-geohash
# import statements
from time import sleep
from json import dumps
from kafka import KafkaProducer
import random
import datetime as dt
import geohash

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
topic = 'Terra'
print('Publishing records..')
producer = connect_kafka_producer()
terra_records = ["-36.704,144.252,89.5,90,66",
"-36.748,145.486,21,99,46",
"-36.216,146.388,19.9,81,59",
"-36.218,146.377,22.5,83,60",
"-37.232,143.252,30.5,80,55",
"-36.822,143.854,22,83,59",
"-36.821,143.865,215.2,100,122",
"-36.812,143.863,94,100,93",
"-36.819,143.877,55.3,95,78",
"-36.813,143.852,36.2,90,69"]
terra_header = ['latitude','longitude','power','confidence','surface_temperature_celcius']
for e in range(1000):
    record = terra_records[random.randrange(0,10)]
    key = geohash.encode(float(record[1:7]), float(record[9:15]), 3)
    data = str(record)
    publish_message(producer, topic, key, data)
    sleep(2)
```

![截屏2021-01-23 下午3.26.53](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-23 下午3.26.53.png)