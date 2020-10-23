# Demo of Multinode Kafka and Zookeeper Cluster
This cluster consists of 3 zookeeper nodes and 3 kafka nodes, for a total of 6 containers. This allows 1 zookeeper node and 1 kafka node to fail (while still ensuring the majority of 66.6% is still up) and allow the pub-sub to continue running without interruptions. This is achieved with docker images `confluentinc/cp-zookeeper:latest` and `confluentinc/cp-kafka:latest`

## Setup
To run the configured containers:

`docker-compose -f docker-compose.yml up -d`

## Demo
To add a topic:

`docker run --net=host --rm confluentinc/cp-kafka:latest kafka-topics --create --topic <topic-name> --partitions 1 --replication-factor 3 --if-not-exists --zookeeper localhost:32181`