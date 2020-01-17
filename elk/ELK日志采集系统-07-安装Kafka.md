# 安装Kafka
## 初始化安装目录
```
echo "=================初始化安装目录================="
app=/usr/local/src/app
config=/usr/local/src/config
files=/usr/local/src/files
```

## 导入安装Kafka
```
echo "=================安装kafka================="
cp /mnt/hgfs/服务器现有资料/kafka_2.11-0.10.2.1.tgz $files
tar -zxvf $files/kafka_2.11-0.10.2.1.tgz -C $app
```

## 配置Kafka
```
vim $app/kafka_2.11-0.10.2.1/config/server.properties 
broker.id=0   #节点编号,kafka集群下唯一
delete.topic.enable=true #删除队列功能开启
listeners = PLAINTEXT://172.16.89.150:9092  #kafka集群监听的ip:port  端口默认9092
zookeeper.connect=172.16.89.150:2181,172.16.89.151:2181,172.16.89.152:2181,172.16.89.153:2181 #zookeeper集群节点
```

## 启动Kafka
```
sh  $app/kafka_2.11-0.10.2.1/bin/kafka-server-start.sh  $app/kafka_2.11-0.10.2.1/config/server.properties &
```


## 常用Kafka操作
1.开启zookeeper集群 startzk.sh
2.开启kafka集群  start-kafka.sh
3.开启kafka可视化界面 kafka-manager ： start-kafka-manager.sh
 
4.生产者操作：
```
# my-kafka-topic时topic的名字
kafka-console-producer.sh --broker-list node1:9092 --topic my-kafka-topic    
```
 
5.消费者操作：
```
# 查看消费者接收生产者发送的消息
kafka-console-consumer.sh --bootstrap-server node1:9092 --topic my-kafka-topic

# 如果需要从头开始接收数据，需要添加--from-beginning参数
kafka-console-consumer.sh --bootstrap-server node01:9092 --from-beginning --topic my-kafka-topic
```

6. topic 操作
```
# 创建topic
kafka-topics.sh --create --zookeeper node1:2181 --replication-factor 1 --partitions 1 --topic my-kafka-topic
```
 
```
# 查看topic列表
kafka-topics.sh --list --zookeeper node01:2181
```
 
```
# 如果需要查看topic的详细信息，需要使用describe命令
kafka-topics.sh --describe --zookeeper node1:2181 --topic test-topic
```
 
```
# 若不指定topic，则查看所有topic的信息
kafka-topics.sh --describe --zookeeper node1:2181
```
 
```
# 删除topic
kafka-topics.sh --delete --zookeeper node1:2181 --topic my-kafka-topic
```

7. 测试命令
```
sh $app/kafka_2.11-0.10.2.1/bin/kafka-topics.sh --zookeeper 172.16.89.150:2181,172.16.89.151:2181,172.16.89.152:2181,172.16.89.153:2181 --list osmessages
```