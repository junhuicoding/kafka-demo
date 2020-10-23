# Demo of Multinode Kafka and Zookeeper Cluster
This cluster consists of 3 zookeeper nodes and 3 kafka nodes, for a total of 6 containers. This allows 1 zookeeper node and 1 kafka node to fail (while still ensuring the majority of 66.6% is still up) and allow the pub-sub to continue running without interruptions. This is achieved with docker images `confluentinc/cp-zookeeper:latest` and `confluentinc/cp-kafka:latest`

## Setup
To run the configured containers:

`docker-compose -f docker-compose.yml up -d`

## Demo
To add a topic with 1 partition and 3 replication factior (for the 3 zookeeper nodes):

`docker run --net=host --rm confluentinc/cp-kafka:latest kafka-topics --create --topic <topic-name> --partitions 1 --replication-factor 3 --if-not-exists --zookeeper localhost:22181`

To get more information about a particular topic:

`docker run --net=host --rm confluentinc/cp-kafka:latest kafka-topics --describe --topic <topic-name> --zookeeper localhost:22181`

There is an function to produce a sequence of `n` numbers that you can use in the kafka container. To produce 10 consecutive numbers and have it echo when it is done:

`docker run --net=host --rm confluentinc/cp-kafka:latest bash -c “seq 10 | kafka-console-producer --broker-list localhost:19092 --topic <topic-name>  && echo ‘Producer 10 message.’”`

To consume the messages sent since the beginning:

`docker run --net=host --rm confluentinc/cp-kafka:latest kafka-console-consumer --bootstrap-server localhost:19092 --topic <topic-name> --from-beginning`

Testing it on another node:

`docker run --net=host --rm confluentinc/cp-kafka:latest kafka-console-consumer --bootstrap-server localhost:29092 --topic <topic-name> --from-beginning`

## Fault Tolerance
To test fault tolerance, stop the kafka container which the messages were published in. A consumer (from one of the two running kafka nodes) should then be able to still consume all messages sent since the start:

`docker run --net=host --rm confluentinc/cp-kafka:latest kafka-console-consumer --bootstrap-server localhost:39092 --topic topic1  --from-beginning`

The zookeeper ensemble can be tested as well. The leader container (verified when first building the containers) should be stopped. The pub-sub can be again by publishing a new series of numbers (on a kafka container that has not been stopped), and a consumer would still able to consumer the 2 sequences of numbers since the start correctly.
