Quick Commands


```bash
## create a new topic
./kafka-topics.sh --create --topic topic_name --partitions 3 --replication-factor 3 --bootstrap-server  localhost:9092

## list topics
./kafka-topics.sh --list --bootstrap-server  localhost:9092

## describe topics in detail 
./kafka-topics.sh --describe --bootstrap-server  localhost:9092

## delete topic
./kafka-topics.sh --delete --topic topic_name --bootstrap-server  localhost:9092

## produce message from cli (without key)
./kafka-console-producer.sh --bootstrap-server  localhost:9092 --topic topic_name

## produce message from cli (with key)
./kafka-console-producer.sh --bootstrap-server  localhost:9092 --topic topic_name --property="parse.key=true" --property="key.separator=:"

## consume message from cli (without key)
./kafka-console-consumer.sh --bootstrap-server  localhost:9092 --topic topic_name

## consume message from cli (without key)
./kafka-console-consumer.sh --bootstrap-server  localhost:9092 --topic topic_name --from-beginning

## consume message from cli (with key)
./kafka-console-consumer.sh --bootstrap-server  localhost:9092 --topic topic_name \
--property print.key=true  \
--property print.value=truev \ 
--from-beginning
```

## Q-If a topic has a replication factor of 3

Each partition will live on 3 different brokers




