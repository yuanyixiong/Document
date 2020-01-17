# 安装Flume

## 初始化安装目录
```
echo "=================初始化安装目录================="
app=/usr/local/src/app
config=/usr/local/src/config
files=/usr/local/src/files
```

## 导入安装Flume
```
echo "=================安装logstash-6.6.1================="
cp /mnt/hgfs/服务器现有资料/apache-flume-1.6.0-bin.tar.gz $files
tar -xvf $files/apache-flume-1.6.0-bin.tar.gz -C $app
```

## 配置Flume
```
flume_root=$app/apache-flume-1.6.0-bin

echo "

a1.sources = r1
a1.channels = c1
a1.sinks = k1
 
a1.sources.r1.type = exec
a1.sources.r1.command = tail -F /usr/local/src/app/my.log 
 
a1.channels.c1.type = memory
a1.channels.c1.capacity = 10000
 
a1.sinks.k1.type = org.apache.flume.sink.kafka.KafkaSink

#1.7版本：
#a1.sinks.k1.kafka.topic = test1
#a1.sinks.k1.kafka.bootstrap.servers = 172.16.89.150:9092,172.16.89.151:9092,172.16.89.152:9092,172.16.89.153:9092

# 而1.6版本：
a1.sinks.k1.topic = test1
a1.sinks.k1.brokerList = 172.16.89.150:9092,172.16.89.151:9092,172.16.89.152:9092,172.16.89.153:9092

a1.sinks.k1.kafka.flumeBatchSize = 20
#acks 0:不需要回执 1:leader写入磁盘后回执 -1:所有节点写入磁盘后回执
a1.sinks.k1.kafka.producer.acks = 1
a1.sinks.k1.kafka.producer.linger.ms = 0
 
a1.sources.r1.channels=c1
a1.sinks.k1.channel=c1

" > $flume_root/conf/flume_for_log_to_kafka.conf
```

## 启动Flume
1. 控制台启动Flume
```
flume_root=$app/apache-flume-1.6.0-bin
$flume_root/bin/flume-ng agent -n a1 -c conf -f $flume_root/conf/flume_for_log_to_kafka.conf -Dflume.root.logger=INFO,console
```
2. 后台启动Flume
```
flume_root=$app/apache-flume-1.6.0-bin
nohup $flume_root/bin/flume-ng agent -n a1 -c conf -f $flume_root/conf/flume_for_log_to_kafka.conf -Dflume.root.logger=INFO,console &
```