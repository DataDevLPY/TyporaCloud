```linux
# Automatic starting
sudo systemctl enable kafka
```

# VirtualBox VMs

```linux
# Automatic starting
sudo systemctl enable kafka
```

```linux
# hand start kafka
sudo systemctl start kafka.service
# To check the status
sudo systemctl status kafka.service
# To stop kafka
sudo systemctl stop kafka.service
```



# In my own environment

1. START THE KAFKA ENVIRONMENT

```linux
# Start the ZooKeeper service
# Note: Soon, ZooKeeper will no longer be required by Apache Kafka.
$ bin/zookeeper-server-start.sh config/zookeeper.properties
```

Open another terminal session and run:

```linux
# Start the Kafka broker service
$ bin/kafka-server-start.sh config/server.properties

```

2. CREATE A TOPIC TO STORE YOUR EVENTS

   ```linux
   $ bin/kafka-topics.sh --create --topic quickstart-events --bootstrap-server localhost:9092
   ```

   

   ```
   $ bin/kafka-topics.sh --describe --topic quickstart-events --bootstrap-server localhost:9092
   Topic:quickstart-events  PartitionCount:1    ReplicationFactor:1 Configs:
       Topic: quickstart-events Partition: 0    Leader: 0   Replicas: 0 Isr: 0
   ```

   

3. WRITE SOME EVENTS INTO THE TOPIC

   ```
   $ bin/kafka-console-producer.sh --topic quickstart-events --bootstrap-server localhost:9092
   
   sdasdasdasda
   ```

   

4. READ THE EVENTS

   ```
   $ bin/kafka-console-consumer.sh --topic quickstart-events --from-beginning --bootstrap-server localhost:9092
   ```

   



Ctrl + C 



































