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
topic = 'Climate'
print('Publishing records..')
producer = connect_kafka_producer()
climate_records = ["-36.704,144.252,19,56.8,7.9",
"-36.748,145.486,15,50.7,9.2",
"-36.216,146.388,16,53.6,8.1",
"-36.218,146.377,24,61.6,7.7",
"-37.232,143.252,24,62.3,7"]
climate_header =['latitude','longitude','air_temperature_celcius','relative_humidity','wind_speed_knots']

for e in range(1000):
    record = climate_records[random.randrange(0,5)]
    key = geohash.encode(float(record[1:7]), float(record[9:15]), 3)
    data = str(record)
    publish_message(producer, topic, key, data)
    sleep(10)
```

![截屏2021-01-23 下午3.26.13](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-23 下午3.26.13.png)