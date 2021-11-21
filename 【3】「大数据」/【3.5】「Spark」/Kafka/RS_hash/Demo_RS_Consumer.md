```python
# import statements
from kafka import KafkaConsumer

topic = 'LectureDemoWeek10'
hash_table_R = {}
hash_table_S = {}

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

def consume_messages(consumer):
    try:
        for message in consumer:
            data = str(message.value.decode('utf-8')).split(',')
            table = data[0]
            key = data[1]
            value = data[2]
            # Implementation of bounded Symmetric Hash Join
            if table == 'R':
                # Step 1: Probe tuple into hash table S
                if key in hash_table_S:
                    matching_records = []
                    for values in hash_table_S[key]:
                        if key == values[0]:
                            matching_records.append([key, value, values[1]])
                    # Step 2: Show the join results
                    print(matching_records)
                # Step 3: Hash tuple into hash table R
                if key in hash_table_R:
                    hash_table_R[key].append([key,value])
                else:
                    hash_table_R[key] = []
                    hash_table_R[key].append([key,value])
                    
            else:
                # Step 1: Probe tuple into hash table R
                if key in hash_table_R:
                    matching_records = []
                    for values in hash_table_R[key]:
                        if key == values[0]:
                            matching_records.append([key, value, values[1]])
                    # Step 2: Show the join results
                    print(matching_records)
                # Step 3: Hash tuple into hash table S
                if key in hash_table_S:
                    hash_table_S[key].append([key,value])
                else:
                    hash_table_S[key] = []
                    hash_table_S[key].append([key,value])
                    
    except Exception as ex:
        print(str(ex))
```

```python
consumer = connect_kafka_consumer()
consume_messages(consumer)
```

![截屏2021-01-22 下午10.16.40](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220103062.png?token=AWS37JOPT3CVDT2MADW2ACLBTJ6A2)

