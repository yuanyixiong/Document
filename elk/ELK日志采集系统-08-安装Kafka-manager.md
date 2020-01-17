# 安装Kafka-manager
## 初始化安装目录
```
echo "=================初始化安装目录================="
app=/usr/local/src/app
config=/usr/local/src/config
files=/usr/local/src/files
```

## 导入安装Kafka-manager
```
echo "=================安装kafka-manager================="
cp /mnt/hgfs/服务器现有资料/kafka-manager-1.3.3.7.zip $files
unzip $files/kafka-manager-1.3.3.7.zip -d $app
```

## 配置Kafka-manager
```
vim /usr/local/src/app/kafka-manager-1.3.3.7/conf/application.conf

kafka-manager.zkhosts="172.16.89.150:2181,172.16.89.151:2181,172.16.89.152:2181,172.16.89.153:2181"
```

## 启动Kafka-manager
```
echo "=================安装kafka-manager================="
nohup bin/kafka-manager -Dconfig.file=conf/application.conf -Dhttp.port=9000 &
```

## 访问Kafka-manager
```
http://172.16.89.150:9000
```