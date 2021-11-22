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
        print('Message published successfully. Data: ' + str(data))
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
    
if __name__ == '__main__':
    
    topic = 'Week9-Topic'
    
    print('Publishing records..')
    producer = connect_kafka_producer()
    
    while True:
        data = str(dt.datetime.now().strftime("%X")) + ', ' + str(random.randrange(0,100))
        publish_message(producer, topic, 'parsed', data)
        sleep(1)
```

![截屏2021-01-22 下午8.46.28](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220105127.png?token=AWS37JK5XMYHQ37GYLTWUA3BTJ6KO)