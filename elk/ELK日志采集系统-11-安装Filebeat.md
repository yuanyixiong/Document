# 安装Filebeat
## 初始化安装目录
```
echo "=================初始化安装目录================="
app=/usr/local/src/app
config=/usr/local/src/config
files=/usr/local/src/files
```

## 导入安装Filebeat
```
echo "=================安装filebeat-6.6.1================="
cp /mnt/hgfs/服务器现有资料/filebeat-6.6.1-linux-x86_64.tar.gz $files
tar -zxvf $files/filebeat-6.6.1-linux-x86_64.tar.gz -C $app
```
## 配置Filebeat
```
filebeat.inputs:
- type: log
  enabled: true
  paths:
    - /usr/local/src/app/my.log
output.kafka:
  enabled: true
  hosts: ["172.16.89.150:9092","172.16.89.151:9092","172.16.89.152:9092","172.16.89.153:9092"]
  topic: test1
```
```
filebeat.prospectors:
- type: log
  enabled: true
  paths:
    - /var/log/messages
  fields:
    log_topics: test1
- type: log
  enabled: true
  paths:
    - /var/log/messages-20190630
  fields:
    log_topics: test1
- type: log
  enabled: true
  paths:
    - /var/log/messages-20190728
  fields:
    log_topics: test1
- type: log
  enabled: true
  paths:
    - /var/log/messages-20190726
  fields:
    log_topics: test1
output.kafka:
  enabled: true
  hosts: ["172.16.89.150:9092","172.16.89.151:9092","172.16.89.152:9092","172.16.89.153:9092"]
  topic: '%{[fields][log_topics]}'
```
## 启动Filebeat
1. 控制台启动
```
filebeat_root=$app/filebeat-6.6.1-linux-x86_64
$filebeat_root/filebeat -e -c filebeat.yml
```
2. 后台启动
```
filebeat_root=$app/filebeat-6.6.1-linux-x86_64
nohup $filebeat_root/filebeat -e -c filebeat.yml &
```