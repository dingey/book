# 单机
## 第一步 搭建zookeeper环境

```
docker pull wurstmeister/zookeeper
docker run -d --name zookeeper -p 2181:2181 -t wurstmeister/zookeeper
```
## 第二步 创建kafka环境

```
docker run -d --name kafka -p 9092:9092 -e KAFKA_BROKER_ID=0 -e KAFKA_ZOOKEEPER_CONNECT=106.14.175.92:2181 -e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://106.14.175.92:9092 -e KAFKA_LISTENERS=PLAINTEXT://0.0.0.0:9092 -e KAFKA_HEAP_OPTS="-Xmx256M -Xms256M" -m 256M --oom-kill-disable -t wurstmeister/kafka
```
注：由于docker kafka版本不断迭代，KAFKA_ADVERTISED_HOST_NAME 不再建议是用localhost/127.0.0.1,故需要使用KAFKA_ADVERTISED_HOST_NAME= ip地址

## 第三步 验证kafka是否正确安装
进入到kafka容器中 并创建topic 生产者，执行如下命令：
```
docker exec -it kafka bash
cd /opt/kafka_2.11-2.0.0/bin/
./kafka-topics.sh --create --zookeeper zookeeper:2181 --replication-factor 1 --partitions 8 --topic test
./kafka-console-producer.sh --broker-list localhost:9092 --topic test
```
执行上诉命令后，另起一个标签页，执行如下命令 创建kafka消费者消费消息：
```
docker exec -it kafka bash
cd /opt/kafka_2.11-2.0.0/bin/
./kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --from-beginning
```
执行完上诉命令后，在生产者窗口中 输入任意内容回车，即可在消费者的窗口查看到消息

## 第四步 搭建kafka管理平台
```
docker pull sheepkiller/kafka-manager
docker run -it -d --rm  -p 9000:9000 -e ZK_HOSTS="192.168.9.219:2181" --net=host sheepkiller/kafka-manager
firewall-cmd --add-port=9000/tcp
```
创建成后，在浏览器中访问 http://192.168.9.219:9000

## 附录：
查看消息主题列表
```
./kafka-topics.sh --list --zookeeper zookeeper:2181
test
```
查看指定topic信息：
```
./kafka-topics.sh --describe --zookeeper zookeeper:2181 --topic test
```
# 集群搭建
使用docker命令可快速在同一台机器搭建多个kafka，只需要改变brokerId和端口
```
docker run -d --name kafka1 \
-p 9093:9093 \
-e KAFKA_BROKER_ID=1 \
-e KAFKA_ZOOKEEPER_CONNECT=192.168.204.128:2181 \
-e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://192.168.204.128:9093 \
-e KAFKA_LISTENERS=PLAINTEXT://0.0.0.0:9093 -t wurstmeister/kafka
```
## 创建Replication为2，Partition为2的topic
在kafka容器中的opt/kafka_2.12-1.1.0/目录下输入
```
bin/kafka-topics.sh --create --zookeeper 192.168.204.128:2181 --replication-factor 2 --partitions 2 --topic partopic
```
## 查看topic的状态
在kafka容器中的opt/kafka_2.12-1.1.0/目录下输入
```
bin/kafka-topics.sh --describe --zookeeper 192.168.204.128:2181 --topic partopic
```
输出结果：
```
Topic:partopic  PartitionCount:2    ReplicationFactor:2 Configs:
    Topic: partopic Partition: 0    Leader: 0   Replicas: 0,1   Isr: 0,1
    Topic: partopic Partition: 1    Leader: 0   Replicas: 1,0   Isr: 0,1
```
显示每个分区的Leader机器为broker0，在broker0和1上具有备份，Isr代表存活的备份机器中存活的。 
当停掉kafka1后，
```
docker stop kafka1
```
再查看topic状态，输出结果：
```
Topic:partopic  PartitionCount:2    ReplicationFactor:2 Configs:
    Topic: partopic Partition: 0    Leader: 0   Replicas: 0,1   Isr: 0
    Topic: partopic Partition: 1    Leader: 0   Replicas: 1,0   Isr: 0
```
