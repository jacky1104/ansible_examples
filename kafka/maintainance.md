### Download the client tool
```markdown
wget https://apache.mirror.colo-serv.net/kafka/2.7.0/kafka_2.13-2.7.0.tgz
tar -xzf kafka_2.13-2.7.0.tgz
cd kafka_2.13-2.7.0
```
### Make sure zookeeper-1/2/3 can be accessed.
```markdown
ping zookeepeer-1
```
### How to create topic
```markdown
./kafka-topics.sh --create --zookeeper zookeeper-1:2181 --replication-factor 3 --partitions 3 --topic test_topic
```
### How to describe topic
```markdown
./kafka-topics.sh --zookeeper zookeeper-1:2181 --describe --topic test_topic
```
### How to check the consumer group
```markdown
./kafka-consumer-groups.sh --bootstrap-server zookeeper-1:9092 --describe --group test_group
./kafka-consumer-groups.sh --bootstrap-server zookeeper-1:9092 --describe --group test_group --members
```
### How to delete entries in kafka?
```markdown
// set 1 hour, default 7 days
./kafka-topics.sh --zookeeper zookeeper-1:2181 --alter --topic test_topic --config retention.ms=3600000
// set 10 KB, default unlimited, in our cluster, is 100G.
./kafka-topics.sh --zookeeper zookeeper-1:2181 --alter --topic test_topic --config retention.bytes=10240
You have to wait for log.retention.check.interval.ms which defaults to 5 minutes
```
### How many replications?
Default is one, the cluster will use the `__consumer_offsets` topic to store the offsets, if the offsets lost, the consumers will occur errors.
So set this value will be better in our cluster.
env values:
```markdown
KAFKA_DEFAULT_REPLICATION_FACTOR = 3
```
### How to balance the cluster?
If we need enlarge the cluster, we need add brokers to cluster.
```markdown
1. update the docker-compose filter, add new `extra_hosts` entries.
2. Add .env file for the new broker, keep the id unique.
3. move the partitions to all the cluster, for example, we have broker 1,2,3 right now, we add broker 4, 5.
prepare the json file: topic_move.json
   1) generate the balance.json
./kafka-reassign-partitions.sh --zookeeper zookeeper-1:2181 --topics-to-move-json-file topic_move.json --broker-list "1,2,3,4,5" --generate
this will generate a json template, please copy the result to another json file, named balance.json.
   2) execute
./kafka-reassign-partitions.sh --zookeeper zookeeper-1:2181 --reassignment-json-file  balance.json --execute
  3) verify 
./kafka-reassign-partitions.sh --zookeeper zookeeper-1:2181 --reassignment-json-file  balance.json --verify
```
topic_move.json
```markdown
{
  "topics": [
    {
      "topic": "test_topic"
    }
  ],
  "version": 1
}
```
### How to performance test?
- producer
```markdown
./kafka-producer-perf-test.sh --producer-props bootstrap.servers=zookeeper-1:9092,zookeeper-2:9092,zookeeper-3:9092  --num-records 3000000  --topic test_topic --throughput 20000 --payload-file sandbox.2021-03-03.log
```
-consumer
```markdown
/kafka-consumer-p^Cf-test.sh --topic test_topic --bootstrap-server zookeeper-1:9092 --messages 1000000
```
### What if one node down?
```markdown
1. If the node still can be accessed, clear the disk, and just exec `docker-compose start`
2. If the node can't be accessed, please setup another node which ip the same as the failure one, and prepare the `.env` and `docker-compose` file the same as the failure one too.
Then exec `docker-compose up -d`
```
### What if two nodes down?
If two nodes breaks down for 3 nodes  cluster, the cluster won't work anymore.
Recover the two nodes one by one as [What if one node down?](#What-if-one-node-down?).
