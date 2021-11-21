# FIT5202 Data processing for big data

# Activity: Streaming Data using Apache Kafka

In this activity, we are going to discuss Apache Kafka and how Spark streaming programmers can use it for building distributed systems. Apache Kafka® is a distributed streaming platform. It is an open-source streaming platform that was initially built by LinkedIn. It was later handed over to Apache foundation and open sourced it in 2011.

## 1. Overview

### 1.1 What is Apache Kafka?

According to Wikipedia:

Apache Kafka is an open-source stream-processing software platform developed by the Apache Software Foundation, written in Scala and Java. The project aims to provide a unified, high-throughput, low-latency platform for handling real-time data feeds. Its storage layer is essentially a “massively scalable pub/sub message queue architected as a distributed transaction log,” making it highly valuable for enterprise infrastructures to process streaming data. Additionally, Kafka connects to external systems (for data import/export) via Kafka Connect and provides Kafka Streams, a Java stream processing library.

Think of it is a big commit log where data is stored in sequence as it happens. The users of this log can just access and use it as per their requirement.

### 1.2 Kafka Use Cases

Uses of Kafka are multiple. Here are a few use-cases that could help you to figure out its usage.

- #### Messaging

  Kafka can be used as a message broker among services. If you are implementing a microservice architecture, you can have a microservice as a producer and another as a consumer. For instance, you have a microservice that is responsible to create new accounts and other for sending email to users about account creation.

- #### Activity Monitoring

  Kafka can be used for activity monitoring. The activity could belong to a website or physical sensors and devices. Producers can publish raw data from data sources that later can be used to find trends and pattern.

- #### Log Aggregation

  Kafka can be used to collect logs from different systems and store in a centralized system for further processing.

- #### Extract Transform and Load (ETL)

  Kafka has a feature of almost real-time streaming thus you can come up with an ETL based on your need.

- #### Database

  Kafka can also acts as a database. It is not a typical databases that have a feature of querying the data as per need, but you can keep data in Kafka as long as you want without consuming it (Message queing).

### 1.3 Kafka Concepts

Let’s discuss some core Kafka concepts.![截屏2021-01-22 下午7.54.15](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-22 下午7.54.15.png)

#### Topics

Every message that is feed into the system must be part of some *topic*. The topic is nothing but a stream of records. The messages are stored in *key-value* format. Each message is assigned a sequence, called *Offset*. The output of one message could be an input of the other for further processing.

#### Producers

*Producers* are the applications responsible to publish data into Kafka system. They publish data on the *topic* of their choice.

#### Consumers

The messages published into *topics* are then utilized by *Consumer* applications. A *consumer* gets subscribed to the *topic* of its choice and consumes data.

#### Broker

Every instance of Kafka that is responsible for message exchange is called a *Broker*. Kafka can be used as a stand-alone machine or a part of a cluster.

### 1.4 Setting up and Running

The easiest way to install Kafka is to download binaries and run it. Since it’s based on JVM languages like Scala and Java, you must make sure that you are using Java 7 or greater. If you want to setup Apache Kafka in your personal machine, you can find the tutorial [here](https://kafka.apache.org/quickstart).

*Note: For simplicity, the virtual machines (VM) provided to the students and used in the labs have been configured to run Apache Kafka during the start up of the operating system. *

### 1.5 Accessing Kafka in Python

There are multiple python libraries available to connect Apache Kafka and Python together. We are going to use an open-source community-based library known as [Kafka-Python](https://github.com/dpkp/kafka-python).

You can install the library using the command below:

> **!pip3 install kafka-python**

*Note: This is a python library that facilitates accessing Apache Kafka Producer and Consumer. We will see how we can access Kafka Producer from Apache Spark Streaming in the next section.*

## 2. Streaming Scenarios

Now, we will look into different scenarios on handling data streams as they come. We are not going to process or store data, but just display the data on the fly.

### Producer

A producer `2.Kafka_producer.ipynb` is provided, which continiously streams data (i.e. timestamp and a random count) to a topic **Week9-Topic**. For each of the following scenarios, a separate consumer is implemented in Kafka which subscribes the topic and performs various analysis and visualizations.

**Run this file first before starting the other consumers**

### Scenario #01

The incoming data has uniform arrival. The data comes, and we display it in the graph. For simplicity, we assume that the incoming data is within a specified range, so it will be easy to prepare the display because we know the max (and the min) of y-axis and the x-axis is the arrival time. This is a line graph.

Run the following file to see the real-time plotting in action.

- `3.Kafka_Consumer01.ipynb`

### Scenario #02

We want to improve the graph in Scenario 01 by showing the label of some interesting points such as minimum and maximum values. Run the following file in to see the real-time plotting in action.

- `4. Kafka_Consumer02.ipynb`

### Scenario #03

Scenario 01 and 02 assumes that the incoming data is uniform in terms of their arrivals. Now, how about if the incoming data is bursty. The interval between data is not uniform. The *creation time* in the producer will be different from the *arrival time* in the consumer. Run the following file to see the real-time plotting in action.

- `5. Kafka_Consumer03.ipynb`

### Scenario #04

We want to do some simple processing of the incoming data on the fly. The data source is still one but we want to show the second line graph which is the value of moving window (such as moving average). So the moving average of the current time is the average value of, for example, the previous 5 values. Run the following file to see the real-time plotting in action.

- `6. Kafka_Consumer04.ipynb`

### Scenario #05

Now lets consider we have multiple data sources (say for example 3 producers). We want to plot data from all three producers as well as average of data from all three producers at the given time. So, in total, we will have 4 graphs. Run the following files in sequential order to see the real-time plotting in action.

- `7. Kafka_Multiple_Producers.ipynb`
- `8. Kafka_Consumer05.ipynb`
- 