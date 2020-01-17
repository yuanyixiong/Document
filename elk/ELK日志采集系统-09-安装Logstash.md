# 安装Logstash
## 初始化安装目录
```
echo "=================初始化安装目录================="
app=/usr/local/src/app
config=/usr/local/src/config
files=/usr/local/src/files
```

## 导入安装Logstash
```
echo "=================安装logstash-6.6.1================="
cp /mnt/hgfs/服务器现有资料/logstash-6.6.1.tar.gz $files
tar -zxvf $files/logstash-6.6.1.tar.gz -C $app
```

## 验证是否可用
```
[root@master conf]# echo "=================验证是否可用================="
[root@master conf]# logstash_root=$app/logstash-6.6.1
[root@master conf]# sh /usr/local/src/app/logstash-6.6.1/bin/logstash -e 'input { stdin{} } output { stdout{} }'

Sending Logstash logs to /usr/local/src/app/logstash-6.6.1/logs which is now configured via log4j2.properties
[2019-07-30T00:49:03,593][WARN ][logstash.config.source.multilocal] Ignoring the 'pipelines.yml' file because modules or command line options are specified
[2019-07-30T00:49:03,658][INFO ][logstash.runner          ] Starting Logstash {"logstash.version"=>"6.6.1"}
[2019-07-30T00:49:15,487][INFO ][logstash.pipeline        ] Starting pipeline {:pipeline_id=>"main", "pipeline.workers"=>1, "pipeline.batch.size"=>125, "pipeline.batch.delay"=>50}
[2019-07-30T00:49:15,686][INFO ][logstash.pipeline        ] Pipeline started successfully {:pipeline_id=>"main", :thread=>"#<Thread:0x3f23fe91 run>"}
The stdin plugin is now waiting for input:
[2019-07-30T00:49:15,827][INFO ][logstash.agent           ] Pipelines running {:count=>1, :running_pipelines=>[:main], :non_running_pipelines=>[]}
[2019-07-30T00:49:16,296][INFO ][logstash.agent           ] Successfully started Logstash API endpoint {:port=>9600}

HelloWord
{
       "message" => "HelloWord",
      "@version" => "1",
    "@timestamp" => 2019-07-30T07:49:35.587Z,
          "host" => "master"
}
```

## 验证Logstash和ElasticSearch通信
```
logstash_root=$app/logstash-6.6.1
echo "

input {
        stdin{}
}

output {
   elasticsearch {
     hosts => ["172.16.89.150:9200","172.16.89.151:9200", "172.16.89.152:9200", "172.16.89.153:9200"]
     index => "logstash-%{+YYYY.MM.dd}"
   }
}

" > $logstash_roo/conf/logstash_for_stdin_to_es.conf
```

## 验证Logstash和Kafka通信
```
logstash_root=$app/logstash-6.6.1
echo "

input {
        stdin{}
}
output {
   stdout { 
       codec => rubydebug 
   }
   kafka {
       bootstrap_servers => "172.16.89.150:9092,72.16.89.151:9092,72.16.89.152:9092,72.16.89.153:9092"
       topic_id => "test1" 
       compression_type => "snappy"
    }
}

" > $logstash_roo/conf/logstash_for_stdin_to_kafka.conf
```

## 验证Logstash读取Log和ElasticSearch通信
```
logstash_root=$app/logstash-6.6.1
echo "

input {
   file{
       path => "/var/log/messages"
       start_position => "beginning"
   }
}

output {
   elasticsearch {
     hosts => ["172.16.89.150:9200","172.16.89.151:9200", "172.16.89.152:9200", "172.16.89.153:9200"]
     index => "logstash-%{+YYYY.MM.dd}"
   }
}

" > $logstash_roo/conf/logstash_for_system_messages_to_es.conf
```

## 验证Logstash读取Log和Kafka通信
```
logstash_root=$app/logstash-6.6.1
echo "

input {
   file{
       path => "/var/log/messages"
       start_position => "beginning"
   }
}

output {
   stdout { 
       codec => rubydebug 
   }
   kafka {
       bootstrap_servers => "172.16.89.150:9092,72.16.89.151:9092,72.16.89.152:9092,72.16.89.153:9092"
       topic_id => "test1" 
       compression_type => "snappy"
    }
}

" > $logstash_roo/conf/logstash_for_system_messages_to_kafka.conf
```

## 验证Logstash读取Kafka和ElasticSearch通信
```
logstash_root=$app/logstash-6.6.1
echo "

input {
   kafka {
       codec => "json"
       topics => "test1"
       bootstrap_servers => "172.16.89.150:9092,72.16.89.151:9092,72.16.89.152:9092,72.16.89.153:9092"
       auto_offset_reset => "latest"
       group_id => "test-consumer-group"
    }
}

output {
   elasticsearch {
     hosts => ["172.16.89.150:9200","172.16.89.151:9200", "172.16.89.152:9200", "172.16.89.153:9200"]
     index => "log-%{+YYYY.MM.dd}"
   }
}

" > $logstash_roo/conf/logstash_for_kafka_to_es.conf
```

## 启动Logstash
1. 检查配置文件
```
logstash_root=$app/logstash-6.6.1
sh $logstash_root/bin/logstash -f $logstash_root/config/logstash_for_kafka_to_es.conf -t
```
2. 控制台启动
```
logstash_root=$app/logstash-6.6.1
sh $logstash_root/bin/logstash -f $logstash_root/config/logstash_for_kafka_to_es.conf
```
3. 后台启动
```
logstash_root=$app/logstash-6.6.1
nohup $logstash_root/bin/logstash -f $logstash_root/config/logstash_for_kafka_to_es.conf &
```