# CS3219-OTOT-Task-D

Note that the section on producers and consumers should be run from different terminals

## Part 1: Basic pub-sub

### Set up Zookeeper-Kafka
```
docker compose up -d
```
Wait for the docker compose setup to finish spooling up.

To view the progress in docker compose, run the following command instead:
```
docker compose up
```
Wait until the log messages stop being generated

### Set up topic
```
docker run -it --rm --network kafka_docker_example_net confluentinc/cp-kafka /bin/kafka-topics --bootstrap-server kafka-1:9092 --topic test_topic --create --partitions 1 --replication-factor 3
```

### Set up producer
```
docker run -it --rm --network kafka_docker_example_net confluentinc/cp-kafka /bin/kafka-console-producer --bootstrap-server kafka-1:9092 --topic test_topic
```
Note that this can set up the topic and the producer in one step, but would result in warnings.

### Set up consumer
```
docker run -it --rm --network kafka_docker_example_net confluentinc/cp-kafka /bin/kafka-console-consumer --bootstrap-server kafka-2:9092 --topic test_topic --from-beginning
```
Note that we use a different bootstrap server to demonstrate the multi-node nature of Kafka.

## Part 2: Demonstrate failover

### Get topic leader
```
docker run -it --rm --network kafka_docker_example_net confluentinc/cp-kafka /bin/kafka-topics --bootstrap-server kafka-2:9092 --describe --topic test_topic
```
This will result in an output with the following output format, or something similar:
```
Topic: test_topic    TopicId: DqZUWLGOR5y591SWC3ZNTQ    PartitionCount: 1    ReplicationFactor: 3 
  Configs:
    Topic: test_topic    Partition: 0    Leader: 3    Replicas: 3,1,2    Isr: 3,1,2
```
The last line will contain the following information:
* Topic: test_topic    
* Partition: 0    
* Leader: 3    
* Replicas: 3,1,2    
* Isr: 3,1,2
In particular, the third part, the "Leader" indicates what needs to be killed to test failover. 
Here, the Kafka node with id=3 is what we need to kill

### Test failover

Suppose we find out that the partition leader has id=3. 
By how we specified the `docker-compose.yaml` file, this will correspond to the container with the name:
```
cs3219-otot-task-d-kafka-3-1
```
where the id is inserted as the second last number of the container name. 

Then, run the command:
```
docker container stop cs3219-otot-task-d-kafka-3-1
```
Replace the last argument with the appropriate container name, if the id is different from the example

If you have Docker Desktop, you can use the UI to manually kill the container. 