# CS3219-OTOT-Task-D

Note that the section on producers and consumers should be run from different terminals

## Part 1: Basic pub-sub

### Set up zookeeper-kafka
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
Note that we use a different bootstrap server to demonstrate the multi-node nature of kafka.

## Part 2: Demonstrate failover

### Get topic leader
```
docker run -it --rm --network kafka_docker_example_net confluentinc/cp-kafka /bin/kafka-topics --bootstrap-server kafka-2:9092 --describe --topic test_topic
```

### Test failover

//TODO: Complete empty sections